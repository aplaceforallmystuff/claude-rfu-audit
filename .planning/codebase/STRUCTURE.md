# Codebase Structure

**Analysis Date:** 2026-01-22

## Directory Layout

```
claude-rfu-audit/
├── skill/                      # Skill definition and templates
│   ├── SKILL.md               # Skill metadata and gate documentation
│   └── templates/             # Output templates
│       └── audit-report.md    # Markdown template for audit report
├── README.md                  # Project overview and framework explanation
├── install.sh                 # Installation script for Claude Code
└── LICENSE                    # MIT license
```

## Directory Purposes

**`skill/`:**
- Purpose: Contains the Claude Code skill definition and all supporting materials
- Contains: Skill manifest, gate documentation, output templates
- Key files: `SKILL.md` (140+ lines of gate logic and process), `templates/` directory

**`skill/templates/`:**
- Purpose: Store output templates used by Claude when generating reports
- Contains: Markdown template with variable placeholders
- Key files: `audit-report.md` (structured report template with 30+ placeholder variables)

## Key File Locations

**Entry Points:**
- `skill/SKILL.md`: Skill definition loaded by Claude Code when `/rfu-audit` is invoked (lines 1-282)
- `install.sh`: Installation script executed by end users to set up the skill (lines 1-51)
- `README.md`: Project documentation and framework reference (lines 1-232)

**Configuration:**
- `skill/SKILL.md`: Lines 1-5 contain skill metadata (name, description, user-invocable flag)

**Core Logic:**
- `skill/SKILL.md`: Lines 26-241 define the 11 gates with pass/fail criteria and examples
- `skill/SKILL.md`: Lines 253-262 describe the execution process workflow

**Output Templates:**
- `skill/templates/audit-report.md`: Complete report structure with variable placeholders for:
  - Executive summary (lines 9-13)
  - Gate results (lines 17-182, one section per gate)
  - Scorecard (lines 185-201)
  - Priority fixes (lines 205-216)
  - Next steps (lines 220-222)

## Naming Conventions

**Files:**
- Skill definition: `SKILL.md` (uppercase, markdown format, uppercase extension)
- Project docs: `README.md`, `LICENSE` (uppercase, standard convention)
- Templates: Descriptive lowercase with hyphens: `audit-report.md`
- Scripts: Lowercase with `.sh` extension: `install.sh`

**Directories:**
- Functional grouping: `skill/` (lowercase, singular) for skill-specific content
- Feature grouping: `templates/` (lowercase, plural) for related output templates

## Where to Add New Code

This is a skill distribution package with no executable code. Additions should follow this pattern:

**New Gate Logic:**
- Add gate definition to `skill/SKILL.md` under Gates section (maintain lines 26-241 structure)
- Include: core question, pass criteria, fail indicators, example evaluation
- Add corresponding template variables to `skill/templates/audit-report.md`
- Update skill manifest metadata if adding gates

**New Output Format:**
- Create new template in `skill/templates/` with descriptive filename (e.g., `audit-report-json.md` for JSON variant)
- Use consistent variable placeholder naming: `{{variable_name}}`
- Document template variables at top of file for clarity

**Documentation Updates:**
- Framework explanation: Update `README.md` (keep in sync with `skill/SKILL.md`)
- Gate details: Update `skill/SKILL.md` lines 26-241 (source of truth)
- Examples: Add to gate-specific sections with good/bad examples

**Installation Enhancements:**
- Modify `install.sh` to support new behaviors
- Follow existing pattern: check for existing installation, create directories, copy files, verify

## Special Directories

**`.planning/`:**
- Purpose: GSD (Getting Shit Done) planning directory for codebase analysis
- Generated: Yes (created by GSD orchestrator)
- Committed: Yes (part of repository)
- Contains: Codebase analysis documents written by GSD mapping tools

**`.git/`:**
- Purpose: Git version control metadata
- Generated: Yes (created by git init)
- Committed: No (Git manages this automatically)

## File Dependencies

**Skill Invocation Chain:**
```
User runs: /rfu-audit [path]
    ↓
Claude Code loads: skill/SKILL.md
    ↓
Skill reads project files from [path] (README.md, package.json, etc.)
    ↓
Claude executes gates (logic defined in skill/SKILL.md)
    ↓
Claude fills: skill/templates/audit-report.md with results
    ↓
User receives: Markdown report
```

**Installation Chain:**
```
User runs: bash install.sh
    ↓
Script copies: skill/ → ~/.claude/skills/rfu-audit/
    ↓
Verifies: SKILL.md exists at destination
    ↓
User can now invoke: /rfu-audit [path]
```

## How Skills Work (Claude Code Context)

When a user invokes `/rfu-audit`:

1. Claude Code looks in `~/.claude/skills/` for a directory named `rfu-audit`
2. It reads `SKILL.md` from that directory
3. The SKILL.md metadata (lines 1-5) activates the skill:
   - `name: rfu-audit` — matches the command name
   - `user-invocable: true` — allows user to run it
4. The skill description and documentation (lines 7-282) guide Claude's execution
5. Claude applies the 11-gate framework logic to the target project
6. Claude generates output using the template at `skill/templates/audit-report.md`

**Key point:** There is no code execution. SKILL.md is declarative; it describes what Claude should do via natural language instructions and framework definitions.

---

*Structure analysis: 2026-01-22*
