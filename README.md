# Project Synergos

**Agentic GRC assessment automation for GovRAMP, FedRAMP, and 15+ compliance frameworks — powered by Mario Lunato's claude-grc-plugin (https://github.com/mlunato47/claude-grc-plugin).**

Synergos (σύνεργος) — from the Greek syn (together) + ergon (work) — originally meant "fellow worker" or "co-laborer," someone who works alongside you toward a shared outcome.

Synergos is your GRC co-worker: it turns Claude Code into a team of specialized GRC analysts that can simulate PMO reviews, run 3PAO dry runs, generate SSP narratives, and execute full risk assessments with question sets made by SaltyCloud PBC — all against your actual evidence.

It can also read control narratives, scores implementation maturity, prepares audit checklists, maps controls across frameworks, and generates actionable findings & send documentation to your project Management tool (Jira, Notion etc) through their mcp server.

Where Synergos truly earns its name is in the synergy it creates between compliance worlds that traditionally operate in silos. Most organizations treat SOC 2, GovRAMP, ISO 27001, and CMMC as entirely separate efforts — separate evidence, separate narratives, separate audit prep — even though they're often protecting the same systems with the same controls. Synergos bridges that gap. It can take your existing SOC 2 Trust Services Criteria evidence — say your CC6.1 logical access controls or CC7.2 monitoring artifacts — and map them through NIST 800-53 as a universal hub to infer how ready you are for a GovRAMP authorization. It identifies where your evidence already satisfies federal requirements, where the gaps are, and what additional specificity or documentation you need to get there

---

> **⚠️ Before You Deploy: A Word on Security**
>
> Synergos gives AI agents read access to your compliance evidence — the same documents you'd hand to an auditor. That's powerful, and it's also something you should think about before you run it.
>
> **What to consider:**
> - Your evidence folders may contain CUI, PII, system architecture details, vulnerability scan results, and network diagrams. Synergos reads these files to do its job.
> - This project is an **open-source tool, not a hardened product**. It does not ship with its own access controls, encryption, audit logging, or data loss prevention.
> - Follow the **principle of least privilege**: only give the agent access to the evidence it needs for the assessment you're running. Don't point it at your entire file system.
> - **Assess the risk for your organization** before going full send. If your data is classified, sensitive, or subject to handling requirements — scope your usage accordingly.
> - Synergos writes output to `./output/` only and never modifies evidence. But you're responsible for where that output goes next.
>
> **TL;DR:** Synergos is a sharp tool built for GRC practitioners who understand what they're working with. Treat your evidence folders like you'd treat any sensitive data — because that's exactly what they are. We built this to help, not to babysit. Use it wisely.

---

## Credits

