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
| [[llm-wiki-pattern]] | LLM-maintained persistent wiki vs RAG; three layers, three ops (ingest/query/lint) | Knowledge Management |
| [[soul-md-identity-layer]] | Hand-authored static identity file — slot #1 in system prompt; the fixed frame for memory and skills | Agent Design |
| [[self-evolving-skills]] | Agent-authored procedural playbooks with background curation; the agent writes its own SKILL.md files | Agent Self-Improvement |
| [[retrieval-first-principle]] | Organize for retrieval, not storage. Every structural decision evaluated by whether it makes finding information faster. | Knowledge Management |
| [[personal-operating-system]] | Obsidian-based personal OS: 3-layer architecture (storage/intelligence/automation) designed to survive bad days and operate autonomously | Knowledge Management |

## Engineering (`/engineering`)
| Page | Summary | Category |
|------|---------|----------|
| [[agentic-coding-tools]] | Shift from copilot to autonomous agent; capability timeline; real production numbers (Shopify, Mercado Libre, Spotify); new features | Engineering Practice |
| [[lean-engineering]] | Build minimum viable, validate, iterate; delete before optimize | Engineering Practice |
| [[vibe-coder-security-checklist]] | Pre-launch security/privacy checklist for AI-built products — env vars, OWASP, rate limits, secrets management | Engineering Practice |
| [[agentic-coding-principles]] | Six principles and 28 practices for moving beyond vibe coding to production-quality AI-assisted development | Engineering Practice |
| [[advisor-pattern-for-ai-agents]] | Smaller model as executor + larger model as advisor; beats single large model; cost-effective agentic workflows | Engineering Practice |
| [[obsidian-vault-organization]] | Complete system for organizing a vault: folder structure, naming convention, YAML properties, tag taxonomy, MOCs, inbox processing, quarterly review. | Knowledge Management |
| [[kb-evolution-plan]] | Our 6-month roadmap to evolve our KB from freeform wiki to retrieval-first, properties-driven system. Phases: foundation, structure, maintenance, intelligence. | Knowledge Management |
| [[evals-ai-evaluation-frameworks]] | How to test AI outputs systematically; eval types, metrics, frameworks, getting started | Engineering Practice |
| [[scaffolding-for-ai-agents]] | Tooling around the model determines outcomes more than the model itself; scaffolding is the ceiling, model is the floor | Engineering Practice |
| [[progressive-disclosure-pattern]] | Three-level skill architecture: YAML frontmatter → SKILL.md body → linked files. Core pattern for building skills that trigger reliably | Engineering Practice |
| [[skill-trigger-design]] | How to write the YAML description field (what + when), trigger phrase patterns, 90% trigger rate metric, fixing under/over-triggering | Engineering Practice |
| [[ask-mode-before-code-mode]] | Two-step pattern: plan first in Ask mode, then execute in Code mode. Prevents expensive wrong turns on complex tasks | Engineering Practice |
| [[agents-md-for-persistent-context]] | Keep a project-level context file with naming conventions, business logic, known quirks. Reduces start-from-zero tax on every session | Engineering Practice |
| [[hermes-three-tier-memory]] | Three distinct memory layers: frozen micro-files (Tier 1), SQLite FTS (Tier 2), external providers (Tier 3) | Agent Architecture |
| [[hermes-kanban]] | Durable SQLite-backed multi-agent task board with kanban_* tools; peer coordination vs delegate_task's hierarchical model | Agent Collaboration |

## First Principles (`/first-principles`)
| Page | Summary | Category |
|------|---------|----------|
| [[physics-first-principles]] | Reason from fundamental truths, not analogy or convention | Reasoning Framework |
| [[gepa-prompt-evolution]] | Offline skill optimization via execution traces; genetic-Pareto approach to evolving prompts | AI Optimization |

## Mental Models (`/mental-models`)
| Page | Summary | Category |
|------|---------|----------|
| [[elon-musk-5-step]] | Question, delete, simplify, accelerate, automate — in that order | Decision Framework |

## People (`/people`)
| Page | Summary | Category |
|------|---------|----------|
| [[elon-musk]] | Management heuristics: hands-on rule, failure philosophy, hiring, skip-levels | Leadership |
| [[karpathy-knowledge-substrate]] | Personal KB as persistent second brain for AI agents | AI Framework |

## Raw Sources (`/raw`)
| File | Summary | Source |
|------|---------|--------|
| [[llm-wiki-karpathy]] | Karpathy's original LLM Wiki gist — full raw text | GitHub Gist |
| [[elon-musk-5-step-corporate-rebels]] | Detailed 5-step algorithm with Isaacson quotes + additional heuristics | Corporate Rebels |
| [[anthropic-building-skills-guide]] | "The Complete Guide to Building Skills for Claude" — full PDF with skill architecture, triggering, testing patterns | Anthropic PDF |
| [[openai-how-openai-uses-codex]] | "How OpenAI Uses Codex" — 7 use cases, best practices, engineering anecdotes from OpenAI teams | OpenAI PDF |

---
*Last updated: 2026-05-25*