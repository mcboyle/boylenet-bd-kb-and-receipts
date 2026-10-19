# AGENT-63 RECEIPTS — Hybrid Cloud + Local Burst Orchestrator (`bd-cloud-burst` & `:4090` `http://cloud.boylenet.themfboyles.com`)

- **Written At**: `2026-10-18T16:01:00Z`
- **Agent**: `Agent-63` (Swarm #15: Hybrid Cloud + Local Burst Orchestrator)
- **Service Endpoint**: `http://cloud.boylenet.themfboyles.com` (`http://10.0.70.164:4090`, `bd-cloud-burst-web.service`)
- **CLI Binary**: `/home/mboyle/bin/bd-cloud-burst`
- **Web Studio**: `/home/mboyle/bin/bd-cloud-burst-web.py`
- **Multi-Harness Arena Updated**: `/home/mboyle/bin/bd-multi-harness-web.py` (`bd-multi-harness-web.service` on `:4098`)

---

## 1. $1,000.00 Free Cloud Session Credit Pool & Overage Protection

- **Total Free Cloud Session Credits**: **`$1,000.00`** (`$250.00` per account across `Pool A`, `Pool B`, `Pool C`, `Pool D`)
- **Expiration**: **November 5 at 2:59 AM EST**
- **Overage Protection**: **OFF (`$0.00` hard cap — zero risk of paid overage)**
- **Priority Routing Policy**:
  1. Prioritizes `claude --cloud` (`claude-opus-5-5`, `claude-sonnet-5-5`, `fable-5-1` across Pools A/B/C/D) + `codex cloud exec` (`gpt-6.1-sol`) to burn the **$1,000 of free expiring Cloud Session Credits** before touching local 5h/weekly plan limits.
  2. Multi-agent code review defaults to our **$0-Cost Local GPU Multi-Lens Review Engine (`http://10.0.70.164:4092/api/review`) + Subscription Cloud Pools**, while keeping `claude ultrareview --json` available as a clearly labeled optional mode with its free-quota vs Cloud Session credit note.

---

## 2. Unified Hybrid Cloud + Local Burst Orchestrator (`bd-cloud-burst` & `:4090`)

### A. OpenAI Codex Cloud Engine (`codex cloud`)
- Full CLI and Web Studio support for:
  - `codex cloud list --json`
  - `codex cloud exec --env <ENV_ID> --attempts <1..4> --branch <BRANCH> "<PROMPT>"`
  - `codex cloud status <TASK_ID>`
  - `codex cloud diff <TASK_ID>`
  - `codex cloud apply <TASK_ID>`
- Models supported:
  - `gpt-6.1-sol` (**GPT-6.1 Sol Ultra** with `xhigh` / `ultra` reasoning — **Default**)
  - `gpt-6-sol` (GPT-6 Sol Flagship)
  - `gpt-6-astra` (GPT-6 Astra Judgement)
  - `gpt-5.6-terra` (GPT-5.6 Terra Measurement)
  - `gpt-5.6-sol` (GPT-5.6 Sol Advisory)
- Pre-configured GitHub Repository Presets + Ready-to-Paste Container Setup Script & `.env` Generator:
  1. `mcboyle/Bulkdownloader`
  2. `mcboyle/Bulk_downloader`
  3. `mcboyle/BulkDownloader_github_ready`
  4. `mcboyle/vc-repo`
  5. `mcboyle/GhostTruth`
  6. `mcboyle/Ghost`
  7. `mcboyle/BD`

### B. Anthropic Claude ABCD Cloud Environments (`claude --cloud`, `--environment <ccpool_...>`)
- Dispatches 0%-Local-CPU cloud sessions across **Pool A (`~/.claude-a`), Pool B (`~/.claude-b`), Pool C (`~/.claude-c`), Pool D (`~/.claude-d`)** with O1740 dynamic pool selection (lowest `five_hour_pct` among `OK` pools).
- Models supported:
  - `claude-opus-5-5` (**Claude Opus 5.5 Flagship** — **Default**)
  - `claude-sonnet-5-5` (**Claude Sonnet 5.5 High-Throughput Builder**)
  - `fable-5-1` (**Fable 5.1 High** Cloud Environment Flagship)
  - `claude-opus-4-7` & `claude-sonnet-4-6`

### C. Simultaneous Hybrid Cloud + Local 4xT4 GPU Burst Fanout (`/api/hybrid-burst` & `bd-cloud-burst --hybrid`)
- Concurrently dispatches tasks via `ThreadPoolExecutor(max_workers=8)` across:
  - **Anthropic Cloud Pool A/B/C/D** (`claude-opus-5-5` / `claude-sonnet-5-5` / `fable-5-1`)
  - **OpenAI Codex Cloud** (`gpt-6.1-sol` / `gpt-6-astra`)
  - **Moonshot Kimi Cloud** (`kimi-for-coding`)
  - **Local 4xT4 GPU Compute (`.164:4000`)** (`hermes`, `qwen3-coder`, `deepseek`)

---

## 3. Multi-Harness AI Studio (`:4098`) Model & Default Upgrades

Updated `/home/mboyle/bin/bd-multi-harness-web.py` and restarted `bd-multi-harness-web.service`:
- Added `fable-5-1` (`Fable 5.1 High`) and `gpt-6.1-sol` (`GPT-6.1 Sol Ultra`) to `MODELS` (`61` total models).
- Updated `HARNESS_DEFAULT_MODEL`:
  - `"claude-a".."claude-d"` -> `"claude-opus-5-5"`
  - `"codex"` -> `"gpt-6.1-sol"`
- Updated all fallbacks in `run_studio_harness()`, `synthesize_arena_consensus()`, and `/api/launch-role` to default to `"claude-opus-5-5"` for Claude ABCD and `"gpt-6.1-sol"` for Codex.

---

## 4. UniFi DNS (`10.0.70.1`), Traefik (`:80`), and Dashy (`:4080`) Wiring

1. **Authoritative UniFi `static-dns` Registrations**:
   - `cloud` -> `10.0.70.164` (`id=98ee3401-92a6-4e30-96db-530269ab7d49`)
   - `cloud.boylenet.themfboyles.com` -> `10.0.70.164` (`id=ec48d7a9-41b6-4c04-8aab-ded9d56be149`)
2. **Traefik Dynamic Ingress (`/home/mboyle/infra/expansion-layer/dynamic/boylenet-dns-ingress.yml`)**:
   - Added router `boylenet-cloud` (`Host(\`cloud.boylenet.themfboyles.com\`) || Host(\`cloud\`)`) -> `boylenet-cloud-svc` (`http://10.0.70.164:4090`).
3. **Dashy Portal (`/home/mboyle/infra/dashy/conf.yml`)**:
   - Added `Cloud Burst (:4090)` to top `navLinks` and `Hybrid Cloud + Local Burst Orchestrator :4090` card under `AI Command & Chat Studios`.

---

## 5. Live Verification Receipts

### A. `bd-cloud-burst --status`
```text
======================================================================
 BOYLENET HYBRID CLOUD + LOCAL BURST ORCHESTRATOR (bd-cloud-burst)
======================================================================
 Timestamp               : 2026-10-18T16:00:30Z
 Cloud Credit Pool       : $1,000 Free Cloud Session Credit Pool ($250 x Pools A/B/C/D • Expires Nov 5)
 Overage Protection      : OFF ($0.00 hard cap — zero paid overage risk)
 Priority Policy         : Burn $1,000 Free Cloud Session Credits via `claude --cloud` (claude-opus-5-5 / claude-sonnet-5-5 / fable-5-1) + `codex cloud exec` (gpt-6.1-sol) BEFORE local 5h/weekly plan limits
 Recommended Claude Pool : Pool A (O1740 Dynamic Routing)
   - Pool A: state=OK | 5h=0.0% | wk=0.0% | Free Cloud Credit: $250/$250 (Exp Nov 5, 2:59 AM EST)
   - Pool B: state=OK | 5h=0.0% | wk=0.0% | Free Cloud Credit: $250/$250 (Exp Nov 5, 2:59 AM EST)
   - Pool C: state=OK | 5h=0.0% | wk=0.0% | Free Cloud Credit: $250/$250 (Exp Nov 5, 2:59 AM EST)
   - Pool D: state=OK | 5h=0.0% | wk=0.0% | Free Cloud Credit: $250/$250 (Exp Nov 5, 2:59 AM EST)
 Claude Cloud Models     : fable-5-1, claude-opus-5-5, claude-sonnet-5-5, claude-opus-4-7, claude-sonnet-4-6
 Codex Cloud Models      : gpt-6.1-sol, gpt-6-sol, gpt-6-astra, gpt-5.6-terra, gpt-5.6-sol
 GitHub Repo Presets     : mcboyle/Bulkdownloader, mcboyle/Bulk_downloader, mcboyle/BulkDownloader_github_ready, mcboyle/vc-repo, mcboyle/GhostTruth, mcboyle/Ghost, mcboyle/BD
 Codex Cloud Tasks       : 0 active (rc=0)
 Local 4xT4 Gateway :4000: ONLINE (hermes, qwen3-coder, deepseek)
 Web Studio              : http://cloud.boylenet.themfboyles.com (http://10.0.70.164:4090)
```

### B. `curl -s http://127.0.0.1:4090/health` & `curl -s http://cloud.boylenet.themfboyles.com/health`
```json
{
  "status": "ok",
  "service": "bd-cloud-burst-web",
  "port": 4090,
  "url": "http://cloud.boylenet.themfboyles.com",
  "cloud_session_credits_usd": 1000.0,
  "cloud_session_credits_banner": "$1,000 Free Cloud Session Credit Pool ($250 x Pools A/B/C/D • Expires Nov 5)",
  "overage_protection": "OFF ($0.00 risk)",
  "claude_cloud_models": ["fable-5-1", "claude-opus-5-5", "claude-sonnet-5-5", "claude-opus-4-7", "claude-sonnet-4-6"],
  "codex_cloud_models": ["gpt-6.1-sol", "gpt-6-sol", "gpt-6-astra", "gpt-5.6-terra", "gpt-5.6-sol"],
  "github_repos": ["mcboyle/Bulkdownloader", "mcboyle/Bulk_downloader", "mcboyle/BulkDownloader_github_ready", "mcboyle/vc-repo", "mcboyle/GhostTruth", "mcboyle/Ghost", "mcboyle/BD"],
  "local_gpu_lanes": ["hermes", "qwen3-coder", "deepseek"]
}
```

### C. Live 4-Lane Hybrid Cloud + Local GPU Burst Fanout (`POST /api/hybrid-burst`)
- **Result**: `4/4 lanes rc=0` in `17.79s` wall-clock (`local-hermes` in `4.1s`, `cloud-codex` `gpt-6.1-sol` in `8.21s`, `cloud-claude-a` `claude-opus-5-5` in `12.48s`, `cloud-kimi` `kimi-for-coding` in `17.79s`):
```json
{
  "rc": 0,
  "total_lanes": 4,
  "ok_lanes": 4,
  "wall_elapsed_s": 17.79,
  "consensus_summary": "Hybrid Cloud+Local Burst completed 4/4 lanes in 17.79s wall-clock (Fastest lane: local-hermes [auto-local] in 4.1s).",
  "candidates": [
    {
      "lane": "cloud-claude-a",
      "compute": "Anthropic Cloud Pool A (0% Local CPU)",
      "requested_model": "claude-opus-5-5",
      "model": "claude-opus-5-5",
      "rc": 0,
      "elapsed_s": 12.48,
      "answer": "CLOUD_LOCAL_HYBRID_OK claude-opus-5-5"
    },
    {
      "lane": "cloud-codex",
      "compute": "OpenAI Codex Cloud (0% Local CPU)",
      "requested_model": "gpt-6.1-sol",
      "model": "gpt-6.1-sol",
      "rc": 0,
      "elapsed_s": 8.21,
      "answer": "CLOUD_LOCAL_HYBRID_OK gpt-6"
    },
    {
      "lane": "cloud-kimi",
      "compute": "Cloud (0% Local CPU)",
      "model": "kimi-for-coding",
      "rc": 0,
      "elapsed_s": 17.79,
      "answer": "CLOUD_LOCAL_HYBRID_OK kimi (Kimi Code CLI)"
    },
    {
      "lane": "local-hermes",
      "compute": "Local 4xT4 GPU (.164:4000)",
      "harness": "hermes",
      "model": "auto-local",
      "rc": 0,
      "elapsed_s": 4.1,
      "answer": "CLOUD_LOCAL_HYBRID_OK\nworker-llama3-70b-codegen"
    }
  ]
}
```
