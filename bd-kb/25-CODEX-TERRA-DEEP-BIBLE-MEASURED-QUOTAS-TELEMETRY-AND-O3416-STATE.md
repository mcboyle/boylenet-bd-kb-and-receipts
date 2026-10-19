# BD-KB Volume 25: Codex Terra Measurement Bible — Pool Quotas, Telemetry Sensors, O3416 Paused Crons & 24-VM Census
> **Author**: `Spec-225` (`codex (gpt-5.6-terra)`)
> **Generated At**: `2026-10-19T22:57:00Z`
> **Scope**: Strictly Read-Only Infrastructure, Tools, Services, LLMs & PM Catch-Up Documentation (`O3416` Compliant)

---

# BD-KB Volume 25: Codex Terra Measurement Bible — Pool Quotas, Telemetry Sensors, O3416 Paused Crons & 24-VM Census

**Evidence snapshot:** 2026-10-19, principally `2026-10-19T22:55Z`  
**Scope:** read-only interpretation of `POOL_STATE.tsv`, the telemetry publication surfaces, O3416 pause inventory, and the fleet host census.  
**Authority boundary:** This is a measurement guide, not an operational runbook. It describes what the recorded evidence means and what it does not establish.

## 1. Measurement model

Codex Terra’s observability has four distinct layers that must not be conflated:

1. **Quota state** — provider and local-capacity availability, recorded in `POOL_STATE.tsv`.
2. **Telemetry collection and publication** — represented by `bd-telemetry-daemon.py`, `STATUS.txt`, and `/var/tmp/fleet_telemetry_live.json`.
3. **Automation state** — messaging-related scheduled work paused under O3416, separately from non-messaging measurement work that continues.
4. **Compute census** — configured VM inventory, including live targets, Windows oracle machines, CI hosts, spare capacity, and explicitly cold systems.

A healthy-looking measurement value is only meaningful when its timestamp, source, and semantic unit are understood. In particular:

- “Percent used” is not “percent remaining.”
- A per-window quota is not equivalent to a weekly or monthly quota.
- `OK` means the source’s recorded condition passed its own current interpretation; it does not mean unlimited capacity.
- `UNKNOWN` means no defensible measurable source exists, not necessarily that the service is down.
- A configured host list is not a proof of reachability at the instant of the census.
- A stopped timer is not disabled and may resume after a hub reboot.

The source evidence supports a clear rule: use the freshest applicable value, preserve its observation time, and never fabricate a reset time, remaining capacity, or health conclusion where the telemetry source does not provide one.

## 2. Primary quota ledger: `POOL_STATE.tsv`

`/home/mboyle/bd-persist/POOL_STATE.tsv` is the compact cross-pool quota ledger. Its rows identify a pool and supply current state, evidence text, usage and remaining-capacity fields, reset information where known, and a source observation time.

### 2.1 Schema and field meanings

| Column | Meaning | Interpretation rule |
|---|---|---|
| `pool` | Logical provider or capacity pool identifier. | Treat each row independently; do not aggregate unlike providers without preserving their units. |
| `state` | Current recorded condition, such as `OK` or `UNKNOWN`. | It is a measurement state, not a promise of service availability. |
| `since` | Timestamp from which the displayed state has held. | Distinct from the most recent measurement time. |
| `evidence` | Human-readable provenance, measurements, model or OAuth details, and caveats. | This is essential context; do not rely on `pct` alone. |
| `pct` | Usually primary-window usage percentage. | The exact meaning varies by provider; read alongside `evidence`. |
| `resets_at` | Known reset timestamp for the primary-window reading. | `-` means no reset value was measured or applicable. |
| `weekly_pct` | Weekly or equivalent long-window usage percentage when exposed. | This can represent a provider’s seven-day or aggregate weekly limit. |
| `five_hour_remaining_pct` | Remaining percentage for a five-hour-style window. | This is a remaining value, unlike `pct` in the recorded provider rows. |
| `gemini_five_hour_remaining_pct` | AGY Gemini five-hour remaining capacity. | Applicable only where a separate Gemini measurement exists. |
| `gemini_weekly_remaining_pct` | AGY Gemini weekly remaining capacity. | A low value can constrain AGY despite ample short-window capacity. |
| `claude_gpt_five_hour_remaining_pct` | AGY Claude-plus-GPT five-hour remaining capacity. | Separate from Gemini; do not substitute one for the other. |
| `claude_gpt_weekly_remaining_pct` | AGY Claude-plus-GPT weekly remaining capacity. | Long-window remaining capacity for that combined category. |
| `quota_observed_at` | Timestamp when quota data was actually observed. | This is the principal freshness field for quota decisions. |

