# 🩺 BD-KB 5: Boylenet CI Failure Taxonomy, Gridlock Recovery, 14 MCP Servers & 50 OpenWebUI Toolkits

- **Document ID**: `bd-kb-05-ci-and-uis`
- **Canonical Path**: `/home/mboyle/bd-persist/bd-kb/05-CI-TAXONOMY-RECOVERY-AND-MCP-TOOLS.md`
- **Source Authority**: `/home/mboyle/.agents/skills/bd-ci-taxonomy/SKILL.md`, `/home/mboyle/bin/bd-openwebui-tool-gateway.py`, `/home/mboyle/bin/bd-openwebui-sync-tools-skills-mcp.py`

---

## 1. BulkDownloader CI Failure Taxonomy (`BD-CI-01` through `BD-CI-09`)

| Class ID | Failure Class | Error Signature | Canonical Remediation |
|---|---|---|---|
| **`BD-CI-01`** | **Hermetic Git Committer Identity** | `fatal: empty ident name (for <runner@...>) not allowed` (exit 128) | Pass explicit `-c user.name=Test -c user.email=test@example.com` to `git commit` (`Rule 41`); verify with `bd-git-hermetic-check` |
| **`BD-CI-02`** | **AST / Token Parse Bottlenecks** | `TimeoutExpired` (>900s) or `runner import hazard detected` | Add raw-text substring guard (`if "needle" not in raw: continue`) before `ast.parse()` (`Rule 42`); use `bdtools_cache.py` blob-SHA cache |
| **`BD-CI-03`** | **Subshell, Socket & Playwright Leaks** | `Playwright Sync API inside the asyncio loop` or `Too many open files` | Wrap Playwright in nested `try...finally` (`context.close()`, `browser.close()`, `pw.stop()`) and kill child process groups via `os.killpg()` (`Rule 45`) |
| **`BD-CI-04`** | **Fixture & Global State Contamination** | `sites configuration path changed after runtime activation` or dirty porcelain | Reset `bulk_downloader.app._SITE_RUNTIME_*` globals in `finally:` (`Rule 47`); add scratch dirs to `.gitignore` |
| **`BD-CI-05`** | **Async Marker File Races** | `ValueError: invalid literal for int() with base 10: ''` | Poll until `marker.is_file() and marker.read_text().endswith('\n')` or write `.tmp` + `os.replace()` (`Rule 44`) |
| **`BD-CI-06`** | **CI Matrix Shard Mismatch** | `Executable doesn't exist at /home/runner/.cache/ms-playwright/...` | Move browser tests to `download-chain` or `template-selectors` shards; keep `application-safety` pure-Python (`Rule 46`) |
| **`BD-CI-07`** | **Generated Artifact Drift** | `FUNCTION_INDEX.md is out of sync` or dirty `PIN_INDEX.json` / `STATIC_KB_MANIFEST.json` | Run `venv/bin/python toolchain/bin/bd-regen-order --work "$PWD"` before committing |
| **`BD-CI-08`** | **Collection-Phase Import Abort** | `ERROR collecting tests/test_*.py` with `ModuleNotFoundError` (exit 2) | Defer import of new symbols to call-phase helper `_get_symbol()` so base replay yields behavioral `FAILED` (exit 1), never collection `ERROR` (exit 2) |
| **`BD-CI-09`** | **Subprocess Inline String Escaping** | `SyntaxError: unterminated string literal` in `python -c` | Use raw string `r"""..."""` or double-escape `\\n` in inline scripts |

---

## 2. All 15 Boylenet MCP Servers (`119` Tools Bridged via `:4079` & Synced Across 24 VMs)

