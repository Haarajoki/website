# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Static website for Haarajoen asukasyhdistys ry (a Finnish neighborhood association in Järvenpää). Built with Hugo, styled with a custom theme, content-managed via Decap CMS, deployed to GitHub Pages.

## Build & Dev Commands

```bash
hugo                    # Build site to public/
hugo --minify           # Production build (used in CI)
hugo server             # Dev server at localhost:1313 with live reload
hugo server -D          # Dev server including draft content
```

Hugo version: 0.152.2 extended. No npm/node dependencies.

## Architecture

**Hugo site with custom theme** (`themes/haarajoki/`):

- `hugo.toml` — Site config, menus (main nav: 4 items, footer nav: 4 secondary pages), params
- `themes/haarajoki/layouts/` — All templates:
  - `_default/baseof.html` — Base layout: header with sticky frosted-glass nav, footer with two nav columns, mobile hamburger menu, scroll-reveal JS, Google Fonts (Cormorant Garamond + DM Sans)
  - `index.html` — Home: full-bleed hero section with SVG decorations and wave divider, intro text, latest 5 news cards
  - `_default/single.html` — Article/page template with breadcrumbs for news items
  - `_default/list.html` — Section listing (news archive)
- `themes/haarajoki/static/css/style.css` — Single CSS file, no preprocessor. Uses CSS custom properties. Design: Nordic nature editorial (forest greens, warm parchment, golden amber accents)
- `static/favicon.svg` — SVG favicon with river-fork motif

**Content structure** (`content/`):

- `_index.md` — Home page (title + subtitle in frontmatter)
- `ajankohtaista/` — News section. Posts use `YYYY-MM-DD-slug.md` naming. Frontmatter: title, date, summary (optional)
- Root-level `.md` files — Static pages (yhdistys, werso, yhteystiedot, etc.)
- Menu assignment: main nav pages configured in `hugo.toml`, secondary pages use `menu: footer` in their own frontmatter

**CMS** (`static/admin/`):

- Decap CMS v3 loaded from CDN. Config in `static/admin/config.yml`
- GitHub backend (OAuth). Repo: `haarajoki/website.github.io`
- Two collections: "Ajankohtaista" (folder-based, create new) and "Sivut" (file-based, 8 named pages)
- Media uploads go to `static/images/uploads/`

**Deployment** (`.github/workflows/deploy.yml`):

- Push to `main` → GitHub Actions builds with `hugo --minify` → deploys to GitHub Pages
- Uses `peaceiris/actions-hugo@v3` and `actions/deploy-pages@v4`

## Key Conventions

- All content is in Finnish
- Markdown rendering has `unsafe: true` enabled (allows raw HTML in content)
- The `public/` directory is gitignored — always clean-build with `rm -rf public && hugo` to avoid stale output
- Logo mark is an inline SVG river-fork motif (references "Haarajoki" = fork river), duplicated in baseof.html header and footer
