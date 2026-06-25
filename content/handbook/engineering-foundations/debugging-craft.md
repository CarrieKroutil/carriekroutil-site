---
title: "Debugging Craft"
weight: 90
description: "Debugging is a learnable, systematic skill — not a personality trait or a flash of genius. How to reproduce, narrow down, and actually fix bugs instead of guessing."
lastUpdated: 2026-06-25
stub: true
goDeeper:
  - group: Books
    title: "Debugging — David J. Agans"
    url: "https://debuggingrules.com/"
    why: "Nine rules that apply to any system, software or not. 'Make it fail' and 'quit thinking and look' have saved me more times than I can count."
  - group: Books
    title: "The Pragmatic Programmer — Hunt & Thomas"
    url: "https://pragprog.com/titles/tpp20/the-pragmatic-programmer-20th-anniversary-edition/"
    why: "Debugging as a mindset — rubber-ducking, not assuming, and tracing problems to their root rather than patching symptoms."
  - group: Tools
    title: "Wizard Zines — Julia Evans"
    url: "https://wizardzines.com/"
    why: "Delightful, deeply practical zines on the tools underneath your bugs: networking, debugging, DNS, strace, and more."
---

Debugging is the most undertaught skill in software, and the most quietly career-defining. Bootcamps teach you to build; almost nobody teaches you to systematically find out why the thing you built doesn't work. The good news: debugging is a *craft*, learnable and repeatable, not a mystical talent some people are born with. The engineers who look like wizards aren't guessing faster than you — they're following a disciplined process of forming a hypothesis, checking it against reality, and narrowing the search space until the bug has nowhere to hide.

When this page is filled in, it'll cover the core loop: reproduce it reliably first (an intermittent bug you can't trigger is nearly impossible to fix), then narrow down by halving the search space, change one thing at a time, read the actual error instead of assuming, and verify the fix actually fixed *this* bug. Until then, the Go Deeper links carry it — Agans' *Debugging* is the one I'd hand to anyone starting out.

{{< protip >}}
When you're stuck, the most powerful question is not "what's wrong?" but "what do I *know* is true?" Most debugging dead-ends come from an unexamined assumption — the config is loaded, the function is even being called, the data is what you think it is. Stop theorizing and go look: add a log line, set a breakpoint, print the value. "Quit thinking and look" is rule three in Agans' book for a reason.
{{< /protip >}}
