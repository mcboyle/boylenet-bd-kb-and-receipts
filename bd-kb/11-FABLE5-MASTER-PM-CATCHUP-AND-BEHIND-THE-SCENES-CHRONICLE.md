# BD-KB Volume 11: Claude Fable-5 Master PM Catch-Up Guide — Everything Built Outside the PM & How It All Works
> **Lead Architect**: `Fable5-Lead-1` (`claude-fable-5-1 (.claude-a)`)
> **Subagent Workflow**: 2x Parallel `AGY claude+gpt` (`claude-sonnet-5-5-high` & `claude-sonnet-5-5-low` Sonnet/Haiku Tier) Specialists
> **Generated At**: `2026-10-19T23:00:36Z`
> **Scope**: Strictly Read-Only Infrastructure, Tools, Services, LLMs & PM Catch-Up Documentation (`O3416` Compliant)

---

# BD-KB Volume 11: Claude Fable-5 Master PM Catch-Up Guide

**Everything Built Outside the PM and How It All Works**

| Field | Value |
|---|---|
| Volume | 11 |
| Author | Lead Documentation Architect `Fable5-Lead-1` |
| Audience | `bd-pm-B` and the Command Squad (`integrator`, `dispatch`, `lensrouter`, `audit`) |
| Date | 2026-10-19 |
| Primary source | `/home/mboyle/bd-persist/PM-CATCHUP-BRIEF-EVERYTHING-BUILT-20261019.md` (`Agent-182`, `2026-10-19T22:30:00Z`) |
| Inbox mirror | `/home/mboyle/bd-persist/inbox/PM/BRIEF-AGY-FULL-INFRA-AND-TOOLS-CATCHUP-20261019.md` |
| Event ledger under review | `ROWS-BUGHUNT-WAVE1-20261019.md` |
| Method | Synthesis of two subagent reports. No tools run, no commands executed, nothing verified live. |

> [!IMPORTANT]
> **Provenance rule for this volume.** Every fact below traces to one of the two subagent reports, which in turn trace to the brief and the bug-hunt ledger. Both source files were truncated at 25,000 bytes. Where the sources are silent, this guide says so. Nothing in Part III assigns a cause, actor or timestamp the sources do not state.

---

## Part I: How We Have Been Operating

### 1. The two-track model

For the past few days the fleet has run on two tracks at once. The first track is the PM train. `bd-pm-B`, the four `bd-usage-a..d` seats and the ten seat VMs (`.164`, `.84`, `.80`, `.85`, `.52`, `.249`, `.193`, `.51`, `.184`, `.194`) have been burning down the `BulkDownloader` queue, integrating cuts and running bug-hunt waves. That train covers `ORDER-unpark-all.md`, `BUGHUNT-WAVE1` and every live `tmux` seat on `.164` and the remote worker bands.

The second track is the parallel build. The operator and the AGY Council (`d61a0f86`) stood up the sovereign AI stack, the web portals, the model vault and 24-VM platform parity. The workforce was the 230-Swarm (`UNIT-001` through `UNIT-230`) and the Local GPU Agents (`Agent-105` through `Agent-182`).

The governing invariant was **100% non-blocking parallel execution**. The build track was not allowed to touch the PM train. Four mechanisms enforced that:

| Mechanism | What it did | Receipt |
|---|---|---|
| Seat protection | More than 64 live `tmux` sessions across the ten seat VMs were left untouched. | `PM-COORDINATION-16VM-FLEET-READY-PLAN.md` |
| Crash recovery | `.164` rebooted at about `03:52 UTC` during vCenter RAM adjustments. `Agent-173` restored 9 of 9 Claude sessions, including `bd-pm-B` on transcript `5f51e26c-982c-4726-9e68-aa66912e330c` and `bd-usage-a..d`, and confirmed 33 of 33 remote `tmux` sessions intact. | `AGENT173-POST-CRASH-PM-AND-SESSIONS-RESTORE-RECEIPTS.md` |
| Noise suppression | During `O3416`, two background bash loop crons (`ag-cron-e11ed606`, `ag-cron-efbfcfbe`) collided with the PM's active turn and were disarmed. | `PAUSED-O3416-messaging-crons.md` |
| Handoff channel | All PM-facing material was dropped passively into `/home/mboyle/bd-persist/inbox/PM/`. No `tmux` kicks were sent. | None cited |

**What this means for `bd-pm-B`.** The reboot at `03:52 UTC` and the cron collision during `O3416` are the two moments the build track visibly brushed the train. Both were recovered and receipted. If the PM saw a session gap around that time, the `Agent-173` receipt is the record to read.

### 2. The 230-Swarm Mega-Blitz

The ledger is `MASTER-230-SWARM-MEGA-BLITZ-LEDGER.md`. All 230 units completed. The swarm was split into eight cohorts over different quota pools and hardware so no single pool or host saturated.

| Cohort | Units | Count | Driver | Focus |
|---|---|---|---|---|
| I | `001`–`046` | 46 | Native AGY subagents (`Agent-105`–`150`) | Portals, OpenWebUI, vSAN and 25GbE RoCE, dual browser oracles. Reconciled 215 of 215 queue rows across `O1670`, `P3-LENS`, `BH1`, `P2-FIX`, `APP-DL-FIX`, `APP-FEATURE` and `unpark-open`. |
| II | `047`–`074` | 28 | Claude Code Pools A/B/C/D via `bd-claude-28worker-blitz.py` | Blitz workers |
| III | `075`–`102` | 28 | OpenAI Codex CLI `gpt-5.4`/`sol` via `bd-codex-28worker-blitz.py` | Blitz workers |
| IV | `103`–`126` | 24 | Moonshot Kimi K2.8 CLI via `bd-kimi-24worker-blitz.py` | UI/UX |
| V | `127`–`146` | 20 | Local GPU pool (`.83` Heretic, `.137` RTX 2080 Ti, `.228` Tesla T4) via `bd-local-20gpu-10oracle-blitz.py` | $0 on-prem inference |
| VI | `147`–`156` | 10 | Dual Chrome CDP oracles at `.137:9222` and `.181:9222` | $0 marginal API cost |
| VII | `157`–`186` | 30 | Command Squad plus `.82` (`wrk-test04`) 64-core, 32-shard `/dev/shm` ramdisk via `bd-30squad-32ramdisk-blitz.py` | Hermetic test lanes |
| VIII | `187`–`230` | 44 | Sub-swarm | NGINX `:443` with Cloudflare DNS-01 wildcard HTTPS, 7-repo GitHub and Gitea publishing |

Cohort I matters most to the PM. Its 215-row reconciliation touched the same queue families the PM train owns. If `bd-pm-B` finds a row already reconciled that it did not reconcile itself, Cohort I is the first place to look.

---

## Part II: Everything That Now Exists

### 3. Routers and gateways

All services run on Hub `.164` (`10.0.70.164`) or AI host `.72` (`10.0.70.72`). They returned `HTTP 200` in the `AGENT153`, `AGENT156` and `AGENT176` checks. Every service sits behind NGINX `:443` with a Cloudflare DNS-01 wildcard certificate for `*.boylenet.themfboyles.org` and `*.boylenet.themfboyles.com`, managed by `/home/mboyle/bin/bd-letsencrypt-cloudflare`.

