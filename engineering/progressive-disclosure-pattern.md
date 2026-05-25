---
type: engineering
status: active
date: 2026-05-20
summary: Three-level skill architecture: YAML frontmatter → SKILL.md body → linked files. Core pattern for triggering reliability.
tags: [agent-design, knowledge-management]
related: [[skill-trigger-design]], [[self-evolving-skills]]
sources: [internal]
---

# Progressive Disclosure Pattern

Skills use a three-level system that lets Claude access deep expertise without burning through context. Each level loads at a different point in the interaction.

## The Three Levels

### Level 1 — YAML Frontmatter (Always in System Prompt)
```yaml
---
name: my-skill
description: What it does. Use when user asks to [specific trigger phrases].
---
```
Purpose: Claude knows when to consider the skill without loading the full content.

### Level 2 — SKILL.md Body (Loaded When Relevant)
Full instructions, guidance, domain knowledge, examples, error handling. Claude loads this when it determines the skill is applicable to the current task.

### Level 3 — Linked Files (Navigated as Needed)
Scripts, references, templates, assets. Claude discovers and loads these selectively during execution.

## Why This Matters

Without progressive disclosure:
- Full skill content in every conversation → context overflow, slow responses, wasted tokens
- Claude can't tell when a skill is relevant → over-triggering on unrelated queries

With progressive disclosure:
- Token usage is minimized because only relevant skills load their full content
- Claude knows what each skill does at a glance → accurate triggering
- Deep expertise is available on demand without preloading

## Key Rule: Description Must Have Both

The YAML frontmatter `description` field is the critical trigger mechanism. It must include:
1. **What the skill does** — the capability it adds
2. **When to use it** — specific trigger phrases or situations

```yaml
# ❌ Incomplete — only says what, not when
description: Reviews code for security issues.

# ✅ Complete — says what AND when
description: Reviews code for security vulnerabilities. Use when reviewing pull requests,
auditing code changes, or asking "is this secure?"
```

## Relationship to My Work

When I build skills, I should design all three levels:
- Frontmatter: Clear what + when triggers
- Body: Complete instructions, examples, error handling
- Linked files: Scripts and templates that get used during execution

This pattern is the core architectural decision for any skill. Get the frontmatter right and the triggering works. Get the body right and the execution works.

## See Also

- [[skill-trigger-design]] — How to write the description field and test triggering
- [[scaffolding-for-ai-agents]] — How tooling around the model determines outcomes