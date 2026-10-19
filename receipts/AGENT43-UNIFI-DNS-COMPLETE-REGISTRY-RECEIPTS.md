# AGENT-43: UniFi DNS Complete Infrastructure & Web UI Service Registry Receipts

- **Timestamp (UTC)**: `2026-10-18T12:15:42Z`
- **UniFi Cloud Console ID**: `602232292CA100000000069BFAAB0000000006EB9D760000000062F4D112:2071288107`
- **Gateway Resolver**: `10.0.70.1` (`UDMPROSE` / `BoyleNetwork`)
- **Search Domain**: `boylenet.themfboyles.com`
- **Total Active Static DNS Records in UniFi (`/v2/api/site/default/static-dns`)**: `382`
- **Active Network Clients Assigned Friendly Names (`/api/s/default/rest/user/<_id>`)**: `33`
- **Safety Invariants**: Family VLAN 5 (`10.0.5.1/24`) = `INTACT` | Dual-WAN (`3` WAN interfaces) = `INTACT`

---

## 1. Summary by Category (IP -> Short Hostnames & FQDNs)

| Category | IP Address | Short Hostnames (`A`) | Full FQDNs (`A`) |
| :--- | :--- | :--- | :--- |
| Management & Network | `10.0.70.1` | `unifi`, `gateway`, `udmpro`, `unifi-gateway` | `unifi.boylenet.themfboyles.com`, `gateway.boylenet.themfboyles.com`, `udmpro.boylenet.themfboyles.com`, `unifi-gateway.boylenet.themfboyles.com` |
| Management & Network | `10.0.10.111` | `usaggpro`, `switch-agg` | `usaggpro.boylenet.themfboyles.com`, `switch-agg.boylenet.themfboyles.com` |
| Management & Network | `10.0.10.182` | `usl8a`, `switch-8a` | `usl8a.boylenet.themfboyles.com`, `switch-8a.boylenet.themfboyles.com` |
| Management & Network | `10.0.10.34` | `uspm16p`, `switch-16p` | `uspm16p.boylenet.themfboyles.com`, `switch-16p.boylenet.themfboyles.com` |
| Management & Network | `10.0.10.192` | `usl24b`, `switch-24b` | `usl24b.boylenet.themfboyles.com`, `switch-24b.boylenet.themfboyles.com` |
| Management & Network | `10.0.10.221` | `u7pro`, `ap-u7pro` | `u7pro.boylenet.themfboyles.com`, `ap-u7pro.boylenet.themfboyles.com` |
| Management & Network | `10.0.1.147` | `uapa697`, `ap-pantry` | `uapa697.boylenet.themfboyles.com`, `ap-pantry.boylenet.themfboyles.com` |
| Management & Network | `10.0.1.97` | `udba69f`, `switch-udb` | `udba69f.boylenet.themfboyles.com`, `switch-udb.boylenet.themfboyles.com` |
| Management & Network | `10.0.1.98` | `usl8a-pantry`, `switch-pantry` | `usl8a-pantry.boylenet.themfboyles.com`, `switch-pantry.boylenet.themfboyles.com` |
| Management & Network | `10.0.1.145` | `u6plus`, `ap-u6plus` | `u6plus.boylenet.themfboyles.com`, `ap-u6plus.boylenet.themfboyles.com` |
| Management & Network | `10.0.10.10` | `ilom-10`, `esxi01-ilo` | `ilom-10.boylenet.themfboyles.com`, `esxi01-ilo.boylenet.themfboyles.com` |
| Management & Network | `10.0.10.20` | `ilom-20`, `esxi02-ilo` | `ilom-20.boylenet.themfboyles.com`, `esxi02-ilo.boylenet.themfboyles.com` |
| Management & Network | `10.0.10.30` | `ilom-30`, `esxi03-ilo` | `ilom-30.boylenet.themfboyles.com`, `esxi03-ilo.boylenet.themfboyles.com` |
| Management & Network | `10.0.10.40` | `ilom-40`, `esxi04-ilo` | `ilom-40.boylenet.themfboyles.com`, `esxi04-ilo.boylenet.themfboyles.com` |
| Management & Network | `10.0.10.252` | `ilom-252` | `ilom-252.boylenet.themfboyles.com` |
| Management & Network | `10.0.10.101` | `dc01` | `dc01.boylenet.themfboyles.com` |
| Management & Network | `10.0.10.137` | `battlestation`, `oracle-bs` | `battlestation.boylenet.themfboyles.com`, `oracle-bs.boylenet.themfboyles.com` |
| Management & Network | `10.0.10.224` | `box` | `box.boylenet.themfboyles.com` |
| Management & Network | `10.0.10.88` | `garage` | `garage.boylenet.themfboyles.com` |
| Management & Network | `10.0.120.3` | `ws01` | `ws01.boylenet.themfboyles.com` |
| Management & Network | `10.0.1.125` | `desktop-co8d5gv` | `desktop-co8d5gv.boylenet.themfboyles.com` |
| Management & Network | `10.0.1.136` | `front-door` | `front-door.boylenet.themfboyles.com` |
| VMware vSphere & ESXi | `10.0.20.70` | `vc04`, `vcenter`, `vsphere` | `vc04.boylenet.themfboyles.com`, `vcenter.boylenet.themfboyles.com`, `vsphere.boylenet.themfboyles.com` |
| VMware vSphere & ESXi | `10.0.20.41` | `esxi01` | `esxi01.boylenet.themfboyles.com` |
| VMware vSphere & ESXi | `10.0.20.42` | `esxi02` | `esxi02.boylenet.themfboyles.com` |
| VMware vSphere & ESXi | `10.0.20.43` | `esxi03` | `esxi03.boylenet.themfboyles.com` |
| VMware vSphere & ESXi | `10.0.20.44` | `esxi04` | `esxi04.boylenet.themfboyles.com` |
| VMware vSphere & ESXi | `10.0.20.220` | `esxi-mgmt-220` | `esxi-mgmt-220.boylenet.themfboyles.com` |
| VMware vSphere & ESXi | `10.0.30.13` | `vc04-vcha`, `vcha-primary` | `vc04-vcha.boylenet.themfboyles.com`, `vcha-primary.boylenet.themfboyles.com` |
| VMware vSphere & ESXi | `10.0.30.4` | `vc04-peer`, `vcha-peer` | `vc04-peer.boylenet.themfboyles.com`, `vcha-peer.boylenet.themfboyles.com` |
| VMware vSphere & ESXi | `10.0.30.5` | `vc04-witness`, `vcha-witness` | `vc04-witness.boylenet.themfboyles.com`, `vcha-witness.boylenet.themfboyles.com` |
| VMware vSphere & ESXi | `10.0.40.21` | `esxi01-ft` | `esxi01-ft.boylenet.themfboyles.com` |
| VMware vSphere & ESXi | `10.0.40.22` | `esxi02-ft` | `esxi02-ft.boylenet.themfboyles.com` |
| VMware vSphere & ESXi | `10.0.40.23` | `esxi03-ft` | `esxi03-ft.boylenet.themfboyles.com` |
| VMware vSphere & ESXi | `10.0.40.24` | `esxi04-ft` | `esxi04-ft.boylenet.themfboyles.com` |
| VMware vSphere & ESXi | `10.0.50.21` | `esxi01-vmotion` | `esxi01-vmotion.boylenet.themfboyles.com` |
| VMware vSphere & ESXi | `10.0.50.22` | `esxi02-vmotion` | `esxi02-vmotion.boylenet.themfboyles.com` |
| VMware vSphere & ESXi | `10.0.50.23` | `esxi03-vmotion` | `esxi03-vmotion.boylenet.themfboyles.com` |
| VMware vSphere & ESXi | `10.0.50.24` | `esxi04-vmotion` | `esxi04-vmotion.boylenet.themfboyles.com` |
| VMware vSphere & ESXi | `10.0.60.21` | `esxi01-vsan` | `esxi01-vsan.boylenet.themfboyles.com` |
| VMware vSphere & ESXi | `10.0.60.22` | `esxi02-vsan` | `esxi02-vsan.boylenet.themfboyles.com` |
| VMware vSphere & ESXi | `10.0.60.23` | `esxi03-vsan` | `esxi03-vsan.boylenet.themfboyles.com` |
| VMware vSphere & ESXi | `10.0.60.24` | `esxi04-vsan` | `esxi04-vsan.boylenet.themfboyles.com` |
| GPU AI Nodes & RoCE | `10.0.70.72` | `ai`, `ai-srv01`, `ai-vault01`, `vault-a`, `ai-webui`, `switch-72` | `ai.boylenet.themfboyles.com`, `ai-srv01.boylenet.themfboyles.com`, `ai-vault01.boylenet.themfboyles.com`, `vault-a.boylenet.themfboyles.com`, `ai-webui.boylenet.themfboyles.com`, `switch-72.boylenet.themfboyles.com` |
| GPU AI Nodes & RoCE | `10.0.70.83` | `aidev`, `ai-dev`, `ai-vault02`, `vault-b` | `aidev.boylenet.themfboyles.com`, `ai-dev.boylenet.themfboyles.com`, `ai-vault02.boylenet.themfboyles.com`, `vault-b.boylenet.themfboyles.com` |
| GPU AI Nodes & RoCE | `10.0.70.125` | `ai-infer01`, `aiinference`, `qdrant` | `ai-infer01.boylenet.themfboyles.com`, `aiinference.boylenet.themfboyles.com`, `qdrant.boylenet.themfboyles.com` |
| GPU AI Nodes & RoCE | `10.0.70.228` | `ai-ollama01`, `langfuse`, `ollama`, `llama` | `ai-ollama01.boylenet.themfboyles.com`, `langfuse.boylenet.themfboyles.com`, `ollama.boylenet.themfboyles.com`, `llama.boylenet.themfboyles.com` |
| GPU AI Nodes & RoCE | `10.0.75.11` | `aidev-roce` | `aidev-roce.boylenet.themfboyles.com` |
| GPU AI Nodes & RoCE | `10.0.75.12` | `ai-roce` | `ai-roce.boylenet.themfboyles.com` |
| GPU AI Nodes & RoCE | `10.0.75.13` | `ai-infer01-roce` | `ai-infer01-roce.boylenet.themfboyles.com` |
| GPU AI Nodes & RoCE | `10.0.75.14` | `ai-ollama01-roce` | `ai-ollama01-roce.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.20` | `switch-20`, `pump` | `switch-20.boylenet.themfboyles.com`, `pump.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.25` | `mcp`, `ops-mcp01`, `switch-25` | `mcp.boylenet.themfboyles.com`, `ops-mcp01.boylenet.themfboyles.com`, `switch-25.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.31` | `fitness`, `infra-fresh0` | `fitness.boylenet.themfboyles.com`, `infra-fresh0.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.35` | `seatc1`, `bd-seat-c1` | `seatc1.boylenet.themfboyles.com`, `bd-seat-c1.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.50` | `bd`, `wrk-bd01` | `bd.boylenet.themfboyles.com`, `wrk-bd01.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.51` | `bd1`, `wrk-bd02` | `bd1.boylenet.themfboyles.com`, `wrk-bd02.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.52` | `bd2`, `wrk-bd03` | `bd2.boylenet.themfboyles.com`, `wrk-bd03.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.53` | `bd3`, `wrk-bd04` | `bd3.boylenet.themfboyles.com`, `wrk-bd04.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.54` | `bd4`, `wrk-bd05` | `bd4.boylenet.themfboyles.com`, `wrk-bd05.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.80` | `test3`, `wrk-test03` | `test3.boylenet.themfboyles.com`, `wrk-test03.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.81` | `spare4` | `spare4.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.82` | `test3big`, `wrk-test04` | `test3big.boylenet.themfboyles.com`, `wrk-test04.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.84` | `test7`, `wrk-test07` | `test7.boylenet.themfboyles.com`, `wrk-test07.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.85` | `test4`, `wrk-test05` | `test4.boylenet.themfboyles.com`, `wrk-test05.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.86` | `cir2`, `bd-ci-r2` | `cir2.boylenet.themfboyles.com`, `bd-ci-r2.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.95` | `test2`, `wrk-test02` | `test2.boylenet.themfboyles.com`, `wrk-test02.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.107` | `ops-test01`, `infra-test` | `ops-test01.boylenet.themfboyles.com`, `infra-test.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.120` | `spare7`, `spr-pool01` | `spare7.boylenet.themfboyles.com`, `spr-pool01.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.121` | `cir1`, `bd-ci-r1` | `cir1.boylenet.themfboyles.com`, `bd-ci-r1.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.122` | `spare3` | `spare3.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.149` | `fresh149` | `fresh149.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.162` | `cicd`, `ops-cicd01` | `cicd.boylenet.themfboyles.com`, `ops-cicd01.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.164` | `test5`, `hub-mesh01`, `controller` | `test5.boylenet.themfboyles.com`, `hub-mesh01.boylenet.themfboyles.com`, `controller.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.171` | `seatc2`, `bd-seat-c2` | `seatc2.boylenet.themfboyles.com`, `bd-seat-c2.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.181` | `bittorrent`, `oracle-bt` | `bittorrent.boylenet.themfboyles.com`, `oracle-bt.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.182` | `cache`, `stg-cache01` | `cache.boylenet.themfboyles.com`, `stg-cache01.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.183` | `spare12`, `spr-pool02` | `spare12.boylenet.themfboyles.com`, `spr-pool02.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.184` | `spare8` | `spare8.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.185` | `spare5` | `spare5.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.186` | `spare6` | `spare6.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.187` | `spare9` | `spare9.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.188` | `wrk-188` | `wrk-188.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.189` | `wrk-189` | `wrk-189.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.190` | `wrk-190` | `wrk-190.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.191` | `wrk-191` | `wrk-191.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.193` | `spare2` | `spare2.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.194` | `test1`, `wrk-test01` | `test1.boylenet.themfboyles.com`, `wrk-test01.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.195` | `spare11` | `spare11.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.196` | `spare10` | `spare10.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.197` | `spare1` | `spare1.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.198` | `kvcache-cx1` | `kvcache-cx1.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.224` | `spare` | `spare.boylenet.themfboyles.com` |
| Fleet & Media VMs | `10.0.70.249` | `test6`, `wrk-test06` | `test6.boylenet.themfboyles.com`, `wrk-test06.boylenet.themfboyles.com` |
| Dashy & Web UI Services | `10.0.70.164` | `dashy`, `portal`, `dashboard`, `jarvis`, `cockpit`, `chat`, `openwebui`, `litellm`, `orchestrator`, `gitea`, `git`, `grafana`, `prometheus`, `alertmanager`, `minio`, `traefik`, `cadvisor`, `devdocs`, `searxng`, `search`, `codeserver`, `ide`, `verdaccio`, `npm`, `devpi`, `pypi`, `acng`, `apt`, `elastic`, `treesitter`, `ast`, `registry`, `novnc` | `dashy.boylenet.themfboyles.com`, `portal.boylenet.themfboyles.com`, `dashboard.boylenet.themfboyles.com`, `jarvis.boylenet.themfboyles.com`, `cockpit.boylenet.themfboyles.com`, `chat.boylenet.themfboyles.com`, `openwebui.boylenet.themfboyles.com`, `litellm.boylenet.themfboyles.com`, `orchestrator.boylenet.themfboyles.com`, `gitea.boylenet.themfboyles.com`, `git.boylenet.themfboyles.com`, `grafana.boylenet.themfboyles.com`, `prometheus.boylenet.themfboyles.com`, `alertmanager.boylenet.themfboyles.com`, `minio.boylenet.themfboyles.com`, `traefik.boylenet.themfboyles.com`, `cadvisor.boylenet.themfboyles.com`, `devdocs.boylenet.themfboyles.com`, `searxng.boylenet.themfboyles.com`, `search.boylenet.themfboyles.com`, `codeserver.boylenet.themfboyles.com`, `ide.boylenet.themfboyles.com`, `verdaccio.boylenet.themfboyles.com`, `npm.boylenet.themfboyles.com`, `devpi.boylenet.themfboyles.com`, `pypi.boylenet.themfboyles.com`, `acng.boylenet.themfboyles.com`, `apt.boylenet.themfboyles.com`, `elastic.boylenet.themfboyles.com`, `treesitter.boylenet.themfboyles.com`, `ast.boylenet.themfboyles.com`, `registry.boylenet.themfboyles.com`, `novnc.boylenet.themfboyles.com` |

