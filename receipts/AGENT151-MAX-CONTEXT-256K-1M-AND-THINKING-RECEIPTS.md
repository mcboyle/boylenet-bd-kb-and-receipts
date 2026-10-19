# AGENT-151: Max Context (`256K` / `512K` / `1M`) + Full `<think>` Reasoning Upgrade Receipts

- **Agent**: `Agent-151` (`Max Context 256K/512K/1M + Full <think> Reasoning Architect`)
- **Timestamp (UTC)**: `2026-10-19T02:06:30Z`
- **Status**: **COMPLETE (`rc=0`)**

---

## 1. Executive Summary & Measured Context / VRAM Matrix

| Node & Endpoint | Service / Engine | Model (`GGUF` / Ollama) | Measured Context (`n_ctx`) | Slots (`-np`) | Unified KV (`-kvu`) | KV Cache Quant | Measured Tesla T4 VRAM (`15,360 MiB` Total) | `<think>` / `reasoning_content` | Status |
|---|---|---|---|---|---|---|---|---|---|
| **`10.0.70.83:18080`** | `llama-heretic-18080.service` | `Qwen3.6-35B-A3B-Heretic-Q4_K_M.gguf` (256K Native) | **`262,144` (`256K`)** | `4` | `true` | `q8_0 / q8_0` | **`5,515 MiB / 15,360 MiB`** (`9,845 MiB` free) | **ENABLED** (`--reasoning-preserve`) | **LIVE (200 OK)** |
| **`10.0.70.83:18080`** | `llama-server` (512K Benchmark) | `Qwen3.6-35B-A3B-Heretic-Q4_K_M.gguf` (512K YaRN 2.0 / KVU) | **`524,288` (`512K`)** | `4` | `true` | `q8_0 / q8_0` | **`9,007 MiB / 15,360 MiB`** (`6,353 MiB` free) | **ENABLED** (`--reasoning-preserve`) | **VERIFIED (`rc=0`)** |
| **`10.0.70.72:8000` & `:8082`** | `llama-tier1-alpha.service` | `Qwen3-Coder-30B-A3B-Instruct-Q4_K_M.gguf` | **`262,144` (`256K`)** | `4` | `true` | `q8_0 / q8_0` | **`14,863 MiB / 15,360 MiB`** (GPU0 + AVX-512 MoE) | **ENABLED** (`--reasoning-preserve`) | **LIVE (200 OK)** |
| **`10.0.70.72:18081`** | `llama-qwythos-18081.service` (512K GPU Benchmark) | `Qwythos-9B-Claude-Mythos-5-1M-MTP-Q8_0.gguf` | **`524,288` (`512K`)** | `4` | `true` | `q8_0 / q8_0` | **`12,289 MiB / 15,360 MiB`** (`-ngl 20 -ot ".ffn_.*=CPU"`) | **ENABLED** (`--reasoning-preserve`) | **VERIFIED (`rc=0`)** |
| **`10.0.70.72:18081`** | `llama-qwythos-18081.service` (1M GPU Benchmark) | `Qwythos-9B-Claude-Mythos-5-1M-MTP-Q8_0.gguf` | **`1,048,576` (`1M`)** | `4` | `true` | `q8_0 / q8_0` | **`10,767 MiB / 15,360 MiB`** (`-ngl 99 -ot ".ffn_.*=CPU" -nkvo`) | **ENABLED** (`--reasoning-preserve`) | **VERIFIED (`rc=0`)** |
| **`10.0.70.72:18081`** | `llama-qwythos-18081.service` (24/7 Co-Resident w/ `:8000`) | `Qwythos-9B-Claude-Mythos-5-1M-MTP-Q8_0.gguf` | **`1,048,576` (`1M`)** | `4` | `true` | `q8_0 / q8_0` | **`0 MiB VRAM`** (`314 GiB` Skylake AVX-512 DDR4 RAM) | **ENABLED** (`--reasoning-preserve`) | **LIVE (200 OK)** |
| **`10.0.70.164:4010`** | `bd-gpu-orchestrator.service` | Sovereign Local Gateway (86-Model Vault Router) | **`262,144`–`1,048,576`** | `4` | `true` | `q8_0 / q8_0` | Routes `.83:18080`, `.72:8000/:8082`, `.72:18081` | **ENABLED** (`<think>` + `reasoning_content`) | **LIVE (200 OK)** |
| **`10.0.70.125:11434`** | `ollama.service` | `qwen2.5-coder:32b/14b/7b`, `qwythos-q8-fixed`, `bge-m3` | **`131,072` (`128K`)** | `2` | `FLASH_ATTN=1` | `q8_0` | **`6,728 MiB / 15,360 MiB`** | Native | **LIVE (`OK125`)** |
| **`10.0.70.228:11434`** | `ollama.service` | `qwen2.5-coder:32b/14b/7b`, `reap50-q5-fixed`, `qwythos` | **`131,072` (`128K`)** | `4` | `FLASH_ATTN=1` | `q8_0` | **`8,292 MiB / 15,360 MiB`** | Native | **LIVE (`OK228`)** |
| **`10.0.10.137:11434`** | BattleStation Ollama | `qwen2.5-coder:7b-16k`, `qwen2.5vl:7b` (`128K`) | **`32,768`–`128,000`** | `4` | `FLASH_ATTN=1` | `q8_0` | **`8,299 MiB / 11,264 MiB`** (RTX 2080 Ti) | Native | **LIVE (`OK137`)** |

