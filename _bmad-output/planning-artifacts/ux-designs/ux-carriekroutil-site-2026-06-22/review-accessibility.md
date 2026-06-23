---
review: accessibility
target: WCAG 2.1 AA
stakes: low (personal site, unregulated)
date: 2026-06-22
reviewer: accessibility reviewer (BMAD UX)
scope:
  - DESIGN.md
  - EXPERIENCE.md
  - .working/home-direction-a.html
  - .working/post-single.html
  - .working/about.html
---

# Accessibility Review — carriekroutil.com UX

Target **WCAG 2.1 AA**, low-stakes personal site. The mocks are throwaway visualizations, so
spec-level findings (things that should land in DESIGN.md / EXPERIENCE.md) are weighted over mock
HTML semantics — but mock issues that *should become spec guidance* are flagged as such.

**Headline:** the palette is in good shape. Every core text/background pair clears AA, including the
amber gradient stop that was flagged as a risk (it passes comfortably). The real accessibility work
is in the **build spec**: making the icon "buttons" and theme toggle real semantic controls,
specifying a visible focus style, writing alt-text guidance, and wiring `prefers-reduced-motion`.

---

## Contrast (computed)

Relative luminance per WCAG sRGB formula; contrast = (L_light + 0.05) / (L_dark + 0.05).
AA thresholds: **4.5:1** normal text, **3.0:1** large text (≥24px, or ≥18.66px bold) and UI
components.

| Foreground | Background | Ratio | AA normal | AA large/UI | Notes |
|---|---|---:|:---:|:---:|---|
| Body `#ededed` | `#0a0a0a` | **16.9:1** | PASS | PASS | Excellent. |
| Muted `#a0a0ab` | `#0a0a0a` | **7.65:1** | PASS | PASS | Meta/lead text — comfortable. |
| Muted `#a0a0ab` | card `#15151a` | **6.70:1** | PASS | PASS | Muted on cards still passes. |
| Link fuchsia `#d946ef` | `#0a0a0a` | **5.71:1** | PASS | PASS | Body links OK. |
| Card tag fuchsia `#d946ef` | card `#15151a` | **5.00:1** | PASS | PASS | Uppercase tag on card — passes. |
| Chip violet `#c9b8ff` | `#15151a` | **9.71:1** | PASS | PASS | |
| Chip fuchsia `#f3a8ff` | `#15151a` | **9.83:1** | PASS | PASS | |
| Chip amber `#ffd591` | `#15151a` | **12.5:1** | PASS | PASS | Lightest chip, best contrast. |
| **Amber stop `#f59e0b`** | `#0a0a0a` | **9.22:1** | **PASS** | **PASS** | **Flagged risk — verified OK.** |
| Amber stop `#f59e0b` | `#15151a` | **8.08:1** | PASS | PASS | |
| Violet stop `#8b5cf6` | `#0a0a0a` | **4.68:1** | PASS | PASS | Darkest gradient stop; AA but tightest. |
| Meta/footer `#70707c` | `#0a0a0a` | **4.11:1** | FAIL | PASS | Mock-only; see Should-fix. |

### On the amber-end gradient risk (EXPERIENCE.md §Accessibility Floor `[NOTE FOR UX]`)
The concern was that the amber gradient stop might fail as small text on dark. **It does not** —
`#f59e0b` on `#0a0a0a` is **9.22:1**, well above the 4.5:1 normal-text floor. The *lowest* point
anywhere along the violet→fuchsia→amber gradient is the violet end at **4.68:1**, which still
passes AA for normal text. So the gradient is safe even at small sizes.

**Recommendation:** No color change needed for contrast. Resolve the `[NOTE FOR UX]` by recording
this verification in the spine. The one real caveat is *gradient text rendering itself* (see
Should-fix: gradient text-clip fallback), not the color values.

---

## Blocker
_None._ No contrast failure on any specified text role, and core reading works without JS. For a
low-stakes personal site there is nothing that hard-blocks an accessible launch.

