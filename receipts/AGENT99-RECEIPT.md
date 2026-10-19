# AGENT-99 RECEIPT: `bd-local-model-verify`

- **WRITTEN-AT**: 2026-10-17T23:41:54Z
- **SCRIPT**: `/home/mboyle/bin/bd-local-model-verify`
- **SHA256**: `c7ed3fb0711f5c51e4ff75ac594bb69cc2e245cac2ae4bfd7effef61a0ad14e4`
- **EXECUTION**: `rc=0`
- **TARGETS**: `10.0.70.72:/opt/models`, `10.0.70.83:/opt/models`

## Live Verified Integrity Table

```text
NODE         | FILE                                                     | SIZE      | MAGIC  | DL_STATE    | INTEGRITY
-------------+----------------------------------------------------------+-----------+--------+-------------+----------
10.0.70.72   | cyberstrike-35b/Huihui-CyberStrike-OffSec-35B-abliterated-bf16.gguf | 67GB      | GGUF   | COMPLETE    | VERIFIED 
10.0.70.72   | cyberstrike-35b/Huihui-CyberStrike-OffSec-35B-abliterated-Q4_K_M.gguf | 21GB      | GGUF   | COMPLETE    | VERIFIED 
10.0.70.72   | cyberstrike-35b/mmproj-model-bf16.gguf                   | 861MB     | GGUF   | COMPLETE    | VERIFIED 
10.0.70.72   | deepseek-v4-flash/DeepSeek-V4-Flash-Q4_K-0731.gguf       | 148GB     | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.72   | deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-BF16.gguf | 11GB      | GGUF   | COMPLETE    | VERIFIED 
10.0.70.72   | deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf | 11GB      | GGUF   | COMPLETE    | VERIFIED 
10.0.70.72   | qwen3.8-flash-next/BF16/Qwen3.8-Flash-Next-BF16-00001-of-00008.gguf | 11MB      | GGUF   | COMPLETE    | VERIFIED 
10.0.70.72   | qwen3.8-flash-next/BF16/Qwen3.8-Flash-Next-BF16-00002-of-00008.gguf | 9.8GB     | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.72   | qwen3.8-flash-next/BF16/Qwen3.8-Flash-Next-BF16-00003-of-00008.gguf | 88GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.72   | qwen3.8-flash-next/BF16/Qwen3.8-Flash-Next-BF16-00004-of-00008.gguf | 40GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.72   | qwen3.8-flash-next/BF16/Qwen3.8-Flash-Next-BF16-00005-of-00008.gguf | 41GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.72   | qwen3.8-flash-next/BF16/Qwen3.8-Flash-Next-BF16-00006-of-00008.gguf | 40GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.72   | qwen3.8-flash-next/BF16/Qwen3.8-Flash-Next-BF16-00007-of-00008.gguf | 40GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.72   | qwen3.8-flash-next/BF16/Qwen3.8-Flash-Next-BF16-00008-of-00008.gguf | 39GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.72   | qwen3.8-flash-next/mmproj-Qwen3.8-Flash-Next-BF16.gguf   | 866MB     | GGUF   | COMPLETE    | VERIFIED 
10.0.70.72   | qwen3.8-flash-next/swift-IQ2_XS/Swift-Qwen3.8-Flash-Next-GSQ-RCO-IQ2_XS-00001-of-00002.gguf | 38GB      | GGUF   | COMPLETE    | VERIFIED 
10.0.70.72   | qwen3.8-flash-next/swift-IQ2_XS/Swift-Qwen3.8-Flash-Next-GSQ-RCO-IQ2_XS-00002-of-00002.gguf | 27GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.72   | qwen3.8-flash-next/UD-IQ4_XS/Qwen3.8-Flash-Next-UD-IQ4_XS-00001-of-00003.gguf | 11MB      | GGUF   | COMPLETE    | VERIFIED 
10.0.70.72   | qwen3.8-flash-next/UD-IQ4_XS/Qwen3.8-Flash-Next-UD-IQ4_XS-00002-of-00003.gguf | 41GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.72   | qwen3.8-flash-next/UD-IQ4_XS/Qwen3.8-Flash-Next-UD-IQ4_XS-00003-of-00003.gguf | 36GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.72   | qwythos-27b/mmproj-Qwythos-27B-F16.gguf                  | 885MB     | GGUF   | COMPLETE    | VERIFIED 
10.0.70.72   | qwythos-27b/Qwythos-27B-MTP-BF16.gguf                    | 51GB      | GGUF   | COMPLETE    | VERIFIED 
10.0.70.72   | qwythos-27b/Qwythos-27B-MTP-Q4_K_M.gguf                  | 17GB      | GGUF   | COMPLETE    | VERIFIED 
10.0.70.72   | qwythos-27b/Qwythos-27B-MTP-Q8_0.gguf                    | 26GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.72   | qwythos-9b/Qwythos-9B-Claude-Mythos-5-1M-MTP-BF16.gguf   | 18GB      | GGUF   | COMPLETE    | VERIFIED 
10.0.70.72   | tier1-qwen3-coder-30b-a3b/Qwen3-Coder-30B-A3B-Instruct-Q4_K_M.gguf | 18GB      | GGUF   | COMPLETE    | VERIFIED 
10.0.70.72   | tier1-qwen3-coder-30b-a3b/Qwen3-Coder-30B-A3B-Instruct-Q6_K.gguf | 22GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.72   | tier2-70b/DeepSeek-R1-Distill-Llama-70B-IQ4_XS.gguf      | 36GB      | GGUF   | COMPLETE    | VERIFIED 
10.0.70.72   | tier2-70b/DeepSeek-R1-Distill-Llama-70B-Q4_K_M.gguf      | 40GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.72   | tier2-70b/Llama-3.3-70B-Instruct-Q4_K_M.gguf             | 40GB      | GGUF   | COMPLETE    | VERIFIED 
10.0.70.83   | deepseek-v4-flash/DeepSeek-V4-Flash-Q2-0731.gguf         | 77GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.83   | deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-BF16.gguf | 11GB      | GGUF   | COMPLETE    | VERIFIED 
10.0.70.83   | deepseek-v4-flash/dspark-DeepSeek-V4-Flash-0731-Q8_0.gguf | 11GB      | GGUF   | COMPLETE    | VERIFIED 
10.0.70.83   | glm-5.2/GLM-5.2-UD-IQ2_XXS_RoutedIQ2XXS_blk78Q2K.gguf    | 187GB     | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.83   | glm-5.2/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00001-of-00009.gguf  | 9.0MB     | GGUF   | COMPLETE    | VERIFIED 
10.0.70.83   | glm-5.2/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00002-of-00009.gguf  | 40GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.83   | glm-5.2/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00003-of-00009.gguf  | 40GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.83   | glm-5.2/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00004-of-00009.gguf  | 40GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.83   | glm-5.2/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00005-of-00009.gguf  | 40GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.83   | glm-5.2/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00006-of-00009.gguf  | 40GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.83   | glm-5.2/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00007-of-00009.gguf  | 40GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.83   | glm-5.2/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00008-of-00009.gguf  | 40GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.83   | glm-5.2/UD-Q3_K_M/GLM-5.2-UD-Q3_K_M-00009-of-00009.gguf  | 2.7GB     | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.83   | qwythos-9b/Qwythos-9B-Claude-Mythos-5-1M-MTP-Q8_0.gguf   | 9.2GB     | GGUF   | COMPLETE    | VERIFIED 
10.0.70.83   | tier3a-qwen3-235b-a22b/Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00001-of-00003.gguf | 46GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.83   | tier3a-qwen3-235b-a22b/Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00002-of-00003.gguf | 47GB      | GGUF   | COMPLETE    | VERIFIED 
10.0.70.83   | tier3a-qwen3-235b-a22b/Qwen3-235B-A22B-Instruct-2507-Q4_K_M-00003-of-00003.gguf | 39GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.83   | tier3a-qwen3-coder-480b-a35b/Qwen3-Coder-480B-A35B-Instruct-Q4_K_M-00001-of-00006.gguf | 44GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.83   | tier3a-qwen3-coder-480b-a35b/Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00001-of-00004.gguf | 44GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.83   | tier3a-qwen3-coder-480b-a35b/Qwen3-Coder-480B-A35B-Instruct-UD-Q2_K_XL-00002-of-00004.gguf | 41GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.83   | tier3b-deepseek-v3-0324/DeepSeek-V3-0324-Q4_K_M-00001-of-00009.gguf | 23GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.83   | tier3b-deepseek-v3-0324/DeepSeek-V3-0324-Q4_K_M-00002-of-00009.gguf | 41GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.83   | tier3b-deepseek-v3-0324/DeepSeek-V3-0324-Q4_K_M-00003-of-00009.gguf | 40GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.83   | tier3b-deepseek-v3-0324/DeepSeek-V3-0324-Q4_K_M-00004-of-00009.gguf | 40GB      | GGUF   | IN_PROGRESS | PARTIAL  
10.0.70.83   | tier3b-deepseek-v3-0324/DeepSeek-V3-0324-Q4_K_M-00005-of-00009.gguf | 41GB      | GGUF   | IN_PROGRESS | PARTIAL  
```