| # | MCP Server | Transport / Endpoint | Key Tools |
|---:|---|---|---|
| 1 | `bd` | HTTP `http://10.0.70.164:8100/mcp` | `ask`, `brief`, `claims`, `collect_gate`, `cut_status`, `evidence`, `fleet_hosts`, `gitea_ci`, `landed`, `lane_status`, `law`, `limits`, `log_slice`, `more`, `queue_rows`, `role_state_get`, `role_state_set`, `row`, `say`, `tool_doc`, `usage`, `who_all` |
| 2 | `bd-bus` | stdio `python3 -m bd_bus_mcp` | `bus_publish`, `bus_read`, `bus_summarize`, `bus_record_assertion`, `smt_verify_invariant`, `bus_check_quorum`, `smt_evaluate_consensus` |
| 3 | `bd-fleet` | stdio `bd-fleet-mcp/server.py` | `invoke_subagent`, `landed_by_content`, `mcp_discover`, `mcp_hydrate`, `premise_verify`, `role_state_get`, `role_state_set`, `say_validate` |
| 4 | `bd-fleet-mcp_bd-fleet` | stdio `bd-fleet-mcp/server.py` | Fleet premise and role state validation mirror |
| 5 | `boylenet-infra` | stdio `/home/mboyle/bin/bd-boylenet-infra-mcp.py` | **20 Universal Cluster Tools**: `vm_exec`, `vm_browse_files`, `local_coding_agent_fix`, `ramdisk_pytest_digest`, `browser_oracle_query`, `browser_oracle_cdp_inspect`, `bd_kb_search`, `qdrant_hivemind_search`, `qdrant_hivemind_store`, `model_vault_catalog_and_switch`, `raw_unrestricted_llm_call`, `airgap_control`, `pool_quota_router`, `spawn_cross_pool_subagent`, `vsan_esxi_cluster_status`, `unifi_dns_traefik_status`, `openwebui_tool_gateway_call`, `gitea_github_repo_sync`, `sentinel_doctor_autoheal`, `queue_backlog_status`, `https_443_status` |
| 6 | `caveman` | stdio `/home/mboyle/.caveman/bin/caveman-mcp` | `caveman_compress`, `caveman_retrieve`, `caveman_stats`, `caveman_toon_encode`, `caveman_toon_decode` |
| 7 | `context-mode` | stdio `npx -y context-mode` | `ctx_execute`, `ctx_execute_file`, `ctx_index`, `ctx_search`, `ctx_fetch_and_index`, `ctx_batch_execute`, `ctx_stats`, `ctx_doctor`, `ctx_upgrade`, `ctx_purge`, `ctx_insight` |
| 8 | `fleet-sync` | stdio `fleet_sync_mcp.py` | `publish_lesson` |
| 9 | `hivemind-memory` | stdio `hivemind-memory/server.py` | `memory_recall`, `memory_store`, `memory_status` |
| 10 | `pg-local` | PostgreSQL `127.0.0.1:5432/ai_mesh` | `query` |
| 11 | `python-linter` | stdio `mcp_server_ruff` | `ruff_check`, `ruff_format` |
| 12 | `ratf` | stdio `mcp_server_ratf` | `ctx_slice`, `ctx_deref`, `ctx_resolve` |
| 13 | `serena` | stdio `uvx serena` | `find_symbol`, `find_referencing_symbols`, `get_symbols_overview`, `replace_symbol_body`, `insert_after_symbol`, `insert_before_symbol`, `rename_symbol` |
| 14 | `type-enforcer` | stdio `mcp_server_mypy` | `mypy_check` |
| 15 | `vmware-clones` | stdio `mcp_server_vmware` (`10.0.70.1`) | `vm_list`, `vm_info`, `vm_clone`, `vm_destroy` |

---

## 3. All 8 OpenWebUI Pipelines on `http://10.0.70.164:4079/v1`

