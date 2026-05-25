# KB Evolution Plan

## Type
Engineering Practice / Knowledge Management

## Summary
Our roadmap to evolve the aprastitis/knowledge-base from its current freeform state toward a retrieval-first, properties-driven system. Six-month phased approach covering properties, tagging, MOCs, naming conventions, inbox processing, and quarterly maintenance.

---

## Current State Assessment

Our KB (aprastitis/knowledge-base) has a solid foundation:
- ✅ Wikilinks for cross-referencing — works well
- ✅ Date-prefixed filenames — retrieval-friendly
- ✅ Clear folder structure (concepts, engineering, mental-models, people, raw, SYSTEM)
- ✅ System files (log, changelog, operations, sources)
- ❌ No YAML frontmatter with structured properties
- ❌ No tag taxonomy (topic/status/project prefixes)
- ❌ No Maps of Content for high-traffic topics
- ❌ No inbox processing habit
- ❌ No quarterly maintenance routine

**Overall assessment:** We're a good wiki that could be a great searchable database. The gap is structure, not content quality.

---

## Phase 1: Foundation (Month 1-2)

### 1.1 Adopt frontmatter standard
Add YAML frontmatter to every new card going forward:

```yaml
---
type: [concept/engineering/mental-model/person/raw]
status: [active/archived/reference]
date: YYYY-MM-DD
tags: [topic1, topic2, topic3]
---
```

**Rules:**
- Every new card gets frontmatter at creation time
- Existing cards updated opportunistically (when reading/referencing)
- `type` maps to folder: `concept` → concepts/, `engineering` → engineering/, etc.
- `status`: new cards default to `active`; `reference` for factual cards that don't change; `archived` for outdated cards moved to .archive/

### 1.2 Establish tag taxonomy
Three categories, consistent prefixes:

| Prefix | Use | Examples |
|--------|-----|----------|
| (none) | Topic tags | #knowledge-management, #agent-design, #reasoning |
| `status/` | Workflow state | #status/active, #status/archived, #status/reference |
| `source/` | Material source | #source/cyrilxbt, #source/anthropic, #source/karpathy |

**Tag creation rule:** Only create a new tag if it will appear on 5+ notes.

### 1.3 Add backlog tagging
Go through existing cards and add tags to them opportunistically. No mass retrofit — just tag as we interact with cards.

---

## Phase 2: Structure (Month 2-3)

### 2.1 Naming convention upgrade
New cards follow pattern:

```
YYYY-MM-DD-[type]-[topic].md
```

Examples:
```
2026-05-25-concept-retrieval-first-principle.md
2026-05-25-engineering-obsidian-vault-organization.md
```

Existing cards keep current names (no mass rename needed).

### 2.2 Create first MOCs
Build Maps of Content for our highest-traffic topics:

**Priority MOCs:**
- `concepts/MOC.md` — Index of all concept cards
- `engineering/MOC.md` — Index of all engineering cards
- `SYSTEM/MOC.md` — Index of KB infrastructure (processes, not content)

**MOC format:**
```markdown
# [Topic] MOC

## Core Cards
[[card-name]] — one-line summary

## Related
[[card-name]]

## Open Questions
- [Things we're still figuring out]

## Tags
#topic  #status/active
```

### 2.3 INBOX folder activation
Set up `00-INBOX/` in our KB structure:
- Any card in progress or undecided goes here
- Weekly review processes the inbox
- Nothing lives in INBOX permanently

---

## Phase 3: Maintenance (Month 3-4)

### 3.1 Establish inbox processing habit
Process the KB inbox during our weekly review:
- Any card in INBOX → evaluate type, topic, status
- Add frontmatter, rename if needed
- Move to correct folder
- Update index.md if new card

### 3.2 Quarterly KB review
Every 3 months:
1. Folder audit — merge thin folders
2. Tag audit — prune tags on <5 cards
3. Archive sweep — move stale cards to .archive/
4. MOC refresh — update Maps of Content
5. Wikilink validation — check for broken links

---

## Phase 4: Intelligence (Month 4-6)

### 4.1 Claude integration
Connect our KB to a Claude instance via Filesystem MCP:
- Natural language retrieval ("find all notes about X from last 6 months")
- KB-wide queries for research tasks
- Automatic backlink suggestions when writing new cards

### 4.2 Dataview-style queries
If we move to Obsidian or a tool that supports it:
- Live dashboards for active projects
- Filtered views by type/status/tag
- Weekly generated digest of new cards

### 4.3 Automated KB reports
Build scripts that:
- List all cards added this month
- Flag cards without frontmatter
- Identify orphaned cards (no wikilinks)
- Generate weekly KB digest for Andreas

---

## Implementation Priorities

| Priority | Action | Impact | Effort |
|----------|--------|--------|--------|
| HIGH | Add frontmatter to new cards | Enables filtering | Low (habit change) |
| HIGH | Create `concepts/MOC.md` and `engineering/MOC.md` | Navigation + topic overview | Medium |
| HIGH | Tag existing cards opportunistically | Better retrieval | Low (as we read cards) |
| MEDIUM | Set up INBOX processing habit | Prevents pile-up | Medium (routine) |
| MEDIUM | Quarterly review routine | Prevents decay | Medium (30-60 min/qtr) |
| LOW | Claude integration | Natural language retrieval | High (setup) |
| LOW | Automated reports | Visibility | Medium (build once) |

---

## What This Means For Me (Cerebro)

When I add a new card to the KB, I will:
1. Add YAML frontmatter with type, status, date, tags
2. Use naming convention: `YYYY-MM-DD-[type]-[topic].md`
3. Add at least 2 wikilinks to related existing cards
4. If inbox: process during next weekly review
5. Log the addition in `SYSTEM/log.md`

When I interact with existing cards, I will:
- Add tags if relevant
- Update frontmatter if missing
- Add wikilinks to new related cards
- Flag any card that needs archiving

---

## Related
- [[concepts/retrieval-first-principle]] — The principle driving this evolution
- [[engineering/obsidian-vault-organization]] — The system we're evolving toward
- [[concepts/llm-wiki-pattern]] — Related compounding pattern; similar evolution applies
- [[SYSTEM/operations]] — Decision log for this evolution