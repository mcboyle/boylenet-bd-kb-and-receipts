# AGENT-5: Remaining FTT=1 Policy Conversion & Orphaned vSAN Object Audit Receipts

- **Timestamp (UTC)**: `2026-10-17T17:17:46Z`
- **Seat**: Agent-5 (Remaining `FTT=1` Policy & Orphaned Object Auditor)
- **Cluster / vCenter**: `Boylenet Cluster` (`domain-c85`) on `vc04.boylenet.themfboyles.com` (`10.0.20.70`, VMware vCenter Server `8.0.3 build-24322831`)
- **Raw Snapshot Artifacts**:
  - Pre-conversion `esxcli` dump: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/vsan_debug_objects_agent5_before.txt` (`795,505` bytes, `RC=0`)
  - Post-conversion `esxcli` dump: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/vsan_debug_objects_agent5_after.txt` (`777,976` bytes, `RC=0`)

---

## 1. Storage Policy Conversion (`FTT=1` -> `FTT=0` `Boylenet`)

### 1.1 Root Cause of `ReconfigVM_Task` Failure on Powered-On VMs & `pyVmomi` Solution
1. **Live SPBM Profile UUID Verification**:
   - Source Policy (`FTT=1`): `Management Storage policy - Thin` — live SPBM `uniqueId = b6423670-8552-66e8-adc1-fa6ae01abeac` (`hostFailuresToTolerate = 1`, `proportionalCapacity = 0`, `replicaPreference = RAID-1 (Mirroring) - Performance`). *(Note: `b4a6e348-3b9b-413b-99d1-3d8790f0a065` in the brief was a shorthand/typo for `b6423670-8552-66e8-adc1-fa6ae01abeac`.)*
   - Target Policy (`FTT=0`): `Boylenet` — live SPBM `uniqueId = 5ae84dbe-ff79-4a5e-ae41-5a8c24ec999a` (`generationId = 2`, `hostFailuresToTolerate = 0`, `stripeWidth = 1`, `proportionalCapacity = 0`, `dataAtRestEncryption = True`).
2. **Why `vim.VirtualMachine.ReconfigVM_Task` Raised `vim.fault.InvalidPowerState`**:
   - All 14 VMs holding the `FTT=1` VMDKs (`Cache`, `AI DEV`, `llama`, `CI%2fCD`, `spare`, `spare2`, `spare3`, `spare6`, `spare7`, `spare8`, `spare9`, `Spare11`, `spare12`, `kvcache-cx1-20261014`) are currently `poweredOn` and already have `Boylenet` (`5ae84dbe-ff79-4a5e-ae41-5a8c24ec999a`) associated at the VM namespace level (`vm.config.keyId.providerId.id = 'Boylenet'`), while their virtual disks (`key=2000`) were created unencrypted (`dev.backing.keyId = <unset>`, `iofilter = []`) under `Management Storage policy - Thin` (`b6423670-8552-66e8-adc1-fa6ae01abeac`).
   - Because `Boylenet` (`5ae84dbe-ff79-4a5e-ae41-5a8c24ec999a`) includes the VM Encryption I/O Filter capability (`com.vmware.storageprofile.dataservice.ad5a249d-cbc2-43af-9366-694d7664fa52` / `VMW_vmwarevmcrypt_1.0.0`), calling `ReconfigVM_Task` on the virtual disk attempts to attach the `VMW_vmwarevmcrypt_1.0.0` filter to an unencrypted disk while `poweredOn`, which vCenter rejects with `vim.fault.InvalidPowerState: The attempted operation cannot be performed in the current state (Powered on)`.
3. **Zero-Downtime Online vSAN DOM Policy Application (`vsan-cluster-object-system`)**:
   - Using `vsanapiutils.GetVsanVcMos` (`vsan.version.version23`) -> `vcMos['vsan-cluster-object-system'].SetVsanObjectPolicy(cluster=cluster, vsanObjectUuid=uuid, profile=vim.vm.DefinedProfileSpec(profileId="5ae84dbe-ff79-4a5e-ae41-5a8c24ec999a"))`, vCenter directly updates the vSAN DOM object policy to `Boylenet` (`5ae84dbe-ff79-4a5e-ae41-5a8c24ec999a`, `generationId = 2`, `hostFailuresToTolerate = 0`) online without touching the VM power state or requiring disk re-encryption.
   - All 4 of Agent-5's assigned VMDKs (`Cache.vmdk`, `AI DEV.vmdk`, `llama.vmdk`, `ai1.vmdk`) **plus** the 10 `spare*`/`kvcache` VMDKs (which were blocked by `InvalidPowerState` in Agent-2's `ReconfigVM_Task` call) were converted in a single pass so that **all 14 VMDKs are now `FTT=0`**.

