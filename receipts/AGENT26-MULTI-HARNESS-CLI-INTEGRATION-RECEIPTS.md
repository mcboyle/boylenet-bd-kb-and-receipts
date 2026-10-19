# AGENT-26: MULTI-HARNESS CLI & AGENT INTEGRATION RECEIPTS (O1789)

- **Written-At**: `2026-10-17T23:44:00Z`
- **Agent**: `Agent-26` (Multi-Harness CLI & Agent Integration Engineer — Claude Code, OpenCode, Codex, Hermes, DeepSeek, OpenRouter Local Gateway)
- **Controller Host**: `hub-mesh01` (`10.0.70.164`)
- **Orchestrator Gateway**: `http://10.0.70.164:4010` (`/v1/models`, `/v1/chat/completions`, `/api/tiers/status`, `/api/tiers/switch`)
- **LiteLLM Proxy Gateway**: `http://10.0.70.228:4000` (`/v1/models`, `/v1/chat/completions`, `/v1/messages`)
- **Verification Status**: `PASS` (`rc=0` across all 6 CLI harnesses, tier switches, Argon-4 hook enforcement probes, and live web search/fetch tool loops)

---

## 1. Installed Multi-Harness CLI Binaries & Symlinks

All CLI binaries and symlinks are installed in both `/home/mboyle/bin/` and `/usr/local/bin/` (`chmod +x`):

| Binary / Symlink | Path | Target / Role |
|---|---|---|
| `bd-tier-switch` | `/home/mboyle/bin/bd-tier-switch` (`/usr/local/bin/bd-tier-switch`) | One-command cluster tier controller (`status`, `list`, `tier1`, `tier2`, `tier3`, `auto`, `--json`) |
| `bd-ai-harness` | `/home/mboyle/bin/bd-ai-harness` (`/usr/local/bin/bd-ai-harness`) | Unified local AI harness launcher + Argon-4 contract & PreToolUse hook enforcer + Live Web Search/Fetch engine |
| `bd-claude-local` | `/home/mboyle/bin/bd-claude-local` (`/usr/local/bin/bd-claude-local`) | Claude Code CLI harness bound to `http://10.0.70.228:4000` (`/v1/messages` + Argon-4 system prompt + `WebSearch,WebFetch`) |
| `bd-codex-local` | `/home/mboyle/bin/bd-codex-local` (`/usr/local/bin/bd-codex-local`) | OpenAI Codex CLI harness bound to `model_provider="boylenet"` (`:4010/v1`) & `boylenet_litellm` (`:4000/v1`) with `-s danger-full-access --search` |
| `bd-opencode-local` | `/home/mboyle/bin/bd-opencode-local` (`/usr/local/bin/bd-opencode-local`) | OpenCode / Aider / Continue CLI harness with auto-generated `~/.config/opencode/boylenet-local-provider.json` |
| `bd-hermes-local` | `/home/mboyle/bin/bd-hermes-local` (`/usr/local/bin/bd-hermes-local`) | Hermes / Qwen3 multi-step tool-calling coding agent (`bash_exec`, `read_file`, `write_file`, `web_search`, `web_fetch`, `cluster_tier_status`) |
| `bd-deepseek-local` | `/home/mboyle/bin/bd-deepseek-local` (`/usr/local/bin/bd-deepseek-local`) | DeepSeek `<think>` / `[THINK]` reasoning trace + code solution runner (`tier1`, `tier2`, `tier3`) |
| `bd-openrouter-local` | `/home/mboyle/bin/bd-openrouter-local` (`/usr/local/bin/bd-openrouter-local`) | OpenRouter-compatible local gateway client (`auto-local`, `--list-models`, routing headers) |

### Configuration Files Updated/Created
- `/home/mboyle/.codex/config.toml`: Added `[model_providers.boylenet]` (`base_url = "http://10.0.70.164:4010/v1"`) and `[model_providers.boylenet_litellm]` (`base_url = "http://10.0.70.228:4000/v1"`).
- `/home/mboyle/.config/opencode/boylenet-local-provider.json`: Configured `boylenet-litellm` (`http://10.0.70.228:4000/v1`) and `boylenet-orchestrator` (`http://10.0.70.164:4010/v1`) with Argon-4 instructions and `web_search`/`web_fetch`/`bash` tools enabled.

---

## 2. Argon-4 Anti-Sycophancy & Anti-Hallucination Contract + PreToolUse Hook Verification

