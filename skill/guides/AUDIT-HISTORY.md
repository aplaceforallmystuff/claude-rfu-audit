# Audit History Guide

Audit history tracking enables comparison of audit results over time. Each audit is saved with metadata, allowing detection of score changes and gate-level improvements or regressions.

## Storage Format

**Location:** `.planning/audits/{project-name}/{mode}/`

**Filename:** `rfu-audit-YYYYMMDD-HHMMSS.md` (timestamp-based)

**Project name extraction:**
1. Try `package.json` "name" field
2. Fall back to directory name
3. Normalize: lowercase, trim whitespace, convert spaces to hyphens

**Examples:**
- Project "my-cli-tool" → `.planning/audits/my-cli-tool/full/rfu-audit-20260128-143022.md`
- Project "Personal Dashboard" → `.planning/audits/personal-dashboard/quick/rfu-audit-20260127-103312.md`

**Directory structure:**
```
.planning/
└── audits/
    └── my-project/
        ├── full/
        │   ├── rfu-audit-20260128-143022.md
        │   └── rfu-audit-20260130-091544.md
        └── quick/
            └── rfu-audit-20260127-103312.md
```

## YAML Frontmatter

Every saved audit report includes YAML frontmatter with structured metadata. Templates populate these fields during report generation.

**Structure:**
```yaml
---
rfu_audit:
  version: "1.0"
  project_name: "my-cli-tool"
  project_path: "/Users/jim/Dev/my-cli-tool"
  audit_date: "2026-01-28"
  audit_mode: "full"  # or "quick"
  score: 8
  max_score: 11  # or 5 for quick
  gate_results:
    gate1: "PASS"
    gate2: "FAIL"
    gate3: "PASS"
    gate4: "PASS"
    gate5: "N/A"
    gate6: "PASS"
    gate7: "FAIL"
    gate8: "PASS"
    gate9: "FAIL"
    gate10: "PASS"
    gate11: "PASS"
---
```

**Fields:**
- `version`: RFU Audit format version (always "1.0" currently)
- `project_name`: Normalized project name
- `project_path`: Absolute path to project directory
- `audit_date`: Date in YYYY-MM-DD format
- `audit_mode`: "full" or "quick"
- `score`: Number of gates passed
- `max_score`: Total gates evaluated (11 for full, 5 for quick)
- `gate_results`: Map of gate IDs to verdicts (PASS/FAIL/N/A)

**Gate results:**
- Full mode: All 11 gates (gate1 through gate11)
- Quick mode: Only 5 gates (gate1, gate3, gate4, gate5, gate9)

## History Detection

History detection runs automatically on every audit. No flag required.

**Process:**
1. Extract project name (normalized)
2. Check for `.planning/audits/{project-name}/{mode}/` directory
3. If directory exists, list all `rfu-audit-*.md` files
4. Sort by filename (natural sort by timestamp)
5. Select most recent file
6. Parse YAML frontmatter to extract `gate_results`
7. If no history, proceed without comparison

**Timing:** History detection occurs after validation and file reading, before gate scoring begins.

**Failure handling:**
- No `.planning/audits/` directory: No history (first audit)
- No `{project-name}/` directory: No history for this project
- No `{mode}/` directory: No history for this mode (first audit of type)
- Empty directory: No history
- Corrupted frontmatter: Log warning, skip comparison, proceed with audit

## Comparison Algorithm

For each gate, compare previous result to current result.

**Comparison table:**

| Previous | Current | Indicator | Meaning |
|----------|---------|-----------|---------|
| FAIL     | PASS    | `↑`       | Improved |
| PASS     | FAIL    | `↓`       | Regressed |
| PASS     | PASS    | `=`       | Unchanged (still passing) |
| FAIL     | FAIL    | `=`       | Unchanged (still failing) |
| N/A      | PASS    | `NEW`     | Newly applicable, passing |
| N/A      | FAIL    | `NEW`     | Newly applicable, failing |
| PASS     | N/A     | `REMOVED` | No longer applicable (was passing) |
| FAIL     | N/A     | `REMOVED` | No longer applicable (was failing) |
| N/A      | N/A     | `-`       | Not applicable in either audit |

**Score delta calculation:**
```
delta = current_score - previous_score
```

**Delta indicator:**
- Positive delta: `↑ +N` (improvement)
- Negative delta: `↓ -N` (regression)
- Zero delta: `-` (no change)

## Inline Delta Display

Deltas appear in two locations within the audit report:

### 1. Executive Summary

Show score with delta in parentheses:

**Current audit (no history):**
```markdown
Score: 8/11
```

**Re-audit (with history):**
```markdown
Score: 8/11 ↑ (was 7/11)
```

