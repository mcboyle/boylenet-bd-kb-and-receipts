# AGENT-55 RECEIPT: Autonomous Cross-Pool Quota Load-Balancer & Failover Daemon (`bd-pool-autobalancer`)

- **Timestamp (UTC)**: `2026-10-18T15:40:42Z`
- **Agent**: `Agent-55 (Swarm #7)`
- **Directive**: O1740 Dynamic Capability Routing + O1789 Autonomous Cross-Pool Failover (`launch 6-14`)
- **CLI & Daemon Binary**: `/home/mboyle/bin/bd-pool-autobalancer` (symlinked at `/usr/local/bin/bd-pool-autobalancer`)
- **Systemd Unit**: `/etc/systemd/system/bd-pool-autobalancer.service` (`enabled`, `active (running)`)
- **Live Telemetry Snapshot**: `/var/tmp/bd-pool-autobalancer.json` (updated every 30s)

---

## 1. Architecture & O1740 Dynamic Capability Routing Implementation

1. **Telemetry & Live Health Ingestion**:
   - Continuously parses `/home/mboyle/bd-persist/POOL_STATE.tsv` (`state`, `pct` / `five_hour_pct`, `weekly_pct`, `resets_at`, `five_hour_remaining_pct`, `quota_observed_at`).
   - Performs live health probes across:
     - `Claude ABCD` (`/home/mboyle/.claude-{a,b,c,d}/.credentials.json` freshness).
     - `codex` (CLI binary, `~/.codex/config.toml`, `~/.codex/hooks.json`).
     - `kimi` (CLI binary / gateway `kimi-for-coding`, monthly quota extraction from evidence).
     - `AGY` (`~/.gemini/config/hooks.json` shield status + subagent quota).
     - `local-4xt4` (`http://10.0.70.164:4010/health` GPU Orchestrator + `http://10.0.70.228:4000/health/liveliness` LiteLLM Gateway).
   - Tracks active heavy-lifter seats via `tmux list-sessions` against the O1740 30-seat global cap.

2. **Role-Tier Routing & Autonomous Failover (`--recommend <role>`)**:
   - **Command Squad** (`integrator`, `dispatch`, `audit`, `adjudicator`, `trainer`, `lensrouter`, `pm`):
     - Picks the `OK` Opus-tier pool (`A`, `B`, `C`, `D`) with the lowest `five_hour_pct` (`<85%`), tie-breaking by lowest `weekly_pct`, active seat count, and role affinity.
     - **Autonomous Failover**: When all Claude pools are `>=85%` or `OFF-PACE`/`LIMITED`/`UNKNOWN`, fails over cleanly to `codex` (`gpt-6-astra`) and then `hermes` (`mimo-v2.6-pro-1024b` / `glm-5.3-753b`).
   - **Heavy Lifters** (`worker`, `builder`, `fixer`, `review-*`):
     - Picks the most capable admitting pool (`five_hour_pct < 80%`), balancing load across `Claude ABCD` (`claude-opus-5-5`), `Codex` (`gpt-6-sol` / `gpt-6-astra`), `Kimi` (`kimi-for-coding`), and `Local 4xT4` (`qwen3-coder-30b-a3b` / `swift-qwen3.8-flash-next`).
   - **Lightweight Investigators** (`investigator`, `probe`, `cavecrew-*`):
     - Picks the fast-model pool with the highest idle quota (`max(100 - five_hour_pct)`) or Local Tier 1 (`local-4xt4` running `qwen3-coder-30b-a3b` / `swift-qwen3.8-flash-next` at `$0.00` marginal cost).

---

## 2. Systemd Service Verification (`bd-pool-autobalancer.service`)

```text
● bd-pool-autobalancer.service - Boylenet Autonomous Cross-Pool Quota Load-Balancer & Failover Daemon (O1740 / O1789)
     Loaded: loaded (/etc/systemd/system/bd-pool-autobalancer.service; enabled; preset: enabled)
     Active: active (running) since Sun 2026-10-18 15:40:41 UTC
   Main PID: 1265022 (python3)
     CGroup: /system.slice/bd-pool-autobalancer.service
             └─1265022 /usr/bin/python3 /home/mboyle/bin/bd-pool-autobalancer --daemon --interval 30
```

---

## 3. CLI Verification Receipts

### 3.1 `bd-pool-autobalancer --status`

