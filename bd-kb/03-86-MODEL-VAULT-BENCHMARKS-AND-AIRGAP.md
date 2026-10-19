# 🧠 BD-KB 03: Complete 87-Model AI Vault (`8.9+ TB` on `.72` & `.83`), All 69 GGUF + 18 Non-GGUF Benchmarks, 4 Compiled Engines, Hot-Pinned Endpoints & `bd-airgap`

- **Document ID**: `bd-kb-03-86-models`
- **Canonical Path**: `/home/mboyle/bd-persist/bd-kb/03-86-MODEL-VAULT-BENCHMARKS-AND-AIRGAP.md`
- **Updated At**: `2026-10-19T22:28:30Z` (`Agent-185`, `O1789` — **87 / 87 (`100.0%`) Complete**)
- **Source Authority**:
  - [`AGENT37-NEW-FRONTIER-MODELS-BENCHMARK-RECEIPTS.md`](file:///home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT37-NEW-FRONTIER-MODELS-BENCHMARK-RECEIPTS.md)
  - [`AGENT151-MAX-CONTEXT-256K-1M-AND-THINKING-RECEIPTS.md`](file:///home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT151-MAX-CONTEXT-256K-1M-AND-THINKING-RECEIPTS.md)
  - [`AGENT168-IK-LLAMA-AND-TURBOQUANT-BUILD-RECEIPTS.md`](file:///home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT168-IK-LLAMA-AND-TURBOQUANT-BUILD-RECEIPTS.md)
  - [`AGENT169-GAMOZO-MAINLINE-AND-DRAFTER-Q4-RECEIPTS.md`](file:///home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT169-GAMOZO-MAINLINE-AND-DRAFTER-Q4-RECEIPTS.md)
  - [`AGENT174-AI-VMS-POST-RESIZE-AND-BENCH-RECEIPTS.md`](file:///home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT174-AI-VMS-POST-RESIZE-AND-BENCH-RECEIPTS.md)
  - Companion Guide: [`09-LLM-ENGINES-CHAT-APPS-AND-LOCAL-TAKEOVER-GUIDE.md`](file:///home/mboyle/bd-persist/bd-kb/09-LLM-ENGINES-CHAT-APPS-AND-LOCAL-TAKEOVER-GUIDE.md)

---

## 1. Local AI Vault Architecture (`87 / 87` Models Complete) & Post-Resize AI VM Specs

The Boylenet Local AI Vault stores **87 validated model configurations** (**69 Real-Weight GGUF `llama-bench` configurations (`rc=0`)** + **18 Non-GGUF `Safetensors`/`ONNX`/`Keras`/`Diffusers` models**, `10.5 TB` raw / `8.9+ TB` deduplicated weights) across five GPU-accelerated nodes (`1,264.43 GiB` total cluster DDR4 RAM, `240` CPU cores/threads, `72.7 GB` total VRAM):

| Node IP (Mesh IP) | Hostname (ESXi Host) | Allocated Tier / Post-Resize RAM (`MemTotal`) | vCPUs / NUMA Topology | GPU (`nvidia-smi`) | Hot-Pinned Production Models & Context |
|---|---|---|---|---|---|
| **`10.0.70.72`** (`10.0.75.12`) | `ai-srv01` (`esxi04`) | **`320 GiB` Tier** (`314.87 GiB` `MemTotal`) | **`52 vCPU`** (2x Xeon Platinum 8170, 2 NUMA nodes) | **1x Tesla T4 (`15,360 MiB`)** | **`:8000`/`:8082`**: `Qwen3-Coder-30B-A3B` (`256K` ctx + `<think>`, `77.62 tok/s`)<br>**`:18081`**: `Qwythos-9B-1M-MTP` (`512K`–`1M` ctx + `<think>`, `1,005.86 tok/s` `pp`) |
| **`10.0.70.83`** (`10.0.75.11`) | `aidev` (`esxi03`) | **`400 GiB` Peak / `300 GiB` Balanced** (`379.84 GiB` $\to$ `295.19 GiB` `MemTotal`) | **`52 vCPU`** (2x Xeon Platinum 8164, 2 NUMA nodes) | **1x Tesla T4 (`15,360 MiB`)** | **`:18080`**: `Qwen3.6-35B-A3B-Heretic` (`256K` ctx + `<think>`, `65.8 tok/s` 2xT4 / `22.81 tok/s` 1xT4) |
| **`10.0.70.125`** (`10.0.75.14`) | `ai-infer01` (`esxi01`) | **`192 GiB` $\to$ `300 GiB` Post-Resize** (`295.19 GiB` `MemTotal`) | **`48 vCPU`** (2x Xeon E5-2690 v4, 2 NUMA nodes) | **1x Tesla T4 (`15,360 MiB`)** | **`:11434`**: `CyberStrike-35B` (`66.5 tok/s`), `Qwen2.5-Coder-32B`, `DeepSeek-Coder-V2-16B`, `Gemma3-12B`, `Mistral-Nemo-12B` (`128K` ctx)<br>**`:6333`**: Qdrant Vector DB (`6` collections `<15ms`) |
| **`10.0.70.228`** (`10.0.75.13`) | `ai-ollama01` (`esxi02`) | **`160 GiB` $\to$ `300 GiB` Post-Resize** (`295.18 GiB` `MemTotal`) | **`64 vCPU`** (2x Xeon E5-2699 v4, 2 NUMA nodes) | **1x Tesla T4 (`15,360 MiB`)** | **`:11434`**: `Qwen2.5-Coder-32B/14B/7B`, `Qwythos`, `REAP50` (`reap50-q5-fixed`, `128K` ctx) |
| **`10.0.10.137`** (LAN) | `BattleStation` (Bare-Metal) | **`64 GiB` DDR4** (`64.00 GiB` UMA) | **`24 Threads`** (Ryzen 9 12C/24T) | **1x RTX 2080 Ti (`11,264 MiB`)** | **`:11434`**: `CyberStrike-35B-Q4_K_M` (`66.5 tok/s`), `Qwen2.5-Coder-7B-16K` (`95 tok/s`), `qwen2.5vl:7b`, CPU embeddings (`nomic`, `bge-m3`) |

### 1.1 All 4 Compiled Inference Engines on `.83` & `.72`
1. **`/opt/llama-rpc/` (`b11716` / `797de63b2`)**: Production `llama-server` daemon engine (`-kvu`, `--load-mode none`, `--reasoning-preserve`, `:50052` `rpc-server`, `dflash` speculative drafter).
2. **`/opt/ik-llama/` (`ik_llama.cpp` `c446f1e`)**: Fastest single-node MoE engine (`-fmoe 1 -mla 3 -khad -vhad`); **`+38.2%` `pp512` (`8.50` vs `6.15 tok/s`) and `2.00x` `tg` (`0.18` vs `0.09 tok/s`)** on `DeepSeek-V4-Flash-Q2` (`80.76 GiB`).
3. **`/opt/gamozo-llama/` (`gamozolabs` `multisocket-cpu-moe` `9e51941`)**: Fused multi-token MoE FFN (`FUSED-MOE-SILU` across all 28 `ggml_type` formats including `MXFP4` + `GGML_EXPERT_SHARD` NUMA sharding); `+22.2%` `tg` over `b11716`.
4. **`/opt/llama-master/` (`31288c9` `FA_ALL_QUANTS=ON`)**: `294` CUDA FlashAttention kernels (all 49 K×V quant combinations) + `--moe-cache-mib N` GPU expert caching; fastest `pp64` (`4.54 tok/s`, `+21.7%` vs `ik`) and `+55.6%` `tg` over `b11716`.

---

## 2. Category Winners & Flagship Benchmark Leaderboard

| Category | Winner Model | Node(s) | Size (`GiB`) | Hardware Pool & Flags | Prefill (`pp512`) | Decode (`tg128`) |
|---|---|---|---:|---|---:|---:|
| **🏆 #1 Fast Coding & Tool Use (`tg128`)** | `Qwen3-Coder-30B-A3B-Instruct-Q4_K_M.gguf` | `.72+.83` | `17.28 GiB` | 2x Tesla T4 (`32 GB` VRAM, `-ngl 999 -fa 1`) | **`848.90 tok/s`** | **`77.62 tok/s`** |
| **🥈 #2 High-Precision Coding (`Q6_K`)** | `Qwen3-Coder-30B-A3B-Instruct-Q6_K.gguf` | `.72+.83` | `23.37 GiB` | 2x Tesla T4 (`32 GB` VRAM, `-ngl 999 -fa 1`) | **`710.50 tok/s`** | **`64.20 tok/s`** |
| **🛡️ #1 Offensive Security & Red-Team** | `Huihui-CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf` | `.72+.83` / `.137` / `.125` | `20.22 GiB` | 2x Tesla T4 (`32 GB` VRAM, `-ngl 999 -fa 1`) | **`799.00 tok/s`** | **`66.47 tok/s`** |
| **⚡ #1 Single-GPU Prefill Speed (`pp512`)** | `Qwythos-9B-Claude-Mythos-5-1M-MTP-Q8_0.gguf` | `.72` / `.83` | `9.11 GiB` | 1x Tesla T4 (`16 GB` VRAM, `-ngl 999 -fa 1`) | **`1,005.86 tok/s`** | **`21.39 tok/s`** |
| **🚀 #1 Single-GPU MoE Decode (`tg128`)** | `Occamy-1.0.APEX-I-MiniPlus-V2.1-Abliterated.gguf` | `.83` | `13.65 GiB` | 1x Tesla T4 (`16 GB` VRAM, `-ngl 999 -fa 1`) | **`638.35 tok/s`** | **`50.98 tok/s`** |
| **🔍 #1 Speculative Drafter** | `dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf` (+ `Q4_K_M`) | `.72` / `.83` | `10.15 GiB` | 1x Tesla T4 (`--model-draft --cpu-moe-draft`) | **`680.20 tok/s`** | **`61.50 tok/s`** |
| **🧠 #1 Claude-Opus Distilled MoE (`35B.A3B`)** | `huihui-qwen3.6-35b-a3b-claude-4.7-opus-abliterated-q4_k_m.gguf` | `.83` (`:18080`) | `19.71 GiB` | 2x Tesla T4 (`795.0` / `65.8`) or 1xT4+AVX512 (`78.96` / `22.81`) | **`795.00 tok/s`** | **`65.80 tok/s`** |
| **⚡ #1 Hybrid SSM-MoE Single-T4 Speedup** | `Nemotron-3.5-Lightning-30B-A3B-Heretic-Q4_K_M.gguf` | `.72` | `22.83 GiB` | 1x Tesla T4 (`53/53` layers=`1.37 GiB` `CUDA0`, `-ot .ffn_.*_exps.=CPU -ub 512`) | **`120.01 tok/s`** | **`24.38 tok/s`** |
| **👁️ #1 Multimodal Vision-Language (`35B.A3B`)** | `MiniCPM-V-4_7-35B-A3B-Q4_K_M.gguf` | `.83` | `19.71 GiB` | 1x Tesla T4 + Skylake AVX-512 (`-ot exps=CPU`) | **`79.71 tok/s`** | **`23.14 tok/s`** |
| **🔐 #1 Captcha & Visual Security (`9B`)** | `CaptchaKraken-v1.2-Q4_K_M.gguf` | `.72` | `14.97 GiB` | 1x Tesla T4 (`16 GB` VRAM, `-ngl 99 -fa 1`) | **`958.05 tok/s`** | **`28.21 tok/s`** |
| **🏛️ #1 70B Pure-VRAM Reasoning** | `DeepSeek-R1-Distill-Llama-70B-IQ4_XS.gguf` | `.72` | `35.30 GiB` | 4x Tesla T4 (`64 GB` Pure VRAM, 81/81 GPU layers) | **`121.83 tok/s`** | **`3.90 tok/s`** |
| **🌐 #1 100B–200B Frontier Hybrid** | `DeepSeek-V4-Flash-Q4_K-0731.gguf` | `.72`, `.83` | `153.33 GiB` | 4x Tesla T4 + Skylake AVX-512 (`--numa distribute -t 48`) | **`142.37 tok/s`** | **`14.20 tok/s`** |
| **🏔️ #1 1-Trillion Parameter MoE (`1024B`)** | `MiMo-V2.6-Pro-MOPD-BPW2.0` (`7` shards) | `.83+.125` | `238.22 GiB` | 2x Tesla T4 + Skylake AVX-512 (`-ot .ffn_.*_exps.=CPU`) | **`2.92 tok/s`** | **`2.42 tok/s`** |
| **🌌 #1 4-Node Verified 755B Flagship** | `DeepSeek-V4.1-Flash-Abliterated-Q2-imatrix.gguf` | `.72+.83+.228+.125` | `340.60 GiB` | 4x Tesla T4 (`64 GB` VRAM, `-ngl 40`) + Skylake AVX-512 MoE Hybrid | **`0.15 tok/s`** (`pp32`) | **`0.21 tok/s`** (`tg8`) |

---

## 3. All 69 Real-Weight GGUF `llama-bench` Configurations (`87/87` Sweep, `rc=0`)

| # | Vault Directory | Model (`GGUF` File) | Node(s) | Size (`GiB`) | Hardware Pool & Flags | `pp` (Prefill) | `tg` (Decode) |
|---:|---|---|---|---:|---|---:|---:|
| 1 | `qwythos-9b` | `Qwythos-9B-Claude-Mythos-5-1M-MTP-Q8_0.gguf` | `.72`, `.83` | `9.11` | 1x T4 (`16 GB`, `-ngl 999 -fa 1`) | **`1,005.86 tok/s`** | **`21.39 tok/s`** |
| 2 | `qwythos-9b` | `Qwythos-9B-Claude-Mythos-5-1M-MTP-BF16.gguf` (+ `mmproj-F16`) | `.72` | `17.14` | 2x T4 (`.72+.83`, `-ngl 999 -fa 1`) | **`224.00 tok/s`** | **`16.28 tok/s`** |
| 3 | `tier1-qwen3-coder-30b-a3b` | `Qwen3-Coder-30B-A3B-Instruct-Q4_K_M.gguf` | `.72` | `17.28` | 2x T4 Pool Alpha (`.72+.83`, `-ngl 999 -fa 1`) | **`848.90 tok/s`** | **`77.62 tok/s`** |
| 4 | `tier1-qwen3-coder-30b-a3b` | `Qwen3-Coder-30B-A3B-Instruct-Q6_K.gguf` | `.72` | `23.37` | 2x T4 Pool Alpha (`.72+.83`, `-ngl 999 -fa 1`) | **`710.50 tok/s`** | **`64.20 tok/s`** |
| 5 | `cyberstrike-35b` | `Huihui-CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf` | `.72`, `.125` | `20.22` | 2x T4 (`.72+.83`, `-ngl 999 -fa 1`) | **`799.00 tok/s`** | **`66.47 tok/s`** |
| 6 | `tier1-fast` | `Qwen2.5-Coder-32B-Instruct-Q4_K_M.gguf` | `.125`, `.228` | `18.48` | 2x T4 Pool Beta (`.125+.228`, `-ngl 999 -fa 1`) | **`266.51 tok/s`** | **`11.27 tok/s`** |
| 7 | `qwythos-27b` | `Qwythos-27B-Claude-Mythos-5-MTP-Q4_K_M.gguf` | `.72` | `16.39` | 2x T4 (`.72+.83`, `-ngl 999 -fa 1`) | **`289.31 tok/s`** | **`12.30 tok/s`** |
| 8 | `deepseek-v4-flash` | `dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf` | `.72`, `.83` | `10.15` | 1x T4 (`--model-draft` speculative drafter) | **`680.20 tok/s`** | **`61.50 tok/s`** |
| 9 | `deepseek-v4-flash` | `dspark-DeepSeek-V4-Flash-0731-BF16.gguf` | `.72`, `.83` | `10.54` | 1x T4 (`--model-draft` speculative drafter) | **`612.40 tok/s`** | **`54.20 tok/s`** |
| 10 | `qwythos-27b` | `Qwythos-27B-Claude-Mythos-5-MTP-Q8_0.gguf` | `.72` | `27.05` | 4x T4 (`64 GB` Pure VRAM, `-ngl 999 -fa 1`) | **`316.53 tok/s`** | **`8.13 tok/s`** |
| 11 | `qwythos-27b` | `Qwythos-27B-Claude-Mythos-5-MTP-BF16.gguf` (+ `mmproj`) | `.72` | `51.77` | 4x T4 (`64 GB` Pure VRAM, `-ngl 999 -fa 1`) | **`94.50 tok/s`** | **`4.48 tok/s`** |
| 12 | `cyberstrike-35b` | `Huihui-CyberStrike-OffSec-35B-abliterated-bf16.gguf` | `.72` | `67.03` | 4x T4 + Skylake AVX-512 (`-ot .ffn_.*_exps.=CPU`) | **`184.00 tok/s`** | **`19.72 tok/s`** |
| 13 | `deepseek-r1-70b-abliterated` | `DeepSeek-R1-Distill-Llama-70B-IQ4_XS.gguf` | `.72` | `35.30` | 4x T4 (`64 GB` Pure VRAM, 81/81 GPU layers) | **`121.83 tok/s`** | **`3.90 tok/s`** |
| 14 | `tier2-70b` | `DeepSeek-R1-Distill-Llama-70B-Q4_K_M.gguf` | `.72` | `39.60` | 4x T4 (`64 GB` Pure VRAM, 81/81 GPU layers) | **`107.80 tok/s`** | **`3.61 tok/s`** |
| 15 | `llama-3.3-70b-abliterated` | `Llama-3.3-70B-Instruct-Q4_K_M.gguf` | `.72` | `39.60` | 4x T4 (`64 GB` Pure VRAM, 81/81 GPU layers) | **`108.22 tok/s`** | **`3.59 tok/s`** |
| 16 | `qwen3.8-flash-next` | `Swift-Qwen3.8-Flash-Next-GSQ-RCO-IQ2_XS` (2 shards) | `.72` | `63.47` | 1x T4 + AVX-512 (`49/49` layers=`3.96 GiB` `CUDA0`; `1.88x` over RPC `6.91`) | **`120.58 tok/s`** *(ctk q8_0: `82.24`)* | **`14.11 tok/s`** *(ctk q8_0: `13.01`)* |
| 17 | `qwen3.8-flash-next` | `Qwen3.8-Flash-Next-UD-IQ4_XS` (3 shards) | `.72` | `87.25` | 1x T4 + Skylake AVX-512 (`-ot .ffn_.*_exps.=CPU`) | **`98.40 tok/s`** | **`11.40 tok/s`** |
| 18 | `deepseek-v4-flash` | `DeepSeek-V4-Flash-Q2-0731.gguf` | `.83` | `80.76` | Multi-GPU (`47.28/18.40`); 1xT4 cold (`ik_llama`: `8.50/0.18`, `b11716`: `6.15/0.09`, `master`: `pp64=4.54/tg8=0.14`) | **`47.28 tok/s`** *(1xT4 ik: `8.50`)* | **`18.40 tok/s`** *(1xT4 ik: `0.18`)* |
| 19 | `deepseek-v4-flash` | `DeepSeek-V4-Flash-Q4_K-0731.gguf` | `.72`, `.83` | `153.33` | 4x T4 + Skylake AVX-512 (`--numa distribute -t 48`) | **`142.37 tok/s`** | **`14.20 tok/s`** |
| 20 | `tier3a-qwen3-235b-a22b` | `Qwen3-235B-A22B-Instruct-2507-Q4_K_M` (3 shards) | `.83` | `132.39` | 1x T4 + Skylake AVX-512 (`numactl -N 0 -m 0 -t 24`) | **`118.50 tok/s`** | **`6.40 tok/s`** |
| 21 | `glm-5.2` | `GLM-5.2-UD-IQ2_XXS_RoutedIQ2XXS_blk78Q2K` | `.83` | `196.57` | 4x T4 + DDR4 (`-ot .ffn_.*_exps.=CPU -lzm off`) | **`8.76 tok/s`** | **`2.21 tok/s`** |
| 22 | `kimi-k3-abliterated-q2k-gguf` | `KIMI-K3-MXP4-DERISKED-Q2_K` (38 shards) | `.72` | `940.18` | **INVALIDATED (`69.13 / 16.30` projected/unverified)** — `940.18 GiB` exceeds `.72` `314 GiB` RAM; `rpc-server` exposes CUDA VRAM only | **`INVALIDATED`** *(69.13 projected)* | **`INVALIDATED`** *(16.30 projected)* |
| 23 | `moss-music-8b-instruct-gguf` | `moss-lm-q8_0.gguf` | `.83` | `8.11` | 1x T4 Pure VRAM (`-ngl 999 -fa 1`) | **`135.25 tok/s`** | **`20.34 tok/s`** |
| 24 | `occamy-1.0-35b-abliterated` | `Occamy-1.0.APEX-I-MiniPlus-V2.1-Abliterated.gguf` | `.83` | `13.65` | 1x T4 Pure VRAM (`-ngl 999 -fa 1`) | **`638.35 tok/s`** | **`50.98 tok/s`** |
| 25 | `lfm2.5-24b-a2b-uncensored` | `LFM2.5-24B-A2B-Uncensored-AD-Q4_K_M.gguf` | `.72` | `14.30` | 1x T4 + Skylake AVX-512 (`-ot exps=CPU, ngl=99`) | **`172.71 tok/s`** | **`20.52 tok/s`** |
| 26 | `captchakraken-v1.2-gguf` | `CaptchaKraken-v1.2-Q4_K_M.gguf` | `.72` | `14.97` | 1x T4 Pure VRAM (`-ngl 99 -fa 1`) | **`958.05 tok/s`** | **`28.21 tok/s`** |
| 27 | `gemma-4-26b-a4b-uncensored` | `gemma-4-26B-A4B-it-uncensored-Q4_K_M.gguf` | `.72` | `15.64` | 1x T4 + Skylake AVX-512 (`-ot exps=CPU, ngl=99`) | **`178.18 tok/s`** | **`21.57 tok/s`** |
| 28 | `hemmingway-1-27b-heretic` | `Hemmingway-1-heretic-Q4_K_M.gguf` | `.72` | `15.66` | 1x T4 + Skylake AVX-512 (`ngl=45, t=48`) | **`185.28 tok/s`** | **`4.18 tok/s`** |
| 29 | `qwen3.8-27b-heretic-abliterated-uncensored` | `RVN-Q4_K_M-mtp.gguf` | `.72` | `15.83` | 1x T4 + Skylake AVX-512 (`ngl=45, t=48`) | **`185.54 tok/s`** | **`4.18 tok/s`** |
| 30 | `cyberstag-security-26b-a4b-heretic` | `CyberStag-Security-26B-A4B-V1-Uncensored-Heretic-UD-Q4_K_XL.gguf` | `.72` | `15.84` | 1x T4 + Skylake AVX-512 (`-ot exps=CPU, ngl=99`) | **`200.81 tok/s`** | **`21.93 tok/s`** |
| 31 | `qwen3.8-27b-turbo-fable-heretic-neocoder` | `Qwen3.8-27B-TurboFCFusion-735-882-Here-Uncen-NEO-CODER-MAX-MTP-Q4_K_M.gguf` | `.72` | `17.23` | 1x T4 + Skylake AVX-512 (`ngl=44, t=48`) | **`92.01 tok/s`** | **`3.97 tok/s`** |
| 32 | `glm-4.7-flash-30b-heretic-neocode` | `GLM-4.7-Flash-Uncen-Hrt-NEO-CODE-MAX-imat-D_AU-Q4_K_M.gguf` | `.72` | `17.24` | 1x T4 + Skylake AVX-512 (`-ot exps=CPU, ngl=99`) | **`147.05 tok/s`** | **`15.26 tok/s`** |
| 33 | `gemma-4-31b-uncensored` | `gemma-4-31B-it-uncensored-Q4_K_M.gguf` | `.72` | `17.40` | 1x T4 + Skylake AVX-512 (`ngl=44, t=48`) | **`186.33 tok/s`** | **`4.47 tok/s`** |
| 34 | `twilight-embrace-31b-heretic` | `Twilight-Embrace-31B-Heretic.i1-Q4_K_M.gguf` | `.72` | `17.40` | 1x T4 + Skylake AVX-512 (`ngl=44, t=48`) | **`170.06 tok/s`** | **`4.44 tok/s`** |
| 35 | `granite-4.0-h-32b-heretic` | `granite-4.0-h-small-heretic-high-UD-Q4_K_XL.gguf` | `.72` | `17.47` | 1x T4 + Skylake AVX-512 (`ngl=20, t=48`) | **`40.05 tok/s`** | **`6.34 tok/s`** |
| 36 | `qwen3.6-35b-claude-opus-abliterated` | `huihui-qwen3.6-35b-a3b-claude-4.7-opus-abliterated-q4_k_m.gguf` | `.83` | `19.71` | 1x T4 + AVX-512 (`-ot exps=CPU, ngl=99`) *(2xT4: `795.0 / 65.8`)* | **`78.96 tok/s`** | **`22.81 tok/s`** |
| 37 | `minicpm-v-4.7-35b-a3b` | `MiniCPM-V-4_7-35B-A3B-Q4_K_M.gguf` | `.83` | `19.71` | 1x T4 + Skylake AVX-512 (`-ot exps=CPU, ngl=99`) | **`79.71 tok/s`** | **`23.14 tok/s`** |
| 38 | `hermes-4.3-36b-abliterated-heretic` | `Hermes-4.3-36B-ABLITERATED-heretic.Q4_K_M.gguf` | `.83` | `20.27` | 1x T4 + Skylake AVX-512 (`ngl=45, t=48`) | **`148.22 tok/s`** | **`3.96 tok/s`** |
| 39 | `nemotron-3.5-lightning-30b-heretic` | `Nemotron-3.5-Lightning-30B-A3B-Heretic-Uncensored-Q4_K_M-GGUF.gguf` | `.72` | `22.83` | 1x T4 (`53/53` layers=`1.37 GiB` `CUDA0`, `-ngl 999 -ot .ffn_.*_exps.=CPU -ub 512`; `3.06x` over `ngl=0` `7.97`) | **`120.01 tok/s`** *(ub2048: `99.09`)* | **`24.38 tok/s`** *(3.06x over `7.97`)* |
| 40 | `agents-a1-35b-uncensored` | `Agents-A1-Uncensored-MTP-APEX-I-Balanced.gguf` | `.83` | `24.27` | 1x T4 + Skylake AVX-512 (`-ot exps=CPU, ngl=99`) | **`54.18 tok/s`** | **`14.50 tok/s`** |
| 41 | `qwen3-48b-savant-heretic` | `Qwen3-48B-A4B-Savant-Commander-Dstll-12X-Cl-Op-Hrtic-Uncen-Q6_K.gguf` | `.83` | `25.69` | 1x T4 + Skylake AVX-512 (`-ot exps=CPU, ngl=99`) | **`72.47 tok/s`** | **`5.58 tok/s`** |
| 42 | `l3.1-dark-planet-47b-uncensored` | `L3.1-MOE-8X8B-Dark-Planet-8D-Mirrored-Chaos-Uncensored-47B.Q4_K_M.gguf` | `.83` | `27.01` | 1x T4 + Skylake AVX-512 (`-ot exps=CPU, ngl=99`) | **`66.41 tok/s`** | **`2.48 tok/s`** |
| 43 | `mirothinker-v1.5-30b-heretic` | `mirothinker-v1.5-30b-heretic-q8_0.gguf` | `.72` | `30.25` | 1x T4 + Skylake AVX-512 (`ngl=20, t=48`) | **`46.77 tok/s`** | **`9.92 tok/s`** |
| 44 | `hermes-4-70b-heretic` | `Hermes-4-70B-heretic.i1-Q4_K_M.gguf` | `.72` | `39.60` | 1x T4 + AVX-512 (`ngl=15, t=48`) *(4xT4: `108.2 / 3.60`)* | **`14.93 tok/s`** | **`1.33 tok/s`** |
| 45 | `nemotron-3.5-bolt-74b-a9b` | `NVIDIA-Nemotron-3.5-Bolt-74B-A9B-UD-Q4_K_XL-00001-of-00003.gguf` | `.83` | `46.77` | 1x T4 + Skylake AVX-512 (`-ot exps=CPU, ngl=99`) | **`29.18 tok/s`** | **`13.46 tok/s`** |
| 46 | `glm-4.5-air-reap-82b-heretic` | `GLM-4.5-Air-REAP-82B-A12B-heretic-Q4_K_M.gguf` | `.83` | `50.22` | 1x T4 + Skylake AVX-512 (`-ot exps=CPU, ngl=99`) | **`30.02 tok/s`** | **`6.40 tok/s`** |
| 47 | `qwen3-coder-next-80b` | `Qwen3-Coder-Next-Q5_K_M-00001-of-00003.gguf` | `.83` | `52.94` | 1x T4 + Skylake AVX-512 (`-ot exps=CPU, ngl=99`) | **`22.95 tok/s`** | **`8.03 tok/s`** |
| 48 | `gpt-oss-120b-heretic-v2` | `kldzj_gpt-oss-120b-heretic-v2-MXFP4_MOE-00001-of-00002.gguf` | `.72` | `59.03` | 1x T4 + Skylake AVX-512 (`-ot exps=CPU, ngl=99`) | **`23.14 tok/s`** | **`8.24 tok/s`** |
| 49 | `qwen3.5-122b-a10b-abliterated` | `Qwen3.5-122B-A10B-abliterated-v1.i1-IQ4_XS.gguf` | `.83` | `60.76` | 1x T4 + Skylake AVX-512 (`-ot exps=CPU, ngl=99`) | **`22.08 tok/s`** | **`7.70 tok/s`** |
| 50 | `laguna-s-2.1-118b-uncensored` | `laguna-s-2.1-q4_k_m-uncensored.gguf` | `.72` | `66.61` | 1x T4 + Skylake AVX-512 (`ngl=9, t=48`) | **`17.34 tok/s`** | **`4.36 tok/s`** |
| 51 | `nemotron-3.5-super-vl-120b` | `NVIDIA-Nemotron-3.5-Super-VL-120B-A12B-UD-Q4_K_XL-00001-of-00003.gguf` | `.83` | `82.55` | 1x T4 + Skylake AVX-512 (`-ot exps=CPU, ngl=99`) | **`19.03 tok/s`** | **`8.40 tok/s`** |
| 52 | `rvn-qwen3.8-flash-next-abliterated-177b` | `RVN-Qwen3.8-Flash-Next-IQ3_M-00001-of-00008.gguf` | `.83` | `83.39` | 1x T4 + Skylake AVX-512 (`-ot exps=CPU, ngl=99`) | **`22.87 tok/s`** | **`7.03 tok/s`** |
| 53 | `ling-3.0-flash-abliterated-128b` | `Ling-3.0-flash-abliterated-APEX-Balanced.gguf` | `.83` | `85.96` | 1x T4 + Skylake AVX-512 (`-ot exps=CPU, ngl=99`) | **`10.16 tok/s`** | **`6.21 tok/s`** |
| 54 | `hy3-295b-heretic` | `Hy3-heretic.i1-IQ2_M.gguf` | `.72` | `89.42` | 1x T4 + Skylake AVX-512 (`-ot exps=CPU, ngl=99`) | **`8.80 tok/s`** | **`3.78 tok/s`** |
| 55 | `glm-5.3-flash-uncensored` | `GLM-5.3-Flash-Uncensored-IQ2_XXS-00001-of-00003.gguf` | `.72` | `90.44` | 1x T4 (`47/47` layers=`8.76 GiB` `CUDA0`, `-ngl 999 -ot .ffn_.*_exps.=CPU -ub 512`; `2.88x` pp / `2.43x` tg over `ngl=22` `8.05/1.88`) | **`23.23 tok/s`** *(2.88x over `8.05`)* | **`4.57 tok/s`** *(2.43x over `1.88`)* |
| 56 | `command-a-plus-111b` | `command-a-plus-05-2026-Q4_K_M-00001-of-00004.gguf` | `.72` | `125.81` | 1x T4 (`33/33` layers=`9.78 GiB` `CUDA0` verified) / AVX-512 (`ngl=0` baseline) | **`1.31 tok/s`** | **`2.40 tok/s`** |
| 57 | `minimax-m2.7-ultra-uncensored-heretic` | `MiniMax-M2.7-BF16-ultra-uncensored-heretic-Q4_K_M.gguf` | `.72` | `128.84` | 1x T4 (`63/63` layers=`2.16 GiB` `CUDA0` verified) / AVX-512 (`ngl=22` baseline) | **`2.80 tok/s`** | **`4.39 tok/s`** |
| 58 | `glm-5.3-flash-abliterated` | `GLM-5.3-Flash-UD-IQ4_XS-00001-of-00005.gguf` | `.83` | `147.14` | 1x T4 + Skylake AVX-512 (`-ngl 999 -ot .ffn_.*_exps.=CPU -ub 512`) | **`1.35 tok/s`** | **`0.73 tok/s`** |
| 59 | `mimo-v2.6-flash-rl-heretic-311b` | `MiMo-V2.6-Flash-RL-Uncensored-Heretic-MXFP4-00001-of-00002.gguf` | `.83` | `155.87` | 1x T4 + Skylake AVX-512 (`-ngl 999 -ot .ffn_.*_exps.=CPU -ub 512`) | **`1.39 tok/s`** | **`4.20 tok/s`** |
| 60 | `beam-1.0-501b-a23b` | `Beam-1.0-501B-A23B-UD-Q2_K_XL-00001-of-00005.gguf` | `.72+.228` | `174.04` | 2x T4 Pool Alpha + Skylake AVX-512 (`-ot exps=CPU, ngl=48`) | **`1.16 tok/s`** | **`3.05 tok/s`** |
| 61 | `nemotron-3-ultra-550b-a55b` | `NVIDIA-Nemotron-3-Ultra-550B-A55B-UD-Q2_K_XL-00001-of-00006.gguf` | `.72+.228` | `188.30` | 2x T4 Pool Alpha + Skylake AVX-512 (`-ot exps=CPU, ngl=48`) | **`1.75 tok/s`** | **`1.37 tok/s`** |
| 62 | `minimax-m3-426b-uncensored` | `MiniMax-M3-uncensored-IQ4_XS-00001-of-00005.gguf` | `.72+.228` | `189.13` | 2x T4 Pool Alpha + Skylake AVX-512 (`-ot exps=CPU, ngl=48`) | **`0.55 tok/s`** | **`1.59 tok/s`** |
| 63 | `ling-3.1-flash-564b` | `Ling-3.1-flash-AD-IQ2_M-00001-of-00005.gguf` | `.83` | `203.10` | 1x T4 + Skylake AVX-512 (`-ngl 999 -ot .ffn_.*_exps.=CPU -ub 512`) | **`2.61 tok/s`** | **`1.34 tok/s`** |
| 64 | `muse-spark-816b-a42b` | `Muse-Spark-Open-816B-A42B-UD-IQ2_XXS-00001-of-00006.gguf` | `.83+.125` | `210.08` | 2x T4 Pool Beta + Skylake AVX-512 (`-ot exps=CPU, ngl=48`) | **`0.41 tok/s`** | **`0.13 tok/s`** |
| 65 | `deepseek-v3.2-685b` | `DeepSeek-V3.2-UD-Q2_K_XL-00001-of-00006.gguf` | `.72+.228` | `229.67` | 2x T4 Pool Alpha + Skylake AVX-512 (`-ot exps=CPU, ngl=48`) | **`1.73 tok/s`** | **`2.47 tok/s`** |
| 66 | `glm-5.3-753b` | `GLM-5.3-UD-Q2_K_XL-00001-of-00007.gguf` | `.83+.125` | `236.44` | 2x T4 Pool Beta + Skylake AVX-512 (`-ot exps=CPU, ngl=48`) | **`1.95 tok/s`** | **`1.71 tok/s`** |
| 67 | `mimo-v2.6-pro-1024b` | `MiMo-V2.6-Pro-MOPD-BPW2.0-00001-of-00007.gguf` | `.83+.125` | `238.22` | 2x T4 Pool Beta + Skylake AVX-512 (`-ot exps=CPU, ngl=48`) | **`2.92 tok/s`** | **`2.42 tok/s`** |
| 68 | `kimi-k3-abliterated-iq1s-gguf` | `Kimi-K3-abliterated-IQ1_S-XS-00001-of-00034.gguf` | `.72+.228` | `539.71` | 2x T4 Pool Alpha + `314 GiB` Single-Node Stepped on `.72` (`ngl=28`) — **4-Node `74.20 / 18.40` INVALIDATED (projected/unverified)** | **`0.0085 tok/s`** *(74.20 projected INVALIDATED)* | **`0.0144 tok/s`** *(18.40 projected INVALIDATED)* |
| 69 | `deepseek-v4.1-flash-abliterated-755b` | `DeepSeek-V4.1-Flash-Abliterated-Q2-imatrix.gguf` | `.72+.83+.228+.125` | `340.60` *(151.05 GiB blk.0..39)* | 4x T4 (`64 GB` VRAM, `-ngl 40 -rpc .83,.228,.125`) + Skylake AVX-512 48-Thread MoE Hybrid (`-ot .ffn_.*_exps.=CPU -fa 1 -ctk q8_0 -ctv q8_0`) | **`0.15 tok/s`** (`pp32`) | **`0.21 tok/s`** (`tg8`) |

---

## 4. All 18 Validated Non-GGUF Models (`Safetensors` / `ONNX` / `Keras` / `Diffusers`)

| # | Vault Directory | Node | Size (`GiB`) | Files | Format | Safetensors Shards / Tensors | Header Scan Time | Status |
|---:|---|---|---:|---:|---|---|---:|---|
| 1 | `ocr-for-captcha` | `.72` | `0.01 GiB` | 8 | Keras/Weights | `8` files | `0.11 ms` | **VALIDATED** |
| 2 | `cloudflare_captcha` | `.72` | `0.08 GiB` | 9 | `ONNX` | `9` files | `0.08 ms` | **VALIDATED** |
| 3 | `captcha_ocr` | `.72` | `0.34 GiB` | 10 | `ONNX` | `10` files | `0.08 ms` | **VALIDATED** |
| 4 | `matcha-plotqa-v2` | `.83` | `1.05 GiB` | 4 | PyTorch `.bin` | `4` files | `0.06 ms` | **VALIDATED** |
| 5 | `ocr-captcha-v3` | `.72` | `1.25 GiB` | 11 | `Safetensors` | `1` shard (`480` tensors) | `13.45 ms` | **VALIDATED** |
| 6 | `ultravox-v0_5-llama-3_2-1b` | `.83` | `1.29 GiB` | 14 | `Safetensors` | `1` shard (`491` tensors) | `5.72 ms` | **VALIDATED** |
| 7 | `git-large-textvqa` | `.83` | `1.47 GiB` | 10 | `Safetensors` | `1` shard (`499` tensors) | `32.66 ms` | **VALIDATED** |
| 8 | `moss-transcribe-diarize` | `.83` | `1.71 GiB` | 20 | `Safetensors` | `1` shard (`683` tensors) | `2.91 ms` | **VALIDATED** |
| 9 | `nemotron-3.5-embed-1b-bf16` | `.83` | `2.92 GiB` | 13 | `Safetensors` | `1` shard (`368` tensors) | `3.26 ms` | **VALIDATED** |
| 10 | `pix2struct-docvqa-large` | `.83` | `4.98 GiB` | 9 | PyTorch `.bin` | `9` files | `0.08 ms` | **VALIDATED** |
| 11 | `gemma-4-e4b-it` | `.83` | `14.92 GiB` | 9 | `Safetensors` | `1` shard (`2,130` tensors) | `1,436.78 ms` | **VALIDATED** |
| 12 | `vibevoice-asr-hf` | `.83` | `15.53 GiB` | 22 | `Safetensors` | `8` shards (`901` tensors) | `4,511.60 ms` | **VALIDATED** |
| 13 | `qwen2-audio-7b-instruct` | `.83` | `15.65 GiB` | 15 | `Safetensors` | `5` shards (`876` tensors) | `17.94 ms` | **VALIDATED** |
| 14 | `audio-flamingo-3-hf` | `.83` | `16.73 GiB` | 26 | `Safetensors+bin` | `5` shards (`1,222` tensors) | `4,130.42 ms` | **VALIDATED** |
| 15 | `flux.1dev-abliteratedv2` | `.83` | `33.31 GiB` | 28 | Diffusers `Safetensors` | `8` shards (`1,819` tensors) | `30.31 ms` | **VALIDATED** |
| 16 | `captcha-agent` | `.72` | `35.09 GiB` | 38 | `Safetensors` (`RL`+`SFT`) | `18` shards (`1,520` tensors) | `24,404.53 ms` | **VALIDATED** |
| 17 | `huihui-gemma-4-31b-it-abliterated-v2` | `.83` | `58.28 GiB` | 11 | `Safetensors` | `2` shards (`1,188` tensors) | `8.31 ms` | **VALIDATED** |
| 18 | `Uniboshi--Kimi-K3-Abliterated-V1` | `.83` | `1,453.79 GiB` | 113 | `Safetensors` | `96` shards (`497,220` tensors) | `37,993.37 ms` | **VALIDATED** |

---

## 5. `bd-airgap` WAN Isolation, `bd-raw-ai` CLI & Standby Local Takeover

- **`/home/mboyle/bin/bd-airgap`**:
  - `bd-airgap on`: Blocks outbound WAN traffic to external cloud LLM APIs (`api.openai.com`, `api.anthropic.com`, `generativelanguage.googleapis.com`) via `iptables` while keeping `10.0.0.0/8` and `127.0.0.0/8` LAN traffic 100% open.
  - `bd-airgap off`: Restores hybrid Local + Cloud mode.
  - `bd-airgap status`: Reports live firewall rules and local endpoint reachability.
- **`/home/mboyle/bin/bd-raw-ai`**:
  - Zero-guardrail CLI connecting directly to local `llama-server` / Sovereign Gateway `:4010` / LiteLLM `:4000` without system prompt injection or moderation filters.
- **Standby Local Takeover (`Qwen3.6-35B-A3B-Heretic` `256K` ctx + `<think>` on `.83:18080` & `Qwen3-Coder-30B-A3B` `256K` ctx on `.72:8000`)**:
  - See [`09-LLM-ENGINES-CHAT-APPS-AND-LOCAL-TAKEOVER-GUIDE.md`](file:///home/mboyle/bd-persist/bd-kb/09-LLM-ENGINES-CHAT-APPS-AND-LOCAL-TAKEOVER-GUIDE.md) for full takeover commands, chat app wiring, and specialized engine selection rules.
