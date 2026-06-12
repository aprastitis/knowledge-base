---
type: card
status: active
date: 2026-06-12
summary: Behavior-changing plugin hooks — llm_request, llm_execution, tool_request, tool_execution middleware
tags: [hermes, middleware, plugins, request-rewriting]
sources:
  - https://github.com/NousResearch/hermes-agent/blob/main/docs/middleware/README.md
related:
  - engineering/hermes/plugins
  - engineering/hermes/observer-hooks
---

# Hermes Middleware

Hermes middleware is the **behavior-changing companion** to observer hooks. Observer hooks report what happened. Middleware can change what happens by rewriting a request before execution or by wrapping the execution callback itself.

This contract is intentionally backend-neutral. A plugin can use it for local policy, request shaping, tracing, adaptive routing, cache control, sandbox selection, or handoff to runtimes such as NeMo Relay.

## Contract

Plugins register middleware from `register(ctx)`:

```python
def register(ctx):
    ctx.register_middleware("llm_request", on_llm_request)
    ctx.register_middleware("llm_execution", on_llm_execution)
    ctx.register_middleware("tool_request", on_tool_request)
    ctx.register_middleware("tool_execution", on_tool_execution)
```

Every middleware callback receives:
- `telemetry_schema_version`: currently `hermes.observer.v1`
- `middleware_schema_version`: currently `hermes.middleware.v1`
- Runtime context: `session_id`, `task_id`, `turn_id`, `api_request_id`, `provider`, `model`, `api_mode`, `tool_name`, `tool_call_id`

## Supported Middleware Kinds

| Kind | Payload | Return shape | Purpose |
|------|---------|--------------|---------|
| `llm_request` | `request`, `original_request` | `{"request": {...}}` | Replace effective provider kwargs before execution |
| `tool_request` | `tool_name`, `args`, `original_args` | `{"args": {...}}` | Replace effective tool args before hooks, guardrails, approvals |
| `llm_execution` | `request`, `original_request`, `next_call` | Any provider response | Wrap or replace the actual provider call |
| `tool_execution` | `tool_name`, `args`, `original_args`, `next_call` | Any tool result | Wrap or replace the actual tool call |

Request middleware can return optional trace fields:
```python
return {
    "request": updated_request,
    "source": "my-plugin",
    "reason": "selected fallback model",
}
```

Hermes stores those trace entries in later observer hook payloads as `middleware_trace`.

Execution middleware receives a `next_call` callback — call it to continue the chain:
```python
def on_tool_execution(**kwargs):
    result = kwargs["next_call"](kwargs["args"])
    return result
```

## Execution Order

### LLM Calls

1. Build provider kwargs from the current conversation
2. Apply `llm_request` middleware
3. Emit `pre_api_request` observer hooks with the effective request
4. Run provider execution through `llm_execution` middleware
5. Emit `post_api_request` or `api_request_error` observer hooks

### Tool Calls

1. Parse and coerce model-provided tool arguments
2. Apply `tool_request` middleware
3. Run the normal Hermes pre-execution path: tool availability checks, observer block directives, guardrails, approvals
4. Run tool execution through `tool_execution` middleware
5. Emit `post_tool_call` observer hooks
6. Apply `transform_tool_result` hooks before result is appended to context

**Tool request middleware runs before approval checks** — a rewritten path, command, or URL is what downstream policy evaluates.

## Enablement

Middleware only runs for enabled plugins:
```bash
hermes plugins enable <plugin-name>
```

For isolated local testing:
```bash
export HERMES_HOME=/tmp/hermes-middleware-test
mkdir -p "$HERMES_HOME"
hermes plugins enable <plugin-name>
hermes chat --query 'Reply exactly ok'
```

## Examples

### LLM Request Middleware (tag requests)
```python
def register(ctx):
    ctx.register_middleware("llm_request", tag_llm_request)

def tag_llm_request(**kwargs):
    request = dict(kwargs["request"])
    extra_body = dict(request.get("extra_body") or {})
    extra_body.setdefault("metadata", {})["hermes_middleware_demo"] = True
    request["extra_body"] = extra_body
    return {
        "request": request,
        "source": "middleware-demo",
        "reason": "tagged provider request",
    }
```

### Tool Request Middleware (constrain workdir)
```python
def register(ctx):
    ctx.register_middleware("tool_request", normalize_terminal_workdir)

def normalize_terminal_workdir(**kwargs):
    if kwargs.get("tool_name") != "terminal":
        return None
    args = dict(kwargs["args"])
    args.setdefault("workdir", "/tmp/hermes-middleware-demo")
    return {
        "args": args,
        "source": "middleware-demo",
        "reason": "defaulted terminal workdir",
    }
```

### LLM Execution Middleware (timing)
```python
import time

def register(ctx):
    ctx.register_middleware("llm_execution", time_llm_execution)

def time_llm_execution(**kwargs):
    started = time.monotonic()
    response = kwargs["next_call"](kwargs["request"])
    elapsed_ms = int((time.monotonic() - started) * 1000)
    print(f"llm_execution elapsed_ms={elapsed_ms}")
    return response
```

### Tool Execution Middleware (annotation)
```python
def register(ctx):
    ctx.register_middleware("tool_execution", annotate_tool_execution)

def annotate_tool_execution(**kwargs):
    result = kwargs["next_call"](kwargs["args"])
    # Metrics, logging, or external routing here
    return result
```

## Safety Notes

- Middleware should be deterministic for the same input unless explicitly routing to a dynamic external system
- Request middleware should return complete replacement payloads, not partial patches
- Execution middleware should call `next_call(...)` exactly once unless intentionally short-circuiting
- If execution middleware raises before calling `next_call(...)`, Hermes treats that as middleware failure and continues with the remaining chain
- If downstream provider or tool execution fails, middleware may let that error propagate or translate it deliberately