# Template Variable Specification

This document specifies all variables used in the RFU audit report templates.

**Templates covered:**
- `templates/audit-report.md` (full audit, 11 gates)
- `templates/audit-report-quick.md` (quick audit, 5 gates)

**Variable sources:**
- Report metadata (project_name, audit_date, etc.)
- Gate-specific variables defined in `config/gates-full.md`
- Computed values (score, rating, triage_recommendation)
- Dynamic sections (Priority Matrix, Next Steps) generated per audit results

---

## Report Metadata Variables

| Variable | Type | Description | Example |
|----------|------|-------------|---------|
| `project_name` | string | Name of the audited project | "my-cli-tool" |
| `project_path` | string | Absolute path to project | "/Users/jim/Dev/my-cli-tool" |
| `audit_date` | string | Date of audit (ISO format) | "2026-01-28" |
| `score` | integer | Total passing gates (0-11) | 8 |
| `rating` | string | Score band label | "Almost There" |
| `summary` | string | Executive summary paragraph | "Project passes 8 of 11 gates..." |

---

## Quick Mode Specific Variables

These variables are used only in `templates/audit-report-quick.md`.

| Variable | Type | Description | Example |
|----------|------|-------------|---------|
| `quick_score` | integer | Quick audit score (0-5) | 4 |
| `triage_recommendation` | string | Triage level | "PROCEED" / "PROMISING" / "BORDERLINE" / "STOP" |
| `recommendation_text` | string | Triage guidance paragraph | "Score 4/5: One gate needs attention before full audit..." |

---

## Full Report Specific Variables

These variables are used only in `templates/audit-report.md`.

| Variable | Type | Description | Example |
|----------|------|-------------|---------|
| `critical_fixes` | string | High-priority fixes (markdown list) | "- Fix Gate 1: Clarify bedrock need..." |
| `important_fixes` | string | Medium-priority fixes (markdown list) | "- Gate 7: Reduce installation friction..." |
| `nice_fixes` | string | Low-priority fixes (markdown list) | "- Gate 10: Document core feature..." |
| `next_steps` | string | Action plan based on audit results | "1. Address critical fixes first..." |

---

## Gate Variables

### Gate 1: 5 Whys of Utility (8 variables)

| Variable | Type | Format | Description |
|----------|------|--------|-------------|
| `gate1_status` | emoji | Pass: check, Fail: x | Gate pass/fail indicator |
| `why1` | string | Free text | Answer to first why |
| `why2` | string | Free text | Answer to second why |
| `why3` | string | Free text | Answer to third why |
| `why4` | string | Free text | Answer to fourth why |
| `why5` | string | Free text | Answer to fifth why |
| `bedrock_need` | string | One of: Autonomy, Time, Money, Status, Connection, Safety | Identified bedrock need |
| `gate1_verdict` | string | Free text | Explanation of pass/fail |

**Used in:** Both templates (full shows chain, quick shows only bedrock_need + verdict)

---

### Gate 2: Inversion Test (5 variables)

| Variable | Type | Format | Description |
|----------|------|--------|-------------|
| `gate2_status` | emoji | Pass: check, Fail: x | Gate pass/fail indicator |
| `inversion_notice` | string | "Yes" / "No" + explanation | Would anyone notice if it was gone? |
| `inversion_alternative` | string | Free text | What they would use instead |
| `inversion_severity` | string | "Low" / "Medium" / "High" + explanation | Consequence severity |
| `gate2_verdict` | string | Free text | Explanation of pass/fail |

**Used in:** Full template only (not in quick mode critical path)

---

### Gate 3: 10-Minute Test (13 variables)

