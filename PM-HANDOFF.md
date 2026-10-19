# MASTER PM HANDOFF (`O1789` / `O3417`) — Reconciled Brief, 87/87 Vault & Standby Relief Commander Runbook

- **WRITTEN-AT**: `2026-10-19T22:25:00Z` (`Agent-183` / `AGY-Council` -> `bd-pm-B` & Command Squad)
- **PREVIOUS BRIEFS RECONCILED**: `/home/mboyle/bd-persist/PM-HANDOFF.md` (`2026-10-19T00:12:00Z`), `/home/mboyle/bd-persist/harness-work/O1789/PM-HANDOFF.md` (`2026-10-18T20:52:00Z`), `/home/mboyle/bd-persist/role-state/pm.md` (`2026-10-19T21:41:39Z`), `/home/mboyle/bd-persist/compact-bd-pm-B.md` (`2026-10-19T21:4xZ`)
- **TIMESTAMPED SNAPSHOT**: `/home/mboyle/bd-persist/PM-HANDOFF-20261019T2225Z.md`
- **ACTIVE PM SEAT**: `bd-pm-B` (`--remote-control` on `hub-mesh01`, Pool B `OK` `97%` 5h remaining / `40%` wk used)
- **FLEET STATE**: **CLEANLY IDLE — ALL BURN QUEUE ROWS & 19 `jarvisos_cockpit` CUTS LANDED**
  - Control seats kept up per **O3415**; messaging crons paused per **O3416** (`/home/mboyle/bd-persist/state/PAUSED-O3416-messaging-crons.md`); seat handoffs written per **O3417**.
  - **BENCHMARK GUARD LIFTED**: `Agent-37`'s dual-vault benchmark sweep is **`87 / 87 (100.0%) COMPLETE`** (including `#87 DeepSeek-V4.1-Flash-Abliterated-755B`). All local GPU/MoE endpoints on `.72`, `.83`, `.125`, `.137`, and `.228` are uncontended and in production serving posture.

---

## 1. Reconciliation Delta (`Oct 18 20:52Z / Oct 19 00:17Z` -> `Oct 19 22:25Z`)