1. `bd_kb_auto_grounding_pipeline` (`filter`): Automatically retrieves top matching `BD KB` chunks and injects grounded citations when prompts mention Boylenet, BulkDownloader, Fleet Law, or cluster services.
2. `bd_smart_o1740_intent_router_pipeline` (`pipe`): Classifies prompt intent (coding, offensive security, vision, math/reasoning, architecture) and routes to the #1 benchmarked local model (`Qwen3-Coder-30B-A3B` at `77.62 tok/s`, `CyberStrike-35B` at `66.47 tok/s`, `Qwythos-9B` at `1,005.86 tok/s`, etc.).
3. `bd_deep_research_multi_engine_pipeline` (`pipe`): Autonomous multi-source research combining SearXNG (`:8888`), ArXiv, Wikipedia, `BD KB`, and Qdrant (`:6333`) into a cited synthesis report.
4. `bd_4model_consensus_council_pipeline` (`pipe`): Queries multiple local models in parallel and synthesizes a consensus ruling with agreement scoring.
5. `bd_airgap_and_rule22_shield_pipeline` (`filter`): Audits prompts/commands against `bd-airgap` WAN status and Rule 22 destructive-git prevention.
6. `bd_code_quality_ruff_mypy_pytest_pipeline` (`filter`): Automatically lints Python code blocks in responses using `ruff` and `mypy`.
7. `bd_multimodal_flux_vision_voice_pipeline` (`pipe`): Generates 1080p/1024px images, HUD diagrams, vision OCR breakdowns, and synthesized WAV speech audio.
8. `bd_token_telemetry_langfuse_pipeline` (`filter`): Appends live `tok/s` decode speed, `[LOCAL]`/`[CLOUD]` provenance badge, and logs episodic trace metadata.

---

## 4. All 50 Native OpenWebUI Toolkits (`Toolkits #01`–`#50`)

- **Toolkits `#01`–`#30` (Core Fleet & Developer Super-Suite)**: Web Search (`SearXNG`), URL Markdown Reader, Chrome CDP Browser Oracles (`.137`/`.181`), Git Clone/Pull/Diff/Log, Gitea REST API, File Read/Write/Grep/Glob, Sandboxed Shell & Python Runner, `.82` 64-Core Ramdisk Pytest Runner, Ruff & Mypy Enforcer, RATF & Serena AST Slicer, Qdrant Vector RAG, Hivemind PostgreSQL Memory, 15-Server MCP Bridge, 128 Skills Loader, 4-GPU Orchestrator (`:4010`), `bd-airgap` Switcher, `bd-raw-ai` Bridge, VMware ESXi/vSAN Inspector, 25GbE & 398-DNS Probe, 4-Model Consensus Council, `llama-bench` Query, CyberStrike Security Auditor, ArXiv/Wiki Research, CSV/SQL Data Analyst, Mermaid/SVG Diagrammer, Caveman Compressor, Boylenet 165-View Portal Navigator.
- **Toolkits `#31`–`#50` (High-ROI Specialist & Infrastructure Suite)**:
  - `31_bd_knowledge_base_oracle`: Sub-20ms full-text & semantic search across all `BD KB` volumes (`00`–`09`) + Qdrant `bd_master_kb`.
  - `32_playwright_live_web_scraper`: Headless Playwright DOM scraper, table extractor & screenshotter.
  - `33_deep_research_multi_engine`: Parallel SearXNG + ArXiv + Wikipedia + GitHub + `BD KB` deep research synthesizer.
  - `34_bulkdownloader_cli_and_ytdlp_inspector`: BulkDownloader (`bd`) extractor, route catalog, `PIN_INDEX.json`, and `yt-dlp` inspector.
  - `35_postgres_pgvector_memory_vault`: PostgreSQL `ai_mesh` & `pgvector` SQL analytics and long-term memory store.
  - `36_redis_minio_s3_object_storage`: MinIO S3 (`:9000`) bucket inspector & Redis (`:6379`) cache diagnostics.
  - `37_prometheus_grafana_loki_telemetry`: Prometheus (`:9090`) PromQL query tool & Grafana (`:3001`) dashboard inspector.
  - `38_langfuse_litellm_spend_analytics`: LiteLLM (`:4000`) & Langfuse (`.228:3000`) token spend, cache hit, and latency analyzer.
  - `39_ssh_27node_parallel_fleet_runner`: 27-Node parallel SSH diagnostic & health probe runner.
  - `40_ilo4_ipmi_physical_rack_sensors`: HPE DL380 Gen10 iLO 4 thermal (`79.5°F`), fan RPM, PSU wattage & 12U rack sensor reader (`:4091`).
  - `41_searxng_news_rss_feed_monitor`: Live tech/security news, CVE & RSS threat-intel aggregator.
  - `42_python_jupyter_notebook_generator`: Automated Jupyter Notebook (`.ipynb`) & self-contained interactive HTML report builder.
  - `43_regex_cron_jwt_crypto_swiss_army`: Developer Swiss-Army Knife (Regex tester, JWT decoder, Cron parser, SHA256/Base64/UUID).
  - `44_openapi_rest_graphql_webhook_tester`: HTTP/REST/GraphQL API client & latency benchmark tool.
  - `45_git_worktree_and_patch_auditor`: Git `PATCH-SHA256` verifier (`Rule 16`), diff stat analyzer & worktree census (`:4071`).
  - `46_huggingface_gguf_quant_calculator`: GGUF quantization VRAM/RAM calculator & `.72`/`.83` vault shard inspector (`:4076`).
  - `47_ocr_vision_pdf_table_extractor`: Local Vision VQA, image OCR & PDF/HTML table-to-Markdown extractor.
  - `48_audio_whisper_tts_music_studio`: Local Whisper STT transcription, Neural TTS WAV synthesis & audio analyzer.
  - `49_flux_1080p_image_and_hud_generator`: Local Flux / 1080p architecture diagram, HUD mockup & PNG image generator.
  - `50_boylenet_doctor_and_self_healer`: 1-Click Boylenet 31-Service Sentinel Doctor (`:4081` / `:4099`) & auto-healer.

