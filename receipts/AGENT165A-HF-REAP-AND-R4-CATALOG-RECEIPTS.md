# AGENT-165A: Hugging Face Pre-Quantized REAP & `ubergarm` `_R4` Catalog Receipts (O1789)

- **Agent**: `Agent-165A` (Hugging Face Pre-Quantized REAP & `ubergarm` `_R4` Catalog Researcher)
- **Timestamp (UTC)**: `2026-10-19T02:09:23Z`
- **Denominator Audited**: `896` Hugging Face repositories queried via `https://huggingface.co/api/models/<repo_id>?blobs=true`
- **Benchmark Load**: `ZERO` on `.72` (`10.0.70.72`) and `.83` (`10.0.70.83`)
- **Safety Invariant**: `/opt/models/Uniboshi--Kimi-K3-Abliterated-V1` (`1,453.74 GiB` / `1,560,936,091,448 B` across 96 Safetensors shards on `.83`, referenced in historical brief as `/mnt/models-d2/Uniboshi--Kimi-K3-Abliterated-V1`) verified **100% untouched**.

---

## 1. Executive Measurement Summary (Rule 6 Format)

1. **Abliterated `Kimi-K3` REAP / Pruned GGUFs under `~360 GiB` matching `Uniboshi/Kimi-K3-Abliterated-V1`**:
   - **Verdict**: **`FOUND NONE` (`0` of `74` audited `Kimi-K3` / `Uniboshi` repos)**.
   - Every abliterated `Kimi-K3` GGUF on Hugging Face is **unpruned** (`>= 539.71 GiB`):
     - `AzynTT/Kimi-K3-Uncensored-GGUF` (`Kimi-K3-abliterated-IQ1_S-XS`, 34 shards): `539.71 GiB` (`579,511,854,976 B`) — *already staged on `.72` at `/opt/models/kimi-k3-abliterated-iq1s-gguf`*
     - `audnai/penclaw-Kimi-K3.0-abliterated-GGUF` (`UD-Q2_K_XL` / `V5-UD-Q2_K_XL`, 19 shards): `802.13 GiB` (`861,277,858,912 B`)
     - `GrEarl/Kimi-K3-Abliterated-V1-Q2_K-GGUF` (`Kimi-K3-Q2_K`, 94 shards): `864.81 GiB` (`928,581,175,840 B`)
     - `Blackfrost-AI/KIMI-K3-Q2_K-GGUF-ABLITERATED` (`KIMI-K3-MXP4-DERISKED-Q2_K`, 38 shards): `940.18 GiB` (`1,009,506,619,072 B`) — *already staged on `.72` at `/opt/models/kimi-k3-abliterated-q2k-gguf`*
     - `Uniboshi/Kimi-K3-Abliterated-V1-GGUF` (`MXFP4_MOE`, 31 shards): `1,404.78 GiB` (`1,508,368,775,552 B`)
   - **Non-Abliterated (Censored Base) `Kimi-K3` REAP / Pruned GGUFs under `~360 GiB`**: **`FOUND 4` repos** (`mmnga-o/Kimi-K3-REAP50-UD-gguf` at `304.47 GiB` / `330.02 GiB`, `mmnga-o/Kimi-K3-REAP50-Width50-UD-gguf` at `180.66 GiB`–`242.89 GiB`, `prometheusAIR/Kimi-K3-REAP55-GGUF` at `318.96 GiB`, `vcruz305/Kimi-K3-GGUF` at `298.33 GiB` / `307.49 GiB`), plus `0xTank/Kimi-K3-IQ1S-REAP568-64K-4XSPARKS` at `371.10 GiB` and `hellohazime/Kimi-K3-REAP-512GB-GGUF` at `411.07 GiB`. None of these are abliterated.
   - **Mandatory Architectural Conclusion**:
     - Abliterated `Kimi-K3` **stays an offline model at `540 GiB`** (`/opt/models/kimi-k3-abliterated-iq1s-gguf` on `.72`, awaiting the `700 GiB` `AI DEV` RAM resize).
     - `/opt/models/Uniboshi--Kimi-K3-Abliterated-V1` on `.83` (`1,453.74 GiB` / `1,560,936,091,448 B` BF16/FP16 Safetensors across 96 shards; historical path `/mnt/models-d2/Uniboshi--Kimi-K3-Abliterated-V1`) **MUST be strictly kept on disk** as the sole input for a future local REAP prune.

2. **Pre-Pruned REAP Checkpoints & GGUFs (`Cerebras`, `bartowski`, `unsloth`, `mradermacher`, `heath0xFF`, `noctrex`, `crucible-labs`, `0xSero`, `pipenetwork`)**:
   - **Verdict**: **`FOUND 48` REAP repositories** across `DeepSeek-V4-Flash` REAP, `DeepSeek-V3.2-REAP-345B-A37B`, `GLM-4.6-REAP-268B-A32B` / `218B-A32B`, `GLM-4.7-REAP-268B-A32B` / `218B-A32B`, `GLM-5.2-REAP-504B`, `Qwen3-Coder-REAP-363B-A35B` / `246B-A35B`, `Gemma-4` REAP (`26B-A4B`, `21B-A4B`, `19B-A4B`), `GLM-4.5-Air-REAP-82B-A12B`, `MiniMax-M2-REAP`, and `Step-3.5-Flash-REAP-149B-A11B`.

3. **`ubergarm` (`ik_llama.cpp` `_R4` & Hybrid `Q8_0` Attention/Shared-Expert + `IQ*_K*` Routed-Expert Quants)**:
   - **Verdict**: **`FOUND 36` `ubergarm` GGUF repositories** (`2` with explicit pre-repacked `_R4` tensor types on disk: `ubergarm/DeepSeek-V3-0324-GGUF` and `ubergarm/DeepSeek-R1-0528-GGUF`; and `34` newer `ik_llama.cpp` repositories where attention + shared experts are quantized to `Q8_0` for GPU offload while routed experts use `IQ1_KT`/`IQ2_KS`/`IQ2_KL`/`IQ3_KS`/`IQ3_K`/`IQ4_KSS`/`IQ4_K` designed for `ik_llama.cpp -rtr` runtime `_R4` CPU repacking).

---

## 2. Task 1A: Complete `Kimi-K3` & `Uniboshi` Catalog (`?blobs=true`)

