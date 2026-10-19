# AGENT-44 SHARD DOWNLOAD RECEIPT (O1789)
- **WRITTEN-AT**: `2026-10-17T23:35:25Z`
- **AGENT**: `Agent-44`
- **TARGET-NODE**: `10.0.70.83`
- **TARGET-DIR**: `/opt/models/tier3b-deepseek-v3-0324/`
- **MODEL-REPO**: `unsloth/DeepSeek-V3-0324-GGUF`
- **QUANT-SELECTED**: `Q4_K_M` (`9` shards, `376.65 GiB` total)
- **HF-API-PROBED**:
  - `https://huggingface.co/api/models/unsloth/DeepSeek-V3-0324-GGUF/tree/main/Q4_K_M` (`9` items)
  - `https://huggingface.co/api/models/unsloth/DeepSeek-V3-0324-GGUF/tree/main/UD-Q2_K_XL` (`6` items)
- **ACTIVE-PID**: `16947`
- **COMMAND**: `nohup aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none -d /opt/models/tier3b-deepseek-v3-0324 -i /opt/models/tier3b-deepseek-v3-0324/shards.urls`
- **STATUS**: `RUNNING (detached)`
- **rc**: `0`

## Active Process Verification (after 5s sleep)
```text
16947 aria2c -x 16 -s 16 -k 10M --continue=true --file-allocation=none --auto-save-interval=0 --lowest-speed-limit=200K --timeout=15 --connect-timeout=10 --max-tries=5 --retry-wait=1 --summary-interval=10 -d /opt/models/deepseek-v4-flash -o DeepSeek-V4-Flash-Q2-0731.gguf https://huggingface.co/huihui-ai/Huihui-DeepSeek-V4-Flash-0731-abliterated-GGUF/resolve/main/DeepSeek-V4-Flash-Q2-0731.gguf
17908 aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 -j 3 -d /opt/models/glm-5.2/UD-Q3_K_M -i /opt/models/glm-5.2/UD-Q3_K_M/shards-4-6.uri
17923 aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 --timeout=60 --connect-timeout=30 -d /opt/models/glm-5.2/UD-Q3_K_M -o GLM-5.2-UD-Q3_K_M-00007-of-00009.gguf https://huggingface.co/huihui-ai/Huihui-GLM-5.2-abliterated-GGUF/resolve/main/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00007-of-00009.gguf
17926 aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 --timeout=60 --connect-timeout=30 -d /opt/models/glm-5.2/UD-Q3_K_M -o GLM-5.2-UD-Q3_K_M-00008-of-00009.gguf https://huggingface.co/huihui-ai/Huihui-GLM-5.2-abliterated-GGUF/resolve/main/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00008-of-00009.gguf
17929 aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 --timeout=60 --connect-timeout=30 -d /opt/models/glm-5.2/UD-Q3_K_M -o GLM-5.2-UD-Q3_K_M-00009-of-00009.gguf https://huggingface.co/huihui-ai/Huihui-GLM-5.2-abliterated-GGUF/resolve/main/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00009-of-00009.gguf
17955 aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 -d /opt/models/glm-5.2/UD-Q3_K_M -o GLM-5.2-UD-Q3_K_M-00002-of-00009.gguf https://huggingface.co/huihui-ai/Huihui-GLM-5.2-abliterated-GGUF/resolve/main/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00002-of-00009.gguf
17957 aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 -d /opt/models/glm-5.2/UD-Q3_K_M -o GLM-5.2-UD-Q3_K_M-00003-of-00009.gguf https://huggingface.co/huihui-ai/Huihui-GLM-5.2-abliterated-GGUF/resolve/main/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00003-of-00009.gguf
18278 aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none -d /opt/models/tier3b-deepseek-v3-0324 -i /opt/models/tier3b-deepseek-v3-0324/shards.urls
18282 aria2c -x 16 -s 16 -k 10M --continue=true --file-allocation=none --auto-save-interval=0 --lowest-speed-limit=200K --timeout=15 --connect-timeout=10 --max-tries=5 --retry-wait=1 --summary-interval=10 -d /opt/models/tier3a-qwen3-235b-a22b -o Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00001-of-00003.gguf https://huggingface.co/unsloth/Qwen3-235B-A22B-Instruct-2507-GGUF/resolve/main/Q4_K_M/Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00001-of-00003.gguf
```

## Directory Verification (`ls -lh /opt/models/tier3b-deepseek-v3-0324/`)
```text
total 12K
-rw-rw-r-- 1 mboyle mboyle 6.0K Oct 17 23:35 aria2c.log
-rw-rw-r-- 1 mboyle mboyle    0 Oct 17 23:35 DeepSeek-V3-0324-Q4_K_M-00001-of-00009.gguf
-rw-rw-r-- 1 mboyle mboyle 1.5K Oct 17 23:35 shards.urls
```

## Filesystem Capacity (`df -h /opt/models`)
```text
Filesystem                         Size  Used Avail Use% Mounted on
/dev/mapper/ubuntu--vg-ubuntu--lv  5.0T  371G  4.7T   8% /
```
