# AGENT-43 SHARD DOWNLOADER RECEIPT (O1789)

- **WRITTEN-AT**: 2026-10-17T23:34:26Z
- **SEAT**: Agent-43
- **TARGET NODE**: `10.0.70.83`
- **TARGET DIRECTORY**: `/opt/models/glm-5.2/UD-Q3_K_M/`
- **SHARDS**:
  - `GLM-5.2-UD-Q3_K_M-00007-of-00009.gguf`
  - `GLM-5.2-UD-Q3_K_M-00008-of-00009.gguf`
  - `GLM-5.2-UD-Q3_K_M-00009-of-00009.gguf`
- **SOURCE**: `https://huggingface.co/huihui-ai/Huihui-GLM-5.2-abliterated-GGUF/resolve/main/UD-Q3_K_M/`
- **DOWNLOADER**: `nohup aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none`
- **STATUS**: ACTIVE (`rc=0`)

## Verification Output (after 5s sleep)
```text
=== ACTIVE_PIDS ===
17921 bash -c until aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 --timeout=60 --connect-timeout=30 -d '/opt/models/glm-5.2/UD-Q3_K_M' -o 'GLM-5.2-UD-Q3_K_M-00007-of-00009.gguf' 'https://huggingface.co/huihui-ai/Huihui-GLM-5.2-abliterated-GGUF/resolve/main/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00007-of-00009.gguf' >> '/opt/models/glm-5.2/UD-Q3_K_M/aria2c-00007.log' 2>&1; do sleep 3; done
17923 aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 --timeout=60 --connect-timeout=30 -d /opt/models/glm-5.2/UD-Q3_K_M -o GLM-5.2-UD-Q3_K_M-00007-of-00009.gguf https://huggingface.co/huihui-ai/Huihui-GLM-5.2-abliterated-GGUF/resolve/main/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00007-of-00009.gguf
17924 bash -c until aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 --timeout=60 --connect-timeout=30 -d '/opt/models/glm-5.2/UD-Q3_K_M' -o 'GLM-5.2-UD-Q3_K_M-00008-of-00009.gguf' 'https://huggingface.co/huihui-ai/Huihui-GLM-5.2-abliterated-GGUF/resolve/main/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00008-of-00009.gguf' >> '/opt/models/glm-5.2/UD-Q3_K_M/aria2c-00008.log' 2>&1; do sleep 3; done
17926 aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 --timeout=60 --connect-timeout=30 -d /opt/models/glm-5.2/UD-Q3_K_M -o GLM-5.2-UD-Q3_K_M-00008-of-00009.gguf https://huggingface.co/huihui-ai/Huihui-GLM-5.2-abliterated-GGUF/resolve/main/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00008-of-00009.gguf
17927 bash -c until aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 --timeout=60 --connect-timeout=30 -d '/opt/models/glm-5.2/UD-Q3_K_M' -o 'GLM-5.2-UD-Q3_K_M-00009-of-00009.gguf' 'https://huggingface.co/huihui-ai/Huihui-GLM-5.2-abliterated-GGUF/resolve/main/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00009-of-00009.gguf' >> '/opt/models/glm-5.2/UD-Q3_K_M/aria2c-00009.log' 2>&1; do sleep 3; done
17929 aria2c -x 8 -s 8 -k 10M --continue=true --file-allocation=none --max-tries=0 --retry-wait=5 --timeout=60 --connect-timeout=30 -d /opt/models/glm-5.2/UD-Q3_K_M -o GLM-5.2-UD-Q3_K_M-00009-of-00009.gguf https://huggingface.co/huihui-ai/Huihui-GLM-5.2-abliterated-GGUF/resolve/main/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00009-of-00009.gguf
=== LS_LH ===
-rw-rw-r-- 1 mboyle mboyle    0 Oct 17 23:34 /opt/models/glm-5.2/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00007-of-00009.gguf
-rw-rw-r-- 1 mboyle mboyle 5.8K Oct 17 23:34 /opt/models/glm-5.2/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00007-of-00009.gguf.aria2__temp
-rw-rw-r-- 1 mboyle mboyle    0 Oct 17 23:34 /opt/models/glm-5.2/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00008-of-00009.gguf
-rw-rw-r-- 1 mboyle mboyle    0 Oct 17 23:34 /opt/models/glm-5.2/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00009-of-00009.gguf
=== LOG_TAIL ===
==> /opt/models/glm-5.2/UD-Q3_K_M/aria2c-00007.log <==

10/17 23:34:21 [[1;32mNOTICE[0m] Downloading 1 item(s)

10/17 23:34:21 [[1;32mNOTICE[0m] CUID#7 - Redirecting to https://us.aws.cdn.hf.co/xet-bridge-us/6a4121863871c942735d9d98/49eb88fa1d1c1c8e0fa140cca3a88dddf743488a579b3eb3dc57169a904646f7?X-Xet-Cas-Uid=public&xip=511TgCyxp8c&response-content-disposition=inline%3B+filename*%3DUTF-8%27%27GLM-5.2-UD-Q3_K_M-00007-of-00009.gguf%3B+filename%3D%22GLM-5.2-UD-Q3_K_M-00007-of-00009.gguf%22%3B&user_id=public&Expires=1792283661&Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly91cy5hd3MuY2RuLmhmLmNvL3hldC1icmlkZ2UtdXMvNmE0MTIxODYzODcxYzk0MjczNWQ5ZDk4LzQ5ZWI4OGZhMWQxYzFjOGUwZmExNDBjY2EzYTg4ZGRkZjc0MzQ4OGE1NzliM2ViM2RjNTcxNjlhOTA0NjQ2ZjdcXD9YLVhldC1DYXMtVWlkPXB1YmxpYyZ4aXA9NTExVGdDeXhwOGMmcmVzcG9uc2UtY29udGVudC1kaXNwb3NpdGlvbj1pbmxpbmUlM0IrZmlsZW5hbWUlMkElM0RVVEYtOCUyNyUyN0dMTS01LjItVUQtUTNfS19NLTAwMDA3LW9mLTAwMDA5LmdndWYlM0IrZmlsZW5hbWUlM0QlMjJHTE0tNS4yLVVELVEzX0tfTS0wMDAwNy1vZi0wMDAwOS5nZ3VmJTIyJTNCJnVzZXJfaWQ9cHVibGljIiwiQ29uZGl0aW9uIjp7IkRhdGVMZXNzVGhhbiI6eyJFcG9jaFRpbWUiOjE3OTIyODM2NjF9fX1dfQ__&Signature=MEUCIQD1YN1KvRsN0nU4zbdb1hyQYCNv0HfB0n5T0i19qf8VewIgBouEsAUf2mbXngnDta2hDypqR7bWtp8uh0FajUuPk%7Ec_&Key-Pair-Id=01KXEF4KZ1B6FV465MAWR4M21F&Hash-Algorithm=SHA256

==> /opt/models/glm-5.2/UD-Q3_K_M/aria2c-00008.log <==

10/17 23:34:21 [[1;32mNOTICE[0m] Downloading 1 item(s)

10/17 23:34:21 [[1;32mNOTICE[0m] CUID#7 - Redirecting to https://us.aws.cdn.hf.co/xet-bridge-us/6a4121863871c942735d9d98/d02ba6b24068dbd88d5d3aeaf51a48af0df3dee603c97a86028c3ee7440e5a45?X-Xet-Cas-Uid=public&response-content-disposition=inline%3B+filename*%3DUTF-8%27%27GLM-5.2-UD-Q3_K_M-00008-of-00009.gguf%3B+filename%3D%22GLM-5.2-UD-Q3_K_M-00008-of-00009.gguf%22%3B&user_id=public&xip=T1WhO5jqZBE&Expires=1792283661&Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly91cy5hd3MuY2RuLmhmLmNvL3hldC1icmlkZ2UtdXMvNmE0MTIxODYzODcxYzk0MjczNWQ5ZDk4L2QwMmJhNmIyNDA2OGRiZDg4ZDVkM2FlYWY1MWE0OGFmMGRmM2RlZTYwM2M5N2E4NjAyOGMzZWU3NDQwZTVhNDVcXD9YLVhldC1DYXMtVWlkPXB1YmxpYyZyZXNwb25zZS1jb250ZW50LWRpc3Bvc2l0aW9uPWlubGluZSUzQitmaWxlbmFtZSUyQSUzRFVURi04JTI3JTI3R0xNLTUuMi1VRC1RM19LX00tMDAwMDgtb2YtMDAwMDkuZ2d1ZiUzQitmaWxlbmFtZSUzRCUyMkdMTS01LjItVUQtUTNfS19NLTAwMDA4LW9mLTAwMDA5LmdndWYlMjIlM0ImdXNlcl9pZD1wdWJsaWMmeGlwPVQxV2hPNWpxWkJFIiwiQ29uZGl0aW9uIjp7IkRhdGVMZXNzVGhhbiI6eyJFcG9jaFRpbWUiOjE3OTIyODM2NjF9fX1dfQ__&Signature=MEQCIE1tdmckAR%7EIaQysfQfcuydgBjcoBQjnwpbgsXZ1csTMAiABvG6waM9rqG1DA9itsHM2JOXyvNIoIrOEWq0U8DsqPA__&Key-Pair-Id=01KXEF4KZ1B6FV465MAWR4M21F&Hash-Algorithm=SHA256

==> /opt/models/glm-5.2/UD-Q3_K_M/aria2c-00009.log <==

10/17 23:34:21 [[1;32mNOTICE[0m] Downloading 1 item(s)

10/17 23:34:21 [[1;32mNOTICE[0m] CUID#7 - Redirecting to https://us.aws.cdn.hf.co/xet-bridge-us/6a4121863871c942735d9d98/5d50677920eb7291ad48b9995850e743a37dff2b1629a1494bd270c88b9ef78e?user_id=public&response-content-disposition=inline%3B+filename*%3DUTF-8%27%27GLM-5.2-UD-Q3_K_M-00009-of-00009.gguf%3B+filename%3D%22GLM-5.2-UD-Q3_K_M-00009-of-00009.gguf%22%3B&X-Xet-Cas-Uid=public&xip=T1WhO5jqZBE&Expires=1792283661&Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly91cy5hd3MuY2RuLmhmLmNvL3hldC1icmlkZ2UtdXMvNmE0MTIxODYzODcxYzk0MjczNWQ5ZDk4LzVkNTA2Nzc5MjBlYjcyOTFhZDQ4Yjk5OTU4NTBlNzQzYTM3ZGZmMmIxNjI5YTE0OTRiZDI3MGM4OGI5ZWY3OGVcXD91c2VyX2lkPXB1YmxpYyZyZXNwb25zZS1jb250ZW50LWRpc3Bvc2l0aW9uPWlubGluZSUzQitmaWxlbmFtZSUyQSUzRFVURi04JTI3JTI3R0xNLTUuMi1VRC1RM19LX00tMDAwMDktb2YtMDAwMDkuZ2d1ZiUzQitmaWxlbmFtZSUzRCUyMkdMTS01LjItVUQtUTNfS19NLTAwMDA5LW9mLTAwMDA5LmdndWYlMjIlM0ImWC1YZXQtQ2FzLVVpZD1wdWJsaWMmeGlwPVQxV2hPNWpxWkJFIiwiQ29uZGl0aW9uIjp7IkRhdGVMZXNzVGhhbiI6eyJFcG9jaFRpbWUiOjE3OTIyODM2NjF9fX1dfQ__&Signature=MEYCIQCsIfmvfR3Ktt0exPYe0ByaaqPI8-ixxF4VvnzU7dwM4gIhAJ7PfB%7EYBeWtaxuTGLqrGHU1brn3BMztGZNLJrAeilPJ&Key-Pair-Id=01KXEF4KZ1B6FV465MAWR4M21F&Hash-Algorithm=SHA256
```
