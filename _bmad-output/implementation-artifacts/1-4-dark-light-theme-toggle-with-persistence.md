---
baseline_commit: a6686d0134d03f9d3fc97a17c099ed866d7cad38
---

# Story 1.4: Dark/light theme toggle with persistence

Status: done

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As a visitor,
I want to switch between dark and light themes and have my choice remembered,
so that I can read comfortably on return visits.

*Implements FR-13 (CAP-13) · UX-DR7 (theme-toggle), UX-DR11 (reduced-motion) · NFR-2 · AD-13 (theme-toggle is a documented JS exception).*

## Acceptance Criteria

**AC1 — Dark by default on first visit**
**Given** a first visit (no stored theme preference)
**When** the site loads
**Then** it loads in dark mode by default
**And** there is no flash of light/unstyled content before dark paints (the pre-paint head script sets the theme before first paint).

**AC2 — Toggle switches theme and persists across loads and visits**
**Given** the header theme toggle (Hextra's built-in — a real focusable `<button>` with an accessible label, opening a Light / Dark / System menu of real `menuitemradio` controls)
**When** the visitor activates it and selects light (or dark, or system)
**Then** the site switches to that theme immediately
**And** the choice persists across page loads and future visits (stored in `localStorage["color-theme"]`, re-applied pre-paint on every load).

**AC3 — Smooth theme transition, dropped under reduced-motion**
**Given** a theme switch
**When** the visitor activates the toggle
**Then** the color change animates with a smooth, quick transition (EXPERIENCE.md "Smooth, quick theme transition")
**And** under `prefers-reduced-motion: reduce` the transition is dropped (the swap is instant) — the media query is actually wired, not merely intended (UX-DR11).

**AC4 — Light mode ships untuned; AA verification explicitly deferred**
**Given** light mode is selected
**When** accessibility is considered
**Then** light mode ships with untuned Hextra defaults and its WCAG AA verification is explicitly deferred and tracked as an open question (dark is the AA-guaranteed path)
**And** that deferral is recorded in `deferred-work.md` so it is not silently lost.

**AC5 — Build + brand + no-reinvention integrity preserved**
**Given** the changes
**When** `hugo --gc --minify` runs
**Then** it builds clean (exit 0, warning-free), no web font is introduced, no custom theme-toggle JS or custom persistence layer is added (Hextra's built-in mechanism is used), and the Hextra module is not edited in place (AD-5).

## Tasks / Subtasks

- [x] **Task 1 — Verify the built-in persistence + dark default already work end-to-end** (AC: 1, 2, 5)
  - [x] Confirm `hugo.toml` already has `[params.theme] default = "dark"` and `displayToggle = true` (set in Stories 1.1–1.3) and the `type = "theme-toggle"` menu item is present (Story 1.3). **Do not re-add or duplicate these.**
  - [x] Confirm — by reading the module, not assuming — that Hextra already provides full persistence and pre-paint application. The pre-paint script `assets/js/head/theme.js` runs in `<head>` and sets `setTheme("color-theme" in localStorage ? localStorage.getItem("color-theme") : "{{ site.Params.theme.default }}")` → reads the stored pref or falls back to our `dark` default, adds the `light`/`dark` class to `<html>` and sets `style.colorScheme` **before first paint** (no FOUC). The body script `assets/js/core/theme.js` wires the toggle and writes `localStorage.setItem("color-theme", theme)` on every selection. [Source: Hextra v0.12.3 `assets/js/head/theme.js`, `assets/js/core/theme.js`]
  - [x] **CRITICAL — do NOT reinvent this.** Do not write a custom theme toggle, a custom `localStorage` handler, an inline persistence snippet, or a `prefers-color-scheme` listener. Persistence (key `color-theme`), pre-paint application, the Light/Dark/System menu, ARIA state, keyboard nav, and outside-click dismiss are **already implemented by Hextra**. This story is verify + the transition (Task 2) + the deferral doc (Task 3).

- [x] **Task 2 — Add the smooth theme transition, gated behind `prefers-reduced-motion`** (AC: 3)
  - [x] In `assets/css/custom.css` (append a new Story 1.4 section; same single stylesheet, loaded last — do **not** create a second stylesheet), add a short color transition on the elements that change color across themes — at minimum the page background + text: target `html`, `body`, and the header backdrop layer `.hextra-nav-container-blur`. Keep it quick (~`0.2s ease`; EXPERIENCE.md "Smooth, quick"). Transition only color-bearing properties (`background-color`, `color`, `border-color`) — **never `transition: all`** (would animate layout/transform and cause jank).
  - [x] **Wire the reduced-motion gate explicitly (UX-DR11 — "Wire the media query, don't just intend it").** Author the transition inside `@media (prefers-reduced-motion: no-preference) { … }` so reduced-motion users get an instant swap by construction. (Hextra also ships a global `@media (prefers-reduced-motion: reduce)` reset in `styles.css` that zeroes `transition-duration !important`; authoring under `no-preference` is the explicit, self-documenting belt-and-suspenders and is what satisfies the AC's "actually wired" requirement.)
  - [x] Do **not** add the card-hover-lift reduced-motion gate here — that motion lands with the post-card (Epic 2/3). Scope this story's reduced-motion work to the theme transition only (don't author no-op rules for motion that doesn't exist yet).
  - [x] Keep the transition subtle and brand-consistent; light mode is untuned (Task 3) so the transition mainly governs the dark↔light/system swap.

- [x] **Task 3 — Document the light-mode AA deferral as a tracked open question** (AC: 4)
  - [x] Append a "Deferred from: story-1.4" entry to `_bmad-output/implementation-artifacts/deferred-work.md` stating: **light mode ships with untuned Hextra defaults; its WCAG AA verification is explicitly deferred — dark is the AA-guaranteed v1 path.** Cross-reference the existing 1.2 "Light-mode accent AA" entry (the fuchsia accent measures ~3.4–3.9:1 on Hextra's white light surface, below the 4.5:1 floor) so the two are connected, not duplicated. Note it is a non-blocking open question, revisit if/when light mode is tuned.
  - [x] Record the same deferral in this story's Dev Agent Record (Completion Notes), so the story is self-contained.
  - [x] **Out of scope (do NOT do):** tuning light-mode colors/contrast, restyling light surfaces, or building light-mode brand overrides. Light intentionally ships as Hextra defaults. Story 1.3's header/footer overrides are deliberately scoped to `.dark` — leave that scoping intact; do not extend brand styling into light mode.

- [x] **Task 4 — Verify (build + browser + persistence + reduced-motion)** (AC: 1, 2, 3, 4, 5)
  - [x] `hugo --gc --minify` → exit 0, warning-free (reuse the Story 1.1–1.3 verification pattern). Grep the built CSS bundle to confirm the `prefers-reduced-motion: no-preference` transition rule shipped.
  - [x] `hugo server` + browser: **First visit** — clear `localStorage` (or use a fresh/incognito profile), load the site → renders **dark** (AC1). Confirm no light→dark flash on load (pre-paint head script). Confirm `localStorage["color-theme"]` is read on load.
  - [x] **Toggle + persist** — open the header ☾ toggle, select **Light** → page switches to light immediately; **reload** → still light; check `localStorage["color-theme"] === "light"`. Repeat for **Dark** and **System** (System should track the OS `prefers-color-scheme`). Confirm the choice survives a full page navigation, not just an in-page state change (AC2).
  - [x] **Transition** — confirm the color change animates smoothly (~0.2s) when toggling; then emulate `prefers-reduced-motion: reduce` (DevTools Rendering pane) and confirm the swap is **instant** (no transition) (AC3).
  - [x] **A11y** — the toggle is a real `<button>` with an accessible label (`aria-label="Change theme"`), `aria-expanded` flips on open, menu items are `role="menuitemradio"` with `aria-checked`, keyboard reachable/operable (Tab to it, Enter/Space opens, Arrow keys move, Esc dismisses), and the brand `2px #d946ef` `:focus-visible` ring shows on the toggle button. (These come from Hextra — confirm, don't rebuild.)
  - [x] Confirm no new web-font request (NFR-1) and the Hextra module dir is untouched (`git status` shows changes only under project `assets/css/custom.css`, `deferred-work.md`, and the story file — likely **no** `hugo.toml` change this story).
  - [x] Record commands + outcomes in the Dev Agent Record. Do not claim persistence, the dark default, the transition, or the reduced-motion drop works without confirming each in the served page + `localStorage`.

## Review Findings

*From 3-layer code review (Blind Hunter · Edge Case Hunter · Acceptance Auditor), 2026-06-23. Acceptance Auditor: all 5 ACs satisfied, 0 violations. 1 patch · 1 defer · 6 dismissed.*

- [x] [Review][Patch] Split the theme transition so each selector animates only the properties it actually uses — `html, body` → `background-color, color` (drop the inert `border-color`; html/body carry no visible border); `.hextra-nav-container-blur` → `background-color, border-color` (drop the inert `color`). Tightens the "color-bearing properties only" intent and removes dead-weight declarations. Behavior unchanged (rule stays unscoped → bidirectional). [assets/css/custom.css:236-242] — **Resolved:** split into two rules; build clean (exit 0); live bundle `a7fa8915…` confirmed to contain `html,body{transition:background-color .2s,color .2s}` + `.hextra-nav-container-blur{transition:background-color .2s,border-color .2s}`.
- [x] [Review][Defer] `.hextra-nav-container-blur` `border-color` transition will animate a phantom border once light mode is tuned — the styled border is defined only under `.dark .hextra-nav-container-blur` (custom.css:139-143), so in a future tuned light mode the border eases from/to an unset value. Latent, not active: light mode is explicitly deferred (AC4). Revisit when light mode is tuned (scope the blur's border transition to `.dark`, or add a light border first). [assets/css/custom.css:236-242]

**Dismissed (6 — noise / refuted / by-design):** `html`+`body` both transition `background-color` (same color, same easing curve, in sync → not muddy); "staggered switch" where non-listed components hard-swap (the comment scopes accurately to the dominant surfaces — page bg + frosted header; light-mode polish deferred; AC3 met for the smooth swap of the primary surfaces); fragile coupling to Hextra's `.hextra-nav-container-blur` class (by-design per AD-8 — every Story 1.3 override targets Hextra's carrying elements; build-verified, breaks loudly on upgrade via the existing build check); "media query is wired" claim unverifiable by the blind layer (refuted by Edge + Auditor — Hextra's global `@media (prefers-reduced-motion:reduce){…transition-duration:.01ms!important}` wins by `!important`, and our rule is gated under `no-preference`); 200ms `color` easing passes through low-contrast intermediates (0.2s is acceptable and reduced-motion-exempt); transition fires on OS-driven System theme change (acceptable/by-design — a smooth swap on an OS dark/light flip is consistent with the "smooth theme transition" intent, and reduced-motion users are exempt).

## Dev Notes

### Scope guardrails (prevent overbuild / reinvention — read first)
This story is deliberately **small**: most of the behavior already ships with Hextra. The failure mode here is **reinventing the wheel**, not under-building.
- **Persistence + pre-paint + the toggle UI are DONE by Hextra.** Do not write any theme JS, `localStorage` code, inline `<script>`, `prefers-color-scheme` listener, or a custom toggle partial. [Source: Hextra `assets/js/head/theme.js` + `assets/js/core/theme.js`]
- **The only genuinely new code is the CSS theme transition + its `prefers-reduced-motion` gate** (Task 2). Story 1.3 explicitly deferred the reduced-motion media query to *this* story ("the prefers-reduced-motion query lands with Story 1.4 (theme transition)"). [Source: 1-3-…md Task 5, Dev Notes]
- **Light mode is NOT tuned here** (Task 3) — it ships as Hextra defaults; AA is explicitly deferred. Do not style light mode. Keep Story 1.3's `.dark`-scoped overrides as-is.
- **No `hugo.toml` changes expected** — `default = "dark"`, `displayToggle = true`, and the `type = "theme-toggle"` menu item already exist. If you find yourself editing config, stop and re-check; you're likely duplicating Story 1.1/1.3 work.
- Do **not** edit the Hextra module (AD-5) — changes live in project `assets/css/custom.css` and `deferred-work.md` only.

### How Hextra's theme toggle works (verified in the v0.12.3 module — do not reimplement)
- **Storage key:** `color-theme` in `localStorage`. Values: `"light"`, `"dark"`, `"system"`.
- **Default:** both scripts fall back to `'{{ site.Params.theme.default | default \`system\` }}'` when nothing is stored → our `hugo.toml` sets `default = "dark"`, so a first visit (empty `localStorage`) loads **dark**. [Source: head/theme.js last line; core/theme.js `defaultTheme`]
- **Pre-paint (no FOUC):** `assets/js/head/theme.js` is injected in `<head>` and runs before body render — it removes any `light`/`dark` class, resolves `system` to the OS preference via `matchMedia("(prefers-color-scheme: dark)")`, adds the resolved class to `<html>`, and sets `document.documentElement.style.colorScheme`. This is why the dark default paints without a flash. [Source: head/theme.js]
- **Toggle behavior:** `assets/js/core/theme.js` queries `.hextra-theme-toggle` buttons + `.hextra-theme-toggle-options button[role=menuitemradio]`, applies the theme, writes `localStorage["color-theme"]`, manages `data-state`/`aria-expanded`, focuses the first menuitem on open, supports Arrow-key navigation, and dismisses on outside click. [Source: core/theme.js]
- **Markup:** `theme-toggle.html` renders a real `<button class="hextra-theme-toggle" aria-label="Change theme" aria-haspopup="menu">` + a `role="menuitemradio"` option list (Light/Dark/System). Surfaced in our header via the `[[menu.main]]` `type = "theme-toggle"` item with `label = false` (icon-only ☾) from Story 1.3. [Source: theme-toggle.html; 1-3-…md Task 2; hugo.toml:53-58]
- **It is a 3-way menu (Light/Dark/System), not a binary switch.** That's Hextra's built-in, which UX-DR7 explicitly specifies ("Hextra built-in"). AC2's "switches to light" is satisfied by selecting Light from the menu — don't try to force a binary toggle.

### Theme transition — exact approach (the one new bit)
- EXPERIENCE.md: "Smooth, quick theme transition." (line 104) and UX-DR11 / EXPERIENCE.md lines 125-126: honor `prefers-reduced-motion` — **drop** the theme-transition animation; **wire** the media query.
- Recommended CSS (append to `custom.css`), consuming existing tokens, transition only color properties:
  ```css
  @media (prefers-reduced-motion: no-preference) {
    html, body, .hextra-nav-container-blur {
      transition: background-color 0.2s ease, color 0.2s ease, border-color 0.2s ease;
    }
  }
  ```
  Authoring the transition *only* under `no-preference` means reduced-motion users never get it (instant swap) — the gate is structural, not a fragile override. Hextra's own `@media (prefers-reduced-motion: reduce){…transition-duration:0.01ms!important}` (styles.css) is a second safety net. **Never** use `transition: all` (animates transforms/layout → jank).
- Keep `.hextra-nav-container-blur` in the transition set so the frosted header doesn't hard-cut when switching themes; don't add transitions to the wordmark gradient or focus ring.

### Accessibility floor (UX-DR11) — what applies in THIS story
- **Reduced motion (build requirement):** the theme transition is gated as above — this is the story's primary a11y deliverable and resolves Story 1.3's deferred reduced-motion item.
- **Real control + label:** the toggle is a real `<button>` with `aria-label="Change theme"`, `aria-expanded`, and `role="menuitemradio"` options with `aria-checked` — all from Hextra. Verify; do not replace with spans or rebuild.
- **Focus ring:** the brand `:focus-visible { outline: 2px solid #d946ef }` (added Story 1.3) applies to the toggle button. Note the Story 1.3 **deferred** item: Hextra-managed controls *with* `hextra-focus-visible*` classes (search input, hamburger) may show a doubled ring — the theme-toggle button is a plain `.hextra-theme-toggle` (not `hextra-focus-visible`-classed), so the brand ring applies cleanly to it. No action needed; just don't regress it. [Source: deferred-work.md "Double focus ring"]
- **AA contrast:** dark is the AA-guaranteed path (verified in 1.2/1.3). Light is untuned and deferred (Task 3) — do not claim light-mode AA.

### No-JS nuance (document, don't engineer around)
- Hextra applies the theme via a `<html>` class set by JS (`head/theme.js`). With JS disabled, no class is added, so the static page falls back to Hextra's light/default styling rather than the dark default. This is acceptable and by-design: per EXPERIENCE.md (line 97) + AD-13, **the theme-toggle (and search) are the documented JS exceptions**; "core reading works without JS" means the content remains readable, not that the dark default is guaranteed without JS. Do **not** attempt to engineer a no-JS dark default (e.g., hardcoding `<html class="dark">` by overriding `baseof.html`) — that's out of scope and would fight Hextra's mechanism. Note the limitation in the Dev Agent Record.

### Previous story intelligence (Story 1.3 — just merged, PR into main @ a6686d0)
- `assets/css/custom.css` is the live stylesheet, loaded **last** by Hextra's `head.html` (variables → main → custom), so project rules win by cascade. **Append** the Story 1.4 transition section here — do not create a second stylesheet. [Source: 1-3-…md Dev Notes]
- Story 1.3 placed the theme-toggle in the header (`[[menu.main]]` `type="theme-toggle"`, `label=false`) and explicitly did **not** build behavior/persistence — leaving exactly this story's scope. [Source: 1-3-…md Task 2, Scope guardrails]
- Story 1.3 added the site-wide `:focus-visible` ring + a scoped search-input override; header/footer color overrides are all scoped to `.dark` (light deliberately left to Hextra). [Source: 1-3-…md Completion Notes]
- Hextra is Tailwind v4 with an `hx:` prefix; utilities compile inside `:where(...)` (specificity 0,0,1), so a direct element/class selector beats them. The transition selectors above (`html`, `body`, `.hextra-nav-container-blur`) are direct and will win. [Source: 1-3-…md Dev Notes]
- Local toolchain: Hugo `v0.151.2+extended`, Go `1.25.3`; `hugo server` at `http://localhost:1313/`. No web fonts ship — preserve that.
- Verification that worked: `hugo --gc --minify` (exit 0, warning-free) + `hugo server` + browser computed-style / `localStorage` checks + targeted greps. Reuse it; add a reduced-motion emulation step.

### Git intelligence
- Branch `story-1.4-theme-toggle` is cut from `main` @ `a6686d0` (merge of Story 1.3, PR #2/#3). Recent commits: `a6686d0` merge 1.3, `5bff828` wordmark/header/footer (1.3), `81335ba` merge 1.2, `aa853df` design tokens (1.2). Pattern: one story = one branch → PR → squash/merge to main; story file + code committed together. Keep that pattern.

### Project Structure Notes
- Expected changes: `assets/css/custom.css` (append Story 1.4 transition + reduced-motion section), `_bmad-output/implementation-artifacts/deferred-work.md` (light-mode AA deferral entry), and this story file. **No new layout/partial files** and **no `hugo.toml` change** expected (the toggle + default + displayToggle already exist). If the dev finds a need to add a partial or JS file, that's a strong signal of reinventing Hextra's built-in — stop and reassess.
- Aligns with AD-5 (overrides only; module read-only). No structural variance.

### Testing / verification standards
No unit-test framework for a static site — "tests" = build + browser/`localStorage`/reduced-motion checks + greps (Task 4). Record commands + outcomes; never claim the dark default, persistence, the transition, or the reduced-motion drop works without confirming it in the served page and `localStorage`.

### References
- [Source: _bmad-output/planning-artifacts/epics.md#Story 1.4] — story + ACs (dark default, switch to light, persistence, light-mode AA deferred)
- [Source: .../ux-designs/.../DESIGN.md:141] — theme-toggle: dark default, persists (Hextra), real focusable labeled control
- [Source: .../ux-designs/.../EXPERIENCE.md:81, 97, 104, 125-126] — persists across visits; theme-toggle is a JS exception; "Smooth, quick theme transition"; honor prefers-reduced-motion (drop the theme transition, wire the media query)
- [Source: .../architecture/.../ARCHITECTURE-SPINE.md#AD-13] — no-JS core; theme-toggle + search are the JS exceptions
- [Source: Hextra v0.12.3 — `assets/js/head/theme.js` (pre-paint, key `color-theme`, default from params), `assets/js/core/theme.js` (toggle + persist), `layouts/_partials/components/theme-toggle.html` (markup/ARIA), `assets/css/styles.css` (global prefers-reduced-motion reset)] — the built-in mechanism; do not reimplement
- [Source: _bmad-output/implementation-artifacts/1-3-…md] — header toggle placement, custom.css cascade, .dark scoping, deferred reduced-motion + double-focus-ring items
- [Source: _bmad-output/implementation-artifacts/deferred-work.md] — existing 1.2 "Light-mode accent AA" entry to cross-reference; 1.3 "Double focus ring" note

## Dev Agent Record

### Agent Model Used

claude-opus-4-8[1m] (Opus 4.8, 1M context)

### Debug Log References

- **Built-in mechanism confirmed (Hextra v0.12.3, read not assumed):** `head.html:59` ranges `resources.Match "js/head/*.js"` → `head/theme.js` is inlined into `<head>` and runs pre-paint: `setTheme("color-theme" in localStorage ? localStorage.getItem("color-theme") : "<params.theme.default=dark>")`, adding the `light`/`dark` class to `<html>` + setting `colorScheme` before first paint (no FOUC). `core/theme.js` wires the toggle and writes `localStorage["color-theme"]` on every selection. Storage key = `color-theme`; values `light`/`dark`/`system`. No custom JS written.
- **Build:** `hugo --gc --minify` exit 0, warning-free (6 pages). Homepage references `css/compiled/main.min.9c13fbc0….css`; grep-confirmed it contains exactly `@media(prefers-reduced-motion:no-preference){html,body,.hextra-nav-container-blur{transition:background-color .2s ease,color .2s ease,border-color .2s ease}}` and Hextra's own `@media(prefers-reduced-motion:reduce){…transition-duration:.01ms!important}` reset. (Several stale `main.min.*` bundles sit unreferenced in `public/`, which is gitignored — not committed.)
- **Browser (Playwright vs the running `hugo server` :1313):**
  - *AC1 first visit* — cleared `localStorage["color-theme"]`, reloaded → `document.documentElement.className === "dark"`, `style.colorScheme === "dark"`, `body` bg `rgb(10,10,10)` (`--ck-bg`). No light flash (pre-paint script). Hextra also writes the resolved default (`dark`) to storage on load.
  - *AC2 persist* — clicked the real `menuitemradio[data-item="light"]` → `html.class` flips to `light`, `localStorage["color-theme"] === "light"` immediately; **navigated to a fresh page load** → still `light`, body bg `rgb(255,255,255)`, the `aria-checked="true"` option is `light`. Switched back to `dark` → persists `dark`. Toggle is a real `<button type=button>` with `aria-label="Change theme"`, `aria-haspopup="menu"`, `aria-expanded` flips, options are `role=menuitemradio` with `aria-checked`.
  - *AC3 transition* — under `no-preference`, `getComputedStyle(body).transitionDuration === "0.2s, 0.2s, 0.2s"`; emulated `prefers-reduced-motion: reduce` → `transitionDuration === "1e-05s"` (≈0.01ms, instant) on both `body` and `html`. Media query verified wired both directions.
- **Console:** only a 404 on `/about/` (page arrives in Epic 3 — expected; Posts/About are forward-referenced literal URLs). No JS errors on Home.
- **Integrity:** `git status` shows changes only under `assets/css/custom.css`, `deferred-work.md`, `sprint-status.yaml`, and the story file — **no `hugo.toml` change** (toggle/default/displayToggle already existed) and **no Hextra module edits** (AD-5). No new web-font request.

### Completion Notes List

- **AC1 (dark default + no FOUC):** Already satisfied by config (`params.theme.default = "dark"`) + Hextra's pre-paint `head/theme.js`. Verified in-browser on a cleared-storage first visit: renders dark, no flash.
- **AC2 (switch + persist):** Satisfied entirely by Hextra's built-in toggle + `localStorage["color-theme"]` — **no reinvention.** Verified light selection persists across a full page load and the menu reflects the persisted choice. The control is Hextra's 3-way Light/Dark/System menu (a real labeled `<button>`), which is exactly what UX-DR7 specifies.
- **AC3 (transition + reduced-motion):** Added the one new bit — a `0.2s` color transition on `html, body, .hextra-nav-container-blur`, authored **only** inside `@media (prefers-reduced-motion: no-preference)` so reduced-motion users get an instant swap by construction (belt-and-suspenders with Hextra's global `reduce` reset). This resolves Story 1.3's deferred reduced-motion item. Verified: smooth under no-preference, instant under reduce.
- **AC4 (light-mode AA deferred):** Light ships as untuned Hextra defaults (white surface, browser-confirmed). WCAG AA verification is explicitly deferred and recorded in `deferred-work.md` ("Deferred from: story-1.4"), cross-referencing the 1.2 "Light-mode accent AA" entry. Dark remains the AA-guaranteed path. No light-mode styling added; Story 1.3's `.dark`-scoped overrides left intact.
- **AC5 (integrity):** Build clean (exit 0, warning-free); no custom theme JS or persistence layer; no web font introduced; Hextra module untouched. No `hugo.toml` change was needed.
- **No-JS nuance (documented, not engineered around):** the dark default is applied via a `<html>` class set by `head/theme.js` (JS). With JS disabled, no class is added and the page falls back to Hextra's default light styling. This is acceptable and by-design — the theme-toggle (and search) are the documented JS exceptions (AD-13, EXPERIENCE.md); "core reading works without JS" means content stays readable, not that the dark default is guaranteed without JS. Not engineered around (would require fighting Hextra's mechanism).

### File List

- `assets/css/custom.css` (modified) — appended Story 1.4 section: `0.2s` color transition on `html, body, .hextra-nav-container-blur`, gated under `@media (prefers-reduced-motion: no-preference)`
- `_bmad-output/implementation-artifacts/deferred-work.md` (modified) — added "Deferred from: story-1.4" light-mode AA deferral, cross-referencing the 1.2 entry
- `_bmad-output/implementation-artifacts/1-4-dark-light-theme-toggle-with-persistence.md` (new) — this story file
- `_bmad-output/implementation-artifacts/sprint-status.yaml` (modified) — story 1.4 status transitions

## Change Log

| Date | Change |
| --- | --- |
| 2026-06-23 | Story 1.4 context created (ready-for-dev). Scope: verify Hextra's built-in dark-default + `color-theme` persistence (no reinvention), add the smooth theme transition gated behind `prefers-reduced-motion` (resolves Story 1.3's deferred reduced-motion item), and document the light-mode AA deferral. Branch `story-1.4-theme-toggle` off main @ a6686d0. |
| 2026-06-23 | Story 1.4 implemented. Confirmed Hextra's built-in toggle already provides the dark default (pre-paint, no FOUC) and `localStorage["color-theme"]` persistence — no custom JS added. Added the one new piece: a `0.2s` color transition on `html, body, .hextra-nav-container-blur`, authored only under `@media (prefers-reduced-motion: no-preference)` (resolves Story 1.3's deferred reduced-motion item). Documented the light-mode AA deferral in `deferred-work.md`. Build clean (exit 0); all 5 ACs browser-verified (dark default on cleared-storage first visit; light persists across a full page load; smooth 0.2s transition under no-preference, instant 1e-05s under reduce; light = untuned Hextra defaults; module untouched, no `hugo.toml` change). Status → review. |
| 2026-06-23 | Code review (3-layer adversarial). Acceptance Auditor: all 5 ACs satisfied, 0 violations. 1 patch applied (split the transition so each selector animates only the properties it uses — build re-verified, live bundle `a7fa8915…`), 1 deferred (phantom blur-border ease once light mode is tuned → `deferred-work.md`), 6 dismissed (refuted/by-design). Status → done. |
