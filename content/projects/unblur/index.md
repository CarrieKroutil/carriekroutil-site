---
title: "Unblur"
date: 2026-04-04T12:00:00-07:00
description: "A multiplayer photo party game: a blurred Unsplash photo races into focus while everyone guesses from their phones. My first from-scratch build with Claude Code — a BMAD rebuild is coming next."
tags: [ai, code, game]
links:
  - { label: "Play the game", url: "https://unblur.carrie.dev/" }
hero:
  src: hero.png
  alt: "A photo caught mid-reveal — blurred on one side, snapping into focus on the other — ringed by floating guess and score bubbles"
draft: false
---

This was my first time building something from scratch with Claude Code — no spec, no plan, just an idea and a free weekend.

The idea: a multiplayer party game where a blurred photo slowly comes into focus and everyone races to guess what it is. The faster you call it, the more points you score.

## How it plays

- A **host** opens a room on a laptop or TV, picks a topic and number of rounds, and gets a 4-character room code.
- **Players** join from their phones with the code — no app to install.
- Each round, a blurred [Unsplash](https://unsplash.com) photo reveals itself over about 20 seconds, then stays up for 5 more. Everyone can guess the whole time, and points scale from 1000 (instant) down to 100 (last second).
- If everyone guesses correctly, the round ends early. Between rounds the host sees the answer, the round winners, and the leaderboard — then hits **Next Round**.
- After the final round, a scoreboard crowns the winner.

Players can refresh or drop their connection mid-game and rejoin without losing their place.

## How it's built

- **Next.js + TypeScript**, deployed on **Vercel**.
- Photos from the free **Unsplash API**.
- Real-time room state — players, scores, guess history — on **Upstash** (Redis), with a Lua script keeping the scoring honest.
- **Mixpanel** for analytics, so I could see how people actually played.

## What's next

I learned a *ton* shipping this first version — and I also felt every spot where "just start coding" got messy. So my next endeavor is to **rebuild Unblur with the BMAD method** — the same spec-first approach behind [this site](/projects/this-site/) and the [handbook](/projects/engineering-manager-handbook/). Stay tuned!
