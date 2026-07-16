# carriekroutil.com

Personal site — a Hugo static site using the [Hextra](https://github.com/imfing/hextra) theme, consumed via Hugo Modules. Authored in Markdown; published by `git push` to `main`, which AWS Amplify builds and serves at carriekroutil.com. For the deploy pipeline and one-time AWS setup, see **[SETUP.md](SETUP.md)**.

## Prerequisites

- **Hugo (extended edition)** ≥ 0.146.0 — the floor Hextra v0.12.3 requires. Check with `hugo version` (look for `+extended`).
- **Go** ≥ 1.21 — required by Hugo Modules to fetch the theme.

On macOS: `brew install hugo go`.

## Run locally

From the repo root:

```bash
# First time (or after a fresh clone / pull): fetch the Hextra theme module
hugo mod get

# Local preview WITH drafts + live reload — your day-to-day authoring loop
hugo server -D                # → http://localhost:1313/

# Preview the way production sees it (drafts hidden)
hugo server

# Full production build into ./public/
hugo --gc --minify
```

- `hugo server -D` shows `draft: true` content and hot-reloads on every save. Stop it with **Ctrl+C**.
- Plain `hugo` (no `-D`) excludes drafts — the same behavior the production build uses.
- `./public/` and `./resources/` are build output and are git-ignored.

## Writing a post

Each post is a **page bundle**: a folder under `content/posts/` with an `index.md` inside.
The folder name becomes the URL slug (`content/posts/my-post/` → `/posts/my-post/`), and any
images live in that same folder, beside the markdown.

```text
content/posts/my-post/
├── index.md        # the post: front matter + markdown body
├── hero.jpg        # optional header image (see below)
└── trail-photo.jpg # optional inline image
```

### Front matter

```yaml
---
title: "Your post title"
date: 2026-06-22T08:30:00-07:00   # ISO 8601 — REQUIRED; a missing/invalid date fails the build.
                                  # A future date withholds the post until then (no silent drop).
tags: [dogs, trips]               # lowercase; add ANY value — see "Tags" below
draft: false                      # true = local-only (hidden from the production build)
description: "One-line summary."  # optional; used as the card excerpt + meta/share description.
                                  #   Omit it and the post's first paragraph is used instead.
featured: false                   # optional; true pins it to the single Home featured slot
                                  #   (newest featured post wins; falls back to most-recent)
hero:                             # optional header image (omit for the gradient-thumb fallback)
  src: hero.jpg
  alt: "Describe the image"       # REQUIRED whenever hero is present
---

Your markdown body starts here.
```

Read-time (`"{n} min read"`) is derived automatically — never hand-enter it.

### Tags — add one, get the rest for free

Tags are an **open taxonomy**: put any new lowercase word in `tags` and publish. No config or
code change. The moment a post uses a new tag, the site wires it up automatically:

- a topic page is generated at **`/tags/{topic}/`**, listing every post under it, newest first;
- the topic appears as a clickable **chip** on the post, on every post card, and in the
  **Home hero + About** lists (ordered most-written-first);
- the topic page is included in the **sitemap** and the `/tags/` index, and its posts stay
  findable in search (search indexes post content, so a topic surfaces through its posts).

To retire a topic, just remove it from the post(s) — its page and chips disappear on the next
build. (Chips only show for topics that have at least one published post.)

### Hero images

The hero is the banner at the top of the post (and the card thumbnail). It's opt-in: a post
with no `hero` falls back to an on-brand gradient thumb. Two ways to set one:

1. **A file in the repo** — drop a `.jpg`/`.png`/`.webp`/`.svg` into the post's folder
   (beside `index.md`) and point `hero.src` at it **by filename**:
   ```yaml
   hero:
     src: dogs.jpg          # the file content/posts/<this-post>/dogs.jpg
     alt: "My three dogs on the trail"
   ```
   There's no automatic resizing yet, so export photos **~1600px wide and compressed** to
   keep pages fast (see *"Sizing & compressing a local image"* below; lazy-loading is applied
   for you).
2. **A gradient SVG** — the abstract banners (e.g. `content/posts/hello-corner-of-the-internet/hero.svg`)
   are hand-written SVGs on a `1200×600` canvas using the brand palette
   (violet `#8b5cf6` → fuchsia `#d946ef` → amber `#f59e0b`, defined as `--ck-accent-*` in
   `assets/css/custom.css`). Copy an existing `hero.svg`, tweak the gradient stops and shapes,
   and save it into the new post's folder. **For SVG heroes, also generate a `hero.png`
   sibling for social sharing** (`rsvg-convert -w 1200 -h 600 hero.svg -o hero.png`) — the
   site keeps the crisp SVG on the page but auto-uses the PNG in link previews. A full
   explainer + authoring recipe lives in [svg-demystified.md](svg-demystified.md).
3. **A web URL** — `hero.src` can also be a full external image URL (e.g. from Unsplash); it's
   used as-is on both the post page and the card. See **"Using a web image"** below for the
   one gotcha (the page URL is not the image URL).

`alt` is **required** on any hero — an empty alt fails the build. (Note: link-preview scrapers
don't render SVG share images, so for an SVG hero the site automatically substitutes a `hero.png`
sibling in the share meta — generate one as shown above; without it, shared links fall back to
the site avatar.)

### Additional (inline) images

Use standard markdown. The source can be a **file in the post's folder** (referenced by
filename)…

