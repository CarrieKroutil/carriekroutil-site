---
title: "How LLMs Work"
weight: 15
description: "A curated path from two friendly intros to a full Stanford course — for when 'good enough to lead' isn't enough and you (or a curious engineer) want to really understand what's under the hood."
lastUpdated: 2026-07-19
goDeeper:
  - group: Books
    title: "The Illustrated Transformer — Jay Alammar"
    url: "https://jalammar.github.io/illustrated-transformer/"
    why: "The canonical visual explainer of attention and the transformer. Read it alongside Stanford Lecture 1 and the architecture clicks twice as fast."
  - group: Books
    title: "Build a Large Language Model (From Scratch) — Sebastian Raschka"
    url: "https://www.manning.com/books/build-a-large-language-model-from-scratch"
    why: "The hands-on book for the 'build a small project' instinct — code a working LLM end to end, one chapter at a time, in plain PyTorch."
  - group: Courses
    title: "Andrej Karpathy on YouTube"
    url: "https://www.youtube.com/@AndrejKarpathy"
    why: "The two intros on this page are his. The rest of the channel — including the famous '[1hr Talk] Intro to Large Language Models' — is the clearest LLM teaching anywhere."
  - group: Courses
    title: "Stanford CME 295 — course site"
    url: "https://cme295.stanford.edu/"
    why: "The home of the deep-dive course below: syllabus, slides, exams, and the readings behind every lecture. Skim the slides to preview or review without watching the full 1.75 hours."
  - group: Courses
    title: "3Blue1Brown — Neural Networks series"
    url: "https://www.youtube.com/playlist?list=PLZHQObOWTQDNU6R1_67000Dx_ZCJB-3pi"
    why: "A visual, math-light picture of what a transformer is doing inside — a gentle warm-up before the Stanford depth, and the best animation of 'attention' out there."
  - group: Tools
    title: "Hugging Face"
    url: "https://huggingface.co/learn"
    why: "Where open models and datasets live, plus a free hands-on LLM course — the natural place to actually run the small experiments this page keeps nudging you toward."
---

[AI Literacy for EMs]({{< relref "/handbook/building-with-ai/ai-literacy-for-ems.md" >}}) gives you the manager's mental model — enough to scope work, smell a bad claim, and lead a team that uses these tools. This page is the step down from there: *actually understanding how a large language model works under the hood.*

You don't need this to be a good manager. But the leaders who invest here stop treating the model as magic — they can reason about *why* it fails, follow their engineers into the details, and make sharper calls on [AI-native work]({{< relref "/handbook/building-with-ai/ai-native-engineering.md" >}}). It's also the single best thing to hand a curious engineer who wants to go deep. Fair warning: the full path below is real study — call it fifteen-plus hours end to end. You don't have to do all of it. The two intros alone will change how you think about these tools.

## Start gentle: two talks to build the intuition

Andrej Karpathy — a founding member of OpenAI and former head of AI at Tesla — is the clearest LLM teacher working today. Watch these two, in order:

- **[How I use LLMs](https://www.youtube.com/watch?v=EWvNQjAaOHw)** *(~2 hrs)* — the practical one. What these tools can actually do, feature by feature, with real examples. Start here; it's the gentlest on-ramp there is.
- **[Deep Dive into LLMs like ChatGPT](https://www.youtube.com/watch?v=7xTGNNLPyMI)** *(~3.5 hrs)* — the mechanics. How a model is trained, what tokens really are, why it hallucinates, and where its stranger behaviors come from. This is the "aha" one — after it, most AI news stops being mysterious.

If you only ever watch these two, you'll be ahead of most people talking confidently about AI in meetings.

## Go deep: Stanford's CME 295 course

For the real thing, **Stanford's CME 295 — Transformers & Large Language Models** (Autumn 2025, taught by Afshine and Shervine Amidi) is one of the strongest free LLM curricula going. Nine lectures, roughly 1.5–1.75 hours each, building from the transformer up to agents and evaluation. Watch them **in sequence** — each one leans on the last.

| # | Lecture | What it covers | Runtime |
|---|---------|----------------|---------|
| 1 | [Transformers](https://www.youtube.com/watch?v=Ub3GoFaUcds) | The architecture everything else is built on — attention, walked end to end | 1:42 |
| 2 | [Transformer Tricks](https://www.youtube.com/watch?v=yT84Y5zCnaA) | The practical techniques and variants that make transformers work at scale | 1:47 |
| 3 | [Large Language Models](https://www.youtube.com/watch?v=Q5baLehv5So) | How a transformer becomes the LLM you actually use | 1:49 |
| 4 | [LLM Training](https://www.youtube.com/watch?v=VlA_jt_3Qc4) | Pretraining, supervised fine-tuning, and LoRA | 1:47 |
| 5 | [LLM Tuning](https://www.youtube.com/watch?v=PmW_TMQ3l0I) | Aligning a model to human preference — RLHF, PPO, DPO | 1:48 |
| 6 | [LLM Reasoning](https://www.youtube.com/watch?v=k5Fh-UgTuCo) | How models learn to reason — RL scaling and GRPO | 1:47 |
| 7 | [Agentic LLMs](https://www.youtube.com/watch?v=h-7S6HNq0Vg) | RAG, tool calling, and agents — models that take actions | 1:49 |
| 8 | [LLM Evaluation](https://www.youtube.com/watch?v=8fNP4N46RRo) | How you actually know it works — LLM-as-a-judge and benchmarks | 1:49 |
| 9 | [Recap & Current Trends](https://www.youtube.com/watch?v=Q86qzJ1K1Ss) | Tying it together and where the field is heading | ~1:45 |

The full [playlist](https://www.youtube.com/playlist?list=PLoROMvodv4rOCXd21gf0CF4xr35yINeOy), plus the slides and readings, live on the [course site](https://cme295.stanford.edu/).

## How to actually get through it

Watching lectures back to back feels productive and teaches you almost nothing. What works:

- **Go in sequence, and don't rush.** Each lecture assumes the last. Spread them out over weeks, not a weekend.
- **Take notes in your own words.** If you can't restate a concept plainly, you haven't got it yet — the [same test]({{< relref "/handbook/building-with-ai/ai-literacy-for-ems.md" >}}) you'd apply to anything.
- **Recreate the simple examples** as you go, even just on paper.
- **Build one small project every few lectures** — a tiny script that calls a model, a toy retrieval over your own notes, a mini eval. The aim is to understand deeply enough to *build with it, evaluate it, and explain how it works*, and building is what makes it stick.

{{< protip >}}
You don't have to finish the course to get the payoff. Watch Karpathy's two talks, then pull single Stanford lectures on demand — the day evals come up, watch Lecture 8; the day someone pitches an agent, watch Lecture 7. Treated as a reference you dip into rather than a mountain you must climb, this whole page gets a lot less intimidating and a lot more useful.
{{< /protip >}}

## Where to go next

You've got the foundation — now put it to work:

- **[AI-Assisted Engineering]({{< relref "/handbook/building-with-ai/ai-assisted-engineering.md" >}})** — this understanding, applied in the everyday dev workflow.
- **[AI-Native Engineering]({{< relref "/handbook/building-with-ai/ai-native-engineering.md" >}})** — designing systems with the model at the core, where knowing the failure modes really pays off.
