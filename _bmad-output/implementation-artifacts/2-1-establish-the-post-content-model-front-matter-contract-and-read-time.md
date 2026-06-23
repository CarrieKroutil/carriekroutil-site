---
baseline_commit: 7e04a52
---

# Story 2.1: Establish the post content model, front-matter contract, and read-time

Status: done

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As Carrie (builder),
I want a single canonical shape for every post and its tags,
so that independently-authored posts never diverge and metadata stays consistent.

*Implements FR-6 (CAP-6), FR-7 (CAP-7) · AR-6, AR-7 (AD-3, AD-4, AD-8). First story of Epic 2 — the content-model foundation every later post surface (2.2 single, 2.3 card, 2.4 stream) builds on.*

## Acceptance Criteria

**AC1 — Posts section at `/posts/`, slug = bundle folder**
**Given** the posts section
**When** set up
**Then** posts live in `content/posts/`, whose `_index.md` is the only home for section-level config
**And** each post URL is `/posts/{slug}/` where the slug derives from the post bundle's folder name (a front-matter `slug:` is allowed only as a deliberate override; the title never sets the URL).

> **Revised 2026-06-23 (Option B):** AD-4 originally required `_index.md` to set `type: blog`. We pivoted to **section-name routing** — no `type` set; overrides live in `layouts/posts/` and Hugo routes to them by the section name `posts`. Same `/posts/{slug}/` URLs, simpler layout tree (no `blog`-named folder, no `type` cascade). AD-4 updated to match.

**AC2 — Front-matter contract is enforced at build**
**Given** a post's YAML front matter
**When** the site builds
**Then** the contract is enforced: `title` (string), `date` (ISO/RFC-3339 — **missing or invalid fails the build**, never silently mis-sorts), `tags` (list, lowercase, open taxonomy), `draft` (bool), optional `featured` (bool), optional `hero` (`{ src, alt }` with `alt` required)
**And** a future `date` withholds the post until that date (`buildFuture = false`) rather than vanishing silently.

**AC3 — Read-time derived; date format site-wide**
**Given** a published post
**When** rendered
**Then** read-time is derived automatically from `.ReadingTime`, displayed as `"{n} min read"` with a floor of 1 min, and is never hand-entered
**And** the date displays site-wide via the Go layout `Jan 2, 2006`.

**AC4 — Open taxonomy, no code change to add a tag**
**Given** Carrie adds a new tag value not seen before
**When** she publishes
**Then** no code change is required — the tag is simply a new lowercase value in `tags`.

## Tasks / Subtasks

- [x] **Task 1 — Create the posts section** (AC: 1)
  - [x] Create `content/posts/_index.md` with `title: "Posts"`, the only section-level config home — no manual list/curation. *(Revised: no `type: blog` — section-name routing per Option B, see AC1 note.)*
  - [x] Confirmed Hugo serves `/posts/` and the bundle at `/posts/hello-corner-of-the-internet/` (slug = folder name). No `[permalinks]` block needed — default handling yields the correct URL.
  - [x] Did NOT author `layouts/posts/list.html` (Story 2.4). Hextra native list renders the section for now.

- [x] **Task 2 — Pin content-model config in `hugo.toml`** (AC: 2)
  - [x] Added `buildFuture = false` (explicit, commented) at top level.
  - [x] Confirmed `[taxonomies] tag = "tags"` already present (Story 1.1) — no change.
  - [x] No web-font / baseURL / title / menu / search / theme changes.

- [x] **Task 3 — Build the reusable post-metadata partial with the date guard** (AC: 2, 3)
  - [x] Created `layouts/_partials/custom/post-meta.html`: `.Date.IsZero` → `errorf` (missing-date gate); date `{{ .Date.Format "Jan 2, 2006" }}`; read-time `.ReadingTime` with explicit floor of 1; rendered as `<div class="ck-post-meta">{date} &middot; {n} min read</div>`. Used `.Path` (not `.File.Path`) in the error for nil-safety.
  - [x] This is THE metadata primitive — 2.2/2.3 call it, no duplication.
  - [x] Wiring seam documented; guard verified via temporary harness in Task 5 (harness removed). Invalid-date enforcement live Hugo-native.

