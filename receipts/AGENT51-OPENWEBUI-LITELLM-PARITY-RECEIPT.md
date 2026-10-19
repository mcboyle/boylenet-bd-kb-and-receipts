# AGENT-51: OpenWebUI (`:8090`) & LiteLLM (`:4000`) Model Catalog Parity Receipt

- **WRITTEN-AT**: `2026-10-17T23:36:26Z`
- **AGENT**: `Agent-51` (Fast 2-Turn Integrator)
- **TARGET NODE**: `ai-ollama01` (`10.0.70.228`)
- **LITELLM ENDPOINT**: `http://10.0.70.228:4000/v1/models` (`HTTP 200`, `38` models registered)
- **OPENWEBUI ENDPOINT**: `http://10.0.70.228:8090` (`HTTP 200`, synced to LiteLLM `:4000/v1`, `38` models exposed)
- **VERDICT**: `PASS` (`rc=0`, 10/10 required GPU tier models registered and reachable)

## Remediations Applied During Integration

1. **LiteLLM (`:4000`) Catalog Registration**: Added missing `tier1-cyberstrike-35b-q4km` (`ollama/cyberstrike:35b-a3b-q4_K_M` -> `http://10.0.10.137:11434`) to `/home/mboyle/infra/support-layer/litellm/config.yaml` on `10.0.70.228` and restarted LiteLLM container (`38` total models now active).
2. **OpenWebUI (`:8090`) Endpoint & Catalog Parity**: Restored `:8090` listener on `10.0.70.228` backed by `http://10.0.70.228:4000/v1/models` so all 10 local GPU tier models are reachable with `rc=0`.

## Model Catalog Parity Matrix

| Model ID | LiteLLM (`:4000/v1/models`) | OpenWebUI (`:8090`) | Reachability |
| :--- | :--- | :--- | :--- |
| `auto-local` | PASS (`registered`) | PASS (`reachable`) | `rc=0` |
| `tier1-qwen3-coder-30b-a3b` | PASS (`registered`) | PASS (`reachable`) | `rc=0` |
| `tier1-cyberstrike-35b-q4km` | PASS (`registered`) | PASS (`reachable`) | `rc=0` |
| `tier1-qwen2.5-coder-32b` | PASS (`registered`) | PASS (`reachable`) | `rc=0` |
| `tier1-qwythos-9b-bf16` | PASS (`registered`) | PASS (`reachable`) | `rc=0` |
| `tier2-deepseek-r1-70b` | PASS (`registered`) | PASS (`reachable`) | `rc=0` |
| `tier2-llama3.3-70b` | PASS (`registered`) | PASS (`reachable`) | `rc=0` |
| `tier2-qwythos-27b-bf16` | PASS (`registered`) | PASS (`reachable`) | `rc=0` |
| `tier2-cyberstrike-35b-bf16` | PASS (`registered`) | PASS (`reachable`) | `rc=0` |
| `tier3-hybrid` | PASS (`registered`) | PASS (`reachable`) | `rc=0` |
