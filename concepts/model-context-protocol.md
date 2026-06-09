---
type: card
status: active
date: 2026-06-07
summary: "An open protocol (JSON-RPC 2.0) for connecting AI applications to external data sources and tools. The USB-C of AI integration — adopted by OpenAI, Google, Microsoft, and donated to the Linux Foundation."
tags:
  - protocol
  - tool-integration
  - mcp
  - standards
  - anthropic
  - open-standard
related:
  - engineering/hermes-native-mcp-catalog
  - engineering/scaffolding-for-ai-agents
  - concepts/context-engineering
  - engineering/agentic-coding-tools
sources:
  - https://www.anthropic.com/news/model-context-protocol
  - https://modelcontextprotocol.io/specification/2025-11-25
  - https://en.wikipedia.org/wiki/Model_Context_Protocol
---

# Model Context Protocol (MCP)

The **Model Context Protocol** is an open protocol that enables seamless integration between LLM applications and external data sources and tools. It is an open standard for connecting AI agents to external systems — the infrastructure layer that replaces point-to-point tool integrations.

In December 2025, Anthropic donated MCP to the **Agentic AI Foundation (AAIF)**, a directed fund under the Linux Foundation, co-founded by Anthropic, Block, and OpenAI. As of early 2026, it has been adopted by **OpenAI, Google, Microsoft**, and major AI platforms. It is becoming the USB-C of AI agent integration.

## The Problem MCP Solves

Before MCP, every AI application integrated with external tools via **custom, point-to-point implementations**:

- Each model provider had its own tool-calling schema
- Each tool required a custom integration
- Switching models or adding tools meant rewriting integrations
- No standard visibility into the tool layer

The result: a **proliferation of brittle, one-off integrations** that don't compose or port.

## Architecture

MCP re-uses the message-flow ideas of the **Language Server Protocol (LSP)** and is transported over **JSON-RPC 2.0**. The architecture has two sides:

### Host
The LLM application (e.g., Claude Code, an IDE, a chat interface) that initiates connections and calls tools.

### Client
The external data source or tool (MCP server) that exposes capabilities via a standardized interface.

### Three Core Primitives

| Primitive | Direction | Description |
|-----------|-----------|-------------|
| **Tools** | Model-controlled | Operations the model can invoke (read, write, execute) |
| **Resources** | App-controlled | Data the application exposes to the model (files, DB rows, API responses) |
| **Prompts** | User-controlled | Pre-built prompt templates for common workflows |

The protocol supports **two-way communication**: the AI can ask a data source questions, retrieve information, or execute an operation — and the server can respond or even prompt the AI for more details if needed.

## Why It Matters

- **Standardization**: One protocol implemented once works everywhere. Switch models or add tools without rewriting integrations.
- **Visibility**: MCP tracing (via OpenTelemetry v1.39+) gives standardized visibility into the tool layer — `mcp.method.name`, `mcp.session.id`, `mcp.protocol.version` enrich existing `execute_tool` spans.
- **Ecosystem**: A growing catalog of pre-built MCP servers means agents can connect to Slack, GitHub, databases, file systems, and more — without custom code.
- **Security**: MCP provides a structured, auditable interface for what data and actions an agent can access.

## Relationship to Hermes

[[engineering/hermes-native-mcp-catalog]] — Hermes ships with a native MCP server catalog, providing built-in Model Context Protocol servers for connecting agents to external tools and data sources. MCP is the protocol; the catalog is the implementation of pre-built servers Hermes ships with.

## Key Properties

- **Open standard** — not vendor-locked to any model provider
- **JSON-RPC 2.0 transport** — lightweight, widely supported
- **Bidirectional** — supports both tool invocation and server-initiated requests
- **Three primitive types** — tools, resources, prompts — cover the main integration patterns
- **Linux Foundation governance** — ensures long-term neutrality and evolution

---

*Related: [[engineering/hermes-native-mcp-catalog]] (Hermes's built-in MCP server catalog) | [[engineering/scaffolding-for-ai-agents]] (tooling around the model) | [[concepts/context-engineering]] (runtime context retrieval via MCP tools) | [[engineering/loop-engineering]] (MCP connectors are the fourth primitive in loop engineering — they let the loop touch real tools like Linear, Slack, and databases)*