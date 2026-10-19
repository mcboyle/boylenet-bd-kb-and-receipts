# BD-KB Volume 30: PM Master Executive Cheatsheet & Complete 31-Volume (00–30) Master Documentation Index
> **Author**: `Spec-230` (`AGY claude+gpt (claude-opus-5-5-high)`)
> **Generated At**: `2026-10-19T22:59:18Z`
> **Scope**: Strictly Read-Only Infrastructure, Tools, Services, LLMs & PM Catch-Up Documentation (`O3416` Compliant)

---

# 🧭 BD-KB Volume 30: PM Master Executive Cheatsheet & Complete 31-Volume (00–30) Master Documentation Index

- **Document ID**: `bd-kb-30-pm-master-executive-cheatsheet-and-master-index`
- **Proposed Canonical Path**: `/home/mboyle/bd-persist/bd-kb/30-PM-MASTER-EXECUTIVE-CHEATSHEET-AND-MASTER-INDEX.md`
- **Author**: `Spec-230`
- **Written At (UTC)**: `2026-10-19T22:56Z`
- **Audience**: `bd-pm-B` (Fleet Project Manager), the operator, and Command Squad (`integrator`, `dispatch`, `lensrouter`, `audit`, `adjudicator`, `trainer`)
- **Sources**: `PM-CATCHUP-BRIEF-EVERYTHING-BUILT-20261019.md` (Agent-182), `bd-kb/07-PM-CREDENTIALS-AND-TRIBAL-KNOWLEDGE-FILE-MAP.md` (Agent-181), `state/PAUSED-O3416-messaging-crons.md`

> [!IMPORTANT]
> **Read this first: what the sources do and don't cover.** I wrote this volume without running any commands, using only three source files. Every fact below comes from those files. When the sources don't contain something you asked for, I mark it **`⚠ UNVERIFIED`** and give you a command to check it, instead of making up a value. The gaps are:
> - the file names of Volumes `10`–`29`
> - the contents of `PM-HANDOFF.md`
> - the behaviour of `bd-creds-lookup`
> - the names of the 7 repos
> - the root causes behind the `18:06`, `~/bin`, `11:29` and `02:30` events
> - the exact argument syntax for `bd-say.sh`

---

## 0. The 60-Second Executive Summary

| # | What you need to know | Where it lives |
|---|---|---|
| 1 | **Everything built while the PM train was running** (230-Swarm, portals, 87-model vault, 24-VM parity) is summarized in one brief. | `/home/mboyle/bd-persist/PM-CATCHUP-BRIEF-EVERYTHING-BUILT-20261019.md` |
| 2 | **The same brief, as a PM inbox copy** (dropped quietly, with no tmux kick). | `/home/mboyle/bd-persist/inbox/PM/BRIEF-AGY-FULL-INFRA-AND-TOOLS-CATCHUP-20261019.md` |
| 3 | **All credentials** are in one plaintext vault: `chmod 600` file inside a `chmod 700` dir. | `/home/mboyle/bd-persist/credentials/MASTER-CREDENTIALS-VAULT.md` |
| 4 | **Credential lookup CLI.** | `/home/mboyle/bin/bd-creds-lookup` *(⚠ UNVERIFIED: not described in the sources)* |
| 5 | **PM handoff file.** | `/home/mboyle/bd-persist/PM-HANDOFF.md` *(⚠ UNVERIFIED: contents not in the sources)* |
| 6 | **Directory and file map** for every credential, ledger and receipt. | `/home/mboyle/bd-persist/bd-kb/07-PM-CREDENTIALS-AND-TRIBAL-KNOWLEDGE-FILE-MAP.md` |
| 7 | **All crons and timers that message the PM or any seat are paused** (O3416, "until I say so"). The handoff channel is now passive file drops in `inbox/PM/`. | `/home/mboyle/bd-persist/state/PAUSED-O3416-messaging-crons.md` |
| 8 | **Two local relief models are standing by** (256K context, `<think>` preserved). | `.83:18080` (Qwen3.6-35B-A3B-Heretic) and `.72:8000` (Qwen3-Coder-30B-A3B) |
| 9 | **Front door for all routing**: `:4010` GPU orchestrator, plus the `:4000` LiteLLM router that sits on top of it. | `https://gpu.boylenet.themfboyles.org`, `https://litellm.boylenet.themfboyles.org` |
| 10 | **Train isolation is the operating invariant**: 100% non-blocking work that never touches `bd-pm-B`'s BURN / UNPARK train. | `ORDER-unpark-all.md`, `BUGHUNT-WAVE1` |