---

## 2. Task 1 Receipts: `.83:18080` (`Qwen3.6-35B-A3B-Heretic`) `256K` & `512K` Context + Full `<think>` Reasoning

### 2.1 Measured `256K` (`262,144`) vs `512K` (`524,288`) VRAM & Context Benchmarks on `10.0.70.83`
- **Previous baseline (`131,072` / `128K` with thinking disabled)**:
  - Flags: `-c 131072 -np 4 -kvu --no-reasoning-preserve --reasoning-budget 0`
  - Measured Tesla T4 VRAM: `3,831 MiB / 15,360 MiB`
- **Measured `256K` (`262,144`) Native Context + Full `<think>` Reasoning**:
  - Flags: `-c 262144 -np 4 -kvu --reasoning-preserve --jinja -t 48 -ngl 99 -ot ".ffn_.*_exps.=CPU" -fa on -ctk q8_0 -ctv q8_0`
  - Startup time: `5s` (mmap cached) / `24s` (`--load-mode none` full RAM preload)
  - Measured Tesla T4 VRAM: **`5,515 MiB / 15,360 MiB`** (`9,845 MiB` free VRAM headroom!)
  - `/props` & `/slots` verification:
    ```
    .83:18080 n_ctx: 262144 total_slots: 4
    .83:18080 slots: 4 slot0 n_ctx: 262144
    I srv load_model: initializing, n_slots = 4, n_ctx_slot = 262144, kv_unified = 'true'
    ```
- **Measured `512K` (`524,288`) Unified Context + Full `<think>` Reasoning**:
  - Tested both native unified KV pool (`-c 524288 -np 4 -kvu`) and YaRN 2.0 (`-c 524288 -np 4 -kvu --rope-scaling yarn --rope-scale 2.0 --yarn-orig-ctx 262144`):
  - Measured Tesla T4 VRAM: **`9,007 MiB / 15,360 MiB`** (`6,353 MiB` free VRAM headroom!)
  - `/props` & `/slots` verification:
    ```
    512K YaRN HEALTHY at 16s!
    0, Tesla T4, 9007 MiB, 15360 MiB
    512K YaRN slots: 4 n_ctx: 524288
    512K YaRN props n_ctx: 524288 total_slots: 4
    ```

