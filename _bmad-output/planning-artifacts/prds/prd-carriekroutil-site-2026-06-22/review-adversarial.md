# Adversarial Review — PRD: carriekroutil.com

**Reviewer stance:** cynical, adversarial. Goal is to find what is weak, hand-wavy, or will bite during implementation — not to be nice.
**Source PRD:** `prd.md` (status: draft, 2026-06-22)
**Review date:** 2026-06-22

**Verification note:** I attempted to (a) confirm Hextra's actual blog/tag/read-time capabilities against its docs and (b) inspect this repo for the legacy artifacts the PRD claims exist. Web access and most shell access were denied in this environment. What I *could* confirm: the repo at `/Users/carrie/code/carriekroutil-site` contains only `_bmad`, `_bmad-output`, `.claude`, and an **empty** `docs/` directory. There is **no Hugo site, no `hugo.toml`, no theme, no `app.yaml`/`main.go`/`.gcloudignore`, and no `.github/workflows`** present. This directly contradicts several "settled" claims in the PRD (see C2). Findings that depend on Hextra's exact runtime behavior are flagged as **owed-verification** rather than asserted as fact, but the *PRD's failure to have verified them itself* is the finding.

---

## Overall verdict

The product thinking is genuinely strong and the document is unusually honest about its own assumptions. But the PRD leans hard on "Hextra gives us this for free" for the three features that are actually load-bearing for a *blog* (post stream, tag pages, read-time/excerpts) — and Hextra is a **documentation theme**, not a blog theme. The north-star claim (markdown → push → live, no manual steps) is asserted but the friction points that will actually break the habit (build failures, bad front matter, image handling, draft handling) are named only as "surface at build time" without saying *what Carrie sees or does next*. The success metrics include two (SM-1, SM-2) that the deliberately tooling-free design **cannot measure**. This is a solid PRD with a soft technical underbelly that architecture must harden before any of it is buildable.

---

## CRITICAL findings

### C1. The entire blog rests on "Hextra gives it for free," but Hextra is a documentation theme — FR-4, FR-5, FR-6, FR-8, §4.5, NFR-4
This is the single biggest risk and it threads through five FRs. The PRD repeatedly assumes Hextra natively provides blog mechanics:
- FR-9 / §4.5: search "Comes 'for free' with the theme."
- FR-6: "`[ASSUMPTION: Hextra/Hugo word-count read-time is acceptable.]`" — assumes read-time *exists*, only questions whether it's "acceptable."
- FR-4 / FR-8: `/posts/` reverse-chron stream and `/tags/{topic}/` auto-generated tag pages "auto-generated."
- FR-2: cards showing "title, a Topic tag, an excerpt, and date + read-time."

Hextra's primary design target is documentation sites (sidebar nav, doc trees, FlexSearch over docs). Its blog support is a secondary archetype and is materially thinner than a purpose-built blog theme (e.g. PaperMod). **Read-time, excerpt rendering on cards, and styled tag/taxonomy pages are not guaranteed to ship in the shape the PRD's "Consequences (testable)" describe.** Hugo *core* provides taxonomy (`/tags/`) and `.ReadingTime`, but the *templates that render them on-brand* are theme-specific — and the PRD's testable consequences are written as if the rendered output already exists.

Why critical: if even one of {tag pages, read-time, card excerpts, the post-stream layout} requires custom layout overrides, then NFR-4 ("actively-maintained Hextra theme via Hugo Modules; no toolchain that adds publishing friction") is undermined — you're now maintaining theme overrides, which is exactly the "still updatable in three years" risk the PRD claims to avoid. The PRD never distinguishes "Hugo gives this" from "Hextra renders this nicely out of the box." Architecture must build a throwaway Hextra blog spike *before* committing, and the PRD should say so as an explicit risk, not a buried `[ASSUMPTION]`.

