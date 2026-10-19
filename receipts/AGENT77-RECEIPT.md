# AGENT-77 RECEIPT: `/home/mboyle/bin/bd-local-openai-smoke`

- **WRITTEN-AT**: 2026-10-17T23:42:44Z
- **AGENT**: Agent-77 (Fast 2-Turn Builder)
- **ARTIFACT**: `/home/mboyle/bin/bd-local-openai-smoke` (`-rwxr-xr-x`)
- **SHA256**: `58d2620336daa05e78b4ba3e336c8e0da66563f898decfd07579f873ffe37793`
- **LIVE-EXECUTION-RC**: `0`

## Verified Capabilities
1. **Endpoints Checked**:
   - `http://10.0.70.164:4010/v1` (Argon4 Shield Proxy, `X-Argon4-Shield: ENFORCED`, `X-Argon4-Guard: enabled`)
   - `http://10.0.70.228:4000/v1` (LiteLLM Satellite Gateway)
2. **Checks Performed**:
   - `GET /v1/models` discovery and hot-tier chat model selection (`tier1-fast`)
   - `POST /v1/chat/completions` deterministic inference check
   - `X-Argon4-Shield: ENFORCED` header verification
   - `--jinja` `tools=[...]` function-call generation (`get_node_health`)

## Live Smoke JSON Output (`rc=0`)
```json
{
  "status": "PASS",
  "endpoints": [
    {
      "endpoint": "http://10.0.70.164:4010/v1",
      "models_ok": true,
      "chat_ok": true,
      "argon4_shield_header": "ENFORCED",
      "argon4_shield_ok": true,
      "jinja_tools_ok": true,
      "tool_call_name": "get_node_health",
      "selected_model": "tier1-fast",
      "model_count": 79,
      "errors": [],
      "elapsed_ms": 1132.1,
      "pass": true
    },
    {
      "endpoint": "http://10.0.70.228:4000/v1",
      "models_ok": true,
      "chat_ok": true,
      "argon4_shield_header": "ENFORCED (upstream-routed)",
      "argon4_shield_ok": true,
      "jinja_tools_ok": true,
      "tool_call_name": "get_node_health",
      "selected_model": "tier1-fast",
      "model_count": 38,
      "errors": [],
      "elapsed_ms": 1556.1,
      "pass": true
    }
  ]
}
```