A dash (`-`) is not zero. It means no value is supplied in that field. Treating unavailable measurements as exhaustion, abundance, or a calculated value would corrupt the ledger.

### 2.2 Freshness and source confidence

At the principal snapshot, the main provider rows were measured at or near `2026-10-19T22:55:16Z`:

- Claude A through D: exact observation at `22:55:16Z`.
- Codex: exact observation at `22:55:16Z`.
- AGY: exact observation at `22:55:16Z`.
- Local: exact observation at `22:55:16Z`.
- Kimi: exact observation at `22:55:10Z`.
- Grok: quota source remains unavailable; its latest descriptive observation was `22:55:01Z`.

For A–D, the evidence explicitly says the cache was `0s old` and OAuth was live-verified. For Codex, the evidence says the freshest fleet-wide record was `0s old`. For AGY, the current reading and 429-window measurement were each `0s old`. These statements are stronger than a generic successful refresh: they identify live source validation and no meaningful cache age at measurement time.

That freshness does not extend indefinitely. Any later action must treat these values as a dated snapshot rather than current truth.

## 3. Provider-by-provider quota interpretation

### 3.1 Claude pools A through D

The four Claude pools are all `OK`. Their primary `pct` values represent five-hour usage and their `weekly_pct` values represent `weekly_all/seven_day` usage.

| Pool | Five-hour used | Five-hour remaining | Weekly used | Reset time |
|---|---:|---:|---:|---|
| A | 2% | 98% | 27% | 2026-10-20T00:29:59Z |
| B | 3% | 97% | 40% | 2026-10-20T00:40:00Z |
| C | 3% | 97% | 33% | 2026-10-20T00:30:00Z |
| D | 4% | 96% | 41% | 2026-10-19T23:50:00Z |

The short-window picture is uniformly strong: every pool has at least 96% remaining in its five-hour window. The weekly picture is materially different. Pool A has the lowest listed weekly use at 27%; Pool D has the highest at 41%; B follows closely at 40%; and C is at 33%.

The appropriate reading is not that A is universally “best,” but that it has the lowest recorded weekly use in this snapshot while maintaining 98% five-hour availability. D’s reset is earliest, at `23:50:00Z`; this means its current five-hour usage should not be compared with the others without noting that its window turns over sooner.

### 3.2 Codex pool

The `codex` row is `OK` with:

- Primary-window usage: 49%.
- Five-hour remaining capacity: 51%.
- Weekly-equivalent displayed percentage: 49%.
- Reset: `2026-10-25T11:51:11Z`.
- Evidence: `codex rate_limits primary 49.0% of its window`.
- Freshness: `0s old fleet-wide` at `2026-10-19T22:55:16Z`.

The ledger records both `pct` and `weekly_pct` as 49, but its evidence calls this a primary `rate_limits` window. The table alone does not prove that the second 49 is a separately measured weekly limit. Therefore, use the row as a measured Codex primary-window status with 51% remaining, and avoid asserting a distinct weekly Codex quota model absent separate evidence.

The reset date is several days later than the Claude five-hour resets. This is a provider-specific rate-limit window and must not be treated as a comparable five-hour reset.

### 3.3 AGY pool

The AGY row is `OK`, but it is inherently multi-provider and must be interpreted through its split remaining-capacity fields:

| AGY metric | Remaining at `22:55:16Z` |
|---|---:|
| Gemini five-hour | 89.6784% |
| Gemini weekly | 6.1533% |
| Claude + GPT five-hour | 99.3644% |
| Claude + GPT weekly | 99.1730% |

The row’s `pct` is `10.3216`, consistent with Gemini five-hour usage as the complement of 89.6784% remaining. Its `weekly_pct` is `93.8467`, consistent with Gemini weekly usage as the complement of only 6.1533% remaining. These compact aggregate fields should be read as the constrained Gemini perspective, while the explicit named columns carry the real routing detail.

