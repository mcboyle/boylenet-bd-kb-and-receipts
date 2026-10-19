# BD-KB Volume 38: Codex Sol (`gpt-5.6-sol`) Advisory — PM Model Selection (`claude-fable-5-1` vs `claude-opus-5-5 --advisor fable`) & Multi-Agent Q&A Council Briefing
> **Lead Investigator**: `Codex-Sol-QA-Readiness` (`codex (gpt-5.6-sol)`)
> **Subagent Squad**: 2x Parallel `AGY claude+gpt` (`claude-sonnet-5-5-high` & `claude-sonnet-5-5-low`) Read-Only Code Investigators
> **Generated At**: `2026-10-19T23:32:04Z`
> **Scope**: Strictly Read-Only Investigation Dossier for `bd-pm-B` (`Zero Code Changes`, `O3416` Compliant)

---

# BD-KB Volume 38: Codex Sol (`gpt-5.6-sol`) Advisory

## PM Model Selection (`claude-fable-5-1` vs. `claude-opus-5-5 --advisor fable`) and Multi-Agent Q&A Council Briefing

**Audience:** `bd-pm-B`  
**Investigation mode:** 100% read-only; no tools or commands used  
**Evidence cutoff:** Supplied investigator reports only  
**Date:** 2026-10-19  
**Overall confidence:** **Low to moderate**, because the source packet contains partial telemetry but no PM transcript metrics, prompt-cache measurements, BH1 row definitions, source excerpts, or complete BD-KB volume manifests.

---

## Executive conclusion

`bd-pm-B` should **remain on the existing warm `claude-opus-5-5 --effort high --advisor fable` session for now**.

This is a continuity and evidence-preservation recommendation, not a claim that Opus is intrinsically superior to Fable. The supplied reports contain no comparative answer-quality measurements, latency results, token-cost figures, cache-hit telemetry, context-window pressure readings, or controlled evaluations for the two configurations. Relaunching on:

```text
--model claude-fable-5-1 --resume 5f51e26c-982c-4726-9e68-aa66912e330c
```

would introduce a session transition without any supplied evidence that the transition would improve PM performance. A resume identifier may restore conversational state, but the evidence packet does not establish that it preserves the existing runtime’s prompt cache, advisor state, tool-side state, compaction boundaries, or unstated operational context. Those properties must not be assumed.

The warm Opus-plus-Fable-advisor configuration therefore wins under a conservative decision rule:

> When comparative capability evidence is absent, preserve the functioning session with the greatest demonstrated continuity and the fewest transition uncertainties.

A Fable relaunch becomes defensible if the warm session exhibits a concrete problem—for example, context degradation, repeated instruction loss, unacceptable latency, quota pressure, persistent routing failure, or materially higher cost—or if a controlled side-by-side evaluation establishes that resumed Fable performs at least as well on the PM’s actual workload.

The Multi-Agent Q&A Council is **not presently ready to claim comprehensive coverage of all 39 BD-KB volumes**. The supplied evidence confirms only that Volume 30 contained a partial master index and that the present document is Volume 38. The Volume 30 index reportedly marked Volumes 00–07 as confirmed and Volumes 08–29 as unverified. No titles or subject summaries for Volumes 00–29 were included. No evidence about Volumes 31–37 was included. A complete epistemic index is provided below, but it necessarily records these volumes as unresolved rather than inventing their contents.

Likewise, no `BH1-*` row dossier, source-code root-cause analysis, exact line citations, `BD_GATE_SCOPE`, or evidence-backed file-conflict grouping can be produced from the supplied reports. Both investigators independently identified the same source deficiency. Filling those fields would be fabrication.

---

## 1. Evidence boundary and citation integrity

The dossier is derived exclusively from two investigator reports. Both investigators obeyed the no-tools restriction and declined to invent missing evidence.

The reports reference two files:

- [POOL_STATE.tsv](/home/mboyle/bd-persist/POOL_STATE.tsv)
- [compact-bd-pm-B.md](/home/mboyle/bd-persist/compact-bd-pm-B.md)

Neither investigator supplied numbered excerpts or line ranges. Consequently, **exact file-and-line citations are unavailable**. Adding line numbers based on guesswork would create false precision. The most exact defensible citations are therefore the absolute file paths plus the reported snapshot metadata.

