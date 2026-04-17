# Portfolio — Syed Aamir

Personal portfolio site for Syed Aamir, Data & AI Solutions Engineer (Dubai, UAE). Built with [MkDocs Material](https://squidfunk.github.io/mkdocs-material/).

**Live site:** https://syedaamir229.github.io/

## Local development

Dependencies are managed with [uv](https://docs.astral.sh/uv/).

```bash
uv sync
uv run mkdocs serve
```

The site is served at http://127.0.0.1:8000.

To build a static copy into `site/`:

```bash
uv run mkdocs build
```

On macOS, if you want social cards to render locally (they need cairo), run:

```bash
brew install cairo freetype libffi libjpeg libpng zlib pngquant
export DYLD_FALLBACK_LIBRARY_PATH=/opt/homebrew/lib
```

CI installs these automatically, so cards always generate on deploy.

## Deployment

[`.github/workflows/deploy.yml`](.github/workflows/deploy.yml) runs `uv run mkdocs gh-deploy` on every push to `main`, publishing to GitHub Pages.

## Structure

```
docs/            # All site content (home, portfolio, resume, blog, assets, stylesheets)
mkdocs.yml       # Site config (nav, theme, plugins, CSS)
pyproject.toml   # Python dependencies (authoritative; uv.lock pins versions)
sources/         # Reference PDFs and notes — gitignored, not deployed
```

Project context, branding rules, and conventions live in [CLAUDE.md](CLAUDE.md).
