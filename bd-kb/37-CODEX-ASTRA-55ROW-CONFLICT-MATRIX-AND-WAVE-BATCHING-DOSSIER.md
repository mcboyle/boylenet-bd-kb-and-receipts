# BD-KB Volume 37: Codex Astra (`gpt-6-astra`) Master 55-Row File-Overlap Conflict Matrix & Wave Batching Dossier
> **Lead Investigator**: `Codex-Astra-Master-Matrix` (`codex (gpt-6-astra)`)
> **Subagent Squad**: 2x Parallel `AGY claude+gpt` (`claude-sonnet-5-5-high` & `claude-sonnet-5-5-low`) Read-Only Code Investigators
> **Generated At**: `2026-10-19T23:33:57Z`
> **Scope**: Strictly Read-Only Investigation Dossier for `bd-pm-B` (`Zero Code Changes`, `O3416` Compliant)

---

# BD-KB Volume 37: Codex Astra (`gpt-6-astra`) Master 55-Row File-Overlap Conflict Matrix & Wave Batching Dossier

**Author:** Lead Principal Investigator `Codex-Astra-Master-Matrix`  
**Recipient:** `bd-pm-B`  
**Scope:** BH1-01 through BH1-55  
**Mode:** 100% read-only synthesis of the two supplied investigator reports. No tools or commands were called.

## 1. Executive assessment

The 55-row backlog contains several concentrated file-conflict groups. The most consequential are the launcher family, role registry, telemetry daemon, usage refresher, and cockpit threat gate. Dispatching their constituent rows as independent edits against the same base would create overlapping ownership and increase the chance of silently undoing another row’s correction.

The recommended approach is to assign each tightly coupled bundle to one integration owner, then stack subsequent changes on its accepted result. Rows whose reported files are disjoint can proceed concurrently, subject to scope confirmation and operational dependencies.

**The supplied evidence supports a provisional file-overlap matrix, not a verified guarantee of zero collisions.** Several inventories are explicitly estimated, some name only test families, and several investigations have unidentified writers or implementation files. Those rows must not be counted as proven independent.

Three distinctions govern the batching recommendations:

1. **File collision:** two rows edit the same file. They require one composite owner or sequential integration.
2. **Behavioral dependency:** rows edit different files but affect the same contract, fixtures, or gate baseline. They may be authored concurrently, but acceptance needs coordination.
3. **Operational dependency:** installation, deployment, shared live state, or policy determines readiness independently of source-file overlap.

### Principal conflict concentrations

| Shared file or surface | Rows | Recommended treatment |
|---|---|---|
| `bd-launch-role.sh` | 13, 18, 19, 20, 31, 38 | BHC-04 composite, then 19 → 20 → 38 |
| `lib_remote_seat.sh` | 09, 18, 20 | Coordinate registry and launcher trains; finish 09 and 18 before 20 |
| `bd-role-claim.sh` | 09, 11, 35 | BHC-03 composite before 09 |
| `bd-telemetry-daemon.py` | 04, 08, 42 | BHC-05 composite |
| `bd_telemetry/registry.py` | 08, 42, 44 | BHC-05 before 44 |
| `bd-usage-probe.sh` | 07, 40 | BHC-06 composite |
| `~/bin/bd-pool-usage-refresher.py` | 07, 17, 40 | BHC-06 before 17; resolve tracked/install identity |
| `bd_cost_accounting.py` | 24, 55 | BHC-07 composite |
| `jarvisos_backend/threat_gate.py` | 16, 51 | BHC-08 composite, subject to policy decision |
| `session_keeper.py` | 34, 46 | Treat as conflicting until estimated overlap is resolved |

The launcher and registry families are connected through BH1-09, BH1-18, and `lib_remote_seat.sh`. They cannot be managed as wholly independent trains.

## 2. Evidence standard and citation limits

This dossier cites the investigator reports as **S1** for Harness and **S2** for BulkDownloader/Cockpit. Their file lists and defect descriptions are the evidence available here; neither report supplied source contents or a complete implementation inventory.

The only supplied numerical source locations are:

- **BH1-04:** `bd-telemetry-daemon.py:342` and `bd-telemetry-daemon.py:345`, within `main()`, reported by S1.
- These locations were not independently checked against a commit and may move between revisions.

For every other file reference below, **line numbers are COULD NOT LOOK**. Function names, paths, test names, and symbols are reproduced where supplied. Inventing numerical citations would make this dossier less reliable.

Additional interpretation rules:

- A named path is a **reported edit surface**, not proof that the final patch will touch only that path.
- “No known overlap” means no overlap appears in these reports. It does not certify unnamed tests or installation scripts.
- Glob patterns identify unresolved file sets.
- Paths under `~/bin` and `~/.local/bin` identify installed artifacts whose relationship to tracked sources needs confirmation.
- `BD_GATE_SCOPE` syntax was not supplied. This dossier identifies gate content without inventing valid configuration tokens.
- Reported root causes remain reported mechanisms unless the evidence explicitly says the cause is unknown.

## 3. Complete 55-row file-overlap and conflict matrix

Rows below are listed exactly once. File citations inherit the line-number limitation above.

### BH1-01 through BH1-15

| Row | Reported files / citation | Reported mechanism or unresolved cause | Conflict and dispatch treatment |
|---|---|---|---|
| **BH1-01** | bd-fleet-mcp plugin `cli.py`; `tests/checks.py` — S1 | PreToolUse parsing misses nested `bash -c '$MSG'` and escaped redirect words; text fidelity also has gaps. Runtime `bd-say.sh` checking provides mitigation. | Shares both files with 15. **BHC-01**, one owner. |
| **BH1-02** | `bd-claim-object.sh`, `take` path — S1 | Home-wide `find` reaches slow NFS and reportedly blocked in D-state for 27 minutes. Search roots need bounding. | Shares script with 53. **BHC-02**. |
| **BH1-03** | `bd-usage-monitor.sh`; `bd_telemetry/sensors/bd_usage_monitor.py`; `bd-usage-forecaster.py` — S1 | Incomplete census output lacks a PARTIAL marker, preventing consumers from distinguishing incomplete observations. | No reported identical-file overlap. Parallel candidate after test scope is fixed. |
| **BH1-04** | `bd-telemetry-daemon.py:342`, `:345`, `main()` — S1 | Error reporting to unwritable stderr reportedly terminates the daemon with rc 120. The register misidentifies the launcher as the affected file. | Shares daemon with 08 and 42. **BHC-05**; check ORDER-2331 r5 first. |
| **BH1-05** | Approximately 100 role-prompt files; `tests/test_role_prompt_budget.py` — S1 | Prompts exceed the 800-token budget; examples include review-security and status. | No reported cross-row overlap, but enumerate prompts. Internal parallelism must give the shared budget test one owner. |
| **BH1-06** | `bd-supply-guard.sh` — S1 | OWED scanning ignores CLOSED-*/LANDED-* markers and repeatedly emits false OWED results. | No reported overlap. Parallel candidate. |
| **BH1-07** | `bd-usage-probe.sh`; `~/bin/bd-pool-usage-refresher.py` — S1 | Non-numeric `fetched_ms` bypasses the seed guard; comma-separated `BD_*` values are ineffective and undocumented. | Shares files with 40 and refresher with 17. **BHC-06**; verify O3394-related premise first. |
| **BH1-08** | `bd-telemetry-daemon.py`; `bd_telemetry/registry.py` — S1 | A hung sensor can re-serve cached output without an explicit running/unknown state; nolock/nosnap mutants survive. | Shares daemon with 04/42 and registry with 42/44. **BHC-05**, before 44. |
| **BH1-09** | `bd-role-claim.sh`; `lib_remote_seat.sh` — S1 | A reap or census reportedly removes a live remote claim, making the seat unreachable through bd-say. Exact cause still needs investigation. | Bridges BHC-03 and BHC-04. Scope first; integrate after both, before 20 in the proposed train. |
| **BH1-10** | Estimated `tests/test_registry_wipe_guard.py` or rwgh test files — S1 | FIFO readers lack a timeout or reliable teardown; absent writers leave `head -c 32` blocked for hours. | Estimated test overlap with 35. **BHC-03**; confirm actual test files. |
| **BH1-11** | `bd-role-claim.sh`, `wipe_alert` — S1 | Check-then-stamp occurs outside a lock, allowing concurrent reaps to race. | Shares script with 09/35. **BHC-03**; likely test ownership also intersects 10/35. |
| **BH1-12** | `~/.local/bin/bd-pm-idle-sweep` — S1 | tmux `window_activity` measures repaint activity, so idle Claude seats appear recently active. | No reported overlap; installed artifact requires separate verification. |
| **BH1-13** | `bd-launch-role.sh`, `--brief` handling — S1 | Brief path is validated but not delivered as the seat’s first message, leaving launches without their order. | **BHC-04** with 18/31; shares launcher with 19/20/38. |
| **BH1-14** | Provisionally `bd-launch-kimi.sh` — S1 | Requested kimi builder did not seat; underlying cause is unknown. | Investigation first. If this launcher is edited, finish before 38. |
| **BH1-15** | bd-fleet-mcp plugin `cli.py`; `tests/checks.py` — S1 | Hook checks file existence before execution, rejecting commands that create the message file earlier in the same command. | Shares both files with 01. **BHC-01**. |

