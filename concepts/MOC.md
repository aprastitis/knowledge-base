# Concepts MOC

## Purpose
Map of Content for all concept cards in `/concepts/`. This MOC provides a topic-cluster view of our concept layer — the distilled, agent-readable ideas that form the conceptual foundation of the knowledge base.

## Tags
#knowledge-management  #status/active

---

## Core Cards

### [[llm-wiki-pattern]]
**LLM-maintained persistent wiki vs RAG.** Three-layer architecture (ingest/query/lint) plus three operations (ingest/query/lint). Compounding pattern: more content → better context → better reasoning → more valuable content.
**Tags:** #knowledge-management #agent-design

### [[retrieval-first-principle]]
**Organize for retrieval, not storage.** Every structural decision — folders, tags, naming — evaluated by whether it makes finding information faster or slower. The core principle driving KB evolution.
**Tags:** #knowledge-management #reasoning

### [[soul-md-identity-layer]]
**Hand-authored static identity file — slot #1 in system prompt.** The fixed frame for memory and skills. Identity lives in SOUL.md, not in model weights. Changes take effect across sessions without retraining.
**Tags:** #agent-design

### [[self-evolving-skills]]
**Agent-authored procedural playbooks with background curation.** The agent writes its own SKILL.md files. Skills are procedural memory; memory is factual memory. Different tiers for different types of knowledge.
**Tags:** #agent-design #knowledge-management

### [[personal-operating-system]]
**Obsidian-based personal OS: 3-layer architecture (storage/intelligence/automation).** Designed to survive bad days and operate autonomously. Combines retrieval-first principles with agentic tooling.
**Tags:** #knowledge-management #agent-design

---

## Related MOCs
- [[engineering/MOC]] — Engineering practices derived from these concepts
- [[SYSTEM/MOC]] — KB infrastructure that supports these concepts

## Open Questions
- Should `concepts/` and `engineering/` have more explicit boundary? Some cards (e.g., agentic-coding-principles) could live in either.
- How do we handle concepts that come from multiple sources (e.g., retrieval-first shows up in both CyrilXBT and Karpathy)?
- Do we need a `card-quality.md` standard to evaluate whether a concept card is "complete"?

## Card Count
5 cards (as of 2026-05-25)

## Last Updated
2026-05-25