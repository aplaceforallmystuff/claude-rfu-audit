# Phase 3: Input Validation - Research

**Researched:** 2026-01-22
**Domain:** CLI input validation, error handling, and user-friendly error messaging
**Confidence:** HIGH

## Summary

Input validation is a critical first-pass filter for CLI tools. The RFU Audit skill currently lacks pre-flight checks for path existence, file accessibility, and minimum project requirements. When invoked with invalid input (missing README, bad path, non-project directory), it fails with cryptic errors or produces nonsense reports.

Best practices from the CLI ecosystem emphasize three principles:
1. **Validate early** - Check inputs before any processing begins
2. **Fail gracefully** - Provide actionable error messages, not stack traces
3. **Guide recovery** - Tell users exactly what to fix and how

Phase 3 implements pre-audit validation that checks path validity, file existence, and project structure, then provides specific, helpful error messages when validation fails. The pattern: "Problem detected → Specific diagnosis → Suggested fix."

**Primary recommendation:** Implement validation as the first step in the audit process, using Node.js fs.access() for path checks and fs.stat() for file type detection. Create a dedicated INPUT-VALIDATION.md guide documenting all error scenarios and their handling patterns.

## Standard Stack

The established libraries and patterns for CLI input validation in Node.js:

### Core

| Library | Version | Purpose | Why Standard |
|---------|---------|---------|--------------|
| Node.js fs/promises | Built-in | Async file system operations | Native, zero dependencies, promise-based |
| Node.js process.exit() | Built-in | Exit with status codes | POSIX-compliant exit codes (0=success, 1=error) |
| chalk | 5.x | Terminal color formatting | 137,680+ projects use it, clear error/warning/success colors |

### Supporting

| Library | Version | Purpose | When to Use |
|---------|---------|---------|-------------|
| fs.access() | Built-in | Check file/directory existence and permissions | Pre-flight validation before reads |
| fs.stat() | Built-in | Get file type and metadata | Distinguish files vs directories, detect symlinks |
| path.resolve() | Built-in | Normalize paths | Convert relative paths to absolute, handle ~/ expansion |

### Alternatives Considered

| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| fs.access() + fs.stat() | fs.existsSync() | existsSync blocks event loop, introduces race conditions if used before operations |
| chalk | ANSI escape codes | Chalk handles terminal compatibility, ANSI codes require manual detection |
| Built-in fs | fs-extra | fs-extra adds bloat for features we don't need (copy, move, ensureDir) |

**Installation:**
```bash
npm install chalk
# fs, path, process are built-in Node.js modules
```

## Architecture Patterns

### Recommended Validation Flow

```
1. Normalize input path (handle relative, ~/, missing trailing slash)
2. Check path exists and is accessible
3. Check path is a directory (not a file)
4. Check directory contains project indicators (README, package.json, source files)
5. Check required files are readable (README.md specifically)
6. Proceed to audit OR exit with specific error + guidance
```

### Pattern 1: Progressive Validation (Fail Fast)

**What:** Validate in stages, stopping at the first failure with specific error message.

**When to use:** When each validation depends on the previous (can't check README exists if path doesn't exist).

**Example:**
```javascript
// Source: CLI best practices pattern
async function validateProject(projectPath) {
  // Stage 1: Path validation
  const normalizedPath = path.resolve(projectPath);

  try {
    await fs.access(normalizedPath, fs.constants.F_OK);
  } catch (error) {
    exitWithError(
      `Path does not exist: ${normalizedPath}`,
      `Check the path and try again.`,
      1
    );
  }

  // Stage 2: Directory check
  const stats = await fs.stat(normalizedPath);
  if (!stats.isDirectory()) {
    exitWithError(
      `Not a directory: ${normalizedPath}`,
      `Provide a directory path, not a file path.`,
      1
    );
  }

  // Stage 3: Project structure check
  const readmePath = path.join(normalizedPath, 'README.md');
  try {
    await fs.access(readmePath, fs.constants.R_OK);
  } catch (error) {
    exitWithGuidance(
      `No README found: ${readmePath}`,
      `The Bartender Test (Gate 4) requires a clear value statement.`,
      `Create README.md with:\n  1. What this project does (one sentence)\n  2. Installation instructions\n  3. Basic usage example`
    );
  }

  return normalizedPath;
}
```

### Pattern 2: Human-Readable Error Messages

**What:** Transform technical errors into actionable guidance.

**When to use:** All error outputs to users (never show raw Error objects).

