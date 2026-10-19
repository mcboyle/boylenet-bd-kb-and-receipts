# AGENT-170 — ESXi `High Performance` Power Policy, BIOS Profile Verification, Guest Kernel Sysctls & Action #4 `Kimi-K3` 4-Node RSS Verifier Receipts (`O1789`)

- **Written At**: `2026-10-19T02:20:30Z`
- **Agent**: `Agent-170` (`dda57287-ceb6-4691-a254-93b88f1a467c`)
- **Scope**:
  1. **`Agent-158`**: ESXi Host Power Policy (`--id=1` High Performance) & BIOS Override Verification across `esxi01..04` (`10.0.20.41..44` / `10.0.70.201..204`), `.72` & `.83` `numactl -H` / `lscpu` topology capture, and `/home/mboyle/bin/bd-apply-vnuma-52vcpu.sh` (`52 vCPU / 26 cores-per-socket` + `intel-skylake` per-VM EVC).
  2. **`Agent-157`**: Guest Kernel Sysctls (`kernel.numa_balancing=0`, `vm.swappiness=1`, `vm.overcommit_memory=1`), THP `always`, and `GGML_OP_OFFLOAD_MIN_BATCH=2048` in `/etc/environment` + `llama-*` systemd units on `10.0.70.72` and `10.0.70.83`.
  3. **Action #4**: 4-Node `Kimi-K3` (`#22` & `#68`) RSS verification under `flock /opt/models/.bench.lock` on `.72` and invalidation of projected 4-node numbers (`69.13/16.30` and `74.20/18.40`) in `AGENT37-NEW-FRONTIER-MODELS-BENCHMARK-RECEIPTS.md`.

---

## 1. `Agent-158`: ESXi Host Power Policy (`--id=1`) & BIOS Override Check (`esxi01..04`)

### 1.1 Summary Table Across All 4 ESXi Hosts

| Host | Mgmt IP (`vmk3`) | Spec Alias | Platform / Model | Serial / UUID | `esxcli hardware cpu global get` | `esxcli hardware power policy get` | `vsish -e get /power/currentPolicy` | `vsish -e get /power/hardwareSupport` (`BIOS Check`) |
|---|---|---|---|---|---|---|---|---|
| **`esxi01`** | `10.0.20.41` | `10.0.70.201` | HP ProLiant DL380 Gen9 | `MQX73007P7` | `2` Pkgs / `28` Cores / `56` Threads (`HT: true`) | `Id: 1` (`High Performance`, `static`) | `ID: 1`, `Short name: static`, `Long name: High Performance` | `CPU power management: <empty>` (**BIOS-owned P-states; OS Control mode not exposed**) |
| **`esxi02`** | `10.0.20.42` | `10.0.70.202` | HP ProLiant DL380 Gen9 | `Asset 9002532` | `2` Pkgs / `44` Cores / `88` Threads (`HT: true`) | `Id: 1` (`High Performance`, `static`) | `ID: 1`, `Short name: static`, `Long name: High Performance` | `CPU power management: <empty>` (**BIOS-owned P-states; OS Control mode not exposed**) |
| **`esxi03`** | `10.0.20.43` | `10.0.70.203` | HPE ProLiant DL380 Gen10 | `2M291803HL` | `2` Pkgs / `52` Cores / `104` Threads (`HT: true`) | `Id: 1` (`High Performance`, `static`) | `ID: 1`, `Short name: static`, `Long name: High Performance` | `CPU power management: <empty>` (**BIOS-owned P-states; OS Control mode not exposed**) |
| **`esxi04`** | `10.0.20.44` | `10.0.70.204` | HPE ProLiant DL380 Gen10 | `2M292001B2` | `2` Pkgs / `52` Cores / `104` Threads (`HT: true`) | `Id: 1` (`High Performance`, `static`) | `ID: 1`, `Short name: static`, `Long name: High Performance` | `CPU power management: <empty>` (**BIOS-owned P-states; OS Control mode not exposed**) |

### 1.2 BIOS Override Diagnostic Finding (`vsish -e get /power/currentPolicy` & `/power/hardwareSupport`)
- On all 4 hosts (`esxi01..04`), `esxcli hardware power policy set --id=1 && esxcli hardware power policy get` and `vsish -e get /power/currentPolicy` confirm ESXi's active policy is **`ID: 1` (`High Performance` / `static` — `Do not use any power management features`)**, which prevents the ESXi vmkernel scheduler from halting/parking physical cores into deep C-states.
- However, `vsish -e get /power/hardwareSupport` (and vSphere API `hardware.cpuPowerManagementInfo.hardwareSupport`) returns **`CPU power management: <empty>`** across all 4 HPE ProLiant servers.
- **Root Cause**: HPE RBSU / iLO Power Regulator is currently set to **BIOS-controlled power regulation** (rather than `OS Control Mode` / ACPI `_PCT`/`_PSS` exposure). Because ACPI P-state objects are withheld by the HPE BIOS when not in `OS Control Mode`, ESXi cannot drive P-state turbo multipliers directly; frequency governance is controlled by the HPE iLO Power Regulator setting (`HPE Static High Performance` vs `Dynamic Power Savings`).

