# AGENT-1: UniFi 25G/10G Switching Fabric & VDS `25GB` Remediation Receipts

- **Timestamp (UTC)**: `2026-10-17T17:10:35Z`
- **Agent**: `Agent-1 (UniFi & VDS 25G/10G Fabric Engineer)`
- **Anti-Decapitation Invariant**: Enforced (zero host, vCenter, or switch reboots; two-layer dead-man timers armed before all NIC/VDS changes; all 13 watchdog targets healthy throughout).
- **Pre-Change UniFi Backup**: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/unifi_switches_pre.json` (125 KB, `exit=0`)
- **Post-Change UniFi Backup**: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/unifi_switches_post.json` (`exit=0`)
- **Full Post-Change Raw Verification Log**: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/post_verify_full.txt` (`exit=0`)

---

## 1. UniFi `Office (USAGGPRO)` (`10.0.10.111`, `_id: c4b8bfa2-f496-46a8-bb31-cda8baf247fb`) Inspection & Port 3 (`ESXI03 - vmnic11`) 10G FDX Fix

### Root Cause of Port 3 (`ESXI03 - vmnic11`) 1000 Mbps Fallback
1. **Pre-Change `port_overrides`**:
   - Ports `1, 2, 3, 4, 15, 29, 30, 31, 32` all referenced `portconf_id: "a5ae6ce7-c2e2-4113-98d7-47a5c9744922"` (`VMWare services`), which specifies `"autoneg": true, "speed": null, "full_duplex": null`.
   - Between the 25G SFP28 Mellanox ConnectX-4 Lx Port 2 (`esxi03:vmnic11`, advertising `Auto, 1000BaseCX-SGMII/Full, 10000BaseKR/Full, 25000BaseTwinax/Full`) and the 10G SFP+ switch port (`USAGGPRO` Port 3) over the `SFP-H10GB-CU3M` passive copper DAC, Clause 73 / SGMII autonegotiation fell back to `1000BaseCX-SGMII/Full` (`speed=1000, autoneg=True`).
   - When only the ESXi NIC side was forced (`esxcli network nic set -n vmnic11 -S 10000 -D full`) while `USAGGPRO` Port 3 remained on `autoneg=True`, link training dropped (`Link Status: Down` / `up: false`).
2. **Fix Applied**:
   - Armed 45-second on-host local dead-man timer on `esxi03` (`PID 10676380`) and detached `vmnic11` from VDS `25GB` (`DVPort 182`) during link testing so zero VM/vmk traffic could be affected.
   - Updated `USAGGPRO` (`c4b8bfa2-f496-46a8-bb31-cda8baf247fb`) `port_overrides` for `port_idx: 3` via `PUT /api/s/default/rest/device/c4b8bfa2-f496-46a8-bb31-cda8baf247fb` (`HTTP 200`) to explicitly lock `autoneg: false, speed: 10000, full_duplex: true` (preserving the exact VLAN/switch settings of `VMWare services`), and set `esxi03:vmnic11` to `esxcli network nic set -n vmnic11 -a` (matching `esxi01:vmnic7` and `esxi02:vmnic7`).
   - Both sides immediately locked at **`10000 Mbps Full Duplex` (`up: true`)**.
   - Re-attached `esxi03:vmnic11` to VDS `25GB` (`DVPort 182`, with 45-second on-host dead-man timer `PID 10678439`, `esxcfg-vswitch -P vmnic11 -V 182 25GB`, `rc=0`), restoring `MTU: 9000` at `10000 Mbps Full Duplex`.

### Raw Before vs. After — UniFi `stat/device` (`USAGGPRO` Ports 1–4, 15, 29–32)

#### BEFORE (`unifi_inspect_pre.txt`):
```json
Port 1:  {"autoneg": false, "enable": true, "flowctrl_rx": true, "flowctrl_tx": true, "full_duplex": true, "jumbo": true, "media": "SFP+",  "name": "ESXI03 - vmnic6",  "op_mode": "switch", "port_idx": 1,  "portconf_id": "a5ae6ce7-c2e2-4113-98d7-47a5c9744922", "sfp_compliance": "DAC",       "sfp_found": true, "sfp_part": "SFP-H10GB-CU3M", "sfp_vendor": "OEM", "speed": 10000, "up": true}
Port 2:  {"autoneg": false, "enable": true, "flowctrl_rx": true, "flowctrl_tx": true, "full_duplex": true, "jumbo": true, "media": "SFP+",  "name": "ESXI04 - vmnic7",  "op_mode": "switch", "port_idx": 2,  "portconf_id": "a5ae6ce7-c2e2-4113-98d7-47a5c9744922", "sfp_compliance": "25GBase-CR", "sfp_found": true, "sfp_part": "SFP-H25G-CU3M",  "sfp_vendor": "OEM", "speed": 10000, "up": true}
Port 3:  {"autoneg": true,  "enable": true, "flowctrl_rx": true, "flowctrl_tx": true, "full_duplex": true, "jumbo": true, "media": "SFP+",  "name": "ESXI03 - vmnic11", "op_mode": "switch", "port_idx": 3,  "portconf_id": "a5ae6ce7-c2e2-4113-98d7-47a5c9744922", "sfp_compliance": "DAC",       "sfp_found": true, "sfp_part": "SFP-H10GB-CU3M", "sfp_vendor": "OEM", "speed": 1000,  "up": true}
Port 4:  {"autoneg": false, "enable": true, "flowctrl_rx": true, "flowctrl_tx": true, "full_duplex": true, "jumbo": true, "media": "SFP+",  "name": "ESXI02 - vmnic7",  "op_mode": "switch", "port_idx": 4,  "portconf_id": "a5ae6ce7-c2e2-4113-98d7-47a5c9744922", "sfp_compliance": "DAC",       "sfp_found": true, "sfp_part": "SFP-H10GB-CU3M", "sfp_vendor": "OEM", "speed": 10000, "up": true}
Port 15: {"autoneg": false, "enable": true, "flowctrl_rx": true, "flowctrl_tx": true, "full_duplex": true, "jumbo": true, "media": "SFP+",  "name": "ESXI01 - vmnic7",  "op_mode": "switch", "port_idx": 15, "portconf_id": "a5ae6ce7-c2e2-4113-98d7-47a5c9744922", "sfp_compliance": "DAC",       "sfp_found": true, "sfp_part": "SFP-H10GB-CU3M", "sfp_vendor": "OEM", "speed": 10000, "up": true}
Port 29: {"autoneg": false, "enable": true, "flowctrl_rx": true, "flowctrl_tx": true, "full_duplex": true, "jumbo": true, "media": "SFP28", "name": "ESXI01 - vmnic6",  "op_mode": "switch", "port_idx": 29, "portconf_id": "a5ae6ce7-c2e2-4113-98d7-47a5c9744922", "sfp_compliance": "25GBase-CR", "sfp_found": true, "sfp_part": "SFP-H25G-CU3M",  "sfp_vendor": "OEM", "speed": 25000, "up": true}
Port 30: {"autoneg": false, "enable": true, "flowctrl_rx": true, "flowctrl_tx": true, "full_duplex": true, "jumbo": true, "media": "SFP28", "name": "ESXI04 - vmnic6",  "op_mode": "switch", "port_idx": 30, "portconf_id": "a5ae6ce7-c2e2-4113-98d7-47a5c9744922", "sfp_compliance": "25GBase-CR", "sfp_found": true, "sfp_part": "SFP-H25G-CU3M",  "sfp_vendor": "OEM", "speed": 25000, "up": true}
Port 31: {"autoneg": false, "enable": true, "flowctrl_rx": true, "flowctrl_tx": true, "full_duplex": true, "jumbo": true, "media": "SFP28", "name": "ESXI03 - vmnic10", "op_mode": "switch", "port_idx": 31, "portconf_id": "a5ae6ce7-c2e2-4113-98d7-47a5c9744922", "sfp_compliance": "25GBase-CR", "sfp_found": true, "sfp_part": "SFP-H25G-CU3M",  "sfp_vendor": "OEM", "speed": 25000, "up": true}
Port 32: {"autoneg": true,  "enable": true, "flowctrl_rx": true, "flowctrl_tx": true, "full_duplex": true, "jumbo": true, "media": "SFP28", "name": "ESXI02 - vmnic6",  "op_mode": "switch", "port_idx": 32, "portconf_id": "a5ae6ce7-c2e2-4113-98d7-47a5c9744922", "sfp_compliance": "25GBase-CR", "sfp_found": true, "sfp_part": "SFP-H25G-CU3M",  "sfp_vendor": "OEM", "speed": 25000, "up": true}
```

#### AFTER (`post_verify_full.txt`):
```json
Port 3 override:
{"autoneg": false, "dot1x_ctrl": "force_authorized", "dot1x_idle_timeout": 300, "excluded_networkconf_ids": ["0ed17ee4-576a-482b-82ef-28ac69fdf370", "735a4415-51cf-43b3-a23d-f1cc4f051d38"], "forward": "customize", "full_duplex": true, "lldpmed_enabled": true, "name": "ESXI03 - vmnic11", "native_networkconf_id": "", "op_mode": "switch", "port_idx": 3, "setting_preference": "manual", "speed": 10000, "stp_port_mode": true, "tagged_vlan_mgmt": "custom"}

