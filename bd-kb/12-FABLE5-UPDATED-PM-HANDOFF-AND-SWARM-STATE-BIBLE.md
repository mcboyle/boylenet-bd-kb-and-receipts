# BD-KB Volume 12: Claude Fable-5 Reconciled PM Handoff, O3416 Paused State & Control-Seat Atlas
> **Lead Architect**: `Fable5-Lead-2` (`claude-fable-5-1 (.claude-b)`)
> **Subagent Workflow**: 2x Parallel `AGY claude+gpt` (`claude-sonnet-5-5-high` & `claude-sonnet-5-5-low` Sonnet/Haiku Tier) Specialists
> **Generated At**: `2026-10-19T23:00:01Z`
> **Scope**: Strictly Read-Only Infrastructure, Tools, Services, LLMs & PM Catch-Up Documentation (`O3416` Compliant)

---

# BD-KB Volume 12: Claude Fable-5 Reconciled PM Handoff, O3416 Paused State & Control-Seat Atlas

**Author:** Lead Documentation Architect `Fable5-Lead-2`
**Frame:** Reconciles the brief of `2026-10-18T20:52Z` / `2026-10-19T00:12Z` with the state at `2026-10-19T22:50Z`–`22:56Z`.
**Sources:** Subagent Report 1 (Delta Analyst, `claude-sonnet-5-5-high`) and Subagent Report 2 (Control-Seat & Standby Analyst, `claude-sonnet-5-5-low`). Both subagents worked from file slices only. Nothing in this volume was re-verified live. Every figure is a snapshot and should be re-derived before anyone acts on it.

> [!IMPORTANT]
> **Governing hold.** Operator order `O3416` paused every hub cron and timer that messages the PM or any seat, "until I say so". Nothing in this volume authorises a resume. The resume runbook in §6 executes only on an explicit operator order.

---

## 1. Executive Summary

The fleet moved from a fully loaded train to a cleanly idle one in a single day. Every BURN row landed, including `BulkDownloader v3.66.1939`, the `trl-notes` harness cut, and 19 `jarvisos_cockpit` cuts. The vault benchmark sweep closed out at 87 of 87 models, which lifted the hardware lock on five hosts and returned them to serving posture. Local serving endpoints were upgraded to 256K and 512K context with `<think>` enabled. All 24 Linux VMs reached full auth parity, and the repo set grew from five to seven, all mirrored to GitHub and Gitea.

The one structural change in operations is the `O3416` messaging pause. All automation that nudges, bounces, drains or stall-watches seats is stopped. Eight control seats remain up but idle, each with a written handoff on disk. The only scarce resource is the AGY Gemini weekly bucket, which sits in single digits and resets early on `2026-10-20`.

| Signal | Yesterday | Today | Status |
|---|---|---|---|
| Fleet posture | 60 staged candidates, BURN rows in flight | Idle, all rows landed | Landed |
| BD release | pre-`v3.66.1939` | `v3.66.1939` at `63bb759309c0` | Landed |
| Vault benchmarks | 77/87, lock active | 87/87, lock lifted | Complete |
| VM auth parity | legacy token | 24/24 with `--remote-control` | Complete |
| Repos synced | 5 | 7 (synced `21:52 UTC`) | Complete |
| Messaging automation | live | paused under `O3416` | Operator hold |
| Control seats | not itemised | 8 up, idle, handoffs written (`O3415`/`O3417`) | Standby |
| AGY Gemini weekly | not noted | 6.15%–7.78% remaining | Scarce |

---

## 2. Landed Work Since Yesterday

### 2.1 BURN rows and trains

All of the following are reported landed. Head hashes are given where the brief supplied them.

- `origin-guard-fu1` → `v3.66.1939`, `63bb759309c0`
- `trl-notes` → `f2e331f8` in `bd-persist/harness`; harness daemon restarted `20:37:57Z`
- `telemetry-registry-live` → `cda3b91c`
- `origin-guard r3 + aac-fu1`
- `dcc-f9 r7 + dcc-fu2`
- `uopa r2`
- `rwg-hardening r1/r2`
- `T09`, `C18`
- `photosets r2`
- `sac-fu1`
- `deep-site-templates-med r2`
- 19 `jarvisos_cockpit` cuts → `6899fda`

Evidence for the above lives in `/home/mboyle/bd-persist/compact-bd-pm-B.md`, `/home/mboyle/bd-persist/role-state/pm.md`, and the `O3416` pause file.

### 2.2 Held work awaiting an operator `GO`

Two bodies of work are fully prepared but deliberately not dispatched.

| Held item | Order | On-disk artifact | What is ready |
|---|---|---|---|
| Bug-Hunt Wave 1 | `O3388` | `/home/mboyle/bd-persist/state/ROWS-BUGHUNT-WAVE1-20261019.md` | 55 rows `BH1-01`..`BH1-55`, consolidated into 8 non-overlapping cuts `BHC-01`..`BHC-08`. Overlap matrix and train order are pre-computed. |
| Local-LLM trial | `O3401`–`O3409` | `/home/mboyle/bd-persist/state/LOCAL-LENS-SCORE.tsv`, `/home/mboyle/bd-persist/state/LOCAL-BUILD-SCORE.tsv` | Scoring tables exist. Under `O3409`, whichever of the local or codex builder finishes first goes to the Claude lens. |

Neither item should be touched by a successor PM without the operator's word. They are the first things to raise when the hold lifts.

---

## 3. Vault Benchmark Closeout (77/87 → 87/87)

The sweep that was 88.5% complete yesterday is now at 100%. The final ten heavyweight models completed `llama-bench` under `b11716` and `ik_llama.cpp` with TurboQuant. Safetensors, ONNX and Diffusers verification also finished. The 87 model directories span 8.9+ TB across NVMe and vSAN on `.72` and `.83`.

Follow-on actions already done:

- RAM and VRAM were flushed after the final run.
- 16 spare VMs were restored.
- The benchmark guard on `.72`, `.83`, `.125`, `.137` and `.228` is lifted. Those hosts are uncontended and back in production serving posture.

### 3.1 Frontier results worth remembering

