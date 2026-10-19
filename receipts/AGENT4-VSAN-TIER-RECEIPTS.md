# AGENT-4: vSAN Tier & Disk Group Evacuation Architecture Receipts

- **Generated At (UTC)**: `2026-10-17T17:22:30Z`
- **Agent**: Agent-4 (vSAN Tier & Disk Group Evacuation Architect)
- **Verification Standard**: Zero-hallucination raw `esxcli`, `partedUtil`, `vdq`, `ssacli`, `vsish`, and HPE iLO5 Redfish outputs with explicit exit codes.
- **Operator Directive & Swarm Coordination**:
  - **Operator Directive (`2026-10-17T16:59:29Z` — `"The ssd stays"`)**: `esxi03` All-Flash Disk Group `DG 528858fc-16cc-a790-ee2a-218b8cdb3cd8` (`1x 1.92TB HGST SAS SSD` cache + `5x 1.92TB HGST SAS SSD` capacity + `1x 512GB LiteOn SATA SSD` capacity) is preserved **untouched** in vSAN (`IsMounted=true`, `InCMMDS=true`).
  - **Active `esxi01` Surgical Re-Grouping Coordination (Agent-6)**: Agent-6 (`agent6_surgical_regroup.py`) is actively executing the software-only per-disk capacity re-grouping on `esxi01` (`Step A`: evacuating `mpx.vmhba0:C2:T4:L0` `UUID 52a3d07a-8c93-7284-b565-f4368d4e2ee8` locally `To Host: ESXI01`). No competing Disk Group evacuation was triggered on `esxi03` so that `esxi01` re-grouping completes with zero write spill onto `esxi02`'s worn cache SSDs (`T9` at `10%` life left, `T8` at `24%` life left).
  - **Safe Phase 2 Execution Completed on `esxi03` (`naa.5000cca06cac4660`)**: Forensic verification proved `esxi03`'s unused 9th `1.92TB HGST Enterprise 12G SAS SSD` (`naa.5000cca06cac4660`, Physical Bay `1I:1:1`, `Media Wearout Indicator = 87`) contained a stale, unreferenced ESXi 7.0 partition table (`OSDATA-690a9a47-925deb90-a3c3-98b78522598c`, `BOOTBANK1` `b1a550e1...`, `BOOTBANK2` `584f42d6...`) while `naa.5000c500b7598017` (Bay `1I:1:8`) is the active boot/OSDATA/datastore1 device. We cleanly unmounted the stale `OSDATA` volume and deleted partitions `1, 5, 6, 7` via `partedUtil`, transitioning `naa.5000cca06cac4660` (`1,831,420 MB`) to **`"State": "Eligible for use by VSAN"`, `"IsSSD": "1"`, `"IsCapacityFlash": "0"`** (`rc=0`).

---

## 1. Forensic Pre-Flight of `esxi01`, `esxi02`, `esxi03`, and `esxi04` vSAN Storage

### 1.1 Pre-Flight Cluster Resync Check (`esxi01` / `esxi03`)
Note: On ESXi 8.0.3 (`build 24022510`), the CLI namespace is `esxcli vsan debug resync summary get` and `esxcli vsan debug resync list` (`esxcli vsan resync list` returns `EXIT_CODE: 1`, `Error: Unknown command or namespace vsan resync list`).

- **Baseline at `2026-10-17T17:05:30Z` (prior to `esxi01` `T4` per-disk evacuation)**:
```text
CMD: esxcli vsan debug resync summary get
EXIT_CODE: 0
STDOUT:
   Total Number Of Resyncing Objects: 0
   Total Bytes Left To Resync: 0
   Total GB Left To Resync: 0.00
```

- **Live Status During `esxi01` Surgical Re-Grouping (`2026-10-17T17:21:00Z`, `mpx.vmhba0:C2:T4:L0` `52a3d07a-8c93-7284-b565-f4368d4e2ee8` evacuating locally `To Host: ESXI01`)**:
```text
CMD: esxcli vsan debug resync summary get; esxcli vsan debug resync list
EXIT_CODE: 0
STDOUT:
   Total Number Of Resyncing Objects: 4
   Total Bytes Left To Resync: 656114524160
   Total GB Left To Resync: 611.05
Group UUID                            Object UUID                           Component UUID                        To Host  GB Left To Resync  Intent
------------------------------------  ------------------------------------  ------------------------------------  -------  -----------------  ------
43b3606a-3c1d-2bd2-c2e2-98b785225eea  f6807e6a-a0c1-de19-ea38-98b78522598c  d6abd36a-1d76-25cd-61fc-48df37d56620  ESXI01   135.42             Decommissioning
43b3606a-3c1d-2bd2-c2e2-98b785225eea  f6807e6a-a0c1-de19-ea38-98b78522598c  * (all 1 components)                  --       135.42             --
43b3606a-3c1d-2bd2-c2e2-98b785225eea  * (all 1 objects)                     * (all 1 components)                  --       135.42             --
67d3916a-90f7-0b95-be6b-98b78522551a  68d3916a-2e20-1321-420d-98b78522551a  d9abd36a-5474-ea50-deb0-48df37e0b790  ESXI01   84.43              Decommissioning
67d3916a-90f7-0b95-be6b-98b78522551a  68d3916a-2e20-1321-420d-98b78522551a  * (all 1 components)                  --       84.43              --
67d3916a-90f7-0b95-be6b-98b78522551a  * (all 1 objects)                     * (all 1 components)                  --       84.43              --
0776746a-7604-ef25-e6b2-48df37e0b790  0776746a-a2ab-9089-1a91-48df37e0b790  c7abd36a-e461-badf-d3fe-98b785225eea  ESXI01   224.54             Decommissioning
0776746a-7604-ef25-e6b2-48df37e0b790  0776746a-a2ab-9089-1a91-48df37e0b790  * (all 1 components)                  --       224.54             --
0776746a-7604-ef25-e6b2-48df37e0b790  * (all 1 objects)                     * (all 1 components)                  --       224.54             --
87d3916a-fe53-ffe1-9de9-98b78522551a  88d3916a-b225-f5c3-f389-98b78522551a  ceabd36a-90e9-ee01-5124-48df37d56620  ESXI01   80.86              Decommissioning
87d3916a-fe53-ffe1-9de9-98b78522551a  88d3916a-b225-f5c3-f389-98b78522551a  ceabd36a-6027-f401-656e-48df37d56620  ESXI01   83.99              Decommissioning
87d3916a-fe53-ffe1-9de9-98b78522551a  88d3916a-b225-f5c3-f389-98b78522551a  * (all 2 components)                  --       164.85             --
87d3916a-fe53-ffe1-9de9-98b78522551a  * (all 1 objects)                     * (all 2 components)                  --       164.85             --
```

---

### 1.2 Complete vSAN Disk Group & Capacity Inventory (`esxcli vsan debug disk list`, `EXIT_CODE: 0`)

> **Crucial vSAN OSA Discovery**: Even though `esxi01` and `esxi02` have 24TB Seagate Exos physical drives (`ST24000NT002-3N1` / `ST24000NM000C-3W`, `22,888,448 MB` = `21.83 TiB` raw), vSAN OSA (`Version: 20`) formats and caps each individual capacity disk at **`16,383.99 GiB` (`16.00 TiB` / `17.59 TB`)**!

