# AGENT-174 — AI VMs Post-Resize Deep Audit, `.125` GPU Repair & Final Benchmark Completion Receipts (`O1789`)

- **WRITTEN-AT**: `2026-10-19T04:28:30Z`
- **SEAT**: `Agent-174` (`12281c64-6c49-49a8-b899-420923dac028`, AI VMs Deep Check & Final Benchmark Completion Verifier)
- **SCOPE**:
  1. Deep hardware & resource audit across all 5 AI/GPU nodes (`10.0.70.72`, `10.0.70.83`, `10.0.70.125`, `10.0.70.228`, `10.0.10.137`) following the operator's VM RAM/vCPU resize at `~03:52 UTC`.
  2. Kernel sysctl (`kernel.numa_balancing=0`, `vm.swappiness=1`, `vm.overcommit_memory=1`) & `transparent_hugepage=always` re-application and verification across `.72`, `.83`, `.125`, and `.228`.
  3. Diagnosis and repair of `10.0.70.125` (`ai-infer01`) broken `libnvidia-compute-550` (`iU`) / missing `nvidia-smi` by restoring `libnvidia-compute=615.71.09-2ubuntu1` (`Tesla T4` online, `llama-rpc.service` & `ollama.service` active).
  4. Full AI service health verification (`.83:18080`, `.72:8082`/`:8000`, `.72:8080`, `:50052` `rpc-server` on `.72/.83/.125/.228`, `:11434` `ollama.service` on `.72/.83/.125/.228/.137`, `:6333` Qdrant on `.125`, `:9222` CDP on `.137`).
  5. Consolidation of all pre-reboot fast-track benchmark receipts into `AGENT37-NEW-FRONTIER-MODELS-BENCHMARK-RECEIPTS.md`, `AGENT171-HOST72-SERIALIZED-BENCHMARKS-RECEIPTS.md`, and `AGENT172-HOST83-SERIALIZED-BENCHMARKS-RECEIPTS.md`.

---

## 1. Executive Summary — Operator VM RAM & vCPU Resize Delta (`03:52 UTC` Reboot)

Across the 4 vSphere Tesla T4 GPU VMs (`esxi01..04`), total cluster guest DDR4 RAM expanded from **`498.5 GiB` (initial `O1789` baseline) / `773.9 GiB` (mid-benchmark)** to **`1,200.43 GiB` (`1.20 TiB`)** across **`216` dedicated physical-core vCPUs** (`Thread(s) per core: 1`, `2` vNUMA nodes per VM):

