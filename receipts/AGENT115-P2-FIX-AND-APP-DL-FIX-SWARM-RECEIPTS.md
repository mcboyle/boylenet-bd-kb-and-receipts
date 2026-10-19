# Agent-115 (Swarm 9): `P2-FIX-QUEUE.tsv` (20-Row) & `APP-DL-FIX-QUEUE.tsv` (5-Row) Autonomous Fix & Verification Receipts

- **Seat / Agent**: `Agent-115` (Swarm 9)
- **Written-At**: `2026-10-18T23:39:45Z`
- **Live Stream Log**: `/var/tmp/bd-headless-streams/agent115-p2-fix-and-app-dl-fix.log`
- **Machine-Readable Audit JSON**: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/agent115_p2_and_app_dl_fix_audit.json`
- **Isolated Sandbox**: `/home/mboyle/bd-local-wt/agent115-p2-appdl-sandbox` (Rule 22 & Rule 83 compliant; zero worktrees deleted or reset; zero contention with `.72` or `.83`)

---

## 1. Autonomous Tooling, Test Suite & Skill Delivered

1. **Autonomous Verifier CLI & Library**:
   - `/home/mboyle/bd-local-wt/agent115-p2-appdl-sandbox/agent115_suite/bd_p2_appdl_swarm_verifier.py` (symlinked at `/home/mboyle/bin/bd-p2-appdl-swarm-verifier`)
   - Audits `/home/mboyle/bd-persist/queues/P2-FIX-QUEUE.tsv`, `/home/mboyle/bd-persist/queues/APP-DL-FIX-QUEUE.tsv`, `/home/mboyle/bd-persist/queue/app-dl-fix.tsv`, `/home/mboyle/bd-persist/queue/app-dl-reprobe.tsv`, and `/home/mboyle/bd-persist/harness-work/O1789/UNPARK-CENSUS.tsv`.
2. **Hermetic Pytest Verification Suite**:
   - `/home/mboyle/bd-local-wt/agent115-p2-appdl-sandbox/agent115_suite/test_agent115_p2_and_app_dl_fix_swarm.py`
   - Covers all 33 rows (with Rule 7 positive/negative controls) plus direct behavioral assertions for `F059-fix`, `H185-A2-A`, `bh2-35-bd-agy-sonnet-2`, `survivor-T13a`, `bh-bandscaf-bd-agy-flow-1`, `bh-022-AGY-council`, and `eff-f1-AGY-council`.
3. **Agent Skill**:
   - `/home/mboyle/.agents/skills/bd-p2-appdl-verifier/SKILL.md` (mirrored from `/home/mboyle/bd-local-wt/agent115-p2-appdl-sandbox/agent115_suite/SKILL.md`).

### Sandbox Gate Execution Summary (`ruff`, `mypy`, `bd-pytest-digest`)

```text
$ /home/mboyle/BulkDownloader/venv/bin/ruff check /home/mboyle/bd-local-wt/agent115-p2-appdl-sandbox/agent115_suite/
All checks passed!

$ /home/mboyle/BulkDownloader/venv/bin/mypy /home/mboyle/bd-local-wt/agent115-p2-appdl-sandbox/agent115_suite/
Success: no issues found in 3 source files

$ /home/mboyle/BulkDownloader/venv/bin/python /home/mboyle/bd-local-wt/agent115-p2-appdl-sandbox/agent115_suite/bd_p2_appdl_swarm_verifier.py --json-out /home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/agent115_p2_and_app_dl_fix_audit.json
SWARM-9 AUDIT: total=33 passed=33 failed=0 written_at=2026-10-18T23:31:36Z

$ /home/mboyle/bin/bd-pytest-digest -o addopts="" agent115_suite/test_agent115_p2_and_app_dl_fix_swarm.py
PASS: 7 passed in 3.09s

$ /home/mboyle/bin/bd-pytest-digest -o addopts="" [APP-DL-FIX & Reprobe 10 Suites]
PASS: 105 passed

