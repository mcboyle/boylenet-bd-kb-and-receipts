# O1789 — Master Serialized Inference Optimization Pipeline Ledger (`Stages 0 to 5`)

- **Written At**: `2026-10-19T21:51:52Z`
- **Orchestrator**: `Agent-167` (`/home/mboyle/bin/bd-serialized-opt-pipeline.py` + `bd-serialized-opt-pipeline.service`)
- **Stage 5 Router Modules**: `/home/mboyle/bin/bd-tier-router.py` (`bd_tier_router`) + `/home/mboyle/bin/bd-gpu-orchestrator.py`
- **Staged VMX Topology Verifier**: `/home/mboyle/bin/bd-esxi-numa-vmx-enforce.sh`
- **Live JSON State**: `/var/tmp/bd-serialized-opt-results.json`

---

## 0. Per-Host `.bench.lock` Serialization & Steady-State Gate (`Stage 0`)

| Host | Lock Path | Lock State | GPU (`used, total MiB, util%`) | Guest Sysctls (`numa_bal,swap,overcommit`) | THP (`transparent_hugepage`) | Stage 0 (`Agent-151`) Gate |
|---|---|---|---|---|---|---|
| **`.72` (`ai-srv01`)** | `/opt/models/.bench.lock` | **`FREE`** | `14909, 15360, 0` | `0,1,1` | `[always] madvise never` | **`PASS`** (`:8000` `256K` + `:18081` `1M` + `<think>`) |
| **`.83` (`aidev`)** | `/mnt/ai-models/.bench.lock` (`-> /opt/models/.bench.lock`) | **`FREE`** | `14345, 15360, 0` | `0,1,1` | `[always] madvise never` | **`PASS`** (`:18080` `256K` + `<think>`) |

### Active Processes on `.72`
  - `1005623 llama-server    /opt/llama-rpc/llama-server -m /opt/models/tier1-qwen3-coder-30b-a3b/Qwen3-Coder-30B-A3B-Instruct-Q4_K_M.gguf --alias qwen3-coder-30b-a3b -ngl 99 -ot .ffn_.*_exps.=CPU -c 262144 -np 4 -kvu --reasoning-preserve -fa on -ctk q8_0 -ctv q8_0 --jinja -t 48 --host 0.0.0.0 --port 8082`

### Active Processes on `.83`
  - `455492 llama-server    /opt/llama-rpc/llama-server -m /mnt/ai-models/qwen3.6-35b-a3b-heretic/Qwen3.6-35B-A3B-Heretic-Q4_K_M.gguf --host 0.0.0.0 --port 18080 --load-mode none --numa distribute -ngl 99 -ot blk\.([0-9]|1[0-5])\.ffn_.*_exps\.=CPU -fa on -ctk q8_0 -ctv q8_0 -c 65536 -np 2 -kvu --reasoning-preserve --jinja -t 24 -b 4096 -ub 2048`

---

## 1. Stage 1 — ESXi Power Policy, HPE iLO BIOS, VMX NUMA & Kernel Hygiene (`Step 1A–1E`)

### Step 1A (`158` — ESXi Power Policy & HPE iLO4/iLO5 BIOS Regulator Verification)
Executed `esxcli hardware power policy set --id=1` and `esxcli hardware power policy get` across all 4 ESXi hypervisors (`10.0.20.41..44`) and queried HPE iLO4/iLO5 Redfish `/redfish/v1/Systems/1/Bios/`:

| Hypervisor | Management / iLO IP | Hardware Platform | ESXi Power Policy (`esxcli`) | HPE BIOS Power Regulator & Workload Profile | CPU / NUMA / C-State BIOS Settings |Hosted GPU VM |
|---|---|---|---|---|---|---|
| **`esxi01`** | `10.0.20.41` / `10.0.10.10` | HPE ProLiant DL380 Gen9 (`2x14C`, `SN: MQX73007P7`) | **`Id: 1` (`High Performance`, `static`)** | `StaticHighPerf` | `NoCStates`, `MaxPerf` | `.125` (`AI Inference`) |
| **`esxi02`** | `10.0.20.42` / `10.0.10.20` | HPE ProLiant DL380 Gen9 (`2x22C`, `Tag: 9002532`) | **`Id: 1` (`High Performance`, `static`)** | `PowerProfile: MaxPerf`, `PowerRegulator: StaticHighPerf` | `MinProcIdlePower: NoCStates`, `EnergyPerfBias: MaxPerf`, `IntelQpiPowerManagement: Disabled`, `ProcTurbo: Enabled` | `.228` (`llama`) |
| **`esxi03`** | `10.0.20.43` / `10.0.10.252` | HPE ProLiant DL380 Gen10 (`2x26C Xeon Platinum 8164`, `SN: 2M291803HL`) | **`Id: 1` (`High Performance`, `static`)** | `WorkloadProfile: Virtualization-MaxPerformance`, `PowerRegulator: StaticHighPerf`, `CollabPowerControl: Disabled` | `MinProcIdlePower: NoCStates`, `EnergyPerfBias: MaxPerf`, `EnergyEfficientTurbo: Disabled`, `IntelUpiPowerManagement: Disabled`, `SubNumaClustering: Enabled` | **`.83` (`AI DEV`)** |
| **`esxi04`** | `10.0.20.44` / `10.0.10.40` | HPE ProLiant DL380 Gen10 (`2x26C Xeon Platinum 8170`, `SN: 2M292001B2`) | **`Id: 1` (`High Performance`, `static`)** | `WorkloadProfile: Virtualization-MaxPerformance`, `PowerRegulator: StaticHighPerf`, `CollabPowerControl: Disabled` | `MinProcIdlePower: NoCStates`, `EnergyPerfBias: MaxPerf`, `EnergyEfficientTurbo: Disabled`, `IntelUpiPowerManagement: Disabled`, `SubNumaClustering: Enabled` | **`.72` (`AI`)** |

