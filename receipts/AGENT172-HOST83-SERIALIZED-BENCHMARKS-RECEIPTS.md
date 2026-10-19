# AGENT-172: Host `.83` (`esxi03`) Serialized Benchmark Runner Receipts (`O1789`)

- **WRITTEN-AT**: `2026-10-19T03:41:00Z`
- **AGENT**: `Agent-172` (`ca47a692-71a0-4db0-be4e-c3ca779a5134`)
- **TARGET HOST**: `10.0.70.83` (`aidev` on `esxi03`, Dual Intel Xeon Platinum 8164 `52 vCPU / 2 vNUMA nodes`, `379 GiB` DDR4-2666, `1x Tesla T4 15,360 MiB`)
- **SERIALIZATION LOCK**: `/mnt/ai-models/.bench.lock` (`flock`)
- **SERVICE GUARD**: `llama-heretic-18080.service` stopped during `flock` benchmark windows (`14,991 MiB` free VRAM out of `15,360 MiB`) and automatically restarted via `trap ... EXIT` (`active` on `:18080`).

---

## 1. Step 1: Post-Sysctl & Power-Policy Baseline on `10.0.70.83`

### 1.1 Host `.83` Kernel, NUMA & GPU Environment Verification
- `sysctl kernel.numa_balancing`: `kernel.numa_balancing = 0`
- `/sys/kernel/mm/transparent_hugepage/enabled`: `[always] madvise never`
- `/etc/environment`: `GGML_OP_OFFLOAD_MIN_BATCH=2048`
- `esxcli hardware power policy get` (`esxi03` / `10.0.20.43`): `Id: 1` (`High Performance` / `static`)
- `nvidia-smi` with `llama-heretic-18080.service` stopped:
  - `Tesla T4, 15360 MiB total, 105 MiB used (rpc-server idle context), 14991 MiB free`
- `numastat -m` on `10.0.70.83` (`388,957.49 MiB` across `Node 0` + `Node 1`):
  ```text
                            Node 0          Node 1           Total
  MemTotal               194469.30       194488.18       388957.49
  MemFree                152001.53        86678.96       238680.49
  Active(file)             3737.44         9766.57        13504.01
  Inactive(file)          37078.62        96349.69       133428.31
  FilePages               41042.31       106880.32       147922.62
  ```

### 1.2 `DeepSeek-V4-Flash-Q2-0731.gguf` (`80.76 GiB`, `deepseek4 284.33B`) Single-Node Baseline & Engine A/Bs
Executed strictly serialized under `flock /mnt/ai-models/.bench.lock` with local Tesla T4 only (`NO --rpc`), `-ngl 999 -ot ".ffn_.*_exps.=CPU" -fa 1 -ctk q8_0 -ctv q8_0 --numa distribute -t 48`:

| Engine Binary (`10.0.70.83`) | Build / Commit | Flags | Prefill (`pp`) | Decode (`tg`) | Key Observation |
|---|---|---|---|---:|---|
| `/opt/llama-rpc/llama-bench` | `b11716` (`797de63b2`) | `-p 512 -n 128 -b 4096 -ub 2048 -r 1` | **`6.15 tok/s`** (`pp512`) | **`0.09 tok/s`** (`tg128`) | Baseline `b11716`; `deepseek4` Layers `0..2` include `ffn_gate_tid2eid.weight` Engram lookup + `hc_attn_*`/`hc_ffn_*` hyper-connections |
| `/opt/ik-llama/bin/llama-bench` (`159`) | `c446f1e` (`ik_llama.cpp`) | `-p 512,128 -n 128 -b 4096 -ub 512 -fmoe 1 -r 1` | **`8.50 tok/s`** (`pp512`) / **`5.11 tok/s`** (`pp128`) | — | **`+38.2%` faster `pp512`** over `b11716` (`8.50` vs `6.15 tok/s`) |
| `/opt/ik-llama/bin/llama-bench` (`159` fast-track) | `c446f1e` (`ik_llama.cpp`) | `-p 64 -n 8 -b 2048 -ub 512 -fmoe 1 -w 0 -r 1` | **`3.73 tok/s`** (`pp64`) | **`0.18 tok/s`** (`tg8`) | **`2.00×` faster decode (`tg`)** than `b11716` (`0.18` vs `0.09 tok/s`) |
| `/opt/gamozo-llama/llama-bench` (`160` fast-track) | `9e51941` (`multisocket-cpu-moe`) | `-p 64 -n 8 -b 2048 -ub 512 --no-warmup -r 1` | **`1.64 tok/s`** (`pp64`) | **`0.11 tok/s`** (`tg8`) | Verified live: `FUSED-MOE-SILU path active (gate clamp [-inf,10], up clamp [-10,10], down fused)` (`+22.2%` `tg` over `b11716`) |
| `/opt/llama-master/llama-bench` (`161` fast-track) | `31288c9` (`mainline master`, `FA_ALL_QUANTS=ON`) | `-p 64 -n 8 -b 2048 -ub 512 --no-warmup -r 1` | **`4.54 tok/s`** (`pp64`) | **`0.14 tok/s`** (`tg8`) | **Fastest `pp64` (`4.54 tok/s`)** and **`+55.6%` faster `tg`** over `b11716` (`0.14` vs `0.09 tok/s`) |

---

## 2. Step 2: `Agent-163` Root-Cause Diagnosis & Fix for `.83` `ngl=22` Rows (`#58, #59, #63`)

### 2.1 Root Cause of `ngl=22` in Prior Sweep (`/tmp/bench_worker.py` Audit)
Inspection of `/tmp/bench_worker.py` (lines `135–147`) on `10.0.70.83` proved why Rows `#58` (`glm-5.3-flash-abliterated`), `#59` (`mimo-v2.6-flash-rl-heretic-311b`), and `#63` (`ling-3.1-flash-564b`) previously ran at `ngl=22`:
1. `bench_worker.py` did **not** include a single-node `-ngl 999 -ot ".ffn_.*_exps.=CPU"` candidate in its candidate list for `100–245 GiB` MoE models:
   ```python
   candidates.append((f'-rpc {PARTNER_RPC} -ngl 48 -ot ".ffn_.*_exps.=CPU" ...'))
   candidates.append((f'-ngl 22 -ot ".ffn_.*_exps.=CPU" -fa 1 -ctk q8_0 -ctv q8_0 -b 2048 -ub 2048 ...'))
   ```
2. Candidate 1 (`-rpc 10.0.75.14:50052 -ngl 48`) failed immediately because partner node `.125` (`10.0.75.14`) had Ollama occupying `13,465 MiB / 15,360 MiB` (`1,463 MiB` free VRAM).
3. Candidate 2 fell directly into hardcoded `-ngl 22 -ot ".ffn_.*_exps.=CPU" -b 2048 -ub 2048`, leaving layers `22..N` (including attention, norms, shared experts `.ffn_.*_shexp.`, and `output.weight`) on CPU!

### 2.2 Strict Operator Rule Compliance (`-ngl 999 -ot ".ffn_.*_exps.=CPU" -ub 512 -ctk q8_0 -ctv q8_0`)
Per Operator Rule:
- **NEVER offload `.ffn_.*_shexp.` or `output.weight` to CPU** — keep all shared experts and `output.weight` in Tesla T4 VRAM.
- Lower `-ub 2048` $\to$ `-ub 512 -ctk q8_0 -ctv q8_0` to shrink the CUDA compute/warmup buffer by `~1.8–2.4 GiB`, fitting all dense attention + shared experts + `output.weight` in `.83`'s `14,991 MiB` usable T4 VRAM (using partial upper-layer attention offload `-ot "blk\.(4[5-9]|[5-8][0-9])\.attn.*=CPU"` only if non-routed weights exceed `14.9 GiB`):

