# Project State

## Project Reference

See: .planning/PROJECT.md (updated 2026-01-22)

**Core value:** Consistent, actionable audit results. Two auditors should score the same project similarly, and the output should tell you exactly what to fix.
**Current focus:** Phase 3 - Input Validation

## Current Position

Phase: 3 of 7 (Input Validation)
Plan: 2 of 2 in current phase
Status: Phase complete
Last activity: 2026-01-22 - Completed 03-02-PLAN.md

Progress: [████████            ] 57% (8/14 plans complete)

## Performance Metrics

**Velocity:**
- Total plans completed: 8
- Average duration: 5min
- Total execution time: 0.92 hours

**By Phase:**

| Phase | Plans | Total | Avg/Plan |
|-------|-------|-------|----------|
| 01-modular-architecture | 2 | 4min | 2min |
| 02-gate-clarity | 4 | 36min | 9min |
| 03-input-validation | 2 | 4min | 2min |

**Recent Trend:**
- Last 5 plans: 02-03 (9min), 02-04 (6min), 03-01 (1min), 03-02 (3min)
- Trend: Phase 3 complete - very fast documentation phase (2min avg)

*Updated after each plan completion*

## Accumulated Context

### Decisions

Decisions are logged in PROJECT.md Key Decisions table.
Recent decisions affecting current work:

| Phase | Decision | Rationale | Impact |
|-------|----------|-----------|--------|
| 01-01 | Single gates-full.md vs. separate files per gate | Easier to search, gates are related concepts, file manageable at 378 lines | Affects how gate content is organized and referenced |
| 01-01 | Template variables co-located with gate definitions | Reduces cognitive load during audit execution | Affects report generation implementation |
| 01-01 | Placeholder guide files with "Status: Placeholder" marker | Establishes structure now, enables parallel development | Affects content development workflow |
| 01-02 | Gate overview table format (Gate#, Name, Core Question only) | Quick reference without duplicating config/gates-full.md content | Affects how gates are presented in main skill file |
| 01-02 | Multiple references to config/gates-full.md (4 locations) | Ensures Claude knows where to find gate details at every relevant section | Affects skill execution and reference lookup |
| 02-01 | Three-section rubric format (Objective Rubric, Scoring, Edge Cases) | Separates measurement protocol from verdict logic from gray areas | Consistent structure for remaining gates (5-11) |
| 02-01 | Six bedrock needs list for Gate 1 | Explicit endpoint prevents subjective interpretation of 5 Whys | Auditors know when to stop drilling |
| 02-01 | 5-minute switching cost threshold (Gate 2) | Quantifiable boundary prevents "minor inconvenience" arguments | Clear pass/fail line for inversion test |
| 02-01 | Pre-req timing rules (Gate 3) | Include project-specific, exclude domain-standard tools | Fair comparison across different tech stacks |
| 02-01 | Explicit jargon reference list (Gate 4) | Prevents disagreement on what counts as "technical" | API/CLI/schema = jargon; email/website/file = acceptable |
| 02-02 | Gate 5 multi-signal validation (4 signals, 3+ required) | Converts subjective "honest yes" into countable evidence | Self-audit and third-party protocols now objective |
| 02-02 | Gate 5 counterfactual test for borderline cases | "Which $20/month subscription would you cancel?" → Specific answer = PASS | Borderline decision now deterministic |
| 02-02 | Gate 5 "real names" validation criteria | Must be person you could message today who you've confirmed has problem | Prevents archetypes/personas from passing |
| 02-02 | Gate 7 friction scoring (Low=1, Medium=2, High=3) | Sum of 6 steps: ≤9=pass, ≥13=fail, 10-12=borderline | Converts time/complexity into comparable numbers |
| 02-02 | Gate 8 consequence net scoring (positive +1, negative -1) | Net ≥1=pass, ≤-1=fail, 0=borderline | Forces explicit enumeration vs vague "pros outweigh cons" |
| 02-03 | Gate 10 concentration ratio threshold (K/N ≤ 0.30) | Replaces subjective "concentrated value" with measurable calculation | Top 30% of features must deliver 80% of value for PASS |
| 02-03 | Gate 10 binary alternative test for borderline | Can name ONE feature + removal breaks project + others are enhancements | Deterministic verdict for 0.30 < K/N ≤ 0.50 range |
| 02-03 | Gate 11 self-audit commitment signals (5 of 6 required) | Observable evidence: 6+ months manual work, searched alternatives, weekly use, 2+ hours/week cost, would rebuild | Replaces "would regret NOT shipping" with countable signals |
| 02-03 | Gate 11 third-party audit signals (4 of 5 required) | External evidence: 3+ month commits, active issues <7 days, roadmap, public problem description, multiple domain projects | Third-party can audit without creator's subjective input |
| 02-03 | Gate 11 automatic disqualifiers | README states "learning exercise," fork without differentiation, creator doesn't use tool | Immediate FAIL regardless of other signals |
| 02-04 | 27 edge case resolution rules | Covers all gray areas: N/A determination, inter-gate dependencies, gate-specific borderlines | Deterministic verdicts for ambiguous situations |
| 02-04 | Edge case resolution format | When → Question → Resolution rule → Tie-breaker test | Structured approach ensures consistency |
| 02-04 | Decision tree for edge cases | 4-step process: disqualifiers → primary criteria → edge case lookup → document | Guides auditors through borderline situations |
| 02-04 | Extensible edge case guide | Explicit process for adding new edge cases as audits reveal them | Guide grows with audit experience |
| 03-01 | 6-stage validation flow order | Normalize → exists → is-dir → README → project indicators → proceed, stops at first failure | Specific, actionable error messages for each failure |
| 03-01 | 3-part error message format | Problem + why it matters + fix steps | Links validation failures to gate requirements |
| 03-01 | Error types mapped to validation stages | 5 specific templates (path not exist, not directory, permission denied, no README, no project files) | Each error links to relevant gate (Gate 3, Gate 4) |
| 03-01 | Validation as step 0 in Process | Added before reading project files | Ensures bad input caught before any audit work |
| 03-02 | Symlinks should be followed | Users expect `/link/to/project` to work like `/actual/project` | Validation uses resolved path for all checks |
| 03-02 | Only README.md recognized | Markdown is standard, variants add complexity without benefit | readme.md, README.txt, README.rst fail Stage 4 |
| 03-02 | Empty README passes validation | Validation checks existence/readability, not content quality | Content evaluation happens at Gate 4 |
| 03-02 | Project indicators check ~10 paths | Path existence checks are fast regardless of directory size | No special handling for large directories needed |

### Pending Todos

None yet.

### Blockers/Concerns

None yet.

## Session Continuity

Last session: 2026-01-22T16:10:42Z
Stopped at: Completed 03-02-PLAN.md (Input Validation Guide) - Phase 3 complete
Resume file: None

---
*Next step: Plan Phase 4 (Gate PASS Examples)*
