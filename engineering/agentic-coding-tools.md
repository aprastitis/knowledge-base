---
type: engineering
status: active
date: 2026-05-20
summary: Shift from copilot to autonomous agent; capability timeline; real production numbers from Shopify, Mercado Libre, Spotify
tags: [agentic-coding, engineering-practice]
related: [[agentic-coding-principles]], [[scaffolding-for-ai-agents]]
sources: [internal]
---

# Agentic Coding Tools

Tools like Claude Code represent a shift from **copilot** (completing your lines) to **autonomous agent** (owning tasks end-to-end). This card covers how to use them effectively as a builder, and what they mean for how I work.

## What "Agentic" Actually Means

An agentic tool doesn't just finish your code — it owns a complete workflow:
1. Reads the task/goals
2. Reads relevant code/context
3. Makes changes across multiple files
4. Runs tests, CI checks, linters
5. Creates PRs and communicates results back

The key technical shift: models now work for **hours** on a single task, not just minutes. Long-task continuity is the new capability threshold.

## Capability Timeline (Anthropic/Claude)

| Model | Capability |
|-------|------------|
| Opus 3 | First trained to write long code |
| Sonnet 3.5 | First to use tools/computation |
| Sonnet 3.7 | First to think before answering — **inflection point for agentic** |
| Opus 4 | Writes Excel formulas, PowerPoint docs |
| Opus 4.7 + Mythos Preview | High-throughput agentic workflows at scale |

Sonnet 3.7 was the turning point. "Think before answering" enabled the chain-of-thought that makes multi-step autonomous tasks viable.

## Practical Usage Patterns

### Trust the tool usage
Let the agent call tools, read files, run tests — don't steer it away from tool usage. The scaffolding around the model matters more than which model you pick.

### Design for full-task handoff, not step-by-step
Instead of "write this function then that test" → "own this feature from spec to PR." The value comes from the agent maintaining context across the full workflow.

### Design for the model in 6 months, not today's model
Build prompts and integrations forward-looking. When a task starts failing repeatedly, that's the signal you've hit the ceiling of your current setup.

## Real Production Numbers

- **Shopify**: Entire org uses agentic coding — including non-technical roles (production managers, designers, data scientists) via internal tooling. Built vendor onboarding flow on Claude Code platform.
- **Mercado Libre**: 23,000 developers, 500K+ PRs through Claude Code with human review, 9,000+ apps modernized. Even managers and BPs who haven't coded in years are using it.
- **Spotify**: Uses Claude Code to move code across repos. Runs agents that open PRs autonomously. 90%+ CI time saved.
- **Asana**: Built AI teammates that collaborate within Asana itself using Claude Agents Manage.

## New Features to Know About

- **Code Review Agent**: Catches critical bugs in code changes before they land
- **Mobile coding**: Claude Code on iOS/Android — work without the laptop
- **Claude Security**: Scans shared context variables for secrets leaks before they go to production

## Relationship to My Work

When I'm building skills or tools, the principle is: design for the agent to own complete jobs, not execute single steps. Think about what context the agent needs to maintain, and what tooling (MCP, CI integration, etc.) would amplify its capabilities.

The gap between what these tools can do and what most teams use them for is massive. Most people still use AI for drafting — the opportunity is treating it as an autonomous worker.

## See Also

- [[agentic-coding-principles]] — Six principles for moving beyond vibe coding to production-quality AI-assisted development
- [[advisor-pattern-for-ai-agents]] — Using smaller models as executors with larger models as advisor
- [[evals-ai-evaluation-frameworks]] — How to test AI outputs systematically; eval types, metrics, frameworks, getting started
- [[scaffolding-for-ai-agents]] — How the tooling around the model determines outcomes
- [[raw/openai-how-openai-uses-codex]] — OpenAI's internal guide to Codex use cases, best practices, and engineering anecdotes