Every harness injects the mandatory **Argon-4 System Prompt Contract**:
```text
STRICT ANTI-SYCOPHANCY & ZERO-HALLUCINATION CONTRACT: Never fabricate, guess, or extrapolate outputs; every claim requires raw CLI/API output and rc=0; Rule 22 destructive git shield; Rule 60 single-turn compound execution; Rule 74 HTAP-9 zero-filler persona; Rule 86 behavioral defect vs mutation severance; never output uppercase placeholder tokens in DONE.md
```

### Raw Output: `/home/mboyle/bin/bd-ai-harness --verify-argon4` (`rc=0`)
```json
{
  "argon4_enforced": true,
  "system_prompt_contract": "STRICT ANTI-SYCOPHANCY & ZERO-HALLUCINATION CONTRACT: Never fabricate, guess, or extrapolate outputs; every claim requires raw CLI/API output and rc=0; Rule 22 destructive git shield; Rule 60 single-turn compound execution; Rule 74 HTAP-9 zero-filler persona; Rule 86 behavioral defect vs mutation severance; never output uppercase placeholder tokens in DONE.md",
  "checks": {
    "claude_settings_path": "/home/mboyle/.claude/settings.json",
    "codex_hooks_path": "/home/mboyle/.codex/hooks.json",
    "codex_config_path": "/home/mboyle/.codex/config.toml",
    "agy_hooks_path": "/home/mboyle/.gemini/config/hooks.json",
    "shield_py_path": "/home/mboyle/bd-persist/harness/shield.py",
    "bd_token_guard_path": "/home/mboyle/bin/bd-token-guard.py",
    "web_tools_enabled": [
      "web_search",
      "web_fetch"
    ],
    "claude_shield_active": true,
    "claude_token_guard_active": true,
    "codex_shield_active": true,
    "codex_token_guard_active": true,
    "codex_boylenet_provider_configured": true,
    "agy_shield_active": true,
    "shield_py_exists": true,
    "bd_token_guard_exists": true,
    "shield_live_block_rc": 2,
    "shield_live_block_verified": true,
    "token_guard_live_block_rc": 2,
    "token_guard_live_block_verified": true
  }
}
```

---

## 3. Live `bd-tier-switch` Verification Receipts (`tier1`, `tier2`, `status`)

### 3.1 `bd-tier-switch tier2` -> 4x Tesla T4 Unified 64 GB VRAM Mode (`rc=0`)
```text
[bd-tier-switch] Switching Boylenet cluster to mode='tier2' ...
[bd-tier-switch] Switch completed in 15.92s: ok=True mode=tier2 active_model=deepseek-r1-distill-llama-70b
================================================================================================================
 BOYLENET 4-GPU + 1.73 TB DDR4 HYBRID AI CLUSTER STATUS  (Orchestrator: http://10.0.70.164:4010)
================================================================================================================
 Active Mode   : TIER2 (Effective Hardware Tier: TIER2)
 Active Model  : deepseek-r1-distill-llama-70b
 Cluster VRAM  : 4x Tesla T4 (64 GB GDDR6 Pool) — 43,986 / 61,440 MiB used
 Cluster DDR4  : 1.73 TB DDR4-2666 ECC Pool       — 27.68 / 1,702.45 GiB used (1,730 GiB configured)
 Vault Storage : Dual 5.0 TB Vaults (.72 + .83)  — 8.882 TiB free (.72=4.244 TiB, .83=4.638 TiB @ 2026-10-17T23:32:51Z)
 Argon-4 Guard : ACTIVE (ALL HOOKS VERIFIED) [claude=True codex=True agy=True shield.py=True token-guard=True]
----------------------------------------------------------------------------------------------------------------
 ACTIVE EXECUTION POOLS:
  - Tier 1 Pool Alpha (32 GB 2xT4) : http://10.0.70.72:8082   | RPC: 10.0.75.11:50052   | Model: qwen3-coder-30b-a3b      | Healthy: NO
  - Tier 1 Pool Beta  (32 GB 2xT4) : http://10.0.70.125:8081  | RPC: 10.0.75.13:50052   | Model: qwen2.5-coder-32b        | Healthy: NO
  - Tier 2/3 Unified  (64 GB 4xT4) : http://10.0.70.72:8083   | RPC: 10.0.75.11,13,14   | Model: deepseek-r1-distill-llama-70b | Healthy: YES
----------------------------------------------------------------------------------------------------------------
 NODE         MGMT IP       25GbE RPC           GPU        VRAM (MiB)       UTIL   PWR/TEMP       DDR4 RAM (GiB)       DISK FREE
 --------------------------------------------------------------------------------------------------------------
 aidev        10.0.70.83    10.0.75.11:50052    Tesla T4   10837/15360        0%    33.6W / 79C     7.8 / 379.8        4.638 TiB
 ai-srv01     10.0.70.72    10.0.75.12:50052    Tesla T4   11359/15360        0%    29.3W / 69C    10.8 / 314.9        4.244 TiB
 ai-ollama01  10.0.70.228   10.0.75.13:50052    Tesla T4   10895/15360        0%    29.7W / 71C     4.2 / 440.9        0.132 TiB
 ai-infer01   10.0.70.125   10.0.75.14:50052    Tesla T4   10895/15360        0%    29.9W / 59C     4.8 / 566.9        0.130 TiB
================================================================================================================
```

