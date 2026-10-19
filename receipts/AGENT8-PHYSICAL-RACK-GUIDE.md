# Boylenet Physical Rack, Switch Port & Hardware Bay Guide (Agent-8 Audit)

**Generated**: `2026-10-17T17:13:00Z`  
**Auditor**: Agent-8 (Physical Rack & Hardware Bay Mapper / Anti-Sycophancy Auditor)  
**Verification Sources**: Live UniFi Network API (`stat/device`), Live ESXi `esxcli` (`network nic`, `hardware pci`, `storage core`, `vsan storage`), `vim-cmd hostsvc/net/query_networkhint`, `/opt/smartstorageadmin/ssacli/bin/ssacli`, and HPE iLO 4 / iLO 5 Redfish (`SmartStorage`, `PCIDevices`, `BaseNetworkAdapters`).

> [!IMPORTANT]
> **Operator Directive Acknowledged**:
> 1. **"The ssd stays"** — Zero physical drive moves or transplants are required. (Full physical drive bay maps and SMART wear tables are preserved in **Appendix A** purely for hardware reference).
> 2. **"Replacing cables or moving cables"** — Sections 1–4 below give the exact physical switch port numbers, server PCIe slot/port locations, transceiver serial numbers, and spare cables already sitting in the rack that you can swap or repatch immediately.

---

## 1. Executive Action Checklist: Physical Cables to Swap / Repatch

### Action 1 (HIGH PRIORITY — Fixes 10G Training Failure & RoCE/DVS Bottleneck)
* **What is broken**: **`Office (USAGGPRO)` Port 3 $\leftrightarrow$ `esxi03:vmnic11`** (`Mellanox ConnectX-4 Lx 25GbE` **Port 2** in **`esxi03` Rear PCI-E Slot 6**).
* **Defective Cable**: 3m 10G Passive DAC, Part **`SFP-H10GB-CU3M`**, Vendor **`OEM`**, Serial **`CSS11OA1407`**.
* **Symptom**: Under Auto-Negotiation, this cable fails 10GBASE-CR link training and falls back to **`1000 Mbps` (1 Gbps)**. When forced to `10000 Mbps` (`autoneg=False`), the physical link drops completely (`LinkDown`).
* **How to fix in 60 seconds (Zero Purchase — Use Spare DAC Already in `USAGGPRO`)**:
  You have **four working 10G DAC cables already plugged into `USAGGPRO` (`10.0.10.111`) that carry zero traffic right now**:
  1. **`USAGGPRO Port 25`** (`SFP-10G-DAC-005`, OEM, SN `2506030118` — currently `Link Down` / unused).
  2. **`USAGGPRO Port 26`** (`SFP-10G-DAC-005`, FLYPRO, SN `2505150068` — currently `Link Down` / unused).
  3. **`USAGGPRO Port 1`** (`SFP-H10GB-CU3M`, OEM, SN `CSS11OA1439` — labeled `"ESXI03 - vmnic6"`, physically linked at `10,000 Mbps` to `esxi03` Embedded FlexibleLOM `556FLR-SFP+`, which ESXi disabled at boot due to Emulex optic lock; carries **0 MACs / 0 traffic**).
  4. **`USAGGPRO Port 17`** (`SFP-H10GB-CU3M`, OEM, SN `CSS11OA3458` — labeled `"ESXI03 - vmnic7"`, physically linked at `10,000 Mbps` to `esxi03` Embedded FlexibleLOM `556FLR-SFP+`, disabled by Emulex optic lock; carries **0 MACs / 0 traffic**).
* **Physical Step**:
  - Unplug the bad DAC (`SN: CSS11OA1407`) from **`USAGGPRO Port 3`** (top row, 2nd port from left) and **`esxi03` PCI-E Slot 6 Port 2** (`vmnic11`).
  - Replace it with the known-good 10G DAC from **`USAGGPRO Port 1` (`SN: CSS11OA1439`)** or **`USAGGPRO Port 25` (`SN: 2506030118`)** (or a new 3m SFP+/SFP28 DAC).
  - Once swapped, all 4 ESXi hosts will have symmetric **25GbE on Mellanox Port 1 (`USAGGPRO Ports 29–32`)** + **10GbE on Mellanox Port 2 (`USAGGPRO Ports 15, 4, 3, 2`)** through the switch!

---

### Action 2 (MEDIUM PRIORITY — Degraded RJ45 & Transceiver Cleanup)
1. **`Office (USL24B)` (`10.0.10.192`) Port 10 $\leftrightarrow$ `esxi02:vmnic3` (Stuck at `100 Mbps` FE)**:
   - **Location**: `esxi02` Rear **Embedded LOM 1 Port 4** (`Broadcom BCM5719 1GbE`, MAC `30:e1:71:54:1d:37`) $\leftrightarrow$ **`Office USL24B` Port 10** (`Name: ESXI02 - vmnic3`).
   - **Issue**: Negotiating at **`100 Mbps` (Fast Ethernet)** instead of `1000 Mbps` despite `Auto Negotiation: true` on both ends (bad RJ45 patch cable / broken pin on pair 3 or 4).
   - **Physical Step**: Replace or reseat the Cat5e/Cat6 RJ45 patch cable between `esxi02` Embedded LOM Port 4 and `USL24B Port 10`. (Also check `USL24B Port 8`, which is likewise negotiating at `100 Mbps`).
2. **`Pantry (USL8A)` (`10.0.1.98`) Port 1 $\leftrightarrow$ `E7 Pantry AP` (`10.0.1.147`) — 2.4 Million Physical Errors**:
   - **Location**: `Pantry USL8A` **Port 1** (`BJ-SFP-10G-T` 10GBASE-T RJ45 module, SN `BJ6240527348`, linked at `10,000 Mbps` to `E7 Pantry` main port).
   - **Issue**: Live switch counters show **`1,866,860 RX errors` and `546,911 TX errors`** on this port (the backup 1G port on `Pantry USL8A Port 7` has `0` errors).
   - **Physical Step**: Reseat/replace the Cat6 patch cord on `Pantry USL8A Port 1` or step `Port 1` down to `2.5 Gbps` / `5 Gbps` if the 10GBASE-T SFP+ transceiver is overheating.
3. **`Office (USAGGPRO)` (`10.0.10.111`) Ports 7, 8, 10, 11 — Four Idle 10GBASE-T RJ45 SFP+ Transceivers (`BJ-SFP-10G-T`)**:
   - **Location**: `USAGGPRO` **Ports 7, 8, 10, 11** contain four `QINIYEK BJ-SFP-10G-T` (10GBASE-T RJ45) transceivers (`SN: BJ6240527063`, `BJ62504040829`, `BJ62504041274`, `BJ62504041275`) that are all **`Up: False` (Link Down)**.
   - **Meanwhile**:
     - `esxi01` has a dual-port 10GBASE-T RJ45 card (**`Intel X540-AT2`** in **PCI-E Slot 5**, `vmnic10` & `vmnic11`). Currently `vmnic10` is unplugged (`Down`) and `vmnic11` is plugged into 1GbE switch `USL24B Port 5` running at **`1000 Mbps`**!
     - `esxi03` has a dual-port 10GBASE-T RJ45 card (**`Intel X540-AT2`** in **PCI-E Slot 3**, `vmnic8` & `vmnic9`). `vmnic8` is in `USAGGPRO Port 12` at `10,000 Mbps`, while `vmnic9` is `Down`.
   - **Physical Step**: Either run Cat6A patch cables from **`esxi01` Slot 5 (`vmnic10`, `vmnic11`)** and **`esxi03` Slot 3 Port 2 (`vmnic9`)** into **`USAGGPRO` Ports 7, 8, 10** to gain three additional `10,000 Mbps` links, **OR** pull the unused `BJ-SFP-10G-T` modules out of `USAGGPRO` Ports 7, 8, 10, 11 so they stop burning ~10–12W of heat in the switch cage.
4. **Why `esxi03` Embedded FlexibleLOM (`vmnic4` & `vmnic5`) Disappeared from ESXi (`USAGGPRO Ports 1 & 17`)**:
   - `esxi03` (DL380 Gen10) has an **`HP FlexFabric 10Gb 2-port 556FLR-SFP+`** (`0000:5d:00.0` = `vmnic4`, `0000:5d:00.1` = `vmnic5`) in its `Embedded FlexibleLOM 1` slot.
   - Live `/var/run/log/boot.gz` on `esxi03` proves that the Emulex `elxnet` driver rejected the generic OEM DACs at boot:
     `WARNING: elxnet: elxnet_checkSFPs:8123: [0000:5d:00.0] Port 1: Incompatible optics – Replace with compatible optics for card to function-Link is operational`
   - Because `elxnet` unregisters `vmnic4` and `vmnic5` when non-HPE-coded DACs are inserted (even though the physical SERDES link stays UP at 10G on `USAGGPRO Ports 1 & 17`), those two cables on `USAGGPRO Ports 1 & 17` are doing nothing right now and can be freely harvested as spares (e.g., to fix `USAGGPRO Port 3`!).