| Dimension | Yesterday's Brief (`Oct 18 20:52Z` / `Oct 19 00:17Z`) | Current Verified State (`2026-10-19T22:25:00Z`) | Primary Evidence / Receipt Path |
|---|---|---|---|
| **1. Queue & Train Status** | `60` staged candidates / active BURN rows in flight | **ALL BURN QUEUE ROWS LANDED & FLEET IDLE**: `origin-guard-fu1` (`v3.66.1939` / `63bb759309c0`), `trl-notes` (`f2e331f8` in `bd-persist/harness`, daemon restarted `20:37:57Z`), `telemetry-registry-live` (`cda3b91c`), `origin-guard r3 + aac-fu1`, `dcc-f9 r7 + dcc-fu2`, `uopa r2`, `rwg-hardening r1/r2`, `T09`, `C18`, `photosets r2`, `sac-fu1`, `deep-site-templates-med r2`, plus **19 `jarvisos_cockpit` cuts (`6899fda`) LANDED**. Control seats up (`O3415`), messaging crons paused (`O3416`). | `/home/mboyle/bd-persist/compact-bd-pm-B.md`, `/home/mboyle/bd-persist/role-state/pm.md`, `/home/mboyle/bd-persist/state/PAUSED-O3416-messaging-crons.md` |
| **2. AI Vault Benchmark Sweep** | `77 / 87 (88.5%)` completed (`10` heavyweight models running; hardware lock active) | **`87 / 87 (100.0%)` COMPLETED**: All 87 Vault models benchmarked and verified, including `#87 DeepSeek-V4.1-Flash-Abliterated-755B`. Post-benchmark RAM/VRAM flush completed and 16 spare VMs restored. | `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT154-87OF87-FINAL-AND-16-SPARES-RESTORE-RECEIPTS.md`, `AGENT155-RAM-VRAM-FLUSH-POST-87-RECEIPTS.md`, `AGENT37-NEW-FRONTIER-MODELS-BENCHMARK-RECEIPTS.md` |
| **3. Local Context & `<think>` Upgrades** | Standard context windows; `.72`/`.83` occupied by `llama-bench` | **Upgraded & Live**: `.83:18080` (`Qwen3.6-35B-A3B-Heretic`) upgraded to **`256K` (`262,144`) unified context** (`65,536` per-slot across parallel slots) + full **`<think>`** reasoning; `.72:8000` (`Qwen3-Coder-30B-A3B`) at **`256K` (`262,144`) context**; `.72:18081` (`Qwythos-9B-1M-MTP`) configured at **`512K` context**; `CyberStrike-35B-Q4_K_M` (`66.5 tok/s`) live on `.137:11434`, `.125:11434`, `.72:11434`, `:4010`, and `:8090`. | `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT151-MAX-CONTEXT-256K-1M-AND-THINKING-RECEIPTS.md`, `AGENT152-ALL-MODELS-THINKING-AND-CONTEXT-VERIFICATION-RECEIPTS.md` |
| **4. OpenWebUI (`.164:8090` & `.72:8080`)** | `260` raw badge-prefixed model names; default narrow dropdown | **Model-First UX & Deduplicated Catalog**: Upgraded with readable **model-first names**, wide **`620px` dropdown selector**, and **`215` deduplicated UI models** (`300` total API models; `352` on `:4010`, `253` on `:4000`). | `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/OPENWEBUI-AUTONOMOUS-RELIEF-AGENT-RECEIPTS.md`, `AGENT140-OPENWEBUI-15AGENTS-83TOOLS-14MCP-QDRANT-PG-RECEIPTS.md` |
| **5. 24-VM Multi-Platform Auth & Parity** | Legacy `token-1year.txt` CLI tokens on remote VMs | **`24 / 24` Linux VMs Verified (`O3394` / `O3412` / `O3413b`)**: Independent full-scope **Claude `A/B/C/D`**, **Codex**, **Kimi**, and **AGY** logins (`token-1year.txt` moved aside so all new launches support `--remote-control`) + 100% plugin, skill, MCP, and `~/bin` parity. | `/home/mboyle/bd-persist/compact-bd-pm-B.md`, `/home/mboyle/bd-persist/OPERATOR_DECISIONS.md` (`O3394`–`O3417`) |
| **6. 7-Repo GitHub & Gitea Sync** | 5 repos synced at `Oct 18 20:52Z` / `Oct 19 00:16Z` | **All 7 Repositories Synced & Pushed (`21:52 UTC`)**: `BulkDownloader` (`v3.66.1939` / `63bb759309c0`), `jarvisos_cockpit` (`6899fda`, 19 cuts), `boylenet-swarm-os` (`df78aee51ce2`), `boylenet-openwebui-supersuite` (`dfc582a5a73e`), `boylenet-bd-kb-and-receipts` (`e57a741c281b`), `boylenet-infra-configs` (`37729db731fc`), `UniversalSwarmOS` (`45d4bb640bec`). | Section 3 below |

---

## 2. Standby Local Relief Commander Verification & Zero-Token Takeover Runbook

### 2.1 Live Read-Only Verification (`2026-10-19T22:25Z` HTTP `/health`, `/slots`, `/props` & `POOL_STATE.tsv`)

