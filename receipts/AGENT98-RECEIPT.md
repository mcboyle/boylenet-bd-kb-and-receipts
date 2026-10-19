# AGENT-98 RECEIPT: `/home/mboyle/bin/bd-local-ddr4-top`

- **WRITTEN-AT**: 2026-10-17T23:41:16Z
- **ARTIFACT**: `/home/mboyle/bin/bd-local-ddr4-top` (`-rwxr-xr-x`)
- **SHA256**: `69fe9f857d4b7d6129d297a378a981b09ac98f4c08d4581c3b6c9990ef6034f2`
- **LIVE EXECUTION**: `rc=0`
- **POOL COVERAGE**: `1,730 GiB` (`1.73 TB`) Locked DDR4 RAM pool across `10.0.70.83` (`386 GiB`), `10.0.70.72` (`320 GiB`), `10.0.70.228` (`448 GiB`), `10.0.70.125` (`576 GiB`)

## Live Output
```text
=== BOYLENET LOCKED DDR4 RAM POOL SUMMARY (1,730 GiB / 1.73 TB) [2026-10-17T23:41:16Z] ===
NODE IP       HOSTNAME           NOMINAL      TOTAL       USED      AVAIL  CACHE/SHM   MLOCKED STATUS     
----------------------------------------------------------------------------------------------------------
10.0.70.83    aidev              386 GiB  379.8 GiB    7.8 GiB  372.1 GiB  267.5 GiB  0.02 GiB ONLINE     
10.0.70.72    ai-srv01           320 GiB  314.9 GiB   14.9 GiB  300.0 GiB  290.9 GiB  0.03 GiB ONLINE     
10.0.70.228   ai-ollama01        448 GiB  440.9 GiB    8.7 GiB  432.2 GiB   64.4 GiB  0.02 GiB ONLINE     
10.0.70.125   ai-infer01         576 GiB  566.9 GiB    6.1 GiB  560.8 GiB   81.9 GiB  0.02 GiB ONLINE     
----------------------------------------------------------------------------------------------------------
POOL TOTAL    4x DDR4 NODES     1730 GiB 1702.5 GiB   37.5 GiB 1665.0 GiB  704.7 GiB  0.09 GiB  97.8% FREE
Pool Spec: 1,730 GiB (1.73 TB) Locked DDR4 [10.0.70.83=386G, 10.0.70.72=320G, 10.0.70.228=448G, 10.0.70.125=576G]
```
