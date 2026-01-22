# The 11 Gates: Complete Specifications

This document contains the complete definitions of all 11 gates used in the RFU audit framework. Each gate includes its process, pass criteria, fail indicators, and template variables for the audit report.

---

## Gate 1: The 5 Whys of Utility

Drill down until you hit bedrock human need.

**Process:**
```
Why does this exist?
→ Why do people need that?
→ Why can't they use existing tools?
→ Why does that matter?
→ Why is that important?
→ BEDROCK: [Fundamental human need]
```

**Pass criteria:** Reaches a fundamental need (autonomy, time, money, status, connection, safety)

**Fail indicators:**
- Chain stops at technical reasons
- Can't articulate user benefit
- "Because it's cool" or "I wanted to learn"

## Objective Rubric

**Measurement Protocol:**

1. Execute the 5 Whys chain by asking "Why does this exist?" and drilling down with each answer
2. Count the number of iterations needed to reach a fundamental human need
3. Evaluate the final answer against the bedrock needs list

**Thresholds:**

| Verdict | Criteria |
|---------|----------|
| **Pass** | Chain reaches fundamental human need (autonomy/time/money/status/connection/safety) within 5 iterations |
| **Borderline** | Chain reaches 5 iterations without clear bedrock, but each why builds logically on the previous answer |
| **Fail** | Chain stops at technical reason, loops back on itself, or cannot proceed past 3 iterations |
| **Disqualifier** | Any why answered with "because I wanted to learn" or "because it's cool" without deeper exploration of how that learning/coolness serves others |

**Bedrock Needs Reference:**
- **Autonomy** - Control over one's time, decisions, or workflow
- **Time** - Getting time back, faster results, reduced waiting
- **Money** - Earning more, saving money, reducing costs
- **Status** - Recognition, credibility, professional advancement
- **Connection** - Relationships, communication, collaboration
- **Safety** - Security, risk reduction, peace of mind

## Scoring

**PASS conditions:**
- Final answer in chain is one of the six bedrock needs
- Each why in the chain builds logically on the previous answer
- Chain demonstrates clear path from technical implementation to human value

**BORDERLINE conditions:**
- Chain reaches 5 iterations with logical progression
- Final answer is close to bedrock but not quite there (e.g., "efficiency" instead of "time")
- Apply tie-breaker: Can you take one more step to reach true bedrock? If yes → FAIL, if no → PASS

**FAIL conditions:**
- Chain stops at technical implementation ("makes API calls easier")
- Chain loops (why 4 repeats why 2)
- Cannot articulate more than 3 iterations
- Any iteration breaks logical causation

## Edge Cases

- **"Learning motivation"** - If why includes learning, must drill to: Why does that learning matter? Who benefits from your increased skill? See EDGE-CASES.md for full protocol.
- **"Cool technology"** - Similar to learning; must drill to utility beyond personal interest
- **Multiple paths** - If project serves multiple needs, auditor should trace the PRIMARY user path (most common use case)

**Template Variables:**
- `gate1_status` - Pass/Fail emoji (✅/❌)
- `why1` - Answer to first why
- `why2` - Answer to second why
- `why3` - Answer to third why
- `why4` - Answer to fourth why
- `why5` - Answer to fifth why
- `bedrock_need` - The fundamental human need identified
- `gate1_verdict` - Text explanation of pass/fail decision

**See Also:** `/wisdom-clarify` skill (5 Whys questioning)

---

## Gate 2: The Inversion Test (Via Negativa)

*"What would happen if this didn't exist?"*

**Questions:**
- Would anyone notice it was gone?
- What would they use instead?
- How much worse would their life be?

**Pass criteria:** Clear, specific negative consequences from non-existence

**Fail indicators:**
- "They'd use [equivalent tool] with minor inconvenience"
- No one would notice
- Convenience-only value

## Objective Rubric

**Measurement Protocol:**

1. Identify what users would use instead if this didn't exist
2. Measure the switching cost (time to switch + data loss + recurring costs)
3. If switching cost is low, apply quantitative test (time/cost savings vs alternative)

**Switching Cost Thresholds:**

| Alternative Exists | Switching Cost | Verdict |
|-------------------|----------------|---------|
| Yes | ≤5 minutes to switch + no data loss | **FAIL** (convenience only) |
| Yes | 1-4 hours to switch + minimal data loss | **BORDERLINE** (apply quantitative test) |
| Yes | >4 hours to switch OR significant data loss OR recurring cost avoided | **PASS** |
| No | N/A - no alternative exists | **PASS** |

