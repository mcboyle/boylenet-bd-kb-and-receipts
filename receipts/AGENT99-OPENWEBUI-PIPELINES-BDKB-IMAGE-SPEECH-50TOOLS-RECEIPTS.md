# AGENT-99 RECEIPTS: OpenWebUI Pipelines Server, Image/Speech Model Suite, Master `BD KB` Knowledge Base & 50-Toolkit High-ROI Architecture

- **Timestamp (UTC)**: `2026-10-18T20:32:30Z`
- **Role**: `Agent-99`
- **Stream Log**: `/var/tmp/bd-headless-streams/agent99-pipelines-bdkb-50tools.log`
- **Target OpenWebUI Instances (100% Parity)**:
  1. **Controller (`.164:8090`)**: `http://openwebui.boylenet.themfboyles.com` (`http://10.0.70.164:8090`)
  2. **Vault A (`.72:8080`)**: `http://ai-webui.boylenet.themfboyles.com` (`http://10.0.70.72:8080`)
- **Universal Tool, BD KB & Pipelines Server (`.164:4079`)**:
  - Gateway Script: `/home/mboyle/bin/bd-openwebui-tool-gateway.py`
  - Extension Module: `/home/mboyle/bin/bd_openwebui_pipelines_bdkb_ext.py`
  - Master Synchronizer: `/home/mboyle/bin/bd-openwebui-sync-bdkb-pipelines-50tools.py`

---

## 1. Master `BD KB` (Boylenet & BulkDownloader Knowledge Base) — 6 Volumes & 3-Way Integration

### 1.1 Authored `BD KB` Volumes in `/home/mboyle/bd-persist/bd-kb/`
| Volume | File Path | Coverage |
|---|---|---|
| **#00** | `/home/mboyle/bd-persist/bd-kb/00-BD-KB-MASTER-ENCYCLOPEDIA.md` | Master index, 27-node topology summary, 86-model routing matrix, O1740 rules, 15+ web portals, and 50-toolkit map |
| **#01** | `/home/mboyle/bd-persist/bd-kb/01-BULKDOWNLOADER-ARCHITECTURE-AND-CONTRACTS.md` | `BulkDownloader` directory layout, `yt-dlp` pin invariant, Rule 22 sandbox paths (`bd-cuts/`, `bd-local-wt/`), Cut Workflow, and `/dev/shm` ramdisk pytest |
| **#02** | `/home/mboyle/bd-persist/bd-kb/02-BOYLENET-27NODE-CLUSTER-12U-RACK-VSAN-25GBE.md` | All 27 Boylenet nodes (`10.0.70.x` / `10.0.10.137`), 656 vCPUs, 1.4+ TB RAM, 12U 36-inch rack physical layout, VMware ESXi 8.0 / vSAN 18TB NVMe, UniFi 25GbE SFP28 fabric |
| **#03** | `/home/mboyle/bd-persist/bd-kb/03-86-MODEL-VAULT-BENCHMARKS-AND-AIRGAP.md` | Complete 86-model catalog across `.72`, `.83`, `.125` (4x Tesla T4), `.137` (RTX 2080 Ti), and `$1,000` Cloud Burst + Dual-Speed WAN Airgap (`AIRGAP-LOCKED` vs `CLOUD-BURST-ELIGIBLE`) |
| **#04** | `/home/mboyle/bd-persist/bd-kb/04-FLEET-LAW-RULES-1-86-AND-O1740-ROUTING.md` | Authoritative Fleet Rules 1–86, Protocols A–F, O1740 Dynamic Capability Routing (`POOL_STATE.tsv`, 30 global heavy-lifter cap), HUB-GATE (O1741), and Rule 86 Severance |
| **#05** | `/home/mboyle/bd-persist/bd-kb/05-CI-TAXONOMY-RECOVERY-AND-MCP-TOOLS.md` | BulkDownloader CI Failure Taxonomy (`C1`–`C6`), Great Train Recovery playbooks, 14 MCP servers, 128 skills/plugins, and 50 OpenWebUI Toolkits |

### 1.2 Three-Layer Live Integration
1. **Native OpenWebUI Knowledge Bases (`GET /api/v1/knowledge/` = `6` BD KB Collections on `.164:8090` & `.72:8080`)**:
   - `bd-kb-master` (`📚 [BD KB #00] Master Encyclopedia`) — all 6 volumes attached
   - `bd-kb-bulkdownloader` (`📦 [BD KB #01] BulkDownloader Architecture, Contracts, Cut Workflow & Ramdisk Pytest`)
   - `bd-kb-cluster-infra` (`🖧 [BD KB #02] Boylenet 27-Node Cluster, 12U Rack, VMware ESXi/vSAN & 25GbE Fabric`)
   - `bd-kb-86-models` (`🧠 [BD KB #03] 86-Model Vault Catalog, SWE-bench / LiveCodeBench & Dual-Speed Airgap`)
   - `bd-kb-fleet-law` (`⚖️ [BD KB #04] Fleet Law (Rules 1-86), O1740 Dynamic Routing & HUB-GATE`)
   - `bd-kb-ci-and-uis` (`🛠️ [BD KB #05] CI Taxonomy, Great Train Recovery, 14 MCP Servers & 50 OpenWebUI Toolkits`)
   - Attached to all workspace models and granted public read permissions (`principal_id="*"`).
