# AGENT-89 RECEIPT: bd-local-pytest-runner (O1789)

- **WRITTEN-AT**: 2026-10-17T23:42:07Z
- **TARGET**: `/home/mboyle/bin/bd-local-pytest-runner`
- **SHA256**: `2045e55074597c1ed53394d9283c7a139d65626a08a9716ca33b6ad57c0bd60d`
- **MODE**: `-rwxr-xr-x` (`chmod +x`)
- **TMPDIR**: `/mnt/ramdisk-ci` (Tier 3 RAM-disk)
- **DIGEST / CAS**: `bd-pytest-digest` (Rule 78 `<=3 lines`, measured `2` lines) + `bd-blob-ptr` (Rule 80 CAS `/var/tmp/bd-blobs/<sha256>.log`)
- **TRIAGE**: `bd-local-ci-triage` (`:4010` `tier1`) on non-zero exit

## Verification

### 1. `--help` Probe (`rc=0`)
```text
Usage: bd-local-pytest-runner [PYTEST_ARGS...]
Env: TMPDIR=/mnt/ramdisk-ci | Digest: bd-pytest-digest (<=3 lines) + bd-blob-ptr | Triage: bd-local-ci-triage (:4010 tier1)
```

### 2. 1-Test Smoke Run (`rc=0`, lines=2)
```text
PASS: 1 passed 
BLOB_PTR: /var/tmp/bd-blobs/d2d744b6b2df2bbb0d73cb7a43f6675bd03881bc2a2c2a74e54ecf18667789e2.log (sha256=d2d744b6b2df bytes=98 TMPDIR=/mnt/ramdisk-ci)
```