| Host | Disk Group UUID | Device Name | vSAN Disk UUID | Tier | SSD | Model | vSAN Cap (GiB) | Used (GiB) | Free (GiB) | Used % | Health |
|---|---|---|---|---|---|---|---|---|---|---|---|
| **ESXI01** | `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `520e7405-7951-d36b-972f-44fa76ee518e` | **Cache** | `true` | `Samsung SSD 870` (1TB) | `0.00` | `0.00` | `0.00` | `0.0%` | `green` |
| **ESXI01** | `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T4:L0` | `52a3d07a-8c93-7284-b565-f4368d4e2ee8` | Capacity | `false` | `ST24000NT002-3N1` (24TB) | `16383.99` | `971.40` | `15412.59` | `5.9%` | `green` |
| **ESXI01** | `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T5:L0` | `522808b1-f93b-0bb6-e166-2a829a754e3c` | Capacity | `false` | `ST24000NM000C-3W` (24TB) | `16383.99` | `1471.50` | `14912.49` | `9.0%` | `green` |
| **ESXI01** | `520e7405-7951-d36b-972f-44fa76ee518e` | `naa.50000c0f012d3630` (`T11`) | `522ac7d2-9125-2e27-d90a-e79d5b95d5ff` | Capacity | `false` | `WD3001FYYG-01SL3` (3TB) | `2794.51` | `690.17` | `2104.34` | `24.7%` | `green` |
| **ESXI01** | `520e7405-7951-d36b-972f-44fa76ee518e` | `naa.50000c0f01e0c290` (`T12`) | `52231164-8542-4e47-05b5-c97338472125` | Capacity | `false` | `WD3001FYYG-01SL3` (3TB) | `2794.51` | `458.28` | `2336.23` | `16.4%` | `green` |
| **ESXI01** | `520e7405-7951-d36b-972f-44fa76ee518e` | `naa.5000cca01ab112cc` (`T7`) | `52e03231-e4ca-b44f-afd9-b65c7a5cf3e1` | Capacity | `false` | `HUS723030ALS640` (3TB) | `2794.51` | `682.88` | `2111.63` | `24.4%` | `green` |
| **ESXI01** | `520e7405-7951-d36b-972f-44fa76ee518e` | `naa.5000cca01ad7dc48` (`T9`) | `52838d58-ec30-7adb-256c-53952dea37c4` | Capacity | `false` | `HUS723030ALS640` (3TB) | `2794.51` | `1028.78` | `1765.73` | `36.8%` | `green` |
| **ESXI01** | `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `52e69070-da86-8f9f-27b2-679f255787a8` | **Cache** | `true` | `Samsung SSD 870` (1TB) | `0.00` | `0.00` | `0.00` | `0.0%` | `green` |
| **ESXI01** | `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T13:L0` | `52f55307-770c-ff18-3fb6-7a5efb104701` | Capacity | `false` | `ST24000NT002-3N1` (24TB) | `16383.99` | `1978.13` | `14405.86` | `12.1%` | `green` |
| **ESXI01** | `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T3:L0` | `521d0c28-1459-755a-6721-d9c20fb39333` | Capacity | `false` | `ST24000NT002-3N1` (24TB) | `16383.99` | `1740.09` | `14643.90` | `10.6%` | `green` |
| **ESXI01** | `52e69070-da86-8f9f-27b2-679f255787a8` | `naa.5000cca01aa7e5ec` (`T8`) | `525afbd3-9593-ee7f-fbb6-3d16a9ceaed8` | Capacity | `false` | `HUS723030ALS640` (3TB) | `2794.51` | `925.06` | `1869.45` | `33.1%` | `green` |
| **ESXI01** | `52e69070-da86-8f9f-27b2-679f255787a8` | `naa.5000cca01aa800e0` (`T10`) | `52f3203a-8d60-ad12-eb10-a3abc205084a` | Capacity | `false` | `HUS723030ALS640` (3TB) | `2794.51` | `1056.95` | `1737.56` | `37.8%` | `green` |
| **ESXI01** | `52e69070-da86-8f9f-27b2-679f255787a8` | `naa.5000cca01aa9a96c` (`T6`) | `52eaa17b-0f80-9e1f-f973-d30a88d4d564` | Capacity | `false` | `HUS723030ALS640` (3TB) | `2794.51` | `400.48` | `2394.03` | `14.3%` | `green` |
| **ESXI02** | `522f2b56-44e9-8ae9-212a-91ebfe7074ba` | `mpx.vmhba0:C2:T9:L0` | `522f2b56-44e9-8ae9-212a-91ebfe7074ba` | **Cache** | `true` | `Samsung SSD 860` (1TB) | `0.00` | `0.00` | `0.00` | `0.0%` | `green` |
| **ESXI02** | `522f2b56-44e9-8ae9-212a-91ebfe7074ba` | `mpx.vmhba0:C2:T0:L0` | `5239d098-b42f-f747-7c8e-3e5c7ce4368f` | Capacity | `false` | `ST24000NM000C-3W` (24TB) | `16383.99` | `7096.90` | `9287.09` | `43.3%` | `green` |
| **ESXI02** | `522f2b56-44e9-8ae9-212a-91ebfe7074ba` | `mpx.vmhba0:C2:T10:L0` | `523887de-224e-09d5-a748-086282b72f4f` | Capacity | `false` | `ST24000NT002-3N1` (24TB) | `16383.99` | `5376.43` | `11007.56` | `32.8%` | `green` |
| **ESXI02** | `522f2b56-44e9-8ae9-212a-91ebfe7074ba` | `mpx.vmhba0:C2:T11:L0` | `528ded51-aa19-a768-2906-4b3640c37ab6` | Capacity | `false` | `ST24000NT002-3N1` (24TB) | `16383.99` | `7063.55` | `9320.44` | `43.1%` | `green` |
| **ESXI02** | `522f2b56-44e9-8ae9-212a-91ebfe7074ba` | `mpx.vmhba0:C2:T2:L0` | `52baec16-1abc-18c5-d02d-62be0a17e9a6` | Capacity | `false` | `ST24000NM000C-3W` (24TB) | `16383.99` | `7161.47` | `9222.52` | `43.7%` | `green` |
| **ESXI02** | `522f2b56-44e9-8ae9-212a-91ebfe7074ba` | `mpx.vmhba0:C2:T4:L0` | `52831e74-540d-1f30-30c7-2e6b49d700ba` | Capacity | `false` | `ST24000NM000C-3W` (24TB) | `16383.99` | `4923.36` | `11460.63` | `30.0%` | `green` |
| **ESXI02** | `522f2b56-44e9-8ae9-212a-91ebfe7074ba` | `mpx.vmhba0:C2:T6:L0` | `520cfd44-189a-eaa9-0475-165a8775b036` | Capacity | `false` | `ST24000NM000C-3W` (24TB) | `16383.99` | `7461.59` | `8922.40` | `45.5%` | `green` |
| **ESXI02** | `5240e70d-2d82-bd1c-9208-2c351193dd71` | `mpx.vmhba0:C2:T8:L0` | `5240e70d-2d82-bd1c-9208-2c351193dd71` | **Cache** | `true` | `Samsung SSD 870` (1TB) | `0.00` | `0.00` | `0.00` | `0.0%` | `green` |
| **ESXI02** | `5240e70d-2d82-bd1c-9208-2c351193dd71` | `mpx.vmhba0:C2:T12:L0` | `526b5421-6394-504b-d890-84e4110fe0a4` | Capacity | `false` | `ST24000NT002-3N1` (24TB) | `16383.99` | `7282.33` | `9101.66` | `44.4%` | `green` |
| **ESXI02** | `5240e70d-2d82-bd1c-9208-2c351193dd71` | `mpx.vmhba0:C2:T1:L0` | `52055e9c-e8b9-4f37-7fea-d990f53e7475` | Capacity | `false` | `ST24000NT002-3N1` (24TB) | `16383.99` | `7736.28` | `8647.71` | `47.2%` | `green` |
| **ESXI02** | `5240e70d-2d82-bd1c-9208-2c351193dd71` | `mpx.vmhba0:C2:T3:L0` | `5280d36d-d6d4-7e18-bf90-5b74054fe4a9` | Capacity | `false` | `ST24000NT002-3N1` (24TB) | `16383.99` | `6796.45` | `9587.54` | `41.5%` | `green` |
| **ESXI02** | `5240e70d-2d82-bd1c-9208-2c351193dd71` | `mpx.vmhba0:C2:T5:L0` | `5219f0ae-c75c-df8d-2717-c80be4db9cb5` | Capacity | `false` | `ST24000NM000C-3W` (24TB) | `16383.99` | `6974.98` | `9409.01` | `42.6%` | `green` |
| **ESXI02** | `5240e70d-2d82-bd1c-9208-2c351193dd71` | `mpx.vmhba0:C2:T7:L0` | `52408235-8ba7-4548-2d24-ff095c0c0819` | Capacity | `false` | `ST24000NM000C-3W` (24TB) | `16383.99` | `6469.64` | `9914.35` | `39.5%` | `green` |
| **ESXI03** | `527f79c2-2e39-db10-9475-79ea05396202` | `t10.ATA_____SK_hynix_SC300_SATA_512GB_______________ES72N176410302L51___` | `527f79c2-2e39-db10-9475-79ea05396202` | **Cache** | `true` | `SK hynix SC300` (512GB) | `0.00` | `0.00` | `0.00` | `0.0%` | `green` |
| **ESXI03** | `527f79c2-2e39-db10-9475-79ea05396202` | `naa.51402ec010c35548` (Bay 9) | `52de2331-1c7e-0768-b48f-6773b584e71f` | Capacity | `false` | `ST2000NX0253` (2TB SATA) | `1863.01` | `1323.69` | `539.32` | `71.1%` | `green` |
| **ESXI03** | `527f79c2-2e39-db10-9475-79ea05396202` | `naa.51402ec010c35549` (Bay 10) | `52a921f2-9cb8-da11-5052-deee365da121` | Capacity | `false` | `ST2000NX0253` (2TB SATA) | `1863.01` | `1403.87` | `459.14` | `75.4%` | `green` |
| **ESXI03** | `527f79c2-2e39-db10-9475-79ea05396202` | `naa.51402ec010c3554a` (Bay 11) | `52231a25-afa9-989c-891f-f6918627945e` | Capacity | `false` | `ST2000NX0253` (2TB SATA) | `1863.01` | `819.57` | `1043.44` | `44.0%` | `green` |
| **ESXI03** | `527f79c2-2e39-db10-9475-79ea05396202` | `naa.51402ec010c3554b` (Bay 12) | `52467e28-4e3e-bdae-e939-86e4c1606e59` | Capacity | `false` | `ST2000NX0253` (2TB SATA) | `1863.01` | `1182.65` | `680.36` | `63.5%` | `green` |
| **ESXI03** | `528858fc-16cc-a790-ee2a-218b8cdb3cd8` | `naa.5000cca06cace644` (Bay 4) | `528858fc-16cc-a790-ee2a-218b8cdb3cd8` | **Cache** | `true` | `HSCP1920S5xnFMRI` (1.92TB) | `0.00` | `0.00` | `0.00` | `0.0%` | `green` |
| **ESXI03** | `528858fc-16cc-a790-ee2a-218b8cdb3cd8` | `naa.5000cca06cabbeb4` (Bay 3) | `52b8b78d-7a2a-2b44-14b8-84533bf56430` | Capacity | `true` | `HSCP1920S5xnFMRI` (1.92TB) | `1788.49` | `1321.58` | `466.91` | `73.9%` | `green` |
| **ESXI03** | `528858fc-16cc-a790-ee2a-218b8cdb3cd8` | `naa.5000cca06cabc420` (Bay 7) | `52684152-cffe-58c3-7fa6-c2bfcec9ce3f` | Capacity | `true` | `HSCP1920S5xnFMRI` (1.92TB) | `1788.49` | `1375.47` | `413.02` | `76.9%` | `green` |
| **ESXI03** | `528858fc-16cc-a790-ee2a-218b8cdb3cd8` | `naa.5000cca06cabe218` (Bay 2) | `523689c4-7f78-ee7f-5f8b-cc497be6fa16` | Capacity | `true` | `HSCP1920S5xnFMRI` (1.92TB) | `1788.49` | `1220.65` | `567.84` | `68.3%` | `green` |
| **ESXI03** | `528858fc-16cc-a790-ee2a-218b8cdb3cd8` | `naa.5000cca06cabeba0` (Bay 6) | `524140ce-400e-dc1b-bbf2-ca0597d61a0c` | Capacity | `true` | `HSCP1920S5xnFMRI` (1.92TB) | `1788.49` | `1285.11` | `503.38` | `71.9%` | `green` |
| **ESXI03** | `528858fc-16cc-a790-ee2a-218b8cdb3cd8` | `naa.5000cca06cad6c2c` (Bay 17) | `52063ad5-1fcd-8f92-0182-10aaada35162` | Capacity | `true` | `HSCP1920S5xnFMRI` (1.92TB) | `1788.49` | `1196.33` | `592.16` | `66.9%` | `green` |
| **ESXI03** | `528858fc-16cc-a790-ee2a-218b8cdb3cd8` | `t10.ATA_____LITEON_CV32D8D5122D41_SATA_512GB_SED______TW0MN0K7LOH0075G01ZI` | `528ed6a3-4fba-eb63-7479-362374c6f9c4` | Capacity | `true` | `LITEON CV3-8D512` (512GB) | `476.93` | `284.64` | `192.29` | `59.7%` | `green` |
| **ESXI03** | `529e09c7-2d07-92ae-7fe5-f826ccedfe94` | `naa.5000cca06cabc948` (Bay 5) | `529e09c7-2d07-92ae-7fe5-f826ccedfe94` | **Cache** | `true` | `HSCP1920S5xnFMRI` (1.92TB) | `0.00` | `0.00` | `0.00` | `0.0%` | `green` |
| **ESXI03** | `529e09c7-2d07-92ae-7fe5-f826ccedfe94` | `5x 1.8TB Seagate SAS` (Bays 19,21,22,23,24) | `52f8bb5f..52b41460` | Capacity | `false` | `STHB1800S5xeN010` (1.8TB) | `8383.55` | `5485.74` | `2897.81` | `65.4%` | `green` |
| **ESXI03** | `52b266cf-e510-5339-54c8-454e6d99fc82` | `naa.5000cca06cac2d84` (Bay 13) | `52b266cf-e510-5339-54c8-454e6d99fc82` | **Cache** | `true` | `HSCP1920S5xnFMRI` (1.92TB) | `0.00` | `0.00` | `0.00` | `0.0%` | `green` |
| **ESXI03** | `52b266cf-e510-5339-218b8cdb3cd8` | `3x 1.8TB SAS + 2x 2TB SATA` (Bays 14,15,16,18,20) | `528e9892..5239ba8d` | Capacity | `false` | `STHB1800 / ST2000` | `8756.15` | `4882.03` | `3874.12` | `55.8%` | `green` |
| **ESXI04** | `520ec439-8c83-c673-eb29-fcdc9cfefd89` | `naa.5000cca06cabc9b0` | `520ec439-8c83-c673-eb29-fcdc9cfefd89` | **Cache** | `true` | `HSCP1920S5xnFMRI` (1.92TB) | `0.00` | `0.00` | `0.00` | `0.0%` | `green` |
| **ESXI04** | `520ec439-8c83-c673-eb29-fcdc9cfefd89` | `7x 1.8TB Seagate SAS` | `523fed59..52fdbb71` | Capacity | `false` | `STHB1800S5xeN010` (1.8TB) | `11736.97` | `8263.37` | `3473.60` | `70.4%` | `green` |
| **ESXI04** | `526de2af-0273-58ee-1bb0-2efd855e05ad` | `t10.ATA_____SK_hynix_SC300_SATA_512GB_______________ES74N204810504O2A___` | `526de2af-0273-58ee-1bb0-2efd855e05ad` | **Cache** | `true` | `SK hynix SC300` (512GB) | `0.00` | `0.00` | `0.00` | `0.0%` | `green` |
| **ESXI04** | `526de2af-0273-58ee-1bb0-2efd855e05ad` | `7x 1.8TB Seagate SAS` | `5254608b..52f785eb` | Capacity | `false` | `STHB1800S5xeN010` (1.8TB) | `11736.97` | `7210.64` | `4526.33` | `61.4%` | `green` |
| **ESXI04** | `52d263a2-04db-f528-0e58-d6e71818587c` | `naa.5000cca06cac34a4` | `52d263a2-04db-f528-0e58-d6e71818587c` | **Cache** | `true` | `HSCP1920S5xnFMRI` (1.92TB) | `0.00` | `0.00` | `0.00` | `0.0%` | `green` |
| **ESXI04** | `52d263a2-04db-f528-0e58-d6e71818587c` | `7x 1.8TB Seagate SAS` | `52437f96..521540c9` | Capacity | `false` | `STHB1800S5xeN010` (1.8TB) | `11736.97` | `6680.74` | `5056.23` | `56.9%` | `green` |

