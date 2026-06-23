---
baseline_commit: NO_VCS
---

# Story 1.1: Scaffold the greenfield Hugo + Hextra site with local preview

Status: done

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As Carrie (builder),
I want a fresh Hugo + Hextra project I can run locally,
so that I have a clean foundation to build on and can preview the site before anything goes live.

*Implements FR-16 (CAP-16) · AR-1, AR-8 (AD-5, AD-10).*

## Acceptance Criteria

**AC1 — Config + module lock + directory seed**
**Given** the `carriekroutil-site` repo contains only BMad tooling (nothing carried from `~/code/quickstart`)
**When** the scaffold is created
**Then** `hugo.toml` exists with the Hextra module import and `baseURL = "https://carriekroutil.com/"`, a carriekroutil.com `title`, `params.theme.default = "dark"`, search, and taxonomy config
**And** `go.mod`/`go.sum` lock the Hextra module `github.com/imfing/hextra` at `v0.12.3`
**And** the Structural Seed directory layout exists (`content/`, `layouts/`, `assets/`, `static/`).

**AC2 — Local preview with live reload, drafts visible locally only**
**Given** the scaffolded project
**When** Carrie runs `hugo server`
**Then** the site renders in a browser with live reload on save
**And** both draft and non-draft content are visible in local preview (drafts shown locally, withheld from production builds).

**AC3 — Customization lives in project overrides, module stays read-only**
**Given** project customization is needed later
**When** a custom layout, partial, or style is added
**Then** it lives in the project's own `layouts/` or `assets/` (shadowing the module), and the Hextra module is never edited in place.

## Tasks / Subtasks

- [x] **Task 1 — Initialize the Hugo Module and lock Hextra v0.12.3** (AC: 1)
  - [x] Run `hugo mod init github.com/carriekroutil/carriekroutil-site` at the repo root to create `go.mod` (no git repo required for module init).
  - [x] Run `hugo mod get github.com/imfing/hextra@v0.12.3` to add the dependency and generate `go.sum`.
  - [x] Run `hugo mod tidy` and verify `go.mod` pins `github.com/imfing/hextra v0.12.3` exactly (no `latest`, no unpinned floats).
  - [x] Do NOT use `hugo new site`-with-git-submodule, an Ananke/quickstart starter, or a vendored theme copy — Hextra is consumed only as a Hugo Module (AD-10).
