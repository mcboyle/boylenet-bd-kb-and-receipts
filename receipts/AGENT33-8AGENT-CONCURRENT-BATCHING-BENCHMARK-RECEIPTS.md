# AGENT-33: Tier 1 Dual-Pool Continuous Batching (`--parallel 4 -cb -ctk q8_0 -ctv q8_0`) & `CyberStrike-35B-Q4_K_M` Pool Beta Receipts

- **Timestamp (UTC)**: `2026-10-17T23:46:45Z`
- **Agent**: `Agent-33` (Tier 1 Dual-Pool Continuous Batching & `CyberStrike-35B-Q4_K_M` Pool Beta Optimizer)
- **Contract**: STRICT ARGON-4 ANTI-SYCOPHANCY & ZERO-HALLUCINATION CONTRACT (`rc=0`)

---

## 1. `25GbE` RoCE Fabric Replication of `Huihui-CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf` (`10.0.75.12` -> `10.0.75.14`)

### 1.1 Pre-Copy Disk Space & Source Verification (`10.0.70.72` -> `10.0.70.125`)
```text
# 10.0.70.72 Source File Byte Count
$ stat -c "%s %n" /opt/models/cyberstrike-35b/Huihui-CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf
21713462464 /opt/models/cyberstrike-35b/Huihui-CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf

# 10.0.70.125 Pre-Copy Disk Space
$ ssh 10.0.70.125 'df -h /'
Filesystem                         Size  Used Avail Use% Mounted on
/dev/mapper/ubuntu--vg-ubuntu--lv  297G  144G  154G  49% /
```

### 1.2 `25GbE` RoCE Fabric Transfer (`10.0.75.12` -> `10.0.75.14`) & Post-Copy Exact Byte Count (`rc=0`)
```text
$ rsync -av --info=progress2 -e "ssh -o StrictHostKeyChecking=no -b 10.0.75.12" \
    /opt/models/cyberstrike-35b/Huihui-CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf \
    mboyle@10.0.75.14:/opt/models/cyberstrike-35b/Huihui-CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf
sending incremental file list
Huihui-CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf
 21,713,462,464 100%  297.61MB/s    0:01:09 (xfr#1, to-chk=0/1)

sent 21,718,763,754 bytes  received 35 bytes  308,067,571.48 bytes/sec
total size is 21,713,462,464  speedup is 1.00
ROCE_TRANSFER_ELAPSED_S: 70.38s, THROUGHPUT_MIB_S: 294.21 MiB/s
10.0.70.125 EXACT_BYTES: 21713462464 /opt/models/cyberstrike-35b/Huihui-CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf
Filesystem                         Size  Used Avail Use% Mounted on
/dev/mapper/ubuntu--vg-ubuntu--lv  297G  164G  133G  56% /
```
- **Source & Destination Exact Byte Count**: `21,713,462,464` bytes (`20.22 GiB`)
- **Transfer Throughput over `25GbE` RoCE (`10.0.75.12` -> `10.0.75.14`)**: `308,067,571.48 bytes/sec` (`294.21 MiB/s`, `70.38s`)
- **Post-Copy Free Disk on `10.0.70.125` (`/`)**: `133 GiB` free (`56%` used)

---

## 2. Multi-Agent Continuous Batching (`--parallel 4 -cb -ctk q8_0 -ctv q8_0 -c 32768 --jinja -fa on`) on Tier 1 Pools

### 2.1 Pool Alpha (`10.0.70.72:8082` — `llama-tier1-alpha.service`, 2x Tesla T4 `.72+.83`)
Also persisted into `/usr/local/bin/bd-gpu-orchestrator.py` (`unit_alpha` template) so `:4010` preserves 4-slot continuous batching across mode switches:
```ini
[Unit]
Description=Tier-1 Pool Alpha Qwen3-Coder-30B-A3B-Instruct-Q4_K_M OpenAI Server (2x Tesla T4 32GB Pool: 10.0.75.12 + 10.0.75.11:50052)
After=network-online.target nvidia-persistenced.service llama-rpc.service
Wants=network-online.target

[Service]
Type=simple
User=mboyle
WorkingDirectory=/opt/llama-rpc
Environment="LD_LIBRARY_PATH=/opt/llama-rpc:/usr/local/lib/ollama/cuda_v12"
ExecStart=/opt/llama-rpc/llama-server -m /opt/models/tier1-qwen3-coder-30b-a3b/Qwen3-Coder-30B-A3B-Instruct-Q4_K_M.gguf --alias qwen3-coder-30b-a3b --rpc 10.0.75.11:50052 -ngl 999 --parallel 4 -cb -ctk q8_0 -ctv q8_0 -c 32768 --jinja -fa on -t 32 --host 0.0.0.0 --port 8082
Restart=always
RestartSec=5
TimeoutStopSec=3

[Install]
WantedBy=multi-user.target
```

