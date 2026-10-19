# AGENT-51: Langfuse LLM Observability + Prometheus + Grafana AI & GPU Telemetry Nexus Receipts

- **Timestamp (UTC)**: `2026-10-18T15:44:00Z`
- **Agent**: `Agent-51` (Swarm 3: Langfuse `.228:3000` + Prometheus `.164:9090` + Grafana `.164:3001` AI & GPU Telemetry Architect)
- **Status**: `PASS` (All endpoints verified with `rc=0`)

---

## 1. Langfuse LLM Observability (`http://10.0.70.228:3000` / `http://langfuse.boylenet.themfboyles.com`)

### Container & Database Deployment
- Replaced the static stub on `10.0.70.228:3000` (`langfuse-bridge.service`) with the production `langfuse/langfuse:2` (`v2.95.11`) Docker container (`--name langfuse --restart always -p 3000:3000`), backed by PostgreSQL (`postgresql://postgres:postgres@10.0.70.164:5432/langfuse`).
- Seeded and verified operator admin logins, default Organization, Project, and static API keys in PostgreSQL (`langfuse` database):
  - **Organization**: `Boylenet` (`id: boylenet-org`)
  - **Project**: `Boylenet Swarm OS` (`id: boylenet-swarm-os`)
  - **Admin Logins (Password: `boylenet-admin`)**:
    1. `mcboyle@me.com` / `boylenet-admin` (`admin=t`, Role: `OWNER`)
    2. `admin@localhost` / `boylenet-admin` (`admin=t`, Role: `OWNER`)
  - **Static API Key Pair**:
    - `LANGFUSE_PUBLIC_KEY`: `pk-lf-boylenet-local`
    - `LANGFUSE_SECRET_KEY`: `sk-lf-boylenet-local`
    - `LANGFUSE_HOST`: `http://10.0.70.228:3000`

### PostgreSQL & NextAuth Login Receipt (`rc=0`)
```text
        id        |      email      |      name      | admin 
------------------+-----------------+----------------+-------
 user-mcboyle     | mcboyle@me.com  | Matthew Boyle  | t
 user-admin-local | admin@localhost | Boylenet Admin | t
(2 rows)

      id      |   name   
--------------+----------
 boylenet-org | Boylenet
(1 row)

        id         |       name        |    org_id    
-------------------+-------------------+--------------
 boylenet-swarm-os | Boylenet Swarm OS | boylenet-org
(1 row)

GET http://10.0.70.228:3000/api/public/projects (Basic Auth pk-lf-boylenet-local:sk-lf-boylenet-local)
HTTP/1.1 200 OK
{"data":[{"id":"boylenet-swarm-os","name":"Boylenet Swarm OS"}]}

POST http://10.0.70.228:3000/api/auth/callback/credentials (mcboyle@me.com / boylenet-admin)
HTTP/1.1 200 OK
Session: {"user":{"name":"Matthew Boyle","email":"mcboyle@me.com","organizations":[{"id":"boylenet-org","name":"Boylenet","role":"OWNER","projects":[{"id":"boylenet-swarm-os","name":"Boylenet Swarm OS","role":"OWNER"}],"plan":"oss"}]}}
```

### LiteLLM Gateway Integration (`10.0.70.164:4000` & `10.0.70.228:4000`)
- Wired `LANGFUSE_PUBLIC_KEY=pk-lf-boylenet-local`, `LANGFUSE_SECRET_KEY=sk-lf-boylenet-local`, `LANGFUSE_HOST=http://10.0.70.228:3000`, and `success_callback: ["langfuse"]` into both LiteLLM gateways (`.164:4000` and `.228:4000`).
- Live `/v1/chat/completions` calls against both `.164:4000` and `.228:4000` verified automatic trace ingestion (`litellm-acompletion`) in Langfuse:
```text
Total traces: 4
 - bb9493c1-8826-4665-97d4-d9dd956cc92c litellm-acompletion 2026-10-18T15:43:52.060Z (from .228:4000)
 - 4b56ba1e-5edb-4f87-8f4a-55f3fc4ec925 litellm-acompletion 2026-10-18T15:43:50.571Z (from .164:4000)
 - trace-litellm-sdk-verify litellm-proxy-trace 2026-10-18T15:42:45.440Z
 - trace-boylenet-init-1 boylenet-health-trace 2026-10-18T15:36:27.588Z
```

---

## 2. Prometheus Metrics & Exporters (`http://10.0.70.164:4010/metrics` & `http://10.0.70.164:9090`)

- Added `/metrics` handler to `/usr/local/bin/bd-gpu-orchestrator.py` (`:4010/metrics`) and registered scrape job `boylenet-ai-orchestrator` (`host.docker.internal:4010`) in `/home/mboyle/infra/support-layer/prometheus.yml`.
- Also added the 4th Tesla T4 node (`aidev` / `10.0.70.83`) to `/home/mboyle/infra/scripts/mesh_exporter.py` (`:9105/metrics`).

