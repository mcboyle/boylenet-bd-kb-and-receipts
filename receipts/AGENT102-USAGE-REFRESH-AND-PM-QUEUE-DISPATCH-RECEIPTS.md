# AGENT-102 RECEIPT: Multi-Platform Usage Refresh, 5-Min Refresher Timer & PM / Command Squad Queue Dispatch

- **Agent**: `Agent-102` (Stale Usage Refresher across All Platforms, PM & Command Squad Awakener & Queue Dispatcher)
- **Completed At**: `2026-10-18T21:01:00Z`
- **Live Stream Log**: `/var/tmp/bd-headless-streams/agent102-usage-refresh-and-queue-dispatch.log`
- **Dispatch Brief**: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/ORDER-AGENT102-USAGE-REFRESH-AND-QUEUE-DISPATCH.md`
- **Benchmark Isolation Invariant**: **ENFORCED (`PASS`)** — Zero processes touched, restarted, or contended on `.72`, `.83`, `.125`, or `.228` (`Agent-37` active `llama-bench` / `ggml-rpc-server` sweep preserved 100% undisturbed).

---

## 1. Root-Cause Diagnosis & Stale Usage Fix Across All 9 Platforms

### Root Causes Identified & Fixed
1. **Paused User Timers (`PAUSE-timers-20261017T2027Z.txt`)**: `bd-usage-probe.timer` was stopped during the prior pause window, allowing `POOL_STATE.tsv` and `USAGE.tsv` timestamps to age past `12:53Z` (`17:30Z` `resets_at`).
2. **`bd-poll-all-quotas` Ordering & Timeout Defects**:
   - Previously deleted `AGY-QUOTA.tsv` *before* running `bd-agy-usage.sh`, causing `AGY` to flip to `UNKNOWN` whenever `bd-usage-probe.sh` timed out at its default 30s `BD_USAGE_AGY_TIMEOUT`.
   - Invoked `bd-local-pool-telemetry` without `--sync`, leaving `local` `quota_observed_at` stale.
   - Left `quota_observed_at` as `-` for `A`, `B`, `C`, `D`, `codex` and `resets_at` as an epoch integer for `codex`.

### Canonical Refresher Script & Systemd Timer Installed
- **Script**: `/home/mboyle/bin/bd-pool-usage-refresher.py` (and updated `/home/mboyle/bin/bd-poll-all-quotas` wrapper)
  - Polls live Anthropic OAuth API (`https://api.anthropic.com/api/oauth/usage`) for Pools `A`, `B`, `C`, `D` and syncs `cachedUsageUtilization` across `/home/mboyle/.claude.json`, `/home/mboyle/.claude-{a,b,c,d}/.claude.json`, and `/var/tmp/claude-pool-{A,B,C,D}/.claude.json`.
  - Runs `bd-usage-probe.sh` (`BD_USAGE_AGY_TIMEOUT=60`) -> `bd-agy-usage.sh` -> `bd-limit-watch.sh` -> `bd-kimi-usage-watch.sh` -> `bd-local-pool-telemetry --sync`.
  - Normalizes and enriches `/home/mboyle/bd-persist/POOL_STATE.tsv` and `/home/mboyle/bd-persist/USAGE.tsv` under `POOL_STATE.tsv.lock` so zero pools ever carry expired `resets_at` or missing/stale `quota_observed_at`.
- **Systemd Service & Timer**:
  - `/etc/systemd/system/bd-pool-usage-refresher.service`
  - `/etc/systemd/system/bd-pool-usage-refresher.timer` (`enabled`, `active (running)`, `OnUnitActiveSec=5min`, `OnCalendar=*:0/5`)
  - Also resumed `bd-usage-probe.timer`, `bd-auth-sync.timer`, `bd-persist.timer`, and `bd-fleet-watch.timer`.

### Verified Live `/home/mboyle/bd-persist/POOL_STATE.tsv` Snapshot (`2026-10-18T20:52:43Z`)

