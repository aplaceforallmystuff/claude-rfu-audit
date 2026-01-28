# Auto-Analyze Guide

Auto-analyze mode reduces manual data entry friction by extracting project information from README.md and package.json, then presenting suggestions for human verification before scoring. This is **friction reduction**, not automation of judgment — the human auditor remains the decision-maker.

## Overview

**What Auto-Analyze Does:**

Auto-analyze generates hypotheses about project characteristics by reading and analyzing project files (README.md and package.json). It extracts structured data fields needed for gate scoring and presents them as suggestions for human verification.

**Core Principle:** Claude suggests, human verifies.

**When to Use:**
- First-time audit of unfamiliar project
- Quick initial data gathering before detailed gate evaluation
- Projects with standard README structure

**When NOT to Use:**
- Projects without README.md (auto-analyze will fail gracefully)
- Non-standard project structures (extraction confidence will be LOW)
- When you already know the project well (manual entry may be faster)

**Important:** Auto-analyze extracts data for hypothesis generation only. You must verify all suggestions before they are used in gate scoring. Never rubber-stamp auto-extracted data.

---

## Fields Extracted

Auto-analyze extracts 7 fields from project files, each mapping to specific gates:

| Field | Gate(s) | Source Priority | Confidence Heuristic |
|-------|---------|-----------------|----------------------|
| `project_name` | Report header | package.json `name` > README H1 | HIGH if explicit in either source |
| `one_line_description` | Gate 4 (Bartender) | README first paragraph > package.json `description` | HIGH if single sentence in clear location, MEDIUM if extracted from longer text |
| `value_proposition` | Gates 1, 2 | "Why/Problem/Motivation" sections > first paragraph after H1 | MEDIUM (usually inferred from context) |
| `target_users` | Gates 1, 5 | "Who is this for/Audience" sections > usage examples | MEDIUM to LOW (often implicit) |
| `key_features` | Gate 10 (Pareto) | "Features" section > bullet lists in top 30% of README | HIGH if labeled section exists |
| `installation_time_estimate` | Gate 3 (10-Minute) | Prerequisites count + steps count in Installation section | MEDIUM (always estimate, depends on user's setup) |
| `prerequisites` | Gate 3 | "Prerequisites/Requirements" section > Installation preamble | HIGH if section exists |

**Required vs Optional:**
- Only `project_name` is truly required (all other fields can gracefully degrade to manual entry)
- Missing fields prompt manual entry during verification, not extraction failure

---

## Extraction Prompt Template

When user invokes `--auto-analyze` flag, use this structured prompt to extract project data:

```markdown
Analyze the following project files and extract structured information for the RFU Audit gates.

**README.md:**
{readme_content}

**package.json (if exists):**
{package_json_content}

Extract the following fields. Use "UNCERTAIN" if information is not present or unclear.

Return JSON:
{
  "project_name": "string",
  "one_line_description": "string (max 1 sentence, for Gate 4)",
  "value_proposition": "string (what problem this solves, for Gates 1-2)",
  "target_users": "string (who this is for)",
  "key_features": ["array", "max 5 items", "for Gate 10"],
  "installation_steps_count": number,
  "prerequisites": ["array", "of required setup"],
  "installation_time_estimate_minutes": number,
  "similar_tools_mentioned": ["competitors or alternatives"],
  "confidence": {
    "project_name": "HIGH|MEDIUM|LOW",
    "one_line_description": "HIGH|MEDIUM|LOW",
    "value_proposition": "HIGH|MEDIUM|LOW",
    "target_users": "HIGH|MEDIUM|LOW",
    "key_features": "HIGH|MEDIUM|LOW",
    "installation_time_estimate": "HIGH|MEDIUM|LOW"
  },
  "sources": {
    "project_name": "package.json name field" or "README H1 heading",
    "one_line_description": "README line 5" or "package.json description",
    "value_proposition": "README 'The Problem' section",
    "target_users": "README 'Who is this for' section",
    "key_features": "README 'Features' section",
    "prerequisites": "README 'Prerequisites' section",
    "installation_time_estimate": "Estimated from prerequisites and steps"
  }
}

**Extraction rules:**
- project_name: package.json "name" field if exists, else README H1 text
- one_line_description: First paragraph after title or package.json "description"
- value_proposition: "The Problem", "Why", "Motivation" sections
- key_features: "Features" section, bullet lists with capabilities
- installation_time_estimate: Based on prerequisites complexity and step count
  * 0 prereqs, <5 steps = 2-5 minutes
  * 1-2 prereqs, 5-10 steps = 5-10 minutes
  * 3+ prereqs, >10 steps = 10-15 minutes
- Confidence levels:
  * HIGH = explicitly stated in files with clear section
  * MEDIUM = inferred from context or multiple sources differ
  * LOW = guessed based on project type or minimal info
  * UNCERTAIN = not present, too ambiguous to extract

**Grounding constraint:**
Extract ONLY information explicitly stated in the provided files. If a field is not present or unclear, return "UNCERTAIN" as the value. DO NOT infer features from project type. DO NOT hallucinate information that is not in the files.
```

**Usage:**
1. Read README.md and package.json from project directory
2. Insert content into placeholders
3. Send extraction prompt to Claude
4. Parse JSON response
5. Present suggestions to user for verification

---

## Confidence Levels

Each extracted field includes a confidence level indicating reliability:

### HIGH Confidence ✓

**Criteria:**
- Value found in clear, labeled section (e.g., "## Features" for features)
- Single unambiguous source (no conflicts between README and package.json)
- Exact match (not paraphrased or inferred)

**Examples:**
- package.json fields (`name`, `description`)
- Section headings (H1, H2, H3)
- Explicit statements ("This project is for data analysts")

**Presentation:**
- Display with green checkmark ✓
- Default to "approved" state (user can override)
- Minimal explanation needed

### MEDIUM Confidence ⚠

**Criteria:**
- Value inferred from context (e.g., value prop inferred from problem statement)
- Multiple sources with similar but not identical values
- Paraphrased or summarized from longer text

**Examples:**
- Installation time estimate (calculated from steps)
- Target users inferred from use cases
- Value proposition synthesized from multiple sections

**Presentation:**
- Display with yellow warning ⚠
- Default to "needs review" state
- Show reasoning: "Inferred from [section name]"

### LOW Confidence ❓

**Criteria:**
- Multiple conflicting sources
- Very limited information (e.g., 2-line README)
- Generic fallback based on project type

**Examples:**
- Guessing target users from project category
- Estimating installation time with no installation section
- Value proposition when no problem statement exists

**Presentation:**
- Display with red question mark ❓
- Default to "rejected" state
- Show why uncertain: "Multiple possible values found" or "No explicit statement"
- Suggest manual entry

### UNCERTAIN

**Criteria:**
- Information not present in files
- Too ambiguous to extract reliably
- User input required for accuracy

**Examples:**
- Missing "Features" section
- No problem statement or value prop
- Empty or minimal README

**Presentation:**
- Display as empty with prompt
- No suggestion provided
- Message: "Could not extract from files. Please enter manually."

---

## README Section Identification Heuristics

Auto-analyze uses common README structure patterns to locate specific information types:

### One-Line Description

**Where to look:**
- First sentence after H1 heading
- Text between title and first H2
- package.json `description` field
- Text in bold immediately after title

**Pattern matching:**
- Look for single sentence within first 3 paragraphs
- Avoid generic phrases like "A tool for..."
- Prefer sentences with specific verbs and outcomes

### Value Proposition

**Where to look:**
- Section titled "Why", "Problem", "The Problem", "Motivation"
- First paragraph of "About" or "Overview" section
- Text following "**Stop...**" or "**Never...**" (imperative pattern)

**Pattern matching:**
- Look for problem → solution framing
- Pain point descriptions ("tired of", "frustrated by", "wasting time")
- Benefit statements ("so you can", "enables you to")

### Key Features

**Where to look:**
- Section titled "Features", "Capabilities", "What it does"
- Bullet lists in top 30% of README
- Numbered lists with action verbs (Create, Generate, Validate)

**Pattern matching:**
- Action-oriented bullets (starts with verb)
- Distinct capabilities (not implementation details)
- Max 5 features (top Pareto items)

### Installation Time Estimate

**Where to look:**
- Count steps in "Installation" or "Setup" section
- Check for "Prerequisites" or "Requirements" section
- Complexity indicators (Docker, API keys, databases)

**Heuristic calculation:**
- 0 prereqs, <5 steps → 2-5 minutes
- 1-2 prereqs, 5-10 steps → 5-10 minutes
- 3+ prereqs, >10 steps → 10-15 minutes
- Docker setup: +10 minutes
- API key signup: +5 minutes
- Database config: +10 minutes

**Always use conservative (higher) estimates to avoid under-promising.**

### Target Users

**Where to look:**
- Section titled "Who is this for", "Audience", "Use Cases"
- Phrases like "for developers who", "if you need to", "helps teams"
- Examples showing specific user workflows

**Pattern matching:**
- Role-based descriptions ("data analysts", "frontend developers")
- Problem-based descriptions ("if you struggle with X")
- Workflow-based descriptions ("teams deploying microservices")

---

## Graceful Degradation

Auto-analyze extraction can fail partially or completely. The system must degrade gracefully to ensure extraction failure never blocks the audit.

### Principles

1. **Don't block the audit** - Proceed with manual entry for failed fields
2. **Explain what happened** - Tell user why extraction failed and what to do
3. **Provide context** - Show which gates need this field and why it matters
4. **Offer options** - Enter now, defer to gate scoring, or mark N/A

### Per-Field Degradation

**When extraction returns UNCERTAIN:**

```
⚠ Could not extract "target_users"

Reason: No "Who is this for" or "Audience" section found in README.

This field is used for Gates 1 and 5. You can:
- Enter manually now
- Leave blank and fill during gate scoring
- Skip if not applicable

[1] Enter manually
[2] Fill during gate scoring
[3] Mark as N/A
```

**Handling partial extraction:**
- If 4+ fields extracted successfully, present suggestions for review
- If 1-3 fields extracted, prompt: "Only partial data extracted. Continue with manual entry or review suggestions?"
- If 0 fields extracted (only possible if README is empty), skip auto-analyze entirely

**Handling missing files:**
- README.md missing → Cannot proceed (validation catches this before auto-analyze)
- package.json missing → Continue with README-only extraction (note in confidence)

**Error message format:**

All error messages follow this structure:
```
⚠ Could not extract "[field_name]"

Reason: [specific technical reason]

Why this matters: [which gates use this field and for what]

What to do: [actionable options]
```

---

## Human Verification Workflow

After extraction, all suggestions must be reviewed and approved before use in gate scoring.

### Verification Interaction Format

```markdown
## Auto-Analyze Results

Claude extracted the following from your project files.
Review each field and approve, edit, or reject.

### {field_name}
**Extracted:** {value}
**Source:** {source_attribution}
**Confidence:** {level} {icon}

[1] Approve as-is
[2] Edit value
[3] Reject and enter manually
[4] Show source in files

Your choice: ___
```

### Confidence-Based Defaults

**HIGH confidence (✓):**
- Default state: Approved (pre-selected)
- User action: Quick Enter to accept all HIGH fields at once
- Message: "This extraction has high confidence. Press Enter to approve."

**MEDIUM confidence (⚠):**
- Default state: Needs review (not pre-selected)
- User action: Must explicitly approve or edit
- Message: "This extraction is inferred. Please verify before approving."

**LOW confidence (❓):**
- Default state: Rejected (marked for manual entry)
- User action: Edit or enter manually
- Message: "This extraction has low confidence. Consider manual entry."

**UNCERTAIN:**
- Default state: Empty (no suggestion)
- User action: Must enter manually
- Message: "Could not extract from files. Please enter manually."

### Batch Actions

For efficiency, allow batch operations:

- **'a'** - Approve all HIGH confidence fields
- **'r'** - Review all MEDIUM/LOW fields (skip HIGH)
- **'q'** - Quit auto-analyze, use full manual entry

**Workflow:**
1. Display all HIGH confidence fields
2. Prompt: "Press 'a' to approve all HIGH confidence, 'r' to review, or Enter to review individually"
3. If 'a': Approve all HIGH, move to MEDIUM/LOW review
4. If 'r': Skip to first MEDIUM/LOW field
5. If Enter: Review each field individually

---

## Conflict Handling

When README and package.json disagree on a field value, the conflict must be surfaced to the user.

### Detecting Conflicts

Compare values from both sources for:
- `project_name` (package.json `name` vs README H1)
- `one_line_description` (package.json `description` vs README first paragraph)

**Threshold for conflict:**
- Exact match → No conflict (HIGH confidence)
- Minor wording difference (same meaning) → No conflict, note sources in attribution
- Different meaning → Conflict detected (flag as MEDIUM confidence)

### Presenting Conflicts

```markdown
### One-Line Description
**README (line 4):** "Stop building things nobody wants"
**package.json:** "A Claude Code skill for project validation"
**Conflict detected:** These differ significantly
**Confidence:** MEDIUM ⚠
**Suggested:** Use README version (more user-facing)

**Actions:**
[1] Approve README version
[2] Use package.json version
[3] Edit to write custom
[4] Show both sources in files
```

### Conflict Resolution Rules

**Default suggestion priority:**
1. README first paragraph (user-facing, intentional)
2. package.json `description` (technical, may be stale)

**Rationale:** README is more likely to be up-to-date and user-focused. package.json may contain technical shorthand.

**User override:** Always allow user to choose either source or write custom.

---

## Pitfalls to Avoid

Common mistakes when using or implementing auto-analyze:

### 1. Automation Bias

**Problem:** User rubber-stamps auto-extracted data without verification, incorrect info flows into gate scoring, produces invalid audit results.

**Warning signs:**
- User approves all suggestions without reading them
- No edits made to MEDIUM/LOW confidence extractions
- Audit results contain information not in project files

**Prevention:**
- Present extractions as **suggestions**, not facts ("Claude suggests" not "Claude determined")
- Show confidence levels with visual indicators (✓ ⚠ ❓)
- Display source attribution ("Extracted from README line 15")
- Require explicit action on MEDIUM/LOW confidence items
- Default LOW confidence to "needs review" state

### 2. Missing Information Treated as Failure

**Problem:** Auto-analyze cannot extract a field (e.g., "target users" not in README), treats this as error, blocks audit or produces confusing results.

**Warning signs:**
- Auto-analyze fails on valid projects missing certain sections
- User sees "extraction error" for normal situations
- Manual entry workaround unclear

**Prevention:**
- Define required vs optional fields (only `project_name` required)
- Use "UNCERTAIN" as fallback value, not error state
- Prompt user to fill missing fields manually
- Explain why field could not be extracted: "No 'Target Users' section found in README"

### 3. Conflicting Information Not Flagged

**Problem:** package.json says "description: X" but README first paragraph says "Y". Auto-analyze picks one arbitrarily, user doesn't notice conflict, incorrect data used in audit.

**Warning signs:**
- Different extractions for same project depending on which file is read first
- User surprised by description used ("that's not what I wrote")
- Contradictory information in audit report

**Prevention:**
- Extract from all available sources (README, package.json)
- Compare values across sources
- Flag conflicts as MEDIUM confidence
- Show both values: "README says X, package.json says Y"
- Prompt user to choose or reconcile

### 4. Over-Extraction (Hallucinating Features)

**Problem:** LLM "extracts" features or capabilities not actually in project files, adds them to suggestions, user approves without noticing, audit evaluates non-existent features.

**Warning signs:**
- Extracted features sound plausible but aren't in README
- Generic features that could apply to any project type
- No specific source attribution for extractions
- User says "I didn't write that" when reviewing suggestions

**Prevention:**
- Ground extraction prompt: "Extract ONLY information explicitly stated in files. If not present, return UNCERTAIN."
- Show source line/section for each extraction
- Flag extractions without clear source as LOW confidence
- Limit feature count (top 5 features, not exhaustive list)
- Include "Show source" link for each extraction

### 5. Installation Time Misestimation

**Problem:** Auto-analyze estimates "2 minutes" for project requiring Docker setup, API keys, and database config. Gate 3 (10-Minute Test) fails incorrectly because estimate was wrong.

**Warning signs:**
- Estimated time consistently shorter than actual
- User completes Gate 3 evaluation, realizes estimate was off, has to revise
- Prerequisites ignored in time calculation

**Prevention:**
- Use conservative estimates (overestimate time)
- Flag prerequisites as complexity factors:
  * Docker/VM setup: +15 minutes
  * API key signup: +5 minutes
  * Database config: +10 minutes
- Mark installation time as MEDIUM confidence (difficult to estimate accurately)
- Provide range: "5-15 minutes depending on prerequisites"

---

## Limitations

Auto-analyze has explicit constraints and cannot replace human judgment:

### What Auto-Analyze Cannot Do

1. **Cannot score gates** - Extraction provides context only, not judgment
   - Auto-analyze extracts data FOR gate evaluation
   - Human auditor scores gates USING that data
   - Never auto-score gates based on extracted data

2. **Cannot guarantee accuracy** - Suggestions are hypotheses, not facts
   - Extraction is LLM-based, subject to interpretation errors
   - Source files may be outdated or incomplete
   - Human verification is REQUIRED step, not optional

3. **Cannot replace human verification** - All suggestions must be reviewed
   - Even HIGH confidence extractions can be wrong
   - User must check source attribution
   - Never auto-approve without explicit user action

4. **Works best with standard README structure** - May fail on unconventional docs
   - Assumes common section names ("Features", "Installation", "Why")
   - Custom or minimal READMEs produce LOW/UNCERTAIN confidence
   - Non-English READMEs may extract in source language (no translation)

### When Auto-Analyze Struggles

- README with non-standard structure (no clear sections)
- Very short README (<100 words)
- Projects using alternative documentation (CONTRIBUTING.md, docs/)
- README is just a bulleted list with no explanatory text
- README is generated from code comments (API docs only)

### Acceptable Failure Modes

Auto-analyze failure is acceptable when:
- README is genuinely insufficient for extraction → Prompts manual entry
- Conflicting information cannot be reconciled → Surfaces conflict to user
- Field is not applicable to project type → Marked as N/A
- Uncertainty is high → User makes decision with LOW confidence signal

**Failure is NOT blocking** - Manual entry always available as fallback.

---

## Related

- `skill/guides/INPUT-VALIDATION.md` - Pre-audit validation that runs before auto-analyze
- `skill/guides/GATE-EXAMPLES.md` - Examples of gate scoring (uses extracted data)
- `skill/config/gates-full.md` - Full gate definitions (extraction maps to these)
- `05-RESEARCH.md` - Research notes on auto-analyze design patterns
