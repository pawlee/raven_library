---
name: grill-with-docs
description: Interview and challenge a plan against the current repository's Raven glossary, code map, and decisions; capture approved terminology and significant decisions in Raven.
---

# Grill with Raven docs

## Resolve the repository and vault first

1. Determine the working Git root and remote (`git rev-parse --show-toplevel`,
   `git remote get-url origin`).
2. Read `rvn vault list --json`. Search the configured shared vault(s) for a
   `repository` record whose `path` or `remote` matches this codebase. Use that
   record's canonical `data.id`. If multiple vaults or records match, ask which
   one is intended. Do not change Raven's machine-wide active/default routing.
3. Use explicit `--vault <name>` or `--vault-path <path>` on subsequent Raven
   commands. If no matching record exists, stop and offer `bootstrap-docs`; do
   not create a repository entry silently. `raven_library` hosts the vault and
   is not itself a target repository by default.
4. Query the matching `repo-doc` records and read only the relevant kinds:
   `glossary`, `code-map`, `conventions`, `decision`, or `architecture`.

## Interview

Interview the user about the plan until there is a shared understanding. Walk
the design branches and dependencies one at a time; give a recommendation with
each question, then wait for the user's answer.

Prefix each question with its response format:

- `[YES/NO]` — a binary question, asked inline.
- `[CHOICE]` — use the question tool with concise options.

If the code or Raven docs can answer a question, inspect them instead of asking.

## Use the repository's language

- Compare user terminology with the matching Raven glossary. If it conflicts,
  explain the existing definition and ask which meaning is intended.
- Sharpen vague or overloaded terms and stress-test relationships with concrete
  scenarios.
- Verify claims about current behavior against the code. Surface contradictions
  rather than silently choosing one source.
- Use the code map to navigate, then inspect source files to verify details; the
  map is an index, not proof that the implementation still matches it.

## Save approved knowledge in Raven

- The glossary is a `repo-doc` with `kind=glossary`, linked to the matching
  repository. Keep it about domain language, not implementation details or
  scratch notes. Follow [CONTEXT-FORMAT.md](./CONTEXT-FORMAT.md).
- When a term is settled, show the concise proposed glossary change and get
  approval before editing or creating the Raven document. Do not create a local
  `CONTEXT.md`.
- Offer a decision record only when it is hard to reverse, surprising without
  context, and the result of a real trade-off. Store it as a `repo-doc` with
  `kind=decision`, using [ADR-FORMAT.md](./ADR-FORMAT.md). Do not create local
  `docs/adr/` files.
- Keep PRDs and issues in their own Raven object types; this skill only updates
  approved glossary and decision content.
