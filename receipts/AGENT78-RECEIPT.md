# AGENT-78 RECEIPT: `bd-local-anthropic-smoke`

- **WRITTEN-AT**: 2026-10-17T23:40:01Z
- **TARGET**: `/home/mboyle/bin/bd-local-anthropic-smoke`
- **MODE**: `0755` (`chmod +x`)
- **SHA256**: `4f642ff840e818ce9763e77a8a5d7999de4d622260ffacaf6cb53e08e374664c`
- **ENDPOINT**: `http://10.0.70.228:4000/v1/messages`
- **HEADERS**: `x-api-key: sk-boylenet-local`, `anthropic-version: 2023-06-01`
- **MODEL**: `tier1-qwen3-coder-30b-a3b`
- **LIVE-RUN-RC**: `0`
- **LIVE-RUN-OUTPUT**:
```json
{"ok": true, "status": 200, "url": "http://10.0.70.228:4000/v1/messages", "model": "tier1-qwen3-coder-30b-a3b", "id": "resp_bGl0ZWxsbTpjdXN0b21fbGxtX3Byb3ZpZGVyOm9wZW5haTttb2RlbF9pZDpkOGFkYjg5NDE1YjEwMTc1OGRkMzM3NWE2OTY5YjgzYzU5ZGVhZWM3OGQzNTEwMmU3Y2QzNjY0NDAxNTlhMTZhO3Jlc3BvbnNlX2lkOnJlc3BfbUNhMWRDc3VHZTNBbUhEZ3lnb0hNYkE3ejRLNURqeE0=", "type": "message", "role": "assistant", "stop_reason": "end_turn", "latency_ms": 19852, "reply": "OK"}
```