```markdown
![Three dogs mid-zoomies on the trail](trail-photo.jpg)
```

…or a **full web URL**:

```markdown
![A sleepy golden retriever](https://images.unsplash.com/photo-XXXXXXXXXXXX)
```

Every inline image **must** have alt text — an empty `![](…)` fails the build (accessibility).
Images lazy-load automatically; the same ~1200px-and-compressed guidance applies to files you
add to the repo.

### Using a web image (e.g. Unsplash)

For a hero or an inline image, an external URL must be the **direct image URL**, not the
gallery/page URL. On Unsplash, `https://unsplash.com/photos/9-iXBal9Ww0` is the *page* — to get
the image URL, open the photo, **right-click it → "Copy image address"** (it will look like
`https://images.unsplash.com/photo-…`). Paste that into `hero.src` or the markdown `![alt](…)`.
Unsplash photos are free to use; crediting the photographer is a nice touch.

Trade-off — durability **and** performance: a repo file is more durable (a web image can move,
change, or disappear) and usually a touch faster — it's served from the site's own CDN with no
extra connection to a third-party host, and you control the exact compressed size. A web image
adds a cross-origin request, and a **bare** Unsplash URL can pull a multi-megabyte original;
append sizing params like `?w=1200&q=80&auto=format` to get an optimized WebP/AVIF instead.
Either way, size is the real lever — there's no automatic resizing yet, so compress before you
ship. For posts that matter, download the image into the post's folder and reference it by
filename.

### Sizing & compressing a local image

The site doesn't resize images yet, so size and compress **before** you add the file. Targets:

| Use | Displays at | Export to (long side) | Shape |
|---|---|---|---|
| **Hero banner** | up to 720px wide | **~1600px** | Landscape, ~2:1 (e.g. 1600×800). It's also center-cropped to a short band on cards, so **keep the subject centered**. |
| **Inline — landscape** | up to 720px wide | **~1440px** | Any landscape ratio. |
| **Inline — portrait** | up to 720px wide | **~1200px** | Portraits get tall fast in the 720px column — keep them modest. |

Rule of thumb: export at **~2× the display size** (for sharp retina rendering), then compress to
**≤ ~250 KB**, quality ~80, in **WebP** (smallest) or optimized JPEG — PNG only for graphics or
transparency. Never enlarge past the original; upscaling just adds bytes with no extra detail.

**How (macOS):**

