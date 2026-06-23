# PRD Quality Review — carriekroutil.com

## Overall verdict

This is a genuinely good PRD for what it is: a low-stakes, solo, chain-top document with a clear thesis ("if posting isn't trivially easy, the posts stop") that actually drives scope, metrics, and non-goals. Done-ness clarity is strong — every FR carries testable consequences, and the few soft adjectives are deliberately bounded or pushed to the authoritative UX spine. The main residual risk is in two pipeline-cleanup FRs (FR-11, FR-2 count) whose "done" depends on manual out-of-band tasks tracked only in Open Questions, which the downstream epics/stories workflow should not silently inherit as build-enforced.

## Decision-readiness — strong

The PRD states decisions as decisions and names what was traded away. The friction-free-publishing thesis is asserted as a hard constraint, not a "consideration" (§1: *"if posting isn't trivially easy, the posts stop"*), and it visibly costs things: no CMS (§5), no comments, no analytics gate. Trade-offs are surfaced with the loser named — e.g. SM-C1 explicitly *"don't chase traffic... the ceiling is sustained publishing, not reach,"* and the analytics deferral is honest about being *"emotionally load-bearing"* rather than smoothed to neutral (§6.2 `[NOTE FOR PM]`).

Open Questions are actually open — analytics tool choice, legacy cleanup, light-mode AA, Home post count, and 404/empty-state copy (§8) are real undecideds with owners, not rhetorical. The `[NOTE FOR PM]` at §6.2 lands on a real tension (a deferred decision that could drift indefinitely), not a safe checkpoint.

One mild inconsistency: §8 Q4 lists "Home post count (N)" as still open, but FR-2 already specifies "4–6" and §10 says *"Post count confirmed: 4–6."* This is a resolved question left sitting in the open list — minor, but it slightly undercuts the "open means open" signal.

### Findings
- **low** Resolved question still listed as open (§8 Q4, vs. FR-2 / §10) — Home post count is "confirmed: 4–6" in the Assumptions Index but still appears in Open Questions as "confirm how many." *Fix:* Drop Q4 or restate it as a narrower residual (e.g. "confirm 4 vs. 6").

## Substance over theater — strong

Content is earned. The three personas (Dana/recruiter, Sam/friend, Carrie/author) each drive concrete FRs — Dana drives the featured-post + header-social behavior (FR-1, FR-3), Sam drives chip→tag-page wandering (FR-2, FR-8), Carrie drives the Authoring Loop (FR-10). No persona is decorative, and the count is within bounds. Non-Users (§2.2) does real work by ruling out interactivity and resume-shape, which the Non-Goals then enforce.

NFRs avoid boilerplate: they carry product-specific bounds (NFR-2 names `#a0a0ab` floor, `2px #d946ef` focus ring; NFR-1 names "no web-font network load, system font stacks"). The Vision is not swappable — it is specific to Carrie and to the durable-personal-home thesis, and even names the launch post as proof-by-construction. The "doubles as professional proof-of-work" framing is the one place that could tip into self-justification, but it's tied to a real artifact (the BMAD build-in-public post), so it reads as a stated bet rather than theater.

## Strategic coherence — strong

There is a single, legible thesis and the features serve it. Prioritization follows the thesis rather than ease: the Authoring Loop (FR-10/11) is named the "north-star capability," and the success metrics validate the thesis directly — SM-1 measures cadence/longevity, SM-2 measures publish friction, both explicitly tied to the posting habit rather than vanity activity. Counter-metrics exist and are pointed (SM-C1 against traffic-chasing, SM-C2 against feature creep that complicates authoring). The MVP scope kind is coherently an "experience + durable-capability" build, and the scope logic matches — search and analytics are pulled in or deferred based on whether they cost authoring friction, not on novelty.

This is the opposite of a backlog-with-headings: §4 features are visibly subordinate to §1's bet.

## Done-ness clarity — strong

This is the dimension the downstream epics/stories workflow leans on hardest, and it holds up. Every FR has a "Consequences (testable)" block with verifiable conditions: FR-1 enumerates the rendered elements and the no-JS requirement; FR-5 specifies draft exclusion from build output; FR-10 specifies the push→Amplify→reachable chain plus the negative test (*"No step... requires a CMS login, web editor, or manual file copy"*). Soft language is policed — "fast" in NFR-1 is bounded by concrete mechanisms (no web fonts, lazy-load, no-JS reading) and explicitly flagged as *"a guideline, not an SLA"*; "few minutes" in FR-10 is pinned to *"~= under 5 min, not a hard SLA."* Where bounds live in the UX spine (FR-12 accent rules, 720/920px widths), the PRD correctly points there rather than inventing duplicate adjectives — appropriate for chain-top with a finalized UX input.

The one place "done" is genuinely soft is FR-11. Its testable consequences include *"Legacy/duplicate deploy paths... are removed or inert,"* but the PRD then states cleanup is *"a manual one-time task Carrie performs (not build-enforced)."* So the FR's acceptance condition is real but unverifiable by the build — an engineer/story author can't write an automated check, and the actual "done" gate sits in §8 Q2 as a manual Carrie task. For a build-enforced feature this would be a problem; here it's honestly disclosed, but the downstream story for FR-11 needs to know its acceptance is a checklist verification, not a test.