---

## 5. Complete Catalog of `300+` `/home/mboyle/bin/bd*` Tools, `39` Skills & `9` Plugins (`O3394` 24-VM Parity)

Under **`O3394`** (`O3394-FLEET-24VM-PLATFORM-PLUGINS-SKILLS-MODS-PARITY-RECEIPT.md`), all **24 active Linux VMs** in `/home/mboyle/.config/bd/hosts` are synchronized with **`>= 299–300` `/home/mboyle/bin/bd*` executable scripts**, **`39` `.agents/skills`**, **`9` `.agents/plugins`**, and **`15` MCP servers**:

### 5.1 Categorized Directory of `300+` `/home/mboyle/bin/bd*` Executables
1. **Local AI, GPU Orchestration & Inference Engines (`~45` scripts)**:
   - `bd-gpu-orchestrator.py` (`:4010` Sovereign Local Gateway, `256K`–`1M` `<think>` router), `bd-tier-router.py` (Single-Node No-RPC & Socket-0 NUMA law), `bd-serialized-opt-pipeline.py` (`Stages 0–5`), `bd-local-coding-agent` / `bd-local-coding-agent.py` (`.83:18080` `Qwen3.6-Heretic` + `.137:11434` `RTX 2080 Ti` self-healing coding agent), `bd-raw-ai` (unrestricted direct GGUF CLI), `bd-airgap` (3-layer kernel/LiteLLM/CloudBurst WAN kill-switch), `bd-kv-warmer` (KV-prefix pinning daemon), `bd-prewarm-daemon`, `bd-satellite-router`, `bd-apply-vnuma-52vcpu.sh`, `bd-esxi-numa-vmx-enforce.sh`.
2. **5-Platform MCP, Swarm Mods & Cross-Pool Subagent Dispatchers (`~55` scripts)**:
   - `bd-boylenet-infra-mcp.py` (`20`-tool universal MCP server), `bd-swarm-mod` / `bd-swarm-mod.py` (Mods 1–5 CLI & hooks), `bd-subagent-router.sh` (O1740 cross-pool launcher), `bd-cross-platform-swarm-assist.py`, `bd-autonomous-swarm-driver.py`, `bd-relief-puppeteer.py`, `bd-claude-28worker-blitz.py`, `bd-codex-28worker-blitz.py`, `bd-kimi-24worker-blitz.py`, `bd-local-20gpu-10oracle-blitz.py`, `bd-30squad-32ramdisk-blitz.py`, `bd-master-230-swarm-ledger.py`.