---

## 2. Complete Authoritative UniFi `static-dns` Table (`GET /v2/api/site/default/static-dns`)

| # | IP Address (`value`) | DNS Record (`key`) | Type | Enabled | UniFi Record `_id` |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | `10.0.1.97` | `switch-udb` | `A` | `True` | `4189c239-9330-4d2e-8f12-188ccc657938` |
| 2 | `10.0.1.97` | `switch-udb.boylenet.themfboyles.com` | `A` | `True` | `f9a05b98-f3ed-439a-b579-a36de9970a9c` |
| 3 | `10.0.1.97` | `udba69f` | `A` | `True` | `2daee8ea-d6c6-4341-95b4-7f4ddb5ccfb0` |
| 4 | `10.0.1.97` | `udba69f.boylenet.themfboyles.com` | `A` | `True` | `00a90e24-1b19-401e-b37a-6af8d1dbf356` |
| 5 | `10.0.1.98` | `switch-pantry` | `A` | `True` | `d205969c-d632-4307-b576-8e263c768024` |
| 6 | `10.0.1.98` | `switch-pantry.boylenet.themfboyles.com` | `A` | `True` | `cf7551ea-b4da-4540-9bb5-9f9fa60c8959` |
| 7 | `10.0.1.98` | `usl8a-pantry` | `A` | `True` | `28efbc99-4e01-47ab-aba1-760c532a4afe` |
| 8 | `10.0.1.98` | `usl8a-pantry.boylenet.themfboyles.com` | `A` | `True` | `503e5846-6ad0-4d57-a319-5cb51d771f67` |
| 9 | `10.0.1.125` | `desktop-co8d5gv` | `A` | `True` | `e45bab5d-8974-490c-9c11-9b6b60acc429` |
| 10 | `10.0.1.125` | `desktop-co8d5gv.boylenet.themfboyles.com` | `A` | `True` | `c3d5088d-6991-4c60-a76d-83d578058f4f` |
| 11 | `10.0.1.136` | `front-door` | `A` | `True` | `52da2576-4feb-40f3-9b40-8ef16b60e0a9` |
| 12 | `10.0.1.136` | `front-door.boylenet.themfboyles.com` | `A` | `True` | `d33e597f-4868-4036-bc91-2a8a9d4c1da3` |
| 13 | `10.0.1.145` | `ap-u6plus` | `A` | `True` | `5c945e06-87a6-4ea7-b5f7-c0962212d084` |
| 14 | `10.0.1.145` | `ap-u6plus.boylenet.themfboyles.com` | `A` | `True` | `a513db8f-6787-46d9-94db-d7e490aff7c9` |
| 15 | `10.0.1.145` | `u6plus` | `A` | `True` | `ca799186-e06f-42a4-9c75-dc0adb0d6213` |
| 16 | `10.0.1.145` | `u6plus.boylenet.themfboyles.com` | `A` | `True` | `7d2b1582-31c4-4b92-83cc-fb9e71ae1920` |
| 17 | `10.0.1.147` | `ap-pantry` | `A` | `True` | `4ffd4bcc-86bc-42ca-a8dc-c58d17c7daa0` |
| 18 | `10.0.1.147` | `ap-pantry.boylenet.themfboyles.com` | `A` | `True` | `003a49c8-0301-46a7-9076-25a67111d69c` |
| 19 | `10.0.1.147` | `uapa697` | `A` | `True` | `e304d163-bf08-4a1c-aa1b-e47ae6a19da1` |
| 20 | `10.0.1.147` | `uapa697.boylenet.themfboyles.com` | `A` | `True` | `3131a648-6e64-4c5e-952c-e019a166dd2a` |
| 21 | `10.0.10.10` | `esxi01-ilo` | `A` | `True` | `f461f393-8702-4c33-9422-1b9326c64459` |
| 22 | `10.0.10.10` | `esxi01-ilo.boylenet.themfboyles.com` | `A` | `True` | `97aad32c-10e6-4276-9480-a96c894d4f59` |
| 23 | `10.0.10.10` | `ilom-10` | `A` | `True` | `beccd966-bcfc-4e69-af81-800d3ce1186e` |
| 24 | `10.0.10.10` | `ilom-10.boylenet.themfboyles.com` | `A` | `True` | `940fa473-f860-4f52-855c-03f8c086a55f` |
| 25 | `10.0.10.20` | `esxi02-ilo` | `A` | `True` | `b47a0f32-7557-4341-b3f9-dcde16e0ee48` |
| 26 | `10.0.10.20` | `esxi02-ilo.boylenet.themfboyles.com` | `A` | `True` | `b4a8d8da-57fa-4a63-9854-ad4ed31110f5` |
| 27 | `10.0.10.20` | `ilom-20` | `A` | `True` | `0f6b08c7-f2ae-4abd-978b-688a54144dab` |
| 28 | `10.0.10.20` | `ilom-20.boylenet.themfboyles.com` | `A` | `True` | `752bb9b1-4b9a-43ee-824f-5630d953b5bb` |
| 29 | `10.0.10.30` | `esxi03-ilo` | `A` | `True` | `42c27025-b4af-402e-8cdf-9bb39f71a7e3` |
| 30 | `10.0.10.30` | `esxi03-ilo.boylenet.themfboyles.com` | `A` | `True` | `9aa4756b-fc23-4030-938b-2ebf7ca11e1f` |
| 31 | `10.0.10.30` | `ilom-30` | `A` | `True` | `c948cbbc-ddf6-4ca5-9bb4-55e58d6e531d` |
| 32 | `10.0.10.30` | `ilom-30.boylenet.themfboyles.com` | `A` | `True` | `7beee30a-1044-47e2-9dda-63b79f622814` |
| 33 | `10.0.10.34` | `switch-16p` | `A` | `True` | `91d294d7-d3da-4055-8717-d38ebf858a01` |
| 34 | `10.0.10.34` | `switch-16p.boylenet.themfboyles.com` | `A` | `True` | `a323aee6-23d7-4b27-9934-860f9637c8c6` |
| 35 | `10.0.10.34` | `uspm16p` | `A` | `True` | `224ee382-4250-4827-bff6-888b021ff4ca` |
| 36 | `10.0.10.34` | `uspm16p.boylenet.themfboyles.com` | `A` | `True` | `ab16b62a-976c-4eac-9a87-8534e6ee8d44` |
| 37 | `10.0.10.40` | `esxi04-ilo` | `A` | `True` | `ee74accb-9118-4ff1-80f6-04c1fc9911e3` |
| 38 | `10.0.10.40` | `esxi04-ilo.boylenet.themfboyles.com` | `A` | `True` | `9e0e252e-9059-4f86-adda-44f5691822d3` |
| 39 | `10.0.10.40` | `ilom-40` | `A` | `True` | `f407b90d-8bcb-484a-9d04-51f826f96bb3` |
| 40 | `10.0.10.40` | `ilom-40.boylenet.themfboyles.com` | `A` | `True` | `91fb5b3e-4652-4c3d-be3b-a71e6e7bf2f4` |
| 41 | `10.0.10.88` | `garage` | `A` | `True` | `65318531-b39a-40db-954e-031d59610ca3` |
| 42 | `10.0.10.88` | `garage.boylenet.themfboyles.com` | `A` | `True` | `1e05b673-b1fa-495e-80f6-11819a897492` |
| 43 | `10.0.10.101` | `dc01` | `A` | `True` | `d9b26297-acbd-4e55-87cc-6d153aa48c67` |
| 44 | `10.0.10.101` | `DC01.boylenet.themfboyles.com` | `A` | `True` | `2b08e38d-5b95-4b91-9f95-7e4552cd7dc6` |
| 45 | `10.0.10.111` | `switch-agg` | `A` | `True` | `4f787139-f8a9-4222-b028-33c9a5775dc9` |
| 46 | `10.0.10.111` | `switch-agg.boylenet.themfboyles.com` | `A` | `True` | `036ab7af-89b9-421f-a47a-a268d1a4fce0` |
| 47 | `10.0.10.111` | `usaggpro` | `A` | `True` | `99cd1e0b-e6db-451f-9784-daa4006e97c4` |
| 48 | `10.0.10.111` | `usaggpro.boylenet.themfboyles.com` | `A` | `True` | `4c692b2a-cf3e-42fa-8cdd-1f5b6ff85586` |
| 49 | `10.0.10.137` | `battlestation` | `A` | `True` | `d57098ad-9071-4551-a46c-5edb6cbdb1f2` |
| 50 | `10.0.10.137` | `battlestation.boylenet.themfboyles.com` | `A` | `True` | `b27e9c00-4383-4c29-924b-412d8a92ddc5` |
| 51 | `10.0.10.137` | `oracle-bs` | `A` | `True` | `6148e076-65fe-4657-bed4-8155b504be64` |
| 52 | `10.0.10.137` | `oracle-bs.boylenet.themfboyles.com` | `A` | `True` | `3c5d473f-f008-4f0a-8dab-56d46c6ee9ff` |
| 53 | `10.0.10.182` | `switch-8a` | `A` | `True` | `9011d589-eadd-437f-af3b-813a661c9e09` |
| 54 | `10.0.10.182` | `switch-8a.boylenet.themfboyles.com` | `A` | `True` | `07b752fc-7dfd-4bf2-8b9f-3d0733fc4e99` |
| 55 | `10.0.10.182` | `usl8a` | `A` | `True` | `1768232d-7faf-4944-ba1c-3131d6dfa025` |
| 56 | `10.0.10.182` | `usl8a.boylenet.themfboyles.com` | `A` | `True` | `0eb27539-ffa4-474d-81cb-7712183797c7` |
| 57 | `10.0.10.192` | `switch-24b` | `A` | `True` | `2a0d3e28-d3bf-40cc-8a99-df8b434bcf2d` |
| 58 | `10.0.10.192` | `switch-24b.boylenet.themfboyles.com` | `A` | `True` | `ae39e7c2-3d4f-4bc9-a9d8-eb8214aa0653` |
| 59 | `10.0.10.192` | `usl24b` | `A` | `True` | `c299b9cf-6c9d-4ad6-8e44-3e2d7f527def` |
| 60 | `10.0.10.192` | `usl24b.boylenet.themfboyles.com` | `A` | `True` | `8f72ae56-1e9d-4050-98d7-dd65ee60b498` |
| 61 | `10.0.10.221` | `ap-u7pro` | `A` | `True` | `642c563a-0771-4bec-b741-e04e1b9f08ae` |
| 62 | `10.0.10.221` | `ap-u7pro.boylenet.themfboyles.com` | `A` | `True` | `75684451-23d9-4b17-ba7f-50864e162edd` |
| 63 | `10.0.10.221` | `u7pro` | `A` | `True` | `b11c5a40-238e-4e65-9284-c88dab4de1a0` |
| 64 | `10.0.10.221` | `u7pro.boylenet.themfboyles.com` | `A` | `True` | `176303e9-4999-4cd3-8ee8-dab4450405b9` |
| 65 | `10.0.10.224` | `box` | `A` | `True` | `4208d375-da1f-4296-a3b4-7e8eb3f437d3` |
| 66 | `10.0.10.224` | `box.boylenet.themfboyles.com` | `A` | `True` | `c4efbbc6-b346-45ef-b848-20e2d012f60c` |
| 67 | `10.0.10.252` | `ilom-252` | `A` | `True` | `ebc7eadb-155f-4936-ac93-caab1825c078` |
| 68 | `10.0.10.252` | `ilom-252.boylenet.themfboyles.com` | `A` | `True` | `f0ee6520-017f-428c-85fb-1965a91850b3` |
| 69 | `10.0.20.41` | `esxi01` | `A` | `True` | `66dae7c5-494d-4e9a-bf5c-35346209bd8b` |
| 70 | `10.0.20.41` | `esxi01.boylenet.themfboyles.com` | `A` | `True` | `42aca241-f6a0-4e63-a5c9-dc423114ba55` |
| 71 | `10.0.20.42` | `esxi02` | `A` | `True` | `102054d9-2c86-495a-bcba-24fbbc5a9cca` |
| 72 | `10.0.20.42` | `esxi02.boylenet.themfboyles.com` | `A` | `True` | `5e3ed44e-61b8-48cf-9ae6-38a4198e8621` |
| 73 | `10.0.20.43` | `esxi03` | `A` | `True` | `d2f95d82-41a1-415b-a882-f078745e69a9` |
| 74 | `10.0.20.43` | `esxi03.boylenet.themfboyles.com` | `A` | `True` | `cad46316-76d5-4329-ad71-7fe0d02b8c8d` |
| 75 | `10.0.20.44` | `esxi04` | `A` | `True` | `2988bc55-cac3-4db5-b2ff-00f3a2b2926e` |
| 76 | `10.0.20.44` | `esxi04.boylenet.themfboyles.com` | `A` | `True` | `b5238592-dc1c-4fd0-9ebd-0bc137db6d8a` |
| 77 | `10.0.20.70` | `vc04` | `A` | `True` | `90fcf0fd-cf5a-41a0-83e5-fd27a0d1e9ce` |
| 78 | `10.0.20.70` | `vc04.boylenet.themfboyles.com` | `A` | `True` | `87d62969-0c50-459d-9212-fce817aafc0c` |
| 79 | `10.0.20.70` | `vcenter` | `A` | `True` | `5bade1d4-450f-40c3-a049-21908acf3763` |
| 80 | `10.0.20.70` | `vcenter.boylenet.themfboyles.com` | `A` | `True` | `4963c80d-ab28-42ee-96f9-bb6b9622dcb1` |
| 81 | `10.0.20.70` | `vsphere` | `A` | `True` | `34dc972f-6f01-474d-9314-6b64a2ffad82` |
| 82 | `10.0.20.70` | `vsphere.boylenet.themfboyles.com` | `A` | `True` | `dee00957-cb00-483c-b2da-a16480fa6e03` |
| 83 | `10.0.20.220` | `esxi-mgmt-220` | `A` | `True` | `26317cfb-f835-49c2-b8c6-0d88b5e97b68` |
| 84 | `10.0.20.220` | `esxi-mgmt-220.boylenet.themfboyles.com` | `A` | `True` | `c3ec8125-5366-4c18-abd5-5620ccf569bd` |
| 85 | `10.0.30.4` | `vc04-peer` | `A` | `True` | `5bf430b4-c393-4366-b791-ddbd34af68ea` |
| 86 | `10.0.30.4` | `vc04-peer.boylenet.themfboyles.com` | `A` | `True` | `843930f8-9890-48be-b273-604b76711bb6` |
| 87 | `10.0.30.4` | `vcha-peer` | `A` | `True` | `ee223caa-e54a-47dc-b0e6-44175da53330` |
| 88 | `10.0.30.4` | `vcha-peer.boylenet.themfboyles.com` | `A` | `True` | `abc52802-3382-4c4b-8dbd-761d06a3e5fe` |
| 89 | `10.0.30.5` | `vc04-witness` | `A` | `True` | `9ace3960-8584-40c4-a27d-da25a5c9d5e1` |
| 90 | `10.0.30.5` | `vc04-witness.boylenet.themfboyles.com` | `A` | `True` | `ce06f134-5b26-4059-9ae4-86f05982849c` |
| 91 | `10.0.30.5` | `vcha-witness` | `A` | `True` | `71431a5f-71ee-4094-a367-07eae07bfc09` |
| 92 | `10.0.30.5` | `vcha-witness.boylenet.themfboyles.com` | `A` | `True` | `d022e84d-d4da-4eb6-acce-a6b31b383762` |
| 93 | `10.0.30.13` | `vc04-vcha` | `A` | `True` | `ea10c930-0698-477d-8b63-e0d87cced03c` |
| 94 | `10.0.30.13` | `vc04-vcha.boylenet.themfboyles.com` | `A` | `True` | `c8db640b-119a-4ae3-a68d-4311530b73a9` |
| 95 | `10.0.30.13` | `vcha-primary` | `A` | `True` | `c9ed352e-d6da-45d5-826b-be3b5da79f8a` |
| 96 | `10.0.30.13` | `vcha-primary.boylenet.themfboyles.com` | `A` | `True` | `f00b94f9-c2c8-4bdb-8065-5ce1dd5ccb3e` |
| 97 | `10.0.40.21` | `esxi01-ft` | `A` | `True` | `a0153351-8682-44cb-893a-ba2b40f7301d` |
| 98 | `10.0.40.21` | `esxi01-ft.boylenet.themfboyles.com` | `A` | `True` | `7bb0cde1-87ad-43bc-9c78-b52595d831cc` |
| 99 | `10.0.40.22` | `esxi02-ft` | `A` | `True` | `58c6f79b-e8e1-4f28-9a05-3676c52302b6` |
| 100 | `10.0.40.22` | `esxi02-ft.boylenet.themfboyles.com` | `A` | `True` | `179d8d10-d578-4046-a571-836464746a2b` |
| 101 | `10.0.40.23` | `esxi03-ft` | `A` | `True` | `246ea4c6-78dd-434f-811d-76ba0f36f9d5` |
| 102 | `10.0.40.23` | `esxi03-ft.boylenet.themfboyles.com` | `A` | `True` | `0869f329-ad8d-4842-b5fc-a47febb5ee3d` |
| 103 | `10.0.40.24` | `esxi04-ft` | `A` | `True` | `c756d0cd-7cce-40c7-85f8-ab03af561b20` |
| 104 | `10.0.40.24` | `esxi04-ft.boylenet.themfboyles.com` | `A` | `True` | `3731b48a-3fe7-448f-89be-f9d9563081ea` |
| 105 | `10.0.50.21` | `esxi01-vmotion` | `A` | `True` | `16c15997-3db3-47ae-92e0-c47bce26ed82` |
| 106 | `10.0.50.21` | `esxi01-vmotion.boylenet.themfboyles.com` | `A` | `True` | `420cc2da-5ffa-4d57-8ef8-07ff1cb6f849` |
| 107 | `10.0.50.22` | `esxi02-vmotion` | `A` | `True` | `b56beafc-f07d-47c7-8184-2026116f6f87` |
| 108 | `10.0.50.22` | `esxi02-vmotion.boylenet.themfboyles.com` | `A` | `True` | `f43b34f5-b5fe-470b-8a2a-21c9507e1d38` |
| 109 | `10.0.50.23` | `esxi03-vmotion` | `A` | `True` | `f03530a5-f428-4a44-b689-a23bbc79d9d5` |
| 110 | `10.0.50.23` | `esxi03-vmotion.boylenet.themfboyles.com` | `A` | `True` | `586621a7-a955-481c-a5b3-d50b1f627ed7` |
| 111 | `10.0.50.24` | `esxi04-vmotion` | `A` | `True` | `78cfcb7a-d00e-4c7b-8e77-5409ef76cb06` |
| 112 | `10.0.50.24` | `esxi04-vmotion.boylenet.themfboyles.com` | `A` | `True` | `b8e872b2-c2d8-42fb-8c8b-9fbc94500cfd` |
| 113 | `10.0.60.21` | `esxi01-vsan` | `A` | `True` | `e60fce12-e920-4365-8b71-ec91c687b733` |
| 114 | `10.0.60.21` | `esxi01-vsan.boylenet.themfboyles.com` | `A` | `True` | `341111d9-801a-4232-a726-4efa3f923668` |
| 115 | `10.0.60.22` | `esxi02-vsan` | `A` | `True` | `037bb7c5-32a6-40f3-9d2b-b43aa8894c26` |
| 116 | `10.0.60.22` | `esxi02-vsan.boylenet.themfboyles.com` | `A` | `True` | `d2e78baf-0577-4cfa-986c-4d4a9bc8784a` |
| 117 | `10.0.60.23` | `esxi03-vsan` | `A` | `True` | `7cfdb8f2-9456-40f0-8281-bc54c9817536` |
| 118 | `10.0.60.23` | `esxi03-vsan.boylenet.themfboyles.com` | `A` | `True` | `0f3cb5be-eb2d-4827-839b-e2fef3a47eef` |
| 119 | `10.0.60.24` | `esxi04-vsan` | `A` | `True` | `c4d0d1cc-0dc5-40bd-9514-386c1dc066e0` |
| 120 | `10.0.60.24` | `esxi04-vsan.boylenet.themfboyles.com` | `A` | `True` | `a4ebb11a-4295-430d-ab89-18d520f007bd` |
| 121 | `10.0.70.1` | `gateway` | `A` | `True` | `5d8da104-5a2d-4811-9f09-6b7320d98564` |
| 122 | `10.0.70.1` | `gateway.boylenet.themfboyles.com` | `A` | `True` | `51010363-2d98-4609-bee5-0c221547daa6` |
| 123 | `10.0.70.1` | `udmpro` | `A` | `True` | `9a0d6bd3-e974-48b3-96fb-3b8f43004a39` |
| 124 | `10.0.70.1` | `udmpro.boylenet.themfboyles.com` | `A` | `True` | `ec8ea66e-ecc7-43e4-b5cb-8b6defec9a73` |
| 125 | `10.0.70.1` | `unifi` | `A` | `True` | `a1fcb3a9-2d53-4ca5-8050-c1f260576ad8` |
| 126 | `10.0.70.1` | `unifi-gateway` | `A` | `True` | `22c4c485-8443-47d2-87cf-f5638cf2135b` |
| 127 | `10.0.70.1` | `unifi-gateway.boylenet.themfboyles.com` | `A` | `True` | `216ef73c-f3b8-4af3-8506-4261ed651ed0` |
| 128 | `10.0.70.1` | `unifi.boylenet.themfboyles.com` | `A` | `True` | `77abd342-582a-45ae-9892-d7fa01ab5fc2` |
| 129 | `10.0.70.20` | `pump` | `A` | `True` | `167bc41d-2406-4509-9a5d-8bcd7afe1981` |
| 130 | `10.0.70.20` | `pump.boylenet.themfboyles.com` | `A` | `True` | `93ceaf91-11cf-4d71-9e48-9eec55329918` |
| 131 | `10.0.70.20` | `pump.lan` | `A` | `True` | `f5f9f48c-caa1-4522-905e-0d4a2a5dbf21` |
| 132 | `10.0.70.20` | `switch-20` | `A` | `True` | `5ed4d90b-f966-4206-87fb-6715546952d7` |
| 133 | `10.0.70.20` | `switch-20.boylenet.themfboyles.com` | `A` | `True` | `e93c5ee3-9f76-42ad-8ee2-0c5079fb4e48` |
| 134 | `10.0.70.25` | `mcp` | `A` | `True` | `c612e922-f110-4a48-ae1c-9a70faab1db9` |
| 135 | `10.0.70.25` | `mcp.boylenet.themfboyles.com` | `A` | `True` | `1597a245-c01e-4b4c-bd06-74140a67e0ce` |
| 136 | `10.0.70.25` | `ops-mcp01` | `A` | `True` | `78e02d98-76b2-494c-b2e4-59a68b551ba2` |
| 137 | `10.0.70.25` | `ops-mcp01.boylenet.themfboyles.com` | `A` | `True` | `0a3612ac-f554-4e22-b3f6-15078de4064f` |
| 138 | `10.0.70.25` | `switch-25` | `A` | `True` | `94c7c403-1b8e-4ff4-bdfb-6ab5a2c39d7a` |
| 139 | `10.0.70.25` | `switch-25.boylenet.themfboyles.com` | `A` | `True` | `5cfc586f-e80f-415e-b44e-af97550e321c` |
| 140 | `10.0.70.31` | `fitness` | `A` | `True` | `f15daa8a-03c9-45e8-bdaa-53523a5fbf3d` |
| 141 | `10.0.70.31` | `fitness.boylenet.themfboyles.com` | `A` | `True` | `d5ec87cc-129c-40d0-8509-beb7c4ea1604` |
| 142 | `10.0.70.31` | `Fitness.themfboyles.com` | `A` | `True` | `a47e7175-c11d-4480-9385-f8ea11aedc0c` |
| 143 | `10.0.70.31` | `infra-fresh0` | `A` | `True` | `7c4eb47b-9a18-4d74-9469-05e67519b8bc` |
| 144 | `10.0.70.31` | `infra-fresh0.boylenet.themfboyles.com` | `A` | `True` | `ab162414-cdab-4e13-a108-8ad027c29b49` |
| 145 | `10.0.70.35` | `bd-seat-c1` | `A` | `True` | `f0131ccb-16d8-4f29-b25e-d663af06b1e1` |
| 146 | `10.0.70.35` | `bd-seat-c1.boylenet.themfboyles.com` | `A` | `True` | `2b2610b1-6396-4d98-bbee-968f0c7981af` |
| 147 | `10.0.70.35` | `seatc1` | `A` | `True` | `e5511ffc-e839-478f-b700-77eb5ba3b59f` |
| 148 | `10.0.70.35` | `seatc1.boylenet.themfboyles.com` | `A` | `True` | `66f802e9-4988-4f02-a554-1a4d56be1fc1` |
| 149 | `10.0.70.50` | `bd` | `A` | `True` | `fca50459-f142-4acf-94f1-7325d25373d3` |
| 150 | `10.0.70.50` | `bd.boylenet.themfboyles.com` | `A` | `True` | `dfd18305-1ea9-4fcb-8c21-cf0a60a6d73d` |
| 151 | `10.0.70.50` | `wrk-bd01` | `A` | `True` | `19459933-a28a-41da-8141-54dc6b4ffb01` |
| 152 | `10.0.70.50` | `wrk-bd01.boylenet.themfboyles.com` | `A` | `True` | `365499c8-77df-4ebd-a027-bfab94c60fa2` |
| 153 | `10.0.70.51` | `bd1` | `A` | `True` | `2b843923-d08c-4c2d-8e59-051fc0d20d45` |
| 154 | `10.0.70.51` | `bd1.boylenet.themfboyles.com` | `A` | `True` | `5f8899c4-052b-473c-8023-9189bb1a6174` |
| 155 | `10.0.70.51` | `wrk-bd02` | `A` | `True` | `d412c7a4-4582-4c46-a398-524f98b65efe` |
| 156 | `10.0.70.51` | `wrk-bd02.boylenet.themfboyles.com` | `A` | `True` | `82f3adc4-c54b-4ca4-ac1c-66c02b99f9ae` |
| 157 | `10.0.70.52` | `bd2` | `A` | `True` | `ff0b98f2-1027-4582-9706-9750030a1333` |
| 158 | `10.0.70.52` | `bd2.boylenet.themfboyles.com` | `A` | `True` | `39e2ad13-3f65-4cbb-93ad-3671834109e4` |
| 159 | `10.0.70.52` | `wrk-bd03` | `A` | `True` | `cae992a1-5c18-473b-845a-0eb7400a9752` |
| 160 | `10.0.70.52` | `wrk-bd03.boylenet.themfboyles.com` | `A` | `True` | `91282c15-d9c0-4418-bb72-aff1b39a9166` |
| 161 | `10.0.70.53` | `bd3` | `A` | `True` | `ea213359-8b96-4585-b3e0-2ea17fd50b67` |
| 162 | `10.0.70.53` | `bd3.boylenet.themfboyles.com` | `A` | `True` | `c44240a6-44ad-4190-9351-c30deda94b69` |
| 163 | `10.0.70.53` | `wrk-bd04` | `A` | `True` | `7919a5a7-68a2-43b5-801b-9f69e86d34a5` |
| 164 | `10.0.70.53` | `wrk-bd04.boylenet.themfboyles.com` | `A` | `True` | `effbf3d3-68af-43ba-9b63-70d9af498bb5` |
| 165 | `10.0.70.54` | `bd4` | `A` | `True` | `b5010ad1-99b0-49d6-b62e-8ce41781c2c4` |
| 166 | `10.0.70.54` | `bd4.boylenet.themfboyles.com` | `A` | `True` | `9f1a1f8c-0996-4626-a8cf-7ee387da2106` |
| 167 | `10.0.70.54` | `wrk-bd05` | `A` | `True` | `fe22a5b8-4718-4524-a295-df6a9823669e` |
| 168 | `10.0.70.54` | `wrk-bd05.boylenet.themfboyles.com` | `A` | `True` | `19ed2795-5f1e-4fd6-a3f8-210300f6fd1b` |
| 169 | `10.0.70.72` | `ai` | `A` | `True` | `5658f47a-2005-4b3c-adc6-0942714c7765` |
| 170 | `10.0.70.72` | `ai-srv01` | `A` | `True` | `76e4b2e0-873a-4b9b-b78a-a9c6387a96eb` |
| 171 | `10.0.70.72` | `ai-srv01.boylenet.themfboyles.com` | `A` | `True` | `e9514b29-3cec-4fdd-92fa-e2d2b95c8648` |
| 172 | `10.0.70.72` | `ai-vault01` | `A` | `True` | `eb63fb77-0c79-4c28-b205-cc2a7e10ebcd` |
| 173 | `10.0.70.72` | `ai-vault01.boylenet.themfboyles.com` | `A` | `True` | `a4b8dec9-fa10-4c3c-82c3-80522824f449` |
| 174 | `10.0.70.72` | `ai-webui` | `A` | `True` | `2cdfb914-2b69-4558-8966-e94fcfbcd22a` |
| 175 | `10.0.70.72` | `ai-webui.boylenet.themfboyles.com` | `A` | `True` | `3c0834ab-b7a6-4f86-865e-80315b8e585f` |
| 176 | `10.0.70.72` | `ai.boylenet.themfboyles.com` | `A` | `True` | `d6858baf-b926-4fad-a4f9-5c1299a5a132` |
| 177 | `10.0.70.72` | `switch-72` | `A` | `True` | `75b3fb54-3cbb-4ca0-9f94-c47bf463e408` |
| 178 | `10.0.70.72` | `switch-72.boylenet.themfboyles.com` | `A` | `True` | `82870910-cbd4-4e0e-910c-17bc84e42cb2` |
| 179 | `10.0.70.72` | `vault-a` | `A` | `True` | `3985f7d7-4f1f-4fc5-9e4a-450027bf074e` |
| 180 | `10.0.70.72` | `vault-a.boylenet.themfboyles.com` | `A` | `True` | `3cb1399b-6d5c-432a-af82-0866adc0ac45` |
| 181 | `10.0.70.80` | `test3` | `A` | `True` | `25e0f9ce-6764-481c-a30b-d7ed426a8e09` |
| 182 | `10.0.70.80` | `test3.boylenet.themfboyles.com` | `A` | `True` | `e049a385-26ab-4a70-8875-b91f74f4548a` |
| 183 | `10.0.70.80` | `wrk-test03` | `A` | `True` | `7caaf300-e1ea-42af-abf6-af1b776fec88` |
| 184 | `10.0.70.80` | `wrk-test03.boylenet.themfboyles.com` | `A` | `True` | `71895714-4137-4201-9889-b5e26692cb89` |
| 185 | `10.0.70.81` | `spare4` | `A` | `True` | `80ec616a-4caa-4f23-b939-cc1252c1fa2b` |
| 186 | `10.0.70.81` | `spare4.boylenet.themfboyles.com` | `A` | `True` | `3fccd07e-c8a3-4d9f-abc9-a99d1eecc9f4` |
| 187 | `10.0.70.82` | `test3big` | `A` | `True` | `6e62ba8f-d893-4848-ada6-fcfd3d8c2de2` |
| 188 | `10.0.70.82` | `test3big.boylenet.themfboyles.com` | `A` | `True` | `5cf48d10-124c-4e0a-9362-604aa7e87069` |
| 189 | `10.0.70.82` | `wrk-test04` | `A` | `True` | `ec27e9e0-f2bf-4ba7-8038-2f630405ec24` |
| 190 | `10.0.70.82` | `wrk-test04.boylenet.themfboyles.com` | `A` | `True` | `ac42f804-2596-4255-b773-65ee59191370` |
| 191 | `10.0.70.83` | `ai-dev` | `A` | `True` | `f7184026-4f26-4750-a9e6-85b0e7e43c3a` |
| 192 | `10.0.70.83` | `ai-dev.boylenet.themfboyles.com` | `A` | `True` | `341ff928-32c1-42b0-825b-bcdaafea30f9` |
| 193 | `10.0.70.83` | `ai-vault02` | `A` | `True` | `de27e633-2397-45ea-a8b2-6343ccb3a5cc` |
| 194 | `10.0.70.83` | `ai-vault02.boylenet.themfboyles.com` | `A` | `True` | `f0b794e1-14b9-4529-a9e7-a110618a5e4c` |
| 195 | `10.0.70.83` | `aidev` | `A` | `True` | `eac31d40-a2cc-4c0e-8303-f5c1f823ddb0` |
| 196 | `10.0.70.83` | `aidev.boylenet.themfboyles.com` | `A` | `True` | `367e7d4f-dc98-449b-b199-f5bdea5f64ec` |
| 197 | `10.0.70.83` | `vault-b` | `A` | `True` | `e7dd6b29-a96a-43a1-a060-057c1ba41019` |
| 198 | `10.0.70.83` | `vault-b.boylenet.themfboyles.com` | `A` | `True` | `92230b44-7321-4f47-a3ca-3252865be5b5` |
| 199 | `10.0.70.84` | `test7` | `A` | `True` | `48359070-ef44-4fe8-9a70-8805e6776049` |
| 200 | `10.0.70.84` | `test7.boylenet.themfboyles.com` | `A` | `True` | `f1a5695e-40ba-43ed-a5ba-6c976f4529de` |
| 201 | `10.0.70.84` | `wrk-test07` | `A` | `True` | `207a070d-c19e-492b-acd6-bba030ea5b43` |
| 202 | `10.0.70.84` | `wrk-test07.boylenet.themfboyles.com` | `A` | `True` | `df4bf676-68c8-4235-8f8c-d9852fbd2fb0` |
| 203 | `10.0.70.85` | `test4` | `A` | `True` | `ffb74d72-7ee8-4404-983c-1571c0fa655b` |
| 204 | `10.0.70.85` | `test4.boylenet.themfboyles.com` | `A` | `True` | `90e3e827-8c06-405a-9efd-d8ab024771d0` |
| 205 | `10.0.70.85` | `wrk-test05` | `A` | `True` | `b8ae2552-c9cf-4a13-9f46-d70603813463` |
| 206 | `10.0.70.85` | `wrk-test05.boylenet.themfboyles.com` | `A` | `True` | `3495dc2d-6c9b-4da3-8d61-aab8a8783fa2` |
| 207 | `10.0.70.86` | `bd-ci-r2` | `A` | `True` | `664d6e1a-875b-41e5-8fd6-6c7107cc93d9` |
| 208 | `10.0.70.86` | `bd-ci-r2.boylenet.themfboyles.com` | `A` | `True` | `4f4144b7-2ee9-47a4-84a0-55d0fc66273b` |
| 209 | `10.0.70.86` | `cir2` | `A` | `True` | `0b7dca62-770a-4ae5-acf1-7f67c3e8184c` |
| 210 | `10.0.70.86` | `cir2.boylenet.themfboyles.com` | `A` | `True` | `9c5df815-06f0-4185-99a8-2f19e93dc45b` |
| 211 | `10.0.70.95` | `test2` | `A` | `True` | `fa8e3388-ba0e-477b-9227-1f656ff2e343` |
| 212 | `10.0.70.95` | `test2.boylenet.themfboyles.com` | `A` | `True` | `5411cb65-bf0b-4387-b56f-9df244303724` |
| 213 | `10.0.70.95` | `wrk-test02` | `A` | `True` | `927755a3-d918-4dd5-804b-848e6677b73c` |
| 214 | `10.0.70.95` | `wrk-test02.boylenet.themfboyles.com` | `A` | `True` | `72fac240-89e4-4b01-95af-65b4267d4f8a` |
| 215 | `10.0.70.107` | `infra-test` | `A` | `True` | `bc4cc97e-5c9e-4a11-bc61-e105aaf4cab4` |
| 216 | `10.0.70.107` | `infra-test.boylenet.themfboyles.com` | `A` | `True` | `5e57f0f5-81ad-445c-8c47-684c716f83a7` |
| 217 | `10.0.70.107` | `ops-test01` | `A` | `True` | `994e9fe9-5405-469e-afde-f84f1ab35507` |
| 218 | `10.0.70.107` | `ops-test01.boylenet.themfboyles.com` | `A` | `True` | `06aaf588-76b6-48f1-834d-2f33e91c2716` |
| 219 | `10.0.70.120` | `spare7` | `A` | `True` | `20a6fe48-74a5-40ff-bad9-ee37e2ef4b7f` |
| 220 | `10.0.70.120` | `spare7.boylenet.themfboyles.com` | `A` | `True` | `00e3eb16-6b8d-486c-b90d-238940d97e6c` |
| 221 | `10.0.70.120` | `spr-pool01` | `A` | `True` | `e73a2473-53a7-40cd-b37f-d01770481072` |
| 222 | `10.0.70.120` | `spr-pool01.boylenet.themfboyles.com` | `A` | `True` | `2f402ecd-3ccd-492f-b290-313e97198b73` |
| 223 | `10.0.70.121` | `bd-ci-r1` | `A` | `True` | `091eaf17-c086-41c0-90c4-36833fe271e9` |
| 224 | `10.0.70.121` | `bd-ci-r1.boylenet.themfboyles.com` | `A` | `True` | `5d932b2a-9817-43d3-b5c7-ff34c88de2ce` |
| 225 | `10.0.70.121` | `cir1` | `A` | `True` | `f8ff7349-4a09-4a42-8028-2ee41875fd36` |
| 226 | `10.0.70.121` | `cir1.boylenet.themfboyles.com` | `A` | `True` | `424f5209-b106-4fb2-8901-946588d1d58d` |
| 227 | `10.0.70.122` | `spare3` | `A` | `True` | `6f4e8a6b-1996-4f8c-b93b-a6a36614833d` |
| 228 | `10.0.70.122` | `spare3.boylenet.themfboyles.com` | `A` | `True` | `3d4f9e3e-c3dc-4e1e-9edd-fdebfe250c6b` |
| 229 | `10.0.70.125` | `ai-infer01` | `A` | `True` | `6540b62d-a8c2-41b0-a457-d9f6904129b9` |
| 230 | `10.0.70.125` | `ai-infer01.boylenet.themfboyles.com` | `A` | `True` | `f88a3b3b-43f0-4364-895c-09287a397927` |
| 231 | `10.0.70.125` | `aiinference` | `A` | `True` | `73262462-03dc-4124-8fd3-0d634a137808` |
| 232 | `10.0.70.125` | `aiinference.boylenet.themfboyles.com` | `A` | `True` | `4f6be0e3-073e-421c-b951-d5978f823c8c` |
| 233 | `10.0.70.125` | `qdrant` | `A` | `True` | `73a5d437-8c37-431c-ac77-12fa1bc2ac79` |
| 234 | `10.0.70.125` | `qdrant.boylenet.themfboyles.com` | `A` | `True` | `abeb71e9-c61d-410d-96bc-ed06ea3c189a` |
| 235 | `10.0.70.149` | `fresh149` | `A` | `True` | `cafac907-fe7b-4b00-aeb6-8c726af8f461` |
| 236 | `10.0.70.149` | `fresh149.boylenet.themfboyles.com` | `A` | `True` | `3dbfcf15-b9b3-493f-b3a6-07c09b239960` |
| 237 | `10.0.70.162` | `cicd` | `A` | `True` | `1ae1a844-2d94-4244-94c8-e1f654490fc1` |
| 238 | `10.0.70.162` | `cicd.boylenet.themfboyles.com` | `A` | `True` | `6206ba27-61ed-4865-8daa-ef947492b1f3` |
| 239 | `10.0.70.162` | `ops-cicd01` | `A` | `True` | `4c5b0892-0ec7-475f-857b-dd0eceb86533` |
| 240 | `10.0.70.162` | `ops-cicd01.boylenet.themfboyles.com` | `A` | `True` | `590143c0-ff0e-4fcb-ab33-a41c1f078294` |
| 241 | `10.0.70.164` | `acng` | `A` | `True` | `2617d9bc-f953-40dc-8113-81fc25c0acf2` |
| 242 | `10.0.70.164` | `acng.boylenet.themfboyles.com` | `A` | `True` | `5dc7d342-5bdd-43a0-af8f-55ae1bf6969a` |
| 243 | `10.0.70.164` | `alertmanager` | `A` | `True` | `09f393a7-d0a6-4ab2-8e25-d1a925f9a3dd` |
| 244 | `10.0.70.164` | `alertmanager.boylenet.themfboyles.com` | `A` | `True` | `1b48ff3d-74ae-43a1-8928-30a1554e58d8` |
| 245 | `10.0.70.164` | `apt` | `A` | `True` | `48369c55-d9d9-489f-b5af-0bd1e149d8b9` |
| 246 | `10.0.70.164` | `apt.boylenet.themfboyles.com` | `A` | `True` | `35ad22e8-51e0-4ab5-936b-368a77b9585f` |
| 247 | `10.0.70.164` | `ast` | `A` | `True` | `ece8628a-d297-444c-87be-d5e98aa8e0bc` |
| 248 | `10.0.70.164` | `ast.boylenet.themfboyles.com` | `A` | `True` | `73793abd-87be-4052-bda7-b95aa6dc4dd2` |
| 249 | `10.0.70.164` | `cadvisor` | `A` | `True` | `7001869d-e7e1-4e30-9822-419199d4bd40` |
| 250 | `10.0.70.164` | `cadvisor.boylenet.themfboyles.com` | `A` | `True` | `2f2ccd7f-65c6-411b-b365-5b326c2318b9` |
| 251 | `10.0.70.164` | `chat` | `A` | `True` | `08255177-2494-42a4-a7e4-bec2af8a88a8` |
| 252 | `10.0.70.164` | `chat.boylenet.themfboyles.com` | `A` | `True` | `71eee0bf-1608-49c1-9f47-1286d9cf5503` |
| 253 | `10.0.70.164` | `cockpit` | `A` | `True` | `9473d760-b8ce-47d1-bd6d-fcd2ec017359` |
| 254 | `10.0.70.164` | `cockpit.boylenet.themfboyles.com` | `A` | `True` | `1e502efc-0770-4a43-b5ee-e88792351646` |
| 255 | `10.0.70.164` | `codeserver` | `A` | `True` | `6a51dd9c-9aed-443d-93aa-d7357138066d` |
| 256 | `10.0.70.164` | `codeserver.boylenet.themfboyles.com` | `A` | `True` | `d3313b03-dfc9-4dac-bf72-4a8510ed6e36` |
| 257 | `10.0.70.164` | `controller` | `A` | `True` | `3049a2f3-9ecd-4fd9-995b-253f547aeb0c` |
| 258 | `10.0.70.164` | `controller.boylenet.themfboyles.com` | `A` | `True` | `6ff80682-e7d7-4fe9-856a-b3e1137db5ae` |
| 259 | `10.0.70.164` | `dashboard` | `A` | `True` | `a2ca3187-45b7-45da-8201-df1b076e0437` |
| 260 | `10.0.70.164` | `dashboard.boylenet.themfboyles.com` | `A` | `True` | `19f3ae24-d68f-403d-b8f5-7edf8e715ac3` |
| 261 | `10.0.70.164` | `dashy` | `A` | `True` | `b03b0269-f01a-4b13-9817-568cb0c73738` |
| 262 | `10.0.70.164` | `dashy.boylenet.themfboyles.com` | `A` | `True` | `2d51eb18-c337-411f-8db3-a3b6dfecc0d8` |
| 263 | `10.0.70.164` | `devdocs` | `A` | `True` | `4c20d7b4-88de-4289-bd32-6e7a1cd9e338` |
| 264 | `10.0.70.164` | `devdocs.boylenet.themfboyles.com` | `A` | `True` | `dd6f2179-acd5-4bc2-a6fb-97b421ccfa02` |
| 265 | `10.0.70.164` | `devpi` | `A` | `True` | `cc3e16fa-34c9-457b-836c-8b21b1f384b1` |
| 266 | `10.0.70.164` | `devpi.boylenet.themfboyles.com` | `A` | `True` | `ec703362-9981-46e5-ac86-a6349458b7fd` |
| 267 | `10.0.70.164` | `elastic` | `A` | `True` | `2b34096b-71f4-46ad-b560-3c92cfd554a5` |
| 268 | `10.0.70.164` | `elastic.boylenet.themfboyles.com` | `A` | `True` | `06191ccf-257a-4531-a794-6f8d0775a59a` |
| 269 | `10.0.70.164` | `git` | `A` | `True` | `e7f6971a-32dd-4663-966c-02e375ca610e` |
| 270 | `10.0.70.164` | `git.boylenet.themfboyles.com` | `A` | `True` | `0cc183a0-e2f4-4ea0-a494-44838e156d73` |
| 271 | `10.0.70.164` | `gitea` | `A` | `True` | `c5ff21c0-a982-4862-a849-0b0f690c0f51` |
| 272 | `10.0.70.164` | `gitea.boylenet.themfboyles.com` | `A` | `True` | `9fdef3ef-0a79-4fd1-af86-2fcba74515be` |
| 273 | `10.0.70.164` | `grafana` | `A` | `True` | `23926320-1f55-473a-a15b-4c76bbbe2399` |
| 274 | `10.0.70.164` | `grafana.boylenet.themfboyles.com` | `A` | `True` | `47e34845-edb2-49d7-8779-3a821892301b` |
| 275 | `10.0.70.164` | `hub-mesh01` | `A` | `True` | `22223e0a-c48c-48ba-96cc-680d6d522374` |
| 276 | `10.0.70.164` | `hub-mesh01.boylenet.themfboyles.com` | `A` | `True` | `90d8a9f9-727b-4783-a706-d1fc2f7f676e` |
| 277 | `10.0.70.164` | `ide` | `A` | `True` | `631caac0-3d00-43f9-bb83-7a7849377bfe` |
| 278 | `10.0.70.164` | `ide.boylenet.themfboyles.com` | `A` | `True` | `0ac02a40-0974-4393-8c7a-3efbeb8556a8` |
| 279 | `10.0.70.164` | `jarvis` | `A` | `True` | `94350f72-ed28-487a-9864-e33ef2f586ba` |
| 280 | `10.0.70.164` | `jarvis.boylenet.themfboyles.com` | `A` | `True` | `2cb0cfa8-2fd2-42d6-8ccf-6d3de1da6d7f` |
| 281 | `10.0.70.164` | `litellm` | `A` | `True` | `c99985d2-2739-4a31-9505-44b9794f662e` |
| 282 | `10.0.70.164` | `litellm.boylenet.themfboyles.com` | `A` | `True` | `0a07c837-8c7c-4f7e-86a9-42465b82f196` |
| 283 | `10.0.70.164` | `minio` | `A` | `True` | `79755f1a-4542-494f-b9a5-4e5b411b9a88` |
| 284 | `10.0.70.164` | `minio.boylenet.themfboyles.com` | `A` | `True` | `b5a7c94e-6811-41ae-867b-ea57da75d57e` |
| 285 | `10.0.70.164` | `novnc` | `A` | `True` | `2076e078-80ae-42af-82a6-9b03cbdba047` |
| 286 | `10.0.70.164` | `novnc.boylenet.themfboyles.com` | `A` | `True` | `b76fef33-15f2-4615-a474-81316e7d8533` |
| 287 | `10.0.70.164` | `npm` | `A` | `True` | `8a819a2c-2c43-436d-a7e5-6fa5c1ac3e21` |
| 288 | `10.0.70.164` | `npm.boylenet.themfboyles.com` | `A` | `True` | `aed31f7a-d9a1-48ff-a197-9f1059f5bb73` |
| 289 | `10.0.70.164` | `openwebui` | `A` | `True` | `710c3fac-60dc-4025-99ed-f03eb9f1bf74` |
| 290 | `10.0.70.164` | `openwebui.boylenet.themfboyles.com` | `A` | `True` | `5fe6fdb6-8c87-444b-8320-9200e1125f94` |
| 291 | `10.0.70.164` | `orchestrator` | `A` | `True` | `57d7e76d-2087-4322-85b5-79333f4c494a` |
| 292 | `10.0.70.164` | `orchestrator.boylenet.themfboyles.com` | `A` | `True` | `b0f41a4b-2446-4fa3-8a65-fa1ee7cedb91` |
| 293 | `10.0.70.164` | `portal` | `A` | `True` | `5096d24f-6d0e-45f4-8fa5-aac0d0efae3c` |
| 294 | `10.0.70.164` | `portal.boylenet.themfboyles.com` | `A` | `True` | `622b21d6-df6e-43a3-8e01-6141a6fca6b1` |
| 295 | `10.0.70.164` | `prometheus` | `A` | `True` | `e486f1a0-7d7c-4955-b03c-af2ecf9cdb8d` |
| 296 | `10.0.70.164` | `prometheus.boylenet.themfboyles.com` | `A` | `True` | `a89c2321-f126-4fb1-8077-4f1ff1a8abfb` |
| 297 | `10.0.70.164` | `pypi` | `A` | `True` | `313b751b-4d1b-46b1-89a6-abcd82a4b5f1` |
| 298 | `10.0.70.164` | `pypi.boylenet.themfboyles.com` | `A` | `True` | `c0d35498-2388-4237-88d6-260f1847f7a2` |
| 299 | `10.0.70.164` | `registry` | `A` | `True` | `b1796f12-cb4a-4343-ab98-6fb83f251cff` |
| 300 | `10.0.70.164` | `registry.boylenet.themfboyles.com` | `A` | `True` | `33ebbc85-274b-4ce3-b66d-573b16fa08af` |
| 301 | `10.0.70.164` | `search` | `A` | `True` | `e29157c2-3524-4458-82cf-ee56c221cfc6` |
| 302 | `10.0.70.164` | `search.boylenet.themfboyles.com` | `A` | `True` | `bdb4cb16-14fb-4094-b8ae-2e17fb90b54a` |
| 303 | `10.0.70.164` | `searxng` | `A` | `True` | `bcaecc6d-d57a-4f41-9b3f-9a5f1edca4ff` |
| 304 | `10.0.70.164` | `searxng.boylenet.themfboyles.com` | `A` | `True` | `916d6c3d-685f-46e4-93e2-4989921948c6` |
| 305 | `10.0.70.164` | `test5` | `A` | `True` | `1b020957-c075-477a-975b-66ca00982fc0` |
| 306 | `10.0.70.164` | `test5.boylenet.themfboyles.com` | `A` | `True` | `b4a8bc36-3073-4e79-871d-3fd4abe2f138` |
| 307 | `10.0.70.164` | `traefik` | `A` | `True` | `df1b9bf9-9854-4301-9a53-692a28ab8cdb` |
| 308 | `10.0.70.164` | `traefik.boylenet.themfboyles.com` | `A` | `True` | `2dc3ab4d-b837-4487-830f-aa41f7bf12f3` |
| 309 | `10.0.70.164` | `treesitter` | `A` | `True` | `f31dccfc-5f6f-46f8-aa91-d2d9e55fe37e` |
| 310 | `10.0.70.164` | `treesitter.boylenet.themfboyles.com` | `A` | `True` | `aa273128-6e85-4865-8628-d79ee113f485` |
| 311 | `10.0.70.164` | `verdaccio` | `A` | `True` | `6705c3fc-2005-4762-bfae-d1540f91b269` |
| 312 | `10.0.70.164` | `verdaccio.boylenet.themfboyles.com` | `A` | `True` | `d485fdd0-1ab8-4d0a-bcbd-8a5d09ce9a05` |
| 313 | `10.0.70.171` | `bd-seat-c2` | `A` | `True` | `157d8429-d15d-414e-8b43-cf755330129f` |
| 314 | `10.0.70.171` | `bd-seat-c2.boylenet.themfboyles.com` | `A` | `True` | `99108a37-cad0-47ea-b942-59ca1343eca6` |
| 315 | `10.0.70.171` | `seatc2` | `A` | `True` | `f12de24b-657a-4e08-bccd-0e1331b7cd83` |
| 316 | `10.0.70.171` | `seatc2.boylenet.themfboyles.com` | `A` | `True` | `f9bbd4c9-542e-4c35-8a21-5bd7ef9990ea` |
| 317 | `10.0.70.181` | `bittorrent` | `A` | `True` | `b280c8b0-1ed0-401f-a8dc-d76141ff8857` |
| 318 | `10.0.70.181` | `bittorrent.boylenet.themfboyles.com` | `A` | `True` | `2b99a095-a263-4ea0-aa0c-176f9b7a0f37` |
| 319 | `10.0.70.181` | `oracle-bt` | `A` | `True` | `63aec958-10f0-45ca-82d8-ec953762c773` |
| 320 | `10.0.70.181` | `oracle-bt.boylenet.themfboyles.com` | `A` | `True` | `3f513e9e-cb36-4ed7-aa24-f669583d1a18` |
| 321 | `10.0.70.182` | `cache` | `A` | `True` | `f6727a54-e8f4-4d80-b479-4272dca4dba8` |
| 322 | `10.0.70.182` | `cache.boylenet.themfboyles.com` | `A` | `True` | `f19f949f-6f79-46f4-b8ed-5cf92fff9560` |
| 323 | `10.0.70.182` | `stg-cache01` | `A` | `True` | `c5d4dc10-a477-4a2a-832c-68b796009e57` |
| 324 | `10.0.70.182` | `stg-cache01.boylenet.themfboyles.com` | `A` | `True` | `fddae052-db58-4ba3-bbd1-597a903375b1` |
| 325 | `10.0.70.183` | `spare12` | `A` | `True` | `e8bab929-ef11-48dc-b4fd-e00ebcbf3c45` |
| 326 | `10.0.70.183` | `spare12.boylenet.themfboyles.com` | `A` | `True` | `713c6329-c34a-423d-8c60-575d119c27fc` |
| 327 | `10.0.70.183` | `spr-pool02` | `A` | `True` | `5ae8a175-ec80-4260-91e6-a023dd04d48b` |
| 328 | `10.0.70.183` | `spr-pool02.boylenet.themfboyles.com` | `A` | `True` | `1975322e-63e5-40ea-9fad-5e2d1673dc8f` |
| 329 | `10.0.70.184` | `spare8` | `A` | `True` | `fefa91a5-a190-4ae9-ae89-3d152e68f70d` |
| 330 | `10.0.70.184` | `spare8.boylenet.themfboyles.com` | `A` | `True` | `d9f9ac6c-7574-4edd-aa3d-e1655d8c32f5` |
| 331 | `10.0.70.185` | `spare5` | `A` | `True` | `cf9d3eda-aa3d-4e2e-a76c-b0d5380abf0e` |
| 332 | `10.0.70.185` | `spare5.boylenet.themfboyles.com` | `A` | `True` | `ea029db8-1be3-426b-b4f6-bf7a6412a840` |
| 333 | `10.0.70.186` | `spare6` | `A` | `True` | `2cad5bad-ec12-410f-bda4-efc0a7f50346` |
| 334 | `10.0.70.186` | `spare6.boylenet.themfboyles.com` | `A` | `True` | `b8b93b93-f302-4f04-9a12-0c61d5034662` |
| 335 | `10.0.70.187` | `spare9` | `A` | `True` | `09ccffa7-5bd0-41f7-b169-a5bd2b0a1018` |
| 336 | `10.0.70.187` | `spare9.boylenet.themfboyles.com` | `A` | `True` | `98ede8a6-f52d-4fca-a70d-15fbd7956f3a` |
| 337 | `10.0.70.188` | `wrk-188` | `A` | `True` | `69bea90c-782e-43d3-97f4-232893d91bb8` |
| 338 | `10.0.70.188` | `wrk-188.boylenet.themfboyles.com` | `A` | `True` | `a2939a5a-593f-4b91-bc11-b3aa94c790c4` |
| 339 | `10.0.70.189` | `wrk-189` | `A` | `True` | `5fd32efa-ab3c-431e-81fb-78862f438b8f` |
| 340 | `10.0.70.189` | `wrk-189.boylenet.themfboyles.com` | `A` | `True` | `82322d24-a125-420f-877a-da6e10bb7b03` |
| 341 | `10.0.70.190` | `wrk-190` | `A` | `True` | `922214bf-18ac-4a92-b9b2-25e2d9568d57` |
| 342 | `10.0.70.190` | `wrk-190.boylenet.themfboyles.com` | `A` | `True` | `cbaa1de2-ea9d-4805-beeb-ada39770008c` |
| 343 | `10.0.70.191` | `wrk-191` | `A` | `True` | `a27a811e-186a-48fc-8884-062f577f9bac` |
| 344 | `10.0.70.191` | `wrk-191.boylenet.themfboyles.com` | `A` | `True` | `8bfe5ffe-dc2f-4532-bf3f-ae6245f109a4` |
| 345 | `10.0.70.193` | `spare2` | `A` | `True` | `b6429071-b09d-4005-a854-e0454ace540d` |
| 346 | `10.0.70.193` | `spare2.boylenet.themfboyles.com` | `A` | `True` | `f8f47d8b-879a-415e-8884-03133162d2d5` |
| 347 | `10.0.70.194` | `test1` | `A` | `True` | `b72b381e-2192-4109-bd4d-a69ed5b469c2` |
| 348 | `10.0.70.194` | `test1.boylenet.themfboyles.com` | `A` | `True` | `a001a4ba-1052-4693-abcd-7fd8060478cd` |
| 349 | `10.0.70.194` | `wrk-test01` | `A` | `True` | `15402e82-7f25-4886-a4d3-ceeb31f5ab63` |
| 350 | `10.0.70.194` | `wrk-test01.boylenet.themfboyles.com` | `A` | `True` | `bb3e5036-95d0-40e4-a3e5-bb788a639e91` |
| 351 | `10.0.70.195` | `spare11` | `A` | `True` | `c7602f5c-b61a-4fdb-ba5e-ed31076643ac` |
| 352 | `10.0.70.195` | `spare11.boylenet.themfboyles.com` | `A` | `True` | `bdab3f21-60f1-4643-aff3-2ce98faa6bbb` |
| 353 | `10.0.70.196` | `spare10` | `A` | `True` | `306ce856-7826-4fcd-b132-58a84d1823b5` |
| 354 | `10.0.70.196` | `spare10.boylenet.themfboyles.com` | `A` | `True` | `ff7ac08d-bda3-4c8e-82d6-d33d834b0d4f` |
| 355 | `10.0.70.197` | `spare1` | `A` | `True` | `d06f9575-b736-4c3c-8f15-3af017aa2c01` |
| 356 | `10.0.70.197` | `spare1.boylenet.themfboyles.com` | `A` | `True` | `c4b29051-b09c-4aa2-9ae4-2e3d34ff6fb9` |
| 357 | `10.0.70.198` | `kvcache-cx1` | `A` | `True` | `891afd23-7277-4bb9-9c9b-73de67db8700` |
| 358 | `10.0.70.198` | `kvcache-cx1.boylenet.themfboyles.com` | `A` | `True` | `72041cec-7d89-43c6-aece-15829c2c7b40` |
| 359 | `10.0.70.224` | `spare` | `A` | `True` | `97db3938-d2f0-4ce2-93b8-b1d5f5a451ca` |
| 360 | `10.0.70.224` | `spare.boylenet.themfboyles.com` | `A` | `True` | `14afd807-7023-4837-803c-3065e3a4f63b` |
| 361 | `10.0.70.228` | `ai-ollama01` | `A` | `True` | `90188492-634c-4dd2-84b3-e8bbde7def61` |
| 362 | `10.0.70.228` | `ai-ollama01.boylenet.themfboyles.com` | `A` | `True` | `7b0b44bd-1157-4ea9-85a3-59645f64b7aa` |
| 363 | `10.0.70.228` | `langfuse` | `A` | `True` | `99f464c0-2798-464b-b727-ac79aae913e1` |
| 364 | `10.0.70.228` | `langfuse.boylenet.themfboyles.com` | `A` | `True` | `c7cbe628-e712-4a33-a23d-d39bd120171b` |
| 365 | `10.0.70.228` | `llama` | `A` | `True` | `dd446e4e-9b19-4e38-a1f9-b593acffd673` |
| 366 | `10.0.70.228` | `llama.boylenet.themfboyles.com` | `A` | `True` | `0f29a1cb-e256-4a4e-85f8-21a9ca4851ff` |
| 367 | `10.0.70.228` | `ollama` | `A` | `True` | `3058deac-0ca9-4dee-b4f0-27b791f8713e` |
| 368 | `10.0.70.228` | `ollama.boylenet.themfboyles.com` | `A` | `True` | `320a71f6-bc35-4bad-98d5-67dc7bd74e22` |
| 369 | `10.0.70.249` | `test6` | `A` | `True` | `b134f9ed-132c-434f-baed-6a2a5f1e2da4` |
| 370 | `10.0.70.249` | `test6.boylenet.themfboyles.com` | `A` | `True` | `d709ecca-f866-4f38-aa3a-8986deab5178` |
| 371 | `10.0.70.249` | `wrk-test06` | `A` | `True` | `d48d3f4d-521b-49c0-9713-72ed8099b2ae` |
| 372 | `10.0.70.249` | `wrk-test06.boylenet.themfboyles.com` | `A` | `True` | `a11076b2-77b9-48bb-ac32-7f89c06ea0b2` |
| 373 | `10.0.75.11` | `aidev-roce` | `A` | `True` | `e2aba343-da58-4349-9313-89144869975b` |
| 374 | `10.0.75.11` | `aidev-roce.boylenet.themfboyles.com` | `A` | `True` | `87401fb6-b33a-4032-a394-aeb1cec0d2db` |
| 375 | `10.0.75.12` | `ai-roce` | `A` | `True` | `a33b60c7-e789-4af0-9c8e-fbf5c65ab1e1` |
| 376 | `10.0.75.12` | `ai-roce.boylenet.themfboyles.com` | `A` | `True` | `4aef851a-ff01-414d-bb9f-0ce49135799a` |
| 377 | `10.0.75.13` | `ai-infer01-roce` | `A` | `True` | `9c932070-097f-4054-a21a-76564842873c` |
| 378 | `10.0.75.13` | `ai-infer01-roce.boylenet.themfboyles.com` | `A` | `True` | `e5db3368-ba64-4573-ad47-22206649382c` |
| 379 | `10.0.75.14` | `ai-ollama01-roce` | `A` | `True` | `ed4ff0d0-b239-42d2-9796-d025070a8ed9` |
| 380 | `10.0.75.14` | `ai-ollama01-roce.boylenet.themfboyles.com` | `A` | `True` | `bb6a01b1-d904-42f5-be6e-b8eb321445d4` |
| 381 | `10.0.120.3` | `WS01` | `A` | `True` | `d5cff7e9-a04e-42f9-a3ea-c5d1fe8786f7` |
| 382 | `10.0.120.3` | `ws01.boylenet.themfboyles.com` | `A` | `True` | `eef6e2f0-bd21-4d5f-8533-de0f40bb22ce` |

