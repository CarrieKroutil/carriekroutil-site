# Project Hero Image — Generation Instructions

Reference for creating the four project hero images with an AI design tool
(e.g. Claude design). Matches the existing post heroes so projects look native.

---

## Specs (must match existing post heroes)

| Property | Value |
|---|---|
| **Dimensions** | **1200 × 600 px** (2:1). Export exactly this; if the tool emits another ratio, crop/resize to 1200×600. |
| **File type** | **PNG**, optimized to ≲ ~150 KB (existing heroes are ~40 KB). |
| **Save as** | `content/projects/<slug>/hero.png` (page-bundle resource, like posts). |
| **Crop-safe zone** | Cards crop to a short, wide banner via `object-fit: cover` (~120 px tall, taller in the featured slot). **Keep the subject centered**, nothing critical near the edges. |
| **No text** | Avoid embedded words/labels — they shrink and crop badly. |
| **No likenesses/logos** | No real-person likenesses, no copyrighted logos or brand marks. |

## Brand palette (use these hex values)

| Role | Hex |
|---|---|
| Base / background | `#0a0a0a` |
| Accent — violet | `#8b5cf6` |
| Accent — fuchsia | `#d946ef` |
| Accent — amber | `#f59e0b` |
| Light text tone | `#ededed` |

> Brand accent gradient (the wordmark): **`#8b5cf6` → `#d946ef` → `#f59e0b`**.
> Keep it rich and low-key against the `#0a0a0a` base — not bright white.

---

## Reusable prompt template

Swap only the final **Subject** line per project.

> Create a wide 2:1 hero banner illustration, 1200×600 px, PNG. Dark, editorial,
> slightly abstract style. Background `#0a0a0a`; lighting and accents follow the brand
> gradient `#8b5cf6` → `#d946ef` → `#f59e0b`. Keep the main subject centered with calm
> negative space around it (the image is cropped to a short, wide banner). No text, no
> logos, no real people. Subject: **{PER-PROJECT SUBJECT}**.

## Per-project subjects

1. **This site** (`this-site/hero.png`) — a stylized gradient-glass browser window with a
   blinking text cursor and a few abstract spec/markdown document cards floating behind it,
   suggesting a site built spec-first with an AI agent. Clean, minimal, hopeful.
2. **EM Handbook** (`engineering-manager-handbook/hero.png`) — an open guidebook glowing
   softly, with a constellation of connected nodes (a team/org graph) rising from its pages
   and a subtle compass motif. Calm, authoritative, mentoring.
3. **Lorelai** (`oy-with-the-poodles-lorelai/hero.png`) — a cozy late-night diner counter,
   a single steaming coffee cup beside a glowing chat bubble, warm amber light against the
   dark base. Witty and inviting. No people, no show branding.
4. **KareoTool** (`kareo-tool/hero.png`) — abstract data flowing from an API endpoint along
   a pipeline into a clean spreadsheet grid, faint gears/automation and a subtle
   healthcare-cross hint. Practical, technical, tidy.
5. **Kona Doodle** (`kona-doodle/hero.png`) — two playful, curly-haired doodle dogs rendered
   as a warm, hand-crafted illustration, framed inside a simple browser/webpage layout built
   from clean template blocks. Charming and hand-coded — the look of a site lovingly written
   by hand, before the AI era. No real logos.
6. **Unblur** (`unblur/hero.png`) — a single photo tile caught mid-reveal: heavily blurred on
   one side, snapping into crisp focus on the other, ringed by a few floating rounded guess /
   score bubbles with a hint of phones around it. Playful, energetic, multiplayer-party-game
   feel. No real people, no logos.

---

## Quick checklist before committing each image

- [ ] Exactly 1200 × 600 px, PNG
- [ ] Subject centered, edges safe to crop
- [ ] On-brand colors (`#0a0a0a` base; `#8b5cf6`/`#d946ef`/`#f59e0b` accents)
- [ ] No text, no logos, no real-person likeness
- [ ] File ≲ ~150 KB, saved to `content/projects/<slug>/hero.png`
