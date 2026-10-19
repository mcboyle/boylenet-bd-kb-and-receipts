# AGENT-149: Cluster-Wide Domain Migration Receipts (`themfboyles.com` -> `themfboyles.org`)

- **Timestamp (UTC)**: `2026-10-19T00:10:45Z`
- **Agent**: `Agent-149 (Cluster-Wide Domain Migrator: themfboyles.com -> themfboyles.org)`
- **Primary Domain**: `themfboyles.org` (`*.boylenet.themfboyles.org` & `*.themfboyles.org`)
- **Backward-Compatible Alias Preserved**: `themfboyles.com` (`*.boylenet.themfboyles.com` & `*.themfboyles.com`)
- **Live Progress Stream**: `/var/tmp/bd-headless-streams/agent149-domain-migration-themfboyles-org.log`
- **Zero `.72` Contention Invariant**: Enforced (`0` commands or SSH probes executed against `10.0.70.72` while `Agent-37` finishes `kimi-k3-abliterated-iq1s-gguf`).

---

## 1. Layer 1 — Traefik `:80` & NGINX `:443` Ingress + Wildcard TLS Certificates

1. **Traefik Dynamic Ingress (`:80`)**:
   - Updated `/home/mboyle/infra/expansion-layer/dynamic/boylenet-dns-ingress.yml` (all 43 routers), `/home/mboyle/infra/expansion-layer/dynamic/boylenet-sso-ca.yml`, and `/home/mboyle/infra/expansion-layer/dynamic/routes.yml` (`/home/mboyle/infra/expansion-layer/routes.yml`) so every router `rule: Host(...)` matches:
     - `Host(\`<service>.boylenet.themfboyles.org\`)` (Primary FQDN)
     - `Host(\`<service>.themfboyles.org\`)` (Primary Short Domain)
     - `Host(\`<service>.boylenet.themfboyles.com\`)` (Backward-Compatible Secondary Alias)
     - `Host(\`<service>.themfboyles.com\`)` (Secondary Short Domain Alias)
     - `Host(\`<service>\`)` (Short Hostname)
     - Plus root domains `boylenet.themfboyles.org`, `themfboyles.org`, `boylenet.themfboyles.com`, `themfboyles.com` on `boylenet-dashy`.
2. **NGINX HTTPS (`:443`) & Wildcard TLS Certificates**:
   - Let's Encrypt DNS-01 wildcard certificate active at `/etc/letsencrypt/live/themfboyles.org/fullchain.pem` (`CN = themfboyles.org`, SANs: `DNS:*.boylenet.themfboyles.org, DNS:*.themfboyles.org, DNS:themfboyles.org`, issuer `Let's Encrypt YE1`).
   - Updated `/home/mboyle/infra/ca/boylenet-wildcard.crt` and `/home/mboyle/infra/expansion-layer/dynamic/certs/boylenet-wildcard.crt` (signed by Boylenet Root CA) with 18 SANs covering `*.boylenet.themfboyles.org`, `*.themfboyles.org`, `*.boylenet.themfboyles.com`, `*.themfboyles.com`, `*.local`, and cluster IPs.
   - Added `/etc/nginx/conf.d/boylenet-extra-vhosts.conf` alongside `/etc/nginx/sites-enabled/boylenet-https.conf` so all 47 Traefik & NGINX subdomains serve HTTP/2 over `:443` with zero hash bucket warnings (`nginx -t` PASS).

---

## 2. Layer 2 — UniFi UDM-Pro Static DNS & Controller `/etc/hosts`

1. **UniFi UDM-Pro (`BoyleNetwork`, Console `602232292CA100000000069BFAAB0000000006EB9D760000000062F4D112:2071288107`)**:
   - Synced `*.boylenet.themfboyles.org` and `*.themfboyles.org` static `A` records via `/proxy/network/v2/api/site/default/static-dns` while preserving all existing `*.boylenet.themfboyles.com` records (`533+` total active static DNS records; exported to `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/boylenet_static_dns_themfboyles_org.json`).
2. **Controller `/etc/hosts` (`10.0.70.164`)**:
   - Updated `/etc/hosts` managed block (`# BEGIN BOYLENET THEMFBOYLES.ORG & .COM DNS`) with 141 lines mapping all 232 cluster and ingress host prefixes across `.boylenet.themfboyles.org`, `.themfboyles.org`, `.boylenet.themfboyles.com`, `.themfboyles.com`, and short names.

