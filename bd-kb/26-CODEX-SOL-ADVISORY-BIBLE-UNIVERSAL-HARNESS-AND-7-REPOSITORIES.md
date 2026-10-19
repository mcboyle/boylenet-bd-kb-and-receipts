# BD-KB Volume 26: Codex Sol Advisory Bible — Universal Multi-Repo Harness Architecture & Onboarding New Projects
> **Author**: `Spec-226` (`codex (gpt-5.6-sol)`)
> **Generated At**: `2026-10-19T22:57:06Z`
> **Scope**: Strictly Read-Only Infrastructure, Tools, Services, LLMs & PM Catch-Up Documentation (`O3416` Compliant)

---

# BD-KB Volume 26: Codex Sol Advisory Bible

## Universal Multi-Repo Harness Architecture and Onboarding New Projects

> **Audience:** operators, `bd-pm-B`, integrators, harness maintainers, and repository owners  
> **Scope:** read-only architectural and operational guidance for `bd-persist/harness`  
> **Primary concern:** safely applying one swarm harness to seven synchronized GitHub/Gitea repositories and to future projects  
> **Evidence boundary:** this guide is based only on the supplied source context. Two named harness files were absent, and only the first 12,000 bytes of `bd-collect-gate.sh` were supplied. Any interface not directly visible in that excerpt is identified as an architectural contract or onboarding requirement rather than asserted as existing implementation.

---

## 1. Purpose

The Universal Swarm Harness exists to separate fleet workflow from repository-specific behavior.

A fleet should not need a different collector, preflight engine, scratch-worktree policy, artifact layout, and verdict protocol for every repository it serves. Those mechanisms belong to the harness. What varies by repository is comparatively small:

- Which repository is under test.
- Which command constitutes its mechanical gate.
- What portion of the repository the gate should cover.
- Which preflight specification defines its required checks.

Those differences are represented by four configuration inputs:

| Variable | Responsibility |
|---|---|
| `BD_REPO` | Identifies the repository being operated on. |
| `BD_GATE_CMD` | Defines the repository-specific mechanical validation command. |
| `BD_GATE_SCOPE` | Limits or describes the files, package, module, or test surface relevant to the task. |
| `BD_PREFLIGHT_SPEC` | Selects the repository-specific preflight policy or specification. |

The harness should own orchestration, artifact handling, worktree lifecycle, timeouts, retries, verdict interpretation, logging, and safety controls. The repository configuration should own only the facts needed to validate that repository.

This division is what makes the harness universal.

---

## 2. Evidence Available in the Supplied Source

The supplied context contains:

- An absent `bd-Cut-Workspace.sh`.
- The beginning of `bd-collect-gate.sh`.
- An absent `bd-cut-preflight.sh`.

The visible collector gate establishes several concrete properties:

1. It runs after collection has written `patch.diff`.
2. It runs before worker markers are removed and before the final collect verdict is reported.
3. It replays the patch’s own added or changed tests.
4. It runs a pre-cut gate.
5. It uses a scratch worktree derived from the integrator’s repository.
6. It must not write into `bd-review-wt`.
7. It preserves evidence under the artifact directory.
8. It distinguishes real failures from inability to measure.
9. It protects registered and foreign worktrees from deletion.
10. Its repository path is configurable through `BD_GATE_REPO`, whose visible default is `/home/mboyle/BulkDownloader`.

The visible excerpt does **not** establish the exact parsing or execution semantics of `BD_REPO`, `BD_GATE_CMD`, `BD_GATE_SCOPE`, or `BD_PREFLIGHT_SPEC`. It also does not enumerate the seven synchronized repositories.

Therefore, the correct universal architecture is:

- Treat those four variables as the public, repository-neutral contract.
- Adapt them into the lower-level variables consumed by individual harness stages.
- Do not assume that setting a variable affects a script unless that script or its wrapper explicitly consumes it.
- Do not invent repository names, commands, or specifications absent from the source.
- Validate all seven repository profiles individually before declaring universal coverage.

In particular, the visible collector presently reads `BD_GATE_REPO`, not `BD_REPO`. A dispatcher or compatibility layer must map the universal repository selection into the collector’s stage-specific name:

```bash
BD_GATE_REPO="$BD_REPO"
export BD_GATE_REPO
```

That mapping is an architectural necessity unless the collector is later changed to accept `BD_REPO` directly.

---

## 3. The Universal Harness Model

The harness can be understood as four layers.

