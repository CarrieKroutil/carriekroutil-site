---
id: SPEC-carriekroutil-site
companions:
  - ../../planning-artifacts/architecture/architecture-carriekroutil-site-2026-06-22/ARCHITECTURE-SPINE.md
  - ../../planning-artifacts/prds/prd-carriekroutil-site-2026-06-22/prd.md
  - ../../planning-artifacts/ux-designs/ux-carriekroutil-site-2026-06-22/DESIGN.md
  - ../../planning-artifacts/ux-designs/ux-carriekroutil-site-2026-06-22/EXPERIENCE.md
sources: []
---

> **Canonical contract.** This SPEC and the files in `companions:` are the complete, preservation-validated contract for what to build, test, and validate. Source documents listed in frontmatter are for traceability only — consult them only if you need narrative rationale or prose color this contract intentionally omits.

# carriekroutil.com

## Why

A **vision to realize**: Carrie wants one durable place on the web that is *hers* — owned end-to-end (domain, content, pipeline) — that presents the whole human (golf, dogs, code, AI, leadership, trips) first and the professional thread underneath, without reading like a resume. It exists because social platforms fragment her across feeds she doesn't own and a resume shows only the professional slice. The defining force is **friction-free publishing**: *if posting isn't trivially easy, the posts stop.* Every trade-off resolves toward protecting the posting habit over years, not toward traffic. Quietly it also serves as professional proof-of-work — the mixed-topic stream signals range and judgment, and the launch post (building this site greenfield with the BMAD method) stands as proof by construction.

This spec is the machine contract that unifies the finalized PRD's functional requirements with the architecture spine's design decisions under one stable capability spine. **Each `CAP-N` mirrors the PRD's `FR-N` 1:1** and names the architecture decision(s) (`AD-n`) that govern it, so downstream work (epics, stories, implementation, verification) can cite all three without renumbering. The full per-requirement testable detail lives in the adopted PRD companion; the full design rules live in the adopted `ARCHITECTURE-SPINE.md`.

## Capabilities

- **CAP-1** *(FR-1 · AD-6, AD-13)*
  - **intent:** A visitor landing on `/` meets Carrie through the wordmark, a warm avatar, a greeting, a one-liner, and clickable topic chips.
  - **success:** `/` renders all five elements; each chip links to `/tags/{topic}/`; the page reads without JavaScript.

- **CAP-2** *(FR-2 · AD-6)*
  - **intent:** A visitor previews the latest posts on Home as full-card link targets so any one is a single click away.
  - **success:** Home shows the 4–6 most-recent posts newest-first, each card showing title, a topic tag, an excerpt, and date + read-time with the whole card linking the post; an "all posts →" affordance links `/posts/`.

- **CAP-3** *(FR-3 · AD-13)*
  - **intent:** A visitor at `/about/` gets the human-first hello — photo, bio, topic chips, and social links.
  - **success:** `/about/` renders photo, bio, and links that open `github.com/carriekroutil` and `linkedin.com/in/carriekroutil`.

- **CAP-4** *(FR-4 · AD-4, AD-8)*
  - **intent:** A visitor browses the full post stream at `/posts/` and can narrow it to a single topic.
  - **success:** `/posts/` lists every non-draft post reverse-chron (each entry links its post and shows title/date/topics); topic narrowing is served by tag term-pages with no client-side JS filtering.

- **CAP-5** *(FR-5 · AD-4, AD-9)*
  - **intent:** A visitor reads a full post — prose, images, and code — at `/posts/{slug}/`.
  - **success:** The post page renders title, date, read-time, clickable tags, and the markdown body with syntax-highlighted code and alt-texted images; drafts never appear; a way back to the stream is present.

- **CAP-6** *(FR-6 · AD-3)*
  - **intent:** Each post shows read-time and publication metadata derived from content and front matter.
  - **success:** Read-time is auto-derived (`.ReadingTime`, `"{n} min read"`, floor 1) and never hand-entered; a missing/invalid front-matter date fails the build rather than silently mis-sorting.

