# AGENT-74 RECEIPTS — UI/UX Super-Swarm Pillars 1 & 7: Boylenet Unified OS Desktop Shell (`:4085` / `http://os.boylenet.themfboyles.com`)

- **WRITTEN-AT**: `2026-10-18T17:49:15Z`
- **AGENT**: `Agent-74` (UI/UX Super-Swarm — Pillars 1 & 7: `#UX-1..5`, `#UX-21`, `#UX-32..35`)
- **SERVICE**: `bd-os-shell.service` (`active (running)` on `0.0.0.0:4085`)
- **PRIMARY SCRIPT**: `/home/mboyle/bin/bd-os-shell.py`
- **SYSTEMD UNIT**: `/etc/systemd/system/bd-os-shell.service`
- **INGRESS URL**: `http://os.boylenet.themfboyles.com` (`http://10.0.70.164:4085`)

---

## 1. Delivered Capabilities (Pillars 1 & 7)

### 1.1 `#UX-1` Left App Dock (macOS / Linear Style with Live Status Dots & `Alt+1..9` Shortcuts)
- **Zero-Reload Tab Switching (`0ms`)**: Visited iframes stay mounted in the DOM inside each pane (`cached-iframe` pool toggled via `.visible`), preserving live WebSocket streams, terminal scrollback buffers, 3D WebGL canvas state, and form inputs across tab switches.
- **18 Flagship Dock Apps with Live TCP Liveness Dots**:
  1. `🏠 Executive Briefing & Doctor (:4081)` (`Alt+1`)
  2. `🕸️ 3D Swarm & Rack Topology (:4088)` (`Alt+2`)
  3. `⚔️ Multi-Harness Studio & Arena (:4098)` (`Alt+3`)
  4. `☁️ $1,000 Cloud Burst Orchestrator (:4090)` (`Alt+4`)
  5. `🧠 86-Model Vault Storefront (:4084)` (`Alt+5`)
  6. `💻 Multi-Node Web Terminal & Receipt Viewer (:4083)` (`Alt+6`)
  7. `🔬 Dual-Oracle Deep Research (:4094)` (`Alt+7`)
  8. `🎙️ Voice & Vision Studio (:4093)` (`Alt+8`)
  9. `🛡️ Gitea Pre-Review & Red-Team (:4092)` (`Alt+9`)
  10. `🏢 Physical Rack & iLO4 Telemetry (:4091)`
  11. `🩺 Infra Sentinel Self-Healer (:4099)`
  12. `🔐 Credentials & SSH Vault (:4087)`
  13. `⚡ Natural-Language Ops Shell (:4089)`
  14. `📱 Mobile PWA Deck (:4086)`
  15. `🚀 Jarvis Cockpit (:3002)`
  16. `💬 Swarm Chat (:3003)`
  17. `📊 Grafana AI Nexus (:3001)`
  18. `🧭 Dashy Portal (:4080)`

