# AGENT-17: Post-LACP Tesla T4 GPU RoCE DVPort Override & Switch Topology Verification Receipts

- **Timestamp (UTC)**: `2026-10-17T21:03:30Z`
- **Status**: `COMPLETED (RC=0)`
- **Artifacts Written / Updated**:
  1. `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/unifi_switches_post_lacp.json` (`91,802 bytes`, `rc=0`)
  2. `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT8-PHYSICAL-RACK-GUIDE.md` (Updated Section 2 `USAGGPRO` 32-Port Post-LACP Table & Section 3.A `USL8A` 8-Port Table)
  3. `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT17-ROCE-POST-LACP-RECEIPTS.md`

---

## 1. `pyVmomi` & `esxcli` Verification of the 4 Tesla T4 GPU RoCE DVPorts on `VDS 25GB` (`Infrastructure 25GB`)

### 1.1 Anti-Sycophancy DVPort Numbering Ground Truth (`364..367` vs. `321, 313, 1, 293`)
- On `VDS 25GB` (`Infrastructure 25GB`, `dvportgroup-11`), DVPorts `365`, `366`, and `367` belong to ephemeral test VMs (`lens-r3c-cx4-20261014`, `lens-r2eb3-cx5-20261014`, `kvcache-cx1-20261014`, all with `inherited=True` and `active=['LAG1']`), and `DVPort 364` is unallocated (`NOT FOUND`).
- The **4 actual Tesla T4 GPU VMs** occupy **DVPorts `321`, `313`, `1`, and `293`** in `Infrastructure 25GB` (`dvportgroup-11`).
- **All 4 GPU VMs retained their per-port `uplinkTeamingPolicy` overrides (`tp.inherited=False, po.inherited=False`)** after `Infrastructure 25GB` default policy was updated to `active=['LAG1']`, keeping them pinned to their host's dedicated **25GbE Mellanox standalone uplink (`USAGGPRO Ports 29–32`)**:
  - **`DVPort 321` (`AI Inference` `.14` on `esxi01`)**: `active=['Uplink 6']` $\rightarrow$ `key=242` (`vmnic7` 25GbE on `USAGGPRO Port 29`)
  - **`DVPort 313` (`llama` `.13` on `esxi02`)**: `active=['Uplink 6']` $\rightarrow$ `key=211` (`vmnic6` 25GbE on `USAGGPRO Port 31`)
  - **`DVPort 1` (`AI DEV` `.11` on `esxi03`)**: `active=['Uplink 3']` $\rightarrow$ `key=177` (`vmnic10` 25GbE on `USAGGPRO Port 30`)
  - **`DVPort 293` (`AI` `.12` on `esxi04`)**: `active=['Uplink 1']` $\rightarrow$ `key=144` (`vmnic6` 25GbE on `USAGGPRO Port 32`)

### 1.2 Raw `pyVmomi` `FetchDVPorts` Output (`rc=0`)
```text
=== SPECIFIC DVPORT CHECK (364, 365, 366, 367, 321, 313, 1, 293) ===
  DVPort 364: NOT FOUND
  DVPort 365 : pg=dvportgroup-11  entity='lens-r3c-cx4-20261014'    nic='4000' tp.inh=True po.inh=True policy=loadbalance_srcid active=['LAG1'] standby=[]
  DVPort 366 : pg=dvportgroup-11  entity='lens-r2eb3-cx5-20261014'  nic='4000' tp.inh=True po.inh=True policy=loadbalance_srcid active=['LAG1'] standby=[]
  DVPort 367 : pg=dvportgroup-11  entity='kvcache-cx1-20261014'     nic='4000' tp.inh=True po.inh=True policy=loadbalance_srcid active=['LAG1'] standby=[]
  DVPort 321 : pg=dvportgroup-11  entity='AI Inference'             nic='4000' tp.inh=False po.inh=False policy=loadbalance_srcid active=['Uplink 6'] standby=['Uplink 1', 'Uplink 2', 'Uplink 3', 'Uplink 4', 'Uplink 5']
  DVPort 313 : pg=dvportgroup-11  entity='llama'                    nic='4000' tp.inh=False po.inh=False policy=loadbalance_srcid active=['Uplink 6'] standby=['Uplink 1', 'Uplink 2']
  DVPort 1   : pg=dvportgroup-11  entity='AI DEV'                   nic='4000' tp.inh=False po.inh=False policy=loadbalance_srcid active=['Uplink 3'] standby=['Uplink 1', 'Uplink 2']
  DVPort 293 : pg=dvportgroup-11  entity='AI'                       nic='4000' tp.inh=False po.inh=False policy=loadbalance_srcid active=['Uplink 1'] standby=['Uplink 2', 'Uplink 3', 'Uplink 4', 'Uplink 5', 'Uplink 6']
```

