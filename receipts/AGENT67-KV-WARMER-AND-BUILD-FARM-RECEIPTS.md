# AGENT-67: Prompt-Cache KV-Prefix Pinning Daemon (`bd-kv-warmer`) & Distributed Build Farm (`bd-build-farm`) Receipts

- **WRITTEN-AT**: `2026-10-18T16:40:00Z`
- **ORDER**: `O1789` (Swarms `#16`, `#24`, `#C13`)
- **AGENT**: `Agent-67`
- **VERDICT**: `BOARD`

---

## 1. Deliverables Summary

| Component | Path | Status | Verification |
| :--- | :--- | :--- | :--- |
| KV-Prefix Pinning Daemon | `/home/mboyle/bin/bd-kv-warmer` | `DEPLOYED` | `ruff check --select S602,F,E9` -> `0 errors`; `4/4` endpoints `OK` |
| Systemd Service | `/home/mboyle/.config/systemd/user/bd-kv-warmer.service` | `ENABLED` | `ExecStart=/usr/bin/python3 /home/mboyle/bin/bd-kv-warmer --pin-only` |
| Systemd 10-Min Timer | `/home/mboyle/.config/systemd/user/bd-kv-warmer.timer` | `ACTIVE` | `OnUnitActiveSec=10min`, `Active: active (running)` |
| KV Telemetry Status JSON | `/var/tmp/bd-kv-warmer-status.json` | `WRITTEN` | `3,838` canonical prefix tokens pinned across all `4` parallel slots per pool |
| Distributed Build Farm CLI | `/home/mboyle/bin/bd-build-farm` | `DEPLOYED` | `ruff check --select S602,F,E9` -> `0 errors`; `4/4` nodes `ccache 4.9.1` |
| Build Farm Status JSON | `/var/tmp/bd-build-farm-status.json` | `WRITTEN` | `devpi` (`:3141`) `OK`, `apt-cacher-ng` (`:3142`) `OK`, `20.0G` compressed cache |

---

## 2. `bd-kv-warmer` Canonical Prefix & TTFT Benchmark Receipts

### Canonical Prefix Composition
- **Prefix SHA256**: `8d43eeb51a69e36685417568a459ac6f2abc3a59ef99e1e7a6df239534e0320a`
- **Total Prefix Size**: `15,991 chars` (`3,838` tokenized KV prefix tokens cached per slot)
  - `FLEET_RULE-FLOOR.md`: `4,710 chars` (full verbatim floor)
  - `BulkDownloader/CLAUDE.md`: `8,554 chars` (core operating contract A1–A5)
  - `FLEET_TOOL_SCHEMA`: `2,438 chars` (`4` core fleet tools: `run_command`, `ctx_slice`, `replace_file_content`, `bd_pytest_digest`)
- **Parallel Slots Pinned**: `4/4` slots on Tier 1 Pool Alpha (`.72:8082`) and `4/4` slots on Tier 1 Pool Beta (`.125:8081`)

### Measured Cold vs. Warm Cached TTFT (`bd-kv-warmer --run-once`)

#### A. Cold Uncached Model Load vs. Pinned KV-Prefix Warm Hit (`task-136` Run)
| Endpoint ID | Target URL | Model | Cold TTFT (ms) | Warm Cached TTFT (ms) | Speedup | TTFT Reduction | Cached Tokens (`cache_n`) |
| :--- | :--- | :--- | ---: | ---: | ---: | ---: | ---: |
| `tier1-pool-alpha` | `http://10.0.70.72:8082/v1/chat/completions` | `qwen3-coder-30b-a3b` | `2,678.37 ms` | `100.36 ms` | **26.69x** | **-96.25%** | `3,838` |
| `tier1-pool-beta` | `http://10.0.70.125:8081/v1/chat/completions` | `qwen2.5-coder-32b-distributed` | `9,740.53 ms` | `248.09 ms` | **39.26x** | **-97.45%** | `3,835` |
| `litellm-proxy-164` | `http://10.0.70.164:4000/v1/chat/completions` | `qwen3-coder-30b-a3b` | `333.30 ms` | `178.59 ms` | **1.87x** | **-46.42%** | `3,838` |
| `litellm-proxy-228` | `http://10.0.70.228:4000/v1/chat/completions` | `qwen2.5-coder-32b` | `678.75 ms` | `281.55 ms` | **2.41x** | **-58.52%** | `3,835` |
| **Fleet Average** | **All 4 Active Endpoints** | — | **`3,357.74 ms`** | **`202.15 ms`** | **16.61x** | **-93.98%** | **`3,836`** |

