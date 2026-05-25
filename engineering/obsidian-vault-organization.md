---
type: engineering
status: active
date: 2026-05-25
summary: Complete system for organizing a vault: folder structure, naming convention, YAML properties, tag taxonomy, MOCs, inbox processing, quarterly review
tags: [knowledge-management, engineering-practice]
related: [[retrieval-first-principle]], [[kb-evolution-plan]]
sources: [cyrilxbt]
---

# Obsidian Vault Organization System

A complete system for organizing a knowledge vault (Obsidian or equivalent) so any note is findable in under 30 seconds. Built on retrieval-first principle. Covers folder structure, naming convention, properties system, tagging taxonomy, Maps of Content, inbox processing, search strategy, and quarterly maintenance.

Source: CyrilXBT (X/Twitter thread, 2026-05-25)

---

## The Retrieval-First Principle

Before structure: understand the principle that drives every organizational decision.

**You do not organize a vault to put things away neatly. You organize a vault to get things back quickly.**

Every folder, tag, and naming convention is evaluated against one question: *does this make retrieval faster or slower?*

---

## Folder Structure (5-8 top-level folders)

Folders are the coarsest organizational layer — reflect the broadest categories of content type.

**Too many folders is a common mistake.** A folder called "Python Programming Notes" seems useful. When you have 15 such folders, navigating between them becomes its own problem.

Recommended structure:

```
00 - INBOX/          # Everything lands here at capture. Processing queue, not storage.
01 - NOTES/           # Time-stamped captures: daily/, meetings/, books/, courses/
02 - PROJECTS/       # One subfolder per active project. Archive when complete.
03 - AREAS/          # Ongoing responsibilities with no end date: health/, finances/, career/
04 - RESOURCES/      # Reference material organized by topic: topics/, people/, tools/
05 - ARCHIVE/        # Everything no longer active. Storage is cheap; losing something important is not.
06 - SYSTEM/         # Vault infrastructure: templates/, MOCs/, config files.
```

### Folder Purpose Rules
- **INBOX** — Nothing lives here permanently. Processing queue.
- **NOTES** — Time-associated content. Find by roughly when it happened.
- **PROJECTS** — Defined outcome + end date. When complete → ARCHIVE.
- **AREAS** — Never-complete responsibilities. Always active.
- **RESOURCES** — Personal Wikipedia. Find by topic, not time.
- **ARCHIVE** — Never delete. Storage is cheap; losing context is expensive.
- **SYSTEM** — Makes the vault work; not content the vault contains.

---

## Naming Convention

The naming convention makes search reliable. Consistent pattern:

```
YYYY-MM-DD-[TYPE]-[TOPIC].md
```

### Examples
```
2026-05-20-daily-wednesday.md
2026-05-18-project-website-launch.md
2026-05-15-meeting-client-quarterly-review.md
2026-05-10-book-thinking-fast-and-slow.md
2026-04-28-resource-claude-prompting-techniques.md
2026-04-20-area-finances-q2-review.md
```

### What the date prefix does
1. Sorts files chronologically automatically (most recent at top)
2. Gives a retrieval path when you know roughly when (but not the name)
3. Prevents naming conflicts (same topic, different dates = different names)

### Type identifiers
- `daily` — Day-level notes
- `project` — Project-specific notes
- `meeting` — Meeting records
- `book` — Book summaries
- `course` — Course notes
- `resource` — Reference material
- `area` — Area-of-responsibility notes

---

## Properties System (YAML Frontmatter)

The naming convention handles search. The properties system handles filtering.

Every note has structured frontmatter:

```yaml
---
type: [daily/meeting/project/area/resource/book/course/idea/task]
status: [active/complete/archived/reference/waiting]
date: 2026-05-20
tags: [topic1, topic2, topic3]
---
```

### Universal properties (every note)
| Property | Values | Purpose |
|----------|--------|---------|
| `type` | daily, meeting, project, area, resource, book, course, idea, task | Content category |
| `status` | active, complete, archived, reference, waiting | Workflow state |
| `date` | YYYY-MM-DD | Creation or association date |
| `tags` | freeform | Topics and cross-references |

### Type-specific properties

**Project notes:**
```yaml
deadline: 2026-06-15
priority: high
next_action: Write the project brief
completion: 35
```

**Book notes:**
```yaml
author: Author Name
finished: 2026-05-10
rating: 4
key_insight: One sentence summary of the most important idea
```

**Meeting notes:**
```yaml
attendees: [Name1, Name2]
decisions: [Key decisions made]
actions: [Action items with owners]
```

**Resource notes:**
```yaml
topic: Primary topic
source: Where this information came from
reliability: high/medium/low
```

### Why properties matter
- Enable **Dataview queries** → live dashboards and filtered views
- `type=project AND status=active` → all active projects
- `type=book AND status=complete` → completed book summaries
- `tags contains [topic]` → everything about a topic regardless of type

---

## Tagging System (Three Categories)

Most vaults fail with no tags or too many inconsistent tags. The system that works:

### Three tag categories with prefix convention

| Prefix | Category | Examples |
|--------|----------|----------|
| (none) | Topic tags | #productivity, #machine-learning, #stoicism |
| `status/` | Status tags | #status/active, #status/waiting, #status/complete |
| `project/` | Project tags | #project/website-launch, #project/book-writing |

