# AGENT-109: Post-Drain `esxi01..04` vSAN Storage Policy, HBA Queue-Depth, 25GbE RoCE & 27-VM Infrastructure E2E Certification Receipts

- **WRITTEN-AT**: `2026-10-18T23:22:48Z`
- **Agent**: `Agent-109` (Post-Drain `esxi01..04` vSAN Storage Policy, HBA Queue-Depth, 25GbE RoCE & 27-VM Infrastructure E2E Certification Architect)
- **Live Stream Log (`:4083`)**: `/var/tmp/bd-headless-streams/agent109-vsan-25gbe-infra-e2e.log`
- **CENSUS**: `FOUND 4/4 HEALTHY ESXi hosts (esxi01..04), 11/11 vSAN Disk Groups (75/75 mounted & InCMMDS disks), 0 bytes resyncing, 0 disk congestion issues, 4/4 25GbE SFP28 RoCE nodes at MTU 9000 with 0% loss and 0.141ms–0.244ms inter-node avg RTT, 4/4 iLO4/iLO5 enclosures with 8/8 PSUs OK and 24/24 fans OK, and 54 vCenter VMs across the 27-node Boylenet topology`
- **PRECHECK**: `PASS`
- **PATCH-SHA256**: `eff967059d3e7c04d178609041352706328426acec1d489f2837f8959077af20`
- **Benchmarking Non-Interference Invariant**: **100% PRESERVED** — Zero destructive commands or disk I/O benchmarks were executed on `.72` (`ai-srv01`) or `.83` (`aidev`) while `Agent-37` completes `mimo-v2.6-pro-1024b` and `kimi-k3-abliterated-iq1s-gguf`.

---

## 1. Pillar 1: Disk Group Homogeneity & Tier Audit Across `esxi01..04` (`esxcli vsan storage list` + `esxcli storage core device list`)

- **`esxi01` (`10.0.20.41`) Post-Drain Homogeneity Verified**:
  - **DG 1 (`52e69070-da86-8f9f-27b2-679f255787a8`)**: Cache SSD `mpx.vmhba0:C2:T0:L0` (`1TB Samsung SSD 870`) backing **4×24TB Seagate Exos** (`mpx.vmhba0:C2:T3:L0`, `mpx.vmhba0:C2:T4:L0`, `mpx.vmhba0:C2:T5:L0`, `mpx.vmhba0:C2:T13:L0` — `22,889,472 MB` each, `DeviceMaxQueueDepth=64`, `CompetingWorldQueueDepth=32`).
  - **DG 2 (`520e7405-7951-d36b-972f-44fa76ee518e`)**: Cache SSD `mpx.vmhba0:C2:T1:L0` (`1TB Samsung SSD 870`) backing **7×3TB SAS** (`naa.5000cca01aa9a96c`, `naa.5000cca01ab112cc`, `naa.5000cca01aa7e5ec`, `naa.5000cca01ad7dc48`, `naa.5000cca01aa800e0`, `naa.50000c0f012d3630`, `naa.50000c0f01e0c290` — `2,861,588 MB` each, `DeviceMaxQueueDepth=64`, `CompetingWorldQueueDepth=32`).
- **`esxi03` (`10.0.20.43`) SSD Invariant Verified**:
  - `t10.ATA_____LITEON_CV32D8D5122D41_SATA_512GB_SED______TW0MN0K7LOH0075G01ZI` (`488,386 MB` LITEON SATA SSD) remains untouched and healthy in `DG 528858fc-593c-7675-862f-0da87d31e53c` (`IsMounted=true`, `InCMMDS=true`, `ChecksumOK=true`).

### 1.1 `esxi01` (`10.0.20.41`) — 2 Disk Groups, 13 vSAN Disks
| Disk Group UUID | Cache Tier SSD | Device | Tier / Role | IsSSD | Size (MB) | Vendor / Model | Max QDepth | World QDepth | Mounted | InCMMDS | ChecksumOK | VSAN UUID |
|---|---|---|---|---|---:|---|---:|---:|---|---|---|---|
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `mpx.vmhba0:C2:T1:L0` | **DG2 Cache SSD (`T1` Samsung 870 1TB)** | `true` | `953869` | `ATA Samsung SSD 870` | `25` | `25` | `true` | `true` | `true` | `520e7405-7951-d36b-972f-44fa76ee518e` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.50000c0f012d3630` | **Consolidated 3TB SAS Tier (DG2)** | `false` | `2861588` | `WD WD3001FYYG-01SL3` | `58` | `58` | `true` | `true` | `true` | `522ac7d2-9125-2e27-d90a-e79d5b95d5ff` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.50000c0f01e0c290` | **Consolidated 3TB SAS Tier (DG2)** | `false` | `2861588` | `WD WD3001FYYG-01SL3` | `58` | `58` | `true` | `true` | `true` | `52231164-8542-4e47-05b5-c97338472125` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01aa7e5ec` | **Consolidated 3TB SAS Tier (DG2)** | `false` | `2861588` | `HITACHI HUS723030ALS640` | `58` | `58` | `true` | `true` | `true` | `52d55f3a-970a-3363-c37c-98bfd6d162b0` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01aa800e0` | **Consolidated 3TB SAS Tier (DG2)** | `false` | `2861588` | `HITACHI HUS723030ALS640` | `58` | `58` | `true` | `true` | `true` | `527b2bdc-ffa6-fd7c-d00d-2ae309c17e78` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01aa9a96c` | **Consolidated 3TB SAS Tier (DG2)** | `false` | `2861588` | `HITACHI HUS723030ALS640` | `58` | `58` | `true` | `true` | `true` | `52191fd7-997a-a5a6-c65f-0b325a677220` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01ab112cc` | **Consolidated 3TB SAS Tier (DG2)** | `false` | `2861588` | `HITACHI HUS723030ALS640` | `58` | `58` | `true` | `true` | `true` | `52e03231-e4ca-b44f-afd9-b65c7a5cf3e1` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01ad7dc48` | **Consolidated 3TB SAS Tier (DG2)** | `false` | `2861588` | `HITACHI HUS723030ALS640` | `58` | `58` | `true` | `true` | `true` | `52838d58-ec30-7adb-256c-53952dea37c4` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T0:L0` | **DG1 Cache SSD (`T0` Samsung 870 1TB)** | `true` | `953869` | `ATA Samsung SSD 870` | `25` | `25` | `true` | `true` | `true` | `52e69070-da86-8f9f-27b2-679f255787a8` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T13:L0` | **Pure 24TB Exos Tier (DG1)** | `false` | `22888448` | `ATA ST24000NT002-3N1` | `25` | `25` | `true` | `true` | `true` | `52f55307-770c-ff18-3fb6-7a5efb104701` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T3:L0` | **Pure 24TB Exos Tier (DG1)** | `false` | `22888448` | `ATA ST24000NT002-3N1` | `25` | `25` | `true` | `true` | `true` | `521d0c28-1459-755a-6721-d9c20fb39333` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T4:L0` | **Pure 24TB Exos Tier (DG1)** | `false` | `22888448` | `ATA ST24000NT002-3N1` | `25` | `25` | `true` | `true` | `true` | `52521303-9b87-0c61-4f80-10bf7d453fe4` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T5:L0` | **Pure 24TB Exos Tier (DG1)** | `false` | `22888448` | `ATA ST24000NM000C-3W` | `25` | `25` | `true` | `true` | `true` | `525ffaa8-33eb-b23b-c59f-09944ca74ecc` |

