# Lab 2: DOM XSS via client-side prototype pollution

**Platform:** PortSwigger Web Security Academy

**Category:** Prototype Pollution (PRACTITIONER)

<img width="995" height="191" alt="image" src="https://github.com/user-attachments/assets/90e2e5d3-26ab-44bd-994b-7bf32386d59e" />

## Lab Description

This lab is vulnerable to DOM XSS via client-side prototype pollution. To solve the lab:

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

I found a potential DOM XSS gadget that uses a property called: transport_url

The application uses this property to dynamically create a <script> element.

This means that if I can control the value of transport_url, I may be able to control the src attribute of the created script.

Unlike the previous lab, this time the application does not properly protect the transport_url property with non writable or non configurable restrictions.

Therefore, I could directly target the transport_url property through prototype pollution.


### 3. Exploiting the Gadget

I used:
```
/?__proto__[transport_url]=data:,alert(1)
```

This effectively pollutes:

Object.prototype.transport_url = "data:,alert(1)";

The gadget then uses the polluted transport_url value when creating the script element.

The result in HTML:
```
<script src="data:,alert(1)"></script>
```

The browser executes the JavaScript from the 'data:' URL:
```
alert(1)
```

Voilaaaa!! 

The alert appeared and the lab was solved.

#

**Lab Link:** [Open the lab](https://portswigger.net/web-security/prototype-pollution/client-side/lab-prototype-pollution-dom-xss-via-client-side-prototype-pollution)

See you in the next lab solution, In sha Allah!

سبحانك اللهم وبحمدك، أشهد أن لا إله إلا أنت، أستغفرك وأتوب إليك.
