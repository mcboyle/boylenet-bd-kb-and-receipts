# AGENT-145 — 20-Worker Local On-Prem GPU Pool + 10-Worker Dual Browser Oracle Mega-Blitz Receipts

- **WRITTEN-AT**: `2026-10-19T00:06:26Z`
- **SEAT**: `Agent-145 (Swarms #071-#085 Doubled: 20-Worker Local GPU + 10-Worker Dual Browser Oracle Mega-Blitz)`
- **EXECUTABLE**: `/home/mboyle/bin/bd-local-20gpu-10oracle-blitz.py`
- **MASTER STREAM LOG**: `/var/tmp/bd-headless-streams/agent145-local-20gpu-10oracle-blitz.log`
- **PER-WORKER STREAM LOGS**: `/var/tmp/bd-headless-streams/local-gpu-w{01..20}.log` & `/var/tmp/bd-headless-streams/oracle-w{01..10}.log`
- **JSON ARTIFACT**: `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/agent145-local-20gpu-10oracle-blitz.json`
- **CRITICAL INVARIANT**: `.72` (`10.0.70.72`) completely untouched (`node_72_touched = False`) while `Agent-37` finishes `kimi-k3-abliterated-iq1s-gguf`!
- **OVERALL VERDICT**: **`30/30 WORKERS PASS`** (`20/20 Local GPU` + `10/10 Dual Browser Oracle`, wall-clock `25.72s`, total marginal cost **`$0.00`**)

---

## 1. Executive Hardware Tier Summary

| Worker Pool | Host & Endpoint | Model / Engine | Workers | Avg TTFT | Avg Throughput | Marginal Cost | Verdict |
|---|---|---|---:|---:|---:|---:|---|
| `local-gpu-w01..w08` | `http://10.0.70.83:18080/v1/chat/completions` | `Qwen3.6-35B-A3B-Heretic` (`Q4_K_M`, Dual Tesla T4, `enable_thinking: false`) | `8/8` | `2024.9 ms` | `8.60 tok/s` | `$0.00` | **PASS** |
| `local-gpu-w09..w16` | `http://10.0.10.137:11434/api/chat` | `qwen2.5-coder:7b-16k` (BattleStation RTX 2080 Ti) | `8/8` | `1025.8 ms` | `81.27 tok/s` | `$0.00` | **PASS** |
| `local-gpu-w17..w20` | `http://10.0.70.228:11434/api/chat` + `http://10.0.70.125:8081/embeddings` | `qwen2.5-coder:7b` + `bge-m3` (`1024-dim` Qdrant) | `4/4` | `108.0 ms` | `18.15 tok/s` + `1024-dim` | `$0.00` | **PASS** |
| `oracle-w01..w05` | `http://10.0.10.137:9222` (BattleStation CDP) | Windows Chrome CDP (`155.0.8059.41`) | `5/5` | N/A | `12 Portal Checks` | `$0.00` | **PASS** |
| `oracle-w06..w10` | `http://10.0.70.181:9222` (Bittorrent CDP) | Windows Chrome CDP (`155.0.8059.80`) | `5/5` | N/A | `12 Portal Checks` | `$0.00` | **PASS** |

---

## 2. Workers `local-gpu-w01..w08` — `.83:18080` (`Qwen3.6-35B-A3B-Heretic` on Dual Tesla T4)

