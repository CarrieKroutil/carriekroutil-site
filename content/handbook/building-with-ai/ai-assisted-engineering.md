---
title: "AI-Assisted Engineering"
weight: 20
description: "Coding assistants and agents in the everyday dev workflow — what changes, what doesn't, and how to help your team get real leverage instead of faster slop."
lastUpdated: 2026-06-25
stub: true
goDeeper:
  - group: Tools
    title: "Claude Code documentation"
    url: "https://docs.claude.com/en/docs/claude-code/overview"
    why: "An agentic coding tool that works in the terminal across your whole repo, not just a single file. The current high-water mark for AI-assisted dev."
  - group: Tools
    title: "GitHub Copilot documentation"
    url: "https://docs.github.com/en/copilot"
    why: "The most widely adopted in-editor assistant. Worth knowing well since much of your team is probably already using it."
  - group: Books
    title: "Simon Willison's blog"
    url: "https://simonwillison.net/"
    why: "The most clear-eyed running commentary on coding with LLMs — what genuinely works, written by someone who ships with these tools daily."
---

AI coding assistants are already in your team's editors and terminals whether you've blessed them or not. The interesting management question isn't "should we use them" — it's how to get durable leverage out of them without trading away the rigor that keeps a codebase alive. Used well, they collapse the boring parts of the job and free people for the thinking that matters; used carelessly, they generate plausible code nobody understands at a pace your review process can't absorb. This page is a stub; the aim is to help you steer toward the first outcome.

What this will eventually cover:

- Where assistants genuinely help (boilerplate, tests, unfamiliar APIs, exploration) vs. where they mislead
- Autocomplete vs. chat vs. agentic tools — and when each fits the work
- What "AI wrote it" does to code review, ownership, and the definition of done
- Measuring whether it's actually helping, not just feeling fast

{{< protip >}}
The trap I watch for is velocity that looks great and ages terribly. Generated code is easy to accept and hard to truly own. My rule for the team: you are accountable for code you commit exactly as if you'd typed every character — if you can't explain it in review, it doesn't ship. That single norm does more than any tooling policy.
{{< /protip >}}
