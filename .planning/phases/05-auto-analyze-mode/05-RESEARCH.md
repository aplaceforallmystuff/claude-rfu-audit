# Phase 5: Auto-Analyze Mode - Research

**Researched:** 2026-01-22
**Domain:** Automated project analysis, metadata extraction, LLM-based structured extraction, human-in-the-loop workflows
**Confidence:** MEDIUM-HIGH

## Summary

Auto-analyze mode reduces manual data entry friction by extracting project information from README.md and package.json, then presenting suggestions for human verification before scoring. This is **friction reduction**, not automation of judgment — the human auditor remains the decision-maker.

Research into LLM-based extraction patterns, README structure conventions, and human-in-the-loop UX reveals three critical design principles:

1. **Extraction is hypothesis generation, not fact-finding** — Auto-extracted data should be presented as "Claude suggests..." not "Claude determined..."
2. **Structured prompting with confidence signals** — LLMs extract more reliably when prompted for specific fields with fallback values and confidence indicators
3. **Approval workflows prevent automation bias** — Users must explicitly approve/reject suggestions to avoid rubber-stamping incorrect extractions

The RFU Audit skill is Claude-based (no external dependencies), so extraction uses Claude's native document analysis capabilities. Pattern: Read project files → Structured extraction prompt → Present suggestions → Human verifies → Proceed to scoring.

**Primary recommendation:** Implement auto-analyze as a pre-gate step that populates gate context with extracted data, displays suggestions with confidence indicators, and requires explicit human confirmation before proceeding to gate scoring. Use markdown parsing for README structure, JSON parsing for package.json, and structured Claude prompts for semantic extraction (value proposition, features, use cases).

## Standard Stack

Auto-analyze for Claude Code skills uses Claude's built-in capabilities, not external libraries.

### Core

| Component | Version | Purpose | Why Standard |
|-----------|---------|---------|--------------|
| Claude document analysis | Native | Read and analyze README.md, package.json | Built-in, no dependencies, 200k token context window |
| Structured prompts | Native | Extract specific fields with confidence levels | Claude's training includes structured extraction patterns |
| Markdown parsing (conceptual) | Native | Identify sections, headings, lists in README | Claude understands markdown structure inherently |
| JSON parsing | Native | Extract metadata from package.json | Standard JSON handling in Claude responses |

### Supporting

| Pattern | Purpose | When to Use |
|---------|---------|-------------|
| Extraction templates | Define fields to extract with fallbacks | Every auto-analyze invocation |
| Confidence indicators | Flag low-confidence extractions for review | When LLM uncertain or contradictory info found |
| Approval prompts | Require human verification before using data | Before proceeding to gate scoring |
| Heuristic fallbacks | Use simple rules when LLM extraction fails | Missing package.json, very short README |

### Alternatives Considered

| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| Claude native analysis | Marked.js + custom parser | Adds dependency, unnecessary for Claude skill |
| Structured prompts | Fine-tuned extraction model | Overkill, prompts sufficient for this use case |
| Manual entry only | Skip auto-analyze | Slower, more tedious, reduces audit adoption |
| Full automation | No human verification | Dangerous, removes human judgment from validation |

**Installation:**
```bash
# No installation required - uses Claude's built-in capabilities
# Skill operates within Claude Code environment
```

## Architecture Patterns

### Recommended Auto-Analyze Flow

```
1. Trigger: User runs /rfu-audit --auto-analyze [path]
2. Validation: Run standard input validation (Phase 3 flow)
3. File Reading: Read README.md, package.json (if exists)
4. Extraction: Use structured Claude prompts to extract gate context
5. Presentation: Display extracted suggestions with confidence levels
6. Verification: Prompt user to approve/edit/reject each suggestion
7. Proceed: Use verified data as gate context for scoring
```

### Pattern 1: Structured Extraction with Fallbacks

**What:** Prompt Claude to extract specific fields with explicit fallback values when data is ambiguous or missing.

**When to use:** Every auto-analyze invocation to populate gate context.

