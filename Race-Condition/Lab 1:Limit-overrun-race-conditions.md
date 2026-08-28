# Lab 1: Limit Overrun Race Conditions

**Platform:** PortSwigger Web Security Academy

**Category:** Race Conditions (APPRENTICE)

<img width="667" height="147" alt="image" src="https://github.com/user-attachments/assets/4693ec80-8e6a-4c5f-b75c-f165bc58d6ed" />


## Lab Description

This lab's purchasing flow contains a race condition that enables you to purchase items for an unintended price.

To solve the lab, successfully purchase a Lightweight L33t Leather Jacket.

You can log in to your account with the following credentials: wiener:peter.

For a faster and more convenient way to trigger the race condition, we recommend that you solve this lab using the Trigger race conditions custom action. This is only available in Burp Suite Professional.


## Steps

### 1. Logging In

First, I logged in using the provided credentials:

```text
Username: wiener
Password: peter
```

### 2. Finding the Target Product

After logging in, I searched for the target product: Lightweight L33t Leather Jacket

I opened the product details and added the jacket to my cart.

### 3. Applying the Coupon

Next, I opened the shopping cart.

The application provided a coupon code in the header of the page.

I used the coupon normally and then intercepted the request responsible for applying the coupon.


### 4. Preparing the Race Condition Attack

I sent the coupon request to the repeater.

Since the application supported HTTP/2, I used the single-packet attack technique.

The goal was to send multiple requests at almost exactly the same time.

I repeated the coupon request approximately 17 times and placed the requests in the same attack group.

By sending them simultaneously, multiple requests could potentially be processed before the server updated the state indicating that the coupon had already been used.

<img width="1262" height="843" alt="image" src="https://github.com/user-attachments/assets/917c35b1-36d4-41b9-afd6-d5c04c2add19" />


### 5. Checking the Cart Balance

The goal was to apply enough discounts to reduce the price of the jacket below my available balance of $50.

If the final amount was still higher than $50, I repeated sending the request.

After successfully exploiting the race condition, the coupon discount was applied multiple times.

The price was finally reduced enough to purchase the jacket.

<img width="1920" height="772" alt="image" src="https://github.com/user-attachments/assets/6be65f1f-8944-47de-a991-6d8ed8c9df1c" />


### 7. Purchasing the Jacket

Once the final price was affordable, I proceeded to checkout and purchased it

Alhamdulillah, the lab was solved successfully!

<img width="1836" height="817" alt="image" src="https://github.com/user-attachments/assets/5dd25eac-4bcc-493a-882e-e3b74d196c84" />


#

**Lab Link:** [Open the lab](https://portswigger.net/web-security/race-conditions/lab-race-conditions-limit-overrun)

See you in the next lab solution, In sha Allah!

سبحانك اللهم وبحمدك، أشهد أن لا إله إلا أنت، أستغفرك وأتوب إليك.