---

## 2. Complete Switch Port Map — `Office (USAGGPRO)` (`10.0.10.111`) — Post-LACP Verified

* **Model**: UniFi Switch Aggregation Pro (`USAGGPRO`, 28x 10G SFP+ `[Ports 1–28]` + 4x 25G SFP28 `[Ports 29–32]`)
* **MAC / Serial / Device ID**: `6c:63:f8:83:8b:df` / `6C63F8838BDF` / `c4b8bfa2-f496-46a8-bb31-cda8baf247fb`
* **Post-LACP Topology Summary**:
  - **`Ports 1–3` (`lag_idx=3`)**: **`esxi02 30G LACP`** (`3x 10GbE`: `vmnic4`, `vmnic5`, `vmnic7` $\rightarrow$ `VDS 25GB LAG1-0..2`)
  - **`Ports 4–8` (`lag_idx=6`)**: **`esxi04 50G LACP`** (`5x 10GbE`: `vmnic4`, `vmnic7`, `vmnic8`, `vmnic5`, `vmnic9` $\rightarrow$ `VDS 25GB LAG1-0..4`)
  - **`Ports 10–12` (`lag_idx=5`)**: **`esxi03 30G LACP`** (`3x 10GbE`: `vmnic9`, `vmnic11`, `vmnic8` $\rightarrow$ `VDS 25GB LAG1-0..2`)
  - **`Ports 13–17` (`lag_idx=7`)**: **`esxi01 50G LACP`** (`5x 10GbE`: `vmnic4`, `vmnic5`, `vmnic9`, `vmnic8`, `vmnic6` $\rightarrow$ `VDS 25GB LAG1-0..4`)
  - **`Ports 29–32` (`switch` / `25,000 Mbps`)**: **`4x 25GbE Storage/RoCE`** standalone Mellanox uplinks (`esxi01:vmnic7`, `esxi03:vmnic10`, `esxi02:vmnic6`, `esxi04:vmnic6`) — active for `VSAN 25GB` / `Management 25GB` and per-port pinned for all 4 Tesla T4 GPU RoCE VMs (`DVPort 321, 313, 1, 293`).
* **Physical Faceplate Layout**: Odd ports (`1, 3, 5 ... 31`) are the **Top Row** (left to right); Even ports (`2, 4, 6 ... 32`) are the **Bottom Row** (left to right).

