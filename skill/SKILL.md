---
name: rfu-audit
description: Run the 11-gate Really Fucking Useful gauntlet to validate project utility before investing significant effort
user-invocable: true
---

# RFU Audit: Really Fucking Useful

Systematic framework to validate project utility BEFORE investing significant effort. Most open source projects fail not because of bad code, but because they solve problems nobody has.

## Philosophy

Developers build what's interesting to *them*, not what's valuable to *users*. The RFU gauntlet forces you to prove utility through 11 rigorous gates before shipping.

**Pass all 11 gates = RFU Certified**

## Usage

```
/rfu-audit [project-path]
/rfu-audit ~/Dev/my-project
```

If no path provided, audits the current working directory.

## The 11 Gates

### Gate 1: The 5 Whys of Utility

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

---

### Gate 2: The Inversion Test (Via Negativa)

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

---

### Gate 3: The 10-Minute Test

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

---

### Gate 4: The Bartender Test

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

---

### Gate 5: The Wallet Test

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

---

### Gate 6: The Job-to-be-Done

*"What job is the user hiring this product to do?"*

**Format:** `When [situation], I want to [motivation], so I can [outcome]`

**Good example:**
"When I need to present an idea to stakeholders, I want to create a professional deck without learning design tools, so I can focus on my message not the medium"

**Pass criteria:** Clear situation → motivation → outcome chain

**Fail indicators:**
- Vague situation ("when I need to do X")
- Missing motivation or outcome
- Multiple unrelated jobs

---

### Gate 7: The Friction Audit

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

---

### Gate 8: The Second-Order Consequences

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

---

### Gate 9: Occam's Razor

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

---

### Gate 10: The Pareto Gate

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

---

### Gate 11: Regret Minimization

*"Will you regret NOT building this in 10 years?"*

**Bezos framework:** Project yourself to age 80. Look back. Will you regret:
- Not trying this?
- Spending time on this instead of something else?

**Pass criteria:** Future-you would regret NOT shipping this

**Fail indicators:**
- Building to avoid FOMO
- Scratching a temporary itch
- Resume-driven development

---

## Scoring

| Score | Rating | Action |
|-------|--------|--------|
| 11/11 | RFU Certified | Ship it |
| 9-10 | Almost There | Fix specific gaps, then ship |
| 6-8 | Promising | Needs significant work |
| 0-5 | Reconsider | Build something else |

## Process

When auditing a project:

1. **Read project files**: README, package.json, main source files
2. **Walk through each gate sequentially**
3. **Score each gate**: Pass (1) or Fail (0)
4. **Provide evidence** for each score
5. **Generate prioritized fix recommendations**
6. **Output structured report**

## Integration with Wisdom Skills

This skill integrates concepts from:
- `/wisdom-clarify` - 5 Whys questioning
- `/wisdom-stoic-premeditation` - Via negativa/inversion
- `/taches-cc-resources:consider:second-order` - Second-order consequences
- `/taches-cc-resources:consider:pareto` - 80/20 analysis
- `/taches-cc-resources:consider:occams-razor` - Simplicity check

## Output

Generate a structured audit report using the template in `templates/audit-report.md`.

## Related Skills

- `/think-first` - Decision framework
- `/wisdom-ground` - Ground in philosophical frameworks
- `/prep-repo` - Prepare repo for publishing (after RFU passes)
