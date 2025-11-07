# Express-session

- [Express-session](#express-session)
  - [Some Useful Links](#some-useful-links)
  - [Intro](#intro)
  - [Session vs Cookie](#session-vs-cookie)
  - [Using `express-session`](#using-express-session)
    - [Installation](#installation)
    - [Simple example](#simple-example)
    - [Options to configure `express-session`](#options-to-configure-express-session)
      - [Managing session timeouts](#managing-session-timeouts)
    - [Example with a mongo store](#example-with-a-mongo-store)
    - [Dealing with `req.session`](#dealing-with-reqsession)
    - [Long Example](#long-example)

<hr>

## Some Useful Links

- https://www.npmjs.com/package/express-session
- https://www.npmjs.com/package/connect-mongodb-session

<hr>

## Intro

A website is based on the HTTP protocol. HTTP is a stateless protocol which means at the end of every request and response cycle, the client and the server forget about each other.

This is where the session comes in. A session will contain some unique data about that client to allow the server to identify the user and keep track of the user’s state. In session-based authentication, the user’s data is stored in the server’s memory or a database. A unique session id is issued for a user, and when a client sends a request to the server, the server uses that id to identify the user.

## Session vs Cookie

What is the difference between a session and a cookie.

- A cookie is a key-value pair that is stored in the browser. The browser attaches cookies to every HTTP request that is sent to the server. In a cookie, you can’t store a lot of data. A cookie cannot store any sort of user credentials or secret information. If we did that, a hacker could easily get hold of that information and steal personal data for malicious activities.

- On the other hand, the session data is stored on the server-side, i.e., a database or a session store. Hence, it can accommodate larger amounts of data. To access data from the server-side, a session is authenticated with a secret key or a session id that we get from the cookie on every request.

## Using `express-session`

### Installation

To use `express-session`, you first need to install it using npm:

```shell
npm install express-session
```

### Simple example

Once you have installed `express-session`, you can use it in your Express app as a middleware. Here is an example:

```js
const express = require("express");
const session = require("express-session");
const app = express();

app.use(
  session({
    // the key that you can use to access the session of a user
    name: "user_sid",

    secret: "key that will sign the cookie",

    // if `resave: true`, for every request we'll create and save a new session, even if it's the same user
    resave: false,

    // `saveUninitialized: false` makes sure that if we haven't modified the session data, we don't save it
    saveUninitialized: false,
    cookie: {
      maxAge: 1000 * 60 * 60,
      sameSite: true,
      secure: false,
    },
  })
);

app.get("/", (req, res) => {
  req.session.isAuth = true;
  console.log(req.session);
  console.log(req.session.id);
  res.status(201).send("Hello Session");
});

const PORT = process.env.PORT || 3000;

app.listen(PORT, () => {
  console.log(`Running`);
});
```

### Options to configure `express-session`

As you can see from the example above, the `expressSession` accepts an options object which could help us to set how a session will be configured. Here are some of the proeprties that `express-session` accepts in the options object.

1. `name`: The name of the session ID cookie to set in the response (and read from in the request). The default value is '`connect.sid`'.
2. `secret`: This option is used to sign the session ID cookie. This way the id becomes unreadable for a naked eye, and we can identify if the cookie was tampered with.
3. `resave`: A boolean indicating whether the session should be saved to the store on every request, even if the session has not been modified. By default, `resave` is set to `true`, but you can set it to `false` if you want to optimize performance.
4. `saveUninitialized`: A session is uninitialized when it is new but not modified. Choosing `false` is useful for implementing login sessions, reducing server storage usage, or complying with laws that require permission before setting a cookie.
5. `cookie`: An object that is used to configure the session ID cookie. This object can contain properties such as
   1. `maxAge` - the maximum age of the cookie in milliseconds,
   2. `secure` - a boolean indicating whether the cookie should only be sent over HTTPS,
   3. `httpOnly` - a boolean indicating whether the cookie should be accessible only by the web server,
   4. `sameSite` - Specifies the boolean or string to be the value for the `SameSite` `Set-Cookie` attribute. By default, this is `false`.
      - `true` will set the `SameSite` attribute to `Strict` for strict same site enforcement.
      - `false` will not set the `SameSite` attribute.
      - `'lax'` will set the `SameSite` attribute to `Lax` for lax same site enforcement.
      - `'none'` will set the `SameSite` attribute to `None` for an explicit cross-site cookie.
      - `'strict'` will set the `SameSite` attribute to `Strict` for strict same site enforcement.
   5. `domain` - The domain name of the session ID cookie. By default, the cookie is scoped to the host that set it (including subdomains), but you can specify a domain name to allow the cookie to be shared across multiple subdomains.
   6. `path` - The path for the session ID cookie. By default, the cookie is scoped to the root path ("/"), but you can specify a different path to limit the cookie's scope.
   7. `expires` - The expiration date for the session ID cookie. By default, the cookie is a session cookie and expires when the user closes their browser, but you can set a specific date to make the cookie persistent.
   8. `signed` - A boolean indicating whether the session ID cookie should be signed. If set to `true`, the cookie's value will be signed with the `secret` key. 6.`store` - A session store that is used to store the session data. By default, `express-session` uses an in-memory store, but you can also use a database store or a file store.

#### Managing session timeouts

To ensure that session data is not stored indefinitely, it is essential to manage session timeouts. Session timeouts determine how long a session can remain idle before it is invalidated. You can set a timeout for a session by configuring the session middleware. When a session timeout occurs, the session middleware deletes the session data from the server or session store.

We can set the session timeout using the `maxAge` option when initializing the session middleware. The `maxAge` option is expressed in milliseconds and determines the maximum age of a session.

### Example with a mongo store

The above example used the in-memory store for sessions. But we can use a database as well. There are various modules/libraries that implement a session store that is compatible with this module. One of them is `connect-mongodb-session`. This module exports a single function which takes an instance of connect (or Express) and returns a `MongoDBStore` class that can be used to store sessions in MongoDB.

We can create a database in MongoDB and store our sessions there using this library.

If you pass in an instance of the `express-session` module, the `MongoDBStore` class will enable you to store your Express sessions in MongoDB.

The `MongoDBStore` class has 3 required options:

- `uri`: a MongoDB connection string
- `databaseName`: the MongoDB database to store sessions in
- `collection`: the MongoDB collection to store sessions in

Here is a simple example:

```js
var express = require("express");
var session = require("express-session");
var MongoDBStore = require("connect-mongodb-session")(session);

var app = express();
var store = new MongoDBStore({
  uri: "mongodb://127.0.0.1:27017/connect_mongodb_session_test",
  collection: "mySessions",
});

// Catch errors
store.on("error", function (error) {
  console.log(error);
});

app.use(
  require("express-session")({
    secret: "This is a secret",
    cookie: {
      maxAge: 1000 * 60 * 60 * 24 * 7, // 1 week
    },
    store: store,
    resave: true,
    saveUninitialized: true,
  })
);

app.get("/", (req, res) => {
  req.session.isAuth = true;
  console.log(req.session);
  console.log(req.session.id);
  res.status(201).send("Hello Session");
});

const PORT = process.env.PORT || 3000;

app.listen(PORT, () => {
  console.log(`Running`);
});
```

You should pass a callback to the `MongoDBStore` constructor to catch errors. If you don't pass a callback to the `MongoDBStore` constructor, `MongoDBStore` will throw if it can't connect.

### Dealing with `req.session`

- We can access the session using the `req.session`.
- To access the session id, we can use either `req.sessionID` or `req.session.id`. Accessing this property helps us to retrieve the relevant Id, compare it to the one in-memory or a database, and then send the relevant response.
- We can save our data by simply adding onto the `req.session`. For example, in our examples above, we added `isAuth` boolean variable. We can add other variables as well.
- `req.session.destroy(<callback>)` destroys the session and will unset the `req.session` property. Once complete, the callback will be invoked. We use this usually when a user logs out.

```js
// ...
app.post("/logout", (req, res) => {
  req.session.destroy((err) => {
    if (err) {
      return res.redirect("/home");
    }

    // We can also clear out the cookie here. But even if we don't, the
    // session is already destroyed at this point, so either way, the
    // user won't be able to authenticate with that same cookie again.
    res.clearCookie(`<cookie name>`);
    res.redirect("/login");
  });
});
// ...
```

### Long Example

```js
const express = require("express");
const session = require("express-session");

TWO_HOURS = 1000 * 60 * 60 * 2;

const {
  PORT = 3000,
  NODE_ENV = "development",

  SESS_NAME = "sid",
  SESS_SECRET = "Some Secret Key",
  SESS_LIFETIME = TWO_HOURS,
} = process.env;
const app = express();

const IN_PROD = NODE_ENV === "production";

const users = [
  { id: 1, name: "Alex", email: "alex@gmail.com", password: "secret" },
  { id: 2, name: "Max", email: "max@gmail.com", password: "secret" },
  { id: 3, name: "Hagard", email: "hagard@gmail.com", password: "secret" },
];

app.use(express.urlencoded({ extended: true }));

app.use(
  session({
    name: SESS_NAME,
    resave: false,
    saveUninitialized: false,
    secret: SESS_SECRET,
    cookie: {
      maxAge: SESS_LIFETIME,
      sameSite: true,
      secure: IN_PROD,
    },
  })
);

app.use((req, res, next) => {
  const { userId } = req.session;

  if (userId) {
    res.locals.user = users.find((user) => user.id === userId);
  }

  next();
});

const redirectLogin = (req, res, next) => {
  if (!req.session.userId) {
    res.redirect("/login");
  } else {
    next();
  }
};

const redirectHome = (req, res, next) => {
  if (req.session.userId) {
    res.redirect("/home");
  } else {
    next();
  }
};

app.get("/", (req, res) => {
  // console.log(req.session);
  const { userId } = req.session;
  // const userId = 1;
  console.log(userId);

  res.send(`
  <h1>Welcome</h1>
  ${
    userId
      ? `
  <a href="/home">Home</a>
  <form method="post" action="/logout">
    <button>Logout</button>
  </form>
  `
      : `
  <a href="/login">Login</a>
  <a href="/register">Register</a>
  `
  }
  `);
});

app.get("/home", redirectLogin, (req, res) => {
  const { user } = res.locals;

  res.send(`
  <h1>Home</h1>
  <a href="/">Main</a>
  <ul>
    <li>Name: ${user.name}</li>
    <li>Email: ${user.email}</li>
  </ul>
  `);
});

app.get("/login", redirectHome, (req, res) => {
  const { user } = res.locals;

  res.send(`
    <h1>Login</h1>
    <form method="post" action="/login">
      <input type="email" name="email" placeholder="Email" required />
      <input type="password" name="password" placeholder="Password" required />
      <input type="submit" />
    </form>
    <a href="/register">Register</a>
    `);
});

app.get("/register", (req, res) => {
  res.send(`
    <h1>Register</h1>
    <form method="post" action="/register">
      <input name="name" placeholder="Name" required />
      <input type="email" name="email" placeholder="Email" required />
      <input type="password" name="password" placeholder="Password" required />
      <input type="submit" />
    </form>
    <a href="/login">Login</a>
  `);
});

app.post("/login", redirectHome, (req, res) => {
  const { email, password } = req.body;

  if (email && password) {
    const user = users.find(
      (user) => user.email === email && user.password === password
    );
    if (user) {
      req.session.userId = user.id;
      return res.redirect("/home");
    }
  }
  res.redirect("/login");
});

app.post("/register", redirectHome, (req, res) => {
  const { name, email, password } = req.body;

  if (name && email && password) {
    const exists = users.some((user) => {
      return user.email === email;
    });

    if (!exists) {
      const user = {
        id: users.length + 1,
        name,
        email,
        password,
      };
      users.push(user);

      req.session.userId = user.id;

      return res.redirect("/home");
    }
  }

  res.redirect("/register");
});

app.post("/logout", redirectLogin, (req, res) => {
  req.session.destroy((err) => {
    if (err) {
      return res.redirect("/home");
    }

    res.clearCookie(SESS_NAME);
    res.redirect("/login");
  });
});

app.listen(PORT, () => {
  console.log(`http://localhost:${PORT}`);
});
```
