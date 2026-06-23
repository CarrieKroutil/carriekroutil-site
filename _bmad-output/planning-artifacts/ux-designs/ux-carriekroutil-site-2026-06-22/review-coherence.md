# Coherence Review — carriekroutil.com UX deliverable

**Reviewer role:** Coherence (DESIGN.md ↔ EXPERIENCE.md ↔ mocks ↔ .memlog.md)
**Date:** 2026-06-22
**Stakes:** Low (personal site, Hugo + Hextra). Findings calibrated accordingly — most items are
documentation hygiene, not design defects.

Scope: do the two spines agree with each other and with the three mocks; do they contradict the
decision record. Verdict: **broadly coherent.** No blockers. A few documentation gaps worth closing
so the spines stay the single source of truth they claim to be.

---

## Blocker
None.

---

## Should-fix

### S1 — Reading-width (720px) is used by two mocks and the memlog but absent from DESIGN.md tokens
DESIGN.md `frontmatter.spacing.content-max: 920px` and §Layout & Spacing state one width: "max width
`920px`". But:
- `post-single.html` and `about.html` set `.wrap{max-width:720px}` for the content column (header nav
  is kept at 920px in both — see post-single L21, about L20).
- `.memlog.md` line 11 explicitly records the decision: *"post-single.html (720px reading width …)"*.

So 720px is an **intentional, recorded decision** that the spine never captured. As written, EXPERIENCE.md's
closing rule "Spines win on conflict with any mock" would make the spine (920px) override the
intended 720px reading column — the opposite of what was decided. **Fix:** add a reading-width token
to DESIGN.md frontmatter (e.g. `spacing.reading-max: "720px"`) and a line in §Layout & Spacing noting
home/list = 920px, single-post/about prose = 720px with the nav bar staying at 920px. This is the
single most load-bearing coherence gap. (Refs: DESIGN frontmatter L40 + §Layout; EXPERIENCE L135;
memlog L11; post-single L18/L21; about L18/L20.)

### S2 — `search` is an orphan: behavior in EXPERIENCE, no component in DESIGN
EXPERIENCE.md §Component Patterns lists **search** as a behavioral component ("opens Hextra search;
type-to-filter; keyboard-navigable"), and IA §6 makes it a surface. DESIGN.md `components:` does NOT
list `search` — the ⌕ glyph only appears inside the `header-nav` description. Per check #1 (no
orphans, both directions) this is a one-directional orphan. Low risk because search is Hextra-provided,
but **recommend** either adding `search` (or `search-trigger`) to DESIGN.Components, or adding a note
under `header-nav` that the ⌕ trigger's behavior is specified in EXPERIENCE. (Refs: DESIGN frontmatter
L41-48, §Components; EXPERIENCE L71.)

