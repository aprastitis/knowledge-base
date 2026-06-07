---
type: card
status: active
date: 2026-06-07
summary: "Five proven patterns for coordinating multiple AI agents: sequential, concurrent, group chat, handoff, and magentic. Complexity should match the problem — start simple, escalate as needed."
tags:
  - multi-agent
  - orchestration
  - coordination
  - agent-architecture
  - patterns
related:
  - engineering/gstack-process-pattern
  - engineering/hermes-kanban
  - engineering/dispatch-routing-tiers
  - concepts/context-engineering
sources:
  - https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns
  - https://gurusup.com/blog/multi-agent-orchestration-guide
---

# Multi-Agent Orchestration Patterns

When a single agent can't reliably handle a task due to **prompt complexity, tool overload, or security requirements**, multiple specialized agents can coordinate to solve problems. This guide covers the five fundamental orchestration patterns.

**First principle:** Use the lowest level of complexity that reliably meets your requirements. Multi-agent orchestration adds coordination overhead, latency, and failure modes. Justify it.

## The Complexity Spectrum

| Level | Description | When to use |
|-------|-------------|-------------|
| **Direct model call** | Single prompt, no tools, one pass | Classification, summarization, translation |
| **Single agent + tools** | One agent loops through multiple tool calls | Varied queries within a single domain |
| **Multi-agent orchestration** | Multiple specialized agents coordinate | Cross-domain, distinct security boundaries, parallel specialization |

## The Five Patterns

### 1. Sequential Orchestration (Pipeline / Prompt Chaining)

Chains agents in a **predefined linear order**. Each agent processes the output from the previous one. The choice of next agent is **deterministic** — not a choice given to the agents themselves.

**Use when:**
- Clear linear dependencies, predictable progression
- Data transformation pipelines where each stage adds value the next depends on
- Progressive refinement workflows: *draft → review → polish*
- Stages cannot be parallelized

**Avoid when:**
- Stages are embarrassingly parallel
- Early failures would propagate bad output to all subsequent stages
- Agents need to collaborate rather than hand off

**Example:** Contract generation: *template selection* → *clause customization* → *regulatory compliance* → *risk assessment*

---

### 2. Concurrent Orchestration (Fan-Out / Fan-In)

Multiple agents work in **parallel** on sub-tasks, then results are aggregated. Uses a ** splitter** to distribute work and a **reducer** to collect results.

**Use when:**
- Tasks can be decomposed into independent sub-tasks
- Speed matters — parallel execution reduces total latency
- Multiple specialized perspectives are needed simultaneously

**Avoid when:**
- Sub-tasks have dependencies on each other's results
- Aggregation logic is complex or order-sensitive

**Example:** Research task: three agents simultaneously search different sources (web, academic, internal docs), results merged into a synthesis.

---

### 3. Group Chat

Multiple agents communicate in a **shared chat space** with defined roles and interaction rules. An **orchestrator** or **manager** agent may moderate, but agents can also communicate peer-to-peer.

**Use when:**
- Collaborative reasoning is needed
- Diverse perspectives must interact
- No predetermined output structure

**Avoid when:**
- Tasks are strictly sequential
- Clear accountability or a single decision-maker is required

**Example:** Product design review — agents representing Engineering, Design, Security, and QA debate and refine a proposal.

---

### 4. Handoff Pattern

A primary agent **delegates to a specialist agent** based on the task type, then regains control when the specialist completes its portion. The primary retains orchestration authority.

**Use when:**
- Tasks require distinct expertise at different stages
- A single primary agent should remain in control of the overall flow
- Clear boundaries exist between task phases

**Avoid when:**
- Agents need to collaborate concurrently
- The delegation chain becomes too deep (loses traceability)

**Example:** Customer support: triage agent identifies issue type → delegates to specialized agents (billing, technical, returns) → receives results and synthesizes response.

---

### 5. Magentic Pattern (Orchestrator-Worker)

A central **orchestrator agent** plans and assigns tasks to **worker agents**, coordinates their outputs, and manages the overall workflow. Workers don't communicate directly with each other — they report back to the orchestrator.

Also known as: **orchestrator-worker** or **supervisor pattern**.

**Use when:**
- Complex, multi-phase tasks requiring central coordination
- Tasks need to be dynamically allocated based on availability and capability
- Central oversight is required for auditability and control

**Avoid when:**
- Tasks are simple enough for a single agent
- Peer-to-peer collaboration would be more efficient than central coordination

**Example:** [[engineering/gstack-process-pattern]] — Garry Tan's AI team methodology uses a CEO agent that chains through Eng Manager, Designer, QA, Security, and Release agents in a sprint process.

## Key Decisions

### Orchestrated vs. Choreographed

| | Orchestrated | Choreographed |
|--|--|--|
| **Control flow** | Explicit, defined in orchestrator | Agents declare capabilities, coordinator routes |
| **Frameworks** | LangGraph | CrewAI |
| **Traceability** | High — explicit control flow | Lower — emergent from capability declarations |
| **Complexity** | More upfront design | More dynamic, less predictable |

Most production teams never need full decentralization. Start with orchestration.

### Shared State Management

In multi-agent systems, agents need to share context and results:

- **Shared context object** — a common data structure passed between agents
- **Persistent memory layer** — external storage (SQLite, KV store) that agents read/write
- **Event-driven** — agents emit events, others subscribe (used in Hermes Kanban via `kanban_*` tools)

### Failure Modes

Multi-agent systems introduce new failure modes:

- **Cascading failures** — one agent's error propagates to dependents
- **Deadlock** — agents waiting on each other
- **Split-brain** — conflicting state across agents
- **Coordination overhead** — the cost of synchronizing can exceed the benefit

Mitigations: iteration limits, timeout budgets, circuit breakers, and explicit error boundaries per agent.

## Relationship to Hermes

[[engineering/hermes-kanban]] uses a SQLite-backed durable task board with `kanban_*` tools for peer coordination. This is a practical implementation of the handoff/orchestrator-worker pattern for multi-agent task management.

[[engineering/gstack-process-pattern]] maps to the magentic/orchestrator-worker pattern — a CEO agent chains through specialized role agents.

---

*Related: [[engineering/gstack-process-pattern]] (magentic pattern in practice) | [[engineering/hermes-kanban]] (durable multi-agent task board) | [[engineering/dispatch-routing-tiers]] (routing by task complexity)*