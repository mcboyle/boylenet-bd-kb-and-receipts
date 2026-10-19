# AGENT 97 — OpenWebUI Universal Tool, Skill, Plugin, Git, Web, Browser & MCP Importer Receipts (`O1789`)

- **Written At**: `2026-10-18T19:53:00Z`
- **Agent**: `AGENT97` (`10a4cce5-4b60-43bd-83bd-efed644e421a`)
- **Live Progress Stream**: `/var/tmp/bd-headless-streams/agent97-openwebui-tools-skills-mcp.log`
- **Host-Side Universal Gateway**: `/home/mboyle/bin/bd-openwebui-tool-gateway.py` (`bd-openwebui-tool-gateway.service` on `http://10.0.70.164:4079`)
- **OpenWebUI Synchronizer**: `/home/mboyle/bin/bd-openwebui-sync-tools-skills-mcp.py`

---

## 1. Executive Summary — End-to-End Verification Matrix

| Subsystem | Target / Endpoint | Deployed Artifacts | Live Verification Result (`rc=0`) |
|---|---|---|---|
| **1. Universal Tool, Git, Web, Browser & MCP Gateway (`:4079`)** | `http://10.0.70.164:4079` (`/health`, `/openapi.json`, `/api/tool/<action>`) | `/home/mboyle/bin/bd-openwebui-tool-gateway.py` + `/etc/systemd/system/bd-openwebui-tool-gateway.service` | `systemctl is-active` = `active` \| `17` REST/OpenAPI 3.1 endpoints \| `14` MCP servers (`99` MCP tools) \| `128` Skills/Plugins/Commands/Agents |
| **2. OpenWebUI Controller (`:8090`) — Native Toolkits (`tool` table)** | `http://10.0.70.164:8090/api/v1/tools/` | `8` Native Python Toolkits + `1` OpenAPI Tool Server (`9` total) | `8` Toolkits in `tool` table with parsed `specs` (`197B`–`2913B`) + public read grants (`principal_id="*"`) |
| **3. OpenWebUI Vault A (`.72:8080`) — Native Toolkits (`tool` table)** | `http://10.0.70.72:8080/api/v1/tools/` | `8` Native Python Toolkits + `1` OpenAPI Tool Server (`9` total) | `8` Toolkits in `tool` table with parsed `specs` (`205B`–`3071B`) + public read grants (`principal_id="*"`) |
| **4. OpenWebUI Skills & Plugins (`skill` table)** | `:8090/api/v1/skills/` & `.72:8080/api/v1/skills/` | `128` Active Skills (`47` `SKILL.md` skills & plugins + `10` Claude commands + `71` Codex agent profiles) | `.164:8090 active skills: 128` \| `.72:8080 active skills: 128` (`is_active=1`) |
| **5. OpenWebUI Slash Prompts (`prompt` table)** | `:8090/api/v1/prompts/` & `.72:8080/api/v1/prompts/` | `128` Slash Commands (`/<skill-slug>`) | `.164:8090 active prompts: 128` \| `.72:8080 active prompts: 128` (`is_active=1`) |
| **6. OpenWebUI Tool Server & SearXNG Web Search (`config` table)** | `:8090/api/v1/configs/tool_servers` & `.72:8080/api/v1/configs/tool_servers` | `http://10.0.70.164:4079` (`/openapi.json`) + SearXNG `http://10.0.70.164:8888/search?q=<query>&format=json` | `verified_tool_servers: 1` on both `:8090` and `.72:8080`; `web.search.enable = true` |

---

## 2. All 8 Native OpenWebUI Toolkits Imported into `.164:8090` & `.72:8080`

