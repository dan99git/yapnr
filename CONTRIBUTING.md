# Contributing to yapnr

## Outside contributions are not accepted yet

yapnr is being migrated out of the Splanc repository and is maintained by its owner (with automated
agents working on the owner's behalf). **Pull requests from outside contributors are not accepted at
this time**, and will be closed without review. Bug reports and questions are welcome as
[GitHub issues](https://github.com/Studio-Fug/yapnr/issues).

This policy will be revisited together with the inbound license terms (for example a contributor
license agreement or a Developer Certificate of Origin sign-off) once the migration is done.

## License

yapnr is licensed under the GNU Affero General Public License, version 3 or (at your option) any
later version (`AGPL-3.0-or-later`); see [LICENSE](LICENSE). Everything committed to this repository
is licensed under the same terms (inbound = outbound).

## How changes are made

- Every change is a pull request against `main`. PRs are squash-merged; the only exceptions are the
  history-import PRs, which are merged with a merge commit so the imported history survives, and the
  engine's mechanical format and move PRs (PR3a, PR3b), whose commits must keep their hashes for
  `.git-blame-ignore-revs`.
- Commit and PR titles follow `<Area>: <summary> (#N)`, where `#N` is a GitHub issue. The body
  explains why, and carries measured results for any engine change.
- New optional engine behavior starts behind a default-off flag with an A/B result. A correctness
  fix restoring an existing contract may become normal behavior after a failing-baseline/passing-fix
  regression, preservation checks and adversarial review. Changed policies/defaults need measured
  A/B evidence; see [AGENTS.md](AGENTS.md).
- Mechanical changes (formatting, renames, moves) stay separate from behaviour changes, as a PR of
  their own. Once it is merged, a follow-up adds its commit on `main` to `.git-blame-ignore-revs`
  (squash merging gives it a new hash).
- Before pushing: `prek run --all-files` and `bazel test //...` (see
  [DEVELOPERS.md](DEVELOPERS.md)). CI must be green: `lint`, `test` and `docs` are required.
- Update `WORKLOG.md` and the documentation pages the change touches.

## Commit identity and privacy

For the `dan99git/yapnr` fork, use the fork owner's authorized public identity or verified GitHub
noreply identity. Attribute agent work to the actual runtime; do not impersonate the upstream owner
or another agent. Use per-command or repository settings, not global identity changes. GitHub
noreply identities are accepted by `tools/privacy_scan.py --identities`. Adding a personal address
to the allowlist remains an owner decision. These fork-local instructions do not change upstream's
contribution or identity policy.

Never commit machine paths, host names, network addresses, personal e-mail addresses, credentials,
logs or conversation transcripts. The allowlisted commit addresses are no exception in file
contents: only the allowlist file may name them. The privacy scan (`tools/privacy_scan.py`) runs as
a pre-commit hook, as a Bazel test over the whole tree, and in CI over the messages and patches of
every new commit, merge commits' diffs included (where the allowlisted commit addresses pass, since
every commit header carries them).

## Reporting security issues

Please do not open a public issue for a security problem; use GitHub's private vulnerability
reporting on the repository instead.

## Fork job and review records

The fork uses [AGENTS.md](AGENTS.md) and [jobs/README.md](jobs/README.md) for independent pre-change
and final adversarial review, job records, daily diaries and TODO ownership. Curated records of
decisions, sanitized commands, aggregate results and evidence references are permitted. Raw run
logs, transcripts and private designs stay out of public commits. `WORKLOG.md` remains a short
current status board. Every fork change still uses a pull request; record an existing issue link or
an explicit local job ID rather than inventing an issue number.

For owner-authorized documentation-only work, a draft PR may be published before unavailable local
full-suite gates run, provided an independent reviewer approves the exact diff and the available
format, privacy, link and structural checks pass. Record every unrun gate in the job and PR. This
exception permits draft publication only: it does not waive required CI, authorize engine/dependency
changes, permit merging, or establish final acceptance. Required CI must pass before the job is
accepted for integration.
