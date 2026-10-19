# O1789 INFRA-UPGRADE: 33-NODE CLUSTER, NFS, GRUB & SERVICE E2E HEALTH RECEIPTS
- **WRITTEN-AT**: `2026-10-17T21:01:26Z`
- **VERIFIER**: Agent-15 (33-Node Cluster, NFS, GRUB & Service E2E Verifier)
- **33-NODE SSH + RW + `/home/mboyle/bd-persist` NFS CENSUS**: `FOUND 33 / 33 HEALTHY (rc=0)`
- **GRUB EFI FALLBACK HARDENING (`10.0.70.82` / `wrk-test04` / `test3big`)**: `PASS (rc=0)`
- **CORE FLEET SERVICES & DUAL-NODE CHROME CDP ORACLES**: `FOUND 14 / 14 HEALTHY (rc=0)`

## 1. Remediations Applied During Verification
1. **`10.0.70.82` (`wrk-test04` / `test3big`) `/home/mboyle/bd-persist` NFS Mount & Persistence**:
   - Backed up stale local directory `/home/mboyle/bd-persist` to `/home/mboyle/bd-persist.local-bak-20261017210002`, created `/mnt/bd/bd-persist`, linked `/home/mboyle/bd-persist -> /mnt/bd/bd-persist` matching `10.0.70.51`.
   - Added `10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0` to `/etc/fstab` and enabled/started `/etc/systemd/system/mnt-bd-bd\x2dpersist.mount`.
2. **`10.0.70.82` (`wrk-test04` / `test3big`) GRUB EFI Hardening (`/boot/efi/EFI/ubuntu/grub.cfg`)**:
   - Hardened `/boot/efi/EFI/ubuntu/grub.cfg` so that if `search.fs_uuid` fails to set `$root` cleanly, `set root=(hd0,gpt2)` is present as an explicit fallback before `set prefix=($root)'/grub'` (plus a post-prefix file check fallback before `configfile $prefix/grub.cfg`), validated with `grub-script-check` (`rc=0`).
3. **`10.0.70.95` (`wrk-test02`) Unprivileged Login & NFS Persistence**:
   - Removed stale `/run/nologin` left behind after boot so unprivileged `mboyle` SSH sessions succeed (`rc=0`).
   - Persisted `10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist` in `/etc/fstab` and enabled `/etc/systemd/system/mnt-bd-bd\x2dpersist.mount` (and persisted `/etc/fstab` entries on `.25`, `.162`, `.107`, `.182`, `.72`, `.125`, `.228`, `.83`, `.50`, `.122`, `.81`, `.184`).
4. **`10.0.70.195` (`spare11`) NFS Read-Write Remount**:
   - Updated `/etc/systemd/system/mnt-bd-bd\x2dpersist.mount` from `Options=ro,...` to `Options=rw,...` and remounted `/mnt/bd/bd-persist` `rw` (`rc=0`).

## 2. 33-Node Linux VM SSH, Local RW (`/tmp/.bd_rw`), and `/home/mboyle/bd-persist` NFS Summary Table