### BH1-16 through BH1-30

| Row | Reported files / citation | Reported mechanism or unresolved cause | Conflict and dispatch treatment |
|---|---|---|---|
| **BH1-16** | `jarvisos_backend/threat_gate.py`; `jarvisos_backend/test_dcc_f9_hardening.py` — S1/S2 | Literal command classification cannot fully observe persistent git aliases or words assembled at runtime. Accepting the limit versus broader gating is a policy decision. | Same two files as 51. **BHC-08**, after policy resolution. |
| **BH1-17** | `~/bin/bd-pool-usage-refresher.py`; `bd-kimi-usage-watch.sh` — S1 | Competing writers flap POOL_STATE between OK/UNKNOWN; an OK write masks a real 403, and partial rows publish. Installed copy has reportedly drifted. | After **BHC-06**. Compare installed and tracked sources before scope approval. |
| **BH1-18** | `bd-launch-role.sh`; `lib_remote_seat.sh` — S1 | Success output precedes confirmed claim/seat creation; rc 8 omits result/receipt, and one failed cplus path loses its warning. | **BHC-04**. Cross-conflicts with 09/20 through remote-seat library. |
| **BH1-19** | `bd-launch-role.sh`, token export/remote environment construction — S1 | OAuth token inheritance broadens exposure to descendants; shell tracing can disclose its value. | Separate secret-boundary change after **BHC-04**, before 20/38. |
| **BH1-20** | `bd-launch-role.sh`; `lib_remote_seat.sh` — S1 | Unvalidated publish configuration, local-only share fallback, and alias canonicalization can route a launch incorrectly, including to the hub. | After 19 and 09 in the recommended sequence; before 38. |
| **BH1-21** | Separate cplus repository: `patched/cplus_authorizer.py`; `patched/operations.py` — S1 | Journal-read/busy-append race, FIFO-induced hang, and overly broad OBSERVEd admission are reported. | No reported harness-file overlap. Separate install window; verify whether grant-token r2 already resolves part of it. |
| **BH1-22** | No source edit identified; release push/pin and crontab work — S1 | Landed fixes may remain absent on DOWN hosts or hosts missing scheduled jobs. | Operational track; release and host state must be established before execution. |
| **BH1-23** | `bd-worker-done.sh`; `bd-verdict-write-hook.sh`; `plugins/bd-cut-hygiene/scripts/bd-worker-done.sh` — S1 | UNKNOWN timestamp validation and verdict allowlist are incomplete; M3/M8 survive. | No reported cross-row collision. Harness script and plugin copy require synchronized ownership. |
| **BH1-24** | `bd_cost_accounting.py`; `tests/test_usage_collector_advisor.py` — S1 | Guards lack mutation coverage; labels and counters are unpinned, and pre-window placeholders are counted. | Shares accounting module with 55. **BHC-07**. |
| **BH1-25** | `bd-ast-tia`; `bd-speculative-patch`; unnamed tests — S1 | Rename-based rollback depends on directory permissions at rollback time; restore/cleanup exception paths lack coverage. | No named overlap. Enumerate tests before calling it disjoint. |
| **BH1-26** | `bd-fuzz-user-verify.py`; `INSTALL.md` — S1 | Over-strict ancestor symlink handling, non-string TypeError, missing relative-path coverage, and lstat TOCTOU are reported. | No named overlap. Include documentation and test paths in scope. |
| **BH1-27** | Precut kit `members.tsv`; unnamed kit runner — S1 | Opt-in tests skip without the enabling environment, and skips are interpreted as green. | Shared infrastructure; possible interaction with 29. Resolve runner/test paths before batching. |
| **BH1-28** | `bd-auto-merge.sh` — S1 | Train-name normalization maps `train/a/b` and `train/a-b` to the same HOLD filename. | No reported overlap. Parallel candidate. |
| **BH1-29** | `tests/test_backlog_*.py`; `tests/test_bdt_sensor_gitstate*.py`; `tests/test_band_remote_roles_source.py`; `tests/test_h516_*.sh` — S1 | Missing fixtures and assertions leave parsing, timing bounds, and environment cleanup unpinned. | Expand globs. Soft dependencies on 27 and 30; no reported identical file with 30. |
| **BH1-30** | `bd-row-audit.py`; `bd-band-remote.sh` — S1 | Band-host execution lacks the harness-root environment needed to locate files. | No reported identical-file overlap; coordinate behavior expectations with 29. |

### BH1-31 through BH1-45

| Row | Reported files / citation | Reported mechanism or unresolved cause | Conflict and dispatch treatment |
|---|---|---|---|
| **BH1-31** | `bd-launch-role.sh`; `bd-launch-codex-role.sh` — S1 | Claim notes bypass canonical mailbox resolution; `kill_own_failed` exists but is never invoked. | **BHC-04**; both launcher surfaces intersect 38. |
| **BH1-32** | `tests/test_batching_safety.py`; `oneshot/bd-unpark-after-355.sh`; `bd-root-ref-resolver.py` — S1 | Dead-root references escaped prior ownership; resolver global-name matching is too broad. | No reported overlap. Parallel candidate. |
| **BH1-33** | Estimated `tests/test_sec2_job_url_token*.py`; `tests/test_v3_66_729*.py`; `tests/test_v3_66_1183*.py`; `tests/source_window_hashes.json`; other unidentified failing tests — S2 | Persistent BASE failures are routinely excused, reducing later gates’ ability to detect regressions. Individual failure causes are unknown. | First acceptance step on BD train. Expand inventory; no complete disjointness claim yet. |
| **BH1-34** | `outbound_guard.py`; `deep_http.py`; `session_keeper.py` — S2 | Per-site proxy handling lacks an agreed SSRF/LAN opt-in policy. | Policy first; conservatively precedes 46 on shared `session_keeper.py`. |
| **BH1-35** | `bd-role-claim.sh`; `tests/test_registry_wipe_guard.py` — S1 | Multiple path, logging, hardlink, and temporary-file race defects remain. Historical wipe writer is still unidentified. | **BHC-03** with 10/11; before 09. Coordinate fixtures with 36. |
| **BH1-36** | `tests/lib_live_registry_guard.sh`; nine unnamed fakehub tests — S1 | DROP attribution, rotated/regrown log detection, and whitespace handling leave fixture guard gaps. | No reported exact overlap with 35, but enumerate all nine tests and coordinate registry semantics. |
| **BH1-37** | `bd-retire-seat.sh`; `tests/test_retire_seat_rc8_kept.py`; probe `ev/test_rsk_probe.py` — S1 | Missing prefix-seat control lets a whitespace mutant survive; retained-seat output still describes failed relaunch. | No reported overlap with 18 despite shared rc8 subject. Parallel candidate. |
| **BH1-38** | `bd-launch-role.sh`; `bd-launch-kimi.sh`; `bd-launch-agy.sh`; `bd-launch-codex-role.sh` — S1 | Locale-sensitive validation and unescaped role/name text leave input and log-handling gaps. | Final launcher change, after 20 and resolved 14. |
| **BH1-39** | `bd-local-seat-feeder`; `~/bin/bd-pick-host` — S1 | Queue failures are masked; timeout stops the loop; ssh option separation and cache isolation are weak; host selection ignores a required mount. | No reported overlap. Installed helper identity needs confirmation. |
| **BH1-40** | `bd-usage-probe.sh`; `systemd/bd-usage-oneshot.service`; refresher; staged `install.sh` — S1 | Exit-status handling, lock-path globbing, configuration documentation, and dry-run compilation behavior are inconsistent. | **BHC-06** with 07; before 17. Resolve staged installer path. |
| **BH1-41** | Unknown writer; observed targets `~/.claude-{a..d}/.claude.json` — S1 | Rewrites removed `cachedUsageUtilization`, leaving usage UNKNOWN. Writer attribution is missing. | Investigation track. Potential conflict with 07/17/40 cannot be excluded. |
| **BH1-42** | `bd-telemetry-daemon.py`; `bd_telemetry/registry.py`; `tests/test_telemetry_registry_live.py`; `bd-telemetry-guard.py` — S1 | Alert-clearing semantics, alert bookkeeping order, crash litter, and durability gaps remain; deployed daemon may lack its package. | **BHC-05**; isolate guard subtask if desired with explicit ownership. Deployment parity is a prerequisite. |
| **BH1-43** | `bd-optimistic-merge.sh` for EDGE-B; otherwise operational items — S1 | Outstanding operational corrections include respawn behavior and undeployed enforcement. | Separate EDGE-B code scope from operational verification. No reported file overlap. |
| **BH1-44** | `bd_telemetry/f3_bridge.py`; `bd_telemetry/registry.py`; `schema_v1.json` — S1 | Sensor promotion and live-store wiring remain owed; sentinel values require unknown-state mapping. | Feature follows **BHC-05**, sharing registry with 08/42. |
| **BH1-45** | `watch_folder.py` — S2 | Exception handling around `bulk_priority` leaves failure-reporting work outside the earlier cut’s scope. | No reported overlap. BD parallel candidate after 33. |

