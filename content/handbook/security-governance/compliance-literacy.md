---
title: "Compliance Literacy: SOC 2, ISO 27001, GDPR, CCPA & Cookie Compliance"
weight: 20
description: "A manager's plain-language map of the compliance regimes that shape how teams handle data and risk — SOC 2, ISO 27001, GDPR, CCPA — plus the cookie-compliance corner that quietly touches almost every website."
lastUpdated: 2026-06-25
stub: true
goDeeper:
  - group: Tools
    title: "AICPA — SOC 2 (Trust Services Criteria)"
    url: "https://www.aicpa-cima.com/topic/audit-assurance/audit-and-assurance-greater-than-soc-2"
    why: "The body behind SOC 2. Worth a skim so you understand what an auditor is actually attesting to before you're sitting in an audit."
  - group: Tools
    title: "ISO/IEC 27001 — overview and standard"
    url: "https://en.wikipedia.org/wiki/ISO/IEC_27001"
    why: "A readable overview of the international standard for information security management systems — the certification your enterprise customers may ask whether you hold."
  - group: Tools
    title: "GDPR — official text and guidance"
    url: "https://gdpr.eu/"
    why: "The EU privacy regulation that set the global baseline. The plain-language summaries here are far more readable than the legal text."
  - group: Tools
    title: "California CCPA — Attorney General's office"
    url: "https://oag.ca.gov/privacy/ccpa"
    why: "The authoritative source on California's consumer privacy law — the U.S. counterpart you're most likely to encounter."
---

*This page is a stub — I'm still filling it out. What's here is the honest skeleton.*

I'll be straight with you: compliance is the part of the job I find least fun and most necessary. It's easy to dismiss as someone else's problem until an enterprise deal stalls on a security questionnaire, or a privacy law you didn't know applied turns out to apply. You don't need to become a compliance officer. You do need to be literate enough to know which regime is which, what it asks of your team, and when to pull in the people who do this for a living. (The companion piece on the [security standards bodies]({{< relref "/handbook/security-governance/security-standards-orgs.md" >}}) — OWASP and NIST — covers the controls side of the same coin.)

This page will cover the four regimes that come up most, plus the cookie-compliance corner that quietly touches almost every website.

## The big four (in plain language)

- **SOC 2** — A U.S. auditing standard (from the AICPA) where an independent auditor attests that your controls around security, availability, and confidentiality actually do what you say. Enterprise buyers ask for the report. *Type I* is a point in time; *Type II* covers a period — usually what customers actually want.
- **ISO 27001** — An international *certification* for running an information security management system (ISMS). Where SOC 2 is an attestation report, ISO 27001 is a formal certification — and the one global customers tend to ask about.
- **GDPR** — The EU's data-protection regulation. It set the modern global baseline: lawful basis for processing, data-subject rights, breach notification, real penalties. It applies based on whose data you handle, not where you're headquartered.
- **CCPA** — California's consumer privacy law, the U.S. regime you're most likely to bump into. Different mechanics from GDPR, similar spirit: people get rights over their personal data.

## Cookie compliance

The corner of all this that sneaks up on engineering managers is **cookie compliance** — aligning the tracking cookies and similar technologies on your website with privacy laws like GDPR and CCPA. The short version: if your site sets non-essential tracking cookies, those laws generally expect you to handle consent and disclosure properly, which is where the consent banners everyone loves to hate come from.

There's a cleaner path that's easy to forget: **don't collect what you don't need.** This handbook itself runs on [GoatCounter](https://www.goatcounter.com/), a cookieless, privacy-friendly analytics tool — no tracking cookies, no consent banner to manage, far less compliance surface to reason about. Privacy-by-design is often less work than privacy-by-disclaimer.

{{< protip >}}
Before you design a consent flow, ask whether you can avoid the cookie entirely. Cookieless analytics, server-side aggregation, and "collect only what you need" eliminate whole categories of compliance work — and they're usually the more honest choice for your users, too.
{{< /protip >}}

## What I'll add here

The full version of this page will get into how to read a SOC 2 report without glazing over, what your team actually has to *do* to support an audit, and how to tell which privacy regime applies to a given product. For now: know the names, know roughly what each one wants, and know when to call your security and legal partners early rather than late.
