# Upstream branch triage

Snapshot: 2026-10-06, inventory captured 12:31:05 UTC after `git fetch origin`. Source:
[Studio-Fug/yapnr](https://github.com/Studio-Fug/yapnr) Main:
`d030a752ccc548dd438f05c29d313a88a9c18ced`. 17 total branches: main plus 16 inventoried below. Eight
open PRs: 71, 70, 66, 64, 62, 61, 59, 51. Scope: first-pass inventory, ancestry/patch-equivalence
and relevant descriptions/diffs. No checkout, merge, cherry-pick, push, package install, branch-code
execution or PCB edits.

## Recommendation

Review the small reusable fixes, then the placement changes, then their integrated defaults branch.
Do not cherry-pick all branches: several are cumulative stacks, one is an already-merged bootstrap,
and one contains deployed documentation. No branch is approved for adoption by this triage.

## Exact branch inventory

Ahead/behind counts compare commit ancestry with the pinned main. Files count the merge-base-to-head
diff, not the whole branch tree. Patch `+/-` counts are `git cherry` unique/equivalent non-merge
commits against main; zero equivalence does not mean a squashed PR is absent.

| Branch                  | Head SHA                                   |      Ahead/behind | Files | Patch +/- | First-pass disposition                                                    |
| ----------------------- | ------------------------------------------ | ----------------: | ----: | --------: | ------------------------------------------------------------------------- |
| claude/lv2-ab           | `770eb3d03987c2a037e2dab85db253ab1402bc56` |               3/0 |     4 |       2/0 | PR 71, experiment runner flags; feature commits already in defaults stack |
| claude/lv2-anim         | `6d4e7c98b63a51abfbc2c79fc2309778700e96ac` |               1/9 |     4 |       1/0 | PR 59, CI artifact completeness check                                     |
| claude/lv2-compact      | `4225d27cac3ec6903936fe67aa03e5dabd122f16` |               4/5 |    18 |       4/0 | PR 66, compact convergence/pair support                                   |
| claude/lv2-defaults     | `405df583c1897ea51f617e5696952a31a3237fe2` |              26/2 |    66 |      20/0 | PR 70, integrated stack and default changes; review last                  |
| claude/lv2-gloss        | `0151f415277f6833860f40f2a60e17f4fb8a916e` |               2/6 |     4 |       2/0 | PR 62, deterministic segment merge plus experiment regex                  |
| claude/lv2-legal        | `7ab6fbd5b1cb71f97d6bebcf0eed5acb6c7af696` |               5/5 |    28 |       5/0 | PR 64, most relevant to unnecessary placement movement                    |
| claude/lv2-plane        | `3bd03471b95331f1811970ef6e7511de8ea9bda6` |               1/9 |     7 |       1/0 | PR 61, platform-independent placement arithmetic                          |
| claude/pnr-explore-gcp  | `4f9d49dfe44ab975dc2f92ad127a8023e51beb0e` |             119/9 |   129 |     106/0 | Large cumulative experiment/cloud/radar work; defer whole branch          |
| claude/pr0-bootstrap    | `d964de6f12c9571f8cb67e05a93634d131a4b381` |            14/213 |    58 |      14/0 | Stale bootstrap; PR 1 already merged by squash                            |
| claude/radar60-3c       | `04443f5d6ec2ece658623c27042d220953160220` |             126/9 |   128 |     108/2 | Cumulative radar integration; contains 3c-power                           |
| claude/radar60-3c-power | `af876ccc4319ff96bc3d593c0c862c4a98ad9de9` |             124/9 |   128 |     107/2 | Radar power-block integration; duplicated generic fixes present           |
| claude/radar60-3d       | `16a5450fc67aca8f5b259c1cf894a1a45ac0899a` |             151/5 |   148 |     130/0 | Integration stack containing 3c, power, rf3b and explore                  |
| claude/radar60-models   | `d6ad8080f28799b3c5ec06aeb229a0789abfac75` |             152/5 |   154 |     131/0 | 3d plus one model-integration commit; no generic placement fix indicated  |
| claude/radar60-rf3b     | `727df415ea51d19d68e11604efc566b14243b05b` |              37/9 |   125 |      35/0 | Radar RF macro/coupon/EM work; defer unless using radar example           |
| claude/rf-multistart    | `e2b9fe7adb57214774b5bcb4714efcfc2acc6647` |              3/15 |     8 |       3/0 | PR 51, RF optimization infrastructure, separate from PCB placement        |
| gh-pages                | `89de391984627dd3f3f150ce72930ca036515005` | unrelated history |   n/a |       n/a | Rendered docs, previews and downloads; never pick into engine             |

`gh-pages` has no merge base with main; its head says it deploys the pinned main SHA and its tree
contains HTML/.doctrees/downloads. Its commits are publication history, not missing engine
development. [PR 1](https://github.com/Studio-Fug/yapnr/pull/1) merged 2026-09-30 as
`2d2c0695f3f34a13a343c39f6890f5d9212eb816`. The retained bootstrap branch is not 14 missing
features.

## Dependencies and duplicated work

- `lv2-defaults` contains the exact heads of `lv2-anim`, `lv2-plane`, `lv2-gloss`, `lv2-legal` and
  `lv2-compact`.
- It also contains both feature commits from `lv2-ab`. The latest `lv2-ab` head is not an ancestor
  only because it subsequently merged two newer main commits; `git cherry defaults ab` lists only
  those main commits (`8ade57de...`, `d030a752...`). Do not count that as another feature
  dependency.
- `lv2-gloss` and `lv2-ab` both widen the ladder-case regex. Their PR text calls this identical, but
  the source differs: gloss allows an uppercase first character (`[A-Za-z0-9]`); ab retains
  lowercase first character (`[a-z0-9]`). Both allow uppercase suffixes. This overlap needs
  deliberate resolution, not a blind duplicate pick.
- `radar60-models` is exactly `radar60-3d` plus commit `d6ad8080...`, whose 11-file delta mostly
  adds/integrates STEP models and example footprints.
- `radar60-3d` contains all of `pnr-explore-gcp`, `radar60-3c`, `radar60-3c-power` and
  `radar60-rf3b`.
- `radar60-3c` contains `radar60-3c-power`; their remaining tree delta is seven radar/example-test
  files, not a separate generic placer.
- On both 3c branches, commits `09b239ed...` (outer pour pieces) and `434322db...` (cloned zone
  connection/fill settings) are patch-equivalent to main. Those fixes must not be duplicated. Other
  radar branches share history/merges even where `git cherry` reports no equivalent individual
  commits.

## Proposed review batches

### Batch 1: bounded reusable fixes

1. [PR 62](https://github.com/Studio-Fug/yapnr/pull/62), `945e400e...`: deterministic gloss merge
   direction. Four branch files; the core fix is `hardware/pnr/pnr/gloss_geometry.py` with a direct
   geometry regression test. Review root fix separately from overlapping experiment regex.
2. [PR 59](https://github.com/Studio-Fug/yapnr/pull/59), `6d4e7c98...`: include `design.json` in CI
   artifacts and validate required files. Four files across workflow, CI helper and tests. This
   improves evidence reliability; it does not improve placement geometry.
3. [PR 61](https://github.com/Studio-Fug/yapnr/pull/61), `3bd03471...`: portable exp/log/Adam and
   exact quarter-turn constants. Seven files including `portable_math.py`, model wiring and golden
   tests. Dedicated numerical review required because it changes all placements, and the reported
   cross-platform evidence is arm64, not Windows x86-64 proof.

### Batch 2: layout quality and routing correctness

1. [PR 64](https://github.com/Studio-Fug/yapnr/pull/64), all five commits: preserve clean
   placements, push mild overlaps within a bounded reach, relocate severe cases, measure motion and
   reserve escape-channel room before legalization. Review `keep.py`, `motion.py`, `legalize.py`,
   `compact.py`, `placer.py`, reorientation and tests together. This directly addresses
   untidy/unnecessary repositioning.
2. [PR 66](https://github.com/Studio-Fug/yapnr/pull/66), four commits: compact relaxation after
   incomplete routes or failed matching, series-part rotation, cache-key coverage and power-first
   integration. Review `route/feedback.py`, compact flags, matched placement, power-first and
   cache-key tests together. Changing convergence criteria is more important than cosmetic animation
   improvements.

PR 64 changes behaviour by default. Its description reports mixed regression results (84/92 versus
86/92 with compact; 61/64 versus 62/64 without). The newest defaults commit explicitly acknowledges
that the dedicated legalize-keep A/B predates the latest GP-channel fix and mixed machine types.
Treat improvement as a candidate to measure, not established across our boards.

### Batch 3: integrated defaults after batch 1/2 review

[PR 71](https://github.com/Studio-Fug/yapnr/pull/71) supplies four runner/experiment-file changes
for power-first and coupled-pair A/Bs. [PR 70](https://github.com/Studio-Fug/yapnr/pull/70)
integrates those changes with all prior fixes, updates CI/docs/animations and enables compact,
gloss, initial pool, coupled diff-pair routing and legalize-keep defaults.

Use the integrated head as a comparison candidate only after reviewing its constituent changes. Do
not add it on top of the same constituent picks without accounting for duplicate history and
dependency order.

**Live correction:** PR 70's title/body still says `jlc-pofv` becomes default. At pinned head
`405df583...`, `hardware/pnr/regression/run.py` sets `DEFAULT_FAB_PROFILE = "legacy"`. The latest
commit reverted that default after 11 manual-only hard rungs failed under jlc-pofv, added a
legalize-keep off switch and corrected stale A/B claims. An omitted flag in an old experiment now
inherits the new enabled default; it does not retain the old off arm. Source outranks the stale PR
description.

### Batch 4: optional, separate scope

- [PR 51](https://github.com/Studio-Fug/yapnr/pull/51): RF multi-start schema, perturbation wiring
  and pause/resume primitive. Its description explicitly excludes the complete
  execution/selection/CLI system. It is not a PCB placement fix.
- Radar/explore stack: inspect only if adopting those example circuits or cloud campaigns. Extract
  narrowly demonstrated generic deltas if any remain beyond main. Do not import 119-152 cumulative
  commits for three placement findings.
- Exclude `gh-pages` and retained bootstrap from engine adoption review.

## Overlap with our placement findings

Compared the seven relevant source-file blobs/diffs across all 14 active non-artifact, non-bootstrap
branches.

- **Native lock drift:** no branch changes the fixed-mask logic to universally honour
  `Component.locked`; `initial_pool.py` is unchanged. Defaults enabling initial-pool covers the
  regression runner path through the existing preservation helper, but direct `place()` still lacks
  universal lock preservation. Do not close the finding from PR 70's default flip.
- **Rotated edge-fixed connector:** `place/geometry.py` is byte-identical to main on all 14
  branches. None of these fixes changes the faulty size-before-rotation resolver.
- **Board contour limitation:** `place/metrics.py` and `ingest.py` are byte-identical to main on all
  14 branches. The inspected placer diffs retain rectangular outline stamping. No contour-aware
  placement fix identified.
- PR 64 addresses motion/neatness and PR 66 addresses matched-routing convergence. These are
  relevant additional improvements, not fixes for the three findings above.

These conclusions are source comparisons, not test runs on branch heads.

## Live CI evidence at snapshot

Checks came from GitHub `statusCheckRollup` for each exact PR head. No local branch tests were run
here.

| PR  | CI test     | macOS test  | run the ladder | animations |
| --- | ----------- | ----------- | -------------- | ---------- |
| 71  | In progress | In progress | Success        | Skipped    |
| 70  | In progress | In progress | Success        | Skipped    |
| 66  | Success     | Success     | Success        | Skipped    |
| 64  | Success     | Success     | Success        | Skipped    |
| 62  | In progress | Success     | Success        | Skipped    |
| 61  | In progress | Success     | Success        | Skipped    |
| 59  | Success     | In progress | Success        | Skipped    |
| 51  | Success     | Success     | Skipped        | Skipped    |

Do not call skipped animation jobs verified, or PR 70/71 wholly green. The snapshot includes reruns,
so descriptions may report earlier successful runs while current checks are pending. The detailed
inventory was retained as local review evidence; refresh live PR checks before adoption. This public
snapshot is not a claim that those checks are still current.

## Limits and completion checks

Inspected all branch heads, changed-file lists, unique/equivalent commit counts and full pairwise
head ancestry. Read all eight PR descriptions, selected high-relevance source diffs, latest defaults
correction, publication tree and merged bootstrap metadata. Did not audit all code in the large
radar stacks or rerun their tests. This is a review queue with dependencies, not a merge
recommendation.

## DIARY

- did: Fetched refs; inventoried all 16 non-main branches and eight open PRs; compared ancestry,
  patch equivalence, changed files and placement-audit overlap.
- decision + why: Review reusable fixes, then placement/convergence changes, then integrated
  defaults; avoid duplicate cumulative imports.
- assumption: Counts and CI state belong to the pinned snapshot; upstream is actively changing.
- left: Full code review and controlled validation before any adoption.
- verified: Exact SHAs/counts from git; PR/CI state from live GitHub; no working-tree checkout or
  source changes.
