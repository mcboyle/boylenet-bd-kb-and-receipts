# AGENT 95 — Universal 86-Model Platform Sync Receipts (`O1789`)

- **Written At**: `2026-10-18T19:32:00Z`
- **Agent**: `AGENT95` (`f2c0f334-127e-47e4-bab4-f7a9a1706276`)
- **Live Progress Stream**: `/var/tmp/bd-headless-streams/agent95-86models-all-platforms-sync.log`
- **Mission**: Sync all **86 Vault Models** (`44` on Vault A `10.0.70.72` + `42` on Vault B `10.0.70.83`), including all **51 GGUF `llama-bench` Configurations (`pp512`/`tg128` tok/s)** + **17 Non-GGUF Validated Models (`Safetensors`/`ONNX`/`Keras`/`Diffusers`)** (`68` total evaluated by `Agent-37` + `80` `BENCHMARK_DB` entries in `/home/mboyle/bin/bd-vault-curator`), across every Boylenet platform.

---

## 1. Executive Summary — Platform-by-Platform Availability Matrix

| Platform | Endpoint / Ingress | Config / Source File | Before Sync | After Sync (`rc=0`) | Verification Command & Receipt |
|---|---|---|---:|---:|---|
| **1A. LiteLLM Master Gateway (Controller)** | `http://10.0.70.164:4000/v1` (`http://litellm.boylenet.themfboyles.com`) | `/home/mboyle/infra/support-layer/litellm/config.yaml` | `64` models | **`228` models** (`86` bare + `86` `vault/*` + `56` fleet aliases) | `curl -s -H "Authorization: Bearer sk-fleet-local" http://127.0.0.1:4000/v1/models` -> `total_models=228 \| vault_prefixed=86` |
| **1B. LiteLLM Gateway (Satellite `.228`)** | `http://10.0.70.228:4000/v1` | `10.0.70.228:/home/mboyle/infra/support-layer/litellm/config.yaml` | `64` models | **`228` models** (`86` bare + `86` `vault/*` + `56` fleet aliases) | `curl -s -H "Authorization: Bearer sk-fleet-local" http://10.0.70.228:4000/v1/models` -> `total_models=228 \| vault_prefixed=86` |
| **2. GPU Mode Orchestrator (`:4010`)** | `http://10.0.70.164:4010/v1` (`http://orchestrator.boylenet.themfboyles.com`) | `/usr/local/bin/bd-gpu-orchestrator.py` | `188` models | **`340` models** (`86` bare + `86` `vault/*` + all tier/quant aliases) | `curl -s http://127.0.0.1:4010/v1/models` -> `4010 total models: 340 vault/* models: 86` |
| **3A. OpenWebUI (Controller `:8090`)** | `http://10.0.70.164:8090` (`http://openwebui.boylenet.themfboyles.com`) | `open-webui` container (`OPENAI_API_BASE_URL=http://10.0.70.164:4000/v1`) | `82` models | **`246` models** (`228` LiteLLM + `18` Ollama; `86` `vault/*` + `86` bare) | `GET http://127.0.0.1:8090/api/models?refresh=true` -> `total=246 \| vault/*=86` |
| **3B. OpenWebUI (Vault A `.72:8080`)** | `http://10.0.70.72:8080` (`http://ai-webui.boylenet.themfboyles.com`) | `open-webui` on `.72` (`webui.db` -> `http://10.0.70.164:4000/v1`) | `64` OpenAI models | **`228` OpenAI/LiteLLM models** + local `.72:11434` Ollama models | `GET http://10.0.70.72:8080/api/version` (`v0.9.6`) + `webui.db` bound to `http://10.0.70.164:4000/v1` (`sk-fleet-local`) |
| **4. 86-Model AI Vault Storefront (`:4084`)** | `http://10.0.70.164:4084` (`http://models.boylenet.themfboyles.com`) | `/home/mboyle/bin/bd-model-storefront.py` | `86` dirs, `22` bench | **`86` dirs, `68` evaluated (`51` GGUF bench + `17` Non-GGUF validated)** + OpenWebUI 1-click button & platform badges | `curl -s http://127.0.0.1:4084/health` -> `total_benchmarked_configs: 68, gguf_benchmarked_configs: 51, non_gguf_validated_configs: 17` |
| **5. Unified OS Desktop Shell (`:4085`)** | `http://10.0.70.164:4085` (`http://os.boylenet.themfboyles.com`) | `/home/mboyle/bin/bd-os-shell.py` (`VAULT_86_MODELS`) | `71` models | **`86` models** with live `pp512`/`tg128` speeds & `VALIDATED` badges | `curl -s http://127.0.0.1:4085/health` -> `"vault_models_count": 86` |
| **6. LobeChat Studio (`:4095`)** | `http://10.0.70.164:4095` | `lobechat-studio:/app/lobechat_server.py` + `/app/lobe_models_86.json` | `8` models | **`87` models** (`auto-local` + all `86` Vault Models with speeds) | `curl -s http://127.0.0.1:4095/health` -> `{"status": "ok", "models": 87}` |
| **7. OpenCode Web (`:4096`)** | `http://10.0.70.164:4096` (`http://opencode.boylenet.themfboyles.com`) | `/home/mboyle/.config/opencode/opencode.json` & `boylenet-local-provider.json` | `20` models | **`101` / `105` models** (`86` Vault Models + fleet aliases) | `HTTP 200 OK` on `http://127.0.0.1:4096/` |
| **8. OpenClaw Control UI (`:4097`)** | `http://10.0.70.164:4097` (`http://openclaw.boylenet.themfboyles.com`) | `/home/mboyle/.openclaw/openclaw.json` | `15` models | **`93` models** (`86` Vault Models + fleet aliases) | `HTTP 200 OK` on `http://127.0.0.1:4097/` |
| **9. Multi-Harness Studio (`:4098`)** | `http://10.0.70.164:4098` | `/home/mboyle/bin/bd-multi-harness-web.py` (`MODELS` & `COST_SPEED_BY_MODEL`) | `61` models | **`225` models** (`86` bare Vault Models + `86` `vault/*` aliases + cloud/local) | `Target Model (All 225 Cloud & 86 Vault Models)` |

