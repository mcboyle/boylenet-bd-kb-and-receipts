# O3394 Fleet-Wide Auth Audit & Independent OAuth Login Receipt (2026-10-19T19:15Z)

All **24 active Linux VMs** in `/home/mboyle/.config/bd/hosts` now hold:
1. **Independent per-VM OAuth refresh tokens** (`claude auth login --claudeai` completed directly on each VM) for all 4 Claude pools:
   - **Pool A (`~/.claude-a` + `~/.claude`)**: `v4tm2wv4p5@privaterelay.appleid.com` (`24 / 24`)
   - **Pool B (`~/.claude-b`)**: `mcboyle1865@gmail.com` (`24 / 24`)
   - **Pool C (`~/.claude-c`)**: `matthew.boyle@whmo.mil` (`24 / 24`)
   - **Pool D (`~/.claude-d`)**: `itdude1865@pm.me` (`24 / 24`)
2. **Codex (`~/.local/bin/codex` + `~/.codex/auth.json`)**: `Logged in using ChatGPT` (`24 / 24`)
3. **Kimi (`~/.local/bin/kimi` + `~/.kimi-code/credentials/kimi-code-env-0e4f99c69cc27850.json` + `config.toml`)**: `OK` (`24 / 24`)
4. **AGY (`~/.local/bin/agy` + `~/.gemini/antigravity-cli/antigravity-oauth-token`)**: `OK` (`24 / 24`)
