# Phase 2: Gate Clarity - Research

**Researched:** 2026-01-22
**Domain:** Assessment rubric design, scoring reliability, evaluation frameworks
**Confidence:** MEDIUM (WebSearch verified with multiple authoritative sources for patterns; LOW for specific Gate 5/10/11 solutions requiring domain-specific validation)

## Summary

Research focused on how professional assessment frameworks achieve objective, reproducible scoring when evaluating potentially subjective criteria. Key findings:

**Inter-rater reliability** (two auditors scoring within 1 point) requires: (1) specific observable behaviors not adjectives, (2) numeric scales with concrete anchors, (3) binary yes/no criteria where possible, and (4) explicit threshold definitions. Research from educational assessment, code review rubrics, and UX evaluation frameworks consistently shows reliability improves when subjective language ("good," "basic," "competent") is replaced with measurable indicators.

**FAIL examples** serve as counterexamples that define boundaries. Most effective structure: present the scenario, show the attempted response, explain specifically why it fails the criteria, and contrast with a passing example. This clarifies the "floor" of acceptable performance.

**Edge case resolution** works best with decision trees or lookup tables that encode specific rules for borderline situations. Medical assessment research shows heuristic decision trees can resolve 90%+ of borderline cases into clear pass/fail decisions when designed with explicit branching logic.

**Primary recommendation:** Replace all subjective descriptors ("honest yes," "core value concentrated," "would regret") with either (a) countable/measurable criteria, (b) multiple observable signals that must co-occur, or (c) explicit comparison tests with documented examples.

## Standard Stack

### Assessment Design Patterns

The "standard stack" for objective assessment is a set of design patterns, not software libraries.

| Pattern | Purpose | Why Standard |
|---------|---------|--------------|
| Behaviorally Anchored Rating Scale (BARS) | Convert subjective judgments to observable behaviors | Increases objectivity by tying ratings to specific actions |
| Multi-dimensional scoring | Evaluate separate aspects independently | Prevents halo effect; each dimension has clear pass threshold |
| Anchor examples | Provide reference specimens for each score level | Calibrates raters; "this is what a 3 looks like" |
| Binary gates + rollup | Use yes/no sub-criteria that combine into overall score | Forces precision on components; reduces interpretation variance |
| Moderation sessions | Raters discuss borderline cases to align interpretation | Research shows this improves ICC from .368 to .766+ |

### Supporting Tools

| Tool/Method | Purpose | When to Use |
|-------------|---------|-------------|
| Scoring rubric template | Define criteria, levels, descriptors | Every assessment that needs reproducibility |
| Decision tree diagram | Resolve edge cases systematically | When borderline cases are frequent (>10% of audits) |
| FAIL example library | Illustrate clear failures for each criterion | Essential for training new auditors |
| Inter-rater reliability testing | Measure actual agreement between auditors | Before deployment and periodically (quarterly) |
| Calibration sets | Standard test cases for auditor training | When onboarding new auditors |

### Key Metrics

- **Intraclass Correlation Coefficient (ICC)**: Target >.75 for "good" reliability, >.9 for "excellent"
- **Percent agreement**: Aim for 80%+ exact agreement, 95%+ within 1 point
- **Cohen's Kappa**: For pass/fail decisions, target >.6 (substantial agreement)

## Architecture Patterns

### Recommended Rubric Structure

For each gate, use this three-layer structure:

```
Gate Definition
├── Pass Criteria Layer
│   ├── Primary threshold (the main decision rule)
│   ├── Supporting signals (observable indicators)
│   └── Disqualifiers (automatic fail conditions)
├── Scoring Rubric Layer (if not binary)
│   ├── Score levels (e.g., 0-4 or Pass/Borderline/Fail)
│   ├── Anchors for each level (concrete examples)
│   └── Observable behaviors per level
└── Edge Case Resolution Layer
    ├── Common gray areas listed
    ├── Decision rules for each
    └── Tie-breaker hierarchy
```

### Pattern 1: Observable Behavior Decomposition

**What:** Convert abstract criteria into multiple observable signals that are individually verifiable.

**When to use:** When the gate asks for subjective judgment ("honest," "concentrated," "regret")

**Example structure:**
```yaml
Abstract: "Honest yes to willingness to pay"
Decomposed signals:
  - Auditor would personally subscribe for 3+ months: [Yes/No]
  - Auditor can name specific workflow disruption this solves: [Yes/No]
  - Auditor can identify existing paid alternatives: [Yes/No]
  - If alternatives exist, this offers ≥30% time/cost savings: [Yes/No]
Pass threshold: All four signals = Yes
```

**Why it works:** Each component is verifiable. Disagreement isolates to specific sub-questions.

### Pattern 2: Comparative Benchmarking

**What:** Define pass/fail relative to established reference points rather than absolute judgment.

**When to use:** When "quality" is hard to define in isolation but clear in comparison.

**Example structure:**
```yaml
Gate 10 (Pareto): "Is value concentrated?"
Benchmark test:
  1. List all features
  2. Force-rank by user value (most to least)
  3. Calculate cumulative value percentage
  4. Pass criterion: Top 20% of features provide ≥70% of stated value

Documentation:
  - Feature list with value scores
  - Pareto chart showing cumulative curve
  - 70% threshold line marked
```

**Why it works:** Forces explicit ranking; threshold is visual and mathematical.

### Pattern 3: Counterfactual Testing

**What:** Use inversion/negative testing to validate subjective claims.

**When to use:** When forward reasoning allows wishful thinking ("I'd pay for this").

**Example structure:**
```yaml
Gate 5 (Wallet): Would you pay?
Forward test: "Would you pay $20/month?" → [Answer]
Counterfactual validation:
  - "If this required payment to continue using, would you pay?" [Yes/No]
  - "Have you ever paid for a similar tool?" [Yes/No]
  - "If you had to cut one $20/month subscription, would it be this?" [Yes/No]
Pass criterion: Forward = Yes AND 2+ counterfactuals = Yes
```

**Why it works:** Counterfactuals reveal revealed preference vs stated preference.

### Pattern 4: Threshold Stacking

**What:** Use multiple weak criteria that stack to strong evidence rather than one strong criterion.

**When to use:** When no single criterion is sufficient but combination is convincing.