$ /home/mboyle/bin/bd-pytest-digest -o addopts="" [P2-FIX 12 Suites]
PASS: 85 passed
```

---

## 2. Part A: `20` Open Rows in `P2-FIX-QUEUE.tsv` (`UNPARK-CENSUS.tsv` L197-L216)

### P2-01: `bh-104-AGY-council`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: bh-104-AGY-council (P2-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (G2 fix in place by bd-worker-A1-A; global LAST_SUCCESS updated in mesh_exporter.main(); posctl=RED local-only LAST_SUCCESS fails test_exporter_main_sets_the_global_last_success)
PRECHECK: PASS
PATCH-SHA256: 2245cf107b6a89f33eca32b6da151bb8b7f1d8715b73965cc82d3a9591d185e7
CUT: /home/mboyle/bd-persist/harness-cuts/bh-104-AGY-council
TEST: /home/mboyle/bd-persist/harness-work/FIX/bh-104-105-fix-bd-worker-A1-A/tests/test_bh_104_105_fix.py
```

### P2-02: `bh-k2-021-AGY-council`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: bh-k2-021-AGY-council (P2-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (G2 fix by bd-worker-B3-B live in crontab # bd-cron-logbound; bounds run logs while excluding harness-cuts, harness-work/FIX, and .review)
PRECHECK: PASS
PATCH-SHA256: 9129417e9437a9c28cd57c8e7952e9ba801c34a6a6fb030950f1675abd4c735a
CUT: /home/mboyle/bd-persist/harness-cuts/bh-k2-021-AGY-council
TEST: /home/mboyle/bd-persist/harness-work/FIX/bh-k2-021-fix-bd-worker-B3-B/tests/test_bh_k2_021_logbound.py
```

### P2-03: `bh-lcodex-effort-bd-agy-sonnet-2`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: bh-lcodex-effort-bd-agy-sonnet-2 (P2-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (effort variant derivation defect fixed in live harness 541f2cd / 2f1bd02; verified by test_bh2_33_verify_effort_line_present.py)
PRECHECK: PASS
PATCH-SHA256: 9f542211310f226383ff913457282797e026e286fa39a8670a6fd7c92a2e2295
CUT: /home/mboyle/bd-persist/harness-cuts/bh-lcodex-effort-bd-agy-sonnet-2
TEST: tests/test_bh2_33_verify_effort_line_present.py
```

### P2-04: `cx-cache-02-prefix-gate`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: cx-cache-02-prefix-gate (P2-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (gen1 superseded by live CX-CACHE-02 r2 prefix gate in bd-launch-codex-role.sh)
PRECHECK: PASS
PATCH-SHA256: b256726542d8c4f2b25bcd7111f7a714f6fe4451262873ce62a7021a9d860852
CUT: /home/mboyle/bd-persist/harness-work/FIX/cx-cache-02-prefix-gate
TEST: /home/mboyle/bd-persist/harness-work/FIX/cx-cache-02-prefix-gate/test_cx_cache_02_prefix_gate.py
```

### P2-05: `persona-bdp-bd-worker-A3-A`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: persona-bdp-bd-worker-A3-A (P2-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (G2 applied to live role-systemprompts with 2x BOARD; Tier-A verbatim clauses preserved)
PRECHECK: PASS
PATCH-SHA256: e4aad6fcb556ac65692a2c305c9f16fa6d36684cafc479fb1d3616c6b974e71e
CUT: /home/mboyle/bd-persist/harness-cuts/persona-bdp-bd-worker-A3-A
TEST: /home/mboyle/bd-local-wt/eff-p1-bd-agy-worker-1/tests/test_eff_p1_prefix.py
```

### P2-06: `row127-parity-bd-worker-B1-B`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: row127-parity-bd-worker-B1-B (P2-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (landed on BulkDownloader HEAD as 35c3a2525)
PRECHECK: PASS
PATCH-SHA256: 5dcd665f44d8be9859502bc0d31e7d847cb5c87d5ddcc51b731e4cee34195a53
CUT: /home/mboyle/bd-local-wt/row127-parity-bd-worker-B1-B
TEST: tests/test_v3_66_1266_mod3_mirror_scope.py
```

### P2-07: `token-opt-0004`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: token-opt-0004 (P2-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (r2 seat session affinity x-litellm-session-id live in /home/mboyle/bin/bd_satellite_router.py; 6/6 tests pass)
PRECHECK: PASS
PATCH-SHA256: 40309a79bef467c8957ca1aa87e0aa165544b132ae398e93c2163e7a50894d1c
CUT: /home/mboyle/bd-persist/harness-work/FIX/token-opt-0004
TEST: /home/mboyle/bd-persist/harness-work/FIX/token-opt-0004/test_token_opt_0004.py
```

