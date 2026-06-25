---
title: "Everyday Tips & Tricks"
weight: 20
description: "Small daily-driver wins across Chrome, Slack, VS Code, and git that quietly save you time every single day."
lastUpdated: 2026-06-25
goDeeper:
  - group: Tools
    title: "VS Code documentation"
    url: "https://code.visualstudio.com/docs"
    why: "Official docs — multi-cursor and shortcuts are just the start."
  - group: Books
    title: "Pro Git — Scott Chacon & Ben Straub"
    url: "https://git-scm.com/book"
    why: "The free, canonical reference for actually understanding git."
  - group: Tools
    title: "Raycast"
    url: "https://raycast.com"
    why: "Launcher and clipboard history that compounds the small-wins habit."
---

None of these are big ideas. They're the small, daily-driver wins — the ones that save you ten seconds a hundred times a day. Individually trivial; collectively, a different relationship with your tools.

## Custom Chrome search engines

Type less and find things faster by teaching Chrome a custom search shortcut.

**Setup:**

1. Click the three-dot menu in the upper-right corner of Chrome.
2. Open **Settings**.
3. Select **Search engine**.
4. Click **Manage search engines and site search** to expand it.
5. Next to **Site search**, click **Add**.
6. Fill in the fields — for example:
   - **Search engine:** Google Drive
   - **Shortcut:** `gd`
   - **URL with %s in place of query:** `https://drive.google.com/drive/search?q=%s`
7. Click **Add**.

**To use it:** click into the address bar, type the shortcut (`gd`) followed by a space or tab, type your search term, and hit Enter.

{{< protip >}}
Set one of these up for every tool you search more than a few times a day — Jira, your design system, your internal wiki. The address bar becomes a universal search box, and you stop loading a homepage just to use its search box.
{{< /protip >}}

## Searching emojis in Slack

In Slack's search bar, use the `hasmy:` keyword with an `:emoji:` to recall messages quickly. For example, `hasmy::pushpin:` pulls up every message I've added a 📌 to as a "do this later" marker. I use 👀 the same way for "needs more of my time."

You can do the same through the UI: run any search, then click the **Reactions** dropdown and pick a reaction to filter by.

To find messages with a given reaction from *anyone* (not just you), use `has:` instead — `has::eyes:` surfaces everything the team has flagged for a closer read.

## Routing Slack messages to a channel

With [Reacji Channeler](https://reacji-channeler.builtbyslack.com), you can configure Slack so that reacting to a message with a specific emoji automatically reposts it to a designated channel.

1. Set up the rule in Reacji Channeler, mapping an emoji to a target channel.
2. Use that emoji anywhere in Slack and watch the message get reposted.

One use case I like: create a channel that collects all the "sad" emojis, so you get a real-time feed of the dumpster fires across the company — useful signal you'd otherwise have to go hunting for.

## Use VS Code like a boss

Multi-cursor editing is the single biggest VS Code productivity unlock — once it's muscle memory, editing many lines at once feels normal. A few ways to get there:

**Mouse-based selection.** Hold `Alt` (Windows/Linux) or `Option` (Mac) and click on multiple lines to drop a cursor on each.

**Add cursors above/below.** Place your cursor, then hold `Ctrl + Alt` (Windows/Linux) or `Cmd + Option` (Mac) and press `Down` or `Up` to add cursors on consecutive lines.

**Select matching words.** Select a word, then press `Ctrl + D` (Windows/Linux) or `Cmd + D` (Mac) repeatedly to grab each next occurrence and edit them together.

**Select all matches at once.** Select a word, then press `Ctrl + Shift + L` (Windows/Linux) or `Cmd + Shift + L` (Mac) to select every matching occurrence in the file.

**From the Command Palette.** Open it with `Ctrl + Shift + P` (Windows/Linux) or `Cmd + Shift + P` (Mac) and search for **Insert Cursor Above** or **Insert Cursor Below**.

**Box (column) selection.** Hold `Alt` (Windows/Linux) or `Option` (Mac) and drag to create a vertical box selection, then type to edit every line in it.

## Git

You don't need to memorize git — but a good cheat sheet next to your keyboard pays for itself. I keep Julia Evans' one within reach: the [git cheat sheet PDF](https://wizardzines.com/git-cheat-sheet.pdf) from [Wizard Zines](https://wizardzines.com). When you want to actually understand what those commands are doing, the free [Pro Git book](https://git-scm.com/book) is the canonical reference.
