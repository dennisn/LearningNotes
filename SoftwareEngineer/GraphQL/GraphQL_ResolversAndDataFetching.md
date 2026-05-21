# GraphQL: Resolvers and Data Fetching

- URL: <https://app.pluralsight.com/ilx/video-courses/graphql-resolvers-data-fetching/course-overview>

## Get Started

- Demo using Apollo server library in NodeJS, with SQLite as data source
  - Initialization: `npm init -y && npm pkg set type="module"`
  - Library installation: `npm i better-sqlite3`
- Resolver functions are called with 4 parameters
  - `obj` or `parent`: the previous object --> undefined for top-level query
  - `args`: the arguments provided to the field in GraphQL operation
  - `context`: contextual information provided to every resolver
  - `info`: field-specific information relevant to the current operation & schema details

## The "parent" Parameter

The `parent` parameter: enable nested query --> contain the *whole* parent object, not just those attributes selected by query

## The "args" Parameter

The `args`: enables fmore specific data selection (e.g. object ID, or filter criteria)

- Also useful for mutation (i.e. create/update)
- **Create mutation**: often fields are required
- **Update mutation**: fields should be optional, need extra ID to identify update record

## The "context" Parameter

The `context` carries custom data that are useful to resolvers (i.e. available fwith every step of the request's journey through GraphQL)

- Initiated once for every incoming request --> garbage collected after the response is sent
  - Initialization at the GraphQL server level
- Common use cases:
  - Database context/repository
  - Header token/request user
  - Cache data: useful since GraphQL often request the same data multiple time in subquery
  - Others: Logging,  API testing, error handling with context aware, 3rd party service integration

## The "info" Parameter

The `info` parameter holds the entire abstract syntax tree for the GraphQL query

- Key to fine-grained customization, but implementation specific
- Example: instead of retrieve the whole db rows --> use `info.fieldNodes[0].selectionSet.selections.map((s) => s.name.value).join(", ")` to selec only relevant columns ==> may not works when "computed fields" are used

## Review Recommended Practices

- Removing things like SQL statements from resolvers
- Breaking up resolvers for codebase grows/scale
- Implement good authentication & authorization with `context` parameter
- Limit query complexity & depth with `info` parameter/3rd party libraries --> could be an attack vector
- Logging & performance monitoring
- Use GraphQL's custom error handling
- Limit results & offer pagination, often with `args` parameter
- Request only fields being queried
- Batching & de-dupe requests when loading data (e.g. using data loader)
