# Domain Pitfalls: Audit/Validation Frameworks

**Domain:** Product validation and audit systems
**Researched:** 2026-01-22
**Confidence:** MEDIUM (based on project CONCERNS.md analysis + training data on audit systems)

## Critical Pitfalls

Mistakes that cause rewrites, abandonment, or make audits worthless.

### Pitfall 1: Vague Pass/Fail Criteria (The "I Know It When I See It" Trap)

**What goes wrong:** Gates defined with subjective language ("reasonable", "sufficient", "adequate") lead to wildly inconsistent scoring. Different auditors score the same project differently. Users can't tell if they passed or failed.

**Why it happens:**
- Framework designers assume shared understanding that doesn't exist
- Criteria feel obvious to the creator but ambiguous to users
- Edge cases aren't documented during design phase
- Fear of being "too prescriptive" leads to vagueness

**Consequences:**
- Inter-rater reliability drops below 0.5 (random chance territory)
- Users game the system by reframing answers until they pass
- Audit results become meaningless ("everyone passes if you ask nicely")
- Teams stop using the framework because results aren't trusted

**Real examples from RFU:**
- Gate 5 (Wallet): "3 specific people" — how specific? LinkedIn profiles? Friends? Imaginary?
- Gate 10 (Pareto): "80/20 value distribution" — what if all features are equally important?
- Gate 11 (Regret): Pure psychology; 25-year-old and 50-year-old will score differently

**Prevention:**
- Write concrete examples for PASS, FAIL, and BORDERLINE cases
- Test criteria with 3+ independent auditors on same project; if scores diverge >20%, criteria are vague
- Replace qualitative language with quantitative thresholds where possible
  - Bad: "Would people pay for this?"
  - Good: "Name 3 specific individuals (first name + context) who would pay $20/month"
- Document edge cases explicitly: "If only 2 people would pay, FAIL. If 3+ people, PASS."
- Add calibration examples: side-by-side comparisons showing why Project A passes and Project B fails

**Detection:**
- Multiple auditors score the same project ±2 points differently
- Users ask "what counts as X?" repeatedly
- Audit reports contain hedging language: "arguably", "possibly", "depends on interpretation"

**Which phase addresses:** Phase 2 (Gate Clarity Enhancement)

---

### Pitfall 2: Happy-Path-Only Documentation (No Failure Examples)

**What goes wrong:** Framework shows what PASS looks like but never shows FAIL examples. Users overestimate their projects because they don't know what failure looks like. Scores inflate over time (grade inflation).

