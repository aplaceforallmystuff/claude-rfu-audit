# Phase 6: Actionable Output - Context

**Gathered:** 2026-01-28
**Status:** Ready for planning

<domain>
## Phase Boundary

Transform audit reports so failed gates tell users exactly what to fix and how. Add priority matrix ranking fixes by impact, effort estimates for each fix, and links to relevant resources. The goal: after reading a failed audit, the user knows what to do next.

</domain>

<decisions>
## Implementation Decisions

### Priority Ranking Logic
- Claude determines ranking approach based on audit context (effort-to-impact, dependencies, etc.)
- Tiered display: top 3 fixes in detail, remaining failed gates as compact list
- Explicit dependency arrows when gates require others first (e.g., "Fix Gate 1 before Gate 5")
- Borderline gates (passing but close) appear as secondary recommendations after primary failures

### Effort Estimation
- Three levels: quick / medium / involved
- Claude defines what effort means per fix (may be time-based, complexity-based, or hybrid)
- Gate-specific defaults with audit-specific overrides when findings warrant
- Brief rationale accompanies each estimate explaining why that level

### Fix Resource Linking
- Internal resources (skills, guides, templates) + curated external tools where relevant
- Specificity based on availability: exact invocation if we have a skill, category guidance otherwise
- Resources inline with each fix (not collected separately)
- Claude handles fallback when no specific resource exists

### Report Layout
- Priority matrix appears near top (immediately after summary)
- Quick mode gets same priority matrix format (just fewer gates)
- Replaces existing "Priority Fixes" section entirely
- All-pass audits show recommendations only if borderline gates exist

### Claude's Discretion
- Ranking algorithm implementation
- Effort level definitions per gate
- Resource fallback approach when no tool exists
- How to present dependency chains visually

</decisions>

<specifics>
## Specific Ideas

- Tiered display keeps focus on what matters without hiding anything
- Dependency arrows help users understand sequencing ("fix this first")
- Inline resources reduce back-and-forth ("here's what to run")
- Matrix near top means action items are immediately visible

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope

</deferred>

---

*Phase: 06-actionable-output*
*Context gathered: 2026-01-28*
