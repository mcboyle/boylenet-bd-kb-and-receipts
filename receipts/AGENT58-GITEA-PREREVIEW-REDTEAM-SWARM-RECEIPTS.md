# AGENT-58 RECEIPT: Gitea (`:3000`) PR Webhook -> Autonomous Multi-Lens Pre-Review & Security Red-Team Swarm (`bd-gitea-prereview` on `:4092`)

- **Timestamp (UTC)**: `2026-10-18T15:46:30Z`
- **Service Unit**: `/etc/systemd/system/bd-gitea-prereview.service` (`active (running)`, enabled on boot)
- **Executable & CLI**: `/home/mboyle/bin/bd-gitea-prereview.py` (`0.0.0.0:4092` + `--diff <git-range-or-path>`)
- **Web Dashboard URLs**:
  - Direct IP: `http://10.0.70.164:4092`
  - Port-80 FQDN: `http://prereview.boylenet.themfboyles.com`
  - Port-80 Shortname: `http://prereview`
- **Gitea Webhook Endpoint**: `http://10.0.70.164:4092/webhook/gitea`
- **API Review Endpoint**: `POST http://10.0.70.164:4092/api/review`
- **Persistent Report Store**: `/var/tmp/bd-gitea-prereview/reviews/` & `/var/tmp/bd-gitea-prereview/reviews_index.json`

---

## 1. Architecture & 3-Stage Autonomous Pipeline

1. **Stage 1 — Static Fast-Fail Gates (`<25 ms`)**:
   - **Ruff Check (`/home/mboyle/.local/bin/ruff check`)**: Syntax & lint verification on modified/added Python files.
   - **Tree-Sitter AST Server (`http://10.0.70.164:8095`)**: Health verification (`/health`, `ast-grep 0.45.3`), AST syntax validation (`/parse`), and structural dangerous-pattern matching (`/query` for `eval($A)` and `exec($A)`).
   - **Secret & Credential Scanner**: Regex scan across added diff lines (`+...`) for AWS Access Key IDs, Private Key headers, GitHub/OAuth tokens, OpenAI/Anthropic production keys, Slack tokens, and hardcoded password assignments.

2. **Stage 2 — Parallel Local GPU Multi-Lens Review (`$0.00` Marginal Token Cost via `http://10.0.70.228:4000/v1`)**:
   - **Lens 1 — Behavioral & Logic Defect Lens (`qwen3-coder-30b-a3b`)**: Evaluates diffs under **Fleet Rule 86 (Protocol F)** (`BOARD` vs `REFUTE` vs `BOARD-WITH-ADVISORY`), checking only for demonstrable behavioral defects, runtime crashes, regressions, and race conditions.
   - **Lens 2 — OffSec & Security Red-Team Lens (`cyberstrike-offsec-35b` with fallback `qwen3-coder-30b-a3b`)**: Audits diffs for command injection (`CWE-78`), arbitrary code execution (`CWE-95`), hardcoded credentials (`CWE-798`), SSRF, path traversal, and auth bypass.

3. **Stage 3 — Gitea Feedback Loop & Cyber-Glass Web UI (`:4092`)**:
   - Automatically posts commit statuses (`boylenet/prereview-2lens-swarm`) and structured Markdown review comments back to Gitea (`http://10.0.70.164:3000`).
   - Stores every CLI, API, and Webhook review report in `/var/tmp/bd-gitea-prereview/` and renders a live Cyber-Glass Web UI on `http://prereview.boylenet.themfboyles.com` (`:4092`) with an interactive diff/commit paste workbench.

---

## 2. UniFi Static DNS (`10.0.70.1`), Traefik (`:80`), Dashy (`:4080`), & Gitea (`:3000`) Registration

### 2.1 Authoritative UniFi `static-dns` Records (`10.0.70.1`)
| Record Key | Type | Value | UniFi Record `_id` | `dig +short @10.0.70.1` |
| :--- | :--- | :--- | :--- | :--- |
| `prereview` | `A` | `10.0.70.164` | `326e6753-3c6b-455c-a49c-62f04a93f54a` | `10.0.70.164` |
| `prereview.boylenet.themfboyles.com` | `A` | `10.0.70.164` | `2af53224-433b-404a-9569-95c35cb898b3` | `10.0.70.164` |

### 2.2 Traefik Ingress (`/home/mboyle/infra/expansion-layer/dynamic/boylenet-dns-ingress.yml`)
- Router `boylenet-prereview`: `Host(\`prereview.boylenet.themfboyles.com\`) || Host(\`prereview\`)` -> `boylenet-prereview-svc` (`http://10.0.70.164:4092`).
- Verified via `curl -s -H "Host: prereview.boylenet.themfboyles.com" http://10.0.70.164/health` -> `200 OK`.