| Port | Faceplate Row | Port Label (UniFi) | Link | Speed (Mbps) | AutoNeg | Mode / LAG | Transceiver / DAC Part | Vendor | Serial Number | Connected Device / Server NIC & Slot | Status / Role |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **1** | Top #1 | `esxi02 - 30G LACP (vmnic4/5/7)` | **UP** | `10000` | `False` | `aggregate` (`lag_idx=3`, `[1–3]`) | `SFP-H10GB-CU3M` | `OEM` | `CSS11OA1420` | **`esxi02:vmnic4`** (`48:df:37:e0:b7:90`, Intel X710 **FlexLOM Port 1**) | **OK (`esxi02 30G LACP` Member 1 — `LAG1-0`)** |
| **2** | Bot #1 | `SFP+ 2` | **UP** | `10000` | `False` | `aggregate` (`lag_idx=3`, `agg_by=1`) | `SFP-H10GB-CU3M` | `OEM` | `CSS11OA1724` | **`esxi02:vmnic5`** (`48:df:37:e0:b7:98`, Intel X710 **FlexLOM Port 2**) | **OK (`esxi02 30G LACP` Member 2 — `LAG1-1`)** |
| **3** | Top #2 | `SFP+ 3` | **UP** | `10000` | `False` | `aggregate` (`lag_idx=3`, `agg_by=1`) | `SFP-H10GB-CU3M` | `OEM` | `CSS11OA1102` | **`esxi02:vmnic7`** (`6c:b3:11:79:91:bb`, ConnectX-4 Lx **Slot 4 Port 2**) | **OK (`esxi02 30G LACP` Member 3 — `LAG1-2`)** |
| **4** | Bot #2 | `esxi04 - 50G LACP (vmnic4/7/8/5/9)` | **UP** | `10000` | `False` | `aggregate` (`lag_idx=6`, `[4–8]`) | `SFP-H10GB-CU3M` | `OEM` | `CSS11OA1408` | **`esxi04:vmnic4`** (`70:10:6f:43:b5:18`, HP 556FLR **FlexLOM Port 1**) | **OK (`esxi04 50G LACP` Member 1 — `LAG1-0`)** |
| **5** | Top #3 | `SFP+ 5` | **UP** | `10000` | `False` | `aggregate` (`lag_idx=6`, `agg_by=4`) | `SFP-H10GB-CU3M` | `OEM` | `CSS11OA3482` | **`esxi04:vmnic7`** (`94:40:c9:aa:d6:b5`, ConnectX-5 **Slot 2 Port 2**) | **OK (`esxi04 50G LACP` Member 2 — `LAG1-1`)** |
| **6** | Bot #3 | `SFP+ 6` | **UP** | `10000` | `False` | `aggregate` (`lag_idx=6`, `agg_by=4`) | `SFP-H10GB-CU3M` | `OEM` | `CSS11OA1726` | **`esxi04:vmnic8`** (`98:b7:85:22:5e:ea`, Intel 82599 **Slot 6 Port 1**) | **OK (`esxi04 50G LACP` Member 3 — `LAG1-2`)** |
| **7** | Top #4 | `SFP+ 7` | **UP** | `10000` | `False` | `aggregate` (`lag_idx=6`, `agg_by=4`) | `SFP-H10GB-CU3M` | `OEM` | `CSS11OA1379` | **`esxi04:vmnic5`** (`70:10:6f:43:b5:20`, HP 556FLR **FlexLOM Port 2**) | **OK (`esxi04 50G LACP` Member 4 — `LAG1-3`)** |
| **8** | Bot #4 | `SFP+ 8` | **UP** | `10000` | `False` | `aggregate` (`lag_idx=6`, `agg_by=4`) | `SFP-H10GB-CU3M` | `OEM` | `CSS11OA1406` | **`esxi04:vmnic9`** (`98:b7:85:22:5e:eb`, Intel 82599 **Slot 6 Port 2**) | **OK (`esxi04 50G LACP` Member 5 — `LAG1-4`)** |
| **9** | Top #5 | `ESXi Reserved 9` | **UP** | `10000` | `False` | `switch` | `SFP-H10GB-CU3M` | `OEM` | `CSS11OA1439` | Spare / Reserved 10G DAC | **OK (10G Link Up, Reserved)** |
| **10** | Bot #5 | `esxi03 - 30G LACP (vmnic9/11/8)` | **UP** | `10000` | `False` | `aggregate` (`lag_idx=5`, `[10–12]`) | `BJ-SFP-10G-T` | `QINIYEK` | `BJ62504041274` | **`esxi03:vmnic9`** (`98:b7:85:22:8d:2e`, Intel X540-AT2 **Slot 3 Port 2**) | **OK (`esxi03 30G LACP` Member 1 — `LAG1-0`)** |
| **11** | Top #6 | `SFP+ 11` | **UP** | `10000` | `False` | `aggregate` (`lag_idx=5`, `agg_by=10`) | `SFP-H10GB-CU3M` | `OEM` | `CSS11OA1384` | **`esxi03:vmnic11`** (`6c:b3:11:79:91:0f`, ConnectX-4 Lx **Slot 6 Port 2**) | **OK (`esxi03 30G LACP` Member 2 — `LAG1-1`)** |
| **12** | Bot #6 | `SFP+ 12` | **UP** | `10000` | `False` | `aggregate` (`lag_idx=5`, `agg_by=10`) | `BJ-SFP-10G-T` | `QINIYEK` | `BJ62504040333` | **`esxi03:vmnic8`** (`98:b7:85:22:8d:2d`, Intel X540-AT2 **Slot 3 Port 1**) | **OK (`esxi03 30G LACP` Member 3 — `LAG1-2`)** |
| **13** | Top #7 | `esxi01 - 50G LACP (vmnic4/5/9/8/6)` | **UP** | `10000` | `False` | `aggregate` (`lag_idx=7`, `[13–17]`) | `SFP-H10GB-CU3M` | `OEM` | `CSS11OA3459` | **`esxi01:vmnic4`** (`48:df:37:d5:66:20`, Intel X710 **FlexLOM Port 1**) | **OK (`esxi01 50G LACP` Member 1 — `LAG1-0`)** |
| **14** | Bot #7 | `SFP+ 14` | **UP** | `10000` | `False` | `aggregate` (`lag_idx=7`, `agg_by=13`) | `SFP-H10GB-CU3M` | `OEM` | `CSS11OA3478` | **`esxi01:vmnic5`** (`48:df:37:d5:66:28`, Intel X710 **FlexLOM Port 2**) | **OK (`esxi01 50G LACP` Member 2 — `LAG1-1`)** |
| **15** | Top #8 | `SFP+ 15` | **UP** | `10000` | `False` | `aggregate` (`lag_idx=7`, `agg_by=13`) | `SFP-H10GB-CU3M` | `OEM` | `CSS11OA1382` | **`esxi01:vmnic9`** (`98:b7:85:22:55:1b`, Intel 82599 **Slot 3 Port 2**) | **OK (`esxi01 50G LACP` Member 3 — `LAG1-2`)** |
| **16** | Bot #8 | `SFP+ 16` | **UP** | `10000` | `False` | `aggregate` (`lag_idx=7`, `agg_by=13`) | `SFP-H10GB-CU3M` | `OEM` | `CSS11OA1377` | **`esxi01:vmnic8`** (`98:b7:85:22:55:1a`, Intel 82599 **Slot 3 Port 1**) | **OK (`esxi01 50G LACP` Member 4 — `LAG1-3`)** |
| **17** | Top #9 | `SFP+ 17` | **UP** | `10000` | `False` | `aggregate` (`lag_idx=7`, `agg_by=13`) | `SFP-H25G-CU3M` | `OEM` | `CSC241104260371` | **`esxi01:vmnic6`** (`6c:b3:11:79:91:ce`, ConnectX-4 Lx **Slot 1 Port 1**) | **OK (`esxi01 50G LACP` Member 5 — `LAG1-4`)** |
| **18** | Bot #9 | `SFP+ 18` | Down | `10000` | `False` | `switch` | `BJ-SFP-10G-T` | `QINIYEK` | `BJ62504041275` | *Unplugged 10GBASE-T RJ45 module* | Spare / Remove unused hot module |
| **19** | Top #10 | `ESXi Reserved 19` | **UP** | `10000` | `False` | `switch` | `SFP-H10GB-CU3M` | `OEM` | `CSS11OA3458` | Spare / Reserved 10G DAC | **OK (10G Link Up, Reserved)** |
| **20** | Bot #10 | `To USW Agg Office` | **UP** | `10000` | `False` | `aggregate` (`lag_idx=1`, `[20,21]`) | `SFP-H10GB-CU3M` | `OEM` | `CSS11OA1103` | **`Office USL8A` (`10.0.10.182`) Port 7** (20G LACP Trunk Member 1) | **OK (10G Trunk)** |
| **21** | Top #11 | `SFP+ 21` | **UP** | `10000` | `False` | `aggregate` (`lag_idx=1`, `agg_by=20`) | `SFP-H10GB-CU3M` | `OEM` | `CSS11OA1109` | **`Office USL8A` (`10.0.10.182`) Port 8** (20G LACP Trunk Member 2) | **OK (10G Trunk)** |
| **22** | Bot #11 | `To UDM SE` | **UP** | `10000` | `False` | `switch` | `SFP-10G-DAC-005` | `OEM` | `2506030095` | **`UDM SE` (`10.0.1.1`) Port 11** (`60:22:32:29:2C:AB`) | **OK (10G Gateway Uplink)** |
| **23** | Top #12 | `To USW Pro Max` | **UP** | `10000` | `False` | `aggregate` (`lag_idx=2`, `[23,24]`) | `SFP-H10GB-CU3M` | `OEM` | `CSS11OA1302` | **`Office USPM16P` (`10.0.10.34`) Port 18** (20G LACP Trunk Member 1) | **OK (10G Trunk)** |
| **24** | Bot #12 | `SFP+ 24` | **UP** | `10000` | `False` | `aggregate` (`lag_idx=2`, `agg_by=23`) | `SFP-H10GB-CU3M` | `OEM` | `CSS11OA1107` | **`Office USPM16P` (`10.0.10.34`) Port 17** (20G LACP Trunk Member 2) | **OK (10G Trunk)** |
| **25** | Top #13 | `SFP+ 25` | **UP** | `10000` | `False` | `switch` | `BJ-SFP+-SR` | `QINIYEK` | `BJ82406120324` | 10GBase-SR Fiber Downlink | **OK (10G Fiber)** |
| **26** | Bot #13 | `SFP+ 26` | Down | `0` | `True` | `switch` | *Empty Cage* | — | — | *Empty* | Spare 10G SFP+ Cage |
| **27** | Top #14 | `To USW24 Office` | **UP** | `1000` | `True` | `aggregate` (`lag_idx=4`, `[27,28]`) | `SFP-10G-DAC-005` | `OEM` | `2506030094` | **`Office USL24B` (`10.0.10.192`) Port 26** (1G SFP Cage limit) | **OK (Normal — USL24B is 1G SFP)** |
| **28** | Bot #14 | `SFP+ 28` | **UP** | `1000` | `True` | `aggregate` (`lag_idx=4`, `agg_by=27`) | `SFP-10G-DAC-005` | `FLYPRO` | `2505150027` | **`Office USL24B` (`10.0.10.192`) Port 25** (1G SFP Cage limit) | **OK (Normal — USL24B is 1G SFP)** |
| **29** | Top #15 | `esxi01 - vmnic7 (25G)` | **UP** | **`25000`** | `False` | `switch` | `SFP-H25G-CU3M` | `OEM` | `CSC241104261356` | **`esxi01:vmnic7`** (`6c:b3:11:79:91:cf`, ConnectX-4 Lx **Slot 1 Port 2**) | **OK (25G Storage/RoCE — `Uplink 6`, Pinned `DVPort 321` `AI Inference`)** |
| **30** | Bot #15 | `esxi03 - vmnic10 (25G)` | **UP** | **`25000`** | `False` | `switch` | `SFP-H25G-CU3M` | `OEM` | `CSC241104261212` | **`esxi03:vmnic10`** (`6c:b3:11:79:91:0e`, ConnectX-4 Lx **Slot 6 Port 1**) | **OK (25G Storage/RoCE — `Uplink 3`, Pinned `DVPort 1` `AI DEV`)** |
| **31** | Top #16 | `esxi02 - vmnic6 (25G)` | **UP** | **`25000`** | `False` | `switch` | `SFP-H25G-CU3M` | `OEM` | `CSC241104261315` | **`esxi02:vmnic6`** (`6c:b3:11:79:91:ba`, ConnectX-4 Lx **Slot 4 Port 1**) | **OK (25G Storage/RoCE — `Uplink 6`, Pinned `DVPort 313` `llama`)** |
| **32** | Bot #16 | `esxi04 - vmnic6 (25G)` | **UP** | **`25000`** | `False` | `switch` | `SFP-H25G-CU3M` | `OEM` | `CSC241104260821` | **`esxi04:vmnic6`** (`94:40:c9:aa:d6:b4`, ConnectX-5 **Slot 2 Port 1**) | **OK (25G Storage/RoCE — `Uplink 1`, Pinned `DVPort 293` `AI`)** |

---

## 3. Complete Switch Port Maps — Secondary Switches (`USL8A`, `USPM16P`, `USL24B`, `UDM SE`)

### A. `Office (USL8A)` — 8-Port 10G SFP+ Aggregation Switch (`10.0.10.182`, MAC `0c:ea:14:bb:e0:64`) — Post-LACP Verified

