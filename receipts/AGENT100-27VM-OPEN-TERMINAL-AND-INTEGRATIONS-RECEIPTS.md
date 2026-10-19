# AGENT-100: All-27-VM Open Terminal Multiplexer, Personal & Admin Integrations Configurator & High-ROI Tool Swarm Receipts

- **Written-At**: `2026-10-18T20:46:00Z`
- **Role**: Agent-100 (`All-27-VM Open Terminal Multiplexer, Personal & Admin Integrations Configurator & High-ROI Tool Swarm Architect`)
- **Stream Log**: `/var/tmp/bd-headless-streams/agent100-27vm-open-terminal.log`
- **Modified / Created Files**:
  - `/home/mboyle/bin/bd_openwebui_27vm_terminal.py` (1,486 lines — 27-VM Open Terminal Multiplexer & SSH File/Command Bridge)
  - `/home/mboyle/bin/bd-openwebui-tool-gateway.py` (`:4079` — integrated `/vm/all/*` and `/vm/<vm_target>/*` routes + full CORS headers + `OPTIONS` preflight)
  - `/home/mboyle/bin/bd-openwebui-sync-tools-skills-mcp.py` (31 native Python toolkits including `tk_31_all_27_vms_open_terminal_multiplexer`, 28 Open Terminal connections in both Admin and Personal Settings, 5 OpenAPI 3.1 Tool Servers in both Admin and Personal Settings)

---

## 1. Architecture & Endpoints Implemented on `:4079` (`bd-openwebui-tool-gateway.py` + `bd_openwebui_27vm_terminal.py`)

### A. CORS & Browser Direct-Connection Compatibility
- Every HTTP response (`200`, `204`, `4xx`, `5xx`) and `OPTIONS *` preflight on `:4079` emits:
  - `Access-Control-Allow-Origin: *`
  - `Access-Control-Allow-Methods: GET, POST, PUT, DELETE, PATCH, OPTIONS, HEAD`
  - `Access-Control-Allow-Headers: *`
  - `Access-Control-Expose-Headers: *`
- Supports both OpenWebUI's backend proxy (`/api/v1/terminals/<id>/*`) and browser-direct connections (`User Settings -> Integrations -> Open Terminal` & `Tools`).

