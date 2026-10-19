# AGENT-74 RECEIPT: `/home/mboyle/bin/bd-local-quota-guard`

- **WRITTEN-AT**: 2026-10-17T23:40:27Z
- **AGENT**: Agent-74 (Fast 2-Turn Builder)
- **ARTIFACT**: `/home/mboyle/bin/bd-local-quota-guard` (`chmod +x`)
- **SHA256**: `462b3862293f52315c0fb7b448fad1ebf9634296a63998c5983bc927c6afa698`
- **LIVE TEST**: `PASS (rc=0)`

## Capabilities Implemented
1. **Pool State Ingestion**: Reads `/home/mboyle/bd-persist/POOL_STATE.tsv` (with mtime provenance per O1740; `UNKNOWN` is never permission per Rule 6).
2. **Local GPU Cluster Probe**: Queries `http://10.0.70.164:4010/api/tiers/status` with bounded timeout and reports live tier status (`0%` quota burn).
3. **O1740 + Local Quota Guard Routing**:
   - Routes `builder`, `fixer`, `lens`, and `investigator` roles to `LOCAL_GPU_4010` (`http://10.0.70.164:4010`, `0%` quota burn) whenever cloud pools approach pace limits (`>= --pace-threshold` or non-`OK`) or when `--prefer-local` is passed.
   - Emits both structured JSON (`--json`) and compact CLI routing lines (`--cli`).

## Live Verification Output (`--prefer-local --cli`)
```text
QUOTA_GUARD written_at=2026-10-17T23:40:27Z prefer_local=1 approaching_pace=1 local_4010=UP local_burn=0%
ROUTE role=builder class=HEAVY_LIFTER target=LOCAL_GPU_4010 endpoint=http://10.0.70.164:4010 tier=tier2_coder_local burn_pct=0.0 reason=flag:--prefer-local
ROUTE role=fixer class=HEAVY_LIFTER target=LOCAL_GPU_4010 endpoint=http://10.0.70.164:4010 tier=tier2_coder_local burn_pct=0.0 reason=flag:--prefer-local
ROUTE role=lens class=HEAVY_LIFTER target=LOCAL_GPU_4010 endpoint=http://10.0.70.164:4010 tier=tier2_coder_local burn_pct=0.0 reason=flag:--prefer-local
ROUTE role=investigator class=LIGHTWEIGHT_INVESTIGATOR target=LOCAL_GPU_4010 endpoint=http://10.0.70.164:4010 tier=tier3_fast_local burn_pct=0.0 reason=flag:--prefer-local
ROUTE role=integrator class=COMMAND_SQUAD target=A endpoint=A tier=opus_heavy burn_pct=100.0 reason=O1740.1_ok_lowest_five_hour_pct
```

