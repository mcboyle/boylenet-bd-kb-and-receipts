# 🏛️ BD-KB 08: Complete Infrastructure Services, Docker Containers, Systemd Daemons & Portals Bible

- **Document ID**: `bd-kb-08-complete-services-containers-portals-daemons-guide`
- **Native KB ID**: `bd-kb-services-portals-daemons-bible`
- **Canonical Path**: `/home/mboyle/bd-persist/bd-kb/08-COMPLETE-SERVICES-CONTAINERS-PORTALS-AND-DAEMONS-GUIDE.md`
- **Author**: `Agent-184` (Complete Infrastructure Services, Docker Containers, Systemd Daemons & Portals Bible Author)
- **Scope**: Authoritative, zero-gap operational reference covering every running web portal (`:4079`–`:4099`, `:8090`, `.72:8080`, `:3000`, `:3001`, `:3002`, `:3003`, `:3080`, `:4000`, `:4010`, `:8081`, `:8082`, `:9090`, plus supporting endpoints), all 27+ Docker containers on `.164` / `.72` / `.83` / `.125` / `.228`, all system-level (`/etc/systemd/system/`) and user-level (`~/.config/systemd/user/`) systemd daemons & timers across `.164`, `.72`, `.83`, `.125`, `.228`, `.82`, `.137`, and `.181`, and the dual-layer NGINX `:443` HTTPS + Traefik `:80` HTTP ingress routing mesh (`*.boylenet.themfboyles.org` / `*.boylenet.themfboyles.com`).

---

## 1. Master Web Portals & Core Endpoints Bible (`:4079`–`:4099`, `:8090`, `.72:8080`, `:3000`–`:9090`)

Every interactive web studio, AI gateway, observability dashboard, and API portal in the Boylenet cluster is cataloged below with its exact backend script or container image, systemd unit or Docker container name, local HTTP URL, wildcard HTTPS `:443` URL (`*.boylenet.themfboyles.org` & `.com`), health probe endpoint, and operational role.

### 1.1 The 21 Boylenet AI Command Studios & Gateways (`:4079` through `:4099`)

All `:4079`–`:4099` portals run on Controller `.164` (`10.0.70.164`) and are continuously monitored every `60s` by `bd-infra-sentinel.service` (`:4099`).

| Port | Portal Name | Backend Script / Image | Systemd Unit / Container | Local URL | HTTPS `:443` URL (`*.boylenet.themfboyles.org`) | Health Endpoint | What It Does |
|---:|---|---|---|---|---|---|---|
| **`:4079`** | **OpenWebUI Universal Tool Gateway** | `/home/mboyle/bin/bd-openwebui-tool-gateway.py` | `/etc/systemd/system/bd-openwebui-tool-gateway.service` | `http://10.0.70.164:4079` | `https://gateway.boylenet.themfboyles.org`<br>`https://tools.boylenet.themfboyles.org` | `GET /health`<br>(`200 OK`, JSON) | Exposes 50 native OpenWebUI toolkits (`core`, `infra`, `rag`, `coding`, `fleet`), OpenAPI 3.1 specs (`/openapi/infra.json`), 27-VM command execution (`/vm/<vm>/execute`), file browsing, Git sync, and MCP bridging for `:8090` and `.72:8080`. |
| **`:4080`** | **Dashy Master Command Portal** | `lissy93/dashy:latest` (`->8080/tcp`) | Docker: `dashy-portal` | `http://10.0.70.164:4080` | `https://dashy.boylenet.themfboyles.org`<br>`https://portal.boylenet.themfboyles.org`<br>`https://dashboard.boylenet.themfboyles.org` | `GET /`<br>(`200 OK`, HTML) | Central single-pane-of-glass launcher directory linking all 15+ Boylenet AI studios, 27-node infrastructure consoles, observability dashboards, and local registries with live status indicators. |
| **`:4081`** | **Morning Executive Briefing, Universal Semantic Search & 1-Click Cluster Doctor** | `/home/mboyle/bin/bd-executive-briefing.py` | `/etc/systemd/system/bd-executive-briefing.service` | `http://10.0.70.164:4081` | `https://briefing.boylenet.themfboyles.org`<br>`https://doctor.boylenet.themfboyles.org`<br>`https://search.boylenet.themfboyles.org` | `GET /health`<br>(`200 OK`, JSON) | Synthesizes daily executive swarm reports, overnight train progress, universal hybrid semantic search across Qdrant + BD KB, and 1-Click Cluster Doctor self-diagnostic checks. |
| **`:4082`** | **Zero-Friction SSO, QR Handoff & Root CA Trust Portal** | `/home/mboyle/bin/bd-trust-and-sso-portal.py` | `/etc/systemd/system/bd-trust-and-sso-portal.service` | `http://10.0.70.164:4082` | `https://ca.boylenet.themfboyles.org`<br>`https://sso.boylenet.themfboyles.org`<br>`https://trust.boylenet.themfboyles.org` | `GET /health`<br>(`200 OK`, JSON) | Distributes the Boylenet Root CA certificate (`.crt`/`.mobileconfig`) for desktop/iOS/Android trust, provides QR mobile session handoff, and manages zero-friction LAN SSO cookies. |
| **`:4083`** | **Browser Multi-Node Terminal, Live Agent TV Wall & Receipt Explorer** | `/home/mboyle/bin/bd-web-terminal-drawer.py` | `/etc/systemd/system/bd-web-terminal-drawer.service` | `http://10.0.70.164:4083` | `https://terminal.boylenet.themfboyles.org`<br>`https://tv.boylenet.themfboyles.org` | `GET /health`<br>(`200 OK`, JSON) | Interactive browser PTY/WebSocket terminal across all 27 nodes, 16-pane Live Agent TV Wall streaming active tmux/subagent sessions, and searchable task receipt explorer. |
| **`:4084`** | **86-Model AI Vault Storefront** | `/home/mboyle/bin/bd-model-storefront.py` | `/etc/systemd/system/bd-model-storefront.service` | `http://10.0.70.164:4084` | `https://models.boylenet.themfboyles.org`<br>`https://vault.boylenet.themfboyles.org` | `GET /health`<br>`GET /api/models`<br>(`200 OK`, JSON) | Interactive catalog and benchmark explorer for all 86 Local Vault GGUF/Ollama models (`8.9+ TB` across `.72`, `.83`, `.125`, `.228`, `.137`) + 12 Cloud frontier models, with live `tok/s`, quantization specs, and 1-click GPU tier profile switching. |
| **`:4085`** | **Boylenet Unified OS Single-Window Desktop Shell** | `/home/mboyle/bin/bd-os-shell.py --port 4085` | `/etc/systemd/system/bd-os-shell.service` | `http://10.0.70.164:4085` | `https://os.boylenet.themfboyles.org`<br>`https://boylenet.themfboyles.org` *(default `:443` catch-all)* | `GET /health`<br>(`200 OK`, JSON) | macOS/Desktop-style single-window web operating system shell hosting dockable multi-app windows, integrated terminal drawer, command palette (`Cmd+K`), and embedded studios (`:4079`–`:4099`) without tab sprawl. |
| **`:4086`** | **Mobile & Tablet PWA Command Deck** | `/home/mboyle/bin/bd-mobile-pwa.py --port 4086` | `/etc/systemd/system/bd-mobile-pwa.service` | `http://10.0.70.164:4086` | Traefik `:80`: `http://mobile.boylenet.themfboyles.org`<br>NGINX `:443`: `https://dreamer.boylenet.themfboyles.org` / `https://memory.boylenet.themfboyles.org` *(see §4.3 note)* | `GET /health`<br>(`200 OK`, JSON) | Touch-optimized iOS/Android Progressive Web App (PWA) command deck for monitoring swarm queues, approving cuts, inspecting pool quotas, and triggering mobile voice/chat actions. |
| **`:4087`** | **Unified Credentials, API Key & 27-Node SSH Health Vault** | `/home/mboyle/bin/bd-credentials-vault.py` | `/etc/systemd/system/bd-credentials-vault.service` | `http://10.0.70.164:4087` | Traefik `:80`: `http://vault.boylenet.themfboyles.org`<br>NGINX `:443`: `https://prereview.boylenet.themfboyles.org` *(see §4.3 note)* | `GET /health`<br>(`200 OK`, JSON) | Manages out-of-VCS provider OAuth/API tokens (Claude Pools A–D, Codex, AGY, Kimi, OpenRouter, Grok), tracks token TTL/refresh status (`O1789`/`O3050`), and audits passwordless SSH mesh connectivity across all 27 VMs. |
| **`:4088`** | **Live 3D/2D Architecture Topology & HiveMind Swarm Visualizer** | `/home/mboyle/bin/bd-topology-visualizer.py` | `/etc/systemd/system/bd-topology-visualizer.service` *(also referred to as `bd-hivemind-visualizer`)* | `http://10.0.70.164:4088` | `https://topology.boylenet.themfboyles.org`<br>`https://hivemind.boylenet.themfboyles.org` | `GET /health`<br>(`200 OK`, JSON) | Real-time 3D/2D interactive cluster topology, 12U physical rack digital twin, 25GbE RoCE vSAN dataflow map, and Qdrant HiveMind semantic vector cluster visualizer. |
| **`:4089`** | **Natural-Language Ops Shell & Verified Playbook Runner** | `/home/mboyle/bin/bd-ops-web.py --serve --port 4089` | `/etc/systemd/system/bd-ops-web.service` | `http://10.0.70.164:4089` | `https://ops.boylenet.themfboyles.org`<br>`https://credentials.boylenet.themfboyles.org`<br>`https://keys.boylenet.themfboyles.org` | `GET /health`<br>(`200 OK`, JSON) | Translates natural-language operator requests into guarded, pre-verified infrastructure playbooks (cache warmup, gridlock recovery, VM clone spinup, service health sweeps) backed by LiteLLM (`:4000`). |
| **`:4090`** | **Hybrid Cloud + Local Burst Orchestrator Web UI** | `/home/mboyle/bin/bd-cloud-burst-web.py` | `/etc/systemd/system/bd-cloud-burst-web.service` | `http://10.0.70.164:4090` | `https://burst.boylenet.themfboyles.org`<br>`https://quota.boylenet.themfboyles.org`<br>`https://cloud.boylenet.themfboyles.org` | `GET /health`<br>(`200 OK`, JSON) | Visualizes and controls O1740 Dynamic Capability Routing across Claude A–D, Codex, AGY, Kimi, and $0 Local GPU pools (`.83:18080`, `.72:8082`, `.125:8081`, `.137:11434`), plus Gitea/GitHub 8-repo sync status (`/api/git-repos`). |
| **`:4091`** | **Physical Rack, HPE iLO4/iLO5, vSphere vc04, vSAN & 4x T4 Telemetry Autopilot** | `/home/mboyle/bin/bd-hardware-telemetry.py` | `/etc/systemd/system/bd-hardware-telemetry.service` | `http://10.0.70.164:4091` | `https://hardware.boylenet.themfboyles.org`<br>`https://research.boylenet.themfboyles.org` *(NGINX alias)* | `GET /health`<br>(`200 OK`, JSON) | Polls HPE ProLiant DL380 Gen10 / Gen9 iLO4/iLO5 sensors (`10.0.10.10`, `.20`, `.40`, `.252`), vCenter `vc04` (`10.0.20.70`), 4x ESXi hosts (`10.0.20.41`–`.44`), 328.4 TiB vSAN datastore metrics, and 4x Tesla T4 GPU thermals/VRAM every 60s. |
| **`:4092`** | **Gitea Autonomous Pre-Review & OffSec Red-Team Swarm** | `/home/mboyle/bin/bd-gitea-prereview.py` | `/etc/systemd/system/bd-gitea-prereview.service` | `http://10.0.70.164:4092` | `https://offsec.boylenet.themfboyles.org`<br>`https://security.boylenet.themfboyles.org`<br>Traefik `:80`: `http://prereview.boylenet.themfboyles.org` | `GET /health`<br>`GET /api/status`<br>(`200 OK`, JSON) | Runs automated static analysis, Tree-Sitter AST structural checks (`:8095`), Ruff/Mypy fast-fail gates, and `CyberStrike-OffSec-35B` security red-team pre-reviews on Gitea (`:3000`) branches/PRs, storing reports in `/var/tmp/bd-gitea-prereview/`. |
| **`:4093`** | **Unified Voice, Vision & Multimodal Cockpit Bridge** | `/home/mboyle/bin/bd-multimodal-bridge.py` (via `/home/mboyle/BulkDownloader/venv/bin/python3`) | `/etc/systemd/system/bd-multimodal-bridge.service` | `http://10.0.70.164:4093` | `https://voice.boylenet.themfboyles.org`<br>`https://media.boylenet.themfboyles.org`<br>`https://multimodal.boylenet.themfboyles.org` | `GET /health`<br>(`200 OK`, JSON) | Provides STT (Whisper), TTS (Kokoro/Piper), vision OCR/screenshot analysis, and real-time voice command bridging into LiteLLM (`:4000`) and OpenWebUI (`:8090`). |
| **`:4094`** | **Deep Research & Dual-Oracle Citation Studio Web UI** | `/home/mboyle/bin/bd-deep-research-web.py` | `/etc/systemd/system/bd-deep-research-web.service` | `http://10.0.70.164:4094` | Traefik `:80`: `http://research.boylenet.themfboyles.org`<br>NGINX `:443`: `https://pwa.boylenet.themfboyles.org` / `https://mobile.boylenet.themfboyles.org` *(see §4.3 note)* | `GET /health`<br>`GET /api/status`<br>(`200 OK`, JSON) | Multi-hop autonomous research studio combining SearXNG (`:8888`), Dual Windows Chrome CDP Browser Oracles (`.137:9222` & `.181:9222`), and Qdrant RAG (`:6333`) to produce citation-backed technical reports. |
| **`:4095`** | **LobeChat AI Studio & Sovereign Airgap Deck** | `lobehub/lobe-chat:latest` (`->3210/tcp`) | Docker: `lobechat-studio` | `http://10.0.70.164:4095` | `https://lobechat.boylenet.themfboyles.org` | `GET /health`<br>`GET /`<br>(`200 OK`) | Rich multi-agent LobeChat workspace pre-wired to LiteLLM (`:4000`) and Sovereign Local GPU Orchestrator (`:4010`) with artifact rendering, plugin support, and airgap readiness. |
| **`:4096`** | **OpenCode Web UI & ACP Server** | `/home/mboyle/.local/bin/opencode serve --hostname 0.0.0.0 --port 4096` | `/etc/systemd/system/bd-opencode-web.service` | `http://10.0.70.164:4096` | `https://opencode.boylenet.themfboyles.org` | `GET /health`<br>`GET /`<br>(`200 OK`) | Browser-accessible OpenCode autonomous coding agent UI and Agent Communication Protocol (ACP) server connected to `http://10.0.70.164:4000/v1` with CORS enabled for Boylenet domains. |
| **`:4097`** | **OpenClaw Control UI & WebSocket Gateway** | `/home/mboyle/.local/bin/openclaw --dev gateway run ... --port 4097` | `/etc/systemd/system/bd-openclaw-web.service` | `http://10.0.70.164:4097` | `https://openclaw.boylenet.themfboyles.org` | `GET /health`<br>`GET /`<br>(`200 OK`) | OpenClaw agentic browser/computer-use control plane and WebSocket gateway (`auth: password`, wired to LiteLLM `:4000/v1`). |
| **`:4098`** | **Multi-Harness AI Studio & Side-by-Side Arena Web UI** | `/home/mboyle/bin/bd-multi-harness-web.py` | `/etc/systemd/system/bd-multi-harness-web.service` | `http://10.0.70.164:4098` | `https://harnesses.boylenet.themfboyles.org`<br>`https://arena.boylenet.themfboyles.org` | `GET /health`<br>(`200 OK`, JSON) | Side-by-side multi-harness prompt arena and comparator dispatching prompts concurrently to Claude Code, OpenAI Codex, Kimi K2.8, Antigravity, and Local GPU (`Qwen3.6-Heretic` / `2080 Ti`). |
| **`:4099`** | **Autonomous 83-Service Self-Healing Infrastructure Sentinel** | `/home/mboyle/bin/bd-infra-sentinel.py` | `/etc/systemd/system/bd-infra-sentinel.service` | `http://10.0.70.164:4099` | `https://sentinel.boylenet.themfboyles.org` | `GET /health`<br>`GET /api/status`<br>(`200 OK`, JSON) | Sweeps 68–83 cluster services, Docker containers, systemd units, remote GPU RPC endpoints, Traefik vhosts, ESXi hosts, and iLO interfaces every `60s`. Automatically executes approved self-heal commands after 2 consecutive failures and logs state to `/var/tmp/bd-infra-sentinel.json`. |

