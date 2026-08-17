# Session 01 — Modern Python for Backend Engineers

## Purpose

This session focused on Python language and data-model features that directly affect backend correctness, reliability, maintainability, and performance.

The emphasis was not Python syntax, but how Python behaves at runtime when used for:

- domain models,
- persistence boundaries,
- dependency contracts,
- transaction/resource ownership,
- exception handling,
- streaming and large-result processing.

---

## 1. Mutable Defaults

### Problem

Mutable default values are created once and then shared by all instances.

```python
from dataclasses import dataclass

@dataclass
class Product:
    tags: list[str] = []  # wrong
```

All `Product` instances using the default reference the same list.

### Correct form

```python
from dataclasses import dataclass, field

@dataclass
class Product:
    tags: list[str] = field(default_factory=list)
```

### Production rule

Never use mutable objects such as `[]`, `{}`, or `set()` directly as dataclass/default argument values when each instance should own independent state.

---

## 2. Money: `float` vs `Decimal`

Binary floating-point values cannot exactly represent many decimal fractions.

```python
0.1 + 0.2
```

This can produce values unsuitable for accounting, payment reconciliation, or exact monetary comparisons.

### Prefer

```python
from decimal import Decimal

price = Decimal("19.95")
```

Alternative: store monetary values as integer minor units, such as cents.

### Production rule

For financial values:

- use `Decimal`, or
- use integer minor units.

Do not use `float` for authoritative monetary calculations.

---

## 3. Type Hints Are Not Runtime Validation

```python
def find(product_id: int):
    ...
```

does not guarantee that `product_id` is actually an `int` at runtime.

Therefore this remains unsafe:

```python
sql = f"SELECT * FROM products WHERE id = {product_id}"
```

### Correct approach

Use parameter binding:

```python
db.execute(
    "SELECT * FROM products WHERE id = :product_id",
    {"product_id": product_id},
)
```

### Production rule

Static typing and validation improve correctness, but they do not replace security controls.

For SQL injection prevention, parameterisation is the primary defence.

---

## 4. Repository Contracts

A repository should expose explicit, typed behaviour.

Weak:

```python
def find(self, product_id: int):
    ...
```

Better:

```python
def find(self, product_id: int) -> Product | None:
    ...
```

For batch access:

```python
def find_many(
    self,
    ids: Collection[int],
) -> dict[int, Product]:
    ...
```

### Key design question

The repository should usually report what exists.

The service layer should decide whether missing data violates a business rule.

Example:

```python
products = repository.find_many(product_ids)

missing = set(product_ids) - products.keys()

if missing:
    raise ProductNotFoundError(missing)
```

---

## 5. Avoid N Database Round Trips

This is inefficient:

```python
for product_id in product_ids:
    product = repository.find(product_id)
```

For large input, this creates N database calls.

Consequences:

- high latency,
- excessive connection usage,
- DB load,
- pool contention,
- downstream saturation.

### Prefer

Batch retrieval:

```sql
WHERE id IN (...)
```

For very large ID collections, split into bounded chunks.

### Important distinction

Batching/chunking and pagination solve different problems.

- **Batching/chunking**: process a known set of IDs efficiently.
- **Pagination**: expose large browsable result sets incrementally.

---

## 6. Explicit Missing-Data Semantics

Silently ignoring missing products is dangerous.

Example:

```python
if product:
    total += product.price
```

For requested IDs:

```text
[1, 2, 999]
```

returning only the total of products `1` and `2` hides invalid input.

### Better contracts include

- fail if any requested item is missing,
- return found items plus missing IDs,
- explicitly document that missing items are ignored.

The key is not one universal policy, but an explicit and testable contract.

---

## 7. Exception Handling

This is dangerous:

```python
try:
    ...
except Exception:
    return []
```

It converts:

```text
database failure
```

into:

```text
successful empty result
```

This destroys failure semantics.

### Better approach

Let infrastructure exceptions propagate, or translate them at a deliberate application boundary.

Typical flow:

```text
Repository
    ↓ raises infrastructure/database error

Service/Application
    ↓ may translate to application/domain error

HTTP Boundary
    ↓ maps to API response
```

### Production rule

Do not encode failure as an apparently successful empty value unless that is explicitly part of the contract.

---

## 8. Structured Exceptions

Avoid storing important data only inside exception text.

Weak:

```python
raise ProductNotFoundError(str(missing))
```

Better:

```python
class ProductNotFoundError(Exception):
    def __init__(self, product_ids: Collection[int]) -> None:
        self.product_ids = frozenset(product_ids)
        super().__init__("One or more products were not found")
```

### Why

Callers can inspect structured state without parsing strings.

Use exception messages for humans; use exception attributes for programs.

---

## 9. Protocols and Structural Typing

A service should depend on the behaviour it needs, not necessarily on a concrete repository class.

```python
from typing import Protocol

class ProductReader(Protocol):
    def find_many(
        self,
        ids: Collection[int],
    ) -> dict[int, Product]:
        ...
```

Then:

```python
class OrderService:
    def __init__(self, products: ProductReader):
        self.products = products
```

Implementations do not need to inherit from `ProductReader`.

### Production value

`Protocol` enables dependency inversion using structural typing.

Useful for:

- repositories,
- external-service adapters,
- cache abstractions,
- test doubles.

### Principle

Depend on the minimum capability required by the consumer.

---

## 10. Choosing Collection Abstractions

Use the narrowest abstraction that expresses the actual requirement.

### `Sequence[T]`

Use when:

- ordering matters,
- duplicates may matter,
- indexing is conceptually valid.

Examples:

- `list`
- `tuple`

A `set` is not a `Sequence`.

### `Collection[T]`

Use when the code mainly needs:

- iteration,
- membership,
- size.

Useful where ordering is not part of the contract.

### Example distinction

`calculate_total()` may need a `Sequence[int]` because duplicates represent multiplicity.

A repository `find_many()` may only need a `Collection[int]`.

---

## 11. Dataclasses vs Pydantic

### Dataclass

Often suitable for internal domain/application objects.

```python
@dataclass(frozen=True, slots=True)
class Product:
    id: int
    name: str
    price: Decimal
```

Benefits:

- lightweight,
- simple semantics,
- good for trusted internal objects.

### Pydantic

Best suited to trust/transport boundaries:

- HTTP request validation,
- HTTP response schemas,
- parsing external data,
- serialisation,
- FastAPI/OpenAPI integration.

Typical structure:

```text
HTTP JSON
   ↓
Pydantic request model
   ↓
service/domain
   ↓
dataclass/domain entity
   ↓
repository
```

Do not automatically duplicate every model; use separate types where the boundary or contract justifies it.

---

## 12. `frozen=True`

```python
@dataclass(frozen=True)
class Product:
    ...
```

Prevents normal attribute reassignment.

Useful when an object should represent an immutable value or snapshot.

Benefits:

- easier reasoning,
- fewer accidental mutations,
- clearer domain semantics.

Not mandatory for every domain class.

---

## 13. `slots=True`

```python
@dataclass(slots=True)
class Product:
    ...
```

Main effects:

- avoids a per-instance `__dict__`,
- prevents arbitrary new attributes,
- reduces memory overhead,
- may slightly improve attribute access.

Useful for memory-sensitive/high-volume objects, but generally not a core correctness requirement.

---

## 14. Repository vs Transaction Ownership

A repository should not necessarily own its database session or connection.

If each repository opens and commits independently:

```text
OrderRepository      → Transaction A
InventoryRepository  → Transaction B
PaymentRepository    → Transaction C
```

a single business operation can partially commit.

Example:

```text
create order       → commit
reserve inventory  → commit
create payment     → fail
```

The system is now inconsistent.

### Better model

```text
Request / Application Operation
          ↓
Transaction / Unit of Work
          ↓
 ┌────────┼──────────┐
Order   Inventory   Payment
Repo      Repo       Repo
```