---

## 3. Layer 3 — Boylenet Web Portals (`:4080`–`:4099`) & OpenWebUI (`:8090`) Primary Domain Cutover

Updated primary visible links, titles, `/health` metadata, QR codes, and SSO/CORS rules to `boylenet.themfboyles.org` (while retaining `.boylenet.themfboyles.com` compatibility in host checks & browser extension permissions) and restarted all systemd services:
- `/home/mboyle/infra/dashy/conf.yml` (`:4080` Dashy Command Nexus)
- `/home/mboyle/bin/bd-executive-briefing.py` (`:4081` Executive Briefing & Cluster Doctor)
- `/home/mboyle/bin/bd-trust-and-sso-portal.py` (`:4082` Zero-Friction SSO, QR Handoff & Root CA Portal)
- `/home/mboyle/bin/bd-web-terminal-drawer.py` (`:4083` Multi-Node Web Terminal & Agent TV Wall)
- `/home/mboyle/bin/bd-model-storefront.py` (`:4084` 86-Model AI Vault Storefront)
- `/home/mboyle/bin/bd-os-shell.py` (`:4085` Unified OS Desktop Shell)
- `/home/mboyle/bin/bd-mobile-pwa.py` (`:4086` Mobile & Tablet PWA Command Deck)
- `/home/mboyle/bin/bd-credentials-vault.py` (`:4087` Unified Credentials & SSH Health Vault — preserved `Administrator@boylenet.themfboyles.com` for vCenter SSO)
- `/home/mboyle/bin/bd-topology-visualizer.py` (`:4088` 3D/2D Architecture Topology)
- `/home/mboyle/bin/bd-ops-web.py` (`:4089` Natural-Language Ops Shell)
- `/home/mboyle/bin/bd-cloud-burst-web.py` (`:4090` Cloud Burst Orchestrator)
- `/home/mboyle/bin/bd-hardware-telemetry.py` (`:4091` Physical Rack, iLO & vSAN Matrix)
- `/home/mboyle/bin/bd-gitea-prereview.py` (`:4092` Gitea Pre-Review & OffSec Swarm)
- `/home/mboyle/bin/bd-multimodal-bridge.py` (`:4093` Voice, Vision & Multimodal Cockpit)
- `/home/mboyle/bin/bd-deep-research-web.py` (`:4094` Deep Research & Dual-Oracle Studio)
- `/home/mboyle/bin/bd-multi-harness-web.py` (`:4098` Multi-Harness AI Command & Reasoning Studio)
- `/home/mboyle/bin/bd-infra-sentinel.py` (`:4099` Infra Sentinel 83-Service Self-Healer)
- `/home/mboyle/bin/bd-boylenet-infra-mcp.py`, `/home/mboyle/bin/bd-openwebui-tool-gateway.py` (`:4079`), `/home/mboyle/bin/bd-openwebui-sync-bdkb-pipelines-50tools.py` (`:8090`)

---

## 4. Layer 4 — Complete 47-Subdomain (235-Probe) HTTP `:80` & HTTPS `:443` Verification Matrix

- **Subdomains Verified**: `47 / 47` (`100%`)
- **Total Probes Executed**: `235 / 235` (`200 OK`)

