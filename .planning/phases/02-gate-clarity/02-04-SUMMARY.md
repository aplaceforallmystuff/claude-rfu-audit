---
phase: 02-gate-clarity
plan: 04
subsystem: audit-framework
tags: [decision-rules, edge-cases, scoring-guidance, determinism]

# Dependency graph
requires:
  - phase: 02-01
    provides: Objective rubrics for Gates 1-4
  - phase: 02-02
    provides: Objective rubrics for Gates 5-8
  - phase: 02-03
    provides: Objective rubrics for Gates 9-11
provides:
  - Comprehensive edge case resolution guide with 27 explicit decision rules
  - Cross-gate edge cases (N/A determination, inter-gate dependencies)
  - Decision tree summary for structured edge case resolution
  - Process for extending guide with new edge cases
affects: [03-gate-fail-examples, 04-report-templates, implementation]

# Tech tracking
tech-stack:
  added: []
  patterns: [decision-tree-documentation, tie-breaker-rules, deterministic-scoring]

key-files:
  created: []
  modified: [skill/guides/EDGE-CASES.md]

key-decisions:
  - "27 edge case resolution rules covering all gray areas from gate rubrics"
  - "Cross-gate edge cases documented separately (N/A determination, inter-gate dependencies)"
  - "Decision tree provides 4-step process: disqualifiers → primary criteria → edge case lookup → document"
  - "Guide is extensible with explicit process for adding new edge cases"

patterns-established:
  - "Resolution rule format: When → Question → Resolution rule → Tie-breaker test"
  - "Edge cases include concrete examples and quantitative thresholds"
  - "Proactive edge cases added based on prior rubric development (6 additional beyond explicit references)"

# Metrics
duration: 6min
completed: 2026-01-22
---

# Phase 02 Plan 04: Edge Case Resolution Guide Summary

**Comprehensive edge case resolution guide with 27 explicit decision rules, eliminating auditor ambiguity in borderline scoring situations**

## Performance

- **Duration:** 6 min
- **Started:** 2026-01-22T14:44:35Z
- **Completed:** 2026-01-22T14:50:56Z
- **Tasks:** 2
- **Files modified:** 1

## Accomplishments

- Created comprehensive EDGE-CASES.md with 27 resolution rules (21 explicit from gates-full.md + 6 proactive)
- Documented cross-gate edge cases (N/A determination, inter-gate dependencies)
- Established decision tree summary for structured approach to edge cases
- Verified all edge case references in gates-full.md have corresponding resolution rules
- Documented process for extending guide with new edge cases as audits uncover them

## Task Commits

1. **Task 1: Create comprehensive edge case resolution guide** - `0dd8b0d` (docs)
   - 27 explicit resolution rules for borderline scoring
   - Cross-gate edge cases section
   - Gate-specific edge cases for all 11 gates
   - Decision tree summary
   - Guide extensibility process

2. **Task 2: Verify cross-references** - No commit needed (verification only)
   - Confirmed all 21 explicit edge case references in gates-full.md are covered
   - Identified 6 proactive edge cases added from prior rubric development
   - Cross-reference count: 21 explicit + 6 proactive = 27 total

## Files Created/Modified

- `skill/guides/EDGE-CASES.md` - Replaced placeholder with comprehensive edge case resolution guide (461 insertions, 29 deletions)

## Decisions Made

**Edge case resolution structure:**
- Format: When → Question → Resolution rule → Tie-breaker test
- Each rule includes concrete examples and quantitative thresholds where applicable
- Proactive inclusion of edge cases identified during rubric development but not yet explicitly referenced

**Cross-gate edge cases documented:**
- N/A determination (when gates don't apply to certain project types)
- Inter-gate dependencies (when one gate's failure affects another's scoring)

**Decision tree approach:**
1. Check for automatic FAIL disqualifiers
2. Apply primary gate criteria
3. If borderline, lookup edge case in guide
4. Document which edge case rule was applied

**Guide extensibility:**
- Explicit process for adding new edge cases: document ambiguity → record signals → define resolution rule → add to guide → note in audit
- Enables guide to grow with audit experience

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered

None.

## Next Phase Readiness

Phase 2 (Gate Clarity) is now complete with all 4 plans finished:
- 02-01: Objective rubrics for Gates 1-4
- 02-02: Objective rubrics for Gates 5-8
- 02-03: Objective rubrics for Gates 9-11
- 02-04: Edge case resolution guide

All gates now have:
✓ Objective measurement protocols
✓ Scoring formulas (pass/fail/borderline thresholds)
✓ Edge case resolution rules
✓ FAIL example criteria documented

Ready for Phase 3 (Gate FAIL Examples) to create concrete examples for each gate's failure modes.

---
*Phase: 02-gate-clarity*
*Completed: 2026-01-22*
