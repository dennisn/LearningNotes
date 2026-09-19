# FastAPI Module 6 — Async and Blocking I/O

**Completed:** 19 September 2026  
**Status:** Demonstrated through executed code and observed timings

## 1. `def` versus `async def`

- FastAPI runs a normal `def` route in its thread pool.
- FastAPI runs an `async def` route on the event-loop thread.
- A blocking call inside `async def` blocks that event loop.
- FastAPI only manages functions it invokes directly, such as routes and dependencies. A synchronous service method called from an async route runs directly on the event loop unless explicitly offloaded.

Observed with two concurrent one-second requests:

| Implementation | Total time | Behaviour |
|---|---:|---|
| `def` + `time.sleep(1)` | ~1.01 s | Two thread-pool calls overlapped |
| `async def` + `time.sleep(1)` | ~2.00 s | Event loop was blocked; requests ran sequentially |
| `async def` + `await asyncio.sleep(1)` | ~1.02 s | Coroutines yielded while waiting |

## 2. I/O-bound versus CPU-bound work

- Async improves concurrency while awaiting compatible I/O; it does not make the operation itself faster.
- Declaring CPU-heavy code `async def` does not create yield points or parallel execution.
- A thread pool can keep the event loop responsive during synchronous I/O, but Python CPU-heavy work generally belongs in separate processes or an external worker system.

## 3. Timeouts and cancellation

```python
try:
    result = await asyncio.wait_for(operation(), timeout=0.2)
except TimeoutError:
    raise HTTPException(status_code=504, detail="Order service timed out")
```

- `wait_for()` cancels the awaited coroutine when its deadline expires.
- Cancellation is cooperative and normally arrives as `asyncio.CancelledError` at an `await` point.
- Use `finally` for cleanup and normally re-raise `CancelledError`.
- Swallowing cancellation can defeat the timeout and make cancelled work appear successful.
- A timeout cannot forcibly stop blocking synchronous work or guarantee that a remote server stops processing.

Observed sequence: downstream cancellation → downstream cleanup → dependency cleanup → `504` response in ~0.21 seconds.

## 4. Bounded concurrency and back-pressure

- `asyncio.Semaphore(n)` limits how many coroutines enter a protected operation concurrently.
- The semaphore must be shared at application/client scope. Creating one inside each call or request provides no shared limit.
- Waiting for a semaphore is awaitable and does not block the event loop.
- If `wait_for()` wraps the whole operation, its deadline includes both queue time and downstream execution time.

With limit `2`, six concurrent calls, `0.25`-second operations and a `0.6`-second timeout:

- Four requests completed with `200`.
- Two acquired capacity too late and were cancelled with `504`.
- Total time was ~0.61 seconds.

Limits are per process. Four server processes with a limit of two can perform up to eight protected operations unless coordination happens externally.

## 5. Async HTTP-client lifetime

- Create one `httpx.AsyncClient` during application lifespan and close it during shutdown.
- Reusing the client preserves its connection pool and avoids repeated socket, DNS and TLS setup.
- Pass request-specific headers per call; do not mutate shared defaults with user-specific state.
- An HTTP connection-pool limit controls transport resources. A semaphore controls an application-level operation policy; the values need not match.

Cleanup should be failure-safe, use local variables initialised to `None`, release resources in reverse acquisition order, and check with `is not None`.

## 6. Mixing synchronous and asynchronous libraries

Calling synchronous SQLAlchemy directly inside `async def` blocks the event loop. Valid choices include:

1. Keep the entire route synchronous, including its HTTP client, so FastAPI runs it in the thread pool.
2. Explicitly offload a complete synchronous database unit of work. Create, use and close its `Session` within the same offloaded callable because a `Session` is not thread-safe.
3. Migrate the complete database path to an async driver, async engine, `AsyncSession` and awaited operations.

Merely changing a service method to `async def` does not make synchronous database calls asynchronous.

## 7. SQLAlchemy async path

- Application scope: `AsyncEngine` and `async_sessionmaker`.
- Request/unit-of-work scope: `AsyncSession`.
- `AsyncSession` contains mutable transaction, identity-map and pending-change state and must not be shared across requests or concurrent tasks.
- Session creation is lazy; a physical connection is normally checked out on the first database operation.
- `await async_engine.dispose()` closes pooled async-driver connections during shutdown.

Request dependency pattern:

```python
async def get_db_session_async(request: Request) -> AsyncIterator[AsyncSession]:
    factory = request.app.state.session_factory_async
    async with factory() as session:
        yield session
```

Read pattern:

```python
result = await session.scalars(statement)
order = result.first()  # no second await: ScalarResult is buffered
```

Use deliberate eager loading such as `selectinload(OrderRecord.items)`. Ordinary attribute access cannot transparently await lazy relationship I/O and may raise `MissingGreenlet`; after session closure, the object may be detached.

Observed async order lookup:

- Existing order: `200`; one order query plus one `selectinload` item query.
- Missing order: `404`; only the order query.
- Read-only session cleanup logged `ROLLBACK` for the implicit transaction.

`aiosqlite` integrates SQLite with asyncio using background threads; it is useful for learning and local work but is not equivalent to a genuinely asynchronous network database driver.

## Key recall checks

1. Is every slow operation inside `async def` automatically non-blocking? **No.**
2. Does async make CPU work faster? **No.**
3. Does cancellation forcibly terminate arbitrary work? **No; it is cooperative.**
4. Should an `AsyncSession` be application-scoped? **No.**
5. Does entering an `AsyncSession` immediately acquire a connection? **Usually no; acquisition is lazy.**
6. Why bound concurrency? **To protect finite downstream, pool and memory capacity under load.**

## Follow-up

- Consider renaming the demonstrated `OrderServiceAsync` to `AsyncOrderRepository`, or inject an async repository into the service, because it currently contains SQLAlchemy query construction.
- Next prompt: **Start module 7 only.**
