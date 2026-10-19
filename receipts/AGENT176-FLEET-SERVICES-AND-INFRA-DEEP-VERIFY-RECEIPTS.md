# AGENT-176 — Fleet Services, Docker, Gitea, Portals (`:4080-:4099`), HTTPS `:443` & Dual Browser Oracles Deep Verification Receipts

- **Timestamp (UTC)**: `2026-10-19T04:17:45Z`
- **Seat**: `Agent-176`
- **Scope**: Post-reboot deep verification and auto-healing across `.164` (`hub-mesh01`), `.72` (`ai-srv01`), `.83` (`aidev`), `.82` (`wrk-test04`), `.125` (`ai-infer01`), `.137` (`BattleStation`), and `.181` (`Bittorrent`).
- **Overall Status**: **PASS (100% Services Active & Returning HTTP 200)**

---

## 1. Root-Cause Remediation Applied During Verification

1. **Exited Docker Containers on `.164` Post-Reboot**:
   - Started exited containers on `.164`: `forgejo` (`:3000`), `litellm-proxy` (`:4000`), `devpi-server` (`:3141`), `tei-embeddings` (`:8081`), `prometheus` (`:9090`), `grafana-dashboards` (`:3001`), `traefik-ingress` (`:80/:8080`), `kafka` (`:9092`), `code-server` (`:8443`), `alertmanager` (`:9093`), `searxng-search` (`:8888`), `devdocs-mirror` (`:8088`), `cadvisor-metrics` (`:8085`), `node-exporter` (`:9100`), `docker-mirror` (`:5000`), `verdaccio` (`:4873`), `apt-cacher-ng` (`:3142`).
2. **Stale Pre-Reboot `iptables -t nat DOCKER` DNAT Rules on `.164`**:
   - Found 19 stale pre-reboot `DNAT` rules at the top of `iptables -t nat DOCKER` on `.164` shadowing the newly started container bridge IPs (`172.17.x.x` – `172.21.x.x`), which caused `http://10.0.70.164:<port>` (for `:3000`, `:3001`, `:3141`, `:4000`, `:8081`, `:9090`, etc.) to refuse connections while `127.0.0.1:<port>` worked.
   - Purged all 19 stale duplicate `DNAT` rules from `iptables -t nat DOCKER`. Immediately restored `HTTP 200` on `10.0.70.164:<port>` both locally on `.164` and remotely from `.82`.
3. **Port `:8000 -> :8082` NAT Redirect on `.72`**:
   - On `.72`, `llama-tier1-alpha.service` (`Qwen3-Coder-30B-A3B-Instruct-Q4_K_M.gguf`) listens on `:8082`. Restored `iptables -t nat` `PREROUTING` and `OUTPUT` `REDIRECT` rules mapping `:8000 -> :8082` so both `http://10.0.70.72:8000` and `http://10.0.70.72:8082` respond `HTTP 200`.

---

## 2. `.164` (`hub-mesh01`) Core Systemd Services & Docker Containers

### Core Systemd Units
| Unit / Service | Active State | Endpoint / Port | Verification Receipt |
| :--- | :--- | :--- | :--- |
| `docker.service` | `active` | 25/25 containers Up | All 25 containers running & healthy |
| `gitea` (`forgejo` container) | `active` (`Up`) | `http://10.0.70.164:3000/` | `HTTP 200` (local `.164` & remote `.82`) |
| `nginx.service` | `active` | `:80` / `:443` | `HTTP 200` across all 38 HTTPS `:443` vhosts |
| `bd-gpu-orchestrator.service` | `active` | `http://10.0.70.164:4010/` | `HTTP 200` (Sovereign Local Gateway) |
| `bd-web-terminal-drawer.service` | `active` | `http://10.0.70.164:4083/` | `HTTP 200` (Terminal TV Wall) |
| `bd-autonomous-swarm-driver.service` | `active` | Daemon | `active (running)` |

