---
type: card
status: active
date: 2026-06-12
summary: Durable SQLite multi-agent task board — CLI, kanban_* toolsets, dispatcher, isolation model, rate limit trap
tags: [hermes, kanban, multi-agent, task-board, dispatcher]
sources:
  - https://github.com/NousResearch/hermes-agent/blob/main/AGENTS.md
  - https://github.com/NousResearch/hermes-agent/blob/main/docs/kanban/multi-gateway.md
related:
  - engineering/hermes/architecture
  - engineering/hermes/profiles
  - engineering/multi-agent-orchestration-patterns
---

# Hermes Kanban — Multi-Agent Work Queue

Durable SQLite-backed board that lets multiple profiles / workers collaborate on shared tasks.

## CLI Commands

`hermes kanban <verb>` where verbs are:
- `init` — Initialize a board
- `create` — Create a task
- `list` / `ls` — List tasks
- `show` — Show task details
- `assign` — Assign task
- `link` / `unlink` — Link/unlink tasks
- `comment` — Add comment
- `complete` — Mark complete
- `block` / `unblock` — Block/unblock task
- `archive` — Archive task
- `tail` — Watch new tasks
- `watch` — Watch task changes
- `stats` — Board statistics
- `runs` — Task run history
- `log` — Activity log
- `assignees` — List assignees
- `heartbeat` — Send worker heartbeat
- `notify-*` — Notification settings
- `dispatch` — Run dispatcher manually
- `daemon` — Run dispatcher as daemon
- `gc` — Garbage collect stale tasks

## Worker/Orchestrator Toolset

`tools/kanban_tools.py` exposes: `kanban_show`, `kanban_complete`, `kanban_block`, `kanban_heartbeat`, `kanban_comment`, `kanban_create`, `kanban_link`

Profiles that explicitly enable the `kanban` toolset outside a dispatcher-spawned task also get `kanban_list` and `kanban_unblock` for board routing.

**Zero schema footprint** when workers aren't inside a kanban task.

## Dispatcher

Long-lived loop that (default every 60s):
1. Reclaims stale claims
2. Promotes ready tasks
3. Atomically claims tasks
4. Spawns assigned profiles

Runs **inside the gateway** by default via `kanban.dispatch_in_gateway: true`.

**Plugin assets:** `plugins/kanban/dashboard/` (web UI) + `plugins/kanban/systemd/` (`hermes-kanban-dispatcher.service` for standalone dispatcher deployment).

## Isolation Model

- **Board** is the hard boundary — workers spawned with `HERMES_KANBAN_BOARD` pinned in their env so they can't see other boards
- **Tenant** is a soft namespace *within* a board — one specialist fleet can serve multiple businesses with workspace-path + memory-key isolation

## Kanban Rate Limit Trap (CRITICAL)

**Symptom:** API usage spikes to quota limit in under an hour despite no user activity.

**Root Cause:** When workers hit a rate limit (HTTP 429), they crash and the dispatcher re-schedules within 60s. With `failure_limit=2`, each task needs 2 consecutive crashes before blocking. If rate limit persists, this creates an infinite crash-loop.

**Fix:**
- `failure_limit: 1` in config.yaml — tasks block after the FIRST crash
- Archive all stuck READY and RUNNING tasks before restart

**Before re-enabling dispatcher:**
1. Verify no READY/RUNNING tasks: `hermes kanban ls --status ready` and `hermes kanban ls --status running`
2. If tasks exist, archive them: `hermes kanban archive <task-id>`
3. Check API quota status

## Multi-Gateway Dispatch Configuration

Only one gateway owns the dispatcher:
```yaml
# Dispatch-owning gateway (default profile)
kanban:
  dispatch_in_gateway: true   # default

# Non-dispatch gateways (other profiles)
kanban:
  dispatch_in_gateway: false
```

Or via env var: `HERMES_KANBAN_DISPATCH_IN_GATEWAY=false`