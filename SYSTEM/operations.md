---
type: system
status: active
date: 2026-05-20
purpose: Decision log — why I placed things where, what I considered, what I skipped, outcomes. Append-only.
tags: [kb-infrastructure]
---

# KB Operations Log

Running log of KB maintenance decisions. Append-only — never edit past entries.

Format:
```
## [DATE] — [Action]: [Card/Topic]
Source: [URL or context]
Decision: [why I placed it here vs elsewhere, what I considered]
Outcome: [pushed immediately / queued / deprioritized]
```

---

## 2026-05-20 — KB Maintenance Upgrade: Obsidian OS Pattern Applied
Source: Self-initiated review of `concepts/personal-operating-system.md` (CyrilXBT's Obsidian OS)
Decision: Applied 6 upgrades to my KB maintenance workflow (SYSTEM/ folder, operations log, folder rules, archive never-delete, queue folder, updated AGENTS.md)
Outcome: Pushed to GitHub (knowledge-base & workspace)

<!-- Add entries below -->

## 2026-05-25 — KB Evolution Implementation: Frontmatter + MOCs
Source: Self-initiated based on `engineering/kb-evolution-plan.md`
Decision: Created `KB-EVOLUTION-DESIGN.md` as working design before implementing. Decided to skip INBOX folder (our KB is Cerebro-managed, not human-capture, so INBOX adds complexity without solving a real problem). Retroactive frontmatter added opportunistically during MOC creation rather than mass-script.
Outcome: Pushed to GitHub. All 26 content cards now have YAML frontmatter. 3 MOCs created. SYSTEM files also got frontmatter (type=system, purpose= instead of summary=).
## [2026-05-21] Anthropic Claude Code talk — 3 engineering cards
**Material:** Anthropic conference talk (YouTube video, 46 min, Welsh Whisper transcription)
**Decisions:**
- All 3 cards go to `engineering/` — they're about engineering practices and implementation patterns, not pure concepts or mental models
- `agentic-coding-tools.md` — covers tool usage, production numbers, new features; intended as a practical reference when tackling issues or building skills
- `advisor-pattern-for-ai-agents.md` — directly relevant to tool/skill building (which model for which task type)
- `scaffolding-for-ai-agents.md` — directly relevant to being efficient and secure (how tooling determines outcomes; security scanning in context)
- Linked existing cards (`agentic-coding-principles`, `vibe-coder-security-checklist`) via See Also
**Outcome:** All 3 pushed to GitHub.

## [2026-05-21] Skill-building docs from Anthropic + OpenAI
**Material:** Two PDFs shared by Andreas
**Decisions:**
- All 4 cards go to `engineering/` — they're engineering practices, not concepts or mental models
- `progressive-disclosure-pattern` — the core architectural pattern from the Anthropic guide; this is the most important one from that doc
- `skill-trigger-design` — directly actionable; how to write descriptions that trigger correctly
- `ask-mode-before-code-mode` — from OpenAI doc; directly applicable when tackling complex tasks
- `agents-md-for-persistent-context` — from OpenAI doc; already something I do partially but this clarifies the pattern
- Stored both PDFs in `raw/` — Andreas confirmed raw material should be kept alongside synthesized cards
**Outcome:** All pushed to GitHub.

## [2026-05-24] Weekly KB Review — Maintenance + Gap Analysis
**Review scope:** Queue check, wikilink validation, orphaned cards, gap analysis, state update
**Decisions:**
- `hermes-saas-audit.md` → archived (`.archive/`) — stale audit doc from 2025-05-18, not a KB card
- Added wikilink to `raw/anthropic-building-skills-guide.md` from `engineering/skill-trigger-design.md`
- Added wikilink to `raw/openai-how-openai-uses-codex.md` from `engineering/agentic-coding-tools.md`
- Updated `memory/kb-state.md` with review findings
- All wikilinks validated ✅ (no broken links found)
**Gaps identified:**
- No "evaluations/evals" card — **FILLED ✅** (see `engineering/evals-ai-evaluation-frameworks.md`)
- No "context window management" card — still pending
- No "tool use/tool definition" card — still pending
- No "model routing" card — still pending
- No "observability/telemetry" card — still pending
- No "chain-of-thought" card — still pending
**Outcome:** Pushed to GitHub.

## [2026-05-25] CyrilXBT Obsidian Vault Organization — 3 cards + KB evolution decision
**Material:** CyrilXBT X thread on organizing Obsidian vaults for retrieval
**Decisions:**
- `retrieval-first-principle.md` → `concepts/` — pure principle, not tool-specific, fits our concepts folder
- `obsidian-vault-organization.md` → `engineering/` — implementable practice, naming/properties/tagging all fit engineering
- `kb-evolution-plan.md` → `engineering/` — roadmap is an engineering document, not a concept
- All 3 created from single thread, clean material, high quality
- Wikilinks added to: llm-wiki-pattern (related compounding pattern), personal-operating-system (related Obsidian OS), kb-evolution-plan (mutual reference), soul-md-identity-layer (related identity/KB overlap)
- Source tagged: #source/cyrilxbt (applied tagging taxonomy from the card itself)
**KB Evolution Decision:**
Andreas asked to both add this material AND evolve our KB approach. The article describes a properties-driven, retrieval-first system. Our KB is currently a good wiki that could be great with structure. Key gaps: no YAML frontmatter, no tag taxonomy, no MOCs, no inbox processing habit.

Created `kb-evolution-plan.md` with 4 phases:
- Phase 1 (Foundation): frontmatter standard + tag taxonomy + opportunistic backlog tagging
- Phase 2 (Structure): naming convention + first MOCs + INBOX activation
- Phase 3 (Maintenance): inbox processing habit + quarterly review routine
- Phase 4 (Intelligence): Claude integration + Dataview queries + automated reports

Going forward, ALL new cards will have YAML frontmatter with type/status/date/tags. Existing cards updated opportunistically as we interact with them.
**Outcome:** All 3 pushed to GitHub.

## [2026-05-25] Cleanup: Remove redundant ## Type headers + move SYSTEM/MOC.md
Source: Self-initiated review triggered by Andreas asking to ensure no conflicts
Issue found: 9 cards had BOTH frontmatter `type:` (new canonical) AND body `## Type` (old redundant). This dual type system created ambiguity — which one is authoritative?
Resolution:
- Frontmatter `type:` is canonical — all type info lives there
- Removed `## Type` and `## Summary` from body of 9 cards (kept body content)
- Kept `## Origin` where it exists (those are source attributions, not type metadata)
- Cards cleaned: concepts/personal-operating-system, concepts/retrieval-first-principle, concepts/self-evolving-skills, concepts/soul-md-identity-layer, engineering/agentic-coding-principles, engineering/hermes-kanban, engineering/hermes-three-tier-memory, engineering/kb-evolution-plan, engineering/obsidian-vault-organization, first-principles/gepa-prompt-evolution
Also: `SYSTEM/MOC.md` was at root level instead of inside `SYSTEM/`. Moved it to `SYSTEM/MOC.md`.
Updated `AGENTS.md` with explicit rule: "Frontmatter is canonical — do NOT duplicate type/summary in body with ## Type / ## Summary headers."
KB is now clean: 25 frontmatter-only cards, no dual type systems, MOC in correct location.
**Outcome:** Pushed to GitHub.

## [2026-05-25] Gstack ingest — 2 cards for Hermes KB reference
**Material:** garrytan/gstack on GitHub — Garry Tan's AI team methodology
**Decisions:**
- `gstack-process-pattern.md` → engineering/ — implementable practice, process discipline, role-based AI team
- `dispatch-routing-tiers.md` → engineering/ — complexity-based routing is an engineering decision framework
- Both are KB reference cards for Hermes, not AGENTS.md instructions
- Natural fit with existing cards: ask-mode-before-code-mode (simplified planning/implement split), advisor-pattern (role separation), scaffolding (tooling determines outcomes)
- Source: #source/garrytan
**KB use case:** Andreas points Hermes to KB for project reference. Cards written so Hermes can read and apply directly.
**Notes:** gstack has 23 skills — we captured the philosophy and process discipline, not the specific slash commands. Hermes can adopt the methodology without implementing the slash command interface.
**Outcome:** Pushed to GitHub.

## 2026-05-28 — Hermes May 27 Updates: MCP Catalog + Krea 2
**Source:** NousResearch X posts (May 27, 2026) — @NousResearch
**Decisions:**
- MCP Catalog → `engineering/` — protocol/architecture for tool integration; MCP is the discovery layer for connecting agents to external systems; natural fit with hermes-three-tier-memory and scaffolding-for-ai-agents
- Krea 2 → `concepts/` — foundation model capability; aesthetic quality + fine control is a capability description, not an engineering practice; fits with personal-operating-system (aesthetic coherence in KM)
- Both tagged #source/nousresearch
- Both designed as Hermes reference cards — Andreas can share with Hermes for context on new features
- Krea 2 cross-linked to personal-operating-system (visual/aesthetic quality matters in personal KM)
**Outcome:** Pushed to GitHub.
