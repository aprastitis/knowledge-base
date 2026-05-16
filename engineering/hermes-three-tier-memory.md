# Hermes Three-Tier Memory System

## Type
Engineering / Architecture

## Summary
Hermes uses three distinct memory layers, each designed for a different purpose and speed — from always-in-context tiny files to unlimited searchable conversation history.

## The Three Tiers

### Tier 1: Frozen Micro-Files
Two tiny markdown files injected into the system prompt at session start:
- **MEMORY.md** (~2,200 chars max) — agent's notes: environment, project conventions, tool quirks, lessons learned
- **USER.md** (~1,375 chars max) — user profile: name, preferences, skill level, things to avoid

Both are frozen snapshots in the system prompt. If the agent writes a new memory mid-session, it persists immediately but won't appear in the prompt until the next session. At ~80% capacity, the agent must consolidate — merging entries into denser versions so only useful information survives.

**Tradeoff:** Always in context but tiny. Critical facts only.

### Tier 2: Full-Text Session Search
Every CLI and messaging conversation stored in SQLite with full-text search (FTS5). The agent can search weeks of past conversations.

**Tradeoff:** Unlimited capacity but requires active search + LLM summarization to use.

### Tier 3: External Memory Providers
8 pluggable providers that run alongside built-in memory (never replacing it). Only one active at a time. When active, Hermes automatically:
- Prefetches relevant memories before each turn
- Syncs conversation turns after each response
- Extracts memories on session end

## Key Principle

> "Critical facts live in memory. Everything else is searchable on demand."

This is the core tradeoff design: Tier 1 is expensive to keep full (token cost) but always present. Tier 2 is cheap to store but requires an active retrieval action.

## Related Concepts
- [[soul-md-identity-layer]] — Memory is the "moving part inside the fixed frame" of SOUL.md
- [[self-evolving-skills]] — Skills are procedural memory; memory is factual memory. Different tiers for different types of knowledge
- [[llm-wiki-pattern]] — Similar three-layer idea (ingest/query/lint) but for wiki vs agent memory

## Source
- https://x.com/akshay_pachaar/status/2054564519280804028 (Hermes Agent Masterclass by Akshay 🚀)
- Engagement: widely shared thread on X (45K+ likes)