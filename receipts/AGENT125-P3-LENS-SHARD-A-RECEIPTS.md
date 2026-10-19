# AGENT-125: `P3-LENS-QUEUE.tsv` Shard A (Rows 1..33) Parallel Verification & 3-Platform Audit Receipts

- **Timestamp**: `2026-10-18T23:38:00Z`
- **Agent**: `Agent-125` (`P3-LENS-QUEUE.tsv` Shard A — Rows 1..33 Parallel Verifier & Codex/Claude/Local `.137` Assistant)
- **Live Stream Log**: `/var/tmp/bd-headless-streams/agent125-p3-lens-shard-a.log` (`:4083` visible)
- **Benchmark Isolation Invariant**: Strictly observed — zero touches or connections to `.72` (`10.0.70.72`) or `.83` (`10.0.70.83`) while `Agent-37` completes the benchmark.
- **Coordination**: Synced with `Agent-114` (`/var/tmp/bd-headless-streams/agent114-p3-bh1-unpark-reconciliation.log`, `p3_66=66, p3_cur=31, union_p3=82`) and `Agent-126` (Shard B Rows 34..66).

---

## 1. Executive Summary & Denominator Reconciliation

1. **66 Non-Closed `P3-LENS-QUEUE.tsv` Population (`UNPARK-CENSUS.tsv`)**:
   - Total non-closed `P3-LENS-QUEUE.tsv` rows in `/home/mboyle/bd-persist/state/UNPARK-CENSUS.tsv`: **`66` rows** (**`28` OPEN** + **`38` UNKNOWN**).
   - **Shard A Scope (Rows 1..33)**: `15` `OPEN` + `18` `UNKNOWN` = **`33` rows**.
2. **Live `/home/mboyle/bd-persist/queues/P3-LENS-QUEUE.tsv` Population**:
   - Current live queue holds **`31` rows** (`14` overlap with Shard A Rows 1..33; `17` additional rows from Shard B / recent BH1-B6 & BH2 additions, also cross-referenced in Section 4 below).
3. **Shard A (Rows 1..33) Disposition Breakdown**:
   - **`LANDED / CLOSED-STALE / SUPERSEDED` (`21 / 33`)**: Proven landed in `/home/mboyle/BulkDownloader` git history (`git log --all --grep=<id>`) or superseded by newer-generation landed cuts (`G2`/`G3`/`G4`/`r1`/`r2`/`r3-refresh`/`LENSOBJ`).
   - **`DECIDED BOARD (Ready for Landverify / Integrator Apply)` (`5 / 33`)**:
     1. `bh-018-AGY-council` (`DECIDED 3x BOARD ff8e48d2 -> landverify APPLY`, 5 verdicts on disk)
     2. `bh-bandscaf-bd-agy-flow-1` (`DECIDED BOARD A1-A G2 8ec7f038 -> integrator`, 2 verdicts on disk)
     3. `p5-failopen-carry003-bd-worker-A2-A` (`DECIDED BOARD B3-B 68fcf45b -> landverify`, 1 verdict on disk)
     4. `limitwatch-codex-B2B-TESTFIX` (`DECIDED BOARD cx-1 -> integrator`, 3 verdicts on disk + `fc72c3fa7`)
     5. `o1504-rule21-orphan-bd-worker-A2-A` (`DECIDED BOARD bd-agy-trainer-1`, 2 verdicts on disk)
   - **`DECIDEDBOARD + Landed in Train / Spec` (`1 / 33`)**:
     - `bh1-38-codex-r127c` (`bd-worker-C2-C BOARD` in `.review/VERDICT-correctness-bd-worker-C2-C.md`)
   - **`DECIDED REFUTE / BOUNCE / SCOPED_BOARD (Superseded by R1/R3/Codex Cuts or Pending Re-cut)` (`6 / 33`)**:
     - `bh2-12-bd-agy-sonnet-2` (`REFUTE flash-1 e014accd` -> superseded by `bh2-12-g4-bd-worker-B3-B` landed `d880e3f33`)
     - `bh2-35-bd-agy-sonnet-2` (`REFUTE flash-3 73b9d081` -> superseded by `bh2-35-r1-bd-worker-A3-A` landed `ff41a4b40`)
     - `bh2-40-bd-agy-sonnet-2` (`BOUNCE cx-1 3eac26e8` -> needs staged test / `HEAD==BASE` fix)
     - `bh2-08-G2-bd-agy-worker-1` (`SCOPED_BOARD A3-A ce8b0959` -> unstage G1 test or re-declare)
     - `bh2-45-bd-agy-sonnet-2` (`REFUTE bd-agy-worker-1 f9acbe6a` -> superseded by `bh2-45-codex-r127c` landed `e1f68dd10`)
     - `bh1-29-bd-cx-worker-2` (`BOUNCE bd-worker-A1-A 00617a8d` -> superseded by `bh1-29 r2` landed `efacfc763`)