**Example:**
```markdown
## Extraction Prompt Structure

Read the following project files and extract structured information:

**README.md:**
{readme_content}

**package.json:**
{package_json_content}

Extract the following fields. If a field cannot be determined, use "UNCERTAIN" as the value.

**Output format (JSON):**
{
  "project_name": "string",
  "one_line_description": "string (for Gate 4: Bartender Test)",
  "value_proposition": "string (what problem this solves)",
  "target_users": "string (who this is for)",
  "key_features": ["array", "of", "features"],
  "installation_time_estimate": "string (minutes)",
  "dependencies_count": "number",
  "confidence": {
    "project_name": "HIGH|MEDIUM|LOW",
    "one_line_description": "HIGH|MEDIUM|LOW",
    "value_proposition": "HIGH|MEDIUM|LOW"
  }
}

**Extraction rules:**
- project_name: Use package.json "name" field if exists, else README H1 heading
- one_line_description: First paragraph after H1 or package.json "description"
- value_proposition: Look for "why use this", "problems solved", or similar sections
- key_features: Extract from "Features" section or bullet lists
- installation_time_estimate: Estimate based on prerequisites, setup steps
- confidence: HIGH = explicit in docs, MEDIUM = inferred, LOW = guessed
```

**Why it works:**
- Explicit field definitions reduce ambiguity
- Confidence levels flag uncertain extractions for human review
- Fallback values prevent empty/null responses
- JSON output is parseable and structured

### Pattern 2: Human-in-the-Loop Verification

**What:** Present extracted suggestions with approve/edit/reject controls before using data.

**When to use:** Always — never proceed with unverified auto-extracted data.

**Example:**
```markdown
## Auto-Analyze Suggestions

Claude extracted the following from your project files. Review and verify:

### Project Name
**Extracted:** claude-rfu-audit
**Source:** package.json "name" field
**Confidence:** HIGH
[✓ Approve] [✎ Edit] [✗ Reject]

### One-Line Description (Gate 4)
**Extracted:** "A Claude Code skill that validates project utility through an 11-gate gauntlet"
**Source:** README.md first paragraph
**Confidence:** HIGH
[✓ Approve] [✎ Edit] [✗ Reject]

### Value Proposition (Gate 1, Gate 2)
**Extracted:** "Forces validation of project utility before significant investment to prevent building things nobody wants"
**Source:** README.md "The Problem" section
**Confidence:** MEDIUM (inferred from problem statement)
[✓ Approve] [✎ Edit] [✗ Reject]

### Key Features (Gate 10: Pareto)
**Extracted:**
- 11-gate validation framework
- Scoring system (11/11 to 0-5)
- Actionable priority fixes
- Integration with wisdom skills
**Source:** README.md "The 11 Gates" and "Example Output" sections
**Confidence:** HIGH
[✓ Approve] [✎ Edit] [✗ Reject]

---

**Actions:**
- Press Enter to approve all HIGH confidence suggestions
- Type field number to edit (e.g., "2" to edit description)
- Type "reject" to discard auto-analyze and enter manually
```

**Why it works:**
- Shows extracted data with source attribution for transparency
- Confidence levels guide user attention to uncertain fields
- Approve/edit/reject gives user full control
- Defaults to trust HIGH confidence, review MEDIUM/LOW

### Pattern 3: README Section Identification Heuristics

**What:** Use common README structure patterns to locate specific information types.

**When to use:** When extracting value proposition, features, installation steps from README.

**Example heuristics:**
```yaml
One-line description:
  - First sentence after H1 heading
  - Text between title and first H2
  - package.json "description" field
  - Text in bold immediately after title

Value proposition:
  - Section titled "Why", "Problem", "The Problem", "Motivation"
  - First paragraph of "About" or "Overview" section
  - Text following "**Stop...**" or "**Never...**" (imperative pattern)

Features:
  - Section titled "Features", "Capabilities", "What it does"
  - Bullet lists in top 30% of README
  - Numbered lists with action verbs (Create, Generate, Validate)

Installation time:
  - Count steps in "Installation" or "Setup" section
  - Check for "Prerequisites" or "Requirements" section
  - Heuristic: 0 prereqs = 2 min, 1-2 prereqs = 5 min, 3+ = 10 min

Target users:
  - Section titled "Who is this for", "Audience", "Use Cases"
  - Phrases like "for developers who", "if you need to", "helps teams"
```

