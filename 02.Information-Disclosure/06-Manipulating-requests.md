# 06: Manipulating Requests

In this lesson, we will learn how web applications can accidentally leak sensitive information through error messages. We will also explore the fundamental difference between GET and POST requests.

## Lab: Reconnaissance and Observation

We will be using this lab from PortSwigger:
* **Lab URL:** [Information disclosure in error messages](https://portswigger.net/web-security/information-disclosure/exploiting/lab-infoleak-in-error-messages)

As always, a good first step in reconnaissance is to check for interesting files, like `robots.txt`.

When we browse the lab site, we can observe the URL as we click on different products. We'll see something like this:

`https://web-security-academy.net/product?productId=4`

We can see that the ID of the product changes every time we click on a new one. This tells us a lot about how the site works.

---

## GET vs POST Requests

The URL we see (`?productId=4`) is a clear sign that the website is using a **GET** request.

* **GET Request:**
    * **What it is:** Used to **request** data from a server.
    * **How it works:** It sends the data needed right in the URL (this is called a "query string").
    * **Example:** When you ask for a product, you are "getting" it. The URL shows the details of your request: `productId=4`.

* **POST Request:**
    * **What it is:** Used to **send** data to a server to create or update a resource.
    * **How it works:** It sends the data in the **body** of the HTTP request, not in the URL. This means the details are not visible in your browser's address bar.
    * **Example:** Submitting a login form (username/password) or posting a comment. You are "posting" information *to* the server.

---

## Exploiting the Request

Since we can see the `productId` parameter in the URL, we can try to manipulate it to see how the server responds.

### Test 1: A Plausible, Non-Existent ID

What if we give it a number that probably doesn't exist, like `30`?

`https://web-security-academy.net/product?productId=30`

The website will likely be prepared for this. It will just return a normal "Not Found" page. This is a handled error.

### Test 2: An Invalid or "Broken" ID

But what if we give it an input it truly can't handle? A number that is *too* big for the database field, or maybe letters?

Let's try a very, very
large number:

`https://web-security-academy.net/product?productId=999999999999999999999`

The website's code might not be prepared to handle such a large value. This could cause the application to crash and generate an **unhandled error**.

This is the vulnerability. Instead of a friendly "Not Found" page, a broken server might show a detailed error message. This message could contain sensitive information, such as:

* Internal code snippets
* Software versions (e.g., "Apache Struts 2.5.12")
* Database keys or credentials
* Internal file paths

Once you have this information (like a specific version number), you can search for known vulnerabilities for that software. As I said, your Google and AI tools are your best friends here!

## How to Solve the Lab

To solve this specific PortSwigger lab, just follow the steps provided in the lab instructions and read them carefully.
