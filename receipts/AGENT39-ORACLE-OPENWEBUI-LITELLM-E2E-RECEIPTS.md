# AGENT-39: Browser Oracle CDP End-to-End UI Verification Receipt (OpenWebUI `:8090` / `.72:8080` & LiteLLM `:4000/ui`)

- **WRITTEN-AT**: `2026-10-18T09:49:10Z`
- **AGENT**: `Agent-39` (Oracle CDP E2E Tester — OpenWebUI `:8090` / `.72:8080` & LiteLLM `:4000/ui`)
- **ORACLE CDP NODE**: `http://10.0.10.137:9222` (BattleStation, Chrome `155.0.8059.41`, `1920x1080` viewport) & `http://10.0.70.181:9222` (Bittorrent standby verified)
- **VERDICT**: `PASS` (`100% GREEN` across all OpenWebUI and LiteLLM UI/API endpoints)

---

## 1. Misconfigurations Discovered & Remediated

1. **Controller LiteLLM (`10.0.70.164:4000`) Missing `custom_auth.py` & Stale In-Memory Catalog (`18` vs `38` Models)**:
   - **Defect**: `/home/mboyle/infra/support-layer/litellm/config.yaml` on `10.0.70.164` was missing `tier1-cyberstrike-35b-q4km` (37 models on disk vs 38 on `10.0.70.228`), the running container had not been reloaded since prior edits (serving only 18 models in `/v1/models`), and `general_settings.custom_auth: custom_auth.user_api_key_auth` caused `ImportError: Could not import user_api_key_auth from custom_auth` on container restart because `/app/custom_auth.py` was not present or mounted on `10.0.70.164`.
   - **Fix**:
     - Added `tier1-cyberstrike-35b-q4km` (`ollama/cyberstrike:35b-a3b-q4_K_M` -> `http://10.0.10.137:11434`) to `/home/mboyle/infra/support-layer/litellm/config.yaml` on `10.0.70.164`.
     - Installed `/home/mboyle/infra/support-layer/litellm/custom_auth.py` on `10.0.70.164` matching `10.0.70.228`.
     - Updated `/home/mboyle/infra/support-layer/litellm/run.sh` on `10.0.70.164` to bind-mount `-v "$D/custom_auth.py:/app/custom_auth.py:ro"` and recreated `litellm-proxy`.
     - Verified `http://10.0.70.164:4000/v1/models` now exposes all **38** models (`HTTP 200`), matching `http://10.0.70.228:4000/v1/models` (**38** models), and `http://10.0.70.164:8090/api/models` now exposes **60** total models (`38` LiteLLM + `17` Ollama + `5` custom/arena).

2. **AI Vault A OpenWebUI (`10.0.70.72:8080`) Unconfigured OpenAI/LiteLLM Upstream**:
   - **Defect**: `http://10.0.70.72:8080/openai/config` had `OPENAI_API_BASE_URLS: ["https://api.openai.com/v1"]` with an empty API key `""`, exposing only 7 local Ollama/Arena models (`qwen2.5-coder:7b`, `bge-m3:latest`, etc.) and failing to surface the fleet LiteLLM model catalog.
   - **Fix**: Updated `POST http://10.0.70.72:8080/openai/config/update` with `OPENAI_API_BASE_URLS: ["http://10.0.70.164:4000/v1"]` and `OPENAI_API_KEYS: ["sk-fleet-local"]`.
   - **Verification**: `http://10.0.70.72:8080/api/models` immediately populated **45** models (`38` LiteLLM fleet models + `7` local Ollama/Arena models).

---

## 2. Interactive Browser Oracle CDP End-to-End Verification Matrix

