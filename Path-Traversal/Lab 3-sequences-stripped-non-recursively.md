# Lab 3 : File path traversal, traversal sequences stripped non-recursively

**Platform:** PortSwigger Web Security Academy

**Category:** Path Traversal (PRACTITIONER)


<img width="1080" height="222" alt="image" src="https://github.com/user-attachments/assets/ff0ba813-a7fb-4d99-98d8-33821feeea83" />


## Lab Description

This lab contains a path traversal vulnerability in the display of product images.

The application strips path traversal sequences from the user-supplied filename before using it.

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

The application removes normal path traversal sequences like ../

so I tried to bypass the filter by using a different path traversal sequence:
```
?filename=....//....//....//etc/passwd
```

### 3. Retrieving the File

Voilaaaaaaa!!

The file was successfully retrieved and the lab was solved.

The application bypassed the filtering and processed the path traversal successfully.

<img width="1254" height="735" alt="image" src="https://github.com/user-attachments/assets/1075e60b-63f8-4d3f-9c65-9e3048f66dca" />


#
**Lab Link:** [Open the lab](https://portswigger.net/web-security/file-path-traversal/lab-sequences-stripped-non-recursively)

See you in the next lab solution, In sha Allah!


سبحانك اللهم وبحمدك، أشهد أن لا إله إلا أنت، أستغفرك وأتوب إليك.
