# AGENT-96 RECEIPT: Local vs Cloud Model Labeling & Filtering across OpenWebUI (`.164:8090` & `.72:8080`), Storefront (`:4084`), Multi-Harness Studio (`:4098`), and Boylenet OS Shell (`:4085`)

- **WRITTEN-AT**: `2026-10-18T19:58:00Z`
- **AGENT**: `Agent-96` (Local vs Cloud Model Labeling & Filtering Architect)
- **STREAM LOG**: `/var/tmp/bd-headless-streams/agent96-local-vs-cloud-labels.log`

---

## 1. OpenWebUI (`.164:8090` `http://openwebui.boylenet.themfboyles.com` & `.72:8080` `http://ai-webui.boylenet.themfboyles.com`) Model Labeling & Tagging

Every model in OpenWebUI on both `.164:8090` and `.72:8080` has been upserted into `/app/backend/data/webui.db` (`model` table + `access_grant` public read grants) and verified via `GET /api/models?refresh=true`:

| OpenWebUI Instance | Total DB Rows (`model`) | Live `/api/models` Total | `🟢 LOCAL` Labeled & Tagged | `☁️ CLOUD` Labeled & Tagged | Default Enabled Toolkits (`toolIds`) |
|---|---:|---:|---:|---:|---:|
| **Controller `.164:8090`** (`openwebui.boylenet.themfboyles.com`) | **260** | **259** | **247** (`🟢 LOCAL` + `⚡ Airgap-Ready`) | **12** (`☁️ CLOUD` + `🌐 Requires WAN`) | **8 / 8** (`web_search_and_fetch`, `browser_oracle_cdp`, `git_and_gitea_ops`, `claude_codex_agy_code_tools`, `mcp_universal_bridge`, `skills_and_plugins_runner`, `rag_and_hivemind_memory`, `swarm_and_airgap_control`) |
| **Vault A `.72:8080`** (`ai-webui.boylenet.themfboyles.com`) | **260** | **251** | **239** (`🟢 LOCAL` + `⚡ Airgap-Ready`) | **12** (`☁️ CLOUD` + `🌐 Requires WAN`) | **8 / 8** |

### Sample Live `/api/models` Entries
- `tier1-qwen3-coder-30b-a3b` -> `🟢 [LOCAL • .72 T4] tier1-qwen3-coder-30b-a3b (77.6 tok/s)` | Tags: `["🟢 LOCAL", "⚡ Airgap-Ready", "Vault A (.72)"]`
- `qwen3.6-35b-claude-opus-abliterated` -> `🟢 [LOCAL • .83 T4] qwen3.6-35b-claude-opus-abliterated (22.8 tok/s)` | Tags: `["🟢 LOCAL", "⚡ Airgap-Ready", "Vault B (.83)"]`
- `kimi-k3-abliterated-q2k-gguf` -> `🟢 [LOCAL • .72 T4] kimi-k3-abliterated-q2k-gguf (16.3 tok/s)` | Tags: `["🟢 LOCAL", "⚡ Airgap-Ready", "Vault A (.72)"]`
- `qwen2.5-coder-7b` -> `🟢 [LOCAL • .228 T4] qwen2.5-coder-7b (68.5 tok/s)` | Tags: `["🟢 LOCAL", "⚡ Airgap-Ready", "Ollama (.228)"]`
- `claude-opus-5-5` -> `☁️ [CLOUD • Anthropic] claude-opus-5-5 ($1,000 Credit)` | Tags: `["☁️ CLOUD", "🌐 Requires WAN", "Anthropic Cloud"]`
- `gpt-6.1-sol` -> `☁️ [CLOUD • OpenAI] gpt-6.1-sol (Codex Default)` | Tags: `["☁️ CLOUD", "🌐 Requires WAN", "OpenAI Codex"]`
- `kimi-k2.8` -> `☁️ [CLOUD • Moonshot] kimi-k2.8 (256K Reasoning)` | Tags: `["☁️ CLOUD", "🌐 Requires WAN", "Moonshot Cloud"]`

---

## 2. Boylenet UI Services (`:4084`, `:4098`, `:4085`) Updated & Verified

1. **86-Model AI Vault Storefront (`/home/mboyle/bin/bd-model-storefront.py` • `:4084`)**:
   - Added prominent **`🟢 100% LOCAL (On-Prem • Airgap-Ready)`** badge on all 86 Vault model cards (`models_86=86`).
   - Added **`☁️ Cloud Reference Models (Claude / Codex / Kimi) (12)`** comparison tab and **`🟢 Local (86)`** vs **`☁️ Cloud (12)`** filter buttons (`cloud_12=12`), showing each Cloud model's external API endpoint, WAN requirement, and on-prem Local Twin.
2. **Multi-Harness Studio (`/home/mboyle/bin/bd-multi-harness-web.py` • `:4098`)**:
   - Prefixed all **225** model options in `#model` and `#arenaJudgeModel` dropdowns and `/api/models` with **`🟢 [LOCAL]`** (211 models) or **`☁️ [CLOUD]`** (14 models).
3. **Boylenet Unified OS Desktop Shell (`/home/mboyle/bin/bd-os-shell.py` • `:4085`)**:
   - Prefixed all **86** Vault models in `Cmd+K` Spotlight and `/api/os-state` with **`🟢 [LOCAL]`** and added all **12** Cloud Reference models prefixed with **`☁️ [CLOUD]`** under dedicated **`🟢 Local Models (86)`** and **`☁️ Cloud Models (12)`** tabs.
4. **Lint & Service Health**:
   - Formatted and verified with `/home/mboyle/BulkDownloader/venv/bin/ruff format` and `ruff check --select F,E9` (`All checks passed!`).
   - Restarted and verified `bd-model-storefront.service`, `bd-multi-harness-web.service`, and `bd-os-shell.service` (`active`).