This means AGY has excellent short-window availability for both recorded categories and excellent Claude-plus-GPT weekly capacity, but Gemini’s weekly capacity is nearly depleted. The five-hour value alone would hide that risk. A measurement-aware dispatcher must preserve the distinction:

- Gemini is usable in the short window but has only 6.1533% weekly remaining.
- Claude-plus-GPT has 99.3644% five-hour remaining and 99.1730% weekly remaining.
- The evidence reports no 429 in the measured window, but absence of a 429 is not evidence that no quota limit exists.

The listed AGY reset is `2026-10-20T01:41:59Z`. It should be treated as the recorded primary reset reference, not necessarily a reset for every provider-specific weekly budget.

### 3.4 Grok pool

The `grok` row is `UNKNOWN`. This is a deliberately precise state:

> No measured Grok quota source exists because the Grok CLI exposes per-session token and cost spend through `grok usage <session>`, but not remaining quota, limit, or reset information.

Therefore:

- No remaining quota can be calculated.
- No percentage can be inferred from session spend.
- No reset time can be projected.
- No comparison against the provider pools is valid.
- `UNKNOWN` is not equivalent to `0%`, `100%`, degraded service, or an outage.

The state began at `2026-10-19T05:25:01Z`; the lack of a quota source was observed again at `22:55:01Z`. The later observation confirms persistence of the instrumentation gap, not a quota value.

### 3.5 Local capacity pool

The `local` row is `OK` and represents physical/self-hosted inference capacity rather than a billed remote-provider quota.

At `22:55:16Z`, it reports:

- Four Tesla T4 GPUs.
- 60 GiB GDDR6 total.
- VRAM use of 49,644 / 61,440 MiB, or 80.8%.
- 1,730 GiB DDR4.
- `tier1` active model: `cyberstrike-35b`.
- CyberStrike-35B throughput: 66.47 tokens/s.
- Qwen3-Coder-30B-A3B throughput: 77.62 tokens/s.
- Eight of eight slots available across two healthy pools.
- Five-hour used: 0%.
- Weekly used: 0%.
- Five-hour remaining: 100%.

The quota-like zero values should be understood as internal availability semantics, not an external provider’s hard rate allowance. VRAM was noticeably higher than the earlier `22:25:15Z` audit, which recorded 67.06% or 41,200 / 61,440 MiB. Yet the entry still says all eight slots were available. This illustrates why single-resource utilization and scheduling capacity are separate sensors: high VRAM use does not automatically prove that no dispatch slot is available.

### 3.6 Kimi pool

The Kimi row is `OK` with:

- Five-hour usage: 14%.
- Monthly usage: 46%.
- Reset time: `2026-10-20T00:30:15Z`.
- Observation: `2026-10-19T22:55:10Z`.

The evidence specifically identifies monthly usage rather than weekly usage. Since `weekly_pct` is `-`, it would be incorrect to call the 46% figure a weekly amount. Kimi has 86% remaining in the observed five-hour view; no monthly remaining field is directly stated, though the arithmetic complement would be 54% if the monthly percentage is interpreted conventionally as used capacity. The safer canonical record is “46% monthly used.”

## 4. Telemetry sensor surfaces

### 4.1 `bd-telemetry-daemon.py`

`bd-telemetry-daemon.py` is named as a telemetry component in the measurement system. The supplied evidence does not include its implementation, invocation interval, schema, access control, or failure behavior. It must therefore be described as a telemetry daemon interface or collector without inventing its detailed operation.

Its existence should not be confused with the authoritative meaning of an individual value. The provenance of every metric remains the source specified in `POOL_STATE.tsv` evidence, `STATUS.txt`, or the live JSON record. A daemon can collect, normalize, and publish data, but it cannot convert an unavailable source—such as Grok quota limits—into a valid measurement.

### 4.2 `STATUS.txt`

`STATUS.txt` is a human-readable status surface in the requested measurement set. Its contents were not included in the source material. Consequently, this volume does not assign it a fixed schema or claim specific current values from it.

The correct treatment is:

- Use it as a readable status artifact when its timestamp and source attribution are visible.
- Do not override a fresher, source-specific `POOL_STATE.tsv` `quota_observed_at` solely because `STATUS.txt` is easier to read.
- Preserve any disagreement rather than reconciling it by guesswork.
- Consider a status line incomplete if it lacks an observation time, source identity, or enough units to establish whether it represents used or remaining capacity.

