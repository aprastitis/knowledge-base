---
type: card
status: active
date: 2026-06-12
summary: Core agent loop, CLI architecture, TUI, project structure, toolsets, delegation, profiles
tags: [hermes, architecture, agent-loop, cli, toolsets, delegation, profiles]
sources:
  - https://github.com/NousResearch/hermes-agent/blob/main/AGENTS.md
related:
  - engineering/hermes/README
  - engineering/hermes/plugins
  - engineering/hermes/skills
  - engineering/hermes-kanban
  - engineering/hermes/2026-08-03-hermes-v0-20-herald-release
  - engineering/hermes/2026-08-10-hermes-browser-use-mode
---

# Hermes Architecture

## Project Structure

```
hermes-agent/
├── run_agent.py          # AIAgent class — core conversation loop (~12k LOC)
├── model_tools.py        # Tool orchestration, discover_builtin_tools(), handle_function_call()
├── toolsets.py           # Toolset definitions, _HERMES_CORE_TOOLS list
├── cli.py                # HermesCLI class — interactive CLI orchestrator (~11k LOC)
├── hermes_state.py       # SessionDB — SQLite session store (FTS5 search)
├── hermes_constants.py   # get_hermes_home(), display_hermes_home() — profile-aware paths
├── hermes_logging.py     # setup_logging() — agent.log / errors.log / gateway.log
├── batch_runner.py       # Parallel batch processing
├── agent/                # Agent internals (provider adapters, memory, caching, compression)
├── hermes_cli/           # CLI subcommands, setup wizard, plugins loader, skin engine
├── tools/                # Tool implementations — auto-discovered via tools/registry.py
│   └── environments/     # Terminal backends (local, docker, ssh, modal, daytona, singularity)
├── gateway/              # Messaging gateway — run.py + session.py + platforms/
│   └── platforms/        # Adapter per platform (telegram, discord, slack, whatsapp, etc.)
├── plugins/              # Plugin system
│   ├── memory/           # Memory-provider plugins (honcho, mem0, supermemory, ...)
│   ├── model-providers/  # Inference backend plugins
│   ├── kanban/           # Multi-agent board dispatcher + worker plugin
│   └── observability/    # Metrics / traces / logs plugin
├── skills/               # Built-in skills bundled with the repo
├── optional-skills/      # Heavier/niche skills shipped but NOT active by default
├── ui-tui/               # Ink (React) terminal UI — `hermes --tui`
├── tui_gateway/          # Python JSON-RPC backend for the TUI
├── cron/                 # Scheduler — jobs.py, scheduler.py
└── tests/                # Pytest suite (~17k tests across ~900 files)
```

**User config:** `~/.hermes/config.yaml` (settings), `~/.hermes/.env` (API keys only).
**Logs:** `~/.hermes/logs/` — `agent.log` (INFO+), `errors.log` (WARNING+), `gateway.log`.

## AIAgent Class

```python
class AIAgent:
    def __init__(self,
        base_url: str = None,
        api_key: str = None,
        provider: str = None,
        model: str = "",
        max_iterations: int = 90,
        enabled_toolsets: list = None,
        disabled_toolsets: list = None,
        platform: str = None,
        session_id: str = None,
        skip_context_files: bool = False,
        skip_memory: bool = False,
        # ... plus callbacks, thread/user/chat IDs, iteration_budget, fallback_model, etc.
    ): ...

    def chat(self, message: str) -> str:
        """Simple interface — returns final response string."""

    def run_conversation(self, user_message: str, system_message: str = None,
                         conversation_history: list = None, task_id: str = None) -> dict:
        """Full interface — returns dict with final_response + messages."""
```

## Agent Loop

The core loop is inside `run_conversation()` — entirely synchronous, with interrupt checks, budget tracking, and a one-turn grace call:

```python
while (api_call_count < self.max_iterations and self.iteration_budget.remaining > 0) \
        or self._budget_grace_call:
    if self._interrupt_requested: break
    response = client.chat.completions.create(model=model, messages=messages, tools=tool_schemas)
    if response.tool_calls:
        for tool_call in response.tool_calls:
            result = handle_function_call(tool_call.name, tool_call.args, task_id)
            messages.append(tool_result_message(result))
        api_call_count += 1
    else:
        return response.content
```

Messages follow OpenAI format: `{"role": "system/user/assistant/tool", ...}`.
Reasoning content is stored in `assistant_msg["reasoning"]`.

## CLI Architecture

- **Rich** for banner/panels, **prompt_toolkit** for input with autocomplete
- **KawaiiSpinner** (`agent/display.py`) — animated faces during API calls, `┊` activity feed for tool results
- **Skin engine** (`hermes_cli/skin_engine.py`) — data-driven CLI theming; skins customize banner colors, spinner faces/verbs/wings, tool prefix, response box, branding text
- `process_command()` dispatches on canonical command name resolved via `resolve_command()` from the central registry

### Slash Command Registry

All slash commands are defined in `COMMAND_REGISTRY` list of `CommandDef` objects. Every downstream consumer derives from this registry automatically: CLI, Gateway, Telegram menu, Slack mapping, autocomplete, CLI help.

Adding a command:
1. Add `CommandDef` entry to `COMMAND_REGISTRY` in `hermes_cli/commands.py`
2. Add handler in `HermesCLI.process_command()` in `cli.py`
3. If available in gateway, add handler in `gateway/run.py`

## TUI Architecture (ui-tui + tui_gateway)

The TUI is a full replacement for the classic CLI, activated via `hermes --tui` or `HERMES_TUI=1`.

