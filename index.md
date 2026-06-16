# Knowledge Base Index

## System (`/SYSTEM`)
| File | Purpose |
|------|---------|
| [[SYSTEM/README]] | Folder structure, rules, and KB documentation |
| [[SYSTEM/log]] | Running log of every KB change |
| [[SYSTEM/changelog]] | Auto-generated change summary |
| [[SYSTEM/operations]] | My decision log — why I placed things where |
| [[SYSTEM/sources]] | Index of all material added to the KB with concept tags and source URLs |

## Concepts (`/concepts`)
| Page | Summary | Category |
|------|---------|----------|
| [[concepts/llm-wiki-pattern]] | LLM-maintained persistent wiki vs RAG; three layers, three ops (ingest/query/lint) | Knowledge Management |
| [[concepts/soul-md-identity-layer]] | Hand-authored static identity file — slot #1 in system prompt; the fixed frame for memory and skills | Agent Design |
| [[concepts/self-evolving-skills]] | Agent-authored procedural playbooks with background curation; the agent writes its own SKILL.md files | Agent Self-Improvement |
| [[concepts/retrieval-first-principle]] | Organize for retrieval, not storage. Every structural decision evaluated by whether it makes finding information faster. | Knowledge Management |
| [[concepts/personal-operating-system]] | Obsidian-based personal OS: 3-layer architecture (storage/intelligence/automation) designed to survive bad days and operate autonomously | Knowledge Management |
| [[concepts/krea-2-image-generation]] | Krea 2 is a foundation image generation model trained from scratch for aesthetic quality and fine control — style transfer, moodboard input, adjustable creativity | Creative AI |
| [[concepts/context-engineering]] | Curating the optimal set of tokens in context — attention as finite budget, observation masking, LLM summarization, hybrid strategies | Agent Design |
| [[concepts/model-context-protocol]] | Open protocol (JSON-RPC 2.0) for connecting AI apps to data sources and tools. USB-C of AI integration. Adopted by OpenAI, Google, Microsoft. | Tool Integration |

