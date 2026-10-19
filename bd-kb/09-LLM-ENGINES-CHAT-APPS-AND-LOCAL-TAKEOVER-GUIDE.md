# 🚀 BD-KB 09: Specialized LLM Inference Engines, Hot-Pinned GPU Endpoints, Chat & Coding Apps, and Standby Local Takeover Guide

- **Document ID**: `bd-kb-09-llm-engines-chat-apps-takeover`
- **Canonical Path**: `/home/mboyle/bd-persist/bd-kb/09-LLM-ENGINES-CHAT-APPS-AND-LOCAL-TAKEOVER-GUIDE.md`
- **Author**: `Agent-185` (`O1789`)
- **Updated At**: `2026-10-19T22:28:30Z`
- **Source Authorities**:
  - [`AGENT37-NEW-FRONTIER-MODELS-BENCHMARK-RECEIPTS.md`](file:///home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT37-NEW-FRONTIER-MODELS-BENCHMARK-RECEIPTS.md)
  - [`AGENT140-OPENWEBUI-15AGENTS-83TOOLS-14MCP-QDRANT-PG-RECEIPTS.md`](file:///home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT140-OPENWEBUI-15AGENTS-83TOOLS-14MCP-QDRANT-PG-RECEIPTS.md)
  - [`AGENT151-MAX-CONTEXT-256K-1M-AND-THINKING-RECEIPTS.md`](file:///home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT151-MAX-CONTEXT-256K-1M-AND-THINKING-RECEIPTS.md)
  - [`AGENT152-ALL-MODELS-THINKING-AND-CONTEXT-VERIFICATION-RECEIPTS.md`](file:///home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT152-ALL-MODELS-THINKING-AND-CONTEXT-VERIFICATION-RECEIPTS.md)
  - [`AGENT153-ALL-CHAT-APPS-LOBE-MULTI-OPENCODE-E2E-RECEIPTS.md`](file:///home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT153-ALL-CHAT-APPS-LOBE-MULTI-OPENCODE-E2E-RECEIPTS.md)
  - [`AGENT168-IK-LLAMA-AND-TURBOQUANT-BUILD-RECEIPTS.md`](file:///home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT168-IK-LLAMA-AND-TURBOQUANT-BUILD-RECEIPTS.md)
  - [`AGENT169-GAMOZO-MAINLINE-AND-DRAFTER-Q4-RECEIPTS.md`](file:///home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT169-GAMOZO-MAINLINE-AND-DRAFTER-Q4-RECEIPTS.md)
  - [`AGENT174-AI-VMS-POST-RESIZE-AND-BENCH-RECEIPTS.md`](file:///home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT174-AI-VMS-POST-RESIZE-AND-BENCH-RECEIPTS.md)

---

## 1. Post-Resize AI VM Hardware Topology (`1.26 TiB` RAM, `240` Cores, `72.7 GB` VRAM)

Across the 4 vSphere NVIDIA Tesla T4 GPU VMs (`esxi01..04`) and the bare-metal BattleStation (`RTX 2080 Ti`), all AI nodes run with `kernel.numa_balancing=0`, `vm.swappiness=1`, `vm.overcommit_memory=1`, and `transparent_hugepage=always` persisted in `/etc/sysctl.d/99-boylenet-llm.conf`:

| Node IP (25GbE Mesh IP) | Hostname (ESXi Host) | Allocated Tier / Post-Resize RAM (`MemTotal`) | CPU / NUMA Topology | GPU (`nvidia-smi`) | Local Storage | Primary Role |
|---|---|---|---|---|---|---|
| **`10.0.70.72`** (`10.0.75.12`) | `ai-srv01` (`esxi04`) | **`320 GiB` Tier** (`314.87 GiB` / `330,167,840 kB` `MemTotal`) | **`52 vCPU`** (2x Xeon Platinum 8170 @ 2.10GHz, 2 NUMA nodes `161.2 GB` each) | **1x NVIDIA Tesla T4 (`15,360 MiB`)** | `5.0 TB` Vault A (`/mnt/models_hot` & `/opt/models`) | Hot-Pinned Tier-1 Coder (`:8000`/`:8082`), `Qwythos-9B-1M` (`:18081`), OpenWebUI Compute (`:8080`), `rpc-server` (`:50052`) |
| **`10.0.70.83`** (`10.0.75.11`) | `aidev` (`esxi03`) | **`400 GiB` Peak / `300 GiB` Balanced** (`379.84 GiB` mid-run $\to$ `295.19 GiB` / `309,524,036 kB` post-balance) | **`52 vCPU`** (2x Xeon Platinum 8164 @ 2.00GHz, 2 NUMA nodes `151.1 GB` each) | **1x NVIDIA Tesla T4 (`15,360 MiB`)** | `5.0 TB` Vault B (`/mnt/ai-models` & `/mnt/models_hot`) | Hot-Pinned `Qwen3.6-35B-A3B-Heretic` Relief & Reasoning Commander (`:18080`), `rpc-server` (`:50052`) |
| **`10.0.70.125`** (`10.0.75.14`) | `ai-infer01` (`esxi01`) | **`192 GiB` $\to$ `300 GiB` Post-Resize** (`295.19 GiB` / `309,525,424 kB` `MemTotal`, `+9.22x` over 32 GiB baseline) | **`48 vCPU`** (2x Xeon E5-2690 v4 @ 2.60GHz, 2 NUMA nodes `151.1 GB` each) | **1x NVIDIA Tesla T4 (`15,360 MiB`)** *(driver `615.71.09` verified)* | `297 GB` NVMe | Qdrant Vector Engine (`:6333`, 6 collections `<15ms`), Ollama Pool Beta (`:11434`), `rpc-server` (`:50052`), Off-Host C++/CUDA Builder |
| **`10.0.70.228`** (`10.0.75.13`) | `ai-ollama01` (`esxi02`) | **`160 GiB` $\to$ `300 GiB` Post-Resize** (`295.18 GiB` / `309,519,800 kB` `MemTotal`, `+6.15x` over 48 GiB baseline) | **`64 vCPU`** (2x Xeon E5-2699 v4 @ 2.20GHz, 2 NUMA nodes `151.1 GB` each) | **1x NVIDIA Tesla T4 (`15,360 MiB`)** | `297 GB` NVMe | Ollama Pool Alpha/Beta (`:11434`), Langfuse (`:3000`), `rpc-server` (`:50052`) |
| **`10.0.10.137`** (LAN) | `BattleStation` (Bare-Metal Win11) | **`64 GiB` DDR4** (`64.00 GiB` UMA) | **`24 Threads`** (AMD Ryzen 9 12C/24T) | **1x NVIDIA GeForce RTX 2080 Ti (`11,264 MiB`)** | Local NVMe | Rule 84 Hardware Segregation: Ryzen 9 CPU Embeddings (`nomic`, `bge-m3`), RTX 2080 Ti High-Throughput Coder (`:11434`), Chrome CDP Oracle (`:9222`) |

---

## 2. All Hot-Pinned Local GPU Models & Production Endpoints

All hot-pinned local endpoints run with **Flash Attention (`-fa on` / `OLLAMA_FLASH_ATTENTION=1`)**, **`q8_0` KV Cache Quantization (`-ctk q8_0 -ctv q8_0` / `OLLAMA_KV_CACHE_TYPE=q8_0`)**, and **full `<think>` / `reasoning_content` preservation** (`--reasoning-preserve`).

### 2.1 Master Hot-Pinned Endpoint Directory

| Endpoint | Systemd Service / Runtime | Hot-Pinned Model(s) | Active Context (`n_ctx`) | Slots (`-np`) | Measured Throughput (`pp512` / `tg128`) | VRAM / RAM Footprint | Primary Workload |
|---|---|---|---:|---:|---|---|---|
| **`10.0.70.83:18080`** | `llama-heretic-18080.service` (`/opt/llama-rpc/llama-server`) | **`Qwen3.6-35B-A3B-Heretic-Q4_K_M.gguf`** (also aliased as `qwen3.6-35b-claude-opus-abliterated`) | **`262,144` (`256K`)** *(verified up to `524,288` / `512K` YaRN)* | `4` (`-kvu`) | **`795.0 tok/s` `pp` / `65.8 tok/s` `tg`** (2xT4); **`152.6 tok/s` `pp` / `22.8 tok/s` `tg`** (1xT4 + AVX-512) | `5,515 MiB / 15,360 MiB` T4 VRAM (`9.8 GiB` free) + `--load-mode none` RAM | **Primary Local Reasoning & Standby Relief Commander (`<think>` + `reasoning_content` active)** |
| **`10.0.70.72:8000`** *(+ `:8082` NAT)* | `llama-tier1-alpha.service` (`/opt/llama-rpc/llama-server`) | **`Qwen3-Coder-30B-A3B-Instruct-Q4_K_M.gguf`** (`tier1-qwen3-coder-30b-a3b`) | **`262,144` (`256K`)** | `4` (`-kvu`) | **`848.90 tok/s` `pp` / `77.62 tok/s` `tg`** | `1,990 MiB` (`-nkvo`) to `14,863 MiB` T4 VRAM + AVX-512 MoE | **Primary High-Speed Autonomous Coding & Tool-Calling Engine** |
| **`10.0.70.72:18081`** | `llama-qwythos-18081.service` (`/opt/llama-rpc/llama-server`) | **`Qwythos-9B-Claude-Mythos-5-1M-MTP-Q8_0.gguf`** | **`524,288` (`512K`) – `1,048,576` (`1M`)** | `4` (`-kvu`) | **`1,005.86 tok/s` `pp` / `21.39 tok/s` `tg`** | `12,289 MiB` (`512K` GPU) / `10,767 MiB` (`1M` `-nkvo`) / `0 MiB` VRAM co-resident DDR4 | **Ultra-Long-Context (`512K`–`1M`) Repo Ingestion & Mythos Distill** |
| **`10.0.10.137:11434`** | BattleStation Ollama (`RTX 2080 Ti 11 GB` + `Ryzen 9`) | **`CyberStrike-35B-Q4_K_M`** (`66.5 tok/s`), **`Qwen2.5-Coder-7B-16K`** (`95 tok/s`), `qwen2.5vl:7b`, `nomic-embed-text-cpu`, `bge-m3` | **`32,768` – `128,000`** | `4` | **`799.0 tok/s` `pp` / `66.47 tok/s` `tg`** (`CyberStrike-35B`); **`95.0 tok/s` `tg`** (`Coder-7B`) | `8,299 MiB / 11,264 MiB` RTX 2080 Ti VRAM | **Sub-Second Overflow Coder (`0.42s`), OffSec Red-Team & CPU Embeddings (Rule 84)** |
| **`10.0.70.125:11434`** | `ollama.service` (`ai-infer01` Tesla T4) | **`CyberStrike-35B`**, **`Qwen2.5-Coder-32B`**, **`DeepSeek-Coder-V2-16B`**, **`Gemma3-12B`**, **`Mistral-Nemo-12B`**, `qwythos-q8-fixed`, `bge-m3` | **`131,072` (`128K`)** | `2` (`MAX_MODELS=4`) | **`266.51 tok/s` `pp` / `11.27 tok/s` `tg`** (`Coder-32B`); **`66.47 tok/s` `tg`** (`CyberStrike-35B`) | `6,728 MiB / 15,360 MiB` T4 VRAM | **Multi-Model Specialist Pool (OffSec, Polyglot Coding, General Reasoning)** |
| **`10.0.70.228:11434`** | `ollama.service` (`ai-ollama01` Tesla T4) | **`Qwen2.5-Coder-32B`**, **`Qwen2.5-Coder-14B`**, **`Qwen2.5-Coder-7B`**, **`Qwythos`**, **`REAP50` (`reap50-q5-fixed`)** | **`131,072` (`128K`)** | `4` (`NUM_GPU=999`) | **`266.51 tok/s` `pp` / `11.27 tok/s` `tg`** (`32B`); **`0.31s` latency** (`7B`) | `8,292 MiB / 15,360 MiB` T4 VRAM | **Satellite AI Pool, `bd-satellite-router` & Fast Overflow Pool** |
| **`10.0.70.164:4010`** | `bd-gpu-orchestrator.service` | **Sovereign Local Gateway** (Routes `.83:18080`, `.72:8000/:8082`, `.72:18081`, `.137:11434`, `.125:11434`, `.228:11434`) | **`262,144` – `1,048,576`** | `4` (`auto-overflow`) | **`0.37s` – `1.21s` on `76K`–`148K` NIAH prompts** | Host proxy on Controller (`.164`) | **Unified Zero-Guardrail Local Router, Slot-Idle Overflow & 87-Model Hot-Swapper** |
| **`10.0.70.164:4000`** | `litellm-proxy` (Docker) | **Unified LiteLLM Proxy** (`87` Local + `11` Cloud + `16` Custom Agent aliases) | **`262,144` – `1,048,576`** | Unlimited | Sub-second pass-through to `:4010` with fallback chains | Docker container on `.164` | **Cluster-Wide OpenAI API Gateway (`Bearer sk-fleet-local`)** |

### 2.2 Production Launch Flags for Core Hot-Pinned Services

- **`.83:18080` (`llama-heretic-18080.service` — `Qwen3.6-35B-A3B-Heretic`)**:
  ```bash
  /opt/llama-rpc/llama-server \
    -m /mnt/ai-models/qwen3.6-35b-a3b-heretic/Qwen3.6-35B-A3B-Heretic-Q4_K_M.gguf \
    --host 0.0.0.0 --port 18080 --load-mode none \
    -c 262144 -np 4 -kvu --reasoning-preserve --reasoning-format deepseek --jinja \
    --numa isolate -t 24 -ngl 99 -ot ".ffn_.*_exps.=CPU" -fa on -ctk q8_0 -ctv q8_0
  ```
- **`.72:8000` & `:8082` (`llama-tier1-alpha.service` — `Qwen3-Coder-30B-A3B`)**:
  ```bash
  /opt/llama-rpc/llama-server \
    -m /opt/models/tier1-qwen3-coder-30b-a3b/Qwen3-Coder-30B-A3B-Instruct-Q4_K_M.gguf \
    --host 0.0.0.0 --port 8000 \
    -c 262144 -np 4 -kvu --reasoning-preserve --jinja \
    -t 48 -ngl 99 -ot ".ffn_.*_exps.=CPU" -nkvo -fa on -ctk q8_0 -ctv q8_0
  ```
- **`.72:18081` (`llama-qwythos-18081.service` — `Qwythos-9B-1M-MTP` at `512K` / `1M`)**:
  ```bash
  # 512K GPU + CPU Hybrid Mode:
  /opt/llama-rpc/llama-server \
    -m /opt/models/qwythos-9b/Qwythos-9B-Claude-Mythos-5-1M-MTP-Q8_0.gguf \
    --host 0.0.0.0 --port 18081 \
    -c 524288 -np 4 -kvu --reasoning-preserve --jinja \
    -t 48 -ngl 20 -ot ".ffn_.*=CPU" -fa on -ctk q8_0 -ctv q8_0
  ```

---

## 3. All 4 Compiled Inference Engines on `.83` & `.72` (Plus `turboquant` Audit)

All four C++/CUDA engines are compiled specifically for **Intel Skylake-SP `AVX-512` (`GGML_AVX512=ON`, `VBMI=OFF`, `VNNI=OFF`)** and **NVIDIA Turing `sm_75` (`CMAKE_CUDA_ARCHITECTURES=75`)** and deployed on both `10.0.70.83` (`aidev`) and `10.0.70.72` (`ai-srv01`).

### 3.1 Master Comparison Matrix of the 4 Compiled Engines

| # | Install Path (`.83` & `.72`) | Upstream Repo & Commit | Build / Key Flags | Unique Capabilities & Kernel Specializations | Measured A/B on `DeepSeek-V4-Flash-Q2` (`80.76 GiB`, 1xT4 + AVX-512) | **When to Use** |
|---:|---|---|---|---|---|---|
| **1** | **`/opt/llama-rpc/`** | `ggerganov/llama.cpp` build **`b11716`** (`797de63b2`) | `GGML_CUDA=ON` (`sm_75`), `GGML_AVX512=ON`, `GGML_RPC=ON`, `libggml-cpu-skylakex.so` | Multi-node TCP/RoCE `rpc-server` (`:50052`), `--load-mode none`, `-kvu` unified KV pool, `--reasoning-preserve`, `peg-native` parser, `dflash` (`--spec-type dspark --cpu-moe-draft`) support | `pp512 = 6.15 tok/s`<br>`tg128 = 0.09 tok/s` (cold 1xT4);<br>`47.28 / 18.40 tok/s` (multi-GPU warm) | **Default Production Server (`:18080`, `:8000`, `:18081`) & Multi-Node 4xT4 RPC Runs** (`DeepSeek-V4.1 755B`). Highest stability for 24/7 `llama-server` daemons. |
| **2** | **`/opt/ik-llama/`** | `ikawrakow/ik_llama.cpp` commit **`c446f1e`** (`#2692`) | `GGML_CUDA=ON` (`sm_75`), `GGML_AVX512=ON`, `/etc/ld.so.conf.d/ik-llama.conf` | **`-fmoe 1` (Fused MoE), `-mla 3` (Multi-Head Latent Attention), `-khad -vhad` (Hadamard KV rotation)**, `qwen4exp` PLE prefetching, `deepseek2/4/41`, `glm-dsa`, `glm5-next`, `qwen35moe` | **`pp512 = 8.50 tok/s` (`+38.2%`)**<br>`pp128 = 5.11 tok/s`<br>`pp64 = 3.73 tok/s`<br>**`tg8 = 0.18 tok/s` (`2.00x` speedup!)** | **Fastest Single-Node MoE Decode (`2.00x` `tg`) & DeepSeek/GLM Latent Attention (`-fmoe -mla 3`)**. Use for large `deepseek2/4`, `glm-dsa`, `glm5-next`, and `qwen4exp` models with CPU-offloaded routed experts. |
| **3** | **`/opt/gamozo-llama/`** | `gamozolabs/llama.cpp` (`multisocket-cpu-moe`, commit **`9e51941`**) | `gcc-14`/`g++-14`, `GGML_CUDA=ON` (`sm_75`), `GGML_AVX512=ON`, `GGML_OPENMP=OFF` | **`FUSED-MOE-SILU`** (`ggml_compute_forward_fused_moe_silu`): fuses 3-op/5-op/6-op MoE `gate+up+clamp+SiLU+down` across **all 28 `ggml_type` weight formats** (including `MXFP4`, `NVFP4`, `K-quants`, `I-quants`); `GGML_EXPERT_SHARD`, `GGML_EXPERT_REPLICAS`, `GGML_PIN_CORE` | `pp64 = 1.64 tok/s`<br>**`tg8 = 0.11 tok/s` (`+22.2%` over `b11716`)**;<br>Verified `FUSED-MOE-SILU path active` | **Dual-Socket NUMA CPU-MoE & `MXFP4` Models** (`MiMo-V2.6-Flash-311B-MXFP4`, `gpt-oss-120b-MXFP4`) when `n_tokens <= 8` (`ids <= 64`) and cross-socket NUMA expert sharding (`GGML_EXPERT_SHARD=1`) is needed. |
| **4** | **`/opt/llama-master/`** *(alias `/opt/llama-mainline/`)* | `ggerganov/llama.cpp` `master` commit **`31288c9`** (`0.7.0-dev`) | `GGML_CUDA=ON` (`sm_75`), **`GGML_CUDA_FA_ALL_QUANTS=ON`**, `GGML_AVX512=ON`, `GGML_RPC=ON` | **`294` CUDA FlashAttention vector kernels** (all **49** K×V quant combinations: `{f16, bf16, q8_0, q5_1, q5_0, q4_1, q4_0}`) + **`--moe-cache-mib N`** (GPU LRU cache in MiB for CPU-resident MoE experts) | **`pp64 = 4.54 tok/s` (`#1 fastest short-prompt prefill`, `+21.7%` vs `ik`)**<br>**`tg8 = 0.14 tok/s` (`+55.6%` over `b11716`)** | **Asymmetric KV Cache Quants (`-ctk q8_0 -ctv q4_0` / `q5_0` with CUDA FlashAttention) & `--moe-cache-mib N` GPU Expert Caching** when spare T4 VRAM (`2–6 GiB`) can cache hot CPU MoE experts. |

### 3.2 Critical Hardware & Offload Rules Learned (`Agent-163`, `Agent-168`, `Agent-169`, `Agent-171`, `Agent-172`)

1. **Always Keep Shared Experts (`.ffn_.*_shexp.`) and `output.weight` in GPU VRAM**:
   - Use `-ngl 999 -ot ".ffn_.*_exps.=CPU" -ub 512 -ctk q8_0 -ctv q8_0` on a single local Tesla T4 (`NO --rpc` unless weights exceed single-node RAM/VRAM).
   - Lowering `-ub 2048` $\to$ `-ub 512` saves `~1.8–2.4 GiB` of CUDA compute buffer, allowing `100%` of non-routed layers (`49/49` on `Qwen3.8-Flash-Next 63.5 GiB`, `53/53` on `Nemotron-3.5-Lightning 22.8 GiB`, `47/47` on `GLM-5.3-Flash 90.4 GiB`, `33/33` on `Command-A-Plus 125.8 GiB`, `63/63` on `MiniMax-M2.7 128.8 GiB`) to fit inside a single `15,360 MiB` Tesla T4 (`3.06x` decode speedup on `Nemotron-3.5`, `2.43x` on `GLM-5.3`, `1.88x` over 4-GPU RPC on `Qwen3.8-Flash-Next`).
2. **Speculative Drafter (`dspark-DeepSeek-V4-Flash-0731-Q4_K_M.gguf`)**:
   - Quantized to `/mnt/ai-models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-Q4_K_M.gguf` (`11.18 GiB`).
   - Always pass `--model-draft /mnt/ai-models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-Q4_K_M.gguf --spec-type dspark --cpu-moe-draft` (`-cmoed`) so the 9 draft MoE expert tensors (`~9.8 GiB`) stay in host DDR4 while only **`~245 MiB`** of dense draft attention/markov weights reside in Tesla T4 VRAM.
3. **Why `/opt/turboquant/` (`tq3_0` / `turbo3_0`) Is NOT Used for Long-Context VRAM Savings on Turing `sm_75`**:
   - Upstream `tq3_0` (`PR #20969`) has zero CUDA FlashAttention kernel cases (`BEST_FATTN_KERNEL_NONE` $\to$ CPU fallback).
   - `atomicmilkshake/llama-cpp-turboquant` (`turbo3_0`/`turbo4_0` in `/opt/turboquant/`) supports CUDA vector FA only for single-token decode (`Q->ne[1] == 1`), but during prefill (`Q->ne[1] > 1`) on Turing `sm_75` it dispatches `BEST_FATTN_KERNEL_MMA_F16` (`need_f16_K = true, need_f16_V = true`), which allocates and dequantizes a **full uncompressed FP16 copy of the entire KV cache in VRAM**. Use `-fa on -ctk q8_0 -ctv q8_0` (or `ik_llama.cpp` `-khad -vhad`) instead.

---

## 4. Complete 87/87 Model Vault Inventory & Benchmarks (`69` GGUF + `18` Non-GGUF)

- **Sweep Completion**: **`87 / 87 models (`100.0%`)`** across Vault A (`10.0.70.72`) and Vault B (`10.0.70.83`).
- Full tables of all **69 Real-Weight GGUF `llama-bench` Configurations (`rc=0`)** and all **18 Validated Non-GGUF (`Safetensors`/`ONNX`/`Keras`/`Diffusers`) Models** are maintained in [`03-86-MODEL-VAULT-BENCHMARKS-AND-AIRGAP.md`](file:///home/mboyle/bd-persist/bd-kb/03-86-MODEL-VAULT-BENCHMARKS-AND-AIRGAP.md).

---

## 5. All Chat, Coding & Autonomous Swarm Web Apps

Every Boylenet chat and coding portal is wired to the **Unified LiteLLM Proxy (`http://10.0.70.164:4000/v1`)** and **Sovereign Local Gateway (`http://10.0.70.164:4010/v1`)**, with publicly trusted **Let's Encrypt Wildcard HTTPS (`*.boylenet.themfboyles.org`)** via NGINX on `:443` and verified E2E via Dual Chrome CDP Browser Oracles (`.137:9222` & `.181:9222`).

### 5.1 Master Chat & Coding Portals Directory

| Application / Portal | Primary Port(s) & Node | HTTPS Subdomain (`:443`) | Models & Context Configured | Key Features & E2E Verified State (`AGENT129`, `AGENT140`, `AGENT152`, `AGENT153`) |
|---|---|---|---|---|
| **OpenWebUI Primary (Controller)** | **`10.0.70.164:8090`** | `https://openwebui.boylenet.themfboyles.org/` | **`295` Models** (`87` Local Vault + `16` Custom Agents + `8` Pipelines + `11` Cloud + Media), **`256K`–`1M` ctx**, `max_tokens=8192` | **15 Autonomous Agents + `#16` Relief Commander**, readable `[🟢 LOCAL • ...]` / `[☁️ CLOUD • ...]` / `🤖 [AGENT]` model names, wide model selector dropdown, `42` tag filter pills, `51` native toolkits (`:4079` `83/83` routes), `157` skills/prompts, `12` Knowledge Bases (`6` `BD KB` + `6` Qdrant), `28` Open Terminal instances (`All 27 VMs`), collapsible `<think>` traces (`0` hidden schema bloat). |
| **OpenWebUI Compute (Vault A)** | **`10.0.70.72:8080`** | `https://openwebui72.boylenet.themfboyles.org/` | **`295` Models** (synced 1:1 with `:8090`), **`256K`–`1M` ctx**, `max_tokens=8192` | 1:1 SQLite/Agent/Tool/Knowledge parity with `.164:8090`; dedicated compute UI on `ai-srv01`. |
| **LobeChat AI Studio** | **`10.0.70.164:4095`** | `https://lobe.boylenet.themfboyles.org/` | All 7 core sovereign/council models (`<262144:fc:reasoning>`) + full `:4000/v1` catalog | Configured with `OPENAI_PROXY_URL=http://10.0.70.164:4000/v1`, function calling (`fc`), collapsible `<think>` reasoning blocks, and plugin workspace (`HTTP 200`, `7,563B` DOM). |
| **Multi-AI Hub & SSO War-Room** | **`10.0.70.164:4082`** *(SSO/War-Room)* & **`:4098`** *(Multi-Harness Studio)* | `https://sso.boylenet.themfboyles.org/` & `https://multi.boylenet.themfboyles.org/` | Side-by-side 5-Platform (`Claude`, `Codex`, `Kimi`, `AGY`, `Local Heretic`) at `256K` ctx | Zero-Friction SSO, QR Handoff, Root CA Trust (`:4082`) + Multi-Harness AI Command & Reasoning Studio (`:4098`, `277,404B` DOM) with real-time Council Relief Watch. |
| **OpenCode Web IDE** | **`10.0.70.164:4091`** *(Rack/IDE index)* & **`:4096`** *(Live OpenCode Web)* | `https://opencode.boylenet.themfboyles.org/` | `114` models (`boylenet-litellm`) + `95` models (`boylenet-orchestrator`) at `context: 262144, output: 16384` | Browser-native Agentic Coding IDE (`bd-opencode-web.service`) with `--cors https://opencode.boylenet.themfboyles.org`, direct git worktree access, and `<think>` streaming. |
| **OpenClaw Control UI** | **`10.0.70.164:4097`** | `https://openclaw.boylenet.themfboyles.org/` | 7 core sovereign/council models (`contextWindow: 262144, maxTokens: 16384, reasoning: true`) | Autonomous multi-session coding control plane (`248,604B` DOM, `models.list` WebSocket `52,336B` in `396ms`). |
| **LibreChat** | **`10.0.70.164:3080`** | `https://librechat.boylenet.themfboyles.org/` | Multi-endpoint `:4000/v1` & `:4010/v1` (`256K` ctx) | Multi-user conversation branching, presets, and artifact code preview wired to LiteLLM `:4000`. |
| **SillyTavern** | **`10.0.70.164:4096`** *(shared/alt profile)* | `https://tavern.boylenet.themfboyles.org/` | Uncensored / Abliterated / Heretic Vault models (`Qwen3.6-Heretic`, `CyberStrike-35B`, `Hemmingway-27B`) | Advanced prompt formatting, character cards, lorebooks, and raw sampler control via `:4010/v1`. |
| **OpenHands & Jarvis Cockpit** | **`10.0.70.164:3002`** *(Jarvis/OpenHands)* & **`:4090`** *(Burst/Jarvis Bridge)* | `https://jarvis.boylenet.themfboyles.org/` & `https://burst.boylenet.themfboyles.org/` | `agent-master-coder`, `tier1-qwen3-coder-30b-a3b`, `qwen3.6-35b-a3b-heretic` (`256K` ctx) | Heterogeneous Swarm Architecture Dashboard (`447,016B` DOM on `:3002`) + Hybrid Cloud/Local Burst Orchestrator (`:4090`). |
| **Fleet Chat PWA & Gitea Pre-Review** | **`10.0.70.164:4086`** *(Fleet Chat PWA)* & **`:4092`** *(Pre-Review Swarm)* | `https://fleetchat.boylenet.themfboyles.org/` & `https://prereview.boylenet.themfboyles.org/` | `agent-council-relief-commander`, `CyberStrike-35B`, `Qwen3.6-Heretic` | Mobile Command Deck & Episodic Memory Dreamer (`:4086`, `30,867B` DOM) + Gitea Pre-Review & OffSec Red-Team Swarm (`:4092`). |
| **NL Ops, Multimodal & Deep Research** | **`10.0.70.164:4089`** (NL Ops), **`:4093`** (Voice/Vision), **`:4094`** (Research) | `https://nlops...`, `https://multimodal...`, `https://research.boylenet.themfboyles.org/` | `Qwen3.6-Heretic`, `MiniCPM-V-4.7-35B`, `Whisper/TTS/Flux` (`:4079/v1`) | Natural-Language SSH Ops Shell (`:4089`), Voice/Vision Multimodal Bridge (`:4093`), and Dual-Oracle Citation Studio (`:4094`). |

### 5.2 All 16 Pre-Wired Autonomous Agents in OpenWebUI (`.164:8090` & `.72:8080`)

All 16 `agent-*` presets are synced across both OpenWebUI instances with `max_tokens=8192`, `<think>` enabled, Qdrant RAG grounding (`10.0.70.125:6333`), and `:4079` Universal Tool Gateway integration (`83/83` endpoints `HTTP 200`):

1. **`agent-council-relief-commander`** (`#16` Flagship Standby Commander) -> `qwen3.6-35b-claude-opus-abliterated` (`256K` ctx + `<think>`)
2. **`agent-master-coder`** -> `qwen3.6-35b-claude-opus-abliterated` (`python_ruff_mypy_check` + `boylenet_codebase_ast`)
3. **`agent-instant-sprinter`** -> `qwen2.5-coder:7b-16k` on `.137:11434` (`run_command` + `bd_master_kb`)
4. **`agent-deep-researcher`** -> `deep_research_multi_engine` + `swarm_docs_rag`
5. **`agent-offsec-redteam`** -> `cyber_cve_security_audit` + `boylenet_fleet_rag`
6. **`agent-vsan-infra-architect`** -> `Qwen3.6-35B-A3B-Heretic` (`vmware_esxi_vsan_control` + `bd_master_kb`)
7. **`agent-bdkb-ci-doctor`** -> `ci_taxonomy_doctor` + `bd_master_kb`
8. **`agent-media-voice-studio`** -> `render_architecture_diagram` / `flux_1080p_image_hud_generator`
9. **`agent-data-sql-analyst`** -> `sql_database_query` (`PostgreSQL` `boylenet_swarm_ledger`)
10. **`agent-mcp-skill-commander`** -> `Qwen3.6-35B-A3B-Heretic` (`mcp_list` / `mcp_call` across 14 MCP servers)
11. **`agent-airgap-sovereign`** -> `airgap_and_cluster_status` + `bd_master_kb`
12. **`agent-4model-council`** -> `Qwen3.6-35B-A3B-Heretic` (`bd_bus_wal_and_smt_consensus` + `fleet_lessons_hivemind`)
13. **`agent-claude-opus-integrator`** -> `git_worktree_patch_auditor` + `bd_master_kb`
14. **`agent-codex-sol-verifier`** -> `ast_slicer_serena` + `boylenet_codebase_ast`
15. **`agent-kimi-ux-swarm`** -> `browse_live_page_cdp` (`.137:9222` & `.181:9222`)
16. **`agent-o1740-smart-router`** -> `Qwen3.6-35B-A3B-Heretic` (`o1740_pool_and_seat_router` + `POOL_STATE.tsv`)

---

## 6. Standby Local Takeover / Relief Commander Protocol (`$0.00` Cloud-Exhaustion Failover)

When external cloud pools (`AGY`, `Claude`, `Codex`, `Kimi`) reach quota limits (`OFF-PACE` or `LIMITED` in `/home/mboyle/bd-persist/POOL_STATE.tsv`) or when `bd-airgap on` is engaged, the Boylenet Swarm automatically fails over to the **Hot-Pinned Local GPU Triad** with zero downtime:

1. **Primary Local Relief Commander & Architect (`256K` Context + `<think>`)**:
   - **Model**: `Qwen3.6-35B-A3B-Heretic-Q4_K_M.gguf` (`qwen3.6-35b-a3b-heretic` / `qwen3.6-35b-claude-opus-abliterated` / `agent-council-relief-commander`)
   - **Direct GPU Endpoint**: `http://10.0.70.83:18080/v1/chat/completions` (`n_ctx = 262,144`, `4` slots, `--reasoning-preserve`)
   - **Sovereign Gateway Endpoint**: `http://10.0.70.164:4010/v1/chat/completions`
   - **LiteLLM Gateway Endpoint**: `http://10.0.70.164:4000/v1/chat/completions` (`Authorization: Bearer sk-fleet-local`)
2. **Primary Local Master Coder & Patch Builder (`256K` Context + `<think>`)**:
   - **Model**: `Qwen3-Coder-30B-A3B-Instruct-Q4_K_M.gguf` (`tier1-qwen3-coder-30b-a3b` / `agent-master-coder`)
   - **Direct GPU Endpoint**: `http://10.0.70.72:8000/v1/chat/completions` (and `:8082/v1/chat/completions`) (`n_ctx = 262,144`, `4` slots, `848.9 tok/s` prefill / `77.6 tok/s` decode)
3. **Sub-Second Slot-Saturation Overflow (`<0.45s` TTFT)**:
   - When all 4 slots on `.83:18080` or `.72:8000` are busy with `76K`–`148K` token context prefills, `:4010` (`bd-gpu-orchestrator.py`) automatically detects `heretic_83_idle == False` / `coder_72_idle == False` via `/slots` and overflows interactive turns to `10.0.10.137:11434` (`qwen2.5-coder:7b-16k` on RTX 2080 Ti, `0.42s`) and `10.0.70.228:11434` (`qwen2.5-coder:7b` on Tesla T4, `0.31s`).

### 6.1 Exact Commands for Automatic & Manual Local Takeover

#### A. Dispatch Autonomous Local Coding & `.82` 64-Core Ramdisk Pytest Self-Healer (`$0` Cost)
```bash
# 1. Dispatch Qwen3.6-35B-A3B-Heretic (.83:18080, 256K ctx + <think>) as Local Relief Coder:
/home/mboyle/bin/bd-local-coding-agent \
  --backend t4-heretic \
  --task "Verify, fix, and gate open queue cut with 64-core ramdisk pytest" \
  --target-dir /home/mboyle/bd-local-wt/active-cut

# 2. Dispatch Qwen3-Coder-30B-A3B (.72:8000, 256K ctx, 77.6 tok/s) for Fast Multi-File Edits:
/home/mboyle/bin/bd-local-coding-agent \
  --backend tier1-alpha \
  --task "Implement feature cut and run ruff + mypy + bd-pytest-digest" \
  --target-dir /home/mboyle/bd-local-wt/active-cut
```

#### B. Engage Full WAN Airgap (`100%` Local Sovereign Mode)
```bash
# Block all external cloud LLM APIs (OpenAI, Anthropic, Google) while keeping 10.0.0.0/8 LAN 100% active:
/home/mboyle/bin/bd-airgap on
/home/mboyle/bin/bd-airgap status
```

#### C. Direct `curl` / API Invocation for Standby Takeover (`256K` Context + `<think>`)
```bash
# 1. Invoke Standby Relief Commander via Sovereign Local Gateway (:4010):
curl -sS http://10.0.70.164:4010/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "agent-council-relief-commander",
    "messages": [
      {"role": "user", "content": "Assume Relief Commander watch. Audit POOL_STATE.tsv and active queues."}
    ],
    "max_tokens": 4096
  }' | jq -r '.choices[0].message | "<think>\n" + (.reasoning_content // "") + "\n</think>\n\n" + .content'

# 2. Invoke Qwen3.6-35B-A3B-Heretic Directly on Node .83:18080 (256K Context):
curl -sS http://10.0.70.83:18080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen3.6-35b-a3b-heretic",
    "messages": [{"role": "user", "content": "Verify 256K context and <think> reasoning trace."}],
    "max_tokens": 2048
  }'

# 3. Invoke Qwen3-Coder-30B-A3B Directly on Node .72:8000 (256K Context):
curl -sS http://10.0.70.72:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "tier1-qwen3-coder-30b-a3b",
    "messages": [{"role": "user", "content": "Generate a hermetic pytest fixture for git worktree validation."}],
    "max_tokens": 2048
  }'
```

#### D. Claim Council / PM Seat for Cloud Pool `C` (`claude-opus-5-5` `1M`) or Local Heretic Relief
```bash
# Hand off Council / PM watch to Pool C (99% weekly quota free) + Local Heretic co-pilot:
BD_SEAT=bd-claude-swarm-C /home/mboyle/bd-persist/harness/bd-role-claim.sh claim council bd-claude-swarm-C
/home/mboyle/bd-persist/harness/bd-say.sh bd-claude-swarm-C "RELIEF HANDOFF: Read /home/mboyle/bd-persist/bd-kb/09-LLM-ENGINES-CHAT-APPS-AND-LOCAL-TAKEOVER-GUIDE.md — assume council relief."
```