Port 3 port_table:
{"autoneg": false, "enable": true, "flowctrl_rx": true, "flowctrl_tx": true, "full_duplex": true, "jumbo": true, "media": "SFP+", "name": "ESXI03 - vmnic11", "op_mode": "switch", "port_idx": 3, "sfp_part": "SFP-H10GB-CU3M", "speed": 10000, "up": true}
```

### Raw Before vs. After — `esxi03` (`10.0.20.43`) `esxcli network nic list`

#### BEFORE (`esxi_inspect_pre.txt`, `rc=0`):
```text
Name     PCI Device    Driver      Admin Status  Link Status  Speed  Duplex  MAC Address         MTU  Description
-------  ------------  ----------  ------------  -----------  -----  ------  -----------------  ----  -----------
vmnic0   0000:02:00.0  ntg3        Up            Up            1000  Full    08:f1:ea:97:7a:d8  1500  Broadcom Corporation NetXtreme BCM5719 Gigabit Ethernet
vmnic1   0000:02:00.1  ntg3        Up            Up            1000  Full    08:f1:ea:97:7a:d9  1500  Broadcom Corporation NetXtreme BCM5719 Gigabit Ethernet
vmnic10  0000:b0:00.0  nmlx5_core  Up            Up           25000  Full    6c:b3:11:79:91:0e  9000  Mellanox Technologies ConnectX-4 Lx EN NIC; 25GbE; dual-port SFP28; PCIe3.0 x8; (MCX4121A-ACU)
vmnic11  0000:b0:00.1  nmlx5_core  Up            Up            1000  Full    6c:b3:11:79:91:0f  9000  Mellanox Technologies ConnectX-4 Lx EN NIC; 25GbE; dual-port SFP28; PCIe3.0 x8; (MCX4121A-ACU)
vmnic2   0000:02:00.2  ntg3        Up            Up            1000  Full    08:f1:ea:97:7a:da  1500  Broadcom Corporation NetXtreme BCM5719 Gigabit Ethernet
vmnic3   0000:02:00.3  ntg3        Up            Up            1000  Full    08:f1:ea:97:7a:db  1500  Broadcom Corporation NetXtreme BCM5719 Gigabit Ethernet
vmnic8   0000:13:00.0  ixgben      Up            Up           10000  Full    98:b7:85:22:8d:2d  9000  Intel(R) Ethernet Controller 10 Gigabit X540-AT2
vmnic9   0000:13:00.1  ixgben      Down          Down             0  Half    98:b7:85:22:8d:2e  9000  Intel(R) Ethernet Controller 10 Gigabit X540-AT2
```

#### AFTER (`post_verify_full.txt`, `rc=0`):
```text
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
```

---

## 2. Forensic Audit of `ESXI03 - vmnic6` (USAGGPRO Port 1) & `ESXI03 - vmnic7` (USAGGPRO Port 17) and `esxi03` Active Uplink Mapping

1. **Why USAGGPRO Port 1 (`ESXI03 - vmnic6`) and Port 17 (`ESXI03 - vmnic7`) show 10G Up (`mac_table_count=0`) while missing from `esxcli network nic list`**:
   - `esxi03` has an onboard `Emulex Corporation HP FlexFabric 10Gb 2-port 556FLR-SFP+ Adapter` (`0000:5d:00.0` and `0000:5d:00.1`, SkyHawk ASIC `fw 10.5.65.21`, driver `elxnet`, PCI-assigned `vmnic4` and `vmnic5`; historical DVS `uplinkInfo` also shows `vmnic4..7` mapped to `DVPort 178..181`).
   - Physical SFP+ electrical signaling on `0000:5d:00.0` / `0000:5d:00.1` keeps USAGGPRO Ports 1 and 17 linked at 10G (`mac_table_count=0`), but during ESXi boot (`2026-10-01T06:19:10Z`), the `elxnet` driver failed Mailbox/MCC commands (`opcode 36-1: Set flow control failed`, `opcode 5-1: Link status query failed`), causing the VMkernel Uplink layer to clear `DEVICE_PRESENT` (`0x41800`):
     ```text
     2026-10-01T06:19:10.189Z cpu61:2098248)WARNING: elxnet: elxnet_mccComplProcess:1152: [vmnic5] Mailbox/MCC command opcode 36-1 failed:status 1-0
     2026-10-01T06:19:10.189Z cpu22:2097613)WARNING: elxnet: elxnet_uplinkStartIO:3361: [vmnic5] Set flow control failed
     2026-10-01T06:19:10.207Z cpu61:2098248)WARNING: elxnet: elxnet_mccComplProcess:1152: [vmnic5] Mailbox/MCC command opcode 5-1 failed:status 1-0
     2026-10-01T06:19:10.207Z cpu22:2097613)WARNING: elxnet: elxnet_uplinkStartIO:3382: [vmnic5] Link status query failed
     2026-10-01T06:19:10.302Z cpu10:2098124)Uplink: 14903: vmnic5: clear flags 0x41800 DEVICE_PRESENT
     ```
2. **`esxi03` VDS `25GB` Active `teamUplink` Before vs. After (`vsish -e get /net/portsets/DvsPortset-0/ports/<PortNum>/teamUplink`)**:
   - **Before**: `AI DEV.eth0` (`10.0.70.125`, `dvPort=1`) and `vmk4` (`10.0.40.23`, `dvPort=90`) were hashed onto `vmnic11` (`dvPort=182`, `Uplink 8`) while `vmnic11` was stuck at **1000 Mbps**!
   - **After**: `AI DEV.eth0` (`10.0.70.125`) is hashed onto **`vmnic10` (25000 Mbps)**, `vmk1` (`10.0.60.23`) and `vmk2` (`10.0.50.23`) are hashed onto **`vmnic10` (25000 Mbps)**, `vmk4` (`10.0.40.23`) is on **`vmnic11` (now 10000 Mbps Full Duplex, MTU 9000)**, and `vmk3` (`10.0.20.43`), `VC04.eth0` (`10.0.20.70`), `VC04.eth1`, `test3big.eth0`, `spare4.eth0`, `spare3.eth0` are on **`vmnic8` (10000 Mbps)**. Zero ports on `esxi03` ride a 1 Gbps uplink.

---

## 3. UniFi Jumbo Frames & IEEE 802.3x Flow Control Verification (`USAGGPRO` & `USL8A`)

Command: `python3 /home/mboyle/UniversalSwarmOS/infra/network/unifi-flow-control.py --verify` (`exit=0`)

```text
============================================================
 UniFi Switching Fabric: Flow Control & Jumbo Frames Audit  