**Why it works:**
- Leverages established README conventions from 2026 best practices
- Multiple fallback heuristics increase extraction success rate
- Section titles are reliable signals across projects

### Pattern 4: Confidence-Based Presentation

**What:** Adjust UI prominence based on extraction confidence level.

**When to use:** Presenting suggestions to balance trust and verification.

**Example:**
```markdown
Presentation rules:

HIGH confidence (explicit in docs):
  - Display with green checkmark ✓
  - Default to "approved" state (user can change)
  - Minimal explanation needed

MEDIUM confidence (inferred from context):
  - Display with yellow warning ⚠
  - Default to "needs review" state
  - Show reasoning: "Inferred from [section name]"

LOW confidence (guessed or contradictory):
  - Display with red question mark ❓
  - Default to "rejected" state
  - Show why uncertain: "Multiple possible values found" or "No explicit statement"
  - Suggest manual entry

UNCERTAIN (could not extract):
  - Display as empty with prompt
  - No suggestion provided
  - "Could not extract from files. Please enter manually."
```

**Why it works:**
- Visual indicators (✓ ⚠ ❓) communicate confidence at a glance
- Default states align with trustworthiness (HIGH = approved, LOW = rejected)
- Explanations build user trust in the extraction process

### Anti-Patterns to Avoid

- **Auto-approve without review:** Bypasses human judgment, defeats purpose of verification
- **Generic "project description" extraction:** Too vague, doesn't map to specific gates
- **Single-source extraction:** If README contradicts package.json, must flag conflict
- **No confidence signals:** User can't distinguish certain vs uncertain extractions
- **Extraction failure = audit failure:** Gracefully degrade to manual entry, don't block audit

## Don't Hand-Roll

Problems that have existing solutions:

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Markdown parsing | Custom regex for headings | Claude native markdown understanding | Claude trained on markdown, handles edge cases |
| JSON extraction | String parsing for package.json | Standard JSON parse | JSON is structured, parsing is reliable |
| Feature extraction | Keyword matching | LLM semantic analysis | Features expressed variably, LLMs understand intent |
| Installation complexity | Count lines in README | LLM estimation from prerequisites | Complexity is semantic (Docker setup vs npm install) |
| Confidence scoring | Binary yes/no | Multi-level (HIGH/MEDIUM/LOW) | Captures gradations of certainty |

**Key insight:** The RFU Audit skill runs in Claude Code, so Claude is *already present* to read and analyze documents. Use Claude's document understanding capabilities rather than building custom parsers. The challenge is structuring the prompts, not parsing the data.

## Common Pitfalls

### Pitfall 1: Automation Bias (Trusting Incorrect Suggestions)

**What goes wrong:** User rubber-stamps auto-extracted data without verification, incorrect info flows into gate scoring, produces invalid audit results.

**Why it happens:**
- Auto-extracted data presented as authoritative
- No confidence signals to flag uncertain extractions
- Approve action is too easy (single click without review)
- User assumes "AI extracted it, must be right"

**How to avoid:**
1. Present extractions as **suggestions**, not facts ("Claude suggests" not "Claude determined")
2. Show confidence levels with visual indicators (✓ ⚠ ❓)
3. Display source attribution ("Extracted from README line 15")
4. Require explicit action on MEDIUM/LOW confidence items
5. Default LOW confidence to "needs review" state

**Warning signs:**
- User approves all suggestions without reading them
- No edits made to MEDIUM/LOW confidence extractions
- Audit results contain information not in project files

**Implementation:**
```markdown
WRONG:
**Project Name:** claude-rfu-audit ✓
(appears authoritative, no context)

RIGHT:
**Project Name (extracted):** claude-rfu-audit
**Source:** package.json "name" field
**Confidence:** HIGH ✓
**Action:** [✓ Approve] [✎ Edit] [✗ Reject]
(shows this is a suggestion with evidence)
```

### Pitfall 2: Missing Information Treated as Failure