### BH1-46 through BH1-55

| Row | Reported files / citation | Reported mechanism or unresolved cause | Conflict and dispatch treatment |
|---|---|---|---|
| **BH1-46** | `cloak.py`; estimated `session_keeper.py`; unnamed tests — S2 | Cross-site bare-URL dismiss can evict another site’s pending state and clearance; failed-delete preservation lacks a test. | Treat as conflicting with 34; stack after it unless final scope proves otherwise. |
| **BH1-47** | Unnamed site-templates module and secret backend — S2 | Replacement saves orphan previous vault keys; plaintext/locked backends drop a typed password while setting a flag. | **Unknown file scope.** Do not certify parallel independence until both modules and tests are named. |
| **BH1-48** | `app_pair.py`; `app.py`, `_check_csrf` — S2 | Headerless top-level GETs pass the CSRF check and can mint or evict pairing codes; reported impact is availability. | No demonstrated overlap with 49. Confirm whether DECIDED OPEN authorizes a change before briefing implementation. |
| **BH1-49** | `login_impl/_common.py`; `synthetic_input_scheduler.py` — S2 | Focus may change between origin validation and global keyboard input, sending a character to the wrong origin. | No reported file collision. Locator-bound typing is the proposed approach, not a verified remedy here. |
| **BH1-50** | `cockpit_frontend/app/api/bd/[...path]/route.ts`; `cockpit_frontend/app/api/bd/ping/route.ts` — S2 | Redirects are relayed/followed, response-body reading sits outside error handling, and ping omits Bearer authentication. | Frontend paths are disjoint from BHC-08 backend paths. Concurrent candidate. |
| **BH1-51** | `jarvisos_backend/threat_gate.py`; `jarvisos_backend/test_dcc_f9_hardening.py` — S2 | Verb matching scans later argument words, causing benign-command overblocking; destructive forms and parser controls remain uncovered. | **BHC-08** with 16. One owner for parser, verb table, and paired controls. |
| **BH1-52** | No code files; cockpit deployment — S2 | Deployed build may lag eight landings associated with reported main `6899fda`. | Last cockpit step after applicable code acceptance. Verify actual intended main at execution time. |
| **BH1-53** | `bd-claim-object.sh`, A3 guard — S1 | Missing or failing `realpath` makes the guard fail open; refusal wording is inaccurate. | **BHC-02** with 02. |
| **BH1-54** | `bd-backlog-append.sh`; unidentified backlog rewriter — S1 | A later rewrite drops prior appended rows; append/rewrite coordination is missing or ineffective. | Investigation first. Unknown rewriter prevents a complete collision inventory. |
| **BH1-55** | `bd_cost_accounting.py`; `bd_telemetry/sensors/bd_cost_accounting.py` — S1 | Missing receipt propagation or a `None` host key leaves the sensor unknown and hub status WARN. Exact branch is unresolved. | **BHC-07** with 24; verify receipt diagnosis and resulting live state. |

## 4. Composite bundles and why they belong together

The eight reported BHC bundles account for **20 distinct rows**. The other **35 rows** remain standalone candidates, sequential followers, investigations, or operational work.

| Bundle | Rows | Shared ownership boundary | Required acceptance content |
|---|---|---|---|
| **BHC-01** | 01, 15 | Plugin parser and `tests/checks.py` | Nested-command, escaping, text-fidelity, and create-then-send cases |
| **BHC-02** | 02, 53 | `bd-claim-object.sh` | Bounded search plus missing/erroring `realpath` controls |
| **BHC-03** | 10, 11, 35 | Registry script and wipe-guard tests | Concurrent reap, FIFO cleanup, path/hardlink/race regressions |
| **BHC-04** | 13, 18, 31 | Main launcher, remote-seat library, Codex launcher | Brief delivery, truthful terminal result, mailbox routing, failed-claim cleanup |
| **BHC-05** | 04, 08, 42 | Daemon, registry, associated tests | Stderr fault, hung sensor, outage/alert transitions, deployed package parity |
| **BHC-06** | 07, 40 | Usage probe, refresher, service/install surfaces | Invalid timestamps, knob parsing, exit status, locking, genuine dry-run behavior |
| **BHC-07** | 24, 55 | Cost-accounting implementation | Guard mutation coverage and receipt/host propagation |
| **BHC-08** | 16, 51 | Threat-gate implementation and hardening tests | Agreed alias policy, benign/destructive paired controls, parser edges, latency |

A bundle is **one integration work unit**, not permission to send all member rows to independent writers simultaneously. If internal work is divided, shared-file ownership must still remain exclusive.

BHC-04 benefits particularly from composite ownership because brief delivery, claim failure, result emission, and cleanup describe one launch lifecycle. Fixing these independently risks producing contradictory terminal states.

BHC-05 similarly spans one observation lifecycle: sensor execution, cached state, outage classification, alert persistence, and deployed imports. A local test pass cannot compensate for a deployed daemon that cannot import its package.

BHC-08 needs joint treatment because reducing overblocking and adding destructive-command coverage modify the same parser contract. Testing only newly blocked commands could preserve or worsen false positives.

## 5. Conflict graph and safe stacking order

### Launcher/registry train

The following is a **recommended conservative ordering**, rather than proof that every edge is a strict semantic dependency:

```text
BHC-03 (10 + 11 + 35) ───┐
                         ├──> BH1-09 ──────────┐
BHC-04 (13 + 18 + 31) ───┤                    ├──> BH1-20 ───> BH1-38
                         └──> BH1-19 ──────────┘                  ^
                                                                  |
BH1-14 investigation and any confirmed launcher correction ───────┘
```

BHC-03 and BHC-04 have disjoint reported file inventories and can begin concurrently. BH1-09 then inherits both registry and remote-seat-library changes. BH1-19 can run alongside a scoped BH1-09 because its reported file is `bd-launch-role.sh`, while BH1-09 names `bd-role-claim.sh` and `lib_remote_seat.sh`.

BH1-20 joins those branches because it edits both the main launcher and remote-seat library. BH1-38 comes last because its four-launcher scope intersects multiple earlier corrections.

BH1-14 is not ready for an unconditional implementation slot. Its investigation may establish a different responsible file, in which case the graph must be updated.

### Other trains

| Train | Recommended order | Reason |
|---|---|---|
| Telemetry | BHC-05 → 44 | Shared `bd_telemetry/registry.py`; stabilize behavior before feature promotion |
| Usage | BHC-06 → 17 | Shared refresher; establish source/install identity before writer-integrity work |
| BD baseline | 33 → later BD acceptance | Remove or explicitly account for excused failures before relying on downstream gates |
| Proxy/session | Policy decision → 34 → 46 | Estimated shared `session_keeper.py` and related session semantics |
| Cockpit | BHC-08 and 50 concurrently → 52 | Backend/frontend files are disjoint; deployment follows accepted changes |

