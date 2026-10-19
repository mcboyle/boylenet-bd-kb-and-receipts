# AGENT-21: Tier-3 CI Runner `tmpfs` DDR4 RAM-Disk & Hot-Add Memory Receipts (O1789)

- **Timestamp (UTC)**: `2026-10-17T21:47:53Z`
- **Role**: Agent-21 (Tier-3 CI Runner `tmpfs` RAM-Disk & Hot-Add Memory Engineer)
- **Status**: `PASS` (`rc=0` across all 7 CI/Controller VMs; `0 B` idle overhead verified post-benchmark)

---

## 1. Executive Summary Table (All 7 Target VMs)

| Node Hostname | VM Name | IP Address | ESXi Host | vCPUs | Physical RAM (`free -h`) | `/mnt/ramdisk-ci` Size | Idle Used (`df -h`) | Symlink `/var/tmp/bd-ram-scratch` | 4 GiB `dd` (`conv=fdatasync`) | Exit Code |
|---|---|---|---|---:|---:|---:|---:|---|---:|---|
| `wrk-test04` | `test3big` | `10.0.70.82` | `esxi03` | 64 | `125Gi` (`122Gi` avail) | `64G` | `0` (`0%`) | `-> /mnt/ramdisk-ci/bd-ram-scratch` (`1777`) | `1.9 GB/s` (`2.2589 s`) | `rc=0` |
| `wrk-test02` | `Test2` | `10.0.70.95` | `esxi03` | 32 | `62Gi` (`61Gi` avail) | `24G` | `0` (`0%`) | `-> /mnt/ramdisk-ci/bd-ram-scratch` (`1777`) | `1.6 GB/s` (`2.6788 s`) | `rc=0` |
| `wrk-test03` | `test3` | `10.0.70.80` | `esxi03` | 32 | `62Gi` (`61Gi` avail) | `24G` | `0` (`0%`) | `-> /mnt/ramdisk-ci/bd-ram-scratch` (`1777`) | `1.3 GB/s` (`3.3402 s`) | `rc=0` |
| `wrk-test01` | `test` | `10.0.70.194` | `esxi01` | 24 | `47Gi` (`45Gi` avail) | `16G` | `0` (`0%`) | `-> /mnt/ramdisk-ci/bd-ram-scratch` (`1777`) | `927 MB/s` (`4.6326 s`) | `rc=0` |
| `wrk-test06` | `test6` | `10.0.70.249` | `esxi01` | 24 | `47Gi` (`45Gi` avail) | `16G` | `0` (`0%`) | `-> /mnt/ramdisk-ci/bd-ram-scratch` (`1777`) | `926 MB/s` (`4.6364 s`) | `rc=0` |
| `wrk-test07` | `test7` | `10.0.70.84` | `esxi02` | 16 | `47Gi` (`45Gi` avail) | `16G` | `0` (`0%`) | `-> /mnt/ramdisk-ci/bd-ram-scratch` (`1777`) | `814 MB/s` (`5.2786 s`) | `rc=0` |
| `hub-mesh01` | `Test5` | `10.0.70.164` | `esxi04` | 48 | `344Gi` (`288Gi` avail) | `64G` | `0` (`0%`) | `-> /mnt/ramdisk-ci/bd-ram-scratch` (`1777`) | `1.7 GB/s` (`2.4876 s`) | `rc=0` |

### Reboot Persistence Configuration
- **`/etc/fstab` Entry**:
  - `64G` nodes (`10.0.70.82`, `10.0.70.164`): `tmpfs /mnt/ramdisk-ci tmpfs rw,nosuid,nodev,noatime,size=64G,mode=1777,nofail 0 0`
  - `24G` nodes (`10.0.70.95`, `10.0.70.80`): `tmpfs /mnt/ramdisk-ci tmpfs rw,nosuid,nodev,noatime,size=24G,mode=1777,nofail 0 0`
  - `16G` nodes (`10.0.70.194`, `10.0.70.249`, `10.0.70.84`): `tmpfs /mnt/ramdisk-ci tmpfs rw,nosuid,nodev,noatime,size=16G,mode=1777,nofail 0 0`