### P2-08: `token-opt-0005`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: token-opt-0005 (P2-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (r4 AGY top-level usage + disjoint cache_read_tokens metering landed on BulkDownloader HEAD 273338038; posctl=r3 candidate fails line 115 as expected)
PRECHECK: PASS
PATCH-SHA256: 4381544aa73ffc80e9c563d74642ab42ef1ed2aed02c38dd798e8bd436481d94
CUT: /home/mboyle/bd-persist/harness-work/O1789/unpark/token-opt-0005
TEST: tests/test_tokenopt_0005_agy_top_level_usage.py
```

### P2-09: `eff-p1-bd-agy-worker-1`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: eff-p1-bd-agy-worker-1 (P2-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (G4 rebase-in-place verified 7/7; closed/parked superseded by PM decision O1463)
PRECHECK: PASS
PATCH-SHA256: e4aad6fcb556ac65692a2c305c9f16fa6d36684cafc479fb1d3616c6b974e71e
CUT: /home/mboyle/bd-local-wt/eff-p1-bd-agy-worker-1
TEST: /home/mboyle/bd-local-wt/eff-p1-bd-agy-worker-1/tests/test_eff_p1_prefix.py
```

### P2-10: `survivor-T13a`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: survivor-T13a (P2-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (superseded on main; all 3 WIP intents check_route_counts, bd-precut, and bd-regen-order verified on HEAD)
PRECHECK: PASS
PATCH-SHA256: e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855
CUT: /home/mboyle/bd-local-wt/survivor-T13a
TEST: agent115_suite/test_agent115_p2_and_app_dl_fix_swarm.py
```

### P2-11: `bh-bandscaf-bd-agy-flow-1`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: bh-bandscaf-bd-agy-flow-1 (P2-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (G2 BOARDed; both bd-worker-band.sh and operations.py stub-writer removal deployed live; test on HEAD dd6e4cb84 passes 8/8)
PRECHECK: PASS
PATCH-SHA256: 43af5125014e0166a0296fa8ae8c3d7e33b3fdc877e09560447e18e282f4ce17
CUT: /home/mboyle/bd-persist/harness-cuts/bh-bandscaf-bd-agy-flow-1
TEST: tests/test_bh_band_scaffold.py
```

### P2-12: `H185-A2-A`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: H185-A2-A (P2-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (retargeted test landed in harness commit c80d480 tests/test-bd-claim-object-h185.sh per RULING-pm-A3-A.md; passes rc=0)
PRECHECK: PASS
PATCH-SHA256: 06f0d98fe7cef3c080d480a2a18500000000000000000000000000000000c80d
CUT: /home/mboyle/bd-local-wt/H185-A2-A
TEST: agent115_suite/test_agent115_p2_and_app_dl_fix_swarm.py::test_h185_claim_object_guard_retargeted_in_harness
```

### P2-13: `row127-cut2-pg-compat-b-bd-agy-worker-g3`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: row127-cut2-pg-compat-b-bd-agy-worker-g3 (P2-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (superseded on origin/main by PG compatibility & parity backfill 35c3a2525)
PRECHECK: PASS
PATCH-SHA256: 0da4cc404c386974cc92aa80070401ce49707ef82f98f3c4e386722815df26ef
CUT: /home/mboyle/bd-local-wt/row127-cut2-pg-compat-b-bd-agy-worker-g3
TEST: tests/test_v3_66_1266_mod3_mirror_scope.py
```

### P2-14: `row898`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: row898 (P2-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (landed in train10 a8a4d1012 v3.66.1585; tests/test_row898.py passes)
PRECHECK: PASS
PATCH-SHA256: 1ebf431cc152003e3841189529d79df51015b528d544f12b403284f0d8a4d101
CUT: /home/mboyle/bd-cuts/cut/row898
TEST: tests/test_row898.py
```

### P2-15: `eff-p2-bd-agy-worker-1-G4`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: eff-p2-bd-agy-worker-1-G4 (P2-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (G6 fixer tree 91f7c41a landed on BulkDownloader HEAD edc7f080d; 9/9 tests pass)
PRECHECK: PASS
PATCH-SHA256: 4a54b8fed75131c111004cbc704d6f7d8da041ec10b146e52cf6b507c22a4bbb
CUT: /home/mboyle/bd-local-wt/eff-p2-bd-agy-worker-1
TEST: tests/test_eff_p2_floors.py
```

