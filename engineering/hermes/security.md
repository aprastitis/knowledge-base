---
type: card
status: active
date: 2026-06-12
summary: Trust model, OS-level isolation as the only boundary, in-process heuristics, plugin trust, deployment hardening
tags: [hermes, security, isolation, trust-model]
sources:
  - https://github.com/NousResearch/hermes-agent/blob/main/SECURITY.md
related:
  - engineering/hermes/README
  - engineering/hermes/architecture
  - engineering/hermes/plugins
---

# Hermes Security Policy

## Trust Model

Hermes Agent is a single-tenant personal agent. Its posture is layered, and the layers are not equally load-bearing.

### The Boundary: OS-Level Isolation

**The only security boundary against an adversarial LLM is the operating system.** Nothing inside the agent process constitutes containment — not the approval gate, not output redaction, not any pattern scanner, not any tool allowlist.

Hermes supports two OS-level isolation postures:

**Terminal-backend isolation:** A non-default terminal backend runs LLM-emitted shell commands inside a container, remote host, or cloud sandbox. The file tools (`read_file`, `write_file`, `patch`) also run through this backend since they're implemented on top of the shell contract.

**Whole-process wrapping:** Runs the entire agent process tree inside a sandbox (Docker or NVIDIA OpenShell). Every code path — shell, code-execution, MCP, file tools, plugins, hooks, skill loading — is subject to the same filesystem, network, process, and inference policy.

### In-Process Heuristics (Not Boundaries)

These components screen or warn about LLM behavior — they are useful but **not** security boundaries:

- **Approval gate** — detects common destructive shell patterns and prompts before execution. Shell is Turing-complete; a denylist over shell strings is structurally incomplete.
- **Output redaction** — strips secret-like patterns from display. A motivated output producer will defeat it.
- **Skills Guard** — scans installable skill content for injection patterns. The boundary for third-party skills is operator review before install.

### Plugin Trust Model

Plugins load into the agent process and run with full agent privileges: they can read the same credentials, call the same tools, register the same hooks. The boundary for third-party plugins is operator review before install — the same rule as skills.

### Credential Scoping

Hermes filters the environment it passes to lower-trust in-process components: shell subprocesses, MCP subprocesses, and the code-execution child. Credentials like provider API keys and gateway tokens are stripped by default; variables explicitly declared by the operator or by a loaded skill are passed through.

This is not containment. Any component running inside the agent process can read whatever the agent itself can read, including in-memory credentials.

### External Surfaces

An **external surface** is any channel outside the local agent process through which a caller can dispatch agent work, resolve approvals, or receive agent output:

- **Gateway platform adapters** — Telegram, Discord, Slack, email, SMS, etc.
- **Network-exposed HTTP surfaces** — API server adapter, dashboard plugin, kanban plugin HTTP endpoints
- **Editor / IDE adapters** — ACP adapter, VS Code / Zed / JetBrains integration
- **The TUI gateway** — JSON-RPC backend for the Ink terminal UI

**Uniform rules:**
1. Authorization is required at every surface that crosses a trust boundary
2. An allowlist is required for every enabled network-exposed adapter
3. Session identifiers are routing handles, not authorization boundaries
4. Within the authorized set, all callers are equally trusted
5. Binding a local-only surface to a non-loopback interface is a break-glass operator decision

## Scope

### In Scope
- Escape from a declared OS-level isolation posture
- Unauthorized external-surface access
- Credential exfiltration via a mechanism that should have prevented it
- Trust-model documentation violations (code behaving contrary to stated policy)

### Out of Scope (Not Security Vulnerabilities)
- **Bypasses of in-process heuristics** — approval-gate regex bypasses, redaction bypasses, Skills Guard pattern bypasses
- **Prompt injection per se** — getting the LLM to emit unusual output without a chained outcome
- **Consequences of a chosen isolation posture** — shell or file tools reaching host state under the local backend
- **Documented break-glass settings** — `--insecure` flags, disabled approvals, local backend in production
- **Community-contributed skills and plugins** — third-party code is in the operator's review surface
- **Public exposure without external controls** — gateway or API to the public internet without VPN or firewall

## Deployment Hardening

1. **Match isolation to trust of ingested content** — the single most important hardening decision
2. **Run as non-root user** — the supplied container image does this by default
3. **Keep credentials in the operator credential file with tight permissions** — never in config.yaml, never in version control
4. **Do not expose gateway or API to the public internet without VPN or Tailscale**
5. **Configure a caller allowlist for every network-exposed adapter**
6. **Review third-party skills and plugins before install** — read the Python and scripts, not just SKILL.md
7. **Use dependency pinning policy** — PyPI packages need `>=floor,<next_major` bounds

## Supply Chain Hardening

After the litellm compromise (March 2026) and Mini Shai-Hulud worm campaign (May 2026):

| Source type | Treatment |
|-------------|-----------|
| PyPI package | `>=floor,<next_major` |
| Git URL | Full commit SHA |
| GitHub Actions | Full commit SHA + version comment |
| CI-only pip | `==exact` |

## Disclosure

- **Coordinated disclosure window:** 90 days from report, or until a fix is released
- **Channel:** GHSA thread or security@nousresearch.com
- **Credit:** reporters credited in release notes unless anonymity requested

Report privately via [GitHub Security Advisories](https://github.com/NousResearch/hermes-agent/security/advisories/new) or **security@nousresearch.com**. Do not open public issues for security vulnerabilities.