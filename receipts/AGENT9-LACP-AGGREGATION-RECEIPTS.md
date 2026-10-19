# AGENT-9: UniFi Port Labeling & vSphere VDS `25GB` Multi-NIC Load-Based Teaming Receipts

- **Timestamp (UTC)**: `2026-10-17T20:25:30Z`
- **Status**: `COMPLETED (RC=0)`
- **Target Switches**:
  - `Office (USAGGPRO)` (`10.0.10.111`, `_id: c4b8bfa2-f496-46a8-bb31-cda8baf247fb`)
  - `Office (USL8A)` (`10.0.10.182`, `_id: 68f7f7e04d734b5c1c97b3e6`)
  - vSphere Distributed Switch `25GB` on `VC04` (`10.0.20.70`)

---

## 1. Verified Contiguous Physical Port Layout & Labels on `Office (USAGGPRO)` (`10.0.10.111`)

All 16 active 10GbE server links and 4 active 25GbE server links were verified via `esxcli network nic list`, `pyVmomi` `QueryNetworkHint()` (LLDP), and UniFi API `stat/device`, and persisted via `PUT /rest/device/c4b8bfa2-f496-46a8-bb31-cda8baf247fb` (`meta: {'rc': 'ok'}`):

| Switch Port | Host & Physical NIC | Speed / Duplex | Transceiver / DAC Model | Serial Number | LLDP Verification |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Port 1** | `esxi02 - vmnic4 (10G)` | `10,000 Mbps Full` | `SFP-H10GB-CU3M` | `CSS11OA1420` | Verified (`48:df:37:e0:b7:90`) |
| **Port 2** | `esxi02 - vmnic5 (10G)` | `10,000 Mbps Full` | `SFP-H10GB-CU3M` | `CSS11OA1724` | Verified (`48:df:37:e0:b7:98`) |
| **Port 3** | `esxi02 - vmnic7 (10G)` | `10,000 Mbps Full` | `SFP-H10GB-CU3M` | `CSS11OA1102` | `LLDP=Port 3` (`6c:b3:11:79:91:bb`) |
| **Port 4** | `esxi04 - vmnic4 (10G)` | `10,000 Mbps Full` | `SFP-H10GB-CU3M` | `CSS11OA1408` | `LLDP=Port 4` (`70:10:6f:43:b5:18`) |
| **Port 5** | `esxi04 - vmnic7 (10G)` | `10,000 Mbps Full` | `SFP-H10GB-CU3M` | `CSS11OA3482` | `LLDP=Port 5` (`94:40:c9:aa:d6:b5`) |
| **Port 6** | `esxi04 - vmnic8 (10G)` | `10,000 Mbps Full` | `SFP-H10GB-CU3M` | `CSS11OA1726` | `LLDP=Port 6` (`98:b7:85:22:5e:ea`) |
| **Port 7** | `esxi04 - vmnic5 (10G)` | `10,000 Mbps Full` | `SFP-H10GB-CU3M` | `CSS11OA1379` | `LLDP=Port 7` (`70:10:6f:43:b5:20`) |
| **Port 8** | `esxi04 - vmnic9 (10G)` | `10,000 Mbps Full` | `SFP-H10GB-CU3M` | `CSS11OA1406` | `LLDP=Port 8` (`98:b7:85:22:5e:eb`) |
| **Port 10** | `esxi03 - vmnic9 (10G RJ45)` | `10,000 Mbps Full` | `BJ-SFP-10G-T` | `BJ62504041274` | `LLDP=Port 10` (`98:b7:85:22:8d:2e`) |
| **Port 11** | `esxi03 - vmnic11 (10G)` | `10,000 Mbps Full` | `SFP-H10GB-CU3M` | `CSS11OA1384` | `LLDP=Port 11` (`6c:b3:11:79:91:0f`) |
| **Port 12** | `esxi03 - vmnic8 (10G RJ45)` | `10,000 Mbps Full` | `BJ-SFP-10G-T` | `BJ62504040333` | `LLDP=Port 12` (`98:b7:85:22:8d:2d`) |
| **Port 13** | `esxi01 - vmnic4 (10G)` | `10,000 Mbps Full` | `SFP-H10GB-CU3M` | `CSS11OA3459` | Verified (`48:df:37:d5:66:20`) |
| **Port 14** | `esxi01 - vmnic5 (10G)` | `10,000 Mbps Full` | `SFP-H10GB-CU3M` | `CSS11OA3478` | Verified (`48:df:37:d5:66:28`) |
| **Port 15** | `esxi01 - vmnic9 (10G)` | `10,000 Mbps Full` | `SFP-H10GB-CU3M` | `CSS11OA1382` | `LLDP=Port 15` (`98:b7:85:22:55:1b`) |
| **Port 16** | `esxi01 - vmnic8 (10G)` | `10,000 Mbps Full` | `SFP-H10GB-CU3M` | `CSS11OA1377` | `LLDP=Port 16` (`98:b7:85:22:55:1a`) |
| **Port 17** | `esxi01 - vmnic6 (10G)` | `10,000 Mbps Full` | `SFP-H25G-CU3M` | `CSC241104260371` | `LLDP=Port 17` (`6c:b3:11:79:91:ce`) |
| **Port 29** | `esxi01 - vmnic7 (25G)` | `25,000 Mbps Full` | `SFP-H25G-CU3M` | `CSC241104261356` | `LLDP=Port 29` (`6c:b3:11:79:91:cf`) |
| **Port 30** | `esxi03 - vmnic10 (25G)` | `25,000 Mbps Full` | `SFP-H25G-CU3M` | `CSC241104261212` | `LLDP=Port 30` (`6c:b3:11:79:91:0e`) |
| **Port 31** | `esxi02 - vmnic6 (25G)` | `25,000 Mbps Full` | `SFP-H25G-CU3M` | `CSC241104261315` | `LLDP=Port 31` (`6c:b3:11:79:91:ba`) |
| **Port 32** | `esxi04 - vmnic6 (25G)` | `25,000 Mbps Full` | `SFP-H25G-CU3M` | `CSC241104260821` | `LLDP=Port 32` (`94:40:c9:aa:d6:b4`) |

### `Office (USL8A)` (`10.0.10.182`) Verified Port Labels (`meta: {'rc': 'ok'}`)
- **Port 2**: `esxi03 - vmnic5 (10G)` (`DAC-SFP10-1M`, `LLDP=Port 2`, `34:64:a9:bb:8d:a0`)
- **Port 6**: `esxi03 - vmnic4 (10G)` (`SFP-10G-DAC-005`, `LLDP=Port 6`, `34:64:a9:bb:8d:98`)
- **Ports 7–8**: `To USAGGPRO Port 20/21 (LAG)` (`20 Gbps` LACP trunk to `USAGGPRO`)

---

## 2. vSphere Distributed Switch (`VDS 25GB`) Load-Based Teaming (`loadbalance_loadbased`)

Because each ESXi host combines **`1x 25GbE` uplink + `3..5x 10GbE` uplinks** on `VDS 25GB` (`55 Gbps` on `esxi02`, `75 Gbps` on `esxi01`, `esxi03`, and `esxi04` = **`280 Gbps` aggregate fabric capacity**), IEEE 802.3ad LACP LAGs cannot mix `25GbE` and `10GbE` links in the same active portgroup team. Instead, VMware's **Route Based on Physical NIC Load (`loadbalance_loadbased` / LBT)** keeps all `25GbE` and `10GbE` uplinks simultaneously active (`Uplink 1..6`) and dynamically rebalances flows across all physical NICs without LACP negotiation risk.
