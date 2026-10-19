# AGENT-113 (SWARM 5): `O1670-QUEUE.tsv` 69-ROW BACKLOG BURN-DOWN & RE-EXERCISE RECEIPTS

- **WRITTEN-AT**: `2026-10-18T23:41:32Z`
- **ARCHITECT**: `Agent-113` (Swarm 5 — `O1670-QUEUE.tsv` 69-Item Backlog Burn-Down & Re-Exercise Architect)
- **BULKDOWNLOADER HEAD**: `747c3f6cb6286dd80390c84ef7bf0bb3635919cd`
- **HARNESS HEAD**: `e3c85faedd3bed29c72155a8a9f359089b6d8712`
- **RUNNER TOOL**: `/home/mboyle/bin/bd-o1670-backlog-runner.py`
- **RECONCILIATION TSV**: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/O1670-69ROW-RECONCILIATION.tsv`
- **RECONCILIATION JSON**: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/O1670-69ROW-RECONCILIATION.json`
- **LIVE STREAM LOG**: `/var/tmp/bd-headless-streams/agent113-o1670-backlog-burndown.log`
- **BENCHMARK CONTENTION INVARIANT**: Zero commands or connections issued to `.72` or `.83`; all off-host pytest & delta probes executed exclusively on `.82` (`10.0.70.82` `wrk-test04` 64-core `/dev/shm` ramdisk) via `/home/mboyle/bin/bd-pytest-digest`.

---

## 1. Executive Denominator & Burn-Down Summary (Rule 8)

- **`O1670-QUEUE.tsv` Total Physical Rows**: `148` (`150` lines minus header & trailing blank line)
- **`UNPARK-CENSUS-SUMMARY.md` Baseline (2026-10-13T06:39Z)**: `79` closed + `69` non-closed (`65` open, `3` built-unlanded, `1` unknown)
- **Reconciled Post-Execution Breakdown of All `69` Rows**:
  - **`CLOSED-LANDED`**: `27` rows
  - **`CLOSED-LIVE`**: `19` rows
  - **`CLOSED-EXERCISED`**: `4` rows
  - **`CLOSED-DELIVERED`**: `3` rows
  - **`CLOSED-UMBRELLA`**: `3` rows
  - **`BOARDED-READY-FOR-TRAIN`**: `2` rows
  - **`BUILT-LIVE-TEST-STAGED`**: `2` rows
  - **`CLOSED-NO-REPRO`**: `2` rows
  - **`BUILT-READY-FOR-TRAIN`**: `1` rows
  - **`CLOSED-CANCELLED-LIVE`**: `1` rows
  - **`CLOSED-LANDED-AND-SUPERSEDED`**: `1` rows
  - **`CLOSED-PARKED-BY-PM`**: `1` rows
  - **`CLOSED-REFUTED`**: `1` rows
  - **`CLOSED-SUPERSEDED`**: `1` rows
  - **`CLOSED-SUPERSEDED-LIVE`**: `1` rows

| Group | Count | Scope | Outcome |
|---|---:|---|---|
| **Group 1** | `5` | `o1670-r1-t154`, `o1670-r2-testguard`, `o1670-r3-recall-kimi`, `o1670-r5-flaky`, `o1670-r6-mcp-trim` | **5/5 CLOSED** (`3b184f605`+`0.349` coupling ratio; `9d67b0302`+`669cff5`; `3b1b7ee`; `6a9985b8d`; `MCP-TRIM.md` delivered) |
| **Group 2** | `4` | `o1670-r4-s1..s4` (`APP-FEATURE F001–F064` read-only delta re-exercise) | **4/4 CLOSED-EXERCISED** (`R4-s1-1754.md`, `R4-s2-bd-worker-D1.md`, `R4-s3-bd-agy-worker-c1.md`, `R4-s4.md` + `R4-DELTA-SUMMARY-F001-F064.md`) |
| **Group 3** | `60` | Remaining `o1671-*`, `o1672-*`, `o1675-*`, `o1698-*`, `o1713-*`, `sg-*` rows | **55/60 CLOSED** (landed in BD git, deployed live in harness/plugins, or refuted/superseded/parked) + **5/60 BUILT/BOARDED** (`2` BOARDed product cuts verified RED->GREEN on `.82`, `2` BOARDed harness cuts live on disk with repo test staged, `1` r2 product patch staged) |
| **TOTAL** | **`69`** | All `69` non-closed `O1670-QUEUE.tsv` rows from `UNPARK-CENSUS.tsv` | **64/69 Closed/Live/Delivered + 5/69 Built & Verified (`100%` accounted with `RESULT.md` receipts)** |

