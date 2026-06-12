---
type: card
status: active
date: 2026-06-12
summary: Read-only telemetry contract — pre/post API request, pre/post tool call, session lifecycle, subagent hooks
tags: [hermes, observer-hooks, telemetry, tracing, plugins]
sources:
  - https://github.com/NousResearch/hermes-agent/blob/main/docs/observability/README.md
related:
  - engineering/hermes/plugins
  - engineering/hermes/middleware
  - engineering/agent-observability-opentelemetry
---

# Hermes Observer Hooks

Hermes observer hooks are the **read-only telemetry contract** for plugins that need to reconstruct agent execution without changing runtime behavior. They expose stable lifecycle events, correlation IDs, sanitized payloads, timing, status, and error fields.

Observer hooks should report what happened; they should **not** replace provider requests, tool arguments, or execution callbacks. Behavior-changing request or execution wrappers are outside this contract.

## Contract

Plugins register observer callbacks from `register(ctx)`:

```python
def register(ctx):
    ctx.register_hook("pre_api_request", on_pre_api_request)
    ctx.register_hook("post_api_request", on_post_api_request)
    ctx.register_hook("pre_tool_call", on_pre_tool_call)
    ctx.register_hook("post_tool_call", on_post_tool_call)
```

Every hook callback receives `**kwargs` so additive fields remain backward-compatible. The plugin manager injects `telemetry_schema_version = "hermes.observer.v1"` into every hook payload.

Hook callbacks are **fail-open** — Hermes catches exceptions, logs a warning, and keeps the agent loop running.

## Behavior-Affecting Hooks (Exceptions)

Most observer hook return values are ignored. The exceptions:

| Hook | Return behavior |
|------|----------------|
| `pre_llm_call` | May return a string or `{"context": "..."}` to inject ephemeral context |
| `pre_tool_call` | May return `{"action": "block", "message": "..."}` to block tool before execution |
| `transform_tool_result` | May return a replacement tool result string |
| `transform_llm_output` | May return a replacement final assistant text string |

## Correlation IDs

| Field | Meaning |
|-------|---------|
| `session_id` | Conversation/session identity |
| `task_id` | Task identity (useful for subagents) |
| `turn_id` | User-turn identity shared by API attempts and tool calls in a turn |
| `api_request_id` | Opaque provider-attempt identity |
| `tool_call_id` | Provider-supplied tool call ID when available |
| `parent_session_id` / `child_session_id` | Session link for delegated subagents |
| `parent_subagent_id` / `child_subagent_id` | Subagent link when available |

## Event Families

### Session Lifecycle

| Hook | When it fires |
|------|---------------|
| `on_session_start` | Brand-new session starts after system prompt is built |
| `on_session_end` | `run_conversation` call ends (including interrupted or incomplete turns) |
| `on_session_finalize` | CLI or gateway tears down an active session identity |
| `on_session_reset` | CLI or gateway moves from an old session identity to a new one |

### Turn-Scoped LLM Hooks

| Hook | When it fires |
|------|---------------|
| `pre_llm_call` | Before the tool loop begins for a user turn |
| `post_llm_call` | After the turn completes with final assistant output |

### Request-Scoped API Hooks

| Hook | When it fires |
|------|---------------|
| `pre_api_request` | Immediately before a provider API request |
| `post_api_request` | After a successful provider response |
| `api_request_error` | After a failed provider request or retryable error path |

`pre_api_request` includes: identity (`session_id`, `task_id`, `turn_id`, `api_request_id`), runtime (`platform`, `model`, `provider`, `base_url`, `api_mode`), attempt metadata (`api_call_count`, `message_count`, `tool_count`, `approx_input_tokens`), timing (`started_at`), and sanitized `request` payload.

`post_api_request` includes the same identity/runtime fields plus: `api_duration`, `finish_reason`, `usage`, `assistant_content_chars`, `assistant_tool_call_count`, and sanitized `response` payload.

### Tool Lifecycle

| Hook | When it fires |
|------|---------------|
| `pre_tool_call` | Before guardrail-approved tool dispatch |
| `post_tool_call` | After tool dispatch, cancellation, block, or error completion |
| `transform_tool_result` | After `post_tool_call`, before result is appended to model context |

`post_tool_call` status values:
- `ok` — Tool completed normally
- `error` — Tool ran and returned or raised an error
- `blocked` — `pre_tool_call` hook blocked execution
- `cancelled` — Execution was cancelled before normal completion

### Approval Lifecycle

| Hook | When it fires |
|------|---------------|
| `pre_approval_request` | Before the approval request is shown or sent |
| `post_approval_response` | After the user responds or the request times out |

Approval hooks are observer-only — plugins cannot pre-answer or veto approvals. To prevent a tool from reaching approval, use `pre_tool_call` blocking.

### Subagent Lifecycle

| Hook | When it fires |
|------|---------------|
| `subagent_start` | A delegated child agent is created |
| `subagent_stop` | A delegated child agent returns or fails |

## Payload Safety

Use the sanitized API payloads for new consumers:
- `pre_api_request.request`
- `post_api_request.response`
- `api_request_error.request`
- `api_request_error.error`

Sanitization converts provider objects to JSON-compatible structures, bounds large payloads, redacts sensitive keys, and avoids exposing raw response objects.

## Performance

Expensive request/response payload construction is gated behind `has_hook(...)` — Hermes only builds sanitized API telemetry payloads when at least one plugin registered the relevant hook.

Plugin authors should:
- Register only hooks the plugin actually consumes
- Avoid deep-copying or re-sanitizing already sanitized payloads
- Keep hook callbacks fast and fail-open

## Minimal Observer Plugin

```python
def register(ctx):
    ctx.register_hook("pre_api_request", on_pre_api_request)
    ctx.register_hook("post_api_request", on_post_api_request)
    ctx.register_hook("pre_tool_call", on_pre_tool_call)
    ctx.register_hook("post_tool_call", on_post_tool_call)

def on_pre_api_request(**kwargs):
    start_llm_span(
        request_id=kwargs.get("api_request_id"),
        turn_id=kwargs.get("turn_id"),
        request=kwargs.get("request"),
        model=kwargs.get("model"),
    )

def on_post_api_request(**kwargs):
    finish_llm_span(
        request_id=kwargs.get("api_request_id"),
        response=kwargs.get("response"),
        usage=kwargs.get("usage"),
        duration=kwargs.get("api_duration"),
    )

def on_pre_tool_call(**kwargs):
    start_tool_span(
        call_id=kwargs.get("tool_call_id"),
        name=kwargs.get("tool_name"),
        args=kwargs.get("args"),
    )

def on_post_tool_call(**kwargs):
    finish_tool_span(
        call_id=kwargs.get("tool_call_id"),
        result=kwargs.get("result"),
        status=kwargs.get("status"),
        duration_ms=kwargs.get("duration_ms"),
    )
```