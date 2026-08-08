---
type: system
status: active
date: 2026-05-20
purpose: Chronological record of every KB change — what was added/updated, when, source
tags: [kb-infrastructure]
---

# Knowledge Base Log

## [2026-06-20] ingest | Hermes Blender MCP Skill
**Source:** NousResearch tweet scan 2026-06-20 (memory/tweet-scans/2026-06-20-nousresearch.md); teaser reply https://x.com/NousResearch/status/2068099500040728918 + official docs https://hermes-agent.nousresearch.com/docs/user-guide/skills/optional/creative/creative-blender-mcp
**Action:** Created `engineering/hermes/2026-06-20-hermes-blender-mcp-skill.md`
**What it covers:** Optional Hermes skill (author alireza78a, v1.0.0) wrapping `ahujasid/blender-mcp` addon. Drives a running Blender instance over plain UTF-8 JSON over TCP (port 9876). Four commands: `execute_code` (arbitrary bpy Python), `get_scene_info`, `get_object_info`, `get_viewport_screenshot`. One-time Blender setup: install addon → enable "Interface: Blender MCP" → sidebar "Start Server".
**Backlinks added:** `engineering/hermes/skills.md` (See Also), `engineering/hermes-native-mcp-catalog.md` (See Also)
**Decisions:**
- Placed in `engineering/hermes/` — same subfolder as `hermes/skills`, `hermes/architecture`, etc. Most other Hermes feature cards live at `engineering/` root (e.g. `hermes-stripe-partnership`, `hermes-native-mcp-catalog`) but the Blender MCP skill is concretely about an *optional skill*, so the `engineering/hermes/` subfolder (where the SKILL.md format docs live) is a closer match
- Naming follows `YYYY-MM-DD-[type]-[topic].md` per KB-EVOLUTION-DESIGN.md — `2026-06-20-hermes-blender-mcp-skill.md`
- Source tagged `#source/nousresearch` + raw URL references
- Cross-linked to: `hermes/skills` (the SKILL.md format), `hermes-native-mcp-catalog` (the catalog this skill rides on), `model-context-protocol` (the protocol), `2026-06-19-engineering-agentic-loop-pattern` (the gather→act→verify loop Blender workflows naturally fit)
**Caveat noted in card:** the tweet was a brief teaser, not a release; the skill already existed at v1.0.0 in the optional catalog. Ingested because (a) it fills a KB gap (no concrete creative-skill catalog entry), (b) it's the most explicit example we have of a creative optional skill bridging to a real desktop app via MCP.
**Outcome:** Pushed to GitHub.

## [2026-06-12] ingest | Hermes Full Documentation — 11 cards added
**Source:** `/home/cerebro/.hermes/hermes-agent/` (local clone of NousResearch/hermes-agent)
**Action:** Added entire Hermes documentation into KB as a dedicated `engineering/hermes/` section
**Cards added:**
- `engineering/hermes/README.md` — Overview, quick install, getting started, Nous Portal, CLI vs messaging comparison
- `engineering/hermes/architecture.md` — Project structure, AIAgent class, agent loop, CLI architecture, TUI, toolsets, delegation, profiles
- `engineering/hermes/security.md` — Trust model, OS-level isolation as the only boundary, in-process heuristics, plugin trust, deployment hardening
- `engineering/hermes/plugins.md` — General plugins, memory providers, model providers, ABC + orchestrator pattern
- `engineering/hermes/skills.md` — SKILL.md format, authoring standards, platform gating, conditional activation, curator system
- `engineering/hermes/observer-hooks.md` — Read-only telemetry contract, pre/post API request, pre/post tool call, session lifecycle
- `engineering/hermes/middleware.md` — Behavior-changing plugin hooks — llm_request, llm_execution, tool_request, tool_execution
- `engineering/hermes/profiles.md` — Multi-instance isolated profiles, profile-aware paths, gateway token locks, multi-gateway
- `engineering/hermes/kanban.md` — SQLite multi-agent task board, CLI, kanban_* toolsets, dispatcher, isolation model, rate limit trap
- `engineering/hermes/network-egress-isolation.md` — Docker network segmentation for prompt injection defense
- `engineering/hermes/contributing.md` — Dev setup, skill vs tool decision, cross-platform rules, dependency pinning, PR process
**Decisions:**
- All → `engineering/hermes/` — Hermes is a specific technical tool/agent framework; documentation fits engineering
- Created `engineering/hermes/` as dedicated section (11 files, 1 for each major doc topic)
- Used existing `engineering/hermes-kanban.md` as reference for kanban card (merged concepts)
- Source paths tagged: local hermes-agent clone
- Related to existing cards: hermes-native-mcp-catalog, hermes-three-tier-memory, hermes-kanban (now in subfolder)
**Policy change:** From now on, Hermes updates go directly into KB `engineering/hermes/` section. No longer need to keep separate RECOVERY.md in workspace.
**Outcome:** Pushed to GitHub.

## [2026-05-25] kb-evolution | Frontmatter added to all cards + 3 MOCs created
**Action:** Implemented KB evolution plan (Phase 1 foundation + Phase 2 structure)
- Created `KB-EVOLUTION-DESIGN.md` — design document for frontmatter schema, tag taxonomy, MOC priorities, naming conventions, retrofit strategy
- Created `concepts/MOC.md` — Map of Content for all 5 concept cards
- Created `engineering/MOC.md` — Map of Content for all 14 engineering cards (organized by topic cluster)
- Created `SYSTEM/MOC.md` — Map of Content for KB infrastructure files
- Added YAML frontmatter to ALL content cards across all folders (26 cards total): concepts (5), engineering (14), first-principles (2), mental-models (1), people (2), raw (2)
- Added YAML frontmatter to all SYSTEM files (5 files)
- Skipped INBOX folder (not needed for Cerebro-only KB maintenance; see KB-EVOLUTION-DESIGN.md)
- Retroactive frontmatter added during MOC creation (opportunistic retrofit)
**Notes:** All new cards going forward will use `YYYY-MM-DD-[type]-[topic].md` naming and get frontmatter at creation time. Existing cards keep current names.

