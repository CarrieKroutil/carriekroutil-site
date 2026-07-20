---
title: "AI Literacy for EMs"
weight: 10
description: "The working mental model of modern AI tooling that every engineering manager needs — enough to make good calls, ask sharp questions, and not get sold a story."
lastUpdated: 2026-07-19
goDeeper:
  - group: Books
    title: "Co-Intelligence — Ethan Mollick"
    url: "https://www.penguinrandomhouse.com/books/741805/co-intelligence-by-ethan-mollick/"
    why: "The best non-hype, non-doom orientation to what these tools are and how to actually work alongside them. Start here."
  - group: Courses
    title: "DeepLearning.AI — short courses"
    url: "https://www.deeplearning.ai/"
    why: "Free, hands-on, no-fluff courses on LLMs, prompting, and agents. The fastest way to turn vague intuition into real understanding."
  - group: Tools
    title: "Anthropic documentation"
    url: "https://docs.anthropic.com/"
    why: "Read how a frontier model actually works — context windows, tool use, prompting. Concepts here carry across every provider."
---

You don't need to train a model to lead engineers who use them — but you do need a working mental model, the kind that lets you smell a bad claim, scope a feature realistically, and ask the question that cuts through a demo. AI literacy for a manager isn't about prompt tricks; it's about understanding what these systems are good at, where they quietly fail, and what that means for how your team builds. It's the foundation under everything else in this section — whether your engineers are leaning on assistants in their [everyday workflow]({{< relref "/handbook/building-with-ai/ai-assisted-engineering.md" >}}) or putting a model at the [core of the product]({{< relref "/handbook/building-with-ai/ai-native-engineering.md" >}}). The goal here is to get you "dangerous enough to be useful" without drowning you in research papers.

*Part of the **Foundations** track — pair this manager's-eye view with [How LLMs Work]({{< relref "/handbook/building-with-ai/how-llms-work.md" >}}), the optional deep dive into how the machine really works.*

## What an LLM actually is (and isn't)

Strip away the mystique and a large language model (LLM) is a **next-word predictor**. It was trained on an enormous pile of text by playing one game billions of times: given some words, guess what comes next. Do that at massive scale and something surprising falls out — a system that can write, summarize, translate, and code, all as a side effect of getting very good at "what comes next."

That origin explains most of what you'll deal with as a manager:

- **It doesn't "know" facts — it models patterns.** When it's right, it's because true statements were the likeliest continuation. When it's wrong, it produces a false one with exactly the same confidence. This is *hallucination*, and it isn't a bug they'll patch away — it's the flip side of how the thing works.
- **It starts every conversation blank.** The model carries nothing between sessions. Everything it "remembers" is either pasted into the current conversation or stored in notes that get re-fed to it — a line [Claude Code 101]({{< relref "/handbook/building-with-ai/claude-code-101.md" >}}) draws precisely as *context vs. memory*.
- **It thinks inside a fixed window.** The *context window* is its working memory — the text it can see right now. It's finite, and when it fills up, earlier detail slips out of view and answers get worse.

{{< protip >}}
The literacy that's paid off most for me isn't technical — it's knowing the failure modes. A model will give you a fluent, plausible, completely wrong answer with total confidence. Once you internalize that, you stop asking "is the AI right?" and start asking "how would we know if it weren't?" — which is the question that actually protects your product.
{{< /protip >}}

## The vocabulary you'll keep hearing

You'll sit in rooms where these words fly around. You don't need to implement any of them — you need to know what they mean well enough to follow the conversation and call a bluff.

| Term | In plain words |
|------|----------------|
| **Token** | The chunk a model reads and writes in — roughly ¾ of a word. Cost and limits are counted in tokens. |
| **Context window** | How much text the model can hold in view at once. Bigger means it can consider more, but every turn costs more. |
| **Prompt** | What you send it. "Prompt engineering" is just being deliberate about that input. |
| **RAG** (retrieval-augmented generation) | Fetch the relevant documents first, paste them into the prompt, then answer — how you get a model to reason over *your* data, not just its training. |
| **Tool use / function calling** | Letting the model call real systems — search, a database, an API — instead of only producing text. |
| **Agent** | A model put in a loop that can take several steps and use tools to reach a goal, rather than answering once. |
| **Fine-tuning** | Further-training a base model on your own examples to bias its behavior. Powerful, costly, and rarely your first move. |
| **Evals** | Tests for a non-deterministic system — how you measure whether the model is doing the job, and whether a change made it better or worse. |

