# Technology Stack

**Analysis Date:** 2026-01-22

## Languages

**Primary:**
- Markdown - Documentation and skill definition
- Bash - Installation and shell scripting

**Secondary:**
- None detected

## Runtime

**Environment:**
- macOS (target platform based on README and install script)
- Bash shell (3.2+)

**Package Manager:**
- N/A - No package manager required

## Frameworks

**Core:**
- Claude Code Skills Framework - Skill delivery system for Claude Code (version not specified, used via `/rfu-audit` command syntax)

**Development/Tooling:**
- None detected - This is a skill, not a traditional application

## Key Dependencies

**Critical:**
- Claude Code CLI - Required for skill execution (https://docs.anthropic.com/en/docs/claude-code)
  - Provides the skill invocation system
  - No external SDK or library dependencies

**Infrastructure:**
- GitHub - Repository hosting (https://github.com/aplaceforallmystuff/claude-rfu-audit.git)

## Configuration

**Environment:**
- No environment variables required
- No configuration files (.env, .json, .yaml detected)
- No runtime secrets or API keys needed

**Build:**
- No build process - Skill is delivered as static Markdown files
- Installation via `install.sh` script that copies files to `~/.claude/skills/rfu-audit/`

## Platform Requirements

**Development:**
- Git - For cloning repository
- macOS or Unix-like environment - For shell script execution
- Bash 3.2 or later - For install.sh script

**Execution:**
- Claude Code application (installed locally)
- Skill installed to `~/.claude/skills/rfu-audit/`

**Skill Dependencies:**
- Integration with optional related skills mentioned in `SKILL.md`:
  - `/wisdom-clarify` - 5 Whys questioning (optional enhancement)
  - `/wisdom-stoic-premeditation` - Via negativa/inversion (optional enhancement)
  - `/taches-cc-resources:consider:second-order` - Second-order consequences (optional enhancement)
  - `/taches-cc-resources:consider:pareto` - 80/20 analysis (optional enhancement)
  - `/taches-cc-resources:consider:occams-razor` - Simplicity check (optional enhancement)
  - `/think-first` - Decision framework (optional enhancement)
  - `/wisdom-ground` - Philosophical frameworks (optional enhancement)
  - `/prep-repo` - Repository preparation (used after RFU certification)

## Skill Architecture

**Delivery Method:**
- Claude Code Skill - Invokable via `/rfu-audit` command
- Stateless prompt-based execution (all logic embedded in skill definition)

**Entry Point:**
- `skill/SKILL.md` - Skill definition file (7.5 KB)
- Defines 11 gates and scoring framework
- Provides template and process instructions

**Output Template:**
- `skill/templates/audit-report.md` - Markdown report template with 70+ placeholders for structured audit output

---

*Stack analysis: 2026-01-22*
