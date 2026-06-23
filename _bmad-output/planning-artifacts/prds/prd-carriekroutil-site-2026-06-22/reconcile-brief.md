# Brief → PRD Reconciliation

**Inputs**
- PRD: `prd-carriekroutil-site-2026-06-22/prd.md`
- Brief: `brief-carriekroutil-site-2026-06-22/brief.md`
- Addendum: `brief-carriekroutil-site-2026-06-22/addendum.md`

**Verdict:** The PRD is a faithful, often improved restatement of the brief — vision, friction-free
publishing, whole-human positioning, ownership, non-goals, and the deferred-analytics intent all
survive cleanly. The gaps below are the substantive things the brief established that the PRD has
dropped, weakened, or quietly contradicted. None are catastrophic; #1 and #2 are the ones worth
fixing before downstream work.

---

## Gap 1 — The launch post's *teaching* intent collapses to "proof-of-work" (weakened)

The brief frames the BMAD launch post around two verbs, and **teaching comes first**:

> "the first post teaches others how approachable it was to go from nothing to a live personal
> site" … "written to teach and to demonstrate" … the build-in-public thread "could grow into a
> small 'how I make things' body of work … that's *genuinely useful to others*."

The PRD keeps "demonstrate" but **drops "teach."** It recasts the post as "proof by construction
for anyone (a recruiter, a peer) evaluating whether Carrie ships" (§1) and SM-3 reduces it to
"published and shareable … comfortable sending the link to both a friend and a recruiter." The
"approachable / how-easy-it-actually-was / useful to others" angle — the reason the post exists as
*content* and not just a credential — is gone. **Where covered:** §1, FR-5, SM-3 cover publishing
and signaling; nothing carries the "teach others it's approachable" intent. This matters because it
shapes how the post (and the future "how I make things" thread) should read — generous and
instructional, not just self-evidencing.

## Gap 2 — "The occasional stranger who googled a golf or AI thing" / discoverability (dropped)

The brief explicitly names an inbound-search audience:

> "plus the occasional stranger who lands on a post (e.g. googled a golf or AI thing)."

This is the *only* audience that arrives without already knowing Carrie, and it implies the site
should be findable (SEO / per-post discoverability). The PRD's UJ list (UJ-1 recruiter with resume
open, UJ-2 friend sent a link, UJ-3 Carrie publishing) covers only people who already have the
link. The stranger-via-search journey is absent, and there is no FR or NFR touching
discoverability/SEO/meta tags/sitemap. **Where covered:** §2.1 mentions "the occasional stranger"
in passing but assigns them no journey; no FR addresses how they'd ever find a post. Worth a
deliberate decision: either a lightweight SEO NFR, or an explicit non-goal ("not optimizing for
search discovery in v1") so it's a choice, not an omission.

## Gap 3 — "Boring, reliable" as an explicit pipeline value (weakened)

The brief twice editorializes the pipeline choice as a *value*, not just a fact:

> "a single, boring, reliable deploy pipeline" … "Consolidated onto a *single* settled pipeline."

"Boring/reliable" is the stated rationale — durability and predictability over cleverness. The PRD
captures *single* and *settled* well (FR-11, NFR-4) but loses the "boring on purpose" framing. NFR-4
talks about "low-maintenance and durable" which is adjacent, but the explicit anti-cleverness
posture — choose the dull, dependable option — isn't stated as a principle anyone downstream
(architecture) would read as binding. **Where covered:** FR-11, NFR-4 cover single/durable;
"boring by design" is implied but not named. Minor, but architecture decisions hinge on it.

## Gap 4 — "No ceremony" / emotional tone of the authoring loop (slightly weakened)

The brief's language around publishing is emotional and absolute:

> "pushes it, and it's live. No CMS, no login, **no ceremony**."

The PRD translates this precisely on the mechanical side ("no CMS, no login, and no manual copy
steps," FR-10, SM-2) — that part is fully preserved and even strengthened with SM-2's "Carrie never
hesitates to post because of process." The one nuance lost is "no ceremony," which is broader than
"no manual steps": it's about the *feeling* of weightlessness, no ritual or setup overhead. This is
largely covered in spirit by §1's "if posting isn't trivially easy, the posts stop" and SM-C2, so
this is a minor note, not a real hole.

## Gap 5 — FixIt theme fallback (dropped from decision record)

The addendum records an actionable contingency:

> "FixIt — actively-maintained LoveIt-lineage successor; **viable fallback if Hextra disappoints**."

The PRD commits hard to Hextra throughout (FR-9, FR-13, NFR-4) with no mention of a fallback, and
NFR-4 leans on "actively-maintained Hextra theme" as a durability guarantee. The documented
escape hatch if that assumption fails is gone. This is addendum-level detail and arguably belongs in
architecture, not the PRD — flagging it so it isn't lost entirely, not asserting the PRD must carry
it.

---

### Things checked and confirmed faithfully carried (not gaps)
- Whole-human positioning / "the mix is the point" → §1, FR-7, Non-Goals (resume).
- Friction-free publishing as defining constraint → §1, §4.6, SM-2, SM-C2 (well preserved).
- Ownership / no lock-in → NFR-5, Non-Goals.
- Deferred analytics as conscious "pick one soon," Mixpanel as overkill → FR-14, §6.2, Open Q1
  (the brief's "don't skip it" emotional weight is explicitly preserved in §6.2's PM note — good).
- SheBytes/quickstart cleanup, single repo, Route 53 → FR-11, addendum brownfield detail → Open Q2.
- "Ceiling isn't traffic … still being updated in three years" → SM-1, SM-C1, NFR-4 (preserved well).
