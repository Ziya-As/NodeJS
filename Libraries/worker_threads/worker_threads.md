# Worker Threads in NodeJS

- [Worker Threads in NodeJS](#worker-threads-in-nodejs)
  - [Useful Links](#useful-links)
  - [Intro](#intro)
  - [Using `cluster` module to handle CPU-intensive tasks](#using-cluster-module-to-handle-cpu-intensive-tasks)
  - [Using threads to handle CPU-intensive tasks](#using-threads-to-handle-cpu-intensive-tasks)
  - [Using the `worker_threads` module](#using-the-worker_threads-module)
  - [Optimizing our server with more worker threads](#optimizing-our-server-with-more-worker-threads)
  - [Diving deep into the `worker_threads` module](#diving-deep-into-the-worker_threads-module)
  - [Worker pool](#worker-pool)

<hr>

## Useful Links

- https://nodejs.org/api/worker_threads.html
- https://medium.com/@manikmudholkar831995/worker-threads-multitasking-in-nodejs-6028cdf35e9d
- https://www.digitalocean.com/community/tutorials/how-to-use-multithreading-in-node-js
- https://betterstack.com/community/guides/scaling-nodejs/nodejs-workers-explained/
- https://www.youtube.com/watch?v=P1sWw1bLyVg

## Intro

JavaScript is a single-threaded programming language. Being single-threaded means that only one set of instructions is executed at any time in the same process.

NodeJS runs JavaScript code in a single thread, which means that your code can only do one task at a time. However, NodeJS itself is multithreaded and provides hidden threads through the libuv library, which handles I/O operations like reading files from a disk or network requests. Through the use of hidden threads, NodeJS provides asynchronous methods that allow your code to make I/O requests without blocking the main thread.

## Using `cluster` module to handle CPU-intensive tasks

Although NodeJS has hidden threads, you cannot use them to offload CPU-intensive tasks. In a single threaded language, a time-consuming task would block the rest of the code from running until the heavy task is over. To overcome this, one option is to use the `cluster` module to have more processes.

A process is a running program in the operating system. It has its own memory and cannot see nor access the memory of other running programs. It also has an instruction pointer, which indicates the instruction currently being executed in a program. Only one task can be executed at a time.

When you run a program using the node command, you create a process. The operating system allocates memory for the program, locates the program executable on your computer’s disk, and loads the program into memory. It then assigns it a process ID and begins executing the program. At that point, your program has now become a process.

On a single core machine, the processes execute concurrently. That is, the operating system switches between the processes in regular intervals. For example, process D executes for a limited time, then its state is saved somewhere and the OS schedules process B to execute for a limited time, and so on. This happens back and forth until all the tasks have been finished. From the output, it might look like each process has run to completion, but in reality, the OS scheduler is constantly switching between them.

On a multi-core system—assuming you have four cores—the OS schedules each process to execute on each core at the same time. This is known as parallelism. However, if you create four more processes (bringing the total to eight), each core will execute two processes concurrently until they are finished.

We can fork new processes using the `cluster` module and pass a message between them. This achieves the following goals:

- The main process can communicate with the child process by sending and receiving events
- No memory is shared
- All the data exchanged is “cloned,” meaning that changing it on one side doesn’t change it on the other side
- If we don’t share memory, we don’t have race conditions

This is not the ideal solution. Forking a process is expensive and slow — it means running a new virtual machine from scratch and using a lot of memory, since processes don’t share memory.

We can use the same process, but using different heavy, synchronously-executed workloads inside a single forked process creates two problems:

- You may not be blocking the main app, but the forked process will only be able to process one task at a time
- If one task crashes the process, it will leave all tasks sent to the same process unfinished

To fix these problems, we need multiple forks, not only one. But we need to limit the number of forked processes because each one will have all the virtual machine code duplicated in memory, meaning a few MBs per process and a non-trivial boot time.

## Using threads to handle CPU-intensive tasks

Threads are very lightweight in terms of resources compared to forked processes. This is why worker threads were born.

Threads are like processes: they have their own instruction pointer and can execute one JavaScript task at a time. Unlike processes, threads do not have their own memory. Instead, they reside within a process’s memory. Threads can communicate with one another through message passing or by transferring ArrayBuffer instances. This makes them lightweight in comparison to processes, since spawning a thread does not ask for more memory from the operating system.

When it comes to the execution of threads, they have similar behavior to that of processes. If you have multiple threads running on a single core system, the operating system will switch between them in regular intervals, giving each thread a chance to execute directly on the single CPU. On a multi-core system, the OS schedules the threads across all cores and executes the JavaScript code at the same time. If you end up creating more threads than there are cores available, each core will execute multiple threads concurrently.

To allow the usage of threads, NodeJS introduced the `worker_threads` module. The advantage of using worker threads is that CPU-bound tasks don’t block the main thread and you can divide and distribute a task to multiple workers to optimize it. Worker threads operate in isolated contexts. Each worker operates on its own V8 engine and event loop, potentially running tasks in parallel on different CPU cores. This approach helps us avoid the race conditions problem regular threads have. At the same time, worker threads live in the same parent process as the main thread, so they use a lot less memory.

Note that the actual execution of threads on different cores depends on several factors, including the operating system's scheduling policies, the number of available cores, and the current load on each core.

The `worker_threads` module only provides the framework for parallel execution, but the underlying system architecture and OS scheduler is what determines how worker threads are allocated to CPU cores.

## Using the `worker_threads` module

To initiate a worker thread, we import the `Worker` class from the `worker_threads` module. Then, we need to create the worker thread using `new Worker(<workerFile.js>)`. This creates a new thread and the code in the `<workerFile.js>` file starts running in the new thread (on another core, if we have more cores).

To send a message to the main thread we use the `parentPort` class. More specifically, it has the `postMessage()` method which sends a message to the main thread.

When the `postMessage()` method is used, `message` event fires. We can listen to this event in the main thread, and receive the data sent from worker threads.

Also, there is an `error` event, which fires when there is an error in the worker thread.

Here is an example taking all of the above points into account:

```js
// worker.js
const { parentPort } = require("worker_threads");

let counter = 0;
for (let i = 0; i < 20_000_000_000; i++) {
  counter++;
}

parentPort.postMessage(counter);
```

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

const { Worker } = require("worker_threads");

app.get("/non-blocking", (req, res) => {
  res.status(200).send("This page is non-blocking");
});

app.get("/blocking", async (req, res) => {
  const worker = new Worker("./worker.js");

  worker.on("message", (data) => {
    res.status(200).send(`result is ${data}`);
  });

  worker.on("error", (err) => {
    res.status(404).send(`An error occurred: ${err}`);
  });
});

console.log("test");
app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

## Optimizing our server with more worker threads

In the previous example, we used a single worker thread. This helped to isolate a heavy computation in a worker thread, and the main thread was able to continue responding to requests. However, we can optimize our code further to use more CPUs, if they are available. In the following example, we are adding four worker threads (assuming we have 4 CPUs).

- We create a function which returns a Promise.
  - Inside this Promise, we initiate the threads, and make sure that this Promise resolves if there is a message from worker threads.
  - Also, when we initiate the worker threads with `new Worker()` syntax, we pass an object to it. This object has a `workerData` property, which could be accessed in the worker threads. We need this because we want to divide the heavy computation between our worker threads evenly.
- In the worker thread file, we do the actual heavy computation. We use the `workerData` that was passed from the main thread to the worker threads. In this object, we have the thread count. So, we use this value to know how many threads there should be and how much computation we need to have in a single thread.

```js
const { workerData, parentPort } = require("worker_threads");

let counter = 0;
for (let i = 0; i < 20_000_000_000 / workerData.thread_count; i++) {
  counter++;
}

parentPort.postMessage(counter);
```

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

const { Worker, workerData } = require("worker_threads");

// assume this device has 4 CPUs
const THREAD_COUNT = 4;

function createWorker() {
  return new Promise((resolve, reject) => {
    const worker = new Worker("./four_worker.js", {
      workerData: { thread_count: THREAD_COUNT },
    });
    worker.on("message", (data) => {
      resolve(data);
    });

    worker.on("error", (err) => {
      reject(`An error occurred: ${err}`);
    });
  });
}

app.get("/non-blocking", (req, res) => {
  res.status(200).send("This page is non-blocking");
});

app.get("/blocking", async (req, res) => {
  const workerPromises = [];
  for (let i = 0; i < THREAD_COUNT; i++) {
    workerPromises.push(createWorker());
  }
  // Promise.all() accepts Promises and resolves when all the accepted Promises resolved
  const thread_results = await Promise.all(workerPromises);
  let total = 0;

  for (let i = 0; i < thread_results.length; i++) {
    total += thread_results[i];
    console.log(i, thread_results[i]);
  }

  res.status(200).send(`result is ${total}`);
});

console.log("test");
app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

## Diving deep into the `worker_threads` module

To instantiate a worker thread, you can utilize the `Worker` constructor from the `worker_threads` module. The constructor takes the path to a script file that the worker will execute, allowing CPU-heavy tasks to be offloaded from the main thread.

```js
const Worker = require("worker_threads");

const worker = new Worker("/path/to/worker.js");
```

In the worker thread file:

- `parentPort` represents a communication channel with the main/parent thread, allowing the worker thread to send messages back to the parent thread.
- `parentPort.postMessage()` sends a message to the main thread.

```js
const Worker = require("worker_threads");

const worker = new Worker("/path/to/worker.js");
```

```js
// worker.js
const { parentPort } = require("worker_threads");

// some code

parentPort.message("some result sent to the main thread");
```

- We can pass an optional object when we initiate the worker thread with `new Worker()`. We can use various properties of this optional object. `workerData` is one of them. We use it to pass data to the worker thread.

```js
const Worker = require("worker_threads");

const worker = new Worker("/path/to/worker.js", { workerData: "some data" });
```

```js
// worker.js
const { workerData } = require("worker_threads");

console.log(workerData); // some data
```

- `env: SHARE_ENV`. Normally, worker threads and the main thread do not share the environment variables. But in the optional object, when we initiate the worker threads, we can set the `env` property to `SHARE_ENV`. This way, worker threads will be able to share the environment with the main thread.

```js
const { Worker, isMainThread, SHARE_ENV } = require("worker_threads");

if (isMainThread) {
  const worker = new Worker(__filename, { env: SHARE_ENV });

  worker.on("exit", () => {
    console.log(process.env.SOME_VAR);
  });
} else {
  process.env.SOME_VAR = "set in the worker";
}
```

- `eval: true`. We might have some short piece of code, that we want to run on a separate thread. Because it's a small piece of code, we might not want to create a separate file for a worker thread or to change the current file and use `isMainThread`. We can set the `eval` to `true` in the optional object, when we initiate the worker thread. With this property set to `true`, we are telling that the first argument to `new Worker` is a code that needs to be run on a different thread:

```js
const { Worker, SHARE_ENV } = require("worker_threads");

const worker = new Worker(`process.env.SOME_VAR = "set in the worker"`, {
  eval: true,
  env: SHARE_ENV,
});

worker.on("exit", () => {
  console.log(process.env.SOME_VAR);
});
```

- `message` event fires when a message is sent. We can listen to a message sent by worker to the main thread using:

```js
const Worker = require("worker_threads");

const worker = new Worker("/path/to/worker.js");

worker.on("message", (result) => {
  // some code
});
```

- We can also use `<worker>.postMessage` in the main thread, and listen to the `message` event in the worker thread to receive the messages that the main thread sends:

```js
const Worker = require("worker_threads");

const worker = new Worker("/path/to/worker.js", { workerData: "some data" });

worker.postMessage("Message from the main thread");
```

```js
// worker.js
const { parentPort, workerData } = require("worker_threads");

parentPort.on("message", (msg) => {
  console.log(`Main Thread: ${msg}`);
});

parentPort.message("some result sent to the main thread");
```

- `error` event fires when there is an error. We can listen to errors happening in a worker thread using:

```js
const Worker = require("worker_threads");

const worker = new Worker("/path/to/worker.js");

worker.on("error", (error) => {
  console.error("Worker error: ", error);
  // some code
});
```

- `exit` event. When the worker thread has no more code to run it exits. We can listen to the `exit` event, if we want to do when the worker thread exits.

```js
const Worker = require("worker_threads");

const worker = new Worker("/path/to/worker.js");

worker.on("exit", (error) => {
  worker.on("exit", (code) => console.log(`Worker exited with code ${code}.`));
});
```

- `online` event triggers in the main thread when the worker thread starts to execute.

```js
const Worker = require("worker_threads");

const worker = new Worker("/path/to/worker.js");

worker.on("online", () => console.log("worker started working"));
```

- `isMainThread`. Instead of having a separate file for a worker thread, and initiate the worker thread using `new Worker(<workerFile>.js)`, we can use the same file where we will have the main thread and the worker thread. We can use the `isMainThread`, which returns true if the thread is the main thread. Including `isMainThread`, in the `if... else` block, helps to distinguish between the main thread and the worker thread.

```js
const {
  Worker,
  isMainThread,
  parentPort
  workerData
} = require("worker_threads");

if (isMainThread) {
  const worker = new Worker(__filename, {workerData: "some data"});

  worker.on("message", msg => console.log(`Worker message received: ${msg}`));
  worker.on("error", err => console.error("Worker error: ", error));
  worker.on("exit", (code) => console.log(`Worker exited with code ${code}.`));
}
else {
  const data = workerData;
  parentPort.postMessage(`Your data is \"${data}\".`);
}
```

- We can use the `<worker>.terminate()` to stop the worker thread from executing the code

```js
const Worker = require("worker_threads");

const worker = new Worker("/path/to/worker.js");

worker.terminate();

worker.on("exit", (code) => {
  console.log(`Worker stopped working with the code: ${code}`);
});
```

- `threadId`. If we want to get the unique number for a worker thread, we can use the `<worker>.threadId` property:

```js
const { Worker, isMainThread } = require("worker_threads");

if (isMainThread) {
  const worker = new Worker(__filename);
  console.log(worker.threadId);
} else {
  console.log("Hello from worker");
}
```

## Worker pool

Initiating a new worker thread creates a separate instance of the V8 JavaScript engine along with associated resources. Consequently, excessive use of worker threads can lead to significant resource consumption on your system, potentially negating the benefits of utilizing worker threads altogether.

For CPU-bound tasks, having more worker threads than available CPUs can lead to context switching overhead, reducing efficiency. The optimal number of threads for maximizing performance is often related to the number of available CPUs, although the best ratio can vary based on the workload.

A good rule of thumb is to spawn a maximum of one less than available CPUs. So if your machine has 8 cores, the maximum number of workers that should be spawned is 7.

To avoid the overhead of creating and destroying worker threads per request, the worker pool pattern allows you to define a reusable pool of workers to execute tasks. Incoming tasks are put in a queue and subsequently executed by an available worker.

You can use these libraries to create and manage worker pools:

- [workerpool](https://www.npmjs.com/package/workerpool),
- [piscina](https://www.npmjs.com/package/piscina), and
- poolifier.
