---
status: final
created: 2026-06-22
updated: 2026-06-22
sources:
  - ../../briefs/brief-carriekroutil-site-2026-06-22/brief.md
mockups:
  - ./mockups/home.html
  - ./mockups/post-single.html
  - ./mockups/about.html
colors:
  bg: "#0a0a0a"
  surface: "#15151a"
  surface-2: "#1c1c23"
  border: "#2a2a33"
  fg: "#ededed"
  muted: "#a0a0ab"
  accent-violet: "#8b5cf6"
  accent-fuchsia: "#d946ef"
  accent-amber: "#f59e0b"
  accent-gradient: "linear-gradient(90deg, #8b5cf6, #d946ef, #f59e0b)"
  link: "#d946ef"
  focus: "#d946ef"
  meta-min: "#a0a0ab"
  chip-violet-text: "#c9b8ff"
  chip-fuchsia-text: "#f3a8ff"
  chip-amber-text: "#ffd591"
typography:
  font-sans: "ui-sans-serif, system-ui, -apple-system, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif"
  font-mono: "ui-monospace, SFMono-Regular, Menlo, Consolas, monospace"
  scale-h1: "2.5rem"
  scale-h2: "1.15rem"
  scale-body: "1.05rem"
  scale-small: "0.85rem"
  weight-body: 400
  weight-strong: 600
  weight-display: 800
  tracking-display: "-0.03em"
rounded:
  card: "18px"
  control: "10px"
  pill: "999px"
spacing:
  base: "4px"
  gutter: "22px"
  content-max: "920px"
  reading-max: "720px"
focus:
  ring: "2px solid #d946ef"
  style: ":focus-visible"
components:
  - brand-wordmark
  - avatar-ring
  - topic-chip
  - post-card
  - header-nav
  - theme-toggle
  - search
  - footer-accent
---

# carriekroutil.com — Visual Identity

## Brand & Style

Playful and cozy. A personal site that feels like *a person*, not a portfolio template — warm,
upbeat, a little colorful, easy to linger in. The whole human comes first; the professional
credibility rides along underneath.

**Foundation / inherited system:** this site is built on the **Hugo `Hextra` theme**. DESIGN.md does
not reinvent Hextra's base — it *extends* it. Where a token below differs from Hextra's default
(notably the accent palette and dark-default), the token here wins. Anything unspecified inherits
Hextra's defaults.

The signature element is the **gradient wordmark**: the full name `carrie kroutil`, set in a
left-to-right violet→fuchsia→amber gradient, top-left in the header. The gradient is borrowed from
Carrie's own `unblur.carrie.dev` logo — it is *her* mark, reused deliberately. The gradient is a
garnish, not a wallpaper: it lives on the wordmark, the avatar ring, small accents, and a thin
footer rule — never as a large background fill.

## Colors

Dark-by-default, with a light toggle (Hextra-provided). Tokens in frontmatter; roles below.

- **Background** `#0a0a0a` (near-black) · **Surfaces** `#15151a` / `#1c1c23` for cards & wells ·
  **Borders** `#2a2a33`.
- **Text** `#ededed` body · `#a0a0ab` muted/meta.
- **Accent gradient** violet `#8b5cf6` → fuchsia `#d946ef` → amber `#f59e0b`. Used as a gradient for
  the wordmark/avatar-ring/footer rule, and as discrete hues for topic chips and tags.
- **Links / primary accent** fuchsia `#d946ef` (the "pink" reads best as an interactive color on
  dark).
- **Topic-chip tints** — each chip borrows one accent: violet text `#c9b8ff`, fuchsia text
  `#f3a8ff`, amber text `#ffd591`, on a `#15151a` surface with a dimmed accent border.
- `[ASSUMPTION]` **Light mode** inherits Hextra's light palette with the same three accents; exact
  light-mode surface values left to Hextra defaults unless Carrie wants them tuned.

## Typography

System sans stack (fast, native, no web-font load — supports the "loads fast" success criterion).

