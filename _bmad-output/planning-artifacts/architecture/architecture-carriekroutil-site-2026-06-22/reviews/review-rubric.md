# Rubric Review — ARCHITECTURE-SPINE.md (carriekroutil.com)

- **Spine:** `_bmad-output/planning-artifacts/architecture/architecture-carriekroutil-site-2026-06-22/ARCHITECTURE-SPINE.md`
- **Reviewed against:** PRD (FR-1..FR-18, NFR-1..NFR-5) + UX DESIGN.md
- **Date:** 2026-06-22
- **Reviewer mode:** good-spine checklist walk, calibrated to a small low-stakes personal static site.

## Verdict: PASS-WITH-FIXES

This is a strong, tight spine. The paradigm (build-time static, content-as-source, git-as-truth) is correct for the product, the dependency-direction invariant is crisply drawn, and the ADs map cleanly onto the real divergence surfaces (front-matter contract, post section/URL, override location, card markup, no-JS reading). Every FR and NFR has a home. The fixes below are small: one genuinely silent operational dimension (preview/branch deploys), one stale-but-not-wrong version assertion, and a couple of minor enforceability tightenings.

---

## Checklist 1 — Does it fix the real divergence points for epics/stories, missing none?

**Largely yes.** The highest-divergence surfaces for independently-authored units are all pinned:

- Front-matter contract (AD-3) — the single biggest divergence risk for a multi-post stream. Field names, types, date format, draft semantics, derived read-time all fixed.
- Post location + URL (AD-4) — `content/posts/` with `type: blog`, preventing the Hextra-default `content/blog/` URL drift. Real, specific, correct.
- Override location (AD-5) — project `layouts/`+`assets/`, never the module. Prevents the same override authored in two places.
- Card markup (AD-6) — one custom partial shared by Home and list, preventing divergent card HTML. Good catch that Hextra does not auto-populate a latest-posts feed.
- No-JS reading (AD-13) — fixes the "a contributor adds a JS dep to a core path" divergence.

**Gap (medium):** Slug derivation is asserted to be "stable, human-readable" (AD-4) but the *mechanism* is left implicit — folder-name-as-slug vs front-matter `slug:` vs Hugo's title-slugification. Two posts authored independently could pick different conventions (e.g. one relies on folder name, one sets `slug:`), producing inconsistent URLs and, worse, a slug that silently changes if a folder is renamed. For a site whose discoverability requirement (FR-18) explicitly demands "stable" URLs, naming the slug source (recommend: folder name is the slug, front-matter `slug:` only as deliberate override) would close this. **Low-stakes but real for a one-author site that will accrete posts over years.**

## Checklist 2 — Is every AD's Rule enforceable and does it prevent its stated divergence?

Mostly yes. Notes by AD:

- **AD-1 (authoring loop inviolable):** Enforceable by review/principle, not by a build gate — but that is appropriate; it is a design-intent invariant. Fine.
- **AD-3 (front-matter contract):** Strong. "Invalid/missing date fails the build" is mechanically enforceable in Hugo. **However, "hero image field carrying a required alt" and "inline alt required" (also AD-9) are asserted as enforced but Hugo does not fail a build on a missing `alt` by default.** The rule states the intent but names no enforcement mechanism. For inline markdown images especially, "required alt" is unenforceable without a render hook or a lint step — neither is specified, and the image render hook is explicitly *deferred* (AD-9). So the alt-text requirement is currently aspirational, not enforced. **(medium)** — either accept it as a convention (honest) or name a lightweight enforcement (a render-image hook that errors on empty alt, or a CI grep). NFR-2 leans on this being enforced.
- **AD-7 (featured single slot):** Enforceable via the documented `where ... first` query; fallback-to-recent is specified. Good.
- **AD-8 (tag term-pages, no client JS):** Enforceable and clean. Note it *resolves* PRD FR-4's open assumption (Tag-Page vs client-side filtering) in favor of Tag-Page — correct call, well-justified by NFR-1.
- **AD-10 / AD-11 (version pinning, single build truth):** Enforceable via `HUGO_VERSION` env + `go.mod` lock + root `amplify.yml`. Solid.
- **AD-12 (build failure notifies):** Relies on Amplify native email-on-failure — enforceable by configuration, correctly resolves FR-17's open channel question.

No AD has a rule that *fails* to prevent its stated divergence. The two soft spots (alt enforcement, slug mechanism) are about completeness of the mechanism, not contradiction.

