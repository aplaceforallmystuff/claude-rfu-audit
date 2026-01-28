# RFU Audit Enhancement

## What This Is

A comprehensive enhancement to the RFU Audit skill — the 11-gate validation framework that forces you to prove a project is "Really Fucking Useful" before investing significant effort. v1.0 delivered objective scoring rubrics, quick mode triage, auto-analyze extraction, actionable priority matrices, and audit history tracking.

## Core Value

**Consistent, actionable audit results.** Two auditors score the same project similarly, and the output tells you exactly what to fix — not just what failed.

## Current State (v1.0 shipped)

**Tech stack:** Claude Code skill system (markdown-based, no external dependencies)

**Artifact structure:**
```
skill/
├── SKILL.md (316 lines) - Orchestrator
├── config/
│   ├── gates-full.md (836 lines) - 11-gate definitions
│   └── gates-quick.md (183 lines) - 5-gate subset
├── guides/
│   ├── AUDIT-HISTORY.md - History tracking
│   ├── AUTO-ANALYZE.md - Extraction heuristics
│   ├── EDGE-CASES.md - 27 resolution rules
│   ├── GATE-EXAMPLES.md - 25 FAIL examples
│   ├── INPUT-VALIDATION.md - Error handling
│   └── PRIORITY-MATRIX.md - Impact-effort ranking
├── templates/
│   ├── audit-report.md - Full 11-gate template
│   └── audit-report-quick.md - Quick 5-gate template
└── reference/
    └── TEMPLATE-SPEC.md - 123 template variables
```

**Usage modes:**
- `/rfu-audit [path]` — Full 11-gate audit (45-90 min)
- `/rfu-audit --quick [path]` — Quick 5-gate triage (5-10 min)
- `/rfu-audit --auto-analyze [path]` — Extract context from project files
- `/rfu-audit --history [path]` — View audit timeline with deltas

## Requirements

### Validated

- ✓ 11-gate framework defined with pass/fail criteria — v1.0
- ✓ Template-based report output — v1.0
- ✓ Skill invocation via `/rfu-audit [path]` — v1.0
- ✓ Objective rubrics with measurable thresholds (all gates) — v1.0
- ✓ FAIL examples for every gate (25 total) — v1.0
- ✓ Edge case resolution guide (27 rules) — v1.0
- ✓ Input validation with actionable error messages — v1.0
- ✓ Quick audit mode (5 gates, 5-10 min) — v1.0
- ✓ Auto-analyze mode (README/package.json extraction) — v1.0
- ✓ Priority matrix ranking failed gates by impact — v1.0
- ✓ Fix resource linking for failed gates — v1.0
- ✓ Effort estimates (quick/medium/involved) — v1.0
- ✓ Audit history with score deltas — v1.0

### Active

(No active requirements — v1.0 shipped, observing usage patterns)

### Out of Scope

- Multi-user/team auditing — complexity outweighs value for solo use
- Competitor database for Gate 2 — requires ongoing maintenance
- Cryptographic signing of audits — overkill for current use case
- Caching/persistence layer — skill must remain stateless
- Partial completion (save/resume) — nice-to-have, deferred to v2

## Constraints

- **Skill architecture**: Must remain a Claude Code skill (markdown-based definition, no external dependencies)
- **Stateless**: Skill cannot persist data between invocations; history uses file-based approaches
- **Single auditor**: Designed for individual use, not team consensus
- **Manual judgment**: Gates require human reasoning; auto-analyze supplements but doesn't replace judgment

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Keep 11 gates vs. reduce | All 11 gates cover distinct concerns; none are redundant | ✓ Good |
| Quick mode = 5 gates (1, 3, 4, 5, 9) | Critical path for viability testing | ✓ Good |
| Gate 5 multi-signal (4 signals, 3+ pass) | Converts "honest yes" to countable evidence | ✓ Good |
| Gate 10 concentration ratio (K/N ≤ 0.30) | Replaces "concentrated value" with math | ✓ Good |
| Gate 11 commitment signals (5 of 6) | Observable evidence vs. subjective regret | ✓ Good |
| Auto-analyze via project reading | Claude reads files; no new tooling needed | ✓ Good |
| History via file output | Save to .planning/audits/ for comparison | ✓ Good |
| Impact-effort priority matrix | HIGH: 1, 3, 4, 5, 7, 11 prioritized | ✓ Good |

---
*Last updated: 2026-01-28 after v1.0 milestone*
