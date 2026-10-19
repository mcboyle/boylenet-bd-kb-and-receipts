# AGENT-11: Post-Boot SR-IOV VF Instantiation, 4-Node Tesla T4 GPU RoCE Mesh & Satellite AI Verification Receipts

**Generated At:** `2026-10-17T20:15:00Z`  
**Engineer:** Agent-11 (`a33ab452-f036-4742-8625-4de0c667d05f`)  
**Contract:** Strict Anti-Sycophancy & Zero-Hallucination Contract (Raw CLI Output + Exit Codes on Every Claim)

---

## Executive Summary

1. **Hardware SR-IOV Virtual Functions (`nmlx5_core` `max_vfs=4,4`) Verified LIVE on `esxi01` and `esxi03`:**
   - Following the post-maintenance reboots of **`esxi01`** (`10.0.20.41`) and **`esxi03`** (`10.0.20.43`), the pre-staged `nmlx5_core` module parameter (`max_vfs=4,4`) has instantiated **8 PCIe SR-IOV Virtual Functions per host (16 total VFs live across `esxi01` and `esxi03`)**:
     - **`esxi01` (`10.0.20.41`):** `vmnic6` (`0000:05:00.0`, 10G, MTU 9000) -> VFs `0000:05:00.2..5` (`numVFs=4, sriovActive=True`); `vmnic7` (`0000:05:00.1`, 25G, MTU 9000) -> VFs `0000:05:01.2..5` (`numVFs=4, sriovActive=True`).
     - **`esxi03` (`10.0.20.43`):** `vmnic10` (`0000:b0:00.0`, 25G, MTU 9000) -> VFs `0000:b0:00.2..5` (`numVFs=4, sriovActive=True`); `vmnic11` (`0000:b0:00.1`, 10G, MTU 9000) -> VFs `0000:b0:01.2..5` (`numVFs=4, sriovActive=True`).
     - **`esxi02` (`10.0.20.42`) & `esxi04` (`10.0.20.44`):** `max_vfs=4,4` is pre-staged (`sriovEnabled=True, sriovActive=False, numVFs=0`), ready to instantiate on their next scheduled host reboot.

2. **DVS `25GB` Uplink Re-Pinning for `AI Inference` (`esxi01` `DVPortID 321` -> `vmnic7` 25G):**
   - After `esxi01` rebooted, `vmnic7` (`0000:05:00.1`, `Uplink 6` / Port 29) negotiated at **25,000 Mbps Full Duplex (MTU 9000)** while `vmnic6` (`0000:05:00.0`, `Uplink 1`) negotiated at 10,000 Mbps.
   - Using `pyVmomi` `ReconfigureDVPort_Task` against `VC04` (`10.0.20.70`), `DVPortID 321` (`AI Inference` on `esxi01`) was updated to `activeUplinkPort = ['Uplink 6']` (`vmnic7` 25G). All 4 Tesla T4 GPU VMs are confirmed pinned to 25Gbps Mellanox uplinks (`vmnic7` on `esxi01`, `vmnic6` on `esxi02`, `vmnic10` on `esxi03`, `vmnic6` on `esxi04`).

3. **Persistent `/etc/netplan/60-gpu-roce.yaml` (`10.0.75.11..14/24`, `mtu: 9000`) & 6-Pair 25GbE Benchmarks:**
   - Created `/etc/netplan/60-gpu-roce.yaml` (`mode 0600`) and executed `netplan apply` (`rc=0`) across all 4 Tesla T4 GPU VMs (`AI DEV` `10.0.75.11/24`, `AI` `10.0.75.12/24`, `llama` `10.0.75.13/24`, `AI Inference` `10.0.75.14/24`) so guest reboots never drop the `10.0.75.0/24` GPU mesh subnet.
   - All 6 pairs pass `ping -c 3 -M do -s 8972` (9000-byte unfragmented jumbo frames) with **0% packet loss** (`0.389–0.512 ms` min RTT) and sustain **20.8–24.1 Gbits/sec** on `iperf3 -P 4`.

4. **All 4 Tesla T4 GPUs & Satellite AI Services Verified Healthy:**
   - All 4 Tesla T4 GPUs (`15,360 MiB` VRAM each, `64 GB` cluster total) are online (`nvidia-smi` `rc=0`, `0` uncorrected ECC errors) with `ollama.service` (`:11434`) and `nvidia-persistenced.service` `active` across `.83`, `.72`, `.228`, `.125`, plus `BattleStation` (`10.0.10.137:11434` HTTP 200, `:9222` CDP HTTP 200, `RTX 2080 Ti` online).