============================================================
Timestamp:  2026-10-17T17:06:16Z
Console ID: 602232292CA100000000069BFAAB0000000006EB9D760000000062F4D112:2071288107

--- Global Switch Configuration ---
  Flow Control (802.3x): ENABLED (True)
  Jumbo Frames:          ENABLED (True)
  Switch Exclusions:     []

--- Switch Inventory & Port Inspection ---
  ✓ Office       (10.0.10.111 , USAGGPRO  ): Ports: 32 total, 26 up | Jumbo: 32/32 | FlowCtrl RX: 32, TX: 32
  ✓ Office       (10.0.10.34  , USPM16P   ): Ports: 18 total, 13 up | Jumbo: 18/18 | FlowCtrl RX: 16, TX: 16
  ✓ Office       (10.0.10.182 , USL8A     ): Ports: 8 total, 7 up | Jumbo: 8/8 | FlowCtrl RX: 0, TX: 0
  ✓ Pantry       (10.0.1.98   , USL8A     ): Ports: 8 total, 3 up | Jumbo: 8/8 | FlowCtrl RX: 0, TX: 0
  ✓ Office       (10.0.10.192 , USL24B    ): Ports: 26 total, 9 up | Jumbo: 26/26 | FlowCtrl RX: 24, TX: 24

