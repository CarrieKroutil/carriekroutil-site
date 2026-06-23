---
status: final
created: 2026-06-22
updated: 2026-06-22
sources:
  - ../../briefs/brief-carriekroutil-site-2026-06-22/brief.md
  - ./DESIGN.md
mockups:
  - ./mockups/home.html
  - ./mockups/post-single.html
  - ./mockups/about.html
---

# carriekroutil.com — Experience

## Foundation

- **Form factor:** responsive website (desktop + mobile), single intimate column.
- **Build system:** static site, **Hugo + `Hextra` theme**, hosted on AWS Amplify. No client app,
  no accounts, no server. Behavior that isn't specified here inherits Hextra's defaults; this doc
  records only the *delta* from stock Hextra.
- **Visual identity:** see `DESIGN.md`. Tokens are referenced by name as `{colors.accent-gradient}`,
  `{rounded.card}`, etc. On any conflict, the spines win over a mock or theme default.
- **Content model:** every post is a markdown file with front matter (title, date, tags, optional
  hero image, **image alt text**). Publishing = write markdown → push → live. The experience must
  never require a CMS or admin UI.
- **Carried-forward open decision (from brief):** pick a lightweight, privacy-friendly **analytics**
  tool (GoatCounter or Cloudflare Web Analytics — *not* Mixpanel). Not a launch gate, but a
  conscious "do it soon" item; whatever's chosen drops in as a single script include. Recorded here
  so it isn't lost between brief and build.

## Information Architecture

Surfaces (every one ties to a stated need; nothing invented beyond these):

1. **Home `/`** — hero (avatar + greeting + one-liner + topic chips) → *Latest posts* card grid →
   footer. The "whole human, come hang out" front door.
2. **Posts `/posts/`** — full reverse-chronological list of every post; filterable by tag.
3. **Post `/posts/{slug}/`** — a single post: title, date, read-time, tags, body (prose + images +
   code), prev/next or back-to-posts.
4. **About `/about/`** — who Carrie is, the human-first hello with a photo; light thread to the
   professional self; GitHub + LinkedIn.
5. **Tag page `/tags/{topic}/`** — every post under one topic (golf, dogs, code, AI, leadership,
   trips). Reached by clicking any chip or tag.
6. **Search** — Hextra's built-in full-text search over posts (header ⌕).
7. `[ASSUMPTION]` **404** — friendly, on-brand "this page wandered off" with a way home.

**Global nav (sticky header):** wordmark (→ home) · Posts · About · search · theme toggle · GitHub
· LinkedIn. **Footer:** GitHub + LinkedIn, gradient rule, one muted line.

Visual reference for key surfaces: [home](./mockups/home.html),
[single post](./mockups/post-single.html), [about](./mockups/about.html). Posts-list and tag pages
derive from the post-card grid + this IA (spine-only, no separate mock).

**Closure check:** every brief need has a surface (about→About, mixed posts→Posts/Post, topic
browsing→Tag pages, findability→Search, credibility→About + GitHub/LinkedIn + the BMAD post), and
every surface has a journey landing on it (see Key Flows). IA closes.

## Voice and Tone

Playful, cozy, warm, first-person. Upbeat and **never self-deprecating** — golf is about *fun and
the next round*, not being bad at it. Talks like Carrie texting a friend who happens to also respect
her work.

- Greeting: "Hey, I'm Carrie 👋"
- One-liner: "Engineering leader who's always up for the next round of golf, loves her dogs
  unconditionally, and builds things with AI for fun. This is my corner of the internet — come hang
  out."
- Section labels stay casual: "Latest posts", "all posts →".
- Empty/utility microcopy is light, not corporate (e.g. 404: "This page wandered off the fairway.").
- Emoji: sparing and friendly (a 👋 here, not confetti everywhere).

## Component Patterns (behavioral)

Visual specs live in `DESIGN.md.Components`; behavior here.

- **post-card** — entire card is one click target → the post. Hover lift is affordance only.
- **topic-chip / tag** — click → that tag page. Same control in hero, on cards, and on posts.
- **brand-wordmark** — click → home.
- **header-nav** — sticky on scroll; on mobile collapses to Hextra's hamburger/condensed nav.
- **theme-toggle** — switches dark/light, **persists** the choice across visits (Hextra).
- **search** — opens Hextra search; type-to-filter across post titles/content; keyboard-navigable
  results.
- **avatar-ring** — decorative; not interactive (links live in nav/about).
- **footer-accent** — decorative gradient rule; not interactive (the footer's GitHub/LinkedIn links
  are the only operable elements there).

## State Patterns

