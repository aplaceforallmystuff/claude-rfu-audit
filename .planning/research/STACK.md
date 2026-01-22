# Technology Stack: Claude Code Skill Enhancement

**Project:** RFU Audit Enhancement
**Researched:** 2026-01-22
**Domain:** Claude Code skill design for consistent LLM-based evaluation
**Overall Confidence:** HIGH

## Executive Summary

Claude Code skills are markdown-based declarative specifications that guide Claude's reasoning engine. Unlike traditional software with executable code, skills are **prompt architectures** — structured instructions that shape LLM behavior. The "technology stack" for enhancing RFU Audit consists of:

1. **Prompt engineering patterns** for consistent evaluation
2. **Structured output formats** that reduce variance
3. **Self-documentation techniques** that make skills maintainable
4. **Template validation approaches** for quality control
5. **Cognitive forcing functions** that prevent common LLM failure modes

No external libraries or frameworks are needed. The entire enhancement lives in markdown files that guide Claude's native capabilities.

---

## Recommended Stack

### Core Framework

| Technology | Version | Purpose | Why |
|------------|---------|---------|-----|
| Markdown | CommonMark | Skill definition format | Native to Claude Code, human-readable, version-controllable |
| Template Variables | `{{var}}` syntax | Output structure | Already in use, familiar to users, simple interpolation |
| Claude's Reasoning Engine | Sonnet 4.5+ | Execution environment | No choice — this is the runtime for all Claude Code skills |

### Prompt Engineering Patterns

| Pattern | Purpose | When to Use |
|---------|---------|-------------|
| **Rubric-Based Evaluation** | Define scoring criteria with concrete examples | All 11 gates — especially Gates 5, 10, 11 (currently vague) |
| **Forced Binary Choices** | Eliminate "maybe" responses that create scoring variance | Gate pass/fail decisions |
| **Concrete Anchors** | Provide reference examples for each score level | Gate pass/fail indicators |
| **Chain-of-Thought Elicitation** | Require reasoning before scoring | All gates — prevents snap judgments |
| **Negative Examples** | Show what FAIL looks like, not just PASS | Each gate's fail indicators |
| **Edge Case Handling** | Document gray areas and resolution rules | Borderline gate decisions |

### Self-Documentation Techniques

| Technique | Purpose | Implementation |
|-----------|---------|----------------|
| **Inline Examples** | Show expected behavior within skill definition | Add "Good example" / "Bad example" sections to each gate |
| **Meta-Commentary** | Explain WHY a gate matters | Add "Why This Gate" sections |
| **Decision Trees** | Handle ambiguous cases systematically | "If X, then score Y; if Z, then score W" |
| **Variable Glossary** | Document all template variables | Create `TEMPLATE_SPEC.md` (referenced in CONCERNS.md) |
| **Consistency Checks** | Cross-validate gates for logical coherence | Add "Gate Dependencies" section showing correlations |

### Template Validation Approaches

| Approach | Purpose | Confidence |
|----------|---------|------------|
| **Required Fields Check** | Ensure all critical variables populated | HIGH (simple pattern matching) |
| **Markdown Escaping** | Prevent user input from breaking report format | HIGH (sanitize variables) |
| **Type Hints** | Document expected variable types (string, emoji, number) | HIGH (specification, not runtime check) |
| **Output Schema** | Define expected structure for programmatic parsing | MEDIUM (requires JSON alternative) |

### Cognitive Forcing Functions

| Function | Purpose | Implementation |
|----------|---------|----------------|
| **Pre-Scoring Checklist** | Prevent rushed judgments | Add "Before scoring this gate, confirm you have..." section |
| **Evidence Collection** | Require specific proof before pass/fail | Add "Evidence required" to each gate |
| **Comparative Anchoring** | Reference previous audits for calibration | Store example audits in skill documentation |
| **Auditor State Documentation** | Capture context affecting subjective gates | Add auditor profile fields (experience, motivation) |
| **Conflict Resolution Rules** | Handle contradictory gate results | Add "If Gate X passes but Gate Y fails, then..." logic |

---

## Supporting Tools & Patterns

### Structured Prompting Architecture

**Problem:** Vague gates (5, 10, 11) lead to inconsistent scoring.

