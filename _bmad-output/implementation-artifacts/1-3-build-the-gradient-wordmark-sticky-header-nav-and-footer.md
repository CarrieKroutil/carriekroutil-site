---
baseline_commit: 81335bacb4bb476c21b31ac181243a296056bd54
---

# Story 1.3: Build the gradient wordmark, sticky header nav, and footer

Status: done

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As a visitor,
I want a consistent header and footer with the signature wordmark and the key links,
so that I can recognize the site and navigate it from anywhere.

*Implements FR-12 (CAP-12, wordmark) · UX-DR2, UX-DR6, UX-DR9 · UX-DR11 (focus ring) · NFR-2.*

## Acceptance Criteria

**AC1 — Gradient wordmark, top-left, links Home, with a solid fallback**
**Given** any page
**When** the header renders
**Then** the `carrie kroutil` wordmark appears top-left, weight 800, painted with the accent gradient via `background-clip:text`, and links Home
**And** where `background-clip:text` is unsupported (forced-colors mode, print), the wordmark falls back to solid fuchsia `#d946ef` (never invisible).

**AC2 — Sticky, translucent/blurred header with the right-side controls, each a real labelled control with a visible focus ring**
**Given** the sticky header
**When** rendered
**Then** the right side shows, in order, **Posts**, **About**, **search (⌕)**, **theme toggle (☾)**, **GitHub**, and **LinkedIn** — each a real `<a>`/`<button>` with an accessible label
**And** every interactive element shows a visible `2px solid #d946ef` focus ring on `:focus-visible`
**And** the header is translucent/blurred and stays pinned to the top on scroll, and collapses to Hextra's condensed/hamburger nav on mobile.