| Repository ID | Variant / Subfolder | Abliterated? | REAP / Pruned? | Shards | Exact Size (Bytes) | Size (GiB) | Fits `.72` (`314 GiB`) | Fits `.83` (`395 GiB`) |
|---|---|---:|---:|---:|---:|---:|---:|---:|
| `Uniboshi/Kimi-K3-Abliterated-V1` | `Safetensors (BF16/FP16)` | **YES** | No (Base) | 96 | `1,560,936,091,448` | `1453.74` | NO | NO (On disk `.83`) |
| `Uniboshi/Kimi-K3-Abliterated-V1-GGUF` | `MXFP4_MOE/Kimi-K3-Abliterated-V1-MXFP4_MOE-*.gguf` | **YES** | No | 31 | `1,508,368,775,552` | `1404.78` | NO | NO |
| `Blackfrost-AI/KIMI-K3-Q2_K-GGUF-ABLITERATED` | `KIMI-K3-MXP4-DERISKED-Q2_K-*.gguf` | **YES** | No | 38 | `1,009,506,619,072` | `940.18` | NO (On disk `.72`) | NO |
| `GrEarl/Kimi-K3-Abliterated-V1-Q2_K-GGUF` | `Kimi-K3-Q2_K-*.gguf` | **YES** | No | 94 | `928,581,175,840` | `864.81` | NO | NO |
| `audnai/penclaw-Kimi-K3.0-abliterated-GGUF` | `UD-Q2_K_XL/Kimi-K3-UD-Q2_K_XL-00001-of-00019.gguf` | **YES** | No | 19 | `861,277,858,912` | `802.13` | NO | NO |
| `audnai/penclaw-Kimi-K3.0-abliterated-GGUF` | `V5-UD-Q2_K_XL/Kimi-K3-UD-Q2_K_XL-00001-of-00019.gguf` | **YES** | No | 19 | `861,277,858,912` | `802.13` | NO | NO |
| `AzynTT/Kimi-K3-Uncensored-GGUF` | `Kimi-K3-abliterated-IQ1_S-XS-00001-of-00034.gguf` | **YES** | No | 34 | `579,511,854,976` | `539.71` | **NO (Needs `700 GiB` resize; on disk `.72`)** | NO |
| `mmnga-o/Kimi-K3-REAP50-Width50-UD-gguf` | `UD-IQ1_S/Kimi-K3-REAP50-Width50-UD-IQ1_S-*.gguf` | No | **YES (REAP50+W50)** | 14 | `193,977,034,208` | `180.66` | YES | YES |
| `mmnga-o/Kimi-K3-REAP50-Width50-UD-gguf` | `UD-IQ1_M/Kimi-K3-REAP50-Width50-UD-IQ1_M-*.gguf` | No | **YES (REAP50+W50)** | 15 | `207,695,554,208` | `193.43` | YES | YES |
| `mmnga-o/Kimi-K3-REAP50-Width50-UD-gguf` | `UD-Q2_K_XL/Kimi-K3-REAP50-Width50-UD-Q2_K_XL-*.gguf` | No | **YES (REAP50+W50)** | 19 | `260,797,016,160` | `242.89` | YES | YES |
| `vcruz305/Kimi-K3-GGUF` | `UD-IQ1_S_DENSE` | No | **YES (Dense Pruned)** | 9 | `320,330,179,168` | `298.33` | Borderline | YES |
| `mmnga-o/Kimi-K3-REAP50-UD-gguf` | `UD-IQ1_S/Kimi-K3-REAP50-UD-IQ1_S-*.gguf` | No | **YES (REAP50)** | 14 | `326,923,363,808` | `304.47` | NO | YES |
| `vcruz305/Kimi-K3-GGUF` | `UD-IQ1_S` | No | **YES (Pruned)** | 9 | `330,167,807,328` | `307.49` | NO | YES |
| `prometheusAIR/Kimi-K3-REAP55-GGUF` | `Kimi-K3-REAP55-IQ1_M-00001-of-00008.gguf` | No | **YES (REAP55)** | 8 | `342,484,184,832` | `318.96` | NO | YES |
| `mmnga-o/Kimi-K3-REAP50-UD-gguf` | `UD-IQ1_M/Kimi-K3-REAP50-UD-IQ1_M-*.gguf` | No | **YES (REAP50)** | 15 | `354,360,403,616` | `330.02` | NO | YES |
| `0xTank/Kimi-K3-IQ1S-REAP568-64K-4XSPARKS` | `Kimi-K3-UD-IQ1_S-00001-of-00014.gguf` | No | **YES (REAP568)** | 14 | `398,461,200,102` | `371.10` | NO | Borderline |
| `hellohazime/Kimi-K3-REAP-512GB-GGUF` | `REAP640-IQ1_S` | No | **YES (REAP640)** | 10 | `441,383,893,056` | `411.07` | NO | NO |
| `mmnga-o/Kimi-K3-REAP50-UD-gguf` | `UD-Q2_K_XL/Kimi-K3-REAP50-UD-Q2_K_XL-*.gguf` | No | **YES (REAP50)** | 19 | `460,563,327,072` | `428.93` | NO | NO |
| `hellohazime/Kimi-K3-REAP-512GB-GGUF` | `REAP576-IQ2_XXS` | No | **YES (REAP576)** | 1 | `478,489,565,024` | `445.63` | NO | NO |
| `unsloth/Kimi-K3-GGUF` | `UD-Q1_0` .. `UD-Q8_K_XL` | No | No | 11–34 | `466,369,456,320` – `1,561,157,884,384` | `434.34` – `1453.94` | NO | NO |

---

## 3. Task 1B: Existing REAP Checkpoints & GGUFs Catalog (`?blobs=true`)

### 3.1 Official `cerebras/*` REAP Safetensors Checkpoints (Upstream Sources)

