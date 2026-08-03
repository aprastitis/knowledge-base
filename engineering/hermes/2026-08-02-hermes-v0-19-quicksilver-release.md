---
type: engineering
status: active
date: 2026-08-02
summary: Hermes v0.19 "Quicksilver" made first-turn latency, visible reasoning, durable delivery, and approval ergonomics first-class product concerns.
tags: [hermes, release, performance, reasoning, approvals, secrets, desktop, gateway]
related:
  - engineering/hermes/2026-07-26-hermes-v0-18-judgment-release
  - engineering/2026-06-19-engineering-agentic-loop-pattern
  - engineering/2026-06-27-hermes-moa-virtual-models
  - engineering/hermes/skills
  - engineering/hermes/messaging
  - engineering/hermes/security
  - engineering/hermes/network-egress-isolation
  - engineering/hermes-kanban
sources:
  - https://x.com/NousResearch/status/2079278653997809984
  - https://github.com/NousResearch/hermes-agent/releases
  - https://x.com/NousResearch/status/2080728699100406042
  - https://x.com/NousResearch/status/2080319476243861569
  - https://x.com/NousResearch/status/2080745634710139113
  - https://hermes-ai.net/changelog/
---

# Hermes v0.19.0 — The Quicksilver Release

Released July 20, 2026 as `v2026.7.20`, Hermes Agent v0.19.0 is named for the messenger god's own speed: the spine of the release is raw responsiveness, with an ~80% cut in first-turn time-to-first-token across every platform (cold start from ~4.3s to ~0.9s).

Since v0.18.0 the project reports ~2,245 commits, ~1,065 merged PRs, ~2,465 files changed, ~300,000 insertions, ~36,000 deletions, ~3,300 issues closed, and 450+ community contributors. v0.19.1 shipped the same week and makes tagged-release Docker builds reliable by pinning the published WhatsApp Baileys dependency.

The release thesis: **the agent loop should feel instant, every action should be legible, and nothing should drop on the floor.** Where v0.18 answered "how does the agent know it is done?", v0.19 answers "how does the agent feel while it is doing it, and what happens when the world does not cooperate?"

## Speed Spine

The headline number is the cold-start reduction. A turn that previously needed ~4.3 s of gateway-and-process setup now needs ~0.9 s. The cuts are not concentrated in one place — they are the result of a series of cumulative optimizations that touch the gateway boot path, the agent process spawn, the prompt-build step, and the early-token pipeline.

The operational consequence is not "agents are a bit faster." It is that **the cost of starting a turn is now comparable to the cost of waiting for a keystroke to register.** Background work, exploratory chats, and short-lived tasks all stop being latency-prohibitive. The release notes report that the same optimization pass also improved TUI and desktop responsiveness, so the surface-level feel of the agent improved at every entry point.

## Live Reasoning Streams

v0.19 makes the agent's deliberation visible in real time, separate from token output. Where v0.18's MoA work showed labeled reference-model blocks *and then* the aggregator's verdict, v0.19 streams the aggregator's reasoning live as it composes the answer.

This pairs naturally with the v0.18 MoA architecture. The user now sees both:

- the structured `model-A: <output>` / `model-B: <output>` blocks from each reference, then
- the aggregator's reasoning stream leading into the final composed response.

For single-model runs, the live stream replaces the older "wait for the whole response" feel with progressive rendering. The model is no longer a black box that produces a block; it is a process the user watches.

## Smart Approvals by Default

Approval prompts now read the action and propose a sensible default scope. Rather than a generic "allow / deny" prompt, the user is offered the smallest reasonable permission that covers the action, with an obvious escape hatch to broaden it.

The release does not change the underlying approval model — destructive actions still require explicit consent — but the *friction* of routine approvals drops sharply. The pattern Hermes is using maps onto the same ergonomic principle as progressive disclosure in skills: hide the full permission surface by default, but make it one click away.

## Durable Response Delivery

v0.19 hardens the path between the agent finishing a turn and the user actually seeing it. The release notes call out "durable response delivery" — responses survive gateway restarts, network blips, and message-bus retries without being lost or duplicated.

The previous failure mode was quiet: a long turn finishes, the gateway restarts mid-flush, the user sees a generic "agent timed out" or nothing at all, with no way to recover the output. Durable delivery treats response-finalization as a journaled event rather than a fire-and-forget send. This complements the v0.18 scale-to-zero/drain lifecycle work: the gateway can now quiesce cleanly *and* the response it was about to deliver survives the quiesce.

## Secrets: Bitwarden and 1Password

The optional-secrets surface in Hermes now reaches Bitwarden and 1Password as first-class providers, alongside whatever Hermes was already able to load from `.env` files and the platform's native keychain. The pattern is the same as the existing secret-loading code paths: secrets are resolved at the moment a tool needs them, never written to disk in plaintext, and never echoed in logs or tool traces.

The significance is not "Hermes can read more secrets" but "operators can stop hand-pasting API keys into `.env` files for every new machine." A developer joining a team installs Hermes, signs in to the team's shared 1Password vault, and the same set of provider credentials become available — without any of them ever appearing in a plaintext file on the new laptop.

## Terminal Subscription Management

Hermes terminals (TUI and desktop) now expose a subscription model for long-running command output. Commands that produce more output than fits the visible buffer can be subscribed to, with the tail of the output streaming into a separate pane rather than being lost to scrollback truncation or wrapped into a single indistinguishable block.

