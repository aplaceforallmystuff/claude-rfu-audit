# Phase 1: Modular Architecture - Research

**Researched:** 2026-01-22
**Domain:** Claude Code skill structure and modularization patterns
**Confidence:** HIGH

## Summary

Claude Code skills support modular multi-file architectures where SKILL.md serves as the orchestrator and references external files. The current RFU Audit skill contains all gate definitions within SKILL.md (282 lines), which works but doesn't scale for planned enhancements (examples, quick mode, auto-analyze).

Phase 1 refactors the skill into separate concerns: orchestration logic (SKILL.md), gate definitions (config/gates-full.md), and placeholder structure for examples/edge cases (guides/). This preserves current functionality while enabling incremental enhancement without monolithic edits.

**Key insight:** Claude Code's progressive disclosure model means referenced files are loaded into context **only when Claude needs them**, not on every invocation. This allows detailed reference material without context bloat.

**Primary recommendation:** Extract gate definitions to config/gates-full.md, reduce SKILL.md to orchestration logic with explicit file references, create placeholder files for future content, and verify identical behavior via test invocation.

## Standard Stack

The established approach for modular Claude Code skills:

### Core

| Technology | Version | Purpose | Why Standard |
|------------|---------|---------|--------------|
| Markdown | CommonMark | Skill definition format | Native to Claude Code, human-readable, version-controllable |
| YAML frontmatter | 1.2 | Skill metadata (name, description, invocable) | Required by Claude Code skill system |
| Template variables | `{{var}}` | Output structure | Simple interpolation, already in use in audit-report.md |

### Supporting

| Tool | Purpose | When to Use |
|------|---------|-------------|
| Progressive disclosure pattern | Load files on-demand | Multi-file skills with >500 line SKILL.md |
| Relative file references | Reference supporting files | Skills with config/, guides/, templates/ subdirectories |

### Alternatives Considered

| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| Markdown files | JSON schemas | JSON adds complexity; markdown is human-readable |
| Relative paths | Absolute paths | Absolute paths break portability across installations |
| Multiple small files | Single large SKILL.md | Single file causes cognitive overload at >500 lines |

**Installation:**
No external dependencies. All changes are markdown file reorganization within existing `skill/` directory.

## Architecture Patterns

### Recommended Project Structure

```
skill/
├── SKILL.md                    # Orchestration logic (100-150 lines)
├── config/
│   └── gates-full.md          # All 11 gate definitions (~200 lines)
├── guides/
│   ├── GATE-EXAMPLES.md       # Placeholder for Phase 2
│   └── EDGE-CASES.md          # Placeholder for Phase 2
├── templates/
│   └── audit-report.md        # Existing template (unchanged)
└── reference/
    └── (empty for now)
```

### Pattern 1: SKILL.md as Orchestrator

**What:** SKILL.md contains only metadata, process flow, and references to external files. No gate definitions inline.

**When to use:** When skill has multiple concerns (process flow, evaluation criteria, examples, templates).

**Example:**

```markdown
---
name: rfu-audit
description: Run the 11-gate Really Fucking Useful gauntlet
user-invocable: true
---

# RFU Audit: Really Fucking Useful

## Usage
/rfu-audit [project-path]

## The 11 Gates

Gate definitions are maintained in `config/gates-full.md`. Each gate includes:
- Core question
- Pass criteria
- Fail indicators

See `config/gates-full.md` for complete gate specifications.

## Process

When auditing a project:

1. **Read project files**: README, package.json, main source files
2. **Walk through each gate sequentially** (see config/gates-full.md)
3. **Score each gate**: Pass (1) or Fail (0)
4. **Provide evidence** for each score
5. **Generate prioritized fix recommendations**
6. **Output structured report** using templates/audit-report.md

[Rest of orchestration logic...]
```

**Why:** Separates "what to do" (orchestration) from "how to evaluate" (gate criteria). Claude loads SKILL.md first, then reads config/gates-full.md when evaluating gates.

### Pattern 2: Config Directory for Gate Definitions

**What:** Store gate specifications in `config/` directory, separate from orchestration logic.