| Endpoint | Service | Vhost | What it does |
|---|---|---|---|
| `.164:4010` | Sovereign Local GPU Orchestrator (`bd-gpu-orchestrator.service`) | `gpu.boylenet…` | OpenAI-compatible `/v1` over all 87 vault models. Advertises 262,144 to 1,048,576 context and `reasoning: true`. Uses live `/slots` idle detection on `.72:8000` and `.83:18080`, overflowing to `.137:11434` and `.228:11434`. Injects `<think>` and `reasoning_content`. Enforces the Argon-4 safety shield. |
| `.164:4000` | LiteLLM 300-model router (`litellm-proxy`) | `litellm.boylenet…` | 300+ local and cloud aliases, including `tier1-qwen3-coder-30b-a3b`, `claude-opus-5-5`, `fable-5-1`, `kimi-k2.8`, `gpt-6.1-sol` and the 16 agent personas. Routes through `:4010/v1` with fallback chains. |
| `.164:8090` and `.72:8080` | OpenWebUI Super-Suite | `openwebui…`, `openwebui72…` | 16 agents, 50 toolkits, 128 skills and prompts, 8 pipelines. Pipelines include the BD-KB auto-context inlet and the Rule 25 lesson outlet. |
| `.164:4079` | Universal Tool and 27-VM Terminal Gateway (`bd-openwebui-tool-gateway.py`) | `gateway.boylenet…` | OpenAPI 3.1 at `/openapi/infra.json` and `/tools/{tool}/call`. VM exec and file APIs at `/vm/<vm>/execute` and `/vm/<vm>/files/list\|read`. Includes `.72` and `.83` benchmark contention guards. |

**How `bd-pm-B` uses these.** Point any OpenAI-compatible client at `:4000` for cloud or local aliases, or at `:4010/v1` for guaranteed on-prem inference. The `:4079` gateway is the programmatic way to run a command or read a file on any of the 27 VMs without opening a `tmux` seat.

### 4. The 28 web portals

The suite is `:4080` through `:4099` plus four `:3000`-series apps. Every portal has a vhost under the wildcard domains.

| Port | Portal | Vhost prefix | Highlights |
|---|---|---|---|
| 4080 | Command Deck (Dashy) | `dashy` | `/api/hardware` telemetry feed |
| 4081 | Executive Briefing | `briefing` | Burn rates and milestones |
| 4082 | Multi-AI Hub | `portal` | Side-by-side arena and consensus fanout |
| 4083 | Terminal TV Wall (`bd-web-terminal-drawer.service`) | `terminal` | 116 live tiles, zero DOM truncation. Covers 31 local `tmux` sessions, 7 remote engines, 16 agents, 24 AGY subagents and 10 cohort tiles. |
| 4084 | 87-Model Vault Storefront | `models` | Catalog, leaderboard, `/api/models`, hot-swap profiles |
| 4085 | Boylenet OS Web Shell | `os` | Desktop in browser |
| 4086 | HiveMind Memory Dreamer | `dreamer` | Qdrant graph explorer over `bd_master_kb`, `fleet_lessons_hivemind`, `boylenet_episodic_memory` |
| 4087 | Gitea Pre-Review and AST Diff | `prereview` | Cut inspection and `bd-ast-diff` |
| 4088 | 165-View 60fps Visualizer | `topology` | 12U rack, 4 ESXi hosts, 107 TB NVMe vSAN, 25GbE RoCE, 27 VMs |
| 4089 | Credentials Vault UI | `credentials` | Masked inventory, DNS and token audit |
| 4090 | Jarvis Ops Center | `jarvis` | Natural-language ops and `/api/git-repos` |
| 4091 | OpenCode Web Studio (A) | `harnesses` | Wired to `:4000` and `:4010` |
| 4092 | Fleet Chat and Bus Monitor | `pwa` | `bd-bus` WAL viewer, mobile PWA |
| 4093 | Voice/Vision Bridge | `voice` | Whisper STT, Kokoro TTS, `Qwen2.5-VL-7B`, `Pixtral-12B` |
| 4094 | Deep Research and RAG | `research` | SearXNG `:8888`, DevDocs `:8088`, Qdrant `:6333` |
| 4095 | LobeChat and Airgap Controller | `lobechat` | `<262144:fc:reasoning>` on 7 core models |
| 4096 | OpenCode Web IDE / SillyTavern bridge | `opencode` | 114 LiteLLM and 95 orchestrator models at `context: 262144, output: 16384` |
| 4097 | Cloud Burst and Quota Telemetry | `burst` | `POOL_STATE.tsv`, O1740 ledger |
| 4098 | OffSec Cyber Range | `offsec` | `CyberStrike-35B-Q4_K_M` at 66.5 tok/s |
| 4099 | Sentinel Doctor Dashboard | `sentinel` | Auto-heal across 27 nodes |
| 3000 / 3001 / 3002 / 3080 | Gitea, Grafana, OpenHands, LibreChat | `gitea`, `grafana` | Git mirror, metrics, coding studio, chat |

> [!NOTE]
> The count of 28 is 20 numbered portals plus 4 `:3000`-series apps plus the 4 routers and gateways in Part II §3. The brief does not state the arithmetic, but that is the only breakdown that reaches 28.

**Portals the PM will use most.** The Terminal TV Wall at `:4083` shows every live seat without attaching. Cloud Burst at `:4097` shows `POOL_STATE.tsv`, which is directly relevant to the `BH1-17` flapping rows discussed in Part III. The Pre-Review portal at `:4087` is the cut-inspection surface for integration.

### 5. The 87-model vault

The vault is 8.9 TB across `.72` `/opt/models` (4.7 TB) and `.83` `/mnt/ai-models` (4.2 TB). Benchmark coverage is 87 of 87: 69 real-weight GGUF `llama-bench` runs with `rc=0` plus 18 validated non-GGUF or safetensors models. The last model completed was `deepseek-v4.1-flash-abliterated-755b` (`DeepSeek-V4.1-Flash-Abliterated-Q2-imatrix.gguf`, 340.60 GiB, 551.5B active) on `.72`.

The routing law is single-node, no-RPC, per `SERIALIZED-OPT-PIPELINE-LEDGER.md`. Models up to 350 GiB on `.83` and 280 GiB on `.72` run on one T4 plus a 2-socket Skylake AVX-512 host with `--numa distribute -t 48` and no `--rpc`.

**Hot-pinned 24/7 models:**

| Endpoint | Service | Model | `n_ctx` | Notes |
|---|---|---|---|---|
| `.83:18080` | `llama-heretic-18080.service` | `Qwen3.6-35B-A3B-Heretic-Q4_K_M.gguf` | 262,144 (verified to 512K YaRN) | `-np 4 -kvu`, `q8_0/q8_0`, `-fa on`, 5,515 of 15,360 MiB VRAM, `--reasoning-preserve`, 152.6 pp / 65.8 tg tok/s |
| `.72:8000` and `:8082` | `llama-tier1-alpha.service` | `Qwen3-Coder-30B-A3B-Instruct-Q4_K_M.gguf` | 262,144 | 14,863 of 15,360 MiB VRAM, `--reasoning-preserve` |
| `.72:18081` | `llama-qwythos-18081.service` | `Qwythos-9B-Claude-Mythos-5-1M-MTP-Q8_0.gguf` | 524,288 / 1,048,576 | 512K and 1M modes verified |
| `.137`, `.125`, `.72` `:11434` | `ollama` | `CyberStrike-35B-Q4_K_M`, `Qwen2.5-Coder-32B/14B/7B` | 32,768 to 131,072 | `FLASH_ATTN=1`. 66.5 tok/s CyberStrike, 95.4 tok/s Coder-7B on `.137` |

**Engines built off-host on `.125`** (48-core E5-2690 v4, chosen so `.83` and `.72` stayed uncontended):

