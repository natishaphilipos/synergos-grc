# Synergos Agentic Workflow

## Purpose

This workflow turns Claude Code into four specialized GRC roles that work together to accelerate your path from **Progressing Snapshot** to **GovRAMP Ready** status, and ultimately to a successful **3PAO assessment**:

1. **PMO Reviewer** — Replicates the GovRAMP PMO quarterly review process
2. **3PAO Assessor** — Simulates a third-party assessment organization audit
3. **Narrative Generator** — Produces auditor-ready SSP narratives from existing evidence
4. **ISORA Risk Assessor** — Runs a 216-question evidence-based risk assessment

The goal: by the time a real 3PAO walks in the door, every control has been reviewed, scored, gap-identified, and remediated — with zero surprises.

---

## How to Launch

```bash
claude --plugin-dir "./grc"
```

Once loaded, all `/grc:*` commands are available.

---

## Directory Structure

```
synergos-grc/
├── evidence/                          # Control-family evidence folders (READ ONLY)
├── soc2-evidence/                     # SOC 2 audit artifacts
├── govramp-process/                   # Snapshot matrices, score letters
├── govramp-question-set/              # ISORA question set CSV
├── sp-package/                        # Official GovRAMP templates
│   └── 3_Policies/                    # 18 policy templates (AC-SR)
│   └── 4_Procedures/                  # 18 procedure templates
│   └── 7_Plans Catalog/              # CMP, IRP, ISCM, ISCP, SCRM
├── output/                            # All generated content goes here
└── grc/                               # Claude Code GRC plugin
```

### Critical Rules

- **NEVER** edit or rename files in evidence folders
- **ALL** generated content goes to `./output/` only
- **ALWAYS** cite specific artifact paths when making claims
- **ALWAYS** flag weak or indirect evidence — never assume sufficiency

---

## Role 1: PMO Reviewer

### What the GovRAMP PMO Does

The GovRAMP PMO conducts **quarterly snapshot reviews**. For each control they:

1. Review submitted evidence against NIST 800-53 Rev 5
2. Evaluate each sub-part (a, b, c, ...) independently
3. Assign verdicts: **Pass** / **Pass with Concerns** / **Fail** / **No evidence submitted**
4. Provide written feedback identifying exactly what is missing
5. Track evidence expiration (valid for **one year**)
6. Calculate weighted snapshot score using MITRE Control Protection Values

### How to Simulate

```
/grc:control-lookup fedramp <control-id>    # Step 1: Get requirements
# Read all evidence in the control folder    # Step 2: Review evidence
/grc:pmo-review <control-id>                # Step 3: Run the simulation
```

### Output Format

```markdown
## PMO Review: <Control-ID> — <Control Name>

**Overall Verdict**: Pass / Pass with Concerns / Fail
**Maturity Score**: X/5

### Sub-Part Assessment
#### (a) <requirement summary>
- **Verdict**: Pass with Concerns
- **Evidence**: `evidence/<control-id>/artifact.png`
- **Finding**: [What's missing]
- **Remediation**: [What to do]
```

---

## Role 2: 3PAO Assessor

A 3PAO conducts a more rigorous evaluation than the PMO:

1. **Examines** documentation (policies, procedures, SSP, diagrams)
2. **Interviews** key personnel (generates realistic questions)
3. **Tests** controls through assessment objective walkthrough
4. Produces SAR-style findings rated **High / Moderate / Low / Informational**

### How to Simulate

```
/grc:3pao-dryrun <control-id>
```

The assessor is **adversarial but fair** — finding gaps before the real audit.

---

## Role 3: Narrative Generator

SSP narratives follow the standard format:

1. **Control Summary** (1-2 sentences)
2. **Implementation** (tools, services, configurations in place)
3. **Operational Process** (who, what, when, approvals)
4. **Evidence** (bulleted artifact paths)
5. **Gaps / Assumptions** (if needed)
6. **Ready Delta** (changes vs previous phase)