| Variable | Type | Format | Description |
|----------|------|--------|-------------|
| `gate3_status` | emoji | Pass: check, Fail: x | Gate pass/fail indicator |
| `time_discovery` | string | Duration (e.g., "30 sec") | Time to discover project |
| `notes_discovery` | string | Free text | Discovery phase notes |
| `time_comprehension` | string | Duration (e.g., "1 min") | Time to understand value |
| `notes_comprehension` | string | Free text | Comprehension phase notes |
| `time_install` | string | Duration (e.g., "2 min") | Time to install/setup |
| `notes_install` | string | Free text | Installation phase notes |
| `time_first_use` | string | Duration (e.g., "1 min") | Time to first use |
| `notes_first_use` | string | Free text | First use phase notes |
| `time_aha` | string | Duration (e.g., "2 min") | Time to aha moment |
| `notes_aha` | string | Free text | Aha moment notes |
| `time_total` | string | Duration (e.g., "8 min 30 sec") | Total time across all phases |
| `gate3_verdict` | string | Free text | Explanation of pass/fail |

**Used in:** Both templates (full shows table, quick shows only time_total + verdict)

---

### Gate 4: Bartender Test (6 variables)

| Variable | Type | Format | Description |
|----------|------|--------|-------------|
| `gate4_status` | emoji | Pass: check, Fail: x | Gate pass/fail indicator |
| `bartender_sentence` | string | Single sentence | One-sentence explanation of project value |
| `bartender_jargon` | string | "Yes" / "No" | Uses technical jargon? |
| `bartender_value` | string | "Yes" / "No" | Describes value (not implementation)? |
| `bartender_immediate` | string | "Yes" / "No" | Immediate understanding without questions? |
| `gate4_verdict` | string | Free text | Explanation of pass/fail |

**Used in:** Both templates (full shows comprehension check, quick shows only sentence + verdict)

---

### Gate 5: Wallet Test (7 variables)

| Variable | Type | Format | Description |
|----------|------|--------|-------------|
| `gate5_status` | emoji | Pass: check, Fail: x | Gate pass/fail indicator |
| `wallet_self` | string | "Yes" / "No" | Would YOU pay $20/month? |
| `wallet_self_reason` | string | Free text | Reasoning for self-payment answer |
| `wallet_person1` | string | Real name + context | First person who would pay |
| `wallet_person2` | string | Real name + context | Second person who would pay |
| `wallet_person3` | string | Real name + context | Third person who would pay |
| `gate5_verdict` | string | Free text | Explanation of pass/fail |

**Used in:** Both templates (both show wallet_self and 3 people list)

---

### Gate 6: Job-to-be-Done (8 variables)

| Variable | Type | Format | Description |
|----------|------|--------|-------------|
| `gate6_status` | emoji | Pass: check, Fail: x | Gate pass/fail indicator |
| `jtbd_situation` | string | Free text | The situation/context ("When...") |
| `jtbd_motivation` | string | Free text | What they want to do ("I want to...") |
| `jtbd_outcome` | string | Free text | What they want to achieve ("so I can...") |
| `jtbd_specific` | string | "Yes" / "No" | Is situation specific? |
| `jtbd_motivation_clear` | string | "Yes" / "No" | Is motivation clear? |
| `jtbd_outcome_clear` | string | "Yes" / "No" | Is outcome tangible? |
| `gate6_verdict` | string | Free text | Explanation of pass/fail |

**Used in:** Full template only (not in quick mode critical path)

---

### Gate 7: Friction Audit (14 variables)

| Variable | Type | Format | Description |
|----------|------|--------|-------------|
| `gate7_status` | emoji | Pass: check, Fail: x | Gate pass/fail indicator |
| `friction_discovery` | string | "Low" / "Medium" / "High" | Discovery friction level |
| `friction_discovery_issue` | string | Free text | Specific discovery issue (if any) |
| `friction_comprehension` | string | "Low" / "Medium" / "High" | Comprehension friction level |
| `friction_comprehension_issue` | string | Free text | Specific comprehension issue (if any) |
| `friction_install` | string | "Low" / "Medium" / "High" | Installation friction level |
| `friction_install_issue` | string | Free text | Specific installation issue (if any) |
| `friction_first` | string | "Low" / "Medium" / "High" | First use friction level |
| `friction_first_issue` | string | Free text | Specific first use issue (if any) |
| `friction_repeated` | string | "Low" / "Medium" / "High" | Repeated use friction level |
| `friction_repeated_issue` | string | Free text | Specific repeated use issue (if any) |
| `friction_mastery` | string | "Low" / "Medium" / "High" | Mastery friction level |
| `friction_mastery_issue` | string | Free text | Specific mastery issue (if any) |
| `friction_highest` | string | Free text | Highest friction point identified |
| `gate7_verdict` | string | Free text | Explanation of pass/fail |

