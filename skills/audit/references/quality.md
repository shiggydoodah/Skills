# Code quality checklist

Does this code match our standards and the structure of the rest of the app? Use
what the pre-flight found about sibling modules. Work every item and cite
`file:line`.

## Consistency with the codebase

- [ ] Naming, file layout, and module boundaries match sibling modules in the same
      app (compare against the pre-flight findings).
- [ ] No reinvention of helpers/utilities that already exist in shared
      `packages/*`.

## NestJS structure

- [ ] Clean module / controller / service / provider separation.
- [ ] No business logic in controllers — they delegate to services.
- [ ] Dependency injection used properly; no manual provider instantiation, no
      service-locator anti-patterns.
- [ ] DTOs and entities are distinct; controllers speak DTOs, not raw entities.
- [ ] Config read via `ConfigService`, not scattered `process.env` access.

## Type safety

- [ ] No `any` (explicit or implicit); prefer `unknown` plus narrowing.
- [ ] No unchecked `as` casts hiding real type mismatches.
- [ ] Strict null handling; optionals modelled rather than ignored.
- [ ] Proper DTOs/interfaces; discriminated unions where they clarify intent.

## Error handling

- [ ] Consistent strategy — Nest exception filters / typed HTTP exceptions; no
      swallowed errors (`catch {}`).
- [ ] Meaningful messages; failures surfaced, not hidden.

## Structure & maintainability

- [ ] Single responsibility; functions and files are a reasonable length.
- [ ] No duplication / copy-paste that should be extracted; no dead code.
- [ ] No lingering TODO/FIXME debt left untracked.
- [ ] Public APIs and non-obvious logic documented — the *why*, not just the what.

## Coupling

- [ ] No circular dependencies between modules/packages.
- [ ] Appropriate boundaries — the area doesn't reach into another module's
      internals.

## Frontend (React)

- [ ] Composition over deep prop-drilling or giant components.
- [ ] Rules of hooks respected; correct dependency arrays.
- [ ] Props are typed; no `any` props.
- [ ] Basic accessibility — semantic elements, labels, keyboard reachability.