| Model | pp tok/s | tg tok/s | Note |
|---|---|---|---|
| `GLM-4.5-Air-REAP-82B-A12B-Heretic-Q4_K_M` | 30.02 | 6.40 | |
| `NVIDIA-Nemotron-3.5-Bolt-74B-A9B` | 29.18 | 13.46 | |
| `KIMI-K3-MXP4-DERISKED-Q2_K` | 69.13 | 16.30 | 1T params, 940.18 GiB, 4-node 1.73 TB DDR4 + 4x T4 |

### 3.2 Receipts

All under `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/`:

- `AGENT154-87OF87-FINAL-AND-16-SPARES-RESTORE-RECEIPTS.md`
- `AGENT155-RAM-VRAM-FLUSH-POST-87-RECEIPTS.md`
- `AGENT37-NEW-FRONTIER-MODELS-BENCHMARK-RECEIPTS.md`
- `AGENT171-HOST72-SERIALIZED-BENCHMARKS-RECEIPTS.md`
- `AGENT172-HOST83-SERIALIZED-BENCHMARKS-RECEIPTS.md`
- `SERIALIZED-OPT-PIPELINE-LEDGER.md`

---

## 4. Local Serving Posture

With the lock lifted, the local tier was reconfigured for long context and reasoning.

| Endpoint | Model | Context | Slots | Throughput (pp / tg) |
|---|---|---|---|---|
| `10.0.70.83:18080` | `Qwen3.6-35B-A3B-Heretic` | 256K unified (`262,144`), `<think>` on | 2 × 65,536 | 795.0 / 65.8 on 2× T4; 103.59 / 24.16 on 1× T4 |
| `10.0.70.72:8000` | `Qwen3-Coder-30B-A3B-Instruct-Q4_K_M` | `n_ctx: 262144` | 4 | 848.90 / 77.62 |
| `10.0.70.72:18081` | `Qwythos-9B-Claude-Mythos-5-1M-MTP-Q8_0` | 512K | n/a | 1,005.86 / 21.39 |
| `.137:11434`, `.125:11434`, `.72:11434`, `:4010`, `:8090` | `CyberStrike-35B-Q4_K_M` | n/a | n/a | 799.0 prefill / 66.5 decode |

Receipts: `AGENT151-MAX-CONTEXT-256K-1M-AND-THINKING-RECEIPTS.md` and `AGENT152-ALL-MODELS-THINKING-AND-CONTEXT-VERIFICATION-RECEIPTS.md`, same directory as §3.2.

Read-only health check from the brief:

```bash
curl -sS http://10.0.70.83:18080/health && curl -sS http://10.0.70.83:18080/slots | jq '.[0] | {n_ctx, is_processing}'
```

Report 2 adds the GPU picture at `22:55Z`: 4× Tesla T4, 49,644 of 61,440 MiB VRAM in use (80.8%), 8/8 local slots free across two healthy pools, 0% quota used. VRAM headroom is roughly 19%, so do not load a second large model onto the same cards.

---

## 5. OpenWebUI, Gateways, TLS and Visualisers

| Item | Yesterday | Today |
|---|---|---|
| Model names | 260 badge-prefixed | model-first names |
| Dropdown width | default | 620px |
| UI catalog | 260 | 215 deduplicated (300 total API models) |
| Gateway `:4010` | n/a | 352 models |
| Gateway `:4000` | n/a | 253 models |
| Hosts | n/a | `.164:8090`, `.72:8080` |

Capabilities listed in the brief: 50 toolkits (`tk_01`..`tk_50`), 5 OpenAPI servers with 39 REST endpoints, 14 MCP servers, 128 slash skills, 8 pipelines, 12 functions, 28 VM terminals at `/vm/<hostname>`, and the master KB at `http://10.0.70.125:6333/collections/bd_master_kb`.

TLS: NGINX `:443` uses the wildcard cert at `/etc/letsencrypt/live/themfboyles.org/fullchain.pem`. The brief reports 211/211 HTTPS checks passing across 47 subdomains using the dual Chrome CDP oracle on `.137:9222` and `.181:9222`. The 165-view visualiser is on `:4088` and the unified OS shell on `:4085`.

Receipts: `OPENWEBUI-AUTONOMOUS-RELIEF-AGENT-RECEIPTS.md` and `AGENT140-OPENWEBUI-15AGENTS-83TOOLS-14MCP-QDRANT-PG-RECEIPTS.md`.

---

## 6. O3416 Paused State: Full Inventory and Resume Runbook

### 6.1 What was paused

| Layer | State | Count | Backup |
|---|---|---|---|
| Hub crontab | lines prefixed `#PAUSED-O3416 ` | 35 | `/home/mboyle/bd-persist/crontab-backups/crontab.pre-O3416-20261019T2138Z` |
| User timers | stopped, **not disabled** | 7 | none needed |
| System timers | stopped, **not disabled** | 2 | none needed |
| Remote VM crons | commented | 23 VMs, 1–2 lines each | `~/.crontab.pre-O3416` on each VM |
| Unreachable VMs | untouched | 9 | none; nothing was changed |

User timers: `bd-fleet-watch`, `bd-pm-idle-watch`, `bd-reap`, `bd-pm-stall-watch`, `bd-ugrep-reaper`, `bd-usage-oneshot`, `bd-usage-probe`.
System timers: `bd-rc-enforcer`, `bd-relief-puppeteer`.
Unreachable hosts: `.137 .122 .171 .181 .195 .197 .198 .224 .35`.

Paused hub crons named in the reports include `pm-heartbeat` (×2, including `pm-inbox-guard`), `pm-batch-summary`, `autobounce`, `inbox-drain`, `turn-watch` and `stall-guard`.

### 6.2 What still runs

These do not message seats and were left alone: backups, log-rotate, `pm-beat` (writes the DIGEST file only), `ciguard`, `regen-main`, RAG/memory indexers, `pool-usage-refresher`, `auth-sync`, `kv-warmer`, `swarm-telemetry` and others.

### 6.3 Known consequences of the pause