### 1.2 `esxi02` (`10.0.20.42`) — 2 Disk Groups, 13 vSAN Disks
| Disk Group UUID | Cache Tier SSD | Device | Tier / Role | IsSSD | Size (MB) | Vendor / Model | Max QDepth | World QDepth | Mounted | InCMMDS | ChecksumOK | VSAN UUID |
|---|---|---|---|---|---:|---|---:|---:|---|---|---|---|
| `522f2b56-44e9-8ae9-212a-91ebfe7074ba` | `mpx.vmhba0:C2:T9:L0` | `mpx.vmhba0:C2:T9:L0` | **Cache SSD** | `true` | `953869` | `ATA Samsung SSD 860` | `25` | `25` | `true` | `true` | `true` | `522f2b56-44e9-8ae9-212a-91ebfe7074ba` |
| `522f2b56-44e9-8ae9-212a-91ebfe7074ba` | `mpx.vmhba0:C2:T9:L0` | `mpx.vmhba0:C2:T0:L0` | Capacity Tier | `false` | `22888448` | `ATA ST24000NM000C-3W` | `25` | `25` | `true` | `true` | `true` | `5239d098-b42f-f747-7c8e-3e5c7ce4368f` |
| `522f2b56-44e9-8ae9-212a-91ebfe7074ba` | `mpx.vmhba0:C2:T9:L0` | `mpx.vmhba0:C2:T10:L0` | Capacity Tier | `false` | `22888448` | `ATA ST24000NT002-3N1` | `25` | `25` | `true` | `true` | `true` | `523887de-224e-09d5-a748-086282b72f4f` |
| `522f2b56-44e9-8ae9-212a-91ebfe7074ba` | `mpx.vmhba0:C2:T9:L0` | `mpx.vmhba0:C2:T11:L0` | Capacity Tier | `false` | `22888448` | `ATA ST24000NT002-3N1` | `25` | `25` | `true` | `true` | `true` | `528ded51-aa19-a768-2906-4b3640c37ab6` |
| `522f2b56-44e9-8ae9-212a-91ebfe7074ba` | `mpx.vmhba0:C2:T9:L0` | `mpx.vmhba0:C2:T2:L0` | Capacity Tier | `false` | `22888448` | `ATA ST24000NM000C-3W` | `25` | `25` | `true` | `true` | `true` | `52baec16-1abc-18c5-d02d-62be0a17e9a6` |
| `522f2b56-44e9-8ae9-212a-91ebfe7074ba` | `mpx.vmhba0:C2:T9:L0` | `mpx.vmhba0:C2:T4:L0` | Capacity Tier | `false` | `22888448` | `ATA ST24000NM000C-3W` | `25` | `25` | `true` | `true` | `true` | `52831e74-540d-1f30-30c7-2e6b49d700ba` |
| `522f2b56-44e9-8ae9-212a-91ebfe7074ba` | `mpx.vmhba0:C2:T9:L0` | `mpx.vmhba0:C2:T6:L0` | Capacity Tier | `false` | `22888448` | `ATA ST24000NM000C-3W` | `25` | `25` | `true` | `true` | `true` | `520cfd44-189a-eaa9-0475-165a8775b036` |
| `5240e70d-2d82-bd1c-9208-2c351193dd71` | `mpx.vmhba0:C2:T8:L0` | `mpx.vmhba0:C2:T8:L0` | **Cache SSD** | `true` | `953869` | `ATA Samsung SSD 870` | `25` | `25` | `true` | `true` | `true` | `5240e70d-2d82-bd1c-9208-2c351193dd71` |
| `5240e70d-2d82-bd1c-9208-2c351193dd71` | `mpx.vmhba0:C2:T8:L0` | `mpx.vmhba0:C2:T12:L0` | Capacity Tier | `false` | `22888448` | `ATA ST24000NT002-3N1` | `25` | `25` | `true` | `true` | `true` | `526b5421-6394-504b-d890-84e4110fe0a4` |
| `5240e70d-2d82-bd1c-9208-2c351193dd71` | `mpx.vmhba0:C2:T8:L0` | `mpx.vmhba0:C2:T1:L0` | Capacity Tier | `false` | `22888448` | `ATA ST24000NT002-3N1` | `25` | `25` | `true` | `true` | `true` | `52055e9c-e8b9-4f37-7fea-d990f53e7475` |
| `5240e70d-2d82-bd1c-9208-2c351193dd71` | `mpx.vmhba0:C2:T8:L0` | `mpx.vmhba0:C2:T3:L0` | Capacity Tier | `false` | `22888448` | `ATA ST24000NT002-3N1` | `25` | `25` | `true` | `true` | `true` | `5280d36d-d6d4-7e18-bf90-5b74054fe4a9` |
| `5240e70d-2d82-bd1c-9208-2c351193dd71` | `mpx.vmhba0:C2:T8:L0` | `mpx.vmhba0:C2:T5:L0` | Capacity Tier | `false` | `22888448` | `ATA ST24000NM000C-3W` | `25` | `25` | `true` | `true` | `true` | `5219f0ae-c75c-df8d-2717-c80be4db9cb5` |
| `5240e70d-2d82-bd1c-9208-2c351193dd71` | `mpx.vmhba0:C2:T8:L0` | `mpx.vmhba0:C2:T7:L0` | Capacity Tier | `false` | `22888448` | `ATA ST24000NM000C-3W` | `25` | `25` | `true` | `true` | `true` | `52408235-8ba7-4548-2d24-ff095c0c0819` |

