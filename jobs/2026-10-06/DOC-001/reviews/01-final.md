# DOC-001: independent final documentation review

Verdict: **approved for documentation-only draft publication** of the exact candidate below.
This is not merge approval or final integration acceptance. Required CI remains pending.

- Reviewer: Codex `protocol_final_review`, separate from the proposal authors and implementer.
- Independence: I did not prepare, apply or modify the repository documents reviewed here.
- Baseline: `d030a752ccc548dd438f05c29d313a88a9c18ced`.
- Working branch: `docs/fork-agent-protocol`.
- Review completed: 2026-10-06 12:55 UTC.

## Scope and challenges

Read all ten changed documents in full, the three existing-file diffs, the pre-change approval,
formatter/lint configuration and the validation helper. Inspected the prior source-review record
and targeted source sections for locks, phase capture, cold final DRC, edge rotation and test
wiring. Independently checked pinned branch ancestry and the integrated fabrication default.

Challenged review bypasses, conflicting publication rules, self-approval, stale resume instructions,
queue dependencies, hidden runtime claims, privacy, input preservation and cherry-pick duplication.
The rules require independent review before implementation and again before acceptance, bind both
reviews to exact artifacts, and invalidate approvals after material changes. Record preparation is
explicitly separated from implementation. Runtime, budget, ownership and stop conditions are stated.

The brief preserves electrical/mechanical requirements ahead of appearance and speed; defines
copy-in/candidate-out operation, negative controls and saved-output checks; distinguishes placement
from routed acceptance; and identifies unsupported native/runtime/performance evidence. Queued
engineering work and branch triage are not represented as implemented fixes or approved imports.

## Findings and dispositions

1. Low, resolved: the applied job's local-link count still said 34. The coordinator corrected the
   current count to 35; I rechecked that change and reran its format/lint/privacy checks.
   Historical diary and pre-change counts remain historical observations.
2. Low, resolved: the earlier job handoff directed a reader back to plan approval/application.
   The reviewed candidate now directs independent candidate review, draft publication and CI.
3. Medium, deferred before merge: `docs/build_docs.py` stages root documents and `docs/**/*.md`,
   but not `jobs/`. Repository/GitHub file links pass; generated-site job links are unsupported by
   that staging code. `docs/_sphinx/conf.py` suppresses missing MyST cross-references, so green
   docs CI alone would not establish these links work. No full site build was run. The draft PR
   must disclose this limit. Before merge, the owner must explicitly accept repository/GitHub-only
   operating records or authorize a separately reviewed site-link solution. No build edit is
   approved here.

No unresolved finding blocks the narrowly scoped draft publication. This verdict does not clear
the deferred site decision or unavailable full-suite gates for integration.

## Independent checks run

All commands were bounded. Tool cache and temporary output stayed in the authorized local review
area. npm ran offline with lifecycle scripts disabled and the existing cache; no global installs,
application dependencies, lockfiles or repository source were changed by this review.

- SHA-256 assertions: all ten manifest entries matched before and after checks.
- Git baseline and branch assertions: matched the pinned base and documented working branch.
- Changed/untracked-file set assertion: exactly the ten listed documentation files.
- `git diff --check`: exit 0; Git emitted checkout LF/CRLF warnings, not whitespace errors.
- `npm exec --offline --yes --ignore-scripts --package=prettier@3.1.0 -- prettier --check`
  with the ten explicit candidate paths: exit 0, all matched files use Prettier style.
- `npm exec --offline --yes --ignore-scripts --package=markdownlint-cli@0.38.0 -- markdownlint`
  with the ten explicit candidate paths: exit 0, no findings.
- `python -B tools/privacy_scan.py` with the ten explicit candidate paths: exit 0, no findings.
- Local Markdown destination assertions: 35 existing file destinations. This does not validate
  fragment anchors, external URLs or generated-site output.
- WORKLOG byte assertion: removing only the new fork-status insertion exactly restores the
  committed original blob. The original upstream text is preserved.
