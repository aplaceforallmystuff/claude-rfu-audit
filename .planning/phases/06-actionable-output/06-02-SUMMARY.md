---
phase: 06-actionable-output
plan: 02
subsystem: templates
tags: [priority-matrix, audit-report, tiered-display, effort-estimates]

# Dependency graph
requires:
  - phase: 06-01
    provides: Priority Matrix generation guide with impact-effort ranking algorithm
provides:
  - Priority Matrix section in full audit template (audit-report.md)
  - Priority Matrix section in quick audit template (audit-report-quick.md)
  - Priority matrix generation step in SKILL.md process
affects: [07-phase]

# Tech tracking
tech-stack:
  added: []
  patterns:
    - Tiered display format (Top Priority detailed, Others compact, Borderline recommended)
    - Pseudo-Handlebars template syntax for dynamic sections

key-files:
  modified:
    - skill/templates/audit-report.md
    - skill/templates/audit-report-quick.md
    - skill/SKILL.md

key-decisions:
  - "Priority Matrix replaces Priority Fixes in full template - structured tiers vs flat categories"
  - "Same Priority Matrix format for quick and full templates - consistency across modes"
  - "All-pass case handles borderline gates as optimization opportunities"

patterns-established:
  - "Dynamic sections in templates reference guide files via HTML comments"
  - "Template variables follow pseudo-Handlebars syntax for illustration"

# Metrics
duration: 2min
completed: 2026-01-28
---

# Phase 06 Plan 02: Template Priority Matrix Integration Summary

**Audit templates now include tiered Priority Matrix section with effort estimates and resource links inline**

## Performance

- **Duration:** 2 min
- **Started:** 2026-01-28T11:57:14Z
- **Completed:** 2026-01-28T11:59:27Z
- **Tasks:** 3
- **Files modified:** 3

## Accomplishments

- Replaced old "Priority Fixes" section in full template with structured Priority Matrix
- Added Priority Matrix section to quick audit template with same format
- Updated SKILL.md process step 7 to explicitly generate priority matrix before report output
- Added guide references to PRIORITY-MATRIX.md in SKILL.md

## Task Commits

Each task was committed atomically:

1. **Task 1: Update full audit template with Priority Matrix** - `f7174c9` (feat)
2. **Task 2: Update quick audit template with Priority Matrix** - `f0ff2b3` (feat)
3. **Task 3: Update SKILL.md process to include priority matrix generation** - `5f77096` (docs)

## Files Created/Modified

- `skill/templates/audit-report.md` - Replaced Priority Fixes with tiered Priority Matrix section
- `skill/templates/audit-report-quick.md` - Added Priority Matrix section after Quick Scorecard
- `skill/SKILL.md` - Added step 7 (priority matrix generation) and guide reference

## Decisions Made

None - followed plan as specified

## Deviations from Plan

None - plan executed exactly as written

## Issues Encountered

None

## User Setup Required

None - no external service configuration required.

## Next Phase Readiness

- Both audit templates now include actionable Priority Matrix sections
- SKILL.md process explicitly directs auditor to generate priority matrix using the guide
- Ready for 06-03 (Template Variable Specification) to document all template variables
- Phase 6 (Actionable Output) is complete pending 06-03 execution

---
*Phase: 06-actionable-output*
*Completed: 2026-01-28*
