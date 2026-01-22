---
phase: 02-gate-clarity
plan: 01
status: complete
completed: 2026-01-22

# Dependencies
requires:
  - 01-01 # Modular architecture with gates-full.md
  - 01-02 # SKILL.md orchestrator with gate references
provides:
  - Objective rubrics for Gates 1-4
  - FAIL examples defining quality floor
  - Measurable thresholds for consistent scoring
affects:
  - 02-02 # Will build on these rubrics with EDGE-CASES.md
  - 03-* # Report generation will use these scoring protocols

# Technical
subsystem: audit-criteria
tech-stack:
  added: []
  patterns:
    - Binary checklists (Gate 4)
    - Threshold-based scoring (Gates 2, 3)
    - Measurement protocols (all gates)
key-files:
  created: []
  modified:
    - skill/config/gates-full.md
    - skill/guides/GATE-EXAMPLES.md

# Decisions
decisions:
  - id: rubric-structure
    what: Three-section rubric format (Objective Rubric, Scoring, Edge Cases)
    why: Separates measurement protocol from verdict logic from gray areas
    impact: Consistent structure for remaining gates (5-11)

  - id: bedrock-needs-list
    what: Six fundamental needs (autonomy/time/money/status/connection/safety)
    why: Gate 1 needs explicit endpoint to prevent subjective interpretation
    impact: Auditors know when to stop drilling

  - id: switching-cost-thresholds
    what: 5-minute rule for convenience vs. value (Gate 2)
    why: Quantifiable boundary prevents "minor inconvenience" arguments
    impact: Clear pass/fail line for inversion test

  - id: pre-req-timing-rules
    what: Include project-specific pre-reqs, exclude domain-standard tools (Gate 3)
    why: Fair comparison across different tech stacks
    impact: Python projects not penalized for assuming Python installed

  - id: jargon-reference-list
    what: Explicit jargon vs. acceptable terms list (Gate 4)
    why: Prevents disagreement on what counts as "technical"
    impact: API/CLI/schema = jargon; email/website/file = acceptable

  - id: fail-example-priority
    what: Started with Gates 1-4, leaving 5-11 as placeholders
    why: Wave 1 focus on foundational gates; later waves add advanced gates
    impact: Enables parallel development (can audit with Gates 1-4 while others develop 5-11)

# Metrics
duration: 9min
tasks_completed: 2
commits: 2
files_modified: 2
---

# Phase 2 Plan 1: Objective Rubrics and FAIL Examples Summary

**One-liner:** Replaced subjective gate criteria with measurable thresholds and documented 9 concrete failure patterns for Gates 1-4.

## What Was Built

### Objective Rubrics (Gates 1-4)

Added three new sections to each of Gates 1-4 in `skill/config/gates-full.md`:

1. **Objective Rubric** - Measurement protocols with numeric/binary thresholds
2. **Scoring** - Explicit pass/fail/borderline conditions
3. **Edge Cases** - Gray area pointers (detailed in future EDGE-CASES.md)

**Gate 1 (5 Whys):**
- Bedrock needs list: autonomy, time, money, status, connection, safety
- Pass threshold: Reaches bedrock within 5 iterations
- Fail condition: Stops at technical reason or cannot proceed past 3 iterations
- Disqualifier: "Because I wanted to learn" without deeper exploration

**Gate 2 (Inversion):**
- Switching cost measurement: ≤5 min = FAIL, >4 hours = PASS, 1-4 hours = BORDERLINE
- Quantitative test for borderline: >20% time/cost savings vs alternative
- Pre-requisite rule: Don't count domain-standard tools in switching cost

**Gate 3 (10-Minute):**
- Time threshold: ≤10 min = PASS, >15 min = FAIL, 10-15 min = BORDERLINE
- Aha moment requirement: Must demonstrate value, not just "it installed"
- Pre-req timing rules: Include project-specific (Docker, Redis), exclude domain-standard (Node, Python)

**Gate 4 (Bartender):**
- 4-point binary checklist: zero jargon, value focus, single sentence, no questions
- Jargon reference list: API/CLI/schema = jargon; email/website/file = acceptable
- Scoring: Must pass ALL 4 checks (3/4 still fails)

