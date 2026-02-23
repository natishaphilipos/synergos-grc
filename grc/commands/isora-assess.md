---
name: isora-assess
description: "Run an evidence-based risk assessment using the ISORA question set. For each question, searches control-family folders and SOC 2 Evidence to determine if the control is actually in place — not narrative quality, but operational reality."
arguments: "[family|all] — Control family (AC, AU, CM, CP, IA, IR, MA, PE, RA, SA, SC, SI) or 'all'"
---

# ISORA Evidence-Based Risk Assessment

## What This Command Does

This is NOT a narrative review. This is NOT a maturity score. This command answers one question per control:

> **"Based on the evidence artifacts available, can we infer that this control is actually in place and operating?"**

It reads the ISORA question set (216 questions across 12 families), then for each question it hunts through every available evidence source to find proof. If proof exists, the answer is favorable. If proof is missing or contradictory, the answer is unfavorable. If proof is indirect but strongly suggestive, it's favorable with an inference note.

## Source Files

### ISORA Question Set
**Path**: `GovRAMP Ready Question Set/GovRAMP_Ready_Assessment_2026.csv`

This CSV has a metadata header block (rows 0-11), a column header at row 12, and question data starting at row 13. The columns are:

| Col | Field | Use |
|-----|-------|-----|
| 0 | `text` | The question (e.g., "AC-02 a: Does your organization have a documented list...") |
| 1 | `parent` | Parent question text (blank if top-level) |
| 2 | `question_category` | Family label (e.g., "AC — Access Control") |
| 3 | `show_if_parent` | `y` = show if parent favorable, `n` = show if parent unfavorable |
| 4 | `explanation_required` | `f` = when favorable, `n` = when unfavorable, `b` = both |
| 5 | `weight` | `c` = critical, `h` = high, `m` = medium, `l` = low, `i` = informational |
| 6 | `allow_explanation` | `true` / `false` |
| 7 | `help_text` | Full NIST 800-53 Rev 5 control language — READ THIS for each question |
| 8 | `question_response_group` | Answer format (e.g., "FedRAMP") |
| 9 | `documentation_required` | When docs are required |
| 10 | `allow_documentation` | `true` / `false` |

### Evidence Sources (search in this order)

**1. Control-family evidence folders** (primary):
```
evidence/AC-02 Account Management/
evidence/CM-06 Configuration Settings/
... (one folder per in-scope control)
```
AND the GovRAMP Assessment folder (if populated):
```
govramp-assessment/
  AC-02 Account Management/
  CM-06 Configuration Settings/
  ... etc
```

**2. SOC 2 Evidence folder** (cross-reference):
```
soc2-evidence/
```
If you have SOC 2 artifacts, map them to GovRAMP controls in your `your-context.md` file. SOC 2 evidence demonstrates the same controls from a different audit perspective and carries significant weight.

**3. Screenshots folder**:
```
evidence/Screenshots/
```

**4. GovRAMP Snapshot Matrix** (for PMO verdicts):
```
govramp-process/  (your Snapshot Matrix .xlsx file)
```
Sheet `3_Snapshot Criteria` has PMO feedback per control. Sheet `4_Test Case Procedures` has assessment objectives.

**5. SOC 2 Gap Analysis** (if available):
```
govramp-assessment/Risk Assessment/  (any prior gap analysis CSV)
```

## How to Assess Each Question

For EACH of the 216 questions, follow this exact process:

### Step 1: Understand What Is Being Asked
Read the question text (column 0) AND the help_text (column 7). The help_text contains the full NIST 800-53 Rev 5 control language. Understand the specific requirement.

### Step 2: Find the Right Evidence Folder
Extract the control ID from the question text (e.g., "AC-02 a:" → AC-02). Find the matching evidence folder. Note: folder names vary slightly (e.g., "AC-02 Account Management ( a thru l )" vs "AC-02(1)_Account Management"). Match by control ID prefix.

### Step 3: Read the Evidence
Actually READ the documents in the evidence folder. For `.docx` files, extract the text. For screenshots (`.png`), note they exist and what the filename suggests they show. For PDFs, note their presence and filename.

