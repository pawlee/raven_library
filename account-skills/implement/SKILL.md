---
name: implement
description: Implement an approved Raven issue using the matching repository's code map, glossary, conventions, and test guidance.
disable-model-invocation: true
---

# Implement a Raven issue

Implement the work described by the Raven issue ID supplied by the user. Do
not assume a framework, build command, issue file path, or repository from a
previous session.

## Resolve and load context

1. Read `rvn vault list --json`. Resolve the issue in the selected shared vault
   using an explicit `--vault` or `--vault-path`.
2. Read the issue and follow its `repository`, `parent`, and `blocked_by`
   references. If the repository is not the current Git root/remote, stop and
   ask rather than editing the wrong checkout.
3. Read the matching repository's code map, glossary, conventions, architecture,
   and decisions. Use the map to find likely entry points; verify them against
   the current code. If commands or paths are stale, inspect the repository
   and correct the map only with user approval.
4. If the issue is not `ready`, or a blocker is unresolved, report that before
   starting. Set status to `in_progress` when implementation begins.

## Plan and implement

- Confirm the intended interface and important observable behaviors before
  coding. Use [TDD](../tdd/SKILL.md) where practical, at the agreed seams.
- Follow the repository's Raven conventions and accepted decisions. Use
  [Codebase Design](../codebase-design/SKILL.md) when designing or deepening a
  module.
- Implement one vertical behavior at a time. Run the relevant focused tests,
  type checks, and broader suite using commands verified for this repository's
  code map or actual project configuration. Do not copy gym-app or Flutter
  commands into another codebase.
- Keep scope to the issue. If it needs a different interface, larger scope, or
  a new decision, stop and discuss it before proceeding.

## Finish

- Check every acceptance criterion and report the commands/results that verify
  it. If work cannot proceed, set status to `blocked` and record why. Mark the
  issue `done` only when its approved behavior is implemented and verified.
- Add a concise `## Resolution` section to the issue body in Raven, including
  the behavior delivered and verification results. Include a commit hash only
  if a commit exists. Before writing, read [the shared Raven write
  recipe](../shared/RAVEN-WRITE-RECIPE.md) and inspect the existing issue body.
  If using `rvn write`, include the complete updated body so the issue history,
  acceptance criteria, and links are preserved; never replace the issue with
  only the resolution text. If using the section fallback, check whether the
  section already exists before creating or appending to it.
- Update a code map, glossary, convention, or decision document only when the
  work reveals a durable fact; show proposed knowledge changes to the user
  before writing them.
- Do not create local PRD/issue/context files. Do not commit or tag unless the
  user explicitly asks.
