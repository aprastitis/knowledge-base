---
type: concept
status: active
date: 2026-04-26
summary: Organize for retrieval, not storage. Every structural decision evaluated by retrieval speed.
tags: [knowledge-management, reasoning]
related: [[llm-wiki-pattern]], [[obsidian-vault-organization]]
sources: [internal]
---

# Retrieval-First Principle

## Type
Concept / Knowledge Management

## Summary
Organize a knowledge base for retrieval, not storage. Every structural decision — folders, tags, naming — is evaluated by whether it makes finding information faster or slower.

## Core Insight

Most organizational systems fail because they are designed for **capture** rather than **retrieval**.

You create a folder called "Ideas" because that's what the note contains when you create it. Six months later you're looking for a business idea and can't remember if it was filed under Ideas, Projects, Business, or your daily note from that day.

The folder name made sense at capture time. It tells you nothing at retrieval time.

A well-organized system is designed from the retrieval end — asking: "When I need this information, what will I know about it that I can use to find it?"

## The Four Retrieval Dimensions

When you need a note in the future, you reliably know one or more of:

1. **Type** — What kind of content is it? (project, reference, daily note, task, book summary, idea?)
2. **Time** — When did you create or use it? (this week, this month, last year?)
3. **Topic** — What subject area is it about? (health, finances, machine learning?)
4. **Status** — What state is it in? (active, completed, archived, in progress?)

A good organizational system makes it possible to filter by any one of these dimensions — or any combination — in seconds.

## The Storage vs Retrieval Mindset

| Storage Mindset | Retrieval Mindset |
|---|---|
| Organize by what the thing is | Organize by how you'll search for it |
| Folders = categories | Folders = broad content types |
| Tags = everything that might matter | Tags = searchable patterns (5+ uses) |
| Naming = descriptive | Naming = lookup-friendly with date + type + topic |
| "Where does this go?" | "How will I find this?" |

## Applied to Our KB

Our KB (aprastitis/knowledge-base) currently uses:
- Wikilinks for cross-referencing ✅ (retrieval-friendly)
- Date-prefixed filenames ✅ (retrieval-friendly)
- Clear folder structure ✅ (concepts, engineering, mental-models, etc.)

Gaps to address:
- No structured properties (type, status, tags) — limits filtering
- No consistent tag taxonomy (topic/status/project prefixes)
- No Maps of Content (MOCs) for high-traffic topics
- No inbox processing habit

See [[engineering/obsidian-vault-organization]] for the full system including folder structure, naming convention, properties, and tagging.
See [[engineering/kb-evolution-plan]] for our roadmap to close these gaps.

## Related
- [[concepts/llm-wiki-pattern]] — Compounding wiki pattern; retrieval-first applied to LLM knowledge systems
- [[concepts/personal-operating-system]] — Personal OS built on Obsidian; related to knowledge retrieval architecture
- [[engineering/obsidian-vault-organization]] — The full organizational system
- [[engineering/kb-evolution-plan]] — Our plan to evolve our KB toward this principle