| Evidence item | Reported observation | Citation quality |
|---|---|---|
| Pool telemetry | `POOL_STATE.tsv`, observed `2026-10-19T23:25:15Z`; report saw only the first 2,375 bytes | File-level only; exact lines unavailable |
| PM compaction summary | `compact-bd-pm-B.md`, written `22:35:59Z`; report saw only the first 498 bytes | File-level only; exact lines unavailable |
| Volume 30 material | First 16,000 bytes reportedly supplied to Investigator 2 | No absolute file path or line numbers supplied |
| `BH1-*` rows | No row identifiers or definitions supplied | No citation possible |
| Relevant source code | No source excerpts supplied | No citation possible |
| Git blame | No blame output supplied | No citation possible |
| Prompt-cache metrics | No cache counters, logs, or traces supplied | No citation possible |
| PM model comparison | No controlled evaluation supplied | Configuration-level reasoning only |

This limitation matters because the requested deliverable combines several distinct kinds of evidence:

1. Operational telemetry.
2. Session continuity and cache behavior.
3. Backlog-row definitions.
4. Source-code ownership and causal tracing.
5. Cross-volume knowledge indexing.

Only a fragment of the first category and a partial description of the fifth were provided.

---

## 2. PM configuration decision

### 2.1 Options under consideration

| Option | Known state | Principal advantage | Principal uncertainty |
|---|---|---|---|
| Keep warm session: `claude-opus-5-5 --effort high --advisor fable` | Reported as already warm and active | Preserves current conversational and operational continuity | No supplied measurements of cache health, quality, latency, or remaining context |
| Relaunch: `claude-fable-5-1 --resume 5f51e26c-982c-4726-9e68-aa66912e330c` | Proposed but not evidenced as tested | May simplify the configuration and recover a prior transcript | Unknown fidelity of resumed runtime state; unknown cache behavior; no comparative evaluation |

### 2.2 Why keeping the warm session is the current recommendation

The decisive consideration is the difference between **known continuity** and **unmeasured transition risk**.

The existing session is described as warm. At minimum, “warm” indicates that it is already established and has accumulated working context. The supplied packet does not quantify the prompt cache, so it would be improper to claim a particular cache-hit rate or token saving. Still, preserving an already functioning session avoids introducing several unanswered questions:

- Does `--resume` reconstruct the exact transcript, or only a persisted subset?
- Does it retain summaries produced during compaction?
- Does it restore advisor-mediated reasoning state?
- Does it preserve any ephemeral PM assumptions not written into durable files?
- Does it preserve prompt-cache entries, or must those entries be rebuilt?
- Does the resumed Fable session interpret prior Opus-generated state consistently?
- Is the resume target current enough to include all decisions made after its capture?

None of these questions is answered by the investigator reports. The resume UUID proves only that a candidate resume target has been identified; it does not prove semantic or cache equivalence.

The existing configuration also has a useful architectural property: the primary model and advisor are not identical. That may provide a second perspective during difficult PM judgments. However, because no routing traces were supplied, this dossier does **not** assert how often the advisor is invoked, what information it receives, or whether its contribution measurably improves outcomes.

### 2.3 Why this is not a blanket endorsement of Opus

The recommendation should not be misread as a permanent model-ranking judgment.

No evidence was supplied for:

- PM task accuracy by model.
- Instruction-retention rate.
- Hallucination rate.
- Council synthesis quality.
- Time to first useful answer.
- End-to-end task latency.
- Input or output token consumption.
- Prompt-cache read or write volume.
- Dollar cost.
- Compaction frequency.
- Resume fidelity.
- Advisor invocation frequency.
- Operator correction frequency.

Without those measurements, a claim such as “Opus is better,” “Fable is cheaper,” or “the advisor configuration has higher quality” would be unsupported.

The present recommendation is narrower: **do not replace a functioning warm session with an unvalidated resumed session merely on configuration preference.**

### 2.4 Conditions that would justify a Fable relaunch

A relaunch should be considered when at least one trigger is observed and documented:

| Trigger | Required evidence | Decision implication |
|---|---|---|
| Context degradation | Repeated loss of current orders, volume state, or PM constraints | Resume or rebuild may be safer than continuing |
| Compaction damage | Material facts missing after compaction and recoverable from the resume target | Fable resume becomes attractive |
| Latency problem | Comparable PM prompts show materially lower Fable latency | Consider controlled migration |
| Cost or quota pressure | Actual per-task consumption shows the warm configuration is unsustainable | Prefer the sustainable configuration if quality remains acceptable |
| Advisor failure | Routing logs show the advisor is unavailable, ignored, or counterproductive | Remove or replace the advisor path |
| Quality advantage | Blind or rubric-based comparison favors Fable on representative PM questions | Relaunch becomes evidence-backed |
| Session instability | Repeated errors, crashes, or unusable state in the warm session | Transition risk may be lower than continuation risk |
| Resume verification | The resume target is shown to contain all current critical state | Major continuity objection is removed |

The best evaluation would use a small set of real PM questions covering queue triage, conflict adjudication, evidence synthesis, and operator-order retention. Outputs should be judged against the same rubric, with model identity hidden where practical.

---

## 3. Telemetry findings and their limited relevance

Investigator 1 reported the following snapshot from [POOL_STATE.tsv](/home/mboyle/bd-persist/POOL_STATE.tsv):

| Resource | Reported status | Reported consumption or availability |
|---|---|---|
| Pool A | `OK` | 27% weekly use |
| Pool B | `OK` | 40% weekly use |
| Pool C | `OK` | 33% weekly use |
| Pool D | `OK` | 41% weekly use |
| Codex | `OK` | 49% weekly; reset reported as `2026-10-25T11:51:11Z` |
| AGY Gemini | Constrained relative to others | 5.14% weekly remaining |
| AGY Claude+GPT | `OK` | 82.4% weekly remaining |
| Grok | `UNKNOWN` | CLI reportedly exposes session spend but not remaining quota or reset |
| Local | `OK` | 8/8 slots available |
| Kimi | `OK` | 15% of five-hour window and 46% monthly quota |

Pool D’s five-hour window was reported to reset at `23:50Z`. Relative to the observation timestamp of `23:25:15Z`, that is approximately 25 minutes, although the investigator summarized it as “about 20 minutes.”

The telemetry does not establish which pool serves `bd-pm-B`, nor does it map the warm Opus session or proposed Fable session to a specific quota. It therefore cannot resolve the model-selection question directly.

The most relevant general observation is that the reported Claude+GPT weekly capacity was not under immediate pressure. Conversely, Gemini was the tightest disclosed resource. Since neither proposed PM configuration is described as Gemini-backed, that constraint does not favor either PM option.

The source was truncated at 2,375 bytes. Later rows, if any, were not visible to the investigator. The telemetry must therefore be treated as a partial historical snapshot, not a comprehensive present-state census.

---

## 4. PM compaction evidence

Investigator 1 described [compact-bd-pm-B.md](/home/mboyle/bd-persist/compact-bd-pm-B.md) as a 498-byte truncated snapshot written at `22:35:59Z`. It reportedly states:

- `bd-pm-B` claims no review directories.
- `trains/claims.tsv` is absent.
- There is consequently no train-row data in that snapshot.
- A verdict must not be filed from the compaction summary alone.
- The census and commands should be rerun before treating the state as verified.

This file does not provide prompt-cache telemetry and does not compare the two PM model configurations. More importantly, its own warning undermines any attempt to treat it as authoritative operational truth.

The root cause is straightforward: a compaction summary is a lossy continuity artifact, not a substitute for primary evidence. It may preserve orientation while omitting the detail necessary for a verdict. That reinforces the conservative model recommendation: avoid a session transition until durable state has been verified, because neither the compaction artifact nor the supplied resume identifier establishes complete continuity.

---

## 5. Multi-Agent Q&A Council index: Volumes 00–38

The following is the complete **epistemic index** possible from the supplied packet. “Confirmed” means Volume 30 reportedly marked the volume as present. It does not mean its title or contents were supplied.

