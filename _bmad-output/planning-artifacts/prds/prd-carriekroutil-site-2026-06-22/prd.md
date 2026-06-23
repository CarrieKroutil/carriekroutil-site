---
title: carriekroutil.com
status: final
created: 2026-06-22
updated: 2026-06-22
---

# PRD: carriekroutil.com
*Working title — confirm.*

## 0. Document Purpose

This PRD is for Carrie (builder/PM) and any downstream BMAD workflows — UX is already done, so the natural next steps are architecture and epics/stories. It is structured Glossary-first, with features grouped and Functional Requirements (FR-N) nested and globally numbered for stable downstream reference; assumptions are tagged inline as `[ASSUMPTION]` and indexed in §9. This document **builds on two finalized inputs and does not duplicate them**:

- **Brief** — `_bmad-output/planning-artifacts/briefs/brief-carriekroutil-site-2026-06-22/` (vision, scope, decisions).
- **UX design** — `_bmad-output/planning-artifacts/ux-designs/ux-carriekroutil-site-2026-06-22/` (`DESIGN.md`, `EXPERIENCE.md`, mockups for home/about/post-single, palette). The UX spine is authoritative for visual tokens, IA, and journeys; where this PRD touches those areas it points there rather than restating detail.

## 1. Vision

`carriekroutil.com` is Carrie's durable home on the web — a warm, human-first landing/about page plus an easy-to-grow stream of short posts spanning her whole range: golf, dogs, code, AI, leadership, trips. The promise is "here's the whole human, come hang out" first, and "here's what I'm about, professionally" underneath. It exists because no single place online is *hers*: social platforms fragment her across feeds she doesn't own, and a resume shows the professional slice but not the person.

The site's defining constraint is **friction-free publishing**. Posting is markdown → `git push` → live in minutes, with no CMS, no login, and no manual copy steps. The whole architecture exists to protect the posting habit: *if posting isn't trivially easy, the posts stop.* Success is measured in years of sustained, low-effort publishing — not in traffic.

It also doubles, quietly, as professional proof-of-work. The mixed-topic stream signals range and judgment, and the launch post — the story of building this site greenfield with the BMAD method — both teaches (it shows, concretely, how approachable the build was) and stands as proof by construction for anyone (a recruiter, a peer) evaluating whether Carrie ships and explains clearly.

## 2. Target User

### 2.1 Jobs To Be Done

- **For Carrie (builder):** give me one place that's *mine* — that I own end-to-end (domain, content, pipeline) and can add to on a whim without it becoming a chore.
- **For Carrie (builder):** let me show the whole human — hobbies *and* professional range — without it reading like a resume or a sterile portfolio.
- **For "curious humans" (friends, peers, the occasional stranger):** give me personality and a good read, and a clear sense of who Carrie is. Success feels like *"that was fun/useful, and I get who she is."*
- **For recruiters & professional network:** show me signal — range, judgment, communication, evidence she builds and ships. Success feels like *"interesting, capable, clearly makes things."*

### 2.2 Non-Users (v1)

- People wanting to **interact** on the site — comment, create accounts, submit content. The site is read-only and one-author by design.
- Anyone looking for a **formal resume / CV experience.** The professional thread is deliberately woven through the human story, not presented as a credential list.

### 2.3 Key User Journeys

*Mirrored from the finalized UX (`EXPERIENCE.md`), which is authoritative. FRs reference these by ID.*

- **UJ-1. Dana, the recruiter, does her homework.**
  - **Persona + context:** Dana has Carrie's resume open in another tab and clicks through to the site on a laptop.
  - **Path:** Lands on **Home** → reads as a real, interesting person (gradient wordmark, warm photo, one-liner) → the featured *"I built this site with the BMAD method"* post catches her → reads it.
  - **Climax:** "She actually builds and ships, and explains it clearly" — credibility without a resume vibe.
  - **Resolution:** Taps GitHub + LinkedIn from the header to confirm; leaves with a positive read.