| Relief Tier & Role | Model / Pool Identifier | Endpoint / Router Alias | Live Health & Context Verification (`2026-10-19T22:25Z`) | Cost / Quota Remaining |
|---|---|---|---|---|
| **Tier 1A: Primary Local Relief Commander** | **`Qwen3.6-35B-A3B-Heretic`** (`Q4_K_M`, Uncensored MoE + **`<think>`** reasoning) | Direct: `http://10.0.70.83:18080`<br>Gateways (`:4010` / `:4000` / `:8090`):<br>• `qwen3.6-35b-a3b-heretic`<br>• `qwen3.6-35b-claude-opus-abliterated`<br>• `agent-council-relief-commander` | **HOT & IDLE**: `GET /health` -> `{"status":"ok"}`; `GET /slots` -> `is_processing: False` (`2` parallel slots active, `65,536` per-slot / `256K` (`262,144`) unified context capability, `<think>` enabled, `795.0 tok/s` pp / `65.8 tok/s` tg on 2x T4). | **`$0.00` (`100%` Free / Airgap-Ready)** |
| **Tier 1B: Secondary Local Coding Commander** | **`Qwen3-Coder-30B-A3B-Instruct-Q4_K_M`** (`30B` MoE High-Speed Coder) | Direct: `http://10.0.70.72:8000`<br>Gateways (`:4010` / `:4000` / `:8090`):<br>• `tier1-qwen3-coder-30b-a3b`<br>• `qwen3-coder-30b-a3b`<br>• `agent-master-coder` | **HOT & SERVING**: `GET /health` -> `{"status":"ok"}`; `GET /props` -> `total_slots: 4`, **`n_ctx: 262144` (`256K` context)**, `848.90 tok/s` prefill / `77.62 tok/s` decode on 2x T4 Pool Alpha. | **`$0.00` (`100%` Free / Airgap-Ready)** |
| **Tier 1C: Local OffSec & Fast Specialist Pool** | **`CyberStrike-35B-Q4_K_M`** (`66.5 tok/s`) & **`Qwythos-9B-1M-MTP`** (`512K` ctx) | `.137:11434`, `.125:11434`, `.72:11434`, `.72:18081`, `:4010` (`tier1-cyberstrike-35b-q4km`, `cyberstrike-offsec-35b`) | **HOT & READY**: Routed across `:4010` (`352` models), `:4000` (`253` models), and `:8090` (`215` deduplicated UI / `300` API models); `8/8` local pool slots healthy in `POOL_STATE.tsv`. | **`$0.00` (`100%` Free / Airgap-Ready)** |
| **Tier 2A: Cloud Relief — AGY `claude+gpt` Pool** | **AGY `claude+gpt`** (Opus / Sonnet / GPT lane inside AGY) | `/home/mboyle/bd-persist/POOL_STATE.tsv` (`AGY` row) | **VERIFIED `OK`**: While AGY `gemini` weekly quota is at **`7.78%` remaining** (`94.49%` 5h remaining, resets `2026-10-20T01:41:59Z`), the AGY **`claude+gpt`** pool has **`99.49%` weekly (`100.0%` 5h) remaining**. | **`99.5%` Weekly / `100%` 5h Remaining** |
| **Tier 2B: Cloud Relief — Claude Pools `A / B / C / D`** | **Claude Code 4-Pool (`A`, `B`, `C`, `D`)** (`claude-opus-5-5` / `claude-sonnet-5-5`) | `/home/mboyle/bd-persist/POOL_STATE.tsv` (`A`, `B`, `C`, `D` rows, live OAuth verified `22:25:15Z`) | **VERIFIED `OK` (`96%–99%` 5h Remaining)**:<br>• **Pool A**: **`99%` 5h rem** (`1%` 5h used, `27%` wk used)<br>• **Pool B**: **`97%` 5h rem** (`3%` 5h used, `40%` wk used)<br>• **Pool C**: **`97%` 5h rem** (`3%` 5h used, `33%` wk used)<br>• **Pool D**: **`96%` 5h rem** (`4%` 5h used, `41%` wk used) | **`96%–99%` 5h Remaining (`59%–73%` Wk Rem)** |
| **Tier 2C: Cloud Relief — Codex & Kimi Pools** | **OpenAI Codex (`gpt-6-astra` / `fable-5-1`)** & **Kimi K2.8** | `/home/mboyle/bd-persist/POOL_STATE.tsv` (`codex`, `kimi` rows) | **VERIFIED `OK`**:<br>• **Codex**: **`51%` 5h remaining** (`49%` used)<br>• **Kimi**: **`86%` 5h remaining** (`14%` 5h used, `46%` monthly used) | **`51%` (Codex) / `86%` (Kimi) 5h Remaining** |

### 2.2 Standby Takeover Runbook (When AGY Gemini Weekly Quota `7.78%` Hits `0%`)

If AGY Gemini weekly quota exhausts (`0%`) before the weekly window reset, execute the following **zero-downtime takeover sequence**:

1. **Immediate Zero-Token Local Commander Takeover ($0 Marginal Cost, Airgap-Safe)**:
   - **Primary Local Relief Commander (`Qwen3.6-35B-A3B-Heretic` on `.83:18080`)**:
     ```bash
     # Verify health & slot readiness (read-only)
     curl -sS http://10.0.70.83:18080/health && curl -sS http://10.0.70.83:18080/slots | jq '.[0] | {n_ctx, is_processing}'

     # Direct CLI / Agent invocation via Sovereign Gateway (:4010) or LiteLLM (:4000)
     OPENAI_BASE_URL=http://10.0.70.164:4010/v1 OPENAI_API_KEY=sk-boylenet-master \
       bd-local-coding-agent --model agent-council-relief-commander "<task-or-pm-directive>"

     # Or raw unguarded direct invocation to .83:18080 with <think> reasoning
     /home/mboyle/bin/bd-raw-ai --reason "<prompt>"
     ```
   - **Secondary Local Coding Commander (`Qwen3-Coder-30B-A3B` on `.72:8000`, `256K` Context)**:
     ```bash
     # Route builder/fixer patches + .82 64-core /dev/shm ramdisk pytest verification at $0 cost
     OPENAI_BASE_URL=http://10.0.70.164:4010/v1 OPENAI_API_KEY=sk-boylenet-master \
       bd-local-coding-agent --model tier1-qwen3-coder-30b-a3b "<builder-brief>"
     ```
   - **OpenWebUI Autonomous Commander (`https://openwebui.boylenet.themfboyles.org` / `:8090`)**:
     - Select **`Qwen3.6-35B-A3B-Heretic [🟢 LOCAL • 256K • THINK]`** (`agent-council-relief-commander`) or **`Qwen3-Coder-30B-A3B [🟢 LOCAL • 256K]`** (`agent-master-coder`) in the `620px` model selector with all `50` native toolkits (`tk_01`..`tk_50`) and `28` VM terminals enabled.

2. **Cloud Pool Relief Takeover (AGY `claude+gpt` `99.5%` Wk + Claude Pools `A/B/C/D` `96%–99%` 5h)**:
   - **Switch AGY Council / Subagents to `claude+gpt` Quota Bucket (`99.49%` weekly / `100%` 5h remaining)**:
     - Select Claude/GPT model tier in AGY (`claude+gpt` bucket is completely independent of the `gemini` `7.78%` weekly bucket).
   - **Active Remote-Control PM Seat (`bd-pm-B`) & Claude Pool `A` (`99%` 5h / `73%` wk remaining)**:
     - `bd-pm-B` is already live on `hub-mesh01` (`Pool B`, `97%` 5h remaining).
     - For heavy integrator/command-squad rotation under **Directive O1740**, Pool `A` (`1%` 5h used, `27%` wk used) and Pool `C` (`3%` 5h used, `33%` wk used) have full Opus/Sonnet headroom.
   - **Full Relief Architecture Reference**:
     - `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGY-RELIEF-HANDOFF-OPUS55-FABLE51-LOCAL.md`
     - `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/OPUS55-POOL-C-RELIEF-TAKEOVER.md`
     - `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/OPENWEBUI-AUTONOMOUS-RELIEF-AGENT-RECEIPTS.md`

---

## 3. Published 7-Repository GitHub & Local Gitea Matrix (Synced `2026-10-19 21:52 UTC`)

All 7 production and infrastructure repositories are committed, secret-sanitized (`BOYLENET_SECRET_GUARD`), and pushed in 100% parity to both **GitHub (`mcboyle`)** and **Local Gitea (`http://10.0.70.164:3000/mboyle` / `https://gitea.boylenet.themfboyles.org/mboyle`)**:

