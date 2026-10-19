# AGENT-42 FLEET-WIDE AI CLI BINARY UPGRADE & PARITY RECONCILIATION RECEIPT (O1789)

- **Timestamp (UTC)**: `2026-10-18T10:10:01Z`
- **Seat**: `Agent-42 (Fleet-Wide AI CLI Binary Updater & Parity Reconciler)`
- **Controller**: `10.0.70.164 (hub-mesh01)`
- **Audit Artifact**: `/home/mboyle/bd-persist/FLEET_PARITY_AUDIT.json`
- **Parity Scripts Updated**:
  - `/home/mboyle/bin/bd-fleet-sync-parity.sh`
  - `/home/mboyle/bin/bd-fleet-reconcile-all.py`
  - `/home/mboyle/bin/bd-fleet-parity-audit.py`

---

## 1. Controller (`10.0.70.164`) Upstream AI CLI Binary Matrix (Before vs. After)

| CLI Tool | Binary / Wrapper Path | Before (`.164`) | After (`.164` & Fleet Target) | Wrapper / Symlink Topology Preserved |
| :--- | :--- | :--- | :--- | :--- |
| **Claude Code (`claude`)** | `~/.local/bin/claude` & `/usr/local/bin/claude` | `2.1.302` (`~/.local/bin`), `2.1.287` (`/usr/local/bin`) | **`2.1.302 (Claude Code)`** (both paths) | `~/.local/bin/claude -> ~/.local/share/claude/versions/2.1.302` |
| **OpenAI Codex (`codex`)** | `~/.local/bin/codex` & `~/.npm-global/bin/codex` | `0.163.0` (`~/.local/bin`), `0.148.0` (`~/.npm-global/bin`) | **`0.163.0`** (both paths) | `~/.local/bin/codex -> ~/.codex/packages/standalone/current/bin/codex` |
| **Kimi Code (`kimi`)** | `~/.local/bin/kimi` | `2.2.0` | **`2.2.0`** (latest CDN release) | `~/.local/bin/kimi -> ~/.local/bin/kimi-real -> ~/.kimi-code/bin/kimi` |
| **Google Gemini CLI (`gemini`)** | `~/.local/bin/gemini` & `/usr/bin/gemini` | `0.63.0` | **`0.64.0`** | `~/.local/bin/gemini` wrapper (`GEMINI_CLI_TRUSTED_FOLDERS_PATH` -> `direct_gemini_trustedFolders.json`) -> `bundle/gemini.js` |
| **OpenCode (`opencode`)** | `~/.local/bin/opencode` & `/usr/bin/opencode` | `absent` (`.83` had `1.18.30`) | **`1.19.3`** | `~/.local/bin/opencode -> ~/.local/bin/opencode-real` |
| **Grok CLI (`grok`)** | `~/.local/bin/grok` | `1.0.60 (01a8e1c591c0)` | **`1.0.62 (1474f9cbdd91)`** | `~/.local/bin/grok -> ~/.local/bin/grok-real -> ~/.grok/bin/grok -> ../downloads/grok-1.0.62-linux-x86_64` |
| **Antigravity CLI (`agy`)** | `~/.local/bin/agy` | `1.4.0` | **`1.4.0`** | `~/.local/bin/agy -> ~/.local/bin/agy-real` (atomic rsync, zero in-place overwrite) |
| **Caveman CLI (`caveman`)** | `~/.local/bin/caveman` & `/usr/bin/caveman` | `4.0.1 (bin-v4.0.1)` | **`4.0.1 (bin-v4.0.1)`** | `~/.local/bin/caveman -> ~/.local/lib/node_modules/@caveman-ai/cli/dist/index.js` + `~/.caveman/bin/*` |
| **Codeburn (`codeburn`)** | `~/.local/bin/codeburn` & `/usr/bin/codeburn` | `0.9.27` | **`0.9.27`** | `~/.local/bin/codeburn -> ~/.local/lib/node_modules/codeburn/dist/cli.js` |
| **Firecrawl CLI (`firecrawl`)** | `~/.local/bin/firecrawl` & `/usr/bin/firecrawl` | `1.26.3` | **`1.26.9`** | `~/.local/bin/firecrawl -> ~/.local/lib/node_modules/firecrawl-cli/dist/index.js` |
| **Local Harnesses (`bd-*-local`)** | `/home/mboyle/bin/bd-*-local` | Present on `.164` | **Synced across all 17 reachable nodes** | `bd-{claude,codex,opencode,hermes,deepseek,openrouter}-local -> bd-ai-harness` |