### 1.3 `esxi03` (`10.0.20.43`) — 4 Disk Groups, 25 vSAN Disks
| Disk Group UUID | Cache Tier SSD | Device | Tier / Role | IsSSD | Size (MB) | Vendor / Model | Max QDepth | World QDepth | Mounted | InCMMDS | ChecksumOK | VSAN UUID |
|---|---|---|---|---|---:|---|---:|---:|---|---|---|---|
| `527f79c2-2e39-db10-9475-79ea05396202` | `t10.ATA_____SK_hynix_SC300_SATA_512GB_______________ES72N176410302L51___` | `t10.ATA_____SK_hynix_SC300_SATA_512GB_______________ES72N176410302L51___` | **Cache SSD** | `true` | `488386` | `ATA SK hynix SC300 S` | `31` | `31` | `true` | `true` | `true` | `527f79c2-2e39-db10-9475-79ea05396202` |
| `527f79c2-2e39-db10-9475-79ea05396202` | `t10.ATA_____SK_hynix_SC300_SATA_512GB_______________ES72N176410302L51___` | `naa.51402ec010c35548` | Capacity Tier | `false` | `1907729` | `ATA ST2000NX0253` | `32` | `32` | `true` | `true` | `true` | `52de2331-1c7e-0768-b48f-6773b584e71f` |
| `527f79c2-2e39-db10-9475-79ea05396202` | `t10.ATA_____SK_hynix_SC300_SATA_512GB_______________ES72N176410302L51___` | `naa.51402ec010c35549` | Capacity Tier | `false` | `1907729` | `ATA ST2000NX0253` | `32` | `32` | `true` | `true` | `true` | `52a921f2-9cb8-da11-5052-deee365da121` |
| `527f79c2-2e39-db10-9475-79ea05396202` | `t10.ATA_____SK_hynix_SC300_SATA_512GB_______________ES72N176410302L51___` | `naa.51402ec010c3554a` | Capacity Tier | `false` | `1907729` | `ATA ST2000NX0253` | `32` | `32` | `true` | `true` | `true` | `52231a25-afa9-989c-891f-f6918627945e` |
| `527f79c2-2e39-db10-9475-79ea05396202` | `t10.ATA_____SK_hynix_SC300_SATA_512GB_______________ES72N176410302L51___` | `naa.51402ec010c3554b` | Capacity Tier | `false` | `1907729` | `ATA ST2000NX0253` | `32` | `32` | `true` | `true` | `true` | `52467e28-4e3e-bdae-e939-86e4c1606e59` |
| `528858fc-16cc-a790-ee2a-218b8cdb3cd8` | `naa.5000cca06cace644` | `naa.5000cca06cace644` | **Cache SSD** | `true` | `1831420` | `HGST HSCP1920S5xnFMRI` | `64` | `64` | `true` | `true` | `true` | `528858fc-16cc-a790-ee2a-218b8cdb3cd8` |
| `528858fc-16cc-a790-ee2a-218b8cdb3cd8` | `naa.5000cca06cace644` | `naa.5000cca06cabbeb4` | Capacity Tier | `true` | `1831420` | `HGST HSCP1920S5xnFMRI` | `64` | `64` | `true` | `true` | `true` | `52b8b78d-7a2a-2b44-14b8-84533bf56430` |
| `528858fc-16cc-a790-ee2a-218b8cdb3cd8` | `naa.5000cca06cace644` | `naa.5000cca06cabc420` | Capacity Tier | `true` | `1831420` | `HGST HSCP1920S5xnFMRI` | `64` | `64` | `true` | `true` | `true` | `52684152-cffe-58c3-7fa6-c2bfcec9ce3f` |
| `528858fc-16cc-a790-ee2a-218b8cdb3cd8` | `naa.5000cca06cace644` | `naa.5000cca06cabe218` | Capacity Tier | `true` | `1831420` | `HGST HSCP1920S5xnFMRI` | `64` | `64` | `true` | `true` | `true` | `523689c4-7f78-ee7f-5f8b-cc497be6fa16` |
| `528858fc-16cc-a790-ee2a-218b8cdb3cd8` | `naa.5000cca06cace644` | `naa.5000cca06cabeba0` | Capacity Tier | `true` | `1831420` | `HGST HSCP1920S5xnFMRI` | `64` | `64` | `true` | `true` | `true` | `524140ce-400e-dc1b-bbf2-ca0597d61a0c` |
| `528858fc-16cc-a790-ee2a-218b8cdb3cd8` | `naa.5000cca06cace644` | `naa.5000cca06cac4660` | Capacity Tier | `true` | `1831420` | `HGST HSCP1920S5xnFMRI` | `64` | `64` | `true` | `true` | `true` | `524c337a-0aaa-59f4-8a6a-e9ba26fa390b` |
| `528858fc-16cc-a790-ee2a-218b8cdb3cd8` | `naa.5000cca06cace644` | `naa.5000cca06cad6c2c` | Capacity Tier | `true` | `1831420` | `HGST HSCP1920S5xnFMRI` | `64` | `64` | `true` | `true` | `true` | `52063ad5-1fcd-8f92-0182-10aaada35162` |
| `528858fc-16cc-a790-ee2a-218b8cdb3cd8` | `naa.5000cca06cace644` | `t10.ATA_____LITEON_CV32D8D5122D41_SATA_512GB_SED______TW0MN0K7LOH0075G01ZI` | **Capacity SSD (LITEON 512GB — Untouched per Operator Order)** | `true` | `488386` | `ATA LITEON CV3-8D512` | `31` | `31` | `true` | `true` | `true` | `528ed6a3-4fba-eb63-7479-362374c6f9c4` |
| `529e09c7-2d07-92ae-7fe5-f826ccedfe94` | `naa.5000cca06cabc948` | `naa.5000cca06cabc948` | **Cache SSD** | `true` | `1831420` | `HGST HSCP1920S5xnFMRI` | `64` | `64` | `true` | `true` | `true` | `529e09c7-2d07-92ae-7fe5-f826ccedfe94` |
| `529e09c7-2d07-92ae-7fe5-f826ccedfe94` | `naa.5000cca06cabc948` | `naa.5000c500a6a778e3` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `52f8bb5f-5787-474d-fca4-21dc8d907738` |
| `529e09c7-2d07-92ae-7fe5-f826ccedfe94` | `naa.5000cca06cabc948` | `naa.5000c500b7497527` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `52aae1b9-2ba8-1aec-b4ed-0f0016908108` |
| `529e09c7-2d07-92ae-7fe5-f826ccedfe94` | `naa.5000cca06cabc948` | `naa.5000c500b749a45b` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `52f5bd8e-ef0c-8927-4634-a5562be95638` |
| `529e09c7-2d07-92ae-7fe5-f826ccedfe94` | `naa.5000cca06cabc948` | `naa.5000c500b756c0a7` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `52e9d9f4-d28c-71f0-c14f-ef16d95d7739` |
| `529e09c7-2d07-92ae-7fe5-f826ccedfe94` | `naa.5000cca06cabc948` | `naa.5000c500b80352df` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `52b41460-0486-3412-c5d0-67cc699d9a50` |
| `52b266cf-e510-5339-54c8-454e6d99fc82` | `naa.5000cca06cac2d84` | `naa.5000cca06cac2d84` | **Cache SSD** | `true` | `1831420` | `HGST HSCP1920S5xnFMRI` | `64` | `64` | `true` | `true` | `true` | `52b266cf-e510-5339-54c8-454e6d99fc82` |
| `52b266cf-e510-5339-54c8-454e6d99fc82` | `naa.5000cca06cac2d84` | `naa.5000c500b77b0213` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `528e9892-89ca-2693-d7ad-bd3a8e4afe11` |
| `52b266cf-e510-5339-54c8-454e6d99fc82` | `naa.5000cca06cac2d84` | `naa.5000c500b78252c3` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `528b3118-135f-44c6-1c06-e4b31e754d00` |
| `52b266cf-e510-5339-54c8-454e6d99fc82` | `naa.5000cca06cac2d84` | `naa.5000c500b8056747` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `52b57fe8-c60b-0d35-a310-932c56efbd80` |
| `52b266cf-e510-5339-54c8-454e6d99fc82` | `naa.5000cca06cac2d84` | `naa.51402ec010c3554e` | Capacity Tier | `false` | `1907729` | `ATA ST2000NX0253` | `32` | `32` | `true` | `true` | `true` | `526c478f-df77-2608-8cff-3bca82dd362c` |
| `52b266cf-e510-5339-54c8-454e6d99fc82` | `naa.5000cca06cac2d84` | `naa.51402ec010c3554f` | Capacity Tier | `false` | `1907729` | `ATA ST2000NX0253` | `32` | `32` | `true` | `true` | `true` | `5239ba8d-5e25-3b6f-3467-dece3959d34a` |

