---
title: "Internationalization (i18n)"
weight: 60
description: "Designing software so it can adapt to new languages, regions, and cultural conventions without a rewrite — the difference between 'we'll add languages later' and actually being able to."
lastUpdated: 2026-06-25
stub: true
goDeeper:
  - group: Tools
    title: "MDN — Localization"
    url: "https://developer.mozilla.org/en-US/docs/Mozilla/Localization"
    why: "Practical, code-level guidance on locales, language negotiation, and the browser APIs that make i18n work."
  - group: Tools
    title: "W3C Internationalization (i18n) Activity"
    url: "https://www.w3.org/International/"
    why: "The canonical source for the hard parts — text direction, character encoding, language tags, and why your assumptions about names and dates are wrong."
  - group: Books
    title: "The Unicode Standard"
    url: "https://home.unicode.org/"
    why: "Once you've been bitten by an encoding bug, you'll want to understand the system underneath every piece of text your software touches."
---

Internationalization — i18n, because there are eighteen letters between the *i* and the *n* — is the work of designing your software so it *can* adapt to other languages and regions, before you've actually translated a single string. It's the structural groundwork; localization (l10n) is the act of filling in a specific language. The reason this is a foundation and not an afterthought is brutal in practice: retrofitting i18n into a codebase that assumed English, left-to-right text, US dates, and dollar signs is one of the most expensive things a team can do. Get the bones right early and adding a language is a content task, not an engineering project.

When this page is filled in, it'll cover the usual traps: hard-coded strings, concatenated sentences that break under translation, assumptions about text direction (right-to-left languages like Arabic and Hebrew), date/number/currency formatting, character encoding (use UTF-8, always), and why you should never split a person's name into "first" and "last." Until then, the Go Deeper links carry it.

{{< protip >}}
The single highest-leverage habit: never build a sentence by gluing strings together in code. "You have " + count + " messages" is untranslatable — word order, pluralization, and grammar differ wildly across languages. Pass the whole sentence (with the variable inside it) to your i18n library and let it handle plurals and ordering. If your team adopts only one i18n rule, make it this one.
{{< /protip >}}
