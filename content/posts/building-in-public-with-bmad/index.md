---
title: "Building this site in public with the BMAD method"
date: 2026-06-23T12:00:00-07:00
tags: [code, ai]
draft: false
# Takes the single Home featured slot: newer date than the welcome post, so date-desc
# selection (Story 3.3, AD-7) makes this win and the welcome post falls into the recent grid.
featured: true
description: "I built this site with an AI coding agent — but the real story is the planning. Here's how the BMAD method made building it calm instead of chaotic."
hero:
  src: hero.svg
  alt: "A blueprint-grid banner in violet, fuchsia, and amber with stacked plan-to-build bars"
---

I built this site with an AI coding agent. That part you might have guessed. The part that
surprised _me_ was that the agent wrote almost no code until we'd done a lot of thinking — and
that's exactly why it went well.

The approach is called **BMAD**: a spec-first way of working where you break a project into
planning artifacts _before_ anyone touches a layout file. Instead of "hey, build me a blog"
and hoping, you move through a series of roles — and the AI plays each one with you.

## The flow I actually used

It went, roughly, like this:

- **Analyst** — pin down what this site is _for_. (A warm front door for a recruiter or a
  friend. Not a content farm.)
- **Product manager** — turn that into real requirements: every feature written as an `FR-N`
  I could point at later.
- **Architect** — decide the boring-on-purpose stack and capture the trade-offs as numbered
  decisions (`AD-n`) so nothing drifts three months from now.
- **UX designer** — the palette, the tokens, the voice, the empty states.
- **Epics & stories** — slice all of that into small, testable chunks, each one tracing back
  to a requirement.
- **Developer** — _now_ write code, one story at a time.
- **Code review** — a separate adversarial pass before anything merges.

By the time we were building, every story carried its receipts: this card, because of that
requirement, governed by this decision. When the agent and I disagreed, we argued about the
spec — not about the code. That's a much cheaper argument to have.

## Why it worked

A few things made the difference, and they're worth stealing:

**The spec was the single source of truth.** Functional requirement, capability, architecture
decision — they all lined up one-to-one. So "should the whole card be clickable?" wasn't a
vibe; it was a line I'd already written and could go read.

**Decisions were captured once and reused everywhere.** One post-card renderer shows up on the
home page, the posts list, _and_ the tag pages. I decided that once, early, and never re-litigated
it. Consistency for free.

**Constraints made the defaults good.** "Core reading works with JavaScript off" sounds like a
limitation. In practice it forced clean, static HTML and kept the whole thing fast on a phone.
The one exception — search — earns its keep.

## The honest part

This isn't magic, and the agent isn't the one making the calls. _I_ decided what mattered, where
to cut scope, and when "good enough" was actually good enough. What BMAD did was front-load the
thinking so the building felt calm — fewer surprises, fewer rewrites, more "oh nice, that just
worked."

The proof is the page you're reading. Same method, top to bottom. If you're curious about any one
piece — the tokens, the no-JS choices, the deploy pipeline — that's what the rest of this corner
of the internet is for.

And if you want to try BMAD yourself, I wrote a step-by-step [BMAD 101](/handbook/building-with-ai/bmad-101/)
in the handbook — install, the agent roles, and the plan-then-build loop.

More on the way. 👋
