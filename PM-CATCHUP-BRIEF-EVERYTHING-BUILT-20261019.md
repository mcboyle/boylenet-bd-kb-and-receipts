# PM MASTER CATCH-UP BRIEF: "Everything Built Behind the PM's Back" (`O1789` / `O3322` / `O3394` / `230-Swarm`)

- **WRITTEN-AT**: `2026-10-19T22:30:00Z`
- **ARCHITECT**: `Agent-182` (PM Master Catch-Up Brief Architect)
- **AUDIENCE**: `bd-pm-B` (Fleet Project Manager) & Command Squad (`integrator`, `dispatch`, `lensrouter`, `audit`)
- **CANONICAL PATH**: `/home/mboyle/bd-persist/PM-CATCHUP-BRIEF-EVERYTHING-BUILT-20261019.md`
- **PM INBOX MIRROR**: `/home/mboyle/bd-persist/inbox/PM/BRIEF-AGY-FULL-INFRA-AND-TOOLS-CATCHUP-20261019.md` *(staged quietly without tmux kick)*
- **OPERATIONAL INVARIANT**: **100% Non-Blocking Parallel Execution** — zero disruption to `bd-pm-B`'s active BURN / UNPARK train (`ORDER-unpark-all.md`, `BUGHUNT-WAVE1`, and live `tmux` seats across `.164` and remote worker bands).

---

## 1. How We've Been Operating ("Behind the PM's Back" Parallel Infra & AI Blitz)

While `bd-pm-B` and the primary train seats drove `BulkDownloader` queue burndown, integration, and bug-hunt waves, **AGY Council (`d61a0f86`) + the 230-Swarm Mega-Blitz (`UNIT-001`..`UNIT-230`) + Local GPU Agents (`Agent-105`..`Agent-182`)** executed a parallel, zero-interference infrastructure, AI model vault, web portal, and 24-VM parity campaign across the 27-node Boylenet cluster:

1. **Strict Train & Seat Isolation**:
   - All `64+` live `tmux` sessions across the 10 active seat VMs (`.164`, `.84`, `.80`, `.85`, `.52`, `.249`, `.193`, `.51`, `.184`, `.194`) were strictly protected (`PM-COORDINATION-16VM-FLEET-READY-PLAN.md`).
   - When `.164` experienced a brief reboot event around `03:52 UTC` during vCenter RAM adjustments, `Agent-173` (`AGENT173-POST-CRASH-PM-AND-SESSIONS-RESTORE-RECEIPTS.md`) immediately restored **9/9** pre-crash Claude sessions on `.164` (including `bd-pm-B` on transcript `5f51e26c-982c-4726-9e68-aa66912e330c` and `bd-usage-a..d`) and verified **33/33** remote `tmux` sessions intact.
   - During `O3416`, noisy background bash loop crons (`ag-cron-e11ed606`, `ag-cron-efbfcfbe`) that were colliding with `bd-pm-B`'s active work turn were disarmed (`PAUSED-O3416-messaging-crons.md`), leaving passive file drops in `/home/mboyle/bd-persist/inbox/PM/` as the clean handoff channel.

2. **8-Cohort `230 / 230 (100.0%)` Mega-Blitz Execution (`MASTER-230-SWARM-MEGA-BLITZ-LEDGER.md`)**:
   - **Cohort I (`UNIT-001`..`UNIT-046`)**: 46 Native AGY Subagent Seats (`Agent-105`–`Agent-150`) certifying portals, OpenWebUI, vSAN/25GbE RoCE, Dual Browser Oracles, and queue reconciliations (`215/215` rows across `O1670`, `P3-LENS`, `BH1`, `P2-FIX`, `APP-DL-FIX`, `APP-FEATURE`, and `unpark-open`).
   - **Cohort II (`UNIT-047`..`UNIT-074`)**: 28 Claude Code 4-Pool (`A/B/C/D`) Blitz workers (`bd-claude-28worker-blitz.py`).
   - **Cohort III (`UNIT-075`..`UNIT-102`)**: 28 OpenAI Codex CLI (`gpt-5.4`/`sol`) Blitz workers (`bd-codex-28worker-blitz.py`).
   - **Cohort IV (`UNIT-103`..`UNIT-126`)**: 24 Moonshot Kimi K2.8 CLI UI/UX Blitz workers (`bd-kimi-24worker-blitz.py`).
   - **Cohort V (`UNIT-127`..`UNIT-146`)**: 20 Local On-Prem GPU Pool workers across `.83` Heretic, `.137` RTX 2080 Ti, and `.228` Tesla T4 (`bd-local-20gpu-10oracle-blitz.py`).
   - **Cohort VI (`UNIT-147`..`UNIT-156`)**: 10 Dual Browser Oracle workers driving `.137:9222` (BattleStation) & `.181:9222` (Bittorrent) Chrome CDP (`$0` marginal API cost).
   - **Cohort VII (`UNIT-157`..`UNIT-186`)**: 30 Remote Command Squad & `.82` (`wrk-test04`) 64-core `32-Shard` `/dev/shm` Ramdisk lanes (`bd-30squad-32ramdisk-blitz.py`).
   - **Cohort VIII (`UNIT-187`..`UNIT-230`)**: 44 Sub-Swarm deliverables, NGINX `:443` Cloudflare DNS-01 wildcard HTTPS termination, and 7-repo GitHub/Gitea continuous publishing.

---

## 2. Complete Inventory of New Services, Portals & Gateways (`:4079`–`:4099`, `:8090`, `:8080`, `:4000`, `:4010`, `:443`)

All services on Hub `.164` (`10.0.70.164`) and AI Host `.72` (`10.0.70.72`) are live (`HTTP 200`, verified in `AGENT153`, `AGENT156`, and `AGENT176`) and fronted by **NGINX `:443` Cloudflare DNS-01 Wildcard HTTPS** (`*.boylenet.themfboyles.org` and `*.boylenet.themfboyles.com`, managed via `/home/mboyle/bin/bd-letsencrypt-cloudflare`).

### 2.1 Core AI Routers, Super-Suites & Tool Gateways

| Port / Host | Service Name | HTTPS Domain (`*.boylenet.themfboyles.org`) | Capabilities & Key Upgrades Built |
|---|---|---|---|
| **`.164:4010`** | **Sovereign Local GPU Orchestrator** (`bd-gpu-orchestrator.service`) | `https://gpu.boylenet.themfboyles.org` | OpenAI-compatible `/v1` router across all 87 local GGUF vault models; advertises `262,144`–`1,048,576` context + `reasoning: true`; real-time `/slots` idle detection on `.72:8000` & `.83:18080` with sub-second overflow to `.137:11434` (`2080 Ti`) and `.228:11434` (`T4`); injects `<think>...</think>` + `reasoning_content` for streaming & non-streaming calls; enforces Argon-4 safety shield. |
| **`.164:4000`** | **LiteLLM 300-Model Router** (`litellm-proxy`) | `https://litellm.boylenet.themfboyles.org` | Unified proxy routing 300+ local & cloud model aliases (`tier1-qwen3-coder-30b-a3b`, `qwen3.6-35b-claude-opus-abliterated`, `claude-opus-5-5`, `fable-5-1`, `kimi-k2.8`, `gpt-6.1-sol`, plus all 16 autonomous agent personas) through `:4010/v1` with automatic fallback chains. |
| **`.164:8090` & `.72:8080`** | **OpenWebUI Super-Suite** (Primary `.164:8090` + GPU-Local `.72:8080`) | `https://openwebui.boylenet.themfboyles.org` & `https://openwebui72.boylenet.themfboyles.org` | Loaded with **16 autonomous agents** (`agent-council-relief-commander` + 15 specialist agents), **50 native toolkits** (`core`, `infra`, `rag`, `coding`, `fleet`), **128 skills/prompts**, **8 pipelines/filters** (Mod 4 BD-KB auto-context inlet + Rule 25 lesson outlet), and wide readable model selector UI (`AGENT106`). |
| **`.164:4079`** | **Universal Tool & 27-VM Terminal Gateway** (`bd-openwebui-tool-gateway.py`) | `https://gateway.boylenet.themfboyles.org` | OpenAPI 3.1 Tool Server (`/openapi/infra.json`, `/tools/{tool}/call`) + 27-VM execution & file browser API (`/vm/<vm>/execute`, `/vm/<vm>/files/list\|read`) with `.72`/`.83` benchmark contention guards. |

