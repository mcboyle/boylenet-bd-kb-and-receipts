# AGENT-48 RECEIPT: `/home/mboyle/bin/bd-local-ci-triage`

- **WRITTEN-AT**: 2026-10-17T23:35:03Z
- **ARTIFACT**: `/home/mboyle/bin/bd-local-ci-triage` (`chmod +x`)
- **SHA256**: `c1c96d0d5c86b3327a72d71554cb496a31974af283c930430036a61eee6b6d16`
- **LLM-ENDPOINT**: `http://10.0.70.164:4010/v1/chat/completions` (`model: tier1`)
- **TEST-RC**: `0`

## Coverage & Discriminator Verification
1. **Fleet Rules 41-47 (`bd-ci-taxonomy`)**:
   - Rule 41: `DIRTY_WORKTREE_GATE`
   - Rule 42: `NON_HERMETIC_LEAK`
   - Rule 43: `COLLECTION_SYNTAX_ERROR` (requires literal `SyntaxError:` / `IndentationError:` traceback)
   - Rule 44: `SHARD_TIMEOUT`
   - Rule 45: `PIN_DRIFT`
   - Rule 46: `BEHAVIORAL_RED` (requires genuine test-body failure `=== FAILURES ===`)
   - Rule 47: `INFRA_EPHEMERAL_COLLISION`
2. **GEMINI.md Pytest Digest Setup-Error vs Behavioral RED Discriminator**:
   - Tested against `RED.log` candidate-absent setup-error snippet (`15 errors in 0.42s` + false `bd-pytest-digest` `SyntaxError` banner).
   - Verified: `category == "CANDIDATE_ABSENT_SETUP_ERROR"`, `behavioral_red == false`, `is_syntax_error == false`, `digest_false_syntax_overridden == true`, `rc=0`.

## Sample Test Output
```json
{
  "source": "/tmp/sample-red-setup-error.WDHOhj.log",
  "category": "CANDIDATE_ABSENT_SETUP_ERROR",
  "rule": "GEMINI.md-Setup-Error-Discriminator",
  "behavioral_red": false,
  "is_syntax_error": false,
  "digest_false_syntax_overridden": true,
  "remediation": "Treat measured setup errors as candidate-absent setup errors; do not certify behavioral RED or SyntaxError.",
  "reason": "Detected pytest setup error banner (ERRORS / ERROR at setup) with no literal SyntaxError traceback and 0 behavioral failures.",
  "llm": {
    "ok": false,
    "endpoint": "http://10.0.70.164:4010/v1/chat/completions",
    "model": "tier1",
    "latency_s": 12.041,
    "tok_per_s": null,
    "llm_category": null
  }
}
```