### 1.2 Raw `pyVmomi` `SetVsanObjectPolicy` & `VosQueryVsanObjectInformation` Receipts (`RC=0`)
```text
=== APPLYING BOYLENET (5ae84dbe-ff79-4a5e-ae41-5a8c24ec999a, FTT=0) TO GROUP A (4 VMDKs) ===
  Cache.vmdk                   (d1a5a46a-48e7-8805-12cb-98b785225eea) -> SetVsanObjectPolicy=True | FTT=0 | Profile=Boylenet (5ae84dbe-ff79-4a5e-ae41-5a8c24ec999a) | Compliance=compliant | Health=healthy
  AI DEV.vmdk                  (7ca5a46a-c0d8-ccd0-da52-48df37e0b790) -> SetVsanObjectPolicy=True | FTT=0 | Profile=Boylenet (5ae84dbe-ff79-4a5e-ae41-5a8c24ec999a) | Compliance=compliant | Health=healthy
  llama.vmdk                   (16a6a46a-c010-92aa-c91c-98b785225eea) -> SetVsanObjectPolicy=True | FTT=0 | Profile=Boylenet (5ae84dbe-ff79-4a5e-ae41-5a8c24ec999a) | Compliance=compliant | Health=healthy
  ai1.vmdk                     (f569a46a-8447-723f-742f-98b785225eea) -> SetVsanObjectPolicy=True | FTT=0 | Profile=Boylenet (5ae84dbe-ff79-4a5e-ae41-5a8c24ec999a) | Compliance=compliant | Health=healthy

=== APPLYING BOYLENET (5ae84dbe-ff79-4a5e-ae41-5a8c24ec999a, FTT=0) TO GROUP B (10 SPARE/KVCACHE VMDKs) ===
  spare7.vmdk                  (61aea46a-fee3-440f-6502-98b78522598c) -> SetVsanObjectPolicy=True | FTT=0 | Profile=Boylenet (5ae84dbe-ff79-4a5e-ae41-5a8c24ec999a) | Compliance=compliant | Health=healthy
  spare6.vmdk                  (4daea46a-2603-5542-0334-98b78522551a) -> SetVsanObjectPolicy=True | FTT=0 | Profile=Boylenet (5ae84dbe-ff79-4a5e-ae41-5a8c24ec999a) | Compliance=compliant | Health=healthy
  spare3.vmdk                  (31aaa46a-2a41-3f88-040e-98b785225eea) -> SetVsanObjectPolicy=True | FTT=0 | Profile=Boylenet (5ae84dbe-ff79-4a5e-ae41-5a8c24ec999a) | Compliance=compliant | Health=healthy
  spare12.vmdk                 (d2aea46a-608b-1f90-ae34-98b785225eea) -> SetVsanObjectPolicy=True | FTT=0 | Profile=Boylenet (5ae84dbe-ff79-4a5e-ae41-5a8c24ec999a) | Compliance=compliant | Health=healthy
  spare.vmdk                   (08aaa46a-7044-4b96-e933-98b78522551a) -> SetVsanObjectPolicy=True | FTT=0 | Profile=Boylenet (5ae84dbe-ff79-4a5e-ae41-5a8c24ec999a) | Compliance=compliant | Health=healthy
  kvcache-cx1-20261014_2.vmdk  (6c08d06a-d814-8ea9-69cb-98b785225eea) -> SetVsanObjectPolicy=True | FTT=0 | Profile=Boylenet (5ae84dbe-ff79-4a5e-ae41-5a8c24ec999a) | Compliance=compliant | Health=healthy
  spare8.vmdk                  (72aea46a-66fd-b5b2-f37a-98b78522551a) -> SetVsanObjectPolicy=True | FTT=0 | Profile=Boylenet (5ae84dbe-ff79-4a5e-ae41-5a8c24ec999a) | Compliance=compliant | Health=healthy
  spare9.vmdk                  (86aea46a-4262-6dc4-6cb9-98b78522551a) -> SetVsanObjectPolicy=True | FTT=0 | Profile=Boylenet (5ae84dbe-ff79-4a5e-ae41-5a8c24ec999a) | Compliance=compliant | Health=healthy
  spare2.vmdk                  (1faaa46a-928d-dfea-13a7-98b78522551a) -> SetVsanObjectPolicy=True | FTT=0 | Profile=Boylenet (5ae84dbe-ff79-4a5e-ae41-5a8c24ec999a) | Compliance=compliant | Health=healthy
  Spare11.vmdk                 (c5aea46a-fc12-eceb-9701-98b785225eea) -> SetVsanObjectPolicy=True | FTT=0 | Profile=Boylenet (5ae84dbe-ff79-4a5e-ae41-5a8c24ec999a) | Compliance=compliant | Health=healthy
```

