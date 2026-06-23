# Tech Currency Review — ARCHITECTURE-SPINE.md

**Reviewer lens:** Verify every committed technical decision was web-researched or reality-checked rather than asserted from training data.
**Date of review:** 2026-06-22 (live web checked same day)
**Target:** `architecture-carriekroutil-site-2026-06-22/ARCHITECTURE-SPINE.md`

**Verdict: ISSUES-FOUND** — Most decisions check out against the live web and were clearly reality-checked, but two claims are wrong/misleading: (1) the Hugo version notation `0.14x+` is incorrect against current reality, and (2) the spine asserts Hextra renders `/tags/` term pages "natively" when it does not — this is custom work mismarked as native.

---

## Claims verified TRUE (web-confirmed)

### Hextra latest version v0.12.3, released 2026-05-05, actively maintained — CONFIRMED
GitHub releases page confirms **v0.12.3 is the latest release, dated May 5, 2026** ("focuses on bug fixes and small maintenance updates"). Prior tags: v0.12.2 (Apr 17), v0.12.1 (Mar 6), v0.12.0 (Feb 20). The theme is actively maintained (2,176 stars, regular cadence). The spine's claim is exactly right.
Source: https://github.com/imfing/hextra/releases

### Hugo Modules install path `github.com/imfing/hextra` — CONFIRMED
The official Getting Started docs confirm the Hugo Modules method: `hugo mod get github.com/imfing/hextra` plus a `module.imports.path = github.com/imfing/hextra` entry. This is the current, recommended install path. Spine is correct.
Sources: https://imfing.github.io/hextra/docs/getting-started/ , https://github.com/imfing/hextra

### Hextra blog list layout via `type: blog` section — CONFIRMED
Hextra documents exactly three layout types: `docs`, `blog`, `default`. The `blog` type provides "both listing and detailed article views" and is activated by setting `type: blog` (typically via `cascade`) on a section's `_index.md`. AD-4's mechanism is real and correct.
Source: https://imfing.github.io/hextra/docs/guide/organize-files/

### FlexSearch on by default — CONFIRMED
Hextra docs state verbatim: "Full-text search powered by FlexSearch is enabled by default." No enablement flag required. AD-13 and the Stack table are correct.
Source: https://imfing.github.io/hextra/docs/guide/configuration/

### Dark-default via `params.theme.default: dark` — CONFIRMED
Config key is exactly `params.theme.default` with `dark` as a documented value ("always use dark mode"). The Consistency Conventions row is correct.
Source: https://imfing.github.io/hextra/docs/guide/configuration/

### AWS Amplify build-from-source for Hugo with `HUGO_VERSION` + `amplify.yml` — CONFIRMED
This is a real, current, widely-documented pattern. `amplify.yml` is detected at repo root; Amplify's default bundled Hugo is stale (0.75.1), so the established workaround is to pin via a `HUGO_VERSION` env var and curl the `hugo_extended_${HUGO_VERSION}` tarball in `preBuild`. AWS's own DevOps blog and Hugo's official host-and-deploy docs cover this. AD-10/AD-11 are sound. (Minor note below on the `min`/`extended` wording.)
Sources: https://aws.amazon.com/blogs/devops/agile-website-delivery-with-hugo-and-aws-amplify/ , https://gohugo.io/host-and-deploy/host-on-aws-amplify/

### Amplify native build-failure email notification — CONFIRMED
AWS Amplify Hosting has a native "Build notifications" feature (Hosting → Build notifications → Manage notifications) that emails stakeholders on build SUCCEED/FAILED/STARTED via an SNS topic behind the scenes. AD-12's "native email-on-failure" is real. (Caveat: a known cosmetic bug mis-encodes branch names with slashes/periods in notification URLs — not relevant to a single default-branch deploy, but worth noting.)
Source: https://docs.aws.amazon.com/amplify/latest/userguide/notifications.html

### Custom-work items correctly marked NOT native — MOSTLY CONFIRMED
The spine correctly identifies as custom work:
- **Home latest-posts feed** (AD-6: "assuming Hextra auto-populates a latest-posts feed (it does not)") — correct; Hextra has no home latest-posts feed, this is a custom partial.
- **Featured post** (AD-7) — correct; `featured: true` + `where ... first` is custom Hugo templating, not a Hextra feature.
- **Read-time** (AD-3, Capability map "read-time partial override") — correctly treated as a custom override deriving from `.ReadingTime`. (`.ReadingTime` is a Hugo-core page variable, not Hextra; the spine attributes it to a partial override, which is accurate.)

---

## ISSUES