--- Target Switch Verification ---
  USW Pro Aggregation (10.0.10.111): Flow Control active on 32/32 ports, Jumbo on 32/32 ports. State: CONNECTED.
  USW Aggregation (10.0.10.182): Jumbo active on 8/8 ports. State: CONNECTED.

Overall Compliance: PASS (Fabric Optimized)
============================================================
[SUCCESS] All switches verified with Flow Control & Jumbo Frames active.
unifi-flow-control exit=0
```

---

## 4. VDS `25GB` Uplink Audit & Addition of `esxi01:vmnic7` and `esxi02:vmnic7` (MTU 9000)

### Uplink Audit & Remediation Summary
- **`esxi01` (`10.0.20.41`)**:
  - **Before**: `Uplinks: vmnic4, vmnic5, vmnic8, vmnic9, vmnic6`. `vmnic7` (`0000:05:00.1`, Mellanox ConnectX-4 Lx Port 2, `10000 Full` on `USAGGPRO` Port 15) was unattached (`MTU 1500`).
  - **Action**: Armed 45s on-host dead-man timer (`PID 12669001`) and attached `vmnic7` to VDS `25GB` `DVPort 242` (`esxcfg-vswitch -P vmnic7 -V 242 25GB`, `rc=0`).
  - **After**: `Uplinks: vmnic7, vmnic4, vmnic5, vmnic8, vmnic9, vmnic6`. `vmnic7` is `Up 10000 Full, MTU 9000`.
- **`esxi02` (`10.0.20.42`)**:
  - **Before**: `Uplinks: vmnic4, vmnic5, vmnic6`. `vmnic7` (`0000:88:00.1`, Mellanox ConnectX-4 Lx Port 2, `10000 Full` on `USAGGPRO` Port 4) was unattached (`MTU 1500`).
  - **Action**: Armed 45s on-host dead-man timer (`PID 7045412`) and attached `vmnic7` to VDS `25GB` `DVPort 208` (`esxcfg-vswitch -P vmnic7 -V 208 25GB`, `rc=0`).
  - **After**: `Uplinks: vmnic7, vmnic4, vmnic5, vmnic6`. `vmnic7` is `Up 10000 Full, MTU 9000`.
- **`esxi03` (`10.0.20.43`)**:
  - **Before**: `Uplinks: vmnic11 (1000 Mbps), vmnic8 (10000 Mbps), vmnic9 (Down), vmnic10 (25000 Mbps)`.
  - **After**: `Uplinks: vmnic11 (10000 Mbps Full, MTU 9000), vmnic8 (10000 Mbps Full, MTU 9000), vmnic9 (Down), vmnic10 (25000 Mbps Full, MTU 9000)`.
- **`esxi04` (`10.0.20.44`)**:
  - **Before & After**: `Uplinks: vmnic4 (10G), vmnic5 (10G), vmnic7 (10G), vmnic8 (10G), vmnic9 (10G), vmnic6 (25G)` — all `Up, Full, MTU 9000`.

### Post-Change NIC & VDS Uplink State Across `esxi01..04`
```text
=== esxi01 (10.0.20.41) ===
vmnic4   0000:04:00.0  i40en       Up  Up  10000  Full  48:df:37:d5:66:20  9000  Intel(R) Ethernet Controller X710 for 10GbE SFP+
vmnic5   0000:04:00.1  i40en       Up  Up  10000  Full  48:df:37:d5:66:28  9000  Intel(R) Ethernet Controller X710 for 10GbE SFP+
vmnic6   0000:05:00.0  nmlx5_core  Up  Up  25000  Full  6c:b3:11:79:91:ce  9000  Mellanox Technologies ConnectX-4 Lx EN NIC; 25GbE
vmnic7   0000:05:00.1  nmlx5_core  Up  Up  10000  Full  6c:b3:11:79:91:cf  9000  Mellanox Technologies ConnectX-4 Lx EN NIC; 25GbE
vmnic8   0000:08:00.0  ixgben      Up  Up  10000  Full  98:b7:85:22:55:1a  9000  Intel(R) 82599 10 Gigabit Dual Port Network Connection
vmnic9   0000:08:00.1  ixgben      Up  Up  10000  Full  98:b7:85:22:55:1b  9000  Intel(R) 82599 10 Gigabit Dual Port Network Connection
DVS 25GB Uplinks: vmnic7 (DVPort 242), vmnic4 (DVPort 241), vmnic5 (DVPort 240), vmnic8 (DVPort 239), vmnic9 (DVPort 238), vmnic6 (DVPort 237)

