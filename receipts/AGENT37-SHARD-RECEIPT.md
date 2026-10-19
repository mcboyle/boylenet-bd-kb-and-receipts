# AGENT-37 SHARD DOWNLOAD RECEIPT (O1789)

- **Agent**: Agent-37
- **Target Host**: `10.0.70.72`
- **Target Directory**: `/opt/models/qwen3.8-flash-next/BF16/`
- **Shards**:
  - `Qwen3.8-Flash-Next-BF16-00001-of-00008.gguf`
  - `Qwen3.8-Flash-Next-BF16-00002-of-00008.gguf`
- **Downloader**: Detached `nohup setsid aria2c -c -x 16 -s 16 -k 1M --max-tries=0 --retry-wait=5 --auto-file-renaming=false`
- **Status**: ACTIVE (`rc=0`)
- **Timestamp**: 2026-10-17T23:34:31Z

## Process Verification (`ps -ef`)
```
mboyle    106006       1  6 23:34 ?        00:00:01 aria2c -c -x 16 -s 16 -k 1M --max-tries=0 --retry-wait=5 --auto-file-renaming=false --console-log-level=notice --summary-interval=60 -d /opt/models/qwen3.8-flash-next/BF16 -o Qwen3.8-Flash-Next-BF16-00002-of-00008.gguf https://huggingface.co/huihui-ai/Huihui-Qwen3.8-Flash-Next-abliterated-GGUF/resolve/main/BF16/Qwen3.8-Flash-Next-BF16-00002-of-00008.gguf
```

## File Creation Verification (`ls -lh /opt/models/qwen3.8-flash-next/BF16/`)
```
total 12G
-rw-rw-r-- 1 mboyle mboyle 9.5K Oct 17 23:34 aria2c-00005.log
-rw-rw-r-- 1 mboyle mboyle 9.5K Oct 17 23:34 aria2c-00006.log
-rw-rw-r-- 1 mboyle mboyle 9.5K Oct 17 23:34 aria2c-shard7.log
-rw-rw-r-- 1 mboyle mboyle  11K Oct 17 23:34 aria2c-shard8.log
-rw-rw-r-- 1 mboyle mboyle  11M Oct 17 23:34 Qwen3.8-Flash-Next-BF16-00001-of-00008.gguf
-rw-rw-r-- 1 mboyle mboyle 9.8G Oct 17 23:34 Qwen3.8-Flash-Next-BF16-00002-of-00008.gguf
-rw-rw-r-- 1 mboyle mboyle 1.3K Oct 17 23:34 Qwen3.8-Flash-Next-BF16-00002-of-00008.gguf.aria2
-rw-rw-r-- 1 mboyle mboyle  88G Oct 17 23:34 Qwen3.8-Flash-Next-BF16-00003-of-00008.gguf
-rw-rw-r-- 1 mboyle mboyle  12K Oct 17 23:34 Qwen3.8-Flash-Next-BF16-00003-of-00008.gguf.aria2
-rw-rw-r-- 1 mboyle mboyle  40G Oct 17 23:34 Qwen3.8-Flash-Next-BF16-00005-of-00008.gguf
-rw-rw-r-- 1 mboyle mboyle 5.7K Oct 17 23:34 Qwen3.8-Flash-Next-BF16-00005-of-00008.gguf.aria2
-rw-rw-r-- 1 mboyle mboyle  40G Oct 17 23:34 Qwen3.8-Flash-Next-BF16-00006-of-00008.gguf
-rw-rw-r-- 1 mboyle mboyle 5.8K Oct 17 23:34 Qwen3.8-Flash-Next-BF16-00006-of-00008.gguf.aria2
-rw-rw-r-- 1 mboyle mboyle  40G Oct 17 23:34 Qwen3.8-Flash-Next-BF16-00007-of-00008.gguf
-rw-rw-r-- 1 mboyle mboyle 5.7K Oct 17 23:34 Qwen3.8-Flash-Next-BF16-00007-of-00008.gguf.aria2
-rw-rw-r-- 1 mboyle mboyle  39G Oct 17 23:34 Qwen3.8-Flash-Next-BF16-00008-of-00008.gguf
-rw-rw-r-- 1 mboyle mboyle 5.6K Oct 17 23:34 Qwen3.8-Flash-Next-BF16-00008-of-00008.gguf.aria2
-rw-rw-r-- 1 mboyle mboyle  14K Oct 17 23:34 shard1-aria2c.log
-rw-rw-r-- 1 mboyle mboyle  19K Oct 17 23:34 shard2-aria2c.log
```