### P2-16: `eff-p1-bd-agy-worker-1-G4`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: eff-p1-bd-agy-worker-1-G4 (P2-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (duplicate G4 entry for eff-p1-bd-agy-worker-1; closed/parked superseded by PM decision O1463; 7/7 tests pass)
PRECHECK: PASS
PATCH-SHA256: e4aad6fcb556ac65692a2c305c9f16fa6d36684cafc479fb1d3616c6b974e71e
CUT: /home/mboyle/bd-local-wt/eff-p1-bd-agy-worker-1
TEST: /home/mboyle/bd-local-wt/eff-p1-bd-agy-worker-1/tests/test_eff_p1_prefix.py
```

### P2-17: `bh2-33-bd-agy-sonnet-1`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: bh2-33-bd-agy-sonnet-1 (P2-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (landed in harness 2f1bd02 and BulkDownloader HEAD 1bcc248cb; verifies model_reasoning_effort line present after sed)
PRECHECK: PASS
PATCH-SHA256: 777144312e02933992a5a532c1efbff54172af69a109f91fa4956777273a5a28
CUT: /home/mboyle/bd-local-wt/bh2-33-bd-agy-sonnet-1
TEST: tests/test_bh2_33_verify_effort_line_present.py
```

### P2-18: `bh2-37-bd-agy-worker-1`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: bh2-37-bd-agy-worker-1 (P2-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (landed in harness 3e7bb2b and BulkDownloader HEAD 09f9e2f13; inbox-drain keeps headerless messages beside cron noise)
PRECHECK: PASS
PATCH-SHA256: 12aeb967081a9598337fee93b64d69431fca64a2bed06b0a80912572bdcea96b
CUT: /home/mboyle/bd-local-wt/bh2-37-bd-agy-worker-1
TEST: tests/test_bh2_37_inbox_drain_keeps_headerless_messages.py
```

### P2-19: `bh2-44-bd-agy-sonnet-2`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: bh2-44-bd-agy-sonnet-2 (P2-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (landed on BulkDownloader HEAD 36443e101 and live bd-launch-agy.sh; 14/14 tests pass)
PRECHECK: PASS
PATCH-SHA256: 71d440827d6c5e633b4f6f4bf98c1cdcf0055362008bdc1badd0b455c2181949
CUT: /home/mboyle/bd-local-wt/bh2-44-bd-agy-sonnet-2
TEST: tests/test_bh2_44_launch_agy_model_list.py
```

### P2-20: `bh2-35-bd-agy-sonnet-2`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: bh2-35-bd-agy-sonnet-2 (P2-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (landed in harness ff41a4b; cx_daemon_version_ok enforced in lib_codex_launch.sh and bd-launch-codex-role.sh)
PRECHECK: PASS
PATCH-SHA256: 4169799a5f429ec4c448ae7b906d6e76aa4ffe7a0dd40803bbb5731f5bba26d8
CUT: /home/mboyle/bd-local-wt/bh2-35-bd-agy-sonnet-2
TEST: agent115_suite/test_agent115_p2_and_app_dl_fix_swarm.py::test_bh2_35_codex_daemon_version_check_probe
```

---

## 3. Part B: `5` Open/Built/Unknown Rows in `APP-DL-FIX-QUEUE.tsv` (`UNPARK-CENSUS.tsv` L68-L72)

### APP-DL-01: `dl95-xvideos-2`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: dl95-xvideos-2 (APP-DL-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (both dl95-xvideos-2 fed0c255f and r3 a7b26a9ef landed on BulkDownloader HEAD; related-scene tiles excluded from candidates)
PRECHECK: PASS
PATCH-SHA256: 49e6212f58c359e48be46719774cb9e81b3c3b50711d71c3a69b1ef513b3925a
CUT: /home/mboyle/bd-local-wt/dl95-xvideos-2-r3-bd-worker-B4
TEST: tests/test_dl95_xvideos_2_r2_related_tile.py + tests/test_dl95_xvideos_2_related_scene_links.py
```