---

## 1. Master File Location Index (PM Critical Paths)

### 1.1 The Five Files You Asked About

| File | Path | Mode | Purpose | How to read it safely |
|---|---|---|---|---|
| **Master Credentials Vault** | `/home/mboyle/bd-persist/credentials/MASTER-CREDENTIALS-VAULT.md` | `chmod 600` (dir `700`) | All plaintext credentials: vCenter/ESXi/iLO, UniFi, Cloudflare, Gitea, GitHub, HF, LiteLLM, OpenWebUI, VNC, CDP oracle PIN, OAuth stores | Use `less` in a **private** shell, never in a shared or streamed tmux pane. The Terminal TV Wall (`:4083`) streams 31 local `.164` tmux sessions, including `bd-pm-B`. |
| **Creds Lookup CLI** | `/home/mboyle/bin/bd-creds-lookup` | executable | Looks up single entries in the vault | ⚠ UNVERIFIED. Check usage with `bd-creds-lookup --help \| head -30` |
| **PM Catch-Up Brief** | `/home/mboyle/bd-persist/PM-CATCHUP-BRIEF-EVERYTHING-BUILT-20261019.md` | `644` (assumed) | Full inventory of the parallel infra blitz | `grep -n "^## " <path>` to jump between sections |
| **PM Handoff** | `/home/mboyle/bd-persist/PM-HANDOFF.md` | — | PM seat continuity and handoff | ⚠ UNVERIFIED. `head -60 /home/mboyle/bd-persist/PM-HANDOFF.md` |
| **O3416 Pause Ledger** | `/home/mboyle/bd-persist/state/PAUSED-O3416-messaging-crons.md` | — | Inventory of the pause, plus resume instructions | `cat` it (about 2 KB) |

### 1.2 Fleet Law and State (from Vol 07 §3)

| What | Path |
|---|---|
| Carried law floor (`<=4 KB`, read first) | `/home/mboyle/bd-persist/FLEET_RULE-FLOOR.md` |
| Full law, Rules 1–86 (query by heading, never read the whole file) | `/home/mboyle/bd-persist/FLEET_RULE.md` |
| Law evidence | `/home/mboyle/bd-persist/FLEET_RULE-EVIDENCE.md` |
| Cut lifecycle (RED → GREEN → `PATCH-SHA256` → lens) | `/home/mboyle/bd-persist/CUT-WORKFLOW.md` |
| Operator decisions (`O1`–`O3417+`) | `/home/mboyle/bd-persist/OPERATOR_DECISIONS.md`, `/home/mboyle/bd-persist/state/ORDER-*.md` |
| O1740 pool quota | `/home/mboyle/bd-persist/POOL_STATE.tsv`, `/home/mboyle/bd-persist/state/PLATFORM-SCORE-quota.tsv` |
| Hosts and placements | `HOST-STATE.tsv`, `state/PLACEMENTS.tsv`, `~/.config/bd/hosts`, `~/.config/bd/roles` |
| Dispatch and launch audit | `DISPATCH-LEDGER.tsv`, `launch-ledger.tsv`, `sessions.tsv` |
| Per-seat role state | `/home/mboyle/bd-persist/role-state/bd-pm-B.md` (and other seats) |
| Local GPU scoreboards | `state/LOCAL-BUILD-SCORE.tsv`, `state/LOCAL-LENS-SCORE.tsv`, `state/LOCAL_LLM_HEALTH.tsv` |
| Backlog (215/215 reconciled) | `BACKLOG.tsv`, `REBRIEF-QUEUE.tsv`, PostgreSQL `boylenet_swarm_ledger.boylenet_backlog_215_ledger` |
| O3394 parity receipts | `state/O3394-ALL-24VMS-CLAUDE-ABCD-CODEX-KIMI-AGY-AUTH-VERIFIED.md`, `state/O3394-FLEET-24VM-PLATFORM-PLUGINS-SKILLS-MODS-PARITY-RECEIPT.md` |
| O1789 receipts | `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/` |
| 230-Swarm ledger | `MASTER-230-SWARM-MEGA-BLITZ-LEDGER.md` |
| Post-crash restore receipt | `AGENT173-POST-CRASH-PM-AND-SESSIONS-RESTORE-RECEIPTS.md` |