**Solution:** Rubric-based evaluation pattern.

**Implementation:**

```markdown
### Gate 5: Wallet Test

**Scoring Rubric:**

| Score | Criteria | Evidence Required |
|-------|----------|-------------------|
| PASS | 1. You would personally pay $20/month, AND 2. You can name 3 specific people (real names, not archetypes), AND 3. You've validated willingness (asked them OR have strong evidence) | Names with context: "Sarah Chen, my colleague at Acme Corp, currently pays for similar tool X" |
| FAIL | Any of: 1. You wouldn't pay, OR 2. Can only name archetypes ("developers"), OR 3. Zero validation | Generic answers: "SaaS founders would pay" |
| GRAY AREA RESOLUTION | If you'd pay but can only name 2 people → FAIL (must be 3) | Document as "2/3 validation — needs one more" |

**Examples:**

**PASS Example:**
- Self: Yes, I'd pay $20/month because I currently waste 2 hours/week on X
- Person 1: Jane Doe (jane@startup.com) — told me last week she'd pay $50 for this
- Person 2: John Smith (founder of TechCo) — currently uses inferior tool, confirmed interest
- Person 3: Sarah Johnson (freelance designer) — piloted prototype, said she'd pay

**FAIL Example:**
- Self: Well, it's free so I'd use it, but probably wouldn't pay
- Person 1: Developers
- Person 2: Content creators
- Person 3: Small business owners

**Edge Case: Open-Source Projects**
If project is intentionally free/open-source, reframe question:
"Would you donate $20/month to this if it was the only way to keep it maintained?"
```

### Self-Documenting Skill Pattern

**Problem:** Skills written months ago become opaque; authors forget reasoning.

**Solution:** Embedded meta-commentary.

**Implementation:**

```markdown
### Gate 1: The 5 Whys of Utility

**Why This Gate Exists:**
Developers build what's interesting to them, not what's valuable to users. The 5 Whys forces you to drill past "because it's cool" to bedrock human need (autonomy, time, money, status, connection, safety).

**What Makes This Gate Fragile:**
The chain can stop at surface-level technical benefits ("it's faster") without reaching fundamental needs. Example: "Why does speed matter?" → "Users hate waiting" → "Why?" → "Autonomy: they control their time."

**Common Failure Mode:**
Circular reasoning: "Why does this exist?" → "To solve problem X" → "Why?" → "Because X is a problem" (this restates the first answer).

**How to Avoid:**
Each "why" must reveal a NEW layer. If you're repeating yourself, push deeper or restart from a different angle.
```

### Chain-of-Thought Elicitation Pattern

**Problem:** LLMs can score gates without showing reasoning, hiding mistakes.

**Solution:** Force explicit reasoning before scoring.

**Implementation:**

```markdown
### Gate 3: 10-Minute Test

**Evaluation Process:**

1. **Collect Data First** (before scoring)
   - [ ] Read README: How does user discover what this does?
   - [ ] Check installation docs: How many steps? What dependencies?
   - [ ] Review "Getting Started": Is there a quick start guide?
   - [ ] Estimate time for each phase (discovery, comprehension, install, first use, aha moment)

2. **Calculate Total Time**
   - Discovery: ___ minutes
   - Comprehension: ___ minutes
   - Installation: ___ minutes
   - First Use: ___ minutes
   - Aha Moment: ___ minutes
   - **TOTAL: ___ minutes**

3. **Apply Scoring Logic**
   - If TOTAL ≤ 5 minutes: PASS (ideal)
   - If TOTAL ≤ 10 minutes: PASS (acceptable)
   - If TOTAL > 10 minutes: FAIL

4. **Document Bottlenecks**
   - Which phase took longest?
   - What could be optimized?
   - Are prerequisites missing?

**Only after steps 1-4 are complete can you score the gate.**
```

### Negative Example Documentation Pattern

**Problem:** Gates only show PASS criteria; auditors don't recognize FAIL clearly.

**Solution:** Document failure patterns explicitly.

**Implementation:**

