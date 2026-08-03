---
type: card
status: active
date: 2026-06-12
summary: General plugins, memory providers, model providers, context engines, image-gen — ABC + orchestrator pattern. Distinct from the Desktop plugin surface (page / sidebar / hotkeys / status bar / backend endpoints / SDK) introduced in `engineering/hermes/2026-07-31-hermes-desktop-kanban-plugin`.
tags: [hermes, plugins, architecture, extensibility]
sources:
  - https://github.com/NousResearch/hermes-agent/blob/main/AGENTS.md
related:
  - engineering/hermes/architecture
  - engineering/hermes/observer-hooks
  - engineering/hermes/middleware
  - engineering/hermes/2026-07-31-hermes-desktop-kanban-plugin
---

# Hermes Plugin System

Hermes has two plugin surfaces. Both live under `plugins/` in the repo so repo-shipped plugins can be discovered alongside user-installed ones in `~/.hermes/plugins/` and pip-installed entry points.

## General Plugins

`PluginManager` discovers plugins from `~/.hermes/plugins/`, `./.hermes/plugins/`, and pip entry points. Each plugin exposes a `register(ctx)` function that can:

- Register Python-callback lifecycle hooks: `pre_tool_call`, `post_tool_call`, `pre_llm_call`, `post_llm_call`, `on_session_start`, `on_session_end`
- Register new tools via `ctx.register_tool(...)`
- Register CLI subcommands via `ctx.register_cli_command(...)`

**Discovery timing pitfall:** `discover_plugins()` only runs as a side effect of importing `model_tools.py`. Code paths that read plugin state without importing `model_tools.py` first must call `discover_plugins()` explicitly (it's idempotent).

## Memory Provider Plugins

Separate discovery system for pluggable memory backends. Current built-in providers: **honcho, mem0, supermemory, byterover, hindsight, holographic, openviking, retaindb**.

Each provider implements the `MemoryProvider` ABC (see `agent/memory_provider.py`) and is orchestrated by `agent/memory_manager.py`. Lifecycle hooks include `sync_turn(turn_messages)`, `prefetch(query)`, `shutdown()`, and optional `post_setup(hermes_home, config)` for setup-wizard integration.

**CLI commands via `plugins/memory/<name>/cli.py`:** if a memory plugin defines `register_cli(subparser)`, `discover_plugin_cli_commands()` finds it at argparse setup time and wires it into `hermes <plugin>`. Only the **currently active** memory provider's CLI commands are exposed.

### No New In-Tree Memory Providers (Policy, May 2026)

The set of built-in memory providers under `plugins/memory/` is closed. New memory backends must ship as **standalone plugin repos** that users install into `~/.hermes/plugins/` — they implement the same `MemoryProvider` ABC, register through the same discovery path, and integrate via `hermes memory setup` without landing in this tree.

PRs that add a new directory under `plugins/memory/` will be closed with a pointer to publish the provider as its own repo. Existing in-tree providers stay; bug fixes are welcome.

## Model Provider Plugins

Every inference backend (openrouter, anthropic, gmi, deepseek, nvidia, …) ships as a plugin. Each plugin's `__init__.py` calls `providers.register_provider(ProviderProfile(...))` at module load.

Discovery is **lazy, separate** — scanned on first `get_provider_profile()` or `list_providers()` call, NOT by the general PluginManager.

**Scan order:**
1. Bundled: `<repo>/plugins/model-providers/<name>/`
2. User: `$HERMES_HOME/plugins/model-providers/<name>/`
3. Legacy: `<repo>/providers/<name>.py` (back-compat)

User plugins of the same name override bundled ones — `register_provider()` is last-writer-wins.

Full authoring guide: `website/docs/developer-guide/model-provider-plugin.md`.

## Model Provider Plugin Architecture

```
providers/__init__.py._discover_providers()
       │
       ├── bundles: <repo>/plugins/model-providers/<name>/
       ├── user: $HERMES_HOME/plugins/model-providers/<name>/
       └── legacy: <repo>/providers/<name>.py (back-compat)
```

The general PluginManager records `kind: model-provider` manifests but does NOT import them (would double-instantiate `ProviderProfile`). Plugins without an explicit `kind:` get auto-coerced via source-text heuristic.

### Vertex AI Provider (v0.18)

Hermes v0.18.0 added first-class Gemini access through Google Vertex AI. Unlike a static custom endpoint, the provider mints and refreshes short-lived OAuth2 access tokens from a GCP service-account JSON or Application Default Credentials. This prevents the mid-session expiry that occurs when a one-hour Vertex token is pasted as if it were a permanent API key. See [[engineering/hermes/2026-07-26-hermes-v0-18-judgment-release]].

## Context Engine / Image-Gen Plugins

`plugins/context_engine/`, `plugins/image_gen/`, etc. follow the same pattern (ABC + orchestrator + per-plugin directory):
- Context engines plug into `agent/context_engine.py`
- Image-gen providers into `agent/image_gen_provider.py`

Reference plugins (`example-dashboard`, `strike-freedom-cockpit`, `plugin-llm-example`) live in the [`hermes-example-plugins`](https://github.com/NousResearch/hermes-example-plugins) companion repo.

## Plugin Rules (Teknium, May 2026)

Plugins **MUST NOT** modify core files (`run_agent.py`, `cli.py`, `gateway/run.py`, `hermes_cli/main.py`, etc.). If a plugin needs a capability the framework doesn't expose, expand the generic plugin surface (new hook, new ctx method) — never hardcode plugin-specific logic into core.

PR #5295 removed 95 lines of hardcoded honcho argparse from `main.py` for exactly this reason.

## See Also: Desktop Plugin Surface (New, July 2026)

This card covers the **general plugin system** — extending the agent's runtime. In July 2026 Nous Research introduced a separate **Desktop plugin surface** for extending the desktop UI shell: a plugin can add its own page, sidebar row, hotkeys, status bar actions, and backend endpoints, with an SDK for writing or importing plugins. The first official Desktop plugin is Kanban.

The two surfaces do not overlap. The general plugin system gives you `register(ctx)` for tools, CLI commands, and lifecycle hooks; the Desktop plugin system gives you a page in the shell. Code targeting one does not automatically get the other's affordances.

See [[engineering/hermes/2026-07-31-hermes-desktop-kanban-plugin]] for the Desktop surface.