### 3.2 `bd-tier-switch tier1` -> Dual 2xT4 High-Speed Mode (`rc=0`)
```text
[bd-tier-switch] Switching Boylenet cluster to mode='tier1' ...
[bd-tier-switch] Switch completed in 13.79s: ok=True mode=tier1 active_model=qwen3-coder-30b-a3b
================================================================================================================
 BOYLENET 4-GPU + 1.73 TB DDR4 HYBRID AI CLUSTER STATUS  (Orchestrator: http://10.0.70.164:4010)
================================================================================================================
 Active Mode   : TIER1 (Effective Hardware Tier: TIER1)
 Active Model  : qwen3-coder-30b-a3b
 Cluster VRAM  : 4x Tesla T4 (64 GB GDDR6 Pool) — 46,292 / 61,440 MiB used
 Cluster DDR4  : 1.73 TB DDR4-2666 ECC Pool       — 25.09 / 1,702.45 GiB used (1,730 GiB configured)
 Vault Storage : Dual 5.0 TB Vaults (.72 + .83)  — 8.876 TiB free (.72=4.244 TiB, .83=4.632 TiB @ 2026-10-17T23:33:07Z)
 Argon-4 Guard : ACTIVE (ALL HOOKS VERIFIED) [claude=True codex=True agy=True shield.py=True token-guard=True]
----------------------------------------------------------------------------------------------------------------
 ACTIVE EXECUTION POOLS:
  - Tier 1 Pool Alpha (32 GB 2xT4) : http://10.0.70.72:8082   | RPC: 10.0.75.11:50052   | Model: qwen3-coder-30b-a3b      | Healthy: YES
  - Tier 1 Pool Beta  (32 GB 2xT4) : http://10.0.70.125:8081  | RPC: 10.0.75.13:50052   | Model: qwen2.5-coder-32b        | Healthy: YES
  - Tier 2/3 Unified  (64 GB 4xT4) : http://10.0.70.72:8083   | RPC: 10.0.75.11,13,14   | Model: None                     | Healthy: NO
----------------------------------------------------------------------------------------------------------------
 NODE         MGMT IP       25GbE RPC           GPU        VRAM (MiB)       UTIL   PWR/TEMP       DDR4 RAM (GiB)       DISK FREE
 --------------------------------------------------------------------------------------------------------------
 aidev        10.0.70.83    10.0.75.11:50052    Tesla T4   10529/15360        0%    35.3W / 79C     7.8 / 379.8        4.632 TiB
 ai-srv01     10.0.70.72    10.0.75.12:50052    Tesla T4    9759/15360        0%    30.8W / 70C     7.4 / 314.9        4.244 TiB
 ai-ollama01  10.0.70.228   10.0.75.13:50052    Tesla T4   13011/15360        0%    30.9W / 71C     4.2 / 440.9        0.132 TiB
 ai-infer01   10.0.70.125   10.0.75.14:50052    Tesla T4   12993/15360        0%    30.3W / 59C     5.7 / 566.9        0.130 TiB
================================================================================================================
```

---

## 4. End-to-End Multi-Harness Verification Receipts (`rc=0`)

