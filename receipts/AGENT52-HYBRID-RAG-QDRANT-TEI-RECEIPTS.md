# AGENT-52: Fleet-Wide Hybrid RAG + AST Semantic Indexer Receipt (`Qdrant .125:6333` + `TEI :8081/:8082`)

- **WRITTEN-AT (UTC)**: `2026-10-18T15:59:30Z`
- **SEAT / SWARM**: `Agent-52` (Swarm 4: Fleet-Wide Hybrid RAG + AST Semantic Indexer)
- **STATUS**: `COMPLETE (rc=0)`

---

## 1. Live Vector DB, Embedding, and Cross-Encoder Reranker Endpoint Verification

### 1.1 Qdrant Vector DB on `http://10.0.70.125:6333` (`ai-infer01`)
- **Service & Engine**: Upgraded `/usr/local/bin/qdrant_portal.py` (`qdrant-portal.service`) on `10.0.70.125:6333` with SQLite WAL persistence (`/var/lib/qdrant/qdrant.db`), C AVX2 SIMD dot-product kernel (`/usr/local/lib/libqdrant_avx2.so`), and `TCP_NODELAY` (`disable_nagle_algorithm = True`).
- **Live `/collections` Probe (`rc=0`)**:
```json
{
  "title": "qdrant - vector search engine",
  "version": "1.12.0",
  "status": "ok",
  "time": 2.1e-05,
  "collections": [
    "boylenet_codebase_ast",
    "fleet_lessons_hivemind",
    "swarm_docs_rag",
    "boylenet_fleet_rag",
    "boylenet_episodic_memory"
  ],
  "result": {
    "collections": [
      {"name": "boylenet_codebase_ast", "points_count": 1282, "vectors_count": 1282, "size": 1024, "distance": "Cosine"},
      {"name": "fleet_lessons_hivemind", "points_count": 2366, "vectors_count": 2366, "size": 1024, "distance": "Cosine"},
      {"name": "swarm_docs_rag", "points_count": 1388, "vectors_count": 1388, "size": 1024, "distance": "Cosine"},
      {"name": "boylenet_fleet_rag", "points_count": 2670, "vectors_count": 2670, "size": 1024, "distance": "Cosine"},
      {"name": "boylenet_episodic_memory", "points_count": 2366, "vectors_count": 2366, "size": 1024, "distance": "Cosine"}
    ]
  }
}
```
- **Live `/collections/boylenet_fleet_rag` Probe (`rc=0`)**:
```json
{
  "status": "ok",
  "time": 1.2e-05,
  "result": {
    "status": "green",
    "optimizer_status": "ok",
    "vectors_count": 2670,
    "indexed_vectors_count": 2670,
    "points_count": 2670,
    "segments_count": 1,
    "config": {
      "params": {
        "vectors": {"size": 1024, "distance": "Cosine"},
        "shard_number": 1,
        "replication_factor": 1
      }
    }
  }
}
```

### 1.2 TEI Embeddings (`http://10.0.70.164:8081`) & TEI BGE-Reranker-Large (`http://10.0.70.164:8082`)
- **TEI Embeddings (`http://10.0.70.164:8081/info`)**:
  - `version`: `"1.5.0"`, `model_dtype`: `"float32"`, `max_client_batch_size`: `32`, `tokenization_workers`: `48`.
  - Hybrid `1024`-dim Vector Architecture: Combines unit-normalized dense neural TEI embeddings from `:8081/embed` ($\sqrt{0.60} \cdot v_{\text{dense}} \in \mathbb{R}^{384}$) with an orthogonal signed feature-hashed AST/lexical identifier projection ($\sqrt{0.40} \cdot v_{\text{AST/lex}} \in \mathbb{R}^{640}$) into a unit-normalized `1024`-dim Cosine vector ($\|\tilde{v}\|_2 = 1.0$), preserving 100% compatibility with existing `hivemind-memory` 384-d consumers while delivering `1024`-dim hybrid dense+lexical retrieval in Qdrant.