| Repository ID | Format | Shards | Exact Size (Bytes) | Size (GiB) |
|---|---|---:|---:|---:|
| `cerebras/DeepSeek-V3.2-REAP-345B-A37B` | FP8 Safetensors | 65 | `346,853,616,400` | `323.03` |
| `cerebras/DeepSeek-V3.2-REAP-508B-A37B` | FP8 Safetensors | 96 | `510,406,802,624` | `475.35` |
| `cerebras/GLM-4.6-REAP-268B-A32B` | BF16 Safetensors | 108 | `537,581,517,872` | `500.66` |
| `cerebras/GLM-4.6-REAP-268B-A32B-FP8` | FP8 Safetensors | 55 | `270,585,984,168` | `252.00` |
| `cerebras/GLM-4.6-REAP-252B-A32B` | BF16 Safetensors | 102 | `503,977,580,000` | `469.37` |
| `cerebras/GLM-4.6-REAP-252B-A32B-FP8` | FP8 Safetensors | 51 | `253,768,303,648` | `236.34` |
| `cerebras/GLM-4.6-REAP-218B-A32B` | BF16 Safetensors | 88 | `436,769,705,400` | `406.77` |
| `cerebras/GLM-4.6-REAP-218B-A32B-FP8` | FP8 Safetensors | 45 | `220,132,937,328` | `205.01` |
| `cerebras/GLM-4.7-REAP-268B-A32B` | BF16 Safetensors | 101 | `537,581,518,568` | `500.66` |
| `cerebras/GLM-4.7-REAP-218B-A32B` | BF16 Safetensors | 82 | `436,769,706,312` | `406.77` |
| `cerebras/GLM-4.5-Air-REAP-82B-A12B` | BF16 Safetensors | 33 | `169,991,698,280` | `158.32` |
| `cerebras/GLM-4.5-Air-REAP-82B-A12B-FP8` | FP8 Safetensors | 17 | `83,257,878,808` | `77.54` |
| `cerebras/Qwen3-Coder-REAP-363B-A35B` | BF16 Safetensors | 146 | `726,239,968,312` | `676.36` |
| `cerebras/Qwen3-Coder-REAP-363B-A35B-FP8` | FP8 Safetensors | 74 | `365,081,827,408` | `340.01` |
| `cerebras/Qwen3-Coder-REAP-246B-A35B` | BF16 Safetensors | 99 | `492,166,397,312` | `458.37` |
| `cerebras/Qwen3-Coder-REAP-246B-A35B-FP8` | FP8 Safetensors | 50 | `248,014,116,016` | `230.98` |
| `cerebras/Step-3.5-Flash-REAP-149B-A11B` | BF16 Safetensors | 35 | `298,834,769,320` | `278.31` |
| `cerebras/Step-3.5-Flash-REAP-121B-A11B` | BF16 Safetensors | 29 | `242,796,959,144` | `226.12` |

### 3.2 `DeepSeek-V4-Flash` REAP & `DeepSeek-V3.2` REAP GGUFs

| Repository ID | Exact `.gguf` Filename / Subfolder | Quant Type | Shards | Exact Size (Bytes) | Size (GiB) | Fits `.72` (`314 GiB`) | Fits `.83` (`395 GiB`) |
|---|---|---|---:|---:|---:|---:|---:|
| `heath0xFF/DeepSeek-V4-Flash-0731-REAP-GGUF` | `DeepSeek-V4-Flash-0731-REAP-K160-Q2_K.gguf` | `Q2_K` (K160 REAP) | 1 | `65,426,101,024` | `60.93` | YES | YES |
| `heath0xFF/DeepSeek-V4-Flash-0731-REAP-GGUF` | `DeepSeek-V4-Flash-0731-REAP-K160-MXFP4.gguf` | `MXFP4` (K160 REAP) | 1 | `101,155,861,504` | `94.21` | YES | YES |
| `heath0xFF/DeepSeek-V4-Flash-0731-REAP-GGUF` | `DeepSeek-V4-Flash-0731-REAP-K160-Q4_K_M.gguf` | `Q4_K_M` (K160 REAP) | 1 | `108,997,623,872` | `101.51` | YES | YES |
| `heath0xFF/DeepSeek-V4-Flash-0731-REAP-K216-GGUF` | `DeepSeek-V4-Flash-0731-REAP-K216-UD-IQ3_XXS-00001-of-00003.gguf` | `UD-IQ3_XXS` (K216 REAP) | 3 | `89,065,420,996` | `82.95` | YES | YES |
| `alesha-pro/DeepSeek-V4-Flash-0731-REAP-K160-GGUF` | `DeepSeek-V4-Flash-0731-REAP-K160-Q3_K_Q4_K-00001-of-00004.gguf` | `Q3_K_Q4_K` (K160) | 4 | `89,926,231,168` | `83.75` | YES | YES |
| `Tinker-Stack/DeepSeek-V4-Flash-0731-REAP-150b-TQ3_4S-GGUF` | `DeepSeek-V4-Flash-0731-REAP-150b-TQ3_4S.gguf` | `TQ3_4S` (150B REAP) | 1 | `65,508,564,192` | `61.01` | YES | YES |
| `mmnga-o/DeepSeek-V4-Flash-0731-REAP-W-gguf` | `UD-Q2_K_XL-E216-W1280/DeepSeek-V4-Flash-0731-UD-Q2_K_XL-00001-of-00003.gguf` | `UD-Q2_K_XL` (E216) | 3 | `54,991,304,960` | `51.21` | YES | YES |
| `mmnga-o/DeepSeek-V4-Flash-0731-REAP-W-gguf` | `UD-Q2_K_XL-E256-W1280/DeepSeek-V4-Flash-0731-UD-Q2_K_XL-00001-of-00003.gguf` | `UD-Q2_K_XL` (E256) | 3 | `63,166,927,360` | `58.83` | YES | YES |
| `ioworker0/DeepSeek-V4-Flash-0731-REAP` | `DeepSeek-V4-Flash-0731-REAP-K160-00001-of-00003.gguf` .. `K224` | `MXFP4/Q2` (K160–K224) | 3 | `56,146,564,704` – `73,741,680,224` | `52.29` – `68.68` | YES | YES |
| `lovedheart/DeepSeek-V3.2-REAP-345B-A37B-GGUF-Experimental` | `Q2_K/DeepSeek-V3.2-REAP-345B-A37B-Q2_K-00001-of-00236.gguf` | `Q2_K` | 236 | `123,153,294,784` | `114.70` | YES | YES |

### 3.3 `GLM-4.6-REAP-268B` / `218B`, `GLM-4.7-REAP-268B` / `218B`, & `GLM-5.2-REAP-504B` GGUFs

