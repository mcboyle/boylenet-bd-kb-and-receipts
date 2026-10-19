# AGENT-57: Dual-Oracle Deep-Web Research & Citation Engine (`bd-deep-research` + `:4094` Web Studio) Receipts

- **WRITTEN-AT**: `2026-10-18T15:53:00Z`
- **SEAT**: `Agent-57 (Swarm #9: Dual-Oracle Deep-Web Research & Citation Engine)`
- **CLI BINARY**: `/home/mboyle/bin/bd-deep-research`
- **WEB STUDIO DAEMON**: `/home/mboyle/bin/bd-deep-research-web.py` (`bd-deep-research-web.service`, `active` on `0.0.0.0:4094`)
- **TRAEFIK INGRESS (`:80`)**: `http://research.boylenet.themfboyles.com` / `http://research` -> `http://10.0.70.164:4094`
- **VERDICT**: `PASS (3-Stage SearXNG :8888 + Dual-Oracle CDP .137/.181 + Qdrant .125:6333 + Cited Synthesis Verified; UniFi DNS, Traefik :80 & Dashy :4080 Wired)`

---

## 1. Deployed Components & Architecture

1. **`/home/mboyle/bin/bd-deep-research` (Standalone CLI & Python Engine)**:
   - **Stage 1 — Meta-Search Fanout (`SearXNG :8888`)**:
     - Queries `http://10.0.70.164:8888/search?q=<query>&format=json` (with automatic HTML fallback parser `searxng_html_fallback` if JSON format is restricted; verified `formats: [html, json]` enabled in `/home/mboyle/infra/expansion-layer/searxng/settings.yml`).
   - **Stage 2 — Dual-Oracle Live Browser Extraction (`10.0.10.137:9222` BattleStation + `10.0.70.181:9222` Bittorrent + HTTP Fast-Fetch + Qdrant `10.0.70.125:6333`)**:
     - Probes both Chrome DevTools Protocol (CDP) Browser Oracles (`BattleStation 10.0.10.137:9222` `Chrome/155.0.8059.41` at `3.5 ms` and `Bittorrent 10.0.70.181:9222` `Chrome/155.0.8059.80` at `4.0 ms`).
     - Extracts readable text in parallel across top $N$ URLs using fast HTTP + ephemeral CDP tabs (`Runtime.evaluate` of rendered DOM text with automatic tab cleanup via `/json/close/<id>`) and enriches with local **Qdrant** (`http://10.0.70.125:6333` — 5 collections online: `boylenet_codebase_ast`, `fleet_lessons_hivemind`, `swarm_docs_rag`, `boylenet_fleet_rag`, `boylenet_episodic_memory`) and **Local RAG** (`http://127.0.0.1:8099`).
   - **Stage 3 — Multi-Engine LLM Synthesis with Inline Numbered Citations (`[1]`, `[2]`, ...)**:
     - Supports selectable synthesis engines:
       - `qwen3-coder-30b-a3b` (`Local 4xT4 Qwen3-Coder-30B-A3B` via `http://10.0.70.228:4000/v1/chat/completions`)
       - `deepseek-think` (`DeepSeek <think> Reasoning` via `deepseek-v4-flash` / `tier2-deepseek-r1-70b`)
       - `claude-a`, `claude-b`, `claude-c`, `claude-d` (`Claude Pools A/B/C/D`)
       - `codex` (`OpenAI Codex Flagship gpt-6-sol`)
       - `kimi` (`Kimi Code CLI Cloud kimi-for-coding`)
2. **`/home/mboyle/bin/bd-deep-research-web.py` (`bd-deep-research-web.service` on `:4094`)**:
   - Perplexity-Pro-style dark glassmorphic interface at `http://10.0.70.164:4094` and `http://research.boylenet.themfboyles.com`.
   - Real-time Server-Sent Events (`/api/research-stream`) stage-by-stage progress stepper (`Stage 1: Searching SearXNG :8888` -> `Stage 2: Extracting Sources via Dual-Oracle` -> `Stage 3: Synthesizing Cited Brief`), clickable numbered source cards (`[1]`..`[7]`) with SHA256 hashes, interactive inline citation badges that highlight/scroll to source cards, `<think>` reasoning trace accordion, and persistent history (`/var/tmp/bd-deep-research-history.json`).