### C2. "Single settled pipeline" FR-11 claims cleanup of artifacts that are not in this repo — FR-11, §8 Q2
FR-11 and Open Question 2 describe removing "the old S3-sync GitHub Action and leftover GCP App Engine artifacts (`app.yaml`, `main.go`, `.gcloudignore`)" and "the prior `ananke` theme," and ensuring no "`code.shebytes.io` / 'SheBytes' remnants." **None of these files exist in `/Users/carrie/code/carriekroutil-site`.** The repo is greenfield — there is no Hugo site here at all yet.

Two possibilities, both bad for the PRD as written:
1. The legacy artifacts live in a *different* repo (the old SheBytes repo) and v1 is being built fresh here — in which case "no carry-over" is trivially true and FR-11's cleanup task is about a repo this PRD never names. The testable consequence "no `code.shebytes.io` remnants" is then testing the absence of something that was never present.
2. The build hasn't started and the PRD is describing a future state it *assumes* will inherit the old repo — in which case the "settled" framing and the §10 claim "(Post count confirmed)" / resolved-feeling Open Questions are premature.

Either way, FR-11 is written with false confidence about the starting state. This is critical because FR-11 is one of only two FRs (with FR-10) that SM-1 and SM-2 validate, and "Cleanup is a manual one-time task Carrie performs (not build-enforced)" means **nothing in the system prevents a competing legacy pipeline from publishing** if it does exist elsewhere. A single manual checklist item, in a different repo, owned by "Carrie, manual," is the kind of thing that silently double-deploys. The PRD should name the legacy repo explicitly and state how Carrie *verifies* the old pipeline is dead (not just "removed or inert").

### C3. The north-star ("no manual steps") has no failure-mode story — FR-10, FR-6, §1, §4.6
§1 and §4.6 make the load-bearing promise: "markdown → `git push` → live in minutes... *if posting isn't trivially easy, the posts stop.*" Then FR-10's testable consequences describe only the **happy path**. The PRD never says what happens when:
- **The Amplify build fails** (broken front matter, Hugo template error, bad shortcode). On a static-site push-to-deploy pipeline, a failed build means the push *silently does nothing* — the site stays on the last good version and Carrie gets an email (if configured) or nothing. The promised experience is "wrote it, pushed it, it's live." The actual experience on failure is "pushed it, nothing happened, no error in my face." That is *precisely* the friction that kills the habit, and it is unaddressed.
- **Front matter is malformed.** FR-6 says invalid dates "surface at build time rather than silently mis-sorting `[ASSUMPTION]`." But "surface at build time" = the build fails = see above. Worse: Hugo by default may *not* fail on a missing date — it can default to zero-value or mtime depending on config. The assumption that bad dates "surface" is unverified and likely false without explicit Hugo config (`build.errorOn*` style guards). This is tagged `[ASSUMPTION]` but it's really an unverified claim about Hugo behavior dressed as a design decision.
- **A broken image link or missing `alt`.** NFR-2 says alt is "enforced via front-matter `alt` field" — *enforced how?* Hugo does not natively fail a build because a front-matter field is absent. Without a custom check, a missing alt produces an `<img>` with empty/no alt and the build succeeds. "Enforced" is aspirational.

Why critical: the PRD's own thesis is that friction kills the product, yet the most common real-world friction (a build that fails on push) is glossed. A north-star feature with no defined failure UX is a north-star with a hole in it. The PRD needs an FR (or NFR) covering "what Carrie sees and does when a push doesn't go live," including build-failure notification and a fast local-preview/validate step (`hugo server` locally) so failures are caught *before* push.

---

## HIGH findings