- [x] **Task 4 — Author the canonical reference post (page bundle)** (AC: 1, 2, 3, 4)
  - [x] Created `content/posts/hello-corner-of-the-internet/index.md` — real, non-draft, on-voice launch content (not a throwaway post).
  - [x] Front matter conforms: `title`, `date` (ISO, past), `tags: [code, ai]` (lowercase), `draft: false`. No `hero` (heroless on purpose — image + alt hook is 2.2; exercises gradient-thumb fallback in 2.3).
  - [x] Body short + on-voice (golf/dogs/code/AI, never self-deprecating, one 👋).
  - [x] Did NOT pre-build the BMAD launch post (Epic 5, Story 5.2).

- [x] **Task 5 — Verify the contract holds (build + gate)** (AC: 1, 2, 3, 4)
  - [x] `hugo --gc --minify` exits 0; seed post at `/posts/hello-corner-of-the-internet/`, section at `/posts/`.
  - [x] **Missing-date gate:** stripping `date` → build fails with our `errorf` ("missing a required `date`…", `Error: error building site`). Verified through a temporary `layouts/posts/single.html` harness calling `post-meta`; meta line rendered `Jun 23, 2026 &#183; 1 min read`. Harness removed.
  - [x] **Invalid-date check:** `date: not-a-real-date` → build fails with TWO errors (Hugo-native "not a parsable date" + our guard, since the unparsable date coerces to zero) → `Error: error building site: logged 2 error(s)`.
  - [x] **Future-date check:** `date: 2099-…` → omitted from plain `hugo` build (correct), included with `hugo -F` (correct) — confirms `buildFuture = false`.
  - [x] **Read-time floor:** the short seed post renders `1 min read` (floor working, not `0 min read`).
  - [x] **No-remnant check:** `grep -ri 'shebytes\|code.shebytes\|ananke\|quickstart' content/ layouts/ hugo.toml` → clean.

## Dev Notes