| Node IP (Mesh IP) | Guest Hostname (ESXi Host) | Previous vCPU / RAM (Baseline $\to$ Mid-Run) | **New Post-Resize vCPU & CPU Model** | **New Exact RAM (`MemTotal`)** | **RAM Delta vs. Previous** | **NUMA Topology (`numactl -H`)** | Disk (`df -h /`) | GPU (`nvidia-smi`) |
|---|---|---|---|---|---|---|---|---|
| **`10.0.70.72`** (`10.0.75.12`) | `ai-srv01` (`AI` on `esxi04`) | `16 vCPU / 32.0 GiB` $\to$ `52 vCPU / 314.87 GiB` | **`52 vCPU`** (2x Xeon Platinum 8170 @ 2.10GHz, `26c/socket`) | **`314.87 GiB`** (`330,167,840 kB`; `292 GiB` free, `309 GiB` avail) | **`+282.87 GiB`** vs initial (`0.00 GiB` vs mid-run) | `Node 0` (`0–25`): `161,205 MB`<br>`Node 1` (`26–51`): `161,224 MB` | `5.0T` total, `4.7T` used, **`86G` free** (`99%`) | `Tesla T4, 15360 MiB, 14891 MiB used, 22 MiB free, 0%` |
| **`10.0.70.83`** (`10.0.75.11`) | `aidev` (`AI DEV` on `esxi03`) | `64 vCPU / 386.0 GiB` $\to$ `52 vCPU / 379.84 GiB` | **`52 vCPU`** (2x Xeon Platinum 8164 @ 2.00GHz, `26c/socket`) | **`295.19 GiB`** (`309,524,036 kB`; `247 GiB` free, `271 GiB` avail) | **`-84.65 GiB`** (`379.84` $\to$ `295.19 GiB`, balanced to `300 GB`) | `Node 0` (`0–25`): `151,082 MB`<br>`Node 1` (`26–51`): `151,187 MB` | `5.0T` total, `4.3T` used, **`804G` free** (`85%`) | `Tesla T4, 15360 MiB, 5427 MiB used, 9669 MiB free, 0%` |
| **`10.0.70.125`** (`10.0.75.14`) | `ai-infer01` (`AI Inference` on `esxi01`) | `16 vCPU / 32.0 GiB` (`32,768 MB`) | **`48 vCPU`** (`3.0x`, 2x Xeon E5-2690 v4 @ 2.60GHz, `24c/socket`) | **`295.19 GiB`** (`309,525,424 kB`; `274 GiB` free, `283 GiB` avail) | **`+263.19 GiB` (`+9.22x` RAM, `+32 vCPU`)** | `Node 0` (`0–23`): `151,082 MB`<br>`Node 1` (`24–47`): `151,188 MB` | `297G` total, `222G` used, **`76G` free** (`75%`) | `Tesla T4, 15360 MiB, 105 MiB used, 14823 MiB free` *(repaired)* |
| **`10.0.70.228`** (`10.0.75.13`) | `ai-ollama01` (`llama` on `esxi02`) | `16 vCPU / 48.0 GiB` (`49,152 MB`) | **`64 vCPU`** (`4.0x`, 2x Xeon E5-2699 v4 @ 2.20GHz, `32c/socket`) | **`295.18 GiB`** (`309,519,800 kB`; `277 GiB` free, `290 GiB` avail) | **`+247.18 GiB` (`+6.15x` RAM, `+48 vCPU`)** | `Node 0` (`0–31`): `151,079 MB`<br>`Node 1` (`32–63`): `151,185 MB` | `297G` total, `226G` used, **`71G` free** (`77%`) | `Tesla T4, 15360 MiB, 543 MiB used, 14385 MiB free, 0%` |
| **`10.0.10.137`** (LAN) | `BattleStation` (Windows 11 Bare-Metal) | `Ryzen 9 12C/24T` / `64.0 GiB` DDR4 | **`Ryzen 9`** (`12C / 24T`) | **`64.00 GiB`** DDR4 | `0.00 GiB` (bare-metal unchanged) | Single socket (`UMA`) | NVMe `C:` online | `NVIDIA GeForce RTX 2080 Ti, 11264 MiB, 1287 MiB used, 9741 MiB free, 1%` |
| **TOTAL (5 Nodes)** | **4x ESXi GPU VMs + 1x Bare-Metal** | **`124 vCPU / 562.5 GiB`** (baseline) | **`240 Cores/Threads`** (`216` Xeon vCPUs + `24T` Ryzen 9) | **`1,264.43 GiB` (`1.23 TiB`)** | **`+701.93 GiB` (`+2.25x` Cluster RAM)** | **8 Xeon NUMA Nodes (`~151–161 GiB` each)** | `10.6 TB` Local NVMe/vSAN | **4x Tesla T4 (`61,440 MiB`) + 1x RTX 2080 Ti (`11,264 MiB`) = `72.7 GB` VRAM** |

---

## 2. Kernel Sysctl & Transparent Hugepage Verification (`10.0.70.72`, `.83`, `.125`, `.228`)

Since `.72`, `.83`, `.125`, and `.228` rebooted at `03:52 UTC` (`up 20 min` at `04:12 UTC`), `/etc/sysctl.d/99-boylenet-llm.conf` and `/sys/kernel/mm/transparent_hugepage/enabled` were persisted and re-applied across all 4 Linux GPU VMs:

| Parameter | `.72` (`ai-srv01`) | `.83` (`aidev`) | `.125` (`ai-infer01`) | `.228` (`ai-ollama01`) | Status |
|---|---|---|---|---|---|
| `kernel.numa_balancing` | `0` | `0` | `0` | `0` | **PASS** |
| `vm.swappiness` | `1` | `1` | `1` | `1` | **PASS** |
| `vm.overcommit_memory` | `1` | `1` | `1` | `1` | **PASS** |
| `/sys/kernel/mm/transparent_hugepage/enabled` | `[always] madvise never` | `[always] madvise never` | `[always] madvise never` | `[always] madvise never` | **PASS** |
| `/etc/sysctl.d/99-boylenet-llm.conf` | Persisted on disk | Persisted on disk | Persisted on disk | Persisted on disk | **PASS** |

