# Fork improvement brief

Status: proposed engineering work, not implemented or accepted. Reviewed base:
`d030a752ccc548dd438f05c29d313a88a9c18ced`. Source references below refer to that base; recheck them
after upstream intake. Execution rules: [AGENTS.md](../AGENTS.md). Job queue:
[TODO](../jobs/TODO.md).

## Objective

Produce neat, constrained PCB placement candidates that agents can inspect and test before any live
KiCad change. Improve the engine and its checks, not individual demonstration boards by hand.
Success means reproducible results meeting explicit gates on a declared fixture set. It does not
mean perfect placement on every board. Keep preservation and electrical constraints ahead of
appearance, compactness and speed.

## Workflow and contract

1. Accept an immutable project copy, its complete rule/library sidecars, a versioned constraint
   profile and explicit compute limits.
2. Inventory footprints, identifiers, pad/net assignments, locks, zones, outline/cutouts, layers,
   stackup and custom rules; hash the input bundle.
3. Validate supported geometry and constraints. Return a specific refusal for missing or unsupported
   requirements; never silently approximate them.
4. Generate candidates using recorded seeds and flags. Apply only the transformations permitted by
   the input contract.
5. Evaluate placement geometry, then route and run native checks where the requested acceptance
   level requires them.
6. Select mechanically using the declared objective. Preserve rejected candidates with failure
   reasons; do not select by attractive screenshots.
7. Export candidate copies, previews, machine-readable measurements, validation reports and a
   reproducibility manifest.
8. Independently check the exact saved output. A preview or in-memory graph is not the accepted
   artifact.

The output manifest binds board and sidecar hashes to engine revision, dependencies, profile, seed,
flags and test results. Each candidate states its level: placement only, routed and
software-validated, or rejected. Unrun checks remain unknown. Show top/bottom placement,
courtyard/keepout overlays, routing lanes and constraint failures in previews. No automatic edits to
a live project, GUI session, fabrication order or external service. Applying an accepted candidate
is a separate authorized action. No private design upload or private design data in public fixtures,
issues, logs, screenshots or commits.

## Requested project profile

These are configurable project preferences, not universal EDA rules or claims about current
implementation. The profile declares coordinate origin, eligible component classes, exceptions and
tolerances before generation. Immutable source locks and mechanical requirements take precedence;
conflicting requirements fail with an explanation.

| Property                   | Requested setting                    | Check                                                                                            |
| -------------------------- | ------------------------------------ | ------------------------------------------------------------------------------------------------ |
| Movable footprint origins  | 0.25 mm grid                         | Maximum distance from the declared grid is within the profile tolerance.                         |
| Eligible passive placement | 0.5 mm grid                          | Every eligible passive passes; exceptions are explicit and counted.                              |
| Movable part rotation      | Cardinal angles                      | Every eligible part uses an allowed angle; locked non-cardinal parts stay unchanged.             |
| Courtyard separation       | 0.25 mm minimum, 0.5 mm preferred    | Minimum is a hard gate; report count and total deficit below preferred spacing.                  |
| Routing lanes              | 3 mm floor plus net-demand allowance | Named lanes retain their required clear corridor; extra width follows the reviewed demand model. |
| Alignment and rows         | Explicit groups and anchors          | Report maximum alignment error and spacing variation per group.                                  |

Use transformed courtyard/body geometry, including rotation, side and offset origins; do not
substitute origin distance for clearance. Courtyard spacing does not replace copper, voltage,
assembly, enclosure or edge-clearance rules. Lane demand must account for crossing nets, assigned
layers, track widths/clearances and escape/via needs. Define the demand formula and its tests before
implementation; until validated, lane width is a planning constraint, not proof of routability. The
current compact legalizer describes a 0.125 mm slot grid (`hardware/pnr/pnr/compact_flags.py:13`);
the requested grids need explicit implementation and tests. Never silently snap fixed parts, shrink
the board, change layer count, relax clearance or rewrite an electrical requirement to improve a
score.

## Hard preservation and acceptance gates

- Input files remain unchanged. Output footprint identities, references, pad numbers and pad/net
  assignments match the approved transformation contract.
- Locked position, rotation, side and mechanical anchors survive every entry point and saved-file
  round trip.
- Source outline, cutouts, stackup, layer usage and board-specific rules survive unless a reviewed
  job explicitly permits their change.
- Every footprint/body/courtyard fits the actual allowed region, accounting for approved connector
  overhangs and exclusions.
- No prohibited overlap, keepout intrusion, wrong-side placement, unsupported constraint or
  unexplained geometry loss.
- Run final native DRC cold on the exact final board with its complete project and custom rules,
  after every geometry-changing stage and refill.
- Routed acceptance requires zero unconnected items and zero unresolved native DRC findings;
  missing, stale or incomplete reports fail.
- Compare saved-board electrical identity independently of the router's own completion counters.
- Recheck profile gates after routing, cleanup and export; earlier placement legality does not cover
  later changes.
- Each failure identifies the item, requirement, measured value and artifact hash. Do not suppress
  findings or rename failures as warnings to pass.

