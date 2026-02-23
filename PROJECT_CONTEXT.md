# Synergos — The Engine Behind the Four Roles

> This document is the engine that powers Synergos. It takes you from zero to understanding how the four AI roles work together to accelerate your GovRAMP/FedRAMP authorization. Read it top to bottom.

---

## Part 1: What Problem Does Synergos Solve?

### The Authorization Journey Is Brutal

Getting a cloud product authorized for government use (GovRAMP Ready, FedRAMP, StateRAMP) means proving you meet ~304 individual security controls from NIST 800-53 Rev 5 at the Moderate baseline. Each control needs:

- **Evidence** — screenshots, configs, logs, policies proving the control is in place
- **Narratives** — auditor-ready descriptions of what you do, who does it, how often, and where
- **Assessment** — independent review by the PMO (quarterly) and eventually a 3PAO (formal audit)

Most organizations spend **months** manually reviewing evidence, writing narratives, and discovering gaps right before the auditor walks in. Synergos compresses this into hours.

### How Synergos Fixes It

Synergos turns Claude Code into a team of four specialized GRC analysts that read your actual evidence and do the work:

| Role | What It Does | Think of It As |
|------|-------------|----------------|
| **PMO Reviewer** | Simulates the GovRAMP PMO quarterly review | Your internal pre-flight check |
| **3PAO Assessor** | Simulates a third-party audit (adversarial) | Finding gaps before the real auditor does |
| **Narrative Generator** | Writes SSP narratives from evidence | Your compliance writing team |
| **ISORA Risk Assessor** | Runs the full 216-question risk assessment | Your evidence reality check |

These aren't chatbot responses — they read your actual documents, cite specific file paths, and produce structured outputs that match real assessment formats.

---

## Part 2: The Frameworks You Need to Know

### NIST 800-53 Rev 5
The master security control catalog. 20 control families covering everything from Access Control (AC) to Supply Chain Risk Management (SR). Each control has an ID like `AC-02` with sub-parts (a through l) and enhancements like `AC-02(01)`.

| Family | Code | What It Covers |
|--------|------|----------------|
| Access Control | AC | Who can log in, what they can do, privilege management |
| Audit & Accountability | AU | Logging, log retention, log review |
| Configuration Management | CM | System baselines, change control, inventory |
| Contingency Planning | CP | Backups, disaster recovery, alternate sites |
| Identification & Authentication | IA | Passwords, MFA, user identity verification |
| Incident Response | IR | Detecting, reporting, handling security incidents |
| Risk Assessment | RA | Vulnerability scanning, risk analysis |
| System & Comms Protection | SC | Encryption, network segmentation, DNS |
| System & Info Integrity | SI | Patching, malware protection, monitoring |
| *...and 11 more families* | | PE, PL, PM, PS, PT, SA, SR, AT, CA, MA, MP |

### ODPs (Organization-Defined Parameters)
Many controls say things like "review accounts [organization-defined frequency]." GovRAMP specifies these values:
- Account reviews: at least annually (quarterly for privileged)
- Audit log retention: 90 days online, 1 year total
- Password minimum length: 12 characters (privileged), 8 characters (non-privileged)
- Vulnerability scanning: at least monthly
- Account change notification: 24 hours
- Account disable/remove notification: 8 hours

### The Shared Responsibility Model
If you run on a cloud provider (AWS, Azure, GCP), some controls are split:
- **Cloud provider handles**: Physical security (PE family), hardware, hypervisor
- **You handle**: Application security, access control, encryption config, monitoring
- **Shared**: Network security (provider gives you VPCs, you configure security groups)

This is why all 22 PE (Physical & Environmental) questions are automatically "Fully Implemented" — they're inherited from your cloud provider.

### GovRAMP Authorization Stages

| Stage | Controls | What You Need |
|-------|----------|---------------|
| **Progressing Snapshot** | ~40 | Quarterly PMO reviews, evidence for core controls |
| **Core** | ~60 | 20 additional controls, SSP draft |
| **Ready** | ~80 | 20 more, 3PAO engagement, full SSP |
| **Authorized** | 319 | Full NIST 800-53 Moderate, 3PAO SAR, ATO package |

### SOC 2 Cross-Reference
If your organization has a SOC 2 Type II audit, that evidence is gold. SOC 2 and GovRAMP overlap significantly. Evidence that passed a SOC 2 audit is legitimate evidence for GovRAMP controls — Synergos knows how to cross-reference between them.

---

## Part 3: The Four Roles (How They Work)

### Role 1: PMO Reviewer

**What the real GovRAMP PMO does:** Conducts quarterly snapshot reviews. For each control, they review evidence, evaluate each sub-part independently, and assign verdicts: Pass / Pass with Concerns / Fail.

