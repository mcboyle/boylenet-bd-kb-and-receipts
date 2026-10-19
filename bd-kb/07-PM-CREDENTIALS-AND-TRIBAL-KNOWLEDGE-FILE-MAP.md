# 🗺️ BD-KB 07: PM Credentials, Fleet State & Tribal Knowledge Master File Map

- **Document ID**: `bd-kb-07-pm-credentials-and-tribal-knowledge-file-map`
- **Canonical Path**: `/home/mboyle/bd-persist/bd-kb/07-PM-CREDENTIALS-AND-TRIBAL-KNOWLEDGE-FILE-MAP.md`
- **Companion Vault**: `/home/mboyle/bd-persist/credentials/MASTER-CREDENTIALS-VAULT.md` (`chmod 600`)
- **Written At (UTC)**: `2026-10-19T22:35:00Z`
- **Author**: `Agent-181` (Master Credentials, API Keys, Database Credits & Passwords Vault + PM File Map Architect)
- **Purpose**: Single authoritative directory and file lookup map for `bd-pm-B`, Command Squad seats (`integrator`, `dispatch`, `lensrouter`, `audit`, `adjudicator`, `trainer`), and autonomous subagents to locate every credential, tribal knowledge volume, live fleet state ledger, queue TSV, and O1789/O3394 receipt on disk without blind searching.

---

## 1. Master Credentials, API Keys, Tokens & Auth Stores

