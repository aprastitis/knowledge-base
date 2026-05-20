# Spec-Driven Development (SDD)

**Source:** [github/spec-kit](https://github.com/github/spec-kit) | [spec-kit.github.io](https://github.github.io/spec-kit/)

## What It Is

Spec-Driven Development inverts the traditional code-first paradigm. Specifications don't serve code — code serves specifications. The PRD becomes the **source that generates implementation**, not a guide for it.

> "For decades, code has been king. Specifications were just scaffolding we built and discarded once the 'real work' of coding began."

In SDD:
- Specifications are **executable** — they generate working implementations
- Implementation plans are **precise definitions** that produce code
- The gap between spec and implementation is **eliminated** — not narrowed, but gone
- Code is the "last-mile" expression of a higher-level lingua franca (specifications)

## Core Principles

1. **Specifications as Source of Truth** — Code serves specs, not the other way around
2. **Intent-Driven Development** — Team intent expressed in natural language, design assets, core principles
3. **Continuous Refinement** — Consistency validation happens continuously, not as a one-time gate
4. **Research-Driven Context** — Agents investigate library compatibility, performance, security throughout
5. **Bidirectional Feedback** — Production metrics and incidents update specifications for the next regeneration
6. **Branching for Exploration** — Generate multiple implementations from the same spec to explore different optimization targets

## The SDD Workflow

### 1. Constitution (`/speckit.constitution`)
Create governing principles and development guidelines for the project.

### 2. Specify (`/speckit.specify`)
Describe what to build — the **what and why**, not the tech stack. The AI asks clarifying questions, identifies edge cases, defines acceptance criteria.

### 3. Plan (`/speckit.plan`)
Provide tech stack and architecture choices. Generates detailed implementation plan with technology rationale.

### 4. Tasks (`/speckit.tasks`)
Create actionable task list from the plan. Includes parallelization hints.

### 5. Implement (`/speckit.implement`)
Execute all tasks and build the feature according to the plan.

## Key Commands

| Command | Description |
|---------|-------------|
| `/speckit.constitution` | Create project governing principles |
| `/speckit.specify` | Define requirements and user stories |
| `/speckit.plan` | Create technical implementation plans |
| `/speckit.tasks` | Generate actionable task lists |
| `/speckit.implement` | Execute all tasks |
| `/speckit.clarify` | Clarify underspecified areas |
| `/speckit.analyze` | Cross-artifact consistency analysis |
| `/speckit.checklist` | Generate quality checklists |

## Development Phases

| Phase | Focus | Activities |
|-------|-------|------------|
| 0-to-1 (Greenfield) | Generate from scratch | High-level requirements → specifications → plan → build |
| Creative Exploration | Parallel implementations | Explore diverse solutions, multiple tech stacks, UX patterns |
| Iterative Enhancement (Brownfield) | Modernization | Add features iteratively, modernize legacy systems |

## Key Insight: Templates as Quality Constraints

The true power of SDD lies in how its templates constrain LLM behavior toward discipline:

- **Forced [NEEDS CLARIFICATION] markers** — Don't guess, mark ambiguities
- **"WHAT not HOW"** constraint — Focus on user needs, not implementation details
- **Phase gates** — e.g., Simplicity Gate (≤3 projects? No future-proofing?)
- **Test-first file ordering** — contracts → integration → e2e → unit → source
- **No speculation** — Only features traceable to concrete user stories

## Why It Matters Now

Three converging trends make SDD necessary:

1. **AI capabilities** have reached a threshold where natural language specs can reliably generate working code
2. **Software complexity** grows exponentially — manual alignment through docs is unsustainable
3. **Pace of change** accelerates — pivots become systematic regenerations, not manual rewrites

## Tooling

- **Requires:** `uv` package manager, Python 3.11+, Git
- **Install:** `uv tool install specify-cli --from git+https://github.com/github/spec-kit.git@vX.Y.Z`
- **Init:** `specify init my-project --integration copilot`
- **Supports 30+ AI coding agents** (CLI and IDE-based)

## Related

- [[llm-wiki-pattern]] — Documentation-as-code patterns
- [[lean-engineering]] — Lean principles applied to engineering; SDD is the extreme version — minimum spec, validate, regenerate
- [[first-principles]] — Reasoning from fundamental truths; SDD is first-principles applied to the spec-implementation gap
- [[agentic-coding-principles]] — Agentic coding provides the human accountability and verification layer around AI-generated code; complements SDD's spec-first approach with human-in-the-loop discipline