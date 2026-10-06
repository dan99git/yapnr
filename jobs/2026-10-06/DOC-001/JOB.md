# DOC-001: fork operating protocol and improvement brief

- Status: final-review
- Priority: P0
- Owner / coordinator: fork owner / Codex coordinator
- Implementer: Codex coordinator; brief draft contributed by a separate Codex worker
- Plan reviewer: independent Codex reviewer `audit_verifier`
- Final reviewer: fresh independent Codex reviewer `protocol_final_review`
- Opened / last updated (UTC): 2026-10-06
- Issue / PR: none yet; DOC-001 is the local job identifier
- Baseline commit: `d030a752ccc548dd438f05c29d313a88a9c18ced`
- Intended branch: `docs/fork-agent-protocol`
- Dependencies: source review and upstream branch inventory at the pinned base
- Approved owner scope: write agent instructions, job/diary/TODO protocol and an
  improvement brief for this fork; assess upstream branches for possible later intake

## Outcome and acceptance

Provide a usable protocol requiring independent adversarial review before execution
and again before acceptance. Define durable job, diary, TODO and resume records. Define
measurable correctness, preservation, placement-quality and performance objectives.
Keep upstream safeguards and record actual limitations. Include upstream review
dependencies; importing branches is a later reviewed job.

Required documentation checks: exact change scope, valid local links, valid Markdown,
formatting at repository pins, privacy scan, no whitespace errors, and independent
reviews of the proposal and resulting diff. No engine changes or dependency changes.
Full required CI remains a gate before merge; a docs-only draft PR may carry pending
checks under the narrowly documented publication exception.

## Evidence and proposal

Read AGENTS, CONTRIBUTING, WORKLOG, ignore/build/hook configuration and the targeted
source review. The review found source-lock movement, loss of custom rule sidecars,
rotated fixed-edge placement failure and conditional warm final-check use. Native
integration and pinned-runtime performance were not established.

Drafted additions:

- `AGENTS.md`: mandatory plan/final adversarial review, bounded sustained work and records.
- `CONTRIBUTING.md`: matching fork identity, privacy, correctness-fix and draft-PR rules.
- `WORKLOG.md`: short fork status; upstream history preserved.
- `jobs/README.md`, `jobs/TODO.md`: protocol, templates, states and prioritized queue.
- `docs/FORK-IMPROVEMENT-BRIEF.md`: input/output contract and measurable work packages.
- `docs/UPSTREAM-TRIAGE-2026-10-06.md`: pinned first-pass branch dependency inventory.
- This job, daily diary and review records: curated execution evidence.

Out of scope: engine fixes, cherry-picks, default flips, scheduler creation, background
24/7 service, paid campaigns, private-design publication and fabrication activity.

The smaller alternative was a single free-form instruction file. Separate brief and
records were chosen because durable queue ownership and acceptance evidence must
survive handoffs without turning AGENTS into a changing experiment log.

## Reviews

Initial plan review required corrections: resolve full-local-checks-before-push versus
draft-CI workflow, invalidate review for any artifact change, align priorities and
submit actual record/status drafts. These were addressed in the revised proposal.
The pinned formatter plan was independently approved before use. Exact approval and
candidate manifests belong in `reviews/01-plan.md` and `reviews/01-final.md` when issued.

## Execution and checks

Formatter proposal: repository-pinned prettier 3.1.0 and markdownlint-cli 0.38.0,
serial finite-time commands, lifecycle scripts disabled, all cache/temp output in the
authorized local review area. No application dependency or lockfile changes.

Before applying drafts, bind reviewer approval to SHA-256 digests. Archive modified
originals in the permitted private local archive. Apply only approved files, validate
them, obtain final review and publish a docs-only draft PR to the personal fork.
Record actual commands/results below as they occur; never pre-fill passes.

## Handoff

No engine implementation or overnight supervisor is running. Next: independent review
of the applied candidate, draft publication and required CI before integration acceptance.
Read the latest daily diary and `jobs/TODO.md` before resuming. Keep this record open
until its required acceptance/CI gates are resolved.

## Applied candidate evidence

Pre-change approval: [01-plan](reviews/01-plan.md), bound to the nine approved drafts.
Archived and hash-checked three existing local originals before applying the approved files.
No engine, dependency, configuration, PCB or upstream branch changes were made.

Applied-tree checks on ten documentation files, including the plan review:

- `git diff --check`: exit 0. Git emitted checkout line-ending warnings only.
- `prettier@3.1.0 --check` on the exact changed files: exit 0.
- `markdownlint-cli@0.38.0` on the exact changed files: exit 0, no findings.
- `python tools/privacy_scan.py` on the exact changed files: exit 0, no findings.
- Local Markdown file-destination assertions: 35 valid; external links and anchors not checked.
- Exact changed-file scope assertion: ten documentation files, no other edits.

Initial formatting check found WORKLOG checkout CRLF endings. Normalized to repository-configured
LF; removing the new status section reproduces the committed original bytes exactly. No original
upstream prose was changed. Repeated available checks passed after that correction.

`bazel test //...` and `prek run --all-files` were not run: neither executable is available
in this environment. Native KiCad and engine/performance tests are outside this documentation
change. Full required CI remains pending; this job is not accepted for merge. Next: fresh exact
candidate review, then owner-authorized draft publication with these limits.