**Quantitative Test (for BORDERLINE cases):**

Calculate: `(Time saved per use × Use frequency) + (Cost saved)` vs `Switching cost`

- If this project saves >20% time OR >20% cost compared to alternative → **PASS**
- If savings <20% → **FAIL**

**Examples:**
- Alternative exists, takes 30 seconds to type equivalent command → FAIL (trivial switch cost)
- Alternative exists, requires 2 hours to migrate config files → BORDERLINE → Apply test: Does this save >2.4 hours over reasonable usage period?
- Alternative exists, but requires $10/month subscription → PASS (recurring cost avoided)

## Scoring

**PASS conditions:**
- No alternative exists that delivers the same value
- Alternative exists but switching cost is >4 hours
- Alternative exists but requires data loss or migration pain
- Alternative exists but has recurring costs this project eliminates
- Borderline case where quantitative test shows >20% savings

**FAIL conditions:**
- Alternative exists with ≤5 minute switching cost
- Only benefit is minor convenience ("saves me typing 2 flags")
- Borderline case where quantitative test shows <20% savings
- Users wouldn't notice if it disappeared

## Edge Cases

- **"Minor inconvenience" boundary** - Use the 5-minute rule: If switching takes ≤5 minutes total, it's minor. See EDGE-CASES.md for gray areas.
- **Learning curves** - Don't count learning time for domain-standard tools (e.g., learning SQL doesn't count against SQL alternative)
- **Emotional cost** - Only count if tied to measurable outcome (e.g., "frustration" doesn't count, but "context-switching breaks flow state, loses 15 min/day" does)

**Template Variables:**
- `gate2_status` - Pass/Fail emoji (✅/❌)
- `inversion_notice` - Would anyone notice if it was gone?
- `inversion_alternative` - What would they use instead?
- `inversion_severity` - How severe are the consequences?
- `gate2_verdict` - Text explanation of pass/fail decision

**See Also:** `/wisdom-stoic-premeditation` skill (Via negativa/inversion)

---

## Gate 3: The 10-Minute Test

Can a new user get meaningful value within 10 minutes of first contact?

**Measure:**
1. Time to discover the project
2. Time to understand what it does
3. Time to install/setup
4. Time to first successful use
5. Time to "aha moment"

**Pass criteria:** Total ≤10 minutes, ideally ≤5

**Fail indicators:**
- Requires pre-requisites user doesn't have
- Complex configuration before first value
- Documentation gaps that force exploration

## Objective Rubric

**Measurement Protocol:**

Simulate a new user's journey from zero to aha moment. Start timer at README open, stop when real value is demonstrated. Sum all phases:

1. **Discovery** (0 if starting from README) - Time to find the project
2. **Comprehension** - Time to understand what it does and what value it provides
3. **Installation** - Time to install, including pre-requisites if project-specific
4. **First Use** - Time to run first successful command/action
5. **Aha Moment** - Time to see concrete value demonstrated (not just "it worked")

**Time Thresholds:**

| Total Time | Verdict |
|------------|---------|
| ≤10 minutes | **PASS** |
| 10-15 minutes | **BORDERLINE** |
| >15 minutes | **FAIL** |
| Any time without aha moment | **FAIL** (time irrelevant if no value shown) |

**Pre-requisite Timing Rules:**

| Pre-requisite Type | Count Time? |
|-------------------|-------------|
| Domain-standard (Node for JS project, Python for Python project) | **NO** - Exclude from timing |
| Common dev tools (git, curl, npm) if target is developers | **NO** - Assume present |
| Project-specific (Redis, Docker, specific API keys) | **YES** - Include full setup time |
| Uncommon (<30% of target users have it) | **YES** - Include installation time |

**Aha Moment Definition:**
Not just "it installed successfully" or "command ran." Must demonstrate tangible value:
- CLI tool: Shows actual useful output, not just help text
- API: Returns real data, not just 200 OK
- Library: Demo/example produces visible result
- Automation: Completes real task, not just logs "task started"

## Scoring

**PASS conditions:**
- Total time ≤10 minutes
- Aha moment reached (real value demonstrated)
- Path is reproducible (not lucky guesswork)

**BORDERLINE conditions:**
- Total time 10-15 minutes with clear aha moment
- Apply tie-breaker: Would a motivated user continue past 10 minutes to get this value? If yes → PASS, if no → FAIL

**FAIL conditions:**
- Total time >15 minutes
- No aha moment reached at any time
- Requires uncommon pre-requisites without guidance
- Documentation gaps force trial-and-error
- Setup succeeds but unclear what value was delivered

## Edge Cases

- **Pre-requisite timing** - If 30-50% of users have it, apply 50% weight to installation time. See EDGE-CASES.md for detailed decision tree.
- **Multiple paths** - Test the "happy path" (recommended/documented approach), not theoretical minimums
- **API keys** - If requires signup/approval, count actual wait time (can be hours/days) unless instant approval
- **"Works on my machine"** - Must test from clean environment or VM, not from your configured dev setup

**Template Variables:**
- `gate3_status` - Pass/Fail emoji (✅/❌)
- `time_discovery` - Time to discover project
- `notes_discovery` - Notes on discovery phase
- `time_comprehension` - Time to understand what it does
- `notes_comprehension` - Notes on comprehension phase
- `time_install` - Time to install/setup
- `notes_install` - Notes on installation phase
- `time_first_use` - Time to first successful use
- `notes_first_use` - Notes on first use
- `time_aha` - Time to "aha moment"
- `notes_aha` - Notes on aha moment
- `time_total` - Total time for all phases
- `gate3_verdict` - Text explanation of pass/fail decision

---

## Gate 4: The Bartender Test

Explain the value in ONE sentence to a non-technical person.

**Bad examples:**
- "A Claude Code skill that generates self-contained HTML presentations with Three.js animations and brand profile support"
- "A distributed caching layer with eventual consistency guarantees"

**Good examples:**
- "You describe what you want, it makes you a presentation you own forever"
- "It remembers your data so your app runs faster"

**Pass criteria:** Immediate comprehension, no follow-up questions needed

**Fail indicators:**
- Requires technical jargon
- Describes implementation, not value
- More than one sentence

## Objective Rubric

**Measurement Protocol:**

Craft a single-sentence explanation of the project's value. Test against a 4-point binary checklist:

**Checklist:**

| Check | Pass Criteria | Jargon Reference |
|-------|---------------|------------------|
| 1. Zero jargon | Contains NO technical terms a non-technical person wouldn't know | See jargon list below |
| 2. Value focus | Describes outcome/benefit, NOT implementation/features | Ask: "So what?" after reading |
| 3. Single sentence | One sentence only (compound clauses with "and" are OK, but not multiple independent sentences with separate subjects) | Count periods |
| 4. No questions | Listener understands immediately without needing clarification | Test: Would your grandmother need to ask "what's that?" |

**Jargon List (Non-exhaustive):**

❌ **Technical jargon:** API, CLI, SDK, schema, endpoint, deploy, distributed, caching, framework, library, repository, database, server, authentication, webhook, REST, GraphQL, microservice, containerized, orchestration

✅ **Acceptable terms:** email, website, file, folder, app, program, internet, online, search, click, type, save, send, receive, password, account, settings

**Scoring Formula:**
- All 4 checks = YES → **PASS**
- Any check = NO → **FAIL**

## Scoring

**PASS conditions:**
- All 4 checklist items = YES
- Sentence would make sense to bartender, grandmother, or Uber driver
- No follow-up questions needed to understand the value

**FAIL conditions:**
- Any technical jargon present (even one term)
- Describes HOW it works instead of WHAT value it provides
- Uses multiple sentences (even if connected with semicolon or "and then")
- Requires domain knowledge to understand

**Tie-breaker test (if unsure):**
Read the sentence to someone outside tech. If they say "okay, but what does that mean?" → FAIL

## Edge Cases

- **Domain-specific terms** - If target users are technical (developers, sysadmins), can use domain terms ONLY if unavoidable AND they know it (e.g., "git" to developers is OK). See EDGE-CASES.md for boundary cases.
- **Compound sentences** - "It does X and Y" is one sentence. "It does X. It also does Y." is two sentences → FAIL
- **Acronyms** - Treat all acronyms as jargon unless universally known (PDF, USB are OK; API, CLI are not)
- **"Tech-adjacent" terms** - Terms like "app" or "website" are acceptable; terms like "web app" or "SaaS" are jargon

**Template Variables:**
- `gate4_status` - Pass/Fail emoji (✅/❌)
- `bartender_sentence` - The one-sentence explanation
- `bartender_jargon` - Does it use jargon? (Yes/No)
- `bartender_value` - Does it describe value not implementation? (Yes/No)
- `bartender_immediate` - Is understanding immediate? (Yes/No)
- `gate4_verdict` - Text explanation of pass/fail decision

---

## Gate 5: The Wallet Test

Two questions:
1. Would YOU pay $20/month for this?
2. Can you name 3 specific people who would pay?

**Pass criteria:**
- Honest "yes" to both
- Real names, not archetypes like "developers" or "content creators"

**Fail indicators:**
- "Well, it's free so..."
- Can only name hypothetical user types
- Wouldn't pay because you already have Claude/other tool

**Template Variables:**
- `gate5_status` - Pass/Fail emoji (✅/❌)
- `wallet_self` - Would YOU pay $20/month? (Yes/No)
- `wallet_self_reason` - Reasoning for your answer
- `wallet_person1` - First specific person (real name)
- `wallet_person2` - Second specific person (real name)
- `wallet_person3` - Third specific person (real name)
- `gate5_verdict` - Text explanation of pass/fail decision

---

## Gate 6: The Job-to-be-Done

*"What job is the user hiring this product to do?"*

**Format:** `When [situation], I want to [motivation], so I can [outcome]`

**Good example:**
"When I need to present an idea to stakeholders, I want to create a professional deck without learning design tools, so I can focus on my message not the medium"

**Pass criteria:** Clear situation → motivation → outcome chain

**Fail indicators:**
- Vague situation ("when I need to do X")
- Missing motivation or outcome
- Multiple unrelated jobs

**Template Variables:**
- `gate6_status` - Pass/Fail emoji (✅/❌)
- `jtbd_situation` - The situation/context
- `jtbd_motivation` - What they want to do
- `jtbd_outcome` - What they want to achieve
- `jtbd_specific` - Is the situation specific? (Yes/No)
- `jtbd_motivation_clear` - Is the motivation clear? (Yes/No)
- `jtbd_outcome_clear` - Is the outcome tangible? (Yes/No)
- `gate6_verdict` - Text explanation of pass/fail decision

---

## Gate 7: The Friction Audit

Map every step between "I have a need" and "need is satisfied":

| Step | Question |
|------|----------|
| 1. Discovery | How do they find it? |
| 2. Comprehension | How do they understand what it does? |
| 3. Installation | How do they get it running? |
| 4. First Use | How do they get first value? |
| 5. Repeated Use | How do they integrate into workflow? |
| 6. Mastery | How do they get full value? |

**Pass criteria:** No step takes longer than the value it unlocks

**Fail indicators:**
- Discovery: Not findable (not published, bad SEO)
- Installation: Complex dependencies or configuration
- First Use: Multiple steps before any value

**Template Variables:**
- `gate7_status` - Pass/Fail emoji (✅/❌)
- `friction_discovery` - Friction level for discovery (Low/Medium/High)
- `friction_discovery_issue` - Specific issue if any
- `friction_comprehension` - Friction level for comprehension (Low/Medium/High)
- `friction_comprehension_issue` - Specific issue if any
- `friction_install` - Friction level for installation (Low/Medium/High)
- `friction_install_issue` - Specific issue if any
- `friction_first` - Friction level for first use (Low/Medium/High)
- `friction_first_issue` - Specific issue if any
- `friction_repeated` - Friction level for repeated use (Low/Medium/High)
- `friction_repeated_issue` - Specific issue if any
- `friction_mastery` - Friction level for mastery (Low/Medium/High)
- `friction_mastery_issue` - Specific issue if any
- `friction_highest` - The highest friction point identified
- `gate7_verdict` - Text explanation of pass/fail decision

---

## Gate 8: The Second-Order Consequences

*"What happens AFTER they use this successfully?"*

**Positive second-order:**
- They tell others (viral potential)
- They build on top of it (ecosystem)
- They come back for more (retention)

**Negative second-order:**
- They're locked into your thing now
- They have to maintain something
- They've created a dependency

**Pass criteria:** Positive consequences outweigh negative

**Fail indicators:**
- Creates lock-in worse than alternatives
- Maintenance burden exceeds value
- One-time use with no retention

**Template Variables:**
- `gate8_status` - Pass/Fail emoji (✅/❌)
- `second_viral` - Viral potential assessment
- `second_ecosystem` - Ecosystem potential assessment
- `second_retention` - Retention potential assessment
- `second_lockin` - Lock-in concerns assessment
- `second_maintenance` - Maintenance burden assessment
- `second_dependencies` - Dependency concerns assessment
- `second_net` - Net second-order consequences (Positive/Negative/Neutral)
- `gate8_verdict` - Text explanation of pass/fail decision

**See Also:** `/taches-cc-resources:consider:second-order` skill

---

## Gate 9: Occam's Razor

*"Is this the simplest solution to the problem?"*

**Questions:**
- Could you solve this with fewer features?
- Is there a simpler tool that already exists?
- Are you over-engineering?
- What could you remove and still solve the core problem?

**Pass criteria:** No simpler solution exists that delivers the same value

**Fail indicators:**
- A bash script could do 80% of this
- You built a framework when a function would do
- Features exist "just in case"

**Template Variables:**
- `gate9_status` - Pass/Fail emoji (✅/❌)
- `occam_fewer` - Could fewer features work? (Yes/No)
- `occam_simpler` - Does a simpler tool exist? (Yes/No)
- `occam_overengineered` - Is this over-engineered? (Yes/No)
- `occam_essential` - Is it essential complexity only? (Yes/No)
- `gate9_verdict` - Text explanation of pass/fail decision

**See Also:** `/taches-cc-resources:consider:occams-razor` skill

---

## Gate 10: The Pareto Gate

*"Does 20% of the features deliver 80% of the value?"*

**Questions:**
- What's the ONE feature that matters most?
- What could you cut and nobody would notice?
- What's the MVP that still passes the 10-minute test?

**Pass criteria:** Core value is concentrated, not spread thin

**Fail indicators:**
- Can't identify the one essential feature
- "All features are equally important"
- Feature creep without usage data

**Template Variables:**
- `gate10_status` - Pass/Fail emoji (✅/❌)
- `pareto_one_feature` - The ONE feature that matters most
- `pareto_cuttable` - Features that could be cut
- `pareto_concentration` - Assessment of value concentration
- `gate10_verdict` - Text explanation of pass/fail decision

**See Also:** `/taches-cc-resources:consider:pareto` skill

---

## Gate 11: Regret Minimization

*"Will you regret NOT building this in 10 years?"*

**Bezos framework:** Project yourself to age 80. Look back. Will you regret:
- Not trying this?
- Spending time on this instead of something else?

**Pass criteria:** Future-you would regret NOT shipping this

**Fail indicators:**
- Building to avoid FOMO
- Scratching a temporary itch
- Resume-driven development

**Template Variables:**
- `gate11_status` - Pass/Fail emoji (✅/❌)
- `regret_answer` - Would you regret NOT shipping? (Yes/No)
- `regret_reasoning` - Reasoning for the answer
- `gate11_verdict` - Text explanation of pass/fail decision

---

## Template Variable Summary

This section provides a quick reference for all template variables used in the audit report template.

### Gate 1 (8 variables)
gate1_status, why1, why2, why3, why4, why5, bedrock_need, gate1_verdict

### Gate 2 (5 variables)
gate2_status, inversion_notice, inversion_alternative, inversion_severity, gate2_verdict

### Gate 3 (13 variables)
gate3_status, time_discovery, notes_discovery, time_comprehension, notes_comprehension, time_install, notes_install, time_first_use, notes_first_use, time_aha, notes_aha, time_total, gate3_verdict

### Gate 4 (6 variables)
gate4_status, bartender_sentence, bartender_jargon, bartender_value, bartender_immediate, gate4_verdict

### Gate 5 (7 variables)
gate5_status, wallet_self, wallet_self_reason, wallet_person1, wallet_person2, wallet_person3, gate5_verdict

### Gate 6 (8 variables)
gate6_status, jtbd_situation, jtbd_motivation, jtbd_outcome, jtbd_specific, jtbd_motivation_clear, jtbd_outcome_clear, gate6_verdict

### Gate 7 (14 variables)
gate7_status, friction_discovery, friction_discovery_issue, friction_comprehension, friction_comprehension_issue, friction_install, friction_install_issue, friction_first, friction_first_issue, friction_repeated, friction_repeated_issue, friction_mastery, friction_mastery_issue, friction_highest, gate7_verdict

### Gate 8 (9 variables)
gate8_status, second_viral, second_ecosystem, second_retention, second_lockin, second_maintenance, second_dependencies, second_net, gate8_verdict

### Gate 9 (6 variables)
gate9_status, occam_fewer, occam_simpler, occam_overengineered, occam_essential, gate9_verdict

### Gate 10 (5 variables)
gate10_status, pareto_one_feature, pareto_cuttable, pareto_concentration, gate10_verdict

### Gate 11 (4 variables)
gate11_status, regret_answer, regret_reasoning, gate11_verdict

**Total: 85 template variables across 11 gates**