### H1. Two of the four primary success metrics are not measurable with the chosen tooling — SM-1, SM-2, §7
- **SM-1 ("the habit holds")**: "~1–2 Posts/month... still being updated a year — ideally three — from now." Measurable in principle (count commits/posts), but the PRD provides **no instrument**: no tracking of post cadence, and analytics (FR-14) is explicitly counted to *pageviews*, not authoring cadence. Nobody is assigned to look. A metric with no measurement mechanism and no review cadence is a wish.
- **SM-2 ("publishing is trivial")**: "Carrie never hesitates to post because of process." This is **unmeasurable by construction** — it's an internal emotional state with no proxy metric, no instrumentation, and (by SM-C1) deliberately no analytics that would even hint at it. It's a fine *design principle* but mislabeled as a *success metric*. As written it can never be falsified, which means it can never validate FR-10 as claimed.

The PRD is internally honest that it doesn't chase traffic (SM-C1), but the consequence is that **its own primary metrics are inert.** Recommend: demote SM-2 to a design principle, and give SM-1 a concrete, zero-tooling check (e.g., "review post count in repo every 6 months" — a calendar reminder, which is the only instrument compatible with the minimalist constraint).

### H2. FR-1 / FR-9 contradict each other on the "works without JavaScript" floor — FR-1, FR-9, NFR-1
FR-1 consequence: "usable and readable without JavaScript (core content is static HTML)." NFR-1: "core reading works without JavaScript." FR-9 then carves out search as "the accepted exception." Fine so far — but FR-13 (theme toggle) "the choice persists (Hextra-provided)" relies on JS/localStorage, and the dark-default behavior, if implemented via a JS class-flip, can cause a **flash of incorrect theme** or a no-JS visitor stuck in whatever the server-rendered default is. The PRD asserts "loads in dark mode by default" but doesn't reconcile that with the no-JS floor (does a no-JS visitor get dark? does the toggle simply not appear?). The "works without JS" claim is stated three times but never tested against the theme-toggle and search mechanics that are inherently JS. Testable consequence for FR-1 ("usable without JS") is real, but the PRD hasn't thought through what *degraded* looks like for the toggle.

### H3. "Read-time and metadata" FR-6 hides two unverified Hugo behaviors as one assumption — FR-6
FR-6 bundles: (a) automatic read-time derivation, (b) date from front matter, (c) "missing/invalid dates surface at build time." As noted in C3, (c) is an unverified claim about Hugo's default behavior, not a design choice — Hugo will happily build with a zero-value date and *silently mis-sort*, which is the exact failure FR-6 claims won't happen. The "testable consequence" "missing/invalid dates surface at build time" is **not testable as a property of the system** until someone configures Hugo to actually fail on it (and that config is non-trivial). This FR over-promises on a default that doesn't exist.

### H4. Image handling — the most friction-prone part of "drop in two photos" — is barely specified — UJ-3, FR-5, FR-2
UJ-3's whole charm is "drops in two photos." But the PRD never says **where images live or how they're referenced**: page bundles (`/posts/my-post/photo.jpg` + bundle index) vs. a shared `/static/` or `/assets/` tree vs. remote URLs. This is the #1 real-world friction in markdown blogging:
- If page bundles: Carrie must create a folder per post, not just a `.md` file — which contradicts the Glossary's "one markdown file."
- If `static/`: relative image paths in markdown break between local preview and built site unless handled carefully.
- Hero image + `alt` (Glossary, FR-5, NFR-2) requires a specific front-matter shape that the PRD never defines.
- Image optimization/resizing (NFR-1 "images lazy-load") — Hugo image processing requires `assets/` + resource functions, which the default markdown `![]()` does **not** trigger.

