---
name: to-prd
description: Synthesize the current feature conversation into a repository-linked PRD stored in the shared Raven vault.
disable-model-invocation: true
---

# Create a PRD in Raven

Turn the current conversation and verified codebase understanding into a PRD.
Do not restart the interview; synthesize what is already known and flag genuine
gaps instead of inventing requirements.

## Resolve context

1. Determine the current Git root and remote.
2. Read `rvn vault list --json`; locate the unique `repository` record matching
   this codebase by `path` or `remote`. Use its canonical ID and an explicit
   `--vault`/`--vault-path` for every Raven command. If the match is missing or
   ambiguous, stop and ask; do not switch machine-wide vault routing.
3. Read only the relevant `repo-doc` records: code map, glossary, conventions,
   architecture, and decisions. Verify code details against the source.

## Process

1. Draft a concise PRD from the conversation. Use the repository's glossary and
   respect its accepted decisions.
2. Identify the highest useful test seams. Prefer existing seams and keep the
   number small. Confirm the proposed seams with the user before writing.
3. Present the proposed title and PRD content for approval.
4. After approval, read [the shared Raven write recipe](../shared/RAVEN-WRITE-RECIPE.md)
   and write a `repo-doc` with `kind=prd` and a `repository` ref containing the
   matched repository's plain canonical ID. Use `--content-file` for the whole
   PRD body. Keep it in Raven; do not create `docs/prd/` files. Use the returned
   `data.id` for later references.

## PRD body

```md
## Problem Statement
The user's problem, in their terms.

## Solution
The proposed user-visible solution.

## User Stories
Numbered user stories describing the actors, capabilities, and benefits.

## Implementation Decisions
Approved module/interface changes, architectural choices, schema changes, API
contracts, and interaction decisions. Avoid volatile file paths and code dumps;
include a small shape only when it captures an approved decision better than
prose.

## Testing Decisions
Observable behavior to verify, the relevant seams, and useful test prior art.

## Out of Scope
Explicitly excluded work.

## Further Notes
Only relevant remaining context.
```

Keep the story list complete but not padded. Do not write, rename, or backfill
project-local PRD files; Raven links and backlinks are the index.