---

## 1. Hardware SR-IOV Virtual Function Verification (`esxi01..04`)

### 1.1 `govc host.esxcli` SR-IOV NIC & Virtual Function Tables (`esxi01` & `esxi03`)

```text
=== ESXI01 (10.0.20.41) SR-IOV CHECK ===
# govc host.esxcli -u 'https://root:Matt99!!@10.0.20.41/sdk' -k system module parameters list -m nmlx5_core
Name     Type           Value  Description
max_vfs  array of uint  4,4    Number of PCI VFs to initialize
   This parameter is ignored if max_vfs=0.
rc=0

# govc host.esxcli -u 'https://root:Matt99!!@10.0.20.41/sdk' -k network sriovnic list
Name     PCI Device    Driver      Link     Speed    Duplex   MAC Address        MTU      Description
----     ----------    ------      ----     -----    ------   -----------        ---      -----------
vmnic6   0000:05:00.0  nmlx5_core  Up       10000    Full     6c:b3:11:79:91:ce  9000     Mellanox Technologies ConnectX-4 Lx EN NIC; 25GbE; dual-port SFP28; PCIe3.0 x8; (MCX4121A-ACU)
vmnic7   0000:05:00.1  nmlx5_core  Up       25000    Full     6c:b3:11:79:91:cf  9000     Mellanox Technologies ConnectX-4 Lx EN NIC; 25GbE; dual-port SFP28; PCIe3.0 x8; (MCX4121A-ACU)
rc=0

# govc host.esxcli -u 'https://root:Matt99!!@10.0.20.41/sdk' -k network sriovnic vf list -n vmnic6
VF ID  Active  Hex PCI Address  Owner World ID
-----  ------  ---------------  --------------
0      false   0000:05:00.2      - 
1      false   0000:05:00.3      - 
2      false   0000:05:00.4      - 
3      false   0000:05:00.5      - 
rc=0

# govc host.esxcli -u 'https://root:Matt99!!@10.0.20.41/sdk' -k network sriovnic vf list -n vmnic7
VF ID  Active  Hex PCI Address  Owner World ID
-----  ------  ---------------  --------------
0      false   0000:05:01.2      - 
1      false   0000:05:01.3      - 
2      false   0000:05:01.4      - 
3      false   0000:05:01.5      - 
rc=0

=== ESXI03 (10.0.20.43) SR-IOV CHECK ===
# govc host.esxcli -u 'https://root:Matt99!!@10.0.20.43/sdk' -k system module parameters list -m nmlx5_core
Name     Type           Value  Description
max_vfs  array of uint  4,4    Number of PCI VFs to initialize
   This parameter is ignored if max_vfs=0.
rc=0

# govc host.esxcli -u 'https://root:Matt99!!@10.0.20.43/sdk' -k network sriovnic list
Name     PCI Device    Driver      Link     Speed    Duplex   MAC Address        MTU      Description
----     ----------    ------      ----     -----    ------   -----------        ---      -----------
vmnic10  0000:b0:00.0  nmlx5_core  Up       25000    Full     6c:b3:11:79:91:0e  9000     Mellanox Technologies ConnectX-4 Lx EN NIC; 25GbE; dual-port SFP28; PCIe3.0 x8; (MCX4121A-ACU)
vmnic11  0000:b0:00.1  nmlx5_core  Up       10000    Full     6c:b3:11:79:91:0f  9000     Mellanox Technologies ConnectX-4 Lx EN NIC; 25GbE; dual-port SFP28; PCIe3.0 x8; (MCX4121A-ACU)
rc=0

# govc host.esxcli -u 'https://root:Matt99!!@10.0.20.43/sdk' -k network sriovnic vf list -n vmnic10
VF ID  Active  Hex PCI Address  Owner World ID
-----  ------  ---------------  --------------
0      false   0000:b0:00.2      - 
1      false   0000:b0:00.3      - 
2      false   0000:b0:00.4      - 
3      false   0000:b0:00.5      - 
rc=0

# govc host.esxcli -u 'https://root:Matt99!!@10.0.20.43/sdk' -k network sriovnic vf list -n vmnic11
VF ID  Active  Hex PCI Address  Owner World ID
-----  ------  ---------------  --------------
0      false   0000:b0:01.2      - 
1      false   0000:b0:01.3      - 
2      false   0000:b0:01.4      - 
3      false   0000:b0:01.5      - 
rc=0
```