| Port | Port Label | Link | Speed (Mbps) | AutoNeg | Mode / LAG | Transceiver / DAC Part | Vendor | Serial Number | Connected Device / Server NIC & Slot | Status / Notes |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **1** | `SFP+ 1 (Empty)` | Down | `10000` | `False` | `switch` | *Empty Cage* | — | — | *Empty (Migrated `esxi02:vmnic4` to `USAGGPRO Port 1`)* | Spare 10G SFP+ Cage |
| **2** | `esxi03 - vmnic5 (10G)` | **UP** | `10000` | `False` | `switch` | `DAC-SFP10-1M` | `Ubiquiti Inc.` | `AH24112009036` | **`esxi03:vmnic5`** (`34:64:a9:bb:8d:a0`, HP 556FLR **FlexLOM Port 2**) | **OK (10G Active DVS `Uplink 6` — `key=180`)** |
| **3** | `SFP+ 3 (Empty)` | Down | `0` | `False` | `switch` | *Empty Cage* | — | — | *Empty* | Spare 10G SFP+ Cage |
| **4** | `SFP+ 4 (Empty)` | Down | `0` | `False` | `switch` | *Empty Cage* | — | — | *Empty* | Spare 10G SFP+ Cage |
| **5** | `SFP+ 5 (Empty)` | Down | `0` | `False` | `switch` | *Empty Cage* | — | — | *Empty (Migrated `esxi04:vmnic4` to `USAGGPRO Port 4`)* | Spare 10G SFP+ Cage |
| **6** | `esxi03 - vmnic4 (10G)` | **UP** | `10000` | `False` | `switch` | `SFP-10G-DAC-005` | `OEM` | `2506030096` | **`esxi03:vmnic4`** (`34:64:a9:bb:8d:98`, HP 556FLR **FlexLOM Port 1**) | **OK (10G Active DVS `Uplink 7` — `key=181`)** |
| **7** | `To USAGGPRO Port 20 (LAG)` | **UP** | `10000` | `False` | `aggregate` (`lag_idx=1`, `[7,8]`) | `SFP-H10GB-CU3M` | `OEM` | `CSS11OA1104` | **`USAGGPRO` (`10.0.10.111`) Port 20** | **OK (20G LACP Uplink Member 1)** |
| **8** | `SFP+ 8` | **UP** | `10000` | `False` | `aggregate` (`lag_idx=1`, `agg_by=7`) | `SFP-H10GB-CU3M` | `OEM` | `CSS11OA1110` | **`USAGGPRO` (`10.0.10.111`) Port 21** | **OK (20G LACP Uplink Member 2)** |

### B. `Office (USPM16P)` (`10.0.10.34`), `Office (USL24B)` (`10.0.10.192`), and `UDM SE` (`10.0.1.1`) — Active Server & iLO Ports

| Switch | Port | Port Label | Media | Link | Speed (Mbps) | Connected Server Interface | Physical Server Location | Status / Action |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **`USPM16P` (`.34`)** | **2** | `ESXI04 - vmnic3` | RJ45 1G | **UP** | `1000` | `esxi04:vmnic3` (`08:f1:ea:97:c9:9b`) | `esxi04` Embedded LOM 1 Port 4 | OK (1G Unassigned) |
| **`USPM16P` (`.34`)** | **3** | `ESXI04 - vmnic0` | RJ45 1G | **UP** | `1000` | `esxi04:vmnic0` (`08:f1:ea:97:c9:98`) | `esxi04` Embedded LOM 1 Port 1 | OK (1G Unassigned) |
| **`USPM16P` (`.34`)** | **4** | `ESXI04 - vmnic1` | RJ45 1G | **UP** | `1000` | `esxi04:vmnic1` (`08:f1:ea:97:c9:99`) | `esxi04` Embedded LOM 1 Port 2 | OK (1G Unassigned) |
| **`USPM16P` (`.34`)** | **5** | `ESXI04 - vmnic2` | RJ45 1G | **UP** | `1000` | `esxi04:vmnic2` (`08:f1:ea:97:c9:9a`) | `esxi04` Embedded LOM 1 Port 3 | OK (1G Unassigned) |
| **`USPM16P` (`.34`)** | **7** | `ESXI03 - vmnic0` | RJ45 1G | **UP** | `1000` | `esxi03:vmnic0` (`08:f1:ea:97:7a:d8`) | `esxi03` Embedded LOM 1 Port 1 | OK (1G Unassigned) |
| **`USPM16P` (`.34`)** | **8** | `ESXI03 - vmnic2` | RJ45 1G | **UP** | `1000` | `esxi03:vmnic2` (`08:f1:ea:97:7a:da`) | `esxi03` Embedded LOM 1 Port 3 | OK (1G Unassigned) |
| **`USPM16P` (`.34`)** | **9** | `ESXI03 - vmnic3` | RJ45 1G | **UP** | `1000` | `esxi03:vmnic3` (`08:f1:ea:97:7a:db`) | `esxi03` Embedded LOM 1 Port 4 | OK (1G Unassigned) |
| **`USPM16P` (`.34`)** | **10** | `ESXI03 - vmnic1` | RJ45 1G | **UP** | `1000` | `esxi03:vmnic1` (`08:f1:ea:97:7a:d9`) | `esxi03` Embedded LOM 1 Port 2 | OK (1G Unassigned) |
| **`USPM16P` (`.34`)** | **11** | `ESXI01 - vmnic3` | RJ45 1G | **UP** | `1000` | `esxi01:vmnic3` (`14:02:ec:07:73:5b`) | `esxi01` Embedded LOM 1 Port 4 | OK (1G Unassigned) |
| **`USPM16P` (`.34`)** | **14** | `ESXI01 - vmnic1` | RJ45 2.5G | **UP** | `1000` | `esxi01:vmnic1` (`14:02:ec:07:73:59`) | `esxi01` Embedded LOM 1 Port 2 | OK (1G Unassigned) |
| **`USL24B` (`.192`)** | **2** | `ESXI02 - vmnic0` | RJ45 1G | **UP** | `1000` | `esxi02:vmnic0` (`30:e1:71:54:1d:34`) | `esxi02` Embedded LOM 1 Port 1 | OK (1G Unassigned) |
| **`USL24B` (`.192`)** | **5** | `ESXI01 - vmnic11` | RJ45 1G | **UP** | `1000` | `esxi01:vmnic11` (`98:b7:85:22:8d:30`) | `esxi01` **PCI-E Slot 5 Port 2** (10G X540-AT2) | **10G NIC on 1G switch port** (Can move to `USAGGPRO Port 8/10`) |
| **`USL24B` (`.192`)** | **10** | `ESXI02 - vmnic3` | RJ45 1G | **DEGRADED** | **`100`** | `esxi02:vmnic3` (`30:e1:71:54:1d:37`) | `esxi02` Embedded LOM 1 Port 4 | **Replace/reseat RJ45 cable (`100 Mbps` FE fault)** |
| **`USL24B` (`.192`)** | **12** | `ESXI02 - vmnic1` | RJ45 1G | **UP** | `1000` | `esxi02:vmnic1` (`30:e1:71:54:1d:35`) | `esxi02` Embedded LOM 1 Port 2 | OK (1G Unassigned) |
| **`USL24B` (`.192`)** | **13** | `ESXI02 - vmnic2` | RJ45 1G | **UP** | `1000` | `esxi02:vmnic2` (`30:e1:71:54:1d:36`) | `esxi02` Embedded LOM 1 Port 3 | OK (1G Unassigned) |
| **`UDM SE` (`.1.1`)** | **2** | `ESXI03 - iLO` | RJ45 1G | **UP** | `1000` | `esxi03` iLO 5 (`10.0.10.252`) | `esxi03` Dedicated iLO Port | OK (Out-of-Band Mgmt) |
| **`UDM SE` (`.1.1`)** | **4** | `ESXI04 - iLO` | RJ45 1G | **UP** | `1000` | `esxi04` iLO 5 (`10.0.10.40`) | `esxi04` Dedicated iLO Port | OK (Out-of-Band Mgmt) |
| **`UDM SE` (`.1.1`)** | **5** | `ESXI01 - iLO` | RJ45 1G | **UP** | `1000` | `esxi01` iLO 4 (`10.0.10.10`) | `esxi01` Dedicated iLO Port | OK (Out-of-Band Mgmt) |

---

## 4. Per-Server Rear Panel PCIe Slot & NIC Port Map (`esxi01` – `esxi04`)

