# 03: Accessing Sensitive Data of Other Users

As we already said, the vulnerability of Broken Access Control is based on a user (or bug hunter) being able to access or modify another user's info, even without the proper privileges.

This means if we want to find this vulnerability, we should look for places where sensitive info is stored or can be changed.

> **A Very Important Note: The Two-Account Method**
>
> The best way to test for this is to **always use two accounts**.
> 1.  Create one account as the **"Attacker"** (this is you).
> 2.  Create a second account as the **"Target"**.
> 3.  In the "Target" account, add some fake, sensitive info (like a birthday, a private phone number, etc.).
> 4.  Log in as the "Attacker" and try to find a way to access or modify the "Target's" private data. If you can, you have found a Broken Access Control vulnerability.

---

## Lab: Broken Access Control

Now let's see a lab that has this vulnerability.
* **Lab:** [User role controlled by request parameter](https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter-with-password-disclosure)

### Step 1: Reconnaissance
As usual, click "Access the Lab." He already gives us the credentials `wiener:peter` and asks us to delete the account of `carlos`.

Let's look at the website. There is a "My account" page, so let's log in. As we said, when you log in, some new cases or pages could appear for you, and that's exactly what happens.

### Step 2: Finding and Exploiting the Vulnerability
Now that we are logged in as `wiener`, let's look at the URL in the browser. It shows something like:
`.../my-account?id=wiener`

This is our vulnerability. The website is using the `id` parameter in the URL to decide which user's data to show. It's trusting us!

Let's change the `id` in the URL from `wiener` to `carlos` and see what happens:
`.../my-account?id=carlos`

Yes! It logs us directly into Carlos's account. We can see his username, and his password (which is hidden).

### Step 3: Bonus - Viewing the Hidden Password
We can even see Carlos's password by using the browser's "Inspect Element" feature.

1.  Right-click on the password text box (where the `****` are).
2.  Choose **Inspect**.
3.  The developer tools will open and highlight a line of HTML, something like:
    `<input type="password" ...>`
4.  Double-click on the word `password` and change it to `text`.
5.  Press Enter. Congrats, the password of `carlos` is now visible on the screen.

### Step 4: Privilege Escalation
We can try this again. What if there is an `administrator`? Let's try it in the URL:
`.../my-account?id=administrator`

It works! And now, a new case shows up: an "Admin panel" where we can delete users.

## How to Automate This Attack (Burp Intruder)

But what if we didn't know the usernames `carlos` or `administrator`? We can't use `feroxbuster` for this. Feroxbuster is good for finding *files and folders* (like `/admin`), but it's not for testing *values inside a parameter* (like `id=...`).

For this job, we use **Burp Intruder**. Here is the step-by-step process:

1.  **Capture the Request:** First, I turn on **Burp Intercept** and capture the request for my *own* account:
    `GET /my-account?id=wiener`
2.  **Send to Intruder:** I right-click this request in Burp and choose **"Send to Intruder"**.
3.  **Set the Position:** I go to the **Intruder** tab, then **Positions**. I click the "Clear §" button to remove all the default selections. Then, I highlight *only* the username `wiener` and click the "Add §" button. The line will look like this:
    `GET /my-account?id=§wiener§`
4.  **Load Payloads:** Next, I go to the **Payloads** tab. Under "Payload Options," I need to load a list of usernames. I can use a list from SecLists (like `SecLists/Usernames/names.txt`) or just add a simple, custom list with common names:
    * `admin`
    * `administrator`
    * `carlos`
    * `root`
    * `test`
5.  **Start Attack:** I click the **"Start Attack"** button. Burp will now send one request for every username in my list. I will watch the **"Status"** and **"Length"** columns. A `200 OK` status is good, but I'm looking for a `200 OK` that has a different **Content Length**—that means I probably found a real user's page.

---

### Solving the Lab
To solve the lab, just read the instructions and apply what we learned today (find Carlos's password and delete his account using the admin panel).
