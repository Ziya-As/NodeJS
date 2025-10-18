# NodeJS and ExpressJS

- [NodeJS and ExpressJS](#nodejs-and-expressjs)
  - [Useful Links](#useful-links)
  - [Browser vs NodeJS comparison:](#browser-vs-nodejs-comparison)
  - [Checking the Node versions](#checking-the-node-versions)
  - [Using NPM](#using-npm)
  - [Running Node apps](#running-node-apps)
  - [Some global variables in NodeJS](#some-global-variables-in-nodejs)
  - [Using modules in NodeJS](#using-modules-in-nodejs)
    - [Enable ES6 import and exporting in NodeJS](#enable-es6-import-and-exporting-in-nodejs)
  - [Some built-in modules in NodeJS](#some-built-in-modules-in-nodejs)
    - [OS](#os)
      - [`os.userInfo()`](#osuserinfo)
      - [`os.uptime()`](#osuptime)
      - [`os.type()`, `os.release()`, `os.totalmem()`, `os.freemem()`](#ostype-osrelease-ostotalmem-osfreemem)
      - [`os.version()`, `os.homedir()`](#osversion-oshomedir)
    - [Process](#process)
      - [`process.argv`](#processargv)
      - [`process.env`](#processenv)
      - [`process.cwd()`](#processcwd)
      - [`process.nextTick()`](#processnexttick)
    - [Path](#path)
      - [`path.sep`](#pathsep)
      - [`path.join()`](#pathjoin)
      - [`path.basename()`, `path.extname()`](#pathbasename-pathextname)
      - [`path.resolve()`](#pathresolve)
      - [`path.normalize()`](#pathnormalize)
      - [`path.dirname()`](#pathdirname)
      - [`path.parse()`, `path.format()`](#pathparse-pathformat)
      - [`path.relative(<from>, <to>)`](#pathrelativefrom-to)
    - [Events](#events)
      - [`.emit('<eventName>')` and `.on('<eventName>', callbackFunction)`](#emiteventname-and-oneventname-callbackfunction)
      - [`once()`, `removeListener()` / `off()`, `removeAllListeners()`](#once-removelistener--off-removealllisteners)
      - [`.prependListener()`](#prependlistener)
      - [`.prependOnceListener()`](#prependoncelistener)
    - [FS](#fs)
      - [`fs.open()`, `fs.openSync`, `fsPromises.open()`](#fsopen-fsopensync-fspromisesopen)
      - [`fs.writeFile()`, `fs.writeFileSync()`, `fsPromises.writeFile()`](#fswritefile-fswritefilesync-fspromiseswritefile)
      - [Flags for `fs` module](#flags-for-fs-module)
      - [`fs.readFile()`, `fs.readFileSync()`, `fsPromises.readFile()`](#fsreadfile-fsreadfilesync-fspromisesreadfile)
      - [`fs.appendFile()`, `fs.appendFileSync()`, `fsPromises.appendFile()`](#fsappendfile-fsappendfilesync-fspromisesappendfile)
      - [`fs.rename()`, `fs.renameSync()`, `fsPromises.rename()`](#fsrename-fsrenamesync-fspromisesrename)
      - [`fs.unlink()`, `fs.unlinkSync()`, `fsPromises.unlink()`](#fsunlink-fsunlinksync-fspromisesunlink)
      - [`fs.stat()`, `fs.statSync()`, `fsPromises.stat()`](#fsstat-fsstatsync-fspromisesstat)
      - [`fs.access()`, `fsPromises.access()`](#fsaccess-fspromisesaccess)
      - [`fs.mkdir()`, `fs.mkdirSync()`, `fsPromises.mkdir()`](#fsmkdir-fsmkdirsync-fspromisesmkdir)
      - [`fs.rmdir()`, `fs.rmdirSync()`, `fsPromises.rmdir()`](#fsrmdir-fsrmdirsync-fspromisesrmdir)
      - [`fs.rm()`, `fs.rmSync()`, `fsPromises.rm()`](#fsrm-fsrmsync-fspromisesrm)
      - [`fs.readdir()`, `fs.readdirSync()`, `fsPromises.readdir()`](#fsreaddir-fsreaddirsync-fspromisesreaddir)
      - [`fs.copyFile()`, `fs.copyFileSync()`, `fsPromises.copyFile()`](#fscopyfile-fscopyfilesync-fspromisescopyfile)
    - [Stream](#stream)
      - [`fs.createReadStream()`](#fscreatereadstream)
      - [`fs.createWriteStream()`](#fscreatewritestream)
      - [`<readable>.pipe()`](#readablepipe)
      - [`stream.pipeline()`](#streampipeline)
      - [`<readable>.pipe()` vs `stream.pipeline()`](#readablepipe-vs-streampipeline)
      - [`<readable>.unpipe()`](#readableunpipe)
      - [`<readable>.pause()`](#readablepause)
      - [`.finished(stream[, options])`](#finishedstream-options)
    - [HTTP](#http)
      - [`http.createServer()`, `http.listen()`](#httpcreateserver-httplisten)
      - [`http.write()`](#httpwrite)
      - [`req.url()`, `res.end()`](#requrl-resend)
      - [`http.writeHead()`](#httpwritehead)
      - [`req.method()`](#reqmethod)
      - [`http.get()`](#httpget)
  - [ExpressJS](#expressjs)
    - [Creating a server](#creating-a-server)
    - [Response methods](#response-methods)
      - [`res.end()`](#resend)
      - [`res.json()`](#resjson)
      - [`res.redirect()`](#resredirect)
      - [`res.send()`](#ressend)
      - [`res.sendFile()`](#ressendfile)
      - [`res.sendStatus()`](#ressendstatus)
      - [`res.download()`](#resdownload)
      - [`express.static()`](#expressstatic)
    - [Middleware](#middleware)
      - [Adding middleware using the request method](#adding-middleware-using-the-request-method)
      - [`app.use()`](#appuse)
    - [Request methods and properties](#request-methods-and-properties)
      - [`req.params`](#reqparams)
      - [`req.query`](#reqquery)
      - [`req.cookies`](#reqcookies)
      - [`req.hostname`](#reqhostname)
      - [`req.ip`](#reqip)
      - [`req.originalUrl`, `req.baseUrl`, and `req.path`](#reqoriginalurl-reqbaseurl-and-reqpath)
      - [`req.protocol`](#reqprotocol)
      - [`req.route`](#reqroute)
      - [`req.secure`](#reqsecure)
    - [`app.post()`](#apppost)
    - [`app.put()`](#appput)
    - [`app.delete()`](#appdelete)
    - [`express.Router()`, `app.route`](#expressrouter-approute)
      - [`express.Router()`](#expressrouter)
      - [`app.route()`](#approute)
    - [Error handling](#error-handling)
      - [Handling Errors in Route Handler Functions](#handling-errors-in-route-handler-functions)
      - [Default Built-in Error Handler of Express](#default-built-in-error-handler-of-express)
      - [Handling Errors with the Custom Error Handling Middleware Functions](#handling-errors-with-the-custom-error-handling-middleware-functions)
        - [Calling the Custom Error Handling Middleware Function](#calling-the-custom-error-handling-middleware-function)
      - [Adding Multiple Middleware Functions for Error Handling](#adding-multiple-middleware-functions-for-error-handling)
        - [Error Handling while Calling Promise-based Methods](#error-handling-while-calling-promise-based-methods)
    - [Setting up a view engine in Express](#setting-up-a-view-engine-in-express)
      - [`res.locals`](#reslocals)
      - [`app.locals`](#applocals)
  - [Debug](#debug)
  - [A Note about Deployment](#a-note-about-deployment)

<hr>

## Useful Links

- https://www.freecodecamp.org/news/node-js-streams-everything-you-need-to-know-c9141306be93/
- https://www.freecodecamp.org/news/understanding-node-js-event-driven-architecture-223292fcbc2d/

<hr>

## Browser vs NodeJS comparison:

| Browser          | NodeJS           |
| ---------------- | ---------------- |
| DOM              | No DOM           |
| Window           | No Window        |
| Interactive Apps | Server Side Apps |
| No Filesystem    | Filesystem       |
| Fragmentation    | Versions         |
| ES6 Modules      | CommonJS         |

<hr>

## Checking the Node versions

If you want to check the version of the node try:

```bash
node --version
```

or

```bash
node -v
```

<hr>

## Using NPM

[npmjs.com](https://www.npmjs.com/) - is a website to find published node packages.

To check the npm version, we run

```bash
npm --version
```

or

```bash
npm -v
```

If you want to download a package for a specific project - _local dependency_, we can use:

```bash
npm i <packageName>
```

You can install a specific version of a package, by running

```bash
npm install <package-name>@<version>
```

If you want to install, let's say the version 8.3.1, you can do it like this:

```bash
npm i <packageName>@8.3.1
```

To use it in any project - _global dependency_, we can use:

```bash
npm install -g <packageName>
sudo install -g <packageName> // For mac
```

<hr>

`package.json` is a file that stores important information about project/package.
There are 3 ways to create it:

- manual approach (create package.json in the root)
- `npm init` (step by step, press enter to skip)
- `npm init -y` (everything default)

We need `package.json`, because we need to provide information about our package. Inside that file, we have dependencies property, which is useful and important in providing information about packages that our project depends upon.

The dependencies/packages which are installed using `npm i <packageName>` appear in `dependencies` part of `package.json` file. The installed package also appears in "node_modules" folder.

<hr>

For more information about "package.json" file, you can use this link

- [nodesource.com/blog/the-basics-of-package-json](https://nodesource.com/blog/the-basics-of-package-json/)

<hr>

A Note To Remember:

**When you are committing and pushing your app to GIT, you should put the "node_modules" directory into the ".gitignore" file.**

<hr>

There are certain packages that you might need only while building an app. We can add such packages to "dev-dependency". Command for "dev-dependency" is

```bash
npm i <packageName> --save-dev
```

Or

```bash
npm i <packageName> -D
```

<hr>

There is a "scripts" object in package.json file. If you want to create a shortcut to run certain commands, you can use this object. For example, if we have below:

```js
"scripts": {
    "start": "node app.js",
    "dev": "nodemon app.js"
}
```

Then, we can run the above by using these commands:

```bash
npm start
npm run dev
```

or

```bash
npm run start
npm run dev
```

<hr>

If we want to uninstall a package, we can use the below commands:

```bash
npm uninstall <packageName>
```

or

```bash
npm un <packageName>
```

To uninstall a dev-dependency, you need to add `-D`:

```bash
npm uninstall <packageName> -D
```

To uninstall a package, which was installed globally, you need to add `-g`:

```bash
npm uninstall <packageName> -g
```

<hr>

When a package is installed it looks like this in the "package.json" file:

```js
"dependencies": {
    "date-fns": "^2.29.3",
    "uuid": "^9.0.0"
}
```

This "`^2.29.3`" is an example for a version number of a package. The first number is a major version, the second number indicates a minor version, and the final number is a patch.

This "`^`" sign indicates that whenever a minor update is available, go ahead and install that, but don't automatically install the major update. If we delete this sign, and simply keep numbers, our project will work only with a specific version of a package, which is indicated in the "package.json" file.

If we have " `~` " in front of a version number (like this "`~2.29.3`"), this would indicate to only update the patch versions.

If we have " `\*` " instead of number, this would indicate to update the latest version (major, minor, patches).

<hr>

If you want to update your packages, you can use this command:

```bash
npm update
```

You can specify a single package to update as well:

```bash
npm update <package-name>
```

<hr>

## Running Node apps

To run the node app you can try 2 ways:

- Go to the directory where the file/app is located:

  ```bash
  cd <directoryName>
  ```

  For example,

  ```bash
  cd C:\Users\ZiyaA\learning NodeJS\tutorial
  ```

  Then, run the file in that directory using

  ```bash
  node <fileName.js>
  ```

  For example,

  ```bash
  node app.js
  ```

  You can also simply write:

  ```bash
  node app
  ```

<hr>

## Some global variables in NodeJS

You always have access to global variables in NodeJS.  
Some global variables in NodeJS:

- `__dirname` - path to current directory
- `__filename` - file name
- `require` - function to use modules (CommonJS)
- `module` - info about current module (file)
- `process` - info about env where the program is being executed

<hr>

## Using modules in NodeJS

If you want to split the code into different modules/files, you can do that in NodeJS.

Here we use `module.exports` to export two variables inside of an object.

```js
// app2.js file
const secret = `SUPER SECRET`;

// exported variables
const john = `John`;
const peter = `Peter`;

module.exports = { john, peter };
```

Here we use `module.exports` to export a single function.

```js
// app3.js file
const sayHi = (name) => {
  console.log(`Hi there ${name}`);
};

module.exports = sayHi;
```

Here, we use `require('./<filename>')` to import the exports from the relevant files.  
We always use the `.` in referring to the filename. If you want to go to an earlier folder, you can use more dots.

```js
// app.js file
const names = require("./app2");
const sayHi = require("./app3");

sayHi(`susan`); // Hi there susan
sayHi(names.john); // Hi there John
sayHi(names.peter); // Hi there Peter
```

<hr>

We can also export, as we declare a variable:

```js
// app4.js file
module.exports.items = ["item1", "item2"];
const person = {
  name: "bob",
};

module.exports.singlePerson = person;
```

```js
// app.js file
const data = require("./app4.js");

console.log(data);
```

Another way to export variables is to declare them with just `exports.variableName`:

```js
exports.add = (a, b) => a + b;
```

<hr>

### Enable ES6 import and exporting in NodeJS

NodeJS doesn’t support ES6 import directly. Node has experimental support for ES modules. To enable them, we need to make some changes to the package.json file.

In the `package.json` file add `“type” : “module”`. Adding this enables ES6 modules.

```js
{
  "name": "node-starter",
  "version": "0.0.0",
  "type": "module",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  }
}
```

Now, we can use ES6 import and exports:

```js
// test.js
const name = "Some name";
const surname = "Some surname";

export { name };
export default surname;
```

```js
// index.js
import { name } from "./test.js";
import defaultExport from "./test.js";

console.log(name); // Some name
console.log(defaultExport); // Some surname
```

<hr>

## Some built-in modules in NodeJS

Node has a lot of built-in modules.  
We'll cover a few modules, and a few methods of these.

<hr>

### OS

To set up a built-in module, we use `require()` without a dot or a slash:

```js
const os = require("os");
```

#### `os.userInfo()`

If we want a user info, we can use `os.userInfo()`:

```js
const os = require("os");

// info about current user
const user = os.userInfo();

console.log(user);
```

#### `os.uptime()`

To get the system uptime in seconds, we can use `os.uptime()`:

```js
console.log(`The System Uptime is ${os.uptime()} seconds`);
```

#### `os.type()`, `os.release()`, `os.totalmem()`, `os.freemem()`

Some more useful methods of the os module:

```js
const currentOS = {
  name: os.type(),
  release: os.release(),
  totalMem: os.totalmem(), // total memory
  freeMem: os.freemem(), // free memory
};
```

#### `os.version()`, `os.homedir()`

Here are additional methods from the OS module:

```js
os.version();
os.homedir();
```

<hr>

### Process

The `process` object provides information about, and control over, the current NodeJS process.

```
import process from 'node:process';
```

#### `process.argv`

You can access command-line arguments via the global `process` object. `process.argv` returns the command-line arguments as an array. The first element of the `process.argv` array is always 'node', and the second element is always the path to your file. Also be aware that all elements of `process.argv` are strings.

<hr>

#### `process.env`

The `process.env` property returns an object containing the user environment. An example of this object looks like:

```js
{
    TERM: 'xterm-256color',
    SHELL: '/usr/local/bin/bash',
    USER: 'maciej',
    PATH: '~/.bin/:/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin',
    PWD: '/Users/maciej',
    EDITOR: 'vim',
    SHLVL: '1',
    HOME: '/Users/maciej',
    LOGNAME: 'maciej',
    _: '/usr/local/bin/node'
}
```

You can learn the path for the environment where the Node is running using `process.env.PATH`

```js
console.log(process.env.PATH);
// Prints: 'C:\Windows\system32;C:\Windows;C:\Program Files\node\'
```

#### `process.cwd()`

The `process.cwd()` method returns the current working directory of the NodeJS process.

```js
import { cwd } from "node:process";

console.log(`Current directory: ${cwd()}`);
```

<hr>

#### `process.nextTick()`

As you try to understand the NodeJS event loop, one important part of it is `process.nextTick()`. Every time the event loop takes a full trip, we call it a tick.

When we pass a function to `process.nextTick()`, we instruct the engine to invoke this function at the end of the current operation, before the next event loop tick starts:

```js
process.nextTick(() => {
  // do something
});
```

<hr>

NodeJS also provides `setImmediate()`, which is equivalent to using `setTimeout(() => {}, 0)`, mostly used to work with the NodeJS Event Loop.

Any function passed as the `setImmediate()` argument is a callback that's executed in the next iteration of the event loop.

How is `setImmediate()` different from `setTimeout(() => {}, 0)` (passing a 0ms timeout), and from `process.nextTick()` and `Promise.then()`?

A function passed to `process.nextTick()` is going to be executed on the current iteration of the event loop, after the current operation ends. This means it will always execute before `setTimeout` and `setImmediate`.

A `setTimeout()` callback with a 0ms delay is very similar to `setImmediate()`. The execution order will depend on various factors, but they will be both run in the next iteration of the event loop.

A `process.nextTick` callback is added to `process.nextTick` queue. A `Promise.then()` callback is added to promises microtask queue. A `setTimeout`, `setImmediate` callback is added to macrotask queue.

Event loop executes tasks in `process.nextTick` queue first, and then executes promises microtask queue, and then executes macrotask queue.

Here is an example to show the order between `setImmediate()`, `process.nextTick()` and `Promise.then()`:

```js
const baz = () => console.log("baz");
const foo = () => console.log("foo");
const zoo = () => console.log("zoo");
const start = () => {
  console.log("start");

  setImmediate(baz);

  new Promise((resolve, reject) => {
    resolve("bar");
  }).then((resolve) => {
    console.log(resolve);

    process.nextTick(zoo);
  });

  process.nextTick(foo);
};

start();

// start foo bar zoo baz
```

<hr>

### Path

#### `path.sep`

To get the separator of a directory (some systems have a backslash, and others forward slash), we can use `path.sep`:

```js
const path = require("path");
console.log(path.sep);
```

#### `path.join()`

To provide folder, and file names, and join them as a directory, we can use `path.join()` method:

```js
const filePath = path.join("/content/", "subfolder", "test.txt");

console.log(filePath); // \content\subfolder\test.txt
```

Zero-length path segments are ignored. If the joined path string is a zero-length string then '`.`' will be returned, representing the current working directory.

```js
path.join("/foo", "bar", "baz/asdf", "quux", "..");
// Returns: '/foo/bar/baz/asdf'

path.join("foo", {}, "bar");
// Throws 'TypeError: Path must be a string. Received {}'
```

<hr>

#### `path.basename()`, `path.extname()`

To get the filename, we can use `path.basename()`:

```js
const filePath = path.join("/content/", "subfolder", "test.txt");
const base = path.basename(filePath);

console.log(base); // test.txt
```

To get the extension name, we can use `path.extname()`:

```js
console.log(path.extname(__filename));

path.extname("index.html");
// Returns: '.html'

path.extname("index.coffee.md");
// Returns: '.md'

path.extname("index.");
// Returns: '.'

path.extname("index");
// Returns: ''

path.extname(".index");
// Returns: ''

path.extname(".index.md");
// Returns: '.md'
```

You can get the file name without the extension by specifying a second argument to `basename`:

```js
// index.js
path.basename(__filename, path.extname(__filename)); // index
```

Another example:

```js
path.basename("/foo/bar/baz/asdf/quux.html", ".html");
// Returns: 'quux'
```

<hr>

#### `path.resolve()`

To get the absolute path to a certain file or folder, we can use `path.resolve()`:

```js
const absolute = path.resolve(__dirname, "content", "subfolder", "test.txt");

console.log(absolute);
```

If the first parameter starts with a slash, that means it's an absolute path:

```js
path.resolve("/etc", "joe.txt"); // '/etc/joe.txt'
```

More examples:

```js
console.log(path.resolve("/foo/bar", "./tmp/file/"));
// Output: '/foo/bar/tmp/file'

console.log(path.resolve("/foo/bar", "tmp/file/"));
// Output: '/foo/bar/tmp/file'

console.log(path.resolve("/foo/bar", "/tmp/file/"));
// Output: '/tmp/file'

console.log(path.resolve("/foo/bar", "./baz"));
// Output: '/foo/bar/baz'

console.log(path.resolve("/foo/bar", "baz"));
// Output: '/foo/bar/baz'

console.log(path.resolve("/foo/bar/", "/baz"));
// Output: '/baz
```

<hr>

#### `path.normalize()`

`path.normalize()` is another useful function, that will try and calculate the actual path, when it contains relative specifiers like `.` or `..`, or double slashes:

```js
path.normalize("/users/joe/..//test.txt");
// '/users/test.txt'

path.normalize("C:\\temp\\\\foo\\bar\\..\\");
// 'C:\\temp\\foo\\'
```

Neither `resolve` nor `normalize` check if a path exists. They just calculate the path based on the information they get.

<hr>

#### `path.dirname()`

Path module has its own method to retrieve the directory name with `path.dirname()`:

```js
console.log(path.dirname(__filename));
```

<hr>

#### `path.parse()`, `path.format()`

The `path.parse()` method returns an object whose properties represent significant elements of the `path`. `path.parse()` returns an object with properties such as `root`, `dir`, `base`, `ext`, and `name`:

```js
path.parse(__filename);
```

```js
path.parse("C:\\path\\dir\\file.txt");
/* Returns:
 { root: 'C:\\',
   dir: 'C:\\path\\dir',
   base: 'file.txt',
   ext: '.txt',
   name: 'file' } */
```

`path.format()` is the opposite of `path.parse()`.

```js
path.format({
  dir: "C:\\path\\dir",
  base: "file.txt",
});
// Returns: 'C:\\path\\dir\\file.txt'
```

<hr>

#### `path.relative(<from>, <to>)`

The `path.relative(<from>, <to>)` method returns the relative path from `<from>` to `<to>` based on the current working directory.

```js
const path = require("path");

console.log(
  path.relative(
    "C:\\users\\User1\\documents",
    "C:\\users\\User2\\documents\\MyFiles"
  )
);
// Output: '..\\User2\\documents\\MyFiles'
```

```js
const path = require("path");

console.log(
  path.relative(
    "C:\\users\\User1\\documents\\MyFiles",
    "C:\\users\\User1\\documents"
  )
);
// Output: '..'
```

<hr>

### Events

#### `.emit('<eventName>')` and `.on('<eventName>', callbackFunction)`

We can have events in NodeJS. The `events` module helps us to emit custom events using the `.emit('<eventName>')` method, and react to events using `.on('eventName', callbackFunction)`:

```js
const EventEmitter = require("events");

const customEmitter = new EventEmitter();

customEmitter.on("response", () => {
  console.log(`data received `);
});

customEmitter.emit("response");
```

Here is another example using the `Class` and `extends` syntax:

```js
const EventEmitter = require("events");

class MyEmitter extends EventEmitter {}

const myEmitter = new MyEmitter();

myEmitter.on("log", (msg) => console.log(msg));

setTimeout(() => {
  myEmitter.emit("log", "Log event emitted");
}, 2000);
```

We first need to listen to an event (with `.on()`), and only then emit.

We can provide arguments using the `.emit()` method. We also can have more than one `.on()` for the same event:

```js
const EventEmitter = require("events");

const customEmitter = new EventEmitter();

customEmitter.on("response", (name, id) => {
  console.log(`data received ${name} with id: ${id}`);
});

customEmitter.on("response", () => {
  console.log(`some other logic `);
});

customEmitter.emit("response", "john", 34);
```

Here is an example of creating a server with the `http` module and `.on()` method:

```js
const http = require("http");

const server = http.createServer(); // emits request event

server.on("request", (req, res) => {
  res.end("Welcome"); // writes "Welcome" to the webpage
});

server.listen(5000);
```

<hr>

#### `once()`, `removeListener()` / `off()`, `removeAllListeners()`

The EventEmitter object also exposes several other methods to interact with events, like

- `once()`: add a one-time listener
- `removeListener()` / `off()`: remove an event listener from an event
- `removeAllListeners()`: remove all listeners for an event

```js
import { EventEmitter } from "node:events";
class MyEmitter extends EventEmitter {}

const myEmitter = new MyEmitter();

let m = 0;

myEmitter.once("event", () => {
  console.log(++m);
});

myEmitter.emit("event");
// Prints: 1

myEmitter.emit("event");
// Ignored
```

```js
const EventEmitter = require("events");
class MyEmitter extends EventEmitter {}

const myEmitter = new MyEmitter();

// Registering event listener
const listener1 = () => {
  console.log("Listener 1 called");
};
myEmitter.on("event", listener1);

// Removing event listener
myEmitter.removeListener("event", listener1);

// Emitting the event
myEmitter.emit("event");
```

```js
const EventEmitter = require("events");

class MyEmitter extends EventEmitter {}

const myEmitter = new MyEmitter();

// Define an event handler function
const eventHandler = () => {
  console.log("The event has been emitted");
};

// Attach the event handler to the 'event' event
myEmitter.on("event", eventHandler);

// Emit the 'event' event
myEmitter.emit("event");
// Output: 'The event has been emitted'

// Detach the event handler from the 'event' event
myEmitter.off("event", eventHandler);

// Emit the 'event' event again
myEmitter.emit("event");
// No output
```

```js
const EventEmitter = require("events");

class MyEmitter extends EventEmitter {}

const myEmitter = new MyEmitter();

// Listener 1
myEmitter.on("event1", () => {
  console.log("event1 fired!");
});

// Listener 2
myEmitter.on("event1", () => {
  console.log("event1 fired again!");
});

// Fire event1
myEmitter.emit("event1");

// Output:
// event1 fired!
// event1 fired again!

// Remove all listeners for event1
myEmitter.removeAllListeners("event1");

// Fire event1 again
myEmitter.emit("event1");

// Output: (Nothing)
```

<hr>

#### `.prependListener()`

By default, event listeners are invoked in the order they are added. The `.prependListener()` method can be used as an alternative to add the event listener to the beginning of the listeners array.

```js
import { EventEmitter } from "node:events";
const myEE = new EventEmitter();
myEE.on("foo", () => console.log("a"));
myEE.prependListener("foo", () => console.log("b"));
myEE.emit("foo");
// Prints:
//   b
//   a
```

#### `.prependOnceListener()`

`.prependOnceListener()` method can be used as an alternative to add the event listener to the beginning of the listeners array. This is a similar method to the above one, but this just listens to the event once.

```js
import { EventEmitter } from "node:events";
const myEE = new EventEmitter();
myEE.once("foo", () => console.log("a"));
myEE.prependOnceListener("foo", () => console.log("b"));
myEE.emit("foo");
// Prints:
//   b
//   a
```

<hr>

### FS

`fs` module provides methods in asynchronous, synchronous and promise-based versions. Here all three versions of a method will be mentioned.

**Note:** The `fs/promises` module is available starting only from NodeJS v14. Before v14, after v10, you can use `require('fs').promises` instead. Before v10, after v8, you can use `util.promisify` to convert fs methods into promise-based methods.

<hr>

#### `fs.open()`, `fs.openSync`, `fsPromises.open()`

Before you're able to interact with a file that sits in your filesystem, you must get a file descriptor.

A file descriptor is a reference to an open file. It is a number, which is returned by opening the file using the `open()` method, offered by the `fs` module. This number uniquely identifies an open file in operating system. To use `open()`, we provide

- the directory where the file is, as the first argument
- an optional object with the `flag` property, and
- a callback function

```js
const fs = require("fs");

fs.open("/Users/joe/test.txt", (err, fd) => {
  // fd is our file descriptor
});
```

Here is a synchronous version - `fs.openSync`. It returns the file descriptor, instead of providing it in a callback:

```js
const fs = require("fs");

try {
  const fd = fs.openSync("/Users/joe/test.txt");
} catch (err) {
  console.error(err);
}
```

You can also open the file by using the promise-based `fsPromises.open` method offered by the `fs/promises` module.

```js
const fsPromises = require("fs/promises");

async function example() {
  let filehandle;
  try {
    filehandle = await fsPromises.open("/Users/joe/test.txt");
    console.log(filehandle.fd);
    console.log(await filehandle.readFile({ encoding: "utf8" }));
  } finally {
    if (filehandle) await filehandle.close();
  }
}
example();
```

Once you get the file descriptor, in whatever way you choose, you can perform all the operations that require it, like calling `fs.close()` and many other operations that interact with the filesystem.

<hr>

#### `fs.writeFile()`, `fs.writeFileSync()`, `fsPromises.writeFile()`

If we want to write to a file, we use the `fs.writeFile()` method. We provide

- the directory, where we want the file to be written, as the first argument
- the content that we want to write as a second argument,
- an optional object with the `flag` property, and
- a callback function

```js
const fs = require("fs");

// Data to be written to the file
const data = "Hello, World!\n";

// File path
const filePath = "./example.txt";

// Callback function called when the operation completes
function callback(err) {
  if (err) {
    console.error(err);
  }
  console.log(`Data has been written to ${filePath}`);
}

// Write data to the file at filePath
fs.writeFile(filePath, data, callback);
```

Alternatively, you can use the synchronous version `fs.writeFileSync()`:

```js
const fs = require("fs");

// Data to be written to the file
const data = "Hello, World!\n";

// File path
const filePath = "./example.sync.txt";

try {
  // Write data to the file at filePath
  fs.writeFileSync(filePath, data);
  console.log(`Data has been written to ${filePath}`);
} catch (error) {
  console.error(error);
}
```

Here is an example of using promise-based `writeFile()` method:

```js
const fsPromises = require("fs/promises");

// Data to be written to the file
const data = "Hello, World!\n";

// File path
const filePath = "./example.txt";

async function example() {
  try {
    await fsPromises.writeFile(filePath, data);
    console.log(`Data has been written to ${filePath}`);
  } catch (err) {
    console.error(err);
  }
}

example();
```

<hr>

#### Flags for `fs` module

By default, if file already exists then these methods will overwrite the existing content otherwise they will create a new file and write data into it. You can modify the default by specifying a flag.

The following flags are available wherever the flag option takes a string. Remember these flags are not just for writing to a file. They could be used with other methods, as well:

- `'a'`: Open file for appending. The file is created if it does not exist.

- `'ax'`: Like `'a'` but fails if the path exists.

- `'a+'`: Open file for reading and appending. The file is created if it does not exist.

- `'ax+'`: Like `'a+'` but fails if the path exists.

- `'as'`: Open file for appending in synchronous mode. The file is created if it does not exist.

- `'as+'`: Open file for reading and appending in synchronous mode. The file is created if it does not exist.

- `'r'`: Open file for reading. An exception occurs if the file does not exist.

- `'rs'`: Open file for reading in synchronous mode. An exception occurs if the file does not exist.

- `'r+'`: Open file for reading and writing. An exception occurs if the file does not exist.

- `'rs+'`: Open file for reading and writing in synchronous mode. Instructs the operating system to bypass the local file system cache.

  This is primarily useful for opening files on NFS mounts as it allows skipping the potentially stale local cache. It has a very real impact on I/O performance so using this flag is not recommended unless it is needed.

  This doesn't turn `fs.open()` or `fsPromises.open()` into a synchronous blocking call. If synchronous operation is desired, something like `fs.openSync()` should be used.

- `'w'`: Open file for writing. The file is created (if it does not exist) or truncated (if it exists).

- `'wx'`: Like `'w'` but fails if the path exists.

- `'w+'`: Open file for reading and writing. The file is created (if it does not exist) or truncated (if it exists).

- `'wx+'`: Like `'w+'` but fails if the path exists.

Here is an example using a flag to append data with asynchronous `fs.writeFile()`:

```js
const fs = require("fs");

// Data to be written to the file
const data = "More data appended\n";

// File path
const filePath = "./example.txt";

// Callback function called when the operation completes
function callback(err) {
  if (err) {
    console.error(err);
  }
  console.log(`Data has been written to ${filePath}`);
}

// Write data to the file at filePath
fs.writeFile(filePath, data, { flag: "a" }, callback);
```

<hr>

#### `fs.readFile()`, `fs.readFileSync()`, `fsPromises.readFile()`

If we want to read a file as a whole, we can use `fs.readFile()` method. We provide

- the directory, where the file is, as the first argument,
- the character encoding as the second argument,
- an optional object with the `flag` property, and
- a callback function. If the file is successfully read, the callback function receives two arguments:
  - An optional Error object, if an error occurred while trying to read the file. If no errors were encountered, this value will be `null` or `undefined`.
  - A Buffer object containing the data from the file

```js
const fs = require("fs");

fs.readFile("./example.txt", "utf8", (err, data) => {
  if (err) {
    console.error("Error reading file:", err);
  } else {
    console.log("File data:", data);
  }
});
```

Here's an example of using the `fs.readFile()` method with a flag:

```js
const fs = require("fs");

fs.readFile("./example.txt", "utf8", { flag: "r+" }, (err, data) => {
  if (err) {
    console.error("Error reading file:", err);
  } else {
    console.log("File data:", data);
  }
});
```

Alternatively, you can use the synchronous version `fs.readFileSync()`:

```js
const fs = require("fs");

try {
  const data = fs.readFileSync("./example.txt", "utf8");
  console.log("File data:", data);
} catch (err) {
  console.error("Error reading file:", err);
}
```

Here is an example of using promise-based `readFile()` method:

```js
const fsPromises = require("fs/promises");

async function main() {
  try {
    const data = await fsPromises.readFile("./example.txt", "utf8");
    console.log("File data:", data);
  } catch (err) {
    console.error("Error reading file:", err);
  }
}

main();
```

<hr>

#### `fs.appendFile()`, `fs.appendFileSync()`, `fsPromises.appendFile()`

There is an `appendFile()` method, which appends to a file and if a file doesn't exist, it is created:

```js
const fs = require("fs");

fs.appendFile(
  path.join(__dirname, "files", "test.txt"),
  `Testing text`,
  (err) => {
    if (err) throw err;
    console.log("Write complete");
  }
);
```

Here is an `appendFileSync()` example:

```js
const fs = require("fs");

fs.appendFileSync(
  path.join(__dirname, "sample.txt"),
  `Testing stuff with Sync`
);
```

Here is a `fsPromises.appendFile()` example:

```js
const fsPromises = require("fs/promises");

async function example() {
  try {
    const content = "Some content!";
    await fsPromises.appendFile("/Users/joe/test.txt", content);
  } catch (err) {
    console.error(err);
  }
}
example();
```

<hr>

#### `fs.rename()`, `fs.renameSync()`, `fsPromises.rename()`

There is `rename()` method which renames a file.

- The first parameter is the current path and name,
- the second is the new name,
- the third is the callback function.

Here we take a file "`reply.txt`" from a subdirectory "files", and rename it into "`newReply.txt`" :

```js
const fs = require("fs");
const path = require("path");

fs.rename(
  path.join(__dirname, "files", "reply.txt"),
  path.join(__dirname, "files", "newReply.txt"),
  (err) => {
    if (err) console.error(err);
    console.log("Rename complete");
  }
);
```

`fs.renameSync()` is the synchronous version:

```js
const fs = require("fs");

try {
  fs.renameSync("/Users/joe", "/Users/roger");
} catch (err) {
  console.error(err);
}
```

`fsPromises.rename()` is the promise-based version:

```js
const fsPromises = require("fs/promises");

async function example() {
  try {
    await fsPromises.rename("/Users/joe", "/Users/roger");
  } catch (err) {
    console.error(err);
  }
}
example();
```

<hr>

#### `fs.unlink()`, `fs.unlinkSync()`, `fsPromises.unlink()`

If you want to delete a file, you can use the `.unlink()` method for that.

```js
const fs = require("fs");
const path = require("path");

fs.unlink(path.join(__dirname, "sample2.txt"), (err) => {
  if (err) throw err;
});
```

Here is an example using `unlinkSync()`:

```js
const fs = require("fs");
const path = require("path");

try {
  fs.unlinkSync(path.join(__dirname, "sample2.txt"));
} catch (err) {
  console.error(err);
}
```

Here is an example using the Async-Await syntax with Promises:

```js
const fsPromises = require("fs/promises");
const path = require("path");

const fileOps = async () => {
  try {
    await fsPromises.unlink(path.join(__dirname, "files", "starter.txt"));
  } catch (err) {
    console.error(err);
  }
};

fileOps();
```

<hr>

#### `fs.stat()`, `fs.statSync()`, `fsPromises.stat()`

Every file comes with a set of details that we can inspect using NodeJS. In particular, using the `stat()` method provided by the `fs` module.

You call it passing a file path, and once NodeJS gets the file details, it will call the callback function you pass, with 2 parameters:

- an error message, and
- the file stats:

What kind of information can we extract using the stats?

A lot, including:

- if the file is a directory or a file, using `stats.isFile()` and `stats.isDirectory()`
- if the file is a symbolic link using `stats.isSymbolicLink()`
- the file size in bytes using `stats.size`.

```js
const fs = require("fs");

fs.stat("/Users/joe/test.txt", (err, stats) => {
  if (err) {
    console.error(err);
    return;
  }

  stats.isFile(); // true
  stats.isDirectory(); // false
  stats.isSymbolicLink(); // false
  stats.size; // 1024000 //= 1MB
});
```

NodeJS also provides synchronous version `fs.statSync()`:

```js
const fs = require("fs");

try {
  const stats = fs.statSync("/Users/joe/test.txt");

  stats.isFile(); // true
  stats.isDirectory(); // false
  stats.isSymbolicLink(); // false
  stats.size; // 1024000 //= 1MB
} catch (err) {
  console.error(err);
}
```

You can also use promise-based `stat()` method offered by the `fs/promises` module if you like:

```js
const fsPromises = require("fs/promises");

async function example() {
  try {
    const stats = await fsPromises.stat("/Users/joe/test.txt");
    stats.isFile(); // true
    stats.isDirectory(); // false
    stats.isSymbolicLink(); // false
    stats.size; // 1024000 //= 1MB
  } catch (err) {
    console.error(err);
  }
}

example();
```

<hr>

#### `fs.access()`, `fsPromises.access()`

The NodeJS `fs` core module provides many handy methods you can use to work with folders.

Use `fs.access()` (and its promise-based `fsPromises.access()` counterpart) to check if the folder exists and NodeJS can access it with its permissions.

<hr>

#### `fs.mkdir()`, `fs.mkdirSync()`, `fsPromises.mkdir()`

To create a new directory, we can use `fs.mkdir()`. We provide:

- path that we want to create,
- optional object to indicate if the parent folders of the to-be-created folder should be created or not. To indicate that the parent folders should be created we use `recursive:true`. Otherwise, it's `false` by default.
- the callback function

```js
const fs = require("fs");

fs.mkdir("./newdir", (err) => {
  if (err) {
    console.log("Error creating directory: ", err);
  } else {
    console.log("Directory created successfully");
  }
});
```

Here is an example using the `recursive` property.

This one will throw an error as it doesn't have the `recursive` property and if the parent folders do not exist:

```js
const fs = require("fs");

fs.mkdir("./path/to/new/directory", { recursive: true }, (err) => {
  if (err) {
    console.log("Error creating directory: ", err);
  } else {
    console.log("Directory created successfully");
  }
});
```

This one should work without an issue, and parent folders should be created as well:

```js
const fs = require("fs");

fs.mkdir("./path/to/new/directory", { recursive: true }, (err) => {
  if (err) {
    console.log("Error creating directory: ", err);
  } else {
    console.log("Directory created successfully");
  }
});
```

Here is the synchronous version - `fs.mkdirSync()`:

```js
const fs = require("fs");

try {
  fs.mkdirSync("./newdir");
  console.log("Directory created successfully");
} catch (err) {
  console.error(err);
}
```

Here is the promise-based version - `fsPromises.mkdir()`:

```js
const fsPromises = require("fs/promises");

async function example() {
  try {
    await fsPromises.mkdir("./newdir");
    console.log("Directory created successfully");
  } catch (err) {
    console.error(err);
  }
}

example();
```

<hr>

#### `fs.rmdir()`, `fs.rmdirSync()`, `fsPromises.rmdir()`

`fs.rmdir()` is used to remove an empty and existing directory. We provide

- a path to be deleted, and
- a callback function that will be called after the directory is removed or if an error occurs during the removal.

```js
const fs = require("fs");

fs.rmdir("/path/to/empty/directory", (err) => {
  if (err) {
    console.log("Error deleting directory: ", err);
  } else {
    console.log("Directory deleted successfully");
  }
});
```

Here is a synchronous version - `fs.rmdirSync()`

```js
const fs = require("fs");

try {
  fs.rmdirSync("/path/to/empty/directory");
  console.log("Directory deleted successfully");
} catch (err) {
  console.log("Error deleting directory: ", err);
}
```

Here is a promise-based version - `fsPromises.rmdir()`

```js
const fsPromises = require("fs/promises");

async function deleteDirectory() {
  try {
    await fsPromises.rmdir("/path/to/directory", { recursive: true });
    console.log("Directory deleted successfully");
  } catch (err) {
    console.log("Error deleting directory: ", err);
  }
}

deleteDirectory();
```

<hr>

#### `fs.rm()`, `fs.rmSync()`, `fsPromises.rm()`

To remove a folder that has contents use `fs.rm()` with the option `{ recursive: true }` to recursively remove the contents.

```js
const fs = require("fs");

fs.rm("/path/to/directory", { recursive: true }, (err) => {
  if (err) {
    console.log("Error deleting directory: ", err);
  } else {
    console.log("Directory and its contents deleted successfully");
  }
});
```

`{ recursive: true, force: true }` makes it so that exceptions will be ignored if the folder does not exist.

```js
const fs = require("fs");

fs.rm("/path/to/directory", { recursive: true, force: true }, (err) => {
  if (err) {
    console.log("Error deleting directory: ", err);
  } else {
    console.log("Directory and its contents deleted successfully");
  }
});
```

Here is the synchronous version - `fs.rmSync()`

```js
const fs = require("fs");

const dirPath = "./test-dir";

// Remove the directory synchronously
try {
  fs.rmSync(dirPath, { recursive: true, force: true });
  console.log(`Directory removed at ${dirPath}`);
} catch (err) {
  console.error(`Failed to remove directory: ${err}`);
}
```

Here is the promise-based version - `fsPromises.rm()`

```js
const fsPromises = require("fs/promises");

const dirPath = "./test-dir";

async function removeDir() {
  try {
    await fsPromises.rm(dirPath, { recursive: true, force: true });
    console.log(`Directory removed at ${dirPath}`);
  } catch (err) {
    console.error(`Failed to remove directory: ${err}`);
  }
}

removeDir();
```

<hr>

#### `fs.readdir()`, `fs.readdirSync()`, `fsPromises.readdir()`

The `fs.readdir()` method takes a pathname as its first argument and a callback as its second. It returns the files in a given directory as an array.

```js
"use strict";
const fs = require("fs");

fs.readdir("/path/to/directory", (err, files) => {
  if (err) {
    console.log("Error reading directory: ", err);
  } else {
    console.log("Files in directory:");
    files.forEach((file) => {
      console.log(`- ${file}`);
    });
  }
});
```

Here is the synchronous version - `fs.readdirSync()`

```js
const fs = require("fs");

try {
  const files = fs.readdirSync("/path/to/directory");

  console.log("Files in directory:");

  files.forEach((file) => {
    console.log(`- ${file}`);
  });
} catch (err) {
  console.log("Error reading directory: ", err);
}
```

Here is the promise-based version - `fsPromises.readdir()`

```js
const fsPromises = require("fs/promises");

async function readDirectory() {
  try {
    const files = await fsPromises.readdir("/path/to/directory");

    console.log("Files in directory:");

    for (const file of files) {
      console.log(`- ${file}`);
    }
  } catch (err) {
    console.log("Error reading directory: ", err);
  }
}

readDirectory();
```

<hr>

#### `fs.copyFile()`, `fs.copyFileSync()`, `fsPromises.copyFile()`

The `fs.copyFile(src, dest [,mode], callback)` method takes in

- the source file,
- the destination file,
- the optional mode for copying to the destination file, and
- the callback function

It copies the source file into the destination file asynchronously. If the destination file already exists, it will be overwritten.

```js
const fs = require("fs");

fs.copyFile("./source/file.txt", "./destination/file.txt", (err) => {
  if (err) {
    console.log("Error copying file: ", err);
  } else {
    console.log("File copied successfully");
  }
});
```

Here is the synchronous version - `fs.copyFileSync()`:

```js
const fs = require("fs");

try {
  fs.copyFileSync("./source/file.txt", "./destination/file.txt");
  console.log("File copied successfully");
} catch (err) {
  console.log("Error copying file: ", err);
}
```

Here is the promise-based version - `fsPromises.copyFile()`:

```js
const { copyFile } = require("fs/promises");

async function copyFileAsync() {
  try {
    await copyFile("./source/file.txt", "./destination/file.txt");
    console.log("File copied successfully");
  } catch (err) {
    console.log("Error copying file: ", err);
  }
}

copyFileAsync();
```

<hr>

### Stream

Streams help to write and read data sequentially. There are 4 different streams in NodeJS:

- Writable
- Readable
- Duplex
- Transform

All streams are instances of `EventEmitter`.

When we use methods such as `readFile` or `writeFile`, we are taking the whole file. However, we can read or write data in chunks. This is especially handy when the handled file is big.

#### `fs.createReadStream()`

Here is a quick example of reading a file using `.createReadStream()`:

```js
const { createReadStream } = require("fs");

const stream = createReadStream("./content/big.txt");

stream.on("data", (result) => {
  console.log(result);
});
```

The data that is read comes in buffer of 64 kb (except the last chunk). But we can control the number of bytes received in each chunk using the `highWaterMark: <byteNumber>`:

```js
const { createReadStream } = require("fs");

const stream = createReadStream("./content/big.txt", { highWaterMark: 90000 });

stream.on("data", (result) => {
  console.log(result);
});
```

We can also set the encoding:

```js
const { createReadStream } = require("fs");

const stream = createReadStream("./content/big.txt", {
  highWaterMark: 90000,
  encoding: "utf8",
});

stream.on("data", (result) => {
  console.log(result);
});
```

It is better to remember checking for errors as well:

```js
const { createReadStream } = require("fs");

const stream = createReadStream("./content/big.txt", {
  highWaterMark: 90000,
  encoding: "utf8",
});

stream.on("data", (result) => {
  console.log(result);
});

stream.on("error", (err) => console.error(err));
```

<hr>

#### `fs.createWriteStream()`

Here is an example where we read a file in streams and write the stream into a different file:

```js
const fs = require("fs");

const rs = fs.createReadStream("./files/lorem.txt", { encoding: "utf8" });

const ws = fs.createWriteStream("./files/new-lorem.txt");

rs.on("data", (dataChunk) => {
  ws.write(dataChunk);
});
```

#### `<readable>.pipe()`

A better way to do the above is using the `.pipe()` method:

```js
const fs = require("fs");

const rs = fs.createReadStream("./files/lorem.txt", { encoding: "utf8" });

const ws = fs.createWriteStream("./files/new-lorem.txt");

rs.pipe(ws);
```

Calling the `<writable>.end()` method signals that no more data will be written to the Writable. The optional chunk and encoding arguments allow one final additional chunk of data to be written immediately before closing the stream.

```js
// Write 'hello, ' and then end with 'world!'.
const fs = require("node:fs");
const file = fs.createWriteStream("example.txt");
file.write("hello, ");
file.end("world!");
// Writing more now is not allowed!
```

#### `stream.pipeline()`

`stream.pipeline()` method sets up a pipeline between a source stream (the first argument), zero or more transforming streams (the second and subsequent arguments), and a destination stream (the last argument).

```js
const fs = require("fs");
const pipeline = require("stream").pipeline;

const source = fs.createReadStream("./sourceFile.txt");
const destination = fs.createWriteStream("./destinationFile.txt");

pipeline(source, destination, (error) => {
  if (error) {
    console.error(`An error occurred: ${error}`);
  } else {
    console.log("The pipeline was finished successfully");
  }
});
```

#### `<readable>.pipe()` vs `stream.pipeline()`

The `pipe` and `pipeline` methods in NodeJS are both used to direct the output of one stream into another stream. However, there are some differences between the two:

- `pipe` is a method on individual streams and is used to connect a single source stream to a single destination stream. On the other hand, `pipeline` is a method on the `stream` module and is used to connect multiple streams together, with the output of each stream being passed to the next stream.

- `pipe` returns the destination stream, allowing further manipulation of the destination stream if needed. On the other hand, `pipeline` returns the last stream in the `pipeline` and provides no access to intermediate streams in the `pipeline`.

- `pipe` allows for error handling through the error event on the destination stream. With `pipeline`, error handling must be done manually, either by using `try...catch` blocks or by passing a callback function as an argument to `pipeline`.

Here is an example that illustrates the difference between the pipe method and the pipeline method in terms of error handling.

```js
const fs = require("fs");

const readStream = fs.createReadStream("file.txt");
const writeStream = fs.createWriteStream("file_copy.txt");

readStream.pipe(writeStream);

readStream.on("error", (err) => {
  console.error(`Error while reading the file: ${err.message}`);
});

writeStream.on("error", (err) => {
  console.error(`Error while writing to the file: ${err.message}`);
});
```

```js
const fs = require("fs");

const readStream = fs.createReadStream("file.txt");
const writeStream = fs.createWriteStream("file_copy.txt");

fs.pipeline(readStream, writeStream, (err) => {
  if (err) {
    console.error(`An error occurred: ${err.message}`);
  } else {
    console.log("File copied successfully!");
  }
});
```

#### `<readable>.unpipe()`

The `<readable>.unpipe()` method detaches a Writable stream previously attached using the `.pipe()` method.

```js
const fs = require("fs");
const stream = fs.createReadStream("sample.txt");

// Create a write stream to write the data from the read stream to a new file
const writeStream = fs.createWriteStream("copy_of_sample.txt");

// Start piping the data from the read stream to the write stream
stream.pipe(writeStream);

// Wait for some time
setTimeout(() => {
  console.log("Stopping the flow of data...");

  // Stop the flow of data by unpiping the read stream
  stream.unpipe(writeStream);
}, 5000);
```

<hr>

#### `<readable>.pause()`

`<readable>.pause()` is a method on a readable stream that temporarily pauses the flow of data from the readable stream to the underlying resource. Once the stream has been paused, the `<readable>.resume()` method can be called to resume the flow of data.

```js
const fs = require("fs");
const stream = fs.createReadStream("./file.txt");

stream.on("data", (chunk) => {
  console.log(chunk.toString());
  stream.pause();

  setTimeout(() => {
    console.log("resuming...");
    stream.resume();
  }, 1000);
});

stream.on("end", () => {
  console.log("end");
});
```

<hr>

#### `.finished(stream[, options])`

`.finished(stream[, options])` method from the `stream` module returns a Promise. Fulfills when the stream is no longer readable or writable.

```js
const { finished } = require("node:stream/promises");
const fs = require("node:fs");

const rs = fs.createReadStream("archive.tar");

async function run() {
  await finished(rs);
  console.log("Stream is done reading.");
}

run().catch(console.error);
rs.resume(); // Drain the stream.
```

<hr>

### HTTP

#### `http.createServer()`, `http.listen()`

We can create a server using `createServer()` method. It accepts 2 arguments:

- one for incoming request, and
- second for our response back.

We can write to a webpage using `response.write()` method and we end the connection with `response.end()` method.

```js
const http = require("http");

const server = http.createServer((inComingRequest, response) => {
  response.writeHead(200, { "Content-Type": "text/plain" });
  response.write("Welcome to our home page");
  response.end();
});

server.listen(5000); // localhost:5000
```

`.listen()` should always be at the end.

#### `http.write()`

The `write` method of the response object in NodeJS is used to send data back to the client as part of the HTTP response. This method can be called multiple times to send data in chunks, rather than all at once.

```js
const http = require("http");
const server = http.createServer((req, res) => {
  res.writeHead(200, { "Content-Type": "text/plain" });
  res.write("Hello, ");
  res.write("world!");
  res.end();
});

server.listen(8000, () => {
  console.log("Server listening on port 8000");
});
```

#### `req.url()`, `res.end()`

The request object of the `createServer()` method of HTTP has a `url` property, which is helpful to set up responses for different pages of a website. Here we give different responses depending on the page accessed:

```js
const http = require("http");

const server = http.createServer((inComingRequest, response) => {
  if (inComingRequest.url === "/") {
    // home page
    response.end("Welcome to our home page");
  } else if (inComingRequest.url === "/about") {
    // about page
    response.end("Here is our short history");
  } else {
    // when a non-existent page is being accessed
    response.end(`
        <h1>Oops!</h1>
        <p>We can't seem to find the page you are looking for</p>
        <a href="/">Back Home</a>
        `);
  }
});

server.listen(5000);
```

We always have to have `response.end()` method in our server. Otherwise, the response from server will never be finished.

<hr>

Below is a more complex example, where several pages are requested and served with a response of different files:

```js
const http = require("http");
const { readFileSync } = require("fs");

// get all files
const homePage = readFileSync("./navbar-app/index.html", "utf8");
const homeStyles = readFileSync("./navbar-app/styles.css", "utf8");
const homeImage = readFileSync("./navbar-app/logo.svg", "utf8");
const homeLogic = readFileSync("./navbar-app/browser-app.js", "utf8");

const server = http.createServer((req, res) => {
  const url = req.url;
  console.log(url);
  if (url === "/") {
    res.writeHead(200, { "content-type": "text/html" });
    res.write(homePage);
    res.end();
  } else if (url === "/about") {
    res.writeHead(200, { "content-type": "text/html" });
    res.write("<h1>About Page</h1>");
    res.end();
  } else if (url === "/styles.css") {
    res.writeHead(200, { "content-type": "text/css" });
    res.write(homeStyles);
    res.end();
  } else if (url === "/logo.svg") {
    res.writeHead(200, { "content-type": "image/svg+xml" });
    res.write(homeImage);
    res.end();
  } else if (url === "/browser-app.js") {
    res.writeHead(200, { "content-type": "text/javascript" });
    res.write(homeLogic);
    res.end();
  } else {
    res.writeHead(404, { "content-type": "text/html" });
    res.write("<h1>page not found</h1>");
    res.end();
  }
});

server.listen(5000);
```

<hr>

#### `http.writeHead()`

`.writeHead()` method helps us to send the headers for a response. We first write the status code, then the type of the content that we are sending back as a response.

```js
const http = require("http");

const server = http.createServer((req, res) => {
  res.writeHead(200, { "content-type": "text/html" });
  res.write("<h1>Welcome Page</h1>");
  res.end();
});

server.listen(5000);
```

Optionally one can give a human-readable status message as the second argument.

```JS
const http = require('http');
const server = http.createServer((req, res) => {
    res.writeHead(404, 'Not Found', { 'Content-Type': 'text/plain' });
    res.end('The requested page was not found.');
});

server.listen(8000, () => {
    console.log('Server listening on port 8000');
});
```

For a thorough list of

- HTTP status codes, you can use: [MDN HTTP status codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
- MIME (media) types, you can use: [MDN MIME types](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types)

<hr>

#### `req.method()`

We can retrieve more information about the request object of HTTP. If you'd like to know the method of the request, you can try `request.method`:

```js
const http = require("http");

const server = http.createServer((req, res) => {
  console.log(req.method);
  console.log(req.url);

  res.writeHead(200, { "content-type": "text/html" });
  res.write("<h1>Welcome Page</h1>");
  res.end();
});

server.listen(5000);
```

<hr>

#### `http.get()`

HTTP also has a `get()` method. It sends an HTTP GET request to a specified URL and returns the response as a stream. It could be used in two ways

- `http.get(options[, callback])`
- `http.get(url[, options][, callback])`

The response object of this method is a Node Stream object. You can treat Node Streams as objects that emit events. The three events that are of most interest are: "`data`", "`error`" and "`end`". This object also has a `setEncoding()` method. If you call this method with "`utf8`", the "`data`" events will emit Strings rather than the standard Node Buffer objects which you have to explicitly convert to Strings. Here is an example:

```js
const http = require("http");

// Replace with any URL you want
const url = "http://example.com";

http
  .get(url, (res) => {
    res.setEncoding("utf8");

    res.on("data", (chunk) => {
      console.log(chunk);
    });

    res.on("error", (err) => {
      console.error("Response error:", err.message);
    });
  })
  .on("error", (err) => {
    console.error("Request error:", err.message);
  });
```

Here is an example, using the `"end"` event of the response object:

```js
const http = require("http");
const url = process.argv[2];

http.get(url, (res) => {
  let receivedData = "";
  let characterQuantity;
  res.setEncoding("utf8");

  res.on("data", (data) => {
    receivedData += data;
  });

  res.on("end", () => {
    characterQuantity = receivedData.length;
    console.log(characterQuantity);
    console.log(receivedData);
  });
});
```

<hr>

## ExpressJS

To install the "`express`" package, use:

```bash
npm install express
```

<hr>

### Creating a server

Here is a simple way to create a server with Express:

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

app.listen(PORT, () => {
  console.log(`Server is listening on port ${PORT}`);
});
```

Here is simple server that responds to a get request with Express:

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

app.get("/", (req, res) => {
  res.send("Home Page");
});

app.listen(PORT, () => {
  console.log(`Server is listening on port ${PORT}`);
});
```

Here is a more elaborate server that responds to "home" and "about" pages and all other pages. We can also set status codes with Express:

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

app.get("/", (req, res) => {
  res.status(200).send("Home Page");
});

app.get("/about", (req, res) => {
  res.status(200).send("About Page");
});

app.use((req, res) => {
  res.status(404).send("<h1>resource not found</h1>");
});

app.listen(PORT, () => {
  console.log(`Server is listening on port ${PORT}`);
});
```

<hr>

### Response methods

The methods on the response object (usually denoted as `res`) in the following table can send a response to the client, and terminate the request-response cycle. If none of these methods are called from a route handler, the client request will be left hanging.

| Method             | Description                                                                           |
| ------------------ | ------------------------------------------------------------------------------------- |
| `res.end()`        | End the response process.                                                             |
| `res.json()`       | Send a JSON response.                                                                 |
| `res.jsonp()`      | Send a JSON response with JSONP support.                                              |
| `res.redirect()`   | Redirect a request.                                                                   |
| `res.render()`     | Render a view template.                                                               |
| `res.send()`       | Send a response of various types.                                                     |
| `res.sendFile()`   | Send a file as an octet stream.                                                       |
| `res.sendStatus()` | Set the response status code and send its string representation as the response body. |
| `res.download()`   | Prompt a file to be downloaded.                                                       |

<hr>

#### `res.end()`

<hr>

#### `res.json()`

The `res.json()` method sends a JSON response to the client. It sets the `Content-Type` header to `application/json`, converts the given data to JSON, and sends it back to the client.:

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

app.get("/", (req, res) => {
  res.status(200).json([{ name: "John" }, { name: "Susan" }]);
});

app.listen(PORT, () => {
  console.log(`Server is listening on port ${PORT}`);
});
```

<hr>

#### `res.redirect()`

The `res.redirect()` method redirects the client to a new location. It sets the Location header to the specified URI, and set the status code of 302 Found, by default. We can use it either by providing a path to it or by providing a path and a status code:

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

// Redirect from /old-url to /new-url
app.get("/old-url", (req, res) => {
  res.redirect("/new-url");
});

app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

Keep in mind that when you use `res.redirect()`, the redirection happens on the client-side, and the subsequent request is made by the client. If you want to perform server-side redirection (without involving the client), you might want to consider using `res.location()` and `res.status()` together

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

app.get("/server-side-redirect", (req, res) => {
  // Set the Location header to the new URL
  res.location("/new-page");

  // Set the status code to 302 (Found) or any other appropriate code
  res.status(302).end();
});

app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

<hr>

#### `res.send()`

The `res.send()` method sends a response to the client. It can send various types of responses, including strings, HTML, JSON, and streams. The `res.send()` method automatically sets the appropriate `Content-Type` header based on the type of data being sent:

- If you send a JavaScript object or an array, the `Content-Type` header is set to `application/json`.
- If you send a string, the `Content-Type` header is set to `text/html`.
- If you send a Buffer or a File stream, the `Content-Type` header is set to `application/octet-stream`.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

// Sending a plain text response
app.get("/text", (req, res) => {
  res.status(200).send("This is a plain text response");
});

// Sending an HTML response
app.get("/html", (req, res) => {
  res.status(200).send("<h1>This is an HTML response</h1>");
});

// Sending a JSON response
app.get("/json", (req, res) => {
  const jsonData = {
    key1: "value1",
    key2: "value2",
    key3: "value3",
  };
  res.status(200).send(jsonData);
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

In the case of streaming, you do not need to use `res.send()` explicitly because piping the stream to res takes care of sending the data to the client.

```js
const express = require("express");
const fs = require("fs");
const app = express();
const PORT = process.env.PORT || 3000;

app.get("/stream", (req, res) => {
  const filePath = "./example.txt";
  const fileStream = fs.createReadStream(filePath);

  // Pipe the stream to the response object
  fileStream.pipe(res);
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

<hr>

#### `res.sendFile()`

The `res.sendFile()` method is a method in Express for sending a file as a response. It's similar to `res.send()`, but it's specifically designed for sending files.

The `res.sendFile()` method accepts the following arguments:

- The path to the file to be sent.
- The Optional options object.
- The optional callback to be called after the file has been sent.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

app.get("/image.jpg", (req, res) => {
  const filePath = __dirname + "/public/images/image.jpg";

  res.sendFile(filePath, (err) => {
    if (err) {
      console.error(err);
      res.status(500).send("Internal Server Error");
    }
  });
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

<hr>

#### `res.sendStatus()`

The `res.sendStatus()` method in Express is used to send a response with a specified HTTP status code and a short message describing the status code. Below 2 are equivalent:

- `res.sendStatus(404)`.
- `res.status(404).send('Not Found')`.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

app.get("/not-found", (req, res) => {
  res.sendStatus(404);
});

app.get("/success", (req, res) => {
  res.sendStatus(200);
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

<hr>

#### `res.download()`

The `res.download()` method in Express is used to send a file as an attachment in the HTTP response, typically prompting the user to download the file. The `res.download()` method in Express can receive the following arguments:

- The path to the file you want to send for download.
- The optional filename to be used for the downloaded file. If not provided the actual name of the file will be used
- The Optional options object.
- The optional callback function.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

app.get("/download", (req, res) => {
  const filePath = "./files/example.txt";

  res.download(filePath, "custom-filename.txt", (err) => {
    if (err) {
      console.error(err);
      res.status(500).send("Internal Server Error");
    }
  });
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

<hr>

#### `express.static()`

The `express.static()` method in Express is a built-in middleware that serves static files, such as HTML, images, stylesheets, and JavaScript files. It simplifies the process of serving static content by configuring a static file server for your Express application. It creates a middleware function that maps the directory path to a URL path. It should be called before defining any routes or other middleware functions.

It accepts two main arguments:

- The root directory from which to serve static assets.
- The Optional options object.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

app.use(express.static("./public"));

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

In this example, we use `express.static()` to serve static files from a directory named "public". When the browser requests a resource from the root URL path, such as "<http://localhost:3000/index.html>", Express checks if the file exists under the "public" directory, and if so, serves the file as a static asset.

<hr>

### Middleware

#### Adding middleware using the request method

We can reference a (middleware) function in `app.get()` method. If we are referencing a middleware function, we should either terminate our response in the middleware or we should call `next()`.

Here, we are using `res.send()` to terminate our response in the middleware. This middleware only works when a `GET` request is done to the home page:

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

// req => middleware => res

const logger = (req, res, next) => {
  const method = req.method;
  const url = req.url;
  const time = new Date().getFullYear();
  console.log(method, url, time);
  res.send("Testing"); // *
};

app.get("/", logger, (req, res) => {
  res.send("Home");
});

app.get("/about", (req, res) => {
  res.send("About");
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

The above outputs "Testing" when a user moves to the homepage, because in the line \*, we terminate our response. But the below code, calls `next()`, and the output becomes "Home":

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

// req => middleware => res

const logger = (req, res, next) => {
  const method = req.method;
  const url = req.url;
  const time = new Date().getFullYear();
  console.log(method, url, time);
  next();
};

app.get("/", logger, (req, res) => {
  res.send("Home");
});

app.get("/about", (req, res) => {
  res.send("About");
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

#### `app.use()`

There is a better way to insert a middleware. We can use `app.use()` method. Below we first create a new "logger.js" file to insert our middleware there, as it is better to have middleware in a separate file. Then, we use `app.use()` instead of putting the middleware function into the `app.get()` method. Here, because we use the middleware before all the routes, the middleware works for all the routes and request methods:

```js
// logger.js file
const logger = (req, res, next) => {
  const method = req.method;
  const url = req.url;
  const time = new Date().getFullYear();
  console.log(method, url, time);
  next();
};

module.exports = logger;
```

```js
// app.js file
const express = require("express");
const app = express();
const logger = require("./logger");
const PORT = process.env.PORT || 3000;

// req => middleware => res

app.use(logger);

app.get("/", (req, res) => {
  res.send("Home");
});

app.get("/about", (req, res) => {
  res.send("About");
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

If we want the middleware to be applied to a specific path, we can provide the path to `app.use()`:

```js
const express = require("express");
const app = express();
const logger = require("./logger");
const PORT = process.env.PORT || 3000;

// req => middleware => res

app.use("/api", logger);

app.get("/", (req, res) => {
  res.send("Home");
});

app.get("/about", (req, res) => {
  res.send("About");
});

app.get("/api/items", (req, res) => {
  res.send("Items");
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

We can have more than one middleware in the `app.use()` method. We need to provide the middleware in an array, if we are going to have more than one middleware in the same `app.use()`:

```js
// authorize.js file
const authorize = (req, res, next) => {
  console.log("authorize");
  next();
};

module.exports = authorize;
```

```js
// app.js file
const express = require("express");
const app = express();
const logger = require("./logger");
const authorize = require("./authorize");
const PORT = process.env.PORT || 3000;

// req => middleware => res

app.use([logger, authorize]);

app.get("/", (req, res) => {
  res.send("Home");
});

app.get("/about", (req, res) => {
  res.send("About");
});

app.get("/api/items", (req, res) => {
  res.send("Items");
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

<hr>

To skip the rest of the middleware functions from a router middleware stack, call `next('route')` to pass control to the next route. NOTE: `next('route')` will work only in middleware functions that were loaded by using the `app.METHOD()` or `router.METHOD()` functions.

This example shows a middleware sub-stack that handles `GET` requests to the "/user/:id" path.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

app.get(
  "/user/:id",
  (req, res, next) => {
    // if the user ID is 0, skip to the next route
    if (req.params.id === "0") next("route");
    // otherwise pass the control to the next middleware function in this stack
    else next();
  },
  (req, res, next) => {
    // send a regular response
    res.send("regular");
  }
);

// handler for the /user/:id path, which sends a special response
app.get("/user/:id", (req, res, next) => {
  res.send("special");
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

<hr>

### Request methods and properties

#### `req.params`

We can access the URL parameters using `req.params`. To explain further, if you have the route "/user/:name", then the “name” property is available as `req.params.name`. Remember that `req.params` always returns a string.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

app.get("/users/:userId", (req, res) => {
  const userId = req.params.userId;
  res.send(`User ID: ${userId}`);
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

The name of route parameters must be made up of “word characters” ([A-Za-z0-9_]).

Since the hyphen (-) and the dot (.) are interpreted literally, they can be used along with route parameters for useful purposes.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

// hyphen to separate 2 URL parameters
app.get("/flights/:from-:to", (req, res) => {
  const { from, to } = req.params;
  res.send(`Flights from ${from} to ${to}`);
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

With the above code, if a user goes to "http://localhost:3000/flights/LAX-SFO", then `req.params` object would be like this: `{ "from": "LAX", "to": "SFO" }`

Instead of hyphen, we could also use a dot, and the result would be the same:

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

// dot to separate 2 URL parameters
app.get("/flights/:from.:to", (req, res) => {
  const { from, to } = req.params;
  res.send(`Flights from ${from} to ${to}`);
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

<hr>

#### `req.query`

If we want to get the query details from the url, we can use `req.query`:

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

app.get("/api/v1/=", (req, res) => {
  console.log(req.query);
  res.send("Hello World");
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

After writing the above code,

- if a user accesses "http://localhost:3000/api/v1/?search=a&limit=2", then we will have "`{ search: 'a', limit: '2' }`" in the console.
- If a user accesses "http://localhost:3000/api/v1/?name=john&id=4", then we will have "`{ name: 'john', id: '4' }`" in the console.

<hr>

#### `req.cookies`

`req.cookies` is an object that contains cookies sent by the request. If the request contains no cookies, it defaults to `{}`. We have to use `cookie-parser` middleware, to be able to access `req.cookies`.

```js
const express = require("express");
const app = express();
const cookieParser = require("cookie-parser");
const PORT = process.env.PORT || 3000;

app.use(cookieParser());

app.get("/api/v1", (req, res) => {
  console.log(req.cookies.name);
  res.end();
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

<hr>

#### `req.hostname`

`req.hostname` contains the hostname derived from the Host HTTP header.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

app.get("/api/v1", (req, res) => {
  console.log(req.hostname);
  res.end();
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

<hr>

#### `req.ip`

`req.ip` contains the remote IP address of the request.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

app.get("/api/v1", (req, res) => {
  console.log(req.ip);
  res.end();
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

<hr>

#### `req.originalUrl`, `req.baseUrl`, and `req.path`

Using `req.originalUrl`, `req.baseUrl`, and `req.path`

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

app.use("/admin", function (req, res, next) {
  // GET 'http://www.example.com/admin/new?sort=desc'
  console.log(req.originalUrl); // '/admin/new?sort=desc'
  console.log(req.baseUrl); // '/admin'
  console.log(req.path); // '/new'
  next();
});

app.get("/api/v1", (req, res) => {
  res.end("/api/v1");
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

#### `req.protocol`

`req.protocol` contains the request protocol string: either http or (for TLS requests) https.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

app.get("/api/v1", (req, res) => {
  console.log(req.protocol);
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

#### `req.route`

`req.route` contains the currently-matched route, a string. For example:

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

app.get("/user/:id?", function userIdHandler(req, res) {
  console.log(req.route);
  res.send("GET");
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

#### `req.secure`

`req.secure` is a Boolean property that is true if a TLS connection is established. Equivalent to `req.protocol === "https"`

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

app.get("/user/:id?", function userIdHandler(req, res) {
  console.log(req.protocol);
  res.send("GET");
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

<hr>

<hr>

### `app.post()`

To handle the `POST` request, we can use the `app.post()` method. We won't be able to immediately have access to the body of data that is sent. To get the data, we need to use a middleware. We can use a built-in middleware `express.urlencoded({ extended: false })`:

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

app.use(express.json()); // for parsing application/json
app.use(express.urlencoded({ extended: true })); // for parsing application/x-www-form-urlencoded

app.post("/login", (req, res) => {
  const { name } = req.body;
  if (name) {
    return res.status(200).send(`Welcome ${name}`);
  }

  res.status(401).send("Please Provide Credentials");
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

`req.body` contains key-value pairs of data submitted in the request body. By default, it is `undefined`, and is populated when you use body-parsing middleware such as `express.json()` or `express.urlencoded()`.

`express.urlencoded` is a middleware in the Express framework for NodeJS that parses incoming request bodies that have a `application/x-www-form-urlencoded` content type. This is commonly used for submitting HTML form data to a server.

<hr>

### `app.put()`

To make a `PUT` request, we can use the `app.put()` method:

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

const user = {
  id: 1,
  name: "Some Name",
};

app.put("/api/people/:id", (req, res) => {
  const { id } = req.params;
  const { name } = req.body;

  user.id = id;
  user.name = name;

  res.send(`User was changed: ${user.id}, ${user.name}`);
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

<hr>

### `app.delete()`

To make a `DELETE` request, we can use `app.delete()` method:

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

let people = [
  {
    id: 1,
    name: "Some Name",
  },
  {
    id: 2,
    name: "Some Other Name",
  },
];

app.delete("/api/people/:id", (req, res) => {
  const person = people.find((person) => person.id === Number(req.params.id));
  if (!person) {
    return res
      .status(404)
      .json({ success: false, msg: `no person with id ${req.params.id}` });
  }

  const newPeople = people.filter(
    (person) => person.id !== Number(req.params.id)
  );
  people = newPeople;

  return res.status(200).json({ success: true, data: newPeople });
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

<hr>

### `express.Router()`, `app.route`

#### `express.Router()`

Having all the routes and route-related functionality in one file might make our files unmanagable. Separating some routes or at least putting routing-related functionality to a different file might be helpful.

Use the `express.Router` class to create modular, mountable route handlers. A Router instance is a complete middleware and routing system; for this reason, it is often referred to as a “mini-app”.

The following example

- creates a router as a module using `const router = express.Router()`,
- loads a middleware function in it using `router.use()`,
- defines some routes using `router.route()`, and `router.get()`, and
- mounts the router module on a path in the main app using `app.use()`.

Create a router file named `birds.js` in the app directory, with the following content:

```js
// birds.js
const express = require("express");
const router = express.Router();

// middleware that is specific to this router
router.use((req, res, next) => {
  console.log("Time: ", Date.now());
  next();
});

// define the home page route
router
  .route("/")
  .get((req, res) => {
    res.send("Hello from the GET method route for birds");
  })
  .post((req, res) => {
    res.send("Hello from the POST method route for birds");
  });

// define the about route
router.get("/about", (req, res) => {
  res.send("About birds");
});

module.exports = router;
```

Then, load the router module in the app:

```js
const express = require("express");
const app = express();
const birds = require("./birds");
const PORT = process.env.PORT || 3000;

app.use("/birds", birds);

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

The app will now be able to handle requests to `/birds` and `/birds/about`, as well as call the time logging middleware function that is specific to the route.

For more information about routes, see: [Router() documentation](https://expressjs.com/en/4x/api.html#router).

<hr>

#### `app.route()`

You can also create chainable route handlers for a route path by using `app.route()`. Because the path is specified at a single location, creating modular routes is helpful, as is reducing redundancy and typos.

Here is an example of chained route handlers that are defined by using `app.route()`.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

app
  .route("/book")
  .get((req, res) => {
    res.send("Get a random book");
  })
  .post((req, res) => {
    res.send("Add a book");
  })
  .put((req, res) => {
    res.send("Update the book");
  });

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

<hr>

<hr>

### Error handling

Error handling functions in an application detect and capture multiple error conditions and take appropriate remedial actions to either recover from those errors or fail gracefully. Common examples of remedial actions are providing a helpful message as output, logging a message in an error log that can be used for diagnosis, or retrying the failed operation.

#### Handling Errors in Route Handler Functions

The simplest way of handling errors in Express applications is by putting the error handling logic in the individual route handler functions. We can either check for specific error conditions or use a `try... catch` block.

```js
const express = require("express");
const app = express();

app.use(express.json());
app.use(express.urlencoded({ extended: true }));

app.post("/post", (req, res) => {
  const { name } = req.body;

  // Check for error condition
  if (!name) {
    // Error handling logic: log the error
    console.log("input error");

    // Error handling logic: return error response
    return res
      .status(400)
      .json({ message: "Mandatory field: name is missing" });
  } else {
    return res.status(200).json(name);
  }
});
```

But this method of putting error handling logic in all the route handler functions is not clean. Adding error handling logic to a middleware is a cleaner option.

#### Default Built-in Error Handler of Express

When we use the Express framework to build our web applications, we get an error handler by default that catches and processes all the errors thrown in the application.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

// handle get request for path /productswitherror
app.get("/productswitherror", (req, res) => {
  // throw an error with status code of 400
  let error = new Error(`processing error in request at ${req.url}`);
  error.statusCode = 400;
  throw error;
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

When we invoke this route with URL `/productswitherror`, we will get an error but we do not have to handle this error since it is handled by the default error handler of the Express framework. Express catches this error for us and responds to the caller with the error’s status code, message, and stack trace (only for non-production environments). **But this behavior applies only to synchronous functions**.

However, the asynchronous functions that are called from route handlers which throw an error, need to be handled differently. The error from asynchronous functions are not handled by the default error handler in Express and result in the stopping (crashing) of the application.

To prevent this behaviour, we need to pass the error thrown by any asynchronous function invoked by route handlers and middleware, to the `next()` function as shown below:

```js
const asyncFunction = async (request, response, next) => {
  try {
    throw new Error(`processing error in request `);
  } catch (error) {
    next(error);
  }
};
```

Here we are catching the error and passing the error to the `next()` function. Now the application will be able to run without interruption and invoke the default error handler or any custom error handler if we have defined it.

However, this default error handler is not very elegant and user-friendly giving scant information about the error to the end-user. Adding custom error handling functions might be better to control the output of error handling functions.

#### Handling Errors with the Custom Error Handling Middleware Functions

When an error occurs in an asyncronous function in ExpressJS, we call the `next(error)` function with the error object as input. The Express framework will process this by skipping all the functions in the middleware function stack and triggering the functions in the error handling middleware function stack.

The error handling middleware functions are defined in the same way as other middleware functions, but they accept the error object as the first input parameter followed by the three input parameters.

```js
const express = require('express')
const app = express()

const errorHandler = (error, req, res, next) {
  // Error handling middleware functionality
}

// route handlers
app.get(...)
app.post(...)

// attach error handling middleware functions after route handlers
app.use(errorHandler)
```

These error-handling middleware functions are attached to the app instance after the route handler functions have been defined.

The built-in default error handler of Express described in the previous section is also an error-handling middleware function and is attached at the end of the middleware function stack if we do not define any error-handling middleware function.

Any error in the route handlers gets propagated through the middleware stack and is handled by the last middleware function which can be the default error handler or one or more custom error-handling middleware functions if defined.

##### Calling the Custom Error Handling Middleware Function

When we get an error in the application, the error object is passed to the error-handling middleware, by calling the `next(error)` function as shown below:

```js
const express = require('express')
const axios = require("axios")
const app = express()

const errorHandler = (error, req, res, next) {
  // Error handling middleware functionality
  console.log( `error ${error.message}`) // log the error
  const status = error.status || 400
  // send back an easily understandable error message to the caller
  res.status(status).send(error.message)
}

app.get('/products', async (req, res) => {
  try {
    const apiResponse = await axios.get("http://localhost:3001/products")

    const jsonResponse = apiResponse.data

    res.send(jsonResponse)
  } catch(error) {
    next(error) // calling next error handling middleware
  }

})
app.use(errorHandler)
```

#### Adding Multiple Middleware Functions for Error Handling

We can chain multiple error-handling middleware functions similar to what we do for other middleware functions.

Let us define two middleware error handling functions and add them to our routes:

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

// Error handling Middleware function for logging the error message
const errorLogger = (error, req, res, next) => {
  console.log(`error ${error.message}`);
  next(error); // calling next middleware
};

// Error handling Middleware function reads the error message
// and sends back a response in JSON format
const errorResponder = (error, req, res, next) => {
  res.header("Content-Type", "application/json");

  const status = error.status || 400;
  res.status(status).send({ message: error.message });
};

// Fallback Middleware function for returning
// 404 error for undefined paths
const invalidPathHandler = (req, res, next) => {
  res.status(404);
  res.send("invalid path");
};

// Route with a handler function which throws an error
app.get("/productswitherror", (req, res) => {
  let error = new Error(`processing error in request at ${req.url}`);
  error.statusCode = 400;
  throw error;
});

app.get("/products", async (request, response) => {
  try {
    const apiResponse = await axios.get("http://localhost:3001/products");

    const jsonResponse = apiResponse.data;

    response.send(jsonResponse);
  } catch (error) {
    next(error); // calling next error handling middleware
  }
});

// Attach the first Error handling Middleware
app.use(errorLogger);

// Attach the second Error handling Middleware
app.use(errorResponder);

// Attach the fallback Middleware
app.use(invalidPathHandler);

app.listen(PORT, () => {
  console.log(`Server listening at http://localhost:${PORT}`);
});
```

##### Error Handling while Calling Promise-based Methods

We can enable Express to catch errors in Promises by providing `next` as the final `catch` handler as shown in this example:

```js
app.get("/product", (request, response, next) => {
  axios
    .get("http://localhost:3001/product")
    .then((response) => response.json)
    .then((jsonresponse) => response.send(jsonresponse))
    .catch(next);
});
```

According to the Express Docs, from Express 5 onwards, the route handlers and middleware functions that return a Promise will call `next(value)` automatically when they reject or throw an error.

<hr>

### Setting up a view engine in Express

Template engines (also referred to as "view engines" by ExpressJS documentation) allow you to specify the structure of an output document in a template, using placeholders for data that will be filled in, when a page is generated. Templates are often used to create HTML, but can also create other types of documents.

You set the template engine to use and the location where Express should look for templates using `app.set()` to set the '`views'` and `'view engine'`. Remember that you will also have to install the package containing your template library too.

```js
const express = require("express");
const path = require("path");
const app = express();

// Set directory to contain the templates ('views')
app.set("views", path.join(__dirname, "views"));

// Set view engine to use, in this case 'some_template_engine_name'
app.set("view engine", "<some_template_engine_name>");
```

Here is an example with setting the "`pug`" as a template engine.

```js
const app = express();

// view engine setup
app.set("views", path.join(__dirname, "views"));
app.set("view engine", "pug");
```

To render the template files, we use `res.render()`.

`res.render()` takes three arguments:

- The name of the view template to render.
- An optional object of local variables to pass to the view template.
- An optional callback function that is executed after the view has been rendered.

Assume that you have a template file named "`index.pug`" which contains placeholders for data variables named '`title`' and "`message`".Then, you would call `res.render()` in a route handler function to create and send the HTML response like this:

```js
app.get("/", function (req, res) {
  res.render("index", { title: "About dogs", message: "Dogs rock!" });
});
```

If a callback is specified in `res.render()`, the rendered HTML string has to be sent explicitly.

```js
res.render("index", function (err, html) {
  res.send(html);
});
```

#### `res.locals`

Use `res.locals` to set variables accessible in templates rendered with `res.render`.

The variables set on `res.locals` are available within a single request-response cycle, and will not be shared between requests.

```js
app.use(function (req, res, next) {
  // Make `user` and `authenticated` available in templates
  res.locals.user = req.user;
  res.locals.authenticated = !req.user.anonymous;
  next();
});
```

In order to keep local variables for use in template rendering between requests, use `app.locals` instead.

#### `app.locals`

The `app.locals` object has properties that are local variables within the application, and will be available in templates rendered with `res.render`. Once set, the value of `app.locals` properties persist throughout the life of the application, in contrast with `res.locals` properties that are valid only for the lifetime of the request.

<hr>

## Debug

`debug` is like an augmented version of `console.log`, but unlike `console.log`, you don’t have to comment out debug logs in production code. Logging is turned off by default and can be conditionally turned on by using the `DEBUG` environment variable.

To see all the internal logs used in Express, set the "DEBUG" environment variable to "`express:*`" when launching your app.

```bash
$ DEBUG=express:* node index.js
```

On Windows, use the corresponding command.

```bash
> set DEBUG=express:* & node index.js
```

To see the logs only from the router implementation set the value of "`DEBUG`" to "`express:router`". Likewise, to see logs only from the application implementation set the value of "`DEBUG`" to "`express:application`", and so on.

<hr>

## A Note about Deployment

In a deployment, we need to use the port provided by the service provider. Because they handle lots of applications, they might like to set the available port independently. So, simply hardcoding the port in the production is not useful. It is better to set the port like this, so that either the system port of the environment the application is deployed gets used or the hardcoded value if we are testing our app locally:

```js
const PORT = process.env.PORT || 3000;
```

<hr>

<hr>
