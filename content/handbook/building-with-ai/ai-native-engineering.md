---
title: "AI-Native Engineering"
weight: 30
description: "Designing products and systems with AI at the core — when the model is a first-class component, not a feature bolted on, and what that demands of the team."
lastUpdated: 2026-06-25
stub: true
goDeeper:
  - group: Tools
    title: "Anthropic — Building with Claude"
    url: "https://docs.anthropic.com/en/docs/build-with-claude/overview"
    why: "The patterns that matter when a model is load-bearing — tool use, structured outputs, agents, evals. Concepts transfer across providers."
  - group: Tools
    title: "OpenAI platform documentation"
    url: "https://platform.openai.com/docs/overview"
    why: "Worth reading alongside Anthropic's to see the shared shape of the problem space and where providers diverge."
  - group: Courses
    title: "DeepLearning.AI — agents and LLM app courses"
    url: "https://www.deeplearning.ai/"
    why: "Hands-on courses on building real LLM applications and agentic systems — RAG, evaluation, orchestration."
---

There's a difference between adding an AI feature and building an AI-native system. When the model is at the core of how your product works, the whole engineering discipline shifts: behavior is probabilistic, "correct" gets fuzzy, and your hardest problems move from logic to evaluation, prompts, context, and graceful failure. This is genuinely new territory for most teams, and it leans hard on the [failure modes]({{< relref "/handbook/building-with-ai/ai-literacy-for-ems.md" >}}) every manager needs to understand — hallucination, drift, the confident-but-wrong answer. It's easy to underestimate how much the surrounding system — guardrails, evals, observability — matters more than the model call itself. This page is a stub, but the through-line is that AI-native work rewards teams who treat the non-deterministic core as an engineering problem, not magic.

## Getting started

The fastest way to internalize "engineering problem, not magic" is to build something with a method that forces the discipline on you — traceable requirements, decisions captured once, code written against a spec instead of a vibe. A couple of ways in:

- **[Getting Started with BMAD]({{< relref "/handbook/building-with-ai/getting-started-with-bmad.md" >}})** — a spec-first, role-based workflow where an AI agent plays each role (analyst, PM, architect, developer) with you, producing a documented artifact at every step. Our guide covers install, the agent roles, and the planning-to-implementation flow. I built this handbook and site this way, so the whole paper trail is public if you want to see it in practice.
- **[GSD Core](https://github.com/open-gsd/gsd-core)** — a lighter-weight, spec-driven system that works with Claude Code and other coding agents. Instead of BMAD's role-by-role cast, it runs a repeating loop — discuss, plan, build, verify, ship — with far less ceremony, so it's a good fit when the full process feels like too much for the job in front of you.

Either way, the method supplies the discipline and [Claude Code]({{< relref "/handbook/building-with-ai/getting-started-with-claude-code.md" >}}) does the building — see [Getting Started with Claude Code]({{< relref "/handbook/building-with-ai/getting-started-with-claude-code.md" >}}) for that side.

What this will eventually cover:

- Designing for non-determinism: what changes when output isn't guaranteed
- Evals as a first-class discipline — how you know the system works and stays working
- Context, retrieval, and agents as [architecture]({{< relref "/handbook/engineering-foundations/software-architecture-design.md" >}}), not afterthoughts
- The new failure modes: hallucination, drift, prompt injection, runaway cost

{{< protip >}}
The teams that struggle with AI-native work are usually the ones who skipped building evals because "we'll just eyeball it." You can't eyeball a probabilistic system at scale. The moment we invested in a real evaluation harness — before chasing model upgrades — every other decision got easier, because for the first time we could actually tell whether a change made things better or worse.
{{< /protip >}}
