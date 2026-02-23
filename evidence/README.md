# Evidence

Place your control evidence here — one folder per NIST 800-53 control.

## Expected Structure

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
└── ... (one folder per in-scope control)
```

Each folder should contain the artifacts that demonstrate the control is implemented:
- `.docx` narratives
- `.png` / `.pdf` screenshots
- Policy documents
- Configuration exports

**These folders are READ ONLY** — the agentic workflow will never modify evidence files.