- **Default:** dark theme.
- **Empty states:** `[ASSUMPTION]` if a tag has no posts yet, show a light "nothing here yet —
  check back" rather than a blank page. Home before many posts exist still feels intentional (hero
  carries it).
- **Hover/focus:** cards lift; all interactive elements have a visible focus ring (keyboard).
- **Image states:** posts may have a hero image; cards without an image fall back to the subtle
  gradient thumb. All images lazy-load.
- **No-JS:** core reading works without JS (static HTML); search and theme-toggle are the only
  JS-dependent niceties and degrade gracefully.
- **404:** friendly on-brand page with a path home.

## Interaction Primitives

- Card hover lift (`translateY ~3px` + border brighten); disabled under reduced-motion.
- Smooth, quick theme transition.
- Sticky translucent header on scroll.
- Standard link underline/color on body links (fuchsia `{colors.link}`).

## Accessibility Floor

- **Contrast (verified AA — `review-accessibility.md`):** body `#ededed` on `#0a0a0a` 16.9:1,
  muted `#a0a0ab` 7.65:1, fuchsia link 5.71:1, chips 9.7–12.5:1. Gradient *text* is reserved for
  large/bold display (the wordmark): amber end 9.22:1, weakest violet end 4.68:1 — both AA for
  normal-size text. **Resolved:** the amber-stop concern checked out, **no color change needed.**
  Never put the gradient behind small body copy.
- **Meta/caption text** must not go below `{colors.meta-min}` `#a0a0ab` on dark (the mock's lighter
  `#70707c` fails AA — fix in build).
- **Real controls, not spans (build requirement):** nav links, search, theme toggle, and social
  icons must be real `<a>`/`<button>` elements with accessible labels (`aria-label` on
  icon-only ones) — use Hextra's built-in controls. Each shows a visible focus ring
  (`{focus.ring}`, on `:focus-visible`). The mock's `<span>` icons are visualization only.
- **Images:** every photo (Carrie, dogs, golf, trips) needs alt text — enforced via an `alt` field
  in post front matter; decorative gradient thumbs are `alt=""`.
- **Keyboard:** all nav, cards, chips, search, and toggle reachable and operable by keyboard with
  visible focus.
- **Motion (build requirement):** honor `prefers-reduced-motion` — drop hover lifts and the
  theme-transition animation. Wire the media query, don't just intend it.
- **Gradient fallback:** the text-clip wordmark needs a solid-color fallback (`#d946ef`) for
  forced-colors / print / unsupported browsers.
- **Light mode:** inherits Hextra defaults; re-verify the three accents meet AA on light surfaces
  before shipping (not yet checked).
- **Semantics:** one `h1` per page, logical heading order, landmark regions (header/main/footer).
- Target: **WCAG AA** for a personal site (not regulated, but no reason to be sloppy).

## Key Flows

### Flow 1 — Dana, the recruiter, doing her homework (climax: "she actually ships")
1. Dana has Carrie's resume open in another tab; clicks through to carriekroutil.com on her laptop.
2. Lands on Home — sees the gradient `carrie kroutil` wordmark, a warm photo + one-liner. First read:
   "this is a real, interesting person."
3. The featured post catches her: *"I built this site greenfield with the BMAD method."* She clicks.
4. Reads it — concrete, well-written, shows process and judgment. **Climax:** "okay, she actually
   builds and ships things, and explains them clearly." Credibility earned without a resume vibe.
5. Taps **GitHub** and **LinkedIn** from the header to confirm. Leaves with a clear, positive read.

### Flow 2 — Sam, a friend, on the couch (climax: "this feels like hanging out with her")
1. Carrie texts Sam a link to a new dog post. Sam opens it on their phone.
2. Reads the post — photos of the dogs, Carrie's voice, a laugh. Cozy on a small screen (single
   column, big tap targets).
3. Curious, Sam taps the **dogs** chip → the dogs tag page, sees more.
4. Wanders into a **trips** post from there. **Climax:** "this is just *her* — fun to scroll, feels
   like hanging out." Bookmarks the site.

### Flow 3 — Carrie, publishing on a Sunday (climax: "wrote it, pushed it, it's live")
1. Sunday morning, Carrie has a golf story to tell. Opens the repo, `hugo new content`, writes
   markdown, drops in two photos.
2. Sets tags `golf`, maybe `trips`; saves.
3. `git push`. **Climax:** Amplify builds and the post is live in a couple minutes — no CMS, no
   manual copy, no friction. The habit survives because publishing never got in the way.

---
*Spines win on conflict with any mock, wireframe, or theme default. Visual tokens: see `DESIGN.md`.*
