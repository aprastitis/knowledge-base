---
type: engineering
status: active
date: 2026-08-03
summary: Hermes v0.20 "Herald" is the release that turns the agent into a participant — it speaks, it cites its sources, it talks to other agents on a standard wire, and it pushes signed events to your systems.
tags: [hermes, release, voice, a2a, webhooks, grounded-research, desktop-platform, compression, smart-approvals, performance, agent-architecture]
related:
  - engineering/hermes/2026-08-02-hermes-v0-19-quicksilver-release
  - engineering/hermes/2026-07-26-hermes-v0-18-judgment-release
  - engineering/2026-06-19-engineering-agentic-loop-pattern
  - engineering/multi-agent-orchestration-patterns
  - engineering/async-subagents
  - engineering/hermes/architecture
  - engineering/hermes/messaging
  - engineering/hermes/skills
  - engineering/hermes/security
  - engineering/hermes/2026-07-31-hermes-desktop-kanban-plugin
  - engineering/hermes/2026-08-03-hermes-cloud-launch
  - engineering/hermes/2026-08-03-a2a-v1-bundled-plugin
  - engineering/hermes/2026-08-03-grounded-citations-skill
  - engineering/hermes/2026-08-03-outbound-webhooks
  - engineering/hermes/2026-07-24-hermes-credential-firewall
  - engineering/hermes/network-egress-isolation
  - engineering/hermes-kanban
  - engineering/hermes/2026-08-16-hermes-desktop-19x-session-load
  - engineering/hermes/2026-08-16-hermes-desktop-cross-platform
  - engineering/hermes/2026-08-17-hermes-2500-contributors-milestone
sources:
  - https://github.com/NousResearch/hermes-agent/releases/tag/v2026.8.3
  - https://x.com/NousResearch/status/2084325600643445095
  - https://x.com/NousResearch/status/2084325603348816037
---

# Hermes v0.20.0 — The Herald Release

Released August 3, 2026 as `v2026.8.3`, Hermes Agent v0.20.0 is named after Hermes as the herald of the gods — the one who *speaks*, *carries word*, and *announces*. Where v0.18 answered "how does the agent decide it is done?" and v0.19 answered "how does it feel while it is doing it?", v0.20 answers **"how does the agent exist in the world alongside other agents, devices, and humans who are listening for it?"**

The numbers: since v0.19.0 the project reports **~3,650 commits · ~1,400 merged PRs · ~5,200 files changed · ~559,000 insertions · ~405,000 deletions · ~1,200 issues closed** with 650+ contributors (this card counts 647 contributor handles in the credits block of the release notes). v0.20 also rolls up everything that landed in the v0.19.1 patch tag.

The release thesis in three sentences: **the agent now talks to humans on equal conversational footing (streaming voice with barge-in, wake words, "stop" hands-free), it talks to other agents on a standard protocol (A2A v1.0), and it talks to systems that were not built to listen (signed outbound webhooks). Around that spine, the desktop became a platform, the CLI got a wave of power commands, the agent loop got smarter compression, and the tools themselves stopped making the model guess.**

## The Spine

### Conversational Voice with Barge-In

v0.19 made the agent's *thinking* visible. v0.20 makes its *speaking* conversational.

The previous voice mode was a voicemail exchange: speak, wait for the whole reply to generate, listen to one long audio file, speak again. v0.20's voice pipeline streams TTS clause-by-clause — the next sentence renders while the current one speaks — and adds **barge-in**: interrupt Hermes mid-sentence by just talking and the model is told you cut in. Busy-aware silence detection prevents the agent from talking over the user; full-duplex turn listening works during both generation and playback.

This is implemented across the CLI voice mode, the Hermes Desktop app, and every audio-capable gateway platform (WhatsApp, Feishu, DingTalk, LINE, QQ, Photon, Weixin). STT is now fully configurable — its own `hermes tools` category, GUI toggles, dashboard dropdowns, unified language resolution, OpenAI gpt-transcribe support. One unified spoken-text preprocessor cleans markdown/code/URLs out of speech across all TTS providers.