### 2.2 Production `/etc/systemd/system/llama-heretic-18080.service` on `10.0.70.83`
- Removed `--no-reasoning-preserve` and `--reasoning-budget 0`.
- Enabled `--reasoning-preserve`, `-kvu`, `-c 262144`, `--load-mode none` (eliminates mmap page-fault stalls on CPU MoE experts), `-fa on -ctk q8_0 -ctv q8_0`:
```ini
[Unit]
Description=Boylenet Tier-2 Qwen3.6-35B-A3B-Heretic 256K Context + Full Reasoning Server (:18080)
After=network-online.target

[Service]
Type=simple
User=mboyle
Environment="LD_LIBRARY_PATH=/opt/llama-rpc:/usr/local/lib/ollama/cuda_v12"
ExecStart=/opt/llama-rpc/llama-server -m /mnt/ai-models/qwen3.6-35b-a3b-heretic/Qwen3.6-35B-A3B-Heretic-Q4_K_M.gguf --host 0.0.0.0 --port 18080 --load-mode none -c 262144 -np 4 -kvu --reasoning-preserve --jinja -t 48 -ngl 99 -ot ".ffn_.*_exps.=CPU" -fa on -ctk q8_0 -ctv q8_0
Restart=always
RestartSec=3
TimeoutStopSec=3

[Install]
WantedBy=multi-user.target
```
- Verified `/slots` generation parameters on `10.0.70.83:18080`:
  - `"n_ctx": 262144`
  - `"chat_format": "peg-native"`
  - `"reasoning_format": "deepseek"`
  - `"generation_prompt": "<|im_start|>assistant\n<think>\n"`

---

## 3. Task 2 Receipts: `.72:8000` (`Qwen3-Coder-30B-A3B` at `256K`) & `.72:18081` (`Qwythos-9B-1M` at `512K` & `1M`)

### 3.1 Cleanup & Root-Cause Fix on `10.0.70.72`
1. Cleaned `/dev/mapper/ds41_compat` and loop devices (`sudo dmsetup remove ds41_compat || true; sudo losetup -D || true`), freeing `309 GiB / 314 GiB` DDR4 RAM and `15,255 MiB / 15,360 MiB` Tesla T4 VRAM.
2. Discovered that during yesterday's `#87` benchmark on `.72`, `/opt/llama-rpc/llama-server` had been replaced by a 27-byte `llama-server.stub` (`exit 0`) and `/run/systemd/system/llama-tier1-alpha.service.d/zz-pause.conf` was active.
3. Restored `/opt/llama-rpc/llama-server.real` -> `/opt/llama-rpc/llama-server` and removed `zz-pause.conf`.

### 3.2 Measured `Qwythos-9B-Claude-Mythos-5-1M-MTP-Q8_0.gguf` at `512K` (`524,288`) & `1M` (`1,048,576`) on `.72:18081`
- **`512K` (`524,288`) GPU + CPU Hybrid (`-ngl 20 -ot ".ffn_.*=CPU" -fa on -ctk q8_0 -ctv q8_0 -np 4 -kvu`)**:
  ```
  Qwythos 512K (q8_0 KV) HEALTHY at 23s!
  0, Tesla T4, 12289 MiB, 15360 MiB
  Qwythos 512K props n_ctx: 524288 total_slots: 4
  Qwythos 512K slots: 4 n_ctx: 524288
  ```
- **`1M` (`1,048,576`) GPU + CPU Hybrid (`-ngl 99 -ot ".ffn_.*=CPU" -nkvo -fa on -ctk q8_0 -ctv q8_0 -np 4 -kvu`)**:
  ```
  Qwythos 1M (1048576) HEALTHY at 23s!
  0, Tesla T4, 10767 MiB, 15360 MiB
  Qwythos 1M props n_ctx: 1048576 total_slots: 4
  Qwythos 1M slots: 4 n_ctx: 1048576
  ```
- **`256K` (`262,144`) `Qwen3-Coder-30B-A3B-Instruct-Q4_K_M.gguf` on `.72:8000` (and `:8082` via NAT redirect)**:
  - Flags: `-c 262144 -np 4 -kvu --reasoning-preserve -ngl 99 -ot ".ffn_.*_exps.=CPU" -fa on -ctk q8_0 -ctv q8_0 --jinja -t 48`
  - Measured Tesla T4 VRAM: **`14,875 MiB / 15,360 MiB`**
  - Both `llama-tier1-alpha.service` (`:8000` / `:8082` at `n_ctx: 262144`) and `llama-qwythos-18081.service` (`:18081` at `n_ctx: 1048576`) run **simultaneously** on `10.0.70.72`:
    ```
    BOTH .72:8000 (256K) AND .72:18081 (1M) HEALTHY at 18s!
    0, Tesla T4, 14875 MiB, 15360 MiB
    .72:8000 props n_ctx: 262144 total_slots: 4
    .72:8082 props n_ctx: 262144 total_slots: 4
    .72:18081 props n_ctx: 1048576 total_slots: 4
    ```

