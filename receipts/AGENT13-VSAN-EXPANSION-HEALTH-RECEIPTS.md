# AGENT-13: `esxi03` 9th Enterprise 12G SAS SSD Expansion, Orphaned `lens-*` VM Inventory Hygiene & vSAN Cluster Health Receipts

- **Timestamp (UTC)**: `2026-10-17T20:20:36Z`
- **Seat**: Agent-13 (vSAN Health, Inventory Hygiene & `esxi03` 9th Enterprise SAS SSD Expansion Engineer)
- **Cluster / vCenter**: `Boylenet Cluster` (`domain-c85`) on `vc04.boylenet.themfboyles.com` (`10.0.20.70`, VMware vCenter Server `8.0.3 build-24322831`)
- **Operator Directive Honored**: `"The ssd stays"` — **zero SSDs removed from vSAN**. All 6 existing capacity SSDs (`5x 1.92TB HGST` + `1x 512GB LiteOn`) in `esxi03` `DG 528858fc-16cc-a790-ee2a-218b8cdb3cd8` remain untouched, and `naa.5000cca06cac4660` (`1.92TB HGST HSCP1920S5xnFMRI`, Bay `1I:1:1`, `87%` life remaining) was added as the **7th capacity SSD** (`vSAN UUID 524c337a-0aaa-59f4-8a6a-e9ba26fa390b`).
- **Raw Snapshot Artifacts**:
  - Pre-expansion `esxcli vsan storage list` (`esxi03`): `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/agent13_esxi03_vsan_storage_before.txt` (`20,464` bytes, `RC=0`)
  - Post-expansion `esxcli vsan storage list` (`esxi03`): `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/agent13_esxi03_vsan_storage_final.txt` (`21,320` bytes, `RC=0`)
  - Post-expansion `esxcli vsan debug disk list`: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/agent13_esxi03_vsan_debug_disk_final.txt` (`RC=0`)
  - Post-expansion `esxcli vsan health cluster list`: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/agent13_vsan_health_cluster_final.txt` (`RC=0`)
  - JSON telemetry bundle: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/agent13_results.json`

---

## 1. Claiming `esxi03`'s 9th 1.92TB HGST Enterprise 12G SAS SSD (`naa.5000cca06cac4660`) into All-Flash `DG 528858fc-16cc-a790-ee2a-218b8cdb3cd8`

### 1.1 Pre-Expansion Inspection & Technical Notes
1. **Exact Disk Group & Cache SSD Identifiers on `esxi03` (`10.0.20.43`)**:
   - All-Flash Disk Group UUID: `528858fc-16cc-a790-ee2a-218b8cdb3cd8` *(referred to as `DG 528858fc` in the brief)*
   - Cache Tier SSD: `naa.5000cca06cace644` (Bay `1I:1:4`, `HGST HSCP1920S5xnFMRI`, `1.92TB`, `77%` life remaining)
   - Pre-Existing Capacity Tier SSDs (`6` drives, max supported by vSAN OSA = `7` capacity drives per Disk Group):
     1. `naa.5000cca06cabe218` (Bay `1I:1:2`, `1.92TB HGST SAS SSD`, `VSANUUID: 523689c4-7f78-ee7f-5f8b-cc497be6fa16`)
     2. `naa.5000cca06cabbeb4` (Bay `1I:1:3`, `1.92TB HGST SAS SSD`, `VSANUUID: 52b8b78d-7a2a-2b44-14b8-84533bf56430`)
     3. `naa.5000cca06cabeba0` (Bay `1I:1:6`, `1.92TB HGST SAS SSD`, `VSANUUID: 524140ce-400e-dc1b-bbf2-ca0597d61a0c`)
     4. `naa.5000cca06cabc420` (Bay `1I:1:7`, `1.92TB HGST SAS SSD`, `VSANUUID: 52684152-cffe-58c3-7fa6-c2bfcec9ce3f`)
     5. `naa.5000cca06cad6c2c` (Bay `1I:1:17`, `1.92TB HGST SAS SSD`, `VSANUUID: 52063ad5-1fcd-8f92-0182-10aaada35162`)
     6. `t10.ATA_____LITEON_CV32D8D5122D41_SATA_512GB_SED______TW0MN0K7LOH0075G01ZI` (Rear SATA AHCI, `512GB LiteOn SSD`, `VSANUUID: 528ed6a3-4fba-eb63-7479-362374c6f9c4`) — **retained per `"The ssd stays"`**.
2. **Why `capacityFlash` Tagging & `VsanVcDiskManagementSystem.InitializeDiskMappings` Were Required**:
   - Calling raw `esxcli vsan storage add` on an untagged SSD returns `Unable to add device: Disk: naa.5000cca06cac4660 is an SSD and can not be used as a vSAN HDD` (`RC=1`) because `IsCapacityFlash` defaults to `0`.
   - Furthermore, because `Boylenet Cluster` has **vSAN Data-At-Rest Encryption (`Encryption: true`, KMS `Boylenet`)** enabled, extending an All-Flash Disk Group via vCenter's `VsanVcDiskManagementSystem.InitializeDiskMappings` (`vim.vsan.host.DiskMappingCreationSpec(host=esxi03, cacheDisks=[ssd_cace644], capacityDisks=[cap_cac4660], creationType="allFlash")`) coordinates the new Disk Encryption Key (`dekId`) from the vCenter Native Key Provider (`Boylenet`) and mounts the disk online with `Encryption: true` and `DiskKeyLoaded: true`.

### 1.2 Raw `esxcli` & `pyVmomi` Claim Execution Receipts (`RC=0`)
```text
=== 1. Clean GPT Label & Tag naa.5000cca06cac4660 as capacityFlash on esxi03 (10.0.20.43) ===
CMD: partedUtil mklabel /vmfs/devices/disks/naa.5000cca06cac4660 gpt
CMD: govc host.esxcli -u 'https://root:Matt99!!@10.0.20.43/sdk' -k vsan storage tag add -d naa.5000cca06cac4660 -t capacityFlash
EXIT_CODE: 0
STDOUT:
true