---

## 3. Active UniFi Client Friendly Name Assignments (`PUT /api/s/default/rest/user/<_id>`)

| UniFi Client `_id` | Assigned Friendly Name |
| :--- | :--- |
| `799f4997-c5d5-4b2c-be4a-9a80b2a620db` | `AI (ai-vault01)` |
| `355af564-cffb-48f9-9ad0-472031815d2b` | `BD1 (wrk-bd02)` |
| `3f483ab2-12d5-444a-a78b-fa8e6db90127` | `BattleStation (Oracle GPU)` |
| `bdeaea99-00e0-4222-ae5f-48b9e0d45fe2` | `DESKTOP-CO8D5GV` |
| `70412f19-8d07-43a9-8590-cde646852d25` | `ESXi01` |
| `7e6a54ad-3587-4fe9-be45-b372bebee1ff` | `ESXi01 Fault Tolerance` |
| `6e34c187-deea-48fc-85e7-3625601e7bb5` | `ESXi01 iLO (ilom-10)` |
| `a9028f02-80ca-4272-9d85-9f62c50ca99b` | `ESXi01 vMotion` |
| `8712b096-895b-490c-8276-80748773e36f` | `ESXi01 vSAN` |
| `26c58928-69f3-4d5c-920a-b1133339cf36` | `ESXi02` |
| `e0b4a869-db6b-404f-8c33-56347cc06add` | `ESXi02 Fault Tolerance` |
| `0f44b4b5-e22a-4e50-a686-03f78ef37087` | `ESXi02 iLO (ilom-20)` |
| `ef2d7b6e-c51c-404a-9560-21026ec84307` | `ESXi02 vMotion` |
| `18502b16-96ec-4383-9a10-6484407007e9` | `ESXi02 vSAN` |
| `9f7952c9-afc2-47ad-bef6-5cba4427b2e4` | `ESXi03` |
| `145f3c13-635c-4f05-9221-e8d634429523` | `ESXi03 Fault Tolerance` |
| `2ad4b1a6-4223-4c6e-9ebc-9adc727c1fee` | `ESXi03 vMotion` |
| `9146acb8-8d93-463b-bb87-65aa3eeb554b` | `ESXi03 vSAN` |
| `a1ed0e69-e359-426d-a7fd-e0781bc370b1` | `ESXi04` |
| `a8860054-98fc-48e6-9507-ee98d1894d92` | `ESXi04 Fault Tolerance` |
| `6870b72b-c3b8-484d-b089-214f50dad0c3` | `ESXi04 vMotion` |
| `e4d7d0f1-edc6-46be-aa93-a5e152f4e12d` | `ESXi04 vSAN` |
| `0ba98409-ce51-4bfb-a724-1e769586178b` | `Front Door Camera` |
| `4dac2841-6dd1-4d56-a5c6-39f7e2f43628` | `Garage Camera` |
| `78ec14ea-82b5-4c53-9304-86ea1e760f43` | `Test1 (wrk-test01)` |
| `2abf7260-b7e8-4047-b41c-627691c5de5a` | `Test3Big (wrk-test04)` |
| `4577bd13-9039-4259-8318-1c4fc9d2bc88` | `VC04 VCHA Peer` |
| `4609ad00-07e0-4996-8368-da0368fda572` | `VC04 VCHA Primary` |
| `fd86515a-0111-4385-8a3b-adc7a181bde9` | `VC04 VCHA Witness` |
| `bc830406-5682-427b-931f-9a59cf126cf1` | `box (10.0.10.224)` |
| `39b7a0de-c659-484c-8d41-8be12fd89803` | `esxi-mgmt-220` |
| `d1ab6b5b-2c51-4559-a0ee-19c64826fcbe` | `ops-test01 (infra-test)` |
| `3525bb02-11fc-4dda-96f9-57d235202b7c` | `vCenter (VC04)` |

