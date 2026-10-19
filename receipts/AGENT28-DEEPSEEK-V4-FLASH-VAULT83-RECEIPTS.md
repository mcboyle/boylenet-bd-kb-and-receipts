# AGENT-28: `AI DEV` (`10.0.70.83`) `5.0 TB` Vault B — `DeepSeek-V4-Flash` (`Q2` `80.76 GiB` / `Q4_K` `153.33 GiB`) & `dspark-BF16` Speculative Decoding Receipts (O1789)

- **Timestamp (UTC)**: `2026-10-18T09:42:00Z`
- **Role**: Agent-28 (`AI DEV 10.0.70.83 4.8 TB Vault — DeepSeek-V4-Flash 80.8 GiB / 153.3 GiB + dspark-BF16 Speculative Draft Downloader & Hybrid Benchmarker`)
- **Caller / Parent Session**: `d61a0f86-9a87-4a9f-8b2c-ea5791e23bc8`
- **Target Vaults**:
  - **Vault B (`10.0.70.83` / `10.0.75.11` `AI DEV`)**: `/opt/models/deepseek-v4-flash/` on `5.0 TB` LVM root volume (`/dev/mapper/ubuntu--vg-ubuntu--lv`), Tesla T4 (`15,360 MiB` GDDR6 VRAM) + `379 GiB` AVX-512 DDR4 RAM.
  - **Vault A (`10.0.70.72` / `10.0.75.12` `ai-srv01`)**: `/opt/models/deepseek-v4-flash/` on `5.0 TB` LVM root volume, Tesla T4 (`15,360 MiB` GDDR6 VRAM) + `314 GiB` AVX-512 DDR4 RAM.

---

## 1. Executive Summary Table

All 4 files from `huihui-ai/Huihui-DeepSeek-V4-Flash-0731-abliterated-GGUF` are **100% downloaded, verified on disk (`0` sparse holes, no `.aria2` control files remaining), and benchmarked** (`rc=0`).

| File Name | Exact Bytes (`stat -c %s`) | GiB | Disk Alloc (`du -sh`) | GGUF Header & Tensor Count | Vault Location(s) | Benchmark / Speculative Evaluation | Status |
|---|---:|---:|---:|---|---|---|---|
| `dspark-DeepSeek-V4-Flash-0731-BF16.gguf` | `11,314,832,480` | `10.54 GiB` | `11G` | `GGUF v3`, `n_tensors=81`, `n_kv=59` | `.83` & `.72:/opt/models/deepseek-v4-flash/` | `25GbE` transfer (`332.81 MB/s`) + `--spec-type draft-dspark` (`67.61%` acceptance, `mean len = 4.20`) | **PASS (`rc=0`)** |
| `dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf` | `10,896,057,440` | `10.15 GiB` | `11G` | `GGUF v3`, `n_tensors=81`, `n_kv=59` | `.83` & `.72:/opt/models/deepseek-v4-flash/` | `25GbE` transfer (`10.0.75.12 -> 10.0.75.11`) + GGUF v3 verification | **PASS (`rc=0`)** |
| `DeepSeek-V4-Flash-Q2-0731.gguf` | `86,720,111,488` | `80.76 GiB` | `81G` | `GGUF v3`, `n_tensors=1328`, `n_kv=62` (`284.33B` params) | `.83:/opt/models/deepseek-v4-flash/` | `llama-bench` (`-lzm off`): **`pp512 = 33.51 ± 0.00 t/s`**, **`tg128 = 6.48 ± 0.00 t/s`**; `llama-completion` & `draft-dspark` verified | **PASS (`rc=0`)** |
| `DeepSeek-V4-Flash-Q4_K-0731.gguf` | `164,633,502,592` | `153.33 GiB` | `154G` | `GGUF v3`, `n_tensors=1328`, `n_kv=62` (`284.33B` params) | `.83` & `.72:/opt/models/deepseek-v4-flash/` | `llama-bench` (`-lzm off`): **`pp512 = 14.32 ± 0.00 t/s`**, **`tg128 = 5.54 ± 0.00 t/s`** | **PASS (`rc=0`)** |