### 1.3 Credential Stores at a Glance (from Vol 07 §1)

| Domain | Path |
|---|---|
| vSphere / ESXi / iLO | `~/.config/bd/vsphere.env` (`source` it, then `govc about`) |
| UniFi (`10.0.70.1`) | `~/.config/bd/unifi.env` |
| Cloudflare DNS-01 | `/etc/letsencrypt/cloudflare.ini` (root, `600`) |
| Gitea tokens | `~/.config/bd/gitea-token`, `gitea-token-write`, `gitea-prereview-token` |
| GitHub | `~/.config/gh/hosts.yml`, `~/.config/bd/gh-pat` |
| Hugging Face | `~/.cache/huggingface/token` |
| LiteLLM | `~/infra/support-layer/litellm/.env` (bearer `sk-fleet-local`) |
| OpenWebUI DB | `~/bd-persist/open-webui-data/webui.db` |
| VNC (15 VMs) | `~/.config/bd/vnc-passwords.tsv` |
| SSH (27 nodes) | `~/.ssh/id_ed25519` |
| Claude pools A–D | `~/.claude-{a,b,c,d}/.credentials.json` |
| Codex / Kimi / AGY | `~/.codex/auth.json`, `~/.kimi/config.toml`, `~/.gemini/antigravity-cli/antigravity-oauth-token` |

---

## 2. Complete 31-Volume (00–30) BD-KB Master Documentation Index

**Root directory**: `/home/mboyle/bd-persist/bd-kb/`
**Registry**: `/home/mboyle/bd-persist/bd-kb/manifest.json`
**Live index**: Qdrant `http://10.0.70.125:6333`, collection `bd_master_kb`. It is kept in sync by `bd-kb-live-sync.service` (`/home/mboyle/bin/bd-kb-live-sync.py`), which picks up new files automatically.

> [!NOTE]
> Volumes `01` and `05` each have an **A** and a **B** file. That means "31 volumes" (00–30) is more than 31 files on disk. Use `manifest.json` as the source of truth for counts.

| Vol | File (exact where known) | Scope | Status in sources |
|---|---|---|---|
| **00** | `00-BD-KB-MASTER-ENCYCLOPEDIA.md` | Master index, architecture overview, cross-volume table of contents | ✅ Confirmed |
| **01A** | `01-BOYLENET-27NODE-CLUSTER-AND-12U-RACK-BIBLE.md` | 27-node topology, VLAN 10/20/70 IP table, 12U rack wiring | ✅ Confirmed |
| **01B** | `01-BULKDOWNLOADER-ARCHITECTURE-AND-CONTRACTS.md` | BulkDownloader modules, test bands, cut contracts | ✅ Confirmed |
| **02** | `02-BOYLENET-27NODE-CLUSTER-12U-RACK-VSAN-25GBE.md` | 4x HPE DL380 Gen10 (`.41`–`.44`), 107 TB all-NVMe vSAN, 25GbE RoCE v2 | ✅ Confirmed |
| **03** | `03-86-MODEL-VAULT-BENCHMARKS-AND-AIRGAP.md` | Vault catalog, tok/s figures, Tier 1/2/3, `bd-airgap` | ✅ Confirmed (title says **86**; the brief certifies **87/87**) |
| **04** | `04-FLEET-LAW-RULES-1-86-AND-O1740-ROUTING.md` | Rules 1–86 summary, O1740 routing | ✅ Confirmed |
| **05A** | `05-CI-TAXONOMY-RECOVERY-AND-MCP-TOOLS.md` | CI taxonomy, `.82` `/dev/shm` ramdisk pytest, MCP tools | ✅ Confirmed |
| **05B** | `05-SWARM-OPERATIONS-RECEIPTS-AND-PLAYBOOKS.md` | Gridlock recovery, swarm runbooks | ✅ Confirmed |
| **06** | `06-5PLATFORM-MCP-MODS-HTTPS-AND-215-LEDGER.md` | 20-tool `boylenet-infra` MCP, Mods 1–5, NGINX/Cloudflare `:443`, 215 ledger | ✅ Confirmed |
| **07** | `07-PM-CREDENTIALS-AND-TRIBAL-KNOWLEDGE-FILE-MAP.md` | Credential, state and receipt file map | ✅ Confirmed |
| **08** | `08-*.md` | Reserved / expansion slot | ⚠ Name not in sources |
| **09** | `09-*.md` | Reserved / expansion slot | ⚠ Name not in sources |
| **10–29** | `10-*.md` … `29-*.md` | Written by the Spec series; titles not in the provided context | ⚠ UNVERIFIED: resolve with the commands below |
| **30** | `30-PM-MASTER-EXECUTIVE-CHEATSHEET-AND-MASTER-INDEX.md` | **This document** | Proposed path |

