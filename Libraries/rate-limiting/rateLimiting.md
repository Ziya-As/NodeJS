# Rate Limiting in NodeJS

- [Rate Limiting in NodeJS](#rate-limiting-in-nodejs)
  - [Useful Links](#useful-links)
  - [Intro](#intro)
  - [Key considerations for implementing rate limiters on an application](#key-considerations-for-implementing-rate-limiters-on-an-application)
  - [Algorithms for Implementing a Rate Limiter](#algorithms-for-implementing-a-rate-limiter)
    - [Fixed window counter](#fixed-window-counter)
    - [Sliding logs](#sliding-logs)
    - [Sliding window counter](#sliding-window-counter)
    - [Token bucket](#token-bucket)
    - [Leaky bucket](#leaky-bucket)
  - [Using `rate-limiter-flexible`](#using-rate-limiter-flexible)
    - [Example 1](#example-1)
    - [Some properties of rate-limiting classes from `rate-limiter-flexible`](#some-properties-of-rate-limiting-classes-from-rate-limiter-flexible)
    - [Example 2](#example-2)
    - [The `consume` method](#the-consume-method)
  - [Using `express-rate-limit`](#using-express-rate-limit)
    - [Example](#example)
    - [Properties of the `rateLimit` method](#properties-of-the-ratelimit-method)
    - [Rate-limiting a specific path](#rate-limiting-a-specific-path)
    - [Rate-limiting a specific endpoint](#rate-limiting-a-specific-endpoint)
    - [Rate-limiting with an external store](#rate-limiting-with-an-external-store)
  - [Using `express-slow-down`](#using-express-slow-down)
    - [Example](#example-1)
    - [Properties of the `slowDown` method](#properties-of-the-slowdown-method)
    - [`express-slow-down` with an external store](#express-slow-down-with-an-external-store)

<hr>

## Useful Links

- https://express-rate-limit.mintlify.app/overview
- https://www.npmjs.com/package/express-slow-down
- https://blog.appsignal.com/2024/04/03/how-to-implement-rate-limiting-in-express-for-nodejs.html
- https://reflectoring.io/tutorial-nodejs-rate-limiter/
- https://blog.logrocket.com/rate-limiting-node-js/
- https://github.com/animir/node-rate-limiter-flexible

## Intro

Rate limiting refers to regulating the rate at which users or services can access a resource. In other words, rate limiting is a strategy for limiting network traffic on a server. It can be used to restrict the number of requests a client can make to an API within a given time. A server typically responds with an HTTP **429 Too Many Requests** status code, and an error message is thrown indicating the client has made too many requests and exceeded the maximum request limit.

There are two approaches to rate limiting:

- **Blocking incoming requests**: When a client exceeds the defined limits, deny its additional requests.
- **Slowing down requests**: Introduce a delay for requests beyond the limits, making the caller wait longer and longer for a response.

## Key considerations for implementing rate limiters on an application

- **Determine the client identity**: Before implementing rate limiting, we need to determine the client identity to be rate limited. This can be based on factors like IP address, user account, or API key. However, note that relying solely on IP addresses has limitations due to shared IPs and malicious users. It’s advisable to combine IP-based rate limiting with other authentication methods for enhanced security.
- **Clearly defined constraint (limit)**:
  - Users: The constraint might be specific to a user and is implemented using a unique user identifier
  - Location: The constraint might be based on geography and is implemented based on the location from which the request was made
  - IP addresses: The constraint might be based on the IP address of the device that initiates a request
- **Determine application’s traffic volume limit**: Before setting rate limits, it’s crucial we have a deep understanding of our application’s capacity and performance limits.
- **The appropriate rate limiting libraries to use, or build your own**

## Algorithms for Implementing a Rate Limiter

Note: the fixed window counter and sliding logs are the most inefficient ways to implement rate limiting.

### Fixed window counter

In this approach, we track the number of requests a user makes in each window of time. For instance, if I want my API to allow ten requests per minute, we have a 60 second window. For the first request a user makes in the minute,
we can store the user’s ID against a count. On subsequent requests within the same window, we check to see that the user has not exceeded the limit (i.e., the count is not greater than ten). If the user hasn’t, we increment the count by one; otherwise, the request is dropped and an error is triggered. At the end of the window, we reset every user’s record to count 0 and repeat the process for the current window.

### Sliding logs

The sliding logs algorithm keeps track of the timestamp for each request a user makes. It does not impose a fixed window for all users. The process of logging the requests is as follows:

- Retrieve all requests logged in the last window (60s) and check if the number of requests exceeds the allowed limit
- If the number of requests is less than the limit, log the request and process it
- If the number of requests is equal to the limit, drop the request

### Sliding window counter

In this technique, the user’s requests are grouped by timestamp, and rather than log each request, we keep a counter for each group.

### Token bucket

In the token bucket algorithm, we simply keep a counter indicating how many tokens a user has left and a timestamp showing when it was last updated. When the first request is received, we log the timestamp and then create a new bucket of tokens for the user. On subsequent requests, we test whether the window has elapsed since the last timestamp was created. If it hasn’t, we check whether the bucket still contains tokens for that particular window. If it does, we will decrement the tokens by 1 and continue to process the request; otherwise, the request is dropped and an error is triggered.

In a situation where the window has elapsed since the last timestamp, we update the timestamp to that of the current request and reset the number of tokens to the allowed limit.

This is one of the efficient algorithms.

### Leaky bucket

The leaky bucket algorithm makes use of a queue that accepts and processes requests in a first-in, first-out (FIFO) manner. The limit is enforced on the queue size. If, for example, the limit is ten requests per minute, then the queue would only be able to hold ten requests at a time.

As requests get queued up, they are processed at a relatively constant rate. This means that even when the server is hit with a burst of traffic, the outgoing responses are still sent out at the same rate.

Once the queue is filled up, the server will drop any more incoming requests until space is freed up for more.

This technique smooths out traffic, thus preventing server overload. However, traffic shaping may result in a perceived overall slowness for users, because requests are being throttled, affecting your application’s UX.

> **Traffic shaping** (also known as packet shaping) is a bandwidth management technique that delays the flow of certain types of network packets in order to ensure network performance for higher-priority applications. In this context, it describes the ability to manage server resources to process and respond to requests at a certain rate, no matter the amount of traffic it receives.

## Using `rate-limiter-flexible`

One of the packages that we can use to rate-limit our apps in NodeJS is `rate-limiter-flexible`. This package provides various classes to use as a storage space for the rate-limiting information. We can store the data from a request in the server memory with `RateLimiterMemory` class or in an external DB, for example with `RateLimiterMongo` or `RateLimiterPostgres`.

### Example 1

Here's an example of how you can use `rate-limiter-flexible` to limit the number of requests made to your server from a specific IP address:

```js
const express = require("express");
const app = express();

const { RateLimiterMemory } = require("rate-limiter-flexible");

const rateLimiter = new RateLimiterMemory({
  points: 10, // number of requests allowed for a time window
  duration: 10, // time window in seconds
});

const handleRequest = async (req, res, next) => {
  const ip = req.ip; // get the client's IP address

  try {
    await rateLimiter.consume(ip); // consume a point from the rate limiter

    console.log("Request allowed");
    next();
  } catch (err) {
    res.status(429).send("Too many requests");
  }
};

app.get("/", handleRequest, (req, res) => {
  res.status(200).send({ message: "You've reached the home directory" });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

In the above example, we configure the `RateLimiterMemory` so that every IP can consume 10 points in 10 second-time-window. In each request, we consume one point. So, if in 10 seconds more than 10 requests come from an IP, we respond with the status code of 429 "Too many requests".

### Some properties of rate-limiting classes from `rate-limiter-flexible`

There are various properties that the rate limiting classes from the `rate-limiter-flexible` accept:

- `points`: default is 4. We use this as the maximum number of points that can be consumed over duration. Limiter compares this number with number of consumed points by key to decide if an operation should be rejected or resolved.
- `duration`: default is 1. This is the number of seconds before consumed points are reset, starting from the time of the first consumed point on a key. Points will be reset every second if the points never expire, if duration is 0.

In the below example, only 10 points can be consumed every 10 seconds. Points will be reset every 10 seconds.

```js
const rateLimiter = new RateLimiterMemory({
  points: 10, // number of requests allowed for a time window
  duration: 10, // time window in seconds,
});
```

- `blockDuration`: default is 0. If `blockDuration` is a positive number and more points are consumed than available, the limiter rejects further consume calls for that key during this `blockDuration` time.

In the below example, 10 points are available for consumption in 30 seconds. If more than 10 points are consumed, then the requests (for a key, not for everyone's request) will be rejected for 5 seconds.

```js
const rateLimiter = new RateLimiterMemory({
  points: 10,
  duration: 30,
  blockDuration: 5,
});
```

### Example 2

In this example, we are identifying the request by the ip address and the request method:

```js
const express = require("express");
const app = express();

const { RateLimiterMemory } = require("rate-limiter-flexible");

const rateLimiter = new RateLimiterMemory({
  points: 10, // number of requests allowed for a time window
  duration: 10, // time window in seconds
});

const handleRequest = async (req, res, next) => {
  const { ip, method } = req; // get the client's IP address and HTTP method

  try {
    await rateLimiter.consume(`${ip}:${method}`); // consume a point from the rate limiter based on IP and HTTP method

    console.log("Request allowed");
    next();
  } catch (err) {
    res.status(429).send("Too many requests");
  }
};

app.get("/", handleRequest, (req, res) => {
  res.status(200).send({ message: "You've reached the home directory" });
});

app.post("/", handleRequest, (req, res) => {
  res.status(200).send({
    message: "You've reached the home directory with the POST method",
  });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### The `consume` method

We use the `consume` method to consume points in a specific time window. Points can be consumed by IP address, user ID, authorization token, API route or any other string.

It responds with `RateLimiterRes` object. This object has useful properties

- `remainingPoints` - Number of remaining points in current duration
- `msBeforeNext` - Number of milliseconds before next action can be done
- `consumedPoints` - Number of consumed points in current duration
- `isFirstInDuration` - `true` if the action is first in current duration

The `consume` method accepts

- a key (an IP, a user ID, or anything to identify the request),
- points to consume, and
- optional options object

Here is an example where we consume 2 points for every request:

```js
const express = require("express");
const app = express();

const { RateLimiterMemory } = require("rate-limiter-flexible");

const rateLimiter = new RateLimiterMemory({
  points: 10, // number of requests allowed for a time window
  duration: 10, // time window in seconds
  blockDuration: 15,
});

const handleRequest = async (req, res, next) => {
  const { ip, method } = req; // get the client's IP address and HTTP method

  try {
    const resLimiter = await rateLimiter.consume(`${ip}:${method}`, 2);

    console.log(resLimiter);
    console.log("Request allowed");
    next();
  } catch (err) {
    console.log(`rejected: ${err}`);
    res.status(429).send("Too many requests");
  }
};

app.get("/", handleRequest, (req, res) => {
  res.status(200).send({ message: "You've reached the home directory" });
});

app.post("/", handleRequest, (req, res) => {
  res.status(200).send({
    message: "You've reached the home directory with the POST method",
  });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

## Using `express-rate-limit`

Another package that we can use to rate-limit our apps in NodeJS is `express-rate-limit`.

### Example

An example with the recommended configuration is as follows:

```js
const express = require("express");
const app = express();

const { rateLimit } = require("express-rate-limit");

const limiter = rateLimit({
  windowMs: 1 * 60 * 1000, // 1 minute
  limit: 10, // Limit each IP to 10 requests per `window` (here, per 1 minute)
  standardHeaders: true, // Return rate limit info in the `RateLimit-*` headers
  legacyHeaders: false, // Disable the `X-RateLimit-*` headers
});

app.use(limiter);

app.get("/", (req, res) => {
  res.status(200).send({ message: "You've reached the home directory" });
});

app.post("/", (req, res) => {
  res.status(200).send({
    message: "You've reached the home directory with the POST method",
  });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### Properties of the `rateLimit` method

Let's learn more about the properties of the `rateLimit` method:

- `windowMs` accepts a number. It defaults to 60000 ms (= 1 minute). It is a time frame for which requests are checked/remembered. The time window for each user starts at their first request. After it elapses, the user’s hit count is reset to zero. A new time window is then started on their next request. Also, it's used in the `Retry-After` header when the limit is reached.

- `limit` accepts a number or a function that accepts the Express `req` and `res` objects and then returns a number. It specifies the maximum number of connections to allow during the window before rate limiting the client. It defaults to 5.

- `message` specifies the response body to send back when a client is rate limited. It may be a string, JSON object, or any other value that Express’s `res.send` method supports.
  - It can also be a (sync/async) function that accepts the Express request and response objects and then returns a string, JSON object or any other value the Express `res.send` function accepts.

Example with a string:

```js
const express = require("express");
const app = express();

const { rateLimit } = require("express-rate-limit");

const limiter = rateLimit({
  windowMs: 1 * 60 * 1000,
  limit: 10,
  message: "What do you think you are doing? Are you a hacker or something?!",
  standardHeaders: true,
  legacyHeaders: false,
});

app.use(limiter);

app.get("/", (req, res) => {
  res.status(200).send({ message: "You've reached the home directory" });
});

app.post("/", (req, res) => {
  res.status(200).send({
    message: "You've reached the home directory with the POST method",
  });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

Example with a json object:

```js
const express = require("express");
const app = express();

const { rateLimit } = require("express-rate-limit");

const limiter = rateLimit({
  windowMs: 1 * 60 * 1000, // 1 minute
  limit: 10, // Limit each IP to 10 requests per `window` (here, per 1 minute)
  message: {
    message: "What do you think you are doing? Are you a hacker or something?!",
  },
  standardHeaders: true, // Return rate limit info in the `RateLimit-*` headers
  legacyHeaders: false, // Disable the `X-RateLimit-*` headers
});

app.use(limiter);

app.get("/", (req, res) => {
  res.status(200).send({ message: "You've reached the home directory" });
});

app.post("/", (req, res) => {
  res.status(200).send({
    message: "You've reached the home directory with the POST method",
  });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

- `statusCode` accepts a number. It specifies the HTTP status code to send back when a client is rate limited. It defaults to 429.

```js
const express = require("express");
const app = express();

const { rateLimit } = require("express-rate-limit");

const limiter = rateLimit({
  windowMs: 1 * 60 * 1000, // 1 minute
  limit: 10, // Limit each IP to 10 requests per `window` (here, per 1 minute),
  message: "Stop sending so many requests",
  statusCode: 404, // this status code is not correct for this response. Added as a non-default example
  standardHeaders: true, // Return rate limit info in the `RateLimit-*` headers
  legacyHeaders: false, // Disable the `X-RateLimit-*` headers
});

app.use(limiter);

app.get("/", (req, res) => {
  res.status(200).send({ message: "You've reached the home directory" });
});

app.post("/", (req, res) => {
  res.status(200).send({
    message: "You've reached the home directory with the POST method",
  });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

- `handler` accepts a function. It is similar to the above `message` and `statusCode` properties. It sends back a response when a client is rate-limited. By default, sends back the `statusCode` and `message` set via the options, similar to this:

```js
const limiter = rateLimit({
  // ...
  handler: (req, res, next, options) =>
    res.status(options.statusCode).send(options.message),
});
```

- `legacyHeaders` accepts a boolean value. It specifies whether to send the legacy rate limit headers for

  - the limit (`X-RateLimit-Limit`),
  - current usage (`X-RateLimit-Remaining`) and
  - reset time (if the store provides it) (`X-RateLimit-Reset`) on all responses.

  If set to `true`, the middleware also sends the `Retry-After` header on all blocked requests.

  This option only defaults to `true` for backward compatibility, and it is recommended to set it to `false` and use `standardHeaders` instead.

- `standardHeaders` accepts a boolean value or `"draft-6"` or `"draft-7"`. It specifies whether to enable support for headers conforming to the [RateLimit header fields for HTTP standardization draft adopted by the IETF](https://github.com/ietf-wg-httpapi/ratelimit-headers?tab=readme-ov-file).

- `keyGenerator` accepts a function that accepts the Express `request` and `response` objects and then returns a string.. It specifies a method to retrieve custom identifiers for clients, such as their IP address, username, or API Key. By default, the client’s IP address is used.

```js
const express = require("express");
const app = express();

const { rateLimit } = require("express-rate-limit");

const limiter = rateLimit({
  windowMs: 1 * 60 * 1000,
  limit: 10,
  standardHeaders: true,
  legacyHeaders: false,
  keyGenerator: (req, res) => {
    return `${req.ip}-${req.method}`;
  },
});

app.use(limiter);

app.get("/", (req, res) => {
  res.status(200).send({ message: "You've reached the home directory" });
});

app.post("/", (req, res) => {
  res.status(200).send({
    message: "You've reached the home directory with the POST method",
  });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

- `skip` accepts a function that accepts the Express `request` and `response` objects and then returns `true` (to skip counting the request) or `false`. This function determines whether or not this request counts towards a client’s quota.

Here is an example, where we use the `skip` to avoid rate-limiting the requests with the `GET` method:

```js
const express = require("express");
const app = express();

const { rateLimit } = require("express-rate-limit");

const limiter = rateLimit({
  windowMs: 1 * 60 * 1000,
  limit: 10,
  standardHeaders: true,
  legacyHeaders: false,
  skip: (req, res) => {
    if (req.method === "GET") return true;
  },
});

app.use(limiter);

app.get("/", (req, res) => {
  res.status(200).send({ message: "You've reached the home directory" });
});

app.post("/", (req, res) => {
  res.status(200).send({
    message: "You've reached the home directory with the POST method",
  });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

It could also act as an allowlist for certain keys:

```js
const allowlist = ["192.168.0.56", "192.168.0.21"];

const limiter = rateLimit({
  // ...
  skip: (req, res) => allowlist.includes(req.ip),
});
```

### Rate-limiting a specific path

To use it only for a certain path, specify the url as the first parameter in `app.use`:

```js
const express = require("express");
const app = express();

const { rateLimit } = require("express-rate-limit");

const limiter = rateLimit({
  windowMs: 1 * 60 * 1000, // 1 minute
  limit: 10, // Limit each IP to 10 requests per `window` (here, per 1 minute)
  standardHeaders: true, // Return rate limit info in the `RateLimit-*` headers
  legacyHeaders: false, // Disable the `X-RateLimit-*` headers
});

app.use("/", limiter);

app.get("/", (req, res) => {
  res.status(200).send({ message: "You've reached the home directory" });
});

app.post("/", (req, res) => {
  res.status(200).send({
    message: "You've reached the home directory with the POST method",
  });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### Rate-limiting a specific endpoint

We can also use it to rate-limit a specific endpoint:

```js
const express = require("express");
const app = express();

const { rateLimit } = require("express-rate-limit");

const limiter = rateLimit({
  windowMs: 1 * 60 * 1000, // 1 minute
  limit: 10, // Limit each IP to 10 requests per `window` (here, per 1 minute)
  standardHeaders: true, // Return rate limit info in the `RateLimit-*` headers
  legacyHeaders: false, // Disable the `X-RateLimit-*` headers
});

app.get("/", limiter, (req, res) => {
  res.status(200).send({ message: "You've reached the home directory" });
});

app.post("/", (req, res) => {
  res.status(200).send({
    message: "You've reached the home directory with the POST method",
  });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### Rate-limiting with an external store

By default, the built-in memory-store is used. Deployments requiring more consistently enforced rate limits should use an external store. For example, `rate-limit-memcached`, `rate-limit-mongo`, `rate-limit-postgresql`. To use an external store, pass an instance of the store to the `rateLimit` function:

```js
const limiter = rateLimit({
  // ... other options,
  store: new ExternalStore(),
});
```

## Using `express-slow-down`

`express-slow-down` is built on top of `express-rate-limit`. It slows down responses rather than blocking them.

### Example

Here is a simple example of using `express-slow-down`:

```js
const express = require("express");
const app = express();

const { slowDown } = require("express-slow-down");

const limiter = slowDown({
  windowMs: 15 * 60 * 1000, // 15 minutes
  delayAfter: 5, // Allow 5 requests per 15 minutes.
  delayMs: (hits) => hits * 100, // Add 100 ms of delay to every request after the 5th one.
});

app.use(limiter);

app.get("/", (req, res) => {
  res.status(200).send({ message: "You've reached the home directory" });
});

app.post("/", (req, res) => {
  res.status(200).send({
    message: "You've reached the home directory with the POST method",
  });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

- In this example, we use a function: requests 1-5 are not delayed because `delayAfter` specifies that delay should start after 5 requests. After 5 requests:
  - request 6 is delayed by 600ms
  - request 7 is delayed by 700ms
  - request 8 is delayed by 800ms
  - and so on.
- After 15 minutes, the delay is reset to 0.

### Properties of the `slowDown` method

In the above example, we imported the `slowDown` method from `express-slow-down` to slow down the responses. This method accepts several properties:

- `delayAfter` specifies the max number of requests allowed during `windowMs` before the middleware starts delaying responses. It accepts
  - a number or
  - a function that accepts the Express `req` and `res` objects and then returns a number. It defaults to 1.
- `delayMs` specifies how many milliseconds should the duration of the delay be. It accepts
  - a number (in milliseconds) or
  - a function that accepts a number (number of requests in the current window), the Express `req` and `res` objects and then returns a number.
- `maxDelayMs` specifies the absolute maximum value for `delayMs`. After many consecutive attempts, the delay will always be this value. It accepts

  - a number (in milliseconds) or
  - a function that accepts the Express `req` and `res` objects and then returns a number.

  Defaults to `Infinity`.

```js
const express = require("express");
const app = express();

const { slowDown } = require("express-slow-down");

const limiter = slowDown({
  windowMs: 15 * 60 * 1000,
  delayAfter: 5,
  delayMs: (hits) => hits * 100,
  maxDelayMs: 4000,
});

app.use(limiter);

app.get("/", (req, res) => {
  res.status(200).send({ message: "You've reached the home directory" });
});

app.post("/", (req, res) => {
  res.status(200).send({
    message: "You've reached the home directory with the POST method",
  });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `express-slow-down` with an external store

Just like with `express-rate-limit`, we can use the `express-slow-down` with an external store:

```js
const { slowDown } = require("express-slow-down");
const { MemcachedStore } = require("rate-limit-memcached");

const speedLimiter = slowDown({
  windowMs: 15 * 60 * 1000, // 15 minutes
  delayAfter: 1, // Allow only one request to go at full-speed.
  delayMs: (hits) => hits * hits * 1000, // Add exponential delay after 1 request.
  store: new MemcachedStore({
    /* ... */
  }), // Use the external store
});

// Apply the rate limiting middleware to all requests.
app.use(speedLimiter);
```
