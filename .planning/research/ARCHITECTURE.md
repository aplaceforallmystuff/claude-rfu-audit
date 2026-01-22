# Architecture Patterns for Complex Claude Code Skills

**Domain:** Claude Code skill structure for multi-step evaluation frameworks
**Researched:** 2026-01-22
**Confidence:** HIGH (based on existing codebase analysis and software architecture principles)

## Executive Summary

Claude Code skills are declarative, markdown-based instruction sets that guide Claude's reasoning. For complex multi-step evaluation skills like RFU Audit, architecture must balance three concerns:

1. **Cognitive load on Claude** — Keep instructions clear and parseable
2. **Maintainability** — Enable additions/changes without breaking existing logic
3. **User clarity** — Make the skill self-documenting and consistent

The current single-file structure (SKILL.md + single template) works for v1 but will not scale for the planned enhancements (examples, quick mode, auto-analyze). This research recommends a **modular skill architecture** that separates concerns into focused files while maintaining the stateless, markdown-based nature of Claude Code skills.

## Recommended Architecture

### File Organization Pattern

```
skill/
├── SKILL.md                    # Core manifest and orchestration logic
├── config/
│   ├── gates-full.md          # Complete 11-gate definitions
│   ├── gates-quick.md         # 5-gate quick mode definitions
│   └── scoring-rules.md       # Scoring bands and interpretation
├── guides/
│   ├── GATE-EXAMPLES.md       # Concrete pass/fail examples per gate
│   ├── EDGE-CASES.md          # Gray area handling guidelines
│   ├── AUTO-ANALYZE.md        # Project file parsing heuristics
│   └── INPUT-VALIDATION.md    # Error handling patterns
├── templates/
│   ├── audit-report.md        # Full 11-gate report template
│   ├── audit-report-quick.md  # Quick mode report template
│   └── audit-history.md       # Re-audit comparison template
└── reference/
    ├── FAILED-AUDITS.md       # Example audits that scored 0-5
    └── TEMPLATE-SPEC.md       # Variable documentation for templates
```

**Rationale:** This structure separates **what to do** (SKILL.md orchestration) from **how to evaluate** (gates), **what good/bad looks like** (examples), and **output format** (templates). Each file has a single responsibility.

### Component Boundaries