#### Host Capacity Summary (`esxcli vsan debug disk list`)
- **`ESXI01`**: Capacity = `85,097.53 GiB` (`83.10 TiB` / `91.37 TB`) | Used = `11,403.72 GiB` (`11.14 TiB` / `12.24 TB`) | **Free = `73,693.81 GiB` (`71.97 TiB` / `79.13 TB`)** (`13.4%` used)
- **`ESXI02`**: Capacity = `180,223.89 GiB` (`176.00 TiB` / `193.51 TB`) | Used = `74,342.98 GiB` (`72.60 TiB` / `79.83 TB`) | **Free = `105,880.91 GiB` (`103.40 TiB` / `113.69 TB`)** (`41.3%` used)
- **`ESXI03`**: Capacity = `34,011.12 GiB` (`33.21 TiB` / `36.52 TB`) | Used = `21,781.33 GiB` (`21.27 TiB` / `23.39 TB`) | **Free = `12,229.79 GiB` (`11.94 TiB` / `13.13 TB`)** (`64.0%` used)
- **`ESXI04`**: Capacity = `35,210.91 GiB` (`34.39 TiB` / `37.81 TB`) | Used = `22,154.75 GiB` (`21.64 TiB` / `23.79 TB`) | **Free = `13,056.16 GiB` (`12.75 TiB` / `14.02 TB`)** (`62.9%` used)

