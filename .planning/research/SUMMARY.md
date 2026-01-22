# Project Research Summary

**Project:** RFU Audit Enhancement
**Domain:** Claude Code skill design for product validation frameworks
**Researched:** 2026-01-22
**Confidence:** MEDIUM-HIGH

## Executive Summary

The RFU Audit skill is an 11-gate validation framework that forces developers to prove project utility before investing significant effort. The enhancement goals center on three themes: **clarity** (eliminating gate ambiguity), **automation** (reducing audit friction), and **actionability** (turning scores into improvement paths). Research across stack, features, architecture, and pitfalls converges on a clear recommendation: **modularize the skill structure first, then enhance gate clarity before adding new modes.**

The core insight is that Claude Code skills are prompt architectures, not executable code. All enhancements are markdown edits that shape LLM reasoning. The "stack" is prompt engineering patterns (rubric-based evaluation, chain-of-thought elicitation, negative example documentation). The architecture should separate orchestration (SKILL.md) from evaluation details (gates), examples (GATE-EXAMPLES.md), and edge cases (EDGE-CASES.md). This modular structure enables incremental enhancement without breaking existing functionality.

The primary risk is **vague criteria creating inconsistent scoring**. Gates 5 (Wallet), 10 (Pareto), and 11 (Regret) are currently too subjective. Without concrete rubrics and failure examples, different auditors will reach different conclusions, destroying framework credibility. The mitigation is systematic: add explicit pass/fail criteria, document what FAIL looks like for every gate, and codify edge case resolution rules. Secondary risks include audit fatigue (11 gates is long) and non-actionable output (scores without fix guidance). Quick mode and priority matrix features address these.

## Key Findings

### Recommended Stack

Claude Code skills are declarative markdown specifications that guide Claude's reasoning. No external libraries or frameworks needed. The "stack" consists of prompt engineering patterns and self-documentation techniques.

**Core technologies:**
- **Markdown (CommonMark):** Skill definition format — native to Claude Code, version-controllable, human-readable
- **Template Variables ({{var}} syntax):** Output structure — already in use, simple interpolation
- **Rubric-Based Evaluation Pattern:** Gate consistency — define concrete examples for each score level
- **Chain-of-Thought Elicitation:** Force reasoning before scoring — prevents snap judgments
- **Negative Example Documentation:** Show what FAIL looks like — prevents score inflation

**Key patterns to adopt:**
1. Pre-scoring evidence collection (checklists before scoring)
2. Edge case codification (document gray area resolution)
3. Self-documentation commentary (explain WHY each gate exists)
4. Binary scoring with forced choices (eliminate "maybe" responses)

### Expected Features

**Must have (table stakes):**
- Clear pass/fail criteria with concrete thresholds
- Specific failure messages (not "failed" but "missing installation instructions")
- Examples of pass/fail for each gate
- Human-readable output with summary before details
- Deterministic results (same input = same output)
- Progress indicators during audit

**Should have (differentiators):**
- Quick triage mode (5 gates: 1, 3, 4, 5, 9) for 80% signal in 20% time
- Auto-analyze mode (pre-populate from README/package.json)
- Priority ranking (which failures to fix first)
- Fix integration (link failures to relevant skills/tools)
- Edge case handling (explicit gray area guidance)

**Defer (v2+):**
- Comparison over time (audit history tracking)
- Partial completion (save/resume mid-audit)
- Confidence scoring per gate
- Custom thresholds (project-specific pass criteria)
- Interactive mode (prompt for clarification)

### Architecture Approach

The current single-file structure (SKILL.md + single template) will not scale. Research recommends a modular skill architecture that separates concerns into focused files while maintaining the stateless, markdown-based nature of Claude Code skills.

**Major components:**

1. **SKILL.md** — Orchestration: skill metadata, mode selection, process flow, references to other files
2. **config/gates-full.md** — Complete 11-gate definitions with pass/fail criteria
3. **config/gates-quick.md** — 5-gate quick mode subset (Gates 1, 3, 4, 5, 9)
4. **guides/GATE-EXAMPLES.md** — Concrete pass/fail/borderline examples per gate
5. **guides/EDGE-CASES.md** — Gray area resolution rules
6. **guides/AUTO-ANALYZE.md** — Project file parsing heuristics
7. **guides/INPUT-VALIDATION.md** — Error handling for missing/bad files
8. **templates/audit-report.md** — Full report output (existing, enhanced)
9. **templates/audit-report-quick.md** — Quick mode report output
10. **reference/TEMPLATE-SPEC.md** — Variable documentation for templates

