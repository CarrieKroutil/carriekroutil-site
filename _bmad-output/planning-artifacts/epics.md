---
stepsCompleted: ["step-01-validate-prerequisites", "step-02-design-epics", "step-03-create-stories", "step-04-final-validation"]
inputDocuments:
  - ../specs/spec-carriekroutil-site/SPEC.md
  - ./prds/prd-carriekroutil-site-2026-06-22/prd.md
  - ./architecture/architecture-carriekroutil-site-2026-06-22/ARCHITECTURE-SPINE.md
  - ./ux-designs/ux-carriekroutil-site-2026-06-22/DESIGN.md
  - ./ux-designs/ux-carriekroutil-site-2026-06-22/EXPERIENCE.md
---

# carriekroutil-site - Epic Breakdown

## Overview

This document provides the complete epic and story breakdown for carriekroutil-site, decomposing the requirements from the PRD, the UX design contract (DESIGN.md + EXPERIENCE.md), and the Architecture spine into implementable stories. Traceability spine: each FR is mirrored 1:1 by a SPEC capability (**CAP-N ≡ FR-N**) and governed by one or more architecture decisions (**AD-n**). Stories cite **CAP-N / FR-N / AD-n** so the AD IDs stay stable and every story is traceable back to the contract.

## Requirements Inventory

### Functional Requirements

*From PRD §4 (FR-N) — each mirrored by SPEC CAP-N and governed by the cited AD-n.*

- **FR-1** *(CAP-1 · AD-6, AD-13)*: Home hero introduces Carrie — `/` shows the wordmark, a warm avatar, the greeting, the one-liner, and clickable Topic Chips; usable without JS.
- **FR-2** *(CAP-2 · AD-6)*: Home previews the latest posts — a grid of the 4–6 most-recent Posts as full-card link targets (title, topic tag, excerpt, date + read-time), newest first, with an "all posts →" affordance to `/posts/`.
- **FR-3** *(CAP-3 · AD-13)*: About page presents Carrie — `/about/` shows photo, bio, Topic Chips, and links to GitHub and LinkedIn.
- **FR-4** *(CAP-4 · AD-4, AD-8)*: Browse all posts — `/posts/` lists every non-draft Post reverse-chronologically; narrowable to a single Topic via tag term-pages (no client-side JS filtering).
- **FR-5** *(CAP-5 · AD-4, AD-9)*: Read a single post — `/posts/{slug}/` renders title, date, read-time, clickable Tags, and the markdown body (prose, syntax-highlighted code, alt-texted images); drafts excluded; a way back to the stream present.
- **FR-6** *(CAP-6 · AD-3)*: Read-time and metadata — each Post shows an auto-derived read-time (`.ReadingTime`, "{n} min read", floor 1) and a front-matter date; missing/invalid date fails the build.
- **FR-7** *(CAP-7 · AD-3, AD-8)*: Tag a post by topic — Carrie assigns Topics via the lowercase `tags` front-matter field; open taxonomy, no code change to add a new tag value.
- **FR-8** *(CAP-8 · AD-8)*: Browse a topic — clicking a Topic Chip opens `/tags/{topic}/` listing all Posts under it, newest first; empty term page shows a light "check back" state.
- **FR-9** *(CAP-9 · AD-13)*: Search posts — a header control opens Hextra FlexSearch, filtering titles/content as you type, keyboard-navigable; the one accepted JS-dependent feature, degrades gracefully.
- **FR-10** *(CAP-10 · AD-1, AD-2, AD-11)*: Publish by push — adding a markdown file and pushing the default branch triggers an Amplify build that deploys; new non-draft Post live at its page and in the stream within a few minutes; no CMS/login/copy step.
- **FR-11** *(CAP-11 · AD-2)*: Single settled pipeline on the correct brand — exactly one Amplify pipeline builds `carriekroutil-site`, served at `carriekroutil.com` via Route 53; `baseURL`/`title` reflect it; zero SheBytes/`code.shebytes.io`/ananke/quickstart remnants; no second deploy path in-repo.
- **FR-12** *(CAP-12 · UX spine + conventions)*: Brand identity consistent and on-palette — gradient Wordmark links Home with a solid `#d946ef` fallback; accents stay a garnish (no large gradient fill behind body text).
- **FR-13** *(CAP-13 · conventions)*: Dark/light theme toggle — dark default; a real focusable, labeled toggle switches to light; choice persists across visits (Hextra).
- **FR-14** *(CAP-14 · Deferred)*: Lightweight, privacy-friendly readership signal — once chosen, basic page views via a single privacy-friendly include (GoatCounter or Cloudflare Web Analytics, not Mixpanel); no cookie banner, no PII. *Tool choice deferred, not a launch gate.*
- **FR-15** *(CAP-15 · AD-7, AD-6)*: Featured post on Home — one `featured: true` Post holds a single Home slot independent of recency; falls back cleanly to most-recent when none flagged.
- **FR-16** *(CAP-16 · AD-2)*: Preview locally before publishing — `hugo server` renders the site with the new/edited Post and live reload, showing draft + non-draft.
- **FR-17** *(CAP-17 · AD-12)*: Build outcomes are surfaced — a failed Amplify build emails Carrie and keeps the prior good site live; a successful build with no new Post is distinguishable from a failure.
- **FR-18** *(CAP-18 · conventions)*: Basic discoverability — sitemap + indexing `robots.txt`; each Post Page emits a descriptive `<title>`, meta description, and OG/Twitter tags (reading `hero.src`/`hero.alt`); stable human-readable `/posts/{slug}/` URLs.

