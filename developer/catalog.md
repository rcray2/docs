# The Maana Catalog

The Maana Catalog is the repository for all components that can be used as part of MAANA knowledge solutions.  The following section discusses how to  add/publish/update/delete components in the catalogue.

## Defining a Model in Maana

The following documentation uses *XXX.XXX.XXX.XXX* facing to represent the public URL of the system. All of the following examples use the GraphiQL endpoint - which is interactive, programmatic access conducted through the GraphQL endpoint.  GraphQL API is organized around three main building blocks:
1. Schema
2. Queries
3. Resolvers

Queries can be executed via GET or POST, with either the body of the query as the query string or in the body of the POST. All mutations must use the POST endpoint and have the header content type set to application/json. Also, the GraphQL server needs a resolver to know what to do with an incoming query.

![](/Users/tmelhuish/Documents/testing/docs/assets/animations/graphqlQuery.png)
Source : [Anatomy of a GraphQL Query](https://blog.apollographql.com/the-anatomy-of-a-graphql-query-6dffa9e9e747)

The following is an example of how to create a graph with no mutation specified, using the cURL command line tool for getting or sending files using URL syntax.

```
curl -v -H "Content-Type: application/json" -X POST -d "{\"query\": \"mutation { ..... }\"}" http://XXX.XXX.XXX.XXX:8003/graphql
```

Maana engineering team is currently implementing authentication functionality, which will be required for all clients. (RC TO UPDATE ALL LINKS
*	How to create a graph from a GQL definition (https://confluence.corp.maana.io/display/RD/Maana+Q+programmatic+interface)
*	How to add instance data
*	How to query instance data
*	How to perform complex queries (https://confluence.corp.maana.io/display/RD/Technical+Design+Notes#TechnicalDesignNotes-Queries)
*	How to perform search (https://confluence.corp.maana.io/display/RD/Maana+Q+Search+-+Technical+Design)

## Hydrating the model with Data

*	Create a Service with graphql schema. Types must correspond to Kinds on the portal endpoint XXX.XXX.XXX.XXX:8003/graphiql. Note that the service can have many Kind definitions.

```
mutation {
  addServiceSource(input: {
    name: "TestService",
    schema: "type TestKind { \n id: ID \n name: String! \n something: Int! }"
  })
}
```
* Once the Service has been created, it will return a Service ID:

```
{
  "data": {
    "addServiceSource": "54769aae-2c63-4415-9b34-74e9ceb1d789"
  }
}
```

This ID is used to establish the Service endpoint for subsequent interactions

> XXX.XXX.XXX.XXX:8003/service/54769aae-2c63-4415-9b34-74e9ceb1d789/graphiql

Note: If the service is re-created by updating definitions, its service ID will change, while the old service will still be available.

## Adding a single instance of the kind

```
mutation {
  addTestKind(input: {
    name: "testname",
    something: 42
  })
}
```
* An ID is assigned and returned for the instance itself

```
{
  "data": {
    "addTestKind": "431f9338-95be-4f89-968c-4f2bb197965c"
  }
}
```
* Once the instance data was added, we can query it using:


```
query {
  allTestKinds {
    id
    name
    something
  }
}
```
## Adding multiple data instances simultaneously to a kind

```
mutation addSomethings {
  addTestKinds(input: [{
    name: "testname2",
    something: 45
  }, {
    name: "testname3",
    something: 43
  }
  ])
}
```
Note: Larger data volumes should be broken into reasonably sized chunks (i.e. up to 5,000 instances), and submitted sequentially through the endpoint.

* Data can be queried using:
```
query {
  allTestKinds {
    id
    name
    something
  }
}
```