| # | IP | Hostname | SSH + `/tmp/.bd_rw` | `/home/mboyle/bd-persist` Mount Source | Persistence (`systemd` / `/etc/fstab`) | Exit Code |
|---|---|---|---|---|---|---|
| 1 | `10.0.70.164` | `hub-mesh01` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/ /dev/mapper/ubuntu--vg-ubuntu--lv xfs rw,relatime,attr2,inode64,logbufs=8,logbsize=32k,noquota` | `LOCAL_EXPORT_ROOT` | `0` |
| 2 | `10.0.70.25` | `ops-mcp01` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.25,local_lock=none,addr=10.0.70.164` | `10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0` | `0` |
| 3 | `10.0.70.162` | `ops-cicd01` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.162,local_lock=none,addr=10.0.70.164` | `10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0` | `0` |
| 4 | `10.0.70.107` | `ops-test01` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.107,local_lock=none,addr=10.0.70.164` | `10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0` | `0` |
| 5 | `10.0.70.182` | `stg-cache01` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.182,local_lock=none,addr=10.0.70.164` | `10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0` | `0` |
| 6 | `10.0.70.72` | `ai-srv01` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.72,local_lock=none,addr=10.0.70.164` | `10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0` | `0` |
| 7 | `10.0.70.125` | `ai-infer01` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.125,local_lock=none,addr=10.0.70.164` | `10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0` | `0` |
| 8 | `10.0.70.228` | `ai-ollama01` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.228,local_lock=none,addr=10.0.70.164` | `10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0` | `0` |
| 9 | `10.0.70.83` | `aidev` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.83,local_lock=none,addr=10.0.70.164` | `10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0` | `0` |
| 10 | `10.0.70.50` | `wrk-bd01` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.50,local_lock=none,addr=10.0.70.164` | `10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0` | `0` |
| 11 | `10.0.70.51` | `wrk-bd02` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.51,local_lock=none,addr=10.0.70.164` | `enabled` | `0` |
| 12 | `10.0.70.52` | `wrk-bd03` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.52,local_lock=none,addr=10.0.70.164` | `enabled` | `0` |
| 13 | `10.0.70.53` | `wrk-bd04` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.53,local_lock=none,addr=10.0.70.164` | `enabled` | `0` |
| 14 | `10.0.70.54` | `wrk-bd05` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.54,local_lock=none,addr=10.0.70.164` | `enabled` | `0` |
| 15 | `10.0.70.194` | `wrk-test01` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.194,local_lock=none,addr=10.0.70.164` | `enabled` | `0` |
| 16 | `10.0.70.95` | `wrk-test02` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.95,local_lock=none,addr=10.0.70.164` | `enabled` | `0` |
| 17 | `10.0.70.80` | `wrk-test03` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.80,local_lock=none,addr=10.0.70.164` | `enabled` | `0` |
| 18 | `10.0.70.82` | `wrk-test04` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.82,local_lock=none,addr=10.0.70.164` | `enabled` | `0` |
| 19 | `10.0.70.85` | `wrk-test05` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.85,local_lock=none,addr=10.0.70.164` | `enabled` | `0` |
| 20 | `10.0.70.249` | `wrk-test06` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.249,local_lock=none,addr=10.0.70.164` | `enabled` | `0` |
| 21 | `10.0.70.84` | `wrk-test07` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.84,local_lock=none,addr=10.0.70.164` | `enabled` | `0` |
| 22 | `10.0.70.197` | `spare1` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.197,local_lock=none,addr=10.0.70.164` | `enabled` | `0` |
| 23 | `10.0.70.193` | `node1` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.193,local_lock=none,addr=10.0.70.164` | `enabled` | `0` |
| 24 | `10.0.70.122` | `spare3` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.122,local_lock=none,addr=10.0.70.164` | `10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0` | `0` |
| 25 | `10.0.70.81` | `spare4` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.81,local_lock=none,addr=10.0.70.164` | `10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0` | `0` |
| 26 | `10.0.70.185` | `spare5` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.185,local_lock=none,addr=10.0.70.164` | `enabled` | `0` |
| 27 | `10.0.70.186` | `spare6` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.186,local_lock=none,addr=10.0.70.164` | `enabled` | `0` |
| 28 | `10.0.70.120` | `spr-pool01` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.120,local_lock=none,addr=10.0.70.164` | `enabled` | `0` |
| 29 | `10.0.70.184` | `spare8` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.184,local_lock=none,addr=10.0.70.164` | `10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0` | `0` |
| 30 | `10.0.70.187` | `node2` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.187,local_lock=none,addr=10.0.70.164` | `enabled` | `0` |
| 31 | `10.0.70.196` | `spare10` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.196,local_lock=none,addr=10.0.70.164` | `enabled` | `0` |
| 32 | `10.0.70.195` | `spare11` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.195,local_lock=none,addr=10.0.70.164` | `enabled` | `0` |
| 33 | `10.0.70.183` | `spr-pool02` | `LOCAL_RW_OK` + `BD_PERSIST_RW_OK` | `/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.183,local_lock=none,addr=10.0.70.164` | `enabled` | `0` |

## 3. Raw CLI Receipts Across All 33 Linux VMs