---

## Should-fix (spec-level — fold into DESIGN.md / EXPERIENCE.md before build)

1. **Icon controls must be real semantic buttons/links — not `<span>`.**
   All three mocks render search, theme toggle, GitHub, LinkedIn as `<span class="icon">` (and the
   nav uses bare `<a href="#">`). A `<span>` is not focusable, not keyboard-operable, and exposes no
   role/name to assistive tech. EXPERIENCE.md already *promises* "all nav, cards, chips, search, and
   toggle reachable and operable by keyboard." **Spec guidance:** search/socials = `<a>`; theme
   toggle = `<button aria-pressed>`; every icon-only control needs an accessible name
   (`aria-label="Search"`, `"Toggle theme"`, `"GitHub"`, `"LinkedIn"`). Hextra's stock components
   largely do this already — the spec should require the build to *use Hextra's real controls* and
   not hand-roll `<span>` icons. (Mock issue → make it explicit spec guidance.)

2. **Define a visible focus indicator in the spine.**
   EXPERIENCE.md says interactive elements "have a visible focus ring (keyboard)," but DESIGN.md
   defines no focus token and the mocks set none (so focus falls to the UA default, which is often
   invisible against `#0a0a0a` and is removed entirely on the `<span>` icons since they aren't
   focusable). **Spec guidance:** add a focus token — e.g. a 2px fuchsia `#d946ef` outline with
   2px offset (or a 2px ring) on `:focus-visible` for links, cards, chips, toggle, and search. Must
   meet the 3:1 non-text contrast for focus indicators; fuchsia on `#0a0a0a` is 5.71:1, fine.

3. **Theme-toggle accessible state + persisted-preference contrast.**
   The toggle is a `<span>☾`. Beyond #1, spec should require it to announce state
   (`aria-pressed` / `aria-label="Switch to light theme"`). Also: all contrast numbers above are for
   **dark mode**. Light mode inherits Hextra defaults (`[ASSUMPTION]` in DESIGN.md), so the three
   accents (`#8b5cf6` / `#d946ef` / `#f59e0b`) on a *light* surface are unverified — fuchsia and
   amber on white are the usual offenders. **Spec guidance:** state that light-mode accent contrast
   must be re-verified (fuchsia/amber on light surfaces likely need darkening for links and small
   tags).

4. **`prefers-reduced-motion` is specified but not demonstrated; require it in the spec.**
   EXPERIENCE.md correctly says to drop card hover-lift and the theme transition under reduced
   motion, but no mock includes a `@media (prefers-reduced-motion: reduce)` block. **Spec guidance:**
   make the reduced-motion media query a build requirement covering: card `translateY` hover lift,
   theme-transition animation, and any sticky-header/backdrop transitions. Cheap and explicitly
   promised.

5. **Alt-text guidance needs to be operational, not just "needs alt text."**
   EXPERIENCE.md states photos need alt and decorative gradient thumbs are `alt=""` — good, but the
   content model is author-written markdown, so this depends entirely on **Carrie remembering to
   write alt per post.** **Spec guidance:** (a) add an `alt`/`caption` field to the post front-matter
   schema or an authoring checklist so it's prompted at write time; (b) confirm the gradient
   fallback thumb and the avatar-ring photo placeholder render with `alt=""` (decorative) vs. a real
   description for the actual avatar photo (the avatar *is* meaningful on About — give it a real
   alt like "Carrie"). Mocks use text-in-a-div placeholders, so this is untested.

6. **Mock meta/footer text `#70707c` fails AA as small text (4.11:1).**
   Used for card dates/read-time and footer line in all mocks. It's *below* the muted token
   `#a0a0ab` and fails the 4.5:1 normal-text floor. DESIGN.md only defines `#a0a0ab` for muted/meta —
   the mocks invented a darker `#70707c`. **Spec guidance:** do not introduce a sub-muted gray; keep
   meta text at the spec's `#a0a0ab` (7.65:1) or no darker than ~`#8a8a94` to stay ≥4.5:1.

