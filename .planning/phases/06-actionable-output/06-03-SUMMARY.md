---
phase: 06-actionable-output
plan: 03
subsystem: documentation
tags: [templates, variables, specification, reference]

# Dependency graph
requires:
  - phase: 01-modular-architecture
    provides: templates directory structure and audit-report templates
  - phase: 04-quick-audit-mode
    provides: quick audit template with 5-gate subset
provides:
  - Complete template variable specification (98 static + 2 dynamic)
  - Variable type, format, and description for all 11 gates
  - Template-to-variable mapping for both audit modes
  - Cross-references to gate definitions and guides
affects: [template-maintenance, future-gate-additions, documentation]

# Tech tracking
tech-stack:
  added: []
  patterns: [variable-documentation-format]

key-files:
  created:
    - skill/reference/TEMPLATE-SPEC.md
  modified: []

key-decisions:
  - "98 total static variables (85 gate + 13 metadata/mode-specific)"
  - "Documented both full and quick template variable subsets"
  - "Included dynamic section documentation (Priority Matrix, Next Steps)"

patterns-established:
  - "Variable documentation: Type, Format, Description table per gate"
  - "Template-to-variable mapping section for quick reference"

# Metrics
duration: 2min
completed: 2026-01-28
---

# Phase 06 Plan 03: Template Variable Specification Summary

**Complete variable specification documenting all 98 static variables and 2 dynamic sections across both audit report templates**

## Performance

- **Duration:** 2 min
- **Started:** 2026-01-28T11:52:06Z
- **Completed:** 2026-01-28T11:54:25Z
- **Tasks:** 2
- **Files modified:** 1

## Accomplishments
- Created comprehensive TEMPLATE-SPEC.md with all template variables
- Documented all 11 gates with variable tables (type, format, description)
- Added template-to-variable mapping for both full and quick audit modes
- Documented dynamic sections (Priority Matrix, Next Steps) with generation logic

## Task Commits

Each task was committed atomically:

1. **Task 1: Create reference directory if needed** - Skipped (directory already existed)
2. **Task 2: Create TEMPLATE-SPEC.md** - `5d184d7` (docs)

**Plan metadata:** Combined with task commit

## Files Created/Modified
- `skill/reference/TEMPLATE-SPEC.md` - Complete template variable specification with 98 static variables across report metadata (6), quick mode specific (3), full report specific (4), and 11 gates (85 variables total)

## Decisions Made
- **Variable count:** Documented 98 static variables total (more than the 85 in gates-full.md summary because it includes metadata and mode-specific variables)
- **Dynamic sections:** Documented Priority Matrix and Next Steps as dynamic rather than static template variables since they're generated based on audit results
- **Cross-references:** Added references to config/gates-full.md, config/gates-quick.md, guides/EDGE-CASES.md, and guides/AUTO-ANALYZE.md

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered
- Reference directory already existed with .gitkeep file, so Task 1 was a no-op verification

## User Setup Required

None - no external service configuration required.

## Next Phase Readiness
- Template variable specification complete for ARCH-08 requirement
- Ready for Phase 6 completion (all 3 plans done once 06-01 and 06-02 are complete)
- TEMPLATE-SPEC.md provides single reference for template development

---
*Phase: 06-actionable-output*
*Completed: 2026-01-28*