**What goes wrong:** Auto-analyze cannot extract a field (e.g., "target users" not in README), treats this as error, blocks audit or produces confusing results.

**Why it happens:**
- Extraction logic assumes all fields must be present
- No graceful degradation to manual entry
- Error messages imply user did something wrong

**How to avoid:**
1. Define required vs optional fields (only project_name required)
2. Use "UNCERTAIN" as fallback value, not error state
3. Prompt user to fill missing fields manually
4. Explain why field could not be extracted: "No 'Target Users' section found in README"

**Warning signs:**
- Auto-analyze fails on valid projects missing certain sections
- User sees "extraction error" for normal situations
- Manual entry workaround unclear

**Implementation:**
```markdown
WRONG:
Error: Could not extract "target_users" field. Auto-analyze failed.

RIGHT:
**Target Users**
**Extracted:** UNCERTAIN (no "Who is this for" or "Audience" section found in README)
**Confidence:** N/A
**Action:** [✎ Enter manually]

(graceful degradation to manual entry)
```

### Pitfall 3: Conflicting Information Not Flagged

**What goes wrong:** package.json says "description: X" but README first paragraph says "Y". Auto-analyze picks one arbitrarily, user doesn't notice conflict, incorrect data used in audit.

**Why it happens:**
- Extraction logic uses first-found value without checking alternatives
- No comparison between sources (README vs package.json)
- Confidence scoring doesn't account for conflicts

**How to avoid:**
1. Extract from all available sources (README, package.json, other docs)
2. Compare values across sources
3. Flag conflicts as MEDIUM or LOW confidence
4. Show both values: "README says X, package.json says Y"
5. Prompt user to choose or reconcile

**Warning signs:**
- Different extractions for same project depending on which file is read first
- User surprised by description used ("that's not what I wrote")
- Contradictory information in audit report

**Implementation:**
```markdown
**One-Line Description**
**README (line 4):** "Stop building things nobody wants"
**package.json:** "A Claude Code skill for project validation"
**Conflict detected:** These differ significantly
**Confidence:** MEDIUM ⚠
**Suggested:** Use README version (more user-facing)
**Action:** [✓ Approve suggested] [✎ Edit] [Package.json version] [Write custom]
```

### Pitfall 4: Over-Extraction (Hallucinating Features Not Present)

**What goes wrong:** LLM "extracts" features or capabilities not actually in project files, adds them to suggestions, user approves without noticing, audit evaluates non-existent features.

**Why it happens:**
- LLM fills gaps with plausible but incorrect information
- Extraction prompt too vague ("extract features")
- No grounding constraint ("only use info from files provided")
- User trusts suggestions without verifying against source

**How to avoid:**
1. Ground extraction prompt: "Extract ONLY information explicitly stated in files. If not present, return UNCERTAIN."
2. Show source line/section for each extraction
3. Flag extractions without clear source as LOW confidence
4. Limit feature count (top 5 features, not exhaustive list)
5. Include "Show source" link for each extraction

**Warning signs:**
- Extracted features sound plausible but aren't in README
- Generic features that could apply to any project type
- No specific source attribution for extractions
- User says "I didn't write that" when reviewing suggestions

**Implementation:**
```markdown
Extraction prompt (grounding):
"Extract key features from README.md. Rules:
- ONLY extract features explicitly listed or demonstrated
- DO NOT infer features from project type (e.g., don't assume 'logging' for Node.js)
- If feature is mentioned but not described, mark UNCERTAIN
- Cite line number or section for each feature"

Presentation (with source):
**Key Features:**
1. 11-gate validation framework [source: README line 14, "The 11 Gates" section]
2. Scoring system 11/11 to 0-5 [source: README line 29, "Scoring" table]
3. UNCERTAIN: Priority matrix (mentioned in line 98 but not implemented yet)
```

### Pitfall 5: Installation Time Misestimation

**What goes wrong:** Auto-analyze estimates "2 minutes" for project requiring Docker setup, API keys, and database config. Gate 3 (10-Minute Test) fails incorrectly because estimate was wrong.

**Why it happens:**
- Heuristic counts steps without understanding complexity
- Prerequisites listed but time not estimated
- Setup instructions vague ("Install dependencies" could be 1 minute or 30)

