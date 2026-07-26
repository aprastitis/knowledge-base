---
type: engineering
status: active
date: 2026-07-26
summary: Hermes Agent v0.18.0 made verification, visible self-improvement, background fan-out, and production gateway lifecycle first-class capabilities.
tags: [hermes, release, verification, self-improvement, multi-agent, gateway, desktop, security]
related:
  - engineering/2026-06-19-engineering-agentic-loop-pattern
  - engineering/harness-engineering
  - concepts/self-evolving-skills
  - engineering/async-subagents
  - engineering/2026-06-27-hermes-moa-virtual-models
  - engineering/hermes-three-tier-memory
  - engineering/hermes/messaging
  - engineering/hermes/plugins
sources:
  - https://github.com/NousResearch/hermes-agent/releases/tag/v2026.7.1
  - https://x.com/NousResearch/status/2072413332665962617
---

# Hermes v0.18.0 — The Judgment Release

Released July 1, 2026 as `v2026.7.1`, Hermes Agent v0.18.0 centers on a single reliability question: **how does the agent know its work is actually done?** It combines completion contracts and verification evidence with visible, steerable self-improvement, non-blocking multi-agent fan-out, and production gateway lifecycle controls.

The release also closed every open P0/P1 issue and PR at the sweep cutoff: 3 P0 issues + 8 P0 PRs, and 493 P1 issues + 188 P1 PRs. The release notes report roughly 1,720 commits, 998 merged PRs, 2,215 files changed, and 949 issues closed since v0.17.0.

## Core Pattern: Completion Is an Evidence Contract

`/goal` can now carry a **completion contract**: a concrete description of what “done” means. The standing-goal loop judges completion against verification evidence rather than the model's claim.

For coding work, Hermes maintains a profile-scoped verification ledger of canonical project checks detected from coding context. A `pre_verify` hook can add custom checks; verification can also use ad-hoc scripts. The important architectural shift is:

```text
agent says done  →  harness collects evidence  →  contract judges evidence  →  stop or continue
```

This turns the verify step in [[engineering/2026-06-19-engineering-agentic-loop-pattern]] into a durable subsystem rather than a prompt-level suggestion. It is also a concrete implementation of [[engineering/harness-engineering]]: deterministic or custom checks, a recorded evidence ledger, and an explicit stopping rule.

### Operational nuance

Verification-on-stop defaults are conservative: the release restored a surface-aware `auto` mode, skips documentation-only edits, and gates automatic verification off on messaging surfaces. `/goal wait` can park the standing-goal loop on a background process instead of repeatedly waking the agent.

## Self-Improvement Becomes Visible and Steerable

### `/learn`

`/learn` distills a reusable skill from:

- a directory,
- a URL, or
- a workflow just demonstrated in the conversation.

The generated skill follows the repository's `CONTRIBUTING.md` skill standards. This extends [[concepts/self-evolving-skills]] beyond passive learning from successful trajectories: users can now deliberately point Hermes at external material or a fresh walkthrough and ask it to compile the procedure.

### `/journey` and the memory graph

`/journey` provides a CLI/TUI timeline of accumulated memories and skills with in-place edit and deletion. The desktop app adds a playable radial memory graph. Together they make agent learning inspectable: users can see what Hermes retained, correct it, and prune it instead of treating memory as a black box.

This is the missing governance layer around [[engineering/hermes-three-tier-memory]]: storage and retrieval are not enough; accumulated knowledge also needs provenance, inspection, and correction surfaces.

### Cheaper background review

The post-turn self-improvement fork now:

1. routes to an auxiliary model,
2. digests context rather than replaying the full conversation, and
3. adapts its cadence.

This separates the economics of reflection from the main interaction model. Self-improvement remains continuous without paying full main-model cost after every turn.

## Background Fan-Out

`delegate_task` can fan out several subagents in parallel while the parent conversation remains usable. Results return as one consolidated turn after all children finish.

