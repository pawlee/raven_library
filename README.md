# Raven Library

This repository is the Git-backed home for a shared Raven vault and the source
for account-level OpenCode skills. Repository-specific glossaries, code maps,
decisions, PRDs, and issues live as Markdown in `vault/`; reusable agent
workflows live in `account-skills/`.

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
- **`repo-doc`** — a document tied to a repository. Its `kind` is one of
  `glossary`, `code-map`, `conventions`, `decision`, `architecture`, `runbook`,
  or `prd`. Its required `repository` reference keeps documents scoped to the
  right codebase.
- **`issue`** — a work item with required `repository` and `status` fields,
  plus optional `parent` and `blocked_by` references. Status is `draft`,
  `ready`, `in_progress`, `blocked`, `done`, or `dropped`. Its Markdown body
  holds the description and acceptance criteria.
- Daily notes and the built-in `@todo` trait are for informal capture; not every
  next action needs a formal issue.

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

# Write a complete issue body after confirming the title identity is intended
rvn write issue "<issue title>" --field "repository=<canonical-repo-id>" --field status=ready --content-file "<temp-file>" --json
rvn set <issue-id> --field status=in_progress --json

# Validate the schema and vault content
rvn schema validate --json
rvn check --json
```

Run `rvn query saved list --json` to see saved queries. Use `rvn query
<name> --json` to run one. The `all-issues`, `open-issues`, `ready-issues`,
`blocked-issues`, and `repo-docs` queries are already defined in `vault/`.

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
