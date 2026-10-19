# AGENT-6: Per-Disk vSAN Surgical Re-Grouping Receipts (`esxi01`)

- **Generated At (UTC)**: `2026-10-17T17:08:58Z`
- **Agent**: Agent-6 (Per-Disk vSAN Surgical Re-Grouping Architect)
- **Operator Directive Compliance (`2026-10-17T16:59:29Z`)**:
  - **"The ssd stays"**: `t10.ATA_____LITEON_CV32D8D5122D41_SATA_512GB_SED______TW0MN0K7LOH0075G01ZI` in `esxi03` `DG 528858fc-16cc-a790-ee2a-218b8cdb3cd8` was **NOT removed** (`IsMounted=true`, `InCMMDS=true`). All SSDs in `esxi03` remain untouched in vSAN.
  - **Scope**: Software-only per-disk capacity re-grouping on `esxi01` so `DG 52e69070` (`mpx.vmhba0:C2:T0:L0`) holds all `4x 24TB Seagate Exos` (`T3, T4, T5, T13`) and `DG 520e7405` (`mpx.vmhba0:C2:T1:L0`) holds all `7x 3TB SAS` (`T6..T12`).
  - **Runtime Name (`T6..T12`) Ground Truth**:
    - `T6` = `naa.5000cca01aa9a96c` (3TB Hitachi SAS, `360.3 GiB` used) — initially in `DG 52e69070` (`T0`)
    - `T7` = `naa.5000cca01ab112cc` (3TB Hitachi SAS, `642.0 GiB` used) — already in `DG 520e7405` (`T1`)
    - `T8` = `naa.5000cca01aa7e5ec` (3TB Hitachi SAS, `881.8 GiB` used) — initially in `DG 52e69070` (`T0`)
    - `T9` = `naa.5000cca01ad7dc48` (3TB Hitachi SAS, `984.3 GiB` used) — already in `DG 520e7405` (`T1`)
    - `T10` = `naa.5000cca01aa800e0` (3TB Hitachi SAS, `1012.3 GiB` used) — initially in `DG 52e69070` (`T0`)
    - `T11` = `naa.50000c0f012d3630` (3TB WD SAS, `648.7 GiB` used) — already in `DG 520e7405` (`T1`)
    - `T12` = `naa.50000c0f01e0c290` (3TB WD SAS, `414.8 GiB` used) — already in `DG 520e7405` (`T1`)

---

## 0. Pre-Flight Baseline (`2026-10-17T17:08:58Z`)

### `esxi01`: `esxcli vsan cluster get` (`rc=0`)
```text
ConfigGeneration:                   11deb2da-af76-4c32-8756-f7e5e7e05301 20 2026-10-17T08:16:46.99
CurrentLocalTime:                   2026-10-17T17:08:56Z
Enabled:                            true
LocalNodeHealthState:               HEALTHY
LocalNodeState:                     MASTER
LocalNodeType:                      NORMAL
LocalNodeUUID:                      690bf025-a834-166a-7963-48df37d56620
MaintenanceModeState:               OFF
Mode:                               REGULAR
SubClusterBackupUUID:               690bed89-0b5f-39b0-71b3-48df37e0b790
SubClusterMasterUUID:               690bf025-a834-166a-7963-48df37d56620
SubClusterMemberCount:              4
SubClusterMemberHostNames:          ESXI01, ESXI02, ESXI04, ESXI03
SubClusterMemberUUIDs:              690bf025-a834-166a-7963-48df37d56620, 690bed89-0b5f-39b0-71b3-48df37e0b790, 690be3b3-a4fc-84dc-bf83-98b785225eea, 690bf3f8-a3c0-fbe0-3b20-98b78522598c
SubClusterMembershipEntryRevision:  11
SubClusterMembershipUUID:           58c9bf6a-f3b6-199f-c311-48df37d56620
SubClusterUUID:                     5221e29f-8740-f516-7273-0ae338b3eb48
UnicastModeEnabled:                 true
vSANESAEnabled:                     false
```

### `esxi01`: `esxcli vsan debug resync summary get` (`rc=0`)
```text
TotalBytesLeftToResync:         0
TotalGBLeftToResync:            0.00
TotalNumberOfResyncingObjects:  0
```

### Pre-Flight `esxi01` Disk Group Membership
| Disk Group UUID | Cache SSD | Device | Capacity Tier | IsSSD | Mounted | InCMMDS | VSAN UUID |
|---|---|---|---|---|---|---|---|
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `mpx.vmhba0:C2:T1:L0` | `false` | `true` | `true` | `true` | `520e7405-7951-d36b-972f-44fa76ee518e` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `mpx.vmhba0:C2:T4:L0` | `true` | `false` | `true` | `true` | `52a3d07a-8c93-7284-b565-f4368d4e2ee8` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `mpx.vmhba0:C2:T5:L0` | `true` | `false` | `true` | `true` | `522808b1-f93b-0bb6-e166-2a829a754e3c` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.50000c0f012d3630` | `true` | `false` | `true` | `true` | `522ac7d2-9125-2e27-d90a-e79d5b95d5ff` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.50000c0f01e0c290` | `true` | `false` | `true` | `true` | `52231164-8542-4e47-05b5-c97338472125` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01ab112cc` | `true` | `false` | `true` | `true` | `52e03231-e4ca-b44f-afd9-b65c7a5cf3e1` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01ad7dc48` | `true` | `false` | `true` | `true` | `52838d58-ec30-7adb-256c-53952dea37c4` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T0:L0` | `false` | `true` | `true` | `true` | `52e69070-da86-8f9f-27b2-679f255787a8` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T13:L0` | `true` | `false` | `true` | `true` | `52f55307-770c-ff18-3fb6-7a5efb104701` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T3:L0` | `true` | `false` | `true` | `true` | `521d0c28-1459-755a-6721-d9c20fb39333` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `naa.5000cca01aa7e5ec` | `true` | `false` | `true` | `true` | `525afbd3-9593-ee7f-fbb6-3d16a9ceaed8` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `naa.5000cca01aa800e0` | `true` | `false` | `true` | `true` | `52f3203a-8d60-ad12-eb10-a3abc205084a` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `naa.5000cca01aa9a96c` | `true` | `false` | `true` | `true` | `52eaa17b-0f80-9e1f-f973-d30a88d4d564` |

### Pre-Flight SMART Wear Baseline (`esxi01` `T0`/`T1` & `esxi02` `T8`/`T9`)
### esxi01 T0 (DG 52e69070 Cache) (`mpx.vmhba0:C2:T0:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    51     0          51     890
Write Error Count          100    10         100    0
Power-on Hours             98     0          98     9499
Power Cycle Count          99     0          99     292
Reallocated Sector Count   100    10         100    0
Drive Temperature          42     0          34     58
Write Sectors TOT Count    99     0          99     670531637741
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```
### esxi01 T1 (DG 520e7405 Cache) (`mpx.vmhba0:C2:T1:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    34     0          34     1186
Write Error Count          100    10         100    0
Power-on Hours             98     0          98     9499
Power Cycle Count          99     0          99     296
Reallocated Sector Count   100    10         100    0
Drive Temperature          45     0          34     55
Write Sectors TOT Count    99     0          99     826589452512
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```
### esxi02 T8 (DG 5240e70d Cache) (`mpx.vmhba0:C2:T8:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    24     0          24     1370
Write Error Count          100    10         100    0
Power-on Hours             98     0          98     9594
Power Cycle Count          99     0          99     307
Reallocated Sector Count   100    10         100    0
Drive Temperature          40     0          29     60
Write Sectors TOT Count    99     0          99     1020944370742
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```
### esxi02 T9 (DG 522f2b56 Cache) (`mpx.vmhba0:C2:T9:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    10     0          10     1628
Write Error Count          100    10         100    0
Power-on Hours             95     0          95     24351
Power Cycle Count          98     0          98     1485
Reallocated Sector Count   100    10         100    0
Drive Temperature          39     0          29     61
Write Sectors TOT Count    99     0          99     1219074843801
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```

---

## 1. Step A: Move `mpx.vmhba0:C2:T4:L0` (T4 (24TB Seagate Exos ST24000NT002-3N1, 751.6 GiB used))
- **Source Disk Group**: `520e7405-7951-d36b-972f-44fa76ee518e` (`mpx.vmhba0:C2:T1:L0`)
- **Target Disk Group**: `52e69070-da86-8f9f-27b2-679f255787a8` (`mpx.vmhba0:C2:T0:L0`)
- **Remove Start / End (UTC)**: `2026-10-17T19:42:27Z` -> `2026-10-17T21:22:52Z`
- **Remove Command**: `esxcli vsan storage remove -d mpx.vmhba0:C2:T4:L0 -m evacuateAllData`
- **Remove Exit Code**: `rc=1`
- **Remove Output / Stderr**: `govc: EsxCLI.CLIFault.summary <obj xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="urn:vim25" versionId="5.0" xsi:type="LocalizedMethodFault"><fault xsi:type="VimEsxCLICLIFault"><errMsg>Unable to remove device: Failed to get VsanInfo operation lock for diskOpLock, an operation is currently in progress(locked pid: 0), error: /tmp/.vsanDiskOpLock.lock.LOCK: timeout waiting for lock after 30 seconds. Lock is currently held by process 2102671 (hostd: /bin/hostd)</errMsg></fault><localizedMessage>EsxCLI.CLIFault.summary</localizedMessage></obj>`

### Evacuation Telemetry (`mpx.vmhba0:C2:T4:L0`)
- `2026-10-17T19:42:53Z`: `Objects=4`, `GBLeft=0.00` (`2048 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671606114821] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=827679794512] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021042718878] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219149424977]`
- `2026-10-17T19:43:20Z`: `Objects=4`, `GBLeft=0.00` (`2048 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671614564373] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=827682689696] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021042747846] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219149498993]`
- `2026-10-17T19:44:13Z`: `Objects=4`, `GBLeft=0.00` (`2048 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671622272693] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=827689163176] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021042785438] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219149733225]`
- `2026-10-17T19:45:06Z`: `Objects=4`, `GBLeft=0.00` (`2048 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671636805933] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=827697837880] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021042823982] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219149880777]`
- `2026-10-17T19:46:00Z`: `Objects=4`, `GBLeft=0.00` (`2048 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671648639877] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=827704944456] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021042880246] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219150054881]`
- `2026-10-17T19:46:53Z`: `Objects=4`, `GBLeft=0.00` (`2048 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671660099621] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=827711812424] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021042912702] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219150175097]`
- `2026-10-17T19:47:46Z`: `Objects=4`, `GBLeft=0.00` (`2048 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671665489725] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=827718513256] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021043102046] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219150320121]`
- `2026-10-17T20:08:45Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671789942381] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=827938005952] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021055815134] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219185318529]`
- `2026-10-17T20:09:22Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671792352637] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=827946306232] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021056536110] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219188152193]`
- `2026-10-17T20:10:25Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671798534589] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=827967925408] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021057614910] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219194698753]`
- `2026-10-17T20:11:27Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671802987037] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=827979988528] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021059007222] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219200125809]`
- `2026-10-17T20:12:29Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671806322925] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=827987878536] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021060792222] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219206337289]`
- `2026-10-17T20:13:31Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671810451637] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=827999877048] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021063336342] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219209452273]`
- `2026-10-17T20:14:33Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671815682157] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=828014323976] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021064170694] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219211378689]`
- `2026-10-17T20:16:26Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671824665365] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=828039943400] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021065049150] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219221605073]`
- `2026-10-17T20:17:29Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671830953621] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=828052828888] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021066139814] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219227421417]`
- `2026-10-17T20:18:50Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671837480213] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=828077393952] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021071481190] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219238105497]`
- `2026-10-17T20:19:28Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671839883069] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=828087829288] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021073709334] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219242609009]`
- `2026-10-17T20:20:29Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671844621941] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=828106210840] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021075708622] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219252529945]`
- `2026-10-17T20:21:32Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671850410301] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=828114861792] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021078627678] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219258844985]`
- `2026-10-17T20:22:34Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671856129397] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=828125636232] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021081339694] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219264906497]`
- `2026-10-17T20:23:36Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671863043469] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=828144496576] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021086136982] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219275172801]`
- `2026-10-17T20:24:39Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671869878285] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=828155051920] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021089327366] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219280485489]`
- `2026-10-17T20:25:41Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671876788813] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=828166325912] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021091583982] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219287350409]`
- `2026-10-17T20:26:43Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671883447397] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=828177068000] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021094637582] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219292272545]`
- `2026-10-17T20:27:45Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671890360973] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=828191392184] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021096041638] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219301985481]`
- `2026-10-17T20:28:48Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671897329797] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=828199995768] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021096098870] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219310779801]`
- `2026-10-17T20:29:50Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671904338949] | esxi01:T1[Health=OK,Wear=34(raw=1188),WrSec=828207777880] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021096155078] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219317591929]`
- `2026-10-17T20:30:52Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671909470149] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828218180064] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021098805958] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219325626265]`
- `2026-10-17T20:31:54Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671910723485] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828227597224] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021101431486] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219333904425]`
- `2026-10-17T20:32:56Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671912487277] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828236235664] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021104227622] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219340689073]`
- `2026-10-17T20:33:58Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671914247093] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828249284152] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021105407918] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219345372065]`
- `2026-10-17T20:35:00Z`: `Objects=3`, `GBLeft=0.00` (`1536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671915735573] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828260973568] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021105547982] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219350245857]`
- `2026-10-17T20:36:02Z`: `Objects=2`, `GBLeft=0.00` (`1024 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671916933437] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828267788192] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021105630374] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219354808913]`
- `2026-10-17T20:37:04Z`: `Objects=2`, `GBLeft=0.00` (`1024 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671918122293] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828274643088] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021105671422] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219361959913]`
- `2026-10-17T20:38:06Z`: `Objects=2`, `GBLeft=0.00` (`1024 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671920421061] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828281779856] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021106211662] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219368841041]`
- `2026-10-17T20:39:08Z`: `Objects=2`, `GBLeft=0.00` (`1024 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671923211885] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828290104576] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021108840254] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219377946145]`
- `2026-10-17T20:40:10Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671926333453] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828299403048] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021110012526] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219385678265]`
- `2026-10-17T20:41:12Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671930995197] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828308584584] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021112169046] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219394558177]`
- `2026-10-17T20:42:14Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671935455669] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828317111160] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021113767374] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219402696121]`
- `2026-10-17T20:43:16Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671938949797] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828328131488] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021115070518] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219409597273]`
- `2026-10-17T20:44:18Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671939908773] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828341226152] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021116575950] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219416338905]`
- `2026-10-17T20:45:19Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=892),WrSec=671943949965] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828353270744] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021120143382] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219423760185]`
- `2026-10-17T20:46:21Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671946193317] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828366403248] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021121981678] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219431284633]`
- `2026-10-17T20:47:23Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671950439437] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828381992992] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021124154590] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219437907729]`
- `2026-10-17T20:48:25Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671952834741] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828394512144] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021124331982] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219443591545]`
- `2026-10-17T20:49:27Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671954655373] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828403478256] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021124386510] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219451061881]`
- `2026-10-17T20:50:29Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671957257677] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828412858816] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021124444150] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219456150193]`
- `2026-10-17T20:51:31Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671959172469] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828422836312] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021124497318] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219461888241]`
- `2026-10-17T20:52:33Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671960734397] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828430420720] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021124614326] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219468069689]`
- `2026-10-17T20:53:35Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671962844765] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828438638336] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021124667270] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219474940945]`
- `2026-10-17T20:54:37Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671965332013] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828447160136] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021124719318] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219478303681]`
- `2026-10-17T20:55:38Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671967216301] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828458372512] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021124791950] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219482914617]`
- `2026-10-17T20:56:40Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671969907893] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828470876336] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021124876278] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219490004033]`
- `2026-10-17T20:57:42Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671971270709] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828482500144] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021124927286] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219493414337]`
- `2026-10-17T20:58:44Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671972663029] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828493466360] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021124971350] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219497951177]`
- `2026-10-17T20:59:46Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671973488869] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828500554696] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021125021486] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219504036345]`
- `2026-10-17T21:00:48Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671975323693] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828509185448] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021125122390] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219506929537]`
- `2026-10-17T21:01:49Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671978622325] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828522796144] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021125182198] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219510050009]`
- `2026-10-17T21:02:51Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671980627597] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828534036368] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021125246158] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219513670513]`
- `2026-10-17T21:03:53Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671982284645] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828542972600] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021125293574] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219517011153]`
- `2026-10-17T21:04:55Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671983791765] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828553649768] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021125337846] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219519625097]`
- `2026-10-17T21:05:57Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671985953909] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828566995856] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021125456742] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219522775697]`
- `2026-10-17T21:06:58Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671986326253] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828574444480] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021125495086] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219524884665]`
- `2026-10-17T21:08:00Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671987115469] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828581775952] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021125535174] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219529322841]`
- `2026-10-17T21:09:02Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671988753357] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828588846512] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021125572118] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219533317393]`
- `2026-10-17T21:10:04Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671990204357] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828595617888] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021125607686] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219538609321]`
- `2026-10-17T21:11:06Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671991517917] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828602255544] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021125662110] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219540782561]`
- `2026-10-17T21:12:08Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671992746573] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828608957984] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021125747910] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219541368329]`
- `2026-10-17T21:13:10Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671993568261] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828615413720] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021125798766] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219541614569]`
- `2026-10-17T21:14:12Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671994381085] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828622094064] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021125846654] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219541669761]`
- `2026-10-17T21:15:13Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671995803533] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828629267008] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021125908614] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219541731737]`
- `2026-10-17T21:16:15Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671996932789] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828638117600] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021125951182] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219541775929]`
- `2026-10-17T21:17:17Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=671999292717] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828650045856] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021126007894] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219541833961]`
- `2026-10-17T21:18:19Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672000682085] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828662532696] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021126050014] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219541913873]`
- `2026-10-17T21:19:20Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672001396677] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828672982032] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021126093158] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219541983889]`
- `2026-10-17T21:20:22Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672002129261] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828680931744] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021126140774] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219542109825]`
- `2026-10-17T21:21:24Z`: `Objects=1`, `GBLeft=0.00` (`512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672002791893] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828688700600] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021126186838] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219542569497]`
- `2026-10-17T21:22:26Z`: `Objects=0`, `GBLeft=0.00` (`0 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672004019821] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828695914530] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021126230702] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219543293105]`

### Post-Evacuation Resync Verification (`esxcli vsan debug resync summary get`, `rc=0`)
```text
TotalBytesLeftToResync:         0
TotalGBLeftToResync:            0.00
TotalNumberOfResyncingObjects:  0
```

- **Add Start / End (UTC)**: `2026-10-17T21:22:53Z` -> `2026-10-17T21:23:24Z`
- **Add Command**: `esxcli vsan storage add -s mpx.vmhba0:C2:T0:L0 -d mpx.vmhba0:C2:T4:L0`
- **Add Exit Code**: `rc=0`
- **Add Output / Stderr**: `true`
- **Post-Add Disk Verification**:
  - `Device`: `mpx.vmhba0:C2:T4:L0`
  - `VSANDiskGroupName`: `mpx.vmhba0:C2:T0:L0`
  - `VSANDiskGroupUUID`: `52e69070-da86-8f9f-27b2-679f255787a8`
  - `VSANUUID`: `52521303-9b87-0c61-4f80-10bf7d453fe4`
  - `IsMounted`: `true`
  - `InCMMDS`: `true`

### Post-Step A Resync Summary (`rc=0`)
```text
TotalBytesLeftToResync:         0
TotalGBLeftToResync:            0.00
TotalNumberOfResyncingObjects:  0
```

### Post-Step A SMART Wear Check (`esxi01` `T0`/`T1` & `esxi02` `T8`/`T9`)
### esxi01 T0 (DG 52e69070 Cache) (`mpx.vmhba0:C2:T0:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    50     0          50     893
Write Error Count          100    10         100    0
Power-on Hours             98     0          98     9503
Power Cycle Count          99     0          99     293
Reallocated Sector Count   100    10         100    0
Drive Temperature          46     0          34     54
Write Sectors TOT Count    99     0          99     672006530094
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```
### esxi01 T1 (DG 520e7405 Cache) (`mpx.vmhba0:C2:T1:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    34     0          34     1189
Write Error Count          100    10         100    0
Power-on Hours             98     0          98     9503
Power Cycle Count          99     0          99     297
Reallocated Sector Count   100    10         100    0
Drive Temperature          42     0          34     58
Write Sectors TOT Count    99     0          99     828698682586
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```
### esxi02 T8 (DG 5240e70d Cache) (`mpx.vmhba0:C2:T8:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    24     0          24     1371
Write Error Count          100    10         100    0
Power-on Hours             98     0          98     9598
Power Cycle Count          99     0          99     307
Reallocated Sector Count   100    10         100    0
Drive Temperature          36     0          29     64
Write Sectors TOT Count    99     0          99     1021126275974
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```
### esxi02 T9 (DG 522f2b56 Cache) (`mpx.vmhba0:C2:T9:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    10     0          10     1629
Write Error Count          100    10         100    0
Power-on Hours             95     0          95     24355
Power Cycle Count          98     0          98     1485
Reallocated Sector Count   100    10         100    0
Drive Temperature          34     0          29     66
Write Sectors TOT Count    99     0          99     1219544395865
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```

### Post-Step A `esxi01` Disk Group Layout
| Disk Group UUID | Cache SSD | Device | Capacity Tier | IsSSD | Mounted | InCMMDS | VSAN UUID |
|---|---|---|---|---|---|---|---|
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `mpx.vmhba0:C2:T1:L0` | `false` | `true` | `true` | `true` | `520e7405-7951-d36b-972f-44fa76ee518e` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `mpx.vmhba0:C2:T5:L0` | `true` | `false` | `true` | `true` | `522808b1-f93b-0bb6-e166-2a829a754e3c` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.50000c0f012d3630` | `true` | `false` | `true` | `true` | `522ac7d2-9125-2e27-d90a-e79d5b95d5ff` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.50000c0f01e0c290` | `true` | `false` | `true` | `true` | `52231164-8542-4e47-05b5-c97338472125` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01ab112cc` | `true` | `false` | `true` | `true` | `52e03231-e4ca-b44f-afd9-b65c7a5cf3e1` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01ad7dc48` | `true` | `false` | `true` | `true` | `52838d58-ec30-7adb-256c-53952dea37c4` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T0:L0` | `false` | `true` | `true` | `true` | `52e69070-da86-8f9f-27b2-679f255787a8` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T13:L0` | `true` | `false` | `true` | `true` | `52f55307-770c-ff18-3fb6-7a5efb104701` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T3:L0` | `true` | `false` | `true` | `true` | `521d0c28-1459-755a-6721-d9c20fb39333` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T4:L0` | `true` | `false` | `true` | `true` | `52521303-9b87-0c61-4f80-10bf7d453fe4` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `naa.5000cca01aa7e5ec` | `true` | `false` | `true` | `true` | `525afbd3-9593-ee7f-fbb6-3d16a9ceaed8` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `naa.5000cca01aa800e0` | `true` | `false` | `true` | `true` | `52f3203a-8d60-ad12-eb10-a3abc205084a` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `naa.5000cca01aa9a96c` | `true` | `false` | `true` | `true` | `52eaa17b-0f80-9e1f-f973-d30a88d4d564` |

---

## 2. Step B1: Move `naa.5000cca01aa9a96c` (T6 (3TB Hitachi SAS HUS723030ALS640, 360.3 GiB used))
- **Source Disk Group**: `52e69070-da86-8f9f-27b2-679f255787a8` (`mpx.vmhba0:C2:T0:L0`)
- **Target Disk Group**: `520e7405-7951-d36b-972f-44fa76ee518e` (`mpx.vmhba0:C2:T1:L0`)
- **Remove Start / End (UTC)**: `2026-10-17T21:23:31Z` -> `2026-10-17T22:26:43Z`
- **Remove Command**: `esxcli vsan storage remove -d naa.5000cca01aa9a96c -m evacuateAllData`
- **Remove Exit Code**: `rc=1`
- **Remove Output / Stderr**: `govc: EsxCLI.CLIFault.summary <obj xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="urn:vim25" versionId="5.0" xsi:type="LocalizedMethodFault"><fault xsi:type="VimEsxCLICLIFault"><errMsg>Unable to remove device: Disk not found: naa.5000cca01aa9a96c</errMsg></fault><localizedMessage>EsxCLI.CLIFault.summary</localizedMessage></obj>`

### Evacuation Telemetry (`naa.5000cca01aa9a96c`)
- `2026-10-17T21:23:57Z`: `Objects=14`, `GBLeft=330.19` (`354542555136 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672009739878] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828699756346] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021126322942] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219544832145]`
- `2026-10-17T21:24:24Z`: `Objects=11`, `GBLeft=328.13` (`352323399680 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672012381982] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828701093930] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021126335198] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219545335185]`
- `2026-10-17T21:25:17Z`: `Objects=10`, `GBLeft=324.65` (`348589404160 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672017755382] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828703953562] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021126382758] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219546471577]`
- `2026-10-17T21:26:10Z`: `Objects=10`, `GBLeft=321.29` (`344983527424 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672022764646] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828706667362] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021126427430] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219547766913]`
- `2026-10-17T21:27:03Z`: `Objects=8`, `GBLeft=318.29` (`341756243968 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672028499662] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828709801370] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021126742566] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219549089657]`
- `2026-10-17T21:27:56Z`: `Objects=8`, `GBLeft=313.81` (`336953950208 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672034371758] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828713036586] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021126907286] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219550085697]`
- `2026-10-17T21:28:48Z`: `Objects=7`, `GBLeft=309.75` (`332596158464 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672039988070] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828716885082] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021126937862] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219550935913]`
- `2026-10-17T21:29:41Z`: `Objects=7`, `GBLeft=306.01` (`328580493312 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672047190486] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828721526858] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021128012046] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219551882281]`
- `2026-10-17T21:30:34Z`: `Objects=7`, `GBLeft=301.58` (`323819880448 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672052104886] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828726426802] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021128201206] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219552710937]`
- `2026-10-17T21:31:27Z`: `Objects=7`, `GBLeft=297.56` (`319504138240 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672056868526] | esxi01:T1[Health=OK,Wear=34(raw=1189),WrSec=828731094018] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021128233150] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219553474841]`
- `2026-10-17T21:32:21Z`: `Objects=7`, `GBLeft=293.18` (`314804375552 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672061981598] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828737122594] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021130460086] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219554548033]`
- `2026-10-17T21:33:14Z`: `Objects=7`, `GBLeft=288.80` (`310099611648 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672067493614] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828743606178] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021130498742] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219557513489]`
- `2026-10-17T21:34:07Z`: `Objects=6`, `GBLeft=284.36` (`305330241536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672073038430] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828764799506] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021130531126] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219558479337]`
- `2026-10-17T21:35:00Z`: `Objects=6`, `GBLeft=279.84` (`300477456384 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672080405790] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828781328090] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021130580342] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219559571417]`
- `2026-10-17T21:35:54Z`: `Objects=5`, `GBLeft=274.56` (`294803722240 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672087140390] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828789525154] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021130630766] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219559886201]`
- `2026-10-17T21:36:47Z`: `Objects=5`, `GBLeft=269.69` (`289582174208 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672093408982] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828794361818] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021130661806] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219560441825]`
- `2026-10-17T21:37:40Z`: `Objects=5`, `GBLeft=264.90` (`284428845056 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672099492390] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828798418842] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021134266382] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219562438993]`
- `2026-10-17T21:38:33Z`: `Objects=5`, `GBLeft=260.24` (`279426080768 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672105806022] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828802738906] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021140645502] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219569939521]`
- `2026-10-17T21:39:25Z`: `Objects=5`, `GBLeft=254.50` (`273269112832 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672111806342] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828807444258] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021140746670] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219570405025]`
- `2026-10-17T21:40:18Z`: `Objects=5`, `GBLeft=249.55` (`267956637696 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672117729430] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828812061202] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021140787350] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219570710449]`
- `2026-10-17T21:41:11Z`: `Objects=5`, `GBLeft=245.16` (`263238303744 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672123351502] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828817408746] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021140865598] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219570902065]`
- `2026-10-17T21:42:04Z`: `Objects=5`, `GBLeft=241.05` (`258828484608 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672129164430] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828822142642] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021140977102] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219571010937]`
- `2026-10-17T21:42:57Z`: `Objects=5`, `GBLeft=235.80` (`253193228288 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672135654694] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828827164490] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021141123366] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219571558329]`
- `2026-10-17T21:43:50Z`: `Objects=5`, `GBLeft=231.22` (`248272003072 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672142165406] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828831663866] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021141260518] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219572159481]`
- `2026-10-17T21:44:42Z`: `Objects=5`, `GBLeft=226.93` (`243659079680 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672149853542] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828837851130] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021141475382] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219572287289]`
- `2026-10-17T21:45:35Z`: `Objects=5`, `GBLeft=222.85` (`239287586816 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672156527798] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828844340450] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021141611798] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219573025049]`
- `2026-10-17T21:46:29Z`: `Objects=5`, `GBLeft=217.68` (`233727668224 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672163237046] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828852907058] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021150263510] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219573272129]`
- `2026-10-17T21:47:22Z`: `Objects=5`, `GBLeft=214.03` (`229814910976 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672170330262] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828861410610] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021150407246] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219573513625]`
- `2026-10-17T21:48:14Z`: `Objects=5`, `GBLeft=209.61` (`225063337984 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672176530542] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828867798858] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021150533582] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219573559665]`
- `2026-10-17T21:49:07Z`: `Objects=5`, `GBLeft=204.03` (`219072253952 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672182575646] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828871446434] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021150568918] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219573914113]`
- `2026-10-17T21:50:00Z`: `Objects=5`, `GBLeft=199.50` (`214209388544 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672187010190] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828875029914] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021150693590] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219574099345]`
- `2026-10-17T21:50:53Z`: `Objects=5`, `GBLeft=194.58` (`208924659712 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672191227718] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828877794954] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021150820654] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219574198081]`
- `2026-10-17T21:51:46Z`: `Objects=5`, `GBLeft=189.84` (`203841753088 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672195227286] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828880574994] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021150850958] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219574225945]`
- `2026-10-17T21:52:40Z`: `Objects=5`, `GBLeft=184.86` (`198489686016 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672199389286] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828883030338] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021150919654] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219574355977]`
- `2026-10-17T21:54:08Z`: `Objects=5`, `GBLeft=176.59` (`189609029632 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672206600534] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828887880338] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021150969878] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219574481841]`
- `2026-10-17T21:55:10Z`: `Objects=5`, `GBLeft=170.94` (`183550042112 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672211983598] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828891121906] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021151051438] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219576062993]`
- `2026-10-17T21:56:11Z`: `Objects=5`, `GBLeft=165.47` (`177669722112 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672216755790] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828894623626] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021151106630] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219576450625]`
- `2026-10-17T21:57:13Z`: `Objects=4`, `GBLeft=159.76` (`171538345984 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672222322542] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828898041802] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021151137142] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219580547473]`
- `2026-10-17T21:58:15Z`: `Objects=4`, `GBLeft=154.22` (`165588987904 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672227991990] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828901376290] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021151186190] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219581382897]`
- `2026-10-17T21:59:19Z`: `Objects=4`, `GBLeft=147.79` (`158685097984 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672234144038] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828913836490] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021151217822] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219581551857]`
- `2026-10-17T22:00:21Z`: `Objects=4`, `GBLeft=141.70` (`152146575360 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672240604846] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828921454458] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021151258262] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219581705449]`
- `2026-10-17T22:01:23Z`: `Objects=4`, `GBLeft=136.10` (`146135285760 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672247198958] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828929226162] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021151298654] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219581840585]`
- `2026-10-17T22:02:25Z`: `Objects=4`, `GBLeft=130.42` (`140039913472 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672253580230] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828936356322] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021153389510] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219582312153]`
- `2026-10-17T22:03:27Z`: `Objects=4`, `GBLeft=124.91` (`134118264832 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672260210294] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828943622914] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021156391238] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219586776889]`
- `2026-10-17T22:04:31Z`: `Objects=4`, `GBLeft=118.65` (`127400497152 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672267066390] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828951096210] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021156433798] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219594312401]`
- `2026-10-17T22:05:33Z`: `Objects=4`, `GBLeft=113.12` (`121462935552 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672273640590] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828957764570] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021160335710] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219608982729]`
- `2026-10-17T22:06:35Z`: `Objects=4`, `GBLeft=107.48` (`115402559488 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672279199030] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828961040522] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021163746990] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219628661841]`
- `2026-10-17T22:07:37Z`: `Objects=4`, `GBLeft=101.89` (`109406035968 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672284514662] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828964386730] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021163779886] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219635989721]`
- `2026-10-17T22:08:43Z`: `Objects=4`, `GBLeft=95.88` (`102955728896 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672289562454] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828967400578] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021163822350] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219636162361]`
- `2026-10-17T22:09:45Z`: `Objects=4`, `GBLeft=91.03` (`97746436096 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672294545694] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828970399050] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021163855766] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219636342417]`
- `2026-10-17T22:10:46Z`: `Objects=4`, `GBLeft=85.51` (`91819982848 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672300002526] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828973590346] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021163900262] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219636399001]`
- `2026-10-17T22:11:48Z`: `Objects=4`, `GBLeft=80.29` (`86209077248 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672305089974] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828976603722] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021163929894] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219636441625]`
- `2026-10-17T22:12:50Z`: `Objects=4`, `GBLeft=74.70` (`80209805312 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672309752350] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828979649906] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021163964222] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219636479577]`
- `2026-10-17T22:13:52Z`: `Objects=4`, `GBLeft=69.13` (`74228240384 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672315277638] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828982924122] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021163995342] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219636530809]`
- `2026-10-17T22:14:53Z`: `Objects=4`, `GBLeft=63.45` (`68133048320 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672321307830] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828986417594] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021164027118] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219636585321]`
- `2026-10-17T22:15:55Z`: `Objects=4`, `GBLeft=57.69` (`61946036224 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672328327214] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828990465290] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021164072270] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219636633305]`
- `2026-10-17T22:16:57Z`: `Objects=4`, `GBLeft=52.44` (`56304824320 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672334416270] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828994135394] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021164103302] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219636767161]`
- `2026-10-17T22:17:58Z`: `Objects=3`, `GBLeft=46.62` (`50055680000 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672339379726] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=828999542106] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021164156446] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219636824281]`
- `2026-10-17T22:19:00Z`: `Objects=2`, `GBLeft=39.72` (`42654081024 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672341994734] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829006310322] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021164186390] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219636863777]`
- `2026-10-17T22:20:02Z`: `Objects=2`, `GBLeft=34.06` (`36572930048 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672342074126] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829014207578] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021164216838] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219636921905]`
- `2026-10-17T22:21:03Z`: `Objects=2`, `GBLeft=28.41` (`30509297664 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672342175558] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829021983714] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021164259942] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219636968905]`
- `2026-10-17T22:22:06Z`: `Objects=2`, `GBLeft=22.48` (`24132820992 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672342260038] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829030145346] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021164292086] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219637039305]`
- `2026-10-17T22:23:08Z`: `Objects=2`, `GBLeft=17.24` (`18510852096 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672342356446] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829037841450] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021164735614] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219637299153]`
- `2026-10-17T22:24:13Z`: `Objects=2`, `GBLeft=11.36` (`12202487808 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672342446902] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829046328674] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021181674046] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219637430873]`
- `2026-10-17T22:25:15Z`: `Objects=2`, `GBLeft=5.20` (`5579210752 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672342588558] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829054220354] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021182591022] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219637734001]`
- `2026-10-17T22:26:17Z`: `Objects=0`, `GBLeft=0.00` (`0 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672342692160] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829056834514] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021182626734] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219637873489]`

### Post-Evacuation Resync Verification (`esxcli vsan debug resync summary get`, `rc=0`)
```text
TotalBytesLeftToResync:         0
TotalGBLeftToResync:            0.00
TotalNumberOfResyncingObjects:  0
```

- **Add Start / End (UTC)**: `2026-10-17T22:26:44Z` -> `2026-10-17T22:27:12Z`
- **Add Command**: `esxcli vsan storage add -s mpx.vmhba0:C2:T1:L0 -d naa.5000cca01aa9a96c`
- **Add Exit Code**: `rc=0`
- **Add Output / Stderr**: `true`
- **Post-Add Disk Verification**:
  - `Device`: `naa.5000cca01aa9a96c`
  - `VSANDiskGroupName`: `mpx.vmhba0:C2:T1:L0`
  - `VSANDiskGroupUUID`: `520e7405-7951-d36b-972f-44fa76ee518e`
  - `VSANUUID`: `52191fd7-997a-a5a6-c65f-0b325a677220`
  - `IsMounted`: `true`
  - `InCMMDS`: `true`

### Post-Step B1 Resync Summary (`rc=0`)
```text
TotalBytesLeftToResync:         0
TotalGBLeftToResync:            0.00
TotalNumberOfResyncingObjects:  0
```

### Post-Step B1 SMART Wear Check (`esxi01` `T0`/`T1` & `esxi02` `T8`/`T9`)
### esxi01 T0 (DG 52e69070 Cache) (`mpx.vmhba0:C2:T0:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    50     0          50     893
Write Error Count          100    10         100    0
Power-on Hours             98     0          98     9504
Power Cycle Count          99     0          99     293
Reallocated Sector Count   100    10         100    0
Drive Temperature          48     0          34     52
Write Sectors TOT Count    99     0          99     672342776056
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```
### esxi01 T1 (DG 520e7405 Cache) (`mpx.vmhba0:C2:T1:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    34     0          34     1190
Write Error Count          100    10         100    0
Power-on Hours             98     0          98     9504
Power Cycle Count          99     0          99     297
Reallocated Sector Count   100    10         100    0
Drive Temperature          46     0          34     54
Write Sectors TOT Count    99     0          99     829056979227
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```
### esxi02 T8 (DG 5240e70d Cache) (`mpx.vmhba0:C2:T8:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    24     0          24     1371
Write Error Count          100    10         100    0
Power-on Hours             98     0          98     9599
Power Cycle Count          99     0          99     307
Reallocated Sector Count   100    10         100    0
Drive Temperature          41     0          29     59
Write Sectors TOT Count    99     0          99     1021182661182
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```
### esxi02 T9 (DG 522f2b56 Cache) (`mpx.vmhba0:C2:T9:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    10     0          10     1629
Write Error Count          100    10         100    0
Power-on Hours             95     0          95     24356
Power Cycle Count          98     0          98     1485
Reallocated Sector Count   100    10         100    0
Drive Temperature          33     0          29     67
Write Sectors TOT Count    99     0          99     1219656000745
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```

### Post-Step B1 `esxi01` Disk Group Layout
| Disk Group UUID | Cache SSD | Device | Capacity Tier | IsSSD | Mounted | InCMMDS | VSAN UUID |
|---|---|---|---|---|---|---|---|
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `mpx.vmhba0:C2:T1:L0` | `false` | `true` | `true` | `true` | `520e7405-7951-d36b-972f-44fa76ee518e` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `mpx.vmhba0:C2:T5:L0` | `true` | `false` | `true` | `true` | `522808b1-f93b-0bb6-e166-2a829a754e3c` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.50000c0f012d3630` | `true` | `false` | `true` | `true` | `522ac7d2-9125-2e27-d90a-e79d5b95d5ff` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.50000c0f01e0c290` | `true` | `false` | `true` | `true` | `52231164-8542-4e47-05b5-c97338472125` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01aa9a96c` | `true` | `false` | `true` | `true` | `52191fd7-997a-a5a6-c65f-0b325a677220` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01ab112cc` | `true` | `false` | `true` | `true` | `52e03231-e4ca-b44f-afd9-b65c7a5cf3e1` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01ad7dc48` | `true` | `false` | `true` | `true` | `52838d58-ec30-7adb-256c-53952dea37c4` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T0:L0` | `false` | `true` | `true` | `true` | `52e69070-da86-8f9f-27b2-679f255787a8` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T13:L0` | `true` | `false` | `true` | `true` | `52f55307-770c-ff18-3fb6-7a5efb104701` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T3:L0` | `true` | `false` | `true` | `true` | `521d0c28-1459-755a-6721-d9c20fb39333` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T4:L0` | `true` | `false` | `true` | `true` | `52521303-9b87-0c61-4f80-10bf7d453fe4` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `naa.5000cca01aa7e5ec` | `true` | `false` | `true` | `true` | `525afbd3-9593-ee7f-fbb6-3d16a9ceaed8` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `naa.5000cca01aa800e0` | `true` | `false` | `true` | `true` | `52f3203a-8d60-ad12-eb10-a3abc205084a` |

