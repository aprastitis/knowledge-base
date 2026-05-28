---
type: engineering
status: active
date: 2026-05-28
summary: Hermes ships with a native MCP server catalog — built-in directory of Model Context Protocol servers for connecting agents to external tools and data sources
tags: [hermes, mcp, protocol, tool-integration]
related: [[scaffolding-for-ai-agents]], [[hermes-three-tier-memory]], [[skill-trigger-design]]
sources: [#source/nousresearch]
---

# Hermes Native MCP Catalog

Model Context Protocol (MCP) is the standard protocol for connecting AI agents to external tools and data sources. Rather than manually configuring each tool integration, MCP provides a discovery mechanism — a directory of servers the agent can connect to and use.

## What's New in Hermes

Hermes now ships with a **native MCP Catalog** — a built-in directory of MCP servers available out of the box. This means:

- **No manual setup** — servers are discoverable without configuring URLs, ports, or credentials
- **Standardized interface** — every MCP server implements the same protocol, so the agent interacts with them uniformly
- **Tool ecosystem expansion** — MCP is an open standard; the catalog can grow to include any tool that implements the protocol

## Why MCP Matters

The bottleneck for AI agents has always been tool access. An agent that can reason brilliantly but can't actually interact with your systems is limited. MCP solves this by being the **lingua franca** for agent-tool communication:

```
Agent → MCP Client → MCP Server → Your Systems (GitHub, Postgres, Slack, etc.)
```

Instead of building custom integrations for every tool, MCP provides a universal interface. The catalog in Hermes makes this a default capability rather than a DIY project.

## Implications for Agent Design

When designing agents that use Hermes:

1. **Tool access is no longer the hard part** — MCP catalog handles discovery
2. **Security still matters** — MCP servers still need credentials and permissions; the catalog doesn't bypass access control
3. **Tool selection becomes a design decision** — which MCP servers to enable determines what the agent can actually do
4. **Context management remains critical** — even with MCP, what the agent sees when it invokes a tool determines outcomes

## Relationship to Scaffolding

MCP is the **protocol layer** of scaffolding. It handles the integration interface so you can focus on:
- What tools to make available (catalog configuration)
- How to structure context around tool calls (prompt design)
- What the agent should do with the outputs (workflow design)

See [[scaffolding-for-ai-agents]] for how tooling infrastructure determines agent outcomes more than model capability.

## Sources

- [NousResearch X: Hermes MCP Catalog announcement](https://x.com/NousResearch/status/2059638198075109769)