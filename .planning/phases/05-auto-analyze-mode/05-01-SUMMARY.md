---
phase: 05
plan: 01
subsystem: documentation
tags: [auto-analyze, extraction, heuristics, verification, guide]

requires:
  - phase: 04
    artifact: Quick-audit-mode interface completion
    why: Foundation for friction reduction features

provides:
  - artifact: AUTO-ANALYZE.md guide
    usage: Reference for extraction heuristics and verification workflow
    quality: Complete with 568 lines covering all extraction scenarios

affects:
  - phase: 05
    plan: 02
    how: Provides extraction specifications for implementation

tech-stack:
  added: []
  patterns:
    - LLM-based structured extraction with confidence scoring
    - Human-in-the-loop verification workflow
    - Graceful degradation for partial extraction failures

key-files:
  created:
    - skill/guides/AUTO-ANALYZE.md
  modified: []

decisions:
  - what: Combined Task 1 and Task 2 into single atomic file creation
    why: Sections naturally belong together in complete guide
    alternatives: [Create file incrementally in two commits]
    chosen: Single comprehensive guide
    impact: One commit instead of two, same end result

  - what: Included 5 pitfalls instead of minimum 4
    why: Installation time misestimation is common enough to document
    alternatives: [Stop at 4 pitfalls]
    chosen: Document all identified pitfalls
    impact: More comprehensive guide for implementers

  - what: Used conservative time estimates for installation complexity
    why: Over-promising on speed leads to failed Gate 3 evaluations
    alternatives: [Use average estimates, provide ranges only]
    chosen: Conservative + range notation
    impact: Safer estimates, user expectations managed

metrics:
  lines-added: 568
  duration: 3 minutes
  completed: 2026-01-28

---

# Phase 05 Plan 01: Auto-Analyze Extraction Heuristics Summary

**One-liner:** Comprehensive extraction guide with confidence scoring, verification workflow, and graceful degradation patterns for LLM-based project analysis.

## What Was Delivered

Created `skill/guides/AUTO-ANALYZE.md` — a 568-line reference guide documenting:

1. **Field extraction mappings** - All 7 fields mapped to specific gates with source priorities
2. **Confidence scoring** - 4-level system (HIGH/MEDIUM/LOW/UNCERTAIN) with unambiguous criteria
3. **Extraction prompt template** - Full structured prompt with JSON schema and grounding constraints
4. **README heuristics** - Section identification patterns for value prop, features, installation time
5. **Verification workflow** - Human-in-the-loop interaction format with batch actions
6. **Conflict handling** - Both-values display when README and package.json disagree
7. **Pitfall documentation** - 5 common mistakes with prevention strategies
8. **Graceful degradation** - Failure handling ensuring extraction issues never block audit
9. **Explicit limitations** - Clear statement of what auto-analyze cannot do

## Tasks Completed

| Task | Name | Commit | Lines | Notes |
|------|------|--------|-------|-------|
| 1 | Create AUTO-ANALYZE.md with extraction template | c6de23e | 568 | Included all Task 2 content atomically |
| 2 | Add verification workflow and conflict handling | c6de23e | (same) | Combined with Task 1 for coherent guide |

**Rationale for combining tasks:** Both tasks contribute to a single coherent guide. Splitting into two commits would have created an incomplete intermediate state. The guide is more useful as a complete reference document.

## Field-to-Gate Mapping

| Extracted Field | Used By | Confidence Pattern |
|----------------|---------|-------------------|
| `project_name` | Report header | HIGH (package.json or README H1) |
| `one_line_description` | Gate 4 (Bartender) | HIGH if explicit, MEDIUM if summarized |
| `value_proposition` | Gates 1, 2 | MEDIUM (inferred from problem/why sections) |
| `target_users` | Gates 1, 5 | MEDIUM to LOW (often implicit in docs) |
| `key_features` | Gate 10 (Pareto) | HIGH if labeled section exists |
| `installation_time_estimate` | Gate 3 (10-Minute) | MEDIUM (calculated estimate) |
| `prerequisites` | Gate 3 | HIGH if section exists |

## Confidence Scoring System

**HIGH (✓)** - Explicit in docs, unambiguous, single source
**MEDIUM (⚠)** - Inferred from context, multiple sources agree
**LOW (❓)** - Guessed, conflicting sources, minimal info
**UNCERTAIN** - Not present, too ambiguous to extract

**Critical rule:** LOW and UNCERTAIN fields default to rejected state, requiring manual entry. This prevents automation bias.

## Extraction Prompt Design

The guide includes a full structured prompt template (lines 70-132) with:

- JSON schema for all 7 fields
- Confidence scoring per field
- Source attribution requirements
- Grounding constraint: "Extract ONLY information explicitly stated in files"
- Installation time heuristics based on prerequisite complexity

**Key insight:** Conservative time estimates prevent under-promising. Better to estimate 10 minutes and complete in 5 than promise 5 and take 15.

## Human Verification Workflow

All extracted data requires explicit approval:

1. **HIGH confidence** - Default approved, quick Enter to accept
2. **MEDIUM confidence** - Default needs review, must verify
3. **LOW confidence** - Default rejected, suggests manual entry
4. **UNCERTAIN** - No suggestion, prompts manual entry

