---
type: system
status: active
date: 2026-05-25
purpose: Folder structure, folder rules, archive never-delete policy, update schedule
tags: [kb-infrastructure]
---

# SYSTEM/ — Knowledge Base Infrastructure

This folder holds KB maintenance files — not content cards.

## Contents

| File | Purpose |
|------|---------|
| `log.md` | Running log of every KB change — what was added/updated, when, source |
| `changelog.md` | Auto-generated change summary for the index |
| `operations.md` | My decision log — why I placed things where, what I skipped, outcomes |
| `README.md` | This file — structural documentation |

## Folder Rules

Every card in the KB lives in exactly one folder. No overlaps.

### `concepts/`
**What belongs here:** Patterns, frameworks, mental models, ideas.
**What never belongs here:** Engineering-specific practices tied to a specific toolchain.
**Examples:** llm-wiki-pattern, soul-md-identity-layer, self-evolving-skills, personal-operating-system.

### `engineering/`
**What belongs here:** Engineering practices, methodologies, technical approaches that are implementable.
**What never belongs here:** Pure concepts or mental models without engineering context.
**Examples:** spec-driven-development, lean-engineering, agentic-coding-principles, hermes-kanban, vibe-coder-security-checklist.

### `first-principles/`
**What belongs here:** Reasoning from fundamental truths. Physics-style first-principles thinking applied to any domain.
**What never belongs here:** Mental models (that's mental-models/), reference material (that's raw/).
**Examples:** physics-first-principles, gepa-prompt-evolution.

### `mental-models/`
**What belongs here:** Decision/reasoning frameworks. How to think, not what to build.
**What never belongs here:** Implementable engineering practices.
**Examples:** elon-musk-5-step.

### `people/`
**What belongs here:** Person-specific knowledge — their frameworks, quotes, methods, biography.
**What never belongs here:** Abstract concepts that aren't tied to a specific person.
**Examples:** elon-musk, karpathy-knowledge-substrate.

### `raw/`
**What belongs here:** Unprocessed source material. Tweets, articles, transcripts — stored verbatim.
**What never belongs here:** Edited or synthesized content. If I edited it, it belongs in a card.
**Examples:** llm-wiki-karpathy, elon-musk-5-step-corporate-rebels.

### `hermes-saas-audit.md`
Lives at root — audit of Hermes SaaS, not a card.

## Archive Never-Delete Rule

KB files are **never deleted**. If a card becomes outdated or redundant:
1. Move it to `.archive/` subfolder within the same directory
2. Update `index.md` to remove the link
3. Log the archival in `operations.md`

The cost of keeping outdated cards is zero. The cost of losing a pattern I can't reconstruct is high.

## KB State Tracking

- `memory/kb-state.md` — weekly review log (exists in /workspace/memory/)
- `memory/kb-queue/` — pending material as individual .md files
- Operations log — this folder (`SYSTEM/operations.md`)

## Update Schedule

- `log.md` and `changelog.md` updated after every ingest
- `operations.md` appended after every ingest decision
- `kb-state.md` updated weekly during Sunday review
- `index.md` updated whenever a new card is added