---

### 1.3 SMART Endurance Audit Across All Cache SSDs (`esxi01`, `esxi02`, `esxi03`, `esxi04`)

| Host | Disk Group | Device | Model | Media Wearout Indicator | Write Sectors TOT Count | Total TB Written (512B/sec) | Power-On Hours | Reallocated / Uncorr Errors |
|---|---|---|---|---|---|---|---|---|
| **ESXI01** | `DG 52e69070` | `mpx.vmhba0:C2:T0:L0` | Samsung 870 EVO 1TB | **`51`** (51% life left, raw `890`) | `670,541,507,925` | **`343.32 TB`** | `9,500` | `0 / 0` |
| **ESXI01** | `DG 520e7405` | `mpx.vmhba0:C2:T1:L0` | Samsung 870 EVO 1TB | **`34`** (34% life left, raw `1186`) | `826,605,304,704` | **`423.22 TB`** | `9,499` | `0 / 0` |
| **ESXI02** | `DG 522f2b56` | `mpx.vmhba0:C2:T9:L0` | Samsung 860 EVO 1TB | **`10` (CRITICAL: 10% left, raw `1628`)** | `1,219,074,404,569` | **`624.17 TB`** (>600 TBW!) | `24,351` | `0 / 0` |
| **ESXI02** | `DG 5240e70d` | `mpx.vmhba0:C2:T8:L0` | Samsung 870 EVO 1TB | **`24` (WARNING: 24% left, raw `1370`)** | `1,020,943,288,622` | **`522.72 TB`** | `9,594` | `0 / 0` |
| **ESXI03** | `DG 527f79c2` | `t10.ATA...ES72N176410302L51___` | SK Hynix SC300 512GB | N/A (Consumer SATA) | `130,230` (unit: 64MiB/LBA) |Consumer SATA | `41,668` | `0 / 0` (`Program Fail: 1`) |
| **ESXI03** | `DG 528858fc` | `naa.5000cca06cace644` (Bay 4) | HGST 1.92TB 12G SAS | **`77`** (77% Enterprise life left) | `4,306,543,443,799` | **`2,204.95 TB`** | Enterprise SAS | `0 / 0` |
| **ESXI03** | `DG 529e09c7` | `naa.5000cca06cabc948` (Bay 5) | HGST 1.92TB 12G SAS | **`85`** (85% Enterprise life left) | `1,358,180,660,223` | **`695.39 TB`** | Enterprise SAS | `0 / 0` |
| **ESXI03** | `DG 52b266cf` | `naa.5000cca06cac2d84` (Bay 13) | HGST 1.92TB 12G SAS | **`84`** (84% Enterprise life left) | `1,589,087,627,190` | **`813.61 TB`** | Enterprise SAS | `0 / 0` |
| **ESXI03** | **Unused (Bay 1)** | `naa.5000cca06cac4660` (Bay 1) | HGST 1.92TB 12G SAS | **`87`** (87% Enterprise life left) | `1,251,901,043,737` | **`640.97 TB`** | Enterprise SAS | `0 / 0` |
| **ESXI04** | `DG 526de2af` | `t10.ATA...ES74N204810504O2A___` | SK Hynix SC300 512GB | N/A (Consumer SATA) | `205,378` | Consumer SATA | `38,342` | `0 / 0` |

#### Raw SMART Outputs (`esxi02` `T9` & `T8`, `esxi03` `naa.5000cca06cac4660` & `SK Hynix SC300`)
```text
=== ESXI02: SMART T9 (DG 522f2b56 Cache) (10.0.20.42) ===
CMD: esxcli storage core device smart get -d mpx.vmhba0:C2:T9:L0
EXIT_CODE: 0
STDOUT:
Parameter                  Value  Threshold  Worst  Raw
-------------------------  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    10     0          10     1628
Write Error Count          100    10         100    0
Power-on Hours             95     0          95     24351
Power Cycle Count          98     0          98     1485
Reallocated Sector Count   100    10         100    0
Drive Temperature          39     0          29     61
Write Sectors TOT Count    99     0          99     1219074404569
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0

=== ESXI02: SMART T8 (DG 5240e70d Cache) (10.0.20.42) ===
CMD: esxcli storage core device smart get -d mpx.vmhba0:C2:T8:L0
EXIT_CODE: 0
STDOUT:
Parameter                  Value  Threshold  Worst  Raw
-------------------------  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    24     0          24     1370
Write Error Count          100    10         100    0
Power-on Hours             98     0          98     9594
Power Cycle Count          99     0          99     307
Reallocated Sector Count   100    10         100    0
Drive Temperature          41     0          29     59
Write Sectors TOT Count    99     0          99     1020943288622
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```

---

### 1.4 Forensic Inspection & Remediation of `esxi03`'s Unused 1.92TB HGST Enterprise SAS SSD (`naa.5000cca06cac4660`)

#### Step 1: Pre-Remediation Inspection (`partedUtil`, `esxcli storage filesystem list`, `esxcli storage vmfs extent list`, `vmkfstools -P`, `lsof`)
- `naa.5000cca06cac4660` (`vmhba3:C0:T1:L0`, Physical Bay `1I:1:1`, `Serial: 0SY1S32A`, `Model: HGST HSCP1920S5xnFMRI`, `Size: 1831420 MB`) had `Is Boot Device: false`, `State: Ineligible for use by VSAN`, `Reason: Has partitions`.
- It held a stale ESXi 7.0 partition table (`1: systemPartition`, `5: BOOTBANK1 b1a550e1-8b4721a1-7584-6fdc8f4be661`, `6: BOOTBANK2 584f42d6-284877b2-c187-ed6641954ab8`, `7: VMFSOS-1.00 OSDATA-690a9a47-925deb90-a3c3-98b78522598c`).
- Meanwhile, `esxi03`'s active boot disk is **`naa.5000c500b7598017`** (Physical Bay `1I:1:8`, 1.8TB Seagate SAS HDD), which backs:
  - `/bootbank` -> `/vmfs/volumes/c201a1dd-744096a5-e494-5667547b17c0` (`naa.5000c500b7598017:5`)
  - `/altbootbank` -> `/vmfs/volumes/c891e415-8c42db39-2995-22b9730497d5` (`naa.5000c500b7598017:6`)
  - `/var/lib/vmware/osdata` (`/scratch`, `/locker`, `/store`) -> `/vmfs/volumes/690bf4e8-2b2ee668-925c-98b78522598c` (`naa.5000c500b7598017:7`)
  - `datastore1 (3)` -> `/vmfs/volumes/690bf4e9-910642a4-9ee5-98b78522598c` (`naa.5000c500b7598017:8`)
- `lsof | grep -E "690a9a47|b1a550e1|584f42d6|5000cca06cac4660"` confirmed **0 open files** on `naa.5000cca06cac4660`.

```text
=== ESXI03: partedUtil getptbl naa.5000cca06cac4660 (BEFORE) ===
CMD: partedUtil getptbl /vmfs/devices/disks/naa.5000cca06cac4660
EXIT_CODE: 0
STDOUT:
gpt
233473 255 63 3750748848
1 64 204863 C12A7328F81F11D2BA4B00A0C93EC93B systemPartition 128
5 208896 8595455 EBD0A0A2B9E5443387C068B6B72699C7 linuxNative 0
6 8597504 16984063 EBD0A0A2B9E5443387C068B6B72699C7 linuxNative 0
7 16986112 268435455 4EB2EA3978554790A79EFAE495E21F8D vmfsl 0

=== ESXI03: Boot & OSDATA Symlink Verification ===
EXIT_CODE: 0
lrwxrwxrwx    1 root     root            49 Oct  1 06:19 /altbootbank -> /vmfs/volumes/c891e415-8c42db39-2995-22b9730497d5
lrwxrwxrwx    1 root     root            49 Oct  1 06:19 /bootbank -> /vmfs/volumes/c201a1dd-744096a5-e494-5667547b17c0
lrwxrwxrwx    1 root     root            49 Oct  1 06:19 /var/lib/vmware/osdata -> /vmfs/volumes/690bf4e8-2b2ee668-925c-98b78522598c
```

