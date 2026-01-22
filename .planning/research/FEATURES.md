# Feature Landscape: Audit/Validation Frameworks

**Domain:** Project audit and validation tools
**Researched:** 2026-01-22
**Confidence:** LOW (based on training knowledge only; WebSearch unavailable)

## Executive Summary

Effective audit tools bridge the gap between mechanical checking and human judgment. The difference between a useful audit and a frustrating checklist comes down to three pillars:

1. **Clarity** - Can auditors reach consistent conclusions?
2. **Actionability** - Does the output tell you what to fix, or just what failed?
3. **Efficiency** - Can you get quick feedback without hours of work?

Poor audit tools check boxes. Great audit tools guide improvement.

---

## Table Stakes

Features users expect from any audit/validation tool. Missing these = product feels incomplete.

| Feature | Why Expected | Complexity | Notes |
|---------|--------------|------------|-------|
| **Clear pass/fail criteria** | Eliminates subjective interpretation | Low | Must be specific enough that two auditors reach same conclusion |
| **Specific failure messages** | Generic "this failed" is useless | Low | Must identify exactly what's wrong: "Missing README.md" not "Documentation insufficient" |
| **Exit codes** | Scripts/CI need programmatic success/fail | Low | 0 = pass, non-zero = fail with severity codes |
| **Human-readable output** | Auditors are humans making decisions | Low | Not just machine-parseable JSON |
| **Deterministic results** | Same input = same output | Low | Random or inconsistent scoring destroys trust |
| **Examples of pass/fail** | Shows what good/bad looks like | Medium | Concrete examples prevent misinterpretation |
| **Error messages** | Handle bad input gracefully | Low | "File not found" not uncaught exceptions |
| **Progress indicators** | Long audits need feedback | Low | "Processing gate 3/11..." prevents abandonment |
| **Severity levels** | Not all failures are equal | Medium | Critical vs. warning vs. info |
| **Summary before details** | TL;DR at top, details below | Low | Inverted pyramid: verdict first, evidence after |

### Evidence from Training Data

Common patterns in successful tools:
- **ESLint**: Clear rule definitions, auto-fixable flags, severity levels
- **TypeScript**: Specific error locations, concrete fix suggestions
- **Lighthouse**: Numeric scores + actionable recommendations
- **npm audit**: Severity levels (low/medium/high/critical)

---

## Differentiators

Features that set great audit tools apart. Not expected, but highly valued.

| Feature | Value Proposition | Complexity | Notes |
|---------|-------------------|------------|-------|
| **Auto-fix suggestions** | "Run this command to fix" vs. "this failed" | Medium | ESLint --fix pattern; may not apply to all gates |
| **Contextual examples** | Show pass/fail examples FROM YOUR PROJECT | High | "Your README says X, but users need Y" |
| **Quick triage mode** | Fast feedback before full audit commitment | Low | 5 core gates = 80% signal in 20% time |
| **Progressive disclosure** | Summary → scorecard → details → evidence | Medium | Let users drill down, don't force full report read |
| **Comparison over time** | "You fixed X since last audit" | Medium | Requires audit history tracking |
| **Priority ranking** | Which failures to fix first | Medium | Impact vs. effort matrix |
| **Fix integration** | Link failures to tools that help | Low | "Run /prep-repo to fix discovery issues" |
| **Edge case handling** | Explicit guidance for gray areas | Medium | "If X and Y, then consider it a pass" |
| **Pre-populated context** | Auto-extract from README/docs | Medium | Saves manual data entry, reduces friction |
| **Partial completion** | Save progress, resume later | High | Long audits interrupted = lost work |
| **Custom thresholds** | Allow pass at 9/11 vs. requiring 11/11 | Low | Different projects have different bars |
| **Confidence scoring** | "This gate is LOW confidence judgment" | Medium | Flags subjective gates needing more thought |

### What Makes These Differentiating

**Auto-fix suggestions**: Most linters identify problems. Great ones fix them automatically.
- ESLint `--fix` flag
- Prettier's opinionated formatting
- `npm audit fix` for security issues

**Quick triage mode**: Lighthouse has "Mobile" vs. "Desktop" modes; security tools have "quick scan" vs. "deep scan"
- Gives instant go/no-go without full investment
- RFU context: Gates 1, 3, 4, 5, 9 = minimal viable triage

**Priority ranking**: Accessibility checkers show impact (A/AA/AAA conformance)
- Not all failures matter equally
- Fix order = maximize improvement per hour spent

**Pre-populated context**: Package managers auto-detect project type
- npm recognizes package.json
- pip recognizes requirements.txt
- Reduces "fill out this form" friction

---

## Anti-Features

Features to explicitly NOT build. Common mistakes in audit tool design.