Key things to look for:
- **Policies**: Do they exist? Do they cover this specific sub-requirement?
- **Narratives**: Do they describe an implementation? (But remember: you're checking if the control is IN PLACE, not if the narrative is well-written)
- **Screenshots**: Do they show the actual configuration or setting being implemented?
- **SOC 2 artifacts**: Do they demonstrate the same capability from a different angle?
- **Procedures**: Do they document an operational process?

### Step 4: Check SOC 2 Cross-Reference
Search the SOC 2 Evidence folder for artifacts that relate to this control. Use the mapping table above. Also check the SOC 2 Gap Analysis CSV — if the SOC 2 assessment rated a related question as "Fully Implemented", that's supporting evidence.

### Step 5: Check PMO Status
Look up the control in the Snapshot Matrix. If the PMO rated it:
- **Pass**: Strong supporting signal
- **Pass with Concerns**: Favorable, but note the specific concern
- **Fail**: Unfavorable unless new evidence was submitted since
- **No evidence submitted**: Unfavorable

### Step 6: Make the Call

| Conclusion | Answer | ISORA Value | Criteria |
|------------|--------|-------------|----------|
| Direct evidence shows control operating | **Yes** | `favorable` | Policy + procedure + screenshot/config showing implementation |
| Indirect evidence strongly suggests it | **Yes (Inferred)** | `favorable` | Related evidence implies it (e.g., SOC 2 shows it, or PMO passed it) |
| Some parts implemented, gaps remain | **Partial** | `unfavorable` | Evidence covers some sub-parts but not all |
| No evidence anywhere | **No** | `unfavorable` | Nothing in any evidence source |
| AWS inherited (PE controls) | **N/A** | `favorable` | Physical/environmental = CSP responsibility |
| Not in scope for this snapshot level | **N/A** | `favorable` | Mark with note about scope |

### Step 7: Write the Explanation
For `answer_details`, include:
- **What evidence you found** (cite specific file paths)
- **What the evidence shows** (1-2 sentences)
- **Confidence level**: High (direct proof) / Medium (inferred) / Low (weak signal)
- **Gap** (if answer is not Yes): What specific evidence or implementation is missing

## Special Rules

### PE (Physical & Environmental) Controls
All 22 PE questions → `favorable` if your system runs on a major cloud provider (AWS, Azure, GCP). Physical and environmental controls are inherited from the CSP under the shared responsibility model. Note: "Inherited from [Cloud Provider] (IaaS provider). Physical and environmental controls are CSP responsibility under shared responsibility model."

### Controls Not in Progressing Snapshot Scope
The current Progressing Snapshot covers ~40 controls. Questions about controls NOT in scope (e.g., CP-02, CM-03, CM-08, CM-11, SA-11, etc.) should:
1. First check SOC 2 Evidence for any cross-reference
2. If SOC 2 evidence exists → `favorable` (inferred from SOC 2)
3. If no evidence anywhere → `unfavorable` with gap noting this control needs evidence for GovRAMP Ready

### Duplicate Questions
The ISORA CSV contains some duplicate questions (same text, same category). Answer them identically.

## Output

### Primary: ISORA Upload CSV
Write to `./output/isora answer/GovRAMP_Ready_Assessment_2026_answered.csv`

**CRITICAL**: This file will be uploaded directly into ISORA GRC. The format must EXACTLY match the survey template at `./output/isora answer/survey-template.csv`:

```csv
# Field Name,Field Description
# uuid (Optional),If uuid is provided the record will be updated
# question_category,Question Category
# question_text,Question Text
# answer_text,Answer Text
# answer_details,Answer Details
# answer,Answer
uuid,question_category,question_text,answer_text,answer_details,answer
,AC — Access Control,AC-02 a: Does your organization have a documented list...,Yes,Confidence: High. Evidence: evidence/AC-02/AC-Policy.docx defines allowed account types. soc2-evidence/admin-accounts.docx shows admin accounts. PMO Status: Pass with Concerns (prohibited types not enumerated).,favorable
```

- `uuid`: Always blank (new records)
- `question_category`: Copy from source CSV column 2, but **replace em dashes (—) with plain hyphens (-)** and remove any other non-ASCII characters. ISORA cannot handle Unicode em dashes. Example: `AC — Access Control` → `AC - Access Control`
- `question_text`: Copy from source CSV column 0, replacing any em dashes/smart quotes with plain ASCII equivalents
- `answer_text`: Yes / Yes (Inferred) / Partial / No / N/A
- `answer_details`: Your explanation with evidence paths and gaps
- `answer`: `favorable` or `unfavorable`

### Secondary: Risk Summary
Write to `./output/isora answer/risk-summary.md`

Include:
- Total pass/fail counts and percentages
- Pass rate by question weight (critical/high/medium/low)
- Pass rate by control family
- List of all "No" answers (critical gaps)
- List of all "Partial" answers (evidence gaps)
- List of "Yes" answers that have noted concerns
- Consolidated evidence gaps summary

## Example Usage

```
/grc:isora-assess all          # Full 216-question assessment
/grc:isora-assess AC           # Just Access Control (27 questions)
/grc:isora-assess SI           # Just System & Information Integrity (23 questions)
/grc:isora-assess CM           # Just Configuration Management (36 questions)
```

## Encoding Rules

The output CSV MUST be clean ASCII-compatible for ISORA upload:
- Write with UTF-8-BOM encoding (`utf-8-sig`) for Excel compatibility
- Replace `—` (em dash, U+2014) with `-` (hyphen-minus)
- Replace `–` (en dash, U+2013) with `-`
- Replace `"` `"` (smart quotes) with `"` (straight quote)
- Replace `'` `'` (smart apostrophes) with `'` (straight apostrophe)
- Replace `…` (ellipsis) with `...`
- No other Unicode characters in category names or answer fields

## Important Reminders

1. You are checking if the control IS IN PLACE — not if the narrative is well-written
2. A poorly-written narrative about a control that IS implemented → favorable
3. A beautifully-written narrative with no supporting evidence → unfavorable
4. SOC 2 evidence counts — if the SOC 2 audit verified it, that's real evidence
5. PMO "Pass" is a strong signal — the GovRAMP PMO already reviewed the evidence
6. PMO "Fail" is a strong signal the other way — but check if new evidence was submitted since
7. Screenshots showing actual configurations are the strongest evidence
8. Policies alone are NOT sufficient — a policy saying "we do X" without evidence of doing X → Partial at best
9. Never fabricate evidence. If it's not there, say so.
