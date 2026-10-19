# AGENT-41 SHARD DOWNLOAD RECEIPT (GLM-5.2-UD-Q3_K_M Shards 1-3)
- **Timestamp**: 2026-10-17T23:34:32Z
- **Target Host**: 10.0.70.83
- **Target Directory**: /opt/models/glm-5.2/UD-Q3_K_M/
- **Shards**: GLM-5.2-UD-Q3_K_M-00001-of-00009.gguf, 00002-of-00009.gguf, 00003-of-00009.gguf
- **rc**: 0

## Verification Output
```
=== ACTIVE PIDS ===
16564 aria2c -x 16 -s 16 -k 10M --continue=true --file-allocation=none --auto-save-interval=0 --summary-interval=10 -d /opt/models/tier3a-qwen3-235b-a22b -o Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00002-of-00003.gguf https://huggingface.co/unsloth/Qwen3-235B-A22B-Instruct-2507-GGUF/resolve/main/Q4_K_M/Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00002-of-00003.gguf
16799 aria2c -x 16 -s 16 -k 10M --continue=true --file-allocation=none --auto-save-interval=0 --lowest-speed-limit=200K --timeout=15 --connect-timeout=10 --max-tries=5 --retry-wait=1 --summary-interval=10 -d /opt/models/tier3a-qwen3-235b-a22b -o Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00001-of-00003.gguf https://huggingface.co/unsloth/Qwen3-235B-A22B-Instruct-2507-GGUF/resolve/main/Q4_K_M/Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00001-of-00003.gguf
16800 aria2c -x 16 -s 16 -k 10M --continue=true --file-allocation=none --auto-save-interval=0 --lowest-speed-limit=200K --timeout=15 --connect-timeout=10 --max-tries=5 --retry-wait=1 --summary-interval=10 -d /opt/models/tier3a-qwen3-235b-a22b -o Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00003-of-00003.gguf https://huggingface.co/unsloth/Qwen3-235B-A22B-Instruct-2507-GGUF/resolve/main/Q4_K_M/Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00003-of-00003.gguf
17953 aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 -d /opt/models/glm-5.2/UD-Q3_K_M -o GLM-5.2-UD-Q3_K_M-00001-of-00009.gguf https://huggingface.co/huihui-ai/Huihui-GLM-5.2-abliterated-GGUF/resolve/main/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00001-of-00009.gguf
17955 aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 -d /opt/models/glm-5.2/UD-Q3_K_M -o GLM-5.2-UD-Q3_K_M-00002-of-00009.gguf https://huggingface.co/huihui-ai/Huihui-GLM-5.2-abliterated-GGUF/resolve/main/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00002-of-00009.gguf
17957 aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 -d /opt/models/glm-5.2/UD-Q3_K_M -o GLM-5.2-UD-Q3_K_M-00003-of-00009.gguf https://huggingface.co/huihui-ai/Huihui-GLM-5.2-abliterated-GGUF/resolve/main/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00003-of-00009.gguf
=== LS -LH ===
total 92K
-rw-rw-r-- 1 mboyle mboyle 1.3K Oct 17 23:34 aria2c_00001.log
-rw-rw-r-- 1 mboyle mboyle 1.3K Oct 17 23:34 aria2c_00002.log
-rw-rw-r-- 1 mboyle mboyle 1.3K Oct 17 23:34 aria2c_00003.log
-rw-rw-r-- 1 mboyle mboyle 1.3K Oct 17 23:34 aria2c-00007.log
-rw-rw-r-- 1 mboyle mboyle 1.3K Oct 17 23:34 aria2c-00008.log
-rw-rw-r-- 1 mboyle mboyle 1.3K Oct 17 23:34 aria2c-00009.log
-rw-rw-r-- 1 mboyle mboyle 3.5K Oct 17 23:34 aria2c-shards-4-6.log
-rw-rw-r-- 1 mboyle mboyle    0 Oct 17 23:34 GLM-5.2-UD-Q3_K_M-00001-of-00009.gguf
-rw-rw-r-- 1 mboyle mboyle   60 Oct 17 23:34 GLM-5.2-UD-Q3_K_M-00001-of-00009.gguf.aria2__temp
-rw-rw-r-- 1 mboyle mboyle    0 Oct 17 23:34 GLM-5.2-UD-Q3_K_M-00002-of-00009.gguf
-rw-rw-r-- 1 mboyle mboyle 5.8K Oct 17 23:34 GLM-5.2-UD-Q3_K_M-00002-of-00009.gguf.aria2__temp
-rw-rw-r-- 1 mboyle mboyle    0 Oct 17 23:34 GLM-5.2-UD-Q3_K_M-00003-of-00009.gguf
-rw-rw-r-- 1 mboyle mboyle 5.8K Oct 17 23:34 GLM-5.2-UD-Q3_K_M-00003-of-00009.gguf.aria2__temp
-rw-rw-r-- 1 mboyle mboyle    0 Oct 17 23:34 GLM-5.2-UD-Q3_K_M-00005-of-00009.gguf
-rw-rw-r-- 1 mboyle mboyle 5.8K Oct 17 23:34 GLM-5.2-UD-Q3_K_M-00005-of-00009.gguf.aria2__temp
-rw-rw-r-- 1 mboyle mboyle    0 Oct 17 23:34 GLM-5.2-UD-Q3_K_M-00006-of-00009.gguf
-rw-rw-r-- 1 mboyle mboyle 5.8K Oct 17 23:34 GLM-5.2-UD-Q3_K_M-00006-of-00009.gguf.aria2
-rw-rw-r-- 1 mboyle mboyle    0 Oct 17 23:34 GLM-5.2-UD-Q3_K_M-00007-of-00009.gguf
-rw-rw-r-- 1 mboyle mboyle 5.8K Oct 17 23:34 GLM-5.2-UD-Q3_K_M-00007-of-00009.gguf.aria2
-rw-rw-r-- 1 mboyle mboyle    0 Oct 17 23:34 GLM-5.2-UD-Q3_K_M-00008-of-00009.gguf
-rw-rw-r-- 1 mboyle mboyle 5.8K Oct 17 23:34 GLM-5.2-UD-Q3_K_M-00008-of-00009.gguf.aria2__temp
-rw-rw-r-- 1 mboyle mboyle    0 Oct 17 23:34 GLM-5.2-UD-Q3_K_M-00009-of-00009.gguf
-rw-rw-r-- 1 mboyle mboyle  403 Oct 17 23:34 GLM-5.2-UD-Q3_K_M-00009-of-00009.gguf.aria2__temp
-rw-rw-r-- 1 mboyle mboyle    6 Oct 17 23:34 shards-4-6.pid
-rw-rw-r-- 1 mboyle mboyle  510 Oct 17 23:34 shards-4-6.uri
```