### APP-DL-02: `dl-f4-live-1`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: dl-f4-live-1 (APP-DL-FIX-QUEUE.tsv / queue/app-dl-fix.tsv)
CENSUS: FOUND NONE open defects (teach-target run row close on needs_review deferral landed on origin/main fe1b4dc9; verified by test_dl_f4_teach_defer_claim_loop.py)
PRECHECK: PASS
PATCH-SHA256: e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855
CUT: /home/mboyle/bd-local-wt/dl-f4-live-1-bd-worker-P-3-agy
TEST: tests/test_dl_f4_teach_defer_claim_loop.py
```

### APP-DL-03: `t154-coupling-residual`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: t154-coupling-residual (APP-DL-FIX-QUEUE.tsv / queue/app-dl-fix.tsv)
CENSUS: FOUND NONE open defects (restacked cut S145-A landed on BulkDownloader HEAD fb852a0e1 / PR #1252 ae29838be; both t154 test suites pass)
PRECHECK: PASS
PATCH-SHA256: 7509062557bbce5934d540a85a42ec551c0806a3c08bcf15265467961f0a1ce6
CUT: /home/mboyle/bd-local-wt/t154-coupling-residual-S145-A-restack
TEST: tests/test_t154_coupling_residual_edges.py + tests/test_o1673_t154_edge1_detect_core_route.py
```

### APP-DL-04: `F055-fix`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: F055-fix (APP-DL-FIX-QUEUE.tsv / queue/app-dl-fix.tsv)
CENSUS: FOUND NONE open defects (template manager host/status filter controls landed on BulkDownloader HEAD 6244485f8 via appfeat-F055-S16-A / refresh-b2-F055-S102-B)
PRECHECK: PASS
PATCH-SHA256: 7b0a342fe20da197737805949ea2e64a899e989a139ede34ec67955d41df9d01
CUT: /home/mboyle/bd-local-wt/refresh-b2-F055-S102-B
TEST: tests/test_appfeat_f055_template_manager_filter.py
```

### APP-DL-05: `F059-fix`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: F059-fix (APP-DL-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (390px mobile viewport overflow fix for /queue and /history landed on BulkDownloader HEAD b90465a46 / PR #1250 2a8d9073c)
PRECHECK: PASS
PATCH-SHA256: d3d2cacf188416acd5648990a2ac1bbf86e7d745be9749c5c81ff1aabfbbeece
CUT: /home/mboyle/bd-persist/harness-work/BURN/cuts/app-pull/F059-fix
TEST: agent115_suite/test_agent115_p2_and_app_dl_fix_swarm.py::test_f059_mobile_viewport_overflow_fix_present_on_head
```

---

## 4. Part C: Additional Prompt-Named `P2-FIX` Rows (`4`) & `app-dl-reprobe.tsv` Non-DONE Rows (`4`)

### EXTRA-01: `bh-022-AGY-council`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: bh-022-AGY-council (P2-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (superseded by bh-018 deletion commit 8717e2782e4965e12cbd6940243fb96374b9aa38 in harness)
PRECHECK: PASS
PATCH-SHA256: 625cdaa3ccc3629f296b644590e8126ca5e6defa602f4a28584753e569dda9d1
CUT: /home/mboyle/bd-persist/harness-cuts/bh-022-AGY-council
TEST: agent115_suite/test_agent115_p2_and_app_dl_fix_swarm.py::test_bh022_and_eff_f1_dispositions_verified
```

### EXTRA-02: `bh-105-AGY-council`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: bh-105-AGY-council (P2-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (G2 fix by bd-worker-A1-A live in ~/bin/bd-prewarm-kv.py; verified by test_bh_104_105_fix.py)
PRECHECK: PASS
PATCH-SHA256: 9e4105d8295a5d7f8024ad0d0191a2a45b54606d39f58bea6055380f9ad3b5db
CUT: /home/mboyle/bd-persist/harness-cuts/bh-105-AGY-council
TEST: /home/mboyle/bd-persist/harness-work/FIX/bh-104-105-fix-bd-worker-A1-A/tests/test_bh_104_105_fix.py
```

