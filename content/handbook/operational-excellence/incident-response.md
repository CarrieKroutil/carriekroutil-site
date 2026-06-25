---
title: "Incident Response"
weight: 30
description: "Responding to outages calmly and fast — clear roles, an incident commander who coordinates rather than fixes, and blameless postmortems that turn pain into durable learning."
lastUpdated: 2026-06-25
stub: true
goDeeper:
  - group: Books
    title: "The Site Reliability Workbook — Google"
    url: "https://sre.google/books/"
    why: "Free online; the incident-management and postmortem-culture chapters are the source material for blameless practice."
  - group: Tools
    title: "PagerDuty Incident Response documentation"
    url: "https://www.pagerduty.com/resources/learn/incident-response/"
    why: "An open, end-to-end guide to roles, severity levels, and running an incident — the closest thing to a free standard playbook."
  - group: Tools
    title: "Atlassian Incident Management Handbook"
    url: "https://www.atlassian.com/incident-management"
    why: "Practical templates and process for response, communication, and postmortems you can adapt to a small team."
---

How a team handles an incident tells you almost everything about its culture. The good ones are eerily calm — clear roles, one person coordinating, steady communication out to the people waiting on a fix. The bad ones are five engineers all typing into the same box at once while nobody knows who's actually in charge. The difference is rarely talent; it's that the calm teams decided how they'd respond *before* the pager went off.

This topic is about building that muscle: a response that resolves the outage fast, and a postmortem afterward that makes the next one less likely instead of finding someone to blame.

What the full version will cover:

- Roles in an incident — incident commander, communications lead, subject-matter experts — and why the commander coordinates rather than fixes
- Severity levels and when to declare, so people escalate without second-guessing themselves
- Keeping a timeline and communicating status to stakeholders while the fire is still burning
- Blameless postmortems: separating the human from the system, and turning findings into tracked action items that actually get done

{{< protip >}}
The phrase I open every postmortem with is some version of "we're here to fix the system, not the person." I mean it, and I prove it by going first — if I made a call during the incident that turned out wrong, I name it before anyone else has to. The moment people believe they won't be punished for telling the truth, you start getting the real story, and the real story is the only thing that prevents a repeat.
{{< /protip >}}
