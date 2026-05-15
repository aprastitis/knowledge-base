# LLM Wiki Pattern

## Type
Concept / Knowledge Management

## Summary
A framework where an LLM incrementally builds and maintains a persistent, compounding wiki — a structured, interlinked collection of markdown files that sits between raw sources and user queries. Knowledge is compiled once and kept current, not re-derived on every query (contrast with RAG).

## Core Architecture: Three Layers

### Layer 1: Raw Sources
Immutable collection of source documents (articles, papers, PDFs, images). The LLM reads from them but never modifies them. This is the source of truth.

### Layer 2: The Wiki
LLM-generated markdown files — summaries, entity pages, concept pages, comparisons. The LLM owns this layer entirely: creates pages, updates them when new sources arrive, maintains cross-references, keeps everything consistent.

### Layer 3: The Schema
A configuration file (e.g. `AGENTS.md` or `CLAUDE.md`) that tells the LLM how the wiki is structured, what conventions to follow, and what workflows to use when ingesting sources, answering queries, or maintaining the wiki. This is the key discipline-enforcing file.

## Three Core Operations

### Ingest
1. Drop new source into `/raw`
2. LLM reads it, extracts key information
3. LLM integrates into wiki — writes summary page, updates index, updates relevant entity/concept pages, appends entry to log
4. A single source may touch 10–15 wiki pages

### Query
1. Search wiki for relevant pages (index-first, then drill in)
2. Synthesize answer with citations
3. If the answer itself is valuable (comparison, analysis, discovery) → file it back into the wiki as a new page

### Lint
Periodic health-check:
- Contradictions between pages
- Stale claims superseded by newer sources
- Orphan pages with no inbound links
- Important concepts mentioned but lacking their own page
- Missing cross-references
- Data gaps fillable with web search

## Key Files

| File | Purpose |
|------|---------|
| `index.md` | Content-oriented catalog — all pages with one-line summaries, organized by category |
| `log.md` | Chronological append-only record — ingests, queries, lint passes (prefix format `## [YYYY-MM-DD]`) |

## Why This Beats Standard RAG

| RAG | LLM Wiki |
|-----|----------|
| Re-derives knowledge from scratch on every query | Knowledge already compiled and integrated |
| No accumulation — every question starts fresh | Cross-references already exist, synthesis already done |
| Fragments scattered across documents | Contradictions flagged, connections made |
| ~hundreds of pages requires embedding infrastructure | Index file handles ~100 sources / hundreds of pages without vector search |

## The Maintenance Problem (Solved)

Humans abandon wikis because the maintenance burden grows faster than the value:
- Updating cross-references
- Keeping summaries current
- Noting contradictions
- Maintaining consistency across dozens of pages

LLMs don't get bored, don't forget to update a cross-reference, and can touch 15 files in one pass. The cost of maintenance is near zero.

**Human's job:** curate sources, direct the analysis, ask good questions, think about what it all means.
**LLM's job:** everything else.

## Related Concepts
- [[karpathy-knowledge-substrate]] — This document IS the source for Karpathy's KB pattern
- [[physics-first-principles]] — The wiki approach is first-principles applied to knowledge management — not accepting "RAG is how you do KB with LLMs"
- [[lean-engineering]] — LLM Wiki is lean: minimum viable structure, validate, iterate

## Sources
- [Karpathy's LLM Wiki Gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
- [Vannevar Bush's Memex (1945)](https://en.wikipedia.org/wiki/Memex) — original vision that inspired this pattern