### [HIGH] AD-8 asserts `/tags/` term pages are "rendered natively by Hextra" — they are NOT
AD-8 (and the Capability map row "Topic chips + tag pages → Hextra term pages") states a Topic Chip links to `/tags/{topic}/` "rendered natively by Hextra as the filtered list." This is the one place the spine mis-marks custom work as native.

Reality: **Hextra does not ship a styled taxonomy/term-page layout.** Hugo *core* auto-generates `/tags/` and `/tags/{term}/` pages, but those fall back to Hugo's generic `_default` term/taxonomy templates, not a Hextra-styled blog-list view. The Hextra maintainer-tracked discussion #330 ("Support for Taxonomies out of the box") confirms users wanting "theme dedicated pages for tags and categories in the style of Hextra" must supply their own `layouts/_default/taxonomy.html` / `layouts/_default/term.html` (or `layouts/tags/...`) — it is a known gap, resolved only by custom layouts.

Impact: AD-8 claims "No custom filter code to maintain" and the Structural Seed does not list a taxonomy/term layout under `layouts/`. As written, the build will produce unstyled (or mismatched) tag pages, contradicting the "rendered natively" promise. This is a training-data-style assertion that did not survive a reality check.

Recommended fix: Reword AD-8 to acknowledge tag term-pages require a **custom Hextra-styled term/taxonomy layout override** (project `layouts/`), consistent with AD-5. Add that layout to the Structural Seed (e.g. `layouts/_default/term.html` reusing the blog card/list partial). The no-client-JS principle still holds; only the "native, zero custom layout" claim is wrong.
Source: https://github.com/imfing/hextra/discussions/330

### [MEDIUM] Stack table "Hugo (extended) ... current 0.14x+" — version notation is wrong/stale
The Stack table says Hugo is "current 0.14x+". Against the live web (2026-06-22) this is incorrect:
- **Current Hugo is v0.163.3 (released 2026-06-18)** — far beyond the 0.14x range. "0.14x+" reads as 0.140–0.149, which is below even Hextra's floor.
- Hextra v0.12.3's `theme.toml` sets **`min_version = "0.146.0"`** — so the real constraint is "≥ 0.146.0", and the sensible pin is a recent 0.16x.

The "+" arguably saves it, but "current 0.14x" is plainly not current. Because this spine's whole Stack section is captioned "web-verified current at authoring (2026-06-22)," an out-of-date version number directly undercuts that claim. This looks asserted from training data rather than checked.

Recommended fix: State the real minimum (`>= 0.146.0`, Hextra's floor) and pin to a current release (e.g. `HUGO_VERSION = 0.163.x` as of authoring). Drop the "0.14x" phrasing.
Sources: https://github.com/gohugoio/hugo/releases , https://github.com/imfing/hextra/blob/main/theme.toml

### [LOW] "Hugo (extended)" requirement — correct, but the extended/standard distinction is shifting
Requiring **Hugo extended** is the right call (Hextra/Tailwind-built theme; extended provides the asset pipeline / Sass support historically needed) and Hextra's Getting Started explicitly says "Hugo (extended version)". So AD-10/AD-11's "Hugo extended" is correct.

Minor currency note: in the 0.16x line the Hugo project briefly moved to deprecate the separate `extended`/`extended_withdeploy` editions (then reverted). The `hugo_extended_${HUGO_VERSION}` download artifact still exists and the amplify.yml curl pattern still works, so nothing here is broken — but if/when the pin moves to a much newer Hugo, the "extended" download URL convention is worth re-checking at upgrade time (already covered in spirit by AD-10's "upgrades are deliberate").
Source: https://github.com/gohugoio/hugo/releases

### [LOW] Fallback theme "FixIt" — asserted, not evidenced in this review
The Stack lists "FixIt (vetted, if Hextra disappoints)" as a fallback. The "vetted" claim was not independently web-checked in this pass and isn't load-bearing (it is a deferred contingency, not a committed decision). Flagging only so it isn't mistaken for a verified current fact. No action required for v1.

---

## Bottom line
The architecture was substantially reality-checked: the Hextra version/date, install path, blog layout, FlexSearch default, dark-default param, the Amplify+HUGO_VERSION build pattern, and Amplify's native failure emails all verify against the live web exactly as the spine states, and the home-feed/featured/read-time items are correctly flagged as custom. Two corrections are needed before this Stack can honestly claim "web-verified current":
1. **HIGH** — AD-8: `/tags/` term pages are not natively Hextra-styled; add a custom term/taxonomy layout (mark as custom, add to Structural Seed).
2. **MEDIUM** — Stack: replace "current 0.14x+" with the real floor (≥0.146.0) and a current pin (~0.163.x).