| Repository ID | Exact `.gguf` Filename / Subfolder | Quant Type | Shards | Exact Size (Bytes) | Size (GiB) | Fits `.72` (`314 GiB`) | Fits `.83` (`395 GiB`) |
|---|---|---|---:|---:|---:|---:|---:|
| `unsloth/GLM-4.6-REAP-268B-A32B-GGUF` | `UD-IQ1_S/GLM-4.6-REAP-268B-A32B-UD-IQ1_S-00001-of-00002.gguf` | `UD-IQ1_S` | 2 | `74,185,013,344` | `69.09` | YES | YES |
| `unsloth/GLM-4.6-REAP-268B-A32B-GGUF` | `UD-IQ2_XXS/GLM-4.6-REAP-268B-A32B-UD-IQ2_XXS-00001-of-00002.gguf` | `UD-IQ2_XXS` | 2 | `89,148,889,184` | `83.03` | YES | YES |
| `unsloth/GLM-4.6-REAP-268B-A32B-GGUF` | `Q2_K/GLM-4.6-REAP-268B-A32B-Q2_K-00001-of-00002.gguf` | `Q2_K` | 2 | `98,719,828,032` | `91.94` | YES | YES |
| `unsloth/GLM-4.6-REAP-268B-A32B-GGUF` | `UD-Q2_K_XL/GLM-4.6-REAP-268B-A32B-UD-Q2_K_XL-00001-of-00003.gguf` | `UD-Q2_K_XL` | 3 | `103,028,758,752` | `95.95` | YES | YES |
| `unsloth/GLM-4.6-REAP-268B-A32B-GGUF` | `UD-Q3_K_XL/GLM-4.6-REAP-268B-A32B-UD-Q3_K_XL-00001-of-00003.gguf` | `UD-Q3_K_XL` | 3 | `120,263,620,832` | `112.00` | YES | YES |
| `unsloth/GLM-4.6-REAP-268B-A32B-GGUF` | `Q3_K_M/GLM-4.6-REAP-268B-A32B-Q3_K_M-00001-of-00003.gguf` | `Q3_K_M` | 3 | `128,744,163,552` | `119.90` | YES | YES |
| `unsloth/GLM-4.6-REAP-268B-A32B-GGUF` | `IQ4_XS/GLM-4.6-REAP-268B-A32B-IQ4_XS-00001-of-00003.gguf` | `IQ4_XS` | 3 | `143,746,009,312` | `133.87` | YES | YES |
| `unsloth/GLM-4.6-REAP-268B-A32B-GGUF` | `UD-Q4_K_XL/GLM-4.6-REAP-268B-A32B-UD-Q4_K_XL-00001-of-00004.gguf` | `UD-Q4_K_XL` | 4 | `153,852,152,128` | `143.29` | YES | YES |
| `unsloth/GLM-4.6-REAP-268B-A32B-GGUF` | `Q4_K_M/GLM-4.6-REAP-268B-A32B-Q4_K_M-00001-of-00004.gguf` | `Q4_K_M` | 4 | `162,527,234,336` | `151.37` | YES | YES |
| `unsloth/GLM-4.6-REAP-268B-A32B-GGUF` | `Q5_K_M/GLM-4.6-REAP-268B-A32B-Q5_K_M-00001-of-00004.gguf` | `Q5_K_M` | 4 | `190,818,920,800` | `177.71` | YES | YES |
| `unsloth/GLM-4.6-REAP-268B-A32B-GGUF` | `Q6_K/GLM-4.6-REAP-268B-A32B-Q6_K-00001-of-00005.gguf` | `Q6_K` | 5 | `220,850,813,344` | `205.68` | YES | YES |
| `unsloth/GLM-4.6-REAP-268B-A32B-GGUF` | `Q8_0/GLM-4.6-REAP-268B-A32B-Q8_0-00001-of-00006.gguf` | `Q8_0` | 6 | `285,764,754,016` | `266.14` | YES | YES |
| `mradermacher/GLM-4.6-REAP-268B-A32B-Derestricted-i1-GGUF` | `GLM-4.6-REAP-268B-A32B-Derestricted.i1-Q3_K_M.gguf` | `i1-Q3_K_M` (Uncensored) | 1 | `128,379,762,624` | `119.56` | YES | YES |
| `mradermacher/GLM-4.6-REAP-268B-A32B-Derestricted-i1-GGUF` | `GLM-4.6-REAP-268B-A32B-Derestricted.i1-IQ4_XS.gguf` | `i1-IQ4_XS` (Uncensored) | 1 | `143,300,712,384` | `133.46` | YES | YES |
| `mradermacher/GLM-4.6-REAP-268B-A32B-Derestricted-i1-GGUF` | `GLM-4.6-REAP-268B-A32B-Derestricted.i1-Q4_K_M.gguf` | `i1-Q4_K_M` (Uncensored) | 1 | `162,258,986,944` | `151.12` | YES | YES |
| `mradermacher/GLM-4.6-REAP-268B-A32B-Derestricted-i1-GGUF` | `GLM-4.6-REAP-268B-A32B-Derestricted.i1-Q5_K_M.gguf` | `i1-Q5_K_M` (Uncensored) | 1 | `190,584,956,864` | `177.50` | YES | YES |
| `mradermacher/GLM-4.6-REAP-268B-A32B-Derestricted-i1-GGUF` | `GLM-4.6-REAP-268B-A32B-Derestricted.i1-Q6_K.gguf` | `i1-Q6_K` (Uncensored) | 1 | `220,681,299,904` | `205.53` | YES | YES |
| `bartowski/cerebras_GLM-4.7-REAP-218B-A32B-GGUF` | `cerebras_GLM-4.7-REAP-218B-A32B-Q4_K_M/cerebras_GLM-4.7-REAP-218B-A32B-Q4_K_M-00001-of-00004.gguf` | `Q4_K_M` | 4 | `133,841,155,776` | `124.65` | YES | YES |
| `unsloth/GLM-4.7-REAP-218B-A32B-GGUF` | `UD-Q4_K_XL/GLM-4.7-REAP-218B-A32B-UD-Q4_K_XL-00001-of-00003.gguf` | `UD-Q4_K_XL` | 3 | `124,916,913,888` | `116.34` | YES | YES |
| `mradermacher/GLM-4.7-REAP-268B-A32B-i1-GGUF` | `GLM-4.7-REAP-268B-A32B.i1-Q4_K_M.gguf` | `i1-Q4_K_M` | 1 | `162,258,986,976` | `151.12` | YES | YES |
| `0xSero/GLM-5.2-REAP-504B-GGUF` | `GLM-5.2-REAP-504B-Q2_K_XL-00001-of-00005.gguf` | `Q2_K_XL` | 5 | `210,750,157,472` | `196.28` | YES | YES |
| `0xSero/GLM-5.2-REAP-504B-GGUF` | `GLM-5.2-REAP-504B-Q3_K_XL-00001-of-00006.gguf` | `Q3_K_XL` | 6 | `264,805,848,384` | `246.62` | YES | YES |
| `0xSero/GLM-5.2-REAP-504B-GGUF` | `GLM-5.2-REAP-504B-Q4_K_XL-00001-of-00008.gguf` | `Q4_K_XL` | 8 | `330,680,994,848` | `307.97` | NO | YES |
| `pipenetwork/GLM-5.2-REAP50-Q2_K-GGUF` | `GLM-5.2-REAP50-Q2_K-00001-of-00004.gguf` | `Q2_K` (REAP50) | 4 | `138,991,139,296` | `129.45` | YES | YES |
| `pipenetwork/GLM-5.2-REAP50-Q3_K_M-GGUF` | `GLM-5.2-REAP50-Q3_K_M-00001-of-00005.gguf` | `Q3_K_M` (REAP50) | 5 | `181,814,784,128` | `169.33` | YES | YES |

### 3.4 `Qwen3-Coder-REAP-363B-A35B` & `246B-A35B` GGUFs

