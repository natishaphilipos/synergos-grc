# Synergos GRC — Working Rules

## Goal
Accelerate GovRAMP/FedRAMP authorization using agentic GRC assessment workflows powered by four specialized AI roles: PMO Reviewer, 3PAO Assessor, Narrative Generator, and ISORA Risk Assessor.

## Source Evidence Structure
Evidence lives in control-family folders under `./evidence/`:
```
evidence/
├── AC-02 Account Management/
├── AC-04 Information Flow Enforcement/
├── AU-11 Audit Record Retention/
└── ... (one folder per in-scope control)
```

Additional evidence sources:
- `./soc2-evidence/` — SOC 2 audit artifacts for cross-referencing
- `./sp-package/` — Official GovRAMP Moderate Impact SP Package templates (policies, procedures, plans)
- `./govramp-process/` — Snapshot matrices, score letters, controls matrix
- `./govramp-question-set/` — ISORA question set CSV (216 questions)
- `./govramp-assessment/` — Full GovRAMP assessment submission folders

## Ground Rules
- **DO NOT** edit or rename any files in evidence folders — they are READ ONLY
- **Write all generated content to `./output/` only**
- When you make a claim, cite the specific artifact path(s) that support it
- If evidence is weak or indirect, say so and list what would make it sufficient
- Use GovRAMP-defined ODP parameters, not NIST defaults
- Match PMO language: Pass / Pass with Concerns / Fail
- Never fabricate evidence — if it doesn't exist, document the gap
- Track evidence expiry — flag anything within 3 months of expiration

## Narrative Format (per control)
1. Control summary (1–2 sentences)
2. Implementation (what is in place — name tools, services, configurations)
3. Operational process (who does what, how often, approvals)
4. Evidence (bulleted artifact list with paths)
5. Gaps / assumptions (only if needed)
6. Ready delta (what changed vs previous assessment phase)

## Tone
Clear, auditor-friendly, specific. No marketing language.

## Organization Context
See `grc/skills/grc-knowledge/your-context.md` for organization-specific configuration.
If that file doesn't exist, copy from `your-context.md.template` and fill in your details.

## Plugin
Load the GRC plugin with:
```bash
claude --plugin-dir "./grc"
```

## Key Reference Files
- `grc/skills/grc-knowledge/SKILL.md` — Core plugin brain (GRC analyst persona + framework knowledge)
- `grc/commands/` — All 27 slash command definitions
- `grc/agents/grc-researcher.md` — Read-only research agent
- `workflow/README.md` — Full agentic workflow methodology
- `PROJECT_CONTEXT.md` — How the four roles work together (the engine)
- `sp-package/` — Official GovRAMP templates for narrative generation