| Resource / Credential Domain | Canonical Path on Disk (`.164` / Shared) | Access Mode / Format | Live Query / Verification Command |
|---|---|---|---|
| **Master Plaintext Credentials Vault** | `/home/mboyle/bd-persist/credentials/MASTER-CREDENTIALS-VAULT.md` | Markdown (`chmod 600` inside `chmod 700` dir) | `cat /home/mboyle/bd-persist/credentials/MASTER-CREDENTIALS-VAULT.md` |
| **Live Web Credentials Vault & SSH Matrix (`:4087`)** | `/home/mboyle/bin/bd-credentials-vault.py` (`/etc/systemd/system/bd-credentials-vault.service`) | Python HTTP Server + JSON API (`:4087`) | `curl -s http://127.0.0.1:4087/api/credentials \| jq .` / `curl -s http://127.0.0.1:4087/api/ssh-matrix \| jq .` |
| **VMware vCenter (`vc04`), ESXi (`01..04`) & HPE iLO4/iLO5** | `/home/mboyle/.config/bd/vsphere.env` & `/home/mboyle/bin/lib_vsphere_govc.py` | Env file (`chmod 600`) + Python helper | `source /home/mboyle/.config/bd/vsphere.env && govc about` |
| **Ubiquiti UniFi OS Gateway (`10.0.70.1`)** | `/home/mboyle/.config/bd/unifi.env` | Env file (`chmod 600`) | `source /home/mboyle/.config/bd/unifi.env` |
| **Cloudflare DNS-01 API Token (Let's Encrypt)** | `/etc/letsencrypt/cloudflare.ini` | Root-owned INI (`chmod 600`) | `sudo cat /etc/letsencrypt/cloudflare.ini` |
| **Gitea API Tokens (Read / Write / Pre-Review)** | `/home/mboyle/.config/bd/gitea-token`, `/home/mboyle/.config/bd/gitea-token-write`, `/home/mboyle/.config/bd/gitea-prereview-token` | Plaintext token files | `cat /home/mboyle/.config/bd/gitea-token-write` |
| **GitHub CLI (`gh`) & Fine-Grained PAT** | `/home/mboyle/.config/gh/hosts.yml` & `/home/mboyle/.config/bd/gh-pat` | YAML / Plaintext token | `gh auth status` |
| **Hugging Face Hub Token** | `/home/mboyle/.cache/huggingface/token` | Plaintext token (`hf_...`) | `cat /home/mboyle/.cache/huggingface/token` |
| **LiteLLM Proxy Master Key & DB Env (`.164:4000`)** | `/home/mboyle/infra/support-layer/litellm/.env`, `config.yaml`, `custom_auth.py` | Env (`chmod 600`) + YAML + Python | `curl -s -H "Authorization: Bearer sk-fleet-local" http://10.0.70.164:4000/v1/models` |
| **Sovereign Local GPU Gateway (`:4010`)** | `/usr/local/bin/bd-gpu-orchestrator.py` | Python daemon (`bd-gpu-orchestrator.service`) | `curl -s http://10.0.70.164:4010/v1/models \| jq .` |
| **OpenWebUI SQLite DB & Admin API Key (`.164:8090`)** | `/home/mboyle/bd-persist/open-webui-data/webui.db` | SQLite 3 (`api_key` table: `sk-owui-admin-test`) | `sqlite3 /home/mboyle/bd-persist/open-webui-data/webui.db "SELECT email, role FROM user;"` |
| **15 Linux VM VNC Passwords** | `/home/mboyle/.config/bd/vnc-passwords.tsv` | TSV (`chmod 600`) | `cat /home/mboyle/.config/bd/vnc-passwords.tsv` |
| **Windows Dual Chrome CDP Oracles (`.137:9222` & `.181:9222`)** | `/home/mboyle/bin/bd-oracle` & `/home/mboyle/bin/bd-chrome-bridge.py` | Python CDP client (PIN `628895`) | `bd-oracle --oracle all --json "ping"` / `curl -s http://10.0.10.137:9222/json/list` |
| **27-Node Linux SSH Ed25519 Keypair** | `/home/mboyle/.ssh/id_ed25519` & `/home/mboyle/.ssh/id_ed25519.pub` | OpenSSH Ed25519 (`chmod 600`) | `ssh -i ~/.ssh/id_ed25519 10.0.70.82 hostname` |
| **Claude Code 4-Pool OAuth Stores (`A/B/C/D`)** | `/home/mboyle/.claude-a/.credentials.json`, `~/.claude-b/.credentials.json`, `~/.claude-c/.credentials.json`, `~/.claude-d/.credentials.json` | JSON OAuth 2.0 (`24/24` VMs independent) | `CLAUDE_CONFIG_DIR=~/.claude-b claude auth status` |
| **OpenAI Codex CLI OAuth Store (Pool C)** | `/home/mboyle/.codex/auth.json` & `/home/mboyle/.codex/config.toml` | JSON JWT + TOML | `~/.local/bin/codex login status` |
| **Kimi K2.8 CLI OAuth Store (Pool E)** | `/home/mboyle/.kimi/config.toml` & `/home/mboyle/.kimi-code/credentials/kimi-code-env-0e4f99c69cc27850.json` | TOML + JSON OAuth | `~/.local/bin/kimi --version` |
| **Google Antigravity CLI OAuth Store (Pool D)** | `/home/mboyle/.gemini/antigravity-cli/antigravity-oauth-token` | JSON Google OAuth (`ya29...`) | `ls -l ~/.gemini/antigravity-cli/antigravity-oauth-token` |

---

## 2. Boylenet Master Knowledge Base (`BD-KB` Volumes `00`–`07` + Live Qdrant Index)

All volumes live in `/home/mboyle/bd-persist/bd-kb/` and are continuously indexed into Qdrant (`http://10.0.70.125:6333`, collection `bd_master_kb`) by `bd-kb-live-sync.service` (`/home/mboyle/bin/bd-kb-live-sync.py`).

| Volume | Exact File Path | Scope & Contents |
|---|---|---|
| **Manifest** | `/home/mboyle/bd-persist/bd-kb/manifest.json` | JSON registry of all BD-KB volumes, vector counts, and sync timestamps. |
| **Vol 00** | `/home/mboyle/bd-persist/bd-kb/00-BD-KB-MASTER-ENCYCLOPEDIA.md` | Master index, architecture overview, and cross-volume table of contents. |
| **Vol 01A** | `/home/mboyle/bd-persist/bd-kb/01-BOYLENET-27NODE-CLUSTER-AND-12U-RACK-BIBLE.md` | Complete 27-node VM topology, IP/VLAN table (`10/20/70`), and 12U physical rack wiring. |
| **Vol 01B** | `/home/mboyle/bd-persist/bd-kb/01-BULKDOWNLOADER-ARCHITECTURE-AND-CONTRACTS.md` | BulkDownloader repo architecture, module boundaries, test bands, and cut workflow contracts. |
| **Vol 02** | `/home/mboyle/bd-persist/bd-kb/02-BOYLENET-27NODE-CLUSTER-12U-RACK-VSAN-25GBE.md` | 4x HPE DL380 Gen10 ESXi hosts (`.41`–`.44`), 11-Disk-Group `107 TB` All-NVMe vSAN, `25GbE` RoCE v2. |
| **Vol 03** | `/home/mboyle/bd-persist/bd-kb/03-86-MODEL-VAULT-BENCHMARKS-AND-AIRGAP.md` | 86-model (`8.9+ TB`) AI Vault catalog on `.72` & `.83`, `tok/s` benchmarks, Tier 1/2/3 orchestrator, `bd-airgap`. |
| **Vol 04** | `/home/mboyle/bd-persist/bd-kb/04-FLEET-LAW-RULES-1-86-AND-O1740-ROUTING.md` | Complete Fleet Rules `1`–`86` summary and Operator Directive `O1740` dynamic capability routing law. |
| **Vol 05A** | `/home/mboyle/bd-persist/bd-kb/05-CI-TAXONOMY-RECOVERY-AND-MCP-TOOLS.md` | CI failure taxonomy, `.82` 64-core `/dev/shm` ramdisk pytest execution, and MCP tool reference. |
| **Vol 05B** | `/home/mboyle/bd-persist/bd-kb/05-SWARM-OPERATIONS-RECEIPTS-AND-PLAYBOOKS.md` | Operational playbooks, gridlock recovery, and autonomous swarm runbooks. |
| **Vol 06** | `/home/mboyle/bd-persist/bd-kb/06-5PLATFORM-MCP-MODS-HTTPS-AND-215-LEDGER.md` | 20-tool `boylenet-infra` MCP server, Mods 1–5 (`bd-swarm-mod`), NGINX/Cloudflare `:443` HTTPS, and `215/215` backlog ledger. |
| **Vol 07** | `/home/mboyle/bd-persist/bd-kb/07-PM-CREDENTIALS-AND-TRIBAL-KNOWLEDGE-FILE-MAP.md` | **This document**: Master file location map for credentials, tribal knowledge, fleet state, and O1789/O3394 receipts. |
| **Vol 08–09** | `/home/mboyle/bd-persist/bd-kb/08-*.md` – `09-*.md` (reserved/expansion slots) | Indexed automatically by `/home/mboyle/bin/bd-kb-live-sync.py` whenever created. |

**Fast Search CLI / MCP**:
- CLI: `/home/mboyle/bin/bd-boylenet-infra-mcp.py call bd_kb_search '{"query":"..."}'`
- MCP: `call_mcp_tool(ServerName="boylenet-infra", ToolName="bd_kb_search", Arguments={"query": "..."})`

---

## 3. Fleet Law, Operator Directives, Live State & Scoreboards

| File / Directory | Exact Path on Disk | Role & Consumers |
|---|---|---|
| **Carried Fleet Law Floor (`<=4 KB`)** | `/home/mboyle/bd-persist/FLEET_RULE-FLOOR.md` | Mandatory read-first floor for every agent seat (`GEMINI.md` / `CLAUDE.md` / `AGENTS.md`). |
| **Full Fleet Law (`Rules 1–86`)** | `/home/mboyle/bd-persist/FLEET_RULE.md` | Authoritative law book; query by heading (`grep -n "^## " /home/mboyle/bd-persist/FLEET_RULE.md`), never read whole. |
| **Fleet Rule Evidence Ledger** | `/home/mboyle/bd-persist/FLEET_RULE-EVIDENCE.md` | Empirical incident provenance backing Fleet Rules. |
| **Cut Workflow Contract** | `/home/mboyle/bd-persist/CUT-WORKFLOW.md` | Canonical RED -> GREEN -> `PATCH-SHA256` -> adversarial lens review lifecycle. |
| **Operator Decisions Ledger** | `/home/mboyle/bd-persist/OPERATOR_DECISIONS.md` (and `O*` orders in `/home/mboyle/bd-persist/state/ORDER-*.md`) | Chronological operator directives (`O1`–`O3417+`). |
| **O1740 Dynamic Pool Quota State** | `/home/mboyle/bd-persist/POOL_STATE.tsv` (and `/home/mboyle/bd-persist/state/PLATFORM-SCORE-quota.tsv`) | Live pool admission (`state`, `five_hour_pct`, `weekly_pct`, `resets_at`) consumed by every launcher under O1740. |
| **Host & Seat Placement Ledgers** | `/home/mboyle/bd-persist/HOST-STATE.tsv`, `/home/mboyle/bd-persist/state/PLACEMENTS.tsv`, `/home/mboyle/.config/bd/hosts`, `/home/mboyle/.config/bd/roles` | Live VM health, role capacities, and active agent seat placements. |
| **Dispatch & Launch Ledgers** | `/home/mboyle/bd-persist/DISPATCH-LEDGER.tsv`, `/home/mboyle/bd-persist/launch-ledger.tsv`, `/home/mboyle/bd-persist/sessions.tsv` | O1042 / O1740 launch audit trail recording resolved pool and `POOL_STATE.tsv` timestamp per launch. |
| **Per-Seat Role State Files (`role-state/`)** | `/home/mboyle/bd-persist/role-state/*.md` (`bd-pm-B.md`, `bd-dispatch-B5-B.md`, `bd-integrator-C.md`, `bd-lensrouter-*.md`, etc.) | Durable per-seat state checkpoints read/written via `bd` MCP `role_state_get` / `role_state_set`. |
| **Local GPU Builder & Lens Scoreboards** | `/home/mboyle/bd-persist/state/LOCAL-BUILD-SCORE.tsv` & `/home/mboyle/bd-persist/state/LOCAL-LENS-SCORE.tsv` | Live pass/fail and latency scoreboards for `.83:18080` (`Qwen3.6-35B-A3B-Heretic`) and `.137:11434` (`RTX 2080 Ti`) shadow/primary builders and lenses. |
| **Local LLM Health & Inference Logs** | `/home/mboyle/bd-persist/state/LOCAL_LLM_HEALTH.tsv`, `/home/mboyle/bd-persist/state/LOCAL_LLM_INFERENCE.log`, `/home/mboyle/bd-persist/state/PLATFORM-SCORE.tsv` | Sub-minute telemetry of local GPU endpoints and cross-platform throughput. |
| **6 Master Backlog Queues (`215/215` Reconciled)** | `/home/mboyle/bd-persist/BACKLOG.tsv`, `/home/mboyle/bd-persist/REBRIEF-QUEUE.tsv`, plus the 6 canonical queue ledgers indexed in PostgreSQL `boylenet_swarm_ledger.boylenet_backlog_215_ledger` and queried via `/home/mboyle/bin/bd-boylenet-infra-mcp.py call queue_backlog_status '{}'` | Live backlog and unpark tracking across `P3-LENS`, `BH1`, `BH2`, `P2-FIX`, `APP-DL-FIX`, and `O1670`. |

---

## 4. O1789 & O3394 Infrastructure Upgrade Receipts & Benchmark Ledgers

All `O1789` ("Double It and the 115 Approved") and `O3394` receipts live in `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/` and `/home/mboyle/bd-persist/state/`:

| Category | Key Receipt / Ledger Paths | What It Certifies |
|---|---|---|
| **O3394 24-VM 5-Platform Auth & Plugin Parity** | `/home/mboyle/bd-persist/state/O3394-ALL-24VMS-CLAUDE-ABCD-CODEX-KIMI-AGY-AUTH-VERIFIED.md` & `/home/mboyle/bd-persist/state/O3394-FLEET-24VM-PLATFORM-PLUGINS-SKILLS-MODS-PARITY-RECEIPT.md` | Certifies independent per-VM OAuth across all 24 VMs for Claude Pools `A/B/C/D`, Codex, Kimi, and AGY, plus full MCP/Skill/Mod parity. |
| **Credentials Vault & 27-Node SSH Matrix (`Agent-66`)** | `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT66-CREDENTIALS-AND-SSH-VAULT-RECEIPTS.md` | Certifies `:4087` (`bd-credentials-vault.py`), 31 live service credentials, 15 VNC passwords, and 38-node SSH key matrix. |
| **LiteLLM PostgreSQL DB & Admin UI (`Agent-46`)** | `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT46-LITELLM-POSTGRES-DB-RECEIPTS.md` | Certifies PostgreSQL `litellm` & `litellm_228` DBs (`77` Prisma tables) and `custom_auth.py` on `.164:4000` & `.228:4000`. |
| **86-Model Vault & Serialized GPU Benchmarks (`Agent-36`, `37`, `151`, `152`, `171`, `172`, `174`)** | `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT36-MASTER-MODEL-VAULT-AND-FLEET-PARITY-REPORT.md`, `AGENT152-ALL-MODELS-THINKING-AND-CONTEXT-VERIFICATION-RECEIPTS.md`, `AGENT171-HOST72-SERIALIZED-BENCHMARKS-RECEIPTS.md`, `AGENT172-HOST83-SERIALIZED-BENCHMARKS-RECEIPTS.md` | Certifies 86 local GGUF models, 256K–1M context windows, `<think>` tag reasoning, and serialized host `.72` & `.83` benchmarks. |
| **OpenWebUI 15 Custom Agents, 83 Tools, 14 MCPs & Qdrant/PG (`Agent-106`, `129`, `140`)** | `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT106-OPENWEBUI-E2E-TESTS-AND-15-CUSTOM-AGENTS-RECEIPTS.md`, `AGENT140-OPENWEBUI-15AGENTS-83TOOLS-14MCP-QDRANT-PG-RECEIPTS.md` | Certifies 15 `agent-*` presets on `.164:8090` & `.72:8080`, 83 `:4079` tool routes, 14 MCP servers, 6 Qdrant collections, and `boylenet_swarm_ledger`. |
| **NGINX + Cloudflare DNS-01 Let's Encrypt HTTPS (`Agent-135`, `136`, `148`, `149`, `150`)** | `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT135-NGINX-LETSENCRYPT-CLOUDFLARE-HTTPS-RECEIPTS.md`, `AGENT148-CLOUDFLARE-THEMFBOYLES-ORG-LETSENCRYPT-CERT-RECEIPTS.md`, `AGENT149-THEMFBOYLES-ORG-DOMAIN-MIGRATION-RECEIPTS.md` | Certifies wildcard TLS certificates (`*.boylenet.themfboyles.org`, `*.themfboyles.org`) and `:443` HTTPS reverse proxy across all portals. |
| **vSAN 107TB All-NVMe, 25GbE RoCE v2 & ESXi Hardware (`Agent-1`, `3`, `4`, `13`, `17`, `62`, `109`, `170`)** | `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT109-VSAN-25GBE-HARDWARE-E2E-CERTIFICATION-RECEIPTS.md`, `AGENT62-HARDWARE-ILO-VSAN-TELEMETRY-RECEIPTS.md`, `AGENT170-ESXI-POWER-SYSCTL-AND-ACTION4-KIMI-RECEIPTS.md` | Certifies 4x HPE DL380 Gen10 ESXi hosts, High Performance power policy, 25GbE RoCE v2, and 11-DG vSAN health. |
| **5-Platform MCP, Mods 1–5 & 215/215 Backlog (`Agent-111`, `116`, `123`, `124`, `147`, `176`)** | `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT111-4PLATFORM-AUTONOMOUS-INFRA-MCP-PLUGINS-SKILLS-RECEIPTS.md`, `AGENT124-MASTER-215-BACKLOG-AND-GITHUB-SYNC-RECEIPTS.md`, `AGENT176-FLEET-SERVICES-AND-INFRA-DEEP-VERIFY-RECEIPTS.md` | Certifies 5-platform `boylenet-infra` MCP, `bd-swarm-mod` Mods 1–5, 22 slash commands/skills, and `215/215` backlog closure. |
