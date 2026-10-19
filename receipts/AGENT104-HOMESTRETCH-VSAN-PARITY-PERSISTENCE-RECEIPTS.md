# AGENT-104: Post-Drain `esxi01` vSAN Certification, LobeChat/OpenCode/OpenClaw/Jarvis `:4079` Parity, Live `BD KB` Auto-Indexer & Reboot-Persistence Audit Receipts

- **WRITTEN-AT (UTC)**: `2026-10-18T20:59:00Z`
- **Agent**: `Agent-104` (Home-Stretch High-ROI Finisher)
- **Live Headless Stream Log (`:4083`)**: `/var/tmp/bd-headless-streams/agent104-homestretch-vsan-parity.log`
- **Benchmarking Non-Interference Invariant**: **100% PRESERVED** — Zero processes, containers, or I/O benchmarks were touched or contended with on `.72`, `.83`, `.125`, or `.228` (`Agent-37`'s `llama-bench` / `ggml-rpc-server` workloads remained completely undisturbed).

---

## 1. Post-Drain Certification: `esxi01` Per-Disk vSAN Surgical Re-Grouping (100% Complete, `0.00 GB` Resync)

### 1.1 Final Step C2 (`naa.5000cca01aa800e0` / `T10` 3TB Hitachi SAS) Completion Summary
- **Orchestrator Process**: `PID 1223012` (`python3 -u /home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/agent6_surgical_regroup.py`) completed all 5 moves and exited cleanly (`rc=0`) at `2026-10-18T20:57:46Z`.
- **Step C2 Drain Timeline (`naa.5000cca01aa800e0`, `1012.3 GiB` used)**:
  - **Evacuation Start / End (UTC)**: `2026-10-18T15:18:17Z` $\rightarrow$ `2026-10-18T20:57:06Z`
  - **Final Countdown Observed Live by Agent-104**:
    - `2026-10-18T20:43:39Z`: `Objects=1`, `GBLeft=34.94` (`37,518,348,288 B`)
    - `2026-10-18T20:47:47Z`: `Objects=1`, `GBLeft=22.78` (`24,459,599,872 B`)
    - `2026-10-18T20:51:55Z`: `Objects=1`, `GBLeft=10.28` (`11,038,695,424 B`)
    - `2026-10-18T20:53:59Z`: `Objects=1`, `GBLeft=5.47` (`5,868,142,592 B`)
    - `2026-10-18T20:56:03Z`: `Objects=1`, `GBLeft=0.97` (`1,042,808,832 B`)
    - `2026-10-18T20:57:06Z`: `Objects=0`, `GBLeft=0.00` (`0 B`) — detached from `DG 52e69070-da86-8f9f-27b2-679f255787a8` (`mpx.vmhba0:C2:T0:L0`).
  - **Target Disk Group Claim (`esxcli vsan storage add -s mpx.vmhba0:C2:T1:L0 -d naa.5000cca01aa800e0`)**:
    - **Start / End (UTC)**: `2026-10-18T20:57:07Z` $\rightarrow$ `2026-10-18T20:57:36Z` (`rc=0`, `out=true`)
    - **New `VSANUUID`**: `527b2bdc-ffa6-fd7c-d00d-2ae309c17e78` (`IsMounted=true`, `InCMMDS=true`).

### 1.2 Certified Post-Drain `esxi01` Disk Group Architecture (Pure Tier Separation Achieved)
All 5 surgical disk moves (**Step A**: `T4` 24TB Exos $\rightarrow$ `T0`; **Step B1**: `T6` 3TB SAS $\rightarrow$ `T1`; **Step B2**: `T8` 3TB SAS $\rightarrow$ `T1`; **Step C1**: `T5` 24TB Exos $\rightarrow$ `T0`; **Step C2**: `T10` 3TB SAS $\rightarrow$ `T1`) are complete:

| Disk Group UUID | Cache Tier SSD | Device | Drive Role / Tier | Capacity Tier | IsSSD | Mounted | InCMMDS | VSAN UUID |
|---|---|---|---|---|---|---|---|---|
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T0:L0` | **DG1 Cache SSD (`T0`)** | `false` | `true` | `true` | `true` | `52e69070-da86-8f9f-27b2-679f255787a8` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T3:L0` | **Pure 24TB Exos (`T3`)** | `true` | `false` | `true` | `true` | `521d0c28-1459-755a-6721-d9c20fb39333` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T4:L0` | **Pure 24TB Exos (`T4`, Step A)** | `true` | `false` | `true` | `true` | `52521303-9b87-0c61-4f80-10bf7d453fe4` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T5:L0` | **Pure 24TB Exos (`T5`, Step C1)** | `true` | `false` | `true` | `true` | `525ffaa8-33eb-b23b-c59f-09944ca74ecc` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T13:L0` | **Pure 24TB Exos (`T13`)** | `true` | `false` | `true` | `true` | `52f55307-770c-ff18-3fb6-7a5efb104701` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `mpx.vmhba0:C2:T1:L0` | **DG2 Cache SSD (`T1`)** | `false` | `true` | `true` | `true` | `520e7405-7951-d36b-972f-44fa76ee518e` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01aa9a96c` | **Consolidated 3TB SAS (`T6`, Step B1)** | `true` | `false` | `true` | `true` | `52191fd7-997a-a5a6-c65f-0b325a677220` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01ab112cc` | **Consolidated 3TB SAS (`T7`)** | `true` | `false` | `true` | `true` | `52e03231-e4ca-b44f-afd9-b65c7a5cf3e1` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01aa7e5ec` | **Consolidated 3TB SAS (`T8`, Step B2)** | `true` | `false` | `true` | `true` | `52d55f3a-970a-3363-c37c-98bfd6d162b0` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01ad7dc48` | **Consolidated 3TB SAS (`T9`)** | `true` | `false` | `true` | `true` | `52838d58-ec30-7adb-256c-53952dea37c4` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01aa800e0` | **Consolidated 3TB SAS (`T10`, Step C2)** | `true` | `false` | `true` | `true` | `527b2bdc-ffa6-fd7c-d00d-2ae309c17e78` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.50000c0f012d3630` | **Consolidated 3TB SAS (`T11`)** | `true` | `false` | `true` | `true` | `522ac7d2-9125-2e27-d90a-e79d5b95d5ff` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.50000c0f01e0c290` | **Consolidated 3TB SAS (`T12`)** | `true` | `false` | `true` | `true` | `52231164-8542-4e47-05b5-c97338472125` |

### 1.3 Post-Drain Cluster Resync, Node Membership & SMART Health (`esxi01..04`)
- **vSAN Resync Summary (`esxcli vsan debug resync summary get`)**:
  - `TotalBytesLeftToResync: 0` (`0.00 GB`)
  - `TotalNumberOfResyncingObjects: 0`
- **vSAN Cluster Health (`esxcli vsan cluster get`)**:
  - `LocalNodeHealthState: HEALTHY` | `SubClusterMemberCount: 4` (`ESXI01, ESXI02, ESXI03, ESXI04`) | `MaintenanceModeState: OFF`
- **Post-Drain Cache SSD SMART Wear (`0` write/uncorrectable errors across all 4 cache SSDs)**:
  - `esxi01 T0` (`DG 52e69070` 4x24TB Exos Cache): `Health=OK`, `Wear=50 (raw=899)`, `WriteErrorCount=0`, `UncorrectableErrorCount=0`
  - `esxi01 T1` (`DG 520e7405` 7x3TB SAS Cache): `Health=OK`, `Wear=33 (raw=1210)`, `WriteErrorCount=0`, `UncorrectableErrorCount=0`
  - `esxi02 T8` (`DG 5240e70d` Cache): `Health=OK`, `Wear=24 (raw=1375)`, `WriteErrorCount=0`, `UncorrectableErrorCount=0`
  - `esxi02 T9` (`DG 522f2b56` Cache): `Health=OK`, `Wear=9 (raw=1641)`, `WriteErrorCount=0`, `UncorrectableErrorCount=0`
- **Full Per-Step Receipt File**: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT6-SURGICAL-REGROUP-RECEIPTS.md` (`1,988 lines`, `330,496 bytes`).

---

## 2. `:4079` 50-Toolkit / `BD KB` / `🟢 LOCAL vs ☁️ CLOUD` / `🛡️ Airgap` Parity Across `:4095`, `:4096`, `:4097`, `:3002`, and `:4098`

| Surface | Port | Script / Config | Models Synced (`🟢 LOCAL` / `☁️ CLOUD`) | `:4079` 50-Toolkit OpenAPI Suites | `BD KB` Search & Qdrant `bd_master_kb` | `🛡️ Airgap` Badge | Live Verification |
|---|---:|---|---|---|---|---|---|
| **LobeChat Studio** | `:4095` | `/home/mboyle/bin/bd-lobechat-web.py` + `lobechat-studio:/app/lobechat_server.py` | **95 total** (`87 🟢 LOCAL` + `8 ☁️ CLOUD` with filter pills) | All 5 OpenAPI specs linked + `/api/toolkits` | `/api/bdkb/search` (`10.94ms`) + Auto-Ground Chat toggle | `🛡️ Airgap: LOCAL-FIRST ($0.00) • WAN ACTIVE` (`/api/airgap`) | `HTTP 200 OK` |
| **OpenCode Web** | `:4096` | `/home/mboyle/bin/bd-opencode-web.py` + `/home/mboyle/.config/opencode/opencode.json` | **205 total** (`197 🟢 LOCAL` + `8 ☁️ CLOUD`) | All 5 OpenAPI specs in `BOYLENET-4079-50TOOLS-BDKB.md` | `http://10.0.70.164:4079/api/tool/bd_kb_search` + Qdrant | `🛡️ Airgap: LOCAL-FIRST ($0.00) • WAN ACTIVE` | `HTTP 200 OK` (`/config`) |
| **OpenClaw Web** | `:4097` | `/home/mboyle/bin/bd-openclaw-web.py` + `~/.openclaw/openclaw.json` & `~/.openclaw-dev/openclaw.json` | **105 total** (`97 🟢 LOCAL` + `8 ☁️ CLOUD`) | All 5 OpenAPI specs + `openapi_50_toolkits_4079` in `tools.alsoAllow` | `bd_kb_search` & `qdrant_rag_search` enabled + `AGENTS.md` | `🛡️ Airgap: LOCAL-FIRST ($0.00) • WAN ACTIVE` | `HTTP 200 OK` |
| **Jarvis Cockpit & Multi-Harness** | `:3002` / `:4098` | `/home/mboyle/bin/bd-jarvis-cockpit.py` + `/home/mboyle/bin/bd-multi-harness-web.py` | `87 🟢 LOCAL` + `8 ☁️ CLOUD` | All 5 `:4079` OpenAPI suites linked | `:4079` `BD KB` search + Qdrant `bd_master_kb` | `🛡️ Airgap: LOCAL-FIRST ($0.00)` header badge | `HTTP 200 OK` (`:3002`, `:4098`, `:4079`) |

---

## 3. Live `BD KB` Auto-Indexer (`bd-kb-live-sync.service` + `bd-kb-live-sync.timer`) & Reboot-Persistence Audit

### 3.1 Live `BD KB` Auto-Indexer Deployment
- **Executable**: `/home/mboyle/bin/bd-kb-live-sync.py`
- **Systemd Service & Timer**:
  - `/etc/systemd/system/bd-kb-live-sync.service` (`enabled`)
  - `/etc/systemd/system/bd-kb-live-sync.timer` (`enabled`, `active (waiting)`, runs every 15 minutes with `OnBootSec=2min`, `OnUnitActiveSec=15min`, `Persistent=true`)
- **Generated BD KB Playbook Volume**:
  - `/home/mboyle/bd-persist/bd-kb/05-SWARM-OPERATIONS-RECEIPTS-AND-PLAYBOOKS.md` (`168+` `AGENT*.md` receipts, `PM-HANDOFF.md`, and `WATCHDOG-STATUS.md` automatically digested and indexed)
  - Registered as `bd-kb-swarm-ops` in `/home/mboyle/bin/bd_openwebui_pipelines_bdkb_ext.py` (`BD_KB_BOOKS_META`)
  - Indexed `198+` 1024-d Cosine vectors into Qdrant `http://10.0.70.125:6333/collections/bd_master_kb` (`points_upserted=198`, `status="ok"`).

### 3.2 Systemd Reboot-Persistence Audit & Remediation Across All `bd-*.service` Units on `.164`
- **Remediations Applied**:
  1. Terminated unmanaged orphan `python3 /home/mboyle/bin/bd-openwebui-tool-gateway.py` process holding `:4079` and restarted `bd-openwebui-tool-gateway.service` so systemd cleanly owns `:4079` (`active=active`, `enabled=enabled`, `Restart=always`).
  2. Upgraded `bd-novnc.service`, `bd-openbox.service`, `bd-x11vnc.service`, `bd-xvfb.service`, and user unit `bd-cockpit.service` (`:3002`) from `Restart=on-failure` to `Restart=always`.
  3. Cleared transient failed state on timer-triggered `bd-memory-dreamer.service` and verified all 28 long-running `/etc/systemd/system/bd-*.service` daemons are `enabled` + `active` + `Restart=always`:
     - `bd-cloud-burst-web.service`: `enabled` | `active` | `Restart=always`
     - `bd-credentials-vault.service`: `enabled` | `active` | `Restart=always`
     - `bd-deep-research-web.service`: `enabled` | `active` | `Restart=always`
     - `bd-executive-briefing.service`: `enabled` | `active` | `Restart=always`
     - `bd-gitea-prereview.service`: `enabled` | `active` | `Restart=always`
     - `bd-gpu-orchestrator.service`: `enabled` | `active` | `Restart=always`
     - `bd-hardware-telemetry.service`: `enabled` | `active` | `Restart=always`
     - `bd-infra-sentinel.service`: `enabled` | `active` | `Restart=always`
     - `bd-mcp.service`: `enabled` | `active` | `Restart=always`
     - `bd-mobile-pwa.service`: `enabled` | `active` | `Restart=always`
     - `bd-model-storefront.service`: `enabled` | `active` | `Restart=always`
     - `bd-multi-harness-web.service`: `enabled` | `active` | `Restart=always`
     - `bd-multimodal-bridge.service`: `enabled` | `active` | `Restart=always`
     - `bd-novnc.service`: `enabled` | `active` | `Restart=always`
     - `bd-openbox.service`: `enabled` | `active` | `Restart=always`
     - `bd-openclaw-web.service`: `enabled` | `active` | `Restart=always`
     - `bd-opencode-web.service`: `enabled` | `active` | `Restart=always`
     - `bd-openwebui-tool-gateway.service`: `enabled` | `active` | `Restart=always`
     - `bd-ops-web.service`: `enabled` | `active` | `Restart=always`
     - `bd-os-shell.service`: `enabled` | `active` | `Restart=always`
     - `bd-pool-autobalancer.service`: `enabled` | `active` | `Restart=always`
     - `bd-rag.service`: `enabled` | `active` | `Restart=always`
     - `bd-topology-visualizer.service`: `enabled` | `active` | `Restart=always`
     - `bd-trust-and-sso-portal.service`: `enabled` | `active` | `Restart=always`
     - `bd-web-terminal-drawer.service`: `enabled` | `active` | `Restart=always`
     - `bd-x11vnc.service`: `enabled` | `active` | `Restart=always`
     - `bd-xvfb.service`: `enabled` | `active` | `Restart=always`
     - `bd-iptables-restore.service` (`Type=oneshot`, `RemainAfterExit=yes`): `enabled` | `active`
     - `bd-kb-live-sync.service` + `bd-kb-live-sync.timer` (`every 15m`): `enabled` | `active (waiting)`