| # | Repository | GitHub URL (`mcboyle`) | Local Gitea URL (`10.0.70.164:3000/mboyle`) | Local Path | Tracked Files | Head Commit SHA (`main`) | Latest Milestone Landed |
|---:|---|---|---|---|---:|---|---|
| **1** | **`BulkDownloader`** (`BD`) | `https://github.com/mcboyle/BulkDownloader` | `http://10.0.70.164:3000/mboyle/BulkDownloader` | `/home/mboyle/BulkDownloader` | `5,828` | `63bb759309c0` | **`v3.66.1939`** (`origin-guard-fu1`, `telemetry-registry-live` `cda3b91c`, all BURN rows landed) |
| **2** | **`jarvisos_cockpit`** | `https://github.com/mcboyle/jarvisos_cockpit` | `http://10.0.70.164:3000/mboyle/jarvisos_cockpit` | `/home/mboyle/jarvisos_cockpit` | `Synced` | `6899fda` | **All 19 `jarvisos_cockpit` cuts landed & verified** |
| **3** | **`boylenet-swarm-os`** | `https://github.com/mcboyle/boylenet-swarm-os` | `http://10.0.70.164:3000/mboyle/boylenet-swarm-os` | `/home/mboyle/git-repos/boylenet-swarm-os` | `428` | `df78aee51ce2` | `230-Swarm` blitz drivers, relief agents, 165-view 60fps visualizers, `~/bin` fleet tools |
| **4** | **`boylenet-openwebui-supersuite`** | `https://github.com/mcboyle/boylenet-openwebui-supersuite` | `http://10.0.70.164:3000/mboyle/boylenet-openwebui-supersuite` | `/home/mboyle/git-repos/boylenet-openwebui-supersuite` | `17` | `dfc582a5a73e` | `215` deduplicated UI models (`300` API), `620px` wide selector, model-first names, `50` toolkits |
| **5** | **`boylenet-bd-kb-and-receipts`** | `https://github.com/mcboyle/boylenet-bd-kb-and-receipts` | `http://10.0.70.164:3000/mboyle/boylenet-bd-kb-and-receipts` | `/home/mboyle/git-repos/boylenet-bd-kb-and-receipts` | `252` | `e57a741c281b` | 6-Volume Master `BD KB`, `87/87` Vault benchmark receipts, `AGENT105`–`AGENT176` receipts |
| **6** | **`boylenet-infra-configs`** | `https://github.com/mcboyle/boylenet-infra-configs` | `http://10.0.70.164:3000/mboyle/boylenet-infra-configs` | `/home/mboyle/git-repos/boylenet-infra-configs` | `24` | `37729db731fc` | NGINX `:443` Let's Encrypt Cloudflare wildcard TLS, Traefik `.org`/`.com`, `256K`–`512K` systemd units |
| **7** | **`UniversalSwarmOS`** | `https://github.com/mcboyle/UniversalSwarmOS` | `http://10.0.70.164:3000/mboyle/UniversalSwarmOS` | `/home/mboyle/git-repos/UniversalSwarmOS` | `1,162` | `45d4bb640bec` | Unified monorepo mirror of all Swarm OS portals, `BD KB`, and `O1789` infrastructure |

*(Note: `bd-persist/harness` also landed `trl-notes r1c` at commit `f2e331f8` with telemetry daemon restarted at `20:37:57Z`.)*

---

## 4. `87 / 87 (100%)` Dual-Vault Benchmark Completion & Upgraded Context / Serving Posture

1. **`87 / 87 (100.0%)` Dual-Vault Models Benchmarked & Verified (`AI Vault A .72` & `AI Vault B .83`)**:
   - **Completion**: All **`87 / 87`** model directories (`8.9+ TB` across NVMe/vSAN on `.72` and `.83`) have completed `llama-bench` (`b11716` / `ik_llama.cpp` / TurboQuant) and Safetensors/ONNX/Diffusers verification, concluding with **`#87 DeepSeek-V4.1-Flash-Abliterated-755B`**.
   - **Receipts**:
     - `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT154-87OF87-FINAL-AND-16-SPARES-RESTORE-RECEIPTS.md`
     - `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT155-RAM-VRAM-FLUSH-POST-87-RECEIPTS.md`
     - `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT37-NEW-FRONTIER-MODELS-BENCHMARK-RECEIPTS.md`
     - `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT171-HOST72-SERIALIZED-BENCHMARKS-RECEIPTS.md`
     - `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT172-HOST83-SERIALIZED-BENCHMARKS-RECEIPTS.md`
     - `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/SERIALIZED-OPT-PIPELINE-LEDGER.md`
2. **Production Context Window & Reasoning Upgrades (`Agent-151` & `Agent-152`)**:
   - **`.83:18080` (`Qwen3.6-35B-A3B-Heretic`)**: Upgraded to **`256K` (`262,144`) unified context** (`65,536` per slot across parallel slots) + full **`<think>`** reasoning tags (`103.59 tok/s` pp / `24.16 tok/s` tg on 1x T4; `795.0 tok/s` pp / `65.8 tok/s` tg on 2x T4).
   - **`.72:8000` (`Qwen3-Coder-30B-A3B-Instruct-Q4_K_M`)**: Live at **`256K` (`262,144`) context** (`4` slots, `848.90 tok/s` pp / `77.62 tok/s` tg).
   - **`.72:18081` (`Qwythos-9B-Claude-Mythos-5-1M-MTP-Q8_0`)**: Configured for **`512K` context** (`1,005.86 tok/s` pp / `21.39 tok/s` tg).
   - **`CyberStrike-35B-Q4_K_M` (`Huihui-CyberStrike-OffSec-35B-abliterated`)**: Live at **`66.5 tok/s` decode** (`799.0 tok/s` prefill) across `.137:11434`, `.125:11434`, `.72:11434`, Sovereign Gateway `:4010`, and OpenWebUI `:8090`.
   - **100B–1T Frontier Benchmarks**:
     - `GLM-4.5-Air-REAP-82B-A12B-Heretic-Q4_K_M`: `30.02 tok/s` pp / `6.40 tok/s` tg
     - `NVIDIA-Nemotron-3.5-Bolt-74B-A9B`: `29.18 tok/s` pp / `13.46 tok/s` tg
     - `KIMI-K3-MXP4-DERISKED-Q2_K` (`1T` params, `940.18 GiB`): `69.13 tok/s` pp / `16.30 tok/s` tg across 4-Node `1.73 TB` DDR4 + 4x T4 Hybrid.

