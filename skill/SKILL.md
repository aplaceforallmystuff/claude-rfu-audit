---
name: rfu-audit
description: Run the 11-gate Really Fucking Useful gauntlet to validate project utility before investing significant effort
user-invocable: true
---

# RFU Audit: Really Fucking Useful

Systematic framework to validate project utility BEFORE investing significant effort. Most open source projects fail not because of bad code, but because they solve problems nobody has.

## Philosophy

Developers build what's interesting to *them*, not what's valuable to *users*. The RFU gauntlet forces you to prove utility through 11 rigorous gates before shipping.

**Pass all 11 gates = RFU Certified**

## Usage

```
/rfu-audit [project-path]
/rfu-audit ~/Dev/my-project
```

If no path provided, audits the current working directory.

## Input Validation

Before starting the audit, run a 6-stage validation flow to catch bad input early and provide actionable error messages.

### Validation Flow

Execute in order. Stop at first failure.

1. **Normalize path**: Handle `~/`, relative paths, trailing slashes
2. **Check path exists**: Directory or file must exist at the given path
3. **Check path is a directory**: Not a single file
4. **Check README.md exists**: Must be present and readable in the directory
5. **Check for project indicators**: At least one of:
   - Configuration: `package.json`, `Cargo.toml`, `pyproject.toml`, `go.mod`
   - Source directories: `src/`, `lib/`
   - Entry files: `index.js`, `main.py`
6. **Proceed to audit**

### Error Message Format

All validation errors follow a three-part structure:

```
X Cannot audit: {Problem}

  Why this matters:
    {Link to specific gate requirement}

  Fix this by:
    {Actionable steps}
```

### Error Types

**Path does not exist:**
```
X Cannot audit: Path does not exist
  Path: /bad/path

  Why this matters:
    Cannot read project files without a valid path.

  Fix this by:
    Check the path and try again.
```

**Not a directory:**
```
X Cannot audit: Path is a file, not a directory
  Path: /path/to/README.md

  Why this matters:
    Audit requires a project directory, not a single file.

  Fix this by:
    Provide the directory containing your project.
    Try: /rfu-audit /path/to
```

**Permission denied:**
```
X Cannot audit: Permission denied
  Path: /restricted/path

  Why this matters:
    Cannot read project files without read access.

  Fix this by:
    Grant read access: chmod +r /restricted/path
    Or run from a directory you have access to.
```

**Missing README:**
```
X Cannot audit: No README.md found
  Path: /path/to/project

  Why this matters:
    Gate 4 (Bartender Test) requires a clear value statement,
    which should be in README.md.

  Fix this by:
    Create README.md with:
    1. What this project does (one sentence)
    2. Installation instructions
    3. Basic usage example
```

**No project files:**
```
X Cannot audit: No project files detected
  Path: /path/to/empty

  Why this matters:
    Gate 3 (10-Minute Test) requires working code to evaluate.

  Fix this by:
    Ensure directory contains source code and configuration.
    Expected indicators: package.json, Cargo.toml, pyproject.toml,
    go.mod, src/, lib/, or entry files (index.js, main.py)
```

For complete error handling patterns, see `guides/INPUT-VALIDATION.md`.

## The 11 Gates

Complete gate definitions are maintained in `config/gates-full.md`. Each gate includes:
- Core question and methodology
- Pass criteria (what constitutes success)
- Fail indicators (red flags)
- Template variables for report generation

### Gate Overview

| Gate | Name | Core Question |
|------|------|---------------|
| 1 | 5 Whys of Utility | What bedrock human need does this serve? |
| 2 | Inversion Test | What if this didn't exist? |
| 3 | 10-Minute Test | Can users get value in 10 minutes? |
| 4 | Bartender Test | Can you explain it in one sentence? |
| 5 | Wallet Test | Would you pay? Name 3 who would. |
| 6 | Job-to-be-Done | What job is the user hiring this for? |
| 7 | Friction Audit | What friction exists discovery to mastery? |
| 8 | Second-Order Consequences | What happens after success? |
| 9 | Occam's Razor | Is this the simplest solution? |
| 10 | Pareto Gate | Does 20% deliver 80% of value? |
| 11 | Regret Minimization | Will you regret NOT building this? |

Refer to `config/gates-full.md` for complete specifications when evaluating each gate.

## Scoring

| Score | Rating | Action |
|-------|--------|--------|
| 11/11 | RFU Certified | Ship it |
| 9-10 | Almost There | Fix specific gaps, then ship |
| 6-8 | Promising | Needs significant work |
| 0-5 | Reconsider | Build something else |

## Process

When auditing a project:

1. **Read project files**: README, package.json, main source files
2. **Walk through each gate sequentially** (detailed in `config/gates-full.md`)
3. **Score each gate**: Pass (1) or Fail (0)
4. **Provide evidence** for each score
5. **Generate prioritized fix recommendations**
6. **Output structured report** using `templates/audit-report.md`

For borderline cases, consult `guides/EDGE-CASES.md` (when available).

## Integration with Wisdom Skills

This skill integrates concepts from:
- `/wisdom-clarify` - 5 Whys questioning
- `/wisdom-stoic-premeditation` - Via negativa/inversion
- `/taches-cc-resources:consider:second-order` - Second-order consequences
- `/taches-cc-resources:consider:pareto` - 80/20 analysis
- `/taches-cc-resources:consider:occams-razor` - Simplicity check

## Output

Generate a structured audit report using the template in `templates/audit-report.md`.

The template expects variables documented in each gate's "Template Variables" section in `config/gates-full.md`.

## Related Skills

- `/think-first` - Decision framework
- `/wisdom-ground` - Ground in philosophical frameworks
- `/prep-repo` - Prepare repo for publishing (after RFU passes)