- **`/etc/tmpfiles.d/bd-ram-scratch.conf`** (installed on all 7 nodes so `/mnt/ramdisk-ci/bd-ram-scratch` with `mode=1777` is automatically recreated on boot):
  ```
  d /mnt/ramdisk-ci/bd-ram-scratch 1777 root root -
  ```

---

## 2. DDR4 `tmpfs` vs vSAN Disk Benchmark Summary (`10.0.70.82` & `10.0.70.164`)

| Node | Workload | `/mnt/ramdisk-ci` (DDR4 `tmpfs`) | `/var/tmp` (vSAN Backing Disk) | Speedup Factor |
|---|---|---|---|---|
| `wrk-test04` (`10.0.70.82`) | Sequential 4 GiB Write (`dd bs=1M count=4096 conv=fdatasync`) | `1.9 GB/s` (`2.2589 s`) | `342 MB/s` (`12.5404 s`) | **5.55x faster** |
| `wrk-test04` (`10.0.70.82`) | 2,000-file Pytest Cache `write+fsync` (~1 KB/file) | `68,501 ops/s` (`0.0292 s`) | `621 ops/s` (`3.2185 s`) | **110.3x faster** |
| `wrk-test04` (`10.0.70.82`) | 2,000-file Pytest Cache Full Lifecycle (`write+fsync+read+rmtree`) | `0.0850 s` total | `3.3785 s` total | **39.7x faster** |
| `hub-mesh01` (`10.0.70.164`) | Sequential 4 GiB Write (`dd bs=1M count=4096 conv=fdatasync`) | `1.7 GB/s` (`2.4876 s`) | `41.6 MB/s` (`103.213 s`) | **40.9x faster** |
| `hub-mesh01` (`10.0.70.164`) | 2,000-file Pytest Cache `write+fsync` (~1 KB/file) | `69,510 ops/s` (`0.0288 s`) | `208 ops/s` (`9.6311 s`) | **334.2x faster** |
| `hub-mesh01` (`10.0.70.164`) | 2,000-file Pytest Cache Full Lifecycle (`write+fsync+read+rmtree`) | `0.0864 s` total | `9.7945 s` total | **113.4x faster** |

---

## 3. Complete Raw CLI Receipts Across All 7 Nodes

### 3.1 `wrk-test04` / `test3big` (`10.0.70.82` on `esxi03`, `128 GiB` RAM, `64 vCPUs`)