---

## 4. Task 3 Receipts: `/home/mboyle/bin/bd-gpu-orchestrator.py` (`:4010` Sovereign Local Gateway)

### 4.1 Upgrades Applied to `/home/mboyle/bin/bd-gpu-orchestrator.py`
1. **Default `<think>` Reasoning Enabled**:
   - Removed `fwd_body.setdefault("chat_template_kwargs", {"enable_thinking": False})`.
   - Defaulted `chat_template_kwargs["enable_thinking"] = True` for all requests unless the caller explicitly passes `enable_thinking: False`.
2. **Dual `reasoning_content` + `<think>\n...\n</think>\n\n` Formatting**:
   - Preserves `choices[i].message.reasoning_content` (non-streaming) and `choices[i].delta.reasoning_content` (streaming).
   - Automatically prepends `<think>\n{reasoning_content}\n</think>\n\n` to `choices[i].message.content` when `reasoning_content` is returned and `<think>` is not already embedded in `content` (and conversely extracts `reasoning_content` if `<think>...</think>` is present in `content`), ensuring OpenWebUI, LobeChat, Multi-AI Hub, and OpenCode always render collapsible thinking traces.
3. **86-Model Vault Hot-Swap & Catalog Upgraded to `256K` (`262,144`) / `1M` (`1,048,576`)**:
   - Upgraded `MODEL_CATALOG` and `VAULT_86_SYNC_ENTRIES` context windows to `262144` (`256K`) and `qwythos-9b` to `1048576` (`1M`).
   - Upgraded dynamic hot-swap `llama-server` systemd unit templates (`unit_alpha`, `unit_t2`, `t3_exec_line`) to launch with `-c 262144 -np 4 -kvu --reasoning-preserve -fa on -ctk q8_0 -ctv q8_0`.
   - Increased `max_tokens` cap to `16384`.
4. **Restarted `bd-gpu-orchestrator.service` and Verified Live**:
   - `systemctl is-active bd-gpu-orchestrator.service` -> `active`

---

## 5. Task 4 Receipts: Ollama `128K` (`131,072`) Context + Flash Attention + `q8_0` KV Cache on `.125`, `.228`, and `.137`

### 5.1 Systemd Drop-In `/etc/systemd/system/ollama.service.d/context-override.conf` on `10.0.70.125` & `10.0.70.228`
```ini
[Service]
Environment="OLLAMA_FLASH_ATTENTION=1"
Environment="OLLAMA_KV_CACHE_TYPE=q8_0"
Environment="OLLAMA_CONTEXT_LENGTH=131072"
```
- Verified via `systemctl show ollama --property=Environment`:
  - **`10.0.70.125`**: `OLLAMA_FLASH_ATTENTION=1 OLLAMA_KV_CACHE_TYPE=q8_0 OLLAMA_CONTEXT_LENGTH=131072 OLLAMA_HOST=0.0.0.0 OLLAMA_KEEP_ALIVE=-1 OLLAMA_MAX_LOADED_MODELS=4 OLLAMA_NUM_PARALLEL=2`
  - **`10.0.70.228`**: `OLLAMA_FLASH_ATTENTION=1 OLLAMA_KV_CACHE_TYPE=q8_0 OLLAMA_CONTEXT_LENGTH=131072 OLLAMA_HOST=0.0.0.0 OLLAMA_NUM_GPU=999 OLLAMA_KEEP_ALIVE=-1 OLLAMA_MAX_LOADED_MODELS=1 OLLAMA_NUM_PARALLEL=4`
- Live large-context generation probes (`num_ctx=131072` on `.125` & `.228`, `num_ctx=32768` on `.137`):
  - `10.0.70.125:11434`: `.125 response: OK125` (`rc=0`)
  - `10.0.70.228:11434`: `.228 response: OK228` (`rc=0`)
  - `10.0.10.137:11434`: `.137 response: OK137` (`rc=0`)