### Distinction

- **Repository**: how data is accessed.
- **Transaction / Unit of Work**: which operations succeed or fail together.

---

## 15. Context Managers

Context managers express deterministic resource lifecycle.

```python
with resource() as r:
    ...
```

Semantics:

```text
acquire
  ↓
use
  ↓
release reliably
```

Common backend uses:

- DB sessions/connections,
- transactions,
- files,
- locks,
- temporary resources,
- tracing spans.

### Equivalent idea

```python
resource = acquire()

try:
    ...
finally:
    release(resource)
```

Context managers centralise and clarify this policy.

---

## 16. `__exit__()` Semantics

A context manager may define:

```python
def __exit__(self, exc_type, exc_value, traceback):
    ...
```

If an exception occurred inside the `with` block, these arguments describe it.

### Return value

- truthy → suppress the exception,
- falsy / `None` → propagate it.

For transaction boundaries, unexpected exceptions should normally not be silently suppressed.

---

## 17. Cleanup Must Survive Cleanup Failure

This is unsafe:

```python
if exc_type:
    connection.rollback()
else:
    connection.commit()

connection.close()
```

If `commit()` or `rollback()` raises, `close()` never executes.

Better structure:

```python
try:
    if exc_type is None:
        connection.commit()
    else:
        connection.rollback()
finally:
    connection.close()
```

### Production rule

Cleanup logic itself must be designed for failure.

---

## 18. Exception Chaining

A difficult case:

```text
business operation raises A
rollback raises B
```

Both failures may matter diagnostically.

Python supports exception chaining so the original cause can be preserved.

This becomes important when translating infrastructure exceptions into application-level exceptions.

---

## 19. Generators and Laziness

A generator:

```python
def load_orders(repository):
    for row in repository.iter_rows():
        yield Order.from_row(row)
```

does not build a full list before returning results.

Benefits:

- lower peak memory,
- earlier first-result availability,
- incremental processing.

### Critical rule

`yield` does **not** guarantee bounded memory.

If an upstream layer uses:

```python
cursor.fetchall()
```

all rows may already be materialised.

Similarly:

```python
list(load_orders(repository))
```

materialises the generator output.

Bounded-memory behaviour requires the entire pipeline to be incremental.

---

## 20. Streaming Pipeline Thinking

For large exports, reason about the complete path:

```text
Database
   ↓
incremental cursor fetch
   ↓
repository iterator
   ↓
Python generator
   ↓
incremental serialisation
   ↓
HTTP streaming
   ↓
client
```

Any stage that buffers everything defeats the memory advantage.

---

## 21. Generator Resource Lifetime

If a generator owns a DB cursor:

```python
def iter_rows():
    cursor = ...

    try:
        ...
        yield row
    finally:
        cursor.close()
```

cleanup must occur when:

- iteration completes,
- iteration raises,
- iteration is cancelled,
- the consumer stops early and the generator is closed.

Do not rely solely on eventual garbage collection for expensive resources.

---

## 22. Partial Consumption

This is subtle:

```python
orders = stream_orders()

for index, order in enumerate(orders):
    if index == 99:
        break
```

The generator object may still exist after `break`.

Therefore a cursor tied to that generator may remain open unless generator/resource cleanup is explicitly handled.

Lazy iteration changes resource-lifetime design.

---

## 23. HTTP Streaming

Returning a generator is not by itself enough.

Efficient streaming requires cooperation across:

- DB access,
- generator,
- serialiser,
- HTTP framework,
- network/client.

FastAPI/Starlette provides streaming responses, but the DB side must also be incremental.

### Backpressure

If the producer is faster than the consumer, the implementation must not buffer unbounded data.

The slowest stage determines throughput.

---

## 24. JSON vs Streaming Formats

Yielding individual JSON objects:

```text
{"id": 1}
{"id": 2}
{"id": 3}
```

is not a single valid JSON document.

A JSON array needs framing:

```json
[
  {"id": 1},
  {"id": 2},
  {"id": 3}
]
```

