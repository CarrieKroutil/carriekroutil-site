---
title: "AI Literacy for EMs"
weight: 10
description: "The working mental model of modern AI tooling that every engineering manager needs — enough to make good calls, ask sharp questions, and not get sold a story."
lastUpdated: 2026-06-25
stub: true
goDeeper:
  - group: Books
    title: "Co-Intelligence — Ethan Mollick"
    url: "https://www.penguinrandomhouse.com/books/740855/co-intelligence-by-ethan-mollick/"
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

You don't need to train a model to lead engineers who use them — but you do need a working mental model, the kind that lets you smell a bad claim, scope a feature realistically, and ask the question that cuts through a demo. AI literacy for a manager isn't about prompt tricks; it's about understanding what these systems are good at, where they quietly fail, and what that means for how your team builds. This page is a stub, but the goal is to get you to "dangerous enough to be useful" without drowning you in research papers.

What this will eventually cover:

- What an LLM actually is (and isn't) — context windows, tokens, why it confidently makes things up
- The vocabulary you'll keep hearing: prompting, RAG, agents, tool use, fine-tuning, evals
- How to reason about cost, latency, and capability when scoping AI-touched work
- Reading a capability claim or a benchmark without getting fooled

{{< protip >}}
The literacy that's paid off most for me isn't technical — it's knowing the failure modes. A model will give you a fluent, plausible, completely wrong answer with total confidence. Once you internalize that, you stop asking "is the AI right?" and start asking "how would we know if it weren't?" — which is the question that actually protects your product.
{{< /protip >}}