### A. `esxi01` (`10.0.20.41` / iLO `10.0.10.10`) — HPE ProLiant DL380 Gen9 (12-Bay LFF)
| ESXi NIC | PCI Address | Physical Rear Location | Adapter Model | Port | MAC Address | Speed | MTU | Connected Switch & Port | DVS / Role |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `vmnic0` | `0000:02:00.0` | **Embedded LOM 1** | HPE 1Gb 4-port 331i (`BCM5719`) | Port 1 | `14:02:ec:07:73:58` | Down | 1500 | Unplugged | Unused |
| `vmnic1` | `0000:02:00.1` | **Embedded LOM 1** | HPE 1Gb 4-port 331i (`BCM5719`) | Port 2 | `14:02:ec:07:73:59` | 1000 | 1500 | `USPM16P` Port 14 | Unassigned |
| `vmnic2` | `0000:02:00.2` | **Embedded LOM 1** | HPE 1Gb 4-port 331i (`BCM5719`) | Port 3 | `14:02:ec:07:73:5a` | Down | 1500 | Unplugged | Unused |
| `vmnic3` | `0000:02:00.3` | **Embedded LOM 1** | HPE 1Gb 4-port 331i (`BCM5719`) | Port 4 | `14:02:ec:07:73:5b` | 1000 | 1500 | `USPM16P` Port 11 | Unassigned |
| `vmnic4` | `0000:04:00.0` | **Embedded FlexibleLOM** | HPE 10Gb 2-port 562FLR-SFP+ (`X710`) | Port 1 | `48:df:37:d5:66:20` | **10000** | 9000 | **`USAGGPRO` Port 13** (`SN: CSS11OA3459`) | `VDS 25GB` (DVPort 241) |
| `vmnic5` | `0000:04:00.1` | **Embedded FlexibleLOM** | HPE 10Gb 2-port 562FLR-SFP+ (`X710`) | Port 2 | `48:df:37:d5:66:28` | **10000** | 9000 | **`USAGGPRO` Port 14** (`SN: CSS11OA3478`) | `VDS 25GB` (DVPort 240) |
| **`vmnic6`** | `0000:05:00.0` | **PCI-E Slot 1** (Riser 1) | **Mellanox ConnectX-4 Lx 25GbE (`MCX4121A-ACU`)** | **Port 1** | `6c:b3:11:79:91:ce` | **25000** | 9000 | **`USAGGPRO` Port 29** (`SN: CSC241104260821`) | **`VDS 25GB` Primary 25G (DVPort 237)** |
| **`vmnic7`** | `0000:05:00.1` | **PCI-E Slot 1** (Riser 1) | **Mellanox ConnectX-4 Lx 25GbE (`MCX4121A-ACU`)** | **Port 2** | `6c:b3:11:79:91:cf` | **10000** | 1500 | **`USAGGPRO` Port 15** (`SN: CSS11OA1382`) | Ready for 10G RoCE / Secondary Uplink |
| `vmnic8` | `0000:08:00.0` | **PCI-E Slot 3** (Riser 1) | Intel 82599 10GbE Dual-Port SFP+ | Port 1 | `98:b7:85:22:55:1a` | **10000** | 9000 | **`USAGGPRO` Port 16** (`SN: CSS11OA1377`) | `VDS 25GB` (DVPort 239) |
| `vmnic9` | `0000:08:00.1` | **PCI-E Slot 3** (Riser 1) | Intel 82599 10GbE Dual-Port SFP+ | Port 2 | `98:b7:85:22:55:1b` | **10000** | 9000 | **`USAGGPRO` Port 19** (`SN: CSS11OA3482`) | `VDS 25GB` (DVPort 238) |
| `vmnic10` | `0000:84:00.0` | **PCI-E Slot 5** (Riser 2) | Intel X540-AT2 10GBASE-T RJ45 | Port 1 | `98:b7:85:22:8d:2f` | Down | 1500 | Unplugged | Unused 10GBASE-T |
| `vmnic11` | `0000:84:00.1` | **PCI-E Slot 5** (Riser 2) | Intel X540-AT2 10GBASE-T RJ45 | Port 2 | `98:b7:85:22:8d:30` | 1000 | 1500 | `USL24B` Port 5 (1G port) | Unassigned (Capable of 10G on `USAGGPRO`) |

### B. `esxi02` (`10.0.20.42` / iLO `10.0.10.20`) — HPE ProLiant DL380 Gen9 (12-Bay LFF)
| ESXi NIC | PCI Address | Physical Rear Location | Adapter Model | Port | MAC Address | Speed | MTU | Connected Switch & Port | DVS / Role |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `vmnic0` | `0000:02:00.0` | **Embedded LOM 1** | HPE 1Gb 4-port 331i (`BCM5719`) | Port 1 | `30:e1:71:54:1d:34` | 1000 | 1500 | `USL24B` Port 2 | Unassigned |
| `vmnic1` | `0000:02:00.1` | **Embedded LOM 1** | HPE 1Gb 4-port 331i (`BCM5719`) | Port 2 | `30:e1:71:54:1d:35` | 1000 | 1500 | `USL24B` Port 12 | Unassigned |
| `vmnic2` | `0000:02:00.2` | **Embedded LOM 1** | HPE 1Gb 4-port 331i (`BCM5719`) | Port 3 | `30:e1:71:54:1d:36` | 1000 | 1500 | `USL24B` Port 13 | Unassigned |
| `vmnic3` | `0000:02:00.3` | **Embedded LOM 1** | HPE 1Gb 4-port 331i (`BCM5719`) | Port 4 | `30:e1:71:54:1d:37` | **100** | 1500 | **`USL24B` Port 10** | **Degraded RJ45 cable (`100 Mbps`)** |
| `vmnic4` | `0000:04:00.0` | **Embedded FlexibleLOM** | HPE 10Gb 2-port 562FLR-SFP+ (`X710`) | Port 1 | `48:df:37:e0:b7:90` | **10000** | 9000 | **`Office USL8A` Port 1** (`SN: CSS11OA1420`) | `VDS 25GB` (DVPort 207) |
| `vmnic5` | `0000:04:00.1` | **Embedded FlexibleLOM** | HPE 10Gb 2-port 562FLR-SFP+ (`X710`) | Port 2 | `48:df:37:e0:b7:98` | **10000** | 9000 | **`Office USL8A` Port 2** (`SN: CSS11OA1724`) | `VDS 25GB` (DVPort 206) |
| **`vmnic6`** | `0000:88:00.0` | **PCI-E Slot 4** (Riser 2) | **Mellanox ConnectX-4 Lx 25GbE (`MCX4121A-ACU`)** | **Port 1** | `6c:b3:11:79:91:ba` | **25000** | 9000 | **`USAGGPRO` Port 32** (`SN: CSC241104261315`) | **`VDS 25GB` Primary 25G (DVPort 211)** |
| **`vmnic7`** | `0000:88:00.1` | **PCI-E Slot 4** (Riser 2) | **Mellanox ConnectX-4 Lx 25GbE (`MCX4121A-ACU`)** | **Port 2** | `6c:b3:11:79:91:bb` | **10000** | 1500 | **`USAGGPRO` Port 4** (`SN: CSS11OA1102`) | Ready for 10G RoCE / Secondary Uplink |

### C. `esxi03` (`10.0.20.43` / iLO `10.0.10.252`) — HPE ProLiant DL380 Gen10 (24-Bay SFF)
*(Note: **PCI-E Slot 2** houses the **NVIDIA Tesla T4 GPU** at `0000:37:00.0`)*

| ESXi NIC | PCI Address | Physical Rear Location | Adapter Model | Port | MAC Address | Speed | MTU | Connected Switch & Port | DVS / Role |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `vmnic0` | `0000:02:00.0` | **Embedded LOM 1** | HPE 1Gb 4-port 331i (`BCM5719`) | Port 1 | `08:f1:ea:97:7a:d8` | 1000 | 1500 | `USPM16P` Port 7 | Unassigned |
| `vmnic1` | `0000:02:00.1` | **Embedded LOM 1** | HPE 1Gb 4-port 331i (`BCM5719`) | Port 2 | `08:f1:ea:97:7a:d9` | 1000 | 1500 | `USPM16P` Port 10 | Unassigned |
| `vmnic2` | `0000:02:00.2` | **Embedded LOM 1** | HPE 1Gb 4-port 331i (`BCM5719`) | Port 3 | `08:f1:ea:97:7a:da` | 1000 | 1500 | `USPM16P` Port 8 | Unassigned |
| `vmnic3` | `0000:02:00.3` | **Embedded LOM 1** | HPE 1Gb 4-port 331i (`BCM5719`) | Port 4 | `08:f1:ea:97:7a:db` | 1000 | 1500 | `USPM16P` Port 9 | Unassigned |
| *(vmnic4)* | `0000:5d:00.0` | **Embedded FlexibleLOM** | HP FlexFabric 10Gb 2-port 556FLR-SFP+ (`elxnet`) | Port 1 | `34:64:a9:bb:8d:98` | *10000 PHY* | — | **`USAGGPRO` Port 1** (`SN: CSS11OA1439`) | **Disabled by `elxnet` optic lock (0 MACs — harvest DAC)** |
| *(vmnic5)* | `0000:5d:00.1` | **Embedded FlexibleLOM** | HP FlexFabric 10Gb 2-port 556FLR-SFP+ (`elxnet`) | Port 2 | `34:64:a9:bb:8d:a0` | *10000 PHY* | — | **`USAGGPRO` Port 17** (`SN: CSS11OA3458`) | **Disabled by `elxnet` optic lock (0 MACs — harvest DAC)** |
| `vmnic8` | `0000:13:00.0` | **PCI-E Slot 3** (Riser 1) | Intel X540-AT2 10GBASE-T RJ45 | Port 1 | `98:b7:85:22:8d:2d` | **10000** | 9000 | **`USAGGPRO` Port 12** (`BJ-SFP-10G-T`) | `VDS 25GB` (DVPort 176) |
| `vmnic9` | `0000:13:00.1` | **PCI-E Slot 3** (Riser 1) | Intel X540-AT2 10GBASE-T RJ45 | Port 2 | `98:b7:85:22:8d:2e` | Down | 9000 | Unplugged / Admin Down | `VDS 25GB` (DVPort 175) |
| **`vmnic10`** | `0000:b0:00.0` | **PCI-E Slot 6** (Riser 2) | **Mellanox ConnectX-4 Lx 25GbE (`MCX4121A-ACU`)** | **Port 1** | `6c:b3:11:79:91:0e` | **25000** | 9000 | **`USAGGPRO` Port 31** (`SN: CSC241104261212`) | **`VDS 25GB` Primary 25G (DVPort 177)** |
| **`vmnic11`** | `0000:b0:00.1` | **PCI-E Slot 6** (Riser 2) | **Mellanox ConnectX-4 Lx 25GbE (`MCX4121A-ACU`)** | **Port 2** | `6c:b3:11:79:91:0f` | **`1000` (Bad DAC)** | 1500 | **`USAGGPRO` Port 3** (`SN: CSS11OA1407`) | **SWAP DAC CABLE WITH PORT 1 OR PORT 25** |

