# BD-KB Volume 24: Codex Astra Deep Bible — vCenter 10.0.20.70, 4-ESXi Hosts, 54-VM Post-Resize RAM & vSAN/NFS Storage
> **Author**: `Spec-224` (`codex (gpt-6-astra)`)
> **Generated At**: `2026-10-19T22:57:39Z`
> **Scope**: Strictly Read-Only Infrastructure, Tools, Services, LLMs & PM Catch-Up Documentation (`O3416` Compliant)

---

# BD-KB Volume 24: Codex Astra Deep Bible — vCenter 10.0.20.70, 4-ESXi Hosts, 54-VM Post-Resize RAM & vSAN/NFS Storage

**Author:** Spec-224  
**Document mode:** Read-only documentation from supplied source context  
**Evidence date:** 2026-10-19  
**Management endpoint:** `10.0.20.70`

This volume documents the supplied hardware inventory, ESXi power-policy receipts, named VM memory allocations, guest vNUMA topology, and the evidence boundaries surrounding the 54-VM estate and its vSAN/NFS storage.

The strongest supplied evidence covers four ESXi hosts and the two guests `AI` and `AI DEV`. The requested post-resize allocations cover five VMs. A complete 54-VM export, host memory totals, and storage inventory are absent. Consequently, this guide distinguishes recorded observations, allocations supplied in the task, calculations, and information that remains unverified.

No tools, commands, infrastructure changes, or live verification were performed while preparing this document.

## 1. Source Provenance and Interpretation

The supplied material contains one explicitly absent receipt and an excerpt of one available receipt.

| Source | Availability | What it can establish |
|---|---|---|
| `AGENT175-POST-RESIZE-VCENTER-RESOURCE-REPOLL-RECEIPTS.md` | Explicitly absent | No post-resize vCenter repoll evidence is available from this file |
| `AGENT170-ESXI-POWER-SYSCTL-AND-ACTION4-KIMI-RECEIPTS.md` | First 12,000 bytes supplied | Host hardware and power-policy observations, `.72`/`.83` topology, guest configuration records, and partial benchmark provenance |
| User-provided scope and allocations | Supplied directly | Intended 54-VM estate scope and the five named post-resize RAM allocations |

The available Agent-170 receipt was written at `2026-10-19T02:20:30Z`. It records work associated with `Agent-158`, `Agent-157`, and Action #4 under `O1789`.

Four evidence categories apply throughout this guide:

- **Recorded observation:** A value appears in the supplied receipt or its quoted output.
- **Specified allocation:** A value is explicitly supplied in the task but lacks the missing post-resize repoll.
- **Derived value:** Arithmetic follows directly from supplied quantities.
- **Unknown:** The supplied context does not establish the value.

The excerpt ends during the Action #4 discussion. Its missing remainder cannot establish additional benchmark results or final conclusions.

## 2. vCenter and Addressing

The documented vCenter endpoint is **`10.0.20.70`**. The receipt states that the vNUMA configuration helper was tested against this endpoint using its check mode.

The four ESXi hosts have management addresses on `10.0.20.0/24` and separate addresses identified by the source as specification aliases.

| ESXi host | Hostname recorded in receipts | Management IP, identified as `vmk3` | Specification alias |
|---|---|---|---|
| `esxi01` | `esxi01.boylenet.themfboyles.com` | `10.0.20.41` | `10.0.70.201` |
| `esxi02` | `esxi02.boylenet.themfboyles.com` | `10.0.20.42` | `10.0.70.202` |
| `esxi03` | `esxi03.boylenet.themfboyles.com` | `10.0.20.43` | `10.0.70.203` |
| `esxi04` | `esxi04.boylenet.themfboyles.com` | `10.0.20.44` | `10.0.70.204` |

The aliases should retain that description. The source does not identify them as vSAN, NFS, vMotion, or other storage-network interfaces.

The following vCenter properties remain unknown:

- vCenter version, build, appliance sizing, and deployment topology.
- Datacenter and cluster names.
- ESXi software versions and builds.
- HA and DRS configuration.
- Admission-control policy and failover capacity.
- Cluster EVC settings.
- Datastore names, capacities, and attachment relationships.

The documented **per-VM** EVC setting on two guests does not establish a cluster-wide EVC baseline.

## 3. Physical Hardware Inventory

All four hosts are dual-socket HPE-family ProLiant servers. The estate combines two Gen9 systems and two Gen10 systems.

