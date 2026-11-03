---
name: review-implementation
description: Review an implementation against its Raven issue, repository-specific conventions, tests, and current codebase context.
disable-model-invocation: true
---

# Review a Raven issue's implementation

Review the implementation associated with the issue ID supplied by the user.
Use a stronger review model when available, but never assume the project is a
Flutter app or that a particular command is valid.

## 1. Resolve the issue and repository

- Read `rvn vault list --json` and resolve the issue in the selected shared
  vault. Use an explicit `--vault` or `--vault-path`.
- Follow the issue's `repository` reference. Verify that it matches the current
  Git root/remote; stop and ask if it points elsewhere or is ambiguous.
- Read the issue, parent PRD, and relevant Raven repo-docs: code map, glossary,
  conventions, architecture, and decisions.

## 2. Select the review diff and checks

- If the issue's `## Resolution` identifies a commit, review that commit's
  changes. Otherwise use the user-specified base/commit or the current diff.
  If the target cannot be determined, ask; do not guess from a manually
  numbered filename.
- Use verified build, analyzer, and test commands from the code map. If those
  commands are missing or stale, inspect the repository configuration or ask.
- Run the relevant checks. Report failures as initial findings before the
  detailed review.

## 3. Review checklist

For each changed file, check:

- Compliance with the repository's Raven `conventions` doc.
- Logic errors, edge cases, races, null handling, and incorrect assumptions.
- Test coverage and correctness against the issue acceptance criteria.
- Performance, accessibility, and platform conventions relevant to this repo.
- Scope conformance and unintended behavior changes.

Use concrete `file:line` references. Do not copy generic or gym-specific
conventions into another repository.

## 4. Findings loop

Present findings grouped by category. For each, explain the impact and offer
**fix** or **ignore**. Apply a fix only after the user chooses it, then rerun
the affected checks. Continue until findings are fixed, explicitly ignored, or
the user stops.

## 5. Capture and finish

- If a finding reveals a reusable convention, propose a concise addition to the
  repository's Raven `repo-doc` with `kind=conventions`. Write it only after the
  user approves.
- After the findings are handled, show a proposed `## Review` summary for the
  issue object and update it in Raven after approval.
- Do not create a local issue file, commit, tag, or mark the issue done unless
  the user explicitly asks and the work is actually complete.
