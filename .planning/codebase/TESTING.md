# Testing Patterns

**Analysis Date:** 2026-01-22

## Project Type and Test Framework

This is a Claude Code skill project — a documentation and template-based tool. There are no automated tests, no test runner, no assertion libraries, and no compiled code to test.

The skill operates by:
1. Claude Code reading `SKILL.md` to understand the skill definition
2. Claude processing user input (project path)
3. Claude generating audit output using `templates/audit-report.md` as template
4. Claude returning structured report to user

**Testing Approach:** Manual validation only. No test framework employed.

## Manual Validation Approach

Since this is a meta-skill (designed to audit other projects), testing is done through:

1. **Skill Execution** - Run `/rfu-audit [project]` against test projects
2. **Output Validation** - Verify the generated report follows `templates/audit-report.md` structure
3. **Gate Logic** - Manually verify all 11 gates execute correctly
4. **Template Rendering** - Confirm all template variables are filled appropriately

## Files Related to Testing/Validation

**Validation Artifacts:**
- `skill/SKILL.md` — Skill definition that Claude reads
- `skill/templates/audit-report.md` — Expected output structure
- `README.md` — Example output (lines 74-98) shows expected report format
- `install.sh` — Installation verification (lines 38-49)

**No Test Files:**
- No `*.test.md`, `*.spec.md`, or test directories
- No test configuration files (jest.config, vitest.config, etc.)
- No test fixtures or mock data

## Manual Testing Patterns

**Installation Verification (install.sh):**
```bash
# Verify installation success
if [ -f "$INSTALL_DIR/SKILL.md" ]; then
    echo -e "${GREEN}Success!${NC} RFU Audit installed to $INSTALL_DIR"
else
    echo -e "${RED}Error:${NC} Installation failed - SKILL.md not found"
    exit 1
fi
```

This pattern:
- Checks for expected file presence
- Reports success/failure with color-coded output
- Exits with code 1 on failure

**Dry-Run Example (README.md):**
Lines 74-98 show example output, demonstrating:
- Gate results with ✅/❌ status
- Score calculation (7/11)
- Priority fixes list
- This serves as reference for expected output format

## What Gets Validated

**Skill Definition (SKILL.md):**
1. All 11 gates properly defined (Gate 1-11, lines 27-242)
2. Each gate includes:
   - Clear process/questions
   - Pass criteria
   - Fail indicators
   - Process steps

**Template Rendering (audit-report.md):**
1. All template variables present and correctly named
2. Correct variable placement within sections
3. Status indicators work correctly (✅/❌)
4. Table formatting is valid markdown

**Installation (install.sh):**
1. Skill directory copied correctly
2. SKILL.md exists in target location
3. Colors output correctly (if terminal supports it)
4. Error handling for duplicate installations

## Testing the Skill Manually

**To validate a skill execution:**

```bash
# 1. Install the skill
bash install.sh --force

# 2. Run against a test project
/rfu-audit ~/Dev/test-project

# 3. Verify output contains:
#    - All 11 gates with status
#    - Score calculation (X/11)
#    - Gate results section
#    - Priority fixes
#    - Next steps
```

**Expected Report Structure** (from audit-report.md):
```markdown
# RFU Audit Report

**Project:** [name]
**Date:** [date]

## Executive Summary
[Score and rating]

## Gate Results
[All 11 gates with details]

## Scorecard
[Status table]

## Priority Fixes
[Categorized recommendations]

## Next Steps
```

## Test Coverage Gaps

**What's Not Tested:**
- No automated validation of gate logic
- No unit tests for question relevance
- No tests for template variable coverage
- No regression tests for report format changes
- No tests for edge cases (missing README, empty project, etc.)

**Risk Areas Without Tests:**
- Gate descriptions could become inconsistent between SKILL.md and template
- Template variables could be typo'd without detection
- Installation script could fail silently on certain systems
- Edge cases (special characters in project names, deep paths) untested

## Quality Assurance Approach

**Manual Checklist Before Release:**
1. Read through all 11 gates in SKILL.md
2. Verify each gate has clear:
   - Process or questions
   - Pass criteria
   - Fail indicators
3. Run `/rfu-audit` against 3-5 real projects
4. Verify generated reports:
   - All gates represented
   - No template variable leakage ({{unparsed}})
   - Scoring logic correct
   - All sections populated
5. Test installation on clean system:
   ```bash
   bash install.sh
   /rfu-audit
   ```
6. Test force reinstall:
   ```bash
   bash install.sh --force
   ```

## Documentation as Specification

Since there are no unit tests, documentation serves as the specification:

**SKILL.md** — Authoritative definition of:
- What each gate measures (lines 27-242)
- Pass/fail criteria for each gate
- Integration points with other skills
- Process steps for using the skill

**audit-report.md** — Specification for:
- Output structure
- Required sections
- Variable naming convention
- Status indicator format

**README.md** — Specification for:
- User-facing behavior
- Example output
- Installation process
- Usage patterns

## Testing Philosophy

For a documentation/template-based skill:
- **Manual validation** is primary testing method
- **Reference implementations** (example output in README) serve as acceptance tests
- **File structure validation** is the only automated check (install.sh)
- **Integration testing** happens when users run the skill

This approach is appropriate because:
1. The skill is primarily driven by human judgment (Claude asking questions)
2. Output quality depends on Claude's reasoning, not code logic
3. Template rendering is simple string substitution without complex logic
4. The skill is meant to be human-understandable documentation

---

*Testing analysis: 2026-01-22*