### NonFunctional Requirements

*From PRD §9 — cross-cutting, carried as SPEC Constraints.*

- **NFR-1 (Performance)**: Static site, fast on mobile; no web-font network load (system font stacks); images lazy-load; core reading works without JavaScript. *(Guideline, not a hard SLA.)*
- **NFR-2 (Accessibility — WCAG AA)**: Dark (default) theme meets AA (contrasts verified); structural a11y in both themes — one `<h1>`/page, logical heading order, landmark regions, visible `2px #d946ef` focus ring on `:focus-visible`, real `<a>`/`<button>` with labels, alt on every content image (build-enforced), `prefers-reduced-motion` honored. Light mode ships untuned, not yet AA-verified.
- **NFR-3 (Privacy)**: No consent-requiring cookies; no PII; any analytics is privacy-friendly, script-include only.
- **NFR-4 (Maintainability / longevity)**: Low-maintenance, durable stack — Hugo + actively-maintained Hextra via Hugo Modules; one boring, reliable deploy pipeline; optimize for "still updatable in three years". FixIt is the vetted fallback theme.
- **NFR-5 (Ownership / no lock-in)**: Carrie owns domain, content (plain markdown in her repo), and pipeline; portable off Amplify.

### Additional Requirements

*From the Architecture spine (AD-n) — technical decisions that shape implementation. Greenfield: the build lands fresh in `carriekroutil-site` (BMad tooling only today); nothing carried over from `~/code/quickstart`.*

- **AR-1 — Greenfield scaffold (no starter template; fresh Hugo + Hextra)** *(AD-5, AD-10; Structural Seed)*: Initialize a fresh Hugo (extended) + Hextra site via Hugo Modules. `hugo.toml` (baseURL, title, module import, `params.theme.default=dark`, search, taxonomy), `go.mod`/`go.sum` locking Hextra `v0.12.3` (`github.com/imfing/hextra`), and the Structural Seed directory layout. **→ Epic 1, Story 1.**
- **AR-2 — Pinned versions, deliberate upgrades** *(AD-10)*: Hugo extended pinned via `HUGO_VERSION` in the Amplify build env; Hextra locked in `go.mod`; upgrades only on purpose (`hugo mod get -u` + re-test).
- **AR-3 — `amplify.yml` is the single build truth** *(AD-11)*: repo-root `amplify.yml`; `preBuild` runs `hugo mod get`; `build` runs `hugo --gc --minify` at the pinned `HUGO_VERSION`; artifacts = `public/`.
- **AR-4 — Exactly one pipeline, production only** *(AD-2)*: AWS Amplify builds from source on the **default branch only**, served at `carriekroutil.com` via Route 53; branch/PR auto-previews off; one environment; no S3-sync GitHub Action, no GCP artifacts in-repo.
- **AR-5 — Build-failure notification** *(AD-12)*: Amplify native email-on-failure enabled; a failed build keeps the prior good site live; a successful build with no new published Post is distinguishable from a failure.
- **AR-6 — Canonical post front-matter contract** *(AD-3)*: YAML front matter — `title`; `date` (ISO, invalid/missing **fails the build**); `tags` (lowercase, open); `draft`; optional `featured`; optional `hero {src, alt}` with `alt` required (single source for OG/Twitter). Read-time derived from `.ReadingTime` ("{n} min read", floor 1).
- **AR-7 — Post stream as `type: blog` at `/posts/`** *(AD-4)*: Posts in `content/posts/`; `_index.md` sets `type: blog` and is the only section-level config home; slug = bundle folder name; `/posts/{slug}/` URLs preserved.
- **AR-8 — Project overrides only; module read-only** *(AD-5)*: custom partials, layout overrides, and CSS live in the project's `layouts/` + `assets/`, shadowing the Hextra module by lookup order; the module is never edited in place.
- **AR-9 — One post-card partial across Home + list + term** *(AD-6)*: a single `layouts/_partials/custom/post-card.html` (ported from the UX `home.html` mock) renders cards on Home, the `/posts/` list (overridden `list.html`), and tag term pages; native `hextra-card` not used for posts.
- **AR-10 — Featured = front-matter flag, single slot** *(AD-7)*: `featured: true` candidates sorted by `date` desc, first wins (deterministic tiebreak); fallback to most-recent when none flagged; no second curation surface.
- **AR-11 — Tag filtering via custom term page, no client JS** *(AD-8)*: chips link `/tags/{topic}/`; Hugo core generates the term page; a custom `layouts/_default/term.html` (reusing the AD-6 card partial) styles it; `/posts/` stays the full stream.
- **AR-12 — Images via page bundles; alt enforced; processing deferred** *(AD-9)*: each Post is a Hugo page bundle (images beside the markdown); hero via the `hero` map; inline via standard markdown; `layouts/_default/_markup/render-image.html` **fails the build** on empty alt; lazy-load native; resize/responsive/WebP deferred.
- **AR-13 — No-JS core; FlexSearch the sole exception** *(AD-13)*: Home, post, stream, about, tag pages render as static HTML with JS off; Hextra FlexSearch the only JS-dependent feature (degrades gracefully); system-font stacks only (no web-font network load).

