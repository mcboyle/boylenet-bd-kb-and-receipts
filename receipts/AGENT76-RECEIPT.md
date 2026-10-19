# AGENT-76 EXECUTION RECEIPT — `bd-local-kv-calc`

- **Agent**: Agent-76 (Fast 2-Turn Builder)
- **Timestamp (UTC)**: `2026-10-17T23:40:30Z`
- **Executable**: `/home/mboyle/bin/bd-local-kv-calc` (`chmod +x`, `rc=0`)
- **SHA-256**: `5f1d14e0ad91f0fddcbb9ab98d8365a4caa1d2d6fcb0cf106f07751f9333809c`
- **Cluster Target**: `4x NVIDIA Tesla T4` (`59,648 MiB` usable GDDR6 VRAM) + `1,730 GiB` Locked DDR4 RAM
- **KV Quantization Modes**: `FP16` (`2.000 B/elem`), `Q8_0` (`1.0625 B/elem`, GGML blk32), `Q4_0` (`0.5625 B/elem`, GGML blk32)

## Live Output (`bd-local-kv-calc --ctx 16384`)

```text
====================================================================================================================================
BOYLENET KV-CACHE & CONCURRENCY CALCULATOR | 4x Tesla T4 (59,648 MiB GDDR6) + 1,730 GiB Locked DDR4 | Slot Ctx: 16,384 tok
CUDA/Compute Reserve: 2,048 MiB | KV Element Sizes: FP16=2.000 B, Q8_0=1.0625 B (GGML blk32), Q4_0=0.5625 B (GGML blk32)
====================================================================================================================================
Tier | Model                               | Arch      |  VRAM Wt | DDR4 Wt |  KV Pool |    FP16 Slot / N |    Q8_0 Slot / N |    Q4_0 Slot / N
------------------------------------------------------------------------------------------------------------------------------------
T1   | Qwen2.5-Coder-32B-Instruct-Q4_K_M   | GQA       |   19200M |      0G |   38400M |    4096M / p=9   |    2176M / p=17  |    1152M / p=33 
T1   | DeepSeek-R1-Distill-Qwen-32B-Q4_K_M | GQA       |   19200M |      0G |   38400M |    4096M / p=9   |    2176M / p=17  |    1152M / p=33 
T1   | Qwen3-30B-A3B-Q4_K_M                | MoE-GQA   |   17920M |      0G |   39680M |    1536M / p=25  |     816M / p=48  |     432M / p=91 
T1   | Qwen2.5-Coder-14B-Instruct-Q8_0     | GQA       |   15100M |      0G |   42500M |    3072M / p=13  |    1632M / p=26  |     864M / p=49 
T1   | Qwen2.5-Coder-7B-Instruct-Q8_0      | GQA       |    7800M |      0G |   49800M |     896M / p=55  |     476M / p=104 |     252M / p=197
------------------------------------------------------------------------------------------------------------------------------------
T2   | Llama-3.3-70B-Instruct-Q4_K_M       | GQA       |   41472M |      0G |   16128M |    5120M / p=3   |    2720M / p=5   |    1440M / p=11 
T2   | Qwen2.5-72B-Instruct-Q4_K_M         | GQA       |   42200M |      0G |   15400M |    5120M / p=3   |    2720M / p=5   |    1440M / p=10 
T2   | Qwen3-235B-A22B-Q4_K_M              | MoE-GQA   |   18432M |    124G |   39168M |    3008M / p=13  |    1598M / p=24  |     846M / p=46 
------------------------------------------------------------------------------------------------------------------------------------
T3   | DeepSeek-V3-0324-Q4_K_M             | MoE-MLA   |   16384M |    385G |   41216M |    1098M / p=37  |     583M / p=70  |     309M / p=133
T3   | DeepSeek-R1-671B-Q4_K_M             | MoE-MLA   |   16384M |    385G |   41216M |    1098M / p=37  |     583M / p=70  |     309M / p=133
T3   | Llama-3.1-405B-Instruct-Q4_K_M      | GQA-Hybrid |   32768M |    215G |   24832M |    8064M / p=3   |    4284M / p=5   |    2268M / p=10 
====================================================================================================================================
```
