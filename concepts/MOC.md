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

### [[context-engineering]]
**Curating the optimal set of tokens in context — attention as finite budget.** Observation masking, LLM summarization, hybrid strategies. Anthropic + JetBrains research. The follow-up to prompt engineering once prompts get long enough to be a real engineering problem.
**Tags:** #agent-design #context-management

### [[model-context-protocol]]
**Open protocol (JSON-RPC 2.0) for connecting AI applications to data sources and tools.** The USB-C of AI integration. Donated by Anthropic to the Linux Foundation's Agentic AI Foundation; adopted by OpenAI, Google, Microsoft.
**Tags:** #protocol #tool-integration #standards

### [[krea-2-image-generation]]
**Krea 2 — foundation image generation model trained from scratch for aesthetic quality and fine control.** Style transfer, moodboard input, adjustable creativity. Reference implementation of image-gen as a first-class tool capability.
**Tags:** #creative-ai #image-generation

---

## Related MOCs
- [[engineering/MOC]] — Engineering practices derived from these concepts
- [[SYSTEM/MOC]] — KB infrastructure that supports these concepts

## Open Questions
- Should `concepts/` and `engineering/` have more explicit boundary? Some cards (e.g., agentic-coding-principles) could live in either.
- How do we handle concepts that come from multiple sources (e.g., retrieval-first shows up in both CyrilXBT and Karpathy)?
- Do we need a `card-quality.md` standard to evaluate whether a concept card is "complete"?

## Card Count
8 cards (as of 2026-06-21)

## Last Updated
2026-06-21 (weekly review: added context-engineering, model-context-protocol, krea-2-image-generation — these were added to the KB on 2026-06-07 and 2026-05-28 but the MOC was never refreshed)