# 05: IDOR (Insecure Direct Object References)

In this lesson, we will talk about a specific and very common type of **Broken Access Control** called **IDOR**.

## What is IDOR?

**IDOR** stands for **Insecure Direct Object Reference**.

It's a vulnerability that happens when a website lets you access a "thing" (like a file, a user profile, or a database entry) just by giving it an ID, *without checking if you're actually allowed to see it.*

* **What is the difference between this and Broken Access Control?**
    Broken Access Control is the *main category* (like "Fruit"). IDOR is a *specific type* (like "Apple"). All IDORs are Broken Access Control, but not all Broken Access Control is IDOR.

* **Example:**
    I log in as the "Attacker" and go to my profile. The URL is `.../profile?user=attacker`.
    I have a "Target" account with private photos.
    I change the URL to `.../profile?user=target`.
    If the website shows me the *target's* private info, that is an IDOR. The website "directly" served me the "object" (the target's profile) just because I referenced it.

---

## Lab: IDOR in a Live Chat Feature

Let's find an IDOR vulnerability in a lab.

* **Lab:** [IDOR vulnerability](https://portswigger.net/web-security/access-control/lab-insecure-direct-object-references)

### Step 1: Reconnaissance
As usual, click "Access the Lab" and start your recon. This time, they don't give us credentials. While checking the pages, I see a special case: a **"Live Chat"** feature.

1.  Click on "Live Chat" and have a normal conversation (e.g., type "hello").
2.  Check the buttons. "Send" is for sending, but **"View transcript"** looks interesting. It lets you download the chat history.

### Step 2: Finding the Vulnerability
Let's see what happens when we download the transcript.

1.  Click "View transcript." It downloads a file. Look at the filename... it's `2.txt`.
2.  Go back to the chat and download it again. The new file is named `3.txt`.

This is our vulnerability! The server is just assigning transcript files a sequential number (`2`, `3`, etc.). This means the user *before* me, who probably also had a chat, must have downloaded file `1.txt`.

### Step 3: Exploitation (Burp Proxy Method)
We can use Burp Suite to get this file.

1.  Turn on **Burp Proxy** and go to the **Proxy -> Intercept** tab.
2.  In the lab, click "View transcript" one more time. The next file would be `4.txt`.
3.  Burp will catch the request. It will look something like this:
    `GET /download-transcript/4.txt`
4.  Change the `4.txt` in the request to `1.txt`.
5.  Click **Forward**.

Now, check your downloads. You have `1.txt`. When you open it, you will see it's not *your* chat, and it contains sensitive information. We have successfully exploited an IDOR.

### Alternative Method (Manual URL)
You can also do this without Burp. If you know the path from the request (`/download-transcript/`), you can just try to access it directly from the URL bar in your browser:

`[your-lab-url]/download-transcript/1.txt`

It will download the file for you.

---

### Solving the Lab
To solve the lab, just read the instructions and follow the steps to download the sensitive file and submit the information you find.