**Example structure:**
```yaml
Gate 11 (Regret): Would future-you regret not shipping?
Stacked signals (need 4 of 6):
  1. Solves your own persistent problem (not theoretical): [Yes/No]
  2. You've manually done this task 10+ times: [Yes/No]
  3. You would use this weekly after building: [Yes/No]
  4. Absence of this creates measurable cost/delay: [Yes/No]
  5. You've searched for solutions and found none adequate: [Yes/No]
  6. Problem has existed for 6+ months: [Yes/No]
Pass: 4+ signals = Yes
```

**Why it works:** Weak evidence accumulates; reduces impact of any single misjudgment.

### Anti-Patterns to Avoid

- **Unanchored scales**: "Rate utility 1-5" without defining what each number means
- **Adjective escalation**: "Good/Great/Excellent" with no behavioral distinction
- **Solo judgment**: Any criterion that requires only one person's interpretation without verification
- **Undefined "reasonable"**: Phrases like "reasonable person would agree" without specifying who or how
- **Hidden weightings**: Multiple criteria with unclear relative importance

## Don't Hand-Roll

Problems that have existing solutions in assessment design:

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Measuring inter-rater reliability | Custom agreement calculator | ICC calculation (established formula) | Accounts for chance agreement; validated statistically |
| Subjective-to-objective conversion | Ad-hoc criteria invention | BARS methodology | 50+ years of validation in performance assessment |
| Edge case documentation | Unstructured notes file | Decision tree with explicit branches | Forces completeness; reveals gaps in logic |
| Calibration training | Verbal explanation of standards | Anchor examples with score labels | Visual/concrete; raters can self-check |
| Score aggregation | Simple averaging | Weighted criteria with explicit trade-offs | Some criteria are more important; averaging hides this |

**Key insight:** Assessment science has solved these problems repeatedly across education, HR, clinical settings, and code review. The failure mode is assuming your domain is "different enough" that established patterns don't apply. They do.

## Common Pitfalls

### Pitfall 1: Confidence Theater (Subjective Language Disguised as Objective)

**What goes wrong:** Rubric uses terms like "honest yes," "clearly concentrated," "obviously better" that feel precise but hide judgment calls.

**Why it happens:** Writer knows what they mean by "honest" but hasn't operationalized it for others.

**How to avoid:**
- Ban these words: honest, clear, obvious, reasonable, appropriate, adequate, sufficient
- Replace with countable criteria or multi-signal tests
- Test: Could two auditors apply this criterion to the same project and disagree? If yes, it's still subjective.

**Warning signs:**
- Audit team discussions focus on "what did you mean by..." questions
- Auditors report feeling uncertain about borderline cases
- Variance between auditors is high (ICC <.6)

### Pitfall 2: The Missing Floor (No FAIL Examples)

**What goes wrong:** Rubric defines good performance but not bad performance, so auditors invent their own failure threshold.

**Why it happens:** Writing pass criteria is natural; imagining specific failures requires effort.

**How to avoid:**
- For each gate, write 2-3 FAIL examples before finalizing pass criteria
- Use real-world projects that actually failed the gate
- Include "close but not quite" failures (borderline cases that should fail)

**Warning signs:**
- Auditors say "I know a pass when I see it" but can't articulate failure
- Inter-rater reliability is low on "weak" projects (they disagree on degree of failure)
- Appeals from creators focus on "you didn't say we couldn't do X"

### Pitfall 3: Hidden Dependencies (Criteria Assume Context)

**What goes wrong:** Gate criteria assume auditor has specific knowledge, tools, or context that isn't documented.

**Why it happens:** The rubric writer knows the domain deeply and doesn't notice implicit assumptions.

**How to avoid:**
- Onboard a completely new auditor with just the rubric (no verbal explanation)
- Document what information auditor needs access to (project docs, user interviews, analytics)
- Make all comparison references explicit ("compare to [named alternative]")

**Warning signs:**
- New auditors ask many questions that experienced auditors don't
- Criteria reference "typical," "standard," or "normal" without definition
- Audit reproducibility requires domain expertise beyond the rubric

### Pitfall 4: Scope Creep in Criteria

**What goes wrong:** Gate criteria expand to test multiple unrelated things, diluting the core question.

**Why it happens:** "While we're evaluating X, might as well check Y too..."

**How to avoid:**
- Each gate tests ONE question (even if using multiple signals)
- If a criterion feels like "and also check Z," that's probably a separate gate
- Maintain strict boundary: this gate passes/fails for THIS reason only

**Warning signs:**
- Gate verdict section lists multiple independent reasons for failure
- Removing one criterion changes the gate's purpose
- Auditors debate which sub-criterion "really" drove the decision

### Pitfall 5: The Anchoring Vacuum (No Reference Standards)

**What goes wrong:** Numeric scores (1-5, Pass/Borderline/Fail) lack concrete examples, so each auditor calibrates differently.

**Why it happens:** Assumes scales are self-evident ("everyone knows what a 3 means").

**How to avoid:**
- Provide ≥1 anchor example per score level
- Use real project excerpts, not made-up scenarios
- Include borderline examples ("this is a 2.5, we round to 3")

**Warning signs:**
- Auditors consistently use different parts of scale (one gives mostly 4-5, another gives 2-3)
- Debates about scores focus on labels not evidence
- Creators surprised by scores ("I thought this was clearly a 4")

## Code Examples

These are structural patterns, not code per se, but here are templates verified from assessment literature.

### Binary Criteria with Multi-Signal Validation

From coding interview rubrics (Tech Interview Handbook):

```yaml
Dimension: Communication
Question: "Does candidate explain their thinking?"

Observable signals (all required for PASS):
  - Asked clarifying questions about requirements: [Yes/No]
  - Explained approach before coding: [Yes/No]
  - Narrated logic while implementing: [Yes/No]
  - Stated trade-offs for chosen solution: [Yes/No]

Scoring:
  - 4/4 signals = Strong Pass
  - 3/4 signals = Pass
  - 2/4 signals = Borderline (use counterfactual tests)
  - 0-1/4 signals = Fail

Edge case: If candidate is silent but code is perfect, ask direct question.
If they can explain retroactively → Borderline/Pass. If not → Fail.
```

### Quantitative Threshold with Measurement Protocol