```markdown
### Gate 4: Bartender Test

**PASS Examples:**
- "You describe what you want, it makes you a presentation you own forever"
- "It remembers your data so your app runs faster"
- "Turns your GitHub stars into a personal CRM"

**FAIL Examples:**
- "A Claude Code skill that generates self-contained HTML presentations with Three.js animations and brand profile support" (too technical, describes implementation)
- "A distributed caching layer with eventual consistency guarantees" (jargon, no human value)
- "It's like Notion meets Figma but for developers" (requires knowing two products, vague analogy)
- "You know how you always have that problem where X? This fixes it" (two sentences, requires setup)

**Gray Area — Resolution:**
If sentence is comprehensible but requires follow-up question ("Wait, what does it present?"), it's a FAIL. Bartender test requires IMMEDIATE understanding, zero questions.
```

### Edge Case Handling Pattern

**Problem:** Real projects hit edge cases not covered by gate definitions.

**Solution:** Document known edge cases and resolution logic.

**Implementation:**

```markdown
### Gate 9: Occam's Razor

**Standard Case:**
If a simpler tool exists that does 80% of the job, FAIL unless your tool adds critical value.

**Edge Case 1: Library vs. Framework**
If your project is a framework (e.g., a validation library), the question becomes "could this be a set of functions instead of a framework?"
- Resolution: PASS if the framework provides essential abstractions (e.g., plugin system, extension points)
- FAIL if it's just wrapped functions with extra ceremony

**Edge Case 2: Internal Tools**
If the project is internal (not public), the bar is lower — "simpler" means "works for our team" not "works for everyone."
- Resolution: Reframe question: "Could we solve this with existing internal tools?" If no, PASS.

**Edge Case 3: Learning Projects**
If the goal is "I wanted to learn X," the project automatically FAILS Occam's Razor — learning goals don't optimize for simplicity.
- Resolution: FAIL, but document as "learning project — not intended for production use."
```

### Input Validation Pattern

**Problem:** Skills assume valid input; missing README or bad paths cause cryptic errors.

**Solution:** Explicit error handling documentation.

**Implementation:**

```markdown
## Input Validation

Before starting the audit, check:

**Required Files:**
- [ ] README.md exists and is readable
- [ ] Project path is valid directory
- [ ] At least one source file exists (to understand what project does)

**If Missing README:**
- Stop audit, output: "Cannot audit — no README.md found. The Bartender Test (Gate 4) requires a clear value statement, which should be in README."

**If Invalid Path:**
- Stop audit, output: "Cannot audit — path does not exist or is not readable: [path]"

**If Empty Project:**
- Stop audit, output: "Cannot audit — no source files detected. Projects must have implementation to evaluate 10-Minute Test (Gate 3)."

**Minimal Viable Project for Audit:**
1. README with value statement
2. Installation/setup instructions
3. At least one example or demo
```

### Quick Audit Mode Pattern

**Problem:** Full 11-gate audit takes 1-2 hours; need faster triage.

**Solution:** Define subset of gates for rapid evaluation.

**Implementation:**

```markdown
## Quick Audit Mode

For rapid triage (15 minutes), audit only these 5 gates:

| Gate | Why Essential |
|------|---------------|
| Gate 1 (5 Whys) | If it doesn't reach bedrock need, stop |
| Gate 3 (10-Minute) | If it's too hard to use, stop |
| Gate 4 (Bartender) | If you can't explain it simply, stop |
| Gate 5 (Wallet) | If no one would pay, stop |
| Gate 9 (Occam's Razor) | If simpler tool exists, stop |

**Quick Scoring:**
- 5/5: Proceed to full 11-gate audit
- 3-4/5: Promising but needs fixes; full audit optional
- 0-2/5: Stop — project is not viable; fix core issues before full audit

**When to Use:**
- First pass on new project idea (before investing time)
- Re-audit after major changes (quick check before full audit)
- Portfolio triage (evaluating multiple projects quickly)

**Invocation:**
```
/rfu-audit --quick [project-path]
```
```

### Auto-Analyze Mode Pattern

**Problem:** Manual data collection is slow; Claude can pre-populate some fields.

**Solution:** Read project files and suggest answers.

**Implementation:**

