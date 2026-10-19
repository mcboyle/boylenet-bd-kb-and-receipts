# AGENT-29 RECEIPTS: AI DEV `10.0.70.83` 4.8 TB Vault — `GLM-5.2`, `Qwen3-235B-A22B`, `Qwen3-Coder-480B` & `CyberStrike-OffSec-35B` Benchmarks

- **Timestamp (UTC)**: `2026-10-18T01:20:00Z`
- **Agent**: `Agent-29` (`AI DEV 10.0.70.83 4.8 TB Vault — GLM-5.2 & Qwen3-235B/480B Parallel Downloader & Hybrid Benchmarker`)
- **Primary Vault Node**: `10.0.70.83` (`10.0.75.11`, `4.8 TB` free on `/`, `386 GiB` Locked DDR4 RAM, `64 vCPUs`, `1x Tesla T4 16GB`)
- **4x Tesla T4 RPC Cluster (`64 GB` VRAM + `10 GbE` Dedicated Mesh `10.0.75.0/24`)**:
  - `10.0.70.83` (`10.0.75.11:50052`) — `Tesla T4` (`15095 MiB`)
  - `10.0.70.72` (`10.0.75.12:50052`) — `Tesla T4` (`14912 MiB`)
  - `10.0.70.228` (`10.0.75.13:50052`) — `Tesla T4` (`14928 MiB`)
  - `10.0.70.125` (`10.0.75.14:50052`) — `Tesla T4` (`14928 MiB`)

---

## 1. Vault Directory Provisioning & Storage Verification on `10.0.70.83`

```text
$ ssh mboyle@10.0.70.83 'df -h /opt/models && ls -ld /opt/models/glm-5.2 /opt/models/tier3a-qwen3-235b-a22b /opt/models/tier3a-qwen3-coder-480b-a35b'
Filesystem                         Size  Used Avail Use% Mounted on
/dev/mapper/ubuntu--vg-ubuntu--lv  5.0T  763G  4.3T  15% /
drwxrwxr-x 4 mboyle mboyle 187 Oct 17 23:43 /opt/models/glm-5.2
drwxrwxr-x 2 mboyle mboyle 195 Oct 17 23:54 /opt/models/tier3a-qwen3-235b-a22b
drwxrwxr-x 2 mboyle mboyle 330 Oct 18 00:02 /opt/models/tier3a-qwen3-coder-480b-a35b
```

---

## 2. Completed Model Downloads & Exact Byte Verification (`stat -c`)

### A. `unsloth/Qwen3-235B-A22B-Instruct-2507-GGUF` (`Q4_K_M`, 3 Shards = `142,154,074,880` Bytes / `132.39 GiB`) on `10.0.70.83`

```text
$ ssh mboyle@10.0.70.83 'stat -c "EXACT_BYTES: %n -> %s bytes" /opt/models/tier3a-qwen3-235b-a22b/Qwen3-235B-A22B-Instruct-2507-Q4_K_M-0000*-of-00003.gguf'
EXACT_BYTES: /opt/models/tier3a-qwen3-235b-a22b/Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00001-of-00003.gguf -> 49944699040 bytes
EXACT_BYTES: /opt/models/tier3a-qwen3-235b-a22b/Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00002-of-00003.gguf -> 49930349824 bytes
EXACT_BYTES: /opt/models/tier3a-qwen3-235b-a22b/Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00003-of-00003.gguf -> 42279026016 bytes
```
- **Sum of 3 Shards**: `49,944,699,040 + 49,930,349,824 + 42,279,026,016 = 142,154,074,880` bytes (`132.39 GiB` / `142.15 GB`), matching the upstream HuggingFace manifest to the exact byte (`rc=0`, no `.aria2` control files remaining).

### B. `huihui-ai/Huihui-GLM-5.2-abliterated-GGUF` (`DS4/GLM-5.2-UD-IQ2_XXS_RoutedIQ2XXS_blk78Q2K.gguf`, `211,075,856,448` Bytes / `196.58 GiB`) on `10.0.70.83`