| Engine | Path | Result |
|---|---|---|
| `ik_llama.cpp` (commit `c446f1e`) | `/opt/ik-llama/bin/{llama-server,llama-bench}` | CUDA arch 75 and AVX512. `+38.2%` prefill on `pp512`, `2.0x` on `tg8`. |
| `gamozolabs/llama.cpp` (`multisocket-cpu-moe`) | `/opt/gamozo-llama/bin/llama-bench` | Fused multi-socket CPU MoE with `MADV_HUGEPAGE`. |
| Mainline `llama-master` `b11716` | `/opt/llama-mainline`, `/opt/llama-rpc` | `GGML_CUDA_FA_ALL_QUANTS=ON`, `GGML_RPC=ON`. Speculative drafter at `/opt/models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-Q4_K_M.gguf`, about 245 MiB VRAM. |

**Host tuning.** All four ESXi hosts (`esxi01`–`04`, `.20.41`–`.44`) are on the High Performance power policy (`Id: 1`). The four GPU VMs carry `/etc/sysctl.d/99-boylenet-llm.conf` with `kernel.numa_balancing=0`, `vm.swappiness=1`, `vm.overcommit_memory=1`, `transparent_hugepage=always` and `GGML_OP_OFFLOAD_MIN_BATCH=2048`. `.125` was repaired by pinning `libnvidia-compute=615.71.09-2ubuntu1`, which restored `nvidia-smi`, `llama-rpc.service` on `:50052` and `ollama.service` on `:11434`.

### 6. VM right-sizing and 24-VM parity (`O3394`)

**vCenter (`10.0.20.70`) sizing after the resize:**

| VM | IP | ESXi | vCPU | RAM | Role |
|---|---|---|---|---|---|
| `Test5` | `.164` | esxi03 | 32 | 250 GiB | Hub, `bd-pm-B`, portals, NGINX, Gitea |
| `AI` (`ai-srv01`) | `.72` | esxi04 | 52 | 320 GiB | Tier-1 coder, Qwythos, OpenWebUI, T4 |
| `AI DEV` (`aidev`) | `.83` | esxi03 | 52 | 400 / 300 GiB | Heretic, T4 |
| `AI Inference` (`ai-infer01`) | `.125` | esxi01 | 48 | 192 / 300 GiB | Qdrant `:6333`, Ollama, T4 |
| `llama` (`ai-ollama01`) | `.228` | esxi02 | 64 | 160 / 300 GiB | Satellite Ollama, T4 |
| `test3big` (`wrk-test04`) | `.82` | esxi04 | 64 | 128 GiB (63 GiB tmpfs) | 32-shard `/dev/shm` pytest, 0.0% iowait |

The resize freed more than 544 GiB from 12 `#COLD-O3336` spares. The `.164` reboot at `03:52 UTC` in Part I happened during this RAM adjustment work.

**Four-platform parity across 24 of 24 VMs.** Receipts are `/home/mboyle/bd-persist/state/O3394-ALL-24VMS-CLAUDE-ABCD-CODEX-KIMI-AGY-AUTH-VERIFIED.md` and `/home/mboyle/bd-persist/state/O3394-FLEET-24VM-PLATFORM-PLUGINS-SKILLS-MODS-PARITY-RECEIPT.md`. The host list is `~/.config/bd/hosts`.

| Platform | Auth location | Result |
|---|---|---|
| Claude Pool A | `~/.claude-a` and `~/.claude` | 24/24, independent OAuth per VM |
| Claude Pool B | `~/.claude-b` | 24/24 |
| Claude Pool C | `~/.claude-c` | 24/24 |
| Claude Pool D | `~/.claude-d` | 24/24 |
| Codex CLI 0.163.0 | `~/.codex/auth.json` | 24/24, 44 skills |
| Kimi CLI 2.2.0 | `~/.kimi-code/credentials/kimi-code-env-0e4f99c69cc27850.json` | 24/24, 8 skills plus MCP config |
| AGY CLI 1.4.2 | `~/.gemini/antigravity-cli/antigravity-oauth-token` | 24/24, 15 to 16 MCP servers, `hooks.json` (2,219 B), `settings.json` (12,552 B) |

Parity details that matter to the PM:

- `claude auth login --claudeai` ran on each VM individually so token refreshes never collide across hosts.
- All 14 MCP servers (`bd`, `caveman`, `context-mode`, `pg-local`, `vmware-clones`, `python-linter`, `type-enforcer`, `bd-fleet`, `ratf`, `bd-bus`, `fleet-sync`, `hivemind-memory`, `serena`, `boylenet-infra`) and 35 to 36 skills were merged into each Claude pool config. The brief says this was done without touching OAuth credentials.
- At least 299 scripts are synced into `~/bin` on all 24 VMs. `fresh149`, `test3big` and `spare4` previously had only 64 to 71 and were repaired.
- `.agents` holds 39 skills and 9 plugins on every VM.
- `CLAUDE.md` (8,710 B), `AGENTS.md` (10,175 B) and `GEMINI.md` are synced across all 24 VMs.

### 7. Tools `bd-pm-B` can call today

```bash
# $0 local GPU coding agent (guarded edit + ruff + mypy + .82 ramdisk pytest)
/home/mboyle/bin/bd-local-coding-agent.py --worktree /home/mboyle/bd-local-wt/<cut-name> \
  --backend auto --test-target tests/test_target.py --prompt "Fix <defect> and verify green."

# Raw local LLM query
/home/mboyle/bin/bd-raw-ai -m qwen3.6-35b-claude-opus-abliterated "Review this diff..."

# Hermetic ramdisk pytest on .82
/home/mboyle/bin/bd-swarm-mod verify --worktree /home/mboyle/bd-local-wt/<cut-name> --pytest-args "tests/test_target.py"

# Knowledge base / HiveMind
/home/mboyle/bin/bd-swarm-mod kb "O1740 dynamic routing and local GPU endpoints"

# Dual Chrome CDP oracles
/home/mboyle/bin/bd-oracle --oracle all --json "Evaluate architectural trade-offs for..."
```

| Mod | Subcommand | Function |
|---|---|---|
| 1 | `context` | Pre-turn quota, GPU slot and health injection in under 100 tokens |
| 2 | `verify` | Post-edit `ruff`, `mypy` and `.82` pytest digest in under 4 s |
| 3 | `publish-lesson` | Rule 25 lessons into Qdrant `fleet_lessons_hivemind` (805 pts) and `bd_master_kb` (455 pts) |
| 4 | `openwebui-filter` | BD-KB inlet context and action buttons in `:8090` and `:8080` |
| 5 | `status` / `doctor` | 27-node, 5-platform self-test and auto-heal |

### 8. Repositories and the knowledge base

Everything is mirrored to GitHub (`github.com/mcboyle/*`) and Gitea (`http://10.0.70.164:3000/mboyle/*`).

| # | Repo | Local path |
|---|---|---|
| 1 | `BulkDownloader` | `/home/mboyle/BulkDownloader` |
| 2 | `UniversalSwarmOS` | `/home/mboyle/UniversalSwarmOS` |
| 3 | `boylenet-swarm-os` | `/home/mboyle/git-repos/boylenet-swarm-os` |
| 4 | `boylenet-openwebui-supersuite` | `/home/mboyle/git-repos/boylenet-openwebui-supersuite` |
| 5 | `boylenet-infra-configs` | `/home/mboyle/git-repos/boylenet-infra-configs` |
| 6 | `boylenet-bd-kb-and-receipts` | `/home/mboyle/git-repos/boylenet-bd-kb-and-receipts` |
| 7 | `boylenet-portals-suite` | Web portals `:4080`–`:4099` |

