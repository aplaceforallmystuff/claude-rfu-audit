# RFU Audit: Quick Mode Gate Definitions

**This file defines the 5-gate quick audit subset for rapid triage.**

For complete gate specifications, objective rubrics, scoring rules, and edge cases, see `config/gates-full.md`.

---

## Quick Mode Overview

Quick mode tests the critical path through utility validation. These 5 gates catch ~80% of non-viable projects in ~20% of audit time.

| Gate | Core Question | Stop Condition |
|------|---------------|----------------|
| 1 | Why does this exist? | No bedrock human need |
| 3 | Can users get value in 10 minutes? | Inaccessible or unclear |
| 4 | Can you explain it in one sentence? | Unexplainable = unusable |
| 5 | Would anyone pay for this? | No market validation |
| 9 | Is this the simplest solution? | Simpler alternative exists |

**Time target:** 5-10 minutes total (1-2 minutes per gate)

**Gate selection rationale:** These gates form the critical path through utility validation. They test fundamental viability: Why it exists (Gate 1), whether users can access value (Gate 3), whether it's explainable (Gate 4), whether anyone would pay (Gate 5), and whether simpler alternatives exist (Gate 9). A project failing any of these gates has core viability issues that make further evaluation unnecessary.

> **Quick Mode Mindset**
>
> Target: 1-2 minutes per gate. If you're researching deeply, you're overthinking.
> Quick mode = gut check, not deep analysis. Save the rigor for full mode.

**Use quick mode when:**
- First-time audit of unfamiliar project
- Portfolio triage (evaluating multiple projects)
- Pre-audit sanity check before major investment
- Time-constrained (need answer in <10 minutes)

**Use full mode when:**
- Quick mode passed (5/5 or 4/5)
- Making go/no-go decision on significant investment
- Need complete documentation of all gates
- Project is already launched (comprehensive evaluation)

---

## Triage Decision Algorithm

After completing the 5-gate quick audit, calculate the score and apply the threshold-based recommendation:

| Score | Verdict | Recommendation |
|-------|---------|----------------|
| 5/5 | **PROCEED** | Run full 11-gate audit |
| 4/5 | **PROMISING** | Fix 1 issue, then consider full audit |
| 3/5 | **BORDERLINE** | Fix 2 issues before full audit |
| 0-2/5 | **STOP** | Fundamental issues; fix before audit |

**Next steps by threshold:**

**5/5 - PROCEED:** Project shows strong utility signals across all critical dimensions. The project has a clear bedrock need, is accessible to users, can be explained simply, shows market validation, and represents a necessary solution. Run the full 11-gate audit to validate market fit, friction levels, second-order effects, and Pareto principle application.

**4/5 - PROMISING:** Project has a strong foundation with one fixable gap. Address the single failed gate, then reconsider whether a full audit is warranted. The core concept is viable, but one critical dimension needs work before investing in comprehensive evaluation.

**3/5 - BORDERLINE:** Project shows potential but has multiple core gaps. Fix the 2 failed gates before proceeding to full audit. With 3 of 5 critical gates passing, the concept has merit, but the gaps are significant enough that full audit would likely reveal additional issues. Shore up the foundation first.

**0-2/5 - STOP:** Project has fundamental utility issues. Multiple critical gates failed, indicating deep structural problems. Revisit the failed gates and potentially reconsider the project's core premise before proceeding with any audit. Full audit would be premature at this stage.

---

## Gate 1: The 5 Whys of Utility

**Full specification:** `config/gates-full.md#gate-1`

**Quick evaluation:**
- Execute the 5 Whys chain starting with "Why does this exist?"
- Stop immediately if chain stops at technical reason (no human need visible)
- Stop if you reach "because I wanted to learn" without drilling to who benefits from that learning
- Stop if you cannot proceed past 3 iterations
- Look for path to bedrock needs: autonomy, time, money, status, connection, safety

**Pass in quick mode:** Chain reaches one of the six bedrock needs within 5 iterations, with each why building logically on the previous answer.

**Fail in quick mode:** Chain stops at technical implementation, loops back on itself, or cannot proceed past 3 iterations.

**Template variables:** Same as full mode (gate1_status, why1-5, bedrock_need, gate1_verdict)

---

## Gate 3: The 10-Minute Test

**Full specification:** `config/gates-full.md#gate-3`