**Why it happens:**
- Creators document their own successful projects as examples
- Showing failures feels negative/demotivating
- Easier to write one good example than three varied examples
- Assumption that "if you don't match the PASS example, you failed" is obvious (it's not)

**Consequences:**
- Users rationalize borderline projects into passing scores
- Framework loses credibility when users discover they shipped "validated" projects that failed in market
- Self-selection bias: only confident projects get audited (timid projects avoid it)
- No learning from failure patterns across projects

**Real examples from RFU:**
- README shows hypothetical 7/11 score but no detail on what the 4 failures were
- No examples of projects that failed Gate 1 (5 Whys) with explanation
- No "this project got 3/11 and here's why it was right to kill it" case studies

**Prevention:**
- Document 2+ failure examples per gate showing what clear FAIL looks like
- Create comparison tables: "Project A passed Gate 3 because X; Project B failed because Y"
- Show borderline cases with reasoning: "This barely passed; here's why"
- Maintain anonymized audit archive with real failed projects (with permission)
- Add "common failure patterns" section: "80% of Gate 5 failures are because..."

**Detection:**
- Average audit scores drift upward over time (8.5 → 9.2 → 9.8)
- Users rarely score themselves below 7/11
- Questions like "I sort of meet this, does that count?" increase
- Projects that "passed" still fail in market

**Which phase addresses:** Phase 2 (Gate Clarity Enhancement)

---

### Pitfall 3: No Gray Area Handling (Binary Thinking for Spectrum Problems)

**What goes wrong:** Real projects exist on a spectrum, but framework forces binary PASS/FAIL. This creates three problems:
1. Auditors inflate borderline cases to PASS (optimism bias)
2. Legitimate "CONDITIONAL PASS" cases get misclassified
3. Framework can't distinguish between "strong pass" and "barely passed"

**Why it happens:**
- Simple scoring (1 or 0) is easier to implement than nuanced scoring
- Fear that "conditional pass" becomes a loophole for weak projects
- Difficulty defining what constitutes "conditional" vs "fail"

**Consequences:**
- Projects with 5 strong passes and 4 weak passes score same as projects with 9 strong passes
- No guidance on prioritizing fixes ("fix your 2 weak passes first")
- Borderline projects ship prematurely because they technically "passed"
- Framework can't differentiate between "barely viable" and "obviously strong"

**Real examples from RFU:**
- Project barely passes Gate 5 (only 3 people would pay, barely) vs strongly passes (50 people would pay immediately)
- Both score 1/1 for Gate 5, but risk profiles are completely different

**Prevention:**
- Add confidence levels: STRONG PASS (2 points), PASS (1 point), CONDITIONAL (0.5 points), FAIL (0)
- Document conditions for CONDITIONAL: "Gate 5 conditional if 2-3 people would pay; investigate further"
- Show score distributions: 11/11 full points vs 8/11 with 3 conditionals = different risk
- Require auditor commentary on strength: "Weak pass; revisit after MVP"
- Create "hesitation flags": if auditor hesitates, mark as conditional and explain why

**Detection:**
- Auditors spend >5 minutes debating a single gate score
- Phrases like "technically yes but..." appear in audit notes
- Projects score 9-10 but still feel risky
- Two auditors disagree not on fail/pass but on strength

**Which phase addresses:** Phase 2 (Gate Clarity Enhancement)

---

### Pitfall 4: Audit Fatigue (Death by 1000 Questions)

**What goes wrong:** Comprehensive audits (11 gates, 50+ questions) are thorough but exhausting. Users start rushing through later gates, give low-effort answers, or abandon mid-audit. Quality degrades as fatigue sets in.

**Why it happens:**
- Desire for comprehensiveness leads to long checklists
- Each gate added independently without considering cumulative burden
- No consideration for user energy/attention as finite resource
- Assumption that "more rigor = better outcomes"

**Consequences:**
- Later gates (8-11) get weaker analysis than early gates (1-4)
- Users skip the audit entirely for "small" projects
- Audit becomes thing to endure rather than tool to use
- Self-selection: only patient/obsessive users complete audits

**Real examples from RFU:**
- 11 gates with 3-5 questions each = ~40 questions total
- Gates 8-11 (second-order, Occam's, Pareto, regret) all require deep thinking
- No fast path for "I just want to know if this is worth pursuing"

**Prevention:**
- Create tiered audit system:
  - Quick triage (5 gates, 15 min): Gates 1, 3, 4, 5, 9 — core viability check
  - Full audit (11 gates, 60-90 min): Only for projects that pass triage
- Show progress: "Gate 3/11: 27% complete"
- Allow save-and-resume: don't force completion in one session
- Prioritize gates by impact: put highest-signal gates first (if they fail, stop early)
- Time-box each gate: "Spend max 5 minutes on Gate 1; if you can't answer quickly, that's a signal"

**Detection:**
- Audit completion rate <50% (people start but don't finish)
- Later gate answers are shorter/vaguer than early gate answers
- Users ask "can I skip some of these?"
- Complaints about time investment: "took me 3 hours"

**Which phase addresses:** Phase 4 (Quick Audit Mode)

---

### Pitfall 5: Non-Actionable Output (The "Now What?" Problem)

**What goes wrong:** Audit identifies failures but doesn't tell users how to fix them. Report says "Failed Gate 3: 10-Minute Test" but not "Simplify installation by removing Docker dependency" or "Add 2-minute video demo". Users get a diagnosis, not a treatment plan.

**Why it happens:**
- Audit frameworks focus on assessment, not remediation
- Fixes are context-dependent and hard to generalize
- Fear of prescribing wrong solution
- Assumption that "identifying the problem is enough"

**Consequences:**
- Users know they failed but don't know what to do next
- Projects stall: "we need to fix Gate 3 somehow"
- Framework generates busywork (audit ritual) without improvement
- Users stop auditing because it's not useful, just depressing

**Real examples from RFU:**
- Report shows 4/11 failed gates
- For each failure: "❌ Failed" with evidence
- No "here are 3 concrete fixes" or "similar projects solved this by..."
- No prioritization: which of 4 failures to fix first?

**Prevention:**
- Link each gate to fix resources:
  - Gate 3 failure → "See /prep-repo for publishing, consider this checklist..."
  - Gate 5 failure → "Run customer discovery interviews using this script..."
- Generate prioritized fix list with effort estimates:
  - "Fix Gate 7 (friction) first: High impact, low effort (2 hours)"
  - "Fix Gate 11 (regret) last: Low impact, requires deep thinking"
- Show examples: "Projects that failed Gate 3 and fixed it did so by: (1) Adding demo GIF (40%), (2) Simplifying install (30%), (3) Publishing to npm (20%)"
- Create fix templates: README template, demo script template, customer interview template
- Cross-reference to tools: "Failed Gate 3? Try /prep-repo to improve discoverability"

**Detection:**
- Users ask "how do I fix this?" after receiving audit results
- Audit reports get filed away without action
- No correlation between audit completion and project improvement
- Repeat audits show same failures (not improving over time)

**Which phase addresses:** Phase 7 (Priority Matrix), Phase 8 (Fix Integration)

---

### Pitfall 6: Cargo Cult Auditing (Process Without Understanding)

**What goes wrong:** Users complete the audit because it's "required" or trendy, but don't internalize the reasoning. They optimize for passing the audit rather than building useful products. The audit becomes a checkbox, not a thinking tool.

**Why it happens:**
- Framework gains reputation/authority, becomes "best practice"
- Users follow process mechanically without understanding why
- Social proof: "everyone does RFU audits so we should too"
- Pressure to show "we validated this" to stakeholders

**Consequences:**
- Projects game the audit: reframe answers until they pass, without changing the product
- False confidence: "we passed the audit so we're good" even if product still sucks
- Framework becomes compliance theater rather than validation tool
- Users resent the audit: "bureaucratic hoop to jump through"

**Prevention:**
- Explain the "why" for each gate: not just "check this" but "here's why this matters"
- Show failure case studies: "this project passed the audit but failed because..."
- Require auditor justification: can't just mark PASS/FAIL, must explain reasoning
- Add meta-gate: "Did this audit change your understanding of the project? If no, you're doing it wrong."
- Warning in documentation: "If you're optimizing to pass this audit rather than building something useful, you've missed the point."

**Detection:**
- Users ask "what do I need to say to pass Gate X?"
- Audit answers sound rehearsed/templated
- Projects pass audit but still fail in market
- Users complete audit quickly without hesitation (not actually thinking)

**Which phase addresses:** Not directly addressed by planned phases; mitigated by Phase 2 (clarity) and Phase 5 (auto-analyze preventing shallow answers)

---

### Pitfall 7: Stale Audit Results (Time-Decay Problem)

**What goes wrong:** Projects change after audits. Feature scope expands, target users shift, competitive landscape evolves. An audit done 6 months ago is irrelevant today, but teams cite it as validation. "We passed RFU in March" means nothing in October.

**Why it happens:**
- No expiration date on audit results
- No mechanism to trigger re-audit when project changes significantly
- Users want stable validation, not constant re-evaluation
- Audit feels like "one-and-done" milestone rather than ongoing practice

**Consequences:**
- Teams ship products based on outdated validation
- Scope creep invalidates original utility assessment (passed Gate 9/Occam's but added 50 features)
- Competitive landscape shifts (passed Gate 2/Inversion but competitor launched similar tool)
- Audit becomes historical artifact, not current reality check

**Prevention:**
- Add expiration date to audit results: "Valid until [date] or until significant project changes"
- Trigger re-audit conditions:
  - Added >3 major features since last audit
  - Target user changed
  - 6+ months since last audit
  - Competitive landscape changed
- Quick re-audit mode: focus on gates most likely to change (2, 3, 7, 9, 10)
- Version audit results: "v1.0 audit (2025-06-01)" vs "v2.0 audit (2025-12-01)"
- Show diff between audits: "Last time: 8/11. This time: 9/11. Improved Gate 3."

**Detection:**
- Teams cite audits from >6 months ago
- Project scope has obviously changed since audit
- Audit results don't match current reality
- Stakeholders question audit validity: "but that was before we added X"

**Which phase addresses:** Phase 6 (Audit History/Comparison)

---

## Moderate Pitfalls

Mistakes that cause delays, confusion, or technical debt.

### Pitfall 8: Gate Interdependencies Ignored

**What goes wrong:** Gates are scored independently, but some gates logically depend on others. Project passes Gate 1 (bedrock human need) but fails Gate 5 (nobody would pay) — which is contradictory. Auditors don't catch logical inconsistencies.

**Prevention:**
- Document gate interdependencies:
  - "If PASS Gate 1 but FAIL Gate 5, re-examine: does the need translate to willingness to pay?"
  - "If FAIL Gate 9 (not simplest) and FAIL Gate 10 (no core value), likely over-engineered"
- Add consistency checks: flag contradictions for manual review
- Show correlation patterns: "Projects that fail Gates 1+5 together have 90% market failure rate"

**Which phase addresses:** Phase 2 (Gate Clarity Enhancement) — add interdependency documentation

---

### Pitfall 9: One-Size-Fits-All Gates

**What goes wrong:** The 11 gates apply identically to all project types (CLI tools, SaaS, open-source libraries, internal tools). But Gate 5 (Wallet) doesn't make sense for free OSS. Gate 7 (Friction) is less relevant for internal tools. Forcing universal criteria makes some gates meaningless.

**Prevention:**
- Create project type profiles:
  - "Open-Source Library" profile: Skip Gate 5, double-weight Gates 3, 7, 9
  - "Internal Tool" profile: Skip Gates 2, 11, focus on Gates 3, 6, 10
  - "SaaS Product" profile: All gates apply, emphasize Gates 5, 8
- Allow gate exemptions with justification: "Gate 5 N/A: Free open-source project"
- Adjust scoring: score out of applicable gates only (9/10 applicable gates vs 9/11 total)

**Which phase addresses:** Not in current roadmap; consider for Phase 2 or later enhancement

---

### Pitfall 10: No Calibration Between Auditors

**What goes wrong:** Auditor A is harsh (average 5/11), Auditor B is lenient (average 9/11). Same project gets wildly different scores depending on who audits. No calibration process to align auditor expectations.

**Prevention:**
- Provide calibration dataset: 5 reference projects (2 strong, 2 weak, 1 borderline) with official scores
- New auditors score calibration projects first; if >20% divergence, they read more examples
- Track auditor harshness: if someone consistently scores 3 points lower than others, flag for review
- Pair auditing: 2 auditors score independently, discuss discrepancies, learn from each other

**Which phase addresses:** Out of scope for solo use; relevant if multi-user mode added later

---

### Pitfall 11: Manual Data Entry Errors

**What goes wrong:** Auditor reads README, types findings into audit report, introduces typos or misinterpretations. Project says "install in 5 minutes", auditor writes "installation takes 15 minutes", scores fail. Human transcription errors corrupt audit accuracy.

**Prevention:**
- Auto-populate where possible: extract from README, package.json, GitHub metadata
- Link to evidence: don't just say "FAIL", link to specific README section that caused failure
- Quote directly: "Project claims: [exact quote]" vs auditor's interpretation
- Structured extraction: parse README for time-to-value claims, feature lists, use cases

**Which phase addresses:** Phase 5 (Auto-Analyze Mode)

---

## Minor Pitfalls

Mistakes that cause annoyance but are fixable.

### Pitfall 12: Template Variable Errors

**What goes wrong:** Report template has `{{gate1_status}}` placeholder but auditor fills `{{gate_1_status}}` (underscore placement). Output shows literal template variable instead of value. Looks unprofessional and confuses users.

**Prevention:**
- Validate template variables before rendering: list expected variables, check all are populated
- Use structured output (JSON) that can't have missing fields
- Show preview before finalizing: "Review report for placeholders"
- Automated testing: render template with test data, check for remaining `{{*}}` patterns

**Which phase addresses:** Not in current roadmap; quick fix for existing template system

---

### Pitfall 13: Audit Report Bloat

**What goes wrong:** Audit report includes full evidence, reasoning, examples for all 11 gates. Results in 10+ page documents that nobody reads. Users want TL;DR: score + top 3 fixes.

**Prevention:**
- Executive summary first: score, rating, top 3 priorities
- Collapsible detail sections: click to expand full gate analysis
- Multiple report formats:
  - Short: 1 page, scores + fixes only
  - Standard: 3 pages, scores + evidence + fixes
  - Detailed: Full report with all reasoning
- Let user choose verbosity level

**Which phase addresses:** Not in current roadmap; consider for report template enhancement

---

### Pitfall 14: No Input Validation

**What goes wrong:** User runs `/rfu-audit ~/Dev/project-that-doesnt-exist`, skill tries to read files, fails with unclear error. Or user audits directory with no README, skill can't extract any info but continues anyway, producing nonsense report.

**Prevention:**
- Check path exists before starting audit
- Require minimum project files: README or package.json or source code
- Fail gracefully with clear error: "Cannot audit: no README found. Add README.md describing project."
- Suggest fixes: "Missing package.json? Run `npm init` first."
- Pre-flight check: "Found README ✓, package.json ✓, source files ✓. Proceeding..."

**Which phase addresses:** Phase 3 (Input Validation)

---

## Phase-Specific Warnings

| Phase Topic | Likely Pitfall | Mitigation |
|-------------|---------------|------------|
| Gate Clarity (Phase 2) | Over-specification — criteria so detailed they're rigid | Balance concrete examples with judgment flexibility; show range of acceptable answers |
| Quick Audit Mode (Phase 4) | Cherry-picking easy gates for quick mode, missing critical signals | Validate 5-gate triage actually correlates with full 11-gate outcome |
| Auto-Analyze (Phase 5) | Over-reliance on auto-populated answers, shallow audits | Mark auto-analyzed fields clearly; require human verification |
| Audit History (Phase 6) | Comparative gaming — "how do I improve from 7/11 to 9/11?" becomes goal instead of building better product | Emphasize that improving score ≠ improving product; score is diagnostic not target |
| Priority Matrix (Phase 7) | Generic fix priorities don't match specific project context | Allow manual priority override with justification |
| Fix Integration (Phase 8) | Tool sprawl — linking to 20 different skills/tools overwhelms user | Curate: link to top 2-3 most relevant resources per gate |

---

## Anti-Patterns to Watch For

### The Kitchen Sink Audit
**What:** Adding more and more gates because "we should check this too"
**Why bad:** Increases audit fatigue without proportional value increase
**Instead:** Maintain gate discipline; each gate must offer unique signal

### The Rubber Stamp Audit
**What:** Audits always pass because users optimize for passing, not for truth
**Why bad:** Audit loses credibility, becomes meaningless ritual
**Instead:** Show failure examples prominently; normalize project death

### The Gatekeeping Audit
**What:** Audit becomes barrier to shipping rather than tool for validation
**Why bad:** Stifles experimentation, favors safe/boring projects
**Instead:** Frame as "what to fix" not "whether to ship"; ship fast and iterate

### The Archaeological Audit
**What:** Audit produces historical document nobody looks at again
**Why bad:** Time investment with no ongoing value
**Instead:** Living document that triggers re-evaluation when project changes

---

## Sources

**PRIMARY:** `.planning/codebase/CONCERNS.md` — comprehensive technical debt and gap analysis
**SECONDARY:** `skill/SKILL.md`, `README.md` — current gate definitions and examples
**CONFIDENCE:** MEDIUM — based on existing codebase analysis and general audit framework patterns from training data. WebSearch unavailable for verification of current best practices (2026).

**Key insight:** Most pitfalls stem from tension between rigorous validation (needs clear criteria) and subjective judgment (unavoidable for "is this useful?"). Prevention strategies balance specificity with flexibility.

---

*Pitfalls research: 2026-01-22*