| Anti-Feature | Why Avoid | What to Do Instead |
|--------------|-----------|-------------------|
| **Vague criteria** | "Is the documentation good?" | "Does README contain installation steps?" |
| **Score inflation** | Everything passes = scores meaningless | Be honest; failing is useful information |
| **Generic advice** | "Improve your README" | "Add installation instructions with code examples" |
| **Binary-only output** | Just show pass/fail score | Show evidence: "Failed because X, Y, Z" |
| **Blame-focused language** | "You failed to..." | "This project lacks..." (focus on project, not person) |
| **Checklist theater** | 100 gates that nobody reads | 11 focused gates with clear rationale |
| **Hidden assumptions** | Fails without explaining why | Make evaluation logic explicit |
| **Jargon in output** | "Non-orthogonal concerns" | "These features overlap and confuse users" |
| **Wall of text** | 10-page audit report | Summary + drill-down structure |
| **False precision** | "Score: 7.234/11" | "Score: 7/11" (false precision destroys trust) |
| **Audit-only mode** | Check boxes, no improvement path | Link failures to remediation resources |
| **One-size-fits-all** | Same criteria for all project types | Allow threshold/scope customization |
| **Surprise failures** | Criteria appear after you fail | Show all criteria upfront |

### Why These Are Harmful

**Vague criteria**: Different auditors reach different conclusions. Destroys consistency and credibility.

**Score inflation**: If everything passes, the audit has no signal. Honest failure is more valuable than false success.

**Generic advice**: "Make it better" is useless. Specificity = actionability.

**Checklist theater**: Security compliance checklists with 200 items = nobody reads them, boxes get checked randomly. Small focused checklists get taken seriously.

**Jargon**: Audits require clarity. Technical jargon excludes non-experts and obscures meaning.

---

## Feature Dependencies

```
Clear criteria → Deterministic results
                → Examples of pass/fail
                → Contextual examples

Auto-fix suggestions → Fix integration
                     → Actionable output

Quick triage → Partial completion
             → Progressive disclosure

Pre-populated context → Auto-analyze
                      → Reduced friction

Comparison over time → Audit history
                     → Progress tracking

Priority ranking → Severity levels
                 → Impact analysis
```

### Critical Path

**Minimum viable audit tool:**
1. Clear pass/fail criteria (without this, nothing else matters)
2. Specific failure messages (actionability)
3. Human-readable output (usability)
4. Examples of pass/fail (learning)
5. Summary before details (efficiency)

**Enhancement path:**
1. Quick triage mode (efficiency)
2. Pre-populated context (reduced friction)
3. Priority ranking (actionability)
4. Fix integration (improvement path)
5. Comparison over time (progress visibility)

---

## Feature Complexity Assessment

### Low Complexity (Quick Wins)

- **Specific error messages**: Replace "failed" with "missing installation instructions in README"
- **Exit codes**: Return 0 for pass, 1 for fail
- **Summary structure**: Put verdict at top of output
- **Progress indicators**: "Evaluating gate 3/11..."
- **Fix integration**: Add "Run /prep-repo to fix" links

### Medium Complexity (Standard Features)

- **Quick triage mode**: Define 5-gate subset, run only those
- **Priority ranking**: Calculate impact score, sort failures
- **Edge case handling**: Document "if X then Y" logic per gate
- **Pre-populated context**: Parse README for project description
- **Comparison over time**: Save reports to `.planning/audits/`, diff on rerun

### High Complexity (Future Enhancement)

- **Contextual examples**: Extract relevant sections from project files to show in output
- **Partial completion**: Serialize audit state, resume from checkpoint
- **Confidence scoring**: Meta-evaluate judgment certainty per gate
- **Custom thresholds**: Allow project-specific pass criteria
- **Interactive mode**: Prompt for clarification on ambiguous gates

---

## MVP Recommendation

For initial enhancement of RFU audit tool, prioritize:

### Phase 1: Clarity (Core Value)
1. **Gate clarity improvements** - Add concrete examples, edge cases, gray area handling to SKILL.md
2. **Failed audit examples** - Show what FAIL looks like for each gate
3. **Specific failure messages** - Replace generic verdicts with precise failures

### Phase 2: Efficiency (Reduced Friction)
4. **Quick audit mode** - 5-gate triage (Gates 1, 3, 4, 5, 9)
5. **Auto-analyze mode** - Pre-populate from README/package.json
6. **Progress indicators** - Show gate progress during execution

### Phase 3: Actionability (Improvement Path)
7. **Priority matrix** - Show which gates to fix first
8. **Fix integration** - Link failures to relevant skills/tools
9. **Comparison over time** - Track audit history in `.planning/audits/`

### Defer to Post-MVP

- **Interactive mode**: Adds complexity, may not be needed if pre-population works well
- **Partial completion**: Only needed if audits take >30 minutes; optimize speed first
- **Custom thresholds**: Wait for user demand; premature flexibility creates confusion
- **Confidence scoring**: Meta-feature; focus on core gates first

---

## Implementation Notes for RFU Audit

