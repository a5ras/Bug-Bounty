# Introduction to Web Proxies and Burp Suite

As we learned in the previous lesson, there are two main HTTP methods: GET and POST.

* **GET:** You can see the request data in the URL (e.g., `?id=1`).
* **POST:** The data is sent in the body of the request, so you can't see it in the URL (e.g., a username and password login form).

But what if we want to see *all* the requests and responses, even the hidden ones? Sometimes, a webpage makes many background requests that we don't know about.

If we could reach these hidden requests and modify them, this would open a wide space to find more vulnerabilities. We don't just want to test the URL or the login fields; we want to test *everything*.

To do this, we use a **proxy**.

## What is a Proxy?

A proxy is an **intermediary** that sits between you (your computer) and the website (the server).

Instead of your browser talking directly to the website, it talks to the proxy, and the proxy talks to the website.

This has two main benefits for us:

1.  **Anonymity:** You can use a proxy to hide your real IP address or location from the website.
2.  **Interception:** This is the important one for us. The proxy can "catch" every single request and response. We can stop them, look at them, modify them, and then send them on their way.

You might also hear this called a "3rd party" tool. In this case, "You" are the 1st party (the client), the "Website" is the 2nd party (the server), and the "Proxy" is the 3rd party that sits in the middle.

## Finding More Vulnerabilities

With a proxy, we can find vulnerabilities like **SQL Injection**, **XSS**, and more.

Why? Because when you just look at a website in your browser, you might only have one or two places to test (like a search bar). But with a proxy, you can see *every* parameter in *every* request. You might find 10 or 20 different "cases" or places to try your attacks.

## Our Tool: Burp Suite

The proxy tool we will use is **Burp Suite** from PortSwigger.

### Installation and Setup

1.  Download and install Burp Suite from the PortSwigger website.
2.  Run the application.
3.  On the startup screen, select **Temporary project** and click **Next**.
4.  Click **Start Burp**. You are now in the Burp Suite dashboard.

### Using the Proxy

Burp Suite has its own built-in browser that is pre-configured to work with the proxy.

1.  Go to the **Proxy** tab, then the **Intercept** sub-tab.
2.  Click the button that says **Intercept is off** to turn it **on**.
3.  Click **Open Browser**.
4.  In Burp's browser, search for `google.com`.
5.  Look back at Burp Suite. You will see that the request has been *intercepted* and is waiting for you to **Forward** it.

### Bypassing Proxy Detection

Some websites (like `udemy.com`) are smart and try to check if you are using a tool like Burp. They might block requests that identify themselves as "Burp".

We need to escape that by changing our identity.

1.  In Burp Suite, go to the **Proxy** tab, then the **Options** sub-tab.
2.  Find the **Match and Replace** section.
3.  You can add a rule here to find the part of the request that says you are "Burp" (this is called the **User-Agent**) and replace it with one that looks like a normal browser (e.g., Firefox, Chrome, or even an iPhone).
4.  Try again, and you should not have that problem!

## Next Steps

We will learn more about Burp Suite as we go, but it is a very good idea to start learning it on your own to be ready.

* **TryHackMe:** [Learn Burp Suite Module](https://tryhackme.com/module/learn-burp-suite)
* **PortSwigger Academy:** [Getting Started Documentation](https://portswigger.net/burp/documentation/desktop/getting-started)

See you in the **Broken Access Control** part!
