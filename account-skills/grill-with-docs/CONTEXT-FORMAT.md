# Raven glossary format

Store glossary content in a `repo-doc` object with `kind=glossary` and a
reference to its owning `repository`. The document is Markdown in the Raven
vault; do not create or update a project-local `CONTEXT.md`.

## Structure

```md
# {Context or domain name}

{One or two sentences describing the domain covered by this glossary.}

## Language

**Order**
An instruction to purchase goods from a supplier.
_Avoid_: Purchase, transaction

**Customer**
A person or organization that places orders.
_Avoid_: Client, buyer, account
```

## Rules

- Be opinionated: choose one canonical term and list misleading alternatives
  under `_Avoid_`.
- Keep definitions to one or two sentences. Define what the concept is, not
  every behavior it participates in.
- Include terms specific to this repository's domain. General programming
  concepts do not belong just because the code uses them.
- Group terms only when useful clusters emerge; do not create empty sections.
- For a repository with distinct domains, create separate glossary documents
  linked to the same repository and name each for its scope. The code map or an
  architecture `repo-doc` can point agents to the relevant glossary. Ask if a
  term's domain is unclear.
- Update the glossary only after the user confirms the term or correction.
