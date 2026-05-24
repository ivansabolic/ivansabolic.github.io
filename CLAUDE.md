# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A personal academic website for Ivan Sabolić, built on the **al-folio** Jekyll theme and deployed to GitHub Pages at https://ivansabolic.github.io. Most of the repo is upstream theme code; the personalised content lives in `_pages/`, `_news/`, `_bibliography/papers.bib`, `_data/`, `assets/`, and `_config.yml`.

## Local development

Local builds require Ruby + Bundler with the gems in `Gemfile`, plus `imagemagick` on PATH (the `jekyll-imagemagick` plugin shells out to it).

```bash
bundle install                         # install gems
bundle exec jekyll serve --livereload  # dev server at http://localhost:4000
bundle exec jekyll build               # one-off build into _site/ (what CI runs)
```

Docker alternative (avoids local Ruby/imagemagick): `docker compose up` → http://localhost:8080. The container's `bin/entry_point.sh` runs `jekyll serve` and restarts it on `_config.yml` changes (other files use livereload).

There is **no test suite**. Verification = build succeeds + visually inspect the dev server. Formatting is enforced by Prettier (`.prettierrc` + `@shopify/prettier-plugin-liquid`) and a `prettier` GitHub Action — run `npx prettier --write .` before pushing if you touched `.liquid`/`.html`/`.md`/`.yml`.

## Deployment

Deploy is fully automated by `.github/workflows/deploy.yml`: on push to `main`, GitHub Actions builds with `JEKYLL_ENV=production`, runs `purgecss` against `purgecss.config.js`, and publishes `_site/` to the `gh-pages` branch via `JamesIves/github-pages-deploy-action`. **Do not commit to `gh-pages` manually** — it is force-pushed by CI. The `bin/deploy` script is the legacy manual-deploy path; prefer the workflow.

Note: the deploy workflow's path filter ignores `README.md`, `CONTRIBUTING.md`, `CUSTOMIZE.md`, `FAQ.md`, `INSTALL.md`, and `lighthouse_results/` — edits to those alone will not trigger a rebuild.

## Architecture and where content lives

This is a Jekyll site, so the directory layout *is* the architecture — the build assembles pages by combining collections, layouts, includes, and data files.

- **`_config.yml`** is the central control panel. It declares Jekyll plugins, the `books`/`news` collections, scholar/bibliography settings, archives, minifier, terser, imagemagick presets, and theme-level toggles. Changes here trigger a Jekyll restart in dev (handled by `entry_point.sh` in Docker) and need a server restart otherwise.
- **`_pages/`** — top-level pages (`about.md` is the homepage via `permalink: /`, plus `cv.md`, `publications.md`, `books.md`, `news.md`). Each declares a `layout:` and front-matter that switches features on/off (e.g. `selected_papers`, `social`, `announcements`).
- **`_layouts/*.liquid`** — page skeletons (`about`, `cv`, `bib`, `book-shelf`, `distill`, `page`, `post`, …). `default.liquid` is the base; others extend the pattern.
- **`_includes/*.liquid`** — reusable fragments injected by layouts (header, footer, social, news, selected_papers, citation, bib_search, etc.). The `cv/` and `resume/` subdirectories render the structured CV from `_data/cv.yml`.
- **`_sass/`** — SCSS partials compiled to CSS (set to `style: compressed` in config).
- **`_plugins/*.rb`** — custom Ruby plugins that run at build time (citation fetchers for Google Scholar / Inspire HEP, BibTeX hiding, cache-busting, downloading 3rd-party assets, etc.). These run on every build; failures here break the build.
- **`_data/`** — structured YAML driving generated sections: `cv.yml` (CV content), `coauthors.yml`, `repositories.yml`, `socials.yml`, `venues.yml`.
- **`_bibliography/papers.bib`** — publications, rendered by `jekyll-scholar` via `_layouts/bib.liquid`. The "Filter out certain bibtex entry keywords" list in `_config.yml` controls which custom fields (e.g. `selected`, `preview`, `pdf`, `arxiv`) are interpreted vs. shown raw.
- **`_news/`** — short announcement posts surfaced on the homepage via the `announcements` block in `about.md`.
- **`_books/`** — book-review collection rendered through `book-review.liquid` / `book-shelf.liquid`.
- **`assets/`** — static files. `assets/pdf/` holds CV/transcripts/research-statement PDFs that pages link to directly; `assets/img/` holds images (auto-converted to responsive WebP if `imagemagick:` is enabled in `_config.yml`).
- **`_scripts/*.liquid.js`** — analytics and search snippets templated through Liquid before being served as JS.

When changing how a page looks, the trail is usually: `_pages/<page>.md` → `layout:` field → `_layouts/<name>.liquid` → the `{% include %}` fragments it pulls from `_includes/`. Styling cascades from `_sass/` partials imported by `assets/css/main.scss`.

## Things worth knowing

- The `jekyll-scholar` plugin's bibliography setup is non-trivially configured under `scholar:` in `_config.yml` (style, filters, grouping). If publications render oddly, look there first, then at `_layouts/bib.liquid` and `_includes/citation.liquid`.
- `purgecss` runs in production but **not** in `jekyll serve` — CSS that works locally can be purged in deploy if it's only referenced from JS-generated DOM. The allowlist lives in `purgecss.config.js`.
- The site icon, title, analytics IDs, and most identity fields are in the top section of `_config.yml`; prefer editing config over hardcoding.
- `_pages/about_einstein.md` is excluded from the build (see `exclude:` in `_config.yml`) — it is theme demo content, not Ivan's about page.
- Reference docs from the upstream theme (`README.md`, `CUSTOMIZE.md`, `FAQ.md`, `INSTALL.md`) are excluded from the built site but useful for theme-feature questions.
