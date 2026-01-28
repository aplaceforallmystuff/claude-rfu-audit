# Phase 7: Audit History - Research

**Researched:** 2026-01-28
**Domain:** File-based audit history tracking, score comparison, delta visualization
**Confidence:** HIGH

## Summary

Phase 7 adds file-based history tracking to the RFU audit skill, allowing users to save audit reports, re-audit projects, and see score deltas showing which gates improved or regressed. This research examined patterns for stateless history management, structured data comparison, and inline delta display.

The standard approach combines timestamped files with YAML frontmatter metadata for identification, git-style comparison logic for detecting changes, and inline visual indicators (arrows, symbols) for displaying deltas. Key insight: the skill remains stateless—all history lives in files, each audit reads previous reports to compute comparisons.

**Context decisions:** User decided on timestamp-based naming (`rfu-audit-20260128-143022.md`), inline delta display with arrows, automatic detection without flags, and mode-separated histories (quick vs full audits tracked separately). Research validates these patterns as standard practice.

**Primary recommendation:** Use `.planning/audits/` for storage (consistent with existing `.planning/` convention), YAML frontmatter for metadata extraction, arrow indicators (↑ improved, ↓ regressed, = unchanged) for inline deltas, and project name matching with path fallback for identification.

## Standard Stack

### Core

| Pattern | Purpose | Why Standard |
|---------|---------|--------------|
| Timestamped filenames | Chronological file organization | ISO 8601 format (YYYYMMDD-HHMMSS) allows natural filesystem sorting and avoids timezone ambiguity |
| YAML frontmatter | Structured metadata in markdown | Industry standard from Jekyll, widely supported, human-readable, separates metadata from content |
| File-based storage | Stateless history persistence | No database dependencies, git-friendly, human-readable, inspection-friendly |
| Inline delta indicators | Change visualization | Preserves context (see change where it matters), not buried in separate comparison section |

### Supporting

| Pattern | Purpose | When to Use |
|---------|---------|-------------|
| Project name matching | History association | Primary identification method (handles project moves/renames better than path) |
| Path fallback matching | Disambiguation | When multiple projects share same name |
| Mode separation | Apples-to-apples comparison | Prevents confusing 5-gate quick audit with 11-gate full audit |
| Arrow symbols | Visual delta encoding | ↑ improved, ↓ regressed, = unchanged, NEW/REMOVED for N/A transitions |

### Alternatives Considered

| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| Timestamped files | Incremental naming (v1, v2, v3) | Version numbers require tracking state; timestamps are stateless |
| YAML frontmatter | JSON metadata | YAML more human-readable, supports comments, standard for markdown |
| Inline deltas | Separate comparison section | Inline keeps context; separate section requires mental cross-referencing |
| File-based storage | SQLite database | Database adds dependency, reduces portability; files are git-friendly |

**Installation:**

No external dependencies required. Implementation uses existing template system and file I/O patterns.

## Architecture Patterns

### Recommended File Structure

```
.planning/
└── audits/
    ├── {project-name}/
    │   ├── full/
    │   │   ├── rfu-audit-20260128-143022.md
    │   │   ├── rfu-audit-20260130-091544.md
    │   │   └── rfu-audit-20260202-154813.md
    │   └── quick/
    │       ├── rfu-audit-20260127-103312.md
    │       └── rfu-audit-20260128-142847.md
    └── {another-project}/
        └── full/
            └── rfu-audit-20260125-160045.md
```

**Why this structure:**
- `.planning/audits/` - Consistent with existing `.planning/` convention for project management artifacts
- `{project-name}/` - Groups all audits for same project (enables `--history` listing)
- `full/` and `quick/` - Mode separation prevents comparing incompatible audit types
- Timestamped files - Natural chronological ordering, collision-resistant

### Pattern 1: YAML Frontmatter for Metadata

**What:** Structured metadata block at top of markdown report for machine-readable project identification

**When to use:** Every saved audit report (automatic)

**Format:**
```yaml
---
rfu_audit:
  version: "1.0"
  project_name: "my-cli-tool"
  project_path: "/Users/jim/Dev/my-cli-tool"
  audit_date: "2026-01-28"
  audit_mode: "full"
  score: 8
  max_score: 11
  gate_results:
    gate1: "PASS"
    gate2: "FAIL"
    gate3: "PASS"
    # ... (all gates)
---

# RFU Audit Report
[Rest of report content...]
```