| Volume | Council knowledge status | Q&A readiness |
|---:|---|---|
| 00 | Existence reportedly confirmed by Volume 30; title and contents absent | Not briefable beyond existence |
| 01 | Existence reportedly confirmed; title and contents absent | Not briefable beyond existence |
| 02 | Existence reportedly confirmed; title and contents absent | Not briefable beyond existence |
| 03 | Existence reportedly confirmed; title and contents absent | Not briefable beyond existence |
| 04 | Existence reportedly confirmed; title and contents absent | Not briefable beyond existence |
| 05 | Existence reportedly confirmed; title and contents absent | Not briefable beyond existence |
| 06 | Existence reportedly confirmed; title and contents absent | Not briefable beyond existence |
| 07 | Existence reportedly confirmed; title and contents absent | Not briefable beyond existence |
| 08 | Reportedly marked unverified in Volume 30 | Not ready |
| 09 | Reportedly marked unverified in Volume 30 | Not ready |
| 10 | Reportedly marked unverified in Volume 30 | Not ready |
| 11 | Reportedly marked unverified in Volume 30 | Not ready |
| 12 | Reportedly marked unverified in Volume 30 | Not ready |
| 13 | Reportedly marked unverified in Volume 30 | Not ready |
| 14 | Reportedly marked unverified in Volume 30 | Not ready |
| 15 | Reportedly marked unverified in Volume 30 | Not ready |
| 16 | Reportedly marked unverified in Volume 30 | Not ready |
| 17 | Reportedly marked unverified in Volume 30 | Not ready |
| 18 | Reportedly marked unverified in Volume 30 | Not ready |
| 19 | Reportedly marked unverified in Volume 30 | Not ready |
| 20 | Reportedly marked unverified in Volume 30 | Not ready |
| 21 | Reportedly marked unverified in Volume 30 | Not ready |
| 22 | Reportedly marked unverified in Volume 30 | Not ready |
| 23 | Reportedly marked unverified in Volume 30 | Not ready |
| 24 | Reportedly marked unverified in Volume 30 | Not ready |
| 25 | Reportedly marked unverified in Volume 30 | Not ready |
| 26 | Reportedly marked unverified in Volume 30 | Not ready |
| 27 | Reportedly marked unverified in Volume 30 | Not ready |
| 28 | Reportedly marked unverified in Volume 30 | Not ready |
| 29 | Reportedly marked unverified in Volume 30 | Not ready |
| 30 | Partial master index and PM cheatsheet described by Investigator 2 | Partially ready; unresolved items remain |
| 31 | No evidence supplied | Unknown |
| 32 | No evidence supplied | Unknown |
| 33 | No evidence supplied | Unknown |
| 34 | No evidence supplied | Unknown |
| 35 | No evidence supplied | Unknown |
| 36 | No evidence supplied | Unknown |
| 37 | No evidence supplied | Unknown |
| 38 | Present advisory dossier | Ready within its explicit evidence limitations |

### Volume 30 contents known from the report

Investigator 2 says the supplied portion of Volume 30 covered:

- Credential and state-file locations.
- A BD-KB index in which Volumes 00–07 were confirmed.
- Volumes 08–29 marked unverified.
- The O3416 pause ledger and resume steps.
- Four unexplained timestamps or labels: `18:06`, `~/bin`, `11:29`, and `02:30`.
- A repository-count discrepancy: seven versus eight repositories.
- Standby local models at `.83:18080` and `.72:8000`.

The four timestamp-like entries were reportedly marked low or medium confidence. Their meanings should not be inferred from formatting alone. Likewise, the seven-versus-eight repository discrepancy remains a census problem rather than a resolved fact.

Volume 30 reportedly declared that it had been written without commands and marked these gaps `⚠ UNVERIFIED`. This self-labeling is important: the volume can orient the Council, but it cannot by itself close questions that depend on live state or primary files.

---

## 6. Q&A Council readiness matrix

| Question class | Present readiness | Reason |
|---|---|---|
| What PM configuration should be used now? | **Conditional answer available** | Continuity favors the warm session, but no comparative benchmark exists |
| What is the current prompt-cache hit rate? | **Not answerable** | No cache telemetry supplied |
| Will the resume UUID preserve all PM state? | **Not answerable** | No resume-fidelity evidence supplied |
| What are the titles of all 39 volumes? | **Not answerable** | Titles absent except the described roles of Volumes 30 and 38 |
| Which volumes are verified? | **Partially answerable** | 00–07 reportedly confirmed; 08–29 reportedly unverified; 31–37 unknown |
| What are the `BH1-*` root causes? | **Not answerable** | No row definitions or source code supplied |
| Which files conflict across `BH1-*` rows? | **Not answerable** | No touched-file lists supplied |
| What should `BD_GATE_SCOPE` contain? | **Not answerable** | Scope depends on actual touched files |
| What did the O3416 pause ledger require? | **Only broadly answerable** | Its existence and resume-step coverage were reported, but text was not supplied |
| Why is the repository count seven versus eight? | **Not answerable** | No repository census supplied |
| What do the four unexplained timestamps mean? | **Not answerable** | Volume 30 itself reportedly assigned low or medium confidence |