---

## 2. Before vs. After `FTT=1` / `FTT=0` Object Count & Reclaimed Capacity

### 2.1 Object Count Summary (`govc host.esxcli -host esxi01.boylenet.themfboyles.com vsan debug object list -all=true`, `RC=0`)
| Metric | Before Conversion | After Conversion | Delta |
| :--- | ---: | ---: | ---: |
| **Total vSAN DOM Objects** (`ObjectUUID:`) | `174` (`173` path + `1` `vdfs`) | `174` (`173` path + `1` `vdfs`) | `0` |
| **`FTT=1` Objects** (`hostFailuresToTolerate: 1`) | `18` (`14` `vdisk` + `4` `vmnamespace`) | **`4`** (`0` `vdisk` + `4` `vmnamespace`) | **`-14`** |
| **`FTT=0` Objects** (`hostFailuresToTolerate: 0`) | `156` (`52` `vdisk` + `60` `vmnamespace` + `43` `vmswap` + `1` `vdfs`) | **`170`** (`66` `vdisk` + `60` `vmnamespace` + `43` `vmswap` + `1` `vdfs`) | **`+14`** |
| **Cluster Free Space** (`QuerySpaceUsage`) | `198.18 TiB` (`218.03 TB`) | **`199.94 TiB` (`219.83 TB`)** | **`+1.76 TiB` (`+1.80 TB`)** |

### 2.2 Verification of Remaining `FTT=1` Objects (0 VMDKs Remaining!)
Only the 4 non-VM system/library namespace objects remain on `FTT=1`:
```text
=== REMAINING FTT=1 OBJECTS AFTER ===
  91ba6f6a-fc07-db14-8b8e-98b785225eea | vmnamespace | Size=16.00GB  | Used=30.07GB | 4K=29.44GB | Dir='.vsan.trace'                                      | Path=/vmfs/volumes/vsan:5221e29f8740f516-72730ae338b3eb48/.vsan.trace (Exists)
  731e0d69-d215-6e50-470c-48df37e0b790 | vmnamespace | Size=255.00GB | Used=19.29GB | 4K=19.02GB | Dir='New Folder'                                       | Path=/vmfs/volumes/vsan:5221e29f8740f516-72730ae338b3eb48/New Folder (Exists)
  d18a0c69-ef29-4f8c-6a17-48df37e0b790 | vmnamespace | Size=255.00GB | Used=44.35GB | 4K=44.02GB | Dir='contentlib-14f9afa4-9283-4a2e-8b24-df8ab4614ef7' | Path=/vmfs/volumes/vsan:5221e29f8740f516-72730ae338b3eb48/contentlib-14f9afa4-9283-4a2e-8b24-df8ab4614ef7 (Exists)
  710c0c69-2009-908d-6ab1-48df37d56620 | vmnamespace | Size=16.00GB  | Used=30.18GB | 4K=29.63GB | Dir='.vsan.trace-1'                                    | Path=/vmfs/volumes/vsan:5221e29f8740f516-72730ae338b3eb48/.vsan.trace (Exists)
```

