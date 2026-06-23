# Build-in-Public Capture — the Architecture phase

*Raw material for the launch post ("I built this site greenfield with the BMAD method"). Not a polished post — a quarry. Mine the beats, the surprises, and the quotable lines.*

---

## The one-line story

The architecture phase took a fully-specced personal site (brief + PRD + UX already done) and, in one coaching session, turned it into a **13-rule "spine"** — then sent three AI reviewers to attack that spine and found real holes *before a line of code existed.* The interesting part isn't the stack (it was basically decided already). It's how little needed deciding, and how the cheap stuff got caught early.

## Narrative beats (in order they happened)

1. **The stack was already a paved path.** Hugo + the Hextra theme, AWS Amplify build-from-source, Route 53. The brief and PRD had made every load-bearing platform call. So the architecture's job wasn't "choose technology" — it was "pin the handful of invariants that two future posts, or two layout files, could otherwise build incompatibly." That reframing is the whole lesson: *good upstream docs shrink the architecture to almost nothing.*

2. **The honesty check on the theme.** Hextra is primarily a **documentation** theme. The first real move was a research spike: *does a docs theme cleanly do a blog?* Answer: **~70% native** (post list, cards, search, dark mode, tag pages) and ~30% small custom work (read-time, a featured post, the home "latest posts" feed). Verdict: not a fight, just a few small partials — and the UX team had *already built the home mockup*, so the biggest custom piece was translation, not design.

3. **The "what does the home page give you for free" moment.** Easy to assume the theme auto-lists your latest posts on the homepage. It doesn't — Hextra's home is hand-authored cards. That gap became an explicit architecture decision instead of a surprise during the build. *The pattern: write down what the framework does NOT do, not just what it does.*

4. **Naming the highest-risk surface.** The single most divergence-prone thing on a markdown site is the **front-matter contract** — the exact field names every post must share. If post #1 writes `hero:` and post #40 writes `featured_image:`, the site quietly breaks. So the contract got pinned hard: YAML, named fields, build-fails-on-bad-date, build-fails-on-missing-alt-text.

5. **The adversarial review earned its keep.** Three AI reviewers ran in parallel against the finished spine: a rubric-checker, a tech-fact-checker, and an *adversary* told to find two posts that obey every rule yet still build incompatibly. It found **real holes** — the hero-image field was never actually named; "required alt text" had no mechanism that would *fail*; the slug source was ambiguous; two `featured: true` posts had no tiebreak. All cheap to fix on paper. All expensive to discover in month three.

6. **The fact-checker caught me being wrong.** It flagged that I'd claimed Hextra renders styled tag pages natively — it doesn't (Hugo generates them, but unstyled). And that a version number I'd written was stale. *An AI confidently stating something, then a second AI checking it against the live web and correcting it — that's the workflow, not a bug in it.*

## Surprises worth a paragraph

- **"Required" is a lie until something fails.** "Every image must have alt text" means nothing until there's a build hook that *errors* on an empty alt. The review forced the upgrade from a wish to a gate.
- **The boring decisions are the load-bearing ones.** Pin the Hugo version. Put custom files where they shadow the theme instead of editing the theme. One deploy pipeline, not three. None of these are clever — they're the difference between "still builds in three years" and "mystery breakage."
- **Deferring on purpose is a feature.** Analytics tool, light-mode color tuning, image optimization, custom 404 — all consciously pushed to "after launch" with a written reason. The spine is as much about what it *won't* decide as what it does.

## Quotable lines (steal/adapt)

- "The whole architecture exists to protect one habit: if posting isn't trivially easy, the posts stop."
- "Markdown-and-push *is* the product. A CMS would defeat the purpose."
- "I had three AIs attack the design before I wrote any code. The adversary found four holes. Each one would've been a real bug; each one was a one-line fix on a Tuesday."
- "Good planning docs don't make the architecture bigger — they make it almost disappear."
- "A docs theme doing a blog: 70% free, 30% a few small files, 0% fighting the tool."

## The numbers (for a 'by the numbers' sidebar)

- Inputs the architecture built on: **3** finalized docs (brief, PRD, UX) — zero re-litigated.
- Decisions explicitly deferred *to* architecture by the PRD: **5** — all resolved.
- Architecture rules (ADs) in the final spine: **13**.
- AI reviewers run against it: **3** (rubric, tech-currency, adversarial).
- Real holes found and closed before any code: **~6** (alt enforcement, hero field name, slug authority, featured tiebreak, card-renderer drift, a wrong "native" claim).
- Custom layout files the build actually needs: a small handful (`post-card`, `term.html`, a read-time bit, an alt-checking image hook) — the rest is config.

## Pointer for the post

The real spine lives in `ARCHITECTURE-SPINE.md` (same folder). If the post wants a "here's the actual artifact" screenshot, the dependency-direction diagram and the AD-3 front-matter contract are the two most legible, most relatable pieces.
