---
type: engineering
status: active
date: 2026-08-02
summary: Official optional Hermes skill that drives a running tldraw desktop whiteboard over MCP, and can embed scripts that make saved .tldr files self-executing.
tags: [hermes, skills, mcp, tldraw, desktop-automation, executable-artifact, optional-skill]
related:
  - engineering/hermes/skills
  - engineering/hermes/2026-06-20-hermes-blender-mcp-skill
  - engineering/hermes-native-mcp-catalog
  - concepts/model-context-protocol
  - engineering/2026-06-19-engineering-agentic-loop-pattern
  - engineering/hermes/2026-08-02-hermes-v0-19-quicksilver-release
sources:
  - https://x.com/NousResearch/status/2080319476243861569
---

# Hermes tldraw-offline Skill

## What It Is

An official optional skill under `optional-skills/creative/tldraw-offline` that drives a running instance of [tldraw](https://www.tldraw.com/)'s offline desktop whiteboard. The skill reads the user's open canvas, edits it live, and — uniquely among the creative optional skills — can **embed a script in the saved `.tldr` file so the drawing opens with working buttons and state.**

Install with:

```
hermes skills install optional-skills/creative/tldraw-offline
```

## Same Pattern as the Blender MCP Skill

The architecture mirrors [[engineering/hermes/2026-06-20-hermes-blender-mcp-skill]]:

- ships under `optional-skills/creative/...` in the official catalog,
- bridges Hermes to a running desktop application (tldraw instead of Blender),
- uses MCP over a local transport to issue commands and query state,
- falls into the same gather → act → verify loop documented in [[engineering/2026-06-19-engineering-agentic-loop-pattern]].

The Blender MCP card remains the canonical worked example for the "creative optional skill that drives a desktop app" pattern; this card is the second instance, paralleling the first.

## The Twist: Executable Artifacts

What the tldraw skill adds that no other optional skill in the catalog currently does is **self-executing artifacts.** The skill can write a small script into the saved `.tldr` file alongside the shape data. When a viewer — human or another agent — opens the file later, the script runs, the buttons work, and the diagram has state. The drawing is no longer a static image; it is a small program that renders itself.

This is the closest published example of an agent producing *executable artifacts*: files whose state evolves when opened, not files whose state is fixed at write time. The pattern generalizes beyond tldraw — any document format that supports embedded scripting (Notion, Figma via plugins, Excalidraw with custom embeds, HTML/SVG with `<script>`) could in principle host a similar skill — but tldraw is the first shipped case.

## Why It Matters

Three reasons this skill is interesting beyond the feature itself:

1. **The artifact is the deliverable.** In the Blender MCP card, the deliverable is whatever state the user wants in the `.blend` file — geometry, materials, renders. In the tldraw card, the deliverable is the *file itself,* and the file carries its own execution semantics. A diagram that opens with working controls is qualitatively different from a diagram that opens as shapes.

2. **The handoff is human-readable and agent-readable.** A `.tldr` file with embedded script is something a human can open, inspect, edit by hand, and run. A binary format like `.blend` is not. The tldraw skill therefore composes well with mixed human/agent workflows: the agent produces the file, the human reviews and tweaks, the file still works.

3. **It generalizes the "skill is a procedure, not just a prompt" idea.** The tldraw skill's procedure is *not* "write shapes that look like this." It is "write a file that, when opened, performs this interaction." That is a larger unit of automation than the rest of the optional catalog currently supports.

## Operational Notes

- **Prerequisite:** a running tldraw instance on the host. The skill assumes the desktop app is already open and reachable on the local MCP transport.
- **Command surface:** reads the current canvas (shapes, layers, selection), creates/edits/deletes shapes, and writes embedded script blocks.
- **Verification:** the skill's `## Verification` step (per [[engineering/hermes/skills]] authoring standards) is "open the saved `.tldr` in a fresh tldraw window and confirm the embedded script executes."
- **Security:** the embedded script runs in the tldraw sandbox at file-open time, not in the Hermes agent process. That keeps the security boundary at the desktop app rather than at the agent — the same posture Blender MCP uses. A user opening a malicious `.tldr` runs the malicious code in tldraw's own JS sandbox, not in Hermes.

## When To Use It

- **Diagrams with state machines, flow charts, or interactive decision trees** where the viewer should be able to click through paths.
- **Mockups with clickable buttons** that show interaction without needing a frontend.
- **Teaching material** where the student manipulates the diagram to see consequences.
- **Agent-to-agent handoff** where the producing agent's intent is preserved as working state, not as documentation.

## When Not To Use It

- **Static publication material** (PDF, slide deck, screenshot) — overkill, the script never runs.
- **Sensitive material** where the embedded script could leak information at file-open time. The script is plain JS, runs in tldraw's sandbox, but it is still code in a document you may want to share widely.
- **Collaborative real-time editing** — tldraw's multiplayer story is independent of Hermes; the skill works on a single canvas at a time.

## See Also

- [[engineering/hermes/2026-06-20-hermes-blender-mcp-skill]] — the canonical worked example of this pattern
- [[engineering/hermes/skills]] — the skills system; the tldraw skill follows the same SKILL.md authoring standards
- [[engineering/hermes-native-mcp-catalog]] — the catalog this skill is published in
- [[concepts/model-context-protocol]] — the underlying protocol
- [[engineering/hermes/2026-08-02-hermes-v0-19-quicksilver-release]] — release in whose window this skill shipped