| Host | Platform | Identifier supplied | CPU packages | Physical cores | Hardware threads | Hyperthreading |
|---|---|---|---:|---:|---:|---|
| `esxi01` | HP ProLiant DL380 Gen9 | Serial `MQX73007P7` | 2 | 28 | 56 | Active, supported, enabled |
| `esxi02` | HP ProLiant DL380 Gen9 | BIOS asset tag `9002532` | 2 | 44 | 88 | Active, supported, enabled |
| `esxi03` | HPE ProLiant DL380 Gen10 | Serial `2M291803HL` | 2 | 52 | 104 | Active, supported, enabled |
| `esxi04` | HPE ProLiant DL380 Gen10 | Serial `2M292001B2` | 2 | 52 | 104 | Active, supported, enabled |
| **Total** | **4 hosts** | — | **8** | **176** | **352** | — |

These totals describe physical inventory. They do not express available CPU capacity after reservations, running workloads, hypervisor overhead, or host failures.

The `esxi02` identifier is an **asset tag**, not a verified serial number. Although the receipt’s summary combines serial and UUID information under one heading, its detailed output labels `9002532` as `BIOS Asset Tag`.

All four hosts report IPMI support. The supplied output also records `HV Support: 3`; the source does not provide a decoding of that field.

Physical RAM, DIMM layout, memory speed, disk inventory, storage controllers, network adapters, and physical GPU assignments are not supplied. Host memory headroom therefore cannot be calculated.

### CPU Heterogeneity

The hosts differ materially in core count. A 52-vCPU VM has the same numerical vCPU count as the physical-core count recorded for either Gen10 host. That numerical match does not prove exclusive use of those cores.

The same 52-vCPU configuration exceeds the total physical-core count of either Gen9 host. Hardware threads should remain separately accounted for: 56 hardware threads on `esxi01` do not mean 56 physical cores.

Guest-visible processor descriptions associate `AI` with a Xeon Platinum 8170 and `AI DEV` with a Xeon Platinum 8164. Those observations are documented below. No processor model is supplied for either Gen9 host.

## 4. ESXi High Performance Policy and BIOS Ownership

All four hosts have matching recorded ESXi power policies.

| Host | Policy ID | Policy name | Short name |
|---|---:|---|---|
| `esxi01` | 1 | High Performance | `static` |
| `esxi02` | 1 | High Performance | `static` |
| `esxi03` | 1 | High Performance | `static` |
| `esxi04` | 1 | High Performance | `static` |

Both the ESXi policy output and the internal current-policy output report this state. The recorded policy description is “Do not use any power management features.”

This establishes the selected **ESXi policy at the receipt time**. It does not independently establish sustained processor frequency, turbo residency, package power, thermal limits, or the exact firmware power profile.

### BIOS Diagnostic Finding

Across all four hosts, the hardware-support output contains an empty `CPU power management` field. Agent-170 interprets this as BIOS-controlled power regulation with OS-controlled P-state support unavailable to ESXi.

The receipt attributes that condition to HPE firmware withholding the relevant ACPI P-state interfaces when OS Control Mode is not exposed. It identifies the HPE Power Regulator setting as the remaining authority over frequency governance.

The documentation distinction is:

- **Direct observation:** ESXi reports High Performance, and the hardware-support field is empty.
- **Source diagnosis:** Firmware owns power regulation rather than exposing OS-controlled P-states.
- **Unverified detail:** The exact selected HPE firmware profile is not shown in an iLO or RBSU receipt.

Accordingly, this volume does not claim that every host is independently verified in HPE Static High Performance mode. The source discusses that profile and Dynamic Power Savings as relevant alternatives without supplying the actual firmware setting for each machine.

The receipt also makes a broader statement about preventing deep core power states. The supplied policy output alone is insufficient to prove the absence of all firmware-controlled power-state transitions.

## 5. The 54-VM Estate: Inventory Coverage

The task identifies a **54-VM estate**. Only five VM names and corresponding address suffixes are supplied. Of those five, two have explicit full IP addresses, guest names, and host placements in the available receipt.

| VM | Address supplied | Guest identity | Recorded ESXi placement | Inventory evidence |
|---|---|---|---|---|
| `Test5` | `.164` | Not supplied | Not supplied | Named in task |
| `AI` | `10.0.70.72` | `ai-srv01` | `esxi04` | Task and Agent-170 receipt |
| `AI DEV` | `10.0.70.83` | `aidev` | `esxi03` | Task and Agent-170 receipt |
| `AI Inference` | `.125` | Not supplied | Not supplied | Named in task |
| `llama` | `.228` | Not supplied | Not supplied | Named in task |
| Remaining 49 VMs | Not supplied | Not supplied | Not supplied | Individual records absent |