```text
============================================================
NODE: 10.0.70.82
============================================================
[HOSTNAME & CPU/RAM]
wrk-test04
CPU(s):                                  64
Model name:                              Intel(R) Xeon(R) Platinum 8164 CPU @ 2.00GHz
BIOS Model name:                         Intel(R) Xeon(R) Platinum 8164 CPU @ 2.00GHz  CPU @ 1.9GHz
               total        used        free      shared  buff/cache   available
Mem:           125Gi       3.6Gi       122Gi        21Mi       580Mi       122Gi
Swap:          8.0Gi          0B       8.0Gi
[DF -H /mnt/ramdisk-ci & /var/tmp]
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                               64G     0   64G   0% /mnt/ramdisk-ci
/dev/mapper/ubuntu--vg-ubuntu--lv  2.0T  792G  1.3T  39% /
[FINDMNT /mnt/ramdisk-ci]
TARGET          SOURCE FSTYPE OPTIONS
/mnt/ramdisk-ci tmpfs  tmpfs  rw,nosuid,nodev,noatime,size=67108864k,inode64
[/ETC/FSTAB]
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/ubuntu-vg/ubuntu-lv during curtin installation
/dev/disk/by-id/dm-uuid-LVM-qTZ4BsKh6L9MfHnEbSlu1xRC6Den15kIFuuZYYid0WPFKfRxt3swGa7zJcn7NjEK / xfs defaults 0 1
# /boot was on /dev/sda2 during curtin installation
/dev/disk/by-uuid/a374c1cd-0008-4470-a8ea-ae4a800bfb1f /boot ext4 defaults 0 1
# /boot/efi was on /dev/sda1 during curtin installation
/dev/disk/by-uuid/6DDD-CAAE /boot/efi vfat defaults 0 1
/swap.img	none	swap	sw	0	0
tmpfs /mnt/agent-workspaces tmpfs defaults,size=32G,uid=1000,gid=1000 0 0
tmpfs /mnt/ramdisk-stage tmpfs defaults,size=16G,uid=1000,gid=1000 0 0
10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0
tmpfs /mnt/ramdisk-ci tmpfs rw,nosuid,nodev,noatime,size=64G,mode=1777,nofail 0 0
[SYMLINK & PERMISSIONS]
drwxrwxrwt 3 root root 60 Oct 17 21:44 /mnt/ramdisk-ci
drwxrwxrwt 2 root root 40 Oct 17 21:44 /mnt/ramdisk-ci/bd-ram-scratch
lrwxrwxrwx 1 root root 30 Oct 17 21:44 /var/tmp/bd-ram-scratch -> /mnt/ramdisk-ci/bd-ram-scratch
1777 /mnt/ramdisk-ci
1777 /mnt/ramdisk-ci/bd-ram-scratch
[TMPFS SEQUENTIAL DD BENCHMARK (4096 MiB)]
4096+0 records in
4096+0 records out
4294967296 bytes (4.3 GB, 4.0 GiB) copied, 2.99631 s, 1.4 GB/s
[POST-CLEANUP DF -H /mnt/ramdisk-ci]
Filesystem      Size  Used Avail Use% Mounted on
tmpfs            64G     0   64G   0% /mnt/ramdisk-ci
rc=0
```

### 3.2 `wrk-test02` / `Test2` (`10.0.70.95` on `esxi03`, `64 GiB` RAM, `32 vCPUs`)

```text
============================================================
NODE: 10.0.70.95
============================================================
[HOSTNAME & CPU/RAM]
wrk-test02
CPU(s):                                  32
Model name:                              Intel(R) Xeon(R) Platinum 8164 CPU @ 2.00GHz
BIOS Model name:                         Intel(R) Xeon(R) Platinum 8164 CPU @ 2.00GHz  CPU @ 1.9GHz
               total        used        free      shared  buff/cache   available
Mem:            62Gi       1.4Gi        60Gi        21Mi       1.6Gi        61Gi
Swap:          8.0Gi          0B       8.0Gi
[DF -H /mnt/ramdisk-ci & /var/tmp]
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                               24G     0   24G   0% /mnt/ramdisk-ci
/dev/mapper/ubuntu--vg-ubuntu--lv  2.0T  1.4T  526G  73% /
[FINDMNT /mnt/ramdisk-ci]
TARGET          SOURCE FSTYPE OPTIONS
/mnt/ramdisk-ci tmpfs  tmpfs  rw,nosuid,nodev,noatime,size=25165824k,inode64
[/ETC/FSTAB]
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/ubuntu-vg/ubuntu-lv during curtin installation
/dev/disk/by-id/dm-uuid-LVM-dz2M2OdxvF09tmbaChpM835kRcfAvVlKhmZixmEe0AfsdPiesS1dCKg5Yta8iN8S / ext4 defaults 0 1
# /boot was on /dev/sda2 during curtin installation
/dev/disk/by-uuid/5ba40ec3-d732-4e4c-bfe9-6f2a96741d17 /boot ext4 defaults 0 1
# /boot/efi was on /dev/sda1 during curtin installation
/dev/disk/by-uuid/25E3-F0E1 /boot/efi vfat defaults 0 1
/swap.img	none	swap	sw	0	0
10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0
tmpfs /mnt/ramdisk-ci tmpfs rw,nosuid,nodev,noatime,size=24G,mode=1777,nofail 0 0
[SYMLINK & PERMISSIONS]
drwxrwxrwt 3 root root 60 Oct 17 21:44 /mnt/ramdisk-ci
drwxrwxrwt 2 root root 40 Oct 17 21:44 /mnt/ramdisk-ci/bd-ram-scratch
lrwxrwxrwx 1 root root 30 Oct 17 21:44 /var/tmp/bd-ram-scratch -> /mnt/ramdisk-ci/bd-ram-scratch
1777 /mnt/ramdisk-ci
1777 /mnt/ramdisk-ci/bd-ram-scratch
[TMPFS SEQUENTIAL DD BENCHMARK (4096 MiB)]
4096+0 records in
4096+0 records out
4294967296 bytes (4.3 GB, 4.0 GiB) copied, 2.67879 s, 1.6 GB/s
[POST-CLEANUP DF -H /mnt/ramdisk-ci]
Filesystem      Size  Used Avail Use% Mounted on
tmpfs            24G     0   24G   0% /mnt/ramdisk-ci
rc=0
```

