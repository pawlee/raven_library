---
name: tdd
description: Use test-driven development for a repository-linked issue, fixing one observable behavior at a time through public interfaces.
---

# Test-driven development

## Resolve the domain first

Before planning tests, identify the current Git root and match it to the issue's
`repository` record in the selected Raven vault. Read the relevant glossary,
code map, conventions, and decisions. Use explicit `--vault` or `--vault-path`
when querying; ask if repository or vault selection is ambiguous. Verify code
navigation hints against the current source.

## Principles

- Tests verify observable behavior through public interfaces, not internal
  implementation details.
- Prefer integration-style tests that follow real paths through the system.
- Mock system boundaries only; avoid mocking modules the project owns.
- Work in vertical tracer bullets: one test, one minimal implementation, then
  the next behavior. Do not write a large batch of tests before implementation.

## Workflow

### Plan

Before changing code, confirm the interface changes and the behaviors that
matter most with the user. Identify a high-level test seam, consult the
repository's code map for relevant tests and commands, and agree on the first
behavior to prove.

### Tracer bullet

1. Write one test for one externally observable behavior.
2. Run it and confirm it fails for the expected reason.
3. Implement the smallest change that makes it pass.
4. Run the focused test, then refactor only while green.

Repeat for the next behavior. Keep the tests at the public interface; if an
internal rename breaks them without changing behavior, they are too coupled.

### Finish

Run the relevant checks from the repository's verified code map and report the
results against the issue acceptance criteria. Record lasting discoveries in
Raven only through the owning skill's approved workflow; do not create a local
`CONTEXT.md` or issue document.

## References

- [Good and bad tests](tests.md)
- [Mocking guidelines](mocking.md)
- [Refactoring candidates](refactoring.md)

## Checklist per cycle

```text
[ ] Test describes behavior, not implementation
[ ] Test uses the public interface
[ ] Test would survive an internal refactor
[ ] Code is minimal for this behavior
[ ] No speculative features were added
```
