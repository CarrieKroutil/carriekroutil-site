# UX → PRD Reconciliation: carriekroutil.com

**Date:** 2026-06-22
**Inputs reconciled:**
- PRD: `prd-carriekroutil-site-2026-06-22/prd.md`
- UX DESIGN: `ux-carriekroutil-site-2026-06-22/DESIGN.md`
- UX EXPERIENCE: `ux-carriekroutil-site-2026-06-22/EXPERIENCE.md`

**Scope note:** Per the PRD's own framing (§0), the UX spine is authoritative for visual tokens, IA,
and journeys, and the PRD deliberately references rather than restates them. Visual-detail "omissions"
are therefore expected and are NOT counted as gaps below. The findings below are UX-established
*behaviors*, *journey beats*, or *build-level accessibility items* that have no corresponding FR/NFR,
or that the PRD contradicts.

---

## Gap 1 — The "featured post" on Home has no FR; the recruiter journey's climax isn't enabled

**Severity: High** (a primary journey beat and a Success Metric depend on it)

The UX makes the featured BMAD post the hinge of the most important journey. EXPERIENCE Flow 1 step 3:
> "The featured post catches her: *'I built this site greenfield with the BMAD method.'* She clicks."

PRD UJ-1 mirrors this: "the **featured** *'I built this site with the BMAD method'* post catches her."

But **FR-2 only specifies recency**, not featuring:
> "Home shows up to 4–6 most-recent Posts as cards, newest first." (prd.md §4.1, FR-2 Consequences)

There is no FR that surfaces, pins, or features a specific post on Home. The recruiter journey (UJ-1)
and SM-3 ("the launch lands … Carrie is comfortable sending the link to … a recruiter") both assume the
BMAD post is reliably visible on Home. As written, that only holds while it happens to be among the 4–6
newest posts — once Carrie publishes ~6 more posts, the climax of UJ-1 silently breaks. Either FR-2
needs a "featured/pinned post" consequence, or the journey language ("featured") should be reconciled
to "happens to be recent at launch."

---

## Gap 2 — Posts list "filterable by tag" is dropped from FR-4

**Severity: Medium**

EXPERIENCE Information Architecture, surface #2:
> "**Posts `/posts/`** — full reverse-chronological list of every post; **filterable by tag**."

PRD FR-4 ("Browse all posts") specifies only:
> "`/posts/` lists all non-draft Posts, newest first. Each entry links to its Post Page and shows
> title, date, and Topic(s)." (prd.md §4.3, FR-4 Consequences)

The "filterable by tag" capability on the `/posts/` index is absent. Topic browsing exists separately
via Tag Pages (FR-8), so this may be a deliberate consolidation — but the UX named in-place filtering on
the Posts list specifically, and the PRD neither implements nor explicitly drops it. Worth confirming
whether `/posts/` filtering is intended, or whether Tag Pages fully cover the need (in which case the
UX line is the one that should be reconciled).

---

## Gap 3 — Accessibility semantics (single h1, heading order, landmark regions) missing from NFR-2

**Severity: Medium** (build-level requirement the UX explicitly flagged)

EXPERIENCE Accessibility Floor lists a "Semantics" requirement:
> "**Semantics:** one `h1` per page, logical heading order, landmark regions (header/main/footer)."

PRD NFR-2 enumerates contrast, meta-min color, focus ring, real controls/labels, image alt, and
`prefers-reduced-motion` — but **omits the semantics/landmark requirement entirely** (prd.md §9, NFR-2).
This is a concrete, testable build requirement (proper `h1`/heading hierarchy and header/main/footer
landmarks), not visual detail, and it's the kind of thing a downstream architecture/epics pass will skip
if it isn't in an NFR. Recommend adding it to NFR-2.

---

## Gap 4 — 404 page: UX defines a real surface with copy; PRD defers it with no FR

**Severity: Low** (acknowledged deferral, but the deferral is one-sided)

EXPERIENCE treats 404 as a first-class surface (IA #7, State Patterns, Voice and Tone) with mandated copy:
> IA #7: "**404** — friendly, on-brand 'this page wandered off' with a way home."
> Voice: "404: 'This page wandered off the fairway.'"

The PRD has **no FR for 404** and defers it (§6.2 "Custom 404 page … deferred"; Open Question 5). This is
explicitly acknowledged rather than silently dropped, so it is the weakest finding — but note the
asymmetry: the UX spine still lists 404 as a closed surface ("IA closes"), while the PRD ships without it.
If 404 is genuinely out of v1, the UX closure claim and the PRD should agree; otherwise a thin FR is
warranted since Hugo/Hextra makes a basic on-brand 404 nearly free.

---

## Gap 5 — Empty/near-empty states: defined in UX, present only as inline assumptions, no FR consequence binding

**Severity: Low**

EXPERIENCE State Patterns specifies two empty states as behaviors:
> "if a tag has no posts yet, show a light 'nothing here yet — check back' rather than a blank page.
> Home before many posts exist still feels intentional (hero carries it)."

The PRD captures both, but only as `[ASSUMPTION]`-tagged notes inside FR-2 and FR-8 consequences
(prd.md §4.1 and §4.4), and Open Question 5 lumps empty-state copy into "acceptable to ship without for
v1." So the empty-state behavior the UX *established as a pattern* is, in the PRD, simultaneously stated as
a testable consequence (FR-8: "An empty Tag Page shows a light 'check back' state") and flagged as possibly
shippable-without (Open Q5). That's a mild internal tension — confirm whether the empty Tag Page state is
in-scope (FR-8 says yes) or deferred (Open Q5 implies maybe not).

---

## Items checked and found consistent (not gaps)

- **Prev/next / back-to-posts** (EXPERIENCE IA #3) — covered by FR-5 ("a way back … and/or prev/next").
- **Search behavior + no-JS degradation** (EXPERIENCE) — covered by FR-9.
- **Theme toggle persistence, real control** (DESIGN/EXPERIENCE) — covered by FR-13 + NFR-2.
- **Gradient-clip wordmark solid fallback** (DESIGN Do's) — covered by FR-12.
- **Real controls not spans, focus ring, alt text, reduced-motion, meta-min #a0a0ab** — covered by NFR-2.
- **Lazy-load images, no web-font load, no-JS core reading** — covered by NFR-1.
- **Analytics deferral (GoatCounter/Cloudflare, not Mixpanel)** — covered by FR-14 + §8.
- **Light-mode AA re-verify** — covered by FR-13 assumption + Open Q3.
- **Mobile hamburger nav collapse** (EXPERIENCE) — Hextra-provided default; not a build gap.