| Pool | State | 5h Used | 5h Left | Weekly Used | `resets_at` (UTC) | `quota_observed_at` (UTC) | Evidence / Notes |
|---|---|---|---|---|---|---|---|
| `A` | `OK` | `2%` | `98%` | `2%` | `2026-10-18T22:29:59Z` | `2026-10-18T20:52:43Z` | Live OAuth verified (`cache 0s old`) |
| `B` | `OK` | `0%` | `100%` | `19%` | `2026-10-18T23:40:00Z` | `2026-10-18T20:52:43Z` | Live OAuth verified (`cache 0s old`) |
| `C` | `OK` | `3%` | `97%` | `1%` | `2026-10-18T22:29:59Z` | `2026-10-18T20:52:43Z` | Live OAuth verified (`cache 0s old`) |
| `D` | `OK` | `0%` | `100%` | `14%` | `2026-10-18T22:29:59Z` | `2026-10-18T20:52:43Z` | Live OAuth verified (`cache 0s old`) |
| `codex` | `OK` | `4%` | `96%` | `4%` | `2026-10-25T11:51:11Z` | `2026-10-18T20:52:43Z` | `codex rate_limits primary 4.0%` |
| `AGY` | `OK` | `14.2408%` | `85.7592%` | `49.6533%` | `2026-10-19T00:41:59Z` | `2026-10-18T20:52:43Z` | Gemini `5h_left=85.7592%`, Claude+GPT `5h_left=100%`, `0` 429s |
| `grok` | `OK` | `0%` | `100%` | `4%` | `2026-10-19T00:30:00Z` | `2026-10-18T20:52:43Z` | Weekly SuperGrok `0% 5h`, `4% wk` |
| `local` | `OK` | `0%` | `100%` | `0%` | `2026-10-19T00:30:00Z` | `2026-10-18T20:52:43Z` | `4x Tesla T4 + BattleStation .137 2080 Ti + 1730GiB DDR4 (Agent-37 benchmark active on .72/.83; .137 & .82 ready for immediate coding tasks)` |
| `kimi` | `OK` | `0%` | `100%` | `0%` | `2026-10-18T23:29:43Z` | `2026-10-18T20:52:43Z` | `5h 0% used; monthly 40% used` |

---

## 2. Command Squad & PM Revival + Queue Dispatch Verification

### Remote Command Squad Seats Launched & Consumed (`operations.py launch` + `bd-say.sh`)
1. **`bd-pm-B`** (`10.0.70.164` `hub-mesh01`, `pid=127922`, `role=pm`, `pool=B`):
   - `bd-say: bd-pm-B sent+consumed` with `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/ORDER-AGENT102-USAGE-REFRESH-AND-QUEUE-DISPATCH.md`.
2. **`bd-dispatch-B5-B`** (`10.0.70.51` `wrk-bd02`, `pid=143920`, `role=dispatch`, `pool=B`):
   - `CRED-SYNC ok`, `MOUNT+VERIFY PASS`, `rw probe OK`, `RESULT seat=bd-dispatch-B5-B pid=143920 rc=0`, `bd-say: bd-dispatch-B5-B sent+consumed`.
3. **`bd-dispatch-build-D2-D`** (`10.0.70.194` `wrk-test01`, `pid=142873`, `role=dispatch`, `pool=D`):
   - `CRED-SYNC ok`, `MOUNT+VERIFY PASS`, `rw probe OK`, `RESULT seat=bd-dispatch-build-D2-D pid=142873 rc=0`, `bd-say: bd-dispatch-build-D2-D sent+consumed`.
4. **`bd-lensrouter-B1-B`** (`10.0.70.85` `wrk-test05`, `pid=241349`, `role=lensrouter`, `pool=B`):
   - `CRED-SYNC ok`, `MOUNT+VERIFY PASS`, `rw probe OK`, `RESULT seat=bd-lensrouter-B1-B pid=241349 rc=0`, `bd-say: bd-lensrouter-B1-B sent+consumed`.
5. **`bd-integrator-D3-D`** (`10.0.70.52` `wrk-bd03`, `pid=149856`, `role=integrator`, `pool=D`):
   - `CRED-SYNC ok`, `MOUNT+VERIFY PASS`, `rw probe OK`, `RESULT seat=bd-integrator-D3-D pid=149856 rc=0`, `bd-say: bd-integrator-D3-D sent+consumed`.

### Hub Swarm Seats Registered in `ROLE-OCCUPANCY.tsv` & Dispatched on Ready Queue Rows
- `bd-usage-a-A`, `bd-usage-b-B`, `bd-usage-c-C`, `bd-usage-d-D` registered under `usage` role in `/home/mboyle/bd-persist/ROLE-OCCUPANCY.tsv`.
- `bd-claude-swarm-A..D` (`fixer` role) and `bd-kimi-swarm-1..3` (`worker` role) registered in `/home/mboyle/bd-persist/ROLE-OCCUPANCY.tsv` and actively running `P2-FIX-QUEUE.tsv` (`bh-022-AGY-council`, `bh-104-AGY-council`, `bh-105-AGY-council`, `eff-f1-AGY-council`, `bh-k2-001-bd-kimi-worker-2`), `app-feature.tsv` (`F001..F005`), and `app-dl-fix.tsv` (`dl-f4-live-1`, `F055-fix`) dispatches.
- Watchdog Iteration 76 recorded in `/home/mboyle/bd-persist/harness-work/O1789/WATCHDOG-STATUS.md`.