### 2.2 Pool Beta (`10.0.70.125:8081` — `llama-distributed-32b.service`, 2x Tesla T4 `.125+.228`)
```ini
[Unit]
Description=Distributed Qwen2.5-Coder-32B OpenAI Server (2x Tesla T4 32GB Pool: 10.0.75.14 + 10.0.75.13:50052)
After=network-online.target nvidia-persistenced.service llama-rpc.service
Wants=network-online.target

[Service]
Type=simple
User=mboyle
WorkingDirectory=/opt/llama-rpc
Environment="LD_LIBRARY_PATH=/opt/llama-rpc:/usr/local/lib/ollama/cuda_v12"
ExecStart=/opt/llama-rpc/llama-server -m /usr/share/ollama/.ollama/models/blobs/sha256-ac3d1ba8aa77755dab3806d9024e9c385ea0d5b412d6bdf9157f8a4a7e9fc0d9 --alias qwen2.5-coder-32b-distributed --rpc 10.0.75.13:50052 -ngl 999 --parallel 4 -cb -ctk q8_0 -ctv q8_0 -c 32768 --jinja -fa on -t 32 --host 0.0.0.0 --port 8081
Restart=always
RestartSec=5
TimeoutStopSec=3

[Install]
WantedBy=multi-user.target
```

### 2.3 Pool Beta CyberStrike-35B (`10.0.70.125:8084` — `llama-tier1-beta-cyberstrike.service` & `llama-cyberstrike-35b.service`, 2x Tesla T4 `.125+.228`)
```ini
[Unit]
Description=Tier-1 Pool Beta CyberStrike-OffSec-35B-Q4_K_M OpenAI Server (2x Tesla T4 32GB Pool: 10.0.75.14 + 10.0.75.13:50052)
After=network-online.target nvidia-persistenced.service llama-rpc.service
Wants=network-online.target

[Service]
Type=simple
User=mboyle
WorkingDirectory=/opt/llama-rpc
Environment="LD_LIBRARY_PATH=/opt/llama-rpc:/usr/local/lib/ollama/cuda_v12"
ExecStart=/opt/llama-rpc/llama-server -m /opt/models/cyberstrike-35b/Huihui-CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf --alias cyberstrike-offsec-35b-q4_k_m --rpc 10.0.75.13:50052 -ngl 999 --parallel 4 -cb -ctk q8_0 -ctv q8_0 -c 32768 --jinja -fa on -t 32 --host 0.0.0.0 --port 8084
Restart=always
RestartSec=5
TimeoutStopSec=3

[Install]
WantedBy=multi-user.target
```

### 2.4 Slot & `Q8_0` KV Cache Verification (`n_slots = 4, n_ctx_slot = 8192`)
```text
Oct 17 23:39:38 ai-srv01 llama-server[108572]: 0.51.285.078 I srv    load_model: initializing, n_slots = 4, n_ctx_slot = 8192, kv_unified = 'false'
Oct 17 23:39:38 ai-srv01 llama-server[108572]: 0.51.289.737 I srv  llama_server: listening on http://0.0.0.0:8082
Oct 17 23:39:25 ai-infer01 llama-server[9994]: 0.37.836.694 I srv    load_model: initializing, n_slots = 4, n_ctx_slot = 8192, kv_unified = 'false'
Oct 17 23:39:25 ai-infer01 llama-server[9994]: 0.37.843.492 I srv  llama_server: listening on http://0.0.0.0:8081

Alpha slots (:8082): [(0, False, 8192), (1, False, 8192), (2, False, 8192), (3, False, 8192)]
Beta slots  (:8081): [(0, False, 8192), (1, False, 8192), (2, False, 8192), (3, False, 8192)]
Beta CyberStrike :8084 slots: [(0, False, 8192), (1, False, 8192), (2, False, 8192), (3, False, 8192)]
```

---

## 3. Real 8-Agent Concurrent Continuous Batching Benchmark Results (`rc=0`)

