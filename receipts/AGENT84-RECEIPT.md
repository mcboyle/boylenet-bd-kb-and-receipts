# AGENT-84 RECEIPT: Systemd Unit Boot-Persistence Audit

- **WRITTEN-AT**: `2026-10-17T23:39:54Z`
- **SEAT**: `Agent-84`
- **CENSUS**: `9` target `(host, unit)` pairs across `5` hosts (`10.0.70.164`, `10.0.70.72`, `10.0.70.83`, `10.0.70.228`, `10.0.70.125`)
- **SUMMARY**: `FOUND 7/9 enabled`, `FOUND 7/9 active` (`rc=0`)

## Verified Systemd Unit Status Table

| Host | Unit | `is-enabled` | `en_rc` | `is-active` | `ac_rc` | `SubState` | `FragmentPath` |
| :--- | :--- | :--- | :---: | :--- | :---: | :--- | :--- |
| `10.0.70.164` | `bd-gpu-orchestrator.service` | `enabled` | `0` | `active` | `0` | `running` | `/etc/systemd/system/bd-gpu-orchestrator.service` |
| `10.0.70.164` | `bd-cockpit.service` | `not-found` | `4` | `inactive` | `4` | `dead` | `` |
| `10.0.70.164` | `bd-chat.service` | `not-found` | `4` | `inactive` | `4` | `dead` | `` |
| `10.0.70.72` | `llama-rpc.service` | `enabled` | `0` | `active` | `0` | `running` | `/etc/systemd/system/llama-rpc.service` |
| `10.0.70.72` | `llama-tier1-alpha.service` | `enabled` | `0` | `active` | `0` | `running` | `/etc/systemd/system/llama-tier1-alpha.service` |
| `10.0.70.83` | `llama-rpc.service` | `enabled` | `0` | `active` | `0` | `running` | `/etc/systemd/system/llama-rpc.service` |
| `10.0.70.228` | `llama-rpc.service` | `enabled` | `0` | `active` | `0` | `running` | `/etc/systemd/system/llama-rpc.service` |
| `10.0.70.125` | `llama-rpc.service` | `enabled` | `0` | `active` | `0` | `running` | `/etc/systemd/system/llama-rpc.service` |
| `10.0.70.125` | `llama-distributed-32b.service` | `enabled` | `0` | `active` | `0` | `running` | `/etc/systemd/system/llama-distributed-32b.service` |
