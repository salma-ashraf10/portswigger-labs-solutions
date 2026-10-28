# Lab 1: Detecting NoSQL Injection

**Platform:** PortSwigger Web Security Academy

**Category:** NoSQL Injection (APPRENTICE)

<img width="673" height="179" alt="image" src="https://github.com/user-attachments/assets/e79d321a-cb90-4cdb-99a5-8682c2676cfd" />

## Lab Description

The product category filter for this lab is powered by a MongoDB NoSQL database. It is vulnerable to NoSQL injection.

To solve the lab, perform a NoSQL injection attack that causes the application to display unreleased products. 

## Steps

### 1. Testing the Category Filter

First, I navigated to the product listing page and filtered the products by a category, such as Accessories.

The application used the following endpoint:

```
/filter?category=Accessories
```

While testing the category parameter, I wondered what would happen if I appended a single quote (`'`) to the category value.

I modified the URL to:

```
/filter?category=Accessories%27
```

Here, `%27` represents a URL-encoded single quote.

After sending the request, the application returned a database error:

```text
Command failed with error 139 (JSInterpreterFailure):
SyntaxError: unterminated string literal
```
<img width="1447" height="448" alt="image" src="https://github.com/user-attachments/assets/c86e9cdb-25af-40a9-999c-b9eca5f91243" />

The error also referenced MongoDB's JavaScript expression parser.

This indicated that the injected quote was interfering with the application's query or expression parsing. It was a useful sign of a potential NoSQL injection vulnerability.

### 2. Exploiting the NoSQL Injection

I decided to try MongoDB's $ne operator, which matches values that are not equal to a specified value.

**Payload:**

```text
category[$ne]=s
```

The resulting URL was:

```
/filter?category[$ne]=s
```

Depending on the request parser, the brackets and dollar sign can also be URL-encoded:

```
/filter?category%5B%24ne%5D=s
```

### 3. Understanding the Payload

The payload uses MongoDB's `$ne` operator to change the filtering condition.

- `category` is the original parameter used to filter products.
- `$ne` means *not equal*.
- `s` is the value being compared against.


Instead of matching a category equal to a specific value, the query matches values that are not equal to `s`.

This changes the behavior of the original category filter and allows products from other categories to appear in the results.

The application returned the products matching the manipulated filter, including the unreleased products.

<img width="1142" height="452" alt="image" src="https://github.com/user-attachments/assets/0995e652-49b3-4d80-af14-849e933e5994" />

Alhamdulillah, the lab was solved successfully!

<img width="1099" height="327" alt="image" src="https://github.com/user-attachments/assets/1ad7eb05-81ec-40d2-a2e1-64a2f8ff05f6" />

---

**Lab Link:** [Open the lab](https://portswigger.net/web-security/nosql-injection/lab-nosql-injection-detection)

See you in the next lab solution, In sha Allah!

سبحانك اللهم وبحمدك، أشهد أن لا إله إلا أنت، أستغفرك وأتوب إليك.
