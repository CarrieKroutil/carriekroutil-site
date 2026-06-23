# Adversarial Review — ARCHITECTURE-SPINE.md (carriekroutil.com)

**Reviewer stance:** adversary. Method: construct concrete pairs of units one level down (two posts, two layout files, two partials) that each obey *every* AD and convention to the letter, yet still build incompatibly — clashing data shapes, two owners of one piece of state, conflicting paths, or rules ambiguous enough that two authors resolve them differently. Every such pair is a hole to close.

**Verdict: HOLES-FOUND.** The spine's *strategic* invariants (pipeline, dependency direction, no-JS, no-second-deploy) are tight. The *content contract* (AD-3, AD-4, AD-6, AD-7, AD-9) is under-specified at exactly the surface the spine itself names "the highest-divergence-risk surface on the site." A single author over time, or any second contributor, will diverge. All are cheap to fix now by tightening the named ADs with literal field names and literal tiebreak rules.

Severity is calibrated for a small personal static site: "critical" = will silently produce wrong output or fail a build non-obviously; "high" = two authors WILL diverge in field name/shape; "medium" = ambiguous, resolvable two ways; "low" = cosmetic/edge.

---

## H1 — Hero image field name is unspecified `[HIGH] → tighten AD-3 / AD-9`

**The pair.** Two posts, both obeying AD-3 ("optional hero image field carrying a required `alt` string") and AD-9 ("Hero via the front-matter field with a required alt"):

- `posts/range-day/index.md`:
  ```yaml
  featured_image: hero.jpg
  images: ["hero.jpg"]   # author read "front-matter field" as Hugo's built-in `images`
  ```
- `posts/new-irons/index.md`:
  ```yaml
  hero:
    src: hero.jpg
    alt: "A bag of new irons"
  ```

**Why it's a hole.** The spine says "the front-matter field" as if there is one, but never names it. Hextra has no single canonical hero key; candidates in the wild include `images`, `featured_image`, `image`, `thumbnail`, plus a nested `hero.{src,alt}` shape. The home cards partial (AD-6) and the single template (AD-9) must each read *one* key with *one* shape. Two posts above each "carry a hero field with an alt" and pass any prose reading of the rule, yet the partial can render neither generically. This is the literal divergence AD-3 exists to prevent ("independently-authored posts diverging on field names/shapes").

**Close it.** AD-3/AD-9 must name the exact key and shape, e.g.: hero is `images: ["hero.jpg"]` (Hugo-native, also feeds OG) **plus** a sibling `imageAlt: "..."` string — OR a single nested object `hero: {src, alt}`. Pick one literally. State that the partial/template reads only that key.

## H2 — Alt text has no enforcement mechanism, only a requirement `[CRITICAL] → tighten AD-3 / AD-9 / Conventions row "Alt text"`

**The pair.**
- Post A: hero front matter includes `alt: "..."`.
- Post B: hero front matter omits `alt` entirely (author forgot).

**Why it's a hole.** AD-3 says alt is "required"; the conventions table says "enforced via front-matter/markdown." But **there is no enforcement primitive named.** Hugo does not fail a build because a front-matter alt key is absent, and standard markdown `![](img.png)` with empty alt is valid markdown that compiles fine. So Post B builds successfully and ships with a missing/empty alt — silently violating the one accessibility invariant the spine repeats three times. "Required" with no failing mechanism is a wish, not an invariant. This is critical because it is the failure mode the rule explicitly claims to prevent, and it fails *silently* (no build error).

**Close it.** Either (a) state that alt enforcement is best-effort/authoring-discipline and downgrade the language from "required/enforced" to "expected" (honest), or better (b) specify the enforcement: a `errorf`/`warnf` in the hero partial when the alt key is empty, plus `--printPathWarnings`/a link-check step in `amplify.yml`, so a missing alt is at least a loud warning. Markdown inline images cannot be build-enforced by Hugo at all — say so explicitly so the contract isn't claiming a guarantee it can't keep.

