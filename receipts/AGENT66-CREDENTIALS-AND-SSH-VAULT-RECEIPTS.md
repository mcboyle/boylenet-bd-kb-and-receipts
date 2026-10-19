# AGENT-66 RECEIPTS — Unified Credentials, API Key & 27-Node SSH Health Vault (`:4087` / `http://vault.boylenet.themfboyles.com`)

- **Written At**: `2026-10-18T16:17:30Z`
- **Agent**: `Agent-66` (Swarms #21 & #C10: Unified Credentials, API Key & 27-Node SSH Health Vault)
- **Web Portal & API**: `http://vault.boylenet.themfboyles.com` | `http://10.0.70.164:4087` (`bd-credentials-vault.service`)
- **CLI Binary**: `/home/mboyle/bin/bd-credentials-vault.py` (`--list`, `--json`, `--ssh-matrix`, `--verify-auth`)

---

## 1. Unified Credentials & API Token Vault (`/home/mboyle/bin/bd-credentials-vault.py` on `:4087`)

Built `/home/mboyle/bin/bd-credentials-vault.py` and deployed `/etc/systemd/system/bd-credentials-vault.service` on `0.0.0.0:4087`:
- **31 Pre-Populated & Live-Verified Service Credentials** across 3 categories + 15 VNC host entries from `/home/mboyle/.config/bd/vnc-passwords.tsv`:
  1. **AI Studios & Gateways (`21` services)**:
     - `OpenWebUI Controller (:8090)` (`http://openwebui.boylenet.themfboyles.com`) -> `admin@localhost` / `boylenet-admin` (`sk-fleet-local`)
     - `OpenWebUI AI Vault A (.72:8080)` (`http://ai-webui.boylenet.themfboyles.com`) -> `admin@localhost` / `boylenet-admin` (`sk-fleet-local`)
     - `OpenWebUI Satellite (.228:8090)` (`http://10.0.70.228:8090`) -> `admin@localhost` / `boylenet-admin` (`sk-boylenet-local`)
     - `LiteLLM Proxy Admin (.164:4000/ui)` (`http://litellm.boylenet.themfboyles.com/ui`) -> `admin` / `boylenet-admin` (`sk-fleet-local`, `sk-boylenet-local`)
     - `LiteLLM Satellite Admin (.228:4000/ui)` (`http://litellm-228.boylenet.themfboyles.com/ui`) -> `admin` / `boylenet-admin` (`sk-boylenet-local`, `sk-fleet-local`)
     - `OpenClaw Control UI (:4097)` (`http://openclaw.boylenet.themfboyles.com`) -> `admin` / `boylenet-admin` (Token: `boylenet-openclaw-local`)
     - `LobeChat AI Studio (:4095)` (`http://lobechat.boylenet.themfboyles.com`) -> `no-login-required` / Access Code `boylenet-admin`
     - `Langfuse LLM Observability (.228:3000)` (`http://langfuse.boylenet.themfboyles.com`) -> `mcboyle@me.com` / `boylenet-admin` (`pk-lf-boylenet-local` / `sk-lf-boylenet-local`)
     - `OpenCode Web UI (:4096)`, `Multi-Harness Studio (:4098)`, `Jarvis Cockpit (:3002)`, `Swarm Chat (:3003)`, `Cloud Burst (:4090)`, `Deep Research (:4094)`, `Voice/Vision (:4093)`, `Pre-Review (:4092)`, `Hardware (:4091)`, `Sentinel (:4099)`, `Topology (:4088)`, `Ops (:4089)`, `Mobile PWA (:4086)` -> Direct access (`No login wall`)
  2. **Dev, Git, Storage & Observability (`6` services)**:
     - `Gitea (:3000)` (`http://gitea.boylenet.themfboyles.com`) -> `mboyle` / `boylenet-admin`
     - `Grafana (:3001)` (`http://grafana.boylenet.themfboyles.com`) -> `admin` / `admin`
     - `MinIO S3 Console (:9001)` (`http://minio.boylenet.themfboyles.com`) -> `minioadmin` / `minioadmin123`
     - `Code-Server Web VSCode (:8443)` (`http://codeserver.boylenet.themfboyles.com`) -> `coder` / `boylenet-admin` (`--auth none` enabled)
     - `Dashy Command Nexus (:4080)` & `Qdrant Vector DB (.125:6333/dashboard)` -> Direct access (`No login wall`)
  3. **VMware vSphere, ESXi, HPE iLO4 & UniFi (`4` entries dynamically hydrated from `/home/mboyle/.config/bd/vsphere.env` & `/home/mboyle/.config/bd/unifi.env`)**:
     - `vCenter vc04 (https://10.0.20.70/ui)` -> `Administrator@boylenet.themfboyles.com` / `Matt99!!` (`GOVC_URL=https://10.0.20.70/sdk`)
     - `ESXi 01..04 (https://10.0.20.41..44/ui)` -> `root` / `Matt99!!`
     - `HPE iLO4 (https://10.0.10.10..40 & .252)` -> `mboyle` / `Matt99!!`
     - `UniFi OS Gateway (https://10.0.70.1)` -> `mboyle` / `Matt99!!` (API Key: `az3533azXwwJnRD5OciI0A9rvNnF-hPC`)
- **1-Click Action Buttons on Every Card**:
  - `Copy Username`, `Copy Password`, `Copy API Key`, and `Open UI ↗` (plus `Copy SSH` on every node in the SSH Health Grid).

---

## 2. UniFi `static-dns` (`10.0.70.1`), Traefik (`:80`), and Dashy (`:4080`) Wiring

1. **Authoritative UniFi `static-dns` Records (`10.0.70.1`)**:
   - `vault` -> `10.0.70.164` (`_id: 3012cbad-2bb7-49f7-8c5e-7582d9f59fcd`, `dig +short @10.0.70.1 vault` -> `10.0.70.164`)
   - `vault.boylenet.themfboyles.com` -> `10.0.70.164` (`_id: 277560b3-0741-4595-8a7a-6f1132a4a81a`, `dig +short @10.0.70.1 vault.boylenet.themfboyles.com` -> `10.0.70.164`)
2. **Traefik Dynamic Ingress (`/home/mboyle/infra/expansion-layer/dynamic/boylenet-dns-ingress.yml`)**:
   - Added router `boylenet-vault` (`Host(\`vault.boylenet.themfboyles.com\`) || Host(\`vault\`)`) -> `boylenet-vault-svc` (`http://10.0.70.164:4087`).
3. **Dashy Portal (`/home/mboyle/infra/dashy/conf.yml`)**:
   - Added `Credentials Vault (:4087)` to top `navLinks`, `AI Command & Chat Studios`, and `UniFi DNS VirtualHost Shortlinks (Traefik Port 80)`.

---

## 3. Live Verification Receipts

### A. `curl -s http://127.0.0.1:4087/health` & `curl -s http://vault.boylenet.themfboyles.com/health`
```json
{
  "status": "ok",
  "service": "bd-credentials-vault",
  "port": 4087,
  "url": "http://vault.boylenet.themfboyles.com",
  "direct_url": "http://10.0.70.164:4087",
  "credentials_count": 31,
  "vnc_entries_count": 15,
  "checked_at": "2026-10-18T16:17:20Z"
}
```

### B. Live 27-Node + AI Vault SSH Key Health Matrix (`GET /api/ssh-matrix`)
```json
{
  "status": "ok",
  "checked_at": "2026-10-18T16:17:21Z",
  "wall_elapsed_ms": 917.4,
  "total_nodes": 38,
  "online_count": 16,
  "cold_spare_count": 22,
  "offline_or_auth_fail_count": 0,
  "avg_latency_ms": 408.6
}
```

### C. Live Parallel Auth & Endpoint Verification (`POST /api/verify-auth`)
```json
{
  "status": "ok",
  "checked_at": "2026-10-18T16:17:23Z",
  "wall_elapsed_ms": 2182.0,
  "total_checked": 31,
  "verified_count": 31
}
```

### D. CLI Output (`/home/mboyle/bin/bd-credentials-vault.py --list`)
```text
================================================================================================================
 BOYLENET UNIFIED CREDENTIALS, API TOKENS & 27-NODE SSH HEALTH VAULT (:4087 | http://vault.boylenet.themfboyles.com)
================================================================================================================

[AI Studios & Gateways]
----------------------------------------------------------------------------------------------------------------
  * OpenWebUI Controller (:8090)           | URL: http://openwebui.boylenet.themfboyles.com
    User: admin@localhost                  | Pass: boylenet-admin       | Key: sk-fleet-local
  * OpenWebUI AI Vault A (.72:8080)        | URL: http://ai-webui.boylenet.themfboyles.com
    User: admin@localhost                  | Pass: boylenet-admin       | Key: sk-fleet-local
  * OpenWebUI Satellite (.228:8090)        | URL: http://10.0.70.228:8090
    User: admin@localhost                  | Pass: boylenet-admin       | Key: sk-boylenet-local
  * LiteLLM Proxy Admin (.164:4000/ui)     | URL: http://litellm.boylenet.themfboyles.com/ui
    User: admin                            | Pass: boylenet-admin       | Key: sk-fleet-local
  * LiteLLM Satellite Admin (.228:4000/ui) | URL: http://litellm-228.boylenet.themfboyles.com/ui
    User: admin                            | Pass: boylenet-admin       | Key: sk-boylenet-local
  * OpenClaw Control UI (:4097)            | URL: http://openclaw.boylenet.themfboyles.com
    User: admin                            | Pass: boylenet-admin       | Key: boylenet-openclaw-local
  * LobeChat AI Studio (:4095)             | URL: http://lobechat.boylenet.themfboyles.com
    User: no-login-required                | Pass: boylenet-admin       | Key: sk-fleet-local
  * Langfuse LLM Observability (.228:3000) | URL: http://langfuse.boylenet.themfboyles.com
    User: mcboyle@me.com                   | Pass: boylenet-admin       | Key: pk-lf-boylenet-local / sk-lf-boylenet-local
```