### 1.3 Raw ESXi Kernel `TeamUplink` Verification (`govc host.esxcli network vm port list`, `rc=0`)
```text
=== 1. ESXi KERNEL TEAMUPLINK VERIFICATION ===
--- Host 10.0.20.41 (AI_Inference, WorldID=2112836) ---
DVPortID:      321
IPAddress:     0.0.0.0
MACAddress:    00:50:56:83:2f:fd
PortID:        67108898
Portgroup:     dvportgroup-11
TeamUplink:    vmnic7
UplinkPortID:  2214592537
vSwitch:       25GB
rc=0
--- Host 10.0.20.42 (llama, WorldID=4689267) ---
DVPortID:      313
IPAddress:     0.0.0.0
MACAddress:    00:50:56:83:60:61
PortID:        67109091
Portgroup:     dvportgroup-11
TeamUplink:    vmnic6
UplinkPortID:  2214592523
vSwitch:       25GB
rc=0
--- Host 10.0.20.43 (AI_DEV, WorldID=2114091) ---
DVPortID:      1
IPAddress:     0.0.0.0
MACAddress:    00:50:56:83:7a:1c
PortID:        67108893
Portgroup:     dvportgroup-11
TeamUplink:    vmnic10
UplinkPortID:  2214592525
vSwitch:       25GB
rc=0
--- Host 10.0.20.44 (AI, WorldID=7305928) ---
DVPortID:      293
IPAddress:     0.0.0.0
MACAddress:    00:50:56:83:e6:7a
PortID:        67108998
Portgroup:     dvportgroup-11
TeamUplink:    vmnic6
UplinkPortID:  2214592525
vSwitch:       25GB
rc=0
```

---

## 2. All-Pairs 9000-Byte Jumbo Frame Ping (`ping -c 2 -M do -s 8972`) & `iperf3 -P 4` Benchmarks (`10.0.75.11..14`)