- **Preview** (no install) — open the image → *Tools → Adjust Size* (set Width, keep "Scale
  proportionally") → *File → Export* and pick JPEG with Quality ~80. Save into the post's folder.
- **`sips`** (built-in CLI) — `-Z` scales the **longest side** to N px, preserving aspect ratio:
  ```bash
  # Hero → 1600px wide, JPEG quality 80
  sips -Z 1600 -s format jpeg -s formatOptions 80 original.jpg \
    --out content/posts/<post-folder>/hero.jpg

  # Inline landscape → 1440px;  inline portrait → 1200px (swap the -Z value)
  sips -Z 1440 -s format jpeg -s formatOptions 80 original.jpg \
    --out content/posts/<post-folder>/photo.jpg
  ```
- **[squoosh.app](https://squoosh.app)** (no install, best compression) — drag the image in, choose
  **WebP**, set the width and quality ~80, download into the post's folder. Often roughly half the
  size of the equivalent JPEG.

Then reference the file by name in `hero.src` or `![alt](filename.webp)`.

### Embedding a YouTube video

Use Hugo's built-in shortcode in the markdown body — paste the video ID (the part after
`watch?v=`):

```markdown
{{< youtube dQw4w9WgXcQ >}}
```

It renders a responsive, lazy player. (Core reading still works without it — video is
supplementary, in keeping with the no-JS-core principle.)

## Analytics & privacy

The site uses **[GoatCounter](https://www.goatcounter.com/)** for a lightweight readership
signal — how many people viewed each page, and where they came from. It's a single async
script include (`count.js`), no SDK and no build step.

### Why there's no cookie-consent banner

GoatCounter is privacy-friendly by design, which is the whole reason the site needs **no
consent flow**:

- **No cookies** — it doesn't set any, so there's nothing that triggers the EU ePrivacy
  "cookie law".
- **No personal data** — no IP storage, no cross-site identifiers, no fingerprinting; visits
  are counted, visitors are not tracked across sites. With no PII collected, GDPR consent
  isn't required either.
- **No third-party ad/tracking network** — the only request is to GoatCounter's own CDN.

So readers get a clean page with no interstitial banner, and the site still stays on the right
side of privacy law. (This is the NFR-3 constraint: *no consent-requiring cookies, no PII,
analytics is script-include only*.)

### Collected in production only

Analytics is emitted **only in production builds** — your local authoring loop never inflates
the stats:

| Command | Hugo environment | Counter emitted? |
|---|---|---|
| `hugo server -D` / `hugo server` | `development` (Hugo default) | No |
| `hugo --gc --minify` (and Amplify's build) | `production` (Hugo default) | Yes |

You don't pass any flag for this — it rides Hugo's built-in environment defaults, so the
commands in **Run locally** already do the right thing.

### How it works (technical details)

Three small pieces, all in-repo:

1. **`hugo.toml`** holds the endpoint as a param, so the code stays out of the template and is
   easy to change:
   ```toml
   [params]
   goatcounter = "https://carrie.goatcounter.com/count"
   ```
2. **`layouts/_partials/custom/analytics.html`** emits the include, gated **twice**:
   ```go-html-template
   {{- if hugo.IsProduction -}}
   {{- with .Site.Params.goatcounter -}}
   <script data-goatcounter="{{ . }}" async src="//gc.zgo.at/count.js"></script>
   {{- end -}}
   {{- end -}}
   ```
   - `hugo.IsProduction` → true only in production builds (the environment-default behavior
     above), so previews never count.
   - `with .Site.Params.goatcounter` → emits nothing if the param is unset, so a fork or a
     misconfigured build silently produces no script rather than a broken tag.
3. **`layouts/_partials/custom/head-end.html`** — Hextra's `<head>` extension hook — calls the
   partial, so the include lands on **every page** (posts, home, tags, 404, …).

To verify the gating after a change: `hugo --gc --minify -d /tmp/out` then
`grep -rl gc.zgo.at /tmp/out` should list every HTML page; the same grep against a
`hugo server` build (or `--environment development`) should find none.

The live numbers are in the GoatCounter dashboard at `carrie.goatcounter.com`. Counting starts
only once a change is deployed to production on `main` — the script does nothing in local or
preview builds.

## Project layout

```text
hugo.toml          # site config: baseURL, title, Hextra module import, theme/search/taxonomy
amplify.yml        # AWS Amplify build spec (single deploy pipeline) — see SETUP.md
go.mod / go.sum    # Hugo Module — Hextra theme version lock
content/           # Markdown content (content/_index.md is the home page)
layouts/           # project layout/partial overrides (shadow the theme; never edit the module)
assets/            # custom CSS/JS
static/            # favicon and other static files copied as-is
```

Customizations live in `layouts/` and `assets/`, which shadow the Hextra module by Hugo's lookup order — the theme module itself is treated as read-only and is never edited in place.

### Upgrading Hextra

Bumping the theme (`hugo mod get -u github.com/imfing/hextra`) is usually safe because our overrides shadow the module. The one thing to check by hand: **frozen copies of theme templates.** `layouts/docs/list.html` embeds a verbatim copy of Hextra's `docs/list.html` `main` block (currently from **v0.12.3**) in its section-index branch. Our override always wins with no build error, so a theme fix to that block won't reach the site until the copy is refreshed. After an upgrade, diff that branch against the new module's `docs/list.html` and re-copy if it changed. (`grep -rn "v0.12.3" layouts/` surfaces any other version-pinned copies to re-check.)