| Worker | Category | Task | `enable_thinking` | TTFT (`ms`) | Tokens | Throughput (`tok/s`) | Cost | Stream Log | Output Snippet |
|---|---|---|---|---:|---:|---:|---:|---|---|
| `local-gpu-w01` | `coding` | Token-Bucket Rate Limiter (bd-ai-harness) | `false` | `1571.7` | `96` | **`5.62`** | `$0.00` | `/var/tmp/bd-headless-streams/local-gpu-w01.log` | ````python import time, threading  class TokenBucketLimiter:     def __init__(self, rate_pe` |
| `local-gpu-w02` | `coding` | O1740 Dynamic Pool Resolver (POOL_STATE.tsv) | `false` | `1567.8` | `76` | **`7.07`** | `$0.00` | `/var/tmp/bd-headless-streams/local-gpu-w02.log` | ````python def resolve_best_pool(rows: list[dict]) -> str:     pools = [r for r in rows if ` |
| `local-gpu-w03` | `bug-fix` | Fix SQLite WAL Busy-Timeout Race (bd-bus) | `false` | `1564.2` | `96` | **`5.62`** | `$0.00` | `/var/tmp/bd-headless-streams/local-gpu-w03.log` | ````python import sqlite3 from contextlib import contextmanager  @contextmanager def connec` |
| `local-gpu-w04` | `bug-fix` | Fix Exponential Backoff Overflow (bd_satellite_oracle) | `false` | `1564.3` | `61` | **`10.25`** | `$0.00` | `/var/tmp/bd-headless-streams/local-gpu-w04.log` | ````python def backoff_delay(attempt: int, base: float = 0.25, cap: float = 8.0) -> float: ` |
| `local-gpu-w05` | `ast-review` | AST Review: Pytest Digest Setup-Error vs SyntaxError (Rule 78) | `false` | `3697.5` | `96` | **`7.04`** | `$0.00` | `/var/tmp/bd-headless-streams/local-gpu-w05.log` | `- **AST structure**: `summary` is a single `str` parameter; the function body likely conta` |
| `local-gpu-w06` | `ast-review` | AST Security Audit: Rule 22 Worktree Shield Hook | `false` | `4921.1` | `96` | **`11.33`** | `$0.00` | `/var/tmp/bd-headless-streams/local-gpu-w06.log` | ````python import ast import os import sys  def _resolve_cwd(path: str) -> str:     """Reso` |
| `local-gpu-w07` | `tool-calling` | Tool-Call Dispatch: .82 64-Core Ramdisk Pytest Digest | `false` | `656.1` | `48` | **`10.67`** | `$0.00` | `/var/tmp/bd-headless-streams/local-gpu-w07.log` | `{"tool":"ramdisk_pytest_digest","args":{"host":"10.0.70.82","shm_dir":"/dev/shm/bd-ramdisk` |
| `local-gpu-w08` | `tool-calling` | Tool-Call Dispatch: Qdrant HiveMind Semantic Search | `false` | `656.5` | `66` | **`11.21`** | `$0.00` | `/var/tmp/bd-headless-streams/local-gpu-w08.log` | `{"tool": "qdrant_hivemind_search", "arguments": {"endpoint": "http://10.0.70.125:6333", "c` |

---

## 3. Workers `local-gpu-w09..w16` — `.137:11434` (`qwen2.5-coder:7b-16k` on BattleStation RTX 2080 Ti)

| Worker | Category | Task | TTFT (`ms`) | Kernel Prompt (`ms`) | Tokens | Kernel Eval (`tok/s`) | Cost | Stream Log | Output Snippet |
|---|---|---|---:|---:|---:|---:|---:|---|---|
| `local-gpu-w09` | `docstring` | Docstring: dispatch_heavy_lifter_seat (O1740) | `811.8` | `360.8` | `80` | **`77.43`** | `$0.00` | `/var/tmp/bd-headless-streams/local-gpu-w09.log` | ````python """ Dispatches a heavy lifter seat based on role and pool.  Args:     role (str)` |
| `local-gpu-w10` | `docstring` | Docstring: capture_cdp_owned_window_fps (.137/.181) | `29.5` | `12.2` | `57` | **`86.84`** | `$0.00` | `/var/tmp/bd-headless-streams/local-gpu-w10.log` | ````python """ Capture FPS of CDP-owned window.  Args:     host (str): The host address.   ` |
| `local-gpu-w11` | `docstring` | Docstring: verify_patch_sha256_gate (Rule 22/74) | `28.2` | `12.0` | `65` | **`88.48`** | `$0.00` | `/var/tmp/bd-headless-streams/local-gpu-w11.log` | ````python """ Verifies the SHA-256 gate for a patch.  Args:     worktree (Path): The path ` |
| `local-gpu-w12` | `type-signature` | Type Signatures: FleetBusPublisher & WalRecord | `37.4` | `15.3` | `61` | **`73.97`** | `$0.00` | `/var/tmp/bd-headless-streams/local-gpu-w12.log` | ````python from typing import TypedDict, Protocol, Dict  class WalRecord(TypedDict):     se` |
| `local-gpu-w13` | `type-signature` | Type Signatures: OracleConsensusMatrix & CdpMetrics | `4979.8` | `44.8` | `60` | **`44.42`** | `$0.00` | `/var/tmp/bd-headless-streams/local-gpu-w13.log` | ````python from typing import TypedDict  class CdpMetrics(TypedDict):     http_status: int ` |
| `local-gpu-w14` | `type-signature` | Type Signatures: GpuPoolTelemetrySnapshot | `2258.5` | `13.1` | `63` | **`91.89`** | `$0.00` | `/var/tmp/bd-headless-streams/local-gpu-w14.log` | ````python from dataclasses import dataclass  @dataclass(frozen=True) class GpuPoolTelemetr` |
| `local-gpu-w15` | `json-receipt` | JSON Receipt Synthesis: Rule 76 BOARD Verdict | `29.3` | `10.8` | `55` | **`93.23`** | `$0.00` | `/var/tmp/bd-headless-streams/local-gpu-w15.log` | ````json {   "round": "O1789-R2",   "seat": "review-correctness",   "verdict": "BOARD",   "` |
| `local-gpu-w16` | `json-receipt` | JSON Receipt Synthesis: RTX 2080 Ti Hardware Gate | `31.6` | `13.1` | `80` | **`93.93`** | `$0.00` | `/var/tmp/bd-headless-streams/local-gpu-w16.log` | ````json {   "node": "10.0.10.137:11434",   "gpu": "RTX 2080 Ti 11GB",   "model": "qwen2.5-` |

---

## 4. Workers `local-gpu-w17..w20` — `.228:11434` (`qwen2.5-coder:7b`) & `.125:8081/embeddings` (`bge-m3` 1024-dim)

| Worker | Task | `.228` TTFT (`ms`) | `.228` `tok/s` | `.125:8081` Vector Dim | Embed Latency (`ms`) | Vector Sample (`[0..3]`) | Qdrant Collections | Cost | Stream Log |
|---|---|---:|---:|---:|---:|---|---:|---:|---|
| `local-gpu-w17` | Vol 01 Cluster Topology Summary + 1024-dim BGE-M3 Embedding | `113.5` | `18.69` | **`1024`** | `554.2` | `[0.01343, -0.00453, -0.03421, 0.02807]` | `5` | `$0.00` | `/var/tmp/bd-headless-streams/local-gpu-w17.log` |
| `local-gpu-w18` | Vol 02 O1740 Dynamic Routing Summary + 1024-dim BGE-M3 Embedding | `63.9` | `17.08` | **`1024`** | `454.6` | `[-0.02718, -0.02749, -0.04426, 0.01562]` | `5` | `$0.00` | `/var/tmp/bd-headless-streams/local-gpu-w18.log` |
| `local-gpu-w19` | Vol 04 Dual Browser Oracle Summary + 1024-dim BGE-M3 Embedding | `96.0` | `16.73` | **`1024`** | `569.9` | `[-0.03983, 0.0084, -0.05491, 0.01967]` | `5` | `$0.00` | `/var/tmp/bd-headless-streams/local-gpu-w19.log` |
| `local-gpu-w20` | Vol 06 Sovereign Local GPU Summary + 1024-dim BGE-M3 Embedding | `158.4` | `20.11` | **`1024`** | `588.1` | `[-0.04197, -0.00633, -0.00317, -0.03752]` | `5` | `$0.00` | `/var/tmp/bd-headless-streams/local-gpu-w20.log` |

---

## 5. Workers `oracle-w01..w10` — Dual Browser Oracle (`.137:9222` BattleStation vs `.181:9222` Bittorrent) & Consensus

### 5.1 `.137:9222` vs `.181:9222` Pairwise Consensus Matrix

| Pair ID | `.137:9222` Worker | `.181:9222` Worker | Portals Verified (`http://` & `https://`) | `.137` Status | `.181` Status | Consensus Verdict |
|---|---|---|---|---|---|---|
| `pair-01` | `oracle-w01` | `oracle-w06` | Unified OS Shell (:4085), Dashy Hub (:4080), HTTPS Unified Ingress (:443) | **PASS** | **PASS** | **`BOARD (100% AGREE)`** |
| `pair-02` | `oracle-w02` | `oracle-w07` | 165-View 60fps Visualizer (:4088), 165-View Gallery (:4088/gallery) | **PASS** | **PASS** | **`BOARD (100% AGREE)`** |
| `pair-03` | `oracle-w03` | `oracle-w08` | Web Terminal & TV Wall (:4083), 86-Model Vault Storefront (:4084) | **PASS** | **PASS** | **`BOARD (100% AGREE)`** |
| `pair-04` | `oracle-w04` | `oracle-w09` | Trust, SSO & Root CA (:4082), Briefing, Search & Doctor (:4081) | **PASS** | **PASS** | **`BOARD (100% AGREE)`** |
| `pair-05` | `oracle-w05` | `oracle-w10` | Multi-Harness Studio (:4098), Cloud Burst Orchestrator (:4090), OpenWebUI Controller (:8090) | **PASS** | **PASS** | **`BOARD (100% AGREE)`** |

### 5.2 Per-Worker CDP Visual, DOM, Button/Tab Click & Live-Update Breakdown

| Worker | Oracle Node | Portal | URL | HTTP/TLS | Width Guard | Buttons | Interactive / Live Update Proof | Stream Log |
|---|---|---|---|---:|---|---:|---|---|
| `oracle-w01` | `137_BattleStation` | Unified OS Shell (:4085) | `http://10.0.70.164:4085/` | `200` | `1920<=1920` | `25` | `topbar_buttons=25,clicked=true` | `/var/tmp/bd-headless-streams/oracle-w01.log` |
| `oracle-w01` | `137_BattleStation` | Dashy Hub (:4080) | `http://10.0.70.164:4080/` | `200` | `1912<=1912` | `47` | `interactive_nodes=194` | `/var/tmp/bd-headless-streams/oracle-w01.log` |
| `oracle-w01` | `137_BattleStation` | HTTPS Unified Ingress (:443) | `https://10.0.70.164/` | `200` | `1920<=1920` | `25` | `proto=https:,tls_ignored_ok=true` | `/var/tmp/bd-headless-streams/oracle-w01.log` |
| `oracle-w02` | `137_BattleStation` | 165-View 60fps Visualizer (:4088) | `http://10.0.70.164:4088/` | `200` | `1920<=1920` | `56` | `views=165,wallboard_cycled=2x2->1x1,cot_ticker=true` | `/var/tmp/bd-headless-streams/oracle-w02.log` |
| `oracle-w02` | `137_BattleStation` | 165-View Gallery (:4088/gallery) | `http://10.0.70.164:4088/gallery` | `200` | `1905<=1905` | `188` | `gallery_elements=904` | `/var/tmp/bd-headless-streams/oracle-w02.log` |
| `oracle-w03` | `137_BattleStation` | Web Terminal & TV Wall (:4083) | `http://10.0.70.164:4083/` | `200` | `1905<=1905` | `168` | `tv_wall_live=true,filter_btns=168` | `/var/tmp/bd-headless-streams/oracle-w03.log` |
| `oracle-w03` | `137_BattleStation` | 86-Model Vault Storefront (:4084) | `http://10.0.70.164:4084/` | `200` | `1905<=1905` | `286` | `vault_86_models=true,tabs_clicked=3` | `/var/tmp/bd-headless-streams/oracle-w03.log` |
| `oracle-w04` | `137_BattleStation` | Trust, SSO & Root CA (:4082) | `http://10.0.70.164:4082/` | `200` | `1905<=1905` | `28` | `sso_buttons=28,qr_matrix_ready=true` | `/var/tmp/bd-headless-streams/oracle-w04.log` |
| `oracle-w04` | `137_BattleStation` | Briefing, Search & Doctor (:4081) | `http://10.0.70.164:4081/` | `200` | `1905<=1905` | `234` | `doctor_buttons=232,search_ready=true` | `/var/tmp/bd-headless-streams/oracle-w04.log` |
| `oracle-w05` | `137_BattleStation` | Multi-Harness Studio (:4098) | `http://10.0.70.164:4098/` | `200` | `1920<=1920` | `45` | `harness_tabs=45,clicked=true` | `/var/tmp/bd-headless-streams/oracle-w05.log` |
| `oracle-w05` | `137_BattleStation` | Cloud Burst Orchestrator (:4090) | `http://10.0.70.164:4090/` | `200` | `1905<=1905` | `14` | `cloud_burst_controls=18,clicked=true` | `/var/tmp/bd-headless-streams/oracle-w05.log` |
| `oracle-w05` | `137_BattleStation` | OpenWebUI Controller (:8090) | `http://10.0.70.164:8090/` | `200` | `1920<=1920` | `0` | `interactive_nodes=1` | `/var/tmp/bd-headless-streams/oracle-w05.log` |
| `oracle-w06` | `181_Bittorrent` | Unified OS Shell (:4085) | `http://10.0.70.164:4085/` | `200` | `1920<=1920` | `25` | `topbar_buttons=25,clicked=true` | `/var/tmp/bd-headless-streams/oracle-w06.log` |
| `oracle-w06` | `181_Bittorrent` | Dashy Hub (:4080) | `http://10.0.70.164:4080/` | `200` | `1912<=1912` | `47` | `interactive_nodes=194` | `/var/tmp/bd-headless-streams/oracle-w06.log` |
| `oracle-w06` | `181_Bittorrent` | HTTPS Unified Ingress (:443) | `https://10.0.70.164/` | `200` | `1920<=1920` | `25` | `proto=https:,tls_ignored_ok=true` | `/var/tmp/bd-headless-streams/oracle-w06.log` |
| `oracle-w07` | `181_Bittorrent` | 165-View 60fps Visualizer (:4088) | `http://10.0.70.164:4088/` | `200` | `1920<=1920` | `56` | `views=165,wallboard_cycled=2x2->1x1,cot_ticker=true` | `/var/tmp/bd-headless-streams/oracle-w07.log` |
| `oracle-w07` | `181_Bittorrent` | 165-View Gallery (:4088/gallery) | `http://10.0.70.164:4088/gallery` | `200` | `1905<=1905` | `188` | `gallery_elements=904` | `/var/tmp/bd-headless-streams/oracle-w07.log` |
| `oracle-w08` | `181_Bittorrent` | Web Terminal & TV Wall (:4083) | `http://10.0.70.164:4083/` | `200` | `1905<=1905` | `168` | `tv_wall_live=true,filter_btns=168` | `/var/tmp/bd-headless-streams/oracle-w08.log` |
| `oracle-w08` | `181_Bittorrent` | 86-Model Vault Storefront (:4084) | `http://10.0.70.164:4084/` | `200` | `1905<=1905` | `286` | `vault_86_models=true,tabs_clicked=3` | `/var/tmp/bd-headless-streams/oracle-w08.log` |
| `oracle-w09` | `181_Bittorrent` | Trust, SSO & Root CA (:4082) | `http://10.0.70.164:4082/` | `200` | `1905<=1905` | `28` | `sso_buttons=28,qr_matrix_ready=true` | `/var/tmp/bd-headless-streams/oracle-w09.log` |
| `oracle-w09` | `181_Bittorrent` | Briefing, Search & Doctor (:4081) | `http://10.0.70.164:4081/` | `200` | `1905<=1905` | `234` | `doctor_buttons=232,search_ready=true` | `/var/tmp/bd-headless-streams/oracle-w09.log` |
| `oracle-w10` | `181_Bittorrent` | Multi-Harness Studio (:4098) | `http://10.0.70.164:4098/` | `200` | `1920<=1920` | `45` | `harness_tabs=45,clicked=true` | `/var/tmp/bd-headless-streams/oracle-w10.log` |
| `oracle-w10` | `181_Bittorrent` | Cloud Burst Orchestrator (:4090) | `http://10.0.70.164:4090/` | `200` | `1920<=1920` | `14` | `cloud_burst_controls=18,clicked=true` | `/var/tmp/bd-headless-streams/oracle-w10.log` |
| `oracle-w10` | `181_Bittorrent` | OpenWebUI Controller (:8090) | `http://10.0.70.164:8090/` | `200` | `1920<=1920` | `0` | `interactive_nodes=0` | `/var/tmp/bd-headless-streams/oracle-w10.log` |