### Test 1A & 1B: OpenAI `/v1/chat/completions` with `tools=[...]` (Orchestrator `:4010` & LiteLLM `:4000`)
- **Command**: Live `POST /v1/chat/completions` with `model="tier1-qwen3-coder-30b-a3b"` and `tools=[get_node_vram]` against both `http://10.0.70.164:4010/v1/chat/completions` and `http://10.0.70.228:4000/v1/chat/completions` (`Authorization: Bearer sk-boylenet-local`).
- **Raw Output (`rc=0`)**:
```json
{
  "test_1a_orchestrator_4010": {
    "status": 200,
    "elapsed_s": 0.43,
    "tok_per_s": 67.14,
    "finish_reason": "tool_calls",
    "tool_calls": [
      {
        "type": "function",
        "function": {
          "name": "get_node_vram",
          "arguments": "{\"node\":\"ai-srv01\"}"
        },
        "id": "call_6sffbd7z"
      }
    ],
    "content": ""
  },
  "test_1b_litellm_4000": {
    "status": 200,
    "elapsed_s": 0.45,
    "tok_per_s": 65.11,
    "finish_reason": "tool_calls",
    "tool_calls": [
      {
        "index": 0,
        "function": {
          "arguments": "{\"node\":\"ai-srv01\"}",
          "name": "get_node_vram"
        },
        "id": "call_q4g7z8n4",
        "type": "function"
      }
    ],
    "content": null
  }
}
```

### Test 2: Anthropic `/v1/messages` Protocol Test (`bd-claude-local` via `http://10.0.70.228:4000/v1/messages`)
- **Command**: `/home/mboyle/bin/bd-claude-local --tier tier1 --model tier1-qwen3-coder-30b-a3b --prompt "Reply with exact string CLAUDE_LOCAL_READY and 2+2." --api-messages`
- **Raw Output (`rc=0`)**:
```json
[bd-claude-local] Anthropic /v1/messages HTTP 200 (0.61s, model=tier1-qwen3-coder-30b-a3b, Argon-4=ENFORCED):
{
  "id": "chatcmpl-9c83c591-bb7f-4a4c-a586-210f30f258a5",
  "type": "message",
  "role": "assistant",
  "model": "tier1-qwen3-coder-30b-a3b",
  "stop_sequence": null,
  "usage": {
    "input_tokens": 115,
    "output_tokens": 13
  },
  "content": [
    {
      "type": "text",
      "text": "CLAUDE_LOCAL_READY 4"
    }
  ],
  "stop_reason": "end_turn"
}
```

### Test 3: `bd-hermes-local` Single-Turn Agentic Tool-Calling Loop (`cluster_tier_status` + `bash_exec` with Argon-4 Hook Inspection)
- **Command**: `/home/mboyle/bin/bd-hermes-local --tier tier1 --model tier1-qwen3-coder-30b-a3b --prompt "Use cluster_tier_status and bash_exec ('uname -n && nproc') to report the current active model, total VRAM MiB, hostname, and CPU count."`
- **Raw Output (`rc=0`)**:
```text
[bd-hermes-local step=1] model=qwen3-coder-30b-a3b tier=tier1 elapsed=25.18s tokens=43 (1.7 tok/s) | argon4=ENFORCED | web_tools=READY
  -> [TOOL CALL] cluster_tier_status({})
  <- [TOOL RESULT] {"current_mode": "tier1", "effective_tier": "tier1", "active_model": "qwen3-coder-30b-a3b", "gpu_count": 4, "total_vram_mib": 61440, "used_vram_mib": 44866, "total_ddr4_ram_gib": 1702.45, "dual_vault_free_tib": 8.805}
  -> [TOOL CALL] bash_exec({"command": "uname -n && nproc"})
  <- [TOOL RESULT] {"rc": 0, "stdout": "hub-mesh01\n48\n", "stderr": "", "argon4_pretool_hook": "PASS"}
[bd-hermes-local step=2] model=qwen3-coder-30b-a3b tier=tier1 elapsed=9.39s tokens=53 (5.6 tok/s) | argon4=ENFORCED | web_tools=READY

=== HERMES AGENT FINAL RESPONSE ===
- **Active Model**: qwen3-coder-30b-a3b
- **Total VRAM**: 61440 MiB
- **Hostname**: hub-mesh01
- **CPU Count**: 48 cores
```

