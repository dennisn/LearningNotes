# Session 01-Extra — Modern Python Backend Concepts

## Scope

This session reviewed six Python 3 topics that matter directly in production backend code:

1. `Protocol` syntax and structural typing
2. Precise collection abstractions: `Sequence` vs `Collection`
3. `__exit__()` exception suppression semantics
4. Why generators do not themselves guarantee bounded memory
5. Lifecycle of partially consumed generators
6. Streaming formats and framing: JSON vs NDJSON

These topics reinforce the Session 1 curriculum areas of typing, abstractions, iterators/generators, context managers, exceptions, and resource management.

---

## 1. `Protocol` and Structural Typing

### Core idea

`Protocol` expresses a **required capability** rather than a required inheritance hierarchy.

```python
from typing import Protocol

class Cache(Protocol):
    def get(self, key: str) -> bytes | None:
        ...

    def set(self, key: str, value: bytes) -> None:
        ...
```

A concrete implementation does not need to inherit from `Cache`:

```python
class RedisCache:
    def get(self, key: str) -> bytes | None:
        ...

    def set(self, key: str, value: bytes) -> None:
        ...
```

A static type checker considers `RedisCache` compatible because it provides the required members with compatible signatures.

### Structural vs nominal typing

- **Nominal typing:** compatibility is based on explicit inheritance.
- **Structural typing:** compatibility is based on the object's available members and their signatures.

A useful mental model is:

> “I do not care what class you are; I care what operations you support.”

### Why it matters in backend code

Protocols reduce coupling between application layers.

Typical uses include:

- repositories
- cache interfaces
- payment gateways
- serializers
- message publishers
- external service adapters
- test doubles

Example:

```python
class PaymentGateway(Protocol):
    def charge(self, amount: int) -> str:
        ...
```

A service can depend on `PaymentGateway` instead of a concrete Stripe, Adyen, or mock implementation.

### `@runtime_checkable`

```python
from typing import Protocol, runtime_checkable

@runtime_checkable
class Closable(Protocol):
    def close(self) -> None:
        ...
```

This allows:

```python
isinstance(obj, Closable)
```

However, runtime checking verifies required members only in a limited structural sense. It does **not** perform full runtime validation of method signatures and annotations.

### Key rule

Prefer protocols when the dependency is fundamentally about **capabilities**, especially when implementations should remain decoupled from the interface definition.

---

## 2. `Sequence` vs `Collection`

### Core principle

Use the **weakest abstraction that provides the operations your function requires**.

This makes the function's real contract explicit and avoids unnecessary restrictions.

---

### `Iterable[T]`

Use when the function only needs iteration:

```python
from collections.abc import Iterable

def process(values: Iterable[int]) -> None:
    for value in values:
        ...
```

Typical capability:

```python
for value in values:
    ...
```

---

### `Collection[T]`

A `Collection` provides:

- iteration
- `len()`
- membership testing with `in`

```python
from collections.abc import Collection

def contains_error(values: Collection[str]) -> bool:
    return "error" in values
```

Common implementations include:

- `list`
- `tuple`
- `set`
- `frozenset`

A `Collection` does **not** guarantee ordering or numeric indexing.

This is invalid against the abstraction:

```python
def first(values: Collection[int]) -> int:
    return values[0]
```

because a `set[int]` is a valid collection but is not indexable.

---

### `Sequence[T]`

A `Sequence` adds ordered, indexable semantics.

Typical capabilities include:

```python
values[0]
values[-1]
values[1:5]
len(values)
x in values
```

Typical sequence implementations:

- `list`
- `tuple`
- `str`
- `range`

Example:

```python
from collections.abc import Sequence

def compare_ends(values: Sequence[int]) -> bool:
    return values[0] == values[-1]
```

---

### `Sequence` does not imply mutability

A tuple is a sequence:

```python
tuple[int, ...]
```

Therefore this is not valid for a general `Sequence[int]`:

```python
values[0] = 42
```

If mutation is required, use:

```python
from collections.abc import MutableSequence

def update_first(values: MutableSequence[int]) -> None:
    values[0] = 42
```