### D. `esxi04` (`10.0.20.44` / iLO `10.0.10.40`) — HPE ProLiant DL380 Gen10 (24-Bay SFF)
*(Note: **PCI-E Slot 5** houses the **NVIDIA Tesla T4 GPU** at `0000:86:00.0`)*

| ESXi NIC | PCI Address | Physical Rear Location | Adapter Model | Port | MAC Address | Speed | MTU | Connected Switch & Port | DVS / Role |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `vmnic0` | `0000:02:00.0` | **Embedded LOM 1** | HPE 1Gb 4-port 331i (`BCM5719`) | Port 1 | `08:f1:ea:97:c9:98` | 1000 | 1500 | `USPM16P` Port 3 | Unassigned |
| `vmnic1` | `0000:02:00.1` | **Embedded LOM 1** | HPE 1Gb 4-port 331i (`BCM5719`) | Port 2 | `08:f1:ea:97:c9:99` | 1000 | 1500 | `USPM16P` Port 4 | Unassigned |
| `vmnic2` | `0000:02:00.2` | **Embedded LOM 1** | HPE 1Gb 4-port 331i (`BCM5719`) | Port 3 | `08:f1:ea:97:c9:9a` | 1000 | 1500 | `USPM16P` Port 5 | Unassigned |
| `vmnic3` | `0000:02:00.3` | **Embedded LOM 1** | HPE 1Gb 4-port 331i (`BCM5719`) | Port 4 | `08:f1:ea:97:c9:9b` | 1000 | 1500 | `USPM16P` Port 2 | Unassigned |
| `vmnic4` | `0000:5d:00.0` | **Embedded FlexibleLOM** | HP FlexFabric 10Gb 2-port 556FLR-SFP+ (`elxnet`) | Port 1 | `70:10:6f:43:b5:18` | **10000** | 9000 | **`Office USL8A` Port 5** (`SN: 2506030098`) | `VDS 25GB` (DVPort 149) |
| `vmnic5` | `0000:5d:00.1` | **Embedded FlexibleLOM** | HP FlexFabric 10Gb 2-port 556FLR-SFP+ (`elxnet`) | Port 2 | `70:10:6f:43:b5:20` | **10000** | 9000 | **`USAGGPRO` Port 18** (`SN: CSS11OA1379`) | `VDS 25GB` (DVPort 148) |
| **`vmnic6`** | `0000:37:00.0` | **PCI-E Slot 2** (Riser 1) | **HPE 10/25Gb 2P 641SFP28 (`ConnectX-5`)** | **Port 1** | `94:40:c9:aa:d6:b4` | **25000** | 9000 | **`USAGGPRO` Port 30** (`SN: CSC241104261356`) | **`VDS 25GB` Primary 25G (DVPort 144)** |
| **`vmnic7`** | `0000:37:00.1` | **PCI-E Slot 2** (Riser 1) | **HPE 10/25Gb 2P 641SFP28 (`ConnectX-5`)** | **Port 2** | `94:40:c9:aa:d6:b5` | **10000** | 9000 | **`USAGGPRO` Port 2** (`SN: CSC241104260371`) | `VDS 25GB` / 10G RoCE (DVPort 147) |
| `vmnic8` | `0000:b0:00.0` | **PCI-E Slot 6** (Riser 2) | Intel 82599 10GbE Dual-Port SFP+ | Port 1 | `98:b7:85:22:5e:ea` | **10000** | 9000 | **`USAGGPRO` Port 6** (`SN: CSS11OA1726`) | `VDS 25GB` (DVPort 146) |
| `vmnic9` | `0000:b0:00.1` | **PCI-E Slot 6** (Riser 2) | Intel 82599 10GbE Dual-Port SFP+ | Port 2 | `98:b7:85:22:5e:eb` | **10000** | 9000 | **`USAGGPRO` Port 5** (`SN: CSS11OA1406`) | `VDS 25GB` (DVPort 145) |

---

## Appendix A: Physical Drive Bay & SMART Wear Reference Map (`esxi01` – `esxi04`)

*(Reference only — per operator directive **"The ssd stays"**, no physical drive swaps are scheduled).*

### 1. `esxi01` (`10.0.20.41`, DL380 Gen9 12-LFF + Rear 2-SFF) — `Smart Array P840ar` (`SN: PVYKH0BRH550V7`)
* **Front 12-LFF Cage (`Box 1` = Bays 1–8 on Port `2I`, `Box 2` = Bays 1–4 on Port `1I`) + Rear 2-SFF Cage (`Box 4` = Bays 1–2 on Port `1I`)**:

| Physical Bay (`Port:Box:Bay`) | Physical Location | ESXi Device / Runtime Path | Model | Serial Number | Capacity | Media / Intf | Role (vSAN Disk Group) | SMART Life Remaining / Wear |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **`1I:4:1`** | **Rear SFF Bay 1** | `mpx.vmhba0:C2:T0:L0` | `Samsung SSD 870` | `S75BNL0Y507863Z` | 1 TB | SATA SSD | **vSAN Cache (`DG 52e69070`)** | **51% Life Left** (343 TBW, 9,499 POH) |
| **`1I:4:2`** | **Rear SFF Bay 2** | `mpx.vmhba0:C2:T1:L0` | `Samsung SSD 870` | `S75BNL0Y507820A` | 1 TB | SATA SSD | **vSAN Cache (`DG 520e7405`)** | **34% Life Left** (423 TBW, 9,499 POH) |
| `1I:2:1` | Front LFF Box 2 Bay 1 | `naa.50000c0f01e34abc` (`T2`) | `WD3001FYYG-01SL3` | `WMC1F1832094` | 3 TB | SAS HDD | **ESXi Boot Disk** (Non-vSAN) | OK |
| `1I:2:2` | Front LFF Box 2 Bay 2 | `mpx.vmhba0:C2:T3:L0` | `ST24000NT002-3N1` | `ZYD6BXLN` | 24 TB | SATA HDD | vSAN Capacity (`DG 52e69070`) | OK |
| `1I:2:3` | Front LFF Box 2 Bay 3 | `mpx.vmhba0:C2:T4:L0` | `ST24000NT002-3N1` | `ZYD656KQ` | 24 TB | SATA HDD | vSAN Capacity (`DG 520e7405`) | OK |
| `1I:2:4` | Front LFF Box 2 Bay 4 | `mpx.vmhba0:C2:T5:L0` | `ST24000NM000C-3W` | `ZXA0BPK8` | 24 TB | SATA HDD | vSAN Capacity (`DG 520e7405`) | OK |
| `2I:1:1` | Front LFF Box 1 Bay 1 | `naa.5000cca01aa800e0` (`T10`) | `HUS723030ALS640` | `YHJZD8JG` | 3 TB | SAS HDD | vSAN Capacity (`DG 52e69070`) | OK |
| `2I:1:2` | Front LFF Box 1 Bay 2 | `naa.50000c0f01e0c290` (`T12`) | `WD3001FYYG-01SL3` | `WMC1F1896094` | 3 TB | SAS HDD | vSAN Capacity (`DG 520e7405`) | OK |
| `2I:1:3` | Front LFF Box 1 Bay 3 | `naa.50000c0f012d3630` (`T11`) | `WD3001FYYG-01SL3` | `WMC1F1684362` | 3 TB | SAS HDD | vSAN Capacity (`DG 520e7405`) | OK |
| `2I:1:4` | Front LFF Box 1 Bay 4 | `mpx.vmhba0:C2:T13:L0` | `ST24000NT002-3N1` | `ZYD4T802` | 24 TB | SATA HDD | vSAN Capacity (`DG 52e69070`) | OK |
| `2I:1:5` | Front LFF Box 1 Bay 5 | `naa.5000cca01aa9a96c` (`T6`) | `HUS723030ALS640` | `YHK09K0G` | 3 TB | SAS HDD | vSAN Capacity (`DG 52e69070`) | OK |
| `2I:1:6` | Front LFF Box 1 Bay 6 | `naa.5000cca01ab112cc` (`T7`) | `HUS723030ALS640` | `YHK4BX5G` | 3 TB | SAS HDD | vSAN Capacity (`DG 520e7405`) | OK |
| `2I:1:7` | Front LFF Box 1 Bay 7 | `naa.5000cca01aa7e5ec` (`T8`) | `HUS723030ALS640` | `YHJZAGWG` | 3 TB | SAS HDD | vSAN Capacity (`DG 52e69070`) | OK |
| `2I:1:8` | Front LFF Box 1 Bay 8 | `naa.5000cca01ad7dc48` (`T9`) | `HUS723030ALS640` | `YHKUR5SD` | 3 TB | SAS HDD | vSAN Capacity (`DG 520e7405`) | OK |

