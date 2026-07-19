---
title: "Claude Code 101"
weight: 25
description: "A practical on-ramp for Claude Code — install it, point it at an existing repo with /init, and learn the handful of commands that matter on day one."
lastUpdated: 2026-07-12
goDeeper:
  - group: Tools
    title: "Claude Code quickstart"
    url: "https://code.claude.com/docs/en/quickstart"
    why: "The official install-and-first-session walkthrough. Start here if anything below has drifted — the tool moves fast."
  - group: Tools
    title: "Claude Code memory & CLAUDE.md"
    url: "https://code.claude.com/docs/en/memory"
    why: "How /init, CLAUDE.md, and auto memory work — the mechanism that makes Claude actually understand your project."
  - group: Tools
    title: "Claude Code commands reference"
    url: "https://code.claude.com/docs/en/commands"
    why: "The full slash-command list. Worth a skim once the basics click, so you know what's there when you need it."
  - group: Courses
    title: "Claude Code 101"
    url: "https://anthropic.skilljar.com/claude-code-101"
    why: "Anthropic's free intro course — a guided, hands-on walkthrough for anyone who learns better by doing than by reading docs."
  - group: Courses
    title: "Claude Code Beginner to Pro: Agentic Coding for Developers"
    url: "https://www.udemy.com/course/learn-claude-code/"
    why: "A paid, project-based Udemy course that takes you from beginner to genuinely productive — you build a real Next.js app with Claude Code, start to finish. About ten hours all in, course plus tinkering, and a fast on-ramp if you'd rather learn by shipping something."
---

Claude Code is an agentic coding tool that runs in your terminal and works across a whole repository, not just the file you happen to have open. This page is the fast path from "installed nothing" to "productively pairing on an existing codebase." It's the practical companion to [AI-Assisted Engineering]({{< relref "/handbook/building-with-ai/ai-assisted-engineering.md" >}}) — that page is the *why* and the *when*; this one is the *how*.

## 1. Install it

The recommended install is the native script — it self-updates, so you're not chasing versions later.

**macOS, Linux, or WSL:**

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**Windows (PowerShell):**

```powershell
irm https://claude.ai/install.ps1 | iex
```

If you'd rather manage it through a package manager, Homebrew works too — just know it won't auto-update, so you'll upgrade it yourself:

```bash
brew install --cask claude-code
# later: brew upgrade claude-code
```

On native Windows, install [Git for Windows](https://git-scm.com/download/win) alongside it so Claude Code can use Bash; without it, it falls back to PowerShell.

## 2. Start it and log in

Run it from anywhere:

```bash
claude
```

The first session opens your browser to log in. You'll need one of:

- A **Claude subscription** (Pro, Max, Team, or Enterprise), or
- An **Anthropic Console account** with pre-paid API credits, or
- Access through a cloud provider (Amazon Bedrock, Google Cloud, Microsoft Foundry).

Credentials are stored locally and persist across sessions. If you ever need to switch accounts, run `/login` from inside a session.

{{< protip >}}
For a team rollout, decide the billing path *before* people install. A Max subscription is the simplest per-seat story for engineers who'll live in the tool; Console/API billing gives you central spend visibility but needs someone to own the credits. Sorting this out first saves a round of "why won't it log me in" tickets.
{{< /protip >}}

## 3. Point it at an existing repo with `/init`

This is the step people skip and then wonder why the results feel generic. Open a session **inside your project directory** and run:

```text
/init
```

`/init` reads through the codebase and generates a starter `CLAUDE.md` in the project root — a short markdown file capturing your build commands, how tests run, and the conventions it can infer. Claude reads that file at the start of *every* future session, so it stops re-discovering your project each time. If a `CLAUDE.md` already exists, `/init` suggests improvements rather than clobbering it.

Treat the generated file as a first draft. Skim it, fix anything it guessed wrong, and add the two or three house rules a new hire would need to know — the lint command you actually use, the directory that's off-limits, the pattern you always follow. That hand-editing is where most of the leverage comes from. You can reopen it any time with `/memory`.

{{< protip >}}
Commit `CLAUDE.md` to the repo. It's shared team context, not personal config — the same way a good README is. When someone tightens a convention in review, it belongs here too, so the next person (and the next session) inherits it automatically.
{{< /protip >}}

## 4. The commands worth knowing on day one

You don't need the whole reference to be productive. These are the ones that earn their keep immediately:

| Command | What it's for |
|---------|---------------|
| `/help` | The full command list. Type `/` alone to browse and filter. |
| `/init` | Generate or improve the project's `CLAUDE.md` (above). |
| `/memory` | Edit `CLAUDE.md` and toggle auto memory. |
| `/clear` | Start a fresh conversation when you switch tasks — keeps context clean and costs down. |
| `/model` | Switch the model for this session or as your new default. |
| `/context` | See how much of the context window you're using. |
| `/compact` | Summarize a long conversation to free up context without losing the thread. |
| `/exit` | Leave the session (`Ctrl+D` also works). |

Plan mode is worth a mention too: ask Claude to plan a change before it touches files, and you get a reviewable approach up front instead of a surprise diff. It's the single habit that most reliably keeps generated work inside the rails.

## 5. Context is not memory

One distinction is worth getting straight early, because it quietly shapes how well everything above works.

**Context** is the current conversation's working memory — everything Claude can "see" right now. It's finite, and the fuller it gets, the worse things go on two fronts. **Cost:** every new message re-sends the entire conversation, so a long session quietly burns more tokens with each turn — you pay for all that history over and over. **Accuracy:** a crowded window makes the model more likely to lose the thread and *hallucinate* — confidently inventing a detail instead of tracking what's actually there. That's why `/clear` between tasks and `/compact` on a long one aren't housekeeping — they keep answers both cheaper and sharper. Treat them as reflexes, not options: new task, fresh context.

**Memory** is the opposite instinct: the durable notes that persist *across* conversations — your `CLAUDE.md` and auto memory. That, you let *grow*.

So the rule of thumb is **prune context, cultivate memory** — keep each conversation tight and scoped, and let the long-term notes accumulate.

## Where to go next

Three good next steps:

- **[LeaderOS: Your Second Brain]({{< relref "/handbook/building-with-ai/leaderos.md" >}})** — grow that memory into a second brain for your whole job, not just code.
- **[AI-Native Engineering]({{< relref "/handbook/building-with-ai/ai-native-engineering.md" >}})** — how to reinvent the software development lifecycle (SDLC) around AI-led work.
- **[Leading AI-Adopting Teams]({{< relref "/handbook/building-with-ai/leading-ai-adopting-teams.md" >}})** — helping your whole team pick up these tools well.