CMD: vdq -q -d naa.5000cca06cac4660
EXIT_CODE: 0
STDOUT:
[
   {
      "Name"     : "naa.5000cca06cac4660",
      "VSANUUID" : "",
      "State"    : "Eligible for use by VSAN",
      "Reason"   : "None",
"StoragePoolState": "Eligible for use by Storage Pool",
"StoragePoolReason": "None",
      "IsSSD"    : "1",
"IsCapacityFlash": "1",
      "IsPDL"    : "0",
      "Size(MB)" : "1831420",
    "FormatType" : "512e",
   "IsVsanDirectDisk" : "0"
   }
]

=== 2. Claim naa.5000cca06cac4660 into All-Flash DG 528858fc-16cc-a790-ee2a-218b8cdb3cd8 via InitializeDiskMappings ===
Found Cache SSD: naa.5000cca06cace644 and Capacity SSD: naa.5000cca06cac4660
Launched task: 'vim.Task:task-775023'
  Task state: running progress: 0
  Task state: running progress: 20
  Task state: running progress: 100
Final task state: success
EXIT_CODE: 0
```

### 1.3 Post-Expansion `esxcli vsan storage list` & `esxcli vsan debug disk list` Verification (`RC=0`)
```text
=== govc host.esxcli -u 'https://root:Matt99!!@10.0.20.43/sdk' -k vsan storage list (RC=0) ===
Checksum:                      16894100035943954403
ChecksumOK:                    true
Compression:                   false
CreationTime:                  Sat Oct 17 20:17:35 2026
Deduplication:                 false
Device:                        naa.5000cca06cac4660
DiskKeyLoaded:                 true
DisplayName:                   naa.5000cca06cac4660
Encryption:                    true
EncryptionMetadataChecksumOK:  true
InCMMDS:                       true
IsCapacityTier:                true
IsMounted:                     true
IsSSD:                         true
Ondiskformatversion:           20
Usedbythishost:                true
VSANDiskGroupName:             naa.5000cca06cace644
VSANDiskGroupUUID:             528858fc-16cc-a790-ee2a-218b8cdb3cd8
VSANUUID:                      524c337a-0aaa-59f4-8a6a-e9ba26fa390b

