---
phase: 02-gate-clarity
plan: 02
subsystem: audit-framework
tags: [gates, rubrics, validation, scoring, examples]

# Dependency graph
requires:
  - phase: 02-01
    provides: Objective rubrics for Gates 1-4 with three-section format (Objective Rubric, Scoring, Edge Cases)
provides:
  - Objective rubrics for Gates 5-8 with measurable thresholds
  - Multi-signal validation for Gate 5 (Wallet Test) replacing subjective "honest yes"
  - Explicit "real names" validation criteria for Gate 5
  - FAIL examples for Gates 5-8 showing common failure patterns
affects: [02-03, 02-04, future gate content development, audit execution]

# Tech tracking
tech-stack:
  added: []
  patterns:
    - "Multi-signal validation pattern for subjective tests (4 signals with 3+ threshold)"
    - "Counterfactual test for borderline cases (specific substitution question)"
    - "Numeric friction scoring (Low=1, Medium=2, High=3) with sum-based thresholds"
    - "Net consequence scoring (positive +1, negative -1) for second-order effects"

key-files:
  created: []
  modified:
    - skill/config/gates-full.md
    - skill/guides/GATE-EXAMPLES.md

key-decisions:
  - "Gate 5 multi-signal validation: 4 signals each for self-audit and third-party, 3+ required for pass"
  - "Gate 5 counterfactual test for borderline: 'Which $20/month subscription would you cancel?' If specific answer → PASS"
  - "Gate 5 'real names' validation: Must be person you could message today who you've confirmed has problem"
  - "Gate 6 JTBD checklist: All 4 items (specific situation, action verb, tangible outcome, single job) must be Yes for pass"
  - "Gate 7 friction scoring: 6-18 point scale, ≤9 = pass, ≥13 = fail, 10-12 = borderline with value justification check"
  - "Gate 8 consequence scoring: Net ≥1 = pass, ≤-1 = fail, 0 = borderline with impact weighting check"

patterns-established:
  - "Signal-based validation: Convert subjective judgment into countable signals with clear thresholds"
  - "Borderline decision tests: When score is borderline, apply specific tiebreaker question (not auditor discretion)"
  - "Dual audit protocols: Different signals for self-audit (creator) vs third-party audit (external)"

# Metrics
duration: 12min
completed: 2026-01-22
---

# Phase 02 Plan 02: Gates 5-8 Objective Rubrics Summary

**Gate 5 'honest yes' replaced with 4-signal validation, Gates 6-8 now have measurable thresholds (JTBD checklist, friction scores, consequence net), 10 new FAIL examples added**

## Performance

- **Duration:** 12 min
- **Started:** 2026-01-22T18:41:42Z
- **Completed:** 2026-01-22T18:53:26Z
- **Tasks:** 2
- **Files modified:** 2

## Accomplishments
- Replaced Gate 5 subjective "honest yes" with multi-signal validation (4 signals for self-audit, 4 for third-party)
- Added objective rubrics to Gates 5-8 following three-section format (Objective Rubric, Scoring, Edge Cases)
- Created 10 FAIL examples for Gates 5-8 showing common failure patterns tied to rubric criteria
- Established explicit "real names" validation (can message today + confirmed problem) vs archetypes/personas

## Task Commits

Each task was committed atomically:

1. **Task 1: Add objective rubrics to Gates 5-8** - `9f263a5` (feat)
   - Gate 5 multi-signal validation
   - Gate 6 JTBD checklist
   - Gate 7 friction scoring table
   - Gate 8 consequence net scoring
   - Followed by `0bd2405` (fix) - removed "honest yes" from pass criteria

2. **Task 2: Add FAIL examples for Gates 5-8** - `3924595` (feat)
   - 3 Gate 5 examples (archetypes, reframe failure, no market signals)
   - 2 Gate 6 examples (vague situation, multiple jobs)
   - 2 Gate 7 examples (high installation friction, repeated use friction)
   - 2 Gate 8 examples (creates worse lock-in, one-time use)

**Total commits:** 3 (2 task commits + 1 fix commit)

## Files Created/Modified
- `skill/config/gates-full.md` - Added Objective Rubric, Scoring, and Edge Cases sections to Gates 5-8 (127 insertions)
- `skill/guides/GATE-EXAMPLES.md` - Added 10 FAIL examples for Gates 5-8 with detailed scoring analysis (306 insertions)

## Decisions Made

**Gate 5 Multi-Signal Validation:**
- Self-audit signals: Paid for similar (A), 3 real names (B), 5+ conversations (C), comparable tools charge $10-100 (D)
- Third-party signals: Stars >100 or features >10 (A), paid tools exist (B), costs time/money (C), 3+ production users (D)
- Threshold: 3+ signals = PASS, 2 signals triggers counterfactual test
- Counterfactual: "Which $20/month subscription would you cancel?" Specific answer = PASS, hesitation = FAIL
- Rationale: Converts subjective judgment into countable evidence, reduces auditor interpretation variance

**Gate 5 Real Names Validation:**
- PASS: Person you could message today who you've confirmed has this problem
- FAIL: Hypothetical persona ("a PM at a startup"), celebrity you've never talked to, person you assume has problem
- Rationale: "Real names" must mean validated real people, not just non-generic labels

**Gate 6 JTBD Checklist:**
- All 4 items must be Yes: Specific situation (observable), action-oriented motivation (concrete verb), tangible outcome (measurable), single job (not multiple)
- Borderline: 3 of 4 → If situation vagueness, try adding context, still can't specify → FAIL
- Rationale: JTBD format has 3 components but all 3 must meet quality bars (specificity, concreteness, tangibility)

**Gate 7 Friction Scoring:**
- Low (1): ≤2 min, no surprises | Medium (2): 2-10 min OR minor confusion | High (3): >10 min OR blocker
- Sum 6 steps: ≤9 = PASS, ≥13 = FAIL, 10-12 = borderline → "Is highest friction justified by value?"
- Rationale: Converts time/complexity into numeric scale, enables objective comparison across projects

**Gate 8 Consequence Scoring:**
- Positive: +1 per genuine second-order effect (viral, ecosystem, retention)
- Negative: -1 per genuine second-order effect (lock-in, maintenance, dependency)
- Net ≥1 = PASS, ≤-1 = FAIL, 0 = borderline → "Are positives high-impact?"
- Rationale: Forces explicit enumeration of consequences and net calculation, prevents vague "pros outweigh cons"

## Deviations from Plan

None - plan executed exactly as written.

All rubrics and examples added as specified. The "honest yes" removal required one additional fix commit (0bd2405) after initial Task 1 commit, but this was part of the planned objective (replace subjective language).

## Issues Encountered

None. Plan was clear, files had expected structure from 02-01, and examples followed established format.

## Next Phase Readiness

**Gates 5-8 rubrics complete.** Ready for:
- 02-03: Objective rubrics for Gates 9-11 (final 3 gates)
- 02-04: EDGE-CASES.md population with gray area protocols

**Patterns established for Gates 9-11:**
- Three-section format (Objective Rubric, Scoring, Edge Cases)
- Quantifiable thresholds (not "auditor discretion")
- Borderline decision tests (specific tiebreaker questions)
- FAIL examples tied to rubric criteria with contrast-with-PASS comparisons

**No blockers.** Gates 9-11 can follow same structure.

---
*Phase: 02-gate-clarity*
*Completed: 2026-01-22*