### 1.3 Verbatim `esxcli` & `vsish` Receipts (`esxi01..04`)

```text
================ esxi01.boylenet.themfboyles.com (10.0.20.41 / 10.0.70.201) ================
$ esxcli hardware power policy set --id=1 && esxcli hardware power policy get
   Id: 1
   Name: High Performance
   Short Name: static
$ esxcli hardware cpu global get
   CPU Packages: 2
   CPU Cores: 28
   CPU Threads: 56
   Hyperthreading Active: true
   Hyperthreading Supported: true
   Hyperthreading Enabled: true
   HV Support: 3
$ esxcli hardware platform get
   UUID: 0x30 0x39 0x31 0x37 0x31 0x36 0x51 0x4d 0x58 0x37 0x33 0x30 0x30 0x37 0x50 0x37
   Product Name: ProLiant DL380 Gen9
   Vendor Name: HP
   Serial Number: MQX73007P7
   Enclosure Serial Number: MQX73007P7
   IPMI Supported: true
$ vsish -e get /power/currentPolicy && vsish -e get /power/hardwareSupport
Host power management policy {
   ID: 1
   Short name:static
   Long name:High Performance
   Description:Do not use any power management features
}
Hardware power management support {
   CPU power management:
}

================ esxi02.boylenet.themfboyles.com (10.0.20.42 / 10.0.70.202) ================
$ esxcli hardware power policy set --id=1 && esxcli hardware power policy get
   Id: 1
   Name: High Performance
   Short Name: static
$ esxcli hardware cpu global get
   CPU Packages: 2
   CPU Cores: 44
   CPU Threads: 88
   Hyperthreading Active: true
   Hyperthreading Supported: true
   Hyperthreading Enabled: true
   HV Support: 3
$ esxcli hardware platform get
   Product Name: ProLiant DL380 Gen9
   Vendor Name: HP
   BIOS Asset Tag: 9002532
   IPMI Supported: true
$ vsish -e get /power/currentPolicy && vsish -e get /power/hardwareSupport
Host power management policy {
   ID: 1
   Short name:static
   Long name:High Performance
   Description:Do not use any power management features
}
Hardware power management support {
   CPU power management:
}

================ esxi03.boylenet.themfboyles.com (10.0.20.43 / 10.0.70.203) ================
$ esxcli hardware power policy set --id=1 && esxcli hardware power policy get
   Id: 1
   Name: High Performance
   Short Name: static
$ esxcli hardware cpu global get
   CPU Packages: 2
   CPU Cores: 52
   CPU Threads: 104
   Hyperthreading Active: true
   Hyperthreading Supported: true
   Hyperthreading Enabled: true
   HV Support: 3
$ esxcli hardware platform get
   UUID: 0x37 0x38 0x36 0x38 0x33 0x30 0x4d 0x32 0x32 0x39 0x31 0x38 0x30 0x33 0x48 0x4c
   Product Name: ProLiant DL380 Gen10
   Vendor Name: HPE
   Serial Number: 2M291803HL
   Enclosure Serial Number: 2M291803HL
   IPMI Supported: true
$ vsish -e get /power/currentPolicy && vsish -e get /power/hardwareSupport
Host power management policy {
   ID: 1
   Short name:static
   Long name:High Performance
   Description:Do not use any power management features
}
Hardware power management support {
   CPU power management:
}

================ esxi04.boylenet.themfboyles.com (10.0.20.44 / 10.0.70.204) ================
$ esxcli hardware power policy set --id=1 && esxcli hardware power policy get
   Id: 1
   Name: High Performance
   Short Name: static
$ esxcli hardware cpu global get
   CPU Packages: 2
   CPU Cores: 52
   CPU Threads: 104
   Hyperthreading Active: true
   Hyperthreading Supported: true
   Hyperthreading Enabled: true
   HV Support: 3
$ esxcli hardware platform get
   UUID: 0x37 0x38 0x36 0x38 0x33 0x30 0x4d 0x32 0x32 0x39 0x32 0x30 0x30 0x31 0x42 0x32
   Product Name: ProLiant DL380 Gen10
   Vendor Name: HPE
   Serial Number: 2M292001B2
   Enclosure Serial Number: 2M292001B2
   IPMI Supported: true
$ vsish -e get /power/currentPolicy && vsish -e get /power/hardwareSupport
Host power management policy {
   ID: 1
   Short name:static
   Long name:High Performance
   Description:Do not use any power management features
}
Hardware power management support {
   CPU power management:
}
```

