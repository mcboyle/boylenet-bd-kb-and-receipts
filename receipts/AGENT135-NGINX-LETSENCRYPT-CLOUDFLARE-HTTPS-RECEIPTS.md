# AGENT-135: NGINX `:443` + Let's Encrypt Cloudflare DNS-01 Wildcard HTTPS Verification Receipt

- **Written At**: `2026-10-19T00:07:35Z`
- **Agent**: `Agent-135` (NGINX + Let's Encrypt + Cloudflare DNS-01 Wildcard HTTPS Architect)
- **Target Host**: `10.0.70.164` (`hub-mesh01` / controller)
- **Live Log Stream**: `/var/tmp/bd-headless-streams/agent135-nginx-letsencrypt-cloudflare-https.log`
- **Benchmarking Invariant**: `.72` (`10.0.70.72` `bench_worker.py` / `llama-bench`) untouched (0 processes/files touched on `.72`).

---

## 1. NGINX `:443` + Docker Traefik `:80` Coexistence

- **Installed Packages on `.164`**:
  - `nginx/1.24.0 (Ubuntu)`
  - `certbot 2.9.0`
  - `python3-certbot-dns-cloudflare`
- **Port Allocation**:
  - `0.0.0.0:80` / `[::]:80` -> Docker `traefik` (`pid 7358`, untouched)
  - `0.0.0.0:443` / `[::]:443` -> `nginx.service` (`listen 443 ssl http2;`, `/etc/nginx/sites-enabled/default` removed)
- **Systemd Auto-Heal Drop-in**:
  - `/etc/systemd/system/nginx.service.d/override.conf` (`Restart=always`, `RestartSec=3`)
- **Config & Snippets**:
  - `/etc/nginx/sites-available/boylenet-https.conf` (symlinked in `/etc/nginx/sites-enabled/boylenet-https.conf`)
  - `/etc/nginx/snippets/boylenet-tls.conf` (TLS 1.2/1.3, modern ECDHE-ECDSA/RSA GCM & ChaCha20-Poly1305 ciphers)
  - `/etc/nginx/snippets/boylenet-proxy.conf` (WebSocket `Upgrade`/`Connection` map, `client_max_body_size 500M;`, `proxy_read_timeout 3600s;`, `HEAD -> GET` upstream method map for Python `BaseHTTPRequestHandler` compatibility)
  - `/etc/nginx/conf.d/boylenet-extra-vhosts.conf` (Traefik parity subdomains)

---

## 2. Live Public Let's Encrypt Wildcard Certificate + Boylenet Root CA SAN Wildcard

- **Active Public Let's Encrypt Certificate (Cloudflare DNS-01)**:
  - **Path**: `/etc/letsencrypt/live/themfboyles.org/fullchain.pem` & `/etc/letsencrypt/live/boylenet.themfboyles.com/fullchain.pem`
  - **Subject**: `CN = themfboyles.org`
  - **Issuer**: `C = US, O = Let's Encrypt, CN = YE1`
  - **Validity**: `Oct 18 23:08:28 2026 GMT` -> `Jan 16 23:08:27 2027 GMT`
  - **SANs**: `DNS:*.boylenet.themfboyles.org, DNS:*.themfboyles.org, DNS:themfboyles.org`
  - **Cloudflare DNS-01 Credentials**: `/etc/letsencrypt/cloudflare.ini` (`chmod 600`, token `<REDACTED_CF_PASSWORD>`)
  - **Auto-Renewal**: `certbot.timer` enabled and active + `/etc/letsencrypt/renewal-hooks/deploy/reload-nginx.sh` (`chmod +x`)
- **Boylenet Internal Root CA Multi-Domain + IP Wildcard Certificate**:
  - **Path**: `/home/mboyle/infra/ca/boylenet-wildcard.crt` & `/home/mboyle/infra/ca/boylenet-wildcard.key`
  - **SANs (16)**: `DNS:themfboyles.org, DNS:*.themfboyles.org, DNS:boylenet.themfboyles.org, DNS:*.boylenet.themfboyles.org, DNS:themfboyles.com, DNS:*.themfboyles.com, DNS:boylenet.themfboyles.com, DNS:*.boylenet.themfboyles.com, DNS:*.local, DNS:localhost, IP:10.0.70.164, IP:10.0.70.72, IP:10.0.70.83, IP:10.0.70.125, IP:10.0.70.228, IP:127.0.0.1`

---

## 3. CLI Tool & Portal 1-Click Endpoints (`:4082` & `:4087`)

- **CLI Provisioner & Status Tool**: `/home/mboyle/bin/bd-letsencrypt-cloudflare` (`chmod +x`)
  - `bd-letsencrypt-cloudflare --status [--json]`
  - `bd-letsencrypt-cloudflare --set-token <CF_API_TOKEN> [--domain themfboyles.org]`
  - `bd-letsencrypt-cloudflare --ensure-hooks`
- **Portal API & UI Cards**:
  - `:4082` (`/home/mboyle/bin/bd-trust-and-sso-portal.py`): `GET /api/https-status`, `POST /api/letsencrypt-cloudflare`, and live 1-Click Cloudflare Let's Encrypt DNS-01 UI Card in `#ux10-root-ca`.
  - `:4087` (`/home/mboyle/bin/bd-credentials-vault.py`): `GET /api/https-status`, `POST /api/letsencrypt-cloudflare`, and live 1-Click Cloudflare Let's Encrypt DNS-01 UI Toolbar Card.

---

## 4. Full 52/52 Virtual Host `https://` Route Verification (`HTTP/2 200`)

All 52 `*.boylenet.themfboyles.org`, `*.themfboyles.org`, `*.boylenet.themfboyles.com`, and `*.themfboyles.com` virtual hosts on `10.0.70.164:443` verified via `curl -skI` (and strict `curl -sI` without `-k` on `.org` domains):

| Virtual Host (`:443` HTTPS) | Upstream Target | Status |
|---|---|---|
| `https://os.boylenet.themfboyles.org/` | `http://127.0.0.1:4085` | `HTTP/2 200` (Strict TLS Verified) |
| `https://os.boylenet.themfboyles.com/` | `http://127.0.0.1:4085` | `HTTP/2 200` |
| `https://os.themfboyles.org/` | `http://127.0.0.1:4085` | `HTTP/2 200` (Strict TLS Verified) |
| `https://topology.boylenet.themfboyles.org/` | `http://127.0.0.1:4088` | `HTTP/2 200` |
| `https://hivemind.boylenet.themfboyles.com/` | `http://127.0.0.1:4088` | `HTTP/2 200` |
| `https://models.boylenet.themfboyles.org/` | `http://127.0.0.1:4084` | `HTTP/2 200` |
| `https://vault.boylenet.themfboyles.com/` | `http://127.0.0.1:4084` | `HTTP/2 200` |
| `https://terminal.boylenet.themfboyles.org/` | `http://127.0.0.1:4083` | `HTTP/2 200` |
| `https://tv.boylenet.themfboyles.com/` | `http://127.0.0.1:4083` | `HTTP/2 200` |
| `https://ca.boylenet.themfboyles.org/` | `http://127.0.0.1:4082` | `HTTP/2 200` (Strict TLS Verified) |
| `https://sso.boylenet.themfboyles.com/` | `http://127.0.0.1:4082` | `HTTP/2 200` |
| `https://trust.boylenet.themfboyles.org/` | `http://127.0.0.1:4082` | `HTTP/2 200` |
| `https://briefing.boylenet.themfboyles.org/` | `http://127.0.0.1:4081` | `HTTP/2 200` |
| `https://doctor.boylenet.themfboyles.com/` | `http://127.0.0.1:4081` | `HTTP/2 200` |
| `https://search.boylenet.themfboyles.org/` | `http://127.0.0.1:4081` | `HTTP/2 200` |
| `https://openwebui.boylenet.themfboyles.org/` | `http://127.0.0.1:8090` | `HTTP/2 200` (Strict TLS Verified) |
| `https://chat.boylenet.themfboyles.com/` | `http://127.0.0.1:8090` | `HTTP/2 200` |
| `https://ai.boylenet.themfboyles.org/` | `http://127.0.0.1:8090` | `HTTP/2 200` |
| `https://ai-webui.boylenet.themfboyles.org/` | `http://10.0.70.72:8080` | `HTTP/2 200` |
| `https://gateway.boylenet.themfboyles.org/` | `http://127.0.0.1:4079` | `HTTP/2 200` |
| `https://tools.boylenet.themfboyles.com/` | `http://127.0.0.1:4079` | `HTTP/2 200` |
| `https://harnesses.boylenet.themfboyles.org/` | `http://127.0.0.1:4098` | `HTTP/2 200` |
| `https://arena.boylenet.themfboyles.com/` | `http://127.0.0.1:4098` | `HTTP/2 200` |
| `https://burst.boylenet.themfboyles.org/` | `http://127.0.0.1:4090` | `HTTP/2 200` |
| `https://quota.boylenet.themfboyles.com/` | `http://127.0.0.1:4090` | `HTTP/2 200` |
| `https://research.boylenet.themfboyles.org/` | `http://127.0.0.1:4091` | `HTTP/2 200` |
| `https://offsec.boylenet.themfboyles.org/` | `http://127.0.0.1:4092` | `HTTP/2 200` |
| `https://security.boylenet.themfboyles.com/` | `http://127.0.0.1:4092` | `HTTP/2 200` |
| `https://voice.boylenet.themfboyles.org/` | `http://127.0.0.1:4093` | `HTTP/2 200` |
| `https://media.boylenet.themfboyles.com/` | `http://127.0.0.1:4093` | `HTTP/2 200` |
| `https://pwa.boylenet.themfboyles.org/` | `http://127.0.0.1:4094` | `HTTP/2 200` |
| `https://mobile.boylenet.themfboyles.com/` | `http://127.0.0.1:4094` | `HTTP/2 200` |
| `https://lobechat.boylenet.themfboyles.org/` | `http://127.0.0.1:4095` | `HTTP/2 200` |
| `https://opencode.boylenet.themfboyles.org/` | `http://127.0.0.1:4096` | `HTTP/2 200` |
| `https://openclaw.boylenet.themfboyles.org/` | `http://127.0.0.1:4097` | `HTTP/2 200` |
| `https://sentinel.boylenet.themfboyles.org/` | `http://127.0.0.1:4099` | `HTTP/2 200` |
| `https://dashy.boylenet.themfboyles.org/` | `http://127.0.0.1:4080` | `HTTP/2 200` |
| `https://gitea.boylenet.themfboyles.org/` | `http://127.0.0.1:3000` | `HTTP/2 200` |
| `https://git.boylenet.themfboyles.com/` | `http://127.0.0.1:3000` | `HTTP/2 200` |
| `https://jarvis.boylenet.themfboyles.org/` | `http://127.0.0.1:3002` | `HTTP/2 200` |
| `https://litellm.boylenet.themfboyles.org/` | `http://127.0.0.1:4000` | `HTTP/2 200` |
| `https://gpu.boylenet.themfboyles.org/` | `http://127.0.0.1:4010` | `HTTP/2 200` |
| `https://orchestrator.boylenet.themfboyles.com/` | `http://127.0.0.1:4010` | `HTTP/2 200` |
| `https://dreamer.boylenet.themfboyles.org/` | `http://127.0.0.1:4086` | `HTTP/2 200` |
| `https://memory.boylenet.themfboyles.com/` | `http://127.0.0.1:4086` | `HTTP/2 200` |
| `https://prereview.boylenet.themfboyles.org/` | `http://127.0.0.1:4087` | `HTTP/2 200` |
| `https://credentials.boylenet.themfboyles.org/` | `http://127.0.0.1:4089` | `HTTP/2 200` |
| `https://keys.boylenet.themfboyles.com/` | `http://127.0.0.1:4089` | `HTTP/2 200` |
| `https://searxng.boylenet.themfboyles.org/` | `http://127.0.0.1:8888` | `HTTP/2 200` |
| `https://novnc.boylenet.themfboyles.org/` | `http://127.0.0.1:6080` | `HTTP/2 200` |
| `https://boylenet.themfboyles.org/` | `http://127.0.0.1:4085` | `HTTP/2 200` |
| `https://boylenet.themfboyles.com/` | `http://127.0.0.1:4085` | `HTTP/2 200` |