### Node 1: `10.0.70.164` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.164 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_164 && rm /home/mboyle/bd-persist/.probe_164 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
hub-mesh01
LOCAL_RW_OK
drwxrwxr-x 78 mboyle mboyle 602112 Oct 17 21:01 /home/mboyle/bd-persist
/ /dev/mapper/ubuntu--vg-ubuntu--lv xfs rw,relatime,attr2,inode64,logbufs=8,logbsize=32k,noquota
BD_PERSIST_RW_OK
not-found
LOCAL_EXPORT_ROOT
EXIT_CODE=0
```

### Node 2: `10.0.70.25` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.25 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_25 && rm /home/mboyle/bd-persist/.probe_25 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
ops-mcp01
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct 17 20:18 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.25,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
not-found
10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0
EXIT_CODE=0
```

### Node 3: `10.0.70.162` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.162 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_162 && rm /home/mboyle/bd-persist/.probe_162 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
ops-cicd01
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct 17 20:18 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.162,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
not-found
10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0
EXIT_CODE=0
```

### Node 4: `10.0.70.107` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.107 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_107 && rm /home/mboyle/bd-persist/.probe_107 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
ops-test01
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct 17 20:18 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.107,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
not-found
10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0
EXIT_CODE=0
```

### Node 5: `10.0.70.182` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.182 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_182 && rm /home/mboyle/bd-persist/.probe_182 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
stg-cache01
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct 17 20:19 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.182,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
not-found
10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0
EXIT_CODE=0
```

### Node 6: `10.0.70.72` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.72 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_72 && rm /home/mboyle/bd-persist/.probe_72 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
ai-srv01
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct 17 20:19 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.72,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
not-found
10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0
EXIT_CODE=0
```

### Node 7: `10.0.70.125` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.125 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_125 && rm /home/mboyle/bd-persist/.probe_125 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
ai-infer01
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct 17 20:20 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.125,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
not-found
10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0
EXIT_CODE=0
```

### Node 8: `10.0.70.228` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.228 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_228 && rm /home/mboyle/bd-persist/.probe_228 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
ai-ollama01
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct 17 20:20 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.228,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
not-found
10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0
EXIT_CODE=0
```

### Node 9: `10.0.70.83` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.83 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_83 && rm /home/mboyle/bd-persist/.probe_83 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
aidev
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct 17 20:21 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.83,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
not-found
10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0
EXIT_CODE=0
```

### Node 10: `10.0.70.50` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.50 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_50 && rm /home/mboyle/bd-persist/.probe_50 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
wrk-bd01
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct 17 16:22 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.50,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
not-found
10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0
EXIT_CODE=0
```

### Node 11: `10.0.70.51` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.51 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_51 && rm /home/mboyle/bd-persist/.probe_51 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
wrk-bd02
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct 12 22:22 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.51,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
enabled
EXIT_CODE=0
```

### Node 12: `10.0.70.52` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.52 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_52 && rm /home/mboyle/bd-persist/.probe_52 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
wrk-bd03
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct  3 13:08 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.52,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
enabled
EXIT_CODE=0
```

### Node 13: `10.0.70.53` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.53 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_53 && rm /home/mboyle/bd-persist/.probe_53 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
wrk-bd04
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct  3 14:13 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.53,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
enabled
EXIT_CODE=0
```

### Node 14: `10.0.70.54` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.54 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_54 && rm /home/mboyle/bd-persist/.probe_54 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
wrk-bd05
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct 12 22:22 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.54,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
enabled
EXIT_CODE=0
```

### Node 15: `10.0.70.194` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.194 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_194 && rm /home/mboyle/bd-persist/.probe_194 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
wrk-test01
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct 12 22:22 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.194,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
enabled
EXIT_CODE=0
```

### Node 16: `10.0.70.95` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.95 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_95 && rm /home/mboyle/bd-persist/.probe_95 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
wrk-test02
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct 17 20:29 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.95,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
enabled
EXIT_CODE=0
```

