# GraphQL: Design and Implement Schemas

- URL: <https://app.pluralsight.com/ilx/video-courses/graphql-design-implement-schemas/course-overview>
- Sample code:
  - Init. project: `npm init -y & pnm pkg set type="module"`
  - library: `npm i graphql @apollo/server`
  - package.json > scripts > "dev": `"node --env-file=.env --watch index.js"`
- Notes
  - May want to disable "introspection", only allow it for development

## Get a Data Graph with Queries

- Related data (aka foreign key): using the `parent` variables in resolver to filter
  
  ```graphql
  export const resolvers = {
    Query: {
        products: () => products,
        users: () => users,
        user: (_, args) => users.find(u => u.id === args.id)
    },
    Product: {
        reviews: (parent) => reviews.filter(r => r.productId === parent.id)
    },
    User: {
        reviews: (parent) => reviews.filter(r => r.userId === parent.id)
    },
    Review: {
        product: (parent) => products.find(p => p.id === parent.productId),
        user: (parent) => products.find(p => p.id === parent.userId),
    }
  }
  ```

- Then we can use a sample query (in production, may need to limit depth size to prevent infinite recursion):

  ```graphql
  query ($id: ID!) {P
    user (id: $id) {
      username,
      reviews {
        text
        product {
            name
        }
      }
    }
  }
  ```

- Enum: define list of valid values
- Interface: define the common attribute
  - Interface can't be returned by the resolver --> resolver have to resolve the "implementation type"

  ```graphql
  User: {
    __resolveType: (obj) => {
        const userTypes = {
            GOLD: "GoldUser",
            PLATINUM: "PlatinumUser",
            DIAMON: "DiamondUser"
        }
    },
    reviews: ...
  }
  ```

- Error handling: for case where the userTypes is missing, may return the `ErrorResult` instead --> still return the remaining good data
  - Could use map to add error message to the ErrorResult
- When writting query, can use alias: `productA: product(id: $product_1_Id) { .. }`

## Mutate Data

- Create user: basic use-case for "Mutation" request with `input` data type
  - `input` data types may contain "hidden" fields not available to query
  - Mutation can return the data record "after" mutation

## Scale and Maintain Schemas

- To scale, typedefs & resolvers can be splitted into different files
  - Need to "merge" them together, probably with "deep_merge" --> **JS solution only**
- Schema evolution: recommended not to version them
  - Add new fields --> not breaking change
  - Removing fields --> breaking change, but can progressively implement with `@deprecated` tag
  