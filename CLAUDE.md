# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Marketing/brochure site for Pump to Go (pumptogo.co.uk), a mobile tyre-fitting service. It's a single-page Hugo site built on the `hugo-scroll` theme, vendored directly under `themes/hugo-scroll` (not a git submodule — `.gitmodules` is empty, so treat that theme directory as third-party code, not something to hand-edit for site content).

## Commands

- Build: `npm run build` (runs `hugo --minify --gc`). Requires the extended Hugo binary — see `devDependencies.hugo-extended` in `package.json` (pinned to `0.147.9`, matching `HUGO_VERSION` in `netlify.toml`) for the version to use if installing Hugo locally.
- Local preview: `hugo server` (no npm script defined; run the Hugo binary directly).
- No lint or test commands/config exist in this repo.

## Deployment

- **Netlify** is the primary host: `netlify.toml` runs `npm install && npm run build` and publishes `public/`.
- A separate **`github-pages`** branch exists for GitHub Pages hosting (currently identical to `master`). `CNAME` pins the custom domain (`pumptogo.co.uk`).
- The Hugo build output in `public/` is committed to the repo (not gitignored) — after changing content/theme, expect to rebuild and commit `public/` if that branch is meant to serve the built site directly.

## Site structure

- `config.toml` — Hugo site config. Site copy (title, description, contact info, meta keywords, social links) lives under `[params]` and `[params.contact]` here, not hardcoded in templates.
- `content/_index.md` — front matter for the home page (header image/video toggle, logo, nav entries). Has no body; it just configures the page shell.
- `content/homepage/*.md` — this is a **page bundle**; each file is one section of the single-page scroll layout, ordered by its `weight` front-matter field (e.g. `opener.md` weight 1, `about-us.md` weight 4, `contact.md` weight 5). `header_menu: true` puts a section in the nav. Add a new home page section with `hugo new homepage/my-section.md`.
- `content/legal.md`, `content/price-list.md`, `content/contact.md`, `content/service-quote.md` — standalone pages outside the home-page scroll (linked to individually, e.g. `/legal`, `/price-list`).
- `layouts/partials/` and `layouts/shortcodes/` — project-level overrides/additions layered on top of the theme's own `layouts/`. `custom_head.html` is the theme's designated hook for injecting extra `<head>` content/CSS; `custom_header_video.html` supplies the optional video header source.
- `layouts/shortcodes/tyreQuoteForm.html` — a self-contained HTML/JS quote calculator invoked via `{{< tyreQuoteForm >}}` in `content/service-quote.md`. Pricing logic is duplicated here separately from the table in `content/price-list.md` — if prices change, update both.
- `assets/css/custom.css` — small custom stylesheet; theme CSS variables (colors, list icons, etc.) can also be overridden inline via `custom_head.html` per the theme's README.
- `static/images/` — logo, favicons, and other static image assets referenced by `config.toml` and content front matter.

## Content conventions

- Icons in markdown use the theme's shortcode: `` {{<icon class="fa fa-envelope">}} `` (Fork Awesome icon set).
- Contact details are pulled from `config.toml`'s `[params.contact]` via shortcodes like `{{< email >}}`, `{{< phone >}}`, `{{< whatsapp >}}`, `{{< facebook >}}`, `{{< instagram >}}` rather than being typed literally into page content.