## H3 — Two posts with `featured: true`: tiebreak is under-determined `[HIGH] → tighten AD-7`

**The pair.** `posts/a/` and `posts/b/` both set `featured: true`. AD-7 says Home renders "exactly one featured slot (`where … "Params.featured" true`, first match)."

**Why it's a hole.** "First match" is ambiguous because Hugo's `where` returns pages in the **default site order**, which is *not* guaranteed to be date-descending unless the template explicitly sorts. Two authors implementing AD-6's partial will resolve "first match" differently: one writes `where .Site.RegularPages "Params.featured" true` and takes `index 0` (weight/date/default order — Hugo default is by weight then date then title/link, surprising), another writes `... | sort ... "Date" "desc" | first 1`. Same front matter, two different featured posts rendered depending on whose partial it is. The flag is single-slot but the *selection among multiple flagged posts* is not pinned.

**Close it.** AD-7 must state the literal selection: e.g. "among `featured: true` posts, the most recent by `date` wins" and give the canonical pipeline `where ... "Params.featured" true | sort ... ".Date" "desc" | first 1`. Optionally note that flagging two is allowed (newest shown) rather than an error.

## H4 — Slug authority is unspecified: filename vs `slug:` vs title `[HIGH] → tighten AD-4`

**The pair.**
- `content/posts/my-range-day/index.md` with no `slug` in front matter → Hugo derives slug `my-range-day` from the bundle folder.
- `content/posts/2026-range/index.md` with `slug: my-range-day` in front matter → Hugo uses `my-range-day`.

**Why it's a hole.** AD-4 says "Slugs are stable, human-readable" but never says **who decides the slug.** Three sources compete in Hugo: the bundle directory name, a `slug:` front-matter key, and (fallback) the title. The two posts above each obey AD-4's prose and produce the *same* `/posts/my-range-day/` URL — a hard collision (one will be overwritten/ambiguous, or Hugo errors at build). Even without collision, two authors split: one names slugs via folder, one via `slug:` key, and now URL stability depends on which knob you trust. "Stable, human-readable" doesn't pin the authority.

**Close it.** AD-4 must declare a single authority: e.g. "the page-bundle folder name IS the slug; `slug:` front-matter is not used." That makes collisions impossible (filesystem forbids duplicate folders) and makes the URL visible in the repo tree. State it literally.

## H5 — Home card and `/posts/` list card can diverge in fields shown `[HIGH] → tighten AD-6`

**The pair.**
- Home: `layouts/_partials/custom/blog-cards.html` (AD-6) shows title, date, read-time, hero, tags, excerpt — ported from `home.html` mock.
- `/posts/`: Hextra's **native blog list layout** (AD-4 explicitly activates "Hextra's native blog list") shows whatever Hextra's list card shows — typically title, date, summary, and possibly no read-time / different date format / no tag chips / different hero crop.

**Why it's a hole.** AD-6 forbids the native `hextra-card` *on Home* ("does not match the UX card design and is not used for posts"), but AD-4 **mandates** the native blog list layout *on `/posts/`*. So the site has two different card renderers for the same post, by design, owned by two different code paths (custom partial vs vendored module). A reader sees one card shape on Home and a different one on the stream. Whether that's acceptable is a real decision the spine never makes — and the two cards' *date format, read-time presence, and tag-chip presence* will drift because nothing forces them to match. This is the exact "divergent card markup between Home and the list page" that AD-6's *Prevents* clause claims to stop — but AD-6 only governs Home, leaving `/posts/` to the module.

**Close it.** Decide explicitly: either (a) the custom partial renders BOTH Home and the `/posts/` list (override `blog/list.html` to call the same partial), making one card the single source of truth — recommended and consistent with AD-5/AD-6's intent; or (b) accept divergence and document that Home cards and stream cards intentionally differ, with the field set of each pinned. Today the spine implies (a) in AD-6's rationale but mandates (b) via AD-4.