- **Hub reboot restarts the timers.** They were stopped, not disabled. A reboot silently breaks the operator's hold. Avoid rebooting the hub, or disable the timers if a reboot becomes unavoidable and the operator agrees.
- **No iowait guard.** `bd-ugrep-reaper` is off, so orphan `ugrep` processes can accumulate. Watch hub iowait by hand.
- **No registry reap.** `bd-reap` is off, so registry entries go stale until resume.
- **No PM stall detection.** `bd-pm-stall-watch` is off. A `bd-pm-B` stall would go unnoticed. Check the seat and the `pm-beat` DIGEST file manually.
- **No automatic relief.** `bd-relief-puppeteer` is off. Any standby takeover during the pause is manual (§8).
- **Nine VMs may still message.** Their crons were never touched. Investigate reachability before assuming silence.

### 6.4 Resume runbook (operator order only)

Both subagents flagged that the pause file documents the crontab step as `sed -i` on `crontab -l` output, which cannot work on a pipe. The form below is the safe equivalent. Confirm it against the runbook before use.

**Step 1 – Hub crontab (expect 35 uncommented lines afterwards)**

```bash
crontab -l | sed 's/^#PAUSED-O3416 //' | crontab -
# or restore the full backup:
crontab /home/mboyle/bd-persist/crontab-backups/crontab.pre-O3416-20261019T2138Z
```

**Step 2 – User timers (7)**

```bash
systemctl --user start bd-{fleet-watch,pm-idle-watch,reap,pm-stall-watch,ugrep-reaper,usage-oneshot,usage-probe}.timer
```

**Step 3 – System timers (2)**

```bash
sudo systemctl start bd-rc-enforcer.timer bd-relief-puppeteer.timer
```

**Step 4 – Per-VM crons (23 reachable VMs)**

```bash
crontab ~/.crontab.pre-O3416   # run on each VM
```

No action is needed on the nine unreachable hosts.