### S3 — `footer-accent` has visual spec but no behavioral entry (reverse orphan)
DESIGN.Components lists **footer-accent**; EXPERIENCE §Component Patterns has no entry for it. It is
genuinely decorative (like `avatar-ring`, which EXPERIENCE *does* explicitly mark "decorative; not
interactive"). **Fix:** add a one-line "footer-accent — decorative rule, non-interactive" to
EXPERIENCE for parity, mirroring the avatar-ring treatment, so the no-orphan rule holds cleanly.
(Refs: DESIGN §Components L127; EXPERIENCE §Component Patterns L62-72.)

---

## Nice-to-have

### N1 — Header social icons inconsistent across mocks vs. spec
Both spines say the header carries **GitHub + LinkedIn** (DESIGN §header-nav L125; EXPERIENCE L40).
`post-single.html` and `about.html` show both (GH + in). But `home-direction-a.html` header shows only
GitHub (↗) — LinkedIn is missing (L85). Mock-level only; the approved-home mock (memlog L10) predates
the LinkedIn confirmation, but worth aligning so all three mocks match the spine. (Refs: home L83-85;
post-single L52; about L48.)

### N2 — Wordmark tracking differs between spec and mocks
DESIGN §Typography / §Brand imply the display tracking token `tracking-display: -0.03em`, but all three
mocks set the brand wordmark to `letter-spacing:-.02em` (home L26, post L22, about L21). The hero `h1`
*does* use -0.03em correctly (home L41). Minor; either treat -0.02em as an intentional wordmark-specific
value or align. Not contradictory enough to block.

### N3 — Avatar-ring "links live in nav/about" vs. about-page reality
EXPERIENCE says avatar-ring is decorative and "links live in nav/about" (L72). On about.html the avatar
is indeed non-interactive and social links are buttons below — consistent. Just confirming: coherent,
no action.

### N4 — Hero one-liner is duplicated verbatim across DESIGN-adjacent copy and mocks
The one-liner in EXPERIENCE §Voice (L55-56) matches home-direction-a hero exactly (L94) and about.html
uses a trimmed version (L55-56). Consistent; flagged only so future copy edits update all three places.

---

## Looks good

- **Token integrity (check #2): PASS.** Every `{token}` referenced in EXPERIENCE.md resolves in
  DESIGN.md frontmatter: `{colors.accent-gradient}` (L17), `{colors.link}` (L18), `{rounded.card}`
  (L34). No dangling references.
- **Color/surface/border tokens (check #3): PASS.** All three mocks hardcode the exact DESIGN palette
  (`--bg:#0a0a0a`, `--surface:#15151a`, `--surface-2:#1c1c23`, `--fg:#ededed`, `--muted:#a0a0ab`,
  `--border:#2a2a33`, violet/fuchsia/amber). Chip tint hexes (#c9b8ff / #f3a8ff / #ffd591) match
  frontmatter `chip-*-text` exactly.
- **Radii: PASS.** `--radius:18px` (card) across mocks = `rounded.card`. Controls/icons use 10px
  (= `rounded.control`), chips/avatar 999px/50% (= `rounded.pill`). Note: post-single uses 12px on
  `pre` and 14px on inline-img — between control and card; acceptable, not token-bound.
- **Voice / no-self-deprecation golf rule (check #4): PASS — strong.** memlog L9-10 sets the rule:
  golf = "fun/eager for next round, NOT improvement-chasing or self-deprecating." Hero one-liner
  "always up for the next round of golf" honors it. The post title "Breaking 100, finally (sort of)"
  (home L113) is light and self-aware but reads as *celebration*, not self-criticism — within the
  rule. No "I'm terrible at golf" / apologetic tone anywhere. About-page voice ("It's all me", "love
  turning fuzzy ideas into things that ship") is warm and confident.
- **Hextra-inheritance claims (check #6): CONSISTENT.** DESIGN §Brand ("extends Hextra, token here
  wins, unspecified inherits") and EXPERIENCE §Foundation ("records only the delta from stock Hextra;
  spines win on conflict") tell the same story. Both attribute theme-toggle persistence, search, light
  mode, and code styling to Hextra. memlog L11 ("light mode = Hextra defaults untuned") matches
  DESIGN's `[ASSUMPTION]` light-mode line. No contradiction.
- **Decision record (check #5): no spine drift except S1.** Every memlog decision (gradient wordmark
  top-left/hero name plain, dark-default + light toggle, topic chips across 3 accents, GH+LinkedIn
  only/Instagram deferred, on-brand 404 "wandered off the fairway", WCAG AA + verify amber
  gradient-text) is reflected in one or both spines. Hero name *is* plain in mocks (home L93 — name
  not gradient-clipped; gradient lives on the wordmark), matching the memlog L9 decision. The only
  recorded decision the spines failed to capture is the 720px reading width (see S1).
- **IA closure & flows: coherent.** EXPERIENCE's six surfaces + 404 each map to a brief need and a
  flow; the three mocks (home, post, about) instantiate three of them faithfully.