## Checklist 3 — Could anything under Deferred let two units diverge incompatibly?

**No serious risk.** The five deferred items are correctly chosen as non-divergence-creating:

- Analytics tool (FR-14) — single script include, capability shape already fixed (privacy-friendly, no PII, no banner). Choice between GoatCounter/Cloudflare doesn't let posts diverge.
- Light-mode AA (NFR-2) — dark is the AA-guaranteed path; light deferral is a quality gate, not a divergence vector.
- Image resize/WebP — additive render hook; existing posts remain valid.
- Custom 404 — isolated page.
- Legacy deploy cleanup — outside this repo entirely.

One thing worth noting: the deferred image render hook (AD-9) is *also* the natural home for enforcing required alt (Checklist 2). Deferring it means the alt-enforcement mechanism is deferred with it. Not an incompatible-divergence risk, but it's why the alt requirement is currently unenforced. **Low.**

## Checklist 4 — Is named tech verified-current?

Web-verified at review (2026-06-22):

- **Hugo extended** — actively released; latest extended edition shipped June 2026 (Go 1.26.x base). The spine says "pinned via `HUGO_VERSION`, current 0.14x+". The pin-via-env decision is correct and the "0.14x+" is in the right family but **slightly stale / vague** — the live line is past that. Since the spine wisely says "the code owns this once it exists" and pins via env rather than hard-coding, this is low-severity, but the seed number should read "current ~0.15x" or just "latest extended" to avoid implying 0.14x is the ceiling. **(low)**
- **Hextra v0.12.3** — the v0.12.x line is confirmed current (v0.12.0 was the major feature release with WCAG 2.2 AA work; patch releases follow). v0.12.3 is consistent with the evidence and is **not an unfounded assertion** — it carries a verifiable basis. Locking it in `go.mod` (AD-10) is the right move. **No flag.**
- **AWS Amplify / Route 53 / FlexSearch / FixIt fallback** — all stable, real, appropriate. FixIt named as vetted fallback (NFR-4) is good hedging.

The stack table is honestly captioned ("Seed — web-verified current at authoring; the code owns this once it exists"), which is exactly the right posture.

## Checklist 5 — FR/NFR coverage

Every FR and NFR has a home. Trace:

| Item | Home in spine |
| --- | --- |
| FR-1 Home hero | AD-6, AD-13; Capability Map |
| FR-2 Latest-posts preview | AD-6; Capability Map |
| FR-3 About | AD-13; `content/about.md`; Capability Map |
| FR-4 Browse all posts | AD-4, AD-8 |
| FR-5 Read single post | AD-4, AD-9, AD-3 |
| FR-6 Read-time + metadata | AD-3 (derived `.ReadingTime`), read-time partial |
| FR-7 Tag a post | AD-3, taxonomy convention (open taxonomy) |
| FR-8 Browse a topic | AD-8 (Hextra term pages) |
| FR-9 Search | Stack (FlexSearch) + AD-13 exception |
| FR-10 Publish by push | AD-1, AD-2, AD-11 |
| FR-11 Single settled pipeline / brand | AD-2, AD-11, branding convention |
| FR-12 Brand identity | conventions + UX spine (visual tokens) |
| FR-13 Dark/light toggle | theme-default convention; light AA deferred |
| FR-14 Analytics | **Deferred** (capability defined, choice deferred) |
| FR-15 Featured post | AD-7, AD-6 |
| FR-16 Local preview | AD-1; `hugo server` (Capability Map) |
| FR-17 Build outcomes surfaced | AD-12 |
| FR-18 Discoverability | conventions; Capability Map (sitemap/OG defaults) |
| NFR-1 Performance / no-JS / system fonts | AD-13, AD-8, AD-9 |
| NFR-2 Accessibility (WCAG AA) | AD-3/AD-9 (alt), theme-default convention; light-AA deferred |
| NFR-3 Privacy | privacy convention; FR-14 deferred |
| NFR-4 Maintainability / longevity | AD-5, AD-10, AD-11 |
| NFR-5 Ownership / no lock-in | paradigm (git-as-truth, static, portable); AD-2 |

**No FR/NFR is left without a home.** Minor notes:

- **FR-18** is parked entirely on "Hugo/Hextra defaults" via a convention, with no AD. That matches the PRD's own intent (verify-don't-build), so acceptable — but there is no invariant ensuring OG/Twitter-card tags actually emit the *hero image* (FR-18 names this explicitly), which depends on Hextra's OG partial reading the same hero front-matter field AD-3/AD-9 defines. Worth a one-line note that the hero field name must match what the OG partial expects. **(low)**
- **FR-12 forced-colors/print fallback** (`#d946ef` solid wordmark) is owned by the UX spine and referenced via the visual-tokens convention — correctly not restated. Fine.

## Checklist 6 — Are all structural dimensions the feature altitude owns decided/deferred/open? (esp. operational/environmental envelope)

Most are covered well: build pipeline (AD-2/AD-11), version/runtime envelope (AD-10), failure/notification (AD-12), DNS/hosting (Stack), source-of-truth repo & branch (AD-2: "default branch of `carriekroutil-site`"), directory/structural seed (Structural Seed section). Content model, URL scheme, override model — all decided.

**The one genuinely silent dimension (high):** **Environments / preview-deploy topology.** The spine decides the *production* path (push to default branch → Amplify build → live) and decides *local* preview (`hugo server`, AD-1/FR-16). But Amplify has a native **branch/PR preview-deploy** capability, and the spine is silent on whether feature branches or PRs get their own Amplify deploys. This matters because:
  - AD-2 says "exactly one deploy pipeline ... builds ... on push to the default branch" and "no second deploy path." Read strictly, that *forbids* branch previews; read loosely, it's silent. An epic/story author wiring Amplify could reasonably enable branch auto-builds (Amplify's default behavior connects a branch) and believe they're compliant — or disable them and believe the same. **This is exactly the kind of whole-dimension silence the rubric asks to flag.** A one-line decision — "Amplify builds *only* the default branch; branch/PR previews are off (or: deferred)" — closes it and removes ambiguity from AD-2's "single pipeline" claim. For this site, "off, local preview is the only pre-publish check" is the natural answer and aligns with FR-16.

Other operational dimensions, assessed and judged adequately handled for this altitude/stakes:
  - **Rollback** — implicitly handled (AD-12: failed build keeps prior good site live; Amplify atomic swap). Fine, and worth the explicit mention it gets.
  - **Secrets/config** — none needed (static site, no backend, analytics is a public script). Correctly not raised — not inventing enterprise concerns.
  - **Caching/CDN invalidation** — Amplify handles on deploy; not a divergence surface. Fine to leave silent.
  - **Domain/TLS** — Amplify + Route 53 imply managed TLS; not called out but not a divergence risk for stories. Acceptable.
  - **Backup/DR** — git repo *is* the backup (paradigm). Implicit but sound.

So: one whole dimension (environments/preview-deploys) is silent in a way that interacts with an ADOPTED rule; the rest of the operational envelope is either decided or correctly judged out-of-scope.

---

## Findings summary

| # | Severity | Finding | Fix |
| --- | --- | --- | --- |
| 1 | **high** | Environments / preview-deploy topology is silent; interacts ambiguously with AD-2's "single pipeline, default branch only" | Add one line to AD-2/AD-11: Amplify builds the default branch only; branch/PR auto-previews are off (local `hugo server` is the pre-publish check). |
| 2 | **medium** | "Required alt text" (AD-3/AD-9, leaned on by NFR-2) names no enforcement mechanism and the natural one (image render hook) is deferred — so it's currently a convention, not enforced | Either label alt as a convention honestly, or add a lightweight enforcement (render-image hook erroring on empty alt, or a CI grep) and pull just that slice out of the deferred hook. |
| 3 | **medium** | Slug derivation mechanism unspecified; independently-authored posts could diverge on URL source, risking the "stable URL" FR-18 promise | Name the slug source: folder-name-as-slug, front-matter `slug:` only as deliberate override. |
| 4 | **low** | Hugo seed version "0.14x+" is stale/vague (live extended line is past that as of 2026-06) | Reword to "latest extended (~0.15x)"; pin-via-env decision is already correct, so cosmetic. |
| 5 | **low** | FR-18 hero-image OG tag depends on the OG partial reading the same hero front-matter field AD-3/AD-9 defines, but no note ties them | Add a one-line note that the hero field name must match Hextra's OG partial expectation (verify during the Hextra blog-capability spike, PRD OQ#5). |

**Hextra v0.12.3 is NOT flagged** — the v0.12.x line is web-verified current; the version carries a basis.

Net: a tight, well-aimed spine. Apply fix #1 (the only structural hole), decide #2/#3 as a sentence each, and this is a clean PASS.