### Prometheus Scrape Proof (`http://10.0.70.164:9090/api/v1/query`)
```text
--- PromQL: boylenet_pool_quota_used_pct ---
status: success count: 8
  {'pool': 'A', 'state': 'OK'} => 0
  {'pool': 'B', 'state': 'OK'} => 0
  {'pool': 'C', 'state': 'OK'} => 0
  {'pool': 'D', 'state': 'OK'} => 0
  {'pool': 'codex', 'state': 'OK'} => 0
  {'pool': 'AGY', 'state': 'OK'} => 18.5338
  {'pool': 'local', 'state': 'OK'} => 0
  {'pool': 'kimi', 'state': 'OK'} => 0

--- PromQL: boylenet_pool_weekly_pct ---
status: success count: 8
  {'pool': 'A', 'state': 'OK'} => 2
  {'pool': 'B', 'state': 'OK'} => 19
  {'pool': 'C', 'state': 'OK'} => 0
  {'pool': 'D', 'state': 'OK'} => 13
  {'pool': 'codex', 'state': 'OK'} => 0
  {'pool': 'AGY', 'state': 'OK'} => 26.8882
  {'pool': 'local', 'state': 'OK'} => 0
  {'pool': 'kimi', 'state': 'OK'} => 40

--- PromQL: boylenet_gpu_vram_used_mib ---
status: success count: 4
  {'gpu': 'Tesla T4', 'host': 'aidev', 'ip': '10.0.70.83', 'node': '83'} => 819
  {'gpu': 'Tesla T4', 'host': 'ai-srv01', 'ip': '10.0.70.72', 'node': '72'} => 1421
  {'gpu': 'Tesla T4', 'host': 'ai-ollama01', 'ip': '10.0.70.228', 'node': '228'} => 1125
  {'gpu': 'Tesla T4', 'host': 'ai-infer01', 'ip': '10.0.70.125', 'node': '125'} => 707

--- PromQL: boylenet_gpu_utilization_pct ---
status: success count: 4
  {'gpu': 'Tesla T4', 'host': 'aidev', 'ip': '10.0.70.83', 'node': '83'} => 4
  {'gpu': 'Tesla T4', 'host': 'ai-srv01', 'ip': '10.0.70.72', 'node': '72'} => 5
  {'gpu': 'Tesla T4', 'host': 'ai-ollama01', 'ip': '10.0.70.228', 'node': '228'} => 4
  {'gpu': 'Tesla T4', 'host': 'ai-infer01', 'ip': '10.0.70.125', 'node': '125'} => 3

--- PromQL: boylenet_gpu_temp_c ---
status: success count: 4
  {'gpu': 'Tesla T4', 'host': 'aidev', 'ip': '10.0.70.83', 'node': '83'} => 76
  {'gpu': 'Tesla T4', 'host': 'ai-srv01', 'ip': '10.0.70.72', 'node': '72'} => 79
  {'gpu': 'Tesla T4', 'host': 'ai-ollama01', 'ip': '10.0.70.228', 'node': '228'} => 74
  {'gpu': 'Tesla T4', 'host': 'ai-infer01', 'ip': '10.0.70.125', 'node': '125'} => 60

--- PromQL: boylenet_vault_models_ready_count ---
status: success count: 2
  {'host': 'ai-srv01', 'node': '72', 'vault': 'A'} => 30
  {'host': 'aidev', 'node': '83', 'vault': 'B'} => 10
```

---

## 3. Grafana Command Dashboard (`http://10.0.70.164:3001` / `http://grafana.boylenet.themfboyles.com`)

- **Dashboard Title**: `Boylenet AI Swarm • 4xT4 GPU Vault & Pool Quota Nexus`
- **UID**: `boylenet-ai-nexus`
- **Direct URL (IP)**: `http://10.0.70.164:3001/d/boylenet-ai-nexus/3aa156a`
- **Direct URL (DNS)**: `http://grafana.boylenet.themfboyles.com/d/boylenet-ai-nexus/3aa156a`
- **Credentials**: `admin` / `admin`
- **Starred**: `200 {"message":"Dashboard starred!"}`
- **Panels (13 total)**:
  1. Pool ABCD / Codex / Kimi / AGY / Local Quota Command Nexus (Row)
  2. Pool 5-Hour Quota Used (%) (`boylenet_pool_quota_used_pct`)
  3. Pool Weekly / Window Quota Used (%) (`boylenet_pool_weekly_pct`)
  4. Pool Admission Health & Vault Ready Models (`boylenet_vault_models_ready_count`, `boylenet_vault_free_tib`, `sum(boylenet_pool_state_ok)`)
  5. 4x Tesla T4 GPU Cluster (60 GiB GDDR6) & Dual 5TB Vault Telemetry (Row)
  6. 4x Tesla T4 VRAM Used (MiB / 15,360 MiB) (`boylenet_gpu_vram_used_mib`)
  7. 4x Tesla T4 GPU Compute Utilization (%) (`boylenet_gpu_utilization_pct`)
  8. 4x Tesla T4 Core Temperature (°C) (`boylenet_gpu_temp_c`)
  9. 4x Tesla T4 VRAM & Utilization Timeline (`timeseries`)
  10. Cluster Node CPU/RAM (1.73 TB DDR4) & Container Telemetry (Row)
  11. AI Node DDR4 RAM Used & Total Capacity (GiB) (`boylenet_node_ddr4_used_gib`, `boylenet_node_ddr4_total_gib`)
  12. Controller Host CPU & RAM Utilization (%) (Node Exporter)
  13. Container Memory Working Set (Top 10 MiB • cAdvisor)
