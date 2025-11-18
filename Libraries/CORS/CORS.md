# CORS

- [CORS](#cors)
  - [What is CORS?](#what-is-cors)
  - [Using `cors` package](#using-cors-package)
  - [The Default Values that `cors` sets](#the-default-values-that-cors-sets)
  - [Explaining the headers that `cors` sets](#explaining-the-headers-that-cors-sets)
  - [Customizing `cors`](#customizing-cors)
  - [Preflight request](#preflight-request)
  - [More about `Access-Control-Allow-Origin` (ACAO)](#more-about-access-control-allow-origin-acao)
  - [Examples of setting all of the CORS-related headers](#examples-of-setting-all-of-the-cors-related-headers)
  - [More Examples](#more-examples)
    - [Example 1](#example-1)
    - [Example 2](#example-2)

## What is CORS?

**CORS** (**Cross-Origin Resource Sharing**) is a security feature in web browsers that restricts web pages from making requests to resources (e.g., data, images, scripts) on a different domain than the one that served the original web page. This means that if you're browsing a web page on a domain called "example.com", that web page is not allowed to directly request resources from another domain (e.g., "google.com" or "facebook.com") unless that domain explicitly allows it.

This security feature is enforced by web browsers by default, and it is designed to prevent malicious websites from accessing sensitive data from other websites. Imagine, for example, that you're logged into your online banking account, and you also happen to be browsing a malicious website that contains some malicious code. If the malicious website were allowed to make requests to your online banking domain, it could potentially access your sensitive banking data and use it for fraudulent purposes.

To prevent this kind of attack, web browsers implement CORS to prevent web pages from making cross-origin requests by default. This means that if a web page wants to make a cross-origin request to another domain, it needs to go through a process called "preflighting", which involves sending an HTTP OPTIONS request to the target domain to ask whether the cross-origin request is allowed. If the target domain responds with the appropriate CORS headers (e.g., `Access-Control-Allow-Origin`), the browser will allow the cross-origin request to proceed. If the target domain does not respond with the appropriate CORS headers, the browser will block the cross-origin request.

<hr>

## Using `cors` package

The `cors` package for NodeJS is a middleware that can be used to enable cross-origin requests for a NodeJS server. When the server receives a request with the `cors` middleware enabled, it will add specific HTTP headers to the response that tell the web browser that it is allowed to make cross-origin requests to this server.

Here's an example of how to use the `cors` package in a NodeJS server:

```js
const express = require("express");
const app = express();

const cors = require("cors");

const PORT = process.env.PORT || 3000;

app.use(cors());

app.get("/", (req, res) => {
  res.status(200).send("Hello World!");
});

app.listen(PORT, () => {
  console.log(`Server listening on port: ${PORT}`);
});
```

<hr>

## The Default Values that `cors` sets

Here are the default values for the CORS headers that the `cors` package sets:

- `Access-Control-Allow-Origin`: The default value is "`*`", which means that any origin is allowed to access the resource.

- `Access-Control-Allow-Methods`: The default value is "`GET`,`HEAD`,`PUT`,`PATCH`,`POST`,`DELETE`", which allows all HTTP methods.

- `Access-Control-Allow-Headers`: The default value is "`Content-Type`", which allows requests with the `Content-Type` header.

- `Access-Control-Allow-Credentials`: The default value is `false`, which means that cookies and other sensitive information are not sent with cross-origin requests.

- `Access-Control-Max-Age`: The default value is `undefined`, which means that the browser will use its own default value for the maximum age of the preflight response.

- `Access-Control-Expose-Headers`: The default value is `undefined`, which means that no response headers are exposed to the client.

<hr>

## Explaining the headers that `cors` sets

To briefly explain the purpose of each header:

- `Access-Control-Allow-Origin`: Indicates which origins are allowed to make cross-origin requests to the resource.
- `Access-Control-Allow-Methods`: Indicates which HTTP methods are allowed for the resource.
- `Access-Control-Allow-Headers`: Indicates which HTTP headers are allowed for the resource.
- `Access-Control-Allow-Credentials`: Indicates whether or not cookies and authentication headers should be included in cross-origin requests to the resource.
- `Access-Control-Max-Age`: Indicates how long the results of a preflight request can be cached.
- `Access-Control-Expose-Headers`: Indicates which response headers can be exposed to the client. This header is not necessary for simple cross-origin requests, but is required for requests that access non-standard headers or use methods other than `GET` or `POST`.

<hr>

## Customizing `cors`

There are several options that can be passed to the cors middleware to customize its behavior. For example, you can specify which origins are allowed to make cross-origin requests to the server, or you can set custom HTTP headers to be included in the response. Here's an example of how to use some of these options:

```js
const express = require("express");
const app = express();

const cors = require("cors");

const PORT = process.env.PORT || 3000;

const corsOptions = {
  origin: "http://example.com",
  optionsSuccessStatus: 200,
  methods: ["GET", "POST"],
  allowedHeaders: ["Content-Type", "Authorization"],
};

app.use(cors(corsOptions));

app.get("/", (req, res) => {
  res.status(200).send("Hello World!");
});

app.listen(PORT, () => {
  console.log(`Server listening on port: ${PORT}`);
});
```

In this example, we create a `corsOptions` object that specifies that only requests coming from http://example.com are allowed to make cross-origin requests to the server. We also set the `optionsSuccessStatus` to `200`, which tells the browser that it can cache the preflight response for future requests. We then specify that only `GET` and `POST` requests are allowed, and that only the `Content-Type` and `Authorization` headers are allowed in the request.

<hr>

## Preflight request

When making cross-origin requests that use certain HTTP methods or headers, the browser will first send a preflight request to the server to check whether the actual request is allowed. The preflight request is an HTTP OPTIONS request that includes additional headers, such as the `Access-Control-Request-Method` and `Access-Control-Request-Headers` headers, that inform the server about the actual request that will be sent.

The server responds to the preflight request with the appropriate `Access-Control-Allow-*` headers, which inform the client's browser whether the actual request is allowed. If the server allows the actual request, the client's browser will then send the actual request to the server.

The purpose of the preflight request is to ensure that the actual request is safe to send before sending it. This helps prevent malicious websites from accessing sensitive data from other websites.

<hr>

## More about `Access-Control-Allow-Origin` (ACAO)

The `Access-Control-Allow-Origin` (ACAO) header is an HTTP response header that is used to indicate which origins are allowed to access a resource on a web server. This header is a key part of the Cross-Origin Resource Sharing (CORS) mechanism that allows web pages to access resources from a different domain than the one that served the original web page.

The ACAO header can have one of three possible values:

- A single origin: This value indicates that the resource can only be accessed from a single origin. For example, if the ACAO header is set to "https://example.com", then only requests from the origin "https://example.com" will be allowed to access the resource.

- A list of origins: This value indicates that the resource can be accessed from a specific list of origins. For example, if the ACAO header is set to "https://example.com, https://sub.example.com", then requests from the origins "https://example.com" and "https://sub.example.com" will be allowed to access the resource.

- An asterisk (`*`): This value indicates that any origin is allowed to access the resource. This is useful for resources that are intended to be publicly accessible.

It's important to note that the ACAO header is a response header that is sent from the server to the client's browser. The client's browser uses this header to determine whether the resource can be accessed from the current origin.

Here are some examples of how to set the `Access-Control-Allow-Origin` header in an ExpressJS application, both with and without using the CORS package:

Without using the CORS package:

```js
const express = require("express");
const app = express();

// Set the Access-Control-Allow-Origin header to allow requests from a single origin
app.use((req, res, next) => {
  res.setHeader("Access-Control-Allow-Origin", "https://example.com");
  next();
});

// Set the Access-Control-Allow-Origin header to allow requests from a list of origins
app.use((req, res, next) => {
  res.setHeader(
    "Access-Control-Allow-Origin",
    "https://example.com, https://sub.example.com"
  );
  next();
});

// Set the Access-Control-Allow-Origin header to allow requests from any origin
app.use((req, res, next) => {
  res.setHeader("Access-Control-Allow-Origin", "*");
  next();
});

// Set up a route to handle requests
app.get("/", (req, res) => {
  res.send("Hello, world!");
});

app.listen(3000);
```

With the CORS package:

```js
const express = require("express");
const cors = require("cors");
const app = express();

// Set up CORS middleware to allow requests from a single origin
app.use(
  cors({
    origin: "https://example.com",
  })
);

// Set up CORS middleware to allow requests from a list of origins
app.use(
  cors({
    origin: ["https://example.com", "https://sub.example.com"],
  })
);

// Set up CORS middleware to allow requests from any origin
app.use(cors());

// Set up a route to handle requests
app.get("/", (req, res) => {
  res.send("Hello, world!");
});

app.listen(3000);
```

<hr>

## Examples of setting all of the CORS-related headers

Here are examples of setting all of the CORS-related headers both with and without using the CORS package, using the Express framework:

Without CORS:

```js
const express = require("express");
const app = express();

app.get("/", (req, res) => {
  res.setHeader("Access-Control-Allow-Origin", "http://example.com");
  res.setHeader("Access-Control-Allow-Methods", "GET, POST, PUT");
  res.setHeader("Access-Control-Allow-Headers", "Content-Type");
  res.setHeader("Access-Control-Allow-Credentials", true);
  res.setHeader("Access-Control-Max-Age", "86400");
  res.setHeader("Access-Control-Expose-Headers", "Content-Length");

  // send response
  res.send("Hello World!");
});

app.listen(3000);
```

With CORS:

```js
const express = require("express");
const cors = require("cors");
const app = express();

app.use(
  cors({
    origin: "http://example.com",
    methods: ["GET", "POST", "PUT"],
    allowedHeaders: ["Content-Type"],
    credentials: true,
    maxAge: 86400,
    exposedHeaders: ["Content-Length"],
  })
);

app.get("/", (req, res) => {
  // send response
  res.send("Hello World!");
});

app.listen(3000);
```

<hr>

## More Examples

### Example 1

In this example, we're using the `cors` middleware to allow all origins to access our API. We then define a route that returns a JSON response with a simple message. The cors middleware automatically sets the necessary CORS headers on the response, allowing any origin to access the `/api/data` endpoint.

```js
const express = require("express");
const app = express();

const cors = require("cors");

const PORT = process.env.PORT || 3000;

// Allow all origins to access this API
app.use(cors());

// Define a route that returns a JSON response
app.get("/api/data", (req, res) => {
  const data = {
    message: "Hello, world!",
  };
  // Send the response with the CORS headers automatically set by the `cors` middleware
  res.status(200).json(data);
});

app.listen(PORT, () => {
  console.log(`Server listening on port: ${PORT}`);
});
```

<hr>

### Example 2

```js
const express = require("express");
const app = express();

const cors = require("cors");

const PORT = process.env.PORT || 3000;

app.use(
  cors({
    origin: ["https://example.com", "https://sub.example.com"],
    methods: ["GET", "POST"],
    allowedHeaders: ["Content-Type", "Authorization"],
    maxAge: 3600, // 1 hour
  })
);

// Define a route that returns a JSON response
app.get("/api/data", (req, res) => {
  const data = {
    message: "Hello, world!",
  };
  // Send the response with the CORS headers automatically set by the `cors` middleware
  res.status(200).json(data);
});

app.listen(PORT, () => {
  console.log(`Server listening on port: ${PORT}`);
});
```
