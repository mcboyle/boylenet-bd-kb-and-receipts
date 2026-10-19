# BD-KB Volume 31: Read-Only Investigation Dossier — BH1-01 through BH1-10 (Say-Gate, Claim-Object, Usage, Telemetry & Reaper)
> **Lead Investigator**: `Fable5-Investigator-1` (`claude-fable-5-1 (.claude-a)`)
> **Subagent Squad**: 2x Parallel `AGY claude+gpt` (`claude-sonnet-5-5-high` & `claude-sonnet-5-5-low`) Read-Only Code Investigators
> **Generated At**: `2026-10-19T23:33:29Z`
> **Scope**: Strictly Read-Only Investigation Dossier for `bd-pm-B` (`Zero Code Changes`, `O3416` Compliant)

---

# BD-KB Volume 31: Read-Only Investigation Dossier — BH1-01 through BH1-10

**Subject:** Say-Gate, Claim-Object, Usage, Telemetry & Reaper rows
**Audience:** `bd-pm-B`
**Author:** `Fable5-Investigator-1` (Lead PI), synthesizing two specialist reports
**Mode:** 100% read-only. No tools called, no commands run, no code modified.
**Date:** 2026-10-19

---

## 0. Executive summary

Ten rows were inspected. One row (BH1-02) has a concrete, visible code defect with a clear fix. One row (BH1-04) is most likely already fixed or mis-attributed and should close at STEP0. Two rows (BH1-06, BH1-07) have a well-supported mechanism visible in the supplied source. The remaining six rest on row text and evidence notes only, because the source files were not in the context window given to the specialists.

**Critical honesty note on line numbers.** Neither specialist excerpt carried line numbers, and both excerpts were truncated at roughly 10–12 KB. Every location below is given by **file path plus function name**. Where a line number is requested but unavailable, it is marked `L:UNVERIFIED`. `bd-pm-B` must pin lines with `grep -n` before dispatching any cut. Fabricating a line number here would be a rule-6 "COULD NOT LOOK" violation of exactly the kind BH1-03 is about.

### Summary table

| Row | Slug | Sev / Size / Noise | Primary file | Function / site | Confidence | Verdict |
|---|---|---|---|---|---|---|
| BH1-01 | say-gate-preexisting | — | PreToolUse bd-say hook; `bd-say.sh` | static command parser; runtime re-check | Low (no source) | Real, mitigated, pre-existing |
| BH1-02 | claim-object-homewide-find | — | `/home/mboyle/bd-persist/harness/bd-claim-object.sh` | `closed()` | Medium-high | Real defect, fix well-defined |
| BH1-03 | usage-csv-partial | — | usage CSV writer (unknown; possibly `bd_cost_accounting.py`) | CSV emit path | Low (no source) | Real contract gap |
| BH1-04 | launch-base-loop-stderr | — | `/home/mboyle/bd-persist/harness/bd-telemetry-daemon.py` (not `bd-launch-role.sh`) | `_hook_log()`, `caveman_minify()` | Medium | Likely DUP/RESOLVED; STEP0 |
| BH1-05 | role-prompt-trim-wave | — | ~100 role-prompt files | content budget | Medium | Content work, not a bug |
| BH1-06 | supply-guard-closed-markers | H / S / MED | `/home/mboyle/bd-persist/harness/bd-supply-guard.sh` | section "(c) starved DONE"; skip checks | Medium | Real, fix well-defined |
| BH1-07 | pool-usage-refresher-residuals | H / S / LOW | `/home/mboyle/bin/bd-pool-usage-refresher.py` | `poll_claude_oauth_caches()` | Medium (A1), Low (A2) | Real; drift risk with BH1-17 |
| BH1-08 | telemetry-hung-sensor | H / S / LOW | telemetry-registry (unknown) | sensor cache/serve path | Low | Real per verdict artifact |
| BH1-09 | worker-claim-lost-remote | H / M / MED | registry reaper (unknown) | liveness/reap path | Low | Real incident; path unlocated |
| BH1-10 | rwgh-fifo-test-leak | H / S / MED | rwg-hardening FIFO tests (unknown) | FIFO reader in test | Medium (mechanism), Low (file) | Real; orphan readers observed |

---

## 1. Row-by-row findings

### BH1-01 — say-gate-preexisting

**Location.** The PreToolUse say-gate hook script and `bd-say.sh`. Neither file was in the supplied context. Path `L:UNVERIFIED`. The only concrete pointer is the evidence note at `harness-work/O1789/dispatch-E3-D/cuts/sayhook-dollar/rc-SHD2C-C-r2/NOTE-pm-preexisting-say-gate.md`.

**Root cause.** The gate inspects Bash command text statically, before execution. Static inspection has three documented gaps:

- **A1 — nested shell opacity.** A command of the form `bash -c '$MSG'` is not unwrapped. The gate sees a quoted string, not the message, so it cannot apply its rules to the payload.
- **A2 — escaped redirect mis-tokenisation.** `\<` and `\>` are treated as redirect operators or as literal words inconsistently with how the shell itself will treat them.
- **A3 — inspected-text vs sent-text mismatch.** The text the gate parses may not be byte-identical to the text that reaches the mailbox after shell expansion.

All three are pre-existing and are **mitigated**, because `bd-say.sh` performs its own runtime re-validation after expansion. The gate is defence-in-depth, not the sole barrier. Exact function names in the hook are `UNVERIFIED`.

**Why it matters anyway.** A static gate that can be bypassed by trivial quoting gives a false sense of coverage. The fix direction is either to unwrap one level of `bash -c` / `sh -c` and re-parse, or to document that the gate is advisory and the runtime check is authoritative.

