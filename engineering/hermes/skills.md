---
type: card
status: active
date: 2026-06-12
summary: SKILL.md format, authoring standards, platform gating, conditional activation, setup metadata, curator system
tags: [hermes, skills, SKILL.md, authoring-standards]
sources:
  - https://github.com/NousResearch/hermes-agent/blob/main/AGENTS.md
  - https://github.com/NousResearch/hermes-agent/blob/main/CONTRIBUTING.md
related:
  - engineering/hermes/README
  - engineering/hermes/architecture
  - concepts/self-evolving-skills
---

# Hermes Skills System

## Two Skill Surfaces

- **`skills/`** — built-in skills shipped and loadable by default. Organized by category (research/, productivity/, etc.)
- **`optional-skills/`** — heavier or niche skills shipped with the repo but NOT active by default. Installed explicitly via `hermes skills install official/<category>/<skill>`

## SKILL.md Format

```markdown
---
name: my-skill
description: Brief description (≤60 chars, one sentence, ends with period)
version: 1.0.0
author: Your Name
license: MIT
platforms: [macos, linux]          # Optional — omit for all platforms
metadata:
  hermes:
    tags: [Category, Subcategory, Keywords]
    related_skills: [other-skill-name]
    fallback_for_toolsets: [web]       # Optional — show only when toolset unavailable
    requires_toolsets: [terminal]      # Optional — show only when toolset available
required_environment_variables:
  - name: MY_API_KEY
    prompt: API key
    help: https://...
    required_for: full functionality
---

# Skill Title

Brief intro (2-3 sentences, what it does and doesn't do).

## When to Use
Trigger conditions — when should the agent load this skill?

## Prerequisites
Env vars, install steps, MCP setup.

## How to Run
Canonical invocation through the `terminal` tool.

## Quick Reference
Flat command/API reference.

## Procedure
Numbered steps with copy-paste commands.

## Pitfalls
Known failure modes and how to handle them.

## Verification
Single command that proves the skill works.
```

## Skill Authoring Standards (HARDLINE)

Every new or modernized skill must meet these standards before merge:

1. **`description` ≤ 60 characters, one sentence, ends with a period.** No marketing words ("powerful", "comprehensive"). Don't repeat the skill name.
2. **Tools referenced in SKILL.md prose must be native Hermes tools or MCP servers.** `terminal`, `web_extract`, `read_file`, `patch`, `search_files`, `vision_analyze`, `delegate_task`, etc. Don't name shell utilities — `grep` → `search_files`, `cat` → `read_file`.
3. **`platforms:` gating audited against actual script imports.** Skills using POSIX-only primitives (`fcntl`, `termios`, `/proc`, `osascript`, `apt`, `systemctl`) must declare supported platforms. Default posture: fix cross-platform first.
4. **`author` credits the human contributor first.** Replace "Hermes Agent" with the actual human name.
5. **SKILL.md body uses the modern section order:** `# <Skill> Skill` → `## When to Use` → `## Prerequisites` → `## How to Run` → `## Quick Reference` → `## Procedure` → `## Pitfalls` → `## Verification`. Target ~200 lines for complex, ~100 for simple.
6. **Scripts go in `scripts/`, references in `references/`, templates in `templates/`.** Don't expect the model to inline-write parsers every call.
7. **Tests live at `tests/skills/test_<skill>_skill.py`** — stdlib + pytest + `unittest.mock`, no live network calls.
8. **`.env.example` additions are isolated to a clearly delimited block.**

## Platform Gating

Skills can declare which OS platforms they support via the `platforms` frontmatter field:
```yaml
platforms: [macos]            # macOS only
platforms: [macos, linux]     # macOS and Linux
platforms: [windows]          # Windows only
```

Skills with this field are automatically hidden from system prompt, `skills_list()`, and slash commands on incompatible platforms.

## Conditional Skill Activation

Four fields control when skills appear based on available tools/toolsets:

```yaml
metadata:
  hermes:
    fallback_for_toolsets: [web]      # Show ONLY when these toolsets are unavailable
    requires_toolsets: [terminal]     # Show ONLY when these toolsets are available
    fallback_for_tools: [web_search]  # Show ONLY when these specific tools are unavailable
    requires_tools: [terminal]        # Show ONLY when these specific tools are available
```

**Semantics:**
- `fallback_for_*`: The skill is a backup — **hidden** when listed tools/toolsets are available, **shown** when unavailable
- `requires_*`: The skill needs certain tools — **hidden** when listed tools/toolsets are unavailable

Filtering happens at prompt build time in `agent/prompt_builder.py`.

## Skill Setup Metadata

Skills can declare secure setup-on-load metadata:
```yaml
required_environment_variables:
  - name: TENOR_API_KEY
    prompt: Tenor API key
    help: https://developers.google.com/tenor
    required_for: full functionality
```

Missing values trigger a CLI-only secure prompt when the skill is loaded. Gateway/messaging sessions instruct the user to run `hermes setup` locally.

## Curator (Skill Lifecycle)

Background skill-maintenance system that tracks usage on agent-created skills and auto-archives stale ones. Archives go to `~/.hermes/skills/.archive/` and are restorable.

**Core:** `agent/curator.py` (review loop, auto-transitions, LLM review prompt) + `agent/curator_backup.py` (pre-run tar.gz snapshots).

**CLI:** `hermes curator <verb>` where verbs are: `status`, `run`, `pause`, `resume`, `pin`, `unpin`, `archive`, `restore`, `prune`, `backup`, `rollback`.

**Invariants:**
- Curator only touches skills with `created_by: "agent"` — bundled + hub-installed skills are off-limits
- Never deletes; max destructive action is archive
- Pinned skills exempt from every auto-transition and LLM review pass

**Config:** `curator: enabled, interval_hours, min_idle_hours, stale_after_days, archive_after_days, backup.*`

## Should It Be a Skill or a Tool?

**Make it a Skill when:**
- Capability can be expressed as instructions + shell commands + existing tools
- Wraps an external CLI or API the agent can call via `terminal` or `web_extract`
- Doesn't need custom Python integration or API key management baked into the agent

**Make it a Tool when:**
- Requires end-to-end integration with API keys, auth flows, or multi-component configuration
- Needs custom processing logic that must execute precisely every time
- Handles binary data, streaming, or real-time events that can't go through the terminal

## Bundled vs Optional vs Hub

| Type | Location | Activation |
|------|----------|------------|
| Bundled | `skills/` | Always available |
| Optional | `optional-skills/` | `hermes skills install official/<cat>/<skill>` |
| Hub | Nous Research Skills Hub | `hermes skills install <hub-id>` |

## See Also

- [[engineering/hermes/2026-06-20-hermes-blender-mcp-skill]] — concrete example of a creative optional skill that bridges to a desktop GUI app (Blender) over MCP/TCP. Useful as a reference for desktop-automation skills. See [[engineering/hermes/2026-07-23-tldraw-offline-skill]] for the same pattern applied to tldraw's offline desktop whiteboard (added in Hermes v0.19).