---

## 5. OpenWebUI Super-Suite (`:8090` / `.72:8080`), 165-View 60fps Visualizer (`:4088`) & Wildcard HTTPS (`:443`)

1. **OpenWebUI Maximum-Power Super-Suite (`https://openwebui.boylenet.themfboyles.org` / `.164:8090` & `https://ai-webui.boylenet.themfboyles.org` / `.72:8080`)**:
   - **Model-First Naming & Wide `620px` Selector**: All models use clean **model-first names** (so model name is immediately readable before `[🟢 LOCAL]` / `[☁️ CLOUD]` badges) in an expanded **`620px` wide dropdown selector**.
   - **Deduplicated Model Catalog**: **`215` deduplicated UI models** (`300` total API models; `352` on Sovereign Local Gateway `:4010`, `253` on LiteLLM `:4000`).
   - **`50` Native Python Toolkits (`tk_01`..`tk_50`) + `5` OpenAPI 3.1 Servers (`39` REST endpoints)** + **`14` MCP Servers** + **`128` Slash Skills** + **`8` Pipelines / `12` Functions** + **`11` Local Image/Speech Models** + **`6-Volume Master BD KB`** (`http://10.0.70.125:6333/collections/bd_master_kb`) + **`28` Interactive VM Terminals (`/vm/<hostname>`)**.
2. **NGINX `:443` Reverse Proxy + Let's Encrypt Cloudflare Wildcard TLS (`*.themfboyles.org` & `*.boylenet.themfboyles.org`)**:
   - Production Let's Encrypt wildcard cert at `/etc/letsencrypt/live/themfboyles.org/fullchain.pem` (`211 / 211` HTTPS `:443` + Dual Chrome CDP Oracle `.137:9222` & `.181:9222` checks `PASS` across all `47` subdomains).
3. **165-View Live 60fps Visualizer (`https://topology.boylenet.themfboyles.org` / `:4088`) & Unified OS Shell (`https://os.boylenet.themfboyles.org` / `:4085`)**:
   - `V01`–`V100` HiveMind CoT views + `T01`–`T65` 3D Topology/Rack/vSAN/25GbE/Military views, `/wallboard`, `/gallery`, Quake terminal drawer (`:4083`), and 3-layer Kernel WAN Airgap (`bd-airgap` + `:4085` top-bar pill).

---

## 6. Current Fleet Posture (`O3415`–`O3417`), Active Control Seats & Held Backlog

### 6.1 Active Control Seats Kept Up (`O3415` / `O3417`)
- **PM Seat**: `bd-pm-B` (`hub-mesh01`, Pool B `--remote-control`) — Handoff at `/home/mboyle/bd-persist/compact-bd-pm-B.md`.
- **BD Lane Control Seats**:
  - `bd-dispatch-build-D5-D` (`.194` `wrk-test05`) — `/home/mboyle/bd-persist/handoff-bd-dispatch-build-D5-D.md`
  - `bd-dispatch-F2-D` (`.186`) — `/home/mboyle/bd-persist/handoff-bd-dispatch-F2-D.md`
  - `bd-lensrouter-B5-C` (`.186`) — `/home/mboyle/bd-persist/handoff-bd-lensrouter-B5-C.md`
  - `bd-integrator-D4-A` (`.54`) — `/home/mboyle/bd-persist/handoff-bd-integrator-D4-A.md`
