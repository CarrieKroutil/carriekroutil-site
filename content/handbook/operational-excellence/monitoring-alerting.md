---
title: "Monitoring & Alerting"
weight: 20
description: "Telling signal from noise — SLOs that reflect what users actually feel, and alerts that only fire when a human needs to act."
lastUpdated: 2026-06-25
stub: true
goDeeper:
  - group: Books
    title: "Site Reliability Engineering — Google"
    url: "https://sre.google/books/"
    why: "Free online; the SLO and alerting chapters are where the \"alert on symptoms, not causes\" discipline comes from."
  - group: Books
    title: "Observability Engineering — Charity Majors, Liz Fong-Jones, George Miranda"
    url: "https://www.honeycomb.io/observability-engineering-oreilly-book-2022"
    why: "The case for high-cardinality, event-based observability over a wall of dashboards you never look at."
  - group: Tools
    title: "Prometheus — open-source monitoring & alerting"
    url: "https://prometheus.io/"
    why: "The de-facto open-source stack for metrics and alerting; its docs are a practical model for how to structure alert rules."
---

Monitoring is easy to do badly in a way that feels productive — you add a dashboard, you add an alert, you add another, and a year later your team has trained itself to ignore the pager because most of what it says is noise. The hard, valuable work is the opposite: deciding what's actually worth waking someone up for, and being ruthless about everything else. An alert that doesn't require a human to act is not an alert; it's a distraction with a sound effect.

This topic is about building monitoring that tells you the truth about what your users are experiencing, and alerting that earns the trust of the people who carry the pager — so that when a page does fire, it kicks off [incident response]({{< relref "/handbook/operational-excellence/incident-response.md" >}}) instead of getting ignored.

What the full version will cover:

- The difference between monitoring (what's happening) and alerting (what needs a human), and why conflating them creates noise
- Writing SLOs and error budgets that track what users feel, not just what's easy to measure
- Alerting on symptoms rather than causes, with runbooks attached so a page always points somewhere
- A regular alert review: every page should be actionable, or it gets tuned, routed, or deleted

{{< protip >}}
My test for any new alert: "If this fires at 3am, what does the on-call person do in the next five minutes?" If I can't answer that, it's not an alert — it's a dashboard panel or a ticket. That one question has killed more well-intentioned, sleep-destroying alerts than any policy I've ever written.
{{< /protip >}}
