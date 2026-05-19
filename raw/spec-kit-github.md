# Spec-Driven Development — Raw Source

**Source:** https://github.com/github/spec-kit
**Fetched:** 2026-05-19
**URL:** https://github.com/github/spec-kit

## Overview

An open source toolkit that allows you to focus on product scenarios and predictable outcomes instead of vibe coding every piece from scratch.

Requires `uv` for package management. Install via:
```
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git@vX.Y.Z
```

## What is Spec-Driven Development?

Spec-Driven Development flips the script on traditional software development. For decades, code has been king — specifications were just scaffolding we built and discarded once the "real work" of coding began. Spec-Driven Development changes this: specifications become executable, directly generating working implementations rather than just guiding them.

## Quick Start

```bash
specify init my-project --integration copilot
cd my-project
/speckit.constitution  # Create governing principles
/speckit.specify       # Describe what to build
/speckit.plan          # Provide tech stack
/speckit.tasks         # Generate task list
/speckit.implement     # Execute
```

## Key Commands

| Command | Agent Skill | Description |
|---------|-------------|-------------|
| `/speckit.constitution` | `speckit-constitution` | Create governing principles |
| `/speckit.specify` | `speckit-specify` | Define requirements and user stories |
| `/speckit.plan` | `speckit-plan` | Create technical implementation plans |
| `/speckit.tasks` | `speckit-tasks` | Generate actionable task lists |
| `/speckit.taskstoissues` | `speckit-taskstoissues` | Convert tasks to GitHub issues |
| `/speckit.implement` | `speckit-implement` | Execute all tasks |
| `/speckit.clarify` | `speckit-clarify` | Clarify underspecified areas |
| `/speckit.analyze` | `speckit-analyze` | Cross-artifact consistency analysis |
| `/speckit.checklist` | `speckit-checklist` | Generate quality checklists |

## Development Phases

- **0-to-1 (Greenfield):** Start with high-level requirements, generate specs, plan, build
- **Creative Exploration:** Explore diverse solutions with multiple tech stacks
- **Iterative Enhancement (Brownfield):** Add features iteratively, modernize legacy

## Experimental Goals

- Validate SDD is not tied to specific technologies or frameworks
- Demonstrate mission-critical application development
- Incorporate organizational constraints (cloud providers, tech stacks)
- Support enterprise design systems and compliance requirements
- Support various development approaches (vibe-coding to AI-native)
- Validate parallel implementation exploration
- Provide robust iterative feature development workflows

## Prerequisites

- Linux/macOS/Windows
- Supported AI coding agent
- `uv` for package management (or `pipx`)
- Python 3.11+
- Git

## Extensions & Presets System

**Priority order (highest to lowest):**
1. Project-local overrides: `.specify/templates/overrides/`
2. Presets: `.specify/presets/templates/`
3. Extensions: `.specify/extensions/templates/`
4. Core defaults: `.specify/templates/`

**Use extensions when:** Adding new commands or workflows, integrating external tools, adding new development phases.

**Use presets when:** Customizing spec/plan/task formats, enforcing organizational standards, changing terminology or templates.

## Documentation

- Complete SDD methodology: `/spec-driven.md`
- CLI Reference: `spec-kit.github.io/spec-kit/`
- Supported integrations: 30+ AI coding agents
- Community extensions, presets, walkthroughs, and friends available