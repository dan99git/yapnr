# Rules for agents

These rules apply to every automated agent (and every human) working in this repository. They carry
over the rules that governed the place-and-route work in Splanc. When a rule and a request conflict,
stop and ask the owner.

## Engineering rules

- **Selection is mechanical.** Candidates, seeds and branches are chosen by the Monte Carlo and
  successive-halving machinery, never by hand.
- **No model-driven hand routing.** Agents change the engine, not individual boards.
- **Nothing in the loop may be GUI-bound.** No `wx.App`, no KiCad GUI, and no `kicad-cli` from the
  stock macOS application bundle (every call registers a Dock icon). Use the headless copy described
  in [DEVELOPERS.md](DEVELOPERS.md#kicad). Never start `/Applications/KiCad/...` binaries directly.
- **Every worker is time-bounded.** A subprocess without a timeout is a bug.
- **Native KiCad DRC is the judge.** Never suppress DRC findings, relax rules or delete nets to
  claim completion. Electrical contracts are never relaxed without the designer.
- **New algorithms, optimization policies and changed defaults require measured A/B evidence.** New
  optional engine behavior starts behind a default-off flag. A correctness fix restoring an existing
  contract may become the normal behavior after a failing-baseline/passing-fix regression,
  preservation checks and adversarial review. Never label a policy change a correctness fix.
- **Ordering is staging only, and agents only dry-run it.** Agents run `yapnr order stage` only with
  `--dry-run`. They never upload a file to a vendor or any third-party service, never call a vendor
  API, and never open a vendor page on a human's behalf. Paying, confirming an order, accounts and
  terms belong to the human on the vendor's page
  ([docs/fab-and-ordering.md](docs/fab-and-ordering.md)).

## Repository rules

- **Public repository.** No machine paths, host names, network addresses, personal e-mail addresses,
  credentials, conversation logs or run logs. `tools/privacy_scan.py` runs as a pre-commit hook and
  as a Bazel test; its findings block the change. Curated job/diary/review records following
  `jobs/README.md` are allowed; raw logs and private design data are not.
- **Commit identity:** use the fork owner's authorized public identity or verified GitHub noreply
  identity. Name the actual agent runtime when attributing agent work; do not impersonate another
  runtime or the upstream owner. Use per-command/repository settings, never global identity changes.
  Do not write personal addresses into public records. Preserve supplied attribution trailers.
- **Issues:** GitHub issues, referenced as `#N`.
- **Keep the WORKLOG convention.** `WORKLOG.md` is a short status board (in progress, next,
  blockers, do-not-retry), rewritten at the end of each session, not a diary.
- **Run the checks you touch:** `bazel test //...` (with `--config=lowmem` on a shared machine) and
  `prek run --all-files`.
- Do not push, merge, publish or open pull requests unless the owner asked for it.

## Shared machines

The development Mac also runs long place-and-route experiments. On it:

- Check for other agents' activity (processes you did not start, new handoff entries) before long
  operations, and report it rather than competing.
- Never stop, signal or `renice` experiment processes, and never write into their directories.
- Run Bazel niced with `--config=lowmem`, one Bazel server at a time, with the output base on the
  internal disk (see [DEVELOPERS.md](DEVELOPERS.md#bazel)). Check free disk space first.
- Install tools into a private virtualenv or with `uv`/`pipx`; never into the system Python.

## Fork mission and authority

This section governs work for the `dan99git/yapnr` fork. Read `docs/FORK-IMPROVEMENT-BRIEF.md`,
`jobs/README.md`, `jobs/TODO.md`, the active job and the latest daily diary before selecting work.
It supplements the engineering rules above and resolves fork-local workflow differences with the
upstream contribution policy. It does not change upstream policy or override the owner's task
boundaries.

The goal is to perfect the software through demonstrated correctness, neat PCB placement, useful
routing results, reproducibility and measured performance. Treat perfection as a continuing
improvement objective; never claim universal correctness or completion from a clean test run or an
attractive board image.

The intended workflow is to generate and compare constrained candidate layouts on copies, then
independently validate the selected candidate before any user working board is changed. Preserve the
schematic/net mapping, original project/custom rules, mechanical anchors, footprint geometry and
existing protected layout.

## Adversarial review before execution

Every multi-agent workflow must use an independent adversarial reviewer. No feature, fix, refactor,
dependency, configuration, test, fixture, benchmark, default, scoring rule or performance-affecting
operation may be implemented until that reviewer has challenged the proposed change and recorded a
pre-change approval for its exact scope. Documentation changes require the same process, scaled to
link/claim/scope checks.

Read-only inspection and preparation of proposals, review records and task records are permitted
before approval; otherwise obtaining the first review is impossible. This exception permits
preparation only, not implementation, heavy experiments, deployment, installing dependencies or
altering the baseline. Prepared documentation drafts are proposals until reviewed and promoted to
the working branch.

1. The coordinator records the baseline commit, source inspected, problem evidence, scope, intended
   files, dependencies, alternatives, acceptance criteria, test plan, expected performance effects
   and resource limits in the job proposal.
2. A reviewer who did not author the proposal checks assumptions, failure paths, existing
   alternatives, test quality, preservation of user rules, privacy and performance risk. They must
   try to falsify the claim, not endorse the author.
3. The review record identifies reviewer, proposal digest/revision, baseline SHA, findings,
   severity, disposition and `approved`, `changes-required` or `blocked`. Approval is technical
   clearance within the owner's authorization, not new scope.
4. Blocking findings must be resolved before execution. Unsupported reviewer claims may be rejected
   only with recorded counter-evidence and reviewer reassessment.
5. Material changes to files/scope, dependencies, baseline, defaults, acceptance criteria or
   resource envelope invalidate approval. Amend and review first.

Do not bypass a reviewer because a change seems obvious, urgent, small or likely to improve
performance. If reviewers disagree materially, use a third independent reviewer; unresolved blockers
stay blocked. Do not rotate reviewers until one agrees.

## Execution and final acceptance

- Split independent jobs among workers with explicit file ownership and separate branches/worktrees
  where useful. One writer per file or shared artifact. The coordinator owns TODO, status and diary
  integration.
- Require regression evidence for each defect. A failing baseline check must fail for the claimed
  reason; the patched check must pass. Native tests remain necessary for native behavior. Mocks,
  syntax checks and image reviews have narrower meanings.
- Pin versions, inputs, seeds, flags and limits. Benchmark baseline and candidate on the same
  environment, with repeated seeds/runs and all failures/timeouts counted. No cherry-picked winning
  seed, hidden fallback or silently relaxed constraint.
- Treat runtime, memory, CPU, routing completeness, layout quality, determinism and evaluation
  correctness as separate measurements. Set tolerances before the run. Better speed does not excuse
  broken layout or electrical requirements.
- Before integration, a fresh adversarial reviewer who did not implement the change checks the exact
  diff/commit, test evidence and acceptance criteria. The proposal reviewer may do this if still
  independent of implementation. Re-run critical checks independently; an author's summary is not
  evidence.
- The final record names the candidate SHA/diff digest and validated tree. Any reviewed artifact
  change after review, including docs/tests/config, requires proportionate re-review and affected
  checks again.
- Only mark a job `done` after the coordinator confirms both review gates, required checks and
  evidence. A missing native environment means `blocked` or a narrower explicitly partial result,
  not an integration pass.
- Never suppress a finding, remove a test, loosen a rule or alter a benchmark simply to obtain green
  results. A necessary requirement change needs explicit owner authorization and its own review.

## Upstream branch intake

Review upstream work before duplicating it. Ahead counts are not evidence of useful or independent
changes. Pin main/head SHAs, inspect PR state and actual diffs, map ancestry and patch-equivalent
commits, and identify dependencies and conflicts. Review stacked work once as a dependency graph.
Publishing branches are not engine fixes. Never bulk cherry-pick every ahead commit.

Each proposed import has its own job, source PR/commit, rationale, required predecessor commits,
licence/provenance, pre-change review and tests. Apply approved commits to an isolated integration
branch, then run the same regression/performance/final-review gates as local code. Upstream checks
do not prove compatibility with this fork. Default changes require explicit before/after evaluation;
stale PR descriptions do not override source or current check results.

## Sustained and overnight work

Continue through the highest-priority unblocked authorized jobs while runtime and allocated
resources permit. Re-read TODO, the job record and diary after every handoff or context reset. Do
not abandon difficult work merely because a session is long.

Before a long run, record supervisor/worker ownership, available runtime, wall-clock deadline,
concurrency, CPU/memory/disk limits, any financial budget, heartbeat interval and checkpoint/resume
location. Every subprocess must be bounded and cancellation must stop only this job's processes.
Reuse work instead of restarting blind.

At each stage boundary and before yielding, checkpoint evidence, unresolved findings, active process
IDs, exact next action and reproducible resume instructions. A real supervisor may schedule another
bounded session; an AGENTS file alone cannot run 24/7. Do not claim background activity or
install/start a scheduler without an available authorized mechanism. Never evade service limits or
spend unapproved money.

Pause the affected job for owner stop requests, exhausted limits, missing authority, unresolved
review blockers, data-integrity risks or unavailable required capabilities. Two repetitions of the
same failed approach require recorded diagnosis before a new approach. Continue independent approved
work where useful; do not repeatedly retry a blocked step. Close completed milestones and select the
next measured improvement.

## Records, privacy and reporting

Follow `jobs/README.md` for the canonical job, review, diary and TODO formats. Curated records
contain decisions, commands, aggregate results and evidence references, not raw run logs,
transcripts, credentials, local machine details or private PCB files. Keep private/run artifacts in
the already ignored `.yapnr/` store or another locally authorized private location. Never stage
ignored artifacts with `git add -f`.

Preserve repository content. Do not delete or revert work without explicit authority; keep
superseded artifacts in the permitted local archive, outside public commits. Respect the
environment's filesystem boundaries. Do not write global configuration or install global
dependencies as a convenience.

Report the result first, briefly: what changed, what actually ran, what failed or is unknown, review
status and next action. Distinguish planned, implemented, tested, native-validated and physically
verified work. End job handoffs with:

```text
DIARY:
- did:
- decision + why:
- dead end:
- assumption:
- left:
- verified:
```
