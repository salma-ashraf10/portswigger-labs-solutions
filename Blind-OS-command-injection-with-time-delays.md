# Lab 2: Blind OS Command Injection with Time Delays

**Level:** Practitioner

**Author:** 0xL0m4

<img width="1098" height="258" alt="image" src="https://github.com/user-attachments/assets/f34e1454-9511-478d-9731-b843cb10d097" />

## Tools Used

* Burp Suite

## Steps

### 1. Open the website
You can see Submit feedback option on the top-right side of the page.

### 2. Open the feedback form

Click the Submit feedback link.

You will be redirected to a page containing a feedback form.

<img width="1859" height="586" alt="VirtualBox_kali-linux-2025 3-virtualbox-amd64_04_06_2026_23_09_09222" src="https://github.com/user-attachments/assets/40956e81-2e22-4a2f-9557-b8ab02ab53f0" />

### 3. Intercept the request

Fill out the feedback form, submit it, and intercepting the request with Burp Suite.

<img width="1608" height="778" alt="VirtualBox_kali-linux-2025 3-virtualbox-amd64_04_06_2026_23_09_49" src="https://github.com/user-attachments/assets/92898eaf-8bb8-498a-9794-70a401a8af3a" />

### 4. Test the parameters

Each form field corresponds to a parameter in the request.

To test for OS command injection, modify the parameter values one by one using the following payload. Remember to URL-encode the payload before sending the request (Ctrl + u in Burp Suite).

```bash
;sleep 10;
```

Since this is a blind OS command injection lab, there is no visible command output. Therefore, a time-delay payload such as sleep 10 can be used to determine whether command execution is possible.

<img width="1596" height="786" alt="image" src="https://github.com/user-attachments/assets/2c6ddef0-b61b-4ec0-88ee-f7cf8fe987b5" />

### 5. Verify the result

After sending the modified request, the server takes approximately 10 seconds to respond.

This delay confirms that the injected command was executed successfully, proving the presence of a blind OS command injection vulnerability.

Congratulations! The lab has been successfully solved.

<img width="1920" height="781" alt="image" src="https://github.com/user-attachments/assets/a24e5b32-0d14-460d-885f-ead519a34986" />

---

**Lab Link:** [Open the lab](https://portswigger.net/web-security/os-command-injection/lab-blind-time-delays)

See you in the next lab solution!


سبحانك اللهم وبحمدك، أشهد أن لا إله إلا أنت، أستغفرك وأتوب إليك.