### 2.3 Per-Object Physical Capacity Reclaimed Across All 14 VMDKs
| Group | VMDK Name | VM Name (`moId`) | Object UUID | Provisioned | Used Before (`FTT=1`) | Used After (`FTT=0`) | Physical Used Reclaimed | 4K Blocks Reclaimed |
| :--- | :--- | :--- | :--- | ---: | ---: | ---: | ---: | ---: |
| **Agent-5 (4)** | `Cache.vmdk` | `Cache` (`vm-289027`) | `d1a5a46a-48e7-8805-12cb-98b785225eea` | `300.00 GB` | `396.39 GB` | `198.20 GB` | **`198.19 GB`** | `196.49 GB` |
| **Agent-5 (4)** | `AI DEV.vmdk` | `AI DEV` (`vm-289026`) | `7ca5a46a-c0d8-ccd0-da52-48df37e0b790` | `5120.00 GB` | `278.05 GB` | `139.03 GB` | **`139.02 GB`** | `136.11 GB` |
| **Agent-5 (4)** | `llama.vmdk` | `llama` (`vm-289028`) | `16a6a46a-c010-92aa-c91c-98b785225eea` | `300.00 GB` | `250.84 GB` | `125.42 GB` | **`125.42 GB`** | `124.41 GB` |
| **Agent-5 (4)** | `ai1.vmdk` | `CI%2fCD` (`vm-289023`) | `f569a46a-8447-723f-742f-98b785225eea` | `300.00 GB` | `61.08 GB` | `30.54 GB` | **`30.54 GB`** | `29.19 GB` |
| *Subtotal (4)* | *4 AI Pipeline VMDKs* | — | — | *`6020.00 GB`* | *`986.36 GB`* | *`493.19 GB`* | ***`493.17 GB`*** | *`486.20 GB`* |
| **Spare/KV (10)** | `spare6.vmdk` | `spare6` (`vm-289036`) | `4daea46a-2603-5542-0334-98b78522551a` | `300.00 GB` | `593.68 GB` | `296.84 GB` | **`296.84 GB`** | `296.51 GB` |
| **Spare/KV (10)** | `spare12.vmdk` | `spare12` (`vm-289042`) | `d2aea46a-608b-1f90-ae34-98b785225eea` | `300.00 GB` | `573.61 GB` | `286.80 GB` | **`286.81 GB`** | `285.58 GB` |
| **Spare/KV (10)** | `spare8.vmdk` | `spare8` (`vm-289038`) | `72aea46a-66fd-b5b2-f37a-98b78522551a` | `300.00 GB` | `411.49 GB` | `205.75 GB` | **`205.74 GB`** | `200.52 GB` |
| **Spare/KV (10)** | `spare7.vmdk` | `spare7` (`vm-289037`) | `61aea46a-fee3-440f-6502-98b78522598c` | `300.00 GB` | `382.73 GB` | `191.37 GB` | **`191.36 GB`** | `178.38 GB` |
| **Spare/KV (10)** | `Spare11.vmdk` | `Spare11` (`vm-289041`) | `c5aea46a-fc12-eceb-9701-98b785225eea` | `300.00 GB` | `377.82 GB` | `188.91 GB` | **`188.91 GB`** | `181.06 GB` |
| **Spare/KV (10)** | `spare2.vmdk` | `spare2` (`vm-289032`) | `1faaa46a-928d-dfea-13a7-98b78522551a` | `300.00 GB` | `78.58 GB` | `39.29 GB` | **`39.29 GB`** | `35.17 GB` |
| **Spare/KV (10)** | `kvcache-cx1-20261014_2.vmdk` | `kvcache-cx1-20261014` (`vm-296077`) | `6c08d06a-d814-8ea9-69cb-98b785225eea` | `300.00 GB` | `74.79 GB` | `37.39 GB` | **`37.40 GB`** | `32.96 GB` |
| **Spare/KV (10)** | `spare9.vmdk` | `spare9` (`vm-289039`) | `86aea46a-4262-6dc4-6cb9-98b78522551a` | `300.00 GB` | `55.45 GB` | `27.73 GB` | **`27.72 GB`** | `26.07 GB` |
| **Spare/KV (10)** | `spare3.vmdk` | `spare3` (`vm-289033`) | `31aaa46a-2a41-3f88-040e-98b785225eea` | `300.00 GB` | `39.90 GB` | `19.95 GB` | **`19.95 GB`** | `18.31 GB` |
| **Spare/KV (10)** | `spare.vmdk` | `spare` (`vm-289031`) | `08aaa46a-7044-4b96-e933-98b78522551a` | `300.00 GB` | `25.14 GB` | `12.57 GB` | **`12.57 GB`** | `11.11 GB` |
| *Subtotal (10)* | *10 Spare/KVCache VMDKs* | — | — | *`3000.00 GB`* | *`2613.19 GB`* | *`1306.60 GB`* | ***`1306.59 GB`*** | *`1265.67 GB`* |
| **GRAND TOTAL (14)** | **All 14 `FTT=1` VMDKs** | — | — | **`9020.00 GB`** | **`3599.55 GB`** | **`1799.79 GB`** | **`1799.76 GB` (`1.76 TiB`)** | **`1751.87 GB`** |