---

## 7. Root-cause analysis of the investigation gaps

The central failure is not a model failure or a source-code defect. It is an **evidence-packaging failure**.

The requested dossier requires row-level and line-level conclusions, but the packet contains summary prose only. Specifically:

1. No `BH1-*` identifiers were assigned.
2. No backlog-row text was included.
3. No relevant source files or excerpts were included.
4. No git-blame output was included.
5. No complete Volume 00–38 manifest was included.
6. No PM prompt-cache counters were included.
7. No side-by-side model outputs were included.
8. The two referenced state files were truncated.
9. The PM compaction file explicitly warns against issuing a verdict from it.
10. The no-tools rule prevented independent recovery of missing primary evidence.

The two investigators converged on this diagnosis independently. That agreement increases confidence in the finding: the missing dossier fields are caused by absent inputs, not merely by one investigator overlooking available material.

---

## 8. File-conflict and overlap groupings

No evidence-backed code conflict groups can be constructed. The only defensible groupings are evidence-availability groups:

| Group | Items | Conflict status |
|---|---|---|
| Operational-state artifacts | `POOL_STATE.tsv`, `compact-bd-pm-B.md` | No file conflict established; both are partial snapshots serving different purposes |
| Volume-index evidence | Partial Volume 30 material | No repository path or line citation supplied |
| `BH1-*` implementation files | Unknown | Conflict analysis impossible |
| Train claims | `trains/claims.tsv` reportedly absent from the PM snapshot | No row conflict can be derived |
| Volumes 00–07 | Existence reportedly confirmed | Internal overlap unknown |
| Volumes 08–29 | Reportedly unverified | Internal overlap unknown |
| Volumes 31–37 | No evidence supplied | Internal overlap unknown |
| Volume 38 | This advisory | Analytical only; no code files touched |

### `BD_GATE_SCOPE`

`BD_GATE_SCOPE` must remain **UNKNOWN / UNSET** for all `BH1-*` work. A gate scope should be derived from verified touched files and relevant tests. Assigning one without row definitions or source paths could omit affected components or gate unrelated areas.

---

## 9. Final advisory to `bd-pm-B`

Continue the warm:

```text
claude-opus-5-5 --effort high --advisor fable
```

Do not relaunch solely because the Fable resume target exists. Treat:

```text
claude-fable-5-1 --resume 5f51e26c-982c-4726-9e68-aa66912e330c
```

as a candidate recovery or migration path, not as a proven equivalent.

Before a planned switch, the PM should require four evidence bundles:

1. A verified current-state ledger identifying decisions made after the resume checkpoint.
2. A small representative PM benchmark executed under both configurations.
3. Session telemetry covering latency, context pressure, cache behavior, and quota consumption.
4. A resume-fidelity check demonstrating that critical orders, council state, and unresolved questions survive the transition.

For Council briefing, Volume 38 should be presented with a strict confidence legend:

- **Established:** the two file paths, reported snapshot metadata, reported quota values, existence of the resume UUID, and the current configuration alternatives.
- **Reported but not independently verified:** Volume 30’s index status, O3416 material, repository discrepancy, timestamps, and standby local endpoints.
- **Unknown:** all `BH1-*` details, source root causes, line citations, conflict groupings, `BD_GATE_SCOPE`, Volumes 31–37, and comparative model performance.

The Council can responsibly answer the immediate configuration question with a conservative “keep warm pending evidence.” It cannot responsibly claim a complete semantic briefing across all 39 volumes or a source-level BH1 dossier from the current packet. That boundary is not a weakness in the analysis; it is the necessary distinction between an auditable investigation and invented certainty.

---

# Appendix: Specialist Subagent Read-Only Code Inspection Reports

## Investigator Report 1: PM Session & Prompt-Cache Telemetry Analyst (`claude-sonnet-5-5-high`)

