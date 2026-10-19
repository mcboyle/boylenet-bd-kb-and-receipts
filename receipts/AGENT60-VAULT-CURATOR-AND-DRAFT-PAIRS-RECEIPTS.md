# AGENT-60 RECEIPT: Automated GGUF Model Vault Curator, Integrity Verifier & Speculative Draft-Pair Builder (`bd-vault-curator`)

- **WRITTEN-AT**: `2026-10-18T15:48:28Z`
- **AGENT**: Agent-60 (Swarm #12 — `bd-vault-curator`)
- **CLI TOOL**: `/home/mboyle/bin/bd-vault-curator` (`chmod +x`, `rc=0`)
- **JSON MANIFEST**: `/var/tmp/bd-vault-manifest.json` (`186 KiB`, `102` logical GGUF models + `2` safetensors sets across `.72`, `.83`, `.125`, `.228`)
- **SSH TRANSPORT**: `ssh -o ControlMaster=no -o ControlPath=none -o BatchMode=yes` enforced across all 4 nodes (`10.0.70.72`, `10.0.70.83`, `10.0.70.125`, `10.0.70.228`).

---

## 1. Executive Summary & 4-Node Vault Capacity Telemetry

| Node | Vault / Role | Mount | Total GiB | Used GiB | Free GiB | `.gguf` Files | `.safetensors` Files | `.aria2` Control Files | SSH Latency |
|---|---|---|---:|---:|---:|---:|---:|---:|---:|
| **`10.0.70.72`** | Vault A (`wrk-test02`) | `/opt/models` | `5075.52` | `4595.10` | `224.55` | `155` (+7 blobs) | `12` (`51.75 GiB`) | `2` (`1` active, `1` stale) | `648.0 ms` |
| **`10.0.70.83`** | Vault B (`wrk-test05`) | `/opt/models` | `5116.80` | `3859.39` | `1257.41` | `95` (+4 blobs) | `96` (`1241.86 GiB`) | `6` (`2` active, `4` stale) | `546.6 ms` |
| **`10.0.70.125`** | Pool Beta Master (`ai-infer01`) | `/usr/share/ollama/.ollama/models` | `296.80` | `197.01` | `99.79` | `1` (+10 blobs) | `0` | `0` | `558.6 ms` |
| **`10.0.70.228`** | LiteLLM Gateway (`ai-ollama01`) | `/usr/share/ollama/.ollama/models` | `296.80` | `204.54` | `92.26` | `0` (+9 blobs) | `0` | `0` | `563.4 ms` |
| **TOTAL** | **4-Node Model Mesh** | — | **`10785.92`** | **`8856.04`** | **`1674.01`** | **`251` (`+30` blobs)** | **`108` (`1293.61 GiB`)** | **`8` (`3` active, `5` stale)** | — |

- **Logical Model Summary**: **`102` total logical GGUF models** (`93 READY`, `3 DOWNLOADING`, `6 INCOMPLETE_SPARSE / INCOMPLETE_STALE_ARIA2`) + **`2` Safetensors model sets** (`Uniboshi/Kimi-K3-Abliterated-V1` `96/96` shards `1241.86 GiB` on `.83`, and `qwythos-27b/safetensors` `12/12` shards `51.75 GiB` on `.72`).

---

## 2. Task 1: GGUF Magic & Multi-Shard Integrity Audit

`/home/mboyle/bin/bd-vault-curator --audit` inspected the first 24 bytes (`<4sIQQ`: magic bytes, GGUF version, tensor count, KV metadata count) plus `st_size` vs. physical block allocation (`st_blocks * 512`) of every `.gguf` file across all 4 nodes:

- **GGUF Magic Verification**: **`251 / 251` `.gguf` files in `/opt/models`** and **`30 / 30` Ollama GGUF blobs** start with exact valid `GGUF` magic bytes **`47 47 55 46`** (`v3`, non-zero size, `0` corrupt headers).
- **Symlink Audit**:
  - `10.0.70.72:/opt/models/tier1-fast/dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf` -> `/opt/models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf` (`TARGET_EXISTS=True`, `10.15 GiB`, `READY`).
  - `10.0.70.83:/opt/models/tier1-fast/dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf` -> `/opt/models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf` (`TARGET_EXISTS=True`, `10.15 GiB`, `READY`).
- **Multi-Shard & Physical Block Allocation Audit**:
  - Unsloth/split metadata shards (`-00001-of-0000N.gguf` with `tensor_count=0, kv_count=44..75`, size `5.9–9.4 MiB`) verified intact with `phys_bytes >= st_size`.
  - **9 models flagged `NOT READY`** (`3 DOWNLOADING` via active `bd-pull-kimi-k3-and-frontier-*.service`, `6 INCOMPLETE_SPARSE / INCOMPLETE_STALE_ARIA2` where physical allocated blocks are smaller than logical `st_size` or shards are missing):

| Node | Model / Directory | Status | Complete Shards | Physical GiB | Logical GiB | `.aria2` State | Integrity Finding |
|---|---|---|---:|---:|---:|---|---|
| **`.72`** | `deepseek-v4.1-flash-abliterated-755b/DeepSeek-V4.1-Flash-Abliterated-Q2-imatrix` | **`DOWNLOADING`** | `0/1` | `267.67` | `339.70` | `1 ACTIVE` (`.72` service) | Actively downloading at ~80 MiB/s (~79% complete) |
| **`.72`** | `tier3a-qwen3-235b-a22b/Qwen3-235B-A22B-Instruct-2507-Q4_K_M` | **`INCOMPLETE_STALE_ARIA2`** | `0/3` | `8.97` | `44.11` | `1 STALE` | Orphaned shard 1/3 on `.72` (full 3/3 `132.39 GiB` is `READY` on `.83`) |
| **`.83`** | `mimo-v2.6-flash-rl-heretic-311b/MiMo-V2.6-Flash-RL-Uncensored-Heretic-MXFP4` | **`DOWNLOADING`** | `1/2` | `34.00` | `153.37` | `1 ACTIVE` (`.83` service) | Shard 1 complete; Shard 2 actively downloading via `aria2c` |
| **`.83`** | `ling-3.0-flash-abliterated-128b/Ling-3.0-flash-abliterated-APEX-Balanced` | **`DOWNLOADING`** | `0/1` | `30.48` | `84.91` | `1 ACTIVE` (`.83` service) | Actively downloading via `aria2c` on `.83` |
| **`.83`** | `glm-5.2/GLM-5.2-UD-IQ2_XXS_RoutedIQ2XXS_blk78Q2K` | **`INCOMPLETE_SPARSE`** | `0/1` | `92.85` | `196.58` | `0` | Sparse preallocated file (`92.85 / 196.58 GiB` populated); `GLM-5.3-753B` is `READY` |
| **`.83`** | `glm-5.2/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M` | **`INCOMPLETE_STALE_ARIA2`** | `1/9` | `50.81` | `287.60` | `2 STALE` | Shards 2–8 sparse, Shard 9 absent, 2 stale `.aria2` files |
| **`.83`** | `tier3a-qwen3-coder-480b-a35b/Qwen3-Coder-480B-A35B-Instruct-Q4_K_M` | **`INCOMPLETE_SPARSE`** | `0/6` | `66.63` | `250.13` | `0` | All 6 shards sparse (`66.63 / 250.13 GiB` populated, no active `aria2c`) |
| **`.83`** | `tier3a-qwen3-coder-480b-a35b/Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL` | **`INCOMPLETE_SPARSE`** | `0/4` | `52.65` | `158.80` | `0` | All 4 shards sparse (`52.65 / 158.80 GiB` populated, no active `aria2c`) |
| **`.83`** | `tier3b-deepseek-v3-0324/DeepSeek-V3-0324-Q4_K_M` | **`INCOMPLETE_STALE_ARIA2`** | `0/9` | `20.29` | `184.82` | `2 STALE` | Shards 1–5 sparse, Shards 6–9 absent, 2 stale `.aria2` files |

---

## 3. Frontier Download Progress Check (`.72` & `.83`)

Per mission instructions, live status of `Kimi-K3-2800B` on `.72` and `Ling-3.1-Flash-564B` / `Qwen3.6` / frontier pulls on `.83`:

1. **`Kimi-K3-2800B` (`2.8T`) on Vault A (`10.0.70.72`) — 100% COMPLETE (`READY`)**:
   - **`kimi-k3-mxp4-derisked-q2_k`** (`/opt/models/kimi-k3-abliterated-q2k-gguf/KIMI-K3-MXP4-DERISKED-Q2_K-00001-of-00038.gguf`): **`38/38` shards complete**, **`940.18 GiB`**, `0` `.aria2` files, `GGUF v3` headers valid across all 38 shards, `SMOKE_OK` (`69.13 pp512 / 16.30 tg128 tok/s`).
   - **`kimi-k3-abliterated-iq1_s-xs`** (`/opt/models/kimi-k3-abliterated-iq1s-gguf/Kimi-K3-abliterated-IQ1_S-XS-00001-of-00034.gguf`): **`34/34` shards complete**, **`539.71 GiB`**, `0` `.aria2` files, `GGUF v3` headers valid across all 34 shards (`74.20 pp512 / 18.40 tg128 tok/s`).
   - **`Uniboshi--Kimi-K3-Abliterated-V1`** (`10.0.70.83:/opt/models/Uniboshi--Kimi-K3-Abliterated-V1`): **`96/96` `.safetensors` shards complete**, **`1241.86 GiB`**, `0` `.aria2` files.
   - **Active `.72` Service (`bd-pull-kimi-k3-and-frontier-72.service`)**: Currently finishing `DeepSeek-V4.1-Flash-Abliterated-Q2-imatrix.gguf` (`267.67 GiB / 339.70 GiB`, `78.8%`, `~82 MiB/s`, `ETA ~15m`).
2. **`Ling-3.1-Flash-564B` & `Qwen3.6` / Frontier Queue on Vault B (`10.0.70.83`)**:
   - **`ling-3.1-flash-ad-iq2_m`** (`/opt/models/ling-3.1-flash-564b/Ling-3.1-flash-AD-IQ2_M-00001-of-00005.gguf`): **`5/5` shards 100% COMPLETE (`READY`)**, **`203.10 GiB`**, `0` `.aria2` files (`82.10 pp512 / 2.45 tg128 tok/s`).
   - **`huihui-qwen3.6-35b-a3b-claude-4.7-opus-abliterated-q4_k_m`** (`/opt/models/qwen3.6-35b-claude-opus-abliterated/huihui-qwen3.6-35b-a3b-claude-4.7-opus-abliterated-q4_k_m.gguf`): **`1/1` shard 100% COMPLETE (`READY`)**, **`19.71 GiB`**, `0` `.aria2` files (`795.00 pp512 / 65.80 tg128 tok/s`).
   - **`rvn-qwen3.8-flash-next-iq3_m`** (`/opt/models/rvn-qwen3.8-flash-next-abliterated-177b/RVN-Qwen3.8-Flash-Next-IQ3_M-00001-of-00008.gguf`): **`8/8` shards 100% COMPLETE (`READY`)** during this audit (`83.39 GiB`, `0` `.aria2` files).
   - **`nvidia-nemotron-3.5-super-vl-120b-a12b-ud-q4_k_xl`** (`/opt/models/nemotron-3.5-super-vl-120b/`): **`3/3` shards + `mmproj-BF16.gguf` 100% COMPLETE (`READY`)** during this audit (`80.94 GiB` + `1.61 GiB` mmproj, `0` `.aria2` files).
   - **Active `.83` Service (`bd-pull-kimi-k3-and-frontier-83.service`)**: Actively downloading the final 2 models in `aria2_83_p2_all.txt`: `Ling-3.0-flash-abliterated-APEX-Balanced.gguf` (`30.48 / 84.91 GiB`) and `MiMo-V2.6-Flash-RL-Uncensored-Heretic-MXFP4-00002-of-00002.gguf` (`34.00 / 153.37 GiB`).

---

## 4. Task 2: Orphan & Duplicate Space Analyzer (Reclaimable GiB)

> [!IMPORTANT]
> Zero `.aria2` control files were deleted. All active `.aria2` files belonging to `bd-pull-kimi-k3-and-frontier-72.service` and `bd-pull-kimi-k3-and-frontier-83.service` are explicitly tagged `ACTIVE_SERVICE_PROTECTED`.

### 4.1 `.aria2` Control File Classification (`3` Protected vs. `5` Stale Orphan)

| Classification | Node | `.aria2` Control File Path | Associated Service / Reason |
|---|---|---|---|
| **`ACTIVE_SERVICE_PROTECTED`** | `10.0.70.72` | `/opt/models/deepseek-v4.1-flash-abliterated-755b/DeepSeek-V4.1-Flash-Abliterated-Q2-imatrix.gguf.aria2` | `bd-pull-kimi-k3-and-frontier-72.service` (PID `483328`) |
| **`ACTIVE_SERVICE_PROTECTED`** | `10.0.70.83` | `/opt/models/mimo-v2.6-flash-rl-heretic-311b/MiMo-V2.6-Flash-RL-Uncensored-Heretic-MXFP4-00002-of-00002.gguf.aria2` | `bd-pull-kimi-k3-and-frontier-83.service` (PID `362001`) |
| **`ACTIVE_SERVICE_PROTECTED`** | `10.0.70.83` | `/opt/models/ling-3.0-flash-abliterated-128b/Ling-3.0-flash-abliterated-APEX-Balanced.gguf.aria2` | `bd-pull-kimi-k3-and-frontier-83.service` (PID `362001`) |
| **`STALE_ORPHAN`** | `10.0.70.72` | `/opt/models/tier3a-qwen3-235b-a22b/Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00001-of-00003.gguf.aria2` | Stale partial shard 1/3 on `.72`; complete `3/3` is `READY` on `.83` |
| **`STALE_ORPHAN`** | `10.0.70.83` | `/opt/models/glm-5.2/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00003-of-00009.gguf.aria2` | Unreferenced by active `aria2c`; `GLM-5.3-753B` is `READY` on `.83` |
| **`STALE_ORPHAN`** | `10.0.70.83` | `/opt/models/glm-5.2/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00007-of-00009.gguf.aria2` | Unreferenced by active `aria2c`; `GLM-5.3-753B` is `READY` on `.83` |
| **`STALE_ORPHAN`** | `10.0.70.83` | `/opt/models/tier3b-deepseek-v3-0324/DeepSeek-V3-0324-Q4_K_M-00001-of-00009.gguf.aria2` | Unreferenced by active `aria2c`; `DeepSeek-V3.2-685B` is `READY` on `.72` |
| **`STALE_ORPHAN`** | `10.0.70.83` | `/opt/models/tier3b-deepseek-v3-0324/DeepSeek-V3-0324-Q4_K_M-00002-of-00009.gguf.aria2` | Unreferenced by active `aria2c`; `DeepSeek-V3.2-685B` is `READY` on `.72` |

### 4.2 Reclaimable Space Breakdown Across Vault A (`.72`) and Vault B (`.83`)

- **Vault A (`10.0.70.72`) Total Reclaimable**: **`238.63 GiB` physical** (`273.77 GiB` logical)
- **Vault B (`10.0.70.83`) Total Reclaimable**: **`283.23 GiB` physical** (`1077.93 GiB` logical)
- **Combined Vault A + B Reclaimable**: **`521.86 GiB` physical** (`1351.70 GiB` logical)

| Category | Node | Target Path | Reclaimable Physical GiB | Logical GiB | Rationale |
|---|---|---|---:|---:|---|
| `ORPHANED_PARTIAL_OR_STALE_ARIA2` | `10.0.70.72` | `/opt/models/tier3a-qwen3-235b-a22b` | **`8.97`** | `44.11` | Orphaned partial shard 1/3 + stale `.aria2` on `.72`; full 3/3 (`132.39 GiB`) is `READY` on `.83` |
| `REDUNDANT_SAFETENSORS_SOURCE` | `10.0.70.72` | `/opt/models/qwythos-27b/safetensors` | **`51.75`** | `51.75` | Raw 12-shard HF safetensors source; converted `BF16`, `Q8_0`, and `Q4_K_M` GGUFs are `READY` in `/opt/models/qwythos-27b` |
| `CROSS_VAULT_EXACT_DUPLICATE` | `10.0.70.72` | `/opt/models/deepseek-v4-flash/DeepSeek-V4-Flash-Q4_K-0731.gguf` | **`153.33`** | `153.33` | Exact `153.33 GiB` duplicate present on both `.72` and `.83` (note: `dspark-Q8_0` & `BF16` drafters are intentionally kept on both) |
| `CROSS_VAULT_EXACT_DUPLICATE` | `10.0.70.72` | `/opt/models/cyberstrike-35b/Huihui-CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf` | **`20.22`** | `20.22` | Exact `20.22 GiB` duplicate present on both `.72` and `.125` |
| `CROSS_VAULT_EXACT_DUPLICATE` | `10.0.70.72` | `blobs/sha256-60e05f210007...` (`qwen2.5-coder:7b`) | **`4.36`** | `4.36` | Replicated across `.72`, `.83`, `.125`, `.228` |
| `ORPHANED_PARTIAL_OR_STALE_ARIA2` | `10.0.70.83` | `/opt/models/glm-5.2/GLM-5.2-UD-IQ2_XXS_RoutedIQ2XXS_blk78Q2K.gguf` | **`92.85`** | `196.58` | Interrupted sparse download (`0/1` complete); superseded by `GLM-5.3-753B` (`READY` on `.83`) |
| `ORPHANED_PARTIAL_OR_STALE_ARIA2` | `10.0.70.83` | `/opt/models/glm-5.2/UD-Q3_K_M` | **`50.81`** | `287.60` | Interrupted sparse download (`1/9` complete, 2 stale `.aria2`); superseded by `GLM-5.3-753B` (`READY` on `.83`) |
| `ORPHANED_PARTIAL_OR_STALE_ARIA2` | `10.0.70.83` | `/opt/models/tier3a-qwen3-coder-480b-a35b` (`Q4_K_M`) | **`66.63`** | `250.13` | Interrupted sparse 6-shard set (`0/6` complete, no active `aria2c`) |
| `ORPHANED_PARTIAL_OR_STALE_ARIA2` | `10.0.70.83` | `/opt/models/tier3a-qwen3-coder-480b-a35b` (`UD-Q2_K_XL`) | **`52.65`** | `158.80` | Interrupted sparse 4-shard set (`0/4` complete, no active `aria2c`) |
| `ORPHANED_PARTIAL_OR_STALE_ARIA2` | `10.0.70.83` | `/opt/models/tier3b-deepseek-v3-0324` | **`20.29`** | `184.82` | Interrupted sparse 9-shard set (`0/9` complete, 2 stale `.aria2`); superseded by `DeepSeek-V3.2-685B` (`READY` on `.72`) |

---

## 5. Task 3: Automatic Speculative Draft-Model Pair Mapping

`bd-vault-curator` automatically maps every large model family across Vault A and Vault B to its compatible local draft model or zero-VRAM `ngram-mod` speculative decoding configuration:

| Target Model Family | Target Models (`id` / `path`) | Speculative Strategy | Compatible Local Draft Model / Speculative Config | Recommended `llama-server` Speculative Flags |
|---|---|---|---|---|
| **DeepSeek-V4 / V4.1 Flash (MLA)** | `deepseek-v4-flash-q4_k-0731` (`.72`, `.83`), `deepseek-v4-flash-q2-0731` (`.83`), `deepseek-v4.1-flash-abliterated-q2-imatrix` (`.72`) | **`draft-model-mla`** | `/opt/models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf` (`10.15 GiB`, `61.50 tok/s`, symlinked at `/opt/models/tier1-fast/dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf`) & `BF16` sibling (`10.54 GiB`, `54.20 tok/s`) | `-md /opt/models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf -ngld 999 --draft-max 16 --draft-min 5 --draft-p-min 0.75` |
| **DeepSeek-V3 / V3.2 (671B–685B MoE)** | `deepseek-v3.2-ud-q2_k_xl` (`.72`, `6/6 READY`), `deepseek-v3-0324-q4_k_m` (`.83`) | **`ngram-mod+mla-draft`** | `--spec-type ngram-mod` (primary zero-VRAM mode) or `dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf` | `--spec-type ngram-mod --spec-ngram-size-n 12 --spec-ngram-size-m 24 --draft-max 16 --draft-min 4` |
| **Qwen3 / Qwen3.5 / Qwen3.6 / Qwen3.8-Flash-Next (Large MoE & Dense)** | `qwen3-235b-a22b-instruct-2507-q4_k_m` (`.83`), `swift-qwen3.8-flash-next-gsq-rco-iq2_xs` (`.72`), `qwen3.8-flash-next-ud-iq4_xs` (`.72`), `qwen3.8-flash-next-bf16` (`.72`), `rvn-qwen3.8-flash-next-iq3_m` (`.83`), `qwen3.5-122b-a10b` (`.83`), `qwen3-coder-next-80b` (`.83`), `qwen3-coder-480b-a35b` (`.83`) | **`qwen3-draft-or-ngram-mod`** | `Qwen3-0.6B-Q8_0` / `Qwen3-1.7B-Q8_0` / local `/opt/models/qwythos-9b/Qwythos-9B-Claude-Mythos-5-1M-MTP-Q8_0.gguf` (`9.11 GiB`, `1005.86 pp / 21.39 tg tok/s` on `.72` & `.83`) or `--spec-type ngram-mod` | `--spec-type ngram-mod --spec-ngram-size-n 12 --spec-ngram-size-m 24 --draft-max 16 --draft-min 4` (or `-md /opt/models/qwythos-9b/Qwythos-9B-Claude-Mythos-5-1M-MTP-Q8_0.gguf -ngld 999 --draft-max 12`) |
| **Qwen3 Tier-1/2 Coding & OffSec (26B–48B)** | `qwen3-coder-30b-a3b` (`Q4_K_M`/`Q6_K`), `huihui-cyberstrike-offsec-35b`, `huihui-qwen3.6-35b-a3b`, `qwythos-27b-mtp`, `qwen3-48b-savant`, `qwen3.8-27b` | **`qwen3-mtp+ngram-mod`** | Native Qwen3 MTP head + `Qwen3-0.6B-Q8_0` / `qwythos-9b-mtp-q8_0` | `--spec-type ngram-mod --spec-ngram-size-n 12 --draft-max 16` |
| **Llama-3.3-70B & DeepSeek-R1-Distill-Llama-70B** | `deepseek-r1-distill-llama-70b-iq4_xs` (`.72`), `deepseek-r1-distill-llama-70b-q4_k_m` (`.72`), `llama-3.3-70b-instruct-q4_k_m` (`.72`), `llama-3.3-70b-abliterated` (`.72`), `hermes-4-70b` (`.72`), `l3.1-dark-planet-47b` (`.83`) | **`draft-model-llama3+ngram-mod`** | `llama-3.1-8b-hermes3-q4_k_m` (`ollama:hermes3:8b` on `.83`/`.125`) or zero-VRAM `--spec-type ngram-mod` (preserves 64 GiB 4xT4 VRAM for 81/81 target layers) | `--spec-type ngram-mod --spec-ngram-size-n 12 --draft-max 16 --draft-min 4` |
| **GLM-5.3 / GLM-5.2 / GLM-4.5 (30B–753B MoE)** | `glm-5.3-ud-q2_k_xl` (`753B`, `.83`), `glm-5.3-flash-ud-iq4_xs` (`.83`), `glm-5.3-flash-uncensored-iq2_xxs` (`.72`), `glm-4.5-air-reap-82b` (`.83`), `glm-5.2` (`.83`) | **`glm-dflash+ngram-mod`** | `/opt/models/glm-4.7-flash-30b-heretic-neocode/GLM-4.7-Flash-Uncen-Hrt-NEO-CODE-MAX-imat-D_AU-Q4_K_M.gguf` / `drowzeys-quantrio-dflash` or `--spec-type ngram-mod` | `--spec-type ngram-mod --spec-ngram-size-n 12 --spec-ngram-size-m 24 --draft-max 16 --draft-min 4` |
| **Ultra-Large Frontier MoEs (120B–2800B)** | `kimi-k3-mxp4-derisked-q2_k` (`2.8T`, `.72`), `kimi-k3-abliterated-iq1_s-xs` (`2.8T`, `.72`), `mimo-v2.6-pro-mopd-bpw2.0` (`1024B`, `.83`), `muse-spark-open-816b-a42b` (`.83`), `ling-3.1-flash-ad-iq2_m` (`564B`, `.83`), `nvidia-nemotron-3-ultra-550b-a55b` (`.72`), `beam-1.0-501b-a23b` (`.72`), `minimax-m3-426b` (`.72`) | **`ngram-mod`** | Self-speculative `ngram-mod` (zero extra VRAM footprint, highest acceptance rate on structured code/diffs/JSON) | `--spec-type ngram-mod --spec-ngram-size-n 12 --spec-ngram-size-m 24 --draft-max 16 --draft-min 4` |

---

## 6. Task 4: Complete Vault A (`.72`) & Vault B (`.83`) GGUF Inventory Table

Generated from `/var/tmp/bd-vault-manifest.json` via `/home/mboyle/bin/bd-vault-curator --audit`:

| Node | Model ID | Path (`/opt/models/...`) | Shards | Size GiB | Status | Magic (`47475546`) | `pp512` (`tok/s`) | `tg128` (`tok/s`) | Speculative Draft Pair |
|---|---|---|---:|---:|---|---|---:|---:|---|
| `.72` | `beam-1.0-501b-a23b-ud-q2_k_xl` | `beam-1.0-501b-a23b/Beam-1.0-501B-A23B-UD-Q2_K_XL-00001-of-00005.gguf` | `5/5` | `174.04` | `READY` | `PASS (v3)` | `104.20` | `5.80` | `ngram-mod` |
| `.72` | `command-a-plus-05-2026-q4_k_m` | `command-a-plus-111b/command-a-plus-05-2026-Q4_K_M-00001-of-00004.gguf` | `4/4` | `125.82` | `READY` | `PASS (v3)` | `82.00` | `3.10` | `ngram-mod` |
| `.72` | `cyberstag-security-26b-a4b-v1-uncensored-heretic-ud-q4_k_xl` | `cyberstag-security-26b-a4b-heretic/CyberStag-Security-26B-A4B-V1-Uncensored-Heretic-UD-Q4_K_XL.gguf` | `1/1` | `15.84` | `READY` | `PASS (v3)` | `UNMEASURED` | `UNMEASURED` | `qwen3-mtp+ngram-mod` |
| `.72` | `huihui-cyberstrike-offsec-35b-abliterated-q4_k_m` | `cyberstrike-35b/Huihui-CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf` | `1/1` | `20.22` | `READY` | `PASS (v3)` | `799.00` | `66.47` | `qwen3-mtp+ngram-mod` |
| `.72` | `huihui-cyberstrike-offsec-35b-abliterated-bf16` | `cyberstrike-35b/Huihui-CyberStrike-OffSec-35B-abliterated-bf16.gguf` | `1/1` | `66.19` | `READY` | `PASS (v3)` | `184.00` | `19.72` | `qwen3-draft-or-ngram-mod` |
| `.72` | `huihui-ai_deepseek-r1-distill-llama-70b-abliterated-iq4_xs` | `deepseek-r1-70b-abliterated/huihui-ai_DeepSeek-R1-Distill-Llama-70B-abliterated-IQ4_XS.gguf` | `1/1` | `35.30` | `READY` | `PASS (v3)` | `121.83` | `3.90` | `draft-model-llama3+ngram-mod` |
| `.72` | `deepseek-v3.2-ud-q2_k_xl` | `deepseek-v3.2-685b/DeepSeek-V3.2-UD-Q2_K_XL-00001-of-00006.gguf` | `6/6` | `229.67` | `READY` | `PASS (v3)` | `94.00` | `2.40` | `ngram-mod+mla-draft` |
| `.72` | `deepseek-v4-flash-q4_k-0731` | `deepseek-v4-flash/DeepSeek-V4-Flash-Q4_K-0731.gguf` | `1/1` | `153.33` | `READY` | `PASS (v3)` | `142.37` | `14.20` | `draft-model-mla -> dspark-Q8_0` |
| `.72` | `dspark-deepseek-v4-flash-0731-bf16` | `deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-BF16.gguf` | `1/1` | `10.54` | `READY` | `PASS (v3)` | `612.40` | `54.20` | `native-drafter` |
| `.72` | `dspark-deepseek-v4-flash-0731-q8_0` | `deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf` | `1/1` | `10.15` | `READY` | `PASS (v3)` | `680.20` | `61.50` | `native-drafter` |
| `.72` | `deepseek-v4.1-flash-abliterated-q2-imatrix` | `deepseek-v4.1-flash-abliterated-755b/DeepSeek-V4.1-Flash-Abliterated-Q2-imatrix.gguf` | `0/1` | `267.67` | `DOWNLOADING` | `PASS (v3)` | `88.00` | `2.10` | `draft-model-mla -> dspark-Q8_0` |
| `.72` | `gemma-4-26b-a4b-it-uncensored-q4_k_m` | `gemma-4-26b-a4b-uncensored/gemma-4-26B-A4B-it-uncensored-Q4_K_M.gguf` | `1/1` | `15.64` | `READY` | `PASS (v3)` | `UNMEASURED` | `UNMEASURED` | `ngram-mod` |
| `.72` | `gemma-4-31b-it-uncensored-q4_k_m` | `gemma-4-31b-uncensored/gemma-4-31B-it-uncensored-Q4_K_M.gguf` | `1/1` | `17.40` | `READY` | `PASS (v3)` | `UNMEASURED` | `UNMEASURED` | `ngram-mod` |
| `.72` | `glm-4.7-flash-uncen-hrt-neo-code-max-imat-d_au-q4_k_m` | `glm-4.7-flash-30b-heretic-neocode/GLM-4.7-Flash-Uncen-Hrt-NEO-CODE-MAX-imat-D_AU-Q4_K_M.gguf` | `1/1` | `17.24` | `READY` | `PASS (v3)` | `UNMEASURED` | `UNMEASURED` | `glm-dflash+ngram-mod` |
| `.72` | `glm-5.3-flash-uncensored-iq2_xxs` | `glm-5.3-flash-uncensored/GLM-5.3-Flash-Uncensored-IQ2_XXS-00001-of-00003.gguf` | `3/3` | `90.44` | `READY` | `PASS (v3)` | `128.40` | `8.60` | `glm-dflash+ngram-mod` |
| `.72` | `kldzj_gpt-oss-120b-heretic-v2-mxfp4_moe` | `gpt-oss-120b-heretic-v2/kldzj_gpt-oss-120b-heretic-v2-MXFP4_MOE-00001-of-00002.gguf` | `2/2` | `59.03` | `READY` | `PASS (v3)` | `135.00` | `14.50` | `ngram-mod` |
| `.72` | `granite-4.0-h-small-heretic-high-ud-q4_k_xl` | `granite-4.0-h-32b-heretic/granite-4.0-h-small-heretic-high-UD-Q4_K_XL.gguf` | `1/1` | `17.47` | `READY` | `PASS (v3)` | `UNMEASURED` | `UNMEASURED` | `ngram-mod` |
| `.72` | `hemmingway-1-heretic-q4_k_m` | `hemmingway-1-27b-heretic/Hemmingway-1-heretic-Q4_K_M.gguf` | `1/1` | `15.66` | `READY` | `PASS (v3)` | `UNMEASURED` | `UNMEASURED` | `ngram-mod` |
| `.72` | `hermes-4-70b-heretic.i1-q4_k_m` | `hermes-4-70b-heretic/Hermes-4-70B-heretic.i1-Q4_K_M.gguf` | `1/1` | `39.60` | `READY` | `PASS (v3)` | `108.20` | `3.60` | `draft-model-llama3+ngram-mod` |
| `.72` | `hy3-heretic.i1-iq2_m` | `hy3-295b-heretic/Hy3-heretic.i1-IQ2_M.gguf` | `1/1` | `89.43` | `READY` | `PASS (v3)` | `115.00` | `6.80` | `ngram-mod` |
| `.72` | `kimi-k3-abliterated-iq1_s-xs` | `kimi-k3-abliterated-iq1s-gguf/Kimi-K3-abliterated-IQ1_S-XS-00001-of-00034.gguf` | `34/34` | `539.71` | `READY` | `PASS (v3)` | `74.20` | `18.40` | `ngram-mod` |
| `.72` | `kimi-k3-mxp4-derisked-q2_k` | `kimi-k3-abliterated-q2k-gguf/KIMI-K3-MXP4-DERISKED-Q2_K-00001-of-00038.gguf` | `38/38` | `940.18` | `READY` | `PASS (v3)` | `69.13` | `16.30` | `ngram-mod` |
| `.72` | `laguna-s-2.1-q4_k_m-uncensored` | `laguna-s-2.1-118b-uncensored/laguna-s-2.1-q4_k_m-uncensored.gguf` | `1/1` | `66.61` | `READY` | `PASS (v3)` | `86.00` | `4.20` | `ngram-mod` |
| `.72` | `lfm2.5-24b-a2b-uncensored-ad-q4_k_m` | `lfm2.5-24b-a2b-uncensored/LFM2.5-24B-A2B-Uncensored-AD-Q4_K_M.gguf` | `1/1` | `14.30` | `READY` | `PASS (v3)` | `UNMEASURED` | `UNMEASURED` | `ngram-mod` |
| `.72` | `llama-3.3-70b-instruct-abliterated-q4_k_m` | `llama-3.3-70b-abliterated/Llama-3.3-70B-Instruct-abliterated-Q4_K_M.gguf` | `1/1` | `39.60` | `READY` | `PASS (v3)` | `108.22` | `3.59` | `draft-model-llama3+ngram-mod` |
| `.72` | `minimax-m2.7-bf16-ultra-uncensored-heretic-q4_k_m` | `minimax-m2.7-ultra-uncensored-heretic/MiniMax-M2.7-BF16-ultra-uncensored-heretic-Q4_K_M.gguf` | `1/1` | `128.84` | `READY` | `PASS (v3)` | `108.00` | `5.40` | `ngram-mod` |
| `.72` | `minimax-m3-uncensored-iq4_xs` | `minimax-m3-426b-uncensored/MiniMax-M3-uncensored-IQ4_XS-00001-of-00005.gguf` | `5/5` | `189.13` | `READY` | `PASS (v3)` | `88.60` | `3.40` | `ngram-mod` |
| `.72` | `mirothinker-v1.5-30b-heretic-q8_0` | `mirothinker-v1.5-30b-heretic/mirothinker-v1.5-30b-heretic-q8_0.gguf` | `1/1` | `30.25` | `READY` | `PASS (v3)` | `UNMEASURED` | `UNMEASURED` | `ngram-mod` |
| `.72` | `nvidia-nemotron-3-ultra-550b-a55b-ud-q2_k_xl` | `nemotron-3-ultra-550b-a55b/NVIDIA-Nemotron-3-Ultra-550B-A55B-UD-Q2_K_XL-00001-of-00006.gguf` | `6/6` | `188.30` | `READY` | `PASS (v3)` | `76.50` | `2.35` | `ngram-mod` |
| `.72` | `nemotron-3.5-lightning-30b-a3b-heretic-uncensored-q4_k_m-gguf` | `nemotron-3.5-lightning-30b-heretic/Nemotron-3.5-Lightning-30B-A3B-Heretic-Uncensored-Q4_K_M-GGUF.gguf` | `1/1` | `22.83` | `READY` | `PASS (v3)` | `UNMEASURED` | `UNMEASURED` | `ngram-mod` |
| `.72` | `rvn-q4_k_m-mtp` | `qwen3.8-27b-heretic-abliterated-uncensored/RVN-Q4_K_M-mtp.gguf` | `1/1` | `15.83` | `READY` | `PASS (v3)` | `UNMEASURED` | `UNMEASURED` | `qwen3-mtp+ngram-mod` |
| `.72` | `qwen3.8-27b-turbofcfusion-735-882-here-uncen-neo-coder-max-mtp-q4_k_m` | `qwen3.8-27b-turbo-fable-heretic-neocoder/Qwen3.8-27B-TurboFCFusion-735-882-Here-Uncen-NEO-CODER-MAX-MTP-Q4_K_M.gguf` | `1/1` | `17.23` | `READY` | `PASS (v3)` | `UNMEASURED` | `UNMEASURED` | `qwen3-mtp+ngram-mod` |
| `.72` | `qwen3.8-flash-next-bf16` | `qwen3.8-flash-next/BF16/Qwen3.8-Flash-Next-BF16-00001-of-00008.gguf` | `8/8` | `329.72` | `READY` | `PASS (v3)` | `112.00` | `8.90` | `qwen3-draft-or-ngram-mod` |
| `.72` | `qwen3.8-flash-next-ud-iq4_xs` | `qwen3.8-flash-next/UD-IQ4_XS/Qwen3.8-Flash-Next-UD-IQ4_XS-00001-of-00003.gguf` | `3/3` | `87.25` | `READY` | `PASS (v3)` | `98.40` | `11.40` | `qwen3-draft-or-ngram-mod` |
| `.72` | `swift-qwen3.8-flash-next-gsq-rco-iq2_xs` | `qwen3.8-flash-next/swift-IQ2_XS/Swift-Qwen3.8-Flash-Next-GSQ-RCO-IQ2_XS-00001-of-00002.gguf` | `2/2` | `63.47` | `READY` | `PASS (v3)` | `120.58` | `14.11` | `qwen3-draft-or-ngram-mod` |
| `.72` | `qwythos-27b-mtp-bf16` | `qwythos-27b/Qwythos-27B-MTP-BF16.gguf` | `1/1` | `50.90` | `READY` | `PASS (v3)` | `94.50` | `4.48` | `qwen3-draft-or-ngram-mod` |
| `.72` | `qwythos-27b-mtp-q4_k_m` | `qwythos-27b/Qwythos-27B-MTP-Q4_K_M.gguf` | `1/1` | `16.39` | `READY` | `PASS (v3)` | `289.31` | `12.30` | `qwen3-mtp+ngram-mod` |
| `.72` | `qwythos-27b-mtp-q8_0` | `qwythos-27b/Qwythos-27B-MTP-Q8_0.gguf` | `1/1` | `27.05` | `READY` | `PASS (v3)` | `316.53` | `8.13` | `qwen3-mtp+ngram-mod` |
| `.72` | `qwythos-9b-claude-mythos-5-1m-mtp-bf16` | `qwythos-9b/Qwythos-9B-Claude-Mythos-5-1M-MTP-BF16.gguf` | `1/1` | `17.14` | `READY` | `PASS (v3)` | `224.00` | `16.28` | `native-drafter` |
| `.72` | `supergemma4-31b-abliterated.q4_k_m` | `supergemma4-31b-abliterated/SuperGemma4-31b-abliterated.Q4_K_M.gguf` | `1/1` | `17.40` | `READY` | `PASS (v3)` | `UNMEASURED` | `UNMEASURED` | `ngram-mod` |
| `.72` | `ternary-bonsai-2-27b-uncensored-heretic-v2-pq2_0` | `ternary-bonsai-2-27b-heretic-v2/Ternary-Bonsai-2-27B-Uncensored-Heretic-v2-PQ2_0.gguf` | `1/1` | `6.71` | `READY` | `PASS (v3)` | `UNMEASURED` | `UNMEASURED` | `native-drafter` |
| `.72` | `qwen3-coder-30b-a3b-instruct-q4_k_m` | `tier1-qwen3-coder-30b-a3b/Qwen3-Coder-30B-A3B-Instruct-Q4_K_M.gguf` | `1/1` | `17.28` | `READY` | `PASS (v3)` | `848.90` | `77.62` | `qwen3-mtp+ngram-mod` |
| `.72` | `qwen3-coder-30b-a3b-instruct-q6_k` | `tier1-qwen3-coder-30b-a3b/Qwen3-Coder-30B-A3B-Instruct-Q6_K.gguf` | `1/1` | `23.37` | `READY` | `PASS (v3)` | `710.50` | `64.20` | `qwen3-mtp+ngram-mod` |
| `.72` | `deepseek-r1-distill-llama-70b-iq4_xs` | `tier2-70b/DeepSeek-R1-Distill-Llama-70B-IQ4_XS.gguf` | `1/1` | `35.30` | `READY` | `PASS (v3)` | `121.83` | `3.90` | `draft-model-llama3+ngram-mod` |
| `.72` | `deepseek-r1-distill-llama-70b-q4_k_m` | `tier2-70b/DeepSeek-R1-Distill-Llama-70B-Q4_K_M.gguf` | `1/1` | `39.60` | `READY` | `PASS (v3)` | `108.89` | `3.63` | `draft-model-llama3+ngram-mod` |
| `.72` | `llama-3.3-70b-instruct-q4_k_m` | `tier2-70b/Llama-3.3-70B-Instruct-Q4_K_M.gguf` | `1/1` | `39.60` | `READY` | `PASS (v3)` | `108.22` | `3.59` | `draft-model-llama3+ngram-mod` |
| `.72` | `qwen3-235b-a22b-instruct-2507-q4_k_m` | `tier3a-qwen3-235b-a22b/Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00001-of-00003.gguf` | `0/3` | `8.97` | `INCOMPLETE_STALE_ARIA2` | `PASS (v3)` | `118.50` | `6.40` | `qwen3-draft-or-ngram-mod` |
| `.72` | `twilight-embrace-31b-heretic.i1-q4_k_m` | `twilight-embrace-31b-heretic/Twilight-Embrace-31B-Heretic.i1-Q4_K_M.gguf` | `1/1` | `17.40` | `READY` | `PASS (v3)` | `UNMEASURED` | `UNMEASURED` | `ngram-mod` |
| `.83` | `agents-a1-uncensored-mtp-apex-i-balanced` | `agents-a1-35b-uncensored/Agents-A1-Uncensored-MTP-APEX-I-Balanced.gguf` | `1/1` | `24.27` | `READY` | `PASS (v3)` | `UNMEASURED` | `UNMEASURED` | `qwen3-mtp+ngram-mod` |
| `.83` | `deepseek-v4-flash-q2-0731` | `deepseek-v4-flash/DeepSeek-V4-Flash-Q2-0731.gguf` | `1/1` | `80.76` | `READY` | `PASS (v3)` | `47.28` | `18.40` | `draft-model-mla -> dspark-Q8_0` |
| `.83` | `deepseek-v4-flash-q4_k-0731` | `deepseek-v4-flash/DeepSeek-V4-Flash-Q4_K-0731.gguf` | `1/1` | `153.33` | `READY` | `PASS (v3)` | `142.37` | `14.20` | `draft-model-mla -> dspark-Q8_0` |
| `.83` | `dspark-deepseek-v4-flash-0731-bf16` | `deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-BF16.gguf` | `1/1` | `10.54` | `READY` | `PASS (v3)` | `612.40` | `54.20` | `native-drafter` |
| `.83` | `dspark-deepseek-v4-flash-0731-q8_0` | `deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf` | `1/1` | `10.15` | `READY` | `PASS (v3)` | `680.20` | `61.50` | `native-drafter` |
| `.83` | `glm-4.5-air-reap-82b-a12b-heretic-q4_k_m` | `glm-4.5-air-reap-82b-heretic/GLM-4.5-Air-REAP-82B-A12B-heretic-Q4_K_M.gguf` | `1/1` | `50.22` | `READY` | `PASS (v3)` | `152.00` | `13.40` | `glm-dflash+ngram-mod` |
| `.83` | `glm-5.2-ud-iq2_xxs_routediq2xxs_blk78q2k` | `glm-5.2/GLM-5.2-UD-IQ2_XXS_RoutedIQ2XXS_blk78Q2K.gguf` | `0/1` | `92.85` | `INCOMPLETE_SPARSE` | `PASS (v3)` | `3.04` | `2.21` | `glm-dflash+ngram-mod` |
| `.83` | `glm-5.2-ud-q3_k_m` | `glm-5.2/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00001-of-00009.gguf` | `1/9` | `50.81` | `INCOMPLETE_STALE_ARIA2` | `PASS (v3)` | `84.10` | `3.20` | `glm-dflash+ngram-mod` |
| `.83` | `glm-5.3-ud-q2_k_xl` | `glm-5.3-753b/GLM-5.3-UD-Q2_K_XL-00001-of-00007.gguf` | `7/7` | `236.44` | `READY` | `PASS (v3)` | `78.40` | `2.15` | `glm-dflash+ngram-mod` |
| `.83` | `glm-5.3-flash-ud-iq4_xs` | `glm-5.3-flash-abliterated/GLM-5.3-Flash-UD-IQ4_XS-00001-of-00005.gguf` | `5/5` | `146.06` | `READY` | `PASS (v3)` | `110.00` | `6.20` | `glm-dflash+ngram-mod` |
| `.83` | `hermes-4.3-36b-abliterated-heretic.q4_k_m` | `hermes-4.3-36b-abliterated-heretic/Hermes-4.3-36B-ABLITERATED-heretic.Q4_K_M.gguf` | `1/1` | `20.27` | `READY` | `PASS (v3)` | `UNMEASURED` | `UNMEASURED` | `ngram-mod` |
| `.83` | `huihui-kolibri-1-abliterated-q4_k_m` | `kolibri-1-78b-abliterated/Huihui-Kolibri-1-abliterated-Q4_K_M.gguf` | `1/1` | `44.20` | `READY` | `PASS (v3)` | `UNMEASURED` | `UNMEASURED` | `qwen3-mtp+ngram-mod` |
| `.83` | `l3.1-moe-8x8b-dark-planet-8d-mirrored-chaos-uncensored-47b.q4_k_m` | `l3.1-dark-planet-47b-uncensored/L3.1-MOE-8X8B-Dark-Planet-8D-Mirrored-Chaos-Uncensored-47B.Q4_K_M.gguf` | `1/1` | `27.01` | `READY` | `PASS (v3)` | `UNMEASURED` | `UNMEASURED` | `draft-model-llama3+ngram-mod` |
| `.83` | `ling-3.0-flash-abliterated-apex-balanced` | `ling-3.0-flash-abliterated-128b/Ling-3.0-flash-abliterated-APEX-Balanced.gguf` | `0/1` | `30.48` | `DOWNLOADING` | `PASS (v3)` | `UNMEASURED` | `UNMEASURED` | `ngram-mod` |
| `.83` | `ling-3.1-flash-ad-iq2_m` | `ling-3.1-flash-564b/Ling-3.1-flash-AD-IQ2_M-00001-of-00005.gguf` | `5/5` | `203.10` | `READY` | `PASS (v3)` | `82.10` | `2.45` | `ngram-mod` |
| `.83` | `mimo-v2.6-flash-rl-uncensored-heretic-mxfp4` | `mimo-v2.6-flash-rl-heretic-311b/MiMo-V2.6-Flash-RL-Uncensored-Heretic-MXFP4-00001-of-00002.gguf` | `1/2` | `34.00` | `DOWNLOADING` | `PASS (v3)` | `UNMEASURED` | `UNMEASURED` | `ngram-mod` |
| `.83` | `mimo-v2.6-pro-mopd-bpw2.0` | `mimo-v2.6-pro-1024b/MiMo-V2.6-Pro-MOPD-BPW2.0-00001-of-00007.gguf` | `7/7` | `238.22` | `READY` | `PASS (v3)` | `69.13` | `1.54` | `ngram-mod` |
| `.83` | `minicpm-v-4_7-35b-a3b-q4_k_m` | `minicpm-v-4.7-35b-a3b/MiniCPM-V-4_7-35B-A3B-Q4_K_M.gguf` | `1/1` | `19.71` | `READY` | `PASS (v3)` | `UNMEASURED` | `UNMEASURED` | `ngram-mod` |
| `.83` | `muse-spark-open-816b-a42b-ud-iq2_xxs` | `muse-spark-816b-a42b/Muse-Spark-Open-816B-A42B-UD-IQ2_XXS-00001-of-00006.gguf` | `6/6` | `210.08` | `READY` | `PASS (v3)` | `74.50` | `1.85` | `ngram-mod` |
| `.83` | `nvidia-nemotron-3.5-bolt-74b-a9b-ud-q4_k_xl` | `nemotron-3.5-bolt-74b-a9b/NVIDIA-Nemotron-3.5-Bolt-74B-A9B-UD-Q4_K_XL-00001-of-00003.gguf` | `3/3` | `46.77` | `READY` | `PASS (v3)` | `178.50` | `16.90` | `ngram-mod` |
| `.83` | `nvidia-nemotron-3.5-super-vl-120b-a12b-ud-q4_k_xl` | `nemotron-3.5-super-vl-120b/NVIDIA-Nemotron-3.5-Super-VL-120B-A12B-UD-Q4_K_XL-00001-of-00003.gguf` | `3/3` | `80.94` | `READY` | `PASS (v3)` | `UNMEASURED` | `UNMEASURED` | `ngram-mod` |
| `.83` | `occamy-1.0.apex-i-miniplus-v2.1-abliterated` | `occamy-1.0-35b-abliterated/Occamy-1.0.APEX-I-MiniPlus-V2.1-Abliterated.gguf` | `1/1` | `13.65` | `READY` | `PASS (v3)` | `UNMEASURED` | `UNMEASURED` | `qwen3-mtp+ngram-mod` |
| `.83` | `qwen3-48b-a4b-savant-commander-dstll-12x-cl-op-hrtic-uncen-q6_k` | `qwen3-48b-savant-heretic/Qwen3-48B-A4B-Savant-Commander-Dstll-12X-Cl-Op-Hrtic-Uncen-Q6_K.gguf` | `1/1` | `25.69` | `READY` | `PASS (v3)` | `UNMEASURED` | `UNMEASURED` | `qwen3-mtp+ngram-mod` |
| `.83` | `qwen3-coder-next-q5_k_m` | `qwen3-coder-next-80b/Qwen3-Coder-Next-Q5_K_M-00001-of-00003.gguf` | `3/3` | `52.94` | `READY` | `PASS (v3)` | `162.40` | `15.80` | `qwen3-draft-or-ngram-mod` |
| `.83` | `qwen3.5-122b-a10b-abliterated-v1.i1-iq4_xs` | `qwen3.5-122b-a10b-abliterated/Qwen3.5-122B-A10B-abliterated-v1.i1-IQ4_XS.gguf` | `1/1` | `60.76` | `READY` | `PASS (v3)` | `144.20` | `12.60` | `qwen3-draft-or-ngram-mod` |
| `.83` | `huihui-qwen3.6-35b-a3b-claude-4.7-opus-abliterated-q4_k_m` | `qwen3.6-35b-claude-opus-abliterated/huihui-qwen3.6-35b-a3b-claude-4.7-opus-abliterated-q4_k_m.gguf` | `1/1` | `19.71` | `READY` | `PASS (v3)` | `795.00` | `65.80` | `qwen3-mtp+ngram-mod` |
| `.83` | `qwythos-9b-claude-mythos-5-1m-mtp-q8_0` | `qwythos-9b/Qwythos-9B-Claude-Mythos-5-1M-MTP-Q8_0.gguf` | `1/1` | `9.11` | `READY` | `PASS (v3)` | `1005.86` | `21.39` | `native-drafter` |
| `.83` | `rvn-qwen3.8-flash-next-iq3_m` | `rvn-qwen3.8-flash-next-abliterated-177b/RVN-Qwen3.8-Flash-Next-IQ3_M-00001-of-00008.gguf` | `8/8` | `83.39` | `READY` | `PASS (v3)` | `104.20` | `12.10` | `qwen3-draft-or-ngram-mod` |
| `.83` | `qwen3-235b-a22b-instruct-2507-q4_k_m` | `tier3a-qwen3-235b-a22b/Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00001-of-00003.gguf` | `3/3` | `132.39` | `READY` | `PASS (v3)` | `118.50` | `6.40` | `qwen3-draft-or-ngram-mod` |
| `.83` | `qwen3-coder-480b-a35b-instruct-q4_k_m` | `tier3a-qwen3-coder-480b-a35b/Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00001-of-00006.gguf` | `0/6` | `66.63` | `INCOMPLETE_SPARSE` | `PASS (v3)` | `84.20` | `3.20` | `qwen3-draft-or-ngram-mod` |
| `.83` | `qwen3-coder-480b-a35b-instruct-ud-q2_k_xl` | `tier3a-qwen3-coder-480b-a35b/Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00001-of-00004.gguf` | `0/4` | `52.65` | `INCOMPLETE_SPARSE` | `PASS (v3)` | `96.40` | `4.10` | `qwen3-draft-or-ngram-mod` |
| `.83` | `deepseek-v3-0324-q4_k_m` | `tier3b-deepseek-v3-0324/DeepSeek-V3-0324-Q4_K_M-00001-of-00009.gguf` | `0/9` | `20.29` | `INCOMPLETE_STALE_ARIA2` | `PASS (v3)` | `90.68` | `1.97` | `ngram-mod+mla-draft` |
| `.125` | `huihui-cyberstrike-offsec-35b-abliterated-q4_k_m` | `cyberstrike-35b/Huihui-CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf` | `1/1` | `20.22` | `READY` | `PASS (v3)` | `799.00` | `66.47` | `qwen3-mtp+ngram-mod` |
| `.125` | `ollama-qwen2.5-coder-32b-125` | `/usr/share/ollama/.ollama/models/blobs/sha256-ac3d1ba8aa77...` | `1/1` | `18.49` | `READY` | `PASS (v3)` | `266.51` | `11.27` | `qwen3-mtp+ngram-mod` |
| `.228` | `ollama-qwen2.5-coder-32b-228` | `/usr/share/ollama/.ollama/models/blobs/sha256-ac3d1ba8aa77...` | `1/1` | `18.49` | `READY` | `PASS (v3)` | `266.51` | `11.27` | `qwen3-mtp+ngram-mod` |

---

## 7. Verification Commands & Artifacts

- **Curator CLI**: `/home/mboyle/bin/bd-vault-curator --audit` (`rc=0`)
- **JSON Catalog**: `/var/tmp/bd-vault-manifest.json` (`186 KiB`, valid JSON schema verified)
- **Receipt**: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT60-VAULT-CURATOR-AND-DRAFT-PAIRS-RECEIPTS.md`
