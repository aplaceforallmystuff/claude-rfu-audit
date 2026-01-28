# Roadmap: RFU Audit Enhancement

## Overview

This roadmap transforms the RFU Audit skill from a working-but-inconsistent framework into a production-quality tool that produces consistent scores and actionable output. The journey starts with modularizing the architecture (Phase 1), then establishes scoring consistency through rubrics and examples (Phase 2), adds input robustness (Phase 3), reduces friction with quick and auto-analyze modes (Phases 4-5), and culminates in actionable output with priority matrices and fix integration (Phase 6). Audit history tracking (Phase 7) is a future enhancement.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [x] **Phase 1: Modular Architecture** - Separate concerns into focused files without changing behavior
- [x] **Phase 2: Gate Clarity** - Add rubrics, FAIL examples, and edge case handling for consistent scoring
- [x] **Phase 3: Input Validation** - Handle bad paths and missing files gracefully
- [x] **Phase 4: Quick Audit Mode** - 5-gate triage for rapid project filtering
- [x] **Phase 5: Auto-Analyze Mode** - Pre-populate gate context from project files
- [x] **Phase 6: Actionable Output** - Priority matrix and fix integration
- [ ] **Phase 7: Audit History** - Track and compare audits over time (Future)

## Phase Details

### Phase 1: Modular Architecture
**Goal**: Skill structure supports incremental enhancement without monolithic file edits
**Depends on**: Nothing (first phase)
**Requirements**: ARCH-01, ARCH-02, ARCH-04, ARCH-05
**Success Criteria** (what must be TRUE):
  1. Skill invocation (`/rfu-audit [path]`) works identically to current behavior
  2. Gate definitions live in `config/gates-full.md`, not SKILL.md
  3. SKILL.md orchestrates flow and references external files
  4. Directory structure exists: `config/`, `guides/`, `templates/`, `reference/`
  5. Example structure placeholder exists in `guides/GATE-EXAMPLES.md`
**Plans:** 2 plans

Plans:
- [x] 01-01-PLAN.md — Create directory structure and migrate gate definitions to config/gates-full.md
- [x] 01-02-PLAN.md — Update SKILL.md as orchestrator with file references

### Phase 2: Gate Clarity
**Goal**: Two auditors score the same project within 1 point of each other
**Depends on**: Phase 1 (modular structure enables targeted edits)
**Requirements**: GATE-01, GATE-02, GATE-03, GATE-04
**Success Criteria** (what must be TRUE):
  1. Every gate has explicit pass/fail criteria with concrete thresholds
  2. Every gate has 2+ FAIL examples showing what failure looks like
  3. Gates 5, 10, 11 have specific scoring rubrics (not subjective language)
  4. Edge case guide exists with resolution rules for borderline cases
  5. Auditor can determine pass/fail without subjective interpretation
**Plans:** 4 plans (4 waves - sequential due to shared file dependencies)

Plans:
- [x] 02-01-PLAN.md — Add objective rubrics and FAIL examples for Gates 1-4
- [x] 02-02-PLAN.md — Add objective rubrics and FAIL examples for Gates 5-8 (esp. Gate 5 Wallet)
- [x] 02-03-PLAN.md — Add objective rubrics and FAIL examples for Gates 9-11 (esp. Gates 10, 11)
- [x] 02-04-PLAN.md — Create edge case resolution guide with decision trees

### Phase 3: Input Validation
**Goal**: Clear error messages for bad input instead of cryptic failures
**Depends on**: Phase 1 (validation patterns go in guides/)
**Requirements**: INPUT-01, INPUT-02, INPUT-03, ARCH-07
**Success Criteria** (what must be TRUE):
  1. Invalid path produces specific error ("Path does not exist: /bad/path")
  2. Missing README produces guidance, not failure ("No README found. Create one with: ...")
  3. Permission denied produces actionable message
  4. Non-project directory produces guidance on what constitutes a project
  5. Validation guide documents all error handling patterns
**Plans:** 2 plans (1 wave - parallel execution)

Plans:
- [x] 03-01-PLAN.md — Add input validation section to SKILL.md with 6-stage validation flow
- [x] 03-02-PLAN.md — Create INPUT-VALIDATION.md guide with complete error handling patterns

### Phase 4: Quick Audit Mode
**Goal**: Filter projects in 5-10 minutes before committing to full audit
**Depends on**: Phase 2 (quick mode needs clear gate criteria)
**Requirements**: MODE-01, MODE-02, ARCH-03
**Success Criteria** (what must be TRUE):
  1. `/rfu-audit --quick [path]` runs 5-gate subset (Gates 1, 3, 4, 5, 9)
  2. Quick mode completes in under 10 minutes
  3. Quick report shows 5-gate score with recommendation (proceed to full audit or not)
  4. Quick mode gates reference full gate definitions (no duplication)
  5. Quick mode template exists in `templates/audit-report-quick.md`