### Wake Words and Hands-Free Control

Wake-word detection runs on-device (no audio leaves the machine while Hermes is listening), supports open-vocabulary phrases ("hey Hermes" or anything the user picks), and supports multi-profile routing — different wake words reach different profiles. Saying "stop" ends voice chat hands-free on every surface. The terminal is now something you can talk to from across the room.

### Outbound Webhooks (Signed)

Until v0.20, integrating with Hermes meant polling or listening on a platform. Now Hermes pushes **signed lifecycle events** (session activity, turn completions, tool events) to any HTTP endpoint the operator registers. HMAC signatures let the receiver verify authenticity. Wire Hermes into CI, home automation, dashboards, or any service that speaks HTTP without a polling loop. This is its own card: [[engineering/hermes/2026-08-03-outbound-webhooks]].

### A2A v1.0 — Hermes Speaks Agent-to-Agent

A bundled plugin now implements the Agent-to-Agent protocol, so Hermes can discover, talk to, and be driven by other A2A-compatible agents. This closes **issue #514** — one of the oldest open feature requests in the repo (4 years). If you are building multi-agent systems with heterogeneous stacks, Hermes now has a standard wire protocol for joining them. This is its own card: [[engineering/hermes/2026-08-03-a2a-v1-bundled-plugin]].

The protocol pairs naturally with [[engineering/multi-agent-orchestration-patterns]] and [[engineering/async-subagents]]. A2A is the wire format; multi-agent patterns are the topologies; async subagents are the in-process delegation primitive. They are complementary, not competing.

### Grounded Research with Verifiable Citations

The new `grounded-citations` skill makes Hermes produce research where every claim is backed by a verifiable source. Quotes are matched against the actual page text (not hallucinated), citations link to the exact evidence, and a **fact-checking mode** turns the same machinery on any document or claim the user hands it — it reports what checks out, what does not, and what could not be verified. If you use Hermes for research, this is the difference between "sounds right" and "provably sourced."

