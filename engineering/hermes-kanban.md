# Hermes Kanban — Multi-Agent Task Board

## Type
Engineering / Agent Collaboration

## Summary
Hermes Kanban is a durable SQLite-backed task board that lets multiple named agent profiles collaborate on work across agent boundaries. Tasks survive restarts, support human-in-the-loop, and are driven by agents through `kanban_*` tool calls rather than CLI commands.

## The Core Distinction: Kanban vs delegate_task

| | delegate_task | Kanban |
|---|---|---|
| Shape | RPC call (fork → join) | Durable message queue + state machine |
| Parent | Blocks until child returns | Fire-and-forget after create |
| Child identity | Anonymous subagent | Named profile with persistent memory |
| Resumability | None — failed = failed | Block → unblock → re-run; crash → reclaim |
| Human in loop | Not supported | Comment / unblock at any point |
| Agents per task | One call = one subagent | N agents over task's life |
| Audit trail | Lost on context compression | Durable rows in SQLite forever |
| Coordination | Hierarchical (caller → callee) | Peer — any profile reads/writes any task |

**One-liner:** `delegate_task` is a function call; Kanban is a work queue where every handoff is a row any profile (or human) can see and edit.

Use `delegate_task` when the parent agent needs a short reasoning answer before continuing, no humans involved, result goes back into the parent's context.

Use Kanban when work crosses agent boundaries, needs to survive restarts, might need human input, might be picked up by a different role, or needs to be discoverable after the fact.

## Core Concepts

### Board
A standalone queue of tasks with its own SQLite DB, workspaces directory, and dispatcher loop. One install can have many boards (one per project/repo/domain). Single-project users stay on the `default` board and never see the word "board" outside this docs section.

### Task
A row with: `title`, optional `body`, one `assignee` (profile name), `status` (triage | todo | ready | running | blocked | done | archived), optional `tenant` namespace, optional `idempotency_key` for dedup on retried automation.

### Link
A `task_links` row recording a parent → child dependency. The dispatcher promotes `todo` → `ready` when all parents are done.

### Comment
The inter-agent protocol. Agents and humans append comments; when a worker is (re-)spawned it reads the full comment thread as part of its context.

### Workspace
The directory a worker operates in. Three kinds:
- **scratch** (default) — fresh tmp dir under `~/.hermes/kanban/workspaces/<board>/`
- **dir:** — an existing shared directory (must be absolute path; relative paths rejected at dispatch to prevent path traversal)
- **worktree** — a git worktree under `.worktrees/<task_id>/` for coding tasks

### Dispatcher
A long-lived loop that every N seconds (default 60): reclaims stale claims, reclaims crashed workers, promotes ready tasks, atomically claims, spawns assigned profiles. Runs inside the gateway by default (`kanban.dispatch_in_gateway: true`). After `kanban.failure_limit` consecutive spawn failures (default: 2), the dispatcher auto-blocks the task with the last error — prevents thrashing.

### Tenant
Optional string namespace within a board. One specialist fleet can serve multiple businesses with data isolation by workspace path and memory key prefix. Tenants are a soft filter; boards are the hard isolation boundary.

## Two Surfaces

The board has two front doors, both backed by the same `~/.hermes/kanban.db`:

1. **Agents** drive through `kanban_*` toolset: `kanban_show`, `kanban_list`, `kanban_complete`, `kanban_block`, `kanban_heartbeat`, `kanban_comment`, `kanban_create`, `kanban_link`, `kanban_unblock`. Workers call these like any other tool — they never shell out to `hermes kanban` CLI.

2. **Humans/scripts/cron** drive through `hermes kanban …` CLI, `/kanban` slash command, or the dashboard.

Both surfaces route through the same `kanban_db` layer — reads see a consistent view, writes can't drift.

### Why Tools Instead of CLI?

- **Backend portability** — Workers on remote backends (Docker/Modal/SSH) wouldn't have `hermes` installed or access to `~/.hermes/kanban.db`. The kanban tools run in the agent's own Python process.
- **No shell-quoting fragility** — Passing `--metadata '{"files": [...]}'` through shlex is a latent footgun.
- **Better errors** — Tool results are structured JSON the model can reason about, not stderr strings.

Zero schema footprint on normal sessions. The `check_fn` on each tool only returns `True` when `HERMES_KANBAN_TASK` is set — only true when the dispatcher spawned this process.

## Kanban Tool Reference

| Tool | Purpose | Required params |
|------|---------|----------------|
| `kanban_show` | Read current task (title, body, prior attempts, handoffs, comments, worker_context) | — |
| `kanban_list` | List task summaries with filters (assignee, status, tenant, archived, limit) | — |
| `kanban_complete` | Finish with summary + metadata structured handoff | `summary` or `result` |
| `kanban_block` | Escalate for human input with a reason | `reason` |
| `kanban_heartbeat` | Signal liveness during long operations | — |
| `kanban_comment` | Append a durable note to the task thread | `task_id`, `body` |
| `kanban_create` | (Orchestrators) fan out into child tasks | `title`, `assignee` |
| `kanban_link` | (Orchestrators) add a parent → child dependency edge | `parent_id`, `child_id` |
| `kanban_unblock` | (Orchestrators) move a blocked task back to ready | `task_id` |

## Recommended Handoff Metadata

For engineering and review tasks, prefer this shape:

```json
{
  "changed_files": ["path/to/file.py"],
  "verification": ["pytest tests/hermes_cli/test_kanban_db.py -q"],
  "dependencies": ["parent task id or external issue, if any"],
  "blocked_reason": null,
  "retry_notes": "what failed before, if this was a retry",
  "residual_risk": ["what was not tested or still needs human review"]
}
```

These keys are a convention, not a schema requirement. The goal is that every worker leaves enough evidence for the next reader to answer four questions quickly:
- What changed?
- How was it verified?
- What can unblock or retry this if it fails?
- What risk is still deliberately left open?

## Workloads Kanban Covers That delegate_task Can't

- **Research triage** — parallel researchers + analyst + writer, human-in-the-loop
- **Scheduled ops** — recurring daily briefs that build a journal over weeks
- **Digital twins** — persistent named assistants (inbox-triage, ops-review) that accumulate memory over time
- **Engineering pipelines** — decompose → implement in parallel worktrees → review → iterate → PR
- **Fleet work** — one specialist managing N subjects (50 social accounts, 12 monitored services)

## Worker Skill

Any profile that should work kanban tasks must load the `kanban-worker` skill (bundled, synced to all profiles during install/update). The skill teaches:
- On spawn, call `kanban_show()` to read title + body + parent handoffs + prior attempts + full comment thread
- `cd $HERMES_KANBAN_WORKSPACE` and do the work there
- Call `kanban_heartbeat(note="...")` every few minutes during long operations
- Complete with `kanban_complete(summary="...", metadata={...})`, or `kanban_block(reason="...")` if stuck

## Related Concepts

- [[hermes-three-tier-memory]] — Kanban persists state in SQLite; Hermes also has a three-tier memory architecture
- [[delegate_task]] — The alternative primitive; function call vs work queue
- [[kanban-worker-skill]] — The bundled skill that teaches agents how to interact with the board
- [[kanban-orchestrator-skill]] — Skill for orchestrator profiles that fan out work to child tasks

## Source

- https://hermes-agent.nousresearch.com/docs/user-guide/features/kanban