**Why YAML:**
- Human-readable for manual inspection
- Standard markdown frontmatter format (Jekyll convention)
- Supports structured data (nested objects, arrays)
- Comments allowed (unlike JSON)
- Source: [GitHub YAML frontmatter documentation](https://docs.github.com/en/contributing/writing-for-github-docs/using-yaml-frontmatter)

**Metadata extraction strategy:**
1. Check for frontmatter first (fast, accurate)
2. Fall back to parsing markdown headers if frontmatter missing (backward compatibility)
3. Fail gracefully if neither found (log warning, skip comparison)

### Pattern 2: Project Identification and Matching

**What:** Logic to associate new audit with historical audits of same project

**When to use:** Every audit when checking for history

**Matching algorithm:**
```
1. Extract project_name from current audit
2. Look for .planning/audits/{project_name}/{mode}/ directory
3. If found → read most recent file in that directory
4. If not found → check all project paths for exact match (handles renames)
5. If still not found → first audit (no comparison)
```

**Why name-first matching:**
- Project paths change (move between directories, refactor monorepos)
- Project names stable across refactors
- Path fallback handles genuine name collisions
- Source: Research on [project identification strategies](https://blog.tabsstudio.com/2020/05/24/full-path-disambiguation/)

**Edge case handling:**
- Same project name in different locations → Path fallback disambiguates
- Project renamed → New history starts (intentional reset)
- Project moved → Name matching maintains continuity

### Pattern 3: Inline Delta Display

**What:** Show score changes directly in report where relevant, not in separate comparison section

**When to use:** When previous audit exists for same project/mode

**Visual indicators:**
```
Gate 1: PASS ↑  (was FAIL)
Gate 3: FAIL ↓  (was PASS)
Gate 5: PASS =  (unchanged)
Gate 7: PASS ⚡ (newly applicable, was N/A)
Gate 9: N/A ⊗  (removed, was PASS)
```

**Why inline:**
- Preserves context (see change where it matters)
- No mental cross-referencing required
- Doesn't clutter report if no history exists
- Source: Accessibility audit patterns showing [before/after improvements inline](https://www.levelaccess.com/blog/so-you-want-an-accessibility-score/)

**Implementation locations:**
1. **Executive Summary:** `Score: 8/11 ↑ (was 7/11)` — Overall progress
2. **Gate Results:** Individual gate status lines — Specific changes
3. **Priority Matrix:** Note if fix addresses regressed gate — Context for urgency

### Pattern 4: History View (`--history` Flag)

**What:** List all saved audits for current project in table format

**When to use:** When user wants to review audit timeline

**Display format:**
```
# Audit History: my-cli-tool

## Full Audits
| Date       | Time     | Score | File                              |
|------------|----------|-------|-----------------------------------|
| 2026-02-02 | 15:48:13 | 9/11  | rfu-audit-20260202-154813.md      |
| 2026-01-30 | 09:15:44 | 8/11  | rfu-audit-20260130-091544.md      |
| 2026-01-28 | 14:30:22 | 7/11  | rfu-audit-20260128-143022.md      |

## Quick Audits
| Date       | Time     | Score | File                              |
|------------|----------|-------|-----------------------------------|
| 2026-01-28 | 14:28:47 | 4/5   | rfu-audit-20260128-142847.md      |
| 2026-01-27 | 10:33:12 | 3/5   | rfu-audit-20260127-103312.md      |

Total: 5 audits (3 full, 2 quick)
```

**Why table format:**
- Easy to scan chronologically (most recent first)
- Shows score progression at a glance
- File paths allow direct inspection (`cat .planning/audits/...`)

### Anti-Patterns to Avoid

- **Storing raw scores only:** Loses context about which gates changed (store full gate results in frontmatter)
- **Complex similarity matching:** Don't try to fuzzy-match project names (exact match or path fallback only)
- **Dominating the report:** Deltas should augment, not overshadow current audit results
- **Comparing across modes:** Never compare quick (5-gate) to full (11-gate) audits

## Don't Hand-Roll

Problems that look simple but have existing solutions:

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Parsing YAML frontmatter | Custom regex parser | Standard YAML parser (built into skill execution) | YAML edge cases (escaping, multiline, etc.) are subtle |
| Timestamp formatting | Manual string manipulation | ISO 8601 standard (YYYYMMDD-HHMMSS) | Avoids timezone bugs, naturally sortable |
| File path normalization | String replacement for ~ and .. | Standard path resolution (expand ~, resolve ..) | Handles edge cases (symlinks, whitespace) |
| Directory creation | Check if exists, then create | Recursive mkdir with exist_ok | Race conditions, permission errors |

**Key insight:** History tracking is 80% file I/O patterns, 20% comparison logic. Don't build custom storage or serialization systems.

## Common Pitfalls

### Pitfall 1: Comparing Incompatible Audit Types

**What goes wrong:** Comparing quick audit (5 gates) with full audit (11 gates) produces meaningless deltas

**Why it happens:** Modes share some gates (1, 3, 4, 5, 9) but full mode has 6 additional gates

**How to avoid:** Separate history by mode (`.planning/audits/{project}/full/` vs `.../quick/`)

**Warning signs:**
- Gate deltas show "NEW" for most gates (likely comparing quick → full)
- Scores on different scales (X/5 vs Y/11)

**Example fix:**
```
# Wrong: comparing across modes
Previous: 4/5 (quick mode)
Current: 7/11 (full mode)
→ Meaningless comparison

# Right: mode separation
Previous: 3/5 (quick mode)
Current: 4/5 (quick mode)
→ Valid comparison (1 gate improved)
```

### Pitfall 2: Treating N/A as Fail or Pass

**What goes wrong:** Showing "Gate 7: PASS ↑ (was N/A)" implies improvement, but gate became *applicable*, not necessarily better

**Why it happens:** Binary thinking (pass/fail) doesn't account for applicability changes

**How to avoid:** Special indicators for N/A transitions (⚡ newly applicable, ⊗ no longer applicable)

**Warning signs:**
- Project type changed (library → CLI tool makes Gate 7 applicable)
- Major refactor changes gate applicability
- User questions why "improvement" arrow shown for new gate

**Example fix:**
```
# Wrong: treating N/A → PASS as improvement
Gate 7: PASS ↑ (was N/A)

# Right: indicating applicability change
Gate 7: PASS ⚡ (newly applicable, was N/A)
Gate 9: N/A ⊗ (no longer applicable, was FAIL)
```

**Source:** Existing EDGE-CASES.md defines N/A criteria per gate

### Pitfall 3: Filename Collisions from Rapid Audits

**What goes wrong:** Running two audits in same second creates filename collision

**Why it happens:** Timestamp format (YYYYMMDD-HHMMSS) has 1-second granularity

**How to avoid:** Append milliseconds for uniqueness: `rfu-audit-20260128-143022-847.md`

**Warning signs:**
- File overwrite warnings in logs
- Missing audit in history view
- "Previous audit" shows current audit (overwrote itself)

**Example implementation:**
```bash
# Wrong: 1-second granularity
filename="rfu-audit-$(date +%Y%m%d-%H%M%S).md"

# Right: millisecond precision
filename="rfu-audit-$(date +%Y%m%d-%H%M%S)-$(date +%3N).md"
```

### Pitfall 4: Project Name Extraction Failure

**What goes wrong:** Can't find history because project name extraction differs between audits

**Why it happens:** Inconsistent extraction logic (sometimes from path, sometimes from README, sometimes from package.json)

**How to avoid:** Define precedence order and normalize (lowercase, trim whitespace, replace special chars)

**Warning signs:**
- History exists but not detected
- Multiple history directories for same project
- `--history` shows empty but files exist

**Example fix:**
```
# Wrong: inconsistent extraction
Audit 1: project_name = "My CLI Tool" (from README title)
Audit 2: project_name = "my-cli-tool" (from package.json name)
→ Creates separate histories

# Right: normalized extraction
1. Try package.json "name" field (canonical)
2. Fall back to directory name
3. Normalize: lowercase, trim, replace spaces with hyphens
→ Consistent "my-cli-tool" across audits
```

## Code Examples

Verified patterns from standard practices:

### Example 1: Checking for Previous Audit

```bash
# Source: Standard file-based history pattern

PROJECT_NAME="my-cli-tool"
AUDIT_MODE="full"  # or "quick"
HISTORY_DIR=".planning/audits/${PROJECT_NAME}/${AUDIT_MODE}"

if [ -d "$HISTORY_DIR" ]; then
  # Get most recent audit (files sorted by timestamp)
  PREVIOUS_AUDIT=$(ls -1 "$HISTORY_DIR"/rfu-audit-*.md | tail -1)

  if [ -n "$PREVIOUS_AUDIT" ]; then
    echo "Found previous audit: $PREVIOUS_AUDIT"
    # Extract metadata for comparison
  fi
else
  echo "First audit for this project (no history)"
fi
```

### Example 2: Saving Audit with Frontmatter

```markdown
---
rfu_audit:
  version: "1.0"
  project_name: "my-cli-tool"
  project_path: "/Users/jim/Dev/my-cli-tool"
  audit_date: "2026-01-28"
  audit_mode: "full"
  score: 8
  max_score: 11
  gate_results:
    gate1: "PASS"
    gate2: "FAIL"
    gate3: "PASS"
    gate4: "PASS"
    gate5: "PASS"
    gate6: "FAIL"
    gate7: "PASS"
    gate8: "PASS"
    gate9: "FAIL"
    gate10: "PASS"
    gate11: "PASS"
---

# RFU Audit Report

**Project:** my-cli-tool
**Path:** /Users/jim/Dev/my-cli-tool
**Date:** 2026-01-28

---

## Executive Summary

**Score: 8/11 ↑** (was 7/11) — Almost There

Improved since last audit (2026-01-25). Fixed Gate 4 (Bartender Test).
Still need to address Gates 2, 6, and 9 before shipping.
```

### Example 3: Computing Gate Delta

```python
# Pseudo-code for delta computation logic

def compute_gate_delta(previous_gate_status, current_gate_status):
    """
    Returns tuple: (indicator, description)
    indicator: ↑ ↓ = ⚡ ⊗
    """

    # Handle N/A transitions specially
    if previous_gate_status == "N/A" and current_gate_status != "N/A":
        return ("⚡", "newly applicable, was N/A")

    if previous_gate_status != "N/A" and current_gate_status == "N/A":
        return ("⊗", "no longer applicable, was " + previous_gate_status)

    # Handle pass/fail changes
    if previous_gate_status == "FAIL" and current_gate_status == "PASS":
        return ("↑", "was FAIL")

    if previous_gate_status == "PASS" and current_gate_status == "FAIL":
        return ("↓", "was PASS")

    # Unchanged
    if previous_gate_status == current_gate_status:
        return ("=", "unchanged")

    # Unexpected state
    return ("?", f"was {previous_gate_status}")
```

### Example 4: History View Query

```bash
# Source: Standard chronological listing pattern

PROJECT_NAME="my-cli-tool"
AUDITS_DIR=".planning/audits/${PROJECT_NAME}"

echo "# Audit History: ${PROJECT_NAME}"
echo ""

# Full audits
if [ -d "${AUDITS_DIR}/full" ]; then
  echo "## Full Audits"
  echo "| Date       | Time     | Score | File                              |"
  echo "|------------|----------|-------|-----------------------------------|"

  for audit in $(ls -1r "${AUDITS_DIR}/full"/rfu-audit-*.md); do
    # Extract timestamp from filename: rfu-audit-20260128-143022.md
    timestamp=$(basename "$audit" | sed 's/rfu-audit-//' | sed 's/.md//')
    date_part="${timestamp:0:8}"      # 20260128
    time_part="${timestamp:9:6}"      # 143022

    # Format date: 2026-01-28
    formatted_date="${date_part:0:4}-${date_part:4:2}-${date_part:6:2}"
    # Format time: 14:30:22
    formatted_time="${time_part:0:2}:${time_part:2:2}:${time_part:4:2}"

    # Extract score from frontmatter
    score=$(grep "score:" "$audit" | head -1 | awk '{print $2}')
    max_score=$(grep "max_score:" "$audit" | head -1 | awk '{print $2}')

    echo "| $formatted_date | $formatted_time | $score/$max_score | $(basename $audit) |"
  done
  echo ""
fi

# Quick audits (similar logic)
```

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Separate comparison reports | Inline delta indicators | 2023-2024 | Accessibility audits now show improvements contextually, not in appendix |
| Manual versioning (v1, v2) | Timestamped files | ISO 8601 adoption | Natural sorting, collision resistance |
| Custom serialization | YAML frontmatter | Jekyll influence | Standard format, tool interop |
| Database storage | File-based history | Git workflow shift | Human-readable, version-controllable |

**Deprecated/outdated:**
- Version numbers for audit files: Timestamps are more informative and collision-resistant
- Separate comparison sections: Inline deltas keep context together

## Open Questions

Things that couldn't be fully resolved:

1. **Storage location: In-project vs central**
   - What we know: `.planning/audits/` is consistent with existing convention
   - What's unclear: Should this be configurable for teams with central audit tracking?
   - Recommendation: Start with `.planning/audits/` (in-project), add config later if needed

2. **Comparison granularity: Full diff vs score-only**
   - What we know: Score delta is essential, gate-level deltas highly valuable
   - What's unclear: Should we diff gate verdict text to show "why" changes?
   - Recommendation: Start with status-only (PASS/FAIL), full diff is complexity creep

3. **Collision handling: Milliseconds vs UUID suffix**
   - What we know: Milliseconds sufficient for manual audits (not automated/CI)
   - What's unclear: Future CI integration may run audits in parallel
   - Recommendation: Milliseconds for Phase 7, UUID if CI integration added later

## Sources

### Primary (HIGH confidence)

- [GitHub YAML frontmatter documentation](https://docs.github.com/en/contributing/writing-for-github-docs/using-yaml-frontmatter) - Standard format for markdown metadata
- [ISO 8601 filename conventions](https://datamanagement.hms.harvard.edu/plan-design/file-naming-conventions) - Timestamp naming best practices
- [File naming and versioning](https://researchdata.wisc.edu/file-naming-and-versioning/) - Version control patterns for data files
- Existing codebase: `skill/guides/EDGE-CASES.md` - N/A handling rules

### Secondary (MEDIUM confidence)

- [Visual Studio path disambiguation](https://blog.tabsstudio.com/2020/05/24/full-path-disambiguation/) - Project identification strategies
- [Accessibility audit score tracking](https://www.levelaccess.com/blog/so-you-want-an-accessibility-score/) - Before/after comparison patterns
- [WCAG 3.0 scoring changes](https://rubyroidlabs.com/blog/2025/10/how-to-prepare-for-wcag-3-0/) - Evolution of audit scoring systems (2026 trends)
- [Git diff algorithms](https://www.cloudbees.com/blog/git-diff-a-complete-comparison-tutorial-for-git) - Comparison logic patterns

### Tertiary (LOW confidence)

- [Delta symbol usage](https://delta-symbol.com/) - Visual indicators for change
- [Emoji design convergence 2018-2026](https://blog.emojipedia.org/emoji-design-convergence-review-2018-2026/) - Platform consistency for visual indicators
- [YAML vs JSON comparison](https://aws.amazon.com/compare/the-difference-between-yaml-and-json/) - Structured data format tradeoffs

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH - YAML frontmatter, timestamped files, inline deltas are industry standard
- Architecture: HIGH - File-based storage, mode separation, project matching validated by similar tools
- Pitfalls: HIGH - N/A handling from existing codebase, collision risks from timestamp math

**Research date:** 2026-01-28
**Valid until:** 60 days (stable domain, patterns unlikely to change)

**Context decisions validated:**
- ✓ Timestamp-based naming: Standard practice (ISO 8601)
- ✓ Inline delta display: Accessibility audit pattern
- ✓ Automatic detection: No flag needed (check on every audit)
- ✓ Mode separation: Prevents invalid comparisons
- ✓ File-based storage: Git-friendly, stateless

**Key decisions for planner:**
- Storage location: `.planning/audits/{project-name}/{mode}/` (consistent with existing .planning convention)
- Project identification: Name-first with path fallback
- Visual indicators: ↑↓= for changes, ⚡⊗ for N/A transitions
- Frontmatter format: Structured YAML with nested rfu_audit object
