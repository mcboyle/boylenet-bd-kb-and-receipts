# Agent-76 Receipts — UI/UX Super-Swarm Pillar 2: Zero-Friction Auto-Auth, QR Mobile Handoff, Companion Bookmarklet & Local Wildcard HTTPS Root CA (`:4082` `#UX-6..10`)

- **Written-At**: `2026-10-18T17:04:00Z`
- **Agent**: `Agent-76` (UI/UX Super-Swarm — Pillar 2 `#UX-6..10`)
- **Service**: `bd-trust-and-sso-portal.service` (`0.0.0.0:4082`)
- **Ingress Domains**:
  - `http://ca.boylenet.themfboyles.com` (`http://10.0.70.164:4082`)
  - `http://sso.boylenet.themfboyles.com` (`http://10.0.70.164:4082`)

---

## 1. Boylenet Internal Root CA & Wildcard TLS Certificate (`#UX-10`)

Generated PKI hierarchy in `/home/mboyle/infra/ca/` and synced into Traefik dynamic TLS store (`/home/mboyle/infra/expansion-layer/dynamic/certs/`):

| Artifact | Path | Details |
|---|---|---|
| **Root CA Certificate** | `/home/mboyle/infra/ca/boylenet-root-ca.crt` | `CN=Boylenet Swarm OS Internal Root CA`, 4096-bit RSA, 10-year validity (`2026-10-17` → `2036-10-16`), SHA-256 `D3:45:1E:FC:22:07:BD:E1:7A:4A:D6:13:16:B5:82:84:51:DC:F6:27:53:CE:56:9F:9B:98:0B:87:B0:D0:48:3D` |
| **Root CA Private Key** | `/home/mboyle/infra/ca/boylenet-root-ca.key` | `0600` permissions |
| **Wildcard TLS Certificate** | `/home/mboyle/infra/ca/boylenet-wildcard.crt` | `CN=*.boylenet.themfboyles.com`, signed by Root CA (`openssl verify: OK`), SANs: `DNS:*.boylenet.themfboyles.com, DNS:boylenet.themfboyles.com, DNS:*.local, DNS:localhost, IP:10.0.70.164, IP:10.0.70.72, IP:10.0.70.83, IP:10.0.70.125, IP:10.0.70.228, IP:127.0.0.1` |
| **Wildcard Private Key** | `/home/mboyle/infra/ca/boylenet-wildcard.key` | `0600` permissions |
| **Apple iOS/iPadOS Profile** | `/home/mboyle/infra/ca/boylenet-root-ca.mobileconfig` | 1-Tap Apple Configuration Profile (`com.themfboyles.boylenet.ca.profile`) for iPhone/iPad (`Boylenet Mobile`) |
| **Ubuntu Trust Store (`.164`)** | `/usr/local/share/ca-certificates/boylenet-root-ca.crt` | Installed via `sudo update-ca-certificates` (`1 added, 0 removed; done.`) |

---

## 2. Boylenet Zero-Friction SSO, QR Handoff & Root CA Portal (`:4082` — `#UX-6..10`)

Implemented `/home/mboyle/bin/bd-trust-and-sso-portal.py` (`bd-trust-and-sso-portal.service`):

### `#UX-7` 1-Click Auto-Login Launchpad (All 8 Live Handshakes Verified)
Each button triggers `/sso/login/<target>`, performing a live server-side authentication handshake and returning an auto-submitting browser trampoline (`Set-Cookie` on `.boylenet.themfboyles.com` + native POST / token redirect):

| Button Label | Endpoint | Live Handshake Verification (`?mode=json`) |
|---|---|---|
| **⚡ Auto-Login LiteLLM (.164)** | `/sso/login/litellm-164` | `verified=True`, `HTTP 303`, mints `token=eyJ...` (`proxy_admin` JWT cookie) |
| **⚡ Auto-Login LiteLLM (.228)** | `/sso/login/litellm-228` | `verified=True`, `HTTP 303`, mints `token=eyJ...` (`proxy_admin` JWT cookie) |
| **⚡ Auto-Login OpenWebUI (:8090)** | `/sso/login/openwebui` | `verified=True`, `HTTP 200`, `webui_auth=false_direct_entry` |
| **⚡ Auto-Login Gitea (:3000)** | `/sso/login/gitea` | `verified=True`, `HTTP 200`, `gitea_user=mboyle` + auto-POST `/user/login` |
| **⚡ Auto-Login Grafana (:3001)** | `/sso/login/grafana` | `verified=True`, `HTTP 200`, mints `grafana_session=...` cookie |
| **⚡ Auto-Login MinIO (:9001)** | `/sso/login/minio` | `verified=True`, `HTTP 204`, mints S3 Console `token=...` JWT cookie |
| **⚡ Auto-Login Langfuse (.228:3000)** | `/sso/login/langfuse` | `verified=True`, `HTTP 200`, negotiates NextAuth `csrfToken` |
| **⚡ Auto-Login OpenClaw (:4097)** | `/sso/login/openclaw` | `verified=True`, `HTTP 200`, injects gateway password & token |

### `#UX-9` Live QR Code Mobile Handoff Matrix
- Built a zero-dependency pure-Python ISO/IEC 18004 Reed-Solomon GF(256) SVG QR generator (`/api/qr?url=...`) rendering instant crisp SVG QR codes for:
  - `http://mobile.boylenet.themfboyles.com`
  - `http://os.boylenet.themfboyles.com`
  - `http://topology.boylenet.themfboyles.com`
  - `http://dashy.boylenet.themfboyles.com`
  - `http://ca.boylenet.themfboyles.com/download/boylenet-root-ca.mobileconfig`
  - Active Claude Remote Control session URL (`https://claude.ai/code/session_01XfKMnHmQPiwqmCnqwnZSvy`) + interactive custom QR input bar.

### `#UX-8` Boylenet Companion Bookmarklet & Unpacked Chrome Extension
- Drag-to-bookmarks-bar **"⚡ Boylenet Auto-Fill & Dispatch"** bookmarklet (`javascript:(function(){...})()`).
- Dynamic Manifest V3 unpacked Chrome Extension bundle at `/download/boylenet-chrome-ext.zip` (`HTTP 200`, `application/zip`, `2084 bytes`).

### `#UX-10` 1-Click Root CA Downloaders
- `/download/boylenet-root-ca.crt` (`HTTP 200`, `application/x-x509-ca-cert`, `2053 bytes`)
- `/download/boylenet-root-ca.mobileconfig` (`HTTP 200`, `application/x-apple-aspen-config`, `3685 bytes`)
- `/download/boylenet-wildcard.crt` (`HTTP 200`, `application/x-x509-ca-cert`)
- `/download/install-ca-windows.ps1` + copyable 1-liner `Import-Certificate` PowerShell command for `BattleStation` (`.137`) & `Bittorrent` (`.181`).
