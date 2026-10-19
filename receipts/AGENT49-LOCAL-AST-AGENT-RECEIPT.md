# AGENT-49 RECEIPT: `/home/mboyle/bin/bd-local-ast-agent`

- **Timestamp (UTC)**: 2026-10-17T23:34:49Z
- **Agent**: Agent-49 (Fast 2-Turn Builder)
- **Target Binary**: `/home/mboyle/bin/bd-local-ast-agent` (`-rwxr-xr-x`)
- **Endpoint**: `http://10.0.70.164:4010/v1/chat/completions` (`model=tier1`, `77.6 tok/s` class)
- **Live Target**: `/opt/bd-gpu-orchestrator/bd-gpu-orchestrator.py`
- **Live Verification Exit Code**: `rc=0`

## 1. Architecture & Law Enforcement
- **GEMINI.md AST Slicing Law**: Uses Python `ast.NodeVisitor` (`ClassDef`, `FunctionDef`, `AsyncFunctionDef` with decorator-aware `lineno..end_lineno`) and TypeScript/JS symbol/brace-balanced scanner without ingesting full files into LLM context.
- **Rule 80 (Pointer-Payload Decoupling)**: Writes full AST slice + LLM trace to `/var/tmp/bd-blobs/<sha256>.log` and emits a 4-line bounded terminal summary.

## 2. Live Execution Output (`/opt/bd-gpu-orchestrator/bd-gpu-orchestrator.py`)

### AST Symbol Table Discovery (`--list`)
```text
FILE /usr/local/bin/bd-gpu-orchestrator.py (1770 lines, 22 AST symbols)
  def run_ssh [L531-L555] (25 lines)
  def check_http_health [L558-L564] (7 lines)
  def _probe_vault_host [L567-L608] (42 lines)
  class OrchestratorState [L611-L1399] (789 lines)
  def OrchestratorState.__init__ [L612-L630] (19 lines)
  def OrchestratorState._load_persisted_state [L632-L645] (14 lines)
  def OrchestratorState._save_persisted_state [L647-L661] (15 lines)
  def OrchestratorState._probe_single_node [L663-L728] (66 lines)
  def OrchestratorState._probe_models_status [L730-L852] (123 lines)
  def OrchestratorState.refresh_telemetry [L854-L871] (18 lines)
  def OrchestratorState.get_status_payload [L873-L1000] (128 lines)
  def OrchestratorState.switch_mode [L1002-L1317] (316 lines)
  def OrchestratorState.classify_and_resolve_route [L1319-L1399] (81 lines)
  class OrchestratorHTTPHandler [L1405-L1743] (339 lines)
  def OrchestratorHTTPHandler.log_message [L1408-L1409] (2 lines)
  def OrchestratorHTTPHandler._send_json [L1411-L1420] (10 lines)
  def OrchestratorHTTPHandler.do_OPTIONS [L1422-L1430] (9 lines)
  def OrchestratorHTTPHandler.do_GET [L1432-L1516] (85 lines)
  def OrchestratorHTTPHandler.do_POST [L1518-L1547] (30 lines)
  def OrchestratorHTTPHandler._handle_chat_completions [L1549-L1743] (195 lines)
  def background_telemetry_loop [L1746-L1752] (7 lines)
  def main [L1755-L1766] (12 lines)
```

### Live AST Slice + `tier1` Dispatch (`rc=0`)
```text
AST-SLICE: bd-gpu-orchestrator.py::run_ssh L531-L555 (25/1770 lines, 98.6% reduction)
SYMBOLS-INDEXED: 22 (run_ssh, check_http_health, _probe_vault_host, OrchestratorState, OrchestratorState.__init__, OrchestratorState._load_persisted_state...)
TIER1-LLM: status=200 model=qwen3-coder-30b-a3b latency=5.306s (17.7 tok/s) -> **Symbol Contract**: `run_ssh` executes SSH commands remotely with timeout handling. | **Inputs**: `host` (str), `cmd` (str), `timeout` (int, default 30) | **Outputs**: Tuple of (returncode: int, stdout: str, stderr: str
CAS-PTR: /var/tmp/bd-blobs/cc589aa603db5230b01f110625689a89df0b59bfa609a718c0992083e2526ccf.log (sha256=cc589aa603db5230)
```
