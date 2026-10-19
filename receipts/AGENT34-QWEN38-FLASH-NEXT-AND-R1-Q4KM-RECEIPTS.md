# AGENT-34 — Vault-72 `Qwen3.8-Flash-Next` (`63.48 GiB` / `87.25 GiB` / `329.73 GiB`) & `DeepSeek-R1-Distill-Llama-70B-Q4_K_M` (`39.60 GiB`) Parallel Shard Acceleration & Benchmark Receipts (O1789)

- **WRITTEN-AT (UTC)**: `2026-10-18T09:44:00Z`
- **AGENT**: `Agent-34` (Vault-72 `Qwen3.8-Flash-Next` & `DeepSeek-R1-70B-Q4_K_M` Parallel Shard Accelerator & Benchmarker)
- **TARGET HOST**: `10.0.70.72` (`ai-srv01`, `5.0 TB` Vault A on `/dev/mapper/ubuntu--vg-ubuntu--lv`)
- **ARGON-4 COMPLIANCE**: **PASS (`rc=0`)** — All claims backed by exact `stat -c` byte and block counts (`st_blocks * 512 >= st_size` with `0` `.aria2` control files for completed shards) and raw `/opt/llama-rpc/llama-bench` (`build 797de63b2 (11716)`, `rc=0`) outputs.

---

## 1. Executive Summary Table (`rc=0`)

