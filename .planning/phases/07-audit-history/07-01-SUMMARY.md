---
phase: 07
plan: 01
subsystem: documentation
tags: [audit-tracking, history, comparison, delta-indicators]

requires:
  - phase: 01
    provides: modular-guide-structure
  - phase: 02
    provides: gate-definitions
  - phase: 04
    provides: quick-mode-gates
  - phase: 05
    provides: auto-analyze-extraction

provides:
  - audit-history-tracking
  - timestamped-audit-saves
  - delta-comparison-algorithm
  - history-timeline-view

affects:
  - phase: 07-02
    impact: Templates need frontmatter generation

tech-stack:
  added: []
  patterns:
    - file-based-history-storage
    - yaml-frontmatter-metadata
    - automatic-comparison-detection

key-files:
  created:
    - skill/guides/AUDIT-HISTORY.md
  modified:
    - skill/SKILL.md

decisions:
  - id: history-storage-location
    choice: .planning/audits/{project-name}/{mode}/
    rationale: Keeps history with project planning artifacts, mode-specific for independent comparison
    alternatives: Single directory, JSON database

  - id: filename-timestamp-format
    choice: rfu-audit-YYYYMMDD-HHMMSS.md
    rationale: Natural sort by timestamp, no milliseconds needed for manual audits
    alternatives: ISO 8601, Unix timestamp, sequential numbering

  - id: history-detection-timing
    choice: Automatic on every audit, no flag required
    rationale: Comparison is valuable when available, no user action needed
    alternatives: --compare flag, manual diff command

  - id: delta-indicators
    choice: Unicode arrows (↑↓=) plus NEW/REMOVED for N/A transitions
    rationale: Visual clarity, semantic meaning, handles edge cases
    alternatives: +/-, color codes, numeric diff

  - id: save-behavior
    choice: Always save (default on)
    rationale: History is valuable for all audits, user doesn't need to opt in
    alternatives: --save flag, prompt after audit

metrics:
  duration: 2min 24sec
  completed: 2026-01-28
---

# Phase 07 Plan 01: Audit History Tracking Summary

**One-liner:** File-based history tracking with automatic comparison, delta indicators, and timeline view using timestamped YAML-frontmatter audit files in .planning/audits/

## What Was Built

### AUDIT-HISTORY.md Guide (319 lines)
Comprehensive specification for audit history tracking:
- **Storage format**: `.planning/audits/{project-name}/{mode}/rfu-audit-YYYYMMDD-HHMMSS.md` with mode-specific directories
- **YAML frontmatter structure**: version, project metadata, score, gate results for parsing
- **History detection**: Automatic on every audit—check for previous files, load most recent, parse frontmatter
- **Comparison algorithm**: 9 transition states (PASS→FAIL, N/A→PASS, etc.) with semantic indicators
- **Delta display**: Executive Summary score delta and Scorecard table "Change" column
- **--history flag**: Timeline view showing all audits with dates, scores, changes, filenames
- **Save behavior**: Always save after audit generation, graceful degradation on errors
- **Error handling**: Permission denied, corrupted frontmatter, missing gate results

### SKILL.md Process Integration
History functionality integrated into audit workflow:
- **Usage updated**: Added --history flag to usage examples
- **Mode Selection table**: Added History View row with reference to guide
- **History Tracking section**: 20-line overview of automatic save and re-audit comparison
- **Process steps renumbered**: 0-12 (was 0-8) with 4 new history steps:
  - Step 1: Check for --history flag (exit early if timeline view)
  - Step 3: Detect previous audit (automatic, load for comparison)
  - Step 10: Compute deltas (if previous audit exists)
  - Step 12: Save audit (create directories, write with frontmatter, confirm)
- **Guide reference**: Added to bottom of Process section with other guide links

## Deviations from Plan

None—plan executed exactly as written.

## Key Technical Decisions

### Storage Architecture
**Decision:** File-based with mode-specific subdirectories

**Why:**
- Stateless—skill doesn't need to manage database or state
- Mode-independent comparison—full audits compare to full, quick to quick
- Git-friendly—plain text files track naturally with project
- Easy inspection—users can read audit history with any text editor

**Alternative considered:** Single SQLite database
- Rejected: Adds dependency, requires schema migrations, less transparent

### Project Name Normalization
**Decision:** Extract from package.json "name" field, fall back to directory name, normalize to lowercase with hyphens

**Why:**
- package.json name is canonical for npm projects
- Directory name fallback handles non-npm projects
- Normalization prevents duplicate directories (My-Project vs my-project vs my_project)

**Example:** "Personal Dashboard" → "personal-dashboard"

### Automatic vs Opt-in History
**Decision:** History detection and save happen automatically on every audit

