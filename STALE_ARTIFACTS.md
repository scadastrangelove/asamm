# PDF Artifacts

The PDF files in this repository are generated from Markdown sources using
pandoc + weasyprint. Source files are in `assets/sources/`.

## Current PDFs (v0.5.0-draft)

| File | Language | Generated from |
|---|---|---|
| `agentic-samm-review.pdf` | English | `assets/sources/agentic-samm-full.md` |
| `assets/asamm-ru.pdf` | Russian | `assets/sources/agentic-samm-ru.md` (v0.2 translation; pending v0.5 sync) |

> Two byte-identical copies of the Russian PDF (`asamm-ru-old.pdf`,
> `agentic-samm-ru-v02.pdf`) were removed; `assets/asamm-ru.pdf` is the single
> canonical Russian artifact until the v0.5 translation is regenerated.

The English PDF source includes all v0.5.0-draft additions: AG-04, AI-06, trust grading
calibration, delegated evidence rules, ecosystem modifiers E1-E3, and the updated
audit methodology reference (§2.7).

## Reviewer pack scope

The v0.5 reviewer pack should treat `agentic-samm-review.pdf` and
`assets/sources/agentic-samm-full.md` as the current review artifacts.
The Russian PDF is retained as a legacy translation artifact and should not be
used to review v0.5 normative changes.

Historical audit samples under `audit/samples/` are retained as raw evidence of
methodology development. They reference ASAMM v0.2 and have not yet been
re-run against the v0.5 control set; include them only as background, not as
current normative examples.

## Regeneration

See `assets/sources/README.md` for the full regeneration procedure.

**Note on figure paths:** the English Markdown source uses repository-relative
figure paths and can be regenerated from the repository root.
