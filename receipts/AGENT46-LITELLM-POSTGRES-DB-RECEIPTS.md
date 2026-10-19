# Agent-46 Receipt: LiteLLM PostgreSQL Database & Admin UI Provisioning (`.164:4000` & `.228:4000`)

**WRITTEN-AT**: `2026-10-18T14:30:30Z`
**STATUS**: `PASS (0 "Not connected to DB!" errors; 77 Prisma tables synced; HTTP 200 on /v2/login, /v2/user/info, /v2/model/info, /key/list, /v1/models)`

---

## 1. Root Cause of `"Authentication Error, Not connected to DB!"`

1. **Missing `DATABASE_URL`**: Both `litellm-proxy` on `10.0.70.164:4000` and `litellm-proxy` on `10.0.70.228:4000` were running `ghcr.io/berriai/litellm:main-latest` without `DATABASE_URL` in `/home/mboyle/infra/support-layer/litellm/.env` or `general_settings.database_url` in `config.yaml`. When `/v2/login` ran `user_update()` and `generate_key_helper_fn()`, `prisma_client` was `None`, raising `Exception("Not connected to DB!")`.
2. **Incomplete `custom_auth.py` Principal**: `/home/mboyle/infra/support-layer/litellm/custom_auth.py` previously returned `UserAPIKeyAuth(api_key=api_key or "sk-boylenet-local")` with `user_id=None` and `user_role=None`, which caused Admin UI endpoints (`/v2/user/info`, `/user/list`, `/config/list`) to reject requests even with a database connected.
3. **Docker Bridge PostgreSQL HBA Rule**: `/etc/postgresql/16/main/pg_hba.conf` on `10.0.70.164` lacked an entry for `172.16.0.0/12`, blocking Docker bridge containers (`172.17.0.x`) on `.164` from authenticating to `10.0.70.164:5432`.

---

## 2. Remedies & Provisioning

1. **PostgreSQL 16 (`10.0.70.164:5432`)**:
   - Added `host all all 172.16.0.0/12 scram-sha-256` to `/etc/postgresql/16/main/pg_hba.conf` and reloaded PostgreSQL (`SELECT pg_reload_conf();`).
   - Created role `litellm` with password `boylenet-admin` (`SUPERUSER CREATEDB LOGIN`).
   - Created databases `litellm` (shared primary DB for `.164` and `.228`) and `litellm_228` owned by `litellm`.
   - Synced all **77 Prisma tables** (`LiteLLM_UserTable`, `LiteLLM_VerificationToken`, `LiteLLM_TeamTable`, `LiteLLM_SpendLogs`, `LiteLLM_ProxyModelTable`, etc.) via `PrismaManager.setup_database()`.
   - Seeded `LiteLLM_UserTable` (`user_id='default_user_id'`, `user_email='admin'`, `user_role='proxy_admin'`), `LiteLLM_TeamTable` (`litellm-dashboard`, `boylenet-fleet`), and `LiteLLM_VerificationToken` with hashed tokens for `sk-fleet-local`, `sk-boylenet-local`, and `sk-local`.
2. **LiteLLM Configuration (`/home/mboyle/infra/support-layer/litellm/` on `.164` and `.228`)**:
   - `config.yaml`: Added `database_url: os.environ/DATABASE_URL` and `store_model_in_db: true` under `general_settings`.
   - `.env`: Configured `LITELLM_MASTER_KEY=sk-fleet-local`, `DATABASE_URL=postgresql://litellm:boylenet-admin@10.0.70.164:5432/litellm`, `UI_USERNAME=admin`, `UI_PASSWORD=boylenet-admin`, `STORE_MODEL_IN_DB=True`, `DISABLE_SCHEMA_UPDATE=True`, and `LITELLM_LOCAL_MODEL_COST_MAP=True`.
   - `custom_auth.py`: Updated `user_api_key_auth` to return `user_id=LITELLM_PROXY_ADMIN_NAME` (`default_user_id`) and `user_role=LitellmUserRoles.PROXY_ADMIN` so all existing API keys (`sk-fleet-local`, `sk-boylenet-local`, `sk-local`) and Admin UI sessions work seamlessly.
   - Recreated `litellm-proxy` on both `10.0.70.164:4000` and `10.0.70.228:4000` via `/home/mboyle/infra/support-layer/litellm/run.sh`.

---

## 3. Working Admin UI Credentials

Both passwords below are verified working for `http://litellm.boylenet.themfboyles.com/ui/login/`, `http://10.0.70.164:4000/ui/login/`, and `http://10.0.70.228:4000/ui/login/`:

| Field | Option A (Env Admin Credentials) | Option B (Master Key Fallback via DB User) |
| :--- | :--- | :--- |
| **Username** | `admin` | `admin` |
| **Password** | `boylenet-admin` | `sk-fleet-local` |

---

## 4. End-to-End Verification Output

```text
=== Domain POST http://litellm.boylenet.themfboyles.com/v2/login (admin / boylenet-admin) ===
{"redirect_url":"http://litellm.boylenet.themfboyles.com/ui?login=success","token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."}
Domain /v2/login HTTP 200

=== .164 POST /v2/login (admin / boylenet-admin) ===
HTTP/1.1 200 OK
set-cookie: token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...; Path=/; SameSite=lax
{"redirect_url":"http://10.0.70.164:4000/ui?login=success","token":"eyJ..."}

=== .164 POST /v2/login (admin / sk-fleet-local) ===
HTTP/1.1 200 OK
set-cookie: token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...; Path=/; SameSite=lax
{"redirect_url":"http://10.0.70.164:4000/ui?login=success","token":"eyJ..."}

=== .228 POST /v2/login (admin / boylenet-admin) ===
HTTP/1.1 200 OK
set-cookie: token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...; Path=/; SameSite=lax
{"redirect_url":"http://10.0.70.228:4000/ui?login=success","token":"eyJ..."}

=== .228 POST /v2/login (admin / sk-fleet-local) ===
HTTP/1.1 200 OK
set-cookie: token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...; Path=/; SameSite=lax
{"redirect_url":"http://10.0.70.228:4000/ui?login=success","token":"eyJ..."}

=== .164 & .228 UI Management Endpoints ===
.164 /v2/user/info HTTP 200 -> {"user_id":"default_user_id","user_email":"admin","user_alias":"Boylenet Proxy Admin","user_role":"proxy_admin",...}
.228 /v2/user/info HTTP 200 -> {"user_id":"default_user_id","user_email":"admin","user_alias":"Boylenet Proxy Admin","user_role":"proxy_admin",...}
.164 /key/list HTTP 200 -> {"keys":["7c9f...","578b...","0134..."],"total_count":3,"current_page":1,"total_pages":1}
.228 /key/list HTTP 200 -> {"keys":["7c9f...","578b...","0134..."],"total_count":3,"current_page":1,"total_pages":1}
.164 /v2/model/info total_count: 73 data len: 50
.228 /v2/model/info total_count: 73 data len: 50

=== .164 & .228 /v1/models with sk-fleet-local and sk-boylenet-local ===
.164 sk-fleet-local models: 58
.164 sk-boylenet-local models: 58
.228 sk-fleet-local models: 58
.228 sk-boylenet-local models: 58
```