### Node 17: `10.0.70.80` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.80 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_80 && rm /home/mboyle/bd-persist/.probe_80 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
wrk-test03
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct  3 17:05 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.80,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
enabled
EXIT_CODE=0
```

### Node 18: `10.0.70.82` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.82 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_82 && rm /home/mboyle/bd-persist/.probe_82 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
wrk-test04
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct 17 21:00 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.82,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
enabled
EXIT_CODE=0
```

### Node 19: `10.0.70.85` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.85 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_85 && rm /home/mboyle/bd-persist/.probe_85 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
wrk-test05
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct 12 22:22 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.85,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
enabled
EXIT_CODE=0
```

### Node 20: `10.0.70.249` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.249 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_249 && rm /home/mboyle/bd-persist/.probe_249 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
wrk-test06
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct  3 18:11 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.249,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
enabled
EXIT_CODE=0
```

### Node 21: `10.0.70.84` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.84 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_84 && rm /home/mboyle/bd-persist/.probe_84 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
wrk-test07
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct 17 20:12 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.84,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
enabled
EXIT_CODE=0
```

### Node 22: `10.0.70.197` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.197 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_197 && rm /home/mboyle/bd-persist/.probe_197 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
spare1
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct  8 15:52 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.197,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
enabled
EXIT_CODE=0
```

### Node 23: `10.0.70.193` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.193 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_193 && rm /home/mboyle/bd-persist/.probe_193 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
node1
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct 12 22:22 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.193,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
enabled
EXIT_CODE=0
```

### Node 24: `10.0.70.122` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.122 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_122 && rm /home/mboyle/bd-persist/.probe_122 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
spare3
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct 17 20:22 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.122,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
not-found
10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0
EXIT_CODE=0
```

### Node 25: `10.0.70.81` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.81 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_81 && rm /home/mboyle/bd-persist/.probe_81 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
spare4
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct 17 20:13 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.81,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
not-found
10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0
EXIT_CODE=0
```

### Node 26: `10.0.70.185` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.185 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_185 && rm /home/mboyle/bd-persist/.probe_185 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
spare5
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct  8 15:53 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.185,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
enabled
EXIT_CODE=0
```

### Node 27: `10.0.70.186` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.186 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_186 && rm /home/mboyle/bd-persist/.probe_186 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
spare6
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct 17 20:13 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.186,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
enabled
EXIT_CODE=0
```

### Node 28: `10.0.70.120` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.120 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_120 && rm /home/mboyle/bd-persist/.probe_120 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
spr-pool01
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct 17 20:13 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.120,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
enabled
EXIT_CODE=0
```

### Node 29: `10.0.70.184` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.184 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_184 && rm /home/mboyle/bd-persist/.probe_184 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
spare8
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct 17 20:15 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.184,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
not-found
10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0
EXIT_CODE=0
```

### Node 30: `10.0.70.187` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.187 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_187 && rm /home/mboyle/bd-persist/.probe_187 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
node2
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct  8 15:53 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.187,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
enabled
EXIT_CODE=0
```

### Node 31: `10.0.70.196` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.196 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_196 && rm /home/mboyle/bd-persist/.probe_196 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
spare10
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct  8 15:53 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.196,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
enabled
EXIT_CODE=0
```

### Node 32: `10.0.70.195` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.195 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_195 && rm /home/mboyle/bd-persist/.probe_195 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
spare11
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct  3 13:37 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.195,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
enabled
EXIT_CODE=0
```

### Node 33: `10.0.70.183` (`rc=0`)
```text
$ ssh -o BatchMode=yes -o ConnectTimeout=8 10.0.70.183 hostname && touch /tmp/.bd_rw && rm /tmp/.bd_rw && echo LOCAL_RW_OK && ls -ld /home/mboyle/bd-persist && findmnt -rn -T /home/mboyle/bd-persist && test -f /home/mboyle/bd-persist/FLEET_RULE-FLOOR.md && touch /home/mboyle/bd-persist/.probe_183 && rm /home/mboyle/bd-persist/.probe_183 && echo BD_PERSIST_RW_OK && (systemctl is-enabled 'mnt-bd-bd\x2dpersist.mount' 2>/dev/null || grep 'bd-persist' /etc/fstab || echo LOCAL_EXPORT_ROOT)
spr-pool02
LOCAL_RW_OK
lrwxrwxrwx 1 mboyle mboyle 18 Oct  3 09:01 /home/mboyle/bd-persist -> /mnt/bd/bd-persist
/mnt/bd/bd-persist 10.0.70.164:/home/mboyle/bd-persist nfs4 rw,noatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.0.70.183,local_lock=none,addr=10.0.70.164
BD_PERSIST_RW_OK
enabled
EXIT_CODE=0
```