**How to avoid:**
1. Use conservative estimates (overestimate time)
2. Flag prerequisites as complexity factors:
   - Docker/VM setup: +15 minutes
   - API key signup: +5 minutes
   - Database config: +10 minutes
3. Mark installation time as MEDIUM confidence (difficult to estimate accurately)
4. Provide range: "5-15 minutes depending on prerequisites"

**Warning signs:**
- Estimated time consistently shorter than actual
- User completes Gate 3 evaluation, realizes estimate was off, has to revise
- Prerequisites ignored in time calculation

**Implementation:**
```markdown
**Installation Time Estimate**
**README prerequisites:**
- Node.js 18+ (assume already installed for target users)
- Docker (not common, +15 minutes)
- OpenAI API key (+5 minutes signup)

**Steps in installation:**
1. Clone repo (1 min)
2. npm install (2 min)
3. Docker setup (15 min)
4. API key config (5 min)
5. Test installation (2 min)

**Estimated total:** 25 minutes
**Confidence:** MEDIUM ⚠ (depends on user's existing setup)
**Note:** If user already has Docker and API key, time drops to 5 minutes.

**Action:** [✓ Approve] [✎ Edit estimate] [Mark as UNCERTAIN]
```

## Code Examples

Patterns for implementing auto-analyze in Claude Code skill context.

### Structured Extraction Prompt

```markdown
# AUTO-ANALYZE.md Implementation Pattern

## Extraction Prompt Template

When user invokes --auto-analyze flag:

1. Read project files:
   - README.md (required, already validated)
   - package.json (optional, check existence first)

2. Use this structured prompt:

"""
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
    ...
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
"""

3. Parse JSON response
4. Present suggestions to user
5. Await verification
```

### Human Verification Interaction

```markdown
## Verification Prompt Template

After extraction, display:

"""
## Auto-Analyze Results

Claude extracted the following information from your project files.
Review each field and approve, edit, or reject.

{for each field}
### {field_name}
**Extracted:** {extracted_value}
**Source:** {source_attribution}
**Confidence:** {confidence_level} {icon}

{if confidence == HIGH}
✓ This extraction has high confidence. Press Enter to approve.
{else if confidence == MEDIUM}
⚠ This extraction is inferred. Please verify before approving.
{else if confidence == LOW}
❓ This extraction has low confidence. Consider manual entry.
{endif}

**Actions:**
[1] Approve as-is
[2] Edit value
[3] Reject and enter manually
[4] Show source in files

Your choice: ___
{end for}

---

**Batch Actions:**
- Press 'a' to approve all HIGH confidence fields
- Press 'r' to review all MEDIUM/LOW fields
- Press 'q' to quit auto-analyze and enter manually

What would you like to do?
"""

Handle user input:
- 1: Mark field as verified, proceed
- 2: Prompt for new value, mark as manually entered
- 3: Mark field as UNCERTAIN, will prompt during gate scoring
- 4: Display source text from README/package.json
- a: Approve all HIGH confidence, proceed to MEDIUM/LOW review
- r: Skip to first MEDIUM/LOW field
- q: Discard all extractions, proceed to standard manual audit
```

### Confidence Calculation Logic

```markdown
## Confidence Scoring Heuristic

Determine confidence level for each extraction:

**HIGH confidence requires:**
- Value found in clear, labeled section (e.g., "## Features" section for features)
- Single unambiguous source (no conflicts)
- Exact match (not paraphrased or inferred)
- Examples: package.json fields, section headings, explicit statements

**MEDIUM confidence when:**
- Value inferred from context (e.g., value prop inferred from problem statement)
- Multiple sources with similar but not identical values
- Paraphrased or summarized from longer text
- Examples: installation time estimate, target users from use cases

**LOW confidence when:**
- Multiple conflicting sources
- Very limited information (e.g., 2-line README)
- Generic fallback based on project type
- Examples: guessing target users, estimating time with no installation section

**UNCERTAIN when:**
- Information not present in files
- Too ambiguous to extract reliably
- User input required for accuracy
- Examples: missing features section, no problem statement
```

