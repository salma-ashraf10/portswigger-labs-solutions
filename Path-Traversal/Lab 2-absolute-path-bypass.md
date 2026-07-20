# Lab 2 : File path traversal, traversal sequences blocked with absolute path bypass

**Platform:** PortSwigger Web Security Academy

**Category:** Path Traversal (PRACTITIONER)

<img width="1082" height="208" alt="image" src="https://github.com/user-attachments/assets/7033f1e0-4cbe-464d-bedf-2a84c91586a5" />

## Lab Description

This lab contains a path traversal vulnerability in the display of product images.

The application blocks traversal sequences but treats the supplied filename as being relative to a default working directory.

To solve the lab, retrieve the contents of the /etc/passwd file.

## Steps

### 1. Inspecting the Website

First, I accessed the website and found several posts.

I opened any post and noticed there was an image.

I opened the image in a new tab.

The URL was:
```
https://0a3700d6040714ea8056a875007c00d2.web-security-academy.net/image?filename=8.jpg
```

### 2. Testing Path Traversal

I noticed there was a filename parameter used to load the image.

I tried to directly access the /etc/passwd file:

```
https://0a3700d6040714ea8056a875007c00d2.web-security-academy.net/image?filename=/etc/passwd
```

### 3. Retrieving the File

The server returned the contents of the /etc/passwd file.

Voilaaaaaaa!!

The file was successfully retrieved and the lab was solved.


<img width="1250" height="780" alt="image" src="https://github.com/user-attachments/assets/1e57bf49-db82-469e-bc5e-34a91b48d990" />



#
**Lab Link:** [Open the lab](https://portswigger.net/web-security/file-path-traversal/lab-absolute-path-bypass)

See you in the next lab solution, In sha Allah!


سبحانك اللهم وبحمدك، أشهد أن لا إله إلا أنت، أستغفرك وأتوب إليك.
