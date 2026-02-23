---
name: 3pao-dryrun
description: "Simulate a full 3PAO assessment for a specific control. Walks through every assessment objective, checks evidence sufficiency, generates interview questions, and produces SAR-style findings."
arguments: "<control-id> — The control to assess (e.g., AC-02, IA-05, SC-07)"
---

# 3PAO Dry Run Command

## Purpose

Simulate what a Third-Party Assessment Organization (3PAO) would find when assessing a control. Be adversarial but fair — the value is finding gaps before the real assessor does.

## Process

1. Read the assessment objectives from the Snapshot Matrix `4_Test Case Procedures` sheet
2. Read ALL evidence in the control folder + relevant SOC 2 cross-references
3. Read the SSP narrative if one exists in `./output/narratives/`
4. Read the relevant policy template from `sp-package/3_Policies/`
5. Read the relevant procedure template from `sp-package/4_Procedures/`
6. For each assessment objective:
   - State the objective
   - Identify the assessment method (Examine/Interview/Test)
   - Review evidence against the objective
   - Determine result: SATISFIED / OTHER THAN SATISFIED
   - If OTS, assign risk rating and write finding
7. Check evidence for: Relevance, Currency (<1 year), Completeness, Specificity, Consistency, Parameter alignment
8. Generate interview questions a 3PAO would ask
9. Generate POA&M entries for any findings

## Output Format

```markdown
## 3PAO Assessment: <Control-ID> — <Control Name>

**Assessment Result**: SATISFIED / OTHER THAN SATISFIED
**Risk Rating**: High / Moderate / Low (if OTS)
**Methods Used**: Examine, Interview, Test

### Assessment Objectives

#### <Objective ID>: <Objective Text>
- **Method**: Examine
- **Evidence Reviewed**: `<path>`
- **Result**: OTHER THAN SATISFIED
- **Finding**: [description]
- **Risk**: Low
- **Recommendation**: [fix]

### Evidence Inventory
| Artifact | Path | Covers | Current? | Sufficient? |
|----------|------|--------|----------|-------------|

### Interview Questions
1. [question for system admin]
2. [question for ISSO]

### Interview Readiness Checklist
- [ ] [item]

### POA&M Entries (if OTS)
[generated entries]
```

Write output to `./output/3pao-dryrun/<control-id>.md`