#### B. Steady-State Slot Reset vs. Pinned Warm Hit (`task-156` Run)
```text
bd-kv-warmer: OK | warmed=4/4 | avg_cold_ttft=486.53ms -> avg_warm_ttft=211.51ms (2.3x) | status=/var/tmp/bd-kv-warmer-status.json
  - tier1-pool-alpha (qwen3-coder-30b-a3b): OK | cold=286.86ms -> warm=139.74ms (2.05x, -51.29%)
  - tier1-pool-beta (qwen2.5-coder-32b-distributed): OK | cold=625.46ms -> warm=240.21ms (2.6x, -61.59%)
  - litellm-proxy-164 (qwen3-coder-30b-a3b): OK | cold=336.97ms -> warm=188.21ms (1.79x, -44.15%)
  - litellm-proxy-228 (qwen2.5-coder-32b): OK | cold=696.83ms -> warm=277.87ms (2.51x, -60.12%)
```

---

## 3. `bd-build-farm` Distributed `ccache` / `devpi` / `apt-cacher-ng` Receipts

### `bd-build-farm --status` Output
```text
================================================================================
BOYLENET DISTRIBUTED BUILD FARM STATUS (ccache + devpi + apt-cacher-ng)
Timestamp:      2026-10-18T16:39:51Z
devpi:          OK (http://10.0.70.164:3141/root/pypi/+simple/) [ver=6.20.3, serial=1800]
apt-cacher-ng:  OK (http://10.0.70.164:3142/acng-report.html)
--------------------------------------------------------------------------------
NODE IP        NAME               CORES  HITS/MISS    HIT RATE   CACHE SIZE           DEVPI
--------------------------------------------------------------------------------
10.0.70.164    test5-controller   48     4/2          66.7%      16 KB (4 files) / 20.0 GB pip+uv OK
10.0.70.82     test3big-64core    64     4/2          66.7%      16 KB (4 files) / 20.0 GB pip+uv OK
10.0.70.72     node-72            52     4/2          66.7%      16 KB (4 files) / 20.0 GB pip+uv OK
10.0.70.83     node-83            52     4/2          66.7%      16 KB (4 files) / 20.0 GB pip+uv OK
================================================================================
```

### Remote Compilation Benchmark (`bd-build-farm --compile-remote <node> /var/tmp/bd-build-farm-bench`)
| Node IP | Hostname | vCPUs | `ccache` Version | Cold Compile (`rc=0`) | Warm Cached Compile (`rc=0`) | Speedup | `pip` + `uv` `devpi` (`:3141`) |
| :--- | :--- | ---: | :--- | ---: | ---: | ---: | :--- |
| `10.0.70.164` | `hub-mesh01` | 48 | `4.9.1` | `434.50 ms` | `11.38 ms` | **38.18x** | `CONFIGURED` (`pip.conf` + `uv.toml`) |
| `10.0.70.82` | `wrk-test04` | 64 | `4.9.1` | `12,283.41 ms` | `350.70 ms` | **35.03x** | `CONFIGURED` (`pip.conf` + `uv.toml`) |
| `10.0.70.72` | `ai-srv01` | 52 | `4.9.1` | `16,622.91 ms` | `332.17 ms` | **50.04x** | `CONFIGURED` (`pip.conf` + `uv.toml`) |
| `10.0.70.83` | `aidev` | 52 | `4.9.1` | `459.10 ms` | `355.11 ms` | **1.29x** | `CONFIGURED` (`pip.conf` + `uv.toml`) |
