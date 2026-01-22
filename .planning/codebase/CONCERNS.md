# Codebase Concerns

**Analysis Date:** 2026-01-22

## Tech Debt

**Template Placeholder System:**
- Issue: The audit report template at `skill/templates/audit-report.md` uses a string interpolation approach with `{{variable}}` placeholders. This is a fragile handoff mechanism with no validation that required variables are present before rendering.
- Files: `skill/templates/audit-report.md`
- Impact: If Claude Code skill execution forgets to populate a placeholder (e.g., `{{score}}`), the output will show the template variable literal rather than the actual value, making the report appear unfinished or broken to users.
- Fix approach: Implement a template validation step that checks all required placeholders are populated before returning the report. Alternatively, migrate to a structured output format (JSON) that Claude can fill programmatically with guaranteed type safety.

**Manual Installation Process:**
- Issue: The skill requires manual installation via `bash install.sh` rather than leveraging Claude Code's built-in skill management system.
- Files: `install.sh`
- Impact: Users must manually manage installation updates, versions, and uninstallation. If they forget the `--force` flag, the installer warns but doesn't proceed, creating friction for upgrading to newer versions.
- Fix approach: Investigate if Claude Code supports declarative skill configuration or versioned skill packages rather than shell scripts. This reduces user toil and makes updates automatic.

**No Input Validation:**
- Issue: The skill definition at `skill/SKILL.md` accepts a project path argument but there's no documented error handling for invalid paths, missing files, or unreadable directories.
- Files: `skill/SKILL.md` (lines 19-24)
- Impact: Users passing non-existent paths or directories they don't have permission to read will get unclear error messages, potentially confusing them about whether the skill is broken or their input is wrong.
- Fix approach: Add explicit error handling documentation and examples of what happens with common failure modes (missing README, permission denied, not a project directory).

## Known Gaps

**No Implementation Code Shown:**
- Issue: The repository contains only documentation and templates (`README.md`, `skill/SKILL.md`, `audit-report.md` template). There is no actual skill implementation code (no Python, TypeScript, or executable logic for running the audit).
- Files: N/A (gap in repository structure)
- Impact: This appears to be a specification document, not a working implementation. Users running `/rfu-audit` will fail unless there's hidden implementation that invokes the 11 gates, collects responses, and fills the template.
- Priority: Critical — The skill cannot function without implementation code that exists somewhere (likely in a Claude Code runtime or separate execution context).

**Template Variable Coverage Incomplete:**
- Issue: The template has 200+ placeholder variables (`{{gate1_status}}`, `{{bedrock_need}}`, etc.) but there's no specification document showing which variables are required vs. optional, or their expected formats.
- Files: `skill/templates/audit-report.md`
- Impact: When implementing the skill, developers must reverse-engineer the template structure to know what data to collect. This is error-prone and leads to inconsistent/incomplete reports.
- Fix approach: Create a `TEMPLATE_SPEC.md` that lists all variables, their expected content, valid values (e.g., is `gate1_status` a string or an emoji?), and usage context.

**No Error States Documented:**
- Issue: The gates use binary pass/fail scoring, but the template and SKILL.md don't document what happens if a gate is ambiguous or the user provides contradictory evidence.
- Files: `skill/SKILL.md` (all 11 gates), `skill/templates/audit-report.md`
- Impact: If evidence is weak but not completely failing, auditors might overweight subjective interpretation, leading to inconsistent scores across different projects.
- Fix approach: Add "gray area" guidelines for borderline gates and define scoring rules (e.g., "if 2+ people confirm, it passes Gate 5").

## Fragile Areas

**Gate 5 (Wallet Test) — Vague Specific People Requirement:**
- Files: `skill/SKILL.md` (lines 113-124)
- Why fragile: The gate requires "3 specific people (not archetypes)" who would pay. But "specific" is undefined — does this mean named LinkedIn profiles? Customers? Friends? People you haven't asked yet?
- Safe modification: Add examples: "✅ 'Sarah, my colleague at Acme, uses similar tools daily' vs ❌ 'Developers would pay'". Show the difference between specific and generic.
- Test coverage gap: No validation that the auditor actually asked these people or projected hypothetically.