---

### Practical hierarchy

```text
Iterable
   ↑
Collection
   ↑
Sequence
```

A separate collection branch includes set-like abstractions.

### Key rule

Choose types according to required semantics:

- iteration only → `Iterable`
- iteration + membership/length → `Collection`
- ordered/indexed access → `Sequence`
- ordered/indexed mutation → `MutableSequence`

Avoid using `list[T]` unless list-specific behaviour is genuinely required.

---

## 3. `__exit__()` Exception Suppression Semantics

A class-based context manager implements:

```python
class Resource:
    def __enter__(self):
        ...

    def __exit__(self, exc_type, exc_value, traceback):
        ...
```

### Critical rule

The return value of `__exit__()` controls exception propagation.

- falsey value (`None`, `False`) → exception propagates
- truthy value → exception is suppressed

Example:

```python
def __exit__(self, exc_type, exc_value, traceback):
    self.cleanup()
    return False
```

This performs cleanup but does not hide failures.

---

### Transaction example

```python
class Transaction:
    def __exit__(self, exc_type, exc_value, traceback):
        if exc_type is None:
            self.commit()
        else:
            self.rollback()
```

If code inside the `with` block fails:

1. execution immediately leaves the body of the `with`
2. `__exit__()` is called
3. rollback occurs
4. because `__exit__()` returns `None`, the original exception propagates

Important distinction:

> Cleanup or rollback does not mean the exception has been handled.

---

### Dangerous suppression

```python
def __exit__(self, exc_type, exc_value, traceback):
    if exc_type:
        self.rollback()

    return True
```

This suppresses all exceptions.

That can lead to code continuing after a failed transaction:

```python
with Transaction():
    save_order()
    validate_payment()   # raises

send_confirmation()      # may still run
```

The statements after the failing statement **inside the `with` block do not execute**. If the exception is suppressed, execution resumes **after the `with` block**.

---

### Cleanup failures

If:

1. application code raises `PaymentGatewayError`
2. `__exit__()` attempts rollback
3. rollback itself raises another exception

then the rollback failure may become the exception that propagates outward, while the original exception remains in the exception context.

Production code must therefore reason about:

- which failure is the root cause
- whether cleanup can fail
- whether cleanup failure should obscure the original error
- logging and exception chaining

### Key rule

For resource-management context managers, the normal default is:

```python
return False
```

or no explicit return.

Only suppress exceptions when suppression is an intentional part of the abstraction's contract.

---

## 4. Generators Do Not Guarantee Bounded Memory

A generator provides **lazy value production**.

It does not guarantee that the whole processing pipeline uses bounded memory.

Example:

```python
def numbers():
    for i in range(1_000_000):
        yield i
```

This is lazy:

```python
for value in numbers():
    process(value)
```

But this is not bounded-memory:

```python
values = list(numbers())
```

because the consumer materialises all values.

---

### Common causes of unbounded memory

#### Materialising the generator

```python
list(generator)
tuple(generator)
set(generator)
sorted(generator)
```

#### Accumulating results

```python
results = []

for row in rows():
    results.append(process(row))
```

#### Hidden eager loading

```python
def export_orders(session):
    orders = session.execute(query).scalars().all()

    for order in orders:
        yield order
```

The function uses `yield`, but `.all()` has already loaded the complete result set.

#### Growing internal state

```python
def export_orders(session):
    cache = {}

    for order in stream_orders(session):
        cache[order.id] = order
        yield order
```

Memory still grows with the dataset.

#### Large generator locals

A suspended generator retains its local variables.

```python
def generate():
    huge_lookup = load_large_lookup()

    for row in get_rows():
        yield transform(row, huge_lookup)
```

`huge_lookup` remains alive while the generator remains alive.

---

### End-to-end bounded-memory reasoning

For backend streaming, examine every stage:

```text
Database
   ↓
DB driver
   ↓
ORM
   ↓
Repository
   ↓
Generator
   ↓
Serializer
   ↓
HTTP framework / middleware
   ↓
Network
   ↓
Client
```