### Findings
- **medium** FR-11 acceptance is manual, not buildable (§4.6 FR-11; §8 Q2) — "removed or inert so they cannot publish competing output" is a real condition but explicitly *not* build-enforced; the story author could mistake it for an automatable check. *Fix:* In the FR, state the acceptance method explicitly (manual verification that S3-sync Action / GCP App Engine artifacts are disabled), so the downstream story scopes a checklist task, not a test.

## Scope honesty — strong

Omissions are explicit and load-bearing. §5 Non-Goals does real work (no UGC, no CMS, no resume, no docs section "considered and dropped," no backend), and §6.2 Out-of-Scope distinguishes deferred-but-defined (analytics, light-mode tuning, 404) from never (comments/newsletter). De-scoping is done out loud, not silently — the 404 page is *"desired but deferred,"* not omitted. `[ASSUMPTION]` tags sit on genuine inferences (open tag taxonomy, read-time mechanism, "few minutes" meaning) and every one round-trips to the §10 index.

Open-items density (5 Open Questions + ~7 assumptions + 2 `[NOTE FOR PM]`) is high in absolute terms but entirely appropriate for a low-stakes hobby PRD where none of these block launch — and the PRD says so per item (e.g. FR-14 *"Carries no launch dependency"*). On a green-light-to-build commercial PRD this density would warrant scrutiny; here it reads as honest bookkeeping, not unfinished thinking.

## Downstream usability — adequate

This matters more than usual because the PRD is chain-top (feeds architecture and epics/stories). It mostly extracts cleanly: a Glossary (§3) is present and its nouns (Post, Post Stream, Topic, Tag, Tag Page, Wordmark, Authoring Loop, Deploy Pipeline) are used consistently across FRs, UJs, and SMs; FR/UJ/SM/NFR IDs are contiguous and unique; UJs each have a named protagonist carrying context inline; cross-refs ("see Open Questions," "see §8") resolve. A story author can pull any FR section and have its acceptance largely self-contained.

Two soft spots. First, "Tag" vs "Topic Chip" vs "Topic" are three glossary terms for adjacent concepts and the prose occasionally blurs them (FR-7 says tags "render as Topic Chips," FR-1 says "Topic Chips," FR-8 says "clicks a Topic Chip" — workable, but a story author must hold all three in mind). Second, several FRs' true done-state lives in the *UX spine* (FR-12, FR-13, layout widths) — correct and intended, but it means the architecture/stories workflow cannot source those bounds from the PRD alone; it must open the UX docs. The PRD flags this clearly, so it's a usability cost, not a defect.

### Findings
- **low** Three adjacent glossary terms (Topic / Tag / Topic Chip) (§3; FR-7, FR-8) — the distinction is correct but forces the reader to track which noun applies where. *Fix:* Consider a one-line "relationship" note in the Glossary (Topic = the thread; Tag = the front-matter field; Topic Chip = the rendered control), so downstream extraction is unambiguous.

## Shape fit — strong

The shape matches the product. This is a hobby/solo personal site with meaningful UX and a recruiter-facing dimension, so named-protagonist UJs are load-bearing — and they're present and used (FRs reference UJ-1/2/3 throughout). Rigor is appropriately light: no formal perf SLA, loose ~1–2 posts/month metric, assumptions accepted rather than litigated — all correct for the stakes. It is not over-formalized (no UJ bloat, no invented NFR thresholds beyond what the UX review actually produced) nor under-formalized (the durable-capability FRs that *do* matter — FR-10/11 — get full testable treatment). The decision to reference the finalized UX spine for tokens/IA/journeys rather than duplicate is the right call for chain-top and is explicitly justified in §0. No shape mismatch.

## Mechanical notes

- **Glossary drift:** Low. Domain nouns are used consistently in case and form. The only soft area is the Topic / Tag / Topic Chip cluster (see Downstream usability) — not drift so much as three legitimately distinct but easily-conflated terms.
- **ID continuity:** Clean. FR-1…FR-14 contiguous and unique; UJ-1…UJ-3; SM-1/2/3/4 + SM-C1/C2; NFR-1…NFR-5. No gaps or duplicates. Cross-references (FR→UJ, SM→FR/NFR) resolve.
- **Assumptions Index roundtrip:** Complete. Every inline `[ASSUMPTION]` (FR-2, FR-6 ×2, FR-7, FR-10, FR-13, §6.2 404, NFR-1) appears in §10, and §10 entries all trace back inline. Note: NFR-1's `[ASSUMPTION]` and §6.2's 404 assumption are both indexed correctly. One §10 entry adds resolved info ("Post count confirmed: 4–6") that conflicts with §8 Q4 still being open — flagged above.
- **UJ protagonist naming:** Each UJ names a protagonist (Dana, Sam, Carrie) and carries persona+context inline. No floating UJs.
- **Required sections:** All present for the stakes and type — Vision, Target User/JTBD, Glossary, Features/FRs, Non-Goals, MVP Scope, Success Metrics + counter-metrics, Open Questions, NFRs, Assumptions Index. §0 Document Purpose correctly orients downstream consumers to the two authoritative inputs.
