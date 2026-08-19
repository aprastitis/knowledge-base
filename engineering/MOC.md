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
| [[engineering/hermes/2026-07-24-hermes-credential-firewall]] | Token-substituting proxy at the Docker sandbox network boundary. Real provider keys stay outside the sandbox; leaked tokens are useless outside the firewall. Pairs with [[engineering/hermes/network-egress-isolation]]. |
| [[engineering/hermes/contributing]] | Dev setup, skill vs tool decision, cross-platform rules, dependency pinning, PR process. |
| [[engineering/hermes/cronjob]] | Scheduled-task subsystem — gateway-owned 60s tick, single `cronjob` tool, skill attachments, multi-platform delivery, no-agent mode for watchdogs, `wakeAgent` gate. |
| [[engineering/hermes/messaging]] | Gateway — 22+ platform adapters (Telegram, Discord, Slack, WhatsApp, Signal, Email…), allowlist + pairing + admin/user tiers, shared slash commands, circuit breakers, day-2 ops. |
| [[engineering/hermes/2026-07-26-hermes-v0-18-judgment-release]] | v0.18.0 release synthesis — evidence-based completion, `/learn` + `/journey`, background fan-out, first-class MoA, scale-to-zero/draining, desktop Projects, Vertex AI, security hardening. |
| [[engineering/hermes/2026-08-02-hermes-v0-19-quicksilver-release]] | v0.19.0 release synthesis — Quicksilver speed spine (~80% cold-start cut), live reasoning streams, smart approvals, durable response delivery, terminal subscriptions, Bitwarden/1Password secrets, desktop/TUI polish, v0.19.1 patch. |
| [[engineering/hermes/2026-08-03-hermes-v0-20-herald-release]] | v0.20.0 "Herald" release synthesis — conversational voice with barge-in + wake words, A2A v1.0 wire protocol, signed outbound webhooks, Desktop becomes a platform, smarter loop compression. ~3,650 commits / 1,400 merged PRs since v0.19. |
| [[engineering/hermes/2026-08-03-a2a-v1-bundled-plugin]] | First bundled plugin implementing the Agent-to-Agent v1.0 protocol — Hermes can discover, talk to, and be driven by other A2A-compatible agents regardless of framework. Closes 4-year-old issue #514. |
| [[engineering/hermes/2026-08-03-hermes-cloud-launch]] | Hermes Cloud — first officially hosted, multi-tenant Hermes deployment. Two-click, 60-second provisioning billed through Nous Portal; auto-discovers in Hermes Desktop. |
| [[engineering/hermes/2026-07-23-tldraw-offline-skill]] | Optional skill under `optional-skills/creative/tldraw-offline` driving tldraw desktop over MCP. Embeds scripts in the saved `.tldr` for self-executing artifacts. Parallel to the Blender MCP card. |
| [[engineering/hermes/2026-08-06-hermes-actual-computer-local-inference]] | Hermes × Actual Computer pairing — out-of-the-box local inference stack. User's own hardware as the inference backend. Fourth instance of the "user's machine as agent's substrate" pattern (tldraw, Desktop Kanban, Blender MCP). |
| [[engineering/hermes/2026-08-10-hermes-browser-use-mode]] | Browser Use mode replaces ~12 individual browser tools with one tool that writes scripts via `@browser_use` CLI 3.0 backend. 48–66% token cut on browser-using requests, no accuracy drop. Establishes the "toolset key resolves to swappable backend" pattern. |
| [[engineering/hermes/2026-08-12-portable-profiles-export-import]] | `/export` and `/import` make profiles portable and shareable. One file packs skills, memory, persona, crons, plugins, settings, and desktop themes with credentials stripped. Sibling to [[engineering/hermes/profiles]] (runtime story); this is the distribution story. |
| [[engineering/hermes/2026-08-16-hermes-desktop-19x-session-load]] | Desktop session load 19× faster (Aug 16 2026). First Desktop-specific perf number in the KB; likely a session-state SQLite query/index/JSON-parse improvement. Extends the Quicksilver + Herald speed spine to the window-open path. |
| [[engineering/hermes/2026-08-16-hermes-desktop-cross-platform]] | Hermes Desktop GUI distributed for macOS, Windows, and Linux (Aug 16 2026). Distinct from CLI cross-platform and from v0.20's Windows hardening wave — distribution milestone for the user-facing Desktop app. Coordinated launch with the 19× perf fix. |
| [[engineering/hermes/2026-08-16-zero-data-retention-opt-out]] | Zero-data-retention opt-out at the inference-provider layer (Aug 16 2026). Provider-layer privacy primitive — complements the OS-boundary primitives in `security.md` and pairs with the local-inference story in `actual-computer-local-inference`. |
| [[engineering/hermes/2026-08-17-hermes-2500-contributors-milestone]] | Hermes Agent crossed 2,500 contributors on the weekend of Aug 15-17 2026 — ~4× the v0.20 base (~650) in two weeks. Community-scale inflection; review and triage are now the bottleneck, not contribution supply. |

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
55 cards (as of 2026-08-17): 3 Agentic Coding + 16 Agent Architecture + 26 Hermes Reference + 5 Skill & Prompt Engineering + 4 Development Workflow + 2 AI Team Methodologies + 1 Agent Collaboration + 2 Knowledge Management = 59, minus the 4 placeholder template references (PARA, Atomic Habits, etc.) = 55 cards listed here. (Excludes placeholder template references in schema/example docs, raw source files, and this MOC entry itself.)

