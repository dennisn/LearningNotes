# FastAPI Module 2 — Quick Revision

## Request models

- A handler parameter typed as a Pydantic model is read from the JSON request body.
- FastAPI validates the body before executing the handler.
- Invalid request data normally returns `422 Unprocessable Content`.
- Constructing a request model validates input; it does not mean an order was persisted.

## Required, omittable, and nullable

| Declaration | Can be omitted? | Accepts JSON `null`? |
|---|---:|---:|
| `value: str` | No | No |
| `value: str | None` | No | Yes |
| `value: str | None = None` | Yes | Yes |
| `value: str = "default"` | Yes | No |

`| None` controls accepted values. A default such as `= None` controls omission.

## Constraints and nested models

Use `Annotated` with `Field` for boundary constraints:

```python
reference: Annotated[str, Field(min_length=3, max_length=30)]
quantity: Annotated[int, Field(gt=0)]
items: Annotated[list[OrderItemRequest], Field(min_length=1)]
```

- Nested models are validated recursively.
- An error location such as `["body", "items", 0, "quantity"]` identifies the exact invalid field.
- Length validation counts characters, so three spaces satisfy `min_length=3` unless whitespace is separately handled.
- A valid `product_id` type does not prove that the product exists.

## Request versus response contracts

Use separate models because clients should not supply server-controlled fields such as `order_id`, timestamps, or initial status.

`response_model`:

- Documents the public response contract.
- Validates and serialises the handler result.
- Filters undeclared output fields.
- Produces a server error if application code returns an invalid response; this is not a client `422` error.

Do not rely on response filtering as the primary protection for secrets. Sensitive values may still reach logs, tracing, middleware, or exception reports.

## PATCH: omitted versus explicit `null`

```python
class OrderUpdate(BaseModel):
    model_config = ConfigDict(extra="forbid")

    notes: str | None = None
```

Apply only fields the client supplied:

```python
changes = update.model_dump(exclude_unset=True)
```

| Request | Meaning | `changes` |
|---|---|---|
| `{}` | Leave notes unchanged | `{}` |
| `{"notes": null}` | Clear existing notes | `{"notes": None}` |
| `{"notes": "Call first"}` | Replace notes | `{"notes": "Call first"}` |

Do not use `exclude_none=True` when explicit `null` means “clear this value”. `extra="forbid"` rejects unsupported fields such as client-supplied status changes instead of silently ignoring them.

## Validation boundaries

- **Pydantic/FastAPI:** shape, types, declared value and collection constraints.
- **Business/service layer:** allowed state transitions and domain invariants.
- **Persistence layer:** existence, uniqueness, foreign keys, and other database-enforced invariants.
- **Authorisation:** whether the current user may perform the operation.

Important invariants may need protection beyond the HTTP schema because scripts, workers, and other interfaces can bypass FastAPI.