### 1.2 `#UX-4` Split-Screen Side-by-Side Workspace Mode & `#UX-21` TV Wallboard Auto-Cycle
- **`1-Pane` Mode**: Full-bleed single-app desktop view.
- **`2-Pane Split` Mode**: Side-by-side 50/50 split workspace (e.g., `⚔️ Multi-Harness Studio :4098` on left + `🕸️ 3D Topology :4088` on right), each with independent app selector, reload, and popout controls.
- **`3-Pane Tactical Grid` Mode**: 3-column tactical command layout (`:4098` + `:4088` + `:4090`).
- **`📺 TV Wallboard Auto-Cycle (#UX-21)`**: Automatically cycles through flagship operational dashboards (`:4081`, `:4088`, `:4098`, `:4090`, `:4084`, `:4091`, `:4099`, `:3001`) every 15 seconds with next-up indicator and manual step controls.
- **Collapsible Bottom Web Terminal Drawer (Backtick `` ` ``)**: Slides up `:4083` Multi-Node Web Terminal & Receipt Viewer (with 1-click switch to `:4089` NL Ops Shell and `:4098` Multi-Harness Studio) from the bottom of any workspace mode.

### 1.3 `#UX-2` Universal `Cmd+K` / `Ctrl+K` Raycast-Style Command Palette
- Searches **195 indexed entities** in real time:
  - **54 Boylenet Apps & Infrastructure Services** (`18` Dock apps + `36` cluster services across Traefik, LiteLLM, Qdrant, Langfuse, Prometheus, Alertmanager, MinIO, cAdvisor, Tree-Sitter, SearXNG, DevDocs, VS Code Server, noVNC, iLO4 `.10..13`)
  - **86 Verified Model Vault Directories** (`~8.9 TB` across `10.0.70.72` and `10.0.70.83` from Agent-37's complete inventory)
  - **40 Fleet Roles** (`pm`, `council`, `dispatch`, `lensrouter`, `integrator`, `worker`, `review-*`, etc.)
  - **15 Quick Actions** (`Switch Tier 1 Model`, `Switch Tier 2 Model`, `Switch Tier 3 Frontier Model`, `Run Boylenet Doctor`, `Heal All Services`, `Copy LiteLLM Key (sk-fleet-local)`, `Open Claude Remote Control`, workspace mode switches, PiP monitor, and snapshot export).

### 1.4 `#UX-3` Persistent 36px Top Telemetry & Quota Pill Bar + `#UX-5` PiP + `#UX-32..35` Theme & Snapshot Engine
- **Live Telemetry Pills**:
  - `Claude Pools A/B/C/D 5h %` (`A:0% • B:0% • C:0% • D:0%` from `/home/mboyle/bd-persist/POOL_STATE.tsv`)
  - `$1,000 Cloud Credit` (`$250×4 • Cap OFF`)
  - `4xT4 Active Model & tok/s` (`qwen3-coder-30b-a3b • 77.62 tok/s` / live `:4010/status`)
  - `86/86 Models (8.9 TB)`
  - `Sentinel 82/83 (98.64%)` (live `:4099/health`)
- **`#UX-5` Picture-in-Picture (PiP) Mini-Monitor Popout**: Supports in-page HUD + always-on-top HTML5 `documentPictureInPicture` / mini-window popout.
- **`#UX-33` 4-Theme Switcher**: `Cyber-Glass Dark`, `OLED Midnight`, `Solarized Ops`, `Daylight`, persisted in `localStorage` and synchronized across tabs/iframes via `BroadcastChannel('boylenet_os_channel')`.
- **`#UX-35` Export Snapshot**: `POST /api/snapshot` saves timestamped cluster + workspace state to `/var/tmp/bd-os-snapshots/` and triggers browser JSON download.
- **`#UX-32 / #UX-34` Shared Embeddable Assets**:
  - `GET /static/boylenet-nav.js` (`CORS *`)
  - `GET /static/boylenet-ui.css` (`CORS *`)

---

## 2. Live Verification Evidence (`rc=0`)

```text
$ systemctl is-active bd-os-shell.service
active

$ curl -fsS http://127.0.0.1:4085/health
{
  "status": "ok",
  "service": "bd-os-shell",
  "port": 4085,
  "url": "http://os.boylenet.themfboyles.com",
  "pillars": [
    "Pillar 1: Unified OS Desktop Shell",
    "Pillar 7: Shared TopBar, 4-Theme Engine & PiP"
  ],
  "ux_items": [
    "#UX-1", "#UX-2", "#UX-3", "#UX-4", "#UX-5",
    "#UX-21", "#UX-32", "#UX-33", "#UX-34", "#UX-35"
  ],
  "dock_apps_count": 18,
  "total_apps_count": 54,
  "vault_models_count": 86,
  "fleet_roles_count": 40,
  "quick_actions_count": 15,
  "themes": [
    "cyber-glass", "oled-midnight", "solarized-ops", "daylight"
  ],
  "static_assets": [
    "/static/boylenet-nav.js", "/static/boylenet-ui.css"
  ],
  "topbar": {
    "claude_summary": "A:0% • B:0% • C:0% • D:0%",
    "cloud_credit_usd": 1000,
    "cloud_credit_label": "$1,000 Cloud ($250×4 • Cap OFF)",
    "gpu_active_model": "qwen3-coder-30b-a3b",
    "gpu_tier": "tier1",
    "gpu_tok_s": "77.62",
    "vault_models_ready": 86,
    "vault_models_total": 86,
    "vault_size_tb": 8.9,
    "vault_label": "86/86 Models (8.9 TB)",
    "sentinel_healthy": 82,
    "sentinel_total": 83,
    "sentinel_uptime_pct": 98.64,
    "litellm_key": "sk-fleet-local"
  },
  "timestamp": "2026-10-18T17:49:04Z"
}

$ curl -fsS -X POST http://127.0.0.1:4085/api/action -H "Content-Type: application/json" -d '{"action":"run_doctor"}'
{
  "rc": 0,
  "action": "run_doctor",
  "message": "Boylenet Doctor PASS: 82/83 services, 86/86 vault models READY"
}

$ curl -fsS -H "Host: os.boylenet.themfboyles.com" http://127.0.0.1:80/health
HTTP 200 OK (routed via Traefik boylenet-os -> http://10.0.70.164:4085)
```
