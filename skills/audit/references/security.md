# Security checklist

Pragmatic OWASP Top 10 plus NestJS/React/Node specifics. Work every item against
the target area. For each finding ask: is it hackable, can data leak, are secrets
exposed? Flag accordingly and cite `file:line`.

## Injection

- [ ] Raw SQL or string-interpolated queries — TypeORM `query()`, `createQueryBuilder`
      with concatenated input, Prisma `$queryRawUnsafe`. Use parameterised queries.
- [ ] NoSQL injection — unsanitised objects passed into Mongo / `where` filters.
- [ ] Command injection — `child_process` `exec`/`execSync` built from user input.
- [ ] Unsafe deserialization or `eval`-like dynamic execution.

## Broken authentication

- [ ] JWT handling: signature verified, algorithm pinned (no `alg: none`, no
      HS↔RS confusion), sensible expiry, refresh-token rotation, revocation on
      logout.
- [ ] Password hashing uses bcrypt/argon2 with a proper cost — never plaintext,
      MD5, SHA1, or unsalted hashing.
- [ ] Brute-force protection / account lockout / throttling on login and OTP.
- [ ] Tokens transmitted and stored securely; never placed in URLs or logs.

## Broken access control

- [ ] Every protected route has a guard (`@UseGuards(AuthGuard...)`); no endpoint
      is silently public.
- [ ] Object-level authorization (IDOR): the handler verifies the caller *owns* or
      may access the specific resource id, not merely that they are logged in.
- [ ] Role/permission checks enforced **server-side** on every protected endpoint
      (`@Roles` + `RolesGuard`), not just hidden in the UI.
- [ ] No mass-assignment of privileged fields (`role`, `isAdmin`, ownership ids)
      from the request body.

## Secrets exposure

- [ ] No hardcoded keys, tokens, passwords, or connection strings in source.
- [ ] Secrets read via `ConfigService`/env, not committed `.env` files.
- [ ] Secrets never logged (check the shared logging package) or returned in error
      responses / stack traces.

## Sensitive data exposure

- [ ] PII not logged or over-returned; responses shaped by DTOs, not raw entities.
- [ ] `class-transformer` `@Exclude()` / a serialization interceptor strips
      password hashes, tokens, and internal fields.
- [ ] No over-fetching that pulls back more than the caller needs.

## Input validation

- [ ] DTOs validated with `class-validator` decorators.
- [ ] Global `ValidationPipe` with `whitelist: true` and `forbidNonWhitelisted:
      true` (plus `transform: true` where relevant).
- [ ] Output encoding/sanitisation for anything reflected back or rendered.

## SSRF / unsafe outbound requests

- [ ] User-controlled URLs in server-side `fetch`/`axios`/`http` calls are
      validated or allow-listed; no fetching arbitrary internal addresses.

## Vulnerable dependencies

- [ ] Packages used by this area have no known critical CVEs; note risky or
      abandoned deps. Suggest `pnpm audit` if unsure — do not run it silently.

## Security misconfiguration

- [ ] CORS not wildcard-open while also allowing credentials.
- [ ] Security headers present (`helmet`).
- [ ] Verbose errors, stack traces, or debug mode not reachable in production.

## Weak crypto / randomness

- [ ] `Math.random()` not used for tokens, ids, or secrets — use `crypto`.
- [ ] No predictable sequential ids where unguessable ones are needed.
- [ ] No weak or legacy hashing/ciphers.

## Rate limiting

- [ ] Auth and expensive/abuse-prone endpoints are throttled (`@nestjs/throttler`
      or equivalent).

## Frontend (React)

- [ ] No `dangerouslySetInnerHTML` with untrusted content (XSS).
- [ ] Session tokens stored safely — prefer httpOnly cookies over `localStorage`;
      no secrets baked into the client bundle via `import.meta.env`/`process.env`.
- [ ] No open redirects from user-supplied `next`/`returnUrl` params.
