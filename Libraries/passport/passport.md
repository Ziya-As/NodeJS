# passport

- [passport](#passport)
  - [Some Useful Links](#some-useful-links)
  - [Intro](#intro)
  - [Installation](#installation)
  - [Using passport](#using-passport)
  - [setting the strategy](#setting-the-strategy)
    - [verify function](#verify-function)
  - [Authenticating using the strategy](#authenticating-using-the-strategy)
    - [changing the name of the strategy in `passport.authenticate()`](#changing-the-name-of-the-strategy-in-passportauthenticate)
  - [Setting up sessions](#setting-up-sessions)
  - [Checking if the user is authenticated](#checking-if-the-user-is-authenticated)
  - [`req.login` and `req.logout`](#reqlogin-and-reqlogout)
  - [Using passport-jwt strategy](#using-passport-jwt-strategy)
  - [Using passport strategy for Google OAuth 2.0](#using-passport-strategy-for-google-oauth-20)

<hr>

## Some Useful Links

- https://www.passportjs.org/concepts/authentication/middleware/

## Intro

Passport is a popular, modular authentication middleware for Node.js applications. With it, authentication can be easily integrated into any Node and Express-based app. The Passport library provides more than 500 authentication mechanisms, including OAuth, JWT, and simple username and password based authentication.

Using Passport makes it easy to integrate more than one type of authentication into the application, too.

On each HTTP request, Passport will use a "Strategy" to determine if the requestor has permission to view that resource. If not, "401 - Unauthorized" error will be thrown.

The "Strategy" in PassportJS is the way of authentication. For example, `passport-github` is using github login to authenticate a user.

This is how you'd setup PassportJS for the local strategy. Local strategy means in-app user registration and login system:

- install `passport`, and `passport-local`
- import `passport`
- import the strategy using `require(passport-local).Strategy`
- set up the strategy
  - define a verify callback function that checks if the username and password provided by the client matches the username and password in db.
  - provide the verify callback function to the strategy as an argument.
- add the strategy to `passport.use`.
- use `passport.authenticate` on a route that will receive the `POST` request with client's username and password.
- install, import, and set the `express-session`.
- add `app.use(passport.authenticate('session'))`

<hr>

## Installation

To use `passport`, we need to install `passport` and a strategy that we want. There are various passport strategies. For example, for authentication using just the app itself, we would use `passport-local`. But if we want to have a 3rd party authentication available, we would use, for example, `passport-facebook`.

```shell
npm i passport passport-local
```

`passport` uses the `express-session` middleware under the hood.

## Using passport

To setup the `passport` in our app and build a local password system, we first do below imports:

```js
// routes/login.js
const passport = require("passport");
const LocalStrategy = require("passport-local").Strategy;
```

## setting the strategy

Now, the strategy needs to be configured. The configuration varies with each authentication mechanism, so strategy-specific documentation should be consulted.

The `LocalStrategy` constructor takes a function as an argument. This function is known as a `verify` function, and is a common pattern in many strategies. When authenticating a request, a strategy parses the credential contained in the request. A `verify` function is then called, which is responsible for determining the user to which that credential belongs.

### verify function

A `verify` function ends in one of three results: success, failure, or an error.

If the `verify` function finds a user to which the credential belongs, and that credential is valid, it calls the callback with the authenticating user:

```js
return cb(null, user);
```

If the credential does not belong to a known user, or is not valid, the `verify` function calls the callback with `false` to indicate an authentication failure:

```js
return cb(null, false);
```

If an error occurs, such as the database not being available, the callback is called with an error.:

```js
return cb(err);
```

It is important to distinguish between the two failure cases that can occur. Authentication failures are expected conditions, in which the server is operating normally, even though invalid credentials are being received from the user (or a malicious adversary attempting to authenticate as the user). Only when the server is operating abnormally should the first argument to the callback function be sett as `err`.

Here is an example:

```js
// models/db.js
const { MongoClient } = require("mongodb");

const dbUri = `mongodb://localhost:27017`;
const client = new MongoClient(dbUri);

const dbName = "passportJWT";
const appDB = client.db(dbName);

module.exports = {
  dbUri,
  dbName,
  appDB,
};
```

```js
// models/userCollection.js
const { appDB } = require("./db");

const userCollection = appDB.collection("users");

module.exports = {
  userCollection,
};
```

```js
// passportAuth.js
const bcrypt = require("bcrypt");
const { userCollection } = require("./models/userCollection");

// import passport and the local strategy
const passport = require("passport");
const LocalStrategy = require("passport-local").Strategy;

// set up the user verification function
const verify = async (username, password, cb) => {
  try {
    // find the user in db
    const registeredUser = await userCollection.findOne({ username });

    // if there is no user
    if (!registeredUser) {
      return cb(null, false, { message: `Incorrect username or password` });
    }

    // if there is a user, compare passwords
    bcrypt.compare(password, registeredUser.password, (err, success) => {
      if (err) {
        console.log(err);
        return cb(err);
      }

      // if there is no error, but the passwords don't match
      if (!success) {
        return cb(null, false, {
          message: `Incorrect username or password`,
        });
      } else {
        const verifiedUser = {
          id: registeredUser._id,
          username: registeredUser.username,
        };
        return cb(null, verifiedUser);
      }
    });
  } catch (error) {
    console.log(error);
    cb(error);
  }
};

// add the user verification function to the local strategy
const strategy = new LocalStrategy(verify);

// export the strategy
module.exports = strategy;
```

With the strategy configured, it is then registered by calling `.use()`:

```js
passport.use(strategy);
```

## Authenticating using the strategy

Once registered, the strategy can be employed to authenticate a request by passing the name of the strategy as the first argument to `passport.authenticate()` middleware. All strategies have a name which, by convention, corresponds to the package name according to the pattern `passport-<name>`. For instance, the LocalStrategy configured above is named **"local"** as it is distributed in the `passport-local` package.

```js
// routes/login.js
const router = require("express").Router();
const passport = require("passport");

router.post(
  "/",
  passport.authenticate("local", {
    /*     failureRedirect: "/login",
    failureMessage: true, 
    successRedirect: "/profile",
    successMessage: true,*/
  }),
  (req, res) => {
    res.status(200).send(`Successfully logged in, ${req.user.username}`);
  }
);

module.exports = router;
```

### changing the name of the strategy in `passport.authenticate()`

In cases where there is a naming conflict, or the default name is not sufficiently descriptive, the name can be overridden when registering the strategy by passing a name as the first argument to `.use()`:

```js
passport.use("password", strategy);
```

That name is then specified to `passport.authenticate()` middleware:

```js
app.post(
  "/login/password",
  passport.authenticate("password", {
    failureRedirect: "/login",
    failureMessage: true,
  }),
  function (req, res) {
    res.redirect("/~" + req.user.username);
  }
);
```

## Setting up sessions

At this point, application must initialize session support in order to make use of login sessions. In an Express app, session support is added by using `express-session` middleware.

To maintain a login session, Passport serializes and deserializes user information to and from the session. The information that is stored is determined by the application, which supplies a `serializeUser` and a `deserializeUser` function.

```js
// passportAuth.js
// ...
passport.serializeUser(function (user, cb) {
  return cb(null, {
    id: user.id,
    username,
  });
});

passport.deserializeUser(function (user, cb) {
  return cb(null, user);
});

// ...
```

A login session is established upon a user successfully authenticating using a credential. If successfully verified, Passport will call the `serializeUser` function, which in the above example is storing the user's ID and username.

As the user navigates from page to page, the session itself can be authenticated using the built-in session strategy. Because an authenticated session is typically needed for the majority of routes in an application, it is common to use this as application-level middleware, after `session` middleware.

```js
// index.js
// ...
app.use(
  session({
    name: "id",
    secret: process.env.SESSION_KEY,
    resave: false,
    saveUninitialized: false,
    store: store,
    cookie: {
      maxAge: 1000 * 60 * 60 * 24,
    },
  })
);

app.use(passport.authenticate("session"));
// ...
```

This can also be accomplished, more succinctly, using the `passport.session()` alias.

When the session is authenticated, Passport will call the `deserializeUser` function, which in the above example is yielding the previously stored user ID, and username. The `req.user` property is then set to the yielded information.

## Checking if the user is authenticated

After the successful authentication, we get the access not only to `req.user`, but also to `req.isAuthenticated()` and `req.isUnauthenticated()`. These help us to set which routes could be accessed when a user is authenticated.

```js
// profile.js
const router = require("express").Router();

router.get("/", (req, res) => {
  if (req.isAuthenticated()) {
    res
      .status(200)
      .send(
        `Congrats. You've reached a protected route after the successful authentication`
      );
  } else {
    res.sendStatus(401);
  }
});

module.exports = router;
```

## `req.login` and `req.logout`

Passport exposes a `logout()` function on `req` (also aliased as `logOut()`) that can be called from any route handler which needs to terminate a login session. Invoking `logout()` will remove the `req.user` property and clear the login session (if any).

It is a good idea to use `POST` or `DELETE` requests instead of `GET` requests for the logout endpoints, in order to prevent accidental or malicious logouts.

```js
// logout.js
const router = require("express").Router();

router.post("/", (req, res) => {
  if (req.isAuthenticated()) {
    req.logout((err) => {
      if (err) {
        console.log(err);
        return next(err);
      }
    });
    // res.redirect("/");
    return res.status(200).send("successfully logged out");
  }
  return res.status(200).send("you are not logged in");
});

module.exports = router;
```

Remember: `req.logout` doesn't delete the session cookie on the client side.

Passport exposes a `login()` function on `req` (also aliased as `logIn()`) that can be used to establish a login session.

`passport.authenticate()` middleware invokes `req.login()` automatically. This function is primarily used when users sign up, during which `req.login()` can be invoked to automatically log in the newly registered user.

Let's change our registration to incorporate `req.login`.

```js
// routes/register.js
const router = require("express").Router();
const bcrypt = require("bcrypt");

const { userCollection } = require("../models/userCollection");

router.get("/", (req, res) => {
  res.status(200).send("This is the register page");
});

router.post("/", (req, res) => {
  const { username, password } = req.body;

  bcrypt.hash(password, 10, async (err, hash) => {
    if (err) {
      console.log(err);
      return res.send("Please try again");
    }

    await userCollection.insertOne({ username, password: hash });
    req.login(username, (err) => {
      if (err) {
        console.log(err);
      }
      // res.redirect('/profile')
      res.send("You've successfully registered");
    });
  });
});

module.exports = router;
```

Here is how our index.js file looks after all the steps:

```js
// index.js
const express = require("express");
const app = express();

const session = require("express-session");
const sessionStore = require("./models/sessionStore");

const passport = require("passport");
const strategy = require("./passportAuth");

app.use(express.urlencoded({ extended: false }));
app.use(
  session({
    secret: "Secret key",
    resave: false,
    saveUninitialized: false,
    cookie: {
      maxAge: 1000 * 60 * 60,
    },
    store: sessionStore,
  })
);
app.use(passport.session());
passport.use(strategy);

const login = require("./routes/login");
const profile = require("./routes/profile");
const register = require("./routes/register");
const logout = require("./routes/logout");

app.use("/login", login);
app.use("/profile", profile);
app.use("/register", register);
app.use("/logout", logout);

app.listen(3000);
```

## Using passport-jwt strategy

Instead of a session-based strategy, we can also use json web token-based strategy with passport. To user this strategy, we install `passport-jwt`.

This is how we implement this strategy.

- We install the `passport-jwt` library and import 2 things `Strategy` and `ExtractJwt` from it.
- The strategy accepts an options object and the verify function.
  - The options object have several important properties.
    - `jwtFromRequest` property lets us to set how we want the JWT to be derived. For example, we can write `jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken()` and this way passport will look for JWT in the `Authorization` header. There are other methods that we could use in addition to specifying our custom one. Here are some more examples of methods that could be used:
    - `fromHeader(<header_name>)`
    - `fromBodyField(<field_name>)`
    - `fromUrlQueryParameter(<param_name>)`
  - The verify function is simple. We won't need to verify JWT ourselves. Passport will do it, and by the time it arrives to our verify function, we would be able to access the decoded JWT payload. The first argument to the verify function would be the JWT payload and the second one is the callback function.

Here is an example where we setup the passport for the JWT strategy:

```js
// config/passport.js
const JwtStrategy = require("passport-jwt").Strategy;
const ExtractJwt = require("passport-jwt").ExtractJwt;
const fs = require("fs");

const PUB_KEY = fs.readFileSync("./config/public.key", "utf8");

const options = {
  jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
  secretOrKey: PUB_KEY,
  algorithms: ["RS256"],
};

const mockDB = require("./mockDB");

module.exports = (passport) => {
  passport.use(
    new JwtStrategy(options, function (jwt_payload, done) {
      console.log(
        `jwt_payload in the verify function: ${JSON.stringify(jwt_payload)}`
      );

      try {
        const foundUser = mockDB.find(
          (registeredUser) => registeredUser.id === jwt_payload.id
        );

        if (foundUser) {
          return done(null, foundUser);
        } else {
          return done(null, false);
        }
      } catch (error) {
        return done(error, false);
      }
    })
  );
};
```

Here is the **login** route, where we issue a signed JWT:

```js
// index.js
// ...
app.post("/login", (req, res) => {
  try {
    const foundUser = mockDB.find(
      (registeredUser) => registeredUser.username === req.body.username
    );

    if (!foundUser) {
      return res.status(401).json({
        success: false,
        msg: `Couldn't find the user`,
      });
    } else {
      // if the user is found we need to check the password, but I won't do it this time
      const PRIV_KEY = fs.readFileSync("./config/private.key", "utf8");
      const token = jwt.sign(
        { id: foundUser.id, username: foundUser.username },
        PRIV_KEY,
        { expiresIn: "1d", algorithm: "RS256" }
      );
      res.status(200).json({
        success: true,
        msg: "You are authenticated",
        token: token,
      });
    }
  } catch (error) {
    console.log(error);
    res.json({
      success: false,
      msg: "Please try again: " + error.message,
    });
  }
});
// ...
```

With the `'local'` strategy, we used `req.isAuthenticated()` to check if the user is authenticated or not. However, with the JWT strategy, we have to use `passport.authenticate()`. When a user tries to access a private route, this method will run the verify function, where JWT verification will happen. Here is the full index.js file with the **protected** route and the previously defined **login** route:

```js
// index.js
const express = require("express");
const passport = require("passport");
const jwt = require("jsonwebtoken");
const fs = require("fs");

const app = express();

require("./config/passport")(passport);
const mockDB = require("./config/mockDB");

app.use(passport.initialize());
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

app.get(
  "/protected",
  passport.authenticate("jwt", { session: false }),
  (req, res) => {
    res.status(200).json({
      success: true,
      msg: "You are authenticated",
    });
  }
);

app.post("/login", (req, res) => {
  try {
    const foundUser = mockDB.find(
      (registeredUser) => registeredUser.username === req.body.username
    );

    if (!foundUser) {
      return res.status(401).json({
        success: false,
        msg: `Couldn't find the user`,
      });
    } else {
      // if the user is found we need to check the password, but I won't do it this time
      const PRIV_KEY = fs.readFileSync("./config/private.key", "utf8");
      const token = jwt.sign(
        { id: foundUser.id, username: foundUser.username },
        PRIV_KEY,
        { expiresIn: "1d", algorithm: "RS256" }
      );
      res.status(200).json({
        success: true,
        msg: "You are authenticated",
        token: token,
      });
    }
  } catch (error) {
    console.log(error);
    res.json({
      success: false,
      msg: "Please try again: " + error.message,
    });
  }
});

app.listen(3000, () => {
  console.log(`Server is listening on port 3000`);
});
```

## Using passport strategy for Google OAuth 2.0

We can also use passport strategies enabling authentication through third-parties. For example, we can use the package `passport-google-oauth2`. First, we will need to register our app in google. We can search for google credentials to get to the proper url. After registering our app on google, we get the `Client ID`, and the `Client secret`. We also provide **Authorized redirect URIs** to google. This tells google which url to redirect the user on a successful authentication.

We save the `Client ID`, and the `Client secret` in the environment variables, and prepare the `auth.js` file using the `passport-google-oauth2` strategy:

```js
// auth.js
require("dotenv").config();

const passport = require("passport");
const GoogleStrategy = require("passport-google-oauth2").Strategy;

passport.use(
  new GoogleStrategy(
    {
      clientID: process.env.GOOGLE_CLIENT_ID,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET,
      callbackURL: "http://localhost:5000/auth/google/callback",
      passReqToCallback: true,
    },
    function (request, accessToken, refreshToken, profile, done) {
      // normally, we would save the user in a db, and handle the possible errors
      // here we just return the profile of the user that we'll receive from google
      return done(null, profile);
    }
  )
);

passport.serializeUser(function (user, done) {
  done(null, user);
});

passport.deserializeUser(function (user, done) {
  done(null, user);
});
```

Then, we do the next steps:

- Import and setup `express-session`
- Initialize passport with `passport.initialize()`
- Make sure that passport controls sessions using `passport.session()`
- Prepare relevant routes
  - Allow only authenticated users to access the "/protected" route
  - Authenticate the user on "/auth/google" using `passport.authenticate("google", { scope: ["email", "profile"] })`

```js
require("dotenv").config();

const express = require("express");
const session = require("express-session");
const passport = require("passport");
require("./auth");

function isLoggedIn(req, res, next) {
  req.user ? next() : res.sendStatus(401);
}

const app = express();
app.use(
  session({
    secret: process.env.SESSION_SECRET,
    resave: false,
    saveUninitialized: false,
  })
);
app.use(passport.initialize());
app.use(passport.session());

app.get("/", (req, res) => {
  res.send('<a href="/auth/google">Authenticate with Google</a>');
});

app.get(
  "/auth/google",
  passport.authenticate("google", { scope: ["email", "profile"] })
);

app.get(
  "/auth/google/callback",
  passport.authenticate("google", {
    successRedirect: "/protected",
    failureRedirect: "/auth/failure",
  })
);

app.get("/auth/failure", (req, res) => {
  res.send("something went wrong...");
});

app.get("/protected", isLoggedIn, (req, res) => {
  console.log(req.user);
  res.send(`
  Hello, ${req.user.displayName}!
  <br>
  <a href="/logout">Logout</a>
  `);
});

app.get("/logout", (req, res, next) => {
  req.logout((err) => {
    if (err) {
      console.log(err);
      return next(err);
    }
    req.session.destroy();
  });
  res.send("Goodbye");
});

app.listen(5000, () => console.log("listening on 5000"));
```