2. **Qdrant 1024-Dim Vector Collection (`http://10.0.70.125:6333/collections/bd_master_kb`)**:
   - Upserted `28` semantic section chunks (`1024`-d hybrid BGE-M3 vectors) across all 6 books (`status: "ok"`, `verify_hits: 3`).
   - Registered `qdrant-bd-master-kb` (`bd_master_kb`) in OpenWebUI `external_knowledge.connections` on both `.164:8090` and `.72:8080`.
   - Fixed `/usr/local/bin/qdrant_portal.py` on `10.0.70.125` (`qdrant-portal.service`) to support `qdrant-client 1.18.0` `POST /collections/{name}/points/query` (`{"query": {"nearest": [...]}}`) and `Connection: close`.
3. **Gateway Endpoint & Native Toolkit `#31`**:
   - `POST http://10.0.70.164:4079/api/tool/bd_kb_search` + Native Toolkit `tk_31_bd_knowledge_base_oracle`.

---

## 2. OpenWebUI `Admin -> Tools -> Pipelines` & Multimodal Model Suite (`:4079/v1`)

Both `.164:8090` and `.72:8080` have `http://10.0.70.164:4079/v1` registered in `OPENAI_API_BASE_URLS` (`idx: 1`), verified via `GET /api/v1/pipelines/list` and `GET /api/v1/pipelines/?urlIdx=1`.

### 2.1 Eight Live Configurable Pipelines (`valves: true`, persisted to `/home/mboyle/bd-persist/openwebui-pipeline-valves.json`)
1. `bd_smart_o1740_intent_router_pipeline` — **🔀 [PIPELINE] Boylenet O1740 Smart Intent & Pool Router**
2. `bd_kb_auto_grounding_pipeline` — **📚 [PIPELINE • FILTER] BD KB Auto-RAG Grounding & Citation Injector** (`inlet`/`outlet` filter + pipe)
3. `bd_rule22_airgap_dlp_shield_pipeline` — **🛡️ [PIPELINE • FILTER] Rule 22 Anti-Hallucination & WAN Airgap DLP Shield**
4. `bd_multi_agent_council_consensus_pipeline` — **⚖️ [PIPELINE] Multi-Model Council Fanout & Protocol B Consensus**
5. `bd_autonomous_code_and_pytest_pipeline` — **🧪 [PIPELINE] Autonomous Code Architect + Ruff/Mypy + Ramdisk Pytest**
6. `bd_deep_research_multi_engine_pipeline` — **🔬 [PIPELINE] Deep Research Synthesis (SearXNG + ArXiv + Wiki + BD KB + Qdrant)**
7. `bd_multimodal_flux_vision_voice_pipeline` — **🎨🎙️ [PIPELINE] Multimodal Flux 1080p Image + Vision OCR + Neural TTS Studio**
8. `bd_langfuse_caveman_telemetry_filter` — **📊 [PIPELINE • FILTER] Langfuse Trace + Caveman Token Optimizer**

### 2.2 Six Local Image & Vision Generator Models (`/static/generated/<id>.png`)
1. `flux-1-schnell-1080p-local` (`🎨 [LOCAL • IMAGE GEN] flux-1-schnell-1080p-local`)
2. `sdxl-lightning-diagram-studio` (`📐 [LOCAL • IMAGE GEN] sdxl-lightning-diagram-studio`)
3. `qwen3-vl-30b-vision-inspector` (`👁️ [LOCAL • VISION + GEN] qwen3-vl-30b-vision-inspector`)
4. `gemma-4-31b-multimodal-studio` (`🖼️ [LOCAL • VISION + GEN] gemma-4-31b-multimodal-studio`)
5. `mermaid-svg-hd-blueprint-renderer` (`📊 [LOCAL • DIAGRAM GEN] mermaid-svg-hd-blueprint-renderer`)
6. `deepseek-ocr-document-vision-scanner` (`📄 [LOCAL • VISION OCR] deepseek-ocr-document-vision-scanner`)

### 2.3 Five Local Speech & Audio Models (`/static/audio/<id>.wav`)
1. `whisper-large-v3-turbo-local` (`🎙️ [LOCAL • SPEECH STT] whisper-large-v3-turbo-local`)
2. `kokoro-82m-neural-tts-local` (`🔊 [LOCAL • SPEECH TTS] kokoro-82m-neural-tts-local`)
3. `orpheus-3b-tts-local` (`🔊 [LOCAL • SPEECH TTS] orpheus-3b-tts-local`)
4. `qwen3-asr-audio-analyst-local` (`🎧 [LOCAL • AUDIO ASR] qwen3-asr-audio-analyst-local`)
5. `yue-acestep-music-studio-local` (`🎵 [LOCAL • MUSIC GEN] yue-acestep-music-studio-local`)