This advances [[engineering/async-subagents]] from “one delegated task no longer blocks” to a proper fork-join pattern:

```text
parent turn
  ├─ subagent A ┐
  ├─ subagent B ├─ run concurrently in background
  └─ subagent C ┘
         ↓
consolidated completion turn
```

The clean consolidation boundary matters. It reduces result-handling noise and lets the parent reason over a complete batch instead of manually babysitting each child.

## MoA Graduates Into the Model System

Named Mixture-of-Agents presets now appear as ordinary selectable models under a `moa` provider across CLI, TUI, desktop, and gateway surfaces. Reference-model outputs render as labeled blocks, then the aggregator streams the final answer live.

Additional maturity changes include full tool-state visibility for reference models, optional JSONL trace persistence for debugging/evals, aggregator-aware context-window resolution, and provider-route correctness. These changes complete the productization described in [[engineering/2026-06-27-hermes-moa-virtual-models]]: MoA is no longer a special mode but a virtual-model abstraction with observability.

## Gateway Lifecycle Maturity

The gateway gained two production deployment primitives:

- **Scale-to-zero:** an idle hosted or relay-only gateway can go dormant and wake on demand.
- **Drain coordination:** restart, migration, and auto-update can quiesce the gateway without cutting off in-flight conversations.

This moves [[engineering/hermes/messaging]] from a long-running adapter process toward a schedulable service with explicit lifecycle states. It matters for hosted and team deployments where rolling operations and idle cost are real constraints.

## Desktop Coding Projects

The desktop app added profile-scoped Projects backed by a `project → repo → lane` model, plus:

- codebase sidebar,
- coding rail and review pane,
- git status/review/worktree management,
- agent-facing project tools,
- persistent multi-terminal tabs and scrollback,
- PR-style file diffs and in-app spot editing.

The significance is not just UI polish. A project becomes structured context the agent can act on, rather than a loose collection of chats and folders.

## Provider and Prompting Additions

- **`/prompt`:** opens `$EDITOR` for long, multiline Markdown prompts and queues the saved text as the next message.
- **Google Vertex AI:** first-class Gemini provider using a GCP service account or Application Default Credentials, with automatic refresh of short-lived OAuth2 access tokens. This solves the mid-session expiry problem that static custom-provider configuration cannot handle.

## Security Round

The release hardened several credential and network surfaces:

- MCP configuration persistence,
- cron `base_url` overrides that could exfiltrate provider credentials,
- prefix-secret file-read sentinels,
- Slack app-level token redaction,
- browser cloud-metadata access floors,
- minimum safe `aiohttp` versions in lazy messaging paths.

The theme is consistent with Hermes's trust model: tool-capable sessions need defenses against both prompt injection and accidental credential routing.

## Why This Release Matters

v0.18.0 ties together four layers that were previously separate:

1. **Execution:** parallel agents and desktop projects do work.
2. **Judgment:** completion contracts and evidence decide whether work is done.
3. **Learning:** `/learn` and background review preserve reusable procedures.
4. **Oversight:** `/journey`, memory graphs, traces, and labeled MoA outputs make internal accumulation and deliberation visible.

The result is not merely more features. It is a more complete agent operating model: **act, verify, learn, expose the evidence, and remain interruptible while background work continues.**

## Related

- [[engineering/2026-06-19-engineering-agentic-loop-pattern]] — inner gather/act/verify/repeat loop
- [[engineering/harness-engineering]] — completion contracts and evidence as harness controls
- [[concepts/self-evolving-skills]] — procedural learning model extended by `/learn`
- [[engineering/async-subagents]] — foundation for background fan-out
- [[engineering/2026-06-27-hermes-moa-virtual-models]] — MoA virtual-model architecture
- [[engineering/hermes-three-tier-memory]] — storage architecture governed by `/journey`
- [[engineering/hermes/messaging]] — gateway architecture extended by scale-to-zero and draining
- [[engineering/hermes/plugins]] — provider architecture extended by Vertex AI
