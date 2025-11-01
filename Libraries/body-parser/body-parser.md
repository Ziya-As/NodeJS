# body-parser

- [body-parser](#body-parser)
  - [When urlencoded needs to be false](#when-urlencoded-needs-to-be-false)
  - [When urlencoded needs to be true](#when-urlencoded-needs-to-be-true)
  - [Drawback of `body-parser`](#drawback-of-body-parser)
  - [No need for `body-parser` after Express version 4.16+](#no-need-for-body-parser-after-express-version-416)
  - [Example 1](#example-1)
  - [Example 2](#example-2)

<hr>

In Node.js, when a client sends a request to a server, the request data is passed to the server in the form of streams. These streams are raw data and need to be parsed to be understood and used by the server code.

The `body-parser` middleware in Express is used to parse the request body and make it available in a convenient format for server-side code to access. The middleware parses the request body data and attaches it to the `req` object as a JavaScript object, which can be accessed as `req.body`.

Without the `body-parser` middleware, you would need to manually parse the raw request data into a JavaScript object, which can be a tedious and error-prone task, especially when dealing with large amounts of data or complex data structures.

<hr>

Express `body-parser` is an npm module used to process data sent in an HTTP request body. It provides four express middleware for parsing JSON, Text, URL-encoded, and raw data sets over an HTTP request body.

Using `body-parser` allows you to access `req.body` from within routes and use that data.

`body-parser` doesn’t have to be installed as a separate package because it is a dependency of express version 4.16.0+.

`body-parser` isn’t a dependency between version 4.0.0 and 4.16.0, so it will be installed separately in projects locked to those versions.

<hr>

## When urlencoded needs to be false

When you submit a standard HTML form with application/x-www-form-urlencoded as the encoding type, you should set `extended` to `false`. Here's an example:

```html
<form action="/example" method="POST">
  <label>Name:</label>
  <input type="text" name="name" />

  <label>Age:</label>
  <input type="text" name="age" />

  <button type="submit">Submit</button>
</form>
```

In this example, we have a simple form that submits the data to `/example` using the `POST` method and the application/x-www-form-urlencoded encoding type. To parse this data in the server-side code, we should use body-parser with `extended` set to `false`:

```js
app.use(bodyParser.urlencoded({ extended: false }));
```

## When urlencoded needs to be true

If your form includes complex data structures like arrays or objects, or if you need to handle data submitted in a non-standard way, then you may need to set `extended` to `true`. Here's an example:

```html
<form action="/example" method="POST">
  <label>Name:</label>
  <input type="text" name="name" />

  <label>Contacts:</label>
  <input type="text" name="contacts[0]" />
  <input type="text" name="contacts[1]" />

  <button type="submit">Submit</button>
</form>
```

In this example, we have a form with a contacts field that has two values. To parse this data in the server-side code, we should use `body-parser` with `extended` set to `true`:

```js
app.use(bodyParser.urlencoded({ extended: true }));
```

Note that if you're using modern front-end frameworks like React or Angular, you may not need to worry about setting `extended` to `true`. These frameworks often handle the serialization of complex data structures for you, and you can simply use `body-parser` with `extended` set to `false` for standard forms.

<hr>

## Drawback of `body-parser`

One potential drawback of using body-parser is that it can have performance issues in certain cases. For example, if you are parsing large files or streams, body-parser may not be the best solution. In such cases, you may want to consider using a different middleware, such as `Multer` or `Busboy`.

`Multer` is a middleware that is specifically designed for handling file uploads. It can handle multipart/form-data requests, which are commonly used for file uploads.

`Busboy` is another middleware that can be used for handling file uploads. It is similar to `Multer`, but it is more lightweight and has fewer dependencies.

<hr>

## No need for `body-parser` after Express version 4.16+

Express has its own built in method to parse json responses. In the past, people might have added a line to their code that looks like the following:

```js
app.use(bodyparser.json()); // utilizes the body-parser package
```

If you are using Express 4.16+, you can now replace that line with:

```js
app.use(express.json()); // Used to parse JSON bodies
```

This should not introduce any breaking changes into your applications since the code in `express.json()` is based on `bodyparser.json()`.

If you also have the following code in your environment:

```js
app.use(bodyParser.urlencoded({ extended: false }));
```

You can replace that with:

```js
app.use(express.urlencoded({ extended: false })); // Parse URL-encoded bodies
```

Remember for `express.json` and `express.urlencoded` to work `Content-Type` header of a request should match the type option (`"application/json"` or `"application/x-www-form-urlencoded"`).

`express.json` accepts an optional options object, which we can use to specify the way `express.json` should work. For example, by default the maximum request body size is 100kb, but we can change it using the `limit` property. If we specify the limit with a number, then the value specifies the number of bytes; if we specify with a string, the value is passed to the bytes library for parsing. Here is an example:

```js
app.use(express.json({ limit: "50mb" }));
```

<hr>

## Example 1

```js
const express = require("express");

const app = express();

app.use(express.json());

app.use(express.urlencoded({ extended: false }));

app.post("/example", (req, res) => {
  console.log(req.body);
  res.send("Got a POST request");
});

app.listen(3000, () => console.log("Server listening on port 3000"));
```

<hr>

## Example 2

```js
const express = require("express");
const bodyParser = require("body-parser");

const app = express();

app.use(bodyParser.urlencoded({ extended: false }));

app.post("/feedback", (req, res) => {
  const feedback = req.body;
  // Do something with the feedback data, e.g. store it in a database
  res.send("Feedback received!");
});

app.listen(3000, () => {
  console.log("Server started on port 3000");
});
```

<hr>
