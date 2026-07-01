---
type: card
status: active
date: 2026-06-12
summary: Self-improving AI agent built by Nous Research — CLI + messaging gateway + skills + memory + cron + kanban
tags: [hermes, agent, nous-research, cli, messaging, skills]
sources:
  - https://github.com/NousResearch/hermes-agent/blob/main/README.md
  - https://hermes-agent.nousresearch.com/docs/
related:
  - engineering/hermes-kanban
  - engineering/hermes-native-mcp-catalog
  - engineering/hermes-three-tier-memory
  - engineering/hermes/messaging
  - engineering/hermes/cronjob
  - engineering/2026-06-21-hermes-blank-slate-setup
  - engineering/agentic-coding-principles
---

# Hermes Agent ☤

**The self-improving AI agent built by [Nous Research](https://nousresearch.com).**

Hermes is the only agent with a built-in learning loop — it creates skills from experience, improves them during use, nudges itself to persist knowledge, searches its own past conversations, and builds a deepening model of who you are across sessions.

## Key Capabilities

| Capability | Description |
|------------|-------------|
| **Any model** | Nous Portal, OpenRouter (200+), NVIDIA NIM, OpenAI, Anthropic, Hugging Face, or custom endpoint |
| **Real terminal** | Full TUI with multiline editing, slash-command autocomplete, conversation history, interrupt-and-redirect |
| **Messaging platforms** | Telegram, Discord, Slack, WhatsApp, Signal, Email — all from one gateway process |
| **Closed learning loop** | Agent-curated memory, autonomous skill creation, FTS5 session search, Honcho dialectic user modeling |
| **Scheduled automations** | Built-in cron with delivery to any platform |
| **Delegation** | Spawn isolated subagents for parallel workstreams |
| **Runs anywhere** | Local, Docker, SSH, Singularity, Modal, Daytona — $5 VPS to GPU cluster |

## Quick Install

**Linux/macOS/WSL2:**
```bash
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash
```

**Windows (native, PowerShell):**
```powershell
iex (irm https://hermes-agent.nousresearch.com/install.ps1)
```

After installation:
```bash
source ~/.bashrc    # reload shell
hermes              # start chatting!
```

## Core Commands

```bash
hermes              # Interactive CLI
hermes model        # Choose LLM provider and model
hermes tools        # Configure which tools are enabled
hermes config set   # Set individual config values
hermes gateway      # Start the messaging gateway (Telegram, Discord, etc.)
hermes setup        # Run the setup wizard (Quick / Full / Blank Slate)
hermes update       # Update to the latest version
hermes doctor       # Diagnose any issues
```

## Setup Modes

`hermes setup` now offers three modes (added Blank Slate on 2026-06-20):

- **Quick** (default) — Reasonable defaults; bundled skills + MCP catalog ready to go
- **Full** (`hermes setup --portal`) — OAuth-driven; Nous as provider; Tool Gateway (web search, image gen, TTS, browser) auto-enabled
- **Blank Slate** — Minimal baseline: provider + model + file ops + terminal only. No bundled skills, no MCP catalog. Add everything else manually. Best for power users, minimal-surface installs, or debugging whether a default-loaded skill is causing a problem.

See [[engineering/2026-06-21-hermes-blank-slate-setup]] for full details.

## Nous Portal — Skip the API-Key Collection

One command from a fresh install:
```bash
hermes setup --portal
```

That logs you in via OAuth, sets Nous as your provider, and turns on the Tool Gateway (web search via Firecrawl, image generation via FAL, TTS via OpenAI, cloud browser via Browser Use).

## CLI vs Messaging Quick Reference

| Action | CLI | Messaging platforms |
|--------|-----|---------------------|
| Start chatting | `hermes` | Run `hermes gateway setup` + `hermes gateway start` |
| Change model | `/model [provider:model]` | `/model [provider:model]` |
| Retry/undo | `/retry`, `/undo` | `/retry`, `/undo` |
| Compress context | `/compress`, `/usage` | `/compress`, `/usage` |
| Interrupt work | `Ctrl+C` or send new message | `/stop` or send new message |

## Architecture

The core loop: `User message → AIAgent._run_agent_loop()`:

1. Build system prompt (prompt_builder.py)
2. Build API kwargs (model, messages, tools, reasoning config)
3. Call LLM (OpenAI-compatible API)
4. If `tool_calls` in response → execute each tool, add results to conversation, loop back
5. If text response → persist session to DB, return final_response
6. Context compression if approaching token limit

**Key files:**
- `run_agent.py` — AIAgent class, core conversation loop
- `model_tools.py` — Tool orchestration (thin layer over tools/registry.py)
- `hermes_state.py` — SQLite session database with FTS5 full-text search
- `cli.py` — HermesCLI class, interactive TUI
- `gateway/run.py` — GatewayRunner, platform lifecycle, message routing

## Migrating from OpenClaw

```bash
hermes claw migrate              # Interactive migration
hermes claw migrate --dry-run     # Preview what would be migrated
hermes claw migrate --preset user-data   # Migrate without secrets
```

What gets imported: SOUL.md, memories, skills, command allowlist, messaging settings, API keys, TTS assets, workspace instructions.

## Documentation

Full docs at **[hermes-agent.nousresearch.com/docs](https://hermes-agent.nousresearch.com/docs/)**:
- Quickstart, CLI Usage, Configuration
- Messaging Gateway (Telegram, Discord, Slack, etc.)
- Security, Tools & Toolsets, Skills System
- Memory, MCP Integration, Cron Scheduling
- Context Files, Architecture, Contributing

## License

MIT — built by [Nous Research](https://nousresearch.com).