```text
Operator or bd-pm-B
        |
        v
Repository profile
  BD_REPO
  BD_GATE_CMD
  BD_GATE_SCOPE
  BD_PREFLIGHT_SPEC
        |
        v
Universal orchestration
  workspace creation
  patch collection
  gate execution
  preflight evaluation
        |
        v
Repository-neutral evidence
  patch.diff
  DONE.md
  .gate/*
  stream.log
  ledger entries
        |
        v
Verdict routing
  pass / retry / failed / refused / return / unknown
```

The repository profile answers, “What are we validating?” The harness answers, “How do we validate it safely, preserve the evidence, and route the result?”

No repository profile should redefine artifact contracts or deletion rules. No universal harness stage should hard-code the build system of a particular repository.

---

## 4. `BD_REPO`: Repository Identity

`BD_REPO` is the canonical path to the local clone that owns the objects, refs, and worktrees used by the harness.

It should be an absolute path:

```bash
export BD_REPO=/absolute/path/to/project
```

The path should resolve to the intended repository, not merely a directory inside it. It should support at least:

```bash
git -C "$BD_REPO" rev-parse --show-toplevel
git -C "$BD_REPO" worktree list --porcelain
```

For the visible collector gate, the universal wrapper must pass the value as `BD_GATE_REPO`:

```bash
export BD_GATE_REPO="$BD_REPO"
```

### Required invariants

A valid `BD_REPO` must:

- Name exactly one repository.
- Be absolute and stable for the duration of a run.
- Be readable by the harness.
- Have an appropriate base ref available.
- Be the repository whose worktree registry is consulted.
- Match the repository against which the patch is intended to apply.
- Not point at an artifact directory or review worktree.
- Not change meaning between workspace creation, collection, gate execution, and preflight.

Repository identity is a safety boundary. If the harness creates a scratch tree from repository A but applies a patch intended for repository B, all later results are untrustworthy even when commands exit successfully.

### Repository aliases

Human-friendly aliases are useful, but aliases should resolve before any harness stage starts:

```text
bulk-downloader -> /home/mboyle/BulkDownloader
service-api     -> /srv/repos/service-api
web-console     -> /srv/repos/web-console
```

Downstream scripts should receive the resolved absolute path, not the alias.

### GitHub and Gitea synchronization

Whether a repository is mirrored to GitHub, Gitea, or both should not alter `BD_REPO`. The variable identifies the local repository used for validation. Remote synchronization is a separate concern.

A repository may have multiple remotes:

```text
origin  -> GitHub
gitea   -> internal Gitea
```

The harness must not infer repository identity solely from a remote URL. The local repository, selected base ref, and patch must agree independently of which remote is authoritative for publication.

---

## 5. `BD_GATE_CMD`: Repository-Specific Mechanical Validation

`BD_GATE_CMD` defines the command that determines whether the candidate patch clears the repository’s mechanical floor.

Examples of possible project commands include:

```bash
export BD_GATE_CMD='pytest -q'
```

```bash
export BD_GATE_CMD='npm test -- --runInBand'
```

```bash
export BD_GATE_CMD='go test ./...'
```

These examples illustrate the contract; they are not claims about any of the seven existing repositories.

### What belongs in the gate

A mechanical gate should be:

- Deterministic enough to support repeatable verdicts.
- Non-interactive.
- Runnable inside a scratch worktree.
- Scoped to the candidate repository.
- Capable of returning a meaningful exit status.
- Fast enough for the collector’s gate budget.
- Free from credential disclosure.
- Unlikely to modify external systems.

The visible collector distinguishes this cheap mechanical floor from later, more comprehensive preparation and lens review. The gate’s purpose is to reject obviously broken patches before spending higher-cost review resources.

### What does not belong in the gate

Avoid placing the following in `BD_GATE_CMD` unless explicitly required and safely isolated:

- Deployment.
- Publication.
- Database migration against shared infrastructure.
- Destructive cleanup.
- Remote branch mutation.
- Credential-printing diagnostics.
- Long-running complete-system evaluation better suited to preflight or lens review.
- Commands that depend on an interactive terminal.

### Command execution contract

The wrapper executing `BD_GATE_CMD` should define:

- The working directory.
- The shell used.
- Timeout behavior.
- Environment inheritance.
- Scope substitution.
- Logging.
- Exit-code translation.

A safe contract is to run the command from the scratch worktree root. The harness should record the exact command and its exit status without leaking secrets.

The command must never be built by unsafely concatenating untrusted task text. Repository profiles are trusted configuration; worker-provided values are not.

---

## 6. `BD_GATE_SCOPE`: Validation Boundaries

`BD_GATE_SCOPE` describes the relevant portion of a repository for the gate.

Possible scopes include:

- The entire repository.
- One package.
- One service.
- One workspace member.
- A set of changed test files.
- A path prefix.
- A named test target.

Examples:

```bash
export BD_GATE_SCOPE='.'
```

```bash
export BD_GATE_SCOPE='packages/downloader'
```

```bash
export BD_GATE_SCOPE='services/api'
```

The value’s syntax must be defined by the repository profile or by the command adapter. A universal harness must not assume that every build system interprets a path in the same way.

For example:

```text
pytest       may accept file or directory arguments
go test      expects package patterns
npm scripts  may require workspace flags
cargo test   may require package names
```

Therefore, `BD_GATE_SCOPE` should be treated as structured profile input, not blindly appended to `BD_GATE_CMD`.

### Recommended scope modes

A profile should explicitly choose one of these modes:

| Mode | Meaning |
|---|---|
| `repo` | Run the gate for the complete repository. |
| `changed-tests` | Run test files added or modified by the patch. |
| `path` | Run validation for one repository-relative directory. |
| `package` | Run validation for a named package or workspace member. |
| `target` | Run a build-system-specific test target. |
| `custom` | Let a repository adapter interpret the value. |

The visible collector specifically states that it replays the patch’s own added or changed test files. That behavior can coexist with `BD_GATE_SCOPE`: changed tests provide the first narrow floor, while the configured scope provides the repository-aware validation boundary.

### Scope safety

A scope must be repository-relative unless a profile explicitly requires another form. Reject values that:

- Escape through `..`.
- Resolve outside `BD_REPO`.
- Name the artifact store.
- Name `bd-review-wt`.
- Depend on shell expansion from worker-controlled content.
- Change meaning based on the caller’s current directory.

An empty scope needs a documented meaning. It must not silently alternate between “whole repository,” “no tests,” and “infer from patch.”

---

## 7. `BD_PREFLIGHT_SPEC`: Policy Above the Mechanical Floor

`BD_PREFLIGHT_SPEC` selects the checks required after the cheap gate and before higher-cost review or integration.

Conceptually, a preflight specification may define:

- Required test lanes.
- Linting.
- Type checking.
- Build checks.
- Generated-file consistency.
- Dependency validation.
- Packaging checks.
- Repository-specific forbidden changes.
- Timeout budgets.
- Required evidence.
- Allowed skip conditions.

Example profile values might be:

```bash
export BD_PREFLIGHT_SPEC=/home/mboyle/bd-persist/harness/specs/service-api.preflight
```

or:

```bash
export BD_PREFLIGHT_SPEC=python-library
```

The first form selects a file; the second selects a named built-in profile. The actual implementation must choose and document one form. Because `bd-cut-preflight.sh` was absent from the supplied context, this guide does not assert which representation is currently implemented.

### Separation from `BD_GATE_CMD`

`BD_GATE_CMD` is an executable mechanical check. `BD_PREFLIGHT_SPEC` is policy.

That distinction matters:

```text
BD_GATE_CMD
    "Can this candidate clear a cheap, objective floor?"

BD_PREFLIGHT_SPEC
    "What evidence must exist before this repository's candidate is review-ready?"
```

The same gate command may be reused by several repositories while their preflight policies differ. Conversely, repositories may share a preflight template while using different test commands.

### Specification immutability

For reproducibility, a preflight result should identify the specification version or digest used. If the specification changes between retry attempts, the artifact should show that change. Otherwise two verdicts with the same task ID may appear contradictory when they were evaluated under different policies.

---

## 8. Collector Gate Behavior

The supplied `bd-collect-gate.sh` defines the most concrete part of the architecture.

Its normal placement is:

```text
worker completion
    -> collect writes patch.diff
    -> collector gate
    -> collect interprets gate result
    -> marker cleanup or retry/bounce routing
    -> preparation and lens review
```

The gate preserves evidence under:

```text
<artifact-directory>/.gate/
```

Visible evidence includes:

- `SKIP`
- `UNKNOWN`
- `NONPATCH`
- `REFUSED`
- `stream.log`
- A preserved worker verdict referenced as `.gate/DONE.md.worker`

The exact later portions of the script were not supplied, so additional evidence files may exist.

### Exit-code contract

The collector must interpret exit codes exactly:

| Exit | Meaning | Required collector behavior |
|---:|---|---|
| `0` | Pass or intentional skip | Proceed unchanged. |
| `10` | Retry sent | Exit as still running; preserve worker markers. |
| `11` | Gate failure | Proceed into the normal bounce path without spending a lens. |
| `12` | Gate refused | Do not proceed; the verdict contract was not parseable. |
| `13` | Non-patch return | Preserve the answer for registrar return. |
| `14` | Gate unknown | Measurement could not be completed. |
| `2` | Usage error | Treat as harness invocation failure. |
| `3` | Named refusal | Treat as explicit safety/configuration refusal. |

