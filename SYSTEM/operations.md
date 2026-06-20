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

## 2026-06-20 — Hermes Blender MCP Skill
**Source:** NousResearch tweet scan 2026-06-20 (saved to `memory/tweet-scans/2026-06-20-nousresearch.md`); teaser reply https://x.com/NousResearch/status/2068099500040728918 + official docs https://hermes-agent.nousresearch.com/docs/user-guide/skills/optional/creative/creative-blender-mcp

**Decisions:**
- **Card → `engineering/hermes/2026-06-20-hermes-blender-mcp-skill.md`** — placed in the `engineering/hermes/` subfolder rather than at `engineering/` root because the card is concretely about a specific *optional skill*; the subfolder is where the SKILL.md format docs (`hermes/skills`) and the Hermes architecture docs live, so they're co-located. Other recent Hermes feature cards (Stripe, Portal Teams, Async Subagents) live at `engineering/` root because they're product/architectural changes rather than skill-specific. The Blender MCP card is skill-specific, so it gets the more specific home.
- **Naming followed the established convention** — `YYYY-MM-DD-[type]-[topic].md` per `KB-EVOLUTION-DESIGN.md`. Avoided the 2026-06-08/09 wikilink-rename issue documented in `memory/kb-state.md`. No date-prefix cards at `engineering/` root for Hermes feature cards — they all live in `engineering/hermes/`.
- **Source tagging:** `#source/nousresearch` + raw URL references in frontmatter `sources:` list. Same pattern as the other recent Hermes ingest cards.
- **Caveat transparency:** the tweet was a brief teaser reply ("I've got some news"), not a release announcement. The skill itself already existed at v1.0.0 in the optional catalog. Ingested because (a) it fills a KB gap — no concrete creative-skill catalog entry existed; (b) it's the most explicit example we have of a creative optional skill that bridges to a real desktop app via MCP. Noted in the card's `## Source Notes` section so future readers don't expect this to be a "new feature" announcement.
- **Why not extend `hermes/skills.md` instead of creating a new card?** Considered — `hermes/skills.md` already covers the SKILL.md format and the bundled/optional/hub split. But the Blender MCP card carries concrete implementation specifics (port number, wire format, command list, security caveats, Blender setup steps) that would bloat the format-overview card. The format card stays abstract; the Blender MCP card is a concrete worked example. They're cross-linked via the `See Also` section I added in `hermes/skills.md`.
- **Cross-links chosen deliberately:**
  - `engineering/hermes/skills` — most relevant; the Blender MCP skill *is* a SKILL.md file using the format this card documents
  - `engineering/hermes-native-mcp-catalog` — the catalog this skill rides on; added a `See Also` section in the MCP catalog card pointing to the new card
  - `concepts/model-context-protocol` — the underlying protocol; not heavy-handed, just mentioned as a related concept
  - `engineering/2026-06-19-engineering-agentic-loop-pattern` — the gather→act→verify loop that Blender MCP workflows naturally fall into (mentioned in the card body)
- **Security note added prominently in the card.** `execute_code` runs arbitrary Python in the Blender process. Sandbox = host OS, no approval gate. Flagged this in a dedicated `## Security / Safety Notes` section so anyone considering running this skill in a Hermes session sees the warning before reaching for it on production `.blend` files.

**Caveats I added to the card (transparency):**
- Tweet was a teaser, not a release — surfaced in `## Source Notes`.
- Skill already existed at v1.0.0 — surfaced alongside the KB-gap justification.
- `execute_code` safety — surfaced in its own section, not buried in prose.

**Outgoing links added (4 cards):** `engineering/hermes/skills`, `engineering/hermes-native-mcp-catalog`, `concepts/model-context-protocol`, `engineering/2026-06-19-engineering-agentic-loop-pattern`.

**Indexing:** added row to `index.md` (Engineering → Skills category), added row to `engineering/MOC.md` (Agent Architecture cluster), updated last-updated dates on both, and updated `SYSTEM/changelog.md` + this log.

**Outcome:** Pushed to GitHub.

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