**What Synergos does:** Replicates this exact process. For each control:
1. Reads the NIST 800-53 Rev 5 requirements with GovRAMP parameters
2. Reads ALL evidence in the control folder
3. Cross-references SOC 2 evidence
4. Evaluates each sub-part (a, b, c...) independently
5. Assigns verdicts with specific feedback on what's missing
6. Compares against historical PMO feedback if available
7. Scores maturity (0-5 scale)

**Command:** `/grc:pmo-review <control-id>`

**Output:** Structured review with per-sub-part verdicts, evidence inventory, and specific remediation recommendations.

### Role 2: 3PAO Assessor

**What a real 3PAO does:** Conducts a significantly more rigorous evaluation than the PMO. They examine documentation, interview staff, and test controls. Findings are rated High / Moderate / Low.

**What Synergos does:** Simulates this adversarial assessment:
1. Walks through every assessment objective from NIST 800-53A
2. Checks evidence for relevance, currency (<1 year), completeness, specificity
3. Generates realistic interview questions a 3PAO would ask your team
4. Produces SAR-style findings with risk ratings
5. Auto-generates POA&M entries for any findings

**Command:** `/grc:3pao-dryrun <control-id>`

**Output:** Full assessment report with findings, evidence inventory, interview readiness checklist, and POA&M entries.

### Role 3: Narrative Generator

**What SSP narratives need:** Every control requires a narrative that covers the Five W's — what is done, who does it, how it's done, when/how often, and where (which systems). GovRAMP-defined parameter values must appear. Every claim must reference evidence.

**What Synergos does:**
1. Reads the official GovRAMP policy template for the control family (from `sp-package/3_Policies/`)
2. Reads the procedure template (from `sp-package/4_Procedures/`)
3. Reads all evidence in the control folder
4. Generates a narrative following the standard format:
   - Control Summary → Implementation → Operational Process → Evidence → Gaps → Ready Delta
5. Self-reviews using `/grc:review-narrative` and iterates until maturity score >= 4

**Commands:** `/grc:review-narrative`, `/grc:ssp-section`, `/grc:score-maturity`

### Role 4: ISORA Risk Assessor

**What ISORA is:** The GRC platform that GovRAMP uses to manage risk assessments. The 216-question assessment is administered through ISORA and uploaded via CSV.

**What Synergos does:** For each of the 216 questions:
1. Reads the question and full NIST 800-53 help text
2. Searches the control evidence folder
3. Cross-references SOC 2 evidence
4. Checks PMO status from the Snapshot Matrix
5. Makes the call: Fully Implemented / Partially Implemented / Not Implemented
6. Writes an explanation citing specific file paths and confidence level
7. Outputs in exact ISORA CSV upload format

**Command:** `/grc:isora-assess [family|all]`

**Output:**
- ISORA upload CSV (exact format for direct upload)
- Risk summary with pass rates by family and weight, critical gaps listed

---

## Part 4: Key Decision Rules

These rules govern how all four roles evaluate evidence:

1. **You are checking if the control IS IN PLACE — not if the narrative is well-written.** A poorly-written narrative about a control that IS implemented = favorable. A beautiful narrative with no supporting evidence = unfavorable.

2. **Evidence hierarchy:**
   Screenshots of actual configs > SOC 2 auditor-verified artifacts > PMO Pass verdicts > Policies + procedures > Narratives alone

3. **Policy alone is NOT sufficient.** A policy saying "we do X" without evidence of actually doing X = Partially Implemented at best.

4. **SOC 2 evidence counts.** If a SOC 2 auditor verified it, that's real, independent evidence.

5. **PMO "Pass" is a strong favorable signal.** The GovRAMP PMO already reviewed the evidence.

6. **PMO "Fail" is a strong unfavorable signal** — but check if new evidence was submitted since.

7. **PE controls = always favorable** if you run on a major cloud provider. Physical security is their responsibility.

8. **Never fabricate evidence.** If it's not there, say so. Generate the gap, not a fiction.

---

## Part 5: The Directory Structure