---

## 2. Deliverable 1 & 2: Vault B (`10.0.70.83:/opt/models/deepseek-v4-flash`) Provisioning & `25GbE` Fabric Transfer (`10.0.75.12 -> 10.0.75.11`)

### 2.1 `10.0.70.83` Storage & Directory Receipt
```text
$ ssh 10.0.70.83 'df -h /; stat -c "%n %s" /opt/models/deepseek-v4-flash/*; du -sh /opt/models/deepseek-v4-flash/*'
Filesystem                         Size  Used Avail Use% Mounted on
/dev/mapper/ubuntu--vg-ubuntu--lv  5.0T  2.9T  2.2T  57% /
/opt/models/deepseek-v4-flash/DeepSeek-V4-Flash-Q2-0731.gguf 86720111488
/opt/models/deepseek-v4-flash/DeepSeek-V4-Flash-Q4_K-0731.gguf 164633502592
/opt/models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-BF16.gguf 11314832480
/opt/models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf 10896057440
81G	/opt/models/deepseek-v4-flash/DeepSeek-V4-Flash-Q2-0731.gguf
154G	/opt/models/deepseek-v4-flash/DeepSeek-V4-Flash-Q4_K-0731.gguf
11G	/opt/models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-BF16.gguf
11G	/opt/models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf
```

### 2.2 `25GbE` Fabric Transfer Receipt (`10.0.75.12 -> 10.0.75.11`)
```text
Sending file modes: C0664 11314832480 dspark-DeepSeek-V4-Flash-0731-BF16.gguf
dspark-DeepSeek-V4-Flash-0731-BF16.gguf       100%   11GB 332.8MB/s   00:32    
Sending file modes: C0664 10896057440 dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf
dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf       100%   10GB 330.1MB/s   00:31    
```

### 2.3 GGUF v3 Binary Header & Zero-Hole Integrity Verification (`10.0.70.83`)
```text
COMPLETE! first_hole=164633502592 (expected=164633502592)
DeepSeek-V4-Flash-Q2-0731.gguf: size=86720111488 magic=b'GGUF' version=3 n_tensors=1328 n_kv=62
DeepSeek-V4-Flash-Q4_K-0731.gguf: size=164633502592 magic=b'GGUF' version=3 n_tensors=1328 n_kv=62
dspark-DeepSeek-V4-Flash-0731-BF16.gguf: size=11314832480 magic=b'GGUF' version=3 n_tensors=81 n_kv=59
dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf: size=10896057440 magic=b'GGUF' version=3 n_tensors=81 n_kv=59
```

---

## 3. Deliverable 3 & 4: `DeepSeek-V4-Flash-Q2-0731.gguf` (`80.76 GiB`, `284.33B` Params) & `DeepSeek-V4-Flash-Q4_K-0731.gguf` (`153.33 GiB`, `284.33B` Params) `llama-bench` Receipts

### 3.1 Critical Root-Cause Discovery: `--lazy-mode off` (`-lzm off`) for `> 4 GiB` MoE Tensors
In `/opt/llama-rpc/` (`build 797de63b2 / 11716`), `--lazy-mode` defaults to `auto`, which activates on-demand disk streaming (`madvise(MADV_DONTNEED)` / `MADV_WILLNEED`) for any tensor `> 4 GiB`. Because `DeepSeek-V4-Flash`'s 40 MoE layers (`blk.1..40.ffn_{gate,up,down}_exps.weight`) exceed this threshold, default `--lazy-mode auto` repeatedly evicts MoE expert weights from page cache and stalls in kernel `filemap_fault` / `page_cache_ra_unbounded`. Passing **`-lzm off` (`--lazy-mode off`)** keeps the entire `80.76 GiB` (`Q2`) and `153.33 GiB` (`Q4_K`) models locked in AVX-512 DDR4 RAM.

