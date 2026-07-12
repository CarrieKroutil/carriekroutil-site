---
title: "carriekroutil.com — this very site"
date: 2026-06-23T07:00:00-07:00
description: "A personal site and blog, planned spec-first with the BMAD method and built with an AI coding agent on Hugo, shipped continuously to AWS Amplify."
tags: [code, ai, bmad, hugo]
links:
  - { label: "Live site", url: "https://carriekroutil.com" }
  - { label: "GitHub", url: "https://github.com/CarrieKroutil/carriekroutil-site" }
hero:
  src: hero.png
  alt: "A gradient-glass browser window with a blinking cursor and floating spec cards"
draft: false
---

This site is itself a project — and one I built in public using agentic spec-driven development.

At work we're adopting an AI-native approach and trialing the [BMAD method](/posts/building-in-public-with-bmad/) — a spec-first workflow — and this site was my first go at it. The full paper trail (brief, PRD, UX, architecture, epics and stories) lives in the repo's `_bmad-output` folder, and an AI coding agent ([Claude Code](https://claude.com/claude-code)) built the site one story at a time, with a retro after each completed epic. I wrote up how the method actually felt in that post — short version: front-loading the thinking made the building calm. If you want to try the method yourself, there's a step-by-step [Getting Started with BMAD](/handbook/building-with-ai/getting-started-with-bmad/) in the handbook.

Overall, I was delighted by the experience — and now I find myself wanting to try it again on something more complicated.

## Under the hood

Most of the interesting work here was in the decisions, not the stack. A few I'm glad I made:

- **Reading works with JavaScript off.** The core experience is clean, static HTML — fast on a
  phone, resilient anywhere. Search is the one exception that earns its keep.
- **Tags are an open taxonomy.** Add a lowercase word to a post and the topic page, the chips,
  the sitemap, and the Home/About lists all wire themselves up — add one, get the rest for free.
- **Privacy-first analytics.** A lightweight counter (GoatCounter) tells me how many people read a
  page without setting cookies or tracking anyone — so there's no annoying cookie pop-up to dismiss.
- **The tooling enforces the standards.** Missing alt text or a bad date *fails the build*, so
  accessibility and correctness aren't left to good intentions.
- **One deploy pipeline, no drift.** A single AWS Amplify app builds from one committed build
  spec; publishing is just `git push` to `main`. No CMS, no console tinkering, one source of truth.
- **A custom design layer over Hextra.** Gradient wordmark, dark-default theme, design tokens,
  and accessible cards — shadowing the theme rather than forking it, so upgrades stay painless.

The whole planning paper trail lives in the repo, and if you're curious how it all fits together the
[README](https://github.com/CarrieKroutil/carriekroutil-site/blob/main/README.md) covers authoring and
local setup while [SETUP](https://github.com/CarrieKroutil/carriekroutil-site/blob/main/SETUP.md) covers
the deploy pipeline. It's the home base everything else here links back to.