**Used in:** Full template only (not in quick mode critical path)

---

### Gate 8: Second-Order Consequences (9 variables)

| Variable | Type | Format | Description |
|----------|------|--------|-------------|
| `gate8_status` | emoji | Pass: check, Fail: x | Gate pass/fail indicator |
| `second_viral` | string | Free text | Viral potential assessment |
| `second_ecosystem` | string | Free text | Ecosystem potential assessment |
| `second_retention` | string | Free text | Retention potential assessment |
| `second_lockin` | string | Free text | Lock-in concerns assessment |
| `second_maintenance` | string | Free text | Maintenance burden assessment |
| `second_dependencies` | string | Free text | Dependency concerns assessment |
| `second_net` | string | "Positive" / "Negative" / "Neutral" | Net second-order assessment |
| `gate8_verdict` | string | Free text | Explanation of pass/fail |

**Used in:** Full template only (not in quick mode critical path)

---

### Gate 9: Occam's Razor (6 variables)

| Variable | Type | Format | Description |
|----------|------|--------|-------------|
| `gate9_status` | emoji | Pass: check, Fail: x | Gate pass/fail indicator |
| `occam_fewer` | string | "Yes" / "No" + explanation | Could fewer features work? |
| `occam_simpler` | string | "Yes" / "No" + explanation | Does a simpler tool exist? |
| `occam_overengineered` | string | "Yes" / "No" + explanation | Is it over-engineered? |
| `occam_essential` | string | "Yes" / "No" + explanation | Is it essential complexity only? |
| `gate9_verdict` | string | Free text | Explanation of pass/fail |

**Used in:** Both templates (full shows all 4 questions, quick shows only occam_simpler + verdict)

---

### Gate 10: Pareto Gate (5 variables)

| Variable | Type | Format | Description |
|----------|------|--------|-------------|
| `gate10_status` | emoji | Pass: check, Fail: x | Gate pass/fail indicator |
| `pareto_one_feature` | string | Free text | The ONE feature that matters most |
| `pareto_cuttable` | string | Free text | Features that could be cut |
| `pareto_concentration` | string | Free text | Value concentration assessment |
| `gate10_verdict` | string | Free text | Explanation of pass/fail |

**Used in:** Full template only (not in quick mode critical path)

---

### Gate 11: Regret Minimization (4 variables)

| Variable | Type | Format | Description |
|----------|------|--------|-------------|
| `gate11_status` | emoji | Pass: check, Fail: x | Gate pass/fail indicator |
| `regret_answer` | string | "Yes" / "No" | Would you regret NOT shipping? |
| `regret_reasoning` | string | Free text | Reasoning for the answer |
| `gate11_verdict` | string | Free text | Explanation of pass/fail |

**Used in:** Full template only (not in quick mode critical path)

---

## Dynamic Sections

The following sections are generated dynamically based on audit results rather than using static template variables.

### Priority Matrix

The Priority Matrix section replaces the static "Priority Fixes" section. It is generated dynamically based on failed gates.

**Structure:**
1. **Top Priority Fixes** (up to 3 failed gates, shown in detail)
   - Gate name and failure reason
   - Effort estimate (Quick / Medium / Involved)
   - Specific fix guidance
   - Relevant resources (skills, tools, templates)
   - Dependencies ("Fix Gate X first" / "Unlocks Gate Y")

2. **Other Failed Gates** (remaining failures, compact list)
   - Gate name and brief issue
   - Effort level

3. **Recommendations** (borderline/near-failing gates)
   - Gates that passed but are close to failing
   - Preventive improvements