The source comments contain older wording saying an unmeasurable condition could fail open with exit `0`, but the visible implementation’s `unk()` function exits `14`. The executable behavior in the shown code is the stronger evidence: callers must recognize `14` distinctly and must not collapse it into success.

### Verdict contract

`DONE.md` line one is a protocol boundary, not informal prose. The script explicitly warns that invisible bytes and malformed verdict lines must not be interpreted as success.

A collector should never use generic shell truth such as:

```bash
gate && collect
```

That loses the meaning of codes `10` through `14`. It must capture and dispatch on the exact result.

---

## 9. Scratch Worktrees and Rule 22 Protection

The gate uses a scratch worktree root, visibly defaulting to:

```text
/home/mboyle/bd-collect-gate-wt
```

The worktree guard is designed to prevent accidental removal of:

- Registered worktrees.
- Locked worktrees.
- Linked worktrees.
- Directories with malformed `.git` files.
- Git metadata that Git cannot safely resolve.
- Paths not created by the current run.

The guard relies on Git’s own resolved git-dir and common-dir answers rather than guessing from filenames.

This is vital in a multi-repository harness. Cleanup must always consult the same repository selected by `BD_REPO` and passed as `BD_GATE_REPO`. Using one repository’s worktree registry to judge a path created by another repository defeats the protection.

The visible override, `BD_ALLOW_WT_PRUNE=1`, is powerful and should not be part of routine onboarding. A repository profile must never set it by default.

The harness must also preserve the rule that scratch roots and artifact directories cannot be located inside `bd-review-wt`.

---

## 10. Applying the Contract Across Seven Repositories

Each of the seven synchronized repositories should have one explicit profile containing the same four fields.

A conceptual registry is:

```yaml
repositories:
  repo-1:
    BD_REPO: /absolute/path/to/repo-1
    BD_GATE_CMD: "<repo-specific command>"
    BD_GATE_SCOPE: "<repo-specific scope>"
    BD_PREFLIGHT_SPEC: "<repo-specific specification>"

  repo-2:
    BD_REPO: /absolute/path/to/repo-2
    BD_GATE_CMD: "<repo-specific command>"
    BD_GATE_SCOPE: "<repo-specific scope>"
    BD_PREFLIGHT_SPEC: "<repo-specific specification>"

  # Continue through repo-7.
```

The supplied source does not name the seven repositories, so an authoritative document must not fabricate them. The operator or `bd-pm-B` should populate the registry from the actual synchronized-repository inventory.

Universal coverage means more than having seven entries. For every entry, verify:

- Repository resolution.
- Base-ref resolution.
- Scratch-worktree creation.
- Patch applicability.
- Gate command execution.
- Scope interpretation.
- Preflight-spec resolution.
- Artifact isolation.
- Exact exit-code routing.
- Safe cleanup.
- GitHub/Gitea remote independence.

A repository is not onboarded merely because its path appears in configuration.

---

## 11. Onboarding a New Project

The operator or `bd-pm-B` can onboard a new project through the following controlled procedure.

### Step 1: Establish repository identity

Choose the canonical local clone and record its absolute path as `BD_REPO`.

Confirm that the intended base branch is available locally and that the clone can create linked worktrees. Decide which remote is used for GitHub and which is used for Gitea, but keep remote publication outside the gate contract.

### Step 2: Define the cheapest meaningful gate

Select a non-interactive command that catches mechanical breakage quickly.

The first gate should favor high signal and bounded runtime. Full integration suites may belong in preflight rather than collection.

Record the command as `BD_GATE_CMD`.

### Step 3: Define scope semantics

Decide whether the repository validates:

- The whole tree.
- Changed tests.
- An affected package.
- An affected service.
- A named build target.

Record both the value and its interpretation as `BD_GATE_SCOPE`. Do not leave consumers to guess whether a value is a filesystem path or package name.

### Step 4: Author or select the preflight specification

Define the evidence required for review readiness and select it using `BD_PREFLIGHT_SPEC`.

The specification should state which checks are mandatory, which may be skipped, their budgets, and how unknown results are represented.

### Step 5: Map universal variables into stage variables

At minimum, bridge repository identity for the visible collector:

```bash
export BD_GATE_REPO="$BD_REPO"
```

Apply equivalent mappings for workspace and preflight stages only where their actual implementations require them. Because the corresponding scripts were absent from the supplied context, inspect their interfaces before defining those mappings.