```text
$ ssh mboyle@10.0.70.83 'stat -c "EXACT_BYTES: %n -> %s bytes" /opt/models/glm-5.2/GLM-5.2-UD-IQ2_XXS_RoutedIQ2XXS_blk78Q2K.gguf'
EXACT_BYTES: /opt/models/glm-5.2/GLM-5.2-UD-IQ2_XXS_RoutedIQ2XXS_blk78Q2K.gguf -> 211075856448 bytes

10/18 00:34:52 [NOTICE] Download complete: /opt/models/glm-5.2/GLM-5.2-UD-IQ2_XXS_RoutedIQ2XXS_blk78Q2K.gguf
Download Results:
gid   |stat|avg speed  |path/URI
======+====+===========+=======================================================
d83e41|OK  |    55MiB/s|/opt/models/glm-5.2/GLM-5.2-UD-IQ2_XXS_RoutedIQ2XXS_blk78Q2K.gguf
[2026-10-18T00:34:52+00:00] COMPLETED: /opt/models/glm-5.2/GLM-5.2-UD-IQ2_XXS_RoutedIQ2XXS_blk78Q2K.gguf (211075856448 bytes) rc=0
```

### C. `CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf` (`21,713,462,464` Bytes / `20.21 GiB`) on `10.0.70.72`

```text
$ ssh mboyle@10.0.70.72 'stat -c "EXACT_BYTES: %n -> %s bytes" /opt/models/cyberstrike-35b/Huihui-CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf'
EXACT_BYTES: /opt/models/cyberstrike-35b/Huihui-CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf -> 21713462464 bytes
```

### D. Persistent Background Downloader Queue (`/opt/models/download_vault83_tier3.sh`) on `10.0.70.83`
- Active persistent script `/opt/models/download_vault83_tier3.sh` queues and pulls:
  1. `huihui-ai/Huihui-GLM-5.2-abliterated-GGUF` (`UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00001-of-00009.gguf` .. `00009-of-00009.gguf`, `319.21 GiB`) into `/opt/models/glm-5.2/UD-Q3_K_M/`
  2. `drowzeys/keys-latest-GLM-5.2-Quantrio-INT4-INT8-Mixed-Abliterated-DFlash` (`384.63 GiB`, 124 safetensors shards + 4 MTP shards + `dflash/` speculative drafter) into `/opt/models/glm-5.2/drowzeys-quantrio-dflash/`
  3. `unsloth/Qwen3-Coder-480B-A35B-Instruct-GGUF` (`Q4_K_M`, 6 shards = `290,058,826,208` bytes) into `/opt/models/tier3a-qwen3-coder-480b-a35b/`

---

## 3. `CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf` (`35.51B` / `20.21 GiB`) Pure-VRAM Benchmarks on `10.0.70.72`

### A. 2x Tesla T4 (`32 GB` Pure VRAM: `10.0.75.12` Local + `10.0.75.11:50052` RPC)

```text
$ LD_LIBRARY_PATH=/opt/llama-rpc /opt/llama-rpc/llama-bench \
  -m /opt/models/cyberstrike-35b/Huihui-CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf \
  --rpc 10.0.75.11:50052 -ngl 999 -fa 1 -p 512 -n 128 -r 1
ggml_cuda_init: found 1 CUDA devices (Total VRAM: 14912 MiB):
  Device 0: Tesla T4, compute capability 7.5, VMM: yes, VRAM: 14912 MiB
load_backend: loaded CUDA backend from /opt/llama-rpc/libggml-cuda.so
load_backend: loaded RPC backend from /opt/llama-rpc/libggml-rpc.so
load_backend: loaded CPU backend from /opt/llama-rpc/libggml-cpu-haswell.so
| model                          |       size |     params | backend    | ngl |  fa |            test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | --: | --------------: | -------------------: |
| qwen35moe 35B.A3B Q4_K - Medium |  20.21 GiB |    35.51 B | CUDA,RPC   | 999 |   1 |           pp512 |        779.56 ± 0.00 |
| qwen35moe 35B.A3B Q4_K - Medium |  20.21 GiB |    35.51 B | CUDA,RPC   | 999 |   1 |           tg128 |         66.52 ± 0.00 |

build: 797de63b2 (11716)
RC=0
```

### B. 4x Tesla T4 (`64 GB` Pure VRAM: `10.0.75.12` Local + `10.0.75.11:50052,10.0.75.13:50052,10.0.75.14:50052` RPC)

