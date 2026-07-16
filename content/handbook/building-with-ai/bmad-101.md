---
title: "BMAD 101"
weight: 35
description: "A practical on-ramp for BMAD — install it into a project, meet the agent roles, and walk the planning-to-implementation flow that turns 'build me a thing' into a spec you can trust."
lastUpdated: 2026-07-12
goDeeper:
  - group: Tools
    title: "BMAD-METHOD on GitHub"
    url: "https://github.com/bmad-code-org/BMAD-METHOD"
    why: "The canonical repo (org: bmad-code-org). Source of truth for the install command, module list, and release channels — check here first if anything below has drifted."
  - group: Tools
    title: "BMAD documentation"
    url: "https://docs.bmad-method.org"
    why: "The official docs — install guide, workflow map, and the agents reference. Where the current version's shape is defined."
  - group: Courses
    title: "BMAD Getting Started tutorial"
    url: "https://docs.bmad-method.org/tutorials/getting-started/"
    why: "A guided run from install to your first story, entirely in the IDE. The fastest way to feel the loop instead of just reading about it."
  - group: Courses
    title: "The Official BMAD-Method Masterclass"
    url: "https://www.youtube.com/watch?v=LorEJPrALcg"
    why: "The complete IDE workflow, end to end — the deep how-to companion once the idea has clicked. Filmed against one version, so cross-check the specifics against the docs."
  - group: Courses
    title: "Stop Vibe Coding: Spec-Driven Development with BMAD"
    url: "https://www.youtube.com/watch?v=3goqlnVyEXk"
    why: "The best *why-this-exists* framing on video — watch this first if you're still deciding whether the ceremony is worth it. Shows a specific version, so treat the exact steps as illustrative."
  - group: Tools
    title: "BMAD Discord community"
    url: "https://discord.gg/gk8jAdXWmj"
    why: "The method moves fast across versions. The community is where you'll find out what changed and how people are actually using it."
---

BMAD — the **Breakthrough Method for Agile AI-Driven Development** — is a spec-first way of working with an AI coding agent. Instead of "hey, build me a blog" and hoping, you move through a series of expert roles — analyst, PM, architect, UX, developer — and the AI plays each one *with* you, producing a documented artifact at every step. The planning is front-loaded so the building stays calm. This page gets you from nothing installed to your first story; it pairs naturally with [Claude Code 101]({{< relref "/handbook/building-with-ai/claude-code-101.md" >}}), which is the agent that does the building.

I built [this site](/projects/this-site/) and the [handbook you're reading](/projects/engineering-manager-handbook/) this way — the full paper trail (brief, PRD, UX, architecture, epics and stories) is public in each repo's `_bmad-output/` folder if you want to see the method in practice before you try it. For the fuller story of what that felt like, see [Building in public with BMAD](/posts/building-in-public-with-bmad/).

{{< protip >}}
The thing to internalize before you install anything: **BMAD isn't magic, and the agent isn't the one making the calls.** *You* decide what matters, where to cut scope, and when "good enough" is actually good enough. What BMAD does is force those decisions to happen — and get written down — *before* code exists, which is exactly when they're cheapest to change.
{{< /protip >}}

## 1. What you need first

- **Node.js 20.12 or newer** — the one hard requirement enforced at install.
- **Git** — used to pull in modules.
- **An AI coding agent** — [Claude Code]({{< relref "/handbook/building-with-ai/claude-code-101.md" >}}) or Cursor are the common choices. Get that working first; BMAD installs *into* the tool you already use.

## 2. Install it into your project

From the root of the project you want to work in:

```bash
npx bmad-method install
```

There's no global install to manage — you run it per project. The installer is interactive: it asks where to install (defaults to the current directory), which **modules** you want, and which IDE/agent to wire up (e.g. Claude Code). Accept the defaults your first time. It writes its configuration and the artifacts it generates into the project so everything lives alongside your code and gets committed with it.

To see every supported tool integration before you commit:

```bash
npx bmad-method install --list-tools
```

{{< protip >}}
Run BMAD inside a real git repo and commit the generated planning docs. The paper trail *is* the point — a PRD or architecture decision you can go back and read six months later is worth far more than the same reasoning trapped in a chat history you'll never scroll to again.
{{< /protip >}}

## 3. Meet the roles

BMAD gives you a set of named agent personas, each an expert collaborator for one part of the work. You invoke them one at a time:

| Role | Agent | What they're for |
|------|-------|------------------|
| **Analyst** | Mary | Brainstorming, research, pinning down what the thing is actually *for* |
| **Product Manager** | John | Turning that into real requirements — a PRD, epics, and stories |
| **Architect** | Winston | Choosing the (boring-on-purpose) stack and capturing decisions so nothing drifts |
| **UX Designer** | Sally | Palette, tokens, voice, the empty states |
| **Developer** | Amelia | Implementing one story at a time, plus code review and sprint planning |
| **Technical Writer** | Paige | Documentation, diagrams, explanations |

The magic isn't any single agent — it's that each one's output becomes the *context* for the next. The analyst's framing feeds the PM's requirements; the requirements feed the architect; and so on down to the developer, who finally writes code against a spec that traces all the way back.

## 4. The flow: plan, then build

BMAD splits work into two big movements — **planning** (heavy human-AI collaboration) and **implementation** (the agent executes, one story at a time). In practice it runs in a few phases:

1. **Analysis** *(optional)* — brainstorm and research to make sure you're solving the right problem.
2. **Planning** — create the PRD, and optionally a UX design. Every feature becomes a numbered requirement you can point at later.
3. **Solutioning** — design the architecture, then break everything into epics and small, testable stories. Run a readiness check before you build.
4. **Implementation** — do sprint planning, then loop per story: **create the story → implement it → review it**, with a retrospective after each epic.

A newcomer path: after install, ask BMAD's help/guide to inspect your project and recommend the next step. For small, well-understood work there's a **quick** track that clarifies, plans, implements, and reviews in a single pass — you don't need the full ceremony for a bug fix.

{{< protip >}}
**Start a fresh chat for each workflow.** It's the single habit that most reliably keeps BMAD working well — a clean context per phase stops the analyst's tangents from leaking into the architect's decisions. Same instinct as reaching for `/clear` in [Claude Code]({{< relref "/handbook/building-with-ai/claude-code-101.md" >}}): new task, new context.
{{< /protip >}}

## Why it's worth the ceremony

The upfront structure sounds heavy, but it pays for itself — the spec becomes the single source of truth, decisions get captured once and reused everywhere, and constraints quietly make the defaults good. I wrote about the three ways that played out while building this very site in [Building in public with BMAD](/posts/building-in-public-with-bmad/).

## Where to go next

BMAD is one concrete way to practice the discipline that [AI-Native Engineering]({{< relref "/handbook/building-with-ai/ai-native-engineering.md" >}}) is really about — treating the work as an engineering problem with artifacts and traceability, not a pile of clever prompts. And getting a whole team to adopt a method like this is its own [change-management challenge]({{< relref "/handbook/building-with-ai/leading-ai-adopting-teams.md" >}}).
