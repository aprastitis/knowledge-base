---
type: engineering
status: active
date: 2026-07-01
summary: Hermes MoA (mixture-of-agents) presets are now exposed as virtual models in the routing layer, so an arbitrary N-way cross-provider ensemble addresses as a single model. +8%/+11% benchmark deltas (Opus 4.8 / GPT 5.5) on the upcoming HermesBench.
tags: [hermes, moa, mixture-of-agents, multi-model, routing, model-routing]
related:
  - engineering/2026-06-27-hermesbench
  - engineering/hermes/architecture
  - engineering/advisor-pattern-for-ai-agents
  - engineering/dispatch-routing-tiers
  - engineering/multi-agent-orchestration-patterns
  - engineering/async-subagents
sources:
  - https://x.com/NousResearch/status/2070610321278988385
  - https://x.com/NousResearch/status/2070846869849116796
  - https://x.com/NousResearch/status/2070893054739783921
  - https://github.com/NousResearch/hermes-agent/blob/main/tools/mixture_of_agents_tool.py
---

# MoA (Mixture-of-Agents) as Virtual Models

> Note: date-stamped filename uses 2026-07-01 (today's ingest); the underlying announcement + clarifications are from the 2026-06-27 / 06-28 tweet-scan window.

## What Changed (Jun 2026)

Hermes Agent's MoA presets are exposed as **virtual models** in the model routing layer. Before this change, MoA was an internal tool that the agent could call. Now a configured MoA preset — say `moa:frontier` — can be addressed the same way as any other model provider (`provider:model` syntax):

- `/model moa:frontier` in chat
- Direct API / config selection
- Cron jobs, kanban dispatch, subagents — anything that takes a model string

The presets are **provider-agnostic ensembles**. Teknium's clarifying reply and NousResearch's confirmation both state you can mix any N models from any providers — 5× Opus + 3× GPT + 1× Grok + 1× Gemini addresses as a single virtual model. Nous benchmarked one specific combination (Opus 4.8 + GPT 5.5 mixed ensemble) for the +8% / +11% deltas; the underlying MoA architecture is generic over any N models.

## The Underlying Architecture (already in code)

The MoA implementation lives in `tools/mixture_of_agents_tool.py` and uses a 2-layer architecture based on Wang et al. 2024 (arXiv:2406.04692):

1. **Reference layer (parallel).** A list of `REFERENCE_MODELS` each generate an independent response to the prompt. Defaults: Claude Opus 4.6, Gemini 2.5 Pro, GPT 5.4 Pro, DeepSeek V3.2. These run in parallel via OpenRouter.
2. **Aggregator layer.** A single `AGGREGATOR_MODEL` (default: Claude Opus 4.6) synthesizes the reference responses into one final answer, with a system prompt asking it to "critically evaluate" the inputs.
3. **Failure handling.** `MIN_SUCCESSFUL_REFERENCES = 1` — at least one reference must succeed before aggregation; failures are dropped, not retried.

### What the virtual-model layer adds

The June 2026 release wraps this 2-layer pipeline behind a `provider:model`-style handle so callers can treat the whole ensemble as if it were a single model. Operational consequences:

- No need to add MoA as a special tool call in prompts — just select the virtual model
- Routing / fallbacks / provider pools work uniformly
- The agent doesn't need to "remember" MoA exists; it just sees another model choice

## Numbers (vendor-reported, treat as directional)

- **+8%** over Opus 4.8 on the upcoming HermesBench
- **+11%** over GPT 5.5 on the upcoming HermesBench
- Scored on Nous's own benchmark suite (HermesBench leaderboard)
- Per Nous: numbers quoted were for one specific illustrative ensemble — the same architecture accepts any mix

## Why Nous Built Their Own (per @llmDestructor, 06-28)

> "Everyone is welcome to their own opinion. As the most prolific users of Hermes Agent we decided to create our own to get a better sense of which models work best with it."

Nous Research positions the MoA lineup partly as internal R&D — they're the heaviest Hermes user, so the "which models work best with Hermes" question is one they need answered for themselves first. Worth knowing because future MoA variants may bias toward what works well *with Hermes specifically*, not just generic benchmarks.

## When to Use MoA vs a Single Model

| Use case | Default | Try MoA when |
|---|---|---|
| Simple chat, lookup, edits | Single model | — |
| Hard reasoning / math / proof | Single strong model | Worth it: domain is the MoA sweet spot |
| Code generation on difficult problems | Single strong model | Worth it: heavy reasoning, parallel perspectives help |
| Cheap/frequent tasks (cron `wakeAgent` jobs, bot replies) | Single fast model | **Avoid** — MoA is multi-model-per-call, ~5–10× the cost |
| Latency-sensitive (interactive UI) | Single model | **Avoid** — MoA waits for all references before aggregation |

The cost-vs-quality trade is the central decision point. See [[engineering/dispatch-routing-tiers]] for the broader complexity-based routing pattern.

## Operational Notes

- The virtual-model abstraction is in the routing layer; the 2-layer MoA pipeline still runs underneath
- Cross-provider ensembles are explicit — pick anything with an OpenRouter-keyed handle
- Provider-side rate limits apply per reference; a single rate-limited provider doesn't fail the whole ensemble (drop and continue, per `MIN_SUCCESSFUL_REFERENCES`)
- For Hermes-internal use, MoA presets live alongside the provider selection in `/model`; they pick up credentials from your existing provider config

## Source Notes

- Primary announcement: Nous tweet 2026-06-27 — "+8%/+11% on the upcoming HermesBench, virtual model abstraction, provider-agnostic N-way ensembles, MoA presets are accessible as virtual models"
- Clarification 1: @jihoonhci 06-27 — "Yep you can use any combination of any number of models with the MoA, not just the one we benchmarked." (official-handle confirmation of N-way flexibility)
- Clarification 2: @llmDestructor 06-28 — motivation for custom MoA-tuned models (Nous builds them because they're the heaviest Hermes user)
- Architecture reference: `tools/mixture_of_agents_tool.py` in the Hermes repo — concrete defaults (REFERENCE_MODELS list, AGGREGATOR_MODEL, temperatures, failure semantics)

## See Also

- [[engineering/2026-06-27-hermesbench]] — The benchmark the +8%/+11% numbers are measured on
- [[engineering/hermes/architecture]] — Toolset list including `moa` (now exposed as virtual models)
- [[engineering/advisor-pattern-for-ai-agents]] — Smaller-executor + larger-advisor pattern; related but distinct from MoA (one call, asymmetric roles; MoA is symmetric ensemble)
- [[engineering/multi-agent-orchestration-patterns]] — MoA sits between "concurrent" and "group chat" patterns
- [[engineering/dispatch-routing-tiers]] — When MoA is worth its cost vs a single model
- [[engineering/async-subagents]] — MoA is intra-call parallelism; subagents are inter-call parallelism. Different axes.
