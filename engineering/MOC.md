# Engineering MOC

## Purpose
Map of Content for all engineering cards in `/engineering/`. This MOC organizes our engineering practice cards into topic clusters for easier navigation and discovery.

## Tags
#engineering-practice  #status/active

---

## Core Cards (by cluster)

### Agentic Coding
| Card | Summary |
|------|---------|
| [[engineering/agentic-coding-principles]] | Six principles and 28 practices for production-quality AI-assisted development. Moves beyond vibe coding. |
| [[engineering/agentic-coding-tools]] | Capability timeline for autonomous coding tools. Production numbers from Shopify, Mercado Libre, Spotify. |
| [[engineering/vibe-coder-security-checklist]] | Pre-launch security/privacy checklist for AI-built products. Env vars, OWASP, rate limits, secrets. |

### Agent Architecture
| Card | Summary |
|------|---------|
| [[engineering/hermes-three-tier-memory]] | Three distinct memory layers: frozen micro-files (Tier 1), SQLite FTS (Tier 2), external providers (Tier 3). |
| [[engineering/hermes-kanban]] | Durable SQLite-backed multi-agent task board with kanban_* tools. Peer coordination vs hierarchical delegate_task. |
| [[engineering/2026-06-19-engineering-agentic-loop-pattern]] | Inner-loop pattern (gather → act → verify → repeat) for an agent task — activation gate, six controls, six failure modes. Curated distillation of Anthropic + OpenAI engineering guidance applied to Hermes + MiniMax. |
| [[engineering/harness-engineering]] | Designing the agent's environment: AGENT.md / persistent context, plugin hooks, eval harness, guidance/corrections. Beyond prompt engineering — the harness is what surrounds the model. |
| [[engineering/loop-engineering]] | Next evolution beyond harness engineering — designing systems that *prompt* agents for you. Five building blocks + external memory compose into self-running outer loops. |
| [[engineering/multi-agent-orchestration-patterns]] | Five patterns for orchestrating multiple agents: sequential, concurrent, group chat, handoff, magentic. |
| [[engineering/async-subagents]] | Delegation that doesn't block the parent chat. Sub-agents run in their own session, return a final report. Recent addition to Hermes (June 2026). |
| [[engineering/hermes/skills]] | Skills are progressive-disclosure `SKILL.md` files. Description is the trigger; body loads on demand. Hermes self-authors skills from successful trajectories. |
| [[engineering/hermes/architecture]] | Hermes internals: 90-iteration default budget, 50%/85% compaction triggers, system-prompt-stability rule, plugin hooks. |
| [[engineering/hermes-native-mcp-catalog]] | Hermes ships with a built-in MCP server catalog — discovery + standardized interface for tool integrations. The distribution channel for skill ↔ tool wiring. |
| [[engineering/hermes/2026-06-20-hermes-blender-mcp-skill]] | Concrete creative optional skill — drives a running Blender instance over MCP/TCP with arbitrary bpy Python, scene queries, and viewport screenshots. Reference example for desktop-bridge skills. |
| [[engineering/hermes-stripe-partnership]] | Hermes + Stripe partnership: three new skills (stripe-link-cli, mpp-agent, stripe-projects) for web payments, per-call APIs, SaaS provisioning. Safety model for credential handling. |
| [[engineering/2026-06-18-hermes-portal-teams]] | Hermes Portal team billing — shared credit pool, per-member spend caps, Spend Intelligence dashboard, multi-team membership. First org-level deployment feature. |
| [[engineering/2026-06-21-hermes-blank-slate-setup]] | `hermes setup` third mode (Blank Slate) — minimal-baseline install with provider + model + file + terminal only. Best for power users, minimal-surface installs, debugging. |
| [[engineering/2026-06-27-hermes-moa-virtual-models]] | MoA (mixture-of-agents) presets exposed as virtual models in the routing layer; provider-agnostic N-way ensembles; +8%/+11% HermesBench vs Opus 4.8 / GPT 5.5. |
| [[engineering/2026-06-27-hermesbench]] | Nous Research's internal benchmark suite — reasoning, coding, tool-use, multi-step. Basis for MoA release numbers. Full leaderboard pre-announced. |

### Hermes Reference (`engineering/hermes/`)
| Card | Summary |
|------|---------|
| [[engineering/hermes/README]] | Entry point — Hermes Agent overview, CLI, messaging gateway, skills, memory, cron, kanban. |
| [[engineering/hermes/security]] | Trust model, OS-level isolation as the only boundary, in-process heuristics, plugin trust, deployment hardening. |
| [[engineering/hermes/plugins]] | General plugins, memory providers, model providers — ABC + orchestrator pattern for extending Hermes. |
| [[engineering/hermes/observer-hooks]] | Read-only telemetry contract — pre/post API request, pre/post tool call, session lifecycle hooks. |
| [[engineering/hermes/middleware]] | Behavior-changing plugin hooks — llm_request, llm_execution, tool_request, tool_execution. |
| [[engineering/hermes/profiles]] | Multi-instance isolated profiles — profile-aware paths, gateway token locks, multi-gateway deployment. |
| [[engineering/hermes/network-egress-isolation]] | Docker network segmentation — internal + egress (proxy allowlist) networks for prompt injection defense. |
| [[engineering/hermes/contributing]] | Dev setup, skill vs tool decision, cross-platform rules, dependency pinning, PR process. |
| [[engineering/hermes/cronjob]] | Scheduled-task subsystem — gateway-owned 60s tick, single `cronjob` tool, skill attachments, multi-platform delivery, no-agent mode for watchdogs, `wakeAgent` gate. |
| [[engineering/hermes/messaging]] | Gateway — 22+ platform adapters (Telegram, Discord, Slack, WhatsApp, Signal, Email…), allowlist + pairing + admin/user tiers, shared slash commands, circuit breakers, day-2 ops. |