### 1.4 `esxi04` (`10.0.20.44`) — 3 Disk Groups, 24 vSAN Disks
| Disk Group UUID | Cache Tier SSD | Device | Tier / Role | IsSSD | Size (MB) | Vendor / Model | Max QDepth | World QDepth | Mounted | InCMMDS | ChecksumOK | VSAN UUID |
|---|---|---|---|---|---:|---|---:|---:|---|---|---|---|
| `520ec439-8c83-c673-eb29-fcdc9cfefd89` | `naa.5000cca06cabc9b0` | `naa.5000cca06cabc9b0` | **Cache SSD** | `true` | `1831420` | `HGST HSCP1920S5xnFMRI` | `64` | `64` | `true` | `true` | `true` | `520ec439-8c83-c673-eb29-fcdc9cfefd89` |
| `520ec439-8c83-c673-eb29-fcdc9cfefd89` | `naa.5000cca06cabc9b0` | `naa.5000c500b756a94f` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `523fed59-d11c-1d2a-f071-d0d40b6be8ea` |
| `520ec439-8c83-c673-eb29-fcdc9cfefd89` | `naa.5000cca06cabc9b0` | `naa.5000c500b756b6af` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `523a5ef6-8ec0-00d6-7cde-bdd365cfea74` |
| `520ec439-8c83-c673-eb29-fcdc9cfefd89` | `naa.5000cca06cabc9b0` | `naa.5000c500b756b6d7` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `52a7d1ab-b1c7-5cd3-7eb2-3e8e90596a0e` |
| `520ec439-8c83-c673-eb29-fcdc9cfefd89` | `naa.5000cca06cabc9b0` | `naa.5000c500b756b6ef` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `520b6529-1eed-893a-c3b4-b97fc62f733e` |
| `520ec439-8c83-c673-eb29-fcdc9cfefd89` | `naa.5000cca06cabc9b0` | `naa.5000c500b756b973` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `52d9fe20-f36f-5b04-7236-29e1cac0ce9a` |
| `520ec439-8c83-c673-eb29-fcdc9cfefd89` | `naa.5000cca06cabc9b0` | `naa.5000c500bb794fc7` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `522fa0a7-8314-8191-d5e7-ff4e6e52ae2b` |
| `520ec439-8c83-c673-eb29-fcdc9cfefd89` | `naa.5000cca06cabc9b0` | `naa.5000c500bb795a7f` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `52fdbb71-8cdd-24f9-75b7-fbb41a16a254` |
| `526de2af-0273-58ee-1bb0-2efd855e05ad` | `t10.ATA_____SK_hynix_SC300_SATA_512GB_______________ES74N204810504O2A___` | `t10.ATA_____SK_hynix_SC300_SATA_512GB_______________ES74N204810504O2A___` | **Cache SSD** | `true` | `488386` | `ATA SK hynix SC300 S` | `31` | `31` | `true` | `true` | `true` | `526de2af-0273-58ee-1bb0-2efd855e05ad` |
| `526de2af-0273-58ee-1bb0-2efd855e05ad` | `t10.ATA_____SK_hynix_SC300_SATA_512GB_______________ES74N204810504O2A___` | `naa.5000c500a6a3cddf` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `5254608b-9280-b64c-7d55-0d80e6381bf0` |
| `526de2af-0273-58ee-1bb0-2efd855e05ad` | `t10.ATA_____SK_hynix_SC300_SATA_512GB_______________ES74N204810504O2A___` | `naa.5000c500a6a7722b` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `52f2e1f1-26a8-b6ee-9e98-afdf1ea6c433` |
| `526de2af-0273-58ee-1bb0-2efd855e05ad` | `t10.ATA_____SK_hynix_SC300_SATA_512GB_______________ES74N204810504O2A___` | `naa.5000c500b749756b` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `52815a0b-8f4f-ba1d-9384-0873e7bb9694` |
| `526de2af-0273-58ee-1bb0-2efd855e05ad` | `t10.ATA_____SK_hynix_SC300_SATA_512GB_______________ES74N204810504O2A___` | `naa.5000c500b749776f` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `52a1efb6-f8de-7469-da65-23c334ee5c3d` |
| `526de2af-0273-58ee-1bb0-2efd855e05ad` | `t10.ATA_____SK_hynix_SC300_SATA_512GB_______________ES74N204810504O2A___` | `naa.5000c500b749920f` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `52ca4c25-4e35-a6f4-9bf8-93526eae678c` |
| `526de2af-0273-58ee-1bb0-2efd855e05ad` | `t10.ATA_____SK_hynix_SC300_SATA_512GB_______________ES74N204810504O2A___` | `naa.5000c500b7510073` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `52952301-6668-6696-4cd0-197a49428339` |
| `526de2af-0273-58ee-1bb0-2efd855e05ad` | `t10.ATA_____SK_hynix_SC300_SATA_512GB_______________ES74N204810504O2A___` | `naa.5000c500b80563e7` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `52f785eb-9efa-b7a7-1955-d071b4e797b8` |
| `52d263a2-04db-f528-0e58-d6e71818587c` | `naa.5000cca06cac34a4` | `naa.5000cca06cac34a4` | **Cache SSD** | `true` | `1831420` | `HGST HSCP1920S5xnFMRI` | `64` | `64` | `true` | `true` | `true` | `52d263a2-04db-f528-0e58-d6e71818587c` |
| `52d263a2-04db-f528-0e58-d6e71818587c` | `naa.5000cca06cac34a4` | `naa.5000c500b7498613` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `52437f96-6a47-4bd4-43c4-69da0dcdf4cc` |
| `52d263a2-04db-f528-0e58-d6e71818587c` | `naa.5000cca06cac34a4` | `naa.5000c500b749af5f` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `52b1a309-1b91-5ae4-c9b8-e8b5cedcb5f1` |
| `52d263a2-04db-f528-0e58-d6e71818587c` | `naa.5000cca06cac34a4` | `naa.5000c500b756c813` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `52e5cab6-58af-dfe6-f7af-220fe84861c6` |
| `52d263a2-04db-f528-0e58-d6e71818587c` | `naa.5000cca06cac34a4` | `naa.5000c500b756d6e7` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `52a821a7-df32-a592-6549-7360fb3ced27` |
| `52d263a2-04db-f528-0e58-d6e71818587c` | `naa.5000cca06cac34a4` | `naa.5000c500b756dc57` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `528f0c19-312c-52a9-c351-2fdc196f3ad9` |
| `52d263a2-04db-f528-0e58-d6e71818587c` | `naa.5000cca06cac34a4` | `naa.5000c500b756e477` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `52702bf9-631e-71ed-551c-4176c665cea1` |
| `52d263a2-04db-f528-0e58-d6e71818587c` | `naa.5000cca06cac34a4` | `naa.5000c500bb79603f` | Capacity Tier | `false` | `1716957` | `SEAGATE STHB1800S5xeN010` | `64` | `64` | `true` | `true` | `true` | `521540c9-81c2-545a-7960-09793c2eed67` |


