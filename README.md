# Raven Library

This repository is the Git-backed home for a shared Raven vault and the source
for account-level OpenCode skills. Project and repository records, people,
documents, and issues live as Markdown in `vault/`; reusable agent workflows
live in `account-skills/`.

`raven_library` is the **vault host**, not a codebase that needs its own
`repository` record. Add records only for the actual repositories whose context
you want agents to use.

## Layout

- `vault/` — Raven configuration, schema, saved queries, and Git-tracked
  knowledge. Raven's `.raven/` index and `.trash/` are ignored; Markdown is the
  source of truth.
- `account-skills/` — customized OpenCode skills, kept in Git as their source
  of truth. This directory is not auto-discovered by OpenCode; copy its skill
  folders into the account-level skills directory as described below.

## Install Raven
https://github.com/aidanlsb/raven

Install with Homebrew:

```powershell
brew tap aidanlsb/tap
brew install aidanlsb/tap/rvn
rvn version
```

Or install with Go:

```powershell
go install github.com/aidanlsb/raven/cmd/rvn@latest
rvn version
```

## Connect Raven to the vault

Raven stores vault registrations in machine-level configuration, not in this
repository. Check before adding a registration:

```powershell
rvn version
rvn vault list --json
```

On this machine the vault is registered as `vault` and is the default. On a new
machine, from this repository's root, register the checked-out vault once:

```powershell
$vaultPath = (Resolve-Path .\vault).Path
rvn vault add raven-library $vaultPath --pin --json
rvn vault list --json
```

`--pin` makes it the machine's default. Do not add a second registration if the
same vault path is already listed.

## Knowledge model

All durable project material remains Markdown in Raven:

- **`repository`** — one record per actual codebase, identified by its local
  `path` and, when available, Git `remote`.
- **`project`** — an initiative that can exist before its Git repository is
  chosen. Its optional `contacts` reference list links to `person` records; its
  optional `repositories` reference list can be populated later.
- **`person`** — a reusable contact record with a name and optional email.
- **`project-doc`** — a Markdown document linked to a project, independent of
  any repository. Its `kind` distinguishes `overview`, `discovery`,
  `decision`, `requirements`, and `runbook`.
- **`meeting`** — one Markdown record per meeting, with optional `date`,
  `attendees`, a `project` link, and `issues` links. Meetings can exist without
  a project.
- **`repo-doc`** — a document tied to a repository. Its `kind` is one of
  `glossary`, `code-map`, `conventions`, `decision`, `architecture`, `runbook`,
  or `prd`. Its required `repository` reference keeps documents scoped to the
  right codebase.
- **`issue`** — a work item with required `name` and `status`, and optional
  `project`, `repository`, `parent`, and `blocked_by` references. Status is
  `draft`, `ready`, `in_progress`, `blocked`, `done`, or `dropped`. Its Markdown
  body holds the description and acceptance criteria.
- Daily notes and the built-in `@todo` trait are for informal capture. Use an
  `issue` when work needs its own status or dependencies; project and repository
  links are optional.

Issue IDs are Raven's canonical object IDs, not manually numbered filenames.
Use the `data.id` returned by `rvn new` or `rvn write` for later references. For
`ref` fields passed through `--field`, use that ID as a plain value, without
`[[...]]`; use wikilinks in Markdown bodies and RQL queries.

### Common commands

These examples use the configured shared vault. If more than one vault is
registered, add `--vault <name>` or `--vault-path <path>` to target it
explicitly.

```powershell
# Browse repositories and their documents
rvn query 'type:repository' --json
rvn query repo-docs --json

# Browse projects and project documents
rvn query 'type:project' --json
rvn query project-docs --json

# Browse all meetings and the project-linked subset
rvn query meetings --json
rvn query project-meetings --json

# Register an actual codebase (not the Raven Library host)
rvn new repository "<repo-name>" --field "path=<git-root>" --field "remote=<git-remote>" --json

# Issue overviews (saved queries in raven.yaml)
rvn query all-issues --json
rvn query open-issues --json
rvn query ready-issues --json
rvn query blocked-issues --json

# Filter to one repository; replace the ID with its canonical Raven ID
rvn query 'type:issue .repository==[[repositories/<repo-id>]] sort(.status) sort(.name)' --json

# Write a complete code map or glossary body from a UTF-8 temp file
rvn write repo-doc "Code map: <repo-name>" --field kind=code-map --field "repository=<canonical-repo-id>" --content-file "<temp-file>" --json
rvn write repo-doc "Glossary: <repo-name>" --field kind=glossary --field "repository=<canonical-repo-id>" --content-file "<temp-file>" --json

# Write a complete issue body; optionally add project/repository refs when known
rvn write issue "<issue title>" --field status=ready --content-file "<temp-file>" --json
# Optional fields: --field "project=<project-id>" and/or --field "repository=<repo-id>"
rvn set <issue-id> --field status=in_progress --json

# Validate the schema and vault content
rvn schema validate --json
rvn check --json
```