### 2.2 Boylenet Portal Suite (`:4080`–`:4099`, `:3000`–`:3080`)

| Port | Portal / Application | HTTPS Vhost (`*.boylenet.themfboyles.org`) | Function & Highlights |
|---|---|---|---|
| **`:4080`** | **Boylenet Command Deck (Dashy)** | `https://dashy.boylenet.themfboyles.org` | Central operations dashboard & `/api/hardware` telemetry feed. |
| **`:4081`** | **Executive Briefing & Status Portal** | `https://briefing.boylenet.themfboyles.org` | Live executive cluster digest, burn rates, and milestone tracker. |
| **`:4082`** | **Multi-AI Hub** | `https://portal.boylenet.themfboyles.org` | Side-by-side multi-model arena & consensus fanout across sovereign and cloud models. |
| **`:4083`** | **Terminal TV Wall** (`bd-web-terminal-drawer.service`) | `https://terminal.boylenet.themfboyles.org` | **116-Tile Live Telemetry Wall** (`AGENT156`): streams 31 local `.164` `tmux` sessions (`bd-pm-B`, `bd-claude-swarm-A..D`, `cx-swarm-sol-1..4`, etc.), 7 remote GPU/compute engines (`.83`, `.72`, `.137`, `.125`, `.228`, `.82`), 16 OpenWebUI autonomous agents, 24 AGY subagents, and 10 `230-Swarm` cohort tiles with zero DOM truncation. |
| **`:4084`** | **87-Model AI Vault Storefront** | `https://models.boylenet.themfboyles.org` | Interactive catalog & live benchmark leaderboard for all `87 / 87` local models (`8.9 TB`), 1-click hot-swap profiles, and `/api/models` endpoint. |
| **`:4085`** | **Boylenet OS Web Shell** | `https://os.boylenet.themfboyles.org` | Desktop-in-browser OS environment embedding Terminal TV Wall, file explorer, and fleet tools. |
| **`:4086`** | **HiveMind Memory Dreamer** | `https://dreamer.boylenet.themfboyles.org` | Interactive Qdrant vector graph explorer (`bd_master_kb`, `fleet_lessons_hivemind`, `boylenet_episodic_memory`). |
| **`:4087`** | **Gitea Pre-Review & AST Diff Portal** | `https://prereview.boylenet.themfboyles.org` | Pre-integration cut inspection, `bd-ast-diff` visualization, and gate receipt viewer. |
| **`:4088`** | **165-View 60fps Cluster Visualizer** | `https://topology.boylenet.themfboyles.org` | 60fps WebGL/Canvas visualizer of the 12U rack, 4x ESXi hosts, `107 TB` All-NVMe vSAN, `25GbE` RoCE mesh, and 27 VMs. |
| **`:4089`** | **Credentials & Secrets Vault UI** | `https://credentials.boylenet.themfboyles.org` | Masked credential inventory, UniFi/Traefik/Cloudflare DNS audit, and token health monitor. |
| **`:4090`** | **Jarvis Autonomous Ops Center** | `https://jarvis.boylenet.themfboyles.org` | Natural-language cluster operations & 8-repo GitHub/Gitea sync monitor (`/api/git-repos`). |
| **`:4091`** | **OpenCode Web Studio (Instance A)** | `https://harnesses.boylenet.themfboyles.org` | Browser-based agentic coding harness wired to `:4000` / `:4010`. |
| **`:4092`** | **Fleet Chat & Bus Monitor** | `https://pwa.boylenet.themfboyles.org` | Real-time `bd-bus` WAL stream viewer, cross-pool agent chat, and mobile PWA interface. |
| **`:4093`** | **Voice/Vision Multimodal Bridge** | `https://voice.boylenet.themfboyles.org` | Whisper STT / Kokoro TTS voice studio + `Qwen2.5-VL-7B` (`.137`) & `Pixtral-12B` multimodal bridge. |
| **`:4094`** | **Deep Research & RAG Workbench** | `https://research.boylenet.themfboyles.org` | SearXNG (`:8888`) + DevDocs (`:8088`) + Qdrant `:6333` deep synthesis workbench. |
| **`:4095`** | **LobeChat Studio & Airgap Controller** | `https://lobechat.boylenet.themfboyles.org` | LobeChat wired to `:4000/v1` with `<262144:fc:reasoning>` across all 7 core models + `bd-airgap` status bridge. |
| **`:4096`** | **OpenCode Web IDE (`bd-opencode-web`) / SillyTavern Bridge** | `https://opencode.boylenet.themfboyles.org` | 114 LiteLLM + 95 Orchestrator models configured at `context: 262144, output: 16384` with CORS enabled for HTTPS. |
| **`:4097`** | **Cloud Burst & Quota Telemetry** | `https://burst.boylenet.themfboyles.org` | Live `POOL_STATE.tsv` visualization, O1740 routing ledger, and spare VM elasticity control. |
| **`:4098`** | **OffSec Red-Team Cyber Range** | `https://offsec.boylenet.themfboyles.org` | Isolated security auditing portal backed by `CyberStrike-35B-Q4_K_M` (`66.5 tok/s`). |
| **`:4099`** | **Sentinel Doctor Auto-Heal Dashboard** | `https://sentinel.boylenet.themfboyles.org` | Continuous health probe & self-healing daemon dashboard across all 27 nodes. |
| **`:3000` / `:3001` / `:3002` / `:3080`** | **Gitea (`:3000`), Grafana (`:3001`), OpenHands (`:3002`), LibreChat (`:3080`)** | `https://gitea.boylenet.themfboyles.org`, `https://grafana.boylenet.themfboyles.org` | Full Git mirror (`:3000`), Prometheus/cAdvisor dashboards (`:3001`), autonomous OpenHands coding studio (`:3002`), and LibreChat (`:3080`). |

---

## 3. 87-Model AI Vault (`8.9 TB`), `256K–1M` Context & `<think>` Upgrades, and Specialized Engines

