# 01: What is Broken Access Control?

Broken Access Control is currently ranked as the **number one** most critical web application vulnerability, according to the OWASP Top 10. It is a massive problem, found in an estimated 94% of all web applications.

This is a very broad category that includes many other specific vulnerabilities, which we will cover in this Bug-Bounty-Encyclopedia:
* Path Traversal
* Cross-Site Request Forgery (CSRF)
* Insecure Direct Object References (IDOR)
* And many others

## What is the Vulnerability?

At its core, Broken Access Control is a vulnerability where a user can **access data or perform actions that are outside of their intended privileges.**

This means the server fails to properly check if a user is *actually authorized* to do what they are asking to do.

### A Simple Example

Think about a website like Facebook:

* **Intended Access:** You can log in to your account and see other users' *public* usernames and posts. You *cannot* see their private emails, passwords, or private images.
* **The Vulnerability:** If you, as a normal user, found a way to access another user's private data (their email, their passwords, their private photos), this is a critical Broken Access Control failure.

The vulnerability becomes even more dangerous when it's not just about *viewing* data, but *modifying* it. If you could change another user's email or delete their photos, the damage is much higher.

This is not a theoretical vulnerability. Major websites like **Uber, GitHub, and Shopify** have all had and fixed serious Broken Access Control bugs.

In this module, we will learn how to find and exploit these vulnerabilities. In the next parts, we will dive deeper into the specific types like CSRF.