From Pareto/80-20 product management frameworks:

```yaml
Gate: Value Concentration Test (Gate 10 analog)

Measurement protocol:
  1. List all distinct features (feature = capability user can invoke)
  2. For each feature, estimate time-to-value (hours saved or earned per month)
  3. Sort features by time-to-value (descending)
  4. Calculate cumulative percentage of value
  5. Identify feature count at 80% cumulative value
  6. Calculate percentage: (features at 80% threshold / total features) × 100

Pass criteria:
  - ≤30% of features provide ≥80% of value → PASS
  - 31-50% of features provide ≥80% of value → BORDERLINE
  - >50% of features provide ≥80% of value → FAIL

Documentation required:
  - Feature list with value estimates
  - Pareto chart (cumulative curve)
  - Calculation showing percentage
```

### Counterfactual Validation Stack

From willingness-to-pay assessment research:

```yaml
Gate: Wallet Test (Gate 5 analog)

Primary question: "Would YOU pay $20/month for this?"
Forward answer: [Yes/No]

If "Yes", apply counterfactual validation (need 3/4):
  CF1: "If free version disappeared tomorrow, would you pay to keep using?"
    → Reveals dependency vs nice-to-have: [Yes/No]

  CF2: "Have you paid for similar categories of tools in past?"
    → Tests revealed preference vs hypothetical: [Yes/No]

  CF3: "List 3 current $20/month subscriptions. Would this rank in top 50%?"
    → Tests relative priority: [Yes/No]

  CF4: "If you could only choose this OR [named competitor], which would you pay for?"
    → Tests differentiated value: [This tool/Competitor]

Scoring:
  - Forward=Yes + 3-4 counterfactuals pass → PASS
  - Forward=Yes + 2 counterfactuals pass → BORDERLINE (apply tie-breaker)
  - Forward=Yes + 0-1 counterfactuals pass → FAIL (stated preference, not revealed)
  - Forward=No → FAIL (skip counterfactuals)

Tie-breaker for BORDERLINE:
  - Can you name 3 specific people (real names, real context) who have this problem? → If yes, upgrade to PASS. If no, downgrade to FAIL.
```

### FAIL Example Structure

From educational rubric best practices:

```markdown
## FAIL Example: Gate 4 (Bartender Test)

**Scenario:** Project is an MCP server that provides Claude Code with enhanced file system operations including recursive directory traversal with filtering, atomic file operations, and content transformation pipelines.

**Attempted one-sentence explanation:**
"A Claude Code skill that extends the native filesystem tools with advanced capabilities like atomic writes, recursive operations with glob patterns, and transformation pipelines for batch file processing."

**Why this FAILS:**
1. Uses technical jargon ("atomic writes," "glob patterns," "transformation pipelines")
2. Describes implementation features, not user value
3. Requires follow-up questions ("What's a transformation pipeline?" "Why do I care about atomic writes?")
4. More than one independent clause (compound sentence)

**Contrast with PASS example:**
"It lets Claude modify thousands of files at once without breaking anything."

**Why this PASSES:**
1. No jargon (anyone understands "thousands of files at once")
2. States value ("without breaking anything" = reliability)
3. Immediate comprehension, no follow-up needed
4. Single clear benefit
```

### Decision Tree for Edge Cases

From medical assessment heuristic decision trees:

```
Gate X: [Name]
Primary criterion: [Binary yes/no question]

Decision Tree:
                    [Apply primary criterion]
                            |
                +-----------+-----------+
                |                       |
            Clear YES               Clear NO
            → PASS                  → FAIL

If borderline (can't determine clear yes/no):

    Check Signal A: [Observable indicator 1]
         |
    +----+----+
    |         |
   YES       NO
    |         |
    v         v
  PASS    Check Signal B: [Observable indicator 2]
               |
          +----+----+
          |         |
         YES       NO
          |         |
          v         v
       PASS      FAIL

Example (Gate 5 - Wallet Test):
    Primary: "Would you pay $20/month?"
          |
    +-----+-----+
    |           |
  Clear      Uncertain
   YES          |
    |           v
  PASS    Check: "Have you searched for existing solutions?"
                    |
              +-----+-----+
              |           |
             YES         NO
              |           |
              v           v
        Check: "Were     FAIL
        existing tools    (hypothetical
        inadequate?"      need, not
              |           validated)
        +-----+-----+
        |           |
       YES         NO
        |           |
        v           v
      PASS        FAIL
                (existing
                solution
                works)
```

## State of the Art

### Evolution of Scoring Models

| Old Approach | Current Approach (2025) | When Changed | Impact |
|--------------|-------------------------|--------------|--------|
| Simple pass/fail binary | Graduated scales with explicit anchors | ~2015-2020 | More granular feedback but requires anchor definitions |
| Subjective judgment ("evaluator discretion") | Behaviorally Anchored Rating Scales (BARS) | 2000s-2010s | Dramatically improved ICC (.3-.5 → .7-.9) |
| Single-rater assessment | Multi-rater with moderation | 2010s | Catches individual bias; research shows moderation improves reliability .37 → .77 |
| Verbal criteria only | Criteria + anchor examples + FAIL examples | 2015+ | Reduces onboarding time; enables self-calibration |
| Holistic scoring | Multi-dimensional with rollup rules | 1990s-2000s | Isolates where disagreement occurs; clearer feedback |

### WCAG Accessibility Scoring (Domain-Specific Precedent)

**Current (WCAG 2.x, through 2025):**
- Binary pass/fail per success criterion
- Conformance levels: A, AA, AAA (strict hierarchy)
- Clear technical criteria ("contrast ratio ≥4.5:1")

**Emerging (WCAG 3.0, expected 2027+):**
- Graduated scoring: 0 (very poor) to 4 (excellent) per outcome
- Conformance levels: Bronze, Silver, Gold (replacing A/AA/AAA)
- Accommodates partial compliance and continuous improvement

**Key lesson for RFU audit:** Binary pass/fail is easier for compliance but graduated scales provide better diagnostic information. WCAG 3.0 adds graduated scales ONLY AFTER 20+ years of validated binary criteria. Don't skip the binary foundation.

### Code Review Rubrics (2025 Standard)

