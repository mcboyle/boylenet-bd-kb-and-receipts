# AGENT-44: Dashy Command Nexus Portal (`:4080`) & Traefik DNS VirtualHost Ingress (`:80`) Receipts

- **WRITTEN-AT**: `2026-10-18T12:18:00Z`
- **AGENT**: `Agent-44` (Dashy Container & Traefik Reverse-Proxy Architect)
- **HOST**: `10.0.70.164` (`hub-mesh01` / `controller.boylenet.themfboyles.com`)
- **VERDICT**: `PASS` (`100% GREEN` across `dashy-portal` `:4080` and all 24 Traefik VirtualHost routers / 66 DNS & shortname aliases on `:80`)

---

## 1. Deployed Dashy Container & Configuration (`10.0.70.164:4080`)

- **Compose Manifest**: `/home/mboyle/infra/dashy/docker-compose.yml`
- **Dashboard Configuration**: `/home/mboyle/infra/dashy/conf.yml` (mounted at `/app/user-data/conf.yml:ro`)
- **Container Status**:
  - `dashy-portal` (`lissy93/dashy:latest` / Dashy `v4.8.7`) — `Up (healthy)`, `restart: unless-stopped`
  - **Host Port Mapping**: `0.0.0.0:4080->8080/tcp`
  - **Networks**: `dashy_default`, `expansion-layer_default` (connected to `traefik-ingress`)
  - **Extra Hosts**: `host.docker.internal:host-gateway`
- **Portal Design & Features**:
  - **Title**: `"Boylenet Swarm OS • Command Nexus"`
  - **Description**: `"Unified 27-Node • 656-vCPU • 1.73 TB RAM • 4x Tesla T4 & Dual-Vault Frontier AI Infrastructure Portal"`
  - **Theme & Styling**: `one-dark` with custom obsidian glassmorphism CSS (`backdrop-filter: blur(14px)`, neon cyan `#38bdf8` hover glow, crisp Inter typography)
  - **Integrated Search**: Wired directly to local SearXNG (`http://10.0.70.164:8888/search?q=`) with `/g`, `/p`, `/d`, `/s` search bangs
  - **Header `navLinks` (8)**: Jarvis Cockpit (`:3002`), Swarm Chat (`:3003`), OpenWebUI (`:8090`), LiteLLM (`:4000/ui`), Grafana (`:3001`), Gitea (`:3000`), vCenter (`https://10.0.20.70/ui`), UniFi (`https://10.0.70.1`)
  - **Preconfigured Sections (10 Sections, 74 Total Items)**:
    1. `AI Command & Chat Studios` (9 items, hotkeys `1..5`: Jarvis Cockpit `:3002`, Swarm Chat `:3003`, OpenWebUI Controller `:8090`, OpenWebUI Vault A `.72:8080`, OpenWebUI Satellite `.228:8090`, LiteLLM Admin `.164:4000/ui`, LiteLLM Swagger `.164:4000`, LiteLLM Satellite `.228:4000/ui`, GPU Orchestrator `:4010/status`)
    2. `4-GPU Inference Engines & Model Vaults` (9 items: Tier 1 Pool Alpha `.72:8082`, Tier 1 Pool Beta `.125:8081`, Tier 2/3 Unified 4xT4 `.72:8083`, Vault B Tier 3 MoE `.83:8084`, Ollama `.228:11434`, Ollama Vault A `.72:8081`, Ollama Infer01 `.125:8005`, TEI Embeddings `:8081/docs`, TEI Reranker `:8082/docs`)
    3. `Observability, Telemetry & Tracing` (7 items, hotkeys `6..7`: Grafana `:3001`, Prometheus `:9090`, Alertmanager `:9093`, Langfuse `.228:3000`, cAdvisor `:8085`, Traefik `:8080/dashboard/`, Node Exporter `:9100/metrics`)
    4. `Developer Platform, Git, Search & AST` (9 items, hotkeys `8..9`: Gitea `:3000`, Code-Server `:8443`, SearXNG `:8888`, DevDocs `:8088`, Tree-Sitter AST `:8095/health`, Qdrant `.125:6333/dashboard`, Elasticsearch `:9200`, MinIO `:9001`, BulkDownloader `.72:5555`)
    5. `Local Package & Container Registries` (4 items: Docker Registry `:5000/v2/_catalog`, Verdaccio `:4873`, devpi `:3141`, apt-cacher-ng `:3142/acng-report.html`)
    6. `Remote Desktops & Browser Oracles` (4 items: Controller noVNC `.164:6080/vnc.html`, Vault A noVNC `.72:6080/vnc.html`, BattleStation CDP `.10.137:9222/json/version`, Bittorrent CDP `.70.181:9222/json/version`)
    7. `VMware vSphere, ESXi & HPE iLO Hardware` (9 items: vCenter `.20.70/ui`, ESXi01..04 `.20.41..44/ui`, HPE iLO4 `.10.10`, `.10.20`, `.10.40`, `.10.252`)
    8. `UniFi Network & Core Switching` (3 items: UniFi OS Gateway `https://10.0.70.1`, USW Pro Aggregation `10.0.10.111`, USW Pro 8 PoE `10.0.10.182`)
    9. `UniFi DNS VirtualHost Shortlinks (Traefik Port 80)` (9 items: `dashy.*`, `jarvis.*`, `chat.*`, `openwebui.*`, `litellm.*`, `grafana.*`, `gitea.*`, `codeserver.*`, `searxng.*`)
    10. `Fleet Node Directory (All VLAN 70 VMs & DNS Names)` (11 items covering `controller`, `ai`, `aidev`, `ai-infer01`, `ai-ollama01`, `bd..bd4`, `test1..test7`, `test3big`, `fresh149` & spares, `bittorrent`, `battlestation`)

