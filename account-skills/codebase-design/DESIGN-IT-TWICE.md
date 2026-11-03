# Design It Twice

When a user wants alternatives for a deepening candidate, do not stop at the
first interface idea. Use the vocabulary in [SKILL.md](SKILL.md)—**module**,
**interface**, **seam**, **adapter**, **leverage**, and **locality**—and the
matched repository's Raven glossary.

## 1. Frame the problem

Explain the constraints, dependencies and their categories from
[DEEPENING.md](DEEPENING.md), plus a small illustrative sketch. The sketch is
not a proposal. Show this framing to the user before comparing designs.

## 2. Compare alternatives

Unless the user explicitly requests delegation and the active harness permits
it, develop three meaningfully different interfaces in-session:

- Minimize the interface (few entry points, high leverage).
- Optimize for flexibility and extension.
- Make the most common caller's path simple by default.

If delegation is explicitly requested and allowed, parallel agents may explore
these constraints. Do not spawn agents automatically.

For each design, describe:

1. The interface and its invariants, ordering constraints, errors, and
   performance characteristics.
2. A usage example.
3. What the implementation hides behind the seam.
4. Dependencies and adapters.
5. Trade-offs in leverage, locality, and seam placement.

## 3. Recommend

Present the alternatives clearly, compare them on depth, locality, and seam
placement, then recommend one and explain why. Offer a hybrid only when parts
combine cleanly. Confirm the choice before recording a durable architectural
decision in Raven; use the `grill-with-docs` workflow for that record.