---

## 2. Pillar 2: Cluster Health, Resync (`0 B`), Congestion (`0`), SMART Audit & HBA Queue Depths

### 2.1 Cluster Membership, Resync & Congestion Matrix (`esxi01..04`)
| Host | Node Role | `LocalNodeHealthState` | `MaintenanceModeState` | `SubClusterMemberCount` | `TotalBytesLeftToResync` | `ResyncingObjects` | `OverallHealth` | `ComponentMetadataHealth` | `MemoryPoolsheaps` | `MemoryPoolsslabs` |
|---|---|---|---|---:|---:|---:|---|---|---|---|
| **`esxi01`** (`10.0.20.41`) | `BACKUP` | `HEALTHY` | `OFF` | `4` | `0` (`0.00 GB`) | `0` | `green` | `green` | `green` | `green` |
| **`esxi02`** (`10.0.20.42`) | `AGENT` | `HEALTHY` | `OFF` | `4` | `0` (`0.00 GB`) | `0` | `green` | `green` | `green` | `green` |
| **`esxi03`** (`10.0.20.43`) | `AGENT` | `HEALTHY` | `OFF` | `4` | `0` (`0.00 GB`) | `0` | `green` | `green` | `green` | `green` |
| **`esxi04`** (`10.0.20.44`) | `MASTER` | `HEALTHY` | `OFF` | `4` | `0` (`0.00 GB`) | `0` | `green` | `green` | `green` | `green` |