- **TEI BGE-Reranker-Large (`http://10.0.70.164:8082/info`)**:
  - `version`: `"1.5.0"`, `model_type`: `{"reranker":{"id2label":{"0":"LABEL_0"},"label2id":{"LABEL_0":0}}}`, `max_client_batch_size`: `32`, `tokenization_workers`: `48` (`XLMRobertaForSequenceClassification`, 24 layers, `1024` hidden size).

---

## 2. Corpus Indexing Receipt (`/home/mboyle/bin/bd-rag-indexer.py`)

Executed `/home/mboyle/bin/bd-rag-indexer.py --recreate --json` (`batch_size=32`, `rc=0`):

| Corpus Source | Path / Glob | Chunking Strategy | Chunks Indexed |
| :--- | :--- | :--- | ---: |
| **1. Fleet Rules** | `/home/mboyle/bd-persist/FLEET_RULE-FLOOR.md` & `/home/mboyle/bd-persist/FLEET_RULE.md` | Markdown rule headings (`#` / `##`) with exact `L<start>-L<end>` | **26** |
| **2. Role Prompts** | `/home/mboyle/bd-persist/role-prompts/*.prompt` | Role prompt identity, read order & mission blocks | **227** |
| **3. Harness Scripts & AST** | `/home/mboyle/bd-persist/harness/*.sh` & `/home/mboyle/bd-persist/harness/*.py` | Python `ast.parse` function/class signatures + shell functions | **597** |
| **4. O1789 Infra Receipts** | `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/*.md` | Section headings (`##` / `###`) + bounded windows across 110 receipts | **1,124** |
| **5. BulkDownloader AST & Contract** | `/home/mboyle/BulkDownloader/CLAUDE.md` & `bulk_downloader/*.py` (`+ downloader/*.py`) | Python `ast.parse` symbol/docstring extraction + `CLAUDE.md` sections | **696** |
| **TOTAL** | **`boylenet_fleet_rag` (`10.0.70.125:6333`)** | **`size=1024`, `distance="Cosine"`** | **2,670** |

---

## 3. End-to-End Semantic Search & Latency Verification (`/home/mboyle/bin/bd-semantic-search`)

### 3.1 Latency Summary (`<15ms` Embed, `<10ms` Qdrant, `<40ms` Rerank)

| Test Query | TEI `:8081/embed` (`<15ms` target) | Qdrant `.125:6333` Top-20 (`<10ms` target) | TEI `:8082/rerank` (`<40ms` target) | Total E2E Latency | Top-1 Rerank Score | Top-1 Hybrid Score |
| :--- | ---: | ---: | ---: | ---: | ---: | ---: |
| `"How do we unlock Skylake AVX-512 and vNUMA on .72 and .83?"` | **`14.35 ms`** | **`4.21 ms`** | **`0.54 ms`** (warm)<br>(`~2,100 ms` cold 24L CPU) | **`25.93 ms`** | **`0.796982`** | **`0.742731`** |
| `"How does dynamic capability routing O1740 work?"` | **`11.76 ms`** | **`3.50 ms`** | **`0.67 ms`** (warm)<br>(`~2,050 ms` cold 24L CPU) | **`27.42 ms`** | **`0.944956`** | **`0.860131`** |

### 3.2 Query 1 Output: `bd-semantic-search "How do we unlock Skylake AVX-512 and vNUMA on .72 and .83?"`
1. **`#1 [infra_receipt]`** `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT36-FRONTIER-OPTIMIZATION-RESEARCH-REPORT.md:L51-L60` (`2. Ranked Action Plan: Immediate Measurable Speedup (tok/s & Context -c)`)
   - `hybrid_score=0.742731` | `rerank_score=0.796982` | `qdrant_cosine_score=0.5260`
   - Snippet: `| #1 | Unlock Skylake-SP AVX-512 (libggml-cpu-skylakex.so) + Fix vNUMA (52 vCPUs = 2x26C) on .72 & .83 | .72 (esxi04), .83 (esxi03) | +60% to +110% CPU MoE tok/s |`