**Gate 10 (Pareto) — "20% of features deliver 80% of value" is Arbitrary:**
- Files: `skill/SKILL.md` (lines 209-223)
- Why fragile: The 80/20 rule is a heuristic, not a law. Some products have uniform value distribution. The gate forces auditors to identify "THE ONE feature" which may not exist or may be artificial.
- Safe modification: Clarify that the gate is checking "does one dominant feature exist?" not requiring a specific math ratio. Allow answers like "No, all 3 features are equally important" as a valid finding (which then fails the gate).
- Risk: Current wording misleads auditors into manufacturing a dominant feature that doesn't exist.

**Gate 11 (Regret Minimization) — Highly Personal, Non-Reproducible:**
- Files: `skill/SKILL.md` (lines 227-241)
- Why fragile: This gate is purely psychological and will vary dramatically by auditor age, career stage, financial situation, and risk tolerance. Jeff Bezos's 80-year-old self might have different regrets than a 25-year-old starting their first project.
- Safe modification: Acknowledge the subjectivity and add a "context" field to the audit report documenting the auditor's situation (years of experience, project scope, personal motivation). This allows readers to calibrate the score.
- Test coverage gap: No way to validate this gate — no two auditors will score identically.

**Template Rendering — No Markdown Validation:**
- Files: `skill/templates/audit-report.md`
- Why fragile: The template contains raw markdown with template variables mixed in. If a variable contains markdown syntax (e.g., user writes `**bold here**` in a verdict), it could break formatting or create unintended emphasis.
- Safe modification: Escape or sanitize variable content before insertion, or enforce plain text for certain fields (verdicts, fixes).

## Scaling Limits

**No Support for Project Type Variants:**
- Current capacity: The 11 gates are generic and apply equally to all project types (CLI tool, library, SaaS, open-source skill, etc.)
- Limit: Some gates are inapplicable to certain projects. Gate 5 (Wallet) doesn't make sense for free, open-source utilities. Gate 7 (Friction) is less relevant for internal tools.
- Scaling path: Create project-type profiles that skip irrelevant gates or adjust pass criteria. For example: open-source projects might focus heavily on discovery/documentation (Gate 7), while SaaS products focus on retention/second-order (Gate 8).

**No Multi-User Audit Mode:**
- Current capacity: Skill assumes a single auditor fills the report.
- Limit: For high-stakes decisions, teams want to audit together, averaging disagreement on borderline gates.
- Scaling path: Support collecting multiple auditor responses and aggregating: "Auditor A passed Gate 5, Auditor B failed. Consensus: CONDITIONAL PASS."

**Report Feedback Loop Missing:**
- Current capacity: Audit creates a report, but there's no mechanism for users to dispute gates, ask for re-evaluation, or track improvements over time.
- Limit: Projects improve after an audit fails. There's no way to re-audit and compare results systematically.
- Scaling path: Store audit results with version control. When a project re-audits (e.g., 3 months later), show before/after and track progress.

## Missing Critical Features

**No Automation for Data Collection:**
- Problem: Each gate requires manual human judgment. For Gate 1 (5 Whys), the auditor must read the project and interview the author (implied). There's no tool to suggest answers.
- Blocks: A partially-automated audit (script reads README, extracts key info, summarizes for faster human review) could reduce friction.
- Recommendation: Add an optional "auto-analyze" mode that: reads README for value prop, extracts from package.json for core feature, counts GitHub stars for inversion test, summarizes GitHub issues for friction points.

**No Comparison to Competing Projects:**
- Problem: Gate 2 (Inversion Test) asks "what would they use instead?" but there's no database of competing projects to reference or learn from.
- Blocks: Auditors must research alternatives manually, which is tedious and incomplete.
- Recommendation: Create a living database of projects by category (presentation tools, data validators, etc.) with their scores. New audits can reference these automatically.

**No Score Interpretation Guidelines:**
- Problem: The scoring (11/11 = ship, 9-10 = almost, 6-8 = promising, 0-5 = reconsider) is coarse. A project with 7/11 gets "promising" — but what specific fixes prioritize getting to 9?
- Blocks: Projects know they failed but not which gates to tackle first.
- Recommendation: Add a "priority matrix" showing which gates are most common failure points and highest-impact to fix (e.g., "80% of projects that fail Gate 3 also fail Gate 7 — fix discovery/onboarding first").