Any stage may buffer the complete dataset.

### Key rule

Do not ask only:

> “Is this implemented with a generator?”

Ask:

> “What is the maximum amount of live data retained at every stage of the pipeline?”

---

## 5. Lifecycle of Partially Consumed Generators

A generator is a suspended execution frame with live state.

Example:

```python
def rows():
    resource = acquire()

    try:
        for row in resource:
            yield row
    finally:
        resource.close()
```

When the generator reaches a `yield`, execution is suspended and its frame remains alive.

---

### `break` does not necessarily close a generator

```python
orders = iter_orders(session)

for order in orders:
    if order.id == target_id:
        break
```

`break` stops the loop.

It does not mean:

```python
orders.close()
```

If `orders` remains referenced, the generator remains alive.

Its frame may retain:

- a DB cursor
- a session or connection
- the current row
- transaction state
- large local objects

---

### Explicit `close()`

Generators support:

```python
generator.close()
```

Calling `close()` injects `GeneratorExit` at the generator's suspension point.

This causes `finally` blocks to execute:

```python
def rows():
    cursor = acquire_cursor()

    try:
        yield from cursor
    finally:
        cursor.close()
```

---

### Garbage collection is not a lifecycle strategy

In CPython, reference counting often causes a generator to be destroyed quickly after its last reference disappears.

This can hide bugs during development.

Do not rely on this for production resource correctness because:

- the reference may remain alive
- reference cycles can delay collection
- other Python implementations can have different collection timing

Deterministic cleanup should be explicit.

---

### Connection pool impact

A partially consumed generator can hold a pooled DB connection.

If many concurrent requests do this:

```text
partially consumed generators
        ↓
retained DB connections
        ↓
connection pool exhaustion
        ↓
requests block or time out
```

This can cause:

- latency spikes
- pool acquisition timeouts
- failed requests
- apparently unexplained DB pressure

even if the database itself is healthy.

---

### Useful rule for review

When a generator owns resources, ask:

> “Who closes this generator if the consumer stops before exhaustion?”

“Garbage collection will eventually handle it” is generally not a sufficient answer for database, file, or network resources.

---

## 6. JSON vs NDJSON for Streaming

### Standard JSON array

A conventional API response may be:

```json
[
  {"id": 1},
  {"id": 2},
  {"id": 3}
]
```

This is one JSON document.

A naive implementation:

```python
orders = list(iter_orders())
return json.dumps(orders)
```

loads everything before returning the response.

JSON itself can technically be streamed, but maintaining array syntax and whole-document semantics makes incremental processing more complicated.

---

### NDJSON

NDJSON means **Newline-Delimited JSON**.

Each line is an independent JSON record:

```text
{"id":1}
{"id":2}
{"id":3}
```

Conceptually:

```python
def stream_orders(orders):
    for order in orders:
        yield json.dumps(order) + "\n"
```

This naturally supports record-by-record processing.

---

### Framing

Streaming formats need a way to determine where one record ends and another begins.

NDJSON uses:

```text
JSON object + newline
```

as the framing rule.

That makes incremental parsing straightforward.

---

### NDJSON does not guarantee bounded memory

Even with NDJSON, memory can still grow if:

- SQLAlchemy calls `.all()`
- the DB driver buffers everything
- middleware buffers the response
- serialization creates large intermediate structures
- the consumer stores all records
- the client library reads the whole response before processing

Bounded memory remains an end-to-end property.

---

### Partial failure

Suppose the server has already returned:

```text
{"id":1}
{"id":2}
{"id":3}
```

and then PostgreSQL fails.

With NDJSON, the first three records are still individually valid.

With a partially produced JSON array:

```text
[
{"id":1},
{"id":2},
{"id":3},
```

the overall JSON document is invalid.

However, NDJSON introduces an application-level question:

> What should the consumer do with already processed records when the stream fails?

---

### HTTP status after streaming begins

Once response headers have been sent with:

```text
HTTP 200
```

the server generally cannot later convert that response into:

```text
HTTP 500
```

after a mid-stream failure.

