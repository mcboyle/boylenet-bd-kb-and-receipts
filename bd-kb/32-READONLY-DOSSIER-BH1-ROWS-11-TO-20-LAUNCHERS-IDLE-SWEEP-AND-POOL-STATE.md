# BD-KB Volume 32: Read-Only Investigation Dossier — BH1-11 through BH1-20 (Wipe-Alert Lock, Idle Sweep, Launchers & Pool State)
> **Lead Investigator**: `Fable5-Investigator-2` (`claude-fable-5-1 (.claude-b)`)
> **Subagent Squad**: 2x Parallel `AGY claude+gpt` (`claude-sonnet-5-5-high` & `claude-sonnet-5-5-low`) Read-Only Code Investigators
> **Generated At**: `2026-10-19T23:33:57Z`
> **Scope**: Strictly Read-Only Investigation Dossier for `bd-pm-B` (`Zero Code Changes`, `O3416` Compliant)

---

# BD-KB Volume 32: Read-Only Investigation Dossier — BH1-11 through BH1-20

**Scope:** Wipe-Alert Lock, Idle Sweep, Launchers & Pool State
**Author:** `Fable5-Investigator-2` (Lead PI), synthesizing specialist reports S1 (BH1-11–15) and S2 (BH1-16–20)
**Consumer:** `bd-pm-B`
**Mode:** 100% read-only. No tools were called and no commands were run by the lead or either specialist. Every claim below derives from the specialist reports, which in turn derive only from the provided `<SOURCE_CONTEXT>`.

---

## 0. Evidence provenance and confidence legend

The source context was truncated. The table below records exactly what was and was not visible, because it bounds every citation in this dossier.

| Artifact | Visibility | Consequence |
|---|---|---|
| `/home/mboyle/.local/bin/bd-pm-idle-sweep` | Full file (19 lines) | BH1-12 has exact line citations |
| `bd-launch-role.sh` | First ~14,000 bytes only (header, `model_for`, start of `effort_for`) | No line numbers for BH1-13, 18, 19, 20 |
| `bd-launch-kimi.sh` | First ~8,000 bytes only (header, exit-code map) | No line numbers for BH1-14 |
| `bd-launch-codex-role.sh` | First ~10,000 bytes only | Exit-code contract visible; no LAUNCHED/RESULT emission site |
| `bd-role-claim.sh` (`wipe_alert`) | Not provided | No line numbers for BH1-11 |
| `lib_remote_seat.sh` | Not provided | BH1-18/20 mechanics unverified |
| Say-gate PreToolUse hook | Not provided, not confidently named | BH1-15 file name unknown |
| `threat_gate.py` | **[FILE ABSENT]** in context | BH1-16 cannot be code-verified |
| `bd-pool-usage-refresher.py`, `bd-kimi-usage-watch.sh` | Not provided | BH1-17 unverified |
| dcc-f9 rulings r6/r7, PRECHECK-RWGHP-C, ORDER-pm-B-accelerate-1740Z | Not provided | Evidence files cited by row text only |

**Confidence markers used throughout:**

- **[READ]** — mechanism read directly from visible code.
- **[INFERRED]** — mechanism derived from the row text or header documentation, not verified against code.
- **[UNKNOWN]** — not determinable from the context; requires a STEP0 read before assignment.

`BD_GATE_SCOPE` values are proposals. The scope vocabulary was not in the context.

---

## 1. Executive summary table

| Row | Slug | Sev/Size/Risk | Primary file(s) | Line cites | Root-cause confidence | Hard conflicts |
|---|---|---|---|---|---|---|
| BH1-11 | `rwgh-wipe-alert-lock` | H / S / LOW | `bd-role-claim.sh` (`wipe_alert`), `tests/test_registry_wipe_guard.py` | none | [INFERRED] | BH1-35 |
| BH1-12 | `idle-sweep-activity-signal` | H / S / MED | `~/.local/bin/bd-pm-idle-sweep` | L5, L7, L8, L9, L15, L16 | [READ] | none |
| BH1-13 | `launcher-brief-not-delivered` | H / S / MED | `bd-launch-role.sh`, likely `bd-launch-codex-role.sh` | none | [INFERRED] | BH1-04, 18, 19, 20, 31, 38 |
| BH1-14 | `kimi-builder-launch` | H / S / LOW | `bd-launch-kimi.sh` | none | [UNKNOWN] | BH1-17 (causal) |
| BH1-15 | `say-hook-path-precheck` | H / S / LOW | say-gate PreToolUse hook, `/home/mboyle/bd-say.sh` | none | [INFERRED] (row states mechanism) | BH1-01 |
| BH1-16 | `dcc-f9-known-limits` | — | `jarvisos_backend/threat_gate.py` **[FILE ABSENT]** | none | [INFERRED] | none |
| BH1-17 | `pool-state-writer-integrity` | — | `~/bin/bd-pool-usage-refresher.py`, `bd-kimi-usage-watch.sh` | none | [INFERRED] | BH1-07 |
| BH1-18 | `launcher-result-line-consistency` | — | `bd-launch-role.sh`, `lib_remote_seat.sh` | none | [INFERRED] | BH1-13, 19, 20, 31, (04, 37, 38) |
| BH1-19 | `launcher-token-exposure` | — | `bd-launch-role.sh` | none | [INFERRED] | BH1-13, 18, 20, 31 |
| BH1-20 | `lvc-cplus-routing-advisories` | — | `bd-launch-role.sh`, `lib_remote_seat.sh` | none | [INFERRED] (advisories are explicit) | BH1-18, 19 |

Only one row in this range (BH1-12) has a root cause read from code. The rest need a STEP0 code read before a builder is dispatched.

---

## 2. Row-by-row dossier

### BH1-11 — `rwgh-wipe-alert-lock` (H, S, LOW)

**Files and functions**

- Estimated file: `bd-role-claim.sh`, function `wipe_alert`. The estimate comes from the BH1-35 "Files (est.)" column, not from a direct read.
- Tests: `tests/test_registry_wipe_guard.py`, same provenance.
- Evidence: `dispatch-E3-D/cuts/rwg-hardening/r1/PRECHECK-RWGHP-C.md`, finding A1. Not seen.
- Line numbers: none available.

**Root-cause mechanism [INFERRED]**

