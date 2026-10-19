# AGENT-155: Cluster RAM & VRAM Flush Post-87/87 Benchmark Sweep Receipts

- **Timestamp (UTC)**: `2026-10-19T01:44:15Z`
- **Role**: Agent-155 (Cluster RAM & VRAM Flush Engineer)
- **Trigger**: Completion of 87/87 Model Benchmark Sweep (`#87 deepseek-v4.1-flash-abliterated-755b`, `RC=0` across all 4 GPUs)

---

## 1. Executive Summary: Reclaimed Cluster Memory

| Node | Role | Total RAM | `free` Before | `free` After | `buff/cache` Before | `buff/cache` After | **Free RAM Gained** | **Cache Flushed** |
| :--- | :--- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| `10.0.70.72` | `ai-srv01` (T4 #1) | 314 GiB (322,429 MiB) | 157 GiB (161,320 MiB) | **306 GiB (314,032 MiB)** | 154 GiB (158,212 MiB) | 5.1 GiB (5,195 MiB) | **+149.13 GiB (+152,712 MiB)** | **-149.43 GiB (-153,017 MiB)** |
| `10.0.70.83` | `aidev` (T4 #2) | 379 GiB (388,957 MiB) | 339 GiB (347,540 MiB) | **357 GiB (365,733 MiB)** | 37 GiB (38,003 MiB) | 20 GiB (21,052 MiB) | **+17.77 GiB (+18,193 MiB)** | **-16.55 GiB (-16,951 MiB)** |
| `10.0.70.125` | `ai-infer01` (T4 #3) | 566 GiB (580,478 MiB) | 514 GiB (526,838 MiB) | **544 GiB (557,919 MiB)** | 48 GiB (49,534 MiB) | 10 GiB (10,924 MiB) | **+30.35 GiB (+31,081 MiB)** | **-37.71 GiB (-38,610 MiB)** |
| `10.0.70.228` | `ai-ollama01` (T4 #4) | 440 GiB (451,449 MiB) | 391 GiB (400,630 MiB) | **432 GiB (442,395 MiB)** | 45 GiB (46,939 MiB) | 5.6 GiB (5,743 MiB) | **+40.79 GiB (+41,765 MiB)** | **-40.23 GiB (-41,196 MiB)** |
| `10.0.70.164` | `controller` | 344 GiB (352,670 MiB) | 1.3 GiB (1,345 MiB) | **299 GiB (306,674 MiB)** | 263 GiB (270,162 MiB) | 7.5 GiB (7,633 MiB) | **+298.17 GiB (+305,329 MiB)** | **-256.38 GiB (-262,529 MiB)** |
| `10.0.10.137` | `BattleStation` (WSL2) | 15 GiB (15,949 MiB) | 13 GiB (13,839 MiB) | **14 GiB (15,205 MiB)** | 1.3 GiB (1,330 MiB) | 210 MiB (210 MiB) | **+1.33 GiB (+1,366 MiB)** | **-1.09 GiB (-1,120 MiB)** |
| **TOTAL** | **6 Cluster Nodes** | **2,062.43 GiB** | **1,417.49 GiB** | **1,955.04 GiB** | **550.96 GiB** | **49.57 GiB** | **+537.54 GiB (+550,446 MiB)** | **-501.39 GiB (-513,423 MiB)** |

---

## 2. Benchmark Mounts & Loop Device Cleanup (`10.0.70.72`)

- Removed device-mapper target `/dev/mapper/ds41_compat` (`sudo dmsetup remove ds41_compat 2>/dev/null || true`).
- Detached all loop devices (`sudo losetup -D 2>/dev/null || true`).
- Removed patched header file `/tmp/ds41_compat_header.bin` (`-rw-rw-r-- 1 mboyle mboyle 5832704 Oct 19 01:08 /tmp/ds41_compat_header.bin`, **5.56 MiB** freed).
- Post-cleanup verification:
  ```text
  ls: cannot access '/dev/mapper/ds41_compat': No such file or directory
  ls: cannot access '/tmp/ds41_compat_header.bin': No such file or directory
  losetup -a: (empty)
  ```

---

## 3. RPC Server Restart & VRAM Reclamation (`50052` Mesh)

All 4 `llama-rpc.service` (`rpc-server -p 50052 -c`) instances across the 4 Tesla T4 nodes were cleanly restarted via `sudo systemctl restart llama-rpc.service`, freeing all residual benchmark RPC tensor/context buffers down to the `102 MiB` CUDA driver baseline:

| Node | RPC Endpoint | Old PID -> New PID | `rpc-server` VRAM Before | `rpc-server` VRAM After | **RPC VRAM Reclaimed** | Total GPU VRAM (`Used / Free / Total`) After |
| :--- | :--- | :--- | ---: | ---: | ---: | :--- |
| `10.0.70.72` | `10.0.75.12:50052` | `1000549 -> 1601545` | 102 MiB | 102 MiB | **0 MiB** (already clean) | `105 MiB / 14,808 MiB / 15,360 MiB` |
| `10.0.70.83` | `10.0.75.11:50052` | `815354 -> 1388637` | 134 MiB | 102 MiB | **-32 MiB** | `9,023 MiB / 6,073 MiB / 15,360 MiB` (`:18080` Heretic active) |
| `10.0.70.125` | `10.0.75.14:50052` | `1002821 -> 1509632` | 140 MiB | 102 MiB | **-38 MiB** | `13,465 MiB / 1,463 MiB / 15,360 MiB` (active Ollama embeddings/model) |
| `10.0.70.228` | `10.0.75.13:50052` | `359957 -> 831790` | 190 MiB | 102 MiB | **-88 MiB** | `8,529 MiB / 6,399 MiB / 15,360 MiB` (active Ollama daemon) |
| `10.0.10.137` | BattleStation GPU | N/A | N/A | N/A | **0 MiB** | `7,886 MiB / 3,142 MiB / 11,264 MiB` (RTX 2080 Ti) |
| `10.0.70.164` | Controller | N/A | N/A | N/A | **N/A** | `NO_GPU` |
| **TOTAL** | **4x Tesla T4 RPC** | **All `active`** | **566 MiB** | **408 MiB** | **-158 MiB** | **14,808 MiB free on `.72`; `.83:18080` active** |

---

## 4. Before-Flush vs. After-Flush Raw Telemetry Snapshots

### `10.0.70.72` (`ai-srv01`)
- **BEFORE**:
  ```text
                 total        used        free      shared  buff/cache   available
  Mem:           314Gi       5.3Gi       157Gi        20Mi       154Gi       309Gi
  Swap:          8.0Gi        37Mi       8.0Gi
  Tesla T4, 105 MiB, 14808 MiB, 15360 MiB
  1000549, /opt/llama-rpc/rpc-server, 102 MiB
  ```
- **AFTER**:
  ```text
                 total        used        free      shared  buff/cache   available
  Mem:           314Gi       5.8Gi       306Gi        20Mi       5.1Gi       309Gi
  Swap:          8.0Gi        18Mi       8.0Gi
  Tesla T4, 105 MiB, 14808 MiB, 15360 MiB
  1601545, /opt/llama-rpc/rpc-server, 102 MiB
  ```

### `10.0.70.83` (`aidev`)
- **BEFORE**:
  ```text
                 total        used        free      shared  buff/cache   available
  Mem:           379Gi       5.6Gi       339Gi       178Mi        37Gi       374Gi
  Swap:          8.0Gi       3.0Mi       8.0Gi
  Tesla T4, 3831 MiB, 11265 MiB, 15360 MiB
  815354, /opt/llama-rpc/rpc-server, 134 MiB
  1310681, /opt/llama-rpc/llama-server, 3694 MiB
  ```
- **AFTER**:
  ```text
                 total        used        free      shared  buff/cache   available
  Mem:           379Gi       4.3Gi       357Gi        77Mi        20Gi       375Gi
  Swap:          8.0Gi       3.0Mi       8.0Gi
  Tesla T4, 9023 MiB, 6073 MiB, 15360 MiB
  1388637, /opt/llama-rpc/rpc-server, 102 MiB
  1406327, /opt/llama-rpc/llama-server, 8918 MiB (llama-heretic-18080.service active on :18080)
  ```

### `10.0.70.125` (`ai-infer01`)
- **BEFORE**:
  ```text
                 total        used        free      shared  buff/cache   available
  Mem:           566Gi       7.3Gi       514Gi       259Mi        48Gi       559Gi
  Swap:          8.0Gi          0B       8.0Gi
  Tesla T4, 8409 MiB, 6519 MiB, 15360 MiB
  934581, /opt/llama-rpc/ggml-rpc-server, 102 MiB
  1002821, /opt/llama-rpc/rpc-server, 140 MiB
  ```
- **AFTER**:
  ```text
                 total        used        free      shared  buff/cache   available
  Mem:           566Gi        14Gi       544Gi       179Mi        10Gi       552Gi
  Swap:          8.0Gi          0B       8.0Gi
  Tesla T4, 13465 MiB, 1463 MiB, 15360 MiB
  934581, /opt/llama-rpc/ggml-rpc-server, 102 MiB
  1509632, /opt/llama-rpc/rpc-server, 102 MiB
  1515905, /usr/local/lib/ollama/llama-server, 13258 MiB
  ```

### `10.0.70.228` (`ai-ollama01`)
- **BEFORE**:
  ```text
                 total        used        free      shared  buff/cache   available
  Mem:           440Gi       6.3Gi       391Gi        47Mi        45Gi       434Gi
  Swap:          8.0Gi          0B       8.0Gi
  Tesla T4, 633 MiB, 14295 MiB, 15360 MiB
  359957, /opt/llama-rpc/rpc-server, 190 MiB
  817882, /usr/local/lib/ollama/llama-server, 440 MiB
  ```
- **AFTER**:
  ```text
                 total        used        free      shared  buff/cache   available
  Mem:           440Gi       5.7Gi       432Gi        71Mi       5.6Gi       435Gi
  Swap:          8.0Gi          0B       8.0Gi
  Tesla T4, 8529 MiB, 6399 MiB, 15360 MiB
  831790, /opt/llama-rpc/rpc-server, 102 MiB
  834860, /usr/local/lib/ollama/llama-server, 8424 MiB
  ```

### `10.0.10.137` (`BattleStation`)
- **BEFORE**:
  ```text
                 total        used        free      shared  buff/cache   available
  Mem:            15Gi       1.0Gi        13Gi        11Mi       1.3Gi        14Gi
  Swap:          4.0Gi          0B       4.0Gi
  NVIDIA GeForce RTX 2080 Ti, 7885 MiB, 3143 MiB, 11264 MiB
  ```
- **AFTER**:
  ```text
                 total        used        free      shared  buff/cache   available
  Mem:            15Gi       741Mi        14Gi        11Mi       210Mi        14Gi
  Swap:          4.0Gi          0B       4.0Gi
  NVIDIA GeForce RTX 2080 Ti, 7886 MiB, 3142 MiB, 11264 MiB
  ```

### `10.0.70.164` (`controller`)
- **BEFORE**:
  ```text
                 total        used        free      shared  buff/cache   available
  Mem:           344Gi        82Gi       1.3Gi       155Mi       263Gi       262Gi
  Swap:          8.0Gi       3.8Gi       4.2Gi
  ```
- **AFTER**:
  ```text
                 total        used        free      shared  buff/cache   available
  Mem:           344Gi        40Gi       299Gi       155Mi       7.5Gi       304Gi
  Swap:          8.0Gi       3.8Gi       4.2Gi
  ```
