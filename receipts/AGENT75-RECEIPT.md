# AGENT-75 RECEIPT — `/home/mboyle/bin/bd-local-speculative-bench`

- **WRITTEN-AT**: 2026-10-17T23:40:05Z
- **AGENT**: Agent-75 (Fast 2-Turn Builder)
- **ARTIFACT**: `/home/mboyle/bin/bd-local-speculative-bench` (`0755`)
- **SHA256**: `2eb49383551e131acd7dbffbef540f5ea6c94a0d20812cbbdebebe8dbddbdea8`
- **STATUS_RC**: `0`
- **LIST_PAIRS_RC**: `0`

## Live Verification Output

### `bd-local-speculative-bench --status` (`rc=0`)
```text
bd-local-speculative-bench status (runners: /opt/llama-rpc/llama-speculative | /opt/llama-rpc/llama-completion --model-draft)
  NODE 10.0.70.72   REACHABLE              spec=no            comp=yes           gguf=DeepSeek-R1-Distill-Llama-70B-IQ4_XS.gguf,DeepSeek-R1-Distill-Llama-70B-Q4_K_M.gguf,DeepSeek-V4-Flash-Q4_K-0731.gguf,dspark-DeepSeek-V4-Flash-0731-BF16.gguf,dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf,Huihui-CyberStrike-OffSec-35B-abliterated-bf16.gguf,Huihui-CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf,Llama-3.3-70B-Instruct-Q4_K_M.gguf,mmproj-model-bf16.gguf,mmproj-Qwen3.8-Flash-Next-BF16.gguf,mmproj-Qwythos-27B-F16.gguf,mmproj-Qwythos-9B-Claude-Mythos-5-1M-F16.gguf,Qwen3.8-Flash-Next-BF16-00001-of-00008.gguf,Qwen3.8-Flash-Next-BF16-00002-of-00008.gguf,Qwen3.8-Flash-Next-BF16-00003-of-00008.gguf,Qwen3.8-Flash-Next-BF16-00004-of-00008.gguf,Qwen3.8-Flash-Next-BF16-00005-of-00008.gguf,Qwen3.8-Flash-Next-BF16-00006-of-00008.gguf,Qwen3.8-Flash-Next-BF16-00007-of-00008.gguf,Qwen3.8-Flash-Next-BF16-00008-of-00008.gguf,Qwen3-Coder-30B-A3B-Instruct-Q4_K_M.gguf,Qwen3-Coder-30B-A3B-Instruct-Q6_K.gguf,Qwythos-27B-MTP-BF16.gguf,Qwythos-27B-MTP-Q4_K_M.gguf,Qwythos-9B-Claude-Mythos-5-1M-MTP-BF16.gguf,Qwythos-9B-Claude-Mythos-5-1M-MTP-Q8_0.gguf,Swift-Qwen3.8-Flash-Next-GSQ-RCO-IQ2_XS-00001-of-00002.gguf,Swift-Qwen3.8-Flash-Next-GSQ-RCO-IQ2_XS-00002-of-00002.gguf
  NODE 10.0.70.83   REACHABLE              spec=no            comp=yes           gguf=DeepSeek-V3-0324-Q4_K_M-00001-of-00009.gguf,DeepSeek-V3-0324-Q4_K_M-00002-of-00009.gguf,DeepSeek-V3-0324-Q4_K_M-00003-of-00009.gguf,DeepSeek-V3-0324-Q4_K_M-00004-of-00009.gguf,DeepSeek-V3-0324-Q4_K_M-00005-of-00009.gguf,DeepSeek-V4-Flash-Q2-0731.gguf,dspark-DeepSeek-V4-Flash-0731-BF16.gguf,dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf,GLM-5.2-UD-IQ2_XXS_RoutedIQ2XXS_blk78Q2K.gguf,GLM-5.2-UD-Q3_K_M-00001-of-00009.gguf,GLM-5.2-UD-Q3_K_M-00002-of-00009.gguf,GLM-5.2-UD-Q3_K_M-00003-of-00009.gguf,GLM-5.2-UD-Q3_K_M-00004-of-00009.gguf,GLM-5.2-UD-Q3_K_M-00005-of-00009.gguf,GLM-5.2-UD-Q3_K_M-00006-of-00009.gguf,GLM-5.2-UD-Q3_K_M-00007-of-00009.gguf,GLM-5.2-UD-Q3_K_M-00008-of-00009.gguf,GLM-5.2-UD-Q3_K_M-00009-of-00009.gguf,Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00001-of-00003.gguf,Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00002-of-00003.gguf,Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00003-of-00003.gguf,Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00001-of-00006.gguf,Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00001-of-00004.gguf
```

### `bd-local-speculative-bench --list-pairs` (`rc=0`)
```text
PAIR_ID                    HOST         RUNNER                                       TARGET (-m)                                DRAFT (-md)
deepseek-v4-flash          10.0.70.72   /opt/llama-rpc/llama-speculative             DeepSeek-V4-0731-Q4_K_M.gguf               dspark-DeepSeek-V4-Flash-0731-BF16.gguf
qwythos-9b                 10.0.70.83   /opt/llama-rpc/llama-completion --model-draft Qwen2.5-72B-Instruct-Q4_K_M.gguf           Qwythos-9B-Q8_0.gguf
cross-node-qwythos-draft   10.0.70.72   /opt/llama-rpc/llama-speculative             dspark-DeepSeek-V4-Flash-0731-BF16.gguf    Qwythos-9B-Q8_0.gguf
```

### Command Resolution Proof (`--dry-run`)
```text
ssh 10.0.70.72 /opt/llama-rpc/llama-speculative\ -m\ /models/DeepSeek-V4-0731-Q4_K_M.gguf\ -md\ /opt/models/deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-BF16.gguf\ -p\ Explain\ speculative\ decoding\ in\ distributed\ GGUF\ inference\ and\ why\ draft\ acceptance\ rates\ govern\ token\ latency.\ -n\ 128\ --draft-max\ 16\ --draft-min\ 5\ --draft-p-min\ 0.75\ -t\ 16\ -ngl\ 99\ -ngld\ 99\ -c\ 4096\ --temp\ 0.0\ --no-cnv
ssh 10.0.70.83 /opt/llama-rpc/llama-completion\ -m\ /models/Qwen2.5-72B-Instruct-Q4_K_M.gguf\ -md\ /models/Qwythos-9B-Q8_0.gguf\ -p\ Explain\ speculative\ decoding\ in\ distributed\ GGUF\ inference\ and\ why\ draft\ acceptance\ rates\ govern\ token\ latency.\ -n\ 128\ --draft-max\ 16\ --draft-min\ 5\ --draft-p-min\ 0.75\ -t\ 16\ -ngl\ 99\ -ngld\ 99\ -c\ 4096\ --temp\ 0.0\ --no-cnv
```