### Boylenet Web Portals (`:4080`–`:4099`) & Support Services on `.164`
| Port | Service / Portal | URL Tested | HTTP Status |
| :--- | :--- | :--- | :--- |
| `:3000` | Gitea / Forgejo | `http://10.0.70.164:3000/` | `200` |
| `:3001` | Grafana Dashboards | `http://10.0.70.164:3001/` (`-L`) | `200` |
| `:3141` | DevPI Server | `http://10.0.70.164:3141/` (`-L`) | `200` |
| `:4000` | LiteLLM Proxy | `http://10.0.70.164:4000/` | `200` |
| `:4010` | Sovereign Local GPU Gateway | `http://10.0.70.164:4010/` | `200` |
| `:8081` | TEI Embeddings | `http://10.0.70.164:8081/info` | `200` |
| `:8082` | TEI Reranker | `http://10.0.70.164:8082/` | `200` |
| `:8090` | OpenWebUI (`.164`) | `http://10.0.70.164:8090/` | `200` |
| `:9090` | Prometheus | `http://10.0.70.164:9090/` (`-L`) | `200` |
| `:4080` | Portal `:4080` (Dashy) | `http://10.0.70.164:4080/` | `200` |
| `:4081` | Portal `:4081` | `http://10.0.70.164:4081/` | `200` |
| `:4082` | Portal `:4082` | `http://10.0.70.164:4082/` | `200` |
| `:4083` | Portal `:4083` (Terminal TV Wall) | `http://10.0.70.164:4083/` | `200` |
| `:4084` | Portal `:4084` | `http://10.0.70.164:4084/` | `200` |
| `:4085` | Portal `:4085` | `http://10.0.70.164:4085/` | `200` |
| `:4086` | Portal `:4086` | `http://10.0.70.164:4086/` | `200` |
| `:4087` | Portal `:4087` | `http://10.0.70.164:4087/` | `200` |
| `:4088` | Portal `:4088` | `http://10.0.70.164:4088/` | `200` |
| `:4089` | Portal `:4089` | `http://10.0.70.164:4089/` | `200` |
| `:4090` | Portal `:4090` | `http://10.0.70.164:4090/` | `200` |
| `:4091` | Portal `:4091` | `http://10.0.70.164:4091/` | `200` |
| `:4092` | Portal `:4092` | `http://10.0.70.164:4092/` | `200` |
| `:4093` | Portal `:4093` | `http://10.0.70.164:4093/` | `200` |
| `:4094` | Portal `:4094` | `http://10.0.70.164:4094/` | `200` |
| `:4095` | Portal `:4095` | `http://10.0.70.164:4095/` | `200` |
| `:4096` | Portal `:4096` | `http://10.0.70.164:4096/` | `200` |
| `:4097` | Portal `:4097` | `http://10.0.70.164:4097/` | `200` |
| `:4098` | Portal `:4098` | `http://10.0.70.164:4098/` | `200` |
| `:4099` | Portal `:4099` | `http://10.0.70.164:4099/` | `200` |

### Nginx HTTPS `:443` Vhosts (`*.boylenet.themfboyles.org`)
| HTTPS Vhost | HTTP Status | HTTPS Vhost | HTTP Status |
| :--- | :--- | :--- | :--- |
| `https://ai-webui.boylenet.themfboyles.org` | `200` | `https://models.boylenet.themfboyles.org` | `200` |
| `https://boylenet.themfboyles.org` | `200` | `https://novnc.boylenet.themfboyles.org` | `200` |
| `https://briefing.boylenet.themfboyles.org` | `200` | `https://offsec.boylenet.themfboyles.org` | `200` |
| `https://burst.boylenet.themfboyles.org` | `200` | `https://openclaw.boylenet.themfboyles.org` | `200` |
| `https://ca.boylenet.themfboyles.org` | `200` | `https://opencode.boylenet.themfboyles.org` | `200` |
| `https://codeserver.boylenet.themfboyles.org` | `200` (`302->200`) | `https://openwebui.boylenet.themfboyles.org` | `200` |
| `https://credentials.boylenet.themfboyles.org` | `200` | `https://openwebui72.boylenet.themfboyles.org` | `200` |
| `https://dashy.boylenet.themfboyles.org` | `200` | `https://os.boylenet.themfboyles.org` | `200` |
| `https://dreamer.boylenet.themfboyles.org` | `200` | `https://portal.boylenet.themfboyles.org` | `200` |
| `https://gateway.boylenet.themfboyles.org` | `200` | `https://prereview.boylenet.themfboyles.org` | `200` |
| `https://gitea.boylenet.themfboyles.org` | `200` | `https://pwa.boylenet.themfboyles.org` | `200` |
| `https://gpu.boylenet.themfboyles.org` | `200` | `https://qdrant.boylenet.themfboyles.org` | `200` |
| `https://grafana.boylenet.themfboyles.org` | `200` (`302->200`) | `https://research.boylenet.themfboyles.org` | `200` |
| `https://harnesses.boylenet.themfboyles.org` | `200` | `https://searxng.boylenet.themfboyles.org` | `200` |
| `https://jarvis.boylenet.themfboyles.org` | `200` | `https://sentinel.boylenet.themfboyles.org` | `200` |
| `https://langfuse.boylenet.themfboyles.org` | `200` | `https://terminal.boylenet.themfboyles.org` | `200` |
| `https://litellm-228.boylenet.themfboyles.org` | `200` | `https://topology.boylenet.themfboyles.org` | `200` |
| `https://litellm.boylenet.themfboyles.org` | `200` | `https://voice.boylenet.themfboyles.org` | `200` |
| `https://lobechat.boylenet.themfboyles.org` | `200` | `https://minio.boylenet.themfboyles.org` | `200` |

---

