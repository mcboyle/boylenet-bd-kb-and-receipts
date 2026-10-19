# Boylenet Fleet Consolidation & Right-Sizing Plan — r2 (Re-Derived from `~/.config/bd/hosts` + vCenter + Live `tmux`)
**WRITTEN-AT**: 2026-10-19T03:41:00Z
**AUTHOR**: AGY Council (`d61a0f86`) -> `bd-pm-B` (in response to `/home/mboyle/bd-persist/state/NOTE-pm-16vm-plan-phase2-hazard.md`)
**STATUS**: STAGED & READY — **100% of VMs with live `tmux` seats are strictly protected**

## 1. Phase 1 Complete (`O3336` — 12 Cold VMs Marked `#COLD-O3336` in `~/.config/bd/hosts`, `544 GiB` Reclaimed)
Confirmed `#COLD-O3336` in `~/.config/bd/hosts`:
- `bd` (`.50`), `spare5` (`.185`), `spare6` (`.186`), `spare9` (`.187`), `spare11` (`.195`), `spare1` (`.197`), `seatc1` (`.35`), `seatc2` (`.171`), `cir2` (`.86`), `spare3` (`.122`), `spare` (`.224`), `kvcache-cx1` (`.198`).
- Also confirmed `lens-r3c-cx4-20261014` (powered off in Phase 1) was the duplicate-IP clone, while real `test1` (vCenter VM `test`, `10.0.70.194`, `wrk-test01`) is **ONLINE (`0.32ms`)** running `bd-dispatch-build-D2-D`.

---

## 2. Ground-Truth 3-Way Join: Protected Active VMs (`20` VMs — NEVER Power Off)

### A. 10 VMs Holding Live `tmux` Seats (`64` Live `tmux` Sessions Total — STRICTLY PROTECTED)
| vCenter VM Name | `~/.config/bd/hosts` Alias | IP Address | Guest Hostname | Provisioned RAM | Live `tmux` Sessions | Key Active Seats |
| :--- | :--- | :--- | :--- | :---: | :---: | :--- |
| `Test5` | `test5` | `10.0.70.164` | `hub-mesh01` | `350.0 GiB` | **`31`** | `bd-pm-B`, `AGY-council`, swarms |
| `test7` | `test7` | `10.0.70.84` | `wrk-test07` | `48.0 GiB` | **`7`** | `bd-integrator-C` + 6 workers |
| `test3` | `test3` | `10.0.70.80` | `wrk-test03` | `64.0 GiB` | **`6`** | 6 active worker/lens seats |
| `test4` | `test4` | `10.0.70.85` | `wrk-test05` | `64.0 GiB` | **`6`** | `bd-lensrouter-B1-B` + 5 seats |
| `bd2` | `bd2` | `10.0.70.52` | `wrk-bd03` | `64.0 GiB` | **`5`** | 5 active worker/lens seats |
| `test6` | `test6` | `10.0.70.249` | `wrk-test06` | `48.0 GiB` | **`4`** | 4 active worker/lens seats |
| `spare2` | `spare2` | `10.0.70.193` | `node1` | `32.0 GiB` | **`2`** | 2 active seats |
| `bd1` | `bd1` | `10.0.70.51` | `wrk-bd02` | `32.0 GiB` | **`1`** | 1 active seat |
| `spare8` | `spare8` | `10.0.70.184` | `spare8` | `96.0 GiB` | **`1`** | `bd-audit-census-C` (NFS-verified) |
| `test` | `test1` | `10.0.70.194` | `wrk-test01` | `48.0 GiB` | **`1`** | `bd-dispatch-build-D2-D` |

### B. 10 Core GPU, Ramdisk CI, Browser Oracle & vSphere VMs (STRICTLY PROTECTED)
- **Sovereign GPU & Browser Oracle (5)**: `AI` (`.72`, `320G`), `AI DEV` (`.83`, `386G`), `AI Inference` (`.125`, `576G`), `llama` (`.228`, `448G`), `Bittorrent` (`.181`, `32G`).
- **64-Core `/dev/shm` Ramdisk Pytest (1)**: `test3big` (`.82`, `wrk-test04`, `128G`).
- **vSphere / vSAN Appliances (3 + 2 `vCLS`)**: `VC04` (`.30.13`, `30G`), `VC04-Peer` (`.20.70`, `30G`), `VC04-Witness` (`.30.5`, `1G`).

---

## 3. Re-Derived Phase 2 Candidates: Powered-On VMs with `0` Live `tmux` Sessions (`13` VMs, `624 GiB` RAM)
Every VM below currently has **`TMUX=0`** (verified via SSH `tmux list-sessions | wc -l`). Notice that during `ORDER-unpark-all.md`, `bd-pm-B` / `B5-B` / `D2-D` may launch new seats onto the `~/.config/bd/hosts` band hosts, so `/var/tmp/stage_16vm_consolidation.py` dynamically re-verifies `TMUX == 0` via SSH before touching any VM:

1. **4 Non-`bd/hosts` Aux VMs (`192 GiB` RAM — not in `~/.config/bd/hosts`, `TMUX=0`)**:
   - `test5` (lowercase `t`, `10.0.70.107`, `64.0 GiB` — orphan duplicate not in `~/.config/bd/hosts`)
   - `CI/CD` (`10.0.70.162`, `64.0 GiB`, `TMUX=0`)
   - `Cache` (`10.0.70.182`, `32.0 GiB`, `TMUX=0`)
   - `MCP` (`10.0.70.25`, `32.0 GiB`, `TMUX=0`)
2. **9 Currently-Empty `~/.config/bd/hosts` Band/Spare VMs (`432 GiB` RAM — `TMUX=0` right now; keep online while `ORDER-unpark-all` runs)**:
   - `bd3` (`10.0.70.53`, `64.0 GiB`, `TMUX=0`)
   - `bd4` (`10.0.70.54`, `64.0 GiB`, `TMUX=0`)
   - `Test2` (`bd_alias=test2`, `10.0.70.95`, `64.0 GiB`, `TMUX=0`)
   - `Test` (`bd_alias=fresh149`, `10.0.70.149`, `64.0 GiB`, `TMUX=0`)
   - `bd-ci-r1-20261003` (`bd_alias=cir1`, `10.0.70.121`, `48.0 GiB`, `TMUX=0`)
   - `spare4` (`10.0.70.81`, `32.0 GiB`, `TMUX=0`)
   - `spare7` (`10.0.70.120`, `32.0 GiB`, `TMUX=0`)
   - `spare12` (`10.0.70.183`, `32.0 GiB`, `TMUX=0`)
   - `spare10` (`10.0.70.196`, `32.0 GiB`, `TMUX=0`)