**Batch actions:**
- 'a' = approve all HIGH confidence
- 'r' = review MEDIUM/LOW only
- 'q' = quit auto-analyze, full manual entry

**Why this matters:** Automation bias (rubber-stamping suggestions) is the #1 pitfall. Requiring explicit action prevents blind approval.

## Conflict Handling

When README and package.json disagree:

```
**README:** "Stop building things nobody wants"
**package.json:** "A Claude Code skill for project validation"
**Conflict detected:** These differ significantly
**Suggested:** Use README version (more user-facing)
```

User can choose either source or write custom. README takes priority in default suggestion (more user-facing, likely up-to-date).

## Documented Pitfalls

1. **Automation bias** - Rubber-stamping without verification → Prevention: Confidence indicators, source attribution, explicit approval required
2. **Missing info as failure** - Treating UNCERTAIN as error → Prevention: Graceful degradation to manual entry
3. **Conflicting info not flagged** - Arbitrary choice between sources → Prevention: Conflict detection and both-values display
4. **Over-extraction** - Hallucinating features not in files → Prevention: Grounding constraint in prompt, source attribution
5. **Installation time misestimation** - Optimistic estimates → Prevention: Conservative heuristics, prerequisite complexity weighting

## Graceful Degradation

Extraction failure never blocks audit:

- **Partial extraction** (1-6 fields) → Present suggestions for review, manual entry for rest
- **Zero extraction** (empty README) → Skip auto-analyze, proceed to full manual entry
- **Per-field failure** → Prompt: "Enter manually now / Fill during gate scoring / Mark N/A"

**Error message format:**
```
⚠ Could not extract "[field_name]"
Reason: [technical reason]
Why this matters: [which gates use this]
What to do: [actionable options]
```

## Limitations Documented

Explicitly states what auto-analyze cannot do:

1. **Cannot score gates** - Extraction provides context only, not judgment
2. **Cannot guarantee accuracy** - Suggestions are hypotheses, not facts
3. **Cannot replace human verification** - All suggestions must be reviewed
4. **Works best with standard README structure** - May fail on unconventional docs

**Acceptable failure modes:** README with non-standard structure, very short README (<100 words), non-English READMEs (extracts in source language).

## Implementation Notes

For Phase 05-02 implementation:

- Use provided extraction prompt template (lines 70-132)
- Implement confidence-based default states (HIGH=approved, LOW=rejected)
- Display conflicts when README/package.json differ (both-values format)
- Include "Show source" action for each extraction
- Test with minimal READMEs to verify graceful degradation
- Ensure UNCERTAIN fields don't error, just prompt manual entry

## Deviations from Plan

None - plan executed exactly as written. Both tasks completed successfully, with decision to combine them into single atomic commit for coherence.

## Next Phase Readiness

**Ready to proceed to 05-02 (Auto-Analyze Implementation):**
- ✅ Extraction heuristics documented
- ✅ Confidence scoring criteria defined
- ✅ Verification workflow specified
- ✅ Edge cases and pitfalls covered
- ✅ Prompt template ready for implementation

**No blockers or concerns.**

## Technical Debt / Future Improvements

None introduced. Guide is complete and ready for implementation reference.

## Validation Against Success Criteria

| Criterion | Status | Evidence |
|-----------|--------|----------|
| AUTO-ANALYZE.md >200 lines | ✅ | 568 lines delivered |
| All fields map to specific gates | ✅ | Table on lines 20-28 shows 7 field mappings |
| Confidence scoring unambiguous | ✅ | 4 levels with clear criteria (lines 134-213) |
| Human verification documented as required | ✅ | "REQUIRED step" language throughout, workflow on lines 321-383 |
| Graceful degradation ensures no blocking | ✅ | Section on lines 263-318, "never blocks" principle stated |
| Guide follows INPUT-VALIDATION.md patterns | ✅ | Similar structure, error message formats, edge case handling |

**All success criteria met.**

## Related Files

- Input: `05-RESEARCH.md` (extraction research)
- Input: `INPUT-VALIDATION.md` (guide format reference)
- Input: `GATE-EXAMPLES.md` (gate scoring context)
- Input: `gates-full.md` (gate definitions for field mapping)
- Output: `AUTO-ANALYZE.md` (deliverable)
- Affects: `05-02-PLAN.md` (implementation will reference this guide)

## Lessons Learned

1. **Atomic guide creation beats incremental** - Building complete guide in one pass created more coherent documentation than splitting across commits
2. **Conservative estimates prevent disappointment** - Installation time heuristics use upper bounds + ranges to manage expectations
3. **Confidence indicators prevent automation bias** - Visual symbols (✓ ⚠ ❓) communicate trustworthiness at a glance
4. **Grounding constraints reduce hallucination** - Explicit "ONLY extract from files" instruction in prompt critical for accuracy
5. **Both-values display surfaces conflicts** - Showing README vs package.json side-by-side forces user to make informed choice

## Time Breakdown

- **Task 1 (Guide creation):** 3 minutes
- **Task 2 (Verification workflow):** 0 minutes (included in Task 1)
- **Verification:** Included in execution time
- **Summary creation:** Not yet counted

**Total execution:** 3 minutes