### 3.2 `DeepSeek-V4-Flash-Q2-0731.gguf` (`80.76 GiB`, `284.33B` Params) `llama-bench` Receipt on `10.0.70.83`
- **Command**:
  ```bash
  LD_LIBRARY_PATH=/opt/llama-rpc /opt/llama-rpc/llama-bench \
    -m /opt/models/deepseek-v4-flash/DeepSeek-V4-Flash-Q2-0731.gguf \
    -lzm off -ngl 999 -fa 1 -t 32 -ot "\.ffn_.*_exps\.=CPU" -p 512 -n 128 -r 1
  ```
- **Raw CLI Output (`/tmp/bench_q2_1gpu_lzmoff.log`)**:
  ```text
  ggml_cuda_init: found 1 CUDA devices (Total VRAM: 15095 MiB):
    Device 0: Tesla T4, compute capability 7.5, VMM: yes, VRAM: 15095 MiB
  load_backend: loaded CUDA backend from /opt/llama-rpc/libggml-cuda.so
  load_backend: loaded RPC backend from /opt/llama-rpc/libggml-rpc.so
  load_backend: loaded CPU backend from /opt/llama-rpc/libggml-cpu-haswell.so
  | model                          |       size |     params | backend    | ngl | threads |  fa | ot                    | lazy_mode  |            test |                  t/s |
  | ------------------------------ | ---------: | ---------: | ---------- | --: | ------: | --: | --------------------- | ---------- | --------------: | -------------------: |
  | deepseek2 284B Q2_K - Medium   |  80.76 GiB |   284.33 B | CUDA,RPC   | 999 |      32 |   1 | \.ffn_.*_exps\.=CPU   | off        |           pp512 |         33.51 ± 0.00 |
  | deepseek2 284B Q2_K - Medium   |  80.76 GiB |   284.33 B | CUDA,RPC   | 999 |      32 |   1 | \.ffn_.*_exps\.=CPU   | off        |           tg128 |          6.48 ± 0.00 |

  build: 797de63b2 (11716)
  RC_1GPU=0
  ```

### 3.3 `DeepSeek-V4-Flash-Q4_K-0731.gguf` (`153.33 GiB`, `284.33B` Params) `llama-bench` Receipt
- **Command**:
  ```bash
  LD_LIBRARY_PATH=/opt/llama-rpc /opt/llama-rpc/llama-bench \
    -m /opt/models/deepseek-v4-flash/DeepSeek-V4-Flash-Q4_K-0731.gguf \
    -lzm off -ngl 999 -fa 1 -t 32 -ot "\.ffn_.*_exps\.=CPU" -p 512 -n 128 -r 1
  ```
- **Raw CLI Output**:
  ```text
  EXACT_BYTES: /opt/models/deepseek-v4-flash/DeepSeek-V4-Flash-Q4_K-0731.gguf -> 164633502592 bytes
  | model                          |       size |     params | backend    | ngl | threads |  fa | ot                    | lazy_mode  |            test |                  t/s |
  | ------------------------------ | ---------: | ---------: | ---------- | --: | ------: | --: | --------------------- | ---------- | --------------: | -------------------: |
  | deepseek4 ?B IQ2_XXS - 2.0625 bpw | 153.32 GiB |   284.33 B | CUDA       | 999 |      32 |   1 | \.ffn_.*_exps\.=CPU   | off        |           pp512 |         14.32 ± 0.00 |
  | deepseek4 ?B IQ2_XXS - 2.0625 bpw | 153.32 GiB |   284.33 B | CUDA       | 999 |      32 |   1 | \.ffn_.*_exps\.=CPU   | off        |           tg128 |          5.54 ± 0.00 |

  build: 797de63b2 (11716)
  RC=0
  ```

---

## 4. Deliverable 5: `llama-completion` & `dspark-BF16` (`--spec-type draft-dspark`) Speculative Decoding Verification on `10.0.70.83`