All narratives must:
- Align with official GovRAMP Moderate Impact SP Package templates
- Reference the relevant policy template from `sp-package/3_Policies/` first
- Use GovRAMP ODP parameter values
- Target maturity score of **4 or higher**

---

## Role 4: ISORA Risk Assessor

Runs the full 216-question ISORA assessment. For each question:

1. Read the question and NIST help text
2. Search control evidence folder
3. Cross-reference SOC 2 evidence
4. Determine answer: Fully Implemented / Partially Implemented / Not Implemented
5. Cite evidence and explain reasoning

### Output

- **ISORA Upload CSV**: `output/isora-answer/` (exact ISORA format for upload)
- **Risk Summary**: `output/isora-answer/risk-summary.md` (internal analysis)

---

## Full GovRAMP Ready Sprint Workflow

### Phase 0: ISORA Risk Assessment (Evidence Reality Check)
Run the full 216-question assessment to establish baseline.

### Phase 1: Assess Current State
Score each control's maturity. Compare against PMO feedback. Cross-reference ISORA results.

### Phase 2: Remediate Critical Gaps
Priority order:
1. Controls with PMO **Fail** status
2. Controls **Not Completed**
3. Controls with **Pass with Concerns**

### Phase 3: Generate Narratives
For each control: read template → read evidence → generate narrative → review → iterate until score >= 4.

### Phase 4: 3PAO Dry Run
Full simulated assessment per control. Generate interview questions. Identify remaining gaps.

### Phase 5: Final Readiness Report
Overall maturity scores, controls ready for 3PAO, outstanding POA&M items, evidence expiry calendar.

---

## GovRAMP-Defined ODP Values

| Control | Parameter | Value |
|---------|-----------|-------|
| AC-2(h)(1) | Account change notification | **24 hours** |
| AC-2(h)(2) | Account disable/remove notification | **8 hours** |
| AC-2(j) | Account review frequency | **Quarterly** (privileged), **Annually** (non-privileged) |
| AC-6(2) | Security functions scope | **All security functions** |
| AU-11 | Audit record retention | **90 days online, 1 year total** |
| CM-2(b)(1) | Baseline review frequency | **At least annually + significant change** |
| IA-05(01) | Password authentication | Must include automated screening against compromised passwords |

---

## Maturity Scoring Guide

| Score | Level | Meaning |
|-------|-------|---------|
| **0** | Non-existent | No documentation, no implementation |
| **1** | Initial/Ad Hoc | Some awareness, informal processes |
| **2** | Developing | Documented but inconsistent, partial evidence |
| **3** | Defined | Documented, implemented, evidence exists, gaps in monitoring |
| **4** | Managed | Fully documented, implemented, monitored, minor gaps only |
| **5** | Optimized | Fully automated, continuously monitored, proactively improved |

**Target for GovRAMP Ready**: Level 3 minimum, Level 4 for 3PAO readiness.

---

## Output File Organization

```
output/
├── isora-answer/          # ISORA CSV answers + risk summary
├── narratives/            # SSP narratives per control
├── gaps/                  # Gap analyses
├── 3pao-dryrun/           # Simulated 3PAO assessments
├── pmo-reviews/           # Simulated PMO reviews
├── evidence-map/          # Evidence-to-control mapping
└── poam/                  # Generated POA&M entries
```

---

## Guardrails

1. **Never fabricate evidence.** If it doesn't exist, document the gap.
2. **Never edit source evidence.** Evidence folders are read-only.
3. **Always cite paths.** Every factual claim references a specific file.
4. **Flag indirect evidence.** Note when evidence is adjacent but not exact.
5. **Use GovRAMP parameters.** Not NIST defaults.
6. **Match PMO language.** Pass / Pass with Concerns / Fail.
7. **Track expiry.** Flag evidence within 3 months of expiration.
8. **Be adversarial in 3PAO mode.** Find gaps before the real assessor.
9. **Be constructive in PMO mode.** Specific, actionable feedback.
10. **Score honestly.** Maturity 4 means genuinely ready.