---

## 2. Off-Host Verification & Delta Probes on `.82` (`10.0.70.82` `/dev/shm` Ramdisk via `bd-pytest-digest`)

- **Target Host**: `10.0.70.82` (`wrk-test04`, 64 vCPUs, `/dev/shm` tmpfs ramdisk)
- **Positive Control (Rule 7 — Prove Probe Can Say YES/FAIL)**: `rc=1` -> `FAIL: test_posctl_fail.py | line 2 | AssertionError: ...`
- **Landed `O1670`/`O1671`/`O1673`/`O1675`/`O1698`/`O1713` Verification Suite (`22` test modules in `/dev/shm`)**: `rc=0` -> **`PASS: 122 passed`**
- **Unlanded BOARDed Cut Delta Probe 1 (`o1671-a07-pgbackend-pragma-quote`, `D65-D` BOARD)**:
  - Unpatched Main + Candidate Test (RED): `rc=1` -> `FAIL: test_o1671_a07_pgbackend_pragma_quote.py | line 43 | AssertionError`
  - Patched Candidate Tree `5725e9b1` (GREEN): `rc=0` -> **`PASS: 14 passed`**
- **Unlanded BOARDed Cut Delta Probe 2 (`o1671-a18-relocate-store-dry-run-mkdir`, `D66-D` BOARD)**:
  - Unpatched Main + Candidate Test (RED): `rc=1` -> `FAIL: test_o1671_a18_relocate_store_dry_run.py | line 46 | AssertionError`
  - Patched Candidate Tree `a1b34603` (GREEN): `rc=0` -> **`PASS: 4 passed`**

---

## 3. Group 1 & Group 2 Deep-Dive Verification (`o1670-r1..r6`)

1. **`o1670-r1-t154` (`O1670 R1 MED`)**:
   - Edge #1 (`detect -> runner_transport` inversion) fixed on `BulkDownloader` `main` via `3b184f605` (`tests/test_o1673_t154_edge1_detect_core_route.py` 17/17 PASS on `.82`); coupling meter fixed in `2c1798640`. Current `main` `coupling_ratio = 0.349` (below pre-T154 `0.362`), superseding rebaseline.
2. **`o1670-r2-testguard` (`O1670 R2 HIGH`)**:
   - Landed on `harness` `main` (`669cff5`) and `BulkDownloader` `main` (`9d67b0302`, `tests/test_o1670_r2_testguard.py` PASS on `.82`). `test-shims/tmux` refuses non-scratch server creation under tests; `bd-role-claim.sh` refuses live claims under test runs.
3. **`o1670-r3-recall-kimi` (`O1670 R3 MED`)**:
   - Product fixture test landed on `BulkDownloader` `main` (`51f498337`) and `bd-recall.py` (`DEFAULT_SRC` + Kimi transcripts with redaction) landed on `harness` `main` (`3b1b7ee`).
4. **`o1670-r4-s1..s4` (`O1670 R4 MED` — `APP-FEATURE F001–F064` Read-Only Re-Exercise)**:
   - `o1670-r4-s1` (`F001–F016`): `/home/mboyle/bd-persist/harness-work/O1670/R4-s1-1754.md` (32/32 HTTP 200; F001 `/cockpit/review` delta fixed in `10853a622`).
   - `o1670-r4-s2` (`F017–F032`): `/home/mboyle/bd-persist/harness-work/O1670/R4-s2-bd-worker-D1.md` (16/16 HTTP 200; 0 new behavioral defects).
   - `o1670-r4-s3` (`F033–F048`): `/home/mboyle/bd-persist/harness-work/O1670/R4-s3-bd-agy-worker-c1.md` (16/16 HTTP 200; F033 `/api/tools` delta documented).
   - `o1670-r4-s4` (`F049–F064`): `/home/mboyle/bd-persist/harness-work/O1670/R4-s4.md` synthesized from `F049..F063-agy-feat-29..33-r2.md` (`13/15` PASS, `2/15` FAIL; F058 & F061 resolved FAIL->PASS; F064 absent in 63-row `APP-FEATURE-QUEUE.tsv`). Consolidated in `/home/mboyle/bd-persist/harness-work/O1670/R4-DELTA-SUMMARY-F001-F064.md`.
