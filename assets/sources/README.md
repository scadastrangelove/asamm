# PDF Source Files

This directory contains the monolith Markdown sources and CSS used to generate
the PDF versions of Agentic SAMM. These are the authoritative sources for PDF
regeneration.

## Files

| File | Description |
|---|---|
| `agentic-samm-full.md` | English monolith (all parts in one file, with frontmatter) |
| `agentic-samm-ru.md` | Russian version (includes Executive Summary, Glossary, GOST mapping) |
| `agentic-samm-style.css` | Shared CSS for PDF rendering |

## Regeneration

```bash
# Step 1: HTML via pandoc (run from repo root)
pandoc assets/sources/agentic-samm-full.md \
  --from markdown+smart --to html5 --standalone \
  --css assets/sources/agentic-samm-style.css --no-highlight \
  -o /tmp/agentic-samm-full.html

# Step 2: PDF via weasyprint
python3 -c "
import weasyprint
weasyprint.HTML(filename='/tmp/agentic-samm-full.html').write_pdf(
    'agentic-samm-review.pdf',
    stylesheets=[weasyprint.CSS(filename='assets/sources/agentic-samm-style.css')]
)
"

# Repeat for Russian:
pandoc assets/sources/agentic-samm-ru.md \
  --from markdown+smart --to html5 --standalone \
  --css assets/sources/agentic-samm-style.css --no-highlight \
  -o /tmp/agentic-samm-ru.html

python3 -c "
import weasyprint
weasyprint.HTML(filename='/tmp/agentic-samm-ru.html').write_pdf(
    'assets/asamm-ru.pdf',
    stylesheets=[weasyprint.CSS(filename='assets/sources/agentic-samm-style.css')]
)
"
```

## Dependencies

```bash
apt install pandoc
pip install weasyprint
```

Figure paths in the Markdown files use `file:///home/claude/asamm_v02/assets/figures/`.
Adjust to your local repo path before rendering.
