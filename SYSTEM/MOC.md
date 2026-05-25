# SYSTEM MOC

## Purpose
Map of Content for KB infrastructure files in `/SYSTEM/`. These files are the operating machinery of the knowledge base — they are not content cards but the tools that maintain the content.

## Tags
#kb-infrastructure  #status/active

---

## Infrastructure Files (by purpose)

### Documentation
| File | Purpose |
|------|---------|
| [[SYSTEM/README]] | Folder structure, folder rules (what belongs in each folder), archive never-delete policy, update schedule |

### Logging
| File | Purpose |
|------|---------|
| [[SYSTEM/log]] | Chronological record of every KB change — what was added/updated, when, source. Updated after every ingest. |
| [[SYSTEM/changelog]] | Auto-generated change summary for index. Mirrors recent entries from log.md in a formatted view. |

### Decision Tracking
| File | Purpose |
|------|---------|
| [[SYSTEM/operations]] | My decision log — why I placed things where, what I considered, what I skipped, outcomes. Appended after every ingest decision. |
| [[SYSTEM/sources]] | Index of all source material with concept tags and source URLs. Complete provenance trail. |

### Evolution & Design
| File | Purpose |
|------|---------|
| [[KB-EVOLUTION-DESIGN]] | Working design document for KB evolution. Frontmatter schemas, tag taxonomy, MOC priorities, naming conventions, retrofit strategy. |

---

## What SYSTEM Is NOT

- SYSTEM files are **not content cards** — they don't go in `/concepts/`, `/engineering/`, etc.
- SYSTEM files are **not project notes** — they have no `related` field, no card-level content
- SYSTEM files get `type: system` and `purpose:` instead of `summary:` — distinguishable in any automated scan

## The SYSTEM Rule

> SYSTEM is where the KB keeps itself running. It is not part of the KB's knowledge — it is the infrastructure that holds the knowledge.

## Related MOCs
- [[concepts/MOC]] — Content cards (not infrastructure)
- [[engineering/MOC]] — Content cards (not infrastructure)

## File Count
5 files (as of 2026-05-25)

## Last Updated
2026-05-25