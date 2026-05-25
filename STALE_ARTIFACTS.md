# PDF Artifacts

The PDF files in this repository are generated from Markdown sources using
pandoc + weasyprint. Source files are in `assets/sources/`.

## Current PDFs (v0.3.0-draft)

| File | Language | Generated from |
|---|---|---|
| `agentic-samm-review.pdf` | English | `assets/sources/agentic-samm-full.md` |
| `assets/asamm-ru.pdf` | Russian | `assets/sources/agentic-samm-ru.md` (v0.2 translation; pending v0.3 sync) |

The English PDF source includes all v0.3 additions: AG-04, AI-06, trust grading
calibration, delegated evidence rules, ecosystem modifiers E1-E3, and the updated
audit methodology reference (§2.7).

## Regeneration

See `assets/sources/README.md` for the full regeneration procedure.

**Note on figure paths:** the English Markdown source uses repository-relative
figure paths and can be regenerated from the repository root.