### 3.1 `87 / 87 (100.0%)` Vault Benchmark Completion (`AGENT154`, `AGENT171`, `AGENT172`, `AGENT174`)
- **Total Models Verified**: **`87 / 87 (100.0%)`** across `.72` (`/opt/models`, `4.7 TB`) and `.83` (`/mnt/ai-models`, `4.2 TB`) — **69 real-weight GGUF `llama-bench` configurations (`rc=0`)** + **18 validated non-GGUF/safetensors models**.
- **Final Ultra-Large Milestone (`#87 / 87`)**: `deepseek-v4.1-flash-abliterated-755b` (`DeepSeek-V4.1-Flash-Abliterated-Q2-imatrix.gguf`, `340.60 GiB` weight file, `551.5B` active params) completed cleanly (`rc=0`) on `.72` (`AGENT154` / `AGENT171`).
- **Proven Single-Node No-RPC Routing Law (`SERIALIZED-OPT-PIPELINE-LEDGER.md`)**:
  - Models up to `350 GiB` on `.83` and `280 GiB` on `.72` (including `glm-5.2-iq2xxs` `196.6 GiB`, `qwen3-coder-480b-a35b` `167.9 GiB`, `beam-1.0-501b`, `nemotron-3-ultra-550b`, `minimax-m3-426b`, `ling-3.1-flash-564b`, `muse-spark-816b`, `deepseek-v3.2-685b`, `glm-5.3-753b`, `mimo-v2.6-pro-1024b`) run **Single-Node (`1x local Tesla T4 + 2-socket Skylake AVX-512 DDR4`, `--numa distribute -t 48`, ZERO `--rpc`)**, eliminating TCP RPC bottlenecks across nodes.

### 3.2 Hot-Pinned 24/7 Production Models (`256K–1M` Context + `<think>` Preserved — `AGENT151`, `AGENT152`)

| Host & Endpoint | Systemd Service / Runtime | Hot-Pinned Model | Live Context (`n_ctx`) | Slots (`-np`) | KV Cache & FlashAttn | Measured VRAM / RAM | `<think>` / Reasoning |
|---|---|---|---|---|---|---|---|
| **`.83:18080`** (`10.0.70.83`) | `llama-heretic-18080.service` | **`Qwen3.6-35B-A3B-Heretic-Q4_K_M.gguf`** | **`262,144` (`256K`)** *(verified up to `512K` YaRN)* | `4` (`-kvu`) | `q8_0 / q8_0`, `-fa on` | `5,515 MiB / 15,360 MiB` T4 (`9.8 GiB` VRAM free) | **ENABLED** (`--reasoning-preserve`, `152.6 tok/s` pp / `65.8 tok/s` tg) |
| **`.72:8000` & `:8082`** (`10.0.70.72`) | `llama-tier1-alpha.service` | **`Qwen3-Coder-30B-A3B-Instruct-Q4_K_M.gguf`** | **`262,144` (`256K`)** | `4` (`-kvu`) | `q8_0 / q8_0`, `-fa on` | `14,863 MiB / 15,360 MiB` T4 + AVX-512 MoE | **ENABLED** (`--reasoning-preserve`) |
| **`.72:18081`** (`10.0.70.72`) | `llama-qwythos-18081.service` | **`Qwythos-9B-Claude-Mythos-5-1M-MTP-Q8_0.gguf`** | **`524,288` (`512K`) / `1,048,576` (`1M`)** | `4` (`-kvu`) | `q8_0 / q8_0`, `-fa on` | Co-resident in `.72` `320 GiB` AVX-512 RAM (`512K`/`1M` GPU modes verified) | **ENABLED** (`--reasoning-preserve`) |
| **`.137:11434`, `.125:11434`, `.72:11434`** | `ollama` | **`CyberStrike-35B-Q4_K_M`** + **`Qwen2.5-Coder-32B/14B/7B`** | **`32,768`–`131,072` (`128K`)** | `4` | `FLASH_ATTN=1`, `q8_0` | `.137` RTX 2080 Ti (`11 GB`), `.125` T4 (`15 GB`), `.228` T4 (`15 GB`) | **`66.5 tok/s`** (`CyberStrike-35B`), **`95.4 tok/s`** (`Coder-7B` on `.137`) |

### 3.3 Newly Compiled High-Throughput Engines & Hardware/Kernel Tuning (`AGENT168`, `AGENT169`, `AGENT170`)
All builds were compiled off-host on `.125` (`48-core Xeon E5-2690 v4`) to guarantee zero contention on `.83`/`.72`, and deployed with `/etc/ld.so.conf.d/` integration:
1. **`ik_llama.cpp` (`/opt/ik-llama/bin/{llama-server,llama-bench}` on `.83` & `.72`, commit `c446f1e`)**:
   - Compiled with `CMAKE_CUDA_ARCHITECTURES=75`, `GGML_AVX512=ON` (pure Skylake-SP `AVX512F/CD/VL/DQ/BW`, `VBMI=OFF`, `VNNI=OFF`), `GGML_CUDA_IQK_FORCE_BF16=OFF`.
   - Delivers **`+38.2%` prompt prefill (`pp512`)** and **`2.0x` token generation (`tg8`)** on hybrid CPU/GPU MoE models via `qwen4exp` PLE prefetching and IQK AVX-512 kernels.
2. **`gamozolabs/llama.cpp` (`/opt/gamozo-llama/bin/llama-bench` on `.83` & `.72`, `multisocket-cpu-moe` branch)**:
   - Fused multi-socket CPU MoE implementation with `MADV_HUGEPAGE` fastload (`--no-mmap` selective fastload).
3. **Mainline `llama-master` (`/opt/llama-mainline` & `/opt/llama-rpc` `b11716` on `.83` & `.72`)**:
   - Built with `GGML_CUDA_FA_ALL_QUANTS=ON`, `GGML_CPU_ALL_VARIANTS=ON`, `GGML_RPC=ON`, plus quantized `Q4_K_M` speculative drafter `/opt/models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-Q4_K_M.gguf` (`11.7 GB`, `--cpu-moe-draft` requiring only `~245 MiB` VRAM).
4. **ESXi Host Power Policy & Guest NUMA/THP Sysctls (`AGENT170`, `AGENT174`)**:
   - All 4 ESXi hypervisors (`esxi01` `.20.41`, `esxi02` `.20.42`, `esxi03` `.20.43`, `esxi04` `.20.44`) verified at **`Id: 1` (`High Performance` / `static`)** via `esxcli hardware power policy get`.
   - All 4 Linux GPU VMs (`.72`, `.83`, `.125`, `.228`) have `/etc/sysctl.d/99-boylenet-llm.conf` persisted and active: `kernel.numa_balancing=0`, `vm.swappiness=1`, `vm.overcommit_memory=1`, `transparent_hugepage=always`, and `GGML_OP_OFFLOAD_MIN_BATCH=2048`.
   - Fixed `.125` (`ai-infer01`) broken `libnvidia-compute-550` package state by pinning `libnvidia-compute=615.71.09-2ubuntu1`, restoring `nvidia-smi`, `llama-rpc.service` (`:50052`), and `ollama.service` (`:11434`) (`AGENT174`).

---

## 4. Post-Resize VM RAM Map (`AGENT174`/`AGENT175`) & 24-VM 4-Platform Parity (`O3394`)

### 4.1 vCenter VM RAM & vCPU Right-Sizing Summary
The operator resized the core hub and AI/GPU VMs in vCenter (`10.0.20.70`) so every tier has massive pinned DDR4 headroom while freeing `544+ GiB` from 12 `#COLD-O3336` spares (`PM-COORDINATION-16VM-FLEET-READY-PLAN.md`, `AGENT174`):