| Repository ID | Exact `.gguf` Filename / Subfolder | Quant Type | Shards | Exact Size (Bytes) | Size (GiB) | Fits `.72` (`314 GiB`) | Fits `.83` (`395 GiB`) |
|---|---|---|---:|---:|---:|---:|---:|
| `noctrex/Qwen3-Coder-REAP-246B-A35B-MXFP4_MOE-GGUF` | `Qwen3-Coder-REAP-246B-A35B-MXFP4_MOE-00001-of-00007.gguf` | `MXFP4_MOE` | 7 | `137,225,283,168` | `127.80` | YES | YES |
| `unsloth/Qwen3-Coder-REAP-363B-A35B-GGUF` | `UD-IQ1_S/Qwen3-Coder-REAP-363B-A35B-UD-IQ1_S-00001-of-00003.gguf` | `UD-IQ1_S` | 3 | `104,410,983,936` | `97.24` | YES | YES |
| `unsloth/Qwen3-Coder-REAP-363B-A35B-GGUF` | `UD-Q2_K_XL/Qwen3-Coder-REAP-363B-A35B-UD-Q2_K_XL-00001-of-00003.gguf` | `UD-Q2_K_XL` | 3 | `138,552,405,504` | `129.04` | YES | YES |
| `unsloth/Qwen3-Coder-REAP-363B-A35B-GGUF` | `UD-Q3_K_XL/Qwen3-Coder-REAP-363B-A35B-UD-Q3_K_XL-00001-of-00004.gguf` | `UD-Q3_K_XL` | 4 | `162,342,760,032` | `151.19` | YES | YES |
| `unsloth/Qwen3-Coder-REAP-363B-A35B-GGUF` | `Q3_K_M/Qwen3-Coder-REAP-363B-A35B-Q3_K_M-00001-of-00004.gguf` | `Q3_K_M` | 4 | `173,488,842,400` | `161.57` | YES | YES |
| `unsloth/Qwen3-Coder-REAP-363B-A35B-GGUF` | `IQ4_XS/Qwen3-Coder-REAP-363B-A35B-IQ4_XS-00001-of-00004.gguf` | `IQ4_XS` | 4 | `197,090,864,768` | `183.56` | YES | YES |
| `unsloth/Qwen3-Coder-REAP-363B-A35B-GGUF` | `UD-Q4_K_XL/Qwen3-Coder-REAP-363B-A35B-UD-Q4_K_XL-00001-of-00005.gguf` | `UD-Q4_K_XL` | 5 | `208,988,794,624` | `194.64` | YES | YES |
| `unsloth/Qwen3-Coder-REAP-363B-A35B-GGUF` | `Q4_K_M/Qwen3-Coder-REAP-363B-A35B-Q4_K_M-00001-of-00005.gguf` | `Q4_K_M` | 5 | `219,307,470,592` | `204.25` | YES | YES |
| `unsloth/Qwen3-Coder-REAP-363B-A35B-GGUF` | `Q5_K_M/Qwen3-Coder-REAP-363B-A35B-Q5_K_M-00001-of-00006.gguf` | `Q5_K_M` | 6 | `257,473,310,624` | `239.79` | YES | YES |
| `unsloth/Qwen3-Coder-REAP-363B-A35B-GGUF` | `Q6_K/Qwen3-Coder-REAP-363B-A35B-Q6_K-00001-of-00007.gguf` | `Q6_K` | 7 | `298,024,515,584` | `277.56` | Borderline | YES |
| `unsloth/Qwen3-Coder-REAP-363B-A35B-GGUF` | `Q8_0/Qwen3-Coder-REAP-363B-A35B-Q8_0-00001-of-00008.gguf` | `Q8_0` | 8 | `385,956,030,272` | `359.45` | NO | YES |

### 3.5 `GLM-4.5-Air-REAP-82B-A12B` & `Gemma-4` REAP (`26B-A4B`, `21B-A4B`, `19B-A4B`) GGUFs

| Repository ID | Exact `.gguf` Filename | Quant Type | Shards | Exact Size (Bytes) | Size (GiB) | Status on `.72`/`.83` |
|---|---|---|---:|---:|---:|---|
| `jtl11/GLM-4.5-Air-REAP-82B-A12B-heretic-GGUF` | `GLM-4.5-Air-REAP-82B-A12B-heretic-Q4_K_M.gguf` | `Q4_K_M` (Heretic) | 1 | `53,918,977,760` | `50.22` | **Already on `.83`** (`/opt/models/glm-4.5-air-reap-82b-heretic`) |
| `bartowski/cerebras_GLM-4.5-Air-REAP-82B-A12B-GGUF` | `cerebras_GLM-4.5-Air-REAP-82B-A12B-Q4_K_M-00001-of-00002.gguf` | `Q4_K_M` | 2 | `56,568,477,216` | `52.68` | Fits `.72` & `.83` |
| `mradermacher/GLM-4.5-Air-REAP-82B-A12B-i1-GGUF` | `GLM-4.5-Air-REAP-82B-A12B.i1-IQ4_XS.gguf` | `i1-IQ4_XS` | 1 | `46,245,230,368` | `43.07` | Fits `.72` & `.83` |
| `mradermacher/ArliAI_GLM-4.5-Air-Derestricted_REAP50_Creative-i1-GGUF` | `ArliAI_GLM-4.5-Air-Derestricted_REAP50_Creative.i1-Q4_K_M.gguf` | `i1-Q4_K_M` (REAP50) | 1 | `37,389,744,640` | `34.82` | Fits `.72` & `.83` |
| `noctrex/GLM-4.5-Air-REAP-82B-A12B-MXFP4_MOE-GGUF` | `GLM-4.5-Air-REAP-82B-A12B-i1-MXFP4_MOE-00001-of-00003.gguf` | `i1-MXFP4_MOE` | 3 | `49,783,502,528` | `46.36` | Fits `.72` & `.83` |
| `mradermacher/gemma-4-21b-a4b-it-REAP-heretic-i1-GGUF` | `gemma-4-21b-a4b-it-REAP-heretic.i1-Q4_K_M.gguf` | `i1-Q4_K_M` (Heretic) | 1 | `13,832,450,784` | `12.88` | **Pulled onto `.83` by Agent-165A** |
| `mradermacher/gemma-4-21b-a4b-it-REAP-heretic-i1-GGUF` | `gemma-4-21b-a4b-it-REAP-heretic.i1-Q6_K.gguf` | `i1-Q6_K` (Heretic) | 1 | `18,610,808,544` | `17.33` | Fits `.72` & `.83` |
| `mradermacher/gemma-4-19b-a4b-it-REAP-heretic-i1-GGUF` | `gemma-4-19b-a4b-it-REAP-heretic.i1-Q4_K_M.gguf` | `i1-Q4_K_M` (Heretic) | 1 | `12,291,399,456` | `11.45` | Fits `.72` & `.83` |
| `crucible-labs/Gemma4-26B-A4B-REAP-25-GGUF` | `Gemma4-26B-A4B-REAP-25-Q3_K-mixed.gguf` | `Q3_K-mixed` (REAP25) | 1 | `10,308,983,776` | `9.60` | Fits `.72` & `.83` |
| `techhermit/gemma-4-26B-A4B-it-reap126` | `gemma-4-26b-reap126-pruned-UD-IQ4_NL.gguf` | `UD-IQ4_NL` (REAP126) | 1 | `13,248,943,968` | `12.34` | Fits `.72` & `.83` |

