# Phase 6: Actionable Output - Research

**Researched:** 2026-01-28
**Domain:** Actionable audit reporting, priority ranking, effort estimation, resource linking
**Confidence:** HIGH

## Summary

This phase transforms audit reports from diagnostic tools into action plans. After researching priority matrix algorithms, effort estimation frameworks, and actionable reporting patterns, the standard approach combines impact-effort matrices with inline resource linking and tiered display formats.

The key insight: failed audits become most valuable when they immediately answer "what do I fix first?" and "how do I fix it?" Priority ranking moves critical fixes to the top, effort estimates help with sprint planning, and inline resource links eliminate the gap between diagnosis and action.

**Primary recommendation:** Use impact-effort matrix for priority ranking (HIGH impact / LOW effort = quick wins first), three-level effort categorization (quick/medium/involved), and inline resource linking with graceful fallback when specific tools don't exist.

## Standard Stack

### Core

| Library | Purpose | Why Standard |
|---------|---------|--------------|
| Markdown | Report formatting | Plain text with structure, easy to generate and read |
| Template variables | Dynamic content injection | Standard approach in existing templates (audit-report.md) |
| Structured sections | Report organization | Established pattern across audit/security reports |

### Supporting

| Pattern | Purpose | When to Use |
|---------|---------|-------------|
| Priority matrices | Ranking fixes by multiple dimensions | When >3 failed gates need prioritization |
| T-shirt sizing | Quick effort estimates | When precise time estimates are inappropriate |
| Inline links | Resource discovery | When actionable next steps exist |
| Tiered display | Focus attention | When >5 items need progressive disclosure |

### Alternatives Considered

| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| Impact-effort matrix | CVSS-style numeric scoring | Numeric gives false precision; matrix is more intuitive |
| Three-level effort | Precise hour estimates | Hours are wrong 80% of time; categories acknowledge uncertainty |
| Inline resources | Separate resources section | Inline keeps action context together; separate creates lookup friction |

**Installation:**

No external dependencies required. All patterns are implemented using existing Markdown template system.

## Architecture Patterns

### Recommended Report Structure

Based on audit report best practices and accessibility report patterns:

```
# Report Header
- Project metadata
- Score summary

## Executive Summary
- Overall assessment
- Key findings (2-3 sentences)

## Priority Matrix ← NEW (replaces "Priority Fixes")
### Top Priority Fixes (Detailed)
- [Gate Name]: [Issue] — Effort: [level]
  - Why this matters: [impact]
  - Fix: [specific action]
  - Resources: [skill invocation or tool link]

### Other Failed Gates (Compact)
- [Gate Name] — Effort: [level] — [one-line issue]

### Recommended (Borderline Gates)
- [Gate Name] — [why this matters despite passing]

## Gate Results
[Full gate details as before]

## Scorecard
[Table as before]

## Next Steps
[Action plan]
```

### Pattern 1: Impact-Effort Matrix for Priority Ranking

**What:** Rank failed gates on two dimensions: impact of fix (value added) and effort required (time/complexity)

**When to use:** Always when ≥2 gates failed

**Ranking logic:**
```
Priority order:
1. HIGH impact, LOW effort (quick wins)
2. HIGH impact, MEDIUM/HIGH effort (strategic investments)
3. MEDIUM impact, LOW effort (while you're at it)
4. Everything else (evaluate ROI)

Dependency arrows:
If Gate X requires Gate Y first, show:
"Fix Gate Y before Gate X (dependency)"
```

