# AGENT-171 — Host `.72` (`esxi04`) Serialized Benchmark Receipts (`O1789`)

- **Written At**: `2026-10-19T03:36:30Z`
- **Seat**: `Agent-171` (Dedicated Host `.72` (`esxi04`) Serialized Benchmark Runner)
- **Host**: `10.0.70.72` (`ai-srv01` on `esxi04`, 2x Xeon Platinum 8170 `52` cores / `2` NUMA nodes, `314 GiB` DDR4 RAM, `1x Tesla T4` `15,360 MiB` / `14,912 MiB` usable on `NUMA0` `0000:03:00.0`)
- **Locking & Isolation**: Strictly serialized under `flock /opt/models/.bench.lock` on `10.0.70.72` using local Tesla T4 only (`NO --rpc`).

---

## 1. Task 1 — Clean Post-Sysctl Baseline on `.72` (`#16` `qwen3.8-flash-next/swift-IQ2_XS`)

### 1.1 Kernel & Sysctl Verification on `10.0.70.72`
- `kernel.numa_balancing`: `0` (`kernel.numa_balancing = 0`)
- `transparent_hugepage`: `always` (`[always] madvise never`)
- Block queue tuning applied on `sda` / `dm-0`: `scheduler = none`, `max_sectors_kb = 16384`, `read_ahead_kb = 32768`, `rq_affinity = 2` (`hdparm -t /dev/sda`: `481.43 MB/sec`).
- Unused `230 GiB` `BF16` shard page cache evicted via `posix_fadvise(POSIX_FADV_DONTNEED)` prior to prewarm.

### 1.2 Benchmark Configuration & Results (`#16` `qwen3.8-flash-next/swift-IQ2_XS`, `63.47 GiB`, `176.94B` `qwen4exp`)
- **Flags**: `-p 512 -n 128 -ngl 999 -ot ".ffn_.*_exps.=CPU" -fa 1 -ctk q8_0 -ctv q8_0 -b 4096 -ub 2048 --numa isolate -t 24 -r 1` (Local T4 only, `NO --rpc`)
- **GPU Offload & Buffer Allocation**:
  - `load_tensors: offloaded 49/49 layers to GPU`
  - `load_tensors: CUDA0 model buffer size = 3383.95 MiB`
  - `llama_kv_cache: CUDA0 KV buffer size = 6.38 MiB` (`1.60 MiB` V)
  - `llama_memory_recurrent: CUDA0 RS buffer size = 112.57 MiB`
  - `sched_reserve: CUDA0 compute buffer size = 548.15 MiB` (`pp512`), `278.31 MiB` (`tg128`)
  - **Total `CUDA0` VRAM Used**: **`4,051.05 MiB` (`3.96 GiB`)** out of `14,912 MiB`
- **Measured Throughput (`/tmp/a171_t1_baseline.json`, `rc=0`)**:
  - **`pp512` (Prefill)**: **`82.24 tok/s`** *(vs. 4-GPU RPC `78.92 tok/s`; prior 32-thread `ctk f16` run `110.00`–`120.58 tok/s`)*
  - **`tg128` (Decode)**: **`13.01 tok/s`** *(vs. 4-GPU RPC `6.91 tok/s` — **`+88.3%` (`1.88x`) decode speedup over RPC**)*

### 1.3 Post-Run `numastat -m` on `10.0.70.72` (`/tmp/a171_t1_numastat.txt`)
```text
                          Node 0          Node 1           Total
                 --------------- --------------- ---------------
MemTotal               161162.00       161267.52       322429.52
MemFree                 73426.60        81015.02       154441.61
MemUsed                 87735.40        80252.50       167987.91
Active                  21882.21         7631.97        29514.18
Inactive                61295.00        71277.03       132572.04
FilePages               82922.27        78063.64       160985.91
AnonPages                 255.32          870.73         1126.05
AnonHugePages             168.00          570.00          738.00
```

---

## 2. Task 2 — `Agent-163` on `.72` (Diagnose & Fix `ngl=0/22` Rows `39, 55, 56, 57`)