---

## 4. Task 2: Complete `ubergarm` (`ik_llama.cpp` `_R4` & Hybrid `Q8_0` Attention/Shared-Expert) Catalog

> **Technical Note on `ubergarm` `_R4` Quants**:
> - In `ubergarm/DeepSeek-V3-0324-GGUF` and `ubergarm/DeepSeek-R1-0528-GGUF`, `ubergarm` published **explicit offline-repacked `_R4` GGUFs** (`IQ1_S_R4`, `IQ2_K_R4`, `IQ3_K_R4`, `IQ4_KS_R4`, `IQ4_K_R4`), where routed experts (`ffn_*_exps.weight`) are CPU-repacked `_R4` and all attention (`attn_*`) + shared expert (`ffn_*_shexp`) layers are full `Q8_0` for GPU offload on our `16 GiB` T4s.
> - In newer `ubergarm` repos (`DeepSeek-V3.1-Terminus-GGUF`, `DeepSeek-V3.2-Speciale-GGUF`, `GLM-4.5-GGUF`, `GLM-4.6-GGUF`, `GLM-4.7-GGUF`, `GLM-5-GGUF`, `GLM-5.1-GGUF`, `Qwen3-235B-A22B-Instruct-2507-GGUF`, `Qwen3-Coder-480B-A35B-Instruct-GGUF`, `Kimi-K2-Instruct-0905-GGUF`, `Kimi-K2-Thinking-GGUF`, `Kimi-K2.5-GGUF`, `Kimi-K2.6-GGUF`), the exact same `Q8_0` attention/shared-expert + `IQ*_K*` routed-expert recipe is used, and `ik_llama.cpp` repacks the routed experts into `_R4` layout in RAM at startup via `-rtr` (`--run-time-repack`) or offline via `llama-quantize --repack`.
> - **`DeepSeek-V4-Flash` under `author=ubergarm`**: `FOUND NONE` (`0` repos by `ubergarm` for `DeepSeek-V4-Flash`; use `heath0xFF/DeepSeek-V4-Flash-0731-REAP-GGUF` + `ik_llama.cpp -rtr`).

