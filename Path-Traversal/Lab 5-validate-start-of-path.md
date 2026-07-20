# Lab 5 : File path traversal, validation of start of path

**Platform:** PortSwigger Web Security Academy

**Category:** Path Traversal (PRACTITIONER)

<img width="987" height="187" alt="image" src="https://github.com/user-attachments/assets/bb6df981-8823-4429-ae78-e5d03f9768b3" />


## Lab Description

This lab contains a path traversal vulnerability in the display of product images.

The application transmits the full file path via a request parameter, and validates that the supplied path starts with the expected folder.

To solve the lab, retrieve the contents of the /etc/passwd file.

## Steps

### 1. Inspecting the Website

First, I accessed the website and found several posts.

I opened any post and noticed there was an image.

I opened the image in a new tab.

The URL was:
```
https://0a3700d6040714ea8056a875007c00d2.web-security-academy.net/image?filename=/var/www/images/32.jpg
```

### 2. Testing Path Traversal

I noticed there was a filename parameter used to load the image.

The application validates that the path starts with:
```
/var/www/images/
```

So my first idea was to keep the required path at the beginning and then use path traversal to escape the directory.

The payload:
```
/image?filename=/var/www/images/../../../etc/passwd
```

### 3. Retrieving the File

The server returned the contents of the /etc/passwd file.

Voilaaaaaaa!!

The file was successfully retrieved and the lab was solved.

<img width="1256" height="778" alt="image" src="https://github.com/user-attachments/assets/c3b04c1c-d2dd-44c5-81e5-bbe00ceb5fa6" />


#
**Lab Link:** [Open the lab](https://portswigger.net/web-security/file-path-traversal/lab-validate-start-of-path)

See you in the next lab solution, In sha Allah!


سبحانك اللهم وبحمدك، أشهد أن لا إله إلا أنت، أستغفرك وأتوب إليك.