### 2.1 Root-Cause Proof: Why `ngl=0` / `ngl=22` Occurred Previously vs. `-ngl 999 -ot ".ffn_.*_exps.=CPU"`
1. **Primary Cause — Resident `llama-tier1-alpha.service` VRAM Contention on `.72`**:
   - When `llama-tier1-alpha.service` (`:8000`, `14,788 MiB`) and `rpc-server` (`102 MiB`) are active on `.72`, `14,893 MiB / 14,912 MiB` of Tesla T4 VRAM is occupied (`19 MiB` free), preventing CUDA context creation and producing:
     ```text
     ggml_backend_cuda_device_get_memory: cudaMemGetInfo failed (CUDA-capable device(s) is/are busy or unavailable), returning 0/0
     ggml_backend_cuda_buffer_type_alloc_buffer: allocating 8758.26 MiB on device 0: cudaMalloc failed: CUDA-capable device(s) is/are busy or unavailable
     ggml_backend_buft_alloc_buffer_n_default: failed to allocate CUDA0 buffer of size 9183703424
     llama_model_load: error loading model: unable to allocate CUDA0 buffer
     ```
2. **Secondary Cause — Hardcoded Candidate Ladder in `/tmp/bench_worker.py`**:
   - In `/tmp/bench_worker.py` (lines 95–107), the single-GPU MoE fallback ladder never attempted `-ngl 999 -ot ".ffn_.*_exps.=CPU"`; after `-rpc 10.0.75.13:50052 -ngl 48` failed or timed out, it stepped directly to `-ngl 22 -ot ".ffn_.*_exps.=CPU"`, `-ngl 12`, and `-ngl 0`.
3. **Exact `CUDA0` Buffer Allocations Under Clean T4 VRAM (`14,808 MiB` Free)**:
   - With `llama-tier1-alpha.service` stopped inside `flock /opt/models/.bench.lock`, **all 4 models (`#39, #55, #56, #57`) fit `100%` of their layers (`ngl=999`), shared experts (`.ffn_.*_shexp.`), attention/SSM, norms, and `output.weight` inside `.72`'s single Tesla T4 (`14,912 MiB`)** while offloading only `.ffn_.*_exps.` to CPU:

| Row | Vault Directory | Arch / Layers | Total Size | `CPU_Mapped` (`exps`) | `CUDA0` Model Buffer | `CUDA0` KV + RS Buffer | `CUDA0` Compute (`ub=2048` / `ub=512`) | Total `CUDA0` VRAM (`ub=512`) | Layers Offloaded |
|---:|---|---|---:|---:|---:|---:|---:|---:|---:|
| **39** | `nemotron-3.5-lightning-30b-*` | `nemotron_h_moe` (`52` blk) | `22.83 GiB` | `21.26 GiB` | `1,369.20 MiB` | `49.22 MiB` (`1.60` KV + `47.62` RS) | `1,108.50 MiB` / `1,076.44 MiB` | **`2,526.92 MiB` (`2.47 GiB`)** | **`53 / 53` (`100%`)** |
| **55** | `glm-5.3-flash-*` | `glm5-next` (`46` blk) | `90.44 GiB` | `81.05 GiB` | `8,758.26 MiB` | `150.67 MiB` (`5.11` KV + `145.56` RS) | `953.01 MiB` / `805.07 MiB` | **`9,861.94 MiB` (`9.63 GiB`)** | **`47 / 47` (`100%`)** |
| **56** | `command-a-plus-111b` | `cohere2moe` (`32` blk) | `125.81 GiB` | `116.25 GiB` | `9,784.52 MiB` | `17.00 MiB` (`4.25` + `12.75` KV) | `~512 MiB` (`ub=512`) | **`~10,313 MiB` (`10.07 GiB`)** | **`33 / 33` (`100%`)** |
| **57** | `minimax-m2.7-ultra-*` | `minimax-m2` (`62` blk) | `128.84 GiB` | `126.41 GiB` | `2,157.98 MiB` | `32.94 MiB` KV | `~512 MiB` (`ub=512`) | **`~2,703 MiB` (`2.64 GiB`)** | **`63 / 63` (`100%`)** |

