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

AI coding assistants are already in your team's editors and terminals whether you've blessed them or not. The interesting management question isn't "should we use them" — it's how to get durable leverage out of them without trading away the rigor that keeps a codebase alive. Used well, they collapse the boring parts of the job and free people for the thinking that matters; used carelessly, they generate plausible code nobody understands at a pace your review process can't absorb. This page is a stub; the aim is to help you steer toward the first outcome. The tooling is only half the story — getting a whole team to adopt these tools well is its own [change-management problem]({{< relref "/handbook/building-with-ai/leading-ai-adopting-teams.md" >}}).

## Getting started

If you want hands-on experience before forming opinions — and you should — pick one tool and actually ship something small with it. A few reasonable entry points:

- **[Claude Code]({{< relref "/handbook/building-with-ai/claude-code-101.md" >}})** — an agentic, terminal-based tool that works across your whole repo. Our step-by-step guide covers install, pointing it at an existing project with `/init`, and the handful of commands worth knowing on day one.
- **[GitHub Copilot](https://docs.github.com/en/copilot)** — the most widely adopted in-editor assistant; likely already in your team's IDEs, so worth knowing well.
- **Whatever your team already reaches for** — meet people where they are before standardizing on anything.

The tool matters less than the reps. Spend a week doing real work through one of these and the trade-offs below stop being abstract.

What this will eventually cover:

- Where assistants genuinely help (boilerplate, tests, unfamiliar APIs, exploration) vs. where they mislead
- Autocomplete vs. chat vs. agentic tools — and when each fits the work
- What "AI wrote it" does to code review, ownership, and the definition of done
- Measuring whether it's actually helping, not just feeling fast

{{< protip >}}
The trap I watch for is velocity that looks great and ages terribly. Generated code is easy to accept and hard to truly own. My rule for the team: you are accountable for code you commit exactly as if you'd typed every character — if you can't explain it in [review]({{< relref "/handbook/team-health-operations/development-lifecycle.md" >}}), it doesn't ship. That single norm does more than any tooling policy.
{{< /protip >}}
