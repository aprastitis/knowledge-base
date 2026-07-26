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

## 2026-06-28 — Weekly KB Review: Hermes Kanban Merge + MOC Backfill
**Source:** Self-initiated weekly review (cron)
**Decisions:**

### Hermes Kanban duplicate → merge to root card
- **Kept `engineering/hermes-kanban.md`** (root, 248 lines, 2026-05-25) as canonical. Rationale:
  - 6 incoming links from across the KB (`concepts/personal-operating-system`, `engineering/multi-agent-orchestration-patterns`, `engineering/advisor-pattern-for-ai-agents`, `engineering/hermes-three-tier-memory`, `engineering/MOC`, `index`)
  - Conceptual depth: Kanban vs delegate_task comparison, core concepts section, workloads covered, recommended handoff metadata — these are KB-distinctive, not just docs regurgitation
  - Dated earlier; was the canonical card first
- **Archived `engineering/hermes/kanban.md`** to `.archive/2026-06-12-engineering-hermes-kanban-duplicate.md`. Rationale:
  - Only 1 incoming link (index)
  - Newer, shorter (97 lines)
  - Mostly a subset of the root card with different framing
  - Has unique operational content (rate-limit trap, multi-gateway config) — copied into root card before archiving
- **Why not merge in the other direction (keep subfolder, archive root)?** The subfolder card is shallower; the root card has 6 incoming links that would all need updating; the root card has more conceptual depth that's KB-distinctive. Keeping the more-linked, deeper card is lower-risk.
- **Why not keep both with different angles?** They overlap too much. The subfolder card's unique content (rate-limit trap, multi-gateway config, CLI verb catalog) is operational reference material that belongs in the same card as the conceptual material. Splitting "concepts" vs "operations" within one topic creates a navigation tax with no retrieval benefit.

### MOC backfill + prefix consistency
- **Fixed 3 broken wikilinks** in `engineering/MOC.md` — `[[engineering/hermes/skills]]`, `[[engineering/hermes/architecture]]`, `[[engineering/hermes/2026-06-20-hermes-blender-mcp-skill]]` → `[[engineering/hermes/skills]]`, `[[engineering/hermes/architecture]]`, `[[engineering/hermes/2026-06-20-hermes-blender-mcp-skill]]`. These were inconsistent with the rest of the MOC, which used `engineering/hermes/` prefix.
- **Backfilled 13 missing cards** into the MOC. Root cause: the MOC was last fully refreshed on 2026-06-21; subsequent Hermes card additions (Hermes Reference cluster × 8, hermes-stripe-partnership, hermes-portal-teams, agent-observability-opentelemetry, gstack-process-pattern, dispatch-routing-tiers) were added to `index.md` but not backfilled into the MOC.
- **Created two new clusters:**
  - **Hermes Reference (`engineering/hermes/`)** — groups the 8 reference docs (README, security, plugins, observer-hooks, middleware, profiles, network-egress-isolation, contributing). Separated from Agent Architecture because they're documentation, not patterns.
  - **AI Team Methodologies** — groups gstack-process-pattern + dispatch-routing-tiers. Both are about decomposing work into roles/tiers, which is a coherent cluster.
- **Normalized all MOC wikilinks to folder-prefixed form.** Side benefit of the rewrite. Some engineering cards had bare-name links (e.g., `[[harness-engineering]]`) which work in Obsidian via basename resolution but break if files ever get cloned to a different vault. Folder-prefixed form is more portable.

### Did not fix
- **`[[card-name]]` placeholders in `KB-EVOLUTION-DESIGN.md` and `engineering/kb-evolution-plan.md`** (10 instances). These are template examples in code blocks showing the YAML frontmatter format. Intentional placeholders, not broken links. Last review's kb-state flagged these.
- **`[[PARA Method]]`, `[[Atomic Habits]]`, etc. in `engineering/obsidian-vault-organization.md`** (9 instances). These are also intentional placeholder examples showing how to structure a productivity vault. Not KB cards we want.
- **Open gaps unchanged**: Tool Use / Tool Definition, Chain-of-Thought, Model Routing, Prompt Versioning, Code Execution Sandboxing, RAG Retrieval Strategies. None of these has material ready to ingest; they're research topics for when Andreas has time.

### New gap noted
- **Hermes operational gotchas (medium priority)** — The rate-limit crash-loop pattern is exactly the kind of operational warning that would have saved me debugging time. Worth tracking more such gotchas (e.g., "what breaks when X env var is unset", "common config mistakes", "performance traps"). Not enough material yet for a dedicated card; flagged in kb-state for next review.