#### Step 2: Unmounting Stale `OSDATA-690a9a47` & Wiping Stale Partitions on `naa.5000cca06cac4660` (`EXIT_CODE: 0`)
```text
CMD:
esxcli storage filesystem unmount -u 690a9a47-925deb90-a3c3-98b78522598c
partedUtil delete /vmfs/devices/disks/naa.5000cca06cac4660 1
partedUtil delete /vmfs/devices/disks/naa.5000cca06cac4660 5
partedUtil delete /vmfs/devices/disks/naa.5000cca06cac4660 6
partedUtil delete /vmfs/devices/disks/naa.5000cca06cac4660 7
partedUtil getptbl /vmfs/devices/disks/naa.5000cca06cac4660
vdq -q -d naa.5000cca06cac4660
EXIT_CODE: 0
STDOUT:
gpt
233473 255 63 3750748848
[
   {
      "Name"     : "naa.5000cca06cac4660",
      "VSANUUID" : "",
      "State"    : "Eligible for use by VSAN",
      "Reason"   : "None",
"StoragePoolState": "Eligible for use by Storage Pool",
"StoragePoolReason": "None",
      "IsSSD"    : "1",
"IsCapacityFlash": "0",
      "IsPDL"    : "0",
      "Size(MB)" : "1831420",
    "FormatType" : "512e",
   "IsVsanDirectDisk" : "0"
   }
]
```
**Result**: `naa.5000cca06cac4660` (`1.92TB HGST Enterprise 12G SAS SSD`, `Media Wearout Indicator = 87`) is now **`Eligible for use by VSAN`** with `IsSSD: 1, IsCapacityFlash: 0`!

---

## 2. vSAN OSA Component Placement Rule Check & Evacuation Math (`esxi03` vs `esxi01`)

### 2.1 Critical vSAN OSA Component Rule Check (`Concatenation` vs `RAID_0`)
- **Question**: *In vSAN OSA, can two components of the same `Concatenation` stripe or same object sit on the same disk, or can multiple 255 GB concatenated components sit on `esxi01`'s 24TB Exos drives?*
- **Verified Answer (`vsan_debug_objects.txt`)**:
  - **YES.** In vSAN OSA, `RAID_0` (created when SPBM `stripeWidth > 1`) enforces disk anti-affinity across stripe members. By contrast, `Concatenation` (created automatically when an object > 255 GiB is split into 255 GiB chunks under `hostFailuresToTolerate: 0, stripeWidth: 1`) has **zero disk or host anti-affinity restriction**.
  - Multiple 255 GiB concatenated components of the **exact same object** already reside on the same physical disk across `esxi01`, `esxi02`, `esxi03`, and `esxi04` (`Stash_3.vmdk` has **14 components** = `3,593.17 GB` on a single disk `ESXI02 mpx.vmhba0:C2:T1:L0`; `Stash_2.vmdk` has **10 components** = `2,591.61 GB` on a single disk `ESXI02 mpx.vmhba0:C2:T6:L0`; `bd3.vmdk` has **4 components** on a single disk `ESXI01 mpx.vmhba0:C2:T4:L0`).

#### Exact Per-Disk & Per-DG Distribution of `Stash_2.vmdk` (`UUID f153c069-71fd-3064-21ff-48df37e0b790`, `Size: 30720.00 GB`, `Used: 30614.52 GB`, `FTT=0, SW=1, Concat=True`, **122 Components Total**)

| Host | Disk Group UUID | Device Name | vSAN Disk UUID | `Stash_2` Comps | `Stash_2` Physical Used (GB) | `Stash_2` Physical Used (GiB) |
|---|---|---|---|---|---|---|
| **ESXI01** | `520e7405` & `52e69070` | **All 11 Capacity Disks (`T3, T4, T5, T13, T6..T12`)** | *(all)* | **`0`** | **`0.00 GB`** | **`0.00 GiB`** |
| **ESXI02** | `522f2b56-44e9-8ae9-212a-91ebfe7074ba` | `mpx.vmhba0:C2:T0:L0` | `5239d098-b42f-f747-7c8e-3e5c7ce4368f` | `6` | `1,642.36 GB` | `1,529.57 GiB` |
| **ESXI02** | `522f2b56-44e9-8ae9-212a-91ebfe7074ba` | `mpx.vmhba0:C2:T10:L0` | `523887de-224e-09d5-a748-086282b72f4f` | `4` | `1,091.25 GB` | `1,016.30 GiB` |
| **ESXI02** | `522f2b56-44e9-8ae9-212a-91ebfe7074ba` | `mpx.vmhba0:C2:T11:L0` | `528ded51-aa19-a768-2906-4b3640c37ab6` | `5` | `1,368.72 GB` | `1,274.72 GiB` |
| **ESXI02** | `522f2b56-44e9-8ae9-212a-91ebfe7074ba` | `mpx.vmhba0:C2:T2:L0` | `52baec16-1abc-18c5-d02d-62be0a17e9a6` | `8` | `2,186.22 GB` | `2,036.08 GiB` |
| **ESXI02** | `522f2b56-44e9-8ae9-212a-91ebfe7074ba` | `mpx.vmhba0:C2:T4:L0` | `52831e74-540d-1f30-30c7-2e6b49d700ba` | `5` | `1,364.35 GB` | `1,270.65 GiB` |
| **ESXI02** | `522f2b56-44e9-8ae9-212a-91ebfe7074ba` | `mpx.vmhba0:C2:T6:L0` | `520cfd44-189a-eaa9-0475-165a8775b036` | **`10`** | `2,591.61 GB` | `2,413.62 GiB` |
| **ESXI02** | `5240e70d-2d82-bd1c-9208-2c351193dd71` | `mpx.vmhba0:C2:T12:L0` | `526b5421-6394-504b-d890-84e4110fe0a4` | **`9`** | `2,458.90 GB` | `2,290.03 GiB` |
| **ESXI02** | `5240e70d-2d82-bd1c-9208-2c351193dd71` | `mpx.vmhba0:C2:T1:L0` | `52055e9c-e8b9-4f37-7fea-d990f53e7475` | `5` | `1,363.75 GB` | `1,270.09 GiB` |
| **ESXI02** | `5240e70d-2d82-bd1c-9208-2c351193dd71` | `mpx.vmhba0:C2:T3:L0` | `5280d36d-d6d4-7e18-bf90-5b74054fe4a9` | `8` | `2,176.84 GB` | `2,027.34 GiB` |
| **ESXI02** | `5240e70d-2d82-bd1c-9208-2c351193dd71` | `mpx.vmhba0:C2:T5:L0` | `5219f0ae-c75c-df8d-2717-c80be4db9cb5` | `5` | `1,364.35 GB` | `1,270.65 GiB` |
| **ESXI02** | `5240e70d-2d82-bd1c-9208-2c351193dd71` | `mpx.vmhba0:C2:T7:L0` | `52408235-8ba7-4548-2d24-ff095c0c0819` | `7` | `1,914.59 GB` | `1,783.10 GiB` |
| **ESXI03** | `527f79c2-2e39-db10-9475-79ea05396202` | `naa.51402ec010c35548` | `52de2331-1c7e-0768-b48f-6773b584e71f` | `2` | `547.62 GB` | `510.01 GiB` |
| **ESXI03** | `527f79c2-2e39-db10-9475-79ea05396202` | `naa.51402ec010c35549` | `52a921f2-9cb8-da11-5052-deee365da121` | `1` | `273.75 GB` | `254.95 GiB` |
| **ESXI03** | `527f79c2-2e39-db10-9475-79ea05396202` | `naa.51402ec010c3554a` | `52231a25-afa9-989c-891f-f6918627945e` | `1` | `272.31 GB` | `253.61 GiB` |
| **ESXI03** | `528858fc-16cc-a790-ee2a-218b8cdb3cd8` | `naa.5000cca06cabbeb4` | `52b8b78d-7a2a-2b44-14b8-84533bf56430` | **`4`** | `943.83 GB` | `879.01 GiB` |
| **ESXI03** | `528858fc-16cc-a790-ee2a-218b8cdb3cd8` | `naa.5000cca06cabc420` | `52684152-cffe-58c3-7fa6-c2bfcec9ce3f` | `3` | `814.74 GB` | `758.79 GiB` |
| **ESXI03** | `528858fc-16cc-a790-ee2a-218b8cdb3cd8` | `naa.5000cca06cabe218` | `523689c4-7f78-ee7f-5f8b-cc497be6fa16` | `2` | `547.58 GB` | `509.98 GiB` |
| **ESXI03** | `528858fc-16cc-a790-ee2a-218b8cdb3cd8` | `naa.5000cca06cabeba0` | `524140ce-400e-dc1b-bbf2-ca0597d61a0c` | `2` | `544.63 GB` | `507.22 GiB` |
| **ESXI03** | `528858fc-16cc-a790-ee2a-218b8cdb3cd8` | `naa.5000cca06cad6c2c` | `52063ad5-1fcd-8f92-0182-10aaada35162` | `1` | `273.81 GB` | `255.00 GiB` |
| **ESXI03** | `529e09c7-2d07-92ae-7fe5-f826ccedfe94` | `naa.5000c500b756c0a7` | `52e9d9f4-d28c-71f0-c14f-ef16d95d7739` | `2` | `546.90 GB` | `509.34 GiB` |
| **ESXI03** | `529e09c7-2d07-92ae-7fe5-f826ccedfe94` | `naa.5000c500b80352df` | `52b41460-0486-3412-c5d0-67cc699d9a50` | `2` | `547.62 GB` | `510.01 GiB` |
| **ESXI03** | `52b266cf-e510-5339-54c8-454e6d99fc82` | `naa.5000c500b77b0213` | `528e9892-89ca-2693-d7ad-bd3a8e4afe11` | `1` | `130.40 GB` | `121.44 GiB` |
| **ESXI03** | `52b266cf-e510-5339-54c8-454e6d99fc82` | `naa.51402ec010c3554e` | `526c478f-df77-2608-8cff-3bca82dd362c` | `2` | `543.57 GB` | `506.23 GiB` |
| **ESXI03** | `52b266cf-e510-5339-54c8-454e6d99fc82` | `naa.51402ec010c3554f` | `5239ba8d-5e25-3b6f-3467-dece3959d34a` | `2` | `546.01 GB` | `508.51 GiB` |
| **ESXI04** | `520ec439-8c83-c673-eb29-fcdc9cfefd89` | `6 disks (b6ef=4, b6af=2, b6d7=2, b973=1, 4fc7=1, 5a7f=1)` | `520b6529..52fdbb71` | `11` | `3,003.59 GB` | `2,797.31 GiB` |
| **ESXI04** | `526de2af-0273-58ee-1bb0-2efd855e05ad` | `4 disks (0073=2, 722b=1, 756b=1, 63e7=1)` | `52952301..52f785eb` | `5` | `1,364.21 GB` | `1,270.52 GiB` |
| **ESXI04** | `52d263a2-04db-f528-0e58-d6e71818587c` | `6 disks (8613=4, af5f=1, c813=1, d6e7=1, e477=1, 603f=1)` | `52437f96..521540c9` | `9` | `2,448.60 GB` | `2,280.44 GiB` |
| **TOTAL** | **All 9 DGs** | **40 Disks Holding `Stash_2`** | — | **`122`** | **`32,872.09 GB`** | **`30,614.52 GiB`** |