**When to use:** When you have reusable evaluation criteria that may be extended (e.g., future quick mode with 5-gate subset).

**Structure for config/gates-full.md:**

```markdown
# RFU Audit: Gate Definitions

This file contains the complete specifications for all 11 gates.

## Gate 1: The 5 Whys of Utility

Drill down until you hit bedrock human need.

**Process:**
```
Why does this exist?
→ Why do people need that?
→ Why can't they use existing tools?
→ Why does that matter?
→ Why is that important?
→ BEDROCK: [Fundamental human need]
```

**Pass criteria:** Reaches a fundamental need (autonomy, time, money, status, connection, safety)

**Fail indicators:**
- Chain stops at technical reasons
- Can't articulate user benefit
- "Because it's cool" or "I wanted to learn"

**Template Variables:**
- gate1_status (✅ or ❌)
- why1, why2, why3, why4, why5
- bedrock_need
- gate1_verdict

---

## Gate 2: The Inversion Test (Via Negativa)

[Continue for all 11 gates...]
```

**Why:** Gates are self-contained specifications. Future phases can add config/gates-quick.md without modifying gates-full.md.

### Pattern 3: Placeholder Structure for Future Content

**What:** Create directory structure and placeholder files even if content is minimal, to establish architecture.

**When to use:** When planning incremental enhancement across multiple phases.

**Structure for guides/GATE-EXAMPLES.md:**

```markdown
# Gate Examples: Pass/Fail Reference

This file will contain concrete examples of pass/fail cases for each gate.

**Status:** Placeholder - Phase 2 will populate with examples

## Gate 1: 5 Whys

### Example: PASS
(To be added in Phase 2)

### Example: FAIL
(To be added in Phase 2)

---

## Gate 2: Inversion Test

### Example: PASS
(To be added in Phase 2)

### Example: FAIL
(To be added in Phase 2)

[Continue structure for all 11 gates...]
```

**Structure for guides/EDGE-CASES.md:**

```markdown
# Edge Case Handling Guide

This file will document gray area resolution rules for borderline gates.

**Status:** Placeholder - Phase 2 will populate with edge case rules

## Gate 5: Wallet Test — Borderline Cases

### Scenario: Would pay, but only 2 specific people
(To be added in Phase 2)

### Scenario: Specific people who "might" pay
(To be added in Phase 2)

[Continue structure for common edge cases...]
```

**Why:** Establishes architecture before content exists. Future phases know exactly where to add content. Prevents ad-hoc file additions later.

### Pattern 4: Explicit File References in SKILL.md

**What:** Reference external files explicitly in SKILL.md with clear context about when to consult them.

**When to use:** Always, for multi-file skills.

**Reference pattern:**

```markdown
## The 11 Gates

Gate definitions are maintained in `config/gates-full.md`. Each gate includes:
- Core question
- Pass criteria
- Fail indicators

When evaluating a project, refer to `config/gates-full.md` for complete gate specifications.
```

**Why:** Makes it clear to Claude (and humans reading the skill) where to find detailed information. Progressive disclosure: Claude only loads config/gates-full.md when actually evaluating gates.

### Anti-Patterns to Avoid

**Anti-Pattern 1: Implicit file loading**
- Bad: "Gate definitions are in another file" (which file?)
- Good: "Gate definitions are maintained in `config/gates-full.md`"

**Anti-Pattern 2: Mixing concerns in SKILL.md**
- Bad: Gate definitions inline in SKILL.md, examples scattered throughout
- Good: SKILL.md orchestrates, config/ defines gates, guides/ provide examples

**Anti-Pattern 3: Empty directories without purpose**
- Bad: Creating `reference/` directory with no plan for content
- Good: Only create directories needed for current or next phase

**Anti-Pattern 4: Relative paths without directory context**
- Bad: "See gates-full.md" (where?)
- Good: "See `config/gates-full.md`"

## Don't Hand-Roll

