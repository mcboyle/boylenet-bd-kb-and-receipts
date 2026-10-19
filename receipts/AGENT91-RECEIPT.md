# AGENT-91 RECEIPT: `/home/mboyle/bin/bd-local-rebase-helper`

- **WRITTEN-AT**: 2026-10-17T23:41:59Z
- **TARGET**: `/home/mboyle/bin/bd-local-rebase-helper`
- **MODE**: `-rwxr-xr-x` (`chmod +x`)
- **SHA256**: `b17326e9480407a48edca8641d9e85bb1a81131c0839a017da0f147027d38bbc`
- **ENDPOINT**: `http://10.0.70.164:4010/v1/chat/completions` (`tier1`)
- **LIVE-TEST-RC**: `0`

## Live Verification Output (`--self-test`)
```text
REBASE-HELPER | cuts=4 overlaps=2 disjoint=1 llm=OK
STACK_ORDER: cut-103 -> cut-101 -> cut-104 -> cut-102
OVERLAP: cut-101 <-> cut-102 (1 files: src/router.py)
OVERLAP: cut-102 <-> cut-104 (1 files: src/worker.py)
TIER1_SUGGESTION: {
  "stack_order": ["cut-103", "cut-101", "cut-102", "cut-104"],
  "strategy": "Linear stack with disjoint base cut",
  "risk": "MEDIUM"
}
```

## Live JSON Verification (`--self-test --json`)
```json
{
  "analysis": {
    "conflict_clusters": [
      [
        "cut-101",
        "cut-104",
        "cut-102"
      ]
    ],
    "cut_count": 4,
    "deterministic_stack_order": [
      "cut-103",
      "cut-101",
      "cut-104",
      "cut-102"
    ],
    "disjoint_cuts": [
      "cut-103"
    ],
    "files_by_cut": {
      "cut-101": [
        "src/router.py",
        "tests/test_router.py"
      ],
      "cut-102": [
        "src/router.py",
        "src/worker.py"
      ],
      "cut-103": [
        "docs/README.md"
      ],
      "cut-104": [
        "src/metrics.py",
        "src/worker.py"
      ]
    },
    "overlap_pair_count": 2,
    "overlaps": [
      {
        "cut_a": "cut-101",
        "cut_b": "cut-102",
        "shared_count": 1,
        "shared_files": [
          "src/router.py"
        ]
      },
      {
        "cut_a": "cut-102",
        "cut_b": "cut-104",
        "shared_count": 1,
        "shared_files": [
          "src/worker.py"
        ]
      }
    ]
  },
  "llm_tier1": {
    "endpoint": "http://10.0.70.164:4010/v1/chat/completions",
    "model": "qwen3-coder-30b-a3b",
    "status": "OK",
    "suggestion": "{\n  \"stack_order\": [\"cut-103\", \"cut-101\", \"cut-102\", \"cut-104\"],\n  \"strategy\": \"Linear stack with disjoint base cut\",\n  \"risk\": \"MEDIUM\"\n}"
  },
  "mode": "READ_ONLY_BATCH_OVERLAP",
  "written_at": "2026-10-17T23:41:59Z"
}
```