Run `rvn query saved list --json` to see saved queries. Use `rvn query
<name> --json` to run one. The `all-issues`, `open-issues`, `ready-issues`,
`blocked-issues`, `repo-docs`, `meetings`, `project-meetings`, `meeting-todos`,
`project-docs`, and `project-doc-todos` queries are already defined in `vault/`.

## Projects, contacts, meetings, and issues

A project does not need a Git repository. Create the project first, then add
people as reusable Raven `person` objects and link their canonical IDs through
the project's `contacts` field. When the repository decision is made, create
the repository record and add its ID to the project's `repositories` field.
Never create a placeholder repository just to store project notes.

For example, `rvn new project "<project name>" --json` creates a project hub;
`rvn new person "<person name>" --field email=<work-email> --json` creates a
contact. Use each command's returned `data.id` for references. To set `ref[]`
fields such as `contacts`, `meeting.attendees`, or `meeting.issues`, use
`--fields-json` with plain canonical IDs, not `[[...]]`. In PowerShell, if
inline JSON is mangled, follow the temporary-script guidance in
`account-skills/shared/RAVEN-WRITE-RECIPE.md`.

Store project material as `project-doc` objects. For example, discovery notes
use `kind=discovery`. Store each meeting as a `meeting` object, whether or not
it belongs to a project. `project-doc` requires a project; `meeting.project` is
optional, as are `meeting.issues` links. Issues can likewise have optional
project and repository links. Reference fields use plain canonical Raven IDs in
frontmatter/CLI fields; use `[[canonical-id]]` in the Markdown body. For a full
document, use `rvn write --content-file` as described in the shared Raven write
recipe.

```powershell
rvn write project-doc "<project> Discovery" --field "project=<project-id>" --field kind=discovery --content-file "<temp-file>" --json
rvn write meeting "<project> 2026-11-04 Meeting topic" --field "project=<project-id>" --field date=2026-11-04 --content-file "<temp-file>" --json
rvn write meeting "2026-11-04 Team sync" --field date=2026-11-04 --content-file "<temp-file>" --json
```

Set meeting `attendees` and `issues` with `--fields-json` when creating or
updating the note, using the IDs returned by Raven. `project` is a single
optional reference and can be passed as `--field "project=<project-id>"`. If
the body also links attendees/issues with `[[person/<id>]]` or `[[issues/<id>]]`,
those links provide human-readable backlinks.

Create one meeting document per meeting. A useful body template is:

```md
# <Meeting topic>

## Purpose

## Project (if relevant)
- [[project/<canonical-project-id>]]

## Issues (if relevant)
- [[issues/<canonical-issue-id>]]

## Attendees
- [[person/<canonical-person-id>]]

## Discussion

## Decisions

## Actions
- @todo <action>

## Open questions

## Related documents
- [[project-docs/<canonical-document-id>]]
```

Use the `attendees` reference field as well as body links when you want to query
meetings by participant. Put only agreed actions in `@todo` lines; add `@due`
only when a date is known. Keep actual personal identity numbers and secrets
out of the vault, and follow any project-specific rule about customer
information.

## Working with todos

Use `@todo` for a small action that belongs in the context of a meeting or
project document. Put one action on each line, usually under `## Actions` in a
meeting note:

```md
- @todo Ask Daniel about the integration workflow
- @todo @due(2026-11-10) Send the meeting notes
```

In this vault, `todo` is a trait with two values: `todo` (the default, meaning
open) and `done`. Completing an action changes the trait value to `done`; it
does not create a separate `@done` trait. Use an `issue` instead when work
needs its own status, blockers, or acceptance criteria. Issues can be linked to
a project or repository, but neither link is required.

