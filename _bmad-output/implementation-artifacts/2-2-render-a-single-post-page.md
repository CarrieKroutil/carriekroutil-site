---
baseline_commit: d3fdfcf
---

# Story 2.2: Render a single post page

Status: done

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As a visitor,
I want to read a full post with its prose, images, and code,
so that I get the whole piece in a comfortable, readable layout.

*Implements FR-5 (CAP-5) · AR-12 (AD-9), NFR-2. Builds on Story 2.1 (content model + `post-meta` partial).*

## Acceptance Criteria

**AC1 — Single post renders title, date, read-time, clickable tags, body at 720px**
**Given** a non-draft post at `/posts/{slug}/`
**When** opened
**Then** it renders the title, date, read-time, clickable tag chips, and the markdown body, at the `720px` reading width
**And** code blocks render with syntax highlighting and a way back to the Post Stream is present.

**AC2 — Page bundle images; hero via hero map; alt enforced by build**
**Given** a post authored as a Hugo page bundle
**When** it includes images
**Then** the hero comes from the `hero` map and inline images use standard markdown, with images beside the markdown in the bundle
**And** every content image carries alt text — a `render-image.html` markup hook **fails the build** on an empty alt; decorative gradient thumbs use `alt=""`.

**AC3 — Drafts excluded from production**
**Given** a post marked `draft: true`
**When** the site builds for production
**Then** the post does not appear at its Post Page, in the Post Stream, or in build output.

**AC4 — Lazy-load + no-JS core**
**Given** images on the page
**When** the page loads
**Then** images lazy-load and the core post content reads as static HTML with JavaScript disabled.

## Tasks / Subtasks

- [x] **Task 1 — Reusable clickable tag-chip partial** (AC: 1)
  - [x] Created `layouts/_partials/custom/post-tags.html` using `.GetTerms "tags"` → each chip links the real `/tags/{topic}/` term page; accent tint rotates violet/fuchsia/amber by `mod 3`.
  - [x] Accepts a bare Page OR `{page, limit}` (card passes `limit 1`). THE chip renderer — reused by 2.3 + Epic 4.

- [x] **Task 2 — Single post layout override** (AC: 1, 2, 4)
  - [x] Created `layouts/posts/single.html` (`define "main"`); `<main id="content">` keeps the skip-link target. 720px column. Order: back-link → chips → h1 → `post-meta` → hero → `<div class="content">.Content</div>` → foot back-link.
  - [x] Back-link `← all posts` → `/posts/` (real `<a>`).
  - [x] Hero: `with .Params.hero` → `errorf` if alt blank; resolves `.Resources.GetMatch .src`; `<img loading="lazy">`.
  - [x] Kept `<div class="content">` wrapper → inherits Hextra prose styling. Chroma highlighting renders (Monokai inline styles).
  - [x] One `<h1>` (title); body uses `##` — verified h1 count = 1.

- [x] **Task 3 — Inline-image alt enforcement hook** (AC: 2, 4)
  - [x] Created `layouts/_default/_markup/render-image.html`: empty/whitespace alt → `errorf` (build fails); resolves bundle src via `.Page.Resources.GetMatch`; `loading="lazy"` on every image. Gradient-thumb `alt=""` exception lives in the card (2.3), not markdown — so inline content images always require non-empty alt.

- [x] **Task 4 — Single-page styles** (AC: 1, 2, 4)
  - [x] Appended to `assets/css/custom.css`: `.ck-reading` (720px), `.ck-back`, `.ck-tags`/`.ck-tag` + three accent tints (dimmed accent borders, uppercase `.72rem`/700/`.04em`), `.ck-post-title`, `.ck-post-meta` (muted, ≥`#a0a0ab`), `.ck-hero` (18px radius, responsive). Reused existing tokens; module untouched.
  - [x] No new motion introduced beyond the existing reduced-motion-gated transitions; chips/links use the global `:focus-visible` ring.

- [x] **Task 5 — Enrich the reference post + verify** (AC: 1, 2, 3, 4)
  - [x] Added `content/posts/hello-corner-of-the-internet/hero.svg` (on-palette abstract gradient SVG, git-friendly, no binary/fake photo) + `hero: {src, alt}`; added a fenced `sh` code block to the body (exercises highlighting).
  - [x] Build exit 0; post renders title, `Jun 23, 2026 · 1 min read`, chips → `/tags/code/` + `/tags/ai/`, `<img class=ck-hero … alt="…" loading=lazy>`, Chroma-highlighted code, `← all posts`.
  - [x] Reading width: `<main class="ck-reading ck-post">` (max-width `--ck-reading-max` = 720px).
  - [x] Alt gates: inline `![](hero.svg)` (empty alt) → build exit 1 via `render-image.html`; blank hero `alt` → build exit 1 via single-layout guard. Both restored.
  - [x] Drafts: `draft: true` → absent from `public/`. Restored.
  - [x] No-JS: full prose/title/meta/tags present as static HTML; term pages `/tags/code/` + `/tags/ai/` generated (Hugo core; styled in Epic 4) so chip links are live.