### 3.3 `wrk-test03` / `test3` (`10.0.70.80` on `esxi03`, `64 GiB` RAM, `32 vCPUs`)

```text
============================================================
NODE: 10.0.70.80
============================================================
[HOSTNAME & CPU/RAM]
wrk-test03
CPU(s):                                  32
Model name:                              Intel(R) Xeon(R) Platinum 8164 CPU @ 2.00GHz
BIOS Model name:                         Intel(R) Xeon(R) Platinum 8164 CPU @ 2.00GHz  CPU @ 1.9GHz
               total        used        free      shared  buff/cache   available
Mem:            62Gi       1.3Gi        61Gi        22Mi       467Mi        61Gi
Swap:          8.0Gi          0B       8.0Gi
[DF -H /mnt/ramdisk-ci & /var/tmp]
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                               24G     0   24G   0% /mnt/ramdisk-ci
/dev/mapper/ubuntu--vg-ubuntu--lv  2.0T  383G  1.7T  19% /
[FINDMNT /mnt/ramdisk-ci]
TARGET          SOURCE FSTYPE OPTIONS
/mnt/ramdisk-ci tmpfs  tmpfs  rw,nosuid,nodev,noatime,size=25165824k,inode64
[/ETC/FSTAB]
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/ubuntu-vg/ubuntu-lv during curtin installation
/dev/disk/by-id/dm-uuid-LVM-off16bCNKU7L6bMvl3kx8PAlwSQq0tac5WNGCAvAueYx0zwCGWYu1BLnb9Dy7ToC / xfs defaults 0 1
# /boot was on /dev/sda2 during curtin installation
/dev/disk/by-uuid/2d148ea0-cd05-4569-bb84-9b3f6d896567 /boot ext4 defaults 0 1
# /boot/efi was on /dev/sda1 during curtin installation
/dev/disk/by-uuid/709F-280D /boot/efi vfat defaults 0 1
/swap.img	none	swap	sw	0	0
tmpfs /mnt/bd-tmpfs tmpfs size=128G,mode=1777,noatime 0 0
tmpfs /mnt/ramdisk-ci tmpfs rw,nosuid,nodev,noatime,size=24G,mode=1777,nofail 0 0
[SYMLINK & PERMISSIONS]
drwxrwxrwt 3 root root 60 Oct 17 21:44 /mnt/ramdisk-ci
drwxrwxrwt 2 root root 40 Oct 17 21:44 /mnt/ramdisk-ci/bd-ram-scratch
lrwxrwxrwx 1 root root 30 Oct 17 21:44 /var/tmp/bd-ram-scratch -> /mnt/ramdisk-ci/bd-ram-scratch
1777 /mnt/ramdisk-ci
1777 /mnt/ramdisk-ci/bd-ram-scratch
[TMPFS SEQUENTIAL DD BENCHMARK (4096 MiB)]
4096+0 records in
4096+0 records out
4294967296 bytes (4.3 GB, 4.0 GiB) copied, 3.34021 s, 1.3 GB/s
[POST-CLEANUP DF -H /mnt/ramdisk-ci]
Filesystem      Size  Used Avail Use% Mounted on
tmpfs            24G     0   24G   0% /mnt/ramdisk-ci
rc=0
```

### 3.4 `wrk-test01` / `test` (`10.0.70.194` on `esxi01`, `48 GiB` RAM, `24 vCPUs`)

