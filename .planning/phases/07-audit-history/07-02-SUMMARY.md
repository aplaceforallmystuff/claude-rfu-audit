---
phase: 07-audit-history
plan: 02
subsystem: reporting
tags: [templates, yaml, frontmatter, history-tracking, delta-display]

# Dependency graph
requires:
  - phase: 07-01
    provides: History tracking guide with storage format and comparison algorithm
provides:
  - Machine-readable YAML frontmatter in both audit templates
  - Conditional delta display for score and gate changes
  - Complete documentation of history variables in TEMPLATE-SPEC.md
affects: [skill-implementation, future-features]

# Tech tracking
tech-stack:
  added: []
  patterns:
    - YAML frontmatter for machine-readable metadata
    - Conditional rendering with Handlebars {{#if}} blocks

key-files:
  created: []
  modified:
    - skill/templates/audit-report.md
    - skill/templates/audit-report-quick.md
    - skill/reference/TEMPLATE-SPEC.md

key-decisions:
  - "Use YAML frontmatter at template top for machine-readable history metadata"
  - "Conditional delta display only when has_previous=true (clean first audits)"
  - "Quick mode uses subset of 5 gate results (gates 1,3,4,5,9)"

patterns-established:
  - "Frontmatter includes audit metadata, mode, score, and gate_results array"
  - "Delta indicators: ↑ improved, ↓ regressed, = unchanged, NEW/REMOVED for N/A transitions"

# Metrics
duration: 3min
completed: 2026-01-28
---

# Phase 7 Plan 2: Template History Integration Summary

**Both audit templates now produce machine-readable YAML frontmatter and display score/gate deltas when comparing to previous audits**

## Performance

- **Duration:** 3 min
- **Started:** 2026-01-28T13:04:17Z
- **Completed:** 2026-01-28T13:06:55Z
- **Tasks:** 2
- **Files modified:** 3

## Accomplishments

- Full audit template has YAML frontmatter with 11 gate results and conditional delta display
- Quick audit template has YAML frontmatter with 5 gate results and conditional delta display
- TEMPLATE-SPEC.md documents 25 new history variables (12 frontmatter + 13 comparison)
- All three files cross-reference guides/AUDIT-HISTORY.md for history tracking documentation

## Task Commits

Each task was committed atomically:

1. **Task 1: Update templates with frontmatter and delta display** - `424ac9d` (feat)
2. **Task 2: Document history variables in TEMPLATE-SPEC.md** - `53a55f6` (docs)

**Plan metadata:** Will be committed after SUMMARY creation

## Files Created/Modified

- `skill/templates/audit-report.md` - Added YAML frontmatter with 11 gate results, conditional delta in Executive Summary and Scorecard
- `skill/templates/audit-report-quick.md` - Added YAML frontmatter with 5 gate results, conditional delta in Quick Score and Scorecard
- `skill/reference/TEMPLATE-SPEC.md` - Added History Variables section, updated variable counts from 98 to 123, version bumped to 1.1

## Decisions Made

1. **YAML frontmatter placement**: Put at very top of template (before markdown title) for clean parsing
2. **Conditional rendering strategy**: Use `{{#if has_previous}}` blocks so first audits render cleanly without delta artifacts
3. **Quick mode subset**: Only 5 gate results in frontmatter (gates 1,3,4,5,9) matching quick mode critical path
4. **Delta indicators**: Use Unicode arrows (↑↓=) for visual clarity in markdown tables
5. **Cross-reference pattern**: All templates and specs reference guides/AUDIT-HISTORY.md for implementation details

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered

None

## User Setup Required

None - no external service configuration required.

## Next Phase Readiness

Templates are now history-ready. Next step: Implement comparison algorithm in skill logic to:
- Load previous audit from `.planning/audits/{project}/`
- Parse YAML frontmatter to extract gate_results
- Calculate deltas and populate has_previous, score_delta, gate*_change variables
- Render templates with history context

Phase 7 (Audit History) now 100% complete (2/2 plans).

---
*Phase: 07-audit-history*
*Completed: 2026-01-28*
