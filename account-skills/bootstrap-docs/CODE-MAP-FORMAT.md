# Code Map Format

Store this as a Raven `repo-doc` with `kind=code-map`, linked to one
`repository`. The purpose is to help an agent reach the right source and tests
with a narrow read—not to describe every file.

```md
# {Repository name} — Code Map

Purpose: {One or two verified sentences.}

Verified against commit: {short Git commit}
Verified on: {YYYY-MM-DD}

## Start here

- Runtime/build entry point: `path` — {what starts here}
- Main flow: `path::Symbol` → `path::Symbol` → `path::Symbol`
- Tests for the main flow: `path`

## Where to start by task

| Task | Start at | Follow | Tests |
|---|---|---|---|
| {behavior/change} | `path::Symbol` | `path::Symbol` → ... | `test-path` |

## Structure and seams

- `path/` — {responsibility and what it owns}
- {Important dependency or ownership constraint, with paths/symbols}

## Commands

- Test: `{verified command}`
- Type check/analyze: `{verified command}`
- Run locally: `{verified command}`

## Raven references

- [[{canonical glossary repo-doc ID}]]
- [[{canonical conventions/decision repo-doc IDs}]]
```

## Rules

- Keep the summary short; do not paste code, entire manifests, or a full file
  tree.
- Every path, symbol, and command must be verified in the current checkout.
- Describe dependencies and data flow only at the level needed to choose a
  starting point.
- Add one row per common task family, not per issue. Prefer a task route that
  names a starting symbol, its next hop, and the test location.
- Remove or correct stale routes when refreshing the map. Do not retain a path
  merely because it was in the old map.
- If a section depends on an assumption the user confirmed rather than code,
  label it as such and link the relevant glossary or decision doc.