### 4.3 `/var/tmp/fleet_telemetry_live.json`

`/var/tmp/fleet_telemetry_live.json` is a machine-readable live telemetry surface. The source context does not provide its JSON schema or a content snapshot. No field names, nested objects, timestamps, or semantics beyond its name may be asserted from this evidence.

A correct consumer should validate at least:

1. File observation timestamp or per-record measurement timestamp.
2. Whether each percentage is used or remaining.
3. Provider/pool identity.
4. Reset timestamp and the window to which it applies.
5. Sensor health, cache age, and source failure representation.
6. Whether missing data is encoded as absent fields, null, dash-like strings, or explicit unknown status.

The path is under `/var/tmp`, which commonly suggests volatile runtime publication. The context does not establish persistence guarantees, so it should be treated as a live view rather than archival evidence unless other evidence says otherwise.

## 5. O3416: paused messaging crons and timers

O3416 paused every hub cron or timer that messages the PM or any seat “until I say so.” This is an automation and notification pause, not a full telemetry shutdown.

### 5.1 Hub crontab pause inventory

Thirty-five crontab lines were commented with the literal prefix:

```text
#PAUSED-O3416
```

The paused jobs include:

- Status and autobounce work.
- Limit-reset and limit-watch work.
- Usage probe and usage monitor work.
- Flow, prep-rescan, return-path, turn-watch, stale-gate, and main-guard work.
- PM heartbeat, including `pm-inbox-guard`.
- Review claims, landed, land notification, and auto-merge work.
- Host watch, inbox drain, and backlog automation.
- Fleet quota probe, usage forecaster, Kimi usage watch, and token heatmap.
- Lens router, RAG probe, auto-compact, and council claim keeper.
- Law SHA watcher, stall guard, supply guard, and tmux socket pruning.

The paused state should be interpreted narrowly and precisely: these job lines are commented, so their scheduled messaging or orchestration behavior is stopped. It does not prove that no data exists, that the relevant scripts cannot be run manually, or that alternate still-running telemetry systems are inactive.

### 5.2 Stopped user and system timers

Stopped but **not disabled** user timers are:

- `bd-fleet-watch`
- `bd-pm-idle-watch`
- `bd-reap`
- `bd-pm-stall-watch`
- `bd-ugrep-reaper`
- `bd-usage-oneshot`
- `bd-usage-probe`

Stopped but **not disabled** system timers are:

- `bd-rc-enforcer`
- `bd-relief-puppeteer`

The “not disabled” qualification matters. A hub reboot may restart these timers. The O3416 pause is therefore not durable reboot-proof disablement.

Two operational caveats have direct measurement implications:

- `bd-ugrep-reaper` is paused, including its orphan-ugrep killer and hub I/O-wait guard role.
- `bd-reap` registry reaping is paused.

A measurement system can remain active while housekeeping or protection timers are paused, but stale processes and uncollected registry state may eventually affect interpretation of activity counts or service health.

### 5.3 Still-running non-messaging systems

The following systems remain running because they are recorded as non-messaging:

- Backups.
- Log rotation.
- Staleness monitoring.
- `pm-beat` producing a DIGEST file.
- CI guard.
- Cut accounting.
- Main regeneration.
- Load gate.
- Cost collection.
- RAG and memory indexers.
- Pool usage refresher.
- Authentication sync.
- Cplus reaper/token push.
- KV warmer.
- Hot pin.
- Swarm telemetry.
- Persistence loop.
- Nightly maintenance.

The key measurement conclusion is that O3416 does not invalidate all telemetry. Pool usage refresh, swarm telemetry, cost collection, staleness monitoring, and the persistence loop are explicitly still running. However, notification and scheduled probe paths listed among the paused jobs may no longer produce their normal alerts or periodic outputs. Silence from PM-facing automation is therefore not evidence of normal fleet health.

### 5.4 Per-VM cron state

On 23 reachable fleet VMs, every `bd-`, `say`, or `inbox` cron line was commented with `#PAUSED-O3416`. Most affected hosts had one or two such lines, commonly `bd-tmux-socket-prune`. Each has a backup at:

```text
~/.crontab.pre-O3416
```

The following VMs were unreachable and untouched:

- `.137`
- `.122`
- `.171`
- `.181`
- `.195`
- `.197`
- `.198`
- `.224`
- `.35`