Modern code review platforms use 4-dimension model:
1. **Communication** (process behavior)
2. **Problem Solving** (approach quality)
3. **Technical Competency** (implementation quality)
4. **Testing** (verification rigor)

Each dimension scored independently (Strong Hire / Leaning Hire / Leaning No Hire / Strong No Hire), then aggregated with explicit rules ("Any Strong No Hire = overall reject," "2+ Strong Hire with no No Hire = overall hire").

**Key lesson for RFU audit:** Dimension independence prevents halo effect. Make gate scoring independent, then aggregate with explicit pass threshold (e.g., "Must pass 9 of 11 gates").

### UX Evaluation: PURE Method (2020s)

Practical Usability Rating by Experts uses 3-point scale for task difficulty:
- Score 1: Easy (minimal effort, intuitive)
- Score 2: Moderate (noticeable effort, some hunting)
- Score 3: Difficult (significant effort, confusion, errors)

Raters score each task step independently, aggregate into task score, compute overall UX score. Research shows trained raters achieve ICC >.8 with this simple scale.

**Key lesson for RFU audit:** Three-point scales balance granularity and reliability. Five-point scales increase disagreement; two-point (binary) works only when criteria are extremely clear.

### Deprecated/Outdated Approaches

- **Holistic scoring without decomposition:** "Rate overall quality 1-10" → Impossible to calibrate, ICC <.5
- **Undefined subjective terms:** "Adequate," "reasonable," "sufficient" without behavioral anchors → Research shows these reduce ICC by .2-.3 points
- **Single-example rubrics:** Showing only "good" example without "bad" example → Raters invent different failure thresholds, high variance
- **Expert-only criteria:** Requirements that assume deep domain knowledge → Limits auditor pool, reduces consistency as expertise varies

## Open Questions

### Question 1: Inter-Gate Dependencies

**What we know:** Some gates logically depend on others (e.g., Gate 3 "10-Minute Test" requires Gate 4 "Bartender Test" to be comprehensible).

**What's unclear:** Should failing an earlier gate auto-fail dependent gates, or should each be scored independently?

**Recommendation:** Score all gates independently for diagnostic value, but add a "dependency note" field. If Gate 4 fails, note in Gate 3 verdict: "Score may be invalid due to Gate 4 failure (comprehension blocker)." This preserves information without making assumptions.

### Question 2: Calibration Frequency

**What we know:** Inter-rater reliability drifts over time without recalibration. Medical assessment research shows quarterly recalibration maintains ICC.

**What's unclear:** For an open-source audit tool, who conducts calibration? How are anchor examples updated as the ecosystem evolves?

**Recommendation:**
- Create "calibration pack" with 3-5 reference audits covering range of pass/fail patterns
- New auditors must score calibration pack; compare to reference scores
- If agreement <80%, require moderation discussion before live audits
- Update calibration pack annually with new examples from real audits

### Question 3: Aggregate Scoring Model

**What we know:** Current framework has 11 gates. Unclear if project must pass all 11, or if some failures are acceptable.

**What's unclear:**
- Is this a strict AND (all gates pass) or threshold model (9 of 11 pass)?
- Are some gates weighted more heavily?
- Is there a "critical gate" that auto-fails the project?

**Recommendation:** Based on assessment design patterns:
- Define "Must-Pass Gates" (e.g., Gates 1, 2, 4) that are foundational
- Define "Threshold" for other gates (e.g., must pass 6 of 8 remaining)
- Document rationale: why these gates are must-pass vs nice-to-pass
- Alternative: Use graduated project rating (5-star: passes all 11; 4-star: passes 9-10; etc.)

### Question 4: Handling "Not Applicable" Cases

**What we know:** Some gates may not apply to certain project types (e.g., Gate 5 "Wallet Test" might not apply to pure research tools).

**What's unclear:** Does "N/A" count as pass, fail, or skip in aggregate scoring?

**Recommendation:**
- Add N/A as explicit option for each gate
- Criteria for N/A must be defined ("Gate 5 N/A only if project explicitly declares 'research/education only, not production tool'")
- In aggregate scoring, exclude N/A gates from denominator (if project has 2 N/A, must pass 8 of 9 remaining)
- If >50% gates are N/A, question whether audit framework is appropriate for project type

### Question 5: Scoring Open-Source Projects with Future-Oriented Gates

**What we know:** Gates 5 (Wallet), 11 (Regret) ask about hypothetical future behavior. For OSS projects evaluating *others'* work, these become awkward.

**What's unclear:** Should auditor answer as if they built it, or try to evaluate original creator's likely answer?

**Recommendation:**
- Reframe these gates for third-party audit context:
  - Gate 5: "Market test" - Is there evidence users would pay? (GitHub stars, issues requesting features, similar paid tools existing)
  - Gate 11: "Sustainability test" - Is there evidence of ongoing commitment? (Commit frequency, roadmap, issue response time)
- Alternative: Mark these gates as "self-audit only" and exclude from third-party RFU audits
- Document the framing clearly: "These criteria assess market validation, not auditor's personal preference"

## Specific Recommendations for Gates 5, 10, 11

### Gate 5: Wallet Test (Currently Subjective: "Honest Yes")

**Current problem:** "Honest yes" is unmeasurable. "Real names not archetypes" is a step toward objectivity but doesn't capture willingness-to-pay validation.

**Recommended objective criteria:**

```yaml
Gate 5: Market Validation Test
Replace "Would YOU pay?" with multi-signal market evidence.

For self-audits (creator evaluates own project):
Signal A: "I have paid for similar tools in this category" [Yes/No]
Signal B: "I can name 3 people (real names) who have this exact problem" [Yes/No]
Signal C: "I have validated the problem through >5 conversations" [Yes/No]
Signal D: "Comparable tools charge $10-100/month" [Yes/No]

Pass: 3+ signals = Yes
Borderline: 2 signals = Yes (apply counterfactual: "If you had to cut one subscription to afford this, which would you cut?")
Fail: 0-1 signals = Yes

For third-party audits (auditor evaluates others' project):
Signal A: GitHub stars >100 OR issues requesting features >10 [Yes/No]
Signal B: Comparable paid tools exist in same category [Yes/No]
Signal C: Project solves problem that costs time/money (not just convenience) [Yes/No]
Signal D: 3+ users report using in production (Issues/PRs/discussions) [Yes/No]

Pass: 3+ signals = Yes
Fail: 0-2 signals = Yes
```