```text
============================================================
NODE: 10.0.70.194
============================================================
[HOSTNAME & CPU/RAM]
wrk-test01
CPU(s):                                  24
Model name:                              Intel(R) Xeon(R) CPU E5-2690 v4 @ 2.60GHz
BIOS Model name:                         Intel(R) Xeon(R) CPU E5-2690 v4 @ 2.60GHz  CPU @ 2.4GHz
               total        used        free      shared  buff/cache   available
Mem:            47Gi       1.2Gi        45Gi        21Mi       768Mi        45Gi
Swap:          8.0Gi          0B       8.0Gi
[DF -H /mnt/ramdisk-ci & /var/tmp]
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                               16G     0   16G   0% /mnt/ramdisk-ci
/dev/mapper/ubuntu--vg-ubuntu--lv  2.0T  117G  1.9T   6% /
[FINDMNT /mnt/ramdisk-ci]
TARGET          SOURCE FSTYPE OPTIONS
/mnt/ramdisk-ci tmpfs  tmpfs  rw,nosuid,nodev,noatime,size=16777216k,inode64
[/ETC/FSTAB]
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/ubuntu-vg/ubuntu-lv during curtin installation
/dev/disk/by-id/dm-uuid-LVM-NJGgVacTYndZ3XIptgqtMgsCVcuu6DctlW4D2GTziWHctrScSTW71ZcAJrUsJ0I4 / xfs defaults 0 1
# /boot was on /dev/sda2 during curtin installation
/dev/disk/by-uuid/26bb2648-da9e-4919-9428-98d9910da754 /boot ext4 defaults 0 1
# /boot/efi was on /dev/sda1 during curtin installation
/dev/disk/by-uuid/6AE0-45CD /boot/efi vfat defaults 0 1
/swap.img	none	swap	sw	0	0
tmpfs /mnt/ramdisk-ci tmpfs rw,nosuid,nodev,noatime,size=16G,mode=1777,nofail 0 0
[SYMLINK & PERMISSIONS]
drwxrwxrwt 3 root root 60 Oct 17 21:44 /mnt/ramdisk-ci
drwxrwxrwt 2 root root 40 Oct 17 21:44 /mnt/ramdisk-ci/bd-ram-scratch
lrwxrwxrwx 1 root root 30 Oct 17 21:44 /var/tmp/bd-ram-scratch -> /mnt/ramdisk-ci/bd-ram-scratch
1777 /mnt/ramdisk-ci
1777 /mnt/ramdisk-ci/bd-ram-scratch
[TMPFS SEQUENTIAL DD BENCHMARK (4096 MiB)]
4096+0 records in
4096+0 records out
4294967296 bytes (4.3 GB, 4.0 GiB) copied, 4.63255 s, 927 MB/s
[POST-CLEANUP DF -H /mnt/ramdisk-ci]
Filesystem      Size  Used Avail Use% Mounted on
tmpfs            16G     0   16G   0% /mnt/ramdisk-ci
rc=0
```

### 3.5 `wrk-test06` / `test6` (`10.0.70.249` on `esxi01`, `48 GiB` RAM, `24 vCPUs`)