**Why:**
- Comparison is valuable when available—no reason to hide it
- Users expect tools to track progress without manual flags
- Graceful degradation—if history fails, audit still works

**Alternative considered:** --save flag to opt in
- Rejected: Extra cognitive load, users forget to use it, loses historical data

### Delta Indicators
**Decision:** Unicode arrows (↑↓=) plus NEW/REMOVED text for N/A transitions

**Why:**
- Visual clarity—arrows immediately convey direction of change
- Semantic meaning—↑ = improved, ↓ = regressed, = = unchanged
- Edge case handling—NEW/REMOVED cover N/A transitions (newly applicable, no longer applicable)
- No color dependency—works in markdown, terminals, plain text

**Alternative considered:** +/- numeric diff
- Rejected: Less semantic (what does "+1" mean?), doesn't handle N/A transitions well

### Timestamp Format
**Decision:** YYYYMMDD-HHMMSS (no milliseconds)

**Why:**
- Natural sort—lexicographic sort equals chronological sort
- Human-readable—clear at a glance when audit happened
- Sufficient precision—manual audits won't collide within same second
- Filesystem-friendly—no colons (Windows-compatible)

**Alternative considered:** ISO 8601 (2026-01-28T14:30:22Z)
- Rejected: Colons problematic on Windows, overkill precision

## Integration Points

### With Existing Guides
- **INPUT-VALIDATION.md**: History detection occurs after validation passes
- **AUTO-ANALYZE.md**: Extraction happens before history comparison
- **PRIORITY-MATRIX.md**: Failed gates ranked regardless of history, but deltas inform priority
- **EDGE-CASES.md**: Comparison algorithm handles edge cases (N/A transitions)

### With Templates (Next Plan)
Plan 07-02 will update templates to:
1. Add YAML frontmatter generation logic
2. Include score delta in Executive Summary
3. Add "Change" column to Scorecard table
4. Populate gate result map from audit scoring

### With Mode Detection (Existing)
History is mode-aware:
- `--quick` checks `.planning/audits/{project-name}/quick/`
- Full audit checks `.planning/audits/{project-name}/full/`
- No cross-mode comparison (full doesn't compare to quick)

## Next Phase Readiness

**Ready for 07-02:** Template updates for frontmatter and delta display

**Blockers:** None

**Concerns:** None

**Dependencies satisfied:**
- Modular guide structure (Phase 01) ✓
- Gate definitions with template variables (Phase 02) ✓
- Quick mode gates (Phase 04) ✓
- Auto-analyze extraction (Phase 05) ✓

## Testing Notes

For plan 07-02 implementation verification:
1. Run audit on project with no history → verify frontmatter generated, no "Change" column
2. Run second audit on same project → verify delta in Executive Summary, "Change" column present
3. Run `--history` on project with multiple audits → verify timeline table with dates, scores, changes
4. Test error cases: permission denied on save, corrupted frontmatter on load

## Files Modified

### Created
- `skill/guides/AUDIT-HISTORY.md` (319 lines)
  - Complete history tracking specification
  - 7 major sections: Storage, Frontmatter, Detection, Comparison, Display, --history Flag, Error Handling

### Modified
- `skill/SKILL.md` (+51 lines, -11 lines)
  - Usage section: Added --history flag
  - Mode Selection table: Added History View row
  - History Tracking section: 20 lines overview
  - Process section: Renumbered 0-12, added steps 1, 3, 10, 12
  - Guide references: Added AUDIT-HISTORY.md link

## Commits

| Task | Commit | Message |
|------|--------|---------|
| 1 | 52dd642 | docs(07-01): create audit history guide |
| 2 | 39b3853 | feat(07-01): add history functionality to skill process |

## Performance

**Duration:** 2min 24sec
**Tasks:** 2/2 completed
**Velocity:** 1.2 min/task

## Knowledge Captured

### Patterns Established
1. **File-based history**: Stateless skills can track state via timestamped files in .planning/
2. **Mode-specific directories**: Independent comparison per mode prevents apples-to-oranges
3. **Automatic comparison**: Don't require flags for features that are always valuable
4. **Graceful degradation**: History failures don't block core functionality (audit still runs)

### Reusable Artifacts
- YAML frontmatter pattern for metadata (reusable in other audits/reports)
- Delta comparison algorithm (PASS/FAIL/N/A with 9 transitions)
- Timeline table format (date, score, change, file)
- Project name normalization (package.json → directory name → lowercase+hyphens)

### Decision Rationale
- Unicode arrows chosen over color codes for accessibility and markdown compatibility
- Timestamp format without colons for cross-platform filesystem compatibility
- Automatic save without opt-in flag reduces user cognitive load
