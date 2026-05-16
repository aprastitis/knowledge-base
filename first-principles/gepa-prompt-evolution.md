# GEPA — Genetic-Pareto Prompt Evolution

## Type
First Principles / AI Optimization

## Summary
An offline skill optimization pipeline that reads execution traces to understand why things failed, then proposes targeted improvements through evolutionary search. Published as ICLR 2026 Oral paper. MIT licensed.

## The Problem GEPA Solves

The in-agent self-improvement loop (skill creation + Curator) has a known weakness:
- **The agent tends toward self-congratulation** — it almost always thinks it performed well even when it didn't
- The same system that auto-generates skills can overwrite manual customizations with worse versions

GEPA provides an offline validation layer that doesn't trust the agent's own self-assessment.

## How It Works

```
Read current skill → Generate evaluation dataset → Run GEPA optimizer
→ Evaluate candidates (LLM-as-judge) → Apply constraint gates → PR to repo
```

### The Pipeline
1. **Read** the current skill from the Hermes repo
2. **Generate evaluation dataset** — synthetic test cases (Claude Opus), real session history from SQLite, or hand-curated golden sets
3. **GEPA optimizer** — reads execution traces → understands failure points → generates candidate variants through evolutionary search
4. **LLM-as-judge scoring** — evaluates candidates using rubrics (not binary pass/fail)
5. **Constraint gates** — full test suite must pass 100%, skills stay under 15KB, caching compatibility preserved, semantic purpose doesn't drift
6. **Best variant goes out as a PR** — never a direct commit

## Constraints
- No GPU required — everything runs through API calls
- **Cost:** roughly $2-10 per optimization run
- Never direct commit — always PR against the repo

## When to Use GEPA vs Fine-Tuning

> "GEPA is something that can be skipped initially, but is highly effective when you hit a wall and don't want to spend time and money on finetuning (RL/GRPO)."

GEPA = offline prompt optimization (cheap, fast, reversible)
RL/GRPO fine-tuning = expensive, time-consuming, harder to undo

GEPA is the "try this before going to full fine-tuning" option.

## Related Concepts
- [[self-evolving-skills]] — GEPA is the offline validation layer that addresses the self-congratulation weakness in the in-agent skill creation loop
- [[physics-first-principles]] — Reason from execution traces rather than trusting the agent's self-assessment — this is first-principles thinking applied to AI improvement
- [[lean-engineering]] — GEPA validates before shipping; it's the "don't skip testing" principle for agent skills

## Source
- https://x.com/akshay_pachaar/status/2054564519280804028 (Hermes Agent Masterclass by Akshay 🚀)
- https://github.com/NousResearch/hermes-agent-self-evolution
- https://arxiv.org/abs/2507.19457 (ICLR 2026 Oral paper)