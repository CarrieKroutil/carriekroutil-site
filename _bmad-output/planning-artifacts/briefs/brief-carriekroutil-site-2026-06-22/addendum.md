# Addendum — carriekroutil.com brief

Depth captured during discovery that belongs downstream (PRD / architecture), not in the 1-2 page brief.

## Current technical state (brownfield, as of 2026-06-22)

The working site lives at `~/code/quickstart` (a Hugo "quick start" scaffold), NOT in the
`carriekroutil-site` repo (which currently holds only BMad tooling).

- **Generator:** Hugo static site, theme `ananke` (git submodule, see `.gitmodules`).
- **Current branding (mismatch):** `hugo.toml` has `baseURL = 'https://code.shebytes.io/'`
  and `title = 'Code - SheBytes.io'` — i.e. the site is currently branded for a *different*
  domain/brand (SheBytes / code), not carriekroutil.com. Needs resolving (see open questions).
- **Content:** 3 dummy quickstart posts in `content/posts/` (my-first/second/third-post.md).
  Front matter uses TOML (`+++`), fields: date, draft, title.
- **Add-a-post workflow today:** `hugo new content content/posts/<slug>.md` → edit markdown →
  `hugo build` (renders to `public/`) → commit/push.
- **Deploy (multiple half-configured paths — messy):**
  - A GitHub Action `.github/workflows/upload-S3.yaml` syncs `./public/` to S3 bucket
    `hugo-public-20251026` (us-east-1) on push to main.
  - Carrie described the host as **AWS Amplify + Route 53** (manual copy).
  - Leftover GCP App Engine artifacts also present: `app.yaml` (runtime go125), `main.go`,
    `.gcloudignore` — from following the Hugo quickstart tutorial.
  - => The deploy/hosting story is experimental and not yet settled on one approach.

## Open technical questions to resolve later
- Domain/brand: keep "SheBytes / code.shebytes.io" or fully rebrand to carriekroutil.com?
- Settle on ONE host/deploy pipeline (Amplify-builds-from-source vs CI-builds-and-syncs-to-S3).
- Should the live site code move INTO the `carriekroutil-site` repo, or stay in `quickstart`?

## Theme selection trail (for the build)
- Chosen: **Hextra** (https://themes.gohugo.io/themes/hextra/, repo imfing/hextra). Batteries-included,
  built-in FlexSearch + dark mode, installs via Hugo Modules, actively maintained into 2026.
  Posts + about for now; docs capability left unused but available.
- Considered & set aside:
  - PaperMod, Stack — Carrie found them, didn't like the look.
  - LoveIt — liked the look, but ~4-yr maintenance dormancy (mid-2021→early-2025) = bus-factor risk.
  - FixIt — actively-maintained LoveIt-lineage successor; viable fallback if Hextra disappoints.
  - Blowfish — strong modern alternative, not chosen.
  - Docsy — capable but needs Node/PostCSS toolchain; rejected as too much setup friction.
