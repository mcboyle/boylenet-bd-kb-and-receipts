# AGENT-59 RECEIPT: Qwen3-Coder-480B-A35B-UD-Q2_K_XL Shards 1-2 (`10.0.70.83`)

- **Timestamp (UTC)**: `2026-10-17T23:41:36Z`
- **Agent**: Agent-59
- **Target Node**: `10.0.70.83`
- **Target Directory**: `/opt/models/tier3a-qwen3-coder-480b-a35b/`
- **Source API**: `https://huggingface.co/api/models/unsloth/Qwen3-Coder-480B-A35B-Instruct-GGUF/tree/main/UD-Q2_K_XL`
- **Verification**: `rc=0` (PIDs + files verified on disk)

## Shards 1 & 2

| Shard | Relative Path | Expected Bytes | Resolve URL |
| :--- | :--- | :--- | :--- |
| **Shard 1** | `UD-Q2_K_XL/Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00001-of-00004.gguf` | `49584600608` | `https://huggingface.co/unsloth/Qwen3-Coder-480B-A35B-Instruct-GGUF/resolve/main/UD-Q2_K_XL/Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00001-of-00004.gguf` |
| **Shard 2** | `UD-Q2_K_XL/Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00002-of-00004.gguf` | `49008188576` | `https://huggingface.co/unsloth/Qwen3-Coder-480B-A35B-Instruct-GGUF/resolve/main/UD-Q2_K_XL/Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00002-of-00004.gguf` |

## Verification Output (`10.0.70.83`, `rc=0`)
```text
-rw-rw-r-- 1 mboyle mboyle 46648000512 Oct 17 23:41 Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00001-of-00004.gguf
-rw-rw-r-- 1 mboyle mboyle 42896195584 Oct 17 23:41 Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00002-of-00004.gguf
UID          PID    PPID  C STIME TTY      STAT   TIME CMD
mboyle     19682   19336  0 23:39 ?        S      0:00 /usr/bin/aria2c.real -x 16 -s 16 -k 10M --continue=true --file-allocation=none --auto-save-interval=0 --max-tries=0 --retry-wait=2 --timeout=30 --connect-timeout=15 --max-overall-download-limit=2M --summary-interval=10 -d /opt/models/tier3a-qwen3-coder-480b-a35b -o Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00001-of-00004.gguf https://huggingface.co/unsloth/Qwen3-Coder-480B-A35B-Instruct-GGUF/resolve/main/UD-Q2_K_XL/Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00001-of-00004.gguf
mboyle     20410   20408  6 23:41 ?        S      0:00 /usr/bin/aria2c.real -x 8 -s 8 -k 10M --continue=true --file-allocation=none -d /opt/models/tier3a-qwen3-coder-480b-a35b -o Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00002-of-00004.gguf https://huggingface.co/unsloth/Qwen3-Coder-480B-A35B-Instruct-GGUF/resolve/main/UD-Q2_K_XL/Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00002-of-00004.gguf
```
