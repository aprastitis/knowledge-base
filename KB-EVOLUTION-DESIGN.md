# KB Evolution — Design Document

## Type
Engineering / Knowledge Management

## Summary
Working design for evolving the aprastitis/knowledge-base from its current freeform state to a retrieval-first, properties-driven system. Covers frontmatter schemas, tag taxonomy, MOC priorities, naming conventions, and retrofit strategy.

**Created:** 2026-05-25  
**Driven by:** [[engineering/kb-evolution-plan]]  
**Status:** Design complete — implementation begins immediately

---

## 1. Frontmatter Schema by Card Type

### `concept` cards
```yaml
---
type: concept
status: active           # active | reference | archived
date: 2026-MM-DD         # date added to KB
summary: One-line what this is
tags: [knowledge-management, agent-design, reasoning]
related: [[card-name]], [[card-name]]
sources: [source description or URL]
---
```

**Fields specific to concepts:**
| Field | Purpose |
|-------|---------|
| `summary` | One-liner for MOCs, index, and quick recall |
| `related` | Wikilink list — same as bottom-of-card links, mirrored here for programmatic access |
| `sources` | Where the idea came from (used for `#source/*` tag generation) |

### `engineering` cards
```yaml
---
type: engineering
status: active           # active | reference | archived
date: 2026-MM-DD
summary: One-line what this is
tags: [engineering-practice, agentic-coding, testing]
related: [[card-name]], [[card-name]]
sources: [source or internal]
---
```

**Fields same as concept.** No additional type-specific fields for engineering cards in our current scope.

### `mental-model` cards
```yaml
---
type: mental-model
status: active
date: 2026-MM-DD
summary: One-line what this is
tags: [decision-framework, reasoning, leadership]
related: [[card-name]], [[card-name]]
sources: [source description or URL]
---
```

### `person` cards
```yaml
---
type: person
status: active
date: 2026-MM-DD
summary: One-line who this is and their domain
tags: [person, machine-learning, leadership, etc.]
related: [[card-name]], [[card-name]]
sources: [source or internal]
---
```

### `raw` cards
```yaml
---
type: raw
status: reference        # raw is always reference — never active
date: 2026-MM-DD
summary: One-line what the source is
tags: [source/username or source/org if applicable]
related: []              # raw cards don't need related links
sources: [original URL or "internal"]
---
```

### `system` cards (SYSTEM folder)
```yaml
---
type: system
status: active
date: 2026-MM-DD
purpose: One-line what this file does
tags: [kb-infrastructure]
---
```

**Rationale for system cards:** SYSTEM files are infrastructure, not content. They get `purpose` instead of `summary` and `type: system` so they're distinguishable from content cards in any automated scanning.

---

## 2. Tag Taxonomy

Three tag categories, hard prefix rule:

### Topic Tags (no prefix)
These reflect **what the card is about** — the subject domain.

**Our actual tag universe** (based on current card content):

| Tag | Cards | Purpose |
|-----|-------|---------|
| `knowledge-management` | ~8 | Everything about KB structure, retrieval, organization |
| `agent-design` | ~5 | Agent architecture, memory layers, identity |
| `reasoning` | ~4 | First principles, mental models, decision frameworks |
| `engineering-practice` | ~6 | Coding practices, testing, spec-driven development |
| `agentic-coding` | ~4 | Autonomous coding, vibe coding, tooling |
| `hermes` | ~3 | Hermes-specific architecture and tools |
| `leadership` | ~2 | Management, organizational thinking |
| `machine-learning` | ~2 | ML concepts, LLM patterns |

**Tag creation rule (enforced):** Only create a new topic tag if it will appear on 5+ cards. This prevents tag sprawl.

### Status Tags (prefix `status/`)
These reflect workflow state — used for filtering active vs archived content.

| Tag | Use |
|-----|-----|
| `#status/active` | Card is current, in use |
| `#status/archived` | Card moved to .archive/ |
| `#status/reference` | Factual/reference cards (mostly /raw cards) |
| `#status/inbox` | Card in 00-INBOX/ pending processing |

### Source Tags (prefix `source/`)
One tag per distinct source material.

| Tag | Cards |
|-----|-------|
| `#source/cyrilxbt` | 1 (obsidian-vault-organization) |
| `#source/karpathy` | 2 (llm-wiki-pattern, karpathy-knowledge-substrate) |
| `#source/anthropic` | 0 (anthropic-building-skills-guide in raw) |
| `#source/isaacson` | 1 (elon-musk-5-step) |
| `#source/corporate-rebels` | 1 (elon-musk-5-step-corporate-rebels) |
| `#source/openai` | 0 (openai-how-openai-uses-codex in raw) |
| `#source/akshay-pachaar` | 1 (hermes-three-tier-memory) |
| `#source/internal` | ~18 (most cards are internal/conversation sourced) |

**Rule:** Source tags are auto-derived from the `sources` field in frontmatter — no manual tagging needed for sources.

### Tag Application Summary

When adding frontmatter to a card:
1. **Topic tags** — Based on what the card is about (must hit 5+ threshold to create)
2. **Status tag** — Always `#status/active` for new cards (or `reference` for raw)
3. **Source tag** — Derived from `sources` field, only if the source is a recognized external source

---

## 3. Priority MOC List

