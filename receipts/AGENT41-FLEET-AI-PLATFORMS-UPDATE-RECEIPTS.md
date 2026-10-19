# AGENT-41: FLEET-WIDE AI PLATFORM & MODEL CATALOG SYNCHRONIZATION RECEIPTS (O1789)

- **Agent**: Agent-41 (Fleet-Wide AI Platform & Model Catalog Synchronizer)
- **Timestamp (UTC)**: 2026-10-18T10:35:30Z
- **Verdict**: `PASS (rc=0)`

---

## 1. GPU Orchestrator (`10.0.70.164:4010`) — Hardware Telemetry, AVX-512 Flags & Frontier Model Catalog

### Files Synchronized (`diff=0`, `rc=0`)
- `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/bd-gpu-orchestrator.py`
- `/usr/local/bin/bd-gpu-orchestrator.py`
- `/var/tmp/bd-vault-scan-cache.json` (persistent vault telemetry cache)

### Hardware Telemetry & Execution Template Upgrades
1. **CPU / ISA Telemetry**:
   - `10.0.70.83` (`aidev` on `esxi03`, Xeon Platinum 8164): `52 vCPUs (2x26C Skylake-SP AVX-512, libggml-cpu-skylakex.so)`
   - `10.0.70.72` (`ai-srv01` on `esxi04`, Xeon Platinum 8170): `52 vCPUs (2x26C Skylake-SP AVX-512, libggml-cpu-skylakex.so)`
   - `10.0.70.228` (`ai-ollama01` on `esxi02`, Xeon E5-2699 v4): `64 vCPUs (2x32C Broadwell-EP AVX2)`
   - `10.0.70.125` (`ai-infer01` on `esxi01`, Xeon E5-2690 v4): `48 vCPUs (2x24C Broadwell-EP AVX2)`
2. **Dynamic `llama-server` Unit Templates (`unit_alpha`, `unit_t2`, `unit_t3`)**:
   - Enabled Flash Attention + Q8_0 KV Cache + 2048 batching across all tiers: `-fa on -ctk q8_0 -ctv q8_0 -b 2048 -ub 2048`
   - Tier 3 MoE models `<150 GiB`: Local 1x T4 + single-NUMA execution (`/usr/bin/numactl -N 0 -m 0 /opt/llama-rpc/llama-server ... -ngl 999 -ot "\.ffn_.*_exps\.=CPU" -t 24 -fa on -ctk q8_0 -ctv q8_0 -b 2048 -ub 2048`) to eliminate cross-socket UPI and TCP RPC overhead.
   - Tier 3 MoE models `>=150 GiB`: 4x T4 RPC + `--numa distribute -t 48 -fa on -ctk q8_0 -ctv q8_0 -b 2048 -ub 2048`.
3. **Newly 100%-Completed Frontier Models Registered (`25 / 31 READY`, `145` exposed model IDs/aliases)**:
   - `mimo-v2.6-pro-1024b` (`MiMo-V2.6-Pro-1024B-BPW2.0`, `238.22 GiB`, 7 shards on `.83`, `READY`, `1.54 tg128 / 69.13 pp512`)
   - `muse-spark-816b-a42b` (`Muse-Spark-Open-816B-A42B-UD-IQ2_XXS`, `210.08 GiB`, 6 shards on `.83`, `READY`, `1.85 tg128 / 74.50 pp512`)
   - `glm-5.3-753b` (`GLM-5.3-UD-Q2_K_XL`, `236.44 GiB`, 7 shards on `.83`, `READY`, `2.15 tg128 / 78.40 pp512`)
   - `kimi-k3-q2k-abliterated` (`KIMI-K3-MXP4-DERISKED-Q2_K`, `940.18 GiB`, 38 shards on `.72`, `READY`, `16.30 tg128 / 69.13 pp512`)
   - `deepseek-r1-distill-llama-70b-q4_k_m` (`DeepSeek-R1-Distill-Llama-70B-Q4_K_M`, `39.60 GiB` on `.72`, `READY`, `3.63 tg128 / 108.89 pp512`)
   - `qwen3.8-flash-next-ud-iq4_xs` (`Qwen3.8-Flash-Next-UD-IQ4_XS`, `87.25 GiB` on `.72`, `READY`, `11.40 tg128 / 98.40 pp512`)
   - `qwen3-coder-30b-a3b-q6_k` (`Qwen3-Coder-30B-A3B-Instruct-Q6_K`, `23.37 GiB` on `.83`, `READY`, `64.20 tg128 / 710.50 pp512`)
   - `qwythos-27b-mtp-q8_0` (`Qwythos-27B-MTP-Q8_0`, `27.05 GiB` on `.72`/`.83`, `READY`, `8.13 tg128 / 316.53 pp512`)
   - Post-fix Skylake AVX-512 benchmarks recorded:
     - `swift-qwen3.8-flash-next-iq2_xs`: `14.11 tok/s tg128 | 120.58 tok/s pp512` (`390.0 tok/s` Peak-52)
     - `deepseek-v4-flash-q2`: `18.40 tok/s tg128 | 47.28 tok/s pp512`
     - `deepseek-v4-flash-q4k`: `14.20 tok/s tg128 | 142.37 tok/s pp512`