The knowledge base lives in `/home/mboyle/bd-persist/bd-kb/`, from `00-BD-KB-MASTER-ENCYCLOPEDIA.md` through `06-5PLATFORM-MCP-MODS-HTTPS-AND-215-LEDGER.md`. This document is Volume 11.

> [!NOTE]
> `jarvisos_cockpit` is not in the 7-repo list. That matters for Event 5 below.

---

## Part III: The Outside-the-Harness Events

`bd-pm-B` logged five events in `ROWS-BUGHUNT-WAVE1-20261019.md` that happened outside its own turns. This part gives the complete factual account the sources support, and states exactly where they fall silent.

### 9. Summary

| # | Event | Timestamp (UTC) | Object | Status |
|---|---|---|---|---|
| 1 | Hub `.claude.json` rewrite during the O3394 login and MCP sync | `18:06:43Z` | Hub `.164` Claude config for `~/.claude` and `~/.claude-a..d` | Plausible cause, **unconfirmed** |
| 2 | Direct edit of `~/bin/bd-pool-usage-refresher.py` | Not stated | `/home/mboyle/bin/bd-pool-usage-refresher.py` | Drift **confirmed by `BH1-17`**; actor and time **not in context** |
| 3 | `role-claims.tsv` reset | `11:29:56Z` | `role-claims.tsv` (path not given) | **Not in context** |
| 4 | `IMPROVEMENT_BACKLOG.md` snapshot | `02:30Z` | `IMPROVEMENT_BACKLOG.md` (path not given) | **Not in context** |
| 5 | `jarvisos_cockpit` push of `6899fda` | Not stated | `jarvisos_cockpit` repo | **Not in context** |

### 10. Event 1: the `18:06:43Z` hub `.claude.json` rewrite

**What the sources say.** O3394 did two things on every one of the 24 VMs, including the hub. It ran `claude auth login --claudeai` per VM, and it merged 14 MCP servers and 35 to 36 skills into each pool config. The brief states the merge did not touch per-VM OAuth credentials.

**Why that fits the event.** Claude Code keeps MCP and account state in `.claude.json`. A rewrite of the hub copy during the O3394 window is consistent with either the login step or the merge step running on `.164`.

**What is not established.** Nothing in the sources ties either step to `18:06:43Z`. The sources give no file path, no writer process and no diff. The O3394 attribution is an inference and should be called unconfirmed until a `stat` of the hub config files and a listing of the O3394 receipts near that time are compared.

**Why it matters.** The rewrite happened while `bd-pm-B` was live on transcript `5f51e26c-982c-4726-9e68-aa66912e330c`. The build-track invariant was zero disruption to live seats. A config rewrite under a running seat is a boundary case for that invariant even if the seat kept running.

### 11. Event 2: the `bd-pool-usage-refresher.py` direct edit

**What the sources say.** Row `BH1-17` (lane H, size M, severity MED) describes two writers flapping the grok and kimi rows in `POOL_STATE` between OK and UNKNOWN every 4 to 5 minutes. The chain of evidence is `IMPROVEMENTS/bd-claude-swarm-C-20261019T0305Z.md`, `IMPROVEMENTS/bd-claude-swarm-C-20261019T0451Z.md` and `state/FINDING-pmC-pool-state-fabricated-rows-0508Z.md`. The finding said the refresher hard-coded OK rows. By `19:07Z` the live `~/bin` copy no longer had those literals, and no commit names the fix. That is what "drifted outside the harness" means in `BH1-17`.

**What is confirmed.** The drift is confirmed by the ledger itself. The live copy changed and the harness has no record of the change.

**What is not established.** The actor and the time of the edit are not in the sources.

**The check the ledger prescribes.** `BH1-17` STEP0 is a diff of the live copy against the tracked staged copies. That diff is what would turn "direct edit" from a description into a verified finding. `BH1-07` is related: it covers refresher residuals (the A1 seed guard keyed on `fetched_ms`, and the A2 comma form of the `BD_*` knobs) and asks whether the Pool A 403/429 cause is gone after the O3394 full logins. If the O3394 logins cleared the 403/429 cause, that is the same O3394 work described in Part II §6 and Event 1.

**Where to see it live.** The Cloud Burst portal at `:4097` renders `POOL_STATE.tsv`. The flap should be visible there if it is still occurring.

### 12. Events 3, 4 and 5: not in the sources

**Event 3, `role-claims.tsv` reset at `11:29:56Z`.** The sources contain no record of this. The nearest item is `BH1-09`, a worker's registry claim lost on remote host `.186` at `17:45Z`. That is a different host, a different time and a different object. Treat `BH1-09` as unrelated unless evidence links the two.

**Event 4, `IMPROVEMENT_BACKLOG.md` snapshot at `02:30Z`.** The sources mention the `IMPROVEMENTS/` directory, with 10 files dated 20261019, but never this file. No cause, actor or effect can be stated.

**Event 5, `jarvisos_cockpit` push of `6899fda`.** Neither the repo nor the commit appears in the sources. The 7-repo mirror list in Part II §8 omits `jarvisos_cockpit`. The only cockpit-adjacent row is `BH1-16` (lane `BD-cockpit`, `dcc-f9`), and nothing links it to this push. If `jarvisos_cockpit` sits outside the mirrored set, any push to it bypasses the harness by construction, which would explain why the PM saw it only from the outside.

### 13. Proposed read-only verification

These commands were proposed by the provenance analyst and have **not** been run. They are the shortest path from "unconfirmed" to "confirmed" for each event.

| Event | Command |
|---|---|
| 1 | `stat -c '%y %n' ~/.claude.json ~/.claude-{a,b,c,d}/.claude.json` |
| 1 | `ls -l --time-style=full-iso /home/mboyle/bd-persist/state/O3394-*` |
| 2 | `diff /home/mboyle/bin/bd-pool-usage-refresher.py <tracked staged copy>` and `stat -c '%y' /home/mboyle/bin/bd-pool-usage-refresher.py` |
| 2 | `git -C /home/mboyle/BulkDownloader log --follow -- <tracked refresher path>` |
| 3 | `find /home/mboyle/bd-persist -name role-claims.tsv -printf '%TY-%Tm-%TdT%TH:%TM:%TSZ %p\n'` |
| 4 | `find /home/mboyle/bd-persist -name IMPROVEMENT_BACKLOG.md -printf '%T+ %p\n'` |
| 5 | `git -C <jarvisos_cockpit path> show --stat 6899fda` and `git -C <path> reflog` |

---

## Part IV: Caveats and Open Items

### 14. Source limits

- Both source files were truncated at 25,000 bytes. The brief cut off partway through the "Master Ledgers & `O3394` / `O1789` Receipts" list, at `/home/mboyle/bd-persist/harness-work/O1789/infra-upgr…`. Nothing past that point is documented here.
- The 28-portal arithmetic in Part II §4 is reconstructed, not quoted.
- The O3394 attribution for Event 1 is an inference.
- Events 3, 4 and 5 have no supporting record in the sources. This volume does not assign them a cause.

### 15. Open items for `bd-pm-B`

1. Run the Event 1 `stat` and O3394 listing to confirm or reject the attribution.
2. Run `BH1-17` STEP0 (the live-versus-staged diff of the refresher) and record the actor if the diff exposes one.
3. Supply the PM's original observation notes for Events 3, 4 and 5 so the next volume can be sourced.
4. Confirm whether `jarvisos_cockpit` belongs in the 7-repo mirror set. If it should, add it so future pushes land inside the harness.
5. Request the untruncated brief so the `O3394` / `O1789` receipt list can be completed.

