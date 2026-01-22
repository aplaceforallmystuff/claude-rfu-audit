# External Integrations

**Analysis Date:** 2026-01-22

## APIs & External Services

**Not Detected:**
- This skill does not integrate with external APIs or third-party services
- No SDK clients or API credentials required

## Data Storage

**Databases:**
- Not used - Skill is stateless

**File Storage:**
- Local filesystem only
- Output: Markdown audit reports (generated locally, no upload)
- Input: Reads project files from local system (README, package.json, source code)

**Caching:**
- None - Skill executes fresh for each invocation

## Authentication & Identity

**Auth Provider:**
- Not applicable - Skill executes within Claude Code context
- No external authentication required

## Monitoring & Observability

**Error Tracking:**
- None - Errors handled through Claude's response generation

**Logs:**
- No logging system - All output is provided in interactive Claude response

## CI/CD & Deployment

**Hosting:**
- GitHub repository (https://github.com/aplaceforallmystuff/claude-rfu-audit.git)
- Distributed via git clone or shell installation script

**CI Pipeline:**
- Not detected - No automated testing or deployment pipeline

**Installation:**
- Local installation via `install.sh` script
- Copies skill files to `~/.claude/skills/rfu-audit/` directory

## Environment Configuration

**Required env vars:**
- None

**Optional env vars:**
- None detected

**Secrets location:**
- No secrets required
- No credentials, API keys, or tokens needed

## Webhooks & Callbacks

**Incoming:**
- None

**Outgoing:**
- None - Skill generates output through Claude's standard response interface

## Related Systems

**GitHub Integration:**
- Repository: https://github.com/aplaceforallmystuff/claude-rfu-audit
- License: MIT (open source)
- No GitHub API calls or webhooks

**Claude Code Integration:**
- Skill framework is embedded in Claude Code
- User invokes via `/rfu-audit [project-path]` command
- All execution happens within Claude's inference engine

## User-Provided Data

**Input Sources:**
- Project path (optional, defaults to current working directory)
- Project files read from local filesystem:
  - README.md - Project description and context
  - package.json - Dependencies and project metadata
  - Source code files (varies by project language)

**Output:**
- Structured Markdown audit report (11-gate scorecard)
- Evidence-based gate verdicts
- Prioritized fix recommendations

---

*Integration audit: 2026-01-22*