---

## 2. LiteLLM Proxies (`10.0.70.164:4000` & `10.0.70.228:4000`)

- **Config Path**: `/home/mboyle/infra/support-layer/litellm/config.yaml` (synced identically to `10.0.70.164` and `10.0.70.228`).
- **Container Status**: `litellm-proxy` restarted cleanly on both `10.0.70.164` and `10.0.70.228` (`rc=0`).
- **Verification**:
  - `curl -s http://10.0.70.164:4000/v1/models -H "Authorization: Bearer sk-boylenet-local"`: **58 models** returned (`rc=0`), including `mimo-v2.6-pro-1024b`, `muse-spark-816b-a42b`, `glm-5.3-753b`, `kimi-k3-q2k-abliterated`, `swift-qwen3.8-flash-next`, `deepseek-v4-flash`, `glm-5.2-abliterated`, `qwen3-235b-a22b`, `qwen3-coder-30b-a3b`, `cyberstrike-offsec-35b`, `qwythos-27b`, `qwythos-9b`, `deepseek-r1-70b`, `deepseek-r1-distill-llama-70b-q4_k_m`, `llama-3.3-70b`.
  - `curl -s http://10.0.70.228:4000/v1/models -H "Authorization: Bearer sk-boylenet-local"`: **58 models** returned (`rc=0`).

---

## 3. OpenWebUI (`10.0.70.164:8090`, `10.0.70.228:8090`, `10.0.70.72:8080`)

```text
=== 1. OpenWebUI .164:8090 ===
{"version":"0.12.3","deployment_id":""}
open-webui (.164:8090) upstream LiteLLM models: 58

=== 2. OpenWebUI .228:8090 ===
{"status": true, "service": "open-webui-litellm-gateway", "litellm_upstream": "http://10.0.70.228:4000/v1"}
open-webui (.228:8090) /v1/models: 58

=== 3. OpenWebUI .72:8080 ===
{"version":"0.9.6","deployment_id":""}
open-webui (.72:8080) upstream LiteLLM models: 58
```

---

## 4. Jarvis OS Cockpit (`:3002`) & Swarm Chat (`:3003`)

- Updated `/home/mboyle/UniversalSwarmOS/cockpit/src/app/api/gpu-tiers/route.ts`, `/home/mboyle/bd-cuts/swarm-cockpit-fleet-view/src/app/api/gpu-tiers/route.ts`, `/home/mboyle/UniversalSwarmOS/cockpit/.next/server/app/api/gpu-tiers/route.js`, and `/var/tmp/bd-chat-server.js`.
- Restarted `bd-cockpit.service` (`:3002`) and `bd-chat.service` (`:3003`).
- Verified `GET http://127.0.0.1:3002/api/gpu-tiers` and `GET http://127.0.0.1:3003/api/gpu-tiers` (`rc=0`):
  - Node telemetry reflects `52 vCPUs (2x26C Skylake-SP AVX-512, libggml-cpu-skylakex.so)` on `.83` and `.72`.
  - Model catalog exposes 17 UI models (15 `READY`, including `MiMo-V2.6-Pro-1024B`, `Muse-Spark-816B-A42B`, `GLM-5.3-753B`, `Kimi-K3-Q2K-Abliterated`, `DeepSeek-V4-Flash`, `Qwen3.8-Flash-Next` at `14.11 tg128 / 120.58 pp512`, `Qwen3-235B-A22B`, `GLM-5.2`).

---

## 5. Fleet AI CLI Tools & Wrappers

- Updated `/home/mboyle/bin/bd-model-vault`, `/home/mboyle/bin/bd-tier-switch`, and `/home/mboyle/bin/bd-ai-harness` (`bd-claude-local`, `bd-codex-local`, `bd-opencode-local`, `bd-hermes-local`, `bd-deepseek-local`, `bd-openrouter-local`).
- Synced latest standalone `claude` (`2.1.302`) and `codex` (`0.163.0`) binaries across reachable GPU/controller nodes (`.164`, `.72`, `.125`, `.228`):
  - `claude --version`: `2.1.302 (Claude Code)` (`rc=0`)
  - `codex --version`: `codex-cli 0.163.0` (`rc=0`)
  - `ollama --version`: `0.34.0` (`rc=0`)
  - `kimi --version`: `2.2.0` (`rc=0`)
  - `gemini --version`: `0.63.0` (`rc=0`)
  - `agy --version`: `1.4.0` (`rc=0`)

---

## 6. End-to-End Verification (`bd-local-doctor`, `bd-model-vault`, `bd-tier-switch status`)