## Dev Notes

### Hextra override mechanics (verified)
- `layouts/baseof.html` (Hextra) calls `{{ block "main" . }}` directly in `<body>` after navbar; footer + `scripts.html` follow. So our `layouts/posts/single.html` only defines `main` and inherits the Epic-1 header/footer. [Source: hextra@v0.12.3/layouts/baseof.html]
- Hextra's own `blog/single.html` wraps the body in `<div class="content">` and uses `partial "utils/format-date"`; we replace the whole layout but **keep the `<div class="content">` wrapper** to inherit Hextra's prose/markdown/code styling rather than reinventing it. We do NOT reuse Hextra's `hx:`-prefixed Tailwind utility classes (no Tailwind in our pipeline) — our own `ck-` classes + tokens instead. [Source: hextra@v0.12.3/layouts/blog/single.html]
- Lookup: section `posts` routes to `layouts/posts/single.html` (proven by the Story 2.1 temporary harness). `_index.md`'s `type: blog` is the section contract (AD-4); our `posts/` overrides take precedence for our surfaces.
- `id="content"` must be on our `<main>` — baseof's skip-link targets `#content`. [Source: hextra baseof skip-link]

### The metadata + chip primitives are shared — reuse, don't duplicate
- Date + read-time: call `{{ partial "custom/post-meta.html" . }}` from Story 2.1 (it also carries the missing-date build-gate — so this story is what makes that gate a live standing guard, as designed in 2.1). [Source: 2-1-*.md; layouts/_partials/custom/post-meta.html]
- Tag chips: the new `custom/post-tags.html` is THE chip renderer for single (all tags), card (primary tag, 2.3), and term pages (Epic 4). [Source: AR-9 single-renderer philosophy; UX-DR4]