---

## 3. Critical Repair on `10.0.70.125` (`ai-infer01`): `libnvidia-compute` & `nvidia-smi` Restoration

- **Symptom Found During Deep Poll**:
  - `nvidia-smi` returned `command not found` on `10.0.70.125` even though `lspci` showed `03:00.0 3D controller: NVIDIA Corporation TU104GL [Tesla T4] (rev a1)` and `lsmod` showed `nvidia` kernel module `615.71.09` loaded (`dkms status`: `nvidia/615.71.09, 6.8.0-147-generic, x86_64: installed`).
- **Root Cause**:
  - An earlier interrupted `apt` run for `nvidia-cuda-toolkit` (`12.0.140`) had left 30+ CUDA 12.0 packages in `iU` (unpacked/unconfigured) state, removed `libnvidia-compute` (`615.71.09-2ubuntu1`, which provides `/usr/bin/nvidia-smi`, `libcuda.so.1`, and `libnvidia-ml.so.1`), and unpacked incompatible `libnvidia-compute-550` (`550.163.01`) against the loaded `615.71.09` kernel driver.
- **Remediation Executed (`04:24–04:27 UTC`)**:
  1. Completed pending package configuration via `sudo dpkg --configure -a`.
  2. Installed exact matching `libnvidia-compute=615.71.09-2ubuntu1` (`sudo apt-get install -y --no-install-recommends libnvidia-compute=615.71.09-2ubuntu1`), automatically removing conflicting `libnvidia-compute-550`.
  3. Restarted `llama-rpc.service` and `ollama.service` on `10.0.70.125`.
- **Verification**:
  - `nvidia-smi --query-gpu=name,memory.total,memory.used,memory.free,utilization.gpu --format=csv,noheader` on `.125`:
    ```text
    Tesla T4, 15360 MiB, 105 MiB, 14823 MiB, 38 %
    ```
  - `llama-rpc.service` (`10.0.75.14:50052`, `pid=67430`) and `ollama.service` (`*:11434`) both verified `active (running)` with CUDA device context bound to the Tesla T4.

---

## 4. Active AI / GPU Services Verification Across All 5 Nodes

