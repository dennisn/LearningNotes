# GraphQL Foundations

- Course URL: <https://app.pluralsight.com/ilx/video-courses/graphql-foundations/course-overview>

## What it is and Why it matters

- REST Api: multiple endpoints, each with fixed shape & specific data
  - For a combination of data, must do multiple queries & stich them together on client side
- GraphQL: one end point, and client defines what data is needed --> API return json matching the request
  - Sample

  ```graphql
  # Query: all name & price of pies belong to category "seasonal"
  category(id: "seasonal") {
    pies{
        name
        price
    }
  }
  ```

- Differences to "**REST**":
  - Shorter developer time, schema evolves without versioning
  - Needs additional HTTP caching strategy (vs. HTTP caching is automatic with REST API)
- Why GraphQL:
  - Strongly typed: its schema schould have types for all objectes that it uses --> better developer experience
    - self document API via schema
    - strong tools & plugins based on schema
  - Easier API evolution: adding enw fields & types to API without breaking
- Trade-offs
  - Single endpoint bottleneck (i.e. caching)
  - Security: no specific process to ensure security
- Use cases: complex data-heavy apps, or multiple clients needing different data shapes
  - REST & GraphQL can co-exist

## Core GraphQL Concepts in Action

- Source code: <https://github.com/adhithiravi/GraphQL-Foundations.git>
  - Can start from scratch with:
    - Install Node.js
    - New project folder
    - Install graphql and graphql-yoga (documentation: <https://the-guild.dev/graphql/yoga-server/docs>)
    - Command: `npm init -y; npm install graphql graphql-yoga;`
- Types: built-in scalar or enum & custom types
  - By default, scalar type can be set to null --> to make it not null, use trailing '!': `price: Float!`
  - List of type Pie: `type Pies { id: ID!, pies: [Pie]! }` --> for non-null list of Pie: `[Pie!]!`
- Schema: shape of your data --> contract between the client & server
  - Each schema has 2 main attributes: Query and Mutation
    - `query: Query`: Required, equivalent to the "get" operation in REST
    - `mutation: Mutation`: Optional, equivalent to the "write" operation
- Query: handled by resolvers
  - Can also use alias to distinguish different query results

  ```graphql
  query myQuery {
    pies {
        name
        price
    }
  }

  query myNewQuery {
    fruitPies: getPies(category: "fruit") {
        pies {
            name
            price
        }
        count
    }
    seasonalPies: getPies(category: "seasonal") {
        pies {
            name
            price
        }
        count
    }
  }
  ```

- Fragments: re-usable items in GraphQL

  ```graphql
  fragment PieInfo on Pie {
    name
    price
    averageRating
  }

  query myNewQuery {
    fruitPies: getPies(category: "fruit") {
        pies {
            ...PieInfo
        }
    }
    seasonalPies: getPies(category: "seasonal") {
        pies {
            ...PieInfo
        }
    }
  }
  ```

## Dynamic Queries and Mutations in GraphQL

### Dynamic Queries

Dynamic query: by extract variables with types out of the query itself

Benefits
- Increase reusability and security (i.e. prevent injection attack)
- Type safety: variable is of the correct type
- Better caching at client

```graphql
# extract "category" value as a variable outside of the query
query getPiesByCategory($category: String) {
  getPies(category: $category) {
      pies {
          name
          category
          price
      }
  }
}
```

### Mutation

- GraphQL assumes side-effects after mutations, and changes the dataset after a mutation
- Query fields are executed in parallel, mutation fields are executed in sequence

```graphql
type Mutation {
    addPie(input: PieInput!): Pie!
}

input PieInput {
    name: String!
    price: Float!
    inStock: Boolean!
    averageRating: Float
    category: String
}
```

- The query is like following

```graphql
# Will add the new pie, then return its field as requested
mutation myMutation {
    addPie(input: {name: "New Choco", price: 4.4, inStock: true}) {
        id
        name
        price
    }
}
```

## Real-time Data with Subscriptions

- GraphQL sepcification: allows to receive real-time updates via subscription (i.e. long-lived requests)
  - using WebSockets or Server‑Sent Events (SSE)
- Trade-offs:
  - Very complex: maintain context over the lifetime of subscription, race conditions between subscription & updated data
  - For data with less frequent update --> polling, or mobile push notifications maybe better
