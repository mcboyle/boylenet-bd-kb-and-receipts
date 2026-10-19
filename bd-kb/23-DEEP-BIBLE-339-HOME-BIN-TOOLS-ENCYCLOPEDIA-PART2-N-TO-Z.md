# BD-KB Volume 23: Complete Encyclopedia of /home/mboyle/bin/bd* Tools & Libraries (Part 2: N through Z + lib_*)
> **Author**: `Spec-223` (`AGY claude+gpt (claude-sonnet-5-5-high)`)
> **Generated At**: `2026-10-19T22:58:35Z`
> **Scope**: Strictly Read-Only Infrastructure, Tools, Services, LLMs & PM Catch-Up Documentation (`O3416` Compliant)

---

# BD-KB Volume 23: Encyclopedia of `/home/mboyle/bin/bd*` Tools & Libraries
## Part 2: N through Z, plus `lib_*` (Spec-223)

> [!NOTE]
> **Source limits.** This volume is built only from the first 20 lines (`head -n 20`) of each file. It comes from the glob `bd-[n-z]*`, `bd_[n-z]*` and `lib_*`.
> - A flag or file appears below only if it is visible in that header text.
> - Where the header is silent, the entry says **"not visible in header"**.
> - **Provenance labels are inferred** from header markers such as `Agent-NNN`, `Swarm`, `O####` and `PM bd-pm-…`. No file states "harness-managed" or "AGY-built" outright. Each label carries a confidence note.
> - The listing returned **no `bd_[n-z]*` files and no `lib_*` files**. See [§6](#6-lib_-libraries-and-bd_-modules).

---

## 1. Provenance Legend

| Label | Meaning | Evidence used |
|---|---|---|
| **Harness-managed** | Governed by fleet process: operator orders (`O####`), PM roles, lens or ROUTE tickets, fleet Rules | Header cites `O2548`, `O2899`, `PM bd-pm-A3-A`, `ROUTE-lens…`, `Rule 79` and similar |
| **AGY-built** | Authored by an Antigravity swarm agent as a feature or verifier | Header names `Agent-106`, `Agent-113`, `Agent-129`, `Agent-140`, `Swarm #…` or `Mod N` |
| **Unconfirmed** | Header gives no provenance marker | Stated explicitly |

---

## 2. Quick Index

| # | Path | Kind | Provenance |
|---|---|---|---|
| 1 | `bd-o1670-backlog-runner.py` | Python runner | AGY-built (Agent-113) |
| 2 | `bd-openclaw-local` | Python launcher | Unconfirmed (shared `bd-ai-harness`) |
| 3 | `bd-openclaw-web.py` | Python sync/audit (`:4097`) | Unconfirmed (likely AGY-built) |
| 4 | `bd-opencode-local` | Python launcher | Unconfirmed (shared `bd-ai-harness`) |
| 5 | `bd-opencode-web.py` | Python sync/audit (`:4096`) | Unconfirmed (likely AGY-built) |
| 6 | `bd-openrouter-local` | Python launcher | Unconfirmed (shared `bd-ai-harness`) |
| 7 | `bd-openwebui-autonomous-mods-installer.py` | Python installer | AGY-built (Mod 4) |
| 8 | `bd-openwebui-cdp-ui-verifier.py` | Python CDP verifier | AGY-built (Agent-129) |
| 9 | `bd-openwebui-e2e-test.py` | Python E2E suite | AGY-built (Agent-106) |
| 10 | `bd-openwebui-mcp-qdrant-pg-e2e.py` | Python E2E suite | AGY-built (Agent-140) |
| 11 | `bd-openwebui-relief-agent.py` | Python agent | Unconfirmed (O1789 and `bd-role-claim.sh` role) |
| 12 | `bd-openwebui-sync-bdkb-pipelines-50tools.py` | Python synchronizer | Unconfirmed (likely AGY-built) |
| 13 | `bd-openwebui-sync-tools-skills-mcp.py` | Python synchronizer | Unconfirmed (no docstring) |
| 14 | `bd-openwebui-tool-gateway.py` | Python HTTP gateway (`:4079`) | Unconfirmed (no docstring) |
| 15 | `bd-ops` / `bd-ops-web.py` | Python NL ops shell (`:4089`) | AGY-built (Swarms #17 & #C9) |
| 16 | `bd-oracle` | Python CDP client | Unconfirmed |
| 17 | `bd-oracle-seat-daemon.py` | Python daemon | Harness-managed (Rule 79, O1789) |
| 18 | `bd-oracle-suite-verifier.py` | Python verifier | Unconfirmed (follows named skills) |
| 19 | `bd-os-shell.py` | Python web shell (`:4085`) | AGY-built (Pillars 1 & 7, `#UX-*`) |
| 20 | `bd-overnight-watchdog.py` | Python watchdog | Unconfirmed (likely AGY-built) |
| 21 | `bd-p2-appdl-swarm-verifier` | Python verifier | Unconfirmed (swarm-style) |
| 22 | `bd-pick-host` (+ 6 `.pre-*` backups) | Bash | **Harness-managed** (O2548, O2899, O1789) |

---

## 3. Entries: `bd-o*` Family

### 3.1 `/home/mboyle/bin/bd-o1670-backlog-runner.py`
- **Purpose:** Automated verification, re-exercise and reconciliation of the 69-row `O1670-QUEUE.tsv` backlog (Agent-113 / Swarm 5).
- **Invariants:**
  - It never touches `.72` or `.83`, to avoid contention with `bench_worker.py`.
  - It offloads all pytest verification and delta probes to `.82` (`10.0.70.82`, 64-core `/dev/shm` ramdisk).
  - It formats output through `/home/mboyle/bin/bd-pytest-digest`.
  - It performs zero destructive git operations (Rule 22, Anti-Hallucination Shield).
- **Files read:** `O1670-QUEUE.tsv`. The path is not shown in the header.
- **Files written:** not visible in header.
- **Imports (visible):** `datetime`, `hashlib`, `json`, `re`, `subprocess` and `sys`. The `hashlib` import suggests digest computation, but the header does not say so.
- **CLI flags:** not visible in header.
- **Provenance:** AGY-built (Agent-113).

### 3.2 `bd-openclaw-local`, `bd-opencode-local`, `bd-openrouter-local`
These three files share an **identical header** and implement one program, the **`bd-ai-harness` Unified Multi-Harness CLI & Agent Integration Launcher (Argon-4 + Live Web Tools)**. The invoked name selects the harness, and `bd-openclaw-local` carries the same docstring as the others. The file name and the docstring's own harness list do not line up, so the header alone cannot say how `openclaw` is dispatched.

| Harness | Alias | Backend |
|---|---|---|
| `claude` | `bd-claude-local` | Claude Code CLI via Anthropic `/v1/messages` on LiteLLM `:4000` |
| `codex` | `bd-codex-local` | Codex CLI via boylenet local provider (`:4010/v1`, `:4000/v1`) with full web access |
| `opencode` | `bd-opencode-local` | OpenCode / Aider / Continue local provider (`:4000/v1`, `:4010/v1`) |
| `hermes` | `bd-hermes-local` | Native Hermes / Qwythos / Qwen3 tool-calling loop (bash, file, web_search, web_fetch) |
| `deepseek` | `bd-deepseek-local` | DeepSeek `<think>` reasoning and code runner (R1-70B, V4-Flash, V3-671B) |
| `openrouter-local` | `bd-openrouter-local` | Local OpenRouter-compatible gateway client with auto-tier routing and web tools |

- **Enforced contract:** the Argon-4 Anti-Sycophancy & Anti-Hallucination system prompt, plus PreToolUse hooks, across all six harnesses.
- **Hook sources named:** `~/.claude/settings.json`, `~/.codex/hooks.json`, `bd-token-guard.py` and `shield.py`.
- **CLI flags:** the header imports `argparse`, so flags exist, but they are not visible.
- **Provenance:** Unconfirmed. They are shared-launcher scripts, but the header gives no author marker.

### 3.3 `bd-openclaw-web.py` (port `:4097`) and `bd-opencode-web.py` (port `:4096`)
These are sibling synchronizers and parity auditors. Both aim to ensure their target config exposes four things:
1. `🟢 LOCAL` vs `☁️ CLOUD` model labels across 86+ Local Vault and 8 Cloud Burst models.
2. The `:4079` 50-Toolkit OpenAPI specs: `/openapi.json`, `/web-osint/…`, `/data-science/…`, `/devops-infra/…` and `/media-ai/…`.
3. BD KB search at `http://10.0.70.164:4079/api/tool/bd_kb_search` and the Qdrant collection `bd_master_kb`.
4. A `🛡️ Airgap` badge reading `LOCAL-FIRST ($0.00) • WAN ACTIVE`.

| Script | Files read/written (visible) | Extra |
|---|---|---|
| `bd-openclaw-web.py` | `/home/mboyle/.openclaw/openclaw.json`, `/home/mboyle/.openclaw-dev/openclaw.json`, workspace `AGENTS.md`. It imports `shutil`, which suggests backup or copy. | Uses `urllib.request` |
| `bd-opencode-web.py` | `/home/mboyle/.config/opencode/opencode.json`, `/home/mboyle/.config/opencode/BOYLENET-4079-50TOOLS-BDKB.md` | Defines a `CLOUD_MODELS` set |

- **CLI flags:** not visible in header.
- **Provenance:** Unconfirmed. They are likely AGY-built from the style of the header.

### 3.4 `bd-openwebui-*` Suite

| Script | Purpose | Targets / files |
|---|---|---|
| `…-autonomous-mods-installer.py` (Mod 4) | Installs, activates (`is_active=1`, `is_global=1`), attaches to workspace models and E2E-verifies 3 OpenWebUI mods. They are the filter `boylenet_autonomous_context_and_bdkb_mod` and the actions `action_commit_and_push_gitea_github` and `action_dispatch_local_coding_agent`. | `.164:8090` and `.72:8080`. It imports `argparse`, `base64`, `hmac` and `hashlib`, which suggest JWT-style auth. |
| `…-cdp-ui-verifier.py` (Agent-129) | Deep browser CDP verification of every bar, button, dropdown, Workspace tab and Admin Settings tab. It also checks live state updates (pipeline valves, Open Terminal probe), live chat on `.137:11434` and `.228:11434`, and takes screenshots. | CDP on BattleStation `10.0.10.137:9222` and Bittorrent `10.0.70.181:9222`. It has zero contention on `.72` and `.83`. |
| `…-e2e-test.py` (Agent-106) | Super-suite E2E tester. It creates and syncs 15 custom `agent-*` presets in the `model` table, using all 6 BD KB volumes and 51 `toolIds`. It also covers 28 Open Terminal connections, 5 OpenAPI 3.1 tool servers (`POST /api/v1/configs/tool_servers/verify`), 48 live tool calls, and multimodal/pipeline endpoints on `:4079/v1`. | `.164:8090`, `.72:8080`, `:4079`. Chat uses `qwen2.5-coder:7b` on `.137`. |
| `…-mcp-qdrant-pg-e2e.py` (Agent-140) | Unified verifier for Swarms #017–#021. #017 checks 15 agents' live chat and tools. #018 stresses 83 `/api/tool/<action>` routes. #019 certifies 14 fleet MCP servers. #020 checks 6 Qdrant collections (green, `<15ms`). The header is cut off before #021. | Reads `bd-openwebui-tool-gateway.py` and `…/antigravity-cli/mcp/`. Qdrant is at `10.0.70.125:6333`. Only read-only metadata checks run on `.72:8080`, to protect the Agent-37 benchmark. |
| `…-relief-agent.py` | Registers the 16th agent (`agent-council-relief-commander`), wired to `.83:18080` (Qwen3.6-35B-A3B-Heretic). It maintains a pinned chat thread (O1789) on `.164:8090` and claims the `openwebui-council` role via `bd-role-claim.sh`. | Writes to the OpenWebUI DB and claims a role. It runs at $0.00 cost. |
| `…-sync-bdkb-pipelines-50tools.py` | Embeds 6 BD KB volumes (1024-d) into Qdrant `bd_master_kb`. It syncs `bd-kb-*` knowledge bases with public grants on both OpenWebUI instances, registers 8 pipelines, 6 image/vision models and 5 audio models, and registers Toolkits #31–#50. | Qdrant `10.0.70.125:6333`, gateway `10.0.70.164:4079/v1` |
| `…-sync-tools-skills-mcp.py` | Syncs tools, skills and MCP to OpenWebUI instances. It has no docstring. | It imports module `bd_openwebui_27vm_terminal` (as `vm_term`) from `/home/mboyle/bin` and loads `bd-openwebui-tool-gateway.py` via `importlib` as `gw`. It uses `GATEWAY_URL=http://10.0.70.164:4079` and `QDRANT_URL=http://10.0.70.125:6333`. |
| `…-tool-gateway.py` | Universal HTTP tool gateway serving `/api/tool/<action>` (83 routes per Agent-140). It has no docstring. | Env: `BD_OPENWEBUI_TOOL_GATEWAY_PORT` (default `4079`) and `BD_OPENWEBUI_TOOL_GATEWAY_HOST` (default `0.0.0.0`). It uses `http.server`, `socketserver`, `subprocess` and `glob`. |

**CLI flags:** only the installer and relief agent visibly import `argparse`. Their flags are not shown.

### 3.5 `bd-ops` and `bd-ops-web.py`
- **Same docstring in both files.** It is the Boylenet Natural-Language Ops Shell on port `4089` (`ops.boylenet.themfboyles.org`), from Swarms #17 & #C9.
- **Purpose:** translate natural-language requests into pre-vetted commands across `bd-pool-autobalancer`, `bd-gpu-orchestrator` (`bd-tier-switch`, `:4010`), `bd-infra-sentinel` (`:4099`), `bd-remote-pytest-turbo`, `bd-vault-curator`, `bd-semantic-search`, `bd-hardware-telemetry` (`:4091`) and `bd-cloud-burst` (`:4090`).
- **Backends:**
  - Kimi Code CLI: `/home/mboyle/.local/bin/kimi -p "..."` with `stdin=subprocess.DEVNULL`.
  - Local 4×T4 GPU models: `http://10.0.70.164:4000/v1/chat/completions`.
- **Features:** a deterministic fast-path intent classifier and parameter extractor. A dry-run preview mode classifies requests as READ-ONLY or STATE-CHANGE.
- **Provenance:** AGY-built.

### 3.6 Oracle Family

| Script | Detail |
|---|---|
| `bd-oracle` | `bd-web-oracle-client.py`: the Unified Autonomous Virtual Browser Oracle Client. It bridges consumer subscriptions (Gemini Ultra, ChatGPT Plus, Claude Account A/B) into terminal workflows via CDP on `10.0.70.181:9222`. It defines `KNOWN_NODES` (`181`, `137`, `bittorrent`, `battlestation`) and uses `argparse`, `concurrent.futures` and `websocket`. Flags are not visible. |
| `bd-oracle-seat-daemon.py` | The Active Dual-Node Browser Oracle Seat Daemon (Rule 79). It drives `bd-oracle` across `.137:9222` and `.181:9222`. **Reads:** `/home/mboyle/bd-persist/inbox/bd-oracle-swarm-1/*.md`, plus `Q-*.md`, `ESCALATE-*.md` and contested `REFUTE` lens verdicts under `…/harness-work/O1789/`. **Writes:** consensus receipts to `…/harness-work/O1789/oracle-seats/bd-oracle-swarm-1/`. **Provenance:** harness-managed. |
| `bd-oracle-suite-verifier.py` | Dual-oracle visual, interactive and consensus E2E verifier. The shebang is `/home/mboyle/BulkDownloader/venv/bin/python3`. It follows the `cdp-frame-scroll-controls` and `bd-browser-oracle` skills. It uses an owned temporary window (`newWindow: True`), restores bounds, enables focus emulation and calls `Page.bringToFront`. It never modifies or closes pre-existing tabs. It exercises wallboard modes `1x1`, `2x2`, `3x2`, `4x2` and `cinema`. |

### 3.7 `bd-os-shell.py`
The Unified OS Desktop Shell on port `4085` (`os.boylenet.themfboyles.org`), covering Pillars 1 & 7:
- `#UX-1` left app dock, with `Alt+1..9` shortcuts and iframe switching.
- `#UX-2` Cmd/Ctrl+K command palette (52+ apps, 86 vault models, 40 fleet roles).
- `#UX-3` 36px telemetry and quota bar (Claude A/B/C/D 5h%, cloud credit, tok/s).
- `#UX-4` split-screen modes and a backtick terminal drawer.
- `#UX-5` PiP mini-monitor.
- `#UX-32..35` shared assets `/static/boylenet-nav.js` and `/static/boylenet-ui.css`, a 4-theme engine and a snapshot exporter.
- **Imports:** `argparse` and `http.server`. Flags are not visible. **Provenance:** AGY-built.

### 3.8 `bd-overnight-watchdog.py`
- **Purpose:** an autonomous stall-prevention watchdog. It polls every 300 s.
- **Behaviour:**
  - It scans tmux pane buffers for interactive menus or prompt stalls.
  - It auto-approves benign permission and folder-trust menus.
  - It checks session liveness.
- **Writes:** `/home/mboyle/bd-persist/logs/overnight-watchdog.log` (`LOG_FILE`).
- **Config:** a `SEATS_TO_WATCH` list (visible entries `bd-opus-high-B` and `bd-agy-worker-g1`).
- **CLI flags:** none visible. The imports (`subprocess`, `time`, `datetime`, `pathlib`) suggest a loop with no `argparse`.

### 3.9 `bd-p2-appdl-swarm-verifier`
- **Purpose:** the Autonomous P2-FIX (20-row) and APP-DL-FIX (5-row) Swarm Verifier & Reconciler.
- **Files read:**
  - `/home/mboyle/bd-persist/queues/P2-FIX-QUEUE.tsv`
  - `/home/mboyle/bd-persist/queues/APP-DL-FIX-QUEUE.tsv`
  - `/home/mboyle/bd-persist/queue/app-dl-fix.tsv`
  - `/home/mboyle/bd-persist/queue/app-dl-reprobe.tsv`
  - `/home/mboyle/bd-persist/harness-work/O1789/UNPARK-CENSUS.tsv`
- **Output:** canonical `PATCH-SHA256` digests and `PRECHECK: PASS` gate blocks.
- **Written files:** not visible. **Flags:** it imports `argparse`, but flags are not shown. It uses `dataclasses`.

---

## 4. `bd-pick-host` (Harness-Managed)

**Path:** `/home/mboyle/bin/bd-pick-host` (Bash). **Purpose:** print the least-loaded ready test host (O2548, operator). It is read-only, with one SSH probe per host and a 3 s timeout. The probe is `timeout 5 ssh -o BatchMode=yes -o ConnectTimeout=3 mboyle@<ip>`.

### CLI

| Invocation | Output |
|---|---|
| `bd-pick-host` | One IP: the lowest load per core among reachable hosts that have the BulkDownloader venv |
| `bd-pick-host -n N` | The N best IPs, one per line |
| `bd-pick-host --table` | `name ip load cores score`, best first |
| `bd-pick-host --seat [-n N\|--table]` | **Seat hosts** (O1789 launch-verified-host r1, `ANSWER-dispatch-build-B-0051Z`), the predicate `bd-launch-role.sh --host` gates on |

**`--seat` predicate:**
- (a) The latest `apply=1` MOUNT for the IP in `BD_NFS_LOG` has rc=0, and the latest VERIFY after it has `RESULT=PASS`.
- (b) A live read-write probe (touch and remove a temp file) succeeds in every `BD_SEAT_RW_DIRS` directory.
- A seat host is meant to sit on writable NFS, so the writable-NFS exclusion and venv check do not apply in this mode. Skipped hosts are printed on stderr with the reason.
- Revision r2 (`ROUTE-lensrouter-A-launch-verified-host-r1-REFUTE-HIGH-0257Z`) refuses a blank dir list, because an empty list would probe nothing.

### Environment

| Variable | Default |
|---|---|
| `BD_PICK_HOSTS` | `name:ip` list (live: test1, test2, test3, test4, test6, test7, test3big, spare12, spare1, spare5, spare6, spare8, spare9, spare10, spare11, `test` `.149`, spare7 `.120`, spare4 `.81`) |
| `BD_NFS_LOG` | `/home/mboyle/bd-persist/logs/nfs-share.log` |
| `BD_SEAT_RW_DIRS` | `/mnt/bd/bd-persist /mnt/bd/bd-local-wt /mnt/bd/bd-review-wt /mnt/bd/bd-cuts` |

### Files and exclusion logic
- **Reads:** `nfs-share.log`. **Writes:** only a `mktemp` file, removed by `trap`.
- **Default mode excludes** any host where `/home/mboyle/bd-persist` or `/home/mboyle/BulkDownloader` is a **writable** network mount. It checks `stat -f -L -c %T` for `nfs*|fuse*|cifs*|smb*` and `findmnt` options for `ro`. Read-only NFS was re-admitted after Item B (operator, 2026-10-10).
- **Post-G6 embargo lift** (PM `bd-pm-A3-A`): C+ hosts were restored. Operator order **O2899** (03:46Z) says all ready hosts are admitted and reservations are rescinded.
- **Header erratum:** an edit cited `O2895` in error, and the correct ledger entry is O2899.

### Backup lineage (`.pre-*` files)
These are rollback copies. Each is a prior revision of the same script, with the same name and a suffix. They are **not separate tools**.

| Backup | State captured |
|---|---|
| `.pre-nfs-20261010T0129Z` | Earliest copy. The header shows only the first two lines. |
| `.pre-both-20261010T1330Z` | Excludes only a `BulkDownloader` network mount (Item B). |
| `.pre-G5-20261012T2130Z` | Checks both dirs. Excludes spare4, test5 (hub), spare2 and spare7. 15 hosts. |
| `.pre-N3-20261013T0220Z` | G5/G6 narrowing. Five hosts: test2, test3big, spare6, spare8, spare11. |
| `.pre-O2895-20261013T034656Z` | Narrowed further to test2, test3big and spare11. |
| `.pre-lift-20261013T0402Z` | Cites O2895. Adds test, spare7 and spare4. It also shows `COULD NOT LOOK (no host answered)` with `exit 2`. |
| `.pre-81drain-20261013T1950Z` | Pre-`--seat` live version. The default list is 18 hosts, and the header documents only `-n` and `--table`. |

> [!WARNING]
> The backups are executable-looking files in `bin/`. Do not invoke them in place of `bd-pick-host`.

---

## 5. Cross-Cutting Observations

- **Network anchors:**
  - `.164` is the controller (OpenWebUI `:8090`, gateway `:4079`, LiteLLM `:4000`).
  - `.72:8080` is Vault A.
  - `.83:18080` is the Heretic model.
  - `.82` is the 64-core test/ramdisk host.
  - `10.0.70.125:6333` is Qdrant.
  - `.137:9222` and `.181:9222` are the CDP oracle nodes.
- **Benchmark protection:** `.72` and `.83` are repeatedly shielded from inference or test load so that `bench_worker.py` and the Agent-37 benchmark are unaffected.
- **Cost posture:** relief and ops tooling states $0.00 / local-first operation.
- **Ports:**
  - `:4079` gateway
  - `:4085` OS shell
  - `:4089` ops shell
  - `:4096` OpenCode sync
  - `:4097` OpenClaw sync
  - `:4090`, `:4091` and `:4099` are referenced backends

---

## 6. `lib_*` Libraries and `bd_*` Modules

> [!IMPORTANT]
> **No `lib_*` or `bd_[n-z]*` files were captured** in the supplied command output. This volume does not invent entries for them.

The only confirmed underscore-named dependency is `bd_openwebui_27vm_terminal`:
- It is imported as `vm_term` by `bd-openwebui-sync-tools-skills-mcp.py`.
- It is resolved through `sys.path.insert(0, "/home/mboyle/bin")`.
- Its file location, API and provenance were not shown. It is a candidate for the `bd_[a-m]*` or `bd_[n-z]*` range and should be documented in a follow-up.

---

## 7. Gaps for a Follow-Up Pass

1. Full `--help` output for each `argparse` script (the launcher, oracle, installers, ops and OS shells, and the P2 verifier).
2. Written-file paths for `bd-o1670-backlog-runner.py`, the OpenWebUI sync scripts, the oracle verifiers and the tool gateway.
3. Headers beyond line 20, including the cut-off Swarm #021 in `bd-openwebui-mcp-qdrant-pg-e2e.py`.
4. A file listing for `lib_*` and `bd_*`.
5. Authoritative provenance, taken from ledger or git history rather than header inference.