Built on [claude-grc-plugin](https://github.com/mlunato47/claude-grc-plugin) by **Mario Lunato** — the foundational Claude Code plugin providing 15+ framework references, 24 commands, 72+ reference files, and OSCAL structured data. Mario's work made the entire GRC-as-code approach possible. Synergos extends his open-source plugin with GovRAMP/FedRAMP workflow optimization, agentic assessment automation, and integration patterns for real-world authorization journeys.

---

## Breakdown of It's Different Roles

Synergos provides **four GRC Agent roles** that work together to accelerate your path to authorization:

| Role | What It Does | Command |
|------|-------------|---------|
| **PMO Reviewer** | Simulates GovRAMP/FedRAMP PMO quarterly snapshot reviews | `/grc:pmo-review` |
| **3PAO Assessor** | Simulates third-party assessment — finds gaps before the real audit | `/grc:3pao-dryrun` |
| **Narrative Generator** | Produces auditor-ready SSP control narratives from evidence | `/grc:review-narrative` |
| **ISORA Risk Assessor** | Runs the full 216-question evidence-based risk assessment | `/grc:isora-assess` |

Plus 20 additional commands for control lookups, cross-framework mapping, gap analysis, audit prep, compliance calendars, tabletop exercises, and more.

---

## Prerequisites

| Requirement | Version | How to Check |
|-------------|---------|-------------|
| Claude Code CLI | Latest | `claude --version` |
| Git | 2.x+ | `git --version` |
| Anthropic API Key | — | `echo $ANTHROPIC_API_KEY` |

---

## Quick Start

### Step 1: Clone the Repo

```bash
git clone https://github.com/natishaphilipos/synergos-grc.git
cd synergos-grc
```

### Step 2: Configure Your Organization Context

```bash
cp grc/skills/grc-knowledge/your-context.md.template grc/skills/grc-knowledge/your-context.md
```

Edit `your-context.md` with your details:
- **System Name** — e.g., "MyApp"
- **Authorization Target** — GovRAMP Ready, FedRAMP Moderate, StateRAMP, etc.
- **Baseline** — Low, Low+, Moderate, or High
- **Cloud Provider** — AWS, Azure, GCP, or hybrid
- **Evidence Paths** — where your control evidence folders live
- **Key Personnel** — system owner, GRC analyst, technical lead

> **This is the only file you need to customize.** Everything else — frameworks, mappings, OSCAL data, commands — works out of the box.

### Step 3: Set Up Your Evidence

Synergos expects one folder per control in the `evidence/` directory:

```
evidence/
├── AC-02 Account Management/
│   ├── Narrative.docx
│   ├── Screenshots/
│   └── Policy.pdf
├── AC-04 Information Flow Enforcement/
│   ├── Narrative.docx
│   └── Data-Flow-Diagram.png
├── AU-11 Audit Record Retention/
└── ... (one folder per control)
```

**For GovRAMP specifically:**
- Download the **Moderate Impact SP Package** from [govramp.org](https://govramp.org) and place it in `sp-package/`
- Use the official `.docx` templates for all narratives (IRP, CP, CMP, policies)
- Progressing Snapshot requires ~40 controls → Core adds 20 → Ready adds 20 → Authorized is full 319

### Step 4: Launch Claude Code with Synergos

```bash
claude --plugin-dir "./grc"
```

You should see all `/grc:*` commands available. Test with:

```
/grc:control-lookup nist AC-02
```

### Step 5: Run Your First Assessment

**Single control review:**
```
/grc:pmo-review AC-02
```

**Full assessment across all controls:**
```
Assess all controls in my evidence folder using /grc:pmo-review.
Evaluate each sub-part against NIST 800-53 Rev 5 with GovRAMP Moderate parameters.
Generate a findings summary.
```

**3PAO dry run:**
```
/grc:3pao-dryrun AC-02
```

---

## Available Commands (24 Total)

### Framework & Controls
- `/grc:control-lookup` — Search controls across 15+ frameworks
- `/grc:map-controls` — Cross-framework mapping (NIST as universal hub)
- `/grc:oscal-guide` — OSCAL structured data guidance

### Document Review
- `/grc:review-narrative` — SSP narrative quality review
- `/grc:review-ssp` — Full SSP review
- `/grc:review-poam` — POA&M validation
- `/grc:review-policy` — Policy completeness review
- `/grc:review-crm` — Customer Responsibility Matrix review
- `/grc:score-maturity` — Control maturity scoring (0-5)

### Assessments
- `/grc:pmo-review` — PMO snapshot review simulation
- `/grc:3pao-dryrun` — 3PAO assessment simulation
- `/grc:isora-assess` — ISORA risk assessment (216 questions)
- `/grc:gap-analysis` — Gap analysis against target baseline
- `/grc:audit-prep` — Audit preparation checklist

### Operational
- `/grc:significant-change` — Change impact assessment
- `/grc:inheritance` — Control inheritance analysis
- `/grc:sar-response` — SAR finding response drafts
- `/grc:conmon-guide` — Continuous monitoring guidance
- `/grc:compliance-calendar` — Recurring activities calendar
- `/grc:tabletop-scenario` — IR/CP tabletop exercises
- `/grc:multi-framework` — Cross-framework overlap analysis

---

## GovRAMP Assessment Workflow

```
Phase 1: PMO Review (/grc:pmo-review)
    → Phase 2: Remediate findings
    → Phase 3: Generate narratives (/grc:review-narrative)
    → Phase 4: 3PAO Dry Run (/grc:3pao-dryrun)
    → Phase 5: Readiness report (/grc:gap-analysis)
```

### GovRAMP Stages

| Stage | Controls | What You Need |
|-------|----------|---------------|
| Progressing Snapshot | ~40 | Evidence folders + narratives for core controls |
| Core | ~60 | 20 additional controls, SSP draft |
| Ready | ~80 | 20 more, 3PAO engagement, full SSP |
| Authorized | 319 | Full NIST 800-53 Moderate, 3PAO SAR, ATO package |

---

## Using with Other Frameworks

Synergos supports **15+ frameworks** with NIST 800-53 as the universal mapping hub:

```
/grc:map-controls soc2 CC6.1           # SOC 2 → NIST → see overlaps
/grc:review-narrative fedramp AC-02     # FedRAMP-specific narrative review
/grc:multi-framework AC-02             # One control across all frameworks
/grc:map-controls iso27001 A.9.1       # ISO 27001 mapping
/grc:map-controls cmmc AC.L2-3.1.1     # CMMC mapping
```

Your SOC 2 evidence can satisfy GovRAMP requirements. Your GovRAMP narratives map to ISO 27001. Synergos shows you exactly where the overlaps are — so you never duplicate work.

---

## Directory Structure

```
synergos-grc/
├── README.md                     ← You are here
├── CLAUDE.md                     ← Working rules for Claude Code sessions
├── LICENSE                       ← MIT License
├── grc/                          ← Claude Code plugin (all 24 commands + knowledge base)
│   ├── commands/                 ← Slash command definitions
│   ├── skills/grc-knowledge/     ← Framework references, mappings, OSCAL data
│   └── agents/                   ← Research agent definition
├── workflow/                     ← Agentic workflow methodology documentation
├── evidence/                     ← Your control evidence (one folder per control)
├── soc2-evidence/                ← Your SOC 2 audit artifacts (if applicable)
├── govramp-assessment/           ← GovRAMP assessment submission folders
├── govramp-process/              ← Snapshot matrices, score letters
├── govramp-question-set/         ← ISORA question set CSV
├── sp-package/                   ← Official Moderate Impact SP Package templates
└── output/                       ← All generated content (narratives, reviews, gaps)
```

---

## Optional Integrations

### Notion (Findings Tracker)
Connect the Notion MCP server to automatically create findings databases and dashboards after each assessment.

### Jira (POA&M Tracking)
Connect the Atlassian MCP server to route findings to Jira as issues, create POA&M entries, and manage remediation sprints.

---

## Contributing

This project builds on the excellent work of [Mario Lunato's claude-grc-plugin](https://github.com/mlunato47/claude-grc-plugin). Framework references, OSCAL data, and command definitions originate from that project. If you find value in the GRC knowledge base, consider starring his repo too.

---

## Troubleshooting

**Commands not showing up?**
Make sure you're pointing to the `grc/` directory:
```bash
claude --plugin-dir "./grc"    # Correct
claude --plugin-dir "."        # Wrong — needs the grc/ subdirectory
```

**"No evidence found" errors?**
Check that your `your-context.md` has the correct evidence paths and that your folder names match control IDs (e.g., `AC-02 Account Management`).

**Narratives aren't using GovRAMP templates?**
Download the Moderate Impact SP Package from [govramp.org](https://govramp.org) and place it in `sp-package/`. The plugin reads policy templates before generating narratives.

---

## License

MIT — see [LICENSE](LICENSE).
