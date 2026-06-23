# carriekroutil.com

Personal site — a Hugo static site using the [Hextra](https://github.com/imfing/hextra) theme, consumed via Hugo Modules. Authored in Markdown; published by `git push` to `main`, which AWS Amplify builds and serves at carriekroutil.com. For the deploy pipeline and one-time AWS setup, see **[SETUP.md](SETUP.md)**.

## Prerequisites

- **Hugo (extended edition)** ≥ 0.146.0 — the floor Hextra v0.12.3 requires. Check with `hugo version` (look for `+extended`).
- **Go** ≥ 1.21 — required by Hugo Modules to fetch the theme.

On macOS: `brew install hugo go`.

## Run locally

From the repo root:

```bash
# First time (or after a fresh clone / pull): fetch the Hextra theme module
hugo mod get

# Local preview WITH drafts + live reload — your day-to-day authoring loop
hugo server -D                # → http://localhost:1313/

# Preview the way production sees it (drafts hidden)
hugo server

# Full production build into ./public/
hugo --gc --minify
```

- `hugo server -D` shows `draft: true` content and hot-reloads on every save. Stop it with **Ctrl+C**.
- Plain `hugo` (no `-D`) excludes drafts — the same behavior the production build uses.
- `./public/` and `./resources/` are build output and are git-ignored.

## Project layout

```text
hugo.toml          # site config: baseURL, title, Hextra module import, theme/search/taxonomy
amplify.yml        # AWS Amplify build spec (single deploy pipeline) — see SETUP.md
go.mod / go.sum    # Hugo Module — Hextra theme version lock
content/           # Markdown content (content/_index.md is the home page)
layouts/           # project layout/partial overrides (shadow the theme; never edit the module)
assets/            # custom CSS/JS
static/            # favicon and other static files copied as-is
```

Customizations live in `layouts/` and `assets/`, which shadow the Hextra module by Hugo's lookup order — the theme module itself is treated as read-only and is never edited in place.
