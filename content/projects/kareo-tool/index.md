---
title: "KareoTool"
date: 2021-04-17T12:00:00-07:00
description: "A C#/.NET tool that pulls data from Kareo's SOAP API into Excel to streamline a nonprofit's accounting — and the lesson that the best solution was the one I didn't build."
tags: [code, api, nonprofit]
links:
  - { label: "GitHub", url: "https://github.com/CarrieKroutil/KareoTool" }
  - { label: "Catchafire impact", url: "https://www.catchafire.org/impact/match/2614761/the-women-s-center-of-southeastern-michigan--technical-specification-development/" }
hero:
  src: hero.png
  alt: "Data flowing from an API endpoint along a pipeline into a clean spreadsheet grid"
draft: false
---

I volunteered through [Catchafire](https://www.catchafire.org/) on a *Technical Specification
Development* project for **The Women's Center of Southeastern Michigan** — a match with a total
impact of **$7,802**.

## The problem

The center's accounting workflow depended on data from **Kareo**, their practice-management
software, which then had to make its way into QuickBooks and Excel. As they grew their roster of
therapists and clients, that manual shuffle didn't scale. The goal: get the needed data into
Excel with the least possible effort and cost to automate and maintain.

## What I built

Kareo's old-school **SOAP API** can't be reached directly from Excel's Power Query, so I built a
custom **C#/.NET** exporter that calls the API and downloads each endpoint into clean Excel
spreadsheets. Configure your credentials and the endpoints you want in `App.config`, run the
executable, and the data lands in an `Output` folder ready for Power Query to pick up.

## The twist — and the real lesson

Mid-build, I discovered a *better* path: Kareo's **Custom Reports** feature, which hooks into
Excel's Power Query directly — no custom tool to construct and support long-term. The honest
takeaway is that the most maintainable solution was the one I *didn't* need to build. I wrote up
the whole journey (including everything I learned about Power Query and the M language) in the
[repo's README](https://github.com/CarrieKroutil/KareoTool) so the next person can skip straight
to the better answer.
