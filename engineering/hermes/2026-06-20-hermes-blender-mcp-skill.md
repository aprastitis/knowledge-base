---
type: engineering
status: active
date: 2026-06-20
summary: Optional Hermes skill (creative/blender-mcp) that lets the agent drive a running Blender instance over TCP — execute arbitrary bpy Python, query scene state, capture viewport screenshots
tags: [hermes, skills, mcp, blender, creative, desktop-automation]
related:
  - engineering/hermes/skills
  - engineering/hermes-native-mcp-catalog
  - concepts/model-context-protocol
sources:
  - https://x.com/NousResearch/status/2068099500040728918
  - https://hermes-agent.nousresearch.com/docs/user-guide/skills/optional/creative/creative-blender-mcp
  - NousResearch tweet scan 2026-06-20
---

# Hermes Blender MCP Skill

The first concrete example we have of a Hermes *creative* optional skill that exposes **real desktop-app control via MCP**. Wraps the `ahujasid/blender-mcp` addon and lets the agent drive a running Blender instance over a plain TCP socket.

Source: teaser reply from @NousResearch on 2026-06-19 ("@iYaT_TA I've got some news") with a link to the official docs. The skill itself is not new — it lives in the optional catalog at v1.0.0 — but the tweet surfaced it as a flagship example of how creative skills integrate with desktop apps.

## Install

```bash
hermes skills install official/creative/blender-mcp
```

Skill metadata:
- **author:** alireza78a
- **version:** 1.0.0
- **category:** creative (optional — not loaded by default)

## One-Time Blender Setup

1. Install the `ahujasid/blender-mcp` addon (zip from upstream repo → Blender → Edit → Preferences → Add-ons → Install)
2. Enable **"Interface: Blender MCP"** in the Add-ons panel
3. Open the 3D viewport sidebar (N key) → find the **BlenderMCP** tab → click **"Start Server"**

The addon listens on `localhost:9876` (TCP). Verify connectivity from the host:

```bash
nc -z -w2 localhost 9876
```

## Commands Exposed

| Command | Purpose |
|---------|---------|
| `execute_code` | Run arbitrary `bpy` Python in the live Blender session |
| `get_scene_info` | List objects in the current scene |
| `get_object_info` | Detailed properties of a single object (transform, modifiers, materials) |
| `get_viewport_screenshot` | Capture the current viewport as a PNG |

`execute_code` is the power tool — the agent can issue any `bpy` script (model creation, modifier application, material assignment, batch ops). The three query commands are for state inspection and verification.

## Wire Format

- **Plain UTF-8 JSON over TCP** — no length prefix, no framing
- Each request is a single newline-delimited JSON object
- Responses are JSON too
- Connection is per-call — the addon handles the handshake; Hermes opens a fresh socket per command

This is intentionally minimal: same pattern as the upstream `ahujasid/blender-mcp` addon, no protocol upgrades. Trades robustness (no streaming, no binary payloads) for simplicity.

## Example Workflow

A typical agent loop might be:

1. `get_scene_info` → discover what's in the scene
2. `execute_code("bpy.ops.mesh.primitive_cube_add()")` → add geometry
3. `execute_code("bpy.context.active_object.location = (2, 0, 0)")` → position it
4. `get_viewport_screenshot` → verify visually

This matches the canonical Hermes inner-loop pattern from `engineering/2026-06-19-engineering-agentic-loop-pattern` (gather → act → verify → repeat).

## Security / Safety Notes

`execute_code` runs **arbitrary Python in the Blender process**. Implications:

- **Sandbox = host OS.** Anything the Blender process can do, the agent can do. No isolation beyond standard desktop process permissions.
- **No approval gate.** The skill as-shipped has no human-in-the-loop per command. If your Hermes session can invoke it, it runs whatever the model emits.
- **Read `bpy` output carefully.** Long scripts can hang Blender or produce surprising side effects on `.blend` files. Always save before letting an agent iterate on real work.

This is fine for sandbox/scratch `.blend` files and exploratory workflows. Treat it as **unsafe** for production scenes, asset libraries, or any Blender instance that has access to anything you wouldn't hand to the model directly.

## Why It Matters

Three things make Blender MCP a useful reference skill:

1. **Concrete desktop-app bridge.** Most optional skills are CLI/API wrappers. This one drives a long-running GUI application — the agent doesn't shell out, it talks to a running process over a socket. Pattern generalizes to other GUI apps with a Python or RPC layer (GIMP, Krita, Houdini, etc.).
2. **Verify-by-screenshot.** The `get_viewport_screenshot` command enables visual verification — close the loop without asking the human to open Blender. Pairs naturally with image understanding skills for self-checking visual work.
3. **Stateful workflow.** Unlike one-shot CLI tools, the Blender session persists across commands. The agent can build up a scene over many turns rather than re-deriving state every call.

## Related

- [[engineering/hermes/skills]] — SKILL.md format, optional skill install path, skill vs tool decision criteria
- [[engineering/hermes-native-mcp-catalog]] — Hermes's built-in MCP server catalog; Blender MCP rides on the same protocol
- [[concepts/model-context-protocol]] — MCP as the underlying protocol (JSON-RPC 2.0 in the spec, plain JSON over TCP in this addon)
- [[engineering/2026-06-19-engineering-agentic-loop-pattern]] — The gather → act → verify loop Blender MCP workflows naturally fall into

## Source Notes

- **Caveat:** the tweet was a brief teaser reply, not a release announcement. The skill is pre-existing at v1.0.0 in the optional catalog. We ingest it because (a) it fills a KB gap (no concrete creative-skill catalog entry yet), and (b) it's the most explicit example we have of a creative optional skill that bridges to a real desktop app.
- **Upstream:** the addon is `ahujasid/blender-mcp` — Hermes wraps it rather than reimplementing. Worth knowing where the actual TCP/blender code lives if you need to debug or extend.
