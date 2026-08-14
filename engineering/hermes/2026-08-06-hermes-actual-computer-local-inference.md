---
type: engineering
status: active
date: 2026-08-06
summary: Hermes Agent pairs with Actual Computer's local inference stack out of the box — user's own hardware as the inference backend, with low CPU impact and multi-platform portability. Part of Nous's "Hermes is harness, not model" positioning.
tags: [hermes, inference, local-inference, actual-computer, harness-architecture, hardware, user-controlled-compute]
related:
  - engineering/hermes/architecture
  - engineering/hermes/2026-07-23-tldraw-offline-skill
  - engineering/hermes/2026-07-31-hermes-desktop-kanban-plugin
  - engineering/hermes/2026-06-20-hermes-blender-mcp-skill
  - engineering/2026-06-19-engineering-agentic-loop-pattern
  - engineering/hermes/plugins
sources:
  - https://x.com/actualinc/status/2085172429895172136
  - https://x.com/NousResearch/status/2085184069302935999
---

# Hermes Agent × Actual Computer — Local Inference Integration

## What It Is

Actual Computer (@actualinc) is a local inference stack — software that runs large-model inference on the user's own hardware. As of Aug 6, 2026, Actual ships with **out-of-the-box Hermes Agent support**: a Hermes install can route inference through Actual and use the user's personal compute capacity from anywhere Hermes runs.

Per Actual's announcement:

> "The world's best harness, Hermes Agent by @NousResearch now works with Actual out of the box.
> Hermes is the most effective harness for every model and thats why we're thrilled to use it natively.
> Hermes agent users can now use their personal inference capacity from anywhere."

Nous Research amplified it with a frame:

> "Actual is a local inference stack optimized to let you utilize your personal compute.
> With its low CPU impact while inferencing and multi-platform portability, it's a great pair for your local Hermes Agent usage."

## Why This Matters

This is a thinner announcement than the Browser Use mode or portable profiles cards. Two reasons.

**1. It reinforces Nous's "Hermes is harness, not model" positioning.**

Hermes already supports multiple inference providers through its plugin system ([[engineering/hermes/plugins]] — see model-providers). Actual Computer is the same shape — another inference backend, available through the standard provider abstraction. The interesting thing is not "Hermes can call Actual" (it could always call any OpenAI-compatible endpoint); the interesting thing is **Nous's framing that this is a first-class pairing worth announcing.** A third-party local inference stack that ships integration with Hermes out of the box is a signal of ecosystem gravity.

**2. It is part of a broader pattern of "user's own machine as the agent's substrate."**

Across the recent KB, three cards already document the same pattern in different shapes:

- [[engineering/hermes/2026-07-23-tldraw-offline-skill]] — Hermes drives a running tldraw desktop whiteboard (local app, local execution)
- [[engineering/hermes/2026-07-31-hermes-desktop-kanban-plugin]] — first official Hermes Desktop plugin (local UI shell, local state)
- [[engineering/hermes/2026-06-20-hermes-blender-mcp-skill]] — Hermes drives a running Blender instance (local app, local execution)

The Actual Computer card is the **compute substrate** instance of that pattern. Tldraw / Blender / Desktop Kanban are GUI substrates; Actual is the inference substrate. Same posture: the user's own hardware is what the agent runs on, the user's own data does not leave the user's own machine, the user's own CPU/GPU is the resource being consumed.

The whole pattern fits inside the [[engineering/hermes/security]] posture: OS-level isolation as the only real boundary, the user's own machine as the trusted execution environment, network egress either blocked or proxied. Actual adds another primitive — the model weights themselves can be local, not just the agent process.

## When Actual Is the Right Choice

- **Privacy-sensitive workloads.** The model never calls out to a hosted API. For work where neither prompts nor completions should leave the machine, a local inference stack is the only option.
- **Cost ceilings.** After a certain spend on hosted inference, owning the hardware becomes the cheaper long-run answer. Local inference lets the user convert capex into a one-time cost and amortize.
- **Offline operation.** A Hermes + Actual install works without internet (assuming the model is downloaded). For deployment scenarios with intermittent connectivity (field work, restricted networks), this is the path.
- **Latency.** Eliminating the network round-trip to a hosted provider cuts latency by tens to hundreds of milliseconds per turn. For tight inner-loop work, this compounds.
- **Hardware the user already owns.** Modern consumer GPUs (and increasingly NPUs) can run usable models for agent work. Actual's "low CPU impact" framing targets the case where the user's hardware is doing double-duty.

## When Actual Is Not the Right Choice

- **Largest / frontier models.** Local inference runs whatever fits on the user's hardware. Frontier-scale models do not. For the hardest reasoning tasks, hosted inference wins.
- **Bursty workload.** A hosted provider scales elastically; the user's hardware does not. If the workload is bursty with quiet periods, paying per token is cheaper than amortizing hardware across lulls.
- **No local GPU.** Actual is most useful when there is local compute to use. A laptop without a discrete GPU gets limited benefit.

## Operational Notes

- **Activation.** Add Actual as a model provider in Hermes's provider configuration (model-providers plugin), point it at the Actual endpoint, pick a model. The Hermes side treats it like any other OpenAI-compatible provider.
- **Setup cost.** The user has to install Actual, download model weights (one-time), and configure the provider. After that, the install is self-contained.
- **Multi-platform.** Per the announcement, Actual is multi-platform portable. Hermes already runs on macOS / Linux / Windows; the integration does not add platform constraints beyond what Hermes already has.
- **CPU impact.** "Low CPU impact while inferencing" is Actual's claim. This matters because Hermes itself may want to use the CPU for tool execution, browser automation, etc. — Actual's design specifically preserves CPU headroom for those workloads.

## Why This Card Exists (And Why It Is Thin)

The announcement is one tweet from Actual and one quote-tweet from Nous. There is no deep architecture to track, no PR number to reference, no plugin surface to enumerate. The card exists because:

1. **The "user's own machine as the agent's substrate" pattern is now explicit at the inference layer.** It is the third instance of the pattern (tldraw, Desktop Kanban, Blender) plus this one. Worth a single card that names the pattern, even if thinly.
2. **A future expand is cheap.** If Actual Computer usage materializes — i.e., Andreas actually wires Hermes up to it, or Nous ships more Hermes × Actual features — this card is the natural place to expand. If it does not, the card is cheap to maintain and stays useful as a pointer.
3. **It pairs with [[engineering/hermes/architecture]]'s plugin system** to make the "model-providers is one of the four plugin categories" point concrete with a specific named integration.

If a more substantive integration lands (Actual → Hermes plugin instead of bare OpenAI-compat hookup), this card will be expanded. Until then, treat it as a pattern marker, not a feature deep-dive.

## Related

- [[engineering/hermes/architecture]] — Hermes's model-provider plugin abstraction is what makes Actual integration a config switch
- [[engineering/hermes/2026-07-23-tldraw-offline-skill]] — sibling pattern (local app as external substrate)
- [[engineering/hermes/2026-07-31-hermes-desktop-kanban-plugin]] — sibling pattern (local UI shell)
- [[engineering/hermes/2026-06-20-hermes-blender-mcp-skill]] — sibling pattern (local app as external substrate)
- [[engineering/2026-06-19-engineering-agentic-loop-pattern]] — local inference changes the cost-per-accepted-change calculation in the inner loop
- [[engineering/hermes/plugins]] — model-providers is one of the four plugin categories