**Example:**
```javascript
// Source: clig.dev guidelines
function exitWithError(problem, context, exitCode = 1) {
  console.error(chalk.red('✗ Error:'), problem);
  if (context) {
    console.error(chalk.yellow('  →'), context);
  }
  process.exit(exitCode);
}

function exitWithGuidance(problem, why, solution) {
  console.error(chalk.red('✗ Cannot audit:'), problem);
  console.error(chalk.yellow('\n  Why this matters:'));
  console.error(`    ${why}`);
  console.error(chalk.green('\n  Fix this by:'));
  solution.split('\n').forEach(line => {
    console.error(`    ${line}`);
  });
  process.exit(1);
}

// Usage
exitWithError(
  'Path does not exist: /bad/path',
  'Check the path and try again.'
);

exitWithGuidance(
  'No README found',
  'The Bartender Test (Gate 4) requires a clear value statement.',
  'Create README.md describing what this project does.'
);
```

### Pattern 3: Permission Error Handling

**What:** Detect permission-denied scenarios and suggest resolution.

**When to use:** When fs.access() or fs.stat() throws EACCES error.

**Example:**
```javascript
// Source: Node.js CLI best practices
async function checkReadable(filePath) {
  try {
    await fs.access(filePath, fs.constants.R_OK);
    return true;
  } catch (error) {
    if (error.code === 'EACCES') {
      exitWithError(
        `Permission denied: ${filePath}`,
        `You don't have read access. Try:\n    chmod +r ${filePath}`
      );
    }
    throw error; // Other errors propagate
  }
}
```

### Pattern 4: Project Type Detection

**What:** Detect project type by configuration files to provide context-specific guidance.

**When to use:** When validation fails and you want to suggest relevant fixes.

**Example:**
```javascript
// Source: Multi-language project detection pattern
async function detectProjectType(dirPath) {
  const indicators = {
    'package.json': 'Node.js',
    'Cargo.toml': 'Rust',
    'pyproject.toml': 'Python',
    'go.mod': 'Go',
    'Gemfile': 'Ruby'
  };

  for (const [file, type] of Object.entries(indicators)) {
    const filePath = path.join(dirPath, file);
    try {
      await fs.access(filePath);
      return type;
    } catch {
      continue;
    }
  }

  return 'Unknown';
}