### Graceful Degradation Pattern

```markdown
## Handling Missing or Failed Extraction

When extraction fails or returns UNCERTAIN:

1. **Don't block the audit** - proceed with manual entry for that field

2. **Explain what happened:**
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

3. **Provide context:** Tell user why field matters and when it will be used

4. **Offer options:** Manual entry now, defer to gate scoring, or skip

5. **Remember user choice:** If user defers, prompt during relevant gate
```

## State of the Art

| Old Approach | Current Approach (2026) | When Changed | Impact |
|--------------|-------------------------|--------------|--------|
| Manual project analysis | LLM-based extraction with human verification | 2024-2025 | 10x faster initial analysis, but requires verification workflow |
| Simple keyword matching | Semantic understanding via LLMs | 2023-2024 | Handles varied phrasing, but risk of hallucination |
| Binary extraction (success/fail) | Confidence levels (HIGH/MEDIUM/LOW/UNCERTAIN) | 2025-2026 | Better UX, guides user attention to uncertain fields |
| Regex-based markdown parsing | LLM native document understanding | 2024-2025 | More robust to format variations, but requires prompt engineering |
| Auto-approve suggestions | Human-in-the-loop verification | 2025-2026 | Prevents automation bias, maintains audit integrity |

**Emerging patterns (2026):**
- **Agentic UX with guardrails:** AI suggests, human approves (standard pattern for high-stakes workflows)
- **Structured extraction with fallbacks:** JSON output with explicit UNCERTAIN values (LLamaIndex, LangChain patterns)
- **Progressive disclosure:** Show HIGH confidence first, defer LOW confidence review (reduces cognitive load)
- **Source attribution:** Every extraction shows origin ("README line 15") for transparency

**Deprecated/outdated:**
- Fully automated project analysis without verification (2022-2023 approach, now considered risky)
- Generic "extract all features" prompts (2023, replaced by structured field extraction)
- Single-confidence level (success/fail) without gradations (pre-2025)

## Open Questions

Things that couldn't be fully resolved:

1. **Should auto-analyze extract information for ALL gates or subset?**
   - What we know: Gates 1-5 are most data-dependent, Gates 6-11 require more reasoning
   - What's unclear: Is it valuable to extract for Gates 6-11, or just add noise?
   - Recommendation: Start with Gates 1-5 extraction (5 Whys, Inversion, 10-Minute, Bartender, Wallet), expand to others based on user feedback

2. **How to handle projects with multiple READMEs (README.md, CONTRIBUTING.md, docs/)?**
   - What we know: Validation only checks README.md, but other docs may contain relevant info
   - What's unclear: Should auto-analyze read additional docs if present?
   - Recommendation: Start with README.md only. If extraction confidence is LOW across multiple fields, prompt: "Found additional docs. Analyze CONTRIBUTING.md for more context?"

3. **What to do when package.json and README significantly contradict?**
   - What we know: Both are authoritative sources, but README is user-facing, package.json is developer-facing
   - What's unclear: Which should take precedence?
   - Recommendation: Present conflict to user: "README says X, package.json says Y. Which is correct for audit purposes?" with suggestion to use README (user-facing is more relevant for RFU validation)

4. **Should auto-analyze learn from user corrections over time?**
   - What we know: User edits reveal extraction failures
   - What's unclear: Is there a pattern to corrections that could improve future extractions?
   - Recommendation: Not for v1 (stateless skill), but log user edits to a file (.planning/auto-analyze-corrections.log) for future analysis and prompt refinement