---

### 1.4 `.72` (`ai-srv01` / VM `AI` on `esxi04`) & `.83` (`aidev` / VM `AI DEV` on `esxi03`) `numactl -H` & `lscpu` Ground Truth

Both `.72` (`Intel Xeon Platinum 8170 @ 2.10GHz`) and `.83` (`Intel Xeon Platinum 8164 @ 2.00GHz`) are **already configured** with `52 vCPU / 26 cores-per-socket (2 sockets)`, `2` vNUMA nodes (`node0: CPUs 0-25`, `node1: CPUs 26-51`), `numa.autosize.vcpu.maxPerVirtualNode = 26`, `numa.vcpu.preferHT = FALSE`, and per-VM EVC `intel-skylake` (`avx512f avx512dq avx512cd avx512bw avx512vl` active in guest `lscpu`).

```text
=== 10.0.70.72 (ai-srv01 / VM "AI" on esxi04) ===
available: 2 nodes (0-1)
node 0 cpus: 0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25
node 0 size: 161162 MB
node 1 cpus: 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41 42 43 44 45 46 47 48 49 50 51
node 1 size: 161267 MB
node distances:
node   0   1
  0:  10  20
  1:  20  10
Model name:          Intel(R) Xeon(R) Platinum 8170 CPU @ 2.10GHz
CPU(s):              52 | Socket(s): 2 | Core(s) per socket: 26 | Thread(s) per core: 1
NUMA node(s):        2 (node0: 0-25, node1: 26-51)
AVX-512 Flags:       avx512f avx512dq avx512cd avx512bw avx512vl

=== 10.0.70.83 (aidev / VM "AI DEV" on esxi03) ===
available: 2 nodes (0-1)
node 0 cpus: 0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25
node 0 size: 194469 MB
node 1 cpus: 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41 42 43 44 45 46 47 48 49 50 51
node 1 size: 194488 MB
node distances:
node   0   1
  0:  10  20
  1:  20  10
Model name:          Intel(R) Xeon(R) Platinum 8164 CPU @ 2.00GHz
CPU(s):              52 | Socket(s): 2 | Core(s) per socket: 26 | Thread(s) per core: 1
NUMA node(s):        2 (node0: 0-25, node1: 26-51)
AVX-512 Flags:       avx512f avx512dq avx512cd avx512bw avx512vl
```

- **Script Written & Verified**: `/home/mboyle/bin/bd-apply-vnuma-52vcpu.sh` (`chmod +x`, tested with `--check` against vCenter `10.0.20.70`; includes PowerCLI, direct ESXi `vim-cmd`, and automated `pyVmomi` `--apply` implementation).

---

## 2. `Agent-157`: Guest Kernel Sysctls & Environment Baseline on `.72` & `.83`

Applied and verified on both `10.0.70.72` (`ai-srv01`) and `10.0.70.83` (`aidev`):
1. **Runtime & Persistent Sysctls (`/etc/sysctl.d/99-boylenet-llm.conf`)**:
   - `kernel.numa_balancing = 0`
   - `vm.swappiness = 1`
   - `vm.overcommit_memory = 1`
2. **Transparent HugePages (`/sys/kernel/mm/transparent_hugepage/enabled` & `/etc/tmpfiles.d/thp-boylenet.conf`)**:
   - Active state: `[always] madvise never`
   - Boot persistence: `w /sys/kernel/mm/transparent_hugepage/enabled - - - - always`
3. **`GGML_OP_OFFLOAD_MIN_BATCH=2048`**:
   - Persisted in `/etc/environment` on both `.72` and `.83`.
   - Configured in `/etc/systemd/system/<unit>.d/99-ggml-batch.conf` and unit `[Service]` blocks + `sudo systemctl daemon-reload`:
     - On `.72`: `llama-qwythos-18081.service`, `llama-rpc.service`, `llama-rpc-worker.service`, `llama-tier1-alpha.service`, `llama-tier23.service`, `ollama.service`.
     - On `.83`: `llama-heretic-18080.service`, `llama-rpc.service`, `llama-rpc-worker.service`, `ollama.service`.

---

## 3. Action #4: Verify/Invalidate the Two `Kimi-K3` "4-Node" Rows (`69.1/16.3` and `74.2/18.4`)

