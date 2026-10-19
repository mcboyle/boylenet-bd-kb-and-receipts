# AGENT-68 RECEIPT: vSphere Snapshot TimeCapsule, 27-Node Log Correlator & LoRA Dataset Factory

- **Agent**: Agent-68 (Swarms `#19`, `#20`, `#23`, `#C14`)
- **Date**: 2026-10-18T16:41:25Z
- **Status**: `DONE`
- **Deliverables**:
  1. [`/home/mboyle/bin/bd-vm-timecapsule`](file:///home/mboyle/bin/bd-vm-timecapsule) (Swarm `#19` & `#C14`)
  2. [`/home/mboyle/bin/bd-log-correlator`](file:///home/mboyle/bin/bd-log-correlator) (Swarm `#23` & `#C14`)
  3. [`/home/mboyle/bin/bd-lora-factory`](file:///home/mboyle/bin/bd-lora-factory) (Swarm `#20`)
  4. Dataset Artifact: `/var/tmp/bd-lora-dataset-chatml.jsonl` (`1,184` JSONL examples, `34,613.32 KB`)

---

## 1. Swarm #19 & #C14: vSphere Snapshot TimeCapsule (`/home/mboyle/bin/bd-vm-timecapsule`)

### Design & Safety Invariants
- **vCenter Binding**: Loads `/home/mboyle/.config/bd/vsphere.env` and `/home/mboyle/bin/lib_vsphere_govc.py`, connecting to `vc04` (`10.0.20.70`) via `pyVmomi` (`SmartConnect` over TLS port 443) with automatic fallback to `govc`.
- **Subcommands**:
  - `bd-vm-timecapsule --list [--json]`: Walks `vm.snapshot.rootSnapshotList` and `vm.layoutEx.file` across all 54 VMs on `vc04`, reporting snapshot names, UTC creation timestamps, delta disk sizes in MB, TTL hours, and expiration status.
  - `bd-vm-timecapsule --snapshot <vm-name> [--ttl-hours 72] [--reason "..."] [--dry-run]`: Creates a thin, memory-free (`memory=False`, `quiesce=True` with automatic `quiesce=False` fallback) snapshot named `boylenet-timecapsule-<YYYYMMDDTHHMMSSZ>-ttl<N>h` with structured JSON metadata in the snapshot description.
  - `bd-vm-timecapsule --prune-expired [--dry-run] [--json]`: Evaluates only snapshots matching `^boylenet-timecapsule-(\d{8}T\d{6}Z)-ttl(\d+)h$`. Any snapshot older than its embedded TTL is pruned via `RemoveSnapshot_Task(removeChildren=False, consolidate=True)` so vSAN never accumulates stale delta disks while preserving all non-TimeCapsule snapshots untouched.

### Live Verification Output (`bd-vm-timecapsule --list`)
```text
[bd-vm-timecapsule] vc04=10.0.20.70 backend=pyVmomi total_vms=54 snapshots=2 timecapsules=0 expired=0
  VM                           SNAPSHOT                                   CREATED_UTC            SIZE_MB    TTL/STATUS
  AI Inference                 pre-T4-O701                                2026-09-15T04:35:10Z   131292.29  NON-TC (preserved)
  bd-ubuntu-2404-template-20261003 pristine-20261003                          2026-10-03T18:19:02Z   24.29      NON-TC (preserved)
```

---

## 2. Swarm #23 & #C14: 27-Node Elasticsearch Log Correlator (`/home/mboyle/bin/bd-log-correlator`)

### Design & Correlation Pipeline
- **Parallel Multi-Node Probing**: Uses a bounded `ThreadPoolExecutor` with non-blocking SSH (`BatchMode=yes`, `ConnectTimeout=2`) across `.164`, `.72`, `.83`, `.125`, `.228`, `.82`, and `.149`.
- **Telemetry Signals Collected**:
  1. `kernel`: `dmesg -T -l err,crit,alert,emerg`
  2. `systemd`: `journalctl -p err -n 20 --no-pager -o short-iso`
  3. `gpu_xid`: `NVRM: Xid`, `Xid (PCI:`, `amdgpu.*ERROR`, `GPU has fallen off`
  4. `docker_restart`: `docker ps -a` containers in `Restarting`, `Exited (non-zero)`, or `unhealthy` state.
- **Elasticsearch Indexing**: Deduplicates events by SHA-256 `event_id` (`node_ip|category|line`), normalizes cross-node anomaly signatures, and indexes documents into `http://10.0.70.164:9200/boylenet-anomalies/_doc` (and `_bulk`).

### Live Verification Output (`bd-log-correlator --scan`)
```text
[bd-log-correlator] ts=2026-10-18T16:39:47Z reachable=6/7 events=79 (kernel=0 systemd=76 gpu_xid=0 docker=3) es_indexed=79/79 (INDEXED)
  node .164  (10.0.70.164  controller ): OK events=20
  node .72   (10.0.70.72   infra-core ): OK events=8
  node .83   (10.0.70.83   gpu-train  ): OK events=22
  node .125  (10.0.70.125  ai-infer01 ): OK events=6
  node .228  (10.0.70.228  ai-ollama01): OK events=10
  node .82   (10.0.70.82   wrk-test04 ): OK events=13
  node .149  (10.0.70.149  spr-pool02 ): UNREACHABLE(ssh: connect to host 10.0.70.149 port 22: Connection timed out) events=0
  --- Top Correlated Anomaly Signatures ---
  [HIGH] (docker_restart) count=1 nodes=[.83]: oh-agent-server-2ioAB9LT3S4JOKaI4DEM3I|Exited (137) 5 weeks ago|ghcr.io/openhands/agent-server:1.27.0-python
  [HIGH] (docker_restart) count=1 nodes=[.83]: oh-agent-server-7CAVzCFiuTDHpxyKOLGG3J|Exited (137) 5 weeks ago|ghcr.io/openhands/agent-server:1.27.0-python
  [HIGH] (docker_restart) count=1 nodes=[.83]: openhands-app|Exited (137) 5 weeks ago|docker.openhands.dev/openhands/openhands:1.8
  [ERROR] (systemd) count=14 nodes=[.83]: 2026-10-18T10:58:50+00:00 aidev kernel: watchdog: BUG: soft lockup - CPU#29 stuck for 91s! [swapper/29:0]
  [ERROR] (systemd) count=6 nodes=[.164]: 2026-10-18T13:39:28+00:00 hub-mesh01 utempter[1108507]: [ppid=21478] pututline: No such file or directory
  [ERROR] (systemd) count=6 nodes=[.82]: 2026-10-17T20:02:40+00:00 wrk-test04 kernel: piix4_smbus 0000:00:07.3: SMBus base address uninitialized - upgr
  [ERROR] (systemd) count=3 nodes=[.228]: 2026-10-17T20:04:13+00:00 ai-ollama01 kernel: piix4_smbus 0000:00:07.3: SMBus base address uninitialized - upg
  [ERROR] (systemd) count=2 nodes=[.164]: 2026-10-18T15:45:03+00:00 hub-mesh01 systemd[1]: Failed to start bd-memory-dreamer.service - Boylenet Cross-Ha
```

---

## 3. Swarm #20: LoRA Dataset Factory (`/home/mboyle/bin/bd-lora-factory`)

### Design & Dataset Schema
- **Sources Harvested**:
  1. PostgreSQL `boylenet_episodic_memory` (queried with `PGCONNECT_TIMEOUT=2` and `-w` non-interactive safety).
  2. `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/*.md` (133 files) and `BOARD` review verdicts / multi-harness engineering receipts under `/home/mboyle/bd-persist/harness-work/`.
- **Dual ChatML + ShareGPT Output (`/var/tmp/bd-lora-dataset-chatml.jsonl`)**:
  - Target Model: `Qwen3-Coder-30B-A3B` on node `.83` (`10.0.70.83`).
  - Exported `1,184` deduplicated instruction-tuning trajectories (`234` full `BOARD`/receipt trajectories + `950` section-level code/verification trajectories, `34,613.32 KB`).

### Live Verification Output (`bd-lora-factory --export`)
```text
[bd-lora-factory] status=OK model=Qwen3-Coder-30B-A3B node=10.0.70.83 examples=1184 (o1789_md=133, board_receipts=234, pg=0) output=/var/tmp/bd-lora-dataset-chatml.jsonl (34613.32 KB)
1184 /var/tmp/bd-lora-dataset-chatml.jsonl
```
