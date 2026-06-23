---
baseline_commit: 7299385
---

# Story 2.3: Build the shared post-card partial

Status: review

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As a visitor,
I want each post previewed as a consistent, clickable card,
so that scanning posts feels coherent everywhere they appear.

*Implements the renderer for FR-2/FR-4/FR-8 · AR-9 (AD-6) · UX-DR4, UX-DR5, UX-DR13. Builds on Stories 2.1 (`post-meta`) and 2.2 (chip/tint CSS). First consumer is Story 2.4 (the stream); Home (Epic 3) and term pages (Epic 4) reuse it.*

## Acceptance Criteria

**AC1 — Card content + single click target**
**Given** the card design in the UX `home.html` mock
**When** `layouts/_partials/custom/post-card.html` is built
**Then** a card shows a thumb (the post's image, or a subtle gradient fallback when none), an uppercase accent tag, the title (weight 600), a one-line excerpt, and muted date + read-time
**And** the **entire card is a single click target** to the post.

**AC2 — Hover/focus affordance, reduced-motion honored**
**Given** a card
**When** hovered or focused
**Then** it shows a subtle lift (translateY ~3px + border brighten) and a visible focus ring
**And** under `prefers-reduced-motion` the hover lift is dropped (the media query is wired, not just intended).

**AC3 — One card renderer everywhere**
**Given** this is the one card renderer
**When** Home, the posts list, and tag pages need cards
**Then** they all call this same partial (the native `hextra-card` is not used for posts).

## Tasks / Subtasks

- [x] **Task 1 — Build the card partial** (AC: 1, 3)
  - [x] Created `layouts/_partials/custom/post-card.html`: single `<a class="ck-card" href="{{ .RelPermalink }}">` wraps the whole card.
  - [x] Thumb: hero image (`$.Resources.GetMatch .src`) as decorative `alt=""` + `loading="lazy"`; heroless → `<div class="ck-card-thumb ck-card-thumb--g{N}">` (gradient fallback, N = `1+mod(len .Title) 4` for variety).
  - [x] Tag label is a non-link `<span class="ck-tag ck-tag--{tint}">` (primary tag) reusing the 2.2 chip CSS — NOT `post-tags.html` (which emits anchors; would nest `<a>` inside `<a>`).
  - [x] `<h3 class="ck-card-title">`, excerpt `or .Description (.Summary | plainify)`, meta via `{{ partial "custom/post-meta.html" . }}`.
  - [x] No `hextra-card`; no stream/home/term layouts here.

- [x] **Task 2 — Card styles** (AC: 1, 2)
  - [x] Added `.ck-card`, `.ck-card-thumb` (+ `--g1..g4` gradients ported from the mock), `.ck-card-body`, `.ck-card-title` (600), `.ck-card-excerpt` (one-line clamp), card `.ck-post-meta` size to `assets/css/custom.css`. Reused tokens; module untouched.
  - [x] Lift `translateY(-3px)` + border brighten + transition sit **inside** `@media (prefers-reduced-motion: no-preference)`; focus ring from the global `:focus-visible`.

- [x] **Task 3 — Add a heroless reference post + verify (temporary harness)** (AC: 1, 2, 3)
  - [x] Added `content/posts/the-next-round/index.md` — genuine on-voice golf post, heroless, `tags: [golf]`, `description:` for the excerpt → exercises the gradient-thumb path (the `hello-…` post has a hero → image-thumb path).
  - [x] Verified via a temporary `layouts/blog/list.html` harness ranging `posts` RegularPages: 2 cards — `hello-…` image thumb (`hero.svg`, `alt=""`, lazy), `the-next-round` gradient thumb (`--g1`); tag spans `code`/`golf`; titles; one-line excerpts; `Jun 23, 2026` / `Jun 20, 2026` + read-time; each card links its post. Harness removed.
  - [x] Markup valid: 2 card anchors, **no nested `<a>`** (tag is `<span>`), checked programmatically.
  - [x] `prefers-reduced-motion` drops the lift (rules inside the `no-preference` query).
  - [x] `hugo --gc --minify` exit 0; no brand remnants.

- [x] **Task 4 — Make the typed-section routing coherent (relocate single)** (AC: 3)
  - [x] Root-caused the override-lookup split: `type: blog` on `_index.md` routes the **section list** via the `blog` type-slot, while individual posts (default type `posts`) route via the `posts` section-slot — so `posts/single.html` won but `posts/list.html` lost to Hextra's `blog/list.html`.
  - [x] Fix: added `cascade: { type: blog }` to `_index.md` so every post is type `blog`, and relocated the single layout `layouts/posts/single.html` → `layouts/blog/single.html`. Now BOTH single and (2.4's) list resolve to `layouts/blog/*`, shadowing the Hextra module — coherent, and faithful to AD-4's "type: blog activates the blog layout slot". URLs stay `/posts/{slug}/` (slug = bundle folder, not type). Re-verified the single page still renders.

## Dev Notes

### The anchor-in-anchor trap (key design constraint)
The whole card is a single `<a>` to the post (UX-DR5). HTML forbids a nested `<a>`, so the card's tag is a **non-interactive `<span>`** styled with the existing `.ck-tag`/`.ck-tag--{tint}` classes — NOT the `custom/post-tags.html` partial (which renders clickable anchors for the single page + term pages). Same visual chip, different element. This is the one intentional divergence from "always reuse the chip partial". [Source: UX-DR5 single-click-target; HTML content model]

### Reuse, don't duplicate
- Date + read-time: `{{ partial "custom/post-meta.html" . }}` (Story 2.1) — also keeps the date-gate live on every card render. [Source: layouts/_partials/custom/post-meta.html]
- Chip look: the `.ck-tag*` CSS from Story 2.2 — the card adds no new chip styles, only the `<span>` element. [Source: assets/css/custom.css Story 2.2 block]
- This partial is THE post renderer (AR-9): Home (3.2), stream (2.4), term pages (4.1) all call it — the native `hextra-card` is not used for posts. [Source: ARCHITECTURE-SPINE.md#AD-6]

### Thumb / image handling
- Hero present → decorative thumb image, `alt=""` + `loading="lazy"` (accessible name = the title inside the card link; no processing/resize — AD-9 defers it; SVG needs none).
- No hero → gradient-fallback thumb (CSS gradient div). The reference post `hello-…` has a hero (image path); the new heroless post exercises the gradient path. Both shipped in content so the card's two states are real, not hypothetical. [Source: UX-DR5 "no-image cards fall back to the gradient thumb"]

### Excerpt
One line. Prefer `.Description` (author-set), else `.Summary | plainify`; CSS clamps to a single line (`-webkit-line-clamp:1` / ellipsis). Keep it short — the card is a scan target, not the post. [Source: UX-DR5]

### Layout note (grid is the consumer's job)
The card partial renders ONE card and must be grid-agnostic (`display:block`, full width of its cell). The 2-up→1-up responsive grid (UX-DR10) is owned by the consumers: the stream (2.4, 920px content width) and Home (3.2). Don't bake a grid into the card. [Source: UX-DR10]

### Accessibility / motion (UX-DR11, NFR-2)
- Single focusable element per card (the anchor) → one tab stop, gets the global `2px #d946ef` `:focus-visible` ring. Verify the ring shows on keyboard focus.
- Lift transform + transition live ONLY inside `@media (prefers-reduced-motion: no-preference)` so reduced-motion users get a static card by construction. [Source: EXPERIENCE.md; existing Story 1.4 motion pattern in custom.css]
- Meta never below `#a0a0ab` (inherited from `.ck-post-meta`). Card excerpt muted but readable.

### Voice (UX-DR13) for the second post
Same register as the reference post — playful, cozy, first-person, never self-deprecating; golf = the next round (not being bad at it). Short and real. [Source: EXPERIENCE.md §Voice]

### Anti-patterns
- ❌ Nested `<a>` (chip link inside card link). ✅ `<span>` tag label on the card.
- ❌ Calling `custom/post-tags.html` inside the card. ✅ inline `<span class="ck-tag …">`.
- ❌ Hextra `hextra-card` for posts. ✅ our `post-card.html`.
- ❌ Baking a grid into the card. ✅ block-level card; grid is the consumer's.
- ❌ Duplicating date/read-time. ✅ `post-meta.html`.
- ❌ Hover lift without a reduced-motion guard. ✅ transform inside `no-preference`.

### Scope guardrails
OUT of scope: the styled `/posts/` stream (2.4 — but its temp harness here is fine to verify the card, then removed), Home grid (3.2), featured slot (3.3), term page (4.1). This story ships ONE thing: the reusable card partial + its CSS + the two-state content to prove it.

### References
- [Source: epics.md#Story 2.3: Build the shared post-card partial] — ACs
- [Source: ARCHITECTURE-SPINE.md#AD-6] — one post-card partial across Home/list/term; not hextra-card
- [Source: ux DESIGN.md UX-DR5 / EXPERIENCE.md] — card anatomy, single click target, gradient fallback, hover lift
- [Source: mockups/home.html] — card markup reference (the AD-6 card is ported from here)
- [Source: assets/css/custom.css] — `.ck-tag*` chips (2.2), `--ck-*` tokens, reduced-motion pattern (1.4)
- [Source: layouts/_partials/custom/post-meta.html] — shared date+read-time primitive

## Dev Agent Record

### Agent Model Used

claude-opus-4-8[1m] (Opus 4.8, 1M context)

### Debug Log References

- Temp `layouts/blog/list.html` harness → `public/posts/index.html`: 2 `<a class=ck-card>` anchors → `/posts/hello-corner-of-the-internet/`, `/posts/the-next-round/`; `<div class=ck-card-thumb><img src=…/hero.svg alt loading=lazy>`; `ck-card-thumb--g1` (heroless); `<span class="ck-tag ck-tag--fuchsia">code/golf</span>`; titles, excerpts, `ck-post-meta>Jun 23/20, 2026 · 1 min read`.
- Nested-anchor check: 2 cards, no inner `<a>` (valid HTML).
- After `cascade: {type: blog}` + relocate: `blog/single.html` renders the post (`ck-reading ck-post` present); final `hugo --gc --minify` exit 0; no remnants.

### Completion Notes List

- **AC1 met:** card shows thumb (hero image OR gradient fallback), uppercase accent tag (CSS-uppercased `<span>`), title (600), one-line excerpt, muted date + read-time; the whole card is one `<a>` → the post.
- **AC2 met:** hover lift `translateY(-3px)` + border brighten + transition gated by `prefers-reduced-motion: no-preference`; card anchor gets the global `2px #d946ef` focus ring.
- **AC3 met:** `post-card.html` is the one renderer; `hextra-card` not used for posts. Stream (2.4), Home (3.2), term (4.1) will all call it.
- **Anchor-in-anchor solved:** card tag is a `<span>` (not the anchor-emitting `post-tags.html`), so the single click target stays valid HTML.
- **Reuse:** `post-meta.html` for date/read-time (and the live date-gate); `.ck-tag*` CSS from 2.2 for the chip look; tokens for everything else.
- **Routing fix (also updates Story 2.2's footprint):** relocated `layouts/posts/single.html` → `layouts/blog/single.html` and cascaded `type: blog` so the whole post family resolves under `layouts/blog/`. This makes 2.4's stream override (`layouts/blog/list.html`) win cleanly. See Story 2.2 change log note.
- **Tint coincidence:** both sample tags ("code","golf") are length-4 → both fuchsia. Deterministic, harmless; more tag-length variety would spread tints. Not worth forcing.

### File List

- `layouts/_partials/custom/post-card.html` (new) — the shared post-card renderer
- `assets/css/custom.css` (modified) — card styles (`.ck-card*`, gradient thumbs, reduced-motion lift)
- `content/posts/the-next-round/index.md` (new) — heroless reference post (gradient-thumb path)
- `content/posts/_index.md` (modified) — added `cascade: { type: blog }`
- `layouts/blog/single.html` (renamed from `layouts/posts/single.html`) — relocated for coherent typed-section routing
- `_bmad-output/implementation-artifacts/sprint-status.yaml` (modified) — 2.3 → review

## Change Log

| Date | Change |
| --- | --- |
| 2026-06-23 | Story 2.3 drafted via create-story. Status → ready-for-dev. |
| 2026-06-23 | Story 2.3 implemented: `post-card.html` (image/gradient thumb, span tag, title, excerpt, meta; single click target; reduced-motion lift), card CSS, heroless reference post; relocated single layout to `layouts/blog/` + cascaded `type: blog` for coherent routing. All ACs verified. Status → review. |
