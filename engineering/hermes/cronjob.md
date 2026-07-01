---
type: card
status: active
date: 2026-07-01
summary: Hermes scheduled-task subsystem — gateway-driven 60s tick scheduler, single `cronjob` tool for create/list/pause/resume/edit/run/remove, skill attachments, multi-platform delivery, no-agent mode for watchdogs.
tags: [hermes, cron, scheduler, automation, watchdog, no-agent]
sources:
  - https://hermes-agent.nousresearch.com/docs/user-guide/features/cron
  - https://hermes-agent.nousresearch.com/docs/guides/cron-script-only
  - https://hermes-agent.nousresearch.com/docs/guides/cron-troubleshooting
  - https://hermes-agent.nousresearch.com/docs/guides/automate-with-cron
  - https://hermes-agent.nousresearch.com/docs/developer-guide/cron-internals
related:
  - engineering/hermes/README
  - engineering/hermes/architecture
  - engineering/hermes-kanban
  - engineering/loop-engineering
  - engineering/2026-06-19-engineering-agentic-loop-pattern
---

# Hermes Scheduled Tasks (Cron)

Hermes's cron subsystem is the operational primitive most automations ride on — every "wake me if X", "every Y do Z", "alert me on Telegram" pattern reduces to it. It is owned by the gateway daemon, not the agent.

## One Tool, Many Actions

Everything goes through a single `cronjob` tool with action-style operations:

```python
cronjob(action="create", ...)
cronjob(action="list")
cronjob(action="update", job_id="...")
cronjob(action="pause", job_id="...")
cronjob(action="resume", job_id="...")
cronjob(action="run", job_id="...")
cronjob(action="remove", job_id="...")
```

There are also `/cron` slash commands inside any chat, and a `hermes cron ...` CLI surface. Same backend, same jobs.json.

## Tick Loop

The gateway ticks the scheduler every 60 seconds. On each tick:

1. Load jobs from `~/.hermes/cron/jobs.json`
2. Check `next_run_at` against current time
3. Start a fresh `AIAgent` session for each due job
4. Optionally inject attached skills
5. Run the prompt to completion
6. Deliver the final response
7. Update run metadata and schedule

A file lock at `~/.hermes/cron/.tick.lock` prevents overlapping ticks from running the same batch twice.

> Each tick is owned by the **gateway**, not the agent session that scheduled the job. This means cron survives agent restarts and works even when no chat is connected.

## Schedules

| Format | Example | Repetition |
|--------|---------|------------|
| Relative delay | `30m`, `2h`, `1d` | One-shot |
| Interval | `every 30m`, `every 2h`, `every 1d` | Forever (overridable with `repeat=N`) |
| Cron expression | `0 9 * * *`, `*/6 * * * *` | Forever |
| ISO timestamp | `2026-03-15T09:00:00` | One-shot |

The agent can also write schedules in natural language — "every morning at 9am" parses into cron expressions.

## Provider Pinning (important for unattended jobs)

> At creation, an unpinned job follows the global default selected by `hermes model` — and Hermes **snapshots** that provider and model on the job. If the global default later changes, the job **fails closed**: skips the run, makes no inference call, and sends an alert telling you to pin the provider/model explicitly.
>
> To make a job deliberately track your global default, pin it to the new values after changing them. `hermes setup --portal` is the lowest-friction option for unattended runs since OAuth refresh is automatic.

