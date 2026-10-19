# AGENT-103 RECEIPT: `/home/mboyle/bin/bd-local-blob-cat`

- **WRITTEN-AT**: 2026-10-17T23:41:27Z
- **TARGET**: `/home/mboyle/bin/bd-local-blob-cat` (`chmod +x`)
- **SHA256**: `6eb359187306f4784536c5a8eb0e8b3fc1e3253f301ce216c35998c5a6ea2935`
- **RULE**: Rule 80 (`/var/tmp/bd-blobs/<sha256>.log`) bounded CAS blob inspection (hard default/max cap of 15 payload lines so local GPU agents never ingest raw >15-line outputs).
- **SUPPORTED FLAGS**: `--head N`, `--tail N`, `--grep PATTERN`, `-i/--ignore-case`, `-C/--context`, positional or `--blob/--sha/--file` reference resolution (full path, `<sha256>.log`, `<sha256>`, or prefix).

## Live Verification (`rc=0`)
- Test Blob: `/var/tmp/bd-blobs/ca055735adfbe6a710457ac33d436bfc02f966c3d5b55b0b085982643192218d.log` (30 lines)
- Default bounded output: `15` payload lines (capped at 15, `rc=0`)
- `--head 3`: `3` payload lines (`rc=0`)
- `--tail 4`: `4` payload lines (`rc=0`)
- `--grep CRITICAL_ERROR_MARKER`: matched line 26 (`1` match, `rc=0`)
