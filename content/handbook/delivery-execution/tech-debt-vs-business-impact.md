---
title: "Tech Debt vs. Business Impact"
weight: 40
description: "The perennial tension between paying down tech debt and shipping what the business wants — and how to make the trade in terms the business can actually weigh."
lastUpdated: 2026-06-25
stub: true
goDeeper:
  - group: Books
    title: "Managing Technical Debt — Kruchten, Nord, Ozkaya (SEI)"
    url: "https://www.oreilly.com/library/view/managing-technical-debt/9780135645932/"
    why: "The rigorous treatment from the SEI — what tech debt actually is, how to measure it, and how to decide when to pay it down."
  - group: Tools
    title: "TechnicalDebt — Martin Fowler"
    url: "https://martinfowler.com/bliki/TechnicalDebt.html"
    why: "Fowler's quadrant of deliberate/inadvertent and prudent/reckless debt — the vocabulary that makes the trade-off conversation honest."
  - group: Books
    title: "The Phoenix Project — Gene Kim, Kevin Behr, George Spafford"
    url: "https://itrevolution.com/product/the-phoenix-project/"
    why: "Dramatizes what happens when unpaid debt compounds until it eats the whole delivery system — a useful story to hand a skeptical stakeholder."
---

This is the trade-off that never goes away. Engineers feel the drag of the debt every day; the business feels the pull of the next feature. Frame it as "let us stop and clean up" versus "ship the thing customers are asking for" and engineering loses every time — because the cost of debt is invisible to everyone who isn't writing the code, while the upside of the feature is right there on the roadmap.

When this page is filled in, it'll cover how to translate tech debt into the language of business impact — slower delivery, more incidents, harder hiring — so it competes fairly on the roadmap instead of begging for scraps; how to tell prudent debt you took on purpose from the reckless kind that just accumulated; how to fold paydown into feature work so it isn't always a separate ask; and how to know when debt is cheap to carry versus when it's quietly strangling your velocity. Until then, the Go Deeper links carry the weight — Fowler's debt quadrant is the fastest way to make this a grown-up conversation.

{{< protip >}}
Never pitch tech debt as "cleanup." Pitch it as the thing standing between the business and what it wants: "every new feature in this area takes three times as long because of X — paying it down buys back that speed." Debt that's framed as a tax on future delivery gets funded. Debt framed as engineering hygiene gets deferred forever.
{{< /protip >}}
