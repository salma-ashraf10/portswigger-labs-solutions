# Lab 6 : File path traversal, validation of file extension with null byte bypass

**Platform:** PortSwigger Web Security Academy

**Category:** Path Traversal (PRACTITIONER)

<img width="1007" height="222" alt="image" src="https://github.com/user-attachments/assets/c4aed3e3-a26a-4e3d-8867-b631df31594c" />


## Lab Description

This lab contains a path traversal vulnerability in the display of product images.

The application validates that the supplied filename ends with the expected file extension.

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

The application validates that the filename ends with the expected extension (jpg).

So I needed a way to bypass this validation.

I used a null byte (%00) to terminate the filename.

The payload:

```
/image?filename=../../../etc/passwd%00.jpg
```
The application sees the filename as ending with (.jpg)

but after processing the null byte, the actual requested file becomes:
```
../../../etc/passwd
```

### 3. Retrieving the File

The server returned the contents of the /etc/passwd file.

Voilaaaaaaa!!

The file was successfully retrieved and the lab was solved.

<img width="1257" height="738" alt="image" src="https://github.com/user-attachments/assets/914f1a11-2258-4004-932d-880016e0bc61" />


#
**Lab Link:** [Open the lab](https://portswigger.net/web-security/file-path-traversal/lab-validate-file-extension-null-byte-bypass)

See you in the next lab solution, In sha Allah!


سبحانك اللهم وبحمدك، أشهد أن لا إله إلا أنت، أستغفرك وأتوب إليك.
