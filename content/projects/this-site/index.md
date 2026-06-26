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

At work we're adopting an AI-native approach and trialing the [BMAD method](/posts/building-in-public-with-bmad/) — a spec-first workflow — and this site was my first go at it. The full paper trail (brief, PRD, UX, architecture, epics and stories) lives in the repo's `_bmad-output` folder, and an AI coding agent ([Claude Code](https://claude.com/claude-code)) built the site one story at a time, with a retro after each completed epic. I wrote up how the method actually felt in that post — short version: front-loading the thinking made the building calm.

Overall, I was delighted by the experience — and now I find myself wanting to try it again on something more complicated.

## Under the hood

- **Hugo + a custom theme layer** over Hextra — a hand-built design system (gradient wordmark,
  dark-default theme, design tokens, accessible cards) rather than a stock template.
- **The content** — a post stream with topic pages and full-text search, an
  [Engineering Manager Handbook](/handbook/), and this projects section.
- **Continuous deploy** — every push to `main` builds and ships via **AWS Amplify**; the whole
  planning paper trail lives in the repo for anyone curious how it came together.

It's the home base everything else here links back to.
