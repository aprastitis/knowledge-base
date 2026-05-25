---
type: engineering
status: active
date: 2026-05-20
summary: Project-level context file with naming conventions, business logic, known quirks; reduces start-from-zero tax on every session
tags: [engineering-practice, agentic-coding]
related: [[ask-mode-before-code-mode]], [[spec-driven-development]]
sources: [internal]
---

# AGENTS.md for Persistent Context

AGENTS.md is a file in a project or repo that provides persistent context for AI agents. It contains information the agent can't infer from code alone — naming conventions, business logic, known quirks, dependencies, patterns.

## Why It Matters

Without AGENTS.md, every agent session starts from zero:
- Must infer conventions from code (error-prone, slow)
- Can't know business logic that isn't in the code
- Repeatedly asks clarifying questions
- Context window burned on information that could be persistent

With AGENTS.md:
- Agent has stable, persistent context across sessions
- Conventions and patterns established upfront
- Business logic and domain knowledge pre-loaded
- Faster onboarding, fewer clarifying questions

## What Goes in AGENTS.md

**Conventions the agent can't infer from code:**
- Naming conventions (file naming, variable naming, branch naming)
- Module organization patterns
- Import structure preferences
- Code style preferences

**Business logic that isn't in the code:**
- Why certain decisions were made
- Known limitations or technical debt
- Dependencies on external systems
- Workflow-specific knowledge (what "完成" means in this context, etc.)

**Operational context:**
- How to run tests, build, deploy
- Known failure modes and how to handle them
- Connection strings, service endpoints (without secrets)
- CI/CD pipeline details

## Example Structure

```markdown
# Project Context

## Naming Conventions
- Files: kebab-case (my-file.md)
- Branches: feature/[ticket-id]-[short-description]
- Variables: camelCase for JS, snake_case for Python

## Business Logic
- Auth flow lives in auth/service.ts — do not modify directly
- Payment processing requires a review step — always flag PRs touching payments/
- Our A/B testing framework uses [library-name] — import from there, not elsewhere

## Known Quirks
- legacy_adapter.py has incorrect type hints — don't trust them
- DB schema was auto-migrated and has inconsistencies — check with [person] before changing
- Fridays at 4pm the staging environment gets heavy traffic — avoid deploying then

## Running the Project
- Tests: `npm test` (requires Docker running)
- Build: `npm run build`
- Deploy: requires VPN — see [doc-link]
```

## Relationship to My Work

I maintain several context files already (AGENTS.md, SOUL.md, MEMORY.md). AGENTS.md is the project-level version — it lives in the repo and provides context for anyone working in it, including me.

When working in a new project or repo, I should:
1. Check for AGENTS.md (or equivalent) first
2. Add relevant context to it as I learn it
3. Update it when I discover conventions or patterns

This reduces the "start from zero" tax on every session and makes subsequent work faster.

## See Also

- [[progressive-disclosure-pattern]] — Three-level skill architecture
- [[ask-mode-before-code-mode]] — Two-step pattern for complex tasks
- [[scaffolding-for-ai-agents]] — How tooling around models determines outcomes