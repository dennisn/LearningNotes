# FastAPI Module 5 — Persistence and transactions

Date: 15 September 2026  
Status: Core module demonstrated; idempotency extension partly demonstrated, with one reliability test deferred.

## Quick revision

- Share a SQLAlchemy `Engine` and session factory for the application lifetime; create and close a separate synchronous `Session` per request. Dispose of the engine at shutdown. Session cleanup does not decide whether business changes commit.
- Keep SQLAlchemy ORM records separate from Pydantic request/response schemas. `ConfigDict(from_attributes=True)` reads ORM attributes into an API response. Server-owned fields such as order ID and status are not create-request inputs.
- Own the complete order-plus-items write in one `with session.begin():` block. `flush()` sends inserts and can assign an ID, but only a successful commit persists them. An exception crossing the block rolls back all inserts. Session cleanup may emit `ROLLBACK` after an uncommitted read; it need not do so after a completed commit.
- `expire_on_commit=False` kept committed attributes available for response conversion without a post-commit reload. This trades automatic freshness for fewer queries within a short request.
- The named `CHECK` constraint rejects unsupported statuses in the database. `create_all()` creates missing tables but does not migrate existing ones. Reviewed Alembic revisions created `orders`, then `order_items`, and later added a nullable, unique `idempotency_key`. Run migrations as a controlled deployment step, not from every worker at startup.
- Bound listing in SQL using `ORDER BY order_id`, `LIMIT` and `OFFSET`; validate parameters with `Query(ge=1, le=100)` and `Query(ge=0)`. `limit=101` returned `422` without an orders query.
- Default lazy loading produced one orders query plus one items query per order (N+1). `selectinload(OrderRecord.items)` reduced the observed three-order listing to two queries, with an items `IN (1, 2, 3)` query. `item_count` is a typed property using `len(self.items)`; eager loading avoids extra item queries in the list route.
- `cascade="all, delete-orphan"` adds associated items with their parent, deletes them when the parent is deleted, and deletes an item removed from its owning collection at flush.

## Evidence actually observed

- Two created orders committed with distinct generated IDs. An induced failure **after the parent and two items were flushed** logged three inserts and one rollback; a database view confirmed no committed records for that attempt.
- Alembic applied the initial orders revision `c861d52e4f5e`; `alembic current` reported it as head at that point. An invalid status raised `IntegrityError` and rolled back; `pending` committed.
- Pagination logged `LIMIT 2 OFFSET 1`; invalid `limit=101` produced `422` and no orders `SELECT`.
- Identical requests without a key created duplicate orders. With a unique key, sequential replay returned `201` then `200` for the same ID and one stored order; changed quantity and reversed item order returned `409`. A barrier-forced SQLite race showed two no-match lookups, one commit, and responses `201`/`200` for the same ID. This local interleaving does not prove PostgreSQL locking behaviour.

## Open items and next prompt

- **Deferred by request:** The `IntegrityError` handler checks whether the driver message contains `"UNIQUE"`, but that does not isolate the `orders.idempotency_key` constraint. Test that an unrelated integrity failure is re-raised rather than incorrectly replayed. This extension is practising, not fully demonstrated.
- Item order is currently inferred from `order_item_id` for the teaching API; persist explicit position if reordering or imports become part of the contract.
- Module 4's fake-dependency override execution was not recorded in the earlier handoff.
- Module 6 Lesson 1 has been introduced, but its temporary sync-versus-async execution exercise has not been attempted. **Exact next prompt:** “Continue FastAPI Module 6: I’ll add `def` and `async def` routes that report thread ID and whether an event loop is running, then paste my predictions, code and results.”

Files changed in your local project: filenames were not provided; code and Alembic revisions were shown and executed by you. No project source or progress file was edited for this summary.