The one to burn in is the last. Evals are what separate teams who *know* their AI works from teams who *hope* it does — the through-line of [AI-Native Engineering]({{< relref "/handbook/building-with-ai/ai-native-engineering.md" >}}).

## Scoping AI work: capability, cost, latency

When a feature leans on a model, three dials replace the usual "how long will it take?":

- **Capability** — can the model do this *reliably*, or only in the demo? The gap between "works in a slick demo" and "works on the long tail of real inputs" is where most AI projects get into trouble.
- **Cost** — you pay per token, every turn, and the whole conversation is re-sent each time. Long contexts and chatty agents add up fast.
- **Latency** — bigger, smarter models are slower. Sometimes the right call is a smaller, faster model that's 90% as good.

Good scoping is mostly figuring out which of the three is the real constraint, then asking whether a cheaper, smaller, or simpler approach still clears the bar.

**In practice.** Say your team wants a "summarize this thread" button — a customer opens a long email or chat thread and clicks it for a three-sentence recap. Watch the three dials pull against each other:

- *Capability.* On a tidy 10-message thread, any model nails it. The trouble is the 200-message thread with forwarded attachments and three side conversations — does the summary still catch the one detail that mattered, like a promised refund?
- *Cost.* You feed the whole thread in on every click, and you pay per token. Thousands of clicks a day on huge threads adds up fast — so do you summarize everything, or cap it at the last 20 messages?
- *Latency.* The most capable model might take 8 seconds on a long thread, and 8 seconds on a button someone is waiting on feels broken. A smaller, faster model returns in 1 second and is 90% as good.

Here latency is probably the binding constraint — a slow button is a dead feature — so you reach for the faster model, check its capability still holds up on messy threads, and cap thread length to keep cost sane.

## Reading a capability claim without getting fooled

Benchmarks and demos are marketing until proven otherwise. A few questions to ask by default:

- **Ask about the long tail, not the demo.** "What's the failure rate on messy real inputs?" tells you more than any headline score.
- **Watch for cherry-picked benchmarks.** A model that tops one leaderboard may lag on the work you actually do.
- **Separate the model from the system around it.** Most production quality comes from the guardrails, retrieval, and evals *surrounding* the model call — not the model alone.
- **Demand the "how would we know if it were wrong?" answer.** If nobody can give it, the claim isn't ready to bet on.

**In practice.** A vendor demos a support agent that "resolves 95% of tickets automatically." You type three sample questions, it answers all three perfectly, and the room is sold. Ask these four questions to see if the claim holds up:

- *The long tail.* The demo questions were clean ("how do I reset my password?"). Your real queue is misspelled, multi-part, and angry. Ask for the resolution rate on a random sample of *last month's actual tickets*, not the three they picked.
- *Cherry-picked benchmarks.* "#1 on a support leaderboard" means little if that benchmark is mostly billing questions and you sell a developer tool. It measures the work they chose, not the work you do.
- *The model vs. the system.* That flawless refund probably wasn't the model — it was retrieval pulling the right help doc plus a guardrail capping refunds. Swap in the same model without that scaffolding and the quality collapses. They're selling the model; the demo was the system.
- *How would we know?* Ask what happens when the agent gives a confident, wrong answer to a customer. A real answer sounds like a confidence threshold, a human-review queue, or an eval that flags regressions. "It's very accurate, that rarely happens" is not an answer.

The through-line: a demo is built to show the model at its best; your job is to probe how it behaves at its worst.

## Where to go next

- **[How LLMs Work]({{< relref "/handbook/building-with-ai/how-llms-work.md" >}})** — go under the hood: a curated path from two friendly intros to a full Stanford course, for when you want to *really* understand the machine, not just the vocabulary.
- **[AI-Assisted Engineering]({{< relref "/handbook/building-with-ai/ai-assisted-engineering.md" >}})** — what changes when your team writes code with assistants every day.
- **[AI-Native Engineering]({{< relref "/handbook/building-with-ai/ai-native-engineering.md" >}})** — what changes when the model *is* the product, not a feature bolted on.