---

## 2. Parallel 3-Platform Diff Audits & `.82` Ramdisk `bd-pytest-digest` Receipts

To verify the 6 `UNKNOWN (COULD NOT LOOK worktree absent O1540/O1660)` cuts in Shard A (Rows 26, 28, 30, 31, 32, 33) whose preserved artifacts were located in `/home/mboyle/bd-persist/harness-work/FIX/<id>`, we ran parallel subprocesses across **Codex (`codex exec`)**, **Claude (`claude --print` via `.claude-b`)**, **Local `.137:11434` (`qwen2.5-coder:7b-16k`)**, and **`.82` (`10.0.70.82`) `/dev/shm` ramdisk `bd-pytest-digest`**:

| Platform / Runner | Command / Endpoint | Target Cuts / Tests | Latency & Exit Code | Measured Output / Verdict |
|---|---|---|---|---|
| **Local `.137:11434` (`qwen2.5-coder:7b-16k`)** | `POST http://10.0.10.137:11434/api/generate` | `o1503-gpu-p0`, `bh2-14-fwd-bd-worker-B3-B`, `o1503-gpu-p2`, `bh2-12-g4-bd-worker-B3-B`, `o1505-distill`, `o1505-intake-b` | `2.5s – 4.6s` (`HTTP 200`, 6 parallel workers) | `o1503-gpu-p0`: **BOARD** (`ci-summary` & `rag-embed` in `config.yaml`)<br>`o1505-distill`: **BOARD** (LiteLLM `distill` alias + Ollama fallback)<br>`o1505-intake-b`: **BOARD** (complete batch guard in `bd-pm-batch-summary.py`)<br>`bh2-12-g4-bd-worker-B3-B`: **PATCH/BOARD** (G3 rebased on live + DRY guard)<br>`bh2-14-fwd-bd-worker-B3-B`: **REFUTE/STALE** (G1 `ROLE-AGENT.md` drift; landed in `55023ed56`)<br>`o1503-gpu-p2`: **REFUTE/STALE** (G1 refuted; G2 landed in `c496ee3af`) |
| **Claude (`claude --print`)** | `CLAUDE_CONFIG_DIR=/home/mboyle/.claude-b /home/mboyle/.local/bin/claude --print ...` | `o1503-gpu-p0`, `bh2-14-fwd-bd-worker-B3-B` | `14.0s` (`rc=0`) | `o1503-gpu-p0: STALE - ci-summary & rag-embed already live in f685a7a57`<br>`bh2-14-fwd-bd-worker-B3-B: STALE - dead-session check already in 55023ed56` |
| **Codex (`codex exec`)** | `/home/mboyle/.local/bin/codex exec --skip-git-repo-check ...` | `o1503-gpu-p2`, `o1505-intake-b` | `31.8s` (`rc=0`) | `o1503-gpu-p2: STALE - The script is already live; the cited cut worktree is absent.`<br>`o1505-intake-b: STALE - The script is already live and differs from the cited candidate.` |
| **`.82` Ramdisk `bd-pytest-digest`** | `ssh 10.0.70.82 'cd /dev/shm/agent125-shard-a/bh2-12-g4-bd-worker-B3-B && BD_BH2_12_CANDIDATE=.../bd-launch-agy.sh /home/mboyle/BulkDownloader/venv/bin/pytest -q test_bh2_12_agy_cardinality_guard.py' \| /home/mboyle/bin/bd-pytest-digest` | `bh2-12-g4-bd-worker-B3-B/test_bh2_12_agy_cardinality_guard.py` (10 end-to-end launcher cardinality tests) | `10.38s` (`rc=0`) | `PASS: 10 passed` (`.......... [100%] 10 passed in 10.38s`) |

