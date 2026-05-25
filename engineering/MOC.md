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
| [[agentic-coding-principles]] | Six principles and 28 practices for production-quality AI-assisted development. Moves beyond vibe coding. |
| [[agentic-coding-tools]] | Capability timeline for autonomous coding tools. Production numbers from Shopify, Mercado Libre, Spotify. |
| [[vibe-coder-security-checklist]] | Pre-launch security/privacy checklist for AI-built products. Env vars, OWASP, rate limits, secrets. |

### Agent Architecture
| Card | Summary |
|------|---------|
| [[hermes-three-tier-memory]] | Three distinct memory layers: frozen micro-files (Tier 1), SQLite FTS (Tier 2), external providers (Tier 3). |
| [[hermes-kanban]] | Durable SQLite-backed multi-agent task board with kanban_* tools. Peer coordination vs hierarchical delegate_task. |

### Skill & Prompt Engineering
| Card | Summary |
|------|---------|
| [[progressive-disclosure-pattern]] | Three-level skill architecture: YAML frontmatter → SKILL.md body → linked files. Core pattern for triggering reliability. |
| [[skill-trigger-design]] | How to write the YAML description field (what + when), trigger phrase patterns, 90% trigger rate metric. |
| [[scaffolding-for-ai-agents]] | Tooling around the model determines outcomes more than the model itself. Scaffolding is the ceiling, model is the floor. |
| [[evals-ai-evaluation-frameworks]] | How to test AI outputs systematically. Eval types, metrics, frameworks, getting started. |

### Development Workflow
| Card | Summary |
|------|---------|
| [[spec-driven-development]] | Spec-first development. Define what success looks like before writing code. |
| [[lean-engineering]] | Build minimum viable, validate, iterate. Delete before optimize. |
| [[ask-mode-before-code-mode]] | Two-step pattern: plan in Ask mode, execute in Code mode. Prevents expensive wrong turns. |
| [[agents-md-for-persistent-context]] | Keep a project-level context file. Reduces start-from-zero tax on every session. |

### Agent Collaboration
| Card | Summary |
|------|---------|
| [[advisor-pattern-for-ai-agents]] | Smaller model as executor + larger model as advisor. Beats single large model for cost-effective agentic workflows. |

### Knowledge Management
| Card | Summary |
|------|---------|
| [[obsidian-vault-organization]] | Complete system: folder structure, naming, YAML properties, tag taxonomy, MOCs, inbox processing, quarterly review. |
| [[kb-evolution-plan]] | 6-month roadmap to evolve KB toward retrieval-first, properties-driven system. |

---

## Related MOCs
- [[concepts/MOC]] — Conceptual foundation these engineering practices are built on
- [[SYSTEM/MOC]] — KB infrastructure that supports these practices

## Open Questions
- Should vibe-coder-security-checklist be merged into agentic-coding-principles or kept separate? Checklist format is different.
- How does `ask-mode-before-code-mode` relate to `spec-driven-development`? They overlap.
- Should we have an `agentic-coding/MOC.md` to cluster the 4 agentic-coding cards together?

## Card Count
14 cards (as of 2026-05-25)

## Last Updated
2026-05-25