---

## 2. Fleet-Wide Before vs. After Version Matrix (All 18 Inspected Reachable Hosts)

| Host IP | Node Name | Phase | `claude` | `codex` | `kimi` | `gemini` | `opencode` | `grok` | `agy` | `caveman` | `codeburn` | `firecrawl` | `bd-*-local` |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `10.0.70.164` | `hub-mesh01 (controller)` | **BEFORE** | `2.1.302 (usr: 2.1.287)` | `0.163.0` | `2.2.0` | `0.63.0` | `absent` | `1.0.60 (01a8e1c591c0) [stable]` | `1.4.0` | `{` | `0.9.27` | `1.26.3` | `True` |
| `10.0.70.164` | `hub-mesh01 (controller)` | **AFTER** | **`2.1.302`** | **`0.163.0`** | **`2.2.0`** | **`0.64.0`** | **`1.19.3`** | **`1.0.62 (1474f9cbdd91)`** | **`1.4.0`** | **`4.0.1 (bin-v4.0.1)`** | **`0.9.27`** | **`1.26.9`** | **`True`** |
| `10.0.70.95` | `test2 (wrk-test02)` | **BEFORE** | `2.1.301` | `0.163.0` | `2.2.0` | `absent` | `absent` | `1.0.60 (01a8e1c591c0) [stable]` | `1.4.0` | `{` | `absent` | `absent` | `False` |
| `10.0.70.95` | `test2 (wrk-test02)` | **AFTER** | **`2.1.302`** | **`0.163.0`** | **`2.2.0`** | **`0.64.0`** | **`1.19.3`** | **`1.0.62 (1474f9cbdd91)`** | **`1.4.0`** | **`4.0.1 (bin-v4.0.1)`** | **`0.9.27`** | **`1.26.9`** | **`True`** |
| `10.0.70.80` | `test3 (wrk-test03)` | **BEFORE** | `2.1.302` | `0.163.0` | `2.2.0` | `absent` | `absent` | `1.0.60 (01a8e1c591c0) [stable]` | `1.4.0` | `{` | `0.7.2` | `1.23.3` | `True` |
| `10.0.70.80` | `test3 (wrk-test03)` | **AFTER** | **`2.1.302`** | **`0.163.0`** | **`2.2.0`** | **`0.64.0`** | **`1.19.3`** | **`1.0.62 (1474f9cbdd91)`** | **`1.4.0`** | **`4.0.1 (bin-v4.0.1)`** | **`0.9.27`** | **`1.26.9`** | **`True`** |
| `10.0.70.249` | `test6 (wrk-test06)` | **BEFORE** | `2.1.302` | `0.163.0` | `2.2.0` | `absent` | `absent` | `1.0.60 (01a8e1c591c0) [stable]` | `1.4.0` | `{` | `0.7.2` | `1.23.3` | `True` |
| `10.0.70.249` | `test6 (wrk-test06)` | **AFTER** | **`2.1.302`** | **`0.163.0`** | **`2.2.0`** | **`0.64.0`** | **`1.19.3`** | **`1.0.62 (1474f9cbdd91)`** | **`1.4.0`** | **`4.0.1 (bin-v4.0.1)`** | **`0.9.27`** | **`1.26.9`** | **`True`** |
| `10.0.70.85` | `test4 (wrk-test05)` | **BEFORE** | `2.1.302` | `0.163.0` | `2.2.0` | `absent` | `absent` | `1.0.60 (01a8e1c591c0) [stable]` | `1.4.0` | `{` | `0.7.2` | `1.23.3` | `True` |
| `10.0.70.85` | `test4 (wrk-test05)` | **AFTER** | **`2.1.302`** | **`0.163.0`** | **`2.2.0`** | **`0.64.0`** | **`1.19.3`** | **`1.0.62 (1474f9cbdd91)`** | **`1.4.0`** | **`4.0.1 (bin-v4.0.1)`** | **`0.9.27`** | **`1.26.9`** | **`True`** |
| `10.0.70.84` | `test7 (wrk-test07)` | **BEFORE** | `2.1.302` | `0.163.0` | `2.2.0` | `absent` | `absent` | `1.0.60 (01a8e1c591c0) [stable]` | `1.4.0` | `{` | `0.7.2` | `1.23.3` | `True` |
| `10.0.70.84` | `test7 (wrk-test07)` | **AFTER** | **`2.1.302`** | **`0.163.0`** | **`2.2.0`** | **`0.64.0`** | **`1.19.3`** | **`1.0.62 (1474f9cbdd91)`** | **`1.4.0`** | **`4.0.1 (bin-v4.0.1)`** | **`0.9.27`** | **`1.26.9`** | **`True`** |
| `10.0.70.51` | `bd1 (wrk-bd02)` | **BEFORE** | `2.1.302` | `0.163.0` | `2.2.0` | `0.63.0` | `absent` | `1.0.60 (01a8e1c591c0) [stable]` | `1.4.0` | `{` | `0.7.2` | `1.23.3` | `True` |
| `10.0.70.51` | `bd1 (wrk-bd02)` | **AFTER** | **`2.1.302`** | **`0.163.0`** | **`2.2.0`** | **`0.64.0`** | **`1.19.3`** | **`1.0.62 (1474f9cbdd91)`** | **`1.4.0`** | **`4.0.1 (bin-v4.0.1)`** | **`0.9.27`** | **`1.26.9`** | **`True`** |
| `10.0.70.52` | `bd2 (wrk-bd03)` | **BEFORE** | `2.1.302` | `0.163.0` | `2.2.0` | `0.63.0` | `absent` | `1.0.60 (01a8e1c591c0) [stable]` | `1.4.0` | `{` | `0.7.2` | `1.23.3` | `True` |
| `10.0.70.52` | `bd2 (wrk-bd03)` | **AFTER** | **`2.1.302`** | **`0.163.0`** | **`2.2.0`** | **`0.64.0`** | **`1.19.3`** | **`1.0.62 (1474f9cbdd91)`** | **`1.4.0`** | **`4.0.1 (bin-v4.0.1)`** | **`0.9.27`** | **`1.26.9`** | **`True`** |
| `10.0.70.82` | `test3big (wrk-test04)` | **BEFORE** | `2.1.301` | `0.163.0` | `2.2.0` | `absent` | `absent` | `1.0.60 (01a8e1c591c0) [stable]` | `1.4.0` | `{` | `0.7.2` | `1.23.3` | `False` |
| `10.0.70.82` | `test3big (wrk-test04)` | **AFTER** | **`2.1.302`** | **`0.163.0`** | **`2.2.0`** | **`0.64.0`** | **`1.19.3`** | **`1.0.62 (1474f9cbdd91)`** | **`1.4.0`** | **`4.0.1 (bin-v4.0.1)`** | **`0.9.27`** | **`1.26.9`** | **`True`** |
| `10.0.70.194` | `test1 (wrk-test01)` | **BEFORE** | `2.1.302` | `0.163.0` | `2.2.0` | `0.63.0` | `absent` | `1.0.60 (01a8e1c591c0)` | `1.4.0` | `{` | `0.7.2` | `1.23.3` | `True` |
| `10.0.70.194` | `test1 (wrk-test01)` | **AFTER** | **`2.1.302`** | **`0.163.0`** | **`2.2.0`** | **`0.64.0`** | **`1.19.3`** | **`1.0.62 (1474f9cbdd91)`** | **`1.4.0`** | **`4.0.1 (bin-v4.0.1)`** | **`0.9.27`** | **`1.26.9`** | **`True`** |
| `10.0.70.72` | `ai-srv01 (GPU)` | **BEFORE** | `absent` | `absent` | `absent` | `absent` | `absent` | `absent` | `1.4.0` | `absent` | `absent` | `absent` | `False` |
| `10.0.70.72` | `ai-srv01 (GPU)` | **AFTER** | **`2.1.302`** | **`0.163.0`** | **`2.2.0`** | **`0.64.0`** | **`1.19.3`** | **`1.0.62 (1474f9cbdd91)`** | **`1.4.0`** | **`4.0.1 (bin-v4.0.1)`** | **`0.9.27`** | **`1.26.9`** | **`True`** |
| `10.0.70.125` | `ai-infer01 (GPU)` | **BEFORE** | `2.1.287` | `0.154.0` | `absent` | `absent` | `absent` | `absent` | `1.4.0` | `{` | `absent` | `absent` | `False` |
| `10.0.70.125` | `ai-infer01 (GPU)` | **AFTER** | **`2.1.302`** | **`0.163.0`** | **`2.2.0`** | **`0.64.0`** | **`1.19.3`** | **`1.0.62 (1474f9cbdd91)`** | **`1.4.0`** | **`4.0.1 (bin-v4.0.1)`** | **`0.9.27`** | **`1.26.9`** | **`True`** |
| `10.0.70.228` | `ai-ollama01 (GPU)` | **BEFORE** | `2.1.269` | `0.154.0` | `absent` | `absent` | `absent` | `absent` | `1.4.0` | `{` | `absent` | `absent` | `False` |
| `10.0.70.228` | `ai-ollama01 (GPU)` | **AFTER** | **`2.1.302`** | **`0.163.0`** | **`2.2.0`** | **`0.64.0`** | **`1.19.3`** | **`1.0.62 (1474f9cbdd91)`** | **`1.4.0`** | **`4.0.1 (bin-v4.0.1)`** | **`0.9.27`** | **`1.26.9`** | **`True`** |
| `10.0.70.25` | `ops-mcp01` | **BEFORE** | `2.1.301` | `0.163.0` | `absent` | `absent` | `absent` | `absent` | `1.4.0` | `{` | `absent` | `absent` | `False` |
| `10.0.70.25` | `ops-mcp01` | **AFTER** | **`2.1.302`** | **`0.163.0`** | **`2.2.0`** | **`0.64.0`** | **`1.19.3`** | **`1.0.62 (1474f9cbdd91)`** | **`1.4.0`** | **`4.0.1 (bin-v4.0.1)`** | **`0.9.27`** | **`1.26.9`** | **`True`** |
| `10.0.70.162` | `ops-cicd01` | **BEFORE** | `2.1.287` | `0.154.0` | `absent` | `absent` | `absent` | `absent` | `absent` | `{` | `absent` | `absent` | `False` |
| `10.0.70.162` | `ops-cicd01` | **AFTER** | **`2.1.302`** | **`0.163.0`** | **`2.2.0`** | **`0.64.0`** | **`1.19.3`** | **`1.0.62 (1474f9cbdd91)`** | **`1.4.0`** | **`4.0.1 (bin-v4.0.1)`** | **`0.9.27`** | **`1.26.9`** | **`True`** |
| `10.0.70.107` | `ops-test01` | **BEFORE** | `absent` | `absent` | `absent` | `absent` | `absent` | `absent` | `absent` | `absent` | `absent` | `absent` | `False` |
| `10.0.70.107` | `ops-test01` | **AFTER** | **`2.1.302`** | **`0.163.0`** | **`2.2.0`** | **`0.64.0`** | **`1.19.3`** | **`1.0.62 (1474f9cbdd91)`** | **`1.4.0`** | **`4.0.1 (bin-v4.0.1)`** | **`0.9.27`** | **`1.26.9`** | **`True`** |
| `10.0.70.182` | `stg-cache01` | **BEFORE** | `2.1.287` | `0.154.0` | `absent` | `absent` | `absent` | `absent` | `absent` | `{` | `absent` | `absent` | `False` |
| `10.0.70.182` | `stg-cache01` | **AFTER** | **`2.1.302`** | **`0.163.0`** | **`2.2.0`** | **`0.64.0`** | **`1.19.3`** | **`1.0.62 (1474f9cbdd91)`** | **`1.4.0`** | **`4.0.1 (bin-v4.0.1)`** | **`0.9.27`** | **`1.26.9`** | **`True`** |
| `10.0.70.83` | `aidev (GPU)` | **BEFORE** | `2.1.287` | `0.154.0` | `absent` | `absent` | `1.18.30` | `absent` | `1.4.0` | `{` | `absent` | `absent` | `False` |
| `10.0.70.83` | `aidev (GPU)` | **AFTER** | *(offline / powered down during sync window: `Destination Host Unreachable`)* | - | - | - | - | - | - | - | - | - | - |

