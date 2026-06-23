---
baseline_commit: 4ed9de1
---

# Story 2.4: Browse the full post stream at /posts/

Status: done

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As a visitor,
I want a single page listing every post newest-first,
so that I can scan everything Carrie has written.

*Implements FR-4 (CAP-4) · AR-9 (AD-4, AD-6). Builds on Stories 2.1 and 2.3. Final story of Epic 2.*

## Acceptance Criteria

**AC1 — Full reverse-chron stream of cards at 920px**
**Given** the blog `list.html` is overridden to call the `post-card` partial
**When** a visitor opens `/posts/`
**Then** every non-draft post is listed reverse-chronologically as cards at the `920px` content width, each linking its Post Page and showing title, date, and topic(s).

**AC2 — Tag chips link to term pages; no client-side filtering**
**Given** posts carry topic tags
**When** a visitor wants a single topic
**Then** the tag chips link to `/tags/{topic}/` (the styled term page is delivered in Epic 4); `/posts/` itself stays the full reverse-chron stream with no in-place client-side filtering.

## Tasks / Subtasks

- [x] **Task 1 — Resolve UX-DR5 (single click target) vs AC2 (tag chips link): the overlay card pattern** (AC: 1, 2)
  - [x] Evolved `post-card.html` to the overlay pattern: root is `<article class="ck-card">` (relative); title `<a class="ck-card-link">` whose `::after` (inset:0) makes the whole card the click target → post; tag via `{{ partial "custom/post-tags.html" (dict "page" . "limit" 1) }}` — a real `<a>` to `/tags/{topic}/`, raised above the overlay (`.ck-tags { z-index:1 }`).
  - [x] Both ACs hold: whole card → post (UX-DR5), tag → term page (AC2). Two real anchors, both keyboard-focusable; lift now also fires on `:focus-within`. Supersedes 2.3's `<span>` tag (forward-noted in 2.3).

- [x] **Task 2 — The stream layout** (AC: 1, 2)
  - [x] Created `layouts/blog/list.html` (`define "main"`, `<main id="content" class="ck-stream">`). Resolves here via the 2.3 `cascade: type: blog`; fully replaces Hextra's blog/list (no pager / Read-more / sidebar).
  - [x] 920px content width + 22px gutters; optional `_index.md` title/intro. Ranges `(where site.RegularPages "Section" "posts").ByDate.Reverse` → `post-card.html`. No client-side filtering.

- [x] **Task 3 — Grid styles** (AC: 1)
  - [x] Added `.ck-stream`/`.ck-grid` to `assets/css/custom.css`: 2-up grid (`1fr 1fr`, gap 16) at 920px, collapsing to 1-up under `max-width: 620px` (mock breakpoint).

- [x] **Task 4 — Verify** (AC: 1, 2)
  - [x] Build exit 0; `/posts/` lists both posts as cards newest-first: `hello-…` (2026-06-23) then `the-next-round` (2026-06-20).
  - [x] Each card → its post (`ck-card-link href=/posts/{slug}/`); tag chips → term pages (`/tags/code/`, `/tags/golf/`).
  - [x] `ck-stream` (920px) + `ck-grid` (2-up; `@media (max-width:620px)` → 1-up) present.
  - [x] No-JS: cards + chips are real `<a>`s; no `hextra-card`/"Read more" leakage (override replaces `blog/list.html`).
  - [x] Draft exclusion: `the-next-round` set `draft: true` → 0 occurrences in the stream; restored. No brand remnants.

- [x] **Task 5 — Polish: stable per-topic chip tint** (AC: 1)
  - [x] Changed `post-tags.html` tint from loop-index to `mod (hash.FNV32a $t.LinkTitle) 3` so a topic keeps ONE accent color everywhere (card + single + term). Fixes the all-violet card-tag monotony that index-0 tinting produced under `limit 1`. Verified: `code`=amber (card + single page agree), `golf`=violet, `ai`=violet — varied and consistent. `hash.FNV32a` confirmed available on Hugo 0.151.

## Dev Notes

### The central decision: UX-DR5 vs AC2 → overlay card (satisfies both)
- UX-DR5: "entire card is one click target". Story 2.4 AC2: "the tag chips link to /tags/{topic}/". A plain `<a class="card">` (2.3) forces the tag to be a non-link `<span>` (no `<a>` in `<a>`), which fails AC2 on the stream.
- Resolution: the **pseudo-content overlay** pattern — card is an `<article>`; the title link's `::after` overlays the whole card (post is the primary, full-surface target); the tag is a real `<a>` raised above the overlay (term page is the secondary target). This is the standard accessible "clickable card with a secondary link" solution and satisfies UX-DR5 *and* AC2 with no client-side JS. [Source: UX-DR5; epics.md#Story 2.4 AC2; W3C/inclusive-components card pattern]
- Consequence: the card reuses `custom/post-tags.html` (limit 1) — so the chip renderer is now genuinely shared across single page, card, and (Epic 4) term page. This is the 2.3 `<span>` approach evolving; documented in both story records.

### Layout (UX-DR10)
- Stream = `920px` content-max (home/index/list width), `22px` gutters. Cards 2-up, collapsing to 1-up under ~620px (mock breakpoint). The grid is owned here, not in the card (the card stays block-level/grid-agnostic). [Source: DESIGN.md §Layout; mockups/home.html `.grid`]