**Resolve Volumes 08–29 without guessing:**

```bash
ls -1 /home/mboyle/bd-persist/bd-kb/ | sort -V
jq -r '.. | objects | select(has("path")) | .path' /home/mboyle/bd-persist/bd-kb/manifest.json 2>/dev/null | sort -V
/home/mboyle/bin/bd-boylenet-infra-mcp.py call bd_kb_search '{"query":"BD-KB Volume 1"}'
```

**MCP equivalent:**
`call_mcp_tool(ServerName="boylenet-infra", ToolName="bd_kb_search", Arguments={"query": "..."})`

---

## 3. Timestamp Explanations: `18:06` `.claude.json`, `~/bin` Refresher, `11:29` Registry, `02:30` Backlog

> [!WARNING]
> **None of these four events is explained in the provided sources.** The table below gives the most likely link to something that *is* documented (mainly the O3416 inventory), how confident I am, and the command that settles it. Treat the hypothesis column as a lead to check, not a finding.

| Event | Likely link (from the sources) | Confidence | Settle it with |
|---|---|---|---|
| **`18:06` `.claude.json` change** | Claude OAuth lives in `~/.claude-{a..d}/.credentials.json`, and O3394 certified per-VM auth parity. O3416 lists **`auth-sync`** as **still running**. An auth or config sync rewriting `~/.claude.json` fits, but nothing in the sources confirms it. | Low–Medium | `stat ~/.claude.json`; `crontab -l \| grep -n -i -E 'auth-sync\|claude'`; `systemctl --user list-timers --all \| grep -i auth`; `journalctl --user --since "18:00" --until "18:15" \| grep -i claude` |
| **`~/bin` refresher** | O3416 lists `pool-usage-refresher`, `regen-main` and `persist loop` among the jobs **still running**. The sources do not name a `~/bin` sync job. | Low | `crontab -l \| grep -n '/home/mboyle/bin'`; `systemctl --user list-timers --all`; `ls -lt /home/mboyle/bin \| head` |
| **`11:29` registry** | O3416 names **`bd-reap` (registry reap)** as a user timer that was *stopped, not disabled*. **A hub reboot restarts it.** `.164` rebooted at about `03:52 UTC` during vCenter RAM changes, so a registry job firing later that day after the reboot is plausible. | Medium | `systemctl --user list-timers --all \| grep -E 'reap\|registry'`; `systemctl --user cat bd-reap.timer`; `journalctl --user -u bd-reap --since today` |
| **`02:30` backlog** | The O3416 crontab inventory includes a **`backlog`** line, now commented out with `#PAUSED-O3416 `. A nightly `02:30` backlog job matches that line, but the schedule itself is not in the sources. | Medium | `crontab -l \| grep -n -i backlog`; `grep -n -i backlog /home/mboyle/bd-persist/crontab-backups/crontab.pre-O3416-20261019T2138Z` |

> [!CAUTION]
> **About O3416 timers and reboots:** the paused user timers (`bd-fleet-watch`, `bd-pm-idle-watch`, `bd-reap`, `bd-pm-stall-watch`, `bd-ugrep-reaper`, `bd-usage-oneshot`, `bd-usage-probe`) and system timers (`bd-rc-enforcer`, `bd-relief-puppeteer`) were **stopped, not disabled**. If the hub reboots, they all restart, and the PM starts getting messages again. Also, `bd-ugrep-reaper` (the hub iowait guard) is paused, so watch for orphan `ugrep` processes.

**Resume O3416. Do this only on an explicit operator "say so":**

```bash
crontab -l | sed 's/^#PAUSED-O3416 //' | crontab -
# or: crontab /home/mboyle/bd-persist/crontab-backups/crontab.pre-O3416-20261019T2138Z
systemctl --user start bd-{fleet-watch,pm-idle-watch,reap,pm-stall-watch,ugrep-reaper,usage-oneshot,usage-probe}.timer
sudo systemctl start bd-rc-enforcer.timer bd-relief-puppeteer.timer
# per-VM (23 reachable): crontab ~/.crontab.pre-O3416
```