Problems that look simple but have existing solutions:

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Markdown file inclusion | Custom `{{include}}` syntax | Explicit file references in instructions | Claude Code loads files via Read tool when referenced; no special syntax needed |
| Template variable validation | Runtime validator script | Documentation in reference/TEMPLATE-SPEC.md | Skills are stateless; validation would need to run outside Claude |
| YAML frontmatter parsing | Custom parser | Standard YAML 1.2 syntax | Claude Code's skill system handles parsing |

**Key insight:** Claude Code skills are declarative instruction sets, not executable code. "Referencing" a file means telling Claude to read it, not programmatically including its contents.

## Common Pitfalls

### Pitfall 1: Over-engineering File References

**What goes wrong:** Developers assume they need special syntax to include external files, trying patterns like `{{include config/gates-full.md}}` or `#include "gates.md"`.

**Why it happens:** Background in static site generators (Hugo, Jekyll) with explicit include directives.

**How to avoid:** Simply reference the file path in plain markdown. Claude Code provides the skill base path and uses the Read tool to access referenced files when needed.

**Warning signs:** Searching for "markdown include syntax" or trying to implement preprocessor-style includes.

**Example - What Works:**

```markdown
## Gate Evaluation

Refer to `config/gates-full.md` for complete gate specifications.

When scoring Gate 5, consult `guides/EDGE-CASES.md` for borderline case resolution.
```

Claude reads these files via the Read tool when executing the skill.

### Pitfall 2: Assuming All Files Load Immediately

**What goes wrong:** Thinking all skill files load into context on invocation, causing concern about context bloat.

**Why it happens:** Misunderstanding progressive disclosure model.

**How to avoid:** Claude Code's progressive disclosure loads SKILL.md first, then loads referenced files only when Claude determines they're needed for the current task.

**Warning signs:** Trying to minimize total line count across all files, worrying about 1000+ total lines.

**Reality:** A skill with 150-line SKILL.md + 200-line config/gates-full.md + 500-line examples file is fine. Not all 850 lines load at once.

### Pitfall 3: Premature Content Creation

**What goes wrong:** Trying to fully populate GATE-EXAMPLES.md and EDGE-CASES.md during Phase 1 refactoring.

**Why it happens:** Desire for completeness before structural validation.

**How to avoid:** Create placeholder structure with clear "Status: Placeholder - Phase X will populate" markers. Verify architecture works with minimal content first.

**Warning signs:** Phase 1 scope creep, spending hours writing examples before confirming modular structure works.

### Pitfall 4: Breaking Template Variable References

**What goes wrong:** Moving gate definitions to external file, but forgetting template (audit-report.md) references gate-specific variables like `{{gate1_status}}`, `{{why1}}`, etc.

**Why it happens:** Not recognizing that template variables must be populated regardless of where gate definitions live.

**How to avoid:** After refactoring, verify all template variables in audit-report.md are still documented in gate definitions. Template remains unchanged in Phase 1.

**Warning signs:** Template expects `{{gate1_verdict}}` but refactored gates-full.md doesn't mention what goes in that variable.

## Code Examples

Verified patterns from research:

### Current SKILL.md Structure (Lines 27-48)

```markdown
### Gate 1: The 5 Whys of Utility

Drill down until you hit bedrock human need.

**Process:**
```
Why does this exist?
→ Why do people need that?
→ Why can't they use existing tools?
→ Why does that matter?
→ Why is that important?
→ BEDROCK: [Fundamental human need]
```

**Pass criteria:** Reaches a fundamental need (autonomy, time, money, status, connection, safety)

**Fail indicators:**
- Chain stops at technical reasons
- Can't articulate user benefit
- "Because it's cool" or "I wanted to learn"
```

**What needs extraction:** Lines 27-242 (all 11 gate definitions) should move to config/gates-full.md

### Refactored SKILL.md (Orchestration Only)