**Overlap.** BH1-15 (say-hook-path-precheck) edits the same PreToolUse bd-say hook. These two rows form one conflict group and must be serialized. BH1-31 touches bd-say's `mailbox_for` but in launcher files, not the hook.

**Gate scope.** Say-hook and `bd-say.sh` test suites (the sayhook-dollar cut's tests; names `UNVERIFIED`). Add paired controls for `bash -c '$MSG'`, `\<`/`\>`, and the runtime re-check. Run BH1-01 and BH1-15 cases in the same gate.

---

### BH1-02 — claim-object-homewide-find

**Location.** `/home/mboyle/bd-persist/harness/bd-claim-object.sh`, function `closed()`. `L:UNVERIFIED` — the visible excerpt cuts off inside the function's header comment, before its body. Evidence note: `IMPROVEMENTS/bd-pm-B-20261019-claim-object-homewide-find.md`.

**Root cause.** The header comment above `closed()` documents its own history: "H2xx -- closed() resolved ONE root while bd-train-carries resolves two…", then widens to three roots, then to a measured list: "Measured… over EVERY directory under /home/mboyle, not a named few: 15 roots hold…". The best inference, combined with the row text, is that `closed()` now runs a `find` rooted at `/home/mboyle`. That path crosses NFS mounts. A `find` descending NFS trees with stale or slow mounts produces exactly the observed symptom: a process stuck in D-state for 27 minutes.

**Blast radius depends on lock placement (UNVERIFIED).** Visible facts from the same file:

- `take` calls `writer_lock` / `lock()`.
- `BD_CLAIM_LOCK_WAIT` defaults to 30 s.
- The H340 comment says the locked section is "now state()+append only".

If `closed()` runs *inside* the lock, every other claimer hits rc=2 "could not lock" for the duration of the hang, and the whole claim subsystem stalls. If it runs *outside* the lock (as the H340 comment implies), only the calling seat hangs. `bd-pm-B` should confirm which with a read of the `take` body.

**Ruled out.** `carried_by_train` is not the culprit. It is cached for 600 s via `BD_CARRIED_TTL` and calls `bd-train-carries`, which takes about 20 s. That does not produce a 27-minute D-state.

**Fix shape.**

1. Bound the `find` to the known cut roots: `$CUTS` (`BD_CUTS`), the `bd-review-wt` directory, and the `bd-cuts` top level.
2. Wrap in `timeout`.
3. On timeout, return `unknown(...)` / COULD NOT LOOK rather than blocking. Preserve the `take` rc contract: rc=3 held, rc=4 closed, rc=5 carried, rc=6 could-not-measure.

**Overlap.** BH1-09 may touch the same file; the reaper path (`_reap`, `census_or_refuse`, `census_of`) lives here, though BH1-09 may concern the seat registry instead. `UNVERIFIED`.

**Gate scope.** bd-claim-object tests (likely a `tests/*claim_object*` glob, `UNVERIFIED`). Add a bounded-find timing control, plus one closed-cut control per root in the 15-root population so narrowing the search does not change claim outcomes.

---

### BH1-03 — usage-csv-partial

**Location.** The usage CSV writer. Not in context; path `L:UNVERIFIED`. Candidates by inference: the usage collector/census tool or `bd_cost_accounting.py`. Evidence note: `IMPROVEMENTS/bd-pm-B-20261019-usage-csv-partial.md`.

**Root cause.** When a census is incomplete, the CSV carries no column or marker saying so. A downstream reader cannot distinguish a genuine zero from a count that was never completed. This is a rule-6 violation: silence where "COULD NOT LOOK" is required.

**Fix shape.** Add a PARTIAL marker, either as a column or a trailer line. Header must stay backward-compatible for existing parsers.

**Overlap.** BH1-24 (t16-cost-accounting-coverage) lists "label PARTIAL → COULD NOT LOOK", `synthetic_zero_skipped`, and `bd_cost_accounting.py`. The two rows will fight over label wording unless resolved together. BH1-07 and BH1-17 are adjacent (usage area) but probably different files.

**Gate scope.** Usage-collector tests, e.g. `tests/test_usage_collector_advisor.py` (named by BH1-24). Add a complete-census control (no marker) and a partial-census control (marker present).

---

### BH1-04 — launch-base-loop-stderr

**Location.** The row names `bd-launch-role.sh`'s base-loop handler. That file was not in context. The *visible* ORDER-2331 r5 F6 fix is in `/home/mboyle/bd-persist/harness/bd-telemetry-daemon.py`:

- `_hook_log(line)` wraps `print(..., file=sys.stderr, flush=True)` in `try/except Exception: pass`. Its docstring says an unwritable stderr (EPIPE/ENOSPC) must not drop the record or kill the loop.
- `_caveman_one` and `caveman_minify` use it; the comment reads "raises to the boundary in `caveman_minify` (ORDER-2331 r5), which keeps the field raw".

`L:UNVERIFIED`.

**Root cause / assessment.** This is a STEP0 row and the probable outcome is **already fixed or mis-attributed**. The ORDER-2331 advisory concerns the daemon's CAVEMAN hook handler loop, and r5 F6 already makes that stderr write best-effort. The queue item's phrase "base loop handler" matches the daemon's loop, not anything in `bd-launch-role.sh`. The `except Exception` catches `BrokenPipeError`/`OSError`; if `sys.stderr` is `None`, `print` is a no-op. The guard is sufficient.

**Residual risks to check.**

- Other `print(..., file=sys.stderr)` calls in the daemon's main loop beyond the visible 12 KB.
- Any unguarded stderr write in `bd-launch-role.sh` itself, if a base loop exists there.

**Overlap.** If the fix really belongs in `bd-launch-role.sh`, it conflicts with BH1-18, 19, 20 and 31, all of which edit that file (BH1-31 also edits `bd-launch-codex-role.sh`). If it closes as DUP/RESOLVED, there is no code conflict.

**Gate scope.** Verification only. Run the ORDER-2331 r5 daemon tests covering the caveman hook and the simulated unwritable-stderr control. Only add launch-role tests if an equivalent unguarded write is found there.

---

### BH1-05 — role-prompt-trim-wave

**Location.** ~100 role-prompt files for `agy-*`, `cx-*`, `grok-*`, `kimi-*`, adjudicator, lensrouter, review-security (1240 tokens) and status (1072 tokens). Paths `UNVERIFIED`. Reference: `harness-work/O1789/landing/LANDED-integrator-C-eff-m5-trim-r3-1215Z.md`, which trimmed only the eff-m5 set.

**Root cause.** Content budget, not a code bug. The 800-token budget was enforced only on eff-m5 prompts, so other families drifted over. A per-prompt token measure is needed, and any trim must preserve launch-critical rules.

**Overlap.** No BH1 row touches role prompts by file. Soft dependency: BH1-19 and BH1-13 (launcher bootstrap / brief delivery) consume prompt contents via the launcher. Any row editing the token-count tool would conflict (`UNVERIFIED`).

**Gate scope.** The eff-m5 budget test widened to the new families, plus launcher bootstrap tests. Paired control: the same required-rule markers present before and after each trim. Split into per-family sub-cuts (agy / cx / grok / kimi / misc).

---

### BH1-06 — supply-guard-closed-markers (H, S, MED)

**Location.** `/home/mboyle/bd-persist/harness/bd-supply-guard.sh`, section "(c) starved DONE". Relevant functions: `newer_round`, `tier_state`, `router_hold`, `legs_declared`, `legs_named`. The owed-work decision loop that consumes them lies past the excerpt cut-off (`UNVERIFIED`). `L:UNVERIFIED`.

**Root cause.** The header defines a cut as OWED when: its `DONE.md` is older than `WAIT_MIN`; it has fewer `.review/VERDICT-*.md` files newer than `DONE.md` than the legs its `TIER.md` declares, none being REFUTE or BOUNCE; and it has no lens claim, no hold, and no newer round. The visible skip checks are: claim held; effective HOLD via `tier_state`; router `HOLD-*.md` via `router_hold`; newer round via `newer_round`.

**None of these reads `CLOSED-*` or `LANDED-*` markers.** A cut that has landed or been closed by the integrator still appears to have missing VERDICT legs, so it is re-flagged OWED on every cron tick. The 30-minute cadence in the row text matches `NAG_MIN`.

**Fix shape.** Add a skip check treating `CLOSED-*` or `LANDED-*` as terminal. Open questions (`UNVERIFIED`): where the markers live (cut dir vs `harness-work/O1789/landing/`); how they name the cut; whether the `$CUTS` scan depth (`-mindepth 2 -maxdepth 3`) reaches them.

**Overlap.** None found in the visible table. BH1-12 (`bd-pm-idle-sweep`) is a neighbouring PM-noise concern in a different file. BH1-17 to BH1-32 were truncated, so overlap there cannot be ruled out.

**Gate scope.** `bd-supply-guard` tests only. Fixtures: DONE cut with `LANDED-*` (no OWED); DONE cut with `CLOSED-*` (no OWED); unmarked cut (still OWED); marker on a different cut (no leak). Run with `BD_SUPPLY_DRY=1` and temp `BD_SUPPLY_ROOT`, `BD_SUPPLY_BURN`, `BD_SUPPLY_CLAIM`.

---

### BH1-07 — pool-usage-refresher-residuals (H, S, LOW)

**Location.** `/home/mboyle/bin/bd-pool-usage-refresher.py`, `poll_claude_oauth_caches()`. The REUSED branch reads `cached_entry.get("fetchedAtMs", 0)` into `prev_ms`. `L:UNVERIFIED`. The A2 knob parser is not in the excerpt.

**Root cause.**

- **A1 — seed guard.** The REUSED check is `isinstance(prev_ms, (int, float)) and (now_ms - prev_ms) < 720_000 and isinstance(cached_entry.get("utilization"), dict)`. A non-numeric `fetchedAtMs` fails `isinstance` and falls through silently, so a corrupted cache reads as "no reading" instead of as an error. The matching `retryAfterUntilMs` guard behaves the same way. Remedy: reject or flag non-numeric values explicitly. Whether this is exactly the "seed guard" is `UNVERIFIED`.
- **A2 — comma form.** The `BD_*` knob parser would read `"A,B"` as nothing, and the form is undocumented. Document or accept. `UNVERIFIED`.
- **STEP0.** Confirm the 403/429 cause is gone after the O3394 full logins. The 429 `Retry-After` handler is visible only to its start; this is a live check.

**Overlap.** BH1-17 lists `~/bin/bd-pool-usage-refresher.py` (with `bd-kimi-usage-watch.sh`). This is the main conflict. BH1-17 STEP0 notes that the `~/bin` copy has drifted from the tracked copy; **resolve drift first**, or BH1-07 is built on the wrong base. BH1-03 is adjacent.

**Gate scope.** Refresher unit tests (uopa r2 and refresher-n1 suites). Cases: string, `None`, negative `fetchedAtMs`; knob comma form. No live API calls; redirect `BD_PERSIST` to a temp dir.

---

### BH1-08 — telemetry-hung-sensor (H, S, LOW)

**Location.** Telemetry-registry code (`telemetry-registry-live/r2`), per review artifact `VERDICT-correctness-TRL2C-C-trl-r2.md`. File and function `UNVERIFIED`; the sensor-result cache/serve path is the likely site.

**Root cause.**

- **A3.** A hung sensor with a cached result re-serves the stale reading. Staleness is the only signal; there is no in-flight state. Fix: track started-at / in-flight and prefer UNKNOWN over the cached value when a run is outstanding.
- **A2.** `nolock` and `nosnap` mutants survive. Benign test debt; add killing tests.

**Overlap.** None by file in BH1-06..16. BH1-29 touches `tests/test_bdt_sensor_gitstate*.py`, which may be the same sensor test family.

**Gate scope.** Telemetry-registry test module. Add hung-sensor-with-cache → UNKNOWN; add nolock/nosnap mutation controls.

---

### BH1-09 — worker-claim-lost-remote (H, M, MED)

**Location.** Evidence: `harness-work/O1789/dispatch-F1-D/ESC-PM-TRLN1-A-unreachable.md` (worker TRLN1-A, host .186, 17:45Z). The reaper path is `UNVERIFIED`. Candidates: registry reaper near `reg.tsv` (BH1-10's `reg.tsv.reap-refused` places it there), or `bd-claim-object.sh`'s `_reap` / `census_or_refuse` / `census_of`.

**Root cause (hypothesis).** A live remote worker's registry claim was dropped. The reaper likely treats a remote claim as dead because it cannot confirm liveness across hosts: either a local-only PID/liveness check or a TTL expiry. Once reaped, the worker became unreachable by `bd-say` and retire required a seat-written handoff. Fix direction: make the reap decision **fail closed for remote hosts** — never reap what you cannot measure.

**Overlap.** BH1-10 and BH1-11 share the reaper/registry (`reg.tsv.reap-refused`, `wipe_alert`). Possibly BH1-02 if the path is in `bd-claim-object.sh`.

**Gate scope.** Registry and reaper tests including rwg-hardening. Add a live-remote-claim-must-not-be-reaped test with a simulated remote host and temp registry.

---

### BH1-10 — rwgh-fifo-test-leak (H, S, MED)

**Location.** The rwg-hardening FIFO-stamp tests that create `<tmp>/reg.tsv.reap-refused` as a FIFO. File `UNVERIFIED`. Evidence: `state/BUGHUNT-WAVE1-QUEUE.md`, 19:2xZ.

**Root cause.** The test creates a FIFO, then the code under test (or the test) runs `head -c 32 <tmp>/reg.tsv.reap-refused`. With no writer attached, `open()` on a FIFO blocks forever. The test neither times out nor kills the reader on cleanup. Result: 3 orphan `head` readers on .149 for 4–5 hours.

**Fix shape, in order.**

1. Wrap the reader in `timeout`, or open `O_NONBLOCK`.
2. Add a cleanup `trap` that kills the reader.
3. Open a writer end to release it, then remove the FIFO.

**Overlap.** BH1-09, BH1-11 (same area). BH1-21 has a FIFO-hang advisory (IG4S-B ADV-1) in `cplus_authorizer.py` — same bug class, different file, no conflict.

**Gate scope.** rwg-hardening FIFO tests only. Post-run: `pgrep -f 'reg.tsv.reap-refused'` must return nothing. Run once on a band host (.149).

---

## 2. File-conflict groups

| Group | Rows | Shared file | Serialize? | Notes |
|---|---|---|---|---|
| **Say hook** | BH1-01, BH1-15 | PreToolUse bd-say hook (`UNVERIFIED`) | Yes | One cut or strict sequence |
| **Claim object** | BH1-02, possibly BH1-09 | `bd-claim-object.sh` | Yes if BH1-09 lands here | Confirm reaper location first |
| **Usage / accounting** | BH1-03, BH1-24 | usage CSV writer / `bd_cost_accounting.py` (`UNVERIFIED`) | Yes | Agree PARTIAL label wording once |
| **Refresher** | BH1-07, BH1-17 | `~/bin/bd-pool-usage-refresher.py` | Yes | Resolve `~/bin` vs tracked drift before either |
| **Launcher (conditional)** | BH1-04, 18, 19, 20, 31 | `bd-launch-role.sh` | Only if BH1-04 survives STEP0 | Expected: BH1-04 closes DUP |
| **Reaper / registry** | BH1-09, BH1-10, BH1-11 | reaper + `reg.tsv*` (`UNVERIFIED`) | Yes | Dispatch as one wave |
| **Telemetry sensor** | BH1-08, possibly BH1-29 | telemetry-registry tests | Check | BH1-29 may share test module |
| **Supply guard** | BH1-06 | `bd-supply-guard.sh` | No | Isolated |
| **Prompts** | BH1-05 | role-prompt files | No | Isolated; split per family |

---

## 3. Recommended dispatch order

1. **BH1-04 STEP0** — cheapest; most likely closes DUP/RESOLVED and dissolves the launcher conflict.
2. **BH1-02** — most concrete defect, highest operational cost (27-min D-state). Needs one read of the `closed()` body and `take` lock placement first.
3. **BH1-06** — isolated file, clear fix, kills recurring PM noise.
4. **BH1-10** — small, isolated to tests, stops orphan readers.
5. **BH1-07 + BH1-17** together, after drift resolution.
6. **BH1-03 + BH1-24** together.
7. **BH1-01 + BH1-15** together.
8. **BH1-09 + BH1-11** after BH1-10, once the reaper file is located.
9. **BH1-08**, then **BH1-05** per-family sub-cuts.

---

## 4. Open items blocking exact citations

`bd-pm-B` needs these before any cut can carry a real `file:line`:

- `grep -n 'closed()' bd-claim-object.sh` and the `take` body past the header comment.
- The PreToolUse bd-say hook path and `bd-say.sh` re-check function.
- The usage CSV writer identity.
- The tail of `bd-supply-guard.sh` past `legs_named`.
- The tail of `bd-pool-usage-refresher.py` past the 429 branch, plus the knob parser.
- The reaper file for `reg.tsv.reap-refused` and the rwg-hardening test file.
- The telemetry-registry sensor cache module.

Everything above that is marked `UNVERIFIED` is a recommendation to confirm, not a checked fact. No code was read beyond the specialist excerpts; no code was changed.

---

# Appendix: Specialist Subagent Read-Only Code Inspection Reports

## Investigator Report 1: BH1-01 to BH1-05 Code & Blame Inspector (`claude-sonnet-5-5-high`)

# Dossier: BH1-01 to BH1-05 (read-only, no tools called)

**Limits of the source context:**
- It is truncated. Only `bd-claim-object.sh` (first 12 KB) and `bd-telemetry-daemon.py` (first 12 KB) are visible.
- It has no line numbers, and none of the row-level evidence notes or briefs are included.
- Where I couldn't verify something, I mark it **UNVERIFIED**. Every `BD_GATE_SCOPE` below is a recommendation to confirm against the real test tree, not a checked path.
- Conflict groups use only the rows in the supplied table (BH1-01 to BH1-32, some cut off).

---

## BH1-01: say-gate-preexisting

**1. Files and lines**
- The hook script and `bd-say.sh` are **not in the context**. Path and line numbers are UNVERIFIED.
- The only pointer is the evidence note: `harness-work/O1789/dispatch-E3-D/cuts/sayhook-dollar/rc-SHD2C-C-r2/NOTE-pm-preexisting-say-gate.md`.
- The target is the PreToolUse say-gate hook, plus the runtime re-check in `bd-say.sh` that mitigates it.

**2. Root cause (from the row text only)**
- The gate parses the Bash command text statically. It has three gaps:
  - **A1:** a nested `bash -c '$MSG'` is not unwrapped, so the message is opaque to the gate.
  - **A2:** escaped redirect words (`\<`, `\>`) are mis-tokenised.
  - **A3:** the text the gate inspects may not match the text actually sent.
- All three are pre-existing and mitigated because `bd-say.sh` re-validates at runtime.
- Exact functions: UNVERIFIED.

**3. Overlap**
- **BH1-15** (say-hook-path-precheck) is the same PreToolUse bd-say hook, so the two likely edit the same file. Treat them as one conflict group and serialize them.
- BH1-31 has a loose thematic link (bd-say `mailbox_for`) but touches different files.

**4. `BD_GATE_SCOPE`**
- The say-hook and bd-say test suites, i.e. the sayhook-dollar cut's tests (names UNVERIFIED).
- Add paired controls for `bash -c '$MSG'`, `\<`/`\>`, and the `bd-say.sh` runtime re-check.
- Run both BH1-01 and BH1-15 cases together if they share a file.

---

## BH1-02: claim-object-homewide-find

**1. Files and lines**
- File: `/home/mboyle/bd-persist/harness/bd-claim-object.sh`.
- The `closed()` function is the likely site. The visible text cuts off in its header comment, so the function body and line numbers are UNVERIFIED.
- Evidence note: `IMPROVEMENTS/bd-pm-B-20261019-claim-object-homewide-find.md`.

**2. Root cause**
- The comment block "H2xx -- closed() resolved ONE root while bd-train-carries resolves two…" widens `closed()` from one root to three roots, then to a measured list: "Measured… over EVERY directory under /home/mboyle, not a named few: 15 roots hold…".
- The comment is cut off before the code. Taken with the row text, the best inference is that `closed()` ends up running a find across `/home/mboyle`.
- That find goes through NFS, which explains the 27-minute D-state.
- Visible facts that bear on blast radius:
  - `take` calls `writer_lock`/`lock()`. `BD_CLAIM_LOCK_WAIT` defaults to 30 s, and the H340 comment says the locked section is "now state()+append only".
  - So if `closed()` runs inside the lock, other claimers hit rc=2 "could not lock". If it runs outside, only the caller hangs. UNVERIFIED which.
- `carried_by_train` is not the culprit. It is cached for 600 s via `BD_CARRIED_TTL`, and it calls `bd-train-carries`, which takes about 20 s.
- The fix the row asks for is to bound the find to the cut roots: `$CUTS` (`BD_CUTS`), the `bd-review-wt` dir, and the `bd-cuts` top level. It should also add a timeout and fail with `unknown(...)`/COULD NOT LOOK rather than blocking.

**3. Overlap**
- **BH1-09** (worker-claim-lost-remote) may touch the same file. The reaper path (`_reap`, `census_or_refuse`, `census_of`) is in this file, but BH1-09 may concern the seat registry instead. UNVERIFIED.
- No other BH1-01..05 row touches it.

**4. `BD_GATE_SCOPE`**
- The bd-claim-object tests (e.g. a `tests/*claim_object*` glob, UNVERIFIED).
- Include a timing/bounded-find control. Include a closed-cut control for each of the 15-root population cases, so the narrowing doesn't change claim outcomes.
- Keep the `take` rc contract: rc=3 held, rc=4 closed, rc=5 carried, rc=6 could-not-measure.

---

## BH1-03: usage-csv-partial

**1. Files and lines**
- The CSV writer is **not in the context**, so path and lines are UNVERIFIED.
- Evidence note: `IMPROVEMENTS/bd-pm-B-20261019-usage-csv-partial.md`.
- Likely candidates, by inference only: the usage collector/census tool that emits the CSV, or `bd_cost_accounting.py`. Neither is verified.

**2. Root cause (from the row text only)**
- When a census is incomplete, the CSV carries no column or marker for that. A consumer cannot tell a complete zero from a partial count, which is a rule-6 "COULD NOT LOOK" violation.
- The fix is a PARTIAL marker, either a column or a trailer line.

**3. Overlap**
- **BH1-24** (t16-cost-accounting-coverage) is a likely overlap. It lists "label PARTIAL → COULD NOT LOOK", `synthetic_zero_skipped`, and `bd_cost_accounting.py`. Resolve the label wording across both.
- **BH1-07** and **BH1-17** are usage-refresher/pool-state rows. They are adjacent, not necessarily the same file.

**4. `BD_GATE_SCOPE`**
- The usage-collector tests, e.g. `tests/test_usage_collector_advisor.py`, which BH1-24 names.
- Add a control for a complete census (no marker) and one for a partial census (marker present).
- Check the CSV header stays backward-compatible for existing parsers.

---

## BH1-04: launch-base-loop-stderr

**1. Files and lines**
- The row names `bd-launch-role.sh`'s base-loop handler. That file is **not in the context**.
- The visible ORDER-2331 r5 F6 fix is in **`/home/mboyle/bd-persist/harness/bd-telemetry-daemon.py`**:
  - `_hook_log(line)` wraps `print(..., file=sys.stderr, flush=True)` in `try/except Exception: pass`. Its docstring says an unwritable stderr (EPIPE/ENOSPC) must not drop the record or kill the loop.
  - `_caveman_one` and `caveman_minify` ("raises to the boundary in `caveman_minify` (ORDER-2331 r5), which keeps the field raw") use it.
- Line numbers: UNVERIFIED.

**2. Root cause**
- This is **STEP0**, and the likely outcome is "already fixed or mis-attributed". The ORDER-2331 advisory concerns the daemon's CAVEMAN hook handler loop. r5 F6 already makes its stderr write best-effort.
- The row's title and file may be wrong: `bd-launch-role.sh` is probably a mis-attribution. The queue item says "base loop handler", which matches the daemon's loop.
- Residual risks to check:
  - Other `print(..., file=sys.stderr)` calls in the daemon's main loop. They are past the visible 12 KB and UNVERIFIED.
  - Any stderr write in `bd-launch-role.sh` itself, if a base loop exists there.
- The `except Exception` catches `BrokenPipeError`/`OSError` and is sufficient. If `sys.stderr` is `None`, `print` is a no-op.

**3. Overlap**
- If the fix is really in `bd-launch-role.sh`, it conflicts with **BH1-18, 19, 20, 31**, which all edit that file. BH1-31 also edits `bd-launch-codex-role.sh`.
- If it is already fixed in the daemon, this row has no code conflict and can close as DUP/RESOLVED once verified.

**4. `BD_GATE_SCOPE`**
- STEP0 is verification only. Run the ORDER-2331 r5 tests, i.e. the `bd-telemetry-daemon` tests covering the caveman hook and unwritable stderr (names UNVERIFIED).
- Run the simulated unwritable-stderr control against the daemon.
- Only if `bd-launch-role.sh` has an equivalent unguarded stderr write, add a scope for the launch-role tests.

---

## BH1-05: role-prompt-trim-wave

**1. Files and lines**
- The ~100 prompt files are **not in the context**, so paths are UNVERIFIED.
- They are role prompts for `agy-*`, `cx-*`, `grok-*`, `kimi-*`, adjudicator, lensrouter, review-security (1240 tokens) and status (1072 tokens).
- Reference: `harness-work/O1789/landing/LANDED-integrator-C-eff-m5-trim-r3-1215Z.md`. That cut trimmed the eff-m5 set only.

**2. Root cause**
- A content-budget problem rather than a code bug.
- The 800-token budget is enforced only on the eff-m5 prompt set, so the other families drifted over it.
- A per-prompt token measure is needed. Any trim must preserve the rules the prompt carries, which are launch-critical.

**3. Overlap**
- By file, none of BH1-01..32 appears to touch role prompts.
- Soft risks:
  - **BH1-19** and **BH1-13** (launcher bootstrap/brief delivery) depend on prompt contents, but only through the launcher.
  - Any row that edits the role-prompt token-count tool would conflict. UNVERIFIED.

**4. `BD_GATE_SCOPE`**
- The eff-m5 prompt-budget test, widened to the new families, plus the launcher bootstrap tests.
- A trim is a behavior risk, so add a paired control: the same required-rule markers present before and after the trim.
- Split into per-family sub-cuts (agy/cx/grok/kimi/misc) so each gate stays small.

---

## Conflict-group summary

| Group | Rows | Shared file |
|---|---|---|
| Say hook | BH1-01, BH1-15 | PreToolUse bd-say hook (UNVERIFIED) |
| Launcher (only if BH1-04 is real) | BH1-04, 18, 19, 20, 31 | `bd-launch-role.sh` |
| Claim object | BH1-02, possibly BH1-09 | `bd-claim-object.sh` |
| Usage/accounting | BH1-03, BH1-24 | usage collector / `bd_cost_accounting.py` (UNVERIFIED) |
| Prompts | BH1-05 | none known |

## Recommended next steps
1. **BH1-04:** run STEP0 first. It will most likely close as already fixed or mis-attributed.
2. **BH1-02:** the most concrete code defect. Fetch the `closed()` body past the truncation point.
3. **BH1-01, 03, 05:** the source files are missing from the context. Re-run with the hook script, the CSV writer and a prompt-budget listing before assigning verification scopes.

## Investigator Report 2: BH1-06 to BH1-10 Code & Blame Inspector (`claude-sonnet-5-5-low`)

# Read-only dossier: BH1-06 to BH1-10

Source: the supplied `<SOURCE_CONTEXT>` only. No tools were called.

**Limits of the source.** It has two gaps:
- The `bd-supply-guard.sh` excerpt stops mid-script, at `legs_named`, after about 10 KB.
- The `bd-pool-usage-refresher.py` excerpt stops mid-`except` in the 429 branch.

Where a mechanism depends on code that was cut off, I mark it **UNVERIFIED**. No source was provided for BH1-08, BH1-09 or BH1-10, so those rows rest only on the row text. I give no line numbers, because the excerpts carry none. Locations are by function name.

---

## BH1-06 supply-guard-closed-markers (H, S, MED noise)

**1. Location**
- `/home/mboyle/bd-persist/harness/bd-supply-guard.sh`, section "(c) starved DONE".
- The relevant functions are `newer_round`, `tier_state`, `router_hold`, `legs_declared` and `legs_named`.
- The owed-work decision loop that consumes them lies after the cut-off. It is **UNVERIFIED**.

**2. Root cause**
- The header defines a cut as owed when all of these hold:
  - its `DONE.md` is older than `WAIT_MIN`;
  - it has fewer `.review/VERDICT-*.md` files newer than `DONE.md` than the legs its `TIER.md` declares, and none of them is a REFUTE or BOUNCE;
  - it has no lens claim, no hold, and no newer round (`newer_round`).
- The skip checks visible in the excerpt are:
  - claim held;
  - effective HOLD, via `tier_state`;
  - router `HOLD-*.md`, via `router_hold`;
  - newer round, via `newer_round`.
- None of these reads `CLOSED-*` or `LANDED-*` markers. A cut that has landed, or been closed by the integrator, still shows missing VERDICT legs. It is therefore re-flagged OWED on every cron run, which matches the row's "false OWED all day, every 30 min". 30 minutes is `NAG_MIN`.
- The fix is a skip check that treats a `CLOSED-*` or `LANDED-*` marker as terminal.
- Open points, all **UNVERIFIED**:
  - where those markers live (the cut dir, or `harness-work/O1789/landing/`);
  - how they name the cut;
  - whether the `$CUTS` scan depth, `-mindepth 2 -maxdepth 3`, reaches them.

**3. Overlap**
- No other BH1 row in the supplied table lists `bd-supply-guard.sh`, so I found none.
- BH1-12 (`bd-pm-idle-sweep`) is a neighbouring PM-noise concern but a different file.
- BH1-17 to BH1-32 were truncated, so I cannot rule out overlap there.

**4. `BD_GATE_SCOPE` recommendation**
- Scope: `bd-supply-guard` tests only.
- Add fixtures for a DONE cut with a `LANDED-*` marker and with a `CLOSED-*` marker. Both should yield no OWED.
- Add controls for an unmarked cut (still OWED) and a marker on a different cut (no leak).
- Run with `BD_SUPPLY_DRY=1` and temp `BD_SUPPLY_ROOT`, `BD_SUPPLY_BURN` and `BD_SUPPLY_CLAIM` overrides, so no live state is touched.

---

## BH1-07 pool-usage-refresher-residuals (H, S, LOW)

**1. Location**
- `/home/mboyle/bin/bd-pool-usage-refresher.py`, in `poll_claude_oauth_caches()`.
- The REUSED branch reads `cached_entry.get("fetchedAtMs", 0)` into `prev_ms`.
- The A2 knob-parsing code is not in the excerpt. It is **UNVERIFIED**.

**2. Root cause**
- **A1, seed guard.**
  - The REUSED check is `isinstance(prev_ms, (int, float)) and (now_ms - prev_ms) < 720_000 and isinstance(cached_entry.get("utilization"), dict)`.
  - A non-numeric `fetchedAtMs` fails the `isinstance` test, so the code falls through.
  - Per the row, the seed guard keys on `fetched_ms` and a non-number passes as "no reading".
  - The matching `retry_until` guard is similar: a non-number `retryAfterUntilMs` is silently ignored.
  - The remedy is to reject or flag a non-numeric value explicitly.
  - Whether the "seed guard" is exactly this code is **UNVERIFIED**, because the excerpt does not show the seeding.
- **A2, comma form.** The `BD_*` knob parser would read `"A,B"` as nothing, and the form is undocumented. Document it or accept it. This is **UNVERIFIED**.
- **STEP0.** Confirm the A 403/429 cause is gone after the O3394 full logins. The code that handles 429 `Retry-After` is visible only up to its start, so this is a live check, not a code check.

**3. Overlap**
- BH1-17 lists `~/bin/bd-pool-usage-refresher.py`, with `bd-kimi-usage-watch.sh`, as its file. This is the main conflict.
- BH1-03 (usage CSV) is also in the usage area. Its file is unknown and not in the excerpt.
- Sequence BH1-07 and BH1-17 together or serially.
- BH1-17 STEP0 notes the `~/bin` copy has drifted from the tracked copy. Resolve that drift first, or BH1-07 may be built on the wrong base.

**4. `BD_GATE_SCOPE` recommendation**
- Scope: refresher unit tests, namely the uopa r2 and refresher-n1 suites.
- Add cases for a string, `None` or negative `fetchedAtMs`, and for the knob comma form.
- No live API calls. Redirect `BD_PERSIST` to a temp dir.

---

## BH1-08 telemetry-hung-sensor (H, S, LOW)

**1. Location**
- The row names the telemetry-registry code, `telemetry-registry-live/r2`, as the review artifact `VERDICT-correctness-TRL2C-C-trl-r2.md`.
- No source was supplied, so the file and function are **UNVERIFIED**. The sensor-result cache and serve path is the likely place.

**2. Root cause**
- **A3.** A hung sensor that has a cached result re-serves the old reading. It should report "still running", meaning UNKNOWN. Staleness is currently the only signal. The fix is to track an in-flight or started-at state and prefer UNKNOWN over the cached value when a run is outstanding.
- **A2.** The nolock and nosnap mutants survive. This is benign test debt, so add tests that kill them.
- The mechanism is inferred from the row text only.

**3. Overlap**
- None of BH1-06 to BH1-16 lists a telemetry file, so I found none.
- BH1-29 touches `tests/test_bdt_sensor_gitstate*.py`, which may relate to the same sensor tests. This needs checking.

**4. `BD_GATE_SCOPE` recommendation**
- Scope: the telemetry-registry test module.
- Add a hung-sensor-with-cache test that expects UNKNOWN.
- Add nolock and nosnap mutation controls.

---

## BH1-09 worker-claim-lost-remote (H, M, MED)

**1. Location**
- The evidence is `harness-work/O1789/dispatch-F1-D/ESC-PM-TRLN1-A-unreachable.md`.
- It records worker TRLN1-A on host .186 at 17:45Z.
- The reaper path is not in the supplied source, so it is **UNVERIFIED**.
- Candidates are the registry reaper and the claim-registry code. BH1-10 names `reg.tsv.reap-refused` and "rwg-hardening", so the reaper lives near the registry `reg.tsv`.

**2. Root cause**
- A live remote worker's registry claim was dropped.
- The worker became unreachable by `bd-say`, and retire then needs a seat-written handoff.
- A reaper probably treats a remote claim as dead because it cannot confirm liveness remotely. Candidates are a local-only liveness check and a TTL expiry. This hypothesis is unconfirmed.
- The task is to find the path that drops live remote claims and make it fail closed for remote hosts.

**3. Overlap**
- BH1-10 and BH1-11 touch the same reaper and registry, via `reg.tsv.reap-refused` and `wipe_alert`.
- BH1-11 is in the table but is outside this assignment.
- Sequence these three together.

**4. `BD_GATE_SCOPE` recommendation**
- Scope: the registry and reaper tests, including the rwg-hardening suite.
- Add a test with a live remote claim that must not be reaped.
- A remote-host simulation is needed. Use a temp registry only.

---

## BH1-10 rwgh-fifo-test-leak (H, S, MED)

**1. Location**
- The rwg-hardening FIFO-stamp tests, in the test file(s) that create `<tmp>/reg.tsv.reap-refused` as a FIFO.
- The exact file and function are **UNVERIFIED**, because no source was provided.
- Evidence: `state/BUGHUNT-WAVE1-QUEUE.md`, 19:2xZ.

**2. Root cause**
- The tests make a FIFO at `reg.tsv.reap-refused`.
- The code under test, or the test itself, opens it with a reader, `head -c 32 <tmp>/reg.tsv.reap-refused`.
- When no writer ever attaches, `head` blocks indefinitely on the open or read.
- The test neither times out nor kills the reader on cleanup.
- Result: 3 orphan readers on .149 for 4 to 5 hours.
- Fix, in this order:
  1. Wrap the reader in `timeout`, or open it `O_NONBLOCK`.
  2. Add a cleanup `trap` that kills the reader.
  3. Open a writer end to release it, then remove the FIFO.

**3. Overlap**
- BH1-09 and BH1-11 (same reaper and registry area).
- BH1-21 has a FIFO-hang advisory (IG4S-B ADV-1), but in a different file, `cplus_authorizer.py`. It is a similar class of issue and not a file conflict.

**4. `BD_GATE_SCOPE` recommendation**
- Scope: the rwg-hardening FIFO tests only.
- After the run, check that no `head -c 32` process remains.
- Check for leaked processes with `pgrep -f 'reg.tsv.reap-refused'`.
- Run it once on a band host, e.g. .149.

---

## Cross-row summary

| Row | File(s) | Confidence | Conflicts |
|---|---|---|---|
| BH1-06 | `bd-supply-guard.sh` | Medium | none seen |
| BH1-07 | `bin/bd-pool-usage-refresher.py` | Medium for A1, low for A2 | BH1-17, BH1-03 |
| BH1-08 | telemetry-registry (unknown) | Low | BH1-29 (possible) |
| BH1-09 | reaper/registry (unknown) | Low | BH1-10, BH1-11 |
| BH1-10 | rwg-hardening tests (unknown) | Low | BH1-09, BH1-11 |

A follow-up pass with the real sources is needed to pin file, line and function for BH1-08, BH1-09 and BH1-10. It should also finish the cut-off tails of the refresher and the supply guard.
