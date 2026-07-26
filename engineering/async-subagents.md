---
type: card
status: active
date: 2026-06-16
summary: The delegate tool is now non-blocking — parent agent continues while subagents run in the background
tags: [hermes, multi-agent, architecture, async, tool-change]
related: [engineering/multi-agent-orchestration-patterns, engineering/hermes/architecture]
sources: [NousResearch/Hermes scan 2026-06-16]
---

# Async Subagents

## What Changed

The `delegate_task` tool (and equivalent delegation mechanisms) in Hermes no longer blocks the parent chat. Previously, when an agent delegated to a subagent, it waited synchronously for the subagent's summary before continuing. Now the delegation is non-blocking: the agent can keep working while subagents run in the background.

## Impact

- **Before:** Synchronous delegation — agent idle until subagent completes
- **After:** Asynchronous delegation — agent continues immediately, subagent results return via background completion events

This is a material architectural change. Prior KB documentation explicitly described delegate_task as synchronous. That description is now outdated.

## Implications

- Subagent results arrive as completion events rather than direct tool responses
- The parent agent is no longer held hostage by a long-running subagent
- Enables true concurrent task execution — agent can fire off multiple subagents and handle their results as they complete
- Architectural pattern shifts from "hierarchical wait" to "fire-and-receive"

## v0.18 Background Fan-Out

Hermes v0.18.0 extended the non-blocking primitive into a batch fork-join workflow: `delegate_task` can launch multiple subagents in parallel, keep the parent chat usable, and return one consolidated completion turn after all children finish. That consolidation boundary is the key addition over single-task async delegation.

## Related

- [[engineering/multi-agent-orchestration-patterns]] — Five coordination patterns including the concurrent pattern this enables
- [[engineering/hermes/architecture]] — Core agent loop and delegation architecture
- [[engineering/hermes/2026-07-26-hermes-v0-18-judgment-release]] — Release synthesis for parallel fan-out and consolidated completion
