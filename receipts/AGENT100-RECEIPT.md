# AGENT-100 RECEIPT: `bd-local-prompt-guard`

- **WRITTEN-AT**: 2026-10-17T23:41:20Z
- **TARGET**: `/home/mboyle/bin/bd-local-prompt-guard`
- **SHA256**: `39c415f6ee4ca7cbadd9875f8319d1bd86f6c23ab00db9385e84f8743b9b5e05`
- **MODES SUPPORTED**: `rule74`, `rule76`, `standard`
- **ENDPOINT**: `http://10.0.70.164:4010/v1/chat/completions`
- **LIVE TEST RC**: `0`
- **PRECHECK**: PASS

## Verification Summary
1. `--mode rule74`: Verified Argon-4 Anti-Sycophancy & Anti-Hallucination Contract + Protocol A (`<=50 tokens: <STATUS> <path>`).
2. `--mode rule76 --json`: Verified Argon-4 Contract + Protocol B (`BOARD|REFUTE` strict JSON schema).
3. `--mode standard`: Verified Argon-4 Contract standard wrapper.
4. `--mode rule74 --exec`: Executed against `http://10.0.70.164:4010/v1/chat/completions` with `rc=0`.

### Live Execution Sample
```
[ARGON-4 ANTI-SYCOPHANCY & ANTI-HALLUCINATION CONTRACT]
1. ZERO SYCOPHANCY: Do not praise, flatter, validate, or agree with unverified premises. If a user premise is false, flawed, or unmeasured, refute it directly with evidence.
2. ZERO HALLUCINATION (RULE 6): Never fabricate paths, SHA256 digests, symbols, line numbers, test results, or runtime outputs. Report only measured states: FOUND n, FOUND NONE, or COULD NOT LOOK (UNKNOWN != permission).
3. FALSIFICATION FIRST: Actively test claims against filesystem and runtime ground truth before asserting conclusions.
4. ZERO FILLER (HTAP-9): Omit greetings, apologies, hedging, and conversational padding.
[MODE: RULE 74 - HARD OUTPUT CAP & ZERO-PROSE (PROTOCOL A)]
Output strictly <= 50 tokens: `<STATUS> <path>`. All detailed evidence must reside on disk.
If blocked by genuine ambiguity: `AMBIGUOUS: <path> | (1) <opt1> (2) <opt2>. Reply 1 or 2.`

[USER PROMPT]
Reply PASS /home/mboyle/bin/bd-local-prompt-guard
```