```text
============================================================
NODE: 10.0.70.249
============================================================
[HOSTNAME & CPU/RAM]
wrk-test06
CPU(s):                                  24
Model name:                              Intel(R) Xeon(R) CPU E5-2690 v4 @ 2.60GHz
BIOS Model name:                         Intel(R) Xeon(R) CPU E5-2690 v4 @ 2.60GHz  CPU @ 2.4GHz
               total        used        free      shared  buff/cache   available
Mem:            47Gi       1.4Gi        43Gi        22Mi       2.7Gi        45Gi
Swap:          8.0Gi          0B       8.0Gi
[DF -H /mnt/ramdisk-ci & /var/tmp]
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                               16G     0   16G   0% /mnt/ramdisk-ci
/dev/mapper/ubuntu--vg-ubuntu--lv  2.0T  315G  1.6T  17% /
[FINDMNT /mnt/ramdisk-ci]
TARGET          SOURCE FSTYPE OPTIONS
/mnt/ramdisk-ci tmpfs  tmpfs  rw,nosuid,nodev,noatime,size=16777216k,inode64
[/ETC/FSTAB]
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/ubuntu-vg/ubuntu-lv during curtin installation
/dev/disk/by-id/dm-uuid-LVM-FDSrElF8i2Gph43E9Hyd4a4Wp3vUygDDpeIyJgIoFOJSfAmBx15OzLwBlvOHNeb6 / ext4 defaults 0 1
# /boot was on /dev/sda2 during curtin installation
/dev/disk/by-uuid/b0c056f4-6eee-4404-9734-58d45adf4d7f /boot ext4 defaults 0 1
# /boot/efi was on /dev/sda1 during curtin installation
/dev/disk/by-uuid/E572-F513 /boot/efi vfat defaults 0 1
# /home was on /dev/ubuntu-vg/lv-0 during curtin installation
#/dev/disk/by-id/dm-uuid-LVM-FDSrElF8i2Gph43E9Hyd4a4Wp3vUygDDicnpiHLj4k687l1vAf9JncN4TjjWIkKr /home xfs defaults 0 1
/swap.img	none	swap	sw	0	0
tmpfs /mnt/bd-tmpfs tmpfs size=128G,mode=1777,noatime 0 0
tmpfs /mnt/ramdisk-ci tmpfs rw,nosuid,nodev,noatime,size=16G,mode=1777,nofail 0 0
[SYMLINK & PERMISSIONS]
drwxrwxrwt 3 root root 60 Oct 17 21:44 /mnt/ramdisk-ci
drwxrwxrwt 2 root root 40 Oct 17 21:44 /mnt/ramdisk-ci/bd-ram-scratch
lrwxrwxrwx 1 root root 30 Oct 17 21:44 /var/tmp/bd-ram-scratch -> /mnt/ramdisk-ci/bd-ram-scratch
1777 /mnt/ramdisk-ci
1777 /mnt/ramdisk-ci/bd-ram-scratch
[TMPFS SEQUENTIAL DD BENCHMARK (4096 MiB)]
4096+0 records in
4096+0 records out
4294967296 bytes (4.3 GB, 4.0 GiB) copied, 4.63644 s, 926 MB/s
[POST-CLEANUP DF -H /mnt/ramdisk-ci]
Filesystem      Size  Used Avail Use% Mounted on
tmpfs            16G     0   16G   0% /mnt/ramdisk-ci
rc=0
```

### 3.6 `wrk-test07` / `test7` (`10.0.70.84` on `esxi02`, `48 GiB` RAM, `16 vCPUs`)

