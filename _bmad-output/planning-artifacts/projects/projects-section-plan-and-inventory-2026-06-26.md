# Projects Section — Plan & Content Inventory

**Date:** 2026-06-26
**Status:** ✅ **Scaffolded & building** (Epic 6 first cut) — section, layouts, all five project pages, and nav entry are in place and `hugo` builds clean. Pending: real hero images, Lorelai screenshots + video id, content review.
**Owner:** Carrie Kroutil
**Relates to:** [PRD: carriekroutil.com](../prds/prd-carriekroutil-site-2026-06-22/prd.md) §4 (Features). This is the natural **Epic 6 — Projects**, a feature within the existing product PRD rather than a new product.

---

## 0. Why this doc (not a new PRD)

The site already has a product-level PRD. The Projects section is one feature within it,
so the valuable thing to capture here is **(a) the locked decisions + build plan** and
**(b) a content inventory** of the five projects — their stories, links, and the assets
still missing — so nothing gets lost between now and build. This doc is that capture.

---

## 1. Decisions locked (from discussion 2026-06-26)

| # | Decision | Rationale |
|---|----------|-----------|
| D1 | Projects get **on-site detail pages**, not just link-outs (card grid **+** detail pages). | Real portfolio content Carrie owns; SEO + on-site search pick it up. |
| D2 | Projects become a **real content section** (`content/projects/`), mirroring `posts/`. | Reuses the proven post bundle + shared card pattern (AR-9/AD-6). |
| D3 | The **EM Handbook is its own project**, separate from "This site". | Distinct effort with its own public repo (`eng-mgr-toolkit`), README, 53 topics/12 sections, and BMAD paper trail. Merging would undersell both. |
| D4 | Video is served via **unlisted YouTube/Vimeo embed**, click-to-play (poster → iframe). | Keeps the repo lean (no git-LFS here; heroes are ~40KB), gives streaming + captions, stays 100% static. |
| D5 | Grid sorts **reverse-chronological** by `date`, like the post stream. | Consistent with `posts/list.html`. |

---

## 2. Build plan

### 2.1 Structure
```
content/projects/
  _index.md                              # section config + intro (replaces "coming soon")
  this-site/index.md                     # June 2026
  engineering-manager-handbook/index.md  # June 2026
  oy-with-the-poodles-lorelai/index.md   # January 2025
  kareo-tool/index.md                    # April 2021
  kona-doodle/index.md                   # November 2018 (orig 2014)
layouts/projects/
  list.html       # card grid — reuses the custom/post-card pattern
  single.html     # detail page — posts/single.html + action buttons (Live / GitHub)
```
- Retire `layouts/_default/projects.html` ("coming soon") and route the section through the new layouts.
- Cards render an optional `links` list as buttons; detail pages render the same buttons + hero + body.

### 2.2 Front-matter schema (per project)
```yaml
---
title: "…"
date: 2026-06-01            # drives grid order + displayed date
summary: "One-sentence what-it-is (card blurb + meta description)."
tags: ["AI", "Hugo"]        # slots into the existing /tags/ system
hero:                       # card thumb + detail hero; gradient fallback if omitted
  src: "hero.png"           # bundle file or external URL (same resolution as posts)
  alt: "…"                  # REQUIRED when hero present (matches posts AD-9)
links:                      # rendered as buttons on card + detail page
  - { label: "Live", url: "https://…" }
  - { label: "GitHub", url: "https://…" }
video:                      # optional — Lorelai only for now
  provider: "youtube"       # youtube | vimeo
  id: "…"                   # unlisted video id; click-to-play lazy embed
---
```

### 2.3 Reuse / consistency
- Card renderer: reuse `custom/post-card.html` (or a thin `project-card` variant that adds the `links` buttons). Tags, meta, and gradient fallback come for free.
- Detail layout: clone `posts/single.html` (centered reading column, back-link → `/projects/`, tags → meta → hero → body), add a button row for `links` and the lazy video embed partial.

---

## 3. Content inventory

> Newest-first (grid order). "Story source" = where the copy is drafted from.
> **Assets needed** = what Carrie still has to provide before that project is publishable.