---

## 3. Powered-Off / Cold-Spare Fleet Nodes Checked (22 Hosts)

The following 22 cold-spare / powered-off nodes in `/home/mboyle/.config/bd/hosts` and `bd-fleet-parity-audit.py` returned `No route to host` (powered off per Rule 61 cold-spare elasticity) during both baseline and post-sync probes:

| Host IP | Node Name | Status |
| :--- | :--- | :--- |
| `10.0.70.50` | `wrk-bd01` | `UNREACHABLE (Cold Spare / No route to host)` |
| `10.0.70.53` | `wrk-bd04` | `UNREACHABLE (Cold Spare / No route to host)` |
| `10.0.70.54` | `wrk-bd05` | `UNREACHABLE (Cold Spare / No route to host)` |
| `10.0.70.120` | `spr-pool01` | `UNREACHABLE (Cold Spare / No route to host)` |
| `10.0.70.183` | `spr-pool02` | `UNREACHABLE (Cold Spare / No route to host)` |
| `10.0.70.149` | `spr-pool05` | `UNREACHABLE (Cold Spare / No route to host)` |
| `10.0.70.197` | `spare1` | `UNREACHABLE (Cold Spare / No route to host)` |
| `10.0.70.193` | `node1 (spare2)` | `UNREACHABLE (Cold Spare / No route to host)` |
| `10.0.70.81` | `spare4` | `UNREACHABLE (Cold Spare / No route to host)` |
| `10.0.70.185` | `spare5` | `UNREACHABLE (Cold Spare / No route to host)` |
| `10.0.70.186` | `spare6` | `UNREACHABLE (Cold Spare / No route to host)` |
| `10.0.70.187` | `node2 (spare9)` | `UNREACHABLE (Cold Spare / No route to host)` |
| `10.0.70.184` | `spare8` | `UNREACHABLE (Cold Spare / No route to host)` |
| `10.0.70.196` | `spare10` | `UNREACHABLE (Cold Spare / No route to host)` |
| `10.0.70.195` | `spare11` | `UNREACHABLE (Cold Spare / No route to host)` |
| `10.0.70.35` | `seatc1` | `UNREACHABLE (Cold Spare / No route to host)` |
| `10.0.70.171` | `seatc2` | `UNREACHABLE (Cold Spare / No route to host)` |
| `10.0.70.121` | `cir1` | `UNREACHABLE (Cold Spare / No route to host)` |
| `10.0.70.86` | `cir2` | `UNREACHABLE (Cold Spare / No route to host)` |
| `10.0.70.122` | `spare3` | `UNREACHABLE (Cold Spare / No route to host)` |
| `10.0.70.224` | `spare` | `UNREACHABLE (Cold Spare / No route to host)` |
| `10.0.70.198` | `kvcache-cx1` | `UNREACHABLE (Cold Spare / No route to host)` |