| Repository ID | Quant Variant / Subfolder | `_R4` Mode | Shards | Exact Size (Bytes) | Size (GiB) | Fits `.72` (`314 GiB`) | Fits `.83` (`395 GiB`) |
|---|---|---|---:|---:|---:|---:|---:|
| `ubergarm/DeepSeek-V3-0324-GGUF` | `DeepSeek-V3-0324-IQ1_S_R4/DeepSeek-V3-0324-IQ1_S_R4-00001-of-00003.gguf` | **Pre-Repacked `_R4`** | 3 | `139,809,832,608` | `130.21` | **YES** | **YES** |
| `ubergarm/DeepSeek-V3-0324-GGUF` | `DeepSeek-V3-0324-IQ2_K_R4/DeepSeek-V3-0324-IQ2_K_R4-00001-of-00005.gguf` | **Pre-Repacked `_R4`** | 5 | `242,674,078,304` | `226.01` | **YES** | **YES** |
| `ubergarm/DeepSeek-V3-0324-GGUF` | `DeepSeek-V3-0324-IQ4_K_R4/DeepSeek-V3-0324-IQ4_K_R4-00001-of-00010.gguf` | **Pre-Repacked `_R4`** | 10 | `414,665,708,736` | `386.19` | NO | Borderline |
| `ubergarm/DeepSeek-R1-0528-GGUF` | `IQ1_S_R4/DeepSeek-R1-0528-IQ1_S_R4-00001-of-00003.gguf` | **Pre-Repacked `_R4`** | 3 | `139,809,832,224` | `130.21` | **YES** | **YES** |
| `ubergarm/DeepSeek-R1-0528-GGUF` | `IQ2_K_R4/DeepSeek-R1-0528-IQ2_K_R4-00001-of-00005.gguf` | **Pre-Repacked `_R4`** | 5 | `235,175,484,928` | `219.02` | **YES** | **YES** |
| `ubergarm/DeepSeek-R1-0528-GGUF` | `IQ3_K_R4/DeepSeek-R1-0528-IQ3_K_R4-00001-of-00007.gguf` | **Pre-Repacked `_R4`** | 7 | `323,135,508,960` | `300.94` | Tight (`314 GiB`) | **YES** |
| `ubergarm/DeepSeek-R1-0528-GGUF` | `IQ4_KS_R4/DeepSeek-R1-0528-IQ4_KS_R4-00001-of-00009.gguf` | **Pre-Repacked `_R4`** | 9 | `394,900,050,688` | `367.78` | NO | **YES** |
| `ubergarm/DeepSeek-V3.1-Terminus-GGUF` | `IQ2_KL/DeepSeek-V3.1-Terminus-IQ2_KL-00001-of-00006.gguf` | `-rtr` (`Q8_0` attn/shexp) | 6 | `248,422,184,800` | `231.36` | **YES** | **YES** |
| `ubergarm/DeepSeek-V3.1-Terminus-GGUF` | `IQ3_KS/DeepSeek-V3.1-Terminus-IQ3_KS-00001-of-00007.gguf` | `-rtr` (`Q8_0` attn/shexp) | 7 | `297,386,194,976` | `276.96` | **YES** | **YES** |
| `ubergarm/DeepSeek-V3.1-Terminus-GGUF` | `smol-IQ4_KSS/DeepSeek-V3.1-Terminus-smol-IQ4_KSS-00001-of-00007.gguf` | `-rtr` (`Q8_0` attn/shexp) | 7 | `342,254,643,232` | `318.75` | NO | **YES** |
| `ubergarm/DeepSeek-V3.2-Speciale-GGUF` | `smol-IQ2_KS/DeepSeek-V3.2-Speciale-smol-IQ2_KS-00001-of-00005.gguf` | `-rtr` (`Q8_0` attn/shexp) | 5 | `192,681,904,416` | `179.45` | **YES** | **YES** |
| `ubergarm/DeepSeek-V3.2-Speciale-GGUF` | `IQ3_K/DeepSeek-V3.2-Speciale-IQ3_K-00001-of-00008.gguf` | `-rtr` (`Q8_0` attn/shexp) | 8 | `312,354,044,576` | `290.90` | Borderline | **YES** |
| `ubergarm/GLM-4.5-GGUF` | `IQ2_KL/GLM-4.5-IQ2_KL-00001-of-00003.gguf` | `-rtr` (`Q8_0` attn/shexp) | 3 | `137,175,280,064` | `127.75` | **YES** | **YES** |
| `ubergarm/GLM-4.5-GGUF` | `IQ3_KT/GLM-4.5-IQ3_KT-00001-of-00004.gguf` | `-rtr` (`Q8_0` attn/shexp) | 4 | `158,456,142,336` | `147.57` | **YES** | **YES** |
| `ubergarm/GLM-4.5-GGUF` | `IQ4_KSS/GLM-4.5-IQ4_KSS-00001-of-00004.gguf` | `-rtr` (`Q8_0` attn/shexp) | 4 | `186,546,510,336` | `173.73` | **YES** | **YES** |
| `ubergarm/GLM-4.5-GGUF` | `IQ4_K/GLM-4.5-IQ4_K-00001-of-00005.gguf` | `-rtr` (`Q8_0` attn/shexp) | 5 | `220,938,521,280` | `205.77` | **YES** | **YES** |
| `ubergarm/GLM-4.5-GGUF` | `IQ5_K/GLM-4.5-IQ5_K-00001-of-00006.gguf` | `-rtr` (`Q8_0` attn/shexp) | 6 | `268,762,598,176` | `250.30` | **YES** | **YES** |
| `ubergarm/GLM-4.6-GGUF` | `IQ3_KS/GLM-4.6-IQ3_KS-00001-of-00004.gguf` | `-rtr` (`Q8_0` attn/shexp) | 4 | `159,341,631,904` | `148.40` | **YES** | **YES** |
| `ubergarm/GLM-4.6-GGUF` | `IQ4_KS/GLM-4.6-IQ4_KS-00001-of-00005.gguf` | `-rtr` (`Q8_0` attn/shexp) | 5 | `207,205,849,600` | `192.98` | **YES** | **YES** |
| `ubergarm/GLM-4.7-GGUF` | `IQ3_KS/GLM-4.7-IQ3_KS-00001-of-00005.gguf` | `-rtr` (`Q8_0` attn/shexp) | 5 | `166,674,221,696` | `155.23` | **YES** | **YES** |
| `ubergarm/GLM-4.7-GGUF` | `IQ4_K/GLM-4.7-IQ4_K-00001-of-00006.gguf` | `-rtr` (`Q8_0` attn/shexp) | 6 | `224,889,522,944` | `209.44` | **YES** | **YES** |
| `ubergarm/GLM-5-GGUF` | `smol-IQ1_KT/GLM-5-smol-IQ1_KT-00001-of-00005.gguf` | `-rtr` (`Q8_0` attn/shexp) | 5 | `181,676,195,936` | `169.20` | **YES** | **YES** |
| `ubergarm/GLM-5-GGUF` | `smol-IQ2_KS/GLM-5-smol-IQ2_KS-00001-of-00006.gguf` | `-rtr` (`Q8_0` attn/shexp) | 6 | `220,919,152,896` | `205.75` | **YES** | **YES** |
| `ubergarm/GLM-5-GGUF` | `IQ2_KL/GLM-5-IQ2_KL-00001-of-00007.gguf` | `-rtr` (`Q8_0` attn/shexp) | 7 | `281,317,130,560` | `262.00` | **YES** | **YES** |
| `ubergarm/GLM-5-GGUF` | `IQ3_KS/GLM-5-IQ3_KS-00001-of-00008.gguf` | `-rtr` (`Q8_0` attn/shexp) | 8 | `343,838,474,720` | `320.22` | NO | **YES** |
| `ubergarm/GLM-5.1-GGUF` | `smol-IQ2_KS` / `IQ2_KL` / `IQ3_KS` | `-rtr` (`Q8_0` attn/shexp) | 6–8 | `220,919,154,272` – `343,838,476,096` | `205.75` – `320.22` | Up to `IQ2_KL` | **YES** |
| `ubergarm/Qwen3-235B-A22B-Instruct-2507-GGUF` | `IQ2_KL/Qwen3-235B-A22B-Instruct-IQ2_KL-00001-of-00002.gguf` | `-rtr` (`Q8_0` attn) | 2 | `87,908,530,432` | `81.87` | **YES** | **YES** |
| `ubergarm/Qwen3-235B-A22B-Instruct-2507-GGUF` | `IQ3_K/Qwen3-235B-A22B-Instruct-IQ3_K-00001-of-00003.gguf` | `-rtr` (`Q8_0` attn) | 3 | `114,514,205,024` | `106.65` | **YES** | **YES** |
| `ubergarm/Qwen3-235B-A22B-Instruct-2507-GGUF` | `IQ4_KSS/Qwen3-235B-A22B-Instruct-IQ4_KSS-00001-of-00003.gguf` | `-rtr` (`Q8_0` attn) | 3 | `123,577,416,032` | `115.09` | **YES** | **YES** |
| `ubergarm/Qwen3-235B-A22B-Instruct-2507-GGUF` | `IQ4_K/Qwen3-235B-A22B-Instruct-IQ4_K-00001-of-00003.gguf` | `-rtr` (`Q8_0` attn) | 3 | `144,084,048,256` | `134.19` | **YES** | **YES** |
| `ubergarm/Qwen3-235B-A22B-Instruct-2507-GGUF` | `IQ5_K/Qwen3-235B-A22B-Instruct-IQ5_K-00001-of-00004.gguf` | `-rtr` (`Q8_0` attn) | 4 | `173,653,891,552` | `161.73` | **YES** | **YES** |
| `ubergarm/Qwen3-Coder-480B-A35B-Instruct-GGUF` | `IQ2_KS` / `IQ2_KL` / `IQ3_K` / `IQ4_KSS` / `IQ4_K` | `-rtr` (`Q8_0` attn) | 4–6 | `154,760,295,840` – `293,181,507,200` | `144.13` – `273.05` | **YES** | **YES** |
| `ubergarm/Kimi-K2-Instruct-0905-GGUF` | `smol-IQ1_KT/Kimi-K2-Instruct-0905-smol-IQ1_KT-00001-of-00005.gguf` | `-rtr` (`Q8_0` attn/shexp) | 5 | `235,088,187,680` | `218.94` | **YES** | **YES** |
| `ubergarm/Kimi-K2-Instruct-0905-GGUF` | `smol-IQ2_KS/Kimi-K2-Instruct-0905-smol-IQ2_KS-00001-of-00006.gguf` | `-rtr` (`Q8_0` attn/shexp) | 6 | `290,059,784,640` | `270.14` | **YES** | **YES** |
| `ubergarm/Kimi-K2-Instruct-0905-GGUF` | `IQ2_KS/Kimi-K2-Instruct-0905-IQ2_KS-00001-of-00007.gguf` | `-rtr` (`Q8_0` attn/shexp) | 7 | `311,199,076,928` | `289.83` | Tight (`314 GiB`) | **YES** |
| `ubergarm/Kimi-K2-Instruct-0905-GGUF` | `smol-IQ2_KL/Kimi-K2-Instruct-0905-smol-IQ2_KL-00001-of-00008.gguf` | `-rtr` (`Q8_0` attn/shexp) | 8 | `353,477,661,344` | `329.20` | NO | **YES** |
| `ubergarm/Kimi-K2-Instruct-0905-GGUF` | `IQ2_KL/Kimi-K2-Instruct-0905-IQ2_KL-00001-of-00008.gguf` | `-rtr` (`Q8_0` attn/shexp) | 8 | `384,856,298,144` | `358.43` | NO | **YES** |
| `ubergarm/Kimi-K2.5-GGUF` | `smol-IQ1_KT` (`218.94 GiB`), `smol-IQ2_KS` (`270.14 GiB`), `smol-IQ2_KL` (`329.20 GiB`) | `-rtr` (`Q8_0` attn/shexp) | 6–9 | `235,088,189,856` – `353,477,663,584` | `218.94` – `329.20` | Up to `smol-IQ2_KS` | **YES** |
| `ubergarm/Kimi-K2.6-GGUF` | `smol-IQ1_KT` (`218.94 GiB`), `smol-IQ2_KS` (`270.14 GiB`), `smol-IQ2_KL` (`329.20 GiB`) | `-rtr` (`Q8_0` attn/shexp) | 6–9 | `235,088,190,144` – `353,477,663,872` | `218.94` – `329.20` | Up to `smol-IQ2_KS` | **YES** |

