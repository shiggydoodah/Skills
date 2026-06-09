# Performance & scalability checklist

How does this area behave under heavy load, and does it scale horizontally? Work
every item against the target area and cite `file:line`. Where relevant, reason
about what breaks at 10x and 100x traffic.

## Database access

- [ ] **N+1 queries** — loops that query per item; TypeORM lazy relations or a
      missing `relations`, Prisma missing `include`/`select`. Batch or join.
- [ ] **Unbounded queries** — `find()`/`findAll` with no limit; missing pagination
      (`take`/`skip` or cursor) on list endpoints.
- [ ] **Missing indexes** on columns used in `where`, joins, and `order by`
      (`@Index`; composite indexes for multi-column filters).
- [ ] Connection pooling configured; no per-request connection creation.
- [ ] `SELECT *` / entity over-fetch where a narrow projection would do.

## Event loop & compute

- [ ] No synchronous I/O (`*Sync` fs, sync crypto) in the request path.
- [ ] No heavy CPU work (large loops, parsing, image processing) blocking the
      loop — offload to a worker or queue.
- [ ] No inefficient algorithms — O(n²) or worse on hot paths; nested scans over
      large arrays.

## Caching & reuse

- [ ] Cacheable reads are cached (`CacheModule`, HTTP cache headers, Redis,
      memoization) where correctness allows.
- [ ] Expensive pure computations are memoised rather than recomputed.

## Resource management

- [ ] Connections, streams, timers, and event listeners are closed / cleaned up;
      no leaks.
- [ ] External calls have timeouts; retries are bounded with backoff; circuit
      breakers where a dependency can fail.

## Payload & transport

- [ ] No payload bloat — responses shaped to what the client actually needs.
- [ ] Large responses streamed rather than buffered fully in memory.

## Concurrency & correctness under load

- [ ] Race conditions on shared state / read-modify-write — use transactions or
      locks.
- [ ] Idempotency on retried or double-submittable operations (idempotency keys,
      unique constraints).

## Horizontal scalability

- [ ] No in-memory state (caches, sessions, counters) that breaks across multiple
      instances — externalise it.
- [ ] Heavy or slow work offloaded to a queue/job (`@nestjs/bull`) rather than run
      inline in the request.

## Frontend (React)

- [ ] Unnecessary re-renders — missing `React.memo`, unstable props/callbacks
      (`useMemo`/`useCallback`), unstable list `key`s.
- [ ] Request waterfalls — sequential dependent fetches that could be parallel or
      colocated (React Query/SWR, prefetch).
- [ ] Long lists not virtualised.
- [ ] Large bundles — missing code-splitting (`React.lazy`/dynamic import); heavy
      barrel imports pulling in whole `packages/*`.

## Load reasoning

- [ ] State explicitly what breaks first at 10x/100x: which query, endpoint, or
      lock becomes the bottleneck, and whether timeouts, retries, and circuit
      breakers are in place to fail gracefully.