---

## 4. Root-Cause Fixes Applied to Fleet Parity Tooling

1. **SSH ControlSocket Isolation (`-o ControlMaster=no -o ControlPath=none`)**: Added to `/home/mboyle/bin/bd-fleet-sync-parity.sh`, `/home/mboyle/bin/bd-fleet-reconcile-all.py`, and `/home/mboyle/bin/bd-fleet-parity-audit.py` so GPU nodes (`.72`, `.83`, `.125`, `.228`) and ops nodes never hang on stale multiplex sockets.
2. **Gemini CLI `trustedFolders.json` Isolation**: `@google/gemini-cli 0.64.0` strictly validates `~/.gemini/trustedFolders.json` against `{TRUST_FOLDER, TRUST_PARENT, DO_NOT_TRUST}`, whereas Antigravity (`agy`) writes `"TRUSTED"` into `~/.gemini/trustedFolders.json`. Deployed `~/.local/bin/gemini` wrapper across all nodes exporting `GEMINI_CLI_TRUSTED_FOLDERS_PATH=/home/mboyle/.gemini/direct_gemini_trustedFolders.json` and synced `direct_gemini_trustedFolders.json` fleet-wide.
3. **Node.js `v22.23.2` Runtime Parity on `.72` & `.107`**: Nodes `10.0.70.72` (`v18.19.1`) and `10.0.70.107` (no `node`) lacked Node >= 22 required by `gemini`, `codeburn`, `caveman`, and `firecrawl`. `bd-fleet-sync-parity.sh` now automatically provisions `/home/mboyle/.local/bin/node` (`v22.23.2`) on any host with Node `< v22`.
4. **Zero-Internet LAN Binary Distribution**: Replaced slow per-host remote internet downloads in `bd-fleet-sync-parity.sh` with LAN `rsync` from controller `.164` for `claude` (`2.1.302`), `codex` (`0.163.0`), `kimi` (`2.2.0`), `gemini` (`0.64.0`), `opencode` (`1.19.3`), `grok` (`1.0.62`), `agy` (`1.4.0`), `caveman` (`4.0.1`), `codeburn` (`0.9.27`), `firecrawl` (`1.26.9`), and `bd-*-local` wrappers.