`wipe_alert` performs a read-then-write on a dedupe stamp with no mutual exclusion:

1. Read the stamp. Observe "no recent alert".
2. Fire the alert.
3. Write the stamp.

Two reaps running concurrently can both pass step 1 before either reaches step 3. The observable outcomes are duplicate alerts and a last-writer-wins stamp. The BH1-35 text confirms this is the same code path: it explicitly says "Unlocked check-then-stamp is excluded (= BH1-11)".

**Candidate fixes**

- `flock` on a sidecar lockfile around the check-and-stamp pair.
- An atomic primitive for the stamp itself: `mkdir` (fails if exists) or `set -C` noclobber redirect. These make the check and the write one operation, so no lock is needed.

**Conflict and overlap**

- **BH1-35 (hard, same file and same test file).** BH1-35 carved BH1-11 out of its scope. If both land in separate cuts, the SEC A1/A2 edits in BH1-35 and the lock edit in BH1-11 will collide inside `wipe_alert`. Land BH1-11 first, or in the same cut.
- **BH1-10 (test-file overlap).** Shares the rwg-hardening test family, specifically the `reg.tsv.reap-refused` FIFO tests. Likely touches `tests/test_registry_wipe_guard.py`.
- **BH1-36 (semantic, not file).** Touches `tests/lib_live_registry_guard.sh`; its A2 item concerns `wipe_alert`. Probable semantic overlap only.

**Recommended `BD_GATE_SCOPE`**

Harness-only. Run the registry-wipe-guard and rwg-hardening suites. Add a concurrent-reap test: launch two reaps in parallel and assert exactly one alert. The test must include a lock-removal mutant to prove it can fail. No BD band.

---

### BH1-12 — `idle-sweep-activity-signal` (H, S, MED)

**Files and lines [READ]**

File: `/home/mboyle/.local/bin/bd-pm-idle-sweep` (19 lines, fully visible).

| Line | Content | Role in defect |
|---|---|---|
| 5 | `IDLE_MIN` default 45 | Threshold in minutes |
| 7 | `tmux … list-panes -a -F "#{session_name}\|#{window_activity}\|#{pane_current_command}\|#{pane_pid}"` (inside the `scan='…'` string) | Source of the wrong signal |
| 8 | `idle=$(( (now-act)/60 ))` | Faulty age computation |
| 8–9 | `kids=$(pgrep -P "$pid" …)` and `BARE`/`SEAT` classification | Counts only direct children of the pane pid |
| 15 | `sort -u -t'\|' -k2,2` | Secondary defect: dedupes by session name across sockets, keeps an arbitrary row |
| 16 | `[ "$idle" -ge "$IDLE_MIN" ] \|\| continue` | Filter that never trips |

**Root-cause mechanism [READ]**

`#{window_activity}` is tmux's timestamp of the last output to the window. A Claude TUI repaints its spinner, status line and cursor continuously, so tmux resets activity on every repaint. A seat parked at its prompt therefore always reads as active. Line 8 computes a near-zero age, and line 16 never lets a seat through. This matches the evidence: 66 of 66 seats read under 30 minutes, and the 41 idle seats were reaped only by manual verification.

The signal is the wrong proxy for "agent idle". Pane output activity is not agent activity.

**Candidate fix**

Replace the tmux signal with pane content inspection via `tmux capture-pane -p -t <sess>`. Look for the idle prompt and a `done HH:MM` marker, then compute age against the current time. Three constraints:

1. **Midnight wrap.** `HH:MM` carries no date. An age computation must handle `now < done` by assuming the previous day.
2. **No marker.** Panes with no `done` marker must report UNKNOWN or fall back to the tmux signal. They must never classify as idle.
3. **Marker format unverified.** The `done HH:MM` format is not in the context. Confirm against a live pane before writing the parser.

**Secondary defect (line 15).** `sort -u` on the session-name field collapses same-named sessions on different sockets into one arbitrary row. This is minor and arguably out of scope, but a builder touching this file should be told about it.

**Conflict and overlap**

None. No other BH1 row lists this file. BH1-06 (supply-guard noise) and BH1-09 (reaper paths) are functionally adjacent but touch different files.

**Recommended `BD_GATE_SCOPE`**

Harness-only. The script lives under `~/.local/bin`; confirm whether a tracked copy exists before cutting. Test with a stub `tmux` returning pane fixtures. Required cases: idle prompt with old `done`; idle prompt with recent `done`; active pane; no marker; midnight wrap. A read-only live run on one host is a useful smoke check.

---

### BH1-13 — `launcher-brief-not-delivered` (H, S, MED)

**Files and functions**

- Primary: `/home/mboyle/bd-persist/harness/bd-launch-role.sh`. The visible 14,000 bytes contain no `--brief` parsing, kick, or bootstrap feed.
- The visible usage line lists `--dry-run`, `--resume`, `--allow-unconfirmed-kick`, `--plugin-dir`. It does **not** list `--brief`. Either `--brief` is undocumented in usage or parsed later in the file.
- `CPLUS_BRIEF_DIR` (`state/cplus-launch`, approximately line 58 of the visible header) indicates brief handling exists on the C+ path.
- Likely second site: `bd-launch-codex-role.sh`. The evidence is a codex seat, `OGR3X`, blocked at bootstrap (`harness-work/BOOTSTRAP/bd-worker-OGR3X-codex/BLOCKED.md`). Codex launches route through that script.
- Relevant code regions, uncited: argv parsing, bootstrap/kick send, the rc 7 "bootstrap not consumed" check.

**Root-cause mechanism [INFERRED]**

`--brief` is parsed and validated (file-exists and similar) but its value never reaches the first message sent to the seat. The kick carries only the role bootstrap prompt. A seat that needs an order starts without one. Codex roles then stop at bootstrap and write BLOCKED.

**Fix constraints**

- Append the brief path to the first message or the kick, in **both** the Claude and codex launchers.
- `--dry-run` must print the brief delivery in its plan.
- `--allow-unconfirmed-kick` and the rc 7 semantics must be preserved.

**Conflict and overlap**

This is the most contended file in the range. All of these touch `bd-launch-role.sh`:

- BH1-04 (base-loop stderr)
- BH1-18 (LAUNCHED and RESULT lines)
- BH1-19 (token exposure)
- BH1-20 (cplus routing)
- BH1-31 (claim note)
- BH1-38 (seat-name guard)

