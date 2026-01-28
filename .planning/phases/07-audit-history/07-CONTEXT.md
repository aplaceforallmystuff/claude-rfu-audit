# Phase 7: Audit History - Context

**Gathered:** 2026-01-28
**Status:** Ready for planning

<domain>
## Phase Boundary

Track and compare RFU audits over time. Users can save audit reports, re-audit projects, and see score deltas showing which gates improved or regressed. The skill remains stateless (file-based history only).

</domain>

<decisions>
## Implementation Decisions

### Storage Format
- Location: Claude's discretion (decide based on typical audit workflow patterns)
- Naming convention: Timestamp-based (`rfu-audit-20260128-143022.md`)
- Metadata captured: Minimal — date, score, mode only
- Save behavior: Always save (default on), user deletes unwanted files

### Comparison Display
- Delta display: Inline in report (`Gate 1: PASS ↑ was FAIL`)
- Visual indicators: Arrows (↑ improved, ↓ regressed, = unchanged)
- Comparison target: Most recent audit only (no user selection of historical comparison)
- First audit handling: Silent (no comparison section, just normal audit output)

### Re-audit Workflow
- Detection: Check audits folder automatically (no explicit flag needed)
- Project matching: Store both name and path, prefer name match with path fallback
- Mode separation: Separate histories for quick and full modes
- N/A handling: Show as 'NEW' or 'REMOVED' rather than treating as improvement/regression

### History Scope
- Retention: Unlimited (keep all audits, user cleans manually if needed)
- History view: `--history` flag shows table with date, mode, score, file path
- Cleanup: No reset command needed (manual deletion for cleanup)

### Claude's Discretion
- Storage location choice (in-project vs central)
- Implementation of automatic history detection
- Exact format of inline delta display
- Project name extraction logic for matching

</decisions>

<specifics>
## Specific Ideas

- History comparison should feel lightweight — deltas augment the report, not dominate it
- First audit should just work normally without mentioning history
- Mode separation prevents confusing quick (5-gate) and full (11-gate) comparisons

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope

</deferred>

---

*Phase: 07-audit-history*
*Context gathered: 2026-01-28*