### Step 1A VMX Topology Verification on `.83` (`AI DEV.vmx`) and `.72` (`AI.vmx`)
Both `.83` (`395,264 MB`) and `.72` (`327,680 MB`) are verified active with:
- `numvcpus = "52"`, `cpuid.coresPerSocket = "26"` (`2` virtual sockets x `26` physical Skylake-SP AVX-512 cores; `Thread(s) per core: 1`)
- `numa.vcpu.preferHT = "FALSE"`, `numa.autosize.once = "FALSE"`, `numa.vcpu.maxPerVirtualNode = "26"`
- `sched.mem.pin = "TRUE"`, `sched.mem.min = memSize` (`100%` DDR4 pinned in ESXi)
- Guest `numactl -H`: Node 0 (`cpus 0–25`), Node 1 (`cpus 26–51`), distance `10 : 20`.

### Step 1B (`157` — Guest Kernel Sysctls & Tiered `-ub` / Selective `mmap`)
- Applied and persisted in `/etc/sysctl.d/99-boylenet-llm.conf` on `.72` and `.83`:
  - `kernel.numa_balancing = 0`
  - `vm.swappiness = 1`
  - `vm.overcommit_memory = 1`
  - `/sys/kernel/mm/transparent_hugepage/enabled = [always] madvise never`
- Enforced in `/home/mboyle/bin/bd-tier-router.py` and `/home/mboyle/bin/bd-gpu-orchestrator.py`:
  - `GGML_OP_OFFLOAD_MIN_BATCH=2048`
  - Tiered `-ub`: `2048` (`<= 120 GiB`), `1024` (`120–240 GiB`), `512` (`> 240 GiB` or VRAM-tight `ngl=0/22` rows `39, 55–59, 63`)
  - `mmap` kept **ON** for `.125`/`.228` and mainline/`ik_llama` runs; `--no-mmap` restricted exclusively to `gamozolabs` `MADV_HUGEPAGE` fastload
  - `--spec-type ngram-cache` restricted exclusively to `coding`/`agent` routes (`prose` routes untouched)

### Step 1C–1E & Stages 3–4 Serialized Queue Execution
- **`Agent-170`**: ESXi `--id=1` + sysctls + Action #4 `Kimi-K3` 4-node RSS verification across `.72`, `.83`, `.125`, `.228`
- **`Agent-171`**: Host `.72` serialized benchmark queue under `flock /opt/models/.bench.lock` (Step 1C `Swift-Qwen3.8-Flash-Next IQ2_XS`, Step 1D `#69 DeepSeek-V4.1` `-p 512 -n 128`, Step 1E rows `39, 55, 56, 57`)
- **`Agent-172`**: Host `.83` serialized benchmark queue under `flock /mnt/ai-models/.bench.lock` (Step 1C `GLM-5.2 UD-IQ2_XXS`, Step 1E rows `58, 59, 63`, Stage 3 A/Bs `3A–3E`, Stage 4 Single-Node No-RPC rows `60–67`)

---

## 2. Stage 2 — Engine Compiles & Drafter Quantization Status (`.83`)

| Step | Target | Path on `.83` | Build / Artifact Status |
|---|---|---|---|
| **`159`** | `ik_llama.cpp` (`ikawrakow/ik_llama.cpp`, `CUDA sm_75` + `GGML_AVX512=ON`, `GGML_CUDA_IQK_FORCE_BF16=OFF`) | `/opt/ik-llama/bin/llama-bench` | **`READY`** |
| **`160`** | `gamozolabs/llama.cpp` (`multisocket-cpu-moe`, `CMAKE_CUDA_ARCHITECTURES=75`) | `/opt/gamozo-llama/bin/llama-bench` | **`BUILDING`** |
| **`161`** | Mainline `llama.cpp` `master` (`GGML_CUDA_FA_ALL_QUANTS=ON`, `GGML_CPU_ALL_VARIANTS=ON`, `GGML_RPC=ON`) | `/opt/llama-mainline/bin/llama-bench` / `/opt/llama-rpc/llama-bench` | **`READY_RPC_B11716`** |
| **`164`** | `dspark-DeepSeek-V4-Flash-0731-Q4_K_M.gguf` Speculative Drafter | `/opt/models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-Q4_K_M.gguf` | **`11729015904`** |
| **`165B`** | `turboquant-ggml` (`turbo3_0`/`turbo4_0` `sm_75` CUDA FlashAttention check) | `/opt/turboquant-ggml-src` | **`INSPECTED`** |