This is small in the abstract and large in practice. Code review sessions, log-grep workflows, and multi-step build pipelines all produce more output than a screen can hold; the user previously had to choose between watching the output scroll past at terminal speed and copying it to a file by hand. The subscription model is the same fix that IDE consoles, browser devtools, and `tail -f` have used for decades, applied to the agent's own command surface.

## Desktop and TUI Polish

Beyond the headline speed, the release notes report "richer desktop and TUI performance" — faster render paths, smoother scrolling under long thinking streams, and stability improvements under sustained load. The desktop coding Project surfaces (added in v0.18) absorb several smaller polish items: the codebase sidebar, the diff/patch view, and the terminal tab manager all got iteration passes.

## Provider Updates

- **Claude Opus 5** is now available in the Hermes Agent model catalog through Nous Portal, OpenRouter, and Anthropic Direct. The MoA card ([engineering/2026-06-27-hermes-moa-virtual-models]) still names the older reference-model lineup (Haiku 4.5, Sonnet 4.5, Opus 4.6, GPT-5.5, Grok 4); operators using Opus 5 as a reference model should expect the aggregator's quality/latency profile to shift accordingly. This is a model-availability change, not a new architecture, so it does not get a standalone card.
- Three short-lived provider promos (Laguna S 2.1, Hy3, Ling-3.0-flash) ran in the v0.19 window. Logged in `memory/tweet-scans/2026-07-28-nousresearch.md`; not KB material.

## v0.19.1 (Same-Week Patch)

v0.19.1 fixes a tagged-release Docker build that pulled an unpublished WhatsApp Baileys dependency. The functional surface is identical to v0.19.0; install the patch as a normal upgrade. The release as a whole is the unit that matters for KB purposes.

## Parallel Net-New Features in the v0.19 Window

Two substantial features landed in the same window but are documented separately because each has its own deep architecture worth tracking:

- **[[engineering/hermes/2026-07-24-hermes-credential-firewall]]** — a token-substituting proxy at the network boundary for Docker sandboxes. Real keys stay outside the sandbox; the sandbox sees tokens that are valid only inside the firewall. Complements (does not replace) [[engineering/hermes/network-egress-isolation]]; together they cover both *where* a sandbox can reach and *what credentials* it sees on the wire.
- **[[engineering/hermes/2026-07-23-tldraw-offline-skill]]** — optional skill under `optional-skills/creative/tldraw-offline` that drives a running tldraw desktop whiteboard. Same architectural pattern as [[engineering/hermes/2026-06-20-hermes-blender-mcp-skill]] (official skill + MCP/desktop bridge), with one notable twist: the skill can embed a script in the saved `.tldr` so the drawing opens with working buttons and state — the closest published example of an agent producing *executable artifacts*.
- **[[engineering/hermes/2026-07-31-hermes-desktop-kanban-plugin]]** — first official Hermes **Desktop** plugin (Kanban). Establishes a new UI-shell extension surface (page, sidebar, hotkeys, status bar, backend endpoints, SDK) that is architecturally distinct from the general plugin system in [[engineering/hermes/plugins]]. The Quicksilver speed cuts are the precondition for hotkey-driven plugins feeling right; the desktop Projects surface added in v0.18 is the surface the plugin system now extends. The desktop shell is no longer just a wrapper around the agent loop — it is becoming a platform.

## Why This Release Matters

v0.18 made the agent **decide well**. v0.19 makes the agent **feel instant and survive the world.** Together they form the same trajectory: turn an agent that *could* be right and *could* be observable into one that *is* responsive and *is* durable. The remaining 2026 gap is **proactive intelligence** — agents that anticipate the next turn rather than reacting to the current one — which is a 2027 problem, not a 2026 one.

The release is also a maturity signal. v0.18 was "judgment"; v0.19 is "ergonomics, reliability, and the boring infrastructure of a long-lived agent." Both are necessary; neither is sufficient on its own.

## Related

- [[engineering/hermes/2026-07-26-hermes-v0-18-judgment-release]] — preceding release; evidence-based completion, `/learn`, `/journey`, fan-out
- [[engineering/2026-06-19-engineering-agentic-loop-pattern]] — inner loop pattern that v0.19 makes feel faster
- [[engineering/2026-06-27-hermes-moa-virtual-models]] — MoA virtual-model architecture; v0.19 makes the aggregator's reasoning visible
- [[engineering/hermes/skills]] — skills system; v0.19 improves the secrets-resolution path
- [[engineering/hermes/messaging]] — gateway architecture; v0.19 hardens durable delivery
- [[engineering/hermes/security]] — trust model; v0.19 adds credential-firewall layer
- [[engineering/hermes/network-egress-isolation]] — paired with credential firewall for full sandbox defense
- [[engineering/hermes-kanban]] — multi-agent coordination surface benefiting from TUI speed
- [[engineering/hermes/2026-07-24-hermes-credential-firewall]] — net-new security primitive in the v0.19 window
- [[engineering/hermes/2026-07-23-tldraw-offline-skill]] — net-new optional skill in the v0.19 window
- [[engineering/hermes/2026-07-31-hermes-desktop-kanban-plugin]] — net-new Desktop plugin surface in the v0.19-window (announced 2026-07-31)
