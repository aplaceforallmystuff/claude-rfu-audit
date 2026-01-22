# Coding Conventions

**Analysis Date:** 2026-01-22

## Project Type

This is a Claude Code skill — a documentation and template-based project. There is no compiled code, no runtime, no linting tools, or dependency management. The project consists of:

- Markdown files for documentation and skill definition
- YAML frontmatter for skill metadata
- Shell script for installation
- Markdown template files for output generation

## Naming Patterns

**Files:**
- `SKILL.md` - Skill definition and documentation (PascalCase with .md extension)
- `README.md` - Project overview (standard naming)
- `audit-report.md` - Output template (lowercase with hyphens)
- `install.sh` - Installation script (lowercase with hyphens)
- `.planning/codebase/` - Planning documents directory

**Template Variables:**
- `{{variable_name}}` - Template placeholders use double curly braces with snake_case (e.g., `{{gate1_status}}`, `{{bedrock_need}}`)
- Status indicators: `{{gate1_status}}` for pass/fail markers

## Markdown Style

**Headers:**
- Use `#` for main title (single level)
- Use `##` for major sections
- Use `###` for subsections
- Use `####` for sub-subsections

**Structure:**
- Lead with YAML frontmatter for metadata (see `SKILL.md` lines 1-5)
- Follow metadata with title and sections
- Use consistent spacing between sections
- Include "---" as section dividers for clarity

**Code Blocks:**
- Use triple backticks with language specifier for syntax highlighting
- Show actual command examples with bash language specified
- Use plain backticks for inline code references

**Lists:**
- Use `-` for unordered lists
- Use `1. 2. 3.` for numbered lists (when sequence matters)
- Indent sub-items with 2 spaces
- Use `|` for markdown tables with headers

**Formatting:**
- Use **bold** for emphasis on terms or critical information
- Use `backticks` for file paths, code, and technical terms
- Use `>` for blockquotes (as in example sentences)
- Use italics for conceptual framing (*concept*)

## Documentation Patterns

**Skill Metadata (SKILL.md):**
- Lines 1-5: YAML frontmatter with `name`, `description`, `user-invocable`
- Line 7+: Title, philosophy, usage instructions
- Each major concept gets dedicated section with consistent depth

**Table Usage:**
- Tables for comparative information (Gates, scoring, components)
- Format: `| Header | Header |` with `|----|-----|` separator
- Include status emoji indicators (✅, ❌) where appropriate

**Cross-References:**
- Link to related skills using `/skill-name` format
- Reference files using backtick paths: `templates/audit-report.md`
- Reference lines by number in documentation

**Template File Pattern (audit-report.md):**
- Markdown structure with variable placeholders
- Variables follow pattern: `{{descriptive_snake_case}}`
- Status variables use: `{{gateN_status}}` format
- Section dividers: `---` between major gates
- Scorecard: Simple table with gate numbers, names, and status

## Shell Script Conventions

**Location:** `install.sh` at project root

**Structure:**
- Shebang: `#!/bin/bash`
- Error handling: `set -e` at top
- Color variables for output: `RED`, `GREEN`, `YELLOW`, `NC` (no color)
- Comments explaining purpose above logical blocks

**Patterns:**
- Use quotes around variables: `"$HOME"`, `"$INSTALL_DIR"`
- Use parameter expansion for paths: `$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)`
- Check for existing files/dirs before operations
- Provide `--force` override flag for reinstallation
- Verify installation success before reporting completion

**Output:**
- Echo informational messages to stdout
- Use color variables for status indicators:
  - `${GREEN}Success!${NC}` for success
  - `${YELLOW}Warning:${NC}` for warnings
  - `${RED}Error:${NC}` for errors
- Include usage examples in success message

## Error Handling

**Patterns:**
- Shell script uses `set -e` for fail-on-error behavior
- Conditional checks: `if [ -d "$INSTALL_DIR" ]` before operations
- Exit codes: `exit 1` for failures, implicit 0 for success
- Graceful degradation: Warn user about existing installation, suggest `--force` flag

**Documentation of Errors:**
- Error conditions documented in gate definition sections
- Fail indicators listed as bullet points under each gate
- No exceptions raised — documentation only (this is markdown skill)

## Comments

**Style:**
- Shell scripts use `#` for comments
- Comments precede the code block they explain
- Section headers use comment blocks for clarity

**Example from install.sh:**
```bash
# Check for existing installation
if [ -d "$INSTALL_DIR" ] && [ "$1" != "--force" ]; then
```

**When to Comment:**
- Explain non-obvious logic (like parameter expansion for script dir)
- Document what each major section does
- Clarify installation steps

## Module Organization

**Exported Content:**
- Skill definition exported via `SKILL.md`
- Installation script is primary export mechanism
- Template file is consumed by Claude instances running the skill

**File Structure:**
```
skill/
├── SKILL.md              # Skill definition (primary)
└── templates/
    └── audit-report.md   # Output template
```

**Dependency Chain:**
- `install.sh` copies entire `skill/` directory to `~/.claude/skills/rfu-audit`
- When `/rfu-audit` is invoked, Claude reads `SKILL.md` for skill definition
- Claude uses `templates/audit-report.md` as template for generating audit reports

## Version/Metadata

**Project Metadata:**
- Defined in SKILL.md frontmatter (lines 1-5)
- Skill name: `rfu-audit`
- User-invocable: `true`
- No explicit version tracking in code; versioning via Git tags expected

**Related Skills:**
- Documented in SKILL.md integration section (lines 264-271)
- References other skills: `/wisdom-clarify`, `/wisdom-stoic-premeditation`, etc.
- Establishes conceptual relationships, not code dependencies

## Process Examples

**Template Filling Example (audit-report.md):**
- Variable format: `{{variable_name}}`
- Status markers: `{{gate1_status}}` yields ✅ or ❌
- Context replacement: `{{project_name}}` → actual project name
- Boolean conversions: `{{bartender_jargon}}` → "Yes" or "No"

**Conditional Structure:**
- Gates follow identical pattern: Name, questions, verdict, details
- Templates repeat this structure (lines 19-182)
- Each gate gets dedicated section with template variables

---

*Convention analysis: 2026-01-22*