### 1.2 `pyVmomi` `vim.host.SriovInfo` Verification Across `esxi01..04` (`VC04` `10.0.20.70`, `rc=0`)

```text
HOST: esxi01.boylenet.themfboyles.com
  BDF=0000:05:00.0 pnic=None sriovCapable=True sriovEnabled=True sriovActive=True numVFs=4 maxVFs=8
  BDF=0000:05:00.1 pnic=None sriovCapable=True sriovEnabled=True sriovActive=True numVFs=4 maxVFs=8
  BDF=0000:05:00.2 pnic=None sriovCapable=False sriovEnabled=False sriovActive=False numVFs=0 maxVFs=0
  BDF=0000:05:00.3 pnic=None sriovCapable=False sriovEnabled=False sriovActive=False numVFs=0 maxVFs=0
  BDF=0000:05:00.4 pnic=None sriovCapable=False sriovEnabled=False sriovActive=False numVFs=0 maxVFs=0
  BDF=0000:05:00.5 pnic=None sriovCapable=False sriovEnabled=False sriovActive=False numVFs=0 maxVFs=0
HOST: esxi02.boylenet.themfboyles.com
  BDF=0000:88:00.0 pnic=None sriovCapable=True sriovEnabled=True sriovActive=False numVFs=0 maxVFs=8
  BDF=0000:88:00.1 pnic=None sriovCapable=True sriovEnabled=True sriovActive=False numVFs=0 maxVFs=8
HOST: esxi03.boylenet.themfboyles.com
  BDF=0000:b0:00.0 pnic=None sriovCapable=True sriovEnabled=True sriovActive=True numVFs=4 maxVFs=8
  BDF=0000:b0:00.1 pnic=None sriovCapable=True sriovEnabled=True sriovActive=True numVFs=4 maxVFs=8
  BDF=0000:b0:00.2 pnic=None sriovCapable=False sriovEnabled=False sriovActive=False numVFs=0 maxVFs=0
  BDF=0000:b0:00.3 pnic=None sriovCapable=False sriovEnabled=False sriovActive=False numVFs=0 maxVFs=0
  BDF=0000:b0:00.4 pnic=None sriovCapable=False sriovEnabled=False sriovActive=False numVFs=0 maxVFs=0
  BDF=0000:b0:00.5 pnic=None sriovCapable=False sriovEnabled=False sriovActive=False numVFs=0 maxVFs=0
HOST: esxi04.boylenet.themfboyles.com
  BDF=0000:37:00.0 pnic=None sriovCapable=True sriovEnabled=True sriovActive=False numVFs=0 maxVFs=8
  BDF=0000:37:00.1 pnic=None sriovCapable=True sriovEnabled=True sriovActive=False numVFs=0 maxVFs=8
```

---

## 2. DVS `25GB` Uplink Pinning (`vmnic7` 25G on `esxi01`) & Persistent Netplan (`60-gpu-roce.yaml`)

### 2.1 `ReconfigureDVPort_Task` (`DVPortID 321` -> `Uplink 6` / `vmnic7` 25Gbps on `esxi01`) & 4-Host `network vm port list`

```text
DVS: 25GB 50 03 bc 60 ce b8 7d fc-24 8f 81 c8 d3 3e 82 e4
  Host: esxi01.boylenet.themfboyles.com
    portKey=237 -> Uplink 1 -> pnic=vmnic6 (10000 Full)
    portKey=238 -> Uplink 2 -> pnic=vmnic9
    portKey=239 -> Uplink 3 -> pnic=vmnic8
    portKey=240 -> Uplink 4 -> pnic=vmnic5
    portKey=241 -> Uplink 5 -> pnic=vmnic4
    portKey=242 -> Uplink 6 -> pnic=vmnic7 (25000 Full)

Reconfigured Port 321 (AI Inference on esxi01) -> active=['Uplink 6'] (vmnic7 25G)
=== Verify TeamUplink across all 4 GPU VMs ===
# esxi01 (AI_Inference, World ID 2112836)
DVPortID:      321
MACAddress:    00:50:56:83:2f:fd
Portgroup:     dvportgroup-11
TeamUplink:    vmnic7 (25Gbps Mellanox ConnectX-4 Lx, Port 29)
vSwitch:       25GB

# esxi02 (llama, World ID 4689267)
DVPortID:      313
MACAddress:    00:50:56:83:60:61
Portgroup:     dvportgroup-11
TeamUplink:    vmnic6 (25Gbps Mellanox ConnectX-4 Lx)
vSwitch:       25GB

# esxi03 (AI_DEV, World ID 2114091)
DVPortID:      1
MACAddress:    00:50:56:83:7a:1c
Portgroup:     dvportgroup-11
TeamUplink:    vmnic10 (25Gbps Mellanox ConnectX-4 Lx)
vSwitch:       25GB

# esxi04 (AI, World ID 7305928)
DVPortID:      293
MACAddress:    00:50:56:83:e6:7a
Portgroup:     dvportgroup-11
TeamUplink:    vmnic6 (25Gbps Mellanox ConnectX-5)
vSwitch:       25GB
```

