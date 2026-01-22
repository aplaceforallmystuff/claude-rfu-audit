# RFU Audit Enhancement

## What This Is

A comprehensive enhancement to the RFU Audit skill — the 11-gate validation framework that forces you to prove a project is "Really Fucking Useful" before investing significant effort. This enhancement makes the skill self-documenting, produces consistent scores across auditors, and generates actionable output that tells you exactly what to fix.

## Core Value

**Consistent, actionable audit results.** Two auditors should score the same project similarly, and the output should tell you exactly what to fix — not just what failed.

## Requirements

### Validated

- ✓ 11-gate framework defined with pass/fail criteria — existing
- ✓ Template-based report output — existing
- ✓ Skill invocation via `/rfu-audit [path]` — existing
- ✓ Integration references to wisdom skills — existing
- ✓ Scoring bands (11/11, 9-10, 6-8, 0-5) — existing

### Active

- [ ] Gate clarity: Add concrete examples, edge cases, and gray area handling for all gates (especially 5, 10, 11)
- [ ] Failed audit examples: Show what FAIL looks like for each gate, not just PASS
- [ ] Input validation: Handle missing README, bad paths, incomplete projects gracefully with clear error messages
- [ ] Quick audit mode: 5-gate fast triage before committing to full 11-gate audit
- [ ] Auto-analyze mode: Read README/package.json to pre-populate gate context and suggest answers
- [ ] Audit history: Basic tracking/comparison when re-auditing the same project
- [ ] Priority matrix: Show which gates to fix first based on impact
- [ ] Fix integration: Link failed gates to skills/tools that can help fix them

### Out of Scope

- Multi-user/team auditing — complexity outweighs value for v1
- Competitor database — requires ongoing maintenance, not core to audit utility
- Cryptographic signing of audits — overkill for current use case
- Caching/persistence layer — skill should remain stateless

## Context

**Current state:** The skill exists and works but has quality issues identified in `.planning/codebase/CONCERNS.md`:
- Gates 5, 10, 11 are vague/subjective
- No examples of failed audits
- No handling for bad input
- Manual and slow process
- Reports tell you what failed but not what to do about it

**Technical environment:** Claude Code skill system. Skill definition in `skill/SKILL.md`, output template in `skill/templates/audit-report.md`. No executable code — all logic is in the skill definition that guides Claude's behavior.

**User context:** Solopreneurs and indie developers who want to validate project ideas before investing significant time. Need quick, honest feedback on whether something is worth building.

## Constraints

- **Skill architecture**: Must remain a Claude Code skill (markdown-based definition, no external dependencies)
- **Stateless**: Skill cannot persist data between invocations; any "history" must use file-based approaches
- **Single auditor**: Designed for individual use, not team consensus
- **Manual judgment**: Gates require human reasoning; auto-analyze supplements but doesn't replace judgment

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Keep 11 gates vs. reduce | All 11 gates cover distinct concerns; none are redundant | — Pending |
| Quick mode = 5 gates | Gates 1, 3, 4, 5, 9 form minimal viable triage | — Pending |
| Auto-analyze via project reading | Claude already reads files; no new tooling needed | — Pending |
| History via file output | Save audit reports to .planning/audits/ for comparison | — Pending |

---
*Last updated: 2026-01-22 after initialization*