```markdown
---
name: rfu-audit
description: Run the 11-gate Really Fucking Useful gauntlet to validate project utility before investing significant effort
user-invocable: true
---

# RFU Audit: Really Fucking Useful

Systematic framework to validate project utility BEFORE investing significant effort. Most open source projects fail not because of bad code, but because they solve problems nobody has.

## Philosophy

Developers build what's interesting to *them*, not what's valuable to *users*. The RFU gauntlet forces you to prove utility through 11 rigorous gates before shipping.

**Pass all 11 gates = RFU Certified**

## Usage

```
/rfu-audit [project-path]
/rfu-audit ~/Dev/my-project
```

If no path provided, audits the current working directory.

## The 11 Gates

Complete gate definitions are maintained in `config/gates-full.md`. Each gate includes:
- Core question and methodology
- Pass criteria (what constitutes success)
- Fail indicators (red flags)
- Template variables for report generation

### Gate Overview

1. **5 Whys of Utility** - Drill to bedrock human need
2. **Inversion Test** - Via negativa: what if it didn't exist?
3. **10-Minute Test** - Time to meaningful value
4. **Bartender Test** - Explain in one sentence
5. **Wallet Test** - Would you pay? Name 3 people who would
6. **Job-to-be-Done** - Situation → Motivation → Outcome
7. **Friction Audit** - Map discovery to mastery
8. **Second-Order Consequences** - What happens after success?
9. **Occam's Razor** - Simplest solution?
10. **Pareto Gate** - 20% features = 80% value?
11. **Regret Minimization** - Will you regret NOT building this?

Refer to `config/gates-full.md` for complete specifications when evaluating each gate.

## Scoring

| Score | Rating | Action |
|-------|--------|--------|
| 11/11 | RFU Certified | Ship it |
| 9-10 | Almost There | Fix specific gaps, then ship |
| 6-8 | Promising | Needs significant work |
| 0-5 | Reconsider | Build something else |

## Process

When auditing a project:

1. **Read project files**: README, package.json, main source files
2. **Walk through each gate sequentially** (detailed in config/gates-full.md)
3. **Score each gate**: Pass (1) or Fail (0)
4. **Provide evidence** for each score
5. **Generate prioritized fix recommendations**
6. **Output structured report** using templates/audit-report.md

## Integration with Wisdom Skills

This skill integrates concepts from:
- `/wisdom-clarify` - 5 Whys questioning
- `/wisdom-stoic-premeditation` - Via negativa/inversion
- `/taches-cc-resources:consider:second-order` - Second-order consequences
- `/taches-cc-resources:consider:pareto` - 80/20 analysis
- `/taches-cc-resources:consider:occams-razor` - Simplicity check

## Output

Generate a structured audit report using the template in `templates/audit-report.md`.

## Related Skills

- `/think-first` - Decision framework
- `/wisdom-ground` - Ground in philosophical frameworks
- `/prep-repo` - Prepare repo for publishing (after RFU passes)
```

**After refactoring:** SKILL.md drops from ~282 lines to ~100 lines. Gate definitions live in config/gates-full.md (~200 lines).

### config/gates-full.md Structure

```markdown
# RFU Audit: Complete Gate Definitions

This file contains the detailed specifications for all 11 gates of the RFU framework.

**Referenced by:** skill/SKILL.md
**Purpose:** Detailed evaluation criteria for each gate
**Last updated:** 2026-01-22

---

## Gate 1: The 5 Whys of Utility

Drill down until you hit bedrock human need.

**Process:**
```
Why does this exist?
→ Why do people need that?
→ Why can't they use existing tools?
→ Why does that matter?
→ Why is that important?
→ BEDROCK: [Fundamental human need]
```

**Pass criteria:** Reaches a fundamental need (autonomy, time, money, status, connection, safety)

**Fail indicators:**
- Chain stops at technical reasons
- Can't articulate user benefit
- "Because it's cool" or "I wanted to learn"

**Template Variables:**
Referenced in `templates/audit-report.md`:
- `gate1_status` - ✅ or ❌
- `why1`, `why2`, `why3`, `why4`, `why5` - Each why response
- `bedrock_need` - Which fundamental need (autonomy, time, money, status, connection, safety)
- `gate1_verdict` - Explanation of pass/fail decision

**See Also:**
- Future: `guides/GATE-EXAMPLES.md#gate-1` for concrete pass/fail examples (Phase 2)
- Future: `guides/EDGE-CASES.md#gate-1` for gray area handling (Phase 2)