---

### 1.2 Core AI Gateways, Chat Portals, Git, Cockpits & Observability (`:8090`, `.72:8080`, `:3000`–`:9090`)

| Port / Host | Service / Portal Name | Backend Script / Container Image | Systemd Unit / Container Name | Local URL | HTTPS `:443` URL (`*.boylenet.themfboyles.org`) | Health Endpoint | What It Does |
|---|---|---|---|---|---|---|---|
| **`:8090`** (`.164`) | **OpenWebUI Master AI Portal** | `ghcr.io/open-webui/open-webui:main` (`->8080/tcp`) | Docker: `open-webui` (on `.164`) | `http://10.0.70.164:8090` | `https://openwebui.boylenet.themfboyles.org`<br>`https://chat.boylenet.themfboyles.org`<br>`https://ai.boylenet.themfboyles.org` | `GET /health`<br>(`200 OK`) | Primary multi-model chat portal integrated with LiteLLM (`:4000`), Local GPU Orchestrator (`:4010`), Universal Tool Gateway (`:4079`), Qdrant RAG (`:6333`), and Autonomous Mod 4 (BD KB Inlet Filter + 1-Click Actions). |
| **`.72:8080`** (`10.0.70.72`) | **OpenWebUI AI Vault A Satellite Portal** | `ghcr.io/open-webui/open-webui:main` (host network `:8080`) | Docker: `open-webui` (on `10.0.70.72`) | `http://10.0.70.72:8080` | `https://ai-webui.boylenet.themfboyles.org` | `GET /health`<br>`GET /`<br>(`200 OK`) | Secondary high-availability OpenWebUI instance running directly on AI Vault A (`ai-srv01`, `.72`) with direct local access to Tier-1 GPU models and `.164:4079` tools. |
| **`:3000`** (`.164`) | **Forgejo / Gitea Self-Hosted Git Forge** | `codeberg.org/forgejo/forgejo:latest` (`->3000/tcp`, SSH `2222->22/tcp`) | Docker: `forgejo` (on `.164`) | `http://10.0.70.164:3000` | `https://gitea.boylenet.themfboyles.org`<br>`https://git.boylenet.themfboyles.org` | `GET /`<br>`GET /api/v1/version`<br>(`200 OK`) | Local Git forge hosting `BulkDownloader` and all 8 mirrored repositories, CI/CD action runners (`act_runner` on `.83`), and webhook integration with `:4092` Pre-Review Swarm. |
| **`.228:3000`** (`10.0.70.228`) | **Langfuse LLM Observability & Tracing** | `langfuse/langfuse:2` (`->3000/tcp`) | Docker: `langfuse` (on `10.0.70.228`) | `http://10.0.70.228:3000` | `https://langfuse.boylenet.themfboyles.org` | `GET /api/public/health`<br>`GET /`<br>(`200 OK`) | Traces LLM token usage, latency, prompt/completion payloads, and cost analytics across LiteLLM proxies (`.164:4000` & `.228:4000`). |
| **`:3001`** (`.164`) | **Grafana Observability Dashboards** | `grafana/grafana-oss:latest` (`->3001/tcp`) | Docker: `grafana-dashboards` | `http://10.0.70.164:3001` | `https://grafana.boylenet.themfboyles.org` | `GET /api/health`<br>(`200 OK`) | Visualizes Prometheus (`:9090`), cAdvisor (`:8085`), and Node Exporter (`:9100`) time-series metrics for cluster CPU, RAM, disk I/O, and container telemetry. |
| **`:3002`** (`.164`) | **Jarvis / UniversalSwarmOS Next.js Cockpit** | `/usr/bin/node .../next start -p 3002 -H 0.0.0.0` (`/home/mboyle/UniversalSwarmOS/cockpit`) | User systemd: `~/.config/systemd/user/bd-cockpit.service` (alias `bd-cockpit-ui.service`) | `http://10.0.70.164:3002` | `https://jarvis.boylenet.themfboyles.org`<br>`https://cockpit.boylenet.themfboyles.org` | `GET /`<br>(`200 OK`, HTML) | Next.js UniversalSwarmOS real-time operator cockpit (`O1672`) displaying live train progress, agent seats, telemetry streams, and interactive swarm controls. |
| **`:3003`** (`.164`) | **Fleet Multi-Platform Chat UI (`bd-chat`)** | `/usr/bin/node /var/tmp/bd-chat-server.js` (`WorkingDirectory=/home/mboyle/bd-cuts/swarm-cockpit-fleet-view`) | User systemd: `~/.config/systemd/user/bd-chat.service` (alias `bd-chat-ui.service`) | `http://10.0.70.164:3003` | Traefik `:80`: `http://chat.boylenet.themfboyles.org` *(NGINX `:443` `chat.*` routes to `:8090`)* | `GET /`<br>(`200 OK`) | Lightweight Node.js multi-platform fleet chat interface and message relay console on port `:3003`. |
| **`:3004`** (`.164`) | **Cross-Pool Mesh Bus & WAL Daemon (`bd-mesh-bus`)** | `/usr/bin/python3 /home/mboyle/bin/bd-mesh-bus.py daemon --port 3004 --wal /var/tmp/bd-mesh-bus.wal` | User systemd: `~/.config/systemd/user/bd-mesh-bus.service` & System `/etc/systemd/system/bd-mesh-bus.service` | `http://10.0.70.164:3004` | Internal LAN HTTP/SSE endpoint | `GET /health`<br>(`200 OK`) | Write-Ahead Log (WAL) and Server-Sent Events (SSE) pub/sub bus (`Rule 60`/`bd-bus`) for zero-polling cross-pool agent coordination. |
| **`:3080`** (`.164`) | **Legacy LibreChat / OpenWebUI Compatibility Slot** | Superseded by OpenWebUI (`:8090`), LobeChat (`:4095`), and `bd-chat` (`:3003`) | Consolidated into `open-webui` (`:8090`) & `lobechat-studio` (`:4095`) | `http://10.0.70.164:8090` *(canonical replacement)* | `https://chat.boylenet.themfboyles.org`<br>`https://openwebui.boylenet.themfboyles.org` | `GET http://10.0.70.164:8090/health` | Historical LibreChat port slot (`:3080`); all chat workflows now route through OpenWebUI (`:8090`), LobeChat (`:4095`), and `bd-chat` (`:3003`). |
| **`:4000`** (`.164` & `.228`) | **LiteLLM Master & Satellite AI Gateway Proxies** | `ghcr.io/berriai/litellm:main-latest` (`->4000/tcp`) | Docker: `litellm-proxy` (on `.164` and `.228`) | `http://10.0.70.164:4000`<br>`http://10.0.70.228:4000` | `https://litellm.boylenet.themfboyles.org`<br>`https://litellm-228.boylenet.themfboyles.org` | `GET /health/liveliness`<br>(`200 OK`, `"I'm alive!"`) | Unified OpenAI-compatible (`/v1/chat/completions`, `/v1/embeddings`) LLM router multiplexing local GPU models (`.72`, `.83`, `.125`, `.228`, `.137`) and cloud providers (`sk-fleet-local`). |
| **`:4010`** (`.164`) | **Dynamic Tier 1/2/3 GPU Mode Orchestrator & Smart OpenAI Auto-Router** | `/usr/bin/python3 /usr/local/bin/bd-gpu-orchestrator.py` | `/etc/systemd/system/bd-gpu-orchestrator.service` | `http://10.0.70.164:4010` | `https://gpu.boylenet.themfboyles.org`<br>`https://orchestrator.boylenet.themfboyles.org` | `GET /health`<br>`GET /v1/models`<br>(`200 OK`, JSON) | Sovereign local GPU control plane and smart OpenAI router. Dynamically switches the 4x Tesla T4 cluster (`.72`, `.83`, `.125`, `.228`) between Tier-1 Dual-Pool (`35B` + `14B`), Tier-2 (`70B–122B` RPC), and Tier-3 (`671B` MoE RPC + `1.73 TB` RAM), while auto-routing requests by task complexity. |
| **`:8081`** (`.164`, `.125`, `.72`) | **TEI Embeddings (`.164`) / Local T4 Qwen2.5-Coder-14B (`.125`) / Ollama Bridge (`.72`)** | `.164`: `ghcr.io/huggingface/text-embeddings-inference:cpu-1.5`<br>`.125`: `/opt/llama-rpc/llama-server` (`Qwen2.5-Coder-14B`)<br>`.72`: Ollama bridge | `.164`: Docker `tei-embeddings`<br>`.125`: `llama-distributed-32b.service` (`llama-tier1-beta.service`) | `http://10.0.70.164:8081`<br>`http://10.0.70.125:8081`<br>`http://10.0.70.72:8081` | Internal LAN high-throughput inference & embedding endpoints | `GET /health`<br>(`200 OK`) | On `.164:8081`: CPU Text-Embeddings-Inference (`1024-d` vectors for Qdrant RAG). On `.125:8081`: 100% local Tesla T4 `llama-server` serving `qwen2.5-coder-32b-distributed` (`Qwen2.5-Coder-14B`, `32K` ctx, zero RPC). On `.72:8081`: AI Vault A Ollama HTTP bridge. |
| **`:8082`** (`.164` & `.72`) | **TEI Reranker (`.164`) / Tier-1 Pool Alpha CyberStrike-35B (`.72`)** | `.164`: `ghcr.io/huggingface/text-embeddings-inference:cpu-1.5`<br>`.72`: `/opt/llama-rpc/llama-server` (`Huihui-CyberStrike-OffSec-35B-Abliterated`) | `.164`: Docker `tei-reranker`<br>`.72`: `llama-tier1-alpha.service` | `http://10.0.70.164:8082`<br>`http://10.0.70.72:8082` | Internal LAN reranking & Tier-1 LLM server endpoints | `GET /health`<br>(`200 OK`) | On `.164:8082`: Cross-encoder RAG reranker (`tei-reranker`). On `.72:8082`: Tier-1 Pool Alpha `llama-server` serving `cyberstrike-35b` (`262K` context, FlashAttention, `q8_0` KV cache). |
| **`:9090`** (`.164`) | **Prometheus Time-Series Metrics Engine** | `prom/prometheus:latest` (`->9090/tcp`) | Docker: `prometheus` | `http://10.0.70.164:9090` | `https://prometheus.boylenet.themfboyles.org` | `GET /-/healthy`<br>(`200 OK`) | Scrapes and stores cluster-wide metrics from Node Exporter (`:9100`), cAdvisor (`:8085`), Traefik (`:8080`), and AI gateways, feeding Grafana (`:3001`) and Alertmanager (`:9093`). |