```text
=== BOYLENET CROSS-POOL QUOTA AUTOBALANCER (O1740 / O1789) @ 2026-10-18T15:40:42Z ===
Source: /home/mboyle/bd-persist/POOL_STATE.tsv | Soft Cap: <80% | Cmd Failover: >=85% | Global Heavy-Lifter Seats: 0/30 (30 avail)

POOL         STATE      5H USED    WK/MO USED   HEADROOM(<80%)   IDLE %    RESETS AT              LIVE HEALTH & MODELS                                    
----------------------------------------------------------------------------------------------------------------------------------------------------------
A            OK         0.0%       2.0% (wk)    +80.0%           100.0%    2026-10-18T17:30:00Z   ONLINE (.claude-a, cred=ok)
B            OK         0.0%       19.0% (wk)   +80.0%           100.0%    2026-10-18T17:29:59Z   ONLINE (.claude-b, cred=ok)
C            OK         0.0%       0.0% (wk)    +80.0%           100.0%    2026-10-18T17:30:00Z   ONLINE (.claude-c, cred=ok)
D            OK         0.0%       13.0% (wk)   +80.0%           100.0%    2026-10-18T17:29:59Z   ONLINE (.claude-d, cred=ok)
codex        OK         0.0%       0.0% (wk)    +80.0%           100.0%    2026-10-25T11:51:11Z   ONLINE (cli=ok, hooks=ok)
kimi         OK         0.0%       40.0% (mo)   +80.0%           100.0%    2026-10-18T13:54:14Z   ONLINE (model=kimi-for-coding, cli=ok)
AGY          OK         18.5%      26.9% (wk)   +61.5%           81.5%     -                      ONLINE (shield=active, subagent-pool=ready)
local-4xt4   OK         0.0%       0.0% (wk)    +80.0%           100.0%    -                      ONLINE (:4010 tier1/qwen3-coder-30b-a3b 36.2ms, :4000 OK 2.6ms)

--- O1740 RECOMMENDED ROUTING & AUTONOMOUS FAILOVER BY ROLE TIER ---
1. Command Squad (integrator, dispatch, audit, adjudicator, trainer, lensrouter, pm):
   -> Primary: C (model=claude-opus-5-5, 5h=0.0%, wk=0.0%) | Failover: codex (gpt-6-astra) -> hermes (mimo-v2.6-pro-1024b / glm-5.3-753b)
2. Heavy Lifters (worker, builder, fixer, review-*):
   -> Builder/Worker Primary: codex (gpt-6-sol) | Review Lens Primary: codex (gpt-6-astra)
   -> Balanced 4-Family Pool Rotation: Codex=codex:gpt-6-sol (5h=0.0%), Local 4xT4=local-4xt4:qwen3-coder-30b-a3b (5h=0.0%), Claude ABCD=C:claude-opus-5-5 (5h=0.0%), Kimi=kimi:kimi-for-coding (5h=0.0%)
3. Lightweight Investigators (investigator, probe, cavecrew-*):
   -> Primary: local-4xt4 (model=qwen3-coder-30b-a3b, idle=100.0%, cost=$0.00)
State JSON: /var/tmp/bd-pool-autobalancer.json
```

### 3.2 `bd-pool-autobalancer --recommend integrator` (Command Squad + Failover Test)

```text
[bd-pool-autobalancer] Role='integrator' Tier='command_squad'
  RECOMMENDED_POOL   : C
  RECOMMENDED_HARNESS: claude
  RECOMMENDED_MODEL  : claude-opus-5-5
  RECOMMENDED_EFFORT : high
  POOL_STATE         : OK (5h=0.0%, headroom_to_80=80.0%)
  RATIONALE          : O1740 Command Squad: Opus-tier pool 'C' is OK with lowest five_hour_pct=0.0% (weekly=0.0%, headroom_to_80=+80.0%).
  LAUNCH_CMD         : /home/mboyle/bd-persist/harness/bd-launch-role.sh integrator C
  FAILOVER_CHAIN     : codex:gpt-6-astra(READY) -> hermes:mimo-v2.6-pro-1024b(READY)
```

Simulated Claude Exhaustion (`--recommend integrator --simulate-claude-exhausted`):

```text
[bd-pool-autobalancer] Role='integrator' Tier='command_squad'
  RECOMMENDED_POOL   : codex
  RECOMMENDED_HARNESS: codex
  RECOMMENDED_MODEL  : gpt-6-astra
  RECOMMENDED_EFFORT : high
  POOL_STATE         : OK (5h=0.0%, headroom_to_80=80.0%)
  RATIONALE          : O1740 Command Squad AUTONOMOUS FAILOVER: all Claude ABCD pools >=85% or non-OK; failing over cleanly to codex (gpt-6-astra).
  LAUNCH_CMD         : /home/mboyle/bd-persist/harness/bd-launch-role.sh integrator codex --model gpt-6-astra --effort high
  FAILOVER_CHAIN     : codex:gpt-6-astra(READY) -> hermes:mimo-v2.6-pro-1024b(READY)
```

### 3.3 `bd-pool-autobalancer --recommend builder` (Heavy Lifters)

```text
[bd-pool-autobalancer] Role='builder' Tier='heavy_lifter'
  RECOMMENDED_POOL   : codex
  RECOMMENDED_HARNESS: codex
  RECOMMENDED_MODEL  : gpt-6-sol
  RECOMMENDED_EFFORT : medium
  POOL_STATE         : OK (5h=0.0%, headroom_to_80=80.0%)
  RATIONALE          : O1740 Heavy Lifter Load-Balancer: pool 'codex' (Codex, model=gpt-6-sol) admits under <80% soft cap with 5h=0.0%, headroom=+80.0%, load_score=0.0 (balanced across Claude ABCD, Codex, Kimi, Local 4xT4).
  LAUNCH_CMD         : /home/mboyle/bd-persist/harness/bd-launch-role.sh builder codex --model gpt-6-sol
  BALANCED_ROTATION  : Codex=>codex(gpt-6-sol, 5h=0.0%) | Local 4xT4=>local-4xt4(qwen3-coder-30b-a3b, 5h=0.0%) | Claude ABCD=>C(claude-opus-5-5, 5h=0.0%) | Kimi=>kimi(kimi-for-coding, 5h=0.0%)
```

### 3.4 `bd-pool-autobalancer --recommend investigator` (Lightweight Investigators)

```text
[bd-pool-autobalancer] Role='investigator' Tier='lightweight_investigator'
  RECOMMENDED_POOL   : local-4xt4
  RECOMMENDED_HARNESS: hermes
  RECOMMENDED_MODEL  : qwen3-coder-30b-a3b
  RECOMMENDED_EFFORT : low
  POOL_STATE         : OK (5h=0.0%, headroom_to_80=80.0%)
  RATIONALE          : O1740 Lightweight Investigator: pool 'local-4xt4' (model=qwen3-coder-30b-a3b) selected with max idle quota=100.0% (5h_used=0.0%, $0 marginal cost Tier-1 fast probe).
  LAUNCH_CMD         : /home/mboyle/bd-persist/harness/bd-launch-role.sh investigator hermes --model qwen3-coder-30b-a3b --effort low
```