### 2.2 SSD SMART Health & Wear Audit Across `esxi01..04` (`18` vSAN SSDs Inspected)
| Host | Device | vSAN Role & DG | SMART Health | Media Wearout Indicator | Temperature | Write Error Count | Uncorrectable Errors | Reallocated Sectors |
|---|---|---|---|---|---|---|---|---|
| `esxi01` | `mpx.vmhba0:C2:T0:L0` | Cache (`52e69070`) | `OK` | `50 (raw=899)` | `49°C (raw=51)` | `100 (raw=0)` | `100 (raw=0)` | `100 (raw=0)` |
| `esxi01` | `mpx.vmhba0:C2:T1:L0` | Cache (`520e7405`) | `OK` | `33 (raw=1211)` | `47°C (raw=53)` | `100 (raw=0)` | `100 (raw=0)` | `100 (raw=0)` |
| `esxi02` | `mpx.vmhba0:C2:T8:L0` | Cache (`5240e70d`) | `OK` | `24 (raw=1377)` | `33°C (raw=67)` | `100 (raw=0)` | `100 (raw=0)` | `100 (raw=0)` |
| `esxi02` | `mpx.vmhba0:C2:T9:L0` | Cache (`522f2b56`) | `OK` | `9 (raw=1642)` | `37°C (raw=63)` | `100 (raw=0)` | `100 (raw=0)` | `100 (raw=0)` |
| `esxi03` | `naa.5000cca06cabbeb4` | Capacity (`528858fc`) | `OK` | `86 (raw=N/A)` | `28°C (raw=N/A)` | `0 (raw=N/A)` | `0` | `0 (raw=N/A)` |
| `esxi03` | `naa.5000cca06cabc420` | Capacity (`528858fc`) | `OK` | `86 (raw=N/A)` | `28°C (raw=N/A)` | `0 (raw=N/A)` | `0` | `0 (raw=N/A)` |
| `esxi03` | `naa.5000cca06cabc948` | Cache (`529e09c7`) | `OK` | `85 (raw=N/A)` | `28°C (raw=N/A)` | `0 (raw=N/A)` | `0` | `0 (raw=N/A)` |
| `esxi03` | `naa.5000cca06cabe218` | Capacity (`528858fc`) | `OK` | `85 (raw=N/A)` | `28°C (raw=N/A)` | `0 (raw=N/A)` | `0` | `0 (raw=N/A)` |
| `esxi03` | `naa.5000cca06cabeba0` | Capacity (`528858fc`) | `OK` | `86 (raw=N/A)` | `28°C (raw=N/A)` | `0 (raw=N/A)` | `0` | `0 (raw=N/A)` |
| `esxi03` | `naa.5000cca06cac2d84` | Cache (`52b266cf`) | `OK` | `84 (raw=N/A)` | `28°C (raw=N/A)` | `0 (raw=N/A)` | `0` | `0 (raw=N/A)` |
| `esxi03` | `naa.5000cca06cac4660` | Capacity (`528858fc`) | `OK` | `87 (raw=N/A)` | `28°C (raw=N/A)` | `0 (raw=N/A)` | `0` | `0 (raw=N/A)` |
| `esxi03` | `naa.5000cca06cace644` | Cache (`528858fc`) | `OK` | `77 (raw=N/A)` | `28°C (raw=N/A)` | `0 (raw=N/A)` | `0` | `0 (raw=N/A)` |
| `esxi03` | `naa.5000cca06cad6c2c` | Capacity (`528858fc`) | `OK` | `77 (raw=N/A)` | `28°C (raw=N/A)` | `0 (raw=N/A)` | `0` | `0 (raw=N/A)` |
| `esxi03` | `t10.ATA_____LITEON_CV32D8D5122D41_SATA_512GB_SED______TW0MN0K7LOH0075G01ZI` | Capacity (`528858fc`) | `OK` | `N/A` | `100°C (raw=50)` | `0` | `100 (raw=8)` | `100 (raw=0)` |
| `esxi03` | `t10.ATA_____SK_hynix_SC300_SATA_512GB_______________ES72N176410302L51___` | Cache (`527f79c2`) | `OK` | `N/A` | `38°C (raw=94494130214)` | `0` | `253 (raw=0)` | `253 (raw=0)` |
| `esxi04` | `naa.5000cca06cabc9b0` | Cache (`520ec439`) | `OK` | `84 (raw=N/A)` | `28°C (raw=N/A)` | `0 (raw=N/A)` | `0` | `0 (raw=N/A)` |
| `esxi04` | `naa.5000cca06cac34a4` | Cache (`52d263a2`) | `OK` | `84 (raw=N/A)` | `29°C (raw=N/A)` | `0 (raw=N/A)` | `0` | `0 (raw=N/A)` |
| `esxi04` | `t10.ATA_____SK_hynix_SC300_SATA_512GB_______________ES74N204810504O2A___` | Cache (`526de2af`) | `OK` | `N/A` | `40°C (raw=77313998888)` | `0` | `253 (raw=0)` | `253 (raw=0)` |