- Pinned branch assertions: all 16 inventory head SHAs and all 15 comparable ahead/behind counts
  matched fetched refs; the five stated feature heads are ancestors of the defaults head.
- Source assertion at the pinned defaults head: `DEFAULT_FAB_PROFILE = "legacy"` confirmed.
- After the link-count correction: the changed job's formatter, Markdown lint and privacy checks
  again exited 0; all ten latest candidate hashes matched.

Bazel and prek executable discovery returned unavailable. `bazel test //...` and
`prek run --all-files` were not run. Full CI, Sphinx output, native KiCad, routing benchmarks and
runtime performance were not validated. Earlier selected-test and reproduction results in the
brief are prior-review evidence; this documentation review did not rerun them. Branch PR/CI status
remains a dated snapshot and was not refreshed remotely here.

## Exact candidate SHA-256 manifest

The base plus these ten complete-file digests identifies the reviewed candidate. No engine,
configuration, dependency, PCB or test implementation file is included.

| File                                         | SHA-256                                                            |
| -------------------------------------------- | ------------------------------------------------------------------ |
| `AGENTS.md`                                  | `a9857043e715b3ed351bdb38e762693b0bbff0de9e9cd66756b1a1d37c086a3e` |
| `CONTRIBUTING.md`                            | `dca7860256938069244ca3cd5d3bdf62906022874ff64ee1bbc75140fbae3ac5` |
| `WORKLOG.md`                                 | `1d0d5bedf8dcf4ecd4bf8ad604e76ba2df1fcc217c9124b2be3495204469c12e` |
| `jobs/README.md`                             | `aa0adc189a17d89f4d9922458f3e90121a72bd7d99f162a8732e9d84022de6fd` |
| `jobs/TODO.md`                               | `d69d249caa40051eff8c4336f88e21eafe5484003adf9e82cef8700c89c25930` |
| `docs/FORK-IMPROVEMENT-BRIEF.md`             | `fb784646729ea5b34a6526a72dbdf3de019161618910eecf2c407b7cfa2d2c9b` |
| `docs/UPSTREAM-TRIAGE-2026-10-06.md`         | `31aed3a9e0f19f79edb9ab44ed921ca002cca4b5932e083384e2570669830aad` |
| `jobs/2026-10-06/DOC-001/JOB.md`             | `d622b702ed2066c354d0e17777aae4523b45bace345ade74884633a374fbc2b9` |
| `jobs/2026-10-06/DIARY.md`                   | `f6480f0b7d0d3fd78e3b3312ce95f74b4851c4d90cbebbc83b0db061d0c72759` |
| `jobs/2026-10-06/DOC-001/reviews/01-plan.md` | `39f8584ce570a123cfb1ae76d22100180aa3a5dc2350689e5b88682f751acddf` |

## Publication boundary

Publish only to the owner's personal fork as a draft PR, with pending CI and the generated-site
limitation disclosed. No upstream push, merge, cherry-pick, engine change, scheduler, continuous
service or performance campaign is approved. Keep DOC-001 open pending its integration gates.

Promoting this reviewer-authored record to `jobs/2026-10-06/DOC-001/reviews/01-final.md` is within
AGENTS.md's record-preparation exception. Formatting-only changes to this record are permitted
before promotion if its substantive verdict, manifest, findings and limits stay unchanged and
format, lint, privacy, link and exact-scope checks include it. The coordinator must verify those
conditions. This exception does not authorize edits to the ten reviewed documents. Any such edit
needs proportionate re-review and affected checks before publication.

DIARY:

- did: Independently reviewed the ten documentation files, diffs, review evidence and branch claims.
- decision + why: Approved exact documentation-only draft publication; all available checks pass.
- dead end: Full Bazel/prek gates unavailable; generated-site job destinations are not staged.
- left: Promote checked review record, publish draft PR, resolve required CI and premerge site scope.
- verified: Ten digests/scope, 35 file links, WORKLOG preservation, format/lint/privacy/whitespace,
  pinned branch counts/ancestry and actual defaults source.