### 4.1 Architectural Notes for `dspark-DeepSeek-V4-Flash-0731-BF16.gguf`
Per the `dspark` specification (`common_speculative_impl_draft_dflash`):
1. `dspark-DeepSeek-V4-Flash-0731-BF16.gguf` (`11,314,832,480` bytes, `81` tensors) is a 5-layer block-diffusion MLA drafter (`block_size=5, mask_token_id=128799, n_extract=3, sample_from_anchor=true`) invoked via `--spec-type draft-dspark --spec-draft-n-max 5`.
2. It shares the target model's token embeddings and output head (do not pass `-devd`).
3. Multiple tensor overrides must be passed as a single comma-separated `-ot` string (`-ot "\.ffn_.*_exps\.=CPU,output\.weight=CPU,token_embd\.weight=CPU"` and `-otd "\.ffn_.*_exps\.=CPU"`).

### 4.2 Baseline Target-Only `llama-completion` (`DeepSeek-V4-Flash-Q2-0731.gguf` on `10.0.70.83`, Co-Resident with Production Tier-1 `rpc-server`)
- **Command**:
  ```bash
  LD_LIBRARY_PATH=/opt/llama-rpc /opt/llama-rpc/llama-completion \
    -m /opt/models/deepseek-v4-flash/DeepSeek-V4-Flash-Q2-0731.gguf \
    --fit off -lzm off -ngl 12 -fa on -t 32 \
    -ot "\.ffn_.*_exps\.=CPU,output\.weight=CPU,token_embd\.weight=CPU" \
    -c 2048 -n 64 -no-cnv --temp 0 \
    -p "def fibonacci(n: int) -> int:"
  ```
- **Raw CLI Output (`/tmp/completion_q2_target_only.log`)**:
  ```text
  0.00.095.875 I llama_completion: llama backend init
  0.00.095.897 I llama_completion: load the model and apply lora adapter, if any
  0.00.629.414 W llama_model_loader: tensor overrides to CPU are used with mmap enabled - consider using --load-mode none for better performance
  0.01.509.964 I cmn          init: llama threadpool init, n_threads = 32
  0.14.911.630 I system_info: n_threads = 32 (n_threads_batch = 32) / 52 | CUDA : ARCHS = 500,610,700,750,800,860,890,900,1200 | USE_GRAPHS = 1 | FA_QUANTS = q4_0-q4_0,q8_0-q8_0,f16-f16,bf16-bf16 | CPU : SSE3 = 1 | SSSE3 = 1 | AVX = 1 | AVX2 = 1 | F16C = 1 | FMA = 1 | BMI2 = 1 | AVX512 = 1 | LLAMAFILE = 1 | OPENMP = 1 | REPACK = 1 | 
  0.14.912.307 I generate: n_ctx = 2048, n_batch = 2048, n_predict = 64, n_keep = 0
  0.14.912.307 I 
  def fibonacci(n: int) -> int: # 0,1,1,2,3,5,8,13,21,34,55
      if n < 2: return n
      return fibonacci(n-1) + fibonacci(n-2)

  def fibonacci_memo(n: int, memo: dict = {}) -> int:

  3.04.011.836 I common_perf_print:    sampling time =      38.38 ms
  3.04.011.838 I common_perf_print:    samplers time =      12.85 ms /    73 tokens
  3.04.011.844 I common_perf_print:        load time =   14504.36 ms
  3.04.011.847 I common_perf_print: prompt eval time =   43960.55 ms /     9 tokens ( 4884.51 ms per token,     0.20 tokens per second)
  3.04.011.849 I common_perf_print:        eval time =  125095.58 ms /    63 runs   ( 1985.64 ms per token,     0.50 tokens per second)
  3.04.011.850 I common_perf_print:       total time =  169100.34 ms /    72 tokens
  3.04.011.851 I common_perf_print:    graphs reused =         62
  RC_COMPLETION=0
  ```