| # | Subdomain | Backend Port | Service | Primary HTTP (`:80`) | Primary HTTPS (`:443` Let's Encrypt) | Short `.org` HTTPS (`:443`) | Alias `.com` HTTP (`:80`) | Alias `.com` HTTPS (`:443`) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | `os` | `:4085` | Unified OS Desktop Shell | `http://os.boylenet.themfboyles.org/` -> **`200`** | `https://os.boylenet.themfboyles.org/` -> **`200`** | `https://os.themfboyles.org/` -> **`200`** | `http://os.boylenet.themfboyles.com/` -> **`200`** | `https://os.boylenet.themfboyles.com/` -> **`200`** |
| 2 | `dashy` | `:4080` | Dashy Command Nexus | `http://dashy.boylenet.themfboyles.org/` -> **`200`** | `https://dashy.boylenet.themfboyles.org/` -> **`200`** | `https://dashy.themfboyles.org/` -> **`200`** | `http://dashy.boylenet.themfboyles.com/` -> **`200`** | `https://dashy.boylenet.themfboyles.com/` -> **`200`** |
| 3 | `portal` | `:4080` | Dashy Portal Alias | `http://portal.boylenet.themfboyles.org/` -> **`200`** | `https://portal.boylenet.themfboyles.org/` -> **`200`** | `https://portal.themfboyles.org/` -> **`200`** | `http://portal.boylenet.themfboyles.com/` -> **`200`** | `https://portal.boylenet.themfboyles.com/` -> **`200`** |
| 4 | `briefing` | `:4081` | Executive Briefing & Doctor | `http://briefing.boylenet.themfboyles.org/` -> **`200`** | `https://briefing.boylenet.themfboyles.org/` -> **`200`** | `https://briefing.themfboyles.org/` -> **`200`** | `http://briefing.boylenet.themfboyles.com/` -> **`200`** | `https://briefing.boylenet.themfboyles.com/` -> **`200`** |
| 5 | `doctor` | `:4081` | Cluster Doctor Alias | `http://doctor.boylenet.themfboyles.org/` -> **`200`** | `https://doctor.boylenet.themfboyles.org/` -> **`200`** | `https://doctor.themfboyles.org/` -> **`200`** | `http://doctor.boylenet.themfboyles.com/` -> **`200`** | `https://doctor.boylenet.themfboyles.com/` -> **`200`** |
| 6 | `sso` | `:4082` | Zero-Friction SSO & QR Handoff | `http://sso.boylenet.themfboyles.org/` -> **`200`** | `https://sso.boylenet.themfboyles.org/` -> **`200`** | `https://sso.themfboyles.org/` -> **`200`** | `http://sso.boylenet.themfboyles.com/` -> **`200`** | `https://sso.boylenet.themfboyles.com/` -> **`200`** |
| 7 | `ca` | `:4082` | Root CA Trust Portal | `http://ca.boylenet.themfboyles.org/` -> **`200`** | `https://ca.boylenet.themfboyles.org/` -> **`200`** | `https://ca.themfboyles.org/` -> **`200`** | `http://ca.boylenet.themfboyles.com/` -> **`200`** | `https://ca.boylenet.themfboyles.com/` -> **`200`** |
| 8 | `terminal` | `:4083` | Multi-Node Web Terminal & TV Wall | `http://terminal.boylenet.themfboyles.org/` -> **`200`** | `https://terminal.boylenet.themfboyles.org/` -> **`200`** | `https://terminal.themfboyles.org/` -> **`200`** | `http://terminal.boylenet.themfboyles.com/` -> **`200`** | `https://terminal.boylenet.themfboyles.com/` -> **`200`** |
| 9 | `models` | `:4084` | 86-Model AI Vault Storefront | `http://models.boylenet.themfboyles.org/` -> **`200`** | `https://models.boylenet.themfboyles.org/` -> **`200`** | `https://models.themfboyles.org/` -> **`200`** | `http://models.boylenet.themfboyles.com/` -> **`200`** | `https://models.boylenet.themfboyles.com/` -> **`200`** |
| 10 | `mobile` | `:4086` | Mobile & Tablet PWA Command Deck | `http://mobile.boylenet.themfboyles.org/` -> **`200`** | `https://mobile.boylenet.themfboyles.org/` -> **`200`** | `https://mobile.themfboyles.org/` -> **`200`** | `http://mobile.boylenet.themfboyles.com/` -> **`200`** | `https://mobile.boylenet.themfboyles.com/` -> **`200`** |
| 11 | `pwa` | `:4086` | Mobile PWA Alias | `http://pwa.boylenet.themfboyles.org/` -> **`200`** | `https://pwa.boylenet.themfboyles.org/` -> **`200`** | `https://pwa.themfboyles.org/` -> **`200`** | `http://pwa.boylenet.themfboyles.com/` -> **`200`** | `https://pwa.boylenet.themfboyles.com/` -> **`200`** |
| 12 | `vault` | `:4087` | Credentials & SSH Health Vault | `http://vault.boylenet.themfboyles.org/` -> **`200`** | `https://vault.boylenet.themfboyles.org/` -> **`200`** | `https://vault.themfboyles.org/` -> **`200`** | `http://vault.boylenet.themfboyles.com/` -> **`200`** | `https://vault.boylenet.themfboyles.com/` -> **`200`** |
| 13 | `credentials` | `:4087` | Credentials Vault Alias | `http://credentials.boylenet.themfboyles.org/` -> **`200`** | `https://credentials.boylenet.themfboyles.org/` -> **`200`** | `https://credentials.themfboyles.org/` -> **`200`** | `http://credentials.boylenet.themfboyles.com/` -> **`200`** | `https://credentials.boylenet.themfboyles.com/` -> **`200`** |
| 14 | `topology` | `:4088` | 3D/2D Architecture Topology | `http://topology.boylenet.themfboyles.org/` -> **`200`** | `https://topology.boylenet.themfboyles.org/` -> **`200`** | `https://topology.themfboyles.org/` -> **`200`** | `http://topology.boylenet.themfboyles.com/` -> **`200`** | `https://topology.boylenet.themfboyles.com/` -> **`200`** |
| 15 | `ops` | `:4089` | Natural-Language Ops Shell | `http://ops.boylenet.themfboyles.org/` -> **`200`** | `https://ops.boylenet.themfboyles.org/` -> **`200`** | `https://ops.themfboyles.org/` -> **`200`** | `http://ops.boylenet.themfboyles.com/` -> **`200`** | `https://ops.boylenet.themfboyles.com/` -> **`200`** |
| 16 | `cloud` | `:4090` | Cloud Burst Orchestrator | `http://cloud.boylenet.themfboyles.org/` -> **`200`** | `https://cloud.boylenet.themfboyles.org/` -> **`200`** | `https://cloud.themfboyles.org/` -> **`200`** | `http://cloud.boylenet.themfboyles.com/` -> **`200`** | `https://cloud.boylenet.themfboyles.com/` -> **`200`** |
| 17 | `burst` | `:4090` | Cloud Burst Alias | `http://burst.boylenet.themfboyles.org/` -> **`200`** | `https://burst.boylenet.themfboyles.org/` -> **`200`** | `https://burst.themfboyles.org/` -> **`200`** | `http://burst.boylenet.themfboyles.com/` -> **`200`** | `https://burst.boylenet.themfboyles.com/` -> **`200`** |
| 18 | `hardware` | `:4091` | Physical Rack, iLO & vSAN Matrix | `http://hardware.boylenet.themfboyles.org/` -> **`200`** | `https://hardware.boylenet.themfboyles.org/` -> **`200`** | `https://hardware.themfboyles.org/` -> **`200`** | `http://hardware.boylenet.themfboyles.com/` -> **`200`** | `https://hardware.boylenet.themfboyles.com/` -> **`200`** |
| 19 | `prereview` | `:4092` | Gitea Pre-Review & OffSec Swarm | `http://prereview.boylenet.themfboyles.org/` -> **`200`** | `https://prereview.boylenet.themfboyles.org/` -> **`200`** | `https://prereview.themfboyles.org/` -> **`200`** | `http://prereview.boylenet.themfboyles.com/` -> **`200`** | `https://prereview.boylenet.themfboyles.com/` -> **`200`** |
| 20 | `offsec` | `:4092` | OffSec Red-Team Alias | `http://offsec.boylenet.themfboyles.org/` -> **`200`** | `https://offsec.boylenet.themfboyles.org/` -> **`200`** | `https://offsec.themfboyles.org/` -> **`200`** | `http://offsec.boylenet.themfboyles.com/` -> **`200`** | `https://offsec.boylenet.themfboyles.com/` -> **`200`** |
| 21 | `voice` | `:4093` | Voice, Vision & Multimodal Cockpit | `http://voice.boylenet.themfboyles.org/` -> **`200`** | `https://voice.boylenet.themfboyles.org/` -> **`200`** | `https://voice.themfboyles.org/` -> **`200`** | `http://voice.boylenet.themfboyles.com/` -> **`200`** | `https://voice.boylenet.themfboyles.com/` -> **`200`** |
| 22 | `multimodal` | `:4093` | Multimodal Bridge Alias | `http://multimodal.boylenet.themfboyles.org/` -> **`200`** | `https://multimodal.boylenet.themfboyles.org/` -> **`200`** | `https://multimodal.themfboyles.org/` -> **`200`** | `http://multimodal.boylenet.themfboyles.com/` -> **`200`** | `https://multimodal.boylenet.themfboyles.com/` -> **`200`** |
| 23 | `research` | `:4094` | Deep Research & Dual-Oracle Studio | `http://research.boylenet.themfboyles.org/` -> **`200`** | `https://research.boylenet.themfboyles.org/` -> **`200`** | `https://research.themfboyles.org/` -> **`200`** | `http://research.boylenet.themfboyles.com/` -> **`200`** | `https://research.boylenet.themfboyles.com/` -> **`200`** |
| 24 | `lobechat` | `:4095` | LobeChat AI Studio | `http://lobechat.boylenet.themfboyles.org/` -> **`200`** | `https://lobechat.boylenet.themfboyles.org/` -> **`200`** | `https://lobechat.themfboyles.org/` -> **`200`** | `http://lobechat.boylenet.themfboyles.com/` -> **`200`** | `https://lobechat.boylenet.themfboyles.com/` -> **`200`** |
| 25 | `opencode` | `:4096` | OpenCode Web UI | `http://opencode.boylenet.themfboyles.org/` -> **`200`** | `https://opencode.boylenet.themfboyles.org/` -> **`200`** | `https://opencode.themfboyles.org/` -> **`200`** | `http://opencode.boylenet.themfboyles.com/` -> **`200`** | `https://opencode.boylenet.themfboyles.com/` -> **`200`** |
| 26 | `openclaw` | `:4097` | OpenClaw Control UI | `http://openclaw.boylenet.themfboyles.org/` -> **`200`** | `https://openclaw.boylenet.themfboyles.org/` -> **`200`** | `https://openclaw.themfboyles.org/` -> **`200`** | `http://openclaw.boylenet.themfboyles.com/` -> **`200`** | `https://openclaw.boylenet.themfboyles.com/` -> **`200`** |
| 27 | `harnesses` | `:4098` | Multi-Harness AI Studio | `http://harnesses.boylenet.themfboyles.org/` -> **`200`** | `https://harnesses.boylenet.themfboyles.org/` -> **`200`** | `https://harnesses.themfboyles.org/` -> **`200`** | `http://harnesses.boylenet.themfboyles.com/` -> **`200`** | `https://harnesses.boylenet.themfboyles.com/` -> **`200`** |
| 28 | `sentinel` | `:4099` | Infra Sentinel Self-Healer | `http://sentinel.boylenet.themfboyles.org/` -> **`200`** | `https://sentinel.boylenet.themfboyles.org/` -> **`200`** | `https://sentinel.themfboyles.org/` -> **`200`** | `http://sentinel.boylenet.themfboyles.com/` -> **`200`** | `https://sentinel.boylenet.themfboyles.com/` -> **`200`** |
| 29 | `openwebui` | `:8090` | OpenWebUI Controller Studio | `http://openwebui.boylenet.themfboyles.org/` -> **`200`** | `https://openwebui.boylenet.themfboyles.org/` -> **`200`** | `https://openwebui.themfboyles.org/` -> **`200`** | `http://openwebui.boylenet.themfboyles.com/` -> **`200`** | `https://openwebui.boylenet.themfboyles.com/` -> **`200`** |
| 30 | `ai-webui` | `:8090` | OpenWebUI AI Vault A | `http://ai-webui.boylenet.themfboyles.org/` -> **`200`** | `https://ai-webui.boylenet.themfboyles.org/` -> **`200`** | `https://ai-webui.themfboyles.org/` -> **`200`** | `http://ai-webui.boylenet.themfboyles.com/` -> **`200`** | `https://ai-webui.boylenet.themfboyles.com/` -> **`200`** |
| 31 | `litellm` | `:4000` | LiteLLM Gateway (.164) | `http://litellm.boylenet.themfboyles.org/` -> **`200`** | `https://litellm.boylenet.themfboyles.org/` -> **`200`** | `https://litellm.themfboyles.org/` -> **`200`** | `http://litellm.boylenet.themfboyles.com/` -> **`200`** | `https://litellm.boylenet.themfboyles.com/` -> **`200`** |
| 32 | `litellm-228` | `:4000` | LiteLLM Satellite (.228) | `http://litellm-228.boylenet.themfboyles.org/` -> **`200`** | `https://litellm-228.boylenet.themfboyles.org/` -> **`200`** | `https://litellm-228.themfboyles.org/` -> **`200`** | `http://litellm-228.boylenet.themfboyles.com/` -> **`200`** | `https://litellm-228.boylenet.themfboyles.com/` -> **`200`** |
| 33 | `orchestrator` | `:4010` | 4-GPU Orchestrator | `http://orchestrator.boylenet.themfboyles.org/status` -> **`200`** | `https://orchestrator.boylenet.themfboyles.org/status` -> **`200`** | `https://orchestrator.themfboyles.org/status` -> **`200`** | `http://orchestrator.boylenet.themfboyles.com/status` -> **`200`** | `https://orchestrator.boylenet.themfboyles.com/status` -> **`200`** |
| 34 | `jarvis` | `:3002` | Jarvis OS Cockpit | `http://jarvis.boylenet.themfboyles.org/` -> **`200`** | `https://jarvis.boylenet.themfboyles.org/` -> **`200`** | `https://jarvis.themfboyles.org/` -> **`200`** | `http://jarvis.boylenet.themfboyles.com/` -> **`200`** | `https://jarvis.boylenet.themfboyles.com/` -> **`200`** |
| 35 | `chat` | `:3003` | Swarm Chat Arena | `http://chat.boylenet.themfboyles.org/` -> **`200`** | `https://chat.boylenet.themfboyles.org/` -> **`200`** | `https://chat.themfboyles.org/` -> **`200`** | `http://chat.boylenet.themfboyles.com/` -> **`200`** | `https://chat.boylenet.themfboyles.com/` -> **`200`** |
| 36 | `gitea` | `:3000` | Gitea Git Server | `http://gitea.boylenet.themfboyles.org/` -> **`200`** | `https://gitea.boylenet.themfboyles.org/` -> **`200`** | `https://gitea.themfboyles.org/` -> **`200`** | `http://gitea.boylenet.themfboyles.com/` -> **`200`** | `https://gitea.boylenet.themfboyles.com/` -> **`200`** |
| 37 | `grafana` | `:3001` | Grafana v13 Telemetry | `http://grafana.boylenet.themfboyles.org/` -> **`200`** | `https://grafana.boylenet.themfboyles.org/` -> **`200`** | `https://grafana.themfboyles.org/` -> **`200`** | `http://grafana.boylenet.themfboyles.com/` -> **`200`** | `https://grafana.boylenet.themfboyles.com/` -> **`200`** |
| 38 | `prometheus` | `:9090` | Prometheus Metrics | `http://prometheus.boylenet.themfboyles.org/` -> **`200`** | `https://prometheus.boylenet.themfboyles.org/` -> **`200`** | `https://prometheus.themfboyles.org/` -> **`200`** | `http://prometheus.boylenet.themfboyles.com/` -> **`200`** | `https://prometheus.boylenet.themfboyles.com/` -> **`200`** |
| 39 | `alertmanager` | `:9093` | Alertmanager | `http://alertmanager.boylenet.themfboyles.org/` -> **`200`** | `https://alertmanager.boylenet.themfboyles.org/` -> **`200`** | `https://alertmanager.themfboyles.org/` -> **`200`** | `http://alertmanager.boylenet.themfboyles.com/` -> **`200`** | `https://alertmanager.boylenet.themfboyles.com/` -> **`200`** |
| 40 | `minio` | `:9001` | MinIO S3 Console | `http://minio.boylenet.themfboyles.org/` -> **`200`** | `https://minio.boylenet.themfboyles.org/` -> **`200`** | `https://minio.themfboyles.org/` -> **`200`** | `http://minio.boylenet.themfboyles.com/` -> **`200`** | `https://minio.boylenet.themfboyles.com/` -> **`200`** |
| 41 | `codeserver` | `:8443` | Code-Server Web VSCode | `http://codeserver.boylenet.themfboyles.org/` -> **`200`** | `https://codeserver.boylenet.themfboyles.org/` -> **`200`** | `https://codeserver.themfboyles.org/` -> **`200`** | `http://codeserver.boylenet.themfboyles.com/` -> **`200`** | `https://codeserver.boylenet.themfboyles.com/` -> **`200`** |
| 42 | `searxng` | `:8888` | SearXNG Meta-Search | `http://searxng.boylenet.themfboyles.org/` -> **`200`** | `https://searxng.boylenet.themfboyles.org/` -> **`200`** | `https://searxng.themfboyles.org/` -> **`200`** | `http://searxng.boylenet.themfboyles.com/` -> **`200`** | `https://searxng.boylenet.themfboyles.com/` -> **`200`** |
| 43 | `devdocs` | `:8088` | DevDocs API Reference | `http://devdocs.boylenet.themfboyles.org/` -> **`200`** | `https://devdocs.boylenet.themfboyles.org/` -> **`200`** | `https://devdocs.themfboyles.org/` -> **`200`** | `http://devdocs.boylenet.themfboyles.com/` -> **`200`** | `https://devdocs.boylenet.themfboyles.com/` -> **`200`** |
| 44 | `treesitter` | `:8095` | Tree-Sitter AST Server | `http://treesitter.boylenet.themfboyles.org/health` -> **`200`** | `https://treesitter.boylenet.themfboyles.org/health` -> **`200`** | `https://treesitter.themfboyles.org/health` -> **`200`** | `http://treesitter.boylenet.themfboyles.com/health` -> **`200`** | `https://treesitter.boylenet.themfboyles.com/health` -> **`200`** |
| 45 | `qdrant` | `:6333` | Qdrant Vector DB | `http://qdrant.boylenet.themfboyles.org/collections` -> **`200`** | `https://qdrant.boylenet.themfboyles.org/collections` -> **`200`** | `https://qdrant.themfboyles.org/collections` -> **`200`** | `http://qdrant.boylenet.themfboyles.com/collections` -> **`200`** | `https://qdrant.boylenet.themfboyles.com/collections` -> **`200`** |
| 46 | `langfuse` | `:3000` | Langfuse Observability | `http://langfuse.boylenet.themfboyles.org/` -> **`200`** | `https://langfuse.boylenet.themfboyles.org/` -> **`200`** | `https://langfuse.themfboyles.org/` -> **`200`** | `http://langfuse.boylenet.themfboyles.com/` -> **`200`** | `https://langfuse.boylenet.themfboyles.com/` -> **`200`** |
| 47 | `novnc` | `:6080` | NoVNC Desktop Console | `http://novnc.boylenet.themfboyles.org/` -> **`200`** | `https://novnc.boylenet.themfboyles.org/` -> **`200`** | `https://novnc.themfboyles.org/` -> **`200`** | `http://novnc.boylenet.themfboyles.com/` -> **`200`** | `https://novnc.boylenet.themfboyles.com/` -> **`200`** |