| Row | Vault Directory | Model (`GGUF`) | Size (`GiB`) | Prior Broken Config (`bench_worker.py`) | Prior `pp` / `tg` | Fixed Single-Node 1xT4 Config (`shexp` + `output.weight` in VRAM) |
|---:|---|---|---:|---|---|---|
| **58** | `glm-5.3-flash-abliterated` | `GLM-5.3-Flash-UD-IQ4_XS-00001-of-00005.gguf` | `147.14 GiB` | `-ngl 22 -ot ".ffn_.*_exps.=CPU" -ub 2048` | `1.35` / `0.73 tok/s` | `-ngl 999 -ot ".ffn_.*_exps.=CPU" -fa 1 -ctk q8_0 -ctv q8_0 -b 2048 -ub 512 --numa distribute -t 48` |
| **59** | `mimo-v2.6-flash-rl-heretic-311b` | `MiMo-V2.6-Flash-RL-Uncensored-Heretic-MXFP4-00001-of-00002.gguf` | `155.87 GiB` | `-ngl 22 -ot ".ffn_.*_exps.=CPU" -ub 2048` | `1.39` / `4.20 tok/s` | `-ngl 999 -ot ".ffn_.*_exps.=CPU" -fa 1 -ctk q8_0 -ctv q8_0 -b 2048 -ub 512 --numa distribute -t 48` (plus `/opt/gamozo-llama/` `FUSED-MOE-SILU` for `MXFP4`) |
| **63** | `ling-3.1-flash-564b` | `Ling-3.1-flash-AD-IQ2_M-00001-of-00005.gguf` | `203.10 GiB` | `-ngl 22 -ot ".ffn_.*_exps.=CPU" -ub 2048` | `2.61` / `1.34 tok/s` | `-ngl 999 -ot ".ffn_.*_exps.=CPU" -fa 1 -ctk q8_0 -ctv q8_0 -b 2048 -ub 512 --numa distribute -t 48` |

---

## 3. Step 3 & Step 4: Speculative Drafter (`164`) & Single-Node No-RPC Configuration (`Rows 60–67`)

1. **`Agent-164` (`dspark-DeepSeek-V4-Flash-0731-Q4_K_M.gguf` Drafter on `.83`)**:
   - Quantized file verified at `/mnt/ai-models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-Q4_K_M.gguf` (`11 GiB` total: `9` routed MoE expert tensors `~9.8 GiB` + `~245 MiB` dense attention/shexp/markov weights).
   - Required launch flag: `--model-draft /mnt/ai-models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-Q4_K_M.gguf --spec-type dspark --cpu-moe-draft` (`-cmoed`), keeping only `~245 MiB` of draft weights in T4 VRAM alongside the target model.
2. **`Agent-162` (Single-Node No-RPC Policy for Rows `60–67` on `.83` — `muse-spark-816b-a42b`, `glm-5.3-753b`, `mimo-v2.6-pro-1024b`)**:
   - All three `.83` models (`210.08 GiB`, `236.44 GiB`, `238.22 GiB`) fit entirely inside `.83`'s `379 GiB` local DDR4 (`337 GiB` free) with `-ngl 999 -ot ".ffn_.*_exps.=CPU" -ub 512 -ctk q8_0 -ctv q8_0` on `.83`'s single local Tesla T4 (`NO --rpc`), eliminating cross-node TCP RPC synchronization and the old `ngl=48` cap from `/tmp/bench_worker.py`.

---

## 4. Post-Benchmark Production Service Restoration on `10.0.70.83`

- `llama-heretic-18080.service` restarted and verified `active (running)` on `10.0.70.83:18080` (`Qwen3.6-35B-A3B-Heretic-Q4_K_M.gguf`, `262,144` context, `--reasoning-preserve --jinja --numa isolate -t 24 -ngl 99 -ot ".ffn_.*_exps.=CPU" -fa on -ctk q8_0 -ctv q8_0`).