3. **`.82` 64-Core `/dev/shm` Ramdisk Pytest, Precut, AST & Backlog Verifiers (`~60` scripts)**:
   - `bd-pytest-digest` (`<=3` lines, `<25` tokens, Rule 78), `bd-remote-pytest-82` (`.82` `/dev/shm` tmpfs runner), `bd-test`, `bd-blob-ptr` (Rule 80 CAS pointer decoupler), `bd-ast-diff`, `bd-ci-taxonomy`, `bd-git-hermetic-check`, `bd-o1670-backlog-runner.py` (`69/69` `O1670` verifier), `bd-p2-appdl-verifier`, `bd-backlog-reconciler`, `bd-compound-exec` (Rule 60).
4. **Web Portals (`:4079`–`:4099`), OpenWebUI Super-Suite & NGINX `:443` Cloudflare HTTPS (`~55` scripts)**:
   - `bd-openwebui-tool-gateway.py` (`:4079` 50-toolkit + 8-pipeline + 28-VM terminal gateway), `bd-openwebui-sync-tools-skills-mcp.py`, `bd-web-terminal-drawer.py` (`:4083` 116-tile Terminal TV Wall), `bd-letsencrypt-cloudflare` (Cloudflare DNS-01 wildcard TLS provisioner), `bd-https-443-e2e-verifier.py` (`211/211` HTTPS checks), `bd-opencode-web` (`:4096`), and portal daemons for `:4080`–`:4099`.
5. **VMware vCenter/ESXi/vSAN/25GbE RoCE, Dual Browser Oracles & Telemetry (`~85` scripts)**:
   - `lib_vsphere_govc.py`, `bd-oracle` (`.137:9222` BattleStation & `.181:9222` Bittorrent Chrome CDP fanout, Rule 79), `bd-telemetry-daemon.py` (Rule 81 zero-token telemetry), `bd-telemetry-guard.py`, `bd-binary-payload`, `bd-doctor`, `bd-sentinel-healer`.

### 5.2 Complete List of All `39` Skills (`/home/mboyle/.agents/skills/`) & `9` Plugins (`/home/mboyle/.agents/plugins/`)
- **All `39` Agent Skills**:
  1. `bd-backlog-reconciler`
  2. `bd-ci-taxonomy`
  3. `bd-o1670-backlog-burndown`
  4. `bd-oracle-suite-e2e`
  5. `bd-p2-appdl-verifier`
  6. `bd-swarm-mod`
  7. `boylenet-4platform-bridge`
  8. `boylenet-architect`
  9. `boylenet-autonomous-infra`
  10. `boylenet-autonomous-mods`
  11. `boylenet-bdkb-hivemind`
  12. `boylenet-dual-oracle-e2e`
  13. `boylenet-infra-commander`
  14. `boylenet-local-coding-agent`
  15. `cavecrew`
  16. `caveman`
  17. `caveman-commit`
  18. `caveman-compress`
  19. `caveman-discover`
  20. `caveman-evidence-review`
  21. `caveman-explore`
  22. `caveman-help`
  23. `caveman-learn`
  24. `caveman-manage`
  25. `caveman-optimize`
  26. `caveman-review`
  27. `caveman-setup`
  28. `caveman-stats`
  29. `cdp-frame-scroll-controls`
  30. `find-skills`
  31. `fleet-gridlock-recovery`
  32. `fleet-satellite-orchestration`
  33. `investigate-first`
  34. `lean-build`
  35. `migration`
  36. `safe-refactor`
  37. `surgical-patch`
  38. `synced`
  39. `verify-and-stop` *(plus AGY builtin `agy-customizations`, `antigravity-guide`, and plugin skills `bd-agy-dispatch`, `bd-dispatch-ops`, `bd-fleet-mcp`, `bd-harness-cut`, `bd-lens`, `context-guard`, `rebase-orchestrator`)*.