BH1-41 may alter the usage train after writer attribution. BH1-54 may reveal a conflict with backlog tooling or tests. Neither should be forced into a supposedly independent implementation wave prematurely.

## 6. Proposed dispatch waves

These waves describe **file-compatible candidates**, not fleet capacity or authorization. Every unit still needs a concrete edit inventory, including tests and deployment artifacts.

### Wave A: Initial composite work and explicitly scoped independent work

The following seven bundles have mutually disjoint reported paths:

**BHC-01, BHC-02, BHC-03, BHC-04, BHC-05, BHC-06, BHC-07.**

Their STEP0 conditions still apply. In particular, BHC-05 must account for the reported daemon install problem, and BHC-06 must identify the tracked refresher and staged installer.

Additional standalone candidates are:

**BH1-03, 05, 06, 12, 23, 25, 26, 28, 30, 32, 37, 39**, plus the **EDGE-B code portion of 43**.

These have no reported exact cross-row collision, but broad or unnamed test inventories need resolution. BH1-05 should remain one owned scope until its prompt list is enumerated.

In separate repository/frontend scopes, **BH1-21, BH1-33, and BH1-50** can also begin once their prerequisites and actual files are established. **BHC-08** can join when its policy question is resolved.

### Wave B: First sequential followers

| Work unit | Required predecessor | Parallelism supported by reported files |
|---|---|---|
| **BH1-09** | BHC-03 and BHC-04; investigation completed | Can accompany 19, 17, and 44 |
| **BH1-19** | BHC-04 | Can accompany scoped 09 |
| **BH1-17** | BHC-06 | Separate usage files from launcher and telemetry followers |
| **BH1-44** | BHC-05 | Separate telemetry files from launcher and usage followers |
| **BH1-34** | Policy decision and BD baseline acceptance | Can accompany disjoint BD rows |
| **BH1-45, 48, 49** | BH1-33 acceptance; row-specific disposition resolved | Mutually disjoint reported production files |

BH1-47 may join the BD group only after its missing module names and tests are identified. S2’s broad recommendation to run it with 45/48/49 is therefore treated as conditional.

### Wave C: Shared-file joins

- **BH1-20**, after BH1-09 and BH1-19.
- **BH1-46**, after BH1-34 if the `session_keeper.py` overlap remains.

These two units are mutually disjoint on the supplied paths.

### Wave D: Launcher completion

- **BH1-38**, after BH1-20 and completion or disposition of BH1-14.

This placement ensures the four-launcher validation change incorporates earlier brief, cleanup, token, and routing work.

### Investigation and scope-resolution track

**BH1-14, 41, and 54** can undergo read-only investigation concurrently with implementation waves, provided their operational probes do not interfere with shared state. **BH1-09** also requires investigation before its implementation slot.

Resolve the uncertain inventories for **BH1-27, 29, 36, and 47** on this track. These rows are not being deferred as unimportant; their unknown files prevent reliable collision classification.

### Operational completion track

**BH1-22**, the operational portion of **BH1-43**, and **BH1-52** require verified release state and installation targets. BH1-52 is the final cockpit step. Its reported historical reference, `6899fda`, is a comparison starting point, not evidence of the correct deployment target at a later execution time.

## 7. Root-cause patterns and hidden coupling

### Inconsistent lifecycle state

BH1-18 reports success before confirmation; BH1-31 leaves failure cleanup unused; BH1-37 describes a retained seat as a failed relaunch. These defects share a lifecycle-state theme, but only 18 and 31 share launcher files. BH1-37 can therefore proceed independently while its output contract is coordinated.

### Shared state without adequate coordination

BH1-11’s check-then-stamp race, BH1-17’s competing writers, and BH1-54’s lost appends all involve shared mutable state. Their concrete fixes need different evidence: lock boundaries, authoritative writer selection, and append/rewrite serialization respectively. A generic “add locking” prescription would be premature, especially where the writer remains unidentified.

### Parsing that loses execution context

BH1-01 and BH1-15 show hook parsing detached from shell structure or execution order. BH1-51 shows command classification detached from the git verb position. BH1-16 identifies limits of literal inspection. These belong to separate files and can be developed concurrently, but each needs controls showing that the corrected gate preserves legitimate behavior.

### Unknown state presented inadequately

BH1-03 lacks an incomplete-census marker; BH1-08 re-serves a stale sensor result; BH1-55 leaves an unresolved receipt/host condition; BH1-17 can replace a genuine error with OK. Their common requirement is truthful state reporting, but the reports do not establish a shared implementation file across all four.

### Source and deployed artifact drift

BH1-17, 23, 39, 40, 42, and 52 involve installed copies, mirrored scripts, helpers, or deployment state. File-disjoint authorship does not guarantee independent installation. Two accepted changes can still overwrite the same installed artifact if deployment ownership is unclear.

### Test infrastructure as a shared dependency

BH1-27 changes whether skips count as success. BH1-33 changes the trusted BD baseline. BH1-36 changes registry guard fixtures. These can affect interpretation of many other rows without editing their production files. Acceptance should record the gate and fixture revision used so later results remain comparable.

## 8. Verification requirements for dispatch briefs

Each brief should carry the following compact evidence:

| Required field | Purpose |
|---|---|
| Exact repository and base revision | Distinguishes identically named files across repositories and fixes the comparison point |
| Enumerated production, test, fixture, and install files | Converts provisional overlap into a reviewable ownership boundary |
| Predecessor bundle or row | Ensures a follower starts from accepted shared-file changes |
| STEP0 finding or policy disposition | Prevents implementing an obsolete premise or unresolved policy choice |
| Gate content and expected result | Makes acceptance specific without inventing `BD_GATE_SCOPE` syntax |
| Installed artifact mapping, where applicable | Connects tested source to the file actually executed |
| Scope-expansion notification | Forces the conflict matrix to be updated when new shared files appear |

Several checks are especially important:

- **Registry:** concurrent reap behavior and absence of leaked FIFO readers.
- **Launcher:** one truthful final result, confirmed brief delivery, and complete failure cleanup.
- **Token handling:** inspect trace and environment behavior without publishing secret values.
- **Telemetry:** closed-stderr handling, hung-sensor status, alert transitions, and installed import parity.
- **Usage:** invalid seed values, dry-run side effects, competing writers, and real error preservation.
- **Threat gate:** benign/destructive paired controls and the reported ≤50 ms latency target.
- **Session clearance:** cross-site dismiss isolation and preservation after a failed delete.
- **Cockpit deployment:** deployed revision comparison and a responding health endpoint.

The matrix covers **all 55 rows**, preserves every reported hard conflict, and separates unresolved scopes from independent candidates. The immediate batching decision for `bd-pm-B` is to use composite ownership for BHC-01 through BHC-08, enforce the launcher/registry join before BH1-20, and keep unknown-writer rows outside any claim of verified zero-collision dispatch.

---

# Appendix: Specialist Subagent Read-Only Code Inspection Reports

## Investigator Report 1: Harness (Lane H) File-Overlap Extractor (`claude-sonnet-5-5-high`)

# Lane H file-overlap dossier: `Codex-Astra-Master-Matrix-S1`

Read-only. I called no tools and modified nothing.

**Basis and limits**
- The only input was `<SOURCE_CONTEXT>`, which holds the row table, the est. file lists and the overlap section. It has no source code, blame output or briefs.
- **Line numbers:** the only one the context supplies is BH1-04 (`bd-telemetry-daemon.py` `main()` :342/:345). Every other row is marked `COULD NOT LOOK (lines)`.
- **Root-cause mechanism:** it comes from each row's stated defect and evidence text. It is the mechanism as reported, not a code-verified one. Rows with a STEP0 need that check first.
- **Assigned rows:** none were named, so I covered all Lane H rows, BH1-01 to 15 and 17 to 32, 35 to 44 and 53 to 55. BH1-16 is included because the context lists it with the harness files, although its Lane column says BD-cockpit. BD rows (33, 34, 45 to 52) are out of scope except where they share a file.
- **`BD_GATE_SCOPE`:** the context does not define its value grammar. Scopes below are file or test-group scopes, and the exact token is TBD at brief time.