```
hermes --tui
  └─ Node (Ink)  ──stdio JSON-RPC──  Python (tui_gateway)
       │                                  └─ AIAgent + tools + sessions
       └─ renders transcript, composer, prompts, activity
```

### TUI in the Dashboard

The dashboard embeds the real `hermes --tui` — **not** a rewrite. See `hermes_cli/pty_bridge.py` + the `@app.websocket("/api/pty")` endpoint in `hermes_cli/web_server.py`.

- Browser loads `web/src/pages/ChatPage.tsx`, which mounts xterm.js's `Terminal` with the WebGL renderer
- `/api/pty?token=…` upgrades to a WebSocket
- The server spawns whatever `hermes --tui` would spawn, through `ptyprocess`
- Frames: raw PTY bytes each direction; resize via `\x1b[RESIZE:<cols>;<rows>]` intercepted on the server

## Toolsets

All toolsets defined in `toolsets.py` as a single `TOOLSETS` dict. Each platform's adapter picks a base toolset; `_HERMES_CORE_TOOLS` is the default bundle most platforms inherit from.

Current toolset keys: `browser`, `clarify`, `code_execution`, `cronjob`, `debugging`, `delegation`, `discord`, `file`, `homeassistant`, `image_gen`, `kanban`, `memory`, `messaging`, `moa`, `safe`, `search`, `session_search`, `skills`, `terminal`, `todo`, `tts`, `video`, `vision`, `web`, `yuanbao`.

Enable/disable per platform via `hermes tools` or `tools.<platform>.enabled/disabled` in config.yaml.

## Delegation (delegate_task)

`tools/delegate_tool.py` spawns a subagent with an isolated context + terminal session. **Asynchronous as of June 2026** — parent continues immediately while the subagent runs in the background; results return via background completion events. See [[engineering/async-subagents]] for the change. Earlier KB versions described `delegate_task` as synchronous; that framing is superseded.

Two shapes:
- **Single:** pass `goal` (+ optional `context`, `toolsets`)
- **Batch (parallel):** pass `tasks: [...]` — each gets its own subagent, capped by `delegation.max_concurrent_children` (default 3)

Roles:
- `role="leaf"` (default) — focused worker. Cannot call `delegate_task`, `clarify`, `memory`, `send_message`, `execute_code`
- `role="orchestrator"` — retains `delegate_task` for spawning workers. Gated by `delegation.orchestrator_enabled` (default true) and bounded by `delegation.max_spawn_depth` (default 2)

**Durability rule:** Subagents run in the foreground of the gateway but **don't block the parent's chat context**. For long-running work that must outlive the current session entirely (e.g., across gateway restarts), use `cronjob` ([[engineering/hermes/cronjob]]) or `terminal(background=True, notify_on_complete=True)`. For cross-agent durable work that humans and other agents need to see, use Kanban ([[engineering/hermes-kanban]]).

## Profiles: Multi-Instance Support

Hermes supports **profiles** — multiple fully isolated instances, each with its own `HERMES_HOME` directory.

Core mechanism: `_apply_profile_override()` sets `HERMES_HOME` before any module imports. All `get_hermes_home()` references automatically scope to the active profile.

### Profile-safe code rules

1. **Use `get_hermes_home()` for all HERMES_HOME paths.** NEVER hardcode `~/.hermes`
2. **Use `display_hermes_home()` for user-facing messages**
3. **Module-level constants are fine** — they cache `get_hermes_home()` at import time (after `_apply_profile_override()`)
4. **Tests that mock `Path.home()` must also set `HERMES_HOME`**
5. **Gateway platform adapters should use token locks** — call `acquire_scoped_lock()` / `release_scoped_lock()` in connect/disconnect

### Profile operations are HOME-anchored

`_get_profiles_root()` returns `Path.home() / ".hermes" / "profiles"`, NOT `get_hermes_home() / "profiles"`. This lets `hermes -p coder profile list` see all profiles regardless of which one is active.

## Known Pitfalls

- **DO NOT hardcode `~/.hermes` paths** — use `get_hermes_home()`
- **DO NOT introduce new `simple_term_menu` usage** — use `hermes_cli/curses_ui.py`
- **DO NOT use `\033[K` (ANSI erase-to-EOL) in spinner/display code** — use space-padding
- **`_last_resolved_tool_names` is process-global** in `model_tools.py` — saved/restored around subagent execution
- **The gateway has TWO message guards** — both must bypass approval/control commands
- **Squash merges from stale branches can silently revert recent fixes** — verify with `git diff HEAD~1..HEAD`
## See Also

- [[engineering/async-subagents]] — The June 2026 change that made `delegate_task` non-blocking (supersedes the synchronous framing in earlier KB versions)
- [[engineering/hermes/cronjob]] — The `cronjob` tool (referenced in the durability rule above)
- [[engineering/hermes/messaging]] — The gateway platform adapters and overall architecture
- [[engineering/hermes/profiles]] — Profile-based multi-instance isolation (referenced in the gateway token-lock rule)
- [[engineering/hermes/2026-08-03-hermes-v0-20-herald-release]] — v0.20 overhauled compression (proactive tool-result pruning, per-turn micro-compaction, guaranteed tail, ghost-skill defense) and raised the default tool-calling iteration limit 90 → 500; the agent loop shape is unchanged but the bounds are much wider
- [[engineering/hermes/2026-08-10-hermes-browser-use-mode]] — adds the "toolset key resolves to a swappable backend" layer; this card documents `browser` as a toolset key, Browser Use mode is the pluggable-backend extension of that contract