5. **How to handle non-English READMEs?**
   - What we know: Claude supports multiple languages
   - What's unclear: Should extraction prompt specify English output, or preserve source language?
   - Recommendation: Extract in source language, present to user in source language (don't translate), let human auditor work in their native language

## Sources

### Primary (HIGH confidence)

README structure and extraction patterns:
- [Ultimate Guide to README Docs | Archbee Blog](https://www.archbee.com/blog/readme-files-guide)
- [Make a README](https://www.makeareadme.com/)
- [Professional README Guide | The Full-Stack Blog](https://coding-boot-camp.github.io/full-stack/github/professional-readme-guide/)

package.json metadata:
- [Mastering package.json: A Comprehensive Guide | Medium](https://medium.com/@dev.alisamir/mastering-package-json-a-comprehensive-guide-481d9d87645d)
- [package.json | npm docs](https://docs.npmjs.com/files/package.json/)

LLM structured extraction:
- [Claude AI: PDF Reading, Visual Analysis, Structured Extraction | DataStudios](https://www.datastudios.org/post/claude-ai-pdf-reading-visual-analysis-structured-extraction-and-long-document-processing)
- [LLMs for Structured Data Extraction from PDFs in 2026 | Unstract](https://unstract.com/blog/comparing-approaches-for-using-llms-for-structured-data-extraction-from-pdfs/)

### Secondary (MEDIUM confidence)

Markdown parsing patterns:
- [The Benefits of Using Markdown for Efficient Data Extraction | ScrapingAnt](https://scrapingant.com/blog/markdown-efficient-data-extraction)
- [markdown-analysis PyPI](https://pypi.org/project/markdown-analysis/)
- [GitHub - sean0x42/markdown-extract](https://github.com/sean0x42/markdown-extract)

Claude prompting best practices:
- [Claude Skills: Build repeatable workflows in Claude | Zapier](https://zapier.com/blog/claude-skills/)
- [Prompting best practices - Claude Docs](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-4-best-practices)

Human-in-the-loop UX patterns (2026):
- [Progressive Disclosure Matters: Applying 90s UX Wisdom to 2026 AI Agents](https://aipositive.substack.com/p/progressive-disclosure-matters)
- [10 AI-Driven UX Patterns Transforming SaaS in 2026 | Orbix](https://www.orbix.studio/blogs/ai-driven-ux-patterns-saas-2026)

CLI approval workflows:
- [Codex CLI features | OpenAI](https://developers.openai.com/codex/cli/features/)
- [Gemini CLI Tips & Tricks - by Addy Osmani](https://addyo.substack.com/p/gemini-cli-tips-and-tricks)

### Tertiary (LOW confidence - WebSearch only, marked for validation)

Automated metadata extraction challenges:
- [False Positives in AI Detection: Complete Guide 2026](https://proofademic.ai/blog/false-positives-ai-detection-guide/)
- [Automated metadata extraction: challenges and opportunities](https://www.osti.gov/servlets/purl/1897834)

Heuristic extraction research:
- [arXiv:2506.15453v1 - README code snippet descriptions using LLMs](https://brittany-reid.github.io/publication/nugroho-2025-uncovering/nugroho-2025-uncovering.pdf) (2025 research paper)

## Metadata

**Confidence breakdown:**
- README structure patterns: HIGH - Multiple authoritative guides, consistent patterns across 2026 sources
- package.json metadata: HIGH - Official npm documentation, well-established format
- LLM extraction patterns: MEDIUM-HIGH - Recent capabilities (2025-2026), patterns established but evolving
- Human-in-the-loop UX: MEDIUM - Emerging patterns, not yet fully standardized
- Specific confidence scoring heuristics: LOW - Extrapolated from general practices, needs pilot testing

**Research date:** 2026-01-22
**Valid until:** 30 days (February 2026) - LLM extraction patterns evolving rapidly, verification patterns stabilizing

**Critical gaps requiring validation:**
1. Specific confidence thresholds (when HIGH vs MEDIUM vs LOW) need pilot testing with real projects
2. Installation time estimation heuristics are rough approximations, need calibration
3. Conflict resolution preferences (README vs package.json) need user feedback
4. Extraction prompt effectiveness requires iteration based on false positive rate
5. Optimal verification UX (approval flow) needs usability testing

**Next steps for planner:**
- Pilot test extraction prompts with 5-10 diverse projects (Node.js, Python, Rust)
- Measure extraction accuracy by field (project_name likely HIGH accuracy, installation_time likely MEDIUM)
- Define minimum viable extraction (which fields MUST extract successfully vs optional)
- Test verification workflow UX with prototype
- Establish acceptable false positive rate (suggest 5-10% for MEDIUM confidence fields)