| Component | Responsibility | Communicates With |
|-----------|---------------|-------------------|
| **SKILL.md** | Skill metadata, invocation flow, mode selection, high-level orchestration | All other files (references them) |
| **config/gates-*.md** | Gate definitions: questions, pass criteria, fail indicators | GATE-EXAMPLES.md, EDGE-CASES.md |
| **guides/GATE-EXAMPLES.md** | Concrete examples of pass/fail for each gate | config/gates-*.md |
| **guides/EDGE-CASES.md** | Gray area resolution rules, borderline scoring guidance | config/gates-*.md |
| **guides/AUTO-ANALYZE.md** | Heuristics for pre-populating gate context from project files | SKILL.md (orchestration) |
| **guides/INPUT-VALIDATION.md** | Error handling for missing/bad project files | SKILL.md (orchestration) |
| **templates/*.md** | Output format specifications with variable placeholders | SKILL.md (selects template based on mode) |
| **reference/FAILED-AUDITS.md** | Real-world failed audit examples | GATE-EXAMPLES.md (complements) |
| **reference/TEMPLATE-SPEC.md** | Variable documentation for template authors | templates/*.md |

## Patterns to Follow

### Pattern 1: Skill Orchestration via SKILL.md

**What:** SKILL.md is the entry point. It contains skill metadata (name, description, invocable flag) and orchestrates the audit flow by referencing other files.

**When:** Always. SKILL.md is required by Claude Code skill system.

**Structure:**

```markdown
---
name: rfu-audit
description: [description]
user-invocable: true
---

# RFU Audit Skill

## Usage
/rfu-audit [path] [--quick]

## Process

When invoked:

1. **Validate input** (see guides/INPUT-VALIDATION.md)
2. **Select mode** (full 11-gate vs quick 5-gate)
3. **Read project files** (README, package.json, source)
4. **Auto-analyze** (optional, see guides/AUTO-ANALYZE.md)
5. **Execute gates**:
   - Full mode: Apply all gates from config/gates-full.md
   - Quick mode: Apply gates from config/gates-quick.md
   - Reference guides/GATE-EXAMPLES.md for scoring clarity
   - Reference guides/EDGE-CASES.md for borderline situations
6. **Score results** using config/scoring-rules.md
7. **Generate report** using appropriate template from templates/
8. **Return structured output**

## Modes

### Full Mode (default)
All 11 gates. Use for final validation before shipping.
[Reference to config/gates-full.md]

### Quick Mode (--quick flag)
5 gates for rapid triage. Use for initial screening.
[Reference to config/gates-quick.md]

## Related Skills
[Integration references]
```

**Why:** Keeps the orchestration logic separate from evaluation details. Claude reads SKILL.md first, then follows references to specialized files as needed.

### Pattern 2: Modular Gate Definitions

**What:** Separate gate definitions from the main skill file. Create focused files for full vs quick mode gates.

**When:** When you have multiple evaluation modes or when gate documentation exceeds ~150 lines.

**Structure for config/gates-full.md:**

```markdown
# RFU Audit: Full Gate Definitions

## Gate 1: The 5 Whys of Utility

**Core Question:** What bedrock human need does this serve?

**Process:**
[drilling down methodology]

**Pass Criteria:**
- Reaches fundamental need (autonomy, time, money, status, connection, safety)
- Clear causal chain from feature to need
- No circular reasoning

**Fail Indicators:**
- Chain stops at technical reasons
- Can't articulate user benefit
- "Because it's cool" or "I wanted to learn"

**Template Variables:**
- gate1_status (✅ or ❌)
- why1, why2, why3, why4, why5
- bedrock_need
- gate1_verdict (text explanation)

**See Also:**
- guides/GATE-EXAMPLES.md#gate-1 for concrete examples
- guides/EDGE-CASES.md#gate-1 for gray area handling

---

## Gate 2: Inversion Test (Via Negativa)
[similar structure]
```

**Why:** This allows you to maintain two gate sets (full/quick) without duplication. Each gate definition is self-contained with clear pass/fail criteria and template variable mapping.

### Pattern 3: Example-Driven Clarity

**What:** Create a separate examples file showing concrete pass/fail cases for each gate, rather than embedding examples in gate definitions.

**When:** When gates are subjective (Gates 5, 10, 11) or when you need to show what FAIL looks like.

**Structure for guides/GATE-EXAMPLES.md:**

```markdown
# Gate Examples: Pass/Fail Reference

## Gate 1: 5 Whys

### Example: PASS (SlideForge)

**Chain:**
```
Why does SlideForge exist?
→ To create presentations without design tools

Why do people need that?
→ Design tools (Figma, Sketch) have steep learning curves

Why can't they use PowerPoint/Keynote?
→ Default templates look corporate; customization still requires design skills

Why does that matter?
→ Bad design undermines credibility of the message

Why is that important?
→ BEDROCK: Status/credibility in professional context
```

**Verdict:** PASS — Reaches bedrock need (status).

### Example: FAIL (DevOps Dashboard)

**Chain:**
```
Why does this exist?
→ To show Kubernetes metrics in real-time

Why do people need that?
→ So they can monitor their cluster

Why can't they use Grafana?
→ Grafana requires configuration

Why does that matter?
→ Configuration is annoying

Why is that important?
→ [Cannot articulate deeper need]
```

**Verdict:** FAIL — Stops at convenience, never reaches bedrock.

---

## Gate 5: Wallet Test

### Example: PASS

**Would you pay $20/month?** YES — I currently pay for Canva Pro ($12.99/mo) and would switch.

**3 specific people:**
1. Sarah Chen (marketing manager at Acme Corp) — creates investor decks monthly
2. David Rodriguez (freelance consultant) — needs proposals for clients weekly
3. Lisa Park (teacher) — makes classroom presentations

**Verdict:** PASS — Real people with clear use cases.

### Example: FAIL (vague archetypes)

**Would you pay?** Maybe, depends on features.

**3 people:**
1. "Developers who need to present"
2. "Content creators"
3. "Anyone making slides"

**Verdict:** FAIL — These are user types, not specific people.

### Example: BORDERLINE

**Would you pay?** YES — I'd pay to save time.

**3 people:**
1. My colleague Tom (uses Google Slides now)
2. My friend who's a designer (but already has Figma)
3. My brother who teaches (but only presents twice a year)

**Verdict:** BORDERLINE → Check guides/EDGE-CASES.md#gate-5

[Continues for all 11 gates]
```

**Why:** Separates examples from definitions, making both easier to maintain. Examples can be expanded without cluttering the gate logic. Users can quickly scan for "what does FAIL look like" without re-reading gate theory.

### Pattern 4: Edge Case Resolution Guide

**What:** Explicit guidance for handling gray areas, contradictory evidence, or borderline gates.

**When:** When gates are subjective or when you need consistency across multiple audits of similar projects.

**Structure for guides/EDGE-CASES.md:**

```markdown
# Edge Case Handling Guide

## Gate 5: Wallet Test — Borderline Cases

### Scenario: Would pay, but only 2 specific people
**Resolution:** FAIL. Gate requires 3 people. This indicates narrow market.
**Rationale:** If you can only name 2, demand is too thin.

### Scenario: Specific people who "might" pay
**Resolution:** FAIL unless you've actually asked them.
**Rationale:** "Might pay" is projection, not validation.

### Scenario: Named people, but weak use cases
Example: "My friend Tom uses Google Slides but rarely complains"
**Resolution:** FAIL. They need to have a strong pain point, not just "use similar tool."
**Rationale:** No switching motivation = won't pay.

### Scenario: Would pay yourself, but for different reasons than target users
Example: "I'd pay to learn the tech, but users would pay to save time"
**Resolution:** Conditional PASS if the 3 people have the target user motivation.
**Rationale:** Your motivation doesn't matter; user motivation does.

---

## Gate 10: Pareto — No Dominant Feature

### Scenario: Product has 3 equally important features
**Resolution:** FAIL.
**Rationale:** Pareto gate checks if value is concentrated. Equal importance = diffuse value = fails Pareto principle.

### Scenario: Feature A is 60% of value, Features B+C are 40%
**Resolution:** PASS. 20% delivering 60% is close enough to 80/20.
**Rationale:** Pareto is a heuristic, not exact math.

---

## Gate 11: Regret Minimization — Auditor Context Matters

### Scenario: Junior developer (2 years experience) vs senior (20 years)
**Resolution:** Note auditor context in report. Scores may differ legitimately.
**Rationale:** Career stage affects regret tolerance. Junior dev may regret NOT exploring; senior may regret wasting time.

### Scenario: Side project vs full-time commitment
**Resolution:** Adjust risk tolerance. Side projects can be more exploratory.
**Rationale:** Regret from a failed side project << regret from a failed startup.

[Continues for common edge cases across all gates]
```

**Why:** Prevents score inflation by defining boundaries. Two auditors applying this guide should score similarly on borderline cases.

### Pattern 5: Auto-Analyze Heuristics

**What:** Documented heuristics for pre-populating gate context from project files, without replacing human judgment.

**When:** To accelerate audits by extracting known information from README, package.json, etc.

**Structure for guides/AUTO-ANALYZE.md:**

```markdown
# Auto-Analyze Mode: Project File Parsing Heuristics

## Purpose
Read project files to pre-populate gate context and suggest answers. This accelerates the audit but does NOT replace human judgment.

## Files to Read

| File | Extract | Use For |
|------|---------|---------|
| README.md | Title, description, installation steps, features list | Gates 3, 4, 6, 7, 10 |
| package.json | Name, version, description, dependencies count | Gates 3, 9 |
| GitHub repo metadata | Stars, forks, issues count, last commit date | Gates 2, 7, 8 |
| LICENSE | License type | Gate 8 (second-order) |
| CHANGELOG.md | Version history, feature additions | Gate 10 (feature evolution) |

## Heuristics by Gate

### Gate 1: 5 Whys
- **Extract:** README description, "Why this exists" section if present
- **Suggest:** Start the why chain with README value prop
- **Limitation:** Cannot complete the chain; requires human drilling

### Gate 3: 10-Minute Test
- **Extract:** Installation section from README
- **Estimate:**
  - Discovery: If published on npm/GitHub = 2min, unpublished = 10min (auto-fail)
  - Comprehension: Count words in description, estimate 200 wpm reading
  - Installation: Count steps in install section (1 step = 1min estimate)
  - First use: Check if README has "Quick Start" or "Usage" with example
- **Output:** "Estimated 10-minute test: 7 minutes (may vary)"
- **Limitation:** Estimate only; human must verify by actually trying

### Gate 4: Bartender Test
- **Extract:** First sentence of README description or package.json description
- **Evaluate:**
  - Contains jargon? (match against tech term list)
  - Single sentence? (check for period count)
  - Describes value? (heuristic: contains "you", "your", benefit words)
- **Suggest:** "Auto-detected one-liner: [sentence]. Passes jargon check: YES/NO."
- **Limitation:** Cannot judge "immediate comprehension"; human must assess

### Gate 7: Friction Audit
- **Extract:**
  - Discovery: Check if package on npm (search npmjs.com)
  - Installation: Parse installation steps from README
  - First use: Check for "Quick Start" section
- **Estimate friction levels:**
  - Low: <3 steps
  - Medium: 3-5 steps
  - High: >5 steps or prerequisites
- **Output:** "Detected friction: Installation = Medium (4 steps)"
- **Limitation:** Cannot assess comprehension or mastery friction

### Gate 10: Pareto
- **Extract:** Features list from README (look for bullets, numbered lists)
- **Count:** Number of features mentioned
- **Suggest:** "Detected features: [list]. Suggest identifying which is dominant."
- **Limitation:** Cannot determine relative value; human must judge

## Implementation Notes

When auto-analyze mode is invoked:

1. Read project files silently (don't show to user unless requested)
2. Apply heuristics per gate
3. Pre-populate report template with "Auto-detected: [finding]" markers
4. Present to user for validation/override
5. User confirms or corrects each gate before finalizing

**Critical:** Auto-analyze is a starting point, not a final score. All gates require human judgment.
```

**Why:** Makes auto-analyze mode transparent and auditable. Documents what can/cannot be automated, preventing over-reliance on heuristics.

### Pattern 6: Template Variable Documentation

**What:** Explicit documentation of all template variables, their expected types, and usage context.

**When:** When templates have 30+ variables or when multiple templates exist (full/quick/history).

**Structure for reference/TEMPLATE-SPEC.md:**

```markdown
# Template Variable Specification

## audit-report.md (Full Report)

### Metadata Variables

| Variable | Type | Example | Required | Default |
|----------|------|---------|----------|---------|
| project_name | string | "SlideForge" | Yes | N/A |
| project_path | string | "~/Dev/slideforge" | Yes | N/A |
| audit_date | date | "2026-01-22" | Yes | Current date |
| score | integer (0-11) | 7 | Yes | N/A |
| rating | string | "Promising" | Yes | Derived from score |
| summary | text (2-3 paragraphs) | "This project..." | Yes | N/A |

### Gate 1 Variables

| Variable | Type | Example | Required | Context |
|----------|------|---------|----------|---------|
| gate1_status | emoji | ✅ or ❌ | Yes | Pass/Fail indicator |
| why1 | text | "To create presentations easily" | Yes | First why response |
| why2 | text | "Design tools are complex" | Yes | Second why response |
| why3 | text | "PowerPoint requires design skills" | Yes | Third why response |
| why4 | text | "Bad design undermines credibility" | Yes | Fourth why response |
| why5 | text | "Credibility affects career outcomes" | Yes | Fifth why response |
| bedrock_need | string | "Status" | Yes | One of: autonomy, time, money, status, connection, safety |
| gate1_verdict | text (1-2 sentences) | "Passes because chain reaches status need clearly" | Yes | Explanation of pass/fail |

[Continues for all 11 gates with 100+ total variables documented]

## audit-report-quick.md (Quick Mode)

Uses subset of variables from full report:

- All metadata variables (same as full)
- Gate 1, 3, 4, 5, 9 variables only
- Additional: quick_mode_note (explains this is triage only)

## audit-history.md (Re-Audit Comparison)

Additional variables for comparison:

| Variable | Type | Example | Required |
|----------|------|---------|----------|
| previous_audit_date | date | "2025-12-15" | Yes |
| previous_score | integer | 5 | Yes |
| score_delta | integer | +2 | Yes (calculated) |
| improved_gates | list | "Gate 3, Gate 7" | Yes |
| regressed_gates | list | "Gate 5" | Optional |
| delta_summary | text | "Significant improvement in friction reduction" | Yes |
```

**Why:** Prevents template variable errors. Implementers know exactly what to provide and in what format. Enables validation before rendering.

### Pattern 7: Input Validation and Error Handling

**What:** Explicit error handling patterns for common failure modes (missing files, bad paths, incomplete projects).

**When:** Essential for production skills that accept user input.

**Structure for guides/INPUT-VALIDATION.md:**

```markdown
# Input Validation Guide

## Validation Sequence

When /rfu-audit is invoked with [path]:

1. **Validate path exists**
   - Error: "Path does not exist: [path]"
   - Resolution: Prompt user to check path or use current directory

2. **Check if directory (not file)**
   - Error: "Expected directory, got file: [path]"
   - Resolution: "Did you mean: [parent directory]?"

3. **Check read permissions**
   - Error: "Permission denied: [path]"
   - Resolution: "Run with appropriate permissions or choose accessible directory"

4. **Validate project structure** (at least one must exist):
   - README.md or README or readme.md
   - package.json or pyproject.toml or Cargo.toml or go.mod
   - Source files (*.js, *.py, *.rs, *.go, etc.)

   - Error: "Not a recognizable project. No README or package manifest found."
   - Resolution: "Ensure project has README or package file. See examples: [link]"

5. **Check for minimum content**
   - If README exists but <100 characters: Warning (proceed with limited context)
   - If no description available: Warning (Gate 4 will likely fail)

## Graceful Degradation

If project files are incomplete:

- **Missing README:** Proceed with warning. "No README found. Gates 3, 4, 6 will have limited context."
- **Missing package.json:** Proceed with warning. "No package manifest. Auto-analyze disabled."
- **Empty README:** Proceed with warning. "README is very short (<100 chars). Consider expanding before audit."

## Error Output Format

```
❌ RFU Audit Failed: [Error Type]

**Reason:** [Detailed explanation]

**Resolution:** [What user should do]

**Examples:**
- [Example 1]
- [Example 2]
```

## Validation Checklist

Before starting gate evaluation:

- [ ] Path exists and is readable
- [ ] Directory contains project files
- [ ] At least one documentation file (README) present
- [ ] Project has identifiable purpose (from README or manifest)
- [ ] User aware of any warnings (missing files, limited context)
```

**Why:** Prevents cryptic errors. Users get actionable guidance when input is invalid.

## Anti-Patterns to Avoid

### Anti-Pattern 1: Monolithic SKILL.md

**What:** Putting all gate definitions, examples, edge cases, and templates in a single SKILL.md file.

**Why bad:**
- Exceeds cognitive load (500+ lines to parse)
- Hard to maintain (changes to examples affect gate logic)
- No separation of concerns (orchestration mixed with evaluation details)

**Instead:** Use modular structure with SKILL.md as orchestrator referencing specialized files.

### Anti-Pattern 2: Implementation-Coupled Templates

**What:** Template variables that assume specific implementation details (e.g., `database_query_result`, `api_response_json`).

**Why bad:**
- Claude Code skills don't execute code; they guide reasoning
- Templates should reflect evaluation outputs, not technical artifacts
- Breaks abstraction (template assumes implementation that doesn't exist)

**Instead:** Use domain-relevant variables (`gate1_verdict`, `bedrock_need`, `score`) that reflect evaluation concepts.

### Anti-Pattern 3: Hardcoded Examples in Gate Definitions

**What:** Embedding pass/fail examples directly in gate definition prose.

**Why bad:**
- Examples become stale as frameworks evolve
- Clutters gate definitions, making pass criteria harder to extract
- Can't easily expand examples without rewriting gates

**Instead:** Reference a separate GATE-EXAMPLES.md file from gate definitions.

### Anti-Pattern 4: Implicit Edge Case Handling

**What:** Leaving gray areas unspecified, expecting auditors to "use common sense."

**Why bad:**
- Inconsistent scoring across auditors
- Score inflation (auditors give benefit of doubt)
- No way to reproduce scores

**Instead:** Document edge case resolution explicitly in EDGE-CASES.md.

### Anti-Pattern 5: Auto-Analyze as Replacement for Judgment

**What:** Using auto-analyze heuristics to automatically score gates without human review.

**Why bad:**
- Heuristics are approximations (README length ≠ comprehension quality)
- Misses context (project may be great but poorly documented)
- Users game the system (optimize README for auto-analyze, not actual users)

**Instead:** Use auto-analyze to suggest answers, require human validation before scoring.

## Build Order Implications

When implementing the modular architecture, follow this sequence:

### Phase 1: Core Refactoring (Foundation)
1. **Create directory structure** (config/, guides/, templates/, reference/)
2. **Extract gate definitions** from SKILL.md to config/gates-full.md
3. **Update SKILL.md** to reference new gate file (minimal orchestration logic)
4. **Validate** that existing functionality still works

**Why first:** Establishes structure without changing behavior. Tests that modularization doesn't break skill invocation.

### Phase 2: Examples and Clarity (Consistency)
1. **Create GATE-EXAMPLES.md** with pass/fail cases for all 11 gates
2. **Create EDGE-CASES.md** with gray area resolution rules
3. **Update gate definitions** to reference examples and edge cases
4. **Add reference/FAILED-AUDITS.md** with complete failed audit examples

**Why second:** Improves scoring consistency before adding new modes. Ensures existing gates are well-defined before variations.

### Phase 3: Quick Mode (New Capability)
1. **Create config/gates-quick.md** with 5-gate subset (Gates 1, 3, 4, 5, 9)
2. **Create templates/audit-report-quick.md** for quick mode output
3. **Update SKILL.md** to support `--quick` flag and mode selection
4. **Add examples** of quick mode audits to GATE-EXAMPLES.md

**Why third:** Adds new mode after core structure is stable. Quick mode reuses existing examples/edge cases.

### Phase 4: Auto-Analyze (Acceleration)
1. **Create guides/AUTO-ANALYZE.md** with heuristics per gate
2. **Update SKILL.md** to support `--auto-analyze` flag
3. **Document limitations** (what can/cannot be automated)
4. **Add examples** of auto-analyzed output with human corrections

**Why fourth:** Builds on stable gate definitions. Auto-analyze needs well-defined gates to know what to extract.

### Phase 5: History and Comparison (Advanced)
1. **Create templates/audit-history.md** for re-audit comparisons
2. **Define file-based history storage** (.planning/audits/[project]-[date].md)
3. **Update SKILL.md** to detect previous audits and offer comparison
4. **Add reference/TEMPLATE-SPEC.md** documenting all template variables

**Why fifth:** Most complex feature. Requires stable core + templates before adding comparison logic.

### Phase 6: Input Validation (Robustness)
1. **Create guides/INPUT-VALIDATION.md** with error handling patterns
2. **Update SKILL.md** to validate input before starting audit
3. **Add graceful degradation** for missing/incomplete project files
4. **Document error messages** with examples

**Why last:** Polish feature. Core functionality should work before adding validation edge cases.

## Scalability Considerations

### At 11 gates (current)
**Approach:** Modular structure with separate examples and edge cases
**Complexity:** ~500 lines across 8 files (manageable)
**Maintenance:** Low (each gate is independent)

### At 20+ gates (future expansion)
**Approach:** Group related gates into categories (e.g., user-facing gates, technical gates, market gates)
**Complexity:** Create config/gates-user.md, config/gates-technical.md, config/gates-market.md
**Maintenance:** Medium (need category consistency)

### At 50+ gates (enterprise/specialized)
**Approach:** Support custom gate packs (e.g., SaaS-specific, open-source-specific)
**Complexity:** Plugin architecture: config/gates-[pack].md loaded conditionally
**Maintenance:** High (need gate pack versioning)

### Multi-Auditor Mode (team audits)
**Approach:** Support multiple auditor responses per gate
**Complexity:** Templates need arrays of responses, aggregation logic
**File changes:** Add templates/audit-report-multi.md with consensus display
**Maintenance:** High (aggregation rules, conflict resolution)

### Internationalization (i18n)
**Approach:** Separate gate definitions by language
**Complexity:** config/gates-full-en.md, config/gates-full-es.md, etc.
**File changes:** SKILL.md detects language preference, loads appropriate file
**Maintenance:** Very high (keep translations in sync)

## Extensibility Patterns

### Pattern: Gate Packs
**Use case:** Different project types need different gates
**Implementation:**
```
config/
├── gates-core.md          # 6 universal gates
├── gates-saas.md          # 5 SaaS-specific gates
├── gates-opensource.md    # 5 open-source-specific gates
├── gates-library.md       # 5 library-specific gates
```
**Invocation:** `/rfu-audit [path] --pack saas` loads core + saas gates

### Pattern: Custom Scoring Rules
**Use case:** Teams want different pass thresholds
**Implementation:**
```
config/
├── scoring-rules-default.md   # 11/11 = ship, 9-10 = almost, etc.
├── scoring-rules-strict.md    # Only 11/11 passes
├── scoring-rules-lenient.md   # 8/11 = ship
```
**Invocation:** `/rfu-audit [path] --scoring strict`

### Pattern: Template Variants
**Use case:** Output format preferences (markdown vs JSON vs HTML)
**Implementation:**
```
templates/
├── audit-report.md            # Default markdown
├── audit-report.json          # JSON for tooling
├── audit-report.html          # Shareable HTML
```
**Invocation:** `/rfu-audit [path] --format json`

### Pattern: Pre/Post Hooks
**Use case:** Integration with other skills (e.g., auto-run /prep-repo after pass)
**Implementation:**
```
SKILL.md orchestration includes:

After audit completes:
- If score >= 9: Suggest running /prep-repo to prepare for publishing
- If score <= 5: Suggest running /think-first to reconsider approach
```

### Pattern: Progressive Enhancement
**Use case:** Start simple (basic audit), add advanced features over time
**Implementation:**
- v1.0: Core 11 gates, single template
- v1.1: Add quick mode (config/gates-quick.md)
- v1.2: Add auto-analyze (guides/AUTO-ANALYZE.md)
- v1.3: Add history comparison (templates/audit-history.md)
- v2.0: Add gate packs (config/gates-[pack].md)

Each version adds files without changing existing ones (backward compatible).

## Sources

**HIGH Confidence:**
- Existing codebase analysis: /Users/jameschristian/Dev/claude-rfu-audit/skill/SKILL.md
- Existing codebase analysis: /Users/jameschristian/Dev/claude-rfu-audit/skill/templates/audit-report.md
- Existing codebase analysis: /Users/jameschristian/Dev/claude-rfu-audit/.planning/codebase/ARCHITECTURE.md
- Existing codebase analysis: /Users/jameschristian/Dev/claude-rfu-audit/.planning/codebase/CONCERNS.md
- Software architecture principles: Separation of concerns, single responsibility, modularity
- Claude Code skill system constraints: Markdown-based, declarative, stateless

**MEDIUM Confidence:**
- Gate pack extensibility pattern (inspired by plugin architectures in eslint, webpack)
- Progressive enhancement approach (standard web development pattern)
- Template variant pattern (common in static site generators)

**LOW Confidence:**
- Multi-auditor mode scalability (no existing implementation to reference)
- i18n approach (untested in Claude Code skill context)

---

*Architecture research: 2026-01-22*
