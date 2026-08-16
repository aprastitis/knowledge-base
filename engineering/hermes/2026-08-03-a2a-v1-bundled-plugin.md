---
type: engineering
status: active
date: 2026-08-03
summary: A2A v1.0 is the Agent-to-Agent protocol. Hermes ships a bundled plugin implementing it as of v0.20.0, closing 4-year-old issue #514 and giving Hermes a standard wire format for multi-agent composition across stacks.
tags: [hermes, a2a, agent-to-agent, protocol, multi-agent, multi-agent-orchestration, plugin, standard, v0-20]
related:
  - engineering/hermes/2026-08-03-hermes-v0-20-herald-release
  - engineering/multi-agent-orchestration-patterns
  - engineering/async-subagents
  - engineering/hermes/architecture
  - engineering/hermes/plugins
  - engineering/hermes/messaging
  - engineering/hermes-kanban
  - engineering/concepts (cross-link via MOC)
  - concepts/model-context-protocol (parallel: MCP for tools, A2A for agents)
sources:
  - https://github.com/NousResearch/hermes-agent/pull/77109
  - https://github.com/NousResearch/hermes-agent/issues/514
  - https://github.com/NousResearch/hermes-agent/releases/tag/v2026.8.3
---

# A2A v1.0 — Agent-to-Agent Protocol (Hermes Bundled Plugin)

Shipped August 3, 2026 in Hermes v0.20.0, the **Agent-to-Agent (A2A) v1.0 plugin** is the first bundled plugin in Hermes that gives the agent a standard wire format for talking to *other agents*. Where [[concepts/model-context-protocol|MCP]] standardized tool-integration (Hermes-to-data-source), A2A standardizes agent-integration (Hermes-to-other-agent). The plugin implements the A2A v1.0 spec, allowing Hermes to **discover**, **talk to**, and **be driven by** other A2A-compatible agents — regardless of which framework, model, or runtime produced them.

The significance is best captured in the release notes: *"This closes issue #514 — one of the oldest open feature requests in the repo."* Issue #514 had been open for four years.

## What A2A Actually Is

A2A is a wire protocol. Like HTTP for the web or MCP for tools, it defines:

1. **Discovery** — how an agent announces what it can do. A2A "agent cards" describe the agent's capabilities, supported input/output modalities, authentication requirements, and version.
2. **Communication** — how agents exchange messages. A2A messages are structured (not free-form text) and support streaming, attachments, and partial responses.
3. **Task lifecycle** — how long-running tasks are tracked, paused, resumed, and completed across agent boundaries.
4. **Authentication** — how agents prove identity and authority to each other (OAuth-style flows, mTLS, API keys).
5. **Versioning** — how protocol versions are negotiated and capability degradation is signaled.

What A2A is *not*: it is not a framework, an orchestration engine, or a model. It is a contract. An agent that implements A2A can be plugged into any other A2A system without bespoke integration code.

The closest analog in the existing KB is the relationship between MCP and the [[engineering/hermes-native-mcp-catalog|Hermes native MCP catalog]]: MCP is the wire protocol, the catalog is the concrete implementation. The same shape applies here — A2A is the wire protocol, this bundled plugin is the concrete Hermes implementation.

## What the Bundled Plugin Does in Hermes