## 4. `10.0.70.82` (`wrk-test04` / `test3big`) GRUB EFI & NFS Mount Raw Receipts
```text
$ ssh -o BatchMode=yes 10.0.70.82 'sudo cat /boot/efi/EFI/ubuntu/grub.cfg && sudo grub-script-check -v /boot/efi/EFI/ubuntu/grub.cfg && echo GRUB_CHECK_RC=$? && lsblk -f && grep bd-persist /etc/fstab && systemctl status "mnt-bd-bd\\x2dpersist.mount" --no-pager'
search.fs_uuid a374c1cd-0008-4470-a8ea-ae4a800bfb1f root hd0,gpt2
if [ -z "$root" ]; then
  set root=(hd0,gpt2)
  set root=hd0,gpt2
fi
set prefix=($root)'/grub'
if [ ! -f $prefix/grub.cfg ]; then
  set root=(hd0,gpt2)
  set prefix=(hd0,gpt2)/grub
fi
configfile $prefix/grub.cfg
search.fs_uuid a374c1cd-0008-4470-a8ea-ae4a800bfb1f root hd0,gpt2
if [ -z "$root" ]; then
  set root=(hd0,gpt2)
  set root=hd0,gpt2
fi
set prefix=($root)'/grub'
if [ ! -f $prefix/grub.cfg ]; then
  set root=(hd0,gpt2)
  set prefix=(hd0,gpt2)/grub
fi
configfile $prefix/grub.cfg
GRUB_CHECK_RC=0
NAME                      FSTYPE      FSVER    LABEL UUID                                   FSAVAIL FSUSE% MOUNTPOINTS
sda                                                                                                        
├─sda1                    vfat        FAT32          6DDD-CAAE                                   1G     1% /boot/efi
├─sda2                    ext4        1.0            a374c1cd-0008-4470-a8ea-ae4a800bfb1f      1.6G    10% /boot
└─sda3                    LVM2_member LVM2 001       cfPSex-EWyw-pUWc-xpDz-hwhE-l5WF-m3Ctwc                
  └─ubuntu--vg-ubuntu--lv xfs                        ffdcc791-8693-437c-bbfd-1b70c06a6ae6      1.2T    39% /
sdb                                                                                                        
sr0                                                                                                        
10.0.70.164:/home/mboyle/bd-persist /mnt/bd/bd-persist nfs4 rw,hard,proto=tcp,vers=4.2,_netdev,noatime 0 0
● mnt-bd-bd\x2dpersist.mount - bd fleet shared fs (O1666) /mnt/bd/bd-persist from 10.0.70.164
     Loaded: loaded (/etc/systemd/system/mnt-bd-bd\x2dpersist.mount; enabled; preset: enabled)
     Active: active (mounted) since Sat 2026-10-17 21:00:10 UTC; 1min 16s ago
      Where: /mnt/bd/bd-persist
       What: 10.0.70.164:/home/mboyle/bd-persist
      Tasks: 0 (limit: 154544)
     Memory: 168.0K (peak: 1.3M)
        CPU: 9ms
     CGroup: /system.slice/mnt-bd-bd\x2dpersist.mount

Oct 17 21:00:09 wrk-test04 systemd[1]: Mounting mnt-bd-bd\x2dpersist.mount - bd fleet shared fs (O1666) /mnt/bd/bd-persist from 10.0.70.164...
Oct 17 21:00:10 wrk-test04 systemd[1]: Mounted mnt-bd-bd\x2dpersist.mount - bd fleet shared fs (O1666) /mnt/bd/bd-persist from 10.0.70.164.
EXIT_CODE=0
```

## 5. Core Fleet Services & Dual-Node Chrome CDP Oracles Raw Receipts

