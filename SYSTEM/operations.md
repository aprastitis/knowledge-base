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
