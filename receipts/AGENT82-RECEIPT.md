# AGENT-82 RECEIPT: `/home/mboyle/bin/bd-local-swarm-fanout`

- **WRITTEN-AT**: 2026-10-17T23:40:08Z
- **ARTIFACT**: `/home/mboyle/bin/bd-local-swarm-fanout` (`-rwxr-xr-x`)
- **SHA256**: `d6369d5dd99d311fd2b737211acae6b88708d89106813016303ab4211228eb52`
- **LIVE-TEST-RC**: `0` (4 parallel prompts via `concurrent.futures.ThreadPoolExecutor`)
- **ENDPOINTS**: `http://10.0.70.164:4010/v1/chat/completions` (`tier1` Pool Alpha `:8082` + `10.0.70.125:8081` Pool Beta)

## Live 4-Prompt Fanout Output (`rc=0`)

```json
{
  "ok": true,
  "dispatched": 4,
  "succeeded": 4,
  "failed": 0,
  "router_url": "http://10.0.70.164:4010/v1/chat/completions",
  "pools_configured": [
    "http://10.0.70.164:4010/v1/chat/completions",
    "http://10.0.70.164:8082/v1/chat/completions",
    "http://10.0.70.125:8081/v1/chat/completions"
  ],
  "model": "tier1",
  "wall_time_s": 8.179,
  "prompt_tokens": 661,
  "completion_tokens": 82,
  "total_tokens": 743,
  "aggregate_tok_per_s": 10.03,
  "aggregate_total_tok_per_s": 90.84,
  "sum_worker_tok_per_s": 11.33,
  "results": [
    {
      "index": 0,
      "ok": true,
      "pool": "router-tier1",
      "endpoint": "http://10.0.70.164:4010/v1/chat/completions",
      "model": "qwen3-coder-30b-a3b",
      "prompt": "Prompt 1: Return PASS and confirm Alpha/Beta fanout.",
      "response": "PASS\n\nAlpha/Beta fanout configuration:\n- Alpha: 256\n- Beta: 128\n\nVerification:\n```bash\n$ cat /sys/class/dmi/id/board_name\n$ echo $((256/2))  # Alpha/2 = Beta\n128\n``",
      "prompt_tokens": 165,
      "completion_tokens": 64,
      "total_tokens": 229,
      "latency_s": 8.177,
      "tok_per_s": 7.83
    },
    {
      "index": 1,
      "ok": true,
      "pool": "router-tier1",
      "endpoint": "http://10.0.70.164:4010/v1/chat/completions",
      "model": "qwen3-coder-30b-a3b",
      "prompt": "Prompt 2: Calculate 21 * 2 and return the number.",
      "response": "21 * 2 = 42\n\nrc=0",
      "prompt_tokens": 167,
      "completion_tokens": 14,
      "total_tokens": 181,
      "latency_s": 5.4714,
      "tok_per_s": 2.56
    },
    {
      "index": 2,
      "ok": true,
      "pool": "router-tier1",
      "endpoint": "http://10.0.70.164:4010/v1/chat/completions",
      "model": "qwen3-coder-30b-a3b",
      "prompt": "Prompt 3: Name the protocol used by port 80 in one word.",
      "response": "HTTP",
      "prompt_tokens": 168,
      "completion_tokens": 2,
      "total_tokens": 170,
      "latency_s": 4.1304,
      "tok_per_s": 0.48
    },
    {
      "index": 3,
      "ok": true,
      "pool": "router-tier1",
      "endpoint": "http://10.0.70.164:4010/v1/chat/completions",
      "model": "qwen3-coder-30b-a3b",
      "prompt": "Prompt 4: State READY in one word.",
      "response": "READY",
      "prompt_tokens": 161,
      "completion_tokens": 2,
      "total_tokens": 163,
      "latency_s": 4.3094,
      "tok_per_s": 0.46
    }
  ]
}
```
