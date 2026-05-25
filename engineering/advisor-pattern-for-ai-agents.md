---
type: engineering
status: active
date: 2026-05-20
summary: Smaller model as executor + larger model as advisor; beats single large model; cost-effective agentic workflows
tags: [agent-design, agentic-coding]
related: [[hermes-kanban]], [[scaffolding-for-ai-agents]]
sources: [internal]
---

# Advisor Pattern for AI Agents

Using a **smaller model as executor + larger model as advisor** outperforms using the large model alone for many tasks. This pattern gets you near-top-tier quality at a fraction of the cost and latency.

## The Core Pattern

```
User Task
    ↓
[Small Model: Executor] → does the work
    ↓
[Large Model: Advisor] → reviews, catches errors, course-corrects
    ↓
Output
```

The advisor reviews the executor's work and catches mistakes the executor missed. This is essentially code review, applied to AI output.

## Why It Works

1. **Error catching**: The executor makes mistakes that the advisor catches before output
2. **Cost efficiency**: Smaller models are significantly cheaper per token
3. **Latency**: Executor responds quickly; advisor adds minimal latency for the quality gain
4. **Specialization**: The executor can be optimized for speed/repetition tasks; the advisor focuses on quality control

## Real Example: Yv Legal

Yv Legal uses this pattern and achieves font-tier (Opus-level) quality at a fraction of the cost. The advisor model reviews the executor's output and catches issues the executor missed.

## When to Use It

- **Complex multi-step tasks**: The advisor catches compounding errors that a single model would miss
- **High-value outputs**: Code, documents, decisions where quality matters more than speed
- **Cost-sensitive workflows**: When you need high quality but can't afford to run everything through your best model

## When NOT to Use It

- **Simple one-step tasks**: The advisor overhead isn't worth it for tasks the executor handles perfectly
- **Real-time constraints**: If you need immediate response, skip the advisor
- **High-volume low-stakes**: Drafting, summarization, simple extraction — executor alone is fine

## Implementation Considerations

- **Executor model choice**: Pick the smallest model that reliably completes the task type. Sonnet Haiku works for many coding tasks.
- **Prompt the advisor to catch specific error types**: Don't just say "review this." Tell it what kinds of errors to look for.
- **Keep context for both**: The advisor needs enough context to understand what the executor was trying to do.

## Relationship to My Work

When I'm building agent workflows, I should consider: which parts of this task are high-stakes (use advisor) and which are repetitive (executor alone)? The advisor pattern is especially relevant for tool-building and skill-authoring tasks where quality matters and errors are costly.

## See Also

- [[evals-ai-evaluation-frameworks]] — Evals inform model selection; how to measure whether routing decisions are right
- [[agentic-coding-tools]] — How agentic coding tools work and when the advisor pattern applies
- [[scaffolding-for-ai-agents]] — How the tooling around the model determines outcomes