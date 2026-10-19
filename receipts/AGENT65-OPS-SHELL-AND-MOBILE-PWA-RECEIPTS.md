# AGENT-65 RECEIPTS — Natural-Language Ops Shell (`:4089`) & Mobile/Tablet PWA Command Deck (`:4086`)

- **Written At**: `2026-10-18T16:19:30Z`
- **Agent**: `Agent-65` (Swarms `#17`, `#25`, `#C9`, `#C12`)
- **Service A (NL Ops Shell)**:
  - **URL**: `http://ops.boylenet.themfboyles.com` (`http://10.0.70.164:4089`)
  - **Systemd Unit**: `/etc/systemd/system/bd-ops-web.service` (`active (running)`)
  - **Web / Engine Script**: `/home/mboyle/bin/bd-ops-web.py`
  - **CLI Binary**: `/home/mboyle/bin/bd-ops`
- **Service B (Mobile & Tablet PWA Command Deck)**:
  - **URL**: `http://mobile.boylenet.themfboyles.com` (`http://10.0.70.164:4086`)
  - **Systemd Unit**: `/etc/systemd/system/bd-mobile-pwa.service` (`active (running)`)
  - **PWA Service Script**: `/home/mboyle/bin/bd-mobile-pwa.py`
  - **PWA Manifest & Worker**: `GET /manifest.json`, `GET /sw.js`, `GET /icon-192.svg`, `GET /icon-512.svg`

---

## 1. Service A — Boylenet Natural-Language Ops Shell (`http://ops.boylenet.themfboyles.com` / `:4089`)

### Capabilities Built
1. **Multi-Engine Natural-Language Intent Translator (`/api/translate` & `bd-ops --dry-run`)**:
   - Supports **Kimi Code CLI** (`/home/mboyle/.local/bin/kimi -p "..."` with `stdin=subprocess.DEVNULL`), **Local 4xT4 GPU Models** (`http://10.0.70.164:4000/v1/chat/completions`), and **Sub-Millisecond Deterministic Guard**.
   - Translates natural-language infrastructure queries into 12 pre-vetted, parameterized playbooks across `bd-pool-autobalancer`, `bd-gpu-orchestrator` (`bd-tier-switch`), `bd-infra-sentinel`, `bd-remote-pytest-turbo`, `bd-vault-curator`, `bd-semantic-search`, and `bd-hardware-telemetry`.
2. **Dry-Run Preview Mode (`READ-ONLY` vs `STATE-CHANGE`)**:
   - Displays exact command, target node(s), safety classification (`READ-ONLY` vs `STATE-CHANGE`), risk score, tool binary, and translator latency before any execution.
3. **1-Click Verified Playbook Execution (`/api/execute` & `bd-ops --execute`)**:
   - Executes only pre-vetted playbooks (zero arbitrary shell injection surface) and logs all runs to `/var/tmp/bd-ops-history.jsonl`.