The Hermes-side plugin ([PR #77109](https://github.com/NousResearch/hermes-agent/pull/77109)) ships in `bundled-plugins/a2a/` and provides:

- **A2A server mode** — Hermes advertises its capabilities via an A2A agent card and accepts A2A requests from other agents.
- **A2A client mode** — Hermes can discover and call other A2A agents as if they were local tools, routing the request through the standard Hermes request pipeline (so it benefits from streaming, retries, approval gates, observability hooks).
- **Capability mapping** — exposes existing Hermes capabilities (skills, tools, subagents, the kanban board, the gateway adapters) as A2A-discoverable services.
- **Authentication bridge** — translates between Hermes's existing auth surface (Portal credentials, gateway tokens, profile-locked secrets) and A2A's auth requirements.
- **Streaming + attachments** — inherits the v0.20 streaming TTS/voice improvements, so A2A-mediated responses can stream end-to-end without buffering.

The plugin slots into the existing [[engineering/hermes/plugins]] orchestrator pattern. From the operator's perspective, A2A is just another plugin in the catalog; the magic is in the protocol layer beneath.

## Why This Closes a 4-Year-Old Issue

Issue #514 (filed in 2022) asked, in various forms over the years: *"How does a Hermes agent talk to a non-Hermes agent?"* Pre-v0.20 answers were all bespoke: HTTP scraping, custom JSON contracts, screen-scraping CLIs, hand-rolled tool wrappers. Each integration took days of work, broke on every agent-side update, and required the operator to maintain a per-target compatibility layer.

The A2A plugin collapses all of this to "register the other agent's A2A endpoint, get a typed interface, route calls through the standard pipeline." The integration time drops from days to minutes, and the maintenance burden shifts to the protocol maintainers rather than the operator.

The four-year gap between issue and ship is itself informative. A2A was not ready until v0.20 because the underlying Hermes agent had to be capable enough to participate in cross-agent conversations — v0.18 gave it verification contracts, v0.19 gave it durable delivery and live reasoning, v0.20 gave it voice and streaming. An agent that cannot reliably complete a turn, stream a response, or verify its own output is not ready to expose itself to *another* agent that depends on those properties.

## How A2A Complements the Existing Multi-Agent Surfaces

Hermes already had three ways to compose multiple agents. A2A is the fourth, and the first that crosses process and framework boundaries:

| Surface | Scope | Lifetime | Topology |
|---|---|---|---|
| [[engineering/async-subagents\|Async subagents]] (`delegate_task`) | In-process, same model | Per-turn | Parent → child, fire-and-forget |
| [[engineering/hermes-kanban\|Hermes Kanban]] | Cross-session, same Hermes | Persistent (SQLite) | Peer-to-peer, board-mediated |
| [[engineering/hermes/cronjob\|Cron jobs]] | Cross-session, same Hermes | Scheduled | Owner → worker |
| **A2A plugin** | Cross-process, cross-framework | Persistent (A2A task ids) | Any-to-any, protocol-mediated |

The four surfaces compose. A2A can call an agent that *itself* has a Kanban board; an async subagent can wrap an A2A call to a remote agent; a cron job can fire an A2A-mediated task every hour. The pattern in [[engineering/multi-agent-orchestration-patterns]] (sequential, concurrent, group chat, handoff, magentic) is *topology-agnostic* — it now has one more wire to flow over.

## Concrete Operational Patterns

A few patterns become much cleaner with A2A available:

**Heterogeneous multi-agent pipelines.** A Hermes orchestrator can pull a research summary from a Claude-Code agent, send it to a Fable-5 agent for fact-checking ([[engineering/hermes/2026-08-03-grounded-citations-skill]]), and route the result to a Codex agent for code generation — all through A2A, all in one parent turn.

**Provider-agnostic delegation.** The same Hermes profile can delegate to whichever agent is best at the sub-task, regardless of model or framework. A2A hides the implementation behind the protocol.

**Cross-team agent discovery.** Team A's published A2A agent cards can be discovered and called by Team B's Hermes instances, with the auth and rate-limit policy enforced at the protocol layer instead of in bespoke integration code.

**Agent-as-a-service composition.** A team that has invested in a specialized agent (a code-review agent, a security-audit agent, a research agent) can publish it via A2A and let other teams consume it without granting them access to the underlying Hermes install.

## What A2A Does Not Solve

Three things worth naming so they are not misremembered:

1. **A2A is not an agent-runtime standard.** The agents on both ends still run their own loops with their own model context. A2A is the conversation format, not the execution format. The agent doing the work decides how; A2A just decides what gets said.
2. **A2A is not yet an industry standard.** It is a protocol, and Hermes ships an implementation of it. Adoption by other agent frameworks is the next 12–24 months' question, not a given. The MCP analogy is instructive: MCP became an industry standard because Anthropic donated it to a foundation and OpenAI/Google/Microsoft adopted it within months. A2A's adoption trajectory will depend on similar governance moves.
3. **A2A does not replace Hermes's in-process delegation.** Async subagents are still the right tool for in-process parallelism. A2A is for when the other agent lives somewhere you cannot or should not run a Hermes process — a vendor's hosted agent, a teammate's local install, a CI runner.

## Implementation Notes for Operators

Three things to know if you are wiring A2A in production:

1. **Authentication is per-agent, not global.** Each A2A endpoint declares its own auth requirements. The Hermes plugin handles the translation, but operators should expect to manage credentials per-remote-agent (the [[engineering/hermes/2026-07-24-hermes-credential-firewall|credential firewall]] primitive from the v0.19 window is the right substrate).
2. **Streaming behavior depends on the remote agent.** If the remote agent does not support streaming, Hermes falls back to buffered response. The streaming surface in v0.20 is conditional on the protocol endpoint, not on Hermes.
3. **Approvals still apply.** A2A-mediated tool calls go through the same [[engineering/hermes/2026-08-03-hermes-v0-20-herald-release#smart-approvals-grow-up|smart approvals]] gate as local tool calls. An agent that wants to run a destructive operation through another agent still needs the user to approve it.

## Why This Card Matters in the KB

Two reasons this is a standalone card rather than a section in the v0.20 umbrella:

1. **A2A is the protocol, not the release.** The bundled plugin is part of v0.20.0; the protocol is a durable cross-stack standard that other agent frameworks will adopt. Tracking the protocol separately from the release makes the card useful for non-Hermes contexts.
2. **It closes a four-year-old gap.** Issue #514's age makes this a *legitimization* event for Hermes-as-a-platform. Operators who delayed multi-agent work because the cross-agent story was unproven now have a concrete answer.

The combination of A2A + Cloud ([[engineering/hermes/2026-08-03-hermes-cloud-launch]]) + signed webhooks ([[engineering/hermes/2026-08-03-outbound-webhooks]]) is what makes v0.20 the "Herald" release in more than name: Hermes is now *publishable* (it exposes an A2A agent card), *consumable* (it can call other A2A agents), and *observable* (it pushes signed events to anywhere that listens). That is the shape of a participant in a multi-agent system, not just a tool that happens to have plugins.

## Related

- [[engineering/hermes/2026-08-03-hermes-v0-20-herald-release]] — release context; A2A is one of the four spine capabilities (with voice, webhooks, grounded research)
- [[engineering/multi-agent-orchestration-patterns]] — five multi-agent topologies; A2A is the wire that carries any of them across process boundaries
- [[engineering/async-subagents]] — in-process delegation primitive; pairs with A2A for cross-process composition
- [[engineering/hermes/architecture]] — agent loop internals; A2A-mediated calls flow through the standard pipeline
- [[engineering/hermes/plugins]] — plugin orchestrator pattern; A2A is implemented as a bundled plugin in this surface
- [[engineering/hermes/messaging]] — gateway platform adapters; A2A can expose gateway-mediated conversations to other agents
- [[engineering/hermes-kanban]] — Kanban board; A2A agents can call kanban operations on a peer Hermes instance
- [[concepts/model-context-protocol]] — parallel protocol for tool integration (Hermes-to-data-source); A2A is the agent-level (Hermes-to-agent) analog
- [GitHub PR #77109](https://github.com/NousResearch/hermes-agent/pull/77109) — implementation
- [GitHub issue #514](https://github.com/NousResearch/hermes-agent/issues/514) — the 4-year-old feature request