### 2.2 Persistent `/etc/netplan/60-gpu-roce.yaml` (`10.0.75.11..14/24`, `mtu: 9000`) & `ip addr show dev ens33`

```text
--- Configuring /etc/netplan/60-gpu-roce.yaml on AI_DEV (10.0.70.83 -> 10.0.75.11/24, mtu 9000) ---
-rw------- 1 root root 105 Oct 17 20:09 /etc/netplan/60-gpu-roce.yaml
network:
  version: 2
  ethernets:
    ens33:
      mtu: 9000
      addresses:
        - "10.0.75.11/24"
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9000 qdisc mq state UP group default qlen 10000
    link/ether 00:50:56:83:7a:1c brd ff:ff:ff:ff:ff:ff
    altname enp2s1
    inet 10.0.70.83/24 brd 10.0.70.255 scope global ens33
       valid_lft forever preferred_lft forever
    inet 10.0.75.11/24 brd 10.0.75.255 scope global ens33
       valid_lft forever preferred_lft forever
    inet6 fe80::250:56ff:fe83:7a1c/64 scope link 
       valid_lft forever preferred_lft forever
rc=0

--- Configuring /etc/netplan/60-gpu-roce.yaml on AI (10.0.70.72 -> 10.0.75.12/24, mtu 9000) ---
-rw------- 1 root root 105 Oct 17 20:09 /etc/netplan/60-gpu-roce.yaml
network:
  version: 2
  ethernets:
    ens33:
      mtu: 9000
      addresses:
        - "10.0.75.12/24"
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9000 qdisc mq state UP group default qlen 1000
    link/ether 00:50:56:83:e6:7a brd ff:ff:ff:ff:ff:ff
    altname enp2s1
    inet 10.0.70.72/24 brd 10.0.70.255 scope global ens33
       valid_lft forever preferred_lft forever
    inet 10.0.75.12/24 brd 10.0.75.255 scope global ens33
       valid_lft forever preferred_lft forever
    inet6 fe80::250:56ff:fe83:e67a/64 scope link 
       valid_lft forever preferred_lft forever
rc=0

--- Configuring /etc/netplan/60-gpu-roce.yaml on llama (10.0.70.228 -> 10.0.75.13/24, mtu 9000) ---
-rw------- 1 root root 105 Oct 17 20:09 /etc/netplan/60-gpu-roce.yaml
network:
  version: 2
  ethernets:
    ens33:
      mtu: 9000
      addresses:
        - "10.0.75.13/24"
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9000 qdisc mq state UP group default qlen 10000
    link/ether 00:50:56:83:60:61 brd ff:ff:ff:ff:ff:ff
    altname enp2s1
    inet 10.0.70.228/24 brd 10.0.70.255 scope global ens33
       valid_lft forever preferred_lft forever
    inet 10.0.75.13/24 brd 10.0.75.255 scope global ens33
       valid_lft forever preferred_lft forever
    inet6 fe80::250:56ff:fe83:6061/64 scope link 
       valid_lft forever preferred_lft forever
rc=0

--- Configuring /etc/netplan/60-gpu-roce.yaml on AI_Inference (10.0.70.125 -> 10.0.75.14/24, mtu 9000) ---
-rw------- 1 root root 105 Oct 17 20:09 /etc/netplan/60-gpu-roce.yaml
network:
  version: 2
  ethernets:
    ens33:
      mtu: 9000
      addresses:
        - "10.0.75.14/24"
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9000 qdisc mq state UP group default qlen 10000
    link/ether 00:50:56:83:2f:fd brd ff:ff:ff:ff:ff:ff
    altname enp2s1
    inet 10.0.70.125/24 brd 10.0.70.255 scope global ens33
       valid_lft forever preferred_lft forever
    inet 10.0.75.14/24 brd 10.0.75.255 scope global ens33
       valid_lft forever preferred_lft forever
    inet6 fe80::250:56ff:fe83:2ffd/64 scope link 
       valid_lft forever preferred_lft forever
rc=0
```

