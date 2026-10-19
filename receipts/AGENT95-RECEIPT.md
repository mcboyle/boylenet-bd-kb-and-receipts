# AGENT-95 RECEIPT — `bd-local-bench-matrix`

- **WRITTEN-AT**: 2026-10-17T23:41:11Z
- **BINARY**: `/home/mboyle/bin/bd-local-bench-matrix`
- **SHA256**: `d0122d1f24775607b5fbbdede6f3685b1bc21b4dadbc1c4f8badbebaaa420e0e`
- **VERIFICATION**: `bd-local-bench-matrix` (`rc=0`) and `bd-local-bench-matrix --json` (`rc=0`, 11 models verified)

## Table Output (`bd-local-bench-matrix`)

```text
Argon-4 Verified llama-bench Speed Matrix
----------------------  ----------  ---------------  -----------------  ----------
Model                   Quant       Gen Speed (tg)   Prompt Speed (pp)  Mode      
----------------------  ----------  ---------------  -----------------  ----------
Qwen3-Coder-30B-A3B     MoE-A3B     77.62 tok/s      -                  GPU/MoE   
CyberStrike-35B-Q4_K_M  Q4_K_M      66.47 tok/s      -                  GPU       
Qwythos-9B-Q8_0         Q8_0        21.39 tok/s      1005.86 tok/s pp   GPU       
Qwythos-9B-BF16         BF16        16.28 tok/s      -                  GPU       
Qwythos-27B-Q4_K_M      Q4_K_M      12.30 tok/s      -                  GPU       
Qwen2.5-Coder-32B       Q4_K_M      11.27 tok/s      -                  GPU       
CyberStrike-35B-BF16    BF16        7.39 tok/s       -                  GPU/Hybrid
Qwythos-27B-BF16        BF16        4.48 tok/s       -                  GPU/Hybrid
DeepSeek-R1-70B         Q4_K_M      3.90 tok/s       -                  Hybrid    
Llama-3.3-70B           Q4_K_M      3.59 tok/s       -                  Hybrid    
DeepSeek-V3-671B        MoE-Hybrid  1.97-2.31 tok/s  -                  hybrid    
----------------------  ----------  ---------------  -----------------  ----------
```

## JSON Output (`bd-local-bench-matrix --json`)

```json
{
  "host": "Argon-4",
  "benchmark": "llama-bench",
  "verified": true,
  "count": 11,
  "models": [
    {
      "model": "Qwen3-Coder-30B-A3B",
      "quant": "MoE-A3B",
      "tg_tok_s": "77.62 tok/s",
      "tg_value": 77.62,
      "pp_tok_s": "-",
      "pp_value": null,
      "mode": "GPU/MoE",
      "notes": "Argon-4 verified"
    },
    {
      "model": "CyberStrike-35B-Q4_K_M",
      "quant": "Q4_K_M",
      "tg_tok_s": "66.47 tok/s",
      "tg_value": 66.47,
      "pp_tok_s": "-",
      "pp_value": null,
      "mode": "GPU",
      "notes": "Argon-4 verified"
    },
    {
      "model": "Qwythos-9B-Q8_0",
      "quant": "Q8_0",
      "tg_tok_s": "21.39 tok/s",
      "tg_value": 21.39,
      "pp_tok_s": "1005.86 tok/s pp",
      "pp_value": 1005.86,
      "mode": "GPU",
      "notes": "21.39 tok/s / 1005.86 tok/s pp"
    },
    {
      "model": "Qwythos-9B-BF16",
      "quant": "BF16",
      "tg_tok_s": "16.28 tok/s",
      "tg_value": 16.28,
      "pp_tok_s": "-",
      "pp_value": null,
      "mode": "GPU",
      "notes": "Argon-4 verified"
    },
    {
      "model": "Qwythos-27B-Q4_K_M",
      "quant": "Q4_K_M",
      "tg_tok_s": "12.30 tok/s",
      "tg_value": 12.3,
      "pp_tok_s": "-",
      "pp_value": null,
      "mode": "GPU",
      "notes": "Argon-4 verified"
    },
    {
      "model": "Qwen2.5-Coder-32B",
      "quant": "Q4_K_M",
      "tg_tok_s": "11.27 tok/s",
      "tg_value": 11.27,
      "pp_tok_s": "-",
      "pp_value": null,
      "mode": "GPU",
      "notes": "Argon-4 verified"
    },
    {
      "model": "CyberStrike-35B-BF16",
      "quant": "BF16",
      "tg_tok_s": "7.39 tok/s",
      "tg_value": 7.39,
      "pp_tok_s": "-",
      "pp_value": null,
      "mode": "GPU/Hybrid",
      "notes": "Argon-4 verified"
    },
    {
      "model": "Qwythos-27B-BF16",
      "quant": "BF16",
      "tg_tok_s": "4.48 tok/s",
      "tg_value": 4.48,
      "pp_tok_s": "-",
      "pp_value": null,
      "mode": "GPU/Hybrid",
      "notes": "Argon-4 verified"
    },
    {
      "model": "DeepSeek-R1-70B",
      "quant": "Q4_K_M",
      "tg_tok_s": "3.90 tok/s",
      "tg_value": 3.9,
      "pp_tok_s": "-",
      "pp_value": null,
      "mode": "Hybrid",
      "notes": "Argon-4 verified"
    },
    {
      "model": "Llama-3.3-70B",
      "quant": "Q4_K_M",
      "tg_tok_s": "3.59 tok/s",
      "tg_value": 3.59,
      "pp_tok_s": "-",
      "pp_value": null,
      "mode": "Hybrid",
      "notes": "Argon-4 verified"
    },
    {
      "model": "DeepSeek-V3-671B",
      "quant": "MoE-Hybrid",
      "tg_tok_s": "1.97-2.31 tok/s",
      "tg_value": [
        1.97,
        2.31
      ],
      "pp_tok_s": "-",
      "pp_value": null,
      "mode": "hybrid",
      "notes": "1.97-2.31 tok/s hybrid"
    }
  ]
}
```
