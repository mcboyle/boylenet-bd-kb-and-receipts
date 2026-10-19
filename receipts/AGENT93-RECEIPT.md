# AGENT-93 RECEIPT: `bd-local-token-estimator`

- **WRITTEN-AT**: 2026-10-17T23:41:15Z
- **TARGET**: `/home/mboyle/bin/bd-local-token-estimator`
- **MODE**: `-rwxr-xr-x` (`chmod +x`)
- **SHA256**: `5fecc02740e77819aebc2404961ee9aa0a7f15c92f2333688fc36adb582b2a7d`
- **VERIFICATION**: `rc=0`

## Capabilities Verified
1. **Token Estimation**: Supports file paths, `--text`, and `stdin` via `tiktoken` (`cl100k_base`) with deterministic lexical fallback (`heuristic:char4+lex`).
2. **GEMINI.md Context Guard Ceilings**: Enforces and reports `<=150k` (`workers/lens`) and `<=250k` (`pm/integrator`).
3. **Local GPU Tier Routing**: Recommends `tier2` (`16k`), `tier1` (`32k`), or `qwythos-9b` (`1M`) based on estimated token footprint.

## Live Smoke Output (`rc=0`)
- `GEMINI.md`: `TOKENS=2798 (tiktoken:cl100k_base) | GUARD[workers/lens<=150000]=OK (1.87%) | CEILINGS[workers/lens<=150k:PASS, pm/integrator<=250k:PASS] | RECOMMENDED_TIER=tier2 (16k)`
- `GEMINI.md (--json)`: `{"sources": ["/home/mboyle/GEMINI.md"], "bytes": 10175, "lines": 128, "estimated_tokens": 2798, "estimator": "tiktoken:cl100k_base", "context_guard": {"role": "workers/lens", "role_ceiling": 150000, "role_ok": true, "role_utilization_pct": 1.87, "workers_lens_ceiling": 150000, "workers_lens_ok": true, "pm_integrator_ceiling": 250000, "pm_integrator_ok": true}, "gpu_routing": {"recommended_tier": "tier2", "window_label": "16k", "window_tokens": 16384, "fits_local": true}, "tiers_reference": {"tier2": "16k", "tier1": "32k", "qwythos-9b": "1M"}}`
- `~20k tokens (tier1 32k)`: `TOKENS=18001 (tiktoken:cl100k_base) | GUARD[workers/lens<=150000]=OK (12.0%) | CEILINGS[workers/lens<=150k:PASS, pm/integrator<=250k:PASS] | RECOMMENDED_TIER=tier1 (32k)`
- `~46k tokens (qwythos-9b 1M)`: `TOKENS=40001 (tiktoken:cl100k_base) | GUARD[pm/integrator<=250000]=OK (16.0%) | CEILINGS[workers/lens<=150k:PASS, pm/integrator<=250k:PASS] | RECOMMENDED_TIER=qwythos-9b (1M)`
