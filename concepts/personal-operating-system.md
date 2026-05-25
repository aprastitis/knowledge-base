---
type: concept
status: active
date: 2026-04-26
summary: Obsidian-based personal OS: 3-layer architecture (storage/intelligence/automation); survives bad days, operates autonomously
tags: [knowledge-management, agent-design]
related: [[retrieval-first-principle]], [[llm-wiki-pattern]]
sources: [internal]
---

# Personal Operating System (Obsidian-Based)

An architecture for turning a note-taking vault (Obsidian) into a self-operating system that maintains itself during low-energy periods, compounds knowledge intelligently, and produces useful outputs without requiring manual curation. Built on three layers: storage, intelligence, and automation. Designed specifically to survive "bad days" — periods of overwhelm when most productivity systems collapse under their own maintenance burden.

## Origin
**CyrilXBT** (Twitter/X: @cyrilXBT) — AI, tech, and crypto observer who shares emerging patterns before they go mainstream. Full thread: [x.com/cyrilxbt/status/2056924424838815824](https://x.com/cyrilxbt/status/2056924424838815824)

## Core Problem: Why Most Productivity Systems Break

Most systems fail for three reasons:

1. **Manual maintenance burden** — Project statuses need updating, dashboards need refreshing, tags need applying. When life gets busy, maintenance falls behind and the system becomes unreliable. An unreliable system gets abandoned.

2. **Compounding complexity** — Simple structures get layers of complexity added over time. Six months later: a baroque architecture that takes 20 minutes to navigate and requires its own documentation to understand.

3. **No intelligence layer** — The vault stores information but doesn't reason about it. You capture ideas but never find them again. The system is an archive, not an intelligence.

## The Three-Layer Architecture

### Layer 1: Storage Layer
**Obsidian** — Plain text Markdown files in a consistent folder structure. Every piece of information your life generates lives here: human-readable, machine-readable, and permanent.

### Layer 2: Intelligence Layer
**Claude Code + Filesystem MCP** — Claude reads your files, makes connections across them, generates outputs, and updates them based on what happens in your life. This transforms the vault from archive to active reasoning partner.

### Layer 3: Automation Layer
**N8N on a $5 server** — Schedules workflows, fires triggers, calls the Claude API, and passes information between systems without you initiating anything.

**Key insight:** Remove any one layer and you have something less:
- Storage without intelligence = an archive
- Storage + intelligence without automation = a tool you have to use
- All three together = a system that operates autonomously

## The 8-Folder Vault Structure

The vault structure is the foundation. Every note belongs in exactly one folder. No overlaps, no optional folders.

```
00 - CAPTURE/        [Everything unprocessed — absorbs everything at capture speed]
01 - ACTIVE/         [Only what's alive right now]
    projects/        [Active projects with overview, tasks, notes, outputs]
    areas/           [Health, finances, relationships, learning, career]
    daily/           [Daily notes by YYYY-MM-DD]
02 - RESOURCES/      [Reference library — research, references, templates, bookmarks]
03 - SYSTEM/         [The OS itself — CLAUDE.md, skills, workflows, logs]
04 - GENERATED/      [Claude outputs — briefings, summaries, analyses, drafts]
05 - QUEUE/          [Inbox for Claude tasks]
06 - CALENDAR/       [Time-based information — events, reviews]
07 - ARCHIVE/        [Completed projects and outdated content — never delete]
```

### Folder Logic
- **00 CAPTURE** — Capture and processing are separate activities. You capture at the speed of thought; you process at the speed of attention. The capture folder absorbs everything without judgment.
- **01 ACTIVE** — Only current projects, areas, and daily notes. Nothing historical, nothing aspirational.
- **03 SYSTEM** — The operating system itself. CLAUDE.md lives here. The infrastructure layer.
- **04 GENERATED** — Claude outputs. Never manually edit files here.
- **07 ARCHIVE** — Never delete. Storage is infinite; the cost of keeping everything is zero.

## The CLAUDE.md Template

The CLAUDE.md is the single source of truth that tells Claude everything about your life before any workflow runs. Without it, every session starts from zero. With it, every session starts from complete context.

```
# Personal Operating System — CLAUDE.md

## Identity
Name: [YOUR NAME]
Role: [YOUR PRIMARY ROLE]
Location: [YOUR CITY]

## Life Areas and Current Status
Health: [BRIEF STATUS]
Finances: [BRIEF STATUS]
Relationships: [BRIEF STATUS]
Learning: [BRIEF STATUS]
Career: [BRIEF STATUS]

## Active Projects
[PROJECT]: [ONE-SENTENCE DESCRIPTION] | Status: [STATUS] | Next: [SPECIFIC NEXT ACTION]

## Current Priorities
1. [MOST IMPORTANT THIS WEEK]
2. [SECOND MOST IMPORTANT]
3. [THIRD MOST IMPORTANT]

## Standards for Generated Content
Voice: [HOW YOU WRITE]
Format preferences: [YOUR PREFERENCES]
What you never want: [SPECIFIC THINGS TO AVOID]

## Operating Rules
- Never delete files. Move to ARCHIVE with timestamp.
- Never send communications without human review.
- Always date-stamp generated files as YYYY-MM-DD.
- Log every significant action in SYSTEM/logs/operations.md.
- When uncertain about placement, use GENERATED and flag.

## Update Schedule
This file is reviewed and updated: [SCHEDULE — e.g., every Monday morning]
```

**The most important habit:** Update the Current Priorities section every Monday morning. Five minutes of honest reflection dramatically improves the relevance of everything the system generates.

## The Five Workflows

### Workflow 1: Morning Briefing (6AM daily)
Claude reads CLAUDE.md and generates a morning briefing covering:
- **MOST IMPORTANT TODAY** — Single highest-leverage action based on current priorities
- **SCHEDULE** — Today's calendar events with one-sentence briefs
- **OPEN LOOPS** — Any items prefixed `OPEN:` from yesterday's daily note
- **PROJECT PULSE** — One sentence on status + next action for each active project
- **WEEKLY FOCUS** — If Monday: identify most important thing to accomplish this week

Output: `GENERATED/briefings/[DATE]-morning.md` (under 300 words)

### Workflow 2: Capture Processor (8PM daily)
Claude processes everything in 00-CAPTURE:
- Identifies each item type: TASK, IDEA, REFERENCE, NOTE, or EVENT
- Files it in the correct location
- Extracts action items, assigns dates, adds to project task lists
- Moves originals to ARCHIVE after processing

Output: Everything in 00-CAPTURE is filed by morning. Nothing accumulates.

### Workflow 3: Weekly Review (Sunday 7PM)
Claude reads the past week's daily notes, project notes, and CLAUDE.md to generate:
- **WHAT MOVED FORWARD** — Specific wins and what caused each
- **WHAT DID NOT MOVE** — Honest assessment of what stalled and why
- **THE WEEK'S PATTERN** — One theme or insight that appeared repeatedly
- **NEXT WEEK'S PRIORITIES** — Three ranked by impact with specific next actions
- **ONE DECISION** — Most important open decision, what it costs to delay

Output: `GENERATED/summaries/[DATE]-weekly-review.md`

### Workflow 4: Queue Processor (every 2 hours)
Claude checks 05-QUEUE for task files dropped throughout the week. Processes each and deposits outputs in GENERATED.

### Workflow 5: Project Health Monitor (Monday 9AM)
Claude reads all active project files and generates:
- Status of each project
- Flagged risks or stalls
- Suggested next actions

Output: `GENERATED/summaries/[DATE]-project-health.md`

## Anti-Breakdown Mechanisms

### The Capture Safety Net
Everything goes to 00-CAPTURE first. No decision required at capture time. The processor handles filing in the evening automatically. The system survives your busiest days intact.

### The Archive Never-Delete Rule
Nothing is ever deleted. Completed → ARCHIVE. Outdated → ARCHIVE. Processed → ARCHIVE. The system never loses information. The cost of keeping everything is zero.

### The CLAUDE.md Single Source of Truth
Everything Claude needs to operate exists in one file. When priorities change, update CLAUDE.md and every subsequent workflow reflects the new reality automatically. No cascading updates needed.

## Build Order

| When | What | Time |
|------|------|------|
| Saturday Morning | Storage Layer — Create 8 folders, set up CLAUDE.md | 2 hours |
| Saturday Afternoon | Intelligence Layer — Install Claude Desktop, configure Filesystem MCP | 1 hour |
| Saturday Evening | First Queue Task — Drop one real task, verify output | 30 min |
| Sunday Morning | Automation Layer — Set up N8N, build morning briefing workflow | 2 hours |
| Sunday Afternoon | Remaining Workflows — Add capture processor, weekly review, queue processor | 2 hours |

**By Sunday evening:** All five workflows running. The operating system is operational.

## What This Enables

Three months after building this system, you notice something: **the system has been running the entire time**. Not because you maintained it perfectly. There were weeks you barely touched Obsidian. There were days captures piled up for three days. But the morning briefing kept running. The captures kept getting processed. The system kept operating whether you were operating it or not.

**The difference between a tool and an operating system:**
- A tool requires you to use it
- An operating system runs

## Relationship to Our KB

This architecture maps closely to our own setup:

| Obsidian OS | Our Equivalent |
|-------------|----------------|
| CLAUDE.md | [[soul-md-identity-layer]] (SOUL.md + MEMORY.md) |
| 3-layer architecture | [[hermes-three-tier-memory]] |
| 8-folder vault structure | Our KB folder structure (concepts/, engineering/, etc.) |
| Queue processor | [[hermes-kanban]] (Kanban board + delegate_task) |
| Anti-breakdown design | Our heartbeat system / durability focus |
| Generated outputs | KB log.md and changelog.md (auto-generated after updates) |

The key parallel: both systems are designed to **survive bad days** — periods of low energy or engagement where manual maintenance falls away but the system keeps producing.

## Related Concepts

- [[soul-md-identity-layer]] — SOUL.md as the identity layer in our system; the CLAUDE.md template here is the same pattern
- [[hermes-three-tier-memory]] — Hermes's three distinct memory layers parallel the 3-layer architecture
- [[llm-wiki-pattern]] — Both systems use LLM-maintained structured storage as a core mechanism
- [[self-evolving-skills]] — Agent-authored playbooks with background curation; parallels to how the CLAUDE.md and workflows evolve over time
- [[hermes-kanban]] — Our multi-agent task board; maps to the queue processor workflow pattern

## Sources

- [CyrilXBT (@cyrilXBT) on X/Twitter](https://x.com/cyrilxbt/status/2056924424838815824) — Original thread on building the Obsidian Personal OS