### Skill & Prompt Engineering
| Card | Summary |
|------|---------|
| [[engineering/progressive-disclosure-pattern]] | Three-level skill architecture: YAML frontmatter → SKILL.md body → linked files. Core pattern for triggering reliability. |
| [[engineering/skill-trigger-design]] | How to write the YAML description field (what + when), trigger phrase patterns, 90% trigger rate metric. |
| [[engineering/scaffolding-for-ai-agents]] | Tooling around the model determines outcomes more than the model itself. Scaffolding is the ceiling, model is the floor. |
| [[engineering/evals-ai-evaluation-frameworks]] | How to test AI outputs systematically. Eval types, metrics, frameworks, getting started. |
| [[engineering/agent-observability-opentelemetry]] | Tracing every reasoning step, tool call, and model response as structured spans using OTel GenAI conventions — vendor-neutral, step-level, privacy by default. |

### Development Workflow
| Card | Summary |
|------|---------|
| [[engineering/spec-driven-development]] | Spec-first development. Define what success looks like before writing code. |
| [[engineering/lean-engineering]] | Build minimum viable, validate, iterate. Delete before optimize. |
| [[engineering/ask-mode-before-code-mode]] | Two-step pattern: plan in Ask mode, execute in Code mode. Prevents expensive wrong turns. |
| [[engineering/agents-md-for-persistent-context]] | Keep a project-level context file. Reduces start-from-zero tax on every session. |

### AI Team Methodologies
| Card | Summary |
|------|---------|
| [[engineering/gstack-process-pattern]] | Garry Tan's AI team methodology — 6 roles (CEO/Eng Manager/Designer/QA/Security/Release) chained through sprint process (Think → Plan → Build → Review → Test → Ship → Reflect). |
| [[engineering/dispatch-routing-tiers]] | Task complexity-based routing — 5 tiers (Simple/Medium/Heavy/Full/Plan) that determine which AI process and context to use. Prevents over/under-engineering. |

### Agent Collaboration
| Card | Summary |
|------|---------|
| [[engineering/advisor-pattern-for-ai-agents]] | Smaller model as executor + larger model as advisor. Beats single large model for cost-effective agentic workflows. |

### Knowledge Management
| Card | Summary |
|------|---------|
| [[engineering/obsidian-vault-organization]] | Complete system: folder structure, naming, YAML properties, tag taxonomy, MOCs, inbox processing, quarterly review. |
| [[engineering/kb-evolution-plan]] | 6-month roadmap to evolve KB toward retrieval-first, properties-driven system. |

---

## Related MOCs
- [[concepts/MOC]] — Conceptual foundation these engineering practices are built on
- [[SYSTEM/MOC]] — KB infrastructure that supports these practices

## Open Questions
- Should `vibe-coder-security-checklist` be merged into `agentic-coding-principles` or kept separate? Checklist format is different.
- How does `ask-mode-before-code-mode` relate to `spec-driven-development`? They overlap.
- Should `multi-agent-orchestration-patterns` move to the Agent Collaboration cluster? It's about coordinating agents, not single-agent architecture.
- Should the Hermes Reference cluster be merged into Agent Architecture, or kept separate? The reference cards are more "docs" than "patterns" — kept separate for now.

## Card Count
39 cards (as of 2026-07-01): 3 Agentic Coding + 16 Agent Architecture (added moa-virtual-models, hermesbench, blank-slate-setup) + 10 Hermes Reference (added cronjob, messaging) + 5 Skill & Prompt Engineering + 4 Development Workflow + 2 AI Team Methodologies + 1 Agent Collaboration + 2 Knowledge Management = 43, minus the 4 placeholder template references (PARA, Atomic Habits, etc.) = 39 cards listed here. (Excludes 3 placeholder template references in KB-EVOLUTION-DESIGN + kb-evolution-plan, the 2 raw/ files, and this MOC entry itself.)

## Last Updated
2026-07-16 (Loop engineering enrichment from @AnatoliKopadze thread: folded curated additions into [[engineering/2026-06-19-engineering-agentic-loop-pattern]] — DISCOVER → PLAN → EXECUTE → VERIFY → ITERATE five-stage variant, seventh failure mode (Geoffrey Huntley's "Ralph Wiggum loop" silent-billing failure), new "The Cost That Matters" section (cost-per-accepted-change metric + 50% accept-rate tripwire); and into [[engineering/loop-engineering]] — "The Build Order That Works" section (manual → skill → loop → schedule); new raw card [[raw/2026-07-16-raw-anatoli-kopadze-loops]]. Mira product promotion at the tail of the thread deliberately skipped (competitor). No new curated cards; engineering count stays at 39.)