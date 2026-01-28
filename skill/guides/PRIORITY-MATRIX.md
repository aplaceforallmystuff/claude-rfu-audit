# Priority Matrix Generation Guide

This document specifies how to transform failed gates into a prioritized action plan. Use this guide after scoring all gates and before generating the audit report.

---

## 1. Overview

**Purpose:** Transform failed gates into a prioritized fix list that tells users exactly what to do first and why.

**When to use:** After all gate scoring is complete, before generating the Priority Matrix section of the report.

**Applies to:**
- Full audit mode (11 gates)
- Quick audit mode (5 gates)

**Outcome:** Users see their top priority fixes with effort estimates and actionable resources, not just a list of failures.

---

## 2. Priority Ranking Algorithm

Rank failed gates using an impact-effort matrix. HIGH impact + LOW effort = quick wins that should be fixed first.

### Ranking Order

1. **HIGH impact + quick effort** (Quick wins - fix these immediately)
2. **HIGH impact + medium/involved effort** (Strategic investments - plan these)
3. **MEDIUM impact + quick effort** (While you're at it)
4. **Everything else** (Evaluate ROI before investing time)

### Impact Assessment

| Impact Level | Gates | Rationale |
|--------------|-------|-----------|
| **HIGH** | 1, 3, 4, 5, 7, 11 | Core viability (1, 5, 11) or first impression/adoption barrier (3, 4, 7) |
| **MEDIUM** | 2, 6, 8, 9, 10 | Quality of experience (2, 6, 8) or optimization (9, 10) |

**HIGH impact gates explained:**
- **Gate 1 (5 Whys):** Affects core viability - if no bedrock need, nothing else matters
- **Gate 3 (10-Minute):** First barrier to adoption - users leave before experiencing value
- **Gate 4 (Bartender):** First impression - users don't understand value proposition
- **Gate 5 (Wallet):** Market validation - no willingness to pay signals no real need
- **Gate 7 (Friction):** Adoption barrier - friction prevents users from getting value
- **Gate 11 (Regret):** Fundamental commitment - project may be abandoned

**MEDIUM impact gates explained:**
- **Gate 2 (Inversion):** Quality metric - convenience vs. necessity
- **Gate 6 (JTBD):** Quality metric - clarity of purpose
- **Gate 8 (Second-Order):** Quality metric - long-term consequences
- **Gate 9 (Occam's):** Optimization - simplicity/complexity balance
- **Gate 10 (Pareto):** Optimization - feature focus

### Dependency Handling

If Gate X requires Gate Y to be fixed first, Gate Y ranks higher regardless of its individual impact-effort score.

**Known dependencies:**
- Gate 4 (Bartender) often depends on Gate 1 (5 Whys) - can't explain value if bedrock need unclear
- Gate 10 (Pareto) often depends on Gate 1 (5 Whys) - can't identify core feature without clear purpose
- Gate 6 (JTBD) often depends on Gate 5 (Wallet) - JTBD clarity improves after user validation

**Dependency notation in output:**
- **Unlocks:** "Fixing this helps with Gate X" (when fix enables other fixes)
- **Blocked by:** "Must fix Gate Y first" (when gate depends on another)

---

## 3. Gate-Specific Effort Defaults

| Gate | Name | Default Effort | Rationale |
|------|------|---------------|-----------|
| 1 | 5 Whys | medium | Rethinking positioning requires analysis and potentially user research |
| 2 | Inversion | involved | Often needs discovering or creating new unique value |
| 3 | 10-Minute Test | medium | Installation/UX improvements vary - could be docs or architecture |
| 4 | Bartender Test | quick | Text rewrite in README - one sentence, no code changes |
| 5 | Wallet Test | medium | Requires validation conversations with real people |
| 6 | JTBD | medium | Statement refinement needs thought and user context |
| 7 | Friction Audit | medium | Varies by friction source - can be quick (docs) or involved (architecture) |
| 8 | Second-Order | medium | Often involves architectural considerations about lock-in/dependencies |
| 9 | Occam's Razor | involved | Removing features is hard - requires decisions and potentially significant refactoring |
| 10 | Pareto | medium | Feature focus requires decisions about what to emphasize |
| 11 | Regret Minimization | involved | Fundamental commitment question - if this fails, may need to reconsider the project |

---

## 4. Effort Override Rules

Override defaults based on specific audit findings.

### Gate 1: 5 Whys
- **Default:** medium
- **Override to quick:** If chain is almost there (one more why gets to bedrock)
- **Override to involved:** If entire product positioning is wrong and needs user research

### Gate 3: 10-Minute Test
- **Default:** medium
- **Override to quick:** If only missing a demo script or example
- **Override to involved:** If requires new architecture (Docker setup, API abstraction)

### Gate 4: Bartender Test
- **Default:** quick
- **Override to medium:** If entire value proposition is wrong (not just wording)
- **Override to involved:** If project doesn't know its audience yet

### Gate 7: Friction Audit
- **Default:** medium
- **Override to quick:** If only documentation/README issue
- **Override to involved:** If architecture change needed (e.g., add Docker Compose, redesign config)

### Gate 8: Second-Order
- **Default:** medium
- **Override to quick:** If just needs export functionality added
- **Override to involved:** If fundamental design creates lock-in

### Gate 9: Occam's Razor
- **Default:** involved
- **Override to medium:** If just removing unused features (no users depending on them)
- **Override to quick:** If complexity is in documentation, not code (over-explained simple tool)

### Gate 10: Pareto
- **Default:** medium
- **Override to quick:** If just needs README rewrite to emphasize core feature
- **Override to involved:** If genuinely can't identify what the core is (fundamental product clarity issue)

---

## 5. Resource Linking Reference

Map each gate to available resources for fixing.

| Gate | Internal Resources | Skill Invocation | Fallback Guidance |
|------|-------------------|------------------|-------------------|
| 1 | GATE-EXAMPLES.md (lines 7-67) | `/wisdom-clarify` | Continue 5 Whys until reaching bedrock (autonomy/time/money/status/connection/safety) |
| 2 | GATE-EXAMPLES.md (lines 70-127) | `/wisdom-stoic-premeditation` | Articulate specific switching costs; what would users lose if this disappeared? |
| 3 | GATE-EXAMPLES.md (lines 130-200), EDGE-CASES.md | - | Add demo script with sample output; Docker Compose quickstart pattern |
| 4 | GATE-EXAMPLES.md (lines 204-276) | - | Rewrite first line as outcome, not implementation; use grandmother test |
| 5 | GATE-EXAMPLES.md (lines 280-352) | - | Talk to 5 potential users this week; validate with real names, not archetypes |
| 6 | GATE-EXAMPLES.md (lines 354-424) | - | Use JTBD interview framework: "When [situation], I want to [motivation], so I can [outcome]" |
| 7 | GATE-EXAMPLES.md (lines 428-515), INPUT-VALIDATION.md | `/prep-repo` | Friction audit walkthrough; score each step Low/Medium/High |
| 8 | EDGE-CASES.md (lines 293-322) | `/taches-cc-resources:consider:second-order` | Map consequences before/after adoption; enumerate positives and negatives explicitly |
| 9 | GATE-EXAMPLES.md (lines 610-694), EDGE-CASES.md | `/taches-cc-resources:consider:occams-razor` | Feature necessity audit: list all features, mark essential vs. nice-to-have |
| 10 | GATE-EXAMPLES.md (lines 698-838), EDGE-CASES.md | `/taches-cc-resources:consider:pareto` | 80/20 feature analysis: what ONE feature delivers most value? |
| 11 | GATE-EXAMPLES.md (lines 841-954), EDGE-CASES.md | - | Commitment signal inventory; check for learning project disqualifiers |

### Resource Linking Format

**When internal resource exists:**
```markdown
**Resources:** See Gate 4 examples in `skill/guides/GATE-EXAMPLES.md` (lines 204-276)
```

**When skill invocation exists:**
```markdown
**Resources:** `/prep-repo` can generate Docker Compose setup and README templates
```

**When only fallback guidance:**
```markdown
**Resources:** No automated tool exists. Recommended approach:
1. [Manual step 1]
2. [Manual step 2]
3. Validate by [verification method]
```

---

## 6. Tiered Display Format

Structure the Priority Matrix output in three tiers to manage cognitive load.

### Tier 1: Top Priority Fixes (Detailed)

Show top 3 ranked gates with full context:

```markdown
### Top Priority Fixes

1. **Gate [N]: [Name]** - Effort: [level]

   **Issue:** [One-line description of what failed]

   **Why fix this first:** [Impact rationale - why this matters for adoption/viability]

   **Fix:**
   - [Specific action 1]
   - [Specific action 2]

   **Resources:** [Skill invocation, file path, or fallback guidance]

   [If unlocks other gates:]
   Unlocks: Fixing this helps with Gate X (clearer [benefit])

   [If blocked by another gate:]
   Blocked by: Must fix Gate Y first ([reason])
```

### Tier 2: Other Failed Gates (Compact)

Show remaining failures as one-liners:

```markdown
### Other Failed Gates

- **Gate [N]: [Name]** - Effort: [level] - [one-line issue summary]
- **Gate [N]: [Name]** - Effort: [level] - [one-line issue summary]
```

### Tier 3: Recommended (Borderline)

Show gates that passed but are close to failing:

```markdown
### Recommended Optimizations

While these gates passed, they're borderline and could be strengthened:

- **Gate [N]: [Name]** - [Why this is borderline and what to consider]
```

**Only include Tier 3 if:**
- Gate passed but with borderline evidence (e.g., Gate 3 at 9min 30sec when threshold is 10min)
- Gate required tie-breaker test to pass
- Auditor noted "close to failing" in scoring

---

## 7. Dependency Arrow Notation

Use text-based arrows to show relationships between gates.

### Unlocks Notation

When fixing Gate A enables or improves Gate B:

```markdown
**Unlocks:** Fixing this helps with Gate 4 (clearer value explanation) and Gate 6 (JTBD clarity)
```

### Blocked By Notation

When Gate A cannot be meaningfully fixed until Gate B is addressed:

```markdown
**Blocked by:** Must fix Gate 1 first (need to understand bedrock need before identifying core feature)
```

### Common Dependency Patterns

| Upstream Gate | Downstream Gates | Relationship |
|---------------|------------------|--------------|
| Gate 1 (5 Whys) | Gates 4, 6, 10 | Bedrock need clarity enables value explanation, JTBD, and feature focus |
| Gate 5 (Wallet) | Gate 6 | User validation improves JTBD accuracy |
| Gate 7 (Friction) | Gate 3 | Friction reduction often improves 10-minute test |
| Gate 9 (Occam's) | Gate 10 | Removing unnecessary features clarifies Pareto concentration |

---

## 8. All-Pass Report Handling

When all gates pass, don't show empty Priority Matrix sections.

### Check for Borderline Gates

After all gates pass, review scoring notes for:
- Gates that required tie-breaker tests
- Gates scored at exact threshold (e.g., friction score = 9)
- Gates with "close to failing" notes

### If Borderline Gates Exist

```markdown
## Priority Matrix

All gates passed! Project is RFU Certified.

### Recommended Optimizations

While all gates passed, these areas could be strengthened:

- **Gate 3: 10-Minute Test** - Passed at 9min 30sec. Consider instant demo mode to get under 5min.
- **Gate 7: Friction Audit** - Score 9/18 (threshold is <=9). High friction in installation step could be reduced with Docker Compose.
```

### If No Borderline Gates

```markdown
## Priority Matrix

All gates passed! Project is RFU Certified.

No borderline gates identified - strong performance across all criteria.
```

**Do not:**
- Show empty "Top Priority Fixes" or "Other Failed Gates" sections
- Pad with congratulations text
- Invent recommendations that weren't in the scoring

---

## 9. Quick Mode Specifics

Quick mode audits 5 gates instead of 11. The priority matrix format remains identical.

### Quick Mode Gates

| Gate | Name | Impact | Default Effort |
|------|------|--------|---------------|
| 1 | 5 Whys | HIGH | medium |
| 3 | 10-Minute Test | HIGH | medium |
| 4 | Bartender Test | HIGH | quick |
| 5 | Wallet Test | HIGH | medium |
| 9 | Occam's Razor | MEDIUM | involved |

### Quick Mode Ranking

Same algorithm applies - rank by impact then effort, with dependencies.

### Quick Mode Dependencies

- Gate 4 (Bartender) often depends on Gate 1 (5 Whys)
- Gate 9 (Occam's) often depends on Gate 1 (5 Whys)

### Quick Mode Output

Use same tiered display format:
- **Tier 1:** Top 2 failed gates (detailed) - fewer gates means fewer top priority
- **Tier 2:** Remaining failed gates (compact)
- **Tier 3:** Borderline gates if any exist

---

## 10. Example Priority Matrix Output

### Example: 3 Failed Gates

```markdown
## Priority Matrix

### Top Priority Fixes

1. **Gate 4: Bartender Test** - Effort: Quick (30 minutes)

   **Issue:** README says "A distributed caching layer with eventual consistency guarantees"

   **Why fix this first:** HIGH impact - first impression determines whether users investigate further. Jargon-free explanation increases conversion from visitor to user.

   **Fix:**
   - Rewrite first line as: "It remembers your data so your website loads faster"
   - Move technical details below the fold
   - Test with grandmother/bartender test

   **Resources:** See Gate 4 examples in `skill/guides/GATE-EXAMPLES.md` (lines 204-276)

2. **Gate 7: Friction Audit** - Effort: Quick (1-2 hours)

   **Issue:** Installation requires 25 minutes (Docker + Redis + API key signup)

   **Why fix this first:** HIGH impact - installation friction is #1 reason users abandon tools before experiencing value.

   **Fix:**
   - Provide Docker Compose one-command setup
   - Include working .env.example (no debugging)
   - Add mock data mode that skips API key requirement

   **Resources:** `/prep-repo` can generate Docker Compose setup and getting-started documentation

   **Unlocks:** Fixing this helps with Gate 3 (faster 10-minute test)

3. **Gate 1: 5 Whys** - Effort: Medium (2-3 hours)

   **Issue:** Chain stops at "type safety" without reaching human impact

   **Why fix this first:** HIGH impact - clarifies value proposition for all other messaging.

   **Fix:**
   - Continue why chain: "type safety" -> "fewer runtime errors" -> "developers ship faster" -> "teams get home on time" -> BEDROCK: Time
   - Update README to emphasize time savings, not type safety

   **Resources:** `/wisdom-clarify` skill can help extend why chains; see examples in `skill/guides/GATE-EXAMPLES.md` (lines 7-67)

   **Unlocks:** Fixing this helps with Gate 4 (clearer value explanation) and Gate 10 (feature focus clarity)

### Other Failed Gates

(None - only 3 gates failed)

### Recommended Optimizations

- **Gate 3: 10-Minute Test** - Passed at 9min 30sec. Adding instant demo mode with mock data would reduce to <5min.
```

---

## Summary

1. Score all gates first
2. Apply impact-effort matrix to rank failed gates
3. Check for dependencies and adjust ranking
4. Apply gate-specific effort defaults with overrides
5. Generate tiered display (Top Priority detailed, Others compact, Borderline recommended)
6. Include resource links with each fix
7. Handle all-pass and quick mode cases appropriately

The goal: Users read the Priority Matrix and know exactly what to fix first and how.