These VMs were unreachable and left untouched by O3416: `.137 .122 .171 .181 .195 .197 .198 .224 .35`.

---

## 4. The 7 GitHub / Gitea Repositories

The brief certifies **"7-repo GitHub/Gitea continuous publishing"** (Cohort VIII). However, the Jarvis portal (`:4090`) describes an **"8-repo"** sync monitor. **The sources do not list repo names.**

| Item | Value |
|---|---|
| Gitea mirror | `.164:3000` → `https://gitea.boylenet.themfboyles.org` |
| Sync monitor | Jarvis `:4090` → `/api/git-repos` |
| Pre-review portal | `:4087` → `https://prereview.boylenet.themfboyles.org` *(see the port conflict in §7)* |
| Write token | `/home/mboyle/.config/bd/gitea-token-write` |
| GitHub auth | `gh auth status`; PAT at `~/.config/bd/gh-pat` |
| MCP sync tool | `boylenet-infra` → `gitea_github_repo_sync` |
| Known repo | `BulkDownloader` (the primary train repo; see Vol 01B) |

**Get the authoritative list of all 7 (or 8) repos:**

```bash
curl -s http://127.0.0.1:4090/api/git-repos | jq .
curl -s -H "Authorization: token $(cat ~/.config/bd/gitea-token)" http://10.0.70.164:3000/api/v1/repos/search?limit=50 | jq -r '.data[].full_name'
```

---

## 5. Standby Local Relief Models (`.83:18080` and `.72:8000`)

These two hot-pinned models are the **$0-marginal-cost relief** you can use when cloud pools hit their limits under O1740. Both are behind the `:4010` orchestrator. It watches `/slots` for idle capacity and overflows in under a second to `.137:11434` (RTX 2080 Ti) and `.228:11434` (T4).

| | **`.83:18080`** | **`.72:8000` (also `:8082`)** |
|---|---|---|
| Host | `10.0.70.83` (`aidev`, `esxi03`, 52 vCPU) | `10.0.70.72` (`ai-srv01`, `esxi04`, 52 vCPU, 320 GiB) |
| Service | `llama-heretic-18080.service` | `llama-tier1-alpha.service` |
| Model | `Qwen3.6-35B-A3B-Heretic-Q4_K_M.gguf` | `Qwen3-Coder-30B-A3B-Instruct-Q4_K_M.gguf` |
| Context | **262,144 (256K)**; YaRN verified up to 512K | **262,144 (256K)** |
| Slots / KV | `-np 4 -kvu`, KV `q8_0/q8_0`, `-fa on` | `-np 4 -kvu`, KV `q8_0/q8_0`, `-fa on` |
| VRAM | 5,515 / 15,360 MiB T4 (**about 9.8 GiB free**) | 14,863 / 15,360 MiB T4 (**nearly full**) |
| Throughput | 152.6 tok/s prompt processing / 65.8 tok/s generation | not stated |
| Reasoning | `<think>` preserved (`--reasoning-preserve`) | `<think>` preserved |
| Best for | Lens / review relief, shadow builder (`LOCAL-LENS-SCORE.tsv`) | Coding relief (Tier-1 coder, `tier1-qwen3-coder-30b-a3b` alias) |

**Health probes** (standard llama-server endpoints):

```bash
curl -s http://10.0.70.83:18080/health; curl -s http://10.0.70.83:18080/slots | jq '.[].is_processing'
curl -s http://10.0.70.72:8000/health;  curl -s http://10.0.70.72:8000/slots  | jq '.[].is_processing'
curl -s http://10.0.70.164:4010/v1/models | jq -r '.data[].id' | head
tail -5 /home/mboyle/bd-persist/state/LOCAL_LLM_HEALTH.tsv
```

> [!TIP]
> Go through `:4010`, or `:4000` with bearer `sk-fleet-local`, rather than hitting `.83` or `.72` directly. The orchestrator handles idle-slot routing and overflow. The `:4079` gateway also has `.72`/`.83` benchmark contention guards, so relief traffic won't collide with vault benchmarks.

Other local options:
- **`.72:18081`**: Qwythos-9B, 512K to 1M context.
- **`.137` / `.125` / `.228` Ollama**: CyberStrike-35B at 66.5 tok/s, Coder-7B at 95.4 tok/s.