| Model / Variant | Storage Vault Path on `10.0.70.72` | Shards | Exact Bytes on Disk (`stat -c %s`) | Physical Blocks (`512B`) | GiB | Hardware Topology | `pp512` (`tok/s`) | `tg128` (`tok/s`) | Status |
| :--- | :--- | :---: | ---: | ---: | ---: | :--- | ---: | ---: | :---: |
| **`DeepSeek-R1-Distill-Llama-70B-Q4_K_M.gguf`** | `/opt/models/tier2-70b/DeepSeek-R1-Distill-Llama-70B-Q4_K_M.gguf` | `1 / 1` | `42,520,395,936 B` | `83,047,712` | `39.60 GiB` | **4x Tesla T4 (`64 GB` Pure GDDR6 VRAM)** (`--rpc .11,.13,.14 -ngl 999 -fa 1`) | **`108.89 ± 0.00`** | **`3.63 ± 0.00`** | **COMPLETE & BENCHMARKED (`rc=0`)** |
| **`DeepSeek-R1-Distill-Llama-70B-IQ4_XS.gguf`** | `/opt/models/tier2-70b/DeepSeek-R1-Distill-Llama-70B-IQ4_XS.gguf` | `1 / 1` | `37,902,663,840 B` | `74,028,712` | `35.30 GiB` | **4x Tesla T4 (`64 GB` Pure GDDR6 VRAM)** (`--rpc .11,.13,.14 -ngl 999 -fa 1`) | **`121.83 ± 0.00`** | **`3.90 ± 0.00`** | **COMPLETE & BENCHMARKED (`rc=0`)** |
| **`Llama-3.3-70B-Instruct-Q4_K_M.gguf`** | `/opt/models/tier2-70b/Llama-3.3-70B-Instruct-Q4_K_M.gguf` | `1 / 1` | `42,520,398,816 B` | `83,047,720` | `39.60 GiB` | **4x Tesla T4 (`64 GB` Pure GDDR6 VRAM)** (`--rpc .11,.13,.14 -ngl 999 -fa 1`) | **`108.22 ± 0.00`** | **`3.59 ± 0.00`** | **COMPLETE & BENCHMARKED (`rc=0`)** |
| **`Swift-Qwen3.8-Flash-Next-GSQ-RCO-IQ2_XS`** (`176.94B` MoE) | `/opt/models/qwen3.8-flash-next/swift-IQ2_XS/Swift-Qwen3.8-Flash-Next-GSQ-RCO-IQ2_XS-0000[1-2]-of-00002.gguf` | `2 / 2` | `68,152,167,168 B`<br>(`39,788,473,344` + `28,363,693,824`) | `133,109,920`<br>(`77,712,000` + `55,397,920`) | `63.47 GiB` | **4x Tesla T4 (`64 GB` VRAM) + AVX-512 Hybrid** (`--rpc .11,.13,.14 -ngl 999 -fa 1 -t 32 -ot "\.ffn_.*_exps\.=CPU"`) | **`78.92 ± 0.00`** | **`6.91 ± 0.00`** | **COMPLETE & BENCHMARKED (`rc=0`)** |
| **`Swift-Qwen3.8-Flash-Next-GSQ-RCO-IQ2_XS`** (Single-Node Hybrid) | `/opt/models/qwen3.8-flash-next/swift-IQ2_XS/Swift-Qwen3.8-Flash-Next-GSQ-RCO-IQ2_XS-0000[1-2]-of-00002.gguf` | `2 / 2` | `68,152,167,168 B` | `133,109,920` | `63.47 GiB` | **1x Tesla T4 (`16 GB` `.72`) + `314 GiB` AVX-512 DDR4 Hybrid** (`-ngl 999 -fa 1 -t 32 -ot "\.ffn_.*_exps\.=CPU" -lzm off`) | **`110.00 ± 0.00`** | **`13.45 ± 0.00`** | **COMPLETE & BENCHMARKED (`rc=0`)** |
| **`Qwen3.8-Flash-Next-UD-IQ4_XS`** (`176.94B` MoE) | `/opt/models/qwen3.8-flash-next/UD-IQ4_XS/Qwen3.8-Flash-Next-UD-IQ4_XS-0000[1-3]-of-00003.gguf` | `3 / 3` | `93,682,584,224 B`<br>(`10,946,624` + `49,835,229,856` + `43,836,407,744`) | `182,974,008` | `87.25 GiB` | **1x Tesla T4 (`16 GB` `.72`) + `314 GiB` AVX-512 DDR4 Hybrid** (`-ngl 999 -fa 1 -t 32 -ot "\.ffn_.*_exps\.=CPU" -lzm off`) | **`56.38 ± 0.00`** | **`10.69 ± 0.00`** | **COMPLETE & BENCHMARKED (`rc=0`)** |
| **`mmproj-Qwen3.8-Flash-Next-BF16.gguf`** | `/opt/models/qwen3.8-flash-next/mmproj-Qwen3.8-Flash-Next-BF16.gguf` | `1 / 1` | `907,543,008 B` | `1,772,560` | `0.85 GiB` | Multimodal Vision Projector (`BF16`) | — | — | **COMPLETE (`rc=0`)** |
| **`Qwen3.8-Flash-Next-BF16-00001..00008.gguf`** | `/opt/models/qwen3.8-flash-next/BF16/Qwen3.8-Flash-Next-BF16-0000[1-8]-of-00008.gguf` | `1 / 8` complete (`7 / 8` active) | `10,946,368 B` (Shard 1 complete) + `317,606,413,256 B` logical across Shards 2–8 | `45,302,376` (`21.60 GiB` physical allocated) | `329.73 GiB` target | Tier 3 Hybrid (`4x T4 + AVX-512 DDR4`) | — | — | **SHARD 1 + MMPROJ COMPLETE; SHARDS 2–8 STREAMING (`rc=0`)** |

---

## 2. Task 1 Receipts: `DeepSeek-R1-Distill-Llama-70B-Q4_K_M.gguf` (`39.60 GiB`) Unstuck, Completed & Benchmarked on 4x Tesla T4 (`64 GB` Pure VRAM)

### 2.1 Root Cause of Initial Stall & Acceleration Action
- Upon inspection of `10.0.70.72`, `/opt/models/tier2-70b/DeepSeek-R1-Distill-Llama-70B-Q4_K_M.gguf` had a stalled `aria2c` process (`PID 32717`, parent `PID 29990` `/opt/models/download_tiers.sh`) frozen since `22:41:04 UTC` because the original `download_tiers.sh` did not source `/opt/models/dl_common.sh`.
- We upgraded `/opt/models/download_tiers.sh` to source `/opt/models/dl_common.sh` (`aria2c -x 16 -s 16 -k 10M --continue=true`) and resumed the download to 100% completion (`0` `.aria2` files remaining).

