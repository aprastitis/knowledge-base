---
type: engineering
status: active
date: 2026-07-31
summary: First official Hermes Desktop plugin (Kanban) establishes a UI-shell extension surface with its own page, sidebar, hotkeys, status bar, backend endpoints, and SDK. Architecturally distinct from the general plugin system in `engineering/hermes/plugins`.
tags: [hermes, desktop, plugins, kanban, architecture, extensibility, ui-shell]
sources:
  - https://x.com/NousResearch/status/2083257053338898730
related:
  - engineering/hermes/plugins
  - engineering/hermes-kanban
  - engineering/hermes/architecture
  - engineering/hermes/2026-08-02-hermes-v0-19-quicksilver-release
  - engineering/hermes/2026-07-26-hermes-v0-18-judgment-release
---

# Hermes Desktop Plugin Architecture — Kanban First

The first official Hermes **Desktop** plugin is Kanban, announced 2026-07-31 as "desktop native by popular request and significantly upgraded." Beyond shipping the upgraded Kanban UX, the announcement establishes a new extension surface: a **Desktop UI-shell plugin** model that is architecturally distinct from the general plugin system documented in [[engineering/hermes/plugins]].

The tweet (NousResearch, 2,570 likes / 179 reposts / 108 replies) explicitly enumerates the new capabilities a Desktop plugin can claim. None of these map onto the existing general-plugin surface (which is `register(ctx)` for tools, CLI commands, and lifecycle hooks). They are UI shell extensions.

## What a Desktop Plugin Can Do

Per the announcement, a plugin can add:

- **Its own page** in the desktop UI — a top-level route/screen the user navigates to
- **Sidebar row** — a persistent entry in the desktop sidebar
- **Hotkeys** — keybindings that trigger plugin actions from anywhere in the desktop shell
- **Status bar actions** — small UI elements in the desktop status bar
- **Backend endpoints** — HTTP routes the plugin exposes for the desktop front-end (or for external callers) to talk to
- **SDK** — a published SDK for writing or importing plugins

The combination is what makes this a *shell extension* model rather than a *backend plugin* model. The general plugin system (memory providers, model providers, context engines, image-gen, observer/middleware hooks) extends the agent's runtime. Desktop plugins extend the **user's surface** — the window they actually look at.

## What It Is Not

- **Not a general Hermes plugin.** A Desktop plugin is a separate kind of artifact. Code that targets the general plugin system ([[engineering/hermes/plugins]]) does not get a sidebar row; code that targets the Desktop SDK does not get `kanban_*` tools.
- **Not a skill.** Skills ([[engineering/hermes/skills]]) are LLM-loaded procedural playbooks. Desktop plugins are installed UI components. A skill is a prompt; a plugin is a program.
- **Not a memory provider / model provider / context engine.** Those are all the four sub-kinds of the general plugin system, and they all extend the agent runtime, not the user surface.
- **Not a CLI subcommand.** Desktop plugins expose backend HTTP endpoints, not `hermes <plugin>` subcommands (those go through `ctx.register_cli_command(...)` in the general surface).

## Why Kanban First

Kanban was the natural first Desktop plugin candidate because it already had a non-trivial surface on three other paths (CLI, `/kanban` slash command, `kanban_*` toolsets) and the dashboard had a Kanban tab before any of them. The Desktop plugin surface is essentially the *fourth* front door to the same `kanban.db` — but with a richer UI affordance (a dedicated page, sidebar, hotkeys, status bar) than the dashboard's embedded tab can offer.

The underlying engine is still the same durable SQLite-backed multi-agent task board documented in [[engineering/hermes-kanban]]. The Desktop plugin is a presentation layer over the same `kanban_db` — *not* a parallel kanban system. The "significantly upgraded" framing is about the Desktop surface (hotkeys, sidebar, status bar, backend endpoints) and the UX that comes with them, not about the underlying board.

## SDK Implications (What to Watch For)

The published SDK for writing or importing plugins is the operational signal. Three things to track when docs surface:

1. **Plugin manifest shape** — likely a JSON/YAML file declaring the page, sidebar entry, hotkeys, status-bar actions, and backend routes. The "import one" framing implies a package format (analogous to a pip-installable general plugin, but Desktop-shaped).
2. **Permission boundary** — the general plugin system forbids modifying core files (`run_agent.py`, `cli.py`, etc.) and routes extension through the generic surface. The Desktop plugin system presumably has an analogous rule: plugins extend the shell, they do not fork it.
3. **Lifecycle hooks** — the general plugin system exposes `pre_tool_call` / `post_tool_call` / `pre_llm_call` / `post_llm_call` / `on_session_start` / `on_session_end`. A Desktop plugin's equivalent is presumably page-mount / page-unmount / hotkey / status-bar-click — actions that make sense in a UI shell, not an agent loop.

These are inferred from the general plugin system's pattern + the announced capabilities; not yet confirmed by docs. Update the card if/when the SDK repo and docs land.

## Relationship to v0.18 / v0.19

- **v0.18 "Judgment"** ([[engineering/hermes/2026-07-26-hermes-v0-18-judgment-release]]) added the desktop *coding Projects* surface — a place to attach a codebase to a session. The Desktop plugin system is the next layer up: not "give the desktop a project to work on" but "let the desktop grow new screens and affordances."
- **v0.19 "Quicksilver"** ([[engineering/hermes/2026-08-02-hermes-v0-19-quicksilver-release]]) polished desktop and TUI render paths. The Quicksilver speed cuts (~4.3s → ~0.9s cold start) make the desktop shell feel instant — which is the precondition for the Desktop plugin surface being pleasant to use. Hotkey-driven plugins only feel right if the shell responds immediately.

The natural reading: v0.18 introduced the desktop as a *host for code work*, v0.19 made the host *feel fast*, and the Kanban Desktop plugin is the first *third-party content* for that host. The shell is no longer just a wrapper around the agent loop — it is becoming a platform.

## Why It Matters

A Desktop plugin system is the moment a desktop product stops being "an app" and starts being "a platform." Before this, the desktop had a fixed surface (Projects, terminal, kanban tab, settings). After this, that surface is extensible by the team and (eventually) by users. The team ships Kanban first because it is the surface that benefits most from page/hotkey/sidebar affordances — but the SDK is the actual product here. Kanban is the first tenant of a Desktop plugin ecosystem, not the only one.

Practically: the first thing to watch after the SDK repo drops is whether the surface is *open* (anyone can ship a plugin) or *gated* (only Nous-team plugins). The "import one" phrasing in the announcement hints at the former; the "official" framing in the headline hints at the latter for now. Both shapes are reasonable; the KB posture is to track whichever lands.

## Related

- [[engineering/hermes/plugins]] — the **general** plugin system (memory providers, model providers, context engines, image-gen, hooks). Desktop plugins are a *different* surface; do not conflate.
- [[engineering/hermes-kanban]] — the underlying SQLite task board that the Desktop Kanban plugin is a presentation layer over. The four front doors (CLI, `/kanban`, `kanban_*` tools, Desktop plugin) all share the same `kanban_db`.
- [[engineering/hermes/architecture]] — the agent loop, TUI, and CLI architecture that the desktop shell extends.
- [[engineering/hermes/2026-08-02-hermes-v0-19-quicksilver-release]] — Quicksilver release; the speed cuts make the desktop shell feel instant, which is the precondition for hotkey-driven plugins to feel right.
- [[engineering/hermes/2026-07-26-hermes-v0-18-judgment-release]] — Judgment release; introduced the desktop coding Projects surface that the plugin system now extends.