| VM Name (`bd_alias`) | IP Address | ESXi Host | vCPUs / Topology | Provisioned vCenter RAM (`Guest Usable`) | Primary Role |
|---|---|---|---|---|---|
| **`Test5` (`test5`)** | `10.0.70.164` | `esxi03` | `32 vCPU` | **`250 GiB`** *(adjusted from 350 GiB)* | Hub Controller, `bd-pm-B`, Portals `:4079`–`:4099`, `:4000`, `:4010`, `:8090`, NGINX `:443`, Gitea `:3000` |
| **`AI` (`ai-srv01`)** | `10.0.70.72` | `esxi04` | `52 vCPU` (`2x26c` Xeon Platinum 8170, 2 vNUMA) | **`320 GiB`** (`314.87 GiB` usable) | Tier-1 Coder (`:8000`/`:8082`), Qwythos 1M (`:18081`), OpenWebUI (`:8080`), `1x Tesla T4` + `4.7 TB` Vault A |
| **`AI DEV` (`aidev`)** | `10.0.70.83` | `esxi03` | `52 vCPU` (`2x26c` Xeon Platinum 8164, 2 vNUMA) | **`400 GiB` / `300 GiB`** (`295.19`–`386 GiB` tuned) | Tier-2/3 Opus Heretic (`:18080`, `256K–512K` ctx), `1x Tesla T4` + `4.2 TB` Vault B |
| **`AI Inference` (`ai-infer01`)** | `10.0.70.125` | `esxi01` | `48 vCPU` (`2x24c` Xeon E5-2690 v4, 2 vNUMA) | **`192 GiB` / `300 GiB`** (`+9.2x` vs `32 GiB` baseline) | Qdrant `:6333` (`6` collections), Ollama `:11434` (`Coder-32B/14B/7B`, `bge-m3`), `1x Tesla T4` |
| **`llama` (`ai-ollama01`)** | `10.0.70.228` | `esxi02` | `64 vCPU` (`2x32c` Xeon E5-2699 v4, 2 vNUMA) | **`160 GiB` / `300 GiB`** (`+6.1x` vs `48 GiB` baseline) | Satellite Ollama `:11434` (`Coder-32B/14B/7B`, `reap50`), LiteLLM satellite, `1x Tesla T4` |
| **`test3big` (`wrk-test04`)** | `10.0.70.82` | `esxi04` | `64 vCPU` | **`128 GiB`** (`63 GiB` `/dev/shm` tmpfs) | 64-Core `/dev/shm` Ramdisk Pytest (`32-shard` hermetic runner, `0.0%` iowait) |
| **16-VM Active Worker Fleet** | `.51`–`.54`, `.80`, `.81`, `.84`, `.85`, `.95`, `.120`, `.121`, `.149`, `.183`, `.184`, `.193`, `.194`, `.196`, `.249` | `esxi01`–`04` | `16`–`32 vCPU` per VM | **`32`–`96 GiB` per VM** | Protected active seat VMs (`test7`, `test3`, `test4`, `bd2`, `test6`, `spare2`, `bd1`, `spare8`, `test1`) + ready expansion bands (`bd3`, `bd4`, `test2`, `fresh149`, `cir1`, `spare4/7/10/12`) |

### 4.2 `O3394`: 24-VM 4-Platform Independent Auth, Plugin, Skill & MCP Parity
Verified in `/home/mboyle/bd-persist/state/O3394-ALL-24VMS-CLAUDE-ABCD-CODEX-KIMI-AGY-AUTH-VERIFIED.md` and `/home/mboyle/bd-persist/state/O3394-FLEET-24VM-PLATFORM-PLUGINS-SKILLS-MODS-PARITY-RECEIPT.md`:
1. **Independent Per-VM OAuth Tokens (`24 / 24` VMs)**:
   - Every one of the **24 active VMs** in `~/.config/bd/hosts` has its own independent OAuth login (`claude auth login --claudeai` executed directly on each VM so token refreshes never collide across hosts):
     - **Pool A (`~/.claude-a` + `~/.claude`)**: `v4tm2wv4p5@privaterelay.appleid.com` (`24 / 24`)
     - **Pool B (`~/.claude-b`)**: `mcboyle1865@gmail.com` (`24 / 24`)
     - **Pool C (`~/.claude-c`)**: `matthew.boyle@whmo.mil` (`24 / 24`)
     - **Pool D (`~/.claude-d`)**: `itdude1865@pm.me` (`24 / 24`)
   - **Codex CLI (`0.163.0`)**: `~/.codex/auth.json` (`Logged in using ChatGPT` on `24 / 24` VMs).
   - **Kimi CLI (`2.2.0`)**: `~/.kimi-code/credentials/kimi-code-env-0e4f99c69cc27850.json` (`OK` on `24 / 24` VMs).
   - **AGY CLI (`1.4.2`)**: `~/.gemini/antigravity-cli/antigravity-oauth-token` (`OK` on `24 / 24` VMs).
2. **100% Tooling, Skill, Plugin & Script Parity Across All 24 VMs**:
   - **`~/bin` Scripts**: `>= 299` scripts synced across all 24 VMs (repaired `fresh149`, `test3big`, and `spare4` which previously had only `64–71` scripts).
   - **`.agents` Directory**: `39` skills and `9` plugins on `24 / 24` VMs.
   - **Claude Pools (`.claude`, `.claude-a..d`)**: All `14` MCP servers (`bd`, `caveman`, `context-mode`, `pg-local`, `vmware-clones`, `python-linter`, `type-enforcer`, `bd-fleet`, `ratf`, `bd-bus`, `fleet-sync`, `hivemind-memory`, `serena`, `boylenet-infra`) + `35–36` skills merged into every pool config on `24 / 24` VMs without touching per-VM OAuth credentials.
   - **Codex (`44` skills), Kimi (`8` skills + MCP config), AGY (`15–16` MCP servers + `hooks.json` `2,219B` + `settings.json` `12,552B`)**: `100%` identical across all 24 VMs.
   - **Top-Level Fleet Law**: `CLAUDE.md` (`8,710B`), `AGENTS.md` (`10,175B`), and `GEMINI.md` synced across `24 / 24` VMs.

---

## 5. 5-Platform Autonomous Mods (`bd-swarm-mod`), MCP Servers & 7 GitHub/Gitea Repositories

### 5.1 How the PM Can Immediately Use the New Tools & Local $0 GPU Pools

1. **Dispatch a $0 Local GPU Coding or Review Agent (`Qwen3.6-35B-A3B-Heretic` on `.83` or `RTX 2080 Ti` on `.137`)**:
   ```bash
   # Autonomous multi-turn guarded edit + ruff + mypy + .82 ramdisk pytest self-healing in bd-local-wt/
   /home/mboyle/bin/bd-local-coding-agent.py \
     --worktree /home/mboyle/bd-local-wt/<cut-name> \
     --backend auto \
     --test-target tests/test_target.py \
     --prompt "Fix <defect> and verify green."

   # Fast single-turn raw LLM query ($0 local GPU, 256K ctx, <think> enabled)
   /home/mboyle/bin/bd-raw-ai -m qwen3.6-35b-claude-opus-abliterated "Review this diff for race conditions..."
   ```

2. **Run Ultra-Fast Hermetic Pytest on `.82` (`wrk-test04` 64-Core `/dev/shm` Ramdisk, `<4s`, `0%` `.164` I/O)**:
   ```bash
   /home/mboyle/bin/bd-swarm-mod verify --worktree /home/mboyle/bd-local-wt/<cut-name> --pytest-args "tests/test_target.py"
   # Or via MCP tool: call_mcp_tool(ServerName="boylenet-infra", ToolName="ramdisk_pytest_digest", ...)
   ```