---

## 2. Infrastructure Fixes Applied During Ingress Verification

1. **Docker Bridge Access to `BD8095` & `BD8443` iptables Chains on `10.0.70.164`**:
   - Added `172.16.0.0/12` `ACCEPT` rules to `BD8095` (`:8095` Tree-Sitter AST Server) and `BD8443` (`:8443` Code-Server) and persisted `/etc/iptables/rules.v4` so `traefik-ingress` (`172.21.0.4`) and `dashy-portal` health checks reach `:8095` and `:8443` with zero timeouts.
2. **GPU Orchestrator (`/status` Alias on `:4010`)**:
   - Extended `/usr/local/bin/bd-gpu-orchestrator.py` (and synced `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/bd-gpu-orchestrator.py`) so `GET /status` returns `STATE.get_status_payload()` alongside `/api/tiers/status` (`HTTP 200`).
3. **Langfuse (`.228:3000`) & Qdrant (`.125:6333/dashboard`) Systemd Endpoints**:
   - Enabled `langfuse-bridge.service` on `10.0.70.228:3000` and `qdrant-portal.service` on `10.0.70.125:6333` so both direct IP URLs and Traefik VirtualHosts (`langfuse.boylenet.themfboyles.com`, `qdrant.boylenet.themfboyles.com/dashboard`) return `HTTP 200`.

---

## 3. Traefik Port `:80` Reverse-Proxy VirtualHost Verification Matrix

Configured in `/home/mboyle/infra/expansion-layer/dynamic/boylenet-dns-ingress.yml` and live-reloaded by `traefik-ingress`:

| Router Name | Host Rules (`*.boylenet.themfboyles.com` + Shortname) | Backend Target | Verified HTTP Status |
|---|---|---|---:|
| `boylenet-dashy` | `dashy.*`, `dashy`, `portal.*`, `portal`, `dashboard.*`, `dashboard`, `controller.*` | `http://10.0.70.164:4080` | `200 OK` |
| `boylenet-jarvis` | `jarvis.*`, `jarvis`, `cockpit.*`, `cockpit` | `http://10.0.70.164:3002` | `200 OK` |
| `boylenet-chat` | `chat.*`, `chat` | `http://10.0.70.164:3003` | `200 OK` |
| `boylenet-openwebui` | `openwebui.*`, `openwebui` | `http://10.0.70.164:8090` | `200 OK` |
| `boylenet-litellm` | `litellm.*`, `litellm` | `http://10.0.70.164:4000` | `200 OK` |
| `boylenet-orchestrator` | `orchestrator.*`, `orchestrator` | `http://10.0.70.164:4010` | `200 OK` |
| `boylenet-gitea` | `gitea.*`, `gitea`, `git.*`, `git` | `http://10.0.70.164:3000` | `200 OK` |
| `boylenet-grafana` | `grafana.*`, `grafana` | `http://10.0.70.164:3001` | `302 -> /login` |
| `boylenet-prometheus` | `prometheus.*`, `prometheus` | `http://10.0.70.164:9090` | `302 -> /graph` |
| `boylenet-alertmanager` | `alertmanager.*`, `alertmanager` | `http://10.0.70.164:9093` | `200 OK` |
| `boylenet-minio` | `minio.*`, `minio` | `http://10.0.70.164:9001` | `200 OK` |
| `boylenet-cadvisor` | `cadvisor.*`, `cadvisor` | `http://10.0.70.164:8085` | `307 -> /containers/` |
| `boylenet-devdocs` | `devdocs.*`, `devdocs` | `http://10.0.70.164:8088` | `200 OK` |
| `boylenet-searxng` | `searxng.*`, `searxng`, `search.*`, `search` | `http://10.0.70.164:8888` | `200 OK` |
| `boylenet-codeserver` | `codeserver.*`, `codeserver`, `ide.*`, `ide` | `http://10.0.70.164:8443` | `302 -> /?folder=/home/coder` |
| `boylenet-verdaccio` | `verdaccio.*`, `verdaccio`, `npm.*`, `npm` | `http://10.0.70.164:4873` | `200 OK` |
| `boylenet-devpi` | `devpi.*`, `devpi`, `pypi.*`, `pypi` | `http://10.0.70.164:3141` | `200 OK` |
| `boylenet-acng` | `acng.*`, `acng`, `apt.*`, `apt` | `http://10.0.70.164:3142` | `200 OK` |
| `boylenet-elastic` | `elastic.*`, `elastic` | `http://10.0.70.164:9200` | `200 OK` |
| `boylenet-treesitter` | `treesitter.*`, `treesitter`, `ast.*`, `ast` | `http://10.0.70.164:8095` | `200 OK` |
| `boylenet-novnc` | `novnc.*`, `novnc` | `http://10.0.70.164:6080` | `200 OK` |
| `boylenet-langfuse` | `langfuse.*`, `langfuse` | `http://10.0.70.228:3000` | `200 OK` |
| `boylenet-qdrant` | `qdrant.*`, `qdrant` | `http://10.0.70.125:6333` | `200 OK` |
| `boylenet-ai-webui` | `ai-webui.*`, `ai-webui` | `http://10.0.70.72:8080` | `200 OK` |
