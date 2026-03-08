---
rfu_audit:
  version: "1.0"
  project_name: "{{project_name}}"
  project_path: "{{project_path}}"
  audit_date: "{{audit_date}}"
  audit_mode: "full"
  score: {{score}}
  max_score: 11
  gate_results:
    gate1: "{{gate1_result}}"
    gate2: "{{gate2_result}}"
    gate3: "{{gate3_result}}"
    gate4: "{{gate4_result}}"
    gate5: "{{gate5_result}}"
    gate6: "{{gate6_result}}"
    gate7: "{{gate7_result}}"
    gate8: "{{gate8_result}}"
    gate9: "{{gate9_result}}"
    gate10: "{{gate10_result}}"
    gate11: "{{gate11_result}}"
---

# RFU Audit Report

**Project:** {{project_name}}
**Path:** {{project_path}}
**Date:** {{audit_date}}

---

## Executive Summary

**Score: {{score}}/11{{#if has_previous}} {{score_delta}} (was {{previous_score}}/11){{/if}}** — {{rating}}

{{summary}}

---

## Gate Results

### Gate 1: The 5 Whys of Utility {{gate1_status}}

**Chain:**
```
Why does this exist?
→ {{why1}}

Why do people need that?
→ {{why2}}

Why can't they use existing tools?
→ {{why3}}

Why does that matter?
→ {{why4}}

Why is that important?
→ {{why5}} [BEDROCK: {{bedrock_need}}]
```

**Verdict:** {{gate1_verdict}}

---

### Gate 2: Inversion Test (Via Negativa) {{gate2_status}}

**If this didn't exist:**
- Would anyone notice? {{inversion_notice}}
- What would they use instead? {{inversion_alternative}}
- Consequence severity: {{inversion_severity}}

**Verdict:** {{gate2_verdict}}

---

### Gate 3: 10-Minute Test {{gate3_status}}

| Step | Time | Notes |
|------|------|-------|
| Discovery | {{time_discovery}} | {{notes_discovery}} |
| Comprehension | {{time_comprehension}} | {{notes_comprehension}} |
| Installation | {{time_install}} | {{notes_install}} |
| First Use | {{time_first_use}} | {{notes_first_use}} |
| Aha Moment | {{time_aha}} | {{notes_aha}} |
| **Total** | **{{time_total}}** | |

**Verdict:** {{gate3_verdict}}

---

### Gate 4: Bartender Test {{gate4_status}}

**One-sentence explanation:**
> {{bartender_sentence}}

**Comprehension check:**
- Uses jargon? {{bartender_jargon}}
- Describes value (not implementation)? {{bartender_value}}
- Immediate understanding? {{bartender_immediate}}

**Verdict:** {{gate4_verdict}}

---

### Gate 5: Wallet Test {{gate5_status}}

**Would YOU pay $20/month?**
{{wallet_self}} — {{wallet_self_reason}}

**3 specific people who would pay:**
1. {{wallet_person1}}
2. {{wallet_person2}}
3. {{wallet_person3}}

**Verdict:** {{gate5_verdict}}

---

### Gate 6: Job-to-be-Done {{gate6_status}}

**JTBD Statement:**
> When {{jtbd_situation}}, I want to {{jtbd_motivation}}, so I can {{jtbd_outcome}}.

**Quality check:**
- Specific situation? {{jtbd_specific}}
- Clear motivation? {{jtbd_motivation_clear}}
- Tangible outcome? {{jtbd_outcome_clear}}

**Verdict:** {{gate6_verdict}}

---

### Gate 7: Friction Audit {{gate7_status}}

| Step | Friction Level | Issue |
|------|---------------|-------|
| Discovery | {{friction_discovery}} | {{friction_discovery_issue}} |
| Comprehension | {{friction_comprehension}} | {{friction_comprehension_issue}} |
| Installation | {{friction_install}} | {{friction_install_issue}} |
| First Use | {{friction_first}} | {{friction_first_issue}} |
| Repeated Use | {{friction_repeated}} | {{friction_repeated_issue}} |
| Mastery | {{friction_mastery}} | {{friction_mastery_issue}} |

**Highest friction point:** {{friction_highest}}

**Verdict:** {{gate7_verdict}}

---

### Gate 8: Second-Order Consequences {{gate8_status}}

**Positive:**
- Viral potential: {{second_viral}}
- Ecosystem: {{second_ecosystem}}
- Retention: {{second_retention}}

**Negative:**
- Lock-in: {{second_lockin}}
- Maintenance burden: {{second_maintenance}}
- Dependencies: {{second_dependencies}}

**Net:** {{second_net}}

**Verdict:** {{gate8_verdict}}

---

### Gate 9: Occam's Razor {{gate9_status}}

**Simplicity check:**
- Could fewer features work? {{occam_fewer}}
- Simpler tool exists? {{occam_simpler}}
- Over-engineered? {{occam_overengineered}}
- Essential complexity only? {{occam_essential}}

**Verdict:** {{gate9_verdict}}

---

### Gate 10: Pareto Gate {{gate10_status}}

**The ONE feature that matters most:**
{{pareto_one_feature}}

**Features that could be cut:**
{{pareto_cuttable}}

**Value concentration:**
{{pareto_concentration}}

**Verdict:** {{gate10_verdict}}

---

### Gate 11: Regret Minimization {{gate11_status}}

**In 10 years, would you regret NOT shipping this?**
{{regret_answer}}

**Reasoning:**
{{regret_reasoning}}

**Verdict:** {{gate11_verdict}}

---

## Scorecard

| Gate | Name | Status | {{#if has_previous}}Change |{{/if}}
|------|------|--------|{{#if has_previous}}--------|{{/if}}
| 1 | 5 Whys | {{gate1_status}} | {{#if has_previous}}{{gate1_change}} |{{/if}}
| 2 | Inversion | {{gate2_status}} | {{#if has_previous}}{{gate2_change}} |{{/if}}
| 3 | 10-Minute | {{gate3_status}} | {{#if has_previous}}{{gate3_change}} |{{/if}}
| 4 | Bartender | {{gate4_status}} | {{#if has_previous}}{{gate4_change}} |{{/if}}
| 5 | Wallet | {{gate5_status}} | {{#if has_previous}}{{gate5_change}} |{{/if}}
| 6 | JTBD | {{gate6_status}} | {{#if has_previous}}{{gate6_change}} |{{/if}}
| 7 | Friction | {{gate7_status}} | {{#if has_previous}}{{gate7_change}} |{{/if}}
| 8 | Second-Order | {{gate8_status}} | {{#if has_previous}}{{gate8_change}} |{{/if}}
| 9 | Occam's Razor | {{gate9_status}} | {{#if has_previous}}{{gate9_change}} |{{/if}}
| 10 | Pareto | {{gate10_status}} | {{#if has_previous}}{{gate10_change}} |{{/if}}
| 11 | Regret | {{gate11_status}} | {{#if has_previous}}{{gate11_change}} |{{/if}}

**Total: {{score}}/11{{#if has_previous}} {{score_delta}}{{/if}}**

---

## Priority Matrix

{{#if has_failures}}
### Top Priority Fixes

{{#each top_priority_fixes}}
{{index}}. **Gate {{gate_number}}: {{gate_name}}** — Effort: {{effort_level}}

   **Issue:** {{issue_summary}}

   **Why fix this first:** {{impact_rationale}}

   **Fix:** {{fix_action}}

   **Resources:** {{resources}}

   {{#if unlocks}}
   Unlocks: Fixing this helps with {{unlocks}}
   {{/if}}
   {{#if blocked_by}}
   Blocked by: Must fix {{blocked_by}} first
   {{/if}}

{{/each}}

{{#if other_failed_gates}}
### Other Failed Gates

{{#each other_failed_gates}}
- **Gate {{gate_number}}: {{gate_name}}** — Effort: {{effort_level}} — {{issue_summary}}
{{/each}}
{{/if}}

{{#if borderline_gates}}
### Recommended (Borderline)

{{#each borderline_gates}}
- **Gate {{gate_number}}: {{gate_name}}** — {{borderline_reason}}
{{/each}}
{{/if}}

{{else}}
All gates passed! Project is RFU Certified.

{{#if borderline_gates}}
### Recommended Optimizations

While all gates passed, these areas could be strengthened:

{{#each borderline_gates}}
- **Gate {{gate_number}}: {{gate_name}}** — {{borderline_reason}}
{{/each}}
{{/if}}
{{/if}}

<!-- Priority Matrix is dynamically generated following guides/PRIORITY-MATRIX.md -->
<!-- Uses impact-effort ranking, gate-specific effort defaults, and resource linking -->

---

## Next Steps

{{next_steps}}

---

*Generated by `/rfu-audit` — [RFU Framework](https://github.com/aplaceforallmystuff/claude-rfu-audit)*
*Saved to: `.planning/audits/{{project_name_normalized}}/full/`*
<!-- History tracking: see guides/AUDIT-HISTORY.md -->