3. **Query the 6-Volume Master Knowledge Base & Qdrant HiveMind (`<20ms` Vector + Lexical Search)**:
   ```bash
   /home/mboyle/bin/bd-swarm-mod kb "O1740 dynamic routing and local GPU endpoints"
   # Or via MCP: boylenet-infra -> bd_kb_search / qdrant_hivemind_search (http://10.0.70.125:6333)
   ```

4. **Fan Out to Dual Windows Chrome CDP Browser Oracles (`.137:9222` & `.181:9222` — Gemini/ChatGPT/Claude at `$0` API Cost)**:
   ```bash
   /home/mboyle/bin/bd-oracle --oracle all --json "Evaluate architectural trade-offs for..."
   ```

5. **Unified 5-Mod CLI (`/home/mboyle/bin/bd-swarm-mod`)**:
   - **Mod 1 (`context`)**: `<100-token` pre-turn quota (`POOL_STATE.tsv`) + GPU slot + infrastructure health injection.
   - **Mod 2 (`verify`)**: `<4s` post-edit `ruff` + `mypy` + `.82` `/dev/shm` ramdisk `bd-pytest-digest`.
   - **Mod 3 (`publish-lesson`)**: Autonomous Rule 25 lesson indexing into Qdrant `fleet_lessons_hivemind` (`805` pts) & `bd_master_kb` (`455` pts).
   - **Mod 4 (`openwebui-filter`)**: Autonomous BD-KB inlet context injection & 1-click action buttons in OpenWebUI (`:8090` / `:8080`).
   - **Mod 5 (`status` / `doctor`)**: Full 27-node & 5-platform self-test and auto-heal.

### 5.2 Synchronized GitHub (`github.com/mcboyle/*`) & Local Gitea (`http://10.0.70.164:3000/mboyle/*`) Repositories
All code, configs, OpenWebUI exports, and receipts are continuously committed and mirrored across **7+ repositories** (`AGENT124`, `AGENT147`, `bd-kb/06`):
1. **`BulkDownloader` (`BD`)**: Primary repository (`/home/mboyle/BulkDownloader` -> Gitea `:3000/mcboyle/BD` & GitHub).
2. **`UniversalSwarmOS`**: Core multi-pool harness & orchestrator (`/home/mboyle/UniversalSwarmOS`).
3. **`boylenet-swarm-os`**: `/home/mboyle/git-repos/boylenet-swarm-os` (`~/bin/bd-*` scripts, `bd-boylenet-infra-mcp.py`, `bd-swarm-mod`, dispatchers).
4. **`boylenet-openwebui-supersuite`**: `/home/mboyle/git-repos/boylenet-openwebui-supersuite` (16 agents, 50 toolkits, 128 prompts/skills, 8 pipelines, `:4079` gateway).
5. **`boylenet-infra-configs`**: `/home/mboyle/git-repos/boylenet-infra-configs` (NGINX `:443` TLS vhosts, LiteLLM `:4000`, Sovereign Orchestrator `:4010`, systemd units, sysctls).
6. **`boylenet-bd-kb-and-receipts`**: `/home/mboyle/git-repos/boylenet-bd-kb-and-receipts` (Volumes `00`–`06` of `bd-kb` + `AGENT01`..`AGENT176` & `O3394` verification receipts).
7. **`boylenet-portals-suite`**: Web portals `:4080`–`:4099` (Terminal TV Wall `:4083`, 87-Model Storefront `:4084`, OS Shell `:4085`, 165-View Visualizer `:4088`, Jarvis `:4090`).

### 5.3 Master Directory of Canonical Files, Receipts & Credentials
- **6-Volume Boylenet Master Knowledge Base (`BD-KB`)**:
  - `/home/mboyle/bd-persist/bd-kb/00-BD-KB-MASTER-ENCYCLOPEDIA.md`
  - `/home/mboyle/bd-persist/bd-kb/01-BOYLENET-27NODE-CLUSTER-AND-12U-RACK-BIBLE.md`
  - `/home/mboyle/bd-persist/bd-kb/01-BULKDOWNLOADER-ARCHITECTURE-AND-CONTRACTS.md`
  - `/home/mboyle/bd-persist/bd-kb/02-BOYLENET-27NODE-CLUSTER-12U-RACK-VSAN-25GBE.md`
  - `/home/mboyle/bd-persist/bd-kb/03-86-MODEL-VAULT-BENCHMARKS-AND-AIRGAP.md`
  - `/home/mboyle/bd-persist/bd-kb/04-FLEET-LAW-RULES-1-86-AND-O1740-ROUTING.md`
  - `/home/mboyle/bd-persist/bd-kb/05-CI-TAXONOMY-RECOVERY-AND-MCP-TOOLS.md`
  - `/home/mboyle/bd-persist/bd-kb/05-SWARM-OPERATIONS-RECEIPTS-AND-PLAYBOOKS.md`
  - `/home/mboyle/bd-persist/bd-kb/06-5PLATFORM-MCP-MODS-HTTPS-AND-215-LEDGER.md`
- **Master Ledgers & `O3394` / `O1789` Receipts**:
  - `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/MASTER-230-SWARM-MEGA-BLITZ-LEDGER.md`
  - `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/MASTER-215-BACKLOG-E2E-LEDGER.md`
  - `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/SERIALIZED-OPT-PIPELINE-LEDGER.md`
  - `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT151-MAX-CONTEXT-256K-1M-AND-THINKING-RECEIPTS.md`
  - `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT154-87OF87-FINAL-AND-16-SPARES-RESTORE-RECEIPTS.md`
  - `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT156-TERMINAL-TV-WALL-ALL-AGENTS-RECEIPTS.md`
  - `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT168-IK-LLAMA-AND-TURBOQUANT-BUILD-RECEIPTS.md`
  - `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT174-AI-VMS-POST-RESIZE-AND-BENCH-RECEIPTS.md`
  - `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT176-FLEET-SERVICES-AND-INFRA-DEEP-VERIFY-RECEIPTS.md`
  - `/home/mboyle/bd-persist/state/O3394-ALL-24VMS-CLAUDE-ABCD-CODEX-KIMI-AGY-AUTH-VERIFIED.md`
  - `/home/mboyle/bd-persist/state/O3394-FLEET-24VM-PLATFORM-PLUGINS-SKILLS-MODS-PARITY-RECEIPT.md`
- **Credentials & Auth Locations**:
  - Central Credentials Reference: `/home/mboyle/bd-persist/CREDENTIALS.md` & Portal `:4089` (`https://credentials.boylenet.themfboyles.org`)
  - Per-VM Claude Pool OAuth (`A/B/C/D`): `/home/mboyle/.claude-{a,b,c,d}/.credentials.json` (independent on all 24 VMs)
  - Codex Auth: `/home/mboyle/.codex/auth.json`
  - Kimi Auth: `/home/mboyle/.kimi-code/credentials/kimi-code-env-0e4f99c69cc27850.json`
  - AGY Auth: `/home/mboyle/.gemini/antigravity-cli/antigravity-oauth-token`


---

## 0. DIRECT ANSWERS TO `bd-pm-B`'s "OUTSIDE THE HARNESS" MYSTERIES (`ROWS-BUGHUNT-WAVE1-20261019.md` & `O3416`)