### B. All-27-VM Open Terminal Multiplexer (`/vm/all/*`) & Individual VM Terminals (`/vm/<vm_target>/*`)
Each of the **28 Open Terminal endpoints** (`/vm/all` + 27 `/vm/<ip>`) implements the full OpenWebUI Open Terminal specification (`DXIE4il6.js` + `open_webui/routers/terminals.py` + `open_webui/utils/tools.py`):
- `GET .../openapi.json` & `GET .../terminal/openapi.json` (OpenAPI 3.1.0 spec with `x-open-terminal` extension and `operationId`s: `run_command`, `read_file`, `write_file`, `list_files`, `search_files`, `glob_files`, `grep_matches`, `get_system_info`, `exec_on_any_vm`)
- `GET .../api/config` (`{"status": true, "version": "1.2.0", "type": "terminal", "features": {"files": true, "terminal": true, "skills": true, "execute": true, "notebooks": true, "system": true}}`)
- `GET .../system` (live hostname, IP, role, OS, CPU/RAM/load telemetry)
- `GET/POST .../files/cwd` (per-session working directory tracking; defaults to `/vms` for `/vm/all` and each VM's default directory for `/vm/<ip>`)
- `GET .../files/list?directory=...` (virtual `/vms` root listing all 27 `<ip>-<name>` directories + live SSH `os.scandir` listing on any Linux or Windows `.137`/`.181` VM, with `/mnt/ai-models` automatically aliased to `/opt/models` on `.72` where 74 AI model directories reside)
- `GET .../files/read?path=...` & `GET .../files/view?path=...` (live remote file preview and binary/image streaming over SSH)
- `GET .../files/search?query=...&path=...`, `GET .../files/glob?pattern=...&path=...`, `GET .../files/matches?query=...&path=...` (remote ripgrep/glob search over SSH)
- `POST .../files/write`, `POST .../files/mkdir`, `POST .../files/move` (sandboxed to `/var/tmp/openwebui-workspace` and `/dev/shm/bd-openwebui-ci-ramdisk` on any VM)
- `GET .../skills`, `GET .../skills/list`, `GET .../skills/read` (128 fleet skills exposed inside the Open Terminal Skills tab)
- `GET .../ports` (live listening TCP ports on target VM)
- `GET/POST .../execute` & `POST .../exec_on_any_vm` (remote command execution on any of the 27 Boylenet VMs with `@vm:<target>` prefix support in `/vm/all`)

---

## 2. All 28 Open Terminal Connections Configured in Both Personal Settings & Admin Settings

Configured identically on both **Controller (`10.0.70.164:8090` / `http://openwebui.boylenet.themfboyles.com`)** and **Vault A (`10.0.70.72:8080` / `http://ai-webui.boylenet.themfboyles.com`)**:
- **Personal Settings (`User Settings -> Integrations -> Open Terminal` via `POST /api/v1/users/user/settings/update` & `ui.default_interface_settings`)**:
  - Connection `#1` (`boylenet-all-27-vms`) has `enabled: true` (active by default since OpenWebUI's personal terminal toggle allows one active personal terminal at a time), `auth_type: "bearer"`, `key: "sk-fleet-local"`.
  - Connections `#2..#28` (`vm-10-0-70-164` through `vm-10-0-70-181`) are pre-populated with URL, name, `auth_type: "bearer"`, `key: "sk-fleet-local"`, and `enabled: false` for 1-click toggling in `Settings -> Integrations -> Open Terminal`.
- **Admin Settings (`Admin Settings -> Tools -> Integrations -> Open Terminal` via `POST /api/v1/configs/terminal_servers`)**:
  - All 28 connections have `enabled: true` and `access_grants: [{"principal_type": "user", "principal_id": "*", "permission": "read"}]`, so `GET /api/v1/terminals/` returns all 28 terminals in the OpenWebUI Terminal dropdown selector.

| # | Connection ID | URL | Default CWD | Role / Host |
|---|---|---|---|---|
| 1 | `boylenet-all-27-vms` | `http://10.0.70.164:4079/vm/all` | `/vms` | 🌐 ALL 27 BOYLENET VMs — Unified Cluster File Browser & Terminal (`/vms/`) |
| 2 | `vm-10-0-70-164` | `http://10.0.70.164:4079/vm/10.0.70.164` | `/home/mboyle` | 🖥️ VM 01 · `10.0.70.164` — Hub Controller & Master Orchestrator (`hub-mesh01`) |
| 3 | `vm-10-0-70-72` | `http://10.0.70.164:4079/vm/10.0.70.72` | `/opt/models` | 🧠 VM 02 · `10.0.70.72` — AI Model Vault A & OpenWebUI Secondary (`ai-vault-a`) |
| 4 | `vm-10-0-70-83` | `http://10.0.70.164:4079/vm/10.0.70.83` | `/mnt/ai-models` | 🧠 VM 03 · `10.0.70.83` — AI Model Vault B & Heavy Weights (`ai-vault-b`) |
| 5 | `vm-10-0-70-125` | `http://10.0.70.164:4079/vm/10.0.70.125` | `/root` | ⚡ VM 04 · `10.0.70.125` — AI Inference & Qdrant Vector Engine (`ai-infer01`) |
| 6 | `vm-10-0-70-228` | `http://10.0.70.164:4079/vm/10.0.70.228` | `/root` | 🦙 VM 05 · `10.0.70.228` — Ollama & LiteLLM Satellite Router (`ai-ollama01`) |
| 7 | `vm-10-0-70-61` | `http://10.0.70.164:4079/vm/10.0.70.61` | `/home/mboyle` | 🔬 VM 06 · `10.0.70.61` — Worker Test Shard 01 (`wrk-test01`) |
| 8 | `vm-10-0-70-62` | `http://10.0.70.164:4079/vm/10.0.70.62` | `/home/mboyle` | 🔬 VM 07 · `10.0.70.62` — Worker Test Shard 02 (`wrk-test02`) |
| 9 | `vm-10-0-70-63` | `http://10.0.70.164:4079/vm/10.0.70.63` | `/home/mboyle` | 🔬 VM 08 · `10.0.70.63` — Worker Test Shard 03 (`wrk-test03`) |
| 10 | `vm-10-0-70-82` | `http://10.0.70.164:4079/vm/10.0.70.82` | `/home/mboyle` | 🚀 VM 09 · `10.0.70.82` — 64-Core RAG Indexer & Heavy CI (`wrk-test04`) |
| 11 | `vm-10-0-70-143` | `http://10.0.70.164:4079/vm/10.0.70.143` | `/home/mboyle` | 🔬 VM 10 · `10.0.70.143` — Worker Test Shard 05 (`wrk-test05`) |
| 12 | `vm-10-0-70-142` | `http://10.0.70.164:4079/vm/10.0.70.142` | `/home/mboyle` | 🔬 VM 11 · `10.0.70.142` — Worker Test Shard 06 (`wrk-test06`) |
| 13 | `vm-10-0-70-148` | `http://10.0.70.164:4079/vm/10.0.70.148` | `/home/mboyle` | 🔬 VM 12 · `10.0.70.148` — Worker Test Shard 07 (`wrk-test07`) |
| 14 | `vm-10-0-70-179` | `http://10.0.70.164:4079/vm/10.0.70.179` | `/home/mboyle` | 🔬 VM 13 · `10.0.70.179` — Worker Test Shard 08 (`wrk-test08`) |
| 15 | `vm-10-0-70-120` | `http://10.0.70.164:4079/vm/10.0.70.120` | `/home/mboyle` | 🛡️ VM 14 · `10.0.70.120` — Ephemeral Sandbox Pool 01 (`spr-pool01`) |
| 16 | `vm-10-0-70-149` | `http://10.0.70.164:4079/vm/10.0.70.149` | `/home/mboyle` | 🛡️ VM 15 · `10.0.70.149` — Ephemeral Sandbox Pool 02 (`spr-pool02`) |
| 17 | `vm-10-0-70-183` | `http://10.0.70.164:4079/vm/10.0.70.183` | `/home/mboyle` | 🛡️ VM 16 · `10.0.70.183` — Ephemeral Sandbox Pool 03 (`spr-pool03`) |
| 18 | `vm-10-0-70-185` | `http://10.0.70.164:4079/vm/10.0.70.185` | `/home/mboyle` | 🛡️ VM 17 · `10.0.70.185` — Ephemeral Sandbox Pool 04 (`spr-pool04`) |
| 19 | `vm-10-0-70-60` | `http://10.0.70.164:4079/vm/10.0.70.60` | `/root` | 🦊 VM 18 · `10.0.70.60` — GitLab Runner & Container Worker (`gitlab-runner01`) |
| 20 | `vm-10-0-70-95` | `http://10.0.70.164:4079/vm/10.0.70.95` | `/root` | 📊 VM 19 · `10.0.70.95` — Observability & Metrics Node (`mon-metrics01`) |
| 21 | `vm-10-0-70-112` | `http://10.0.70.164:4079/vm/10.0.70.112` | `/root` | 🗄️ VM 20 · `10.0.70.112` — Infrastructure Cache & Registry (`infra-cache01`) |
| 22 | `vm-10-0-70-169` | `http://10.0.70.164:4079/vm/10.0.70.169` | `/root` | 🔧 VM 21 · `10.0.70.169` — Auxiliary Service Node 01 (`aux-srv01`) |
| 23 | `vm-10-0-70-177` | `http://10.0.70.164:4079/vm/10.0.70.177` | `/root` | 🔧 VM 22 · `10.0.70.177` — Auxiliary Service Node 02 (`aux-srv02`) |
| 24 | `vm-10-0-70-217` | `http://10.0.70.164:4079/vm/10.0.70.217` | `/root` | 🔧 VM 23 · `10.0.70.217` — Auxiliary Service Node 03 (`aux-srv03`) |
| 25 | `vm-10-0-70-219` | `http://10.0.70.164:4079/vm/10.0.70.219` | `/root` | 🔧 VM 24 · `10.0.70.219` — Auxiliary Service Node 04 (`aux-srv04`) |
| 26 | `vm-10-0-70-241` | `http://10.0.70.164:4079/vm/10.0.70.241` | `/root` | 🔧 VM 25 · `10.0.70.241` — Auxiliary Service Node 05 (`aux-srv05`) |
| 27 | `vm-10-0-10-137` | `http://10.0.70.164:4079/vm/10.0.10.137` | `C:/Users/Administrator` | 🪟 VM 26 · `10.0.10.137` — BattleStation RTX 2080 Ti + Ryzen 9 + CDP Oracle (`battlestation`) |
| 28 | `vm-10-0-70-181` | `http://10.0.70.164:4079/vm/10.0.70.181` | `C:/Users/Administrator` | 🪟 VM 27 · `10.0.70.181` — Windows Bittorrent & Secondary CDP Oracle (`win-oracle02`) |

---

## 3. Live Verification Output

```text
1. /vm/all/api/config:
   {"status": true, "version": "1.2.0", "type": "terminal", "name": "🌐 ALL 27 BOYLENET VMs — Master Open Terminal Multiplexer (/vms/*)", "cwd": "/vms", "home": "/home/mboyle", "root": "/", "features": {"files": true, "terminal": true, "skills": true, "execute": true, "notebooks": true, "system": true}}

2. /vm/all/files/list?directory=/vms:
   count=27, first 3=['10.0.70.164-controller', '10.0.70.72-ai-vault-a', '10.0.70.83-ai-vault-b']

3. /vm/10.0.70.72/files/list?directory=/mnt/ai-models:
   count=74, first 3=['beam-1.0-501b-a23b', 'captcha-agent', 'captcha_ocr']

4. Instance OpenWebUI-Controller-164:8090 (http://openwebui.boylenet.themfboyles.com):
   - GET /api/v1/terminals/ -> HTTP 200, count=28 (first=boylenet-all-27-vms, last=vm-10-0-70-181)
   - GET /api/v1/users/user/settings -> HTTP 200, ui.terminalServers=28 (active=['boylenet-all-27-vms']), ui.toolServers=5 (active=5)
   - GET /api/v1/terminals/boylenet-all-27-vms/files/list?directory=/vms -> HTTP 200, entries=27
   - Toolkits=31 (56 tools), Functions=12, Skills=128, Prompts=128, Models equipped=22

5. Instance OpenWebUI-VaultA-72:8080 (http://ai-webui.boylenet.themfboyles.com):
   - GET /api/v1/terminals/ -> HTTP 200, count=28 (first=boylenet-all-27-vms, last=vm-10-0-70-181)
   - GET /api/v1/users/user/settings -> HTTP 200, ui.terminalServers=28 (active=['boylenet-all-27-vms']), ui.toolServers=5 (active=5)
   - GET /api/v1/terminals/boylenet-all-27-vms/files/list?directory=/vms -> HTTP 200, entries=27
   - Toolkits=31 (56 tools), Functions=12, Skills=128, Prompts=128, Models equipped=22
```