**No Integration with Fix Execution:**
- Problem: The skill generates priority fixes (e.g., "Publish to GitHub", "Add demo GIF") but doesn't link to skills or tools to execute them.
- Blocks: Users get a to-do list but must find separate tools to implement.
- Recommendation: Link to complementary skills like `/prep-repo` or provide templates/scripts for common fixes (add GitHub action, create demo GIF with ffmpeg, etc.).

## Security Considerations

**No Privacy Guardrails for Wallet Test:**
- Risk: Gate 5 requires listing "3 specific people who would pay." If the audit report is shared publicly (e.g., posted to show validation), it exposes personal names and suggests they'd use your product.
- Files: `skill/templates/audit-report.md` (lines 88-91), `skill/SKILL.md` (lines 113-124)
- Current mitigation: None documented. The template publicly lists names.
- Recommendations: Add a note in the skill that auditors should anonymize names in public reports or use roles instead ("colleague in finance", "SaaS founder"). Suggest a "private report" mode that suppresses names/specifics.

**No Version Control for Audit History:**
- Risk: Projects audit multiple times and improve. But there's no mechanism to prevent re-auditing the same project with a different auditor and getting a wildly different score, then cherry-picking the higher one.
- Files: `skill/templates/audit-report.md` (no version/date field, though template has `{{audit_date}}`)
- Current mitigation: The template includes `{{audit_date}}` but no version ID or auditor signature. Reports are not cryptographically signed.
- Recommendations: Add auditor name, signature, and timestamp. Consider warning users if they're re-auditing a project audited recently (to prevent score-shopping).

## Performance Bottlenecks

**Manual Gate Navigation is Slow:**
- Problem: The 11 gates are sequential and comprehensive. For a typical audit, a human might spend 1-2 hours walking through each gate, researching, and filling the template.
- Files: `skill/SKILL.md` (all 11 gates, ~280 lines)
- Cause: No shortcuts. Gate 3 (10-Minute Test) requires actually trying the project; Gate 5 (Wallet) requires hypothetically asking people.
- Improvement path: Create a "quick audit" mode (5 gates, 15 minutes) for rapid triage, then full audit only for projects that seem promising. This is a product feature, not a code performance issue.

**No Caching of Project Metadata:**
- Problem: If the same project audits twice, the second auditor re-reads the README, package.json, and re-analyzes the GitHub repo.
- Files: Not present in codebase (would be needed in implementation)
- Cause: The skill has no persistence layer.
- Improvement path: Cache project metadata (README text, feature list, author info) with timestamps so repeated audits reuse data if the project hasn't changed.

## Test Coverage Gaps

**No Examples of Failed Audits:**
- What's not tested: The template and skill documentation only show happy-path examples (examples of good answers to gates). There are no examples of failed audits or borderline cases.
- Files: `README.md` (lines 74-98 show a hypothetical output but it's a 7/11 passing example, not a detailed failure case)
- Risk: Auditors won't know what a clear FAIL looks like for each gate, leading to inflated scores.
- Recommendation: Add a `EXAMPLES.md` with 2-3 full audit reports (one passing, one failing, one borderline) showing how evidence leads to scores.

**No Validation that Gates are Mutually Consistent:**
- What's not tested: Can a project pass Gate 1 (clear human need) but fail Gate 5 (no one would pay)? This seems contradictory.
- Files: Gates are defined independently; no cross-gate validation rules.
- Risk: Auditors might score gates that logically conflict with each other.
- Recommendation: Document gate interdependencies. For example: "Gate 1 (bedrock human need) is often correlated with Gate 5 (wallet test). If Gate 1 passes and Gate 5 fails, re-examine whether the need translates to user demand."

**No Regression Testing for Score Consistency:**
- What's not tested: If the same project were audited by 5 independent auditors, would they get the same score?
- Files: N/A
- Risk: Without this test, we don't know if the gates are reliable or just subjective.
- Recommendation: Run a pilot audit on 5 real projects with 3 auditors each. Calculate inter-rater reliability. If below 0.7 (Cronbach's alpha), the gates need refinement.

---

*Concerns audit: 2026-01-22*
