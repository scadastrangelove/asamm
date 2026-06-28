# PDF Source Files

This directory contains the monolith Markdown sources and CSS used to generate
the PDF versions of Agentic SAMM. These are the authoritative sources for PDF
regeneration.

## Files

| File | Description |
|---|---|
| `agentic-samm-full.md` | English monolith (all parts in one file, with frontmatter) |
| `agentic-samm-zh-CN.md` | Simplified Chinese community translation draft for v0.5.0-draft |
| `ASAMM-zh-CN-glossary.md` | zh-CN terminology glossary, aligned where possible with OWASP China translations |
| `agentic-samm-ru.md` | Russian version (legacy v0.2 translation; pending v0.5 sync) |
| `agentic-samm-style.css` | Shared CSS for PDF rendering |
| `agentic-samm-style.zh-CN.css` | CJK-capable CSS override for the zh-CN PDF |

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
weasyprint.HTML(filename='/tmp/agentic-samm-full.html', base_url='.').write_pdf(
    'agentic-samm-review.pdf',
    stylesheets=[weasyprint.CSS(filename='assets/sources/agentic-samm-style.css')]
)
"

# Simplified Chinese community translation draft:
pandoc assets/sources/agentic-samm-zh-CN.md \
  --from markdown+smart --to html5 --standalone \
  --css assets/sources/agentic-samm-style.zh-CN.css --no-highlight \
  -o /tmp/agentic-samm-zh-CN.html

python3 -c "
import weasyprint
weasyprint.HTML(filename='/tmp/agentic-samm-zh-CN.html', base_url='.').write_pdf(
    'assets/asamm-zh-CN.pdf',
    stylesheets=[weasyprint.CSS(filename='assets/sources/agentic-samm-style.zh-CN.css')]
)
"

# Legacy Russian v0.2 artifact only; do not use for v0.5 review:
pandoc assets/sources/agentic-samm-ru.md \
  --from markdown+smart --to html5 --standalone \
  --css assets/sources/agentic-samm-style.css --no-highlight \
  -o /tmp/agentic-samm-ru.html

python3 -c "
import weasyprint
weasyprint.HTML(filename='/tmp/agentic-samm-ru.html', base_url='.').write_pdf(
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

The Markdown sources use repository-relative figure paths. Run the commands
from the repository root so `assets/figures/` resolves correctly.