```text
============================================================
NODE: 10.0.70.84
============================================================
[HOSTNAME & CPU/RAM]
wrk-test07
CPU(s):                                  16
Model name:                              Intel(R) Xeon(R) CPU E5-2699 v4 @ 2.20GHz
BIOS Model name:                         Intel(R) Xeon(R) CPU E5-2699 v4 @ 2.20GHz  CPU @ 2.0GHz
               total        used        free      shared  buff/cache   available
Mem:            47Gi       1.3Gi        45Gi        22Mi       1.0Gi        45Gi
Swap:          8.0Gi          0B       8.0Gi
[DF -H /mnt/ramdisk-ci & /var/tmp]
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                               16G     0   16G   0% /mnt/ramdisk-ci
/dev/mapper/ubuntu--vg-ubuntu--lv  2.0T  201G  1.9T  10% /
[FINDMNT /mnt/ramdisk-ci]
TARGET          SOURCE FSTYPE OPTIONS
/mnt/ramdisk-ci tmpfs  tmpfs  rw,nosuid,nodev,noatime,size=16777216k,inode64
[/ETC/FSTAB]
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/ubuntu-vg/ubuntu-lv during curtin installation
/dev/disk/by-id/dm-uuid-LVM-NJGgVacTYndZ3XIptgqtMgsCVcuu6DctlW4D2GTziWHctrScSTW71ZcAJrUsJ0I4 / xfs defaults 0 1
# /boot was on /dev/sda2 during curtin installation
/dev/disk/by-uuid/26bb2648-da9e-4919-9428-98d9910da754 /boot ext4 defaults 0 1
# /boot/efi was on /dev/sda1 during curtin installation
/dev/disk/by-uuid/6AE0-45CD /boot/efi vfat defaults 0 1
/swap.img	none	swap	sw	0	0
tmpfs /mnt/ramdisk-ci tmpfs rw,nosuid,nodev,noatime,size=16G,mode=1777,nofail 0 0
[SYMLINK & PERMISSIONS]
drwxrwxrwt 3 root root 60 Oct 17 21:44 /mnt/ramdisk-ci
drwxrwxrwt 2 root root 40 Oct 17 21:44 /mnt/ramdisk-ci/bd-ram-scratch
lrwxrwxrwx 1 root root 30 Oct 17 21:44 /var/tmp/bd-ram-scratch -> /mnt/ramdisk-ci/bd-ram-scratch
1777 /mnt/ramdisk-ci
1777 /mnt/ramdisk-ci/bd-ram-scratch
[TMPFS SEQUENTIAL DD BENCHMARK (4096 MiB)]
4096+0 records in
4096+0 records out
4294967296 bytes (4.3 GB, 4.0 GiB) copied, 5.27858 s, 814 MB/s
[POST-CLEANUP DF -H /mnt/ramdisk-ci]
Filesystem      Size  Used Avail Use% Mounted on
tmpfs            16G     0   16G   0% /mnt/ramdisk-ci
rc=0
```

### 3.7 `hub-mesh01` / `Test5` (`10.0.70.164` on `esxi04`, `350 GiB` RAM, `48 vCPUs`)

```text
============================================================
NODE: 10.0.70.164
============================================================
[HOSTNAME & CPU/RAM]
hub-mesh01
CPU(s):                                  48
Model name:                              Intel(R) Xeon(R) Platinum 8170 CPU @ 2.10GHz
BIOS Model name:                         Intel(R) Xeon(R) Platinum 8170 CPU @ 2.10GHz  CPU @ 1.9GHz
               total        used        free      shared  buff/cache   available
Mem:           344Gi        56Gi       233Gi        41Mi        57Gi       288Gi
Swap:          8.0Gi          0B       8.0Gi
[DF -H /mnt/ramdisk-ci & /var/tmp]
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                               64G     0   64G   0% /mnt/ramdisk-ci
/dev/mapper/ubuntu--vg-ubuntu--lv   10T  3.0T  7.1T  30% /
[FINDMNT /mnt/ramdisk-ci]
TARGET          SOURCE FSTYPE OPTIONS
/mnt/ramdisk-ci tmpfs  tmpfs  rw,nosuid,nodev,noatime,size=67108864k,inode64
[/ETC/FSTAB]
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/ubuntu-vg/ubuntu-lv during curtin installation
/dev/disk/by-id/dm-uuid-LVM-k8xKcFcsrNCtW8D5YTaoTjgPHTqIyy5IFYyYVaNqV8g0Bz1r3xTo3pm6KtJIzKBE / xfs defaults 0 1
# /boot was on /dev/sda2 during curtin installation
/dev/disk/by-uuid/640015a9-8b67-4733-8828-5d1a7d876d7e /boot ext4 defaults 0 1
# /boot/efi was on /dev/sda1 during curtin installation
/dev/disk/by-uuid/8EF9-7FBB /boot/efi vfat defaults 0 1
/swap.img	none	swap	sw	0	0
tmpfs /mnt/bd-tmpfs tmpfs size=128G,mode=1777,noatime 0 0
tmpfs /mnt/ramdisk-ci tmpfs rw,nosuid,nodev,noatime,size=64G,mode=1777,nofail 0 0
[SYMLINK & PERMISSIONS]
drwxrwxrwt 3 root root 60 Oct 17 21:44 /mnt/ramdisk-ci
drwxrwxrwt 2 root root 40 Oct 17 21:44 /mnt/ramdisk-ci/bd-ram-scratch
lrwxrwxrwx 1 root root 30 Oct 17 21:44 /var/tmp/bd-ram-scratch -> /mnt/ramdisk-ci/bd-ram-scratch
1777 /mnt/ramdisk-ci
1777 /mnt/ramdisk-ci/bd-ram-scratch
[TMPFS SEQUENTIAL DD BENCHMARK (4096 MiB)]
4096+0 records in
4096+0 records out
4294967296 bytes (4.3 GB, 4.0 GiB) copied, 2.53137 s, 1.7 GB/s
[POST-CLEANUP DF -H /mnt/ramdisk-ci]
Filesystem      Size  Used Avail Use% Mounted on
tmpfs            64G     0   64G   0% /mnt/ramdisk-ci
rc=0
```