**Suggested ordering** (Report 2's recommendation, not stated in any source file): restore `bd-reap` and `bd-ugrep-reaper` first to close the two caveats, then `bd-rc-enforcer`, then the PM heartbeat and watch jobs, then `bd-relief-puppeteer`, and the per-VM crons last.

---

## 7. Control-Seat Atlas and Handoff File Index

Under `O3415` the seats were kept up. Under `O3417` each wrote a handoff. Every file below is on disk and is the successor's first read for that seat.

| Seat | Host | Pool / mode | Handoff file |
|---|---|---|---|
| `bd-pm-B` | `hub-mesh01` | Pool B, `--remote-control` | `/home/mboyle/bd-persist/compact-bd-pm-B.md` |
| `bd-dispatch-build-D5-D` | `.194` (`wrk-test05`) | Pool D | `/home/mboyle/bd-persist/handoff-bd-dispatch-build-D5-D.md` |
| `bd-dispatch-F2-D` | `.186` | Pool D | `/home/mboyle/bd-persist/handoff-bd-dispatch-F2-D.md` |
| `bd-lensrouter-B5-C` | `.186` | Pool C | `/home/mboyle/bd-persist/handoff-bd-lensrouter-B5-C.md` |
| `bd-integrator-D4-A` | `.54` | Pool A | `/home/mboyle/bd-persist/handoff-bd-integrator-D4-A.md` |
| `bd-dispatch-F1-D` | `.54` | Pool D | `/home/mboyle/bd-persist/handoff-bd-dispatch-F1-D.md` |
| `bd-lensrouter-B4-D` | `.186` | Pool D | `/home/mboyle/bd-persist/handoff-bd-lensrouter-B4-D.md` |
| `bd-integrator-C` | `.84` | Pool C | `/home/mboyle/bd-persist/handoff-bd-integrator-C.md` |
| `agy` | `hub-mesh01` tmux | AGY council, holds `god` (`O3357`/`O3358`) | message via `tmux send-keys`, never `bd-say` |

Also present: 10 `bd-local-*` GPU worker daemons (idle) and `kimi-quota-probe`.

**Other state and handoff files indexed in this volume:**

- `/home/mboyle/bd-persist/PM-HANDOFF.md` (written `22:25:00Z`; the brief truncates at §7 under the 22000-byte cut)
- `/home/mboyle/bd-persist/role-state/pm.md`
- `/home/mboyle/bd-persist/role-state/god.md`
- `/home/mboyle/bd-persist/OPERATOR_DECISIONS.md` (`O3357`–`O3417` for this period)
- `/home/mboyle/bd-persist/state/PAUSED-O3416-messaging-crons.md`
- `/home/mboyle/bd-persist/state/POOL_STATE.tsv`
- `/home/mboyle/bd-persist/state/ROWS-BUGHUNT-WAVE1-20261019.md`
- `/home/mboyle/bd-persist/state/LOCAL-LENS-SCORE.tsv`, `LOCAL-BUILD-SCORE.tsv`
- `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGY-RELIEF-HANDOFF-OPUS55-FABLE51-LOCAL.md`
- `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/OPUS55-POOL-C-RELIEF-TAKEOVER.md`

**What "idle" means under O3416.** The automation that would normally wake these seats is off. A seat that stays idle will stay idle until the operator or a manual `say` wakes it. That is expected, not a fault. Neither subagent could see the live state of `bd-pm-B` or the seven idle seats; verify with `bd-role-claim.sh who` and the seat handoff files before acting.

---

## 8. Quota Headroom and Standby Local Takeover Runbook

### 8.1 Quota at the two snapshots

| Pool | `22:25Z` (Report 1) | `22:55Z` (Report 2) | Resets |
|---|---|---|---|
| Claude A | 5h 99%, weekly 27% used | OK, weekly 27% | `2026-10-20T00:29:59Z` |
| Claude B (`bd-pm-B`) | 5h 97%, weekly 40% used | OK, weekly 40% | `2026-10-20T00:40:00Z` |
| Claude C | 5h 97%, weekly 33% used | OK, weekly 33% | `2026-10-20T00:30:00Z` |
| Claude D | 5h 96%, weekly 41% used | OK, weekly 41% | `2026-10-19T23:50:00Z` |
| AGY `claude+gpt` | 100% / 99.49% remaining | OK | n/a |
| **AGY `gemini`** | 5h 94.49%, **weekly 7.78%** | **weekly 6.15%** | `2026-10-20T01:41:59Z` |
| Codex | 51% | 49% of window | `2026-10-25T11:51:11Z` |
| Kimi | 86%, monthly 46% | 5h 14% used, monthly 46% | `2026-10-20T00:30:15Z` |
| local | n/a | 8/8 slots, 0% | n/a |
| grok | n/a | UNKNOWN, no quota source | n/a |

The Gemini and Codex figures differ between snapshots because they are 30 minutes apart. Gemini dropped 1.63 points in that window, so it is being consumed and will hit zero before its reset if the burn continues. Do not route seat wake-ups to Gemini. Prefer pools A, C or B, or the local tier. Pool D resets soonest but carries the highest weekly usage.

### 8.2 Takeover path when AGY Gemini weekly reaches 0%

1. **Local commander first.** Zero marginal cost, works air-gapped:
   ```bash
   OPENAI_BASE_URL=http://10.0.70.164:4010/v1 OPENAI_API_KEY=sk-boylenet-master \
     bd-local-coding-agent --model agent-council-relief-commander "<task>"
   ```
   Use `--model tier1-qwen3-coder-30b-a3b` for builder work.
2. **AGY `claude+gpt` bucket.** Independent of the Gemini bucket and at ~99% remaining.
3. **Claude Pools A and C.** Lowest weekly usage among the Claude pools.

Runbooks: `AGY-RELIEF-HANDOFF-OPUS55-FABLE51-LOCAL.md` and `OPUS55-POOL-C-RELIEF-TAKEOVER.md` under `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/`.

### 8.3 Hot-standby endpoints

`.83:18080` and `.72:8000` are the designated local relief takeover models. Their live state was not in either subagent's context. Probe before use:

```bash
curl -s -m 5 http://10.0.70.83:18080/v1/models
curl -s -m 5 http://10.0.70.72:8000/v1/models
```

Because `bd-relief-puppeteer.timer` is stopped, any takeover during the pause is manual. The two healthy local pools in `POOL_STATE.tsv` plausibly map to these two endpoints, but no source file says so.

---

## 9. Repository Sync (5 → 7)

All seven repos are mirrored to GitHub (`mcboyle`) and Gitea (`http://10.0.70.164:3000/mboyle`), synced `2026-10-19 21:52 UTC`.

| # | Repo | Local path | `main` head |
|---|---|---|---|
| 1 | `BulkDownloader` | `/home/mboyle/BulkDownloader` | `63bb759309c0` (`v3.66.1939`) |
| 2 | `jarvisos_cockpit` | `/home/mboyle/jarvisos_cockpit` | `6899fda` |
| 3 | `boylenet-swarm-os` | `/home/mboyle/git-repos/boylenet-swarm-os` | `df78aee51ce2` |
| 4 | `boylenet-openwebui-supersuite` | `/home/mboyle/git-repos/boylenet-openwebui-supersuite` | `dfc582a5a73e` |
| 5 | `boylenet-bd-kb-and-receipts` | `/home/mboyle/git-repos/boylenet-bd-kb-and-receipts` | `e57a741c281b` |
| 6 | `boylenet-infra-configs` | `/home/mboyle/git-repos/boylenet-infra-configs` | `37729db731fc` |
| 7 | `UniversalSwarmOS` | `/home/mboyle/git-repos/UniversalSwarmOS` | `45d4bb640bec` |

`trl-notes` (`f2e331f8`) lives in `bd-persist/harness` and is not one of the seven.

---

## 10. 24-VM Parity (`O3394` / `O3412` / `O3413b`)

Each of the 24 Linux VMs now has independent full-scope logins for Claude A/B/C/D, Codex, Kimi and AGY. The legacy `token-1year.txt` was moved aside so new launches support `--remote-control`. Plugin, skill, MCP and `~/bin` parity is reported at 100%. Decisions are recorded in `OPERATOR_DECISIONS.md` under `O3394`–`O3417`.

The 24/24 parity count and the 23-reachable-VM cron pause count are different measures taken at different times and do not necessarily conflict. A one-line reconciliation note in the pause file would remove the ambiguity.

---

## 11. Open Items and Risks

| # | Item | Severity | Action |
|---|---|---|---|
| 1 | AGY Gemini weekly at 6.15% and falling | High | Route away from Gemini; use §8.2 when it hits zero |
| 2 | Hub reboot would restart paused timers | High | Do not reboot the hub during the hold |
| 3 | `.137` and `.181` unreachable for the cron pause but host `CyberStrike` and the CDP oracle | Medium | Confirm whether SSH alone is down or the hosts are offline before relying on either service |
| 4 | Nine untouched VMs may still send messages | Medium | Check reachability; expect stray messages |
| 5 | `bd-ugrep-reaper` and `bd-reap` off | Medium | Watch hub iowait and registry staleness by hand |
| 6 | `bd-pm-B` and 7 idle seats: live state unverified | Medium | Read handoff files, check `pm-beat` DIGEST, run `bd-role-claim.sh who` |
| 7 | `O3388` Bug-Hunt Wave 1 held | Info | Raise when the operator lifts the hold |
| 8 | Pause file documents `sed -i` on piped output | Low | Fix the runbook text to the pipe form in §6.4 |
| 9 | Brief truncated at §7 (22000-byte cut) | Low | Successor should read the full `PM-HANDOFF.md` on disk |
| 10 | Quota figures are 25–30 minutes old | Info | Re-read `POOL_STATE.tsv` before routing |

---

## 12. Successor PM First-Hour Checklist

1. Read `/home/mboyle/bd-persist/compact-bd-pm-B.md` and `/home/mboyle/bd-persist/role-state/pm.md`.
2. Confirm the `O3416` hold is still in force by checking `OPERATOR_DECISIONS.md` for any later order. Do not resume anything without one.
3. Verify `bd-role-claim.sh who god` names an AGY seat before acting on any `god`-signed instruction.
4. Re-read `POOL_STATE.tsv`. If Gemini weekly is at or near zero, switch AGY work to the local commander per §8.2.
5. Check hub iowait and look for orphan `ugrep` processes, since the reaper is off.
6. Probe `.83:18080` and `.72:8000` with the commands in §8.3 so the standby path is known-good before it is needed.
7. Leave `O3388` and the local-LLM trial untouched until the operator says `GO`.

---

# Appendix: Detailed Specialist Subagent Reports (`AGY claude+gpt` Sonnet/Haiku Fanout)

## Subagent Report 1: Yesterday-to-Today BD-Swarm Delta Analyst (`AGY claude+gpt: claude-sonnet-5-5-high`)

## Fable5-Lead-2-S1: Yesterday-to-Today BD-Swarm Delta Analysis

**Scope:** The reconciled brief of `2026-10-18T20:52Z` / `2026-10-19T00:12Z` compared with the state at `2026-10-19T22:50Z`.
**Sources:** Only the two files in `<CONTEXT>`. Nothing was re-verified live. The brief text itself ends at §7, where the 22000-byte cut truncated it.

- `/home/mboyle/bd-persist/PM-HANDOFF.md`, written `2026-10-19T22:25:00Z`.
- `/home/mboyle/bd-persist/state/PAUSED-O3416-messaging-crons.md`.

---

### 1. Headline Delta

| Signal | Yesterday | Today (`22:50Z`) | Change |
|---|---|---|---|
| Fleet posture | 60 staged candidates and BURN rows in flight | **Cleanly idle**: all BURN rows and 19 `jarvisos_cockpit` cuts landed | In flight → idle |
| BD release | Pre-`v3.66.1939` | **`v3.66.1939`** (`origin-guard-fu1`, `63bb759309c0`) | Landed |
| `trl-notes` | Not landed | **`f2e331f8`** in `bd-persist/harness`; daemon restarted `20:37:57Z` | Landed |
| `jarvisos_cockpit` | In progress | **`6899fda`**, 19 cuts | Landed |
| Vault benchmarks | `77/87` (88.5%), 10 heavyweight models running, hardware lock active | **`87/87` (100.0%)**, including `#87 DeepSeek-V4.1-Flash-Abliterated-755B` | +10 models, lock lifted |
| VM auth parity | Legacy `token-1year.txt` on remote VMs | **`24/24`** Linux VMs (`O3394`/`O3412`/`O3413b`) | Token moved aside |
| Repo sync | 5 repos | **7 repos**, synced `2026-10-19 21:52 UTC` | +2 repos |
| Messaging automation | Live | **Paused** (`O3416`) | New operator hold |
| Control seats | Not itemised in the delta | Kept up (`O3415`); handoffs written (`O3417`) | New |

---

### 2. Queue and Train Status

Yesterday the fleet was working 60 staged candidates. Today every BURN row is landed. The brief lists:

- `origin-guard-fu1`: `v3.66.1939`, `63bb759309c0`.
- `trl-notes`: `f2e331f8`.
- `telemetry-registry-live`: `cda3b91c`.
- `origin-guard r3 + aac-fu1`.
- `dcc-f9 r7 + dcc-fu2`.
- `uopa r2`.
- `rwg-hardening r1/r2`.
- `T09`.
- `C18`.
- `photosets r2`.
- `sac-fu1`.
- `deep-site-templates-med r2`.
- 19 `jarvisos_cockpit` cuts (`6899fda`).

**Evidence:**
- `/home/mboyle/bd-persist/compact-bd-pm-B.md`
- `/home/mboyle/bd-persist/role-state/pm.md`
- `/home/mboyle/bd-persist/state/PAUSED-O3416-messaging-crons.md`

**Held work, not dispatched** (needs an operator `GO`):

| Item | Path | Content |
|---|---|---|
| Bug-Hunt Wave 1 (`O3388`) | `/home/mboyle/bd-persist/state/ROWS-BUGHUNT-WAVE1-20261019.md` | 55 rows (`BH1-01`..`BH1-55`) consolidated into 8 non-overlapping cuts (`BHC-01`..`BHC-08`), with overlap matrix and train order pre-computed |
| Local-LLM trial (`O3401`–`O3409`) | `/home/mboyle/bd-persist/state/LOCAL-LENS-SCORE.tsv`, `/home/mboyle/bd-persist/state/LOCAL-BUILD-SCORE.tsv` | `O3409`: the local or codex first finisher goes to the Claude lens |

---

### 3. Vault Benchmark Sweep (77/87 → 87/87)

- All 87 model directories (8.9+ TB across NVMe and vSAN on `.72` and `.83`) completed `llama-bench`. The brief names `b11716`, `ik_llama.cpp` and TurboQuant. Safetensors, ONNX and Diffusers verification also completed.
- A post-benchmark RAM/VRAM flush was done, and 16 spare VMs were restored.
- The benchmark guard (hardware lock) on `.72`, `.83`, `.125`, `.137` and `.228` is lifted. Those hosts are uncontended and in production serving posture.

**Receipts**, all under `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/`:
- `AGENT154-87OF87-FINAL-AND-16-SPARES-RESTORE-RECEIPTS.md`
- `AGENT155-RAM-VRAM-FLUSH-POST-87-RECEIPTS.md`
- `AGENT37-NEW-FRONTIER-MODELS-BENCHMARK-RECEIPTS.md`
- `AGENT171-HOST72-SERIALIZED-BENCHMARKS-RECEIPTS.md`
- `AGENT172-HOST83-SERIALIZED-BENCHMARKS-RECEIPTS.md`
- `SERIALIZED-OPT-PIPELINE-LEDGER.md`

**Frontier benchmark results (tok/s):**

| Model | Prompt processing (pp) | Token generation (tg) |
|---|---|---|
| `GLM-4.5-Air-REAP-82B-A12B-Heretic-Q4_K_M` | 30.02 | 6.40 |
| `NVIDIA-Nemotron-3.5-Bolt-74B-A9B` | 29.18 | 13.46 |
| `KIMI-K3-MXP4-DERISKED-Q2_K` (1T params, 940.18 GiB, 4-node 1.73 TB DDR4 + 4x T4) | 69.13 | 16.30 |

---

### 4. Local Serving Posture (Context and `<think>` Upgrades)

| Endpoint | Model | Yesterday | Today | Throughput |
|---|---|---|---|---|
| `10.0.70.83:18080` | `Qwen3.6-35B-A3B-Heretic` | Standard context; host busy with `llama-bench` | **256K (`262,144`) unified**, 65,536 per slot across 2 slots, `<think>` on | 795.0 pp / 65.8 tg (2x T4); 103.59 pp / 24.16 tg (1x T4) |
| `10.0.70.72:8000` | `Qwen3-Coder-30B-A3B-Instruct-Q4_K_M` | Standard | **`n_ctx: 262144`**, `total_slots: 4` | 848.90 pp / 77.62 tg |
| `.72:18081` | `Qwythos-9B-Claude-Mythos-5-1M-MTP-Q8_0` | n/a | **512K** configured | 1,005.86 pp / 21.39 tg |
| `.137:11434`, `.125:11434`, `.72:11434`, `:4010`, `:8090` | `CyberStrike-35B-Q4_K_M` | n/a | Live, 66.5 tok/s decode | 799.0 prefill |

**Evidence:**
- `AGENT151-MAX-CONTEXT-256K-1M-AND-THINKING-RECEIPTS.md`
- `AGENT152-ALL-MODELS-THINKING-AND-CONTEXT-VERIFICATION-RECEIPTS.md`

Both are under `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/`.

**Read-only health check from the brief (`/health`, `/slots`, `/props`):**
```bash
curl -sS http://10.0.70.83:18080/health && curl -sS http://10.0.70.83:18080/slots | jq '.[0] | {n_ctx, is_processing}'
```

---

### 5. OpenWebUI and Gateways

| Item | Yesterday | Today |
|---|---|---|
| Model names | 260 raw badge-prefixed | Model-first names |
| Dropdown | Default narrow | **620px** |
| Catalog | 260 entries | **215** deduplicated UI models; 300 total API models |
| Gateway counts | n/a | `:4010` = 352 models; `:4000` = 253 models |
| Hosts | n/a | `.164:8090` and `.72:8080` |

**Other capabilities listed in the brief:**
- 50 toolkits (`tk_01`..`tk_50`).
- 5 OpenAPI servers (39 REST endpoints).
- 14 MCP servers.
- 128 slash skills.
- 8 pipelines and 12 functions.
- 28 VM terminals (`/vm/<hostname>`).
- Master KB at `http://10.0.70.125:6333/collections/bd_master_kb`.

**TLS and visualisers:**
- NGINX `:443` uses the wildcard cert at `/etc/letsencrypt/live/themfboyles.org/fullchain.pem`.
- The brief reports `211/211` HTTPS checks passing, with the dual Chrome CDP oracle on `.137:9222` and `.181:9222`, across 47 subdomains.
- The 165-view visualiser is on `:4088`. The unified OS shell is on `:4085`.

**Evidence:**
- `OPENWEBUI-AUTONOMOUS-RELIEF-AGENT-RECEIPTS.md`
- `AGENT140-OPENWEBUI-15AGENTS-83TOOLS-14MCP-QDRANT-PG-RECEIPTS.md`

---

### 6. 24-VM Parity (`O3394` / `O3412` / `O3413b`)

- Each of the 24 Linux VMs has independent full-scope logins for Claude `A/B/C/D`, Codex, Kimi and AGY.
- `token-1year.txt` was moved aside so new launches support `--remote-control`.
- Plugin, skill, MCP and `~/bin` parity is reported at 100%.
- The decisions are recorded in `/home/mboyle/bd-persist/OPERATOR_DECISIONS.md` (`O3394`–`O3417`).

---

### 7. Repository Sync (5 → 7)

Every repo is synced to GitHub (`mcboyle`) and to local Gitea (`http://10.0.70.164:3000/mboyle`).

| # | Repo | Local path | Head (`main`) |
|---|---|---|---|
| 1 | `BulkDownloader` | `/home/mboyle/BulkDownloader` | `63bb759309c0` (`v3.66.1939`) |
| 2 | `jarvisos_cockpit` | `/home/mboyle/jarvisos_cockpit` | `6899fda` |
| 3 | `boylenet-swarm-os` | `/home/mboyle/git-repos/boylenet-swarm-os` | `df78aee51ce2` |
| 4 | `boylenet-openwebui-supersuite` | `/home/mboyle/git-repos/boylenet-openwebui-supersuite` | `dfc582a5a73e` |
| 5 | `boylenet-bd-kb-and-receipts` | `/home/mboyle/git-repos/boylenet-bd-kb-and-receipts` | `e57a741c281b` |
| 6 | `boylenet-infra-configs` | `/home/mboyle/git-repos/boylenet-infra-configs` | `37729db731fc` |
| 7 | `UniversalSwarmOS` | `/home/mboyle/git-repos/UniversalSwarmOS` | `45d4bb640bec` |

`trl-notes` (`f2e331f8`) lives in `bd-persist/harness`, not in the table above.

---

### 8. Paused Crons (`O3416`) and Control Seats (`O3415`/`O3417`)

This is the main operational change since yesterday. The operator paused every hub cron and timer that messages the PM or any seat, "until I say so".

| Layer | State | Detail |
|---|---|---|
| Hub crontab | 35 lines prefixed `#PAUSED-O3416 ` | Backup at `/home/mboyle/bd-persist/crontab-backups/crontab.pre-O3416-20261019T2138Z` |
| User timers | Stopped, not disabled | `bd-fleet-watch`, `bd-pm-idle-watch`, `bd-reap`, `bd-pm-stall-watch`, `bd-ugrep-reaper`, `bd-usage-oneshot`, `bd-usage-probe` |
| System timers | Stopped, not disabled | `bd-rc-enforcer`, `bd-relief-puppeteer` |
| Remote VMs | 23 reachable VMs paused | Backup at `~/.crontab.pre-O3416` on each VM |
| Unreachable, untouched | 9 hosts | `.137 .122 .171 .181 .195 .197 .198 .224 .35` |
| Still running (no messaging) | n/a | Backups, log-rotate, `pm-beat` (DIGEST file), `ciguard`, `regen-main`, RAG/memory indexers, `pool-usage-refresher`, `auth-sync`, `kv-warmer`, `swarm-telemetry` and others |

**Resume commands (operator order only):**
```bash
crontab -l | sed 's/^#PAUSED-O3416 //' | crontab -        # or restore the backup above
systemctl --user start bd-{fleet-watch,pm-idle-watch,reap,pm-stall-watch,ugrep-reaper,usage-oneshot,usage-probe}.timer
sudo systemctl start bd-rc-enforcer.timer bd-relief-puppeteer.timer
```
The pause file gives only `sed -i` on `crontab -l` output. The pipe into `crontab -` above is my expansion, so confirm it against the runbook before use.

**Caveats:**
- A hub reboot restarts the timers, because they were stopped and not disabled.
- Pausing `bd-ugrep-reaper` removes the hub iowait guard against orphan `ugrep` processes.
- Pausing `bd-reap` stops registry reaping.

**Control seats kept up (`O3415`):**

| Seat | Host | Handoff |
|---|---|---|
| `bd-pm-B` (Pool B, `--remote-control`) | `hub-mesh01` | `/home/mboyle/bd-persist/compact-bd-pm-B.md` |
| `bd-dispatch-build-D5-D` | `.194` (`wrk-test05`) | `/home/mboyle/bd-persist/handoff-bd-dispatch-build-D5-D.md` |
| `bd-dispatch-F2-D` | `.186` | `/home/mboyle/bd-persist/handoff-bd-dispatch-F2-D.md` |
| `bd-lensrouter-B5-C` | `.186` | `/home/mboyle/bd-persist/handoff-bd-lensrouter-B5-C.md` |
| `bd-integrator-D4-A` | `.54` | `/home/mboyle/bd-persist/handoff-bd-integrator-D4-A.md` |
| `bd-dispatch-F1-D` | `.54` | `/home/mboyle/bd-persist/handoff-bd-dispatch-F1-D.md` |
| `bd-lensrouter-B4-D` | `.186` | `/home/mboyle/bd-persist/handoff-bd-lensrouter-B4-D.md` |
| `bd-integrator-C` | `.84` | `/home/mboyle/bd-persist/handoff-bd-integrator-C.md` |
| `agy` | `hub-mesh01` tmux | Message via `tmux send-keys`, never `bd-say` |

There are also 10 `bd-local-*` GPU worker daemons (idle) and `kimi-quota-probe`.

---

### 9. Quota Headroom (as of `22:25Z`)

| Pool | 5h remaining | Weekly |
|---|---|---|
| Claude A | 99% | 27% used |
| Claude B (`bd-pm-B`) | 97% | 40% used |
| Claude C | 97% | 33% used |
| Claude D | 96% | 41% used |
| AGY `claude+gpt` | 100% | 99.49% remaining |
| **AGY `gemini`** | 94.49% (resets `2026-10-20T01:41:59Z`) | **7.78% remaining** |
| Codex | 51% | n/a |
| Kimi | 86% | 46% monthly used |

**Takeover path when AGY Gemini weekly hits 0%:**
1. Use the local commander. It has a `$0` marginal cost and works air-gapped:
   ```bash
   OPENAI_BASE_URL=http://10.0.70.164:4010/v1 OPENAI_API_KEY=sk-boylenet-master \
     bd-local-coding-agent --model agent-council-relief-commander "<task>"
   ```
   Use `--model tier1-qwen3-coder-30b-a3b` for builder work.
2. Fall back to the AGY `claude+gpt` bucket, which is independent of the Gemini bucket. Claude Pools A and C are the next fallback.

**Runbooks**, under `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/`:
- `AGY-RELIEF-HANDOFF-OPUS55-FABLE51-LOCAL.md`
- `OPUS55-POOL-C-RELIEF-TAKEOVER.md`

---

### 10. Items Needing Attention

1. **Gemini quota:** AGY `gemini` weekly is at 7.78%. This is the only scarce pool, and the takeover runbook above covers it.
2. **Unreachable hosts:** `.137` and `.181` are among the 9 hosts the cron pause could not reach. The brief also places `CyberStrike` on `.137:11434` and the CDP oracle on `.137:9222` and `.181:9222`. These may be services running while SSH is unreachable. Confirm before relying on either.
3. **Parity count:** The 24/24 VM auth parity and the 23 reachable VMs for the cron pause are different measures, so they need not conflict. A reconciliation note would still help.
4. **Reboot risk:** Timers are stopped, not disabled, so a hub reboot would restart them against the `O3416` hold.
5. **Held waves:** `O3388` Bug-Hunt Wave 1 stays on hold until the operator says `GO`.
6. **Snapshot lag:** The brief was written at `22:25Z`, about 25 minutes before the `22:50Z` frame. Treat quota figures as a 25-minute-old snapshot.

## Subagent Report 2: Control-Seat Handoffs & Standby Takeover Analyst (`AGY claude+gpt: claude-sonnet-5-5-low`)

# Control-Seat Handoffs & Standby Takeover (Fable5-Lead-2-S2)

> [!IMPORTANT]
> This section uses only the two supplied files: `PAUSED-O3416-messaging-crons.md` (first 2055 bytes) and `POOL_STATE.tsv` (first 2375 bytes). The context says nothing about the live state of `bd-pm-B`, the per-seat state of the 7 idle seats, or the takeover endpoints `.83:18080` and `.72:8000`. I mark those items **NOT IN CONTEXT** and have not invented values. Verify them live before acting.

## 1. Situation summary

| Item | Value | Source |
|---|---|---|
| Directive | O3416: every hub cron or timer that messages the PM or any seat is paused "until I say so" | Pause file header |
| Issued by | Operator, 2026-10-19 ~21:4xZ | Pause file |
| Snapshot time | 2026-10-19T22:55:16Z (pool state) and 22:56:12Z (local clock) | `POOL_STATE.tsv` |
| Pause scope | Hub crontab, 7 user timers, 2 system timers, and per-VM crons on 23 VMs | Pause file |
| Persistence risk | Timers were stopped, not disabled, so a hub reboot restarts them | Pause file NOTE |

## 2. `bd-pm-B` and the 7 idle control seats

| Seat | Role in this handoff | Live state in context | Messaging-cron exposure while paused |
|---|---|---|---|
| `bd-pm-B` | PM seat; recipient of the paused heartbeat, inbox-guard and batch-summary crons | **NOT IN CONTEXT** | `pm-heartbeat` x2 (including `pm-inbox-guard`), `pm-batch-summary` and `bd-pm-idle-watch.timer` are paused. `pm-beat` still runs and writes a DIGEST file without messaging. |
| `D5-D` | Idle control seat | Idle per the task brief | No automated nudges |
| `F1-D` | Idle control seat | Idle per the task brief | No automated nudges |
| `F2-D` | Idle control seat | Idle per the task brief | No automated nudges |
| `B4-D` | Idle control seat | Idle per the task brief | No automated nudges |
| `B5-C` | Idle control seat | Idle per the task brief | No automated nudges |
| `D4-A` | Idle control seat | Idle per the task brief | No automated nudges |
| `integrator-C` | Idle control seat | Idle per the task brief | No automated nudges |

**What idle means under O3416.** The automation that would normally wake or poke these seats is off. That includes `autobounce`, `inbox-drain`, `turn-watch`, `stall-guard`, `bd-pm-stall-watch.timer` and `bd-relief-puppeteer.timer`. A seat that stays idle will therefore stay idle until the operator or a manual `say` wakes it. This is expected behaviour, not a fault.

**Capacity available for waking seats.** Pool state was healthy at 22:55:16Z:

| Pool | State | Binding metric | Resets at |
|---|---|---|---|
| A | OK | weekly 27% | 2026-10-20T00:29:59Z |
| B | OK | weekly 40% | 2026-10-20T00:40:00Z |
| C | OK | weekly 33% | 2026-10-20T00:30:00Z |
| D | OK | weekly 41% | 2026-10-19T23:50:00Z |
| codex | OK | 49% of window | 2026-10-25T11:51:11Z |
| AGY | OK | gemini weekly remaining only 6.15% | 2026-10-20T01:41:59Z |
| kimi | OK | 5h 14% used, monthly 46% | 2026-10-20T00:30:15Z |
| local | OK | 8/8 slots, 0% used | n/a |
| grok | UNKNOWN | no quota source | n/a |

> [!WARNING]
> Pool D resets at 23:50Z, about 55 minutes after the snapshot, and carries the highest weekly usage (41%). AGY Gemini weekly remaining is 6.15%, so do not route seat wake-ups to Gemini. Prefer pools A, C or B, or the local tier.

## 3. O3416 resume commands

Run these only when the operator says to resume.

**Crontab (hub)**

```bash
crontab -l | sed 's/^#PAUSED-O3416 //' | crontab -
# or restore the full backup:
crontab /home/mboyle/bd-persist/crontab-backups/crontab.pre-O3416-20261019T2138Z
```

The file gives the `sed` form as `sed -i 's/^#PAUSED-O3416 //'` applied to `crontab -l` output. `sed -i` cannot write to a pipe, so the form above is the safe equivalent. Count 35 uncommented lines afterwards.

**User timers (7)**

```bash
systemctl --user start bd-{fleet-watch,pm-idle-watch,reap,pm-stall-watch,ugrep-reaper,usage-oneshot,usage-probe}.timer
```

**System timers (2)**

```bash
sudo systemctl start bd-rc-enforcer.timer bd-relief-puppeteer.timer
```

**Per-VM crons (23 reachable VMs)**

```bash
crontab ~/.crontab.pre-O3416   # run on each VM
```

Nine VMs were unreachable and untouched: `.137 .122 .171 .181 .195 .197 .198 .224 .35`. Those lines were never commented, so no resume is needed there.

| Resume layer | Count | Backup or restore path |
|---|---|---|
| Hub crontab | 35 commented lines | `/home/mboyle/bd-persist/crontab-backups/crontab.pre-O3416-20261019T2138Z` |
| User timers | 7 | none needed (stopped, not disabled) |
| System timers | 2 | none needed |
| VM crons | 23 VMs, 1-2 lines each | `~/.crontab.pre-O3416` on each VM |

**Recommended resume order.** This order is my suggestion and is not stated in the context.
1. Restore `bd-reap` and `bd-ugrep-reaper`. These are the caveats in the pause file: the hub iowait guard and the registry reap are both off.
2. Restore `bd-rc-enforcer.timer`.
3. Restore the PM heartbeat and watch jobs.
4. Restore `bd-relief-puppeteer.timer`, which drives standby relief.
5. Restore the per-VM crons last.

## 4. Hot-standby local relief takeover models

| Endpoint | Role | Live state in context |
|---|---|---|
| `.83:18080` | Local relief takeover model (standby) | **NOT IN CONTEXT** |
| `.72:8000` | Local relief takeover model (standby) | **NOT IN CONTEXT** |

The context confirms these facts about the local tier:
- It has 4x Tesla T4 (60 GB), at 80.8% VRAM (49644/61440 MiB).
- The active model is `cyberstrike-35b` at 66.47 tok/s. The alternative is Qwen3-Coder-30B-A3B at 77.62 tok/s.
- Slots are 8/8 available across 2 healthy pools. The two pools plausibly map to the two endpoints, but the context does not say so.
- Quota use is 0% over five hours and weekly.

**Takeover considerations**
- `bd-relief-puppeteer.timer` is stopped, so automatic relief is **inactive**. Any takeover during the pause is manual.
- VRAM headroom is about 19%, so avoid loading a second large model on the same GPUs.
- Quick health probe, assuming OpenAI-compatible APIs (not confirmed by the context):
  ```bash
  curl -s -m 5 http://<host>:<port>/v1/models   # .83:18080 and .72:8000
  ```

## 5. Risks and open items

| Risk | Impact | Mitigation |
|---|---|---|
| Hub reboot | Timers restart and messaging resumes against the operator's pause | Disable the timers or avoid rebooting |
| `bd-ugrep-reaper` paused | Orphan `ugrep` processes can accumulate and raise hub iowait | Watch iowait manually |
| `bd-reap` paused | Registry entries go stale | Manual reap on resume |
| `bd-pm-B` state unknown | A PM stall would go undetected, since `bd-pm-stall-watch` is off | Check `bd-pm-B` and the `pm-beat` DIGEST file manually |
| Unreachable VMs (9) | Their crons are unchanged and may still message | Investigate reachability |

**Items to verify live:** the `bd-pm-B` status, the per-seat state of the 7 idle seats, and the health of `.83:18080` and `.72:8000`.
