# FastAPI Module 4 — Dependencies and Resource Scopes

Date: 13 September 2026

## Dependency injection

- `Annotated[ServiceType, Depends(provider)]` tells FastAPI to call `provider` and inject its result.
- FastAPI—not the route—calls dependency providers.
- Routes translate business exceptions into HTTP responses; services remain transport-independent.
- Providers construct and connect dependencies, such as `route → OrderService → OrderRepository`.

## Composition and caching

- FastAPI resolves the dependency graph for each request.
- The same dependency callable is normally executed once per request and its result reused across graph branches.
- This is request caching, not singleton scope. A later request resolves a new graph.
- Multiple repository instances can still observe the same data when they reference the same shared dictionary.

## Dependencies with `yield`

```python
def get_resource():
    resource = acquire()
    try:
        yield resource
    finally:
        resource.close()
```

- Code before `yield` acquires the resource; the yielded value is injected.
- FastAPI drives the dependency's exit phase, and `finally` guarantees cleanup on success or error.
- A dependency should yield exactly once.

## Dependency scope

- `scope="function"`: cleanup occurs after the route function finishes but before the response is sent.
- `scope="request"`: cleanup normally occurs after the response is sent; this is the default for `yield` dependencies.
- Request scope is safer when response validation, serialisation, lazy ORM loading, or streaming still needs the resource.
- Function scope can release resources earlier when the response is fully materialised before the handler returns.

## Application lifespan

- Lifespan startup code runs once before a worker accepts requests; shutdown code runs once when that worker stops.
- Share expensive, concurrency-safe infrastructure such as a SQLAlchemy engine/connection pool or HTTP client at application scope.
- Keep mutable transactional resources such as SQLAlchemy sessions request-scoped.
- Application scope means once per process/worker, not once across all workers.
- In-memory `app.state` data is not durable and diverges between worker processes.

## Dependency overrides

```python
app.dependency_overrides[get_order_service] = override_get_order_service
```

- The key is the original provider callable—not its `Annotated` alias or return type.
- Replacing a provider also skips its original sub-dependency graph.
- Clear overrides after a focused check so they cannot leak into later tests.
- A fake-service route check verifies injection, argument forwarding, and response handling; it does not verify real business logic or persistence.

## Demonstrated

- Injected `OrderService` and composed it with `OrderRepository`.
- Observed per-request caching through two dependency paths.
- Verified repository cleanup for `200`, `409`, and `404` outcomes.
- Compared function- and request-scoped cleanup timing.
- Created lifespan-managed application state and observed startup, per-request, and shutdown events.
- Correctly reasoned about fake dependency substitution; final fake-check execution output was not recorded in the session.