### vCenter (https://10.0.20.70/ui/) (`rc=0`)
```text
$ curl -sk -i --max-time 8 https://10.0.20.70/ui/ | head -n 12
HTTP/2 200 
content-security-policy: script-src 'self' 'unsafe-inline' 'unsafe-eval' *.vmware-aws.com *.vmware.com; style-src 'self' 'unsafe-inline'; object-src 'self'; media-src 'self' 'unsafe-inline'; frame-ancestors 'self';
referrer-policy: no-referrer
strict-transport-security: max-age=31536000;includeSubDomains
x-frame-options: deny
x-content-type-options: nosniff
set-cookie: VSPHERE-UI-JSESSIONID=8D736501C6404631EF7E09786C1DE6FB; Path=/ui; Secure; HttpOnly; SameSite=Lax
cache-control: no-store, no-cache, must-revalidate, max-age=0
pragma: no-cache
expires: 0
vary: Origin,Access-Control-Request-Method,Access-Control-Request-Headers, Accept-Encoding
last-modified: Fri, 20 Sep 2024 14:40:42 GMT
EXIT_CODE=0
```

### Gitea (10.0.70.162:3000) (`rc=0`)
```text
$ curl -s -i --max-time 8 http://10.0.70.162:3000/api/v1/version
HTTP/1.1 200 OK
Cache-Control: max-age=0, private, must-revalidate
Content-Type: application/json;charset=utf-8
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Date: Sat, 17 Oct 2026 21:01:27 GMT
Content-Length: 20

{"version":"1.27.3"}
EXIT_CODE=0
```

### Kafka (10.0.70.25:9092) (`rc=0`)
```text
$ nc -zv -w 5 10.0.70.25 9092 2>&1
Connection to 10.0.70.25 9092 port [tcp/*] succeeded!
EXIT_CODE=0
```

### Postgres (10.0.70.164:5432) (`rc=0`)
```text
$ pg_isready -h 10.0.70.164 -p 5432
10.0.70.164:5432 - accepting connections
EXIT_CODE=0
```

### bd-mcp (10.0.70.164:8100) (`rc=0`)
```text
$ curl -s -i --max-time 8 -H 'Accept: application/json, text/event-stream' http://10.0.70.164:8100/mcp
HTTP/1.1 400 Bad Request
date: Sat, 17 Oct 2026 21:01:27 GMT
server: uvicorn
content-type: application/json
mcp-session-id: 517f145fe27248649ca925b399527350
content-length: 105

{"jsonrpc":"2.0","id":"server-error","error":{"code":-32600,"message":"Bad Request: Missing session ID"}}
EXIT_CODE=0
```

### bd-rag (10.0.70.164:8099) (`rc=0`)
```text
$ curl -s -i --max-time 8 http://10.0.70.164:8099/health
HTTP/1.0 200 OK
Server: BaseHTTP/0.6 Python/3.12.3
Date: Sat, 17 Oct 2026 21:01:27 GMT
Content-Type: application/json
Content-Length: 191

{"commit": "189ef7d6a171f901450828f83df8773204bd8bde", "built_utc": "2026-10-04T05:09:02Z", "corpus_hash": "3b76a32da5ff1450d0278b0283b1f80bbe44b1f49cb7b07a39488753f2bb9e3b", "chunks": 18828}
EXIT_CODE=0
```

### Cockpit (10.0.70.164:3002) (`rc=0`)
```text
$ curl -s -i --max-time 8 http://10.0.70.164:3002/ | head -n 12
HTTP/1.1 200 OK
Vary: RSC, Next-Router-State-Tree, Next-Router-Prefetch, Accept-Encoding
x-nextjs-cache: HIT
X-Powered-By: Next.js
Cache-Control: s-maxage=31536000, stale-while-revalidate
ETag: "rn6bvqb3d6ztw"
Content-Type: text/html; charset=utf-8
Content-Length: 46853
Date: Sat, 17 Oct 2026 21:01:27 GMT
Connection: keep-alive
Keep-Alive: timeout=5
EXIT_CODE=0
```

