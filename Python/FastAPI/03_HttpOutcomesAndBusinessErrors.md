# FastAPI Module 3 — HTTP Outcomes and Business Errors

## Core idea

HTTP responses should tell clients unambiguously whether an operation succeeded, the requested resource was absent, the request failed validation, or a valid operation conflicted with current business state.

## Status codes used

| Status | Meaning in the order API |
| --- | --- |
| `200 OK` | Retrieval or cancellation succeeded. |
| `201 Created` | A new order was created. |
| `404 Not Found` | The supplied ID was valid, but no order exists. |
| `409 Conflict` | The request is valid, but the order's current state prevents the operation. |
| `422 Unprocessable Content` | FastAPI could not validate or parse the request input. |
| `500 Internal Server Error` | The server violated its response contract or encountered an unexpected defect or infrastructure failure. |

## Creation response

Declare the consistent success status on the route and set `Location` to the newly created resource:

```python
@app.post(
    "/orders",
    response_model=OrderResponse,
    status_code=status.HTTP_201_CREATED,
)
def create_order(order: OrderRequest, response: Response):
    order_id = 42
    response.headers["Location"] = f"/orders/{order_id}"
    return created_order
```

`Location` should identify `/orders/{order_id}`, not merely the collection `/orders`. An invalid request does not create anything and must not receive this header.

## Retrieval and not-found handling

An integer annotation validates the shape of an ID; it does not prove the order exists.

```python
known_order = ORDERS.get(order_id)
if known_order is None:
    raise HTTPException(
        status_code=status.HTTP_404_NOT_FOUND,
        detail=f"Order not found for id: {order_id}",
    )
```

- `/orders/abc` fails FastAPI path validation and never reaches the handler (`422`).
- `/orders/99` reaches the handler, but an absent order produces a deliberate `404`.
- Returning `None` could misleadingly produce `200` with `null`, or cause response validation to fail when a non-nullable response model is declared.

## Response models

`response_model=OrderResponse` defines the external output contract:

- Extra internal fields are filtered from the response.
- Missing required response fields are a server-side contract failure, normally resulting in `500`.
- A response model does not determine whether an order exists or whether an operation is allowed.

## Business conflict: cancellation

The declared rule was `pending → cancelled`. A dedicated operation avoids letting clients assign arbitrary statuses:

```text
POST /orders/{order_id}/cancel
```

- Missing order: `404`.
- Existing non-pending order: `409`.
- Pending order: update to `cancelled` and return `200`.

FastAPI does not know this transition rule. Application code must enforce it.

## Service errors versus HTTP errors

Keep business logic independent of FastAPI:

```python
class OrderNotFoundError(Exception):
    pass


class OrderStatusConflictError(Exception):
    pass
```

The service raises these application-specific exceptions. The route catches only the expected exceptions and maps them to HTTP outcomes:

```text
OrderNotFoundError       → 404
OrderStatusConflictError → 409
```

Do not broadly catch `Exception` and convert everything to `409`. A `KeyError`, programming defect, or database failure is not automatically a business conflict; unexpected failures should remain server errors.

## Boundary rule to remember

**FastAPI validates and represents HTTP; the service enforces business rules.**

The in-memory `ORDERS` dictionary remains a teaching shortcut. Dependency injection, persistence, concurrency safety, and transaction handling are introduced in later modules.