### Step 6: Validate with a known-good candidate

Use a harmless patch with a known passing test. Verify:

- The scratch worktree uses the new repository.
- The command runs from the expected directory.
- Scope is applied correctly.
- Evidence lands only in the task artifact directory.
- Cleanup removes only the scratch tree created by the run.
- Exit `0` is routed as pass.

### Step 7: Validate with a known-bad candidate

Use a controlled test failure and confirm that the gate records a positive mechanical failure and routes it through the expected gate-failed or bounce path without spending lens capacity.

### Step 8: Exercise protocol failures

Test:

- Missing `DONE.md`.
- Malformed verdict line.
- Non-patch return.
- Unresolvable base.
- Patch-application failure.
- Missing test interpreter.
- Timeout.
- Locked worktree.
- Existing foreign scratch path.

Each case must produce the documented evidence and exact exit code.

### Step 9: Validate both synchronization destinations

Confirm that local validation does not depend on whether GitHub or Gitea is contacted first. Remote differences must not alter gate semantics.

### Step 10: Register and document the profile

Add the project only after its profile has passed the onboarding matrix. Record:

- Repository name.
- Local path.
- GitHub remote.
- Gitea remote.
- Base branch.
- Gate command.
- Scope mode and value.
- Preflight specification.
- Expected runtime.
- Required tools.
- Profile owner.
- Last validation date.

---

## 12. Recommended Profile Template

```markdown
# Repository Harness Profile

## Identity

- Repository:
- `BD_REPO`:
- Base branch:
- GitHub remote:
- Gitea remote:

## Mechanical gate

- `BD_GATE_CMD`:
- Timeout:
- Required runtime/toolchain:
- Expected passing duration:
- External services required:

## Scope

- `BD_GATE_SCOPE`:
- Scope mode:
- Interpretation:
- Empty-scope behavior:
- Changed-test behavior:

## Preflight

- `BD_PREFLIGHT_SPEC`:
- Specification version or digest:
- Mandatory lanes:
- Allowed skips:
- Unknown-result policy:

## Safety

- Scratch root:
- Artifact root:
- Review-worktree exclusion verified:
- Worktree cleanup verified:
- Secrets-in-output review completed:

## Acceptance evidence

- Known-good candidate:
- Known-bad candidate:
- Malformed-verdict test:
- Unknown-measurement test:
- Locked-worktree test:
- GitHub synchronization test:
- Gitea synchronization test:
```

---

## 13. Operational Rules

The following rules preserve universality and trustworthiness:

1. Never hard-code a repository path in a shared wrapper when `BD_REPO` is available.
2. Never assume `BD_REPO` automatically reaches a stage that reads only `BD_GATE_REPO`.
3. Never append `BD_GATE_SCOPE` to a command without a declared adapter.
4. Never treat a preflight specification as arbitrary worker-supplied shell code.
5. Never collapse the collector’s multi-valued exit protocol into Boolean success or failure.
6. Never delete an existing scratch path unless the worktree guard authorizes it.
7. Never place artifacts or scratch worktrees inside `bd-review-wt`.
8. Never expose credentials in command lines, logs, or evidence.
9. Never call an unmeasured result a passing result.
10. Never call a repository onboarded until positive, negative, refusal, return, unknown, and cleanup paths have been exercised.

---

## 14. Final Architecture Standard

The Universal Swarm Harness is successful when every repository can be represented by data and policy rather than by forks of the harness.

The canonical contract is:

```text
BD_REPO
    identifies the repository

BD_GATE_CMD
    identifies the mechanical validation action

BD_GATE_SCOPE
    identifies the validation boundary

BD_PREFLIGHT_SPEC
    identifies the review-readiness policy
```

The harness then supplies:

```text
safe scratch isolation
+ patch replay
+ exact verdict parsing
+ bounded execution
+ durable evidence
+ retry and bounce routing
+ worktree protection
+ repository-neutral collection
```

For the visible `bd-collect-gate.sh`, `BD_REPO` must be deliberately mapped to `BD_GATE_REPO`. The absent workspace and preflight scripts must be treated as unverified interfaces until their actual contents are available. That constraint does not weaken the architecture; it defines the correct boundary between confirmed implementation and required integration work.

All seven synchronized repositories should conform to one profile schema, one artifact protocol, one worktree safety model, and one verdict-routing contract. A new project joins the swarm by supplying its four repository-specific inputs and passing the onboarding matrix—not by copying and modifying the collector.

That is the central rule of the universal design: **repository behavior is configurable; harness safety and evidence semantics are invariant.**