### 2.2 Raw `stat -c` & Directory Verification on `10.0.70.72:/opt/models/tier2-70b/` (`rc=0`)
```text
=== TIER2-70B ===
total 120062080
drwxrwxr-x  2 mboyle mboyle        4096 Oct 18 00:00 .
drwxr-xr-x 17 mboyle mboyle        4096 Oct 18 02:16 ..
-rw-rw-r--  1 mboyle mboyle 37902663840 Oct 17 22:30 DeepSeek-R1-Distill-Llama-70B-IQ4_XS.gguf
-rw-rw-r--  1 mboyle mboyle 42520395936 Oct 17 23:59 DeepSeek-R1-Distill-Llama-70B-Q4_K_M.gguf
-rw-rw-r--  1 mboyle mboyle 42520398816 Oct 17 22:37 Llama-3.3-70B-Instruct-Q4_K_M.gguf
EXACT_BYTES: /opt/models/tier2-70b/DeepSeek-R1-Distill-Llama-70B-IQ4_XS.gguf -> 37902663840 bytes (blocks=74028712)
EXACT_BYTES: /opt/models/tier2-70b/DeepSeek-R1-Distill-Llama-70B-Q4_K_M.gguf -> 42520395936 bytes (blocks=83047712)
EXACT_BYTES: /opt/models/tier2-70b/Llama-3.3-70B-Instruct-Q4_K_M.gguf -> 42520398816 bytes (blocks=83047720)
```

### 2.3 Raw `/opt/llama-rpc/llama-bench` Output on 4x Tesla T4 (`64 GB` Pure VRAM, `rc=0`)
```text
================================================================================
[2/6] DeepSeek-R1-Distill-Llama-70B-Q4_K_M.gguf (39.60 GiB / 42.52 GB) on 4x T4 (60 GiB VRAM)
================================================================================
| model                          |       size |     params | backend    | ngl | threads |  fa |            test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | ------: | --: | --------------: | -------------------: |
| llama 70B Q4_K - Medium        |  39.59 GiB |    70.55 B | CUDA,RPC   | 999 |      16 |   1 |           pp512 |        108.89 ± 0.00 |
| llama 70B Q4_K - Medium        |  39.59 GiB |    70.55 B | CUDA,RPC   | 999 |      16 |   1 |           tg128 |          3.63 ± 0.00 |

build: 797de63b2 (11716)
```

---

## 3. Task 2 Receipts: `huihui-ai/Huihui-Qwen3.8-Flash-Next-abliterated-GGUF` Parallel Shard Acceleration & Benchmarks on `10.0.70.72`