// Use in error message:
const projectType = await detectProjectType(normalizedPath);
console.error(chalk.yellow(`  Detected ${projectType} project without README`));
```

### Anti-Patterns to Avoid

- **Check-then-use race condition:** Don't use fs.access() to check if file exists, then immediately fs.readFile(). Just try reading and handle the error. (Note: For validation-only scenarios like pre-flight checks, fs.access() is appropriate.)
- **Synchronous blocking:** Never use fs.existsSync() or fs.statSync() in production — blocks event loop
- **Generic errors:** "Error: ENOENT" is useless; "No README found at /path/to/README.md" is helpful
- **Stack traces by default:** Only show stack traces in debug mode; default to human messages
- **Missing guidance:** "Invalid input" without explanation is frustrating; always suggest fix

## Don't Hand-Roll

Problems that look simple but have existing solutions:

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Path normalization | Manual string parsing for ~, .., ./ | `path.resolve()` | Handles OS differences, symlinks, edge cases (Windows C:\ vs Linux /) |
| File existence check | Try reading file to see if exists | `fs.access(path, fs.constants.F_OK)` | Purpose-built, correct error codes, no side effects |
| Terminal colors | ANSI escape code strings | `chalk` library | Handles terminal compatibility, color support detection |
| Argument parsing | Manual process.argv slicing | `commander` or `yargs` | Built-in validation, help generation, subcommands |
| Exit code management | Random numbers for errors | POSIX conventions (0=success, 1=error) | Integration with shell scripts, standard expectations |

**Key insight:** File system operations have subtle edge cases (symlinks, permissions, race conditions). Use Node.js built-in APIs that handle these correctly rather than implementing validation logic from scratch.

## Common Pitfalls

### Pitfall 1: Invalid Path Produces Cryptic Error

**What goes wrong:** User provides `/bad/path`, skill tries to read files, Node.js throws `ENOENT: no such file or directory`, stack trace appears, user confused.

**Why it happens:**
- No pre-flight path validation
- Skill assumes inputs are valid
- Error handling added reactively after failures occur

**How to avoid:**
1. Validate path exists before any file operations
2. Use fs.access() with F_OK constant
3. Catch error and translate to user-friendly message
4. Exit immediately with status code 1

**Warning signs:**
- Raw Node.js error codes (ENOENT, EACCES) appear in output
- Stack traces visible to end users
- Error messages contain file paths from skill internals

**Implementation:**
```javascript
// Before any audit logic
try {
  await fs.access(projectPath, fs.constants.F_OK);
} catch {
  console.error(chalk.red('✗ Path does not exist:'), projectPath);
  console.error(chalk.yellow('  Check the path and try again.'));
  process.exit(1);
}
```

### Pitfall 2: Missing README Produces Nonsense Report

**What goes wrong:** User audits directory without README, skill can't extract value statement, continues anyway, generates report with empty fields or hallucinates answers.

**Why it happens:**
- No requirement enforcement for essential files
- Graceful degradation taken too far (tries to continue despite missing critical input)
- Template variables get filled with defaults/assumptions

**How to avoid:**
1. Define minimum viable project: must have README.md
2. Check README exists and is readable before starting
3. Fail audit with helpful message if missing
4. Explain WHY README is required (Gate 4: Bartender Test needs value statement)

**Warning signs:**
- Audit reports with "{{value}}" literal template variables
- Generic/vague gate answers despite specific project
- Users ask "why did it say X when my project does Y?"

**Implementation:**
```javascript
const readmePath = path.join(projectPath, 'README.md');
try {
  await fs.access(readmePath, fs.constants.R_OK);
} catch {
  console.error(chalk.red('✗ Cannot audit: No README found'));
  console.error(chalk.yellow('\n  Why this matters:'));
  console.error('    The Bartender Test (Gate 4) requires a clear value statement,');
  console.error('    which should be in README.md');
  console.error(chalk.green('\n  Create README.md with:'));
  console.error('    1. What this project does (one sentence)');
  console.error('    2. Installation instructions');
  console.error('    3. Basic usage example');
  process.exit(1);
}
```

### Pitfall 3: Permission Denied with No Actionable Guidance

**What goes wrong:** User runs audit on directory they don't have read access to, gets "EACCES" error, doesn't know how to fix.

**Why it happens:**
- Permission errors not handled separately from other errors
- No translation from error codes to user actions
- Skill doesn't detect error type (permission vs existence)

**How to avoid:**
1. Catch EACCES errors specifically (use error.code === 'EACCES')
2. Suggest chmod command to fix permissions
3. Distinguish between "doesn't exist" vs "can't access"

**Warning signs:**
- Users report "access denied" without knowing why
- chmod issues come up in support discussions
- Different error messages for same underlying problem (inconsistent)

**Implementation:**
```javascript
try {
  await fs.access(projectPath, fs.constants.R_OK);
} catch (error) {
  if (error.code === 'EACCES') {
    console.error(chalk.red('✗ Permission denied:'), projectPath);
    console.error(chalk.yellow('  You don\'t have read access to this directory.'));
    console.error(chalk.green('  Fix this by running:'));
    console.error(`    chmod +r ${projectPath}`);
  } else if (error.code === 'ENOENT') {
    console.error(chalk.red('✗ Path does not exist:'), projectPath);
  } else {
    console.error(chalk.red('✗ Cannot access path:'), error.message);
  }
  process.exit(1);
}
```

### Pitfall 4: Non-Directory Path Not Detected

**What goes wrong:** User accidentally provides file path instead of directory (`/rfu-audit ~/README.md`), skill tries to read it as directory, confusing errors result.

**Why it happens:**
- No file type check after path validation
- Assumption that paths are always directories
- fs.readdir() fails but error doesn't clarify "this is a file, not a directory"

**How to avoid:**
1. After confirming path exists, check if it's a directory with fs.stat()
2. Use stats.isDirectory() to validate
3. Provide clear error: "This is a file; provide a directory containing your project"

**Warning signs:**
- Error messages reference files when user expected directory behavior
- Confusion about "where is my project?"

**Implementation:**
```javascript
const stats = await fs.stat(projectPath);
if (!stats.isDirectory()) {
  console.error(chalk.red('✗ Not a directory:'), projectPath);
  console.error(chalk.yellow('  Provide a directory path, not a file path.'));
  console.error(chalk.green('  Try:'));
  console.error(`    /rfu-audit ${path.dirname(projectPath)}`);
  process.exit(1);
}
```

### Pitfall 5: Empty Directory Detected as Valid Project

**What goes wrong:** User audits empty directory or directory with only config files (no source), audit continues but has nothing to evaluate.

**Why it happens:**
- Validation only checks for directory, not for content
- No definition of "minimum viable project" for auditing
- Skill attempts to work with whatever it finds

**How to avoid:**
1. Define minimum project requirements: README + (package.json OR source files)
2. Check for at least one indicator of actual project
3. Fail with message: "No project files detected. Need README + code."

**Warning signs:**
- Audit reports for empty directories
- Gates fail across the board with no useful feedback
- Users wonder "why did this not work?"

**Implementation:**
```javascript
const projectIndicators = [
  'package.json',
  'Cargo.toml',
  'pyproject.toml',
  'go.mod',
  'src/',
  'lib/',
  'index.js',
  'main.py'
];