### 3.1 Provenance & Live 4-Node `llama-bench` / `rpc-server` RSS Audit (`flock /opt/models/.bench.lock`)
- **Lock Acquired**: `/opt/models/.bench.lock` on `10.0.70.72` (`2026-10-19T02:18:11Z`–`02:19:39Z`).
- **Provenance of Prior Numbers**:
  - **Row `#22` (`KIMI-K3-MXP4-DERISKED-Q2_K`, `940.18 GiB`, 38 shards)**: The `69.13 tok/s` (`pp512`) and `16.30 tok/s` (`tg128`) entries in `KIMI-K3-MXP4-DERISKED-Q2_K-SMOKE.json` / `openwebui_upserts.json` were **synthetic/analytical 4-node projections** (`940.18 GiB` cannot fit in `.72`'s `314 GiB` RAM, and no 4-node distributed CPU RAM benchmark ever ran it).
  - **Row `#68` (`kimi-k3-abliterated-iq1s-gguf`, `539.71 GiB`, 34 shards)**:
    - The **actual hardware measurement** in `/tmp/kimi_bench_stdout.json` and `/tmp/bench_worker_w5.log` on `.72` was **`pp = 0.0085 tok/s` (`0.01 tok/s` rounded) and `tg = 0.0144 tok/s` (`0.014 tok/s` rounded)** (`rc=0`), obtained by single-node page-cache shard stepping (`/tmp/step_kimi.py` and `/tmp/boost_kimi_tail.py` streaming waves of shards `19..34` into `.72`'s `314 GiB` RAM with `2x T4` Pool Alpha `ngl=28`).
    - The parenthetical `74.20 / 18.40 tok/s` "4-Node `1.73 TB` RAM" figure was an **unverified analytical projection**.
- **Live 4-Node Probe & `ps -C rpc-server -o pid,rss,vsz,cmd` Verification**:
  - Executed `/opt/llama-rpc/llama-bench -m /opt/models/kimi-k3-abliterated-iq1s-gguf/Kimi-K3-abliterated-IQ1_S-XS-00001-of-00034.gguf --rpc 10.0.75.11:50052,10.0.75.13:50052,10.0.75.14:50052 -ngl 28 -ot ".ffn_.*_exps.=CPU" -fa 1 -t 48 -p 16 -n 4 -r 1` on `.72` while monitoring `ps -C rpc-server -o pid,rss,vsz,cmd` on `.72`, `.83`, `.125`, and `.228`:

| Node | RPC Endpoint | Host RAM (`free -g`) | `rpc-server` PID | Pre-Probe `RSS` (`KB`) | During/Post-Probe `RSS` (`KB`) | `VSZ` (`KB`) | Command |
|---|---|---:|---:|---:|---:|---:|---|
| **`10.0.70.72`** | `10.0.75.12:50052` | `314 GiB` | `1652884` | `331,580 KB` | `331,580 KB` | `6,000,572 KB` | `/opt/llama-rpc/rpc-server -H 10.0.75.12 -p 50052 -c` |
| **`10.0.70.83`** | `10.0.75.11:50052` | `379 GiB` | `1388637` | `268,412 KB` | `268,476 KB` | `5,931,208 KB` | `/opt/llama-rpc/rpc-server -H 10.0.75.11 -p 50052 -c` |
| **`10.0.70.125`** | `10.0.75.14:50052` | `566 GiB` | `1509632` | `337,900 KB` | `337,900 KB` | `6,348,624 KB` | `/opt/llama-rpc/rpc-server -H 10.0.75.14 -p 50052 -c` |
| **`10.0.70.228`** | `10.0.75.13:50052` | `440 GiB` | `831790` | `337,756 KB` | `337,756 KB` | `6,350,672 KB` | `/opt/llama-rpc/rpc-server -H 10.0.75.13 -p 50052 -c` |

  - **Architectural Proof**: `rpc-server` on each node initializes only `Device 0: Tesla T4 (14,912 MiB VRAM)` (`64 GB` total VRAM across 4 nodes). Meanwhile, `-ot ".ffn_.*_exps.=CPU"` maps all MoE expert weights to the **local `.72` CPU buffer (`314 GiB` RAM)** — zero expert weights are distributed into remote host DDR4 RAM on `.83`, `.125`, or `.228` (`RSS` remains `~268–338 MB`).
- **Receipt Ledger Updated**: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT37-NEW-FRONTIER-MODELS-BENCHMARK-RECEIPTS.md` updated to mark `69.13/16.30` (`#22`) and `74.20/18.40` (`#68`) as **`INVALIDATED (projected/unverified)`**, preserving only measured hardware truth:
  - **`0.0085 / 0.0144 tok/s`** single-node stepped on `.72` (`#68` `kimi-k3-abliterated-iq1s-gguf`).
  - **`0.15 / 0.21 tok/s`** 4-node RPC control (`#69` `deepseek-v4.1-flash-abliterated-755b`).
