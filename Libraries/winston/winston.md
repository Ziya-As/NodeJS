# Winston Logging

- [Winston Logging](#winston-logging)
  - [Some Useful Links](#some-useful-links)
  - [Intro](#intro)
    - [Using the Default Logger](#using-the-default-logger)
    - [Basic setup with `.createLogger`](#basic-setup-with-createlogger)
  - [Reconfiguring the logger instance](#reconfiguring-the-logger-instance)
  - [Child loggers](#child-loggers)
  - [Log Levels](#log-levels)
    - [NPM log levels](#npm-log-levels)
    - [Syslog log levels](#syslog-log-levels)
    - [Custom log levels](#custom-log-levels)
    - [Assigning Log Levels to Log Messages](#assigning-log-levels-to-log-messages)
  - [Format](#format)
    - [`winston.format.cli()`](#winstonformatcli)
    - [`winston.format.combine()` and `winston.format.timestamp()`](#winstonformatcombine-and-winstonformattimestamp)
    - [`winston.format.align()`](#winstonformatalign)
    - [`winston.format.printf()`](#winstonformatprintf)
    - [`winston.format.prettyPrint()`](#winstonformatprettyprint)
    - [`winston.format.label()`](#winstonformatlabel)
    - [`winston.format.simple()`](#winstonformatsimple)
    - [`winston.format.colorize()`](#winstonformatcolorize)
    - [`winston.format.splat()`](#winstonformatsplat)
    - [Not logging specific messages](#not-logging-specific-messages)
    - [Changing the log level dynamically](#changing-the-log-level-dynamically)
  - [Transports](#transports)
    - [Adding different levels to each transport](#adding-different-levels-to-each-transport)
    - [Saving in a different file based on the level](#saving-in-a-different-file-based-on-the-level)
    - [Adding or removing transports after initialization](#adding-or-removing-transports-after-initialization)
  - [Custom transports in Winston](#custom-transports-in-winston)
  - [Adding metadata to your logs](#adding-metadata-to-your-logs)
  - [Dealing with errors and promise rejections](#dealing-with-errors-and-promise-rejections)
    - [Logging errors in Winston](#logging-errors-in-winston)
    - [Handling uncaught exceptions and uncaught promise rejections](#handling-uncaught-exceptions-and-uncaught-promise-rejections)
    - [Handling the errors coming from the logger itself](#handling-the-errors-coming-from-the-logger-itself)
  - [Profiling your Node.js code with Winston](#profiling-your-nodejs-code-with-winston)
  - [Log Rotation](#log-rotation)
    - [Events on log rotation](#events-on-log-rotation)
  - [Working with multiple loggers in Winston](#working-with-multiple-loggers-in-winston)

<hr>

## Some Useful Links

- https://www.npmjs.com/package/winston
- https://github.com/winstonjs/winston
- https://github.com/winstonjs/winston-mongodb
- https://github.com/winstonjs/winston-daily-rotate-file
- https://betterstack.com/community/guides/logging/how-to-install-setup-and-use-winston-and-morgan-to-log-node-js-applications/
- https://www.youtube.com/watch?v=YjEqmINAQpI
- https://reflectoring.io/node-logging-winston/
- https://www.youtube.com/watch?v=2UTER21MCdk
- https://developer.ibm.com/tutorials/learn-nodejs-winston/

## Intro

To install `winston`, we use:

```bash
npm i winston
```

### Using the Default Logger

The default logger is accessible through the `winston` module directly:

```js
const winston = require("winston");

// both log a message on the "info" level
winston.log("info", "Info message");
winston.info("Another info message");

// setting the level to "debug"
winston.level = "debug";

// log a message on the "debug" level
winston.log("debug", "Debug message");
```

By default, no transports are set on the default logger. You must add or remove transports via the `add()` and `remove()` methods:

```js
const winston = require("winston");

const console = new winston.transports.Console();
const file = new winston.transports.File({ filename: "logs/combined.log" });

winston.add(console);
winston.add(file);

// remove logging to console
winston.remove(console);

// both log a message on the "info" level
winston.log("info", "Info message");
winston.info("Another info message");

// setting the level to "debug"
winston.level = "debug";

// log a message on the "debug" level
winston.log("debug", "Debug message");
```

### Basic setup with `.createLogger`

Winston loggers can be generated using the default logger `winston()`, but the simplest method with more options is to create your own logger using the `.createLogger()` method:

```js
const winston = require("winston");

const logger = winston.createLogger({
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: "logs/combined.log" }),
  ],
});

logger.info("Info message");
logger.error("Error message");
```

The `.createLogger` method accepts the following parameters:

| Name          | Default                     | Description                                                  |
| ------------- | --------------------------- | ------------------------------------------------------------ |
| `level`       | `'info'`                    | Log only if `info.level` is less than or equal to this level |
| `levels`      | `winston.config.npm.levels` | Levels (and colors) representing log priorities              |
| `format`      | `winston.format.json`       | Formatting for messages                                      |
| `transports`  | [] (No transports)          | Set of logging targets for messages                          |
| `exitOnError` | `true`                      | If `false`, handled exceptions will not cause `process.exit` |
| `silent`      | `false`                     | If `true`, all logs are suppressed                           |

Let's create a basic logger setup, this time using some more `.createLogger` parameters:

```js
const winston = require("winston");

const logger = winston.createLogger({
  level: "info",
  format: winston.format.json(),
  transports: [new winston.transports.Console()],
});

logger.info("Info message");
logger.error("Error message");
```

## Reconfiguring the logger instance

After configuring the winston logger instance, we can reconfigure it using the `configure` method:

```js
const winston = require("winston");

const logger = winston.createLogger({
  level: "info",
  transports: [new winston.transports.Console()],
});

// Replaces the previous transports with those in the new configuration
logger.configure({
  level: "error",
  transports: [new winston.transports.File({ filename: "logs/combined.log" })],
});

logger.info("Info message"); // this doesn't get logged because the level is set to "error"
logger.error("Error message");
```

## Child loggers

You can create child loggers from existing loggers to pass additional data:

```js
const winston = require("winston");

const logger = winston.createLogger({
  transports: [new winston.transports.Console()],
});

const childLogger = logger.child({ moreInfo: "some additional info" });

logger.info("Info message"); // {"level":"info","message":"Info message"}

childLogger.info("Child info message"); // {"level":"info","message":"Child info message","moreInfo":"some additional info"}
```

## Log Levels

Log level is the piece of information in our code that indicates the importance of a specific log message. Using appropriate log levels is one of the best practices for application logging. Logging levels in `winston` conform to the severity ordering specified by **RFC5424**.

When we specify a logging level for our Winston logger, it will only log anything at that level or higher.

Winston allows you to readily customize the log levels to your liking. The default log levels are defined in `winston.config.npm.levels`, but you can also use the Syslog levels through `winston.config.syslog.levels`:

```js
const winston = require("winston");

const logger = winston.createLogger({
  levels: winston.config.syslog.levels,
  level: process.env.LOG_LEVEL || "crit",
  transports: [new winston.transports.Console()],
});

logger.crit("critical message");
logger.error("error message");
```

### NPM log levels

`winston` by default uses npm logging levels. Npm logging levels are prioritized from 0 to 6 (highest to lowest):

```js
{
  error: 0,
  warn: 1,
  info: 2,
  http: 3,
  verbose: 4,
  debug: 5,
  silly: 6
}
```

- 0 - `error`: is a serious problem or failure, which halts current activity but leaves the application in a recoverable state with no effect on other operations. The application can continue working.
- 1 - `warn`: A non-blocking warning about an unusual system exception. These logs provide context for a possible error. It logs warning signs that should be investigated.
- 2 - `info`: This denotes major events and informative messages about the application’s current state. Useful for tracking the flow of the application.
- 3 - `http`: This logs out HTTP request-related messages.
- 4 - `verbose`: Records detailed messages that may contain sensitive information.
- 5 - `debug`: These logs will help us debug our code. Developers and internal teams should be the only ones to see these log messages. They should be disabled in production environments.
- 6 - `silly`: The current stack trace of the calling function should be printed out when silly messages are called. This information can be used to help developers and internal teams debug problems.

### Syslog log levels

Another option is to configure `winston` to use levels as specified by Syslog Protocol. The syslog levels are prioritized from 0 to 7 (highest to lowest):

```js
{
  emerg: 0,
  alert: 1,
  crit: 2,
  error: 3,
  warning: 4,
  notice: 5,
  info: 6,
  debug: 7
}
```

- 0 - `emerg` : Emergency: system is unusable
- 1 - `alert` : Alert: action must be taken immediately
- 2 - `crit` : Critical: critical conditions
- 3 - `error` : Error: error conditions
- 4 - `warning` : Warning: warning conditions
- 5 - `notice` : Notice: normal but significant condition
- 6 - `info` : Informational: informational messages
- 7 - `debug` : Debug: debug-level messages

### Custom log levels

If you prefer to change the levels to a completely custom system, you'll need to create an object and assign a number priority to each one starting from the most severe to the least. Afterwards, assign that object to the `levels` property in the configuration object passed to the `createLogger()` method.

```js
const logLevels = {
  fatal: 0,
  error: 1,
  warn: 2,
  info: 3,
  debug: 4,
  trace: 5,
};

const logger = winston.createLogger({
  levels: logLevels,
  level: process.env.LOG_LEVEL || "info",
  transports: [new winston.transports.Console()],
});

logger.fatal("fatal!");
logger.trace("trace!");
```

### Assigning Log Levels to Log Messages

There are two ways to log messages (and assign a level to each message):

- Use `.log` method with the name of the logging level as a string:

```js
logger.log("error", "error message");
logger.log("warn", "warn message");
logger.log("info", "info message");
logger.log("http", "http message");
logger.log("verbose", "verbose message");
logger.log("debug", "debug message");
logger.log("silly", "silly message");
```

- Call the level on the method for each level directly:

```js
logger.error("error message");
logger.warn("warn message");
logger.info("info message");
logger.http("http message");
logger.verbose("verbose message");
logger.debug("debug message");
logger.silly("silly message");
```

For Syslog levels, there are similar methods

```js
const winston = require("winston");

const logger = winston.createLogger({
  levels: winston.config.syslog.levels,
  level: "debug",
  transports: [new winston.transports.Console()],
});

logger.log("emerg", "Emergency message");
logger.log("alert", "Alert message");
logger.log("crit", "Critical message");
logger.log("error", "Error message");
logger.log("warning", "Warning message");
logger.log("notice", "Notice message");
logger.log("info", "Info message");
logger.log("debug", "Debug message");
```

```js
const winston = require("winston");

const logger = winston.createLogger({
  levels: winston.config.syslog.levels,
  level: "debug",
  transports: [new winston.transports.Console()],
});

logger.emerg("Emergency message");
logger.alert("Alert message");
logger.crit("Critical message");
logger.error("Error message");
logger.warning("Warning message");
logger.notice("Notice message");
logger.info("Info message");
logger.debug("Debug message");
```

It's recommended to use several logging methods and levels on the same application. The reason is that we might be interested in important messages in the production environment, but might like to track a lot more while we are coding in a development environment. The accepted best practice for setting a log level is to use an environmental variable. This is done to avoid modifying the application code when the log level needs to be changed.

```js
const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  format: winston.format.json(),
  transports: [new winston.transports.Console()],
});
```

## Format

Formats in winston can be accessed from `winston.format`. They are implemented in `logform`, a separate module from `winston`.

### `winston.format.cli()`

Winston output is in the JSON format by default. By default, the output has predefined fields `level` and `message`. But we can customize logged messages and format. For example, if you're creating a CLI application, you may want to switch this to the `cli` format which will print a color coded output:

```js
const winston = require("winston");

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  format: winston.format.cli(),
  transports: [new winston.transports.Console()],
});

logger.info("Info message");
logger.error("Error message");
```

### `winston.format.combine()` and `winston.format.timestamp()`

The `combine()` method merges multiple formats into one.

You can modify an existing log and add new properties. Here's an example that adds a `timestamp` field to the each log entry. With this you'll see a datetime value formatted as `new Date().toISOString()`:

```js
const winston = require("winston");
const { combine, timestamp, json } = winston.format;

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  format: combine(timestamp(), json()),
  transports: [new winston.transports.Console()],
});

logger.info("Info message");
logger.error("Error message");
```

The `timestamp()` method outputs a datetime value that corresponds to the time that the message was emitted. You can change the format of this datetime value by passing an object to `timestamp()` as shown below. The string value of the format property must be one acceptable by the [fecha](https://github.com/taylorhakes/fecha?tab=readme-ov-file#formatting) module.

```js
const winston = require("winston");
const { combine, timestamp, json } = winston.format;

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  format: combine(
    timestamp({
      format: "YYYY-MM-DD hh:mm:ss.SSS A", // 2022-01-25 03:23:10.350 PM
    }),
    json()
  ),
  transports: [new winston.transports.Console()],
});

logger.info("Info message");
logger.error("Error message");
logger.warn("Warning message");
```

### `winston.format.align()`

The `align()` method aligns the log messages.

```js
const winston = require("winston");
const { combine, timestamp, align, cli } = winston.format;

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  format: combine(align(), timestamp(), cli()),
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: "logs/combined.log" }),
  ],
});

logger.info("Info message");
logger.error("Error message");
logger.warn("Warning message");
```

### `winston.format.printf()`

`printf()` defines a custom structure for the message:

```js
const winston = require("winston");
const { combine, timestamp, printf } = winston.format;

const myFormat = printf(({ level, message, timestamp }) => {
  return `${level}:   ${message},   ${timestamp}`;
});

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  format: combine(timestamp(), myFormat),
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: "logs/combined.log" }),
  ],
});

logger.info("Info message");
logger.error("Error message");
logger.warn("Warning message");
```

### `winston.format.prettyPrint()`

The `prettyPrint()` method outputs logs in a pre-formatted prettier way. The output becomes more readable:

```js
const winston = require("winston");
const { combine, timestamp, label, printf, prettyPrint } = winston.format;

const myFormat = printf(({ level, message, label, timestamp }) => {
  return `[${label}]  ${level}:   ${message},   ${timestamp}`;
});

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  format: combine(
    label({ label: "logging" }),
    timestamp(),
    myFormat,
    prettyPrint()
  ),
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: "logs/combined.log" }),
  ],
});

logger.info("Info message");
logger.error("Error message");
logger.warn("Warning message");
```

### `winston.format.label()`

`label()` method helps to add custom label associated with each message.

```js
const winston = require("winston");
const { combine, timestamp, label, printf } = winston.format;

const myFormat = printf(({ level, message, label, timestamp }) => {
  return `[${label}]  ${level}:   ${message},   ${timestamp}`;
});

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  format: combine(label({ label: "logging" }), timestamp(), myFormat),
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: "logs/combined.log" }),
  ],
});

logger.info("Info message");
logger.error("Error message");
logger.warn("Warning message");

logger.log({
  level: "info",
  message: "What time is the testing at?",
});
```

### `winston.format.simple()`

`simple()` method outputs the log messages in a simple format:

```js
const winston = require("winston");
const { combine, simple } = winston.format;

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  format: combine(simple()),
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: "logs/combined.log" }),
  ],
});

logger.info("Info message");
logger.error("Error message");
logger.warn("Warning message");
```

### `winston.format.colorize()`

`colorize()` method outputs each level of the log messages in a different color. It needs to come before other format methods:

```js
const winston = require("winston");
const { combine, simple, colorize } = winston.format;

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  format: combine(colorize(), simple()),
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: "logs/combined.log" }),
  ],
});

logger.info("Info message");
logger.error("Error message");
logger.warn("Warning message");
```

The `colorize()` method accepts an optional `{all: true}` object, which makes the whole log message in a specific color and not just the level part of output:

```js
const winston = require("winston");
const { combine, simple, colorize } = winston.format;

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  format: combine(colorize({ all: true }), simple()),
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: "logs/combined.log" }),
  ],
});

logger.info("Info message");
logger.error("Error message");
logger.warn("Warning message");
```

### `winston.format.splat()`

The `log` method provides the string interpolation using `util.format`. It must be enabled using `splat()` method:

```js
const winston = require("winston");
const { combine, simple, colorize, splat } = winston.format;

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  format: combine(colorize({ all: true }), splat(), simple()),
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: "logs/combined.log" }),
  ],
});

// info: Info message my string
logger.info("Info message %s", "my string");

// error: Error message 123
logger.error("Error message %d", 123);

// warn: Warning message first second {number: 123}
logger.warn("Warning message %s %s", "first", "second", { number: 123 });
```

### Not logging specific messages

If you wish to filter out a given `info` Object completely when logging then simply return a falsey value.

```js
const winston = require("winston");
const { combine, json } = winston.format;

// Ignore log messages if they have { private: true }
const ignorePrivate = winston.format((info, opts) => {
  if (info.private) {
    return false;
  }
  return info;
});

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  format: combine(ignorePrivate(), json()),
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: "logs/combined.log" }),
  ],
});

logger.info("Info message");
logger.error("Error message");
logger.warn("Warning message");

// Messages with { private: true } will not be written when logged.
logger.log({
  private: true,
  level: "error",
  message: "This is not logged",
});
```

### Changing the log level dynamically

You may also change the log levels of a transport after initialization.

```js
const winston = require("winston");
const { combine, json } = winston.format;

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  format: combine(json()),
  transports: [
    new winston.transports.Console({ level: "warn" }),
    new winston.transports.File({
      filename: "logs/combined.log",
      level: "error",
    }),
  ],
});

// This is not logged
logger.info("Info message");

// These are logged
logger.error("Error message");
logger.warn("Warning message");

// change the log level for the console and file transports
logger.transports[0].level = "info";
logger.transports[1].level = "info";

// Now all are logged
logger.info("Info message");
logger.error("Error message");
logger.warn("Warning message");
```

## Transports

Transport in Winston refers to the location where our log entries are sent to. Winston gives us a number of options for where we want our log messages to be sent.

Here are the built-in transport options in Winston:

- `Console` - output logs to the Node.js console
- `File` - store log messages to one or more files.
- `Http` - stream logs to an HTTP endpoint.
- `Stream` - output logs to any Node.js stream.

```js
const winston = require("winston");
const { combine, timestamp, json } = winston.format;

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  format: combine(timestamp(), json()),
  transports: [
    new winston.transports.File({
      filename: "logs/combined.log",
    }),
  ],
});

logger.info("Info message");
logger.error("Error message");
logger.warn("Warning message");
```

### Adding different levels to each transport

Winston allows us to use multiple transports. It is common for applications to send the same log output to multiple locations. Also, even if we indicate one level in the `.createLogger()` method, we can indicate another level in a transport itself:

```js
const winston = require("winston");
const { combine, timestamp, json } = winston.format;

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  format: combine(timestamp(), json()),
  transports: [
    new winston.transports.File({
      filename: "logs/combined.log",
    }),
    new winston.transports.File({
      filename: "logs/app-error.log",
      level: "error",
    }),
  ],
});

logger.info("Info message");
logger.error("Error message");
logger.warn("Warning message");
```

### Saving in a different file based on the level

A common need that Winston does not enable by default is the ability to log each level into different files so that only "info" messages go to an app-info.log file, "debug" messages into an app-debug.log file, and so on. To get around this, use a custom format on the transport to filter the messages by level. This is possible on any transport (not just `File`), since they all inherit from `winston-transport`.

```js
const winston = require("winston");
const { combine, timestamp, json } = winston.format;

const errorFilter = winston.format((info, opts) => {
  return info.level === "error" ? info : false;
});

const infoFilter = winston.format((info, opts) => {
  return info.level === "info" ? info : false;
});

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  format: combine(timestamp(), json()),
  transports: [
    new winston.transports.File({
      filename: "logs/combined.log",
    }),
    new winston.transports.File({
      filename: "logs/app-error.log",
      level: "error",
      format: combine(errorFilter(), timestamp(), json()),
    }),
    new winston.transports.File({
      filename: "logs/app-info.log",
      level: "info",
      format: combine(infoFilter(), timestamp(), json()),
    }),
  ],
});

logger.info("Info message");
logger.error("Error message");
logger.warn("Warning message");
```

The above code logs only "error" messages in the app-error.log file and "info" messages to the app-info.log file. What happens is that the custom functions (`infoFilter()` and `errorFilter()`) checks the level of a log entry and returns `false`, if it doesn't match the specified level which causes the entry to be omitted from the transport.

### Adding or removing transports after initialization

You can add or remove transports from the logger once it has been provided to you from `.createLogger()`:

```js
const winston = require("winston");
const { combine, timestamp, json } = winston.format;

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  format: combine(timestamp(), json()),
  transports: [
    new winston.transports.File({
      filename: "logs/combined.log",
    }),
  ],
});

const files = new winston.transports.File({
  filename: "logs/new-combined.log",
});
const console = new winston.transports.Console();

logger
  .clear() // Remove all transports
  .add(console) // Add console transport
  .add(files) // Add file transport
  .remove(console); // Remove console transport

logger.info("Info message");
logger.error("Error message");
logger.warn("Warning message");
```

## Custom transports in Winston

Winston supports the ability to create your own transports or utilize one made by the community . A custom transport may be used to store your logs in a database, log management tool, or some other location. For example, you might want to check out `winston-mongodb`.

## Adding metadata to your logs

You can add default metadata to all log entries, or specific metadata to individual logs. Let's start with the former which can be added to a logger instance through the `defaultMeta` property. When you log any message, the contents of the `defaultMeta` object will be injected into each entry:

```js
const winston = require("winston");
const { json } = winston.format;

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  defaultMeta: {
    service: "admin-service",
  },
  transports: [new winston.transports.Console()],
});

logger.info("info message");
logger.warn("warn message");
logger.error("error message");
```

Another way to add metadata to your logs is by creating a child logger through the `child` method. This is useful if you want to add certain metadata that should be added to all log entries in a certain scope. For example, if you add a `productId` to your logs entries, you can search your logs and find the all the entries that pertain to a specific product.

```js
const winston = require("winston");
const { json } = winston.format;

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  transports: [new winston.transports.Console()],
});

logger.info("info message"); // {"level":"info","message":"info message"}

const childLogger = logger.child({
  productId: "1234567",
});

childLogger.info("child info message"); // {"level":"info","message":"child info message","productId":"1234567"}
```

A third way to add metadata to your logs is to pass an object to the level method at its call site:

```js
const winston = require("winston");

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  transports: [new winston.transports.Console()],
});

logger.info("info message"); // {"level":"info","message":"info message"}
logger.warn("warn message");
logger.error("error message");

const childLogger = logger.child({
  productId: "1234567",
});

childLogger.info("child info message", {
  userId: "7654321",
}); // {"level":"info","message":"child info message","productId":"1234567","userId":"7654321"}
```

## Dealing with errors and promise rejections

### Logging errors in Winston

Logging an instance of the `Error` object results in an empty message:

```js
const winston = require("winston");
const { combine, timestamp, json } = winston.format;

const logger = winston.createLogger({
  level: "info",
  format: combine(timestamp(), json()),
  transports: [new winston.transports.Console()],
});

logger.error(new Error("an error")); // {"level":"error","timestamp":"2022-07-03T19:58:26.516Z"}
```

Notice how the `message` property is omitted, and other properties of the `Error` (like its `name` and `stack`) are also not included in the output.

You can fix this issue by importing and specifying the `errors` format as shown below:

```js
const winston = require("winston");
const { combine, timestamp, json, errors } = winston.format;

const logger = winston.createLogger({
  level: "info",
  format: combine(errors({ stack: true }), timestamp(), json()),
  transports: [new winston.transports.Console()],
});

logger.error(new Error("an error"));
```

### Handling uncaught exceptions and uncaught promise rejections

Winston provides the ability to automatically catch and log uncaught exceptions and uncaught promise rejections on a logger. You'll need to specify the transport where these events should be emitted to through the `exceptionHandlers` and `rejectionHandlers` properties respectively:

```js
const winston = require("winston");
const { prettyPrint } = winston.format;

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  transports: [new winston.transports.Console()],
  format: prettyPrint(),
  exceptionHandlers: [
    new winston.transports.File({ filename: "logs/exception.log" }),
  ],
  rejectionHandlers: [
    new winston.transports.File({ filename: "logs/rejections.log" }),
  ],
});

throw new Error("An uncaught error");
```

The logger above is configured to log uncaught exceptions to the "logs/exception.log" file, while uncaught promise rejections are placed in the "logs/rejections.log" file.

Winston will also cause the process to exit with a non-zero status code once it logs an uncaught exception (in other words, app crashes). You can change this by setting the `exitOnError` property on the logger to `false` as shown below (this way the app doesn't crash):

```js
const winston = require("winston");
const { prettyPrint } = winston.format;

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  transports: [new winston.transports.Console()],
  format: prettyPrint(),
  exceptionHandlers: [
    new winston.transports.File({ filename: "logs/exception.log" }),
  ],
  rejectionHandlers: [
    new winston.transports.File({ filename: "logs/rejections.log" }),
  ],
  exitOnError: false,
});

throw new Error("An uncaught error");
```

Note that the accepted best practice is to exit immediately after an uncaught error is detected as the program will be in an undefined state, so the above configuration is not recommended. Instead, you should let your program crash and set up a Node.js process manager (such as `PM2`) to restart it immediately while setting up some alerting mechanism to notify you of the problem

### Handling the errors coming from the logger itself

It is also worth mentioning that the logger also emits an `error` event if an error occurs within the logger itself which you should handle or suppress if you don't want unhandled exceptions:

```js
const winston = require("winston");
const { combine, timestamp, json } = winston.format;

const logger = winston.createLogger({
  level: "info",
  format: combine(timestamp(), json()),
  transports: [new winston.transports.Console()],
});

// Handle errors originating in the logger itself
logger.on("error", function (err) {
  console.log("error in the winston logger");
});
```

## Profiling your Node.js code with Winston

Winston also provides basic profiling capabilities on any logger through the `profile()` method. You can use it to collect some basic performance data in your application hotspots in the absence of specialized tools.

```js
const winston = require("winston");

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  transports: [new winston.transports.Console()],
});

// start a timer
logger.profile("test");

setTimeout(() => {
  // do something

  // End the timer and log the duration
  logger.profile("test");
}, 1000);
// {"durationMs":1001,"level":"info","message":"test"}
```

You can also use the `startTimer()` method on a logger instance to create a new timer and store a reference to it in a variable. Then use the `done()` method on the timer to halt it and log the duration:

```js
const winston = require("winston");

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  transports: [new winston.transports.Console()],
});

// start a timer
const profiler = logger.startTimer();

setTimeout(() => {
  // do something

  // End the timer and log the duration
  profiler.done({ message: "Logging message" });
}, 1000);
// {"durationMs":1001,"level":"info","message":"Logging message"}
```

The `durationMs` property contains the timers' duration in milliseconds.

## Log Rotation

Over time log messages become large and bulky to manage. To solve these issues logs can be rotated based on size limit, and date. Log rotation removes old logs based on count or elapsed day. Winston provides the `winston-daily-rotate-file` module for file rotation. It should be installed separately.

Once installed, it may be used to replace the default `File` transport as shown below:

```js
const winston = require("winston");
require("winston-daily-rotate-file");

const { combine, timestamp, json } = winston.format;

const fileRotateTransport = new winston.transports.DailyRotateFile({
  filename: "logs/combined-%DATE%.log",
  datePattern: "YYYY-MM-DD",
  maxFiles: "14d",
});

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  format: combine(timestamp(), json()),
  transports: [fileRotateTransport],
});

logger.info("Info message");
logger.error("Error message");
logger.warn("Warning message");
```

The `datePattern` property controls how often the file should be rotated (every day), and the `maxFiles` property ensures that log files that are older than 14 days are automatically deleted.

### Events on log rotation

You can also listen for the following events on a rotating file transport if you want to perform some action on cue:

```js
// fired when a log file is created
fileRotateTransport.on("new", (filename) => {});

// fired when a log file is rotated
fileRotateTransport.on("rotate", (oldFilename, newFilename) => {});

// fired when a log file is archived
fileRotateTransport.on("archive", (zipFilename) => {});

// fired when a log file is deleted
fileRotateTransport.on("logRemoved", (removedFilename) => {});
```

## Working with multiple loggers in Winston

A large application will often have multiple loggers with different settings for logging in different areas of the application. This is exposed in Winston through `winston.loggers`. To add a logger, we use `winston.loggers.add()` method. To use the logger to log a message, we use the `winston.loggers.get()` method:

```js
// loggers.js
const winston = require("winston");
const { logstash, json } = winston.format;

winston.loggers.add("serviceALogger", {
  level: process.env.LOG_LEVEL || "info",
  defaultMeta: {
    service: "service-a",
  },
  format: logstash(),
  transports: [new winston.transports.File({ filename: "logs/service-a.log" })],
});

winston.loggers.add("serviceBLogger", {
  level: process.env.LOG_LEVEL || "info",
  defaultMeta: {
    service: "service-b",
  },
  format: json(),
  transports: [new winston.transports.Console()],
});
```

```js
require("./loggers.js");
const winston = require("winston");

const serviceALogger = winston.loggers.get("serviceALogger");
const serviceBLogger = winston.loggers.get("serviceBLogger");

serviceALogger.error("logging to a file");
serviceBLogger.warn("logging to the console");
```