const hasProjectFiles = await Promise.any(
  projectIndicators.map(async indicator => {
    try {
      await fs.access(path.join(projectPath, indicator));
      return true;
    } catch {
      return false;
    }
  })
);

if (!hasProjectFiles) {
  console.error(chalk.red('✗ No project files detected'));
  console.error(chalk.yellow('  This directory appears empty or has no source code.'));
  console.error(chalk.green('  Projects must have:'));
  console.error('    - README.md (required)');
  console.error('    - Source code files (required)');
  console.error('    - Configuration file like package.json (optional)');
  process.exit(1);
}
```

## Code Examples

Verified patterns from official sources and best practices:

### Complete Validation Function

```javascript
// Source: Synthesized from Node.js docs + CLI best practices
import fs from 'fs/promises';
import path from 'path';
import chalk from 'chalk';

async function validateProjectPath(inputPath) {
  // Normalize path (handles ~/, relative paths, etc.)
  const projectPath = path.resolve(inputPath);

  // Check 1: Path exists
  try {
    await fs.access(projectPath, fs.constants.F_OK);
  } catch (error) {
    console.error(chalk.red('✗ Path does not exist:'), projectPath);
    console.error(chalk.yellow('  Check the path and try again.'));
    process.exit(1);
  }

  // Check 2: Is directory
  const stats = await fs.stat(projectPath);
  if (!stats.isDirectory()) {
    console.error(chalk.red('✗ Not a directory:'), projectPath);
    console.error(chalk.yellow('  Provide a directory path, not a file path.'));
    process.exit(1);
  }

  // Check 3: Has README
  const readmePath = path.join(projectPath, 'README.md');
  try {
    await fs.access(readmePath, fs.constants.R_OK);
  } catch (error) {
    console.error(chalk.red('✗ Cannot audit: No README found'));
    console.error(chalk.yellow('\n  Why this matters:'));
    console.error('    The Bartender Test (Gate 4) requires a value statement');
    console.error(chalk.green('\n  Create README.md with:'));
    console.error('    1. What this project does (one sentence)');
    console.error('    2. Installation instructions');
    console.error('    3. Basic usage example');
    process.exit(1);
  }

  // Check 4: Has project files (at least one indicator)
  const projectIndicators = ['package.json', 'src/', 'lib/', 'index.js'];
  let hasProject = false;

  for (const indicator of projectIndicators) {
    try {
      await fs.access(path.join(projectPath, indicator));
      hasProject = true;
      break;
    } catch {
      continue;
    }
  }

  if (!hasProject) {
    console.error(chalk.red('✗ No project files detected'));
    console.error(chalk.yellow('  Need source code to audit implementation (Gate 3).'));
    process.exit(1);
  }

  // All checks passed
  console.log(chalk.green('✓ Project validated:'), projectPath);
  return projectPath;
}
```

### Error Message Format Template

```javascript
// Source: clig.dev + Node.js CLI best practices
const ErrorFormat = {
  // Simple error (problem + context)
  simple: (problem, context) => {
    console.error(chalk.red('✗ Error:'), problem);
    if (context) console.error(chalk.yellow('  →'), context);
    process.exit(1);
  },

  // Guided error (problem + why + how to fix)
  guided: (problem, why, solution) => {
    console.error(chalk.red('✗ Cannot audit:'), problem);
    console.error(chalk.yellow('\n  Why this matters:'));
    console.error(`    ${why}`);
    console.error(chalk.green('\n  Fix this by:'));
    solution.split('\n').forEach(line => {
      console.error(`    ${line}`);
    });
    process.exit(1);
  },

  // Permission error (special case with chmod suggestion)
  permission: (filePath) => {
    console.error(chalk.red('✗ Permission denied:'), filePath);
    console.error(chalk.yellow('  You don\'t have read access.'));
    console.error(chalk.green('  Try:'));
    console.error(`    chmod +r ${filePath}`);
    process.exit(1);
  }
};