| Node | Service / Container | Endpoint / Port | Model / Payload | Live Status & HTTP Health |
|---|---|---|---|---|
| **`.83` (`AI DEV`)** | `llama-heretic-18080.service` | `10.0.70.83:18080` | `Qwen3.6-35B-A3B-Heretic-Q4_K_M.gguf` (`-c 262144 -np 4 -kvu --reasoning-preserve --jinja --numa isolate -t 24 -ngl 99 -ot .ffn_.*_exps.=CPU -fa on -ctk q8_0 -ctv q8_0`) | **`active (running)` — HTTP `200`** (`/health`) |
| **`.83` (`AI DEV`)** | `llama-rpc.service` (`rpc-server`) | `10.0.75.11:50052` | `/opt/llama-rpc/rpc-server -H 10.0.75.11 -p 50052 -c` (`PID 1938`) | **`LISTEN` (`active`)** |
| **`.83` (`AI DEV`)** | `ollama.service` | `10.0.70.83:11434` | `/usr/local/bin/ollama serve` (`PID 1939`) | **`active (running)` (`LISTEN`)** |
| **`.72` (`AI`)** | `llama-tier1-alpha.service` | `10.0.70.72:8082` & `:8000` | `Qwen3-Coder-30B-A3B-Instruct-Q4_K_M.gguf` (`-c 262144 -np 4 -kvu --reasoning-preserve -fa on -ctk q8_0 -ctv q8_0 --jinja -t 48 -ngl 99 -ot .ffn_.*_exps.=CPU`) | **`active (running)` — HTTP `200`** (`:8082/health` & `:8000/health`) |
| **`.72` (`AI`)** | `open-webui` (Docker) | `10.0.70.72:8080` | OpenWebUI Portal (`open-webui`) | **`Up (healthy)` — HTTP `200`** (`:8080/health`) |
| **`.72` (`AI`)** | `llama-rpc.service` (`rpc-server`) | `10.0.75.12:50052` | `/opt/llama-rpc/rpc-server -H 10.0.75.12 -p 50052 -c` (`PID 1801`) | **`LISTEN` (`active`)** |
| **`.72` (`AI`)** | `ollama.service` | `10.0.70.72:11434` | `/usr/local/bin/ollama serve` (`PID 1695`) | **`active (running)` (`LISTEN`)** |
| **`.125` (`AI Inference`)** | `qdrant` (`qdrant_portal.py` / engine) | `10.0.70.125:6333` | Qdrant `1.18.0` (`6` collections: `bd_master_kb` `455` pts, `boylenet_episodic_memory` `805` pts, `fleet_lessons_hivemind` `805` pts, `boylenet_codebase_ast` `1,282` pts, `swarm_docs_rag` `1,388` pts, `boylenet_fleet_rag` `2,670` pts) | **`status: ok` — HTTP `200`** (`/collections`) |
| **`.125` (`AI Inference`)** | `llama-rpc.service` (`rpc-server`) | `10.0.75.14:50052` | `/opt/llama-rpc/rpc-server -H 10.0.75.14 -p 50052 -c` (`PID 67430`) | **`LISTEN` (`active`, GPU bound)** |
| **`.125` (`AI Inference`)** | `ollama.service` | `10.0.70.125:11434` | `/usr/local/bin/ollama serve` | **`active (running)` (`LISTEN`)** |
| **`.228` (`llama`)** | `llama-rpc.service` (`rpc-server`) | `10.0.75.13:50052` | `/opt/llama-rpc/rpc-server -H 10.0.75.13 -p 50052 -c` (`PID 2091`) | **`LISTEN` (`active`)** |
| **`.228` (`llama`)** | `ollama.service` | `10.0.70.228:11434` | `/usr/local/bin/ollama serve` (`PID 2092`) | **`active (running)` (`LISTEN`)** |
| **`.137` (`BattleStation`)** | `ollama` (Windows) | `10.0.10.137:11434` | `nomic-embed-text-cpu:latest` + RTX 2080 Ti coder models | **`ONLINE` — HTTP `200`** (`/api/tags`) |
| **`.137` (`BattleStation`)** | Chrome CDP Browser Oracle | `10.0.10.137:9222` | `Chrome/155.0.8059.41` (`Protocol-Version: 1.3`) | **`ONLINE` — HTTP `200`** (`/json/version`) |

---

## 5. Final Fast-Track Benchmark Receipts Consolidated Across `.72` & `.83`

