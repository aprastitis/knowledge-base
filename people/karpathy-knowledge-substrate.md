---
type: person
status: active
date: 2026-04-26
summary: Personal KB as persistent second brain for AI agents; KB as substrate for AI to reason in your context
tags: [person, knowledge-management, agent-design]
related: [[llm-wiki-pattern]], [[retrieval-first-principle]]
sources: [internal]
---

# Karpathy's Knowledge Substrate

## Type
People / AI Framework

## Summary
The concept of building a personal, persistent, queryable knowledge base that serves as a "second brain" for AI agents — so that when an AI works on your problems, it's informed by your curated best thinking, not just generic training data. The knowledge base becomes a substrate on top of which AI can reason in your context.

## Core Idea

A personal KB is not a static archive. It's an **active substrate** that:
1. Captures your best distilled thinking (not raw notes, but processed insights)
2. Is structured so an AI with zero context can read it and immediately apply it
3. Gets queried before, during, and after problem-solving
4. Evolves as you encounter new material and refine old concepts

## Why This Matters for AI Agents

When you instruct an AI agent to solve a problem:
- **Without KB**: It reasons from training data, which is generic and may not reflect your actual context, values, or frameworks
- **With KB**: It reads your curated concepts first, then applies your specific mental models to the problem

The KB makes you a more effective collaborator with AI because you don't have to re-explain your framework every time.

## Key Characteristics

### Agent-Readable
- Every concept card should be self-contained: context, summary, application
- No assumed prior knowledge (write as if the AI just woke up with no memory)
- Explicit links between related concepts (wiki-style wikilinks, e.g. `[[llm-wiki-pattern]]`)

### Quality Over Quantity
- Raw material goes in /raw (untouched)
- Only distilled, validated, high-signal content goes in /concepts
- One idea per file, fully developed

### Living Document
- New material may confirm, extend, or conflict with existing cards
- Conflicts are flagged to the owner, not silently resolved
- Changelog tracks evolution

## Implementation in This KB

This knowledge base is built on these principles:
- `/concepts` = primary KB (distilled, agent-readable concept cards)
- `/raw` = drop zone for untouched originals
- `/people` = distilled versions of key thinkers' frameworks
- `sources.md` = provenance of every concept
- `changelog.md` = KB evolution log
- `index.md` = catalog of all pages (updated on every ingest)
- `log.md` = chronological record of KB events

## Key Source: LLM Wiki Pattern

Karpathy's own description of the LLM Wiki pattern is captured in:
- [[llm-wiki-pattern]] — detailed concept card covering the three layers, three operations (ingest/query/lint), and why this beats standard RAG

The pattern is directly implemented here: `/concepts/llm-wiki-pattern.md` is the distilled version of this source.

## Related Concepts
- [[elon-musk-5-step]] — Elon Musk's mental models are a key source for this KB
- [[physics-first-principles]] — First principles reasoning is a core concept
- [[lean-engineering]] — Engineering practices, including lean thinking, are captured

## Sources
- Internal (conversation with Andreas, inspired by Andrej Karpathy's writings on personal KBs for AI)