- **UJ-2. Sam, a friend, on the couch.**
  - **Persona + context:** Carrie texts Sam a link to a new dog post; Sam opens it on a phone.
  - **Path:** Reads it (dog photos, Carrie's voice, a laugh) in a cozy single column → taps the **dogs** chip → dogs tag page → wanders into a **trips** post.
  - **Climax:** "This is just *her*."
  - **Resolution:** Bookmarks the site.

- **UJ-3. Carrie publishes on a Sunday.**
  - **Persona + context:** Carrie, with ten spare minutes, wants a new post live.
  - **Path:** Creates a markdown file, writes, drops in two photos, sets tags (`golf`, maybe `trips`) → `git push`.
  - **Climax:** Amplify builds; the post is live in a couple of minutes — no CMS, no friction.
  - **Resolution:** "Wrote it, pushed it, it's live." The habit holds.

## 3. Glossary

- **Post** — a single piece of content authored as a markdown file — optionally a Hugo **page bundle** (a folder pairing the markdown with its images) — with front matter (title, date, tags, optional hero image with alt text). Rendered as a **Post Page** and listed in the **Post Stream**.
- **Post Stream** — the reverse-chronological collection of all Posts; surfaced fully at `/posts/` and previewed on the **Home** page.
- **Topic** — one of the site's content threads (golf, dogs, code, AI, leadership, trips), expressed as a **Tag** on a Post. Each Topic has a **Tag Page**.
- **Tag** — the front-matter field that assigns a Post to one or more Topics; rendered as a clickable **Topic Chip**.
- **Tag Page** — an auto-generated page (`/tags/{topic}/`) listing every Post under one Topic.
- **About Page** — the human-first introduction to Carrie (`/about/`): photo, bio, social links.
- **Wordmark** — the signature `carrie kroutil` brand element: weight-800 text in a left-to-right violet→fuchsia→amber gradient, top-left in the header, linking Home.
- **Authoring Loop** — the publish path: write a markdown file → `git push` → the deploy pipeline builds and the change goes live, with no CMS or manual copy step.
- **Deploy Pipeline** — AWS Amplify building the site from source on push to the `carriekroutil-site` repo, served on the `carriekroutil.com` domain via Route 53.

## 4. Features

### 4.1 Home / Landing

**Description:** The front door — "whole human, come hang out." A hero (avatar with gradient ring, greeting "Hey, I'm Carrie 👋", one-liner, Topic Chips) over a *Latest posts* card grid, over a footer. Realizes UJ-1 (first impression) and is the entry point for UJ-2. Visual detail (layout, 920px content width, card behavior) is specified in the UX spine.

**Functional Requirements:**

#### FR-1: Home hero introduces Carrie
The visitor lands on `/` and sees the Wordmark, a warm avatar, the greeting, the one-liner, and clickable Topic Chips. Realizes UJ-1.

**Consequences (testable):**
- `/` renders the Wordmark, avatar, greeting text, one-liner, and at least the active Topic Chips.
- Each Topic Chip links to its Tag Page (`/tags/{topic}/`).
- The page is usable and readable without JavaScript (core content is static HTML).

#### FR-2: Home previews the latest posts
The visitor sees a preview grid of the most recent Posts, each a single click target leading to its Post Page. Realizes UJ-1, UJ-2.

**Consequences (testable):**
- Home shows up to 4–6 most-recent Posts as cards, newest first.
- Each card shows title, a Topic tag, an excerpt, and date + read-time; the whole card links to the Post Page.
- An "all posts →" affordance links to `/posts/`.
- Before many Posts exist, the page still reads as intentional (hero carries it). `[ASSUMPTION: thin empty/near-empty state is acceptable for launch.]`

#### FR-15: Featured post on Home
One designated Post (at launch, the BMAD build-in-public story) is featured on Home independent of recency, so it stays visible as the recruiter's entry point. Realizes UJ-1; validates SM-3.

**Consequences (testable):**
- Home renders a featured Post that does not drop off as newer Posts are published (e.g. a `featured: true` front-matter flag or pinned slot). `[ASSUMPTION: a single featured Post via front-matter flag; exact mechanism settled in architecture.]`
- The featured Post is visually distinguishable from the recent-Posts grid (or occupies a dedicated slot).
- When no Post is flagged featured, Home falls back cleanly to most-recent (FR-2) with no empty slot.

### 4.2 About

**Description:** The human-first hello with a photo, a short bio that threads lightly to Carrie's professional self, and social links. Realizes UJ-1 (the confirm step). Mockup exists (`about.html`).

**Functional Requirements:**

#### FR-3: About page presents Carrie
The visitor at `/about/` sees a photo, bio, Topic Chips, and links to GitHub and LinkedIn.

**Consequences (testable):**
- `/about/` renders a photo, bio text, and buttons/links to `https://github.com/carriekroutil` and `https://www.linkedin.com/in/carriekroutil`.
- Social links open the correct external profiles.

### 4.3 Post Stream

**Description:** The heart of the site — a list of all Posts plus individual Post Pages. Each Post is one markdown file; the system renders prose, images, and code with syntax highlighting at a comfortable 720px reading width. Realizes UJ-1, UJ-2.

**Functional Requirements:**

#### FR-4: Browse all posts
The visitor at `/posts/` sees every published Post in reverse-chronological order. Realizes UJ-2.

**Consequences (testable):**
- `/posts/` lists all non-draft Posts, newest first.
- Each entry links to its Post Page and shows title, date, and Topic(s).
- The list can be narrowed to a single Topic (selecting a Topic filters the Post Stream, consistent with the Tag Page in FR-8). `[ASSUMPTION: Tag-Page-based or client-side filtering per Hextra/UX; exact mechanism settled in architecture.]`

#### FR-5: Read a single post
The visitor opens a Post Page and reads the full Post — title, date, read-time, Tags, and body (prose, images, code). Realizes UJ-1, UJ-2.

**Consequences (testable):**
- `/posts/{slug}/` renders the Post's title, date, read-time, clickable Tags, and rendered markdown body.
- Code blocks render with syntax highlighting; images render with their alt text.
- A way back to the Post Stream (and/or prev/next navigation) is present.
- Posts marked `draft` do not appear in the Post Stream or build output.
- Images (hero and inline) are supported via Hugo page bundles or the `static` directory, with a defined front-matter/shortcode shape; every content image carries alt text. `[ASSUMPTION: page-bundle approach; exact image shape and any optimization settled in architecture.]`

#### FR-6: Read-time and metadata
Each Post displays a read-time estimate and publication date drawn from content and front matter.

**Consequences (testable):**
- Read-time is derived automatically (no manual entry). `[ASSUMPTION: Hextra/Hugo word-count read-time is acceptable.]`
- Date comes from front matter; missing/invalid dates surface at build time rather than silently mis-sorting. `[ASSUMPTION]`

### 4.4 Topic Organization

**Description:** Light topic threading so the site reads as a person with range, not a single-topic blog. Tags on Posts become clickable Topic Chips and auto-generated Tag Pages. Realizes UJ-2.

**Functional Requirements:**

#### FR-7: Tag a post by topic
Carrie assigns one or more Topics to a Post via the `tags` front-matter field. Realizes UJ-3.

**Consequences (testable):**
- A Post's `tags` values render as Topic Chips on its Post Page and cards.
- Topics beyond the initial six (golf, dogs, code, AI, leadership, trips) are supported without code changes — adding a new tag value is sufficient. `[ASSUMPTION: open tag taxonomy, no fixed allowlist.]`

#### FR-8: Browse a topic
The visitor clicks a Topic Chip and sees every Post under that Topic. Realizes UJ-2.

**Consequences (testable):**
- `/tags/{topic}/` lists all Posts carrying that Tag, newest first.
- An empty Tag Page shows a light "nothing here yet — check back" state rather than a blank page. `[ASSUMPTION]`

### 4.5 Search

**Description:** Full-text search over Posts, provided by Hextra's built-in FlexSearch — a header trigger that filters as you type. Comes "for free" with the theme.

**Functional Requirements:**

#### FR-9: Search posts
The visitor activates search from the header and finds Posts by typing across titles and content.

**Consequences (testable):**
- A focusable search control in the header opens Hextra search; results filter on title/content as the visitor types and are keyboard-navigable.
- Search degrades gracefully when JavaScript is unavailable (the rest of the site still works). Search is JS-dependent and is the accepted exception to "core reading works without JS".

### 4.6 Authoring & Deploy Pipeline

**Description:** The feature that makes the whole product sustainable — the Authoring Loop. Carrie writes a markdown file, commits, and pushes; AWS Amplify builds from source and the change goes live on `carriekroutil.com` in minutes. No CMS, no admin UI, no manual copy step. Realizes UJ-3. This is the north-star capability — *if Carrie ever hesitates to post because of process, the design failed.*

**Functional Requirements:**

#### FR-10: Publish by push
Carrie publishes a new Post by adding a markdown file to the repo and pushing; it appears live without further manual steps. Realizes UJ-3.

**Consequences (testable):**
- A push to the default branch of `carriekroutil-site` triggers an Amplify build that deploys the rendered site.
- A new non-draft Post is reachable at its Post Page and appears in the Post Stream after the build completes.
- No step in the path requires a CMS login, web editor, or manual file copy/upload.
- Build-to-live time is a few minutes. `[ASSUMPTION: "few minutes" ~= under 5 min, not a hard SLA.]`

#### FR-11: Single settled pipeline on the correct brand
The site builds and serves from exactly one pipeline, correctly branded — and none of the retired project's deploy paths or branding come along when site code lands in `carriekroutil-site`.

**Consequences (testable):**
- The site is served at `carriekroutil.com` via Route 53; `baseURL`/`title` reflect carriekroutil.com (no `code.shebytes.io` / "SheBytes" remnants).
- Only the Amplify pipeline builds `carriekroutil-site`. The retired project's deploy artifacts (the S3-sync GitHub Action and GCP App Engine files `app.yaml` / `main.go` / `.gcloudignore`) live in the old `~/code/quickstart` location, **not** in this repo; the requirement is simply that they are **not carried in**. Disabling the old location so it stops auto-deploying is a manual one-time task Carrie performs (see §8 OQ#2).
- Throwaway quickstart content (dummy posts) and the prior `ananke` theme are not carried into v1.

#### FR-16: Preview locally before publishing
Carrie can preview the rendered site locally before pushing, so mistakes are caught before they reach production. Realizes UJ-3.

**Consequences (testable):**
- Running the local dev server (`hugo server`) renders the site with the new/edited Post, reachable in a browser, with live reload on save.
- The local preview reflects draft and non-draft Posts so Carrie sees the Post before publishing.

#### FR-17: Build outcomes are surfaced
Carrie is told when a publish build fails, so a broken push is never a silent no-op — the failure mode the whole thesis guards against.

**Consequences (testable):**
- A failed Amplify build (e.g. invalid front matter, broken template) notifies Carrie rather than silently leaving the old site live. `[ASSUMPTION: Amplify build-failure notification — email or console — wired up; exact channel settled in architecture.]`
- A successful build that contains no new published Post (e.g. the Post was left `draft`) is distinguishable from a failed build.

### 4.7 Appearance & Theme

**Description:** The site looks like Carrie — playful, cozy, warm; dark by default with a light toggle. The signature gradient lives only on the Wordmark, avatar ring, small tags/accents, and a thin footer rule (never a large background fill). Full visual tokens, palette hex values, typography, and accessibility floors are specified in the UX spine and are authoritative; the FRs below capture only the load-bearing behaviors.

**Functional Requirements:**

#### FR-12: Brand identity is consistent and on-palette
The Wordmark and accent palette (violet `#8b5cf6` → fuchsia `#d946ef` → amber `#f59e0b` on near-black `#0a0a0a`) appear consistently per the UX spine.

**Consequences (testable):**
- The header renders the gradient Wordmark linking Home, with a solid `#d946ef` fallback where `background-clip:text` is unsupported (forced-colors/print).
- Accent usage matches the UX spine's "gradient is a garnish" rule (no large gradient background fills behind body text).

#### FR-13: Dark/light theme toggle
The visitor can switch between dark (default) and light themes, and the choice persists across visits.

**Consequences (testable):**
- The site loads in dark mode by default; a real focusable, labeled toggle switches to light; the choice persists (Hextra-provided).
- `[ASSUMPTION: light mode inherits untuned Hextra defaults for v1; the three accents must be re-verified for AA on light surfaces before relying on them — see Open Questions.]`

### 4.8 Analytics *(deferred decision)*

**Description:** A lightweight, privacy-friendly way to see whether anyone is reading — *not* a launch gate, but a conscious "pick one soon." A single privacy-respecting script include; no cookie banners, no heavy product analytics, no PII.

**Functional Requirements:**

#### FR-14: Lightweight, privacy-friendly readership signal
Once chosen, basic readership (e.g. page views) is visible to Carrie via a single privacy-friendly include.

**Consequences (testable):**
- The chosen tool is one of GoatCounter or Cloudflare Web Analytics (not Mixpanel); integration is a single script/snippet, no cookie consent banner required.
- No personally identifying information is collected.

**Notes:** `[NOTE FOR PM]` Tool not yet chosen — deferred decision, see §8. Carries no launch dependency; may land shortly after launch.

### 4.9 Discoverability

**Description:** Light, zero-maintenance discoverability so the occasional stranger who googles a golf or AI topic can actually land on a relevant Post — a named brief audience. Hugo provides most of this out of the box; the requirement is to keep it on and clean, not to run an SEO-growth program.

**Functional Requirements:**

#### FR-18: Basic discoverability
Published Posts are crawlable and unfurl cleanly when shared.

**Consequences (testable):**
- The site generates a sitemap and serves a `robots.txt` that allows indexing.
- Each Post Page emits a descriptive `<title>`, a meta description, and Open Graph / Twitter-card tags (title, description, and hero image where present) so shared links unfurl well.
- Post URLs are stable, human-readable slugs (`/posts/{slug}/`).
- `[ASSUMPTION: Hugo/Hextra defaults cover most of this; v1 verifies they are enabled rather than building custom SEO tooling.]`

## 5. Non-Goals (Explicit)

- **No interactivity / UGC** — no comments, accounts, reactions, or visitor-submitted content. The site is read-only and single-author.
- **No CMS or web editor** — markdown-and-push *is* the product; an admin UI would defeat the purpose.
- **No newsletter / email capture** and **no heavy product analytics** in v1.
- **No resume/CV section** — the professional signal is woven through the content, not a credential list.
- **No docs/guides section** — considered and dropped for v1 (Hextra can add it later if ever wanted).
- **No server backend** — static files only; Carrie owns domain, content, and pipeline (no platform lock-in).
- **No carry-over** of SheBytes branding or throwaway quickstart content.

## 6. MVP Scope

### 6.1 In Scope

- Home/landing page, including a featured Post slot (FR-1, FR-2, FR-15).
- About page (FR-3).
- Post Stream: list with Topic filtering + Post Pages with prose, images, code (FR-4–FR-6).
- Topic organization: tags, Topic Chips, Tag Pages over golf/dogs/code/AI/leadership/trips (FR-7, FR-8).
- Built-in search (FR-9).
- The Authoring Loop on a single Amplify pipeline — local preview and build-failure feedback included — correctly branded to carriekroutil.com, in the `carriekroutil-site` repo (FR-10, FR-11, FR-16, FR-17).
- Brand identity, dark-default + light toggle (FR-12, FR-13).
- Basic discoverability — sitemap, clean meta/Open Graph (FR-18).
- The first real Post: the BMAD greenfield build-in-public story.

### 6.2 Out of Scope for MVP

- **Analytics tool wired in** — decision deferred; capability defined (FR-14) but choice and integration may land shortly after launch. `[NOTE FOR PM: emotionally load-bearing "pick one soon" — revisit at/just after launch, don't let it drift indefinitely.]`
- **Light-mode color tuning** — v1 accepts untuned Hextra light defaults; tune later.
- **Custom 404 page** — on-brand "wandered off the fairway" copy is desired but deferred. `[ASSUMPTION: non-blocking.]`
- **Instagram and other social links** — GitHub + LinkedIn only for v1.
- **Docs/guides section, comments, newsletter** — see Non-Goals.

## 7. Success Metrics

**Primary**
- **SM-1 (the habit holds):** Carrie keeps publishing — a loose target of **~1–2 Posts/month** early on, with cadence (not count) as the real test. Measured directly from Post dates / git history. Validates FR-10, FR-11. *Success: still being updated a year — ideally three — from now.*
- **SM-2 (publishing stays a single push):** the publish path remains exactly `write → git push` with **zero added manual steps**, and Carrie can preview locally before pushing. Observable proxy for "never hesitates because of process": if the path ever grows a manual step, SM-2 has regressed. Validates FR-10, FR-16, FR-17.

**Secondary**
- **SM-3 (the launch lands):** the BMAD build-in-public Post is published, shareable, and genuinely teaches (useful to a reader, not just self-promotional), and Carrie is comfortable sending the link to both a friend and a recruiter. Validates FR-1, FR-5, FR-15.
- **SM-4 (fast & looks like her):** pages load fast on mobile and read as warm/personal, not corporate-template. Validates FR-12, NFR-1.

**Counter-metrics (do not optimize)**
- **SM-C1 (don't chase traffic):** raw pageviews/visitor counts are *not* a success target and should not drive decisions. Counterbalances any readership signal from FR-14 — the ceiling is sustained publishing, not reach.
- **SM-C2 (don't add features that add friction):** number of site features is not a goal; any addition that complicates the Authoring Loop is a regression, not progress. Counterbalances the urge to expand §4.

## 8. Open Questions

1. **Analytics tool** — GoatCounter vs. Cloudflare Web Analytics. Deferred, not a launch gate; pick before or shortly after launch (see FR-14). *(Owner: Carrie.)*
2. **Legacy deploy cleanup** — a manual one-time task in the old `~/code/quickstart` location: disable the S3-sync GitHub Action and remove the GCP App Engine artifacts (`app.yaml`, `main.go`, `.gcloudignore`) so the retired project stops auto-deploying. None of these exist in `carriekroutil-site`; the requirement is not to carry them in (see FR-11). *(Owner: Carrie, manual.)*
3. **Light-mode AA** — re-verify the three accent colors meet WCAG AA on Hextra's light surfaces before relying on light mode (see FR-13, NFR-2).
4. **Custom 404** — confirm the on-brand 404 ("wandered off the fairway") is acceptable to defer past v1. *(Tag/home empty-state copy is in scope — see FR-2, FR-8.)*
5. **Hextra blog-capability spike** — before architecture commits, verify Hextra (primarily a docs theme) cleanly supports the blog surfaces (post list, cards/excerpts, tag pages, read-time) or quantify the custom layout needed. Most pieces are Hugo-native (taxonomies, `.ReadingTime`, `.Summary`); this just confirms it. *(Resolve in architecture.)*

## 9. Cross-Cutting NFRs

*System-wide qualities not tied to one feature. The UX spine is authoritative on exact accessibility tokens; these capture the load-bearing requirements.*

- **NFR-1 (Performance):** static site, fast on mobile. No web-font network load (system font stacks); images lazy-load; core reading works without JavaScript. `[ASSUMPTION: no formal perf budget beyond "fast static"; treat as a guideline, not an SLA.]`
- **NFR-2 (Accessibility — WCAG AA):** the **dark (default) theme meets WCAG AA** per the UX accessibility review — body/muted/link/chip contrasts verified, meta text never lighter than `#a0a0ab` on dark. **Light mode ships with untuned Hextra defaults and is not yet AA-verified** (deferred — see §8 OQ#3). Structural accessibility holds in both themes: one `<h1>` per page, logical heading order, landmark regions (header / main / footer); visible `2px #d946ef` focus ring on `:focus-visible`; all nav/search/toggle/social are real `<a>`/`<button>` with accessible labels; every content image has alt text (enforced via front-matter `alt` field); `prefers-reduced-motion` honored.
- **NFR-3 (Privacy):** no cookies for consent-requiring tracking; no PII collected; any analytics is privacy-friendly and script-include only (FR-14).
- **NFR-4 (Maintainability / longevity):** stack stays low-maintenance and durable — Hugo + actively-maintained Hextra theme via Hugo Modules; no toolchain that adds publishing friction. Prefer **one boring, reliable deploy pipeline** over clever multi-path setups. The design optimizes for *still being updatable in three years*. `[ASSUMPTION: if Hextra disappoints, FixIt (the actively-maintained LoveIt successor) is the vetted fallback theme.]`
- **NFR-5 (Ownership / no lock-in):** Carrie owns the domain, content (plain markdown in her repo), and pipeline; the site is portable off Amplify if needed.

## 10. Assumptions Index

*Every `[ASSUMPTION]` from the document, for explicit confirmation:*

- §4.1 / FR-2 — thin near-empty Home state acceptable at launch. *(Post count confirmed: 4–6.)*
- §4.1 / FR-15 — single featured Post via a front-matter flag; exact mechanism settled in architecture.
- §4.3 / FR-4 — Topic filtering on `/posts/` via Tag-Page or client-side mechanism; settled in architecture.
- §4.3 / FR-5 — images via Hugo page bundles; exact image shape/optimization settled in architecture.
- §4.3 / FR-6 — Hextra/Hugo automatic read-time is acceptable; invalid front-matter dates should surface at build time.
- §4.4 / FR-7 — Open tag taxonomy (no fixed allowlist); FR-8 — empty Tag Page shows a light "check back" state.
- §4.6 / FR-10 — "few minutes" build-to-live ≈ under 5 minutes, not a hard SLA.
- §4.6 / FR-17 — Amplify build-failure notification channel (email/console) settled in architecture.
- §4.7 / FR-13 — Light mode ships with untuned Hextra defaults for v1; accents re-verified for AA later.
- §4.9 / FR-18 — Hugo/Hextra defaults cover sitemap/meta/OG; v1 verifies they're enabled rather than building SEO tooling.
- §6.2 — Custom 404 deferral is non-blocking.
- §9 / NFR-2 — Light-mode AA deferred; dark theme is the AA-guaranteed v1 path.
- §9 / NFR-4 — FixIt is the vetted fallback theme if Hextra disappoints.
- §9 / NFR-1 — No formal performance budget beyond "fast static."