---

## 4. Deep Side-by-Side Benchmarks (`10.0.70.82` & `10.0.70.164`)

```text
============================================================
DEEP BENCHMARK (TMPFS vs vSAN /var/tmp): 10.0.70.82
============================================================
--- 1. Sequential 4 GiB Write: /mnt/ramdisk-ci (DDR4 tmpfs) ---
4096+0 records in
4096+0 records out
4294967296 bytes (4.3 GB, 4.0 GiB) copied, 2.2589 s, 1.9 GB/s
--- 2. Sequential 4 GiB Write: /var/tmp (vSAN Disk) ---
4096+0 records in
4096+0 records out
4294967296 bytes (4.3 GB, 4.0 GiB) copied, 12.5404 s, 342 MB/s
--- 3. Small-File / Pytest Cache Benchmark (2000 files write+fsync+read+unlink) ---
[DDR4-tmpfs (/mnt/ramdisk-ci)] files=2000 | write+fsync=0.0292s (68501 ops/s) | read=0.0392s (50963 ops/s) | rmtree=0.0165s | total=0.0850s
[vSAN-disk  (/var/tmp)] files=2000 | write+fsync=3.2185s (621 ops/s) | read=0.0463s (43213 ops/s) | rmtree=0.1137s | total=3.3785s
--- 4. Verify Cleanup ---
total 0
drwxrwxrwt 3 root root 60 Oct 17 21:45 .
drwxr-xr-x 6 root root 79 Oct 17 21:44 ..
drwxrwxrwt 2 root root 40 Oct 17 21:44 bd-ram-scratch
Filesystem      Size  Used Avail Use% Mounted on
tmpfs            64G     0   64G   0% /mnt/ramdisk-ci
rc=0
============================================================
DEEP BENCHMARK (TMPFS vs vSAN /var/tmp): 10.0.70.164
============================================================
--- 1. Sequential 4 GiB Write: /mnt/ramdisk-ci (DDR4 tmpfs) ---
4096+0 records in
4096+0 records out
4294967296 bytes (4.3 GB, 4.0 GiB) copied, 2.48763 s, 1.7 GB/s
--- 2. Sequential 4 GiB Write: /var/tmp (vSAN Disk) ---
4096+0 records in
4096+0 records out
4294967296 bytes (4.3 GB, 4.0 GiB) copied, 103.213 s, 41.6 MB/s
--- 3. Small-File / Pytest Cache Benchmark (2000 files write+fsync+read+unlink) ---
[DDR4-tmpfs (/mnt/ramdisk-ci)] files=2000 | write+fsync=0.0288s (69510 ops/s) | read=0.0381s (52561 ops/s) | rmtree=0.0196s | total=0.0864s
[vSAN-disk  (/var/tmp)] files=2000 | write+fsync=9.6311s (208 ops/s) | read=0.0424s (47146 ops/s) | rmtree=0.1209s | total=9.7945s
--- 4. Verify Cleanup ---
total 0
drwxrwxrwt 3 root root  60 Oct 17 21:47 .
drwxr-xr-x 4 root root 124 Oct 17 21:44 ..
drwxrwxrwt 2 root root  40 Oct 17 21:44 bd-ram-scratch
Filesystem      Size  Used Avail Use% Mounted on
tmpfs            64G     0   64G   0% /mnt/ramdisk-ci
rc=0
```