- **Display / headings** weight 800, tight tracking (`-0.03em`), e.g. hero `2.5rem`.
- **Section headings** `~1.15rem`, weight 600.
- **Body** `~1.05rem`, weight 400, line-height ~1.6 for cozy readability.
- **Meta / small** `~0.85rem`, muted.
- **Mono** for code blocks (inherits Hextra's code styling + syntax highlighting).

## Layout & Spacing

- Centered single column. **Home/index pages** use `content-max` `920px`; **reading pages** (single
  post, about) narrow to `reading-max` `720px` for comfortable line length. `22px` side gutters —
  intimate, blog-like, not a wide dashboard.
- 4px base spacing rhythm.
- Home = hero (avatar + greeting + topic chips) then a **2-up post-card grid** (collapses to 1-up
  under ~620px).

## Elevation & Depth

Mostly flat and calm. Depth comes from **surface contrast** (cards a step lighter than the page) and
a **subtle hover lift** on cards (translateY ~3px + border brighten), not heavy shadows. Sticky
header uses a translucent blurred backdrop.

## Shapes

Soft and friendly: cards `18px` radius, controls/icons `10px`, chips & avatar fully round (`999px` /
circle). Roundness is part of the "cozy."

## Components

Visual reference: [home](./mockups/home.html), [single post](./mockups/post-single.html),
[about](./mockups/about.html). Spines win on conflict with any mock.

- **brand-wordmark** — `carrie kroutil`, weight 800, accent-gradient text-clip. Links home. The
  signature.
- **avatar-ring** — circular photo of Carrie with a gradient ring (gradient padding around a
  surface-2 circle).
- **topic-chip** — pill, one accent tint per chip, used in hero and on cards. Clickable → tag page.
- **post-card** — surface card: image/gradient thumb · uppercase accent **tag** · title (weight
  600) · one-line excerpt · muted date + read-time. Hover lift.
- **header-nav** — sticky, translucent: wordmark left; right = Posts, About, search (⌕), theme
  toggle (☾), GitHub, LinkedIn.
- **theme-toggle** — dark default; persists choice (Hextra). A real focusable control with an
  accessible label (Hextra's built-in), not a styled `span`.
- **search** — header trigger (⌕) opening Hextra's built-in full-text search; a real focusable
  button with an `aria-label`.
- **footer-accent** — thin gradient rule above a muted one-line footer. Purely decorative.

**Focus & controls:** every interactive element (nav links, search, theme toggle, social icons,
cards, chips) is a real `<a>`/`<button>` with an accessible label and a visible **focus ring**
(`{focus.ring}`, shown on `:focus-visible`). Do not ship interactive `span`s.

## Do's and Don'ts

- **Do** reserve the gradient for the wordmark, avatar ring, small tags, and the footer rule.
- **Don't** fill large areas with the gradient or stack it behind body text — it should sparkle, not
  shout.
- **Do** keep body text at `#ededed` on `#0a0a0a` for strong contrast.
- **Don't** let "playful" cost legibility: chip and tag tints are accents, never body copy.
- **Do** lean on photos (Carrie, dogs, golf, trips) for warmth; thumbs fall back to a subtle
  gradient when a post has no image.
- **Don't** add heavy drop-shadows or busy textures — depth is surface + a gentle lift.
- **Do** give the gradient text-clip wordmark a **solid-color fallback** (fuchsia `#d946ef`) for
  browsers/forced-colors/print where `background-clip:text` doesn't render.
- **Don't** let meta/caption text dip below `{colors.meta-min}` (`#a0a0ab`) on dark — the mock's
  lighter gray fails AA; `#a0a0ab` and above passes.
- **Do** treat light mode as inheriting Hextra defaults; **verify** the three accents still meet AA
  on Hextra's light surfaces before shipping (not yet checked).

> **Contrast verified (AA):** body 16.9:1, muted 7.65:1, fuchsia link 5.71:1, chips 9.7–12.5:1,
> gradient amber end 9.22:1 (weakest violet end 4.68:1 — AA for normal text). See
> `review-accessibility.md`.