---

## Gate 2: The Inversion Test (Via Negativa)

[Continue for all 11 gates using same structure...]
```

**Key additions:**
- Template variables explicitly documented per gate
- "See Also" references to future guide files
- File metadata at top

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Monolithic SKILL.md | Modular structure with config/ and guides/ | 2025-2026 | Skills can exceed 500 lines without context bloat via progressive disclosure |
| Implicit file loading | Explicit file references | 2026 | Claude knows exactly which files to load when |
| No reference pattern | Standard references/ directory | 2026 | Clear separation of code vs reference material |

**Current state (2026):**
- Skills support multi-file structure (SKILL.md + supporting files)
- Progressive disclosure: files load on-demand
- No special include syntax needed (plain markdown references)
- Standard directories: scripts/, references/, assets/, config/, guides/, templates/

**Deprecated/outdated:**
- Single-file skills with >500 lines: now considered anti-pattern
- Trying to use `{{include}}` or `#include` syntax: unnecessary, Claude reads files directly

## Open Questions

Things that couldn't be fully resolved:

1. **Does Claude load referenced files automatically, or must SKILL.md instruct it explicitly?**
   - What we know: Claude Code uses progressive disclosure, loading files on-demand
   - What's unclear: Whether "refer to config/gates-full.md" is sufficient, or if SKILL.md must say "read the file config/gates-full.md"
   - Recommendation: Use explicit phrasing in Phase 1: "Refer to `config/gates-full.md` for complete specifications when evaluating each gate." Test both approaches.

2. **How does directory structure affect skill packaging/distribution?**
   - What we know: Skills can be zipped with subdirectories
   - What's unclear: Whether config/, guides/, templates/ structure works in all skill installation contexts (marketplace vs local)
   - Recommendation: Test with local installation first (Phase 1 scope), verify structure works before adding content

3. **Do template variables need to be declared anywhere, or just used?**
   - What we know: Current template (audit-report.md) uses ~80+ variables with no declaration file
   - What's unclear: Whether documenting variables in gate definitions is necessary or just helpful
   - Recommendation: Document template variables in gate definitions for maintainability, even if not technically required

## Sources

### Primary (HIGH confidence)

