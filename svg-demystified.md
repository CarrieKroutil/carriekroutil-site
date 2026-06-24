# SVG, demystified

A plain-English reference for understanding the hero images on this site, and a
copy-paste recipe for making new ones. Written for future-Carrie (or an AI helper)
to pick up cold.

---

## 1. What an SVG actually is

**SVG = Scalable Vector Graphics.** It's an image format, but fundamentally
different from a photo:

- A **photo** (`.jpg`, `.png`) is a **raster** image — a fixed grid of colored
  pixels. A 1200×600 JPG is literally 720,000 little color values. Zoom in far
  enough and you see the squares.
- An **SVG** is a **vector** image — not pixels at all, but a *text file of
  drawing instructions*: "draw a circle here, a gradient bar there, fill with
  this color." The browser reads the instructions and redraws the shapes fresh
  at whatever size it's shown.

Your `hero.svg` files are human-readable code. The art is **drawn**, not
**photographed**.

### Strengths (why you'd want SVG)

| Strength | What it means |
|---|---|
| **Infinitely sharp** | Same file is crisp on a phone, a 4K monitor, or zoomed in. A JPG blurs when scaled up. |
| **Tiny** | These heroes are ~1.5 KB of text. An equivalent photo could be 200 KB–2 MB. Faster loads (the site's NFR-1 "fast on mobile" goal). |
| **Editable in code** | Want the circle bigger or the gradient shifted? Change a number. No Photoshop. |
| **Perfect for geometry** | Logos, icons, diagrams, abstract brand art — anything made of shapes. (The wordmark, favicon, and footer rule are SVG too, and should stay that way.) |

### The one weakness (the gotcha that bit this site)

**Social-media link previews.** When you paste a link into Slack / LinkedIn / X,
their servers fetch the page and read its `og:image` meta tag to build the little
preview card. Those scrapers were built for **photos** (`.jpg`/`.png`/`.webp`) and
most of them **refuse to render an SVG** — partly habit, partly security (SVG is
code, and code can misbehave, so they distrust it).

> **Rule of thumb:** SVG is excellent for *displaying on the site*, but unreliable
> as a *share image*. Anything that needs to unfurl on social media wants to be a
> raster (`.jpg`/`.png`).

### Which is which — raster vs. vector at a glance

The format is determined by the **file extension**, and that's exactly what a
social scraper checks first:

| Extension | Type | Made of | Social scrapers accept it? |
|---|---|---|---|
| `.jpg` / `.jpeg` | **Raster** | pixels | ✅ Yes — the default for photos |
| `.png` | **Raster** | pixels | ✅ Yes |
| `.webp` | **Raster** | pixels | ✅ Usually |
| `.gif` | **Raster** | pixels | ✅ Yes |
| **`.svg`** | **Vector** | drawing instructions (code) | ❌ **Usually rejected** |

This site's own files, sorted:

- **Raster (share-safe):** `static/carrie-avatar.jpg` (your photo, also the
  default share image), the favicons / `apple-touch-icon.png`.
- **Vector (great on-site, *not* share-safe):** every post `hero.svg`, the
  gradient wordmark, the favicon `.svg`, the footer rule.

### Why social-sharing sites insist on raster

When you paste a link, the platform (Slack, LinkedIn, X, Facebook, iMessage, …)
runs a little robot — a **scraper** — that downloads your page's HTML, finds the
`<meta property="og:image" content="…">` tag, fetches that image, and bakes it
into the preview card. Two reasons that robot wants a raster:

1. **Predictability.** A raster is a finished, fixed grid of pixels — the scraper
   knows its exact dimensions and can crop/resize it into the card with no
   surprises. An SVG has no inherent pixel size; it's instructions that *could*
   render differently (or hugely) depending on the renderer. Card layouts can't
   gamble on that.
2. **Security.** An SVG is **code**, not just data. It can embed scripts, external
   references, and other active content, which has been used for attacks. Photos
   can't *do* anything — they're inert pixels. So the big platforms simply
   **refuse to render SVG** for previews rather than risk executing someone's
   markup on their servers.

Net effect: an SVG `og:image` typically yields a preview card with **no image at
all** — even though the very same SVG looks perfect *on your page*. That mismatch
("looks great on the site, blank when shared") is the whole reason post heroes
need a raster version for sharing.

---

## 2. Why this site has them

When the site was built with the BMAD / AI workflow, there were no real photos for
post heroes — so the build generated **abstract on-brand placeholder art** as SVG:
gradient bars and soft circles in the exact brand palette. SVG was the natural pick
because the art is pure geometry. Think of them as code-drawn brand wallpaper, one
themed per post.

---

## 3. Anatomy of a hero: the BMAD post

File: `content/posts/building-in-public-with-bmad/hero.svg`

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1200 600" role="img" aria-label="">
  <defs>
    <linearGradient id="bg" x1="0" y1="0" x2="1" y2="1">
      <stop offset="0" stop-color="#241a44"/>
      <stop offset="0.55" stop-color="#3a1d3f"/>
      <stop offset="1" stop-color="#3d2a12"/>
    </linearGradient>
    <linearGradient id="accent" x1="0" y1="0" x2="1" y2="0">
      <stop offset="0" stop-color="#8b5cf6"/>
      <stop offset="0.5" stop-color="#d946ef"/>
      <stop offset="1" stop-color="#f59e0b"/>
    </linearGradient>
  </defs>
  <rect width="1200" height="600" fill="url(#bg)"/>
  <!-- blueprint grid: spec-first, drawn before the build -->
  <g stroke="#d946ef" stroke-width="1" opacity="0.12">
    <path d="M150 0V600 M300 0V600 M450 0V600 M600 0V600 M750 0V600 M900 0V600 M1050 0V600"/>
    <path d="M0 150H1200 M0 300H1200 M0 450H1200"/>
  </g>
  <!-- stacked plan → build bars -->
  <g opacity="0.9">
    <rect x="170" y="250" width="300" height="22" rx="11" fill="url(#accent)" opacity="0.85"/>
    <rect x="170" y="296" width="220" height="22" rx="11" fill="#d946ef" opacity="0.5"/>
    <rect x="170" y="342" width="260" height="22" rx="11" fill="#8b5cf6" opacity="0.5"/>
  </g>
  <circle cx="960" cy="170" r="90" fill="#d946ef" opacity="0.18"/>
  <circle cx="1010" cy="430" r="120" fill="#f59e0b" opacity="0.12"/>
</svg>
```

Read top to bottom — the painter's algorithm, later shapes draw over earlier ones:

| Code | What it draws |
|---|---|
| `viewBox="0 0 1200 600"` | The canvas: 1200 wide × 600 tall (the coordinate system everything else uses). |
| `<defs>` … two `<linearGradient>` | Reusable color recipes — *defined here, not drawn yet*. `bg` = dark plum→brown; `accent` = brand violet→fuchsia→amber. |
| `<rect ... fill="url(#bg)">` | A full-canvas rectangle painted with the `bg` gradient → the background. |
| `<g stroke="#d946ef" opacity="0.12">` + `<path d="M150 0V600 …">` | Faint fuchsia vertical + horizontal lines → the "blueprint grid". |
| three `<rect ... rx="11">` | Rounded bars (one gradient, two dim) → the "plan → build" bars. |
| two `<circle>` | Soft fuchsia + amber glows, upper- and lower-right. |

The art is a **metaphor** for the post: a blueprint grid and "plan → build" bars
for a post about spec-first building.

### Path syntax, briefly
`<path d="…">` is a pen. The letters are commands using the canvas coordinates:
- `M150 0` = **M**ove the pen to (150, 0) without drawing.
- `V600` = draw a **V**ertical line to y=600. `H1200` = **H**orizontal line to x=1200.
- `L304 294` = draw a **L**ine to (304, 294). `Z` = close the shape back to its start.
- `Q`/`T` = **Q**uadratic curves (used in the golf hero below).

---

## 4. A second example: the golf post (same palette, different shapes)

File: `content/posts/first-tee/hero.svg`

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1200 600" role="img" aria-label="">
  <defs>
    <linearGradient id="g" x1="0" y1="0" x2="1" y2="1">
      <stop offset="0"   stop-color="#2a1d4a"/>
      <stop offset="0.5" stop-color="#3a2640"/>
      <stop offset="1"   stop-color="#3d3312"/>
    </linearGradient>
    <linearGradient id="accent" x1="0" y1="0" x2="1" y2="0">
      <stop offset="0"   stop-color="#8b5cf6"/>
      <stop offset="0.5" stop-color="#d946ef"/>
      <stop offset="1"   stop-color="#f59e0b"/>
    </linearGradient>
  </defs>
  <rect width="1200" height="600" fill="url(#g)"/>
  <!-- rolling fairway -->
  <path d="M0 470 Q 400 400 720 450 T 1200 430 L1200 600 L0 600 Z" fill="#8b5cf6" opacity="0.10"/>
  <path d="M0 505 Q 500 455 900 495 T 1200 485 L1200 600 L0 600 Z" fill="#f59e0b" opacity="0.10"/>
  <!-- horizon -->
  <rect x="0" y="300" width="1200" height="14" fill="url(#accent)" opacity="0.8"/>
  <!-- the flag on the green -->
  <rect x="300" y="250" width="4" height="205" rx="2" fill="#ededed" opacity="0.55"/>
  <path d="M304 256 L372 275 L304 294 Z" fill="#d946ef" opacity="0.9"/>
  <!-- low sun + soft accent -->
  <circle cx="950" cy="185" r="80"  fill="#f59e0b" opacity="0.18"/>
  <circle cx="180" cy="430" r="100" fill="#8b5cf6" opacity="0.12"/>
</svg>
```

**What's the same:** identical canvas (1200×600), the identical `accent` gradient
(violet→fuchsia→amber), the same "dark gradient background + faint accent circles"
bones.

**What's different — the topic metaphor:**
- The blueprint grid is gone. Instead, two `<path>` curves using `Q`/`T`
  (quadratic Bézier) draw **rolling fairway hills** along the bottom.
- A thin full-width `<rect>` with the accent gradient = the **horizon**.
- A skinny near-white `<rect>` (the flagpole) + a `<path>` triangle (`L … L … Z`)
  in fuchsia = **the flag on the green**.
- A low amber `<circle>` = the **setting sun**.

Same brand DNA, shapes chosen to *say golf*. That's the pattern for every hero:
**fixed palette + canvas, topic-specific shapes.**

---

## 5. Recipe: create a brand-new on-brand hero SVG

Use this as a checklist, or paste section 5 + 5.1 to an AI as a prompt.

### The fixed ingredients (don't change these — they're the brand)

- **Canvas:** `viewBox="0 0 1200 600"` (also the ideal social-share aspect).
- **Accent gradient** (violet → fuchsia → amber), reuse verbatim:
  ```svg
  <linearGradient id="accent" x1="0" y1="0" x2="1" y2="0">
    <stop offset="0"   stop-color="#8b5cf6"/>
    <stop offset="0.5" stop-color="#d946ef"/>
    <stop offset="1"   stop-color="#f59e0b"/>
  </linearGradient>
  ```
- **Palette tokens:** violet `#8b5cf6`, fuchsia `#d946ef`, amber `#f59e0b`,
  near-white `#ededed`. Background gradient = dark, desaturated mixes of those
  hues (e.g. `#241a44` → `#3a1d3f` → `#3d2a12`).
- **Keep accents low-opacity** (circles `0.10–0.20`, grids `~0.12`) so the art
  stays a quiet backdrop, never shouty. Bars/foreground shapes can go higher.

### The recipe

1. **Pick a metaphor for the post's topic.** Dogs → bones / paw arcs; a talk →
   speech bubbles / soundwave bars; a launch → an upward arc. Keep it abstract,
   2–4 shape groups max.
2. **Start from the template** in 5.1 below.
3. **Background:** full-canvas `<rect>` filled with a dark gradient (`url(#bg)`).
4. **One quiet texture layer:** a faint grid, curves, or dots in `#d946ef` /
   accent at `opacity ~0.10`.
5. **One or two "subject" shapes** that carry the metaphor — these may use the
   `accent` gradient and higher opacity so they read first.
6. **One or two soft glow `<circle>`s** in single accent colors at `0.12–0.18`
   to balance the corners.
7. **Save it** as `hero.svg` **inside the post's bundle folder**
   (`content/posts/<slug>/hero.svg`), next to that post's `index.md`.
8. **Wire it up** in the post front matter (alt text is **required** — the build
   fails without it):
   ```yaml
   hero:
     src: hero.svg
     alt: A short, literal description of the image for screen readers.
   ```
9. **Generate a `hero.png` sibling for social sharing** (see §6). The site keeps
   showing the crisp `hero.svg` on the page but automatically uses `hero.png` for
   link previews. From the post folder:
   ```bash
   rsvg-convert -w 1200 -h 600 hero.svg -o hero.png
   ```
   Commit **both** files. (Skip this only if you don't care about the share-card
   image — without a PNG sibling, shared links fall back to the site avatar.)
10. **Preview:** run `hugo server` and open the post.

### 5.1 Copy-paste starter template

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1200 600" role="img" aria-label="">
  <defs>
    <!-- Dark background gradient — tweak these 3 stops per post mood -->
    <linearGradient id="bg" x1="0" y1="0" x2="1" y2="1">
      <stop offset="0"    stop-color="#241a44"/>
      <stop offset="0.55" stop-color="#3a1d3f"/>
      <stop offset="1"    stop-color="#3d2a12"/>
    </linearGradient>
    <!-- Brand accent gradient — DO NOT change -->
    <linearGradient id="accent" x1="0" y1="0" x2="1" y2="0">
      <stop offset="0"   stop-color="#8b5cf6"/>
      <stop offset="0.5" stop-color="#d946ef"/>
      <stop offset="1"   stop-color="#f59e0b"/>
    </linearGradient>
  </defs>

  <!-- 1. background -->
  <rect width="1200" height="600" fill="url(#bg)"/>

  <!-- 2. quiet texture layer (replace with your own faint shapes) -->
  <g stroke="#d946ef" stroke-width="1" opacity="0.12" fill="none">
    <path d="M150 0V600 M450 0V600 M750 0V600 M1050 0V600"/>
  </g>

  <!-- 3. subject shapes — your topic metaphor goes here -->
  <rect x="170" y="270" width="300" height="22" rx="11" fill="url(#accent)" opacity="0.85"/>

  <!-- 4. soft corner glows -->
  <circle cx="960" cy="170"  r="90"  fill="#d946ef" opacity="0.18"/>
  <circle cx="1010" cy="430" r="120" fill="#f59e0b" opacity="0.12"/>
</svg>
```

---

## 6. The PNG sibling (how sharing works on this site)

Because scrapers don't render SVG (§1), an SVG hero needs a raster companion for
link previews. **This site handles that automatically** — you keep the crisp SVG
on the page *and* get a working share image, with no front-matter juggling.

### How it's wired

`layouts/_partials/custom/social-image.html` resolves the share image (`og:image` /
`twitter:image`) like this:

1. Look at the post's `hero.src`.
2. **If it's an `.svg`**, look for a raster sibling in the same bundle —
   `hero.png` first, then `hero.jpg` — and use *that* for the share meta.
3. If no raster sibling exists, leave it empty → the site falls back to the
   **avatar** (`carrie-avatar.jpg`) as a generic share image.

Meanwhile `layouts/posts/single.html` renders the on-page `<img>` straight from
`hero.src` (the SVG), untouched. So the two jobs are decoupled: **vector on the
page, raster in the share card.** You never change `hero.src` — leave it as
`hero.svg`.

### What you do: generate the PNG

This repo has `rsvg-convert` available (install with `brew install librsvg` on a
new machine). From the post's bundle folder:

```bash
rsvg-convert -w 1200 -h 600 hero.svg -o hero.png
```

- `-w 1200 -h 600` matches the SVG's native size → no stretching. (1200×600 works
  for both `og:image` and Twitter `summary_large_image`; the "textbook" share size
  is 1200×630, also fine.)
- `hero.png` lands next to `hero.svg`. **Commit both.**

> **Maintenance:** the PNG is a *snapshot* of the SVG, so whenever you edit a
> `hero.svg`, **re-run the command** to refresh its `hero.png` — otherwise the
> share card shows the old art. (No PNG yet = share card shows the avatar, never
> broken.)

**No `rsvg-convert`?** Any of these also produce the PNG: open the SVG in a browser
and screenshot, use a design tool (Figma/Illustrator → export PNG at 1200×600), or
a reputable online SVG→PNG converter. Just drop the result in as `hero.png`.

### The all-raster alternative (future-you, maybe)

If you ever switch a post to a **real photo**, there's no SVG and no sibling to
maintain — just set `hero.src: hero.jpg` (or `.png`). One raster file then does
both jobs (on-page *and* sharing), and the SVG machinery above simply doesn't apply
to that post. You can mix freely: some posts SVG-art + PNG sibling, others a single
photo.

---

## Quick reference

| I want to… | Do this |
|---|---|
| Understand a hero | Open `content/posts/<slug>/hero.svg` — it's readable text. |
| Make a new hero | Copy the template in §5.1, swap the subject shapes, save as `hero.svg` in the post bundle, add `hero: {src, alt}` to front matter, then generate its `hero.png` sibling (§6). |
| Keep brand consistency | Never change the `accent` gradient or the 1200×600 canvas; keep accents low-opacity. |
| Make a post link unfurl with an image | Generate a `hero.png` sibling next to `hero.svg` (§6) — the site auto-uses it for sharing. Leave `hero.src` as the SVG. |
| After editing an SVG hero | Re-run `rsvg-convert -w 1200 -h 600 hero.svg -o hero.png` to refresh the share image. |
| Use a real photo instead | Set `hero.src: hero.jpg` — one raster file does on-page + sharing, no sibling needed. |
| Keep SVG for logos/icons/wordmark | Leave them — SVG is the right tool there. |