BH1-31 also touches `bd-launch-codex-role.sh`. BH1-18 and BH1-20 also touch `lib_remote_seat.sh`. BH1-13 and BH1-18 both edit the kick and result flow, so that pair carries the highest merge risk.

**Recommended `BD_GATE_SCOPE`**

Launcher fixtures using the `BD_LAUNCH_*` env seams (`BD_LAUNCH_CODEX`, `BD_LAUNCH_SAY`, `BD_LAUNCH_ROLE_DIR`). Add a test that launches with `--brief <file>` and asserts the first message contains the path, for both Claude and codex. Add a `--dry-run` assertion. Run the existing rc 6/7/8 regressions. No BD band.

---

### BH1-14 — `kimi-builder-launch` (H, S, LOW)

**Files and functions**

- File: `/home/mboyle/bd-persist/harness/bd-launch-kimi.sh`. Only the 8,000-byte header was visible.
- Evidence: `state/ORDER-pm-B-accelerate-1740Z.md`. Not provided.
- The header documents `--host <ip>` routing tmux through `lib_remote_seat.sh`.

**Exit-code map from the header [READ]**

| Exit | Meaning | Fix-ref |
|---|---|---|
| 5 | QUOTA-REFUSED | FIX-R-326 K1 |
| 8 | ROLE-UNVERIFIED / ROLE-UNBOUND / SESSION-CHANGED | K3 |
| 9 | library or agent file missing | — |
| 10 | MCP-BD-ABSENT | K5 |
| 11 | PANE-SID-UNKNOWN / SEAT-UNCENSUSED | — |
| 12 | SEAT-STILL-UP | — |

**Root-cause mechanism [UNKNOWN]**

The context gives no direct cause. The row says the launch was never seated and F2-D was asked twice. That pattern is consistent with an orchestration gap, not a code defect. Candidate causes, in check order:

1. **Exit 5 QUOTA-REFUSED** [INFERRED link to BH1-17]. The kimi pool row flaps OK/UNKNOWN and the kimi OK write hides a real 403 LIMITED. A quota pre-check could refuse or be bypassed unpredictably.
2. **Exit 10 or 9.** A kimi seat needs an MCP bd binding and an agent file; either may be absent on the target.
3. **Exit 11 or 12.** The seat must be censused, or may still be up from a prior attempt.
4. **Exit 8.** Role binding failed.
5. **Not a code defect.** F2-D never called the launcher. The visible usage has no `--brief`, so a BH1-13-style order-delivery gap could also apply.

**STEP0 (mandatory before assignment):** read the ORDER file and F2-D's logs for the actual exit code or refusal line.

**Conflict and overlap**

- BH1-17 (`bd-kimi-usage-watch.sh`, POOL_STATE writers): possible cause and a conflict if quota is the cause.
- BH1-18 and BH1-20 touch `lib_remote_seat.sh`, which the kimi launcher uses for `--host`.
- BH1-13: same brief-delivery theme, different file.
- BH1-05 trims kimi role prompts, which are data files, not code.
- No other row lists `bd-launch-kimi.sh`.

**Recommended `BD_GATE_SCOPE`**

