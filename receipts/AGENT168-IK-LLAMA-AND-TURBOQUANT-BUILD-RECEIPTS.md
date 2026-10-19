# AGENT-168: `ik_llama.cpp` + `turboquant-ggml` Off-Host Build & CUDA `-fa` Receipts

- **WRITTEN-AT**: `2026-10-19T02:39:10Z`
- **AGENT**: `Agent-168` (Off-Host Engine Builder A — `.125`, `.83`, `.72`)
- **BUILD HOST**: `10.0.70.125` (`ai-infer01`, 48-core Xeon E5-2690 v4, 0 benchmark contention on `.83`/`.72`) + `/var/tmp/ik_llama.cpp` staged on `.125` and `.83`
- **DEPLOYMENT TARGETS**: `/opt/ik-llama/` and `/opt/turboquant/` on `10.0.70.83` (`aidev`) and `10.0.70.72` (`ai-srv01`)

---

## 1. `ik_llama.cpp` Build & Installation (`Skylake-SP AVX-512` + `Turing sm_75`)

### Source & CMake Configuration
- **Repository**: `https://github.com/ikawrakow/ik_llama.cpp`
- **Commit**: `c446f1e` (`llama: prefetch qwen4exp PLE rows before the graph (#2692)`)
- **Clone Path**: `/var/tmp/ik_llama.cpp` (on `.125`, `.83`, and `.164`)
- **Verified CMake Flags (`build/CMakeCache.txt`)**:
  ```text
  CMAKE_BUILD_TYPE:STRING=Release
  CMAKE_CUDA_ARCHITECTURES:UNINITIALIZED=75
  GGML_AVX512:BOOL=ON
  GGML_AVX512_BF16:BOOL=OFF
  GGML_AVX512_VBMI:BOOL=OFF
  GGML_AVX512_VNNI:BOOL=OFF
  GGML_CUDA:BOOL=ON
  GGML_CUDA_IQK_FORCE_BF16:BOOL=OFF
  ```
- **Instruction Set Verification (`objdump` / `cuobjdump`)**:
  - `objdump -d /opt/ik-llama/lib/libggml.so`: emits `%zmm` AVX-512 instructions (`vpmovzxwd`, `vfmadd231ps %zmm0,%zmm1,%zmm3`), zero `vpdpbusd`/`vpermb` (clean Skylake-SP `AVX512F/CD/VL/DQ/BW`, `VBMI=OFF`, `VNNI=OFF`).
  - `cuobjdump -lelf /opt/ik-llama/lib/libggml.so`: `sm_75.cubin` ELF sections embedded for Tesla T4.

### Installed Binaries & Shared Libraries (`/opt/ik-llama/` on `10.0.70.83` & `10.0.70.72`)
Configured `/etc/ld.so.conf.d/ik-llama.conf` + `ldconfig` on both `.83` and `.72` so `/opt/ik-llama/bin/*` resolves `/opt/ik-llama/lib/*` and CUDA 12 runtime libs without requiring `LD_LIBRARY_PATH`.

| Artifact Path (`10.0.70.83` & `10.0.70.72`) | Size (Bytes) | SHA256 |
|---|---:|---|
| `/opt/ik-llama/bin/llama-bench` | `4,084,224` | `95d7a11ebd0de825ae44d3ef706dab6d4d284b5789e17d6bf816ec3982ef469d` |
| `/opt/ik-llama/bin/llama-server` | `11,169,632` | `df02395c721187d64c2b6dfb18ee061bda921017f73114e28d24f79295141e68` |
| `/opt/ik-llama/bin/llama-sweep-bench` | `3,952,768` | `8b43fb964f50d1284d1253680fb87b8be60b85280ec808982c8bb59d6317fc40` |
| `/opt/ik-llama/lib/libggml.so` | `1,157,905,080` | `4b3a3ddb309e0c6667935e5ac816d1a8af6448507947ebb186b723bf4464dbd0` |
| `/opt/ik-llama/lib/libllama.so` | `4,083,240` | `4f62e36840ecb4b731684033307cce1b93a31fe66dec2eb2438f4519ab41daf2` |
| `/opt/ik-llama/lib/libmtmd.so` | `1,122,152` | `ba62e10444a1ea8e44d169ad013cdd6f2afd207c76e331f09e5aac4da872f2f3` |

### Runtime Smoke Check (`.83` & `.72`)
- **`.83`**: `version: 1 (c446f1e)` — `ggml_cuda_init: found 1 CUDA devices: Device 0: Tesla T4, compute capability 7.5, VMM: yes, VRAM: 15095 MiB`
- **`.72`**: `version: 1 (c446f1e)` — `ggml_cuda_init: found 1 CUDA devices: Device 0: Tesla T4, compute capability 7.5, VMM: yes, VRAM: 14912 MiB`