## 3. Remote Infrastructure Services & Dual Browser Oracles

| Node / Service | Endpoint / Probe | Status / HTTP Code | Details |
| :--- | :--- | :--- | :--- |
| `.72` OpenWebUI (`:8080`) | `http://10.0.70.72:8080/` | `HTTP 200` | Uvicorn `open_webui.main:app` PID 2639 active post-reboot |
| `.72` OpenWebUI HTTPS | `https://openwebui72.boylenet.themfboyles.org/` | `HTTP 200` | Proxied cleanly via `.164` Nginx `:443` |
| `.72` Tier-1 Coder (`:8000` & `:8082`) | `http://10.0.70.72:8000/v1/models` & `:8082/v1/models` | `HTTP 200` | `llama-tier1-alpha.service` (`Qwen3-Coder-30B-A3B-Instruct-Q4_K_M.gguf`, 262,144 ctx) + `llama-rpc.service` (`10.0.75.12:50052`) active |
| `.83` Tier-2/3 Opus (`:18080`) | `http://10.0.70.83:18080/v1/models` & `/health` | `HTTP 200` | `llama-server` PID 1936 (`Qwen3.6-35B-A3B-Heretic-Q4_K_M.gguf`, 262,144 ctx) + `rpc-server` (`10.0.75.11:50052`) active |
| `.82` (`wrk-test04` Ramdisk Node) | SSH + `df -h /dev/shm` + `nproc` | `PASS` | SSH responsive, 64 vCPUs online, `/dev/shm` `tmpfs` 63G mounted (`672M` used, `63G` avail, `2%`) |
| `.125:6333` Qdrant Vector DB | `http://10.0.70.125:6333/collections` | `HTTP 200` | Qdrant `v1.18.0` status `ok`; collections verified: `bd_master_kb` (455 pts), `fleet_lessons_hivemind` (805 pts), `boylenet_episodic_memory` (805 pts), `boylenet_codebase_ast` (1282 pts), `swarm_docs_rag` (1388 pts), `boylenet_fleet_rag` (2670 pts) |
| `.137:9222` BattleStation CDP Oracle | `http://10.0.10.137:9222/json/version` | `HTTP 200` | `Chrome/155.0.8059.41`, `ws://10.0.10.137:9222/devtools/browser/67bdf502-9b16-48f2-886c-2490777e867d` |
| `.181:9222` Bittorrent CDP Oracle | `http://10.0.70.181:9222/json/version` | `HTTP 200` | `Chrome/155.0.8059.80`, `ws://10.0.70.181:9222/devtools/browser/f9d5afde-4ac1-4c87-a027-752552700b22` |

---

## 4. End-to-End Live Completion Receipts (`:4000`, `:4010`, `.72:8000/:8082`, `.83:18080`)

| Route | Target Model | HTTP Status | Completion Receipt & Throughput |
| :--- | :--- | :--- | :--- |
| `:4010` Sovereign Gateway -> `.72` | `tier1-qwen3-coder-30b-a3b` | `HTTP 200` | `id: chatcmpl-Ne6i5Mt3V8FFCl9dzc3uvVfQEgmHn45M` -> `"OK_4010_72"` (`16.75 tok/s`, Argon-4 shield enforced) |
| `:4010` Sovereign Gateway -> `qwen3.6-35b-claude-opus-abliterated` | `qwen3.6-35b-claude-opus-abliterated` | `HTTP 200` | `id: chatcmpl-PTuEhBbF3iQDsQF0yU6F8jX1X6NlOzRt` -> `"OK_4010_83"` (`15.88 tok/s`, Argon-4 shield enforced) |
| `:4000` LiteLLM Proxy -> `.72` | `tier1-qwen3-coder-30b-a3b` | `HTTP 200` | `id: chatcmpl-I7lpFO7U1zzqMC3cp9LweV36YgDD34ZR` -> `"OK_4000_72"` (`17.57 tok/s`) |
| `:4000` LiteLLM Proxy -> `qwen3.6-35b-claude-opus-abliterated` | `qwen3.6-35b-claude-opus-abliterated` | `HTTP 200` | `id: chatcmpl-l2iboQFJdntLPrr7fYv9CjXruzUSDENL` -> `"OK_4000_83"` (`17.62 tok/s`) |
| Direct `.72:8000` & `.72:8082` | `qwen3-coder-30b-a3b` | `HTTP 200` | `id: chatcmpl-GerfTPaJaCnHWvtE4m6Ct6pfEHgChjXt` -> `"OK_72_800"` (`17.24 tok/s`, `63.83 prompt tok/s`) |
| Direct `.83:18080` | `Qwen3.6-35B-A3B-Heretic-Q4_K_M.gguf` | `HTTP 200` | `id: chatcmpl-0yhbHrMnlJ6A99harvqgMBHdrgYYHVwg` -> `"Hello"` (`finish_reason: length`) |