- **All `9` Fleet Plugins**:
  1. `bd-autonomous-infra` (`/home/mboyle/.agents/plugins/bd-autonomous-infra`)
  2. `bd-browser-oracle` (`/home/mboyle/.agents/plugins/bd-browser-oracle`)
  3. `bd-context-guard` (`/home/mboyle/.agents/plugins/bd-context-guard`)
  4. `bd-cut-hygiene` (`/home/mboyle/.agents/plugins/bd-cut-hygiene`)
  5. `bd-dispatch-ops` (`/home/mboyle/.agents/plugins/bd-dispatch-ops`)
  6. `bd-fleet-mcp` (`/home/mboyle/.agents/plugins/bd-fleet-mcp`)
  7. `bd-lens` (`/home/mboyle/.agents/plugins/bd-lens`)
  8. `bd-rebase` (`/home/mboyle/.agents/plugins/bd-rebase`)
  9. `plugin_data` / `bd-swarm-mod-hooks` (`/home/mboyle/.gemini/antigravity-cli/plugin_data` & Claude/Codex/Kimi hook packs).

---

## 6. Universal Multi-Repository Dispatch Architecture (`BD`, `bd-fleet-harness`, `jarvisos_cockpit`, `UniversalSwarmOS`)

The Boylenet Universal Harness (`/home/mboyle/bd-persist/harness/bd-launch-role.sh`, `/home/mboyle/bin/bd-subagent-router.sh`, `/home/mboyle/bin/bd-boylenet-infra-mcp.py`, and `/home/mboyle/bin/bd-local-coding-agent.py`) dispatches and verifies work seamlessly across all 4 core repositories and the 4 published companion repositories:

| Target Repository | Canonical Checkout / Worktree Root | Dispatch Adapter & Gate Pipeline | Verification & Landing Contract |
|---|---|---|---|
| **`BD` (`BulkDownloader`)** | `/home/mboyle/BulkDownloader` (Worktrees in `/home/mboyle/bd-cuts/` & `/home/mboyle/bd-local-wt/`) | `bd-launch-role.sh` / `bd-subagent-router.sh` / `bd-local-coding-agent.py` (`O1740` pool resolution + `O3409` local/codex first-finisher -> Claude lens) | `ruff_check` + `mypy_check` + `.82` 64-core `/dev/shm` `bd-pytest-digest` + `bd-regen-order` + `bd-precut --gate` + `PATCH-SHA256` (`Rule 16`) |
| **`bd-fleet-harness`** | `/home/mboyle/bd-persist/harness` (`bd-harness-cut` skill) | Dispatched via `bd-harness-cut` (`FIX` candidate shape with paired regression control) onto `.51`/`.54`/`.84`/`.194` harness lanes | Shell/Python syntax & unit check (`bash -n`, `ruff`, `pytest`), `bd-say.sh` / `bd-role-claim.sh` contract verification, and atomic sync to all 24 VMs |
| **`jarvisos_cockpit`** | `/home/mboyle/jarvisos_cockpit` & `:3002` / `:4090` (`https://jarvis.boylenet.themfboyles.org`) | Dispatched via `boylenet-infra` MCP (`vm_exec`, `browser_oracle_cdp_inspect`) and Kimi/Codex/Local GPU UI/UX workers | Live HTTP `200` + Dual Chrome CDP Browser Oracle (`.137:9222` & `.181:9222`) DOM/console/WebSocket verification (`bd-oracle-suite-e2e`) |
| **`UniversalSwarmOS`** | `/home/mboyle/UniversalSwarmOS` & `/home/mboyle/git-repos/UniversalSwarmOS` | Integrator-owned (`O1448`) multi-pool orchestration repo; staged in isolated worktrees and verified via `bd-swarm-mod verify` | Full `ruff` + `mypy` + pytest gate + `BOYLENET_SECRET_GUARD` pre-commit scan + dual push to GitHub (`mcboyle/UniversalSwarmOS`) & Local Gitea (`:3000`) |

