---
phase: 04
plan: 01
subsystem: audit-configuration
tags: [quick-mode, triage, gates, config]
requires: [02-gate-clarity]
provides: [gates-quick.md, 5-gate-subset, triage-algorithm]
affects: [04-02-quick-report-template]
tech-stack:
  added: []
  patterns: [reference-based-config, threshold-triage]
decisions:
  - "5-gate subset (1, 3, 4, 5, 9) selected as critical path"
  - "Reference-based config avoids duplication with gates-full.md"
  - "4-level triage (PROCEED/PROMISING/BORDERLINE/STOP)"
  - "Time target: 5-10 minutes total (1-2 min per gate)"
key-files:
  created: [skill/config/gates-quick.md]
  modified: []
metrics:
  duration: 3min
  completed: 2026-01-22
---

# Phase 04 Plan 01: Quick Mode Gate Configuration Summary

**One-liner:** Reference-based 5-gate quick audit configuration (Gates 1, 3, 4, 5, 9) with threshold-based triage algorithm for rapid project viability evaluation.

## What Was Built

Created `skill/config/gates-quick.md`, the configuration file for Quick Audit Mode. This file defines the 5-gate subset used for rapid triage (5-10 minute evaluation) and provides quick evaluation guidance for each gate.

**Key architectural decision:** Reference-based configuration pattern. Rather than duplicating gate specifications from gates-full.md, each gate section includes a reference link (`config/gates-full.md#gate-N`) and focuses only on quick mode-specific guidance. This keeps the file concise (182 lines vs. potentially 400+ if duplicated) and ensures single source of truth for gate specifications.

**Gate selection rationale:** Gates 1, 3, 4, 5, and 9 form the critical path through utility validation:
- Gate 1: Why does this exist? (bedrock need)
- Gate 3: Can users get value in 10 minutes? (accessibility)
- Gate 4: Can you explain it in one sentence? (explainability)
- Gate 5: Would anyone pay for this? (market validation)
- Gate 9: Is this the simplest solution? (necessity)

A project failing any of these gates has fundamental viability issues that make full 11-gate evaluation unnecessary.

**Triage algorithm:** Threshold-based decision algorithm converts 5-gate score into actionable recommendation:
- 5/5 = PROCEED to full audit
- 4/5 = PROMISING (fix 1 issue first)
- 3/5 = BORDERLINE (fix 2 issues before full audit)
- 0-2/5 = STOP (fundamental issues)

Each threshold includes specific next steps text to guide the user.

## Files Changed

**Created:**
- `skill/config/gates-quick.md` (182 lines)
  - Quick Mode Overview with 5-gate table
  - Quick Mode Mindset callout (gut check, not deep analysis)
  - Usage guidance (when to use quick vs. full mode)
  - Triage Decision Algorithm with 4 verdict levels
  - 5 gate sections with quick evaluation guidance
  - Template Variable Summary
  - See Also references

## Technical Implementation

**Reference-based configuration pattern:**

Each gate section follows this structure:
```markdown
## Gate N: [Name]

**Full specification:** `config/gates-full.md#gate-N`

**Quick evaluation:** [2-4 bullets on what to focus on]

**Pass in quick mode:** [Brief criteria]

**Fail in quick mode:** [Brief criteria]

