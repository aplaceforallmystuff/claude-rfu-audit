---
name: rfu-audit
description: Run the 11-gate Really Effing Useful gauntlet to validate project utility before investing significant effort
user-invocable: true
---

# RFU Audit: Really Effing Useful

Systematic framework to validate project utility BEFORE investing significant effort. Most open source projects fail not because of bad code, but because they solve problems nobody has.

## Philosophy

Developers build what's interesting to *them*, not what's valuable to *users*. The RFU gauntlet forces you to prove utility through 11 rigorous gates before shipping.

**Pass all 11 gates = RFU Certified**

## Usage

```
/rfu-audit [project-path] [--quick] [--auto-analyze] [--history]
/rfu-audit ~/Dev/my-project
/rfu-audit ~/Dev/my-project --quick
/rfu-audit ~/Dev/my-project --auto-analyze
/rfu-audit ~/Dev/my-project --auto-analyze --quick
/rfu-audit ~/Dev/my-project --history
```

If no path provided, audits the current working directory.

### Mode Selection

| Flag | Mode | Gates | Config | Template | Pre-step |
|------|------|-------|--------|----------|----------|
| (none) | Full | 11 gates | `config/gates-full.md` | `templates/audit-report.md` | - |
| `--quick` | Quick | 5 gates | `config/gates-quick.md` | `templates/audit-report-quick.md` | - |
| `--auto-analyze` | Full + Extract | 11 gates | `config/gates-full.md` | `templates/audit-report.md` | Extract & verify |
| `--auto-analyze --quick` | Quick + Extract | 5 gates | `config/gates-quick.md` | `templates/audit-report-quick.md` | Extract & verify |
| `--history` | History View | N/A | N/A | `guides/AUDIT-HISTORY.md` | Show audit timeline |

**Quick mode** runs a 5-gate subset (Gates 1, 3, 4, 5, 9) for rapid triage in 5-10 minutes. Use it to filter projects before committing to a full audit.

**Full mode** runs all 11 gates for comprehensive evaluation (45-90 minutes). Use it after quick mode passes or for final go/no-go decisions.

**Auto-analyze mode** extracts project information from README.md and package.json before scoring begins. Extracted data is presented as suggestions for human verification. Use it to reduce manual data entry when auditing unfamiliar projects.

**Important:** Auto-analyze extracts suggestions, not scores. Human verification is required before gate scoring begins.

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

---

## Auto-Analyze (Optional)

When invoked with `--auto-analyze`, the skill extracts project context from files before scoring begins.

**What gets extracted:**
- Project name, one-line description
- Value proposition, target users
- Key features, installation time estimate, prerequisites

**How it works:**
1. Read README.md and package.json
2. Extract fields using structured prompts
3. Present suggestions with confidence levels (HIGH/MEDIUM/LOW/UNCERTAIN)
4. Human verifies each suggestion (approve/edit/reject)
5. Verified data available during gate scoring

**Graceful degradation:**
- If extraction fails for a field, prompt manual entry
- Partial extraction still proceeds (only project_name required)
- Auto-analyze failure does not block the audit

For complete extraction heuristics, confidence rules, and verification workflow, see `guides/AUTO-ANALYZE.md`.

---

## History Tracking

Audit results are automatically saved for comparison on re-audits.

**Save location:** `.planning/audits/{project-name}/{mode}/rfu-audit-YYYYMMDD-HHMMSS.md`

**What gets tracked:**
- Project name, path, date
- Audit mode (full or quick)
- Score and gate results
- Full report content

**On re-audit:**
- Previous audit detected automatically
- Score delta shown in Executive Summary
- Gate changes shown in Scorecard table
- Deltas: ↑ improved, ↓ regressed, = unchanged

**View history:**
Use `--history` flag to see audit timeline instead of running new audit.

For complete history tracking specification, see `guides/AUDIT-HISTORY.md`.

---

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

0. **Validate input**: Run 6-stage validation (see Input Validation section)
1. **Check for --history flag**: If present, show audit timeline and exit (see `guides/AUDIT-HISTORY.md`)
2. **Read project files**: README, package.json, main source files
3. **Detect previous audit** (automatic):
   - Extract normalized project name
   - Check `.planning/audits/{project-name}/{mode}/` for previous audits
   - If found, load most recent for comparison
4. **Extract context (if --auto-analyze)**:
   - Read README.md and package.json
   - Use structured extraction to identify: project name, description, value proposition, target users, key features, installation time estimate, prerequisites
   - Present extracted suggestions with confidence levels
   - Await human verification (approve/edit/reject each field)
   - For complete extraction workflow, see `guides/AUTO-ANALYZE.md`
5. **Select mode**: Based on `--quick` flag:
   - Quick mode: Use `config/gates-quick.md` (5 gates)
   - Full mode: Use `config/gates-full.md` (11 gates)
6. **Walk through each gate sequentially** (per selected config)
7. **Score each gate**: Pass (1) or Fail (0) — use extracted context if available
8. **Provide evidence** for each score
9. **Generate priority matrix**:
   - Rank failed gates using impact-effort matrix (see `guides/PRIORITY-MATRIX.md`)
   - Assign effort estimates (quick/medium/involved) per gate
   - Link resources inline with each fix
   - Note gate dependencies (unlocks/blocked by)
   - For all-pass audits, check for borderline gates as optimization opportunities
10. **Compute deltas** (if previous audit exists):
    - Compare gate results
    - Prepare inline delta indicators
11. **Output structured report** using corresponding template:
    - Quick mode: `templates/audit-report-quick.md`
    - Full mode: `templates/audit-report.md`
12. **Save audit**:
    - Create `.planning/audits/{project-name}/{mode}/` if needed
    - Write report with YAML frontmatter
    - Confirm save location

> **Using Extracted Context**
>
> When --auto-analyze is used, verified extracted data is available during gate scoring:
> - Gate 1 (5 Whys): Use `value_proposition` as starting point for why chain
> - Gate 3 (10-Minute): Use `installation_time_estimate` and `prerequisites`
> - Gate 4 (Bartender): Use `one_line_description` as candidate sentence
> - Gate 5 (Wallet): Use `target_users` when identifying who would pay
> - Gate 10 (Pareto): Use `key_features` as feature list to analyze
>
> Extracted data accelerates scoring but does not replace gate evaluation. Each gate still requires judgment.

For borderline cases, consult `guides/EDGE-CASES.md`.
For auto-analyze extraction details, see `guides/AUTO-ANALYZE.md`.
For input validation patterns, see `guides/INPUT-VALIDATION.md`.
For priority matrix generation, see `guides/PRIORITY-MATRIX.md`.
For history tracking details, see `guides/AUDIT-HISTORY.md`.

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