### Verified Dry-Run Previews & Live Executions (`rc=0`)
```text
$ /home/mboyle/bin/bd-ops "check GPU temps across all 4 T4 nodes" --execute
==========================================================================================
 BOYLENET 4x TESLA T4 GPU CLUSTER TELEMETRY (active_model=qwythos-27b-mtp-bf16 | tier=tier2)
==========================================================================================
 NODE           MGMT IP       ROCE IP       TEMP     VRAM MiB        POWER     DDR4 RAM GiB     STATUS
------------------------------------------------------------------------------------------
 aidev          10.0.70.83    10.0.75.11    74°C     14403/15360     32.4W     6.2/379.8        ONLINE (rc=0)
 ai-srv01       10.0.70.72    10.0.75.12    72°C     12025/15360     31.4W     6.6/314.9        ONLINE (rc=0)
 ai-ollama01    10.0.70.228   10.0.75.13    73°C     8603/15360      31.1W     6.1/440.9        ONLINE (rc=0)
 ai-infer01     10.0.70.125   10.0.75.14    61°C     611/15360       30.7W     6.0/566.9        ONLINE (rc=0)
==========================================================================================

$ /home/mboyle/bin/bd-ops "show vSAN disk groups on esxi01" --execute
==========================================================================================
 BOYLENET vSAN DATASTORE & DISK GROUP INSPECTOR (Filter: esxi01)
==========================================================================================
 Datastore : vsanDatastore | Accessible: True
 Capacity  : 135.56 TiB used / 328.45 TiB total (41.27% used, 192.89 TiB free)
 Totals    : 11 Disk Groups | 11 Cache SSDs | 64 Capacity Disks
------------------------------------------------------------------------------------------
 Host: esxi01.boylenet.themfboyles.com (10.0.20.41) | vSAN IP: 10.0.60.21 | iLO: 10.0.10.10 | Rack: U11–U12
   Hardware : HP ProLiant DL380 Gen9 | CPU: 8.3% | RAM: 650.86/767.87 GiB (84.8%)
   vSAN DGs : 2 Disk Groups | 2 Cache SSDs | 11 Capacity Disks
     [Disk Group #1] Cache SSD: mpx.vmhba0:C2:T1:L0 -> 6 Capacity Disks:
        - naa.5000cca01ad7dc48
        - naa.5000cca01ab112cc
        - naa.5000cca01aa7e5ec
        - naa.5000cca01aa9a96c
        - naa.50000c0f01e0c290
        - naa.50000c0f012d3630
     [Disk Group #2] Cache SSD: mpx.vmhba0:C2:T0:L0 -> 5 Capacity Disks:
        - mpx.vmhba0:C2:T3:L0
        - naa.5000cca01aa800e0
        - mpx.vmhba0:C2:T13:L0
        - mpx.vmhba0:C2:T5:L0
        - mpx.vmhba0:C2:T4:L0
==========================================================================================
```

---

## 2. Service B — Boylenet Mobile & Tablet PWA Command Deck (`http://mobile.boylenet.themfboyles.com` / `:4086`)

### 5 Touch-Optimized Bottom-Bar Tabs
1. **Tab 1 — Pools & Quotas**:
   - Live progress bars for `$1,000 Free Cloud Session Credit Pool ($250 x Pools A/B/C/D, Expires Nov 5)`, `Claude Pool A/B/C/D`, `OpenAI Codex`, `Kimi Code CLI`, `Google Antigravity (AGY)`, and `Local 4x Tesla T4 GPU Cluster`.
2. **Tab 2 — 1-Tap Launch & Remote Control**:
   - Extracts live `https://claude.ai/code/session_...` Remote Control links from active tmux panes (5 active RC links detected live) for 1-tap opening in iOS/Android Safari, plus 1-tap role launcher (`POST /api/launch` -> `:4098/api/launch-role`).
3. **Tab 3 — 4xT4 GPUs & Model Switcher**:
   - Live VRAM, temperature (`61°C–74°C`), wattage (`30.7W–32.4W`), and DDR4 RAM telemetry across `aidev (.83)`, `ai-srv01 (.72)`, `ai-ollama01 (.228)`, and `ai-infer01 (.125)` + 1-tap tier/model switching (`POST /api/gpu-switch` -> `:4010/api/tiers/switch`).
4. **Tab 4 — Sentinel & Quick Heal**:
   - Live 83-service health matrix from `:4099/api/status` + 1-tap `Heal All` (`POST /api/sentinel-heal` -> `:4099/api/heal-now`).
5. **Tab 5 — All Web UIs Launcher**:
   - Searchable mobile tile grid for all 22 `.boylenet.themfboyles.com` applications.

---

## 3. UniFi DNS (`10.0.70.1`), Traefik (`:80`), and Dashy (`:4080`) Wiring