## Engineering (`/engineering`)
| Page | Summary | Category |
|------|---------|----------|
| [[engineering/agentic-coding-tools]] | Shift from copilot to autonomous agent; capability timeline; real production numbers (Shopify, Mercado Libre, Spotify); new features | Engineering Practice |
| [[engineering/lean-engineering]] | Build minimum viable, validate, iterate; delete before optimize | Engineering Practice |
| [[engineering/vibe-coder-security-checklist]] | Pre-launch security/privacy checklist for AI-built products — env vars, OWASP, rate limits, secrets management | Engineering Practice |
| [[engineering/agentic-coding-principles]] | Six principles and 28 practices for moving beyond vibe coding to production-quality AI-assisted development | Engineering Practice |
| [[engineering/harness-engineering]] | New discipline (Feb 2026) for designing the constraints and feedback loops that make AI agents reliable — Agent = Model + Harness. Five artifacts, three camps, five universal principles, build-to-delete paradox. | Engineering Practice |
| [[engineering/loop-engineering]] | Next evolution beyond harness engineering (Jun 2026) — designing systems that prompt agents for you. Five building blocks + external memory compose into self-running loops. Tool-agnostic (works in Claude Code or Codex). | Engineering Practice |
| [[engineering/async-subagents]] | The delegate tool is now non-blocking — parent agent continues while subagents run in the background | Agent Architecture |
| [[engineering/hermes-stripe-partnership]] | Hermes + Stripe partnership bringing three new skills for web payments, per-call APIs, and SaaS provisioning | Agent Framework |
| [[engineering/advisor-pattern-for-ai-agents]] | Smaller model as executor + larger model as advisor; beats single large model; cost-effective agentic workflows | Engineering Practice |
| [[engineering/obsidian-vault-organization]] | Complete system for organizing a vault: folder structure, naming convention, YAML properties, tag taxonomy, MOCs, inbox processing, quarterly review. | Knowledge Management |
| [[engineering/kb-evolution-plan]] | Our 6-month roadmap to evolve our KB from freeform wiki to retrieval-first, properties-driven system. Phases: foundation, structure, maintenance, intelligence. | Knowledge Management |
| [[engineering/evals-ai-evaluation-frameworks]] | How to test AI outputs systematically; eval types, metrics, frameworks, getting started | Engineering Practice |
| [[engineering/scaffolding-for-ai-agents]] | Tooling around the model determines outcomes more than the model itself; scaffolding is the ceiling, model is the floor | Engineering Practice |
| [[engineering/ask-mode-before-code-mode]] | Two-step pattern: plan first in Ask mode, then execute in Code mode. Prevents expensive wrong turns on complex tasks | Engineering Practice |
| [[engineering/progressive-disclosure-pattern]] | Three-level skill architecture: YAML frontmatter → SKILL.md body → linked files. Core pattern for building skills that trigger reliably | Engineering Practice |
| [[engineering/skill-trigger-design]] | How to write the YAML description field (what + when), trigger phrase patterns, 90% trigger rate metric, fixing under/over-triggering | Engineering Practice |
| [[engineering/spec-driven-development]] | Define what success looks like before writing code; spec-first development for clarity and alignment | Engineering Practice |
| [[engineering/gstack-process-pattern]] | Garry Tan's AI team methodology — 6 roles (CEO/Eng Manager/Designer/QA/Security/Release) chained through sprint process (Think → Plan → Build → Review → Test → Ship → Reflect) | Engineering Practice |
| [[engineering/dispatch-routing-tiers]] | Task complexity-based routing — 5 tiers (Simple/Medium/Heavy/Full/Plan) that determine which AI process and context to use. Prevents over/under-engineering. | Engineering Practice |
| [[engineering/agents-md-for-persistent-context]] | Keep a project-level context file with naming conventions, business logic, known quirks. Reduces start-from-zero tax on every session | Engineering Practice |
| [[engineering/hermes-native-mcp-catalog]] | Hermes ships with a native MCP server catalog — built-in directory of Model Context Protocol servers for connecting agents to external tools and data sources | Agent Architecture |
| [[engineering/hermes-three-tier-memory]] | Three distinct memory layers: frozen micro-files (Tier 1), SQLite FTS (Tier 2), external providers (Tier 3) | Agent Architecture |
| [[engineering/agent-observability-opentelemetry]] | Tracing every reasoning step, tool call, and model response as structured spans using OTel GenAI conventions — vendor-neutral, step-level, privacy by default | Engineering Practice |
| [[engineering/multi-agent-orchestration-patterns]] | Five patterns for multi-agent coordination: sequential, concurrent, group chat, handoff, and magentic. Complexity should match the problem. | Engineering Practice |
| [[engineering/hermes/README]] | Hermes Agent — self-improving AI agent from Nous Research; CLI, messaging gateway, skills, memory, cron, kanban | Agent Framework |
| [[engineering/hermes/architecture]] | Core agent loop, CLI architecture, TUI, project structure, toolsets, delegation, profiles | Agent Architecture |
| [[engineering/hermes/security]] | Trust model, OS-level isolation as the only boundary, in-process heuristics, plugin trust, deployment hardening | Security |
| [[engineering/hermes/plugins]] | General plugins, memory providers, model providers — ABC + orchestrator pattern | Extensibility |
| [[engineering/hermes/skills]] | SKILL.md format, authoring standards, platform gating, conditional activation, curator system | Skills |
| [[engineering/hermes/observer-hooks]] | Read-only telemetry contract — pre/post API request, pre/post tool call, session lifecycle hooks | Telemetry |
| [[engineering/hermes/middleware]] | Behavior-changing plugin hooks — llm_request, llm_execution, tool_request, tool_execution | Middleware |
| [[engineering/hermes/profiles]] | Multi-instance isolated profiles — profile-aware paths, gateway token locks, multi-gateway deployment | Profiles |
| [[engineering/hermes/kanban]] | Durable SQLite multi-agent task board — CLI, kanban_* toolsets, dispatcher, isolation model, rate limit trap | Agent Collaboration |
| [[engineering/hermes/network-egress-isolation]] | Docker network segmentation — internal + egress (proxy allowlist) networks for prompt injection defense | Security |
| [[engineering/hermes/contributing]] | Dev setup, skill vs tool decision, cross-platform rules, dependency pinning, PR process | Contributing |
| [[engineering/hermes-kanban]] | Durable SQLite-backed multi-agent task board with kanban_* tools; peer coordination vs delegate_task's hierarchical model | Agent Collaboration |

## First Principles (`/first-principles`)
| Page | Summary | Category |
|------|---------|----------|
| [[first-principles/physics-first-principles]] | Reason from fundamental truths, not analogy or convention | Reasoning Framework |
| [[first-principles/gepa-prompt-evolution]] | Offline skill optimization via execution traces; genetic-Pareto approach to evolving prompts | AI Optimization |

## Mental Models (`/mental-models`)
| Page | Summary | Category |
|------|---------|----------|
| [[mental-models/elon-musk-5-step]] | Question, delete, simplify, accelerate, automate — in that order | Decision Framework |

## People (`/people`)
| Page | Summary | Category |
|------|---------|----------|
| [[people/elon-musk]] | Management heuristics: hands-on rule, failure philosophy, hiring, skip-levels | Leadership |
| [[people/karpathy-knowledge-substrate]] | Personal KB as persistent second brain for AI agents | AI Framework |

## Raw Sources (`/raw`)
| File | Summary | Source |
|------|---------|--------|
| [[raw/llm-wiki-karpathy]] | Karpathy's original LLM Wiki gist — full raw text | GitHub Gist |
| [[raw/elon-musk-5-step-corporate-rebels]] | Detailed 5-step algorithm with Isaacson quotes + additional heuristics | Corporate Rebels |
| [[raw/anthropic-building-skills-guide]] | "The Complete Guide to Building Skills for Claude" — full PDF with skill architecture, triggering, testing patterns | Anthropic PDF |
| [[raw/openai-how-openai-uses-codex]] | "How OpenAI Uses Codex" — 7 use cases, best practices, engineering anecdotes from OpenAI teams | OpenAI PDF |

---
*Last updated: 2026-06-09*