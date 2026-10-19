# O1789 Agent-55 Receipt: `/home/mboyle/bin/bd-restore-paused-vms`

- **WRITTEN-AT**: `2026-10-17T23:35:15Z`
- **AGENT**: `Agent-55`
- **TOOL PATH**: `/home/mboyle/bin/bd-restore-paused-vms` (`-rwxrwxr-x`)
- **TOOL SHA256**: `e5516be125225771042e72fe3a5f11c339eaece57790df21accb38569b426cd7`
- **MANIFEST SOURCE**: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/temporarily_powered_off_vms.json`
- **PAUSED VM COUNT**: `27 VMs` (`1,676.0 GiB` RAM / `1,716,224 MB`, `540 vCPUs`)
- **GPU VM LOCKED RAM GUARD**: `1,730 GiB` across `esxi01..04` (`ai-ollama01` 440 GiB, `ai-infer01` 410 GiB, `ai-infer02` 440 GiB, `wrk-test01` 440 GiB)

---

## 1. Verification: `bd-restore-paused-vms --list` (`rc=0`)

```text
O1789 Paused VMs Manifest (27 VMs | Source: /home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/temporarily_powered_off_vms.json)
Created: 2026-10-17T21:49:32.421864+00:00 | GPU VMs Locked RAM: 1,730 GiB
----------------------------------------------------------------------------------------------------------
 #  VM Name                       MOID        ESXi Host                         RAM (GiB)  vCPUs  State     
----------------------------------------------------------------------------------------------------------
 1  bd-ci-r1-20261003             vm-296032   esxi01.boylenet.themfboyles.com        48.0     16  poweredOff
 2  bd-ci-r2-20261003             vm-296033   esxi01.boylenet.themfboyles.com        48.0     16  poweredOff
 3  bd-seat-c1-20261003           vm-296030   esxi01.boylenet.themfboyles.com        48.0     16  poweredOff
 4  bd-seat-c2-20261003           vm-296031   esxi01.boylenet.themfboyles.com        48.0     16  poweredOff
 5  bd3                           vm-289019   esxi01.boylenet.themfboyles.com        64.0     32  poweredOff
 6  clm-infer-01                  vm-296051   esxi01.boylenet.themfboyles.com        48.0     16  poweredOff
 7  spare6                        vm-289036   esxi01.boylenet.themfboyles.com        48.0     24  poweredOff
 8  Test                          vm-289014   esxi02.boylenet.themfboyles.com        64.0     32  poweredOff
 9  bd                            vm-289017   esxi02.boylenet.themfboyles.com        32.0     16  poweredOff
10  bd4                           vm-289020   esxi02.boylenet.themfboyles.com        64.0     32  poweredOff
11  kvcache-cx1-20261014          vm-296077   esxi02.boylenet.themfboyles.com        32.0     16  poweredOff
12  lens-r3c-cx4-20261014         vm-296074   esxi02.boylenet.themfboyles.com        48.0     16  poweredOff
13  spare2                        vm-289032   esxi02.boylenet.themfboyles.com        32.0     16  poweredOff
14  spare7                        vm-289037   esxi02.boylenet.themfboyles.com        32.0     32  poweredOff
15  spare8                        vm-289038   esxi02.boylenet.themfboyles.com        96.0     32  poweredOff
16  Fitness                       vm-289021   esxi03.boylenet.themfboyles.com         8.0      8  poweredOff
17  Plex%2fJellyFin               vm-179004   esxi03.boylenet.themfboyles.com       500.0     24  poweredOff
18  Windows test box              vm-184021   esxi03.boylenet.themfboyles.com        64.0     16  poweredOff
19  spare3                        vm-289033   esxi03.boylenet.themfboyles.com        32.0     16  poweredOff
20  spare4                        vm-289034   esxi03.boylenet.themfboyles.com        32.0     32  poweredOff
21  spare5                        vm-289035   esxi03.boylenet.themfboyles.com        48.0     24  poweredOff
22  Spare1                        vm-289030   esxi04.boylenet.themfboyles.com        48.0     16  poweredOff
23  Spare11                       vm-289041   esxi04.boylenet.themfboyles.com        32.0     32  poweredOff
24  spare                         vm-289031   esxi04.boylenet.themfboyles.com        32.0     16  poweredOff
25  spare10                       vm-289040   esxi04.boylenet.themfboyles.com        32.0     16  poweredOff
26  spare12                       vm-289042   esxi04.boylenet.themfboyles.com        32.0     16  poweredOff
27  spare9                        vm-289039   esxi04.boylenet.themfboyles.com        64.0     32  poweredOff
----------------------------------------------------------------------------------------------------------
TOTAL: 27 VMs | 1,676.0 GiB RAM (1,716,224 MB) | 576 vCPUs

