# FastAPI Module 1 — Quick Revision

## Route fundamentals

```python
@app.get("/orders/{order_id}")
def get_order(order_id: int, include_items: bool = False):
    return {"order_id": order_id, "include_items": include_items}
```

- `@app.get(...)` registers the function as a `GET` endpoint.
- A name inside the route path, such as `{order_id}`, becomes a path parameter.
- Other scalar function parameters become query parameters.
- A parameter without a default is required; one with a default may be omitted.
- Returned dictionaries are serialised as JSON responses.

## Parsing and validation

- HTTP path and query values arrive as text.
- FastAPI reads type annotations at runtime to parse and validate them before calling the handler.
- `/orders/123` gives the handler `order_id=123` as an `int`.
- `/orders/abc` fails integer parsing with HTTP `422`; the handler does not execute.
- In an error, `loc`, such as `["path", "order_id"]`, identifies the input source and field.
- `order_id: int` validates the value's type, but does not prove that the order exists or that the caller may access it.
- An annotation alone does not enforce types when the function is called directly from Python; FastAPI's request-processing layer performs the runtime validation.

## Query parameters

```python
@app.get("/orders")
def list_orders(status: str, limit: int = 20, offset: int = 0):
    return {"status": status, "limit": limit, "offset": offset}
```

- `status` is required because it has no default.
- `limit` and `offset` are optional because they have defaults.
- Missing or incorrectly typed query parameters produce HTTP `422` before the handler runs.
- `limit: int` accepts negative integers; rejecting them requires an explicit numeric constraint.

## Route ordering

```python
@app.get("/orders/summary")
def get_order_summary():
    return {"total_orders": 0}

@app.get("/orders/{order_id}")
def get_order(order_id: int):
    return {"order_id": order_id}
```

- Routes are evaluated in registration order.
- Register a fixed route such as `/orders/summary` before a conflicting dynamic route such as `/orders/{order_id}`.
- If the dynamic route matches `summary` first and integer validation then fails, FastAPI returns `422`; it does not try the next route.
- `/orders` does not conflict with `/orders/{order_id}` because the latter requires one additional path segment. Query parameters do not change the path.

## Mental model

`HTTP request → route match → parse and validate inputs → call handler → serialise response`

Keep these concerns separate:

- Framework validation: can the request be parsed into the declared input types?
- Business rules: is the requested operation allowed?
- Persistence: does the referenced order exist?
- Authorisation: may this caller access or modify it?
