---
type: card
status: active
date: 2026-06-07
summary: "Tracing every reasoning step, tool call, and model response as structured spans so you can answer: why did the agent do that? Built on OpenTelemetry's emerging GenAI semantic conventions."
tags:
  - observability
  - tracing
  - opentelemetry
  - agent-debugging
  - genai-semantic-conventions
  - production
related:
  - engineering/scaffolding-for-ai-agents
  - engineering/agentic-coding-principles
  - engineering/evals-ai-evaluation-frameworks
  - concepts/self-evolving-skills
sources:
  - https://www.digitalapplied.com/blog/ai-agent-observability-2026-tracing-monitoring-stack-guide
  - https://opentelemetry.io/blog/2025/ai-agent-observability/
  - https://opentelemetry.io/docs/specs/semconv/gen-ai/
---

# Agent Observability with OpenTelemetry

AI agent observability is the practice of **tracing, monitoring, and evaluating autonomous agents in production** — capturing every model call, tool execution, and reasoning step as structured spans so you can answer the one question that matters when something goes wrong: *why did the agent do that?*

In 2026 it has become a discipline of its own, with a vendor-neutral standard (OpenTelemetry GenAI conventions) and a fast-consolidating market of platforms behind it.

## Why Agents Break Differently

Traditional software is observable in the classic three pillars: metrics, logs, traces. Agents break that model in two ways:

**Non-determinism:** The same input does not always produce the same behavior. Temperature, retrieval results, and tool availability all shift the agent's path. A single "happy path" trace is insufficient — you need to observe the **distribution of behaviors**, not one example.

**Silent failure:** An agent returns something well-formed, plausible, even confident. The problem is it is **wrong**, or it took an expensive detour, or it called a tool it never needed. None of that trips a 500. Traditional up/down or error-rate monitoring is blind to all of it.

> *"Agentic systems fail in ways that look like success: incorrect but well-formed outputs, unnecessary tool calls, or actions that are syntactically valid but semantically wrong."*
> — Aryan Kargwal, PhD Candidate, Polytechnique Montréal

## The Core Requirement: Step-Level Traces

You want to capture, for every agent run:

- The **reasoning trace** (what the model considered)
- The **tools considered** vs. **tools actually invoked**
- The **arguments passed** and **responses returned**
- The **tokens spent** at each step
- The **latency** of each hop

All stitched into one **hierarchical trace** you can replay. This complements offline [[engineering/evals-ai-evaluation-frameworks]] (which catch regressions before deployment) with what production actually throws at you after.

## OpenTelemetry GenAI Semantic Conventions

The most important development in this space is not a product — it is a **specification**. The [OpenTelemetry GenAI semantic conventions](https://opentelemetry.io/docs/specs/semconv/gen-ai/) define a common vocabulary for AI telemetry: a standard set of `gen_ai.*` span and metric attributes that any instrumentation library can emit and any backend can ingest.

Adopt them and you **decouple your instrumentation from your vendor** — you can switch observability platforms without re-instrumenting your agents.

### The Six Layers

1. **Client (model-call) spans** — individual LLM calls
2. **Agent and workflow spans** — agent-level and orchestration-level
3. **MCP conventions** — tool layer protocol detail
4. **Semantic events** — key agent events
5. **Metrics** — histogram measures
6. **Provider-specific attributes** — per-model nuances

### Mandatory Metrics (Floor)

Two histogram metrics are effectively mandatory for any production deployment:

| Metric | Purpose |
|--------|---------|
| `gen_ai.client.operation.duration` | Latency in seconds |
| `gen_ai.client.token.usage` | Token consumption, broken down by input/output |

Export these or you **cannot reason about cost or speed**.

### Agent Span Operations

The spec defines four span operation types for agents:

| Operation | When to use |
|-----------|-------------|
| `create_agent` | Agent instantiation |
| `invoke_agent` | Agent execution — CLIENT if remote (e.g., Bedrock Agent), INTERNAL if in-process (e.g., LangChain, CrewAI) |
| `invoke_workflow` | Parent span wrapping multiple `invoke_agent` children in multi-agent systems |
| `execute_tool` | Tool invocation |

**Key pattern:** In multi-agent systems, a single `invoke_workflow` span (INTERNAL) is the parent that wraps several `invoke_agent` children — this hierarchy lets you follow a task across agent handoffs in one trace.

### Status Warning

As of v1.41, the GenAI conventions are still in **Development status** — not Stable. Nearly every `gen_ai.*` attribute carries a Development badge. Attribute names can change without a major version bump.

**Escape hatch:** Set `OTEL_SEMCONV_STABILITY_OPT_IN=gen_ai_latest_experimental` to enable dual-emission of both legacy (v1.36.0 and earlier) and current attribute names, preventing silent dashboard breakage.

## MCP Span Enrichment (v1.39+)

The tool layer used to be the black box in agent traces. OpenTelemetry v1.39 added MCP semantic conventions: `mcp.method.name`, `mcp.session.id`, `mcp.protocol.version`.

The design principle: **enrich, don't duplicate**. When MCP instrumentation detects that outer GenAI instrumentation already tracks the tool execution, it enriches the existing `execute_tool` span with MCP attributes instead of creating a second span. You get protocol-level detail layered onto the tool span you already had — not a noisier trace.

This matters for anyone building on [[engineering/hermes-native-mcp-catalog]] — visibility into the tool layer is now standardized.

## Privacy by Default

The spec defines three content capture modes — **opt-in, not automatic**:

1. **Not recorded** (default)
2. **Stored on span attributes** — explicit opt-in
3. **External storage plus reference URL on span** — recommended for production systems handling PII

Message bodies (`gen_ai.input.messages`, `gen_ai.output.messages`) are **not captured** unless you explicitly opt in.

## Getting Started

In Python, OpenAI tracing can be a single line:

```python
from opentelemetry.instrumentation.openai import OpenAIInstrumentor

OpenAIInstrumentor().instrument()
```

After which semconv-compliant spans are produced automatically. Major backends (Datadog from v1.37, Langfuse, Arize, Braintrust) already speak the convention natively.

## What to Actually Alert On

Traditional health checks ("is the service up?") are near-useless for agents. Instead:

- **Latency anomalies** — unexpected slowness in model calls or tool execution
- **Token budget overruns** — sudden spikes in context size
- **Tool call loops** — same tool invoked N times in a single turn (indicates planning failure)
- **Error rate by span type** — `execute_tool` errors vs. `invoke_agent` errors tell you different stories
- **Fallback frequency** — how often the agent falls back to a larger model or simpler strategy

---

*Related: [[engineering/evals-ai-evaluation-frameworks]] (offline eval catching regressions before deployment) | [[engineering/scaffolding-for-ai-agents]] (tooling that determines outcomes) | [[concepts/self-evolving-skills]] (agents that improve from telemetry)*