Per-Host Reclaimed Summary:
  - esxi01.boylenet.themfboyles.com :  7 VMs paused |  352.0 GiB RAM reclaimed | 136 vCPUs | Active GPU VM: ai-ollama01 (440 GiB locked)
  - esxi02.boylenet.themfboyles.com :  8 VMs paused |  400.0 GiB RAM reclaimed | 192 vCPUs | Active GPU VM: ai-infer01 (410 GiB locked)
  - esxi03.boylenet.themfboyles.com :  6 VMs paused |  684.0 GiB RAM reclaimed | 120 vCPUs | Active GPU VM: ai-infer02 (440 GiB locked)
  - esxi04.boylenet.themfboyles.com :  6 VMs paused |  240.0 GiB RAM reclaimed | 128 vCPUs | Active GPU VM: wrk-test01 (440 GiB locked)
```

---

## 2. Verification: `bd-restore-paused-vms --status` (`rc=0`)

```text
=== O1789 Paused VMs & ESXi Host Memory Status ===
Manifest File          : /home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/temporarily_powered_off_vms.json
Paused Non-Essential VMs: 27 VMs (1,676.0 GiB RAM, 576 vCPUs)
4 GPU VMs Locked RAM   : 1,730.0 GiB (memoryReservationLockedToMax=true)
Cluster Physical RAM   : 2,048.0 GiB across esxi01..04 (4 x 512 GiB DDR4)
--------------------------------------------------------------------------------------------------
ESXi Host                         Phys GiB  GPU VM (Locked)         Ess+OS GiB  Paused VMs  Paused GiB
--------------------------------------------------------------------------------------------------
esxi01.boylenet.themfboyles.com      512.0  ai-ollama01 (440 GiB)         72.0           7       352.0
esxi02.boylenet.themfboyles.com      512.0  ai-infer01 (410 GiB)         102.0           8       400.0
esxi03.boylenet.themfboyles.com      512.0  ai-infer02 (440 GiB)          72.0           6       684.0
esxi04.boylenet.themfboyles.com      512.0  wrk-test01 (440 GiB)          72.0           6       240.0
--------------------------------------------------------------------------------------------------
STATUS: All 27 non-essential VMs remain recorded as poweredOff while 1,730 GiB RAM is locked to the 4 GPU VMs.
```

---

## 3. Verification: `bd-restore-paused-vms --power-on bd-ci-r1-20261003 --dry-run` (Safety Check Guard)

```text
WARNING: SAFETY CHECK FAILED — Powering on 'bd-ci-r1-20261003' (48.0 GiB RAM, 16 vCPUs) on esxi01.boylenet.themfboyles.com would exceed ESXi host physical RAM while the 4 GPU VMs have 1,730 GiB locked!
  Host Physical RAM       : 512.0 GiB (esxi01.boylenet.themfboyles.com)
  Host Locked GPU VM      : ai-ollama01 = 440.0 GiB locked (Cluster total: 1,730 GiB locked)
  Essential VMs + ESXi OS : ~72.0 GiB committed/reserved
  Requested VM RAM        : +48.0 GiB (bd-ci-r1-20261003 / vm-296032)
  Projected Host Demand   : 560.0 GiB > 512.0 GiB physical RAM (Headroom: 0.0 GiB)
ABORTED: Refusing to power on VM while 1,730 GiB RAM is locked by the 4 GPU VMs. Reclaim/downsize GPU VM locked reservations first or pass --force to override.
```