Report neatness separately: alignment error, row-spacing variation, preferred-gap deficits,
courtyard minimum, lane width/demand, occupied area and outline utilization. Report routing
separately: opens, native findings, vias, wire length, layer use and relevant copper/pad-entry
checks. Use a documented ordering that rejects hard failures before quality scoring. Do not let a
weighted average conceal one broken requirement.

## Evidence and first priorities

The reviewed source contains substantive constraint and validation code. Reuse its rigid groups,
anchors, region checks and native acceptance machinery where they meet the contract. The following
findings are grounded in the reviewed base, not promises about later branches.

| Priority | Finding and evidence                                                                                                                                                                                                                                                                                                                                  | Required regression                                                                                                                                    |
| -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| P0       | Default placement ignores native locks when building its fixed mask: `hardware/pnr/pnr/place/model.py:301`. Source-lock preservation is conditional in `hardware/pnr/pnr/route/feedback.py:278`; initial pool defaults off in `hardware/pnr/pnr/place/initial_pool.py:80`. Independent synthetic checks moved a locked part and returned legal.       | All placement entry points preserve locked position, rotation and side, including saved output; conflicting explicit constraints fail.                 |
| P0       | Final snapshot omits `.kicad_dru`: `hardware/pnr/pnr/phase_capture.py:17`. Generic regeneration retains custom content only if already present: `hardware/pnr/pnr/fab_profile.py:974`. Board-specific rules are generated in `hardware/pnr/pnr/writeback.py:1598`. Two call-path checks confirmed omission; no native violation reproduction was run. | Preserve all source/custom rules across capture and staging; a native synthetic board deliberately violating a custom rule must fail final acceptance. |
| P0       | Final capture omits `final=True` at `hardware/pnr/pnr/phase_capture.py:22`, despite the cold-final contract in `hardware/pnr/pnr/native_drc.py:101`. Effect depends on optional warm-service use; no incorrect warm result was demonstrated.                                                                                                          | Final acceptance bypasses warm service and validates the final saved artifact after all modifications; test service-enabled and disabled paths.        |
| P1       | Edge-fixed placement uses incoming courtyard dimensions before requested rotation: `hardware/pnr/pnr/place/geometry.py:378`. A synthetic 8 by 2 mm part requested at 90 degrees was placed outside the edge; final validation rejected it.                                                                                                            | Centred and offset bodies at all cardinal rotations, all edges, both sides and allowed overhangs; valid requests pass and invalid ones fail.           |
| P1       | Placement uses a bounding rectangle: `hardware/pnr/pnr/ingest.py:74`, `hardware/pnr/pnr/place/metrics.py:111`, `hardware/pnr/pnr/place/placer.py:560`. A synthetic concave-outline notch was not rejected. This is a documented model limitation.                                                                                                     | Initially refuse unsupported contours; then test true contour/cutout containment before claiming support.                                              |
| P0       | Test registration is incomplete. `tools/check_test_wiring.py:33` scans only `tests/`; independent BUILD inspection found 67 engine test files without test entry points. Five generated SI targets explain the initial heuristic overcount.                                                                                                           | Expand inventory without double counting generated targets or aggregators; register relevant tests and prove CI executes them.                         |

Existing native regression gates include saved-board pad/net comparisons
(`hardware/pnr/regression/native.py:274`) and explicit rejection conditions
(`hardware/pnr/regression/run.py:113`). Preserve and extend them. Existing electrical reporting
explicitly withholds qualification (`hardware/pnr/pnr/electrical_audit.py:135`); software closure
must not imply electrical qualification. Before implementing a listed fix, confirm it still exists
and check upstream work to avoid duplicating a fix already available.

## Upstream intake

Track intake as YAP-002 in the [job queue](../jobs/TODO.md); start from the pinned
[branch triage](UPSTREAM-TRIAGE-2026-10-06.md). Branch counts are an inventory, not a count of
independent changes or reviewed fixes. Snapshot upstream refs, pull-request heads and commit hashes;
construct their dependency graph and retain the retrieval date. Compare merge ancestry and patch
equivalence against the fork base. Review each distinct change stack once, including dependent
commits. Inspect lv2 placement, numerical and gloss fixes before duplicating that work. Treat
default-setting changes as behavior changes requiring full A/B testing. Separate publication
branches such as `gh-pages` from engine changes. Do not claim all branches reviewed from a
first-pass triage. For each candidate stack, record purpose, dependencies, conflicts, relevant
tests, expected behavior/performance effects and an accept/defer/reject reason. Obtain adversarial
plan review before any cherry-pick, addition or integration change. No bulk cherry-pick of branch
heads. Use an isolated integration branch, preserve authorship/license/provenance, and test the
combined stack against the pinned baseline. Require a fresh adversarial final review before merge. A
clean cherry-pick or upstream passing status is not fork acceptance evidence.

## Bounded work packages