```text
$ LD_LIBRARY_PATH=/opt/llama-rpc /opt/llama-rpc/llama-bench \
  -m /opt/models/cyberstrike-35b/Huihui-CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf \
  --rpc 10.0.75.11:50052,10.0.75.13:50052,10.0.75.14:50052 -ngl 999 -fa 1 -p 512 -n 128 -r 1
ggml_cuda_init: found 1 CUDA devices (Total VRAM: 14912 MiB):
  Device 0: Tesla T4, compute capability 7.5, VMM: yes, VRAM: 14912 MiB
load_backend: loaded CUDA backend from /opt/llama-rpc/libggml-cuda.so
load_backend: loaded RPC backend from /opt/llama-rpc/libggml-rpc.so
load_backend: loaded CPU backend from /opt/llama-rpc/libggml-cpu-haswell.so
| model                          |       size |     params | backend    | ngl |  fa |            test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | --: | --------------: | -------------------: |
| qwen35moe 35B.A3B Q4_K - Medium |  20.21 GiB |    35.51 B | CUDA,RPC   | 999 |   1 |           pp512 |        752.05 ± 0.00 |
| qwen35moe 35B.A3B Q4_K - Medium |  20.21 GiB |    35.51 B | CUDA,RPC   | 999 |   1 |           tg128 |         52.17 ± 0.00 |

build: 797de63b2 (11716)
RC=0
```

---

## 4. Tier 3A & Frontier Hybrid MoE Benchmarks on `10.0.70.83` (`4x Tesla T4 64 GB VRAM` + `386 GiB Locked DDR4 RAM` + `AVX-512`)

### A. `Qwen3-235B-A22B-Instruct-2507-Q4_K_M` (`132.39 GiB` / `235.09B` Params)

```text
$ LD_LIBRARY_PATH=/opt/llama-rpc /opt/llama-rpc/llama-bench \
  -m /opt/models/tier3a-qwen3-235b-a22b/Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00001-of-00003.gguf \
  --rpc 10.0.75.12:50052,10.0.75.13:50052,10.0.75.14:50052 \
  -ngl 999 -fa 1 -t 32 -ot "\.ffn_.*_exps\.=CPU" -p 512 -n 128 -r 1
ggml_cuda_init: found 1 CUDA devices (Total VRAM: 15095 MiB):
  Device 0: Tesla T4, compute capability 7.5, VMM: yes, VRAM: 15095 MiB
load_backend: loaded CUDA backend from /opt/llama-rpc/libggml-cuda.so
load_backend: loaded RPC backend from /opt/llama-rpc/libggml-rpc.so
load_backend: loaded CPU backend from /opt/llama-rpc/libggml-cpu-haswell.so
| model                          |       size |     params | backend    | ngl | threads |  fa | ot                    |            test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | ------: | --: | --------------------- | --------------: | -------------------: |
| qwen3moe 235B.A22B Q4_K - Medium | 132.39 GiB |   235.09 B | CUDA,RPC   | 999 |      32 |   1 | \.ffn_.*_exps\.=CPU   |           pp512 |         17.79 ± 0.00 |
| qwen3moe 235B.A22B Q4_K - Medium | 132.39 GiB |   235.09 B | CUDA,RPC   | 999 |      32 |   1 | \.ffn_.*_exps\.=CPU   |           tg128 |          2.28 ± 0.00 |

build: 797de63b2 (11716)
BENCH_RC=0
```

### B. `GLM-5.2-UD-IQ2_XXS_RoutedIQ2XXS_blk78Q2K.gguf` (`196.57 GiB` / `753.86B` Params)

```text
$ stat -c "EXACT_BYTES: %n -> %s bytes" /opt/models/glm-5.2/GLM-5.2-UD-IQ2_XXS_RoutedIQ2XXS_blk78Q2K.gguf
$ LD_LIBRARY_PATH=/opt/llama-rpc /opt/llama-rpc/llama-bench \
  -m /opt/models/glm-5.2/GLM-5.2-UD-IQ2_XXS_RoutedIQ2XXS_blk78Q2K.gguf \
  --rpc 10.0.75.12:50052,10.0.75.13:50052,10.0.75.14:50052 \
  -lzm off -ngl 999 -fa 1 -t 32 -ot "\.ffn_.*_exps\.=CPU" -p 512 -n 128 -r 1
EXACT_BYTES: /opt/models/glm-5.2/GLM-5.2-UD-IQ2_XXS_RoutedIQ2XXS_blk78Q2K.gguf -> 211075856448 bytes
ggml_cuda_init: found 1 CUDA devices (Total VRAM: 15095 MiB):
  Device 0: Tesla T4, compute capability 7.5, VMM: yes, VRAM: 15095 MiB
load_backend: loaded CUDA backend from /opt/llama-rpc/libggml-cuda.so
load_backend: loaded RPC backend from /opt/llama-rpc/libggml-rpc.so
load_backend: loaded CPU backend from /opt/llama-rpc/libggml-cpu-haswell.so
| model                          |       size |     params | backend    | ngl | threads |  fa | ot                    | lazy_mode  |            test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | ------: | --: | --------------------- | ---------- | --------------: | -------------------: |
| glm-dsa 744B.A40B Q8_0         | 196.57 GiB |   753.86 B | CUDA,RPC   | 999 |      32 |   1 | \.ffn_.*_exps\.=CPU   | off        |           pp512 |          8.76 ± 0.00 |
| glm-dsa 744B.A40B Q8_0         | 196.57 GiB |   753.86 B | CUDA,RPC   | 999 |      32 |   1 | \.ffn_.*_exps\.=CPU   | off        |           tg128 |          2.21 ± 0.00 |

build: 797de63b2 (11716)
BENCH_RC=0
```

