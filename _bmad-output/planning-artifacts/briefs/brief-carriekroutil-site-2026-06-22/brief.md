---
title: "Product Brief: carriekroutil.com"
status: final
created: 2026-06-22
updated: 2026-06-22
---

# Product Brief: carriekroutil.com

## Executive Summary

carriekroutil.com is Carrie Kroutil's personal home on the web — a place that says "here's the
whole human, come hang out" first, and "here's what I'm about, professionally" second. It pairs a
warm landing page about Carrie with an easy-to-grow stream of short posts spanning her real
interests: golf, dogs, code, AI, leadership, and trips. The mix is the point — it's a person, not a
niche.

The thing that makes this sustainable is friction-free publishing: Carrie writes a markdown file,
pushes it, and it's live. No CMS, no login, no ceremony. The site is a greenfield static build
(Hugo → AWS static hosting) on a single, boring, reliable deploy pipeline — chosen so that "I just
wrote a post" reliably becomes "it's published" within minutes.

Why now: Carrie has already proven the mechanics work via a throwaway tutorial site, and she's
building this one greenfield using the BMAD method. That origin story *is* the launch content — the
first post teaches others how approachable it was to go from nothing to a live personal site, which
doubles as living proof-of-work for anyone (recruiters included) checking out what she can do.

## The Problem

Carrie doesn't have a single place online that is *hers* — one that shows the full person (hobbies,
opinions, range) rather than a one-dimensional professional profile, and that she can add to on a
whim without it becoming a chore.

- Social platforms fragment her across feeds she doesn't own and algorithms she doesn't control.
- A LinkedIn/resume presence shows the professional slice but not the human — and recruiters
  increasingly want to see range, judgment, and the ability to ship, not just a title list.
- Most "start a blog" paths add friction (CMS dashboards, editors, plugins) that quietly kills the
  habit. If posting isn't *trivially* easy, the posts stop.

The cost of the status quo is simple: the site that never gets updated. The goal here is the
opposite — a home base low-friction enough that publishing becomes a habit.

## The Solution

A fast, static personal website with two jobs:

1. **A landing/about page** that introduces Carrie as a whole person — approachable, real, with a
   clear thread to her professional self for those who want it.
2. **A post stream** across mixed topics, where each post is just a markdown file. Authoring loop:
   write markdown → push → live in minutes. Topics are organized lightly (e.g. tags/categories) so
   a visitor can pull the "golf" thread or the "AI" thread without the site pretending to be a
   single-topic blog.

The launch post is the meta one: *how I built this site greenfield with the BMAD method, and how
easy it actually was* — written to teach and to demonstrate.

Tech approach carries forward from what Carrie proved works: **Hugo** static-site generator,
markdown authoring, hosted as static files on **AWS**. Consolidated onto a *single* settled
pipeline: **AWS Amplify** builds from source on git push, with the domain on **Route 53**
(already configured — a quick win), retiring the current mix of half-configured S3/Amplify/GCP
paths. Theme: **Hextra** — clean, fast, built-in search and dark mode, actively maintained, and
comfortably handles the posts + about shape (its docs capability is unused for now but leaves
room if Carrie ever wants a guides section).

## What Makes This Different

This isn't competing with anything — it's Carrie's corner of the internet. The honest
differentiators are about *fit and sustainability*, not moat:

- **Whole-human positioning.** Deliberately mixes hobbies and professional topics, which is exactly
  what makes it memorable versus a sterile portfolio.
- **Friction as the design constraint.** The whole architecture is chosen to protect the posting
  habit. Easy-to-publish beats feature-rich.
- **Ownership.** Carrie owns the domain, the content, and the pipeline — no platform lock-in.
- **Proof by construction.** Built greenfield with BMAD and documented in public; the site is its
  own case study.

## Who This Serves

**Primary — curious humans.** Friends, peers, and people Carrie sends the link to, plus the
occasional stranger who lands on a post (e.g. googled a golf or AI thing). They want personality,
a good read, and a sense of who Carrie is. Success for them: "that was fun / useful, and I get who
she is."

**Secondary — recruiters & professional network.** People evaluating Carrie. They want signal:
range, judgment, communication, and evidence she ships. Success for them: "this person is
interesting, capable, and clearly builds things." The mixed-topic stream and the build-in-public
launch post serve this audience without the site feeling like a resume.

## Success Criteria

- **The habit holds.** Carrie actually keeps posting — a loose, no-pressure target of ~1–2 posts/
  month early on. (Cadence is the real test, not the number; adjust to taste.)
- **Publishing is trivial.** From "post written" to "live" is a single push and a few minutes, with
  no manual copy steps. If Carrie ever hesitates to post because of *process*, the design failed.
- **The launch lands.** The BMAD build-in-public post is published and shareable, and Carrie is
  comfortable sending the link to both a friend and a recruiter.
- **It loads fast and looks like her.** Static, quick, mobile-friendly, and reads as warm/personal
  rather than corporate-template.
- **Open decision (don't skip it):** the site should have *some* lightweight, privacy-friendly
  way to see whether anyone's reading. Tool is deliberately undecided — candidates: GoatCounter
  (free) or Cloudflare Web Analytics; Mixpanel is overkill for a static personal site. Not a
  launch gate, but a conscious "pick one soon" item, not an oversight.

## Scope

**In, for v1:**
- A landing/about page introducing Carrie (the human-first hello).
- A working post stream: list of posts + individual post pages.
- Light topic organization (tags or categories) covering the starting interests
  (golf, dogs, code, AI, leadership, trips).
- The markdown-write → push → live authoring loop on **one** settled deploy pipeline (Amplify).
- carriekroutil.com domain correctly wired (fresh branding — no SheBytes/code.shebytes.io remnants).
- Site code lives in the **`carriekroutil-site` repo** (the greenfield build moves here).
- The first real post: the BMAD greenfield build story.

**Explicitly out (for now):**
- Comments, accounts, or any user-generated content.
- A CMS / web admin editor (markdown-and-push is the whole point).
- Newsletter/email capture, heavy product analytics. (Site search comes free with Hextra.)
- Carrying over the throwaway quickstart content or the SheBytes branding.

## Vision

If it works, carriekroutil.com becomes Carrie's durable home base — the link she gives people that
shows all of her, growing a back-catalog of posts that compounds over time. The build-in-public
thread could grow into a small "how I make things" body of work (BMAD, AI tooling, shipping side
projects) that's genuinely useful to others and quietly persuasive to anyone evaluating her. The
ceiling isn't traffic — it's a site that's *still being updated in three years* because posting
never stopped being easy.

---

*Technical state, the throwaway prior-art details, and open build-time questions are preserved in
`addendum.md`.*
