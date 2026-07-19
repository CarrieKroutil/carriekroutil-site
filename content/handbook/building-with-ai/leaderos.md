---
title: "LeaderOS: Your Second Brain"
weight: 27
description: "Turn Claude Code into a durable thinking partner for the leadership job: how memory really works, the MCP servers worth connecting, and how to grow a folder of notes into a second brain that compounds."
lastUpdated: 2026-07-19
goDeeper:
  - group: Tools
    title: "Claude Code memory & CLAUDE.md"
    url: "https://code.claude.com/docs/en/memory"
    why: "The authoritative reference for CLAUDE.md and auto memory — read this when you want the exact mechanics behind the mental model below."
  - group: Tools
    title: "Connecting Claude Code to tools (MCP)"
    url: "https://code.claude.com/docs/en/mcp"
    why: "How to add MCP servers, what they can and can't touch, and the permission model. Start here before wiring Claude into anything with your real data."
  - group: Tools
    title: "Obsidian"
    url: "https://obsidian.md"
    why: "A free, local-first notes app built on plain markdown files — the natural next home for an AI brain once a single folder isn't enough."
  - group: Tools
    title: "Wispr Flow"
    url: "https://wisprflow.ai"
    why: "Voice-to-text that cleans rambling speech into polished prompts across any app. The fastest way to feed Claude the full context in your head instead of a typed-out fraction of it."
  - group: Books
    title: "Andrej Karpathy — The LLM Wiki"
    url: "https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f"
    why: "The short essay that names the idea: a knowledge base the LLM keeps tidy for you, so it compounds instead of rotting. The 'why' behind this whole page."
---

[Claude Code 101]({{< relref "/handbook/building-with-ai/claude-code-101.md" >}}) got you installed and pairing on a repo. This page is about the shift that happens next: Claude Code stops being a coding assistant you visit and becomes a **thinking partner that remembers** — one you can point at your tools and grow into a personal knowledge base that gets more useful every week. Call it your **LeaderOS**.

Most of what follows isn't really about writing code. It's about the thing leaders quietly struggle with most: **you can't hold everything in your head.** The context on every project, the goals and KPIs you're accountable for, every decision and why you made it — it doesn't fit. This page is how you put some of that load into a system you can *talk to*, instead of carrying it all yourself and hoping nothing important slips.

## Memory: how Claude actually remembers

There are two kinds of memory, and knowing the difference is the whole game.

**`CLAUDE.md` — project memory.** This is the file `/init` creates. It lives *in your repo*, you commit it, and everyone on the team (and every future session) inherits it. Think of it as the shared house rules: build commands, conventions, the directory that's off-limits. Covered in [101]({{< relref "/handbook/building-with-ai/claude-code-101.md" >}}) — nothing new here.

**Auto memory — your personal, private brain.** This is the interesting one. As you work, Claude builds up durable notes — who you are, what you're working on, decisions you've made, corrections you've given it. You don't manage a file. It files away some things on its own, and you lock in the rest by simply saying so: when a discussion wraps, tell it *"remember this,"* and it writes the takeaway down for next time. Under the hood it's plain markdown — one fact per file, plus a `MEMORY.md` that's just an index, a one-line pointer to each fact so the right ones can be found again. Nothing exotic; a tidy folder of notes.

Then, the next session, you don't reintroduce yourself — you pick up mid-thought. This is where it stops being a coding trick and becomes something a leader actually needs: **a place to offload the running state of the job.** The reorg you're halfway through. Who on the team is stretched thin and who's ready for more. Why you killed that project in Q1. The commitment you made to a peer team three weeks ago. Each of those is a note; together they're a kind of operating system for your attention — call it your **LeaderOS** — that you can think out loud with, and that quietly holds the threads so your head doesn't have to. You don't summon any of it; the relevant pieces surface on their own as they become useful, because the `MEMORY.md` index is read in at the start of every session in this folder.

That's worth being precise about, because it's easy to assume more than is true. The model itself doesn't carry anything between conversations — it starts each session blank. What persists is *the notes on disk*. So when it "knows" you're an engineering manager, that one of your reports is ready for a promo case, that a key project is at risk of missing its deadline, that's *recall of what got written down* — closer to a colleague reading your handoff doc than to a mind remembering. Which means two things follow: it only knows what was worth filing, and anything you never said stays unknown. That's exactly why the habit below is "brief it like a sharp new hire," and why the caution about secrets matters — what gets written down is what comes back.

The surprising part is **where those notes live**. They are *not* in your project folder. They're stored separately, under your home directory:

```text
~/.claude/projects/<your-project-path>/memory/
```

That location has three consequences worth understanding, because they're what make it trustworthy:

- **It's private and uncommitted.** Your personal notes never land in the repo by accident. What you say to Claude about a project stays on your machine, not in a pull request.
- **It survives the folder.** Delete the project directory and the memory stays put. The notes are keyed to the folder's *path*, not its contents.
- **It resumes if you come back.** Recreate a folder at the same path — re-clone the repo, restore the directory — and the old memory reconnects as if you never left. Same place, same brain.