## H6 — Date format displayed to the reader is unpinned `[MEDIUM] → add Conventions row or extend AD-3`

**The pair.** Two posts both carry valid RFC-3339 `date`. The read-time/metadata partial on post A formats it `.Date.Format "Jan 2, 2006"`; the Hextra list on post B (see H5) renders `2026-06-22` or `June 22, 2026`.

**Why it's a hole.** AD-3 pins the *input* format (RFC-3339, fails build if invalid) but says nothing about the *display* format. The conventions "Dates" row is about input only. So display date format is a free variable resolved differently per template/partial, and per H5 there are at least two templates. Minor visually, but it's a genuine unpinned shared concern.

**Close it.** Add a one-line convention: canonical display date format (e.g. `"Jan 2, 2006"`) used by every template that prints a post date. Cheap.

## H7 — Read-time: derived, but format/threshold/placement unpinned `[MEDIUM] → tighten AD-3`

**The pair.** AD-3 says read-time is "derived from `.ReadingTime`, never hand-entered" — good, that kills the worst divergence. But: post A's partial renders `{{ .ReadingTime }} min read`; post B's (or the native list, H5) renders `~{{ .ReadingTime }} min` or omits it; a 0-minute (very short) post renders "0 min read."

**Why it's a hole.** The *source* is pinned but the *rendered string* and the *zero/edge case* are not. `.ReadingTime` returns integer minutes, rounding a 30-second post to `0` or `1`. Two templates show read-time two ways, and the home card (AD-6, has read-time) vs native stream card (AD-4, may not) disagree on whether it appears at all.

**Close it.** AD-3 (or the read-time partial spec) should pin the literal string and a floor (e.g. "`min(1, .ReadingTime)` min read", always shown). Lower priority than H1–H5.

## H8 — `draft: true` vs future-dated posts: two mechanisms, one intent `[MEDIUM] → tighten AD-3 / AD-11`

**The pair.**
- Author A hides a not-ready post with `draft: true`.
- Author B hides a not-ready post by setting `date` in the future (e.g. `2027-01-01`).

**Why it's a hole.** AD-3 defines `draft` semantics ("drafts excluded from build + stream"). But Hugo also has `publishDate`/future-date exclusion, governed by build flags (`--buildDrafts`, `--buildFuture`). The spine's `amplify.yml` build command (AD-11) is `hugo --gc --minify` — it does **not** specify `--buildDrafts=false` (the default, OK) **nor** the future-content behavior. If a post is future-dated, Hugo's default *excludes* it — so Author B's mechanism works by accident, but it's undocumented and a different lever than `draft`. Worse: a post with a *typo'd future date* (intended `2026`, typed `2027`) silently vanishes from the build — and AD-3's "invalid date fails the build" does NOT catch it because `2027-01-01` is a perfectly valid RFC-3339 date. Silent disappearance is exactly AD-12's nightmare ("never silently no-op"), reached via content rather than build failure.

**Close it.** AD-3 should declare `draft: true` the *only* sanctioned way to withhold a post, and AD-11 should pin the future/expiry flags explicitly (e.g. document that `--buildFuture` is OFF and that future-dated posts are silently excluded, so dates must be correct). Optionally a build-time check that warns on dates far in the future.

## H9 — Tag string format is not normalized `[MEDIUM] → tighten Conventions "Tags" / AD-3`

**The pair.**
- Post A: `tags: ["Golf"]`
- Post B: `tags: ["golf"]`  (and post C: `tags: ["disc golf"]` vs `tags: ["disc-golf"]`)