| # | Toolkit ID (`tool.id`) | Display Name | Exposed Methods for Local & Cloud Models | `.164:8090` `specs` Size | `.72:8080` `specs` Size |
|---:|---|---|---|---:|---:|
| 1 | `web_search_and_fetch` | 🌐 Web Search (SearXNG) & URL Markdown Reader | `search_web(query, max_results)`, `read_url_content(url, max_chars)` | `851 B` | `894 B` |
| 2 | `browser_oracle_cdp` | 🖥️ Live Chrome CDP Browser & Online Interactor (.137 / .181) | `browse_live_page_cdp(node, action, url, oracle_prompt)` | `689 B` | `724 B` |
| 3 | `git_and_gitea_ops` | 🐙 Git Clone, Pull, Diff, Log & Gitea/GitHub Bridge | `git_clone_or_pull(repo_url, branch)`, `git_status_diff_log(repo_path, action, ref, limit)`, `gitea_api(endpoint, query)` | `1,514 B` | `1,593 B` |
| 4 | `claude_codex_agy_code_tools` | 🛠️ Claude/Codex/Gemini Everyday File, Grep, Glob & Shell Tools | `view_file(file_path, start_line, end_line)`, `grep_search(pattern, path, max_count)`, `find_by_name(pattern, directory, max_depth)`, `write_or_edit_file(...)`, `run_command(command, cwd, timeout)` | `2,913 B` | `3,071 B` |
| 5 | `mcp_universal_bridge` | 🔌 Universal 14-Server MCP Bridge (Serena, RATF, PG, Qdrant, Ruff, Mypy, VMware) | `mcp_list(server)`, `mcp_call(server, tool, arguments_json)` (`14` servers / `99` tools) | `965 B` | `1,007 B` |
| 6 | `skills_and_plugins_runner` | 🧠 Claude, Codex & Gemini/AGY Skills & Plugins Loader (38+ Skills) | `list_skills(query, source)`, `load_skill(skill_id)` (`128` skills/plugins/commands/agents) | `794 B` | `830 B` |
| 7 | `rag_and_hivemind_memory` | 📚 Qdrant Vector RAG & PostgreSQL Episodic Memory Search | `qdrant_rag_search(query, collection, limit)`, `pg_query(sql)` | `913 B` | `956 B` |
| 8 | `swarm_and_airgap_control` | 🛡️ Subagent Delegation, GPU Model Switcher & Airgap Verifier | `airgap_and_cluster_status()` | `197 B` | `205 B` |

---

## 3. Raw Verification Output (`rc=0`)

### 3.1 Host Gateway `:4079/health` & Endpoint Smoke Tests
```json
{
  "status": "ok",
  "service": "bd-openwebui-tool-gateway",
  "port": 4079,
  "endpoints": [
    "/api/tool/search_web",
    "/api/tool/read_url_content",
    "/api/tool/browse_live_page_cdp",
    "/api/tool/git_clone_or_pull",
    "/api/tool/git_status_diff_log",
    "/api/tool/gitea_api",
    "/api/tool/view_file",
    "/api/tool/grep_search",
    "/api/tool/find_by_name",
    "/api/tool/write_or_edit_file",
    "/api/tool/run_command",
    "/api/tool/mcp_list",
    "/api/tool/mcp_call",
    "/api/tool/list_skills",
    "/api/tool/load_skill",
    "/api/tool/qdrant_rag_search",
    "/api/tool/airgap_and_cluster_status"
  ],
  "endpoint_count": 17,
  "mcp_server_count": 14,
  "mcp_total_tools": 99,
  "discovered_skills_and_plugins": 128,
  "timestamp": "2026-10-18T19:47:04Z"
}
```

```text
1. search_web: 3
2. read_url_content: True
3. browse_live_page_cdp: True
4. git_status_diff_log: True
5. gitea_api: True
6. view_file: True
7. grep_search: 5
8. find_by_name: 27
9. write_or_edit_file: True
10. run_command: hello openwebui
11. mcp_list: 14 99
12. mcp_call (pg-local): True
13. list_skills: 128
14. load_skill: True
15. qdrant_rag_search: True
16. airgap_and_cluster_status: True
```

### 3.2 OpenWebUI `.164:8090` & `.72:8080` Synchronization Receipts
```json
{
  "instance": "OpenWebUI-Controller-164:8090",
  "base_url": "http://127.0.0.1:8090",
  "toolkits_registered": 8,
  "skills_imported": 128,
  "prompts_imported": 128,
  "tool_servers_config_status": 200,
  "verified_tools_count": 9,
  "verified_skills_count": 128,
  "verified_prompts_count": 128,
  "verified_tool_servers": 1,
  "elapsed_s": 4.88
}
{
  "instance": "OpenWebUI-VaultA-72:8080",
  "base_url": "http://10.0.70.72:8080",
  "toolkits_registered": 8,
  "skills_imported": 128,
  "prompts_imported": 128,
  "tool_servers_config_status": 200,
  "verified_tools_count": 9,
  "verified_skills_count": 128,
  "verified_prompts_count": 128,
  "verified_tool_servers": 1,
  "elapsed_s": 8.05
}
```