{{< protip >}}
Because auto memory is private and path-keyed, the fastest way to start is to just *use it*. Talk to Claude about a project the way you'd brief a sharp new hire — the history, the politics, why the last approach failed. It remembers, and you stop re-explaining. But the same privacy cuts both ways: never paste real secrets (tokens, credentials, someone's comp) into a session, because it may get written down. Treat memory like a notebook you'd be fine handing to a trusted colleague — candid, but not a vault for secrets.
{{< /protip >}}

### The move most people miss: a folder that isn't code

Here's the unlock. `claude` doesn't need a codebase. Open it in an **empty folder** — call it `~/leader-os`, or one per area of your job — and start talking. Ask it to remember your team's org chart and the major projects your teams own and are accountable for. Think out loud about a reorg. Draft a hard message and have it push back. Dump the messy notes from a 1:1 and ask what patterns it's seeing across the last month. It's the same folder for the technical threads and the people ones — both are the job.

There's no repo, no build, no code. It's a **conversation partner with a memory**, and every session makes the next one sharper. That's the casual on-ramp to an AI brain: not a project, just a folder and the habit of thinking *with* something that keeps notes for you. You can reopen the notes anytime with `/memory`.

{{< protip >}}
The real friction in "just talk to it" is that you're actually *typing* — and nobody types out the full messy context in their head. A voice tool like [Wispr Flow](https://wisprflow.ai) closes that gap: you speak naturally into any app (the terminal included), and it cleans the rambling — filler words, false starts, half-sentences — into text worth sending. It's the difference between a terse one-liner and the full brain-dump you'd actually give a trusted deputy. Talk through a 1:1 on your walk back to your desk, let it tidy the transcript, and hand the whole thing to Claude to find the pattern. Speaking is faster than typing *and* it surfaces more, because you stop self-editing.
{{< /protip >}}

## MCP: the connector that feeds your LeaderOS

So far your LeaderOS only knows what *you* told it. **MCP** (the Model Context Protocol) is how it learns everything else — the standard way to give Claude reach into the tools you already live in: your GitHub issues, your Slack, a Jira ticket, a Confluence spec, a database. One sentence: *MCP servers are adapters that connect Claude to a system you already use.*

You don't need many. A few that earn their place for a leader:

| Server | What it unlocks |
|--------|-----------------|
| **GitHub** | Read issues and PRs, summarize what shipped this week, draft a release note from the actual merge history. |
| **Slack** | Search and summarize channels — "what did the platform team decide about the migration?" without scrolling for an hour. |
| **Atlassian (Jira / Confluence)** | Pull a ticket's full context, cross-reference a spec, turn a messy epic into a readable status. |
| **Google Drive** | Read and search your docs — briefs, strategy decks, meeting notes — so Claude reasons over *your* material, not the public internet. |
| **Filesystem** | Give Claude a specific folder outside the current project — handy for pointing it at that `~/leader-os` vault from anywhere. |
| **Web fetch / search** | Let it pull a live page or look something up, so answers aren't frozen at its training cutoff. |
| **Playwright (browser)** | Drive a real browser — check that a page renders, click through a flow, grab a screenshot. |

Here's where it clicks with your LeaderOS. Once a server is connected, you stop clicking through tabs and just *say what you want*: **"summarize what happened in this Slack thread,"** **"read the Confluence spec for Project Unblur and pull out the decisions,"** **"catch me up on #project-unblur-dev since Monday."** It goes, reads, and reports back — and because memory is sitting right there, you can close the loop: **"remember those three decisions and who owns each."** The connector brings the world in; memory makes the part that matters stick. Mention a project as it comes up, point Claude at the doc or channel to go learn the background, and over time it knows your landscape without you ever assembling a briefing.

You add a server with `claude mcp add` — one line in your terminal. For example, to add the Playwright browser server:

```bash
claude mcp add playwright -- npx @playwright/mcp@latest
```

The pattern is `claude mcp add <name> -- <command to launch it>`; the bit after `--` is whatever the server's docs tell you to run. Restart your session and the new tools are available to ask for in plain language. The [MCP docs](https://code.claude.com/docs/en/mcp) list the setup line for each server. The important part isn't the mechanics — it's the judgment.

{{< protip >}}
An MCP server runs with *your* access. Before you connect one, ask the question you'd ask about any integration: what can it read, what can it change, and whose data is that? Wiring in read-only GitHub is low-stakes. Handing Claude write access to production Slack or a customer database is a real decision — make it deliberately, prefer read-only where you can, and for a team rollout, agree on the approved list rather than letting everyone connect whatever they find.
{{< /protip >}}

## When LeaderOS outgrows the folder

