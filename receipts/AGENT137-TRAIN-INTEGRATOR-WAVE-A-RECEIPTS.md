# AGENT-137: Staged Train Integrator Wave A (Swarms `#001`–`#003`) Verification Receipts

- **WRITTEN-AT**: `2026-10-19T00:15:45Z`
- **AGENT**: `Agent-137` (Swarms `#001`–`#003`: Staged Train Integrator Wave A — `APP-FEATURE` `F026`/`F051`/`F060` + `O1670` Ready Cuts + `P3-LENS` `DECIDED BOARD` Cuts)
- **CALLER**: `d61a0f86-9a87-4a9f-8b2c-ea5791e23bc8`
- **LIVE STREAM LOG**: `/var/tmp/bd-headless-streams/agent137-train-integrator-wave-a.log` (`:4083` visibility)
- **UNIFIED INTEGRATOR PACKET**: `/home/mboyle/bd-persist/harness-work/O1789/landing/TRAIN-BUNDLE-WAVE-A-1018.md` (for `bd-integrator-D3-D` on `.52`)
- **STAGED BUNDLE WORKSPACE**: `/home/mboyle/bd-local-wt/wave-a-train-bundle-1018/` (Rule 21 & Rule 22 strictly enforced; zero index or working-tree writes outside `/home/mboyle/bd-local-wt/`)
- **ZERO-CONTENTION INVARIANT**: Zero commands, packets, or SSH sessions issued to `.72` (`10.0.70.72`, active `Agent-37` `kimi-k3-abliterated-iq1s-gguf`) or `.83` (`10.0.70.83`). All off-host pytest & Vitest verification ran exclusively on `.82` (`10.0.70.82` `wrk-test04` 64-core `/dev/shm` ramdisk).

---

## 1. Executive Summary & Combined Train Bundle Certification

| Swarm | Scope | Denominator | `git apply --check` on `HEAD` (`747c3f6cb6`) | `.82` 64-Core `/dev/shm` Ramdisk `bd-pytest-digest` + `Vitest` | Verdict |
|---|---|---:|---|---|---|
| **Swarm `#001`** | `APP-FEATURE` Staged Cuts (`F026`, `F051`, `F060` + `UI-T27`) | `3` cuts | **`PASS` (`0` conflicts)** | `bd-pytest-digest`: **`PASS: 15 passed in 13.18s`** (`rc=0`)<br>`Vitest`: **`3 passed (3) | 9 passed (9)` in `2.43s`** (`rc=0`) | **`PRECHECK: PASS`** |
| **Swarm `#002`** | `O1670` Ready-for-Train Cuts (`o1670-l28`, `o1670-l31`, `o1670-l33`, `o1670-g3-01`/`l13`, `o1670-g4-04`/`l14`) | `5` cuts | **`PASS` (`0` conflicts)** | Product cuts (`l28`+`l31`+`l33`): **`PASS: 27 passed in 23.98s`** (`rc=0`)<br>Harness repo cuts (`l13`+`l14`): **`PASS: 67 passed in 4.41s`** + **`PASS: 34 passed`** (`rc=0`) | **`PRECHECK: PASS`** |
| **Swarm `#003`** | `P3-LENS` `DECIDED BOARD` Cuts (`bh-018-AGY-council`, `bh-bandscaf-bd-agy-flow-1`, `p5-failopen-carry003-bd-worker-A2-A`, `limitwatch-codex-B2B-TESTFIX`, `o1504-rule21-orphan-bd-worker-A2-A`) | `5` cuts | **`PASS` (3 landed on `harness` `main`, 1 live law, 1 closed-stale)** | `test_bh_band_scaffold.py` + `test_p5_failopen_carry003.py`: **`PASS: 22 passed in 3.13s`** (`rc=0`)<br>`test_limitwatch_codex_probe.py`: **`PASS: 7 passed in 11.63s`** (`rc=0`)<br>`o1504`: **`0/0` orphan hits** | **`PRECHECK: PASS`** |
| **TOTAL** | **Wave A Unified Integrator Bundle (`Swarms #001–#003`)** | **`13` cuts** | **`0` conflicts (`15` distinct files across `8` `BulkDownloader` patches)** | **`172` Python tests + `9` Vitest tests = `181/181 PASS` (`100%`)** | **`READY FOR TRAIN`** |