# Dossier status: BH1 rows can't be documented from the supplied context

You told me not to call tools, and the `<SOURCE_CONTEXT>` has no `BH1-*` row IDs, source code, line numbers or git-blame output. Any file path, function name, root cause or overlap group I gave you would be invented, so I haven't given any.

| Required field | Status for every `BH1-*` row |
|---|---|
| (1) File path(s) and function/line numbers | **UNKNOWN**. No code or row text was provided. |
| (2) Root-cause mechanism | **UNKNOWN**. Nothing in context to trace. |
| (3) Conflict/overlap group | **UNKNOWN**. I don't know which rows are assigned or what files they touch. |
| (4) `BD_GATE_SCOPE` | **UNKNOWN**. I can't set a scope without the touched files. |

## What the context does show (telemetry only, no BH1 link)

**[POOL_STATE.tsv](file:///home/mboyle/bd-persist/POOL_STATE.tsv)** (truncated at 2375 bytes), observed 2026-10-19T23:25:15Z:
- Pools A, B, C, D are `OK`. Weekly use is 27%, 40%, 33% and 41%.
- Pool D's five-hour window resets at 23:50Z, about 20 minutes after the current time, so it is the nearest to resetting.
- `codex` is `OK` at 49% weekly, resetting 2026-10-25T11:51:11Z.
- `AGY` is `OK`. Gemini weekly remaining is only 5.14%, which is the tightest limit shown. Claude+GPT weekly remaining is 82.4%.
- `grok` is `UNKNOWN`. The grok CLI only exposes per-session spend, with no remaining/limit/reset reading.
- `local` is `OK`, with 8/8 slots available.
- `kimi` is `OK` at 15% of its five-hour window and 46% of its monthly quota.
- The file is truncated, so any later rows or pools are not visible.

**[compact-bd-pm-B.md](file:///home/mboyle/bd-persist/compact-bd-pm-B.md)** (truncated at 498 bytes), written 22:35:59Z:
- `bd-pm-B` claims no review directories.
- `trains/claims.tsv` is absent, so there is no train-row data.
- The snapshot says not to file a verdict from a compaction summary and to re-run the census and commands instead. Nothing here counts as verified evidence, and nothing in it concerns prompt-cache telemetry.

## To complete the dossier, I need one of these

1. The `BH1-*` row text, plus the relevant source and blame excerpts, pasted into context.
2. Permission to use read-only tools (`mcp_bd_row`, `mcp_bd_brief`, `mcp_bd_queue_rows`, `mcp_bd_ask`, `read_file`, `git blame`), which lifts the no-tools rule for this task. I would not modify any files.

Tell me which, and I'll produce the per-row dossier.

## Investigator Report 2: 38-Volume Master Index & Q&A Readiness Analyst (`claude-sonnet-5-5-low`)

I can't produce the dossier you asked for, because the `<SOURCE_CONTEXT>` has no `BH1-*` rows, no code, and no git-blame data. I also have no assigned row IDs. Filling in file paths, line numbers, root causes, conflict groups, or `BD_GATE_SCOPE` values would mean making them up.

What the context does contain is the first 16,000 bytes of BD-KB Volume 30, the PM master index and cheatsheet. It covers:
- credential and state file locations
- the BD-KB volume index (Vols 00–07 confirmed, 08–29 unverified)
- the O3416 pause ledger and resume steps
- the four unexplained timestamps (`18:06`, `~/bin`, `11:29`, `02:30`), all marked low or medium confidence
- the repo list, which is unresolved (7 vs 8)
- the standby local models at `.83:18080` and `.72:8000`

The source file itself says it was written without running any commands and flags these gaps as `⚠ UNVERIFIED`.

You'd also need to tell me which `BH1-*` rows are assigned to this Q&A-readiness seat.

To get the dossier, I need one of these:
1. **The `BH1-*` row definitions.** These could come from a backlog or queue file, or from a pasted slice, plus the relevant source files.
2. **Permission to look them up.** Your read-only rule says no tool calls, so I haven't tried. If you lift it, I can look the rows up with the `bd` row and queue tools. I would still only read, and I wouldn't change anything.
3. **A different deliverable from Vol 30.** For example, a Q&A readiness checklist covering the unverified items and the commands that would settle each one.