---

## 3. Post-Boot 6-Pair 25GbE GPU Mesh Benchmarks (`ping -M do -s 8972` & `iperf3 -P 4`)

### 3.1 Summary Benchmark Table (`10.0.75.11..14`, MTU 9000)

| GPU Mesh Pair (`10.0.75.11..14`) | Active 25G Uplinks | Jumbo Ping (`-M do -s 8972`) Loss | Jumbo RTT `min / avg / max` | `iperf3 -P 4` Sender | `iperf3 -P 4` Receiver | Exit Codes |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `esxi01 (.14)` <-> `esxi02 (.13)` | `vmnic7` (25G) <-> `vmnic6` (25G) | `0% packet loss` (3/3) | `0.421 / 0.574 / 0.786 ms` | **`23.9 Gbits/sec`** | **`23.8 Gbits/sec`** | `ping=0, iperf3=0` |
| `esxi01 (.14)` <-> `esxi03 (.11)` | `vmnic7` (25G) <-> `vmnic10` (25G) | `0% packet loss` (3/3) | `0.389 / 0.566 / 0.878 ms` | **`24.1 Gbits/sec`** | **`24.0 Gbits/sec`** | `ping=0, iperf3=0` |
| `esxi01 (.14)` <-> `esxi04 (.12)` | `vmnic7` (25G) <-> `vmnic6` (25G) | `0% packet loss` (3/3) | `0.446 / 0.556 / 0.748 ms` | **`24.0 Gbits/sec`** | **`23.9 Gbits/sec`** | `ping=0, iperf3=0` |
| `esxi02 (.13)` <-> `esxi03 (.11)` | `vmnic6` (25G) <-> `vmnic10` (25G) | `0% packet loss` (3/3) | `0.400 / 0.536 / 0.717 ms` | **`20.8 Gbits/sec`** | **`20.7 Gbits/sec`** | `ping=0, iperf3=0` |
| `esxi02 (.13)` <-> `esxi04 (.12)` | `vmnic6` (25G) <-> `vmnic6` (25G) | `0% packet loss` (3/3) | `0.512 / 0.610 / 0.800 ms` | **`23.3 Gbits/sec`** | **`23.3 Gbits/sec`** | `ping=0, iperf3=0` |
| `esxi03 (.11)` <-> `esxi04 (.12)` | `vmnic10` (25G) <-> `vmnic6` (25G) | `0% packet loss` (3/3) | `0.453 / 0.539 / 0.710 ms` | **`23.3 Gbits/sec`** | **`23.3 Gbits/sec`** | `ping=0, iperf3=0` |

### 3.2 Raw `ping -M do -s 8972` & `iperf3 -P 4 -t 3` Receipts

