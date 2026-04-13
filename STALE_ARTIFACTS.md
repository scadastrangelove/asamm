# PDF Artifacts

The PDF files in this repository are generated from Markdown sources using
pandoc + weasyprint. Source files are in `assets/sources/`.

## Current PDFs (v0.2.0-draft)

| File | Language | Generated from |
|---|---|---|
| `agentic-samm-review.pdf` | English | `assets/sources/agentic-samm-full.md` |
| `assets/asamm-ru.pdf` | Russian | `assets/sources/agentic-samm-ru.md` |

Both PDFs include all v0.2 additions: AI-04, AI-05, Axiom 6, evidence taxonomy,
audit methodology reference (§2.7).

## Regeneration

See `assets/sources/README.md` for the full regeneration procedure.

**Note on figure paths:** the Markdown sources reference figures as
`file:///home/claude/asamm_v02/assets/figures/`. Update this path to match
your local checkout before regenerating.
