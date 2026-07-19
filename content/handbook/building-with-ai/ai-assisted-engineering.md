---
title: "AI-Assisted Engineering"
weight: 20
description: "Coding assistants and agents in the everyday dev workflow — what changes, what doesn't, and how to help your team get real leverage instead of faster slop."
lastUpdated: 2026-07-19
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

AI coding assistants are already in your team's editors and terminals whether you've blessed them or not. The interesting management question isn't "should we use them" — it's how to get durable leverage out of them without trading away the rigor that keeps a codebase alive. Used well, they collapse the boring parts of the job and free people for the thinking that matters; used carelessly, they generate plausible code nobody understands at a pace your review process can't absorb. The aim of this page is to help you steer toward the first outcome. The tooling is only half the story — getting a whole team to adopt these tools well is its own [change-management problem]({{< relref "/handbook/building-with-ai/leading-ai-adopting-teams.md" >}}).

*Part of the **AI-Assisted** track — this page is the mindset; the hands-on path runs [Claude Code 101]({{< relref "/handbook/building-with-ai/claude-code-101.md" >}}) → [LeaderOS]({{< relref "/handbook/building-with-ai/leaderos.md" >}}).*

## What it actually means

AI-assisted engineering is when engineers use AI tools to help with parts of the development process while the engineer still owns the decisions, design, validation, and final result. The one-line version:

> AI is a development partner that speeds up delivery, while human engineers stay responsible for architecture, judgment, quality, and outcomes.

It's broader than "ask the AI to write code." On any given day it might help an engineer:

- explore an unfamiliar codebase
- draft or refactor code
- generate tests
- debug an error
- explain a confusing log or a production incident
- review a pull request
- write documentation
- turn a rough idea into a technical plan or a Jira ticket
- research implementation options
- automate the repetitive parts of the job

The common thread: the engineer sets the goal, and the model does some of the legwork faster than a person could alone.

## A typical workflow

Whatever the task, the loop tends to look the same:

1. The engineer defines the problem and the constraints.
2. The AI proposes an approach or generates a first draft.
3. The engineer reviews it — correctness, security, maintainability, fit with the rest of the system.
4. Tests and validation confirm whether it actually works.
5. The engineer revises, or rejects the output and steers again.

That loop can run in seconds for a one-line change or over an afternoon for a big one, but the shape holds: human sets direction, AI drafts, human verifies.

## Who's in control

The clearest way to place AI-assisted engineering is on a spectrum of who's doing the work:

| Approach | Who does the work |
|---|---|
| **Traditional engineering** | The engineer performs nearly every step by hand. |
| **AI-assisted engineering** | AI speeds up selected steps; the engineer directs and verifies the work. |
| **[AI-native engineering]({{< relref "/handbook/building-with-ai/ai-native-engineering.md" >}})** | The process itself is designed around AI agents and automation, often letting AI complete larger, multi-step workflows. |

The line that matters is the middle one. In AI-assisted work the human is always holding the wheel — the model is a very fast pair of hands, not the driver.

## What it looks like: chasing down a production bug

Here's a real example of the whole loop in one sitting. A service started throwing 500s after a deploy. I worked it through Claude Code with a **Datadog MCP** connected — an MCP is just a standard connector that lets Claude Code read from an outside tool like Datadog directly, so it can pull real logs and traces instead of me copy-pasting them in. That let me stay in one place the whole way:

- I pointed it at the alert and asked it to pull the relevant error traces and find the pattern.
- It surfaced the spike, tied it to one endpoint, and traced the stack back into our code.
- I asked it to locate the offending function; it read the repo, found the bug — an unhandled null from a changed API response — and explained it.
- I had it draft the fix and a regression test, reviewed both closely, tightened the test, and opened the PR.

The whole thing took a fraction of the usual dig. But notice what I did at every step: I chose what to investigate, read its findings with a skeptical eye, confirmed the root cause myself, and owned the PR that shipped. The model compressed the grunt work; the judgment stayed mine.

**Why this is AI-assisted and not AI-native.** I was in the loop the entire time, driving one step at a time. It would tip into [AI-native]({{< relref "/handbook/building-with-ai/ai-native-engineering.md" >}}) only if we built the incident response *around* the model — say, an on-call agent designed to triage alerts, propose patches, and open PRs on its own as a standing part of the system, with people reviewing the output rather than directing each step. Same tool, different center of gravity: here it assists a person; there it's the architecture.

## Getting started

If you want hands-on experience before forming opinions — and you should — pick one tool and actually ship something small with it. A few reasonable entry points:

- **[Claude Code]({{< relref "/handbook/building-with-ai/claude-code-101.md" >}})** — an agentic, terminal-based tool that works across your whole repo. Our step-by-step guide covers install, pointing it at an existing project with `/init`, and the handful of commands worth knowing on day one.
- **[GitHub Copilot](https://docs.github.com/en/copilot)** — the most widely adopted in-editor assistant; likely already in your team's IDEs, so worth knowing well.
- **Whatever your team already reaches for** — meet people where they are before standardizing on anything.

The tool matters less than the reps. Spend a week doing real work through one of these and the trade-offs stop being abstract.

{{< protip >}}
The trap I watch for is velocity that looks great and ages terribly. Generated code is easy to accept and hard to truly own. My rule for the team: you are accountable for code you commit exactly as if you'd typed every character — if you can't explain it in [review]({{< relref "/handbook/team-health-operations/development-lifecycle.md" >}}), it doesn't ship. That single norm does more than any tooling policy.
{{< /protip >}}

## Where to go next

- **[Claude Code 101]({{< relref "/handbook/building-with-ai/claude-code-101.md" >}})** — the fastest way to get the hands-on reps this page keeps insisting on.
- **[LeaderOS: Your Second Brain]({{< relref "/handbook/building-with-ai/leaderos.md" >}})** — the same assistant, pointed at the leadership job instead of the code.
