---
type: concept
status: active
date: 2026-04-26
summary: Agent-authored procedural playbooks with background curation; agent writes its own SKILL.md files
tags: [agent-design, knowledge-management]
related: [[soul-md-identity-layer]], [[progressive-disclosure-pattern]]
sources: [internal]
---

# Self-Evolving Skills

## Type
Concept / Agent Self-Improvement Pattern

## Summary
Agents that create and maintain their own procedural playbooks (skills) rather than relying solely on pre-authored instructions. The agent encounters a problem → solves it through trial and error → saves the approach as a SKILL.md file → next time it loads the skill and follows the proven procedure instead of rediscovering it.

## How It Works in Hermes

Skills are Markdown files with YAML frontmatter — the agent's procedural memory.

```markdown
---
name: k8s-pod-debug
description: >
  Activate for crashing pods, CrashLoopBackOff,
  "why is my pod restarting", container failures.
version: 1.2.0
author: agent
platforms: [linux, macos]
---

## Procedure
1. Get pod status → check events → pull logs
2. Look for OOMKilled, ImagePullBackOff, config errors

## Pitfalls
- Forgetting --previous flag on restarted containers

## Verification
- Pod stays Running with 0 restarts for 5+ minutes
```

### When the Agent Creates a Skill
- Completes a complex task (5+ tool calls)
- Hits errors or dead ends and finds the working path
- User corrects its approach
- Discovers a non-trivial workflow worth preserving

### Skill Authoring Tool Actions
`skill_manage`: create, patch (preferred — token-efficient), edit (full rewrite), delete, write_file, remove_file.

### Progressive Disclosure (keeps token cost low)
- **Level 0:** Agent sees names + descriptions only (~3k tokens for full catalog)
- **Level 1:** Loads full skill content when actually needed
- **Level 2:** Drills into specific reference files within a skill

## The Curator: Garbage Collection

A background maintenance system that runs on inactivity check (not cron):
- Triggers after **7 days idle** + **2+ hours of agent idle**
- Runs a background fork of the agent with its own prompt cache, never touching the active conversation

### Curator Rules
- **30 days unused** → becomes stale
- **90 days unused** → archived (never auto-deleted)
- **Never touches** bundled or hub-installed skills — only agent-authored ones
- **Never auto-deletes** — worst outcome is archival to `~/.hermes/skills/.archive/`
- **Rollback** — tar.gz snapshot before every pass; one-command rollback, reversible

### Pinning
Critical skills can be pinned: `hermes curator pin <skill>` — protects from archival/deletion. Patches and edits to pinned skills still go through.

## Key Weakness (addressed by GEPA)

The in-agent learning loop has a known bias: **the agent tends toward self-congratulation** — it almost always thinks it performed well even when it didn't. This is why GEPA (offline optimization) exists as a separate layer.

## Related Concepts
- [[soul-md-identity-layer]] — Skills are always executed through the lens of SOUL.md's identity frame
- [[hermes-three-tier-memory]] — Skills sit on top of memory; memory stores facts, skills store procedures
- [[gepa-prompt-evolution]] — GEPA validates skills offline to address the self-congratulation weakness in the self-improvement loop
- [[personal-operating-system]] — CyrilXBT's CLAUDE.md-driven workflows are a human-scale parallel: the agent follows self-authored procedural playbooks that run autonomously

## Source
- https://x.com/akshay_pachaar/status/2054564519280804028 (Hermes Agent Masterclass by Akshay 🚀)