5. **`o1670-r5-flaky` (`O1670 R5 MED`)**:
   - CPU-farm isolation round (`o1670-r5-flaky/RESULT.md`, 6/6 PASS on `.120`/`.186`) + teardown watcher/exec failure fixes landed on `BulkDownloader` `main` (`6a9985b8d`, `tests/test_o1670_r5_*.py` PASS on `.82`).
6. **`o1670-r6-mcp-trim` (`O1670 R6 LOW`)**:
   - Read-only per-seat MCP context & PSS audit delivered at `/home/mboyle/bd-persist/harness-work/O1670/MCP-TRIM.md` (`4,619` bytes, measuring 82 Claude transcripts + 21 Codex seats + 35.8 GB hub PSS across 13 MCP servers).

---

## 4. Complete 69-Row Reconciliation Ledger (`O1670-QUEUE.tsv`)

| # | Row ID | Group | Queue Line | Census Class | Reconciled State | Commit / Artifact Reference | Summary |
|---:|---|---|---|---|---|---|---|
| 1 | `o1670-r1-t154` | Group 1 | `T2` | `UNKNOWN` | **`CLOSED-LANDED-AND-SUPERSEDED`** | `3b184f605` | Edge #1 fixed on BD main 3b184f605 + meter 2c1798640; coupling_ratio=0.349 < 0.362 pre-T154 baseline (w3-o1670-r1-t154/DONE.md) |
| 2 | `o1670-r2-testguard` | Group 1 | `T3` | `OPEN` | **`CLOSED-LANDED`** | `9d67b0302 / harness:669cff5` | Landed on harness main 669cff5 + BD main 9d67b0302 (tests/test_o1670_r2_testguard.py) |
| 3 | `o1670-r3-recall-kimi` | Group 1 | `T4` | `OPEN` | **`CLOSED-LANDED`** | `3b1b7eebd / harness:3b1b7ee` | Landed on BD main 51f498337 + harness main 3b1b7ee (bd-recall.py DEFAULT_SRC + Kimi transcripts) |
| 4 | `o1670-r4-s1` | Group 2 | `T5` | `OPEN` | **`CLOSED-EXERCISED`** | `10853a622` | Exercised in harness-work/O1670/R4-s1-1754.md (32/32 HTTP 200); F001 delta fixed in 10853a622 |
| 5 | `o1670-r4-s2` | Group 2 | `T6` | `OPEN` | **`CLOSED-EXERCISED`** | `d35fd5bc1` | Exercised in harness-work/O1670/R4-s2-bd-worker-D1.md (16/16 HTTP 200, 0 behavioral deltas) |
| 6 | `o1670-r4-s3` | Group 2 | `T7` | `OPEN` | **`CLOSED-EXERCISED`** | `d35fd5bc1` | Exercised in harness-work/O1670/R4-s3-bd-agy-worker-c1.md (16/16 HTTP 200, F033 tool deltas documented) |
| 7 | `o1670-r4-s4` | Group 2 | `T8` | `OPEN` | **`CLOSED-EXERCISED`** | `bc57fcfd8` | Exercised in UIUX-20260928/results/F049..F063-agy-feat-29..33-r2.md + synthesized in harness-work/O1670/R4-s4.md (13/15 PASS, 2 resolved deltas) |
| 8 | `o1670-r5-flaky` | Group 1 | `T9` | `OPEN` | **`CLOSED-LANDED`** | `6a9985b8d` | Isolation round 6/6 PASS on .120/.186 + fix landed on BD main 6a9985b8d (w3-o1670-r5-flaky) |
| 9 | `o1670-r6-mcp-trim` | Group 1 | `T10` | `OPEN` | **`CLOSED-DELIVERED`** | `MCP-TRIM.md` | Read-only audit delivered at harness-work/O1670/MCP-TRIM.md (bd-worker-S46-D, 35.8 GB PSS / 82 transcripts) |
| 10 | `o1671-r8a-sites` | Group 3 | `T11` | `OPEN` | **`CLOSED-UMBRELLA`** | `umbrella-r8a` | Umbrella/generator row; child o1671-r8a-ok landed on BD main |
| 11 | `o1671-r8b-ui` | Group 3 | `T12` | `OPEN` | **`CLOSED-UMBRELLA`** | `umbrella-r8b` | Umbrella/generator row; all 7 per-fix children (f040, f003, f019x2, f011, f031, f001) landed on BD main |
| 12 | `o1671-r8c-perf` | Group 3 | `T13` | `OPEN` | **`CLOSED-UMBRELLA`** | `umbrella-r8c` | Umbrella/generator row; all 3 children (sse-reconnect, download-csrf-retry, ytdlp-format-parse) landed on BD main |
| 13 | `o1671-r9-testmech` | Group 3 | `T14` | `OPEN` | **`CLOSED-DELIVERED`** | `TEST-MECHANICAL.md` | Read-only report delivered at harness-work/O1671/TEST-MECHANICAL.md (22/22 shard evidence files verified) |
| 14 | `o1671-r10-agy-claim` | Group 3 | `T15` | `OPEN` | **`CLOSED-SUPERSEDED-LIVE`** | `live:ed33c8ec00ba` | Superseded by o1698-r10-agy-claim-r2; live in harness/bd-launch-agy.sh (sha256 ed33c8ec00ba) |
| 15 | `o1672-launch-adapter-pools-lens` | Group 3 | `T27` | `OPEN` | **`CLOSED-LANDED`** | `5b939a9e6` | Lensed BOARD by D4-r3 (not builder D1) and landed on BD main 5b939a9e6 |
| 16 | `o1671-a07-oidc-account-binding` | Group 3 | `T31` | `OPEN` | **`CLOSED-LANDED`** | `cdf1dbebb` | Landed on BD main cdf1dbebb + follow-ups e442074de, 059fd19cb |
| 17 | `o1671-a12-registrable-domain` | Group 3 | `T33` | `OPEN` | **`CLOSED-LANDED`** | `d4abf43fb` | r2 built by S51-B and landed on BD main d4abf43fb (tests/test_o1671_a12_registrable_domain.py) |
| 18 | `o1671-a16-shell-close-reap` | Group 3 | `T37` | `BUILT-UNLANDED` | **`CLOSED-LANDED`** | `9e478faff` | Landed on BD main 9e478faff (tests/test_o1671_a16_shell_close_reaps.py) |
| 19 | `o1675-breaker-all-pct` | Group 3 | `T42` | `OPEN` | **`CLOSED-CANCELLED-LIVE`** | `live:820e27f0b6ca` | Cancelled by PM 21:16Z; objective holds in live operations.py (refuses only at 100%) |
| 20 | `o1675-stale-gate-could-not-look` | Group 3 | `T43` | `BUILT-UNLANDED` | **`CLOSED-LANDED`** | `0bd1d1d84` | Harness fix live + repo test landed on BD main 0bd1d1d84 (tests/test_o1675_stale_gate_could_not_look.py) |
| 21 | `o1671-a13-batch-retry-confirm-r2` | Group 3 | `T44` | `OPEN` | **`CLOSED-LANDED`** | `f407fffe5` | Landed on BD main f407fffe5 (Execute retry confirm-gated keeping f027 retryPreview) |
| 22 | `sg-band-pg-env` | Group 3 | `T48` | `BUILT-UNLANDED` | **`CLOSED-LANDED`** | `7c91439de / harness:cba5616` | Live in harness HEAD cba5616e3934 + BD main 7c91439de |
| 23 | `o1698-hubgate-iowait-spacing` | Group 3 | `T49` | `OPEN` | **`CLOSED-LIVE`** | `live:820e27f0b6ca` | Installed live in plugins/bd-dispatch-ops/scripts/operations.py (iowait gate + host spacing) |
| 24 | `o1698-fleet-halt-marker` | Group 3 | `T50` | `OPEN` | **`CLOSED-LIVE`** | `live:35a9be7c` | Installed live in harness/bd-halt-check.sh (35a9be7c) + 10 scripts + operations.py |
| 25 | `o1698-adapter-usage-role` | Group 3 | `T51` | `OPEN` | **`CLOSED-LIVE`** | `live:operations.py` | Role 'usage' (USAGE-SEAT-LIVE, exempt from POOL-STATE-UNKNOWN) installed live in operations.py; cut in bd-local-wt/o1698-adapter-usage-role-bd-worker-C2 |
| 26 | `o1698-claim-roots-cache-flock` | Group 3 | `T53` | `OPEN` | **`CLOSED-LIVE`** | `live:fe7cef3c` | Installed live in harness/bd-claim-object.sh (fe7cef3c, single-flight roots_cache under flock) |
| 27 | `o1698-bd-guard-skeleton-fleet-guard` | Group 3 | `T54` | `OPEN` | **`CLOSED-LIVE`** | `live:a77ec26e` | Installed live in bd-tripwire-hook.py (a77ec26e) and plugins/bd-guard |
| 28 | `o1698-checks-before-lens` | Group 3 | `T58` | `OPEN` | **`CLOSED-LANDED`** | `4268ca434` | Landed on BD main 4268ca434 (tests/test_o1698_checks_before_lens.py) |
| 29 | `o1698-verdict-cache-wire` | Group 3 | `T59` | `OPEN` | **`CLOSED-LIVE`** | `live:17ce5358` | Wired live in harness/bd-collect-gate.sh (17ce5358 consults bd-verdict-cache) |
| 30 | `o1698-mutate-fuzz-lane` | Group 3 | `T60` | `OPEN` | **`CLOSED-PARKED-BY-PM`** | `PM-PARK-R7` | Parked by PM ruling (unpark/o1698-mutate-fuzz-lane/RULING-pm-A3-A-r7.md: 'C) PARK the lane. No r7.') |
| 31 | `o1698-visual-diff` | Group 3 | `T61` | `OPEN` | **`CLOSED-LIVE`** | `live:bd-visual-diff.py` | Installed live in harness/bd-visual-diff.py + tour.py (16/16 tests pass) |
| 32 | `o1698-lint-hook` | Group 3 | `T62` | `OPEN` | **`CLOSED-LIVE`** | `harness:5d520c5` | Installed live in harness/bd-lint-hook.py (harness HEAD) + pool settings.json PostToolUse |
| 33 | `o1698-hub-ram-pin` | Group 3 | `T64` | `OPEN` | **`CLOSED-LANDED`** | `8f3ab45c7` | Landed on BD main 8f3ab45c7 (tests/test_o1698_hub_ram_pin.py) |
| 34 | `o1698-quota-routing` | Group 3 | `T65` | `OPEN` | **`CLOSED-LIVE`** | `live:operations.py` | Installed live in plugins/bd-dispatch-ops/scripts/operations.py (29/29 tests pass) |
| 35 | `o1698-hub-gate-sampler` | Group 3 | `T66` | `OPEN` | **`CLOSED-LIVE`** | `harness:5d520c5` | Installed live in harness/bd-hub-gate-sampler + active systemd service |
| 36 | `o1698-l14-heatmap` | Group 3 | `T68` | `OPEN` | **`CLOSED-LANDED`** | `ea7731fe2 / harness:cba5616` | Installed live in harness/bd-token-heatmap.py + cron + BD main ea7731fe2 |
| 37 | `o1698-nfs-share-r3-race-repo` | Group 3 | `T69` | `OPEN` | **`CLOSED-SUPERSEDED`** | `superseded-r3a-r3b` | Superseded by split rows o1698-nfs-share-r3a-mount-root and o1698-nfs-share-r3b-race-verify |
| 38 | `o1698-reap-timer` | Group 3 | `T71` | `OPEN` | **`CLOSED-LANDED`** | `32a516503` | Systemd bd-reap.timer active + repo test landed on BD main 32a516503 (tests/test_o1698_reap_timer.py) |
| 39 | `o1698-r10-agy-claim-r2` | Group 3 | `T73` | `OPEN` | **`CLOSED-LIVE`** | `live:ed33c8ec00ba` | Installed live in harness/bd-launch-agy.sh (ed33c8ec00ba) |
| 40 | `o1698-nfs-share-r3a-mount-root` | Group 3 | `T74` | `OPEN` | **`CLOSED-LIVE`** | `live:bd-nfs-share` | Installed live in harness/bd-nfs-share (/mnt/bd/<name>, /mnt/bd-repo) |
| 41 | `o1698-c1-train-farm-suite` | Group 3 | `T76` | `OPEN` | **`CLOSED-LANDED`** | `e9df8fcb9` | Installed live in harness/bd-train-farm.sh + repo test landed on BD main e9df8fcb9 (tests/test_o1698_c1_train_farm_suite.py) |
| 42 | `o1698-adapter-plugin-dir` | Group 3 | `T79` | `OPEN` | **`CLOSED-LIVE`** | `live:operations.py` | Installed live in operations.py + bd-launch-role.sh (--plugin-dir support, 3x BOARD) |
| 43 | `o1698-sampler-control-48cpu` | Group 3 | `T80` | `OPEN` | **`CLOSED-LANDED`** | `d171e661b` | Landed on BD main d171e661b (tests/test_o1698_sampler_control_48cpu.py) |
| 44 | `o1698-clone-dhcp-spec` | Group 3 | `T82` | `OPEN` | **`CLOSED-LIVE`** | `live:1750af91` | Delta (-ds datastore selection) live in harness/bd-clone-dhcp.py (1750af91); rest superseded by O1719 DHCP-first |
| 45 | `o1698-claim-roots-sibling-tests` | Group 3 | `T85` | `OPEN` | **`CLOSED-LIVE`** | `live:4-sibling-tests` | Installed live: 4 sibling tests byte-identical to FIX candidates |
| 46 | `o1698-adapter-control-roles` | Group 3 | `T89` | `OPEN` | **`BUILT-LIVE-TEST-STAGED`** | `live:operations.py` | Harness half live in operations.py (CONTROL_ROLES, --successor); r3 repo test BOARDed by A2 in bd-local-wt/o1698-adapter-control-roles-r3-bd-worker-H-3-A |
| 47 | `o1698-ugrep-reaper` | Group 3 | `T90` | `OPEN` | **`BUILT-LIVE-TEST-STAGED`** | `live:4de2cd5dda1e` | Harness script live at harness/bd-ugrep-reaper.sh (4de2cd5dda1e); r5b repo test BOARDed by cx-rc2 in bd-local-wt/o1698-ugrep-reaper-bd-worker-D2 |
| 48 | `o1698-codex-fd-leak` | Group 3 | `T91` | `OPEN` | **`CLOSED-LIVE`** | `live:ulimit-65536` | Measured + stopgap live (daemon soft nofile=65536, EMFILE absent) |
| 49 | `o1713-changelog-head-regex` | Group 3 | `T100` | `OPEN` | **`CLOSED-LANDED`** | `afb2eb81a` | Fixed on BD main via afb2eb81a (o1671-a16-changelog-analyzer-head-regex) |
| 50 | `sg-import-graph-timeout` | Group 3 | `T104` | `OPEN` | **`CLOSED-NO-REPRO`** | `main-20.65s` | Does not reproduce on main (runs in 20.65s vs 240s budget; prior timeout was host load) |
| 51 | `sg-hunt-coproc-timing` | Group 3 | `T105` | `OPEN` | **`CLOSED-NO-REPRO`** | `not-red-on-main` | Built in bd-local-wt/sg-hunt-coproc-timing-bd-worker-D5 (UNKNOWN); not RED on main gate |
| 52 | `o1698-worker-done-fullindex` | Group 3 | `T109` | `OPEN` | **`CLOSED-LANDED`** | `bfc48f2c4` | Landed on BD main bfc48f2c4 (tests/test_o1698_worker_done_fullindex.py) |
| 53 | `o1698-lensrouter-role-prompt` | Group 3 | `T111` | `OPEN` | **`CLOSED-LIVE`** | `live:lensrouter.prompt` | Installed live in role-prompts/lensrouter.prompt + ROLE-CARDINALITY.tsv (r3 BOARD) |
| 54 | `o1698-clone-dhcp-r5-guest-diag` | Group 3 | `T112` | `OPEN` | **`CLOSED-DELIVERED`** | `DIAGNOSIS.md` | Diagnosis delivered at harness-work/FIX/o1698-clone-dhcp-r5-guest-diag/DIAGNOSIS.md; code follow-up landed in o1698-clone-dhcp-r5-static |
| 55 | `o1698-band-fail-closed-rc64` | Group 3 | `T113` | `OPEN` | **`CLOSED-LIVE`** | `live:bd-worker-band.sh` | Installed live in harness/bd-worker-band.sh (rc 69 fail-closed, no local pytest fallback; B1 BOARD) |
| 56 | `o1698-band-shim-ssh-explicit` | Group 3 | `T115` | `OPEN` | **`CLOSED-LIVE`** | `live:bd-band-remote.sh` | Installed live in harness/bd-band-remote.sh (fast REMOTE-UNAVAILABLE refusal on test-shims ssh; A2 BOARD) |
| 57 | `o1671-a04-replication-proc-unbound` | Group 3 | `T120` | `OPEN` | **`CLOSED-LANDED`** | `59d277829` | Landed on BD main 59d277829 (proc=None initialized before try in start_replication) |
| 58 | `o1671-a07-ssrf-fail-open` | Group 3 | `T121` | `OPEN` | **`CLOSED-LANDED`** | `0fe253dd9` | Landed on BD main 0fe253dd9 + follow-up o1671-a07-ssrf-validate-url-fail-closed |
| 59 | `o1671-a07-oidc-secret-key` | Group 3 | `T123` | `OPEN` | **`CLOSED-LANDED`** | `bc89bc365` | Landed on BD main bc89bc365 (tests/test_o1671_a07_oidc_secret_key.py; verified 115/115 hunks in CLOSED-LANDED-cx-worker3.md) |
| 60 | `o1671-a07-multiconn-join-timeout` | Group 3 | `T124` | `OPEN` | **`CLOSED-LANDED`** | `8fda1f851` | Landed on BD main 8fda1f851 (o1826-c11-multi-conn-join-timeout) |
| 61 | `o1671-a07-photosets-free-name-race` | Group 3 | `T126` | `OPEN` | **`BUILT-READY-FOR-TRAIN`** | `TREE:c18c186c` | r2 patch (58831d7f, tree c18c186c) built in bd-local-wt/o1671-a07-photosets-free-name-race-bd-worker-W3-7-C; applies cleanly to main |
| 62 | `o1671-a07-lockmonitor-cross-thread` | Group 3 | `T128` | `OPEN` | **`CLOSED-REFUTED`** | `refuted-by-row1059` | Premise refuted: cross-thread release refusal is intentional and pinned by row1059 test (12 passed) |
| 63 | `o1671-a13-captureworkflow-autosuggest-ref` | Group 3 | `T131` | `OPEN` | **`CLOSED-LANDED`** | `10ab79c88` | Landed on BD main 10ab79c88 (o1713-capture-autosuggest-reset-bd-cx-worker17) |
| 64 | `o1671-a07-pgbackend-pragma-quote` | Group 3 | `T132` | `OPEN` | **`BOARDED-READY-FOR-TRAIN`** | `BOARD:D65-D:5725e9b1` | BOARDed by D65-D (tree 5725e9b1, patch 55029c6a); verified on .82 /dev/shm: RED (FAIL: test_o1671_a07_pgbackend_pragma_quote.py | line 43 | AssertionError) -> GREEN (PASS: 14 passed) |
| 65 | `o1671-a15-bdenv-tmp-pythonpath` | Group 3 | `T133` | `OPEN` | **`CLOSED-LANDED`** | `dbec92c6a` | Landed on BD main dbec92c6a (tests/test_o1671_a15_bdenv_tmp_pythonpath_scope.py) |
| 66 | `o1671-a18-relocate-store-dry-run-mkdir` | Group 3 | `T135` | `OPEN` | **`BOARDED-READY-FOR-TRAIN`** | `BOARD:D66-D:a1b34603` | BOARDed by D66-D (tree a1b34603, patch 31723846); verified on .82 /dev/shm: RED (FAIL: test_o1671_a18_relocate_store_dry_run.py | line 46 | AssertionError) -> GREEN (PASS: 4 passed) |
| 67 | `o1671-a16-bulk-review-captures-tz` | Group 3 | `T138` | `OPEN` | **`CLOSED-LANDED`** | `12abf6229` | Landed on BD main 12abf6229 (o1713-review-captures-tz-bd-cx-worker20) |
| 68 | `o1671-a18-local-wheelhouse-semver` | Group 3 | `T141` | `OPEN` | **`CLOSED-LANDED`** | `520dc5262` | Landed on BD main 520dc5262 (o1713-wheelhouse-version-sort) |
| 69 | `o1671-a28-30-test-subprocess-timeouts` | Group 3 | `T148` | `OPEN` | **`CLOSED-LANDED`** | `2beb53e60` | Landed on BD main 2beb53e60 (spec: unpark-a28-30-timeouts-S22-D; tests/test_o1671_a28_30_subprocess_timeouts.py) |
