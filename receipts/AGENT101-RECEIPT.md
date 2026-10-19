# AGENT-101 RECEIPT: bd-local-oracle-fallback

- **WRITTEN-AT**: 2026-10-17T23:41:13Z
- **TARGET**: `/home/mboyle/bin/bd-local-oracle-fallback`
- **MODE**: `-rwxr-xr-x` (`chmod +x`)
- **SHA256**: `51b87a4f2363ac9f1c491e04d89bc93128d08ebe3db8866db3002afbf3497f11`
- **DEFAULT ROUTE**: `http://10.0.70.164:4010/v1/chat/completions` (`tier1`, `77.6 tok/s`, `$0` Local GPU Cluster)
- **ESCALATION ROUTE**: `--escalate-oracle` -> `bd-oracle` (Rule 79 BattleStation `10.0.10.137:9222`)
- **LIVE EXECUTION RC**: `0`
- **ESCALATION ROUTE CHECK RC**: `0`

## Live Verification Output
```json
{"route": "local_gpu_cluster", "endpoint": "http://10.0.70.164:4010/v1/chat/completions", "model": "qwen3-coder-30b-a3b", "throughput_ref": "77.6 tok/s", "cost_usd": 0.0, "content": "READY", "usage": {"completion_tokens": 2, "prompt_tokens": 166, "total_tokens": 168, "prompt_tokens_details": {"cached_tokens": 139}}}
```

## Escalation Route Verification Output
```json
{"route": "bd-oracle", "cdp": "10.0.10.137:9222", "cmd": ["/home/mboyle/bin/bd-oracle", "--oracle", "all", "--json", "Architecture check"], "status": "DRY_RUN_READY"}
```