---

## 3. Shard A (Rows 1..33 of 66 Non-Closed `P3-LENS-QUEUE.tsv` Items) Verification Matrix

| Row | Cut ID | UNPARK-CENSUS State | Cut Path & Disk Artifacts (`DONE.md` / `VERDICT-*.md`) | `BulkDownloader` Git Provenance (`git log --all --grep=<id>`) | Live `P3-LENS-QUEUE.tsv` Holder / Status | Verified Disposition |
|---|---|---|---|---|---|---|
| **01** | `bh-018-AGY-council` | `UNKNOWN` | `/home/mboyle/bd-persist/harness-cuts/bh-018-AGY-council` (`DONE.md`=1, `VERDICT-*.md`=5: `shape-A1`, `A1-A`, `B1-B`, `B2-B`, `B2-B-G3`) | Harness-only cut (`NONE` in `BulkDownloader`) | `DECIDED 3x BOARD ff8e48d2 -> landverify APPLY` | **READY TO APPLY (`3x BOARD ff8e48d2`)** |
| **02** | `bh-k2-021-AGY-council` | `OPEN` | `/home/mboyle/bd-persist/harness-cuts/bh-k2-021-AGY-council` (`DONE.md`=1, `VERDICT-*.md`=4: `A1-A`, `B1-B`, `B2-B`, `shape-A1-A`) | Harness-only cut (superseded on live hub `72169f4a`) | `CLOSED-STALE lensrouter-B1-B 10-18: 3x BOARD; hub live line 72169f4a != candidate 0d6bbfa8 (superseded)` | **CLOSED-STALE (Superseded on live)** |
| **03** | `persona-bdp-bd-worker-A3-A` | `OPEN` | `/home/mboyle/bd-persist/harness-cuts/persona-bdp-bd-worker-A3-A` (`DONE.md`=1, `VERDICT-*.md`=8) | Harness prompt cut applied in place `09-28`; superseded by O1489/O1613 | `CLOSED-STALE lensrouter-B1-B 10-18: applied in place 09-28; live worker/pm prompts 0 RULE58-73 markers` | **CLOSED-STALE (Applied in place)** |
| **04** | `token-opt-0004` | `OPEN` | `/home/mboyle/bd-persist/harness-work/FIX/token-opt-0004` (`DONE.md`=1, `VERDICT-*.md`=3: `fixer-A1-A`, `A2-A`, `shape-A1-A`) | Live `bd_satellite_router.py` `24fa3628 != base db420321` (patch applied) | `CLOSED-STALE lensrouter-B1-B 10-18: 2x BOARD; live bd_satellite_router.py 24fa3628 != base db420321` | **CLOSED-STALE (Applied in place)** |
| **05** | `bh-bandscaf-bd-agy-flow-1` | `UNKNOWN` | `/home/mboyle/bd-persist/harness-cuts/bh-bandscaf-bd-agy-flow-1` (`DONE.md`=1, `VERDICT-*.md`=2: `cx-worker-1`, `A1-A`) | Harness `operations.py` cut (`band.sh` already live) | `DECIDED BOARD A1-A G2 8ec7f038 -> integrator (deploy operations.py only; band.sh live)` | **READY FOR INTEGRATOR (`BOARD 8ec7f038`)** |
| **06** | `p5-failopen-carry003-bd-worker-A2-A` | `UNKNOWN` | `/home/mboyle/bd-persist/harness-cuts/p5-failopen-carry003-bd-worker-A2-A` (`DONE.md`=1, `VERDICT-*.md`=1: `B3-B`) | Harness cut (`68fcf45b`) | `DECIDED BOARD B3-B (68fcf45b) -> landverify` | **READY FOR LANDVERIFY (`BOARD 68fcf45b`)** |
| **07** | `p5-mainguard-write-bd-agy-fixer-1` | `OPEN` | `/home/mboyle/bd-persist/harness-cuts/p5-mainguard-write-bd-agy-fixer-1` (`DONE.md`=1, `VERDICT-*.md`=1: `cx-worker-1`) | Superseded by `p5-mainguard-LENSOBJ` (`cx-1` fix in place) | Removed in O1789 clean (`SUPERSEDED by p5-mainguard-LENSOBJ`) | **CLOSED-SUPERSEDED (`p5-mainguard-LENSOBJ`)** |
| **08** | `p5-failopen-cache009-bd-worker-B2-B` | `OPEN` | `/home/mboyle/bd-persist/harness-cuts/p5-failopen-cache009-bd-worker-B2-B` (`DONE.md`=1, `VERDICT-*.md`=2: `cx-worker-1`, `B4-B`) | Superseded by `p5-cache009-LENSOBJ` (`cx-1` fix in place) | Removed in O1789 clean (`SUPERSEDED by p5-cache009-LENSOBJ`) | **CLOSED-SUPERSEDED (`p5-cache009-LENSOBJ`)** |
| **09** | `limitwatch-codex-B2B-TESTFIX` | `UNKNOWN` | `/home/mboyle/bd-persist/harness-cuts/limitwatch-codex-bd-worker-B4-B/.review` (`DONE.md`=1, `VERDICT-*.md`=3: `cx-worker-1`, `B1-B`, `shape-B2-B`) | `fc72c3fa7 spec: limitwatch-d7c7-T128` | `DECIDED BOARD cx-1 (T1 binding leg, O1484 item 4) -> integrator` | **READY FOR INTEGRATOR (`BOARD cx-1`)** |
| **10** | `p5-opusb003-LENSOBJ` | `OPEN` | `/home/mboyle/bd-persist/harness-work/FIX/p5-opusb003-lens-bd-cx-worker-1` (`VERDICT-*.md`=2: `A2-A`, `B2-B`) | Superseded by author G3 (`3fd7026a`) BOARD `A2-A` -> applied | Removed in O1789 clean (`SUPERSEDED: author G3 3fd7026a BOARD A2-A -> applied`) | **CLOSED-SUPERSEDED (G3 `3fd7026a` applied)** |
| **11** | `allowlist-like-residual-bd-worker-B2-B` | `OPEN` | `/home/mboyle/bd-persist/harness-cuts/allowlist-like-residual-bd-worker-B2-B` (`DONE.md`=1, `VERDICT-*.md`=3: `cx-worker-1`, `A5-A`, `B3-B`) | Superseded by `like-residual-LENSOBJ` (`cx-1` fix) | Removed in O1789 clean (`SUPERSEDED by like-residual-LENSOBJ`) | **CLOSED-SUPERSEDED (`like-residual-LENSOBJ`)** |
| **12** | `bh2-12-bd-agy-sonnet-2` | `OPEN` | `/home/mboyle/bd-local-wt/bh2-12-bd-agy-sonnet-2` (`DONE.md`=1, `VERDICT-*.md`=4: `audit-1`, `flash-1`, `A1-A`, `B4-B`) | `d880e3f33 spec: bh2-12-g4-bd-worker-B3-B` | Removed in O1789 clean (`REFUTE flash-1 e014accd`; superseded by `bh2-12-g4`) | **CLOSED-SUPERSEDED (Landed via `bh2-12-g4` `d880e3f33`)** |
| **13** | `bh2-35-bd-agy-sonnet-2` | `OPEN` | `/home/mboyle/bd-local-wt/bh2-35-bd-agy-sonnet-2` (`DONE.md`=1, `VERDICT-*.md`=6) | `ff41a4b40 apply bh2-35 a8e5cda5 BOARD` (via `bh2-35-r1-bd-worker-A3-A`) | `DECIDED REFUTE flash-3 73b9d081` (superseded by Row 20 `bh2-35-r1-bd-worker-A3-A` landed `ff41a4b40`) | **CLOSED-SUPERSEDED (Landed via `bh2-35-r1` `ff41a4b40`)** |
| **14** | `bh2-40-bd-agy-sonnet-2` | `OPEN` | `/home/mboyle/bd-local-wt/bh2-40-bd-agy-sonnet-2` (`DONE.md`=1, `VERDICT-*.md`=1: `cx-worker-1`) | Not landed (`BOUNCE cx-1 3eac26e8`) | Removed from lens queue pending fixer re-cut (`no test staged; HEAD!=BASE`) | **DECIDED BOUNCE (`3eac26e8` -> Fixer Re-cut)** |
| **15** | `bh2-08-G2-bd-agy-worker-1` | `OPEN` | `/home/mboyle/bd-local-wt/bh2-08-bd-agy-worker-1` (`DONE.md`=1, `VERDICT-*.md`=2: `A3-A`, `codex-r127c`) | Not landed (`SCOPED_BOARD A3-A ce8b0959`) | Removed from lens queue pending index cleanup (`index stages G1 test`) | **DECIDED SCOPED_BOARD (`ce8b0959` -> Unstage G1 test)** |
| **16** | `bh2-45-bd-agy-sonnet-2` | `OPEN` | `/home/mboyle/bd-local-wt/bh2-45-bd-agy-sonnet-2` (`DONE.md`=1, `VERDICT-*.md`=1: `agy-worker-1`) | `e1f68dd10 apply bh2-45 a1cf2ffb BOARD` (via `bh2-45-codex-r127c`) | `DECIDED REFUTE bd-agy-worker-1 f9acbe6a` (superseded by `bh2-45-codex-r127c` landed `e1f68dd10`) | **CLOSED-SUPERSEDED (Landed via `bh2-45-codex-r127c` `e1f68dd10`)** |
| **17** | `bh2-03-bd-agy-flash-1-G3` | `UNKNOWN` | Worktree cleaned after landing | `b462b0d12 spec: bh2-03-fwd-bd-worker-B5-B` | Removed in O1789 clean (`DECIDED BOARD sonnet-1 e0617f2d` -> landed `b462b0d12`) | **CLOSED-LANDED (`b462b0d12`)** |
| **18** | `bh2-41-bd-agy-worker-2-R3` | `UNKNOWN` | `/home/mboyle/bd-local-wt/bh2-41-bd-agy-worker-2` (`DONE.md`=1, `VERDICT-*.md`=4: `flash-1`, `flash-3`, `A1-A`, `A6-A`) | `146c174a4` (rebased onto `bh2-41` relay) + `ee72bd7` (`bh2-41-bd-kimi-worker-2`) | `DECIDED BOARD flash-1 59023646 (R3 verified: F1/F2 fixed; RED 4/7 -> GREEN 11/0)` | **CLOSED-LANDED (`ee72bd7` / `146c174a4`)** |
| **19** | `bh2-05-bd-agy-sonnet-1-G3` | `UNKNOWN` | `/home/mboyle/bd-local-wt/bh2-05-bd-agy-sonnet-1` (`DONE.md`=1, `VERDICT-*.md`=5: `audit-1`, `flash-3`, `A1-A`, `B4-B`, `A6-A`) | `cae389b59 apply bh2-05 G3 eb6b6be1 BOARD` | G3 already landed in `cae389b59` (live row 9 is G1 `REFUTE`) | **CLOSED-LANDED (`cae389b59`)** |
| **20** | `bh2-06-bd-agy-sonnet-2-B6` | `UNKNOWN` | `/home/mboyle/bd-local-wt/bh2-06-bd-agy-sonnet-2` (`DONE.md`=1, `VERDICT-*.md`=4: `flash-2`, `sonnet-1`, `B4-B`, `codex-r127c`) | `f747f616f train4: bh2-06 verdict-route ts= by pattern` | `LANDED as f747f61 train4 (O3322 integrator 2026-10-18)` | **CLOSED-LANDED (`f747f616f` train4)** |
| **21** | `bh2-20-bd-agy-worker-1-A5A` | `UNKNOWN` | Worktree cleaned after train6 landing | `d5055eb39 train6: bh2-20 fix-r2 inbox-drain unreadable entries skipped+named+counted` | Removed in O1789 clean (`DECIDED BOARD bd-cx-worker-1 b0177df2`) | **CLOSED-LANDED (`d5055eb39` train6)** |
| **22** | `bh2-29-bd-agy-fixer-1-A6A` | `UNKNOWN` | `/home/mboyle/bd-local-wt/bh2-29-bd-agy-fixer-1` (`DONE.md`=1, `VERDICT-*.md`=4: `sonnet-1`, `cx-worker-2`, `A1-A`, `A6-A`) | `5eb32ce49 train7: bh2-29 fix-r2 bd-say.sh atomic inbox publish` | `DECIDED BOARD sonnet-1 2b1ee71f` (landed via `5eb32ce49` train7) | **CLOSED-LANDED (`5eb32ce49` train7)** |
| **23** | `bh1-38-codex-r127c` | `OPEN` | `/home/mboyle/bd-local-wt/bh1-38-codex-r127c` (`DONE.md`=1, `VERDICT-*.md`=2: `B6-B`, `C2-C`) | Harness cut (`bd-underived-census.sh` selftest isolation) | `bd-worker-C2-C BOARD` | **DECIDED BOARD (`bd-worker-C2-C`)** |
| **24** | `bh1-29-bd-cx-worker-2` | `OPEN` | `/home/mboyle/bd-local-wt/bh1-29-bd-cx-worker-2` (`DONE.md`=1, `VERDICT-*.md`=3: `shape-A1-A`, `A1-A`, `B6-B`) | `efacfc763 bh1-29 r2: bd-launch-kimi.sh duplicate-session check before any seat write + per-NAME flock` | Removed in O1789 clean (`BOUNCE 00617a8d` superseded by `bh1-29 r2` `efacfc763`) | **CLOSED-LANDED (`efacfc763` via `bh1-29 r2`)** |
| **25** | `bh1-50-codex-r127c` | `OPEN` | `/home/mboyle/bd-local-wt/bh1-50-codex-r127c` (`DONE.md`=1, `VERDICT-*.md`=2: `B6-B`, `C2-C`) | `5f501fe11 bh1-50 r3-refresh: harness land by blob (cut tree 41fa3483 on BASE 61570a43; BOARD per O1672)` | `CLOSED-LANDED lensrouter-B1-B 10-18: landed via r3-refresh 5f501fe` | **CLOSED-LANDED (`5f501fe11`)** |
| **26** | `o1503-gpu-p0` | `UNKNOWN` | `/home/mboyle/bd-persist/harness-work/FIX/o1503-gpu-p0` (`DEPLOY.md`, `o1503-vs-live.patch`, `config.yaml`) | `f685a7a57 spec: o1503-gpu-p0` | Audited by Claude `--print` + `.137` Qwen2.5-Coder: **BOARD/STALE** (landed in `f685a7a57`) | **CLOSED-LANDED (`f685a7a57`)** |
| **27** | `o1504-rule21-orphan-bd-worker-A2-A` | `UNKNOWN` | `/home/mboyle/bd-persist/harness-work/FIX/o1504-rule21-orphan-bd-worker-A2-A` (`DONE.md`=1, `VERDICT-*.md`=2: `audit-2`, `trainer-1`) | Law hygiene (`FLEET_RULE.md` Rule 21 orphan deleted; Tier A in sync 9 rules) | `DECIDED BOARD bd-agy-trainer-1 - (law hygiene: rule 21 orphan deleted)` | **READY FOR PM / INTEGRATOR (`BOARD trainer-1`)** |
| **28** | `bh2-14-fwd-bd-worker-B3-B` | `UNKNOWN` | `/home/mboyle/bd-persist/harness-work/FIX/bh2-14-fwd-bd-worker-B3-B` (`DONE.md`=1, `live-bd-launch-kimi.sh`) | `55023ed56 spec: bh2-14-fwd-bd-worker-B3-B` | Audited by Claude `--print` + `.137` Qwen2.5-Coder: **STALE/LANDED** (`55023ed56`) | **CLOSED-LANDED (`55023ed56`)** |
| **29** | `row113-r2-bd-worker-B2-B` | `UNKNOWN` | Worktree cleaned after landing | `ccf71ee2f spec: row113-r2-bd-worker-B2-B (+ fixup folded in: project-knowledge/IMPROVEMENT_BACKLOG.md)` | Removed in O1789 clean | **CLOSED-LANDED (`ccf71ee2f`)** |
| **30** | `o1503-gpu-p2` | `UNKNOWN` | `/home/mboyle/bd-persist/harness-work/FIX/o1503-gpu-p2` (`bd-ci-summarize.py`, `g1/DONE-G1-refuted.md`) | `c496ee3af spec: o1503-gpu-p2` (and harness `f9809c344455`) | Audited by Codex `exec` + `.137` Qwen2.5-Coder: **STALE/LANDED** (`c496ee3af`) | **CLOSED-LANDED (`c496ee3af`)** |
| **31** | `bh2-12-g4-bd-worker-B3-B` | `UNKNOWN` | `/home/mboyle/bd-persist/harness-work/FIX/bh2-12-g4-bd-worker-B3-B` (`DONE.md`=1, `bd-launch-agy.patch`, `test_bh2_12_agy_cardinality_guard.py`) | `d880e3f33 spec: bh2-12-g4-bd-worker-B3-B` | Verified on `.82` ramdisk `bd-pytest-digest`: **`PASS: 10 passed` (`10.38s`)** + landed `d880e3f33` | **CLOSED-LANDED (`d880e3f33`, `PASS: 10 passed`)** |
| **32** | `o1505-distill` | `UNKNOWN` | `/home/mboyle/bd-persist/harness-work/FIX/o1505-distill` (`DEPLOY.md`, `o1505-vs-live.patch`, `VERDICT-correctness-bd-worker-A4.md`) | `98d16de63 spec: o1505-distill` | Audited by `.137` Qwen2.5-Coder + `A4` verdict: **BOARD/LANDED** (`98d16de63`) | **CLOSED-LANDED (`98d16de63`)** |
| **33** | `o1505-intake-b` | `UNKNOWN` | `/home/mboyle/bd-persist/harness-work/FIX/o1505-intake-b` (`DEPLOY.md`, `bd-pm-batch-summary.py`, `MEASURE-G2-live-copy.json`) | `3ac7eecee spec: o1505-intake-b` (and harness `67c66d7a86e6`) | Audited by Codex `exec` + `.137` Qwen2.5-Coder: **BOARD/LANDED** (`3ac7eecee`) | **CLOSED-LANDED (`3ac7eecee`)** |