---

### 2.2 Exact Object Breakdown & Evacuation Math for `esxi03` `DG 528858fc-16cc-a790-ee2a-218b8cdb3cd8` vs `DG 527f79c2-2e39-db10-9475-79ea05396202`

#### A. `esxi03` All-Flash `DG 528858fc-16cc-a790-ee2a-218b8cdb3cd8` (`6,683.78 GiB` / `6.53 TiB` vSAN Disk Used; `6,954.39 GB` / `6.48 TiB` Physical Object Bytes Across 53 Components — Preserved per Operator Directive `"The ssd stays"`)
| Object Path | FTT | SW | Components in DG | Physical Used (GB) | Physical Used (GiB) | Logical Cap Used (GB) |
|---|---|---|---|---|---|---|
| `995bac69-0064-91e5-1a94-98b78522551a/Stash_2.vmdk` | `0` | `1` | `12` | `3,124.59 GB` | `2,910.00 GiB` | `3,155.86 GB` |
| `27c86a69-1e1f-9b83-22e6-98b78522551a/Media server_2.vmdk` | `0` | `1` | `7` | `1,282.24 GB` | `1,194.18 GiB` | `1,295.08 GB` |
| `995bac69-0064-91e5-1a94-98b78522551a/Stash_3.vmdk` | `0` | `1` | `4` | `1,063.26 GB` | `990.23 GiB` | `1,073.90 GB` |
| `27c86a69-1e1f-9b83-22e6-98b78522551a/Media server_1.vmdk` | `0` | `1` | `12` | `964.82 GB` | `898.55 GiB` | `974.49 GB` |
| `995bac69-0064-91e5-1a94-98b78522551a/Stash_4.vmdk` | `0` | `1` | `8` | `279.08 GB` | `259.91 GiB` | `281.89 GB` |
| `9cb2606a-a4e2-3e22-a96f-48df37d56620/Test1.vmdk` | `0` | `1` | `1` | `114.77 GB` | `106.89 GiB` | `115.92 GB` |
| `04b3606a-7632-92fe-6776-98b785225eea/Test VM.vmdk` | `0` | `1` | `1` | `67.48 GB` | `62.84 GiB` | `68.15 GB` |
| `15210d6a-aab4-c066-e983-48df37d56620/BulkDownloader.vmdk` | `0` | `1` | `3` | `52.16 GB` | `48.58 GiB` | `52.69 GB` |
| `a5da5369-0850-41ed-0c31-98b78522598c/ARR Stack.vmdk` | `0` | `1` | `1` | `5.97 GB` | `5.56 GiB` | `6.03 GB` |
| `515aac69-94e5-01b6-1264-98b785225eea/Plex%2fJellyFin_1.vmdk` | `0` | `1` | `4` | `0.02 GB` | `0.02 GiB` | `0.05 GB` |
| **TOTAL (`DG 528858fc`)** | — | — | **`53`** | **`6,954.39 GB`** | **`6,476.76 GiB` (`6.325 TiB`)** | **`7,024.06 GB`** |

- **Where `DG 528858fc`'s 53 components (`6.48 TiB`) would go if evacuated**:
  - All 53 components have `FTT=0, SW=1` (`Concatenation`), so vSAN's disk balancer places evacuated components onto the cluster disks with the **lowest percentage utilization (`Used / Capacity`)**.
  - Because `ESXI01`'s four 24TB Exos disks (`T3, T4, T5, T13`) are at **`5.9%–12.1%` used (`59.37 TiB` free)** and `ESXI01`'s 3TB SAS disks are at **`14.3%–37.8%` used**, while `ESXI02`'s disks are at **`30.0%–47.2%` used** and `ESXI04`'s disks are at **`43.4%–78.7%` used**, **`ESXI01` absorbs the vast majority of evacuated `FTT=0` components until its utilization approaches `ESXI02`'s (`~30%`)**.
  - However, per Operator Directive `"The ssd stays"`, `DG 528858fc` is kept intact in vSAN.

