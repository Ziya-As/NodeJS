# Workerpool module in NodeJS

## Useful Links

- https://www.npmjs.com/package/workerpool
- https://www.youtube.com/watch?v=yI1PjfXhIpk

## Intro

`workerpool` offers an easy way to create a pool of workers and manage them. `workerpool` implements a thread pool pattern:

- There is a pool of workers to execute tasks.
- New tasks are put in a queue.
- A worker executes one task at a time, and once finished, picks a new task from the queue.

To create a pool of workers, we can use the `pool` method

```js
const workerPool = require("workerpool");

const pool = workerPool.pool();
```

When no argument is provided to the `pool` method, a default worker is started which can be used to offload functions dynamically.

With arguments, this is the syntax for the `pool` method:

```js
workerpool.pool("path/to/file.js" [, options])
```

When the file path argument is provided, the provided script will be started as a dedicated worker. Note that on NodeJS, the file path must be an absolute file path like `__dirname + '/worker.js'`. There are a lot of properties that could be used in the options object. For the full list, it's better to refer to the npm page.

To offload the functions to the worker pool, we use the `pool.exec` method. Here is an example:

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

const workerPool = require("workerpool");

const pool = workerPool.pool();

const complexCalc = () => {
  let counter = 0;
  while (counter < 10_000_000_000) {
    counter++;
  }
  return counter;
};

app.get("/", (req, res) => {
  res.status(200).send("Quick response");
});

app.get("/heavy", (req, res) => {
  pool
    .exec(complexCalc)
    .then((result) => {
      console.log(result);
      res.status(200).send("Heavy response");
    })
    .catch((err) => {
      console.log(err);
    })
    .then(() => {
      pool.terminate();
    });
});

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

Here is an example, where the `pool` method uses an external file:

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

const workerPool = require("workerpool");

const pool = workerPool.pool("./worker.js");

app.get("/", (req, res) => {
  res.status(200).send("Quick response");
});

app.get("/heavy", (req, res) => {
  pool
    .exec("complexCalc")
    .then((result) => {
      console.log(result);
      res.status(200).send("Heavy response");
    })
    .catch((err) => {
      console.log(err);
    })
    .then(() => {
      // terminate all workers when done
      pool.terminate();
    });
});

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

```js
// worker.js
const workerPool = require("workerpool");

const complexCalc = () => {
  let counter = 0;
  while (counter < 10_000_000_000) {
    counter++;
  }
  return counter;
};

// create a worker and register public functions
workerPool.worker({
  complexCalc: complexCalc,
});
```

In the above example, pay attention that we have to use `workerPool.worker()` method in the worker file to create the worker and add the functions to it. In the main file, where we use `pool.exec("complexCalc")`, we refer to that function which is added to the worker. Pay attention that, when we use an external file, the function name in the `exec` method is provided as a string.

We can use the `pool.stats()` method, to get some details about the worker pool. It tells us about total number of workers, busy and idle workers, etc. In the below example, we use it in 2 places so that we can see the difference before the `exec` method passes a function to the worker and after the function returns the result.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

const workerPool = require("workerpool");

const pool = workerPool.pool("./worker.js");

app.get("/", (req, res) => {
  res.status(200).send("Quick response");
});

app.get("/heavy", (req, res) => {
  console.log(pool.stats());
  pool
    .exec("complexCalc")
    .then((result) => {
      console.log(result);
      console.log(pool.stats());

      res.status(200).send("Heavy response");
    })
    .catch((err) => {
      console.log(err);
    })
    .then(() => {
      pool.terminate();
    });
});

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

We can also use the `workerEmit` method from the `workerpool` library to send an event from the worker thread to the main thread. To listen to the emitted events, we use the `on` property in the options object of the `pool.exec` method. In the below example, pay attention that after the function name and before the options object, we use an empty array. That array is used to provide arguments to the function in the `exec` method. In our example, we don't provide anything, therefore the array is empty:

```js
// worker.js
const workerPool = require("workerpool");

const complexCalc = () => {
  let counter = 0;
  workerPool.workerEmit("Worker will now start the calculation");
  while (counter < 10_000_000_000) {
    counter++;
  }
  workerPool.workerEmit("Worker is done with the calculation");
  return counter;
};

workerPool.worker({
  complexCalc: complexCalc,
});
```

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

const workerPool = require("workerpool");

const pool = workerPool.pool("./worker.js");

app.get("/", (req, res) => {
  res.status(200).send("Quick response");
});

app.get("/heavy", (req, res) => {
  console.log(pool.stats());
  pool
    .exec("complexCalc", [], {
      on: (msg) => console.log(msg),
    })
    .then((result) => {
      console.log(result);
      console.log(pool.stats());

      res.status(200).send("Heavy response");
    })
    .catch((err) => {
      console.log(err);
    })
    .then(() => {
      pool.terminate();
    });
});

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```