**Why this works:**
- Eliminates "honest" judgment call
- Uses revealed preference (past payment, user evidence) not stated preference
- Countable criteria (can you list the names? yes/no)
- Third-party version uses observable project data, not auditor opinion

**Confidence:** MEDIUM. This approach is validated in product validation research but hasn't been tested specifically on OSS tools. Requires pilot testing with 5-10 audits to verify inter-rater reliability.

### Gate 10: Pareto Gate (Currently Subjective: "Core Value Concentrated")

**Current problem:** "Concentrated" is vague. "Can't identify the ONE feature" is a useful fail signal but lacks measurement.

**Recommended objective criteria:**

```yaml
Gate 10: Value Concentration Test
Measure actual Pareto distribution, not subjective judgment.

Measurement protocol:
1. List all user-facing features (N = total features)
2. For each feature, estimate impact score (1-10 scale):
   - 10: Core functionality, project useless without it
   - 5: Meaningful enhancement, clear use case
   - 1: Edge case or convenience
3. Sort features by impact score (descending)
4. Calculate cumulative impact percentage
5. Find number of features needed to reach 80% cumulative impact (K)
6. Calculate concentration ratio: K/N

Pass criteria:
  - K/N ≤ 0.30 (top 30% of features = 80% of value) → PASS
  - 0.30 < K/N ≤ 0.50 → BORDERLINE
  - K/N > 0.50 → FAIL (value spread too thin)

Alternative binary test (if impact scoring is too subjective):
  - Can you name the ONE feature that delivers majority of value? [Yes/No]
  - If that feature was removed, would project still be useful? [Yes/No]
  - Are other features primarily enhancements to the core feature? [Yes/No]

Pass: First question = Yes AND (second question = No OR third question = Yes)
Fail: Cannot identify one core feature

Documentation required:
  - Feature list with impact scores
  - Pareto chart showing cumulative distribution
  - Concentration ratio calculation
```

**Why this works:**
- Forces explicit feature enumeration and ranking
- Concentration ratio is a number, not judgment
- Visual (Pareto chart) makes "concentrated" vs "spread thin" obvious
- Binary alternative if impact scoring feels too subjective

**Confidence:** MEDIUM-HIGH. Pareto analysis is well-established in product management. The 30% threshold is based on common 80/20 interpretations (20% = strict Pareto; 30% = relaxed for small feature sets). Requires validation: does this threshold distinguish "focused" vs "feature-creep" projects?

### Gate 11: Regret Minimization (Currently Subjective: "Would Regret NOT Shipping")

**Current problem:** Future projection is inherently personal. "Resume-driven development" is a good fail signal but requires mind-reading.

**Recommended objective criteria:**

```yaml
Gate 11: Commitment Validation Test
Replace future regret projection with current evidence of commitment.

For self-audits (creator evaluates own project):
Signal A: "I have been manually doing this task for 6+ months" [Yes/No]
Signal B: "I searched for existing solutions before building" [Yes/No]
Signal C: "Existing solutions were inadequate (list reasons)" [Yes/No]
Signal D: "I will use this weekly after building" [Yes/No]
Signal E: "This problem costs me 2+ hours per week" [Yes/No]
Signal F: "I would rebuild this if the repo was deleted" [Yes/No]

Pass: 5+ signals = Yes
Borderline: 4 signals = Yes (apply counterfactual: "Would you recommend a friend spend 40 hours building this?")
Fail: 0-3 signals = Yes

For third-party audits (auditor evaluates others' project):
Signal A: Commit history spans 3+ months (not one-time build) [Yes/No]
Signal B: Issues and PRs show active response (median response time <7 days) [Yes/No]
Signal C: Documentation includes roadmap or future plans [Yes/No]
Signal D: Project solves problem creator publicly described before building [Yes/No]
Signal E: Creator has built multiple projects in this domain (not one-off) [Yes/No]

Pass: 4+ signals = Yes
Fail: 0-3 signals = Yes

Disqualifiers (automatic FAIL regardless of signals):
  - Project README explicitly states "learning exercise" or "tutorial"
  - Project is a fork/clone of existing popular tool with no differentiation
  - Creator states in issues/docs they don't use the tool themselves
```

**Why this works:**
- Replaces future projection with past behavior (duration, search effort, cost)
- Uses revealed commitment (time spent, responsiveness) not stated intent
- Counterfactual test ("Would you recommend a friend build this?") applies opportunity cost reasoning
- Third-party version observable from GitHub data, no mind-reading required

**Confidence:** MEDIUM. Commitment validation through behavioral signals is validated in regret minimization research (2025). The specific thresholds (6 months, 2 hours/week, 3+ months commit history) are informed by research but arbitrary for OSS context. Requires pilot testing to tune thresholds.

**Alternative framing:** Rename this gate from "Regret Minimization" to "Commitment Validation" to avoid future-oriented language. The underlying question is the same (is this worth doing?) but framing emphasizes observable evidence over hypothetical projection.

## File Structure Recommendations

Based on research into assessment documentation patterns and the existing RFU audit structure:

### Primary Changes to `config/gates-full.md`

Add three new sections to each gate definition:

```markdown
## Gate N: [Name]

[Existing process description]

**Pass criteria:** [Enhanced with specific thresholds]

**Fail indicators:** [Enhanced with countable criteria]

**NEW → Objective Rubric:**
[Specific measurement protocol with numeric thresholds or multi-signal tests]

**NEW → Scoring:**
- Pass: [Explicit conditions]
- Borderline: [Explicit conditions] → [Tie-breaker rule]
- Fail: [Explicit conditions]

**NEW → Edge Cases:**
- [Common gray area 1]: [Resolution rule]
- [Common gray area 2]: [Resolution rule]

**Template Variables:** [Existing variables, may need additions]
```

### Structure for `guides/GATE-EXAMPLES.md`