The saved `meeting-todos` and `project-doc-todos` queries list todo traits in
those locations, including completed ones. Add `.value==todo` to see only open
actions:

```powershell
rvn query meeting-todos --json
rvn query project-doc-todos --json
rvn query 'trait:todo .value==todo' --json
rvn query 'trait:todo .value==todo within(type:meeting)' --json
rvn query 'trait:todo .value==todo within(type:project-doc)' --json
```

Trait-query results are individual actions. In `--json` output, use an item's
`object_id` to read its source note or section, and its `id` to update the
action:

```powershell
rvn read '<object_id-from-result>' --raw --json
rvn update '<trait-id-from-result>' done --json
```

For bulk completion, first preview the matching open actions, then add
`--confirm` only after reviewing the preview:

```powershell
rvn query 'trait:todo .value==todo within(type:meeting)' --apply 'update done' --json
rvn query 'trait:todo .value==todo within(type:meeting)' --apply 'update done' --confirm --json
```

Useful searches:

```powershell
rvn query 'type:project-doc .project==[[project/bky-integration]]' --json
rvn query 'type:meeting .project==[[project/bky-integration]] sort(.date, desc)' --json
rvn query 'type:meeting refs([[issues/<issue-id>]])' --json
rvn query 'type:issue .project==[[project/bky-integration]] sort(.status) sort(.name)' --json
rvn query 'type:project-doc .project==[[project/bky-integration]] contains(trait:todo .value==todo)' --json
rvn search "BKY SOAP API" --json
```

The saved `meetings` query lists all meetings; `project-meetings` lists the
project-linked subset. `meeting-todos` and `project-doc-todos` include both open
and completed actions; use the `.value==todo` filter for open ones. Use
`rvn search` for terms you do not yet know how to filter structurally.

## Account-level OpenCode skills

The skill folders in `account-skills/` are the editable, Git-tracked source.
Copy each whole skill folder—including its supporting files—and the `shared/`
reference folder into OpenCode's account-level `~/.agents/skills` directory.
On Windows this is `%USERPROFILE%\.agents\skills`. `shared/` contains common
instructions, not another skill; Raven-writing skills read it relative to
their skill directory.

From PowerShell at the repository root:

```powershell
$source = (Resolve-Path .\account-skills).Path
$target = Join-Path $HOME '.agents\skills'
New-Item -ItemType Directory -Path $target -Force | Out-Null
Get-ChildItem $source -Directory | ForEach-Object {
  Copy-Item -Path $_.FullName -Destination $target -Recurse -Force
}
```

Review existing account-level copies before using `-Force`; it replaces files
with the Git-tracked versions. Edit the source here, then copy again to publish
an update to the account-level skills. `rvn skill install` manages Raven's
shipped `raven-*` skills, not these custom workflows; use this copy procedure
for the contents of `account-skills/`.

Included skills:

- `bootstrap-docs` — discover and maintain compact, task-oriented code maps in
  Raven.
- `grill-with-docs` — use the matching repository's glossary and decisions.
- `to-prd` and `to-issues` — create PRDs and linked issues in Raven.
- `implement` and `review-implementation` — work from Raven issues and
  repository context.
- `tdd` and `codebase-design` — reusable engineering practices.

`release`, `regression-suite`, `test-plan`, and `to-issues-default` are
intentionally not included. OpenCode can discover user-level skills from
`~/.agents/skills`; a project-local skill with the same ID can override it.

## Workflow

1. Run `bootstrap-docs` from a real code repository to register that repository
   and create or refresh its code map in Raven.
2. Use `grill-with-docs` to resolve domain language and decisions; approved
   glossaries and decisions are stored as linked `repo-doc` objects.
3. Use `to-prd` and `to-issues` to create linked planning documents and issue
   objects. Query the saved issue lists instead of maintaining numbered files.
4. Use `implement` or `review-implementation` with a canonical issue ID. These
   skills load only the matching repository's relevant Raven documents.

Skills must identify the current Git root and remote, match them to a
`repository` record, and ask if the match is missing or ambiguous. They must
not silently use context from a different repository or change Raven's
machine-wide active/default vault selection.