### FAIL Examples (Gates 1-4)

Created 9 detailed FAIL examples in `skill/guides/GATE-EXAMPLES.md`:

**Gate 1:**
- API wrapper stopping at technical reasons
- Learning project without utility for others

**Gate 2:**
- Trivial switching cost (jq alternative example)
- Minor inconvenience only (tab management example)

**Gate 3:**
- Long setup before first value (Docker/Redis/API key example)
- No aha moment despite quick installation (library import example)

**Gate 4:**
- Pure technical jargon (distributed caching example)
- Implementation over value (CLI deployment example)
- Multiple sentences (presentation tool example)

Each FAIL example includes:
- Project type and scenario
- Attempted answer/evidence
- Specific rubric violations (numbered list)
- Contrast with what passing would look like

## Key Improvements

### Before (Subjective)
- "Clear, specific negative consequences" (Gate 2)
- "Immediate comprehension" (Gate 4)
- "Honest yes" (Gate 5)

### After (Objective)
- "≤5 minute switching cost = FAIL, >4 hours = PASS" (Gate 2)
- "4-point binary checklist: all must be YES" (Gate 4)
- Specific measurement protocols for each gate

### Impact on Consistency

**Scenario:** Two auditors reviewing a CLI tool that saves typing 2 flags vs using jq.

**Before:**
- Auditor A: "Minor inconvenience" → subjective interpretation → might pass
- Auditor B: "Not valuable enough" → subjective interpretation → might fail
- Result: Inconsistent scoring

**After:**
- Both auditors: Apply 5-minute rule → switching cost is <30 seconds → FAIL
- Result: Identical verdict

## Files Changed

```
skill/config/gates-full.md (+209 lines)
├── Gate 1: Added Objective Rubric (bedrock needs, thresholds)
├── Gate 2: Added Objective Rubric (switching cost measurement)
├── Gate 3: Added Objective Rubric (timing protocol, pre-req rules)
└── Gate 4: Added Objective Rubric (binary checklist, jargon list)

skill/guides/GATE-EXAMPLES.md (+250 lines, -19 lines)
├── Removed "Status: Placeholder" marker
├── Gate 1: 2 FAIL examples + 1 PASS contrast
├── Gate 2: 2 FAIL examples + 1 PASS contrast
├── Gate 3: 2 FAIL examples + 1 PASS contrast
└── Gate 4: 3 FAIL examples + 1 PASS contrast
```

## Commits

| Task | Commit | Files | Description |
|------|--------|-------|-------------|
| 1 | a1a3dbc | gates-full.md | Added objective rubrics to Gates 1-4 |
| 2 | 705e42c | GATE-EXAMPLES.md | Added FAIL examples for Gates 1-4 |

## Verification Results

✅ Gates 1-4 each have "Objective Rubric", "Scoring", "Edge Cases" sections (12 sections total)
✅ GATE-EXAMPLES.md has 9 FAIL examples (2+ per gate for Gates 1-4)
✅ Rubric thresholds are numeric or binary (no subjective adjectives)
✅ FAIL examples reference specific rubric criteria
✅ Two auditors can now reach identical verdicts using these rubrics

## Deviations from Plan

None - plan executed exactly as written.

## Next Phase Readiness

**Ready for Phase 2 Plan 2 (Edge Cases Guide):**
- Edge Cases sections in Gates 1-4 have pointers to future EDGE-CASES.md
- Common gray areas identified: "learning motivation", "minor inconvenience boundary", "pre-requisite timing", "domain-specific terms"
- Can now build comprehensive edge case guide with decision trees

**Ready for Phase 3 (Report Generation):**
- Gates 1-4 have explicit scoring protocols that can be programmatically applied
- Template variables already defined (from Phase 1)
- FAIL examples provide calibration for report quality ("verdict" variables should match this level of specificity)

## Blockers/Concerns

None identified.

## Tags

`audit-criteria` `rubrics` `scoring` `gates` `consistency` `measurement`