The three suffix-only addresses remain suffixes here because their full addresses are not explicitly supplied. Likewise, the 49 unidentified VMs are not assigned invented names, roles, or placements.

This is the complete inventory that can be documented from the provided context. It is **not a complete enumeration of all 54 VM objects**.

A full inventory would additionally identify each VM’s stable vCenter object identity, power state, current host, CPU allocation, RAM configuration, reservation, memory limit, storage placement, network attachment, and snapshot status. None of those fields should be inferred for unidentified machines.

## 6. Post-Resize RAM Allocation

The requested post-resize allocation baseline is:

| VM | Address | Specified RAM | Binary equivalent in MiB | Evidence status |
|---|---|---:|---:|---|
| `Test5` | `.164` | **250 GiB** | 256,000 MiB | User-specified; repoll absent |
| `AI` | `.72` | **320 GiB** | 327,680 MiB | User-specified; repoll absent |
| `AI DEV` | `.83` | **400 GiB** | 409,600 MiB | User-specified; repoll absent |
| `AI Inference` | `.125` | **192 GiB** | 196,608 MiB | User-specified; repoll absent |
| `llama` | `.228` | **160 GiB** | 163,840 MiB | User-specified; repoll absent |
| **Named-VM subtotal** | — | **1,322 GiB** | **1,353,728 MiB** | Derived from specified allocations |

The five named allocations total approximately **1.291 TiB**. This subtotal excludes the other 49 VMs and all host overhead.

The two VMs with recorded placements account for:

- `AI` on `esxi04`: specified allocation of **320 GiB**.
- `AI DEV` on `esxi03`: specified allocation of **400 GiB**.
- Combined specified allocation: **720 GiB**.

These are documented allocations and historical placements, not a current host-capacity assessment.

### Memory Quantities Must Remain Distinct

Configured VM memory, guest-visible memory, host-consumed memory, reserved memory, and process resident memory answer different questions.

The supplied data does not establish that any named VM has a full memory reservation. It also does not establish memory limits, ballooning, host swapping, guest swapping, or actual application residency.

Because the post-resize vCenter repoll is absent, the five specified allocations cannot be promoted to independently verified vCenter settings. The guest observations below provide useful topology evidence but do not replace that missing configuration receipt.

## 7. vNUMA Topology on `AI` and `AI DEV`

Agent-170 records the same CPU and vNUMA configuration for both guests.

| Property | `AI` — `.72` | `AI DEV` — `.83` |
|---|---|---|
| Recorded host | `esxi04` | `esxi03` |
| Guest-visible processor | Xeon Platinum 8170 @ 2.10 GHz | Xeon Platinum 8164 @ 2.00 GHz |
| vCPU count | 52 | 52 |
| Virtual sockets | 2 | 2 |
| Cores per virtual socket | 26 | 26 |
| Guest threads per core | 1 | 1 |
| vNUMA nodes | 2 | 2 |
| Node 0 CPUs | 0–25 | 0–25 |
| Node 1 CPUs | 26–51 | 26–51 |
| `numa.autosize.vcpu.maxPerVirtualNode` | 26 | 26 |
| `numa.vcpu.preferHT` | `FALSE` | `FALSE` |
| Per-VM EVC | `intel-skylake` | `intel-skylake` |

Both guests expose `avx512f`, `avx512dq`, `avx512cd`, `avx512bw`, and `avx512vl`.

The guest reports one thread per core while the hosts report active hyperthreading. These describe different layers: the virtual topology presented to the guest and the physical processor topology available to ESXi.

Neither the virtual socket layout nor `numa.vcpu.preferHT = FALSE` proves dedicated core allocation or permanent CPU pinning. The supplied evidence also does not show actual local-versus-remote memory access rates.

### Recorded NUMA Memory

The receipt reports the following node sizes using its original `MB` label:

| Guest | Node 0 size | Node 1 size | Arithmetic total |
|---|---:|---:|---:|
| `AI` | 161,162 MB | 161,267 MB | 322,429 MB |
| `AI DEV` | 194,469 MB | 194,488 MB | 388,957 MB |

If interpreted as MiB-sized units, those totals are approximately **314.87 GiB** and **379.84 GiB**, respectively. That conversion is explicitly conditional; the original output labels are preserved above.

