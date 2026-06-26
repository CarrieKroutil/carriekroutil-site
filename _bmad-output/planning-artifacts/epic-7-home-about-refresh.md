# Epic 7 — Home & About refresh (DRAFT)

**Date:** 2026-06-26
**Status:** Draft / discussion
**Why:** Home and About were designed in Epic 3 when the site was essentially a blog.
Two major pillars now exist — **Projects** (Epic 6) and the **Handbook** — but neither page
reflects them. Home is still 100% posts; About never mentions the Handbook. This epic brings
the pillars to the surface without bloating either page.

---

## Story 7.1 — Surface projects on Home ("Featured Project") ✅ approach agreed

**Decision (Carrie):** Repurpose the existing Home **"Featured"** slot to a **"Featured Project,"**
and leave **"Latest Posts"** exactly as is. Result: Home = Hero → **Featured Project** →
**Latest Posts** — both pillars visible, minimal new design.

**Current behavior (`layouts/index.html`):** the Featured slot pins the first `featured: true`
**post** (falling back to the most-recent post), excluded from the Latest grid below.

**Change:**
- Featured slot selects a **project** instead of a post, rendered with `project-card.html`.
- Section label/heading changes from "Featured" → **"Featured Project."**
- "Latest Posts" grid is untouched (posts-only; no cross-section exclusion needed).
- The previously-pinned post (`building-in-public`, `featured: true`) is no longer pinned on
  Home — it simply flows back into the Latest Posts grid. (Cleanup: leave or drop its
  `featured: true` flag — it becomes inert for Home either way.)

**Pick logic — DECIDED (b):** a **`featured: true` flag on one project** (fallback to
most-recent if none flagged), mirroring the post logic. ✅ **Built.** **Unblur** is flagged and
leads the slot ("Featured Project"); the Latest Posts grid no longer excludes anything (the
featured item is a project, not a post, so `building-in-public` flows back into the grid).

**Note:** the Handbook still isn't a first-class Home element unless it's the featured project.
If we want a permanent Handbook presence on Home later, that's a separate small addition (a
"From the Handbook" callout) — out of scope for 7.1.

---

## Story 7.2 — About refresh 🟡 direction set, details open

**Intent (Carrie):** About is the **personal "about me"** page — keep it warm. The professional
story is carried by the nav + Projects + (now) the Featured Project on Home. **The Handbook does
NOT belong on About.** (Confirms the earlier hypothesis — drop the professional-showcase push.)

**Changes — DECIDED:**
- **Merge "What I do" + "things I'm about" into ONE section.** This dissolves the tag-curation
  problem: framed as "the mix of what I do and what I'm into," the **full taxonomy makes sense**
  (code, ai, leadership, go, dogs, golf…) and grows organically with new content. Chips render
  **inside that section** via a new `topics` shortcode (so placement lives in the markdown, not
  the layout).
- **Keep "Say hi" — just reword.** The disliked part was *"find me where I actually post."*
  Replace with a warm, honest invite. **Add a YouTube button** (youtube.com/@carriekroutil)
  alongside GitHub + LinkedIn, with room to add more later.
- **Bio depth:** add a few concrete, memorable specifics in Carrie's voice — engineering leader,
  builds with AI for fun (this site / Unblur / the Lorelai GPT), lifelong *Gilmore Girls*
  comfort-watch, dogs run the schedule, always up for one more round of golf. Specific over
  comprehensive; posts carry the evolving depth.

**Layout mechanics:** new `layouts/shortcodes/topics.html` (renders chips); remove the chip line
from `about.html`; add a YouTube button to the socials block (consider a data-driven socials list
for easy future additions).

---

## Out of scope (for now)
- A styled `/tags/` topics index page — decided unnecessary (orphan page; term pages already styled).
- A full three-pillar Home redesign — 7.1's lighter "Featured Project" approach is preferred.

## Build/verify notes
- Reuses existing components (`project-card`, `.ck-featured`, `.ck-grid`) — minimal new CSS.
- Verify: `hugo --gc --minify` clean; Home shows one project card above the posts grid.