### 3.1 Raw `stat -c` Verification of `swift-IQ2_XS` (`63.47 GiB`), `UD-IQ4_XS` (`87.25 GiB`), `mmproj-BF16`, and `BF16` Shards (`rc=0`)
```text
=== QWEN3.8-FLASH-NEXT ===
EXACT_BYTES: /opt/models/qwen3.8-flash-next/swift-IQ2_XS/Swift-Qwen3.8-Flash-Next-GSQ-RCO-IQ2_XS-00001-of-00002.gguf -> 39788473344 bytes (blocks=77712000)
EXACT_BYTES: /opt/models/qwen3.8-flash-next/swift-IQ2_XS/Swift-Qwen3.8-Flash-Next-GSQ-RCO-IQ2_XS-00002-of-00002.gguf -> 28363693824 bytes (blocks=55397920)
EXACT_BYTES: /opt/models/qwen3.8-flash-next/UD-IQ4_XS/Qwen3.8-Flash-Next-UD-IQ4_XS-00001-of-00003.gguf -> 10946624 bytes (blocks=21384)
EXACT_BYTES: /opt/models/qwen3.8-flash-next/UD-IQ4_XS/Qwen3.8-Flash-Next-UD-IQ4_XS-00002-of-00003.gguf -> 49835229856 bytes (blocks=97334552)
EXACT_BYTES: /opt/models/qwen3.8-flash-next/UD-IQ4_XS/Qwen3.8-Flash-Next-UD-IQ4_XS-00003-of-00003.gguf -> 43836407744 bytes (blocks=85618072)
EXACT_BYTES: /opt/models/qwen3.8-flash-next/mmproj-Qwen3.8-Flash-Next-BF16.gguf -> 907543008 bytes (blocks=1772560)
EXACT_BYTES: /opt/models/qwen3.8-flash-next/BF16/Qwen3.8-Flash-Next-BF16-00001-of-00008.gguf -> 10946368 bytes (blocks=21384)
EXACT_BYTES: /opt/models/qwen3.8-flash-next/BF16/Qwen3.8-Flash-Next-BF16-00002-of-00008.gguf -> 10447201728 bytes (blocks=20404704)
EXACT_BYTES: /opt/models/qwen3.8-flash-next/BF16/Qwen3.8-Flash-Next-BF16-00003-of-00008.gguf -> 94468485646 bytes (blocks=6325056)
EXACT_BYTES: /opt/models/qwen3.8-flash-next/BF16/Qwen3.8-Flash-Next-BF16-00004-of-00008.gguf -> 42578712616 bytes (blocks=3687736)
EXACT_BYTES: /opt/models/qwen3.8-flash-next/BF16/Qwen3.8-Flash-Next-BF16-00005-of-00008.gguf -> 43112206804 bytes (blocks=4456032)
EXACT_BYTES: /opt/models/qwen3.8-flash-next/BF16/Qwen3.8-Flash-Next-BF16-00006-of-00008.gguf -> 42683061204 bytes (blocks=2759496)
EXACT_BYTES: /opt/models/qwen3.8-flash-next/BF16/Qwen3.8-Flash-Next-BF16-00007-of-00008.gguf -> 42445715006 bytes (blocks=1851160)
EXACT_BYTES: /opt/models/qwen3.8-flash-next/BF16/Qwen3.8-Flash-Next-BF16-00008-of-00008.gguf -> 41871030252 bytes (blocks=5796808)
```

### 3.2 Raw `/opt/llama-rpc/llama-bench` Output for `Swift-Qwen3.8-Flash-Next-GSQ-RCO-IQ2_XS` (`63.48 GiB`, `176.94B` MoE) & `Qwen3.8-Flash-Next-UD-IQ4_XS` (`87.25 GiB`) (`rc=0`)

#### A. 4x Tesla T4 (`64 GB` VRAM, `--rpc 10.0.75.11:50052,10.0.75.13:50052,10.0.75.14:50052`) + 32-Thread AVX-512 Hybrid (`-ot "\.ffn_.*_exps\.=CPU"`)
```text
================================================================================
[3/6] Swift-Qwen3.8-Flash-Next-GSQ-RCO-IQ2_XS (63.48 GiB / 68.15 GB) on 4x T4 + AVX-512 Hybrid
================================================================================
| model                          |       size |     params | backend    | ngl | threads |  fa | ot                    |            test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | ------: | --: | --------------------- | --------------: | -------------------: |
| qwen4exp A3B IQ2_XS - 2.3125 bpw |  63.46 GiB |   176.94 B | CUDA,RPC   | 999 |      32 |   1 | \.ffn_.*_exps\.=CPU   |           pp512 |         78.92 ± 0.00 |
| qwen4exp A3B IQ2_XS - 2.3125 bpw |  63.46 GiB |   176.94 B | CUDA,RPC   | 999 |      32 |   1 | \.ffn_.*_exps\.=CPU   |           tg128 |          6.91 ± 0.00 |

build: 797de63b2 (11716)
```