```markdown
# Gate Examples: Pass and Fail Patterns

This document provides concrete examples of projects that PASS and FAIL each gate, with detailed analysis of why.

## How to Use This Document

- **For auditor training:** Score the example projects yourself before reading the verdict
- **For calibration:** Compare your score to the documented score; if different, review reasoning
- **For edge case reference:** See how borderline cases are resolved

---

## Gate 1: The 5 Whys of Utility

### PASS Example 1: [Project Name]

**Project description:** [2-3 sentence summary]

**5 Whys chain:**
1. Why does this exist? [Answer]
2. Why do people need that? [Answer]
3. Why can't they use existing tools? [Answer]
4. Why does that matter? [Answer]
5. Why is that important? [Answer]
→ Bedrock: [Fundamental human need]

**Why this PASSES:**
- Reaches bedrock need (autonomy/time/money/status/connection/safety)
- Each why builds on the previous
- Final answer is universally relatable

---

### FAIL Example 1: [Project Name]

**Project description:** [2-3 sentence summary]

**5 Whys chain:**
1. Why does this exist? [Answer]
2. Why do people need that? [Answer - but stops at technical reason]

**Why this FAILS:**
- Chain stops at technical implementation ("because the API is easier")
- Never reaches human need
- Could stop at any point without clear end

**Contrast:** Compare to PASS Example 1 where each why reveals deeper motivation.

---

### Borderline Example 1: [Project Name]

**Project description:** [2-3 sentence summary]

**5 Whys chain:** [Ambiguous chain]

**Resolution:**
- Signals toward PASS: [List evidence]
- Signals toward FAIL: [List evidence]
- Applied decision rule: [Edge case guide reference]
- **Final verdict: PASS/FAIL**
- **Reasoning:** [Specific tie-breaker that determined verdict]

---

[Repeat structure for all 11 gates]

---

## Calibration Pack

The following 5 projects represent the range of pass/fail patterns. New auditors should score all 5 and compare to reference scores before conducting live audits.

### Calibration Project 1: [Name]
**Expected scores:**
- Gate 1: PASS (strong)
- Gate 2: PASS (borderline)
- Gate 3: FAIL
[etc.]

**Download:** [Link to archived project snapshot]
**Reference audit:** [Link to completed audit report]
```

### Structure for `guides/EDGE-CASES.md`