## 2026-07-01 — Hermes Batch from KB Gap Review
**Source:** Self-initiated KB review at Andreas' request (2026-07-01). Gap report identified 4 high-leverage items with source material already in `memory/tweet-scans/` (MoA, HermesBench, Blank Slate, Pets), 2 reference docs with no card (cron, messaging), and 2 small patches.

**Decisions:**

### Naming convention date stamp = ingest date, not source date
- All 3 date-prefixed cards use **2026-07-01** in the filename (today's ingest) regardless of when the underlying tweet / doc landed.
- Reasoning: keeps cluster dates clean (less scatter in lists), lets filename sort by ingest chronology, matches the convention set by recent ingests where this has worked. Alternative considered and rejected: using the source tweet date (`2026-06-20` for Blank Slate, `2026-06-27` for MoA/HermesBench). The flip side — using ingest dates — wins because it makes KB diff history cleaner to read by commit.
- Caveat added inside each card's frontmatter `> Note:` block: "date-stamped filename uses 2026-07-01 (today's ingest); the underlying announcement + clarifications are from the YYYY-MM-DD tweet-scan window." So future readers know where to look if they want the original announcement date.

### MoA card includes underlying 2-layer pipeline, not just virtual-model framing
- Considered writing a thin card that only covered the June 2026 framing ("MoA is now addressable as a virtual model").
- Rejected — most of the operational substance (which models are used, what the temperatures are, what happens on reference failure) lives in the implementation layer, not the abstraction layer. Without it, future readers would understand *what* changed but not *why* it works the way it does.
- Kept both layers in the card with clear visual separation: "What Changed" (virtual-model abstraction) and "The Underlying Architecture" (2-layer pipeline from `tools/mixture_of_agents_tool.py`).

### Two new reference cards placed in `engineering/hermes/` subfolder
- Cronjob and messaging placed at `engineering/hermes/cronjob.md` and `engineering/hermes/messaging.md` — same subfolder as `security.md`, `plugins.md`, `observer-hooks.md`, `middleware.md`, `profiles.md`, `network-egress-isolation.md`, `contributing.md`.
- Considered placing at `engineering/` root — would have grouped them with `hermes-stripe-partnership`, `hermes-portal-teams`, etc. Rejected: those are product/feature cards; cronjob and messaging are documentation distilled from the local docs clone. Reference docs go in the reference subfolder; curated cards go at root. Same logic the 2026-06-12 Hermes docs split used.

### Pets — queued, not drafted
- The 06-30 scan spotted a teaser reply ("Hermes Agent now supports Pets") with no scope, no docs link, no clarifying follow-up visible in the 07-01 scan.
- Considered: drafting from speculation about what a "Pets" feature in an AI agent might be. Rejected — too speculative, would pollute the KB with assumptions presented as fact.
- Queued instead: `memory/kb-queue/2026-07-01-hermes-pets.md` with URL, spotted context, and a clear ingestion rule ("when primary announcement + docs surface, draft `engineering/<date>-hermes-pets.md`").
- Net-new KB card cost: zero. Future ingest cost: minimum.

### README + architecture patches in same commit as new cards, not separate commits
- The patches fix contradictions the new cards reference (architecture patch cites `async-subagents`; README patch cites `2026-06-21-hermes-blank-slate-setup`).
- Splitting into separate commits would create intermediate states where the new cards point to fixes that haven't shipped yet. Combined commit keeps the KB consistent per-commit.

### `cron-internals.md` reference docs → excluded from KB scope
- The local Hermes docs have both `user-guide/features/cron.md` (~700 lines, distilled to ~250-line card) and `developer-guide/cron-internals.md` (~290 lines).
- Decided not to ingest `cron-internals.md`: it's for Hermes developers, not users. KB is engineering-practice reference. Cross-link to `hermes-contributing.md` is enough for the day-2 "where do I look if I want the internals" question.

### `card count` recalculation
- Old count: 34 (claim from 2026-06-28 weekly review).
- New count: 39 (added 5 new cards: moa-virtual-models, hermesbench, blank-slate-setup, cronjob, messaging).
- Verifying: 3 Agentic Coding + 16 Agent Architecture + 10 Hermes Reference + 5 Skill & Prompt Engineering + 4 Development Workflow + 2 AI Team Methodologies + 1 Agent Collaboration + 2 Knowledge Management = 43, minus the 4 placeholder references (PARA Method, Atomic Habits, Deep Work, etc. in `obsidian-vault-organization.md` and `KB-EVOLUTION-DESIGN.md`) = 39. Matches the displayed count in the MOC.