### Chat (10.0.70.164:3003) (`rc=0`)
```text
$ curl -s -i --max-time 8 http://10.0.70.164:3003/ | head -n 12
HTTP/1.1 200 OK
Vary: RSC, Next-Router-State-Tree, Next-Router-Prefetch, Accept-Encoding
x-nextjs-cache: HIT
X-Powered-By: Next.js
Cache-Control: s-maxage=31536000, stale-while-revalidate
ETag: "cyhb4tpxjg12e9"
Content-Type: text/html; charset=utf-8
Content-Length: 49923
Date: Sat, 17 Oct 2026 21:01:27 GMT
Connection: keep-alive
Keep-Alive: timeout=5
EXIT_CODE=0
```

### OpenWebUI (10.0.70.164:8090) (`rc=0`)
```text
$ curl -s -i --max-time 8 http://10.0.70.164:8090/ | head -n 12
HTTP/1.1 200 OK
date: Sat, 17 Oct 2026 21:01:26 GMT
server: uvicorn
content-type: text/html; charset=utf-8
accept-ranges: bytes
content-length: 11673
last-modified: Fri, 16 Oct 2026 11:49:25 GMT
etag: "c66a2d811f8d791e62e862f751bf38ae"
cache-control: no-cache
vary: Accept-Encoding, Origin
x-process-time: 0.003827
EXIT_CODE=0
```

### LiteLLM (10.0.70.228:4000) (`rc=0`)
```text
$ curl -s -i --max-time 8 http://10.0.70.228:4000/health/liveliness
HTTP/1.1 200 OK
date: Sat, 17 Oct 2026 21:01:27 GMT
content-length: 12
content-type: application/json
x-frame-options: DENY
content-security-policy: frame-ancestors 'none'
x-content-type-options: nosniff

"I'm alive!"
EXIT_CODE=0
```

### Ollama ai-ollama01 (10.0.70.228:11434) (`rc=0`)
```text
$ curl -s -i --max-time 8 http://10.0.70.228:11434/api/version
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Date: Sat, 17 Oct 2026 21:01:27 GMT
Content-Length: 20

{"version":"0.34.0"}
EXIT_CODE=0
```

### Ollama ai-infer01 (10.0.70.125:11434) (`rc=0`)
```text
$ curl -s -i --max-time 8 http://10.0.70.125:11434/api/version
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Date: Sat, 17 Oct 2026 21:01:27 GMT
Content-Length: 20

{"version":"0.34.0"}
EXIT_CODE=0
```

### BattleStation CDP (10.0.10.137:9222/json/version) (`rc=0`)
```text
$ curl -s -i --max-time 8 http://10.0.10.137:9222/json/version
HTTP/1.1 200 OK
Content-Security-Policy:frame-ancestors 'none'
Content-Length:423
Content-Type:application/json; charset=UTF-8

{
   "Browser": "Chrome/155.0.8059.41",
   "Protocol-Version": "1.3",
   "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/155.0.0.0 Safari/537.36",
   "V8-Version": "15.5.35.20",
   "WebKit-Version": "537.36 (@96e02f81f8f3430db10a322d20b2b13e8a3288e9)",
   "webSocketDebuggerUrl": "ws://10.0.10.137:9222/devtools/browser/67bdf502-9b16-48f2-886c-2490777e867d"
}
EXIT_CODE=0
```

### Bittorrent CDP (10.0.70.181:9222/json/version) (`rc=0`)
```text
$ curl -s -i --max-time 8 http://10.0.70.181:9222/json/version
HTTP/1.1 200 OK
Content-Security-Policy:frame-ancestors 'none'
Content-Length:423
Content-Type:application/json; charset=UTF-8

{
   "Browser": "Chrome/155.0.8059.80",
   "Protocol-Version": "1.3",
   "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/155.0.0.0 Safari/537.36",
   "V8-Version": "15.5.35.23",
   "WebKit-Version": "537.36 (@43a195105613ac83c21d3f14de279f4a045c7da0)",
   "webSocketDebuggerUrl": "ws://10.0.70.181:9222/devtools/browser/56574a15-fe5b-4fa6-91f2-faa3b8d41490"
}
EXIT_CODE=0
```