```text
=== PAIR: esxi01_AI_Inference->esxi02_llama (10.0.75.14 -> 10.0.75.13) ===
PING 10.0.75.13 (10.0.75.13) from 10.0.75.14 : 8972(9000) bytes of data.
8980 bytes from 10.0.75.13: icmp_seq=1 ttl=64 time=0.786 ms
8980 bytes from 10.0.75.13: icmp_seq=2 ttl=64 time=0.515 ms
8980 bytes from 10.0.75.13: icmp_seq=3 ttl=64 time=0.421 ms

--- 10.0.75.13 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2049ms
rtt min/avg/max/mdev = 0.421/0.574/0.786/0.154 ms
ping_rc=0
[ 11]   0.00-3.00   sec  1.98 GBytes  5.67 Gbits/sec    4             sender
[ 11]   0.00-3.00   sec  1.98 GBytes  5.65 Gbits/sec                  receiver
[SUM]   0.00-3.00   sec  8.36 GBytes  23.9 Gbits/sec   11             sender
[SUM]   0.00-3.00   sec  8.34 GBytes  23.8 Gbits/sec                  receiver
iperf3_rc=0

=== PAIR: esxi01_AI_Inference->esxi03_AI_DEV (10.0.75.14 -> 10.0.75.11) ===
PING 10.0.75.11 (10.0.75.11) from 10.0.75.14 : 8972(9000) bytes of data.
8980 bytes from 10.0.75.11: icmp_seq=1 ttl=64 time=0.878 ms
8980 bytes from 10.0.75.11: icmp_seq=2 ttl=64 time=0.389 ms
8980 bytes from 10.0.75.11: icmp_seq=3 ttl=64 time=0.433 ms

--- 10.0.75.11 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2047ms
rtt min/avg/max/mdev = 0.389/0.566/0.878/0.220 ms
ping_rc=0
[ 11]   0.00-3.00   sec  1.80 GBytes  5.15 Gbits/sec  160             sender
[ 11]   0.00-3.00   sec  1.79 GBytes  5.13 Gbits/sec                  receiver
[SUM]   0.00-3.00   sec  8.42 GBytes  24.1 Gbits/sec  800             sender
[SUM]   0.00-3.00   sec  8.39 GBytes  24.0 Gbits/sec                  receiver
iperf3_rc=0

=== PAIR: esxi01_AI_Inference->esxi04_AI (10.0.75.14 -> 10.0.75.12) ===
PING 10.0.75.12 (10.0.75.12) from 10.0.75.14 : 8972(9000) bytes of data.
8980 bytes from 10.0.75.12: icmp_seq=1 ttl=64 time=0.748 ms
8980 bytes from 10.0.75.12: icmp_seq=2 ttl=64 time=0.476 ms
8980 bytes from 10.0.75.12: icmp_seq=3 ttl=64 time=0.446 ms

--- 10.0.75.12 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2065ms
rtt min/avg/max/mdev = 0.446/0.556/0.748/0.135 ms
ping_rc=0
[ 11]   0.00-3.00   sec  1.96 GBytes  5.62 Gbits/sec    0             sender
[ 11]   0.00-3.00   sec  1.96 GBytes  5.61 Gbits/sec                  receiver
[SUM]   0.00-3.00   sec  8.38 GBytes  24.0 Gbits/sec    0             sender
[SUM]   0.00-3.00   sec  8.36 GBytes  23.9 Gbits/sec                  receiver
iperf3_rc=0

=== PAIR: esxi02_llama->esxi03_AI_DEV (10.0.75.13 -> 10.0.75.11) ===
PING 10.0.75.11 (10.0.75.11) from 10.0.75.13 : 8972(9000) bytes of data.
8980 bytes from 10.0.75.11: icmp_seq=1 ttl=64 time=0.717 ms
8980 bytes from 10.0.75.11: icmp_seq=2 ttl=64 time=0.491 ms
8980 bytes from 10.0.75.11: icmp_seq=3 ttl=64 time=0.400 ms

--- 10.0.75.11 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2043ms
rtt min/avg/max/mdev = 0.400/0.536/0.717/0.133 ms
ping_rc=0
[ 11]   0.00-3.00   sec  1.95 GBytes  5.59 Gbits/sec    0             sender
[ 11]   0.00-3.00   sec  1.94 GBytes  5.56 Gbits/sec                  receiver
[SUM]   0.00-3.00   sec  7.27 GBytes  20.8 Gbits/sec    0             sender
[SUM]   0.00-3.00   sec  7.24 GBytes  20.7 Gbits/sec                  receiver
iperf3_rc=0

=== PAIR: esxi02_llama->esxi04_AI (10.0.75.13 -> 10.0.75.12) ===
PING 10.0.75.12 (10.0.75.12) from 10.0.75.13 : 8972(9000) bytes of data.
8980 bytes from 10.0.75.12: icmp_seq=1 ttl=64 time=0.800 ms
8980 bytes from 10.0.75.12: icmp_seq=2 ttl=64 time=0.512 ms
8980 bytes from 10.0.75.12: icmp_seq=3 ttl=64 time=0.520 ms

--- 10.0.75.12 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2050ms
rtt min/avg/max/mdev = 0.512/0.610/0.800/0.133 ms
ping_rc=0
[ 11]   0.00-3.00   sec  1.82 GBytes  5.20 Gbits/sec    0             sender
[ 11]   0.00-3.00   sec  1.81 GBytes  5.19 Gbits/sec                  receiver
[SUM]   0.00-3.00   sec  8.16 GBytes  23.3 Gbits/sec    0             sender
[SUM]   0.00-3.00   sec  8.15 GBytes  23.3 Gbits/sec                  receiver
iperf3_rc=0

=== PAIR: esxi03_AI_DEV->esxi04_AI (10.0.75.11 -> 10.0.75.12) ===
PING 10.0.75.12 (10.0.75.12) from 10.0.75.11 : 8972(9000) bytes of data.
8980 bytes from 10.0.75.12: icmp_seq=1 ttl=64 time=0.710 ms
8980 bytes from 10.0.75.12: icmp_seq=2 ttl=64 time=0.453 ms
8980 bytes from 10.0.75.12: icmp_seq=3 ttl=64 time=0.455 ms

--- 10.0.75.12 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2050ms
rtt min/avg/max/mdev = 0.453/0.539/0.710/0.120 ms
ping_rc=0
[ 11]   0.00-3.00   sec  2.55 GBytes  7.29 Gbits/sec    0             sender
[ 11]   0.00-3.00   sec  2.54 GBytes  7.27 Gbits/sec                  receiver
[SUM]   0.00-3.00   sec  8.15 GBytes  23.3 Gbits/sec    0             sender
[SUM]   0.00-3.00   sec  8.13 GBytes  23.3 Gbits/sec                  receiver
iperf3_rc=0
```