## 1. Per-row dossier

### Launcher family (`bd-launch-role.sh` and friends)

| Row | (1) Files / lines | (2) Root-cause mechanism | (3) Overlap | (4) `BD_GATE_SCOPE` |
|---|---|---|---|---|
| **BH1-13** launcher-brief-not-delivered | `bd-launch-role.sh`, the `--brief` handling (lines `COULD NOT LOOK`). | `--brief` is only validated, so the path is never sent as the seat's first message. Codex OGR3X started with no order and hit BLOCKED. | `bd-launch-role.sh` with 18, 19, 20, 31, 38. BHC-04 member. | launcher tests (brief and bootstrap path), plus a live seat-launch smoke. |
| **BH1-18** launcher-result-line-consistency | `bd-launch-role.sh` and `lib_remote_seat.sh`, the LAUNCHED/RESULT emit sites. | LAUNCHED is printed before the claim and seat are confirmed. A `<C+>` host then yields RESULT rc=7 with no seat. rc 8 writes no RESULT or receipt, and a cplus UNCONFIRMED+FAILED run drops its WARN line. | `bd-launch-role.sh` (13, 19, 20, 31, 38). `lib_remote_seat.sh` (09, 20). BHC-04 member. | launcher result/receipt tests (lcf, launcher-claim-fail-uniform), cplus launch path. |
| **BH1-19** launcher-token-exposure | `bd-launch-role.sh`, the `CLAUDE_CODE_OAUTH_TOKEN` export or remote-env construction. | The token is exported into the target environment, so every claude descendant inherits it (ADV1). `set -x` would trace the token value (ADV2). | `bd-launch-role.sh` (6 rows). It is a secret boundary, so keep it separate from BHC-04. Sequential: BHC-04 → 19. | launcher token and env tests (launcher-1year-token), plus a `set -x` trace-leak probe. |
| **BH1-20** lvc-cplus-routing-advisories | `bd-launch-role.sh` and `lib_remote_seat.sh`: `BD_CPLUS_PUBLISH` read, the no-cplus-share FALLBACK, the `_canon` vs `BD_HUB_HOST` check. | `BD_CPLUS_PUBLISH` is used verbatim (A1). The FALLBACK checks for a cplus share on the launching box only (A2). An alias whose MOUNT row names the hub IP launches on the hub because `_canon == BD_HUB_HOST` is not refused (A3). | `bd-launch-role.sh` (6 rows). `lib_remote_seat.sh` (09, 18). Sequential after 19. | launch-via-cplus tests (lvc r3), hub-by-alias rc6, alias known_hosts edge. |
| **BH1-31** t30-claim-note-inbox | `bd-launch-role.sh` (claim note), `bd-launch-codex-role.sh` (`kill_own_failed`). | The claim note writes `inbox/$BD_SEAT` directly instead of using bd-say's `mailbox_for` (A1). `kill_own_failed` is defined but never called (A3). | `bd-launch-role.sh` (13, 18, 19, 20, 38). `bd-launch-codex-role.sh` (38). BHC-04 member. | T30 claim-fail tests, inbox path test, codex-role claim-fail. |
| **BH1-38** seat-name-guard-residuals | `bd-launch-role.sh`, `bd-launch-kimi.sh`, `bd-launch-agy.sh`, `bd-launch-codex-role.sh`, the NAME validators and note body. | `[A-Za-z0-9]` admits non-ASCII letters under a UTF-8 locale (A1). ROLE is unescaped in the note body (SEC A2). The NAME-invalid log text is unsanitised (A3). | The only row touching 4 launchers. It overlaps `bd-launch-role.sh` (13, 18, 19, 20, 31), `bd-launch-codex-role.sh` (31) and `bd-launch-kimi.sh` (14). Last in the chain. | launcher-seat-name-guard tests under `LC_ALL=C` and a UTF-8 locale, across all 4 launchers. |
| **BH1-14** kimi-builder-launch | `bd-launch-kimi.sh` (after investigation). | Unknown. F2-D was asked twice and never seated the kimi builder. Cause not in evidence: STEP0 is to reproduce the launch path. | `bd-launch-kimi.sh` (38). Investigation, no file yet. Must precede 38. | kimi launcher dry-run and launch smoke. |
| **BH1-09** worker-claim-lost-remote | `bd-role-claim.sh` (reap path) and `lib_remote_seat.sh`. | A reap or census drops a live remote-host claim (TRLN1-A on .186 at 17:45Z). The seat becomes unreachable by bd-say, so retire needs a seat-written handoff. Related fix: lvc r2 role-reap drops C+ claim (3ef66665). | `bd-role-claim.sh` (11, 35). `lib_remote_seat.sh` (18, 20). M investigation, kept out of BHC-03. | role-claim and reap tests (remote host census), plus a registry-wipe-guard regression. |
| **BH1-37** retire-seat-rc8-residuals | `bd-retire-seat.sh`, `tests/test_retire_seat_rc8_kept.py`. | No prefix-seat KEPT test, so the trailing-space mutant survives. The ledger row and RETIRED line still print `relaunch=<pool>-fail(rc=8)` for a kept seat. | Single file. It is thematically adjacent to 18 (rc8) but shares no file. | `test_retire_seat_rc8_kept.py`, plus the lens probe `ev/test_rsk_probe.py`. |

### Registry and claim family

| Row | (1) Files / lines | (2) Root-cause mechanism | (3) Overlap | (4) `BD_GATE_SCOPE` |
|---|---|---|---|---|
| **BH1-11** rwgh-wipe-alert-lock | `bd-role-claim.sh`, the `wipe_alert` check-then-stamp. | The check and the stamp are not under a lock, so concurrent reaps race. | `bd-role-claim.sh` (09, 35). Excluded from 35 by agreement. BHC-03 member. | `tests/test_registry_wipe_guard.py`, concurrent-reap test. |
| **BH1-10** rwgh-fifo-test-leak | rwg-hardening FIFO-stamp tests, in `tests/test_registry_wipe_guard.py` or the rwgh tests (est.). | The FIFO tests start `head -c 32 <tmp>/reg.tsv.reap-refused` as a reader. If no writer opens the FIFO the reader blocks, and there is no timeout or teardown. 3 leaked on .149 for 4 to 5 h. | `tests/test_registry_wipe_guard.py` (35). BHC-03 member. | the FIFO subset of `test_registry_wipe_guard.py`, then check for no leaked `head -c 32` on the host. |
| **BH1-35** registry-wipe-guard-residuals | `bd-role-claim.sh`, `tests/test_registry_wipe_guard.py`, multiple guard sites. | A bundle of 12+ small defects, for example: the newline-name rotation `rm` is cwd-relative, a `BD_SEAT` newline reaches `alerts.log`, a hardlink passes FIX4, and the mktemp→cat swap window. The 11:29:56Z wipe writer was never identified. | `bd-role-claim.sh` (09, 11). Test file (10). Also adjacent to BH1-36 via the fakehub tests. | `test_registry_wipe_guard.py` plus rwg-hardening mutants M2/M11. |
| **BH1-36** fakehub-lrg-residuals | `tests/lib_live_registry_guard.sh` and 9 fakehub tests. | Fixture and guard gaps: an in-window DROP is excused without a caller id, `alerts.log` rotate+regrow misses a leak (inode compare), and `hostname -I` leaves a trailing space. | Single group. It has no shared file with 35, but they touch the same live-registry-guard domain. | the 9 fakehub tests plus the lrg-alerts-log test. |
| **BH1-02** claim-object-homewide-find | `bd-claim-object.sh`, the `take` path. | `take` runs a home-wide `find`, which hit NFS D-state for 27 min. It needs bounding to cut roots. | `bd-claim-object.sh` (53). BHC-02 member. | claim-object tests, with an NFS-slow-find timing probe. |
| **BH1-53** a3-claim-guard-realpath-failopen | `bd-claim-object.sh`, the A3 guard. | The A3 guard fails open when `realpath` is absent or errors (the base is identical). The REFUSED text says "mounted" (A2). | `bd-claim-object.sh` (02). BHC-02 member. | a3-hotfix tests, with a `realpath`-absent / realpath-error fault injection. |

### Telemetry, usage and cost

