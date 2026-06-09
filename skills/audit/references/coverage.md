# Test coverage checklist

Is this area well tested, and are the tests trustworthy? Read the source **and**
its tests. Work every item and cite `file:line`. First confirm the framework from
the pre-flight (Jest or Vitest) — don't assume.

## Map the gap

- [ ] List what is tested vs untested across the area's public surface.
- [ ] Identify the critical paths and whether each is covered.

## Depth of coverage

- [ ] Happy path covered.
- [ ] Error paths and thrown exceptions covered.
- [ ] Edge cases and boundary values (empty, null, max, off-by-one).
- [ ] **Branch** coverage on conditionals — both sides of important `if`/guards,
      not just line coverage.
- [ ] Untested error/exception handling specifically called out.

## Trustworthiness (flaky-test smells)

- [ ] No dependence on real time/date (`Date.now()`, timers) without faking.
- [ ] No reliance on test ordering or shared mutable state between tests.
- [ ] No randomness without a fixed seed.
- [ ] No real network/DB/filesystem calls in unit tests; no `sleep`s.

## Test quality

- [ ] Assertions are meaningful — they would fail if the implementation regressed
      (not trivial `toBeDefined`).
- [ ] Not over-mocked to the point of testing the mocks rather than behaviour.
- [ ] Proper isolation and teardown between tests.

## Right test types

- [ ] Unit vs integration vs e2e are appropriate to the area.
- [ ] NestJS: providers tested via `Test.createTestingModule`; HTTP paths covered
      by e2e (supertest) where they carry real risk.
- [ ] React: React Testing Library + `user-event`, asserting behaviour rather than
      implementation details.

## Deliverable

- [ ] List specific, concrete test cases worth adding (by name/scenario).
- [ ] If judging real coverage needs the suite run, **don't run it silently** —
      give me the command (e.g. `pnpm --filter <pkg> test --coverage`, or
      `turbo run test`) and offer to run it.