### 3.1 This site — carriekroutil.com
- **Date:** June 2026
- **One-liner:** A personal site + blog, built spec-first with the BMAD method and Claude Code, on Hugo + Hextra, shipped via AWS Amplify.
- **Story source:** This repo + project memory — Hugo/Hextra custom theme, BMAD spec-first workflow, Claude Code as the coding agent, Amplify CI/CD, the post stream, the EM Handbook, the custom design system (gradient wordmark, dark-default theme, tokens).
- **Links:** Live → `https://carriekroutil.com` · GitHub → `https://github.com/CarrieKroutil/carriekroutil-site` *(now public; branch protection applied to `main`)*
- **Tags (proposed):** `Hugo`, `BMAD`, `AI`
- **Assets needed:** hero image (1200×600 PNG — see Appendix A).

### 3.2 Engineering Manager Handbook
- **Date:** June 2026
- **One-liner:** A free, searchable Engineering Manager Handbook — 53 topics across 12 sections — planned and built in public with the BMAD method.
- **Story source:** [`github.com/CarrieKroutil/eng-mgr-toolkit`](https://github.com/CarrieKroutil/eng-mgr-toolkit) README — spec-first BMAD ("a spec-first, role-based way of working with an AI coding agent"), full paper trail (brief, PRD, UX, architecture, epics/stories, retros), 23 topics migrated from a prior wiki + 30 new stubs expanded, guided navigation, full-text search, dark mode, WCAG 2.1 AA. Built on Hugo + Hextra inside this same repo, no extra infra.
- **Links:** Live → `/handbook/` · GitHub → `https://github.com/CarrieKroutil/eng-mgr-toolkit`
- **Tags (proposed):** `BMAD`, `Hugo`, `leadership`
- **Assets needed:** hero image (1200×600 PNG — see Appendix A).

### 3.3 "Oy with the poodles already — Lorelai"
- **Date:** January 2025
- **One-liner:** A custom GPT channeling Gilmore Girls' Lorelai — and the AI-generated "lost episode" video it wrote — born from exploring HeyGen's video-localization features.
- **Story source:** Carrie's narrative. Arc in two parts:
  - **Part 1 — the custom GPT.** Work brought Carrie to HeyGen.com to translate videos into other languages (see cross-link below). To explore HeyGen herself she needed a script, so she leaned into a long-running obsession — *The Gilmore Girls* (a years-long bedtime re-watch) — and built her first public custom GPT. It can share a witty reference with its origin, coin a Lorelai-inspired quip, or rephrase your text in Lorelai's witty tone.
  - **Part 2 — the AI episode.** She used the GPT to write a brand-new episode, then turned that script into an AI-generated video. Subtext for the video: *"The missing scene: 'The Lorelai Manifesto' (Set in 2012, in the familiar comfort of Luke's Diner. Lorelai sits at the counter, sipping coffee while Luke tidies up. The diner is quiet, with the occasional clatter of dishes. Rory is absent, busy on an assignment in Washington, D.C. Lorelai leans back and launches into a monologue.)"*
- **Links:**
  - Try the GPT → `https://chatgpt.com/g/g-6780c0992f5881918de1503fd75aee96-oy-with-the-poodles-already-lorelai`
  - HeyGen → `https://heygen.com` — **confirm: keep the mention/link?**
  - Cross-link (internal) → `/handbook/engineering-foundations/video-localization/` *(page exists)*
- **Video:** unlisted YouTube/Vimeo embed — **URL TBD**
- **Tags (proposed):** `AI`, `GPT`, `video`
- **Assets needed:** **two portrait GPT screenshots** (Part 1); **unlisted video URL** (Part 2); choose which image is the card hero; confirm HeyGen mention.

### 3.4 KareoTool
- **Date:** April 2021
- **One-liner:** A C#/.NET tool that pulls data from Kareo's SOAP API into Excel to streamline a nonprofit's accounting — and the lesson that the best solution was the one I didn't build.
- **Story source:** [`github.com/CarrieKroutil/KareoTool`](https://github.com/CarrieKroutil/KareoTool) README (branch: `master`). Volunteered through **Catchafire** on a *Technical Specification Development* project for **The Women's Center of Southeastern Michigan**, **$7,802 total impact**. Goal: optimize the accounting workflow (Kareo practice-management data → QuickBooks/Excel) to support growth with least effort/cost. Kareo's old-school SOAP API can't be hit directly from Excel Power Query, so Carrie built a custom C#/.NET exporter (configured via `App.config`, outputs Excel per endpoint). **The honest arc:** mid-build she discovered Kareo's **Custom Reports + Power Query** path — a no-code "preferred solution" that beats the tool she'd built — and documented the lessons learned (incl. Power Query and the M language) to help others.
- **Links:**
  - GitHub → `https://github.com/CarrieKroutil/KareoTool`
  - Catchafire impact → `https://www.catchafire.org/impact/match/2614761/the-women-s-center-of-southeastern-michigan--technical-specification-development/`
  - YouTube video → `https://youtu.be/dzZETwtgd-c` (channel: `https://www.youtube.com/@carriekroutil`)
- **Tags (proposed):** `C#`, `.NET`, `API`, `nonprofit`
- **Video:** `youtu.be/dzZETwtgd-c` **embeds on the page** (click-to-play, like Lorelai) — no leaving the site.
- **Assets needed:** hero image (1200×600 PNG — see Appendix A).

### 3.5 Kona Doodle
- **Date:** November 2018 *(Go rewrite; repo created 2018-11-13. Originally built 2014 in C#/ASPX.)*
- **One-liner:** A site for my doodle dogs — first built in 2014 (C#/ASPX), rewritten in 2018 as a hand-coded Go-templates + Bootstrap site on GCP App Engine. No AI, just good clean living.
- **Story source:** [`github.com/KonaCoder/GoKonaDoodle`](https://github.com/KonaCoder/GoKonaDoodle) README + Carrie's notes. "Using my doggos to share a golang site using templates and bootstrap." Hosted on **Google Cloud Platform App Engine**, deployed via the **gcloud CLI**. The charm/angle: **written by hand before the AI era** — a craftsmanship counterpoint to the AI-built projects above. Companion blog series: *How to Host a Free Site* (`shebytes.io/tutorials/host-free-site-intro`).
- **Links:** Live → `https://kona.shebytes.io` · GitHub → `https://github.com/KonaCoder/GoKonaDoodle`
- **Tags (proposed):** `Go`, `templates`, `GCP`
- **Assets needed:** hero image (1200×600 PNG — see Appendix A, subject #5).

---

## 4. Consolidated assets-still-needed checklist

| Project | Repo URL | Date | Hero image | Video / screenshots | Other |
|---|---|---|---|---|---|
| This site | ✅ public `carriekroutil-site` | ✅ June 2026 | ⏳ create (1200×600) | — | — |
| EM Handbook | ✅ `eng-mgr-toolkit` | ✅ June 2026 | ✅ dropped | — | — |
| Lorelai | n/a (GPT link) | ✅ Jan 2025 | ✅ dropped | ✅ `dzZETwtgd-c` embeds here; ⏳ **2 GPT screenshots** | confirm HeyGen mention |
| KareoTool | ✅ `KareoTool` | ✅ April 2021 | ⏳ create (1200×600) | — (video moved to Lorelai) | — |
| Kona Doodle | ✅ `GoKonaDoodle` | ✅ Nov 2018 (orig 2014) | ⏳ create (1200×600) | — | — |

All five heroes are **net-new images to create** (no gradient fallback) — see Appendix A for specs + prompts.

---

## 5. Open questions
- ✅ ~~This-site repo public + URL~~ → public; GitHub button on.
- ✅ ~~Handbook date~~ → June 2026.
- ✅ ~~Hero images: real vs gradient~~ → all four get **real, created** images (Appendix A).
- ✅ ~~KareoTool video: embed or link~~ → **embed** on the page.
- ✅ ~~Card variant~~ → built a dedicated `project-card.html` (whole card clickable to the detail page; outbound buttons live on the detail page only).
- ✅ ~~Tags~~ → **unified taxonomy** (revised 2026-06-26 per Carrie). Projects use real `tags:` (same taxonomy as posts) via the shared `post-tags.html` renderer, so chips are genuinely clickable. Term pages (`_default/term.html`) now show **two sections — Posts and Projects** — each rendered only when non-empty. The earlier display-only `stack:` chips are removed. Approve/adjust the `tags` per page if you like.
- ◻ **Content review:** the four sourced drafts (this site, handbook, Kona Doodle, KareoTool) are first drafts — read for voice/accuracy.
- ◻ **Video style:** v1 is a native lazy-loaded responsive iframe (plays in place). A poster-thumbnail click-to-play swap is a possible later enhancement.
- ✅ ~~KareoTool video~~ → confirmed (Carrie 2026-06-26): **KareoTool has no video.** The `dzZETwtgd-c` video lives on the Lorelai/custom-GPT project.
- ✅ ~~Term-page regression~~ → fixed: section headings ("Posts"/"Projects") render **only when a tag spans both** collections; single-collection topic pages render exactly as before (no redundant heading).

---

## Appendix A — Hero image spec & generation prompts

> **Standalone reference:** [`hero-image-instructions.md`](./hero-image-instructions.md) (same content, easy to hand to the design tool). Summary below.

### Specs (match the existing post heroes)
- **Dimensions:** **1200 × 600 px** (2:1). Export at exactly this size; if the tool emits another ratio, crop/resize to 1200×600.
- **File type:** **PNG**. Target file size ≲ ~150 KB (existing heroes are ~40 KB); optimize before commit.
- **Filename / location:** `content/projects/<slug>/hero.png` (page-bundle resource, like posts).
- **Crop-safe zone:** cards crop the image to a short, wide banner via `object-fit: cover` (≈120 px tall, taller in the featured slot). **Keep the key subject centered**; don't put critical detail near the edges, and avoid embedded text (it shrinks/crops badly).
- **Brand palette (use these hexes):** base `#0a0a0a`; accent gradient `#8b5cf6` → `#d946ef` → `#f59e0b` (the wordmark — violet → fuchsia → amber); light text tone `#ededed`. Rich, low-key, not bright white.
- **Style:** cohesive across all four — modern, slightly abstract editorial illustration / gradient-mesh, consistent lighting. **No real-person likenesses, no copyrighted logos or brand marks** (esp. Lorelai → no actor likeness; KareoTool → no real Excel/Kareo logos).

### Reusable prompt template
> Create a wide 2:1 hero banner illustration, 1200×600 px, PNG. Dark, editorial, slightly abstract style. Background `#0a0a0a`; lighting and accents follow the brand gradient `#8b5cf6` → `#d946ef` → `#f59e0b`. Keep the main subject centered with calm negative space around it (the image is cropped to a short, wide banner). No text, no logos, no real people. Subject: **{PER-PROJECT SUBJECT}**.

### Per-project subjects (drop into `{…}` above)
1. **This site:** a stylized browser window made of soft gradient glass, a blinking text cursor and a few abstract spec/markdown document cards floating behind it, suggesting a site built spec-first with an AI agent — clean, minimal, hopeful.
2. **EM Handbook:** an open guidebook glowing softly, with a constellation of connected nodes (a team/org graph) rising from its pages and a subtle compass motif — calm, authoritative, mentoring.
3. **Lorelai (GPT):** a cozy late-night diner counter scene, a single steaming coffee cup beside a glowing chat-bubble, warm amber light against the dark palette — witty and inviting, no people, no real-show branding.
4. **KareoTool:** abstract data flowing from an API endpoint icon along a pipeline into a clean spreadsheet grid, faint gears/automation and a subtle healthcare-cross hint — practical, technical, tidy.

---

## Appendix B — Lorelai GPT screenshots (Part 1)

Two portrait screenshots of the custom GPT, shown side-by-side in the body. These are UI
screenshots, so the spec differs from the heroes.

- **Format:** **PNG** (crisp UI text). JPG only if a shot is photographic.
- **Orientation:** **portrait**, and give **both the same aspect ratio / height** so they pair
  cleanly side-by-side.
- **Source width:** **~800–1000 px wide each.** They display ~350 px wide in a two-up inside the
  720 px reading column, so ~800–1000 px covers 2× retina. Height follows the portrait crop.
- **File size:** optimize each to ≲ ~400 KB.
- **Privacy:** crop out anything personal (account email, unrelated chats).
- **Save as:** `content/projects/oy-with-the-poodles-lorelai/gpt-1.png` and `…/gpt-2.png`.
- **Layout:** they'll render side-by-side (stacking on mobile); the two-up figure + CSS gets
  wired when the files land. Markdown placeholders are already in the project body.

---

## 6. Sources
- KareoTool README — `github.com/CarrieKroutil/KareoTool` (branch `master`), incl. embedded Catchafire impact ($7,802, The Women's Center of Southeastern Michigan).
- eng-mgr-toolkit README — `github.com/CarrieKroutil/eng-mgr-toolkit`.
- Lorelai GPT — `chatgpt.com/g/g-6780c0992f5881918de1503fd75aee96-…`; HeyGen origin; internal handbook page `/handbook/engineering-foundations/video-localization/`.
- This repo — Hugo/Hextra, BMAD planning artifacts under `_bmad-output/`, project memory.