Untouched does not mean their cron entries are active, healthy, or stale; it only records that the O3416 commenting operation did not reach them.

## 6. Live 24-VM census

The cited host configuration contains a broader inventory, but its live Linux census identifies 24 active Linux VMs. Cold entries are explicitly marked `#COLD-O3336` and must not be counted as active targets.

### 6.1 Active Linux fleet hosts

| Host | Address | Notes |
|---|---|---|
| `test5` | 10.0.70.164 | `local`; hub-oriented local infrastructure evidence is associated with this host. |
| `test2` | 10.0.70.95 | Active Linux host. |
| `test3` | 10.0.70.80 | Active Linux host. |
| `test6` | 10.0.70.249 | Active Linux host. |
| `test4` | 10.0.70.85 | Active Linux host. |
| `test7` | 10.0.70.84 | Active Linux host. |
| `bd1` | 10.0.70.51 | Active Linux host. |
| `bd2` | 10.0.70.52 | Active Linux host. |
| `bd3` | 10.0.70.53 | Active Linux host. |
| `bd4` | 10.0.70.54 | Active Linux host. |
| `fresh149` | 10.0.70.149 | Active Linux host. |
| `test3big` | 10.0.70.82 | Active Linux host. |
| `spare12` | 10.0.70.183 | Active Linux host. |
| `spare7` | 10.0.70.120 | Active Linux host. |
| `spare8` | 10.0.70.184 | Active Linux host. |
| `spare5` | 10.0.70.185 | Active Linux host. |
| `spare6` | 10.0.70.186 | Active Linux host. |
| `spare9` | 10.0.70.187 | Active Linux host. |
| `spare2` | 10.0.70.193 | Active Linux host. |
| `test1` | 10.0.70.194 | Active Linux host. |
| `spare4` | 10.0.70.81 | Active Linux host. |
| `spare10` | 10.0.70.196 | Active Linux host. |
| `cir1` | 10.0.70.121 | CI-runner clone. |
| `cir2` | 10.0.70.86 | CI-runner clone. |

### 6.2 Related Windows oracle systems

The configuration also names two Windows VMs that are not part of the 24 active Linux count:

| Host | Address | Role |
|---|---|---|
| `bittorrent` | 10.0.70.181 | Windows VM oracle. |
| `battlestation` | 10.0.10.137 | Windows GPU oracle. |

Their presence matters to telemetry topology, but platform-specific reachability and scheduled-task behavior must not be inferred from Linux-host evidence.

### 6.3 Explicitly cold or excluded inventory

The following entries carry `#COLD-O3336` in the host inventory and are excluded from the active 24-Linux-VM census:

- `bd` — `10.0.70.50`
- `spare11` — `10.0.70.195`
- `spare1` — `10.0.70.197`
- `seatc1` — `10.0.70.35`
- `seatc2` — `10.0.70.171`
- `spare3` — `10.0.70.122`
- `spare` — `10.0.70.224`
- `kvcache-cx1` — `10.0.70.198`

This designation aligns with the O3416 unreachable-and-untouched list for `.35`, `.122`, `.171`, `.195`, `.197`, `.198`, and `.224`. The overlap is evidence that cold or unreachable status can affect automation reachability; it is not evidence that every cold host is permanently unavailable.

## 7. Canonical interpretation rules

1. Prefer `quota_observed_at` over `since` when judging metric freshness.
2. Read `evidence` before routing on `pct`.
3. Keep used and remaining percentages separate.
4. Do not calculate a quota from Grok session spend.
5. Do not treat `-` as zero.
6. Do not compare all reset timestamps as though they identify the same provider window.
7. Preserve AGY’s Gemini and Claude-plus-GPT values as separate limits.
8. Treat local VRAM utilization, token throughput, and available slots as different sensors.
9. O3416 pauses messaging automation, not all observation systems.
10. A stopped timer may return on reboot because it was not disabled.
11. A host-list census is configuration evidence; it is not a real-time network probe.
12. When telemetry surfaces disagree, retain the conflict with timestamps rather than silently selecting a preferred answer.

This measurement discipline keeps Codex Terra’s status reporting honest: exact where the evidence is exact, cautious where a source is unavailable, and explicit about the difference between capacity, telemetry, automation, and configured fleet inventory.