These observations do not exactly equal the specified 320 GiB and 400 GiB allocations. The source does not provide enough timing or configuration detail to reconcile the differences. In particular, the missing post-resize repoll prevents establishing whether all observations represent the same configuration state.

The node sizes are approximately balanced within each guest. Balanced reported capacity does not establish balanced application allocations or effective memory locality.

### NUMA Distances

Both guests report the same distance matrix:

| Origin node | Destination node 0 | Destination node 1 |
|---|---:|---:|
| Node 0 | 10 | 20 |
| Node 1 | 20 | 10 |

These are relative topology distances. They are not measured nanosecond latencies and should not be interpreted as proof that remote access takes exactly twice as long.

The receipt identifies `/home/mboyle/bin/bd-apply-vnuma-52vcpu.sh` as a configuration helper supporting several management mechanisms. It reports a successful check against vCenter. The helper’s contents are not supplied, and this guide neither executes it nor independently validates its implementation.

## 8. Guest Memory and Runtime Baseline

Agent-170 records the following settings as applied and verified on `.72` and `.83`.

| Setting | Recorded value | Documentation meaning |
|---|---|---|
| `kernel.numa_balancing` | `0` | Automatic NUMA balancing disabled |
| `vm.swappiness` | `1` | Low swappiness preference |
| `vm.overcommit_memory` | `1` | Permissive memory overcommit policy |
| Transparent HugePages | `[always] madvise never` | `always` selected |
| `GGML_OP_OFFLOAD_MIN_BATCH` | `2048` | Recorded environment configuration |

Sysctl persistence is recorded in `/etc/sysctl.d/99-boylenet-llm.conf`. Transparent HugePages persistence is recorded in `/etc/tmpfiles.d/thp-boylenet.conf`. The GGML variable is recorded in `/etc/environment` and service configuration.

These settings do not increase physical RAM. Low swappiness does not disable swap, permissive overcommit does not guarantee that allocations can become resident, and an enabled Transparent HugePages policy does not prove that every model allocation uses huge pages.

Disabling automatic NUMA balancing also does not prove correct application placement. Thread scheduling and memory residency remain separate observations.

### Services Covered by the Receipt

| Guest | Services named |
|---|---|
| `.72` | `llama-qwythos-18081.service`, `llama-rpc.service`, `llama-rpc-worker.service`, `llama-tier1-alpha.service`, `llama-tier23.service`, `ollama.service` |
| `.83` | `llama-heretic-18080.service`, `llama-rpc.service`, `llama-rpc-worker.service`, `ollama.service` |

The receipt records service configuration changes and a systemd configuration reload. That evidence does not by itself prove every running process inherited the variable; process environment evidence or a documented restart would establish that separately.

## 9. vSAN and NFS Storage

vSAN and NFS are part of the requested guide scope, but the supplied excerpt contains **no concrete storage inventory**.

| Storage property | Evidence available |
|---|---|
| vSAN cluster membership and architecture | Not supplied |
| Disk groups or storage pools | Not supplied |
| Devices, controllers, and fault domains | Not supplied |
| vSAN capacity, free space, and usable capacity | Not supplied |
| Storage policies and failure tolerance | Not supplied |
| Object health and resynchronization backlog | Not supplied |
| NFS server addresses and export paths | Not supplied |
| NFS protocol versions | Not supplied |
| NFS datastore capacity and free space | Not supplied |
| VM-to-datastore mappings | Not supplied |
| Storage latency, throughput, and congestion | Not supplied |

This absence prevents any defensible claim about storage headroom, redundancy, health, or which storage system backs a particular VM.

### Capacity Interpretation

Raw device capacity, datastore capacity, free space, and policy-adjusted usable capacity are different quantities. For vSAN, a capacity statement needs its storage-policy context. For NFS, it needs the actual exported filesystem or datastore context.

VM logical disk sizes also do not establish physical storage consumption. The supplied source contains no disk-provisioning, snapshot, replication, or space-reclamation information from which consumption could be calculated.

RAM allocation and storage capacity must remain separate. The **1,322 GiB** named-VM RAM subtotal is not a storage-capacity figure and does not establish how much model data can reside in memory simultaneously.

### Model Paths and Storage Backing

The receipt names `/opt/models/.bench.lock` on `.72`. This establishes a guest path used for benchmark coordination. It does not identify the backing datastore.

The source also names benchmark files under `/tmp` and model metadata artifacts. None of those pathnames establishes whether the underlying storage is a virtual disk on vSAN, a virtual disk on NFS, a direct guest mount, or another arrangement.