1. **Authoritative UniFi `static-dns` Registrations (`10.0.70.1`)**:
   - `ops` -> `10.0.70.164` (`_id=0b7e68b2-fd60-4818-9168-8f3766fbda1f`)
   - `ops.boylenet.themfboyles.com` -> `10.0.70.164` (`_id=1738a920-e2b7-4167-b4b9-65cfdebbd61f`)
   - `mobile` -> `10.0.70.164` (`_id=e7944bb1-2aa2-4049-8641-b6f56875031d`)
   - `mobile.boylenet.themfboyles.com` -> `10.0.70.164` (`_id=e03e3153-f8c3-40f4-91a1-90a042e538ff`)
   - Exported updated 418-record static DNS snapshot to `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/boylenet_static_dns_398.json`.
2. **Traefik Dynamic Ingress (`/home/mboyle/infra/expansion-layer/dynamic/boylenet-dns-ingress.yml`)**:
   - Added router `boylenet-ops` (`Host(\`ops.boylenet.themfboyles.com\`) || Host(\`ops\`)`) -> `boylenet-ops-svc` (`http://10.0.70.164:4089`).
   - Added router `boylenet-mobile` (`Host(\`mobile.boylenet.themfboyles.com\`) || Host(\`mobile\`)`) -> `boylenet-mobile-svc` (`http://10.0.70.164:4086`).
3. **Dashy Portal (`/home/mboyle/infra/dashy/conf.yml`)**:
   - Added `NL Ops (:4089)` and `Mobile Deck (:4086)` to top `navLinks` and `AI Command & Chat Studios`.

---

## 4. Health & Endpoint Verification Receipts (`rc=0`)

### A. `http://127.0.0.1:4089/health` & `http://ops.boylenet.themfboyles.com/health`
```json
{
  "status": "ok",
  "service": "bd-ops-web",
  "port": 4089,
  "url": "http://ops.boylenet.themfboyles.com",
  "playbooks_count": 12,
  "kimi_cli_present": true,
  "litellm_gateway": "http://10.0.70.164:4000",
  "safety_modes": [
    "READ-ONLY",
    "STATE-CHANGE"
  ],
  "timestamp": "2026-10-18T16:18:39Z"
}
```

### B. `http://127.0.0.1:4086/health` & `http://mobile.boylenet.themfboyles.com/health`
```json
{
  "status": "ok",
  "service": "bd-mobile-pwa",
  "port": 4086,
  "url": "http://mobile.boylenet.themfboyles.com",
  "pwa_manifest": "/manifest.json",
  "service_worker": "/sw.js",
  "tabs": [
    "Pools & Quotas",
    "1-Tap Launch & Remote Control",
    "4xT4 GPUs & Model Switcher",
    "Sentinel & Quick Heal",
    "All Web UIs Launcher"
  ],
  "apps_count": 22,
  "timestamp": "2026-10-18T16:18:39Z"
}
```

### C. Live Kimi Code CLI Translation Receipt (`POST http://ops.boylenet.themfboyles.com/api/translate`)
```json
{
  "rc": 0,
  "mode": "DRY-RUN PREVIEW",
  "timestamp": "2026-10-18T16:19:31Z",
  "query": "check GPU temps across all 4 T4 nodes",
  "playbook_id": "gpu_temps_all",
  "title": "Check GPU Temps, VRAM & Power Across All 4 T4 Nodes",
  "tool": "bd-gpu-orchestrator",
  "binary": "/home/mboyle/bin/bd-tier-switch",
  "safety_class": "READ-ONLY",
  "risk_level": "LOW (Zero State Mutation)",
  "target_node": "ai-srv01 (.72), ai-srv02 (.73), ai-ollama01 (.228), ai-infer01 (.125)",
  "command": "/home/mboyle/bin/bd-tier-switch status",
  "engine_used": "kimi-code-cli (/home/mboyle/.local/bin/kimi)",
  "translation_ms": 18101.8,
  "ai_trace": {
    "kimi": {
      "ok": true,
      "engine": "kimi-code-cli",
      "elapsed_s": 18.1,
      "playbook_id": "gpu_temps_all",
      "confidence": 0.99,
      "rationale": "Query matches verbatim: check GPU temps, VRAM, power on all 4 T4 nodes"
    }
  }
}
```
