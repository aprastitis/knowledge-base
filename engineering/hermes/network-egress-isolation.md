---
type: card
status: active
date: 2026-06-12
summary: Docker network segmentation — internal (no internet) + egress (proxy allowlist) networks for prompt injection defense
tags: [hermes, docker, network-isolation, security, egress-proxy]
sources:
  - https://github.com/NousResearch/hermes-agent/blob/main/docs/security/network-egress-isolation.md
related:
  - engineering/hermes/security
  - engineering/hermes/architecture
---

# Network Egress Isolation for Docker Deployments

When running Hermes inside Docker, the default `network_mode: host` gives the agent process unrestricted outbound network access. Network egress isolation segments traffic so the agent core can only reach services it needs, blocking arbitrary outbound connections.

**Primary defense:** Prompt injection attacks that attempt to exfiltrate data via `curl`, `wget`, or raw HTTP from tool-generated shell commands.

## Architecture

```
┌─────────────────────────────────────────────┐
│  Docker Network: internal (no internet)     │
│                                             │
│   ┌──────────────┐   ┌──────────────────┐   │
│   │ hermes-agent │   │ hermes-dashboard  │   │
│   └──────┬───────┘   └────────┬─────────┘   │
│          │                    │              │
│          ▼                    │              │
│   ┌──────────────┐            │              │
│   │ hermes-gtw   │◄───────────┘              │
│   └──────┬───────┘                           │
└──────────┼───────────────────────────────────┘
           │
┌──────────┼───────────────────────────────────┐
│  Docker Network: egress (internet-capable)   │
│          │                                   │
│          ▼                                   │
│   ┌─────────────────┐                        │
│   │ egress-proxy    │──► allowlisted hosts   │
│   │ (squid / envoy) │                        │
│   └─────────────────┘                        │
└──────────────────────────────────────────────┘
```

Two Docker networks:
- **`internal`** — no default route, no internet access. Agent, dashboard, gateway run here.
- **`egress`** — has internet access. Only services that need to reach external APIs are attached.

The gateway is dual-homed (both networks) so it can receive inbound messages and forward them to the agent on the internal network.

## Compose Configuration

Override the default `docker-compose.yml` with `docker-compose.override.yml`:

```yaml
networks:
  internal:
    driver: bridge
    internal: true          # no default route, no internet
  egress:
    driver: bridge

services:
  gateway:
    network_mode: ""        # clear the host-mode default
    networks:
      - internal
      - egress              # needs outbound for Telegram, LLM APIs
    ports:
      - "127.0.0.1:9119:9119"

  dashboard:
    network_mode: ""
    networks:
      - internal            # internal only
```

### With an Egress Proxy (Recommended)

Route all outbound traffic through an HTTP proxy with an explicit allowlist:

```yaml
services:
  gateway:
    environment:
      - HTTP_PROXY=http://egress-proxy:3128
      - HTTPS_PROXY=http://egress-proxy:3128
      - NO_PROXY=hermes,hermes-dashboard,localhost

  egress-proxy:
    image: ubuntu/squid:6.10-24.04_edge
    networks:
      - egress
    volumes:
      - ./config/squid-allowlist.conf:/etc/squid/conf.d/allowlist.conf:ro

  dashboard:
    network_mode: ""
    networks:
      - internal
```

Example `config/squid-allowlist.conf`:
```
acl allowed_hosts dstdomain api.openai.com
acl allowed_hosts dstdomain api.anthropic.com
acl allowed_hosts dstdomain openrouter.ai
acl allowed_hosts dstdomain generativelanguage.googleapis.com
acl allowed_hosts dstdomain api.telegram.org
acl allowed_hosts dstdomain api.github.com
acl allowed_hosts dstdomain discord.com

http_access allow CONNECT allowed_hosts
http_access deny all
```

## Validation

```bash
# From agent container — should FAIL (no egress)
docker compose exec gateway \
  curl -sf --max-time 5 https://example.com && echo "FAIL" || echo "OK: egress blocked"

# Should SUCCEED (internal network)
docker compose exec gateway \
  curl -sf --max-time 5 http://hermes-dashboard:9119/health && echo "OK" || echo "FAIL"

# With proxy — should SUCCEED (allowlisted)
docker compose exec gateway \
  curl -sf --max-time 5 --proxy http://egress-proxy:3128 https://api.openai.com/v1/models && echo "OK" || echo "FAIL"
```

## Limitations

- **DNS resolution:** The `internal` network can still resolve external DNS names unless you run a local DNS resolver that blocks external queries
- **Not a substitute for sandbox backends:** This isolates the container's network. For stronger isolation, combine with a sandboxed terminal backend (Docker, Modal, Daytona)
- **Platform adapters need egress:** Gateway needs outbound for messaging platform APIs — add their endpoints to the proxy allowlist