---

## 6. Notifying `bd-pm-B`: the `bd-say.sh` Command

> [!IMPORTANT]
> **Use the quiet channel by default.** Under O3416 the operator paused every cron that messages the PM, because those messages collided with the PM's active turn. The approved non-blocking path is a **passive file drop** in `/home/mboyle/bd-persist/inbox/PM/`. Only send a live `bd-say.sh` kick when the operator explicitly asks for one, or for a genuine P0.

**Step 1. Quiet drop (always safe):**

```bash
cat > /home/mboyle/bd-persist/inbox/PM/BRIEF-BDKB-VOL30-CHEATSHEET-20261019.md <<'EOF'
BD-KB Vol 30 (PM Master Cheatsheet + 00-30 Index) ready:
/home/mboyle/bd-persist/bd-kb/30-PM-MASTER-EXECUTIVE-CHEATSHEET-AND-MASTER-INDEX.md
Catch-up brief: /home/mboyle/bd-persist/PM-CATCHUP-BRIEF-EVERYTHING-BUILT-20261019.md
Vault (600): /home/mboyle/bd-persist/credentials/MASTER-CREDENTIALS-VAULT.md
No action required; read at next idle turn.
EOF
```

**Step 2. Live notification (operator-authorized only).**

⚠ The argument syntax is not in the sources. This assumes the conventional `<seat> "<message>"` form. Run `bd-say.sh --help` before relying on it.

```bash
/home/mboyle/bin/bd-say.sh bd-pm-B "INFO (non-blocking): BD-KB Vol 30 PM Master Cheatsheet + 00-30 index landed at /home/mboyle/bd-persist/bd-kb/30-PM-MASTER-EXECUTIVE-CHEATSHEET-AND-MASTER-INDEX.md; catch-up brief + inbox/PM/BRIEF-BDKB-VOL30-CHEATSHEET-20261019.md. Read at next idle turn. No train action required."
```

**Pre-flight check.** The `bd-fleet` MCP has a `say_validate` tool. Run it before sending, so the attempt is checked and recorded per fleet messaging law.

---

## 7. Known Discrepancies to Reconcile

| # | Conflict | Source A | Source B | Suggested resolution |
|---|---|---|---|---|
| 1 | **Port `:4087`** | Brief: Gitea Pre-Review & AST Diff Portal | Vol 07: Credentials Vault & SSH Matrix (`bd-credentials-vault.py`) | `ss -ltnp \| grep 4087`; `systemctl status bd-credentials-vault`. The brief also lists a separate Credentials UI on **`:4089`**. |
| 2 | **Repo count** | Brief Cohort VIII: 7 repos | Brief `:4090`: 8-repo monitor | Use `/api/git-repos` (§4). |
| 3 | **Vault size** | Vol 03 title: 86 models | Brief: 87/87 certified | 87 is the newer figure (`AGENT154`/`171`). Vol 03's title is out of date. |
| 4 | **`.83` / `.125` / `.228` RAM** | Brief gives two values each (400/300, 192/300, 160/300) | — | Check with `free -g` on each host, or with vCenter `govc vm.info`. |
| 5 | **`.164` RAM** | 250 GiB ("adjusted from 350") | — | Check with `free -g` on `.164`. |

---

## 8. One-Glance Topology Card

| Tier | Host | Key endpoints |
|---|---|---|
| Hub | `.164` (`test5`, 32 vCPU, 250 GiB) | `bd-pm-B`, `:4010`, `:4000`, `:8090`, `:4079`–`:4099`, `:443`, Gitea `:3000`, Grafana `:3001`, OpenHands `:3002`, LibreChat `:3080` |
| AI-A | `.72` | `:8000`/`:8082` Coder-30B, `:18081` Qwythos 1M, `:8080` OpenWebUI, Vault A 4.7 TB |
| AI-B | `.83` | `:18080` Heretic-35B, Vault B 4.2 TB |
| Inference | `.125` | Qdrant `:6333`, Ollama `:11434`, `llama-rpc :50052` |
| Satellite | `.228` | Ollama `:11434` (T4) |
| Ramdisk CI | `.82` (`wrk-test04`) | 64-core, 63 GiB `/dev/shm`, 32-shard pytest |
| Browser oracles | `.137:9222`, `.181:9222` | `bd-oracle --oracle all --json "ping"` |
| Seat VMs | `.164 .84 .80 .85 .52 .249 .193 .51 .184 .194` | 64+ live tmux sessions. **Do not touch.** |

