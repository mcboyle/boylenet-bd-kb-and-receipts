# AGENT-80 RECEIPT: Boylenet Local Codex Provider Config (O1789)

- **WRITTEN-AT**: 2026-10-17T23:39:37Z
- **TARGET**: `/home/mboyle/.codex/boylenet-local-provider.toml`
- **TARGET-SHA256**: `dd215f513b16c851202d265f2d2615bcd616ce91ff43831a58d26d3932a46efe`
- **CONFIG-TOML-SHA256-BEFORE**: `fb370411e7ea90aa8fc766cc1959c6d7de89bbd863e68183e9bdd55969b28c7d`
- **CONFIG-TOML-SHA256-AFTER**: `fb370411e7ea90aa8fc766cc1959c6d7de89bbd863e68183e9bdd55969b28c7d` (untouched)
- **TOML-SYNTAX-CHECK**: `python3 -c 'import tomllib...'` -> `PASS (rc=0)`

## Defined Provider & Profiles
- `[model_providers.boylenet_local]`: `base_url = "http://10.0.70.164:4010/v1"`, `env_key = "OPENAI_API_KEY"`, `wire_api = "chat"`
- `[profiles.tier1]`: `model = "qwen3-coder-30b-a3b"`
- `[profiles.tier1-sec]`: `model = "cyberstrike-offsec-35b-bf16"`
- `[profiles.tier2]`: `model = "deepseek-r1-distill-llama-70b"`
- `[profiles.tier3]`: `model = "deepseek-v4-flash-q4k"`