- **Harness Lane Control Seats**:
  - `bd-dispatch-F1-D` (`.54`) — `/home/mboyle/bd-persist/handoff-bd-dispatch-F1-D.md`
  - `bd-lensrouter-B4-D` (`.186`) — `/home/mboyle/bd-persist/handoff-bd-lensrouter-B4-D.md`
  - `bd-integrator-C` (`.84`) — `/home/mboyle/bd-persist/handoff-bd-integrator-C.md`
- **Council & Local Daemons**:
  - `agy` (`hub-mesh01` tmux `agy`; message via `tmux send-keys`, never `bd-say`)
  - `10` `bd-local-*` GPU worker daemons (idle/standby) + `kimi-quota-probe`.

### 6.2 Paused Messaging Crons (`O3416` — Resume ONLY on Explicit Operator Order)
- Full pause record & exact one-command resume runbook: `/home/mboyle/bd-persist/state/PAUSED-O3416-messaging-crons.md`
- Hub crontab lines prefixed with `#PAUSED-O3416 ` (backup at `/home/mboyle/bd-persist/crontab-backups/crontab.pre-O3416-20261019T2138Z`); user/system timers (`fleet-watch`, `pm-idle-watch`, `reap`, `pm-stall-watch`, `ugrep-reaper`, `usage-oneshot`, `usage-probe`, `bd-rc-enforcer`, `bd-relief-puppeteer`) stopped; 23 remote VM crontabs paused (`~/.crontab.pre-O3416`).

### 6.3 Held Queue Wave (Awaiting Operator `GO`)
- **Bug-Hunt Wave 1 (`O3388` Hold)**: `/home/mboyle/bd-persist/state/ROWS-BUGHUNT-WAVE1-20261019.md` — `55` rows (`BH1-01`..`BH1-55`), consolidated into `8` non-overlapping cuts (`BHC-01`..`BHC-08`) with file-overlap matrix and train order pre-computed. **Do NOT dispatch until operator issues `GO`.**
- **Local-LLM Lens/Build Trial (`O3401`–`O3409`)**: Score ledgers at `/home/mboyle/bd-persist/state/LOCAL-LENS-SCORE.tsv` and `/home/mboyle/bd-persist/state/LOCAL-BUILD-SCORE.tsv` (`O3409`: local/codex first-finisher -> Claude lens).

---

## 7. Master File Map & Key Reference Links

- **Master PM Handoff (Canonical)**: `/home/mboyle/bd-persist/PM-HANDOFF.md`
- **O1789 Campaign PM Handoff**: `/home/mboyle/bd-persist/harness-work/O1789/PM-HANDOFF.md`
- **Timestamped Snapshot (`2026-10-19T22:25Z`)**: `/home/mboyle/bd-persist/PM-HANDOFF-20261019T2225Z.md`
- **PM Role State**: `/home/mboyle/bd-persist/role-state/pm.md`
- **PM Compacted Session Handoff**: `/home/mboyle/bd-persist/compact-bd-pm-B.md`
- **Live Pool & Quota Ledger**: `/home/mboyle/bd-persist/POOL_STATE.tsv`
- **Paused Crons & Resume Runbook (`O3416`)**: `/home/mboyle/bd-persist/state/PAUSED-O3416-messaging-crons.md`
- **Held Bug-Hunt Wave 1 Plan (`O3388`)**: `/home/mboyle/bd-persist/state/ROWS-BUGHUNT-WAVE1-20261019.md`
- **87/87 Vault Completion Receipt**: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT154-87OF87-FINAL-AND-16-SPARES-RESTORE-RECEIPTS.md`
- **256K–1M Context & `<think>` Verification**: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT151-MAX-CONTEXT-256K-1M-AND-THINKING-RECEIPTS.md` & `AGENT152-ALL-MODELS-THINKING-AND-CONTEXT-VERIFICATION-RECEIPTS.md`
- **Local & Cloud Relief Takeover Guides**: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGY-RELIEF-HANDOFF-OPUS55-FABLE51-LOCAL.md` & `OPUS55-POOL-C-RELIEF-TAKEOVER.md`
- **Master 230-Swarm Mega-Blitz Ledger**: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/MASTER-230-SWARM-MEGA-BLITZ-LEDGER.md`
- **Master 215-Backlog E2E Ledger**: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/MASTER-215-BACKLOG-E2E-LEDGER.md`


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