---

## 4. 4-Node Tesla T4 GPU & Satellite AI Service Verification (`.83`, `.72`, `.228`, `.125`, `10.0.10.137`)

### 4.1 Summary Service Matrix

| Node | Primary IP / Mesh IP | GPU Device (`nvidia-smi -L`) | Driver / CUDA | Active Services & Containers | `:11434/api/tags` Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `AI DEV` (`aidev`, `esxi03`) | `10.0.70.83` / `10.0.75.11` | `GPU 0: Tesla T4 (UUID: GPU-ba41b208-e418-2d26-59ee-9cec149e3b31)` | `580.178.04` / `13.0` | `ollama.service`, `nvidia-persistenced.service`, `switcheroo-control.service`, `act_runner@1..16.service` (all `active running`) | HTTP 200 (`hermes3:8b`, etc.) |
| `AI` (`ai-srv01`, `esxi04`) | `10.0.70.72` / `10.0.75.12` | `GPU 0: Tesla T4 (UUID: GPU-e120a9f0-d8a1-0ae2-1d27-39632f15f073)` | `595.91.07` / `13.2` | `ollama.service`, `nvidia-persistenced.service`, `mesh-embeddings-proxy.service`, Docker `open-webui` (`:8080`, `Up 2 weeks (healthy)`) | HTTP 200 (`qwen2.5-coder:7b`, etc.) |
| `llama` (`ai-ollama01`, `esxi02`) | `10.0.70.228` / `10.0.75.13` | `GPU 0: Tesla T4 (UUID: GPU-5e21793b-8afc-eef0-7792-dfa7bb0a12b7)` | `615.71.09` / `13.4` | `ollama.service`, `nvidia-persistenced.service`, `switcheroo-control.service` (all `active running`) | HTTP 200 (`qwen2.5-coder:32b`, etc.) |
| `AI Inference` (`ai-infer01`, `esxi01`) | `10.0.70.125` / `10.0.75.14` | `GPU 0: Tesla T4 (UUID: GPU-a7dafc71-7b3a-6a13-4e90-14ef245dfb1b)` | `615.71.09` / `13.4` | `ollama.service`, `nvidia-persistenced.service`, `switcheroo-control.service` (all `active running`) | HTTP 200 (`bge-m3:latest`, etc.) |
| `BattleStation` (Windows, `.10.137`) | `10.0.10.137` | `GPU 0: NVIDIA GeForce RTX 2080 Ti (UUID: GPU-5ca3c5f2-8f07-7150-c079-336f760ece89)` | Windows WDDM | Ollama (`:11434` HTTP 200), Chrome CDP Oracle (`:9222` HTTP 200) | HTTP 200 (`nomic-embed-text-cpu:latest`, etc.) |

### 4.2 Raw `nvidia-smi` & Service Verification Outputs