### 2.3 Dashy Portal (`/home/mboyle/infra/dashy/conf.yml`)
- Added top nav link `PreReview (:4092)` -> `http://prereview.boylenet.themfboyles.com`.
- Added AI Studio card `Gitea Pre-Review & OffSec Red-Team Swarm :4092` with live health check (`http://10.0.70.164:4092/health`).
- Added DNS directory entry `prereview.boylenet.themfboyles.com`.

### 2.4 Gitea (`http://10.0.70.164:3000`) Webhooks & Config
- Enabled `[webhook] ALLOWED_HOST_LIST = *` in Forgejo `/data/gitea/conf/app.ini` so local subnet webhook delivery to `10.0.70.164:4092` is permitted.
- Generated dedicated API token at `/home/mboyle/.config/bd/gitea-prereview-token`.
- Registered active webhook `http://10.0.70.164:4092/webhook/gitea` (events: `push`, `pull_request`, `pull_request_sync`) across all 4 Gitea repositories:
  - `mboyle/BD-upstream` (`hook_id=1`)
  - `mboyle/test-repo` (`hook_id=2`)
  - `mboyle/pytest` (`hook_id=3`)
  - `mboyle/yt-dlp` (`hook_id=4`)

---

## 3. Live End-to-End Verification Evidence

### 3.1 Real Git Commit Review (`POST /api/review` on `BulkDownloader HEAD~1..HEAD` + Gitea Status Postback)
```json
{
  "review_id": "rev-1792338304-b34c43",
  "overall_verdict": "BOARD-WITH-ADVISORY",
  "total_elapsed_ms": 9690.9,
  "lens1": {
    "lens": "lens1_behavioral",
    "model": "qwen3-coder-30b-a3b",
    "round": "rev-1792338304-b34c43",
    "verdict": "BOARD",
    "summary": "Version string updates across multiple files to reflect v3.66.1908, with no behavioral defects or logic issues detected.",
    "findings": [],
    "elapsed_ms": 7096.9,
    "tokens": 1144,
    "cost_usd": 0.0,
    "status": "ok"
  },
  "lens2": {
    "lens": "lens2_security_redteam",
    "model": "cyberstrike-offsec-35b",
    "round": "rev-1792338304-b34c43",
    "verdict": "BOARD",
    "summary": "No security vulnerabilities detected in the provided git diff. The changes are limited to version string updates in configuration and test files, with no exploitable code modifications.",
    "findings": [],
    "elapsed_ms": 6929.7,
    "tokens": 1290,
    "cost_usd": 0.0,
    "status": "ok"
  },
  "gitea_feedback": {
    "posted": true,
    "repo": "mboyle/BD-upstream",
    "commit_status_code": 201
  }
}
```

### 3.2 Gitea Webhook Red-Team Adversarial Verification (`POST /webhook/gitea` -> `REFUTE`)
```json
{
  "review_id": "rev-1792338344-ebba11",
  "overall_verdict": "REFUTE",
  "total_elapsed_ms": 8972.6,
  "static_gates": {
    "passed": false,
    "elapsed_ms": 24.8,
    "tree_sitter_ast": {
      "server_url": "http://10.0.70.164:8095",
      "server_healthy": true,
      "ast_grep_version": "0.45.3",
      "checked_files": 1,
      "passed": false,
      "finding_count": 1,
      "findings": [
        {
          "file": "diag_exec.py",
          "line": 1,
          "issue": "AST pattern `eval($A)` matched: STDIN:5:    return eval(out)"
        }
      ]
    },
    "secret_scan": {
      "passed": false,
      "finding_count": 1,
      "findings": [
        {
          "file": "diag_exec.py",
          "line": 2,
          "type": "AWS Access Key ID",
          "snippet": "AWS_KEY = \"<REDACTED_BY_BOYLENET_SECRET_GUARD>\""
        }
      ]
    }
  },
  "lens1": {
    "model": "qwen3-coder-30b-a3b",
    "verdict": "REFUTE",
    "elapsed_ms": 7176.2,
    "findings": [
      {
        "file": "diag_exec.py",
        "line": 5,
        "severity": "HIGH",
        "issue": "Use of `eval()` on untrusted HTTP response data creates a critical remote code execution vulnerability."
      }
    ]
  },
  "lens2": {
    "model": "cyberstrike-offsec-35b",
    "verdict": "REFUTE",
    "elapsed_ms": 8942.7,
    "findings": [
      {"file": "diag_exec.py", "line": 5, "cwe": "CWE-95", "severity": "CRITICAL"},
      {"file": "diag_exec.py", "line": 3, "cwe": "CWE-798", "severity": "CRITICAL"},
      {"file": "diag_exec.py", "line": 4, "cwe": "CWE-78", "severity": "CRITICAL"}
    ]
  }
}
```