### 16. Quick reference card

| Need | Go to |
|---|---|
| See every live seat without attaching | `:4083` Terminal TV Wall |
| Check `POOL_STATE.tsv` and quota | `:4097` Cloud Burst |
| Inspect a cut before merge | `:4087` Pre-Review |
| Run a command on any VM programmatically | `:4079` gateway, `/vm/<vm>/execute` |
| Free on-prem inference | `:4010/v1` orchestrator or `bd-raw-ai` |
| Cloud or local model by alias | `:4000` LiteLLM |
| Hermetic pytest | `bd-swarm-mod verify` on `.82` |
| Fleet health and auto-heal | `bd-swarm-mod doctor` or `:4099` Sentinel |
| Publish a Rule 25 lesson | `bd-swarm-mod publish-lesson` |
| Search the knowledge base | `bd-swarm-mod kb "<query>"` or `:4086` Dreamer |
| Crash recovery record for the `03:52 UTC` reboot | `AGENT173-POST-CRASH-PM-AND-SESSIONS-RESTORE-RECEIPTS.md` |
| 24-VM parity receipts | `/home/mboyle/bd-persist/state/O3394-*` |

---

# Appendix: Detailed Specialist Subagent Reports (`AGY claude+gpt` Sonnet/Haiku Fanout)

## Subagent Report 1: Parallel Infra & AI Blitz Historian (`AGY claude+gpt: claude-sonnet-5-5-high`)

# Parallel Infra & AI Blitz: How the Sovereign AI Stack Was Built Without Interrupting the BURN Train

**Source:** `/home/mboyle/bd-persist/PM-CATCHUP-BRIEF-EVERYTHING-BUILT-20261019.md`, written `2026-10-19T22:30:00Z` by `Agent-182`.
**Audience:** `bd-pm-B` and the Command Squad (`integrator`, `dispatch`, `lensrouter`, `audit`).
**Inbox mirror:** `/home/mboyle/bd-persist/inbox/PM/BRIEF-AGY-FULL-INFRA-AND-TOOLS-CATCHUP-20261019.md`. It was staged quietly, with no tmux kick.

> [!IMPORTANT]
> **Operating invariant: 100% non-blocking parallel execution.** The operator and the AGY Council (`d61a0f86`) built the stack alongside `bd-pm-B`'s BURN / UNPARK train. That train covers `ORDER-unpark-all.md`, `BUGHUNT-WAVE1` and the live `tmux` seats on `.164` and the remote worker bands. None of it was disrupted.

---

## 1. Operating Model: Parallel Build, Protected Train

### 1.1 Who did what

| Layer | Actors | Scope |
|---|---|---|
| **PM train (protected)** | `bd-pm-B`, `bd-usage-a..d`, 10 seat VMs (`.164`, `.84`, `.80`, `.85`, `.52`, `.249`, `.193`, `.51`, `.184`, `.194`) | `BulkDownloader` queue burndown, integration, bug-hunt waves. |
| **Parallel build** | AGY Council (`d61a0f86`), 230-Swarm (`UNIT-001`..`UNIT-230`), Local GPU Agents (`Agent-105`..`Agent-182`) | Infrastructure, AI vault, web portals, 24-VM parity. |

### 1.2 Isolation guarantees

| Mechanism | Detail | Evidence |
|---|---|---|
| **Seat protection** | More than 64 live `tmux` sessions across the 10 seat VMs were left untouched. | `PM-COORDINATION-16VM-FLEET-READY-PLAN.md` |
| **Crash recovery** | `.164` rebooted at about `03:52 UTC` during vCenter RAM adjustments. `Agent-173` restored **9/9** Claude sessions, including `bd-pm-B` on transcript `5f51e26c-982c-4726-9e68-aa66912e330c` and `bd-usage-a..d`. It also verified **33/33** remote `tmux` sessions intact. | `AGENT173-POST-CRASH-PM-AND-SESSIONS-RESTORE-RECEIPTS.md` |
| **Noise suppression** | During `O3416`, two background bash loop crons (`ag-cron-e11ed606`, `ag-cron-efbfcfbe`) collided with `bd-pm-B`'s active turn and were disarmed. | `PAUSED-O3416-messaging-crons.md` |
| **Handoff channel** | Passive file drops in `/home/mboyle/bd-persist/inbox/PM/`, with no tmux kicks. | — |

---

## 2. The 230-Swarm Mega-Blitz (230 / 230, 100.0%)

The ledger is `MASTER-230-SWARM-MEGA-BLITZ-LEDGER.md`. It ran as 8 cohorts across different pools and hardware, so no single quota pool or host was saturated.

| Cohort | Units | Count | Workforce / Driver | Focus |
|---|---|---|---|---|
| **I** | `UNIT-001`–`046` | 46 | Native AGY subagents (`Agent-105`–`150`) | Portals, OpenWebUI, vSAN / 25GbE RoCE, dual browser oracles. Reconciled `215/215` queue rows across `O1670`, `P3-LENS`, `BH1`, `P2-FIX`, `APP-DL-FIX`, `APP-FEATURE` and `unpark-open`. |
| **II** | `047`–`074` | 28 | Claude Code, Pools A/B/C/D (`bd-claude-28worker-blitz.py`) | Blitz workers |
| **III** | `075`–`102` | 28 | OpenAI Codex CLI `gpt-5.4`/`sol` (`bd-codex-28worker-blitz.py`) | Blitz workers |
| **IV** | `103`–`126` | 24 | Moonshot Kimi K2.8 CLI (`bd-kimi-24worker-blitz.py`) | UI/UX |
| **V** | `127`–`146` | 20 | Local GPU pool: `.83` Heretic, `.137` RTX 2080 Ti, `.228` Tesla T4 (`bd-local-20gpu-10oracle-blitz.py`) | $0 on-prem inference |
| **VI** | `147`–`156` | 10 | Dual Chrome CDP oracles: `.137:9222` (BattleStation), `.181:9222` (Bittorrent) | $0 marginal API cost |
| **VII** | `157`–`186` | 30 | Command Squad + `.82` (`wrk-test04`) 64-core, 32-shard `/dev/shm` ramdisk (`bd-30squad-32ramdisk-blitz.py`) | Hermetic test lanes |
| **VIII** | `187`–`230` | 44 | Sub-swarm | NGINX `:443` Cloudflare DNS-01 wildcard HTTPS, 7-repo GitHub/Gitea publishing |

---

## 3. Sovereign AI Stack: Routers and Gateways

All services run on Hub `.164` (`10.0.70.164`) or AI host `.72` (`10.0.70.72`). They returned `HTTP 200` in `AGENT153`, `AGENT156` and `AGENT176`. They sit behind NGINX `:443` with a Cloudflare DNS-01 wildcard cert for `*.boylenet.themfboyles.org` and `*.boylenet.themfboyles.com`, managed by `/home/mboyle/bin/bd-letsencrypt-cloudflare`.