---

### 1.3 Additional Supporting Web Portals & Microservice Endpoints Across the Cluster

| Port / Host | Service Name | Backend / Container / Unit | Local URL | HTTPS `:443` / HTTP `:80` VHost | Health Endpoint | Purpose |
|---|---|---|---|---|---|---|
| **`:80` / `:8080`** (`.164`) | **Traefik v3.0 HTTP Ingress & Dashboard** | Docker: `traefik-ingress` (`traefik:v3.0`) | `http://10.0.70.164:8080/dashboard/` | `http://*.boylenet.themfboyles.org` (`:80`) | `GET :8080/dashboard/` (`200 OK`) | Dynamic HTTP `:80` host-header reverse proxy watching `/home/mboyle/infra/expansion-layer/dynamic/*.yml`. |
| **`:443`** (`.164`) | **NGINX Wildcard HTTPS Reverse Proxy** | Systemd: `nginx.service` (`/etc/nginx/sites-available/boylenet-https.conf`) | `https://10.0.70.164/` | `https://*.boylenet.themfboyles.org`<br>`https://*.boylenet.themfboyles.com` | `GET https://os.boylenet.themfboyles.org/health` | Terminates TLS (`HTTP/2`, Let's Encrypt Cloudflare DNS-01 wildcard cert + Boylenet Root CA) and proxies to all local and remote portals. |
| **`:3128`** (`.164`) | **Squid HTTP Caching Proxy** | Docker: `squid-cache` (`ubuntu/squid:latest`) | `http://10.0.70.164:3128` | Internal LAN proxy | TCP `:3128` | Forward HTTP/HTTPS caching proxy for ephemeral VMs and package downloads. |
| **`:3141`** (`.164`) | **DevPI Python Package Mirror** | Docker: `devpi-server` (`jonasal/devpi-server:latest`) | `http://10.0.70.164:3141` | `https://devpi.boylenet.themfboyles.org`<br>`https://pypi.boylenet.themfboyles.org` | `GET /` (`200 OK`) | Local PyPI wheel/package caching mirror for fast, airgap-capable `pip`/`uv` installs across all 27 nodes. |
| **`:3142`** (`.164`) | **APT-Cacher-NG Debian/Ubuntu Mirror** | Docker: `apt-cacher-ng` (`sameersbn/apt-cacher-ng:latest`) | `http://10.0.70.164:3142` | `https://acng.boylenet.themfboyles.org`<br>`https://apt.boylenet.themfboyles.org` | `GET /acng-report.html` (`200 OK`) | Local `.deb` package cache accelerating `apt-get` across Ubuntu VMs. |
| **`:4873`** (`.164`) | **Verdaccio NPM Registry Mirror** | Docker: `verdaccio` (`verdaccio/verdaccio:latest`) | `http://10.0.70.164:4873` | `https://verdaccio.boylenet.themfboyles.org`<br>`https://npm.boylenet.themfboyles.org` | `GET /` (`200 OK`) | Local NPM package cache for Node.js/TypeScript builds and agent CLIs. |
| **`:5000`** (`.164`) | **Local Docker Registry v2 Mirror** | Docker: `docker-mirror` (`registry:2`) | `http://10.0.70.164:5000` | Internal LAN registry | `GET /v2/_catalog` (`200 OK`) | Local OCI/Docker image cache (`registry:2`). |
| **`:6080`** (`.164`, `.72`, `.82`) | **noVNC HTML5 Virtual Desktop** | Systemd: `bd-novnc.service` (`websockify` -> `127.0.0.1:5900` X11VNC/Xvfb `:99`) | `http://10.0.70.164:6080/vnc.html`<br>`http://10.0.70.72:6080/vnc.html`<br>`http://10.0.70.82:6080/vnc.html` | `https://novnc.boylenet.themfboyles.org` | `GET /vnc.html` (`200 OK`) | Browser-accessible 1920x1080 Openbox GUI desktop (`DISPLAY=:99`) for GUI testing and visual inspection. |
| **`:8085`** (`.164`) | **Google cAdvisor Container Telemetry** | Docker: `cadvisor-metrics` (`gcr.io/cadvisor/cadvisor:latest`) | `http://10.0.70.164:8085` | `https://cadvisor.boylenet.themfboyles.org` | `GET /containers/` (`200 OK`) | Per-container CPU, memory, filesystem, and network metrics exporter. |
| **`:8088`** (`.164`) | **DevDocs Offline Documentation Mirror** | Docker: `devdocs-mirror` (`ghcr.io/freecodecamp/devdocs:latest`) | `http://10.0.70.164:8088` | `https://devdocs.boylenet.themfboyles.org` | `GET /` (`200 OK`) | Offline API documentation reference portal for airgapped agent and operator lookups. |
| **`:8095`** (`.164`) | **Tree-Sitter AST Structural Analysis Server** | Process: `/usr/bin/python3 /home/mboyle/infra/support-layer/ast_server.py` | Managed by `bd-infra-sentinel.service` (`164-docker-treesitter`) | `http://10.0.70.164:8095` | `https://treesitter.boylenet.themfboyles.org`<br>`https://ast.boylenet.themfboyles.org` | `GET /health` (`200 OK`) | Fast AST parsing (`/parse`, `/query`) used by `:4092` Gitea Pre-Review and `ratf` symbolic slicing. |
| **`:8099`** (`.164`) | **BD-RAG Transcript & Repo Query Server** | Systemd: `/etc/systemd/system/bd-rag.service` (`/home/mboyle/bd-persist/harness/bd-rag/server2.py`) | `bd-rag.service` | `http://10.0.70.164:8099` | Internal LAN endpoint | TCP `:8099` / `GET /health` | Fleet transcript and codebase RAG query microservice. |
| **`:8100`** (`.164`) | **BD-MCP Streamable-HTTP Fleet Server** | Systemd: `/etc/systemd/system/bd-mcp.service` (`/home/mboyle/bd-persist/harness/bd-mcp/server.py --http`) | `bd-mcp.service` | `http://10.0.70.164:8100` | Internal LAN MCP endpoint | TCP `:8100` | Shared fleet state, queue claims, law lookups, and gate collection over streamable HTTP. |
| **`:8443`** (`.164`) | **Code-Server Browser VS Code IDE** | Docker: `code-server` (`codercom/code-server:latest`) | `code-server` | `http://10.0.70.164:8443` | `https://codeserver.boylenet.themfboyles.org`<br>`https://ide.boylenet.themfboyles.org` | `GET /` (`200 OK` / `302`) | Full VS Code IDE in the browser mounted to Controller `.164` workspaces. |
| **`:8888`** (`.164`) | **SearXNG Private Metasearch Engine** | Docker: `searxng-search` (`searxng/searxng:latest`) | `searxng-search` | `http://10.0.70.164:8888` | `https://searxng.boylenet.themfboyles.org` | `GET /` (`200 OK`) | Privacy-respecting metasearch engine aggregating web/technical results for `:4094` Deep Research and OpenWebUI (`:8090`). |
| **`:9000` / `:9001`** (`.164`) | **MinIO S3-Compatible Object Storage & Console** | Docker: `minio` (`minio/minio:latest`) | `minio` | `http://10.0.70.164:9000` (S3 API)<br>`http://10.0.70.164:9001` (Web Console) | `https://minio.boylenet.themfboyles.org` (`->:9001`) | `GET :9000/minio/health/live` (`200 OK`) | S3-compatible object storage for artifacts, backups, and multimodal payloads. |
| **`:9093`** (`.164`) | **Prometheus Alertmanager** | Docker: `alertmanager` (`prom/alertmanager:latest`) | `alertmanager` | `http://10.0.70.164:9093` | `https://alertmanager.boylenet.themfboyles.org` | `GET /-/healthy` (`200 OK`) | Alert deduplication, grouping, and routing for Prometheus metrics. |
| **`:9200`** (`.164`) | **Elasticsearch 8.13 Full-Text Search** | Docker: `elasticsearch` (`elasticsearch:8.13.0`) | `elasticsearch` | `http://10.0.70.164:9200` | `https://elastic.boylenet.themfboyles.org` | `GET /` (`200 OK`) | Full-text indexing and log search engine on `.164`. |
| **`:9222`** (`.164`, `.137`, `.181`) | **Chrome CDP Browser Oracles & Local Proxy Bridge** | `.164`: `bd-chrome-bridge.service` (`/home/mboyle/bin/bd-chrome-bridge.py`)<br>`.137`: BattleStation Windows Chrome CDP<br>`.181`: Bittorrent Windows Chrome CDP | `bd-chrome-bridge.service` (`.164`) | `http://10.0.70.164:9222`<br>`http://10.0.10.137:9222`<br>`http://10.0.70.181:9222` | Internal LAN CDP endpoints | `GET /json/version` (`200 OK`, JSON) | Dual Windows Chrome CDP Browser Oracles (`Rule 79`) providing `$0` marginal cost access to Gemini, ChatGPT, Claude A, and Claude B web sessions, plus E2E visual portal verification. |
| **`.125:6333`** (`10.0.70.125`) | **Qdrant Vector DB Web UI & REST Endpoint** | Systemd: `qdrant-portal.service` (`/usr/bin/python3 /usr/local/bin/qdrant_portal.py`) | `/etc/systemd/system/qdrant-portal.service` | `http://10.0.70.125:6333` | `https://qdrant.boylenet.themfboyles.org` | `GET /healthz` (`200 OK`) | Hosts all 6 Boylenet vector collections (`bd_master_kb`, `boylenet_episodic_memory`, `fleet_lessons_hivemind`, `boylenet_fleet_rag`, `boylenet_codebase_ast`, `swarm_docs_rag`). |
| **`.83:18080`** (`10.0.70.83`) | **Qwen3.6-35B-A3B-Heretic Flagship Local Coding Server** | Systemd: `llama-heretic-18080.service` (`/opt/llama-rpc/run-heretic-18080.sh`) | `/etc/systemd/system/llama-heretic-18080.service` | `http://10.0.70.83:18080` | Routed via `:4010` (`gpu.*`) & `:4000` (`litellm.*`) | `GET /health`<br>`GET /v1/models` (`200 OK`) | Uncensored/abliterated `Qwen3.6-35B-A3B-Heretic` MoE server (`24/40` expert layers in Dual T4 VRAM, `64K` context, thinking enabled) powering `bd-local-coding-agent` (`--backend t4-heretic`). |
| **`.72:5555` & `.82:5555`** | **BulkDownloader Web UI Instances** | Python Web UI (`:5555`) | Monitored by `bd-infra-sentinel` (`72-bulkdownloader`) | `http://10.0.70.72:5555`<br>`http://10.0.70.82:5555` | Internal LAN endpoints | `GET /` (`200 OK`) | Live deployed BulkDownloader web application instances running on `.72` (`ai-srv01`) and `.82` (`wrk-test04`). |

---

## 2. Complete Docker Containers Inventory Across `.164`, `.72`, `.83`, `.125`, `.228`

### 2.1 Controller `.164` (`10.0.70.164`) — 27 Docker Containers

Inspect at any time via:
```bash
docker ps -a --format 'table {{.Names}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}'
```

| # | Container Name | Image | Port Mapping (`Host -> Container`) | Status | Role & Persistence Notes |
|---:|---|---|---|---|---|
| 1 | `open-webui` | `ghcr.io/open-webui/open-webui:main` | `0.0.0.0:8090->8080/tcp` | `Up (healthy)` | Primary AI Workspace & Chat Portal (`:8090`); wired to `:4000`, `:4010`, and `:4079` tool gateway. |
| 2 | `litellm-proxy` | `ghcr.io/berriai/litellm:main-latest` | `0.0.0.0:4000->4000/tcp` | `Up` | Master OpenAI-compatible AI router multiplexing local GPUs and cloud providers. |
| 3 | `dashy-portal` | `lissy93/dashy:latest` | `0.0.0.0:4080->8080/tcp` | `Up (healthy)` | Master Boylenet visual command directory (`:4080`). |
| 4 | `lobechat-studio` | `lobehub/lobe-chat:latest` | `0.0.0.0:4095->3210/tcp` | `Up` | LobeChat multi-agent workspace (`:4095`). |
| 5 | `traefik-ingress` | `traefik:v3.0` | `0.0.0.0:80->80/tcp`, `0.0.0.0:8080->8080/tcp` | `Up` | Dynamic HTTP `:80` ingress router and `:8080` dashboard; reads `/home/mboyle/infra/expansion-layer/dynamic/`. |
| 6 | `forgejo` | `codeberg.org/forgejo/forgejo:latest` | `0.0.0.0:3000->3000/tcp`, `0.0.0.0:2222->22/tcp` | `Up` | Self-hosted Git forge (`gitea.*` / `git.*` on `:3000`). |
| 7 | `grafana-dashboards` | `grafana/grafana-oss:latest` | `0.0.0.0:3001->3001/tcp` (`3000/tcp` internal) | `Up` | Metrics visualization portal (`:3001`). |
| 8 | `prometheus` | `prom/prometheus:latest` | `0.0.0.0:9090->9090/tcp` | `Up` | Time-series telemetry database (`:9090`). |
| 9 | `alertmanager` | `prom/alertmanager:latest` | `0.0.0.0:9093->9093/tcp` | `Up` | Prometheus alert router (`:9093`). |
| 10 | `node-exporter` | `prom/node-exporter:latest` | `0.0.0.0:9100->9100/tcp` | `Up` | Host hardware/OS Prometheus exporter (`:9100`). |
| 11 | `cadvisor-metrics` | `gcr.io/cadvisor/cadvisor:latest` | `0.0.0.0:8085->8080/tcp` | `Up (healthy)` | Container resource telemetry exporter (`:8085`). |
| 12 | `tei-embeddings` | `ghcr.io/huggingface/text-embeddings-inference:cpu-1.5` | `0.0.0.0:8081->80/tcp` | `Up` | HuggingFace CPU Text Embeddings Inference server (`:8081`). |
| 13 | `tei-reranker` | `ghcr.io/huggingface/text-embeddings-inference:cpu-1.5` | `0.0.0.0:8082->80/tcp` | `Up` | HuggingFace CPU Reranker server (`:8082`). |
| 14 | `searxng-search` | `searxng/searxng:latest` | `0.0.0.0:8888->8080/tcp` | `Up` | Private metasearch engine (`:8888`). |
| 15 | `code-server` | `codercom/code-server:latest` | `0.0.0.0:8443->8443/tcp` (`8080/tcp` internal) | `Up` | Browser VS Code IDE (`:8443`). |
| 16 | `minio` | `minio/minio:latest` | `0.0.0.0:9000-9001->9000-9001/tcp` | `Up` | S3 object storage (`:9000`) & web console (`:9001`). |
| 17 | `elasticsearch` | `docker.elastic.co/elasticsearch/elasticsearch:8.13.0` | `0.0.0.0:9200->9200/tcp`, `9300/tcp` | `Up` | Full-text search and log indexing (`:9200`). |
| 18 | `redis-cache` | `redis:7-alpine` | `0.0.0.0:6379->6379/tcp` | `Up` | In-memory key-value cache & LiteLLM/SearXNG state store (`:6379`). |
| 19 | `pgvector-mesh` | `pgvector/pgvector:pg16` | Internal bridge network (`5432/tcp`) | `Up` | PostgreSQL 16 + `pgvector` relational/vector backing store. |
| 20 | `kafka` | `apache/kafka:latest` | `0.0.0.0:9092->9092/tcp` | `Up` | Event streaming broker (`:9092`). |
| 21 | `coredns` | `coredns/coredns:latest` | `127.0.0.1:53->53/tcp`, `127.0.0.1:53->53/udp` | `Up` | Local loopback DNS resolver (`127.0.0.1:53`). |
| 22 | `devdocs-mirror` | `ghcr.io/freecodecamp/devdocs:latest` | `0.0.0.0:8088->9292/tcp` | `Up` | Offline developer documentation mirror (`:8088`). |
| 23 | `devpi-server` | `jonasal/devpi-server:latest` | `0.0.0.0:3141->3141/tcp` | `Up` | Local PyPI wheel cache (`:3141`). |
| 24 | `verdaccio` | `verdaccio/verdaccio:latest` | `0.0.0.0:4873->4873/tcp` | `Up` | Local NPM package registry (`:4873`). |
| 25 | `docker-mirror` | `registry:2` | `0.0.0.0:5000->5000/tcp` | `Up` | Local Docker Registry v2 mirror (`:5000`). |
| 26 | `apt-cacher-ng` | `sameersbn/apt-cacher-ng:latest` | `0.0.0.0:3142->3142/tcp` | `Up (healthy)` | Local Debian/Ubuntu APT package cache (`:3142`). |
| 27 | `squid-cache` | `ubuntu/squid:latest` | `0.0.0.0:3128->3128/tcp` | `Up` | Forward HTTP caching proxy (`:3128`). |

### 2.2 Remote GPU & Satellite Nodes Docker Containers (`.72`, `.83`, `.125`, `.228`)

| Node | Hostname | Container Name | Image | Ports | Status | Role |
|---|---|---|---|---|---|---|
| **`10.0.70.72`** | `ai-srv01` (AI Vault A) | `open-webui` | `ghcr.io/open-webui/open-webui:main` | Host `:8080` | `Up (healthy)` | AI Vault A OpenWebUI satellite (`https://ai-webui.boylenet.themfboyles.org`). |
| **`10.0.70.72`** | `ai-srv01` (AI Vault A) | `tei-reranker` | `ghcr.io/huggingface/text-embeddings-inference:turing-1.5` | Standby | `Created` | Standby Turing GPU reranker container (VRAM yielded to `llama-tier1-alpha.service`). |
| **`10.0.70.83`** | `aidev` (AI Vault B) | `openhands-app`, `oh-agent-server-*`, `bd-vector-db` | `openhands:1.8`, `pgvector:pg16` | Parked | `Exited` | Historical OpenHands containers parked so 100% of Dual T4 VRAM serves `llama-heretic-18080.service` & `llama-rpc.service`. |
| **`10.0.70.125`** | `ai-infer01` | *(No active Docker containers)* | N/A | N/A | N/A | All `.125` services (`qdrant-portal.service` `:6333`, `llama-distributed-32b.service` `:8081`, `llama-rpc.service` `:50052`, `ollama.service` `:11434`) run as native systemd units for zero container overhead. |
| **`10.0.70.228`** | `ai-ollama01` | `litellm-proxy` | `ghcr.io/berriai/litellm:main-latest` | `0.0.0.0:4000->4000/tcp` | `Up` | Satellite LiteLLM router on `.228:4000` (`https://litellm-228.boylenet.themfboyles.org`). |
| **`10.0.70.228`** | `ai-ollama01` | `langfuse` | `langfuse/langfuse:2` | `0.0.0.0:3000->3000/tcp` | `Up` | Langfuse LLM tracing & observability UI on `.228:3000` (`https://langfuse.boylenet.themfboyles.org`). |

---

## 3. Complete Systemd Daemons & Timers Bible

### 3.1 Controller `.164` System-Level Units (`/etc/systemd/system/`)

Manage with `sudo systemctl status|restart|stop|start <unit>` and `journalctl -u <unit> -n 100 --no-pager`.

| Unit Name | Type / State | Exact `ExecStart` Command | Port / Cadence | Purpose & Operational Details |
|---|---|---|---|---|
| `bd-autonomous-swarm-driver.service` | `simple` (`active/running`) | `/usr/bin/python3 /home/mboyle/bin/bd-autonomous-swarm-driver.py --daemon` | Continuous daemon | 24/7 Continuous Autonomous Multi-Platform Swarm Driver orchestrating work across Claude Pools A–D, Codex, Kimi, OpenWebUI, and Local Heretic. |
| `bd-boot-recovery.service` | `oneshot` (`RemainAfterExit`) | `/usr/bin/python3 /home/mboyle/bin/bd-boot-recovery.py` | At boot (`After=docker.service`) | Post-reboot fleet, AI VM, and remote-control session auto-recovery orchestrator. |
| `bd-chrome-bridge.service` | `simple` (`active/running`) | `/usr/bin/python3 /home/mboyle/bin/bd-chrome-bridge.py` | `:9222` | Local Chrome CDP proxy bridge forwarding `:9222` on `.164` to `.181:9222` and `.137:9222`. |
| `bd-cloud-burst-web.service` | `simple` (`active/running`) | `/usr/bin/python3 /home/mboyle/bin/bd-cloud-burst-web.py` | `:4090` | Hybrid Cloud + Local Burst Orchestrator Web UI (`burst.*` / `quota.*` / `cloud.*`). |
| `bd-credentials-vault.service` | `simple` (`active/running`) | `/usr/bin/python3 /home/mboyle/bin/bd-credentials-vault.py` | `:4087` | Unified Credentials, API Key & 27-Node SSH Health Vault UI. |
| `bd-deep-research-web.service` | `simple` (`active/running`) | `/usr/bin/python3 /home/mboyle/bin/bd-deep-research-web.py` | `:4094` | Deep Research & Dual-Oracle Citation Studio Web UI. |
| `bd-executive-briefing.service` | `simple` (`active/running`) | `/usr/bin/python3 /home/mboyle/bin/bd-executive-briefing.py` | `:4081` | Morning Executive Briefing, Universal Semantic Search & 1-Click Cluster Doctor (`briefing.*` / `doctor.*` / `search.*`). |
| `bd-gitea-prereview.service` | `simple` (`active/running`) | `/usr/bin/python3 /home/mboyle/bin/bd-gitea-prereview.py` | `:4092` | Gitea Autonomous Pre-Review & OffSec Red-Team Swarm UI. |
| `bd-gpu-orchestrator.service` | `simple` (`active/running`) | `/usr/bin/python3 /usr/local/bin/bd-gpu-orchestrator.py` | `:4010` | Dynamic Tier 1/2/3 GPU Mode Orchestrator & Smart OpenAI Auto-Router (`gpu.*` / `orchestrator.*`). |
| `bd-hardware-telemetry.service` | `simple` (`active/running`) | `/usr/bin/python3 /home/mboyle/bin/bd-hardware-telemetry.py` | `:4091` (`60s` poll) | Physical 12U Rack, HPE iLO4/iLO5, vSphere `vc04`, vSAN & 4x T4 Telemetry Autopilot (`hardware.*`). |
| `bd-infra-sentinel.service` | `simple` (`active/running`) | `/usr/bin/python3 /home/mboyle/bin/bd-infra-sentinel.py` | `:4099` (`60s` sweep) | Autonomous 83-Service Self-Healing Infrastructure Sentinel (`sentinel.*`). |
| `bd-iptables-restore.service` | `oneshot` (`active/exited`) | `/sbin/iptables-restore -n /etc/iptables/rules.v4` | At boot | Restores hub iptables firewall and NAT rules after `docker.service` starts. |
| `bd-kb-live-sync.service` + `.timer` | `oneshot` + `timer` (`active/waiting`) | `/usr/bin/python3 /home/mboyle/bin/bd-kb-live-sync.py` | Every `15min` (`OnUnitActiveSec=15min`) | Auto-indexes `AGENT*.md`, `PM-HANDOFF.md`, `WATCHDOG-STATUS.md`, and `/home/mboyle/bd-persist/bd-kb/*.md` into BD KB and Qdrant `bd_master_kb`. |
| `bd-mcp.service` | `simple` (`active/running`) | `/home/mboyle/bd-persist/harness/bd-mcp/venv/bin/python /home/mboyle/bd-persist/harness/bd-mcp/server.py --http` | `:8100` | Shared `bd-mcp` streamable-HTTP server hosting fleet state, queue claims, and gate evidence. |
| `bd-memory-dreamer.service` + `.timer` | `oneshot` + `timer` (`active/waiting`) | `/home/mboyle/bin/bd-memory-dreamer --consolidate` | Every `6h` (`00/6:00:00`) | Cross-Harness Episodic Memory Consolidator (Dreamer Swarm #11) distilling session transcripts into Qdrant `boylenet_episodic_memory`. |
| `bd-mesh-bus.service` | `simple` (`active`) | `/usr/bin/python3 /home/mboyle/bin/bd-mesh-bus.py --daemon --port 3004` | `:3004` | Cross-Pool Mesh Bus & WAL HTTP/SSE daemon (paired with user unit `bd-mesh-bus.service`). |
| `bd-mobile-pwa.service` | `simple` (`active/running`) | `/usr/bin/python3 /home/mboyle/bin/bd-mobile-pwa.py --port 4086` | `:4086` | Mobile & Tablet PWA Command Deck. |
| `bd-model-storefront.service` | `simple` (`active/running`) | `/usr/bin/python3 /home/mboyle/bin/bd-model-storefront.py` | `:4084` | 86-Model AI Vault Storefront (`models.*` / `vault.*`). |
| `bd-multi-harness-web.service` | `simple` (`active/running`) | `/usr/bin/python3 /home/mboyle/bin/bd-multi-harness-web.py` | `:4098` | Multi-Harness AI Studio & Arena Web UI (`harnesses.*` / `arena.*`). |
| `bd-multimodal-bridge.service` | `simple` (`active/running`) | `/home/mboyle/BulkDownloader/venv/bin/python3 /home/mboyle/bin/bd-multimodal-bridge.py` | `:4093` | Unified Voice, Vision & Multimodal Cockpit Bridge (`voice.*` / `media.*`). |
| `bd-novnc.service` | `simple` (`active/running`) | `/usr/bin/websockify --web=/usr/share/novnc 6080 127.0.0.1:5900` | `:6080` | BulkDownloader noVNC HTML5 web frontend (`novnc.*`). |
| `bd-openbox.service` | `simple` (`active/running`) | `/usr/bin/openbox` (`DISPLAY=:99`) | `:99` | Openbox window manager on virtual display `:99`. |
| `bd-openclaw-web.service` | `simple` (`active/running`) | `/home/mboyle/.local/bin/openclaw --dev gateway run --allow-unconfigured --bind lan --auth password --password boylenet-admin --port 4097 --force` | `:4097` | OpenClaw Control UI & WebSocket Gateway (`openclaw.*`). |
| `bd-opencode-web.service` | `simple` (`active/running`) | `/home/mboyle/.local/bin/opencode serve --hostname 0.0.0.0 --port 4096 ...` | `:4096` | OpenCode Web UI & ACP Server (`opencode.*`). |
| `bd-openwebui-tool-gateway.service` | `simple` (`active/running`) | `/usr/bin/python3 /home/mboyle/bin/bd-openwebui-tool-gateway.py` | `:4079` | Universal OpenWebUI Tool, Skill, Plugin, Git, Web, Browser & MCP Gateway (`gateway.*` / `tools.*`). |
| `bd-ops-web.service` | `simple` (`active/running`) | `/usr/bin/python3 /home/mboyle/bin/bd-ops-web.py --serve --port 4089` | `:4089` | Natural-Language Ops Shell & Verified Playbook Runner (`ops.*`). |
| `bd-os-shell.service` | `simple` (`active/running`) | `/usr/bin/python3 /home/mboyle/bin/bd-os-shell.py --port 4085` | `:4085` | Boylenet Unified OS Single-Window Desktop Shell (`os.*`). |
| `bd-pool-autobalancer.service` | `simple` (`active/running`) | `/usr/bin/python3 /home/mboyle/bin/bd-pool-autobalancer --daemon --interval 30` | Every `30s` | Autonomous Cross-Pool Quota Load-Balancer & Failover Daemon (`O1740` / `O1789`). |
| `bd-pool-usage-refresher.service` + `.timer` | `oneshot` + `timer` (`active/waiting`) | `/usr/bin/python3 /home/mboyle/bin/bd-pool-usage-refresher.py` | Every `5min` (`OnCalendar=*:0/5`) | Polls provider quotas across Claude Pools A–D, Codex, AGY, Kimi, Local, and Grok; updates `/home/mboyle/bd-persist/POOL_STATE.tsv` and logs to `/home/mboyle/bd-persist/logs/pool-usage-refresher.log`. |
| `bd-rag.service` | `simple` (`active/running`) | `/home/mboyle/bd-persist/harness/bd-rag/venv/bin/python /home/mboyle/bd-persist/harness/bd-rag/server2.py` | `:8099` | `bd-rag` query endpoint for transcript and repository retrieval. |
| `bd-rc-enforcer.service` + `.timer` | `oneshot` + `timer` (`inactive/dead` standby) | `/usr/bin/python3 /home/mboyle/bin/bd-rc-enforcer.py` | `2min` (when enabled) | 1-Year Token Seat Auth Enforcer (`O3349`/`O3350`, No-RC Mode). |
| `bd-relief-puppeteer.service` + `.timer` | `oneshot` + `timer` (`inactive/dead` standby) | `/usr/bin/python3 /home/mboyle/bin/bd-relief-puppeteer.py` | `OnCalendar=*:0/5` (when enabled) | Autonomous Relief Agent Puppeteer & Supervisor Pass (`bd-relief-puppeteer.timer`). |
| `bd-serialized-opt-pipeline.service` | `simple` (`active/running`) | `/usr/bin/python3 /home/mboyle/bin/bd-serialized-opt-pipeline.py --daemon --interval 20` | Every `20s` | Master Serialized Inference Optimization Pipeline Orchestrator (Stages 0–5). |
| `bd-topology-visualizer.service` | `simple` (`active/running`) | `/usr/bin/python3 /home/mboyle/bin/bd-topology-visualizer.py` | `:4088` | Live 3D/2D Architecture Topology & HiveMind Swarm Visualizer (`topology.*` / `hivemind.*`; also referenced as `bd-hivemind-visualizer`). |
| `bd-trust-and-sso-portal.service` | `simple` (`active/running`) | `/usr/bin/python3 /home/mboyle/bin/bd-trust-and-sso-portal.py` | `:4082` | Zero-Friction SSO, QR Handoff & Root CA Trust Portal (`ca.*` / `sso.*` / `trust.*`). |
| `bd-web-terminal-drawer.service` | `simple` (`active/running`) | `/usr/bin/python3 /home/mboyle/bin/bd-web-terminal-drawer.py` | `:4083` | Browser Multi-Node Terminal, Live Agent TV Wall & Receipt Explorer (`terminal.*` / `tv.*`). |
| `bd-x11vnc.service` | `simple` (`active/running`) | `/usr/bin/x11vnc -display :99 -rfbauth /home/mboyle/.bd_vnc_passwd -localhost -forever -shared -rfbport 5900 -quiet` | `127.0.0.1:5900` | VNC server exporting virtual X11 display `:99` to `bd-novnc.service`. |
| `bd-xvfb.service` | `simple` (`active/running`) | `/usr/bin/Xvfb :99 -screen 0 1920x1080x24 -nolisten tcp -ac` | Display `:99` | Virtual framebuffer X11 server (`1920x1080x24`) on `:99`. |
| `ollama.service` | `simple` (`active/running`) | `/usr/local/bin/ollama serve` | `:11434` | Local Ollama model runner service on `.164`. |

---

### 3.2 Controller `.164` User-Level Units (`/home/mboyle/.config/systemd/user/`)

Manage with `systemctl --user status|restart|stop|start <unit>` and `journalctl --user -u <unit> -n 100 --no-pager`.

| Unit Name | Type / Cadence | Exact `ExecStart` Command | Purpose & Operational Details |
|---|---|---|---|
| `bd-cockpit.service` (alias `bd-cockpit-ui.service`) | `simple` (`:3002`, `active/running`) | `/usr/bin/node /home/mboyle/UniversalSwarmOS/cockpit/node_modules/next/dist/bin/next start -p ${PORT} -H ${HOSTNAME}` | Next.js UniversalSwarmOS Cockpit UI on port `:3002` (`jarvis.*` / `cockpit.*`); reads `~/.config/systemd/user/bd-cockpit.env`. |
| `bd-chat.service` (alias `bd-chat-ui.service`) | `simple` (`:3003`, `active/running`) | `/usr/bin/node /var/tmp/bd-chat-server.js` | Fleet Multi-Platform Chat UI on port `:3003`. |
| `bd-telemetry-daemon.service` | `simple` (`60s` loop, `active/running`) | `/usr/bin/python3 /home/mboyle/bd-persist/harness/bd-telemetry-daemon.py --interval 60` | Rule 81 Zero-Token Deterministic Fleet Telemetry & Heartbeat Daemon; writes `STATUS.txt` and `fleet_telemetry_live.json` with sensor intervals `bd_cost_accounting=900,vram=300`. |
| `bd-swarm-telemetry.service` + `.timer` | `oneshot` + `timer` (`minutely`) | `/usr/bin/python3 -I -S -B %h/bd-persist/harness/bd-swarm-telemetry.py` | Samples I/O pressure, pool pace, and active seats once per minute (`Nice=15`, `CPUQuota=20%`, `MemoryMax=64M`). |
| `bd-hub-sampler.service` | `simple` (`10s` loop, `active/running`) | `/bin/bash /home/mboyle/bd-persist/harness/bd-hub-sampler.sh` | Records HUB-GATE load, blocked processes, iowait delta, and memory every `10s` + hourly `p50`/`p95`/`p99` rollup (`O1698 P0b`). |
| `bd-inbox-watcher.service` | `simple` (`active/running`) | `/usr/bin/python3 -u /home/mboyle/bin/bd-inbox-watcher.py --daemon` | Event-driven `inotify` reactive wakeup daemon monitoring agent inboxes. |
| `bd-realtime-stall-sentinel.service` | `simple` (`active/running`) | `/usr/bin/python3 -u /home/mboyle/bin/bd-realtime-stall-sentinel.py --daemon` | Realtime event-driven stall and deadlock detector across active swarm seats. |
| `bd-self-healing.service` | `simple` (`active/running`) | `/usr/bin/python3 /home/mboyle/bd-persist/harness/bd-self-heal-daemon.py --daemon` | Autonomous self-healing CI/CD pipeline daemon backed by `/home/mboyle/bd-persist/flakes/ledger.sqlite` and LiteLLM `:4000`. |
| `bd-cache-keepalive.service` | `simple` (`active/running`) | `/home/mboyle/bin/bd-cache-keepalive start --foreground` | Active Ephemeral TTL Keepalive Heartbeat Daemon (`R3`) preserving prompt-cache prefixes via `:4000`. |
| `bd-kv-warmer.service` + `.timer` | `oneshot` + `timer` (every `10min`) | `/usr/bin/python3 /home/mboyle/bin/bd-kv-warmer --pin-only` | Boylenet Prompt-Cache KV-Prefix Pinning Daemon keeping warm prefixes resident. |
| `bd-hot-pin.service` + `.timer` | `oneshot` + `timer` (every `5min`) | `/home/mboyle/bd-persist/harness/bd-hot-pin.sh` | Pins hot fleet state files into Linux page cache via `vmtouch -t` (`O1698 C6`). |
| `bd-batch-relay.service` | `simple` (`active/running`) | `/home/mboyle/bin/bd-batch-relay start --batch-size 10 --flush-interval 300` | Batched relay daemon (`O586` / `O1321-flush120.conf`) logging to `/home/mboyle/bd-persist/relay.log`. |
| `bd-ellm-canary.service` | `simple` | `/home/mboyle/bin/bd-ellm-canary.py` | Ephemeral Lens Lifecycle Manager (`ELLM`) canary monitor. |
| `bd-auth-sync.service` + `.timer` | `oneshot` + `timer` (every `15min`) | `/home/mboyle/bin/bd-auth-sync` | Tier-1 Hardened Out-of-VCS Credential Sync (`O1789`). |
| `bd-cplus-authorizer.service` | `simple` (drop-in `50-cplus-hub-release.conf`) | Configured via `bd-cplus-authorizer.service.d/50-cplus-hub-release.conf` | C+ v2 hub spawn authorizer (`O1789`). |
| `bd-cplus-reaper.service` + `.timer` | `oneshot` + `timer` (every `30s`) | Configured via `bd-cplus-reaper.service.d/50-cplus-hub-release.conf` | C+ v2 hub lease reaper pass every `30s` (`TTL/4`, `O1789`). |
| `bd-cplus-kill.service` | `oneshot` (on demand) | Configured via `bd-cplus-kill.service.d/50-cplus-hub-release.conf` | Emergency C+ v2 fleet-wide kill switch (manual invocation only). |
| `bd-cplus-token-push.service` + `.timer` | `oneshot` + `timer` (every `5min`) | `/bin/bash /home/mboyle/bd-persist/harness-work/O1789/token-push/hub-token-push.sh` | Refreshes hub OAuth tokens and pushes `refreshToken`-stripped credentials to 17 C+ worker VMs (`O3050`). |
| `bd-checkpoint.service` + `.timer` | `oneshot` + `timer` (`OnCalendar=*:0/10`) | `/bin/bash /home/mboyle/bd-checkpoint-write` | Appends measured continuity checkpoint blocks every `10min`. |
| `bd-persist.service` + `.timer` | `oneshot` + `timer` (`OnCalendar=*:0/30`) | `/bin/bash /home/mboyle/bd-persist-loop.sh --once` | Captures harness, tools, and patches into `bd-persist` and verifies integrity every `30min`. |
| `bd-fleet-watch.service` + `.timer` | `oneshot` + `timer` (`OnCalendar=*:0/5`) | `/bin/bash /home/mboyle/bd-persist/harness/bd-fleet-watch.sh` | Emits fleet liveness advisory digest every `5min`. |
| `bd-reap.service` + `.timer` | `oneshot` + `timer` (every `5min`) | `/usr/bin/python3 /home/mboyle/bd-persist/plugins/bd-dispatch-ops/scripts/operations.py reap` | Reaps dead fleet role claims when `/home/mboyle/bd-persist/state/FLEET-HALT` is absent. |
| `bd-pm-idle-watch.service` + `.timer` | `oneshot` + `timer` (every `15min`) | `/home/mboyle/.local/bin/bd-pm-idle-watch` | Sweeps idle sessions (`45min` threshold, `O3387`). |
| `bd-pm-stall-watch.service` + `.timer` | `oneshot` + `timer` (every `5min`) | `/home/mboyle/.local/bin/bd-pm-stall-watch` | Monitors every tmux pane across all fleet hosts for stalled agents. |
| `bd-ugrep-reaper.service` + `.timer` | `oneshot` + `timer` (every `5min`) | `/usr/bin/nice -n 19 /usr/bin/ionice -c3 /bin/bash /home/mboyle/bd-persist/harness/bd-ugrep-reaper.sh --report` | Audits runaway `ugrep` processes and logs to `/home/mboyle/bd-persist/logs/ugrep-reaper.cron.log`. |
| `bd-usage-oneshot.service` + `.timer` | `oneshot` + `timer` (`OnCalendar=*:07/10:30`) | `/bin/bash /home/mboyle/bd-usage-probe.sh` (`BD_USAGE_MODE=oneshot`) | Non-interactive one-shot `/usage` probe across Claude pools every `10min` (`O3387`). |
| `bd-usage-probe.service` + `.timer` | `oneshot` + `timer` (`OnCalendar=hourly`) | `/bin/bash /home/mboyle/bd-usage-probe.sh` | Hourly provider quota probe logging to `/home/mboyle/bd-persist/logs/usage-probe.log`. |

---

### 3.3 Remote GPU, RAG & Browser Oracle Nodes Systemd Services (`.72`, `.83`, `.125`, `.228`, `.82`, `.137`, `.181`)

| Node & Hostname | Systemd Unit Path / Process | State | Port / Bind Address | Exact `ExecStart` & Role |
|---|---|---|---|---|
| **`.72` (`ai-srv01`)**<br>`10.0.70.72`<br>*(AI Vault A, Tesla T4 + 25GbE `10.0.75.12`)* | `/etc/systemd/system/llama-tier1-alpha.service` | `active/running` | `0.0.0.0:8082` | `/opt/llama-rpc/llama-server -m /opt/models/cyberstrike-35b --alias cyberstrike-35b -ngl 99 -ot ".ffn_.*_exps.=CPU" -c 262144 -np 4 -kvu --reasoning-preserve -fa on -ctk q8_0 -ctv q8_0 --jinja -t 48 --host 0.0.0.0 --port 8082`<br>Tier-1 Pool Alpha `Huihui-CyberStrike-OffSec-35B-Abliterated` (`262K` context) OpenAI server. |
| **`.72` (`ai-srv01`)** | `/etc/systemd/system/llama-rpc.service` (alias `llama-rpc-worker.service`) | `active/running` | `10.0.75.12:50052` | `/opt/llama-rpc/rpc-server -H 10.0.75.12 -p 50052 -c`<br>GGML CUDA RPC worker over dedicated `25GbE` RoCE (`10.0.75.0/24`). |
| **`.72` (`ai-srv01`)** | `/etc/systemd/system/llama-tier23.service` | `disabled` (Tier-2/3 mode) | `0.0.0.0:8083` | `/opt/llama-rpc/llama-server ... --alias deepseek-v3-0324-671b --rpc 10.0.75.11:50052,10.0.75.13:50052,10.0.75.14:50052 ... --port 8083`<br>Activated by `bd-gpu-orchestrator` (`:4010`) when switching to Tier-3 distributed 4-node RPC mode. |
| **`.72` (`ai-srv01`)** | `/etc/systemd/system/llama-qwythos-18081.service` | `disabled` (1M ctx mode) | `0.0.0.0:18081` | `/opt/llama-rpc/llama-server -m /opt/models/qwythos-9b/Qwythos-9B-Claude-Mythos-5-1M-MTP-Q8_0.gguf --alias qwythos-9b-1m --host 0.0.0.0 --port 18081 -c 1048576 ...`<br>1M-token context CPU/RAM server on `:18081`. |
| **`.72` (`ai-srv01`)** | `/etc/systemd/system/ollama.service` | `active/running` | `*:11434` & `:8081` | `/usr/local/bin/ollama serve` — AI Vault A Ollama model server. |
| **`.72` (`ai-srv01`)** | `/etc/systemd/system/bd-novnc.service`, `bd-openbox.service`, `bd-x11vnc.service`, `bd-xvfb.service` | `active/running` | `:6080` (`127.0.0.1:5900`, `:99`) | Headless X11 virtual desktop + noVNC HTML5 gateway on `.72:6080`. |
| **`.83` (`aidev`)**<br>`10.0.70.83`<br>*(AI Vault B, Tesla T4 + 25GbE `10.0.75.11`)* | `/etc/systemd/system/llama-heretic-18080.service` | `active/running` | `0.0.0.0:18080` | `/opt/llama-rpc/run-heretic-18080.sh`<br>Flagship `Qwen3.6-35B-A3B-Heretic` (`24/40` MoE expert layers in VRAM, `64K` context, thinking mode) on `.83:18080`. Primary `$0` local coding agent backend. |
| **`.83` (`aidev`)** | `/etc/systemd/system/llama-rpc.service` (alias `llama-rpc-worker.service`) | `active/running` | `10.0.75.11:50052` | `/opt/llama-rpc/rpc-server -H 10.0.75.11 -p 50052 -c` (`GGML_OP_OFFLOAD_MIN_BATCH=2048`)<br>GGML CUDA RPC worker on `10.0.75.11:50052`. |
| **`.83` (`aidev`)** | `/etc/systemd/system/ollama.service` | `inactive` (VRAM yielded) | `:11434` | `/usr/local/bin/ollama serve` (stopped so `llama-heretic-18080` holds dedicated T4 VRAM). |
| **`.125` (`ai-infer01`)**<br>`10.0.70.125`<br>*(Tesla T4 + 25GbE `10.0.75.14`)* | `/etc/systemd/system/llama-distributed-32b.service` (alias `llama-tier1-beta.service`) | `active/running` | `0.0.0.0:8081` | `/opt/llama-rpc/llama-server -m .../sha256-ac9bc7a69dab38da1c790838955f1293420b55ab555ef6b4615efa1c1507b1ed --alias qwen2.5-coder-32b-distributed -ngl 999 --parallel 4 -cb -ctk q8_0 -ctv q8_0 -c 32768 --jinja -fa on -t 24 --host 0.0.0.0 --port 8081`<br>100% local Tesla T4 (`Zero RPC`) `Qwen2.5-Coder-14B` fast coding server on `.125:8081`. |
| **`.125` (`ai-infer01`)** | `/etc/systemd/system/llama-cyberstrike-35b.service` (`llama-tier1-beta-cyberstrike.service`) | `disabled` (alt Tier-1 Beta) | `0.0.0.0:8084` | `/opt/llama-rpc/llama-server -m /opt/models/cyberstrike-35b/Huihui-CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf ... --rpc 10.0.75.13:50052 ... --port 8084` |
| **`.125` (`ai-infer01`)** | `/etc/systemd/system/llama-rpc.service` (alias `llama-rpc-worker.service`) | `active/running` | `10.0.75.14:50052` | `/opt/llama-rpc/rpc-server -H 10.0.75.14 -p 50052 -c`<br>GGML CUDA RPC worker on `10.0.75.14:50052`. |
| **`.125` (`ai-infer01`)** | `/etc/systemd/system/qdrant-portal.service` | `active/running` | `0.0.0.0:6333` | `/usr/bin/python3 /usr/local/bin/qdrant_portal.py`<br>Qdrant Vector Database REST API, Web UI, and `/healthz` endpoint (`qdrant.boylenet.themfboyles.org`). |
| **`.125` (`ai-infer01`)** | `/etc/systemd/system/ollama.service` | `active/running` | `*:11434` & `*:8005` | `/usr/local/bin/ollama serve` — Ollama service on `.125` (with HTTP bridge on `:8005`). |
| **`.228` (`ai-ollama01`)**<br>`10.0.70.228`<br>*(Tesla T4 + 25GbE `10.0.75.13`)* | `/etc/systemd/system/llama-rpc.service` (alias `llama-rpc-worker.service`) | `active/running` | `10.0.75.13:50052` | `/opt/llama-rpc/rpc-server -H 10.0.75.13 -p 50052 -c`<br>GGML CUDA RPC worker on `10.0.75.13:50052`. |
| **`.228` (`ai-ollama01`)** | `/etc/systemd/system/ollama.service` | `active/running` | `*:11434` | `/usr/local/bin/ollama serve` — Primary satellite Ollama server (`https://ollama.boylenet.themfboyles.org`). |
| **`.82` (`wrk-test04`)**<br>`10.0.70.82`<br>*(64-Core / 96 GB RAM Ramdisk Worker)* | `/etc/systemd/system/bd-novnc.service`, `bd-openbox.service`, `bd-x11vnc.service`, `bd-xvfb.service` | `active/running` | `:6080` (`127.0.0.1:5900`, `:99`) | Headless GUI & noVNC stack on `.82:6080`, plus `/dev/shm/bd-ramdisk-pytest` 64-core tmpfs test runner and BulkDownloader Web UI on `.82:5555`. |
| **`.137` (`BattleStation`)**<br>`10.0.10.137`<br>*(Ryzen 9 + RTX 2080 Ti 11GB)* | Windows Chrome CDP + Ollama Service | `active/running` | `:9222` (CDP)<br>`:11434` (Ollama) | Chrome `155.0` CDP Oracle (`http://10.0.10.137:9222/json/version`, Rule 79) + RTX 2080 Ti Ollama (`http://10.0.10.137:11434/api/tags`, Rule 84 Hardware Segregation: coder models on 2080 Ti, embeddings on Ryzen 9 CPU). |
| **`.181` (`Bittorrent`)**<br>`10.0.70.181` | Windows Chrome CDP Oracle | `active/running` | `:9222` (CDP) | Chrome `155.0` Secondary Browser Oracle (`http://10.0.70.181:9222/json/version`) for multi-model fanout (`bd-oracle`) and E2E portal rendering verification. |

---

## 4. NGINX `:443` HTTPS VHosts & Traefik `:80` Ingress Routing Architecture

Boylenet uses a clean dual-ingress architecture on Controller `.164` (`10.0.70.164`):
1. **NGINX (`:443` HTTPS / HTTP2)**: Configured in `/etc/nginx/sites-available/boylenet-https.conf` and `/etc/nginx/conf.d/boylenet-extra-vhosts.conf` using `/etc/nginx/snippets/boylenet-tls.conf` and `/etc/nginx/snippets/boylenet-proxy.conf`. Serves wildcard TLS for `*.boylenet.themfboyles.org`, `*.themfboyles.org`, `*.boylenet.themfboyles.com`, and `*.themfboyles.com`.
2. **Traefik v3.0 (`:80` HTTP & `:8080` Dashboard)**: Runs in Docker container `traefik-ingress`, loading dynamic route definitions from `/home/mboyle/infra/expansion-layer/dynamic/boylenet-dns-ingress.yml`, `boylenet-sso-ca.yml`, and `routes.yml`.

### 4.1 Direct NGINX `:443` HTTPS VHost Mapping (`/etc/nginx/sites-available/boylenet-https.conf`)

| VHost Block # | Subdomain Prefixes (`*.boylenet.themfboyles.org` & `.com`) | NGINX `:443` Upstream `proxy_pass` | Target Service |
|---:|---|---|---|
| **Default (`_`)** | `10.0.70.164`, `boylenet.themfboyles.org`, `themfboyles.org` | `http://127.0.0.1:4085` | Unified OS Single-Window Desktop Shell (`bd-os-shell.service`) |
| **1** | `os.*` | `http://127.0.0.1:4085` | Unified OS Single-Window Desktop Shell (`:4085`) |
| **2** | `topology.*`, `hivemind.*` | `http://127.0.0.1:4088` | 3D/2D Architecture Topology & HiveMind Visualizer (`:4088`) |
| **3** | `models.*`, `vault.*` | `http://127.0.0.1:4084` | 86-Model AI Vault Storefront (`:4084`) |
| **4** | `terminal.*`, `tv.*` | `http://127.0.0.1:4083` | Browser Multi-Node Terminal & Live Agent TV Wall (`:4083`) |
| **5** | `ca.*`, `sso.*`, `trust.*` | `http://127.0.0.1:4082` | Zero-Friction SSO, QR Handoff & Root CA Trust Portal (`:4082`) |
| **6** | `briefing.*`, `doctor.*`, `search.*` | `http://127.0.0.1:4081` | Morning Executive Briefing, Semantic Search & Cluster Doctor (`:4081`) |
| **7** | `openwebui.*`, `chat.*`, `ai.*` | `http://127.0.0.1:8090` | OpenWebUI Master AI Portal (`:8090`) |
| **8** | `ai-webui.*` | `http://10.0.70.72:8080` | OpenWebUI AI Vault A Satellite Portal (`.72:8080`) |
| **9** | `gateway.*`, `tools.*` | `http://127.0.0.1:4079` | OpenWebUI Universal Tool Gateway (`:4079`) |
| **10** | `harnesses.*`, `arena.*` | `http://127.0.0.1:4098` | Multi-Harness AI Studio Web UI (`:4098`) |
| **11** | `burst.*`, `quota.*`, `cloud.*` | `http://127.0.0.1:4090` | Hybrid Cloud + Local Burst Orchestrator (`:4090`) |
| **12** | `hardware.*`, `research.*` | `http://127.0.0.1:4091` | Hardware Telemetry Autopilot (`:4091`) |
| **13** | `offsec.*`, `security.*` | `http://127.0.0.1:4092` | Gitea Autonomous Pre-Review & OffSec Swarm (`:4092`) |
| **14** | `voice.*`, `media.*` | `http://127.0.0.1:4093` | Unified Voice, Vision & Multimodal Bridge (`:4093`) |
| **15** | `pwa.*`, `mobile.*` | `http://127.0.0.1:4094` | Deep Research & Citation Studio (`:4094`) *(see §4.3)* |
| **16** | `lobechat.*` | `http://127.0.0.1:4095` | LobeChat Studio (`:4095`) |
| **17** | `opencode.*` | `http://127.0.0.1:4096` | OpenCode Web UI & ACP Server (`:4096`) |
| **18** | `openclaw.*` | `http://127.0.0.1:4097` | OpenClaw Control UI & Gateway (`:4097`) |
| **19** | `sentinel.*` | `http://127.0.0.1:4099` | 83-Service Self-Healing Sentinel (`:4099`) |
| **20** | `dashy.*` | `http://127.0.0.1:4080` | Dashy Master Command Portal (`:4080`) |
| **21** | `gitea.*`, `git.*` | `http://127.0.0.1:3000` | Forgejo / Gitea Git Forge (`:3000`) |
| **22** | `jarvis.*` | `http://127.0.0.1:3002` | UniversalSwarmOS Next.js Cockpit (`:3002`) |
| **23** | `litellm.*` | `http://127.0.0.1:4000` | LiteLLM Master AI Proxy (`:4000`) |
| **24** | `gpu.*`, `orchestrator.*` | `http://127.0.0.1:4010` | Dynamic GPU Mode Orchestrator (`:4010`) |
| **25** | `dreamer.*`, `memory.*` | `http://127.0.0.1:4086` | Mobile PWA / Memory Deck (`:4086`) *(see §4.3)* |
| **26** | `prereview.*` | `http://127.0.0.1:4087` | Credentials Vault (`:4087`) *(see §4.3)* |
| **27** | `credentials.*`, `keys.*`, `ops.*` | `http://127.0.0.1:4089` | Natural-Language Ops Shell (`:4089`) |
| **28** | `searxng.*` | `http://127.0.0.1:8888` | SearXNG Metasearch (`:8888`) |
| **29** | `novnc.*` | `http://127.0.0.1:6080` | noVNC HTML5 Desktop (`:6080`) |
| **30a** | `grafana.*` | `http://127.0.0.1:3001` | Grafana Dashboards (`:3001`) |
| **30b** | `minio.*` | `http://127.0.0.1:9001` | MinIO Object Storage Console (`:9001`) |
| **30c** | `codeserver.*` | `http://127.0.0.1:8443` | Code-Server Browser IDE (`:8443`) |
| **30d** | `litellm-228.*` | `http://10.0.70.228:4000` | Satellite LiteLLM Proxy on `.228:4000` |
| **30e** | `langfuse.*` | `http://10.0.70.228:3000` | Langfuse Observability on `.228:3000` |
| **30f** | `qdrant.*` | `http://10.0.70.125:6333` | Qdrant Vector DB on `.125:6333` |

### 4.2 NGINX `:443` -> Traefik `:80` Parity Subdomains (`/etc/nginx/conf.d/boylenet-extra-vhosts.conf`)

The following HTTPS `:443` subdomains terminate TLS at NGINX and proxy to `http://127.0.0.1:80` (Traefik `web` entrypoint), which routes them via `/home/mboyle/infra/expansion-layer/dynamic/boylenet-dns-ingress.yml` and `routes.yml`:

- `portal.*`, `dashboard.*`, `controller.*` -> `boylenet-dashy-svc` (`http://10.0.70.164:4080`)
- `cockpit.*` -> `boylenet-jarvis-svc` (`http://10.0.70.164:3002`)
- `prometheus.*` -> `boylenet-prometheus-svc` (`http://10.0.70.164:9090`)
- `alertmanager.*` -> `boylenet-alertmanager-svc` (`http://10.0.70.164:9093`)
- `cadvisor.*` -> `boylenet-cadvisor-svc` (`http://10.0.70.164:8085`)
- `devdocs.*` -> `boylenet-devdocs-svc` (`http://10.0.70.164:8088`)
- `ide.*` -> `boylenet-codeserver-svc` (`http://10.0.70.164:8443`)
- `verdaccio.*`, `npm.*` -> `boylenet-verdaccio-svc` (`http://10.0.70.164:4873`)
- `devpi.*`, `pypi.*` -> `boylenet-devpi-svc` (`http://10.0.70.164:3141`)
- `acng.*`, `apt.*` -> `boylenet-acng-svc` (`http://10.0.70.164:3142`)
- `elastic.*` -> `boylenet-elastic-svc` (`http://10.0.70.164:9200`)
- `treesitter.*`, `ast.*` -> `boylenet-treesitter-svc` (`http://10.0.70.164:8095`)
- `multimodal.*` -> `boylenet-multimodal-svc` (`http://10.0.70.164:4093`)
- `ollama.*` -> `ollama-service` (`http://10.0.70.228:11434`)
- `hermes.*`, `deepseek.*`, `openrouter.*` -> Routed via Traefik dynamic rules

### 4.3 Important Routing Differences Between Traefik (`:80`) and NGINX (`:443`)

Operators and agents inspecting vhosts should note five intentional/historical upstream differences between HTTP (`:80` Traefik `boylenet-dns-ingress.yml`) and HTTPS (`:443` NGINX `boylenet-https.conf`):

1. **Root Domain (`boylenet.themfboyles.org`)**:
   - On HTTP `:80` (Traefik): routes to `:4080` (`Dashy Portal`).
   - On HTTPS `:443` (NGINX): routes to `:4085` (`Unified OS Shell`).
2. **`chat.boylenet.themfboyles.org` & `search.boylenet.themfboyles.org`**:
   - On HTTP `:80` (Traefik): `chat.*` -> `:3003` (`bd-chat`), `search.*` -> `:8888` (`SearXNG`).
   - On HTTPS `:443` (NGINX): `chat.*` -> `:8090` (`OpenWebUI`), `search.*` -> `:4081` (`Executive Briefing & Universal Search`).
3. **`mobile.*` / `pwa.*` vs. `research.*`**:
   - On HTTP `:80` (Traefik): `mobile.*` -> `:4086` (`bd-mobile-pwa`), `research.*` -> `:4094` (`bd-deep-research-web`).
   - On HTTPS `:443` (NGINX): `pwa.*`/`mobile.*` -> `:4094`, `research.*` -> `:4091`, and `dreamer.*`/`memory.*` -> `:4086`.
4. **`prereview.*` & `vault.*`**:
   - On HTTP `:80` (Traefik): `prereview.*` -> `:4092` (`bd-gitea-prereview`), `vault.*` -> `:4087` (`bd-credentials-vault`).
   - On HTTPS `:443` (NGINX): `offsec.*`/`security.*` -> `:4092`, `prereview.*` -> `:4087`, and `vault.*`/`models.*` -> `:4084` (`bd-model-storefront`).

---

## 5. Operational Runbook: Inspecting & Managing Services, Containers & Daemons

### 5.1 Read-Only Health & Status Inspection Commands

- **Sweep all 83 monitored services via Infra Sentinel (`:4099`)**:
  ```bash
  curl -s http://127.0.0.1:4099/health | jq '{status, healthy_services, total_services, avg_latency_ms}'
  ```
- **Verify all `:4079`–`:4099` and `:8090` portals in a single compound check**:
  ```bash
  for p in 4079 4080 4081 4082 4083 4084 4085 4086 4087 4088 4089 4090 4091 4092 4093 4094 4095 4096 4097 4098 4099 8090; do
    printf ":%s -> HTTP %s\n" "$p" "$(curl -s -o /dev/null -w '%{http_code}' --max-time 2 http://127.0.0.1:$p/health)"
  done
  ```
- **Inspect System-Level & User-Level Systemd Daemons and Timers on `.164`**:
  ```bash
  systemctl list-units 'bd-*' 'llama-*' 'ollama*' --all --no-pager
  systemctl list-timers 'bd-*' --all --no-pager
  systemctl --user list-units 'bd-*' --all --no-pager
  systemctl --user list-timers 'bd-*' --all --no-pager
  ```
- **Inspect Remote GPU Inference Daemons Across `.72`, `.83`, `.125`, `.228`**:
  ```bash
  for h in 10.0.70.72 10.0.70.83 10.0.70.125 10.0.70.228; do
    echo "=== $h ==="
    ssh -o BatchMode=yes "$h" "systemctl list-units 'llama*' 'ollama*' 'qdrant*' --no-pager"
  done
  ```
- **Inspect Live Logs for Any Daemon**:
  ```bash
  # System unit on .164 (e.g., bd-gpu-orchestrator, bd-infra-sentinel, bd-autonomous-swarm-driver):
  journalctl -u bd-gpu-orchestrator.service -n 50 --no-pager

  # User unit on .164 (e.g., bd-telemetry-daemon, bd-cockpit, bd-chat, bd-hub-sampler):
  journalctl --user -u bd-telemetry-daemon.service -n 50 --no-pager

  # Remote GPU unit (e.g., llama-heretic-18080 on .83 or llama-tier1-alpha on .72):
  ssh 10.0.70.83 "journalctl -u llama-heretic-18080.service -n 50 --no-pager"
  ```

### 5.2 Service Lifecycle & Self-Healing Management

- **Trigger an on-demand Sentinel self-heal pass via MCP or HTTP**:
  ```bash
  # Via boylenet-infra MCP CLI:
  /home/mboyle/bin/bd-boylenet-infra-mcp.py --call sentinel_doctor_autoheal --args '{"action":"status"}'

  # Via Sentinel REST API (:4099):
  curl -s -X POST http://127.0.0.1:4099/api/sweep | jq .
  ```
- **Restart a System or User Unit (when authorized)**:
  ```bash
  # System-level daemon on .164:
  sudo systemctl restart bd-model-storefront.service

  # User-level daemon on .164:
  XDG_RUNTIME_DIR=/run/user/1000 systemctl --user restart bd-cockpit.service

  # Validate and reload NGINX :443 vhosts without dropping connections:
  sudo nginx -t && sudo systemctl reload nginx
  ```