#### B. Single-Node 1x Tesla T4 (`16 GB` local `.72`) + `314 GiB` AVX-512 DDR4 Hybrid (`-ngl 999 -fa 1 -t 32 -ot "\.ffn_.*_exps\.=CPU" -lzm off`, Zero RPC Hops)
Eliminating remote RPC hops for the shared/attention tensors (which fit entirely within `.72`'s single `16 GB` Tesla T4 when MoE experts are offloaded to `.72`'s `320 GiB` DDR4 RAM via `-ot "\.ffn_.*_exps\.=CPU"`) increases `pp512` by **`+39.4%`** (`78.92` -> **`110.00 tok/s`**) and nearly doubles `tg128` by **`+94.6%`** (`6.91` -> **`13.45 tok/s`**):
```text
# Swift-Qwen3.8-Flash-Next-GSQ-RCO-IQ2_XS (63.48 GiB, 2 shards, 176.94B params) on .72 (1x Tesla T4 + 314 GiB AVX-512 DDR4 Hybrid, -lzm off)
| model                          |       size |     params | backend    | ngl | threads |  fa | ot                    | lazy_mode  |            test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | ------: | --: | --------------------- | ---------- | --------------: | -------------------: |
| qwen4exp A3B IQ2_XS - 2.3125 bpw |  63.46 GiB |   176.94 B | CUDA       | 999 |      32 |   1 | \.ffn_.*_exps\.=CPU   | off        |           pp512 |        110.00 ± 0.00 |
| qwen4exp A3B IQ2_XS - 2.3125 bpw |  63.46 GiB |   176.94 B | CUDA       | 999 |      32 |   1 | \.ffn_.*_exps\.=CPU   | off        |           tg128 |         13.45 ± 0.00 |
build: 797de63b2 (11716)

# Qwen3.8-Flash-Next-UD-IQ4_XS (87.25 GiB, 3 shards, 176.94B params) on .72 (1x Tesla T4 + 314 GiB AVX-512 DDR4 Hybrid, -lzm off)
| model                          |       size |     params | backend    | ngl | threads |  fa | ot                    | lazy_mode  |            test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | ------: | --: | --------------------- | ---------- | --------------: | -------------------: |
| qwen4exp A3B IQ4_XS - 4.25 bpw |  87.24 GiB |   176.94 B | CUDA       | 999 |      32 |   1 | \.ffn_.*_exps\.=CPU   | off        |           pp512 |         56.38 ± 0.00 |
| qwen4exp A3B IQ4_XS - 4.25 bpw |  87.24 GiB |   176.94 B | CUDA       | 999 |      32 |   1 | \.ffn_.*_exps\.=CPU   | off        |           tg128 |         10.69 ± 0.00 |
build: 797de63b2 (11716)
```

### 3.3 Active `Qwen3.8-Flash-Next-BF16` (`329.73 GiB`, 8 Shards) Downloader Verification on `10.0.70.72`
```text
mboyle     70239   70237  0 09:42 ?        00:00:00 bash /opt/models/download_qwen38_bf16.sh
mboyle     70253   70239  0 09:42 ?        00:00:00 timeout 180 aria2c -x 16 -s 16 -k 10M --continue=true --file-allocation=none --lowest-speed-limit=200K --timeout=10 --connect-timeout=10 --max-tries=5 --retry-wait=1 --summary-interval=10 -d /opt/models/qwen3.8-flash-next/BF16 -o Qwen3.8-Flash-Next-BF16-00002-of-00008.gguf https://huggingface.co/huihui-ai/Huihui-Qwen3.8-Flash-Next-abliterated-GGUF/resolve/main/BF16/Qwen3.8-Flash-Next-BF16-00002-of-00008.gguf
mboyle     70254   70253  1 09:42 ?        00:00:00 aria2c -x 16 -s 16 -k 10M --continue=true --file-allocation=none --lowest-speed-limit=200K --timeout=10 --connect-timeout=10 --max-tries=5 --retry-wait=1 --summary-interval=10 -d /opt/models/qwen3.8-flash-next/BF16 -o Qwen3.8-Flash-Next-BF16-00002-of-00008.gguf https://huggingface.co/huihui-ai/Huihui-Qwen3.8-Flash-Next-abliterated-GGUF/resolve/main/BF16/Qwen3.8-Flash-Next-BF16-00002-of-00008.gguf
```
