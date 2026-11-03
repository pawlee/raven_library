# Raven decision-record format

Store an architectural decision as a `repo-doc` with `kind=decision`, linked
to the owning `repository`. The Raven object ID is its stable reference; do not
maintain a separate numbered filename sequence or create a local `docs/adr/`
copy.

## Template

```md
# {Short decision title}

{One to three sentences: the context, the decision, and why.}
```

Keep the record short. Add optional sections only when they help a future
reader:

- **Status** — proposed, accepted, deprecated, or superseded by another Raven
  decision object.
- **Considered options** — only when rejected alternatives are worth
  remembering.
- **Consequences** — non-obvious downstream effects.

## When to record a decision

Offer a decision record only when all three apply:

1. It is hard to reverse.
2. It would be surprising without context.
3. There was a real trade-off with alternatives.

Examples include architectural shape, cross-domain integration patterns,
choices with meaningful lock-in, ownership boundaries, deliberate deviations
from an obvious approach, and constraints not visible in code. If a decision is
easy to reverse or obvious, leave it out. Show the proposed record to the user
before creating it.