### `ik_llama.cpp` Vault Architecture Support Matrix (`src/llama-arch.cpp` & `src/llama-arch.h`)

All **6 of 6** target Vault architectures are natively supported in `ik_llama.cpp` (`c446f1e`):

| Vault Arch String | `ik_llama.cpp` Enum | Source Location (`src/llama-arch.cpp`) | Status |
|---|---|---|---|
| `deepseek2` | `LLM_ARCH_DEEPSEEK2` | `src/llama-arch.cpp:58` (`{ LLM_ARCH_DEEPSEEK2, "deepseek2" }`) | **SUPPORTED** (plus `deepseek4` `:59`, `deepseek41` `:60`) |
| `glm4moe` | `LLM_ARCH_GLM4_MOE` | `src/llama-arch.cpp:63` (`{ LLM_ARCH_GLM4_MOE, "glm4moe" }`) | **SUPPORTED** |
| `qwen3moe` | `LLM_ARCH_QWEN3MOE` | `src/llama-arch.cpp:32` (`{ LLM_ARCH_QWEN3MOE, "qwen3moe" }`) | **SUPPORTED** |
| `qwen35moe` | `LLM_ARCH_QWEN35MOE` | `src/llama-arch.cpp:36` (`{ LLM_ARCH_QWEN35MOE, "qwen35moe" }`) | **SUPPORTED** (hybrid recurrent/attention) |
| `glm-dsa` | `LLM_ARCH_GLM_DSA` | `src/llama-arch.cpp:89` (`{ LLM_ARCH_GLM_DSA, "glm-dsa" }`) | **SUPPORTED** |
| `glm5-next` | `LLM_ARCH_GLM5NEXT_DASHED` / `LLM_ARCH_GLM5NEXT` | `src/llama-arch.cpp:90-91` (`"glm5next"` / `"glm5-next"`) | **SUPPORTED** |

---

## 2. `turboquant-ggml` Inspection & Off-Host Build (`165B` CUDA `-fa` Check)

### Executive Verdict by Fork / Quant Type

1. **Upstream PR `#20969` Lineage (`Aaryan-Kapoor/llama.cpp@1fb1fb3a` `turboquant-tq3_0`, `animehacker/llama-turboquant@5bc5ed3b`, `ubergarm/ik_llama.cpp@2fd66fa5` `ug/turboquant-tq3_0` — `tq3_0` / `GGML_TYPE_TQ3_0`)**:
   - **Verdict**: **`CPU FALLBACK — DO NOT USE`**
   - **Code Proof**:
     - In `animehacker/llama-turboquant` (`ggml/src/ggml-cuda/fattn.cu:361-377`), `ggml_cuda_get_best_fattn_kernel()` switches on `K->type` and only admits `F32, F16, Q4_1, Q5_0, Q5_1, Q4_0, Q8_0, BF16`. `GGML_TYPE_TQ3_0` hits `default: return BEST_FATTN_KERNEL_NONE;`.
     - Consequently, `ggml_cuda_flash_attn_ext_supported()` (`ggml/src/ggml-cuda/ggml-cuda.cu:5060-5061`) returns `false` when `-fa on` is used with `-ctk tq3_0 -ctv tq3_0`, forcing `GGML_OP_FLASH_ATTN_EXT` to fall back to the CPU backend.
     - `ubergarm/ik_llama.cpp` (`ug/turboquant-tq3_0@2fd66fa5`, `ik_llama.cpp #1509`) touches zero files under `ggml/src/ggml-cuda/` (CPU-only).

