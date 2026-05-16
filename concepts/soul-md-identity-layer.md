# SOUL.md — Identity Layer

## Type
Concept / Agent Design Pattern

## Summary
SOUL.md is the top layer in the Hermes system prompt — a hand-authored, static file that defines who the agent is before anything else loads. It sets personality, tone, communication style, and hard limits. Memory and skills are "the moving parts inside the fixed frame" of the agent's identity.

## How It Works

SOUL.md lives at `~/.hermes/SOUL.md` and occupies **slot #1** in the system prompt — before memory files, before skills, before anything else.

```markdown
# SOUL.md
You are a pragmatic senior engineer with strong taste.
You optimize for truth, clarity, and usefulness
over politeness theater.
```

The file is:
- **Hand-authored** — you write it once, tweak over time
- **Static** — it stays consistent across every project and every session
- **Required for identity** — if missing, Hermes falls back to a built-in default identity

## Why It Matters

Without an identity layer, every agent feels like the same agent wearing different hats. SOUL.md is what makes a "programmer agent" different from a "researcher agent" — not the instructions you give it mid-conversation, but the foundational frame it arrives with.

Everything that follows (the memory the agent writes, the skills it creates, the way it consolidates knowledge) happens through the lens of this identity.

> "SOUL.md is the fixed frame. Memory and skills are the moving parts inside it."

## Relationship to Other Layers

| Layer | Type | Changes? |
|-------|------|----------|
| SOUL.md | Identity | Static — written once |
| MEMORY.md / USER.md | Factual knowledge | Updated by agent during sessions |
| Skills | Procedures | Self-created and curated by agent |
| Sessions.db | Conversation history | Append-only |

## Key Principle

The agent's identity is **hand-authored and fixed**, not learned. This separates "who the agent is" (SOUL.md) from "what the agent knows" (memory) and "what the agent can do" (skills). Each layer has a different change cadence and authorship model.

## Related Concepts
- [[hermes-three-tier-memory]] — Memory sits below SOUL.md in the system prompt; the three tiers determine what facts are always available vs searchable
- [[self-evolving-skills]] — Skills are created and curated by the agent, but always executed through the lens of SOUL.md's identity

## Source
- https://x.com/akshay_pachaar/status/2054564519280804028 (Hermes Agent Masterclass by Akshay 🚀)