**Regression example:**
```markdown
Score: 6/11 ↓ (was 8/11)
```

**No change:**
```markdown
Score: 8/11 (was 8/11)
```

### 2. Scorecard Table

Add "Change" column showing per-gate indicators:

**Current audit (no history):**
```markdown
| Gate | Status | Evidence |
|------|--------|----------|
| 1    | PASS   | ... |
| 2    | FAIL   | ... |
```

**Re-audit (with history):**
```markdown
| Gate | Status | Change | Evidence |
|------|--------|--------|----------|
| 1    | PASS   | =      | ... |
| 2    | PASS   | ↑      | ... |
| 3    | FAIL   | ↓      | ... |
| 4    | N/A    | NEW    | ... |
```

**Column order:** Gate, Status, Change, Evidence

**Change column:** Only present when history exists. Omit column for first audit.

## --history Flag

When invoked with `--history`, skip audit execution and display audit timeline.

**Usage:**
```
/rfu-audit ~/Dev/my-project --history
```

**Output format:**
```markdown
# Audit History: my-cli-tool

## Full Audits
| Date       | Score | Change | File |
|------------|-------|--------|------|
| 2026-01-30 | 8/11  | ↑ +1   | rfu-audit-20260130-091544.md |
| 2026-01-28 | 7/11  | -      | rfu-audit-20260128-143022.md |

## Quick Audits
| Date       | Score | Change | File |
|------------|-------|--------|------|
| 2026-01-27 | 4/5   | -      | rfu-audit-20260127-103312.md |

Total: 3 audits (2 full, 1 quick)
```

**Sections:**
- One section per mode (Full Audits, Quick Audits)
- Only show sections with audits (omit if empty)
- Sort by date descending (most recent first)

**Change column:**
- Compare to previous audit in SAME mode
- First audit in mode: `-` (no comparison)
- Delta format: `↑ +N` or `↓ -N` where N is score difference

**File column:**
- Filename only (not full path)
- User can navigate to `.planning/audits/{project-name}/{mode}/` to read full reports

**Empty history:**
```markdown
# Audit History: my-cli-tool

No audit history found.

Run an audit to create the first entry:
  /rfu-audit ~/Dev/my-cli-tool
```

## Save Behavior

Audits are saved automatically (always on). No flag required.

**Process:**
1. Generate report content (with or without history comparison)
2. Add YAML frontmatter with metadata
3. Create directory structure if needed: `.planning/audits/{project-name}/{mode}/`
4. Write report to timestamped file: `rfu-audit-YYYYMMDD-HHMMSS.md`
5. Confirm save location to user

**Confirmation message:**
```
Audit saved to: .planning/audits/my-cli-tool/full/rfu-audit-20260128-143022.md
```

**Save happens AFTER report display.** User sees report immediately, then receives save confirmation.

## Error Handling

Errors during history tracking should not block the audit. Graceful degradation ensures users still get audit results.

### No .planning Directory

**Symptom:** `.planning/` does not exist in project root.

**Action:** Create it automatically.

**Message:** No message (silent creation, directory is standard).

### Permission Denied (Save)

**Symptom:** Cannot write to `.planning/audits/` directory.

**Action:**
1. Show error message
2. Display report normally (save is convenience, not requirement)

**Message:**
```
Warning: Could not save audit to .planning/audits/
  Error: Permission denied

Audit report generated successfully (see below).
```

### Corrupted Frontmatter (Load)

**Symptom:** Previous audit file exists but YAML frontmatter is malformed or missing `gate_results`.

**Action:**
1. Log warning
2. Skip comparison (treat as "no history")
3. Proceed with current audit normally

**Message:**
```
Warning: Previous audit found but could not parse metadata
  File: .planning/audits/my-cli-tool/full/rfu-audit-20260126-100000.md

Proceeding without comparison.
```

### Missing Gate Results (Load)

**Symptom:** Previous audit frontmatter exists but missing some gate results (e.g., old format, partial data).

**Action:**
1. Compare gates that exist in both audits
2. Treat missing gates as if no history for those gates
3. Display partial comparison

**Message:** No message (graceful degradation, show available comparisons).

### Mode Mismatch

**Symptom:** Previous audits exist but for different mode (e.g., only quick audits exist, user runs full).

**Action:** No comparison (history is mode-specific).

**Behavior:** First full audit won't compare to previous quick audits, and vice versa.

---

## Related

- `SKILL.md` - Main skill definition with history workflow integration
- `templates/audit-report.md` - Full mode template (includes frontmatter generation)
- `templates/audit-report-quick.md` - Quick mode template (includes frontmatter generation)