**Template variables:** Same as full mode (list)
```

This pattern:
- Eliminates duplication (10 references to gates-full.md throughout file)
- Focuses on quick mode differentiators (time limits, gut checks)
- Maintains single source of truth for objective rubrics
- Keeps file maintainable (changes to gate specs happen in one place)

**Threshold-based triage:**

Decision algorithm uses simple score-to-verdict mapping rather than complex weighting or ML:
- Transparent (anyone can understand the thresholds)
- Explainable (clear rationale for each recommendation)
- Maintainable (thresholds can be adjusted based on empirical validation)

**Usage guidance:**

File includes explicit guidance on when to use quick vs. full mode:
- Quick mode: first-time audit, portfolio triage, pre-audit check, time-constrained
- Full mode: after quick passed, significant investment decision, comprehensive evaluation needed

This prevents misuse (quick mode becoming default when full rigor needed) and sets expectations.

## Decisions Made

**Decision 1: 5-gate subset (Gates 1, 3, 4, 5, 9)**

**Context:** Quick mode needs to filter ~80% of non-viable projects in ~20% of time (5-10 min vs. 45-90 min full audit). Gate selection determines filtering effectiveness.

**Options considered:**
- First 5 gates sequentially (1-5): Includes Gate 2 (Inversion Test) which is time-consuming
- Easy/fast 5 gates: Optimizes for speed over signal strength
- Critical path 5 gates (1, 3, 4, 5, 9): Optimizes for viability signal

**Decision:** Critical path gates (1, 3, 4, 5, 9)

**Rationale:** These gates test different fundamental dimensions of viability. A project can pass Gates 2, 6, 7, 8 and still be viable with improvements, but failing any of the critical path gates indicates core structural issues. Research showed this aligns with smoke testing patterns (test critical paths, not every feature).

**Impact:** Quick mode filters for fundamental viability, not comprehensive quality. Projects passing quick mode still need full audit before launch.

---

**Decision 2: Reference-based config (no duplication)**

**Context:** gates-quick.md needs gate specifications but gates-full.md already defines them. Duplicate or reference?

**Options considered:**
- Full duplication: Copy all gate sections into gates-quick.md
- Partial duplication: Copy quick-relevant parts, reference rest
- Full reference: Link to gates-full.md, only add quick guidance

**Decision:** Full reference (link to gates-full.md with quick guidance)

**Rationale:**
- Maintains single source of truth (changes to rubrics happen once)
- Keeps gates-quick.md concise (182 lines vs. 400+ if duplicated)
- Reduces maintenance burden (2 commits to update vs. N commits)
- Aligns with BASE-AGENT.md inheritance pattern (57% duplication reduction)

**Impact:** gates-quick.md is a pointer file, not a standalone spec. Auditors must read gates-full.md for complete rubrics. This is acceptable tradeoff for maintainability.

---

**Decision 3: 4-level threshold triage (5/5, 4/5, 3/5, 0-2/5)**

**Context:** Need to convert raw score (0-5) into actionable recommendation for user.

**Options considered:**
- Binary (pass/fail): Too coarse, loses signal
- 3-level (proceed/maybe/stop): "Maybe" is vague
- 4-level (proceed/promising/borderline/stop): Specific actions per level
- 6-level (one per score): Over-granular for 5-gate sample size

**Decision:** 4-level with specific next steps per threshold

**Rationale:**
- 5/5 = clear proceed signal
- 4/5 = strong but 1 fixable gap (worth addressing before full audit investment)
- 3/5 = core viability uncertain (fix 2 gaps before full audit)
- 0-2/5 = multiple critical failures (stop, fundamental rethink needed)

Each level maps to specific user action, preventing "what do I do now?" confusion.

**Impact:** Triage algorithm provides clear decision guidance. User knows exactly what to do based on score. This aligns with healthcare ESI 5-level triage (clear protocol per severity level).

---

**Decision 4: Time target 5-10 minutes (1-2 min per gate)**

**Context:** Quick mode purpose is rapid triage. Too slow = defeats purpose. Too fast = loses accuracy.

**Options considered:**
- 3-5 min (30 sec per gate): Too rushed, likely inaccurate
- 5-10 min (1-2 min per gate): Gut check with minimal research
- 10-15 min (2-3 min per gate): Approaching partial full audit

**Decision:** 5-10 minutes total, 1-2 minutes per gate

**Rationale:**
- 1-2 min per gate allows reading README, mental simulation, gut check
- Prevents deep research (if researching, overthinking)
- 5-10 min total is realistic for focused evaluation without interruption
- Research showed smoke tests target 10-20% of full test time (5-10 min is 11% of 45 min)

**Impact:** Added "Quick Mode Mindset" callout prominently in file: "If you're researching deeply, you're overthinking. Quick mode = gut check, not deep analysis." This sets expectations and prevents scope creep during execution.

## Next Phase Readiness

**Blocks Phase 04-02:** No. This plan delivers the quick mode gate configuration. Plan 04-02 can proceed to create the quick report template.

**Key inputs for 04-02:**
- Template variables (listed in Template Variable Summary section)
- Triage recommendation format (PROCEED/PROMISING/BORDERLINE/STOP)
- Next steps text structure (exists in Triage Decision Algorithm section)

**Open questions for future phases:**
- Empirical validation of gate selection (Phase 7 concern: do 5 gates actually catch 80% of non-viable projects?)
- Time target validation (Phase 7: does quick mode actually take 5-10 min in practice?)
- Threshold calibration (Phase 7: should 3/5 be BORDERLINE or PROMISING? Requires real audit data)

**Technical debt:** None. Reference-based config is clean pattern.

**Documentation status:** Complete. gates-quick.md is self-documenting with rationale, usage guidance, and references.

## Deviations from Plan

None - plan executed exactly as written.

## Lessons Learned

**What worked well:**
- Reference-based pattern kept file concise and maintainable
- Quick Mode Mindset callout sets clear expectations
- Usage guidance (when to use quick vs. full) prevents misuse
- 4-level triage with specific next steps provides clear action

**What to apply elsewhere:**
- Reference pattern applicable to any subset/superset config relationship
- "Mindset" callouts useful for setting mode expectations (could apply to full mode too)
- Threshold triage with specific actions better than vague "consider X" recommendations

**What to watch:**
- Need empirical validation (Phase 7) to confirm gate selection catches 80% of non-viable
- Time target needs validation - will auditors actually complete in 5-10 min?
- Triage thresholds may need calibration based on real audit outcomes

## Metrics

**Execution:**
- Tasks: 2/2 complete
- Commits: 2 (870b731, a7ede6f)
- Duration: 3 minutes
- Files created: 1
- Lines added: 182

**Plan accuracy:**
- Estimated scope: Create gates-quick.md with 5-gate subset and triage algorithm
- Actual scope: Exactly as estimated
- Deviations: None

## Links

**Plan:** `.planning/phases/04-quick-audit-mode/04-01-PLAN.md`

**Research:** `.planning/phases/04-quick-audit-mode/04-RESEARCH.md`

**Artifacts:**
- `skill/config/gates-quick.md` (5-gate quick mode configuration)

**Related:**
- `skill/config/gates-full.md` (complete 11-gate specifications, referenced 10 times)
- Phase 04-02 (next: quick report template)
- Phase 02 (gate clarity - established objective rubrics referenced by quick mode)