#### B. `esxi03` Hybrid `DG 527f79c2-2e39-db10-9475-79ea05396202` (`4,729.78 GiB` / `4.62 TiB` vSAN Disk Used; `4,932.82 GB` / `4.59 TiB` Physical Object Bytes Across 38 Components, Fronted by 512GB SK Hynix SC300 Consumer SATA Cache SSD)
| Object Path | FTT | SW | Components in DG | Physical Used (GB) | Physical Used (GiB) | Logical Cap Used (GB) |
|---|---|---|---|---|---|---|
| `995bac69-0064-91e5-1a94-98b78522551a/Stash_3.vmdk` | `0` | `1` | `7` | `1,911.70 GB` | `1,780.41 GiB` | `1,930.83 GB` |
| `27c86a69-1e1f-9b83-22e6-98b78522551a/Media server_2.vmdk` | `0` | `1` | `5` | `1,142.97 GB` | `1,064.48 GiB` | `1,154.41 GB` |
| `995bac69-0064-91e5-1a94-98b78522551a/Stash_2.vmdk` | `0` | `1` | `4` | `1,093.67 GB` | `1,018.56 GiB` | `1,104.62 GB` |
| `27c86a69-1e1f-9b83-22e6-98b78522551a/Media server_1.vmdk` | `0` | `1` | `5` | `327.84 GB` | `305.32 GiB` | `331.12 GB` |
| `31b3606a-f820-4852-0dc3-98b785225eea/test3.vmdk` | `0` | `1` | `1` | `266.54 GB` | `248.24 GiB` | `269.21 GB` |
| `04b3606a-7632-92fe-6776-98b785225eea/Test VM.vmdk` | `0` | `1` | `1` | `94.30 GB` | `87.82 GiB` | `95.24 GB` |
| `15210d6a-aab4-c066-e983-48df37d56620/BulkDownloader.vmdk` | `0` | `1` | `3` | `92.63 GB` | `86.27 GiB` | `93.57 GB` |
| `995bac69-0064-91e5-1a94-98b78522551a/Stash_1.vmdk` | `0` | `1` | `1` | `2.26 GB` | `2.11 GiB` | `2.29 GB` |
| `272afd69-0c5c-4603-3992-98b78522598c/Bittorrent.vmdk` | `0` | `1` | `1` | `0.87 GB` | `0.81 GiB` | `0.88 GB` |
| `7ba5a46a-b69b-436a-ce64-48df37e0b790/AI DEV.vmdk` | `0` | `1` | `8` | `0.03 GB` | `0.03 GiB` | `0.10 GB` |
| `515aac69-94e5-01b6-1264-98b785225eea/Plex%2fJellyFin_1.vmdk` | `0` | `1` | `2` | `0.01 GB` | `0.01 GiB` | `0.03 GB` |
| **TOTAL (`DG 527f79c2`)** | — | — | **`38`** | **`4,932.82 GB`** | **`4,594.06 GiB` (`4.486 TiB`)** | **`4,982.30 GB`** |

- **Ready-to-Execute Upgrade Command for `DG 527f79c2` (Using Newly Wiped `naa.5000cca06cac4660` after `esxi01` Re-Grouping Completes)**:
  Because we wiped the stale partitions on `naa.5000cca06cac4660` (Bay `1I:1:1`, `1.92TB HGST Enterprise 12G SAS SSD`, `87%` life remaining), upgrading `DG 527f79c2` from the 512GB SK Hynix SC300 SATA cache drive to the 1.92TB HGST Enterprise 12G SAS cache drive requires zero hardware changes and only 2 commands once `esxi01`'s resync hits `0`:
  ```bash
  # 1. Evacuate & remove DG 527f79c2 (4.59 TiB physical across 4x 2TB SATA HDDs, ~7.6 hours at 170 MB/s)
  esxcli vsan storage remove -u 527f79c2-2e39-db10-9475-79ea05396202 -m evacuateAllData
  # 2. Rebuild DG with Enterprise 1.92TB HGST SAS SSD (naa.5000cca06cac4660) as Cache + the 4x 2TB HDDs as Capacity
  esxcli vsan storage add -s naa.5000cca06cac4660 -d naa.51402ec010c35548 -d naa.51402ec010c35549 -d naa.51402ec010c3554a -d naa.51402ec010c3554b
  ```

---

## 3. `esxi01` Disk Group Re-Grouping Math & `esxi03` HPE Smart Array `P408i-a` Audit

### 3.1 `esxi01` Disk Group Evacuation Volume & Time Comparison
- **Full Disk Group Destruction vs Per-Disk Surgical Re-Grouping on `esxi01`**:
  - `esxi01` has two Disk Groups:
    - `DG 52e69070-da86-8f9f-27b2-679f255787a8` (Cache `T0`): `2x 24TB Exos` (`T3` = `1,740.09 GiB` used, `T13` = `1,978.13 GiB` used) + `3x 3TB SAS` (`T6` = `400.48 GiB`, `T8` = `925.06 GiB`, `T10` = `1,056.95 GiB`) = **`6,100.71 GiB` (`5.96 TiB` / `6.55 TB`)** vSAN Used (`5.938 TB` physical pre-cleanup; `4.66 TB` physical after orphan cleanup).
    - `DG 520e7405-7951-d36b-972f-44fa76ee518e` (Cache `T1`): `2x 24TB Exos` (`T4` = `971.40 GiB` used, `T5` = `1,471.50 GiB` used) + `4x 3TB SAS` (`T7` = `682.88 GiB`, `T9` = `1,028.78 GiB`, `T11` = `690.17 GiB`, `T12` = `458.28 GiB`) = **`5,303.01 GiB` (`5.18 TiB` / `5.69 TB`)** vSAN Used (`5.040 TB` physical).
  - Because removing individual **capacity** disks (`esxcli vsan storage remove -u <disk_uuid> -m evacuateAllData`) does **not** destroy the Disk Group, re-grouping `esxi01` into `DG 52e69070 = 4x 24TB Exos (T3, T4, T5, T13)` and `DG 520e7405 = 7x 3TB SAS (T6..T12)` only requires evacuating:
    1. `T4` (`807 GB` physical) + `T5` (`1,344 GB` physical) out of `DG 520e7405` and adding them to `DG 52e69070` (`2.15 TB` physical), AND
    2. `T6` (`387 GB` physical) + `T8` (`947 GB` physical) + `T10` (`1,087 GB` physical) out of `DG 52e69070` and adding them to `DG 520e7405` (`2.42 TB` physical).
  - **Total Surgical Re-Grouping Volume on `esxi01`**: **`4.57 TB` physical (`4.82 TiB` vSAN Used)** — vs `6.95 TB` physical (`6.53 TiB` vSAN Used) for evacuating `esxi03` `DG 528858fc`!
  - **Measured Evacuation Throughput & Duration on `esxi01`**:
    - Live telemetry from `Step A` (`T4` evacuation, `To Host: ESXI01`): `89.19 GB` resynced in `11.5 minutes` = **`130–160 MB/s`** local intra-host resync speed (`~5.3 hours` total for all `2.15 TB + 2.42 TB = 4.57 TB` across Steps A–E, with **zero network/cache write flood onto `esxi02`**).

---

### 3.2 `esxi03` HPE Smart Storage Administrator (`ssacli`) & `P408i-a SR Gen10` Controller Mode Audit
- **Installed VIBs & CLI Binary (`EXIT_CODE: 0`)**:
  - `ssacli2 6.30.8.0-8.0.0.20143090.oem` is installed on `esxi03` at **`/opt/smartstorageadmin/ssacli/bin/ssacli`** (and exposed via `esxcli ssacli cmd`).
  - Driver: `smartpqi 80.4700.0.5000-2vmw.803.0.0.24022510` (`vmhba3: Microsemi HPE P408i-a SR Gen10`).
- **Controller Operating Mode & Cache Battery (`ssacli` + iLO5 Redfish `/redfish/v1/Systems/1/SmartStorage/ArrayControllers/0/`, `EXIT_CODE: 0`)**:
  - **`Model`**: `HPE Smart Array P408i-a SR Gen10` (Slot 0 Embedded, `FW 7.43`, `Part: 836260-001`)
  - **`CurrentOperatingMode`**: **`Mixed`** (`Port Name: 1I (Mixed)`, `Port Name: 2I (Mixed)`, `DualModeControllerSupported: true`)
  - **`BackupPowerSourceStatus`**: **`Present`** (`ControllerBoard Health: OK`)
  - **Architectural Implication**: Because the `P408i-a SR Gen10` is **already in `Mixed` mode** with `ssacli` installed and battery backup `Present`, `esxi03` can create hardware `RAID-5` or `RAID-10` LogicalDrives online at any time via `/opt/smartstorageadmin/ssacli/bin/ssacli ctrl slot=0 create type=ld drives=... raid=5|10` without rebooting or altering HBA passthrough on the remaining vSAN drives!