```markdown
# Edge Case Resolution Guide

This document provides explicit rules for resolving borderline cases where pass/fail is unclear.

## How to Use This Document

1. Apply the gate's primary criteria first
2. If verdict is clear (strong pass or clear fail), no need to reference this guide
3. If verdict is borderline (could argue either way), find the relevant edge case below
4. Apply the decision rule to force a deterministic verdict

---

## Cross-Gate Edge Cases

### Edge Case: N/A Determination

**When:** A gate may not apply to certain project types.

**Question:** Under what conditions is a gate marked N/A instead of Pass/Fail?

**Resolution rule:**
1. Check if project explicitly declares out-of-scope for gate's domain:
   - Gate 5 (Wallet): N/A only if README states "educational/research tool, not production"
   - Gate 3 (10-Minute): N/A only if project is library/API with no end-user interface
2. If no explicit declaration → Gate applies → Score Pass/Fail
3. If >50% of gates are N/A → Question if audit framework is appropriate

**Example:**
- Pure research project exploring algorithms → Gate 5 may be N/A
- But must have explicit statement: "This is a research artifact, not a production tool"
- If README presents it as usable tool → Gate 5 applies

---

## Gate 1: The 5 Whys of Utility

### Edge Case 1: Chain Stops at "Because I Wanted to Learn"

**When:** Creator's motivation is learning/skill-building, not solving a user problem.

**Question:** Does this pass (learning is a valid human need) or fail (not utility-focused)?

**Resolution rule:**
- **FAIL** if the learning goal is the primary value (project is tutorial/exercise)
- **PASS** if learning was motivation but project delivers utility to others
- Tie-breaker test: "If creator stops using it, would others continue?" → Yes = PASS, No = FAIL

**Example:**
- "I built this to learn React" + no one else uses it → FAIL
- "I built this to learn React" + 50 GitHub stars + production users → PASS

---

### Edge Case 2: Bedrock Need is "Status" or "Connection"

**When:** The 5 Whys chain reaches "to gain recognition" or "to connect with community."

**Question:** Are these valid bedrock needs or signs of resume-driven development?

**Resolution rule:**
- Check if status/connection is *means* or *end*:
  - If status/connection enables other value (e.g., "recognition helps me get hired to solve bigger problems") → Continue chain, not bedrock yet
  - If status/connection is inherent to the value (e.g., "helps developers showcase their work to employers") → Valid bedrock
- Tie-breaker: Would someone with no status needs (e.g., established expert) find this valuable? → Yes = PASS, No = FAIL

---

## Gate 2: The Inversion Test

### Edge Case 1: "Minor Inconvenience" Boundary

**When:** Users would notice absence but have easy alternatives.

**Question:** What level of inconvenience is sufficient for PASS?

**Resolution rule:**
- Measure switching cost:
  - Alternative exists + ≤5 minutes to switch + no data loss → FAIL (convenience only)
  - Alternative exists + 1-4 hours to switch (setup, migration) → BORDERLINE (apply quantitative test)
  - Alternative exists + >4 hours to switch OR data loss OR recurring cost → PASS
- Quantitative test for BORDERLINE: Does this save >20% time/cost vs alternative? → Yes = PASS, No = FAIL

**Example:**
- "They'd use [identical competitor] with 2-click signup" → FAIL
- "They'd use [complex alternative] requiring 3-hour setup" → PASS

---

## Gate 3: The 10-Minute Test

### Edge Case 1: Pre-requisite Dependency Timing

**When:** Project requires external setup (Docker, API keys, existing tools) that auditor already has.

**Question:** Does time-to-value include pre-requisite setup time?

**Resolution rule:**
- **Include pre-req time** if:
  - Pre-req is project-specific (must sign up for this API, install this specific tool)
  - Pre-req is uncommon (≤30% of target users already have it)
- **Exclude pre-req time** if:
  - Pre-req is domain-standard (Node.js for JS projects, Python for Python projects)
  - Pre-req is common (≥70% of target users already have it)
- Tie-breaker: Check project's "Requirements" section. If it lists >3 items → Include time

**Example:**
- Requires Claude Code (target users are Claude Code users) → Exclude setup time
- Requires obscure API signup + email verification + approval wait → Include time

---

### Edge Case 2: "Aha Moment" for Incremental Tools

**When:** Project improves existing workflow but doesn't create entirely new capability.

**Question:** What counts as "aha moment" for incremental improvements?

**Resolution rule:**
- Aha moment = "Clear demonstration that this is better/faster/easier than what I do now"
- For incremental tools:
  - Must complete a real task (not demo/example)
  - Must measure time/effort savings (e.g., "this took 10 seconds vs 2 minutes manually")
  - If no measurable difference within 10 minutes → FAIL

**Example:**
- Code formatter: Aha = "This auto-formatted 500 lines instantly" (vs manual formatting)
- Search tool: Aha = "This found the function in 3 seconds" (vs grep/IDE search)

---

## Gate 4: The Bartender Test

### Edge Case 1: Domain-Specific Terms

**When:** Explanation uses terms that are jargon to general public but common language in target domain.

**Question:** Does "non-technical person" mean "average person" or "average person in target audience"?

**Resolution rule:**
- Bartender = average person with no domain knowledge (literal bartender at a bar)
- Domain-specific terms are ONLY allowed if:
  - Term is in common vernacular (95%+ of adults know it)
  - Term is immediately explained in same sentence
- Examples of acceptable terms: "email," "website," "file," "password"
- Examples of unacceptable terms: "API," "repository," "CLI," "deploy," "schema"
- Tie-breaker: Would your grandmother understand this? → Yes = PASS, No = FAIL

---

### Edge Case 2: Two-Sentence Explanations

**When:** Explanation is technically two sentences but feels like one thought.

**Question:** Is strict one-sentence rule enforced?

**Resolution rule:**
- Count independent clauses:
  - "It does X. It also does Y." → 2 sentences, multiple thoughts → FAIL
  - "It does X, so you can Y." → 2 clauses, one thought → PASS (acceptable)
  - "It does X. This means Y." → 2 sentences, one thought → BORDERLINE (apply comprehension test)
- Comprehension test: Can you understand value from first sentence alone? → Yes = PASS, No = FAIL

---

## Gate 5: Wallet Test (Market Validation)

### Edge Case 1: Creator Wouldn't Pay Because They Built It

**When:** Using self-audit framing ("Would YOU pay?"), creator says "No, because I already built it."

**Question:** Does this fail (not valuable enough) or is it expected (why pay for what you own)?

**Resolution rule:**
- Reframe question: "If you hadn't built this, and someone else offered it for $20/month, would you pay?"
- If still "No" → FAIL (revealed preference: not worth $20 of your time per month)
- If "Yes, but..." → Apply counterfactual tests (see Gate 5 rubric)
- If clear "Yes" → Continue to naming 3 people test

---

### Edge Case 2: Can Name Types but Not Names

**When:** Can identify 3 specific user personas but not real individuals.

**Question:** Is "Sarah, a mid-career PM at a Series B startup" specific enough?

**Resolution rule:**
- **Real names required** means you could send them a message today:
  - Colleague/friend/contact you've discussed this problem with → PASS
  - Hypothetical persona ("a PM at a startup") → FAIL
  - Real person but you've never validated they have this problem → BORDERLINE (apply validation test)
- Validation test: Have you confirmed this specific person has this problem? → Yes = PASS, No = FAIL

**Example:**
- "John Smith, my coworker, who complained about this last week" → PASS
- "A PM at a Series B startup" → FAIL
- "Jane Doe, a PM I read about on Twitter" → FAIL (no validation)

---

## Gate 10: Pareto Gate (Value Concentration)

### Edge Case 1: Feature Count Ambiguity

**When:** Unclear what counts as separate feature vs variation of same feature.

**Question:** How granular should feature list be?

**Resolution rule:**
- Feature = distinct user-facing capability that could be toggled on/off independently
- Examples:
  - "Search" with filters (keyword, date, author) → 1 feature (filters are parameters)
  - "Search by keyword" + "Search by semantic similarity" → 2 features (different algorithms, different value props)
  - "Light mode" + "Dark mode" → 1 feature (theme is one capability, mode is parameter)
- Tie-breaker: If removing this doesn't break other features → separate feature. If removing breaks dependents → sub-feature.

---

### Edge Case 2: All Features Seem Essential

**When:** Creator believes all features are core, can't identify the "20%."

**Question:** Does this fail Pareto (value spread thin) or pass (all features tightly coupled)?

**Resolution rule:**
- Apply forced ranking:
  - Sort features by "time-to-value per user" (hours saved or problems solved)
  - If top 30% of features represent ≥80% of time-to-value → PASS (coupled but concentrated)
  - If value is evenly distributed (each feature ~same impact) → FAIL (feature creep)
- Alternative test: "Which one feature would you keep if forced to delete all others?" → If clear answer exists = PASS, If "impossible to choose" = FAIL

---

## Gate 11: Regret Minimization (Commitment Validation)

### Edge Case 1: Long-Standing Problem, New Solution Approach

**When:** Creator has problem for years but just discovered this solution approach.

**Question:** Does "6 months of manual work" require 6 months of *awareness* of this solution?

**Resolution rule:**
- Focus on problem duration, not solution awareness:
  - Problem existed 6+ months → Signal A = Yes
  - Searched for solutions (past or present) → Signal B = Yes
  - Newly discovered this approach is valid (solution novelty doesn't reduce problem validity)
- Disqualifier: If solution approach is trendy/recent (e.g., "just heard about this tech") AND problem is vague ("inefficiency") → Apply counterfactual: "Would you rebuild this in 6 months when trend fades?" → No = FAIL

---

### Edge Case 2: High Initial Commitment, Low Future Use

**When:** Creator spent 40 hours building but will only use monthly (not weekly).

**Question:** Does "weekly use" requirement disqualify genuinely useful but infrequent-use tools?

**Resolution rule:**
- Adjust frequency threshold by impact:
  - High-impact, low-frequency (saves 5+ hours per use) → Monthly use acceptable → Modify Signal D to "weekly or monthly high-impact"
  - Low-impact, low-frequency (saves 30 min per use) → Weekly use required
- Quantitative test: (Time saved per use) × (Uses per year) ≥ 20 hours? → Yes = PASS, No = FAIL

---

## Decision Tree Summary

For quick reference, here's the edge case resolution priority:

```
1. Check for automatic FAIL conditions (disqualifiers)
   ↓
