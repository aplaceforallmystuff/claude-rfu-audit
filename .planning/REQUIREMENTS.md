# Requirements: RFU Audit Enhancement

## Overview

Requirements for enhancing the RFU Audit skill to produce consistent, actionable audit results.

## v1 Requirements

### Gate Clarity (GATE)

| ID | Requirement | Priority |
|----|-------------|----------|
| GATE-01 | Add concrete pass/fail rubrics with specific thresholds for all 11 gates | Must |
| GATE-02 | Add FAIL examples for every gate showing what clear failure looks like | Must |
| GATE-03 | Add edge case resolution guide documenting gray area handling | Must |
| GATE-04 | Enhance Gates 5, 10, 11 with explicit scoring criteria (currently vague/subjective) | Must |

### Input Handling (INPUT)

| ID | Requirement | Priority |
|----|-------------|----------|
| INPUT-01 | Validate project path exists and is accessible before audit begins | Must |
| INPUT-02 | Detect and handle missing README gracefully with clear guidance | Must |
| INPUT-03 | Provide specific error messages for common failure modes (permission denied, not a directory, no project files) | Should |

### Audit Modes (MODE)

| ID | Requirement | Priority |
|----|-------------|----------|
| MODE-01 | Support quick audit mode (5 gates: 1, 3, 4, 5, 9) for rapid triage | Should |
| MODE-02 | Generate quick mode template with abbreviated report structure | Should |
| MODE-03 | Support auto-analyze mode that reads README/package.json to pre-populate gate context | Should |
| MODE-04 | Auto-analyze suggests answers but requires human verification before scoring | Should |

### Output Quality (OUTPUT)

| ID | Requirement | Priority |
|----|-------------|----------|
| OUTPUT-01 | Generate priority matrix showing which gates to fix first based on impact | Should |
| OUTPUT-02 | Link failed gates to skills/tools that can help fix them (e.g., /prep-repo for Gate 7) | Should |
| OUTPUT-03 | Provide effort estimates for fixing each failed gate | Could |

### Architecture (ARCH)

| ID | Requirement | Priority |
|----|-------------|----------|
| ARCH-01 | Modularize skill into separate files (orchestration, gate definitions, examples, edge cases) | Must |
| ARCH-02 | Create config/gates-full.md for 11-gate definitions | Must |
| ARCH-03 | Create config/gates-quick.md for 5-gate quick mode subset | Should |
| ARCH-04 | Create guides/GATE-EXAMPLES.md with pass/fail/borderline examples | Must |
| ARCH-05 | Create guides/EDGE-CASES.md with gray area resolution rules | Must |
| ARCH-06 | Create guides/AUTO-ANALYZE.md with project file parsing heuristics | Should |
| ARCH-07 | Create guides/INPUT-VALIDATION.md with error handling patterns | Should |
| ARCH-08 | Create reference/TEMPLATE-SPEC.md documenting all template variables | Should |

### History (HIST)

| ID | Requirement | Priority |
|----|-------------|----------|
| HIST-01 | Support saving audit reports to .planning/audits/ for comparison | Could |
| HIST-02 | Show score delta when re-auditing a previously audited project | Could |

## v2 (Deferred)

| ID | Requirement | Rationale for Deferral |
|----|-------------|------------------------|
| v2-01 | Multi-user/team auditing | Complexity outweighs value for solo use |
| v2-02 | Competitor database for Gate 2 comparisons | Requires ongoing maintenance |
| v2-03 | Cryptographic signing of audits | Overkill for current use case |
| v2-04 | Caching/persistence layer | Skill must remain stateless |
| v2-05 | Partial completion (save/resume mid-audit) | Nice-to-have, not core |
| v2-06 | Confidence scoring per gate | Adds complexity without clear benefit |
| v2-07 | Custom thresholds per project type | Wait for usage patterns |
| v2-08 | Interactive mode with clarification prompts | Added friction for power users |

## Traceability

| Requirement | Phase | Status |
|-------------|-------|--------|
| ARCH-01 | Phase 1 | Pending |
| ARCH-02 | Phase 1 | Pending |
| ARCH-04 | Phase 1 | Pending |
| ARCH-05 | Phase 1 | Pending |
| GATE-01 | Phase 2 | Pending |
| GATE-02 | Phase 2 | Pending |
| GATE-03 | Phase 2 | Pending |
| GATE-04 | Phase 2 | Pending |
| INPUT-01 | Phase 3 | Pending |
| INPUT-02 | Phase 3 | Pending |
| INPUT-03 | Phase 3 | Pending |
| ARCH-07 | Phase 3 | Pending |
| MODE-01 | Phase 4 | Pending |
| MODE-02 | Phase 4 | Pending |
| ARCH-03 | Phase 4 | Pending |
| MODE-03 | Phase 5 | Pending |
| MODE-04 | Phase 5 | Pending |
| ARCH-06 | Phase 5 | Pending |
| OUTPUT-01 | Phase 6 | Pending |
| OUTPUT-02 | Phase 6 | Pending |
| OUTPUT-03 | Phase 6 | Pending |
| ARCH-08 | Phase 6 | Pending |
| HIST-01 | Phase 7 | Pending |
| HIST-02 | Phase 7 | Pending |

---
*Last updated: 2026-01-22*