| Target Service | Endpoint | Catalog Count | Interactive E2E Action | Result | Screenshot Artifact |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **OpenWebUI (Controller)** | `http://10.0.70.164:8090` | `60` models | Authenticated session (`1920x1080`), opened model selector dropdown (`tier1-fast`, `local-coder`, `qwen2.5-coder-32b`, `hermes-3`, `auto-local` verified), submitted live Python `fibonacci(n)` prompt in `#chat-input`, waited for streaming completion and follow-up generation. | `PASS` (`HTTP 200`) | `/home/mboyle/.gemini/antigravity-cli/brain/d61a0f86-9a87-4a9f-8b2c-ea5791e23bc8/screenshots/oracle_e2e_openwebui_8090.png` |
| **OpenWebUI (AI Vault A)** | `http://10.0.70.72:8080` | `45` models | Authenticated session (`1920x1080`), opened model selector dropdown (`tier1-fast`, `local-coder`, `qwen2.5-coder`, `hermes-3`, `auto-local` verified), submitted live Python `binary_search(arr, target)` prompt in `#chat-input`, verified syntax-highlighted Python code block & follow-ups rendered cleanly. | `PASS` (`HTTP 200`) | `/home/mboyle/.gemini/antigravity-cli/brain/d61a0f86-9a87-4a9f-8b2c-ea5791e23bc8/screenshots/oracle_e2e_openwebui_72_8080.png` |
| **LiteLLM Swagger & Admin UI (Controller)** | `http://10.0.70.164:4000/` & `http://10.0.70.164:4000/ui` | `38` models | Opened `/ui` dashboard (`LiteLLM Dashboard` login view rendered cleanly) and `/` Swagger OAS 3.1 UI (`LiteLLM API 1.103.0`), expanded `POST /chat/completions` in Swagger UI, and verified `/v1/models` + `/v1/chat/completions`. | `PASS` (`HTTP 200`) | `/home/mboyle/.gemini/antigravity-cli/brain/d61a0f86-9a87-4a9f-8b2c-ea5791e23bc8/screenshots/oracle_e2e_litellm_4000.png` |
| **LiteLLM Admin UI (`ai-ollama01`)** | `http://10.0.70.228:4000/ui` | `38` models | Opened `http://10.0.70.228:4000/ui/` in Oracle browser (`LiteLLM Dashboard` rendered cleanly) and verified `/v1/models` parity (`38` models). | `PASS` (`HTTP 200`) | Verified in same CDP session |

---

## 3. Live `/v1/chat/completions` Verification Across Registered Local Models

All tested chat models on `http://10.0.70.164:4000/v1/chat/completions` returned `HTTP 200` with valid completions and `error: null`:

| Model ID | Backend Target | Status | Response |
| :--- | :--- | :--- | :--- |
| `trivial` | Ollama cluster (`qwen2.5-coder:7b` / `7b-16k`) | `PASS` | `{"model":"trivial","content":"PONG","error":null}` |
| `local-coder` | `http://10.0.10.137:11434` (`qwen2.5-coder:7b-16k`) | `PASS` | `{"model":"local-coder","content":"PONG","error":null}` |
| `qwen2.5-coder-7b` | Weighted Ollama pool (`.228`, `.83`, `.137`, `.72`, `.125`) | `PASS` | `{"model":"qwen2.5-coder-7b","content":"PONG","error":null}` |
| `hermes-3` | `http://10.0.70.83:11434` (`hermes3:8b`) | `PASS` | `{"model":"hermes-3","content":"PONG","error":null}` |
| `ci-summary` | Weighted `hermes3:8b` pool (`.228`, `.83`) | `PASS` | `{"model":"ci-summary","content":"PONG","error":null}` |
| `tier1-fast` | `http://10.0.70.72:8082/v1` (`qwen3-coder-30b-a3b`) | `PASS` | `{"model":"tier1-fast","content":"PONG","error":null}` |
| `tier1-qwen3-coder-30b-a3b` | `http://10.0.70.72:8082/v1` (`qwen3-coder-30b-a3b`) | `PASS` | `{"model":"tier1-qwen3-coder-30b-a3b","content":"PONG","error":null}` |
| `qwen2.5-coder-32b` | `http://10.0.70.125:8081/v1` (`qwen2.5-coder-32b-distributed`) | `PASS` | `{"model":"qwen2.5-coder-32b","content":"PONG","error":null}` |
| `auto-local` | `http://10.0.70.164:4010/v1` (`auto-local`) | `PASS` | `{"model":"auto-local","content":"PONG","error":null}` |
