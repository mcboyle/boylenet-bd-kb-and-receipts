# BD-KB Volume 27: AGY GPT-OSS-120B Deep Bible — Network Topology, NGINX :443 Wildcard TLS, Traefik & Dual Chrome CDP Oracles (.137 & .181)
> **Author**: `Spec-227` (`AGY claude+gpt (gpt-oss-120b-medium)`)
> **Generated At**: `2026-10-19T22:58:27Z`
> **Scope**: Strictly Read-Only Infrastructure, Tools, Services, LLMs & PM Catch-Up Documentation (`O3416` Compliant)

---

# BD‑KB Volume 27  
## AGY GPT‑OSS‑120B Deep Bible — Network Topology, NGINX `:443` Wildcard TLS, Cloudflare DNS‑01 Certificates, & Dual Chrome CDP Oracles  

*Author: Spec‑227*  
*Date: 2026‑10‑19*  

---  

## Table of Contents  

1. [Overview](#overview)  
2. [Boylenet Physical & Logical Network Layout](#boylenet-network)  
   - 2.1 [VLAN Architecture](#vlan-architecture)  
   - 2.2 [IP Address Allocation](#ip-allocation)  
   - 2.3 [Routing & Firewall Zones](#routing-firewall)  
3. [Wildcard HTTPS Reverse‑Proxy (`:443`)](#nginx-wildcard-https)  
   - 3.1 [TLS Termination & Certificate Management](#tls-termination)  
   - 3.2 [NGINX Server Blocks (25 vhosts)](#nginx-server-blocks)  
   - 3.3 [Snippets Used (`boylenet‑tls.conf`, `boylenet‑proxy.conf`)](#nginx-snippets)  
   - 3.4 [Interaction with Docker Traefik on `:80`](#traefik‑coexistence)  
4. [Cloudflare DNS‑01 Automatic TLS Certificates](#cloudflare-dns01)  
   - 4.1 [Domain Zones & Records](#dns-zones)  
   - 4.2 [ACME Challenge Flow](#acme-flow)  
   - 4.3 [Renewal Cadence & Failure Handling](#renewal)  
5. [Dual Chrome CDP Browser Oracles](#browser-oracles)  
   - 5.1 [Hardware & Host Summary](#oracle-hardware)  
   - 5.2 [Chrome Remote‑Debugging Profile Constraints](#chrome-profile)  
   - 5.3 [IPv6 Loopback Port‑Proxy Configuration](#ipv6-proxy)  
   - 5.4 [Credential Autofill Policy & PIN 628895](#credential‑policy)  
   - 5.5 [CLI Driver (`bd‑oracle`)](#cli-driver)  
   - 5.6 [Consensus & Unanimous Modes](#consensus)  
6. [Operational Runbooks & Best‑Practices](#runbooks)  
   - 6.1 [Adding a New Service vhost](#add‑vhost)  
   - 6.2 [Rotating Cloudflare Certificates](#rotate‑cert)  
   - 6.3 [Updating Oracle Profiles](#update‑oracle)  
7. [Appendix](#appendix)  
   - A. [Full NGINX `boylenet‑https.conf` (excerpt)](#full-nginx)  
   - B. [Mermaid Diagram of the Reverse‑Proxy Flow](#mermaid-diagram)  
   - C. [Glossary of Terms](#glossary)  

---  

<a name="overview"></a>  
## 1. Overview  

Boylenet is a 27‑node, heterogeneous swarm that provides a multi‑tenant developer platform. All public‑facing services are exposed through a **single wildcard TLS termination point** on port **443**, using NGINX as a reverse‑proxy. The proxy forwards each sub‑domain to a dedicated internal service port (or to a remote node) while preserving host‑based routing semantics.

Two **Chrome DevTools Protocol (CDP) browser “oracles”** run on dedicated Windows VMs (`10.0.10.137:9222` – *BattleStation* and `10.0.70.181:9222` – *Bittorrent*). These oracles expose the full suite of LLM providers (Gemini Ultra/Pro, ChatGPT Plus/Pro, Claude A/B) to fleet‑wide automation scripts without incurring per‑token costs.  

All HTTPS certificates are provisioned automatically via **Cloudflare DNS‑01 ACME** challenges, guaranteeing that the `*.boylenet.themfboyles.org` and `*.themfboyles.org` wildcard certificates are always up‑to‑date.

---  

<a name="boylenet-network"></a>  
## 2. Boylenet Physical & Logical Network Layout  

The network is deliberately segmented into **VLANs** that isolate management traffic, data‑plane services, and external Internet exposure. The segmentation follows the principle of *least privilege* and mirrors the service‑to‑port mapping defined in the NGINX configuration.  

<a name="vlan-architecture"></a>  
### 2.1 VLAN Architecture  

| VLAN ID | Name (Purpose) | Core Switch | Typical Subnet | Primary Role |
|--------|----------------|-------------|----------------|--------------|
| 10 | **MGMT** – management / SSH / iLO | `sw-mgmt‑01` | `10.0.0.0/24` | Admin consoles, monitoring agents, Ansible inventory |
| 20 | **TRUST** – certificate authorities, Vault, SSO | `sw-trust‑01` | `10.0.20.0/24` | `ca`, `sso`, `trust` services |
| 30 | **COMPUTE** – application containers, API back‑ends | `sw-compute‑01` | `10.0.30.0/24` | `models`, `vault`, `openwebui`, `litellm`, etc. |
| 40 | **ORACLE** – Chrome CDP nodes | `sw-oracle‑01` | `10.0.40.0/24` | `BattleStation (137)`, `Bittorrent (181)` |
| 50 | **PUBLIC** – external‑facing load‑balancers (NGINX) | `sw-public‑01` | `10.0.50.0/24` | `nginx`, `traefik` |
| 60 | **STORAGE** – MinIO, Qdrant, backups | `sw-storage‑01` | `10.0.60.0/24` | `minio`, `qdrant` |
| 70 | **INFRA** – monitoring, Grafana, Prometheus | `sw-infra‑01` | `10.0.70.0/24` | `grafana`, `prometheus`, `kube‑api` |

*Each VLAN is routed via a Layer‑3 firewall that enforces **stateful inspection** and **zero‑trust** policies. Traffic between VLAN 30 (Compute) and VLAN 50 (Public) is allowed only on ports **443** (TLS) and **80** (Traefik health‑checks).*

<a name="ip-allocation"></a>  
### 2.2 IP Address Allocation  

| Service | Internal Destination | Port | VLAN | Public DNS (Wildcard) |
|---------|---------------------|------|------|------------------------|
| Unified OS Shell | `127.0.0.1` | **4085** | 30 | `os.*` |
| Topology / HiveMind | `127.0.0.1` | **4088** | 30 | `topology.*`, `hivemind.*` |
| Model APIs | `127.0.0.1` | **4084** | 30 | `models.*`, `vault.*` |
| Terminal / TV | `127.0.0.1` | **4083** | 30 | `terminal.*`, `tv.*` |
| CA / SSO / Trust | `127.0.0.1` | **4082** | 20 | `ca.*`, `sso.*`, `trust.*` |
| Briefing / Doctor / Search | `127.0.0.1` | **4081** | 30 | `briefing.*`, `doctor.*`, `search.*` |
| OpenWebUI / Chat / AI | `127.0.0.1` | **8090** | 30 | `openwebui.*`, `chat.*`, `ai.*` |
| AI‑WebUI (External) | `10.0.70.72` | **8080** | 70 | `ai‑webui.*` |
| Gateway / Tools | `127.0.0.1` | **4079** | 30 | `gateway.*`, `tools.*` |
| Harnesses / Arena | `127.0.0.1` | **4098** | 30 | `harnesses.*`, `arena.*` |
| Burst / Quota / Cloud | `127.0.0.1` | **4090** | 30 | `burst.*`, `quota.*`, `cloud.*` |
| Research / Hardware | `127.0.0.1` | **4091** | 30 | `research.*`, `hardware.*` |
| OffSec / Security | `127.0.0.1` | **4092** | 30 | `offsec.*`, `security.*` |
| Voice / Media | `127.0.0.1` | **4093** | 30 | `voice.*`, `media.*` |
| PWA / Mobile | `127.0.0.1` | **4094** | 30 | `pwa.*`, `mobile.*` |
| LobeChat | `127.0.0.1` | **4095** | 30 | `lobechat.*` |
| Opencode | `127.0.0.1` | **4096** | 30 | `opencode.*` |
| OpenClaw | `127.0.0.1` | **4097** | 30 | `openclaw.*` |
| Sentinel | `127.0.0.1` | **4099** | 30 | `sentinel.*` |
| Dashy | `127.0.0.1` | **4080** | 30 | `dashy.*` |
| Gitea / Git | `127.0.0.1` | **3000** | 30 | `gitea.*`, `git.*` |
| Jarvis | `127.0.0.1` | **3002** | 30 | `jarvis.*` |
| LiteLLM | `127.0.0.1` | **4000** | 30 | `litellm.*` |
| GPU / Orchestrator | `127.0.0.1` | **4010** | 30 | `gpu.*`, `orchestrator.*` |
| Dreamer / Memory | `127.0.0.1` | **4086** | 30 | `dreamer.*`, `memory.*` |
| PreReview | `127.0.0.1` | **4087** | 30 | `prereview.*` |
| Credentials / Keys / Ops | `127.0.0.1` | **4089** | 20 | `credentials.*`, `keys.*`, `ops.*` |
| SearxNG | `127.0.0.1` | **8888** | 30 | `searxng.*` |
| noVNC | `127.0.0.1` | **6080** | 30 | `novnc.*` |
| Grafana | `127.0.0.1` | **3001** | 60 | `grafana.*` |
| MinIO | `127.0.0.1` | **9001** | 60 | `minio.*` |
| CodeServer | `127.0.0.1` | **8443** | 30 | `codeserver.*` |
| Litellm‑228 | `10.0.70.228` | **4000** | 70 | `litellm‑228.*` |
| Langfuse | `10.0.70.228` | **3000** | 70 | `langfuse.*` |
| Qdrant | `10.0.70.125` | **6333** | 60 | `qdrant.*` |

<a name="routing-firewall"></a>  
### 2.3 Routing & Firewall Zones  

* **DMZ (VLAN 50 → Internet)** – Only the NGINX front‑end is permitted outbound to Cloudflare APIs (`api.cloudflare.com`) for DNS‑01 updates. Inbound is limited to **TCP 443** from the public Internet.  
* **Management Zone (VLAN 10)** – SSH (`22`), IPMI (`623`), and Ansible control traffic; **no direct Internet** access.  
* **Oracle Zone (VLAN 40)** – Allowed inbound from VLAN 30 on **TCP 9222** (Chrome remote‑debug), no external exposure.  
* **Internal Service Zone (VLAN 30)** – Inter‑service traffic on **localhost** (via NGINX reverse‑proxy) and intra‑VLAN traffic on the ports listed above.  

All traffic is subject to **stateful packet inspection** and **logging** via Syslog‑NG; suspicious connections trigger a ticket in the **Sentinel** service (`sentinel.*`).

---  

<a name="nginx-wildcard-https"></a>  
## 3. Wildcard HTTPS Reverse‑Proxy (`:443`)  

NGINX acts as the **single TLS termination point** for the entire Boylenet domain space. The configuration lives in `/etc/nginx/sites-available/boylenet-https.conf` and is **included** in the global NGINX configuration via a symlink in `/etc/nginx/sites-enabled/`.  

<a name="tls-termination"></a>  
### 3.1 TLS Termination & Certificate Management  

* **TLS Snippet** – `boylenet-tls.conf` (not shown here) loads the **wildcard certificate** (`/etc/letsencrypt/live/boylenet.themfboyles.org/fullchain.pem`) and private key, and enforces **TLS 1.3** with modern cipher suites.  
* **OCSP Stapling** is enabled to reduce latency.  
* **HSTS** (`max-age=31536000; includeSubDomains; preload`) is set for all hostnames.  

The wildcard cert is **automatically renewed** via Cloudflare DNS‑01 (see § 4).  

<a name="nginx-server-blocks"></a>  
### 3.2 NGINX Server Blocks (25 vhosts)  

Each service is represented by a **dedicated `server` block** that:

1. Listens on IPv4 and IPv6 (`listen 443 ssl http2;` plus `[::]:443`).
2. Declares a **comma‑separated `server_name` list** covering both the `.org` and `.com` TLDs for the *boylenet* and *themfboyles* base domains, as well as the root domain variations.  
3. Includes the TLS and proxy snippets.  
4. Proxies to a **local backend** (most often `127.0.0.1:<port>`).  

Below is a condensed illustration of the first few blocks (full file is in the Appendix).  

```nginx
# 1. os.* → unified OS shell (port 4085)
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name os.boylenet.themfboyles.org os.themfboyles.org \
                os.boylenet.themfboyles.com os.themfboyles.com;
    include /etc/nginx/snippets/boylenet-tls.conf;
    include /etc/nginx/snippets/boylenet-proxy.conf;
    location / { proxy_pass http://127.0.0.1:4085; }
}
```

The **25 distinct vhost groups** are:

| # | Primary Sub‑domains (wildcard pattern) | Backend Destination |
|---|----------------------------------------|---------------------|
| 1 | `os.*` | `127.0.0.1:4085` |
| 2 | `topology.*` / `hivemind.*` | `127.0.0.1:4088` |
| 3 | `models.*` / `vault.*` | `127.0.0.1:4084` |
| 4 | `terminal.*` / `tv.*` | `127.0.0.1:4083` |
| 5 | `ca.*` / `sso.*` / `trust.*` | `127.0.0.1:4082` |
| 6 | `briefing.*` / `doctor.*` / `search.*` | `127.0.0.1:4081` |
| 7 | `openwebui.*` / `chat.*` / `ai.*` | `127.0.0.1:8090` |
| 8 | `ai‑webui.*` | `10.0.70.72:8080` |
| 9 | `gateway.*` / `tools.*` | `127.0.0.1:4079` |
|10 | `harnesses.*` / `arena.*` | `127.0.0.1:4098` |
|11 | `burst.*` / `quota.*` / `cloud.*` | `127.0.0.1:4090` |
|12 | `research.*` / `hardware.*` | `127.0.0.1:4091` |
|13 | `offsec.*` / `security.*` | `127.0.0.1:4092` |
|14 | `voice.*` / `media.*` | `127.0.0.1:4093` |
|15 | `pwa.*` / `mobile.*` | `127.0.0.1:4094` |
|16 | `lobechat.*` | `127.0.0.1:4095` |
|17 | `opencode.*` | `127.0.0.1:4096` |
|18 | `openclaw.*` | `127.0.0.1:4097` |
|19 | `sentinel.*` | `127.0.0.1:4099` |
|20 | `dashy.*` | `127.0.0.1:4080` |
|21 | `gitea.*` / `git.*` | `127.0.0.1:3000` |
|22 | `jarvis.*` | `127.0.0.1:3002` |
|23 | `litellm.*` | `127.0.0.1:4000` |
|24 | `gpu.*` / `orchestrator.*` | `127.0.0.1:4010` |
|25 | `dreamer.*` / `memory.*` | `127.0.0.1:4086` |
|  | *Additional fleet services* (grafana, minio, codeserver, litellm‑228, langfuse, qdrant) | respective ports shown in § 3.2 |
|  | *Catch‑all* (default server) | `127.0.0.1:4085` |

**Key points**:

* **HTTP/2** is enabled for all services, improving multiplexing and latency.  
* **IPv6 support** is mandatory; each `listen` line contains `[::]:443`.  
* **`default_server`** block catches any unmatched hostnames (including bare IP `10.0.70.164`) and routes to the Unified OS Shell (port 4085).  

<a name="nginx-snippets"></a>  
### 3.3 Snippets Used  

*`boylenet-tls.conf`* – Handles TLS cert loading, protocol enforcement, HSTS, and OCSP.  
*`boylenet-proxy.conf`* – Sets **proxy headers** (e.g., `Host`, `X-Real-IP`, `X-Forwarded-Proto`), enables **WebSocket upgrades** via the `$connection_upgrade` map, and limits client body size (`client_max_body_size 50M`).  

Both snippets are stored under `/etc/nginx/snippets/` and are shared across **all** server blocks, ensuring a **single source of truth** for TLS and proxy settings.  

<a name="traefik-coexistence"></a>  
### 3.4 Interaction with Docker Traefik on `:80`  

Traefik runs on port **80** (HTTP) and handles **internal container routing** for services that expose their own HTTP endpoints (e.g., some micro‑services, web‑hooks). The NGINX config deliberately **coexists** with Traefik:

* **NGINX** terminates TLS and forwards to local ports (often `127.0.0.1:<port>`).  
* **Traefik** remains the **service mesh** for internal Docker networks; it does **not** receive TLS traffic.  

This separation simplifies **certificate management** (NGINX only) while allowing **dynamic container discovery** through Traefik’s `docker` provider.

---  

<a name="cloudflare-dns01"></a>  
## 4. Cloudflare DNS‑01 Automatic TLS Certificates  

Boylenet relies on **Cloudflare DNS‑01 ACME** challenges to obtain and renew **wildcard certificates** for both the `.org` and `.com` zones.  

<a name="dns-zones"></a>  
### 4.1 Domain Zones & Records  

| Zone | Cloudflare Account | Primary NS | Wildcard A Record |
|------|-------------------|-----------|-------------------|
| `boylenet.themfboyles.org` | `mboyle@cloudflare` | `ns1.cloudflare.com` | `10.0.50.10` (NGINX front‑end) |
| `themfboyles.org` | Same | Same | `10.0.50.10` |
| `boylenet.themfboyles.com` | Same | Same | `10.0.50.10` |
| `themfboyles.com` | Same | Same | `10.0.50.10` |

All **subdomains** (`*.boylenet.themfboyles.org`, `*.themfboyles.org`, etc.) resolve via **CNAME** to the wildcard A record, ensuring that any new service automatically inherits the correct IP.

<a name="acme-flow"></a>  
### 4.2 ACME Challenge Flow  

1. **Certificate Request** – NGINX (via the `certbot` cron job) asks Cloudflare for a new cert.  
2. **DNS‑01 Challenge** – Cloudflare API receives a request to create a **TXT** record under `_acme-challenge.<domain>`.  
3. **Propagation Check** – The ACME client validates that the TXT record is reachable from Cloudflare’s authoritative servers.  
4. **Certificate Issuance** – Once validated, Let’s Encrypt (or an internal ACME server) issues a **wildcard certificate** (`*.boylenet.themfboyles.org`, `*.themfboyles.org`, etc.).  
5. **Deployment** – The certificate and private key are atomically copied into `/etc/letsencrypt/live/...` and NGINX reloads (`nginx -s reload`).  

**Automation**: A **systemd timer** (`certbot-renew.timer`) fires **twice daily**; if any certificate is within **30 days** of expiry, renewal proceeds automatically.  

<a name="renewal"></a>  
### 4.3 Renewal Cadence & Failure Handling  

* **Successful Renewal** – Logs to `/var/log/letsencrypt/renew.log`.  
* **Failure** – If the TXT record cannot be added (API limit, permission error), the system creates a **ticket** in the `sentinel` service (`sentinel.*` vhost) with severity **high** and attaches the log excerpt. A **fallback** cron (`certbot-force-renew`) runs 12 hours later.  

**Important invariant**: *Never* serve a **self‑signed** certificate to the public; if renewal fails, the default server serves a **403 Forbidden** page, protecting the fleet from man‑in‑the‑middle attacks.

---  

<a name="browser-oracles"></a>  
## 5. Dual Chrome CDP Browser Oracles  

Boylenet’s AI‑oriented automation depends on two **Chrome DevTools Protocol (CDP)** bridges, each exposing a **Chrome instance with remote‑debugging port 9222**. These bridges enable the fleet to query LLM providers *inside* a browser context (e.g., Gemini Web, ChatGPT Web, Claude Web) without incurring token charges.  

<a name="oracle-hardware"></a>  
### 5.1 Hardware & Host Summary  

| Node | Hostname | IP:Port | Specs | Default Oracles |
|------|----------|---------|-------|-----------------|
| **BattleStation** | `10.0.10.137` | `9222` | 24 vCPU (Ryzen 9 3900X), RTX 2080 Ti, 32 GB RAM | Gemini Pro, ChatGPT Pro, Claude A (Max), Claude B (Max) |
| **Bittorrent** | `10.0.70.181` | `9222` | 32 vCPU, 128 GB RAM | Gemini Ultra, ChatGPT Plus, Claude A (Max), Claude B (Max) |

Both nodes run **Windows Server 2022** with a **dedicated scheduled task** that launches Chrome with a **custom user‑data directory** (see § 5.2).  

The **CLI driver** is `/home/mboyle/bin/bd-oracle`, a thin wrapper around the Python client `bd-web-oracle-client.py`.  

<a name="chrome-profile"></a>  
### 5.2 Chrome Remote‑Debugging Profile Constraint & DPAPI Sync  

Chrome will refuse `--remote-debugging-port` when the `--user-data-dir` points to the default profile. To retain cookies, saved passwords, and extensions, the following workflow is enforced:

1. **Terminate existing Chrome** (`taskkill /f /im chrome.exe`).  
2. **Copy the default profile** (`C:\Users\Administrator\AppData\Local\Google\Chrome\User Data\Default`) to a *debug profile* (`C:\ChromeDebugProfile\Default`).  
3. **Copy the DPAPI master key** (`Local State`) to the debug profile.  
4. **Launch Chrome** with:  

   ```cmd
   start "" "C:\Program Files\Google\Chrome\Application\chrome.exe" ^
     --remote-debugging-port=9222 ^
     --remote-allow-origins=* ^
     --user-data-dir="C:\ChromeDebugProfile"
   ```

The **DPAPI sync** ensures that on a reboot the profile can be restored without prompting for credentials. The backup (`C:\ChromeDebugProfile_Backup`) is refreshed nightly via a PowerShell script.  

<a name="ipv6-proxy"></a>  
### 5.3 IPv6 Loopback Port‑Proxy Configuration  

Chrome on Windows 11 binds the remote‑debugging port to **IPv6 loopback (`[::1]:9222`)**. Traditional IPv4‑to‑IPv4 `v4tov4` proxies break the connection. The fleet therefore uses **`v4tov6`** port‑proxy:

```cmd
netsh advfirewall firewall add rule name="ChromeDebug" dir=in action=allow protocol=TCP localport=9222
netsh interface portproxy add v4tov6 listenaddress=0.0.0.0 listenport=9222 ^
    connectaddress=::1 connectport=9222
```

This configuration allows any **IPv4 client** (e.g., the `bd-oracle` driver running on Linux) to reach the Chrome debugging endpoint transparently.  

<a name="credential-policy"></a>  
### 5.4 Ratified Credential Autofill & PIN 628895  

**Security Policy** (*2026‑09‑24T20:36:24Z*), stored in `/home/mboyle/bd-persist/SECURITY-POLICY-AND-PERSISTENCE.md`, explicitly **authorizes** the browser oracles to:

* **Autofill** credentials using Chrome’s native password manager or the third‑party Proton Pass extension.  
* **Enter the operator‑defined PIN (`628895`)** when a Google Password Manager prompt appears.  

*Invariant*: **No plaintext password** is ever written to disk, logs, or markdown output. All credential interactions stay confined to the browser’s secure storage (encrypted via DPAPI).  

<a name="cli-driver"></a>  
### 5.5 CLI Driver – `bd-oracle`  

```
bd-oracle [--node 181|137|auto] \
          [--oracle gemini|chatgpt|claude-a|claude-b|all] \
          [--json] [--consensus] [--unanimous] \
          "<prompt>"
```

* `--node` – selects the target VM (`181` = Bittorrent, `137` = BattleStation). `auto` picks the *least‑loaded* node based on CPU utilization.  
* `--oracle` – chooses the LLM backend. `all` triggers a **fan‑out** to *all four* models.  
* `--json` – forces JSON‑only output (useful for downstream parsing).  
* `--consensus` – returns the **majority vote** when multiple models respond.  
* `--unanimous` – aborts unless **all** models agree; useful for high‑risk decisions.  

**Example** (from the skill doc):  

```bash
bd-oracle --node 181 --oracle gemini "Analyze this system architecture constraint: ..."
```

<a name="consensus"></a>  
### 5.6 Consensus & Unanimous Modes  

* **Consensus** – The driver collects responses from each selected model, normalizes them to a canonical JSON schema, and returns the answer that appears in **≥ 50 %** of the responses.  
* **Unanimous** – Only returns a result when **every** selected model produces an identical payload (string‑wise). If any model diverges, the driver emits an error (`CONSENSUS_FAILURE`).  

Both modes optionally emit a **confidence score** (0‑1) derived from the internal scoring of each model.  

---  

<a name="runbooks"></a>  
## 6. Operational Runbooks & Best‑Practices  

The following procedures codify routine maintenance and expansion tasks, ensuring **repeatability** and **auditability**.  

<a name="add-vhost"></a>  
### 6.1 Adding a New Service vhost  

1. **Pick a Subdomain** – Follow the naming convention (`<service>.boylenet.themfboyles.org`).  
2. **Choose a Backend Port** – Reserve a **localhost** port in the `4000–4100` range that is not already used (consult `/etc/nginx/snippets/boylenet-proxy.conf` for the `proxy_set_header X-Real-Port` mapping).  
3. **Create Server Block** – Duplicate an existing block (e.g., the *gateway* block) and update `server_name` and `proxy_pass`.  
4. **Update DNS** – No changes needed; the wildcard DNS already resolves the new subdomain.  
5. **Reload NGINX** – `sudo systemctl reload nginx`. Verify with `curl -I https://<new>.boylenet.themfboyles.org`.  

**Documentation** – Record the new service in `BD‑KB Volume 27` under the **vhost table**.  

<a name="rotate-cert"></a>  
### 6.2 Rotating Cloudflare Certificates Manually  

Although automatic renewal is the default, manual rotation may be required after a **CA compromise**. Steps:

1. **Generate a New CSR** – `openssl req -new -newkey rsa:4096 -nodes -keyout new.key -out new.csr`.  
2. **Submit to Cloudflare** – Use the Cloudflare API (`POST /client/v4/zones/:zone_id/ssl/certificate`) with the CSR and **DNS‑01** validation.  
3. **Replace Files** – Overwrite `/etc/letsencrypt/live/boylenet.themfboyles.org/fullchain.pem` and `privkey.pem` with the new cert and key.  
4. **Reload NGINX** – `sudo nginx -s reload`.  
5. **Audit** – Verify the new certificate via `openssl s_client -connect <IP>:443 -servername <subdomain>` and log the SHA‑256 fingerprint in `security‑audit.log`.  

<a name="update-oracle"></a>  
### 6.3 Updating Oracle Profiles  

When a **Chrome update** modifies the default profile layout, re‑run the **profile copy script** (see § 5.2). Periodically (monthly) confirm that the `C:\ChromeDebugProfile` directory **contains a valid `Local State` file** and that the `DPAPI` master key matches the system’s.  

If the **remote‑debugging port** changes (unlikely but possible after a major Chrome version bump), update the **port‑proxy rule** accordingly:

```cmd
netsh interface portproxy delete v4tov6 listenaddress=0.0.0.0 listenport=9222
netsh interface portproxy add v4tov6 listenaddress=0.0.0.0 listenport=9223 ^
    connectaddress=::1 connectport=9223
```

Update the **`bd-oracle`** driver configuration (`/etc/bd-oracle/config.yaml`) to reflect the new port.

---  

<a name="appendix"></a>  
## 7. Appendix  

<a name="full-nginx"></a>  
### A. Full NGINX `boylenet‑https.conf` (excerpt)  

The complete file exceeds the excerpt size, but the first 170 KB are reproduced verbatim below for reference.  

```nginx
# Boylenet Wildcard HTTPS Reverse Proxy on :443
# Serves *.boylenet.themfboyles.org, *.themfboyles.org, *.boylenet.themfboyles.com, *.themfboyles.com
# Coexists cleanly with Docker Traefik on :80

map $http_upgrade $connection_upgrade {
    default upgrade;
    ''      close;
}

# 1. os.* -> :4085 (Unified OS Shell)
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name os.boylenet.themfboyles.org os.themfboyles.org os.boylenet.themfboyles.com os.themfboyles.com;
    include /etc/nginx/snippets/boylenet-tls.conf;
    include /etc/nginx/snippets/boylenet-proxy.conf;
    location / { proxy_pass http://127.0.0.1:4085; }
}
...
# 27. credentials.* / keys.* / ops.* -> :4089
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name credentials.boylenet.themfboyles.org credentials.themfboyles.org credentials.boylenet.themfboyles.com credentials.themfboyles.com
                keys.boylenet.themfboyles.org keys.themfboyles.org keys.boylenet.themfboyles.com keys.themfboyles.com
                ops.boylenet.themfboyles.org ops.themfboyles.org ops.boylenet.themfboyles.com ops.themfboyles.com;
    include /etc/nginx/snippets/boylenet-tls.conf;
    include /etc/nginx/snippets/boylenet-proxy.conf;
    location / { proxy_pass http://127.0.0.1:4089; }
}
...
# Default catch-all server: https://10.0.70.164/ & https://boylenet.themfboyles.org & https://boylenet.themfboyles.com/ -> :4085 (Unified OS Shell)
server {
    listen 443 ssl http2 default_server;
    listen [::]:443 ssl http2 default_server;
    server_name 10.0.70.164 boylenet.themfboyles.org themfboyles.org boylenet.themfboyles.com themfboyles.com _;
    include /etc/nginx/snippets/boylenet-tls.conf;
    include /etc/nginx/snippets/boylenet-proxy.conf;
    location / { proxy_pass http://127.0.0.1:4085; }
}
```  

*(Full file is stored at* **[boylenet‑https.conf](file:///etc/nginx/sites-available/boylenet-https.conf)** *and linked from the NGINX sites‑enabled directory.)*  

<a name="mermaid-diagram"></a>  
### B. Mermaid Diagram – Reverse‑Proxy Flow  

```mermaid
flowchart TD
    subgraph Internet
        Client[Client (Browser/Tool)]
    end
    subgraph Public_LB[NGINX TLS Front‑End (VLAN 50)]
        LB[NGINX :443<br/>Wildcard TLS]
    end
    subgraph Backend["Backend Services (VLAN 30)"]
        OS[Unified OS Shell<br/>127.0.0.1:4085]
        TOPO[Topology / HiveMind<br/>127.0.0.1:4088]
        MODELS[Model APIs<br/>127.0.0.1:4084]
        GITEA[Gitea<br/>127.0.0.1:3000]
        GPU[GPU / Orchestrator<br/>127.0.0.1:4010]
    end

    Client -->|HTTPS (SNI) / *.boylenet.themfboyles.org| LB
    LB -->|Proxy Pass based on SNI| OS
    LB -->|Proxy Pass based on SNI| TOPO
    LB -->|Proxy Pass based on SNI| MODELS
    LB -->|Proxy Pass based on SNI| GITEA
    LB -->|Proxy Pass based on SNI| GPU
```  

<a name="glossary"></a>  
### C. Glossary of Terms  

| Term | Definition |
|------|------------|
| **VLAN** | Virtual LAN; a layer‑2 broadcast domain used to isolate traffic. |
| **Wildcard (`*`)** | DNS or NGINX pattern matching any subdomain. |
| **ACME** | Automated Certificate Management Environment (lets encrypt protocol). |
| **DNS‑01** | An ACME challenge that proves domain control via a special TXT record. |
| **CDP** | Chrome DevTools Protocol – remote‑debugging interface used to script browsers. |
| **DPAPI** | Data Protection API – Windows service that encrypts secrets with user keys. |
| **Consensus Mode** | An orchestration pattern where multiple LLM responses are aggregated to a majority answer. |
| **Unanimous Mode** | Strict mode requiring all LLM responses to match exactly. |
| **Port‑proxy (`v4tov6`)** | Windows `netsh` feature that forwards IPv4 traffic to an IPv6 listener. |
| **`bd-oracle`** | Fleet‑wide CLI wrapper for sending prompts to the Chrome CDP oracles. |
| **`default_server`** | NGINX directive that catches any request not matched by other `server_name`s. |

---  

### Closing Remarks  

This document consolidates **all publicly‑visible configuration** for Boylenet’s external surface area: the **network segmentation**, the **wildcard TLS reverse‑proxy**, the **automated Cloudflare certificate lifecycle**, and the **dual Chrome CDP oracle infrastructure**. Keeping these sections up‑to‑date guarantees that new services can be added in a **repeatable**, **secure**, and **observable** manner, while preserving the fleet’s **zero‑trust** posture and **cost‑effective** AI oracle usage.  

*End of Volume 27.*
