---
phase: 06-actionable-output
plan: 01
subsystem: guides
tags: [priority-matrix, effort-estimation, impact-assessment, resource-linking]

# Dependency graph
requires:
  - phase: 02-gate-clarity
    provides: Gate definitions and thresholds for priority assessment
provides:
  - Priority ranking algorithm (impact-effort matrix)
  - Gate-specific effort defaults for all 11 gates
  - Resource linking reference with skill invocations
  - Tiered display format specification
  - Dependency arrow notation
affects: [06-02, 06-03, report-generation]

# Tech tracking
tech-stack:
  added: []
  patterns: [impact-effort-matrix, tiered-display, dependency-arrows]

key-files:
  created:
    - skill/guides/PRIORITY-MATRIX.md
  modified: []

key-decisions:
  - "HIGH impact gates: 1, 3, 4, 5, 7, 11 (viability + adoption barriers)"
  - "MEDIUM impact gates: 2, 6, 8, 9, 10 (quality + optimization)"
  - "Gate 4 defaults to quick effort (text rewrite)"
  - "Gates 2, 9, 11 default to involved effort"

patterns-established:
  - "Impact-effort matrix: HIGH/quick first, then HIGH/medium, then MEDIUM/quick"
  - "Tiered display: Top 3 detailed, remaining compact, borderline as recommendations"
  - "Resource linking: skill invocation > file path > fallback guidance"

# Metrics
duration: 3min
completed: 2026-01-28
---

# Phase 6 Plan 1: Priority Matrix Guide Summary

**Impact-effort matrix algorithm with gate-specific effort defaults and resource linking for all 11 gates**

## Performance

- **Duration:** 3 min
- **Started:** 2026-01-28T11:52:04Z
- **Completed:** 2026-01-28T11:54:06Z
- **Tasks:** 1
- **Files created:** 1

## Accomplishments

- Complete priority ranking algorithm using impact-effort matrix
- Effort defaults and override rules for all 11 gates
- Resource linking reference with skill invocations, file paths, and fallback guidance
- Tiered display format specification (detailed/compact/recommended)
- Dependency arrow notation for gate relationships
- All-pass and quick mode handling specifications

## Task Commits

1. **Task 1: Create PRIORITY-MATRIX.md guide** - `5d184d7` (feat)

_Note: Task was committed alongside 06-03 plan execution_

## Files Created

- `skill/guides/PRIORITY-MATRIX.md` - Complete guide for generating priority matrices from failed gates

## Decisions Made

| Decision | Rationale |
|----------|-----------|
| HIGH impact = Gates 1, 3, 4, 5, 7, 11 | Core viability (1, 5, 11) or first impression/adoption barrier (3, 4, 7) |
| Gate 4 = quick effort by default | Text rewrite in README requires no code changes |
| Gates 2, 9, 11 = involved effort by default | Require new unique value, removing features, or fundamental commitment reassessment |
| Top 3 gates detailed, rest compact | Manages cognitive load while keeping all information visible |
| Resource linking precedence: skill > file path > fallback | Use most specific actionable resource available |

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered

None.

## User Setup Required

None - no external service configuration required.

## Next Phase Readiness

- Priority matrix guide complete and ready for template integration (Plan 02)
- Resource linking patterns established for template variable expansion (Plan 03)
- All 11 gates have effort defaults documented

---
*Phase: 06-actionable-output*
*Completed: 2026-01-28*
