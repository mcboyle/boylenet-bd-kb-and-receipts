# Agent-169 Receipts: `gamozolabs/llama.cpp` (`multisocket-cpu-moe`), Mainline `llama.cpp` (`FA_ALL_QUANTS=ON`), & `dspark` `Q4_K_M` Drafter Quantize

- **Timestamp**: `2026-10-19T02:58:47Z`
- **Agent**: `Agent-169` (Off-Host Engine Builder B)
- **Target Nodes**: `10.0.70.83` (`ai-srv02`) & `10.0.70.72` (`ai-srv01`)

---

## 1. `dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf` -> `Q4_K_M` Quantization (`164` Prep)

- **Host**: `10.0.70.83`
- **Source Path**: `/mnt/ai-models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf` (`10,386.28 MiB`, `4.39 BPW`, 81 tensors: `41x f32`, `25x q8_0`, `6x bf16`, `9x mxfp4`)
- **Output Path**: `/mnt/ai-models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-Q4_K_M.gguf` (`11,729,015,904 bytes` / `11,180.65 MiB`, `4.73 BPW`)
- **Command Executed**:
  ```bash
  LD_LIBRARY_PATH=/opt/llama-rpc /opt/llama-rpc/llama-quantize --allow-requantize \
    /mnt/ai-models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf \
    /mnt/ai-models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-Q4_K_M.gguf \
    Q4_K_M 24
  ```
- **Quantization Log Summary** (`/var/tmp/quantize-dspark-q4km.log`):
  ```text
  llama_model_quantize_impl: model size  = 10386.28 MiB (4.39 BPW)
  llama_model_quantize_impl: quant size  = 11180.65 MiB (4.73 BPW)
  llama_quantize: quantize time = 2216731.78 ms
  llama_quantize:    total time = 2216731.78 ms
  ```
- **Critical Architecture / VRAM Note for Agent-164**:
  - `dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf` (`general.architecture = dflash`, `dflash.block_count = 3`, `general.size_label = 256x594M`) is a hybrid model whose 9 routed MoE expert tensors (`blk.{0,1,2}.ffn_{gate,up,down}_exps.weight`, each `[4096, 2048, 256]`) are **already quantized as `mxfp4` (`4.25 BPW`, `1088.00 MiB` each = `9,792 MiB`)** in both the `BF16` and `Q8_0` source GGUFs, while only the 25 dense/shared-expert/attention tensors (`fc.weight`, `attn_q_a`, `attn_q_b`, `attn_kv`, `attn_output_a`, `attn_output_b`, `ffn_*_shexp`) were `q8_0` (`~430 MiB`) and `markov_w1`/`markov_w2` were `bf16` (`126.25 MiB`).
  - Converting to `Q4_K_M` reduces the 25 dense/attention/markov tensors from `~556 MiB` (`q8_0`/`bf16`) down to **`~245 MiB` (`q4_K`/`q6_K`)**, while converting the 9 `mxfp4` (`4.25 BPW`) expert tensors to `8x q4_K` (`1152.00 MiB` each, `4.50 BPW`) + `1x q6_K` (`1680.00 MiB`, `6.56 BPW`).
  - **Recommended T4 (`15.3 GiB`) Speculative Launch Flags**: Pass **`--cpu-moe-draft` (`-cmoed`)** alongside `-md /mnt/ai-models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-Q4_K_M.gguf` so the 9 draft MoE expert tensors reside in host RAM while **only `~245 MiB` of `Q4_K_M` draft attention/fc/markov weights reside in T4 VRAM** alongside `DeepSeek-V4-Flash Q2` attention + `-c 16384` `q8_0` KV cache.

---

## 2. `gamozolabs/llama.cpp` (`multisocket-cpu-moe` Branch) Build & Deployment (`160` Prep)