This is a deliberate fail-safe for unattended jobs (#44585) — prevents a global default switch from silently routing an unattended cron through a paid provider. Pin the job to the model you want it to run on.

## Skills on Cron Jobs

A cron job can load zero, one, or multiple skills before it runs:

```python
cronjob(
    action="create",
    skills=["blogwatcher", "maps"],
    prompt="Look for new local events and interesting nearby places, then combine into one short brief.",
    schedule="every 6h",
    name="Local brief",
)
```

Skills are loaded in order. The prompt becomes the task instruction layered on top of those skills. Useful when a scheduled agent should inherit reusable workflows without stuffing the full skill text into the cron prompt.

## Delivery Targets

Where the agent's final response goes:

| Token | Resolves to |
|-------|-------------|
| `"origin"` (default on messaging) | The chat the job was created in |
| `"local"` (default on CLI) | `~/.hermes/cron/output/` |
| `"telegram"`, `"discord"`, `"slack"`, `"whatsapp"`, `"signal"`, `"matrix"`, `"mattermost"`, `"sms"`, `"email"`, `"homeassistant"`, ... | Respective platform home channel |
| `"telegram:123456"` / `"telegram:-100123:17585"` | Specific chat / topic |
| `"all"` | Every connected home channel, expanded at fire time |
| `"origin,all"` / `"telegram,discord"` | Combine any tokens |

`all` is **resolved at fire time** — a job created before you wired up Telegram will pick up Telegram on the next tick after you set `TELEGRAM_HOME_CHANNEL`.

### Telegram Cron Topic

By default, Telegram cron deliveries go to the bot's home channel. For users running topic-mode Telegram, set:

```bash
TELEGRAM_CRON_THREAD_ID=<topic-id>
```

…and point cron at a dedicated forum topic. The root DM is reserved as a system lobby, so replies sent there get rebuffed with a lobby reminder.

## Wrapping & Continuable Jobs

By default, delivered cron output is wrapped with a header / footer so the recipient knows it came from a scheduled task:

```
Cronjob Response: Morning feeds
-------------

<agent output here>

Note: The agent cannot see this message, and therefore cannot respond to it.
```

Set `cron.wrap_response: false` in config to suppress the wrapper.

For **continuable** jobs, replies to a cron delivery continue the conversation with the brief in context — set `cron.mirror_delivery: true` globally, or per-job via `attach_to_session=True` on `cronjob.create`. Thread-capable platforms open a per-delivery thread; DM-only platforms mirror into the origin DM.

## The Silent Suppression Token

If the agent's final response is exactly `[SILENT]`, delivery is suppressed — output saved to `~/.hermes/cron/output/` for audit, but no message sent. Useful for watchdog-style jobs:

> Check if nginx is running. If everything is healthy, respond with only `[SILENT]`. Otherwise, report the issue.

Failed runs always deliver regardless of `[SILENT]` — only successful runs can be silenced.

## No-Agent Mode (Watchdogs, Heartbeats)

For recurring jobs that don't need LLM reasoning — classic watchdogs, disk/memory alerts, heartbeats — pass `no_agent=True`. The scheduler runs your script on schedule, delivers its stdout directly, and skips the agent entirely:

```bash
hermes cron create "every 5m" \
  --no-agent \
  --script memory-watchdog.sh \
  --deliver telegram \
  --name "memory-watchdog"
```

Semantics:

- Script stdout (trimmed) → delivered verbatim
- **Empty stdout → silent tick**, no delivery (the watchdog pattern)
- Non-zero exit / timeout → error alert delivered (broken watchdog can't fail silent)
- `{"wakeAgent": false}` on the last line → silent tick
- No tokens, no model, no provider fallback — never touches the inference layer

`script_timeout_seconds` defaults to 120s; raise via config or `HERMES_CRON_SCRIPT_TIMEOUT` env var.

## The `wakeAgent` Gate (Cheap Pre-Run Filters)

The wakeAgent pattern lets a pre-run script decide whether the LLM should even be invoked. Emit one final stdout line:

```text
{"wakeAgent": false}
```

…and cron skips the agent run for this tick. Useful for high-frequency polls that should only wake the LLM when state actually changed.

Three pre-run gate patterns cover most cases:

| Gate | Idea |
|------|------|
| **File-change** | Compare `mtime` of a watched file against the job's `last_run_at` |
| **External-flag** | Look for a signal file dropped by another process (CI, deploy hook) |
| **SQL-count** | Query your own database for "new rows since last successful tick" and pass the count via `context` |

All three are $0 on no-wake ticks — no LLM tokens spent.

## Chaining Jobs with `context_from`

A cron job can consume the most recent successful output of one or more other jobs:

```python
cronjob(action="create", name="ai-news-triage",
        schedule="30 7 * * *",
        context_from="<ai-news-collector-id>",
        prompt="Read the collector's output. Score 1–10 for engagement potential. Top 5.")
```

Accepts:

- A single job ID string
- A list of job IDs

Most-recent completed outputs are prepended to the prompt. Use it for multi-stage pipelines (collect → filter → format → deliver), fan-in patterns (one job aggregates from several), or DAG-shaped job graphs.

## Cron Runs in Isolated Sessions

> Cron jobs run in a completely fresh agent session. The prompt must contain everything the agent needs that is not already provided by attached skills.

The prompt **is** the session. There's no chat history, no previous turns, no shared memory state (other than attached skills + `context_from`).

## Security

Scheduled task prompts are scanned for prompt-injection and credential-exfiltration patterns at create / update time. Prompts containing invisible Unicode tricks, SSH backdoor attempts, or obvious secret-exfiltration payloads are blocked.

## Workdir Mode

Cron jobs default to running detached from any repo — no `AGENTS.md`, `CLAUDE.md`, or `.cursorrules` loaded. Pass `--workdir <abs-path>` (CLI) or `workdir=<abs-path>` (tool call) to inject project context:

- `AGENTS.md`, `CLAUDE.md`, `.cursorrules` from that dir are loaded into the system prompt
- `terminal`, `read_file`, `write_file`, `patch`, `search_files`, `execute_code` use that dir as cwd
- Absolute directory required — relative paths and missing dirs rejected at create / update
- **Workdir jobs serialize on the tick** (don't enter the parallel pool) because they apply their cwd through process-global terminal state

## Provider Recovery

Cron jobs inherit your configured `fallback_providers` (legacy: `fallback_model`) and credential pool rotation. A single rate-limited key won't fail the run — Hermes falls back to the next credential, then the next provider.

## Storage

- Jobs: `~/.hermes/cron/jobs.json` (atomic writes)
- Outputs: `~/.hermes/cron/output/{job_id}/{timestamp}.md`
- Storage uses atomic file writes so interrupted writes don't leave a partially written job file

## Job-Naming Lookup

All four mutating verbs (`pause`, `resume`, `run`, `remove`, `edit`) accept a job **name** (case-insensitive) in place of the hex ID. An exact ID takes precedence. Ambiguous name matches (multiple jobs sharing a name) are refused with the candidate IDs printed — prevents silently mutating the wrong job.

## Why This Card Exists

Before this card (and `engineering/hermes/README.md`), the KB had no operational reference for cron — only a one-line mention in the README ("Scheduled automations — Built-in cron with delivery to any platform"). The `cronjob` tool is the single most-used backend for unattended work, and the no-agent mode + `wakeAgent` gate are key cost-control primitives.

## See Also

- [[engineering/hermes/README]] — High-level overview; this card is the depth reference
- [[engineering/hermes/architecture]] — Where the `cronjob` toolset key fits in the broader toolsets registry
- [[engineering/hermes-kanban]] — A different kind of durable coordination (multi-agent task board vs single-agent scheduled tick)
- [[engineering/loop-engineering]] — Cron + skills + `wakeAgent` + workdir compose into a self-running loop; loop-engineering is the umbrella
- [[engineering/2026-06-19-engineering-agentic-loop-pattern]] — The inner agent loop that runs inside each cron tick