**Which 3-5 topics need MOCs first?**

| Priority | MOC | Rationale | Card Count |
|----------|-----|-----------|------------|
| 1 | `concepts/MOC.md` | Concepts is our largest folder with high cross-reference density; MOC will unify the cluster | 5 cards |
| 2 | `engineering/MOC.md` | Engineering is our largest folder (14 cards); without MOC, topic clusters are invisible | 14 cards |
| 3 | `SYSTEM/MOC.md` | SYSTEM is infrastructure, not content — needs its own MOC to keep it visible and maintained | 5 files |
| 4 | `mental-models/MOC.md` | Only 1 card now but growing; worth establishing the pattern early | 1 card |
| 5 | `people/MOC.md` | Only 2 cards but person-centric knowledge has natural clustering; worth planning ahead | 2 cards |

**Not creating yet (not enough cards):**
- `first-principles/MOC.md` — only 2 cards
- `raw/MOC.md` — raw is a drop zone, not a topic cluster; index.md already covers it

**MOC creation trigger:** When a folder reaches 10+ cards or 3+ distinct topic clusters, create an MOC.

---

## 4. Naming Convention

**Current state:**
- Most cards use natural language names: `retrieval-first-principle.md`, `elon-musk-5-step.md`, `ask-mode-before-code-mode.md`
- Engineering cards use descriptive slug format
- SYSTEM files use clear names: `log.md`, `changelog.md`, `operations.md`

**Evolution plan says:** New cards follow `YYYY-MM-DD-[type]-[topic].md`

**Design decision:** We adopt the convention for new cards only. No mass rename of existing cards.

**New card naming pattern:**
```
YYYY-MM-DD-[type]-[descriptive-slug].md
```

**Examples:**
```
2026-05-25-concept-retrieval-first-principle.md
2026-05-25-engineering-kb-evolution-design.md
2026-05-25-mental-model-5-step-process.md
```

**Slug rules:**
- Lowercase only
- Hyphens between words (no underscores)
- Short but descriptive — aim for 2-4 words after the type
- No stop words (the, and, for, with) in slugs

**Existing cards keep their names** (per evolution plan: no mass rename). They will get frontmatter opportunistically.

---

## 5. Retrofit Strategy

**Question:** How should we handle existing cards that don't have frontmatter?

**Answer: Opportunistic, not mass**

Rationale:
- Mass retrofit is time-consuming and error-prone
- We interact with cards frequently enough that frontmatter will get added naturally
- A card without frontmatter still works — wikilinks, content, and folder structure are intact

**Implementation:**

1. **New cards always get frontmatter** — this is the habit that matters
2. **Existing cards get frontmatter when touched** — if I read a card to reference it or update it, I add frontmatter then
3. **MOC creation triggers full frontmatter audit** — when we create `concepts/MOC.md`, I'll add frontmatter to all concept cards at once since I'm reading them all anyway
4. **Index.md frontmatter scan** — during Sunday weekly review, I can add frontmatter to any cards that appear in index but lack frontmatter

**Frontmatter audit checklist** (run during MOC creation):
```
For each card in folder:
  - Does it have YAML frontmatter? → If no, add it
  - Does frontmatter have all required fields for its type? → If no, complete it
  - Are related links reflected in frontmatter? → Sync with bottom-of-card related section
  - Is the status correct? → active/reference/archived
  - Are topic tags present (min 2)? → Add if missing
```

**What NOT to do:** Write a script to mass-add frontmatter to all 30+ cards in one shot. The risk of mistakes is high, and the benefit is low since we touch cards frequently enough.

---

## 6. INBOX Decision

**Evolution plan proposes** `00-INBOX/` folder.

**Design decision: Skip INBOX for now**

Rationale:
- Our KB is primarily Cerebro-maintained, not human-capture
- Andreas gives me material → I process it immediately (not capture-then-process)
- We have `memory/kb-queue/` for deferred material that needs research
- The queue is processed during weekly review
- INBOX adds complexity without solving a real problem for our use case

**When to revisit:** If Andreas starts capturing raw notes that need processing before filing, we add INBOX then.

---

## 7. Implementation Order

1. **Create** `concepts/MOC.md`, `engineering/MOC.md`, `SYSTEM/MOC.md` ← *doing this now*
2. **Create** this design document → `KB-EVOLUTION-DESIGN.md`
3. **Update frontmatter** on all concept cards (since we're building concepts/MOC.md)
4. **Update frontmatter** on all engineering cards (since we're building engineering/MOC.md)
5. **Update frontmatter** on all system files (since we're building SYSTEM/MOC.md)
6. **Log** all changes in `SYSTEM/log.md` and `SYSTEM/operations.md`
7. **Push** to GitHub

---

## 8. Files Created

| File | Purpose |
|------|---------|
| `KB-EVOLUTION-DESIGN.md` | This document — working design reference |
| `concepts/MOC.md` | Map of Content for all concept cards |
| `engineering/MOC.md` | Map of Content for all engineering cards |
| `SYSTEM/MOC.md` | Map of Content for KB infrastructure |

---

## Related
- [[engineering/kb-evolution-plan]] — The plan this design implements
- [[engineering/obsidian-vault-organization]] — Source system for frontmatter schema
- [[concepts/retrieval-first-principle]] — The principle driving this evolution