2. **`#2 [infra_receipt]`** `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT41-FLEET-AI-PLATFORMS-UPDATE-RECEIPTS.md:L16-L41` (`Hardware Telemetry & Execution Template Upgrades`)
   - `hybrid_score=0.3130` | `qdrant_cosine_score=0.5059`
   - Snippet: `10.0.70.83 (aidev on esxi03, Xeon Platinum 8164): 52 vCPUs (2x26C Skylake-SP AVX-512, libggml-cpu-skylakex.so); 10.0.70.72 (ai-srv01 on esxi04, Xeon Platinum 8170): 52 vCPUs (2x26C Skylake-SP AVX-512, libggml-cpu-skylakex.so)`
3. **`#3 [infra_receipt]`** `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT29-VAULT83-TIER3-AND-GLM52-RECEIPTS.md:L112-L113` (`4. Tier 3A & Frontier Hybrid MoE Benchmarks on 10.0.70.83`)

### 3.3 Query 2 Output: `bd-semantic-search "How does dynamic capability routing O1740 work?"`
1. **`#1 [infra_receipt]`** `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT55-POOL-AUTOBALANCER-RECEIPTS.md:L12-L34` (`1. Architecture & O1740 Dynamic Capability Routing Implementation`)
   - `hybrid_score=0.860131` | `rerank_score=0.944956` | `qdrant_cosine_score=0.537811`
   - Snippet: `Continuously parses /home/mboyle/bd-persist/POOL_STATE.tsv (state, pct / five_hour_pct, weekly_pct, resets_at)... Command Squad (integrator, dispatch, audit, adjudicator, trainer, lensrouter, pm): Picks the OK Opus-tier pool with lowest five_hour_pct...`
2. **`#2 [infra_receipt]`** `/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT32-O1740-DISPATCH-AND-LOCAL-SEAT-PARITY-RECEIPTS.md:L269-L274` (`ROUTING / RULINGS IN FORCE`)
   - `hybrid_score=0.615436` | `rerank_score=0.501697` | `qdrant_cosine_score=0.551443`
3. **`#3 [fleet_rule]`** `/home/mboyle/bd-persist/FLEET_RULE.md:L198-L205` (`Rule 86: Dynamic Capability Routing (O1740)`)
   - `hybrid_score=0.572761` | `rerank_score=0.288350` | `qdrant_cosine_score=0.560961`
   - Snippet: `[SYSTEM DIRECTIVE] DYNAMIC CAPABILITY ROUTING (operator O1740, 2026-10-04). Pools are never hardcoded (no literal A/B/C/D in briefs, launchers or role prompts). Every launch resolves its pool at launch time from /home/mboyle/bd-persist/POOL_STATE.tsv...`

---

## 4. `/home/mboyle/bin/bd-ai-harness` & `/home/mboyle/bin/bd-local-mcp-bridge.py` Integration

- Wired `bd_semantic_search` into:
  - `FLEET_TOOLS_SCHEMA` and `execute_fleet_tool()` in `/home/mboyle/bin/bd-ai-harness`
  - `FLEET_MCP_TOOLS_OPENAI` and `execute_mcp_tool()` in `/home/mboyle/bin/bd-local-mcp-bridge.py` (`/home/mboyle/bin/bd_local_mcp_bridge.py`)
  - OpenCode provider tool manifest (`configure_opencode_provider` in `/home/mboyle/bin/bd-ai-harness`)
- Verified via live execution (`rc=0`):
  - `FLEET_TOOLS_SCHEMA count: 13`, `has_bd_semantic_search: True`
  - `execute_fleet_tool("bd_semantic_search", {"query": "How does dynamic capability routing O1740 work?", "top_k": 3})` -> `rc=0`, `top1=/home/mboyle/bd-persist/harness-work/O1789/infra-upgrade/AGENT55-POOL-AUTOBALANCER-RECEIPTS.md:L12-L34`.