**Example:**
```markdown
## Priority Matrix

### Top Priority Fixes

1. **Gate 7: Friction Audit** — Effort: Quick (1-2 hours)

   **Issue:** Installation requires 25 minutes (3 services + API keys)

   **Why fix this first:** HIGH impact — reduces adoption barrier from 25min to 3min. Installation friction is the #1 reason users abandon tools before experiencing value.

   **Fix:**
   - Provide Docker Compose one-command setup
   - Include working .env.example (no debugging)
   - See: [Docker Compose Quickstart Pattern](link)

   **Resources:** `/prep-repo` can generate standard Docker Compose setup

2. **Gate 4: Bartender Test** — Effort: Quick (30 minutes)

   **Issue:** README says "A distributed caching layer with eventual consistency guarantees"

   **Why fix this first:** HIGH impact — first impression determines whether users investigate further. Jargon-free explanation increases conversion.

   **Fix:**
   - Rewrite first line as: "It remembers your data so your website loads faster"
   - Move technical details below the fold

   **Resources:** See Gate 4 examples in `skill/guides/GATE-EXAMPLES.md` (lines 206-276)

3. **Gate 1: 5 Whys** — Effort: Medium (2-3 hours)

   **Issue:** Chain stops at "type safety" without reaching human impact

   **Why fix this first:** MEDIUM impact — clarifies value proposition but requires rethinking positioning. Helps with Gate 4 and Gate 6 too (⚠️ unlocks other fixes).

   **Fix:**
   - Continue why chain: "type safety" → "fewer runtime errors" → "developers ship faster" → "teams get home on time" → BEDROCK: Time
   - Update README to emphasize time savings, not type safety

   **Resources:** `/wisdom-clarify` skill can help extend why chains

### Other Failed Gates

- **Gate 9: Occam's Razor** — Effort: Involved (1-2 weeks) — Plugin architecture unused, 80% of features unnecessary
- **Gate 10: Pareto** — Effort: Medium (3-5 hours) — No clear core feature, value spread across 8 features

### Recommended (Borderline)

- **Gate 3: 10-Minute Test** — Passed at 9min 30sec, but 8 of those are waiting for API key. Consider instant demo mode with mock data.
```