### The rule that keeps tags clean
**Only create a new tag if you will use it on at least 5 notes.**

Tags appearing on 1-2 notes are noise, not searchable patterns.

### Tag category behavior
- `#productivity` → all notes about productivity, any status
- `#status/active` → every active note, any topic
- `#project/website-launch` → every note for that project, any type

---

## Maps of Content (MOCs)

As the vault grows past hundreds of notes, flat search and filter become insufficient for navigating topic clusters.

A **Map of Content** is a note whose primary purpose is to link to other notes — an index for a related cluster. You do not move notes into it; you link to it from them.

### When to create an MOC
Create when a topic has accumulated 20+ notes and navigation through backlinks alone becomes difficult.

### MOC Example (Productivity MOC)
```markdown
# Productivity MOC

## Core Framework Notes
[[The PARA Method Explained]]
[[Why Most Productivity Systems Fail]]
[[Energy Management vs Time Management]]

## Tool Notes
[[Obsidian Setup and Workflow]]
[[Claude Code for Productivity]]
[[N8N Automation Workflows]]

## Book Notes
[[Getting Things Done - Key Ideas]]
[[Deep Work - Key Ideas]]
[[Atomic Habits - Key Ideas]]

## Project Applications
[[Q2 2026 Productivity Audit]]
[[Content Production System Build]]

## Open Questions
- What is the relationship between energy and deep work?
- How does AI change the productivity calculus?
```

---

## Inbox Processing Habit

The organizational system only works if new notes end up in the right place.

**INBOX** handles the most common failure: the note captured quickly without proper filing.

### Daily/Weekly Processing Routine
Set aside 15 minutes at the end of each workday (or weekly) to process the inbox.

For each note in INBOX:
1. **What type of content is this?** → determines top-level folder
2. **Does it already have a home?** → link to existing project/topic note or file in relevant subfolder
3. **Does it need its own note or should it expand an existing note?** → a single thought that expands an existing note belongs in that note

After processing:
- Update properties (type, status, tags)
- Rename to follow naming convention
- Move to correct folder

**Empty inbox. Vault organized.**

---

## Search Strategy (Four Modes)

Even with perfect organization, you'll sometimes not know folder or name. Obsidian's three search modes:

### Full-Text Search
Type any phrase or keyword from the note content. Searches every character of every note. Most powerful when you remember something specific they said.

### Property Search
Filter by properties directly: `type:project status:active` returns every active project note.

### Tag Search
Type the tag with hash: `#productivity` returns every note tagged with that topic.

### Date Navigation
Sort by creation date within a folder when you know roughly when you created the note.

### Four retrieval scenarios
| Scenario | Strategy |
|----------|----------|
| Remember what the note was about | Full-text search for distinctive phrase |
| Know type + roughly when | Combine type filter + date range |
| Know project or topic | Search by project tag or topic tag |
| Know roughly when created | Sort by creation date in relevant folder |

---

## Quarterly Vault Review

Organization degrades over time. Maintenance is required.

### Quarterly review checklist
1. **Folder audit** — Does every folder still represent active content? Merge folders with fewer than 5 notes.
2. **Tag audit** — Remove tags on only 1-2 notes. Identify topics with enough notes to deserve an MOC.
3. **Archive sweep** — Move completed projects and outdated references to ARCHIVE.
4. **Naming check** — Batch rename any notes not following the naming convention.

**Time: 30 minutes to 2 hours depending on vault size.**

Investment pays back every time you find a note instantly rather than spending 10 minutes searching.

---

## Claude Integration for Intelligent Retrieval

A well-organized vault + Claude Code (via Filesystem MCP) = natural language retrieval.

Instead of constructing a Dataview query:
- "Find all notes about pricing strategy I created in the last six months"
- "What have I written about managing energy versus managing time?"
- "Show me every project note that is currently active with a deadline before July"

Claude reads the vault structure, properties, and content — returns relevant notes with context.

The organizational system makes Claude's retrieval accurate. Claude makes the system's power accessible without requiring precise queries.

---

## Our KB Status

| Practice | Current State | Target State |
|----------|---------------|--------------|
| Folder structure | ✅ concepts/, engineering/, mental-models/, people/, raw/, SYSTEM/ | ✅ Close — we have similar structure |
| Naming convention | ✅ Date-prefixed files | Partial — no consistent type/topic in filename |
| Properties system | ❌ No YAML frontmatter | **Gap** — need to add type/status/tags to all cards |
| Tag categories | ❌ No structured tag system | **Gap** — need prefix taxonomy (topic/status/project) |
| Maps of Content | ❌ No MOCs | **Gap** — need MOCs for major topics |
| INBOX processing | ❌ No inbox habit | **Gap** — need to establish processing routine |
| Quarterly review | ❌ No maintenance habit | **Gap** — need to establish quarterly review |

See [[engineering/kb-evolution-plan]] for the roadmap to close these gaps.

---

## Related
- [[concepts/retrieval-first-principle]] — The core principle this system is built on
- [[concepts/llm-wiki-pattern]] — Related compounding wiki pattern for LLM knowledge systems
- [[concepts/personal-operating-system]] — Personal OS architecture that overlaps with this system
- [[engineering/kb-evolution-plan]] — Our roadmap to evolve our KB toward this system