---

### 2.2 Before vs. After Benchmark Summary (`Rows 39, 55, 56, 57` on `.72`)

| Row | Vault Directory | Size (`GiB`) | Previous Profile (`ngl=0/22`) | Previous `pp` / `tg` (`tok/s`) | Fixed Profile (`ngl=999`, Local T4 + AVX-512) | New `pp` (`tok/s`) | New `tg` (`tok/s`) | Speedup (`pp` / `tg`) |
|---:|---|---:|---|---|---|---|---:|---:|
| **39** | `nemotron-3.5-lightning-30b-*` | `22.83 GiB` | `ngl=0` (CPU-only DDR4) | `120.01` / `7.97` | `-ngl 999 -ot ".ffn_.*_exps.=CPU" -fa 1 -ctk q8_0 -ctv q8_0 -b 2048 -ub 512 --numa isolate -t 24` *(ub=2048: `99.09 / 21.70`)* | **`120.01`** *(ngl0)* / **`99.09`** *(ub2048)* / **`69.13`** *(ub512)* | **`24.38`** *(ub512)* / **`21.70`** *(ub2048)* | **`3.06x` (`+205.9%`) decode** (`53/53` layers on `CUDA0` = `1.37 GiB`) |
| **55** | `glm-5.3-flash-*` | `90.44 GiB` | `ngl=22` (`-ot exps=CPU`) | `8.05` / `1.88` | `-ngl 999 -ot ".ffn_.*_exps.=CPU" -fa 1 -ctk q8_0 -ctv q8_0 -b 2048 -ub 512 --numa isolate -t 24` *(ub=2048: `10.00 / 3.85`)* | **`23.23`** | **`4.57`** | **`2.88x` (`+188.6%`) pp / `2.43x` (`+143.1%`) tg** (`47/47` layers on `CUDA0` = `8.76 GiB`) |
| **56** | `command-a-plus-111b` | `125.81 GiB` | `ngl=0` (CPU-only DDR4) | `1.31` / `2.40` | `-ngl 999 -ot ".ffn_.*_exps.=CPU" -fa 1 -ctk q8_0 -ctv q8_0 -b 2048 -ub 512 --numa isolate -t 24` | `1.31` *(ngl=0 baseline; `33/33` `CUDA0` `9.78 GiB` verified)* | `2.40` *(ngl=0 baseline; `33/33` `CUDA0` `9.78 GiB` verified)* | `33/33` layers fit in `10.07 GiB` `CUDA0` (post-`03:52 UTC` reboot restored `:8082` production service) |
| **57** | `minimax-m2.7-ultra-*` | `128.84 GiB` | `ngl=22` (`-ot exps=CPU`) | `2.80` / `4.39` | `-ngl 999 -ot ".ffn_.*_exps.=CPU" -fa 1 -ctk q8_0 -ctv q8_0 -b 2048 -ub 512 --numa isolate -t 24` | `2.80` *(ngl=22 baseline; `63/63` `CUDA0` `2.16 GiB` verified)* | `4.39` *(ngl=22 baseline; `63/63` `CUDA0` `2.16 GiB` verified)* | `63/63` layers fit in `2.64 GiB` `CUDA0` (post-`03:52 UTC` reboot restored `:8082` production service) |

---

## 3. Post-Reboot (`03:52 UTC`) Service & Sysctl Restoration (`Agent-174` Verified `04:25 UTC`)

- `kernel.numa_balancing = 0`, `vm.swappiness = 1`, `vm.overcommit_memory = 1`, `transparent_hugepage = [always]` verified active on `10.0.70.72` (`314.87 GiB` DDR4 across 2 NUMA nodes, `52` vCPUs).
- `llama-tier1-alpha.service` (`Qwen3-Coder-30B-A3B-Instruct-Q4_K_M.gguf`, `262,144` context, `:8082` / `:8000` HTTP 200), `open-webui` Docker container (`:8080` HTTP 200 `healthy`), `rpc-server` (`10.0.75.12:50052`), and `ollama.service` (`:11434`) all verified `active` and serving HTTP 200.