| Row | (1) Files / lines | (2) Root-cause mechanism | (3) Overlap | (4) `BD_GATE_SCOPE` |
|---|---|---|---|---|
| **BH1-04** launch-base-loop-stderr | `bd-telemetry-daemon.py` `main()` :342/:345. The register text wrongly says `bd-launch-role.sh`. | The base-loop handler writes to stderr, and an unwritable stderr makes it die with rc 120. STEP0: check ORDER-2331 r5 first. | `bd-telemetry-daemon.py` (08, 42). BHC-05 member. | `test_telemetry_*` daemon main-loop tests, with a closed-stderr fault injection. |
| **BH1-08** telemetry-hung-sensor | `bd-telemetry-daemon.py` and `bd_telemetry/registry.py`. | A hung sensor with a cached result re-serves the old reading, so staleness is the only signal and there is no "still running" unknown (A3). The nolock/nosnap mutants survive (A2). | Daemon (04, 42). `registry.py` (42, 44). BHC-05 member. | `tests/test_telemetry_registry_live.py`, hung-sensor test. |
| **BH1-42** telemetry-daemon-residuals | `bd-telemetry-daemon.py`, `bd_telemetry/registry.py`, `tests/test_telemetry_registry_live.py`, `bd-telemetry-guard.py` (T21 A2). | Examples: CLEARED is logged for alerted sections on a registry-level outage, `_A3_ALERTED` is set before the `alerts.log` append, there is temp litter after SIGKILL, and there is no parent-dir fsync. DEPLOY-BLOCKER: the hub `~/bin` daemon is a copy without `bd_telemetry`. STEP0 is to verify a symlink or install exists. | Daemon (04, 08). `registry.py` (08, 44). BHC-05 member, with `bd-telemetry-guard.py` split out. | `test_telemetry_registry_live.py`, plus a deploy-parity check of the hub copy. |
| **BH1-44** t09-f3-bridge-promotion | `bd_telemetry/f3_bridge.py`, `bd_telemetry/registry.py`, `schema_v1.json`. | Promotion and live-store wiring of the f3_bridge sensor are owed. A `-` on top_io/flags maps to unknown. | `registry.py` (08, 42). It is a feature row, so it goes after BHC-05. | T09 sensor tests and schema validation. |
| **BH1-55** cost-sensor-standing-warn | `bd_cost_accounting.py`, `bd_telemetry/sensors/bd_cost_accounting.py`. | The sensor reports "response host has no collection receipt: [None]", an unknown that leaves the hub at STATUS WARN. A receipt is not recorded or carried, or the host key is None. | `bd_cost_accounting.py` (24). BHC-07 member. | cost-accounting sensor tests, then a live check that STATUS clears. |
| **BH1-24** t16-cost-accounting-coverage | `bd_cost_accounting.py`, `tests/test_usage_collector_advisor.py`. | 10 surviving mutants, because the new guards are untested. Default-0 fields are pre-existing. The PARTIAL label should be COULD NOT LOOK, `synthetic_zero_skipped=0` is unpinned, and pre-window placeholders are counted. | `bd_cost_accounting.py` (55). BHC-07 member. | `test_usage_collector_advisor.py` plus a mutation run. |
| **BH1-03** usage-csv-partial | `bd-usage-monitor.sh`, `bd_telemetry/sensors/bd_usage_monitor.py`, `bd-usage-forecaster.py`. | The CSV has no PARTIAL column or marker when a census is incomplete, so consumers cannot tell. | Single row (est.). | usage-monitor and forecaster tests. |
| **BH1-07** pool-usage-refresher-residuals | `bd-usage-probe.sh`, `~/bin/bd-pool-usage-refresher.py` (staged install). | The A1 seed guard keys on `fetched_ms`, so a non-number passes as "no reading". The `BD_*` comma form ("A,B") reads none and is undocumented. STEP0: confirm the pool-A 403/429 cause is gone after O3394. | `bd-usage-probe.sh` (40). Refresher (17, 40). BHC-06 member. | usage-probe and refresher tests, with `fetched_ms` and comma-knob cases. |
| **BH1-40** usage-oneshot-residuals | `bd-usage-probe.sh`, `systemd/bd-usage-oneshot.service`, the refresher, staged `install.sh`. | Examples: the unit lacks `SuccessExitStatus=75` (so rc 1 each tick), `LOCK_WAIT=0` is undocumented, `find -lname` treats the lock path as a glob, and install `py_compile` writes `__pycache__` before DRY_RUN. | `bd-usage-probe.sh` (07). Refresher (07, 17). BHC-06 member. | usage-single-turn, usage-oneshot and refresher-n1 tests, plus an `install.sh` DRY_RUN. |
| **BH1-17** pool-state-writer-integrity | `~/bin/bd-pool-usage-refresher.py`, `bd-kimi-usage-watch.sh`. | Two writers flap the grok/kimi POOL_STATE rows OK/UNKNOWN every 4 to 5 min. The kimi OK write hides a real 403, and partial rows are published. The live `~/bin` copy drifted outside the harness. STEP0 is to diff live against tracked. | Refresher (07, 40). Sequential after BHC-06. Launch-gate truth source. | refresher and kimi-usage-watch tests, with a live-vs-tracked diff. |
| **BH1-41** hub-claude-json-cache-rewrite | Investigation, no file (writer unknown). | Around 18:06:43Z, hub `~/.claude-{a..d}/.claude.json` was rewritten without `cachedUsageUtilization`. The cache files are shared and usage went UNKNOWN. | Related to 07/40 (usage cache), no shared file. | none until the writer is found (audit-log or inotify evidence). |

### Gates, hooks, ops, misc