---

## 3. Step B2: Move `naa.5000cca01aa7e5ec` (T8 (3TB Hitachi SAS HUS723030ALS640, 881.8 GiB used))
- **Source Disk Group**: `52e69070-da86-8f9f-27b2-679f255787a8` (`mpx.vmhba0:C2:T0:L0`)
- **Target Disk Group**: `520e7405-7951-d36b-972f-44fa76ee518e` (`mpx.vmhba0:C2:T1:L0`)
- **Remove Start / End (UTC)**: `2026-10-17T22:27:19Z` -> `2026-10-18T04:42:47Z`
- **Remove Command**: `esxcli vsan storage remove -d naa.5000cca01aa7e5ec -m evacuateAllData`
- **Remove Exit Code**: `rc=1`
- **Remove Output / Stderr**: `govc: EsxCLI.CLIFault.summary <obj xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="urn:vim25" versionId="5.0" xsi:type="LocalizedMethodFault"><fault xsi:type="VimEsxCLICLIFault"><errMsg>Unable to remove device: Disk not found: naa.5000cca01aa7e5ec</errMsg></fault><localizedMessage>EsxCLI.CLIFault.summary</localizedMessage></obj>`

### Evacuation Telemetry (`naa.5000cca01aa7e5ec`)
- `2026-10-17T22:27:46Z`: `Objects=9`, `GBLeft=819.88` (`880341307392 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672345227456] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829058229475] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021182703238] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219665125801]`
- `2026-10-17T22:28:12Z`: `Objects=8`, `GBLeft=817.71` (`878005166080 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672347855248] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829059504227] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021182717078] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219667071761]`
- `2026-10-17T22:29:06Z`: `Objects=7`, `GBLeft=813.32` (`873301000192 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672353916744] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829062437187] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021182741326] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219691493633]`
- `2026-10-17T22:29:59Z`: `Objects=7`, `GBLeft=808.43` (`868046581760 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672359907888] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829065544043] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021182769398] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219709840265]`
- `2026-10-17T22:30:52Z`: `Objects=7`, `GBLeft=805.45` (`864850313216 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672365575992] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829068434259] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021182808606] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219717238505]`
- `2026-10-17T22:31:44Z`: `Objects=7`, `GBLeft=801.55` (`860652879872 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672371238424] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829071332243] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021182836190] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219729038849]`
- `2026-10-17T22:32:37Z`: `Objects=7`, `GBLeft=797.03` (`855801368576 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672376864784] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829074205435] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021182860198] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219741504513]`
- `2026-10-17T22:33:30Z`: `Objects=7`, `GBLeft=792.81` (`851278622720 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672381988272] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829076845427] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021182889638] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219755559353]`
- `2026-10-17T22:34:23Z`: `Objects=7`, `GBLeft=789.97` (`848220033024 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672387592512] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829079711387] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021182915374] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219765652649]`
- `2026-10-17T22:35:16Z`: `Objects=7`, `GBLeft=786.03` (`843991588864 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672393228896] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829082636395] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021182958926] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219775442089]`
- `2026-10-17T22:36:09Z`: `Objects=7`, `GBLeft=781.48` (`839109390336 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672398861320] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829085535667] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021182993126] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219786298329]`
- `2026-10-17T22:37:01Z`: `Objects=7`, `GBLeft=776.98` (`834273189888 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672404496456] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829088752827] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021183020934] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219794493585]`
- `2026-10-17T22:37:54Z`: `Objects=7`, `GBLeft=773.54` (`830583701504 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672409967848] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829092240611] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021183055262] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219804002249]`
- `2026-10-17T22:38:47Z`: `Objects=6`, `GBLeft=769.58` (`826325860352 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672415163144] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829097221059] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021183084654] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219832568433]`
- `2026-10-17T22:39:40Z`: `Objects=6`, `GBLeft=763.79` (`820114759680 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672420344176] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829102368851] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021183111838] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219841649865]`
- `2026-10-17T22:40:33Z`: `Objects=6`, `GBLeft=758.19` (`814098698240 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672425755240] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829107937843] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021183149238] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219856331769]`
- `2026-10-17T22:41:26Z`: `Objects=6`, `GBLeft=754.01` (`809607045120 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672431283776] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829119883307] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021183182254] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219875042905]`
- `2026-10-17T22:42:19Z`: `Objects=6`, `GBLeft=750.33` (`805656530944 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672436548184] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829125293835] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021183210838] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219887426537]`
- `2026-10-17T22:43:12Z`: `Objects=6`, `GBLeft=744.82` (`799743938560 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672442653560] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829130279635] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021185411454] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219895718561]`
- `2026-10-17T22:44:04Z`: `Objects=6`, `GBLeft=739.59` (`794132774912 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672448047408] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829135090163] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021201846622] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219900842233]`
- `2026-10-17T22:44:58Z`: `Objects=6`, `GBLeft=735.60` (`789839958016 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672455154776] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829143735515] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021201891286] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219913524025]`
- `2026-10-17T22:45:51Z`: `Objects=6`, `GBLeft=732.25` (`786246287360 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672463148248] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829155094035] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021201957718] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219922526969]`
- `2026-10-17T22:46:44Z`: `Objects=6`, `GBLeft=727.23` (`780861558784 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672469828440] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829164358211] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021202018470] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219929202225]`
- `2026-10-17T22:47:37Z`: `Objects=6`, `GBLeft=721.56` (`774764765184 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672475088320] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829169994851] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021204168950] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219938360825]`
- `2026-10-17T22:48:30Z`: `Objects=6`, `GBLeft=717.44` (`770341564416 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672479920736] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829174654283] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021219180686] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219947388753]`
- `2026-10-17T22:49:22Z`: `Objects=6`, `GBLeft=713.80` (`766433607680 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672484641920] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829181357283] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021223324886] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219964713369]`
- `2026-10-17T22:50:15Z`: `Objects=6`, `GBLeft=708.73` (`760997445632 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672489744616] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829200228051] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021223365070] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1219982964833]`
- `2026-10-17T22:51:08Z`: `Objects=6`, `GBLeft=703.54` (`755419238400 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672494735232] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829207425955] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021223392902] | esxi02:T9[Health=OK,Wear=10(raw=1629),WrSec=1220003035713]`
- `2026-10-17T22:52:01Z`: `Objects=6`, `GBLeft=699.55` (`751133184000 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672499447936] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829212010699] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021223420286] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220019327665]`
- `2026-10-17T22:52:54Z`: `Objects=6`, `GBLeft=695.80` (`747111788544 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672504155896] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829216626379] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021225655958] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220039595625]`
- `2026-10-17T22:53:47Z`: `Objects=6`, `GBLeft=691.30` (`742272917504 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672508228832] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829220794459] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021238796622] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220055764553]`
- `2026-10-17T22:54:40Z`: `Objects=6`, `GBLeft=686.50` (`737119969280 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672512642392] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829225279771] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021238836846] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220069000097]`
- `2026-10-17T22:55:33Z`: `Objects=5`, `GBLeft=682.05` (`732342509568 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672516860448] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829231292571] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021238883966] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220082746529]`
- `2026-10-17T22:56:26Z`: `Objects=5`, `GBLeft=677.47` (`727432601600 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672524046640] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829240725699] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021238921870] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220097289617]`
- `2026-10-17T22:57:18Z`: `Objects=5`, `GBLeft=672.99` (`722619637760 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672531816592] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829250182155] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021238961214] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220113716769]`
- `2026-10-17T22:58:21Z`: `Objects=5`, `GBLeft=667.48` (`716698935296 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672539336120] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829258522723] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021239010638] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220133569033]`
- `2026-10-17T22:59:23Z`: `Objects=4`, `GBLeft=662.05` (`710873989120 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672547595104] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829265582243] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021239042878] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220151858857]`
- `2026-10-17T23:00:25Z`: `Objects=4`, `GBLeft=657.05` (`705496760320 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672557432880] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829274162483] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021241444294] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220170039025]`
- `2026-10-17T23:01:27Z`: `Objects=4`, `GBLeft=650.49` (`698454646784 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672566019568] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829286684627] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021261345862] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220191107649]`
- `2026-10-17T23:02:29Z`: `Objects=4`, `GBLeft=644.82` (`692372500480 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672573931576] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829296187667] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021264709398] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220208013961]`
- `2026-10-17T23:03:31Z`: `Objects=4`, `GBLeft=639.38` (`686533636096 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672584553584] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829306434499] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021264765566] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220229767953]`
- `2026-10-17T23:04:34Z`: `Objects=4`, `GBLeft=634.02` (`680777084928 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672596187448] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829313695267] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021264799062] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220252232337]`
- `2026-10-17T23:05:36Z`: `Objects=4`, `GBLeft=629.74` (`676179865600 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672603224984] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829320733267] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021264848790] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220269775369]`
- `2026-10-17T23:06:38Z`: `Objects=4`, `GBLeft=625.40` (`671518765056 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672610412120] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829330982403] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021264881526] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220286820449]`
- `2026-10-17T23:07:40Z`: `Objects=4`, `GBLeft=620.43` (`666182856704 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672618561792] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829347104859] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021264918158] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220297623369]`
- `2026-10-17T23:08:42Z`: `Objects=4`, `GBLeft=614.98` (`660331782144 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672625960640] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829354060163] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021267458318] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220318706137]`
- `2026-10-17T23:09:44Z`: `Objects=4`, `GBLeft=609.42` (`654364401664 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672631770104] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829362602915] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021282237486] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220339116297]`
- `2026-10-17T23:10:46Z`: `Objects=4`, `GBLeft=603.83` (`648353468416 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672638187152] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829371065379] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021286127566] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220355719425]`
- `2026-10-17T23:11:47Z`: `Objects=4`, `GBLeft=598.12` (`642226835456 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672644211784] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829379561387] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021287897470] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220371265193]`
- `2026-10-17T23:12:49Z`: `Objects=4`, `GBLeft=592.48` (`636174647296 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672650181264] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829388014739] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021289039222] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220386293881]`
- `2026-10-17T23:13:51Z`: `Objects=4`, `GBLeft=587.63` (`630962749440 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672655216048] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829395484251] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021289515110] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220400401129]`
- `2026-10-17T23:14:53Z`: `Objects=4`, `GBLeft=582.03` (`624946302976 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672661032264] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829403868243] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021290106246] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220413869425]`
- `2026-10-17T23:15:55Z`: `Objects=4`, `GBLeft=576.56` (`619079024640 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672666715336] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829412195515] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021290724622] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220429203481]`
- `2026-10-17T23:16:57Z`: `Objects=4`, `GBLeft=571.23` (`613354315776 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672672403360] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829419738907] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021299501110] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220448696457]`
- `2026-10-17T23:17:59Z`: `Objects=4`, `GBLeft=565.98` (`607713988608 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672678328200] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829428261667] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021312149134] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220464808233]`
- `2026-10-17T23:19:01Z`: `Objects=3`, `GBLeft=559.56` (`600828186624 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672685449952] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829441685907] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021312231958] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220478592673]`
- `2026-10-17T23:20:03Z`: `Objects=3`, `GBLeft=554.25` (`595121033216 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672688914048] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829450347491] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021312420830] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220496484993]`
- `2026-10-17T23:21:05Z`: `Objects=3`, `GBLeft=549.18` (`589677576192 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672693854928] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829458451251] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021313013542] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220511197657]`
- `2026-10-17T23:22:07Z`: `Objects=2`, `GBLeft=544.13` (`584254742528 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672697090008] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829468712419] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021313480190] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220530612273]`
- `2026-10-17T23:23:09Z`: `Objects=2`, `GBLeft=539.84` (`579644612608 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672697239312] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829478492651] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021314078662] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220544089649]`
- `2026-10-17T23:24:11Z`: `Objects=2`, `GBLeft=534.26` (`573653393408 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672697479624] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829491197763] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021314528182] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220559068625]`
- `2026-10-17T23:25:13Z`: `Objects=2`, `GBLeft=530.05` (`569139277824 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672697705032] | esxi01:T1[Health=OK,Wear=34(raw=1190),WrSec=829501557523] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021314713646] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220574873881]`
- `2026-10-17T23:26:15Z`: `Objects=2`, `GBLeft=524.87` (`563572535296 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672697856752] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829514607355] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021315057390] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220589748449]`
- `2026-10-17T23:27:17Z`: `Objects=2`, `GBLeft=519.48` (`557791297536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672697987736] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829528694707] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021315813710] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220613921585]`
- `2026-10-17T23:28:19Z`: `Objects=2`, `GBLeft=514.73` (`552691466240 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672698092376] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829538802979] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021316591094] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220633236649]`
- `2026-10-17T23:29:21Z`: `Objects=2`, `GBLeft=510.09` (`547707174912 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672698179000] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829552730291] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021317422470] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220646248825]`
- `2026-10-17T23:30:23Z`: `Objects=2`, `GBLeft=505.14` (`542389284864 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672698290264] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829563984811] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021318087446] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220660925089]`
- `2026-10-17T23:31:26Z`: `Objects=2`, `GBLeft=500.41` (`537307197440 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672698440184] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829576655883] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021318900358] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220674905865]`
- `2026-10-17T23:32:28Z`: `Objects=2`, `GBLeft=495.75` (`532312133632 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672700369632] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829589030115] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021320193214] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220685550785]`
- `2026-10-17T23:33:30Z`: `Objects=2`, `GBLeft=490.90` (`527099736064 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672700677712] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829607470115] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021320564174] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220703062049]`
- `2026-10-17T23:34:32Z`: `Objects=2`, `GBLeft=486.18` (`522033979392 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672701044984] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829635368379] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021321197894] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220717927993]`
- `2026-10-17T23:35:34Z`: `Objects=2`, `GBLeft=482.66` (`518256332800 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672701317768] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829660926971] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021321843334] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220736728289]`
- `2026-10-17T23:36:35Z`: `Objects=2`, `GBLeft=477.20` (`512388657152 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672701515168] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829674015587] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021322427654] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220757630985]`
- `2026-10-17T23:37:38Z`: `Objects=2`, `GBLeft=471.64` (`506420359168 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672701737144] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829687747123] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021322873374] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220783167625]`
- `2026-10-17T23:38:40Z`: `Objects=2`, `GBLeft=466.13` (`500507926528 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672702655776] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829702392451] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021323333702] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220794607313]`
- `2026-10-17T23:39:42Z`: `Objects=2`, `GBLeft=460.57` (`494537744384 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672702884696] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829715752459] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021323647478] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220814837089]`
- `2026-10-17T23:40:44Z`: `Objects=2`, `GBLeft=454.39` (`487896866816 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672704465696] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829729680339] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021323747166] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220837423841]`
- `2026-10-17T23:41:46Z`: `Objects=2`, `GBLeft=449.76` (`482924056576 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672707716608] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829742802763] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021334627486] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220855747425]`
- `2026-10-17T23:42:48Z`: `Objects=2`, `GBLeft=445.75` (`478617739264 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672711129712] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829754470339] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021344757366] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220874096025]`
- `2026-10-17T23:43:51Z`: `Objects=2`, `GBLeft=441.94` (`474530263040 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672714951120] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829767965171] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021344913894] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220891343585]`
- `2026-10-17T23:44:53Z`: `Objects=2`, `GBLeft=438.38` (`470701592576 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672719016464] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829778319595] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021344967678] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220908723089]`
- `2026-10-17T23:45:55Z`: `Objects=2`, `GBLeft=433.77` (`465758511104 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672722777640] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829792479235] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021345049630] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220926633865]`
- `2026-10-17T23:46:57Z`: `Objects=2`, `GBLeft=428.96` (`460590116864 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893),WrSec=672725516320] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829805211435] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021345167022] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220943313337]`
- `2026-10-17T23:47:59Z`: `Objects=2`, `GBLeft=425.18` (`456532144128 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672728503664] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829818593371] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021345548766] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220963375265]`
- `2026-10-17T23:49:01Z`: `Objects=2`, `GBLeft=419.69` (`450641563648 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672733645448] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829834229235] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021345603366] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220978145921]`
- `2026-10-17T23:50:04Z`: `Objects=2`, `GBLeft=413.57` (`444069572608 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672735832480] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829846349795] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021345665406] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220986927393]`
- `2026-10-17T23:51:07Z`: `Objects=2`, `GBLeft=408.85` (`439004188672 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672738948832] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829861172339] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021345737286] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1220996879633]`
- `2026-10-17T23:52:09Z`: `Objects=2`, `GBLeft=404.54` (`434372759552 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672741357512] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829874472843] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021345824070] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221008971809]`
- `2026-10-17T23:53:11Z`: `Objects=2`, `GBLeft=399.95` (`429444816896 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672742993272] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829886543171] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021345860494] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221019950769]`
- `2026-10-17T23:54:14Z`: `Objects=2`, `GBLeft=396.23` (`425446387712 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672745925864] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829898733931] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021346828638] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221026201209]`
- `2026-10-17T23:55:16Z`: `Objects=2`, `GBLeft=392.79` (`421757657088 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672748608064] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829906550403] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021348061478] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221030506681]`
- `2026-10-17T23:56:20Z`: `Objects=2`, `GBLeft=389.12` (`417817546752 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672751894008] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829918138563] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021348301798] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221033332569]`
- `2026-10-17T23:57:23Z`: `Objects=2`, `GBLeft=385.99` (`414455214080 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672754686440] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829930166115] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021348386462] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221040377857]`
- `2026-10-17T23:58:27Z`: `Objects=2`, `GBLeft=383.57` (`411858055168 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672757181736] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829937790251] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021348422110] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221049014105]`
- `2026-10-17T23:59:30Z`: `Objects=2`, `GBLeft=379.78` (`407780794368 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672760767824] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829947841323] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021348469662] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221057205417]`
- `2026-10-18T00:00:32Z`: `Objects=2`, `GBLeft=377.04` (`404848123904 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672763878376] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829957523739] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021348966886] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221072500537]`
- `2026-10-18T00:01:35Z`: `Objects=2`, `GBLeft=372.60` (`400077459456 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672765653344] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829969795075] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021349252862] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221084730673]`
- `2026-10-18T00:02:39Z`: `Objects=2`, `GBLeft=366.89` (`393942994944 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672766150200] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829985566179] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021349472534] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221092445473]`
- `2026-10-18T00:03:41Z`: `Objects=2`, `GBLeft=364.57` (`391452868608 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672766339744] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=829993247571] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021349522830] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221108378641]`
- `2026-10-18T00:04:43Z`: `Objects=2`, `GBLeft=362.30` (`389017612288 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672766453512] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830000227275] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021349570510] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221122533761]`
- `2026-10-18T00:05:45Z`: `Objects=2`, `GBLeft=358.73` (`385182384128 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672767226440] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830011308395] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021349646190] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221144136489]`
- `2026-10-18T00:06:47Z`: `Objects=2`, `GBLeft=355.85` (`382086270976 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672767356456] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830019387219] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021349685806] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221172333521]`
- `2026-10-18T00:07:49Z`: `Objects=2`, `GBLeft=353.23` (`379276414976 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672767609976] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830026179403] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021349753718] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221188679705]`
- `2026-10-18T00:08:50Z`: `Objects=2`, `GBLeft=350.69` (`376552910848 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672767740176] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830034268155] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021349806710] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221204514169]`
- `2026-10-18T00:09:52Z`: `Objects=2`, `GBLeft=347.41` (`373027725312 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672769123712] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830043918339] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021349843726] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221218511865]`
- `2026-10-18T00:10:55Z`: `Objects=2`, `GBLeft=342.41` (`367658254336 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672772878744] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830058227531] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021349890622] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221232311497]`
- `2026-10-18T00:11:57Z`: `Objects=2`, `GBLeft=337.52` (`362412949504 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672774035368] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830071572651] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021349928566] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221249706393]`
- `2026-10-18T00:12:59Z`: `Objects=2`, `GBLeft=333.03` (`357589438464 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672774912352] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830088340787] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021349962302] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221266449217]`
- `2026-10-18T00:14:01Z`: `Objects=2`, `GBLeft=330.58` (`354959847424 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672775237000] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830094629275] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021349998438] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221284108713]`
- `2026-10-18T00:15:03Z`: `Objects=2`, `GBLeft=327.56` (`351713271808 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672775491504] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830107670187] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021350031646] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221298500017]`
- `2026-10-18T00:16:05Z`: `Objects=2`, `GBLeft=323.68` (`347548184576 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672775635616] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830117102219] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021350086334] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221314873745]`
- `2026-10-18T00:17:07Z`: `Objects=2`, `GBLeft=320.13` (`343735746560 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672776025712] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830123577859] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021350474550] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221333462497]`
- `2026-10-18T00:18:09Z`: `Objects=2`, `GBLeft=318.05` (`341498474496 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672776589392] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830129803251] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021350710318] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221361258961]`
- `2026-10-18T00:19:11Z`: `Objects=2`, `GBLeft=315.54` (`338813198336 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672777623088] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830141231291] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021350755766] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221384760057]`
- `2026-10-18T00:20:13Z`: `Objects=2`, `GBLeft=313.29` (`336395943936 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672778329144] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830149999531] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021350808342] | esxi02:T9[Health=OK,Wear=10(raw=1630),WrSec=1221399362833]`
- `2026-10-18T00:21:15Z`: `Objects=2`, `GBLeft=311.09` (`334031335424 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672778683912] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830158878123] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021350845662] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221409719041]`
- `2026-10-18T00:22:17Z`: `Objects=2`, `GBLeft=309.20` (`331996798976 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672779530920] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830165842859] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021350878158] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221425053481]`
- `2026-10-18T00:23:19Z`: `Objects=2`, `GBLeft=307.54` (`330219388928 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672780206224] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830172234555] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021350925126] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221446552521]`
- `2026-10-18T00:24:21Z`: `Objects=2`, `GBLeft=305.90` (`328460775424 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672780396088] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830179462707] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021350975622] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221465555033]`
- `2026-10-18T00:25:23Z`: `Objects=2`, `GBLeft=304.26` (`326698942464 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672780573240] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830186299155] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021351038502] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221482317441]`
- `2026-10-18T00:26:25Z`: `Objects=2`, `GBLeft=302.42` (`324722823168 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672780682960] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830192465379] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021351085374] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221501881289]`
- `2026-10-18T00:27:27Z`: `Objects=2`, `GBLeft=301.08` (`323279106048 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672781466904] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830200696851] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021351134502] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221513225321]`
- `2026-10-18T00:28:29Z`: `Objects=2`, `GBLeft=299.93` (`322045800448 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672781770128] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830206679251] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021351341262] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221529370217]`
- `2026-10-18T00:29:31Z`: `Objects=2`, `GBLeft=298.79` (`320823422976 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672782496760] | esxi01:T1[Health=OK,Wear=34(raw=1191),WrSec=830213092283] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021351521342] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221551310537]`
- `2026-10-18T00:30:33Z`: `Objects=2`, `GBLeft=297.64` (`319593254912 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672782608088] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830219190411] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021351562438] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221566123457]`
- `2026-10-18T00:31:35Z`: `Objects=2`, `GBLeft=296.52` (`318382829568 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672782775216] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830225317491] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021351595198] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221576819057]`
- `2026-10-18T00:32:37Z`: `Objects=2`, `GBLeft=295.34` (`317116739584 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672782936304] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830231966219] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021351627614] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221591552289]`
- `2026-10-18T00:33:39Z`: `Objects=2`, `GBLeft=294.10` (`315787186176 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672783058840] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830237320035] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021351674886] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221603674873]`
- `2026-10-18T00:34:41Z`: `Objects=2`, `GBLeft=292.37` (`313926529024 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672783234376] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830243163171] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021351711582] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221620805561]`
- `2026-10-18T00:35:42Z`: `Objects=2`, `GBLeft=290.92` (`312376987648 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672783705960] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830252154779] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021351756598] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221630619889]`
- `2026-10-18T00:36:44Z`: `Objects=2`, `GBLeft=376.60` (`404366229504 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672784218928] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830254146171] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021351839350] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221642546553]`
- `2026-10-18T00:37:46Z`: `Objects=2`, `GBLeft=375.57` (`403262799872 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672784624704] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830260643339] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021351932598] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221658315817]`
- `2026-10-18T00:38:48Z`: `Objects=2`, `GBLeft=374.03` (`401615880192 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672784805560] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830268843267] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021351992382] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221674973753]`
- `2026-10-18T00:39:50Z`: `Objects=2`, `GBLeft=371.71` (`399120203776 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672784934576] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830275169283] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021352024582] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221684840785]`
- `2026-10-18T00:40:52Z`: `Objects=2`, `GBLeft=370.49` (`397812236288 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672785188680] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830281451611] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021352064462] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221694883153]`
- `2026-10-18T00:41:54Z`: `Objects=2`, `GBLeft=369.28` (`396508332032 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672785284088] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830287538411] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021352109094] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221704972129]`
- `2026-10-18T00:42:56Z`: `Objects=2`, `GBLeft=368.04` (`395175002112 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672785494656] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830293891819] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021352814310] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221715789833]`
- `2026-10-18T00:43:57Z`: `Objects=2`, `GBLeft=366.70` (`393741271040 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672785746552] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830301991843] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021360507510] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221728739785]`
- `2026-10-18T00:44:59Z`: `Objects=2`, `GBLeft=365.89` (`392874557440 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672785875712] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830311027475] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021360798238] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221742051433]`
- `2026-10-18T00:46:01Z`: `Objects=2`, `GBLeft=365.55` (`392509521920 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672786092272] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830318681459] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021360849502] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221752546361]`
- `2026-10-18T00:47:04Z`: `Objects=2`, `GBLeft=365.19` (`392117616640 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672786375816] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830326087899] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021360892382] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221765423169]`
- `2026-10-18T00:48:06Z`: `Objects=2`, `GBLeft=364.84` (`391741440000 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672786475776] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830333150379] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021360926982] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221779232857]`
- `2026-10-18T00:49:08Z`: `Objects=2`, `GBLeft=363.42` (`390214582272 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672786950008] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830339909427] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021360959094] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221790583433]`
- `2026-10-18T00:50:09Z`: `Objects=2`, `GBLeft=361.77` (`388450353152 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672787534208] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830349041211] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021361017718] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221801555321]`
- `2026-10-18T00:51:11Z`: `Objects=2`, `GBLeft=360.19` (`386749693952 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672787874168] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830356401411] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021361056814] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221814845897]`
- `2026-10-18T00:52:13Z`: `Objects=2`, `GBLeft=358.58` (`385021509632 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672788188160] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830364567475] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021361097286] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221827729065]`
- `2026-10-18T00:53:15Z`: `Objects=2`, `GBLeft=356.93` (`383245791232 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672788352056] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830370966171] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021361134086] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221839972161]`
- `2026-10-18T00:54:17Z`: `Objects=2`, `GBLeft=355.52` (`381733429248 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672789036424] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830377321603] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021361555894] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221859360009]`
- `2026-10-18T00:55:19Z`: `Objects=2`, `GBLeft=354.46` (`380597370880 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672790586536] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830383580683] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021376124566] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221875878561]`
- `2026-10-18T00:56:21Z`: `Objects=2`, `GBLeft=353.42` (`379478016000 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672791677192] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830389261659] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021384205094] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1221892926529]`
- `2026-10-18T01:07:09Z`: `Objects=2`, `GBLeft=343.59` (`368925335552 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672807630160] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830457214099] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021447060710] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222021618337]`
- `2026-10-18T01:08:11Z`: `Objects=2`, `GBLeft=342.47` (`367720136704 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672808962936] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830462409291] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021448032382] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222030698689]`
- `2026-10-18T01:09:13Z`: `Objects=2`, `GBLeft=341.62` (`366812463104 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672810247424] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830468635555] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021448201614] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222041000249]`
- `2026-10-18T01:10:15Z`: `Objects=2`, `GBLeft=341.31` (`366482161664 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672811614328] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830474177003] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021455184622] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222051541849]`
- `2026-10-18T01:11:17Z`: `Objects=2`, `GBLeft=341.01` (`366154481664 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672813368280] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830480356811] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021464189926] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222063045145]`
- `2026-10-18T01:12:19Z`: `Objects=2`, `GBLeft=340.49` (`365593821184 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672817767784] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830487146579] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021473912590] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222073846537]`
- `2026-10-18T01:13:21Z`: `Objects=2`, `GBLeft=340.10` (`365176619008 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672818162088] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830493478507] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021480852630] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222084920129]`
- `2026-10-18T01:14:23Z`: `Objects=2`, `GBLeft=339.67` (`364713803776 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672818469616] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830500574091] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021485900886] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222096253121]`
- `2026-10-18T01:15:25Z`: `Objects=2`, `GBLeft=339.24` (`364258328576 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672818853704] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830506290691] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021487128374] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222107076569]`
- `2026-10-18T01:16:27Z`: `Objects=2`, `GBLeft=338.66` (`363631149056 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672819434256] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830513307107] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021487203574] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222116496017]`
- `2026-10-18T01:17:29Z`: `Objects=2`, `GBLeft=338.31` (`363257921536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672819900264] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830519472803] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021487275782] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222126816041]`
- `2026-10-18T01:18:31Z`: `Objects=2`, `GBLeft=337.97` (`362887905280 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672820648720] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830527256091] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021489329590] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222136315969]`
- `2026-10-18T01:19:33Z`: `Objects=2`, `GBLeft=337.62` (`362520903680 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672820986264] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830534068675] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021496696734] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222147187737]`
- `2026-10-18T01:20:35Z`: `Objects=2`, `GBLeft=336.89` (`361728507904 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672821994864] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830541493067] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021496901222] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222156841145]`
- `2026-10-18T01:21:37Z`: `Objects=2`, `GBLeft=335.26` (`359978172416 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672823783408] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830550464603] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021497068502] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222165708569]`
- `2026-10-18T01:22:39Z`: `Objects=2`, `GBLeft=333.56` (`358155223040 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672825926344] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830559843203] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021497107886] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222175262785]`
- `2026-10-18T01:23:41Z`: `Objects=2`, `GBLeft=331.39` (`355827712000 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672826768608] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830566480835] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021497145014] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222186803081]`
- `2026-10-18T01:24:43Z`: `Objects=2`, `GBLeft=329.83` (`354155626496 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672827790312] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830574521459] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021497241790] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222197291193]`
- `2026-10-18T01:25:45Z`: `Objects=2`, `GBLeft=328.00` (`352189939712 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672829342408] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830581880411] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021497298878] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222206860273]`
- `2026-10-18T01:26:48Z`: `Objects=2`, `GBLeft=327.65` (`351813763072 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672830808416] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830588104115] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021497817470] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222217597129]`
- `2026-10-18T01:27:49Z`: `Objects=2`, `GBLeft=327.08` (`351201591296 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672833180000] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830595026067] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021497858982] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222227006865]`
- `2026-10-18T01:28:52Z`: `Objects=2`, `GBLeft=324.41` (`348337799168 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672835749072] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830608265115] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021497893510] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222238650961]`
- `2026-10-18T01:29:53Z`: `Objects=2`, `GBLeft=322.42` (`346191691776 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672836875744] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830615903075] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021497929174] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222248739665]`
- `2026-10-18T01:30:55Z`: `Objects=2`, `GBLeft=320.82` (`344476352512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672837450392] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830623992699] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021497977086] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222259142889]`
- `2026-10-18T01:31:57Z`: `Objects=2`, `GBLeft=318.63` (`342131539968 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672839361960] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830630618803] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021498013398] | esxi02:T9[Health=OK,Wear=10(raw=1631),WrSec=1222272637257]`
- `2026-10-18T01:32:59Z`: `Objects=2`, `GBLeft=317.00` (`340375240704 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672841145288] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830637901251] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021498049910] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222285616985]`
- `2026-10-18T01:34:01Z`: `Objects=2`, `GBLeft=315.59` (`338866204672 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672842136416] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830643484899] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021498085390] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222299107529]`
- `2026-10-18T01:35:03Z`: `Objects=2`, `GBLeft=314.52` (`337717624832 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672842720176] | esxi01:T1[Health=OK,Wear=34(raw=1192),WrSec=830649192091] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021498139510] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222316678369]`
- `2026-10-18T01:36:05Z`: `Objects=2`, `GBLeft=313.37` (`336474275840 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672843367104] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830656216379] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021498207902] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222334364225]`
- `2026-10-18T01:37:07Z`: `Objects=2`, `GBLeft=312.15` (`335170170880 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672844269304] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830663330483] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021498243670] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222349916689]`
- `2026-10-18T01:38:09Z`: `Objects=2`, `GBLeft=310.87` (`333796605952 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672845408864] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830672117555] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021501019406] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222368329321]`
- `2026-10-18T01:39:11Z`: `Objects=2`, `GBLeft=309.71` (`332545851392 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672846241528] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830677810403] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021505619950] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222378524105]`
- `2026-10-18T01:40:13Z`: `Objects=2`, `GBLeft=308.66` (`331423416320 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672847971112] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830683428035] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021506551014] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222393266297]`
- `2026-10-18T01:41:14Z`: `Objects=2`, `GBLeft=307.53` (`330210148352 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672849782768] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830689256019] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021509743646] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222409047473]`
- `2026-10-18T01:42:17Z`: `Objects=2`, `GBLeft=306.45` (`329044656128 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672850144256] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830695478987] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021513465078] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222430860145]`
- `2026-10-18T01:43:18Z`: `Objects=2`, `GBLeft=305.35` (`327863828480 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672850429312] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830701601811] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021514084694] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222450523985]`
- `2026-10-18T01:44:20Z`: `Objects=2`, `GBLeft=304.14` (`326571261952 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672850668992] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830709412819] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021519737054] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222470124641]`
- `2026-10-18T01:45:22Z`: `Objects=2`, `GBLeft=303.46` (`325840928768 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672851203248] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830716450019] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021521924310] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222488641625]`
- `2026-10-18T01:46:25Z`: `Objects=2`, `GBLeft=302.27` (`324561141760 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672851695184] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830722389379] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021524162814] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222508696129]`
- `2026-10-18T01:47:29Z`: `Objects=2`, `GBLeft=300.71` (`322889646080 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672852123632] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830728737099] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021524593918] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222526297921]`
- `2026-10-18T01:48:32Z`: `Objects=2`, `GBLeft=299.04` (`321091993600 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672852696112] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830735347979] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021526844422] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222544001825]`
- `2026-10-18T01:49:35Z`: `Objects=2`, `GBLeft=297.76` (`319715737600 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672853559528] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830741976515] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021532068470] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222561465601]`
- `2026-10-18T01:50:38Z`: `Objects=2`, `GBLeft=296.53` (`318392893440 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672854103960] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830748194155] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021532141558] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222578799705]`
- `2026-10-18T01:51:41Z`: `Objects=2`, `GBLeft=295.17` (`316939960320 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672854586856] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830755061443] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021536380766] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222594828129]`
- `2026-10-18T01:52:44Z`: `Objects=2`, `GBLeft=293.89` (`315557806080 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672855364248] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830761596275] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021539773406] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222609643929]`
- `2026-10-18T01:53:47Z`: `Objects=2`, `GBLeft=292.55` (`314122174464 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672856425400] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830769197179] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021539819782] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222625095425]`
- `2026-10-18T01:54:49Z`: `Objects=2`, `GBLeft=291.40` (`312885051392 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672857530888] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830775368747] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021542138566] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222637035457]`
- `2026-10-18T01:55:51Z`: `Objects=2`, `GBLeft=290.12` (`311518101504 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672858578544] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830782428715] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021545655046] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222652159129]`
- `2026-10-18T01:56:53Z`: `Objects=2`, `GBLeft=288.94` (`310244802560 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672859351424] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830788555131] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021545701030] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222667930377]`
- `2026-10-18T01:57:54Z`: `Objects=2`, `GBLeft=287.63` (`308838400000 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672860332824] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830798055603] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021547887302] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222682073913]`
- `2026-10-18T01:58:56Z`: `Objects=2`, `GBLeft=286.20` (`307302563840 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672861139240] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830806088963] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021554585246] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222693797505]`
- `2026-10-18T01:59:59Z`: `Objects=2`, `GBLeft=284.85` (`305858609152 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672861796224] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830813543187] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021563611206] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222707730833]`
- `2026-10-18T02:01:02Z`: `Objects=2`, `GBLeft=283.55` (`304460922880 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672862784616] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830820585067] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021574222486] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222724667825]`
- `2026-10-18T02:02:04Z`: `Objects=2`, `GBLeft=281.30` (`302048411648 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672863892128] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830828592027] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021584379030] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222734774953]`
- `2026-10-18T02:03:07Z`: `Objects=2`, `GBLeft=278.97` (`299536809984 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672864578080] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830834328611] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021598527774] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222747659225]`
- `2026-10-18T02:04:09Z`: `Objects=2`, `GBLeft=278.10` (`298605608960 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672865366440] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830840925427] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021609584070] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222766289465]`
- `2026-10-18T02:05:12Z`: `Objects=2`, `GBLeft=277.17` (`297607561216 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672866260336] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830848197251] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021614749662] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222785276249]`
- `2026-10-18T02:06:15Z`: `Objects=2`, `GBLeft=274.77` (`295031492608 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672867389600] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830854694595] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021618063206] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222802354673]`
- `2026-10-18T02:07:17Z`: `Objects=2`, `GBLeft=272.46` (`292551327744 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672868256072] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830861627379] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021621113750] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222819213329]`
- `2026-10-18T02:08:20Z`: `Objects=2`, `GBLeft=269.19` (`289041985536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672868785288] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830867814547] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021624422486] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222836794697]`
- `2026-10-18T02:09:24Z`: `Objects=2`, `GBLeft=265.51` (`285084852224 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672869353128] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830875076243] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021627021206] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222850930425]`
- `2026-10-18T02:10:27Z`: `Objects=2`, `GBLeft=261.70` (`281001746432 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672870191288] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830884575299] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021629859326] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222865057321]`
- `2026-10-18T02:11:29Z`: `Objects=2`, `GBLeft=257.51` (`276498743296 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672873052088] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830893539251] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021632234222] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222874957345]`
- `2026-10-18T02:12:32Z`: `Objects=2`, `GBLeft=253.09` (`271754330112 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672874657560] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830900133051] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021634371350] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222890660409]`
- `2026-10-18T02:13:34Z`: `Objects=2`, `GBLeft=249.26` (`267637690368 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672874880768] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830905954819] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021639416366] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222908336105]`
- `2026-10-18T02:14:36Z`: `Objects=2`, `GBLeft=242.24` (`260106289152 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672875314520] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830911862579] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021649888454] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222926846465]`
- `2026-10-18T02:15:38Z`: `Objects=2`, `GBLeft=216.99` (`232991883264 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672875725664] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830919191019] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021654959678] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222942592521]`
- `2026-10-18T02:16:41Z`: `Objects=1`, `GBLeft=185.22` (`198880395264 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672875910112] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830925922571] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021664622878] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222959702857]`
- `2026-10-18T02:17:44Z`: `Objects=1`, `GBLeft=184.09` (`197663522816 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672876038688] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830931709339] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021677769246] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222975269769]`
- `2026-10-18T02:18:46Z`: `Objects=1`, `GBLeft=182.78` (`196260724736 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672876249248] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830939782171] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021689349646] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1222997082561]`
- `2026-10-18T02:19:49Z`: `Objects=1`, `GBLeft=181.42` (`194800615424 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672876400112] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830947794595] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021698390350] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223015169665]`
- `2026-10-18T02:20:51Z`: `Objects=1`, `GBLeft=180.11` (`193389395968 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672876759576] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830954439715] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021706987830] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223029321265]`
- `2026-10-18T02:21:53Z`: `Objects=1`, `GBLeft=178.93` (`192119832576 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672876927824] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830960503243] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021709508550] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223039780305]`
- `2026-10-18T02:22:55Z`: `Objects=1`, `GBLeft=177.17` (`190235701248 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672877007800] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830967638011] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021711891470] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223048997321]`
- `2026-10-18T02:23:57Z`: `Objects=1`, `GBLeft=175.55` (`188494815232 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672877261440] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830974505379] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021720931838] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223060373713]`
- `2026-10-18T02:24:59Z`: `Objects=1`, `GBLeft=174.36` (`187214200832 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672877333080] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830980984459] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021732134470] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223075796329]`
- `2026-10-18T02:26:00Z`: `Objects=1`, `GBLeft=173.04` (`185798623232 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672877547280] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830986951307] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021734511854] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223086630929]`
- `2026-10-18T02:27:02Z`: `Objects=1`, `GBLeft=171.82` (`184487641088 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672877631792] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=830993779667] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021736813958] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223095896113]`
- `2026-10-18T02:28:04Z`: `Objects=1`, `GBLeft=170.49` (`183064920064 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672877682336] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=831000450603] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021739169046] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223099463545]`
- `2026-10-18T02:29:06Z`: `Objects=1`, `GBLeft=169.26` (`181742600192 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672877786080] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=831007915635] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021741529910] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223103760225]`
- `2026-10-18T02:30:08Z`: `Objects=1`, `GBLeft=168.01` (`180397801472 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672877965072] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=831014260683] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021743875102] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223117385305]`
- `2026-10-18T02:31:10Z`: `Objects=1`, `GBLeft=166.11` (`178358845440 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672878105856] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=831020492571] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021746195854] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223129710225]`
- `2026-10-18T02:32:13Z`: `Objects=1`, `GBLeft=164.88` (`177037639680 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672878213056] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=831026043883] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021747557910] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223144379001]`
- `2026-10-18T02:33:14Z`: `Objects=1`, `GBLeft=163.53` (`175594143744 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672878427544] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=831032633003] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021750019110] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223160144609]`
- `2026-10-18T02:34:16Z`: `Objects=1`, `GBLeft=162.11` (`174059618304 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672878676464] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=831039522467] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021752337070] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223179518937]`
- `2026-10-18T02:35:18Z`: `Objects=1`, `GBLeft=160.89` (`172750209024 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672878824472] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=831045132691] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021755514406] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223195721617]`
- `2026-10-18T02:36:20Z`: `Objects=1`, `GBLeft=159.79` (`171569823744 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672878975912] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=831050562851] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021757858254] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223212157145]`
- `2026-10-18T02:37:22Z`: `Objects=1`, `GBLeft=158.66` (`170362142720 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672879193000] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=831056226795] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021760125886] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223233491569]`
- `2026-10-18T02:38:24Z`: `Objects=1`, `GBLeft=157.55` (`169167683584 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672879421432] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=831061688779] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021762269774] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223239612657]`
- `2026-10-18T02:39:26Z`: `Objects=1`, `GBLeft=156.19` (`167709573120 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672879599200] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=831067375355] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021764438590] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223251243897]`
- `2026-10-18T02:40:29Z`: `Objects=1`, `GBLeft=155.04` (`166476054528 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672879778928] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=831073196595] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021766713206] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223265272417]`
- `2026-10-18T02:41:32Z`: `Objects=1`, `GBLeft=153.91` (`165263966208 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672879974360] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=831078948475] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021770374630] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223272824073]`
- `2026-10-18T02:42:34Z`: `Objects=1`, `GBLeft=152.71` (`163975069696 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672880226160] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=831084943659] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021772593918] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223284941537]`
- `2026-10-18T02:43:36Z`: `Objects=1`, `GBLeft=151.53` (`162707521536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672880574328] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=831091693819] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021778541942] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223297169737]`
- `2026-10-18T02:44:38Z`: `Objects=1`, `GBLeft=150.23` (`161312735232 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672880942288] | esxi01:T1[Health=OK,Wear=34(raw=1193),WrSec=831097715667] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021791596166] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223311493401]`
- `2026-10-18T02:45:40Z`: `Objects=1`, `GBLeft=149.22` (`160221560832 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672881133272] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831103442771] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021803886798] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223324169169]`
- `2026-10-18T02:46:41Z`: `Objects=1`, `GBLeft=148.15` (`159074353152 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672881514376] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831109055755] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021818662934] | esxi02:T9[Health=OK,Wear=10(raw=1632),WrSec=1223346082041]`
- `2026-10-18T02:47:43Z`: `Objects=1`, `GBLeft=146.29` (`157074194432 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672881624456] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831115033779] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021829974550] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223358287377]`
- `2026-10-18T02:48:45Z`: `Objects=1`, `GBLeft=145.07` (`155771273216 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672881752416] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831121234763] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021842898774] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223371436913]`
- `2026-10-18T02:49:48Z`: `Objects=1`, `GBLeft=143.91` (`154523795456 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672881928912] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831126933019] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021853969782] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223396809769]`
- `2026-10-18T02:50:50Z`: `Objects=1`, `GBLeft=142.71` (`153232080896 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672882118960] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831133234531] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021869744086] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223418146281]`
- `2026-10-18T02:51:52Z`: `Objects=1`, `GBLeft=141.41` (`151842652160 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672882246992] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831139698435] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021880981118] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223439071769]`
- `2026-10-18T02:52:54Z`: `Objects=1`, `GBLeft=140.31` (`150655336448 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672882302544] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831145657019] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021894553374] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223455810529]`
- `2026-10-18T02:53:57Z`: `Objects=1`, `GBLeft=139.07` (`149322006528 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672882400328] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831152575443] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021906099814] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223482734785]`
- `2026-10-18T02:54:59Z`: `Objects=1`, `GBLeft=137.65` (`147796262912 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672882500136] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831161097723] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021920891454] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223497380545]`
- `2026-10-18T02:56:01Z`: `Objects=1`, `GBLeft=136.20` (`146240172032 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672882769840] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831169180499] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021932861814] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223522080761]`
- `2026-10-18T02:57:04Z`: `Objects=1`, `GBLeft=135.03` (`144986931200 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672882949688] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831175267675] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021946584790] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223543876457]`
- `2026-10-18T02:58:06Z`: `Objects=1`, `GBLeft=133.80` (`143661531136 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672883375512] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831182986427] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021960504366] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223568083081]`
- `2026-10-18T02:59:10Z`: `Objects=1`, `GBLeft=132.36` (`142116388864 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672883505024] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831189487131] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021966947734] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223589995857]`
- `2026-10-18T03:00:12Z`: `Objects=1`, `GBLeft=131.11` (`140781551616 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672883639864] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831195922835] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021981278398] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223608313089]`
- `2026-10-18T03:01:15Z`: `Objects=1`, `GBLeft=129.01` (`138518134784 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672883718928] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831203837603] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1021996001110] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223632230425]`
- `2026-10-18T03:02:17Z`: `Objects=1`, `GBLeft=127.80` (`137227272192 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672883787080] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831210149835] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022006117150] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223649081953]`
- `2026-10-18T03:03:19Z`: `Objects=1`, `GBLeft=126.50` (`135828471808 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672884082288] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831217124915] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022011426006] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223666367489]`
- `2026-10-18T03:04:21Z`: `Objects=1`, `GBLeft=125.32` (`134563364864 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672884569216] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831223803115] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022017437342] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223688638441]`
- `2026-10-18T03:05:23Z`: `Objects=1`, `GBLeft=124.05` (`133197135872 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672884788664] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831231090227] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022017829718] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223707441769]`
- `2026-10-18T03:06:25Z`: `Objects=1`, `GBLeft=122.81` (`131867922432 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672884936552] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831237483363] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022018106022] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223719612337]`
- `2026-10-18T03:07:27Z`: `Objects=1`, `GBLeft=120.92` (`129832976384 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672885023008] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831243853011] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022018284142] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223728916017]`
- `2026-10-18T03:08:28Z`: `Objects=1`, `GBLeft=119.66` (`128479920128 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672885110488] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831251202099] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022018414694] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223741970841]`
- `2026-10-18T03:09:31Z`: `Objects=1`, `GBLeft=118.33` (`127057133568 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672885639960] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831257881467] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022018536766] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223752465089]`
- `2026-10-18T03:10:33Z`: `Objects=1`, `GBLeft=117.04` (`125674848256 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672888696696] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831265898419] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022018698638] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223766665353]`
- `2026-10-18T03:11:35Z`: `Objects=1`, `GBLeft=115.71` (`124246999040 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672890501736] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831272707499] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022018855278] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223779157833]`
- `2026-10-18T03:12:37Z`: `Objects=1`, `GBLeft=114.61` (`123060027392 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672891625592] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831278597211] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022019069070] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223790872825]`
- `2026-10-18T03:13:39Z`: `Objects=1`, `GBLeft=113.44` (`121800294400 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672892701576] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831286293227] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022023301550] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223803988129]`
- `2026-10-18T03:14:41Z`: `Objects=1`, `GBLeft=112.08` (`120349589504 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672893382440] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831293841043] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022023455078] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223815765057]`
- `2026-10-18T03:15:43Z`: `Objects=1`, `GBLeft=110.85` (`119022288896 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672893945904] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831300980923] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022023776518] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223829434865]`
- `2026-10-18T03:16:45Z`: `Objects=1`, `GBLeft=109.55` (`117630369792 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672894688496] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831307289835] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022024093302] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223843633041]`
- `2026-10-18T03:17:47Z`: `Objects=1`, `GBLeft=108.39` (`116378566656 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672895351448] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831314375563] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022024321638] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223858875689]`
- `2026-10-18T03:18:49Z`: `Objects=1`, `GBLeft=106.55` (`114404286464 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672895638984] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831319960275] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022024494294] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223882783465]`
- `2026-10-18T03:19:51Z`: `Objects=1`, `GBLeft=105.41` (`113180213248 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672896760568] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831325430099] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022024976038] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223903233577]`
- `2026-10-18T03:20:52Z`: `Objects=1`, `GBLeft=104.23` (`111919038464 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672898756040] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831331094699] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022025533094] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223922089857]`
- `2026-10-18T03:21:54Z`: `Objects=1`, `GBLeft=103.06` (`110662647808 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672898864016] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831336843859] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022025690406] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223935317633]`
- `2026-10-18T03:22:56Z`: `Objects=1`, `GBLeft=101.90` (`109412679680 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672899247192] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831342555451] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022025840702] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223950810489]`
- `2026-10-18T03:23:58Z`: `Objects=1`, `GBLeft=100.73` (`108156125184 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672899816544] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831348521699] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022026005566] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223967768009]`
- `2026-10-18T03:25:00Z`: `Objects=1`, `GBLeft=99.55` (`106886725632 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672902100384] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831353980779] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022026306974] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1223984754145]`
- `2026-10-18T03:26:02Z`: `Objects=1`, `GBLeft=98.15` (`105386344448 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672906662136] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831359673467] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022026689102] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224000999913]`
- `2026-10-18T03:27:03Z`: `Objects=1`, `GBLeft=96.94` (`104085061632 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672909216848] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831365256875] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022027142182] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224016030065]`
- `2026-10-18T03:28:06Z`: `Objects=1`, `GBLeft=95.66` (`102711361536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672912132696] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831370895675] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022027405054] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224033135497]`
- `2026-10-18T03:29:08Z`: `Objects=1`, `GBLeft=94.28` (`101233053696 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672913272936] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831375918099] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022027526582] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224045739961]`
- `2026-10-18T03:30:09Z`: `Objects=1`, `GBLeft=92.86` (`99705946112 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672914540648] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831381068123] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022027765206] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224057260057]`
- `2026-10-18T03:31:11Z`: `Objects=1`, `GBLeft=91.42` (`98165653504 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672915140232] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831385619947] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022027877990] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224068912993]`
- `2026-10-18T03:32:13Z`: `Objects=1`, `GBLeft=89.97` (`96606093312 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672916010680] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831390369779] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022027999702] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224080652505]`
- `2026-10-18T03:33:15Z`: `Objects=1`, `GBLeft=88.52` (`95048433664 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672918228776] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831396336843] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022028195254] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224094538433]`
- `2026-10-18T03:34:17Z`: `Objects=1`, `GBLeft=87.17` (`93601333248 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672918926256] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831402585155] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022028235190] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224107115737]`
- `2026-10-18T03:35:19Z`: `Objects=1`, `GBLeft=85.83` (`92163014656 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672919697760] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831408644355] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022028294126] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224123591601]`
- `2026-10-18T03:36:21Z`: `Objects=1`, `GBLeft=83.98` (`90174324736 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672920454344] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831414572819] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022028398942] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224136176681]`
- `2026-10-18T03:37:23Z`: `Objects=1`, `GBLeft=82.71` (`88808620032 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672924762072] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831420386555] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022028684726] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224151922825]`
- `2026-10-18T03:38:25Z`: `Objects=1`, `GBLeft=81.30` (`87292116992 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672929895680] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831426961427] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022029119710] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224165059177]`
- `2026-10-18T03:39:26Z`: `Objects=1`, `GBLeft=80.00` (`85895213056 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672931905616] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831433033315] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022029609278] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224181722129]`
- `2026-10-18T03:40:28Z`: `Objects=1`, `GBLeft=78.82` (`84628668416 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672933709912] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831438628387] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022029965086] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224192791801]`
- `2026-10-18T03:41:30Z`: `Objects=1`, `GBLeft=77.66` (`83388985344 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672934976312] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831444138179] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022030369566] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224204844537]`
- `2026-10-18T03:42:32Z`: `Objects=1`, `GBLeft=76.38` (`82008211456 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672937923440] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831450009995] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022031030134] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224215138545]`
- `2026-10-18T03:43:34Z`: `Objects=1`, `GBLeft=75.20` (`80747151360 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672939610488] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831455390899] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022031702782] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224226424401]`
- `2026-10-18T03:44:36Z`: `Objects=1`, `GBLeft=74.03` (`79493005312 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672942640536] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831460391411] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022031971734] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224236805985]`
- `2026-10-18T03:45:38Z`: `Objects=1`, `GBLeft=72.65` (`78003830784 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672943919504] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831465626003] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022032064790] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224249337457]`
- `2026-10-18T03:46:40Z`: `Objects=1`, `GBLeft=71.12` (`76366217216 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672945268984] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831471341779] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022032140518] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224261318225]`
- `2026-10-18T03:47:42Z`: `Objects=1`, `GBLeft=69.89` (`75046256640 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672947009976] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831477360987] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022032201942] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224273361617]`
- `2026-10-18T03:48:44Z`: `Objects=1`, `GBLeft=68.54` (`73595420672 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672949708832] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831483122123] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022032324110] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224285671369]`
- `2026-10-18T03:49:46Z`: `Objects=1`, `GBLeft=67.35` (`72320745472 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672953634872] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831488800627] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022032374350] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224300457297]`
- `2026-10-18T03:50:48Z`: `Objects=1`, `GBLeft=66.16` (`71040892928 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672955222064] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831494195419] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022032432310] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224319074193]`
- `2026-10-18T03:51:50Z`: `Objects=1`, `GBLeft=64.88` (`69662015488 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672956868600] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831501454235] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022032477046] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224332357865]`
- `2026-10-18T03:52:52Z`: `Objects=1`, `GBLeft=63.51` (`68194861056 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672958854792] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831507514427] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022032518598] | esxi02:T9[Health=OK,Wear=10(raw=1633),WrSec=1224348620281]`
- `2026-10-18T03:53:53Z`: `Objects=1`, `GBLeft=61.00` (`65502838784 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672959809400] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831513248867] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022032557158] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224360962745]`
- `2026-10-18T03:54:55Z`: `Objects=1`, `GBLeft=59.88` (`64298876928 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672960704992] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831518667675] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022032595142] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224376629377]`
- `2026-10-18T03:55:57Z`: `Objects=1`, `GBLeft=58.71` (`63038685184 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672962109048] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831524075035] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022032655518] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224390118025]`
- `2026-10-18T03:56:59Z`: `Objects=1`, `GBLeft=57.48` (`61721018368 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672963754080] | esxi01:T1[Health=OK,Wear=34(raw=1194),WrSec=831529774675] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022032694438] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224403601233]`
- `2026-10-18T03:58:01Z`: `Objects=1`, `GBLeft=56.22` (`60371042304 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672966399192] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831535374043] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022032735038] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224418526505]`
- `2026-10-18T03:59:03Z`: `Objects=1`, `GBLeft=54.97` (`59028602880 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672967978648] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831541345323] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022032810118] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224432759953]`
- `2026-10-18T04:00:05Z`: `Objects=1`, `GBLeft=53.70` (`57659752448 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672968978904] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831546994443] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022032849582] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224444806441]`
- `2026-10-18T04:01:07Z`: `Objects=1`, `GBLeft=52.40` (`56265146368 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672970008264] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831553121859] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022032926798] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224458608993]`
- `2026-10-18T04:02:09Z`: `Objects=1`, `GBLeft=51.23` (`55006265344 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672971098728] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831558374067] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022032965462] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224473345721]`
- `2026-10-18T04:03:11Z`: `Objects=1`, `GBLeft=49.85` (`53530853376 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672972649480] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831564968955] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022033005174] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224486838201]`
- `2026-10-18T04:04:13Z`: `Objects=1`, `GBLeft=48.48` (`52057669632 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672974145344] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831571441403] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022033047118] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224500893569]`
- `2026-10-18T04:05:15Z`: `Objects=1`, `GBLeft=47.16` (`50634424320 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672975201792] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831577318043] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022033109806] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224516369849]`
- `2026-10-18T04:06:16Z`: `Objects=1`, `GBLeft=45.97` (`49355423744 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672976811912] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831582435819] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022033150686] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224530535713]`
- `2026-10-18T04:07:18Z`: `Objects=1`, `GBLeft=44.82` (`48122757120 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672977853832] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831588312467] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022033188086] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224545712585]`
- `2026-10-18T04:08:20Z`: `Objects=1`, `GBLeft=43.52` (`46727168000 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672978903320] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831593772923] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022033233838] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224557769633]`
- `2026-10-18T04:09:22Z`: `Objects=1`, `GBLeft=42.27` (`45389381632 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672979628768] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831599172771] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022033304294] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224569346785]`
- `2026-10-18T04:10:24Z`: `Objects=1`, `GBLeft=41.09` (`44120145920 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672980713912] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831604456163] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022033359022] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224584362865]`
- `2026-10-18T04:11:26Z`: `Objects=1`, `GBLeft=39.78` (`42715643904 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672981821016] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831610557723] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022033404622] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224596237665]`
- `2026-10-18T04:12:27Z`: `Objects=1`, `GBLeft=37.92` (`40717975552 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672982805696] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831616031427] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022033444166] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224610385529]`
- `2026-10-18T04:13:29Z`: `Objects=1`, `GBLeft=36.71` (`39420231680 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672985099504] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831621063867] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022033517630] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224622230585]`
- `2026-10-18T04:14:31Z`: `Objects=1`, `GBLeft=35.55` (`38173999104 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672985806720] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831626546363] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022033555694] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224636602857]`
- `2026-10-18T04:15:33Z`: `Objects=1`, `GBLeft=34.28` (`36810129408 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672988278928] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831632268099] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022033618990] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224649140097]`
- `2026-10-18T04:16:35Z`: `Objects=1`, `GBLeft=33.07` (`35504259072 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672989511808] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831637698931] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022033657854] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224663608833]`
- `2026-10-18T04:17:37Z`: `Objects=1`, `GBLeft=31.84` (`34184429568 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672991798824] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831642888267] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022033727110] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224677207761]`
- `2026-10-18T04:18:38Z`: `Objects=1`, `GBLeft=30.53` (`32782221312 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672993627248] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831648770627] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022033793846] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224694178529]`
- `2026-10-18T04:19:40Z`: `Objects=1`, `GBLeft=29.33` (`31492276224 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672995946440] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831654318267] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022033838094] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224713341297]`
- `2026-10-18T04:20:42Z`: `Objects=1`, `GBLeft=28.01` (`30073618432 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672997581576] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831660092659] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022033902126] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224735510185]`
- `2026-10-18T04:21:44Z`: `Objects=1`, `GBLeft=26.71` (`28682420224 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=672998652240] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831665909339] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022033942614] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224754319081]`
- `2026-10-18T04:22:46Z`: `Objects=1`, `GBLeft=25.29` (`27155628032 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=673000393712] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831671952163] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022033984582] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224771865009]`
- `2026-10-18T04:23:48Z`: `Objects=1`, `GBLeft=24.01` (`25775570944 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=673001705176] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831677878051] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022034025998] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224788326417]`
- `2026-10-18T04:24:50Z`: `Objects=1`, `GBLeft=22.71` (`24389615616 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=673003618304] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831683286299] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022034064406] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224804895649]`
- `2026-10-18T04:25:52Z`: `Objects=1`, `GBLeft=21.40` (`22977118208 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=673004551224] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831689003171] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022034116286] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224821716881]`
- `2026-10-18T04:26:53Z`: `Objects=1`, `GBLeft=20.09` (`21576548352 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=673006581544] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831694723067] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022034150174] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224837987249]`
- `2026-10-18T04:27:55Z`: `Objects=1`, `GBLeft=18.68` (`20052574208 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=673009314912] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831700616211] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022034188286] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224854880025]`
- `2026-10-18T04:28:57Z`: `Objects=1`, `GBLeft=17.27` (`18541969408 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=673010370592] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831706322083] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022034248374] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224872904113]`
- `2026-10-18T04:29:59Z`: `Objects=1`, `GBLeft=15.99` (`17174102016 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=673011585248] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831713107611] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022034288094] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224889168489]`
- `2026-10-18T04:31:00Z`: `Objects=1`, `GBLeft=13.98` (`15006695424 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=673012472816] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831719186419] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022034339742] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224902360681]`
- `2026-10-18T04:32:02Z`: `Objects=1`, `GBLeft=12.68` (`13613989888 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=673012825064] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831724746947] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022034375870] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224916033881]`
- `2026-10-18T04:33:04Z`: `Objects=1`, `GBLeft=11.43` (`12267945984 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=673013157456] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831730289211] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022034412358] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224928483417]`
- `2026-10-18T04:34:06Z`: `Objects=1`, `GBLeft=9.64` (`10347544576 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=673013769256] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831735447251] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022034448518] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224940424057]`
- `2026-10-18T04:35:08Z`: `Objects=1`, `GBLeft=8.39` (`9013886976 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=673013969096] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831740572235] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022034501062] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224952747993]`
- `2026-10-18T04:36:10Z`: `Objects=1`, `GBLeft=7.09` (`7608139776 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=673014113912] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831746097187] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022034541454] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224965221193]`
- `2026-10-18T04:37:12Z`: `Objects=1`, `GBLeft=5.84` (`6273630208 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=673014211928] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831751603291] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022034577454] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224977459433]`
- `2026-10-18T04:38:14Z`: `Objects=1`, `GBLeft=4.64` (`4979949568 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=673014354456] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831757267555] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022034635558] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224988092617]`
- `2026-10-18T04:39:15Z`: `Objects=1`, `GBLeft=3.40` (`3654680576 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=673014452912] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831762775123] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022034697814] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1224998408801]`
- `2026-10-18T04:40:17Z`: `Objects=1`, `GBLeft=2.17` (`2332033024 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=673014552424] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831768502315] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022034744374] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1225008641801]`
- `2026-10-18T04:41:19Z`: `Objects=1`, `GBLeft=1.01` (`1084882944 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=673014618600] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831773922595] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022034789374] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1225018680345]`
- `2026-10-18T04:42:21Z`: `Objects=0`, `GBLeft=0.00` (`0 B`) | `esxi01:T0[Health=OK,Wear=50(raw=894),WrSec=673014859706] | esxi01:T1[Health=OK,Wear=34(raw=1195),WrSec=831778662675] | esxi02:T8[Health=OK,Wear=24(raw=1371),WrSec=1022034822766] | esxi02:T9[Health=OK,Wear=10(raw=1634),WrSec=1225031365249]`

### Post-Evacuation Resync Verification (`esxcli vsan debug resync summary get`, `rc=0`)
```text
TotalBytesLeftToResync:         0
TotalGBLeftToResync:            0.00
TotalNumberOfResyncingObjects:  0
```

- **Add Start / End (UTC)**: `2026-10-18T04:42:49Z` -> `2026-10-18T04:43:17Z`
- **Add Command**: `esxcli vsan storage add -s mpx.vmhba0:C2:T1:L0 -d naa.5000cca01aa7e5ec`
- **Add Exit Code**: `rc=0`
- **Add Output / Stderr**: `true`
- **Post-Add Disk Verification**:
  - `Device`: `naa.5000cca01aa7e5ec`
  - `VSANDiskGroupName`: `mpx.vmhba0:C2:T1:L0`
  - `VSANDiskGroupUUID`: `520e7405-7951-d36b-972f-44fa76ee518e`
  - `VSANUUID`: `52d55f3a-970a-3363-c37c-98bfd6d162b0`
  - `IsMounted`: `true`
  - `InCMMDS`: `true`

### Post-Step B2 Resync Summary (`rc=0`)
```text
TotalBytesLeftToResync:         0
TotalGBLeftToResync:            0.00
TotalNumberOfResyncingObjects:  0
```

### Post-Step B2 SMART Wear Check (`esxi01` `T0`/`T1` & `esxi02` `T8`/`T9`)
### esxi01 T0 (DG 52e69070 Cache) (`mpx.vmhba0:C2:T0:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    50     0          50     894
Write Error Count          100    10         100    0
Power-on Hours             98     0          98     9510
Power Cycle Count          99     0          99     293
Reallocated Sector Count   100    10         100    0
Drive Temperature          51     0          34     49
Write Sectors TOT Count    99     0          99     673014914906
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```
### esxi01 T1 (DG 520e7405 Cache) (`mpx.vmhba0:C2:T1:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    34     0          34     1195
Write Error Count          100    10         100    0
Power-on Hours             98     0          98     9510
Power Cycle Count          99     0          99     297
Reallocated Sector Count   100    10         100    0
Drive Temperature          44     0          34     56
Write Sectors TOT Count    99     0          99     831781961196
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```
### esxi02 T8 (DG 5240e70d Cache) (`mpx.vmhba0:C2:T8:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    24     0          24     1371
Write Error Count          100    10         100    0
Power-on Hours             98     0          98     9605
Power Cycle Count          99     0          99     307
Reallocated Sector Count   100    10         100    0
Drive Temperature          42     0          29     58
Write Sectors TOT Count    99     0          99     1022034862254
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```
### esxi02 T9 (DG 522f2b56 Cache) (`mpx.vmhba0:C2:T9:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    10     0          10     1634
Write Error Count          100    10         100    0
Power-on Hours             95     0          95     24363
Power Cycle Count          98     0          98     1485
Reallocated Sector Count   100    10         100    0
Drive Temperature          34     0          29     66
Write Sectors TOT Count    99     0          99     1225044691169
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```

### Post-Step B2 `esxi01` Disk Group Layout
| Disk Group UUID | Cache SSD | Device | Capacity Tier | IsSSD | Mounted | InCMMDS | VSAN UUID |
|---|---|---|---|---|---|---|---|
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `mpx.vmhba0:C2:T1:L0` | `false` | `true` | `true` | `true` | `520e7405-7951-d36b-972f-44fa76ee518e` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `mpx.vmhba0:C2:T5:L0` | `true` | `false` | `true` | `true` | `522808b1-f93b-0bb6-e166-2a829a754e3c` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.50000c0f012d3630` | `true` | `false` | `true` | `true` | `522ac7d2-9125-2e27-d90a-e79d5b95d5ff` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.50000c0f01e0c290` | `true` | `false` | `true` | `true` | `52231164-8542-4e47-05b5-c97338472125` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01aa7e5ec` | `true` | `false` | `true` | `true` | `52d55f3a-970a-3363-c37c-98bfd6d162b0` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01aa9a96c` | `true` | `false` | `true` | `true` | `52191fd7-997a-a5a6-c65f-0b325a677220` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01ab112cc` | `true` | `false` | `true` | `true` | `52e03231-e4ca-b44f-afd9-b65c7a5cf3e1` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01ad7dc48` | `true` | `false` | `true` | `true` | `52838d58-ec30-7adb-256c-53952dea37c4` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T0:L0` | `false` | `true` | `true` | `true` | `52e69070-da86-8f9f-27b2-679f255787a8` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T13:L0` | `true` | `false` | `true` | `true` | `52f55307-770c-ff18-3fb6-7a5efb104701` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T3:L0` | `true` | `false` | `true` | `true` | `521d0c28-1459-755a-6721-d9c20fb39333` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T4:L0` | `true` | `false` | `true` | `true` | `52521303-9b87-0c61-4f80-10bf7d453fe4` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `naa.5000cca01aa800e0` | `true` | `false` | `true` | `true` | `52f3203a-8d60-ad12-eb10-a3abc205084a` |

---

## 4. Step C1: Move `mpx.vmhba0:C2:T5:L0` (T5 (24TB Seagate Exos ST24000NM000C-3W, 1252.0 GiB used))
- **Source Disk Group**: `520e7405-7951-d36b-972f-44fa76ee518e` (`mpx.vmhba0:C2:T1:L0`)
- **Target Disk Group**: `52e69070-da86-8f9f-27b2-679f255787a8` (`mpx.vmhba0:C2:T0:L0`)
- **Remove Start / End (UTC)**: `2026-10-18T04:43:24Z` -> `2026-10-18T14:54:03Z`
- **Remove Command**: `esxcli vsan storage remove -d mpx.vmhba0:C2:T5:L0 -m evacuateAllData`
- **Remove Exit Code**: `rc=0`
- **Remove Output / Stderr**: `(Evacuation completed in hostd at 2026-10-18T14:54:03Z; disk detached from DG 520e7405)`

### Evacuation Telemetry (`mpx.vmhba0:C2:T5:L0`)
- `2026-10-18T04:43:51Z`: `Objects=1`, `GBLeft=339.96` (`365025796096 B`) | `esxi01:T0[Health=OK,Wear=51(raw=889)] | esxi01:T1[Health=OK,Wear=34(raw=1198)]`
- `2026-10-18T06:37:52Z`: `Objects=2`, `GBLeft=896.81` (`962943385600 B`) | `esxi01:T0[Health=OK,Wear=51(raw=890)] | esxi01:T1[Health=OK,Wear=34(raw=1199)]`
- `2026-10-18T11:02:13Z`: `Objects=3`, `GBLeft=808.84` (`868482924544 B`) | `esxi01:T0[Health=OK,Wear=50(raw=893)] | esxi01:T1[Health=OK,Wear=33(raw=1201)]`
- `2026-10-18T14:21:38Z`: `Objects=5`, `GBLeft=1082.86` (`1162709811200 B`) | `esxi01:T0[Health=OK,Wear=50(raw=897)] | esxi01:T1[Health=OK,Wear=33(raw=1204)]`
- `2026-10-18T14:54:03Z`: `T5 (mpx.vmhba0:C2:T5:L0) evacuation 100% complete and removed from DG 520e7405 (remaining 4 objects are Intent: Rebalance)`

### Post-Evacuation Resync Verification (`esxcli vsan debug resync summary get`, `rc=0`)
```text
TotalBytesLeftToResync:         873148960768
TotalGBLeftToResync:            813.18
TotalNumberOfResyncingObjects:  4
```

- **Add Start / End (UTC)**: `2026-10-18T15:17:37Z` -> `2026-10-18T15:18:09Z`
- **Add Command**: `esxcli vsan storage add -s mpx.vmhba0:C2:T0:L0 -d mpx.vmhba0:C2:T5:L0`
- **Add Exit Code**: `rc=0`
- **Add Output / Stderr**: `true`
- **Post-Add Disk Verification**:
  - `Device`: `mpx.vmhba0:C2:T5:L0`
  - `VSANDiskGroupName`: `mpx.vmhba0:C2:T0:L0`
  - `VSANDiskGroupUUID`: `52e69070-da86-8f9f-27b2-679f255787a8`
  - `VSANUUID`: `525ffaa8-33eb-b23b-c59f-09944ca74ecc`
  - `IsMounted`: `true`
  - `InCMMDS`: `true`

### Post-Step C1 Resync Summary (`rc=0`)
```text
TotalBytesLeftToResync:         870261198848
TotalGBLeftToResync:            810.49
TotalNumberOfResyncingObjects:  4
```

### Post-Step C1 SMART Wear Check (`esxi01` `T0`/`T1` & `esxi02` `T8`/`T9`)
### esxi01 T0 (DG 52e69070 Cache) (`mpx.vmhba0:C2:T0:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    50     0          50     897
Write Error Count          100    10         100    0
Power-on Hours             98     0          98     9521
Power Cycle Count          99     0          99     293
Reallocated Sector Count   100    10         100    0
Drive Temperature          49     0          34     51
Write Sectors TOT Count    99     0          99     676367754059
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```
### esxi01 T1 (DG 520e7405 Cache) (`mpx.vmhba0:C2:T1:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    33     0          33     1205
Write Error Count          100    10         100    0
Power-on Hours             98     0          98     9521
Power Cycle Count          99     0          99     297
Reallocated Sector Count   100    10         100    0
Drive Temperature          42     0          34     58
Write Sectors TOT Count    99     0          99     836717156878
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```
### esxi02 T8 (DG 5240e70d Cache) (`mpx.vmhba0:C2:T8:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    24     0          24     1374
Write Error Count          100    10         100    0
Power-on Hours             98     0          98     9616
Power Cycle Count          99     0          99     307
Reallocated Sector Count   100    10         100    0
Drive Temperature          38     0          29     62
Write Sectors TOT Count    99     0          99     1026551667110
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```
### esxi02 T9 (DG 522f2b56 Cache) (`mpx.vmhba0:C2:T9:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    9      0          9      1638
Write Error Count          100    10         100    0
Power-on Hours             95     0          95     24373
Power Cycle Count          98     0          98     1485
Reallocated Sector Count   100    10         100    0
Drive Temperature          35     0          29     65
Write Sectors TOT Count    99     0          99     1231327252681
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```

### Post-Step C1 `esxi01` Disk Group Layout
| Disk Group UUID | Cache SSD | Device | Capacity Tier | IsSSD | Mounted | InCMMDS | VSAN UUID |
|---|---|---|---|---|---|---|---|
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `mpx.vmhba0:C2:T1:L0` | `false` | `true` | `true` | `true` | `520e7405-7951-d36b-972f-44fa76ee518e` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.50000c0f012d3630` | `true` | `false` | `true` | `true` | `522ac7d2-9125-2e27-d90a-e79d5b95d5ff` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.50000c0f01e0c290` | `true` | `false` | `true` | `true` | `52231164-8542-4e47-05b5-c97338472125` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01aa7e5ec` | `true` | `false` | `true` | `true` | `52d55f3a-970a-3363-c37c-98bfd6d162b0` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01aa9a96c` | `true` | `false` | `true` | `true` | `52191fd7-997a-a5a6-c65f-0b325a677220` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01ab112cc` | `true` | `false` | `true` | `true` | `52e03231-e4ca-b44f-afd9-b65c7a5cf3e1` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01ad7dc48` | `true` | `false` | `true` | `true` | `52838d58-ec30-7adb-256c-53952dea37c4` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T0:L0` | `false` | `true` | `true` | `true` | `52e69070-da86-8f9f-27b2-679f255787a8` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T13:L0` | `true` | `false` | `true` | `true` | `52f55307-770c-ff18-3fb6-7a5efb104701` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T3:L0` | `true` | `false` | `true` | `true` | `521d0c28-1459-755a-6721-d9c20fb39333` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T4:L0` | `true` | `false` | `true` | `true` | `52521303-9b87-0c61-4f80-10bf7d453fe4` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T5:L0` | `true` | `false` | `true` | `true` | `525ffaa8-33eb-b23b-c59f-09944ca74ecc` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `naa.5000cca01aa800e0` | `true` | `false` | `true` | `true` | `52f3203a-8d60-ad12-eb10-a3abc205084a` |

---

## 5. Step C2: Move `naa.5000cca01aa800e0` (T10 (3TB Hitachi SAS HUS723030ALS640, 1012.3 GiB used))
- **Source Disk Group**: `52e69070-da86-8f9f-27b2-679f255787a8` (`mpx.vmhba0:C2:T0:L0`)
- **Target Disk Group**: `520e7405-7951-d36b-972f-44fa76ee518e` (`mpx.vmhba0:C2:T1:L0`)
- **Remove Start / End (UTC)**: `2026-10-18T15:18:17Z` -> `2026-10-18T20:57:06Z`
- **Remove Command**: `esxcli vsan storage remove -d naa.5000cca01aa800e0 -m evacuateAllData`
- **Remove Exit Code**: `rc=1`
- **Remove Output / Stderr**: `govc: EsxCLI.CLIFault.summary <obj xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="urn:vim25" versionId="5.0" xsi:type="LocalizedMethodFault"><fault xsi:type="VimEsxCLICLIFault"><errMsg>Unable to remove device: Disk not found: naa.5000cca01aa800e0</errMsg></fault><localizedMessage>EsxCLI.CLIFault.summary</localizedMessage></obj>`

### Evacuation Telemetry (`naa.5000cca01aa800e0`)
- `2026-10-18T15:18:44Z`: `Objects=9`, `GBLeft=1813.46` (`1947190046720 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676371063115] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836723560814] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026551769854] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231336171321]`
- `2026-10-18T15:19:10Z`: `Objects=8`, `GBLeft=1809.27` (`1942688718848 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676375189875] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836729501766] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026551817990] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231338418393]`
- `2026-10-18T15:20:03Z`: `Objects=8`, `GBLeft=1800.64` (`1933427728384 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676383575379] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836740071478] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026551931334] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231351554177]`
- `2026-10-18T15:20:56Z`: `Objects=8`, `GBLeft=1792.67` (`1924861345792 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676392353107] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836751750046] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026552045438] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231359022681]`
- `2026-10-18T15:21:50Z`: `Objects=8`, `GBLeft=1786.47` (`1918212120576 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676400531595] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836761711710] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026552161150] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231367713641]`
- `2026-10-18T15:22:43Z`: `Objects=8`, `GBLeft=1779.06` (`1910255419392 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676406656339] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836772638606] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026552260502] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231372930121]`
- `2026-10-18T15:23:36Z`: `Objects=8`, `GBLeft=1770.25` (`1900787351552 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676415875099] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836783299822] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026552376574] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231376612537]`
- `2026-10-18T15:24:29Z`: `Objects=8`, `GBLeft=1763.15` (`1893172572160 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676422812835] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836794851654] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026552484350] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231381015057]`
- `2026-10-18T15:25:22Z`: `Objects=8`, `GBLeft=1756.75` (`1886296510464 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676431882259] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836803126014] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026552585862] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231384007393]`
- `2026-10-18T15:26:15Z`: `Objects=8`, `GBLeft=1749.40` (`1878399967232 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676440942755] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836811013182] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026552701502] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231395660113]`
- `2026-10-18T15:27:08Z`: `Objects=8`, `GBLeft=1740.96` (`1869341163520 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676449154171] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836821283150] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026552802406] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231413166633]`
- `2026-10-18T15:28:01Z`: `Objects=8`, `GBLeft=1733.32` (`1861141966848 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676457554083] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836830413286] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026552911150] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231418779265]`
- `2026-10-18T15:28:55Z`: `Objects=8`, `GBLeft=1726.37` (`1853680177152 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676466625755] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836838500878] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026553023398] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231422856513]`
- `2026-10-18T15:29:48Z`: `Objects=8`, `GBLeft=1720.70` (`1847591104512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676475269539] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836847788174] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026553155502] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231426933081]`
- `2026-10-18T15:30:41Z`: `Objects=8`, `GBLeft=1712.51` (`1838795333632 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676485513259] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836858096686] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026553284630] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231431679673]`
- `2026-10-18T15:31:34Z`: `Objects=8`, `GBLeft=1704.49` (`1830179520512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676497426187] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836866732542] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026555184278] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231434894497]`
- `2026-10-18T15:32:28Z`: `Objects=8`, `GBLeft=1698.95` (`1824234418176 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676509949691] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836877444782] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026560274046] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231440947433]`
- `2026-10-18T15:33:21Z`: `Objects=8`, `GBLeft=1693.52` (`1818406830080 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676520553867] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836887721358] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026561680366] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231444518905]`
- `2026-10-18T15:34:14Z`: `Objects=8`, `GBLeft=1685.81` (`1810129674240 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676530712547] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836895284102] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026561810246] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231451261849]`
- `2026-10-18T15:35:07Z`: `Objects=8`, `GBLeft=1677.82` (`1801542250496 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676539882715] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836903141926] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026561976230] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231457110409]`
- `2026-10-18T15:36:00Z`: `Objects=8`, `GBLeft=1670.30` (`1793468915712 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676550569443] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836912260638] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026563103974] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231463621705]`
- `2026-10-18T15:36:53Z`: `Objects=8`, `GBLeft=1662.89` (`1785509470208 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676560781411] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836922909190] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026563253526] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231474986009]`
- `2026-10-18T15:37:46Z`: `Objects=8`, `GBLeft=1654.35` (`1776348594176 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676571454739] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836933179182] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026563534654] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231481788897]`
- `2026-10-18T15:38:40Z`: `Objects=8`, `GBLeft=1646.88` (`1768329220096 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676582152187] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836940463110] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026564119558] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231489772753]`
- `2026-10-18T15:39:33Z`: `Objects=8`, `GBLeft=1639.38` (`1760273903616 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676592705443] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836951246502] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026564246838] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231499847825]`
- `2026-10-18T15:40:27Z`: `Objects=8`, `GBLeft=1633.43` (`1753886134272 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676603021027] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836957104078] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026564403502] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231509735449]`
- `2026-10-18T15:41:20Z`: `Objects=8`, `GBLeft=1627.90` (`1747947999232 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676611895267] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836966082966] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026564624654] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231516500929]`
- `2026-10-18T15:42:13Z`: `Objects=8`, `GBLeft=1620.96` (`1740487192576 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676621892283] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836973747118] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026564839366] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231525536065]`
- `2026-10-18T15:43:06Z`: `Objects=8`, `GBLeft=1613.24` (`1732199596032 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676631220715] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836981282670] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026565415750] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231539669465]`
- `2026-10-18T15:44:00Z`: `Objects=8`, `GBLeft=1607.56` (`1726108499968 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676639106531] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836989322158] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026565564462] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231543484329]`
- `2026-10-18T15:44:53Z`: `Objects=8`, `GBLeft=1602.14` (`1720289075200 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676649716851] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=836996331950] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026565726038] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231550130385]`
- `2026-10-18T15:45:46Z`: `Objects=8`, `GBLeft=1594.90` (`1712514367488 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676659692091] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837004240102] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026565833990] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231553647145]`
- `2026-10-18T15:46:39Z`: `Objects=8`, `GBLeft=1587.08` (`1704111710208 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676669192691] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837011728534] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026566002422] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231560468169]`
- `2026-10-18T15:47:35Z`: `Objects=8`, `GBLeft=1580.56` (`1697109573632 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676678102979] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837020560278] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026566284518] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231563511393]`
- `2026-10-18T15:49:04Z`: `Objects=8`, `GBLeft=1570.87` (`1686712963072 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676689737507] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837034830070] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026566464678] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231575080465]`
- `2026-10-18T15:50:06Z`: `Objects=8`, `GBLeft=1564.07` (`1679403499520 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676695998763] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837043858598] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026566578342] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231595512569]`
- `2026-10-18T15:51:08Z`: `Objects=8`, `GBLeft=1557.08` (`1671904440320 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676705601931] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837053172694] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026566703502] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231626833457]`
- `2026-10-18T15:52:11Z`: `Objects=8`, `GBLeft=1548.90` (`1663118643200 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676714342595] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837064388110] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026566820126] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231647889545]`
- `2026-10-18T15:53:13Z`: `Objects=8`, `GBLeft=1540.80` (`1654424653824 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676725609387] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837074865846] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026566943486] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231663931529]`
- `2026-10-18T15:54:15Z`: `Objects=8`, `GBLeft=1532.49` (`1645495201792 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676736732787] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837084622798] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026567075742] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231675420313]`
- `2026-10-18T15:55:17Z`: `Objects=8`, `GBLeft=1524.80` (`1637237215232 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676747613347] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837092327462] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026567204662] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231682487913]`
- `2026-10-18T15:56:20Z`: `Objects=8`, `GBLeft=1516.36` (`1628181676032 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676759420755] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837101196374] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026567332830] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231689427985]`
- `2026-10-18T15:57:22Z`: `Objects=8`, `GBLeft=1508.66` (`1619916554240 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676769235203] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837109182870] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026567444110] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231699021425]`
- `2026-10-18T15:58:24Z`: `Objects=8`, `GBLeft=1502.06` (`1612826103808 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676778006483] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837118211758] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026567582750] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231710520081]`
- `2026-10-18T15:59:27Z`: `Objects=7`, `GBLeft=1493.00` (`1603096866816 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676786281083] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837127669758] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026567697206] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231736933481]`
- `2026-10-18T16:00:29Z`: `Objects=7`, `GBLeft=1484.48` (`1593946894336 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676798245203] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837138149462] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026567815862] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231761486841]`
- `2026-10-18T16:01:31Z`: `Objects=6`, `GBLeft=1475.46` (`1584259264512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676809425387] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837147792166] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026567968702] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231775892297]`
- `2026-10-18T16:02:34Z`: `Objects=6`, `GBLeft=1467.12` (`1575304294400 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676820527611] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837156963038] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026568082150] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231803191217]`
- `2026-10-18T16:03:36Z`: `Objects=6`, `GBLeft=1458.83` (`1566402007040 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676831990323] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837165716670] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026568209030] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231826545921]`
- `2026-10-18T16:04:38Z`: `Objects=6`, `GBLeft=1450.26` (`1557199622144 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676843380851] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837174736062] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026568293198] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231846144737]`
- `2026-10-18T16:05:40Z`: `Objects=6`, `GBLeft=1441.52` (`1547823308800 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676854717747] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837183738374] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026568408166] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231870510329]`
- `2026-10-18T16:06:42Z`: `Objects=6`, `GBLeft=1433.33` (`1539029655552 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676865517963] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837192391910] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026568561510] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231897857169]`
- `2026-10-18T16:07:45Z`: `Objects=6`, `GBLeft=1425.99` (`1531143823360 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676878158867] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837200534070] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026568776198] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231921587985]`
- `2026-10-18T16:08:47Z`: `Objects=6`, `GBLeft=1419.70` (`1524391313408 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676890324051] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837206763182] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026568973670] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231941513921]`
- `2026-10-18T16:09:49Z`: `Objects=6`, `GBLeft=1412.07` (`1516193857536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676902251331] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837213941678] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026569305742] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231964004257]`
- `2026-10-18T16:10:51Z`: `Objects=6`, `GBLeft=1405.85` (`1509519003648 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676919734163] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837219972110] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026569578398] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1231986097473]`
- `2026-10-18T16:11:54Z`: `Objects=6`, `GBLeft=1398.49` (`1501613748224 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676931913707] | esxi01:T1[Health=OK,Wear=33(raw=1205),WrSec=837226766206] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026569752526] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1232006362489]`
- `2026-10-18T16:12:56Z`: `Objects=6`, `GBLeft=1388.45` (`1490836164608 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676943338931] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837233759846] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026569952022] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1232025037625]`
- `2026-10-18T16:13:59Z`: `Objects=6`, `GBLeft=1380.80` (`1482622742528 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676954803483] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837241414006] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026570121718] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1232049385449]`
- `2026-10-18T16:15:01Z`: `Objects=6`, `GBLeft=1373.00` (`1474248871936 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676966625443] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837249252806] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026570253014] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1232063297241]`
- `2026-10-18T16:16:03Z`: `Objects=6`, `GBLeft=1365.00` (`1465662517248 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676978486571] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837257460982] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026570423678] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1232080311953]`
- `2026-10-18T16:17:05Z`: `Objects=6`, `GBLeft=1356.17` (`1456180961280 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=676990296147] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837266651126] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026570577870] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1232097594545]`
- `2026-10-18T16:18:08Z`: `Objects=6`, `GBLeft=1347.71` (`1447090024448 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677002200723] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837276224638] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026570819270] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1232116074225]`
- `2026-10-18T16:19:10Z`: `Objects=6`, `GBLeft=1339.43` (`1438200680448 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677014170219] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837284192806] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026571009774] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1232139008097]`
- `2026-10-18T16:20:12Z`: `Objects=6`, `GBLeft=1330.95` (`1429100134400 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677025739331] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837292852478] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026571102894] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1232151394585]`
- `2026-10-18T16:21:14Z`: `Objects=6`, `GBLeft=1322.83` (`1420372553728 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677037266131] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837301463590] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026571215094] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1232164680545]`
- `2026-10-18T16:22:16Z`: `Objects=6`, `GBLeft=1314.15` (`1411062743040 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677049093003] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837310733966] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026571348990] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1232176012505]`
- `2026-10-18T16:23:18Z`: `Objects=6`, `GBLeft=1305.62` (`1401898266624 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677060392995] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837319787422] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026571492342] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1232189617569]`
- `2026-10-18T16:24:21Z`: `Objects=6`, `GBLeft=1297.35` (`1393024032768 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677073362587] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837328115278] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026571674070] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1232204066297]`
- `2026-10-18T16:25:23Z`: `Objects=6`, `GBLeft=1289.30` (`1384375631872 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677085452867] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837336051726] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026571865558] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1232221163385]`
- `2026-10-18T16:26:25Z`: `Objects=6`, `GBLeft=1281.09` (`1375558197248 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677097412715] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837345475110] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026572100094] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1232238608753]`
- `2026-10-18T16:27:27Z`: `Objects=6`, `GBLeft=1269.60` (`1363219505152 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677109565507] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837354254998] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026572282150] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1232257306185]`
- `2026-10-18T16:28:30Z`: `Objects=6`, `GBLeft=1261.05` (`1354040676352 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677121832491] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837363276518] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026572468214] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1232277117017]`
- `2026-10-18T16:29:32Z`: `Objects=6`, `GBLeft=1252.90` (`1345292472320 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677133934259] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837370820038] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026572624398] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1232292752721]`
- `2026-10-18T16:30:34Z`: `Objects=6`, `GBLeft=1244.97` (`1336779726848 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677146184283] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837378529406] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026572787990] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1232305098553]`
- `2026-10-18T16:31:37Z`: `Objects=6`, `GBLeft=1236.69` (`1327884849152 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677158121963] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837387240934] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026572911406] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1232322625185]`
- `2026-10-18T16:32:39Z`: `Objects=6`, `GBLeft=1227.81` (`1318347304960 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677170402915] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837396081390] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026572948550] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1232341188617]`
- `2026-10-18T16:33:41Z`: `Objects=6`, `GBLeft=1219.52` (`1309449125888 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677181992203] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837406167718] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026572988142] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1232358847745]`
- `2026-10-18T16:34:43Z`: `Objects=6`, `GBLeft=1210.76` (`1300039327744 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677193654659] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837414995966] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026573026134] | esxi02:T9[Health=OK,Wear=9(raw=1638),WrSec=1232377982209]`
- `2026-10-18T16:35:45Z`: `Objects=6`, `GBLeft=1202.29` (`1290951942144 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677205606483] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837424272910] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026573076054] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232399892433]`
- `2026-10-18T16:36:48Z`: `Objects=6`, `GBLeft=1193.65` (`1281674481664 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677217576651] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837435416350] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026573124782] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232420144761]`
- `2026-10-18T16:37:50Z`: `Objects=6`, `GBLeft=1184.25` (`1271582789632 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677229416067] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837445614214] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026573164262] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232438678625]`
- `2026-10-18T16:38:52Z`: `Objects=6`, `GBLeft=1175.47` (`1262151589888 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677241315603] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837455196486] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026573215878] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232460103681]`
- `2026-10-18T16:39:55Z`: `Objects=6`, `GBLeft=1167.09` (`1253148209152 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677253153987] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837464999974] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026573334118] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232481513985]`
- `2026-10-18T16:40:57Z`: `Objects=6`, `GBLeft=1159.01` (`1244474290176 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677265318467] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837473816918] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026573387358] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232502659217]`
- `2026-10-18T16:41:59Z`: `Objects=6`, `GBLeft=1148.27` (`1232949010432 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677277886051] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837482311694] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026573444374] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232524118289]`
- `2026-10-18T16:43:01Z`: `Objects=6`, `GBLeft=1139.53` (`1223563460608 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677289934947] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837490746358] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026573490158] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232542152521]`
- `2026-10-18T16:44:03Z`: `Objects=6`, `GBLeft=1131.05` (`1214452617216 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677302316571] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837499838638] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026573528054] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232561837297]`
- `2026-10-18T16:45:06Z`: `Objects=6`, `GBLeft=1122.65` (`1205437509632 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677314571779] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837507528102] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026573564310] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232584638577]`
- `2026-10-18T16:46:08Z`: `Objects=6`, `GBLeft=1115.16` (`1197391212544 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677325724427] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837515017094] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026573614814] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232594649153]`
- `2026-10-18T16:47:10Z`: `Objects=6`, `GBLeft=1107.32` (`1188972408832 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677337754107] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837522428774] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026573652046] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232606101809]`
- `2026-10-18T16:48:12Z`: `Objects=6`, `GBLeft=1098.57` (`1179580121088 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677350187811] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837531035022] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026573689782] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232610537633]`
- `2026-10-18T16:49:14Z`: `Objects=6`, `GBLeft=1089.51` (`1169848369152 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677362533811] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837540127582] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026573730494] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232610838529]`
- `2026-10-18T16:50:16Z`: `Objects=6`, `GBLeft=1081.07` (`1160794976256 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677374652963] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837548722078] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026573773510] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232611054305]`
- `2026-10-18T16:51:18Z`: `Objects=6`, `GBLeft=1072.81` (`1151920758784 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677386510299] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837557355446] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026573815694] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232611278065]`
- `2026-10-18T16:52:20Z`: `Objects=6`, `GBLeft=1064.50` (`1143001427968 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677398225539] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837565705366] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026573870814] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232611724969]`
- `2026-10-18T16:53:23Z`: `Objects=6`, `GBLeft=1055.91` (`1133779435520 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677409785075] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837575093254] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026573908038] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232612045713]`
- `2026-10-18T16:54:25Z`: `Objects=6`, `GBLeft=1047.71` (`1124972761088 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677421556443] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837583114046] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026573951126] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232612390801]`
- `2026-10-18T16:55:27Z`: `Objects=6`, `GBLeft=1040.73` (`1117470257152 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677434374083] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837590339230] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026574050718] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232612643569]`
- `2026-10-18T16:56:29Z`: `Objects=6`, `GBLeft=1032.48` (`1108621123584 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677448843083] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837597920422] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026574365398] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232613158793]`
- `2026-10-18T16:57:31Z`: `Objects=6`, `GBLeft=1026.16` (`1101831483392 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677461913499] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837605661606] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026574412782] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232613472897]`
- `2026-10-18T16:58:33Z`: `Objects=6`, `GBLeft=1019.87` (`1095081734144 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677474039059] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837612610286] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026574457398] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232613833681]`
- `2026-10-18T16:59:35Z`: `Objects=6`, `GBLeft=1013.76` (`1088515231744 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677485994587] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837620025734] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026574492494] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232614198217]`
- `2026-10-18T17:00:37Z`: `Objects=6`, `GBLeft=1006.61` (`1080834801664 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677498505307] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837625922398] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026574535134] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232614498833]`
- `2026-10-18T17:01:40Z`: `Objects=6`, `GBLeft=1000.54` (`1074324803584 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677507539163] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837632646502] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026574574630] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232614722337]`
- `2026-10-18T17:02:42Z`: `Objects=6`, `GBLeft=993.08` (`1066313388032 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677519179867] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837640319070] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026574610814] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232614991465]`
- `2026-10-18T17:03:44Z`: `Objects=6`, `GBLeft=985.25` (`1057899978752 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677530437515] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837648744782] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026574649078] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232615217697]`
- `2026-10-18T17:04:46Z`: `Objects=6`, `GBLeft=977.03` (`1049083289600 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677540795667] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837657516166] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026574685422] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232615471953]`
- `2026-10-18T17:05:48Z`: `Objects=6`, `GBLeft=968.72` (`1040158269440 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677551674643] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837667145158] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026574832438] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232615915641]`
- `2026-10-18T17:06:50Z`: `Objects=6`, `GBLeft=960.60` (`1031434874880 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677562793667] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837676120310] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026574877038] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232616185385]`
- `2026-10-18T17:07:52Z`: `Objects=6`, `GBLeft=952.52` (`1022764097536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677575810539] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837684663534] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026574913230] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232616469913]`
- `2026-10-18T17:08:54Z`: `Objects=6`, `GBLeft=944.44` (`1014085115904 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677585772811] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837692698182] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026574952022] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232622127217]`
- `2026-10-18T17:09:56Z`: `Objects=6`, `GBLeft=938.12` (`1007295926272 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677595239339] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837699925942] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026574986926] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232629982937]`
- `2026-10-18T17:10:58Z`: `Objects=6`, `GBLeft=929.72` (`998277255168 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677605907451] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837707217678] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026575034686] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232637011473]`
- `2026-10-18T17:12:01Z`: `Objects=6`, `GBLeft=920.69` (`988580179968 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677617038851] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837714135566] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026575071542] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232644885641]`
- `2026-10-18T17:13:03Z`: `Objects=5`, `GBLeft=913.90` (`981297385472 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677627766747] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837720094246] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026575107294] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232655170057]`
- `2026-10-18T17:14:05Z`: `Objects=5`, `GBLeft=907.42` (`974340108288 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677638010203] | esxi01:T1[Health=OK,Wear=33(raw=1206),WrSec=837725787878] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026575171310] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232664775537]`
- `2026-10-18T17:15:07Z`: `Objects=5`, `GBLeft=901.44` (`967908720640 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677647053507] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837731755054] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026575220358] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232675074185]`
- `2026-10-18T17:16:09Z`: `Objects=5`, `GBLeft=894.33` (`960280064000 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677657504563] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837740177462] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026575273398] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232685777505]`
- `2026-10-18T17:17:11Z`: `Objects=5`, `GBLeft=887.00` (`952404217856 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677667773507] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837747934374] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026575320486] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232701997249]`
- `2026-10-18T17:18:14Z`: `Objects=5`, `GBLeft=879.38` (`944227102720 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677677153675] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837757286966] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026575363150] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232715498537]`
- `2026-10-18T17:19:16Z`: `Objects=5`, `GBLeft=871.67` (`935945576448 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677687365435] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837766314518] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026575401510] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232722161953]`
- `2026-10-18T17:20:18Z`: `Objects=5`, `GBLeft=864.05` (`927768145920 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677697404339] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837774984566] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026575441342] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232739957201]`
- `2026-10-18T17:21:20Z`: `Objects=5`, `GBLeft=856.25` (`919386669056 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677708263659] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837783893094] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026575484886] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232749426697]`
- `2026-10-18T17:22:22Z`: `Objects=5`, `GBLeft=847.73` (`910242168832 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677718505243] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837792698550] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026575523350] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232762912985]`
- `2026-10-18T17:23:24Z`: `Objects=5`, `GBLeft=839.94` (`901877678080 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677729540883] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837800917526] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026575560662] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232781366329]`
- `2026-10-18T17:24:26Z`: `Objects=5`, `GBLeft=832.28` (`893648732160 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677740014019] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837809874998] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026575596382] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232783481945]`
- `2026-10-18T17:25:28Z`: `Objects=5`, `GBLeft=824.46` (`885253234688 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677750453459] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837818915422] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026575648478] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232783808137]`
- `2026-10-18T17:26:30Z`: `Objects=5`, `GBLeft=814.51` (`874577600512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677761138987] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837827078134] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026575681766] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232786442961]`
- `2026-10-18T17:27:32Z`: `Objects=5`, `GBLeft=806.93` (`866437836800 B`) | `esxi01:T0[Health=OK,Wear=50(raw=898),WrSec=677771332051] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837835421798] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026575717294] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232789931513]`
- `2026-10-18T17:28:34Z`: `Objects=5`, `GBLeft=799.27` (`858207870976 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=677780897619] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837844402534] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026575752902] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232794301185]`
- `2026-10-18T17:29:36Z`: `Objects=5`, `GBLeft=791.36` (`849711808512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=677791883019] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837853503198] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026575790350] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232805611193]`
- `2026-10-18T17:30:38Z`: `Objects=5`, `GBLeft=783.38` (`841151590400 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=677802014171] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837863105246] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026575837766] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232815564873]`
- `2026-10-18T17:31:40Z`: `Objects=5`, `GBLeft=775.43` (`832610779136 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=677812196667] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837874127214] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026575905366] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232819377433]`
- `2026-10-18T17:32:42Z`: `Objects=5`, `GBLeft=767.61` (`824216780800 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=677823397579] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837883763862] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026575973646] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232822971017]`
- `2026-10-18T17:33:44Z`: `Objects=5`, `GBLeft=759.45` (`815454662656 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=677833463627] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837894875126] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026576015670] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232826505049]`
- `2026-10-18T17:34:46Z`: `Objects=5`, `GBLeft=750.38` (`805716672512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=677844765915] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837906465182] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026576067734] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232830497153]`
- `2026-10-18T17:35:48Z`: `Objects=5`, `GBLeft=742.83` (`797610737664 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=677861364451] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837917891774] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026576462174] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232834137881]`
- `2026-10-18T17:36:50Z`: `Objects=5`, `GBLeft=735.32` (`789542801408 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=677875634579] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837928744958] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026576610094] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232837737233]`
- `2026-10-18T17:37:52Z`: `Objects=5`, `GBLeft=727.74` (`781403533312 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=677887055595] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837937107262] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026576655222] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232839902193]`
- `2026-10-18T17:38:54Z`: `Objects=5`, `GBLeft=720.34` (`773457408000 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=677897200635] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837945304598] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026576689806] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232855905169]`
- `2026-10-18T17:39:56Z`: `Objects=5`, `GBLeft=712.33` (`764856799232 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=677907320523] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837953549390] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026576725758] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232871023961]`
- `2026-10-18T17:40:58Z`: `Objects=5`, `GBLeft=705.33` (`757342806016 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=677917013867] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837960978878] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026576769534] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232875715065]`
- `2026-10-18T17:42:01Z`: `Objects=5`, `GBLeft=696.22` (`747556823040 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=677927395419] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837967660782] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026576818206] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232878338313]`
- `2026-10-18T17:43:03Z`: `Objects=5`, `GBLeft=689.07` (`739885772800 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=677938042155] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837975485990] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026576862750] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232881623441]`
- `2026-10-18T17:44:05Z`: `Objects=5`, `GBLeft=681.86` (`732138348544 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=677949212163] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837982956950] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026576914246] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232884943593]`
- `2026-10-18T17:45:07Z`: `Objects=5`, `GBLeft=674.57` (`724315078656 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=677959608979] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837990739790] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026576998134] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232890852265]`
- `2026-10-18T17:46:09Z`: `Objects=5`, `GBLeft=667.45` (`716664819712 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=677970289051] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=837998053030] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026577039350] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232894095553]`
- `2026-10-18T17:47:11Z`: `Objects=5`, `GBLeft=660.17` (`708851228672 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=677980478867] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838006595334] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026577076470] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232897359377]`
- `2026-10-18T17:48:13Z`: `Objects=5`, `GBLeft=652.49` (`700604932096 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=677990964859] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838015452342] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026577115766] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232900678297]`
- `2026-10-18T17:49:16Z`: `Objects=5`, `GBLeft=644.68` (`692223590400 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678001534451] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838024755686] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026577153198] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232904010017]`
- `2026-10-18T17:50:18Z`: `Objects=5`, `GBLeft=636.94` (`683904749568 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678012866275] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838031889766] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026577192190] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232907261633]`
- `2026-10-18T17:51:20Z`: `Objects=5`, `GBLeft=629.16` (`675555000320 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678023884771] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838039199542] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026577238310] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232910614945]`
- `2026-10-18T17:52:22Z`: `Objects=5`, `GBLeft=621.69` (`667532079104 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678034788259] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838046434662] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026577275334] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232914349137]`
- `2026-10-18T17:53:24Z`: `Objects=5`, `GBLeft=614.39` (`659695661056 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678045301427] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838053922582] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026577311638] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232917813561]`
- `2026-10-18T17:54:26Z`: `Objects=5`, `GBLeft=606.96` (`651720482816 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678056419507] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838061724822] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026577347606] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232918797969]`
- `2026-10-18T17:55:28Z`: `Objects=5`, `GBLeft=599.13` (`643306582016 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678067403003] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838069133446] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026577401422] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232925524041]`
- `2026-10-18T17:56:30Z`: `Objects=5`, `GBLeft=591.71` (`635348570112 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678079996603] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838076174886] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026577436334] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232935083433]`
- `2026-10-18T17:57:32Z`: `Objects=5`, `GBLeft=580.48` (`623284563968 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678092298955] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838083275598] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026577474086] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232944536329]`
- `2026-10-18T17:58:34Z`: `Objects=5`, `GBLeft=572.25` (`614443646976 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678105143595] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838090414534] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026577510694] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232954316961]`
- `2026-10-18T17:59:36Z`: `Objects=5`, `GBLeft=563.92` (`605504557056 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678117784483] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838097965598] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026577546278] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232958277449]`
- `2026-10-18T18:00:38Z`: `Objects=5`, `GBLeft=555.43` (`596389249024 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678131175459] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838105531030] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026577588334] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232962583841]`
- `2026-10-18T18:01:40Z`: `Objects=5`, `GBLeft=546.30` (`586581921792 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678145678227] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838112614174] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026577625958] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232966927121]`
- `2026-10-18T18:02:42Z`: `Objects=5`, `GBLeft=537.18` (`576789508096 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678159789707] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838119625054] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026577661822] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232976831785]`
- `2026-10-18T18:03:44Z`: `Objects=5`, `GBLeft=528.54` (`567520550912 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678173274083] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838127405902] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026577702782] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232987040865]`
- `2026-10-18T18:04:46Z`: `Objects=5`, `GBLeft=519.38` (`557677015040 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678187383459] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838135551510] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026577741046] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1232997563137]`
- `2026-10-18T18:05:48Z`: `Objects=5`, `GBLeft=510.62` (`548272193536 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678201187371] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838142915470] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026577788798] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233010649129]`
- `2026-10-18T18:06:50Z`: `Objects=5`, `GBLeft=501.64` (`538636369920 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678215368571] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838149775670] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026577826726] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233025888265]`
- `2026-10-18T18:07:52Z`: `Objects=5`, `GBLeft=494.78` (`531270684672 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678220062347] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838157061878] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026577907454] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233036313849]`
- `2026-10-18T18:08:54Z`: `Objects=5`, `GBLeft=491.50` (`527739731968 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678220218587] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838164720486] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026578019910] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233043784977]`
- `2026-10-18T18:09:56Z`: `Objects=5`, `GBLeft=488.56` (`524583309312 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678220452107] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838172082718] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026578079358] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233047227793]`
- `2026-10-18T18:10:58Z`: `Objects=5`, `GBLeft=485.48` (`521280806912 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678220831035] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838179495870] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026578150286] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233053818169]`
- `2026-10-18T18:12:00Z`: `Objects=5`, `GBLeft=482.19` (`517746778112 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678221023139] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838187230414] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026578187022] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233060298497]`
- `2026-10-18T18:13:02Z`: `Objects=5`, `GBLeft=479.07` (`514395684864 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678221177243] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838194656406] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026578239990] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233067272073]`
- `2026-10-18T18:14:03Z`: `Objects=5`, `GBLeft=475.83` (`510923239424 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678221331891] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838203203030] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026578389110] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233074252169]`
- `2026-10-18T18:15:05Z`: `Objects=5`, `GBLeft=471.93` (`506731126784 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678221482707] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838211338462] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026578499518] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233081284913]`
- `2026-10-18T18:16:07Z`: `Objects=5`, `GBLeft=468.83` (`503401897984 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678221650779] | esxi01:T1[Health=OK,Wear=33(raw=1207),WrSec=838218458758] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026578640206] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233088367465]`
- `2026-10-18T18:17:09Z`: `Objects=5`, `GBLeft=465.83` (`500179955712 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678221821331] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838225437182] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026578820766] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233095388633]`
- `2026-10-18T18:18:11Z`: `Objects=5`, `GBLeft=462.88` (`497011363840 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678222009619] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838232798342] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026578954382] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233102448241]`
- `2026-10-18T18:19:13Z`: `Objects=5`, `GBLeft=459.92` (`493838315520 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678222175219] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838239729182] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026579073806] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233109430737]`
- `2026-10-18T18:20:15Z`: `Objects=5`, `GBLeft=457.28` (`490996588544 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678222379019] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838246103430] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026579156430] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233116536593]`
- `2026-10-18T18:21:17Z`: `Objects=5`, `GBLeft=454.42` (`487924965376 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678222554139] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838252506278] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026579242358] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233123659993]`
- `2026-10-18T18:22:19Z`: `Objects=5`, `GBLeft=451.20` (`484467802112 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678222741835] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838260625638] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026579279550] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233130589281]`
- `2026-10-18T18:23:20Z`: `Objects=5`, `GBLeft=447.92` (`480955518976 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678222920891] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838269099334] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026579317830] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233132958625]`
- `2026-10-18T18:24:22Z`: `Objects=5`, `GBLeft=444.43` (`477197959168 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678223084947] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838277119134] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026579351390] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233138003841]`
- `2026-10-18T18:25:24Z`: `Objects=5`, `GBLeft=441.43` (`473978888192 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678223264859] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838284262022] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026579454814] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233143628249]`
- `2026-10-18T18:26:26Z`: `Objects=5`, `GBLeft=438.40` (`470732152832 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678223426027] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838291259214] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026579526830] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233149508953]`
- `2026-10-18T18:27:28Z`: `Objects=5`, `GBLeft=435.43` (`467535040512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678223584259] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838298625838] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026579622502] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233155481121]`
- `2026-10-18T18:28:30Z`: `Objects=5`, `GBLeft=432.54` (`464439119872 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678223744075] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838305788230] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026579660350] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233161706569]`
- `2026-10-18T18:29:32Z`: `Objects=5`, `GBLeft=429.25` (`460908695552 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678223917091] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838312673030] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026579696854] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233167576377]`
- `2026-10-18T18:30:34Z`: `Objects=5`, `GBLeft=426.42` (`457860001792 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678224151547] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838319629126] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026579739934] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233173386825]`
- `2026-10-18T18:31:36Z`: `Objects=5`, `GBLeft=423.46` (`454685151232 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678224349539] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838326641934] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026579779198] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233179668033]`
- `2026-10-18T18:32:37Z`: `Objects=5`, `GBLeft=420.50` (`451509932032 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678224567843] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838334050574] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026579817998] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233186917977]`
- `2026-10-18T18:33:39Z`: `Objects=5`, `GBLeft=417.58` (`448370102272 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678225353387] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838342167030] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026579885470] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233194558537]`
- `2026-10-18T18:34:42Z`: `Objects=5`, `GBLeft=413.58` (`444079587328 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678225589755] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838351157550] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026579924102] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233200923801]`
- `2026-10-18T18:35:43Z`: `Objects=5`, `GBLeft=410.25` (`440500080640 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678225784067] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838358761126] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026579973038] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233208979785]`
- `2026-10-18T18:36:45Z`: `Objects=5`, `GBLeft=406.94` (`436949053440 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678225983259] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838367335678] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026580010854] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233220500857]`
- `2026-10-18T18:37:47Z`: `Objects=5`, `GBLeft=403.47` (`433223913472 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678226209427] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838376250742] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026580047374] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233232538849]`
- `2026-10-18T18:38:49Z`: `Objects=5`, `GBLeft=399.79` (`429276286976 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678226454779] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838385189934] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026580086694] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233245984721]`
- `2026-10-18T18:39:51Z`: `Objects=5`, `GBLeft=396.14` (`425354997760 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678226670939] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838394651542] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026580131102] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233252979009]`
- `2026-10-18T18:40:53Z`: `Objects=5`, `GBLeft=392.10` (`421017714688 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678226947619] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838403998438] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026580183246] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233256890433]`
- `2026-10-18T18:41:54Z`: `Objects=5`, `GBLeft=387.77` (`416361054208 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678227202635] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838414231054] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026580221702] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233263886681]`
- `2026-10-18T18:42:56Z`: `Objects=5`, `GBLeft=383.97` (`412283838464 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678227380459] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838422777486] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026580260790] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233271035561]`
- `2026-10-18T18:43:58Z`: `Objects=5`, `GBLeft=380.35` (`408398954496 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678227773835] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838432638646] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026580302534] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233278300841]`
- `2026-10-18T18:45:00Z`: `Objects=5`, `GBLeft=377.03` (`404829863936 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678228196563] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838440959054] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026580340502] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233285479545]`
- `2026-10-18T18:46:02Z`: `Objects=5`, `GBLeft=373.74` (`401301557248 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678228397675] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838449208038] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026580389310] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233292900769]`
- `2026-10-18T18:47:03Z`: `Objects=5`, `GBLeft=370.11` (`397401542656 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678228600171] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838458870134] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026580424670] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233300267289]`
- `2026-10-18T18:48:05Z`: `Objects=5`, `GBLeft=366.43` (`393454182400 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678228770067] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838467321774] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026580463510] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233307775393]`
- `2026-10-18T18:49:07Z`: `Objects=5`, `GBLeft=362.53` (`389264384000 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678230210795] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838476469646] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026580513366] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233315560649]`
- `2026-10-18T18:50:09Z`: `Objects=5`, `GBLeft=361.06` (`387690299392 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678231170579] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838482862734] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026580572638] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233325921465]`
- `2026-10-18T18:51:11Z`: `Objects=5`, `GBLeft=359.52` (`386033623040 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678232020603] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838490346030] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026580631102] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233340303009]`
- `2026-10-18T18:52:13Z`: `Objects=5`, `GBLeft=356.89` (`383205613568 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678233450787] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838496454742] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026580788574] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233353909665]`
- `2026-10-18T18:53:15Z`: `Objects=5`, `GBLeft=354.23` (`380347916288 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678233693915] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838503385110] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026580999638] | esxi02:T9[Health=OK,Wear=9(raw=1639),WrSec=1233361662513]`
- `2026-10-18T18:54:17Z`: `Objects=5`, `GBLeft=351.17` (`377070579712 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678233936003] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838510363742] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026593820878] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233381860257]`
- `2026-10-18T18:55:19Z`: `Objects=5`, `GBLeft=348.45` (`374141120512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678234155443] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838517251758] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026601943998] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233401972425]`
- `2026-10-18T18:56:21Z`: `Objects=5`, `GBLeft=345.63` (`371114012672 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678234347595] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838524783142] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026614317134] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233419023297]`
- `2026-10-18T18:57:23Z`: `Objects=5`, `GBLeft=342.64` (`367911596032 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678234519579] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838532035606] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026624738718] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233429655177]`
- `2026-10-18T18:58:25Z`: `Objects=5`, `GBLeft=339.91` (`364970835968 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678234699235] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838538675742] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026624777534] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233445277121]`
- `2026-10-18T18:59:27Z`: `Objects=5`, `GBLeft=337.14` (`362000089088 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678234876347] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838545510222] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026624828430] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233459936457]`
- `2026-10-18T19:00:29Z`: `Objects=5`, `GBLeft=334.09` (`358728929280 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678235085139] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838551882718] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026633795238] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233473932697]`
- `2026-10-18T19:01:31Z`: `Objects=5`, `GBLeft=331.15` (`355569811456 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678235261891] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838558733478] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026638948446] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233482800833]`
- `2026-10-18T19:02:33Z`: `Objects=5`, `GBLeft=328.23` (`352436207616 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678235449011] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838566224102] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026648614334] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233492408457]`
- `2026-10-18T19:03:35Z`: `Objects=5`, `GBLeft=325.20` (`349185294336 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678235597555] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838573483350] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026660594574] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233501924617]`
- `2026-10-18T19:04:36Z`: `Objects=5`, `GBLeft=322.18` (`345935273984 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678235753211] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838580561182] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026667459318] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233511699625]`
- `2026-10-18T19:05:39Z`: `Objects=5`, `GBLeft=319.53` (`343097614336 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678236047779] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838588753158] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026672520358] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233522610465]`
- `2026-10-18T19:06:41Z`: `Objects=5`, `GBLeft=316.35` (`339676868608 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678236284731] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838595948334] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026677246102] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233534250161]`
- `2026-10-18T19:07:43Z`: `Objects=5`, `GBLeft=313.33` (`336434278400 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678236502347] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838603742542] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026678826014] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233545622801]`
- `2026-10-18T19:08:45Z`: `Objects=5`, `GBLeft=310.28` (`333165182976 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678236680107] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838610974166] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026684138134] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233558578793]`
- `2026-10-18T19:09:47Z`: `Objects=5`, `GBLeft=307.27` (`329930076160 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678236899931] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838618128462] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026685903990] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233570210857]`
- `2026-10-18T19:10:49Z`: `Objects=5`, `GBLeft=303.97` (`326385233920 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678237139699] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838625777246] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026685958158] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233585554065]`
- `2026-10-18T19:11:51Z`: `Objects=5`, `GBLeft=300.84` (`323027496960 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678237314163] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838633248814] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026685993022] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233600980609]`
- `2026-10-18T19:12:53Z`: `Objects=5`, `GBLeft=297.14` (`319053447168 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678237479211] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838641901878] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026686033638] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233613380377]`
- `2026-10-18T19:13:55Z`: `Objects=5`, `GBLeft=294.09` (`315778940928 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678237670627] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838649033862] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026690847310] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233627407233]`
- `2026-10-18T19:14:57Z`: `Objects=5`, `GBLeft=291.11` (`312581439488 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678237829971] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838656105734] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026703663998] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233638713505]`
- `2026-10-18T19:15:58Z`: `Objects=5`, `GBLeft=288.08` (`309321687040 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678238049515] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838663432902] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026715898894] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233654334241]`
- `2026-10-18T19:17:00Z`: `Objects=5`, `GBLeft=285.14` (`306168750080 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678238215171] | esxi01:T1[Health=OK,Wear=33(raw=1208),WrSec=838670639598] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026728996382] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233670151969]`
- `2026-10-18T19:18:02Z`: `Objects=5`, `GBLeft=281.84` (`302625153024 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678238390227] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838677934470] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026744964838] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233681712657]`
- `2026-10-18T19:19:04Z`: `Objects=5`, `GBLeft=278.91` (`299473698816 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678238576507] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838684567966] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026769724270] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233693438969]`
- `2026-10-18T19:20:06Z`: `Objects=5`, `GBLeft=276.14` (`296506142720 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678238752011] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838692521470] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026774795070] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233703248817]`
- `2026-10-18T19:21:08Z`: `Objects=5`, `GBLeft=272.83` (`292952502272 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678238970971] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838699513862] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026791702662] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233712813665]`
- `2026-10-18T19:22:10Z`: `Objects=5`, `GBLeft=269.82` (`289714696192 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678239175507] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838706774686] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026808710966] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233721071817]`
- `2026-10-18T19:23:12Z`: `Objects=5`, `GBLeft=266.88` (`286559256576 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678239339707] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838713603790] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026828947286] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233729682553]`
- `2026-10-18T19:24:14Z`: `Objects=5`, `GBLeft=263.90` (`283355856896 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678239546875] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838721339206] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026844761430] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233740155641]`
- `2026-10-18T19:25:16Z`: `Objects=5`, `GBLeft=260.44` (`279644880896 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678239734811] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838728800798] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026861943014] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233750338449]`
- `2026-10-18T19:26:18Z`: `Objects=5`, `GBLeft=257.52` (`276504920064 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678239946675] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838735455630] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026877355246] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233756707433]`
- `2026-10-18T19:27:20Z`: `Objects=5`, `GBLeft=254.88` (`273673596928 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678240171443] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838741789766] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026890456902] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233768885577]`
- `2026-10-18T19:28:21Z`: `Objects=5`, `GBLeft=252.13` (`270719234048 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678240428083] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838748569230] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026896346206] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233769386481]`
- `2026-10-18T19:29:23Z`: `Objects=5`, `GBLeft=249.18` (`267558432768 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678240612219] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838756095614] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026896386326] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233778915545]`
- `2026-10-18T19:30:25Z`: `Objects=5`, `GBLeft=245.92` (`264053317632 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678240790779] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838763056894] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026896430430] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233789055625]`
- `2026-10-18T19:31:27Z`: `Objects=5`, `GBLeft=243.17` (`261099614208 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678240951875] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838770215342] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026896473270] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233799233817]`
- `2026-10-18T19:32:29Z`: `Objects=5`, `GBLeft=239.78` (`257458139136 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678241098195] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838777871054] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026896513894] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233809564609]`
- `2026-10-18T19:33:31Z`: `Objects=5`, `GBLeft=236.60` (`254044188672 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678241221019] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838785559550] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026896552438] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233819409417]`
- `2026-10-18T19:34:32Z`: `Objects=5`, `GBLeft=233.04` (`250229096448 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678241369627] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838793905614] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026896597486] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233829905049]`
- `2026-10-18T19:35:34Z`: `Objects=5`, `GBLeft=229.73` (`246666559488 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678241527547] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838801638702] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026896654406] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233840359913]`
- `2026-10-18T19:36:36Z`: `Objects=5`, `GBLeft=226.68` (`243397627904 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678241672355] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838808815038] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026896699054] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233850643937]`
- `2026-10-18T19:37:38Z`: `Objects=5`, `GBLeft=223.38` (`239849897984 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678241848243] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838815931926] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026896740174] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233861863417]`
- `2026-10-18T19:38:40Z`: `Objects=5`, `GBLeft=220.35` (`236602589184 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678241976715] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838823103790] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026896783806] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233873105273]`
- `2026-10-18T19:39:42Z`: `Objects=5`, `GBLeft=217.33` (`233358884864 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678242149051] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838830531886] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026896823894] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233885170633]`
- `2026-10-18T19:40:44Z`: `Objects=5`, `GBLeft=213.98` (`229758107648 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678242302315] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838837771694] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026896870590] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233897017297]`
- `2026-10-18T19:41:46Z`: `Objects=5`, `GBLeft=210.96` (`226513747968 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678242439987] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838845280590] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026896917062] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233907908121]`
- `2026-10-18T19:42:48Z`: `Objects=5`, `GBLeft=207.74` (`223055413248 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678242581891] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838852874958] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026896958174] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233919670081]`
- `2026-10-18T19:43:50Z`: `Objects=5`, `GBLeft=204.52` (`219600773120 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678242722875] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838861001078] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026896999998] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233930894537]`
- `2026-10-18T19:44:51Z`: `Objects=5`, `GBLeft=201.05` (`215879376896 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678242848971] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838868510878] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026897039950] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233936762233]`
- `2026-10-18T19:45:53Z`: `Objects=4`, `GBLeft=197.69` (`212268736512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678243074491] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838876954382] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026897104134] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233938635737]`
- `2026-10-18T19:46:55Z`: `Objects=4`, `GBLeft=194.51` (`208853864448 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678243209115] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838884302422] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026897147158] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233943934129]`
- `2026-10-18T19:47:57Z`: `Objects=4`, `GBLeft=191.60` (`205733785600 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678244391459] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838891501822] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026897202494] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233953453145]`
- `2026-10-18T19:48:59Z`: `Objects=4`, `GBLeft=190.40` (`204441202688 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678249735547] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838898008854] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026897312734] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233957631641]`
- `2026-10-18T19:50:01Z`: `Objects=4`, `GBLeft=189.28` (`203236765696 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678249923907] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838904623958] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026897397814] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233959471041]`
- `2026-10-18T19:51:03Z`: `Objects=3`, `GBLeft=187.34` (`201158733824 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678250082379] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838910705958] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026897463606] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233961013609]`
- `2026-10-18T19:52:05Z`: `Objects=3`, `GBLeft=184.91` (`198540918784 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678250255715] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838917255894] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026897508486] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233963598945]`
- `2026-10-18T19:53:07Z`: `Objects=3`, `GBLeft=182.20` (`195634524160 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678250404347] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838923465158] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026916183822] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233970642705]`
- `2026-10-18T19:54:09Z`: `Objects=3`, `GBLeft=179.38` (`192603025408 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678250569691] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838930550814] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026937921534] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1233986850633]`
- `2026-10-18T19:55:11Z`: `Objects=3`, `GBLeft=176.49` (`189500551168 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678250734923] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838937610598] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026959979046] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234002335905]`
- `2026-10-18T19:56:12Z`: `Objects=3`, `GBLeft=173.52` (`186311041024 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678250862947] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838944744662] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026978712574] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234018174737]`
- `2026-10-18T19:57:14Z`: `Objects=3`, `GBLeft=170.21` (`182759645184 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678251020499] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838951184230] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1026997328470] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234035149625]`
- `2026-10-18T19:58:16Z`: `Objects=3`, `GBLeft=167.62` (`179981656064 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678251204851] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838957695590] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1027016596150] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234052412441]`
- `2026-10-18T19:59:18Z`: `Objects=3`, `GBLeft=164.65` (`176793657344 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678251359147] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838965071118] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1027036305646] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234070574929]`
- `2026-10-18T20:00:20Z`: `Objects=3`, `GBLeft=161.66` (`173584211968 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678251542643] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838972279038] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1027056581126] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234088189409]`
- `2026-10-18T20:01:22Z`: `Objects=3`, `GBLeft=158.66` (`170365157376 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678251705891] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838979843622] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1027078180422] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234105462705]`
- `2026-10-18T20:02:24Z`: `Objects=2`, `GBLeft=155.35` (`166807838720 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678251854699] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838987354062] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1027096733806] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234125280969]`
- `2026-10-18T20:03:26Z`: `Objects=2`, `GBLeft=152.26` (`163484217344 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678251990659] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=838994596382] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1027111839478] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234144050065]`
- `2026-10-18T20:04:28Z`: `Objects=2`, `GBLeft=149.17` (`160169406464 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678252125315] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=839002075854] | esxi02:T8[Health=OK,Wear=24(raw=1374),WrSec=1027141040438] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234163338481]`
- `2026-10-18T20:05:29Z`: `Objects=2`, `GBLeft=145.97` (`156732055552 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678252418715] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=839009808622] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027159919102] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234182840929]`
- `2026-10-18T20:06:31Z`: `Objects=2`, `GBLeft=142.87` (`153408561152 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678252554443] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=839017058702] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027159960486] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234201498841]`
- `2026-10-18T20:07:33Z`: `Objects=2`, `GBLeft=139.87` (`150179815424 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678252718683] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=839024185462] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027169590030] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234214826513]`
- `2026-10-18T20:08:35Z`: `Objects=2`, `GBLeft=137.05` (`147157622784 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678252861323] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=839031280614] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027185480294] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234229007065]`
- `2026-10-18T20:09:37Z`: `Objects=2`, `GBLeft=133.79` (`143653928960 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678253030427] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=839037220422] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027205247846] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234248330257]`
- `2026-10-18T20:10:39Z`: `Objects=2`, `GBLeft=130.98` (`140637331456 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678253221707] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=839044344038] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027221515182] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234268731025]`
- `2026-10-18T20:11:40Z`: `Objects=2`, `GBLeft=127.98` (`137419866112 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678253347355] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=839051887358] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027234895470] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234290708953]`
- `2026-10-18T20:12:42Z`: `Objects=2`, `GBLeft=124.84` (`134043123712 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678253501667] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=839059196598] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027248653158] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234311816713]`
- `2026-10-18T20:13:44Z`: `Objects=2`, `GBLeft=121.94` (`130928877568 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678253653075] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=839066050854] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027259939582] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234334514201]`
- `2026-10-18T20:14:46Z`: `Objects=2`, `GBLeft=118.31` (`127036186624 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678253786315] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=839073216798] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027268431238] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234354760849]`
- `2026-10-18T20:15:48Z`: `Objects=1`, `GBLeft=115.08` (`123560972288 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678253996963] | esxi01:T1[Health=OK,Wear=33(raw=1209),WrSec=839080862718] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027279875110] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234372196641]`
- `2026-10-18T20:16:50Z`: `Objects=1`, `GBLeft=112.22` (`120490774528 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678254137939] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839087705774] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027294291366] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234392857849]`
- `2026-10-18T20:17:51Z`: `Objects=1`, `GBLeft=109.65` (`117731885056 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678254295739] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839093780814] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027312329974] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234414881137]`
- `2026-10-18T20:18:53Z`: `Objects=1`, `GBLeft=106.90` (`114783162368 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678254457427] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839100261958] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027331465214] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234439142993]`
- `2026-10-18T20:19:55Z`: `Objects=1`, `GBLeft=104.35` (`112045092864 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678254586171] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839106219230] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027342243302] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234460535329]`
- `2026-10-18T20:20:57Z`: `Objects=1`, `GBLeft=101.46` (`108943122432 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678254757827] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839113228446] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027356718150] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234471475481]`
- `2026-10-18T20:21:59Z`: `Objects=1`, `GBLeft=98.65` (`105929707520 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678254901195] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839119569102] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027365120110] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234477443521]`
- `2026-10-18T20:23:01Z`: `Objects=1`, `GBLeft=96.23` (`103324717056 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678255065859] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839125549006] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027377535430] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234493995585]`
- `2026-10-18T20:24:03Z`: `Objects=1`, `GBLeft=93.52` (`100419723264 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678255233851] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839132151582] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027391333406] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234507858769]`
- `2026-10-18T20:25:05Z`: `Objects=1`, `GBLeft=90.89` (`97594712064 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678255443475] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839138429110] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027405237758] | esxi02:T9[Health=OK,Wear=9(raw=1640),WrSec=1234520738793]`
- `2026-10-18T20:26:07Z`: `Objects=1`, `GBLeft=87.95` (`94436044800 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678255669179] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839145884110] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027425216182] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234533169569]`
- `2026-10-18T20:27:09Z`: `Objects=1`, `GBLeft=85.14` (`91414986752 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678255892459] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839152337758] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027441148214] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234546785593]`
- `2026-10-18T20:28:11Z`: `Objects=1`, `GBLeft=82.69` (`88787099648 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678256143851] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839158272278] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027456284070] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234562482769]`
- `2026-10-18T20:29:13Z`: `Objects=1`, `GBLeft=79.90` (`85795491840 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678256382411] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839165180566] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027473042030] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234575298561]`
- `2026-10-18T20:30:15Z`: `Objects=1`, `GBLeft=76.67` (`82319540224 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678256607219] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839172976894] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027487488566] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234589650785]`
- `2026-10-18T20:31:17Z`: `Objects=1`, `GBLeft=72.08` (`77391400960 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678256862955] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839180063398] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027506825830] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234602925289]`
- `2026-10-18T20:32:19Z`: `Objects=1`, `GBLeft=69.29` (`74400030720 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678257080027] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839186605158] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027523478422] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234605125577]`
- `2026-10-18T20:33:21Z`: `Objects=1`, `GBLeft=66.67` (`71582228480 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678257290467] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839192820526] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027538936454] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234609352017]`
- `2026-10-18T20:34:23Z`: `Objects=1`, `GBLeft=64.29` (`69029662720 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678257510339] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839198505846] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027554345982] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234612316145]`
- `2026-10-18T20:35:24Z`: `Objects=1`, `GBLeft=61.46` (`65995395072 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678257739875] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839205372862] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027578552254] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234615828105]`
- `2026-10-18T20:36:26Z`: `Objects=1`, `GBLeft=58.58` (`62900453376 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678257942763] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839212043998] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027578591814] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234617704553]`
- `2026-10-18T20:37:29Z`: `Objects=1`, `GBLeft=55.62` (`59716780032 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678258175811] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839219794790] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027578632966] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234618665953]`
- `2026-10-18T20:38:30Z`: `Objects=1`, `GBLeft=51.93` (`55761510400 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678258392979] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839228679830] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027578678222] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234619331105]`
- `2026-10-18T20:39:32Z`: `Objects=1`, `GBLeft=48.32` (`51888455680 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678258652595] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839237381006] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027578715966] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234619526545]`
- `2026-10-18T20:40:34Z`: `Objects=1`, `GBLeft=44.88` (`48193220608 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678258898363] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839245486182] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027578765622] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234619806417]`
- `2026-10-18T20:41:36Z`: `Objects=1`, `GBLeft=41.61` (`44677447680 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678259135971] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839253409062] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027578807326] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234620034113]`
- `2026-10-18T20:42:38Z`: `Objects=1`, `GBLeft=38.18` (`40991424512 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678259337195] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839261956782] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027578852854] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234620359625]`
- `2026-10-18T20:43:39Z`: `Objects=1`, `GBLeft=34.94` (`37518348288 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678260369659] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839269352878] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027582380022] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234620629041]`
- `2026-10-18T20:44:41Z`: `Objects=1`, `GBLeft=31.78` (`34127400960 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678260656419] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839276990638] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027595542702] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234630480569]`
- `2026-10-18T20:45:43Z`: `Objects=1`, `GBLeft=28.74` (`30860795904 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678260974667] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839284603470] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027607652862] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234640839025]`
- `2026-10-18T20:46:45Z`: `Objects=1`, `GBLeft=25.70` (`27595268096 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678262545355] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839292670902] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027620207550] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234647540817]`
- `2026-10-18T20:47:47Z`: `Objects=1`, `GBLeft=22.78` (`24459599872 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678263055411] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839300761606] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027633049094] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234654728233]`
- `2026-10-18T20:48:49Z`: `Objects=1`, `GBLeft=18.48` (`19842932736 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678263607347] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839307828798] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027646383166] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234672314177]`
- `2026-10-18T20:49:51Z`: `Objects=1`, `GBLeft=15.46` (`16595755008 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678263874203] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839315140438] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027656218406] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234689148249]`
- `2026-10-18T20:50:53Z`: `Objects=1`, `GBLeft=12.87` (`13817241600 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678264143811] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839323222670] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027664466766] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234707758329]`
- `2026-10-18T20:51:55Z`: `Objects=1`, `GBLeft=10.28` (`11038695424 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678264762459] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839329730886] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027671758094] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234722649425]`
- `2026-10-18T20:52:57Z`: `Objects=1`, `GBLeft=7.85` (`8433639424 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678265030251] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839335615766] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027675771582] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234742510305]`
- `2026-10-18T20:53:59Z`: `Objects=1`, `GBLeft=5.47` (`5868142592 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678265539275] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839341938086] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027686973414] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234763283225]`
- `2026-10-18T20:55:01Z`: `Objects=1`, `GBLeft=3.24` (`3482673152 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678265818347] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839348045574] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027695774710] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234783757441]`
- `2026-10-18T20:56:03Z`: `Objects=1`, `GBLeft=0.97` (`1042808832 B`) | `esxi01:T0[Health=OK,Wear=50(raw=899),WrSec=678266100195] | esxi01:T1[Health=OK,Wear=33(raw=1210),WrSec=839354221798] | esxi02:T8[Health=OK,Wear=24(raw=1375),WrSec=1027708650230] | esxi02:T9[Health=OK,Wear=9(raw=1641),WrSec=1234804821681]`

### Post-Evacuation Resync Verification (`esxcli vsan debug resync summary get`, `rc=0`)
```text
TotalBytesLeftToResync:         0
TotalGBLeftToResync:            0.00
TotalNumberOfResyncingObjects:  0
```

- **Add Start / End (UTC)**: `2026-10-18T20:57:07Z` -> `2026-10-18T20:57:36Z`
- **Add Command**: `esxcli vsan storage add -s mpx.vmhba0:C2:T1:L0 -d naa.5000cca01aa800e0`
- **Add Exit Code**: `rc=0`
- **Add Output / Stderr**: `true`
- **Post-Add Disk Verification**:
  - `Device`: `naa.5000cca01aa800e0`
  - `VSANDiskGroupName`: `mpx.vmhba0:C2:T1:L0`
  - `VSANDiskGroupUUID`: `520e7405-7951-d36b-972f-44fa76ee518e`
  - `VSANUUID`: `527b2bdc-ffa6-fd7c-d00d-2ae309c17e78`
  - `IsMounted`: `true`
  - `InCMMDS`: `true`

### Post-Step C2 Resync Summary (`rc=0`)
```text
TotalBytesLeftToResync:         0
TotalGBLeftToResync:            0.00
TotalNumberOfResyncingObjects:  0
```

### Post-Step C2 SMART Wear Check (`esxi01` `T0`/`T1` & `esxi02` `T8`/`T9`)
### esxi01 T0 (DG 52e69070 Cache) (`mpx.vmhba0:C2:T0:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    50     0          50     899
Write Error Count          100    10         100    0
Power-on Hours             98     0          98     9527
Power Cycle Count          99     0          99     293
Reallocated Sector Count   100    10         100    0
Drive Temperature          50     0          34     50
Write Sectors TOT Count    99     0          99     678266442261
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```
### esxi01 T1 (DG 520e7405 Cache) (`mpx.vmhba0:C2:T1:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    33     0          33     1210
Write Error Count          100    10         100    0
Power-on Hours             98     0          98     9527
Power Cycle Count          99     0          99     297
Reallocated Sector Count   100    10         100    0
Drive Temperature          46     0          34     54
Write Sectors TOT Count    99     0          99     839355953263
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```
### esxi02 T8 (DG 5240e70d Cache) (`mpx.vmhba0:C2:T8:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    24     0          24     1375
Write Error Count          100    10         100    0
Power-on Hours             98     0          98     9622
Power Cycle Count          99     0          99     307
Reallocated Sector Count   100    10         100    0
Drive Temperature          41     0          29     59
Write Sectors TOT Count    99     0          99     1027723308518
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```
### esxi02 T9 (DG 522f2b56 Cache) (`mpx.vmhba0:C2:T9:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    9      0          9      1641
Write Error Count          100    10         100    0
Power-on Hours             95     0          95     24379
Power Cycle Count          98     0          98     1485
Reallocated Sector Count   100    10         100    0
Drive Temperature          35     0          29     65
Write Sectors TOT Count    99     0          99     1234835024185
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```

### Post-Step C2 `esxi01` Disk Group Layout
| Disk Group UUID | Cache SSD | Device | Capacity Tier | IsSSD | Mounted | InCMMDS | VSAN UUID |
|---|---|---|---|---|---|---|---|
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `mpx.vmhba0:C2:T1:L0` | `false` | `true` | `true` | `true` | `520e7405-7951-d36b-972f-44fa76ee518e` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.50000c0f012d3630` | `true` | `false` | `true` | `true` | `522ac7d2-9125-2e27-d90a-e79d5b95d5ff` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.50000c0f01e0c290` | `true` | `false` | `true` | `true` | `52231164-8542-4e47-05b5-c97338472125` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01aa7e5ec` | `true` | `false` | `true` | `true` | `52d55f3a-970a-3363-c37c-98bfd6d162b0` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01aa800e0` | `true` | `false` | `true` | `true` | `527b2bdc-ffa6-fd7c-d00d-2ae309c17e78` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01aa9a96c` | `true` | `false` | `true` | `true` | `52191fd7-997a-a5a6-c65f-0b325a677220` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01ab112cc` | `true` | `false` | `true` | `true` | `52e03231-e4ca-b44f-afd9-b65c7a5cf3e1` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01ad7dc48` | `true` | `false` | `true` | `true` | `52838d58-ec30-7adb-256c-53952dea37c4` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T0:L0` | `false` | `true` | `true` | `true` | `52e69070-da86-8f9f-27b2-679f255787a8` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T13:L0` | `true` | `false` | `true` | `true` | `52f55307-770c-ff18-3fb6-7a5efb104701` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T3:L0` | `true` | `false` | `true` | `true` | `521d0c28-1459-755a-6721-d9c20fb39333` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T4:L0` | `true` | `false` | `true` | `true` | `52521303-9b87-0c61-4f80-10bf7d453fe4` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T5:L0` | `true` | `false` | `true` | `true` | `525ffaa8-33eb-b23b-c59f-09944ca74ecc` |

---

## Final Post-Execution Verification (`2026-10-18T20:57:46Z`)

### `esxi01`: Final Disk Group Table
| Disk Group UUID | Cache SSD | Device | Capacity Tier | IsSSD | Mounted | InCMMDS | VSAN UUID |
|---|---|---|---|---|---|---|---|
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `mpx.vmhba0:C2:T1:L0` | `false` | `true` | `true` | `true` | `520e7405-7951-d36b-972f-44fa76ee518e` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.50000c0f012d3630` | `true` | `false` | `true` | `true` | `522ac7d2-9125-2e27-d90a-e79d5b95d5ff` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.50000c0f01e0c290` | `true` | `false` | `true` | `true` | `52231164-8542-4e47-05b5-c97338472125` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01aa7e5ec` | `true` | `false` | `true` | `true` | `52d55f3a-970a-3363-c37c-98bfd6d162b0` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01aa800e0` | `true` | `false` | `true` | `true` | `527b2bdc-ffa6-fd7c-d00d-2ae309c17e78` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01aa9a96c` | `true` | `false` | `true` | `true` | `52191fd7-997a-a5a6-c65f-0b325a677220` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01ab112cc` | `true` | `false` | `true` | `true` | `52e03231-e4ca-b44f-afd9-b65c7a5cf3e1` |
| `520e7405-7951-d36b-972f-44fa76ee518e` | `mpx.vmhba0:C2:T1:L0` | `naa.5000cca01ad7dc48` | `true` | `false` | `true` | `true` | `52838d58-ec30-7adb-256c-53952dea37c4` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T0:L0` | `false` | `true` | `true` | `true` | `52e69070-da86-8f9f-27b2-679f255787a8` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T13:L0` | `true` | `false` | `true` | `true` | `52f55307-770c-ff18-3fb6-7a5efb104701` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T3:L0` | `true` | `false` | `true` | `true` | `521d0c28-1459-755a-6721-d9c20fb39333` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T4:L0` | `true` | `false` | `true` | `true` | `52521303-9b87-0c61-4f80-10bf7d453fe4` |
| `52e69070-da86-8f9f-27b2-679f255787a8` | `mpx.vmhba0:C2:T0:L0` | `mpx.vmhba0:C2:T5:L0` | `true` | `false` | `true` | `true` | `525ffaa8-33eb-b23b-c59f-09944ca74ecc` |

### `esxi01`: `esxcli vsan cluster get` (`rc=0`)
```text
ConfigGeneration:                   11deb2da-af76-4c32-8756-f7e5e7e05301 20 2026-10-18T16:09:35.865
CurrentLocalTime:                   2026-10-18T20:57:44Z
Enabled:                            true
LocalNodeHealthState:               HEALTHY
LocalNodeState:                     AGENT
LocalNodeType:                      NORMAL
LocalNodeUUID:                      690bf025-a834-166a-7963-48df37d56620
MaintenanceModeState:               OFF
Mode:                               REGULAR
SubClusterBackupUUID:               690bed89-0b5f-39b0-71b3-48df37e0b790
SubClusterMasterUUID:               690be3b3-a4fc-84dc-bf83-98b785225eea
SubClusterMemberCount:              4
SubClusterMemberHostNames:          ESXI02, ESXI04, ESXI01, ESXI03
SubClusterMemberUUIDs:              690bed89-0b5f-39b0-71b3-48df37e0b790, 690be3b3-a4fc-84dc-bf83-98b785225eea, 690bf025-a834-166a-7963-48df37d56620, 690bf3f8-a3c0-fbe0-3b20-98b78522598c
SubClusterMembershipEntryRevision:  8
SubClusterMembershipUUID:           cab8d36a-de22-c9dd-3be4-98b785225eea
SubClusterUUID:                     5221e29f-8740-f516-7273-0ae338b3eb48
UnicastModeEnabled:                 true
vSANESAEnabled:                     false
```

### `esxi01`: `esxcli vsan debug resync summary get` (`rc=0`)
```text
TotalBytesLeftToResync:         0
TotalGBLeftToResync:            0.00
TotalNumberOfResyncingObjects:  0
```

### Final SMART Wear Verification (`esxi01` `T0`/`T1` & `esxi02` `T8`/`T9`)
### esxi01 T0 (DG 52e69070 Cache) (`mpx.vmhba0:C2:T0:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    50     0          50     899
Write Error Count          100    10         100    0
Power-on Hours             98     0          98     9527
Power Cycle Count          99     0          99     293
Reallocated Sector Count   100    10         100    0
Drive Temperature          50     0          34     50
Write Sectors TOT Count    99     0          99     678266452301
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```
### esxi01 T1 (DG 520e7405 Cache) (`mpx.vmhba0:C2:T1:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    33     0          33     1210
Write Error Count          100    10         100    0
Power-on Hours             98     0          98     9527
Power Cycle Count          99     0          99     297
Reallocated Sector Count   100    10         100    0
Drive Temperature          46     0          34     54
Write Sectors TOT Count    99     0          99     839355959119
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```
### esxi02 T8 (DG 5240e70d Cache) (`mpx.vmhba0:C2:T8:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    24     0          24     1375
Write Error Count          100    10         100    0
Power-on Hours             98     0          98     9622
Power Cycle Count          99     0          99     307
Reallocated Sector Count   100    10         100    0
Drive Temperature          40     0          29     60
Write Sectors TOT Count    99     0          99     1027724256830
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```
### esxi02 T9 (DG 522f2b56 Cache) (`mpx.vmhba0:C2:T9:L0`) — `rc=0`
```text
Parameter                  Value  Threshold  Worst  Raw
---------                  -----  ---------  -----  ---
Health Status              OK     N/A        N/A    N/A
Media Wearout Indicator    9      0          9      1641
Write Error Count          100    10         100    0
Power-on Hours             95     0          95     24379
Power Cycle Count          98     0          98     1485
Reallocated Sector Count   100    10         100    0
Drive Temperature          35     0          29     65
Write Sectors TOT Count    99     0          99     1234836351033
Initial Bad Block Count    100    10         100    0
Program Fail Count         100    10         100    0
Erase Fail Count           100    10         100    0
Uncorrectable Error Count  100    0          100    0
```

### Raw `esxi01`: `esxcli vsan storage list` (`rc=0`)
```text
Checksum:                      13973054014092348773
ChecksumOK:                    true
Compression:                   false
CreationTime:                  Sun Aug  2 21:50:46 2026
Deduplication:                 false
Device:                        mpx.vmhba0:C2:T1:L0
DiskKeyLoaded:                 true
DisplayName:                   mpx.vmhba0:C2:T1:L0
Encryption:                    true
EncryptionMetadataChecksumOK:  true
InCMMDS:                       true
IsCapacityTier:                false
IsMounted:                     true
IsSSD:                         true
Ondiskformatversion:           20
Usedbythishost:                true
VSANDiskGroupName:             mpx.vmhba0:C2:T1:L0
VSANDiskGroupUUID:             520e7405-7951-d36b-972f-44fa76ee518e
VSANUUID:                      520e7405-7951-d36b-972f-44fa76ee518e

Checksum:                      17688481394136892150
ChecksumOK:                    true
Compression:                   false
CreationTime:                  Sat Oct 17 22:26:54 2026
Deduplication:                 false
Device:                        naa.5000cca01aa9a96c
DiskKeyLoaded:                 true
DisplayName:                   naa.5000cca01aa9a96c
Encryption:                    true
EncryptionMetadataChecksumOK:  true
InCMMDS:                       true
IsCapacityTier:                true
IsMounted:                     true
IsSSD:                         false
Ondiskformatversion:           20
Usedbythishost:                true
VSANDiskGroupName:             mpx.vmhba0:C2:T1:L0
VSANDiskGroupUUID:             520e7405-7951-d36b-972f-44fa76ee518e
VSANUUID:                      52191fd7-997a-a5a6-c65f-0b325a677220

Checksum:                      17203729761752991664
ChecksumOK:                    true
Compression:                   false
CreationTime:                  Sun Aug  2 21:49:48 2026
Deduplication:                 false
Device:                        mpx.vmhba0:C2:T3:L0
DiskKeyLoaded:                 true
DisplayName:                   mpx.vmhba0:C2:T3:L0
Encryption:                    true
EncryptionMetadataChecksumOK:  true
InCMMDS:                       true
IsCapacityTier:                true
IsMounted:                     true
IsSSD:                         false
Ondiskformatversion:           20
Usedbythishost:                true
VSANDiskGroupName:             mpx.vmhba0:C2:T0:L0
VSANDiskGroupUUID:             52e69070-da86-8f9f-27b2-679f255787a8
VSANUUID:                      521d0c28-1459-755a-6721-d9c20fb39333

Checksum:                      14929893776302125013
ChecksumOK:                    true
Compression:                   false
CreationTime:                  Sun Aug  2 21:50:46 2026
Deduplication:                 false
Device:                        naa.50000c0f01e0c290
DiskKeyLoaded:                 true
DisplayName:                   naa.50000c0f01e0c290
Encryption:                    true
EncryptionMetadataChecksumOK:  true
InCMMDS:                       true
IsCapacityTier:                true
IsMounted:                     true
IsSSD:                         false
Ondiskformatversion:           20
Usedbythishost:                true
VSANDiskGroupName:             mpx.vmhba0:C2:T1:L0
VSANDiskGroupUUID:             520e7405-7951-d36b-972f-44fa76ee518e
VSANUUID:                      52231164-8542-4e47-05b5-c97338472125

Checksum:                      11446875420798458378
ChecksumOK:                    true
Compression:                   false
CreationTime:                  Sun Aug  2 21:50:46 2026
Deduplication:                 false
Device:                        naa.50000c0f012d3630
DiskKeyLoaded:                 true
DisplayName:                   naa.50000c0f012d3630
Encryption:                    true
EncryptionMetadataChecksumOK:  true
InCMMDS:                       true
IsCapacityTier:                true
IsMounted:                     true
IsSSD:                         false
Ondiskformatversion:           20
Usedbythishost:                true
VSANDiskGroupName:             mpx.vmhba0:C2:T1:L0
VSANDiskGroupUUID:             520e7405-7951-d36b-972f-44fa76ee518e
VSANUUID:                      522ac7d2-9125-2e27-d90a-e79d5b95d5ff

Checksum:                      15232996603863013606
ChecksumOK:                    true
Compression:                   false
CreationTime:                  Sat Oct 17 21:23:04 2026
Deduplication:                 false
Device:                        mpx.vmhba0:C2:T4:L0
DiskKeyLoaded:                 true
DisplayName:                   mpx.vmhba0:C2:T4:L0
Encryption:                    true
EncryptionMetadataChecksumOK:  true
InCMMDS:                       true
IsCapacityTier:                true
IsMounted:                     true
IsSSD:                         false
Ondiskformatversion:           20
Usedbythishost:                true
VSANDiskGroupName:             mpx.vmhba0:C2:T0:L0
VSANDiskGroupUUID:             52e69070-da86-8f9f-27b2-679f255787a8
VSANUUID:                      52521303-9b87-0c61-4f80-10bf7d453fe4

Checksum:                      5528039070046886294
ChecksumOK:                    true
Compression:                   false
CreationTime:                  Sun Oct 18 15:17:48 2026
Deduplication:                 false
Device:                        mpx.vmhba0:C2:T5:L0
DiskKeyLoaded:                 true
DisplayName:                   mpx.vmhba0:C2:T5:L0
Encryption:                    true
EncryptionMetadataChecksumOK:  true
InCMMDS:                       true
IsCapacityTier:                true
IsMounted:                     true
IsSSD:                         false
Ondiskformatversion:           20
Usedbythishost:                true
VSANDiskGroupName:             mpx.vmhba0:C2:T0:L0
VSANDiskGroupUUID:             52e69070-da86-8f9f-27b2-679f255787a8
VSANUUID:                      525ffaa8-33eb-b23b-c59f-09944ca74ecc

Checksum:                      9030299180482733843
ChecksumOK:                    true
Compression:                   false
CreationTime:                  Sun Oct 18 20:57:17 2026
Deduplication:                 false
Device:                        naa.5000cca01aa800e0
DiskKeyLoaded:                 true
DisplayName:                   naa.5000cca01aa800e0
Encryption:                    true
EncryptionMetadataChecksumOK:  true
InCMMDS:                       true
IsCapacityTier:                true
IsMounted:                     true
IsSSD:                         false
Ondiskformatversion:           20
Usedbythishost:                true
VSANDiskGroupName:             mpx.vmhba0:C2:T1:L0
VSANDiskGroupUUID:             520e7405-7951-d36b-972f-44fa76ee518e
VSANUUID:                      527b2bdc-ffa6-fd7c-d00d-2ae309c17e78

Checksum:                      1636177605617912446
ChecksumOK:                    true
Compression:                   false
CreationTime:                  Sun Aug  2 21:50:46 2026
Deduplication:                 false
Device:                        naa.5000cca01ad7dc48
DiskKeyLoaded:                 true
DisplayName:                   naa.5000cca01ad7dc48
Encryption:                    true
EncryptionMetadataChecksumOK:  true
InCMMDS:                       true
IsCapacityTier:                true
IsMounted:                     true
IsSSD:                         false
Ondiskformatversion:           20
Usedbythishost:                true
VSANDiskGroupName:             mpx.vmhba0:C2:T1:L0
VSANDiskGroupUUID:             520e7405-7951-d36b-972f-44fa76ee518e
VSANUUID:                      52838d58-ec30-7adb-256c-53952dea37c4

Checksum:                      5472771192555535141
ChecksumOK:                    true
Compression:                   false
CreationTime:                  Sun Oct 18 04:42:59 2026
Deduplication:                 false
Device:                        naa.5000cca01aa7e5ec
DiskKeyLoaded:                 true
DisplayName:                   naa.5000cca01aa7e5ec
Encryption:                    true
EncryptionMetadataChecksumOK:  true
InCMMDS:                       true
IsCapacityTier:                true
IsMounted:                     true
IsSSD:                         false
Ondiskformatversion:           20
Usedbythishost:                true
VSANDiskGroupName:             mpx.vmhba0:C2:T1:L0
VSANDiskGroupUUID:             520e7405-7951-d36b-972f-44fa76ee518e
VSANUUID:                      52d55f3a-970a-3363-c37c-98bfd6d162b0

Checksum:                      12545337803067324202
ChecksumOK:                    true
Compression:                   false
CreationTime:                  Sun Aug  2 21:50:46 2026
Deduplication:                 false
Device:                        naa.5000cca01ab112cc
DiskKeyLoaded:                 true
DisplayName:                   naa.5000cca01ab112cc
Encryption:                    true
EncryptionMetadataChecksumOK:  true
InCMMDS:                       true
IsCapacityTier:                true
IsMounted:                     true
IsSSD:                         false
Ondiskformatversion:           20
Usedbythishost:                true
VSANDiskGroupName:             mpx.vmhba0:C2:T1:L0
VSANDiskGroupUUID:             520e7405-7951-d36b-972f-44fa76ee518e
VSANUUID:                      52e03231-e4ca-b44f-afd9-b65c7a5cf3e1

Checksum:                      1356280590065838741
ChecksumOK:                    true
Compression:                   false
CreationTime:                  Sun Aug  2 21:49:48 2026
Deduplication:                 false
Device:                        mpx.vmhba0:C2:T0:L0
DiskKeyLoaded:                 true
DisplayName:                   mpx.vmhba0:C2:T0:L0
Encryption:                    true
EncryptionMetadataChecksumOK:  true
InCMMDS:                       true
IsCapacityTier:                false
IsMounted:                     true
IsSSD:                         true
Ondiskformatversion:           20
Usedbythishost:                true
VSANDiskGroupName:             mpx.vmhba0:C2:T0:L0
VSANDiskGroupUUID:             52e69070-da86-8f9f-27b2-679f255787a8
VSANUUID:                      52e69070-da86-8f9f-27b2-679f255787a8

Checksum:                      9705038389532789237
ChecksumOK:                    true
Compression:                   false
CreationTime:                  Sun Aug  2 21:49:48 2026
Deduplication:                 false
Device:                        mpx.vmhba0:C2:T13:L0
DiskKeyLoaded:                 true
DisplayName:                   mpx.vmhba0:C2:T13:L0
Encryption:                    true
EncryptionMetadataChecksumOK:  true
InCMMDS:                       true
IsCapacityTier:                true
IsMounted:                     true
IsSSD:                         false
Ondiskformatversion:           20
Usedbythishost:                true
VSANDiskGroupName:             mpx.vmhba0:C2:T0:L0
VSANDiskGroupUUID:             52e69070-da86-8f9f-27b2-679f255787a8
VSANUUID:                      52f55307-770c-ff18-3fb6-7a5efb104701
```