---

## 3. Complete Orphaned vSAN Object & Unregistered VMDK Audit

### 3.1 Global Denominator Reconciliation (`174` vSAN DOM Objects vs. `86` vCenter VMs)
- **vSAN DOM Objects (`esxcli vsan debug object list -all=true`)**: `174` total `ObjectUUID:` entries (`173` objects with a filesystem `Path:` on `/vmfs/volumes/vsan:5221e29f8740f516-72730ae338b3eb48/...` + `1` `vdfs` root object `a2062569-2c6e-068f-e30a-98b78522598c` with `Path: (null)`):
  - `64` `vmnamespace` objects (`54` registered VM namespaces + `10` non-VM / unregistered namespaces)
  - `66` `vdisk` objects (`64` attached to registered VMs or their snapshot parent chains + `2` unattached/orphaned VMDKs)
  - `43` `vmswap` objects (`43` belonging to the `42` `poweredOn` VMs on `vsanDatastore`; **`0` orphaned `vmswap` DOM objects**)
  - `1` `vdfs` object (`a2062569-2c6e-068f-e30a-98b78522598c`, `0.09 GB` Used)
- **vCenter Inventory (`govc find / -type m` / `vim.VirtualMachine`, `RC=0`)**: `86` total VMs:
  - `54` connected VMs residing on `vsanDatastore` (`42` `poweredOn`, `12` `poweredOff`)
  - `8` connected VMs residing on host-local VMFS `[datastore1*]` (`VC04`, `VC04-Peer`, `VC04-Witness`, `VMware-SHD-4.0.8-24579038`, `VMware-skyline`, `VMware-vSAN-File-Services-Appliance-8.0.3.1000-25429389_OVF10`, `nsx-unified-appliance-4.2.3.3.0.25171321`, `vRealize-Operations-Manager-Appliance-8.18.7.25423534`)
  - `2` in-memory CRX `vCLS` cluster agent VMs (`vCLS-30393137-3136-514d-5837-333030375037`, `vCLS-37383638-3330-4d32-3239-31383033484c`)
  - `22` stale `lens-*` VM inventory entries with `runtime.connectionState = 'orphaned'` (`0.00 GB` on vSAN; their `44` vSAN objects were already deleted from vSAN DOM, but still appear in vCenter's `QueryObjectIdentities` cache, explaining `218 - 44 = 174` live objects).

---

### 3.2 Category A: Unregistered / Orphaned VMDKs & VM Namespaces on `vsanDatastore` (`514.60 GB` Used)
*(No user data was deleted; exact paths, sizes, UUIDs, and cross-mount dependencies are documented below.)*

| # | Object UUID | Group UUID | Type | Directory / File | Provisioned | Physical Used | 4K Used | FTT | Forensic Audit Status & Safety Notes |
| :-: | :--- | :--- | :--- | :--- | ---: | ---: | ---: | :-: | :--- |
| 1 | `28c86a69-dab3-fcfc-c0c1-98b78522551a` | `27c86a69-1e1f-9b83-22e6-98b78522551a` | `vdisk` | `/vmfs/volumes/vsan:5221e29f8740f516-72730ae338b3eb48/27c86a69-1e1f-9b83-22e6-98b78522551a/Media server.vmdk` | `3072.00 GB` | **`498.98 GB`** | `498.97 GB` | `0` | **ORPHANED VMDK**: Unattached boot/OS disk from unregistered `Media server.vmx` (last modified `2026-03-08`). Not attached to any registered VM in vCenter. |
| 2 | `27c86a69-1e1f-9b83-22e6-98b78522551a` | `27c86a69-1e1f-9b83-22e6-98b78522551a` | `vmnamespace` | `/vmfs/volumes/vsan:5221e29f8740f516-72730ae338b3eb48/Media server` | `255.00 GB` | **`0.46 GB`** | `0.10 GB` | `0` | **CRITICAL CROSS-MOUNT WARNING — DO NOT DELETE NAMESPACE**: Although `Media server.vmx` is unregistered, this namespace directory ALSO houses `Media server_1.vmdk` (`28c86a69-a03d-8613-acbf-98b78522551a`, `14,769.14 GB` Used) and `Media server_2.vmdk` (`29c86a69-8c4c-0a3f-8930-98b78522551a`, `25,669.48 GB` Used), which are **actively mounted as Hard disk 2 & Hard disk 3 on registered VM `Plex/JellyFin` (`vm-179004`)**! Also contains two stale flat swap files (`vmx-Media server-...-2.vswp` `142.0 MB` and `vmx-Media server-...-3.vswp` `142.0 MB`). |
| 3 | `a9da5369-b22f-b7cd-7290-98b78522598c` | `a5da5369-0850-41ed-0c31-98b78522598c` | `vdisk` | `/vmfs/volumes/vsan:5221e29f8740f516-72730ae338b3eb48/a5da5369-0850-41ed-0c31-98b78522598c/ARR Stack.vmdk` | `500.00 GB` | **`14.29 GB`** | `12.91 GB` | `0` | **ORPHANED VMDK**: Disk of unregistered VM `ARR Stack.vmx` (last modified `2026-01-06`). Not attached to any registered VM. |
| 4 | `a5da5369-0850-41ed-0c31-98b78522598c` | `a5da5369-0850-41ed-0c31-98b78522598c` | `vmnamespace` | `/vmfs/volumes/vsan:5221e29f8740f516-72730ae338b3eb48/ARR Stack` | `255.00 GB` | **`0.28 GB`** | `0.05 GB` | `0` | **ORPHANED VM NAMESPACE**: Unregistered VM directory containing `ARR Stack.vmx`, `ARR Stack.nvram`, logs, and stale `88.0 MB` `vmx-ARR Stack-c2ee7dc7f3cadd107e111d52288ac62cf38042a8c68d843f68f550d295eac931-2.vswp` (last modified `2026-01-11`). |
| 5 | `44237669-fcf3-916a-2a21-48df37e0b790` | `44237669-fcf3-916a-2a21-48df37e0b790` | `vmnamespace` | `/vmfs/volumes/vsan:5221e29f8740f516-72730ae338b3eb48/Plex Windows` | `255.00 GB` | **`0.59 GB`** | `0.13 GB` | `0` | **ORPHANED VM NAMESPACE**: Leftover directory from deleted VM `Plex Windows`; contains only `Plex Windows.nvram` (`276.0 KB`, `2026-03-08`), no `.vmx` or `.vmdk`. |

---

### 3.3 Category B: Duplicate / Renamed vSAN Stats & Trace System Objects (`72.82 GB` Orphaned / Conflict Used)
| # | Object UUID | Type | `DirectoryName` | `Path` | Provisioned | Physical Used | 4K Used | FTT | Status |
| :-: | :--- | :--- | :--- | :--- | ---: | ---: | ---: | :-: | :--- |
| 1 | `51afa66a-7854-2e38-8a10-98b78522598c` | `vmnamespace` | `.vsan.stats` | `/vmfs/volumes/vsan:5221e29f8740f516-72730ae338b3eb48/.vsan.stats` | `512.00 GB` | `9.70 GB` | `9.48 GB` | `0` | **ACTIVE** vSAN Performance Service Stats DB object (`healthy`). |
| 2 | `02010c69-e24b-e701-89c7-48df37d56620` | `vmnamespace` | `.vsan.stats-1` | `/vmfs/volumes/vsan:5221e29f8740f516-72730ae338b3eb48/.vsan.stats` | `512.00 GB` | **`32.66 GB`** | `32.27 GB` | `0` | **CONFLICT / STALE RENAMED STATS DB** (flagged by `vsan health cluster get -t "Stats DB object"`). |
| 3 | `88ba6f6a-100e-0bdb-d031-98b785225eea` | `vmnamespace` | `.vsan.stats-2` | `/vmfs/volumes/vsan:5221e29f8740f516-72730ae338b3eb48/.vsan.stats` | `512.00 GB` | **`9.98 GB`** | `9.72 GB` | `0` | **CONFLICT / STALE RENAMED STATS DB** (flagged by `vsan health cluster get -t "Stats DB object"`). |
| 4 | `91ba6f6a-fc07-db14-8b8e-98b785225eea` | `vmnamespace` | `.vsan.trace` | `/vmfs/volumes/vsan:5221e29f8740f516-72730ae338b3eb48/.vsan.trace` | `16.00 GB` | `30.07 GB` | `29.44 GB` | `1` | **ACTIVE** vSAN trace object (`traceobject`, modified `2026-10-17`). |
| 5 | `710c0c69-2009-908d-6ab1-48df37e0b790` | `vmnamespace` | `.vsan.trace-1` | `/vmfs/volumes/vsan:5221e29f8740f516-72730ae338b3eb48/.vsan.trace` | `16.00 GB` | **`30.18 GB`** | `29.63 GB` | `1` | **STALE RENAMED TRACE OBJECT** (files inside last modified `2026-08-02`). |

---

### 3.4 Category C: Non-VM Datastore Directories & System Objects (`63.73 GB` Used)
| # | Object UUID | Type | `DirectoryName` | `Path` | Provisioned | Physical Used | 4K Used | FTT | Contents / Purpose |
| :-: | :--- | :--- | :--- | :--- | ---: | ---: | ---: | :-: | :--- |
| 1 | `d18a0c69-ef29-4f8c-6a17-48df37e0b790` | `vmnamespace` | `contentlib-14f9afa4-9283-4a2e-8b24-df8ab4614ef7` | `/vmfs/volumes/vsan:5221e29f8740f516-72730ae338b3eb48/contentlib-14f9afa4-9283-4a2e-8b24-df8ab4614ef7` | `255.00 GB` | `44.35 GB` | `44.02 GB` | `1` | Active vCenter Content Library backing store (`5` item directories). |
| 2 | `731e0d69-d215-6e50-470c-48df37e0b790` | `vmnamespace` | `New Folder` | `/vmfs/volumes/vsan:5221e29f8740f516-72730ae338b3eb48/New Folder` | `255.00 GB` | `19.29 GB` | `19.02 GB` | `1` | Datastore ISO/image folder containing `26100.1.240331-1435.ge_release_amd64fre_SERVER_LOF_PACKAGES_OEM.iso` (`6.4 GB`), `ubuntu-24.04.3-live-server-amd64.iso` (`3.1 GB`), and `kali-linux-2025.3-vmware-amd64.vmwarevm`. |
| 3 | `a2062569-2c6e-068f-e30a-98b78522598c` | `vdfs` | `N/A` | `(null)` | `255.00 GB` | `0.09 GB` | `0.06 GB` | `0` | vSAN Distributed File System (`vdfs`) internal root metadata object. |

---

### 3.5 Category D: `.vswp` Swap Objects Audit (`43` `vmswap` DOM Objects)
- **vSAN DOM `vmswap` Objects**: `43` total objects in `esxcli vsan debug object list -all=true`.
  - **`0` orphaned `vmswap` DOM objects**: Every single one of the `43` `vmswap` DOM objects maps to one of the `42` currently `poweredOn` VMs on `vsanDatastore`.
  - `vcMos['vsan-cluster-object-system'].QueryInaccessibleVmSwapObjects(cluster=cluster)` returned `[]` (`0` inaccessible swap objects).
- **Stale Flat `.vswp` Files Inside Unregistered VM Namespaces**:
  - `[vsanDatastore] Media server/vmx-Media server-011d5a6d29a1d8a04542209f5e09d3e97adc4f955e48409ec088e93111233079-2.vswp` (`142.0 MB`, `2026-02-05`)
  - `[vsanDatastore] Media server/vmx-Media server-011d5a6d29a1d8a04542209f5e09d3e97adc4f955e48409ec088e93111233079-3.vswp` (`142.0 MB`, `2026-02-19`)
  - `[vsanDatastore] ARR Stack/vmx-ARR Stack-c2ee7dc7f3cadd107e111d52288ac62cf38042a8c68d843f68f550d295eac931-2.vswp` (`88.0 MB`, `2026-01-06`)

---

### 3.6 Category E: Snapshot & Stale Ephemeral Clone Audit
1. **VMs with Snapshots (`2` VMs total across all `86` VMs)**:
   - `AI Inference` (`vm-289024`, `poweredOn`): `1` snapshot `pre-T4-O701` (created `2026-09-15`):
     - Active delta disk: `fecaa86a-2125-d0db-0b09-48df37e0b790` (`[vsanDatastore] 4d6ba46a-54b1-b6dc-ffbb-98b78522551a/AI Inference-000001.vmdk`, Size `300.00 GB`, Used `19.84 GB`, `FTT=0`)
     - Base parent disk: `4e6ba46a-8445-7391-5d48-98b78522551a` (`[vsanDatastore] 4d6ba46a-54b1-b6dc-ffbb-98b78522551a/AI Inference.vmdk`, Size `300.00 GB`, Used `77.15 GB`, `FTT=0`)
   - `bd-ubuntu-2404-template-20261003` (`vm-296027`, `poweredOff`): `1` snapshot `pristine-20261003` (created `2026-10-03`, template base snapshot):
     - Delta disk: `1647c16a-1a6f-78f0-a515-48df37d56620` (`bd-ubuntu-2404-template-20261003-000001.vmdk`, Used `0.01 GB`, `FTT=0`)
     - Base disk: `d638c16a-d12a-9dff-262c-48df37e0b790` (`bd-ubuntu-2404-template-20261003.vmdk`, Used `18.27 GB`, `FTT=0`)
2. **22 Orphaned `lens-*` VM Inventory Records in vCenter (`runtime.connectionState = 'orphaned'`, `0.00 GB` vSAN Used)**:
   - `lens-a12-cx7-20261003` (`vm-296034`), `lens-a13-cxcorrectness2-20261003` (`vm-296012`), `lens-a18-cxcorrectness4-20261003` (`vm-296018`), `lens-c09-cx5-r2-20261004` (`vm-296044`), `lens-f001-cxcorrectness3-20261003` (`vm-296014`), `lens-f001-dhcp-cx3-20261003` (`vm-296016`), `lens-g105-cx10-relens-20261004` (`vm-296050`), `lens-g113-cx13-r3-20261004` (`vm-296049`), `lens-o1760-cx4-20261004` (`vm-296036`), `lens-o1799-cx3-r2-20261004` (`vm-296035`), `lens-o1807-r2-cxcorrectness2-20261004` (`vm-296037`), `lens-o1815-r11-cx4-20261004` (`vm-296042`), `lens-o1815-r12-cx11-20261004` (`vm-296045`), `lens-o1815-r9-cx3-20261004` (`vm-296038`), `lens-o1819-r16-cx4-20261004` (`vm-296039`), `lens-o1819-r17-bdrc-20261004` (`vm-296041`), `lens-o1826-c01-cx1-20261004` (`vm-296040`), `lens-o1876-5-bdrc-20261004` (`vm-296046`), `lens-o1876-5-cx12-20261004` (`vm-296047`), `lens-scf9-r4-cx209-20261013` (`vm-296072`), `lens-sg3-r7-cx210-20261013` (`vm-296073`), `lens-t154-edge1-cxcorrectness-20261003` (`vm-296010`).
3. **6 Powered-Off Ephemeral Test/Lens Clones Still Consuming Live vSAN Storage (`66.20 GB` Total Used)**:
   - `bd-scratch-dhcp-20261003T044904Z` (`vm-296020`): NS `7889c06a-2694-ecd5-180a-98b785228d2d` (`0.22 GB`) + Disk `7a89c06a-5a66-89a8-d9e6-98b785228d2d` (`6.80 GB`) = `7.02 GB`
   - `bd-lens-dhcp-first-20261003T1432Z` (`vm-296022`): NS `2612c16a-9dc8-00fb-f452-48df37e0b790` (`0.19 GB`) + Disk `2712c16a-71a6-3b5e-558d-48df37e0b790` (`6.80 GB`) = `6.99 GB`
   - `bd-lens-dhcp-first-20261003T1524Z-r2` (`vm-296024`): NS `401ec16a-c2d3-bd9b-944d-48df37e0b790` (`0.17 GB`) + Disk `411ec16a-68f8-195a-a6f5-48df37e0b790` (`6.86 GB`) = `7.03 GB`
   - `bd-lens-dhcp-first-20261003T1559Z-r3` (`vm-296026`): NS `5e26c16a-5b03-9205-d97f-48df37e0b790` (`0.18 GB`) + Disk `5e26c16a-df5a-db83-c16a-48df37e0b790` (`6.86 GB`) = `7.04 GB`
   - `bd-lens-dhcp-tpltest-20261003T1825Z` (`vm-296029`): NS `aa48c16a-92f5-6dfe-e692-48df37e0b790` (`0.18 GB`) + Disk `ab48c16a-158e-55a6-4c1a-48df37e0b790` (`19.45 GB`) = `19.63 GB`
   - `lens-r2eb3-cx5-20261014` (`vm-296075`): NS `b9d9cf6a-3619-0de5-447e-48df37e0b790` (`0.15 GB`) + Disk `bbd9cf6a-43de-fded-68b4-48df37e0b790` (`18.34 GB`) = `18.49 GB`