- **CAP-7** *(FR-7 · AD-3, AD-8)*
  - **intent:** Carrie assigns one or more topics to a post via the `tags` front-matter field.
  - **success:** Tag values render as topic chips on the post and its cards; the taxonomy is open (lowercase-authored) — adding a new tag value needs no code change.

- **CAP-8** *(FR-8 · AD-8)*
  - **intent:** A visitor clicks a topic chip and sees every post under that topic.
  - **success:** `/tags/{topic}/` lists all posts carrying the tag newest-first; an empty term page shows a light "check back" state, not a blank page.

- **CAP-9** *(FR-9 · AD-13)*
  - **intent:** A visitor finds posts by typing in header search across titles and content.
  - **success:** A focusable header control opens Hextra FlexSearch; results filter live and are keyboard-navigable; search is the single accepted JS-dependent feature and degrades gracefully with JS off.

- **CAP-10** *(FR-10 · AD-1, AD-2, AD-11)*
  - **intent:** Carrie publishes a post by adding a markdown file and pushing — no further manual step.
  - **success:** A push to the default branch triggers an Amplify build; a new non-draft post is reachable at its page and in the stream within a few minutes, with no CMS/login/web-editor/copy step anywhere in the path.

- **CAP-11** *(FR-11 · AD-2)*
  - **intent:** The site builds and serves from exactly one correctly-branded pipeline.
  - **success:** Served at `carriekroutil.com` via Route 53 with `baseURL`/`title` reflecting it; zero SheBytes / `code.shebytes.io` / ananke / quickstart remnants; no second deploy path carried into the repo.

- **CAP-12** *(FR-12 · UX spine + conventions)*
  - **intent:** Brand identity stays consistent and on-palette across the site.
  - **success:** The header renders the gradient wordmark linking Home with a solid `#d946ef` fallback (forced-colors/print); accent usage stays a garnish — no large gradient fill behind body text.

- **CAP-13** *(FR-13 · conventions)*
  - **intent:** A visitor switches between dark (default) and light themes and the choice sticks.
  - **success:** The site loads dark; a real focusable, labeled toggle switches to light; the choice persists across visits (Hextra-provided).

- **CAP-14** *(FR-14 · Deferred)*
  - **intent:** Once a tool is chosen, Carrie can see basic readership via a single privacy-friendly script include.
  - **success:** The tool is GoatCounter or Cloudflare Web Analytics (not Mixpanel); integration is one snippet with no cookie-consent banner and no PII. *Tool choice is deferred and carries no launch dependency (see Open Questions).*

- **CAP-15** *(FR-15 · AD-7, AD-6)*
  - **intent:** One designated post is featured on Home independent of recency so it stays the recruiter's entry point.
  - **success:** A `featured: true` post holds a single Home slot that does not drop off as newer posts publish; Home falls back cleanly to most-recent when none is flagged.

- **CAP-16** *(FR-16 · AD-2)*
  - **intent:** Carrie previews the rendered site locally before pushing.
  - **success:** `hugo server` renders the site with the new/edited post and live reload, showing both draft and non-draft posts in a browser before publishing.

- **CAP-17** *(FR-17 · AD-12)*
  - **intent:** Carrie is told when a publish build fails, so a broken push is never a silent no-op.
  - **success:** A failed Amplify build emails Carrie and keeps the prior good site live; a successful build that adds no new published post is distinguishable from a failure.

- **CAP-18** *(FR-18 · conventions)*
  - **intent:** Published posts are crawlable and unfurl cleanly when shared.
  - **success:** The site emits a sitemap and an indexing-allowed `robots.txt`; each post page emits a descriptive `<title>`, meta description, and OG/Twitter tags (reading `hero.src`/`hero.alt`); post URLs are stable, human-readable `/posts/{slug}/`.

## Constraints

