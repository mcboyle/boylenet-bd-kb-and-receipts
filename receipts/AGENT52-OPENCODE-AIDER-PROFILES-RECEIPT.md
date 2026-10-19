# AGENT-52 RECEIPT: OpenCode & Aider Local GPU Profiles (O1789)

- **WRITTEN-AT**: 2026-10-17T23:34:44Z
- **AGENT**: Agent-52 (Fast 2-Turn Integrator)
- **PROTOCOL**: Argon-4 / Rule 60 Single-Turn Compound Execution
- **SYNTAX-VERIFICATION**: PASS (`rc=0`)

## Generated Artifacts

| Profile | Path | SHA-256 | Validation |
| :--- | :--- | :--- | :--- |
| **OpenCode Local GPU** | `/home/mboyle/.config/opencode/boylenet-local-gpu.json` | `e02691e919703ce9303c4d928d5028babf9893a39f85c59ab0145dd07b1bbb42` | `json.loads` PASS (`rc=0`) |
| **Aider Local GPU** | `/home/mboyle/.aider.boylenet.conf.yml` | `0f6c86986322f7508d86dc9ffdb624e02676a5cf1a07072bbb1d76008144f44b` | `yaml.safe_load` PASS (`rc=0`) |

## Endpoint & Tier Mapping

- **Primary Endpoint (Local GPU Router)**: `http://10.0.70.164:4010/v1` (`sk-boylenet-local`)
- **Secondary Endpoint (Satellite LiteLLM)**: `http://10.0.70.228:4000/v1` (`sk-boylenet-local`)
- **Tier 1 (Default / Surgical Code)**: `qwen3-coder-30b-a3b`
- **Tier 2 (Deep Reasoning / Architect)**: `deepseek-r1-distill-llama-70b`
- **Tier 3 (Fast Probe / Editor / Weak)**: `deepseek-v4-flash-q4k`

## Argon-4 Conventions Embedded

1. **HTAP-9 Zero-Prose Persona**: `<= 50` tokens (`<STATUS> <path>`), zero filler/greetings.
2. **Rule 60 Compound Execution**: Single-turn pipeline discipline (`set -eo pipefail`, `<= 2` turns).
3. **Rule 78 Pytest Digest**: `test-cmd: bd-pytest-digest`, never raw pytest.
4. **Rule 22 Worktree & Git Safety**: `auto-commits: false`, `dirty-commits: false`, read-only floor injection (`/home/mboyle/bd-persist/FLEET_RULE-FLOOR.md`, `/home/mboyle/AGENTS.md`).
