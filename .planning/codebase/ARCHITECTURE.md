# Architecture

**Analysis Date:** 2026-01-22

## Pattern Overview

**Overall:** Static Skill Distribution Package

This is a declarative Claude Code skill distribution package. It contains no executable code—only documentation, metadata, and templates. The architecture is purely declarative: the `/rfu-audit` skill is invoked directly by Claude Code, which reads the skill definition and uses Claude's reasoning engine to execute the framework logic.

**Key Characteristics:**
- Single skill definition with structured documentation
- Template-driven output generation
- Conceptual/procedural framework (not algorithmic code)
- Integration with existing wisdom skills and Claude Code ecosystem
- Designed for skill invocation via `/rfu-audit` command

## Layers

**Skill Definition Layer:**
- Purpose: Declare the skill's metadata, parameters, and execution strategy
- Location: `skill/SKILL.md`
- Contains: Skill manifest (name, description, user-invocable flag), full gate documentation, scoring rules, process workflow
- Depends on: Claude Code skill system
- Used by: Claude Code runtime when `/rfu-audit` is invoked

**Documentation Layer:**
- Purpose: Provide reference material explaining the framework and gates
- Location: `README.md` (project-level), `skill/SKILL.md` (skill-level)
- Contains: Philosophy, installation instructions, gate details, usage examples, scoring guidance
- Depends on: None (reference material only)
- Used by: Users and developers reviewing the framework

**Template Layer:**
- Purpose: Provide structured output format for audit reports
- Location: `skill/templates/audit-report.md`
- Contains: Markdown template with variable placeholders for gate results, scoring, recommendations
- Depends on: None (static template)
- Used by: Claude when generating final audit reports

**Installation Layer:**
- Purpose: Enable distribution and setup of the skill
- Location: `install.sh`
- Contains: Bash script that copies skill to `~/.claude/skills/rfu-audit`
- Depends on: Bash, file system access
- Used by: End users during initial installation

## Data Flow

**Execution Flow:**

1. **User invokes skill:**
   ```
   /rfu-audit [project-path]
   ```

2. **Claude Code loads skill definition** from `skill/SKILL.md`

3. **Claude reads project artifacts** (README, package.json, source files from provided path)

4. **Claude walks through 11 gates sequentially**, applying framework logic:
   - Gate 1 (5 Whys): Reads README/project description → asks why chain questions → identifies bedrock need
   - Gate 2 (Inversion): Questions negative consequences → evaluates severity
   - Gate 3 (10-Minute): Estimates discovery, comprehension, install, first-use, aha-moment timing
   - Gate 4 (Bartender): Reviews for one-sentence clarity
   - Gate 5 (Wallet): Checks personal conviction + identifies 3 specific people
   - Gate 6 (JTBD): Constructs situation→motivation→outcome statement
   - Gate 7 (Friction): Maps 6-step user journey → identifies bottlenecks
   - Gate 8 (Second-Order): Weighs positive vs negative consequences
   - Gate 9 (Occam's Razor): Evaluates solution simplicity
   - Gate 10 (Pareto): Identifies core 20% feature value
   - Gate 11 (Regret): Applies Bezos framework

5. **Claude scores each gate** as Pass (1) or Fail (0)

6. **Claude generates audit report** using template at `skill/templates/audit-report.md`:
   - Fills placeholders with gate analysis
   - Calculates total score (0-11)
   - Generates prioritized fix recommendations
   - Outputs structured markdown report

7. **Report returned to user**

**State Management:**
- No persistent state. Each audit is independent.
- All inputs come from project files Claude reads; all outputs are the generated report.

## Key Abstractions

**The RFU Framework:**
- Purpose: A validation gauntlet that forces discipline in proving product utility before investment
- Pattern: Sequential gate-passing model (pass gate = move forward, fail gate = note for fixing)
- Philosophy: Applies proven mental models (5 Whys, via negativa, JTBD, Pareto, regret minimization)
- Integration points: References `/wisdom-clarify`, `/wisdom-stoic-premeditation`, taches-cc-resources skills for conceptual alignment

**The Gate System:**
- 11 independent evaluation criteria, each with clear pass/fail criteria
- Each gate has:
  - A core question
  - A measurement approach
  - Pass criteria
  - Fail indicators
  - Output template variables for report

**The Scoring System:**
- Binary per gate (pass=1, fail=0)
- Total score determines action tier:
  - 11/11 = RFU Certified (ship it)
  - 9-10 = Almost There (fix gaps, ship)
  - 6-8 = Promising (needs work)
  - 0-5 = Reconsider (build something else)

## Entry Points

**Primary Entry Point: `/rfu-audit` command**
- Location: Invoked via Claude Code CLI
- Triggers: User runs `/rfu-audit [optional-path]`
- Responsibilities:
  - Accept optional project path argument (defaults to current directory)
  - Read project files (README, package.json, source)
  - Load skill definition from `skill/SKILL.md`
  - Execute 11-gate analysis
  - Generate and output audit report

**Secondary Entry Point: Installation**
- Location: `install.sh`
- Triggers: `bash install.sh` in project root
- Responsibilities:
  - Copy skill directory to `~/.claude/skills/rfu-audit`
  - Verify successful installation
  - Display usage instructions

**Documentation Entry Point: Framework Reference**
- Location: `README.md`
- Triggers: User reads repository
- Responsibilities:
  - Explain the 11-gate framework
  - Provide philosophy and rationale
  - Show example output
  - Include installation/usage instructions

## Error Handling

**Strategy:** Skill definition drives validation; Claude applies reasoning to incomplete data.

**Patterns:**
- Missing project files: Claude infers from available artifacts or notes gaps in audit
- Unclear project value: Gate returns Fail with explanation of evidence gap
- User uncertainty on specific gate: Claude uses contextual clues and asks clarifying questions within the gate framework
- Installation failures: `install.sh` exits with error message, no recovery attempted

## Cross-Cutting Concerns

**Documentation:** All gates include detailed explanation in `skill/SKILL.md` (lines 28-241) with pass/fail criteria, examples, and indicators. Template variables at `skill/templates/audit-report.md` standardize output.

**Validation:** No input validation needed (reads external project files). Gate pass/fail logic lives in Claude's reasoning, guided by skill definition.

**Integration:** References 5 existing wisdom skills for conceptual alignment:
  - `/wisdom-clarify` - 5 Whys questioning method
  - `/wisdom-stoic-premeditation` - Via negativa technique
  - `/taches-cc-resources:consider:second-order` - Second-order consequence analysis
  - `/taches-cc-resources:consider:pareto` - 80/20 principle
  - `/taches-cc-resources:consider:occams-razor` - Simplicity principle

These are contextual references, not functional dependencies—they inform how Claude applies gate logic.

---

*Architecture analysis: 2026-01-22*
