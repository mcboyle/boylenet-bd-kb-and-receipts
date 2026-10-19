# AGENT-61 RECEIPT: Qwen3.8-Flash-Next-UD-IQ4_XS Shard Download on 10.0.70.72

- **WRITTEN-AT**: 2026-10-17T23:40:21Z
- **SEAT**: Agent-61
- **HOST**: `10.0.70.72`
- **TARGET-DIR**: `/opt/models/qwen3.8-flash-next/UD-IQ4_XS/`
- **SHARDS**: `Qwen3.8-Flash-Next-UD-IQ4_XS-00001-of-00003.gguf` .. `00003-of-00003.gguf`
- **COMMAND**: `nohup aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none -d /opt/models/qwen3.8-flash-next/UD-IQ4_XS -i /opt/models/qwen3.8-flash-next/UD-IQ4_XS/shards.txt`
- **STATUS**: ACTIVE (`rc=0`)

## Verification Evidence
```text
PID=109625
===PS===
mboyle    109625  109620 12 23:40 ?        00:00:00 aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none -d /opt/models/qwen3.8-flash-next/UD-IQ4_XS -i /opt/models/qwen3.8-flash-next/UD-IQ4_XS/shards.txt
===LS===
total 81M
-rw-rw-r-- 1 mboyle mboyle  21K Oct 17 23:40 aria2c.log
-rw-rw-r-- 1 mboyle mboyle    7 Oct 17 23:40 aria2c.pid
-rw-rw-r-- 1 mboyle mboyle 2.0M Oct 17 23:40 Qwen3.8-Flash-Next-UD-IQ4_XS-00001-of-00003.gguf
-rw-rw-r-- 1 mboyle mboyle   60 Oct 17 23:40 Qwen3.8-Flash-Next-UD-IQ4_XS-00001-of-00003.gguf.aria2
-rw-rw-r-- 1 mboyle mboyle  41G Oct 17 23:40 Qwen3.8-Flash-Next-UD-IQ4_XS-00002-of-00003.gguf
-rw-rw-r-- 1 mboyle mboyle 5.9K Oct 17 23:40 Qwen3.8-Flash-Next-UD-IQ4_XS-00002-of-00003.gguf.aria2
-rw-rw-r-- 1 mboyle mboyle  36G Oct 17 23:40 Qwen3.8-Flash-Next-UD-IQ4_XS-00003-of-00003.gguf
-rw-rw-r-- 1 mboyle mboyle 5.2K Oct 17 23:40 Qwen3.8-Flash-Next-UD-IQ4_XS-00003-of-00003.gguf.aria2
-rw-rw-r-- 1 mboyle mboyle  633 Oct 17 23:40 shards.txt
===RC=0===
```