**Generation process:**
1. Collect all failed gates from audit
2. Rank by impact (based on gate position in critical path and severity)
3. Consider dependencies between gates (e.g., Gate 1 before Gate 5)
4. Assign effort levels (gate defaults with audit-specific overrides)
5. Link relevant resources inline
6. Format according to audit mode (full vs quick)

**Gate effort defaults:**

| Gate | Default Effort | Typical Fix |
|------|----------------|-------------|
| Gate 1 | Medium | Revisit value proposition, drill deeper on whys |
| Gate 2 | Quick | Identify and document switching costs |
| Gate 3 | Involved | Improve onboarding, reduce setup steps |
| Gate 4 | Quick | Rewrite one-sentence explanation |
| Gate 5 | Medium | Validate with real potential customers |
| Gate 6 | Quick | Refine JTBD statement |
| Gate 7 | Involved | Reduce friction across user journey |
| Gate 8 | Medium | Mitigate negative second-order effects |
| Gate 9 | Medium | Simplify or justify complexity |
| Gate 10 | Quick | Identify and focus on core feature |
| Gate 11 | Medium | Build conviction or reconsider project |

### Next Steps

The Next Steps section is generated based on score and audit mode.

**Full audit next steps (based on score):**
- 11/11: "Congratulations! Project is RFU Certified. Consider sharing your journey."
- 9-10/11: "Almost there. Address the few remaining gaps and re-audit."
- 6-8/11: "Good foundation. Focus on critical fixes in priority order."
- 3-5/11: "Significant work needed. Start with top priority fixes."
- 0-2/11: "Fundamental issues. Reconsider problem definition before proceeding."

**Quick audit next steps (based on triage level):**

| Score | Triage Level | Next Steps |
|-------|--------------|------------|
| 5/5 | PROCEED | "All critical gates pass. Proceed to full audit." |
| 4/5 | PROMISING | "One gate needs attention. Review the failed gate, then decide on full audit." |
| 3/5 | BORDERLINE | "Two gates need work. Fix these before investing in full audit." |
| 0-2/5 | STOP | "Core viability questions remain. Address fundamental issues before proceeding." |

---

## Variable Count Summary

| Section | Variable Count |
|---------|----------------|
| Report Metadata | 6 |
| Quick Mode Specific | 3 |
| Full Report Specific | 4 |
| Gate 1 | 8 |
| Gate 2 | 5 |
| Gate 3 | 13 |
| Gate 4 | 6 |
| Gate 5 | 7 |
| Gate 6 | 8 |
| Gate 7 | 14 |
| Gate 8 | 9 |
| Gate 9 | 6 |
| Gate 10 | 5 |
| Gate 11 | 4 |
| **Total Static Variables** | **98** |

Plus 2 dynamic sections (Priority Matrix, Next Steps) generated per audit.

---

## Template-to-Variable Mapping

### Full Audit Template (`audit-report.md`)

Uses all variables listed above (98 total).

### Quick Audit Template (`audit-report-quick.md`)

Uses subset of variables:

**Metadata:** project_name, project_path, audit_date (3)
**Quick-specific:** quick_score, triage_recommendation, recommendation_text (3)
**Gate 1:** gate1_status, bedrock_need, gate1_verdict (3)
**Gate 3:** gate3_status, time_total, gate3_verdict (3)
**Gate 4:** gate4_status, bartender_sentence, gate4_verdict (3)
**Gate 5:** gate5_status, wallet_self, wallet_person1, wallet_person2, wallet_person3, gate5_verdict (6)
**Gate 9:** gate9_status, occam_simpler, gate9_verdict (3)
**Dynamic:** next_steps (1)

**Quick mode total:** 25 variables + 1 dynamic section

---

## Cross-References

- **Gate definitions:** See `config/gates-full.md` for complete gate specifications including rubrics
- **Quick mode gates:** See `config/gates-quick.md` for 5-gate critical path subset
- **Edge cases:** See `guides/EDGE-CASES.md` for borderline resolution rules
- **Auto-analyze:** See `guides/AUTO-ANALYZE.md` for extraction heuristics that pre-populate variables

---

*Specification version: 1.0*
*Last updated: 2026-01-28*