All pre-reboot (`03:52 UTC`) fast-track benchmarks and `CUDA0` layer-allocation proofs have been reconciled into:
- [`AGENT37-NEW-FRONTIER-MODELS-BENCHMARK-RECEIPTS.md`](file:///home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT37-NEW-FRONTIER-MODELS-BENCHMARK-RECEIPTS.md)
- [`AGENT171-HOST72-SERIALIZED-BENCHMARKS-RECEIPTS.md`](file:///home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT171-HOST72-SERIALIZED-BENCHMARKS-RECEIPTS.md)
- [`AGENT172-HOST83-SERIALIZED-BENCHMARKS-RECEIPTS.md`](file:///home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT172-HOST83-SERIALIZED-BENCHMARKS-RECEIPTS.md)

### Summary of Captured Fast-Track Benchmarks (`.72` & `.83`)

| Host | Row / Model | Size (`GiB`) | Configuration / Engine | GPU Layers & `CUDA0` Buffer | Prefill (`pp`) | Decode (`tg`) | Speedup vs. Prior |
|---|---|---:|---|---|---:|---:|---|
| **`.72`** | **Row `#16`** `Swift-Qwen3.8-Flash-Next-GSQ-RCO-IQ2_XS` | `63.47 GiB` | `b11716` (`-ngl 999 -ot .ffn_.*_exps.=CPU -fa 1 -ctk q8_0 -ctv q8_0 -b 4096 -ub 2048 --numa isolate -t 24`) | `49/49` layers (`3.30 GiB` model, `3.96 GiB` total `CUDA0`) | **`82.24 tok/s`** (`pp512`) | **`13.01 tok/s`** (`tg128`) | **`1.88x` decode** vs 4-GPU RPC (`6.91 tok/s`) |
| **`.72`** | **Row `#39`** `nemotron-3.5-lightning-30b-heretic` | `22.83 GiB` | `b11716` (`-ngl 999 -ot .ffn_.*_exps.=CPU -fa 1 -ctk q8_0 -ctv q8_0 -b 2048 -ub 512 --numa isolate -t 24`) | `53/53` layers (`1.37 GiB` model, `2.47 GiB` total `CUDA0`) | **`120.01 tok/s`** (`ngl0`) / **`99.09 tok/s`** (`ub2048`) | **`24.38 tok/s`** (`tg128`) | **`3.06x` decode** vs `ngl=0` (`7.97 tok/s`) |
| **`.72`** | **Row `#55`** `glm-5.3-flash-uncensored` | `90.44 GiB` | `b11716` (`-ngl 999 -ot .ffn_.*_exps.=CPU -fa 1 -ctk q8_0 -ctv q8_0 -b 2048 -ub 512 --numa isolate -t 24`) | `47/47` layers (`8.76 GiB` model, `9.63 GiB` total `CUDA0`) | **`23.23 tok/s`** (`pp512`) | **`4.57 tok/s`** (`tg128`) | **`2.88x` `pp` / `2.43x` `tg`** vs `ngl=22` (`8.05 / 1.88`) |
| **`.72`** | **Row `#56`** `command-a-plus-111b` | `125.81 GiB` | `b11716` (`-ngl 999 -ot .ffn_.*_exps.=CPU -ub 512`) | `33/33` layers (`9.78 GiB` model, `10.07 GiB` total `CUDA0` verified) | `1.31 tok/s` (`ngl=0` baseline) | `2.40 tok/s` (`ngl=0` baseline) | `100%` layer offload (`33/33`) verified in `10.07 GiB` `CUDA0` |
| **`.72`** | **Row `#57`** `minimax-m2.7-ultra-uncensored-heretic` | `128.84 GiB` | `b11716` (`-ngl 999 -ot .ffn_.*_exps.=CPU -ub 512`) | `63/63` layers (`2.16 GiB` model, `2.64 GiB` total `CUDA0` verified) | `2.80 tok/s` (`ngl=22` baseline) | `4.39 tok/s` (`ngl=22` baseline) | `100%` layer offload (`63/63`) verified in `2.64 GiB` `CUDA0` |
| **`.83`** | **Row `#18`** `DeepSeek-V4-Flash-Q2-0731.gguf` | `80.76 GiB` | Mainline `b11716` (`-ngl 999 -ot .ffn_.*_exps.=CPU -ub 2048`) | `100%` non-expert layers on 1x T4 (`NO --rpc`) | **`6.15 tok/s`** (`pp512`) | **`0.09 tok/s`** (`tg128` cold) | Single-node baseline (`b11716`) |
| **`.83`** | **Row `#18`** `DeepSeek-V4-Flash-Q2-0731.gguf` | `80.76 GiB` | `ik_llama.cpp` (`c446f1e`, `-fmoe 1 -ub 512`) | `100%` non-expert layers on 1x T4 (`NO --rpc`) | **`8.50 tok/s`** (`pp512`), **`5.11`** (`pp128`), **`3.73`** (`pp64`) | **`0.18 tok/s`** (`tg8`) | **`+38.2%` `pp512` / `2.00x` `tg`** over `b11716` |
| **`.83`** | **Row `#18`** `DeepSeek-V4-Flash-Q2-0731.gguf` | `80.76 GiB` | `gamozolabs/llama.cpp` (`9e51941`, `FUSED-MOE-SILU -ub 512`) | `100%` non-expert layers on 1x T4 (`NO --rpc`) | **`1.64 tok/s`** (`pp64`) | **`0.11 tok/s`** (`tg8`) | **`+22.2%` `tg`** over `b11716` |
| **`.83`** | **Row `#18`** `DeepSeek-V4-Flash-Q2-0731.gguf` | `80.76 GiB` | `llama-master` (`31288c9`, `FA_ALL_QUANTS=ON -ub 512`) | `100%` non-expert layers on 1x T4 (`NO --rpc`) | **`4.54 tok/s`** (`pp64`) | **`0.14 tok/s`** (`tg8`) | **Fastest `pp64` (`+21.7%` vs `ik`) / `+55.6%` `tg`** over `b11716` |