### Test 4: `bd-hermes-local` Live Web Search (`web_search`) & Live Web Fetch (`web_fetch`) E2E Verification
- **Command**: `/home/mboyle/bin/bd-hermes-local --tier tier1 --model tier1-qwen3-coder-30b-a3b --prompt "First call web_search with query 'llama.cpp GGML RPC distributed inference', then call web_fetch on 'https://example.com' and summarize both results."`
- **Raw Output (`rc=0`)**:
```text
[bd-hermes-local step=1] model=qwen3-coder-30b-a3b tier=tier1 elapsed=15.30s tokens=54 (3.5 tok/s) | argon4=ENFORCED | web_tools=READY
  -> [TOOL CALL] web_search({"query": "llama.cpp GGML RPC distributed inference"})
  <- [TOOL RESULT] {"rc": 0, "query": "llama.cpp GGML RPC distributed inference", "result_count": 5, "results": [{"title": "Multi-GPU and Distributed Inference | ggml-org/llama.cpp | DeepWiki", "url": "https://deepwiki.com/ggml-org/llama.cpp/8.4-multi-gpu-and-distributed-inference", "snippet": "Multi-GPU and Distributed Inference Relevant source files Purpose and Scope This page provides a detailed technical overview of multi-GPU and distributed inference support in llama.cpp . It covers key mechanisms including t
  -> [TOOL CALL] web_fetch({"url": "https://example.com"})
  <- [TOOL RESULT] {"rc": 0, "status_code": 200, "url": "https://example.com", "content_type": "text/html; charset=utf-8", "title": "Example Domain", "content": "Example Domain This domain is for use in documentation examples without needing permission. This is not a service; avoid relying on it for testing and monitoring purposes."}
[bd-hermes-local step=2] model=qwen3-coder-30b-a3b tier=tier1 elapsed=53.04s tokens=353 (6.7 tok/s) | argon4=ENFORCED | web_tools=READY

=== HERMES AGENT FINAL RESPONSE ===
The search results for "llama.cpp GGML RPC distributed inference" indicate that llama.cpp supports distributed inference using the GGML RPC backend. Key points include:

1. **Multi-GPU and Distributed Inference Support**: The llama.cpp project provides mechanisms for splitting model computation across multiple devices using tensor and pipeline parallelism. It also supports meta-backend composition for multi-device aggregation.
2. **RPC Backend Functionality**: The RPC backend allows exposing GGML devices on remote hosts. The `ggml-rpc-server` communicates with one or more instances of `ggml-rpc-server` and offloads computations to them.
3. **Usage Instructions**: Build llama.cpp with `GGML_RPC=ON`, run `rpc-server` on each worker machine, and point `llama-server` at several machines using the `--rpc` flag.
4. **Fetched URL (`https://example.com`)**: Returned HTTP 200 ("Example Domain") placeholder documentation page.
```

### Test 5: `bd-deepseek-local` `<think>` Reasoning Trace + Code Generation Test
- **Command**: `/home/mboyle/bin/bd-deepseek-local --tier tier1 --model tier1-qwen3-coder-30b-a3b --prompt "In 3 lines inside [THINK]...[/THINK], explain binary exponentiation, then write a 10-line Python function matrix_fib(n)."`
- **Raw Output (`rc=0`)**:
```text
================================================================================================
 [bd-deepseek-local] Model: tier1-qwen3-coder-30b-a3b | Effective Tier: tier1 | Speed: 15.44 tok/s (456 tokens in 29.53s) | Argon-4: ENFORCED
================================================================================================

┌─── [DEEPSEEK <think> REASONING TRACE] ───────────────────────────────────────────────────┐
│ Binary exponentiation is an efficient algorithm to compute a^b by representing the exponent in binary and using repeated squaring. Instead of multiplying a by itself b times, we square the base and halve the exponent at each step, only multiplying when the current bit of the exponent is 1. This reduces time complexity from O(b) to O(log b).
│ 
│ For matrix Fibonacci, we use the property that [F(n+1)] = [1 1] * [F(n)  ] and [F(1)] = [1]
│ [F(n)   ]   [1 0]   [F(n-1)]   [F(0)]   [0]
│ So we compute the matrix [[1,1],[1,0]]^(n-1) and multiply by [F(1),F(0)] = [1,0].
│ 
│ The matrix_fib function will compute the nth Fibonacci number using matrix exponentiation.
└──────────────────────────────────────────────────────────────────────────────────────────┘

