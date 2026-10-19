# AGENT-63 RECEIPT: Qwythos-27B-MTP-Q8_0.gguf Download on 10.0.70.72

- **Timestamp**: 2026-10-17T23:41:07Z
- **Host**: `10.0.70.72`
- **Target Path**: `/opt/models/qwythos-27b/Qwythos-27B-MTP-Q8_0.gguf` (27.05 GiB)
- **URL**: `https://huggingface.co/empero-ai/Qwythos-27B-v1-GGUF/resolve/main/Qwythos-27B-MTP-Q8_0.gguf`
- **Command**: `nohup aria2c -x 16 -s 16 -k 10M --continue=true --file-allocation=none -d /opt/models/qwythos-27b -o Qwythos-27B-MTP-Q8_0.gguf https://huggingface.co/empero-ai/Qwythos-27B-v1-GGUF/resolve/main/Qwythos-27B-MTP-Q8_0.gguf`
- **Status**: ACTIVE / VERIFIED (`rc=0`)

## Verification Output
```
UID          PID    PPID  C STIME TTY          TIME CMD
mboyle    109914  109913  0 23:41 ?        00:00:00 aria2c -x 16 -s 16 -k 10M --continue=true --file-allocation=none -d /opt/models/qwythos-27b -o Qwythos-27B-MTP-Q8_0.gguf https://huggingface.co/empero-ai/Qwythos-27B-v1-GGUF/resolve/main/Qwythos-27B-MTP-Q8_0.gguf
-rw-rw-r-- 1 mboyle mboyle 0 Oct 17 23:41 /opt/models/qwythos-27b/Qwythos-27B-MTP-Q8_0.gguf
-rw-rw-r-- 1 mboyle mboyle 0 Oct 17 23:41 /opt/models/qwythos-27b/Qwythos-27B-MTP-Q8_0.gguf
VERIFIED_PID=109914 rc=0
```