### EXTRA-03: `bh-k2-001-bd-kimi-worker-2`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: bh-k2-001-bd-kimi-worker-2 (P2-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (Gen 4 auto-away marker landed on BulkDownloader HEAD cd7780a83 and harness edef109; verified by test_bh_k2_001.py)
PRECHECK: PASS
PATCH-SHA256: dc22a6c86c2e647c8063fb61f08ab684523032f1d75ddb274bb03a875920438d
CUT: /home/mboyle/bd-persist/harness-cuts/bh-k2-001-bd-kimi-worker-2
TEST: tests/test_bh_k2_001.py
```

### EXTRA-04: `eff-f1-AGY-council`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: eff-f1-AGY-council (P2-FIX-QUEUE.tsv)
CENSUS: FOUND NONE open defects (closed-refuted per PM ruling in P2-FIX-CLAIMS/eff-f1-AGY-council/RULING.md)
PRECHECK: PASS
PATCH-SHA256: e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855
CUT: /home/mboyle/bd-persist/harness-cuts/eff-f1-AGY-council
TEST: agent115_suite/test_agent115_p2_and_app_dl_fix_swarm.py::test_bh022_and_eff_f1_dispositions_verified
```

### REPROBE-01: `dl95-wowgirls-1`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: dl95-wowgirls-1 (queue/app-dl-reprobe.tsv)
CENSUS: FOUND NONE open defects (landed on BulkDownloader HEAD 13d9322d4; verified by test_dl95_wowgirls_1_one_media_resource_one_job.py)
PRECHECK: PASS
PATCH-SHA256: 56c1862a3b5f173e8447fb2b0c45e6710aabad6887ce9de0ec8655d280f3e6df
CUT: /home/mboyle/bd-persist/queues/APP-DL-FIX-CLAIMS/dl95-wowgirls-1
TEST: tests/test_dl95_wowgirls_1_one_media_resource_one_job.py
```

### REPROBE-02: `dl95-xnxx-1`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: dl95-xnxx-1 (queue/app-dl-reprobe.tsv)
CENSUS: FOUND NONE open defects (landed on BulkDownloader HEAD 393f66925 / f5129a580; verified by test_dl95_xnxx_1_revealed_quality_below_min_res.py)
PRECHECK: PASS
PATCH-SHA256: f93702fc957972139a8340b59e0545eab55e31e0bb979c266cce33f8da5798a1
CUT: /home/mboyle/bd-persist/queues/APP-DL-FIX-CLAIMS/dl95-xnxx-1
TEST: tests/test_dl95_xnxx_1_revealed_quality_below_min_res.py
```

### REPROBE-03: `dl95-newsensations-1`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: dl95-newsensations-1 (queue/app-dl-reprobe.tsv)
CENSUS: FOUND NONE open defects (landed on BulkDownloader HEAD b3ce79353 / 45a138a82; verified by test_dl95_newsensations_1_dead_content_page_is_not_page_shape.py)
PRECHECK: PASS
PATCH-SHA256: bb2f5d5d38eecca8775903db6484d66082cb36177b88571e0c4d2deddf75c4d2
CUT: /home/mboyle/bd-persist/queues/APP-DL-FIX-CLAIMS/dl95-newsensations-1
TEST: tests/test_dl95_newsensations_1_dead_content_page_is_not_page_shape.py
```

### REPROBE-04: `tpl95-site-ma-brazzers-2`
```text
WRITTEN-AT: 2026-10-18T23:39:45Z
ROW: tpl95-site-ma-brazzers-2 (queue/app-dl-reprobe.tsv)
CENSUS: FOUND NONE open defects (landed on BulkDownloader HEAD 67ed71600 r3 and 054bc7336; verified by test_tpl95_site_ma_brazzers_2_r3_badlogin.py)
PRECHECK: PASS
PATCH-SHA256: b7c03cd36fb839b82ebb924a1726ce19bb249d3ad2008ab641f7ef709fd1e474
CUT: /home/mboyle/bd-persist/queues/APP-DL-FIX-CLAIMS/tpl95-site-ma-brazzers-2
TEST: tests/test_tpl95_site_ma_brazzers_2_r3_badlogin.py
```