**AC3 — Footer: thin gradient rule above one muted line; only GitHub/LinkedIn operable**
**Given** any page
**When** the footer renders
**Then** a thin gradient rule (the accent gradient, garnish only) sits above a single muted footer line, with GitHub and LinkedIn as the **only** operable elements
**And** the muted footer text is never lighter than `#a0a0ab` (the mockup's `#70707c` is **not** used), and the "Powered by Hextra" credit is suppressed.

**AC4 — Build + brand integrity preserved**
**Given** the changes
**When** `hugo --gc --minify` runs
**Then** it builds clean (exit 0, warning-free), no web font is introduced, the gradient is used only as a garnish (wordmark + footer rule — never a large fill behind body text), and the Hextra module is not edited in place.

## Tasks / Subtasks

- [x] **Task 1 — Override the wordmark partial → gradient text-clip + solid fallback** (AC: 1, 4)
  - [x] Create the project partial **`layouts/_partials/navbar-title.html`** (this shadows the module's `_partials/navbar-title.html`, which `navbar.html:19` invokes via `{{ partial "navbar-title.html" . }}`). **Do NOT** use `_partials/custom/navbar-title.html` — that module placeholder is **not referenced** by the navbar and overriding it does nothing. (The footer is the opposite — see Task 4.)
  - [x] Render an `<a>` linking Home wrapping a `<span class="ck-wordmark">` with the title. **Preserve the `hx:ltr:mr-auto hx:rtl:ml-auto` classes on the link** — that `mr-auto` is what pushes the nav controls to the right edge (there is no separate spacer element in Hextra's navbar). Use `.Site.Home.RelPermalink` for the href and `.Site.Title` (already `"carrie kroutil"`) for the text. Keep `hx:flex hx:items-center` so it aligns with the controls.
  - [x] Remove the `layouts/.gitkeep` placeholder once a real partial exists under `layouts/` (the dir is no longer empty).
  - [x] In `assets/css/custom.css`, style `.ck-wordmark`: `font-weight: 800; letter-spacing: -0.02em; font-size: 1.12rem;` then the text-clip gradient using the **existing `--ck-accent-gradient` token** — set a solid `color: var(--ck-accent-fuchsia)` **first** as the fallback, then `background: var(--ck-accent-gradient); -webkit-background-clip: text; background-clip: text; -webkit-text-fill-color: transparent;`.
  - [x] Add the fallback guard: `@media (forced-colors: active), print { .ck-wordmark { background: none; -webkit-text-fill-color: var(--ck-accent-fuchsia); color: var(--ck-accent-fuchsia); } }` so the wordmark is **never invisible** when `background-clip:text` is dropped. (`-webkit-text-fill-color: transparent` would otherwise render nothing in forced-colors.)

- [x] **Task 2 — Wire the header right-side controls via `hugo.toml` menu config** (AC: 2)
  - [x] Add `[[menu.main]]` entries to `hugo.toml` in this order (Hextra renders by ascending `weight`): **Posts** (weight 10) · **About** (weight 20) · **search** (weight 30) · **theme-toggle** (weight 40) · **GitHub** (weight 50) · **LinkedIn** (weight 60).
  - [x] **Posts / About** = plain nav links: `name`, `url`, `weight`. **Use literal `url = "/posts/"` and `url = "/about/"` — NOT `pageRef`** — those pages do not exist until Epics 2/3; a literal URL avoids any build-time ref validation and is the documented forward-reference. (Menu items with no `type`/`icon` render via `navbar-link.html` as real `<a>`, `hx:hidden hx:md:inline-flex` → they fold into the hamburger/sidebar on mobile.)
  - [x] **search** = `[[menu.main]]` with `params.type = "search"` → renders Hextra's `search.html` (FlexSearch is already enabled in `[params.search]` from Story 1.1; this just surfaces the ⌕ control — do not tune indexing, that is Story 4.2 scope).
  - [x] **theme-toggle** = `[[menu.main]]` with `params.type = "theme-toggle"` and `params.label = false` (icon-only ☾). This surfaces Hextra's **built-in** toggle in the header. **Do not** build toggle behavior/persistence — that is Story 1.4; Hextra's control already toggles and is a real focusable `<button>` with a label.
  - [x] **GitHub / LinkedIn** = `[[menu.main]]` with `url` + `params.icon = "github"` / `params.icon = "linkedin"`. Both icons **exist in the module** (`data/icons.yaml` lines 12, 304) — no project `data/icons.yaml` needed. Set real profile URLs (confirm with Carrie if unknown — see Questions); they render as real `<a target="_blank">` with a `title` and an `sr-only` label (navbar.html:44-46). Use `name = "GitHub"` / `name = "LinkedIn"` (becomes the accessible label).

- [x] **Task 3 — Header CSS: translucent blur, border, and brand styling of the controls** (AC: 2, 4)
  - [x] Sticky is **already provided** — `.hextra-nav-container` ships `hx:sticky hx:top-0 hx:z-20` (navbar.html:13). Do **not** re-declare sticky; just restyle the backdrop.
  - [x] Override the blur layer: target **`.hextra-nav-container-blur`** (the `z-[-1]` absolute layer, navbar.html:15, default `dark:bg-dark` solid + shadow, **no** blur). Set: `background: rgba(10, 10, 10, 0.72); backdrop-filter: blur(10px); -webkit-backdrop-filter: blur(10px); border-bottom: 1px solid var(--ck-border); box-shadow: none;` to get the frosted translucent header over `#0a0a0a`. Scope to dark (`.dark .hextra-nav-container-blur`) so light mode is untouched (light is deferred).
  - [x] Style the nav text links (Posts/About) to the brand: muted `var(--ck-muted)`, `font-weight: 600`, hover → `var(--ck-fg)`. Target the rendered links inside the nav without breaking Hextra's active-state classes (inspect the built markup and scope narrowly, e.g. under `.hextra-nav-container nav a`).
  - [x] Keep the gradient a **garnish only** — no large gradient fill behind the header/body (AC4, DESIGN.md "Don'ts"). The header background is the translucent dark color, not the gradient.

- [x] **Task 4 — Footer override: gradient rule + one muted line; suppress Hextra chrome** (AC: 3, 4)
  - [x] Create the project partial **`layouts/_partials/custom/footer.html`** — this **is** the correct hook here: module `footer.html:21` invokes `{{ partial "custom/footer.html" ... }}`. Render: a decorative gradient rule `<div class="ck-footer-rule" aria-hidden="true"></div>` then a single muted line containing the GitHub and LinkedIn links as the only `<a>` elements, e.g. `carriekroutil.com · <a>GitHub</a> · <a>LinkedIn</a>` (real links with accessible text; reuse the same URLs as Task 2). Keep it to one line, centered.
  - [x] In `hugo.toml` set `[params.footer] displayPoweredBy = false` so the "Powered by Hextra" credit block is suppressed (footer.html:28 default is `true`). Footer **switches are already off** (`enableFooterSwitches` defaults `false`, footer.html:1) — so no theme toggle appears in the footer; do not enable it (UX-DR9: only GitHub/LinkedIn operable).
  - [x] CSS: `.ck-footer-rule { height: 3px; width: 80px; margin: 0 auto 18px; border-radius: 3px; background: var(--ck-accent-gradient); }`. Footer text: center it, `font-size: var(--ck-scale-small)` (0.85rem), color **`var(--ck-muted)` (`#a0a0ab`)** — **do NOT use `#70707c`** (the mockup value fails AA; AC3). Neutralize Hextra's footer surface: the module wraps it in `.hextra-footer` with `hx:dark:bg-neutral-900` and `.hextra-custom-footer` with `hx:dark:text-gray-400` — override both to `var(--ck-bg)` (or transparent) background and `var(--ck-muted)` text so it matches the dark page.

- [x] **Task 5 — Accessibility floor: brand focus ring + landmarks + motion** (AC: 2)
  - [x] Emit the deferred focus ring (deferred from Story 1.2 → this story): a `:focus-visible` rule applying the **existing `--ck-focus-ring` token** — `:focus-visible { outline: var(--ck-focus-ring); outline-offset: 2px; }` (2px solid `#d946ef`). Verify it shows on the wordmark, nav links, search, theme toggle, and both social links when tabbing. Do not suppress Hextra's existing focus classes destructively — add the brand ring.
  - [x] Landmarks are provided by Hextra (`<nav>` in header, `<main>`, `<footer class="hextra-footer">`); confirm the wordmark/footer overrides keep one logical structure and don't introduce a second `<h1>` or break the header/main/footer regions.
  - [x] `prefers-reduced-motion`: the header/footer in this story introduce **no** transform/transition motion (only a hover color change on nav links, which is not motion), so no new motion needs gating here. The reduced-motion media query lands with the components that actually animate — theme transition (Story 1.4) and card hover lift (Epic 2/3). Note this in the Dev Agent Record; do not add empty/no-op motion rules.

- [x] **Task 6 — Verify (build + browser + grep)** (AC: 1, 2, 3, 4)
  - [x] `hugo --gc --minify` → exit 0, warning-free (reuse the Story 1.1/1.2 verification pattern). Confirm no menu `pageRef` build warnings (literal URLs used).
  - [x] `hugo server` → on Home and at least one other page: wordmark renders gradient top-left and links Home; header is translucent/blurred and sticky on scroll; right side shows Posts · About · ⌕ · ☾ · GitHub · LinkedIn in order; footer shows the gradient rule + one muted line with only GitHub/LinkedIn operable.
  - [x] Browser-verify the focus ring: tab through the header — each control shows the `2px #d946ef` ring on `:focus-visible`.
  - [x] Verify the wordmark fallback: emulate forced-colors (`prefers-color-scheme`/forced-colors via devtools rendering pane) and print preview — the wordmark stays **solid fuchsia**, not invisible.
  - [x] Grep the project for `#70707c` → none (AC3); confirm the footer muted line computes to `#a0a0ab`.
  - [x] Confirm no new web-font request (grep built output for `googleapis`/`gstatic`/remote `@font-face` → none; NFR-1 preserved) and the Hextra module dir is untouched (`git status` shows changes only under project `layouts/`, `assets/`, `hugo.toml`, and the story file).
  - [x] Record commands + outcomes in the Dev Agent Record. Do not claim a control/ring/fallback works without confirming it in the built/served page.

## Review Findings

*From 3-layer code review (Blind Hunter · Edge Case Hunter · Acceptance Auditor), 2026-06-23. Acceptance Auditor: all 4 ACs satisfied, 0 violations. 3 patch · 1 defer · 6 dismissed.*

- [x] [Review][Patch] Over-broad `nav button` selector also dims the theme-toggle's dropdown option buttons (`<button data-item>` in `.hextra-theme-toggle-options`), lowering contrast of the open theme menu — exclude them via `:not([data-item])`. [assets/css/custom.css:150-157] — **Resolved:** selector now `nav button:not([data-item])`; browser-verified popup options render gray-300 (`oklch(0.872…)`), trigger stays muted.
- [x] [Review][Patch] Footer override dropped Hextra's `env(safe-area-inset-*)` + horizontal padding — content can sit under the home indicator / notch on iOS and loses side gutters. Re-add the safe-area + side padding the module footer carried. [layouts/_partials/footer.html] — **Resolved:** re-added `pb-[env(safe-area-inset-bottom)]` on `<footer>` + `pl/pr-[max(env(safe-area-inset-*),1.5rem)]` on the inner div; footer CSS `padding-bottom` now `calc(40px + env(safe-area-inset-bottom))`.
- [x] [Review][Patch] Wordmark exclusion uses fragile `:not([href="/"])` — under a subpath/multilingual deploy `.Site.Home.RelPermalink` ≠ `/`, so the rule would apply weight-600 to the wordmark anchor. Switch to a class-based exclusion (`.ck-wordmark-link` / `:not(.ck-wordmark-link)`). Masked today by the root `baseURL`. [assets/css/custom.css:150 · layouts/_partials/navbar-title.html] — **Resolved:** added `ck-wordmark-link` class to the wordmark anchor; selector now `nav a:not(.ck-wordmark-link)`; browser-verified wordmark stays weight 800 / gradient.
- [x] [Review][Defer] Brand `:focus-visible` outline can render alongside Hextra's own ring on Hextra-managed controls (search input, hamburger) — a possible double ring. Deferred: a visible 2px `#d946ef` ring is present (AC2 met); cosmetic, revisit if the doubled ring proves distracting. [assets/css/custom.css:205-216]

**Dismissed (6 — noise / handled / by-design):** `@supports not (background-clip:text)` hard fallback (forced-colors + print media query already cover the degraded paths; support is near-universal); mobile sidebar nav links don't get the muted/600/hover styling (UX-DR6 specifies the *desktop* header; mobile uses Hextra's condensed/hamburger nav by design); `rel="noreferrer"` without `noopener` (noreferrer implies noopener in all current browsers; matches Hextra's own nav convention); light-mode footer/nav left unstyled (light mode explicitly deferred per project); global `:focus-visible` contrast/clipping (fuchsia `#d946ef` = 5.71:1 on bg, AA; no clipping observed in-browser); `displayPoweredBy=false` redundant after the full footer override (kept intentionally as defensive config if the override is ever removed).

## Dev Notes

### Scope guardrails (prevent overbuild)
Story 1.3 builds the **visual shell chrome** — wordmark, header controls/styling, footer. Explicitly OUT of scope:
- **Theme toggle behavior/persistence** → Story 1.4. Here you only *place* Hextra's built-in toggle (☾) in the header via a menu item; do not implement persistence, light-mode tuning, or transitions.
- **Search functionality / FlexSearch index tuning** → Story 4.2 (UX-DR8). Here you only *surface* the ⌕ control; search is already enabled in config.
- **`/posts/` and `/about/` pages** → Epics 2/3. Link to them with literal URLs; do not create the content. (Links will 404 until those stories — acceptable; the AC is that the controls appear.)
- **post-card / home hero / avatar-ring / topic chips** → later epics; tokens already declared in Story 1.2, applied there.
- **Light-mode AA tuning** → deferred (dark is the AA path). Scope header/footer overrides to dark where they touch color.
- Do **not** edit the Hextra module (AD-5) — all changes live in project `layouts/`, `assets/css/custom.css`, and `hugo.toml`.

### How Hextra customization works here (verified in the v0.12.3 module)
- **Two different override mechanisms — do not mix them up:**
  - **Wordmark:** `navbar.html:19` calls `{{ partial "navbar-title.html" . }}`. Hugo resolves project `layouts/_partials/navbar-title.html` first → **create the project file at that exact path** to override. The module's `_partials/custom/navbar-title.html` is an empty, **unreferenced** placeholder — ignore it.
  - **Footer:** module `footer.html:21` calls `{{ partial "custom/footer.html" ... }}` → **create project `layouts/_partials/custom/footer.html`** to fill the footer's content slot. The surrounding `<footer>` chrome (switches, powered-by) stays in the module partial and is controlled by config.
- **Sticky/blur:** `.hextra-nav-container` is already `hx:sticky hx:top-0 hx:z-20` (navbar.html:13). The frosted background is a separate `z-[-1]` layer, `.hextra-nav-container-blur` (navbar.html:15), default `dark:bg-dark` + box-shadow, **no backdrop-filter**. Restyle that layer to get translucency + blur + the border-bottom. [Source: navbar.html:13-16]
- **Right-side controls come from `[[menu.main]]`,** rendered in weight order (navbar.html:22-52). Item dispatch: `type="search"` → search.html; `type="theme-toggle"` → theme-toggle.html (real `<button>`); `params.icon` (no type) → icon-only social `<a target="_blank">` with `title` + `sr-only` label; otherwise → `navbar-link.html` (text nav link). [Source: navbar.html:22-52, 36-50]
- **Footer chrome is config-gated:** `enableFooterSwitches` defaults `false` (no footer theme toggle by default — good), and `displayPoweredBy` defaults `true` (must set `false`). [Source: footer.html:1-4, 28]
- **Icons:** `github` (data/icons.yaml:12) and `linkedin` (data/icons.yaml:304) both ship in the module — reference by name, no project icon data needed.
- **Tokens already exist** in `assets/css/custom.css` from Story 1.2: `--ck-accent-gradient` (`linear-gradient(90deg, #8b5cf6, #d946ef, #f59e0b)`), `--ck-accent-fuchsia` (`#d946ef`), `--ck-muted` (`#a0a0ab`), `--ck-focus-ring` (`2px solid #d946ef`), `--ck-scale-small` (0.85rem), `--ck-border` (`#2a2a33`), `--ck-bg`. **Consume these — do not hardcode hex or re-declare tokens.**

### Wordmark — exact values (DESIGN.md UX-DR2 + mockup `home.html`)
- weight `800`, `letter-spacing: -0.02em`, `font-size: 1.12rem` (mockup `.brand`, lines 26-27).
- gradient text-clip via `--ck-accent-gradient`; **solid `#d946ef` fallback** for forced-colors/print/unsupported `background-clip:text` (UX-DR2; DESIGN.md "Do" — give the text-clip wordmark a solid fallback).
- Technique: solid `color` first, then `background` + `background-clip: text` + `-webkit-text-fill-color: transparent`; restore solid fill under `@media (forced-colors: active), print`.

### Header — exact values (DESIGN.md UX-DR6 + mockup)
- `background: rgba(10,10,10,0.72)`, `backdrop-filter: blur(10px)`, `border-bottom: 1px solid var(--ck-border)`. (Mockup header height 62px / Hextra nav is `h-16` = 64px — keep Hextra's height; the 62 vs 64 delta is immaterial, do not fight Hextra's `h-16`.)
- Nav links: `color: var(--ck-muted)`, `font-weight: 600`, `font-size: ~0.93rem`, hover → `var(--ck-fg)`.
- Right-side order: Posts · About · ⌕ · ☾ · GitHub · LinkedIn (UX-DR6).
- Mobile: Hextra's hamburger collapses the nav (navbar.html:55, links are `md:inline-flex`). No custom mobile CSS — rely on Hextra's condensed nav (UX-DR6).

### Footer — exact values (DESIGN.md UX-DR9 + mockup)
- gradient rule: `height: 3px; width: 80px; margin: 0 auto 18px; border-radius: 3px; background: var(--ck-accent-gradient)` — decorative (`aria-hidden`).
- one muted line, centered, `0.85rem`, color **`#a0a0ab`** (`var(--ck-muted)`) — **NOT `#70707c`** (mockup value fails AA; fix per UX-DR11/AC3).
- Only GitHub + LinkedIn are operable (real `<a>`); the rule is decorative; "Powered by Hextra" suppressed.

### Accessibility floor (UX-DR11) — what lands in THIS story
- **Focus ring** (deferred from Story 1.2): `:focus-visible { outline: var(--ck-focus-ring); outline-offset: 2px; }` — applies to wordmark + all header controls + footer social links. This is the deferred-work item: *"--ck-focus-ring declared but no :focus-visible rule emitted yet … deferred to Story 1.3+ where real interactive elements land."* [Source: deferred-work.md]
- **Real controls + labels:** Hextra renders search/theme-toggle as real `<button>` and social/nav as real `<a>` with `sr-only`/`title` labels — preserve, do not replace with spans.
- **Landmarks:** header `<nav>`, `<main>`, `<footer>` — provided; don't break them or add a second `<h1>`.
- **Contrast:** muted/meta never below `#a0a0ab` (7.65:1 on bg, AA). [Source: DESIGN.md contrast table]
- **Reduced motion:** nothing in this story animates with motion; the `prefers-reduced-motion` query lands with Story 1.4 (theme transition) / Epic 2-3 (card hover). Don't add no-op rules.

### Previous story intelligence (Story 1.2)
- `assets/css/custom.css` is the live stylesheet, loaded **last** by Hextra's `head.html` (variables → main → custom), so project rules win by cascade. **Append** the wordmark/header/footer/focus CSS here — do not create a second stylesheet.
- Hextra compiles Tailwind utilities wrapped in `:where(...)` (specificity 0,0,1); a direct class/element selector (0,1,0+) beats them. This is how Story 1.2 fixed the body background. Same approach for `.hextra-nav-container-blur` / `.hextra-footer`.
- Hextra is Tailwind v4 with an `hx:` utility prefix; the dark surfaces are literal compiled colors (`dark:bg-dark` → `#111`, footer `dark:bg-neutral-900`). Override by targeting the carrying element, verified in the built page — do not edit utilities.
- Verification that worked: `hugo --gc --minify` (exit 0, warning-free) + `hugo server` + browser computed-style checks + targeted greps. Reuse it.
- Local toolchain: Hugo `v0.151.2+extended`, Go `1.25.3`; `hugo server` at `http://localhost:1313/`. No web fonts ship by default — preserve that.
- `hugo.toml` already has: dark default + `displayToggle = true`, FlexSearch enabled, `tag` taxonomy. **Do not re-add** these. This story ADDS `[[menu.main]]` entries and `[params.footer] displayPoweredBy = false`.

### Git intelligence
- Branch `story-1.3-wordmark-header-footer` is cut from `main` @ `81335ba` (PR #1 merge — Story 1.2). Recent commits: `81335ba` merge 1.2, `aa853df` design tokens (1.2), `b4c749a` scaffold (1.1). Pattern: one story = one branch → PR → squash/merge to main; story file + code committed together.

### Project Structure Notes
- New files: `layouts/_partials/navbar-title.html` (wordmark override), `layouts/_partials/custom/footer.html` (footer content). Modified: `assets/css/custom.css` (append), `hugo.toml` (menu + footer config). Deleted: `layouts/.gitkeep` (dir now has real partials).
- This is the **first** `layouts/` override — aligns with the Structural Seed (AD-5: custom partials live in project `layouts/_partials/` + `_partials/custom/`, shadowing the read-only module). No variance.

### Testing / verification standards
No unit-test framework for a static site — "tests" = build + computed-style/keyboard checks + greps (Task 6). Record commands + outcomes; never claim a control, focus ring, or fallback works without confirming it in the built/served page.

### References
- [Source: _bmad-output/planning-artifacts/epics.md#Story 1.3] — story + ACs
- [Source: .../ux-designs/.../DESIGN.md] — UX-DR2 wordmark, UX-DR6 header-nav, UX-DR9 footer-accent, UX-DR11 a11y floor, contrast table (authoritative tokens)
- [Source: .../ux-designs/.../mockups/home.html] — exact header/footer markup + CSS target
- [Source: .../ux-designs/.../EXPERIENCE.md] — mobile collapse, focus, reduced-motion, contrast-floor fix
- [Source: .../architecture/.../ARCHITECTURE-SPINE.md#AD-5] — overrides only; module read-only; partials in project `layouts/`
- [Source: .../ARCHITECTURE-SPINE.md#AD-13] — core reading works no-JS (header/footer static; search the lone JS exception)
- [Source: Hextra v0.12.3 — `layouts/_partials/navbar.html` (13-16, 19, 22-52), `navbar-title.html`, `footer.html` (1-4, 21, 28), `navbar-link.html`, `data/icons.yaml` (12, 304)] — override surface
- [Source: _bmad-output/implementation-artifacts/1-2-...md + deferred-work.md] — token contract; deferred focus-ring rolls into this story

## Dev Agent Record

### Agent Model Used

claude-opus-4-8[1m] (Opus 4.8, 1M context)

### Debug Log References

- **Override surface (verified in Hextra v0.12.3 module):** `navbar.html:19` calls `partial "navbar-title.html"` directly → wordmark overridden at project `layouts/_partials/navbar-title.html` (the module's `custom/navbar-title.html` is an unreferenced placeholder — not used). Header controls come from `[[menu.main]]` in weight order (Posts/About → `navbar-link.html`; `type=search` → `search.html`; `type=theme-toggle` → real `<button>`; `params.icon` → icon-only `<a target=_blank>` with `title`+`sr-only`). `github` (icons.yaml:12) and `linkedin` (icons.yaml:304) ship in the module.
- **Build:** `hugo --gc --minify` exit 0, warning-free across all iterations. Content pages (`/`, `/tags/`) load `main.min.7062f32f….css`; grep-confirmed it contains `ck-wordmark`, the `forced-colors/print` fallback, `:focus-visible{outline:var(--ck-focus-ring)}`, the `.hextra-nav-container-blur` override, and the footer rule/line. (Stale `6bc2…` bundle is unreferenced by any page.)
- **Browser (Playwright vs `hugo server`):** wordmark = weight 800 / `-0.02em` (−0.3584px) / 1.12rem (17.92px), `background-image` = violet→fuchsia→amber gradient, `-webkit-text-fill-color` transparent, links `/`. `.hextra-nav-container` `position:sticky`; `.hextra-nav-container-blur` background `rgba(10,10,10,.72)` (sole matching rule, `background-image:none`) + `backdrop-filter:blur(10px)` + `1px solid rgb(42,42,51)` border. Nav order: wordmark · Posts · About · search(⌘K) · theme · GitHub · LinkedIn · (hamburger). Footer rule 3px×80px gradient; footer line color `rgb(160,160,171)`=`#a0a0ab`; only GitHub/LinkedIn operable. `Powered by Hextra` absent.
- **Focus ring:** keyboard-tabbed all controls — each shows `2px solid rgb(217,70,239)` (`#d946ef`) @ 2px offset. Search input initially inherited Hextra's grey ring; added a scoped `.hextra-search-input:focus-visible/:focus` override → confirmed fuchsia.
- **Mobile (390px):** hamburger ("Menu") visible, Posts/About hidden (collapsed), wordmark visible — Hextra condensed nav.
- **Console:** 0 errors; 2 benign FlexSearch tokenizer warnings (Hextra internals; search tuning is Story 4.2 scope).

### Completion Notes List

- **AC1 (wordmark):** `layouts/_partials/navbar-title.html` overrides Hextra's title partial — gradient text-clip wordmark (weight 800, `-0.02em`, 1.12rem) consuming `--ck-accent-gradient`, linking Home, `hx:ltr:mr-auto` preserved so it pushes the controls right. Solid `#d946ef` fallback declared first + restored under `@media (forced-colors: active), print` so it's never invisible.
- **AC2 (header):** Right-side controls wired via `[[menu.main]]` (Posts · About · search · theme-toggle · GitHub · LinkedIn) in weight order, each a real labelled `<a>`/`<button>`. Sticky comes free from `.hextra-nav-container`; restyled the `.hextra-nav-container-blur` layer for the translucent `rgba(10,10,10,.72)` + `blur(10px)` frost + `--ck-border` bottom rule. Nav links branded muted/600/hover-fg. Brand `:focus-visible` ring (the deferred Story 1.2 item) applied site-wide + a scoped search-input override. Mobile collapses to Hextra's hamburger.
- **AC3 (footer):** Footer reduced to a decorative gradient rule + one muted line; GitHub/LinkedIn the only operable elements. Footer text uses `--ck-muted` (`#a0a0ab`) — the mockup's `#70707c` is not used.
- **Deviation from planned Task 4 (documented):** config alone (`displayPoweredBy=false`) did **not** yield the clean UX-DR9 footer — the home/full-width layout sets `enableFooterSwitches` true via the page `.Store`, so Hextra rendered a Light/Dark/System theme switch bottom-left. Resolved by overriding the whole `layouts/_partials/footer.html` with a minimal wrapper that renders only the `custom/footer.html` content (no switches, no credit). `displayPoweredBy=false` kept as defensive intent. Browser-confirmed: only GitHub/LinkedIn operable, no footer toggle.
- **AC4 (integrity):** `hugo --gc --minify` clean; no web font introduced (NFR-1 preserved); gradient used only as garnish (wordmark + footer rule); Hextra module untouched (`git status` shows changes only under project `layouts/`, `assets/`, `hugo.toml`). Light mode left to Hextra defaults (deferred); all header/footer color overrides scoped to `.dark`.
- **Scope:** theme-toggle *behavior*/persistence (Story 1.4), search *functionality* (Story 4.2), and `/posts//about/` pages (Epics 2/3) intentionally not built — Posts/About use literal URLs so the build doesn't validate against not-yet-existent pages. `prefers-reduced-motion` not added: nothing in this story animates with motion (only a hover color change); it lands with the components that animate.

### File List

- `layouts/_partials/navbar-title.html` (new) — gradient wordmark override (UX-DR2)
- `layouts/_partials/footer.html` (new) — minimal footer wrapper, no switches/credit (UX-DR9)
- `layouts/_partials/custom/footer.html` (new) — footer content: gradient rule + muted line + GitHub/LinkedIn
- `assets/css/custom.css` (modified) — appended Story 1.3 section: wordmark + fallback, header blur/border/nav-link styling, footer rule/line, `:focus-visible` ring + search-input override
- `hugo.toml` (modified) — `[[menu.main]]` header controls (Posts/About/search/theme/GitHub/LinkedIn) + `[params.footer] displayPoweredBy = false`
- `layouts/.gitkeep` (deleted) — `layouts/` now holds real partials

## Change Log

| Date | Change |
| --- | --- |
| 2026-06-23 | Story 1.3 implemented: gradient wordmark partial, sticky translucent header with the full right-side control set wired via menu config, brand focus ring (resolving Story 1.2's deferred item), and a clean footer (gradient rule + muted line, GitHub/LinkedIn only). Full footer override needed because the home layout force-enables Hextra's footer theme switch. Build clean; all 4 ACs browser-verified (wordmark gradient + forced-colors fallback, control order + focus ring + mobile collapse, footer operable-links, module untouched). Status → review. |
| 2026-06-23 | Code review (3-layer adversarial). Acceptance Auditor: all 4 ACs satisfied, 0 violations. 3 patches applied (theme-toggle popup options excluded via `:not([data-item])`; footer safe-area/side padding restored; wordmark exclusion switched to `.ck-wordmark-link` class), 1 deferred (possible double focus-ring on Hextra-managed controls → `deferred-work.md`), 6 dismissed. Patches re-verified in-browser. Status → done. |
