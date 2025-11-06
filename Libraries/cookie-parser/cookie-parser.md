# cookie-parser

- [cookie-parser](#cookie-parser)
  - [Intro and installation](#intro-and-installation)
  - [Example 1: Setting up cookies in NodeJS](#example-1-setting-up-cookies-in-nodejs)
  - [Example 2](#example-2)
  - [Example 3: Encrypted and Signed cookie](#example-3-encrypted-and-signed-cookie)

## Intro and installation

Cookies are small pieces of data that exists in a user's browser. Cookies continue existing even after the browser is closed. Along with each request, the browser sends all the cookies to the server. Cookies help us identify the user.

`cookie-parser` looks at the headers in between the client and the server transactions, reads these headers, parses out the cookies being sent, and saves them in a browser. In other words, `cookie-parser` helps to create and manage cookies depending on the request a user makes to the server.

To install `cookie-parser`, run:

```shell
npm i cookie-parser
```

## Example 1: Setting up cookies in NodeJS

- import `cookie-parser`, and use it as a middleware

```js
// app.js
const express = require("express");
const app = express();

const cookieParser = require("cookie-parser");

app.use(cookieParser());

app.listen(3000);
```

- Set a cookie

We will set a route and when a client is directed to this route, a cookie will be saved in the browser. To do this, use the `res` object and pass cookie as the method, i.e. `res.cookie()` as shown below.

```js
// app.js
// ...

// a get route for adding a cookie
app.get("/setcookie", (req, res) => {
  res.cookie(`cookie-token-name`, `encrypted cookie string Value`);
  res.send("Cookie have been saved successfully");
});

// ...
```

When the above route is executed from a browser, the client sends a get request to the server. The server will respond with a cookie and save it in the browser.

Go ahead and run `node app.js` to serve the above endpoint. Open http://localhost:3000/setcookie in your browser and access the route.

To confirm that the cookie was saved, go to your browser’s inspector tool 🡆 select the application tab 🡆 cookies 🡆 select your domain URL.

- Using the `req.cookies` method to check the saved cookies

If the server sends this cookie to the browser, this means we can iterate through `req.cookies` and check the existence of a saved cookie. You can log this cookie to the console or send the cookie request as a response to the browser. Let’s do that.

```js
// app.js
// ...

// get the cookie incoming request
app.get("/getcookie", (req, res) => {
  // show the saved cookies
  console.log(req.cookies);
  res.send(req.cookies);
});

// ...
```

Again run the server using `node app.js` to expose the above route (http://localhost:3000/getcookie) and you can see the response on the browser.

- Secure cookies

One precaution that you should always take when setting cookies is security. In the above example, the cookie can be deemed insecure.

For example, you can access this cookie on a browser console using JavaScript (`document.cookie`). This means that this cookie is exposed and can be exploited through cross-site scripting.

You can see the cookie when you open the browser inspector tool and execute the following in the console.

```js
document.cookie;
```

Saved cookie values can be seen through the browser console.

As a precaution, you should always try to make your cookies inaccessible on the client-side using JavaScript.

We can add several attributes to make this cookie more secure.

- `httpOnly` ensures that a cookie is not accessible using the JavaScript code. This is the most crucial form of protection against cross-scripting attacks.
- A `secure` attribute ensures that the browser will reject cookies unless the connection happens over HTTPS.
- `sameSite` attribute improves cookie security and avoids privacy leaks.
  By default, `sameSite` was initially set to `None` (`sameSite = None`). This allowed third parties to track users across sites. Currently, it is set to `Lax` (`sameSite = Lax`) meaning a cookie is only set when the domain in the URL of the browser matches the domain of the cookie, thus eliminating third party’s domains. `sameSite` can also be set to `Strict` (`sameSite = Strict`). This will restrict cross-site sharing even between different domains that the same publisher owns.

- `domain`: Specifies the domain for which the cookie is valid. By default, the cookie is only valid for the domain of the current website, but you can set a different domain to allow the cookie to be shared across multiple subdomains or top-level domains.

- `path`: Specifies the path for which the cookie is valid. By default, the cookie is only valid for the current URL path, but you can set a different path to allow the cookie to be shared across multiple paths or subdirectories.

- You can also add the maximum time you want a cookie to be available on the user's browser. When the set time elapses, the cookie will be automatically deleted from the browser. You can use `maxAge` or `expires` for this.

```js
app.get("/setcookie", (req, res) => {
  res.cookie(`cookie-token-name`, `encrypted cookie string Value`, {
    maxAge: 5000,
    // `expires` works the same as the `maxAge`
    // expires: new Date("01 12 2021"),
    secure: true,
    httpOnly: true,
    sameSite: "lax",
  });
  res.send("Cookie have been saved successfully");
});
```

In this case, we are accessing the server on localhost, which uses a non-HTTPS secure origin. For the sake of testing the server, you can set `secure: false`. However, always use `true` value when you want cookies to be created on an HTTPS secure origin.

If you run the server again (`node app.js`) and navigate to http://localhost:3000/setcookie on the browser, you can see that the values of the cookie have been updated with security values. Furthermore, you cannot access the cookie using JavaScript, i.e., `document.cookie`.

Here is another example. This sets a cookie named "myCookie" with the value "myValue" that is valid for the domain "example.com", the path "/path", and only over HTTPS connections. The cookie also has a strict `sameSite` attribute and expires 15 minutes from the current time.

```js
res.cookie("myCookie", "myValue", {
  domain: "example.com",
  path: "/path",
  secure: true,
  sameSite: "strict",
  expires: new Date(Date.now() + 900000),
});
```

- Step 4 - Deleting a cookie

Typically, cookies can be deleted from the browser depending on the request that a user makes. For example, if cookies are used for login purposes, when a user decides to log out, the request should be accompanied by a delete command.

Here is how we can delete the cookie we have set above in this example. Use `res.clearCookie("<cookieName>")` to clear a cookie.

```js
// app.js

// delete the saved cookie
app.get("/deletecookie", (req, res) => {
  //show the saved cookies
  res.clearCookie("cookie-token-name");
  res.send("Cookie has been deleted successfully");
});
```

Open http://localhost:3000/deletecookie, and you will see that the saved cookie has been deleted.

Another way to clear a cookie is to set it to an empty string, and set a short duration to the `maxAge` property, so that the cookie would expire quickly.

```js
// app.js

// delete the saved cookie
app.get("/deletecookie", (req, res) => {
  //show the saved cookies
  res.cookie("Cookie token name", "", { maxAge: 1 });
  res.send("Cookie has been deleted successfully");
});
```

<hr>

## Example 2

```js
const express = require("express");
const cookieParser = require("cookie-parser");

const app = express();
app.use(cookieParser());
app.use(express.urlencoded({ extended: false }));

app.get("/", (req, res) => {
  const username = req.cookies.username;
  if (username) {
    res.send(`Welcome back, ${username}!`);
  } else {
    res.send("Please log in to continue.");
  }
});

app.post("/login", (req, res) => {
  const { username, password } = req.body;

  // Check if the username and password are correct
  if (username === "johndoe" && password === "password123") {
    // Set a cookie named "username" with the value of the username
    res.cookie("username", username, { maxAge: 900000, httpOnly: true });
    res.send(`Welcome, ${username}!`);
  } else {
    res.send("Invalid username or password.");
  }
});

app.post("/logout", (req, res) => {
  // Clear the "username" cookie
  res.clearCookie("username");
  res.send("You have been logged out.");
});

app.listen(3000, () => {
  console.log("Server is listening on port 3000");
});
```

<hr>

## Example 3: Encrypted and Signed cookie

Signed cookies help detect if a user modified the cookie.

There are several benefits to using encrypted and signed cookies:

- Increased security: Encrypted cookies help prevent unauthorized access to sensitive information by making it more difficult for attackers to read or modify the cookie data. Similarly, signed cookies ensure that the cookie data has not been tampered with, and helps prevent cookie spoofing attacks.

- Protection against session hijacking: Session hijacking is a type of attack where an attacker steals a user's session cookie and uses it to gain access to the user's account. Encrypted and signed cookies can help prevent session hijacking by making it more difficult for attackers to obtain and use a valid session cookie.

- Compliance with privacy regulations: Many countries have data protection laws that require organizations to take appropriate measures to protect the personal data of their users.

To make a cookie signed we use the a secret key with the `cookie-parser`:

```js
const cookieParser = require("cookie-parser");

app.use(cookieParser("mySecretKey"));
```

After this, to access the signed cookies, we use the `req.signedCookies` rather than `req.cookies`.

When, we save a cookie in the browser, we need to add `signed:true` option:

```js
res.cookie("name", "Guest", { maxAge: 150000, signed: true });
```

Here's an example of creating an encrypted and signed cookie using the `cookie-parser` middleware in Express:

```js
const express = require("express");
const cookieParser = require("cookie-parser");
const app = express();

// Use cookie-parser middleware with secret key
app.use(cookieParser("mySecretKey"));

// Set a cookie with signature
app.get("/", (req, res) => {
  res.cookie("myCookie", "myValue", { signed: true });
  res.send("Cookie has been set");
});

// Read the signed cookie
app.get("/readCookie", (req, res) => {
  const myCookie = req.signedCookies.myCookie;
  if (myCookie) {
    res.send(`The value of myCookie is: ${myCookie}`);
  } else {
    res.send("No cookie has been set");
  }
});

// Clear a cookie with a signature
app.get("/clearCookie", (req, res) => {
  res.clearCookie("myCookie", { signed: true });
  res.send("Cookie has been cleared");
});

// Start the server
app.listen(3000, () => console.log("Server is running on port 3000"));
```

In this example, we use the `cookie-parser` middleware with the `signed` option set to `true`. This means that any cookies set or read will be signed using the secret key.

When setting a cookie, we include the `signed` option in the options object passed to the `res.cookie` method. This ensures that the cookie is signed using the secret key.

When reading a cookie, we use the `req.signedCookies` object to access cookies that have been both encrypted and signed. The myCookie cookie is verified automatically using the secret key.

When clearing a cookie, we use the `res.clearCookie` method with the `signed` option set to `true` to clear the cookie with the signature.

<hr>
