# 02: Manipulating Cookies for Privilege Escalation

In this lesson, we will see the first lab for Broken Access Control. As we've said, this vulnerability allows a user to access or modify data that is over their privileges, often belonging to other users.

* **Lab:** [User role controlled by request parameter](https://portswigger.net/web-security/access-control/lab-user-role-controlled-by-request-parameter)

The lab we have today contains a Broken Access Control vulnerability, and our job is to find it.

## Lab Setup and Reconnaissance

First, click on "Access the lab" and check the website. In a normal case, we would search for all vulnerabilities, but for this lesson, we will focus only on Broken Access Control.

There is nothing special on the main pages, but the lab instructions give us credentials: **wiener:peter**.

There is a "My account" page, so let's log in. When you are logged in, new pages and attack surfaces often open up that weren't available before. After logging in, we can see a "Change email" function, and our username is **wiener**.

We could use **feroxbuster** to gather more info, but the lab's goal is to access the admin page.

## Discovering the Vulnerability

Let's try to browse directly to the admin panel: **/admin**

The server responds with an error: **"Admin interface only available if logged in as an administrator."**

This is our clue. Since we are logged in as **wiener**, we don't have the privileges to see this page. Now, we need to find a way to escalate our privileges.

## Exploitation: Using Burp Intercept

Let's use our proxy, **Burp Suite**.

1.  Go to the **Proxy** tab and turn **Intercept on**.
2.  In your browser, try to access the **/admin** page again.
3.  The request will be caught by Burp Suite. Look at the raw request or in the **Inspector** panel.
4.  Find the **Request Cookies** section. You will see something special:
    **Cookie: session=... ; admin=false**

This `admin=false` cookie is the vulnerability. The application is telling the server we are not an admin, right in a place we can control.

Let's try to change it.
1.  Click on the **admin=false** value and change it to **admin=true**.
2.  Click the **Forward** button to send the modified request to the server.

Success! We can now see the **/admin** panel and a list of users, including one named **carlos**.

## The Problem: Temporary Access

Now, try to click the button to delete **carlos**.

It doesn't work! Why?

Because we only intercepted and changed *one* request (the **GET /admin** request). When we clicked "delete," the browser sent a *new* request, and that new request used the *original* cookie from the browser, which still says **admin=false**.

We need a way to keep the cookie set to **admin=true** for our *entire session*.

## Solution: Persistent Access with "Match and Replace"

This is a perfect use case for one of Burp Suite's most powerful features: **Match and Replace**. We can tell Burp to automatically change the cookie for us on *every single request*.

1.  Go to the **Proxy** tab, then the **Options** sub-tab.
2.  Find the **"Match and Replace"** section.
3.  Click the **Add** button to create a new rule.
4.  In the "Match" field, type: **admin=false**
5.  In the "Replace" field, type: **admin=true**
6.  Click **OK**.
7.  Now, go back to the **Intercept** tab and turn **Intercept off**.

Burp Suite is now silently changing **admin=false** to **admin=true** on every request that passes through the proxy.

## Solving the Lab

Now, in your browser, just go to the **/admin** panel. You will have access.

Click the **Delete** button for the user **carlos**.

It will work this time, and the lab will be solved. This is just one of the many powerful features in Burp Suite.

---

See you in the next lesson!