The fact-checking mode is the bigger of the two capabilities in practice. Existing research-quality work in Hermes already had web search and extraction; the new skill turns the *same machinery outward*, letting the agent verify third-party claims (a doc, a paper, a competitor's marketing) the same way it verifies its own output. This is its own card: [[engineering/hermes/2026-08-03-grounded-citations-skill]].

## Around the Spine

### Desktop Becomes a Platform

v0.18 introduced the desktop as a host for code work (coding Projects). v0.19 (Quicksilver) made the host feel fast. v0.20 turns the host into a **platform**:

- **Artifacts** — versioned cards with sandboxed live preview in a right-rail viewer. Generated HTML/apps run safely next to the chat.
- **Real plugin SDK** — Kanban (the [[engineering/hermes/2026-07-31-hermes-desktop-kanban-plugin|first official Desktop plugin]]) becomes the founding reference; `ctx.download` for handing the user a file; floating pane placement; multiple GUI windows; widget-app SDK (apps as state+reducer+render) with three reference apps.
- **Quick-entry window** — global hotkey captures a thought into any session from anywhere in the OS.
- **Let the agent drive the shell** — preview pane + pane focus, plus an inspect-the-app-it's-developing mode. Find-in-page (Ctrl+F), GUI terminal copy/paste + font picker, SSH remote backend for connecting to a remote Hermes.

The desktop is no longer a chat client; it is a workbench that ships its own extension surface.

### CLI Power-User Wave

Seven new commands land in v0.20:

- `!command` — run a shell command instantly, no model turn spent.
- `/init` — scan the project and generate (or update) `AGENTS.md`.
- `/diff` — show staged/all/session changes from any surface.
- `/context` — break down exactly what is filling the context window.
- `/focus` — reduced-output view with hidden-line recovery.
- Ctrl+S — stash a half-written prompt into a browsable panel.
- `hermes import-agent` — migrate Claude Code or Codex CLI setup into Hermes in one command.

Multi-select clarify (checkboxes) lands across CLI/gateway/TUI. The TUI's model picker is reachable without wrecking the draft; mid-turn model switching works; the slash menu leads with the user's most-used skills.

### Correct the Agent Mid-Turn — Redirects

The previous flow for "Hermes is heading the wrong way" was `/stop` + re-explain. v0.20 lets the user type a correction while the agent works, redirects the active turn, preserves in-flight work, keeps the original prompt, and the agent course-corrects with the new guidance. Paired with double-ESC draft discard and a composer undo stack, steering feels like editing, not restarting. The /agents view also exposes live per-child status (structured timeout/stall metadata), and subagents can now use `execute_code`.

### Compression That Respects the Conversation

Context compression got a deep overhaul in v0.20. Previously, a long session hit a single "compress now" wall, then started losing the tail of the user's messages. v0.20 ships:

- Proactive tool-result pruning for large-window models.
- Per-turn micro-compaction that amortizes cost instead of one giant pause.
- A guaranteed N-user-message tail (configurable `compression.min_tail_user_messages`) so recent conversation always survives.
- Progress-aware timeouts that stop punishing slow summary models.
- Ghost-skill defense so a pruned skill cannot silently haunt a session.
- Per-model threshold overrides; absolute-token thresholds (`compression.threshold_tokens`); opt-in idle-triggered compaction; opt-in progress notices.

Long sessions stay coherent and stop stalling. This matters more than it sounds: prior to this, the natural limit of a Hermes session was "the model can fit this context, then it loses coherence." After: that limit moves up substantially, and the agent can recover from compression cycles without losing the thread.

### Smart Approvals Grow Up

`hermes approvals suggest` mines the user's approval history into allowlist proposals. Operators can customize `approvals.smart_policy`. A **consecutive-denial circuit breaker** stops a misbehaving approval loop cold. Profile-correct pairing approvals now have a proper desktop surface to answer from. A new approval gate covers Docker/podman daemon-redirect commands, closing a credential-exfiltration vector.

Less clicking "approve", without giving an inch of control. The v0.19 baseline (smart approvals by default) was the foundation; v0.20 turns that into an actively-curated allowlist with operator override.

### Tools That Fix Themselves

A sweep of self-recovery upgrades means the agent wastes far fewer turns on tool friction:

- Terminal output that overflows the buffer spills to a file the agent can read back. Cwd is echoed when a command changes directory. Output-pattern failure hints classify common terminal errors.
- `patch` detects already-applied edits and diagnoses whitespace mismatches with visualized mismatches.
- `write_file` verifies content on disk.
- Searches that match nothing probe for near-misses and recover; multi-path recovery is automatic; multiline patterns are auto-detected.
- `read_file` default limit 500 → 2000 lines; negative-result cache for read/search misses.
- The default tool-calling iteration limit jumped **90 → 500** — long autonomous runs stopped hitting an artificial wall.

The agent stops asking the model to debug tool failures it can detect itself. The model gets clearer failures and recovers its own retries, so the loop in [[engineering/2026-06-19-engineering-agentic-loop-pattern]] stops stalling on tool ergonomics.

### Speed Wave 2

The Quicksilver release cut cold start ~80%. v0.20 cuts it again:

- Tool schemas cached on native Anthropic without history loss.
- `hermes -w` cold start **14s → ~1.8s**.
- `hermes update` no-ops got 2–6s faster.
- Heavy SDKs lazy-load off the import path (−8–10% import cost on top of the prior mcp/tool-discovery diet).
- Config reads stopped deep-copying (54× faster on the telemetry gate).
- Readonly config loader at 29 call sites (28× cheaper reads).
- Desktop shipped a second 60fps wave — streaming cost independent of transcript length, drag at 60fps with five streaming tabs, idle CPU near zero in the background.

The compounding is the story: v0.19 made the loop feel instant; v0.20 made it stay instant under load. The desktop is now close to native-app feel even with multi-thousand-token transcripts and busy parallel agents.

### Providers & Models

- **Vercel AI Gateway** provider returns, modernized; Vercel Sandbox terminal backend returns.
- **Gemini 3.1 Pro + 3.6 Flash** in catalogs; Gemini salvage cluster (3.6-flash aux default, Vertex catalog, direct cost tracking).
- **Claude Opus 5** in OpenRouter + Nous Portal (was already in v0.19 catalog; now fully wired).
- **DeepSeek V4 Flash 0731** added to catalogs.
- Bedrock Converse API prompt caching (cachePoint).
- OpenAI data-residency endpoints get declared transport + correct catalog; provider-aware API-server request routing.
- Model picker: curated defaults + collapsible providers + select-all; stale caches served instantly with background refresh; honcho OAuth device-code login.

### Security & Reliability

- **Iron-proxy credential-injection egress firewall** re-landed (the [[engineering/hermes/2026-07-24-hermes-credential-firewall]] primitive from the v0.19 window).
- DNS-pinned SSRF-safe fetches + Slack CDN allowlist; strict redaction at compaction boundaries; ReDoS eliminated in config-key redaction patterns.
- Tier-3 credential reads scoped (FAL/XAI/VERCEL/DAYTONA/GITHUB presence checks).
- CVE dependency pins refreshed (cryptography, starlette, python-multipart); `hindsight` env file 0600; `/model` moved off the gateway event loop.
- Windows hardening wave: text-mode subprocess decode bug class closed repo-wide; console flashes hidden across daemons/env probes/LSP/installer paths.
- State/session integrity: four session-state fixes; compact v23 FTS layout + `hermes sessions optimize` + CJK-bigram FTS; read-path split with per-thread read-only connections.

### New Places to Run and Be Reached

- **Buzz** (Block/Nostr) — bundled gateway platform adapter with native WebSocket inbound transport + NIP-42 auth.
- **Vercel AI Gateway** provider and **Vercel Sandbox** terminal backend return, modernized.
- **SSH remote-backend** connection mode in desktop.
- **Relay** shipped four phases of parity — media, interactive prompts, thread lifecycle, typing indicators — plus HSP personal + org skill sync.
- **Hermes Cloud** — full distributed deployment on Nous infrastructure, two-click provisioning (its own card: [[engineering/hermes/2026-08-03-hermes-cloud-launch]]).

### Runtime

- Node 26 required across installers/heal/upgrade.
- Managed Node/uv resolve before bare PATH; outdated managed trees heal to target major.
- brew + pip/PyPI wheel channels retired — shell installer / Docker / Nix are the supported channels.
- This is a meaningful change for anyone using `brew install hermes-agent` or `pip install` — those channels now redirect to the supported ones.

## Parallel Net-New Features in the v0.20 Window

Three substantial capabilities landed in the v0.20 window but are documented in their own cards because each has deep architecture worth tracking individually:

- **[[engineering/hermes/2026-08-03-hermes-cloud-launch]]** — full distributed deployment of Hermes on Nous infrastructure. Two-click provisioning, team spin-up, Hermes Desktop discovers Cloud agents. The shift from "you run Hermes" to "Hermes runs on Nous, you talk to it."
- **[[engineering/hermes/2026-08-03-a2a-v1-bundled-plugin]]** — Agent-to-Agent protocol plugin that closes 4-year-old issue #514. Standard wire format for multi-agent composition across stacks.
- **[[engineering/hermes/2026-08-03-grounded-citations-skill]]** — research-quality skill: every claim backed by a verifiable source, plus a fact-checking mode for third-party documents. Turns "sounds right" into "provably sourced."
- **[[engineering/hermes/2026-08-03-outbound-webhooks]]** — Hermes pushes signed lifecycle events to any HTTP endpoint. The integration model inverts from poll/listen to push/receive.

The umbrella card tells the *release narrative*; the per-feature cards track the *architectural implications* of each capability.

## Why This Release Matters

Three v0.x trajectories have converged in v0.20:

1. **v0.18 (Judgment)** — the agent decides well. Verification evidence, `/learn`, fan-out, gateway lifecycle.
2. **v0.19 (Quicksilver)** — the agent feels instant. Speed spine, visible reasoning, durable delivery, smart approvals, vault secrets.
3. **v0.20 (Herald)** — the agent exists in the world. Conversational voice, A2A, signed webhooks, platform desktop, smarter compression, self-recovering tools.

The convergence is the point. The agent is no longer a thing you operate; it is a thing that *participates* — in your conversations (voice), in your stack (webhooks), in your multi-agent systems (A2A), and in your desktop (platform). The remaining 2026 gaps are mostly about *autonomy* (proactive intelligence, agents that anticipate the next turn rather than reacting to the current one) — which, per the v0.19 card's analysis, is a 2027 problem.

The release is also the first one where the *shape of the company* is visible in the release: the Cloud launch implies a hosted product; the platform desktop implies a third-party plugin ecosystem; A2A and webhooks imply Hermes-as-participant in customer stacks. All of those are bets about the future shape of the agent market, not just engineering milestones.

## Related

- [[engineering/hermes/2026-07-26-hermes-v0-18-judgment-release]] — preceding release; verification contracts, `/learn`, `/journey`, fan-out
- [[engineering/hermes/2026-08-02-hermes-v0-19-quicksilver-release]] — preceding release; speed, visible reasoning, durable delivery, smart approvals, vault secrets
- [[engineering/2026-06-19-engineering-agentic-loop-pattern]] — inner loop pattern that v0.20 makes conversationally bidirectional
- [[engineering/multi-agent-orchestration-patterns]] — multi-agent topologies; A2A v1.0 is the wire format that complements them
- [[engineering/async-subagents]] — in-process delegation primitive; pairs with A2A for cross-process composition
- [[engineering/hermes/architecture]] — agent loop internals; v0.20 overhauls compression and iteration limits
- [[engineering/hermes/messaging]] — gateway architecture; v0.20 adds voice and Buzz and HSP org-skill sync
- [[engineering/hermes/skills]] — skills system; v0.20 adds grounded-citations and the curator's `adopt` flow
- [[engineering/hermes/security]] — trust model; v0.20 hardens redaction, secrets, Windows subprocess, and Docker/podman approval gates
- [[engineering/hermes/2026-07-31-hermes-desktop-kanban-plugin]] — founding Desktop plugin; v0.20 turns Desktop into a platform around it
- [[engineering/hermes/2026-08-03-hermes-cloud-launch]] — Hermes Cloud distribution shift
- [[engineering/hermes/2026-08-03-a2a-v1-bundled-plugin]] — Agent-to-Agent protocol plugin
- [[engineering/hermes/2026-08-03-grounded-citations-skill]] — research quality skill
- [[engineering/hermes/2026-08-03-outbound-webhooks]] — signed outbound integration pattern
- [[engineering/hermes/2026-07-24-hermes-credential-firewall]] — security primitive from v0.19 window; re-landed in v0.20
- [[engineering/hermes/network-egress-isolation]] — paired with credential firewall for full sandbox defense
- [[engineering/hermes/2026-08-16-hermes-desktop-19x-session-load]] — Aug 16 perf milestone: Desktop session load 19× faster; first Desktop-specific perf number, extends v0.20's 60fps wave to the window-open path
- [[engineering/hermes/2026-08-16-hermes-desktop-cross-platform]] — Aug 16 distribution milestone: Hermes Desktop GUI ships for macOS, Windows, and Linux; the ship-channel for the v0.20 Desktop-platform work
- [[engineering/hermes/2026-08-17-hermes-2500-contributors-milestone]] — post-v0.20 trajectory: contributor base went from 650+ (v0.20) to 2,500+ in two weeks. Inverts the contribution-supply bottleneck to a review-triage one
- [[engineering/hermes-kanban]] — multi-agent coordination surface benefiting from voice + speed