### 2.1 All 12 Directional Pairs — 9000-Byte Unfragmented Jumbo Ping (`0% packet loss`, `rc=0`)
```text
=== 2. ALL-PAIRS 9000-BYTE JUMBO PING (ping -c 2 -M do -s 8972) ===
  10.0.75.11 (10.0.70.83) -> 10.0.75.12: 2 packets transmitted, 2 received, 0% packet loss, time 1042ms | rtt min/avg/max/mdev = 0.350/0.358/0.367/0.008 ms | rc=0
  10.0.75.11 (10.0.70.83) -> 10.0.75.13: 2 packets transmitted, 2 received, 0% packet loss, time 1009ms | rtt min/avg/max/mdev = 0.391/0.409/0.428/0.018 ms | rc=0
  10.0.75.11 (10.0.70.83) -> 10.0.75.14: 2 packets transmitted, 2 received, 0% packet loss, time 1010ms | rtt min/avg/max/mdev = 0.346/0.384/0.423/0.038 ms | rc=0
  10.0.75.12 (10.0.70.72) -> 10.0.75.11: 2 packets transmitted, 2 received, 0% packet loss, time 1060ms | rtt min/avg/max/mdev = 0.332/0.355/0.378/0.023 ms | rc=0
  10.0.75.12 (10.0.70.72) -> 10.0.75.13: 2 packets transmitted, 2 received, 0% packet loss, time 1010ms | rtt min/avg/max/mdev = 0.300/0.325/0.351/0.025 ms | rc=0
  10.0.75.12 (10.0.70.72) -> 10.0.75.14: 2 packets transmitted, 2 received, 0% packet loss, time 1011ms | rtt min/avg/max/mdev = 0.384/0.389/0.394/0.005 ms | rc=0
  10.0.75.13 (10.0.70.228) -> 10.0.75.11: 2 packets transmitted, 2 received, 0% packet loss, time 1043ms | rtt min/avg/max/mdev = 0.303/0.330/0.358/0.027 ms | rc=0
  10.0.75.13 (10.0.70.228) -> 10.0.75.12: 2 packets transmitted, 2 received, 0% packet loss, time 1008ms | rtt min/avg/max/mdev = 0.315/0.354/0.393/0.039 ms | rc=0
  10.0.75.13 (10.0.70.228) -> 10.0.75.14: 2 packets transmitted, 2 received, 0% packet loss, time 1007ms | rtt min/avg/max/mdev = 0.398/0.407/0.416/0.009 ms | rc=0
  10.0.75.14 (10.0.70.125) -> 10.0.75.11: 2 packets transmitted, 2 received, 0% packet loss, time 1064ms | rtt min/avg/max/mdev = 0.402/0.439/0.477/0.037 ms | rc=0
  10.0.75.14 (10.0.70.125) -> 10.0.75.12: 2 packets transmitted, 2 received, 0% packet loss, time 1008ms | rtt min/avg/max/mdev = 0.299/0.360/0.422/0.061 ms | rc=0
  10.0.75.14 (10.0.70.125) -> 10.0.75.13: 2 packets transmitted, 2 received, 0% packet loss, time 1008ms | rtt min/avg/max/mdev = 0.338/0.397/0.457/0.059 ms | rc=0
```

### 2.2 All 6 Unique Pairs — `iperf3 -P 4` Sustained 25GbE Line-Rate Throughput (`20.7–24.4 Gbps`, `rc=0`)
```text
=== 3. IPERF3 -P 4 MESH THROUGHPUT VERIFICATION ===
--- iperf3 -P 4: 10.0.75.12 (10.0.70.72) -> 10.0.75.11 (rc=0) ---
[SUM]   0.00-2.00   sec  5.65 GBytes  24.3 Gbits/sec  511             sender
[SUM]   0.00-2.00   sec  5.63 GBytes  24.1 Gbits/sec                  receiver
--- iperf3 -P 4: 10.0.75.13 (10.0.70.228) -> 10.0.75.11 (rc=0) ---
[SUM]   0.00-2.00   sec  4.83 GBytes  20.7 Gbits/sec    0             sender
[SUM]   0.00-2.00   sec  4.82 GBytes  20.7 Gbits/sec                  receiver
--- iperf3 -P 4: 10.0.75.14 (10.0.70.125) -> 10.0.75.11 (rc=0) ---
[SUM]   0.00-2.00   sec  4.91 GBytes  21.1 Gbits/sec   53             sender
[SUM]   0.00-2.00   sec  4.90 GBytes  21.0 Gbits/sec                  receiver
--- iperf3 -P 4: 10.0.75.13 (10.0.70.228) -> 10.0.75.12 (rc=0) ---
[SUM]   0.00-2.00   sec  5.56 GBytes  23.9 Gbits/sec   61             sender
[SUM]   0.00-2.00   sec  5.55 GBytes  23.8 Gbits/sec                  receiver
--- iperf3 -P 4: 10.0.75.14 (10.0.70.125) -> 10.0.75.12 (rc=0) ---
[SUM]   0.00-2.00   sec  5.67 GBytes  24.3 Gbits/sec    0             sender
[SUM]   0.00-2.00   sec  5.66 GBytes  24.3 Gbits/sec                  receiver
--- iperf3 -P 4: 10.0.75.14 (10.0.70.125) -> 10.0.75.13 (rc=0) ---
[SUM]   0.00-2.00   sec  5.68 GBytes  24.4 Gbits/sec   21             sender
[SUM]   0.00-2.00   sec  5.66 GBytes  24.3 Gbits/sec                  receiver
```

---

## 3. Post-LACP Switch Topology Snapshot (`unifi_switches_post_lacp.json`)

