---
type: engineering
status: active
date: 2026-06-08
summary: A new engineering discipline (emerged Feb 2026) for designing the constraints, feedback loops, and environment that make AI agents reliable in production. Agent = Model + Harness.
tags: [engineering-practice, agentic-coding, ai-engineering, harness-engineering]
related: [[engineering/agentic-coding-principles]], [[engineering/agentic-coding-tools]], [[engineering/evals-ai-evaluation-frameworks]], [[engineering/lean-engineering]]
sources: [@sairahul1 (Rahul) on X, 2026-06-07](https://x.com/sairahul1/status/2063544956158185927)
---

# Harness Engineering

A new engineering discipline that emerged in early 2026 for designing the environment and constraints that make AI agents reliable at scale. Coined after OpenAI shipped 1 million lines of production code with zero lines written by hand — the humans designed the system, the agents wrote the code.

## Core Equation

```
Agent = Model + Harness
```

The **harness** is everything that isn't the model: constraints that keep the agent on track, feedback loops that catch mistakes, documentation that tells the agent where it is, tools it has permission to use.

Strip away the harness → raw language model guessing through your codebase.
Add the right harness → system that ships production code.

## The OS Analogy (Philipp Schmid / Hugging Face)

| Component | Analogy |
|-----------|---------|
| Model | CPU (raw processing power) |
| Context window | RAM (limited, volatile working memory) |
| Harness | Operating System (manages what the CPU sees and when) |
| Agent | Application running on top |

Without an OS, a powerful CPU is just silicon. Most teams are running apps with no operating system — that's why their agents fail in production.

## The Evidence

- **LangChain on Terminal Bench 2.0**: Same model, old harness → 52.8%, new harness → 66.5% (+13.7%)
- **Vercel**: Removed 80% of agent tools → better performance (not worse)
- **OpenAI**: Codex agents handle 70% of internal PRs; Sora Android built by 4 engineers in 28 days, #1 on Play Store, 99.9% crash-free

**The uncomfortable truth of 2026:**
- The agent was never the hard part
- The harness is

## The Five Harness Artifacts

### 1. AGENT.md / CLAUDE.md Files

Distributed markdown files throughout the codebase. Agent reads them at session start — like onboarding docs for a new engineer.

OpenAI calls them `AGENT.md`, Anthropic calls them `CLAUDE.md`, Cursor uses `.cursorrules`. Different names, same principle.

### 2. JSON Feature Lists (Progress Tracker)

When an agent builds an app over multiple sessions, it starts each session with a blank context. JSON file tracks:
- Each feature
- How to verify it works
- Pass/Fail status

Anthropic found agents are less likely to accidentally overwrite JSON than Markdown.

### 3. Session Initialization Routines

Same 7-step boot sequence, every session:
1. Confirm working directory
2. Read git logs and progress files
3. Check feature list for highest-priority incomplete item
4. Start the dev server
5. Run basic end-to-end verification
6. Implement one feature
7. Commit with descriptive message + update progress

### 4. Sprint Contracts

Before any coding, two agents negotiate:
- **Generator** proposes what it will build + how success will be verified
- **Evaluator** reviews for completeness and clear criteria

Only after both agree does implementation begin. Planning and execution must be separated.

### 5. Structured Task Templates

Harness analyzes the real codebase first, produces a grounded impact map:
- Real file paths (not hallucinated)
- Real symbol names that actually exist
- Existing patterns to follow
- Concrete acceptance criteria

## The Three Implementation Camps

### OpenAI: Environment-First
- Design the environment thoroughly → agents produce reviewable output
- Strict dependency flows: Types → Config → Repo → Service → Runtime → UI
- Agents wired directly into CI/CD pipelines
- Philosophy: Design the environment. Then let the agent loose.

### Anthropic: Separate Doer from Judge
Problem: Agents praised their own output even when quality was mediocre.

Fix: Three specialized agents:
- **Planner** — turns 2-sentence prompt into full product spec
- **Generator** — implements features one sprint at a time
- **Evaluator** — uses browser automation to test like a real user

Making a standalone evaluator skeptical is far easier than making a generator critical of its own work.

### ThoughtWorks: The 2×2 Framework

Classify every harness control along two axes:

| | Computational (deterministic, ms) | Inferential (LLM, seconds) |
|---|---|---|
| **Feedforward (before agent acts)** | Type systems, linters, architectural rules | Spec documents, constraint descriptions |
| **Feedback (after agent acts)** | Test suites, coverage analysis, mutation testing | LLM code reviewers, behavior validators |

## The Five Universal Principles

1. **Context Beats Instructions** — Show the agent where it is. Real file paths, real code. Abstract instructions consistently underperform grounded context.

2. **Planning and Execution Must Be Separated** — Agents that plan and execute in the same pass produce unreliable output. The planning step doesn't have to be human — but it must be separate and its output reviewed before implementation.

3. **Feedback Loops Are Non-Negotiable** — A harness without feedback is just a prompt with extra steps. OpenAI uses CI, Anthropic uses another LLM, ThoughtWorks says layer both.

4. **One Thing at a Time** — Agents that try to do too much at once run out of context, lose coherence, silently drop requirements. Forced incrementalism is universal across every successful harness.

5. **The Codebase IS the Documentation** — Nobody maintains a separate knowledge base for the agent. The repo is the single source of truth. If a convention isn't in the codebase, the agent won't know about it.

## The Paradox: Build to Delete

### Harness Decay

Every component in a harness encodes an assumption about what the model can't do. As models improve, those assumptions expire and the component becomes overhead.

Anthropic's experience:
- **Opus 4.5**: sprint decomposition + per-sprint evaluation (necessary)
- **Opus 4.6**: no sprint decomposition + single-pass evaluation (saves 38% cost)
- **Opus 4.7**: model starts self-verifying → evaluator role shrinks further

### Build to Delete

Design every harness component to be removable. Test each periodically by turning it off and measuring whether output quality changes. If it doesn't change: delete it.

Manus refactored their harness 5 times in 6 months. Vercel removed 80% of tools → got better performance.

Carrying dead harness components costs tokens on every run. Zero extra quality. Pure waste.

## The Cost Reality

Anthropic's honest numbers:
- **Solo agent (no harness)**: $9, 20 min → working UI, broken core functionality
- **Full harness (Opus 4.5)**: $200, 6 hours → working software, polished UI, correct physics

That's a 22x cost increase. But the trend line matters: the $200 harness became $124 with one model upgrade. Better model → simpler harness → cheaper run → faster output.

## Key Takeaway

> The engineers winning in 2026 aren't writing the best code. They're designing the best constraints. And being willing to throw those constraints away the moment they stop earning their keep.

## Related

- [[engineering/agentic-coding-principles]] — Six principles and 28 practices for production-quality AI-assisted development
- [[engineering/agentic-coding-tools]] — Capability benchmarks for autonomous coding tools
- [[engineering/evals-ai-evaluation-frameworks]] — Frameworks for evaluating AI system performance
- [[engineering/lean-engineering]] — Lean methodology applied to engineering teams