```markdown
## Auto-Analyze Mode

When invoked with `--auto-analyze`, the skill:

1. **Reads Project Files**
   - README.md (for value statement, installation, features)
   - package.json / Cargo.toml / pyproject.toml (for dependencies, description)
   - CHANGELOG.md (for feature evolution)
   - GitHub repo metadata (stars, issues, contributors — if applicable)

2. **Pre-Populates Gate Context**
   - **Gate 1 (5 Whys):** Extract value statement from README
   - **Gate 3 (10-Minute):** Count installation steps, identify dependencies
   - **Gate 4 (Bartender):** Extract tagline or description
   - **Gate 7 (Friction):** Identify if published (npm, GitHub), count setup steps
   - **Gate 9 (Occam's Razor):** List features, suggest cuttable ones

3. **Flags Manual Judgment Required**
   - Gates 5, 8, 11 require human judgment — auto-analyze cannot answer these
   - Output: "Auto-analyzed 6/11 gates. Please manually evaluate: Wallet, Second-Order, Regret."

4. **Output Format**
   - Show suggested answers for each gate
   - Mark confidence level (HIGH for factual data, LOW for inferred)
   - Allow auditor to override all suggestions

**Invocation:**
```
/rfu-audit --auto-analyze [project-path]
```

**Limitations:**
- Cannot validate if people would pay (Gate 5) — requires human interviews
- Cannot predict second-order effects (Gate 8) — requires domain knowledge
- Cannot assess regret (Gate 11) — purely personal
```

---

## Alternatives Considered

| Category | Recommended | Alternative | Why Not |
|----------|-------------|-------------|---------|
| Output Format | Template variables (`{{var}}`) | JSON schema with validation | Templates are human-readable; JSON adds complexity without clear benefit for Claude Code skills |
| Scoring System | Binary (Pass/Fail) | 5-point scale (0-5 per gate) | Binary forces clarity; scales introduce "3/5" middle-ground that hides problems |
| Skill Architecture | Markdown skill definition | Executable Python/TypeScript | Claude Code skills are declarative by design; code would require separate runtime |
| Template Validation | Documentation + type hints | Runtime validator script | Skills are stateless; validation would need to run outside Claude, adding friction |
| Quick Audit | 5-gate subset | Weighted scoring (some gates more important) | Subset is clearer; weights introduce subjectivity |
| Auto-Analyze | Claude reads files natively | External API for project analysis | Claude already has file reading; external API adds dependency |

---

## Patterns to Follow

### Pattern 1: Rubric-Driven Evaluation

**What:** Define explicit scoring criteria with examples for each possible score.

**When:** Any gate that requires judgment (especially Gates 5, 10, 11).

**Example:**

```markdown
**Scoring Rubric:**
| Score | Criteria | Example |
|-------|----------|---------|
| PASS | [specific criteria] | [concrete example] |
| FAIL | [specific criteria] | [concrete example] |
```

**Why:** Eliminates ambiguity; two auditors using the same rubric should score similarly.

### Pattern 2: Pre-Scoring Evidence Collection

**What:** Require auditors to collect specific evidence BEFORE scoring.

**When:** All gates, but especially data-driven ones (Gate 3, Gate 7).

**Example:**

```markdown
**Before Scoring:**
- [ ] Checklist item 1
- [ ] Checklist item 2
- [ ] Checklist item 3

**Only after checklist is complete can you score.**
```

**Why:** Prevents snap judgments; forces thorough evaluation.

### Pattern 3: Negative Example Documentation

**What:** Show what FAIL looks like, not just PASS.

**When:** All gates.

**Example:**

```markdown
**PASS Examples:**
- [Good example 1]
- [Good example 2]

**FAIL Examples:**
- [Bad example 1]
- [Bad example 2]
```

**Why:** Auditors learn from both success and failure patterns.

### Pattern 4: Edge Case Codification

**What:** Document known edge cases and resolution logic.

**When:** Gates with ambiguous scenarios (open-source projects, learning projects, internal tools).

**Example:**

```markdown
**Edge Case: Open-Source Projects**
- Resolution: [specific handling]

**Edge Case: Learning Projects**
- Resolution: [specific handling]
```

**Why:** Prevents inconsistent handling of non-standard projects.

### Pattern 5: Self-Documentation Commentary

**What:** Embed explanations of WHY gates exist and common failure modes.

**When:** All gates (especially those that feel arbitrary).

**Example:**