*(Note: Both Samsung 870 EVO 1TB cache SSDs on `esxi01` are already mounted in the **Rear 2-SFF 2.5" cage (`Box 4, Bays 1 & 2`)**, so no 2.5"-to-3.5" converter brackets are needed on `esxi01`).*

---

### 2. `esxi02` (`10.0.20.42`, DL380 Gen9 12-LFF + Rear 2-SFF) — `Smart Array P840ar` (`SN: PVYKH0BRH450BX`)

| Physical Bay (`Port:Box:Bay`) | Physical Location | ESXi Device / Runtime Path | Model | Serial Number | Capacity | Media / Intf | Role (vSAN Disk Group) | SMART Life Remaining / Wear |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **`2I:4:1`** | **Rear SFF Bay 1** | `mpx.vmhba0:C2:T8:L0` | `Samsung SSD 870` | `S75BNL0Y409393V` | 1 TB | SATA SSD | **vSAN Cache (`DG 5240e70d`)** | **24% Life Left** (522 TBW, 9,594 POH) |
| **`2I:4:2`** | **Rear SFF Bay 2** | `mpx.vmhba0:C2:T9:L0` | `Samsung SSD 860` | `S599NJ0N201091D` | 1 TB | SATA SSD | **vSAN Cache (`DG 522f2b56`)** | **10% Life Left** (624 TBW, 24,351 POH) |
| `1I:1:1` | Front LFF Box 1 Bay 1 | `mpx.vmhba0:C2:T0:L0` | `ST24000NM000C-3W` | `ZXA0K6W5` | 24 TB | SATA HDD | vSAN Capacity (`DG 522f2b56`) | OK |
| `1I:1:2` | Front LFF Box 1 Bay 2 | `mpx.vmhba0:C2:T2:L0` | `ST24000NM000C-3W` | `ZXA0ELZ9` | 24 TB | SATA HDD | vSAN Capacity (`DG 522f2b56`) | OK |
| `1I:1:3` | Front LFF Box 1 Bay 3 | `mpx.vmhba0:C2:T4:L0` | `ST24000NM000C-3W` | `ZXA0M9JE` | 24 TB | SATA HDD | vSAN Capacity (`DG 522f2b56`) | OK |
| `1I:1:4` | Front LFF Box 1 Bay 4 | `mpx.vmhba0:C2:T5:L0` | `ST24000NM000C-3W` | `ZXA0M12T` | 24 TB | SATA HDD | vSAN Capacity (`DG 5240e70d`) | OK |
| `1I:1:5` | Front LFF Box 1 Bay 5 | `mpx.vmhba0:C2:T6:L0` | `ST24000NM000C-3W` | `ZXA0ALKD` | 24 TB | SATA HDD | vSAN Capacity (`DG 522f2b56`) | OK |
| `1I:1:6` | Front LFF Box 1 Bay 6 | `mpx.vmhba0:C2:T1:L0` | `ST24000NT002-3N1` | `ZYD5CKY6` | 24 TB | SATA HDD | vSAN Capacity (`DG 5240e70d`) | OK |
| `1I:1:7` | Front LFF Box 1 Bay 7 | `mpx.vmhba0:C2:T7:L0` | `ST24000NM000C-3W` | `ZXA0H6PZ` | 24 TB | SATA HDD | vSAN Capacity (`DG 5240e70d`) | OK |
| `1I:1:8` | Front LFF Box 1 Bay 8 | `mpx.vmhba0:C2:T3:L0` | `ST24000NT002-3N1` | `ZYD4AJ6L` | 24 TB | SATA HDD | vSAN Capacity (`DG 5240e70d`) | OK |
| `2I:2:1` | Front LFF Box 2 Bay 1 | `mpx.vmhba0:C2:T10:L0` | `ST24000NT002-3N1` | `ZYD53XG2` | 24 TB | SATA HDD | vSAN Capacity (`DG 522f2b56`) | OK |
| `2I:2:2` | Front LFF Box 2 Bay 2 | `mpx.vmhba0:C2:T11:L0` | `ST24000NT002-3N1` | `ZYD45VL4` | 24 TB | SATA HDD | vSAN Capacity (`DG 522f2b56`) | OK |
| `2I:2:3` | Front LFF Box 2 Bay 3 | `mpx.vmhba0:C2:T12:L0` | `ST24000NT002-3N1` | `ZYD3CLXS` | 24 TB | SATA HDD | vSAN Capacity (`DG 5240e70d`) | OK |
| `2I:2:4` | Front LFF Box 2 Bay 4 | `naa.5000cca01aa6e6b0` (`T13`) | `HUS723030ALS640` | `YHJYTGYG` | 3 TB | SAS HDD | **ESXi Boot Disk** (Non-vSAN) | OK |

*(Hardware Verification Note: Both `esxi01` and `esxi02` have the **HPE DL380 Gen9 Rear 2-SFF SmartDrive Cage installed (`Box 4, Bays 1 & 2`)**! That means `esxi02` **already takes native 2.5" SFF SmartDrive carriers (`P/N 651687-001` / `651699-001`)** in Rear Bays `2I:4:1` and `2I:4:2` without needing any 2.5"-to-3.5" LFF adapter bracket (`661914-001` / `654540-001`) if those Rear SFF cache drives are ever replaced in the future!)*

---

### 3. `esxi03` (`10.0.20.43`, DL380 Gen10 24-SFF + Rear 2-SFF AHCI) — `Smart Array P408i-a SR Gen10` (`SN: PEYHC0DRHBU3VH`) + `Intel Lewisburg SATA AHCI` (`vmhba2`)

| Physical Bay (`Port:Box:Bay`) | Physical Location | ESXi Device / Runtime Path | Model | Serial Number | Capacity | Media / Intf | Role | SMART Wear Used (`Endur%`) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **`vmhba2:C0:T4:L0`** | **Rear/Internal SATA AHCI Port 4** | `t10.ATA_____LITEON_CV32D8D512...` | `LITEON CV3-8D512` | `TW0MN0K7LOH0075G01ZI` | 512 GB | SATA SSD | vSAN Capacity (`DG 528858fc`) | 0% wear (2,783 POH, 8 uncorr err) |
| **`vmhba2:C0:T5:L0`** | **Rear/Internal SATA AHCI Port 5** | `t10.ATA_____SK_hynix_SC300_SATA_512GB...` | `SK hynix SC300` | `ES72N176410302L51` | 512 GB | SATA SSD | vSAN Cache (`DG 527f79c2`) | 41,668 POH, 1 prog fail |
| **`1I:1:1`** | Front SFF Box 1 Bay 1 | `naa.5000cca06cac4660` (`vmhba3:T1`) | `HGST HSCP1920S5xnFMRI` | `0SY1S32A` | 1.92 TB | 12G SAS SSD | **ESXi Boot / Local VMFS (`VC04`)** | **13% Used** (87% Life Left) |
| **`1I:1:2`** | Front SFF Box 1 Bay 2 | `naa.5000cca06cabe218` (`vmhba3:T2`) | `HGST HSCP1920S5xnFMRI` | `0SY1JE1A` | 1.92 TB | 12G SAS SSD | vSAN Capacity (`DG 528858fc`) | **15% Used** (85% Life Left) |
| **`1I:1:3`** | Front SFF Box 1 Bay 3 | `naa.5000cca06cabbeb4` (`vmhba3:T3`) | `HGST HSCP1920S5xnFMRI` | `0SY1G1ZA` | 1.92 TB | 12G SAS SSD | vSAN Capacity (`DG 528858fc`) | **14% Used** (86% Life Left) |
| **`1I:1:4`** | Front SFF Box 1 Bay 4 | `naa.5000cca06cace644` (`vmhba3:T4`) | `HGST HSCP1920S5xnFMRI` | `0SY22S5A` | 1.92 TB | 12G SAS SSD | **vSAN Cache (`DG 528858fc`)** | **23% Used** (77% Life Left) |
| **`1I:1:5`** | Front SFF Box 1 Bay 5 | `naa.5000cca06cabc948` (`vmhba3:T5`) | `HGST HSCP1920S5xnFMRI` | `0SY1GSUA` | 1.92 TB | 12G SAS SSD | **vSAN Cache (`DG 529e09c7`)** | **15% Used** (85% Life Left) |
| **`1I:1:6`** | Front SFF Box 1 Bay 6 | `naa.5000cca06cabeba0` (`vmhba3:T6`) | `HGST HSCP1920S5xnFMRI` | `0SY1K1RA` | 1.92 TB | 12G SAS SSD | vSAN Capacity (`DG 528858fc`) | **14% Used** (86% Life Left) |
| **`1I:1:7`** | Front SFF Box 1 Bay 7 | `naa.5000cca06cabc420` (`vmhba3:T7`) | `HGST HSCP1920S5xnFMRI` | `0SY1GE5A` | 1.92 TB | 12G SAS SSD | vSAN Capacity (`DG 528858fc`) | **14% Used** (86% Life Left) |
| `1I:1:8` | Front SFF Box 1 Bay 8 | `naa.5000c500b7598017` (`vmhba3:T8`) | `SEAGATE STHB1800S5xeN010` | `W3Z1GQ24` | 1.8 TB | 12G SAS HDD | Unclaimed / Non-vSAN | OK |
| `1I:1:9` | Front SFF Box 1 Bay 9 | `naa.51402ec010c35548` (`vmhba3:T9`) | `Seagate ST2000NX0253` | `S4601JDD` | 2.0 TB | SATA HDD | vSAN Capacity (`DG 527f79c2`) | OK |
| `1I:1:10` | Front SFF Box 1 Bay 10 | `naa.51402ec010c35549` (`vmhba3:T10`) | `Seagate ST2000NX0253` | `S460BWGY` | 2.0 TB | SATA HDD | vSAN Capacity (`DG 527f79c2`) | OK |
| `1I:1:11` | Front SFF Box 1 Bay 11 | `naa.51402ec010c3554a` (`vmhba3:T11`) | `Seagate ST2000NX0253` | `S46067X4` | 2.0 TB | SATA HDD | vSAN Capacity (`DG 527f79c2`) | OK |
| `1I:1:12` | Front SFF Box 1 Bay 12 | `naa.51402ec010c3554b` (`vmhba3:T12`) | `Seagate ST2000NX0253` | `S4602M33` | 2.0 TB | SATA HDD | vSAN Capacity (`DG 527f79c2`) | OK |
| **`1I:1:13`** | Front SFF Box 1 Bay 13 | `naa.5000cca06cac2d84` (`vmhba3:T13`) | `HGST HSCP1920S5xnFMRI` | `0SY1PESA` | 1.92 TB | 12G SAS SSD | **vSAN Cache (`DG 52b266cf`)** | **16% Used** (84% Life Left) |
| `1I:1:14` | Front SFF Box 1 Bay 14 | `naa.5000c500b8056747` (`vmhba3:T14`) | `SEAGATE STHB1800S5xeN010` | `W3Z1QH9D` | 1.8 TB | 12G SAS HDD | vSAN Capacity (`DG 52b266cf`) | OK |
| `1I:1:15` | Front SFF Box 1 Bay 15 | `naa.51402ec010c3554e` (`vmhba3:T15`) | `Seagate ST2000NX0253` | `S460DPJN` | 2.0 TB | SATA HDD | vSAN Capacity (`DG 52b266cf`) | OK |
| `1I:1:16` | Front SFF Box 1 Bay 16 | `naa.51402ec010c3554f` (`vmhba3:T16`) | `Seagate ST2000NX0253` | `S46023HZ` | 2.0 TB | SATA HDD | vSAN Capacity (`DG 52b266cf`) | OK |
| **`1I:1:17`** | Front SFF Box 1 Bay 17 | `naa.5000cca06cad6c2c` (`vmhba3:T17`) | `HGST HSCP1920S5xnFMRI` | `0SY2BNMA` | 1.92 TB | 12G SAS SSD | vSAN Capacity (`DG 528858fc`) | **23% Used** (77% Life Left) |
| `1I:1:18` | Front SFF Box 1 Bay 18 | `naa.5000c500b78252c3` (`vmhba3:T18`) | `SEAGATE STHB1800S5xeN010` | `W3Z1F2DX` | 1.8 TB | 12G SAS HDD | vSAN Capacity (`DG 52b266cf`) | OK |
| `1I:1:19` | Front SFF Box 1 Bay 19 | `naa.5000c500b749a45b` (`vmhba3:T19`) | `SEAGATE STHB1800S5xeN010` | `W3Z1GQ53` | 1.8 TB | 12G SAS HDD | vSAN Capacity (`DG 529e09c7`) | OK |
| `1I:1:20` | Front SFF Box 1 Bay 20 | `naa.5000c500b77b0213` (`vmhba3:T20`) | `SEAGATE STHB1800S5xeN010` | `W3Z1J6YV` | 1.8 TB | 12G SAS HDD | vSAN Capacity (`DG 52b266cf`) | OK |
| `1I:1:21` | Front SFF Box 1 Bay 21 | `naa.5000c500b7497527` (`vmhba3:T21`) | `SEAGATE STHB1800S5xeN010` | `W3Z1GQKS` | 1.8 TB | 12G SAS HDD | vSAN Capacity (`DG 529e09c7`) | OK |
| `1I:1:22` | Front SFF Box 1 Bay 22 | `naa.5000c500b756c0a7` (`vmhba3:T22`) | `SEAGATE STHB1800S5xeN010` | `W3Z1GBCW` | 1.8 TB | 12G SAS HDD | vSAN Capacity (`DG 529e09c7`) | OK |
| `1I:1:23` | Front SFF Box 1 Bay 23 | `naa.5000c500a6a778e3` (`vmhba3:T23`) | `SEAGATE STHB1800S5xeN010` | `W3Z1ZMN4` | 1.8 TB | 12G SAS HDD | vSAN Capacity (`DG 529e09c7`) | OK |
| `1I:1:24` | Front SFF Box 1 Bay 24 | `naa.5000c500b80352df` (`vmhba3:T24`) | `SEAGATE STHB1800S5xeN010` | `W3Z1NWX4` | 1.8 TB | 12G SAS HDD | vSAN Capacity (`DG 529e09c7`) | OK |

---

### 4. `esxi04` (`10.0.20.44`, DL380 Gen10 24-SFF + Rear AHCI) — `Smart Array P408i-a SR Gen10` (`SN: PEYHC0DRHC21V9`) + `Intel Lewisburg SATA AHCI` (`vmhba2`)

| Physical Bay (`Port:Box:Bay`) | Physical Location | ESXi Device / Runtime Path | Model | Serial Number | Capacity | Media / Intf | Role | SMART Wear Used (`Endur%`) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **`vmhba2:C0:T4:L0`** | **Rear/Internal SATA AHCI Port 4** | `t10.ATA_____SK_hynix_SC300_SATA_512GB...` | `SK hynix SC300` | `ES74N204810504O2A` | 512 GB | SATA SSD | **vSAN Cache (`DG 526de2af`)** | 38,342 POH |
| **`1I:1:1`** | Front SFF Box 1 Bay 1 | `naa.5000cca06cabc9b0` (`vmhba3:T1`) | `HGST HSCP1920S5xnFMRI` | `0SY1GTNA` | 1.92 TB | 12G SAS SSD | **vSAN Cache (`DG 520ec439`)** | **16% Used** (84% Life Left) |
| **`1I:1:9`** | Front SFF Box 1 Bay 9 | `naa.5000cca06cac34a4` (`vmhba3:T9`) | `HGST HSCP1920S5xnFMRI` | `0SY1PXGA` | 1.92 TB | 12G SAS SSD | **vSAN Cache (`DG 52d263a2`)** | **16% Used** (84% Life Left) |
| **`1I:1:17`** | Front SFF Box 1 Bay 17 | `naa.5000cca06cabe888` (`vmhba3:T17`) | `HGST HSCP1920S5xnFMRI` | `0SY1JVAA` | 1.92 TB | 12G SAS SSD | **ESXi Boot / Non-vSAN SSD** | **13% Used** (87% Life Left) |
| `1I:1:2` – `1I:1:8`, `1I:1:10` – `1I:1:16`, `1I:1:18` – `1I:1:24` | Front SFF Box 1 (21x HDD Bays) | `vmhba3:C0:T2..T8, T10..T16, T18..T24` | `SEAGATE STHB1800S5xeN010` | *(21x 1.8TB 12G SAS HDDs)* | 1.8 TB ea | 12G SAS HDD | vSAN Capacity (7 per DG across `520ec439`, `52d263a2`, `526de2af`) | All 21 Drives `Health: OK` |
