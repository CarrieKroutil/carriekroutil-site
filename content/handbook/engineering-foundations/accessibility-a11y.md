---
title: "Accessibility (a11y)"
weight: 70
description: "Building software that works for people with disabilities — and, it turns out, better for everyone. WCAG basics, semantic HTML, and why a11y is a baseline, not a feature."
lastUpdated: 2026-06-25
stub: true
goDeeper:
  - group: Tools
    title: "WCAG — Web Content Accessibility Guidelines (W3C)"
    url: "https://www.w3.org/WAI/standards-guidelines/wcag/"
    why: "The standard everyone references. Start with the four principles — perceivable, operable, understandable, robust — before drowning in the success criteria."
  - group: Tools
    title: "MDN — Accessibility"
    url: "https://developer.mozilla.org/en-US/docs/Web/Accessibility"
    why: "The hands-on, code-first companion to WCAG: semantic HTML, ARIA, keyboard navigation, and how to actually test."
  - group: Tools
    title: "The A11Y Project"
    url: "https://www.a11yproject.com/"
    why: "A friendly, checklist-driven entry point when WCAG feels like reading a legal document."
  - group: Books
    title: "Inclusive Components — Heydon Pickering"
    url: "https://inclusive-components.design/"
    why: "Pattern-by-pattern walkthroughs of building genuinely accessible UI components from scratch."
---

Accessibility — a11y, eleven letters between the *a* and the *y* — is about building software that people with disabilities can actually use: people who navigate by keyboard, rely on screen readers, can't perceive color differences, or need larger text. It matters because it's the right thing to do, because in many places it's the law, and because the techniques that make software accessible (clear structure, good contrast, real keyboard support) tend to make it better for *everyone*. [Captions]({{< relref "/handbook/engineering-foundations/video-localization.md" >}}) help people in loud rooms; high contrast helps people outdoors in sunlight. It sits alongside [internationalization]({{< relref "/handbook/engineering-foundations/internationalization-i18n.md" >}}) as the other foundation aimed at the same thing — software that reaches more people, not fewer. Accessibility is a baseline of competence, not a premium feature.

When this page is filled in, it'll cover the four WCAG principles (perceivable, operable, understandable, robust), why semantic HTML does most of the work for free, color contrast, keyboard navigation, alt text that's actually useful, and when ARIA helps versus when it hurts. Until then, the Go Deeper links carry it — the WCAG principles are the mental model worth internalizing first.

{{< protip >}}
Before reaching for any tool, try this: put your mouse down and navigate your app with only the Tab, Enter, and arrow keys. If you can't reach a control, or you lose track of where focus is, you've found a real bug that a meaningful slice of your users hit every single day. Most accessibility failures are caught not by audits but by people who simply tried to use the thing without a mouse.
{{< /protip >}}
