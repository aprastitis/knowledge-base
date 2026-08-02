---
type: engineering
status: active
date: 2026-08-02
summary: Hermes's credential firewall substitutes stand-in tokens at the network boundary, so real provider keys never enter a Docker sandbox.
tags: [hermes, security, sandbox, credentials, docker, network-isolation, defense-in-depth]
related:
  - engineering/hermes/security
  - engineering/hermes/network-egress-isolation
  - engineering/hermes/2026-07-26-hermes-v0-18-judgment-release
  - engineering/hermes/2026-08-02-hermes-v0-19-quicksilver-release
sources:
  - https://x.com/NousResearch/status/2080728699100406042
---

# Hermes Credential Firewall for Docker Sandboxes

## What It Is

A token-substituting proxy that sits at the network boundary of a Hermes Docker sandbox. The sandbox's processes see stand-in tokens for every outbound call; the local proxy swaps in the real provider credentials at the egress boundary. A token that leaks out of a compromised sandbox is useless anywhere except the proxy itself.

The feature is enabled with `hermes egress setup`.

## The Problem It Solves

Hermes's existing [[engineering/hermes/security]] card is explicit about a long-standing gap:

> "in-process heuristics... are not real boundaries."
> "[credential scoping] is not containment. Any component running inside the agent process can read whatever the agent itself can read, including in-memory credentials."

The reasoning is correct, and it left a real operational hole: even with [[engineering/hermes/network-egress-isolation]] in place, the sandbox still had to *hold* the real provider key to make outbound calls. Network isolation decides *where* the sandbox can reach; it does not decide *what credentials* the sandbox sees when it reaches there. A sandbox with no network access but real keys is still a credential-leak waiting to happen; a sandbox with network access but stand-in tokens is much less of one.

The credential firewall is the first OS-amenable answer to that gap in Hermes. Substitution at the network boundary is a real boundary regardless of what the sandbox process does to itself: even a fully compromised sandbox can only exfiltrate tokens that are valid inside the firewall.

## How It Composes With Network Egress Isolation

The two layers solve different problems and stack:

```text
network egress isolation   →  "may this container reach external hosts?"   (existing)
credential firewall        →  "what does this container see on the wire?"  (new)
```

A defense-in-depth posture is to enable both:

1. The network layer decides whether a sandbox can reach the open internet, an internal proxy, or nothing at all.
2. The credential layer decides what tokens any sandbox-bound call carries.

The combination is the difference between "the sandbox can technically reach the OpenAI API but is using a token that only the egress proxy can swap to the real key" and "the sandbox can technically reach the OpenAI API and is holding the real key directly." Only the first posture survives a sandbox compromise.

## Why It Is a Real Boundary

The interesting property is that the firewall enforces the boundary at a process the sandbox does not own. The proxy runs outside the sandbox's PID namespace, outside its filesystem mount, and outside its network namespace. The sandbox can attempt to call out with any token it likes; only tokens the proxy can resolve make it through. Tokens that the proxy cannot resolve are rejected at the boundary, with a log entry the operator can audit.

This is the same architectural move that production cloud-tenant systems use for IAM-impersonation: the workload never holds the long-lived secret, only a short-lived impersonation token. The credential firewall brings that pattern to agent sandboxes.

## Operational Notes

- Install/setup: `hermes egress setup` walks the operator through creating the proxy, registering the real provider keys, and configuring the sandbox network to route outbound calls through it.
- The proxy is local — same host as the sandbox — so the trust boundary is the host network namespace, not the public internet.
- Tokens issued to a sandbox can be scoped per-sandbox, per-session, or per-task, with expiry. A leaked stand-in token is therefore time-bounded as well as scope-bounded.
- The release did not change the model API surface. Provider SDKs in the sandbox still call the same endpoints with what they believe to be real keys; the swap is invisible to the application code.

## What the Card Does Not Cover

- **Side-channel leaks** — the firewall does not stop the sandbox from emitting credentials through DNS, timing, or covert channels outside the proxied call paths. Network egress isolation is the relevant control for that.
- **In-sandbox process behavior** — the firewall does not change what the agent inside the sandbox can do with the keys it has. Treat the keys it sees as compromised-by-default and the firewall as the layer that prevents the *consequence* of that compromise.
- **Multi-host proxying** — the proxy is a single host today. A multi-host deployment needs a separate story (likely federated proxies or a managed offering).

## Why This Matters

The agent-security field has spent most of 2026 arguing about prompt-injection defenses, content moderation, and tool-capability gating. The credential firewall is a reminder that **the boring infrastructure layer matters too.** A model that produces the right answer is not useful if a compromised sandbox can drain the OpenAI bill or exfiltrate a customer's API key. Hermes's bet — that the right primitive is a network-boundary proxy, not an in-process policy — is the right bet for any system where the agent process itself is treated as untrusted.

## Related

- [[engineering/hermes/security]] — Hermes trust model; the card this implementation closes a known gap in
- [[engineering/hermes/network-egress-isolation]] — the network-isolation layer; pairs with this card for full defense
- [[engineering/hermes/2026-07-26-hermes-v0-18-judgment-release]] — preceding release; security round in the same family
- [[engineering/hermes/2026-08-02-hermes-v0-19-quicksilver-release]] — release in whose window this feature landed