**Quick evaluation:**
- Mental simulation of new user journey from README discovery to first value
- Estimate time to understand from README (how long to grasp what it does)
- Estimate time to install/setup (including prerequisites)
- Estimate time to first "aha moment" (experiencing the value)
- Sum the three estimates to get total time
- Note any prerequisites that are uncommon (non-standard tools, domain knowledge)

**Pass in quick mode:** Total time ≤10 minutes with clear aha moment reachable. Prerequisites are standard for the domain (e.g., Node.js for a JavaScript tool is fine, obscure database system is not).

**Fail in quick mode:** Total time >10 minutes OR no clear aha moment reachable in any timeframe OR requires uncommon prerequisites that add friction.

**Template variables:** Same as full mode (gate3_status, time_understand, time_setup, time_value, time_total, notes_*, gate3_verdict)

---

## Gate 4: The Bartender Test

**Full specification:** `config/gates-full.md#gate-4`

**Quick evaluation:**
- Draft a one-sentence explanation of what the project does
- Check against four criteria:
  - Zero jargon (would a bartender understand every word?)
  - Describes value, not implementation (what it does for users, not how it works)
  - Single sentence (one independent clause, no run-ons)
  - No follow-up questions needed (sentence is complete and clear)

**Pass in quick mode:** All four checks pass. The sentence uses plain English, focuses on user value, fits in one sentence, and requires no clarification.

**Fail in quick mode:** Any of the four checks fail. Most common: jargon present (API, CLI, schema, etc.) or sentence describes technology instead of value.

**Template variables:** Same as full mode (gate4_status, bartender_sentence, bartender_has_jargon, bartender_describes_value, bartender_single_sentence, bartender_needs_followup, gate4_verdict)

---

## Gate 5: The Wallet Test

**Full specification:** `config/gates-full.md#gate-5`

**Quick evaluation:**
- Self-audit path: Answer "Would you pay $20/month for this?" and count validation signals (see gates-full.md for 4-signal list)
- Third-party path: Count market validation signals (see gates-full.md for 4-signal list)
- For borderline cases: Apply counterfactual test ("Which subscription would you cancel?")
- If naming people, ensure they are real individuals you could message today, not archetypes

**Pass in quick mode (self-audit):** 3 or more of the 4 validation signals are present. Most importantly, the "honest yes" to paying $20/month yourself.

**Pass in quick mode (third-party):** 3 or more of the 4 market signals are present (paying users, waitlist, testimonials, market research).

**Fail in quick mode:** 0-2 signals OR only hypothetical personas ("developers would pay") instead of real named individuals.

**Template variables:** Same as full mode (gate5_status, wallet_self, wallet_signals, wallet_score, wallet_verdict)

---

## Gate 9: Occam's Razor

**Full specification:** `config/gates-full.md#gate-9`

**Quick evaluation:**
- Answer three gut-check questions:
  - Could a bash script do 80% of this? (Is the solution overcomplicated?)
  - Does an existing tool solve this acceptably? (Why reinvent the wheel?)
  - Are you building a framework when a function would work? (Is there unnecessary abstraction?)
- If any answer is "Yes," consider whether there's a strong justification for the added complexity

**Pass in quick mode:** All three questions answered "No" without hesitation. Project represents the simplest viable solution to the problem, no existing tools solve it acceptably, and the level of abstraction matches the need.

**Fail in quick mode:** Any question answered "Yes" without a compelling justification that outweighs the complexity cost.

**Template variables:** Same as full mode (gate9_status, occam_simpler_alternative, occam_existing_tool, occam_over_abstraction, occam_verdict)

---

## Template Variable Summary

Quick mode uses the same template variables as full mode for its 5 gates. Each gate section above notes "Same as full mode" for the variables. See `config/gates-full.md` for complete variable documentation for Gates 1, 3, 4, 5, and 9.

**Quick mode specific variables:**
- `quick_score` - Score out of 5 (e.g., "4/5")
- `triage_recommendation` - One of: PROCEED | PROMISING | BORDERLINE | STOP
- `recommendation_text` - Detailed next steps text based on threshold (see Triage Decision Algorithm section)
- `next_steps` - Bulleted action items based on which gates failed

---

## See Also

- `config/gates-full.md` - Complete specifications for all 11 gates, including objective rubrics, scoring rules, and edge cases
- `templates/audit-report-quick.md` - Quick mode report template
- `guides/GATE-EXAMPLES.md` - Pass/fail examples for all gates
- `guides/EDGE-CASES.md` - Edge case resolution rules