### Critical Pitfalls

1. **Vague Pass/Fail Criteria** — Gates 5, 10, 11 use subjective language ("reasonable", "sufficient"). Prevention: Add concrete examples and quantitative thresholds ("3 specific people with names", not "would people pay?"). Test with multiple auditors; if scores diverge >20%, criteria are too vague.

2. **No Failure Examples** — Current documentation only shows what PASS looks like. Prevention: Document 2+ failure examples per gate. Show what clear FAIL looks like. Create comparison tables (Project A passed because X; Project B failed because Y).

3. **No Gray Area Handling** — Real projects exist on a spectrum, but framework forces binary PASS/FAIL. Prevention: Document edge case resolution explicitly. Add CONDITIONAL scoring option (0.5 points) for borderline cases with mandatory auditor commentary.

4. **Audit Fatigue** — 11 gates with deep thinking required causes later gates to receive weaker analysis. Prevention: Quick triage mode (5 gates, 15 min) filters projects before full commitment. Show progress ("Gate 3/11: 27% complete").

5. **Non-Actionable Output** — Audit says "Failed Gate 3" but not how to fix it. Prevention: Link each gate to fix resources. Generate prioritized fix list with effort estimates. Cross-reference to relevant skills ("/prep-repo for discoverability").

## Implications for Roadmap

Based on research, suggested phase structure:

### Phase 1: Modular Refactoring
**Rationale:** Establishes structure without changing behavior. Tests that modularization doesn't break skill invocation. All subsequent phases depend on this structure.
**Delivers:** Directory structure (config/, guides/, templates/, reference/), extracted gate definitions, updated SKILL.md as orchestrator
**Addresses:** Maintainability, separation of concerns
**Avoids:** Monolithic SKILL.md anti-pattern

### Phase 2: Gate Clarity Enhancement
**Rationale:** Improves scoring consistency before adding new modes. The most critical enhancement — without clear criteria, all other features produce inconsistent results.
**Delivers:** Rubrics for all 11 gates (especially 5, 10, 11), FAIL examples for every gate, edge case resolution guide
**Addresses:** Table stakes features (clear criteria, specific failure messages, examples)
**Avoids:** Vague criteria pitfall, happy-path-only documentation pitfall, no gray area handling pitfall

### Phase 3: Input Validation
**Rationale:** Polish feature that makes the skill production-ready. Prevents cryptic errors when users provide bad input.
**Delivers:** Path validation, project structure checks, graceful degradation for missing files, clear error messages
**Addresses:** Error handling (table stakes)
**Avoids:** Manual data entry errors pitfall

### Phase 4: Quick Audit Mode
**Rationale:** Adds efficiency feature after core structure is stable. Quick mode reuses existing examples/edge cases. High user value with low implementation complexity.
**Delivers:** 5-gate subset (Gates 1, 3, 4, 5, 9), quick mode template, --quick flag support
**Addresses:** Quick triage mode differentiator
**Avoids:** Audit fatigue pitfall

### Phase 5: Auto-Analyze Mode
**Rationale:** Builds on stable gate definitions. Auto-analyze needs well-defined gates to know what to extract. Reduces manual data entry friction.
**Delivers:** Project file parsing (README, package.json), pre-populated gate context, --auto-analyze flag, human verification workflow
**Addresses:** Pre-populated context differentiator
**Avoids:** Manual data entry errors pitfall

### Phase 6: Priority Matrix & Fix Integration
**Rationale:** Transforms audit from diagnosis to treatment plan. Highest user value but depends on clear gate definitions and stable output structure.
**Delivers:** Impact/effort scoring per gate failure, prioritized fix list, links to relevant skills (/prep-repo, etc.)
**Addresses:** Priority ranking and fix integration differentiators
**Avoids:** Non-actionable output pitfall