---

## 2. UniFi DNS (`10.0.70.1`), Traefik (`:80`), and Dashy (`:4080`) Wiring

| Component | Configuration / Record | Target | Verification Proof |
|---|---|---|---|
| **UniFi `static-dns` Short** | `research` (`A`) | `10.0.70.164` | `_id: 8ec707cc-26d2-4e3c-baef-a1a3a19f4182` (`dig +short @10.0.70.1 research` -> `10.0.70.164`) |
| **UniFi `static-dns` FQDN** | `research.boylenet.themfboyles.com` (`A`) | `10.0.70.164` | `_id: 5c8d836a-b83f-4078-99fe-0cfff2460e43` (`dig +short @10.0.70.1 research.boylenet.themfboyles.com` -> `10.0.70.164`) |
| **Traefik Dynamic Ingress** | `/home/mboyle/infra/expansion-layer/dynamic/boylenet-dns-ingress.yml` | `boylenet-research` -> `http://10.0.70.164:4094` | `curl -sf http://research.boylenet.themfboyles.com/health` -> `HTTP 200 OK` |
| **Dashy Command Nexus** | `/home/mboyle/infra/dashy/conf.yml` | `Deep Research & Dual-Oracle Studio :4094` | Added under `AI Command & Chat Studios` with `statusCheckUrl: http://10.0.70.164:4094/health` |

---

## 3. End-to-End Verification: `bd-deep-research "AVX-512 MoE inference optimization Xeon Skylake"`

- **Full JSON Artifact**: `/var/tmp/bd-deep-research-avx512.json`
- **Pipeline Telemetry**:
  - **Stage 1 (`SearXNG :8888`)**: `8` deduplicated URLs retrieved via `searxng-json` (`1209.9 ms`)
  - **Stage 2 (`Dual-Oracle + HTTP + Qdrant`)**: `7` grounded sources extracted (`2452.1 ms`):
    - `[1]` `dual-oracle-cdp(10.0.10.137:9222)` — `arxiv.org` (`3498 chars`, `sha256:4e6cd5a3ddcea203`) — *GateBleed: Timing-Only Membership Inference & MoE-Routing Inference*
    - `[2]` `dual-oracle-cdp(10.0.70.181:9222)` — `insiderllm.com` (`3500 chars`, `sha256:588ff6219afcfda3`) — *CPU-Only LLMs 2026: Real tok/s, Best Models, a 70B Xeon Build*
    - `[3]` `dual-oracle-cdp(10.0.10.137:9222)` — `colfaxresearch.com / reddit.com` (`sha256:c0e12bc7bbd6ed9b`) — *Capabilities of Intel AVX-512 in Intel Xeon Scalable Processors (Skylake)*
    - `[4]` `http-fast-fetch` — `news.ycombinator.com` (`3500 chars`, `sha256:d1247f1e0ab15f1d`)
    - `[5]` `http-fast-fetch` — `docs.redhat.com` (`3500 chars`, `sha256:77f1a2d9bf929612`)
    - `[6]` `qdrant+bd-rag(.125:6333)` — `qdrant.boylenet` (`476 chars`, `sha256:de4aa7668f692d09`) — *[Boylenet KB] EXTREME EDGE OPTIMIZATIONS (SWARM V3)*
    - `[7]` `qdrant+bd-rag(.125:6333)` — `qdrant.boylenet` (`1500 chars`, `sha256:72639746405dfeb3`) — *[Boylenet KB] Historical priority*
  - **Stage 3 (`LLM Synthesis`)**:
    - **Engine / Endpoint**: `tier1-qwen3-coder-30b-a3b` via `http://10.0.70.228:4000/v1/chat/completions`
    - **Inline Citations Verified**: `[1, 2, 3, 4, 5, 6, 7]` (`8,359 chars` synthesized technical report)