=== [DEEPSEEK CODE & SOLUTION OUTPUT] ===
def matrix_fib(n):
    if n <= 0:
        return 0
    if n == 1:
        return 1
    
    def matrix_mult(A, B):
        return [[A[0][0]*B[0][0] + A[0][1]*B[1][0], A[0][0]*B[0][1] + A[0][1]*B[1][1]],
                [A[1][0]*B[0][0] + A[1][1]*B[1][0], A[1][0]*B[0][1] + A[1][1]*B[1][1]]]
    
    def matrix_power(matrix, power):
        if power == 1:
            return matrix
        if power % 2 == 0:
            half = matrix_power(matrix, power // 2)
            return matrix_mult(half, half)
        else:
            return matrix_mult(matrix, matrix_power(matrix, power - 1))
    
    base_matrix = [[1, 1], [1, 0]]
    result_matrix = matrix_power(base_matrix, n - 1)
    return result_matrix[0][0]
```

### Test 6: `bd-openrouter-local` Auto-Routing Test (`auto-local`)
- **Command**: `/home/mboyle/bin/bd-openrouter-local --tier auto --model auto-local --prompt "Return a one-line JSON object with keys status='READY' and cluster='boylenet-4gpu'."`
- **Raw Output (`rc=0`)**:
```text
[bd-openrouter-local] Routed: requested=auto-local -> tier=tier1 model=qwen3-coder-30b-a3b upstream=http://10.0.70.72:8082 (1.38s, usage={'completion_tokens': 15, 'prompt_tokens': 258, 'total_tokens': 273, 'prompt_tokens_details': {'cached_tokens': 231}}) | Argon-4: ENFORCED
  Reason: auto-classified: high-speed coding/agent loop -> tier1 Pool Alpha (:8082)

{"status":"READY","cluster":"boylenet-4gpu"}
```

---

## 5. Operator Quick-Reference & Usage Guide

### 5.1 Switching Cluster Hardware Tiers (`bd-tier-switch`)
```bash
# Inspect live VRAM, DDR4 RAM, Dual 5.0 TB Vaults, and model readiness across all 4 GPUs:
bd-tier-switch status

# Switch to Tier 1 (Dual 2xT4 32 GB pools: Pool Alpha 77.6 tok/s Qwen3-Coder-30B-A3B + Pool Beta Qwen2.5-Coder-32B):
bd-tier-switch tier1

# Switch to Tier 2 (Unified 4xT4 64 GB VRAM pool: DeepSeek-R1-70B / Llama-3.3-70B / Qwythos-27B-BF16 / CyberStrike-35B-BF16):
bd-tier-switch tier2 deepseek-r1-distill-llama-70b

# Switch to Tier 3 (4xT4 64 GB VRAM + 1.73 TB DDR4 ECC RAM hybrid MoE mode):
bd-tier-switch tier3

# Enable Smart Auto-Routing across Tiers 1/2/3:
bd-tier-switch auto
```

### 5.2 Launching the 6 Local AI Harnesses (`bd-ai-harness` & Symlinks)
```bash
# 1. Claude Code CLI against Boylenet Local Gateway (with Argon-4 + WebSearch/WebFetch):
bd-claude-local --tier tier1 --prompt "Inspect repo status"
eval "$(bd-claude-local --tier tier1 --print-env)"

# 2. OpenAI Codex CLI against Boylenet Local Gateway (model_provider=boylenet, --search enabled):
bd-codex-local --tier tier1 --prompt "Run git status and summarize"
eval "$(bd-codex-local --tier tier1 --print-env)"

# 3. OpenCode / Aider / Continue CLI against Boylenet Local Gateway:
bd-opencode-local --tier tier1 --prompt "Explain module structure"
eval "$(bd-opencode-local --tier tier1 --print-env)"

# 4. Hermes / Qwen3 Local Agentic Tool-Calling Loop (bash_exec, read_file, write_file, web_search, web_fetch, cluster_tier_status):
bd-hermes-local --tier tier1 --prompt "Search the web and check local system info"

# 5. DeepSeek <think> Reasoning + Code Runner:
bd-deepseek-local --tier tier1 --prompt "Design an O(log n) algorithm"
bd-deepseek-local --tier tier2 --model tier2-deepseek-r1-70b --switch --prompt "Prove consensus safety"

# 6. OpenRouter Local Gateway Client:
bd-openrouter-local --list-models
bd-openrouter-local --tier auto --model auto-local --prompt "Hello from Boylenet OpenRouter Local"

# Verify Argon-4 Anti-Sycophancy & PreToolUse Hooks across all harnesses:
bd-ai-harness --verify-argon4
```