Streaming APIs therefore need deliberate error semantics.

Possible concerns include:

- connection termination
- error records
- incomplete results
- resumability
- retry behaviour
- duplicate processing

---

### Retry correctness

If a client successfully processes 500,000 records and then retries the whole export from the beginning, those records may be processed twice.

Large streaming APIs may therefore need:

- stable ordering
- cursors
- checkpoints
- resume tokens
- idempotent consumers

---

### Streaming vs pagination

For ordinary interactive APIs, pagination is usually preferable.

Example:

```http
GET /orders?limit=50&cursor=...
```

Advantages:

- bounded response size
- short-lived requests
- simpler retries
- simpler error semantics
- easier caching
- better fit for UI/API consumption

Streaming is more appropriate for:

- exports
- ETL
- bulk processing
- large record-oriented transfers

### Key rule

Use pagination for ordinary collection APIs unless there is a specific reason to maintain a long-lived streaming response.

---

# Cross-Topic Production Mental Model

These six topics fit together:

```text
Protocol
    ↓
express minimum required capability

Iterable / Collection / Sequence
    ↓
express precise collection semantics

Context manager
    ↓
manage resources deterministically

Generator
    ↓
produce values lazily

Generator lifecycle
    ↓
handle early termination safely

NDJSON / streaming framing
    ↓
preserve incremental processing across HTTP
```

The main production lesson is that abstractions, resource ownership, memory usage, and failure semantics must be considered together.

---

# Must Know

- `Protocol` enables static structural typing without explicit inheritance.
- Protocol compatibility depends on compatible members, not just member names.
- `@runtime_checkable` does not provide full runtime type/signature validation.
- Prefer the weakest suitable collection abstraction.
- `Collection` does not guarantee ordering or indexing.
- `Sequence` does not guarantee mutability.
- Truthy `__exit__()` return values suppress exceptions.
- Cleanup and exception suppression are separate concerns.
- `yield` provides laziness, not bounded memory.
- `break` does not imply deterministic generator closure.
- Partially consumed generators can retain DB cursors and connections.
- NDJSON is naturally suited to record-oriented streaming.
- Streaming failures require explicit retry and partial-result semantics.

---

# Useful in Production

- Capability-oriented interfaces using `Protocol`
- `MutableSequence` when mutation is actually required
- deterministic resource cleanup for generator-backed DB access
- checking ORM/driver fetch behaviour rather than trusting `yield`
- stable cursors/checkpoints for resumable exports
- idempotent consumers when streamed processing may be retried
- explicit analysis of backpressure and buffering across the full stack

---

# Common Code Review Smells

Watch for:

```python
def __exit__(...):
    ...
    return True
```

without an explicit suppression requirement.

```python
def stream_rows(...):
    rows = query.all()
    yield from rows
```

which only looks like streaming.

```python
payload = list(generator)
```

which defeats lazy production.

```python
for row in resource_owning_generator:
    if condition:
        break
```

without clear ownership of generator cleanup.

```python
def process(values: list[int]):
    ...
```

when only `Iterable`, `Collection`, or `Sequence` behaviour is required.

And claims such as:

> “It uses a generator, therefore it is memory efficient.”

without examining the entire pipeline.

---

# Revision Checklist

- [ ] Explain structural vs nominal typing.
- [ ] Define a useful backend `Protocol` without making implementations inherit from it.
- [ ] Choose correctly between `Iterable`, `Collection`, `Sequence`, and `MutableSequence`.
- [ ] Explain exactly when `__exit__()` suppresses an exception.
- [ ] Explain why rollback does not itself imply exception suppression.
- [ ] Identify eager materialisation hidden behind a generator API.
- [ ] Explain how a suspended generator retains local state.
- [ ] Explain what `generator.close()` and `GeneratorExit` do.
- [ ] Explain why partially consumed generators can exhaust a DB connection pool.
- [ ] Explain NDJSON framing.
- [ ] Explain why NDJSON does not by itself guarantee bounded memory.
- [ ] Explain failure and retry problems after a streaming response has already started.
