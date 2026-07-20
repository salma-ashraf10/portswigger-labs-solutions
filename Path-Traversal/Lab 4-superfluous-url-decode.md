# Lab 4 : File path traversal, traversal sequences stripped with superfluous URL-decode

**Platform:** PortSwigger Web Security Academy

**Category:** Path Traversal (PRACTITIONER)

<img width="1091" height="256" alt="image" src="https://github.com/user-attachments/assets/df9a31eb-9587-41b7-8a87-56482394f05f" />


## Lab Description
This lab contains a path traversal vulnerability in the display of product images.

The application blocks input containing path traversal sequences. It then performs a URL-decode of the input before using it.

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

The application blocks normal path traversal sequences:
```
../
```

and then performs URL decoding before using the input.

So the idea is to bypass the filter by sending an encoded payload.

The payload:
```
filename=%252e%252e%252f%252e%252e%252f%252e%252e%252fetc/passwd
```

The application does not detect it because the request does not contain ../


After the server performs URL decoding, it becomes:
```
../../../etc/passwd
```

which results in a successful path traversal.


### 3. Retrieving the File

The server processes the decoded path and returns the contents of:
```
/etc/passwd
```

Voilaaaaaaa!!

The file was successfully retrieved and the lab was solved.


#
**Lab Link:** [Open the lab](https://portswigger.net/web-security/file-path-traversal/lab-superfluous-url-decode)

See you in the next lab solution, In sha Allah!


سبحانك اللهم وبحمدك، أشهد أن لا إله إلا أنت، أستغفرك وأتوب إليك.