```text
--- AI_DEV (10.0.70.83 / 10.0.75.11) ---
aidev
 20:12:40 up 9 min,  2 users,  load average: 0.00, 0.12, 0.13
GPU 0: Tesla T4 (UUID: GPU-ba41b208-e418-2d26-59ee-9cec149e3b31)
Sat Oct 17 20:12:40 2026       
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 580.178.04             Driver Version: 580.178.04     CUDA Version: 13.0     |
+-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  Tesla T4                       On  |   00000000:03:00.0 Off |                    0 |
| N/A   49C    P8             10W /   70W |       0MiB /  15360MiB |      0%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+
LISTEN 0      4096               *:11434            *:*    users:(("ollama",pid=1880,fd=4))
  act_runner@1..16.service    loaded active running Gitea Actions runner 1..16
  nvidia-persistenced.service loaded active running NVIDIA Persistence Daemon
  ollama.service              loaded active running Ollama Service
  switcheroo-control.service  loaded active running Switcheroo Control Proxy service
rc=0

--- AI (10.0.70.72 / 10.0.75.12) ---
ai-srv01.mesh.local
 20:12:41 up 16 days, 15:45,  2 users,  load average: 0.01, 0.03, 0.00
GPU 0: Tesla T4 (UUID: GPU-e120a9f0-d8a1-0ae2-1d27-39632f15f073)
Sat Oct 17 20:12:41 2026       
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 595.91.07              Driver Version: 595.91.07      CUDA Version: 13.2     |
+-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  Tesla T4                       Off |   00000000:03:00.0 Off |                    0 |
| N/A   71C    P0             31W /   70W |    9213MiB /  15360MiB |      0%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+
LISTEN 0      512        127.0.0.1:43457      0.0.0.0:*    users:(("llama-server",pid=1131018,fd=38))
LISTEN 0      512        127.0.0.1:32981      0.0.0.0:*    users:(("llama-server",pid=1129084,fd=38))
LISTEN 0      128          0.0.0.0:5555       0.0.0.0:*    users:(("python",pid=1540,fd=5))
LISTEN 0      2048         0.0.0.0:8080       0.0.0.0:*    users:(("python3",pid=1941,fd=25))
LISTEN 0      4096               *:11434            *:*    users:(("ollama",pid=41381,fd=4))
  mesh-embeddings-proxy.service loaded active running Cluster Mesh TEI Embeddings Port 8081 Proxy
  nvidia-persistenced.service   loaded active running NVIDIA Persistence Daemon
  ollama.service                loaded active running Ollama Service
NAMES          IMAGE                                                      STATUS
open-webui     ghcr.io/open-webui/open-webui:main                         Up 2 weeks (healthy)
rc=0

--- llama (10.0.70.228 / 10.0.75.13) ---
ai-ollama01.mesh.local
 20:12:42 up 3 min,  2 users,  load average: 0.16, 0.10, 0.04
GPU 0: Tesla T4 (UUID: GPU-5e21793b-8afc-eef0-7792-dfa7bb0a12b7)
Sat Oct 17 20:12:42 2026       
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 615.71.09              KMD Version: 615.71.09     CUDA UMD Version: 13.4     |
+-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  Tesla T4                       On  |   00000000:03:00.0 Off |                    0 |
| N/A   50C    P8             11W /   70W |       0MiB /  15360MiB |      0%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+
LISTEN 0      4096               *:11434            *:*    users:(("ollama",pid=1493,fd=4))
  nvidia-persistenced.service loaded active running NVIDIA Persistence Daemon
  ollama.service              loaded active running Ollama Service
  switcheroo-control.service  loaded active running Switcheroo Control Proxy service
rc=0

--- AI_Inference (10.0.70.125 / 10.0.75.14) ---
ai-infer01.mesh.local
 20:12:43 up  1:11,  2 users,  load average: 0.02, 0.01, 0.00
GPU 0: Tesla T4 (UUID: GPU-a7dafc71-7b3a-6a13-4e90-14ef245dfb1b)
Sat Oct 17 20:12:43 2026       
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 615.71.09              KMD Version: 615.71.09     CUDA UMD Version: 13.4     |
+-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  Tesla T4                       On  |   00000000:03:00.0 Off |                    0 |
| N/A   42C    P8             11W /   70W |       0MiB /  15360MiB |      0%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+
LISTEN 0      4096               *:11434            *:*    users:(("ollama",pid=1176,fd=4))
  nvidia-persistenced.service   loaded active running NVIDIA Persistence Daemon
  ollama.service                loaded active running Ollama Service
  switcheroo-control.service    loaded active running Switcheroo Control Proxy service
rc=0

--- BattleStation (10.0.10.137) ---
GPU 0: NVIDIA GeForce RTX 2080 Ti (UUID: GPU-5ca3c5f2-8f07-7150-c079-336f760ece89)
10.0.10.137:11434 -> HTTP 200 ({"models":[{"name":"nomic-embed-text-cpu:latest",...}]})
10.0.10.137:9222  -> HTTP 200
rc=0
```