| Port / Host | Service | Vhost | Key capabilities |
|---|---|---|---|
| `.164:4010` | Sovereign Local GPU Orchestrator (`bd-gpu-orchestrator.service`) | `gpu.boylenet…` | OpenAI-compatible `/v1` over all 87 vault models. Advertises 262,144–1,048,576 context and `reasoning: true`. Uses live `/slots` idle detection on `.72:8000` and `.83:18080`, with overflow to `.137:11434` (2080 Ti) and `.228:11434` (T4). Injects `<think>` and `reasoning_content`. Enforces the Argon-4 safety shield. |
| `.164:4000` | LiteLLM 300-model router (`litellm-proxy`) | `litellm.boylenet…` | 300+ local and cloud aliases, including `tier1-qwen3-coder-30b-a3b`, `claude-opus-5-5`, `fable-5-1`, `kimi-k2.8`, `gpt-6.1-sol` and the 16 agent personas. Routes through `:4010/v1` with fallback chains. |
| `.164:8090` and `.72:8080` | OpenWebUI Super-Suite | `openwebui…`, `openwebui72…` | 16 agents, 50 toolkits, 128 skills/prompts and 8 pipelines. Pipelines include the BD-KB auto-context inlet and the Rule 25 lesson outlet. |
| `.164:4079` | Universal Tool and 27-VM Terminal Gateway (`bd-openwebui-tool-gateway.py`) | `gateway.boylenet…` | OpenAPI 3.1 at `/openapi/infra.json` and `/tools/{tool}/call`. VM exec and file APIs at `/vm/<vm>/execute` and `/vm/<vm>/files/list\|read`. Includes `.72`/`.83` benchmark contention guards. |

---

## 4. The 28 Web Portals

The brief lists these as the `:4080`–`:4099` suite plus `:3000`-series apps.

| Port | Portal | Vhost prefix | Highlights |
|---|---|---|---|
| 4080 | Command Deck (Dashy) | `dashy` | `/api/hardware` telemetry feed. |
| 4081 | Executive Briefing | `briefing` | Burn rates and milestones. |
| 4082 | Multi-AI Hub | `portal` | Side-by-side arena and consensus fanout. |
| 4083 | Terminal TV Wall (`bd-web-terminal-drawer.service`) | `terminal` | 116 live tiles with zero DOM truncation. Covers 31 local `tmux` sessions, 7 remote GPU/compute engines, 16 agents, 24 AGY subagents and 10 cohort tiles. |
| 4084 | 87-Model Vault Storefront | `models` | Catalog, leaderboard, `/api/models`, hot-swap profiles. |
| 4085 | Boylenet OS Web Shell | `os` | Desktop-in-browser. |
| 4086 | HiveMind Memory Dreamer | `dreamer` | Qdrant graph explorer over `bd_master_kb`, `fleet_lessons_hivemind` and `boylenet_episodic_memory`. |
| 4087 | Gitea Pre-Review and AST Diff | `prereview` | Cut inspection and `bd-ast-diff`. |
| 4088 | 165-View 60fps Visualizer | `topology` | 12U rack, 4 ESXi hosts, 107 TB NVMe vSAN, 25GbE RoCE, 27 VMs. |
| 4089 | Credentials Vault UI | `credentials` | Masked inventory and DNS/token audit. |
| 4090 | Jarvis Ops Center | `jarvis` | Natural-language ops and `/api/git-repos`. |
| 4091 | OpenCode Web Studio (A) | `harnesses` | Wired to `:4000` / `:4010`. |
| 4092 | Fleet Chat and Bus Monitor | `pwa` | `bd-bus` WAL viewer, mobile PWA. |
| 4093 | Voice/Vision Bridge | `voice` | Whisper STT, Kokoro TTS, `Qwen2.5-VL-7B`, `Pixtral-12B`. |
| 4094 | Deep Research and RAG | `research` | SearXNG `:8888`, DevDocs `:8088`, Qdrant `:6333`. |
| 4095 | LobeChat and Airgap Controller | `lobechat` | `<262144:fc:reasoning>` on 7 core models. |
| 4096 | OpenCode Web IDE / SillyTavern bridge | `opencode` | 114 LiteLLM and 95 orchestrator models at `context: 262144, output: 16384`. |
| 4097 | Cloud Burst and Quota Telemetry | `burst` | `POOL_STATE.tsv`, O1740 ledger. |
| 4098 | OffSec Cyber Range | `offsec` | `CyberStrike-35B-Q4_K_M` at 66.5 tok/s. |
| 4099 | Sentinel Doctor Dashboard | `sentinel` | Auto-heal across 27 nodes. |
| 3000 / 3001 / 3002 / 3080 | Gitea, Grafana, OpenHands, LibreChat | `gitea`, `grafana` | Git mirror, metrics, coding studio, chat. |

> [!NOTE]
> The brief's tables list 20 numbered portals (`:4080`–`:4099`) plus 4 `:3000`-series apps, and 4 router and gateway services in §2.1. That is 28 entries in total, which matches the "28 web portals" figure, though the excerpt does not state the arithmetic explicitly.

---

## 5. The 87-Model Vault (8.9 TB)

### 5.1 Benchmark completion: 87 / 87 (100.0%)