- **Repository / Branch / Commit**: `https://github.com/gamozolabs/llama.cpp` (`multisocket-cpu-moe`, commit `9e51941`)
- **Source Checkout**: `/var/tmp/gamozo-llama` on `10.0.70.83` and `10.0.70.72`
- **Deployed Install Path**: `/opt/gamozo-llama/` on both `10.0.70.83` and `10.0.70.72`
- **CMake Build Flags**:
  ```bash
  cmake -S /var/tmp/gamozo-llama -B /var/tmp/gamozo-llama/build -G Ninja \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_C_COMPILER=/usr/bin/gcc-14 \
    -DCMAKE_CXX_COMPILER=/usr/bin/g++-14 \
    -DGGML_CUDA=ON \
    -DCMAKE_CUDA_ARCHITECTURES=75 \
    -DCMAKE_CUDA_HOST_COMPILER=/usr/bin/g++-12 \
    -DGGML_AVX512=ON \
    -DGGML_OPENMP=OFF \
    -DGGML_RPC=ON
  ```
  *(Note: Ubuntu 24.04's CUDA 12.0 `cudafe++` requires `g++-12` as `-ccbin` because `libstdc++-14` `<bits/utility.h>` uses GCC-14-only `__type_pack_element` intrinsics rejected by CUDA 12.0's EDG parser; all C/C++ CPU and host targets including `libggml-cpu.so`, `llama-server`, and `llama-bench` are compiled with `/usr/bin/gcc-14` and `/usr/bin/g++-14` `14.2.0` + `-DGGML_AVX512=ON -march=native`.)*

### Deployed Artifact Verification (`.83` & `.72`)
- `/opt/gamozo-llama/llama-server`: `SHA256 = 0b828d94804b3ef43fc0f1a20caad78efa16133e8a0d4f984ec4ea61bbcfae8a` (`version: 20 (9e51941)`, `built with GNU 14.2.0 for Linux x86_64`)
- `/opt/gamozo-llama/llama-bench`: `SHA256 = d89e2af840316fc2843dab246d36d98ccf67807c086866d82b87144994f21fa1`
- `/opt/gamozo-llama/libggml-cpu.so.0.18.0`: `SHA256 = 9de481e2e246f2c0d081eb1492f39408d78b4354cdbc80b337c572a9bc171e0a` (`1.4 MiB`)
- `/opt/gamozo-llama/libggml-cuda.so.0.18.0`: `185 MiB` (`sm_75`)
- Verified symbols in `/opt/gamozo-llama/libggml-cpu.so`:
  - `FUSED-MOE-SILU path active (gate clamp [%g,%g], up clamp [%g,%g], down %s)`
  - `GGML_EXPERT_SHARD`, `GGML_EXPERT_REPLICAS`, `GGML_PIN_CORE`, `GGML_CPU_DISABLE_FUSION`

### Exact `ggml_type` Quant Formats Supported by `gamozolabs/llama.cpp` Fused Multi-Token MoE FFN Kernel
In `/var/tmp/gamozo-llama/ggml/src/ggml-cpu/ggml-cpu.c`:
- **Graph Fusion Dispatcher (`ggml_cpu_try_fuse_ops`, lines 3710–3830)**:
  - Fuses 3-op (`MUL_MAT_ID + MUL_MAT_ID + GLU`), 5-op clamped (`MUL_MAT_ID + CLAMP + MUL_MAT_ID + CLAMP + GLU`, e.g. `LLM_ARCH_DEEPSEEK4`), and 6-op full FFN (`MUL_MAT_ID + CLAMP + MUL_MAT_ID + CLAMP + GLU + MUL_MAT_ID` including down projection) into `ggml_compute_forward_fused_moe_silu`.
  - **Shape & Type Guards**:
    1. `ggml_nrows(src1) <= 8 && src1->ne[1] == 1 && ids->ne[0] * ids->ne[1] <= 64` (multi-token ubatches up to `n_tokens <= 8` and up to `64` `(token, expert_slot)` pairs).
    2. `gate_mmid->src[0]->type == up_mmid->src[0]->type` (`gate_exps` and `up_exps` must share the same `ggml_type`; `down_exps` can be any supported `down_type` independently!).
    3. `src1->type == GGML_TYPE_F32` (or `src1->type == vec_dot_type`).
    4. Dispatches dynamically through `type_traits_cpu[type].vec_dot`, `type_traits_cpu[type].vec_dot_type`, and `type_traits_cpu[vec_dot_type].from_float` (lines 1973–1977 & 2190–2194).
- **Complete List of All 28 `ggml_type` Weight Formats Supported by `ggml_compute_forward_fused_moe_silu`** (`ggml-cpu.c` lines 206–395):

