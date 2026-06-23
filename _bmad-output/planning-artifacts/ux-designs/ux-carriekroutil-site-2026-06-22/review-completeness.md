---
review: completeness
target: ux-carriekroutil-site-2026-06-22
reviewer: UX spec completeness reviewer
date: 2026-06-22
verdict: pass-with-minor-fixes
---

# UX Completeness Review — carriekroutil.com

Scope: completeness only (not visual/aesthetic judgment). Reviewed `DESIGN.md`,
`EXPERIENCE.md`, the upstream `brief.md`, and the three `.working/` mocks. Right-sized
to a low-stakes hobby personal site — gaps flagged are real omissions, not invented
enterprise requirements.

## Blocker

None. Both spines are structurally complete and internally coherent. The deliverable is
implementable as-is.

## Should-fix

### S1 — Brief's "pick an analytics tool" open decision dropped silently from both spines
The brief calls this out explicitly and tells you *not* to skip it: brief.md Success
Criteria, "Open decision (don't skip it): the site should have *some* lightweight,
privacy-friendly way to see whether anyone's reading … not a launch gate, but a conscious
'pick one soon' item, not an oversight" (brief.md:100–103). Neither DESIGN.md nor
EXPERIENCE.md mentions analytics anywhere. It is arguably a build/ops concern rather than
pure UX, but because the brief flagged it as a conscious open item, silently dropping it is
exactly the failure the brief warned against. Fix: add a one-line `[NOTE FOR UX]` /
`[ASSUMPTION]` in EXPERIENCE.md Foundation (e.g. "analytics: privacy-friendly counter
TBD — GoatCounter or Cloudflare; no UI surface, no consent banner needed") so it is carried
forward rather than lost. Low effort, closes the loop.

### S2 — Header nav: GitHub/LinkedIn count differs across spec and mocks
DESIGN.md `header-nav` and EXPERIENCE.md global nav both specify the header right-side as
"…search · theme toggle · GitHub · LinkedIn" (DESIGN.md:126, EXPERIENCE.md:40–41). The
post and about mocks include both GH and `in` icons (post-single.html:52, about.html:48),
but the home mock omits LinkedIn — only GitHub (`↗`) appears (home-direction-a.html:85).
Spines win on conflict, so the spec is the source of truth and a builder following it is
fine — but the canonical home mock contradicts it, which will read as ambiguous to whoever
implements. Fix: add LinkedIn to the home mock header, or note the home variant is
deliberately abbreviated. Trivial.

## Nice-to-have

### N1 — `[NOTE FOR UX]` amber-gradient contrast check is a real open task, not just prose
EXPERIENCE.md:98–99 leaves a genuine verification action open: confirm the amber end of any
gradient *text* meets contrast on dark at the size used. This is acceptable to leave
unresolved at spec time (it's a measure-at-build check, and the wordmark is large/bold so
likely passes), but it is an action item, not a closed decision. Acceptable to ship the
spec with it open; just make sure it survives into a build checklist so it isn't forgotten.

### N2 — Posts list surface (`/posts/`) has no mock and a thin spec
IA lists Posts `/posts/` as a distinct surface (EXPERIENCE.md:30) and Flow references "all
posts →", but there is no mock for the full list view and the spec only says
"reverse-chronological list … filterable by tag." For a hobby site this is fine — it's
essentially the home grid without the hero, and Hextra provides list templates — but it is
the one IA surface with neither a mock nor component-level detail. Optional: one sentence
clarifying it reuses `post-card` in a 1- or 2-up list.

### N3 — Tag-page and 404 surfaces are spec-only (acceptable)
Tag page (`/tags/{topic}/`) and 404 are in the IA but unmocked. Both are correctly scoped:
tag page reuses the post grid, and 404 has voice microcopy provided ("This page wandered off
the fairway." EXPERIENCE.md:58). No action needed; noting for completeness that they rely on
Hextra defaults + the stated reuse, which is the right call here.

## Looks good

- **DESIGN.md spine order complete and in canonical sequence**: Brand & Style (53),
  Colors (71), Typography (87), Layout & Spacing (97), Elevation & Depth (105), Shapes (111),
  Components (116), Do's and Don'ts (129). All eight present, correctly ordered.
- **DESIGN.md frontmatter tokens all present and coherent**: `colors`, `typography`,
  `rounded`, `spacing`, `components` (DESIGN.md:7–48). Component list (brand-wordmark,
  avatar-ring, topic-chip, post-card, header-nav, theme-toggle, footer-accent) matches the
  Components section prose 1:1. Color/typography/radius/spacing values in frontmatter match
  the prose roles and the mock CSS variables (e.g. `--radius:18px` = `rounded.card`,
  `max-width:920px` = `spacing.content-max`, accent hexes identical).
- **EXPERIENCE.md required sections all present**: Foundation (12), Information Architecture
  (25), Voice and Tone (47), Component Patterns (61), State Patterns (75), Interaction
  Primitives (87), Accessibility Floor (94), Key Flows (108).
- **Token cross-references valid**: the three `{token}` refs in EXPERIENCE.md all resolve
  against DESIGN.md frontmatter — `{colors.accent-gradient}` (→ DESIGN.md:17),
  `{rounded.card}` (→ DESIGN.md:34), `{colors.link}` (→ DESIGN.md:18). No dangling/invalid
  references. The illustrative examples in Foundation (line 20) use real token paths.
- **IA closure holds**: every brief need maps to a surface and every surface has a journey.
  Whole-human landing → Home; about/credibility → About + GitHub/LinkedIn + BMAD post;
  mixed post stream → Posts/Post; light topic org → Tag pages + chips; findability → Search;
  publishing loop → Flow 3. EXPERIENCE.md's own closure check (44–45) is accurate. The three
  Key Flows cover both brief audiences (recruiter = Flow 1, friend = Flow 2) plus the author
  publishing loop (Flow 3, the brief's central "friction-free publishing" constraint).
- **Remaining `[ASSUMPTION]` tags are all acceptable to leave**: light-mode surfaces
  deferred to Hextra (DESIGN.md:83), 404 page (EXPERIENCE.md:38), empty-state copy
  (EXPERIENCE.md:78). None block — each is a sensible defer-to-theme or low-stakes decision
  appropriate for v1.
- **Brief scope respected**: explicit out-of-scope items (comments, accounts, CMS,
  newsletter, SheBytes remnants) are not contradicted by either spine. Voice/tone section
  faithfully encodes brief positioning (whole-human, never-corporate, never self-deprecating).
- Pipeline/domain/repo concerns from the brief (Amplify, Route 53, `carriekroutil-site` repo)
  are build-time, correctly absent from UX spines — not silent drops.