### Phase 7: Audit History (Future)
**Rationale:** Most complex feature. Requires stable core + templates before adding comparison logic. Lower priority than core enhancements.
**Delivers:** File-based history storage, re-audit comparison, score delta tracking
**Addresses:** Comparison over time differentiator
**Avoids:** Stale audit results pitfall

### Phase Ordering Rationale

- **Phases 1-2 first:** Architecture and clarity are prerequisites. Without modular structure, adding features creates unmaintainable code. Without clear criteria, all features produce inconsistent results.
- **Phase 3 before new modes:** Input validation is cheap and prevents user frustration when trying new features.
- **Phases 4-5 together or sequential:** Quick mode and auto-analyze are independent but both reduce friction. Either order works; quick mode is simpler so goes first.
- **Phase 6 after modes:** Priority matrix needs stable gate structure and output format before implementation.
- **Phase 7 last:** Audit history is nice-to-have, not core. Ship without it and add later.

### Research Flags

Phases likely needing deeper research during planning:
- **Phase 5 (Auto-Analyze):** Heuristics for extracting project info need validation. What can README parsing reliably detect? What causes false positives?
- **Phase 6 (Priority Matrix):** Impact/effort scoring logic needs refinement. How to weight gates? How to estimate fix effort?

Phases with standard patterns (skip research-phase):
- **Phase 1 (Modular Refactoring):** File organization is straightforward; no external dependencies
- **Phase 2 (Gate Clarity):** Pattern is clear (rubrics + examples); just needs execution
- **Phase 3 (Input Validation):** Standard error handling patterns
- **Phase 4 (Quick Mode):** Simple subset of existing gates; well-defined in research

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| Stack | HIGH | Claude Code skill patterns well-understood from existing codebase analysis |
| Features | MEDIUM | Based on training knowledge of audit tools (ESLint, Lighthouse, npm audit); no 2026 research available |
| Architecture | HIGH | Based on existing codebase + standard software architecture principles |
| Pitfalls | MEDIUM | Derived from CONCERNS.md analysis + general audit framework patterns |

**Overall confidence:** MEDIUM-HIGH

The architecture and stack recommendations are HIGH confidence because they derive from analysis of the existing codebase and established software patterns. Feature and pitfall recommendations are MEDIUM confidence because WebSearch was unavailable to verify current (2026) best practices in audit tool UX.

### Gaps to Address

- **Inter-rater reliability testing:** No way to verify that enhanced gate criteria produce consistent scores. Recommend testing with 2-3 auditors on same project during Phase 2.
- **Quick mode validation:** 5-gate subset is theorized to catch 80% of issues. Validate by running both quick and full audits on sample projects, comparing outcomes.
- **Auto-analyze accuracy:** Heuristics are approximations. Build in human override and track correction rate to improve heuristics over time.
- **Fix integration resources:** Which skills/tools actually exist to link to? Audit existing skill library before building Phase 6.

## Sources

### Primary (HIGH confidence)
- `/Users/jameschristian/Dev/claude-rfu-audit/skill/SKILL.md` — existing gate definitions
- `/Users/jameschristian/Dev/claude-rfu-audit/skill/templates/audit-report.md` — existing template structure
- `/Users/jameschristian/Dev/claude-rfu-audit/.planning/codebase/CONCERNS.md` — documented technical debt and gaps
- `/Users/jameschristian/Dev/claude-rfu-audit/.planning/codebase/ARCHITECTURE.md` — current architecture analysis

### Secondary (MEDIUM confidence)
- Training knowledge of ESLint, Lighthouse, npm audit patterns — adapted for Claude Code skill context
- LLM evaluation research (rubric-based scoring, chain-of-thought prompting) — established practices
- Software architecture principles (separation of concerns, modularity) — standard patterns

### Tertiary (LOW confidence)
- Quick mode gate selection (Gates 1, 3, 4, 5, 9) — informed heuristic, needs validation
- Auto-analyze heuristics — untested approximations
- Audit tool UX best practices 2026 — WebSearch unavailable for verification

---
*Research completed: 2026-01-22*
*Ready for roadmap: yes*