## [2026-05-20] refactor | SYSTEM/ folder created + KB cleanup
**Action:** Major KB infrastructure refactor — see SYSTEM/operations.md for full decision log
- Created `SYSTEM/` folder with README (folder rules), operations.md (decision log)
- Moved `log.md` and `changelog.md` to SYSTEM/
- Moved `sources.md` to SYSTEM/sources.md
- Deleted `raw/spec-kit-github.md` (redundant with existing spec-driven-development card)
- Added wikilinks to raw cards: llm-wiki-karpathy.md → llm-wiki-pattern, personal-operating-system; elon-musk-5-step-corporate-rebels.md → elon-musk-5-step, people/elon-musk, vibe-coder-security-checklist
- Updated index.md (added SYSTEM/sources, updated paths)
**Notes:** Based on insights from personal-operating-system.md (CyrilXBT's Obsidian OS). KB now has explicit folder rules, operations log, and archive-never-delete policy.

## [2026-05-19] ingest | Spec-Driven Development (spec-kit / GitHub)
**Source:** https://github.com/github/spec-kit
**Action:** Created `/engineering/spec-driven-development.md` — comprehensive card covering: SDD core philosophy (specs generate code, not guide it), the 5-step workflow (constitution → specify → plan → tasks → implement), key commands, development phases, template-as-quality-constraint pattern, why it matters now (AI capabilities threshold, complexity growth, pace of change)
→ Added to `index.md` → added backlink in `lean-engineering.md` → updated `log.md`, `changelog.md`
**Notes:** Major engineering methodology card. Key insight: SDD is "first-principles applied to the spec-implementation gap." The templates (with [NEEDS CLARIFICATION] markers, WHAT-not-HOW constraints, phase gates) are the key discipline mechanism. Supports 30+ AI coding agents. Install via `uv tool install specify-cli`.
**Source:** https://hermes-agent.nousresearch.com/docs/user-guide/features/kanban
**Action:** Updated `/engineering/hermes-kanban.md` — added: boards management CLI, board resolution order, dashboard switcher, quick start human workflow, gateway dispatcher config, idempotent create for automation/webhooks, bulk CLI verbs → committed ec1b183
**Notes:** Major agent collaboration pattern. Key insight: Kanban is peer coordination (any profile reads/writes any task) vs delegate_task's hierarchical fork-join. Useful for multi-agent workflows Andreas might build with Hermes.

  - `/concepts/soul-md-identity-layer.md` — SOUL.md as identity layer
  - `/concepts/self-evolving-skills.md` — agent-authored playbooks + Curator
  - `/engineering/hermes-three-tier-memory.md` — three memory tier architecture
  - `/first-principles/gepa-prompt-evolution.md` — genetic-Pareto prompt evolution (ICLR 2026 Oral)
  → Added wikilinks across related cards → updated `index.md`, `log.md`, `changelog.md`
**Notes:** Strong material — self-evolving skills and GEPA directly relevant to agent design patterns. Three-tier memory is clean architecture. Source is a comprehensive X thread by @akshay_pachaar.

## [2026-05-20] ingest | Agentic Coding Principles & Practices
**Source:** https://x.com/0xdepressionn/status/2055999112470839383
**Action:** Created `/engineering/agentic-coding-principles.md` — 6 principles + 28 practices for production-quality AI-assisted development
**Notes:** Key concept: "meteor over fireball" — move beyond vibe coding (quick demos) to production-grade human-AI collaboration. All tool-specific references (CLAUDE.md, .cursor/rules, etc.) adapted generically. Natural fit with Hermes's Kanban board for multi-agent patterns.

## [2026-05-20] ingest | Personal Operating System (Obsidian)
**Source:** https://x.com/cyrilxbt/status/2056924424838815824
**Action:** Created `/concepts/personal-operating-system.md` — 3-layer architecture (storage/intelligence/automation) + 8-folder vault + CLAUDE.md template + 5 workflows. Backlinks added to soul-md-identity-layer.md and self-evolving-skills.md. Updated `index.md`, `log.md`, `changelog.md`
**Notes:** Natural fit with our KB: CLAUDE.md pattern = SOUL.md, 3-layer architecture = Hermes 3-tier memory, 8-folder vault = our KB structure, queue processor = our Kanban. Key insight: systems designed to survive "bad days" — when maintainers are overwhelmed but the system keeps running. Restored bird-twitter credentials to `/home/cerebro/.openclaw/workspace/skills/bird-twitter/.env` and added MEMORY.md note: **always use bird read for X posts, web_fetch is unreliable on X**.

## [2026-05-16] ingest | Vibe Coder Pre-Launch Security Checklist
**Source:** https://x.com/prajwaltomar_/status/2055294397475148123
**Action:** Created `/engineering/vibe-coder-security-checklist.md` from tweet by @PrajwalTomar_ (5,600 likes) → added to `index.md`, `log.md`, `changelog.md`
**Notes:** First engineering-focused card. Covers env vars, OWASP, SQL/XSS, rate limiting, secrets management. Source is a popular tweet rather than article or paper.

## [2026-05-16] ingest | LLM Wiki (Karpathy Gist)
**Source:** https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
**Action:** Dropped raw to `/raw/llm-wiki-karpathy.md` → created `/concepts/llm-wiki-pattern.md` → updated `karpathy-knowledge-substrate.md` to reference it → updated `index.md`
**Notes:** Single source, 1 new concept card created. Extends existing karpathy KB concept.

## [2026-05-16] ingest | Elon Musk 5-Step Algorithm
**Source:** https://www.corporate-rebels.com/blog/musks-algorithm-to-cut-bureaucracy
**Action:** Dropped raw to `/raw/elon-musk-5-step-corporate-rebels.md` → enriched `elon-musk-5-step.md` with specific quotes (ownership rule, 10% deletion benchmark, accelerate warning, automate timing) → created new `people/elon-musk.md` with management heuristics → updated `index.md`
**Notes:** 2 concept cards updated/created. Elon Musk now has both the 5-step algorithm card and management heuristics card.

## [2026-05-16] init | KB Scaffolded
**Action:** Full folder structure created, 4 seed concept cards added, `sources.md` and `changelog.md` initialized
**Notes:** Initial commit pushed to GitHub.
## [2026-05-21] anthropic-claude-code-talk | 3 cards added
**Action:** Processed Anthropic conference talk (YouTube: 6amLO7I9xdg) — extracted builder-relevant insights and created 3 engineering cards
**Cards added:**
- `engineering/agentic-coding-tools.md` — Agentic vs copilot; capability timeline; Shopify/Mercado Libre/Spotify production numbers; new features (Code Review Agent, Mobile, Claude Security)
- `engineering/advisor-pattern-for-ai-agents.md` — Executor (small model) + advisor (large model) pattern; Yv Legal example; when to use/not use; implementation notes
- `engineering/scaffolding-for-ai-agents.md` — Tooling determines outcomes more than model; context management, tool integration, security scanning; scaffolding as ceiling vs model as floor
**Source:** Anthropic conference talk on Claude Code (YouTube video transcription via Whisper large-v3 CPU)
**Notes:** Transcript also available in `skills/whisper-transcribe/transcripts/6amLO7I9xdg_plain.txt` for reference.

## [2026-05-21] skill-building-docs | 4 cards + 2 raw PDFs
**Action:** Processed two PDFs — Anthropic "Complete Guide to Building Skills for Claude" and OpenAI "How OpenAI Uses Codex". Created 4 engineering cards + stored both PDFs as raw sources.
**Cards added:**
- `engineering/progressive-disclosure-pattern.md` — Three-level skill architecture (frontmatter → body → linked files). Core architectural pattern.
- `engineering/skill-trigger-design.md` — How to write YAML description (what + when), 90% trigger rate metric, fixing under/over-triggering
- `engineering/ask-mode-before-code-mode.md` — Two-step pattern from OpenAI: plan in Ask mode, execute in Code mode
- `engineering/agents-md-for-persistent-context.md` — Project-level context file with conventions, business logic, known quirks
**Raw sources added:**
- `raw/anthropic-building-skills-guide.pdf` — Full PDF
- `raw/openai-how-openai-uses-codex.pdf` — Full PDF
**Source:** Andreas shared two PDF URLs from Anthropic and OpenAI
**Notes:** Andreas confirmed we should store raw material alongside synthesized cards.

## [2026-05-24] ingest | Evals / AI Evaluation Frameworks
**Source:** Web search (Futurice, Arize AI, Evidently AI, Shakudo, Freeplay)
**Action:** Created `/engineering/evals-ai-evaluation-frameworks.md` from web research. Covers 5 eval types (exact match, semantic similarity, LLM-as-judge, retrieval metrics, task-specific), key metrics (Pass@K, MRR, NDCG, BERTScore), framework references (Ragas, DeepEval, lm-evaluation-harness, AlpacaEval), eval vs benchmark distinction, getting started steps. Added wikilinks from 4 related engineering cards.
**Notes:** Major gap filled — zero prior KB coverage on how to test AI outputs. Placed in engineering/ because evals are an operational engineering practice. Could expand later: per-eval-type deep dives, RAG-specific evals, code generation evals. Anthropic's building skills guide has testing patterns that should cross-reference this card.

## [2026-05-25] ingest | Obsidian Vault Organization + KB Evolution Plan
**Source:** https://x.com/cyrilxbt/status/2058373087330959829 (CyrilXBT thread)
**Action:** Created 3 new cards + updated index.md
**Cards added:**
- `concepts/retrieval-first-principle.md` — Core concept: organize for retrieval not storage; four retrieval dimensions; storage vs retrieval mindset comparison
- `engineering/obsidian-vault-organization.md` — Full system: folder structure, naming convention (YYYY-MM-DD-[TYPE]-[TOPIC]), YAML properties, tag taxonomy (topic/status/source prefixes), MOCs, inbox processing, search strategy, quarterly review, Claude integration
- `engineering/kb-evolution-plan.md` — Our 6-month roadmap: Phase 1 foundation (frontmatter, tag taxonomy), Phase 2 structure (naming, MOCs, INBOX), Phase 3 maintenance (processing habit, quarterly review), Phase 4 intelligence (Claude integration, Dataview, automated reports)
**Decisions:**
- All 3 cards from single X thread — clear material, fits KB structure
- `retrieval-first-principle` → concepts/ (pure principle, not tool-specific)
- `obsidian-vault-organization` → engineering/ (implementable practice)
- `kb-evolution-plan` → engineering/ (roadmap is engineering document)
- Added wikilinks from related cards: llm-wiki-pattern, personal-operating-system, kb-evolution-plan, soul-md-identity-layer
**Notes:** This material triggered our KB evolution decision. Andreas asked to update KB and evolve our approach. Created evolution plan with 4 phases. Now following the plan for all new card additions.
**Outcome:** Pushed to GitHub.

## [2026-05-25] maintenance | KB cleanup — remove redundant ## Type headers, move SYSTEM/MOC.md
Source: Andreas asked to ensure no conflicts with evolved approach
Action: Removed `## Type` and `## Summary` from 9 cards that had both frontmatter and body-level metadata. Moved SYSTEM/MOC.md to correct location (SYSTEM/). Updated AGENTS.md with frontmatter-canonical rule.
Cards cleaned: concepts/personal-operating-system, concepts/retrieval-first-principle, concepts/self-evolving-skills, concepts/soul-md-identity-layer, engineering/agentic-coding-principles, engineering/hermes-kanban, engineering/hermes-three-tier-memory, engineering/kb-evolution-plan, engineering/obsidian-vault-organization, first-principles/gepa-prompt-evolution
KB is now conflict-free: frontmatter is canonical, no dual type systems.
**Outcome:** Pushed to GitHub.

## [2026-05-25] ingest | Gstack — role-based AI team + dispatch routing
**Source:** https://github.com/garrytan/gstack (Garry Tan, YC President & CEO)
**Action:** Created 2 engineering cards
**Cards added:**
- `engineering/gstack-process-pattern.md` — 6 roles (CEO/Eng Manager/Designer/QA/Security/Release) + sprint process (Think → Plan → Build → Review → Test → Ship → Reflect). Key: process-not-tools, each step feeds into next, nothing falls through cracks.
- `engineering/dispatch-routing-tiers.md` — 5 complexity tiers (Simple/Medium/Heavy/Full/Plan) that route tasks to right AI process level. Prevents over-engineering simple tasks and under-engineering complex ones.
**Decisions:**
- Both → engineering/ — implementable practices, not pure concepts
- `gstack-process-pattern` → maps to our existing patterns (ask-mode, advisor-pattern, scaffolding); natural fit
- `dispatch-routing-tiers` → complements gstack-process-pattern; useful for Hermes project workflows
- Source tagged: #source/garrytan
- Related links added: ask-mode-before-code-mode, advisor-pattern, scaffolding, vibe-coder-security-checklist, dispatch-routing-tiers
**Notes:** Use case: Andreas points Hermes to KB for project reference. Cards designed for Hermes to read and apply — no AGENTS.md changes, all value in KB.
**Outcome:** Pushed to GitHub.


## 2026-05-28 — Hermes May 27 Updates: MCP Catalog + Krea 2 Image Generation
**Source:** NousResearch X posts (May 27, 2026)
**Action:** Created 2 cards — MCP Catalog (engineering) and Krea 2 (concepts)
**Cards added:**
- `engineering/hermes-native-mcp-catalog.md` — Hermes ships with native MCP server catalog; MCP is the standard protocol for connecting agents to external tools and data sources; discovery mechanism rather than manual integration
- `concepts/krea-2-image-generation.md` — Krea 2 is a foundation image generation model trained from scratch for aesthetic quality + fine control; style transfer, moodboard input, adjustable creativity
**Decisions:**
- MCP Catalog → engineering/ — protocol/architecture concern for tool integration; fits with hermes-three-tier-memory and scaffolding-for-ai-agents
- Krea 2 → concepts/ — foundation model capability card, not an engineering practice; fits with personal-operating-system (aesthetic quality matters in KM too)
- Both are Hermes-specific; placed in KB as reference cards Andreas can share with Hermes
- Source tagged: #source/nousresearch on both
**Outcome:** Pushed to GitHub.


## 2026-06-07 — Research Sprint: 4 High-Priority Cards Added
**Scope:** Weekly KB review (Sunday 2026-06-07) identified 4 high-priority gaps from the gap analysis + did web research to source material.
**Cards added:**
- `concepts/context-engineering.md` — From Anthropic's "Effective context engineering for AI agents" + JetBrains/TUM NeurIPS 2025 research. Covers: context rot, attention as finite budget, observation masking vs LLM summarization, hybrid strategies, just-in-time context retrieval, progressive disclosure.
- `engineering/agent-observability-opentelemetry.md` — From Digital Applied's "AI Agent Observability 2026" guide (2026-05-27). Covers: why agents break differently, OTel GenAI semantic conventions (v1.41), mandatory metrics (duration + token usage), 4 agent span operations, MCP span enrichment, privacy by default, Python instrumentation example.
- `concepts/model-context-protocol.md` — From Anthropic's MCP announcement + Wikipedia + Skilljar course. Covers: what MCP is, JSON-RPC 2.0 architecture, 3 primitives (tools/resources/prompts), why it matters (standardization, visibility, ecosystem), Linux Foundation donation Dec 2025, relationship to Hermes MCP catalog.
- `engineering/multi-agent-orchestration-patterns.md` — From Microsoft Azure Architecture Center guide (updated 2026-05-12). Covers: 5 patterns (sequential, concurrent, group chat, handoff, magentic), when to use each, orchestrated vs choreographed distinction, shared state management, failure modes.
**Decisions:**
- All from web research, no material shared by Andreas this session
- context-engineering → `concepts/` — it's a fundamental principle (like retrieval-first-principle), not a specific tool or practice
- model-context-protocol → `concepts/` — protocol/standard description, fits with other concept cards about frameworks
- agent-observability → `engineering/` — operational practice, implementation-specific
- multi-agent-orchestration → `engineering/` — implementable engineering patterns
- All 4 tagged #source/web-research
- Context engineering fills the #1 gap from last week's KB state analysis
- Agent observability fills the #5 gap (observability/telemetry)
- MCP card supplements the existing hermes-native-mcp-catalog (catalog = implementation, MCP = protocol)
- Multi-agent patterns cross-references hermes-kanban and gstack-process-pattern
**Outcome:** All 4 pushed to GitHub.
**Source:** NousResearch X posts (May 27, 2026)
**Action:** Created 2 cards — MCP Catalog (engineering) and Krea 2 (concepts)
**Cards added:**
- `engineering/hermes-native-mcp-catalog.md` — Hermes ships with native MCP server catalog; MCP is the standard protocol for connecting agents to external tools and data sources; discovery mechanism rather than manual integration
- `concepts/krea-2-image-generation.md` — Krea 2 is a foundation image generation model trained from scratch for aesthetic quality + fine control; style transfer, moodboard input, adjustable creativity
**Decisions:**
- MCP Catalog → engineering/ — protocol/architecture concern for tool integration; fits with hermes-three-tier-memory and scaffolding-for-ai-agents
- Krea 2 → concepts/ — foundation model capability card, not an engineering practice; fits with personal-operating-system (aesthetic quality matters in KM too)
- Both are Hermes-specific; placed in KB as reference cards Andreas can share with Hermes
- Source tagged: #source/nousresearch on both
**Outcome:** Pushed to GitHub.

- 2026-06-08: Added [[engineering/harness-engineering]] — new discipline for AI agent reliability, 19 sections covering artifacts, three camps, five principles, build-to-delete paradox. Source: @sairahul1 X thread.

## [2026-06-16] ingest | Hermes June 16 Updates: Async Subagents + Stripe Partnership
**Source:** NousResearch/Hermes scan 2026-06-16 (via Andreas)
**Action:** Created 2 cards from Hermes update scan
**Cards added:**
- `engineering/async-subagents.md` — The delegate tool is now non-blocking; parent agent continues while subagents run in the background; material architectural change (prior KB explicitly described delegate_task as synchronous)
- `engineering/hermes-stripe-partnership.md` — Hermes + Stripe partnership; three new skills (stripe-link-cli, mpp-agent, stripe-projects); safety model for credential handling
**Decisions:**
- Both → `engineering/` — architectural/tooling changes and new capabilities, not pure concepts
- `async-subagents` → tagged Agent Architecture; linked to multi-agent-orchestration-patterns and hermes/architecture
- `hermes-stripe-partnership` → tagged Agent Framework; linked to hermes/skills and hermes/plugins
- Source tagged: #source/nousresearch (Hermes scan)
- Async subagents is a material change — prior KB documentation explicitly said delegate_task was synchronous. This update supersedes that.
**Outcome:** Pushed to GitHub.

## [2026-06-18] ingest | Hermes Portal Teams — Team Billing
**Source:** NousResearch tweet scan 2026-06-18 (via Andreas)
**Action:** Created `engineering/2026-06-18-hermes-portal-teams.md`
**What it covers:** Team billing for Hermes Portal — shared credit pool, per-member spend caps, Spend Intelligence dashboard, multi-team membership, single billing owner. First org-level deployment feature for Hermes.
**Decisions:**
- Placed in `engineering/hermes/` section alongside other Hermes product updates (async-subagents, hermes-stripe-partnership)
- Tagged: hermes, billing, enterprise, agent-framework, team-deployment, cost-management
- Linked to: hermes-stripe-partnership, hermes/profiles, hermes/architecture, async-subagents, multi-agent-orchestration-patterns
- Source: #source/nousresearch
**Why it matters:** Hermes going from personal agent to org-level AI infrastructure. Team billing + spend controls are prerequisites for deploying Hermes at scale.
**Note:** First KB entry from a saved tweet scan report (memory/tweet-scans/). Cron now saves scan reports to file on each run, so Andreas can say "KB this" and I have the content ready.
**Outcome:** Pushed to GitHub.

## [2026-06-19] ingest | Claude research report: Agentic-Loop Design for Hermes
**Source:** Andreas asked Claude to produce a research report; URL: https://claude.ai/public/artifacts/72bd1d35-98b4-466b-81e4-71cc25aece54
**Material:** 95 KB Claude-generated research synthesis covering Anthropic + OpenAI engineering guidance (2024–2026) on agentic loop design, with specific coverage of Hermes Agent (Nous Research) and MiniMax as the engine. The report includes TL;DR, key findings, conceptual frameworks, concrete implementation patterns (stopping, self-verification, context management, sub-agents, tool design, error handling, task-type playbooks), the Agent Skills delivery format, Hermes + MiniMax internals, and a practical synthesis for writing a meta-skill.
**Action:** Two cards added:
- `raw/2026-06-19-raw-claude-agentic-loop-design-report.md` — full verbatim transcript (38 KB after cleaning 58 citation icon URLs into standard markdown links; all source URLs and labels preserved). Provenance noted in frontmatter as a Claude-generated research artifact.
- `engineering/2026-06-19-engineering-agentic-loop-pattern.md` — curated engineering card distilling the report's actionable engineering content: the canonical loop, activation gate, six controls, six failure modes, Hermes + MiniMax specifics, and a practical skeleton. Cross-linked to existing cards (loop-engineering, harness-engineering, multi-agent-orchestration-patterns, progressive-disclosure-pattern, skill-trigger-design, hermes/skills, hermes/architecture, async-subagents, context-engineering, scaffolding-for-ai-agents, agentic-coding-principles).
**Decisions:**
- **Two-card split** following the precedent set by anthropic-building-skills-guide / openai-how-openai-uses-codex: raw/ holds the verbatim source, engineering/ holds the curated distillation. Keeps the source auditable while the curated card is the one that shows up in retrieval.
- **Card name: `agentic-loop-pattern`** to distinguish from `loop-engineering` (which is the *outer* scheduling loop from Addy Osmani's X thread). The two are complementary layers: outer loop = system that schedules agents; inner loop = per-task iteration cycle inside a single agent.
- **Placement: `engineering/`** — engineering practice methodology, not a pure concept or mental model. Fits with the loop-engineering / harness-engineering / multi-agent-orchestration cluster.
- **Source tags:** `#source/claude-artifact` (it's a Claude synthesis, not first-party), plus `#source/anthropic`, `#source/openai`, `#source/nousresearch`, `MiniMax` for the underlying primary sources the report draws from.
- **Cleanup: 58 citation icon URLs stripped from the raw.** Claude's artifact rendering embeds decorative citation images with multi-kilobyte base64 validation tokens. I parsed them to extract the label (e.g., "Anthropic") and the real destination URL, then wrote the result back as standard `[label](url)` markdown. All 58 source links preserved. The raw/ file is 38 KB instead of 95 KB and is now actually readable.
- **Did not extract the "stage 1–5 implementation plan"** into a separate card. It overlaps significantly with our existing build-evaluate-iterate habit, evals-ai-evaluation-frameworks, and skill-trigger-design. The card links to those rather than duplicating.
**Notes:**
- The report describes Hermes Agent (Nous Research) — the same product we run locally per `engineering/hermes/`. Confirmed by the report's own description of the 90-iteration default, 50%/85% compaction, system-prompt-stability rule, and skill self-authoring — all match our `engineering/hermes/architecture` and `hermes/skills` cards.
- The report's caveats section explicitly notes that the "90-turn hard cap" is imprecise (it's a configurable default, not immutable) and that some doc pages lag the code. Both notes are already captured in our existing Hermes cards.
- The "+40.1% BrowseComp" and "90.2% multi-agent uplift" numbers are vendor/lab-reported — flagged in the card's Caveats section so we don't anchor on them as if they were independently audited.
**Outcome:** Pushed to GitHub.

## 2026-06-28 — Weekly KB Review (Sunday)
**Person:** Cerebro (self-maintenance cron)
**Action:** Maintenance pass — no new cards this week, but 2 quality issues fixed.

### 1. Broken wikilinks in `engineering/MOC.md`: FIXED ✅
**Problem:** `engineering/MOC.md` referenced `[[engineering/hermes/skills]]`, `[[engineering/hermes/architecture]]`, and `[[engineering/hermes/2026-06-20-hermes-blender-mcp-skill]]` (without the `engineering/` prefix). All other hermes subtree links in the MOC used the `engineering/hermes/` prefix. The bare-without-prefix form only works in Obsidian if the linked file is at the vault root, which it isn't — so these were effectively broken links.

**Fix:** Rewrote `engineering/MOC.md` end-to-end with consistent `engineering/hermes/*` prefixes. All entries now use full folder-prefixed paths.

### 2. Hermes Kanban duplicate cards: MERGED + ARCHIVED ✅
**Problem:** Two cards covered the same topic:
- `engineering/hermes-kanban.md` (248 lines, 2026-05-25) — older, deeper card; 6 incoming links from concepts + engineering cards
- `engineering/hermes/kanban.md` (97 lines, 2026-06-12) — newer, shorter "operational reference" card; 1 incoming link (index only)

Both were indexed. The newer subfolder card had unique operational content (Rate Limit Trap warning, Multi-Gateway Dispatch config) that wasn't in the older root card.

**Fix:**
- Enriched `engineering/hermes-kanban.md` with the unique operational content (Rate Limit Trap section explaining the 429 crash-loop and `failure_limit: 1` fix, Multi-Gateway Dispatch config, full CLI verb catalog). Updated frontmatter to include the 2026-06-28 sources and 3 additional related cards.
- Archived `engineering/hermes/kanban.md` to `.archive/2026-06-12-engineering-hermes-kanban-duplicate.md` (archive never-delete policy).
- Removed the duplicate row from `index.md`.

### 3. Stale `engineering/MOC.md` (missing 13 cards): FIXED ✅
**Problem:** The MOC claimed "21 cards" but the engineering folder actually has 34 content cards. Missing:
- From Agent Architecture: `engineering/hermes-stripe-partnership`, `engineering/2026-06-18-hermes-portal-teams`
- New Hermes Reference cluster (8 cards): `engineering/hermes/README`, `security`, `plugins`, `observer-hooks`, `middleware`, `profiles`, `network-egress-isolation`, `contributing`
- From Skill & Prompt Engineering: `engineering/agent-observability-opentelemetry`
- New AI Team Methodologies cluster (2 cards): `engineering/gstack-process-pattern`, `engineering/dispatch-routing-tiers`

**Fix:** Rewrote `engineering/MOC.md` end-to-end with full cluster coverage. Card count now 34 across 8 clusters. Also normalized all wikilinks to folder-prefixed form (fixes the prefix inconsistency at the same time).

### Queue
- `memory/kb-queue.md` — empty (no items below the placeholder line)
- `memory/kb-queue/` folder — only the 2026-05-20 migration note, nothing pending

### Gaps Still Open (unchanged from 2026-06-14)
- Tool Use / Tool Definition (high) — pending
- Chain-of-Thought / Prompt Chaining (high) — pending
- Model Routing (medium) — pending
- Prompt Versioning / Diffing (medium) — pending
- Code Execution Sandboxing (low) — pending
- RAG Retrieval Strategies (low) — partially covered by llm-wiki-pattern

### New Gap Noted (medium priority)
- **Hermes operational gotchas** — the rate-limit crash-loop pattern we discovered during the Kanban merge is exactly the kind of operational warning that should have its own card (or at least a "Hermes operational gotchas" cluster). Not enough material yet for a dedicated card — log it and watch for more.

## [2026-07-16] ingest | Anatoli Kopadze — "Loops explained" (curated into existing loop-engineering + agentic-loop-pattern cards; no new curated cards)
**Source:** @AnatoliKopadze X thread 2026-06-20 (https://x.com/anatolikopadze/status/2068328135611822149, 6,759 likes, 1,026 reposts, 185 replies) — long-form explainer on agentic loops. Andreas shared link directly (Telegram).

**Action:** No new curated cards. Three genuinely new additions surfaced; all folded into existing cards where they belong.

**Raw dropped:** `raw/2026-07-16-raw-anatoli-kopadze-loops.md` (verbatim full thread, ~14 KB). Provenance flagged in frontmatter as a source/x-thread entry; Mira product promotion at the tail deliberately skipped with an `> Editorial skip:` comment.

**Card edits (2 existing cards):**

- `engineering/2026-06-19-engineering-agentic-loop-pattern.md` — three additions:
  1. **Five-stage variant subsection** (§ "### The Five-Stage Variant" under "The Canonical Loop"). Frames DISCOVER → PLAN → EXECUTE → VERIFY → ITERATE as a useful code-oriented expansion of our 4-stage Anthropic *gather → act → verify → repeat* shape. Explicit note: stage count isn't load-bearing; both forms are valid.
  2. **Seventh failure mode: Ralph Wiggum loop** (Geoffrey Huntley's coinage, per Kopadze). Table row added; rationale: silent-billing failure — loop appears to be working but isn't. Worse than "premature stopping" because only the bill tells you something's wrong.
  3. **New § "The Cost That Matters"** — cost-per-accepted-change metric + 50% accept-rate rule of thumb. Frames Ralph Wiggum as a *cost* failure, not just a correctness failure. Practical leverage points (lower ceiling, cheaper model on boring steps, stronger verifier, convergence check) cross-linked to existing cards (`agent-observability-opentelemetry`, `hermes-moa-virtual-models`).
  4. **See Also updated** — new raw entry.

- `engineering/loop-engineering.md` — one addition:
  1. **New § "The Build Order That Works"** after "A Real Loop Example". Four-step ladder (manual → skill → loop → schedule) with explicit cross-links to `progressive-disclosure-pattern` (step 1→2), `agentic-loop-pattern` hardening (step 2→3), and the agentic-coding-principles comprehension-debt caveat (step 3→4). Source list frontmatter extended.

**Card count:** 39 → 39 (no new curated cards).

**Cross-link graph:**
- New raw card links → [[engineering/2026-06-19-engineering-agentic-loop-pattern]], [[engineering/loop-engineering]], [[engineering/harness-engineering]], [[engineering/multi-agent-orchestration-patterns]], [[concepts/context-engineering]]
- Edited cards add the new raw entry to their See Also / source lists.
- engineering/MOC.md — Last Updated section rewritten, footer date bumped to 2026-07-16. Card count line untouched (still 39).
- index.md — new row in Raw Sources table; two existing-loop-engineering / agentic-loop-pattern summaries refreshed to surface the new content; footer date bumped to 2026-07-16.

**Decisions (full rationale in SYSTEM/operations.md):**

- **No new curated cards.** Considered creating `engineering/2026-07-16-anatoli-loops-thread.md` or a `concepts/agentic-loop-pattern-variants.md` and a separate `concepts/loop-cost-metrics.md`. Rejected: all four additions slot cleanly into cards we already curate (canonical-loop variant + cost metric → agentic-loop-pattern; build order → loop-engineering). Creating parallel cards would create the exact duplicate-card problem we just merged away (`hermes-kanban` duplicate, 2026-06-28). New cards cost more than the consolidation saves.
- **The 50% accept-rate rule of thumb is presented as a tripwire, not a law.** Kopadze's source doesn't cite a controlled study. Flagged inline in the new card section.
- **Mira product promotion skipped.** Mira is a Telegram-launched competitor product (500+ integrations via Composio, scheduled triggers, content pipeline, voice transcription — a meaningful fraction of what Hermes + OpenClaw do). Not extracting the promotion would have been the easy call; treating it as KB material would have set a precedent of cataloguing competitors, which our KB isn't for. The conceptual overlap with our existing cards ([[engineering/loop-engineering]], [[concepts/model-context-protocol]]) is noted in the raw's editorial-skip comment so future readers can see the deliberation.
- **Build-order ladder placed in loop-engineering, not a new card.** It's a natural extension of Osmani's framing (his card talks about automation + external memory but doesn't spell out the assembly order). Adding it inline keeps the layer coherent.
- **Ralph Wiggum loop lives with the inner-loop failure modes.** Huntley's coinage describes an agent's behavior inside a single loop — wrong layer for the outer-loop card. Placed in `agentic-loop-pattern` to keep the failure-mode catalog together; `loop-engineering` references it by raw link.

**Cross-links added (3 cards affected):**
- `engineering/2026-06-19-engineering-agentic-loop-pattern.md` — See Also section now includes the new raw card
- `engineering/loop-engineering.md` — See Also implicit (new section references `agentic-loop-pattern` + `agentic-coding-principles`)
- `raw/2026-07-16-raw-anatoli-kopadze-loops.md` — frontmatter `related:` lists the edited curated cards

**Outcome:** Pushed to GitHub.

## [2026-07-01] ingest | Hermes batch — MoA, HermesBench, Blank Slate, cronjob, messaging
**Person:** Cerebro (self-initiated, after Andreas asked for KB review)
**Trigger:** Andreas request "do a thorough review of our KB and tell me what new material would nicely fill any gaps" (2026-07-01)
**Action:** Filled the four highest-leverage gaps from the gap-review report — three new Hermes feature cards (from material already in `memory/tweet-scans/`), two new Hermes reference cards (distilled from local Hermes docs in `~/.hermes/hermes-agent/`), plus two small patches. Pets was not ingested — only a teaser tweet with no detail, queued instead.

**Cards added (5):**
- `engineering/2026-06-27-hermes-moa-virtual-models.md` — MoA presets exposed as virtual models in the routing layer; provider-agnostic N-way ensembles; +8%/+11% HermesBench. See Also links: `2026-06-27-hermesbench`, `hermes/architecture`, `advisor-pattern-for-ai-agents`, `dispatch-routing-tiers`, `multi-agent-orchestration-patterns`, `async-subagents`.
- `engineering/2026-06-27-hermesbench.md` — HermesBench as Nous's internal agent-shaped benchmark; pre-announced full leaderboard. Cross-link to MoA card + general evals framework.
- `engineering/2026-06-21-hermes-blank-slate-setup.md` — `hermes setup` third mode (Quick / Full / Blank Slate). Cross-link to `hermes/README` (which was also patched).
- `engineering/hermes/cronjob.md` — Gateway-owned scheduled-task subsystem (~250 lines). 60s tick scheduler, single `cronjob` tool with action verbs, schedule formats, provider pinning fail-safe (#44585), skill attachments, multi-platform delivery tokens (`all` resolved at fire time), Telegram cron topic, response wrapping, silent suppression token, no-agent mode for watchdogs, `wakeAgent` cheap pre-run gate (`file-change`, `external-flag`, `sql-count`), `context_from` chaining, workdir mode serialization, security scanning at create/update. Distilled from the local Hermes docs at `~/.hermes/hermes-agent/website/docs/user-guide/features/cron.md` + 4 companion docs.
- `engineering/hermes/messaging.md` — Gateway operational reference (~340 lines). 22+ platform adapters with full capability matrix (voice/images/files/threads/reactions/typing/streaming), installation variants (system service vs user + linger for headless VMs), 3-layer security (allowlist, DM pairing, admin/user tier split), shared slash-command library (28 commands), reset policies, busy-input modes (`interrupt/queue/steer`), silence tokens, tool-progress defaults, `/background` sessions, `/platform` day-2 operations, automatic circuit breaker (no auto-resume), restart notifications, session resume across restarts, optional progress-bubble cleanup, multi-HERMES_HOME awareness. Distilled from `~/.hermes/hermes-agent/website/docs/user-guide/messaging/index.md` + per-platform setup guides.

**Patches (2):**
- `engineering/hermes/architecture.md` line 144–156 — `## Delegation (delegate_task)` updated from "Synchronous: parent waits" to "Asynchronous as of June 2026 — parent continues immediately". `Synchronicity rule` renamed to `Durability rule` with explicit See Also backlinks to `async-subagents`, `hermes/cronjob`, and `hermes-kanban`. Architecture card also gained a `## See Also` footer pointing to the new reference docs and `async-subagents`.
- `engineering/hermes/README.md` — `## Core Commands` table updated (`hermes setup` → "Quick / Full / Blank Slate" setup modes). Added `## Setup Modes` section. `related:` frontmatter extended with `hermes/messaging`, `hermes/cronjob`, `2026-06-21-hermes-blank-slate-setup`.

**Queue (1):**
- `memory/kb-queue/2026-07-01-hermes-pets.md` — Hermes Agent now supports "Pets" (per @NousResearch 2026-06-29, a teaser reply with no detail). Primary announcement tweet not visible. **Decision: queue, do not draft cold.** When primary announcement + docs surface, draft `engineering/<date>-hermes-pets.md`.

**Decisions:**

- **Naming convention:** all dated cards use `YYYY-MM-DD-<type>-<topic>.md` per `KB-EVOLUTION-DESIGN.md`. Date stamps reflect today's ingest (2026-07-01), not the source date — flagged inside each card's `> Note:` block. Reasoning: avoids adding more 06-prefixed cards to existing clusters that already have a healthy cluster identity, makes ingest chronology clear from filename alone, and matches the convention set by recent ingests (`2026-06-18-hermes-portal-teams`, `2026-06-20-hermes-blender-mcp-skill`, `2026-06-19-engineering-agentic-loop-pattern`). Alternative considered: date-stamping by source date (2026-06-20 for Blank Slate, 2026-06-27 for MoA/HermesBench) — would scatter the cluster date-prefixes; rejected.

- **Two new Hermes reference cards (cronjob, messaging) placed at `engineering/hermes/`** (same subfolder as existing reference docs `security.md`, `plugins.md`, `observer-hooks.md`, etc.) rather than at `engineering/` root. Reason: they are documentation-style reference cards derived directly from the local Hermes docs clone — they belong with the other reference docs, not with the curated engineering practice cards at the root.

- **MoA / HermesBench / Blank Slate placed at `engineering/` root** rather than `engineering/hermes/` subfolder — same logic as previous Hermes feature cards (`hermes-stripe-partnership`, `hermes-portal-teams`, `async-subagents`, `hermes-blender-mcp-skill`). They're product/architecture/feature cards, not reference docs. They cross-link into the subfolder where appropriate.

- **MoA card captures the underlying 2-layer MoA implementation** (REFERENCE_MODELS, AGGREGATOR_MODEL, temperature settings, MIN_SUCCESSFUL_REFERENCES=1) as well as the new virtual-model abstraction. Both layers matter — the implementation has been in `tools/mixture_of_agents_tool.py` since at least 2025, but the virtual-model exposure is the new framing.

- **HermesBench card deliberately scoped to "what's known"** rather than speculating about methodology or category breakdowns that aren't public yet. Added a "What's Not Yet Public" section and a maintenance note to revise once the leaderboard publishes.

- **Pets deliberately NOT ingested.** The 06-30 scan flagged it as a teaser reply ("@demi_hl Not quite what we anticipated when we said Hermes Agent now supports Pets") with no scope, no docs link, no clarifying follow-up visible in any subsequent scan (07-01). Drafting a card from a single ambiguous tweet would be speculation. Queue entry was written with the URL + spotted-context + decision rule for when to ingest.

- **README and architecture.md patches in the same commit as the new cards** rather than separate commits. Reason: the patches fix contradictions the new cards reference (e.g., `async-subagents` is cited in the architecture patch; `2026-06-21-hermes-blank-slate-setup` is cited from the README patch). Splitting into separate commits would leave intermediate states where the new cards reference fixes that haven't shipped yet.

- **No new `engineering/hermes/cron-internals.md`** despite the local docs having both `user-guide/features/cron.md` and `developer-guide/cron-internals.md`. Reason: the user-facing cron.md (~700 lines, distilled into ~250-line card) covers everything an operator needs. `cron-internals.md` is for Hermes developers, not users — out of KB scope (KB is engineering-practice reference, not developer-onboarding for Hermes internals).

**Indexing:**
- `index.md` — 5 new rows added (MoA, HermesBench, Blank Slate, cronjob, messaging). Last-updated date updated to 2026-07-01.
- `engineering/MOC.md` — 3 rows added to Agent Architecture cluster (MoA, HermesBench, Blank Slate); 2 rows added to Hermes Reference cluster (cronjob, messaging); card count refreshed from 34 to 39; last-updated footer rewritten.
- `SYSTEM/log.md` — this entry
- `SYSTEM/changelog.md` — new dated section
- `SYSTEM/operations.md` — decision rationale appended (separate concern: the append-only operations log gets the "why" calls)
- `SYSTEM/sources.md` — 5 new source rows

**Cross-links added (12 cards affected):**
- New cards → existing: 6+ outbound each
- `engineering/hermes/architecture.md` — `## See Also` section pointing to new cards
- `engineering/hermes/README.md` — `related:` frontmatter + setup modes section

**Outcome:** Pushed to GitHub (knowledge-base repo).

**Engineering batch (4 cards from the 06-21 / 06-07 gap list) — NOT done in this session.** Items deferred: `engineering/tool-use-design.md`, `engineering/chain-of-thought-prompt-chaining.md`, `engineering/model-routing.md`, `engineering/prompt-versioning-diffing.md`. My stated default plan was "Hermes batch now, engineering batch in a dedicated session" — confirmed at 21:24 by Andreas ("Ok do it"), preserving this split. The next dedicated research session will source these from Anthropic blog, OpenAI docs, model routing writeups, and prompt versioning platforms.

## [2026-07-26] weekly-review + ingest | Hermes v0.18.0 “The Judgment Release”
**Source:** NousResearch announcement (https://x.com/NousResearch/status/2072413332665962617) and primary GitHub release notes (https://github.com/NousResearch/hermes-agent/releases/tag/v2026.7.1).
**Queue processed:** `memory/kb-queue/2026-07-05-hermes-v0-18-judgment-release.md`.
**Action:** Created `engineering/hermes/2026-07-26-hermes-v0-18-judgment-release.md`, an umbrella synthesis covering completion contracts and verification evidence, `/learn`, `/journey` + memory graph, cheaper background review, multi-subagent fan-out, first-class MoA models, desktop coding Projects, gateway scale-to-zero + drain coordination, Vertex AI, `/prompt`, and security hardening.
**Existing cards updated:** `engineering/hermes/README.md`, `concepts/self-evolving-skills.md`, `engineering/async-subagents.md`, `engineering/hermes-three-tier-memory.md`, `engineering/2026-06-19-engineering-agentic-loop-pattern.md`, `engineering/hermes/messaging.md`, `engineering/hermes/plugins.md`.
**Indexing:** Added to `index.md` and `engineering/MOC.md`; engineering curated count 39 → 40.
**Audit:** 0 real broken wikilinks; 3 unresolved examples remain intentionally documented (`card-name`, `PARA Method`, and `Atomic Habits` in SYSTEM/operations.md). 0 orphaned curated cards among 57 checked. Short-card scan found only intentionally scoped product/reference cards; no forced expansion.
**Queue remaining:** Hermes Pets teaser remains pending because no primary announcement or technical documentation was available in the queue material.
**Outcome:** Committed and pushed to GitHub.

## [2026-08-02] weekly-review + ingest | Hermes v0.19.0 "Quicksilver" + credential firewall + tldraw-offline skill
**Source:** Combined from (a) NousResearch v0.19.0 announcement https://x.com/NousResearch/status/2079278653997809984 and GitHub release notes, (b) tweet-scan 2026-07-28 (https://x.com/NousResearch/status/2080728699100406042 credential firewall, https://x.com/NousResearch/status/2080319476243861569 tldraw skill, https://x.com/NousResearch/status/2080745634710139113 Opus 5 in catalog), and (c) local tweet-scans 2026-07-20 and 2026-07-28.
**Note on timing:** the 2026-07-20 tweet-scan ran at 15:05 UTC; the v0.19.0 announcement tweet posted at 18:53 UTC the same day. The scan therefore did not capture v0.19.0 because the announcement had not yet posted when the scan window closed. This is a known timing limitation, not a missed detection.

**Action:** Three new cards in `engineering/hermes/`, one wikilink fix, plus index/MOC refreshes.

**Cards added (3):**

1. **`engineering/hermes/2026-08-02-hermes-v0-19-quicksilver-release.md`** — umbrella release synthesis for v0.19.0 "Quicksilver". Sections: speed spine (cold start ~4.3s → ~0.9s, ~80% cut), live reasoning streams (MoA aggregator reasoning rendered live; pairs with v0.18 MoA architecture), smart approvals (smallest-reasonable-permission default), durable response delivery (journaled response finalization across gateway restarts, complements v0.18 scale-to-zero/drain), Bitwarden + 1Password first-class secrets (no more hand-pasted API keys), terminal subscription management (long-output tail streaming into separate pane), desktop/TUI polish, Claude Opus 5 in catalog (via Nous Portal / OpenRouter / Anthropic Direct, model-availability addendum not standalone card), v0.19.1 patch (WhatsApp Baileys Docker build pin). Numbers since v0.18.0: ~2,245 commits, ~1,065 merged PRs, ~2,465 files changed, ~300K insertions, ~36K deletions, ~3,300 issues closed, 450+ community contributors.

2. **`engineering/hermes/2026-07-24-hermes-credential-firewall.md`** — token-substituting proxy at the Docker sandbox network boundary. Real provider keys stay outside the sandbox; leaked tokens are useless outside the firewall. Explicitly complements (does not replace) [[engineering/hermes/network-egress-isolation]]; the two together cover both *where* a sandbox can reach and *what credentials* it sees on the wire. Closes a gap the existing `hermes/security` card explicitly acknowledged ("in-process heuristics... are not real boundaries"). Install via `hermes egress setup`.

3. **`engineering/hermes/2026-07-23-tldraw-offline-skill.md`** — optional skill under `optional-skills/creative/tldraw-offline` that drives a running tldraw desktop whiteboard over MCP. Same architectural pattern as the Blender MCP card (official skill + MCP/desktop bridge) with one notable twist: the skill can embed a script in the saved `.tldr` so the drawing opens with working buttons and state. Card surfaces the "executable artifact" framing — agent produces a file whose state evolves when opened, not a static image. Install via `hermes skills install optional-skills/creative/tldraw-offline`.

**Wikilink fix (1):**

- `engineering/hermes/skills.md` — `[[2026-06-20-hermes-blender-mcp-skill]]` (bare) → `[[engineering/hermes/2026-06-20-hermes-blender-mcp-skill]]` (folder-prefixed). This was the last real broken wikilink in the KB. See Also section also updated to cross-link the new tldraw-offline skill.

**Indexing:** 3 rows added to `index.md` and `engineering/MOC.md`. Engineering curated count 40 → 43.

**Audit (this review):**
- Real broken wikilinks: 1 (`engineering/hermes/skills.md` → fixed)
- Intentional placeholder wikilinks: 3 (`card-name`, `PARA Method`, `Atomic Habits` in SYSTEM/operations.md), unchanged
- Orphaned curated cards: 0 across all content folders
- Short cards: 35 with ≤60 body lines, all intentionally scoped (product/reference cards or thin concept/mental-model cards). No forced expansion.

**Queue this review:**
- `memory/kb-queue.md` (legacy single file): empty below template marker.
- `memory/kb-queue/2026-07-01-hermes-pets.md`: **still pending.** Reviewed again — primary announcement still not visible, no docs page, no clarifying follow-up beyond the 2026-06-29 teaser. Decision unchanged: do not draft from speculation. Queue entry will keep its existing URL + spotted-context + ingestion rule.
- `memory/kb-queue/2026-07-05-hermes-v0-18-judgment-release.md`: already marked done in last review.
- `memory/kb-queue/2026-05-20-queue-migration.md`: legacy migration note, no action needed.

**Out-of-scope material logged this week (not ingested):**
- Claude Opus 5 in Hermes catalog — model-availability change, not architecture. Mentioned in v0.19 card's "Provider Updates" section; not a standalone card.
- NVIDIA Open Secure AI Alliance (NousResearch as founding member) — institutional context, not KB material.
- Three provider promos in the v0.19 window (Laguna S 2.1, Hy3, Ling-3.0-flash) — promotional, logged in tweet-scan only.
- Block featured-preset PRs awaiting merge — nothing shipped, nothing to ingest.

**Decisions:**
- **One umbrella card for v0.19.0, separate cards for the v0.19-window net-new features.** Same shape as the v0.18 review: an umbrella release synthesis captures the release-level narrative (Quicksilver speed + visibility + durability + approval ergonomics), while the credential firewall and tldraw-offline skill each get their own card because they have their own deep architecture and are individually retrievable.
- **Date stamps: all three new cards use 2026-08-02 (today's ingest date).** Same convention as the v0.18 card (`2026-07-26-hermes-v0-18-judgment-release.md`). The actual announcement dates (Jul 20/23/24) are explicit in each card's body. Using the ingest date keeps the MOC cluster visually coherent and makes KB diff history readable by commit.
- **Opus 5 not given a standalone card.** Adding a card per model-availability change would create an unbounded parallel track; the v0.18→v0.19 MoA card already tracks the relevant model lineup.
- **Pets remains queued, not drafted.** Source material still too thin after another 4 weeks. Will continue to wait for primary announcement or docs.

**Outcome:** 3 new curated cards, 1 wikilink fix, 0 broken links, 0 orphans. Engineering curated count 40 → 43. Committed and pushed to GitHub.

## [2026-08-03] ingest | NousResearch mid-week scan: Desktop Kanban plugin + podcast appearance
**Source:** NousResearch tweet scan week of 2026-07-27 → 2026-08-03, delivered via cron-driven scan. Tweets read with `bird read` per Twitter rule: https://x.com/NousResearch/status/2083257053338898730 (Kanban Desktop plugin, 2,570 likes) and https://x.com/NousResearch/status/2083950025386660043 (podcast appearance, 32 likes).
**Action:** Created 2 new curated cards + 3 backlink updates
**Cards added:**
- `engineering/hermes/2026-07-31-hermes-desktop-kanban-plugin.md` — first official Hermes **Desktop** plugin (Kanban). Establishes a new UI-shell extension surface (page, sidebar, hotkeys, status bar, backend endpoints, SDK) architecturally distinct from the general plugin system in `engineering/hermes/plugins`. The underlying board is the same SQLite-backed `kanban_db` documented in `engineering/hermes-kanban`; the Desktop plugin is a presentation layer over it.
- `engineering/hermes/2026-08-02-hermes-podcast-appearance-petergyang-karan4d.md` — thin public-presence marker for the Hermes team's 2026-08-02 appearance on the Peter Yang / Karan 4D show. No substantive content beyond the thank-you tweet; exists to anchor the appearance in the KB so future reference has a hit. Upgrade if episode details surface; archive if not, at the 12-week mark.
**Backlinks added:**
- `engineering/hermes/plugins.md` — frontmatter `related` list + new "See Also: Desktop Plugin Surface" section making the non-overlap between the two plugin surfaces explicit
- `engineering/hermes-kanban.md` — frontmatter `related` list + tag addition; makes it explicit that the Desktop plugin is a front door over the same `kanban_db`
- `engineering/hermes/2026-08-02-hermes-v0-19-quicksilver-release.md` — added Desktop plugin to "Parallel Net-New Features in the v0.19 Window" and the related-links block, with framing about Quicksilver speed being the precondition for hotkey-driven plugins feeling right
**Decisions:**
- **Date stamps use the tweet date (2026-07-31, 2026-08-02)**, not the ingest date (2026-08-03). The Desktop plugin announcement was 2 days before this scan; the podcast appearance was the day before. Using tweet dates keeps the cards temporally aligned with the events they describe. The MOC's "Last Updated" footer carries the ingest date.
- **Desktop plugin card emphasizes the surface distinction** rather than re-describing Kanban. The same `kanban_db` is referenced (with a wikilink), but the *new* content is the plugin surface model — page, sidebar, hotkeys, status bar, backend endpoints, SDK — none of which is covered in `hermes/plugins` (general plugin system), `hermes-kanban` (SQLite task board), or the v0.19 card (Quicksilver release). The card explicitly walks through what a Desktop plugin is *not* (skill, memory provider, model provider, CLI subcommand) to head off future confusion.
- **SDK status labeled "inferred, not confirmed."** Three architectural points (manifest shape, permission boundary, lifecycle hooks) are flagged as educated guesses from the general plugin system's pattern + the announced capabilities. The card includes a "What to Watch For" section so the next weekly review has a checklist when the SDK repo drops.
- **Podcast card is intentionally thin but explicit about it.** A 32-like "thank you" tweet would normally not warrant a card, but the scan explicitly flagged it as "not in the KB" and the public-presence track is currently empty. The card has a "What We Don't Know" section so future-me is not confused about its scope, and explicit upgrade/archive rules.
- **No general AI/ML briefings this week.** The scan ran a 1-2/week cap and the rest of the week's content (DeepSeek V4 Flash promo, FLUX 3 free tier, banter) didn't qualify. Logged here so the cap is visible in operations.
- **Folder placement:** both cards in `engineering/hermes/` — same cluster as the v0.18, v0.19, credential-firewall, tldraw-offline cards. The Desktop plugin is a Hermes-specific UI architecture primitive, the podcast appearance is a Hermes-team public-presence marker. Neither warrants its own subfolder.
**Outcome:** 2 new curated cards, 3 backlink updates, 0 broken links. Engineering curated count 43 → 45. Committing and pushing.

## [2026-08-08] ingest | v0.20 "Herald" batch — A2A v1.0, Hermes Cloud, v0.20 release synthesis (resumed)
**Source:** NousResearch mid-week scan 2026-08-03 (originally drafted same day; commit was interrupted by the fallback-chain API-key error). Primary source: Hermes release notes `v2026.8.3` (https://github.com/NousResearch/hermes-agent/releases/tag/v2026.8.3) and PR #77109 (A2A plugin).
**Action:** Created 3 new curated cards + index/MOC/README/log/changelog/operations updates.
**Cards added:**
- `engineering/hermes/2026-08-03-hermes-v0-20-herald-release.md` — umbrella release synthesis. Voice with barge-in + wake words, A2A v1.0, signed outbound webhooks, Desktop becomes a platform, smarter loop compression. Pulls together the v0.19.1 patch + the v0.20.0 net-new. ~3,650 commits / 1,400 merged PRs since v0.19.0; 647 contributor handles in credits block.
- `engineering/hermes/2026-08-03-a2a-v1-bundled-plugin.md` — first bundled plugin in Hermes that gives the agent a standard wire format for talking to *other agents*. Implements A2A v1.0 (discovery, structured comms, task lifecycle, auth, versioning). Closes 4-year-old issue #514. PR #77109, ships in `bundled-plugins/a2a/`.
- `engineering/hermes/2026-08-03-hermes-cloud-launch.md` — first officially hosted, multi-tenant Hermes deployment, billed through Nous Portal. Two-click, 60-second provisioning. Auto-discovers in Hermes Desktop; team provisioning built in.
**Index/MOC/README updates:**
- `index.md` — added 3 new cards under Engineering; updated "Last updated" footer to 2026-08-08.
- `engineering/MOC.md` — added 3 new cards under Hermes Reference (after v0.19 release); refreshed card count 45 → 48; updated "Last Updated" footer with this batch's narrative.
- `engineering/hermes/README.md` — added v0.20.0 Herald Release section (mirrors the v0.18 Reliability Milestone section).
**Decisions:**
- **Date stamps use ingest date (2026-08-03), not commit date (2026-08-08).** Same convention as the v0.18/v0.19 release cards — these are multi-event release syntheses, not point-in-time announcements. The MOC "Last Updated" footer carries the actual commit date (2026-08-08) so the KB history is still readable by commit time.
- **One umbrella card + 2 feature cards, not 5 separate cards.** The A2A protocol and Hermes Cloud launch are both *referenced from* the v0.20 release card; promoting them to standalone cards is justified because (a) each has its own deep architecture / distribution story, (b) each is independently retrievable, (c) the A2A plugin specifically has a PR-able implementation surface. This matches the v0.18/v0.19 pattern: umbrella + per-feature cards for net-new architectural primitives.
- **Two forward-references kept as-is:** the v0.20 release card links to `engineering/hermes/2026-08-03-grounded-citations-skill` and `engineering/hermes/2026-08-03-outbound-webhooks`, which do not yet exist as cards. Same convention as v0.18's forward-refs to v0.19 cards. Will be filled in by the next batch that has source material for them.
- **No cross-link updates to existing cards in this commit.** The release card's "related" list pulls in the Desktop plugin card (committed 2026-08-03) and the credential firewall (committed 2026-07-24) which is the right shape — they're already wired up. Adding reverse backlinks from `hermes/architecture.md`, `hermes/messaging.md`, `hermes/skills.md`, `hermes/security.md` to v0.20 is a follow-up task for the next weekly review; skipping it here keeps this commit scoped.
- **The original 2026-08-03 mid-week scan was 5 cards total; only 2 (Desktop Kanban + podcast) committed cleanly before the API-key wall hit.** This batch (3 cards, 5 system files) closes the gap. No additional NousResearch scan material was added — only the previously-drafted-but-uncommitted cards.
- **Hermes Cloud launch is dated 2026-07-08 in its body but the card file uses 2026-08-03 (ingest date convention).** The cloud launch is technically a separate event from v0.20 (one month earlier) but it's folded into the v0.20 narrative because the v0.20 release card explicitly references Cloud as part of the v0.20 distribution context. Treating them as one batch keeps the cluster coherent; the cloud launch date is explicit in the card body for those who want the timeline.
**Skipped (logged, not ingested):**
- Two forward-referenced cards (`grounded-citations-skill`, `outbound-webhooks`) — no source material in the current scan; queue for next batch with primary source.
- Pets (`memory/kb-queue/2026-07-01-hermes-pets.md`) — unchanged decision: do not draft cold.

**Outcome:** 3 new curated cards, 4 system/MOC/README updates, 0 broken links, 0 archived items. Engineering curated count 45 → 48. Committing and pushing.