### Images / alt enforcement (AD-9)
- Page bundle: images beside `index.md`; hero via the `hero` map (template-rendered → alt guarded in `single.html`); inline via markdown (→ alt guarded in `render-image.html`). The two guards together satisfy "alt enforced, build fails on empty". [Source: ARCHITECTURE-SPINE.md#AD-9]
- Processing (resize/responsive/WebP) is deferred — do NOT add `.Resize`/`.Process`; just `loading="lazy"` native. An SVG hero needs no processing anyway. [Source: AD-9 "processing deferred"]
- `decorative gradient thumbs use alt=""` — that exception is the card's gradient fallback (2.3), not a markdown image. The inline-image hook enforces non-empty alt for content images.

### Layout / a11y / reading width (UX-DR10, UX-DR11, NFR-2)
- Single post + about = `720px` reading-max (`--ck-reading-max`, already a token); home/list/term = `920px` content-max. [Source: DESIGN.md §Layout; assets/css/custom.css]
- Meta/caption never below `#a0a0ab` on dark (token `--ck-muted`/`--ck-meta-min`). One `<h1>`/page; visible `2px #d946ef` focus ring on `:focus-visible` (already global from Epic 1 — verify chips/back-link inherit it); honor `prefers-reduced-motion`. [Source: EXPERIENCE.md a11y floor; DESIGN.md]

### Visual reference (mockup `post-single.html` — spine still wins)
Order: back-link `← all posts` → tag chips (uppercase, first violet / others rotate, fuchsia variant `.tag.f`) → `<h1>` → meta `June 22, 2026 · 6 min read` (we use `Jan 2, 2006`) → optional hero → prose (p `1.08rem`, h2 `1.4rem`, `pre` surface-2 bg + border radius 12, blockquote fuchsia left-border). Reading column `.wrap{max-width:720px;margin:0 auto;padding:0 22px}`. Footer accent rule is the Epic-1 footer (already shipped) — don't re-add. [Source: mockups/post-single.html]

### Scope guardrails
OUT of scope (later stories): post-card partial → 2.3; styled `/posts/` stream list → 2.4; styled `/tags/{topic}/` term page → Epic 4 (the chip links resolve to Hugo's default term page until then — that's fine and expected). Prev/next post pager is NOT required by the ACs (only a back-to-stream link) — skip it to avoid scope creep.

### Anti-patterns
- ❌ Reinventing prose/code CSS. ✅ Keep `<div class="content">` to inherit Hextra prose.
- ❌ Tailwind `hx:` classes in our override. ✅ `ck-` classes + tokens.
- ❌ Duplicating date/read-time or chip logic. ✅ `post-meta.html` + `post-tags.html`.
- ❌ A second `<h1>` from body markdown. ✅ Title is the only h1.
- ❌ Image `.Resize`/processing. ✅ `loading="lazy"` only.
- ❌ Dropping `id="content"` from `<main>`. ✅ Keep it (skip-link a11y).

### Previous-story intelligence
- Story 2.1 shipped `post-meta.html` (date guard, `Jan 2, 2006`, read-time floor 1) and a heroless seed post; 2.2 adds the hero + makes the date-gate live by wiring `post-meta` into `single.html`. [Source: 2-1-*.md]
- `assets/css/custom.css` already defines all needed tokens (`--ck-reading-max` 720, chip tints, `--ck-muted`, radii, focus ring). Reuse them. [Source: assets/css/custom.css]

### References
- [Source: epics.md#Story 2.2: Render a single post page] — ACs
- [Source: ARCHITECTURE-SPINE.md#AD-9] — page bundles, hero map, alt-enforcement hook, lazy-load, processing deferred
- [Source: ARCHITECTURE-SPINE.md#AD-5/AD-6] — overrides only; single renderer philosophy
- [Source: DESIGN.md §Layout; EXPERIENCE.md §Accessibility] — 720px reading width, a11y floor
- [Source: mockups/post-single.html] — single-post visual structure (date format from spine)
- [Source: hextra@v0.12.3/layouts/baseof.html, blog/single.html] — block + prose-wrapper mechanics

## Dev Agent Record

### Agent Model Used

claude-opus-4-8[1m] (Opus 4.8, 1M context)

### Debug Log References

- Rendered post markers: `ck-post-meta>Jun 23, 2026 &#183; 1 min read`; chips `ck-tag--violet href=/tags/code/`, `ck-tag--fuchsia href=/tags/ai/`; `<img class=ck-hero src=/posts/…/hero.svg alt="…" loading=lazy>`; `class="highlight"` + Chroma `<pre>` inline styles; h1 count = 1; `<main class="ck-reading ck-post">`.
- Empty inline alt → `hugo` exit 1. Blank hero alt → `hugo` exit 1. `draft: true` → absent from `public/`. Final clean `hugo --gc --minify` exit 0.
- `/tags/code/`, `/tags/ai/` term pages present (Hugo core).

### Completion Notes List

- **AC1 met:** single page renders title, `Jan 2, 2006` date + read-time (via `post-meta`), clickable chips (real `/tags/{topic}/` links), markdown body at 720px; Chroma syntax highlighting; `← all posts` back-link (top + foot).
- **AC2 met:** hero via the `hero` map (bundle resource), inline images via markdown; `render-image.html` fails the build on empty alt; the single-layout guard fails the build on blank hero alt. Images live beside `index.md` in the bundle.
- **AC3 met:** `draft: true` → excluded from `public/` (page + build).
- **AC4 met:** every image carries `loading="lazy"`; core post reads as static HTML with JS off.
- **This story makes the Story 2.1 missing-date gate a live standing guard** — `single.html` now calls `post-meta`, so any post missing a `date` fails the production build.
- **Reuse:** kept Hextra's `<div class="content">` prose wrapper (no reinvented prose CSS); `post-meta` + `post-tags` are the shared primitives (no duplication in 2.3/Epic 4).
- **Known minor (flag for review):** Chroma renders with its default Monokai inline styles (`bg #272822`), which is functional but slightly off our `--ck-surface-2` palette. Spine only requires "syntax highlighting" — left as-is; a class-based Chroma stylesheet could align it to the palette later if desired.
- **Chip label casing:** `.LinkTitle` title-cases ("Code"/"Ai"); CSS `text-transform: uppercase` renders them "CODE"/"AI" (matches the mock). Term-page URLs stay lowercase (`/tags/ai/`).
- **Scope held:** no post-card (2.3), no styled stream/term page (2.4/Epic 4), no prev/next pager (not in ACs).

### File List

- `layouts/_partials/custom/post-tags.html` (new) — reusable clickable topic-chip renderer
- `layouts/posts/single.html` (new) — single post page (720px), wires `post-meta` (makes 2.1 date-gate live) + hero alt guard
- `layouts/_default/_markup/render-image.html` (new) — inline-image alt enforcement + lazy-load
- `assets/css/custom.css` (modified) — single-post + chip styles (`.ck-reading`, `.ck-tag*`, `.ck-post-*`, `.ck-hero`)
- `content/posts/hello-corner-of-the-internet/index.md` (modified) — added hero front matter + code block
- `content/posts/hello-corner-of-the-internet/hero.svg` (new) — on-palette gradient hero (git-friendly SVG)
- `_bmad-output/implementation-artifacts/sprint-status.yaml` (modified) — 2.2 → review

## Change Log

| Date | Change |
| --- | --- |
| 2026-06-23 | Story 2.2 drafted via create-story. Status → ready-for-dev. |
| 2026-06-23 | Story 2.2 implemented: `posts/single.html` (720px, chips, meta, hero), `post-tags.html`, `render-image.html` alt gate, single-post CSS, enriched reference post. All ACs verified. Status → review. |
| 2026-06-23 | Follow-up during Story 2.3: single layout briefly relocated to `layouts/blog/single.html` + `cascade: type: blog` to make the typed-section list resolve to our override. |
| 2026-06-23 | Post-merge-review pivot (Option B, Carrie): reverted that — dropped `type: blog`/cascade and moved the layout BACK to `layouts/posts/single.html`. Hugo routes by section name `posts`; same `/posts/` URLs, no `blog`-named folder. Single re-verified green (chips, hero, alt gate, highlight). See AD-4 (revised) + Story 2.1 notes. |