Auto memory is the right place to start, but it isn't built to scale forever. That `MEMORY.md` index gets read into the model's context *in full* at the start of every session. That's exactly what makes recall feel effortless when you're tracking a few dozen notes across a handful of projects — the whole map loads, so the right thing surfaces on its own.

Keep feeding it, though, and that same strength becomes the ceiling. There's no hard cutoff where it suddenly breaks — but the longer that flat index gets, the more of every session's context it eats before you've typed a word: tokens spent re-loading the whole thing on every turn, and less room left for the model's attention on what you're actually asking. (Those tokens are only real money if you're on usage-based billing; on a subscription it's the attention tax that bites first.) It's a notebook, not a filing cabinet, and notebooks stop scaling right about when your job gets big enough to need one.

Your first LeaderOS is a great way to *start* — but you'll notice when it stops keeping up, and it shows up as symptoms, not a number. Claude starts surfacing the wrong note, or misses one you know you captured. It leans on a decision you reversed months ago instead of the current one. You hesitate to add something because the folder's become a pile you can't see into yourself. When recall gets spotty or your own notes start contradicting each other, that's the signal — not that your first version failed, but that your notes have outgrown a single flat folder.

That's when you graduate to a **formal wiki**: the same notes, re-homed in a structure that's organized into folders, cross-linked, and *searched on demand* instead of loaded wholesale every session — so recall stays sharp as the collection grows. Think of it as **LeaderOS v2**. **[Obsidian](https://obsidian.md)** is one good way to get there — free, local-first, and *just markdown files on your disk*, the exact format Claude already reads and writes — but the idea matters more than the app: the point is to give your notes real structure and let the model do the filing.

### Why an LLM Wiki keeps getting more useful

Andrej Karpathy wrote a short piece, [*The LLM Wiki*](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f), that names why this is worth the effort. The usual way we point AI at our documents *rediscovers everything from scratch on every question* — nothing accumulates. A wiki is the opposite: a **persistent, compounding artifact** that "keeps getting richer with every source you add and every question you ask."

The reason people have always abandoned personal wikis is maintenance — the tedious bookkeeping of keeping pages linked, current, and free of contradictions. Karpathy's point, echoing Vannevar Bush's 1945 "Memex," is that *the LLM now does that part.* His model has three layers: **raw sources** you drop in and never edit; **the wiki itself** — summary pages, entity pages, concept pages, all cross-linked — which the model owns and maintains; and a **schema** (a `CLAUDE.md`-style file) that tells the model how your wiki is organized and how to keep it tidy. Day to day it's three moves: **ingest** a new source (the model reads it, talks the takeaways through with you, writes a summary, and updates the linked pages and the index), **query** it (it finds the relevant pages and answers with citations), and **lint** it now and then (hunt for contradictions, stale claims, and orphaned notes). You curate and ask; the model files, cross-references, and cleans up.

For a leader, that's the whole promise in one line: **a place your judgment accumulates instead of evaporating** — the context on your org, your decisions, your reading — kept tidy by the tool and ready to think alongside you whenever you open it.

### Building LeaderOS v2: your notes as a wiki

Getting there is less work than it sounds, because you're not starting from scratch — you already have a folder of memory files. The path:

1. **Install Obsidian and make a vault.** Download [Obsidian](https://obsidian.md), create a new vault (that's just a folder it manages), and you've got a local markdown knowledge base. Nothing to configure yet.
2. **Hand Claude the pattern.** Open the [LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) and paste its full text into a Claude session pointed at your vault. It's deliberately abstract — Karpathy's own instruction is to *"share it with your LLM agent and work together to instantiate a version that fits your needs."* So don't drop it in as-is; ask Claude to turn it into a concrete `CLAUDE.md` schema for your vault — your folders (Projects, People, Decisions, Reading), your conventions, your ingest/query/lint workflow.
3. **Migrate your v1 memory in.** Point Claude at your existing memory folder (`~/.claude/projects/<your-project-path>/memory/`) and ask it to read those one-fact files and the `MEMORY.md` index, then re-file them into the vault's structure as proper linked pages. Your flat list becomes an organized, cross-referenced starting corpus — v1's contents, v2's shape.
4. **Feed it going forward.** From here, *ingest* is the habit: drop a source into the vault — a doc, a set of notes, a page you saved — and tell Claude to process it. Anything that lands in the folder as markdown is fair game, so use whatever gets sources in with the least friction.

That's LeaderOS with room to grow: the same instinct as the folder you started with — think out loud, let the tool keep the notes — just a structure that won't tap out when the job gets big.

## Where to go next

So far this has been about using AI to *assist* the day-to-day — your engineering work and your leadership work alike. The bigger shift is what happens when AI moves from assistant to the center of how software gets built. That's **[AI-Native Engineering]({{< relref "/handbook/building-with-ai/ai-native-engineering.md" >}})** — how to reinvent the software development lifecycle (SDLC) around AI-led work, and what that changes for how you plan, staff, and lead a team.