=== esxi02 (10.0.20.42) ===
vmnic4   0000:04:00.0  i40en       Up  Up  10000  Full  48:df:37:e0:b7:90  9000  Intel(R) Ethernet Controller X710 for 10GbE SFP+
vmnic5   0000:04:00.1  i40en       Up  Up  10000  Full  48:df:37:e0:b7:98  9000  Intel(R) Ethernet Controller X710 for 10GbE SFP+
vmnic6   0000:88:00.0  nmlx5_core  Up  Up  25000  Full  6c:b3:11:79:91:ba  9000  Mellanox Technologies ConnectX-4 Lx EN NIC; 25GbE
vmnic7   0000:88:00.1  nmlx5_core  Up  Up  10000  Full  6c:b3:11:79:91:bb  9000  Mellanox Technologies ConnectX-4 Lx EN NIC; 25GbE
DVS 25GB Uplinks: vmnic7 (DVPort 208), vmnic4 (DVPort 207), vmnic5 (DVPort 206), vmnic6 (DVPort 211)

=== esxi03 (10.0.20.43) ===
vmnic10  0000:b0:00.0  nmlx5_core  Up    Up    25000  Full  6c:b3:11:79:91:0e  9000  Mellanox Technologies ConnectX-4 Lx EN NIC; 25GbE
vmnic11  0000:b0:00.1  nmlx5_core  Up    Up    10000  Full  6c:b3:11:79:91:0f  9000  Mellanox Technologies ConnectX-4 Lx EN NIC; 25GbE
vmnic8   0000:13:00.0  ixgben      Up    Up    10000  Full  98:b7:85:22:8d:2d  9000  Intel(R) Ethernet Controller 10 Gigabit X540-AT2
vmnic9   0000:13:00.1  ixgben      Down  Down      0  Half  98:b7:85:22:8d:2e  9000  Intel(R) Ethernet Controller 10 Gigabit X540-AT2
DVS 25GB Uplinks: vmnic11 (DVPort 182), vmnic8 (DVPort 176), vmnic9 (DVPort 175), vmnic10 (DVPort 177)