---

## 5. Task 3: Disk Headroom Audit (`df -h`) & Live Pull Receipt

### 5.1 Disk & Memory Headroom Audit on `.72` (`10.0.70.72`) and `.83` (`10.0.70.83`)

| Node | Path | Backing Filesystem | Total | Used | Avail (Pre-Pull) | Avail (Post-Pull) | Use% | RAM (`free -h`) | Pull Policy |
|---|---|---|---:|---:|---:|---:|---:|---|---|
| `.72` (`10.0.70.72`) | `/opt/models` | `/dev/mapper/ubuntu--vg-ubuntu--lv` (`/`) | `5.0T` (`5,449,794,560,000 B`) | `4.7T` | `100G` (`99.53 GiB`) | `100G` (`99.53 GiB`) | `98%` | `314 GiB` (`291 GiB` avail) | **DO NOT PULL** (`98%` full; $< 120\text{ GiB}$ safety floor) |
| `.83` (`10.0.70.83`) | `/mnt/ai-models`, `/opt/models` | `/dev/mapper/ubuntu--vg-ubuntu--lv` (`/`) | `5.0T` (`5,494,122,508,288 B`) | `4.2T` | `829G` (`828.87 GiB`) | `816G` (`815.99 GiB`) | `85%` | `379 GiB` OS (`395 GiB` VM) | **HEADROOM PERMITS** (`816 GiB` free; `/opt/models/Uniboshi--Kimi-K3-Abliterated-V1` intact at `1.3T`) |

*(Note: `/mnt/models-d2` is not a separate mounted filesystem on `.83`; the `Uniboshi/Kimi-K3-Abliterated-V1` 96-shard Safetensors directory resides at `/opt/models/Uniboshi--Kimi-K3-Abliterated-V1` on `.83` and was verified untouched before and after the pull.)*

### 5.2 Live Pre-Quantized REAP GGUF Pull Receipt on `.83`

- **Pulled Repository**: `mradermacher/gemma-4-21b-a4b-it-REAP-heretic-i1-GGUF` (complementing `GLM-4.5-Air-REAP-82B-A12B-heretic-Q4_K_M.gguf` `50.22 GiB` already present at `/opt/models/glm-4.5-air-reap-82b-heretic` on `.83`)
- **Destination File on `.83`**: `/opt/models/gemma-4-21b-a4b-it-reap-heretic/gemma-4-21b-a4b-it-REAP-heretic.i1-Q4_K_M.gguf`
- **Verified Exact Byte Size**: `13,832,450,784 bytes` (`12.88 GiB` — 100% match with Hugging Face API `size: 13832450784`)
- **Average Transfer Speed**: `98 MiB/s` (`aria2c -x 16 -s 16` with `LD_PRELOAD=/opt/models/libnofsync.so`, status `OK`)
- **Staged Multi-Target Download Script**: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/pull_reap_and_ubergarm_r4_83.sh` (NFS-accessible on `.83` at `/mnt/bd/bd-persist/harness-work/O1789/infra-upgrade/pull_reap_and_ubergarm_r4_83.sh`) supporting one-command pulls for:
  - `gemma4-reap` (`12.88 GiB` — **COMPLETED**)
  - `dsv4-flash-reap-q4` (`heath0xFF/DeepSeek-V4-Flash-0731-REAP-GGUF` `K160-Q4_K_M`, `101.51 GiB`)
  - `dsv4-flash-reap-iq3` (`heath0xFF/DeepSeek-V4-Flash-0731-REAP-K216-GGUF` `K216-UD-IQ3_XXS`, `82.95 GiB`)
  - `glm46-reap-derest` (`mradermacher/GLM-4.6-REAP-268B-A32B-Derestricted-i1-GGUF` `i1-Q4_K_M`, `151.12 GiB`)
  - `glm46-reap-unsloth` (`unsloth/GLM-4.6-REAP-268B-A32B-GGUF` `UD-Q4_K_XL`, `143.29 GiB`)
  - `qwen3-coder-reap363` (`unsloth/Qwen3-Coder-REAP-363B-A35B-GGUF` `UD-Q4_K_XL`, `194.64 GiB`)
  - `ubergarm-dsv3-r4` (`ubergarm/DeepSeek-V3-0324-GGUF` `DeepSeek-V3-0324-IQ2_K_R4`, `226.01 GiB`)
  - `ubergarm-dsr1-r4` (`ubergarm/DeepSeek-R1-0528-GGUF` `IQ2_K_R4`, `219.02 GiB`)
  - `ubergarm-qwen235b` (`ubergarm/Qwen3-235B-A22B-Instruct-2507-GGUF` `IQ4_KSS`, `115.09 GiB`)
  - `ubergarm-glm45` (`ubergarm/GLM-4.5-GGUF` `IQ4_KSS`, `173.73 GiB`)
  - `ubergarm-glm5` (`ubergarm/GLM-5-GGUF` `smol-IQ2_KS`, `205.75 GiB`)
  - `ubergarm-kimik2` (`ubergarm/Kimi-K2-Instruct-0905-GGUF` `smol-IQ2_KS`, `270.14 GiB`)