**Why it's a hole.** The taxonomy is "open, no fixed allowlist" `[ADOPTED]` — deliberately. But "open taxonomy" pins *which* tags exist, not *how a tag string is spelled/cased*. Hugo generates **separate** term pages for `Golf` and `golf` (`/tags/golf/` from both via slugification of the page, but the *display term* and potentially the term page differ by Hugo version/config), fragmenting AD-8's tag-page filtering — a reader on `/tags/golf/` misses Post A's content. The Topic Chip (AD-8) links to `/tags/{topic}/`; if two posts spell the same topic differently, the chip taxonomy splits silently. Open taxonomy makes this *more* likely, not less, because nothing flags a near-duplicate.

**Close it.** Add a normalization convention: tags are lowercase, hyphenated, singular (or whatever Carrie prefers) — a spelling rule, not an allowlist, so it doesn't violate the open-taxonomy `[ADOPTED]` invariant. One sentence.

## H10 — Featured fallback "most-recent" collides with featured selection ordering `[LOW] → clarify AD-7`

**The pair.** AD-7: "falls back cleanly to most-recent when none is flagged." Compare two implementations of the partial: one computes featured and fallback in a single sorted pipeline; another does `where featured=true` then, if empty, a *separately sorted* `.Site.RegularPages.ByDate.Reverse | first 1`.

**Why it's a hole.** "Most-recent" must mean by `date` desc — but per H3 the featured branch's "first match" order is unpinned, so the two branches can use different orderings, and a draft/future post (H8) could leak into "most recent" if the partial queries `.Pages` vs `.RegularPages` vs `.Site.RegularPages` inconsistently. Edge-only, but it's the same root cause as H3 (ordering + page-set not pinned).

**Close it.** Folds into H3's fix: pin the page set (`where (where .Site.RegularPages "Section" "posts") ...`) and the sort once, used by both the featured and fallback branches.

## H11 — `_index.md` for posts: is `type: blog` the only required key? `[LOW] → clarify AD-4`

**The pair.** Two `content/posts/_index.md` files: one with just `type: blog`; one that also sets `cascade` (e.g. cascading a default `type` or layout to children), or sets `title`/`weight` affecting list order.

**Why it's a hole.** AD-4 pins `type: blog` on `_index.md` but is silent on whether `cascade` or other section-level keys are sanctioned there. A `cascade` block is a powerful, legitimate Hugo feature that could, e.g., cascade a hero default or a `type` to all posts — a second place that influences every post's data shape, partially defeating AD-3's per-post contract. Not wrong, but unowned.

**Close it.** AD-4 can state whether section-level `cascade` is allowed and, if so, what it may set (e.g. nothing that overrides AD-3 per-post fields). Minor.

---

## Non-holes (probed, found tight)

- **AD-1 / AD-2 / AD-11 (pipeline single-source):** genuinely tight. Two contributors cannot create a second deploy path without violating literal text.
- **AD-5 (overrides never edit module):** Hugo's lookup order makes "two owners of one override" impossible by mechanism; tight.
- **AD-13 (no-JS core):** the FlexSearch exception is named as the *single* exception; a second JS dependency violates literal text. Tight.
- **AD-10 (version pinning):** `HUGO_VERSION` + `go.mod` lock is unambiguous.
- **Branding convention (zero SheBytes/ananke/quickstart remnants):** literal and checkable.

---

## Recommended AD edits (priority order)

1. **AD-3 / AD-9 — name the hero field literally** (H1) and **state the real alt enforcement mechanism, downgrading "required" if none exists** (H2). *Critical/High.*
2. **AD-7 — pin featured selection: most-recent-by-date among flagged, with the literal pipeline** (H3, H10). *High.*
3. **AD-4 — declare slug authority = bundle folder name, `slug:` unused** (H4). *High.*
4. **AD-6 / AD-4 — decide one card renderer for Home AND `/posts/`, or document intentional divergence with each field set pinned** (H5). *High.*
5. **AD-3 / Conventions — pin display date format** (H6), **read-time string + floor** (H7), **`draft` as sole withhold mechanism + future-date behavior** (H8), **tag spelling normalization** (H9). *Medium.*
6. **AD-4 — clarify section `_index.md` allowed keys / cascade** (H11). *Low.*
