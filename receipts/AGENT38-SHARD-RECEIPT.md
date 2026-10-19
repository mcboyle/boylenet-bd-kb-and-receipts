# AGENT-38 SHARD DOWNLOAD RECEIPT (O1789)

- **WRITTEN-AT**: 2026-10-17T23:35:02Z
- **AGENT**: Agent-38
- **TARGET HOST**: 10.0.70.72
- **TARGET DIR**: `/opt/models/qwen3.8-flash-next/BF16/`
- **SHARDS**:
  - `Qwen3.8-Flash-Next-BF16-00003-of-00008.gguf` (PID: `106031`)
  - `Qwen3.8-Flash-Next-BF16-00004-of-00008.gguf` (PID: `106539`)
- **SOURCE URL**: `https://huggingface.co/huihui-ai/Huihui-Qwen3.8-Flash-Next-abliterated-GGUF/resolve/main/BF16/`
- **COMMAND FLAGS**: `nohup aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5`
- **RC**: `rc=0`

## Active Processes (`10.0.70.72`)
```
mboyle    106031  105670  0 23:34 ?        00:00:00 timeout 180 aria2c -x 16 -s 16 -k 10M --continue=true --file-allocation=none --lowest-speed-limit=200K --timeout=10 --connect-timeout=10 --max-tries=5 --retry-wait=1 --summary-interval=10 -d /opt/models/qwen3.8-flash-next/BF16 -o Qwen3.8-Flash-Next-BF16-00003-of-00008.gguf https://huggingface.co/huihui-ai/Huihui-Qwen3.8-Flash-Next-abliterated-GGUF/resolve/main/BF16/Qwen3.8-Flash-Next-BF16-00003-of-00008.gguf
mboyle    106032  106031 14 23:34 ?        00:00:08 aria2c -x 16 -s 16 -k 10M --continue=true --file-allocation=none --lowest-speed-limit=200K --timeout=10 --connect-timeout=10 --max-tries=5 --retry-wait=1 --summary-interval=10 -d /opt/models/qwen3.8-flash-next/BF16 -o Qwen3.8-Flash-Next-BF16-00003-of-00008.gguf https://huggingface.co/huihui-ai/Huihui-Qwen3.8-Flash-Next-abliterated-GGUF/resolve/main/BF16/Qwen3.8-Flash-Next-BF16-00003-of-00008.gguf
mboyle    106539       1  0 23:34 ?        00:00:00 aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 -d /opt/models/qwen3.8-flash-next/BF16 -o Qwen3.8-Flash-Next-BF16-00004-of-00008.gguf https://huggingface.co/huihui-ai/Huihui-Qwen3.8-Flash-Next-abliterated-GGUF/resolve/main/BF16/Qwen3.8-Flash-Next-BF16-00004-of-00008.gguf
```

## Directory Listing After 5s (`ls -lh`)
```
-rw-rw-r-- 1 mboyle mboyle 88G Oct 17 23:34 /opt/models/qwen3.8-flash-next/BF16/Qwen3.8-Flash-Next-BF16-00003-of-00008.gguf
-rw-rw-r-- 1 mboyle mboyle 12K Oct 17 23:34 /opt/models/qwen3.8-flash-next/BF16/Qwen3.8-Flash-Next-BF16-00003-of-00008.gguf.aria2
```