=== govc host.esxcli -u 'https://root:Matt99!!@10.0.20.43/sdk' -k vsan debug disk list (RC=0) ===
Compression:        false
CongestionHealth:   
CongestionValue:    0
DedupRatio:         N/A
Deduplication:      false
DiskGroup:          528858fc-16cc-a790-ee2a-218b8cdb3cd8
DiskTier:           Capacity
Encryption:         true
FaultDomain:        N/A
InCmmds:            true
InVsi:              true
MetadataHealth:     green
Model:              HSCP1920S5xnFMRI
Name:               naa.5000cca06cac4660
OperationalHealth:  green
OverallHealth:      green
Owner:              ESXI03
SSD:                true
State:              green, green
UUID:               524c337a-0aaa-59f4-8a6a-e9ba26fa390b
Version:            20
```

### 1.4 Complete `esxi03` All-Flash Disk Group `528858fc-16cc-a790-ee2a-218b8cdb3cd8` Roster After Expansion (`1` Cache SSD + `7` Capacity SSDs)
| # | Physical Bay | Device (`naa.*` / `t10.*`) | Model | Role / Tier | `VSANUUID` | `Encryption` | `IsMounted` | `InCMMDS` | vSAN Formatted Cap |
| :-: | :--- | :--- | :--- | :--- | :--- | :-: | :-: | :-: | ---: |
| 0 | `1I:1:4` | `naa.5000cca06cace644` | `HGST HSCP1920S5xnFMRI` (1.92TB SAS) | **Cache SSD** | `528858fc-16cc-a790-ee2a-218b8cdb3cd8` | `true` | `true` | `true` | `0.00 GiB` (Cache) |
| 1 | **`1I:1:1`** | **`naa.5000cca06cac4660`** | **`HGST HSCP1920S5xnFMRI` (1.92TB SAS)** | **Capacity SSD #7 (NEW)** | **`524c337a-0aaa-59f4-8a6a-e9ba26fa390b`** | **`true`** | **`true`** | **`true`** | **`1,788.49 GiB` (`1.75 TiB` / `1.92 TB`)** |
| 2 | `1I:1:2` | `naa.5000cca06cabe218` | `HGST HSCP1920S5xnFMRI` (1.92TB SAS) | Capacity SSD #1 | `523689c4-7f78-ee7f-5f8b-cc497be6fa16` | `true` | `true` | `true` | `1,788.49 GiB` (`1.75 TiB` / `1.92 TB`) |
| 3 | `1I:1:3` | `naa.5000cca06cabbeb4` | `HGST HSCP1920S5xnFMRI` (1.92TB SAS) | Capacity SSD #2 | `52b8b78d-7a2a-2b44-14b8-84533bf56430` | `true` | `true` | `true` | `1,788.49 GiB` (`1.75 TiB` / `1.92 TB`) |
| 4 | `1I:1:6` | `naa.5000cca06cabeba0` | `HGST HSCP1920S5xnFMRI` (1.92TB SAS) | Capacity SSD #3 | `524140ce-400e-dc1b-bbf2-ca0597d61a0c` | `true` | `true` | `true` | `1,788.49 GiB` (`1.75 TiB` / `1.92 TB`) |
| 5 | `1I:1:7` | `naa.5000cca06cabc420` | `HGST HSCP1920S5xnFMRI` (1.92TB SAS) | Capacity SSD #4 | `52684152-cffe-58c3-7fa6-c2bfcec9ce3f` | `true` | `true` | `true` | `1,788.49 GiB` (`1.75 TiB` / `1.92 TB`) |
| 6 | `1I:1:17` | `naa.5000cca06cad6c2c` | `HGST HSCP1920S5xnFMRI` (1.92TB SAS) | Capacity SSD #5 | `52063ad5-1fcd-8f92-0182-10aaada35162` | `true` | `true` | `true` | `1,788.49 GiB` (`1.75 TiB` / `1.92 TB`) |
| 7 | Rear AHCI | `t10.ATA_____LITEON_CV32D8D5122D41_SATA_512GB_SED______TW0MN0K7LOH0075G01ZI` | `LITEON CV3-8D512` (512GB SATA) | Capacity SSD #6 (Kept) | `528ed6a3-4fba-eb63-7479-362374c6f9c4` | `true` | `true` | `true` | `476.93 GiB` (`0.47 TiB` / `0.51 TB`) |
| — | **TOTAL** | **8 SSDs (`1` Cache + `7` Capacity)** | — | **All-Flash Disk Group** | `528858fc-16cc-a790-ee2a-218b8cdb3cd8` | `true` | `true` | `true` | **`11,207.87 GiB` (`10.95 TiB` / `12.03 TB`)** |

### 1.5 Before vs. After Cluster Capacity (`QuerySpaceUsage` & `vsanDatastore`, `RC=0`)
| Metric | Before Expansion (`19:44:34Z`) | After Expansion (`20:19:46Z`) | Exact Delta |
| :--- | ---: | ---: | ---: |
| **`esxi03` Total vSAN Disks** (`esxcli vsan storage list`) | `24` (`20` Cap + `4` Cache) | **`25` (`21` Cap + `4` Cache)** | **`+1` Capacity SSD** |
| **`esxi03` `DG 528858fc` Capacity SSDs** | `6` (`5x 1.92TB` + `1x 512GB`) | **`7` (`6x 1.92TB` + `1x 512GB`)** | **`+1` (`1.92TB HGST SAS SSD`)** |
| **`QuerySpaceUsage.totalCapacityB`** | `359,213,226,262,528 B` | **`361,133,600,931,840 B`** | **`+1,920,374,669,312 B`** |
| **Total Cluster Capacity (`TiB` / `TB`)** | `326.7025 TiB` (`359.2132 TB`) | **`328.4491 TiB` (`361.1336 TB`)** | **`+1.7466 TiB` (`+1.9204 TB`)** |
| **`QuerySpaceUsage.freeCapacityB`** | `219,961,111,098,979 B` | **`222,004,937,271,729 B`** | **`+2,043,826,172,750 B`** |
| **Cluster Free Space (`TiB` / `TB`)** | `200.0535 TiB` (`219.9611 TB`) | **`201.9123 TiB` (`222.0049 TB`)** | **`+1.8588 TiB` (`+2.0438 TB`)** |
| **`vsanDatastore.summary.capacity`** | `359,213,226,262,528 B` | **`361,133,600,931,840 B`** | **`+1,920,374,669,312 B` (`+1.75 TiB`)** |
| **`vsanDatastore.summary.freeSpace`** | `221,150,702,772,498 B` | **`223,297,431,771,569 B`** | **`+2,146,728,999,071 B` (`+1.95 TiB`)** |

---

## 2. Unregistration of 22 Zero-Byte `orphaned` `lens-*` VM Inventory Ghosts in vCenter

### 2.1 Pre- vs. Post-Cleanup vCenter Inventory Summary (`pyVmomi`, `RC=0`)
| Inventory Metric | Before Cleanup | After Cleanup | Delta |
| :--- | ---: | ---: | ---: |
| **Total VMs in vCenter Inventory** | `86` | **`64`** | **`-22`** |
| **Total `orphaned` VMs (`runtime.connectionState == 'orphaned'`)** | `22` | **`0`** | **`-22`** |
| **Orphaned `lens-*` VMs (`connectionState == 'orphaned'` & `name.startswith('lens-')`)** | `22` | **`0`** | **`-22`** |

### 2.2 Per-VM `vm.UnregisterVM()` Receipts (`22/22` `SUCCESS`, `RC=0`)
| # | VM Name | `moId` | Pre-Cleanup `connectionState` | Pre-Cleanup `powerState` | Committed Bytes on vSAN | `vm.UnregisterVM()` Result |
| :-: | :--- | :--- | :--- | :--- | ---: | :--- |
| 1 | `lens-a12-cx7-20261003` | `vm-296034` | `orphaned` | `poweredOff` | `0 B` (`0.00 GB`) | `SUCCESS (RC=0)` |
| 2 | `lens-a13-cxcorrectness2-20261003` | `vm-296012` | `orphaned` | `poweredOff` | `0 B` (`0.00 GB`) | `SUCCESS (RC=0)` |
| 3 | `lens-a18-cxcorrectness4-20261003` | `vm-296018` | `orphaned` | `poweredOff` | `0 B` (`0.00 GB`) | `SUCCESS (RC=0)` |
| 4 | `lens-c09-cx5-r2-20261004` | `vm-296044` | `orphaned` | `poweredOff` | `0 B` (`0.00 GB`) | `SUCCESS (RC=0)` |
| 5 | `lens-f001-cxcorrectness3-20261003` | `vm-296014` | `orphaned` | `poweredOff` | `0 B` (`0.00 GB`) | `SUCCESS (RC=0)` |
| 6 | `lens-f001-dhcp-cx3-20261003` | `vm-296016` | `orphaned` | `poweredOff` | `0 B` (`0.00 GB`) | `SUCCESS (RC=0)` |
| 7 | `lens-g105-cx10-relens-20261004` | `vm-296050` | `orphaned` | `poweredOff` | `0 B` (`0.00 GB`) | `SUCCESS (RC=0)` |
| 8 | `lens-g113-cx13-r3-20261004` | `vm-296049` | `orphaned` | `poweredOff` | `0 B` (`0.00 GB`) | `SUCCESS (RC=0)` |
| 9 | `lens-o1760-cx4-20261004` | `vm-296036` | `orphaned` | `poweredOff` | `0 B` (`0.00 GB`) | `SUCCESS (RC=0)` |
| 10 | `lens-o1799-cx3-r2-20261004` | `vm-296035` | `orphaned` | `poweredOff` | `0 B` (`0.00 GB`) | `SUCCESS (RC=0)` |
| 11 | `lens-o1807-r2-cxcorrectness2-20261004` | `vm-296037` | `orphaned` | `poweredOff` | `0 B` (`0.00 GB`) | `SUCCESS (RC=0)` |
| 12 | `lens-o1815-r11-cx4-20261004` | `vm-296042` | `orphaned` | `poweredOff` | `0 B` (`0.00 GB`) | `SUCCESS (RC=0)` |
| 13 | `lens-o1815-r12-cx11-20261004` | `vm-296045` | `orphaned` | `poweredOff` | `0 B` (`0.00 GB`) | `SUCCESS (RC=0)` |
| 14 | `lens-o1815-r9-cx3-20261004` | `vm-296038` | `orphaned` | `poweredOff` | `0 B` (`0.00 GB`) | `SUCCESS (RC=0)` |
| 15 | `lens-o1819-r16-cx4-20261004` | `vm-296039` | `orphaned` | `poweredOff` | `0 B` (`0.00 GB`) | `SUCCESS (RC=0)` |
| 16 | `lens-o1819-r17-bdrc-20261004` | `vm-296041` | `orphaned` | `poweredOff` | `0 B` (`0.00 GB`) | `SUCCESS (RC=0)` |
| 17 | `lens-o1826-c01-cx1-20261004` | `vm-296040` | `orphaned` | `poweredOff` | `0 B` (`0.00 GB`) | `SUCCESS (RC=0)` |
| 18 | `lens-o1876-5-bdrc-20261004` | `vm-296046` | `orphaned` | `poweredOff` | `0 B` (`0.00 GB`) | `SUCCESS (RC=0)` |
| 19 | `lens-o1876-5-cx12-20261004` | `vm-296047` | `orphaned` | `poweredOff` | `0 B` (`0.00 GB`) | `SUCCESS (RC=0)` |
| 20 | `lens-scf9-r4-cx209-20261013` | `vm-296072` | `orphaned` | `poweredOff` | `0 B` (`0.00 GB`) | `SUCCESS (RC=0)` |
| 21 | `lens-sg3-r7-cx210-20261013` | `vm-296073` | `orphaned` | `poweredOff` | `0 B` (`0.00 GB`) | `SUCCESS (RC=0)` |
| 22 | `lens-t154-edge1-cxcorrectness-20261003` | `vm-296010` | `orphaned` | `poweredOff` | `0 B` (`0.00 GB`) | `SUCCESS (RC=0)` |

---

## 3. Full vSAN Cluster Health Verification (`esxcli vsan health cluster list`, `RC=0`)

### 3.1 Summary of Storage, Network, Data & Physical Disk Health Checks
- **`Network` Category**: **`100% GREEN`** (`8/8` checks green: `Hosts with connectivity issues = green`, `vSAN cluster partition = green`, `All hosts have a vSAN vmknic configured = green`, `vSAN: Basic (unicast) connectivity check = green`, `vSAN: MTU check (ping with large packet size) = green`, `vMotion: Basic (unicast) connectivity check = green`, `vMotion: MTU check (ping with large packet size) = green`, `Network latency check = green`).
- **`Physical disk` Category**: **`100% GREEN`** (`7/7` checks green: `Operation health = green`, `Congestion = green`, `Physical disk component utilization = green`, `Component metadata health = green`, `Memory pools (heaps) = green`, `Memory pools (slabs) = green`, `Disk capacity = green`).
- **`Data` Category**: **`100% GREEN`** (`2/2` checks green: `vSAN object health = green`, `vSAN object format health = green`).
- **`SubClusterMemberCount`**: **`4/4` hosts healthy** (`ESXI01`, `ESXI02`, `ESXI03`, `ESXI04`).
- **Advisory `yellow` Checks (`0` `red` checks)**:
  1. `vSAN Disk Balance` (`yellow`): Expected while `esxi01`'s 24TB Exos drives (`~6–12%` used) and `esxi03`'s newly added `naa.5000cca06cac4660` (`0%` used) have lower utilization than `esxi03`/`esxi04`'s 1.8TB SAS drives (`~55–76%` used) and automatic rebalance is disabled.
  2. `What if the most consumed host fails` (`yellow`): Simulated 1-host-failure disk space utilization is `82.23% (128,369.95 GB of 156,107.96 GB)` if `esxi02` (`176 TiB` formatted capacity) were removed; normal current `Storage space` and `Cluster and host component utilization` (`4%`, `1,580 of 36,000`) are both **`green`**.
  3. `Stats DB object conflicts` (`yellow`): Pre-existing renamed `.vsan.stats-1` and `.vsan.stats-2` directories documented in `AGENT5-FTT-ORPHAN-RECEIPTS.md` Section 3.3; active `Stats DB object`, `Stats primary election`, `Performance data collection`, and `All hosts contributing stats` are all **`green`**.

### 3.2 Raw `esxcli vsan health cluster list` Output (`RC=0`)
```text
CMD: govc host.esxcli -u 'https://root:Matt99!!@10.0.20.43/sdk' -k vsan health cluster list
EXIT_CODE: 0
STDOUT:
Health Test Name                                    Status
----------------                                    ------
Overall health findings                             yellow (Cluster health issue)
Cluster                                             yellow
  Advanced vSAN configuration in sync               green
  vSAN daemon liveness                              green
  vSAN Disk Balance                                 yellow
  Resync operations throttling                      green
  Software version compatibility                    green
  Disk format version                               green
Capacity utilization                                yellow
  Storage space                                     green
  Cluster and host component utilization            green
  What if the most consumed host fails              yellow
Performance service                                 yellow
  Stats DB object                                   green
  Stats primary election                            green
  Performance data collection                       green
  All hosts contributing stats                      green
  Stats DB object conflicts                         yellow
Network                                             green
  Hosts with connectivity issues                    green
  vSAN cluster partition                            green
  All hosts have a vSAN vmknic configured           green
  vSAN: Basic (unicast) connectivity check          green
  vSAN: MTU check (ping with large packet size)     green
  vMotion: Basic (unicast) connectivity check       green
  vMotion: MTU check (ping with large packet size)  green
  Network latency check                             green
Physical disk                                       green
  Operation health                                  green
  Congestion                                        green
  Physical disk component utilization               green
  Component metadata health                         green
  Memory pools (heaps)                              green
  Memory pools (slabs)                              green
  Disk capacity                                     green
Data                                                green
  vSAN object health                                green
  vSAN object format health                         green
```