```
synergos-grc/
├── CLAUDE.md                          # Working rules for Claude Code
├── PROJECT_CONTEXT.md                 # This file — the engine
├── README.md                          # Setup guide
│
├── grc/                               # Claude Code plugin
│   ├── commands/                      # 27 slash command definitions
│   ├── skills/grc-knowledge/          # 72+ reference files
│   │   ├── SKILL.md                   # Core plugin brain
│   │   ├── your-context.md.template   # Your org config (copy & fill in)
│   │   ├── frameworks/                # 16 framework references
│   │   ├── mappings/                  # 9 cross-framework mapping files
│   │   ├── audits/                    # 14 audit methodology references
│   │   ├── conmon/                    # 6 continuous monitoring references
│   │   ├── oscal/                     # OSCAL JSON (NIST + FedRAMP)
│   │   └── tooling/                   # GRC tooling categories
│   └── agents/                        # Research agent definition
│
├── workflow/                          # Agentic workflow methodology
│
├── sp-package/                        # Official GovRAMP Moderate Impact SP Package
│   └── Moderate Impact SP Package/
│       ├── 1_OCM/                     # Official Controls Matrix
│       ├── 2_Diagrams/                # Authorization boundary guidance
│       ├── 3_Policies/                # 18 policy templates (AC through SR)
│       ├── 4_Procedures/              # 18 procedure templates
│       ├── 5_User Guide/
│       ├── 6_Rules of Behavior/
│       ├── 7_Plans Catalog/           # CMP, IRP, ISCM, ISCP, SCRM templates
│       ├── 8_Continuous Monitoring Matrix/
│       └── 9_Laws & Regs/
│
├── govramp-question-set/              # ISORA 216-question risk assessment CSV
├── evidence/                          # Your control evidence (one folder per control)
├── soc2-evidence/                     # Your SOC 2 audit artifacts
├── govramp-assessment/                # GovRAMP assessment submission
├── govramp-process/                   # Snapshot matrices, score letters
│
└── output/                            # ALL generated content goes here
    ├── narratives/                    # SSP narratives per control
    ├── pmo-reviews/                   # Simulated PMO reviews
    ├── 3pao-dryrun/                   # Simulated 3PAO assessments
    ├── gaps/                          # Gap analyses
    ├── isora-answer/                  # ISORA CSV answers + risk summary
    ├── evidence-map/                  # Evidence-to-control mapping
    └── poam/                          # Generated POA&M entries
```

---

## Part 6: The ISORA CSV Format

The output CSV that gets uploaded into ISORA must be exact:

### Header Structure
```csv
# Field Name,Field Description
# question_category,Question Category
# question_text,Question Text
# answer_text,Answer Text
# answer_details,Answer Details
# answer,Answer
uuid,question_category,question_text,answer_text,answer_details,answer
```

### Field Rules
| Field | Rule |
|-------|------|
| uuid | Leave blank for new records |
| question_category | Must match ISORA's value exactly |
| question_text | Must match character-for-character |
| answer_text | `Fully Implemented`, `Partially Implemented`, `Not Implemented/Planned`, or `Alternative Implementation` |
| answer_details | Free-text explanation (single-line, no newlines) |
| answer | Numeric: `1.00`, `0.50`, `0.00`, or `0.75` |

### Encoding
- UTF-8 with BOM (`utf-8-sig`) for Excel compatibility
- Keep original em dashes in question fields
- Strip ALL newlines from answer_details
- Standard CSV quoting for fields containing commas

---

## Part 7: The Full Sprint Workflow

### Phase 0: ISORA Risk Assessment (Evidence Reality Check)
Run the full 216-question assessment to establish your baseline. This tells you where you actually stand.

### Phase 1: Assess Current State
Score each control's maturity (0-5). Compare against PMO feedback. Cross-reference ISORA results. Output: maturity scores spreadsheet.

### Phase 2: Remediate Critical Gaps
Priority order:
1. Controls with PMO **Fail** — fix these first
2. Controls **Not Completed** — need evidence submission
3. Controls with **Pass with Concerns** — address specific PMO feedback

### Phase 3: Generate Narratives
For each control: read the GovRAMP policy template → read evidence → generate narrative → self-review → iterate until maturity score >= 4.

### Phase 4: 3PAO Dry Run
Full simulated assessment per control. Generate interview questions. Identify remaining gaps. This is your dress rehearsal.

### Phase 5: Final Readiness Report
Overall maturity scores, controls ready for 3PAO, outstanding POA&M items, evidence expiry calendar, interview prep guide.

---

## Part 8: Guardrails

1. **Never fabricate evidence.** If evidence does not exist, say so.
2. **Never edit source evidence.** Evidence folders are read-only.
3. **Always cite paths.** Every factual claim references a specific file.
4. **Flag indirect evidence.** Note when evidence is adjacent but not exact.
5. **Use GovRAMP parameters.** Not NIST defaults.
6. **Match PMO language.** Pass / Pass with Concerns / Fail.
7. **Track expiry.** Note when evidence will expire and flag anything within 3 months.
8. **Be adversarial in 3PAO mode.** The value is finding gaps before the real assessor does.
9. **Be constructive in PMO mode.** Specific, actionable feedback — not just "insufficient."
10. **Score honestly.** A maturity score of 4 means genuinely ready for assessment, not aspirational.