---

## 4. Cross-Reference: Remaining 17 Live Rows in `/home/mboyle/bd-persist/queues/P3-LENS-QUEUE.tsv` (Total 31 Live Rows)

For 100% completeness across both `UNPARK-CENSUS.tsv` (`Rows 1..33`) and the live 31-row `/home/mboyle/bd-persist/queues/P3-LENS-QUEUE.tsv`, here are the remaining 17 live rows:

| Live Row # | Cut ID | Cut Path | Live Holder / Verdict Status | Git / Landed Provenance |
|---|---|---|---|---|
| **05** | `bh1-15-bd-worker-B6-B` | `/home/mboyle/bd-local-wt/bh1-15-bd-worker-B6-B` | `LANDED blobs==HEAD e3c85fa (O3322 integrator 2026-10-18)` | **CLOSED-LANDED (`e3c85fa`)** |
| **06** | `bh1-31-bd-worker-B6-B` | `/home/mboyle/bd-local-wt/bh1-31-bd-worker-B6-B` | `LANDED blobs==HEAD e3c85fa (O3322 integrator 2026-10-18)` | **CLOSED-LANDED (`e3c85fa`)** |
| **08** | `bh1-51-bd-worker-B6-B` | `/home/mboyle/bd-local-wt/bh1-51-bd-worker-B6-B` | `LANDED blobs==HEAD e3c85fa (O3322 integrator 2026-10-18)` | **CLOSED-LANDED (`e3c85fa`)** |
| **09** | `bh2-05-bd-agy-sonnet-1` | `/home/mboyle/bd-local-wt/bh2-05-bd-agy-sonnet-1` | `DECIDED REFUTE A1-A 6ac842a7` | **CLOSED-SUPERSEDED (G3 landed `cae389b59`)** |
| **11** | `bh2-09-bd-agy-worker-2` | `/home/mboyle/bd-local-wt/bh2-09-bd-agy-worker-2` | `LANDED as 9f26568 train6 (O3322 integrator 2026-10-18)` | **CLOSED-LANDED (`9f26568` train6)** |
| **12** | `bh2-10-bd-agy-fixer-1` | `/home/mboyle/bd-local-wt/bh2-10-bd-agy-fixer-1` | `DECIDED BOARD A1-A 174542d5 (un-prefixed lens seats matched)` | **READY FOR INTEGRATOR (`BOARD 174542d5`, 24/24 shell tests pass)** |
| **13** | `bh2-11-bd-agy-trainer-1-G2` | `/home/mboyle/bd-local-wt/bh2-11-bd-agy-trainer-1` | `DECIDED BOARD B4-B/agy-worker-1 de9e4198 (default fix dir)` | **READY FOR INTEGRATOR (`BOARD de9e4198`)** |
| **14** | `bh2-21-bd-agy-worker-2` | `/home/mboyle/bd-local-wt/bh2-21-bd-agy-worker-2` | `DECIDED REFUTE A3-A 00b09ce8` | **DECIDED REFUTE (`00b09ce8` -> Fixer Re-cut)** |
| **15** | `bh2-23-bd-agy-trainer-1` | `/home/mboyle/bd-local-wt/bh2-23-bd-agy-trainer-1` | `DECIDED REFUTE A1-A 35b7397e` | **DECIDED REFUTE (`35b7397e` -> Reassigned `bd-kimi-worker-2`)** |
| **16** | `bh2-26-bd-agy-fixer-1` | `/home/mboyle/bd-local-wt/bh2-26-bd-agy-fixer-1` | `DECIDED BOARD A1-A e27dff03 (PM_SEAT_NOW trimmed)` | **READY FOR INTEGRATOR (`BOARD e27dff03`, 8/8 shell tests pass)** |
| **18** | `bh2-33-bd-agy-worker-c1` | `/home/mboyle/bd-persist/harness-work/FIX/bh2-33-bd-agy-worker-c1` | `CLOSED-LANDED lensrouter-B1-B 10-18: G2 landed 2f1bd02` | **CLOSED-LANDED (`2f1bd02`)** |
| **20** | `bh2-35-r1-bd-worker-A3-A` | `/home/mboyle/bd-local-wt/bh2-35-r1-bd-worker-A3-A` | `DECIDED BOARD flash-3 9409a9d0` | **CLOSED-LANDED (`ff41a4b40`)** |
| **21** | `bh2-41-bd-agy-worker-2` | `/home/mboyle/bd-local-wt/bh2-41-bd-agy-worker-2` | `DECIDED REFUTE flash-3 59023646` | **CLOSED-SUPERSEDED (R3 BOARD & `ee72bd7` landed)** |
| **23** | `bh2-41-bd-kimi-worker-2` | `/home/mboyle/bd-local-wt/bh2-41-bd-kimi-worker-2` | `CLOSED-LANDED lensrouter-B1-B 10-18: ee72bd7` | **CLOSED-LANDED (`ee72bd7`)** |
| **25** | `bh2-45-codex-r127c` | `/home/mboyle/bd-local-wt/bh2-45-codex-r127c` | `DECIDED BOARD bd-agy-worker-1 4f4fe42e` | **CLOSED-LANDED (`e1f68dd10`, 29/29 assertions pass)** |
| **26** | `bh2-49-bd-agy-worker-g1` | `/home/mboyle/bd-local-wt/bh2-49-bd-agy-worker-g1` | `CLOSED-LANDED lensrouter-B1-B 10-18: 1087c6d` | **CLOSED-LANDED (`1087c6d`)** |
| **29** | `o1664-platform-score-bd-worker-B1` | `/home/mboyle/bd-local-wt/o1664-platform-score-bd-worker-B1` | `DECIDED BOARD D2-D G2 -> PM applies (O1664)` | **READY FOR PM APPLY (`BOARD D2-D G2`)** |