### UX Design Requirements

*From the UX design contract — DESIGN.md (visual identity, tokens) + EXPERIENCE.md (IA, behavior, states, accessibility, journeys). Spines win over any mock.*

- **UX-DR1 — Design tokens**: Implement the token system from `DESIGN.md` frontmatter overriding Hextra where specified — colors (bg `#0a0a0a`, surfaces `#15151a`/`#1c1c23`, border `#2a2a33`, fg `#ededed`, muted/meta-min `#a0a0ab`, link/focus `#d946ef`, accent gradient violet→fuchsia→amber, chip tints), typography scale (display 800/`-0.03em`/`2.5rem`, body `1.05rem`/1.6, mono for code), radii (card `18px`, control `10px`, pill `999px`), spacing (4px base, `22px` gutter), focus ring `2px solid #d946ef`. Dark is default.
- **UX-DR2 — brand-wordmark**: `carrie kroutil`, weight 800, accent-gradient text-clip, links Home, with a **solid `#d946ef` fallback** for forced-colors/print/unsupported `background-clip:text`.
- **UX-DR3 — avatar-ring**: circular photo of Carrie with a gradient ring (gradient padding around a surface-2 circle); decorative, non-interactive.
- **UX-DR4 — topic-chip**: pill with one accent tint per chip (violet `#c9b8ff` / fuchsia `#f3a8ff` / amber `#ffd591` text on `#15151a` with dimmed accent border); clickable → tag page; used in hero, on cards, and on posts.
- **UX-DR5 — post-card**: surface card — image/gradient thumb · uppercase accent tag · title (weight 600) · one-line excerpt · muted date + read-time; **entire card is one click target** → the post; hover lift (translateY ~3px + border brighten); no-image cards fall back to the gradient thumb.
- **UX-DR6 — header-nav**: sticky translucent blurred header; wordmark left; right = Posts, About, search (⌕), theme toggle (☾), GitHub, LinkedIn; collapses to Hextra's condensed/hamburger nav on mobile.
- **UX-DR7 — theme-toggle**: dark default, switches dark/light, **persists** across visits; a real focusable control with an accessible label (Hextra built-in), not a styled `span`.
- **UX-DR8 — search**: header ⌕ trigger opening Hextra FlexSearch; a real focusable button with `aria-label`; type-to-filter across post titles/content; keyboard-navigable results.
- **UX-DR9 — footer-accent**: thin gradient rule above a muted one-line footer; only the footer's GitHub/LinkedIn links are operable; rule is decorative.
- **UX-DR10 — Layout & responsive**: centered single column; `content-max 920px` on home/index, `reading-max 720px` on single post + about; `22px` gutters; Home is hero → **2-up post-card grid that collapses to 1-up under ~620px**.
- **UX-DR11 — Accessibility floor (build requirements)**: meta/caption never below `#a0a0ab` on dark (fix the mock's `#70707c`); one `<h1>`/page, logical heading order, landmark regions (header/main/footer); visible `2px #d946ef` focus ring on `:focus-visible`; all nav/search/toggle/social are real `<a>`/`<button>` with accessible labels; **honor `prefers-reduced-motion`** (drop hover lift + theme transition — wire the media query); every content image has alt (build-enforced), decorative gradient thumbs `alt=""`.
- **UX-DR12 — State patterns**: empty tag page shows "nothing here yet — check back"; Home reads as intentional before many posts exist; images lazy-load with gradient-thumb fallback; core reading works no-JS; on-brand 404 ("This page wandered off the fairway.") *(404 deferred per PRD §6.2 — desired, non-blocking)*.
- **UX-DR13 — Voice & microcopy**: greeting "Hey, I'm Carrie 👋"; the hero one-liner; casual section labels ("Latest posts", "all posts →"); light, non-corporate empty/utility microcopy; sparing friendly emoji. Playful, cozy, warm, first-person, never self-deprecating.

### FR Coverage Map

*All 18 FRs mapped; no gaps. NFR-1–5 are cross-cutting, addressed within the relevant stories.*

- **FR-1** → Epic 3 — Home hero introduces Carrie
- **FR-2** → Epic 3 — Home previews the latest posts
- **FR-3** → Epic 3 — About page presents Carrie
- **FR-4** → Epic 2 — Browse all posts (`/posts/`)
- **FR-5** → Epic 2 — Read a single post
- **FR-6** → Epic 2 — Read-time and metadata
- **FR-7** → Epic 2 — Tag a post by topic (authoring-side)
- **FR-8** → Epic 4 — Browse a topic (`/tags/{topic}/`)
- **FR-9** → Epic 4 — Search posts
- **FR-10** → Epic 1 — Publish by push
- **FR-11** → Epic 1 — Single settled pipeline on the correct brand
- **FR-12** → Epic 1 — Brand identity consistent and on-palette
- **FR-13** → Epic 1 — Dark/light theme toggle
- **FR-14** → Epic 5 — Lightweight readership signal *(deferred, not a launch gate)*
- **FR-15** → Epic 3 — Featured post on Home
- **FR-16** → Epic 1 — Preview locally before publishing
- **FR-17** → Epic 1 — Build outcomes are surfaced
- **FR-18** → Epic 5 — Basic discoverability

## Epic List

### Epic 1: Publish-by-push foundation — a live, branded site
Carrie can write a markdown file, `git push`, and see a correctly-branded site live at carriekroutil.com — with local preview and build-failure alerts. Establishes the greenfield Hugo + Hextra scaffold, the single Amplify pipeline (production-only, default-branch), and the visual shell (design tokens, gradient wordmark, sticky header nav, theme toggle, footer) that every later surface reuses. The north-star authoring loop, delivered end-to-end.
**FRs covered:** FR-10, FR-11, FR-12, FR-13, FR-16, FR-17
**Architecture:** AR-1, AR-2, AR-3, AR-4, AR-5, AR-8, AR-13 · **UX-DR:** 1, 2, 6, 7, 9

### Epic 2: The post reading experience
A visitor reads Carrie's posts (prose, images, syntax-highlighted code, clickable tags, read-time) and browses the full reverse-chronological stream at `/posts/`; Carrie authors a post as a Hugo page bundle following the canonical front-matter contract. Introduces the shared `post-card` partial and the content model — the heart of the site.
**FRs covered:** FR-4, FR-5, FR-6, FR-7
**Architecture:** AR-6, AR-7, AR-9, AR-12 · **UX-DR:** 4, 5, 10, 11, 12, 13

### Epic 3: The front door — Home & About
The recruiter/friend first impression — Home hero (avatar-ring, greeting, topic chips), the latest-posts preview grid, the persistent featured-post slot, and the About page with social links. Reuses the Epic 2 card partial; no new card markup.
**FRs covered:** FR-1, FR-2, FR-3, FR-15
**Architecture:** AR-9 (reuse), AR-10 · **UX-DR:** 3, 5 (reuse), 10, 13

### Epic 4: Wayfinding — topics & search
A visitor clicks a topic chip to see every post under that topic (`/tags/{topic}/`) and uses header search to find posts by title/content. Reuses the card partial for the custom term page; Hextra FlexSearch (the one accepted JS-dependent feature) for search.
**FRs covered:** FR-8, FR-9
**Architecture:** AR-11 · **UX-DR:** 8, 12 (empty state)

### Epic 5: Discoverability & launch
Posts are crawlable and unfurl cleanly when shared; the BMAD build-in-public launch post ships (validating SM-3); deferred items (the analytics tool, the custom 404) are parked as clearly post-launch stories so they don't drift.
**FRs covered:** FR-18, FR-14 *(deferred)*
**Architecture:** SEO/OG conventions, analytics include · **UX-DR:** 12 (404, deferred) · **NFR-3** (privacy)

---

## Epic 1: Publish-by-push foundation — a live, branded site

Carrie can write a markdown file, `git push`, and see a correctly-branded site live at carriekroutil.com — with local preview and build-failure alerts. Establishes the greenfield scaffold, the single Amplify pipeline, and the visual shell every later surface reuses. *Covers FR-10, FR-11, FR-12, FR-13, FR-16, FR-17.*

### Story 1.1: Scaffold the greenfield Hugo + Hextra site with local preview

As Carrie (builder),
I want a fresh Hugo + Hextra project I can run locally,
So that I have a clean foundation to build on and can preview the site before anything goes live.

*Implements FR-16 (CAP-16) · AR-1, AR-8 (AD-5, AD-10).*

**Acceptance Criteria:**

**Given** the `carriekroutil-site` repo contains only BMad tooling (nothing carried from `~/code/quickstart`)
**When** the scaffold is created
**Then** `hugo.toml` exists with the Hextra module import and `baseURL = https://carriekroutil.com/`, a carriekroutil.com `title`, `params.theme.default = dark`, search, and taxonomy config
**And** `go.mod`/`go.sum` lock the Hextra module `github.com/imfing/hextra` at `v0.12.3`
**And** the Structural Seed directory layout exists (`content/`, `layouts/`, `assets/`, `static/`).

**Given** the scaffolded project
**When** Carrie runs `hugo server`
**Then** the site renders in a browser with live reload on save
**And** both draft and non-draft content are visible in local preview (drafts shown locally, withheld from production builds).

**Given** project customization is needed later
**When** a custom layout, partial, or style is added
**Then** it lives in the project's own `layouts/` or `assets/` (shadowing the module), and the Hextra module is never edited in place.

### Story 1.2: Apply the design tokens and dark-default theme

As a visitor,
I want the site to look warm, cozy, and on-brand from the first paint,
So that it reads as *Carrie*, not a stock template.

*Implements FR-12 (CAP-12, palette half) · UX-DR1 · NFR-1 (system fonts), AD-13.*

**Acceptance Criteria:**

**Given** the visual identity in `DESIGN.md`
**When** the site stylesheet is built in `assets/`
**Then** the palette tokens are applied (bg `#0a0a0a`, surfaces `#15151a`/`#1c1c23`, border `#2a2a33`, fg `#ededed`, muted/meta-min `#a0a0ab`, link/focus `#d946ef`, the violet→fuchsia→amber accent gradient, and the chip tints)
**And** the typography scale (display 800 / `-0.03em` / `2.5rem`, body `1.05rem` / line-height ~1.6, mono for code), radii (card `18px`, control `10px`, pill `999px`), and spacing (4px base, `22px` gutter) match the tokens.

**Given** the page loads
**When** rendered in a browser
**Then** the site is dark by default
**And** only system-font stacks are used — there is no web-font network request.

**Given** meta/caption text anywhere on dark surfaces
**When** rendered
**Then** it is never lighter than `#a0a0ab` (the mock's `#70707c` is not used).

### Story 1.3: Build the gradient wordmark, sticky header nav, and footer

As a visitor,
I want a consistent header and footer with the signature wordmark and the key links,
So that I can recognize the site and navigate it from anywhere.

*Implements FR-12 (CAP-12, wordmark) · UX-DR2, UX-DR6, UX-DR9 · NFR-2.*

**Acceptance Criteria:**

**Given** any page
**When** the header renders
**Then** the gradient `carrie kroutil` wordmark (weight 800, accent-gradient text-clip) appears top-left and links Home
**And** where `background-clip:text` is unsupported (forced-colors/print), the wordmark falls back to solid fuchsia `#d946ef`.

**Given** the sticky header
**When** rendered
**Then** it shows (right side) Posts, About, search (⌕), theme toggle (☾), GitHub, and LinkedIn, each a real `<a>`/`<button>` with an accessible label and a visible `2px #d946ef` focus ring on `:focus-visible`
**And** the header is translucent/blurred on scroll and collapses to Hextra's condensed/hamburger nav on mobile.

**Given** any page
**When** the footer renders
**Then** a thin gradient rule sits above a single muted footer line, with GitHub and LinkedIn as the only operable elements
**And** the gradient is used only as a garnish — never as a large background fill behind body text.

### Story 1.4: Dark/light theme toggle with persistence

As a visitor,
I want to switch between dark and light themes and have my choice remembered,
So that I can read comfortably on return visits.

*Implements FR-13 (CAP-13) · UX-DR7, AD conventions.*

**Acceptance Criteria:**

**Given** a first visit
**When** the site loads
**Then** it loads in dark mode by default.

**Given** the header theme toggle (Hextra's built-in, a real focusable control with an accessible label)
**When** the visitor activates it
**Then** the site switches to light mode
**And** the choice persists across page loads and future visits.

**Given** light mode is selected
**When** a story notes accessibility
**Then** light mode ships with untuned Hextra defaults and its AA verification is explicitly deferred (tracked as an open question — dark is the AA-guaranteed path).

### Story 1.5: Stand up the single Amplify pipeline on the correct brand

As Carrie (builder),
I want exactly one correctly-branded deploy pipeline,
So that the site builds reproducibly with no leftover paths or branding from the retired project.

*Implements FR-11 (CAP-11) · AR-2, AR-3, AR-4 (AD-2, AD-10, AD-11).*

**Acceptance Criteria:**

**Given** the repo root
**When** `amplify.yml` is authored
**Then** `preBuild` runs `hugo mod get`, `build` runs `hugo --gc --minify` at a pinned `HUGO_VERSION` (extended), and artifacts are `public/`.

**Given** the Amplify app
**When** configured
**Then** it builds from source on the **default branch only**, serves at `carriekroutil.com` via Route 53, has branch/PR auto-previews **off**, and has a single (production) environment
**And** there is no S3-sync GitHub Action and no GCP artifacts (`app.yaml`/`main.go`/`.gcloudignore`) in the repo.

**Given** the built site
**When** inspected
**Then** `baseURL`/`title` reflect carriekroutil.com with zero SheBytes / `code.shebytes.io` / ananke / quickstart remnants
**And** no throwaway quickstart posts or the `ananke` theme are present.

### Story 1.6: Publish a post by push

As Carrie (builder),
I want pushing a markdown file to put it live with no further steps,
So that publishing never becomes a chore and the habit holds.

*Implements FR-10 (CAP-10) · AR-1, AR-3 (AD-1, AD-2, AD-11). Builds on Story 1.5.*

**Acceptance Criteria:**

**Given** the configured Amplify pipeline
**When** Carrie pushes a commit adding a non-draft markdown post to the default branch
**Then** an Amplify build is triggered automatically and deploys the rendered site
**And** the new post is reachable at its Post Page and appears in the Post Stream after the build completes (a few minutes).

**Given** the publish path
**When** Carrie publishes
**Then** no step requires a CMS login, web editor, or manual file copy/upload — the path is exactly `write markdown → git push`.

### Story 1.7: Surface build outcomes so a broken push is never silent

As Carrie (builder),
I want to be told when a publish build fails,
So that a broken push never silently leaves the old site up without my knowing.

*Implements FR-17 (CAP-17) · AR-5 (AD-12). Builds on Story 1.5.*

**Acceptance Criteria:**

**Given** Amplify's native email-on-failure is enabled
**When** a build fails (e.g. invalid front matter or a broken template)
**Then** Carrie receives an email notification
**And** the prior good site stays live (Amplify only swaps in a build on success).

**Given** a build that succeeds but contains no new published post (e.g. the post was left `draft`)
**When** it completes
**Then** that outcome is distinguishable from a failed build.

---

## Epic 2: The post reading experience

A visitor reads Carrie's posts and browses the full stream; Carrie authors a post as a page bundle following the canonical front-matter contract. Introduces the shared `post-card` partial. *Covers FR-4, FR-5, FR-6, FR-7.*

### Story 2.1: Establish the post content model, front-matter contract, and read-time

As Carrie (builder),
I want a single canonical shape for every post and its tags,
So that independently-authored posts never diverge and metadata stays consistent.

*Implements FR-6 (CAP-6), FR-7 (CAP-7) · AR-6, AR-7 (AD-3, AD-4, AD-8).*

**Acceptance Criteria:**

**Given** the posts section
**When** set up
**Then** posts live in `content/posts/`, whose `_index.md` sets `type: blog` and is the only home for section-level config
**And** each post URL is `/posts/{slug}/` where the slug derives from the post bundle's folder name (a front-matter `slug:` is allowed only as a deliberate override; the title never sets the URL).

**Given** a post's YAML front matter
**When** the site builds
**Then** the contract is enforced: `title` (string), `date` (ISO — **missing or invalid fails the build**, never silently mis-sorts), `tags` (list, lowercase, open taxonomy), `draft` (bool), optional `featured` (bool), optional `hero` (`{ src, alt }` with `alt` required)
**And** a future `date` withholds the post until that date (`buildFuture = false`) rather than vanishing silently.

**Given** a published post
**When** rendered
**Then** read-time is derived automatically from `.ReadingTime`, displayed as `"{n} min read"` with a floor of 1 min, and is never hand-entered
**And** the date displays site-wide as `Jan 2, 2006`.

**Given** Carrie adds a new tag value not seen before
**When** she publishes
**Then** no code change is required — the tag is simply a new lowercase value in `tags`.

### Story 2.2: Render a single post page

As a visitor,
I want to read a full post with its prose, images, and code,
So that I get the whole piece in a comfortable, readable layout.

*Implements FR-5 (CAP-5) · AR-12 (AD-9), NFR-2. Builds on Story 2.1.*

**Acceptance Criteria:**

**Given** a non-draft post at `/posts/{slug}/`
**When** opened
**Then** it renders the title, date, read-time, clickable tag chips, and the markdown body, at the `720px` reading width
**And** code blocks render with syntax highlighting and a way back to the Post Stream is present.

**Given** a post authored as a Hugo page bundle
**When** it includes images
**Then** the hero comes from the `hero` map and inline images use standard markdown, with images beside the markdown in the bundle
**And** every content image carries alt text — a `render-image.html` markup hook **fails the build** on an empty alt; decorative gradient thumbs use `alt=""`.

**Given** a post marked `draft: true`
**When** the site builds for production
**Then** the post does not appear at its Post Page, in the Post Stream, or in build output.

**Given** images on the page
**When** the page loads
**Then** images lazy-load and the core post content reads as static HTML with JavaScript disabled.

### Story 2.3: Build the shared post-card partial

As a visitor,
I want each post previewed as a consistent, clickable card,
So that scanning posts feels coherent everywhere they appear.

*Implements the renderer for FR-2/FR-4/FR-8 · AR-9 (AD-6) · UX-DR4, UX-DR5, UX-DR13.*

**Acceptance Criteria:**

**Given** the card design in the UX `home.html` mock
**When** `layouts/_partials/custom/post-card.html` is built
**Then** a card shows a thumb (the post's image, or a subtle gradient fallback when none), an uppercase accent tag, the title (weight 600), a one-line excerpt, and muted date + read-time
**And** the **entire card is a single click target** to the post.

**Given** a card
**When** hovered or focused
**Then** it shows a subtle lift (translateY ~3px + border brighten) and a visible focus ring
**And** under `prefers-reduced-motion` the hover lift is dropped (the media query is wired, not just intended).

**Given** this is the one card renderer
**When** Home, the posts list, and tag pages need cards
**Then** they all call this same partial (the native `hextra-card` is not used for posts).

### Story 2.4: Browse the full post stream at /posts/

As a visitor,
I want a single page listing every post newest-first,
So that I can scan everything Carrie has written.

*Implements FR-4 (CAP-4) · AR-9 (AD-4, AD-6). Builds on Stories 2.1 and 2.3.*

**Acceptance Criteria:**

**Given** the blog `list.html` is overridden to call the `post-card` partial
**When** a visitor opens `/posts/`
**Then** every non-draft post is listed reverse-chronologically as cards at the `920px` content width, each linking its Post Page and showing title, date, and topic(s).

**Given** posts carry topic tags
**When** a visitor wants a single topic
**Then** the tag chips link to `/tags/{topic}/` (the styled term page is delivered in Epic 4); `/posts/` itself stays the full reverse-chron stream with no in-place client-side filtering.

---

## Epic 3: The front door — Home & About

The recruiter/friend first impression — Home hero, latest-posts preview, the featured slot, and the About page. Reuses the Epic 2 card partial. *Covers FR-1, FR-2, FR-3, FR-15.*

### Story 3.1: Build the Home hero

As a visitor,
I want a warm, personal landing that introduces Carrie,
So that my first impression is "a real, interesting person — come hang out."

*Implements FR-1 (CAP-1) · UX-DR3, UX-DR13 · AD-13. Realizes UJ-1, entry to UJ-2.*

**Acceptance Criteria:**

**Given** the Home content (`content/_index.md`) and home layout
**When** a visitor lands on `/`
**Then** the hero shows the wordmark, a warm avatar with a gradient ring, the greeting "Hey, I'm Carrie 👋", the one-liner, and clickable Topic Chips
**And** each Topic Chip links to its Tag Page (`/tags/{topic}/`).

**Given** the page
**When** JavaScript is disabled
**Then** the hero and core content render and read as static HTML
**And** the page uses the `920px` content width with the voice/microcopy from `EXPERIENCE.md` (casual, warm, never self-deprecating).

### Story 3.2: Show the latest-posts preview grid on Home

As a visitor,
I want to see Carrie's most recent posts on the landing page,
So that I can jump straight into something to read.

*Implements FR-2 (CAP-2) · AR-9 reuse (AD-6) · UX-DR5 reuse, UX-DR10. Builds on Story 2.3.*

**Acceptance Criteria:**

**Given** the Home page below the hero
**When** rendered
**Then** the 4–6 most-recent posts appear as a grid of `post-card`s (reusing the Epic 2 partial), newest first, each card a single click target to its Post Page
**And** an "all posts →" affordance links to `/posts/`.

**Given** the viewport width
**When** it is wide
**Then** cards lay out 2-up, collapsing to 1-up under ~620px.

**Given** few or no posts exist yet
**When** Home renders
**Then** it still reads as intentional (the hero carries it) rather than looking broken.

### Story 3.3: Pin a featured post on Home

As Carrie (builder),
I want one post I can keep featured regardless of recency,
So that the launch/BMAD post stays the recruiter's entry point as I publish more.

*Implements FR-15 (CAP-15) · AR-10 (AD-7). Builds on Story 3.2.*

**Acceptance Criteria:**

**Given** a post with `featured: true` in its front matter
**When** Home renders
**Then** exactly one featured slot is shown, visually distinguishable from the recent-posts grid, and it does not drop off as newer posts are published.

**Given** more than one post is flagged `featured`
**When** Home selects the featured post
**Then** candidates are sorted by `date` descending and the first is taken (a deterministic tiebreak).

**Given** no post is flagged `featured`
**When** Home renders
**Then** the slot falls back cleanly to the most-recent post (same date-desc ordering) with no empty slot.

### Story 3.4: Build the About page

As a visitor,
I want a human-first hello with a photo and a way to reach Carrie's profiles,
So that I get who she is and can confirm the professional signal.

*Implements FR-3 (CAP-3) · UX-DR10, AD-13. Realizes the UJ-1 confirm step.*

**Acceptance Criteria:**

**Given** `content/about.md`
**When** a visitor opens `/about/`
**Then** it renders a photo, bio text that threads lightly to the professional self, Topic Chips, and links/buttons to GitHub and LinkedIn, at the `720px` reading width.

**Given** the social links
**When** clicked
**Then** they open `https://github.com/carriekroutil` and `https://www.linkedin.com/in/carriekroutil` (the correct external profiles).

---

## Epic 4: Wayfinding — topics & search

A visitor clicks a chip to see every post under a topic, and uses header search to find posts. Reuses the card partial for term pages. *Covers FR-8, FR-9.*

### Story 4.1: Styled tag term pages at /tags/{topic}/

As a visitor,
I want clicking a topic to show every post under it,
So that I can follow a thread (dogs, golf, AI…) across the whole site.

*Implements FR-8 (CAP-8) · AR-11 (AD-8) · UX-DR12 (empty state). Reuses Story 2.3.*

**Acceptance Criteria:**

**Given** a custom `layouts/_default/term.html` reusing the `post-card` partial
**When** a visitor clicks a Topic Chip and lands on `/tags/{topic}/`
**Then** the page lists every post carrying that tag, newest first, as cards
**And** tag filtering is Hugo-core/native with no client-side JavaScript.

**Given** a topic with no posts yet
**When** its tag page is viewed
**Then** it shows a light "nothing here yet — check back" state rather than a blank page.

### Story 4.2: Header full-text search

As a visitor,
I want to search posts from the header,
So that I can find a specific post by typing.

*Implements FR-9 (CAP-9) · UX-DR8 (AD-13).*

**Acceptance Criteria:**

**Given** Hextra FlexSearch is enabled
**When** a visitor activates the header search (⌕, a real focusable button with an `aria-label`)
**Then** results filter across post titles and content as they type and are keyboard-navigable.

**Given** JavaScript is unavailable
**When** the site loads
**Then** search degrades gracefully and the rest of the site still works (search is the one accepted JS-dependent exception to no-JS core reading).

---

## Epic 5: Discoverability & launch

Posts are crawlable and unfurl cleanly when shared; the launch post ships; deferred items are parked as clearly post-launch stories. *Covers FR-18, FR-14 (deferred).*

### Story 5.1: Basic discoverability — sitemap, robots, and clean share metadata

As an occasional stranger who googles a topic,
I want Carrie's relevant posts to be findable and to unfurl cleanly when shared,
So that I can actually land on and preview the right post.

*Implements FR-18 (CAP-18) · AD conventions, NFR-1.*

**Acceptance Criteria:**

**Given** the built site
**When** crawled
**Then** it generates a sitemap and serves a `robots.txt` that allows indexing
**And** post URLs are stable, human-readable `/posts/{slug}/` slugs.

**Given** a Post Page
**When** rendered
**Then** it emits a descriptive `<title>`, a meta description, and Open Graph / Twitter-card tags (title, description, and hero image where present, reading `hero.src`/`hero.alt`)
**And** a shared link unfurls with that title, description, and image.

### Story 5.2: Publish the BMAD build-in-public launch post

As Carrie (builder),
I want the first real post to be the story of building this site with the BMAD method,
So that the launch teaches and stands as proof-by-construction for a friend or a recruiter.

*Implements the launch content · validates SM-3. Uses the Story 2.1 contract; featured via Story 3.3.*

**Acceptance Criteria:**

**Given** the front-matter contract (Story 2.1)
**When** the launch post is authored as a page bundle
**Then** it is a complete, non-draft post with `featured: true`, valid front matter, and at least one alt-texted image, that renders correctly at `/posts/{slug}/`.

**Given** the post is live
**When** Carrie reviews it
**Then** it genuinely teaches (useful to a reader, not just self-promotional) and she is comfortable sending the link to both a friend and a recruiter.

### Story 5.3 *(deferred — post-launch)*: Wire a lightweight, privacy-friendly analytics include

As Carrie (builder),
I want a simple privacy-friendly readership signal,
So that I can see whether anyone is reading — without trackers or cookie banners.

*Implements FR-14 (CAP-14) · NFR-3. **Deferred: not a launch gate; revisit at/just after launch — don't let it drift.***

**Acceptance Criteria:**

**Given** a tool is chosen (GoatCounter or Cloudflare Web Analytics — **not** Mixpanel)
**When** it is integrated
**Then** it is a single privacy-friendly script include with no cookie-consent banner required and no PII collected.

**Given** this is deferred
**When** launch readiness is assessed
**Then** the site can launch without this story complete (it carries no launch dependency).

### Story 5.4 *(deferred — post-launch)*: On-brand custom 404 page

As a lost visitor,
I want a friendly on-brand "not found" page with a way home,
So that a wrong URL feels like Carrie, not a server error.

*Implements UX-DR12 (404). **Deferred per PRD §6.2 — desired, non-blocking.***

**Acceptance Criteria:**

**Given** an unknown URL
**When** it is requested
**Then** an on-brand 404 renders with the copy "This page wandered off the fairway." and a clear path back home.

**Given** this is deferred
**When** launch readiness is assessed
**Then** the site can launch with Hugo/Hextra's default 404 (this story is non-blocking polish).