Assign permanent IDs in the job queue before execution; package names below are work descriptions.
Every package needs a proposed-plan review before any change, including tests, dependencies,
documentation or performance-affecting experiments. The reviewer must challenge assumptions, input
preservation, failure modes, test sufficiency, scope and resource cost before work starts. Any
material plan change returns to that gate. No author approves their own proposal or final result.
Preparing proposals and review/task records is allowed under the preparation exception in AGENTS.md;
implementing them needs approval.

| Package                         | Deliverable                                                                                        | Exit evidence                                                                                        |
| ------------------------------- | -------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| Reproducible baseline           | Supported pinned runtime, fixture manifest, executable entry point and baseline measurements       | Clean build and declared tests; exact versions/flags; failures retained and explained.               |
| Preservation                    | Locks, complete sidecars, final cold DRC and saved-output identity checks                          | Negative-control fixtures fail; corrected cases pass through real native integration.                |
| Geometry                        | Edge rotation correction, unsupported-contour refusal, tested contour support if separately scoped | Rotated/offset/mirrored/concave fixtures and independent saved-output geometry measurements.         |
| Placement profile               | Explicit grids, rotations, groups, spacing and lane contract                                       | Hard gates tested at boundary values; preferences measured; infeasible inputs rejected.              |
| Candidate assessment            | Deterministic manifest, previews, measurements and mechanical selection                            | An independent checker reproduces selection and rejects a deliberately corrupted candidate.          |
| Routing and quality experiments | Evidence comparing approved variants under equal budgets                                           | Registered hypotheses, complete results including failures, correctness and performance comparisons. |

Separate ownership across placement, preservation, runtime and assessment where tasks are
independent; one writer per shared file area. Each worker is time-bounded and records evidence in
the job/diary protocol. Every subprocess has a timeout. Long-running work proceeds as a sequence of
reviewed jobs, not an unlimited self-directed runner. Before an overnight batch, set wall-time,
CPU/GPU/RAM/disk, concurrency, candidate and retry limits, checkpoints and stop conditions. Reserve
resources for validation and independent review. Stop on preservation failure, exhausted budget,
unavailable reviewer or unresolved gate failure. Checkpoints contain current revision, artifacts,
commands, completed checks, open risks and next authorized action; another agent must be able to
resume without guessing. No silent restart of stalled workers, hidden fallback or retry loop. A
repeated failed gate stops the job for diagnosis. Fresh adversarial final review must inspect the
diff and independently verify load-bearing results before completion or merge.

## Regression and performance experiment design

Use public synthetic fixtures only in the fork. Cover simple boards, dense groups, locked
connectors, both sides, offset courtyards, rotation, concave contours/cutouts, keepouts and custom
rules. Include infeasible inputs and intentional violations so the checker itself can fail. Retain
expected outcomes and review changes to them. Add regression tests beside each behavior change and
wire them into CI; mocks prove orchestration, not native KiCad enforcement. Run the project's
required build/lint/test gates in its supported environment. A selected test subset does not replace
those gates. Use native KiCad saved-board tests for rule enforcement, geometry export, identity and
final acceptance; record executable versions. Declare baseline, variants, seeds, fixtures, budgets,
success thresholds and stop rules before running experiments. Separate baseline defaults from each
variant's initial-pool/compact/gloss settings. Follow the optional-behavior, correctness-fix and A/B
rules in AGENTS.md. Hold input constraints and total compute budget constant; report per-candidate
cost and total search cost, including rejected candidates and retries. Measure valid-completion
rate, all hard-gate failures, neatness metrics, route quality, wall time, peak memory and timeout
rate. Report per-fixture results plus median/tail summaries across fixed seeds; do not publish only
the winning seed or completed runs. Performance acceptance requires no correctness regression and
compliance with the numerical budget/threshold approved in that job's plan. Keep reproducible
sanitized summaries public. Keep private/local run artifacts out of the repository; preserve
evidence under the approved retention policy.

## Go/no-go criteria

Go to implementation only with a reviewed bounded job, understood input contract, baseline evidence
and executable pass/fail checks. Go to candidate trials only after preservation regressions and
native negative controls pass in a supported pinned environment. Go to trusted placement output only
when every applicable profile and preservation gate passes on the exact exported candidate. Go to
routed software acceptance only after complete cold native DRC, saved-board connectivity/identity
checks and fresh independent review pass. Do not merge for prettier images alone, faster incomplete
runs, mocked DRC success, old demonstrations or an author's own approval. No-go on missing evidence,
unsupported geometry, changed electrical intent, unreviewed dependencies, unresolved failures or
exceeded resource limits.

## Validation limits

The initial review parsed 891 tracked Python files and ran 67 selected existing tests successfully;
it was not a full-suite run. That review used dependencies outside the project's pinned environment.
Bazel, native DRC, complete routing benchmarks and rendered-board review were not run. Historic
demonstrations used an older revision and opt-in settings; they do not establish current default
behavior or performance. A passing software candidate establishes only the checked software
properties. It does not prove component suitability, signal/power integrity, thermal behavior, EMC,
manufacturing yield or physical fit. Electrical analysis, mechanical review, fabrication review and
prototype measurements require separately scoped evidence before a real design is called qualified.