=== esxi04 (10.0.20.44) ===
vmnic4   0000:5d:00.0  elxnet      Up  Up  10000  Full  70:10:6f:43:b5:18  9000  Emulex Corporation HP FlexFabric 10Gb 2-port 556FLR-SFP+ Adapter
vmnic5   0000:5d:00.1  elxnet      Up  Up  10000  Full  70:10:6f:43:b5:20  9000  Emulex Corporation HP FlexFabric 10Gb 2-port 556FLR-SFP+ Adapter
vmnic6   0000:37:00.0  nmlx5_core  Up  Up  25000  Full  94:40:c9:aa:d6:b4  9000  Mellanox Technologies MT27800 Family [ConnectX-5]
vmnic7   0000:37:00.1  nmlx5_core  Up  Up  10000  Full  94:40:c9:aa:d6:b5  9000  Mellanox Technologies MT27800 Family [ConnectX-5]
vmnic8   0000:b0:00.0  ixgben      Up  Up  10000  Full  98:b7:85:22:5e:ea  9000  Intel(R) 82599 10 Gigabit Dual Port Network Connection
vmnic9   0000:b0:00.1  ixgben      Up  Up  10000  Full  98:b7:85:22:5e:eb  9000  Intel(R) 82599 10 Gigabit Dual Port Network Connection
DVS 25GB Uplinks: vmnic6 (DVPort 144), vmnic9 (DVPort 145), vmnic8 (DVPort 146), vmnic7 (DVPort 147), vmnic5 (DVPort 148), vmnic4 (DVPort 149)
```

---

## 5. End-to-End Jumbo Frame (`MTU 9000` / `vmkping -d -s 8972`) Verification Mesh Across `esxi01..04`

All 32 end-to-end tests (`vmkping -I vmk1 -d -s 8972 -c 2` across `10.0.60.21..24` and `vmkping -I vmk2 -d -s 8972 -c 2` across `10.0.50.21..24`, plus `vmkping -I vmk4 -d -s 8972 -c 2 10.0.40.21` over `esxi03:vmnic11`) succeeded with **`0% packet loss` (`exit=0`)**:

| Source Host | Interface | Source IP | Target `.21` (esxi01) | Target `.22` (esxi02) | Target `.23` (esxi03) | Target `.24` (esxi04) |
|---|---|---|---|---|---|---|
| `esxi01` | `vmk1` (vSAN) | `10.0.60.21` | `0% loss, 0.065ms (rc=0)` | `0% loss, 0.196ms (rc=0)` | `0% loss, 0.160ms (rc=0)` | `0% loss, 0.142ms (rc=0)` |
| `esxi01` | `vmk2` (vMotion) | `10.0.50.21` | `0% loss, 0.074ms (rc=0)` | `0% loss, 0.290ms (rc=0)` | `0% loss, 0.240ms (rc=0)` | `0% loss, 0.281ms (rc=0)` |
| `esxi02` | `vmk1` (vSAN) | `10.0.60.22` | `0% loss, 0.180ms (rc=0)` | `0% loss, 0.062ms (rc=0)` | `0% loss, 0.179ms (rc=0)` | `0% loss, 0.185ms (rc=0)` |
| `esxi02` | `vmk2` (vMotion) | `10.0.50.22` | `0% loss, 0.319ms (rc=0)` | `0% loss, 0.077ms (rc=0)` | `0% loss, 0.252ms (rc=0)` | `0% loss, 0.246ms (rc=0)` |
| `esxi03` | `vmk1` (vSAN) | `10.0.60.23` | `0% loss, 0.165ms (rc=0)` | `0% loss, 0.200ms (rc=0)` | `0% loss, 0.074ms (rc=0)` | `0% loss, 0.166ms (rc=0)` |
| `esxi03` | `vmk2` (vMotion) | `10.0.50.23` | `0% loss, 0.295ms (rc=0)` | `0% loss, 0.234ms (rc=0)` | `0% loss, 0.085ms (rc=0)` | `0% loss, 0.272ms (rc=0)` |
| `esxi03` | `vmk4` (`vmnic11`) | `10.0.40.23` | `0% loss, 0.330ms (rc=0)` | — | — | — |
| `esxi04` | `vmk1` (vSAN) | `10.0.60.24` | `0% loss, 0.195ms (rc=0)` | `0% loss, 0.192ms (rc=0)` | `0% loss, 0.156ms (rc=0)` | `0% loss, 0.078ms (rc=0)` |
| `esxi04` | `vmk2` (vMotion) | `10.0.50.24` | `0% loss, 0.333ms (rc=0)` | `0% loss, 0.249ms (rc=0)` | `0% loss, 0.221ms (rc=0)` | `0% loss, 0.088ms (rc=0)` |