| Row | (1) Files / lines | (2) Root-cause mechanism | (3) Overlap | (4) `BD_GATE_SCOPE` |
|---|---|---|---|---|
| **BH1-01** say-gate-preexisting | plugin bd-fleet-mcp `cli.py`, `tests/checks.py` (the PreToolUse say gate). | The gate's command parser misses nested `bash -c '$MSG'` and escaped `\<` `\>` redirect words (A1/A2). Text fidelity is also a gap (A3). The `bd-say.sh` runtime re-check mitigates it. | `cli.py` and `checks.py` (15). BHC-01 member. | bd-fleet-mcp `checks.py` say-gate suite. |
| **BH1-15** say-hook-path-precheck | plugin bd-fleet-mcp `cli.py`, `tests/checks.py`. | The hook runs before the command, so `path_missing` rejects a file the same command creates earlier. About 10 refusals. | `cli.py` and `checks.py` (01). BHC-01 member. | bd-fleet-mcp `checks.py`, with a create-then-say fixture. |
| **BH1-06** supply-guard-closed-markers | `bd-supply-guard.sh`. | The OWED scan does not read CLOSED-*/LANDED-* markers, so T30/r3 gives a false OWED every 30 min. | Single row. | supply-guard tests with marker fixtures. |
| **BH1-12** idle-sweep-activity-signal | `~/.local/bin/bd-pm-idle-sweep`, outside the repo. | The sweep uses tmux `window_activity`, and Claude TUIs repaint, so idle seats never age (66/66 under 30 min). | Single row. Installed path, not a repo file. | idle-sweep fixture tests (pane-content parse), plus a dry-run against live panes. |
| **BH1-05** role-prompt-trim-wave | ~100 role prompt files, `tests/test_role_prompt_budget.py`. | The prompts exceed the 800-token budget (review-security 1240, status 1072, and others). | Many files, but each is its own file, so no train conflict by file. | `test_role_prompt_budget.py`. |
| **BH1-23** worker-done-unknown-residuals | `bd-worker-done.sh`, `bd-verdict-write-hook.sh`, plugin copy `plugins/bd-cut-hygiene/scripts/bd-worker-done.sh`. | The M3/M8 mutants survive. UNKNOWN WRITTEN-AT has no calendar check. The verdict-write-hook allowlist lacks `VERDICT: UNKNOWN`. STEP0: verify the plugin byte copy is in sync. | The harness file and plugin copy must stay byte-identical. No other BH1 row touches them. | worker-done-unknown tests, plus a harness-vs-plugin byte compare. |
| **BH1-25** ast-tia-rollback-coverage | `bd-ast-tia`, `bd-speculative-patch`, tests. | Rollback-by-rename needs directory write access at rollback time (a race, A1). The except-path restore and temp cleanup are untested. | Single row. | the ast-tia tool tests. |
| **BH1-26** fuzz-verify-residuals | `bd-fuzz-user-verify.py`, `INSTALL.md`. | Any symlink ancestor gives COULD NOT LOOK (over-strict). `INSTALL.md` cites a CANDIDATE line. A non-str path gives TypeError. The relative `..` cases are untested and the lstat walk is open to TOCTOU. | Single row. | fuzz-literal and fuzz-read-trusted-dotdot tests. |
| **BH1-27** kit-optin-tests-skip-as-green | precut kit `members.tsv`, kit runner. | Opt-in tests SKIP unless an env var is set, and a skip is read as green. | Kit files, which are shared infrastructure. Note: it can interact with 29. | a kit-run verification that opt-in tests execute (fail on skip). |
| **BH1-28** automerge-tnf-collision | `bd-auto-merge.sh`. | The TNF mapping collapses `train/a/b` and `train/a-b` to one name, so the HOLD file is shared and BLOCKED/reason are ambiguous. | Single row. | auto-merge TNF tests (slash TN). |
| **BH1-29** harness-coverage-debt-1019 | `tests/test_backlog_*.py`, `tests/test_bdt_sensor_gitstate*.py`, `tests/test_band_remote_roles_source.py`, `tests/test_h516_*.sh`. | Test-only gaps: no fixture for a claim-dir subdir or lowercase closed-x, no T03 parser rows, no test pinning the 12 s hub bound, and h516 does not unset `BD_SEAT`. | Test files only. Same subject as 30 (`bd-band-remote.sh`) but a different file. | the named test files. |
| **BH1-30** bh-drain-band-env | `bd-row-audit.py`, `bd-band-remote.sh`. | `bd-row-audit.py` raises FileNotFoundError on band hosts. The band needs `BD_HARNESS_HOME` set to the tree. | `bd-band-remote.sh` (30 only). | a band-host row-audit run and the band-remote tests. |
| **BH1-32** dead-root-refs-outside-owns | `tests/test_batching_safety.py`, `oneshot/bd-unpark-after-355.sh`, `bd-root-ref-resolver.py`. | Dead-root references were left outside OWNS. The resolver's global name match is too broad. | Single row. | `test_batching_safety.py` and the resolver tests. |
| **BH1-39** local-seat-feeder-residuals | `bd-local-seat-feeder`, `~/bin/bd-pick-host`. | `queue_take` filesystem failure is masked. A lock timeout ends the loop. ssh lacks `--`. `MYPY_CACHE` is a predictable shared `/tmp` path. `bd-pick-host` picks .149 without the NFS review mount. | Single row. | local-seat-feeder r4 tests, plus a pick-host check. |
| **BH1-54** backlog-file-rewrite-drops-rows | `bd-backlog-append.sh` plus the rewriter (unknown). | A 02:30 rewrite of `IMPROVEMENT_BACKLOG.md` dropped rows appended at 00:2x and 01:2x. Appends are not locked or merged. | Single row, but the rewriter is unknown (investigate first). | backlog-append concurrency test. |
| **BH1-21** cplus-issued-gate-liveness | cplus rid `patched/cplus_authorizer.py`, `patched/operations.py` (different repo). | C4 reads the journal before the busy-append (TOCTOU). A FIFO at `authorizer-pending.tsv` hangs C4. Any OBSERVEd attempt admits G. STEP0: grant-token r2 may close (1). | Different repo, no overlap with harness rows. | cplus authorizer tests (IG4C/IG4S), on its own install window. |
| **BH1-22** push-down-hosts | None (bd_cplus_release push/pin, crontab). | Fixes landed but are not live on DOWN hosts or missing crons (list in the context). | Ops, no file. | a post-push verify pass per host. |
| **BH1-43** pm-ops-leftovers-1019 | `bd-optimistic-merge.sh` (EDGE-B only), otherwise ops. | Various unclosed ops items, such as the swarm driver definition still respawning and rc-enforcer not being deployed. | EDGE-B is the only file item. | ops verify, plus the om-return-f1 EDGE-B test. |
| **BH1-16** dcc-f9-known-limits | `jarvisos_backend/threat_gate.py`, `jarvisos_backend/test_dcc_f9_hardening.py`. | A persistent `~/.gitconfig` alias and run-time-built words bypass the gate (documented limits). The decision is accept or gate `git` when an alias shadows a destructive verb. | Same files as 51. BHC-08 member. | `test_dcc_f9_hardening.py`. |

## 2. Consolidated overlap map (Lane H)

| File | Rows | Verdict |
|---|---|---|
| `bd-launch-role.sh` | 13, 18, 19, 20, 31, 38 | Highest risk. Run sequentially, as BHC-04 (13+18+31) → 19 → 20 → 38. |
| `lib_remote_seat.sh` | 09, 18, 20 | Conflict. 18 is in BHC-04, 20 comes after it, and 09 touches it via the reap path. |
| `bd-launch-codex-role.sh` | 31, 38 | 31 first. |
| `bd-launch-kimi.sh` | 14, 38 | 14 investigation first. |
| `bd-role-claim.sh` | 09, 11, 35 | BHC-03 (10+11+35), then 09. |
| `tests/test_registry_wipe_guard.py` | 10, 35 | BHC-03. The context does not say 11 edits this test file, but it likely adds a race test, so confirm at brief time. |
| `bd-telemetry-daemon.py` | 04, 08, 42 | BHC-05, then 44. |
| `bd_telemetry/registry.py` | 08, 42, 44 | 44 after BHC-05. |
| plugin bd-fleet-mcp `cli.py` / `checks.py` | 01, 15 | BHC-01. |
| `bd-claim-object.sh` | 02, 53 | BHC-02. |
| `bd-usage-probe.sh` | 07, 40 | BHC-06. |
| `~/bin/bd-pool-usage-refresher.py` | 07, 17, 40 | BHC-06, then 17. |
| `bd_cost_accounting.py` | 24, 55 | BHC-07. |

**Hidden or soft overlaps** (not file-identical, so I would verify at brief time):
- 35 and 36 are both live-registry-guard work, so check whether the 9 fakehub tests reference `bd-role-claim.sh` behaviour.
- 29 and 30 both concern band-remote.
- 27 and 29 may share kit infrastructure.
- 41 and 07/17/40 share the usage cache.
- 42 and 55 are both bd_telemetry sensors.
- 12 and 06 sit outside the repo or are env-sensitive, so a gate may not cover them.

## 3. Cautions
- **Unverified claims:** BH1-04 (check against ORDER-2331 r5), BH1-07 (A 403/429 cause) and BH1-17 (live-vs-tracked diff) each need a STEP0 before work starts.
- **No-sha landings:** BH1-21's grant-token r2 and BH1-22's push state have no git sha. Treat their "landed" status as unproven.
- **BH1-09, 14, 41, 54:** these are investigations with an unknown file or writer. Any `BD_GATE_SCOPE` is provisional until the cause is found.

## Investigator Report 2: BulkDownloader & Cockpit (Lanes BD / BD-cockpit) File-Overlap Extractor (`claude-sonnet-5-5-low`)

# Read-only dossier: BD and BD-cockpit lanes (S2)

**Scope:** the rows in lanes `BD` and `BD-cockpit`: BH1-16, 33, 34, 45, 46, 47, 48, 49, 50, 51 and 52.

**Limits of the source:** `<SOURCE_CONTEXT>` has no code, no `git blame` output and no line numbers. Function and line numbers are therefore listed as **COULD NOT LOOK**. Where I give a mechanism, it comes from the row text and I say so. File lists marked "est." come from the rows' "Files (est.)" column and are unchecked.

## Overlap summary

| File | Rows |
|---|---|
| `jarvisos_backend/threat_gate.py` and `test_dcc_f9_hardening.py` | BH1-16, 51 |
| `session_keeper.py` | BH1-34, 46 (est.) |
| `app.py` | BH1-48 only, but see BH1-49 |
| `login_impl/_common.py` and `synthetic_input_scheduler.py` | BH1-49 only |
| Everything else | single row |