It can be streamed carefully, but framing and error handling are more complex.

### NDJSON

A common streaming-friendly format:

```text
{"id":1}
{"id":2}
{"id":3}
```

Each line is an independent JSON document.

---

## 25. Long-Lived Streaming Connections

A long-running export may hold a DB connection for many minutes.

Risks:

- connection-pool exhaustion,
- normal API requests blocking,
- timeouts,
- long-running transactions,
- delayed DB cleanup,
- large operational blast radius.

The consistency requirement should determine whether one long transaction is justified.

---

## 26. Asynchronous Export Jobs

For very large exports, prefer decoupling the HTTP request from the export work.

Typical design:

```text
POST /order-exports
        ↓
create job
        ↓
background worker
        ↓
batch-read database
        ↓
write export file
        ↓
object/file storage
        ↓
client polls status
        ↓
download completed file
```

Advantages:

- short request lifetime,
- no slow client holding a DB connection,
- retryable background work,
- clearer progress/failure state,
- better control over resource usage.

---

# Core Production Principles

## Security

- Type hints are not runtime security controls.
- Always parameterise SQL.

## Correctness

- Avoid shared mutable defaults.
- Use exact monetary representations.
- Define explicit missing-data semantics.
- Use structured exceptions.

## Dependency Design

- Depend on capabilities, not concrete implementations.
- Use `Protocol` where structural typing is appropriate.
- Choose the narrowest collection abstraction that matches requirements.

## Resource Management

- Deterministically acquire/release resources.
- Prefer context managers.
- Cleanup must work even when commit/rollback fails.
- Repositories do not necessarily own transaction lifecycle.

## Performance

- Avoid N database round trips.
- Batch/chunk known IDs.
- Do not confuse batching with pagination.
- Generators only provide laziness; the whole pipeline must stream.

## Reliability

- Never silently convert infrastructure failure into successful empty output.
- Preserve exception semantics.
- Handle cancellation and partial consumption.
- Avoid holding scarce resources for slow clients.

---

# Must Know

- mutable defaults and `default_factory`
- `Decimal` for money
- parameterised SQL
- type hints are not runtime enforcement
- explicit return types
- `Protocol`
- `Sequence` vs `Collection`
- context managers
- `try/finally`
- `__exit__()` return semantics
- repository vs transaction ownership
- exception propagation
- generator laziness and resource lifetime

---

# Useful in Production

- `dataclass(frozen=True, slots=True)`
- structured domain exceptions
- batch/chunk repository APIs
- Unit of Work pattern
- incremental DB cursors
- NDJSON
- asynchronous export jobs
- exception chaining

---

# Framework-Specific Details

To revisit later with FastAPI / SQLAlchemy:

- FastAPI request/response Pydantic models,
- `StreamingResponse`,
- dependency-managed SQLAlchemy sessions,
- SQLAlchemy transaction contexts,
- async session lifecycle,
- cancellation,
- connection pooling,
- OpenAPI error contracts.

---

# Revision Checklist

- [ ] Explain why `tags: list[str] = []` is shared state.
- [ ] Use `field(default_factory=list)`.
- [ ] Explain why `float` is unsafe for authoritative monetary values.
- [ ] Explain why `product_id: int` does not prevent SQL injection.
- [ ] Use bound SQL parameters.
- [ ] Define a repository capability using `Protocol`.
- [ ] Choose between `Sequence` and `Collection` deliberately.
- [ ] Explain why a repository should not automatically own a DB session.
- [ ] Explain the difference between repository and Unit of Work.
- [ ] Explain `__exit__()` exception suppression.
- [ ] Make cleanup reliable when commit/rollback fails.
- [ ] Explain why `yield` does not guarantee bounded memory.
- [ ] Explain what happens when a generator is partially consumed.
- [ ] Describe an end-to-end streaming pipeline.
- [ ] Explain JSON-array framing vs NDJSON.
- [ ] Explain why large exports may be better as background jobs.

