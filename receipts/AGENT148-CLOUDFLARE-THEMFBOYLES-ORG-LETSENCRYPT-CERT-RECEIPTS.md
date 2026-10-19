# AGENT-148: Cloudflare `themfboyles.org` API Token, Zone DNS & Real Let's Encrypt Wildcard Certificate Receipt

- **Timestamp (UTC)**: `2026-10-19T00:08:23Z`
- **Agent**: `Agent-148`
- **Stream Log**: `/var/tmp/bd-headless-streams/agent148-cloudflare-themfboyles-org-letsencrypt.log`
- **Cloudflare Account ID**: `4146bc032b20f07fb1cde192559b5283`
- **Cloudflare Zone**: `themfboyles.org` (`54070477d7394b4a371ad2ee4ff221f5`, status: `active`)

## 1. Cloudflare Scoped DNS-01 Token & Credentials

- Minted scoped Cloudflare API Token (`Edit zone DNS`, Token ID `964009e77b6da859a5773bad2bc18ac8`, status `active`) via CDP hardware mouse automation on `10.0.10.137:9222` (`https://dash.cloudflare.com/profile/api-tokens`).
- Verified via `GET https://api.cloudflare.com/client/v4/user/tokens/verify` (`success: true`, `status: "active"`).
- Stored at `/etc/letsencrypt/cloudflare.ini` (`chmod 600`, `root:root`, value `<REDACTED_CF_API_TOKEN>`).

## 2. Cloudflare DNS Records Provisioned (`themfboyles.org`)

| Record ID | Type | Name | Content | Proxied | TTL | Action |
|---|---|---|---|---|---|---|
| `a58738ffbfd003e71c7bc8a221264c70` | `A` | `themfboyles.org` | `10.0.70.164` | `false` (DNS only) | Auto (`1`) | Created |
| `c4a3dec62b9b03eb8e1efcda9f7b2ce1` | `A` | `*.themfboyles.org` | `10.0.70.164` | `false` (DNS only) | Auto (`1`) | Created |
| `72566db0e0541bd3276f91af8da60fb8` | `A` | `boylenet.themfboyles.org` | `10.0.70.164` | `false` (DNS only) | Auto (`1`) | Created |
| `19d428a8242ac3405a2f957db6f90bc2` | `A` | `*.boylenet.themfboyles.org` | `10.0.70.164` | `false` (DNS only) | Auto (`1`) | Created |

*(Existing `CNAME fitness.themfboyles.org` -> `c91289f8-dd58-4d77-af5e-0ce072d1ca8d.cfargotunnel.com` preserved untouched.)*

## 3. Real Let's Encrypt Wildcard Certificate Issued (`certbot --dns-cloudflare`)

- Command executed:
  ```bash
  sudo certbot certonly \
    --dns-cloudflare \
    --dns-cloudflare-credentials /etc/letsencrypt/cloudflare.ini \
    --dns-cloudflare-propagation-seconds 20 \
    --non-interactive \
    --agree-tos \
    --register-unsafely-without-email \
    --cert-name themfboyles.org \
    -d "themfboyles.org" \
    -d "*.themfboyles.org" \
    -d "*.boylenet.themfboyles.org"
  ```
  *(Note: `*.themfboyles.org` natively covers `boylenet.themfboyles.org` per ACME specification.)*
- Certificate paths:
  - Fullchain: `/etc/letsencrypt/live/themfboyles.org/fullchain.pem`
  - Private Key: `/etc/letsencrypt/live/themfboyles.org/privkey.pem`
  - Renewal deploy hook: `/etc/letsencrypt/renewal-hooks/deploy/reload-nginx.sh`
  - NGINX TLS snippet updated: `/etc/nginx/snippets/boylenet-tls.conf`

## 4. Live `:443` OpenSSL & Strict CA `curl` Verification

```text
$ echo | openssl s_client -connect 127.0.0.1:443 -servername os.boylenet.themfboyles.org 2>/dev/null | openssl x509 -noout -subject -issuer -dates -ext subjectAltName
subject=CN = themfboyles.org
issuer=C = US, O = Let's Encrypt, CN = YE1
notBefore=Oct 18 23:08:28 2026 GMT
notAfter=Jan 16 23:08:27 2027 GMT
X509v3 Subject Alternative Name: 
    DNS:*.boylenet.themfboyles.org, DNS:*.themfboyles.org, DNS:themfboyles.org
```

Strict TLS verification (no `-k` / `--insecure`):
- `https://os.boylenet.themfboyles.org/` -> `HTTP/2 200`
- `https://terminal.boylenet.themfboyles.org/` -> `HTTP/2 200`
- `https://models.boylenet.themfboyles.org/` -> `HTTP/2 200`
- `https://topology.boylenet.themfboyles.org/` -> `HTTP/2 200`
- `https://openwebui.boylenet.themfboyles.org/` -> `HTTP/2 200`
- `https://os.themfboyles.org/` -> `HTTP/2 200`
- `https://boylenet.themfboyles.org/` -> `HTTP/2 200`
