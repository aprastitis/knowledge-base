---
type: card
status: active
date: 2026-07-01
summary: Hermes gateway — single background process that connects Telegram, Discord, Slack, WhatsApp, Signal, Email, 20+ other platforms; per-chat sessions, allowlist + pairing, admin/user tiers, shared slash-command library.
tags: [hermes, gateway, messaging, telegram, discord, slack, security, allowlist]
sources:
  - https://hermes-agent.nousresearch.com/docs/user-guide/messaging/
  - https://hermes-agent.nousresearch.com/docs/user-guide/messaging/telegram
  - https://hermes-agent.nousresearch.com/docs/user-guide/messaging/discord
  - https://hermes-agent.nousresearch.com/docs/user-guide/messaging/slack
related:
  - engineering/hermes/README
  - engineering/hermes/security
  - engineering/hermes/architecture
  - engineering/hermes/cronjob
  - engineering/hermes/profiles
  - engineering/hermes/observer-hooks
  - engineering/hermes/2026-07-26-hermes-v0-18-judgment-release
---

# Hermes Messaging Gateway

The gateway is the single background process that connects Hermes to chat platforms. It runs platform adapters (Telegram, Discord, Slack, WhatsApp, Signal, SMS, Email, Home Assistant, Mattermost, Matrix, DingTalk, Feishu/Lark, WeCom, Weixin, BlueBubbles, QQ, Yuanbao, MS Teams, LINE, ntfy, Raft, Webhooks) and an OpenAI-compatible API server, plus the cron scheduler — all from one process.

## Architecture (in one diagram)

```
[Per-platform adapters] ──► [Per-chat session store] ──► [AIAgent]
[API Server + Webhooks] ──►                              ▲
                                                          │
[Cron scheduler, 60s tick] ─────────────────────────────►┘
```

Each platform adapter receives messages, routes them through a per-chat session store, and dispatches them to the `AIAgent`. The same gateway process runs the cron scheduler ticking every 60s. Each installed platform comes with its own toolset (`hermes-telegram`, `hermes-discord`, etc.) — full tools including terminal for most.

## Supported Platforms (as of Jun 2026)

| Platform | Voice | Images | Files | Threads | Reactions | Typing | Streaming |
|----------|:-----:|:------:|:-----:|:-------:|:---------:|:------:|:---------:|
| Telegram | ✅ | ✅ | ✅ | ✅ | — | ✅ | ✅ |
| Discord | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Slack | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Google Chat | — | ✅ | ✅ | ✅ | — | ✅ | — |
| WhatsApp | — | ✅ | ✅ | — | — | ✅ | ✅ |
| Signal | — | ✅ | ✅ | — | — | ✅ | ✅ |
| SMS | — | — | — | — | — | — | — |
| Email | — | ✅ | ✅ | ✅ | — | — | — |
| Home Assistant | — | — | — | — | — | — | — |
| Mattermost | ✅ | ✅ | ✅ | ✅ | — | ✅ | ✅ |
| Matrix | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| DingTalk | — | ✅ | ✅ | — | ✅ | — | ✅ |
| Feishu/Lark | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| WeCom | ✅ | ✅ | ✅ | — | — | — | — |
| Weixin | ✅ | ✅ | ✅ | — | — | ✅ | ✅ |
| BlueBubbles (iMessage) | — | ✅ | ✅ | — | ✅ | ✅ | — |
| QQ | ✅ | ✅ | ✅ | — | — | ✅ | — |
| Yuanbao | ✅ | ✅ | ✅ | — | — | ✅ | ✅ |
| Microsoft Teams | — | ✅ | — | ✅ | — | ✅ | — |
| LINE | — | ✅ | ✅ | — | — | ✅ | — |
| ntfy | — | — | — | — | — | — | — |
| Raft | — | — | — | — | — | — | — |

The API server is OpenAI-compatible, so any front-end that speaks OpenAI's API (Open WebUI, etc.) can use Hermes as a drop-in backend.

## Production Lifecycle (v0.18)

Hermes v0.18.0 added **scale-to-zero** for idle hosted/relay gateways and **drain coordination** for restart, migration, and auto-update. The gateway can quiesce without dropping in-flight conversations, then wake on demand. See [[engineering/hermes/2026-07-26-hermes-v0-18-judgment-release]] for the release-level context.

## Quick Setup

```bash
hermes gateway setup     # Interactive wizard for all platforms
hermes gateway install   # Install as user service (Linux systemd / macOS launchd)
hermes gateway start     # Start the default service
hermes gateway status    # Check default service status
```

For headless VMs where you need boot-time survival:

```bash
sudo hermes gateway install --system   # boot-time system service (root-needed)
sudo loginctl enable-linger $USER      # alt: user service + linger, no root
```

Use `--system` if you specifically need system-level isolation; otherwise user + linger avoids every root prompt and lets `hermes update` restart the gateway without `sudo`.

## Security Model — Three Layers

By default, **the gateway denies every user not in an allowlist or paired via DM.** This is the safe default for a bot with terminal access. Three layers stack:

### 1. Allowlist

```bash
TELEGRAM_ALLOWED_USERS=123456789,987654321
DISCORD_ALLOWED_USERS=123456789012345678
GATEWAY_ALLOWED_USERS=123456789,987654321     # cross-platform
GATEWAY_ALLOW_ALL_USERS=true                  # NOT recommended for terminal bots
```

Per-platform env vars (`TELEGRAM_ALLOWED_USERS`, `DISCORD_ALLOWED_USERS`, `SIGNAL_ALLOWED_USERS`, etc.) override the global. Email is the exception — unknown email senders are ignored unless email pairing is explicitly enabled.

### 2. DM Pairing (alternative to allowlists)

Unknown users receive a one-time pairing code when they DM the bot; you approve them with:

```bash
hermes pairing approve telegram XKGH5N7P
hermes pairing list                            # pending + approved
hermes pairing revoke telegram 123456789       # remove access
```

Pairing codes expire after 1 hour, are rate-limited, and use cryptographic randomness.

### 3. Admin / User Tier Split

Allowlists answer "can this person reach the bot at all?" The admin / user split answers "now that they're in, what are they allowed to do?" Every allowed user falls into one of two tiers per scope (DM vs group/channel):

- **Admin** — full access; runs every registered slash command (built-in + plugin) and uses every gated capability.
- **Regular user** — chat normally; only runs the slash commands you explicitly enable.

Configuration per platform and per scope (DM admin ≠ group admin):

```yaml
gateway:
  platforms:
    discord:
      extra:
        allow_from: ["111", "222", "333"]
        allow_admin_from: ["111"]                    # admins → all slash commands
        user_allowed_commands: [status, model]       # what non-admins may run
        group_allow_admin_from: ["111"]              # separate group scope
        group_user_allowed_commands: [status]
```

> **Backward compat:** if `allow_admin_from` is not set for a scope, the tier split is disabled for that scope and every allowed user has full access. Existing installs keep working with no changes — opt in when you want the distinction.

Use `/whoami` from any platform to see the active scope, your tier, and which slash commands you can run.

## Shared Slash Commands

All platforms share a unified command set. The full list (current):

| Command | Description |
|---------|-------------|
| `/new` or `/reset` | Start a fresh conversation |
| `/model [provider:model]` | Show or change model |
| `/personality [name]` | Set a personality |
| `/retry`, `/undo` | Retry / remove last exchange |
| `/status` | Show session info |
| `/whoami` | Show scope, tier, allowed slash commands |
| `/stop` | Stop the running agent |
| `/approve`, `/deny` | Approve / reject a pending dangerous command |
| `/sethome` | Set this chat as the home channel |
| `/compress` | Manually compress conversation context |
| `/title [name]`, `/resume [name]` | Set / show / resume named sessions |
| `/usage` | Show token usage |
| `/insights [days]` | Show usage insights |
| `/reasoning [level\|show\|hide]` | Toggle reasoning effort / display |
| `/voice [...]` | Control voice replies and Discord voice channels |
| `/rollback [number]` | List / restore filesystem checkpoints |
| `/background <prompt>` | Run a prompt in a separate background session |
| `/reload-mcp` | Reload MCP servers from config |
| `/update` | Update Hermes to latest |
| `/help` | Show available commands |
| `/<skill-name>` | Invoke any installed skill |

Plus per-platform extras: `/cron`, `/kanban`, `/platform [...]`, etc.

## Session Management

Sessions persist across messages until they reset. Three reset policies:

| Policy | Default | Configure |
|--------|---------|-----------|
| Daily | 4:00 AM | `gateway.reset_by_platform.<platform>` |
| Idle | 1440 min | `idle_minutes` per platform |
| Both | combined | whichever triggers first |

```json
{
  "reset_by_platform": {
    "telegram": { "mode": "idle", "idle_minutes": 240 },
    "discord": { "mode": "idle", "idle_minutes": 60 }
  }
}
```

## Interrupting the Agent

Send any message while the agent is working to interrupt it. Behavior:

- **In-progress terminal commands killed immediately** (SIGTERM, then SIGKILL after 1s)
- **Tool calls cancelled** — only the currently-executing one runs
- **Multiple messages combined** — sent during interruption joined into one prompt
- **`/stop`** interrupts without queuing a follow-up

### Busy-Input Modes (`interrupt` / `queue` / `steer`)

By default, messaging a busy agent interrupts it. Three modes:

| Mode | Behavior |
|------|----------|
| `interrupt` (default) | Cancel current task, start new turn |
| `queue` | Follow-up messages wait and run as the next turn |
| `steer` | Follow-up injected via `/steer` after the next tool call — no interrupt |

```yaml
display:
  busy_input_mode: steer
  busy_ack_enabled: true
```

The first time you message a busy agent on any platform, Hermes appends a one-line tip explaining the knob. Latched in `onboarding.seen.busy_input_prompt`; delete that key to see the tip again.

## Silence Tokens

For group chats and automation flows, Hermes supports explicit silence tokens — if the agent's final response is exactly one of these, delivery is suppressed:

- `[SILENT]`
- `SILENT`
- `NO_REPLY`
- `NO REPLY`

Whitespace and case normalized. The whole final response must be the token. Failure turns always surface as errors — Hermes does not hide failures because the text resembles a silence token.

## Tool Progress Notifications

```yaml
display:
  tool_progress: all        # off | new | all | verbose
  tool_progress_grouping: accumulate    # accumulate | separate
```

When enabled, the bot sends status messages like:

```
💻 `ls -la`...
🔍 web_search...
📄 web_extract...
🐍 execute_code...
```

Mobile-friendly defaults for Telegram: `tool_progress: off`, `busy_ack_detail: off`, with `interim_assistant_messages` and `long_running_notifications` kept on (signal, not noise).

## Background Sessions (`/background`)

Run a prompt in a separate background session so the agent works on it independently:

```
/background Check all servers in the cluster and report any that are down
```

The background agent has its own session, inherits your model / provider / toolsets, doesn't block your chat, and delivers the result back to the same chat with `✅ Background task complete`.

For long-running processes started inside `/background` with `terminal(background=true)`, control push notifications via `display.background_process_notifications` (`all | result | error | off`).

## Platform Operations — Day 2

### `/platform` command

Once the gateway is running, use `/platform` from any CLI session or chat to steer individual adapters without restarting:

```
/platform list                  # all adapters and state
/platform pause <name>          # stop dispatching to one adapter
/platform resume <name>         # re-enable a paused adapter
```

State values: `running`, `paused` (manually), `paused-by-breaker` (circuit breaker tripped).

### Automatic Circuit Breaker

Each adapter wraps in a circuit breaker. Repeated retryable failures (network blips, rate-limit replies, 5xx upstream responses, websocket disconnects) trip the breaker:

- Adapter auto-paused
- Operator notification sent to the home channel of another live platform (if one is configured)
- Structured log line emitted

**Breaker does NOT auto-resume.** Stays open until you run `/platform resume <name>` manually. Intentional — sustained outages shouldn't trigger reconnect thrashing.

When an adapter is paused, look at:

1. Gateway log (`~/.hermes/logs/gateway.log` or systemd / launchd unit log)
2. `/platform list` output — state + last reason
3. Provider's status page (Telegram bot API, Discord status, etc.)

### Restart Notifications

When the gateway restarts (with in-flight sessions), it sends a one-shot "the agent is back" / "the agent was interrupted" message to each platform's home channel. Per-platform:

```yaml
gateway:
  platforms:
    telegram:
      home_chat_id: "123456789"
      gateway_restart_notification: false
```

Disable on noisy / low-priority platforms while keeping it on your primary chat.

### Session Resume Across Restarts

When the gateway shuts down with an in-flight tool call or generation, sessions are flagged `restart_interrupted`. On next startup, the gateway schedules auto-resume; user gets a one-line heads-up ("Send any message after restart and I'll try to resume where you left off.") and the session picks up from the last committed turn on reply.

This is on by default. Disable with `gateway_restart_notification: false`.

### Progress Bubble Cleanup (opt-in)

Auto-delete tool-progress messages, "still working…" heartbeats, status-callback bubbles after the final response lands:

```yaml
display:
  platforms:
    telegram:
      cleanup_progress: true
    discord:
      cleanup_progress: true
```

Defaults to `false`. Only platforms whose adapter implements `delete_message` honor the setting (currently Telegram and Discord). **Failed runs skip cleanup** so the bubbles remain as breadcrumbs.

## Multi-Installation Awareness

Each `HERMES_HOME` directory gets its own service label:

- Linux systemd: `hermes-gateway` (default `~/.hermes`), `hermes-gateway-<hash>` (other homes)
- macOS launchd: `ai.hermes.gateway` (default), `ai.hermes.gateway-<suffix>` (other homes)

The `hermes gateway` commands automatically target the correct service for your current `HERMES_HOME`.

## Why This Card Exists

Before this card, the KB covered the gateway at a one-line level in `engineering/hermes/README`. The gateway is the deployment-level surface most users interact with first — install path, allowlist setup, platform support, security posture, day-2 ops (circuit breakers, restart notifications). A single operational reference card consolidates the architecture, security, and ops pieces that previously required cross-referencing 4–5 separate guides.

## See Also

- [[engineering/hermes/README]] — High-level overview; this card is the depth reference for the gateway specifically
- [[engineering/hermes/security]] — Trust model, isolation posture, plugin trust (composite view of how security layers compose across adapters)
- [[engineering/hermes/architecture]] — AIAgent loop, per-platform toolsets (`hermes-telegram`, `hermes-discord`, etc.) registered as individual toolset keys
- [[engineering/hermes/cronjob]] — Cron uses the same gateway process; cron delivery targets resolve via this gateway's platform adapters
- [[engineering/hermes/profiles]] — Multi-instance isolation; each profile gets its own gateway token lock, allowing per-profile gateway instances
- [[engineering/hermes/observer-hooks]] — Read-only telemetry hooks for the gateway's API request and tool call boundaries
