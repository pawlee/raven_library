---
name: to-issues
description: Break an approved Raven PRD or plan into independently verifiable issues linked to its repository and parent document.
disable-model-invocation: true
---

# Create issues in Raven

Create one Raven `issue` object per approved vertical slice. Issues are Markdown
objects in the shared vault, not numbered files in a repository or entries in
an external tracker.

## 1. Resolve the input and repository

- Accept a Raven issue/PRD ID, a repository-linked `repo-doc` ID, or the plan
  already discussed in this session.
- Resolve the current Git root and remote. Inspect `rvn vault list --json` and
  find the unique matching `repository` record. Use its canonical ID and an
  explicit `--vault` or `--vault-path`. If no unique match exists, stop and ask;
  do not change machine-wide vault routing.
- Read the source PRD and only the relevant code map, glossary, conventions, and
  decision documents from Raven. Verify code-specific assumptions in the repo.

## 2. Choose issue granularity

Ask which model will implement the work: local or cloud API. If local and no
size is given, assume 27–35B parameters. For local models, keep each issue to
one focused behavior change with 2–4 acceptance criteria and a small set of
verified navigation hints. For cloud models, a slice may cover several related
files and 4–7 criteria.

## 3. Draft vertical slices

Break work into tracer-bullet issues: each slice should deliver a narrow,
complete, independently verifiable behavior across the necessary layers. Do
prefactoring first when it is needed to make the change easy. Use the Raven
glossary and honor accepted decisions.

Present the proposed issues in dependency order. For each, include its title,
parent PRD, blockers, user stories covered, and acceptance criteria. Ask whether
the granularity and dependency relationships are right; iterate until approved.

## 4. Create approved issues

Create blockers before dependent issues. Set the required `repository` ref and
`status=ready`; set `parent` to the PRD `repo-doc` and `blocked_by` to canonical
IDs of blocking issues when applicable. Before any writes, read
[the shared Raven write recipe](../shared/RAVEN-WRITE-RECIPE.md). For each
approved issue, confirm its type/title identity is not already occupied by an
unrelated or completed issue; do not overwrite an existing resolution or
review. Then write the complete issue body from a UTF-8 temp file:

```powershell
rvn write issue "<issue title>" --field "repository=<canonical-repository-id>" --field status=ready --field "parent=<canonical-prd-id>" --content-file "<temp-file>" --json --vault "<vault-name>" --vault-path "<vault-path>"
```

Use the returned `data.id` for all later links. Do not invent a numeric issue
sequence or derive IDs from filenames. For `blocked_by` arrays, use
`--fields-json` with the blocking issues' canonical IDs. Raven's canonical
object IDs and references connect the work. Put the complete behavior and
acceptance criteria in the issue body:

```md
## What to build
Describe the narrow end-to-end behavior, not a layer-by-layer implementation.

## Acceptance criteria
- [ ] Observable criterion

## Blocked by
List the blocking issue references, or state that the issue can start
immediately.
```

After creation, verify the result with the saved `all-issues` or `ready-issues`
query. The `parent` and `blocked_by` fields make relationships queryable and
produce backlinks automatically. Do not create `docs/issue-tracker/` files or
append file lists to the PRD; the Raven references are the index.