- **The authoring loop is inviolable** *(NFR-4 · AD-1)* — publishing is exactly `write markdown → git push`. No CMS, login, web editor, or manual copy/upload step may ever enter the path; any feature that adds an authoring step is a regression, not progress.
- **Exactly one deploy pipeline** *(AD-2, AD-11)* — AWS Amplify only, building from source on the **default branch**, served at `carriekroutil.com` via Route 53. Branch/PR auto-previews are off; there is one environment (production); no S3-sync GitHub Action or GCP artifacts live in the repo.
- **Core reading works without JavaScript** *(NFR-1 · AD-13)* — Home, post, stream, about, and tag pages render as static HTML with JS disabled. Hextra FlexSearch is the single accepted JS-dependent exception. No web-font network load (system-font stacks only).
- **Static, no server backend** *(NFR-5)* — static files only; the git repo is the single source of truth; Carrie owns the domain, the content (plain markdown), and the pipeline — portable off Amplify, no platform lock-in.
- **Accessibility floor** *(NFR-2)* — the dark (default) theme meets WCAG AA per the UX accessibility review; structural a11y holds in both themes (one `<h1>` per page, logical heading order, landmark regions, a visible `2px #d946ef` focus ring, real `<a>`/`<button>` with labels, alt text enforced build-failing on every image, `prefers-reduced-motion` honored). Light mode ships untuned and is not yet AA-verified.
- **Privacy** *(NFR-3)* — no consent-requiring cookies, no PII; any analytics is a single privacy-friendly script include only.
- **Visual tokens are owned by the UX spine** (`DESIGN.md`) — palette, 920px content / 720px reading width, and the gradient-as-garnish rule are authoritative there; this contract does not restate them.
- **Canonical post front-matter contract** *(AD-3)* — every post carries YAML front matter: `title`; `date` (ISO, invalid/missing fails the build); `tags` (lowercase, open taxonomy); `draft`; optional `featured`; optional `hero` as `{ src, alt }` with `alt` required. Read-time is derived, never hand-entered.

## Non-goals

- No interactivity / UGC — no comments, accounts, reactions, or visitor-submitted content; read-only and single-author.
- No CMS or web editor — markdown-and-push *is* the product; an admin UI defeats the purpose.
- No newsletter / email capture and no heavy product analytics in v1.
- No resume/CV section — the professional signal is woven through the content, not a credential list.
- No docs/guides section in v1 (Hextra could add one later).
- No server backend; no carry-over of SheBytes branding or throwaway quickstart content.
- **Don't chase traffic** *(SM-C1)* — raw pageviews/visitor counts are not a success target and must not drive decisions.
- **Don't add friction** *(SM-C2)* — the number of features is not a goal; any addition that complicates the authoring loop is a regression, not progress.

## Success signal

Carrie writes a markdown file, runs `git push`, and the post is live on `carriekroutil.com` within a few minutes with zero added manual steps — and a year from now (ideally three) she is **still** doing it on a whim, at a loose cadence of ~1–2 posts/month *(SM-1, SM-2)*. The concrete launch proof: the BMAD build-in-public post is published, shareable, genuinely teaches, and Carrie is comfortable sending the link to both a friend and a recruiter *(SM-3)*.

## Open Questions

- **Analytics tool** (CAP-14 / FR-14) — GoatCounter vs. Cloudflare Web Analytics. Deferred, not a launch gate; pick at or shortly after launch. *(Owner: Carrie.)*
- **Light-mode AA** (NFR-2) — re-verify the three accent colors meet WCAG AA on Hextra's light surfaces before relying on light mode; dark is the AA-guaranteed v1 path.
- **Custom 404** — confirm the on-brand "wandered off the fairway" 404 is acceptable to defer past v1 (non-blocking).
- **Legacy deploy cleanup** — a manual one-time task in the old `~/code/quickstart` location (disable the S3-sync GitHub Action; remove GCP `app.yaml`/`main.go`/`.gcloudignore`) so the retired project stops auto-deploying. Outside this repo; FR-11 only requires they are not carried in. *(Owner: Carrie, manual.)*