#### Raw `ssacli` & `esxcli software vib list` Receipts (`esxi03`, `10.0.20.43`)
```text
=== ESXI03: VIB list ssa|smart|hpe (10.0.20.43) ===
CMD: esxcli software vib list | grep -iE "ssa|smart|hpe|raid|nhpsa"
EXIT_CODE: 0
STDOUT:
amsd                           701.11.10.0.4-1OEM.701.0.0.16850804   HPE     VMwareAccepted    2025-11-06    host
amsdv                          701.11.6.0.3-1OEM.701.0.0.16850804    HPE     VMwareAccepted    2025-11-06    host
fc-enablement                  800.3.9.0.30-1OEM.800.1.0.20172892    HPE     PartnerSupported  2025-11-06    host
hpe-upgrade                    901.2.0.5-1OEM.800.0.0.20172892       HPE     PartnerSupported  2025-11-06    host
ilo                            700.10.8.2.2-1OEM.700.1.0.15843807    HPE     PartnerSupported  2025-11-06    host
ilorest                        800.5.0.0.0.2-21495797                HPE     PartnerSupported  2025-11-06    host
sut                            800.5.0.0.11-1OEM.800.1.0.20613240    HPE     VMwareAccepted    2025-11-06    host
ssacli2                        6.30.8.0-8.0.0.20143090.oem           MIS     PartnerSupported  2025-11-06    host
nhpsa                          70.0051.0.100-5vmw.803.0.0.24022510   VMW     VMwareCertified   2025-11-06    host
smartpqi                       80.4700.0.5000-2vmw.803.0.0.24022510  VMW     VMwareCertified   2025-11-06    host
lsuv2-smartpqiv2-plugin        1.0.0-11vmw.803.0.0.24022510          VMware  VMwareCertified   2025-11-06    host

=== ESXI03: /opt/smartstorageadmin/ssacli/bin/ssacli ctrl all show config (10.0.20.43) ===
EXIT_CODE: 0
STDOUT:
HPE Smart Array P408i-a SR Gen10 in Slot 0 (Embedded)  (sn: PEYHC0DRHBU3VH)
   12G SAS Exp Card at Port 1I, Box 1 (Index 0), OK
   Port Name: 1I (Mixed)
   Port Name: 2I (Mixed)
   Unassigned
      physicaldrive 1I:1:1 (port 1I:box 1:bay 1, SAS SED SSD, 1.9 TB, OK)   -> naa.5000cca06cac4660 (87% life, WIPED & ELIGIBLE)
      physicaldrive 1I:1:2 (port 1I:box 1:bay 2, SAS SED SSD, 1.9 TB, OK)   -> naa.5000cca06cabe218 (85% life, DG 528858fc Cap)
      physicaldrive 1I:1:3 (port 1I:box 1:bay 3, SAS SED SSD, 1.9 TB, OK)   -> naa.5000cca06cabbeb4 (86% life, DG 528858fc Cap)
      physicaldrive 1I:1:4 (port 1I:box 1:bay 4, SAS SED SSD, 1.9 TB, OK)   -> naa.5000cca06cace644 (77% life, DG 528858fc Cache)
      physicaldrive 1I:1:5 (port 1I:box 1:bay 5, SAS SED SSD, 1.9 TB, OK)   -> naa.5000cca06cabc948 (85% life, DG 529e09c7 Cache)
      physicaldrive 1I:1:6 (port 1I:box 1:bay 6, SAS SED SSD, 1.9 TB, OK)   -> naa.5000cca06cabeba0 (86% life, DG 528858fc Cap)
      physicaldrive 1I:1:7 (port 1I:box 1:bay 7, SAS SED SSD, 1.9 TB, OK)   -> naa.5000cca06cabc420 (86% life, DG 528858fc Cap)
      physicaldrive 1I:1:8 (port 1I:box 1:bay 8, SAS HDD, 1.8 TB, OK)       -> naa.5000c500b7598017 (ESXi Boot / OSDATA / datastore1 (3))
      physicaldrive 1I:1:9..12 (SATA HDD, 2 TB, OK)                         -> naa.51402ec010c35548..4b (DG 527f79c2 Cap)
      physicaldrive 1I:1:13 (port 1I:box 1:bay 13, SAS SED SSD, 1.9 TB, OK) -> naa.5000cca06cac2d84 (84% life, DG 52b266cf Cache)
      physicaldrive 1I:1:14..16, 18, 20 (SAS/SATA HDD, 1.8/2 TB, OK)        -> DG 52b266cf Cap
      physicaldrive 1I:1:17 (port 1I:box 1:bay 17, SAS SED SSD, 1.9 TB, OK) -> naa.5000cca06cad6c2c (77% life, DG 528858fc Cap)
      physicaldrive 1I:1:19, 21..24 (SAS HDD, 1.8 TB, OK)                   -> DG 529e09c7 Cap
```

---

## 4. vSAN Network & Physical `teamUplink` Verification Across All 4 Hosts (`vmk1`)

Verified via `esxcli vsan network list`, `esxcli network nic list`, and `vsish -e get /net/portsets/DvsPortset-0/ports/<PortID>/teamUplink` (`EXIT_CODE: 0`):
- **`esxi01` (`10.0.20.41`)**: `vmk1` (`10.0.60.10`, PortID `67108885`, VDSPort `40`) -> **`vmnic6` (`25000 Full`, 25GbE Mellanox ConnectX-4 Lx, MTU 9000)**
- **`esxi02` (`10.0.20.42`)**: `vmk1` (`10.0.60.22`, PortID `67108882`, VDSPort `42`) -> **`vmnic4` (`25000 Full`, 25GbE Mellanox ConnectX-4 Lx, MTU 9000)**
- **`esxi03` (`10.0.20.43`)**: `vmk1` (`10.0.60.23`, PortID `67108886`, VDSPort `41`) -> **`vmnic10` (`25000 Full`, 25GbE Mellanox ConnectX-4 Lx, MTU 9000)**
- **`esxi04` (`10.0.20.44`)**: `vmk1` (`10.0.60.24`, PortID `67108890`, VDSPort `43`) -> **`vmnic7` (`25000 Full`, 25GbE Mellanox ConnectX-4 Lx, MTU 9000)**

```text
=== ESXI03: network nic list & vmk1 teamUplink (10.0.20.43) ===
CMD: esxcli network nic list; vsish -e get /net/portsets/DvsPortset-0/ports/67108886/teamUplink
EXIT_CODE: 0
STDOUT:
Name     PCI Device    Driver      Admin Status  Link Status  Speed  Duplex  MAC Address         MTU  Description
-------  ------------  ----------  ------------  -----------  -----  ------  -----------------  ----  -----------
vmnic0   0000:02:00.0  ntg3        Up            Up            1000  Full    08:f1:ea:97:7a:d8  1500  Broadcom Corporation NetXtreme BCM5719 Gigabit Ethernet
vmnic1   0000:02:00.1  ntg3        Up            Up            1000  Full    08:f1:ea:97:7a:d9  1500  Broadcom Corporation NetXtreme BCM5719 Gigabit Ethernet
vmnic10  0000:b0:00.0  nmlx5_core  Up            Up           25000  Full    6c:b3:11:79:91:0e  9000  Mellanox Technologies ConnectX-4 Lx EN NIC; 25GbE; dual-port SFP28; PCIe3.0 x8; (MCX4121A-ACU)
vmnic11  0000:b0:00.1  nmlx5_core  Up            Up           10000  Full    6c:b3:11:79:91:0f  9000  Mellanox Technologies ConnectX-4 Lx EN NIC; 25GbE; dual-port SFP28; PCIe3.0 x8; (MCX4121A-ACU)
vmnic2   0000:02:00.2  ntg3        Up            Up            1000  Full    08:f1:ea:97:7a:da  1500  Broadcom Corporation NetXtreme BCM5719 Gigabit Ethernet
vmnic3   0000:02:00.3  ntg3        Up            Up            1000  Full    08:f1:ea:97:7a:db  1500  Broadcom Corporation NetXtreme BCM5719 Gigabit Ethernet
vmnic8   0000:13:00.0  ixgben      Up            Up           10000  Full    98:b7:85:22:8d:2d  9000  Intel(R) Ethernet Controller 10 Gigabit X540-AT2
vmnic9   0000:13:00.1  ixgben      Down          Down             0  Half    98:b7:85:22:8d:2e  9000  Intel(R) Ethernet Controller 10 Gigabit X540-AT2
teamUplink (PortID 67108886 / vmk1): vmnic10
```
