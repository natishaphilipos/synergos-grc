---
name: pmo-review
description: "Simulate a GovRAMP PMO quarterly snapshot review for a specific control. Evaluates each sub-part, assigns Pass/Pass with Concerns/Fail verdicts, compares against historical PMO feedback, and scores maturity."
arguments: "<control-id> — The control to review (e.g., AC-02, CM-06, SI-04)"
---

# GovRAMP PMO Review Command

## Purpose

Replicate the GovRAMP PMO quarterly snapshot review process for a control. This simulates what the PMO does every quarter when reviewing submitted evidence.

## Process

1. Look up the control requirements: Read the NIST 800-53 Rev 5 description and GovRAMP parameters from the Snapshot Matrix `3_Snapshot Criteria` sheet (columns H, J, K)
2. Read ALL evidence artifacts in the control's evidence folder
3. Read any relevant SOC 2 Evidence cross-references
4. For each lettered sub-part (a, b, c...):
   - State the requirement with GovRAMP parameters filled in
   - Identify which evidence artifacts address this sub-part
   - Assess sufficiency
   - Assign verdict: Pass / Pass with Concerns / Fail
   - Provide specific feedback on what is missing
5. Compare against historical PMO feedback from the Snapshot Matrix (columns R-Z)
6. Score maturity 0-5 using the rubric from `audits/narrative-quality-criteria.md`

## Output Format

```markdown
## PMO Review: <Control-ID> — <Control Name>

**Overall Verdict**: Pass / Pass with Concerns / Fail
**Maturity Score**: X/5
**Evidence Expiry**: YYYY-MM-DD

### Sub-Part Assessment

#### (a) <requirement summary>
- **Verdict**: Pass with Concerns
- **Evidence**: `<folder>/<file>`
- **Finding**: [what was found]
- **Remediation**: [what to fix]

[repeat for each sub-part]

### Historical PMO Comparison
- **Previous PMO feedback**: [quote from matrix]
- **Delta**: [what changed]

### Recommendations
1. [action items]
```

Write output to `./output/pmo-reviews/<control-id>.md`