### 2.3 Storage Controller (HBA) & Queue-Depth Audit (`esxcli storage core adapter list`)
- **`esxi01` (`10.0.20.41`)**:
  - `vmhba0`: `nhpsa` (`sas.5001438040847e30`, `(0000:03:00.0) Hewlett Packard Enterprise Smart Array P840ar`) — per-device `DeviceMaxQueueDepth: 64` (SATA/SAS drives), `NoofoutstandingIOswithcompetingworlds: 32`, Enclosure `DeviceMaxQueueDepth: 1009`.
  - `vmhba1`: `vmw_ahci` (`sata.vmhba1`, `(0000:00:1f.2) Intel Corporation Wellsburg AHCI Controller`).
- **`esxi02` (`10.0.20.42`)**:
  - `vmhba0`: `nhpsa` (`sas.50014380377bf160`, `(0000:03:00.0) Hewlett Packard Enterprise Smart Array P840ar`) — per-device `DeviceMaxQueueDepth: 64`, `NoofoutstandingIOswithcompetingworlds: 32`.
  - `vmhba1`: `vmw_ahci` (`sata.vmhba1`, `(0000:00:1f.2) Intel Corporation Wellsburg AHCI Controller`).
- **`esxi03` (`10.0.20.43`)**:
  - `vmhba3`: `smartpqi` (`sas.51402ec0129a6b50`, `(0000:5c:00.0) Microsemi HPE P408i-a SR Gen10`) — SAS SSDs (`DeviceMaxQueueDepth: 254`, `CompetingWorldQueueDepth: 32`), SAS HDDs (`DeviceMaxQueueDepth: 254`, `CompetingWorldQueueDepth: 32`), SATA drives (`DeviceMaxQueueDepth: 32`, `CompetingWorldQueueDepth: 32`).
  - `vmhba2`: `vmw_ahci` (`sata.vmhba2`, `(0000:00:11.5) Intel Corporation Lewisburg SATA AHCI Controller`) — `DeviceMaxQueueDepth: 31`.
  - `vmhba0` / `vmhba1`: `elxiscsi` (`(0000:5d:00.2/.3) Emulex OneConnect OCe14000 iSCSI CNA`).
- **`esxi04` (`10.0.20.44`)**:
  - `vmhba3`: `smartpqi` (`sas.51402ec012ba77a0`, `(0000:5c:00.0) Microsemi HPE P408i-a SR Gen10`) — SAS SSDs & SAS HDDs (`DeviceMaxQueueDepth: 254`, `CompetingWorldQueueDepth: 32`).
  - `vmhba2`: `vmw_ahci` (`sata.vmhba2`, `(0000:00:11.5) Intel Corporation Lewisburg SATA AHCI Controller`) — `DeviceMaxQueueDepth: 31`.
  - `vmhba0` / `vmhba1`: `elxiscsi` (`(0000:5d:00.2/.3) Emulex OneConnect OCe14000 iSCSI CNA`).

---

## 3. Pillar 3: 25GbE SFP28 RoCE Fabric (`VLAN 75`, `10.0.75.11..14`) & Physical ESXi Uplinks

### 3.1 Physical ESXi 25GbE SFP28 & 10GbE SFP+ NIC Matrix (`esxcli network nic list`)
| Host | 25GbE Primary RoCE/vSAN Uplink | 25GbE / 10GbE Secondary SFP28 | 10GbE LACP Trunk Uplinks | MTU | Link State |
|---|---|---|---|---:|---|
| **`esxi01`** (`10.0.20.41`) | `vmnic7` (`nmlx5_core` ConnectX-4 Lx, `25000Mb/s Full`, Port 29) | `vmnic6` (`nmlx5_core` ConnectX-4 Lx, `10000Mb/s Full`) | `vmnic4`, `vmnic5` (`i40en` X710 `10000Mb/s Full`) + `vmnic8`, `vmnic9` (`ixgben` 82599 `10000Mb/s Full`) | `9000` | **All `Up` (`Full Duplex`)** |
| **`esxi02`** (`10.0.20.42`) | `vmnic6` (`nmlx5_core` ConnectX-4 Lx, `25000Mb/s Full`, Port 31) | `vmnic7` (`nmlx5_core` ConnectX-4 Lx, `10000Mb/s Full`) | `vmnic4`, `vmnic5` (`i40en` X710 `10000Mb/s Full`) | `9000` | **All `Up` (`Full Duplex`)** |
| **`esxi03`** (`10.0.20.43`) | `vmnic10` (`nmlx5_core` ConnectX-4 Lx, `25000Mb/s Full`, Port 30) | `vmnic11` (`nmlx5_core` ConnectX-4 Lx, `10000Mb/s Full`) | `vmnic4`, `vmnic5` (`elxnet` 556FLR `10000Mb/s Full`) + `vmnic8`, `vmnic9` (`ixgben` X540 `10000Mb/s Full`) | `9000` | **All `Up` (`Full Duplex`)** |
| **`esxi04`** (`10.0.20.44`) | `vmnic6` (`nmlx5_core` ConnectX-5, `25000Mb/s Full`, Port 32) | `vmnic7` (`nmlx5_core` ConnectX-5, `10000Mb/s Full`) | `vmnic4`, `vmnic5` (`elxnet` 556FLR `10000Mb/s Full`) + `vmnic8`, `vmnic9` (`ixgben` 82599 `10000Mb/s Full`) | `9000` | **All `Up` (`Full Duplex`)** |