```markdown
**Why This Gate Exists:** [rationale]
**Common Failure Mode:** [pattern to avoid]
**How to Avoid:** [specific guidance]
```

**Why:** Makes skills maintainable; future authors understand reasoning.

---

## Anti-Patterns to Avoid

### Anti-Pattern 1: Vague Criteria

**What:** Scoring criteria like "good," "clear," "reasonable" without definition.

**Why Bad:** Introduces subjectivity; two auditors will interpret differently.

**Instead:** Use concrete thresholds ("≤10 minutes," "3 specific names," "one sentence").

### Anti-Pattern 2: Implementation-Driven Gates

**What:** Gates that require code inspection rather than user-facing evaluation.

**Why Bad:** RFU framework evaluates utility, not code quality. Implementation details are irrelevant if the product solves a problem.

**Instead:** Focus on user experience, value delivery, and market validation.

### Anti-Pattern 3: One-Sided Examples

**What:** Only showing PASS examples, no FAIL examples.

**Why Bad:** Auditors don't recognize failure patterns clearly.

**Instead:** Document both PASS and FAIL for every gate.

### Anti-Pattern 4: Hidden Assumptions

**What:** Gates that assume project type (SaaS, CLI, library) without stating it.

**Why Bad:** Some gates don't apply universally (Gate 5 for open-source, Gate 7 for internal tools).

**Instead:** Document edge cases and how to handle non-standard projects.

### Anti-Pattern 5: No Cross-Gate Validation

**What:** Gates scored independently without checking logical consistency.

**Why Bad:** Contradictory results (pass Gate 1 but fail Gate 5) suggest scoring error.

**Instead:** Add "Gate Dependencies" section showing expected correlations.

---

## Installation & Configuration

### Skill Enhancement Approach

**No external dependencies required.** All enhancements are markdown edits to:

```
skill/SKILL.md              # Gate definitions with rubrics, examples, edge cases
skill/templates/audit-report.md  # Output template (existing)
skill/TEMPLATE_SPEC.md      # New file: variable documentation
skill/EXAMPLES.md           # New file: full audit examples (pass, fail, borderline)
```

### Implementation Checklist

To enhance the skill for consistency and reproducibility:

- [ ] Add rubrics to Gates 5, 10, 11 (currently vague)
- [ ] Add FAIL examples to all 11 gates
- [ ] Document edge cases (open-source, learning projects, internal tools)
- [ ] Create TEMPLATE_SPEC.md documenting all `{{variables}}`
- [ ] Create EXAMPLES.md with 3 full audit examples
- [ ] Add pre-scoring checklists to all gates
- [ ] Add "Why This Gate" commentary to all gates
- [ ] Define quick audit mode (5-gate subset)
- [ ] Define auto-analyze mode (file reading logic)
- [ ] Add input validation (missing README, bad path handling)
- [ ] Add gate dependency matrix (expected correlations)
- [ ] Add conflict resolution rules (contradictory gate results)

---

## Sources & Confidence Levels

| Finding | Source | Confidence |
|---------|--------|------------|
| Template variable syntax | Existing `skill/templates/audit-report.md` | HIGH (observed) |
| Gate vagueness issues | `.planning/codebase/CONCERNS.md` | HIGH (documented issue) |
| No FAIL examples | `.planning/codebase/CONCERNS.md` | HIGH (confirmed gap) |
| Rubric-based evaluation pattern | LLM evaluation research (training data) | HIGH (established practice) |
| Chain-of-thought prompting | LLM reasoning research (training data) | HIGH (proven technique) |
| Binary scoring vs. scales | Decision theory (training data) | MEDIUM (domain practice) |
| Quick audit mode recommendation | Time-to-value heuristic | MEDIUM (informed judgment) |
| Auto-analyze feasibility | Claude's native file reading | HIGH (existing capability) |

**Key Training Data Limitations:**
- No Claude Code-specific skill design documentation found in training (skill system is relatively new)
- Prompt engineering patterns are from general LLM research, adapted to skill context
- Best practices derived from software testing, decision frameworks, and cognitive psychology

**Verification Needed:**
- Claude Code's official skill design guidelines (if they exist)
- User studies on audit consistency (not available)
- Inter-rater reliability data for existing gates (not collected)

---

*Stack research: 2026-01-22*
