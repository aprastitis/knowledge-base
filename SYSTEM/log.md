# Knowledge Base Log

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
