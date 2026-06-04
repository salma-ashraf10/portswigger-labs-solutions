# Lab 1: OS Command Injection, Simple Case

**Level:** Apprentice

**Author:** 0xL0m4

<img width="1076" height="352" alt="image" src="https://github.com/user-attachments/assets/6d48d2a4-c94a-49c6-91bf-1a05eff3b942" />

## Tools Used

* Burp Suite

## Steps

### 1. Open the website

You can see a list of products and view their details.

### 2. View product details

Click the View Details button for any product and scroll down.

You will find a drop-down menu that allows you to select one of the available store locations.

<img width="1505" height="769" alt="VirtualBox_kali-linux-2025 3-virtualbox-amd64_04_06_2026_22_36_31" src="https://github.com/user-attachments/assets/84c11b88-4c14-4b57-895b-382997fdb4fb" />

### 3. Intercept the request

Select any store, click Check Stock, and intercept the request using Burp Suite.

<img width="1609" height="782" alt="VirtualBox_kali-linux-2025 3-virtualbox-amd64_04_06_2026_22_35_45" src="https://github.com/user-attachments/assets/84d0793b-d1b1-4a9c-b96e-81373840bedb" />

### 4. Test for command injection

Notice the storeId parameter in the request.

To test for OS command injection, modify its value using the following payload:

```bash
1;whoami
```

<img width="1648" height="777" alt="VirtualBox_kali-linux-2025 3-virtualbox-amd64_04_06_2026_22_36_15" src="https://github.com/user-attachments/assets/d8ea6e7c-c96b-46f7-810b-7007bec8cd27" />

### 5. Verify the result

The response returns the username of the current system user, confirming that OS command injection is possible.
Congratulations, the lab has been successfully solved.

<img width="1506" height="774" alt="VirtualBox_kali-linux-2025 3-virtualbox-amd64_04_06_2026_22_32_48" src="https://github.com/user-attachments/assets/aca322c5-25a3-416d-a353-fd8aed206c7d" />

-----

**Lab Link:** https://portswigger.net/web-security/os-command-injection/lab-simple

See you in the next lab solution!


سبحانك اللهم وبحمدك، أشهد أن لا إله إلا أنت، أستغفرك وأتوب إليك.
