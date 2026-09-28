---
title: "Leading AI-Adopting Teams"
weight: 40
description: "Change management, trust, and team norms as your engineers adopt AI tools — moving the team forward without mandates, fear, or quiet erosion of craft."
lastUpdated: 2026-07-19
goDeeper:
  - group: Books
    title: "Co-Intelligence — Ethan Mollick"
    url: "https://www.penguinrandomhouse.com/books/741805/co-intelligence-by-ethan-mollick/"
    why: "Mollick's framing of working with AI as a collaborator, not a threat — useful language for the conversations you'll have with your team."
  - group: Books
    title: "One Useful Thing — Ethan Mollick's blog"
    url: "https://www.oneusefulthing.org/"
    why: "Ongoing, grounded writing on how AI is actually changing how people work — good source material for setting realistic team expectations."
  - group: Courses
    title: "DeepLearning.AI"
    url: "https://www.deeplearning.ai/"
    why: "Point skeptical or anxious engineers here — building real understanding is the fastest cure for both hype and fear."
---

Adopting AI tools is less a tooling rollout than a change-management problem wearing a tooling costume. Your team is somewhere on a spectrum from "already automating half their job" to quietly worried the tools are coming for theirs — and your job is to move the whole group forward with honesty rather than mandates or hype. Get the norms and the trust right and adoption takes care of itself; get them wrong and you'll see either reflexive resistance or a slow erosion of the craft you've worked to build. This is the people problem sitting at the center of everything else in this section — the least automatable part of the whole shift, and the part that decides whether the rest of it lands.

*The cross-cutting page — [AI Literacy]({{< relref "/handbook/building-with-ai/ai-literacy-for-ems.md" >}}) teaches the technology and [AI-Assisted]({{< relref "/handbook/building-with-ai/ai-assisted-engineering.md" >}}) and [AI-Native]({{< relref "/handbook/building-with-ai/ai-native-engineering.md" >}}) change the work; this one is about carrying people through that change.*

## Meet your team where they are

There's no single "the team" to roll a tool out to. In any group you're leading at least three different people at once, and a message that reassures one will land wrong on the others. Figure out who's who before you say anything:

| Where they are | What's really going on | What they need from you |
|---|---|---|
| **The enthusiasts** | Already automating half their work — sometimes running ahead of any norms you've set | Direction, not brakes: channel them into setting good patterns the rest of the team can follow |
| **The skeptics** | Often your highest quality bar, not dead weight — they've been sold hype before | A real problem to try it on, and evidence over evangelism; their scrutiny is an asset, not resistance |
| **The anxious** | Quietly wondering whether they're training their own replacement | Honesty about the "why," and proof the goal is capability, not headcount |

The mistake is treating all three the same. A blanket "everyone start using this" brakes your enthusiasts, insults your skeptics, and confirms the anxious ones' worst read of the situation. Talk to each where they actually are.

## Set the norms before the tools spread

Norms are cheapest to set early, before habits harden. Once a team has spent six months accepting generated code nobody quite understands, that *is* the culture — and it's far harder to walk back than it would have been to prevent. A few worth agreeing on out loud, before the tools are everywhere:

- **Ownership is non-negotiable.** You're accountable for code you commit as if you'd typed every character — if you can't explain it in [review]({{< relref "/handbook/team-health-operations/development-lifecycle.md" >}}), it doesn't ship. It's the one norm that does the most work, and the same line the [AI-Assisted]({{< relref "/handbook/building-with-ai/ai-assisted-engineering.md" >}}) page holds.
- **Review adapts to the volume.** When output triples, review becomes the bottleneck *and* the safeguard. Decide how it scales before it quietly stops keeping up.
- **Some things don't get delegated blind.** Security-sensitive paths, secrets, anything touching customer data — agree on where a human writes it, not just checks it.
- **Using the tools is encouraged; hiding it isn't.** You want people comparing notes on what actually works, not quietly going it alone and re-solving the same problems.

## Have the trust conversation

Under the tooling questions is a quieter one your engineers may not say out loud: *is this coming for my job?* Left unspoken, that fear doesn't disappear — it turns into foot-dragging, defensiveness, or people hiding how much they lean on the tools. Name it directly. The honest message is that the goal is to make people more capable, not more replaceable — and it only holds if it's backed by how you actually evaluate work, because engineers can smell a rollout that's secretly about headcount. Be honest about what you don't know, too; nobody trusts the leader who claims to have it all figured out. It's the same [direct, honest conversation]({{< relref "/handbook/people-leadership/communicating-effectively.md" >}}) you'd want in any hard moment.

## Avoid the two failure modes

Most botched adoptions fail in one of two opposite directions:

| Failure mode | What it looks like | The antidote |
|---|---|---|
| **Mandate-driven backlash** | Top-down "everyone must use AI," usage tracked as a KPI → resentment, gamed numbers, malicious compliance | Enable and invite, don't compel; let results and peers pull people in |
| **Unmanaged free-for-all** | No norms, review unchanged, "just use it" → quality erosion, security holes, review swamped | Set the norms above early; guide, don't abdicate |

Your job is the narrow path between them: enough structure that quality holds, enough freedom that people actually experiment. Lean too far either way and you get the failure waiting on that side.

## What it looks like: a team of eight

Say you're rolling this out to a team of eight. Two are already deep in Claude Code, three are curious but cautious, one is openly skeptical, and two haven't said a word — which often means the anxious ones. A rollout that works rarely opens with a mandate:

- **Make the enthusiasts your pilot.** Let the two who are already in it find what genuinely helps, and have them write down the norms they wish they'd had — so the patterns come from peers, not a policy memo.
- **Hand the skeptic a real problem.** Give them something annoying and ask them to try the tool on it, with a mandate to poke holes. If they find real ones, that's the norm-setting you needed; if they don't, they've convinced themselves better than you could have.
- **Have the job-security conversation with everyone**, before the two quiet ones feel singled out.
- **Name the review bar up front**, so volume never gets ahead of quality.

Nobody was forced; the pull did the work. That's the whole game — adoption you *invited* is durable in a way adoption you *ordered* never is.

{{< protip >}}
You can't lead an adoption you haven't lived. The fastest way to lose the room is to mandate a tool you've never opened — the skeptics will clock it in one meeting. Spend a week doing real work through [Claude Code]({{< relref "/handbook/building-with-ai/claude-code-101.md" >}}) (or whatever your team reaches for) *before* you set a single norm. You'll set better ones, and you'll have earned the standing to ask.
{{< /protip >}}

## Where to go next

This is where the arc closes — the human part is the hard part, and the least automatable. Two ways back in:

- **[AI Literacy for EMs]({{< relref "/handbook/building-with-ai/ai-literacy-for-ems.md" >}})** — the foundation the whole section is built on, if you skipped ahead.
- **[Claude Code 101]({{< relref "/handbook/building-with-ai/claude-code-101.md" >}})** — read the *why* but haven't put a tool in your own hands yet? Start here.