## Last Updated
2026-08-17 (NousResearch scan 2026-08-17 ingest — 4 net-new cards: (1) **new** 2,500-contributors milestone card — post-v0.20 trajectory (450 → 650 → 2,500 in ~30 days), review-triage is now the bottleneck; (2) **new** Desktop 19× session-load card — first Desktop-specific perf number in the KB, likely a session-state SQLite query/index/JSON-parse improvement, extends the Quicksilver + Herald speed spine to the window-open path; (3) **new** Desktop cross-platform distribution card — Hermes Desktop GUI ships for macOS, Windows, and Linux; distinct from CLI cross-platform and v0.20's Windows hardening wave, coordinated launch with the 19× perf fix; (4) **new** zero-data-retention opt-out card — provider-layer privacy primitive, third boundary (inference provider) alongside OS and subprocess/network boundaries, pairs with the local-inference story in `actual-computer-local-inference`. Backlink catch-up: v0.19 + v0.20 release cards now backlink to the 2,500 milestone; v0.20 release card and `desktop-kanban-plugin` and `browser-use-mode` cards now backlink to the 19× session-load card; v0.20 release card and `desktop-kanban-plugin` now backlink to the cross-platform card; `actual-computer-local-inference` and `security.md` now backlink to the zero-data-retention card. Engineering count 51 → 55.)
2026-08-16 (Weekly review maintenance: added the three v0.20-window net-new cards from the 2026-08-13 NousResearch scan that had been omitted from the MOC since the 2026-08-14 ingest commit — Browser Use mode (2026-08-10), portable profiles /export//import (2026-08-12), and Hermes × Actual Computer local inference (2026-08-06). All three cards were already in index.md and committed; only the MOC was missing. Engineering curated count 48 → 51. Backlink catch-up work from the same 2026-08-14 batch (and the v0.20 batch) landed in this review: `architecture.md` ← Browser Use mode + v0.20 compression/iteration-limit overhaul; `security.md` ← portable-profiles credentials-stripping + Actual Compute user-controlled-substrate + v0.20 redaction/Windows-hardening/Docker-approval-gate hardening; `profiles.md` ← portable-profiles (sibling, distribution half of the profiles feature); `skills.md` ← v0.20 (grounded-citations skill + curator adopt flow); `messaging.md` ← v0.20 (Buzz adapter + voice + HSP org-skill sync). One malformed `[[url|alias]]` wikilink in `2026-08-03-a2a-v1-bundled-plugin.md` fixed to a regular markdown link. Pets still queued.)
2026-08-08 (v0.20 "Herald" batch ingest — completing the 2026-08-03 mid-week scan that was originally interrupted by the API-key fallback chain issue. Three new cards: (1) **new** Hermes v0.20.0 "Herald" release synthesis — conversational voice + A2A + signed webhooks + Desktop platform + loop compression; (2) **new** A2A v1.0 bundled plugin card — closes 4-year-old issue #514; (3) **new** Hermes Cloud launch card — first hosted multi-tenant deployment via Nous Portal. Engineering curated count 45 → 48. Pets still queued.)
2026-08-03 (Mid-week NousResearch scan ingest: (1) **new** Desktop Kanban plugin card — first official Hermes Desktop plugin, establishes UI-shell extension surface (page/sidebar/hotkeys/status-bar/backend-endpoints/SDK) architecturally distinct from the general plugin system; (2) **new** thin public-presence card for the Hermes team's 2026-08-02 appearance on the Peter Yang / Karan 4D show. Backlinks added in `hermes/plugins`, `hermes-kanban`, and the v0.19 release card. Engineering count 43 → 45.)
2026-08-02 (Weekly review: processed the v0.19.0 "Quicksilver" release, the credential firewall primitive, and the tldraw-offline optional skill — all net-new in the v0.19 window. Engineering count 40 → 43.)