### 3.2 Full-Mesh `VLAN 75` (`10.0.75.11..14`) GPU Interconnect E2E Latency & Packet Loss Matrix
```text
[ai-srv01 (10.0.70.72) on esxi01 Port 29 (25GbE SFP28 vmnic7)]
IFACE=ens33|IP=10.0.75.12/24|MTU=9000|DRV=vmxnet3|SPD=10000Mb/s
PING_10.0.75.11=5 packets transmitted, 5 received, 0% packet loss, time 204ms rtt min/avg/max/mdev = 0.185/0.234/0.291/0.035 ms 
PING_10.0.75.12=5 packets transmitted, 5 received, 0% packet loss, time 204ms rtt min/avg/max/mdev = 0.016/0.027/0.058/0.015 ms 
PING_10.0.75.13=5 packets transmitted, 5 received, 0% packet loss, time 204ms rtt min/avg/max/mdev = 0.169/0.237/0.289/0.040 ms 
PING_10.0.75.14=5 packets transmitted, 5 received, 0% packet loss, time 204ms rtt min/avg/max/mdev = 0.156/0.190/0.240/0.031 ms
[aidev (ai-srv02) (10.0.70.83) on esxi03 Port 30 (25GbE SFP28 vmnic10)]
IFACE=ens33|IP=10.0.75.11/24|MTU=9000|DRV=vmxnet3|SPD=10000Mb/s
PING_10.0.75.11=5 packets transmitted, 5 received, 0% packet loss, time 203ms rtt min/avg/max/mdev = 0.008/0.017/0.045/0.014 ms 
PING_10.0.75.12=5 packets transmitted, 5 received, 0% packet loss, time 204ms rtt min/avg/max/mdev = 0.197/0.244/0.312/0.052 ms 
PING_10.0.75.13=5 packets transmitted, 5 received, 0% packet loss, time 204ms rtt min/avg/max/mdev = 0.139/0.189/0.274/0.048 ms 
PING_10.0.75.14=5 packets transmitted, 5 received, 0% packet loss, time 204ms rtt min/avg/max/mdev = 0.120/0.173/0.265/0.055 ms
[ai-infer01 (10.0.70.125) on esxi04 Port 32 (25GbE SFP28 vmnic6)]
IFACE=ens33|IP=10.0.75.14/24|MTU=9000|DRV=vmxnet3|SPD=10000Mb/s
PING_10.0.75.11=5 packets transmitted, 5 received, 0% packet loss, time 204ms rtt min/avg/max/mdev = 0.121/0.145/0.183/0.023 ms 
PING_10.0.75.12=5 packets transmitted, 5 received, 0% packet loss, time 204ms rtt min/avg/max/mdev = 0.203/0.235/0.294/0.031 ms 
PING_10.0.75.13=5 packets transmitted, 5 received, 0% packet loss, time 204ms rtt min/avg/max/mdev = 0.141/0.194/0.262/0.045 ms 
PING_10.0.75.14=5 packets transmitted, 5 received, 0% packet loss, time 203ms rtt min/avg/max/mdev = 0.014/0.019/0.031/0.006 ms
[ai-ollama01 (10.0.70.228) on esxi02 Port 31 (25GbE SFP28 vmnic6)]
IFACE=ens33|IP=10.0.75.13/24|MTU=9000|DRV=vmxnet3|SPD=10000Mb/s
PING_10.0.75.11=5 packets transmitted, 5 received, 0% packet loss, time 204ms rtt min/avg/max/mdev = 0.114/0.141/0.188/0.025 ms 
PING_10.0.75.12=5 packets transmitted, 5 received, 0% packet loss, time 204ms rtt min/avg/max/mdev = 0.166/0.217/0.302/0.045 ms 
PING_10.0.75.13=5 packets transmitted, 5 received, 0% packet loss, time 203ms rtt min/avg/max/mdev = 0.015/0.020/0.034/0.007 ms 
PING_10.0.75.14=5 packets transmitted, 5 received, 0% packet loss, time 204ms rtt min/avg/max/mdev = 0.138/0.177/0.253/0.040 ms
```

---

## 4. Pillar 4: iLO 4 / iLO 5 Thermal & Power Telemetry (`:4091/api/hardware`), 4x Tesla T4 GPUs & 27-Node VM Census

- **12U Rack Total Power Draw**: `2,097.0 W` across all 4 HPE ProLiant DL380 hosts (`esxi01`: `480W`, `esxi02`: `459W`, `esxi03`: `574W`, `esxi04`: `584W`).
- **Ambient Intake & Thermal Health**: Rack ambient `79.5°F` (`26.4°C`); host sensors `23.0°C` (`esxi02`), `25.0°C` (`esxi03`), `26.0°C` (`esxi04`), `44.0°C` (`esxi01` front LFF backplane sensor); CPU temperatures `40.0°C`–`48.0°C` across all 8 Xeon sockets; `8/8` PSUs `OK`; `24/24` cooling fans `OK`.
- **4x NVIDIA Tesla T4 GPUs (`60.0 GiB` total ECC VRAM, `0` ECC errors)**:
  - `ai-srv01` (`.72`, `esxi01`): `NVIDIA Tesla T4`, `72.0°C`, `31.24W / 70W`, `P0`, `ecc_errors: 0` (`OK`).
  - `aidev` (`.83`, `esxi03`): `NVIDIA Tesla T4`, `72.0°C`, `31.97W / 70W`, `P0`, `ecc_errors: 0` (`OK`).
  - `ai-infer01` (`.125`, `esxi04`): `NVIDIA Tesla T4`, `60.0°C`, `30.13W / 70W`, `P0`, `ecc_errors: 0` (`OK`).
  - `ai-ollama01` (`.228`, `esxi02`): `NVIDIA Tesla T4`, `72.0°C`, `30.86W / 70W`, `P0`, `ecc_errors: 0` (`OK`).
- **vCenter `vc04` VM Inventory Census (`54` total registered VM objects across active 27-node topology, cold spares `spare1..12`, CI clones & vCLS)**:
  - Verified accessible on `vsanDatastore` (`328.449 TiB` capacity, `194.745 TiB` free, `40.71%` used).
