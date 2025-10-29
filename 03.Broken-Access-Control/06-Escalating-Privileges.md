# 06: Escalating Privileges via Hidden POST Parameters

In this lesson, we'll cover another Broken Access Control lab. This time, we won't just *view* data; we will *modify* our private info (in a hidden way) to escalate our privileges.

* **Lab:** [User role can be modified in user profile](https://portswigger.net/web-security/access-control/lab-user-role-can-be-modified-in-user-profile)

---

## Step 1: Reconnaissance and Login

1.  First, access the lab. They give us credentials, so I'll go to "My account" and log in. As I've said, logging in is important because it often reveals new pages and functionality that guests can't see.
2.  After logging in, I'll do reconnaissance again. I'm on my account page, and I see an "Update email" function.
3.  Let's test it. I'll change my email and click "Update." Ahah, it changes.

## Step 2: Intercepting the Request with Burp

Now, let's see what's happening behind the scenes.

1.  I'll run **Burp Intercept** and turn it **on**.
2.  I'll re-do the email change in the browser and click "Update."
3.  Burp will catch the request. As you'll see, the method here is **POST**. Why? Because we are *sending* data (our new email) *to* the server to be processed.

## Step 3: Finding the Vulnerability with Repeater

This is a perfect time to use **Burp Repeater** to test this request without having to reload the browser.

1.  In the Intercept tab, I'll right-click the request and choose **"Send to Repeater"**.
2.  Now I'll go to the **Repeater** tab. I can click "Forward" on the original request in Intercept to let it go.
3.  In Repeater, I'll click **"Send"** to see the normal response. It's a normal "200 OK" response.
4.  But now, I'll look *very carefully* at the data in the request body (the part at the bottom). I see my email, but what is that? There are parameters like *api_key* and... *roleid=1*.
5.  These parameters did **not** appear in the browser form! They were hidden. This is the vulnerability.

## Step 4: Exploitation and Privilege Escalation

This *roleid=1* must mean I'm a normal user. What would happen if I change the value?

1.  In the Repeater request body, I'll change *roleid=1* to **`roleid=2`**. (I used backticks here because it's a specific value we are typing, but let me know if you want this as an asterisk too).
2.  I'll click **"Send"** again.
3.  Now I'll check the *response* from the server. Okay, in the response, I see *roleid=2*. That means the server accepted my change!
4.  Now, I'll go back to my browser (which is still running through the proxy) and refresh the "My account" page.
5.  Yes! The **"Admin panel"** now appears for me. This means *roleid=2* is for admins.

This is one of the most powerful benefits of using Repeater: finding and testing hidden parameters that you would never see in the browser.

---

### Solving the Lab
To solve the lab, just read the instructions and follow these steps to gain admin access and delete the target user.