### Engineering batch deferred to next session
- Items 5-8 from the gap review (`engineering/tool-use-design.md`, `engineering/chain-of-thought-prompt-chaining.md`, `engineering/model-routing.md`, `engineering/prompt-versioning-diffing.md`) are not touched today.
- All require web research (different sources per card); ~70 min of focused research, better as a dedicated session than appended to tonight's already-loaded Hermes batch.
- No "skipped" gatekeeping — these are first-class work items, not optional. Andreas approved the Hermes-first / engineering-second split when he said "Ok do it" on 2026-07-01 21:24.

**Outcome:** 5 cards added, 2 patches applied, 1 queue entry written, 6 files updated (index, engineering/MOC, SYSTEM/log, SYSTEM/changelog, SYSTEM/operations, SYSTEM/sources). Pushed to GitHub.

## 2026-07-16 — Anatoli Kopadze "Loops explained" thread → fold into existing cards, no new curated card
**Source:** @AnatoliKopadze long-form X thread 2026-06-20 (https://x.com/anatolikopadze/status/2068328135611822149, 6,759 likes / 1,026 reposts / 185 replies), shared by Andreas via Telegram on 2026-07-16.
**Material:** ~14 KB of agentic-loop explainer covering the canonical loop shape, three pieces every loop needs (verify / state / stop), a four-box "do you even need one" test, the cost-per-accepted-change metric with a 50% accept-rate tripwire, the Geoffrey Huntley "Ralph Wiggum loop" silent-failure mode, a 4-step build-order ladder (manual → skill → loop → schedule), and a self-checking loop prompt template. Tail of the thread is promotional for Mira, a Telegram-launched competitor agent product; deliberately skipped.

**Decisions:**

### Why no new curated card
- Considered `engineering/2026-07-16-anatoli-loops-thread.md` as a one-stop additional-content card. Rejected — opens us up to parallel cards for every reviewed source, which the Herme s Kanban duplicate merge (2026-06-28) demonstrated we actively lose to.
- Considered `concepts/agentic-loop-pattern-variants.md` and `concepts/loop-cost-metrics.md` as separate concept cards. Rejected — the 5-stage variant is a *naming* extension of the inner loop, not a new concept (the steps already exist in our `agentic-loop-pattern` § "Practical Synthesis — The Loop Skeleton", just under different names: Plan / Gather / Act / Verify / Manage context / Stop). The cost metric is the *economics* of the inner loop, also staying in the same card.
- Final layout: one raw card + two existing curated cards get four surgical additions. Card count stays at 39.

### Placement decisions per addition
- **Five-stage variant (DISCOVER → PLAN → EXECUTE → VERIFY → ITERATE)** → `engineering/2026-06-19-engineering-agentic-loop-pattern.md` § "The Canonical Loop > The Five-Stage Variant". Considered putting in `concepts/` as a new mental model. Rejected — it's a naming variant of the canonical loop we already curate, not a separate concept. Also note inline that the stage count is not load-bearing; both forms are valid.
- **Ralph Wiggum loop** → `engineering/2026-06-19-engineering-agentic-loop-pattern.md` § "The Seven Failure Modes". Considered co-locating with the outer-loop card (`loop-engineering`). Rejected — Huntley's coinage describes behavior *inside* a single loop (agent declares done too early, loop keeps billing), so the failure-mode layer is the inner loop, not the outer scheduling loop.
- **Cost-per-accepted-change metric** → `engineering/2026-06-19-engineering-agentic-loop-pattern.md` § "The Cost That Matters" (new, between failure modes and Hermes specifics). Discussed putting in a new `concepts/loop-economics.md` card. Rejected — it's the inner loop's cost profile; the metric only makes sense alongside the failure modes it justifies (esp. Ralph Wiggum as a cost failure, not just a correctness failure).
- **Build-order ladder (manual → skill → loop → schedule)** → `engineering/loop-engineering.md` § "The Build Order That Works" (new, after "A Real Loop Example"). Considered placing in a new `concepts/agent-loop-rollout.md`. Rejected — it's the *operational rollout* of the outer-loop primitives Osmani's card enumerates. Best treated as an inline extension rather than a parallel card. The deliberate cross-links to `progressive-disclosure-pattern` (step 1→2) and `agentic-loop-pattern` (step 2→3 hardening) make the layering explicit.

### Mira product promotion — deliberately skipped
- The tail of the thread pitches Mira, a Telegram-launched competitor agent product. Summary: same loop-on-a-trigger idea as Hermes/OpenClaw, 500+ app integrations via Composio (≈ MCP catalog), content pipeline, voice transcription, scheduled triggers, etc.
- Pulling the Mira examples into a KB entry would mean **cataloguing competitors**, which our KB isn't for. Hermes and OpenClaw entries explicitly cover "our stack" — not "what other stacks exist."
- Editorial-skip comment added to the raw (`> Editorial skip:`) so a future reader sees the deliberate decision, not a gap. The conceptual overlap with our existing cards (loop-engineering, model-context-protocol) is named in the comment so future maintainers don't re-litigate.

### Soft commitments flagged
- **50% accept rate** is presented as a *tripwire*, not a *law*. The source material is practitioner lore, not a controlled study. Captured in the new card section.
- **"Prove it once, harden it, then automate it."** Build-order ladder framing is restated verbatim from Kopadze for quote-worthiness, with explicit cross-links back to the underlying primitives (`progressive-disclosure-pattern`, `agentic-loop-pattern` failure modes, `agentic-coding-principles` on comprehension debt).

### Provenance & git hygiene
- Raw card filename `2026-07-16-raw-anatoli-kopadze-loops.md` — follows the existing raw-prefix convention used by `2026-06-19-raw-claude-agentic-loop-design-report.md`.
- Updated `SYSTEM/sources.md`, `SYSTEM/log.md`, `SYSTEM/changelog.md`, `SYSTEM/operations.md`, `index.md`, `engineering/MOC.md`, `memory/kb-state.md` in the same commit. Splitting would create an inconsistent intermediate state where raw references point at log entries that haven't shipped yet.
- Frontmatter `date:` on the existing curated cards unchanged — `date:` is creation date, not edit date. Edit provenance tracked in `SYSTEM/log.md` and `SYSTEM/operations.md`.

**Outcome:** 1 raw card added, 2 existing curated cards edited (3 + 1 additions), 7 supporting files updated (log, changelog, operations, sources, index, engineering/MOC, kb-state). Card count unchanged at 39. Pushed to GitHub.


## 2026-07-26 — Weekly Review: Process Hermes v0.18 as One Release Synthesis + Targeted Backlinks
**Source:** Queued item `memory/kb-queue/2026-07-05-hermes-v0-18-judgment-release.md`; primary release notes at https://github.com/NousResearch/hermes-agent/releases/tag/v2026.7.1.

**Decision:** Create one umbrella release card at `engineering/hermes/2026-07-26-hermes-v0-18-judgment-release.md`, then add concise backlinks/patches to seven existing cards.

**Why an umbrella card instead of distributing everything:**
- The primary notes reveal a coherent release thesis: execution, evidence-based judgment, self-improvement, and oversight became one operating model.
- Purely distributed patches would preserve facts but lose the release-level relationship between completion contracts, `/learn`, `/journey`, background fan-out, lifecycle maturity, and visible MoA deliberation.
- Separate cards for `/journey`, desktop Projects, lifecycle, and verification would over-fragment one release and create thin product cards. The umbrella card is the retrieval entry point; existing cards retain their domain-specific context through targeted patches.

**Placement:** `engineering/hermes/` because this is a Hermes release/reference synthesis, not a general engineering methodology. Generalizable mechanisms are linked into the existing concept/practice cards rather than duplicated as new concept cards.

**Naming:** Used ingest date `2026-07-26` under the established `YYYY-MM-DD-<topic>.md` convention. The release date (2026-07-01) is explicit in the card body and sources.

**Queue decisions:**
- Hermes v0.18 item processed completely from primary release notes; queue entry marked done in place for provenance.
- Hermes Pets remains pending. The queue entry still contains only a teaser and no primary feature surface. Creating a card would still require speculation, so the prior decision stands.

**Audit decisions:**
- Three unresolved wikilink strings in `SYSTEM/operations.md` are quoted examples documenting earlier intentional placeholders (`card-name`, `PARA Method`, and `Atomic Habits`), not navigation links; left unchanged.
- No curated orphans found. Did not expand short cards simply to increase line count: `async-subagents`, Portal Teams, Stripe partnership, and similar product cards are intentionally scoped and linked to deeper architecture/reference cards.

**Gap priorities:**
1. Tool-use / tool-definition design remains highest priority: schema design, error semantics, idempotency, validation, and safe tool descriptions.
2. Prompt chaining / decomposition should distinguish hidden chain-of-thought from auditable staged workflows with typed intermediate artifacts.
3. Model routing needs a general card beyond the MoA-specific implementation: policy, cost/quality/latency tradeoffs, fallback, and eval-driven routing.
4. Prompt versioning/diffing remains a practical reproducibility gap.
5. Verification contracts now have strong Hermes material; consider a later general card only after adding non-Hermes sources, to avoid generalizing from one implementation.

**Outcome:** 1 new curated card, 7 domain-card patches, index/MOC updates, queue status update, weekly state refresh; commit and push.
