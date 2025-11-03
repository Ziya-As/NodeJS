# Cluster in NodeJS

- [Cluster in NodeJS](#cluster-in-nodejs)
  - [Useful Links](#useful-links)
  - [Intro](#intro)
  - [A simple server without clustering](#a-simple-server-without-clustering)
  - [A simple server with clustering](#a-simple-server-with-clustering)
  - [Diving deeper into the `cluster` module](#diving-deeper-into-the-cluster-module)
    - [Messages between the master and workers](#messages-between-the-master-and-workers)
    - [`disconnect()` method and `disconnect` event](#disconnect-method-and-disconnect-event)
    - [`online` event](#online-event)

<hr>

## Useful Links

- https://nodejs.org/api/cluster.html
- https://www.digitalocean.com/community/tutorials/how-to-scale-node-js-applications-with-clustering

## Intro

When you run a NodeJS program on a system with multiple CPUs, it creates a process that uses only a single CPU to execute by default. Since NodeJS uses a single thread to execute your JavaScript code, all the requests to the application have to be handled by that thread running on a single CPU.

Clustering enhances the performance of NodeJS applications by enabling them to run on multiple processes. This technique allows them to use the full potential of a multi-core system. Running multiple processes leverages the processing power of multiple central processing unit (CPU) cores to enable parallel processing, reduce response times, and increase throughput.

When initializing the `cluster` module, the application creates the primary process, which then forks child processes into worker processes. The primary process acts as a load balancer, distributing the workload to the worker processes while each worker process listens for incoming requests. Remember there is no shared memory among processes.

Remember that clusters of NodeJS processes can be used to run multiple instances of NodeJS that can distribute workloads among their application threads. When process isolation is not needed, use the `worker_threads` module instead, which allows running multiple application threads within a single NodeJS instance.

## A simple server without clustering

Here is an example of setting up a server with a heavy computation without clustering.

```js
// no-cluster.js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

app.get("/", (req, res) => {
  res.status(200).send("Response from server");
});

app.get("/slow", (req, res) => {
  // Generate a large array of random numbers
  let arr = [];
  for (let i = 0; i < 100000; i++) {
    arr.push(Math.random());
  }

  // Heavy computation
  let sum = 0;
  for (let i = 0; i < arr.length; i++) {
    sum += arr[i] ** 2;
  }

  res.status(200).send("sum: " + sum);
});

app.listen(PORT, () => {
  console.log(`Server listening on port ${PORT}`);
});
```

## A simple server with clustering

Now we can spawn child processes using the `cluster` module, and compare the performance of our simple app to the one where we didn't use clustering. Each child process runs its event loop and shares the server port with the parent process, allowing better use of available resources.

To implement this, we need to import the NodeJS `cluster` and `os` modules. The `os` module provides information about your computer’s operating system. You need this module to retrieve the number of cores available on your system and ensure that you don’t create more child processes than cores on your system. Creating more child processes than cores on your system will cause it to spend more time switching between processes. This leads to increased overhead and decreased performance.

This is how we implement the clustering:

- If we are on a master process, we start child processes using `.fork()` method.
- If we are on a child process, we do the actual tasks (computation, routing, etc.).
- In the master process, we listen to the `exit` event using the `on()` method of the `cluster` module. This event is emitted when the process dies. We use this to start a new child process, if any of the existing child processes crash.

```js
// cluster.js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

const cluster = require("cluster");
const numCores = require("os").availableParallelism();

if (cluster.isMaster) {
  console.log(`Master process ${process.pid} is running`);
  console.log(`This machine has ${numCores} cores`);

  // Fork workers.
  for (let i = 0; i < numCores; i++) {
    cluster.fork();
  }

  cluster.on("exit", (worker, code, signal) => {
    console.log(`worker ${worker.process.pid} died`);

    // Replace the dead worker
    console.log("Starting a new worker");
    cluster.fork();
  });
} else {
  app.get("/", (req, res) => {
    res.status(200).send("Response from server");
  });

  app.get("/fast", (req, res) => {
    // Start timer
    console.time("slow");

    // Generate a large array of random numbers
    let arr = [];
    for (let i = 0; i < 1000000; i++) {
      arr.push(Math.random());
    }

    // Heavy computation
    let sum = 0;
    for (let i = 0; i < arr.length; i++) {
      sum += arr[i] ** 2;
    }

    // End the timer and send the response
    console.timeEnd("slow");
    res.status(200).send("sum: " + sum);
  });

  app.listen(PORT, () => {
    console.log(
      `Worker process id: ${process.pid}. Server listening on port ${PORT}`
    );
  });
}
```

In the above example,

- `cluster.isMaster` returns `true`, if it's a parent process.
- Within the parent process, we use the `fork()` method. What `fork()` really does is to create a new node process (child process).
- When a child process is created, it also executes the whole file. However, it's not a parent process, so `cluster.isMaster` returns false. Therefore, child process executes the `else` block.
- `cluster.isWorker` returns `true`, if it's a child process.

Now, if we want to compare both servers, we can install `loadtest` library, and run `loadtest http://localhost:3000/fast -n 100 -c 10` to send 100 requests (10 being concurrently) to both of our servers. We would see less time spent on responding to requests, and less errors when we use clustering.

## Diving deeper into the `cluster` module

### Messages between the master and workers

When a worker process is created, an IPC channel is created among the worker and the master, allowing the communication between them.

- From the master process, we can send a message to a worker process using the process reference, i.e. `<worker>.send({ ... })`. Messages can be strings or objects.
- From the worker process, we can send a message to the master using the current process reference, i.e. `process.send({ ... })`.
- We can listen to the `message` event. When we send a message from the master using `<worker>.send()`, this causes a `message` event in the worker process. Also, when we send a message from a worker to the master using `process.send()`, this causes a `message` event in the master process.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

const cluster = require("cluster");
const numCores = require("os").availableParallelism();

if (cluster.isMaster) {
  console.log(`Master process ${process.pid} is running`);

  // Fork workers.
  for (let i = 0; i < numCores; i++) {
    const worker = cluster.fork();

    worker.send({ msg: `Message from master: ${process.pid}` });

    worker.on("message", (message) => {
      console.log(
        `Master ${process.pid} receives a message ${JSON.stringify(
          message
        )} from worker: ${worker.process.pid}`
      );
    });
  }

  cluster.on("exit", (worker, code, signal) => {
    console.log(`worker ${worker.process.pid} died`);

    // Replace the dead worker
    console.log("Starting a new worker");
    cluster.fork();
  });
} else {
  process.on("message", (message) => {
    console.log(
      `worker ${process.pid} received a message: ${JSON.stringify(message)}`
    );
  });

  process.send({ msg: `Message from worker: ${process.pid}` });

  app.get("/", (req, res) => {
    res.status(200).send("Response from server");
  });

  app.listen(PORT, () => {
    // console.log(
    //   `Worker process id: ${process.pid}. Server listening on port ${PORT}`
    // );
  });
}
```

### `disconnect()` method and `disconnect` event

We can call the `<worker>.disconnect()` method to end a child worker process. This emits the `disconnect` event. We can listen to this event both in the master process and a worker process.

In the below example, we are deliberately disconnecting 2 worker processes. This emits the `disconnect` and `exit` events. We use the `disconnect` event to log that a worker process ended, and we use the `exit` event to log about the same worker process and restart a new one.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

const cluster = require("cluster");
const numCores = require("os").availableParallelism();

if (cluster.isMaster) {
  console.log(`Master process ${process.pid} is running`);

  // Fork workers.
  for (let i = 0; i < numCores; i++) {
    const worker = cluster.fork();

    if (i >= numCores - 2) worker.disconnect();
  }

  cluster.on("disconnect", (worker, code, signal) => {
    console.log(`worker ${worker.process.pid} was disconnected`);
  });

  cluster.on("exit", (worker, code, signal) => {
    console.log(`worker ${worker.process.pid} died`);

    // Replace the dead worker
    console.log("Starting a new worker");
    cluster.fork();
  });
} else {
  app.get("/", (req, res) => {
    res.status(200).send("Response from server");
  });

  app.listen(PORT, () => {
    console.log(`Worker - ${process.pid}.\nServer listening on port ${PORT}`);
  });
}
```

### `online` event

We can listen to the `online` event, to know that a worker process was created and is ready to serve.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

const cluster = require("cluster");
const numCores = require("os").availableParallelism();

if (cluster.isMaster) {
  console.log(`Master process ${process.pid} is running`);

  // Fork workers.
  for (let i = 0; i < numCores; i++) {
    cluster.fork();
  }

  cluster.on("online", function (worker) {
    console.log("Worker " + worker.process.pid + " is online");
  });

  cluster.on("exit", (worker, code, signal) => {
    console.log(`worker ${worker.process.pid} died`);

    // Replace the dead worker
    console.log("Starting a new worker");
    cluster.fork();
  });
} else {
  app.get("/", (req, res) => {
    res.status(200).send("Response from server");
  });

  app.listen(PORT, () => {
    console.log(`Worker - ${process.pid}.Server listening on port ${PORT}`);
  });
}
```
