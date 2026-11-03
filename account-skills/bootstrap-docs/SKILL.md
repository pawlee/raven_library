---
name: bootstrap-docs
description: Build or refresh a compact Raven code map for the current Git repository so agents can find the right modules, entry points, tests, and commands without broad exploration.
---

# Bootstrap repository docs in Raven

Use this when a repository has no useful agent orientation docs, when a code
map is stale, or when the user asks where to start. Store the result in the
shared Raven vault, not in a local `CONTEXT.md` or `docs/` directory.

## 1. Resolve the actual code repository

1. Find the Git root and canonical remote (`git rev-parse --show-toplevel`,
   `git remote get-url origin`). A missing remote is acceptable; record the
   local path instead.
2. Read `rvn vault list --json`. Search the configured vaults for a
   `repository` record whose `path` or `remote` matches. Use explicit
   `--vault`/`--vault-path` for every subsequent command.
3. If there is exactly one match, use its returned canonical `data.id`. If
   multiple match, ask which one is intended. If none match, ask before creating
   a `repository` record. Never silently assign a codebase to another repo's
   context or change machine-wide active/default vault selection.
4. Do not register the Raven Library host itself as a code repository by
   default. It contains the shared `vault/`; create a record for it only if the
   user explicitly wants agents to work on that repository's code.

## 2. Explore with a token budget

Use the repository's existing README, package/build manifests, directory tree,
entry points, routing or command registration, representative domain modules,
and nearby tests. Follow only the paths needed to verify the architecture. Do
not read every source file or reproduce source code in the map.

Capture only durable navigation information:

- What the repository does and the main runtime/build entry points.
- Major modules and the important direction of data/control flow.
- A task-to-starting-point map: likely paths/symbols, flow to follow, and
  relevant tests.
- Verified build, test, type-check, and local-run commands.
- Important ownership seams, architectural constraints, and references to
  existing Raven glossary, convention, and decision docs.

Use exact paths and symbol names, and verify that they exist. Prefer a few
high-signal routes over a directory-by-directory inventory. Do not infer
conventions or requirements that the code and user have not established.
Document configuration keys and paths, never credential values. If tracked
credentials are discovered, omit their values and flag the file path and
credential category separately.

## 3. Draft and save the code map

Use [CODE-MAP-FORMAT.md](./CODE-MAP-FORMAT.md). Check for an existing
`repo-doc` with `kind=code-map` linked to this repository. Update that object
rather than creating a duplicate. If none exists, create a `repo-doc` with
`kind=code-map` and the repository's canonical ID.

Include the Git commit inspected and the verification date in the document so
future agents can judge staleness. Show the proposed map to the user before
creating or replacing it. Save it with Raven commands and use the returned
canonical `data.id`; do not create local documentation files. Before writing,
read [the shared Raven write recipe](../shared/RAVEN-WRITE-RECIPE.md). Use
`rvn write repo-doc` with `kind=code-map`, the plain canonical repository ID in
the `repository` ref field, and `--content-file` for the complete map body.

## 4. Verify and hand off

After saving, read the Raven object back, confirm its repository and kind
fields, and check its references. Summarize the highest-value starting points
and the exact commands verified. Tell the user when facts could not be
confirmed. Recommend refreshing the map after major module moves, entry-point
changes, or build/test workflow changes—not after every small implementation.