A complete storage record would connect each relevant guest path to its mount, virtual disk or network filesystem, datastore or export, capacity, policy, and observation time.

## 10. Model Fit and Benchmark Provenance

The Action #4 excerpt is relevant because model capacity claims can otherwise be mistaken for verified infrastructure capability.

For row `#22`, `KIMI-K3-MXP4-DERISKED-Q2_K`, the source records:

| Property | Recorded value |
|---|---|
| Model size | 940.18 GiB |
| Shards | 38 |
| Previously listed prompt-processing rate | 69.13 tok/s |
| Previously listed generation rate | 16.30 tok/s |
| Provenance finding | Synthetic or analytical four-node projections |

The receipt explicitly states that those rates were not produced by an actual four-node distributed CPU RAM benchmark. They must not be cited as measured performance.

For row `#68`, `kimi-k3-abliterated-iq1s-gguf`, the supplied text records a **539.71 GiB** model across **34 shards**. It identifies actual hardware measurements of approximately **0.0085 tok/s** prompt processing and **0.0144 tok/s** generation before the excerpt ends.

The source also identifies `74.20/18.40` as another pair under review as projected four-node figures. The missing remainder prevents reconstructing the complete audit outcome.

### Why Aggregate RAM Does Not Prove Model Fit

Although the five specified allocations total more than either model size, they belong to separate VMs. Their sum does not establish a distributed execution arrangement.

Model fit additionally depends on operating-system usage, runtime buffers, context and cache requirements, worker distribution, actual resident memory, and any other workloads sharing the guests.

The supplied excerpt identifies an audit lock interval on `.72` from `02:18:11Z` to `02:19:39Z`. That coordination record does not itself demonstrate successful model loading or residency across four workers.

No four-node fit or throughput guarantee follows from the named-VM RAM subtotal.

## 11. Capacity and Availability Boundaries

The supplied source supports a physical CPU inventory and a partial VM allocation record. It does not support a cluster admission or failover calculation.

| Assessment | Missing inputs |
|---|---|
| Normal-operation memory headroom | Installed host RAM, complete VM allocations, overhead, reservations, and consumption |
| One-host-failure capacity | Host RAM, placement constraints, HA policy, and surviving workload distribution |
| CPU contention | Complete vCPU inventory, utilization, ready time, and scheduler observations |
| NUMA locality | Physical memory topology and workload locality measurements |
| Migration eligibility | CPU compatibility, device attachments, EVC, and placement constraints |
| Storage resilience | Datastore topology, policies, health, and fault domains |
| Distributed model capacity | Participating workers, available RAM, runtime configuration, and per-worker residency |

Historical placement of `AI` on `esxi04` and `AI DEV` on `esxi03` should remain timestamped evidence. It does not establish permanent affinity.

Likewise, all four hosts reporting High Performance does not establish identical performance. Their core counts differ, and the source lacks memory bandwidth, operating frequency, contention, and storage measurements.

## 12. Authoritative Reference Ledger

The durable record supported by this source is:

| Subject | Supported statement |
|---|---|
| vCenter | Endpoint identified as `10.0.20.70` |
| ESXi hosts | Four hosts, `esxi01` through `esxi04` |
| Hardware totals | 8 packages, 176 physical cores, 352 hardware threads |
| ESXi power policy | All four recorded as High Performance, policy ID 1 |
| Firmware power control | BIOS ownership is the receipt’s diagnosis; exact firmware profiles absent |
| Estate size | 54 VMs specified by the task; five individually named |
| Post-resize RAM | 250, 320, 400, 192, and 160 GiB for the five named VMs |
| Named RAM subtotal | 1,322 GiB |
| `.72` and `.83` topology | 52 vCPUs, two sockets, 26 cores per socket, two vNUMA nodes |
| Per-VM EVC | `intel-skylake` recorded for `.72` and `.83` |
| Guest configuration | Recorded sysctl, THP, and GGML settings on `.72` and `.83` |
| Full post-resize verification | Unavailable because Agent-175 receipt is absent |
| vSAN/NFS inventory | Not supplied |
| Four-node Kimi performance | Projected figures must not be represented as measured results |

Future revisions should attach dated evidence for the missing 54-VM export, post-resize vCenter configuration, host RAM inventory, and storage mappings. Until those records exist, the specified allocations remain clearly identified as the requested baseline, and the absent inventory and storage fields remain unknown.
