# Fork improvement queue

Baseline reviewed: `d030a752ccc548dd438f05c29d313a88a9c18ced`. Follow
[the record protocol](README.md) and [the brief](../docs/FORK-IMPROVEMENT-BRIEF.md). Queued work is
a plan, not evidence of implementation or a running background team.

| ID                                   | Priority | State        | Outcome                                                           | Dependencies                       | Next action                                                                   |
| ------------------------------------ | -------- | ------------ | ----------------------------------------------------------------- | ---------------------------------- | ----------------------------------------------------------------------------- |
| [DOC-001](2026-10-06/DOC-001/JOB.md) | P0       | final-review | Agent protocol and scoped improvement brief                       | None                               | Review applied candidate; publish draft PR; await required CI                 |
| YAP-001                              | P0       | queued       | Reproducible pinned local build and baseline                      | DOC-001                            | Open job; inspect container/runtime locks and bounded native smoke test       |
| YAP-002                              | P0       | queued       | Review upstream branches and select justified imports             | DOC-001                            | Pin refs, map dependencies and patch equivalence; review relevant lv2 changes |
| YAP-003                              | P0       | queued       | Preserve source locks on every placement path                     | YAP-001, YAP-002                   | Reproduce lock drift with a preservation control in pinned runtime            |
| YAP-004                              | P0       | queued       | Preserve complete custom-rule sidecars through snapshots          | YAP-001, YAP-002                   | Prove original custom rule still fails deliberately invalid native candidate  |
| YAP-005                              | P1       | queued       | Resolve rotated edge-fixed geometry correctly                     | YAP-001, YAP-002                   | Add controlled tests across rotations and offset/centred bodies               |
| YAP-006                              | P0       | queued       | Fresh final native validation after the last modification         | YAP-004                            | Audit final flag, caches, saved fills and post-check mutations                |
| YAP-007                              | P0       | queued       | Relevant engine tests are genuinely included in required checks   | YAP-001                            | Inventory real/generated/imported tests; separate historical quarantine debt  |
| YAP-008                              | P1       | queued       | Correct placement containment for supported outlines              | YAP-003, YAP-005                   | Reject unsupported contours; define concave/notch/rounded-board cases         |
| YAP-009                              | P1       | queued       | Enforced project neatness and routing-lane profile                | YAP-003, YAP-008                   | Define independent grid, alignment, spacing and free-lane measurements        |
| YAP-010                              | P1       | queued       | Repeatable candidate-generation command and preview output        | YAP-001, YAP-004                   | Specify copy-in/candidate-out contract and smoke-test it                      |
| YAP-011                              | P1       | queued       | Representative comparison with current placement/routing workflow | YAP-006, YAP-007, YAP-009, YAP-010 | Freeze authorized fixtures, metrics, seeds and run limits                     |
| YAP-012                              | P2       | queued       | Measured performance and reliability improvements                 | YAP-011                            | Profile accepted workflow; propose one measured bottleneck at a time          |

Create a dated job record before claiming any queued item. Acceptance requires both review gates and
the evidence defined by that job. Do not merge imports merely because upstream is ahead, or close
the queue because one demonstration looks neat.
