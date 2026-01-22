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