---

## 9. PM Daily Ritual (Read-Only, Non-Blocking)

1. `cat /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md` (the law floor)
2. `ls -lt /home/mboyle/bd-persist/inbox/PM/ | head` (quiet drops)
3. `column -t /home/mboyle/bd-persist/POOL_STATE.tsv` (O1740 admission)
4. `/home/mboyle/bin/bd-boylenet-infra-mcp.py call queue_backlog_status '{}'` (215 ledger)
5. Probe relief health (§5).
6. `crontab -l | grep -c '^#PAUSED-O3416'` (confirm O3416 still holds; expect about 35 lines)
7. Use `bd_kb_search` before any broad `grep` across `bd-persist`.

*— End of BD-KB Volume 30. Once Volumes 08–29 are resolved with the commands in §2, add their exact file names to this table and to `manifest.json`.*
| **31** | `/home/mboyle/bd-persist/bd-kb/31-READONLY-DOSSIER-BH1-ROWS-01-TO-10-HARNESS-GATES-AND-CLAIMS.md` | **Claude Fable 5 (`claude-fable-5-1` Pool A)** + Sonnet Squad | `39.9 KB` | Read-Only Investigation Dossier: `BH1-01`..`BH1-10` |
| **32** | `/home/mboyle/bd-persist/bd-kb/32-READONLY-DOSSIER-BH1-ROWS-11-TO-20-LAUNCHERS-IDLE-SWEEP-AND-POOL-STATE.md` | **Claude Fable 5 (`claude-fable-5-1` Pool B)** + Sonnet Squad | `44.7 KB` | Read-Only Investigation Dossier: `BH1-11`..`BH1-20` |
| **33** | `/home/mboyle/bd-persist/bd-kb/33-READONLY-DOSSIER-BH1-ROWS-21-TO-30-CPLUS-AUTOMERGE-AND-COVERAGE.md` | **Claude Opus 5.5 (`AGY claude+gpt`)** + 4x Sonnet Squad | `61.1 KB` | Read-Only Investigation Dossier: `BH1-21`..`BH1-30` |
| **34** | `/home/mboyle/bd-persist/bd-kb/34-READONLY-DOSSIER-BH1-ROWS-31-TO-40-CLAIMS-RETIRE-FEEDER-AND-ONESHOT.md` | **Claude Opus 5.5 (`AGY claude+gpt`)** + 4x Sonnet Squad | `59.8 KB` | Read-Only Investigation Dossier: `BH1-31`..`BH1-40` |
| **35** | `/home/mboyle/bd-persist/bd-kb/35-READONLY-DOSSIER-BH1-ROWS-41-TO-48-CLAUDE-JSON-TELEMETRY-AND-BD-CORE.md` | **Claude Opus 5.5 (`AGY claude+gpt`)** + 4x Sonnet Squad | `50.7 KB` | Read-Only Investigation Dossier: `BH1-41`..`BH1-48` |
| **36** | `/home/mboyle/bd-persist/bd-kb/36-READONLY-DOSSIER-BH1-ROWS-49-TO-55-ORIGIN-GUARD-COCKPIT-AND-COST-SENSOR.md` | **Claude Opus 5.5 (`AGY claude+gpt`)** + 4x Sonnet Squad | `56.0 KB` | Read-Only Investigation Dossier: `BH1-49`..`BH1-55` |
| **37** | `/home/mboyle/bd-persist/bd-kb/37-CODEX-ASTRA-55ROW-CONFLICT-MATRIX-AND-WAVE-BATCHING-DOSSIER.md` | **Codex Astra (`gpt-6-astra`)** + Sonnet Squad | `62.7 KB` | Master 55-Row File-Overlap Conflict Matrix & Wave Batching |
| **38** | `/home/mboyle/bd-persist/bd-kb/38-CODEX-SOL-PM-FABLE-VS-OPUS-ADVISORY-AND-QA-COUNCIL-BRIEFING.md` | **Codex Sol (`gpt-5.6-sol`)** + Sonnet Squad | `27.0 KB` | PM Model Advisory (`Fable 5.1` + `Adjudicator Opus 5.5`) & Q&A Guide |
