# Learning FastAPI
Learning FastAPI with ChatGPT

## 1 — Routes and paramaeter binding

**Purpose:** Understand how a Python function becomes an HTTP endpoint.

[Routes and paramaeter binding](./01_RoutesAndParametersBinding.md):
1. The application instance, route decorator, handler, and JSON response.
2. Path parameters, query parameters, defaults, and required values.
3. Parsing and validation at the HTTP boundary; interactive documentation.
4. The difference between direct Python calls and HTTP requests.

## 2 — Request and response contracts

**Purpose:** Separate external API contracts from internal data.

[Request and response contracts](./02_RequestAndResponseContracts.md):
1. Pydantic request models and JSON request bodies.
2. Constraints, nested item models, defaults, and nullable versus optional inputs.
3. Response models and fields that should never be exposed.
4. Partial updates and omitted fields versus explicit nulls.

## 3 — HTTP outcomes and business errors

**Purpose:** Make success and failure unambiguous to clients.

[HTTP outcomes and business errors](./03_HttpOutcomesAndBusinessErrors.md):
1. Creation responses and the Location header.
2. Not-found errors and HTTPException.
3. Business conflicts, invalid transitions, and validation errors.
4. Mapping service-layer exceptions at the HTTP boundary.

## 4 — Dependencies and resource scopes

**Purpose:** Relate FastAPI dependency resolution to constructor injection and resource ownership.

[Dependencies and resource scopes](./04_DependenciesAndResourceScopes.md):
1. Depends and Annotated; dependencies that return values.
2. Dependency composition and per-request caching behaviour.
3. Dependencies using yield for resource cleanup.
4. Request scope versus application lifespan.