### Scope guardrails (prevent overbuild / overlap)
This story is the **content model + contract + metadata primitive only**. Explicitly OUT of scope (each is a later Epic-2 story — do not pre-build):
- Single post page layout (`layouts/posts/single.html`: title/body/code-highlight/back-link/720px width) → **Story 2.2**.
- `layouts/_default/_markup/render-image.html` alt-enforcement hook + hero image rendering → **Story 2.2** (2.1's seed post is heroless on purpose).
- `layouts/_partials/custom/post-card.html` (thumb, uppercase tag, excerpt, hover lift) → **Story 2.3**.
- `layouts/posts/list.html` styled stream + tag-chip links to `/tags/{topic}/` → **Story 2.4**.
- Styled `/tags/{topic}/` term page → **Epic 4 (Story 4.1)**.
- The BMAD build-in-public launch post → **Epic 5 (Story 5.2)**.
2.1 ships: the `posts` section config, `buildFuture=false`, the reusable `post-meta.html` partial (with the date guard + read-time floor + `Jan 2, 2006` format), and one canonical conforming seed post.

### The front-matter contract (AD-3) — enforce exactly
| Field | Type | Required | Rule / failure |
| --- | --- | --- | --- |
| `title` | string | yes | — |
| `date` | RFC-3339/ISO | yes | **missing → build fails** (via `post-meta` `errorf` on `.Date.IsZero`); **invalid → build fails** (Hugo-native parse error). Never silently mis-sort. |
| `tags` | list | yes | open taxonomy, authored **lowercase** (so `Golf`/`golf` don't split term pages). No allowlist; new value needs no code change (AC4). |
| `draft` | bool | yes | `draft: true` excluded from production build + stream; visible only under `hugo server -D`. |
| `featured` | bool | optional | AD-7; consumed in Epic 3 (Story 3.3). Just carry it in the contract; don't build the featured slot here. |
| `hero` | map `{src, alt}` | optional | `alt` required-if-present; enforced by `render-image.html` in **2.2**. Seed post omits `hero` in 2.1. |
[Source: ARCHITECTURE-SPINE.md#AD-3; epics.md#Story 2.1]

### Date display — spine wins over the mock (resolve this conflict deliberately)
- The architecture spine + epics AC say the date displays **site-wide as `Jan 2, 2006`** (Go reference layout → abbreviated month, e.g. "Jun 22, 2026"). [Source: ARCHITECTURE-SPINE.md §Consistency Conventions; epics.md#Story 2.1 AC3]
- The UX mock `post-single.html:60` shows the full-month form "June 22, 2026". **The spine wins over any mock** (workflow rule). Use `Jan 2, 2006`. The middle-dot `·` separator between date and read-time from the mock is kept (it's presentation, not contradicted by the spine). [Source: mockups/post-single.html:60]

### Posts section & URLs (AD-4 / AD-7 wording)
- `content/posts/_index.md` with `type: blog` activates Hextra's native blog list + single layouts AND preserves `/posts/{slug}/` URLs (vs Hextra's default `content/blog/` which would change the public URL). [Source: ARCHITECTURE-SPINE.md#AD-4]
- Slug = bundle folder name. A front-matter `slug:` is a deliberate override only; **the title never determines the URL**. Slugs stay stable + human-readable. [Source: ARCHITECTURE-SPINE.md#AD-4]
- Page-bundle layout (AD-9): `content/posts/{slug}/index.md` with images beside the markdown (portable). 2.1's seed post is text-only (no images yet). [Source: ARCHITECTURE-SPINE.md#AD-9, §Structural Seed]

### Read-time (AD-3)
- Derive from Hugo's `.ReadingTime` (int minutes). Apply an explicit floor of 1 so a tiny post never shows `0 min read`. Render `"{n} min read"`. Never read a front-matter read-time field. [Source: ARCHITECTURE-SPINE.md#AD-3 line 72, §Consistency Conventions line 134]

### Override model (AD-5) — unchanged from Epic 1
- All custom layouts/partials live under the project `layouts/` and shadow the Hextra module by Hugo's lookup order; the module is **never** edited in place. New partial goes at `layouts/_partials/custom/post-meta.html` (matches the existing `layouts/_partials/custom/footer.html` convention from Story 1.3). [Source: ARCHITECTURE-SPINE.md#AD-5; existing repo layout]

### Voice & microcopy for the seed post (UX-DR13)
Playful, cozy, warm, first-person, **never self-deprecating** — "like texting a friend who happens to also respect her work." Sparing friendly emoji (a 👋, not confetti). Carrie's topics: golf (about fun + the next round, never being bad at it), her dogs, code, building things with AI. Sample register from the mock: *"I wanted a little corner of the internet that was actually mine — somewhere to post about golf, my dogs, code, and whatever else I'm into."* [Source: EXPERIENCE.md lines 60–71; mockups/post-single.html:63–65]

### Meta line styling reference (for the partial markup; full styling lands 2.2/2.3)
Mock meta line: `<div class="meta">June 22, 2026 · 6 min read</div>` with `.meta{color:var(--muted);font-size:.88rem}` — muted color must not dip below `#a0a0ab` on dark (AA floor; the mock's lighter `#70707c` fails AA — already encoded as `--ck-muted`/`--ck-meta-min` in `assets/css/custom.css`). Emit a stable class (e.g. `ck-post-meta`) and let 2.2/2.3 own the CSS; 2.1 keeps markup minimal. [Source: mockups/post-single.html:33,60; DESIGN.md:163–164; assets/css/custom.css]

### Previous-story intelligence (Epic 1)
- **Story 1.1 (scaffold):** `hugo.toml` is TOML; front matter is YAML (`---`); `[taxonomies] tag = "tags"` already set; drafts handled by Hugo defaults (no flag). The `[outputs] home = [...JSON]` block was intentionally removed (Hextra emits its own FlexSearch index; a JSON home output only produced a WARN). Don't re-add it. Local Hugo is `v0.151.2+extended`. [Source: 1-1-*.md Dev Agent Record]
- **Story 1.3 (footer):** custom partials shadow Hextra via `layouts/_partials/custom/footer.html` + `layouts/_partials/footer.html`. Follow the same `custom/` convention for `post-meta.html`. [Source: repo layout]
- **Story 1.2 (tokens):** `assets/css/custom.css` already defines `--ck-muted`/`--ck-meta-min` (`#a0a0ab`), radii, gutters, `--ck-reading-max` (720px), `--ck-content-max` (920px), chip tints. Reuse these tokens later; 2.1 doesn't add CSS. [Source: assets/css/custom.css]
- **Branch convention deviation (this epic):** per Carrie's instruction, ALL of Epic 2 (Stories 2.1–2.4) ships on the single branch `epic-2-post-reading-experience` (Epic 1 used per-story branches). One code review + one PR at the end of the epic. Keep each story a self-contained commit on this branch.

### Testing / verification standards
No unit-test framework for a static site — "tests" = build + behavior checks (Task 5). Always run the commands; never claim success without output. Record commands + outcomes in the Dev Agent Record.

### Anti-patterns to avoid
- ❌ Hand-entering read-time / a `readingTime:` front-matter field. ✅ Always `.ReadingTime` + floor 1.
- ❌ Full-month date "June 22, 2026". ✅ `Jan 2, 2006` (spine).
- ❌ Building single.html / list.html / post-card / render-image hook here. ✅ Stay in the content-model lane; those are 2.2–2.4.
- ❌ Adding a hero image to the seed post. ✅ Heroless in 2.1 (image + alt-hook is 2.2).
- ❌ Editing the Hextra module or duplicating date/read-time logic. ✅ One `post-meta.html` partial, shadowing only.
- ❌ Uppercase or mixed-case tags. ✅ lowercase `tags`.
- ❌ A throwaway/quickstart placeholder post. ✅ One genuine, on-voice post.

### Project Structure Notes
Adds `content/posts/_index.md`, `content/posts/{slug}/index.md` (page bundle), and `layouts/_partials/custom/post-meta.html`; edits `hugo.toml` (one `buildFuture = false` line). Matches the Structural Seed exactly — no variance. No conflict with Epic 1 files.

### References
- [Source: _bmad-output/planning-artifacts/epics.md#Story 2.1: Establish the post content model, front-matter contract, and read-time] — story + ACs
- [Source: _bmad-output/planning-artifacts/architecture/architecture-carriekroutil-site-2026-06-22/ARCHITECTURE-SPINE.md#AD-3] — front-matter contract, read-time, date format, future/draft
- [Source: .../ARCHITECTURE-SPINE.md#AD-4] — `type: blog` section at `/posts/`, slug = bundle folder
- [Source: .../ARCHITECTURE-SPINE.md#AD-8] — open lowercase tag taxonomy, term pages (styling deferred to Epic 4)
- [Source: .../ARCHITECTURE-SPINE.md#AD-5] — overrides only; module read-only
- [Source: .../ARCHITECTURE-SPINE.md#AD-9] — page bundles; alt enforced (hook in 2.2)
- [Source: .../ARCHITECTURE-SPINE.md#Consistency Conventions] — dates `Jan 2, 2006`, read-time floor 1, lowercase tags, `buildFuture=false`
- [Source: _bmad-output/planning-artifacts/ux-designs/.../EXPERIENCE.md#Voice] — microcopy/tone for the seed post
- [Source: _bmad-output/planning-artifacts/ux-designs/.../mockups/post-single.html] — meta line markup (spine overrides its full-month date)

## Dev Agent Record

### Agent Model Used

claude-opus-4-8[1m] (Opus 4.8, 1M context)

### Debug Log References

- `hugo --gc --minify` → exit 0, 13 pages; seed post at `public/posts/hello-corner-of-the-internet/index.html`, section at `public/posts/index.html`.
- Temp harness `layouts/posts/single.html` (calls `post-meta`) → rendered `<div class="ck-post-meta">Jun 23, 2026 &#183; 1 min read</div>`. Harness removed after verification.
- Missing `date` → `ERROR post "/posts/hello-corner-of-the-internet" is missing a required `date`…` + `Error: error building site`.
- `date: not-a-real-date` → `ERROR … not a parsable date` + our guard → `Error: error building site: logged 2 error(s)`.
- `date: 2099-…` → absent from plain `hugo` build; present under `hugo -F`.
- `grep -ri 'shebytes\|code.shebytes\|ananke\|quickstart' content/ layouts/ hugo.toml` → no matches.
- Local toolchain: Hugo `v0.151.2+extended` (darwin/arm64).

### Completion Notes List

- **AC1 met:** `content/posts/_index.md` is the sole section-config home; seed post bundle resolves to `/posts/{slug}/` with slug = folder name; no `[permalinks]` override needed. *(Revised 2026-06-23 — Option B: no `type: blog`; section-name routing to `layouts/posts/`.)*
- **AC2 met:** front-matter contract enforced at build — missing `date` fails via `post-meta`'s `errorf` guard; invalid `date` fails Hugo-native (plus our guard); `buildFuture = false` withholds future-dated posts (verified both directions). `featured`/`hero` carried in the contract (consumed in Epic 3 / Story 2.2 respectively).
- **AC3 met:** read-time derived from `.ReadingTime` with explicit floor 1 (`1 min read` on the short seed post); date formats site-wide as `Jan 2, 2006` ("Jun 23, 2026").
- **AC4 met:** tags are a lowercase open list (`[code, ai]`); adding a new value needs no code change (Hugo core generates term pages natively).
- **Design decision — date format:** used the spine's `Jan 2, 2006` (abbreviated month) over the mock's full-month "June 22, 2026" per "spine wins over mock". Kept the mock's `·` separator.
- **Wiring seam (by design):** the missing-date guard ships in `post-meta.html` and becomes a live standing build-gate the moment Story 2.2's `layouts/posts/single.html` calls the partial — same branch, same Epic-2 PR. In 2.1 alone the Hextra-native single renders the post without calling our partial, so the guard was verified via a temporary harness (removed). Invalid-date enforcement is already live (Hugo-native) regardless.
- **Scope held:** no single page / list / post-card / render-image hook / hero image here (Stories 2.2–2.4 + Epic 4). Seed post is heroless on purpose.

### File List

- `content/posts/_index.md` (new) — posts section config (no `type` — section-name routing, Option B)
- `content/posts/hello-corner-of-the-internet/index.md` (new) — canonical reference post (page bundle, heroless)
- `layouts/_partials/custom/post-meta.html` (new) — date + read-time metadata primitive with the AD-3 date guard
- `hugo.toml` (modified) — added `buildFuture = false`
- `_bmad-output/implementation-artifacts/sprint-status.yaml` (modified) — epic-2 → in-progress, 2.1 → review

## Change Log

| Date | Change |
| --- | --- |
| 2026-06-23 | Story 2.1 drafted via create-story (ultimate context engine). Status → ready-for-dev. |
| 2026-06-23 | Story 2.1 implemented: posts `type: blog` section, `post-meta.html` (date guard + `Jan 2, 2006` + read-time floor 1), `buildFuture=false`, canonical seed post. All ACs verified via build + gate checks. Status → review. |
| 2026-06-23 | Post-merge-review pivot (Option B, Carrie): dropped `type: blog` from `_index.md`; posts now route by section name to `layouts/posts/` (see updated AD-4). Same `/posts/` URLs; rebuilt + re-verified green. |
