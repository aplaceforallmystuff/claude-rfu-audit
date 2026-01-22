# RFU Audit: Really Fucking Useful

**Stop building things nobody wants.**

A Claude Code skill that validates project utility through an 11-gate gauntlet. Pass all gates = ship it. Fail too many = build something else.

## The Problem

Most open source projects fail not because of bad code, but because they solve problems nobody has. Developers build what's interesting to *them*, not what's valuable to *users*.

RFU forces you to prove utility BEFORE investing significant effort.

## The 11 Gates

| # | Gate | Core Question |
|---|------|---------------|
| 1 | **5 Whys** | What bedrock human need does this serve? |
| 2 | **Inversion** | What if this didn't exist? |
| 3 | **10-Minute** | Can users get value in 10 min? |
| 4 | **Bartender** | One sentence explanation? |
| 5 | **Wallet** | Would 3 real people pay? |
| 6 | **JTBD** | What job does this do? |
| 7 | **Friction** | What's between user and value? |
| 8 | **Second-Order** | What happens after success? |
| 9 | **Occam's Razor** | Is this the simplest solution? |
| 10 | **Pareto** | Does 20% deliver 80% value? |
| 11 | **Regret** | Will you regret NOT building this? |

## Scoring

| Score | Rating | Action |
|-------|--------|--------|
| 11/11 | RFU Certified | Ship it |
| 9-10 | Almost There | Fix specific gaps |
| 6-8 | Promising | Needs significant work |
| 0-5 | Reconsider | Build something else |

## Installation

### Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed

### Install the Skill

```bash
git clone https://github.com/aplaceforallmystuff/claude-rfu-audit.git
cd claude-rfu-audit
bash install.sh
```

Or manually copy:

```bash
cp -r skill ~/.claude/skills/rfu-audit
```

## Usage

```bash
# Audit a project
/rfu-audit ~/Dev/my-project

# Audit current directory
/rfu-audit
```

The skill will:
1. Read your project's README, package.json, and source files
2. Walk through each gate with targeted questions
3. Score each gate Pass/Fail with evidence
4. Generate a prioritized fix list

## Example Output

```markdown
## RFU Audit Report

**Project:** SlideForge
**Score: 7/11** — Promising, needs work

### Gate Results

| Gate | Status |
|------|--------|
| 5 Whys | ✅ |
| Inversion | ✅ |
| 10-Minute | ❌ |
| Bartender | ✅ |
| Wallet | ❌ |
| ...

### Priority Fixes

1. **Publish to GitHub** — Currently unfindable
2. **Simplify Installation** — Too many prerequisites
3. **Add Demo GIF** — Show don't tell
```

## Gate Details

### Gate 1: The 5 Whys of Utility

Drill down until you hit bedrock human need.

```
Why does this exist?
→ Why do people need that?
→ Why can't they use existing tools?
→ Why does that matter?
→ Why is that important?
→ BEDROCK: [Fundamental need]
```

**Pass criteria:** Reaches a fundamental need (autonomy, time, money, status, connection, safety)

### Gate 2: Inversion Test (Via Negativa)

*"What would happen if this didn't exist?"*

- Would anyone notice?
- What would they use instead?
- How much worse would their life be?

**Pass criteria:** Clear, specific negative consequences from non-existence

### Gate 3: 10-Minute Test

Can a new user get meaningful value within 10 minutes?

- Time to discover
- Time to understand
- Time to install
- Time to first use
- Time to "aha moment"

**Pass criteria:** Total ≤10 minutes

### Gate 4: Bartender Test

Explain the value in ONE sentence to a non-technical person.

**Bad:** "A distributed caching layer with eventual consistency guarantees"
**Good:** "It remembers your data so your app runs faster"

**Pass criteria:** Immediate comprehension, no follow-up questions

### Gate 5: Wallet Test

1. Would YOU pay $20/month for this?
2. Can you name 3 specific people (not archetypes) who would pay?

**Pass criteria:** Honest yes to both

### Gate 6: Job-to-be-Done

*"What job is the user hiring this product to do?"*

Format: `When [situation], I want to [motivation], so I can [outcome]`

**Pass criteria:** Clear situation → motivation → outcome chain

### Gate 7: Friction Audit

Map every step from "I have a need" to "need is satisfied":

1. Discovery — How do they find it?
2. Comprehension — How do they understand it?
3. Installation — How do they get it running?
4. First Use — How do they get first value?
5. Repeated Use — How do they integrate it?
6. Mastery — How do they get full value?

**Pass criteria:** No step takes longer than the value it unlocks

### Gate 8: Second-Order Consequences

*"What happens AFTER they use this successfully?"*

**Positive:** Viral spread, ecosystem, retention
**Negative:** Lock-in, maintenance burden, dependencies

**Pass criteria:** Positive outweighs negative

### Gate 9: Occam's Razor

*"Is this the simplest solution to the problem?"*

- Could fewer features work?
- Does a simpler tool already exist?
- Are you over-engineering?

**Pass criteria:** No simpler solution delivers the same value

### Gate 10: Pareto Gate

*"Does 20% of the features deliver 80% of the value?"*

- What's the ONE feature that matters?
- What could you cut?
- What's the MVP?

**Pass criteria:** Core value is concentrated

### Gate 11: Regret Minimization

*"Will you regret NOT building this in 10 years?"*

Bezos framework: Project to age 80. Look back.

**Pass criteria:** Future-you would regret NOT shipping

## Philosophy

RFU integrates proven mental models:

- **5 Whys** — Toyota's root cause analysis
- **Via Negativa** — Nassim Taleb's "improve by removing"
- **JTBD** — Clayton Christensen's Jobs to be Done
- **Pareto Principle** — Vilfredo Pareto's 80/20 rule
- **Regret Minimization** — Jeff Bezos's decision framework

The goal isn't to kill ideas — it's to validate them quickly so you can either fix gaps or move on to something better.

## License

MIT

## Author

[Jim Christian](https://jimchristian.net)