### Combined 8-Cut `BulkDownloader` Staged Index & Tree (`Swarm #001` + `Swarm #002`)
- **Base Commit (`BulkDownloader` `HEAD`)**: `747c3f6cb6286dd80390c84ef7bf0bb3635919cd` (`v3.66.1908 -- T282ak-D`)
- **Combined Git `TREE`**: `f9a37c7c19ed5f47a031ccdaf5caf75f21c4c2b1`
- **Combined `PATCH-SHA256` (`--full-index` vs `747c3f6cb6`)**: `95b9b3c30a4458770fe809ffdadf36f2aa5675b73458c08ad612f17de65987ec`
- **Combined Staged Diffstat**: `15 files changed, 1870 insertions(+), 17 deletions(-)`
- **Individual Patch Files**: `/home/mboyle/bd-local-wt/wave-a-train-bundle-1018/patches/01-appfeat-F026.patch` .. `09-p3-limitwatch-codex-test.patch`

---

## 2. Swarm `#001` Detailed Verification (`APP-FEATURE` Staged Cuts `F026`, `F051`, `F060`)

| Cut ID | Worktree & Receipt | `BASE` | `TREE` | `PATCH-SHA256` (`--full-index`) | Changed Files | `.82` Ramdisk `bd-pytest-digest` + `Vitest` Receipt |
|---|---|---|---|---|---|---|
| **`F026`** | `/home/mboyle/bd-local-wt/appfeat-F026-cx-swarm-sol-1` | `747c3f6cb6286dd80390c84ef7bf0bb3635919cd` | `8284c75acb4ad01d9f4558110d4d45dec28acd3d` | `99fb9d012fb43b0eff3f214b7f4424b8cd40e9cee523750746408f73f44b156c` | `M frontend/src/routes/ImportsCenter.tsx`<br>`A frontend/src/routes/ImportsCenter.bulkPreview.test.tsx`<br>`A tests/test_appfeat_f026_bulk_sites_preview.py` | `.82` `bd-pytest-digest`: `PASS` (`1/1`)<br>`.82` `Vitest`: `src/routes/ImportsCenter.bulkPreview.test.tsx` (`4/4 passed` in `581ms`) |
| **`F051`** | `/home/mboyle/bd-local-wt/cplus/fu-f051-g2`<br>`/home/mboyle/bd-persist/cplus/results/fu-f051.e16.g2/DONE.md`<br>`/home/mboyle/bd-persist/harness-work/BURN/cuts/UI-T27/DONE.md` | `03d20465adfdfa860ecd94a3e132cc242abbd740` | `28704abdd32bf2508b552884c4589cc0031c6792` | `969c1e77290e2ec9f05b681a5a0339cd67ee3080fd370fd180c5ad3f0390e003`<br>*(vs `HEAD`: `c5373a6ce37b2068c2d697920fd9e77a15c231f4521293b7e9d6c712c5bbb83e`; UI-T27: `b63a9868b36ef24891e0a6f7d810e85a80b0b199ac1dc597d0bdf210d109e0b7`)* | `M bulk_downloader/app_report_center.py`<br>`A tests/test_f051_vpn_secrets_status_unavailable_state.py` | `.82` `bd-pytest-digest`: `PASS` (`7/7 passed`) |
| **`F060`** | `/home/mboyle/bd-local-wt/cplus/fu-f060-r2-g3`<br>`/home/mboyle/bd-persist/cplus/results/fu-f060-r2.e18.g3/DONE.md` | `03d20465adfdfa860ecd94a3e132cc242abbd740` | `5538a6c65079f61f2f08b71684ba6f2d9e0fbb08` | `76ffdf7231768a396567d036c4068cd15005b8743b4152e4d67762b3d3462681`<br>*(vs `HEAD`: `f5a6a11fb987b3bf79de7839d2da04246cf1ecfe954d56992d9b0dfc1a3a5c4d`)* | `M frontend/src/lib/themes.ts`<br>`A tests/test_themes_catalog.py` | `.82` `bd-pytest-digest`: `PASS` (`7/7 passed`)<br>`.82` `Vitest`: `ThemeMenu.test.tsx` (`3/3`) + `ThemeToggle.reexport.test.tsx` (`2/2`) |

