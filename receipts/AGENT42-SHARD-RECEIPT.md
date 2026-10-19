# AGENT-42 SHARD DOWNLOADER RECEIPT (`GLM-5.2-UD-Q3_K_M` Shards 4-6)

- **Status**: `rc=0` (ACTIVE & VERIFIED)
- **Target Host**: `10.0.70.83`
- **Target Directory**: `/opt/models/glm-5.2/UD-Q3_K_M/`
- **Shards**:
  - `GLM-5.2-UD-Q3_K_M-00004-of-00009.gguf`
  - `GLM-5.2-UD-Q3_K_M-00005-of-00009.gguf`
  - `GLM-5.2-UD-Q3_K_M-00006-of-00009.gguf`
- **Source URL Base**: `https://huggingface.co/huihui-ai/Huihui-GLM-5.2-abliterated-GGUF/resolve/main/UD-Q3_K_M/`
- **Downloader Command**:
  `nohup bash -c 'while true; do aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 -j 3 -d /opt/models/glm-5.2/UD-Q3_K_M -i /opt/models/glm-5.2/UD-Q3_K_M/shards-4-6.uri && break; sleep 5; done'`
- **Active PIDs**:
  - Supervisor Loop PID: `17906` (`/opt/models/glm-5.2/UD-Q3_K_M/shards-4-6.pid`)
  - `aria2c` Worker PID: `17908`
- **Log File**: `10.0.70.83:/opt/models/glm-5.2/UD-Q3_K_M/aria2c-shards-4-6.log`
- **Verification (`ls -lh` & Log Check after 5s)**:
  - All 3 shards (`00004`, `00005`, `00006`) resolved and redirected to `us.aws.cdn.hf.co/xet-bridge-us/...` (`CUID#7`, `CUID#8`, `CUID#9`) with active file allocation (`--file-allocation=none`) and `.aria2` state files initialized.
