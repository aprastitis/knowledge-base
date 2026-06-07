---
type: card
status: active
date: 2026-06-07
summary: "Curating the optimal set of tokens in an LLM's context window — the successor to prompt engineering. Context is finite with diminishing returns; attention must be actively managed."
tags:
  - context-management
  - agent-design
  - prompt-engineering
  - llm
  - attention
  - runtime-context
related:
  - concepts/llm-wiki-pattern
  - engineering/scaffolding-for-ai-agents
  - engineering/ask-mode-before-code-mode
  - concepts/self-evolving-skills
sources:
  - https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
  - https://blog.jetbrains.com/research/2025/12/efficient-context-management/
---

# Context Engineering

Context engineering is the discipline of **curating and maintaining the optimal set of tokens** passed to an LLM at inference time — including system prompts, tools, message history, external data, and any other information that lands in the context window. It is the natural successor to prompt engineering as agents operate over longer time horizons and more complex state.

Prompt engineering focuses on *what you write*; context engineering focuses on *what you include and when*.

## Why Context Is Finite

Despite growing context windows, LLMs suffer from **context rot** — their ability to accurately recall and reason over information **decreases as context grows**. This happens because:

- The transformer architecture creates n² pairwise attention relationships. As context length increases, the model's ability to capture these relationships gets stretched thin.
- Models are trained on data distributions where shorter sequences dominate. They have fewer specialized parameters for long-range dependencies.
- LLMs have a finite **attention budget**. Every new token depletes it by some amount.
- Position encoding interpolation lets models handle longer sequences but with degraded position understanding.

The result: context must be treated as a **finite resource with diminishing marginal returns** — like human working memory.

## The Core Principle

> Find the smallest possible set of high-signal tokens that maximize the likelihood of the desired outcome.

Minimal does not mean short — you still need enough information for the agent to behave correctly. It means *curated* and *tight*.

## The Anatomy of Context

### System Prompts

- Use **simple, direct language** at the right altitude for the agent
- Avoid two failure modes: (1) **brittle if-else hardcoded logic** that creates fragility, and (2) **vague high-level guidance** that falsely assumes shared context
- Organize into **distinct sections** (`<background_information>`, `<instructions>`, `## Tool guidance`, `## Output description`) with XML tags or Markdown headers
- **Start minimal** — test with the best model available, then add instructions only to address observed failure modes

### Tools

Tools define the contract between agents and their information/action space. Design principles:

- **Token efficient** — return only what the agent needs
- **Self-contained and unambiguous** — if a human can't definitively say which tool to use in a given situation, the agent can't either
- **Minimal overlap** — a bloated tool set with ambiguous decision points is a common failure mode
- Input parameters should be **descriptive and unambiguous**, playing to the model's strengths

### Few-Shot Examples

- Use **diverse, canonical examples** that portray expected behavior effectively
- Do NOT stuff a laundry list of edge cases — curate instead of enumerating
- For an LLM, **examples are pictures worth a thousand words**

### Message History

- Treat as a **finite resource** — context grows with every turn
- Agent-generated context (reasoning traces, tool results) can quickly become **expensive noise** rather than useful information
- Active pruning and curation is required for long-horizon tasks

## Context Retrieval Strategies

### Pre-inference Retrieval (Traditional RAG)
Embed relevant documents at setup time. Fast but static — suffers from stale indexing and doesn't handle dynamic syntax trees well.

### Just-in-Time / Runtime Exploration (Agentic)

Agents maintain **lightweight identifiers** (file paths, stored queries, web links) and dynamically load data into context at runtime using tools. Key properties:

- **Metadata provides behavioral signals** — folder hierarchies, naming conventions, timestamps all hint at purpose and relevance
- Enables **progressive disclosure** — agents assemble understanding layer by layer, maintaining only what's necessary in working memory
- Mirrors human cognition: we don't memorize corpuses, we use external organization systems (file systems, bookmarks) to retrieve on demand
- Used by Claude Code: `CLAUDE.md` files dropped in naively upfront, then `glob` and `grep` for just-in-time navigation

### Hybrid Strategy

The most effective agents for complex, dynamic environments use **both**:

1. Some data pre-loaded for speed (e.g., project conventions, key files)
2. Runtime exploration for dynamic content (e.g., live data, complex codebases)

## Context Management Techniques

Research (JetBrains / TUM, NeurIPS 2025) identifies two main approaches for managing growing context in agent loops:

### 1. Observation Masking

- **Hides** older, less important environment observations while preserving full reasoning and action history
- Targets only the verbose parts (e.g., test logs, full file reads) — the observation portion of a turn
- Used in production by **Cursor** and **Warp** (SE agents)
- Context still grows with turns, but at a significantly reduced rate
- The agent retains its full decision trail; only reprocessing of verbose text is eliminated

### 2. LLM Summarization

- Another model (or the same model) **compresses** the trajectory into a compact summary
- Reduces the resolution of all turn components: reasoning, action, and observation
- Theoretically allows **infinite scaling** — repeated summarization prevents context window overflow
- More computationally expensive than masking but handles unbounded turns

### Hybrid (Best of Both)

JetBrains Research found a hybrid approach achieves the best cost/performance tradeoff:
> Combines observation masking (for efficiency on recent turns) with periodic LLM summarization (for infinite-horizon tasks)

## Long-Horizon Task Patterns

For agents that run over extended periods:

- Use **note-taking strategies** for persistence beyond working memory
- Leverage **external storage** (files, databases) as a second brain — reference by identifier rather than loading everything at once
- Implement **iteration limits** to prevent infinite tool-call loops
- Re-evaluate context contents at each step: what must stay, what can be evicted, what should be summarized

## Key Takeaways

1. **Context is not free or infinite** — it has a cost and a ceiling. Treat it as a budget.
2. **Curate actively, don't just accumulate** — the goal is minimal high-signal, not maximal coverage.
3. **Context engineering is iterative** — happens at every inference decision, not just at prompt write time.
4. **Hybrid strategies dominate** — pre-load for speed, explore for dynamism, mask/summarize for efficiency.
5. **The field has moved from prompt engineering to context engineering** — the question is no longer "what do I write?" but "what configuration of context will produce the desired behavior?"

---

*Related: [[concepts/llm-wiki-pattern]] (knowledge retrieval patterns) | [[engineering/scaffolding-for-ai-agents]] (tooling around the model) | [[engineering/ask-mode-before-code-mode]] (context-efficient planning)*