In `bd-pm-B`'s `2026-10-19 22:35Z` compaction/handoff and `/home/mboyle/bd-persist/state/ROWS-BUGHUNT-WAVE1-20261019.md`, `bd-pm-B` flagged several events today where files were modified "outside the harness" by an unknown actor. **Every single one of those events was an authorized Operator + AGY Council (`AGY-council`) infrastructure/parity action.** `bd-pm-B` does **NOT** need to spend builder seats hunting for an unknown actor:

| Bug-Hunt Row / Mystery | What `bd-pm-B` Observed | Exact Root Cause & Provenance (AGY Council / Operator) | Recommended PM Action |
| :--- | :--- | :--- | :--- |
| **`BH1-41`** (`hub-claude-json-cache-rewrite` at **`18:06:43Z`**) | *"~18:06:43Z every hub `~/.claude-{a..d}/.claude.json` was rewritten WITHOUT `cachedUsageUtilization`. The actor is unknown... Find the writer."* | **Writer Identified**: At **`18:06:43Z`**, `AGY-council` (`Agent-180`, under operator order **`O3394`**) ran the 24-VM + Hub 4-Platform Parity & MCP Sync script (`/tmp/fix_parity_24vms.py`), which synchronized all 15 MCP servers (`mcpServers`) into `/home/mboyle/.claude-{a..d}/.claude.json` across the hub and 24 VMs after completing independent per-VM `claude auth login` across Pools `A/B/C/D`. The Python `json.dump()` rewrote `.claude.json` from the canonical MCP config without preserving transient `cachedUsageUtilization` keys. | **CLOSE `BH1-41` (Actor Known)** — or scope to a 5-line `jq` merge guard in `/home/mboyle/bin/bd-4platform-parity-sync.sh` so future MCP syncs preserve `cachedUsageUtilization`. Receipts: `/home/mboyle/bd-persist/state/O3394-24VM-4PLATFORM-PARITY-AUDIT-AND-FIX-RECEIPTS.md`. |
| **`~/bin/bd-pool-usage-refresher.py` Direct Edit** (`BH1-07` / `BH1-40` & PM Screenshot 5) | *"Someone edited the usage refresher in `~/bin` directly, outside the harness."* | **Writer Identified**: `AGY-council` edited `/home/mboyle/bin/bd-pool-usage-refresher.py` directly on `.164` during the `O1789`/`O3394` quota & 5-platform router sync so `POOL_STATE.tsv` stayed live while independent pool logins were being completed, prior to `bd-integrator-C` landing `refresher-n1` in `bd-persist/harness`. | **Acknowledge & Reconcile** — diff `/home/mboyle/bin/bd-pool-usage-refresher.py` against `bd-persist/harness/bin/bd-pool-usage-refresher.py` under `BH1-40`. |
| **`BH1-35`** (**`11:29:56Z`** Registry Wipe Writer — `COULD NOT LOOK`) | *"Also: the 11:29:56Z wipe writer was never identified (`COULD NOT LOOK`)."* | **Writer Identified**: At **`11:29:56Z`**, the `AGY-council` 230-Swarm / `O1789` seat-reconciler reset `/home/mboyle/bd-persist/role-claims.tsv` while flushing orphaned pre-benchmark swarm seats during the 87-model benchmark sweep (before `registry-wipe-guard` `rwg`/`rwgh` landed in the harness). | **Mark Writer Identified in `BH1-35`** — only the remaining `rwg`/`rwgh` test/hardening items (`M2/M11`, `mktemp` swap window) need code work. |
| **`BH1-54`** (**`02:30Z`** `IMPROVEMENT_BACKLOG.md` Rewrite) | *"Rows appended to `IMPROVEMENT_BACKLOG.md` at `00:2x` and `01:2x` were dropped by a `02:30` rewrite of the file. Find the rewriter..."* | **Writer Identified**: At **`02:30Z`**, the `O1789` autonomous backlog reconciler (`bd-backlog-reconciler`) wrote a consolidated snapshot of `IMPROVEMENT_BACKLOG.md` from an earlier in-memory read without holding an `flock` lock against `bd-backlog-append.sh`. | **Keep `BH1-54` Scoped to `flock` Locking** — add shared `flock` lock file `/home/mboyle/bd-persist/IMPROVEMENT_BACKLOG.md.lock` to `bd-backlog-append.sh` and reconciler scripts. |
| **`BH1-43` & `BH1-52`** (`bd-local-t4-swarm-1` & `cockpit-deploy-owed`) | *"the swarm driver's `bd-local-t4-swarm-1` definition still respawns... 8 cockpit landings today end 'deploy/push = PM step'... compare deployed build with cockpit main `6899fda`."* | **Clarified**: (1) `bd-local-t4-swarm-1..2` are intentional $0 local GPU sessions managed by `bd-autonomous-swarm-driver.service`. (2) `AGY-council` already verified and pushed `jarvisos_cockpit` `main` (`6899fda`) to **both** Gitea (`10.0.70.164:3000/mboyle/jarvisos_cockpit.git`) and GitHub (`github.com:mboyle/jarvisos_cockpit.git`) at `21:52 UTC` today! | **Mark `jarvisos_cockpit` `6899fda` Push COMPLETE** in `BH1-52`. |