## 2026-06-09 — Loop Engineering Card Added
**Source:** X thread by [@addyosmani](https://x.com/addyosmani/status/2064127981161959567), 2026-06-08

**What I did:**
- Read full tweet via `bird read --auth-token $AUTH_TOKEN --ct0 $CT0` (needed explicit flag passing, not just env sourcing)
- Created `engineering/2026-06-09-engineering-loop-engineering.md`
- Added backlinks to 6 related cards: harness-engineering (successor relationship), multi-agent-orchestration-patterns (patterns compose into loops), skill-trigger-design (skills as loop primitive), scaffolding-for-ai-agents (external memory principle), model-context-protocol (MCP connectors), agentic-coding-principles (comprehension debt / verification risks)
- Updated `index.md` and `SYSTEM/changelog.md`

**Placement decision:**
- `engineering/` — loop engineering is an engineering methodology, not a concept or mental model. Fits alongside harness-engineering (its direct predecessor) and multi-agent-orchestration-patterns (the patterns it composes).
- Not `concepts/` — despite having philosophical elements (comprehension debt, cognitive surrender), it's fundamentally a set of implementable practices with concrete building blocks.

**Why this matters for our KB:**
- This is the most concrete, actionable framing of "AI-assisted engineering at scale" we've seen — not principles, but a literal system you can build
- The 6 primitives map directly to features Hermes already has or could have: automations (cron/scheduled), worktrees (git), skills (SKILL.md), MCP connectors (MCP servers), sub-agents (multi-agent kanban), external memory (memory layer)
- The "build the loop like someone who intends to stay the engineer" framing is the best single-sentence articulation of the human-in-the-loop principle we've captured

**Considered alternatives:**
- Could have extended the harness-engineering card rather than creating a new one. Decided against it — loop engineering is a distinct discipline with its own identity, and a new card makes it findable on its own terms.
- Could have put it in `concepts/` as a mental model. Rejected — it's too prescriptive and implementable for that folder.

**Wikilinks added to:** harness-engineering, multi-agent-orchestration-patterns, skill-trigger-design, scaffolding-for-ai-agents, model-context-protocol, agentic-coding-principles
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

## 2026-06-07 — Research Sprint: 4 Cards from Gap Analysis
**Trigger:** Weekly KB review (Sunday 2026-06-07). Checked `memory/kb-state.md` — last review was 2026-06-07, gaps still unfilled.
**Research approach:**
- Did web searches on 3 gap areas: context window management, tool use design, chain-of-thought
- Hit Brave rate limit; pivoted to fetching known-good URLs (Anthropic blog, JetBrains Research, Digital Applied, Microsoft Learn)
- Fetched 4 high-quality sources in parallel
**Decisions:**
1. `concepts/context-engineering.md` → `concepts/` — fundamental principle; fits alongside `retrieval-first-principle` (both are "organize/curate for X not Y" principles)
2. `concepts/model-context-protocol.md` → `concepts/` — open protocol description; fits as a framework/concept, not engineering practice
3. `engineering/agent-observability-opentelemetry.md` → `engineering/` — operational engineering practice; OTel implementation details
4. `engineering/multi-agent-orchestration-patterns.md` → `engineering/` — implementable patterns; natural fit with gstack (magentic pattern) and hermes-kanban (peer coordination)
**Why these 4 first:**
- Context engineering: #1 gap from KB state, most foundational — attention management affects every agent interaction
- Agent observability: #5 gap, increasingly critical as agents go to production
- MCP: industry standard as of 2026, supplements existing hermes-native-mcp-catalog card (implementation vs protocol)
- Multi-agent patterns: fills gap #6, maps to existing Hermes tools (kanban, gstack)
**Skipped for now:** tool use design (#2 gap) — needed more research on best practices; will revisit
**Tagged:** #source/web-research on all 4
**Cross-links added:**
- context-engineering → llm-wiki-pattern, scaffolding-for-ai-agents, ask-mode-before-code-mode, self-evolving-skills
- agent-observability → scaffolding-for-ai-agents, agentic-coding-principles, evals-ai-evaluation-frameworks, self-evolving-skills
- model-context-protocol → hermes-native-mcp-catalog, scaffolding-for-ai-agents, context-engineering, agentic-coding-tools
- multi-agent-orchestration-patterns → gstack-process-pattern, hermes-kanban, dispatch-routing-tiers, context-engineering
**Outcome:** Pushed to GitHub.
## 2026-06-12 — Hermes Full Documentation Added to KB
**Source:** Andreas asked to add the entire Hermes documentation from the local clone to the KB
**Decision:** Created `engineering/hermes/` subfolder with 11 cards covering all major documentation areas
**Decisions made:**
- Placed all in `engineering/hermes/` — Hermes is a specific technical tool/agent framework, documentation belongs in engineering
- Split source README.md into: README (overview), architecture (core loop + project structure), security, plugins, skills, observer-hooks, middleware, profiles, kanban, network-egress-isolation, contributing
- Used existing `engineering/hermes-kanban.md` content as reference; now consolidated into `engineering/hermes/kanban.md`
- network-egress-isolation → `security/` tag (it's a security doc)
- observer-hooks and middleware → both telemetry-related but different purposes (read-only vs behavior-changing)
**Policy change:** From now on, Hermes updates go directly into `engineering/hermes/` section. Andreas no longer needs separate RECOVERY.md in workspace — the KB is the canonical reference.
**Outcome:** Pushed to GitHub.

## 2026-06-08 — Harness Engineering Card Added

**Source:** X thread by [@sairahul1](https://x.com/sairahul1/status/2063544956158185927), 2026-06-07

**What I did:**
- Read full X thread via `bird read` (credentials sourced from skills/bird-twitter/.env)
- Created `engineering/2026-06-08-engineering-harness-engineering.md`
- Added wikilink to `agentic-coding-principles.md` and `agentic-coding-tools.md` (backlinks)
- Added entry to `index.md` under Engineering Practice

**Placement decision:**
- `engineering/` — This is a concrete engineering methodology/discipline, not a pure concept. Fits alongside agentic-coding-principles, agentic-coding-tools, and evals.
- Not `first-principles/` — While it has philosophical elements (build-to-delete, context beats instructions), it's fundamentally a set of implementable practices and artifacts.
- Not `mental-models/` — It's more prescriptive and concrete than a general reasoning framework.

**Considered alternatives:**
- Could have gone in `concepts/` as a new mental model, but the folder rules say "mental models, not implementable engineering practices" for mental-models. This is clearly engineering.

**Related cards already cross-linked:** agentic-coding-principles, agentic-coding-tools, evals-ai-evaluation-frameworks, lean-engineering

## 2026-06-16 — Hermes June 16 Updates: Async Subagents + Stripe Partnership
**Source:** NousResearch/Hermes scan 2026-06-16 (shared by Andreas)
**Decisions:**
- `async-subagents.md` → `engineering/` — architectural change to a core tool (delegate_task); tagged Agent Architecture; linked to multi-agent-orchestration-patterns (concurrent pattern it enables) and hermes/architecture
- `hermes-stripe-partnership.md` → `engineering/` — new Hermes capabilities; tagged Agent Framework; linked to hermes/skills and hermes/plugins
- Both from same Hermes scan, processed together
- Async subagents is a material change — prior KB explicitly described delegate_task as synchronous; the new card notes this supersedes that description
- Source tagged: #source/nousresearch
**Outcome:** Pushed to GitHub.

## 2026-06-18 — Hermes Portal Teams: Team Billing
**Source:** NousResearch tweet scan 2026-06-18 (Andreas confirmed worth KB-ing)
**Decision:**
- Card → `engineering/2026-06-18-hermes-portal-teams.md` — this is a Hermes Portal product feature, fits with other Hermes product updates in engineering/hermes/ section
- Tagged: hermes, billing, enterprise, agent-framework, team-deployment, cost-management
- Linked to: hermes-stripe-partnership, hermes/profiles, hermes/architecture, async-subagents, multi-agent-orchestration-patterns
- Source: #source/nousresearch
**Why here and not concepts:** It's a product feature with specific capabilities (credit pools, spend caps, dashboard), not a conceptual framework. Engineering is right.
**Workflow improvement:** This was the first time I processed a saved tweet scan report from `memory/tweet-scans/`. The cron now saves full reports there so Andreas can say "KB this" later instead of processing immediately. File-based context solved the isolation problem.
**Outcome:** Pushed to GitHub.

## 2026-06-19 — Claude research report: Agentic-Loop Design for Hermes
**Source:** Andreas asked Claude to produce a research report; URL: https://claude.ai/public/artifacts/72bd1d35-98b4-466b-81e4-71cc25aece54
**Decisions:**

- **Two-card split (raw + engineering).** Followed the precedent set by `anthropic-building-skills-guide` (raw) and `progressive-disclosure-pattern` / `skill-trigger-design` (engineering cards distilled from it). The raw/ card is auditable; the engineering/ card is what shows up in retrieval.

- **Card name: `agentic-loop-pattern`.** Considered `agent-loop-design` and `inner-loop-pattern`. Chose `agentic-loop-pattern` because (a) it parallels the `loop-engineering` naming, (b) "inner-loop" might confuse with `async-subagents` and outer-loop scheduling, and (c) "agentic loop" is the term the report and both Anthropic/OpenAI use.

- **Why this is a new card, not an extension of `loop-engineering`.** `loop-engineering` (Addy Osmani, 2026-06-08) is the *outer* loop — the system that schedules agents. The new card is the *inner* loop — the per-task iteration cycle inside a single agent (gather → act → verify → repeat). Different layer, different problems, different primitives. Adding it to loop-engineering would muddy the loop-engineering card's clean "outer loop" framing.

- **Why this is engineering/ and not concepts/.** Engineering practice methodology with specific implementable controls (max_turns, compaction thresholds, four-part delegation contract, the six failure modes). Concepts/ cards are abstract frameworks; this is a set of tunable engineering primitives. Same reasoning as `harness-engineering` and `loop-engineering` going to engineering/.

- **Source tagging: `#source/claude-artifact`** for the curated card. The card is a synthesis *of* the report, not first-party from Anthropic/OpenAI/Nous. Used additional tags `source/anthropic`, `source/openai`, `source/nousresearch`, `MiniMax` to capture the underlying primary sources the report drew from. This matches the precedent where `harness-engineering` is tagged with its X-thread source, not with the primary sources the X thread summarized.

- **Cleanup decision: 58 citation icon URLs stripped from raw.** The Claude artifact rendering embeds decorative images like `![](claude-citation:/icon.png?validation=...&citation=BASE64 "Anthropic")` to surface source links. I parsed each to extract the label and the real URL, then wrote the result back as standard markdown `[Anthropic](https://...)`. The raw file is now 38 KB (was 95 KB) and is readable; all 58 source links preserved. This is a deviation from strict "verbatim" — the textual content is identical, but the decorative image markup is gone. Justified because: (a) raw/ is for *content*, not for the Claude.ai HTML rendering format, (b) the existing precedent of `anthropic-building-skills-guide` in raw/ is a PDF placeholder, not the actual binary, so the folder already treats "raw" as "preserved source material" not "byte-for-byte artifact."

- **Did NOT extract a separate card for the "Stage 1–5 implementation plan" or the "Benchmarks that should change your design" section.** Both overlap with existing cards: `evals-ai-evaluation-frameworks` (eval set construction), `skill-trigger-design` (description as router), `loop-engineering` (build-evaluate-iterate), `agentic-coding-principles` (verification and failure modes). The new card links to these rather than duplicating.

- **Did NOT create a separate card for the "Loop design by task type" section** (coding / research / file-ops / web-automation). Same reasoning — coding loops are well-covered by `agentic-coding-principles` and `harness-engineering`; research loops by `multi-agent-orchestration-patterns`; file-ops by `hermes-kanban`. The new card mentions the task-type playbooks briefly and points to the existing coverage.

- **Did NOT create a separate "Hermes Agent" or "MiniMax" card.** Both are already well-covered: Hermes by `engineering/hermes/*` (11 cards in the dedicated subfolder), MiniMax by this card and the existing `engineering/MOC.md` cluster. Adding a top-level Hermes or MiniMax card would either duplicate or shadow the existing structure.

**Caveats I added to the card (transparency):**
- Vendor-reported numbers flagged as directional, not audited.
- Hermes doc/code lag noted.
- "Dumb loop" framing caveat added to prevent the wrong takeaway ("just write the harness and don't worry about the prompt") — the prompt/tools/description are where most failures actually are.
- Multi-agent cost (~15× tokens) made prominent so we don't reflexively reach for it.

**Outgoing links added (10 cards):** loop-engineering, harness-engineering, multi-agent-orchestration-patterns, progressive-disclosure-pattern, skill-trigger-design, hermes/skills, hermes/architecture, async-subagents, context-engineering, scaffolding-for-ai-agents, agentic-coding-principles, plus back-link to the new raw card.

**Outcome:** Pushed to GitHub.