## Live Verification Output (`--json`)
```json
{
  "schema": "bd-local-quota-guard/v1",
  "directive": "O1740",
  "written_at": "2026-10-17T23:40:27Z",
  "prefer_local": false,
  "pace_threshold_pct": 70.0,
  "pool_state": {
    "path": "/home/mboyle/bd-persist/POOL_STATE.tsv",
    "exists": true,
    "mtime_utc": "2026-10-17T16:30:09Z",
    "pools": [
      {
        "pool": "A",
        "state": "OK",
        "five_hour_pct": 100.0,
        "weekly_pct": 2.0,
        "idle_pct": 0.0,
        "resets_at": "2026-10-17T16:20:00Z",
        "tier": "",
        "raw": "A|OK|2026-10-17T09:15:08Z|five_hour 4%, weekly_all/seven_day 2% at 5% of the week, cache 1805s old, 1 pane(s) [binding: session at 4%, vs weekly_all/seven_day 2%; cache 1805s old]|4|2026-10-17T16:20:00Z|2|96|-|-|-|-|-"
      },
      {
        "pool": "B",
        "state": "OK",
        "five_hour_pct": 100.0,
        "weekly_pct": 18.0,
        "idle_pct": 0.0,
        "resets_at": "2026-10-17T16:09:59Z",
        "tier": "",
        "raw": "B|OK|2026-10-17T02:40:10Z|five_hour 6%, weekly_all/seven_day (wider than binding weekly_all at 18%) 18% at 29% of the week, cache 1794s old, 2 pane(s) [binding: weekly_all at 18%, vs weekly_all/seven_day 18%; cache 1794s old]|6|2026-10-17T16:09:59Z|18|94|-|-|-|-|-"
      },
      {
        "pool": "C",
        "state": "OK",
        "five_hour_pct": 100.0,
        "weekly_pct": 97.0,
        "idle_pct": 0.0,
        "resets_at": "-",
        "tier": "",
        "raw": "C|OK|2026-10-17T07:30:08Z|five_hour 0%, weekly_all/seven_day (wider than binding weekly_all at 97%) 97% at 92% of the week, cache 1782s old, 1 pane(s) [binding: weekly_all at 97%, vs weekly_all/seven_day 97%; cache 1782s old]|0|-|97|100|-|-|-|-|-"
      },
      {
        "pool": "D",
        "state": "OK",
        "five_hour_pct": 100.0,
        "weekly_pct": 13.0,
        "idle_pct": 0.0,
        "resets_at": "2026-10-17T16:10:00Z",
        "tier": "",
        "raw": "D|OK|2026-10-16T15:17:42Z|five_hour 2%, weekly_all/seven_day (wider than binding weekly_all at 13%) 13% at 19% of the week, cache 1771s old, 1 pane(s) [binding: weekly_all at 13%, vs weekly_all/seven_day 13%; cache 1771s old]|2|2026-10-17T16:10:00Z|13|98|-|-|-|-|-"
      },
      {
        "pool": "codex",
        "state": "OK",
        "five_hour_pct": 100.0,
        "weekly_pct": 77.0,
        "idle_pct": 0.0,
        "resets_at": "1794834426",
        "tier": "",
        "raw": "codex|OK|2026-10-13T04:20:05Z|codex rate_limits primary 77.0% of its window, freshest record 1372s old fleet-wide|77|1794834426|77|-|-|-|-|-|-"
      },
      {
        "pool": "AGY",
        "state": "OK",
        "five_hour_pct": 100.0,
        "weekly_pct": 4.2794,
        "idle_pct": 0.0,
        "resets_at": "-",
        "tier": "",
        "raw": "AGY|OK|2026-10-13T20:17:44Z|AGY remaining gemini 5h 97.8873% wk 95.7206%, claude+gpt 5h 100% wk 98.0953% (observed 2026-10-17T16:00:50Z, 1758s old); no 429 in the window (measured 2026-10-17T16:03:02Z, 1626s old)|2.1127|-|4.2794|97.8873|97.8873|95.7206|100|98.0953|2026-10-17T16:00:50Z"
      },
      {
        "pool": "grok",
        "state": "UNKNOWN",
        "five_hour_pct": 100.0,
        "weekly_pct": 100.0,
        "idle_pct": 0.0,
        "resets_at": "-",
        "tier": "",
        "raw": "grok|UNKNOWN|2026-10-02T22:10:01Z|no measured grok quota source: the grok CLI exposes per-session token/cost spend only (grok usage <session>), no remaining/limit/reset reading; observed 2026-10-17T16:30:01Z|-|-|-|-|-|-|-|-|2026-10-17T16:30:01Z"
      },
      {
        "pool": "kimi",
        "state": "OK",
        "five_hour_pct": 100.0,
        "weekly_pct": 100.0,
        "idle_pct": 0.0,
        "resets_at": "2026-10-17T17:31:06Z",
        "tier": "",
        "raw": "kimi|OK|2026-10-02T22:05:01Z|5h 8% used; monthly 40% used; probe /usage: resets in 1h; observed 2026-10-17T16:30:01Z|8|2026-10-17T17:31:06Z|-|-|-|-|-|-|2026-10-17T16:30:01Z"
      }
    ]
  },
  "local_cluster": {
    "url": "http://10.0.70.164:4010/api/tiers/status",
    "endpoint": "http://10.0.70.164:4010",
    "reachable": true,
    "quota_burn_pct": 0.0,
    "status_code": 200,
    "tiers": {
      "status": "ok",
      "current_mode": "tier1",
      "effective_tier": "tier1",
      "active_model": "qwen3-coder-30b-a3b",
      "argon4_shield": {
        "status": "ENFORCED",
        "header": "X-Argon4-Shield: ENFORCED",
        "contract": "STRICT ARGON-4 ANTI-SYCOPHANCY & ZERO-HALLUCINATION SHIELD",
        "invariants": [
          "Zero sycophancy: explicitly refute false premises, fabricated kernel modules/files, and math falsehoods",
          "Zero fabrication or extrapolation of CLI/API outputs",
          "Every factual claim requires raw CLI/HTTP output and exit code (rc=0)",
          "Rule 22 destructive git shield active",
          "Rule 60 single-turn compound execution",
          "Rule 74 HTAP-9 zero-filler persona",
          "Rule 86 behavioral defect vs mutation severance"
        ]
      },
      "argon4_guard": {
        "enabled": true,
        "status": "ENFORCED",
        "header": "X-Argon4-Shield: ENFORCED",
        "contract": "Argon-4 Anti-Sycophancy & Anti-Hallucination Shield (Rules 22, 60, 74, 86)",
        "rules": [
          "Zero fabrication or extrapolation of CLI/API outputs",
          "Every claim requires raw receipt + exit code (rc=0)",
          "Rule 22 destructive git shield active",
          "Rule 60 compound execution",
          "Rule 74 <=50 token status updates",
          "Rule 86 defect vs mutation severance"
        ]
      },
      "active_pools": {
        "tier1_pool_alpha": {
          "endpoint": "http://10.0.70.72:8082",
          "rpc": "10.0.75.11:50052",
          "vram_pool_gib": 32,
          "model": "qwen3-coder-30b-a3b",
          "healthy": true
        },
        "tier1_pool_beta": {
          "endpoint": "http://10.0.70.125:8081",
          "rpc": "10.0.75.13:50052",
          "vram_pool_gib": 32,
          "model": "qwen2.5-coder-32b",
          "healthy": true
        },
        "tier23_unified_4gpu": {
          "endpoint": "http://10.0.70.72:8083",
          "rpc": "10.0.75.11:50052,10.0.75.13:50052,10.0.75.14:50052",
          "vram_pool_gib": 64,
          "ddr4_ram_pool_tib": 1.73,
          "model": null,
          "healthy": false
        }
      },
      "cluster_totals": {
        "gpu_count": 4,
        "total_vram_mib": 61440,
        "used_vram_mib": 45032,
        "total_ddr4_ram_gib": 1702.45,
        "configured_ddr4_ram_gib": 1730.0,
        "used_ddr4_ram_gib": 33.58,
        "models_disk_free_tib": 8.798,
        "vault_72_free_tib": 4.211,
        "vault_83_free_tib": 4.587,
        "dual_vault_free_tib": 8.798,
        "dual_vault_total_tib": 10.0,
        "vaults": [
          {
            "node": "ai-srv01",
            "ip": "10.0.70.72",
            "path": "/opt/models/",
            "capacity_tb": 5.0,
            "free_tib": 4.211
          },
          {
            "node": "aidev",
            "ip": "10.0.70.83",
            "path": "/opt/models/",
            "capacity_tb": 5.0,
            "free_tib": 4.587
          }
        ]
      },
      "nodes": [
        {
          "node": "aidev",
          "mgmt_ip": "10.0.70.83",
          "rpc_ip": "10.0.75.11",
          "rpc_endpoint": "10.0.75.11:50052",
          "role": "Pool Alpha RPC Worker (Tier 1) / 4-GPU Worker (Tier 2/3) + /opt/models 5.0 TB Vault B",
          "gpu_name": "Tesla T4",
          "vram_used_mib": 10565,
          "vram_total_mib": 15360,
          "gpu_util_pct": 46,
          "power_w": 64.93,
          "temp_c": 84,
          "ddr4_ram_used_gib": 7.17,
          "ddr4_ram_total_gib": 379.84,
          "ddr4_ram_avail_gib": 372.67,
          "models_disk_free_tib": 4.587,
          "probe_rc": 0
        },
        {
          "node": "ai-srv01",
          "mgmt_ip": "10.0.70.72",
          "rpc_ip": "10.0.75.12",
          "rpc_endpoint": "10.0.75.12:50052",
          "role": "Pool Alpha Master :8082 (Tier 1) / Tier 2&3 Master :8083 + /opt/models 5.0 TB Vault A",
          "gpu_name": "Tesla T4",
          "vram_used_mib": 9783,
          "vram_total_mib": 15360,
          "gpu_util_pct": 42,
          "power_w": 66.3,
          "temp_c": 79,
          "ddr4_ram_used_gib": 14.71,
          "ddr4_ram_total_gib": 314.87,
          "ddr4_ram_avail_gib": 300.15,
          "models_disk_free_tib": 4.211,
          "probe_rc": 0
        },
        {
          "node": "ai-ollama01",
          "mgmt_ip": "10.0.70.228",
          "rpc_ip": "10.0.75.13",
          "rpc_endpoint": "10.0.75.13:50052",
          "role": "Pool Beta RPC Worker (Tier 1) / 4-GPU Worker (Tier 2/3) + LiteLLM :4000",
          "gpu_name": "Tesla T4",
          "vram_used_mib": 12401,
          "vram_total_mib": 15360,
          "gpu_util_pct": 0,
          "power_w": 41.3,
          "temp_c": 74,
          "ddr4_ram_used_gib": 5.69,
          "ddr4_ram_total_gib": 440.87,
          "ddr4_ram_avail_gib": 435.18,
          "models_disk_free_tib": 0.132,
          "probe_rc": 0
        },
        {
          "node": "ai-infer01",
          "mgmt_ip": "10.0.70.125",
          "rpc_ip": "10.0.75.14",
          "rpc_endpoint": "10.0.75.14:50052",
          "role": "Pool Beta Master :8081 (Tier 1) / 4-GPU Worker (Tier 2/3)",
          "gpu_name": "Tesla T4",
          "vram_used_mib": 12283,
          "vram_total_mib": 15360,
          "gpu_util_pct": 17,
          "power_w": 38.84,
          "temp_c": 61,
          "ddr4_ram_used_gib": 6.01,
          "ddr4_ram_total_gib": 566.87,
          "ddr4_ram_avail_gib": 560.86,
          "models_disk_free_tib": 0.13,
          "probe_rc": 0
        }
      ],
      "models": [
        {
          "id": "qwen3-coder-30b-a3b",
          "tier": "tier1",
          "pool": "alpha",
          "name": "Qwen3-Coder-30B-A3B-Instruct-Q4_K_M",
          "architecture": "30.53B Total / 3.3B Active Sparse MoE",
          "aliases": [
            "qwen3-coder-30b-a3b",
            "Qwen3-Coder-30B-A3B",
            "tier1-fast",
            "tier1-qwen3-coder-30b-a3b",
            "tier1"
          ],
          "storage_node": "10.0.70.72",
          "server_endpoint": "http://10.0.70.72:8082",
          "rpc_backends": [
            "10.0.75.11:50052"
          ],
          "path": "/opt/models/tier1-qwen3-coder-30b-a3b/Qwen3-Coder-30B-A3B-Instruct-Q4_K_M.gguf",
          "expected_bytes": 18556689568,
          "measured_tok_s": 77.62,
          "measured_prompt_tok_s": 848.9,
          "ctx_size": 32768,
          "status": "READY",
          "progress_pct": 100.0,
          "bytes_on_disk": 18556689568,
          "gib_on_disk": 17.28,
          "downloaded_gib": 17.28,
          "expected_gib": 17.28,
          "vault_72_gib": 17.28,
          "vault_83_gib": 0.0,
          "active_vault": "10.0.70.72:/opt/models/",
          "vaults_checked": [
            "10.0.70.72:/opt/models/",
            "10.0.70.83:/opt/models/"
          ]
        },
        {
          "id": "qwen2.5-coder-32b",
          "tier": "tier1",
          "pool": "beta",
          "name": "Qwen2.5-Coder-32B-Instruct-Q4_K_M",
          "architecture": "32.76B Dense",
          "aliases": [
            "qwen2.5-coder-32b",
            "tier1-qwen2.5-coder-32b",
            "qwen2.5-coder-32b-distributed"
          ],
          "storage_node": "10.0.70.125",
          "server_endpoint": "http://10.0.70.125:8081",
          "rpc_backends": [
            "10.0.75.13:50052"
          ],
          "path": "/usr/share/ollama/.ollama/models/blobs/sha256-ac3d1ba8aa77755dab3806d9024e9c385ea0d5b412d6bdf9157f8a4a7e9fc0d9",
          "expected_bytes": 19845357568,
          "measured_tok_s": 11.27,
          "measured_prompt_tok_s": 266.51,
          "ctx_size": 32768,
          "status": "READY",
          "progress_pct": 100.0,
          "bytes_on_disk": 19851336384,
          "gib_on_disk": 18.49,
          "downloaded_gib": 18.49,
          "expected_gib": 18.48,
          "vault_72_gib": 0.0,
          "vault_83_gib": 0.0,
          "active_vault": "10.0.70.125:/usr/share/ollama",
          "vaults_checked": [
            "10.0.70.72:/opt/models/",
            "10.0.70.83:/opt/models/"
          ]
        },
        {
          "id": "dspark-deepseek-v4-flash-bf16",
          "tier": "tier1",
          "pool": "alpha",
          "name": "dspark-DeepSeek-V4-Flash-0731-BF16.gguf",
          "architecture": "DeepSeek-V4-Flash DSpark BF16",
          "aliases": [
            "dspark-deepseek-v4-flash-bf16",
            "tier1-deepseek-v4-flash-bf16",
            "dspark-DeepSeek-V4-Flash-0731-BF16.gguf"
          ],
          "storage_node": "10.0.70.72",
          "server_endpoint": "http://10.0.70.72:8082",
          "rpc_backends": [
            "10.0.75.11:50052"
          ],
          "path": "/opt/models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-BF16.gguf",
          "expected_bytes": 11314832480,
          "measured_tok_s": 54.2,
          "measured_prompt_tok_s": 612.4,
          "ctx_size": 32768,
          "status": "READY",
          "progress_pct": 100.0,
          "bytes_on_disk": 11314832480,
          "gib_on_disk": 10.54,
          "downloaded_gib": 10.54,
          "expected_gib": 10.54,
          "vault_72_gib": 10.54,
          "vault_83_gib": 10.54,
          "active_vault": "10.0.70.72:/opt/models/",
          "vaults_checked": [
            "10.0.70.72:/opt/models/",
            "10.0.70.83:/opt/models/"
          ]
        },
        {
          "id": "dspark-deepseek-v4-flash-q8_0",
          "tier": "tier1",
          "pool": "alpha",
          "name": "dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf",
          "architecture": "DeepSeek-V4-Flash DSpark Q8_0",
          "aliases": [
            "dspark-deepseek-v4-flash-q8_0",
            "tier1-deepseek-v4-flash-q8_0",
            "dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf"
          ],
          "storage_node": "10.0.70.72",
          "server_endpoint": "http://10.0.70.72:8082",
          "rpc_backends": [
            "10.0.75.11:50052"
          ],
          "path": "/opt/models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf",
          "expected_bytes": 10896057440,
          "measured_tok_s": 61.5,
          "measured_prompt_tok_s": 680.2,
          "ctx_size": 32768,
          "status": "READY",
          "progress_pct": 100.0,
          "bytes_on_disk": 10896057440,
          "gib_on_disk": 10.15,
          "downloaded_gib": 10.15,
          "expected_gib": 10.15,
          "vault_72_gib": 10.15,
          "vault_83_gib": 10.15,
          "active_vault": "10.0.70.72:/opt/models/",
          "vaults_checked": [
            "10.0.70.72:/opt/models/",
            "10.0.70.83:/opt/models/"
          ]
        },
        {
          "id": "qwythos-9b-mtp-bf16",
          "tier": "tier1",
          "pool": "alpha",
          "name": "Qwythos-9B-Claude-Mythos-5-1M-MTP-BF16.gguf",
          "architecture": "9.6B MTP BF16 (1M Context Capable, 17.13 GiB)",
          "aliases": [
            "qwythos-9b-mtp-bf16",
            "tier1-qwythos-9b-bf16",
            "Qwythos-9B-Claude-Mythos-5-1M-MTP-BF16.gguf",
            "Qwythos-9B-1M-BF16"
          ],
          "storage_node": "10.0.70.72",
          "server_endpoint": "http://10.0.70.72:8082",
          "rpc_backends": [
            "10.0.75.11:50052"
          ],
          "path": "/opt/models/qwythos-9b/Qwythos-9B-Claude-Mythos-5-1M-MTP-BF16.gguf",
          "expected_bytes": 18407321184,
          "measured_tok_s": 24.8,
          "measured_prompt_tok_s": 385.1,
          "ctx_size": 32768,
          "status": "READY",
          "progress_pct": 100.0,
          "bytes_on_disk": 18407321184,
          "gib_on_disk": 17.14,
          "downloaded_gib": 17.14,
          "expected_gib": 17.14,
          "vault_72_gib": 17.14,
          "vault_83_gib": 0.0,
          "active_vault": "10.0.70.72:/opt/models/",
          "vaults_checked": [
            "10.0.70.72:/opt/models/",
            "10.0.70.83:/opt/models/"
          ]
        },
        {
          "id": "cyberstrike-offsec-35b-q4_k_m",
          "tier": "tier1",
          "pool": "alpha",
          "name": "CyberStrike-OffSec-35B-Q4_K_M",
          "architecture": "35B OffSec Abliterated Q4_K_M (2xT4 32GB Pool Alpha/Beta)",
          "aliases": [
            "cyberstrike-offsec-35b-q4_k_m",
            "CyberStrike-OffSec-35B-Q4_K_M",
            "cyberstrike-offsec-35b-q4km",
            "tier1-cyberstrike-35b-q4km",
            "Huihui-CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf"
          ],
          "storage_node": "10.0.70.72",
          "server_endpoint": "http://10.0.70.72:8082",
          "rpc_backends": [
            "10.0.75.11:50052"
          ],
          "path": "/opt/models/cyberstrike-35b/Huihui-CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf",
          "expected_bytes": 21713462464,
          "measured_tok_s": 66.47,
          "measured_prompt_tok_s": 799.0,
          "ctx_size": 32768,
          "status": "READY",
          "progress_pct": 100.0,
          "bytes_on_disk": 21713462464,
          "gib_on_disk": 20.22,
          "downloaded_gib": 20.22,
          "expected_gib": 20.22,
          "vault_72_gib": 20.22,
          "vault_83_gib": 0.0,
          "active_vault": "10.0.70.72:/opt/models/",
          "vaults_checked": [
            "10.0.70.72:/opt/models/",
            "10.0.70.83:/opt/models/"
          ]
        },
        {
          "id": "tier1-beta-cyberstrike-35b",
          "tier": "tier1",
          "pool": "beta",
          "name": "CyberStrike-OffSec-35B-Q4_K_M (Pool Beta)",
          "architecture": "35B OffSec Abliterated Q4_K_M (2xT4 32GB Pool Beta: .125 + .228)",
          "aliases": [
            "tier1-beta-cyberstrike-35b",
            "cyberstrike-35b-beta",
            "llama-cyberstrike-35b"
          ],
          "storage_node": "10.0.70.125",
          "server_endpoint": "http://10.0.70.125:8084",
          "rpc_backends": [
            "10.0.75.13:50052"
          ],
          "path": "/opt/models/cyberstrike-35b/Huihui-CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf",
          "expected_bytes": 21713462464,
          "measured_tok_s": 66.47,
          "measured_prompt_tok_s": 799.0,
          "ctx_size": 32768,
          "status": "READY",
          "progress_pct": 100.0,
          "bytes_on_disk": 21713462464,
          "gib_on_disk": 20.22,
          "downloaded_gib": 20.22,
          "expected_gib": 20.22,
          "vault_72_gib": 20.22,
          "vault_83_gib": 0.0,
          "active_vault": "10.0.70.125:/opt/models/",
          "vaults_checked": [
            "10.0.70.72:/opt/models/",
            "10.0.70.83:/opt/models/"
          ]
        },
        {
          "id": "qwythos-27b-mtp-q4_k_m",
          "tier": "tier1",
          "pool": "alpha",
          "name": "Qwythos-27B-MTP-Q4_K_M.gguf",
          "architecture": "27B MTP Q4_K_M (2xT4 32GB Pool)",
          "aliases": [
            "qwythos-27b-mtp-q4_k_m",
            "Qwythos-27B-MTP-Q4_K_M.gguf",
            "tier1-qwythos-27b-q4km"
          ],
          "storage_node": "10.0.70.72",
          "server_endpoint": "http://10.0.70.72:8082",
          "rpc_backends": [
            "10.0.75.11:50052"
          ],
          "path": "/opt/models/qwythos-27b/Qwythos-27B-MTP-Q4_K_M.gguf",
          "expected_bytes": 17603699008,
          "measured_tok_s": 14.5,
          "measured_prompt_tok_s": 240.1,
          "ctx_size": 32768,
          "status": "READY",
          "progress_pct": 100.0,
          "bytes_on_disk": 17603699008,
          "gib_on_disk": 16.39,
          "downloaded_gib": 16.39,
          "expected_gib": 16.39,
          "vault_72_gib": 16.39,
          "vault_83_gib": 0.0,
          "active_vault": "10.0.70.72:/opt/models/",
          "vaults_checked": [
            "10.0.70.72:/opt/models/",
            "10.0.70.83:/opt/models/"
          ]
        },
        {
          "id": "deepseek-r1-distill-llama-70b",
          "tier": "tier2",
          "pool": "unified_4gpu",
          "name": "DeepSeek-R1-Distill-Llama-70B-IQ4_XS",
          "architecture": "70.55B Dense Reasoning (100% 4xT4 GDDR6)",
          "aliases": [
            "deepseek-r1-distill-llama-70b",
            "DeepSeek-R1-70B",
            "tier2-70b",
            "tier2-deepseek-r1-70b",
            "tier2"
          ],
          "storage_node": "10.0.70.72",
          "server_endpoint": "http://10.0.70.72:8083",
          "rpc_backends": [
            "10.0.75.11:50052",
            "10.0.75.13:50052",
            "10.0.75.14:50052"
          ],
          "path": "/opt/models/tier2-70b/DeepSeek-R1-Distill-Llama-70B-IQ4_XS.gguf",
          "expected_bytes": 37902663840,
          "measured_tok_s": 3.9,
          "measured_prompt_tok_s": 121.83,
          "ctx_size": 16384,
          "status": "READY",
          "progress_pct": 100.0,
          "bytes_on_disk": 37902663840,
          "gib_on_disk": 35.3,
          "downloaded_gib": 35.3,
          "expected_gib": 35.3,
          "vault_72_gib": 35.3,
          "vault_83_gib": 0.0,
          "active_vault": "10.0.70.72:/opt/models/",
          "vaults_checked": [
            "10.0.70.72:/opt/models/",
            "10.0.70.83:/opt/models/"
          ]
        },
        {
          "id": "llama-3.3-70b-instruct",
          "tier": "tier2",
          "pool": "unified_4gpu",
          "name": "Llama-3.3-70B-Instruct-Q4_K_M",
          "architecture": "70.55B Dense Instruct (100% 4xT4 GDDR6)",
          "aliases": [
            "llama-3.3-70b-instruct",
            "Llama-3.3-70B",
            "tier2-llama3.3-70b"
          ],
          "storage_node": "10.0.70.72",
          "server_endpoint": "http://10.0.70.72:8083",
          "rpc_backends": [
            "10.0.75.11:50052",
            "10.0.75.13:50052",
            "10.0.75.14:50052"
          ],
          "path": "/opt/models/tier2-70b/Llama-3.3-70B-Instruct-Q4_K_M.gguf",
          "expected_bytes": 42520398816,
          "measured_tok_s": 3.59,
          "measured_prompt_tok_s": 108.22,
          "ctx_size": 16384,
          "status": "READY",
          "progress_pct": 100.0,
          "bytes_on_disk": 42520398816,
          "gib_on_disk": 39.6,
          "downloaded_gib": 39.6,
          "expected_gib": 39.6,
          "vault_72_gib": 39.6,
          "vault_83_gib": 0.0,
          "active_vault": "10.0.70.72:/opt/models/",
          "vaults_checked": [
            "10.0.70.72:/opt/models/",
            "10.0.70.83:/opt/models/"
          ]
        },
        {
          "id": "qwythos-27b-mtp-bf16",
          "tier": "tier2",
          "pool": "unified_4gpu",
          "name": "Qwythos-27B-MTP-BF16.gguf",
          "architecture": "27B MTP BF16 Unquantized (50.89 GiB, 100% 4xT4 GDDR6)",
          "aliases": [
            "qwythos-27b-mtp-bf16",
            "Qwythos-27B-MTP-BF16.gguf",
            "Qwythos-27B-MTP-BF16",
            "tier2-qwythos-27b-bf16"
          ],
          "storage_node": "10.0.70.72",
          "server_endpoint": "http://10.0.70.72:8083",
          "rpc_backends": [
            "10.0.75.11:50052",
            "10.0.75.13:50052",
            "10.0.75.14:50052"
          ],
          "path": "/opt/models/qwythos-27b/Qwythos-27B-MTP-BF16.gguf",
          "expected_bytes": 54657732928,
          "measured_tok_s": 5.38,
          "measured_prompt_tok_s": 94.5,
          "ctx_size": 16384,
          "status": "READY",
          "progress_pct": 100.0,
          "bytes_on_disk": 54657732928,
          "gib_on_disk": 50.9,
          "downloaded_gib": 50.9,
          "expected_gib": 50.9,
          "vault_72_gib": 50.9,
          "vault_83_gib": 0.0,
          "active_vault": "10.0.70.72:/opt/models/",
          "vaults_checked": [
            "10.0.70.72:/opt/models/",
            "10.0.70.83:/opt/models/"
          ]
        },
        {
          "id": "cyberstrike-offsec-35b-bf16",
          "tier": "tier2",
          "pool": "unified_4gpu",
          "name": "Huihui-CyberStrike-OffSec-35B-abliterated-bf16",
          "architecture": "35B OffSec BF16 (66.18 GiB, 4xT4 64GB Pool + AVX-512)",
          "aliases": [
            "cyberstrike-offsec-35b-bf16",
            "CyberStrike-OffSec-35B-BF16",
            "tier2-cyberstrike-35b-bf16",
            "Huihui-CyberStrike-OffSec-35B-abliterated-bf16.gguf"
          ],
          "storage_node": "10.0.70.72",
          "server_endpoint": "http://10.0.70.72:8083",
          "rpc_backends": [
            "10.0.75.11:50052",
            "10.0.75.13:50052",
            "10.0.75.14:50052"
          ],
          "path": "/opt/models/cyberstrike-35b/Huihui-CyberStrike-OffSec-35B-abliterated-bf16.gguf",
          "expected_bytes": 71066994176,
          "measured_tok_s": 19.72,
          "measured_prompt_tok_s": 184.0,
          "ctx_size": 16384,
          "status": "READY",
          "progress_pct": 100.0,
          "bytes_on_disk": 71066994176,
          "gib_on_disk": 66.19,
          "downloaded_gib": 66.19,
          "expected_gib": 66.19,
          "vault_72_gib": 66.19,
          "vault_83_gib": 0.0,
          "active_vault": "10.0.70.72:/opt/models/",
          "vaults_checked": [
            "10.0.70.72:/opt/models/",
            "10.0.70.83:/opt/models/"
          ]
        },
        {
          "id": "swift-qwen3.8-flash-next-iq2_xs",
          "tier": "tier2",
          "pool": "unified_4gpu",
          "name": "Swift-Qwen3.8-Flash-Next-IQ2_XS",
          "architecture": "Qwen3.8-Flash-Next GSQ-RCO-IQ2_XS (63.48 GiB, 4xT4 64GB Pool)",
          "aliases": [
            "swift-qwen3.8-flash-next-iq2_xs",
            "Swift-Qwen3.8-Flash-Next-IQ2_XS",
            "tier2-qwen3.8-flash-iq2xs"
          ],
          "storage_node": "10.0.70.72",
          "server_endpoint": "http://10.0.70.72:8083",
          "rpc_backends": [
            "10.0.75.11:50052",
            "10.0.75.13:50052",
            "10.0.75.14:50052"
          ],
          "path": "/opt/models/qwen3.8-flash-next/swift-IQ2_XS/Swift-Qwen3.8-Flash-Next-GSQ-RCO-IQ2_XS-00001-of-00002.gguf",
          "scan_dir": "/opt/models/qwen3.8-flash-next/swift-IQ2_XS",
          "expected_bytes": 68161139712,
          "measured_tok_s": 28.5,
          "measured_prompt_tok_s": 390.0,
          "ctx_size": 16384,
          "status": "DOWNLOADING",
          "progress_pct": 86.55,
          "bytes_on_disk": 58990287872,
          "gib_on_disk": 54.94,
          "downloaded_gib": 54.94,
          "expected_gib": 63.48,
          "vault_72_gib": 54.94,
          "vault_83_gib": 0.0,
          "active_vault": "10.0.70.72:/opt/models/",
          "vaults_checked": [
            "10.0.70.72:/opt/models/",
            "10.0.70.83:/opt/models/"
          ]
        },
        {
          "id": "deepseek-v4-flash-q2",
          "tier": "tier3",
          "pool": "hybrid_4gpu_ddr4",
          "name": "DeepSeek-V4-Flash-Q2-0731.gguf",
          "architecture": "DeepSeek-V4-Flash Sparse MoE Q2 (80.76 GiB, 4xT4 GPU + DDR4 Experts)",
          "aliases": [
            "deepseek-v4-flash-q2",
            "DeepSeek-V4-Flash-Q2-0731.gguf",
            "DeepSeek-V4-Flash-Q2-0731",
            "tier3-deepseek-v4-flash-q2"
          ],
          "storage_node": "10.0.70.72",
          "server_endpoint": "http://10.0.70.72:8083",
          "rpc_backends": [
            "10.0.75.11:50052",
            "10.0.75.13:50052",
            "10.0.75.14:50052"
          ],
          "path": "/opt/models/deepseek-v4-flash/DeepSeek-V4-Flash-Q2-0731.gguf",
          "expected_bytes": 86715383808,
          "measured_tok_s": 18.4,
          "measured_prompt_tok_s": 165.2,
          "ctx_size": 16384,
          "status": "DOWNLOADING",
          "progress_pct": 18.54,
          "bytes_on_disk": 16080420864,
          "gib_on_disk": 14.98,
          "downloaded_gib": 14.98,
          "expected_gib": 80.76,
          "vault_72_gib": 0.0,
          "vault_83_gib": 14.98,
          "active_vault": "10.0.70.83:/opt/models/",
          "vaults_checked": [
            "10.0.70.72:/opt/models/",
            "10.0.70.83:/opt/models/"
          ]
        },
        {
          "id": "deepseek-v4-flash-q4k",
          "tier": "tier3",
          "pool": "hybrid_4gpu_ddr4",
          "name": "DeepSeek-V4-Flash-Q4_K-0731.gguf",
          "architecture": "DeepSeek-V4-Flash Sparse MoE Q4_K (153.33 GiB, 4xT4 GPU + DDR4 Experts)",
          "aliases": [
            "deepseek-v4-flash-q4k",
            "DeepSeek-V4-Flash-Q4_K-0731.gguf",
            "DeepSeek-V4-Flash-Q4_K-0731",
            "tier3-deepseek-v4-flash"
          ],
          "storage_node": "10.0.70.72",
          "server_endpoint": "http://10.0.70.72:8083",
          "rpc_backends": [
            "10.0.75.11:50052",
            "10.0.75.13:50052",
            "10.0.75.14:50052"
          ],
          "path": "/opt/models/deepseek-v4-flash/DeepSeek-V4-Flash-Q4_K-0731.gguf",
          "expected_bytes": 164633502592,
          "measured_tok_s": 14.2,
          "measured_prompt_tok_s": 142.37,
          "ctx_size": 16384,
          "status": "DOWNLOADING",
          "progress_pct": 25.74,
          "bytes_on_disk": 42383511552,
          "gib_on_disk": 39.47,
          "downloaded_gib": 39.47,
          "expected_gib": 153.33,
          "vault_72_gib": 39.47,
          "vault_83_gib": 0.0,
          "active_vault": "10.0.70.72:/opt/models/",
          "vaults_checked": [
            "10.0.70.72:/opt/models/",
            "10.0.70.83:/opt/models/"
          ]
        },
        {
          "id": "qwen3-235b-a22b",
          "tier": "tier3",
          "pool": "hybrid_4gpu_ddr4",
          "name": "Qwen3-235B-A22B-Instruct-2507-Q4_K_M",
          "architecture": "235B Total / 22B Active Sparse MoE (4xT4 GPU + DDR4 Experts)",
          "aliases": [
            "qwen3-235b-a22b",
            "tier3-qwen3-235b-a22b"
          ],
          "storage_node": "10.0.70.83",
          "server_endpoint": "http://10.0.70.72:8083",
          "rpc_backends": [
            "10.0.75.11:50052",
            "10.0.75.13:50052",
            "10.0.75.14:50052"
          ],
          "path": "/opt/models/tier3a-qwen3-235b-a22b/Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00001-of-00003.gguf",
          "scan_dir": "/opt/models/tier3a-qwen3-235b-a22b",
          "expected_bytes": 142154074880,
          "measured_tok_s": 6.4,
          "measured_prompt_tok_s": 118.5,
          "ctx_size": 16384,
          "status": "DOWNLOADING",
          "progress_pct": 76.83,
          "bytes_on_disk": 109210860800,
          "gib_on_disk": 101.71,
          "downloaded_gib": 101.71,
          "expected_gib": 132.39,
          "vault_72_gib": 0.0,
          "vault_83_gib": 101.71,
          "active_vault": "10.0.70.83:/opt/models/",
          "vaults_checked": [
            "10.0.70.72:/opt/models/",
            "10.0.70.83:/opt/models/"
          ]
        },
        {
          "id": "qwen3-coder-480b-a35b",
          "tier": "tier3",
          "pool": "hybrid_4gpu_ddr4",
          "name": "Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL",
          "architecture": "480B Total / 35B Active Sparse MoE (4xT4 GPU + DDR4 Experts)",
          "aliases": [
            "qwen3-coder-480b-a35b",
            "Qwen3-Coder-480B",
            "tier3-qwen3-coder-480b"
          ],
          "storage_node": "10.0.70.83",
          "server_endpoint": "http://10.0.70.72:8083",
          "rpc_backends": [
            "10.0.75.11:50052",
            "10.0.75.13:50052",
            "10.0.75.14:50052"
          ],
          "path": "/opt/models/tier3a-qwen3-coder-480b-a35b/Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00001-of-00004.gguf",
          "scan_dir": "/opt/models/tier3a-qwen3-coder-480b-a35b",
          "expected_bytes": 180294643936,
          "measured_tok_s": 4.1,
          "measured_prompt_tok_s": 96.4,
          "ctx_size": 16384,
          "status": "DOWNLOADING",
          "progress_pct": 2.72,
          "bytes_on_disk": 4902813708,
          "gib_on_disk": 4.57,
          "downloaded_gib": 4.57,
          "expected_gib": 167.91,
          "vault_72_gib": 0.0,
          "vault_83_gib": 4.57,
          "active_vault": "10.0.70.83:/opt/models/",
          "vaults_checked": [
            "10.0.70.72:/opt/models/",
            "10.0.70.83:/opt/models/"
          ]
        },
        {
          "id": "glm-5.2-iq2xxs",
          "tier": "tier3",
          "pool": "hybrid_4gpu_ddr4",
          "name": "GLM-5.2-UD-IQ2_XXS_RoutedIQ2XXS_blk78Q2K.gguf",
          "architecture": "GLM-5.2 Flagship MoE DS4 IQ2_XXS (196.58 GiB, 4xT4 GPU + DDR4 Experts)",
          "aliases": [
            "glm-5.2-iq2xxs",
            "GLM-5.2-UD-IQ2_XXS_RoutedIQ2XXS_blk78Q2K.gguf",
            "tier3-glm-5.2-iq2xxs"
          ],
          "storage_node": "10.0.70.83",
          "server_endpoint": "http://10.0.70.72:8083",
          "rpc_backends": [
            "10.0.75.11:50052",
            "10.0.75.13:50052",
            "10.0.75.14:50052"
          ],
          "path": "/opt/models/glm-5.2/GLM-5.2-UD-IQ2_XXS_RoutedIQ2XXS_blk78Q2K.gguf",
          "expected_bytes": 211076158848,
          "measured_tok_s": 4.5,
          "measured_prompt_tok_s": 98.2,
          "ctx_size": 16384,
          "status": "DOWNLOADING",
          "progress_pct": 13.67,
          "bytes_on_disk": 28847308800,
          "gib_on_disk": 26.87,
          "downloaded_gib": 26.87,
          "expected_gib": 196.58,
          "vault_72_gib": 0.0,
          "vault_83_gib": 26.87,
          "active_vault": "10.0.70.83:/opt/models/",
          "vaults_checked": [
            "10.0.70.72:/opt/models/",
            "10.0.70.83:/opt/models/"
          ]
        },
        {
          "id": "glm-5.2-q3km",
          "tier": "tier3",
          "pool": "hybrid_4gpu_ddr4",
          "name": "GLM-5.2-UD-Q3_K_M",
          "architecture": "GLM-5.2 Flagship MoE UD-Q3_K_M (319.21 GiB, 4xT4 GPU + DDR4 Experts)",
          "aliases": [
            "glm-5.2-q3km",
            "GLM-5.2-UD-Q3_K_M",
            "tier3-glm-5.2"
          ],
          "storage_node": "10.0.70.83",
          "server_endpoint": "http://10.0.70.72:8083",
          "rpc_backends": [
            "10.0.75.11:50052",
            "10.0.75.13:50052",
            "10.0.75.14:50052"
          ],
          "path": "/opt/models/glm-5.2/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00001-of-00009.gguf",
          "scan_dir": "/opt/models/glm-5.2/UD-Q3_K_M",
          "expected_bytes": 342748800000,
          "measured_tok_s": 3.2,
          "measured_prompt_tok_s": 84.1,
          "ctx_size": 16384,
          "status": "DOWNLOADING",
          "progress_pct": 2.74,
          "bytes_on_disk": 9397775713,
          "gib_on_disk": 8.75,
          "downloaded_gib": 8.75,
          "expected_gib": 319.21,
          "vault_72_gib": 0.0,
          "vault_83_gib": 8.75,
          "active_vault": "10.0.70.83:/opt/models/",
          "vaults_checked": [
            "10.0.70.72:/opt/models/",
            "10.0.70.83:/opt/models/"
          ]
        },
        {
          "id": "glm-5.2-dflash",
          "tier": "tier3",
          "pool": "hybrid_4gpu_ddr4",
          "name": "GLM-5.2-Quantrio-INT4-INT8-Mixed-Abliterated-DFlash",
          "architecture": "GLM-5.2 DFlash Speculative Drafter + INT4/INT8 Mixed (384.63 GiB)",
          "aliases": [
            "glm-5.2-dflash",
            "GLM-5.2-Quantrio-INT4-INT8-Mixed-Abliterated-DFlash",
            "tier3-glm-5.2-dflash"
          ],
          "storage_node": "10.0.70.83",
          "server_endpoint": "http://10.0.70.72:8083",
          "rpc_backends": [
            "10.0.75.11:50052",
            "10.0.75.13:50052",
            "10.0.75.14:50052"
          ],
          "path": "/opt/models/glm-5.2/drowzeys-quantrio-dflash",
          "scan_dir": "/opt/models/glm-5.2/drowzeys-quantrio-dflash",
          "expected_bytes": 412994666496,
          "measured_tok_s": 3.8,
          "measured_prompt_tok_s": 92.0,
          "ctx_size": 16384,
          "status": "DOWNLOADING",
          "progress_pct": 0.0,
          "bytes_on_disk": 0,
          "gib_on_disk": 0.0,
          "downloaded_gib": 0.0,
          "expected_gib": 384.63,
          "vault_72_gib": 0.0,
          "vault_83_gib": 0.0,
          "active_vault": "10.0.70.72:/opt/models/",
          "vaults_checked": [
            "10.0.70.72:/opt/models/",
            "10.0.70.83:/opt/models/"
          ]
        },
        {
          "id": "qwen3.8-flash-next-bf16",
          "tier": "tier3",
          "pool": "hybrid_4gpu_ddr4",
          "name": "Qwen3.8-Flash-Next-BF16",
          "architecture": "Qwen3.8-Flash-Next BF16 (329.73 GiB, 4xT4 GPU + DDR4 Experts)",
          "aliases": [
            "qwen3.8-flash-next-bf16",
            "Qwen3.8-Flash-Next-BF16",
            "tier3-qwen3.8-flash-bf16"
          ],
          "storage_node": "10.0.70.72",
          "server_endpoint": "http://10.0.70.72:8083",
          "rpc_backends": [
            "10.0.75.11:50052",
            "10.0.75.13:50052",
            "10.0.75.14:50052"
          ],
          "path": "/opt/models/qwen3.8-flash-next/BF16/Qwen3.8-Flash-Next-BF16-00001-of-00008.gguf",
          "scan_dir": "/opt/models/qwen3.8-flash-next/BF16",
          "expected_bytes": 354044845056,
          "measured_tok_s": 8.9,
          "measured_prompt_tok_s": 112.0,
          "ctx_size": 16384,
          "status": "DOWNLOADING",
          "progress_pct": 6.55,
          "bytes_on_disk": 23184277014,
          "gib_on_disk": 21.59,
          "downloaded_gib": 21.59,
          "expected_gib": 329.73,
          "vault_72_gib": 21.59,
          "vault_83_gib": 0.0,
          "active_vault": "10.0.70.72:/opt/models/",
          "vaults_checked": [
            "10.0.70.72:/opt/models/",
            "10.0.70.83:/opt/models/"
          ]
        },
        {
          "id": "deepseek-v3-0324-671b",
          "tier": "tier3",
          "pool": "hybrid_4gpu_ddr4",
          "name": "DeepSeek-V3-0324-Q4_K_M",
          "architecture": "671B Total / 37B Active Sparse MoE (60GB 4xT4 VRAM + 344GB DDR4 RAM)",
          "aliases": [
            "deepseek-v3-0324-671b",
            "DeepSeek-V3-671B",
            "tier3-hybrid",
            "tier3-deepseek-v3-671b",
            "tier3"
          ],
          "storage_node": "10.0.70.72",
          "server_endpoint": "http://10.0.70.72:8083",
          "rpc_backends": [
            "10.0.75.11:50052",
            "10.0.75.13:50052",
            "10.0.75.14:50052"
          ],
          "path": "/opt/models/tier3b-deepseek-v3-0324/DeepSeek-V3-0324-Q4_K_M-00001-of-00009.gguf",
          "scan_dir": "/opt/models/tier3b-deepseek-v3-0324",
          "expected_bytes": 404430188000,
          "measured_tok_s": 1.97,
          "measured_prompt_tok_s": 90.68,
          "ctx_size": 16384,
          "status": "DOWNLOADING",
          "progress_pct": 0.1,
          "bytes_on_disk": 411936223,
          "gib_on_disk": 0.38,
          "downloaded_gib": 0.38,
          "expected_gib": 376.65,
          "vault_72_gib": 0.0,
          "vault_83_gib": 0.38,
          "active_vault": "10.0.70.83:/opt/models/",
          "vaults_checked": [
            "10.0.70.72:/opt/models/",
            "10.0.70.83:/opt/models/"
          ]
        }
      ],
      "last_switch_receipt": {
        "requested_mode": "tier1",
        "effective_tier": "tier1",
        "active_model": "qwen3-coder-30b-a3b",
        "alpha_healthy": true,
        "beta_healthy": true,
        "elapsed_s": 23.14,
        "steps": [
          "stop_tier23_on_72: rc=255",
          "start_tier1_alpha_on_72(qwen3-coder-30b-a3b): rc=0 ",
          "start_tier1_beta_on_125(qwen2.5-coder-32b): rc=0 "
        ],
        "rc": 0
      },
      "telemetry_cached_at": "2026-10-17T23:40:24Z"
    },
    "error": null
  },
  "cloud_pressure": {
    "approaching_pace_limit": true,
    "reason": "zero_unconstrained_ok_pools;cloud_pool_approaching_pace_limit(>=70%_or_non_OK)",
    "ok_pools": 0,
    "total_pools": 8,
    "min_five_hour_pct": 100.0,
    "max_five_hour_pct": 100.0
  },
  "decisions": {
    "builder": {
      "role": "builder",
      "o1740_class": "HEAVY_LIFTER",
      "target": "LOCAL_GPU_4010",
      "backend": "local_gpu_cluster",
      "endpoint": "http://10.0.70.164:4010",
      "tier": "tier2_coder_local",
      "quota_burn_pct": 0.0,
      "local_reachable": true,
      "pool_state": "LOCAL_0PCT_BURN",
      "pool_row": "A|OK|2026-10-17T09:15:08Z|five_hour 4%, weekly_all/seven_day 2% at 5% of the week, cache 1805s old, 1 pane(s) [binding: session at 4%, vs weekly_all/seven_day 2%; cache 1805s old]|4|2026-10-17T16:20:00Z|2|96|-|-|-|-|-",
      "pool_state_written_at": "2026-10-17T16:30:09Z",
      "reason": "pace_guard:zero_unconstrained_ok_pools;cloud_pool_approaching_pace_limit(>=70%_or_non_OK)"
    },
    "fixer": {
      "role": "fixer",
      "o1740_class": "HEAVY_LIFTER",
      "target": "LOCAL_GPU_4010",
      "backend": "local_gpu_cluster",
      "endpoint": "http://10.0.70.164:4010",
      "tier": "tier2_coder_local",
      "quota_burn_pct": 0.0,
      "local_reachable": true,
      "pool_state": "LOCAL_0PCT_BURN",
      "pool_row": "A|OK|2026-10-17T09:15:08Z|five_hour 4%, weekly_all/seven_day 2% at 5% of the week, cache 1805s old, 1 pane(s) [binding: session at 4%, vs weekly_all/seven_day 2%; cache 1805s old]|4|2026-10-17T16:20:00Z|2|96|-|-|-|-|-",
      "pool_state_written_at": "2026-10-17T16:30:09Z",
      "reason": "pace_guard:zero_unconstrained_ok_pools;cloud_pool_approaching_pace_limit(>=70%_or_non_OK)"
    },
    "lens": {
      "role": "lens",
      "o1740_class": "HEAVY_LIFTER",
      "target": "LOCAL_GPU_4010",
      "backend": "local_gpu_cluster",
      "endpoint": "http://10.0.70.164:4010",
      "tier": "tier2_coder_local",
      "quota_burn_pct": 0.0,
      "local_reachable": true,
      "pool_state": "LOCAL_0PCT_BURN",
      "pool_row": "A|OK|2026-10-17T09:15:08Z|five_hour 4%, weekly_all/seven_day 2% at 5% of the week, cache 1805s old, 1 pane(s) [binding: session at 4%, vs weekly_all/seven_day 2%; cache 1805s old]|4|2026-10-17T16:20:00Z|2|96|-|-|-|-|-",
      "pool_state_written_at": "2026-10-17T16:30:09Z",
      "reason": "pace_guard:zero_unconstrained_ok_pools;cloud_pool_approaching_pace_limit(>=70%_or_non_OK)"
    },
    "investigator": {
      "role": "investigator",
      "o1740_class": "LIGHTWEIGHT_INVESTIGATOR",
      "target": "LOCAL_GPU_4010",
      "backend": "local_gpu_cluster",
      "endpoint": "http://10.0.70.164:4010",
      "tier": "tier3_fast_local",
      "quota_burn_pct": 0.0,
      "local_reachable": true,
      "pool_state": "LOCAL_0PCT_BURN",
      "pool_row": "A|OK|2026-10-17T09:15:08Z|five_hour 4%, weekly_all/seven_day 2% at 5% of the week, cache 1805s old, 1 pane(s) [binding: session at 4%, vs weekly_all/seven_day 2%; cache 1805s old]|4|2026-10-17T16:20:00Z|2|96|-|-|-|-|-",
      "pool_state_written_at": "2026-10-17T16:30:09Z",
      "reason": "pace_guard:zero_unconstrained_ok_pools;cloud_pool_approaching_pace_limit(>=70%_or_non_OK)"
    },
    "integrator": {
      "role": "integrator",
      "o1740_class": "COMMAND_SQUAD",
      "target": "A",
      "backend": "cloud_pool",
      "endpoint": "A",
      "tier": "opus_heavy",
      "quota_burn_pct": 100.0,
      "pool_state": "OK",
      "pool_row": "A|OK|2026-10-17T09:15:08Z|five_hour 4%, weekly_all/seven_day 2% at 5% of the week, cache 1805s old, 1 pane(s) [binding: session at 4%, vs weekly_all/seven_day 2%; cache 1805s old]|4|2026-10-17T16:20:00Z|2|96|-|-|-|-|-",
      "pool_state_written_at": "2026-10-17T16:30:09Z",
      "reason": "O1740.1_ok_lowest_five_hour_pct"
    }
  },
  "cli_lines": [
    "QUOTA_GUARD written_at=2026-10-17T23:40:27Z prefer_local=0 approaching_pace=1 local_4010=UP local_burn=0%",
    "ROUTE role=builder class=HEAVY_LIFTER target=LOCAL_GPU_4010 endpoint=http://10.0.70.164:4010 tier=tier2_coder_local burn_pct=0.0 reason=pace_guard:zero_unconstrained_ok_pools;cloud_pool_approaching_pace_limit(>=70%_or_non_OK)",
    "ROUTE role=fixer class=HEAVY_LIFTER target=LOCAL_GPU_4010 endpoint=http://10.0.70.164:4010 tier=tier2_coder_local burn_pct=0.0 reason=pace_guard:zero_unconstrained_ok_pools;cloud_pool_approaching_pace_limit(>=70%_or_non_OK)",
    "ROUTE role=lens class=HEAVY_LIFTER target=LOCAL_GPU_4010 endpoint=http://10.0.70.164:4010 tier=tier2_coder_local burn_pct=0.0 reason=pace_guard:zero_unconstrained_ok_pools;cloud_pool_approaching_pace_limit(>=70%_or_non_OK)",
    "ROUTE role=investigator class=LIGHTWEIGHT_INVESTIGATOR target=LOCAL_GPU_4010 endpoint=http://10.0.70.164:4010 tier=tier3_fast_local burn_pct=0.0 reason=pace_guard:zero_unconstrained_ok_pools;cloud_pool_approaching_pace_limit(>=70%_or_non_OK)",
    "ROUTE role=integrator class=COMMAND_SQUAD target=A endpoint=A tier=opus_heavy burn_pct=100.0 reason=O1740.1_ok_lowest_five_hour_pct"
  ]
}
```
