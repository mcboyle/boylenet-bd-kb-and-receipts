# AGENT-56: Ramdisk `/dev/shm` Ephemeral CI Shard Offload Swarm Receipts (O1789 Swarm #8)

- **WRITTEN-AT**: `2026-10-18T15:50:40Z`
- **AGENT**: `Agent-56` (Swarm #8: Ramdisk `/dev/shm` Ephemeral CI Shard Offload Swarm across High-Core Worker Nodes)
- **TOOL PATH**: [`/home/mboyle/bin/bd-remote-pytest-turbo`](file:///home/mboyle/bin/bd-remote-pytest-turbo) (`-rwxrwxr-x`)
- **TOOL SHA256**: `744dc735e4396a31115d0126901d76262954b363769f06cf80463a54320124d2`
- **VERDICT**: `PASS` (`4/4` active high-core worker nodes warmed at `/dev/shm/bd-ci-ramdisk`, `160` remote vCPUs, `159 GiB` total `/dev/shm`; `.82` incremental sync `194-201ms` `<400ms`; `.82` unit slice wall-clock `4.54s` `<5.0s`; `.164` controller CPU reduced by `94.9%`)

---

## 1. High-Core Worker Node Audit (`bd-remote-pytest-turbo --audit`)

| Node IP | Hostname / Alias | vCPUs (`nproc`) | `/dev/shm` Size (Avail) | 1m Load | Python Venv Path | Status / Notes |
| :--- | :--- | :---: | :---: | :---: | :--- | :--- |
| `10.0.70.82` | `wrk-test04` (`test3big`) | **64** | **63G (`63G` avail)** | `0.24` | `/home/mboyle/BulkDownloader/venv` (`pytest 9.1.1` + `xdist`) | **ONLINE (Primary 64-Core Target)** |
| `10.0.70.149` | `spr-pool05` (`fresh149`) | `16` | `-` | `-` | `-` | `OFFLINE` (O1789 `poweredOff` cold spare while `1,730 GiB` GPU RAM locked) |
| `10.0.70.120` | `spr-pool01` (`spare7`) | `32` | `-` | `-` | `-` | `OFFLINE` (O1789 `poweredOff` cold spare while `1,730 GiB` GPU RAM locked) |
| `10.0.70.183` | `spr-pool02` (`spare12`) | `16` | `-` | `-` | `-` | `OFFLINE` (O1789 `poweredOff` cold spare while `1,730 GiB` GPU RAM locked) |
| `10.0.70.85` | `wrk-test05` (`test4`) | **32** | **32G (`32G` avail)** | `0.13` | `/home/mboyle/BulkDownloader/venv` (`pytest 9.1.1` + `xdist`) | **ONLINE (Active 32-Core Failover #1)** |
| `10.0.70.95` | `wrk-test02` (`test2`) | **32** | **32G (`32G` avail)** | `0.00` | `/home/mboyle/BulkDownloader/venv` (`pytest 9.1.1` + `xdist`) | **ONLINE (Active 32-Core Failover #2)** |
| `10.0.70.80` | `wrk-test03` (`test3`) | **32** | **32G (`32G` avail)** | `0.01` | `/home/mboyle/BulkDownloader/venv` (`pytest 9.1.1` + `xdist`) | **ONLINE (Active 32-Core Failover #3)** |

---

## 2. Ramdisk Pre-Warming & Incremental Sync (`bd-remote-pytest-turbo --warm`)

`/home/mboyle/bin/bd-remote-pytest-turbo` uses SSH `ControlMaster=auto` (`ControlPersist=600`) and `rsync -a --delete` excluding bulky non-test artifacts (`.git/`, `.worktrees/` 13G, `captures/` 2.5G, `.claude/` 2.0G, `profiles/`, `node_modules/`, `*.db*`), then pre-compiles `.pyc` bytecode in `/dev/shm/bd-ci-ramdisk` via `python -m compileall -q -j 16`.

```text
Warming /dev/shm/bd-ci-ramdisk from /home/mboyle/BulkDownloader across worker pool...
NODE_IP       HOSTNAME     CORES  COLD_MS      INCR_MS      RAMDISK_USE  STATUS    
------------------------------------------------------------------------------------------
10.0.70.82    wrk-test04   64     257ms        201ms        266M         WARMED    
10.0.70.149   -            -      -            -            -            SKIP(OFF) 
10.0.70.120   -            -      -            -            -            SKIP(OFF) 
10.0.70.183   -            -      -            -            -            SKIP(OFF) 
10.0.70.85    wrk-test05   32     195ms        197ms        246M         WARMED    
10.0.70.95    wrk-test02   32     832ms        208ms        246M         WARMED    
10.0.70.80    wrk-test03   32     180ms        186ms        246M         WARMED    
------------------------------------------------------------------------------------------
WARM_SUMMARY: 4 active high-core node(s) warmed at /dev/shm/bd-ci-ramdisk
```

- **Incremental Sync Latency**: `186ms - 208ms` across all 4 online nodes (`201ms` / `194ms` on primary `10.0.70.82`, well below the `<400ms` (`<0.4s`) target).

---

## 3. Benchmarks: `.82` `/dev/shm` Ramdisk Execution & `.164` Controller CPU Savings

### A. Unit Test Slice on Primary 64-Core `10.0.70.82` (`wrk-test04` / `test3big`)
Command:
```bash
/usr/bin/time -f "WALL_CLOCK=%es LOCAL_USER_CPU=%Us LOCAL_SYS_CPU=%Ss LOCAL_CPU_PCT=%P" \
  /home/mboyle/bin/bd-remote-pytest-turbo \
  tests/test_2c_guard_zero_match_interlock.py tests/test_3a_login_warning_reword.py
```
Output:
```text
PASS: 7 passed in 3.16s
BLOB: /var/tmp/bd-blobs/9f6fa487ccc0d5cce55044f27c4febe635271e314e0e06f00e40f2b433ab444f.log (node=10.0.70.82 sync=194ms run=4163ms bytes=1197)
WALL_CLOCK=4.54s LOCAL_USER_CPU=0.10s LOCAL_SYS_CPU=0.09s LOCAL_CPU_PCT=4%
```

### B. Failover Verification on 32-Core `10.0.70.85` (`wrk-test05` / `test4`)
```text
PASS: 7 passed in 3.64s
BLOB: /var/tmp/bd-blobs/f8e6d2501c8a64ecc37dce4ffedb0eb2d9b8f9ee4bf39c117d39a27068b5aa14.log (node=10.0.70.85 sync=258ms run=6396ms bytes=1198)
WALL_CLOCK=6.77s LOCAL_USER_CPU=0.10s LOCAL_SYS_CPU=0.09s LOCAL_CPU_PCT=2%
```

### C. Parallel 8-File Unit Shard (`72 tests`) on `10.0.70.82` (`-n 8` vs Serial)
- **Serial (1 worker on `.82`)**: `72 passed in 15.09s` (`real 15.883s`)
- **Parallel (`-n 8` on 64-core `.82`)**: `72 passed in 8.48s` (`real 9.161s`, `55.86s` remote user CPU + `6.12s` remote sys CPU offloaded from `.164`)

### D. Negative Control (Rule 7 & Rule 78/80 Exit Code Propagation)
```text
$ /home/mboyle/bin/bd-remote-pytest-turbo tests/test_3a_login_warning_reword.py -k "nonexistent_test_selector_rule7"
FAIL: no tests collected | exit 5
BLOB: /var/tmp/bd-blobs/2b2077765fba41e3881ef0ecbc587847426d3d21785b741608e8d1b3615395b9.log (node=10.0.70.82 sync=232ms run=1655ms bytes=1000)
NEG_CONTROL_RC=5
```

### E. `.164` Controller CPU & Wall-Clock Savings Summary

| Metric | Local `.164` Runner | Offloaded `bd-remote-pytest-turbo` (`.82` `/dev/shm`) | Delta / Improvement |
| :--- | :---: | :---: | :---: |
| **Incremental `rsync --delete` Time** | N/A | **`0.194s` (`194ms`)** | **Meets `<0.4s` target** |
| **End-to-End Wall-Clock (2-file slice)** | `21.16s` | **`4.54s` (`3.16s` pytest)** | **Meets `<5.0s` target (`4.66x` faster)** |
| **Controller `.164` CPU Time (`user+sys`)** | `3.37s` (`3.72s` direct pytest) | **`0.19s` (`0.10s` user + `0.09s` sys)** | **`94.9%` `.164` CPU savings (`99.7%` on 8-file shard)** |
| **Rule 78 (`bd-pytest-digest`) Output** | `<=3 lines` | **`2 lines` (`PASS` + `BLOB`), `rc` preserved** | **Compliant** |
| **Rule 80 (`/var/tmp/bd-blobs/<sha256>.log`)** | Local CAS | **Local CAS `/var/tmp/bd-blobs/<sha256>.log`** | **Compliant** |