// Usage examples:
ErrorFormat.simple('Path does not exist: /bad/path', 'Check the path and try again.');

ErrorFormat.guided(
  'No README found',
  'The Bartender Test (Gate 4) requires a clear value statement.',
  'Create README.md with project description and usage examples.'
);

ErrorFormat.permission('/restricted/directory');
```

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| fs.exists() | fs.access() | Node 4.0 (2015) | fs.exists deprecated due to race conditions; access is safer |
| Callback APIs | Promise-based fs/promises | Node 10.0 (2018) | Cleaner async code, better error handling with try/catch |
| Manual ANSI codes | chalk 5.x ESM-only | 2022 | Simpler API, automatic terminal detection, ESM modern standard |
| Synchronous validation | Async validation | Ongoing | Non-blocking, better for CLI responsiveness |
| Generic error messages | Actionable guidance | clig.dev (2020) | User empowerment vs frustration |

**Deprecated/outdated:**
- `fs.exists()`: Deprecated since Node 4.0, use fs.access() instead
- `fs.existsSync()`: Blocks event loop, introduces race conditions, avoid in production
- Callback-style fs methods: Use fs/promises for cleaner code
- Raw ANSI escape codes: Use chalk for terminal compatibility

## Open Questions

Things that couldn't be fully resolved:

1. **Should validation detect project language/type for customized error messages?**
   - What we know: Can detect via package.json, Cargo.toml, etc.
   - What's unclear: Is this extra complexity worth it, or keep generic?
   - Recommendation: Start generic, add language-specific guidance in Phase 3 iteration if users request it

2. **Should empty README (exists but 0 bytes) be treated as missing?**
   - What we know: fs.access will pass for empty file
   - What's unclear: Is checking file size worth the extra code?
   - Recommendation: Accept empty README initially, let gate evaluation handle quality

3. **What constitutes "project files" for validation?**
   - What we know: Need code to evaluate Gate 3 (10-Minute Test)
   - What's unclear: Exact list of valid indicators varies by language/project type
   - Recommendation: Start with common indicators (package.json, src/, lib/, common entry files), expand based on user feedback

4. **Should symlinks be followed or rejected?**
   - What we know: fs.stat() follows symlinks, fs.lstat() doesn't
   - What's unclear: User expectation when symlinking to project directory
   - Recommendation: Follow symlinks (use fs.stat), matches user expectation

## Sources

### Primary (HIGH confidence)

- [Command Line Interface Guidelines (clig.dev)](https://clig.dev/) - Comprehensive CLI UX best practices, error handling patterns
- [Node.js Official Documentation - Process API](https://nodejs.org/api/process.html) - POSIX exit codes, signal handling
- [Node.js Official Documentation - File System](https://nodejs.org/en/learn/manipulating-files/working-with-folders-in-nodejs) - fs.access, fs.stat patterns
- [Node.js CLI Apps Best Practices (Liran Tal)](https://github.com/lirantal/nodejs-cli-apps-best-practices) - Trackable errors, actionable messages, exit codes

### Secondary (MEDIUM confidence)

- [Error Handling in CLI Tools (Medium - Chloe Zhou)](https://medium.com/@czhoudev/error-handling-in-cli-tools-a-practical-pattern-thats-worked-for-me-6c658a9141a9) - Low-level throw, high-level catch pattern
- [chalk npm package](https://www.npmjs.com/package/chalk) - Terminal styling best practices, 137,680+ projects
- [Exit Status Codes (Wikipedia)](https://en.wikipedia.org/wiki/Exit_status) - POSIX standards, sysexits.h conventions
- [Mastering CLI Arguments in Node.js (Liran Tal)](https://lirantal.com/blog/mastering-cli-arguments-nodejs) - Validation at entry points
- `.planning/research/PITFALLS.md` (Pitfall 14) - Documented input validation gap

### Tertiary (LOW confidence - training data only)

- General patterns for file system validation from Node.js ecosystem
- Common CLI UX patterns across tools (git, npm, cargo)

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH - Node.js built-ins are stable, chalk is industry standard, patterns verified from official docs
- Architecture: HIGH - Validation flow patterns from clig.dev and Node.js CLI best practices repos
- Pitfalls: HIGH - Documented in existing PITFALLS.md + real-world CLI error handling patterns
- Error message formats: MEDIUM - Best practices established but no single authoritative spec

**Research date:** 2026-01-22
**Valid until:** 90 days (April 2026) - Node.js APIs stable, CLI UX patterns evolve slowly