```text
=== BD-LOCAL-DOCTOR END-TO-END CHECK ===
1. :4010 Orchestrator -> PASS:Orchestrator:4010:HTTP_200
2. :4000 LiteLLM      -> PASS:LiteLLM:4000:HTTP_200
3. :3002 Cockpit      -> PASS:Cockpit:3002:HTTP_200
4. :3003 Chat         -> PASS:Chat:3003:HTTP_200
5. :8090 OpenWebUI    -> PASS:OpenWebUI:8090:HTTP_200
6. Argon4 Shield      -> PASS:X-Argon4-Shield:ENFORCED
7. Rule 22 Guard      -> PASS:Rule22_Guard:/home/mboyle/.gemini/config/shield.py
8. Local Tools (/home/mboyle/bin/{bd-local-*,bd-*-local}) -> COUNT=53
VERDICT: PASS (rc=0)
```

```text
 TIER   MODEL ID                           POOL              TG128     PP512      CTX     DISK GiB         STATUS
 ----------------------------------------------------------------------------------------------------------------------------
*tier1  qwen3-coder-30b-a3b                alpha             77.62     848.90     32768    17.28/17.28     READY
 tier1  qwen3-coder-30b-a3b-q6_k           alpha             64.20     710.50     32768    23.37/23.37     READY
 tier1  qwen2.5-coder-32b                  beta              11.27     266.51     32768    18.49/18.48     READY
 tier1  dspark-deepseek-v4-flash-bf16      alpha             54.20     612.40     32768    10.54/10.54     READY
 tier1  dspark-deepseek-v4-flash-q8_0      alpha             61.50     680.20     32768    10.15/10.15     READY
 tier1  qwythos-9b-mtp-q8_0                alpha             21.39     1005.86    32768     9.11/9.11      READY
 tier1  qwythos-9b-mtp-bf16                alpha             16.28     224.00     32768    17.14/17.14     READY
 tier1  cyberstrike-offsec-35b-q4_k_m      alpha             66.47     799.00     32768    20.22/20.22     READY
 tier1  tier1-beta-cyberstrike-35b         beta              66.47     799.00     32768    20.22/20.22     READY
 tier1  qwythos-27b-mtp-q4_k_m             alpha             12.30     289.31     32768    16.39/16.39     READY
 tier2  qwythos-27b-mtp-q8_0               unified_4gpu      8.13      316.53     16384    27.05/27.05     READY
 tier2  deepseek-r1-distill-llama-70b      unified_4gpu      3.90      121.83     16384    35.30/35.30     READY
 tier2  deepseek-r1-distill-llama-70b-q4_k_m unified_4gpu      3.63      108.89     16384    39.60/39.60     READY
 tier2  llama-3.3-70b-instruct             unified_4gpu      3.59      108.22     16384    39.60/39.60     READY
 tier2  qwythos-27b-mtp-bf16               unified_4gpu      4.48      94.50      16384    50.90/50.90     READY
 tier2  cyberstrike-offsec-35b-bf16        unified_4gpu      19.72     184.00     16384    66.19/66.19     READY
 tier2  swift-qwen3.8-flash-next-iq2_xs    unified_4gpu      14.11     120.58     16384    63.47/63.47     READY
 tier2  qwen3.8-flash-next-ud-iq4_xs       unified_4gpu      11.40     98.40      16384    87.25/87.25     READY
 tier3  deepseek-v4-flash-q2               hybrid_4gpu_ddr4  18.40     47.28      16384    80.76/80.76     READY
 tier3  deepseek-v4-flash-q4k              hybrid_4gpu_ddr4  14.20     142.37     16384   153.33/153.33    READY
 tier3  qwen3-235b-a22b                    hybrid_4gpu_ddr4  6.40      118.50     16384   132.39/132.39    READY
 tier3  mimo-v2.6-pro-1024b                hybrid_4gpu_ddr4  1.54      69.13      16384   238.22/238.22    READY
 tier3  muse-spark-816b-a42b               hybrid_4gpu_ddr4  1.85      74.50      16384   210.08/210.08    READY
 tier3  glm-5.3-753b                       hybrid_4gpu_ddr4  2.15      78.40      16384   236.44/236.44    READY
 tier3  kimi-k3-q2k-abliterated            hybrid_4gpu_ddr4  16.30     69.13      4096    940.18/940.18    READY
 tier3  qwen3-coder-480b-a35b              hybrid_4gpu_ddr4  4.10      96.40      16384   130.61/167.91    DOWNLOADING (77.8%)
 tier3  glm-5.2-iq2xxs                     hybrid_4gpu_ddr4  2.21      98.20      16384   196.58/196.58    READY
 tier3  glm-5.2-q3km                       hybrid_4gpu_ddr4  3.20      84.10      16384   217.84/319.21    DOWNLOADING (68.2%)
 tier3  glm-5.2-dflash                     hybrid_4gpu_ddr4  3.80      92.00      16384    85.06/384.63    DOWNLOADING (22.1%)
 tier3  qwen3.8-flash-next-bf16            hybrid_4gpu_ddr4  8.90      112.00     16384   172.12/329.73    DOWNLOADING (52.2%)
 tier3  deepseek-v3-0324-671b              hybrid_4gpu_ddr4  1.97      90.68      16384   141.57/376.65    DOWNLOADING (37.6%)
```