---

## Nice-to-have

1. **Gradient text-clip needs a solid-color fallback.** The wordmark and `<span class="name">` /
   `<span class="g">` use `background-clip:text; color:transparent`. If `background-clip:text` is
   unsupported or a forced-colors / high-contrast mode strips backgrounds, the text becomes
   **invisible** (transparent on dark). Spec a fallback `color` (e.g. `#ededed`) set before the clip,
   and consider `forced-colors` handling. Low risk on modern browsers but trivially cheap insurance.

2. **Heading semantics in mocks are mostly right — keep them.** Each page has a single `<h1>` and
   logical `h2`s (post page: h1 title → h2 sections; about: h1 → h2). Card titles use `<h3>` under
   the "Latest posts" `<h2>`, which is correct order. One nit: the home `<h1>` is "Hey, I'm Carrie 👋"
   while the gradient *wordmark* in the header is also "carrie kroutil" as a `<span>` (not a heading)
   — that's correct (wordmark = link home, not a heading). No change needed; just lock heading order
   as spec.

3. **Emoji in headings/links should be screen-reader-considered.** The 👋 in the h1 and any decorative
   emoji read aloud literally ("waving hand"). Harmless here and on-brand, but if it gets noisy,
   `aria-hidden` decorative emoji. Optional.

4. **Landmark regions.** Mocks use `<header>/<main>/<footer>` — good. Ensure the build keeps a
   `<main>` landmark and consider a "skip to content" link (Hextra may provide one; verify).

5. **Card-as-link accessible name.** The whole post-card is one `<a>` wrapping thumb + tag + title +
   excerpt + meta. That's fine for mouse, but the link's accessible name becomes the concatenation of
   all inner text. Acceptable; if it reads awkwardly, point the accessible name at the title.

---

## Looks good

- **Body and muted text contrast is excellent** (16.9:1 and 7.65:1) — the DESIGN.md "Do keep body
  text at `#ededed` on `#0a0a0a`" instruction is well-founded.
- **The amber gradient risk is a non-issue** — verified 9.22:1; the entire gradient passes AA for
  normal text at its weakest point (violet, 4.68:1).
- **All three topic-chip tints pass comfortably** (9.7–12.5:1 on `#15151a`).
- **Fuchsia link color passes AA** both on page (5.71:1) and on cards (5.00:1).
- **No-JS fallback is sound by design** — static Hugo HTML; only search + theme toggle depend on JS
  and degrade gracefully (EXPERIENCE.md §State Patterns). Core reading works with JS off.
- **Heading structure and landmarks** in the mocks are semantically correct (single h1, ordered
  h2/h3, header/main/footer).
- **Accessibility floor is explicitly stated** in EXPERIENCE.md (contrast, alt, keyboard, motion,
  semantics, AA target) — the intent is right; the Should-fix items are about *making the spec
  binding on the build* rather than leaving them as prose promises.

---

## Spec actions (one-line each)
- [ ] DESIGN.md: add a `focus` token (2px `#d946ef` outline, 2px offset, `:focus-visible`).
- [ ] DESIGN.md: add gradient-text solid fallback color + `forced-colors` note.
- [ ] DESIGN.md: cap meta text at `#a0a0ab`; forbid sub-muted grays like `#70707c`.
- [ ] EXPERIENCE.md: require real semantic controls (button/link + `aria-label`) for icon nav & toggle.
- [ ] EXPERIENCE.md: require `prefers-reduced-motion` media query (hover lift + theme transition).
- [ ] EXPERIENCE.md: add alt-text to the post front-matter/authoring checklist; avatar gets real alt.
- [ ] EXPERIENCE.md: resolve `[NOTE FOR UX]` — amber/gradient verified at 9.22:1 / 4.68:1 min, no change.
- [ ] Build: re-verify light-mode accent contrast (fuchsia/amber on light surfaces).
