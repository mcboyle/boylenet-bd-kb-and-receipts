# AGENT-87 SHARD LAUNCH RECEIPT (O1789)

- **WRITTEN-AT**: `2026-10-17T23:41:20Z`
- **AGENT**: `Agent-87`
- **NODE**: `10.0.70.83`
- **REPO**: `unsloth/Qwen3-Coder-480B-A35B-Instruct-GGUF`
- **QUANT**: `Q4_K_M`
- **TARGET_DIR**: `/opt/models/tier3a-qwen3-coder-480b-a35b/`
- **SHARDS**:
  - `Q4_K_M/Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00005-of-00006.gguf` -> `https://huggingface.co/unsloth/Qwen3-Coder-480B-A35B-Instruct-GGUF/resolve/main/Q4_K_M/Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00005-of-00006.gguf`
  - `Q4_K_M/Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00006-of-00006.gguf` -> `https://huggingface.co/unsloth/Qwen3-Coder-480B-A35B-Instruct-GGUF/resolve/main/Q4_K_M/Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00006-of-00006.gguf`
- **COMMAND**: `nohup aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none`
- **RC**: `rc=0`

## Remote Verification Output
```text
LAUNCHED: Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00005-of-00006.gguf pid=20315
LAUNCHED: Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00006-of-00006.gguf pid=20318
mboyle     17906       1  0 23:34 ?        00:00:00 bash -c while true; do aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 -j 3 -d /opt/models/glm-5.2/UD-Q3_K_M -i /opt/models/glm-5.2/UD-Q3_K_M/shards-4-6.uri && break; sleep 5; done
mboyle     17921       1  0 23:34 ?        00:00:00 bash -c until aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 --timeout=60 --connect-timeout=30 -d '/opt/models/glm-5.2/UD-Q3_K_M' -o 'GLM-5.2-UD-Q3_K_M-00007-of-00009.gguf' 'https://huggingface.co/huihui-ai/Huihui-GLM-5.2-abliterated-GGUF/resolve/main/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00007-of-00009.gguf' >> '/opt/models/glm-5.2/UD-Q3_K_M/aria2c-00007.log' 2>&1; do sleep 3; done
mboyle     17924       1  0 23:34 ?        00:00:00 bash -c until aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 --timeout=60 --connect-timeout=30 -d '/opt/models/glm-5.2/UD-Q3_K_M' -o 'GLM-5.2-UD-Q3_K_M-00008-of-00009.gguf' 'https://huggingface.co/huihui-ai/Huihui-GLM-5.2-abliterated-GGUF/resolve/main/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00008-of-00009.gguf' >> '/opt/models/glm-5.2/UD-Q3_K_M/aria2c-00008.log' 2>&1; do sleep 3; done
mboyle     17927       1  0 23:34 ?        00:00:00 bash -c until aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 --timeout=60 --connect-timeout=30 -d '/opt/models/glm-5.2/UD-Q3_K_M' -o 'GLM-5.2-UD-Q3_K_M-00009-of-00009.gguf' 'https://huggingface.co/huihui-ai/Huihui-GLM-5.2-abliterated-GGUF/resolve/main/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00009-of-00009.gguf' >> '/opt/models/glm-5.2/UD-Q3_K_M/aria2c-00009.log' 2>&1; do sleep 3; done
mboyle     18282       1 20 23:35 ?        00:01:13 aria2c -x 16 -s 16 -k 10M --continue=true --file-allocation=none --auto-save-interval=0 --lowest-speed-limit=200K --timeout=15 --connect-timeout=10 --max-tries=5 --retry-wait=1 --summary-interval=10 -d /opt/models/tier3a-qwen3-235b-a22b -o Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00001-of-00003.gguf https://huggingface.co/unsloth/Qwen3-235B-A22B-Instruct-2507-GGUF/resolve/main/Q4_K_M/Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00001-of-00003.gguf
mboyle     18554   18546 18 23:36 ?        00:00:49 /usr/bin/aria2c.real -x 16 -s 16 -k 10M --continue=true --file-allocation=none --auto-save-interval=0 --max-tries=0 --retry-wait=2 --timeout=30 --connect-timeout=15 --summary-interval=10 -d /opt/models/tier3a-qwen3-235b-a22b -o Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00003-of-00003.gguf https://huggingface.co/unsloth/Qwen3-235B-A22B-Instruct-2507-GGUF/resolve/main/Q4_K_M/Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00003-of-00003.gguf
mboyle     19266   17927  0 23:39 ?        00:00:00 bash /usr/bin/aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 --timeout=60 --connect-timeout=30 -d /opt/models/glm-5.2/UD-Q3_K_M -o GLM-5.2-UD-Q3_K_M-00009-of-00009.gguf https://huggingface.co/huihui-ai/Huihui-GLM-5.2-abliterated-GGUF/resolve/main/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00009-of-00009.gguf
mboyle     19268   17924  0 23:39 ?        00:00:00 bash /usr/bin/aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 --timeout=60 --connect-timeout=30 -d /opt/models/glm-5.2/UD-Q3_K_M -o GLM-5.2-UD-Q3_K_M-00008-of-00009.gguf https://huggingface.co/huihui-ai/Huihui-GLM-5.2-abliterated-GGUF/resolve/main/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00008-of-00009.gguf
mboyle     19269   17921  0 23:39 ?        00:00:00 bash /usr/bin/aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 --timeout=60 --connect-timeout=30 -d /opt/models/glm-5.2/UD-Q3_K_M -o GLM-5.2-UD-Q3_K_M-00007-of-00009.gguf https://huggingface.co/huihui-ai/Huihui-GLM-5.2-abliterated-GGUF/resolve/main/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00007-of-00009.gguf
mboyle     19442   17906  0 23:39 ?        00:00:00 bash /usr/bin/aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 -j 3 -d /opt/models/glm-5.2/UD-Q3_K_M -i /opt/models/glm-5.2/UD-Q3_K_M/shards-4-6.uri
mboyle     19679   19337  0 23:39 ?        00:00:00 /usr/bin/aria2c.real -x 16 -s 16 -k 10M --continue=true --file-allocation=none --auto-save-interval=0 --max-tries=0 --retry-wait=2 --timeout=30 --connect-timeout=15 --max-overall-download-limit=2M --summary-interval=10 -d /opt/models/tier3a-qwen3-coder-480b-a35b -o Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00001-of-00006.gguf https://huggingface.co/unsloth/Qwen3-Coder-480B-A35B-Instruct-GGUF/resolve/main/Q4_K_M/Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00001-of-00006.gguf
mboyle     19682   19336  0 23:39 ?        00:00:00 /usr/bin/aria2c.real -x 16 -s 16 -k 10M --continue=true --file-allocation=none --auto-save-interval=0 --max-tries=0 --retry-wait=2 --timeout=30 --connect-timeout=15 --max-overall-download-limit=2M --summary-interval=10 -d /opt/models/tier3a-qwen3-coder-480b-a35b -o Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00001-of-00004.gguf https://huggingface.co/unsloth/Qwen3-Coder-480B-A35B-Instruct-GGUF/resolve/main/UD-Q2_K_XL/Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00001-of-00004.gguf
mboyle     19706       1  0 23:39 ?        00:00:00 bash /usr/bin/aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none -d /opt/models/tier3a-qwen3-coder-480b-a35b -o Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00002-of-00004.gguf https://huggingface.co/unsloth/Qwen3-Coder-480B-A35B-Instruct-GGUF/resolve/main/UD-Q2_K_XL/Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00002-of-00004.gguf
mboyle     19980       1  0 23:40 ?        00:00:00 bash /usr/bin/aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none -d /opt/models/tier3a-qwen3-coder-480b-a35b -o Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00002-of-00004.gguf https://huggingface.co/unsloth/Qwen3-Coder-480B-A35B-Instruct-GGUF/resolve/main/UD-Q2_K_XL/Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00002-of-00004.gguf
mboyle     20055   16942 10 23:40 ?        00:00:04 /usr/bin/aria2c.real --auto-save-interval=0 -x 16 -s 16 -k 10M --continue=true --file-allocation=none --auto-save-interval=0 --lowest-speed-limit=200K --timeout=15 --connect-timeout=10 --max-tries=5 --retry-wait=1 --summary-interval=10 -d /opt/models/deepseek-v4-flash -o DeepSeek-V4-Flash-Q2-0731.gguf https://huggingface.co/huihui-ai/Huihui-DeepSeek-V4-Flash-0731-abliterated-GGUF/resolve/main/DeepSeek-V4-Flash-Q2-0731.gguf
mboyle     20278   18548 13 23:41 ?        00:00:00 /usr/bin/aria2c.real -x 16 -s 16 -k 10M --continue=true --file-allocation=none --auto-save-interval=0 --max-tries=0 --retry-wait=2 --timeout=30 --connect-timeout=15 --summary-interval=10 -d /opt/models/glm-5.2 -o GLM-5.2-UD-IQ2_XXS_RoutedIQ2XXS_blk78Q2K.gguf https://huggingface.co/huihui-ai/Huihui-GLM-5.2-abliterated-GGUF/resolve/main/DS4/GLM-5.2-UD-IQ2_XXS_RoutedIQ2XXS_blk78Q2K.gguf
mboyle     20315   20301  0 23:41 ?        00:00:00 bash /usr/bin/aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none -d /opt/models/tier3a-qwen3-coder-480b-a35b -o Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00005-of-00006.gguf https://huggingface.co/unsloth/Qwen3-Coder-480B-A35B-Instruct-GGUF/resolve/main/Q4_K_M/Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00005-of-00006.gguf
mboyle     20318   20301  0 23:41 ?        00:00:00 bash /usr/bin/aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none -d /opt/models/tier3a-qwen3-coder-480b-a35b -o Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00006-of-00006.gguf https://huggingface.co/unsloth/Qwen3-Coder-480B-A35B-Instruct-GGUF/resolve/main/Q4_K_M/Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00006-of-00006.gguf
total 4978400
drwxrwxr-x 2 mboyle mboyle        4096 Oct 17 23:41 .
drwxr-xr-x 8 mboyle mboyle        4096 Oct 17 23:40 ..
-rw-rw-r-- 1 mboyle mboyle           0 Oct 17 23:41 aria2c_Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00005-of-00006.gguf.log
-rw-rw-r-- 1 mboyle mboyle           0 Oct 17 23:41 aria2c_Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00006-of-00006.gguf.log
-rw-rw-r-- 1 mboyle mboyle           0 Oct 17 23:40 aria2c_shard2.log
-rw-rw-r-- 1 mboyle mboyle 46914338816 Oct 17 23:41 Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00001-of-00006.gguf
-rw-rw-r-- 1 mboyle mboyle        6316 Oct 17 23:38 Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00001-of-00006.gguf.aria2
-rw-rw-r-- 1 mboyle mboyle 46645903360 Oct 17 23:41 Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00001-of-00004.gguf
-rw-rw-r-- 1 mboyle mboyle        6269 Oct 17 23:38 Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00001-of-00004.gguf.aria2
-rw-rw-r-- 1 mboyle mboyle           6 Oct 17 23:39 shard1.pid
-rw-rw-r-- 1 mboyle mboyle           6 Oct 17 23:39 shard2.pid
```