---

## 3. 50 High-ROI Native Toolkits (`#01`–`#50`) + 6 OpenAPI 3.1 Tool Server Suites

- **Toolkits `#01`–`#30`**: Preserved and verified active (`claude_codex_agy_code_tools`, `git_and_gitea_ops`, `mcp_universal_bridge`, `skills_and_plugins_runner`, `rag_and_hivemind_memory`, `web_search_and_fetch`, `browser_oracle_cdp`, `swarm_and_airgap_control`, `tk_09` through `tk_30`).
- **Toolkits `#31`–`#50` (Newly Engineered & Synchronized on `.164:8090` and `.72:8080`)**:
  - `tk_31_bd_knowledge_base_oracle` -> `/api/tool/bd_kb_search`
  - `tk_32_fleet_law_and_rule_auditor` -> `/api/tool/fleet_law_and_rule_auditor`
  - `tk_33_o1740_pool_and_seat_router` -> `/api/tool/o1740_pool_and_seat_router`
  - `tk_34_hivemind_persistent_memory_bank` -> `/api/tool/hivemind_persistent_memory_bank`
  - `tk_35_caveman_token_compressor` -> `/api/tool/caveman_token_compressor`
  - `tk_36_bd_bus_wal_and_smt_consensus` -> `/api/tool/bd_bus_wal_and_smt_consensus`
  - `tk_37_cut_train_and_worktree_inspector` -> `/api/tool/cut_train_and_worktree_inspector`
  - `tk_38_langfuse_and_litellm_telemetry` -> `/api/tool/langfuse_and_litellm_telemetry`
  - `tk_39_prometheus_grafana_loki_observability` -> `/api/tool/prometheus_grafana_loki_observability`
  - `tk_40_nfs_storage_and_blob_cas_manager` -> `/api/tool/nfs_storage_and_blob_cas_manager`
  - `tk_41_redis_state_and_cache_inspector` -> `/api/tool/redis_state_and_cache_inspector`
  - `tk_42_ssh_27node_parallel_fabric_runner` -> `/api/tool/ssh_27node_parallel_fabric_runner`
  - `tk_43_systemd_and_daemon_healer` -> `/api/tool/systemd_and_daemon_healer`
  - `tk_44_git_log_blame_and_diff_detective` -> `/api/tool/git_log_blame_and_diff_detective`
  - `tk_45_json_yaml_toml_schema_validator` -> `/api/tool/json_yaml_toml_schema_validator`
  - `tk_46_regex_and_jq_data_transformer` -> `/api/tool/regex_and_jq_data_transformer`
  - `tk_47_cryptography_hash_jwt_and_tls_inspector` -> `/api/tool/cryptography_hash_jwt_and_tls_inspector`
  - `tk_48_model_arena_and_speed_benchmarker` -> `/api/tool/model_arena_and_speed_benchmarker`
  - `tk_49_prompt_architect_and_skill_synthesizer` -> `/api/tool/prompt_architect_and_skill_synthesizer`
  - `tk_50_boylenet_doctor_and_self_healer` -> `/api/tool/boylenet_doctor_and_self_healer`

---

## 4. Live Verification Summary

| Check | `.164:8090` (Controller) | `.72:8080` (Vault A) | `:4079` Gateway / `:6333` Qdrant |
|---|---|---|---|
| `GET /api/v1/pipelines/list` | `http://10.0.70.164:4079/v1` (`idx: 1`) | `http://10.0.70.164:4079/v1` (`idx: 1`) | `pipelines: true` (`22` v1 models) |
| `GET /api/v1/pipelines/?urlIdx=1` | `8` Pipelines (`valves: true`) | `8` Pipelines (`valves: true`) | `8` Pipelines + Valves API |
| `GET /api/v1/knowledge/` (`bd-kb-*`) | `6` BD KB Collections (`6` files) | `6` BD KB Collections (`6` files) | `bd_master_kb` (`28` 1024-d vectors) |
| `GET /api/v1/tools/` (Native Toolkits) | `50` Native Toolkits (`#01`–`#50`) | `50` Native Toolkits (`#01`–`#50`) | `59` `/api/tool/*` POST handlers |
| `GET /api/v1/tools/` (Total w/ OpenAPI) | `56` (`50` native + `6` OpenAPI suites) | `56` (`50` native + `6` OpenAPI suites) | `6` OpenAPI 3.1 suites |
| Image/Speech Chat Completion | `200 OK` (`image/png` + `audio/wav`) | `200 OK` (`image/png` + `audio/wav`) | `/static/generated/*` & `/static/audio/*` |