---

## 3. Swarm `#002` Detailed Verification (`O1670` Ready-for-Train Cuts)

| Cut ID | Row ID & Worktree | `BASE` | `TREE` | `PATCH-SHA256` (`--full-index`) | `git apply --check` on `747c3f6cb6` | `.82` Ramdisk `bd-pytest-digest` Receipt |
|---|---|---|---|---|---|---|
| **`o1670-l28`** (`#61` / `T126`) | `o1671-a07-photosets-free-name-race`<br>`/home/mboyle/bd-local-wt/o1671-a07-photosets-free-name-race-bd-worker-W3-7-C` | `8dda38e3c621e015195d85e4568fc1a2358e2bbc` | `c18c186c0e6def829463d6c1ff80b03feea2d82c` | `58831d7fb10a0af9ac15261cc1e7212382e2eb74b0b212dd5941516c2d170563` | **`PASS` (`rc=0`)** | `tests/test_o1671_a07_photosets_free_name_race.py`: **`PASS` (`9/9 passed`)** |
| **`o1670-l31`** (`#64` / `T132`) | `o1671-a07-pgbackend-pragma-quote` (`D65-D` `BOARD`)<br>`/home/mboyle/bd-local-wt/o1671-a07-pgbackend-pragma-quote-bd-cx-worker4` | `ef36a73f33887b2bd38b8991a7b4e9a9356c786e` | `5725e9b1a360a02db7a59bc2d255f4ec01307d1a` | `55029c6a738f3e09afc1db40d5f96db47358a745d7528a508cec633f8027835e` | **`PASS` (`rc=0`)** | `tests/test_o1671_a07_pgbackend_pragma_quote.py`: **`PASS` (`14/14 passed`)** |
| **`o1670-l33`** (`#66` / `T135`) | `o1671-a18-relocate-store-dry-run-mkdir` (`D66-D` `BOARD`)<br>`/home/mboyle/bd-local-wt/o1671-a18-relocate-store-dry-run-mkdir-bd-cx-worker7` | `ef36a73f33887b2bd38b8991a7b4e9a9356c786e` | `a1b34603ef9574c4be8e8894d254e014742df58e` | `31723846094e34635d40d432525e521ee849acea1d1fc21fd1b25aa7db792cc8` | **`PASS` (`rc=0`)** | `tests/test_o1671_a18_relocate_store_dry_run.py`: **`PASS` (`4/4 passed`)** |
| **`o1670-g3-01`** (`o1670-l13` / `#46` / `T89`) | `o1698-adapter-control-roles-r3` (`A2` `BOARD`)<br>`/home/mboyle/bd-local-wt/o1698-adapter-control-roles-r3-bd-worker-H-3-A` | `68ce2c95d8b249f14e3fcae527c3da5da7d19c59` | `fde62a355a5debda7a6fc1f3ea3e152d7985639d` | `b80ff3167d1f64e0135f2ddec6f736c679192ad937f87f2dda1a5bd829c80ded` | **`PASS` (`rc=0`)** | `tests/test_o1698_control_roles_r3.py`: **`PASS: 67 passed in 4.41s` (`rc=0`)** |
| **`o1670-g4-04`** (`o1670-l14` / `#47` / `T90`) | `o1698-ugrep-reaper` (`cx-rc2` `BOARD`)<br>`/home/mboyle/bd-local-wt/o1698-ugrep-reaper-bd-worker-D2` | `633ba3687f7a9f62f06aa19b9ea317d61dcb2f51` | `e34cd876aab3ba418c5fd2058ab1a8a8e691b0fe` | `49ae26ff31d82c9d49a11f6a8ee21bd1d00567c810bd10dbf9fa24e0c64704e3` | **`PASS` (`rc=0`)** | `tests/test_o1698_ugrep_reaper.py`: **`PASS: 34 passed` (`rc=0`)** |

---

## 4. Swarm `#003` Detailed Verification (`P3-LENS` `DECIDED BOARD` Cuts)