---

## 5. Active Phase-2 Background Downloader Verification on `10.0.70.83`

```text
$ ps -ef | grep -E "download_vault83_tier3|aria2c\.(bin|elf)" | grep -v grep
mboyle     18540       1  0 Oct17 ?        00:00:00 bash /opt/models/download_vault83_tier3.sh
mboyle     38768   18540  0 00:34 ?        00:00:00 bash /opt/models/download_vault83_tier3.sh
mboyle     38769   18540  0 00:34 ?        00:00:00 bash /opt/models/download_vault83_tier3.sh
mboyle     38775   38768  0 00:34 ?        00:00:02 /usr/bin/aria2c.elf -x 64 -s 64 -k 10M --continue=true --file-allocation=none --auto-save-interval=0 --max-tries=0 --retry-wait=2 --timeout=30 --connect-timeout=15 --summary-interval=10 -d /opt/models/tier3a-qwen3-coder-480b-a35b -o Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00001-of-00006.gguf https://huggingface.co/unsloth/Qwen3-Coder-480B-A35B-Instruct-GGUF/resolve/main/Q4_K_M/Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00001-of-00006.gguf
mboyle     38781   38769  0 00:34 ?        00:00:00 /usr/bin/aria2c.elf -x 64 -s 64 -k 10M --continue=true --file-allocation=none --auto-save-interval=0 --max-tries=0 --retry-wait=2 --timeout=30 --connect-timeout=15 --summary-interval=10 -d /opt/models/glm-5.2/UD-Q3_K_M -o GLM-5.2-UD-Q3_K_M-00002-of-00009.gguf https://huggingface.co/huihui-ai/Huihui-GLM-5.2-abliterated-GGUF/resolve/main/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00002-of-00009.gguf
```

---

## 6. Summary Table of Verified Benchmarks (`rc=0`)

| Model | Node / Topology | File Size / Params | Prompt (`pp512`) | Generation (`tg128`) | Exit Code |
| :--- | :--- | :--- | ---: | ---: | :---: |
| `CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf` | `10.0.70.72` + `10.0.75.11` (**2x Tesla T4 `32 GB` Pure VRAM**) | `20.21 GiB` (`21,713,462,464` B) / `35.51B` | `779.56 ± 0.00 t/s` | `66.52 ± 0.00 t/s` | `RC=0` |
| `CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf` | `10.0.70.72` + `.11,.13,.14` (**4x Tesla T4 `64 GB` Pure VRAM**) | `20.21 GiB` (`21,713,462,464` B) / `35.51B` | `752.05 ± 0.00 t/s` | `52.17 ± 0.00 t/s` | `RC=0` |
| `Qwen3-235B-A22B-Instruct-2507-Q4_K_M` (3 shards) | `10.0.70.83` + `.12,.13,.14` (**4x T4 `64 GB` VRAM + `386 GiB` DDR4 Hybrid MoE**) | `132.39 GiB` (`142,154,074,880` B) / `235.09B` | `17.79 ± 0.00 t/s` | `2.28 ± 0.00 t/s` | `BENCH_RC=0` |
| `GLM-5.2-UD-IQ2_XXS_RoutedIQ2XXS_blk78Q2K.gguf` | `10.0.70.83` + `.12,.13,.14` (**4x T4 `64 GB` VRAM + `386 GiB` DDR4 Hybrid MoE**) | `196.57 GiB` (`211,075,856,448` B) / `753.86B` (`744B.A40B`) | `8.76 ± 0.00 t/s` | `2.21 ± 0.00 t/s` | `BENCH_RC=0` |