- [Claude Code Skills Documentation](https://code.claude.com/docs/en/skills) - Official skill structure and multi-file support
- [How to create custom Skills](https://support.claude.com/en/articles/12512198-how-to-create-custom-skills) - File structure, REFERENCE.md pattern, progressive disclosure
- Existing codebase analysis: `/Users/jameschristian/Dev/claude-rfu-audit/skill/SKILL.md` - Current gate definitions (lines 27-242)
- Existing codebase analysis: `/Users/jameschristian/Dev/claude-rfu-audit/skill/templates/audit-report.md` - Template variables used
- Existing codebase analysis: `/Users/jameschristian/Dev/claude-rfu-audit/.planning/research/ARCHITECTURE.md` - Recommended modular structure
- Existing codebase analysis: `/Users/jameschristian/Dev/claude-rfu-audit/.planning/research/STACK.md` - Claude Code skill constraints

### Secondary (MEDIUM confidence)

- [Claude Skills Beginner's Guide](https://www.gankinterview.com/en/blog/claude-skills-getting-started-guide-concepts-structure-and-best-practices-with-s) - Progressive disclosure pattern, >500 line threshold
- [Claude Code Customization Guide](https://alexop.dev/posts/claude-code-customization-guide-claudemd-skills-subagents/) - Reference file patterns
- [How to Use Claude Code Skills](https://skywork.ai/blog/how-to-use-skills-in-claude-code-install-path-project-scoping-testing/) - Testing skill invocation
- [How to Make Skills Activate Reliably](https://scottspence.com/posts/how-to-make-claude-code-skills-activate-reliably) - Verification approaches
- [Inside Claude Code Skills](https://mikhail.io/2025/10/claude-code-skills/) - Base path and runtime behavior

### Tertiary (LOW confidence)

- [Skills refactor repository](https://github.com/recca0120/skills-refactor) - Testing/validation patterns (not verified)
- General markdown documentation - Standard markdown does NOT support file inclusion natively (verified)

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH - Verified from official docs and existing codebase
- Architecture patterns: HIGH - Based on official documentation and real skill examples
- File reference mechanism: MEDIUM - Documented but not extensively exemplified in public repos
- Placeholder content approach: HIGH - Standard software practice, no Claude Code-specific concerns

**Research limitations:**
- Could not access GitHub repositories directly (network restrictions)
- Official Anthropic skills repository examples not fully examined
- No access to internal Claude Code skill parsing implementation

**Research date:** 2026-01-22
**Valid until:** 60 days (stable domain, established patterns)

## Testing Approach

To verify skill still works after refactoring:

### Pre-Refactoring Baseline

1. **Record current behavior:**
   ```
   /rfu-audit ~/Dev/sample-project
   ```
   Capture the complete output (all gates evaluated, report generated)

2. **Document invocation process:**
   - Claude reads SKILL.md
   - Claude evaluates all 11 gates
   - Claude generates report using templates/audit-report.md
   - All template variables populated correctly

### Post-Refactoring Verification

1. **Test skill invocation:**
   ```
   /rfu-audit ~/Dev/sample-project
   ```
   Should behave identically to pre-refactoring

2. **Verify gate evaluation:**
   - Check that Claude references config/gates-full.md during evaluation
   - Confirm all 11 gates are still evaluated
   - Verify scoring logic unchanged (Pass/Fail per gate)

3. **Verify report generation:**
   - Check that templates/audit-report.md still works
   - Confirm all template variables populated (gate1_status, why1-why5, etc.)
   - Verify output format identical to baseline

4. **Verify file loading:**
   - Observe Claude's behavior: does it read config/gates-full.md when evaluating gates?
   - If using verbose mode, check for file read operations
   - Confirm no errors about missing files or undefined references

### Validation Checklist

- [ ] Skill invocable via `/rfu-audit [path]`
- [ ] YAML frontmatter parses correctly (name, description, user-invocable)
- [ ] Directory structure exists: config/, guides/, templates/, reference/
- [ ] config/gates-full.md contains all 11 gate definitions
- [ ] SKILL.md references config/gates-full.md explicitly
- [ ] Placeholder files exist: guides/GATE-EXAMPLES.md, guides/EDGE-CASES.md
- [ ] templates/audit-report.md unchanged
- [ ] All 11 gates evaluated in test invocation
- [ ] Report generated with all expected sections
- [ ] Template variables populated correctly
- [ ] Behavior identical to pre-refactoring baseline

### Troubleshooting

**If skill doesn't load:**
- Check YAML frontmatter syntax (validate with Python yaml parser)
- Verify SKILL.md file name and location (skill/SKILL.md)
- Check for markdown syntax errors in SKILL.md

**If gates not evaluated:**
- Check that SKILL.md references config/gates-full.md
- Verify file path is correct (relative to skill directory)
- Try explicit instruction: "Read config/gates-full.md for gate specifications"

**If template fails:**
- Verify templates/audit-report.md still exists and unchanged
- Check that all template variables documented in config/gates-full.md
- Confirm variable naming matches exactly ({{gate1_status}} not {{gate_1_status}})

### Success Criteria for Phase 1

Refactoring succeeds when:

1. **Behavior preserved:** Test invocation produces identical output to baseline
2. **Structure established:** Directory structure exists per ARCHITECTURE.md recommendations
3. **Gate definitions extracted:** config/gates-full.md contains all gates, SKILL.md orchestrates
4. **Placeholders created:** guides/GATE-EXAMPLES.md and guides/EDGE-CASES.md exist with placeholder structure
5. **Documentation updated:** config/gates-full.md documents template variables per gate
6. **No regressions:** All original functionality works without modification

**Phase 1 explicitly does NOT include:**
- Populating examples in GATE-EXAMPLES.md (Phase 2)
- Writing edge case rules in EDGE-CASES.md (Phase 2)
- Creating quick mode (Phase 3)
- Adding auto-analyze (Phase 4)

---

*Phase 1 research completed: 2026-01-22*
