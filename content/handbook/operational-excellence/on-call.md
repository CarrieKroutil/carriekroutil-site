---
title: "On-Call"
weight: 10
description: "Designing on-call rotations that catch real problems without grinding people down — humane scheduling, sane escalation, and treating the pager load as a metric you're responsible for."
lastUpdated: 2026-06-25
stub: true
goDeeper:
  - group: Books
    title: "The Site Reliability Workbook — Google"
    url: "https://sre.google/books/"
    why: "Free online; the chapter on being on-call covers rotation design, toil budgets, and keeping the load humane."
  - group: Books
    title: "Seeking SRE — David N. Blank-Edelman (ed.)"
    url: "https://www.oreilly.com/library/view/seeking-sre/9781491978856/"
    why: "Practitioner essays on what on-call actually feels like and how different orgs make it sustainable."
  - group: Tools
    title: "PagerDuty Incident Response docs"
    url: "https://www.pagerduty.com/resources/learn/incident-response/"
    why: "Battle-tested guidance on on-call practices, handoffs, and escalation from a company that lives this."
---

On-call is where your operational values stop being abstract. You can say you care about your people all day long, but the rotation is the thing that either protects their evenings and weekends or quietly eats them. I treat on-call load as something I'm personally on the hook for — if my team is getting paged at 3am for things that aren't real, that's a problem I own, not one they should just tough out, and it usually traces back to [alerting that fires when it shouldn't]({{< relref "/handbook/operational-excellence/monitoring-alerting.md" >}}).

This topic is about building a rotation that catches the things that genuinely need a human, fast, while leaving people rested enough to do their day jobs and stay on the team. When a page does turn into a real outage, [incident response]({{< relref "/handbook/operational-excellence/incident-response.md" >}}) takes over from here.

What the full version will cover:

- Rotation design: how many people, how long a shift, primary vs. secondary, and follow-the-sun vs. compensated off-hours
- Escalation paths that page the right person and have a real backstop when the first responder is asleep
- Tracking pager load as a metric — and treating a noisy week as a bug to fix, not a fact of life
- Compensation, time off after a rough shift, and the handoff ritual that makes a new shift actually informed

{{< protip >}}
The single best on-call investment I've made is a standing rule: every page that fires gets a quick triage tag — real, noisy, or preventable. Five seconds per alert. At the end of the rotation, the noisy and preventable buckets become the next sprint's reliability work. Without that loop, on-call pain just accumulates and the only signal you get is people quitting.
{{< /protip >}}