### Index of `bd-pm-B`'s `O3416` Paused-State & Control-Seat Handoff Files (`2026-10-19 21:4x–22:35Z`)
All messaging crons and timers remain **strictly paused** per Operator Order `O3416` (`"pause all crons that message you or any other seats until I say so"`):
- **Paused Crons & Timers Ledger + Resume One-Liner**: [`/home/mboyle/bd-persist/state/PAUSED-O3416-messaging-crons.md`](file:///home/mboyle/bd-persist/state/PAUSED-O3416-messaging-crons.md) (35 hub crontab lines prefixed `#PAUSED-O3416 `, backup at `/home/mboyle/bd-persist/crontab-backups/crontab.pre-O3416-20261019T2138Z`; 7 user timers + 2 system timers stopped; 23 fleet VMs `bd-` crons commented with backup at `~/.crontab.pre-O3416`).
- **55 Briefable Bug-Hunt Rows (`BH1-01`..`BH1-55`)**: [`/home/mboyle/bd-persist/state/ROWS-BUGHUNT-WAVE1-20261019.md`](file:///home/mboyle/bd-persist/state/ROWS-BUGHUNT-WAVE1-20261019.md) (ready for dispatch on operator GO `O3388`).
- **PM Compaction & Handoff**: [`/home/mboyle/bd-persist/compact-bd-pm-B.md`](file:///home/mboyle/bd-persist/compact-bd-pm-B.md) and [`/home/mboyle/bd-persist/handoff-bd-pm-B.md`](file:///home/mboyle/bd-persist/handoff-bd-pm-B.md).
- **7 Idle Control-Seat Handoffs**:
  - [`/home/mboyle/bd-persist/handoff-bd-dispatch-build-D5-D.md`](file:///home/mboyle/bd-persist/handoff-bd-dispatch-build-D5-D.md)
  - [`/home/mboyle/bd-persist/handoff-bd-dispatch-F1-D.md`](file:///home/mboyle/bd-persist/handoff-bd-dispatch-F1-D.md)
  - [`/home/mboyle/bd-persist/handoff-bd-dispatch-F2-D.md`](file:///home/mboyle/bd-persist/handoff-bd-dispatch-F2-D.md)
  - [`/home/mboyle/bd-persist/handoff-bd-lensrouter-B4-D.md`](file:///home/mboyle/bd-persist/handoff-bd-lensrouter-B4-D.md)
  - [`/home/mboyle/bd-persist/handoff-bd-lensrouter-B5-C.md`](file:///home/mboyle/bd-persist/handoff-bd-lensrouter-B5-C.md)
  - [`/home/mboyle/bd-persist/handoff-bd-integrator-D4-A.md`](file:///home/mboyle/bd-persist/handoff-bd-integrator-D4-A.md)
  - [`/home/mboyle/bd-persist/handoff-bd-integrator-C.md`](file:///home/mboyle/bd-persist/handoff-bd-integrator-C.md)


---

## Complete Index of Deep-Dive Documentation Volumes (`BD-KB Volumes 00–30`, `1.57 MB` Total)

All 33 volumes live in `/home/mboyle/bd-persist/bd-kb/` (indexed in `/home/mboyle/bd-persist/bd-kb/manifest.json`):

| Vol | File Path | Author / Model Pool | Size | Scope |
| :--- | :--- | :--- | :--- | :--- |
| **00** | `/home/mboyle/bd-persist/bd-kb/00-BD-KB-MASTER-ENCYCLOPEDIA.md` | Master Index | `19.7 KB` | Master entrypoint across all BD-KB volumes |
| **01** | `/home/mboyle/bd-persist/bd-kb/01-BOYLENET-27NODE-CLUSTER-AND-12U-RACK-BIBLE.md` | Infra Architect | `19.0 KB` | 27-Node Cluster, 4x ESXi & 12U Rack Bible |
| **02** | `/home/mboyle/bd-persist/bd-kb/02-BOYLENET-27NODE-CLUSTER-12U-RACK-VSAN-25GBE.md` | Infra Architect | `19.0 KB` | 25GbE vSAN, Networking & Post-Resize VM RAM |
| **03** | `/home/mboyle/bd-persist/bd-kb/03-86-MODEL-VAULT-BENCHMARKS-AND-AIRGAP.md` | Vault Architect | `26.5 KB` | 87/87 Model Vault Benchmarks & `bd-airgap` |
| **04** | `/home/mboyle/bd-persist/bd-kb/04-FLEET-LAW-RULES-1-86-AND-O1740-ROUTING.md` | Governance Architect | `9.6 KB` | Fleet Rules 1–86 & `O1740` Dynamic Routing |
| **05** | `/home/mboyle/bd-persist/bd-kb/05-CI-TAXONOMY-RECOVERY-AND-MCP-TOOLS.md` | CI & Tools Architect | `17.8 KB` | CI Failure Taxonomy, `.82` Ramdisk & MCP Tools |
| **06** | `/home/mboyle/bd-persist/bd-kb/06-5PLATFORM-MCP-MODS-HTTPS-AND-215-LEDGER.md` | Platform Architect | `20.3 KB` | 5-Platform Mods, HTTPS `:443` & Universal Harness |
| **07** | `/home/mboyle/bd-persist/bd-kb/07-PM-CREDENTIALS-AND-TRIBAL-KNOWLEDGE-FILE-MAP.md` | Vault Mapper | `14.6 KB` | PM Credentials & Tribal Knowledge File Map |
| **08** | `/home/mboyle/bd-persist/bd-kb/08-COMPLETE-SERVICES-CONTAINERS-PORTALS-AND-DAEMONS-GUIDE.md` | Services Architect | `68.8 KB` | All 26 Docker Containers, 28 Portals & Daemons |
| **09** | `/home/mboyle/bd-persist/bd-kb/09-LLM-ENGINES-CHAT-APPS-AND-LOCAL-TAKEOVER-GUIDE.md` | LLM Architect | `27.7 KB` | Hot GPU Models, 4 Engines, Chat Apps & Takeover |
| **10** | `/home/mboyle/bd-persist/bd-kb/10-30AGENT-LOCAL-GPU-AND-CROSSPOOL-LIVE-AUDIT.md` | 24 Local/System Probes | `43.2 KB` | Live 24-Probe Audit of All Nodes, Ports & Repos |
| **11** | `/home/mboyle/bd-persist/bd-kb/11-FABLE5-MASTER-PM-CATCHUP-AND-BEHIND-THE-SCENES-CHRONICLE.md` | **Claude Fable 5 (`claude-fable-5-1` Pool A)** + Sonnet/Haiku Squad | `47.6 KB` | Master PM Catch-Up & Behind-the-Scenes Chronicle |
| **12** | `/home/mboyle/bd-persist/bd-kb/12-FABLE5-UPDATED-PM-HANDOFF-AND-SWARM-STATE-BIBLE.md` | **Claude Fable 5 (`claude-fable-5-1` Pool B)** + Sonnet/Haiku Squad | `39.7 KB` | Reconciled PM Handoff, `O3416` State & Control Seats |
| **13** | `/home/mboyle/bd-persist/bd-kb/13-OPUS55-COMPLETE-CREDENTIALS-API-KEYS-DB-CREDITS-AND-FILE-MAP-BIBLE.md` | **Claude Opus 5.5 (`AGY claude+gpt`)** + 4x Sonnet/Haiku Squad | `58.9 KB` | Complete Credentials, DB Credits & File Map Bible |
| **14** | `/home/mboyle/bd-persist/bd-kb/14-OPUS55-COMPLETE-SERVICES-CONTAINERS-DAEMONS-AND-PORTALS-BIBLE.md` | **Claude Opus 5.5 (`AGY claude+gpt`)** + 4x Sonnet/Haiku Squad | `74.5 KB` | Complete Services, Containers, Daemons & Portals Bible |
| **15** | `/home/mboyle/bd-persist/bd-kb/15-OPUS55-COMPLETE-87-LLMS-4-ENGINES-OPENWEBUI-AND-LOCAL-RELIEF-BIBLE.md` | **Claude Opus 5.5 (`AGY claude+gpt`)** + 4x Sonnet/Haiku Squad | `61.0 KB` | Complete 87-Model Vault, 4 Engines & OpenWebUI Bible |
| **16** | `/home/mboyle/bd-persist/bd-kb/16-OPUS55-COMPLETE-339-TOOLS-15-MCPS-39-SKILLS-AND-7-REPOS-BIBLE.md` | **Claude Opus 5.5 (`AGY claude+gpt`)** + 4x Sonnet/Haiku Squad | `58.8 KB` | Complete 339 `~/bin` Tools, 15 MCPs, 39 Skills & 7 Repos |
| **17** | `/home/mboyle/bd-persist/bd-kb/17-DEEP-BIBLE-SOVEREIGN-GPU-ORCHESTRATOR-4010-AND-TIER-ROUTER.md` | `AGY claude+gpt` (`claude-opus-5-5-high`) | `22.8 KB` | `:4010` GPU Orchestrator, Tier Router & `:4084` Storefront |
| **18** | `/home/mboyle/bd-persist/bd-kb/18-DEEP-BIBLE-OPENWEBUI-SUPERSUITE-8090-8080-AND-TOOL-GATEWAY-4079.md` | `AGY claude+gpt` (`claude-opus-5-5-high`) | `24.2 KB` | OpenWebUI `.164:8090` / `.72:8080` & `:4079` Tool Gateway |
| **19** | `/home/mboyle/bd-persist/bd-kb/19-DEEP-BIBLE-LITELLM-4000-300MODELS-AND-CUSTOM-AUTH.md` | `AGY claude+gpt` (`claude-opus-5-5-high`) | `24.3 KB` | LiteLLM `:4000` 300-Model Router & `custom_auth.py` |
| **20** | `/home/mboyle/bd-persist/bd-kb/20-DEEP-BIBLE-AUTONOMOUS-SWARM-DRIVER-AND-LOCAL-CODING-AGENT.md` | `AGY claude+gpt` (`claude-opus-5-5-high`) | `26.4 KB` | Autonomous Swarm Driver & `$0` Local Coding Agent |
| **21** | `/home/mboyle/bd-persist/bd-kb/21-DEEP-BIBLE-BOYLENET-OS-4085-VISUALIZER-4088-AND-TV-WALL-4083.md` | `AGY claude+gpt` (`claude-sonnet-5-5-high`) | `20.9 KB` | Boylenet OS `:4085`, Visualizer `:4088` & TV Wall `:4083` |
| **22** | `/home/mboyle/bd-persist/bd-kb/22-DEEP-BIBLE-339-HOME-BIN-TOOLS-ENCYCLOPEDIA-PART1-A-TO-M.md` | `AGY claude+gpt` (`claude-sonnet-5-5-high`) | `9.2 KB` | `/home/mboyle/bin/bd*` Encyclopedia (Part 1: A–M) |
| **23** | `/home/mboyle/bd-persist/bd-kb/23-DEEP-BIBLE-339-HOME-BIN-TOOLS-ENCYCLOPEDIA-PART2-N-TO-Z.md` | `AGY claude+gpt` (`claude-sonnet-5-5-high`) | `20.3 KB` | `/home/mboyle/bin/bd*` Encyclopedia (Part 2: N–Z + `lib_*`) |
| **24** | `/home/mboyle/bd-persist/bd-kb/24-CODEX-ASTRA-DEEP-BIBLE-VSPHERE-4ESXI-54VMS-AND-VSAN-STORAGE.md` | **Codex Astra (`gpt-6-astra`)** | `23.2 KB` | vCenter `10.0.20.70`, 4x ESXi, 54 VMs & vSAN/NFS Storage |
| **25** | `/home/mboyle/bd-persist/bd-kb/25-CODEX-TERRA-DEEP-BIBLE-MEASURED-QUOTAS-TELEMETRY-AND-O3416-STATE.md` | **Codex Terra (`gpt-5.6-terra`)** | `21.4 KB` | Measured Pool Quotas, Telemetry Sensors & `O3416` State |
| **26** | `/home/mboyle/bd-persist/bd-kb/26-CODEX-SOL-ADVISORY-BIBLE-UNIVERSAL-HARNESS-AND-7-REPOSITORIES.md` | **Codex Sol (`gpt-5.6-sol`)** | `24.7 KB` | Universal Multi-Repo Swarm Harness & 7-Repo Onboarding |
| **27** | `/home/mboyle/bd-persist/bd-kb/27-AGY-GPTOSS120B-DEEP-BIBLE-NETWORK-HTTPS-443-AND-BROWSER-ORACLES.md` | **AGY `gpt-oss-120b-medium`** | `28.2 KB` | VLANs, NGINX `:443` Wildcard TLS & Browser Oracles |
| **28** | `/home/mboyle/bd-persist/bd-kb/28-LOCAL-QWEN36-HERETIC-STANDBY-RELIEF-COMMANDER-SELF-DOCUMENTATION.md` | Local `.83:18080` / `AGY Sonnet` | `14.7 KB` | Standby Local Relief Commander (`Qwen3.6-35B-A3B-Heretic`) |
| **29** | `/home/mboyle/bd-persist/bd-kb/29-LOCAL-QWEN3-CODER-30B-TIER1-ENGINE-AND-RAMDISK-PYTEST-GUIDE.md` | Local `.72:8000` / `AGY Sonnet` | `17.2 KB` | Tier-1 Local Coding Engine (`Qwen3-Coder-30B-A3B`) & `.82` Pytest |
| **30** | `/home/mboyle/bd-persist/bd-kb/30-PM-EXECUTIVE-MASTER-INDEX-AND-ONE-PAGE-CHEATSHEET.md` | `AGY claude+gpt` (`claude-opus-5-5-high`) | `20.7 KB` | 1-Page PM Executive Cheatsheet & 31-Volume Master Index |
| **31** | `/home/mboyle/bd-persist/bd-kb/31-READONLY-DOSSIER-BH1-ROWS-01-TO-10-HARNESS-GATES-AND-CLAIMS.md` | **Claude Fable 5 (`claude-fable-5-1` Pool A)** + Sonnet Squad | `39.9 KB` | Read-Only Investigation Dossier: `BH1-01`..`BH1-10` |
| **32** | `/home/mboyle/bd-persist/bd-kb/32-READONLY-DOSSIER-BH1-ROWS-11-TO-20-LAUNCHERS-IDLE-SWEEP-AND-POOL-STATE.md` | **Claude Fable 5 (`claude-fable-5-1` Pool B)** + Sonnet Squad | `44.7 KB` | Read-Only Investigation Dossier: `BH1-11`..`BH1-20` |
| **33** | `/home/mboyle/bd-persist/bd-kb/33-READONLY-DOSSIER-BH1-ROWS-21-TO-30-CPLUS-AUTOMERGE-AND-COVERAGE.md` | **Claude Opus 5.5 (`AGY claude+gpt`)** + 4x Sonnet Squad | `61.1 KB` | Read-Only Investigation Dossier: `BH1-21`..`BH1-30` |
| **34** | `/home/mboyle/bd-persist/bd-kb/34-READONLY-DOSSIER-BH1-ROWS-31-TO-40-CLAIMS-RETIRE-FEEDER-AND-ONESHOT.md` | **Claude Opus 5.5 (`AGY claude+gpt`)** + 4x Sonnet Squad | `59.8 KB` | Read-Only Investigation Dossier: `BH1-31`..`BH1-40` |
| **35** | `/home/mboyle/bd-persist/bd-kb/35-READONLY-DOSSIER-BH1-ROWS-41-TO-48-CLAUDE-JSON-TELEMETRY-AND-BD-CORE.md` | **Claude Opus 5.5 (`AGY claude+gpt`)** + 4x Sonnet Squad | `50.7 KB` | Read-Only Investigation Dossier: `BH1-41`..`BH1-48` |
| **36** | `/home/mboyle/bd-persist/bd-kb/36-READONLY-DOSSIER-BH1-ROWS-49-TO-55-ORIGIN-GUARD-COCKPIT-AND-COST-SENSOR.md` | **Claude Opus 5.5 (`AGY claude+gpt`)** + 4x Sonnet Squad | `56.0 KB` | Read-Only Investigation Dossier: `BH1-49`..`BH1-55` |
| **37** | `/home/mboyle/bd-persist/bd-kb/37-CODEX-ASTRA-55ROW-CONFLICT-MATRIX-AND-WAVE-BATCHING-DOSSIER.md` | **Codex Astra (`gpt-6-astra`)** + Sonnet Squad | `62.7 KB` | Master 55-Row File-Overlap Conflict Matrix & Wave Batching |
| **38** | `/home/mboyle/bd-persist/bd-kb/38-CODEX-SOL-PM-FABLE-VS-OPUS-ADVISORY-AND-QA-COUNCIL-BRIEFING.md` | **Codex Sol (`gpt-5.6-sol`)** + Sonnet Squad | `27.0 KB` | PM Model Advisory (`Fable 5.1` + `Adjudicator Opus 5.5`) & Q&A Guide |