Investigation first, then a focused fix gated on the kimi launcher tests only, using a stubbed `tmux` and POOL_STATE fixtures. If the cause is quota or pool state, extend to the usage-watch tests (BH1-17's scope). Treat as dry-run reproduction plus stub-seat launch.

---

### BH1-15 — `say-hook-path-precheck` (H, S, LOW)

**Files and functions**

- The PreToolUse say-gate hook for `bd-say`. Not provided; cannot be named with confidence. It is distinct from `bd-verdict-write-hook.sh` (BH1-23).
- `/home/mboyle/bd-say.sh` (the `SAY` variable in `bd-launch-role.sh`). Per the BH1-01 row, this is where the "runtime re-check" lives.
- Evidence: about 10 PM refusals with `path_missing` in the inbox from 15:3xZ to 19:3xZ.

**Root-cause mechanism [INFERRED, but the row states it]**

The hook runs **before** the Bash command executes. It evaluates a static pre-execution snapshot. A single command that both writes a file and sends it, for example:

```bash
cat > f <<EOF
…
EOF
bd-say.sh target @f
```

presents a path to the hook that does not yet exist. The hook refuses with `path_missing`. Filesystem state at hook time is not filesystem state at send time.

**Fix options**

1. **Check at send time.** Drop the hook-time existence check and rely on the `bd-say.sh` runtime re-check that BH1-01 says already exists. Simpler and safer.
2. **Allow a path created earlier in the same command.** Requires the hook to parse the command for a prior write (`>`, `tee`, `cp`, `mv`, heredoc) to that path. This is the same fragile string parsing BH1-01 identifies as a gap. If chosen, it must not introduce new bypasses.

Recommendation: option 1.

**Conflict and overlap**

- **BH1-01 (hard).** Same hook, same concern (command parsing). Batch in one cut or serialize.
- BH1-23 touches a different hook; no conflict.

**Recommended `BD_GATE_SCOPE`**

Hook tests only. Add a write-then-send-in-one-command case that is accepted when the file exists at send time. Keep negatives: a missing path with no earlier write is refused at send time; the BH1-01 bypass attempts are not newly admitted. Include the `bd-say.sh` runtime re-check test.

---

### BH1-16 — `dcc-f9-known-limits`

**Files and functions**

- Row names no file. Evidence: `dispatch-H1-A/cuts/dcc-f9/RULING-pm-B-r6.md` and r7. Not provided.
- Expected target: `/home/mboyle/jarvisos_cockpit/jarvisos_backend/threat_gate.py`. **[FILE ABSENT]** in the context. No functions or lines can be cited.

**Root-cause mechanism [INFERRED]**

The threat gate scans command text for destructive verbs. Two documented limits:

1. It does not expand persistent `~/.gitconfig` aliases. An alias such as `git rm2 = …` shadows a destructive verb.
2. It does not catch words constructed at run time.

The rulings record both as known limits. This is a design decision, not a regression.

**Conflict and overlap**

None among BH1 rows. It is the only BD-cockpit row in this range; the other nine are harness rows.

**Recommended `BD_GATE_SCOPE`**

Gate-specific threat_gate tests (cockpit lane). If the decision is "accept", a docs-only change needs no gate beyond lint. If the decision is "gate `git` on alias shadow", add a test with a `GIT_CONFIG_GLOBAL` alias fixture. First confirm the file path exists.

---

### BH1-17 — `pool-state-writer-integrity`

**Files**

- `~/bin/bd-pool-usage-refresher.py` and `bd-kimi-usage-watch.sh`. Row estimates, unverified. No line numbers.

**Root-cause mechanism [INFERRED from the finding]**

- Two writers update the grok and kimi `POOL_STATE` rows. They flap OK/UNKNOWN every 4–5 minutes.
- The kimi OK write masks a real 403 LIMITED.
- Partial rows get published.
- The finding says the refresher hard-coded OK rows. The live copy no longer contains the literals as of 19:07Z, but no commit names the fix. The `~/bin` copy has drifted outside the harness.

**STEP0:** diff the live `~/bin` copy against the tracked staged copies before any change.

**Conflict and overlap**

- **BH1-07** (pool-usage-refresher-residuals) very likely touches the same refresher: A1 seed guard on `fetched_ms`, A2 BD_* comma form.
- BH1-03 (usage CSV) is related but separate.
- BH1-14 may depend causally on this row (exit 5 QUOTA-REFUSED).

**Recommended `BD_GATE_SCOPE`**

Refresher and kimi usage-watch tests. Add a two-writer interleave test and a 403 LIMITED test proving an OK write cannot hide it. Live-versus-tracked diff first.

---

### BH1-18 — `launcher-result-line-consistency`

**Files**

- `bd-launch-role.sh` and `lib_remote_seat.sh` (from the row).
- The `bd-launch-codex-role.sh` excerpt shows its exit-code contract (0 launched, 2 usage, 3 unknown role, 4 missing agent def, 5 session exists, 6 launch failed), that `lib_remote_seat.sh` is sourced, and that `bd_take_host_arg` handles `--host`. It does not show where LAUNCHED or RESULT is printed.

**Root-cause mechanism [INFERRED]**

Three separate output-contract breaks:

1. On a cplus host, LAUNCHED is printed before the remote claim and seat confirmation complete. The launcher then reports RESULT rc=7 with no seat. Observed 3 of 3 today.
2. rc 8 exits without writing a RESULT line or receipt.
3. An UNCONFIRMED plus FAILED cplus run loses its WARN line.

Fix: print LAUNCHED only on rc 0; emit RESULT and a receipt on every exit path.

**Conflict and overlap**

- BH1-19 and BH1-20 share `bd-launch-role.sh`; BH1-20 also shares `lib_remote_seat.sh`.
- BH1-13, BH1-04 and BH1-31 likely share `bd-launch-role.sh`.
- BH1-37 and BH1-38 are possible overlaps [INFERRED].

**Recommended `BD_GATE_SCOPE`**

Launcher tests covering the rc 0, 7, 8 output contract, including a cplus fixture with no seat. Run launcher-claim-fail-uniform tests as regression.

---

### BH1-19 — `launcher-token-exposure`

**Files**

- `bd-launch-role.sh`. No function or line given; file not in context.

**Root-cause mechanism [INFERRED]**

- **ADV1:** The launcher places `CLAUDE_CODE_OAUTH_TOKEN` in the environment of the target tmux or claude process. Every descendant inherits it.
- **ADV2:** With `set -x` on, the assignment or export line prints the token into the trace.

**Candidate fixes**

- Scope the token to the claude exec only, or have the child read it from a 0600 file and unset it for descendants.
- Disable xtrace around secret lines with `{ set +x; } 2>/dev/null`.

**Conflict and overlap**

BH1-18, BH1-20, BH1-31 share the file; BH1-13 and BH1-04 likely do. Same-file merge conflicts; serialize or rebase.

**Recommended `BD_GATE_SCOPE`**

Launcher tests plus an xtrace-capture test that fails if the token string appears, and an env-inheritance test on a child of the seat. Apply a security-review lens.

---

### BH1-20 — `lvc-cplus-routing-advisories`

**Files**

- `bd-launch-role.sh` and `lib_remote_seat.sh`.

**Root-cause mechanism (advisories are explicit)**

- **A1:** `BD_CPLUS_PUBLISH` is trusted verbatim with no validation.
- **A2:** The no-cplus-share FALLBACK proves the share is absent on the launching box only, not on the target.
- **A3:** An alias whose MOUNT row names the hub IP resolves to the hub, so the seat launches on the hub. Fix: refuse when `_canon == BD_HUB_HOST`.
- **OWED-EDGES:** alias ssh `known_hosts`; hub-by-alias rc6.

**Conflict and overlap**

BH1-18 shares both files. BH1-19 shares `bd-launch-role.sh`. BH1-21 and BH1-22 are cplus-related but touch different files (rid `patched/` files, and ops only).

**Recommended `BD_GATE_SCOPE`**

Launch-via-cplus (lvc) suite plus new cases for each of A1–A3. Run launcher-claim-fail-uniform with BH1-18.

---

## 3. File-conflict groupings

### 3.1 Conflict map by file

| File | Rows in this range | Rows outside this range | Contention |
|---|---|---|---|
| `bd-launch-role.sh` | BH1-13, 18, 19, 20 | BH1-04, 31, 38 (37 possible) | **Highest.** Seven to eight rows. |
| `lib_remote_seat.sh` | BH1-18, 20 | — (BH1-14 consumes it via `--host`) | High |
| `bd-launch-codex-role.sh` | BH1-13 (likely) | BH1-31 | Medium |
| `bd-role-claim.sh` (`wipe_alert`) | BH1-11 | BH1-35 | High (explicit carve-out) |
| `tests/test_registry_wipe_guard.py` | BH1-11 | BH1-35, BH1-10 | Medium |
| say-gate PreToolUse hook | BH1-15 | BH1-01 | High (same parsing logic) |
| `bd-pool-usage-refresher.py` / `bd-kimi-usage-watch.sh` | BH1-17 | BH1-07 | Medium |
| `bd-launch-kimi.sh` | BH1-14 | none | Low |
| `bd-pm-idle-sweep` | BH1-12 | none | None |
| `threat_gate.py` | BH1-16 | none | None |

### 3.2 Recommended cut chains

**Chain A — `bd-launch-role.sh` serialized chain.** Order by blast radius, smallest first:

1. BH1-19 (token scoping; localized to the exec and export lines)
2. BH1-20 (cplus routing; touches `lib_remote_seat.sh` resolution)
3. BH1-18 (LAUNCHED/RESULT contract; touches every exit path)
4. BH1-13 (brief delivery; touches the kick, which BH1-18 just restructured)
5. Then BH1-04, BH1-31, BH1-38 from outside this range.

Rationale: BH1-18 restructures exit paths, so BH1-13's kick edit should land on top of the new structure rather than under it. Run launcher-claim-fail-uniform after every link.

**Chain B — rwg hardening.** BH1-11 first, then BH1-35, then BH1-10. BH1-11 is a one-primitive change inside `wipe_alert`; BH1-35 explicitly excluded it and will edit around it.

**Chain C — say-gate hook.** BH1-15 and BH1-01 in one cut. Both change what the hook accepts. Two cuts would each need to re-derive the other's test matrix.

**Chain D — pool state.** BH1-17 STEP0 diff, then BH1-07 and BH1-17 together or in that order. BH1-14's assignment waits on BH1-17's STEP0 if its exit code turns out to be 5.

**Independent.** BH1-12 (sole owner of its file), BH1-16 (sole cockpit row, decision-first).

---

## 4. Pre-assignment STEP0 checklist for `bd-pm-B`

| Row | STEP0 before dispatch |
|---|---|
| BH1-11 | Read `wipe_alert` in `bd-role-claim.sh` to confirm the stamp primitive and cite lines. |
| BH1-12 | Confirm a tracked copy of `bd-pm-idle-sweep` exists. Confirm the `done HH:MM` marker format on a live pane. |
| BH1-13 | Read `bd-launch-role.sh` past byte 14000 for `--brief` handling and the kick. Check `bd-launch-codex-role.sh` for the same. |
| BH1-14 | Read `state/ORDER-pm-B-accelerate-1740Z.md` and F2-D's logs for the actual exit code. Do not assign a code fix until the exit code is known. |
| BH1-15 | Identify the say-gate hook file name. Confirm the `bd-say.sh` runtime re-check exists as BH1-01 claims. |
| BH1-16 | Confirm `threat_gate.py` exists at the expected path. Decide accept-vs-gate before dispatch. |
| BH1-17 | Diff live `~/bin/bd-pool-usage-refresher.py` against tracked copies. |
| BH1-18 | Locate the LAUNCHED and RESULT emission sites in `bd-launch-role.sh` and `lib_remote_seat.sh`. |
| BH1-19 | Locate the `CLAUDE_CODE_OAUTH_TOKEN` export and any `set -x` regions. |
| BH1-20 | Locate `BD_CPLUS_PUBLISH` consumption and the `_canon` resolution in `lib_remote_seat.sh`. |

---

## 5. Risk notes

- **Nine of ten rows have no line citations.** Builders dispatched from this dossier alone will spend their first turns reading. Budget that, or run the STEP0 reads as a single Explore pass before dispatch.
- **BH1-14 may not be a code defect.** Assigning a builder before STEP0 risks a cut against the wrong file.
- **BH1-17 live/tracked drift** means the harness is not the source of truth for `~/bin`. Any fix landed only in the tracked copy will not change live behavior until the copy is redeployed.
- **BH1-19 is a credential exposure.** It should not wait on Chain A ordering if the token is currently leaking into traces. Consider landing it as the first link regardless.
- **BH1-15 option 2** reintroduces the parsing fragility BH1-01 is trying to close. Pick option 1 unless there is a reason the runtime re-check is insufficient.

---

# Appendix: Specialist Subagent Read-Only Code Inspection Reports

## Investigator Report 1: BH1-11 to BH1-15 Code & Blame Inspector (`claude-sonnet-5-5-high`)

# Dossier: BH1-11 to BH1-15 (`Fable5-Investigator-2-S1`)

I made no tool calls, as instructed. This dossier uses only the `<SOURCE_CONTEXT>`, which is truncated. Four files were not provided or were cut off before the relevant code:

- The `bd-role-claim.sh` section that holds `wipe_alert`.
- Everything in `bd-launch-role.sh` after byte 14000.
- Everything in `bd-launch-kimi.sh` after byte 8000.
- The say-hook script.

Where I give no line numbers, they are unknown. Where a mechanism is inferred rather than read from code, I mark it **[INFERRED]**. `BD_GATE_SCOPE` values are proposals, because the scope vocabulary is not in the context.

---

## BH1-11 `rwgh-wipe-alert-lock` (H, S, LOW)

**1. Files and functions**
- The estimated file is `bd-role-claim.sh`, taken from the BH1-35 "Files (est.)" column. The function is `wipe_alert`.
- The tests are in `tests/test_registry_wipe_guard.py`, also from the BH1-35 column.
- Neither file was provided, so I can give no line numbers.
- The row's evidence is `dispatch-E3-D/cuts/rwg-hardening/r1/PRECHECK-RWGHP-C.md` (finding A1), which I have not seen.

**2. Root-cause mechanism [INFERRED from the row text]**
- `wipe_alert` reads a dedupe or stamp state and then writes the stamp, with no lock around the pair.
- Two concurrent reaps can both read "no recent alert" before either stamps. Both then fire the alert and both write the stamp. The result is duplicate alerts or a stamp that has been overwritten.
- The fix is an atomic section. Options are `flock` on a sidecar lockfile, or an atomic `mkdir` or `set -C` noclobber stamp.
- The BH1-35 text confirms this is the same code path: "Unlocked check-then-stamp is excluded (= BH1-11)".

**3. Conflict and overlap**
- **BH1-35** is the main conflict. It covers the same `bd-role-claim.sh` and `tests/test_registry_wipe_guard.py`, and it explicitly carved BH1-11 out. Land BH1-11 first or in the same cut, so BH1-35's SEC A1/A2 fixes don't conflict with it.
- **BH1-10** shares the rwg-hardening test family, specifically the `reg.tsv.reap-refused` FIFO tests. It will likely touch the same test file.
- **BH1-36** touches `tests/lib_live_registry_guard.sh` and its A2 item concerns `wipe_alert`. It is a probable semantic overlap, not a file overlap.

**4. Recommended `BD_GATE_SCOPE`**
- Harness-only. Run the registry-wipe-guard and rwg-hardening tests.
- Add a new concurrent-reap test that launches two reaps in parallel and asserts exactly one alert. The test needs a mutant that removes the lock to prove it can fail.
- No BD band is needed.

---

## BH1-12 `idle-sweep-activity-signal` (H, S, MED)

**1. Files and lines**
- File: `/home/mboyle/.local/bin/bd-pm-idle-sweep`. The whole file was provided (19 lines).
- **Line 7** (inside the `scan='…'` string): `tmux … list-panes -a -F "#{session_name}|#{window_activity}|#{pane_current_command}|#{pane_pid}"`. This is where `window_activity` is read.
- **Line 8**: `idle=$(( (now-act)/60 ))`. This is the faulty age computation.
- **Line 16**: `[ "$idle" -ge "$IDLE_MIN" ] || continue`. This filter never trips, because `idle` is always small.
- **Lines 8–9**: `kids=$(pgrep -P "$pid" …)` and the `BARE`/`SEAT` classification. Only direct children of the pane pid are counted.
- **Line 5**: `IDLE_MIN` defaults to 45.

**2. Root-cause mechanism**
- `#{window_activity}` is the time of the last output to the window. A Claude TUI repaints its spinner, status line and cursor, so tmux resets activity continuously.
- A seat sitting at its prompt therefore reads as active. This matches the "66/66 read <30 min" evidence, and the sweep reaped 41 idle seats only by manual verification.
- The tmux signal is the wrong proxy for "agent idle". The fix reads pane content instead, with `tmux capture-pane -p -t <sess>`.
- It should look for the idle prompt and a `done HH:MM` marker, then compute age against the current time.
- The parse must handle midnight wrap, because `HH:MM` has no date.
- It must also handle panes with no `done` marker. Those should be reported as UNKNOWN or fall back to the tmux signal, never as idle.
- The `done HH:MM` format is not shown in the context, so confirm it against a live pane first.
- A secondary defect is on **line 15**. `sort -u -t'|' -k2,2` de-duplicates by session name across sockets and keeps an arbitrary row. This is minor and arguably out of scope, but note it.

**3. Conflict and overlap**
- No other BH1 row lists this file. It is conflict-free.
- It is functionally related to BH1-06 (supply-guard noise) and BH1-09 (reaper paths), but they touch different files.

**4. Recommended `BD_GATE_SCOPE`**
- Harness-only. The script lives under `~/.local/bin`, so confirm whether it has a tracked copy.
- Test with a stub `tmux` that returns a captured pane fixture. Cover these cases:
  - an idle prompt with an old `done` time;
  - an idle prompt with a recent `done` time;
  - an active pane;
  - a pane with no marker;
  - midnight wrap.
- A read-only live run against one host is a useful smoke check.

---

## BH1-13 `launcher-brief-not-delivered` (H, S, MED)

**1. Files and functions**
- The primary file is `/home/mboyle/bd-persist/harness/bd-launch-role.sh`.
- The visible 14000 bytes (the header, `model_for`, and the start of `effort_for`) do not contain `--brief` parsing or the kick or bootstrap feed.
- The usage line visible at the top lists `--dry-run`, `--resume`, `--allow-unconfirmed-kick` and `--plugin-dir`. It does not list `--brief`.
- I cannot cite line numbers. The relevant code is argv parsing, the bootstrap/kick send, and the rc 7 "bootstrap not consumed" check.
- The evidence is a codex seat, `OGR3X`, blocked at bootstrap (`harness-work/BOOTSTRAP/bd-worker-OGR3X-codex/BLOCKED.md`). Codex launches go through `bd-launch-codex-role.sh`, which BH1-31 names. That is a likely second site.
- Note `CPLUS_BRIEF_DIR` (`state/cplus-launch`, line ~58 of the visible header). This suggests brief handling exists for the C+ path.

**2. Root-cause mechanism [INFERRED from the row text]**
- `--brief` is parsed and validated (file exists and so on), but the value is never included in the first message sent to the seat.
- The kick sends only the role bootstrap prompt, so the seat starts with no order.
- Codex roles that need an order then stop at bootstrap and write BLOCKED.
- The fix is to append the brief path as the first message, or as part of the kick. It must be applied in both the Claude and codex launchers.
- The fix must also work with `--dry-run`, so the printed plan shows the brief delivery.
- It must keep the `--allow-unconfirmed-kick` and rc 7 semantics intact.

**3. Conflict and overlap**
- All of these touch `bd-launch-role.sh`, so serialize them:
  - **BH1-04** (base-loop stderr)
  - **BH1-18** (LAUNCHED and RESULT lines)
  - **BH1-19** (token exposure)
  - **BH1-20** (cplus routing)
  - **BH1-31** (claim note)
  - **BH1-38** (seat-name guard)
- **BH1-31** also touches `bd-launch-codex-role.sh`, as BH1-13 likely does.
- **BH1-18** and **BH1-20** also touch `lib_remote_seat.sh`.
- BH1-13 and BH1-18 both touch the kick and result flow, so they carry the highest merge risk.

**4. Recommended `BD_GATE_SCOPE`**
- Launcher fixtures that use the `BD_LAUNCH_*` env seams (`BD_LAUNCH_CODEX`, `BD_LAUNCH_SAY`, `BD_LAUNCH_ROLE_DIR`).
- Add a test that launches with `--brief <file>` and asserts the first message sent contains the path. Cover both Claude and codex.
- Also run a `--dry-run` assertion and the existing rc 6/7/8 regressions.
- No BD band is needed.

---

## BH1-14 `kimi-builder-launch` (H, S, LOW)

**1. Files and functions**
- The file is `/home/mboyle/bd-persist/harness/bd-launch-kimi.sh`. Only the header (8000 bytes) was provided.
- The header documents these exit codes: 5 QUOTA-REFUSED (FIX-R-326 K1), 8 ROLE-UNVERIFIED, ROLE-UNBOUND or SESSION-CHANGED (K3), 9 library or agent file missing, 10 MCP-BD-ABSENT (K5), 11 PANE-SID-UNKNOWN or SEAT-UNCENSUSED, and 12 SEAT-STILL-UP.
- The header also says `--host <ip>` routes tmux through `lib_remote_seat.sh`. I cannot give line numbers.
- The evidence file is `state/ORDER-pm-B-accelerate-1740Z.md`. It was not provided.

**2. Root-cause mechanism**
- The context gives no direct root cause. The row says the launch was never seated and F2-D was asked twice, so the cause may be a dispatch or orchestration gap rather than a code defect.
- Candidates from the header's exit map, in the order I would check them:
  1. **Exit 5 QUOTA-REFUSED [INFERRED link to BH1-17]:** the kimi pool row flaps OK/UNKNOWN, and the kimi OK write hides a real 403 LIMITED. A quota pre-check at launch could refuse, or be bypassed unpredictably.
  2. **Exit 10 MCP-BD-ABSENT or exit 9 agent file missing:** a kimi seat needs an MCP bd binding and an agent file, and these may be absent on the target.
  3. **Exit 11 or 12:** the seat has to be censused, or may still be up from a prior attempt.
  4. **Exit 8:** role binding failed.
  5. **Not a code defect:** F2-D never called the launcher. It has no `--brief` in the visible usage, so an order delivery gap like BH1-13 could also apply.
- Step 0 is to read the ORDER file and F2-D's logs for the actual exit code or refusal line, before any code change.

**3. Conflict and overlap**
- **BH1-17** (`bd-kimi-usage-watch.sh`, POOL_STATE writers) is a possible cause and a conflict if the cause is quota.
- **BH1-13** touches `bd-launch-role.sh`, a different file, but the same brief-delivery theme.
- **BH1-18** and **BH1-20** touch `lib_remote_seat.sh`, which the kimi launcher uses for `--host`.
- **BH1-05** trims kimi role prompts, which are data files, not code.
- No other row lists `bd-launch-kimi.sh` itself.

**4. Recommended `BD_GATE_SCOPE`**
- Investigation first, then a focused fix with the kimi launcher tests only. Use a stubbed `tmux` and POOL_STATE fixtures.
- If the cause is quota or pool state, extend verification to the usage-watch tests (BH1-17's scope).
- Treat it as a dry-run reproduction plus a stub-seat launch.

---

## BH1-15 `say-hook-path-precheck` (H, S, LOW)

**1. Files and functions**
- The hook script was not provided and I cannot name it with confidence. It is the PreToolUse say-gate hook for `bd-say`.
- It is distinct from `bd-verdict-write-hook.sh`, which BH1-23 names.
- `bd-say.sh` is `/home/mboyle/bd-say.sh`, the `SAY` variable in `bd-launch-role.sh`. It is where the "runtime re-check" lives, per the BH1-01 row.
- The evidence is about 10 PM refusals, with `path_missing` in the inbox from 15:3xZ to 19:3xZ.

**2. Root-cause mechanism**
- The row states it: the hook runs **before** the Bash command executes. If one command both writes a file and sends it (for example `cat > f <<EOF … EOF; bd-say.sh target @f`), the hook sees a path that does not exist yet and refuses with `path_missing`.
- The hook evaluates a static pre-execution snapshot, and filesystem state at hook time is not the state at send time.
- The fix options in the row:
  1. **Check at send time.** Drop the hook-time existence check and rely on the `bd-say.sh` runtime re-check, which already exists per BH1-01.
  2. **Allow a path created earlier in the same command.** This needs the hook to parse the command for a prior write (`>`, `tee`, `cp`, `mv`, heredoc) to that path.
- Option 1 is simpler and safer. Parsing writes in the hook is the same fragile string parsing that BH1-01 identifies as a gap. If option 2 is chosen, it must not add new bypasses.

**3. Conflict and overlap**
- **BH1-01** touches the same say-gate hook and is a hard conflict. Both are about the hook's command parsing.
- Put BH1-15 and BH1-01 in one cut or serialize them.
- BH1-23 touches a different hook (`bd-verdict-write-hook.sh`) and does not conflict.

**4. Recommended `BD_GATE_SCOPE`**
- Hook tests only. Add a case for write-then-send in one command, so it is accepted when the file exists at send time.
- Keep negative cases:
  - a missing path with no earlier write is refused at send time;
  - the BH1-01 bypass attempts are not newly admitted.
- Include the `bd-say.sh` runtime re-check test.

---

## Cross-row summary

| Row | Primary file | Overlaps |
|---|---|---|
| BH1-11 | `bd-role-claim.sh` | BH1-35 (hard), BH1-10, BH1-36 |
| BH1-12 | `bd-pm-idle-sweep` | none |
| BH1-13 | `bd-launch-role.sh`, `bd-launch-codex-role.sh` | BH1-04, 18, 19, 20, 31, 38 (hard); also BH1-18/20 via `lib_remote_seat.sh` |
| BH1-14 | `bd-launch-kimi.sh` | BH1-17 (cause), BH1-18/20 via `lib_remote_seat.sh` |
| BH1-15 | say-gate hook | BH1-01 (hard) |

**Suggested batching:** BH1-12 can go alone. Batch BH1-11 with BH1-35 and BH1-15 with BH1-01. Serialize BH1-13 behind or with the other `bd-launch-role.sh` rows. Do BH1-14's STEP0 read of the ORDER file before assigning it.

## Investigator Report 2: BH1-16 to BH1-20 Code & Blame Inspector (`claude-sonnet-5-5-low`)

# Read-only dossier: BH1-16 to BH1-20 (Fable5-Investigator-2-S2)

I made no tool calls. Everything below comes from the provided `<SOURCE_CONTEXT>`. That context holds the ROWS table and the first 10,000 bytes of `bd-launch-codex-role.sh`. It does not include `bd-launch-role.sh`, `lib_remote_seat.sh`, or the dcc-f9 rulings. So I cannot give line numbers or confirmed root causes for most rows.

Items marked **(inferred)** are my reading of the row text. They are not verified against code.

## BH1-16: dcc-f9-known-limits
1. **Files and lines:**
   - The row names no file.
   - The evidence is `dispatch-H1-A/cuts/dcc-f9/RULING-pm-B-r6.md` and r7.
   - The expected target is `/home/mboyle/jarvisos_cockpit/jarvisos_backend/threat_gate.py`.
   - That file is marked **[FILE ABSENT]** in the context, so I cannot cite functions or lines.
2. **Root cause (inferred from the row text):**
   - The threat gate checks the command text for destructive verbs.
   - It does not expand persistent `~/.gitconfig` aliases. An alias such as `git rm2 = …` can therefore shadow a destructive verb.
   - It does not catch words built at run time.
   - The ruling documents both as known limits, so this is a design decision and not a regression.
3. **Overlap:** None among BH1 rows. It is the only BD-cockpit row in this range, and the other four are all harness launcher rows.
4. **`BD_GATE_SCOPE`:**
   - The gate-specific tests for threat_gate (cockpit lane).
   - If the decision is "accept", a docs-only change needs no gate beyond lint.
   - If the decision is "gate `git` on an alias shadow", add a test that sets a `GIT_CONFIG_GLOBAL` alias fixture.
   - First confirm that the file path exists.

## BH1-17: pool-state-writer-integrity
1. **Files:**
   - `~/bin/bd-pool-usage-refresher.py` and `bd-kimi-usage-watch.sh`. These are the row's estimates, not yet checked.
   - No line numbers are available.
2. **Root cause (per the finding, unverified):**
   - Two writers update the grok and kimi `POOL_STATE` rows.
   - They flap OK and UNKNOWN every 4–5 minutes.
   - The kimi OK write hides a real 403 LIMITED.
   - Partial rows get published.
   - The finding says the refresher hard-coded OK rows. The live copy no longer has the literals as of 19:07Z, but no commit names the fix, so the `~/bin` copy drifted outside the harness.
   - STEP0: diff the live `~/bin` copy against the tracked staged copies.
3. **Overlap:**
   - BH1-07 (pool-usage-refresher-residuals) very likely touches the same refresher: A1 seed guard on `fetched_ms`, and A2 BD_* comma form.
   - BH1-03 (usage CSV) is related but separate.
4. **`BD_GATE_SCOPE`:** Tests for the refresher and the kimi usage watch. Add a two-writer interleave test and a 403 LIMITED test that an OK write must not hide. First do the live-versus-tracked diff.

## BH1-18: launcher-result-line-consistency
1. **Files:** `bd-launch-role.sh` and `lib_remote_seat.sh`, both from the row. The provided excerpt of `bd-launch-codex-role.sh` shows only the start of the script.
   - Visible in that excerpt: the exit-code contract (0 launched, 2 usage, 3 unknown role, 4 missing agent def, 5 session exists, 6 launch failed).
   - Also visible: `lib_remote_seat.sh` is sourced and `bd_take_host_arg` is used for `--host`.
   - Nothing in the excerpt shows where LAUNCHED or RESULT is printed.
2. **Root cause (inferred):**
   - For a cplus host, the launcher prints LAUNCHED before the remote claim and seat confirmation finish. It then reports RESULT rc=7 with no seat (3 of 3 today).
   - rc 8 exits without writing a RESULT line or receipt.
   - An UNCONFIRMED plus FAILED cplus run loses its WARN line.
   - The fix is to print LAUNCHED only on rc 0 and to emit RESULT and a receipt on every exit path.
3. **Overlap:**
   - BH1-19 and BH1-20 touch `bd-launch-role.sh`. BH1-20 also touches `lib_remote_seat.sh`.
   - BH1-13 (`launch-role --brief`), BH1-04 and BH1-31 likely touch `bd-launch-role.sh` too (BH1-31 per its listed files).
   - BH1-38 and BH1-37 are possible overlaps (inferred).
4. **`BD_GATE_SCOPE`:** Launcher tests covering the rc 0, 7 and 8 output contract, including a cplus fixture with no seat. Run the launcher-claim-fail-uniform tests as a regression.

## BH1-19: launcher-token-exposure
1. **Files:** `bd-launch-role.sh`. The row gives no function or line, and the file is not in the context.
2. **Root cause (inferred):**
   - **ADV1:** the launcher puts `CLAUDE_CODE_OAUTH_TOKEN` in the environment of the target tmux or claude process. Every descendant of claude therefore inherits it.
   - **ADV2:** if `set -x` is on, the assignment or export line prints the token to the trace.
   - Possible fixes: scope the token to the claude exec only, or have the child read it from a 0600 file and unset it for descendants. Disable xtrace around the secret lines with `{ set +x; } 2>/dev/null`.
3. **Overlap:** BH1-18, BH1-20 and BH1-31 touch the same file (BH1-13 and BH1-04 likely do too). These are same-file merge conflicts, so serialize them or rebase.
4. **`BD_GATE_SCOPE`:**
   - Launcher tests with an xtrace-capture test that fails if the token string appears.
   - An env-inheritance test for a child of the seat.
   - Also run a security-review lens.

## BH1-20: lvc-cplus-routing-advisories
1. **Files:** `bd-launch-role.sh` and `lib_remote_seat.sh`.
2. **Root cause (per the advisories):**
   - **A1:** `BD_CPLUS_PUBLISH` is trusted verbatim, with no validation.
   - **A2:** the no-cplus-share FALLBACK proves the share is absent on the launching box only, not on the target.
   - **A3:** an alias whose MOUNT row names the hub IP resolves to the hub, so the seat launches on the hub. The fix is to refuse when `_canon == BD_HUB_HOST`.
   - **OWED-EDGES:** alias ssh `known_hosts`, and hub-by-alias rc6.
3. **Overlap:** BH1-18 shares both files. BH1-19 shares `bd-launch-role.sh`. BH1-21 and BH1-22 are cplus-related but touch different files (the rid `patched/` files, and ops only).
4. **`BD_GATE_SCOPE`:** Launch-via-cplus tests (the lvc suite) plus new cases for each of A1–A3. Run the launcher-claim-fail-uniform tests with BH1-18.

## Conflict map
| File | Rows |
|---|---|
| `bd-launch-role.sh` | BH1-18, 19, 20 (also 13, 31, probably 04) |
| `lib_remote_seat.sh` | BH1-18, 20 |
| refresher / usage | BH1-17, 07 |
| `threat_gate.py` | BH1-16 only |

**Recommendation:** batch BH1-18, 19 and 20 into one sequenced cut chain on `bd-launch-role.sh`. Run BH1-16 and BH1-17 independently.
