# AGENT-107: Production `$0` Local Coding Agent Pool Architect & E2E Verification Receipts

- **WRITTEN-AT**: `2026-10-18T23:31:35Z`
- **AGENT**: `Agent-107` (Autonomous `$0` Local Coding Agent Pool Architect)
- **CLI / DAEMON**: `/home/mboyle/bin/bd-local-coding-agent` (`/home/mboyle/bin/bd-local-coding-agent.py`)
- **MCP BRIDGE UPGRADE**: `/home/mboyle/bin/bd_local_mcp_bridge.py` (`apply_guarded_edit` / `normalize_and_apply_edit`)
- **HTTP API ENDPOINT**: `GET/POST http://127.0.0.1:4098/api/local-coding-agent` (`bd-multi-harness-web.py`)
- **LIVE STREAM LOG**: `/var/tmp/bd-headless-streams/agent107-local-coding-agent-pool.log`
- **RAW JSON RECEIPT**: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/agent107_local_coding_agent_e2e.json`
- **SANDBOX WORKTREE**: `/home/mboyle/bd-local-wt/sb-local-agent-e2e` (recycled symlink -> `/home/mboyle/bd-local-wt/sb-race-local-2080ti`, Rule 83)

---

## 1. Architectural Upgrades Landed

### 1.1 Indentation-Normalized `guarded_edit` (`apply_guarded_edit()`)
- Implemented in `/home/mboyle/bin/bd_local_mcp_bridge.py` (`normalize_and_apply_edit` + `_rebase_new_lines_to_orig_indent` + `apply_guarded_edit`) and exposed in `/home/mboyle/bin/bd-local-coding-agent.py`.
- **Pass 1 (`exact`)**: Attempts exact substring match and validates syntax via `compile(candidate, "<guarded_edit>", "exec")` for `.py` files.
- **Pass 2 (`indentation_normalized`)**: When 7B/14B local models emit 4-space/8-space indentation drift inside deeply nested blocks (e.g., 20-space `with/else` jumping to 8-space `if released_run_id:`), automatically matches line-stripped blocks (with subsequence fallback for omitted comments) and rebases `new_string` using per-section anchor deltas (`active_delta = orig_indent - nl_indent`).
- **Dual-Base Multi-Turn Fallback**: On Turn `t > 1`, `run_local_coding_agent()` attempts `apply_guarded_edit` against the modified file on disk first, and automatically falls back to `original_file_snapshot` if the model diffed against the original prompt snippet.

### 1.2 Multi-Turn Self-Healing Verification Gate Loop (`ruff` -> `mypy` -> `bd-pytest-digest`)
- Automatically runs after every `guarded_edit` turn:
  1. `ruff check --select E9,F63,F7,F82,F401,F841 <target_file> <target_test>`
  2. `/home/mboyle/BulkDownloader/venv/bin/mypy --follow-imports=silent <target_test>` + baseline-diffed signature check on `<target_file>` (ensuring zero new `mypy` errors vs pre-edit baseline)
  3. `/home/mboyle/bin/bd-pytest-digest <target_test>` (Rule 78 `<= 3 lines, < 25 tokens`)
- If any gate fails on Turn `< max_turns`, feeds the structured compiler/pytest failure digest and previous diff back to the local model for Turn `t+1` self-repair. Reverts cleanly to `original_file_snapshot` if still failing after `max_turns`.

### 1.3 Benchmark-Safe Smart Backend Selector (`--backend auto|2080ti|t4-heretic`)
- Probes `10.0.70.72` and `10.0.70.83` via SSH (`ps -ef | grep -E 'bench_worker\.py|llama-bench'`, zero inference impact).
- While `bench_worker.py` is active on `.72` (running `deepseek-v4.1-flash-abliterated-755b` / `kimi-k3-abliterated-iq1s-gguf`), routes 100% of local coding turns to BattleStation `10.0.10.137:11434` (`qwen2.5-coder:7b-16k` on RTX 2080 Ti 11GB GDDR6 VRAM — zero contention with `.72`/`.83`).
- Automatically unlocks `Qwen3.6-35B-A3B-Heretic` (`chat_template_kwargs: {"enable_thinking": false}`) once `.72`/`.83` are free of `bench_worker.py`.

---

## 2. Full End-to-End Verification Suite Results (`all_tasks_passed = true`, `$0.00` Cost)

| E2E Task ID | Target File | Target Test | Pre-Fix RED | Match Mode | Turns Used | TTFT | Speed (`tok/s`) | Wall Time | `ruff` | `mypy` | `bd-pytest-digest` | Cost |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Task 1: `dl-f4-live-1`** | `bulk_downloader/runner_teach.py` | `tests/test_dl_f4_teach_defer_claim_loop.py` | `rc=1` (`FAIL line 207 AssertionError`) | `indentation_normalized` (`indent_norm=True`) | **1 / 3** | `496.19 ms` | **`81.60 tok/s`** | `12.69 s` | `0` | `0` | `0` (`PASS: 12 passed in 6.13s`) | **`$0.00`** |
| **Task 2: `dl-f2-direct-media`** | `bulk_downloader/runner_teach.py` | `tests/test_dl_f2_auto_teach_direct_media.py` | `rc=1` (`FAIL line 47 AssertionError`) | `exact` (`indent_norm=False`) | **1 / 3** | `600.55 ms` | **`67.10 tok/s`** | `6.50 s` | `0` | `0` | `0` (`PASS: 4 passed in 1.64s`) | **`$0.00`** |
| **Task 3: `dl-f2-multiturn-self-heal`** | `bulk_downloader/runner_teach.py` | `tests/test_dl_f2_auto_teach_direct_media.py` | Turn 1 Defect (`return True` -> `pytest rc=1`) | `exact` (Dual-Base Fallback on Turn 2) | **2 / 3** | `5041.10 ms` | **`22.66 tok/s`** | `17.73 s` | `0` | `0` | `0` (`PASS: 4 passed in 3.95s`) | **`$0.00`** |

---

## 3. CLI & HTTP API Invocation Examples

```bash
# 1. Probe Benchmark-Safe Smart Backend Selector
/home/mboyle/bin/bd-local-coding-agent --probe-backends

# 2. Run a single $0 Local Coding Agent task in any authorized sandbox
/home/mboyle/bin/bd-local-coding-agent \
  --sandbox /home/mboyle/bd-local-wt/sb-local-agent-e2e \
  --target-file bulk_downloader/runner_teach.py \
  --target-test tests/test_dl_f4_teach_defer_claim_loop.py \
  --prompt "Fix dl-f4-live-1 in _handle_auto_teach_check..." \
  --backend auto --max-turns 3 --json

# 3. Run the full E2E verification suite
/home/mboyle/bin/bd-local-coding-agent --e2e-suite

# 4. Query or trigger via Multi-Harness Web Studio HTTP API (:4098)
curl -s http://127.0.0.1:4098/api/local-coding-agent
```