```text
=== USAGGPRO (10.0.10.111) _id=c4b8bfa2-f496-46a8-bb31-cda8baf247fb state=1 ===
  Port  1: name='esxi02 - 30G LACP (vmnic4/5/7)'     up=True  speed=10000 autoneg=False op_mode='aggregate' lag_idx=3     agg_by=False stp='forwarding' sfp=SFP-H10GB-CU3M/OEM/CSS11OA1420
  Port  2: name='SFP+ 2'                             up=True  speed=10000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=1     stp='forwarding' sfp=SFP-H10GB-CU3M/OEM/CSS11OA1724
  Port  3: name='SFP+ 3'                             up=True  speed=10000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=1     stp='forwarding' sfp=SFP-H10GB-CU3M/OEM/CSS11OA1102
  Port  4: name='esxi04 - 50G LACP (vmnic4/7/8/5/9)' up=True  speed=10000 autoneg=False op_mode='aggregate' lag_idx=6     agg_by=False stp='forwarding' sfp=SFP-H10GB-CU3M/OEM/CSS11OA1408
  Port  5: name='SFP+ 5'                             up=True  speed=10000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=4     stp='forwarding' sfp=SFP-H10GB-CU3M/OEM/CSS11OA3482
  Port  6: name='SFP+ 6'                             up=True  speed=10000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=4     stp='forwarding' sfp=SFP-H10GB-CU3M/OEM/CSS11OA1726
  Port  7: name='SFP+ 7'                             up=True  speed=10000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=4     stp='forwarding' sfp=SFP-H10GB-CU3M/OEM/CSS11OA1379
  Port  8: name='SFP+ 8'                             up=True  speed=10000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=4     stp='forwarding' sfp=SFP-H10GB-CU3M/OEM/CSS11OA1406
  Port  9: name='ESXi Reserved 9'                    up=True  speed=10000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=False stp='forwarding' sfp=SFP-H10GB-CU3M/OEM/CSS11OA1439
  Port 10: name='esxi03 - 30G LACP (vmnic9/11/8)'    up=True  speed=10000 autoneg=False op_mode='aggregate' lag_idx=5     agg_by=False stp='forwarding' sfp=BJ-SFP-10G-T/QINIYEK/BJ62504041274
  Port 11: name='SFP+ 11'                            up=True  speed=10000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=10    stp='forwarding' sfp=SFP-H10GB-CU3M/OEM/CSS11OA1384
  Port 12: name='SFP+ 12'                            up=True  speed=10000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=10    stp='forwarding' sfp=BJ-SFP-10G-T/QINIYEK/BJ62504040333
  Port 13: name='esxi01 - 50G LACP (vmnic4/5/9/8/6)' up=True  speed=10000 autoneg=False op_mode='aggregate' lag_idx=7     agg_by=False stp='forwarding' sfp=SFP-H10GB-CU3M/OEM/CSS11OA3459
  Port 14: name='SFP+ 14'                            up=True  speed=10000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=13    stp='forwarding' sfp=SFP-H10GB-CU3M/OEM/CSS11OA3478
  Port 15: name='SFP+ 15'                            up=True  speed=10000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=13    stp='forwarding' sfp=SFP-H10GB-CU3M/OEM/CSS11OA1382
  Port 16: name='SFP+ 16'                            up=True  speed=10000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=13    stp='forwarding' sfp=SFP-H10GB-CU3M/OEM/CSS11OA1377
  Port 17: name='SFP+ 17'                            up=True  speed=10000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=13    stp='forwarding' sfp=SFP-H25G-CU3M/OEM/CSC241104260371
  Port 18: name='SFP+ 18'                            up=False speed=10000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=False stp='disabled'   sfp=BJ-SFP-10G-T/QINIYEK/BJ62504041275
  Port 19: name='ESXi Reserved 19'                   up=True  speed=10000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=False stp='forwarding' sfp=SFP-H10GB-CU3M/OEM/CSS11OA3458
  Port 20: name='To USW Agg Office'                  up=True  speed=10000 autoneg=False op_mode='aggregate' lag_idx=1     agg_by=False stp='forwarding' sfp=SFP-H10GB-CU3M/OEM/CSS11OA1103
  Port 21: name='SFP+ 21'                            up=True  speed=10000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=20    stp='forwarding' sfp=SFP-H10GB-CU3M/OEM/CSS11OA1109
  Port 22: name='To UDM SE'                          up=True  speed=10000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=False stp='forwarding' sfp=SFP-10G-DAC-005/OEM/2506030095
  Port 23: name='To USW Pro Max'                     up=True  speed=10000 autoneg=False op_mode='aggregate' lag_idx=2     agg_by=False stp='forwarding' sfp=SFP-H10GB-CU3M/OEM/CSS11OA1302
  Port 24: name='SFP+ 24'                            up=True  speed=10000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=23    stp='forwarding' sfp=SFP-H10GB-CU3M/OEM/CSS11OA1107
  Port 25: name='SFP+ 25'                            up=True  speed=10000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=False stp='forwarding' sfp=BJ-SFP+-SR/QINIYEK/BJ82406120324
  Port 26: name='SFP+ 26'                            up=False speed=0     autoneg=True  op_mode='switch'   lag_idx=None  agg_by=False stp='disabled'   sfp=//
  Port 27: name='To USW24 Office'                    up=True  speed=1000  autoneg=True  op_mode='aggregate' lag_idx=4     agg_by=False stp='forwarding' sfp=SFP-10G-DAC-005/OEM/2506030094
  Port 28: name='SFP+ 28'                            up=True  speed=1000  autoneg=True  op_mode='switch'   lag_idx=None  agg_by=27    stp='forwarding' sfp=SFP-10G-DAC-005/FLYPRO/2505150027
  Port 29: name='esxi01 - vmnic7 (25G)'              up=True  speed=25000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=False stp='forwarding' sfp=SFP-H25G-CU3M/OEM/CSC241104261356
  Port 30: name='esxi03 - vmnic10 (25G)'             up=True  speed=25000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=False stp='forwarding' sfp=SFP-H25G-CU3M/OEM/CSC241104261212
  Port 31: name='esxi02 - vmnic6 (25G)'              up=True  speed=25000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=False stp='forwarding' sfp=SFP-H25G-CU3M/OEM/CSC241104261315
  Port 32: name='esxi04 - vmnic6 (25G)'              up=True  speed=25000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=False stp='forwarding' sfp=SFP-H25G-CU3M/OEM/CSC241104260821

=== USL8A (10.0.10.182) _id=33f933c8-3429-4f1e-a3d9-b8329cbe8d1b state=1 ===
  Port  1: name='SFP+ 1 (Empty)'             up=False speed=10000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=False stp='disabled'   sfp=//
  Port  2: name='esxi03 - vmnic5 (10G)'      up=True  speed=10000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=False stp='forwarding' sfp=DAC-SFP10-1M/Ubiquiti Inc./AH24112009036
  Port  3: name='SFP+ 3 (Empty)'             up=False speed=0     autoneg=False op_mode='switch'   lag_idx=None  agg_by=False stp='disabled'   sfp=//
  Port  4: name='SFP+ 4 (Empty)'             up=False speed=0     autoneg=False op_mode='switch'   lag_idx=None  agg_by=False stp='disabled'   sfp=//
  Port  5: name='SFP+ 5 (Empty)'             up=False speed=0     autoneg=False op_mode='switch'   lag_idx=None  agg_by=False stp='disabled'   sfp=//
  Port  6: name='esxi03 - vmnic4 (10G)'      up=True  speed=10000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=False stp='forwarding' sfp=SFP-10G-DAC-005/OEM/2506030096
  Port  7: name='To USAGGPRO Port 20 (LAG)'  up=True  speed=10000 autoneg=False op_mode='aggregate' lag_idx=1     agg_by=False stp='forwarding' sfp=SFP-H10GB-CU3M/OEM/CSS11OA1104
  Port  8: name='SFP+ 8'                     up=True  speed=10000 autoneg=False op_mode='switch'   lag_idx=None  agg_by=7     stp='forwarding' sfp=SFP-H10GB-CU3M/OEM/CSS11OA1110
```