FR-2 promises card excerpts and FR-5 promises images "render with their alt text," but the authoring ergonomics that make those true are undefined. For a PRD whose thesis is friction, leaving image handling unspecified is a high-severity gap. Architecture must pin the image strategy and the PRD should at least state the constraint (e.g., "page-bundle per post" — and then fix the Glossary's "one file" claim).

### H5. Open Questions are framed as resolved-adjacent but several are real blockers — §8, §10
The Assumptions Index (§10) has a reassuring tone ("Post count confirmed: 4–6") that makes the document *feel* settled, but:
- **Q3 (light-mode AA)** is a genuine accessibility correctness question, yet light mode is *in scope* for v1 (FR-13, §6.1) while the AA verification is deferred (§6.2, Q3). So v1 ships a user-facing toggle to a mode the PRD *admits may fail WCAG AA* (NFR-2 says AA is a hard floor). That's a direct internal contradiction: NFR-2 mandates AA; FR-13 + Q3 ship a mode that hasn't met it. Either light mode is out of v1, or the accents are verified before launch — "tune later" is not compatible with an AA floor.
- **Q2 (legacy cleanup)** — see C2; this is potentially a double-publish risk, not a tidy-up.

### H6. FR-11 "no leftover deploy paths... so they cannot publish competing output" is asserted but not enforceable — FR-11
The consequence "Legacy/duplicate deploy paths... are removed or inert so they cannot publish competing output" is paired with "Cleanup is a **manual one-time task Carrie performs** (not build-enforced)." These two sentences contradict the word "cannot." If it's manual and not enforced, then they *can* publish competing output until Carrie remembers to disable them. The testable consequence is therefore not testable as a guarantee — at best it's "Carrie asserts she did the cleanup." For a static site where an old S3-sync action could overwrite a bucket or a stale DNS record could point at GCP, "cannot" needs to mean DNS/bucket-level verification, not a checklist.

---

## MEDIUM findings

### M1. "Excerpt" on Home/cards is unspecified and Hextra-dependent — FR-2
FR-2 requires cards to show "an excerpt." Hugo excerpts come from `<!--more-->` markers or `.Summary` (auto-truncated). The PRD doesn't say which, doesn't say the length, and doesn't say whether Hextra's blog card partial even renders one. If Carrie must hand-place `<!--more-->` in every post, that's a manual step that nicks the friction-free promise. Minor, but it's an authoring-loop papercut the PRD's own thesis says to hunt for.

### M2. Draft handling is asserted once and never given an authoring story — FR-5, UJ-3
FR-5: "Posts marked `draft` do not appear in the Post Stream or build output." True of Hugo (`draft: true` excluded unless `--buildDrafts`). But: how does Carrie *preview* a draft before publishing? The friction-free loop implies "write, push, live" — but real authoring needs "write, preview locally, then push." The PRD has no local-preview FR at all (`hugo server -D`). Without it, the only way to see a post rendered is to push it live (or push it as draft and... it doesn't build). This is a gap in the authoring loop that also intersects C3 (catching build failures before push). Medium because it's recoverable, but it's exactly the kind of omission that turns "trivial" into "annoying."

### M3. "Build-to-live time is a few minutes `[ASSUMPTION: under 5 min]`" — unverified for Hugo+Modules cold builds on Amplify — FR-10
Amplify cold builds (fetching Hugo, fetching the Hextra Hugo Module, building) can exceed 5 minutes, especially the first build or after cache invalidation. The assumption is reasonable for warm incremental builds but the PRD treats "under 5 min" as the norm. Low-stakes (it's explicitly "not a hard SLA"), but the Hugo *Modules* choice (NFR-4) specifically adds a `go`-based module fetch step that can be slow/flaky on Amplify's build image — worth flagging as a known variable, since Module setup on Amplify (needs Go installed in the build environment) is itself a non-trivial config step the PRD treats as given.

### M4. Hextra Hugo Module on Amplify requires Go in the build image — NFR-4, FR-10
NFR-4 mandates "Hextra theme via Hugo Modules." Hugo Modules require Go to be available in the Amplify build environment, and Amplify's default Hugo/static image may not include it (or the right version). This is a concrete setup landmine: the PRD says "Hugo Modules" as if it's the low-friction choice, but the alternative (theme as git submodule or vendored) may actually be *lower* friction on Amplify. The PRD picks the harder-to-configure option without acknowledging the Amplify build-image dependency. Architecture should evaluate; PRD shouldn't assert Modules as settled.

### M5. FR-14 names tools but defers the decision past launch with a known drift risk the PRD itself flags — FR-14, §6.2
The PRD is self-aware here ("emotionally load-bearing 'pick one soon' — don't let it drift indefinitely") which is good. The weakness: there's no forcing function. "Revisit at/just after launch" with owner "Carrie" and no date is exactly how deferred decisions drift. Minor because it's genuinely non-blocking, but the PRD's own anxiety about drift suggests it should set a date, not a vibe.

### M6. NFR-2 "enforced via front-matter `alt` field" overstates enforcement — NFR-2
As in C3: nothing in stock Hugo/Hextra *enforces* presence of an `alt` front-matter field. "Enforced" should be "convention" unless a build-time check is specified. Accessibility-as-convention degrades silently the first time Carrie forgets it at 10pm on a Sunday — which UJ-3 says is exactly when she posts.

---

## LOW findings

### L1. Title is still "Working title — confirm" and status "draft" — §0
Cosmetic, but the FR-11 testable consequence requires `title` to "reflect carriekroutil.com," while the doc itself hasn't confirmed its own title. Trivial.

### L2. "4–6 most-recent Posts" (FR-2) vs Open Question 4 "confirm how many (N)" — FR-2, §8 Q4, §10
§10 says "Post count confirmed: 4–6" but §8 Q4 still lists it as open ("confirm how many"). Pick one — minor internal inconsistency. Note "4–6" is itself a range, not a number; the implementer needs a single N or a rule.

### L3. SM-4 references "NFR-1" performance but NFR-1 explicitly disclaims a budget — SM-4, NFR-1
SM-4 ("fast & looks like her... Validates NFR-1") points at an NFR that says "no formal perf budget... treat as a guideline, not an SLA." So SM-4 validates against a deliberately unmeasurable target. Consistent with the document's overall "no metrics" posture, but it means SM-4's "fast" half is also unfalsifiable. Low because "looks like her" is the real intent.

---

## What is genuinely solid (move on)

- **Vision (§1), JTBD (§2.1), Non-Users (§2.2), Non-Goals (§5):** crisp, opinionated, internally consistent. The "friction kills the product" thesis is well-argued and correctly drives scope.
- **Counter-metrics (SM-C1, SM-C2):** unusually mature — explicitly naming "don't chase traffic" and "don't add friction" as anti-goals is exactly right for this product.
- **Deferral discipline:** the document is honest about what it's deferring (analytics, light-mode tuning, 404) and mostly tags assumptions inline. The *habit* of `[ASSUMPTION]` tagging is good; the problem (above) is that several tagged "assumptions" are actually unverified technical claims, not product choices.
- **UX delegation:** correctly points to the UX spine as authoritative rather than restating tokens. No notes.
- **FR-3 (About):** concrete, testable, low-risk. Fine as-is.

---

## Top fixes before architecture / epics

1. **Spike Hextra as a blog before anything else (C1).** Confirm — with a throwaway build — that post stream, tag pages, read-time, and card excerpts render acceptably *without* custom layouts. If they don't, NFR-4's low-maintenance claim is false and the theme choice needs reconsidering. Reclassify FR-4/5/6/8's "Hextra-provided" assumptions as risks pending this spike.
2. **Add a build-failure / authoring-safety FR (C3, M2).** Define: local preview (`hugo server`), what Carrie sees when a push fails to deploy, and build-failure notification. The north-star is hollow without it.
3. **Pin image strategy and fix the Glossary "one file" claim (H4).** Page bundles vs static, hero+alt front-matter shape, optimization.
4. **Resolve the light-mode AA contradiction (H5/NFR-2).** Either pull light mode from v1 scope or verify AA before launch — "ship untuned, tune later" violates the stated AA floor.
5. **Demote SM-2 to a principle and give SM-1 a real (zero-tooling) check (H1).**
6. **Name the legacy repo and define verifiable kill of the old pipeline (C2/H6).** "Manual, not enforced" + "cannot publish competing output" cannot both be true.