---

## 4. Live DNS Resolution Verification (`dig +short @10.0.70.1`)

```text
$ dig +short @10.0.70.1 dashy.boylenet.themfboyles.com             -> 10.0.70.164
$ dig +short @10.0.70.1 dashy                                      -> 10.0.70.164
$ dig +short @10.0.70.1 portal.boylenet.themfboyles.com            -> 10.0.70.164
$ dig +short @10.0.70.1 portal                                     -> 10.0.70.164
$ dig +short @10.0.70.1 dashboard.boylenet.themfboyles.com         -> 10.0.70.164
$ dig +short @10.0.70.1 jarvis.boylenet.themfboyles.com            -> 10.0.70.164
$ dig +short @10.0.70.1 jarvis                                     -> 10.0.70.164
$ dig +short @10.0.70.1 cockpit.boylenet.themfboyles.com           -> 10.0.70.164
$ dig +short @10.0.70.1 chat.boylenet.themfboyles.com              -> 10.0.70.164
$ dig +short @10.0.70.1 openwebui.boylenet.themfboyles.com         -> 10.0.70.164
$ dig +short @10.0.70.1 litellm.boylenet.themfboyles.com           -> 10.0.70.164
$ dig +short @10.0.70.1 orchestrator.boylenet.themfboyles.com      -> 10.0.70.164
$ dig +short @10.0.70.1 gitea.boylenet.themfboyles.com             -> 10.0.70.164
$ dig +short @10.0.70.1 git.boylenet.themfboyles.com               -> 10.0.70.164
$ dig +short @10.0.70.1 grafana.boylenet.themfboyles.com           -> 10.0.70.164
$ dig +short @10.0.70.1 prometheus.boylenet.themfboyles.com        -> 10.0.70.164
$ dig +short @10.0.70.1 alertmanager.boylenet.themfboyles.com      -> 10.0.70.164
$ dig +short @10.0.70.1 minio.boylenet.themfboyles.com             -> 10.0.70.164
$ dig +short @10.0.70.1 traefik.boylenet.themfboyles.com           -> 10.0.70.164
$ dig +short @10.0.70.1 cadvisor.boylenet.themfboyles.com          -> 10.0.70.164
$ dig +short @10.0.70.1 devdocs.boylenet.themfboyles.com           -> 10.0.70.164
$ dig +short @10.0.70.1 searxng.boylenet.themfboyles.com           -> 10.0.70.164
$ dig +short @10.0.70.1 search.boylenet.themfboyles.com            -> 10.0.70.164
$ dig +short @10.0.70.1 codeserver.boylenet.themfboyles.com        -> 10.0.70.164
$ dig +short @10.0.70.1 ide.boylenet.themfboyles.com               -> 10.0.70.164
$ dig +short @10.0.70.1 verdaccio.boylenet.themfboyles.com         -> 10.0.70.164
$ dig +short @10.0.70.1 npm.boylenet.themfboyles.com               -> 10.0.70.164
$ dig +short @10.0.70.1 devpi.boylenet.themfboyles.com             -> 10.0.70.164
$ dig +short @10.0.70.1 pypi.boylenet.themfboyles.com              -> 10.0.70.164
$ dig +short @10.0.70.1 acng.boylenet.themfboyles.com              -> 10.0.70.164
$ dig +short @10.0.70.1 apt.boylenet.themfboyles.com               -> 10.0.70.164
$ dig +short @10.0.70.1 elastic.boylenet.themfboyles.com           -> 10.0.70.164
$ dig +short @10.0.70.1 treesitter.boylenet.themfboyles.com        -> 10.0.70.164
$ dig +short @10.0.70.1 ast.boylenet.themfboyles.com               -> 10.0.70.164
$ dig +short @10.0.70.1 registry.boylenet.themfboyles.com          -> 10.0.70.164
$ dig +short @10.0.70.1 novnc.boylenet.themfboyles.com             -> 10.0.70.164
$ dig +short @10.0.70.1 ai.boylenet.themfboyles.com                -> 10.0.70.72
$ dig +short @10.0.70.1 ai                                         -> 10.0.70.72
$ dig +short @10.0.70.1 ai-vault01.boylenet.themfboyles.com        -> 10.0.70.72
$ dig +short @10.0.70.1 vault-a.boylenet.themfboyles.com           -> 10.0.70.72
$ dig +short @10.0.70.1 ai-webui.boylenet.themfboyles.com          -> 10.0.70.72
$ dig +short @10.0.70.1 aidev.boylenet.themfboyles.com             -> 10.0.70.83
$ dig +short @10.0.70.1 aidev                                      -> 10.0.70.83
$ dig +short @10.0.70.1 ai-dev.boylenet.themfboyles.com            -> 10.0.70.83
$ dig +short @10.0.70.1 ai-vault02.boylenet.themfboyles.com        -> 10.0.70.83
$ dig +short @10.0.70.1 vault-b.boylenet.themfboyles.com           -> 10.0.70.83
$ dig +short @10.0.70.1 ai-infer01.boylenet.themfboyles.com        -> 10.0.70.125
$ dig +short @10.0.70.1 qdrant.boylenet.themfboyles.com            -> 10.0.70.125
$ dig +short @10.0.70.1 ai-ollama01.boylenet.themfboyles.com       -> 10.0.70.228
$ dig +short @10.0.70.1 langfuse.boylenet.themfboyles.com          -> 10.0.70.228
$ dig +short @10.0.70.1 ollama.boylenet.themfboyles.com            -> 10.0.70.228
$ dig +short @10.0.70.1 aidev-roce.boylenet.themfboyles.com        -> 10.0.75.11
$ dig +short @10.0.70.1 ai-roce.boylenet.themfboyles.com           -> 10.0.75.12
$ dig +short @10.0.70.1 ai-infer01-roce.boylenet.themfboyles.com   -> 10.0.75.13
$ dig +short @10.0.70.1 ai-ollama01-roce.boylenet.themfboyles.com  -> 10.0.75.14
$ dig +short @10.0.70.1 unifi.boylenet.themfboyles.com             -> 10.0.70.1
$ dig +short @10.0.70.1 gateway.boylenet.themfboyles.com           -> 10.0.70.1
$ dig +short @10.0.70.1 udmpro.boylenet.themfboyles.com            -> 10.0.70.1
$ dig +short @10.0.70.1 usaggpro.boylenet.themfboyles.com          -> 10.0.10.111
$ dig +short @10.0.70.1 switch-agg.boylenet.themfboyles.com        -> 10.0.10.111
$ dig +short @10.0.70.1 usl8a.boylenet.themfboyles.com             -> 10.0.10.182
$ dig +short @10.0.70.1 switch-8a.boylenet.themfboyles.com         -> 10.0.10.182
$ dig +short @10.0.70.1 esxi01-ilo.boylenet.themfboyles.com        -> 10.0.10.10
$ dig +short @10.0.70.1 esxi02-ilo.boylenet.themfboyles.com        -> 10.0.10.20
$ dig +short @10.0.70.1 esxi03-ilo.boylenet.themfboyles.com        -> 10.0.10.30
$ dig +short @10.0.70.1 esxi04-ilo.boylenet.themfboyles.com        -> 10.0.10.40
$ dig +short @10.0.70.1 dc01.boylenet.themfboyles.com              -> 10.0.10.101
$ dig +short @10.0.70.1 battlestation.boylenet.themfboyles.com     -> 10.0.10.137
$ dig +short @10.0.70.1 oracle-bs.boylenet.themfboyles.com         -> 10.0.10.137
$ dig +short @10.0.70.1 ws01.boylenet.themfboyles.com              -> 10.0.120.3
$ dig +short @10.0.70.1 vc04.boylenet.themfboyles.com              -> 10.0.20.70
$ dig +short @10.0.70.1 vcenter.boylenet.themfboyles.com           -> 10.0.20.70
$ dig +short @10.0.70.1 vsphere.boylenet.themfboyles.com           -> 10.0.20.70
$ dig +short @10.0.70.1 esxi01.boylenet.themfboyles.com            -> 10.0.20.41
$ dig +short @10.0.70.1 esxi02.boylenet.themfboyles.com            -> 10.0.20.42
$ dig +short @10.0.70.1 esxi03.boylenet.themfboyles.com            -> 10.0.20.43
$ dig +short @10.0.70.1 esxi04.boylenet.themfboyles.com            -> 10.0.20.44
$ dig +short @10.0.70.1 esxi01-vsan.boylenet.themfboyles.com       -> 10.0.60.21
$ dig +short @10.0.70.1 esxi02-vsan.boylenet.themfboyles.com       -> 10.0.60.22
$ dig +short @10.0.70.1 esxi03-vsan.boylenet.themfboyles.com       -> 10.0.60.23
$ dig +short @10.0.70.1 esxi04-vsan.boylenet.themfboyles.com       -> 10.0.60.24
$ dig +short @10.0.70.1 test5.boylenet.themfboyles.com             -> 10.0.70.164
$ dig +short @10.0.70.1 bittorrent.boylenet.themfboyles.com        -> 10.0.70.181
$ dig +short @10.0.70.1 oracle-bt.boylenet.themfboyles.com         -> 10.0.70.181
```