### Stream content (AD-4 / AD-6)
- `/posts/` is the full reverse-chron stream — every non-draft post, no curation, no JS filter. Topic narrowing = navigate to `/tags/{topic}/` (term page styled in Epic 4; links are live now via Hugo core). [Source: ARCHITECTURE-SPINE.md#AD-4, AD-8]
- Override fully replaces Hextra's `blog/list.html` (pager, "Read more →", sidebar) with our card grid. Resolves at `layouts/blog/list.html` because of the 2.3 `cascade: type: blog`. [Source: 2-3-*.md routing fix]

### Reuse / no-duplication
- Cards: `custom/post-card.html` (2.3, evolved here). Date/read-time: `custom/post-meta.html` (2.1). Chips: `custom/post-tags.html` (2.2). The stream layout itself adds only the grid + page frame. [Source: AR-9]

### Pagination
- Two posts now; no pager needed. If the catalog grows, a pager can be added later — NOT required by the ACs and out of scope here (keep the full list simple). Document if Hugo's default pagination would otherwise truncate (it won't at this size).

### Anti-patterns
- ❌ Re-deriving cards/meta/chips. ✅ Call the shared partials.
- ❌ Client-side JS filtering of the stream. ✅ Full static list; topic via term-page links.
- ❌ Leaving Hextra's `blog/list.html` (pager/Read-more) in place. ✅ Full override at `layouts/blog/list.html`.
- ❌ Grid baked into the card. ✅ Grid in the stream container.
- ❌ Tag as a non-link on the stream (fails AC2). ✅ Overlay card → tag is a real term-page link.

### Scope guardrails
OUT of scope: styled `/tags/{topic}/` term page (Epic 4.1 — links resolve to Hugo's default term page until then), header search (4.2), Home grid/hero/featured (Epic 3). This story ships the stream page + the overlay-card evolution + the grid.

### Previous-story intelligence
- 2.3 shipped `post-card.html` (single-`<a>` + `<span>` tag) and relocated layouts to `layouts/blog/` with `cascade: type: blog`. 2.4 evolves the card to the overlay pattern and adds the real `blog/list.html` stream. [Source: 2-3-*.md]
- Two reference posts exist: `hello-corner-of-the-internet` (hero, 2026-06-23) and `the-next-round` (heroless, 2026-06-20) — the stream shows both, newest first. [Source: content/posts/]

### References
- [Source: epics.md#Story 2.4: Browse the full post stream at /posts/] — ACs
- [Source: ARCHITECTURE-SPINE.md#AD-4, AD-6, AD-8] — type:blog section, one card renderer, term-page (no JS) topic browse
- [Source: DESIGN.md §Layout; mockups/home.html] — 920px content width, 2-up→1-up grid breakpoint
- [Source: 2-1-*.md, 2-2-*.md, 2-3-*.md] — the shared partials this story composes

## Dev Agent Record

### Agent Model Used

claude-opus-4-8[1m] (Opus 4.8, 1M context)

### Debug Log References

- `/posts/index.html`: `class=ck-stream` + `class=ck-grid`; `ck-card-link href=/posts/hello-corner-of-the-internet/` then `…/the-next-round/` (date-desc); tag chips `ck-tag--amber href=/tags/code/`, `ck-tag--violet href=/tags/golf/`.
- `ck-card-link::after` overlay rule present (whole-card click target). No `Read more` / `hextra-card` in output.
- `the-next-round` `draft: true` → 0 occurrences in `/posts/`. Final `hugo --gc --minify` exit 0; no remnants.
- `hash.FNV32a` probe via warnf confirmed available; tints deterministic.

### Completion Notes List

- **AC1 met:** `/posts/` lists every non-draft post reverse-chron as shared `post-card`s at 920px content width, 2-up grid (1-up under 620px); each card links its post and shows title, date, topic.
- **AC2 met:** tag chips are real `<a>` links to `/tags/{topic}/`; `/posts/` is the full stream with no client-side filtering (topic narrowing = navigate to a term page; Hugo core generates them, Epic 4 styles them).
- **Central decision — overlay card:** resolved UX-DR5 (single click target) vs AC2 (tag chips link) with the accessible pseudo-content overlay. Card → post via the title link's `::after`; tag → term page as a raised real link. No JS. This evolved the shared `post-card.html` (2.3 `<span>` → 2.4 overlay), so it now reuses `post-tags.html` too — single page, card, and term page share both the card and chip renderers.
- **Per-topic tint:** a topic's accent color is now stable site-wide (hash of name), improving recognizability and card variety.
- **Override completeness:** `layouts/blog/list.html` fully replaces Hextra's blog list (no pager/Read-more/sidebar). Pagination intentionally omitted (2 posts; not an AC) — add later if the catalog grows.
- **Scope held:** styled term page (Epic 4.1), search (4.2), Home/featured (Epic 3) untouched; term-page links resolve to Hugo's default term page until Epic 4.

### File List

- `layouts/blog/list.html` (new) — the full post stream at /posts/ (card grid, 920px)
- `layouts/_partials/custom/post-card.html` (modified) — evolved to the accessible overlay pattern; reuses `post-tags.html` for the now-clickable tag
- `layouts/_partials/custom/post-tags.html` (modified) — stable per-topic tint via `hash.FNV32a`
- `assets/css/custom.css` (modified) — `.ck-stream`/`.ck-grid` (2-up→1-up), overlay-link `::after`, raised `.ck-tags`, `:focus-within` lift
- `_bmad-output/implementation-artifacts/sprint-status.yaml` (modified) — 2.4 → review

## Change Log

| Date | Change |
| --- | --- |
| 2026-06-23 | Story 2.4 drafted via create-story. Status → ready-for-dev. |
| 2026-06-23 | Story 2.4 implemented: `blog/list.html` stream (920px card grid, 2-up→1-up), evolved `post-card` to the accessible overlay pattern (whole-card→post + clickable tag→term page, satisfying UX-DR5 AND AC2), per-topic chip tint. All ACs verified. Status → review. |