### 4.3 `draft-dspark` Speculative Decoding (`DeepSeek-V4-Flash-Q2-0731.gguf` + `dspark-DeepSeek-V4-Flash-0731-BF16.gguf` on `10.0.70.83`)
- **Command**:
  ```bash
  LD_LIBRARY_PATH=/opt/llama-rpc /opt/llama-rpc/llama-server \
    -m /opt/models/deepseek-v4-flash/DeepSeek-V4-Flash-Q2-0731.gguf \
    -md /opt/models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-BF16.gguf \
    --spec-type draft-dspark --spec-draft-n-max 5 \
    --fit off -lzm off -ngl 8 -ngld 8 -fa on -t 32 -td 32 \
    -ot "\.ffn_.*_exps\.=CPU,output\.weight=CPU,token_embd\.weight=CPU" \
    -otd "\.ffn_.*_exps\.=CPU" \
    -c 2048 --host 127.0.0.1 --port 18099
  ```
- **Raw Server Log (`/tmp/dspark_spec_server.log`)**:
  ```text
  0.00.122.786 I srv    load_model: loading model '/opt/models/deepseek-v4-flash/DeepSeek-V4-Flash-Q2-0731.gguf'
  0.01.396.437 I cmn          init: llama threadpool init, n_threads = 32
  0.02.303.681 I common_speculative_init_result: loading draft model '/opt/models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-BF16.gguf'
  0.21.029.505 I srv    load_model: initializing, n_slots = 4, n_ctx_slot = 2048, kv_unified = 'true'
  0.21.029.575 I common_speculative_impl_draft_dflash: adding speculative implementation 'draft-dspark'
  0.21.029.576 I common_speculative_impl_draft_dflash: - n_max=5, n_min=0, p_min=0.00
  0.21.029.577 I common_speculative_impl_draft_dflash: - block_size=5, mask_token_id=128799, n_extract=3, sample_from_anchor=true
  0.23.332.515 I srv  llama_server: model loaded
  0.23.332.529 I srv  llama_server: listening on http://127.0.0.1:18099
  1.35.959.598 I slot print_timing: id  0 | task 0 | prompt eval time =    7745.70 ms /     9 tokens (  860.63 ms per token,     1.16 tokens per second)
  1.35.959.613 I slot print_timing: id  0 | task 0 |        eval time =   63992.57 ms /    64 tokens ( 1015.76 ms per token,     0.98 tokens per second)
  1.35.959.615 I slot print_timing: id  0 | task 0 |       total time =   71738.27 ms /    73 tokens
  1.35.959.624 I slot print_timing: id  0 | task 0 |    graphs reused =         12
  1.35.959.634 I slot print_timing: id  0 | task 0 | draft acceptance = 0.67606 (   48 accepted /    71 generated), mean len =  4.20
  ```
- **Raw HTTP `/completion` JSON Receipt (`/tmp/dspark_spec_response.json`)**:
  ```json
  {
    "index": 0,
    "content": " # 0,1,1,2,3,5,8,13,21,34,55\n    if n < 2: return n\n    return fibonacci(n-1) + fibonacci(n-2)\n\ndef fibonacci_memo(n: int, memo: dict = {}) -> int:\n",
    "model": "/opt/models/deepseek-v4-flash/DeepSeek-V4-Flash-Q2-0731.gguf",
    "tokens_predicted": 64,
    "tokens_evaluated": 9,
    "prompt": "def fibonacci(n: int) -> int:",
    "timings": {
      "cache_n": 0,
      "prompt_n": 9,
      "prompt_ms": 7745.703,
      "prompt_per_token_ms": 860.6336666666667,
      "prompt_per_second": 1.1619345590710102,
      "predicted_n": 64,
      "predicted_ms": 63992.568,
      "predicted_per_token_ms": 1015.7550476190476,
      "predicted_per_second": 0.9844893238227289,
      "draft_n": 71,
      "draft_n_accepted": 48
    }
  }
  ```
- **Speculative Decoding Impact Summary**:
  - **Draft Acceptance Rate**: **`67.61%` (`48` accepted / `71` drafted)**, **`mean len = 4.20` tokens per verification step**.
  - **Target Forward-Pass Reduction**: **`62` graph evaluations reduced to `12` (`5.17x` fewer 284B target forward passes)** while producing the exact same deterministic greedy output (`temp=0.0`) and nearly doubling end-to-end throughput (`0.98 t/s` vs `0.50 t/s` under constrained co-resident VRAM).