## BH1-16: dcc-f9-known-limits (BD-cockpit, M, LOW)
1. **Files:** `jarvisos_backend/threat_gate.py` and `jarvisos_backend/test_dcc_f9_hardening.py`. Functions and lines: COULD NOT LOOK.
2. **Root cause (from the row):** the threat gate classifies a command by its literal words. A `~/.gitconfig` alias that shadows a destructive verb (for example `git co` expanding to `checkout`) never reaches the gate as that verb. Words built at run time (`$X`, concatenation) are also invisible to it. The limits are documented. The open question is a policy choice: accept them, or gate `git` whenever a user alias shadows a destructive verb.
3. **Overlap:** BH1-51, same two files. Both rows edit the verb table.
4. **`BD_GATE_SCOPE`:** the cockpit backend tests, `test_dcc_f9_hardening.py` plus the rest of `jarvisos_backend`. No frontend.

## BH1-33: bd-band-preexisting-reds (BD, M, MED)
1. **Files (est.):**
   - `tests/test_sec2_job_url_token*.py`
   - `tests/test_v3_66_729*.py`
   - `tests/test_v3_66_1183*.py`
   - `tests/source_window_hashes.json`

   Other reds named in the row: the sec2 display_url test, `call_artifact_is_in_sync`, and the row373 remote-only test. Where they live is not given. Lines: COULD NOT LOOK.
2. **Root cause (from the row):** these tests already fail at BASE. Every BOARD excuses them as "pre-existing", so a real regression in the same tests would be missed. The causes of each red are not in the context. The 1183 test appears to compare against a source-window hash.
3. **Overlap:** it shares no file with any other row. It should still land first on the BD train, because it clears the excused reds for every later BD gate.
4. **`BD_GATE_SCOPE`:** the named tests, plus a full BD band run before and after. The aim is to show that the red set shrinks or is quarantined with a ledger, and that no new red appears.

## BH1-34: ssrf-f14-proxy-lan-optin (BD, M, MED)
1. **Files:** `outbound_guard.py`, `deep_http.py`, `session_keeper.py`. Lines: COULD NOT LOOK.
2. **Root cause (from the row):** the SSRF guard has no policy for the per-site proxy host. `deep_http` has no vetted LAN opt-in. The row marks this OWED-PM: it needs a security decision first, then a cut.
3. **Overlap:** BH1-46 on `session_keeper.py` (est.). Confirm that overlap when briefing.
4. **`BD_GATE_SCOPE`:** the SSRF and outbound-guard tests, the `deep_http` tests, and the `session_keeper` tests. Include negative controls for private and loopback addresses and for DNS rebind.

## BH1-45: watch-folder-failed-reporting (BD, S, LOW)
1. **File:** `watch_folder.py`. Function and line: COULD NOT LOOK.
2. **Root cause (from the row):** when `bulk_priority` raises inside the watch_folder path, the failure ends up as `.failed` reporting. dqb-fix-r3 left this outside its OWNS.
3. **Overlap:** none.
4. **`BD_GATE_SCOPE`:** the watch_folder tests, with a case where `bulk_priority` raises.

## BH1-46: captcha-clearance-residuals (BD, S, LOW)
1. **Files:** `cloak.py` and `session_keeper.py` (est.), plus tests. Lines: COULD NOT LOOK.
2. **Root cause (from the row):**
   - `cloak.py` has a dormant `(egress|UNKNOWN, domain)` key with no caller.
   - A bare-url `/dismiss` from another site evicts site A's pending entry and its clearance. This is a LOW availability issue and the same as BASE.
   - No test pins that a failed delete does not purge.
3. **Overlap:** BH1-34 on `session_keeper.py` (est.).
4. **`BD_GATE_SCOPE`:** the captcha, cloak and session_keeper tests, plus a new cross-site dismiss test and a failed-delete test.

## BH1-47: site-templates-vault-orphan (BD, S, LOW)
1. **Files:** the site templates module and the secret backend. Neither is named, so they are COULD NOT LOOK.
2. **Root cause (from the row):**
   - A successful save leaves the previous account-N vault key orphaned.
   - The plaintext or locked secret backend drops a typed account password and only sets a flag.
3. **Overlap:** none known. The files are unidentified, so check at briefing.
4. **`BD_GATE_SCOPE`:** the site-templates and secret-backend tests. Include a save-then-resave case that checks for orphaned keys.

## BH1-48: pair-code-blind-mint (BD, S, LOW)
1. **Files:** `app_pair.py` and `app.py` (`_check_csrf`). Lines: COULD NOT LOOK.
2. **Root cause (from the row):** a top-level GET that carries no Fetch-Metadata headers passes the CSRF check. It can mint or evict pair codes. The code is 96-bit and unreadable cross-site, so the impact is DoS only. aac-fu1 recorded this as DECIDED OPEN.
3. **Overlap:** none. `app.py` is shared only with aac-fu1, which already landed.
4. **`BD_GATE_SCOPE`:** the pairing and CSRF tests, covering GETs with and without Fetch-Metadata.

## BH1-49: origin-guard-keystroke-toctou (BD, S, MED)
1. **Files:** `login_impl/_common.py` and `synthetic_input_scheduler.py`. Lines: COULD NOT LOOK.
2. **Root cause (from the row):**
   - `page.keyboard` types into whatever has focus. The origin check and the keystroke are separate steps, so up to one character can go to the wrong origin.
   - The proposed fix is to bind the keys to the locator and use `press_sequentially`.
   - Separately, a same-brand `:port`, a trailing dot, or `http` is admitted. That is the row 722 predicate and is by design. The row asks to confirm it.
3. **Overlap:** none.
4. **`BD_GATE_SCOPE`:** the login origin-guard tests and the synthetic input tests. Include a case where focus changes between the check and the keystroke.

## BH1-50: cockpit-proxy-redirect-residuals (BD-cockpit, S, LOW)
1. **Files:** `cockpit_frontend` `app/api/bd/[...path]/route.ts` and `app/api/bd/ping/route.ts`. Lines: COULD NOT LOOK.
2. **Root cause (from the row):**
   - The catch-all route relays BD 3xx statuses to the client.
   - `arrayBuffer()` is outside the `try`, so a read failure is unhandled.
   - The ping route follows redirects and sends no Bearer.
3. **Overlap:** none. It touches only the frontend, so it can ride with BHC-08 (the backend).
4. **`BD_GATE_SCOPE`:** the cockpit frontend route tests only.

## BH1-51: threat-gate-fu2-owed-and-overblock (BD-cockpit, M, MED)
1. **Files:** `jarvisos_backend/threat_gate.py` and `jarvisos_backend/test_dcc_f9_hardening.py`. Lines: COULD NOT LOOK.
2. **Root cause (from the row):**
   - **Over-block (N1, MED):** any later word `restore` or `checkout` marks the whole command DANGEROUS. Examples are `git commit -m "restore ..."`, `git log -S restore` and `git grep checkout .`. The gate does not tie the word to the git verb position.
   - **N2:** switch `-C` text is mishandled.
   - **OWED verbs:**
     - `stash drop` and `stash clear`
     - `checkout --ours`, `--theirs` and `-p`
     - the `:/` magic and globs
     - `read-tree` index forms
     - A1, a latency target of 50 ms or less
   - **Other over-block asks:**
     - CRLF, NBSP, redirections, `commit -m "$VAR"`, braces
     - U+202F, U+3000 and U+2028 are unpinned
3. **Overlap:** BH1-16, same two files. They should not land in parallel.
4. **`BD_GATE_SCOPE`:** `test_dcc_f9_hardening.py` plus the other `jarvisos_backend` tests. Include paired controls: benign commands must pass, destructive ones must be blocked, and each new verb needs its own case. Add a timing check for A1.

## BH1-52: cockpit-deploy-owed (BD-cockpit ops, S, MED)
1. **Files:** none. It is a deploy of the eight cockpit landings listed in the row.
2. **Root cause (from the row):** each of those landings ends with "deploy/push = PM step". The deployed build may therefore lag cockpit main `6899fda`. STEP0 is to compare the deployed build against `6899fda`.
3. **Overlap:** none by file. It depends on every cockpit row landing first, so it goes last.
4. **`BD_GATE_SCOPE`:** no code gate. The check is that the deployed commit equals main and that the cockpit health endpoint responds.

## Recommended ordering
1. BH1-33 first.
2. BH1-45, 47, 48 and 49 together. They touch disjoint files.
3. BH1-34 and BH1-46 in sequence if `session_keeper.py` turns out to be shared.
4. BHC-08 (BH1-16 plus BH1-51) with BH1-50.
5. BH1-52 last.