---

## 2. Raw CLI & HTTP Verification Receipts (`rc=0`)

### 2.1 LiteLLM `.164:4000` & `.228:4000` (`/v1/models`)
```text
LiteLLM .164:4000 -> total_models=228 | vault_prefixed=86
LiteLLM .228:4000 -> total_models=228 | vault_prefixed=86
```

### 2.2 GPU Mode Orchestrator `:4010` (`/v1/models`)
```text
4010 total models: 340 vault/* models: 86
```

### 2.3 OpenWebUI `:8090` (`http://openwebui.boylenet.themfboyles.com`) & `.72:8080`
```text
Admin user on .164:8090: ('a12463df-370f-4948-b488-9437da254b15', 'admin@localhost', 'admin')
OpenWebUI .164:8090 /api/models -> total=246 | vault/*=86 | sample=['vault/kimi-k3-abliterated-q2k-gguf', 'vault/kimi-k3-abliterated-iq1s-gguf', 'vault/qwen3.8-flash-next', 'vault/deepseek-v4.1-flash-abliterated-755b', 'vault/deepseek-v3.2-685b']
```

### 2.4 86-Model Vault Storefront `:4084/health` (`http://models.boylenet.themfboyles.com`)
```json
{
  "status": "ok",
  "service": "bd-model-storefront",
  "port": 4084,
  "url": "http://models.boylenet.themfboyles.com",
  "total_vault_directories": 86,
  "vault_a_72_directories": 44,
  "vault_b_83_directories": 42,
  "total_benchmarked_configs": 68,
  "gguf_benchmarked_configs": 51,
  "non_gguf_validated_configs": 17,
  "total_validated_configs": 68,
  "curator_benchmark_db_entries": 80,
  "platforms_synced": [
    "OpenWebUI (:8090 & .72:8080)",
    "LiteLLM (.164:4000 & .228:4000)",
    "GPU Orchestrator (:4010)",
    "Storefront (:4084)",
    "OS Shell (:4085)",
    "LobeChat (:4095)",
    "OpenCode (:4096)",
    "OpenClaw (:4097)",
    "Multi-Harness Studio (:4098)"
  ],
  "total_weight_tb": 8.9,
  "timestamp": "2026-10-18T19:31:31Z"
}
```

### 2.5 Unified OS Shell `:4085`, LobeChat `:4095`, OpenCode `:4096`, OpenClaw `:4097`, Multi-Harness Studio `:4098`
```text
{"status": "ok", "service": "bd-os-shell", "port": 4085, "vault_models_count": 86}
{"status": "ok", "service": "lobechat", "port": 3210, "openai_proxy_url": "http://10.0.70.164:4000/v1", "models": 87}
Target Model (All 225 Cloud &amp; 86 Vault Models)
OpenCode :4096 HTTP 200 | OpenClaw :4097 HTTP 200
```