2. Apply primary gate criteria (main rubric)
   ↓
3. If clear PASS or FAIL → Done
   ↓
4. If BORDERLINE → Find relevant edge case above
   ↓
5. Apply edge case resolution rule
   ↓
6. Document which edge case was used in verdict
```

---

## Updating This Guide

When you encounter a new edge case in practice:

1. **Document the ambiguity:** What made this case unclear?
2. **Record the signals:** What evidence pointed toward PASS vs FAIL?
3. **Define the resolution rule:** What test/threshold would resolve future similar cases?
4. **Add to this guide** under relevant gate section
5. **Update calibration pack** if this represents a new pattern

This guide should grow with audit experience, codifying institutional knowledge.
```

## Sources

### Primary (HIGH confidence)

Assessment rubric methodology and inter-rater reliability:
- [Effect of moderation on rubric criteria for inter-rater reliability](https://pmc.ncbi.nlm.nih.gov/articles/PMC9358671/)
- [Inter-rater Reliability of a Clinical Documentation Rubric](https://pmc.ncbi.nlm.nih.gov/articles/PMC7405303/)
- [Behaviorally Anchored Rating Scale | Ultimate Guide 2025](https://www.chrmp.com/behaviorally-anchored-rating-scale-bars/)
- [Best Teacher Evaluation Rubric for 2025](https://educationwalkthrough.com/teacher-evaluation-rubric/)

Code review and technical evaluation rubrics:
- [Tech Interview Handbook: Coding Interview Rubrics](https://www.techinterviewhandbook.org/coding-interview-rubrics/)
- [Rubric development for code quality assessment (ACM)](https://dl.acm.org/doi/10.1145/2999541.2999555)

Assessment rubric design best practices:
- [Rubric Best Practices, Examples, and Templates (NC State)](https://teaching-resources.delta.ncsu.edu/rubric_best-practices-examples-templates/)
- [Creating Effective Rubrics (Berkeley Teaching Center)](https://teaching.berkeley.edu/teaching-strategies/assessing-learning/assessment-rubrics)

### Secondary (MEDIUM confidence)

Accessibility scoring and WCAG conformance models:
- [WCAG 3.0's Proposed Scoring Model (Smashing Magazine)](https://www.smashingmagazine.com/2025/05/wcag-3-proposed-scoring-model-shift-accessibility-evaluation/)
- [Website Accessibility Audit: Complete 2025 WCAG 2.2 Guide](https://www.allaccessible.org/blog/website-accessibility-audit-guide-wcag-template)
- [Accessibility Scoring – The BarrierBreak Way](https://www.barrierbreak.com/accessibility-scoring-the-barrierbreak-way/)

UX evaluation frameworks:
- [Practical Usability Rating by Experts (PURE) – MeasuringU](https://measuringu.com/pure/)
- [UI UX Design evaluation methods for usability testing (2025)](https://reloadux.com/blog/ui-ux-design-evaluation-methods-for-usability-testing/)
- [System Usability Scale (SUS) Practical Guide for 2025](https://blog.uxtweak.com/system-usability-scale/)

Product validation and willingness-to-pay assessment:
- [Validate willingness to pay (Learning Loop)](https://learningloop.io/playbooks/validating-willingness-to-pay)
- [Product Validation: 9 Proven Strategies for 2025 (Shopify)](https://www.shopify.com/blog/validate-product-ideas)
- [Willingness to Pay in SaaS: Definition, Calculation, Factors](https://userpilot.com/blog/willingness-to-pay/)

Pareto principle application to product features:
- [Learn the Pareto Principle (The 80/20 Rule) [2025] (Asana)](https://asana.com/resources/pareto-principle-80-20-rule)
- [The 80/20 Rule (Pareto Principle) in Product Management](https://fibery.io/blog/product-management/pareto-principle/)
- [How Does the Pareto Principle Apply to Product Development? (Miro)](https://miro.com/product-development/pareto-principle/)

Regret minimization framework:
- [The Regret Minimization Framework: A Practical Checklist](https://www.heyjoyful.com/innovation-insights/regret-minimization-framework/)
- [Regret Minimization Framework: How It Works & Strategies (Britannica)](https://www.britannica.com/money/regret-minimization-theory)

### Tertiary (LOW confidence - WebSearch only, marked for validation)

Edge case resolution and decision trees:
- [Heuristic decision tree for pass/fail decisions (ResearchGate)](https://www.researchgate.net/figure/Heuristic-decision-tree-used-to-determine-pass-fail-decision-The-decision-tree-was_fig3_380029051)
- [Help Desk Decision Trees Guide for 2025](https://knowmax.ai/blog/help-desk-decision-trees/)

Converting subjective to objective measures:
- [Objective and subjective measurement in applied business settings (2025)](https://journals.sagepub.com/doi/10.1177/03128962241286258)
- [Essential objective and subjective criteria for evaluating design](https://blog.logrocket.com/ux-design/objective-subjective-criteria-evaluating-design/)

## Metadata

**Confidence breakdown:**
- Assessment rubric patterns: HIGH - Multiple authoritative sources (academic research, established frameworks like BARS, PURE, WCAG)
- FAIL example structure: HIGH - Consensus across educational and professional assessment literature
- Edge case documentation: MEDIUM - Medical/clinical examples well-documented; software assessment less standardized
- Code review rubrics: HIGH - Tech Interview Handbook + ACM research provide validated examples
- Gates 5/10/11 specific solutions: LOW - Recommendations extrapolated from domain research but not tested on RFU audit framework

**Research date:** 2026-01-22
**Valid until:** 30 days (assessment methodology stable; specific tool recommendations may update)

**Critical gaps requiring validation:**
1. Recommended thresholds (30% for Pareto, 6 months for commitment, $20 for wallet) are informed estimates, not validated for OSS audit context
2. Inter-rater reliability targets (ICC >.75) are from educational assessment; software project audits may differ
3. Specific counterfactual tests proposed for Gates 5/11 require pilot testing with real projects

**Next steps for planner:**
- Pilot test objective criteria for Gates 5, 10, 11 with 3-5 sample projects
- Create calibration pack with reference audits
- Define aggregate scoring model (must-pass gates vs threshold)
- Establish N/A criteria for each gate