Benchmark script: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/bench_8agent_concurrent.py`

### 3.1 Summary Table Across 8 Simultaneous Parallel Coding Streams

| Configuration | Pool Alpha (4 Streams, `:8082`) | Pool Beta (4 Streams) | Combined Dual-Pool Throughput | End-to-End 8-Agent Wall-Clock | HTTP Errors / Exit Code |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Run A: Default Tier 1 (`:8082` `Qwen3-Coder-30B-A3B` + `:8081` `Qwen2.5-Coder-32B`, Warm Cache)** | **`97.26 tok/s`** wall (`105.03 tok/s` slot decode, `24.43 tok/s`/agent wall, `5.264s` for 512 tok) | **`18.72 tok/s`** wall (`19.72 tok/s` slot decode, `4.69 tok/s`/agent wall, `27.354s` for 512 tok) | **`115.98 tok/s`** (`124.75 tok/s` slot decode sum) | **`37.42 tok/s`** (`27.362s` for `1,024` completion + `376` prompt tokens) | **`0` errors (`rc=0`)** |
| **Run B: Default Tier 1 (`:8082` `Qwen3-Coder-30B-A3B` + `:8081` `Qwen2.5-Coder-32B`, Cold Cache)** | **`42.13 tok/s`** wall (`44.60 tok/s` slot decode, `10.92 tok/s`/agent wall, `12.152s` for 512 tok) | **`19.46 tok/s`** wall (`19.84 tok/s` slot decode, `4.87 tok/s`/agent wall, `26.306s` for 512 tok) | **`61.60 tok/s`** (`64.44 tok/s` slot decode sum) | **`38.92 tok/s`** (`26.313s` for `1,024` completion + `376` prompt tokens) | **`0` errors (`rc=0`)** |
| **Run C: Dual-MoE Tier 1 (`:8082` `Qwen3-Coder-30B-A3B` + `:8084` `CyberStrike-OffSec-35B-Q4_K_M`)** | **`95.29 tok/s`** wall (`105.56 tok/s` slot decode, `23.83 tok/s`/agent wall, `5.373s` for 512 tok) | **`74.02 tok/s`** wall (`104.77 tok/s` slot decode, `18.54 tok/s`/agent wall, `6.917s` for 512 tok) | **`169.31 tok/s`** (`210.33 tok/s` slot decode sum) | **`147.88 tok/s`** (**`6.925s`** for `1,024` completion + `384` prompt tokens) | **`0` errors (`rc=0`)** |

---

### 3.2 Raw JSON Output — Run A: Default Tier 1 (`:8082` `Qwen3-Coder-30B-A3B` + `:8081` `Qwen2.5-Coder-32B`)
Saved at `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/bench_8agent_concurrent_warm_results.json`:
```json
{
  "benchmark": "8-Agent Simultaneous Parallel Continuous Batching (4 Alpha + 4 Beta)",
  "total_agents": 8,
  "http_errors": 0,
  "rc": 0,
  "total_wall_clock_s": 27.362,
  "alpha_pool_4stream": {
    "endpoint": "http://10.0.70.72:8082",
    "model": "Qwen3-Coder-30B-A3B-Instruct-Q4_K_M",
    "wall_clock_s": 5.264,
    "prompt_tokens": 188,
    "completion_tokens": 512,
    "aggregate_tok_s": 97.26,
    "per_agent_avg_wall_tok_s": 24.43,
    "per_agent_avg_slot_tok_s": 26.26
  },
  "beta_pool_4stream": {
    "endpoint": "http://10.0.70.125:8081",
    "model": "Qwen2.5-Coder-32B-Instruct-Q4_K_M",
    "wall_clock_s": 27.354,
    "prompt_tokens": 188,
    "completion_tokens": 512,
    "aggregate_tok_s": 18.72,
    "per_agent_avg_wall_tok_s": 4.69,
    "per_agent_avg_slot_tok_s": 4.93
  },
  "cluster_totals": {
    "total_prompt_tokens": 376,
    "total_completion_tokens": 1024,
    "combined_dual_pool_throughput_tok_s": 115.98,
    "end_to_end_wall_tok_s": 37.42
  },
  "streams": [
    {
      "agent_id": "Agent-A1",
      "pool": "Pool-Alpha (.72+.83)",
      "endpoint": "http://10.0.70.72:8082/v1/chat/completions",
      "model": "qwen3-coder-30b-a3b",
      "http_status": 200,
      "error": null,
      "wall_s": 5.215,
      "prompt_tokens": 47,
      "completion_tokens": 128,
      "slot_predicted_tok_s": 26.21,
      "slot_prompt_tok_s": 23.13,
      "wall_tok_s": 24.54
    },
    {
      "agent_id": "Agent-A2",
      "pool": "Pool-Alpha (.72+.83)",
      "endpoint": "http://10.0.70.72:8082/v1/chat/completions",
      "model": "qwen3-coder-30b-a3b",
      "http_status": 200,
      "error": null,
      "wall_s": 5.264,
      "prompt_tokens": 44,
      "completion_tokens": 128,
      "slot_predicted_tok_s": 26.3,
      "slot_prompt_tok_s": 15.07,
      "wall_tok_s": 24.31
    },
    {
      "agent_id": "Agent-A3",
      "pool": "Pool-Alpha (.72+.83)",
      "endpoint": "http://10.0.70.72:8082/v1/chat/completions",
      "model": "qwen3-coder-30b-a3b",
      "http_status": 200,
      "error": null,
      "wall_s": 5.214,
      "prompt_tokens": 52,
      "completion_tokens": 128,
      "slot_predicted_tok_s": 26.21,
      "slot_prompt_tok_s": 22.69,
      "wall_tok_s": 24.55
    },
    {
      "agent_id": "Agent-A4",
      "pool": "Pool-Alpha (.72+.83)",
      "endpoint": "http://10.0.70.72:8082/v1/chat/completions",
      "model": "qwen3-coder-30b-a3b",
      "http_status": 200,
      "error": null,
      "wall_s": 5.263,
      "prompt_tokens": 45,
      "completion_tokens": 128,
      "slot_predicted_tok_s": 26.31,
      "slot_prompt_tok_s": 14.54,
      "wall_tok_s": 24.32
    },
    {
      "agent_id": "Agent-B1",
      "pool": "Pool-Beta (.125+.228)",
      "endpoint": "http://10.0.70.125:8081/v1/chat/completions",
      "model": "qwen2.5-coder-32b-distributed",
      "http_status": 200,
      "error": null,
      "wall_s": 27.221,
      "prompt_tokens": 44,
      "completion_tokens": 128,
      "slot_predicted_tok_s": 4.88,
      "slot_prompt_tok_s": 49.59,
      "wall_tok_s": 4.7
    },
    {
      "agent_id": "Agent-B2",
      "pool": "Pool-Beta (.125+.228)",
      "endpoint": "http://10.0.70.125:8081/v1/chat/completions",
      "model": "qwen2.5-coder-32b-distributed",
      "http_status": 200,
      "error": null,
      "wall_s": 27.354,
      "prompt_tokens": 44,
      "completion_tokens": 128,
      "slot_predicted_tok_s": 4.98,
      "slot_prompt_tok_s": 68.79,
      "wall_tok_s": 4.68
    },
    {
      "agent_id": "Agent-B3",
      "pool": "Pool-Beta (.125+.228)",
      "endpoint": "http://10.0.70.125:8081/v1/chat/completions",
      "model": "qwen2.5-coder-32b-distributed",
      "http_status": 200,
      "error": null,
      "wall_s": 27.221,
      "prompt_tokens": 52,
      "completion_tokens": 128,
      "slot_predicted_tok_s": 4.88,
      "slot_prompt_tok_s": 58.55,
      "wall_tok_s": 4.7
    },
    {
      "agent_id": "Agent-B4",
      "pool": "Pool-Beta (.125+.228)",
      "endpoint": "http://10.0.70.125:8081/v1/chat/completions",
      "model": "qwen2.5-coder-32b-distributed",
      "http_status": 200,
      "error": null,
      "wall_s": 27.353,
      "prompt_tokens": 48,
      "completion_tokens": 128,
      "slot_predicted_tok_s": 4.98,
      "slot_prompt_tok_s": 74.92,
      "wall_tok_s": 4.68
    }
  ]
}
```

---

### 3.3 Raw JSON Output — Run C: Dual-MoE Tier 1 (`:8082` `Qwen3-Coder-30B-A3B` + `:8084` `CyberStrike-OffSec-35B-Q4_K_M`)
Saved at `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/bench_8agent_cyberstrike_results.json`:
```json
{
  "benchmark": "8-Agent Simultaneous Parallel Continuous Batching (4 Alpha Qwen3-30B-A3B :8082 + 4 Beta CyberStrike-35B-Q4_K_M :8084)",
  "total_agents": 8,
  "http_errors": 0,
  "rc": 0,
  "total_wall_clock_s": 6.925,
  "alpha_pool_4stream_tok_s": 95.29,
  "beta_pool_4stream_cyberstrike_tok_s": 74.02,
  "combined_dual_pool_throughput_tok_s": 169.31,
  "end_to_end_wall_tok_s": 147.88,
  "streams": [
    {
      "agent_id": "Agent-A1",
      "pool": "Pool-Alpha (.72+.83)",
      "endpoint": "http://10.0.70.72:8082/v1/chat/completions",
      "model": "qwen3-coder-30b-a3b",
      "http_status": 200,
      "error": null,
      "wall_s": 5.373,
      "prompt_tokens": 47,
      "completion_tokens": 128,
      "slot_predicted_tok_s": 26.39,
      "slot_prompt_tok_s": 15.72,
      "wall_tok_s": 23.82
    },
    {
      "agent_id": "Agent-A2",
      "pool": "Pool-Alpha (.72+.83)",
      "endpoint": "http://10.0.70.72:8082/v1/chat/completions",
      "model": "qwen3-coder-30b-a3b",
      "http_status": 200,
      "error": null,
      "wall_s": 5.371,
      "prompt_tokens": 44,
      "completion_tokens": 128,
      "slot_predicted_tok_s": 26.39,
      "slot_prompt_tok_s": 15.94,
      "wall_tok_s": 23.83
    },
    {
      "agent_id": "Agent-A3",
      "pool": "Pool-Alpha (.72+.83)",
      "endpoint": "http://10.0.70.72:8082/v1/chat/completions",
      "model": "qwen3-coder-30b-a3b",
      "http_status": 200,
      "error": null,
      "wall_s": 5.372,
      "prompt_tokens": 52,
      "completion_tokens": 128,
      "slot_predicted_tok_s": 26.39,
      "slot_prompt_tok_s": 15.51,
      "wall_tok_s": 23.83
    },
    {
      "agent_id": "Agent-A4",
      "pool": "Pool-Alpha (.72+.83)",
      "endpoint": "http://10.0.70.72:8082/v1/chat/completions",
      "model": "qwen3-coder-30b-a3b",
      "http_status": 200,
      "error": null,
      "wall_s": 5.372,
      "prompt_tokens": 45,
      "completion_tokens": 128,
      "slot_predicted_tok_s": 26.39,
      "slot_prompt_tok_s": 15.29,
      "wall_tok_s": 23.83
    },
    {
      "agent_id": "Agent-B1",
      "pool": "Pool-Beta (.125+.228)",
      "endpoint": "http://10.0.70.125:8084/v1/chat/completions",
      "model": "cyberstrike-offsec-35b-q4_k_m",
      "http_status": 200,
      "error": null,
      "wall_s": 6.916,
      "prompt_tokens": 46,
      "completion_tokens": 128,
      "slot_predicted_tok_s": 26.76,
      "slot_prompt_tok_s": 30.11,
      "wall_tok_s": 18.51
    },
    {
      "agent_id": "Agent-B2",
      "pool": "Pool-Beta (.125+.228)",
      "endpoint": "http://10.0.70.125:8084/v1/chat/completions",
      "model": "cyberstrike-offsec-35b-q4_k_m",
      "http_status": 200,
      "error": null,
      "wall_s": 6.917,
      "prompt_tokens": 46,
      "completion_tokens": 128,
      "slot_predicted_tok_s": 26.77,
      "slot_prompt_tok_s": 30.03,
      "wall_tok_s": 18.51
    },
    {
      "agent_id": "Agent-B3",
      "pool": "Pool-Beta (.125+.228)",
      "endpoint": "http://10.0.70.125:8084/v1/chat/completions",
      "model": "cyberstrike-offsec-35b-q4_k_m",
      "http_status": 200,
      "error": null,
      "wall_s": 6.917,
      "prompt_tokens": 54,
      "completion_tokens": 128,
      "slot_predicted_tok_s": 26.79,
      "slot_prompt_tok_s": 35.16,
      "wall_tok_s": 18.51
    },
    {
      "agent_id": "Agent-B4",
      "pool": "Pool-Beta (.125+.228)",
      "endpoint": "http://10.0.70.125:8084/v1/chat/completions",
      "model": "cyberstrike-offsec-35b-q4_k_m",
      "http_status": 200,
      "error": null,
      "wall_s": 6.866,
      "prompt_tokens": 50,
      "completion_tokens": 128,
      "slot_predicted_tok_s": 24.45,
      "slot_prompt_tok_s": 37.11,
      "wall_tok_s": 18.64
    }
  ]
}
```
