---
type: engineering
status: active
date: 2026-07-01
summary: `hermes setup` gained a third mode (Blank Slate) on 2026-06-20 — gives a minimal-baseline install (provider + model + file ops + terminal only), in contrast to Quick (default-everything) and Full (explicit-everything).
tags: [hermes, setup, blank-slate, install, onboarding]
related:
  - engineering/hermes/README
  - engineering/hermes/skills
  - engineering/hermes-native-mcp-catalog
  - engineering/hermes/architecture
sources:
  - https://x.com/NousResearch/status/2068405008685539514
  - https://x.com/NousResearch/status/2068405010489102353
---

# Hermes Blank Slate Setup Mode

> Note: date-stamped filename uses 2026-07-01 (today's ingest); the underlying announcement was 2026-06-20 UTC, scanned 2026-06-21 Athens time.

## What It Is

`hermes setup` now offers three modes instead of two:

| Mode | What it does | When to use |
|------|--------------|-------------|
| **Quick** (default) | Reasonable defaults; ships with Nous's bundled skills/MCP catalog ready to go | New users who want "just works" |
| **Full** (`hermes setup --portal`) | OAuth-driven; Nous as provider; Tool Gateway (web search, image gen, TTS, browser) auto-enabled | Users on Nous Portal |
| **Blank Slate** (new) | Minimal baseline: provider + model + file ops + terminal only. No bundled skills, no MCP catalog, no extras. Everything else added manually. | Power users / minimal-surface installs / users who want to understand what each piece does |

Before this change, [[engineering/hermes/README]] documented only `hermes setup` and `hermes setup --portal` — the README needs a three-line addition to capture the new mode.

## Why It Exists

Hermes ships with a lot of capability: bundled skills (research, productivity, media, smart-home, etc.), an MCP server catalog, multiple messaging platform adapters, cron, kanban, voice mode. Most of this is opt-in / hidden unless you actively trigger it, but it's still **in the install** — meaning a `hermes setup` Quick install is meaningfully larger on disk, has a larger system-prompt surface area, and has more places that can bite a new user debugging a fresh install.

Blank Slate gives you the minimal agent — provider + model + `terminal` + `file` + `read_file` + `write_file` + `patch` — and lets you opt-in to everything else. Useful when:

- You want to verify that a problem in Hermes isn't caused by a default-loaded skill or MCP server
- You're building a deploy / minimal-footprint image
- You want to understand the agent from the inside out
- You're packing Hermes into a constrained environment (small container, etc.)

## How to Use It

From the announcement tweet (2026-06-21 scan):

> "hermes setup" now offers a third option — Blank Slate.

CLI invocation: `hermes setup` and select "Blank Slate" from the menu, or follow the quickstart guide linked in the announcement tweet. (The exact non-interactive flag wasn't in the 06-21 scan — flag this for follow-up if the docs page doesn't surface it.)

After install, the agent is fully functional for the basics. Skills, MCP catalog, cron, kanban, messaging adapters, etc. can all be added explicitly:

```bash
hermes skills install official/<category>/<skill>      # add a skill
hermes mcp add <server-name>                            # add an MCP server
hermes gateway setup                                    # add a messaging platform
```

## See Also

- [[engineering/hermes/README]] — Documents the older two-mode structure (`hermes setup`, `hermes setup --portal`); needs a small update to cover Blank Slate
- [[engineering/hermes/skills]] — Skills vs tools decision framework; relevant if you're wondering what to add first after Blank Slate
- [[engineering/hermes-native-mcp-catalog]] — The MCP catalog that Blank Slate skips by default

## Source Notes

- Announcement: Nous tweet 2026-06-20 UTC (18:45 Athens) — https://x.com/NousResearch/status/2068405008685539514
- Quickstart guide link: https://x.com/NousResearch/status/2068405010489102353
- KB implication: [[engineering/hermes/README]] should be updated to add a row for the Blank Slate mode
