# AGENT-60 RECEIPT: Qwen3-Coder-480B-A35B Shards 3-4 Launch
- **WRITTEN-AT**: 2026-10-17T23:52:46Z
- **NODE**: `10.0.70.83` (`wrk-test05`)
- **TARGET DIR**: `/opt/models/tier3a-qwen3-coder-480b-a35b/`
- **SHARDS LAUNCHED**:
  - `UD-Q2_K_XL/Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00003-of-00004.gguf`
  - `UD-Q2_K_XL/Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00004-of-00004.gguf`
  - `Q4_K_M/Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00003-of-00006.gguf`
  - `Q4_K_M/Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00004-of-00006.gguf`
- **COMMAND**: `nohup aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none`
- **VERIFICATION**: `rc=0`

## Active PIDs & Disk Verification
```
=== PIDS ===
mboyle     24528   24527  0 23:52 ?        00:00:00 bash /usr/bin/aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none -d /opt/models/tier3a-qwen3-coder-480b-a35b -o Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00003-of-00004.gguf https://huggingface.co/unsloth/Qwen3-Coder-480B-A35B-Instruct-GGUF/resolve/main/UD-Q2_K_XL/Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00003-of-00004.gguf
mboyle     24529   24527  0 23:52 ?        00:00:00 bash /usr/bin/aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none -d /opt/models/tier3a-qwen3-coder-480b-a35b -o Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00004-of-00004.gguf https://huggingface.co/unsloth/Qwen3-Coder-480B-A35B-Instruct-GGUF/resolve/main/UD-Q2_K_XL/Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00004-of-00004.gguf
mboyle     24530   24527  0 23:52 ?        00:00:00 bash /usr/bin/aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none -d /opt/models/tier3a-qwen3-coder-480b-a35b -o Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00003-of-00006.gguf https://huggingface.co/unsloth/Qwen3-Coder-480B-A35B-Instruct-GGUF/resolve/main/Q4_K_M/Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00003-of-00006.gguf
mboyle     24531   24527  0 23:52 ?        00:00:00 bash /usr/bin/aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none -d /opt/models/tier3a-qwen3-coder-480b-a35b -o Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00004-of-00006.gguf https://huggingface.co/unsloth/Qwen3-Coder-480B-A35B-Instruct-GGUF/resolve/main/Q4_K_M/Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00004-of-00006.gguf
=== FILES ===
total 7.7G
-rw-rw-r-- 1 mboyle mboyle    0 Oct 17 23:52 aria2c_Q4_K_M_shard3.log
-rw-rw-r-- 1 mboyle mboyle    0 Oct 17 23:52 aria2c_Q4_K_M_shard4.log
-rw-rw-r-- 1 mboyle mboyle    0 Oct 17 23:41 aria2c_Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00005-of-00006.gguf.log
-rw-rw-r-- 1 mboyle mboyle    0 Oct 17 23:41 aria2c_Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00006-of-00006.gguf.log
-rw-rw-r-- 1 mboyle mboyle  25K Oct 17 23:45 aria2c_shard2.log
-rw-rw-r-- 1 mboyle mboyle    0 Oct 17 23:52 aria2c_UD-Q2_K_XL_shard3.log
-rw-rw-r-- 1 mboyle mboyle    0 Oct 17 23:52 aria2c_UD-Q2_K_XL_shard4.log
-rw-rw-r-- 1 mboyle mboyle  44G Oct 17 23:45 Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00001-of-00006.gguf
-rw-rw-r-- 1 mboyle mboyle 6.2K Oct 17 23:45 Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00001-of-00006.gguf.aria2
-rw-rw-r-- 1 mboyle mboyle  44G Oct 17 23:45 Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00001-of-00004.gguf
-rw-rw-r-- 1 mboyle mboyle 6.2K Oct 17 23:45 Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00001-of-00004.gguf.aria2
-rw-rw-r-- 1 mboyle mboyle  41G Oct 17 23:45 Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00002-of-00004.gguf
-rw-rw-r-- 1 mboyle mboyle 5.9K Oct 17 23:45 Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00002-of-00004.gguf.aria2
-rw-rw-r-- 1 mboyle mboyle    6 Oct 17 23:41 shard1.pid
-rw-rw-r-- 1 mboyle mboyle    6 Oct 17 23:41 shard2.pid
-rw-rw-r-- 1 mboyle mboyle    6 Oct 17 23:52 shard3_q4_k_m.pid
-rw-rw-r-- 1 mboyle mboyle    6 Oct 17 23:52 shard3_ud_q2_k_xl.pid
-rw-rw-r-- 1 mboyle mboyle    6 Oct 17 23:52 shard4_q4_k_m.pid
-rw-rw-r-- 1 mboyle mboyle    6 Oct 17 23:52 shard4_ud_q2_k_xl.pid
```
