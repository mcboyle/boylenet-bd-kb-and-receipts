# AGENT-90 RECEIPT: `/home/mboyle/bin/bd-local-fast-lint`

- **WRITTEN-AT**: 2026-10-17T23:41:17Z
- **TARGET**: `/home/mboyle/bin/bd-local-fast-lint` (`-rwxr-xr-x`)
- **SHA256**: `95f76c4b21ff6f6661d55fc956563f6ef4f1d170f706883dc1084f3c56602a04`
- **PROTOCOL**: Argon-4 surgical fix via `http://10.0.70.164:4010/v1/chat/completions` (`tier1`, `77.6 tok/s`)
- **CHECKS**: `ruff check` + `python3 -m py_compile`
- **LIVE_TEST_CLEAN_RC**: `0` (`PASS bd-local-fast-lint (Argon-4 tier1 77.6 tok/s): 1 file(s) clean`)
- **LIVE_TEST_TIER1_RC**: `0` (`{"protocol": "Argon-4", "endpoint": "http://10.0.70.164:4010/v1/chat/completions", "model": "tier1", "throughput": "77.6 tok/s", "checked_files": 1, "rc": 0, "issues_found": 1, "results": [{"file": "/tmp/bd-fast-lint-test-iggj5T.py", "tool": "ruff", "diagnostics": "I001 [*] Import block is un-sorted or un-formatted\n --> /tmp/bd-fast-lint-test-iggj5T.py:1:1\n  |\n1 | import os, sys\n  | ^^^^^^^^^^^^^^\n2 | x = 1\n  |\nhelp: Organize imports\n  |\n  - import os, sys\n1 + import os\n2 + import sys\n3 +\n4 | x = 1\n  |\n\nF401 [*] `os` imported but unused\n --> /tmp/bd-fast-lint-test-iggj5T.py:1:8\n  |\n1 | import os, sys\n  |        ^^\n2 | x = 1\n  |\nhelp: Remove unused import\n  |\n  - import os, sys\n1 | x = 1\n  |\n\nF401 [*] `sys` imported but unused\n --> /tmp/bd-fast-lint-test-iggj5T.py:1:12\n  |\n1 | import os, sys\n  |            ^^^\n2 | x = 1\n  |\nhelp: Remove unused import\n  |\n  - import os, sys\n1 | x = 1\n  |", "argon4_tier1": {"status": "ok", "endpoint": "http://10.0.70.164:4010/v1/chat/completions", "model": "tier1", "fix": "import sys\nx = 1"}}]}`)
- **PRECHECK**: PASS
