---
type: engineering
status: active
date: 2026-07-01
summary: Nous Research's own internal benchmark suite, announced for upcoming full leaderboard; measures capabilities relevant to Hermes Agent (reasoning, coding, tool-use, multi-step). Already the basis for MoA +8%/+11% deltas.
tags: [hermes, hermesbench, benchmark, evaluation, evals]
related:
  - engineering/2026-06-27-hermes-moa-virtual-models
  - engineering/evals-ai-evaluation-frameworks
  - engineering/2026-06-19-engineering-agentic-loop-pattern
  - engineering/harness-engineering
sources:
  - https://x.com/NousResearch/status/2070610467421028812
---

# HermesBench

> Note: date-stamped filename uses 2026-07-01 (today's ingest); the underlying announcement is from the 2026-06-27 tweet-scan window.

## What It Is

HermesBench is Nous Research's own internal benchmark suite — designed to measure the capabilities that matter for an agent like Hermes Agent, rather than abstract LLM capabilities. It is the benchmark the MoA virtual-models release (+8%/+11%) is scored on.

Nous pre-announced the **full public leaderboard** on 2026-06-27:
- https://x.com/NousResearch/status/2070610467421028812

This KB card captures what's known so far (the public leaderboard wasn't live at the time of the 06-27 scan); it should be revised once the leaderboard publishes.

## What's Known (June 2026)

- **Domains covered** — capabilities relevant to a multi-step agent: reasoning, coding, tool-use, and multi-step planning. Nous framed the +8%/+11% MoA numbers in terms of agent-shaped work rather than static QA.
- **Reported numbers from the MoA release:**
  - **+8%** for MoA ensemble vs Opus 4.8 single
  - **+11%** for MoA ensemble vs GPT 5.5 single
  - Both measured on the upcoming (at the time) HermesBench leaderboard
- **Internal-R&D framing** — Per @llmDestructor's clarification: Nous builds custom MoA models partly because they're heavy Hermes users; HermesBench is the tool that lets them internally measure which models work best *with Hermes specifically*. Expect the benchmark to bias toward tasks where agents matter, not generic NLP benchmarks.

## What's Not Yet Public

- Full methodology / categories / sample counts
- Public leaderboard URL (pre-announced as "coming soon")
- Per-category breakdown of the +8% / +11% deltas
- Whether there's a held-out private portion (likely — most serious benchmarks have one)

When the leaderboard lands, a follow-up revision of this card should capture categories and methodology.

## Why It Matters

- **First Nous-curated benchmark since the pre-Hermes era** (we have at least one precedent — they had internal Hermes eval material before, but HermesBench is the first explicitly *named* public-facing benchmark). Lets external developers reason about which models / ensembles actually work well in Hermes-shaped contexts.
- **MoA numbers become interpretable.** Without HermesBench as a shared yardstick, the "MoA beats Opus 4.8 by 8%" announcement was unauditable. Once public, both model teams and Hermes operators can reproduce or contest the deltas.
- **Agent-shaped.** Where MMLU / HumanEval / GPQA measure static capabilities, HermesBench reportedly includes tool-use and multi-step. That's a shift; cheap benchmarks for these are scarce and the ones that exist tend to leak into training data within months.

## See Also

- [[engineering/2026-06-27-hermes-moa-virtual-models]] — The MoA release that put HermesBench on the radar
- [[engineering/evals-ai-evaluation-frameworks]] — General framework for evaluating AI outputs; HermesBench is one specific instance
- [[engineering/2026-06-19-engineering-agentic-loop-pattern]] — The inner-loop pattern HermesBench is presumably designed to measure components of
- [[engineering/harness-engineering]] — The harness-level capabilities that benchmarks usually miss (real failure modes are interaction-shaped, not benchmark-shaped)

## Source Notes

- Primary: Nous tweet 2026-06-27 pre-announcing the full leaderboard — https://x.com/NousResearch/status/2070610467421028812
- Numbers: from the MoA announcement tweet on the same day — https://x.com/NousResearch/status/2070610321278988385
- KB maintenance: update this card when the leaderboard ships