| Category | Supported `ggml_type` Weight Formats | Activation `.vec_dot_type` | CPU Dot Kernel (`.vec_dot`) |
|---|---|---|---|
| **Float / Unquantized (3)** | `GGML_TYPE_F32`, `GGML_TYPE_F16`, `GGML_TYPE_BF16` | `F32`, `F16`, `BF16` | `ggml_vec_dot_{f32,f16,bf16}` |
| **Microscaling / FP4 (2)** | `GGML_TYPE_MXFP4`, `GGML_TYPE_NVFP4` | `GGML_TYPE_Q8_0` | `ggml_vec_dot_{mxfp4,nvfp4}_q8_0` |
| **Legacy / Symmetric Quants (7)** | `GGML_TYPE_Q1_0`, `GGML_TYPE_Q2_0`, `GGML_TYPE_Q4_0`, `GGML_TYPE_Q4_1`, `GGML_TYPE_Q5_0`, `GGML_TYPE_Q5_1`, `GGML_TYPE_Q8_0` | `Q8_0` (`Q4_1`/`Q5_1` use `Q8_1`) | `ggml_vec_dot_q*_q8_{0,1}` |
| **K-Quants (5)** | `GGML_TYPE_Q2_K`, `GGML_TYPE_Q3_K`, `GGML_TYPE_Q4_K`, `GGML_TYPE_Q5_K`, `GGML_TYPE_Q6_K` | `GGML_TYPE_Q8_K` | `ggml_vec_dot_q{2,3,4,5,6}_K_q8_K` |
| **I-Quants (9)** | `GGML_TYPE_IQ1_S`, `GGML_TYPE_IQ1_M`, `GGML_TYPE_IQ2_XXS`, `GGML_TYPE_IQ2_XS`, `GGML_TYPE_IQ2_S`, `GGML_TYPE_IQ3_XXS`, `GGML_TYPE_IQ3_S`, `GGML_TYPE_IQ4_NL`, `GGML_TYPE_IQ4_XS` | `Q8_K` (`IQ4_NL` uses `Q8_0`) | `ggml_vec_dot_iq*_q8_{K,0}` |
| **Ternary Quants (2)** | `GGML_TYPE_TQ1_0`, `GGML_TYPE_TQ2_0` | `GGML_TYPE_Q8_K` | `ggml_vec_dot_tq{1,2}_0_q8_K` |

---

## 3. Mainline `llama.cpp` `master` with `GGML_CUDA_FA_ALL_QUANTS=ON` & `--moe-cache-mib` (`161` Prep)

- **Audit of Existing `/opt/llama-rpc` (`b11716`, commit `797de63b2`)**:
  - `/opt/llama-rpc/llama-server` already had `--moe-cache-mib N`, **but** `/opt/llama-rpc/libggml-cuda.so` was built **without** `GGML_CUDA_FA_ALL_QUANTS=ON` (only **24** `ggml_cuda_flash_attn_ext_vec_case` symbols: symmetric `f16`, `bf16`, `q4_0`, `q8_0` only).
- **Mainline `master` Build & Deployment**:
  - Built latest mainline `llama.cpp` `master` (commit `31288c9`) with `-DGGML_CUDA=ON -DCMAKE_CUDA_ARCHITECTURES=75 -DGGML_CUDA_FA_ALL_QUANTS=ON -DGGML_AVX512=ON -DGGML_RPC=ON` using `gcc-14`/`g++-14`.
  - Deployed to `/opt/llama-master/` (and `/opt/llama-mainline/`) on both `10.0.70.83` and `10.0.70.72`.
- **Verification on `.83` & `.72`**:
  - `/opt/llama-master/llama-server`: `SHA256 = 9b6bbf2b0ffb2a1f91308b8063110f554d2137273d38b3ca1bd7bb2bc6e3ce7a` (`version: 0.7.0-dev (build 1, commit 31288c9)`, `built with GNU 14.2.0 for Linux x86_64`)
  - `/opt/llama-master/llama-bench`: `SHA256 = 1a97b2601e0396f11486e684d2301f1f54a518ab8181b8d3a18ccd7bdc4dc190`
  - `/opt/llama-master/libggml-cuda.so.0.27.0`: `SHA256 = 7df876f0a7b8b3f9cd3e15a4745c9dcec09d8b4a4157ea34fb5f98cdc218212a` (`264 MiB`)
  - `strings /opt/llama-master/libggml-cuda.so | grep "ggml_cuda_flash_attn_ext_vec_case" | wc -l`: **`294`** (all **49** K-V type combinations across `{f16, bf16, q8_0, q5_1, q5_0, q4_1, q4_0}` x `{f16, bf16, q8_0, q5_1, q5_0, q4_1, q4_0}` across head sizes).
  - `LD_LIBRARY_PATH=/opt/llama-master /opt/llama-master/llama-server --help | grep moe-cache-mib`:
    `--moe-cache-mib N                       GPU cache size in MiB for the MoE experts kept in the CPU.`