### Quick Mode Gate Selection

**Why these 5 gates?**
- Gate 1 (5 Whys): Tests fundamental utility
- Gate 3 (10-Minute): Tests practical usability
- Gate 4 (Bartender): Tests clarity of value prop
- Gate 5 (Wallet): Tests market validation
- Gate 9 (Occam's Razor): Tests against over-engineering

**What they cover:**
- Utility (1)
- Usability (3)
- Clarity (4)
- Market fit (5)
- Simplicity (9)

**What they miss:**
- Gate 2 (Inversion): Covered implicitly by Gate 1
- Gate 6 (JTBD): Covered by Gates 1 + 4
- Gate 7 (Friction): Covered by Gate 3
- Gate 8 (Second-Order): Later-stage concern
- Gate 10 (Pareto): Covered by Gate 9
- Gate 11 (Regret): Personal motivation, less critical

### Auto-Analyze Extraction Points

From README.md:
- Title → Project name
- First paragraph → Bartender test candidate
- "Installation" section → 10-minute test input
- "Usage" examples → First-use friction

From package.json:
- `description` → Value proposition
- `dependencies` → Complexity analysis
- `scripts` → Installation friction

From file structure:
- README exists? → Discovery friction
- Example/demo directory? → 10-minute test support
- Test directory? → Quality signal

### Priority Matrix Logic

**Impact calculation:**
```
High impact gates:
- Gate 1 (5 Whys): No utility = no point
- Gate 5 (Wallet): No market = no users
- Gate 3 (10-Minute): Can't use it = won't use it

Medium impact gates:
- Gate 4 (Bartender): Communication matters
- Gate 7 (Friction): Affects adoption
- Gate 9 (Occam's Razor): Affects maintenance

Low impact gates (for mature projects):
- Gate 8 (Second-Order): Long-term concern
- Gate 10 (Pareto): Optimization phase
- Gate 11 (Regret): Personal motivation
```

**Effort estimation:**
- Gate 3 (10-Minute): Often quick fix (improve docs)
- Gate 4 (Bartender): Quick (rewrite value prop)
- Gate 7 (Friction): Medium (reduce steps)
- Gate 1 (5 Whys): Hard (may require pivot)
- Gate 5 (Wallet): Hard (may require different problem)

**Priority = High impact + Low effort first**

---

## Comparative Analysis

### ESLint Pattern (Code Linting)

**What works:**
- Specific rule violations with line numbers
- Auto-fix for mechanical issues
- Severity levels (error/warning)
- Incremental adoption (fix one rule at a time)

**What RFU can adopt:**
- Gate-specific failure reasons (not just "failed")
- Severity levels (critical gates vs. nice-to-have)
- Incremental improvement (focus on 1-2 gates per iteration)

**What doesn't translate:**
- Auto-fix (judgment can't be automated)
- Line numbers (no code locations)

### Lighthouse Pattern (Performance Auditing)

**What works:**
- Numeric score with color coding
- Separate scores per category (Performance, Accessibility, SEO)
- Concrete recommendations with priority
- Before/after comparison

**What RFU can adopt:**
- Numeric score (X/11)
- Category breakdown (already have 11 gates)
- Prioritized recommendations (fix these first)
- Historical comparison (track over time)

**What doesn't translate:**
- Automated measurement (RFU requires human judgment)
- Continuous monitoring (one-time audit model)

### npm audit Pattern (Security Auditing)

**What works:**
- Severity levels (low/moderate/high/critical)
- Affected packages clearly identified
- Fix suggestions with commands
- Summary statistics

**What RFU can adopt:**
- Severity per gate (which failures matter most)
- Affected areas (which parts of project fail gate)
- Fix commands (link to skills that help)
- Summary stats (gates passed/failed by category)

**What doesn't translate:**
- Automated fixes (judgment required)
- Dependency tree analysis (not code-based)

---

## Research Gaps

Due to WebSearch unavailability, the following remain LOW confidence and should be validated:

1. **Current best practices in audit tool UX (2026)**: Are there new patterns since my training cutoff?
2. **User research on checklist fatigue**: What's the optimal number of criteria before users disengage?
3. **Comparative tool analysis**: How do newer tools (post-2024) handle these problems?
4. **Domain-specific audit tools**: Are there project validation tools specifically for indie developers/solopreneurs?

---

## Sources

**Confidence: LOW** - All findings based on training knowledge of:
- ESLint (code linting)
- TypeScript compiler (type checking)
- Lighthouse (performance/accessibility auditing)
- npm audit (security auditing)
- Accessibility checkers (WCAG compliance)

**Verification needed**: Current best practices, recent tool innovations, domain-specific patterns.

**Recommendation**: When WebSearch becomes available, verify:
- "audit tool UX best practices 2026"
- "validation framework design patterns 2026"
- "project validation tools for indie developers 2026"
- "checklist design usability research 2026"