---

## 3. Stage 5 (`166`) — Proven Routing Table Wired into `bd-tier-router.py` & `bd-gpu-orchestrator.py`

1. **Single-Node No-RPC Law (`140–350 GiB` MoE on `.83`, `140–280 GiB` on `.72`)**:
   - Rows `60–67` (`beam-1.0-501b-a23b`, `nemotron-3-ultra-550b-a55b`, `minimax-m3-426b-uncensored`, `ling-3.1-flash-564b`, `muse-spark-816b-a42b`, `deepseek-v3.2-685b`, `glm-5.3-753b`, `mimo-v2.6-pro-1024b`) + `glm-5.2-iq2xxs` (`196.57 GiB`), `deepseek-v4-flash-q4k` (`153.33 GiB`), `qwen3-coder-480b-a35b` (`167.91 GiB`), `glm-5.2-q3km` (`319.21 GiB`), and `qwen3.8-flash-next-bf16` (`329.73 GiB`) route **Single-Node (`1x local T4 + 2-socket DDR4`, `--numa distribute -t 48`, ZERO `--rpc`)**.
2. **NUMA Socket Pinned Law (`<= 140 GiB` on `.72`, `<= 165 GiB` on `.83`)**:
   - Models fitting within a single socket (`Swift-Qwen3.8-Flash-Next IQ2_XS` `63.47 GiB`, `DeepSeek-V4-Flash Q2` `80.76 GiB`, `Qwen3-235B-A22B Q4_K_M` `132.39 GiB`, `gpt-oss-120b-heretic-v2` `59.03 GiB`, `mimo-v2.6-flash-rl-heretic-311b` `155.87 GiB` on `.83`) pin to Socket 0 (`numactl -N 0 -m 0 -t 26`) to eliminate cross-socket UPI traffic.
3. **100% Shared-Expert & Output-Weight GPU Residency (`Agent-163`)**:
   - `-ngl 999 -ot ".ffn_.*_exps.=CPU"` keeps `.ffn_.*_shexp.` and `output.weight` on CUDA0; VRAM-tight rows (`39, 55–59, 63`) enforce `-ub 512 -ctk q8_0 -ctv q8_0 --mlock` (plus `-ot "blk\.(3[0-9])\.attn.*=CPU"` only where single-T4 warm-up exceeds `14.9 GiB`).
4. **256K / 1M Context & `<think>` Preservation (`Agent-151`)**:
   - `-c 262144 -np 4 -kvu --reasoning-preserve --jinja` (`-c 1048576` on `qwythos-9b`) and `enable_thinking: True` (`<think>...</think>` + `reasoning_content`) are strictly preserved across all tiers.

### Discovered Stage Receipts in `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade`
- **Stage 0 (`151`)**: `AGENT151-MAX-CONTEXT-256K-1M-AND-THINKING-RECEIPTS.md`
- **Stage 1 (`157/158/163/170/171/172`)**: `AGENT170-ESXI-POWER-SYSCTL-AND-ACTION4-KIMI-RECEIPTS.md, AGENT171-HOST72-SERIALIZED-BENCHMARKS-RECEIPTS.md, AGENT172-HOST83-SERIALIZED-BENCHMARKS-RECEIPTS.md`
- **Stage 2 (`159/160/161/164/165B/168/169`)**: `AGENT165A-HF-REAP-AND-R4-CATALOG-RECEIPTS.md, AGENT94-ALL-165-VIEWS-WIRED-LIVE-60FPS-RECEIPTS.md, CODEX-SOL-165-VIEW-REGISTRY-RECEIPT.md, AGENT168-IK-LLAMA-AND-TURBOQUANT-BUILD-RECEIPTS.md, AGENT169-GAMOZO-MAINLINE-AND-DRAFTER-Q4-RECEIPTS.md`
- **Stage 4 (`162`)**: `queued-behind-stage3`
- **Stage 5 (`166/167`)**: `AGENT113-O1670-69ROW-BACKLOG-BURNDOWN-RECEIPTS.md, AGENT119-O1670-WAVE2-LENS-ROWS-RECEIPTS.md, AGENT128-O1670-SHARD-C-L19-L36-RECEIPTS.md, SERIALIZED-OPT-PIPELINE-LEDGER.md`
