---
title: "Delivery Metrics & Agile Foundations"
weight: 20
description: "DORA metrics and the Agile Manifesto — how to measure software delivery performance and the values underneath the way most teams work."
lastUpdated: 2026-06-25
goDeeper:
  - group: Books
    title: "Accelerate — Nicole Forsgren, Jez Humble, Gene Kim"
    url: "https://itrevolution.com/product/accelerate/"
    why: "The research behind DORA, with the evidence that delivery performance predicts business outcomes."
  - group: Tools
    title: "DORA — DevOps Research and Assessment"
    url: "https://dora.dev/"
    why: "The metrics, the annual State of DevOps report, and a free capability assessment."
  - group: Tools
    title: "The Agile Manifesto"
    url: "https://agilemanifesto.org/"
    why: "Four values and twelve principles, in the original two pages — read the source, not the consultancy version."
---

Two things shape how most engineering teams work: a set of metrics for measuring delivery, and a set of values for how to build software in the first place. DORA gives you the first; the Agile Manifesto gives you the second. Knowing both keeps you honest about whether a team is actually high-performing or just busy.

## DORA metrics

**DORA** (DevOps Research and Assessment) metrics are the industry-standard measures of software delivery performance. They tell you whether a team is fast *and* stable — and the research behind them shows those two things rise together rather than trading off.

| Metric | What it measures | Why it matters | Elite benchmark |
|--------------------------|------------------------------------------------------------|--------------------------------------------------------------|----------------------------------|
| **Deployment Frequency** | How often you deploy code to production | Higher frequency means faster value delivery | Multiple deploys per day |
| **Lead Time for Changes** | Time from commit to running in production | Shorter time means more agility, less waste | < 1 day |
| **Change Failure Rate** | % of deployments that degrade service | A lower rate means higher quality and confidence | < 15% |
| **Time to Restore Service** | Time to recover from a production failure | Fast recovery means resilience and user trust | < 1 hour |

The first two measure **velocity**; the last two measure **stability**. You want all four moving in the right direction, because optimizing speed at the cost of stability (or vice versa) is how teams fool themselves.

**Where to get the numbers:**

- **Deployment Frequency** — CI/CD pipeline metrics (GitHub Actions, GitLab CI, Azure DevOps).
- **Lead Time** — commit-to-deploy timestamps from PRs or pipelines.
- **Change Failure Rate** — tagged incidents, rollbacks, or hotfixes per deploy.
- **Time to Restore** — incident duration logs, alerts, and postmortem timelines.

Teams adopt DORA because it aligns engineering with business outcomes, supports data-driven improvement, and lets you benchmark across teams. Tooling that helps: version control and CI (GitHub, GitLab, Bitbucket, Azure DevOps, Jenkins, CircleCI), observability (New Relic, Datadog, Honeycomb), and incident management (PagerDuty, Opsgenie, Incident.io).

{{< protip >}}
DORA metrics are a thermometer, not a steering wheel. The moment you set them as targets — "everyone hit ten deploys a day this quarter" — people game them, and you learn nothing. I use them to spot where a team is stuck and to start a conversation, never as a number to beat. Goodhart's law is undefeated.
{{< /protip >}}

## The Agile Manifesto

The [Agile Manifesto](https://agilemanifesto.org/) is the foundational document of Agile software development, written in 2001 by 17 practitioners. It values individuals, working software, collaboration, and adaptability over rigid process.

> "We are uncovering better ways of developing software by doing it and helping others do it."

**Four core values** — preferring the items on the left, while still valuing those on the right:

| Prefer | Over |
|--------------------------------------------------|------------------------------------|
| **Individuals and interactions** | Processes and tools |
| **Working software** | Comprehensive documentation |
| **Customer collaboration** | Contract negotiation |
| **Responding to change** | Following a plan |

It's worth saying plainly: this doesn't mean the items on the right have no value — only that the items on the left are valued more.

**Twelve principles** behind the manifesto:

1. Satisfy the customer through early and continuous delivery of valuable software.
2. Welcome changing requirements, even late in development.
3. Deliver working software frequently — every couple of weeks to a couple of months.
4. Business people and developers work together daily throughout the project.
5. Build projects around motivated individuals; give them the environment and support they need.
6. Face-to-face conversation is the most efficient and effective way to convey information.
7. Working software is the primary measure of progress.
8. Agile processes promote sustainable development — a constant pace, indefinitely.
9. Continuous attention to technical excellence and good design enhances agility.
10. Simplicity — maximizing the work *not* done — is essential.
11. The best architectures, requirements, and designs emerge from self-organizing teams.
12. The team regularly reflects on how to become more effective, then adjusts.

For the source, see the [official Agile Manifesto](https://agilemanifesto.org/), the [principles behind it](https://agilemanifesto.org/principles.html), and its [history](https://agilemanifesto.org/history.html).