2. **`atomicmilkshake/llama-cpp-turboquant` (formerly `spiritbuun/llama-cpp-turboquant-cuda` in `ik_llama.cpp #1509`, branch `feature/turboquant-kv-cache@8ad0f00e` — `turbo3_0` / `turbo4_0` / `turbo2_0`)**:
   - **Verdict**:
     - **Single-token decode (`Q->ne[1] == 1`, `tg`, `D in {64, 128, 256}`)**: **`CUDA FA SUPPORTED`** (via `BEST_FATTN_KERNEL_VEC` in `fattn-vec.cuh`)
     - **Prefill / batched decode (`Q->ne[1] > 1`, `pp`) on `sm_75`**: **`DEQUANTIZES FULL KV CACHE TO FP16 IN VRAM — DO NOT USE FOR LONG-CONTEXT VRAM SAVINGS`**
   - **Code Proof (`ggml/src/ggml-cuda/fattn.cu`, `fattn-common.cuh`, `fattn-vec.cuh`, `fattn-mma-f16.cuh`)**:
     - `ggml/src/ggml-cuda/fattn.cu:294-325` & `fattn-vec.cuh:635-702` instantiate CUDA vector FlashAttention kernels (`ggml_cuda_flash_attn_ext_vec_case<D, type_K, type_V>`) for `D in {64, 128, 256}` across `GGML_TYPE_TURBO3_0`, `GGML_TYPE_TURBO4_0`, `GGML_TYPE_TURBO2_0`, and mixed `Q8_0` combinations, using device functions `vec_dot_fattn_vec_KQ_turbo3_0` / `vec_dot_fattn_vec_KQ_turbo4_0` (`fattn-common.cuh:935-940`) and `dequantize_V_turbo3_0` / `dequantize_V_turbo4_0` (`fattn-common.cuh:745-917, 963-968`) with `need_f16_K = false, need_f16_V = false` (`fattn-vec.cuh:562-565`).
     - **However**, in `ggml_cuda_get_best_fattn_kernel()` (`fattn.cu:456-478`), on Turing `sm_75` (`turing_mma_available(cc) == true`), `BEST_FATTN_KERNEL_VEC` is **only** returned when `Q->ne[1] == 1` (`fattn.cu:468-470`). Whenever `Q->ne[1] > 1` (all prompt processing `pp` and batched decode), it returns `BEST_FATTN_KERNEL_MMA_F16` (`fattn.cu:477`).
     - `ggml_cuda_flash_attn_ext_mma_f16_case` (`fattn-mma-f16.cuh:1793-1794`) calls `launch_fattn(..., need_f16_K = true, need_f16_V = true)`. Inside `launch_fattn` (`fattn-common.cuh:1229-1289`), `K_f16.alloc(ggml_nelements(K))` and `V_f16.alloc(ggml_nelements(V))` allocate **full-size uncompressed FP16 copies of the entire K and V caches in CUDA pool memory** and dequantize the whole cache via `dequantize_block_cont_cuda<..., dequantize_turbo3_0/turbo4_0>` (`convert.cu:760-765`) before running the Tensor Core MMA kernel. On a 16 GB Tesla T4 (`sm_75`), this spike-allocates the full uncompressed FP16 KV footprint during every prefill/batched pass.
     - Additionally, `ikawrakow` demonstrated in `ik_llama.cpp #1509` that `Q4_0`/`Q8_0` KV cache with Hadamard rotation (`-khad -vhad` in `ik_llama.cpp`) achieves lower perplexity (`PPL 3.6129` vs `3.8527` on Llama-3.1-70B `n_ctx=8192`) and 23% faster throughput without full-cache FP16 scratch allocation.

### Off-Host Build Receipt (`/opt/turboquant/` on `10.0.70.125` -> Synced to `10.0.70.83` & `10.0.70.72`)
Built `atomicmilkshake/llama-cpp-turboquant` (`feature/turboquant-kv-cache@8ad0f00e`) on `10.0.70.125` (`-j 48`, zero CPU contention on `.83`/`.72`) with:
```bash
cmake -B build \
  -DCMAKE_BUILD_TYPE=Release \
  -DGGML_NATIVE=OFF \
  -DGGML_AVX512=ON \
  -DGGML_AVX512_VBMI=OFF \
  -DGGML_AVX512_VNNI=OFF \
  -DGGML_AVX512_BF16=OFF \
  -DGGML_AVX2=ON -DGGML_AVX=ON -DGGML_FMA=ON -DGGML_F16C=ON -DGGML_BMI2=ON \
  -DGGML_CUDA=ON \
  -DCMAKE_CUDA_ARCHITECTURES=75
cmake --build build -j 48 --target llama-bench llama-server
```

| Artifact Path (`10.0.70.83` & `10.0.70.72`) | Size (Bytes) | SHA256 |
|---|---:|---|
| `/opt/turboquant/bin/llama-bench` | `1,978,912` | `8b2b8e17d1c616a3f463d0a3f089d78bdf54f1fdf145033c1026cd9aac455e6f` |
| `/opt/turboquant/bin/llama-server` | `7,795,400` | `a2f9ebe0bb1b16b493fdb293aac3e980e540a6aca8ca18080b9232d9d181d6e8` |
| `/opt/turboquant/lib/libggml-cuda.so.0.9.8` | `225,697,376` | `34ca99f63c39a1abc9c9e31574e7a7e990d56019790896bbdcf435ee511d8074` |
| `/opt/turboquant/lib/libggml-cpu.so.0.9.8` | `1,345,824` | `d3d414b2318ddbfbe84def59fd4dc62a9c8fba9b1eff7c9ce56a3f9466540c08` |
| `/opt/turboquant/lib/libllama.so.0.0.10` | `3,915,848` | `4ca05417a47c743fddc244cbf402523b57733113da04aa657f2a2a5e92dc319d` |