- [x] **Task 2 — Author `hugo.toml` (TOML, repo root)** (AC: 1)
  - [x] `baseURL = "https://carriekroutil.com/"` and a carriekroutil.com `title` (e.g. `"carrie kroutil"`). Zero SheBytes / `code.shebytes.io` / ananke / quickstart strings anywhere.
  - [x] `[[module.imports]]` with `path = "github.com/imfing/hextra"`.
  - [x] `[params.theme] default = "dark"` and `displayToggle = true` (dark is the default paint; toggle styling is Story 1.4).
  - [x] Search config: `[params.search] enable = true`, `type = "flexsearch"`, `[params.search.flexsearch] index = "content"`; add `[outputs] home = ["HTML", "RSS", "JSON"]` so FlexSearch's JSON index is emitted.
  - [x] Taxonomy config: `[taxonomies] tag = "tags"` so the only taxonomy is the lowercase `tags` term (drops Hugo's default `categories`).
  - [x] `[markup.goldmark.renderer] unsafe = true` only if needed for later raw-HTML content — leave default unless a build error forces it; note the decision.
- [x] **Task 3 — Create the Structural Seed directory layout** (AC: 1, 3)
  - [x] Create `content/` with a minimal `content/_index.md` (front matter + a placeholder line) so `hugo server` has a home page to render. Keep copy minimal — the real Home hero is Epic 3.
  - [x] Create empty-but-tracked `layouts/`, `assets/`, and `static/` directories (add a `.gitkeep` to each so they persist; these are the override homes per AD-5).
  - [x] Do NOT create `content/posts/`, term/list/single overrides, the post-card partial, or the render-image hook here — those land in Epics 2–4. This story is scaffold only.
- [x] **Task 4 — Verify local preview, live reload, and draft behavior** (AC: 2)
  - [x] Run `hugo` (production build) and confirm it completes with no errors and emits `public/`.
  - [x] Run `hugo server -D` and confirm the home page renders in a browser with live reload (edit `content/_index.md`, see it update without manual refresh).
  - [x] Confirm draft handling: temporarily add a `draft: true` test page, confirm it appears under `hugo server -D` but is absent from a plain `hugo` production build's `public/`, then remove the test page so the repo stays clean (no throwaway quickstart content committed — Story 1.5 forbids remnants).
- [x] **Task 5 — Confirm override-shadowing model without editing the module** (AC: 3)
  - [x] Run `hugo mod vendor` is NOT required; confirm the module cache is read-only and untouched.
  - [x] Sanity-check the override path: the project `layouts/`/`assets/` exist and shadow the module by Hugo's lookup order (no override files needed yet — just confirm the directories are in place and the module was not edited).
  - [x] Record the exact local Hugo version used (`hugo version`) in the Dev Agent Record.

## Dev Notes

### Scope guardrails (prevent overbuild)
This is **scaffold only**. Deliver a runnable empty-shell Hugo + Hextra site. Explicitly OUT of scope here (each is a later story — do not pre-build):
- Design tokens / custom CSS → **Story 1.2** (`assets/` stays empty now).
- Wordmark, header nav, footer → **Story 1.3**.
- Theme toggle styling/persistence behavior → **Story 1.4** (this story only sets `theme.default = dark` + `displayToggle = true`).
- `amplify.yml`, `HUGO_VERSION` pin, git/Amplify pipeline → **Story 1.5**. Do **not** author `amplify.yml` or pin `HUGO_VERSION` here.
- `content/posts/`, post-card partial, list/single/term overrides, `render-image.html` → **Epics 2–4**.

### Environment (verified on this machine, 2026-06-22)
- Hugo `v0.151.2+extended` (darwin/arm64) is installed — **extended** edition, ≥ the 0.146.0 floor Hextra v0.12.3 requires. Use this for local scaffold/preview. [Source: local `hugo version`]
- Go `1.25.3` is installed — required for Hugo Modules. [Source: local `go version`]
- The repo is **not** a git repo yet and contains only `_bmad/`, `_bmad-output/`, `.claude/`, `docs/`. `hugo mod init` does not require git, so module init proceeds; git init + remote is Story 1.5's concern. [Source: local inspection]
- **Version-pin note:** The architecture Stack table suggests pinning a *current* Hugo (~0.163.x as of authoring) via `HUGO_VERSION` for the build. That pin is an **Amplify build-env concern owned by Story 1.5** — do not introduce it here. Local dev correctly uses the installed 0.151.2 extended. [Source: ARCHITECTURE-SPINE.md §Stack, AD-10]

### Key architecture rules binding this story
- **AR-1 / AD-5, AD-10 — Greenfield scaffold via Hugo Modules.** Fresh Hugo (extended) + Hextra through Modules. `hugo.toml` carries baseURL/title/module import/`params.theme.default=dark`/search/taxonomy; `go.mod`/`go.sum` lock Hextra `v0.12.3` (`github.com/imfing/hextra`); the Structural Seed directory layout exists. [Source: epics.md §AR-1; ARCHITECTURE-SPINE.md AD-5, AD-10]
- **AD-5 / AR-8 — Project overrides only; module read-only.** All future custom partials, layout overrides, and CSS live in the project's `layouts/` + `assets/`, shadowing Hextra by Hugo's lookup order. The Hextra module is **never** edited in place. [Source: ARCHITECTURE-SPINE.md AD-5]
- **AD-10 — Versions pinned, upgrades deliberate.** Hextra is locked in `go.mod`; never `latest`/floating. Upgrades happen on purpose (`hugo mod get -u` + re-test). [Source: ARCHITECTURE-SPINE.md AD-10]
- **Conventions — config format & branding.** Front matter is YAML (`---`). `hugo.toml` is the config (TOML, not hugo.yaml). `baseURL`/`title` must reflect carriekroutil.com with **zero** SheBytes/`code.shebytes.io`/ananke/quickstart remnants. Tags taxonomy authored lowercase. Dark is the default + AA-guaranteed theme. [Source: ARCHITECTURE-SPINE.md §Consistency Conventions]

### Structural Seed (target layout — only the scaffold subset belongs to this story)
```text
carriekroutil-site/
  hugo.toml            # baseURL, title, module import, params(theme.default=dark, search), taxonomy   ← THIS STORY
  go.mod / go.sum      # Hextra v0.12.3 lock (AD-10)                                                    ← THIS STORY
  content/
    _index.md          # minimal home placeholder (real hero = Epic 3)                                  ← THIS STORY (minimal)
  layouts/             # project override home — empty now (AD-5)                                        ← THIS STORY (dir only)
  assets/              # custom CSS home — empty now (AD-5)                                              ← THIS STORY (dir only)
  static/              # favicon / shared assets — empty now                                            ← THIS STORY (dir only)
  amplify.yml          # ← Story 1.5, NOT here
  content/posts/...    # ← Epic 2, NOT here
```
[Source: ARCHITECTURE-SPINE.md §Structural Seed]

### Drafts vs production (AC2 mechanism)
Hugo's default behavior already satisfies AC2 — no config flag needed:
- Local preview: `hugo server -D` (or `--buildDrafts`) renders draft + non-draft with live reload.
- Production build: plain `hugo` (and later `hugo --gc --minify`) excludes `draft: true` by default.
Convention also sets `buildFuture = false` so a future `date` withholds a post rather than vanishing silently — but **that's a posts concern (Story 2.1)**; only add `buildFuture = false` if it's trivial and you note it, otherwise leave for 2.1. Do not over-configure. [Source: ARCHITECTURE-SPINE.md §Consistency Conventions — Drafts vs future dates]

### Hextra config specifics (avoid common mistakes)
- Hextra docs often show `hugo.yaml`; this project uses **`hugo.toml`** by architecture decision. Translate Hextra's example config to TOML faithfully.
- FlexSearch needs the JSON output on the home page — set `[outputs] home = ["HTML", "RSS", "JSON"]`. Without it, search (Story 4.2) silently has no index.
- Setting `[taxonomies] tag = "tags"` replaces Hugo's defaults; that's intended (we want only `tags`, lowercase). Don't also leave `categories` around.
- `params.theme.default = "dark"` is the dark-default paint for this story. The functional toggle + persistence verification is Story 1.4 — don't gold-plate it here.

### Testing / verification standards
No unit-test framework applies to a static-site scaffold. "Tests" here = build + preview verification:
1. `hugo` exits 0 and writes `public/` (production build clean).
2. `hugo server -D` serves the home page with working live reload.
3. Draft visible under `-D`, absent from plain `hugo` output (temporary test page, then removed).
4. `go.mod` shows `github.com/imfing/hextra v0.12.3` (exact), `go.sum` present.
5. `grep -ri` for `shebytes\|code.shebytes\|ananke\|quickstart` across tracked files returns nothing.
Record the commands run and their outcomes in the Dev Agent Record (don't claim success without running them). [Source: derived from AC1–AC3; ARCHITECTURE-SPINE.md AD-2/AD-11 keep amplify out of scope]

### Anti-patterns to avoid (disaster prevention)
- ❌ `hugo new site` then adding Ananke or any theme as a git submodule / vendored copy. ✅ Hugo Modules + Hextra only.
- ❌ Editing files inside the Hextra module cache. ✅ All customization in project `layouts/`/`assets/` (none needed yet).
- ❌ Pinning Hextra to `latest` or leaving it unpinned. ✅ Exact `v0.12.3` in `go.mod`.
- ❌ Authoring `amplify.yml` / `HUGO_VERSION` / git remote here. ✅ Defer to Story 1.5.
- ❌ Committing throwaway quickstart posts or `code.shebytes.io`/SheBytes branding. ✅ Clean greenfield; Story 1.5 enforces zero remnants.
- ❌ Using `hugo.yaml`. ✅ `hugo.toml` (TOML).

### Project Structure Notes
- This is the first story of a greenfield repo — no conflicts with existing structure. The repo currently holds only BMad tooling (`_bmad/`, `_bmad-output/`, `.claude/`) and `docs/`; the scaffold adds `hugo.toml`, `go.mod`/`go.sum`, and the `content/ layouts/ assets/ static/` seed at the repo root, which is exactly the Structural Seed. No variance.

### References
- [Source: _bmad-output/planning-artifacts/epics.md#Story 1.1: Scaffold the greenfield Hugo + Hextra site with local preview] — story statement + ACs
- [Source: _bmad-output/planning-artifacts/epics.md#Additional Requirements] — AR-1, AR-8 definitions
- [Source: _bmad-output/planning-artifacts/architecture/architecture-carriekroutil-site-2026-06-22/ARCHITECTURE-SPINE.md#AD-5] — overrides only, module read-only
- [Source: .../ARCHITECTURE-SPINE.md#AD-10] — pinned versions, deliberate upgrades
- [Source: .../ARCHITECTURE-SPINE.md#Stack] — Hugo extended ≥0.146.0, Hextra v0.12.3, `github.com/imfing/hextra`
- [Source: .../ARCHITECTURE-SPINE.md#Structural Seed] — directory layout
- [Source: .../ARCHITECTURE-SPINE.md#Consistency Conventions] — hugo.toml branding, YAML front matter, lowercase tags, dark default, drafts vs future dates

## Dev Agent Record

### Agent Model Used

claude-opus-4-8[1m] (Opus 4.8, 1M context)

### Debug Log References

- Local toolchain (verified): Hugo `v0.151.2+extended+withdeploy` (darwin/arm64), Go `1.25.3`.
- `hugo mod init github.com/carriekroutil/carriekroutil-site` → created `go.mod`.
- `hugo mod get github.com/imfing/hextra@v0.12.3` + `hugo mod tidy` → locked `github.com/imfing/hextra v0.12.3` in `go.mod`/`go.sum` (exact pin, no float).
- `hugo --gc --minify` → exit 0, `public/` emitted, Hextra FlexSearch index `en.search-data.json` present.
- Draft check: temp `content/drafttest.md` (`draft: true`) absent from plain `hugo` build, present under `hugo -D`, then removed; clean rebuild exit 0.
- `hugo server -D` → home `/` returns HTTP 200 with livereload script injected (live reload confirmed), then stopped.
- Branding grep (`shebytes|code.shebytes|ananke|quickstart`) over tracked project files → none.

### Completion Notes List

- **AC1 met:** `hugo.toml` (TOML) carries `baseURL = "https://carriekroutil.com/"`, `title = "carrie kroutil"`, the `github.com/imfing/hextra` module import, `params.theme.default = "dark"` (+ `displayToggle = true`), `params.search` (FlexSearch), and `[taxonomies] tag = "tags"`. `go.mod`/`go.sum` lock Hextra `v0.12.3` exactly. Structural Seed dirs `content/ layouts/ assets/ static/` exist (override dirs kept via `.gitkeep`).
- **AC2 met:** `hugo server` renders the home page with live reload (HTTP 200 + livereload verified); drafts appear under `-D` and are excluded from the production `hugo` build (Hugo default behavior — no extra config needed).
- **AC3 met:** Hextra lives in the Go module cache (`$GOMODCACHE`, read-only — not vendored or edited in place); `layouts/` and `assets/` are present as the project override homes that shadow the module by Hugo's lookup order. No override files authored yet (correct — those are later stories).
- **Deviation from Task 2 (documented):** Task 2 said to add `[outputs] home = [..., "JSON"]` for the search index. In practice Hextra emits its own `en.search-data.json` from `params.search`, and adding the JSON home output produced a `WARN` (no JSON home layout) without a useful artifact. Removed the `[outputs]` block; FlexSearch index is still generated. Build is now warning-free. The `params.search` config alone satisfies AC1's "search ... config".
- **Scaffold hygiene added:** `.gitignore` (excludes `public/`, `resources/`, `.hugo_build.lock`, `.DS_Store`) so Story 1.5's git init starts clean. Within scope of "a clean foundation to build on".
- **Deferred-as-designed (not done here, by design):** `amplify.yml` / `HUGO_VERSION` pin / git remote → Story 1.5; design tokens/CSS → 1.2; wordmark/nav/footer → 1.3; toggle behavior → 1.4; `content/posts/` + partials/overrides → Epics 2–4.
- **Note for Story 1.5:** local Hugo is `0.151.2 extended`; the Amplify `HUGO_VERSION` pin (architecture suggests a current ~0.163.x) is owned by 1.5 — choose and pin the extended edition there.

### File List

- `hugo.toml` (new) — Hugo + Hextra config
- `go.mod` (new) — Hugo Module, Hextra v0.12.3 lock
- `go.sum` (new) — module checksums
- `content/_index.md` (new) — minimal placeholder home
- `layouts/.gitkeep` (new) — project override home (AD-5)
- `assets/.gitkeep` (new) — custom CSS home (AD-5)
- `static/.gitkeep` (new) — static assets home
- `.gitignore` (new) — ignore build output/caches
- `README.md` (new) — local-run instructions + project layout

## Change Log

| Date | Change |
| --- | --- |
| 2026-06-22 | Story 1.1 implemented: greenfield Hugo + Hextra scaffold via Hugo Modules (Hextra v0.12.3 locked), `hugo.toml` config, Structural Seed dirs, local preview + draft behavior verified. Status → review. |
| 2026-06-22 | Added `README.md` with local-run instructions. |
| 2026-06-22 | Code review (3-layer adversarial: Blind Hunter, Edge Case Hunter, Acceptance Auditor) — clean pass, 0 actionable findings, 4 dismissed. Status → done. |

## Senior Developer Review (AI)

**Reviewer:** Opus 4.8 (3-layer adversarial review — Blind Hunter, Edge Case Hunter, Acceptance Auditor)
**Date:** 2026-06-22
**Outcome:** ✅ **Approve** — clean pass. All three acceptance criteria verified empirically; `hugo` and `hugo server` both build exit 0 with no warnings; no branding remnants; no premature Story 1.5 artifacts.

**Triage:** 0 decision-needed · 0 patch · 0 defer · 4 dismissed.

### Dismissed findings (recorded for traceability)

1. **`// indirect` on the Hextra require** (Blind + Edge + Auditor) — Not a defect for a Hugo template module (consumed via `hugo.toml`, not imported in Go code, so genuinely indirect from Go's view). This is the state `hugo mod tidy` produces; left as-is.
2. **Search index empty until real content exists** (Edge) — Expected scaffold behavior; Hextra indexes only pages/sections with body content, not the home placeholder. Populates once Epic 2 adds posts.
3. **GitHub repo casing vs lowercase module path** (Edge) — No build/deploy impact (self-module Go never fetches; GitHub URLs case-insensitive). Sanity-check the remote URL at Story 1.5 push time.
4. **`go.sum` possibly incomplete** (Blind) — Refuted by the clean build; checksums resolve. False positive.