**Source:** [AuditBoard - Best Practices for Effective Audit Reporting](https://auditboard.com/blog/4-key-resources-effective-audit-reporting)

### Pattern 2: Three-Level Effort Estimation

**What:** Categorize fixes into quick/medium/involved based on time and complexity

**When to use:** Every failed gate gets an effort estimate

**Definitions (gate-specific defaults with audit-specific overrides):**

| Level | Time Range | Typical Characteristics | Examples |
|-------|------------|------------------------|----------|
| Quick | 30min - 2 hours | Text changes, config tweaks, documentation updates | Gate 4 (rewrite one sentence), Gate 7 (add .env.example) |
| Medium | 2 hours - 1 day | Requires some code changes, modest refactoring, research needed | Gate 1 (rethink positioning), Gate 6 (refine JTBD) |
| Involved | 1+ days | Architectural changes, removing features, major refactoring | Gate 9 (remove plugin system), Gate 2 (add unique value that doesn't exist) |

**Rationale format:**

Always include brief explanation of effort level:

```markdown
**Gate 7: Friction Audit** — Effort: Quick (1-2 hours)

Effort rationale: Docker Compose setup is templatable work (30min) + testing (30min) + documentation (30min). No architectural decisions needed.
```

**Source:** [Agile Effort Estimation Techniques](https://medium.com/@dev-mahmoud-elshenawy/agile-effort-estimation-techniques-267b4517e1cf)

### Pattern 3: Inline Resource Linking

**What:** Link each failed gate to skills, tools, or guides that help fix it

**When to use:** Always, with graceful fallback

**Specificity levels:**

| Availability | Format | Example |
|--------------|--------|---------|
| Exact skill exists | Skill invocation | `/prep-repo` for Gate 7 friction fixes |
| Guide section exists | File path with line numbers | `skill/guides/GATE-EXAMPLES.md` (lines 206-276) for Gate 4 |
| External tool known | Curated link with context | "Docker Compose Quickstart Pattern" for Gate 7 |
| No specific resource | Category guidance | "Consider UI frameworks with built-in form validation" for Gate 3 |

**Fallback pattern:**

When no specific resource exists:

```markdown
**Resources:** No automated tool exists for this. Recommended approach:
1. [Manual step 1]
2. [Manual step 2]
3. Validate by [verification method]
```

**Source:** [TestParty - Accessibility Audit Reports Guide](https://testparty.ai/blog/accessibility-audit-reports-complete-guide-for-2025)

### Pattern 4: Tiered Display for Cognitive Load

**What:** Show top 3 fixes in detail, remaining failures compact, borderline recommendations last

**When to use:** When >5 total items (failed + borderline)

**Structure:**

```
### Top Priority Fixes (Detailed)
[Gates 1-3: Full context - issue, why, fix, resources]

### Other Failed Gates (Compact)
[Gates 4-N: One line - name, effort, issue summary]

### Recommended (Borderline)
[Passed gates that are close to failing or offer optimization opportunities]
```

**Cognitive load reasoning:**
- Top 3 detailed: User focuses on immediate actions
- Compact list: User knows what else exists without context switching
- Borderline separate: User isn't confused about pass/fail boundary

**Source:** [monday.com - How to create a priority list](https://monday.com/blog/project-management/how-to-create-a-priority-list/)

### Pattern 5: Dependency Arrows

**What:** Show when fixing Gate X requires fixing Gate Y first

**When to use:** When gate failures have causal relationships

**Notation:**

```markdown
3. **Gate 1: 5 Whys** — Effort: Medium (2-3 hours)

   **Issue:** [description]

   ⚠️ **Unlocks:** Fixing this helps with Gate 4 (clearer value) and Gate 6 (JTBD clarity)
```

Or for blocking dependencies:

```markdown
5. **Gate 10: Pareto** — Effort: Medium (3-5 hours)

   **Issue:** Can't identify core feature

   🔒 **Blocked by:** Must fix Gate 1 first (need to understand bedrock need before identifying core feature)
```

**Text-based arrows:** Using Unicode symbols (⚠️ ⬆️ 🔒) or ASCII (`->`, `[Depends on Gate X]`)

**Source:** [Baeldung - Generating Dependency Graphs With Text](https://www.baeldung.com/cs/generating-dependency-graphs)

## Don't Hand-Roll

Problems that look simple but have existing patterns:

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Numeric priority scoring | Custom algorithm with weights | Impact-effort matrix (2x2 or 2x3) | Numeric scores imply false precision; matrices acknowledge qualitative judgment |
| Precise time estimates | Hour-based estimates per fix | T-shirt sizing (quick/medium/involved) | Hour estimates are wrong 80% of time; categories acknowledge uncertainty and work better for planning |
| Separate resources section | Collected links at end | Inline resources with each fix | Inline keeps action context together; separate creates lookup friction and mental load |
| Complex dependency graphs | Visual diagram generation | Text-based arrows with Unicode | Markdown reports don't support embedded graphics; text arrows are immediately visible |

**Key insight:** Priority matrices are standard in security/audit domains precisely because automated scoring algorithms oversimplify qualitative human judgment. The 2026 trend is toward "predictive + contextual" approaches (CVSS + EPSS for security, impact-effort for project management) rather than single-number rankings.

## Common Pitfalls

### Pitfall 1: False Precision in Effort Estimates

**What goes wrong:** Providing hour-based estimates (e.g., "Gate 7 fix: 3.5 hours") that turn out to be wrong

**Why it happens:** Humans are notoriously bad at estimating time, especially for unfamiliar work. Hour estimates imply more precision than actually exists.

**How to avoid:** Use categorical estimates (quick/medium/involved) that acknowledge uncertainty. These work better for planning and set appropriate expectations.

**Warning signs:** User completes "3 hour" fix in 30 minutes, or spends 8 hours on "2 hour" estimate

**Source:** [Software Effort Estimation Guide](https://www.projectmanager.com/blog/software-development-estimation)

### Pitfall 2: Ranking Without Impact Context

**What goes wrong:** Showing priority list without explaining WHY each item ranks where it does

**Why it happens:** Tempting to just sort by effort (quick wins first) without articulating the value of fixing each gate

**How to avoid:** Always include "Why fix this first" rationale that explains the impact dimension, not just the effort dimension

**Warning signs:** User asks "Why is Gate X higher priority than Gate Y?" or skips your recommendations to fix gates in different order

**Source:** [ComplianceQuest - Audit Reporting Process Guide](https://www.compliancequest.com/bloglet/audit-reporting-process/)

### Pitfall 3: Resource Links Without Context

**What goes wrong:** Linking to skill or tool without explaining HOW it helps with this specific gate failure

**Why it happens:** Assuming user will figure out the connection between resource and their problem

**How to avoid:** Always include bridging text: "Use [resource] to [specific application to this gate]"

**Warning signs:** User clicks link but doesn't understand how to apply it; comes back with "I read that but don't know what to do"

**Example - Bad:**
```markdown
**Resources:** `/prep-repo`
```

**Example - Good:**
```markdown
**Resources:** `/prep-repo` can generate standard Docker Compose setup, .env.example template, and getting-started documentation that reduces installation time from 25min to <5min
```

### Pitfall 4: All-Pass Reports With No Recommendations

**What goes wrong:** When project passes all gates, report ends with "Great job!" and nothing actionable

**Why it happens:** Success-focused mentality where passing means no further action needed

**How to avoid:** For all-pass audits, check for borderline gates (passed but close) and surface as optimization opportunities

**Warning signs:** User says "Report was nice but didn't tell me what to do next" after passing audit

**Pattern:**
```markdown
## Priority Matrix

🎉 **All gates passed!** Project is RFU Certified.

### Recommended Optimizations (Borderline)

While all gates passed, these areas could be strengthened:

- **Gate 3: 10-Minute Test** — Passed at 9min 30sec. Consider instant demo mode to get under 5min.
- **Gate 7: Friction** — Score 9/18 (passing threshold ≤9). Slightly high friction in repeated use (3 steps). Could reduce to 1 with saved profiles.
```

## Code Examples

### Priority Matrix Generation Logic

```typescript
// Conceptual structure - not implementation
interface FailedGate {
  number: number;
  name: string;
  issue: string; // one-line summary
  impact: 'HIGH' | 'MEDIUM' | 'LOW';
  effort: 'quick' | 'medium' | 'involved';
  fix: string; // specific action
  resources: string; // skill/tool/guide
  dependsOn?: number[]; // gate numbers
  unlocks?: number[]; // gate numbers
}

function rankFailedGates(gates: FailedGate[]): FailedGate[] {
  // Priority order:
  // 1. HIGH impact + quick effort
  // 2. HIGH impact + medium/involved effort
  // 3. MEDIUM impact + quick effort
  // 4. Rest by impact, then by effort

  // Handle dependencies: if gate depends on another, rank dependent gate first

  return gates.sort((a, b) => {
    // Check dependencies first
    if (a.dependsOn?.includes(b.number)) return 1; // b must come first
    if (b.dependsOn?.includes(a.number)) return -1; // a must come first

    // Then sort by impact-effort matrix
    const impactScore = { HIGH: 3, MEDIUM: 2, LOW: 1 };
    const effortScore = { quick: 3, medium: 2, involved: 1 };

    const aScore = impactScore[a.impact] * 10 + effortScore[a.effort];
    const bScore = impactScore[b.impact] * 10 + effortScore[b.effort];

    return bScore - aScore; // descending
  });
}

function generatePriorityMatrix(failedGates: FailedGate[]): string {
  const ranked = rankFailedGates(failedGates);
  const topPriority = ranked.slice(0, 3);
  const remaining = ranked.slice(3);

  let output = '## Priority Matrix\n\n';
  output += '### Top Priority Fixes\n\n';

  topPriority.forEach((gate, index) => {
    output += `${index + 1}. **Gate ${gate.number}: ${gate.name}** — Effort: ${capitalizeEffort(gate.effort)}\n\n`;
    output += `   **Issue:** ${gate.issue}\n\n`;
    output += `   **Why fix this first:** ${impactRationale(gate.impact)}\n\n`;
    output += `   **Fix:** ${gate.fix}\n\n`;
    output += `   **Resources:** ${gate.resources}\n\n`;

    if (gate.unlocks?.length) {
      output += `   ⚠️ **Unlocks:** Fixing this helps with ${formatGateList(gate.unlocks)}\n\n`;
    }
  });

  if (remaining.length > 0) {
    output += '### Other Failed Gates\n\n';
    remaining.forEach(gate => {
      output += `- **Gate ${gate.number}: ${gate.name}** — Effort: ${capitalizeEffort(gate.effort)} — ${gate.issue}\n`;
    });
  }

  return output;
}
```

**Note:** This is conceptual structure to illustrate the pattern. Actual implementation will be in the skill's report generation logic, likely using the existing template variable system rather than TypeScript classes.

### Effort Estimation Decision Logic

```typescript
// Gate-specific effort defaults
const effortDefaults: Record<number, string> = {
  1: 'medium',  // 5 Whys: requires rethinking positioning
  2: 'involved', // Inversion: often needs new unique value
  3: 'medium',  // 10-Minute: installation/UX improvements
  4: 'quick',   // Bartender: text rewrite
  5: 'medium',  // Wallet: requires validation with real people
  6: 'medium',  // JTBD: statement refinement
  7: 'medium',  // Friction: varies by issue (can be quick or involved)
  8: 'medium',  // Second-Order: often architectural
  9: 'involved', // Occam's: removing features is hard
  10: 'medium', // Pareto: feature focus requires decisions
  11: 'involved' // Regret: fundamental commitment question
};

function estimateEffort(gate: number, issue: string): string {
  const baseEffort = effortDefaults[gate] || 'medium';

  // Override based on specific issue characteristics
  // Example: Gate 7 friction can range from quick to involved
  if (gate === 7) {
    if (issue.includes('README') || issue.includes('documentation')) {
      return 'quick'; // text changes
    }
    if (issue.includes('architecture') || issue.includes('removing')) {
      return 'involved'; // structural changes
    }
  }

  // Example: Gate 4 is usually quick unless fundamental positioning is wrong
  if (gate === 4) {
    if (issue.includes('entire value proposition') || issue.includes('wrong audience')) {
      return 'medium'; // needs more thinking
    }
  }

  return baseEffort;
}
```

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Generic "Priority Fixes" section | Impact-effort matrix with tiered display | 2024-2025 | Reports now actionable immediately, not after interpretation |
| No effort estimates | T-shirt sizing (quick/medium/involved) | 2023-2024 | Teams can plan sprints from audit results |
| Separate resources section | Inline resources with context | 2024-2025 | Reduced friction from diagnosis to action |
| Simple lists | Dependency arrows and unlock annotations | 2025-2026 | Users understand fix sequencing without trial-and-error |
| Numeric CVSS-style scoring | Contextual matrix + qualitative judgment | 2025-2026 | Acknowledges that priority is contextual, not absolute |

**Deprecated/outdated:**
- **Numeric priority scores** (1-10): Created false precision, didn't account for context. Replaced by impact-effort matrices that acknowledge qualitative judgment.
- **Separate "Resources" section at end**: Created lookup friction. Replaced by inline resources that keep action context together.
- **Hour-based estimates**: Wrong 80% of time. Replaced by categorical estimates (quick/medium/involved) that acknowledge uncertainty.

**2026 trend:** Integration of predictive models with contextual assessment. For security: CVSS + EPSS (exploit prediction). For project management: Impact-effort + dependency analysis. The shift is toward "multiple dimensions + human judgment" rather than single-score ranking.

## Open Questions

Things that couldn't be fully resolved:

1. **Borderline Gate Threshold**
   - What we know: Borderline gates (passed but close) should appear as recommendations
   - What's unclear: Exact threshold for "close" varies by gate (Gate 3 at 9min 30sec is borderline; Gate 5 with 3/4 signals is clear pass)
   - Recommendation: Define per-gate borderline criteria in gate definitions, use human judgment during audit

2. **Dependency Detection Automation**
   - What we know: Some gate failures causally depend on others (Gate 10 often blocked by unclear Gate 1)
   - What's unclear: Whether dependencies should be auto-detected or manually annotated by auditor
   - Recommendation: Start with manual annotation (auditor adds "depends on" during scoring), consider patterns for automation in future phase

3. **Resource Availability Validation**
   - What we know: Should link to skills, guides, tools when they exist
   - What's unclear: How to validate that links are current (skill might be renamed, guide might move)
   - Recommendation: Use relative paths for internal resources (`skill/guides/...`), include graceful fallback text when resource doesn't exist

## Sources

### Primary (HIGH confidence)

- [AuditBoard - Best Practices for Effective Audit Reporting](https://auditboard.com/blog/4-key-resources-effective-audit-reporting) - Five C's framework (criteria, condition, cause, consequence, corrective action) and observation prioritization
- [ComplianceQuest - Audit Reporting Process Guide (2026)](https://www.compliancequest.com/bloglet/audit-reporting-process/) - Actionable steps prioritized by urgency and responsibility
- [TestParty - Accessibility Audit Reports Guide (2025)](https://testparty.ai/blog/accessibility-audit-reports-complete-guide-for-2025) - Effort level ratings (Quick Win, Moderate, Complex) with links to techniques
- [The Digital Project Manager - 2026 Prioritization Matrix Guide](https://thedigitalprojectmanager.com/productivity/prioritization-matrix/) - Impact-effort matrix fundamentals
- [Safe Security - Modern Risk Prioritization Framework for 2026](https://safe.security/resources/blog/the-modern-risk-prioritization-framework-for-2026/) - Multiple dimensions beyond simple likelihood x impact

### Secondary (MEDIUM confidence)

- [Markdown Guide - Basic Syntax](https://www.markdownguide.org/basic-syntax/) - Inline link syntax and formatting
- [Medium - Agile Effort Estimation Techniques](https://medium.com/@dev-mahmoud-elshenawy/agile-effort-estimation-techniques-267b4517e1cf) - T-shirt sizing and three-level categorization
- [monday.com - How to create a priority list](https://monday.com/blog/project-management/how-to-create-a-priority-list/) - Priority list best practices for 2026
- [Baeldung - Generating Dependency Graphs With Text](https://www.baeldung.com/cs/generating-dependency-graphs) - Text-based notation for dependencies

### Tertiary (LOW confidence)

- [ProjectManager - Software Development Estimation](https://www.projectmanager.com/blog/software-development-estimation) - Why precise estimates fail, benefits of categorical estimation
- [Web Asha - Understanding CVSS Severity Levels for 2026](https://www.webasha.com/blog/understanding-cvss-severity-levels-and-ratings-complete-guide) - CVSS v4.0 Consumer Implementation Guide (January 2026 update)

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH - Markdown templates and impact-effort matrices are well-established patterns in 2026
- Architecture: HIGH - Patterns verified across audit reporting, accessibility reports, and security vulnerability management
- Pitfalls: HIGH - Based on documented failures in audit report adoption and user feedback patterns
- Code examples: MEDIUM - Conceptual structure shown, actual implementation will adapt to existing skill architecture

**Research date:** 2026-01-28
**Valid until:** ~60 days (Feb 2026) - Reporting patterns are stable; priority matrix approaches are well-established
