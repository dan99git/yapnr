# Job, diary and TODO protocol

These records coordinate work under [AGENTS.md](../AGENTS.md). The coordinator owns shared records.
Workers submit evidence and handoffs; they do not concurrently edit the shared TODO, diary or status
board.

## Files

| File                                               | Purpose                                                          |
| -------------------------------------------------- | ---------------------------------------------------------------- |
| `jobs/TODO.md`                                     | Canonical prioritized queue and current job states               |
| `jobs/YYYY-MM-DD/JOB-ID/JOB.md`                    | Job opened on that date: scope, plan, evidence and decisions     |
| `jobs/YYYY-MM-DD/JOB-ID/reviews/REVISION-plan.md`  | Pre-change adversarial review                                    |
| `jobs/YYYY-MM-DD/JOB-ID/reviews/REVISION-final.md` | Independent review of the exact result                           |
| `jobs/YYYY-MM-DD/DIARY.md`                         | Append-only curated daily progress across jobs                   |
| `WORKLOG.md`                                       | Short current status, next actions and blockers; not a diary     |
| `.yapnr/`                                          | Ignored local raw output, private fixtures and intermediate runs |

Dates use UTC. A job keeps its opening-date directory across days; each new day's diary links back
to it. Never rename a job merely because a session ended. Local raw artifacts must respect the
active workspace boundary. Public evidence references use relative paths, content hashes, public CI
links or sanitized summaries, not private machine paths. Existing upstream worklog entries are
historical context; update only the fork's current status section as work progresses.

## Job template

```markdown
# JOB-ID: title

- Status: proposed
- Priority: P0 / P1 / P2
- Owner / coordinator:
- Implementer:
- Plan reviewer:
- Final reviewer:
- Opened / last updated (UTC):
- Issue / PR: none yet, or verified links
- Baseline commit / branch:
- Dependencies and upstream source commits:
- Approved owner scope:

## Outcome and acceptance

State observable pass/fail criteria, including preservation and performance limits.

## Evidence and proposal

Read source/tests/config; distinguish observed facts from hypotheses. List intended files,
alternatives, risks and out-of-scope work. Record proposal revision/digest, tests, runtime/toolchain
and resource envelope.

## Reviews

Link plan approval and later final approval, each bound to an exact artifact. List findings,
severity, disposition and evidence resolving every blocker.

## Execution and checks

Record commands, versions, input hashes, flags/seeds, outputs, exit codes, measured results and
skipped checks with reasons. Link private artifacts by hash.

## Handoff / closure

What is complete, what remains, active processes owned by this job, precise resume action,
limitations and final acceptance verdict. Link the latest daily diary.
```

Creating the proposal and its review records is preparation; it does not require its own infinitely
recursive pre-review. Implementing the proposed change does.

## States and transitions

`proposed -> plan-review -> approved -> implementing -> validating -> final-review -> done`

Use `changes-required` for rejected proposals/results, `blocked` for an unmet dependency or required
capability, and `paused` for an owner-directed pause. Record the prior state and exact resume
condition. A reviewer cannot expand user authority. A material proposal/baseline change returns to
`plan-review`. Any change to a reviewed artifact returns to validation and final review. `done` requires
both review records and all acceptance evidence; it never means merely that a worker stopped
responding.

`queued` is used only for TODO items that do not yet have a job record. Claim an item by creating
its job record and changing its TODO state to `proposed`; prevent duplicate claims before assigning
workers. Preserve completed/rejected history rather than deleting it. New findings become linked
TODO items with evidence and dependencies.

## Review record

Each review records reviewer identity, independence from authorship, time, baseline, proposal
revision or candidate commit/diff digest, scope read, checks independently run, findings,
dispositions, verdict and remaining uncertainty. Before execution, the implementer confirms the
approval matches the current proposal. Before acceptance, the coordinator confirms the final
approval matches the current candidate.

A passing review explains which failure conditions were challenged. Empty praise, an unsupported
`LGTM`, or a report based solely on the author's summary is not approval. Record disagreement and
counter-evidence. A third reviewer resolves material disputes; the coordinator cannot silently waive
a correctness blocker.

## Daily diary

Append at meaningful stage boundaries, decisions, failures, review outcomes and handoffs. Correct
old entries with a dated correction, preserving the original.

```markdown
## HH:MM UTC - JOB-ID - stage

- did: concrete action/result
- decision + why: evidence-based choice
- dead end: failure and diagnosed cause, if any
- assumption: unverified premise, if any
- left: next action / blocker / owned running process and deadline
- verified: command/check, result and evidence reference
```

Do not paste terminal logs or chat transcripts. A sanitized exact error or aggregate test result is
useful evidence; private payloads are not. At session end, reconcile TODO, job state, diary and the
short WORKLOG status so the next agent can resume.

## TODO rows

Each row has a stable ID, priority, job/state link, concise outcome, dependencies and the next
concrete action. Put acceptance criteria in the linked job/brief rather than marking an entire theme
complete after a small patch. Reprioritization needs a recorded reason. Never invent GitHub issue/PR
numbers; add real links after they exist.

## Continuous-work checklist

1. Load rules, brief, TODO, current job and latest diary.
2. Confirm authorization, ownership, toolchain, baseline, budgets and review validity.
3. Select the highest-priority unblocked job; dispatch independent workers by scope.
4. Obtain pre-change adversarial approval before implementation or heavy experiments.
5. Execute bounded work, checkpoint evidence, validate and obtain independent final review.
6. Reconcile records, close the accepted job or record its exact blocker and resume step.
7. Continue while the actual runtime and allocated resources permit. State truthfully when no
   process or supervisor is running.