| Cut ID | Artifact Path | `INDEX-TREE` / Commit | `PATCH-SHA256` | `PRECHECK` | `.82` Ramdisk `bd-pytest-digest` / Live Verification |
|---|---|---|---|---|---|
| **`bh-018-AGY-council`** | `/home/mboyle/bd-persist/harness-cuts/bh-018-AGY-council/DONE.md` | `INDEX TREE: N/A` (`CLOSED-STALE` in `P3-LENS-QUEUE.tsv`) | `ff8e48d2a2c414343bce173d928fb62882ec197d38f860d287ca710a127bb680` | **`PASS`** | Reconciled `CLOSED-STALE` (no index tree/patch artifact; 3x BOARD `ff8e48d2` in `.review/`) |
| **`bh-bandscaf-bd-agy-flow-1`** | `/home/mboyle/bd-persist/harness-cuts/bh-bandscaf-bd-agy-flow-1/DONE-r4-integrator.md`<br>`/home/mboyle/bd-local-wt/bh-bandscaf-r3-agent108` | `31c4aa0592b6edf52469c15cc09b3120486d225a`<br>(`LANDED c0f58a6a` on `harness` `main`) | `efca8eaa60e6a1e1da07b8b1501a821482e5bb168aa9e03e3ab65d823d32d37e` *(r4)*<br>`43af5125014e0166` *(r2)* | **`PASS`** | `.82` `bd-pytest-digest` `tests/test_bh_band_scaffold.py`: **`PASS` (`11/11 passed in 3.13s`, `rc=0`)** |
| **`p5-failopen-carry003-bd-worker-A2-A`** | `/home/mboyle/bd-persist/harness-cuts/p5-failopen-carry003-bd-worker-A2-A/DONE.md`<br>`/home/mboyle/bd-persist/harness-work/FIX/p5-failopen-carry003-bd-worker-A2-A/` | `d10ca381d8f4b9ebb3e2266ae52f4376c93841b1`<br>(`LANDED f0d995e` on `harness` `main`) | `68fcf45b3c4acf1d25b33d18dc40ca78598ffedd1a4202aa2e85f55c5f1a8b68` | **`PASS`** | `.82` `bd-pytest-digest` `tests/test_p5_failopen_carry003.py`: **`PASS` (`11/11 passed in 3.13s`, `rc=0`)** |
| **`limitwatch-codex-B2B-TESTFIX`** | `/home/mboyle/bd-persist/harness-cuts/limitwatch-codex-bd-worker-B4-B/DONE.md`<br>`patches/09-p3-limitwatch-codex-test.patch` | `fb1a433d731f448e65cb798cc7a266b20671972d`<br>(Product `LANDED 95df26f` on `harness` `main`) | `51f01fc350401cb67554341aff544c32e557a52916c2253d8d5b027f155f13b1` | **`PASS`** | `.82` `bd-pytest-digest` `tests/test_limitwatch_codex_probe.py` against `FIX/limitwatch-codex`: **`PASS: 7 passed in 11.63s` (`rc=0`)** |
| **`o1504-rule21-orphan-bd-worker-A2-A`** | `/home/mboyle/bd-persist/harness-work/FIX/o1504-rule21-orphan-bd-worker-A2-A/DONE.md` | `live:57be572e` (`FLEET_RULE.md` & `FLEET_RULE-FLOOR.md`) | `0334947cf1bc437161e730ab2e1e0f2587fc78c73f054bfee16b4ebfd6f0c11f` (`live.diff`) | **`PASS`** | Verified live `FLEET_RULE.md` & `FLEET_RULE-FLOOR.md` have `0` orphan rule-21 lines (`0/0` hits, `rc=0`) |

---

## 5. Canonical Gate Block

```text
WRITTEN-AT: 2026-10-19T00:15:45Z
CENSUS: WAVE-A-13-CUTS (SWARM-001: F026,F051,F060 | SWARM-002: o1670-l28,o1670-l31,o1670-l33,o1670-g3-01/l13,o1670-g4-04/l14 | SWARM-003: bh-018-AGY-council,bh-bandscaf-bd-agy-flow-1,p5-failopen-carry003-bd-worker-A2-A,limitwatch-codex-B2B-TESTFIX,o1504-rule21-orphan-bd-worker-A2-A)
PRECHECK: PASS
COMBINED-TREE: f9a37c7c19ed5f47a031ccdaf5caf75f21c4c2b1
PATCH-SHA256: 95b9b3c30a4458770fe809ffdadf36f2aa5675b73458c08ad612f17de65987ec
```