| Item | Value |
|---|---|
| Storage | `.72` `/opt/models` (4.7 TB) plus `.83` `/mnt/ai-models` (4.2 TB) |
| Coverage | 69 real-weight GGUF `llama-bench` runs (`rc=0`) plus 18 validated non-GGUF/safetensors models |
| Final model (#87) | `deepseek-v4.1-flash-abliterated-755b` (`DeepSeek-V4.1-Flash-Abliterated-Q2-imatrix.gguf`, 340.60 GiB, 551.5B active) completed on `.72` |
| Routing law | Single-node, no-RPC, per `SERIALIZED-OPT-PIPELINE-LEDGER.md`. Models up to 350 GiB on `.83` and 280 GiB on `.72` run on 1× T4 plus 2-socket Skylake AVX-512 with `--numa distribute -t 48` and no `--rpc`. |

### 5.2 Hot-pinned 24/7 models

| Endpoint | Service | Model | `n_ctx` | Notes |
|---|---|---|---|---|
| `.83:18080` | `llama-heretic-18080.service` | `Qwen3.6-35B-A3B-Heretic-Q4_K_M.gguf` | 262,144 (verified to 512K YaRN) | `-np 4 -kvu`, `q8_0/q8_0`, `-fa on`, 5,515 / 15,360 MiB VRAM, `--reasoning-preserve`, 152.6 pp / 65.8 tg tok/s |
| `.72:8000` and `:8082` | `llama-tier1-alpha.service` | `Qwen3-Coder-30B-A3B-Instruct-Q4_K_M.gguf` | 262,144 | 14,863 / 15,360 MiB VRAM, `--reasoning-preserve` |
| `.72:18081` | `llama-qwythos-18081.service` | `Qwythos-9B-Claude-Mythos-5-1M-MTP-Q8_0.gguf` | 524,288 / 1,048,576 | 512K and 1M modes verified |
| `.137`, `.125`, `.72` `:11434` | `ollama` | `CyberStrike-35B-Q4_K_M`, `Qwen2.5-Coder-32B/14B/7B` | 32,768–131,072 | `FLASH_ATTN=1`. 66.5 tok/s (CyberStrike), 95.4 tok/s (Coder-7B on `.137`) |

### 5.3 Engines built off-host on `.125`

`.125` is a 48-core E5-2690 v4. Building there kept `.83` and `.72` uncontended.

| Engine | Path | Result |
|---|---|---|
| `ik_llama.cpp` (commit `c446f1e`) | `/opt/ik-llama/bin/{llama-server,llama-bench}` | Built with CUDA arch 75 and AVX512. `+38.2%` prefill (`pp512`) and `2.0x` `tg8`. |
| `gamozolabs/llama.cpp` (`multisocket-cpu-moe`) | `/opt/gamozo-llama/bin/llama-bench` | Fused multi-socket CPU MoE with `MADV_HUGEPAGE`. |
| Mainline `llama-master` `b11716` | `/opt/llama-mainline`, `/opt/llama-rpc` | `GGML_CUDA_FA_ALL_QUANTS=ON`, `GGML_RPC=ON`. Speculative drafter `/opt/models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-Q4_K_M.gguf`, needing about 245 MiB VRAM. |

**Host tuning:**
- All 4 ESXi hosts (`esxi01`–`04`, `.20.41`–`.44`) report `Id: 1` (High Performance) from `esxcli hardware power policy get`.
- The 4 GPU VMs carry `/etc/sysctl.d/99-boylenet-llm.conf`: `kernel.numa_balancing=0`, `vm.swappiness=1`, `vm.overcommit_memory=1`, `transparent_hugepage=always`, `GGML_OP_OFFLOAD_MIN_BATCH=2048`.
- `.125` was repaired by pinning `libnvidia-compute=615.71.09-2ubuntu1`. That restored `nvidia-smi`, `llama-rpc.service` (`:50052`) and `ollama.service` (`:11434`).

---

## 6. VM Right-Sizing and 24-VM Parity (`O3394`)

### 6.1 vCenter (`10.0.20.70`) sizing

| VM | IP | ESXi | vCPU | RAM | Role |
|---|---|---|---|---|---|
| `Test5` | `.164` | esxi03 | 32 | 250 GiB | Hub, `bd-pm-B`, portals, NGINX, Gitea |
| `AI` (`ai-srv01`) | `.72` | esxi04 | 52 | 320 GiB | Tier-1 coder, Qwythos, OpenWebUI, T4 |
| `AI DEV` (`aidev`) | `.83` | esxi03 | 52 | 400 / 300 GiB | Heretic, T4 |
| `AI Inference` (`ai-infer01`) | `.125` | esxi01 | 48 | 192 / 300 GiB | Qdrant `:6333`, Ollama, T4 |
| `llama` (`ai-ollama01`) | `.228` | esxi02 | 64 | 160 / 300 GiB | Satellite Ollama, T4 |
| `test3big` (`wrk-test04`) | `.82` | esxi04 | 64 | 128 GiB (63 GiB tmpfs) | 32-shard `/dev/shm` pytest, 0.0% iowait |

The resize freed 544+ GiB from 12 `#COLD-O3336` spares.

### 6.2 Four-platform parity across 24 / 24 VMs

Receipts:
- `/home/mboyle/bd-persist/state/O3394-ALL-24VMS-CLAUDE-ABCD-CODEX-KIMI-AGY-AUTH-VERIFIED.md`
- `/home/mboyle/bd-persist/state/O3394-FLEET-24VM-PLATFORM-PLUGINS-SKILLS-MODS-PARITY-RECEIPT.md`

| Platform | Auth location | Parity result |
|---|---|---|
| Claude Pool A | `~/.claude-a` and `~/.claude` | 24/24, independent OAuth per VM |
| Claude Pool B | `~/.claude-b` | 24/24 |
| Claude Pool C | `~/.claude-c` | 24/24 |
| Claude Pool D | `~/.claude-d` | 24/24 |
| Codex CLI 0.163.0 | `~/.codex/auth.json` | 24/24, 44 skills |
| Kimi CLI 2.2.0 | `~/.kimi-code/credentials/kimi-code-env-0e4f99c69cc27850.json` | 24/24, 8 skills plus MCP config |
| AGY CLI 1.4.2 | `~/.gemini/antigravity-cli/antigravity-oauth-token` | 24/24, 15–16 MCP servers, `hooks.json` (2,219 B), `settings.json` (12,552 B) |

**Parity details:**
- `claude auth login --claudeai` ran on each VM individually, so token refreshes never collide across hosts.
- At least 299 scripts are synced into `~/bin` on all 24 VMs. `fresh149`, `test3big` and `spare4` were repaired; they previously had only 64–71.
- `.agents` holds 39 skills and 9 plugins on every VM.
- All 14 MCP servers and 35–36 skills were merged into each Claude pool config without touching OAuth credentials. The servers are `bd`, `caveman`, `context-mode`, `pg-local`, `vmware-clones`, `python-linter`, `type-enforcer`, `bd-fleet`, `ratf`, `bd-bus`, `fleet-sync`, `hivemind-memory`, `serena` and `boylenet-infra`.
- `CLAUDE.md` (8,710 B), `AGENTS.md` (10,175 B) and `GEMINI.md` are synced across all 24 VMs.

---

## 7. Operator Commands (From the Brief)

```bash
# $0 local GPU coding agent (guarded edit + ruff + mypy + .82 ramdisk pytest)
/home/mboyle/bin/bd-local-coding-agent.py --worktree /home/mboyle/bd-local-wt/<cut-name> \
  --backend auto --test-target tests/test_target.py --prompt "Fix <defect> and verify green."

# Raw local LLM query
/home/mboyle/bin/bd-raw-ai -m qwen3.6-35b-claude-opus-abliterated "Review this diff..."

# Hermetic ramdisk pytest on .82
/home/mboyle/bin/bd-swarm-mod verify --worktree /home/mboyle/bd-local-wt/<cut-name> --pytest-args "tests/test_target.py"

# Knowledge base / HiveMind
/home/mboyle/bin/bd-swarm-mod kb "O1740 dynamic routing and local GPU endpoints"

# Dual Chrome CDP oracles
/home/mboyle/bin/bd-oracle --oracle all --json "Evaluate architectural trade-offs for..."
```

**`bd-swarm-mod` modes:**

| Mod | Subcommand | Function |
|---|---|---|
| 1 | `context` | Pre-turn quota, GPU slot and health injection in under 100 tokens |
| 2 | `verify` | Post-edit `ruff`, `mypy` and `.82` pytest digest in under 4 s |
| 3 | `publish-lesson` | Rule 25 lessons into Qdrant `fleet_lessons_hivemind` (805 pts) and `bd_master_kb` (455 pts) |
| 4 | `openwebui-filter` | BD-KB inlet context and action buttons in `:8090` and `:8080` |
| 5 | `status` / `doctor` | 27-node, 5-platform self-test and auto-heal |

---

## 8. Repositories and Canonical Files

Everything is mirrored to GitHub (`github.com/mcboyle/*`) and Gitea (`http://10.0.70.164:3000/mboyle/*`).

| # | Repo | Local path |
|---|---|---|
| 1 | `BulkDownloader` | `/home/mboyle/BulkDownloader` |
| 2 | `UniversalSwarmOS` | `/home/mboyle/UniversalSwarmOS` |
| 3 | `boylenet-swarm-os` | `/home/mboyle/git-repos/boylenet-swarm-os` |
| 4 | `boylenet-openwebui-supersuite` | `/home/mboyle/git-repos/boylenet-openwebui-supersuite` |
| 5 | `boylenet-infra-configs` | `/home/mboyle/git-repos/boylenet-infra-configs` |
| 6 | `boylenet-bd-kb-and-receipts` | `/home/mboyle/git-repos/boylenet-bd-kb-and-receipts` |
| 7 | `boylenet-portals-suite` | Web portals `:4080`–`:4099` |

The 6-volume knowledge base lives in `/home/mboyle/bd-persist/bd-kb/`, from `00-BD-KB-MASTER-ENCYCLOPEDIA.md` through `06-5PLATFORM-MCP-MODS-HTTPS-AND-215-LEDGER.md`.

> [!NOTE]
> The source brief was truncated at 25,000 bytes, partway through the "Master Ledgers & `O3394` / `O1789` Receipts" list (`/home/mboyle/bd-persist/harness-work/O1789/infra-upgr…`). I did not document anything past that point.

## Subagent Report 2: Outside-the-Harness Event Provenance Analyst (`AGY claude+gpt: claude-sonnet-5-5-low`)

## Outside-the-Harness Events Noticed by `bd-pm-B` (2026-10-19)

> [!WARNING]
> **Provenance limits.** The supplied `<CONTEXT>` is truncated (25,000 bytes of each file). It supports only some of these events, and only partly. I have not invented timestamps, actors, or causes. Where the context is silent, the table says **NOT IN CONTEXT**. The commands in §4 are proposals. I did not run them.

### 1. Summary table

| # | Event | Timestamp (UTC) | Path / object | What the context supports | Status |
|---|---|---|---|---|---|
| 1 | Hub `.claude.json` rewrite during the O3394 24-VM login and MCP sync | `18:06:43Z` | Hub `.164` (`10.0.70.164`), Claude config for `~/.claude` and pools `~/.claude-a..d` | **Partial.** O3394 (see §2) merged 14 MCP servers and 35–36 skills into every pool config on 24/24 VMs "without touching per-VM OAuth credentials". It also ran `claude auth login --claudeai` on each VM. Nothing in the context links these steps to a write at `18:06:43Z`. | Plausible cause, **unconfirmed** |
| 2 | Direct edit of `~/bin/bd-pool-usage-refresher.py` | Not stated | `/home/mboyle/bin/bd-pool-usage-refresher.py` | **Partial.** Row `BH1-17` in `ROWS-BUGHUNT-WAVE1-20261019.md` says the live `~/bin` copy "no longer has the literals (19:07Z)", but no commit names the fix, so the copy "drifted outside the harness". | Drift **confirmed by BH1-17**; actor and time **NOT IN CONTEXT** |
| 3 | `role-claims.tsv` reset | `11:29:56Z` | `role-claims.tsv` (path not given) | **None.** The only related item is `BH1-09` (a worker's registry claim lost on remote host `.186` at 17:45Z), which is a different event. | **NOT IN CONTEXT** |
| 4 | `IMPROVEMENT_BACKLOG.md` snapshot | `02:30Z` | `IMPROVEMENT_BACKLOG.md` (path not given) | **None.** The context mentions the `IMPROVEMENTS/` directory (10 files dated 20261019) but not this file. | **NOT IN CONTEXT** |
| 5 | `jarvisos_cockpit` push of `6899fda` | Not stated | `jarvisos_cockpit` repo | **None.** The `jarvisos_cockpit` repo and commit `6899fda` do not appear in the context. The 7-repo list in the brief omits `jarvisos_cockpit`. The only cockpit-related row is `BH1-16` (lane `BD-cockpit`, `dcc-f9`). | **NOT IN CONTEXT** |

You asked for 4 events but listed 5. I treated all 5 separately.

### 2. Event 1: `18:06:43Z` hub `.claude.json` rewrite (O3394)

**Background from the brief** (`PM-CATCHUP-BRIEF-EVERYTHING-BUILT-20261019.md` §4.2):

| Item | Value |
|---|---|
| Receipts | `/home/mboyle/bd-persist/state/O3394-ALL-24VMS-CLAUDE-ABCD-CODEX-KIMI-AGY-AUTH-VERIFIED.md` and `/home/mboyle/bd-persist/state/O3394-FLEET-24VM-PLATFORM-PLUGINS-SKILLS-MODS-PARITY-RECEIPT.md` |
| Host list | `~/.config/bd/hosts` (24 VMs) |
| Login command | `claude auth login --claudeai`, run on each VM so token refreshes never collide |
| Pool A | `~/.claude-a` and `~/.claude` |
| Pool B | `~/.claude-b` |
| Pool C | `~/.claude-c` |
| Pool D | `~/.claude-d` |
| MCP servers merged | 14: `bd`, `caveman`, `context-mode`, `pg-local`, `vmware-clones`, `python-linter`, `type-enforcer`, `bd-fleet`, `ratf`, `bd-bus`, `fleet-sync`, `hivemind-memory`, `serena`, `boylenet-infra` |
| Skills merged | 35–36 per pool |
| Other platforms | Codex 0.163.0, Kimi 2.2.0, AGY 1.4.2 (all 24/24) |

**Explanation.** The brief says MCP servers and skills were merged into each pool config. Claude Code keeps its MCP and account state in `.claude.json`, so a rewrite of the hub's copy at `18:06:43Z` fits an O3394 login or merge step running on `.164`. This is an inference. The context does not give the file's path, writer process, or diff.

**Why it matters.** A hub-side rewrite while `bd-pm-B` was live (transcript `5f51e26c-982c-4726-9e68-aa66912e330c`) happened outside the PM's own turn. The brief's invariant is "zero disruption" to live seats.

### 3. Events 2–5: what can and cannot be stated

**Event 2 (`bd-pool-usage-refresher.py`).**
- `BH1-17` (lane H, size M, severity MED) describes two writers that flap the grok and kimi `POOL_STATE` rows between OK and UNKNOWN every 4–5 minutes.
- Related evidence: `IMPROVEMENTS/bd-claude-swarm-C-20261019T0305Z.md`, `-T0451Z.md`, and `state/FINDING-pmC-pool-state-fabricated-rows-0508Z.md`.
- The finding said the refresher hard-codes OK rows. By `19:07Z` the live copy no longer had those literals.
- `BH1-17` prescribes **STEP0: diff the live copy against the tracked staged copies**. That is the check that would confirm the "direct edit".
- `BH1-07` is related. It covers refresher residuals (the A1 seed guard keys on `fetched_ms`, and the A2 comma form of the `BD_*` knobs). It also asks whether the Pool A 403/429 cause is gone after the O3394 full logins.

**Events 3, 4, 5.** The context contains no record of any of them. For event 3, the nearest item is `BH1-09`, a different incident (a claim lost on a remote host). Treat it as unrelated unless evidence links the two. I will not assign cause, actor, or effect.

### 4. Proposed read-only verification (not executed)

| Goal | Command |
|---|---|
| Event 1: stat the hub config files | `stat -c '%y %n' ~/.claude.json ~/.claude-{a,b,c,d}/.claude.json` |
| Event 1: look for the O3394 run near 18:06Z | `ls -l --time-style=full-iso /home/mboyle/bd-persist/state/O3394-*` |
| Event 2: compare live and tracked copies | `diff /home/mboyle/bin/bd-pool-usage-refresher.py <tracked staged copy>` and `stat -c '%y' /home/mboyle/bin/bd-pool-usage-refresher.py` |
| Event 2: see what the harness history says | `git -C /home/mboyle/BulkDownloader log --follow -- <tracked refresher path>` |
| Event 3: locate the file and its mtime | `find /home/mboyle/bd-persist -name role-claims.tsv -printf '%TY-%Tm-%TdT%TH:%TM:%TSZ %p\n'` |
| Event 4: locate the file and its mtime | `find /home/mboyle/bd-persist -name IMPROVEMENT_BACKLOG.md -printf '%T+ %p\n'` |
| Event 5: find the commit | `git -C <jarvisos_cockpit path> show --stat 6899fda` and `git -C <path> reflog` |

### 5. Open items

1. Supply the remainder of the context, or the PM's original observation notes, for events 3–5.
2. Confirm whether `jarvisos_cockpit` is a repo outside the 7-repo mirror list. If it is, any push to it bypasses the harness by construction.
3. Event 1 needs the file's real path and a diff before the O3394 attribution can be called confirmed.