**Plans:** 2 plans (1 wave - parallel execution)

Plans:
- [x] 04-01-PLAN.md — Create gates-quick.md with 5-gate subset and triage algorithm
- [x] 04-02-PLAN.md — Add quick mode to SKILL.md and create quick report template

### Phase 5: Auto-Analyze Mode
**Goal**: Reduce manual data entry by extracting project info automatically
**Depends on**: Phase 2 (auto-analyze needs well-defined gates to know what to extract)
**Requirements**: MODE-03, MODE-04, ARCH-06
**Success Criteria** (what must be TRUE):
  1. `/rfu-audit --auto-analyze [path]` extracts info from README/package.json
  2. Auto-analyze pre-populates: project name, description, value proposition, features
  3. Extracted info is presented as suggestions, not scores
  4. Human must verify/override before gates are scored
  5. AUTO-ANALYZE.md documents heuristics and limitations
**Plans:** 2 plans (1 wave - parallel execution)

Plans:
- [x] 05-01-PLAN.md — Create AUTO-ANALYZE.md guide with extraction heuristics and verification workflow
- [x] 05-02-PLAN.md — Add auto-analyze mode to SKILL.md with --auto-analyze flag

### Phase 6: Actionable Output
**Goal**: Failed audits tell you exactly what to fix and how
**Depends on**: Phase 2 (priority matrix needs stable gate definitions)
**Requirements**: OUTPUT-01, OUTPUT-02, OUTPUT-03, ARCH-08
**Success Criteria** (what must be TRUE):
  1. Report includes priority matrix ranking failed gates by fix impact
  2. Each failed gate links to relevant fix resources (skills, tools, templates)
  3. Effort estimates (quick/medium/involved) accompany each fix
  4. TEMPLATE-SPEC.md documents all template variables
  5. User knows what to do after reading a failed audit
**Plans:** 3 plans (2 waves)

Plans:
- [x] 06-01-PLAN.md — Create PRIORITY-MATRIX.md guide with ranking algorithm and effort estimation
- [x] 06-02-PLAN.md — Update audit templates with Priority Matrix section and SKILL.md process
- [x] 06-03-PLAN.md — Create TEMPLATE-SPEC.md documenting all template variables

### Phase 7: Audit History (Future)
**Goal**: Track project improvement over time
**Depends on**: Phase 6 (stable output format before adding history comparison)
**Requirements**: HIST-01, HIST-02
**Success Criteria** (what must be TRUE):
  1. Audit reports can be saved to `.planning/audits/` with timestamps
  2. Re-auditing a project shows score delta from previous audit
  3. History is file-based (skill remains stateless)
  4. Comparison shows which gates improved/regressed
**Plans**: TBD

Plans:
- [ ] 07-01: Implement audit history storage and comparison

## Build Order Rationale

**Why this order:**

1. **Phase 1 (Architecture) first**: Establishes the modular structure that all other phases build on. Without separating gate definitions from orchestration, every enhancement requires editing a monolithic SKILL.md.

2. **Phase 2 (Gate Clarity) before new modes**: Scoring consistency is the foundation. Quick mode and auto-analyze become useless if the gates themselves produce inconsistent results. This is the highest-impact enhancement.

3. **Phase 3 (Input Validation) before new modes**: Cheap insurance. When users try new modes, they'll make input mistakes. Good error messages prevent frustration and support requests.

4. **Phases 4-5 (Quick/Auto-Analyze) together**: Both reduce friction. Quick mode is simpler (subset of existing gates) so goes first. Auto-analyze is more complex (extraction heuristics) so goes second.

5. **Phase 6 (Actionable Output) after modes**: Priority matrix and fix integration need stable gate definitions and output format. Adding them earlier means rework when output changes.

6. **Phase 7 (History) last**: Nice-to-have, not core. Ship a working, improved skill without it. Add history tracking when core functionality is proven.

## Progress

**Execution Order:**
Phases execute in numeric order: 1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 7

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Modular Architecture | 2/2 | Complete | 2026-01-22 |
| 2. Gate Clarity | 4/4 | Complete | 2026-01-22 |
| 3. Input Validation | 2/2 | Complete | 2026-01-22 |
| 4. Quick Audit Mode | 2/2 | Complete | 2026-01-22 |
| 5. Auto-Analyze Mode | 2/2 | Complete | 2026-01-28 |
| 6. Actionable Output | 3/3 | Complete | 2026-01-28 |
| 7. Audit History | 0/1 | Not started | - |

---
*Created: 2026-01-22*
