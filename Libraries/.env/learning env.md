# Learning .env

- [Learning .env](#learning-env)
  - [Environment Variables](#environment-variables)
  - [Setting Environment Variables](#setting-environment-variables)
  - [Installing and Configuring the `dotenv` package](#installing-and-configuring-the-dotenv-package)
  - [An Example of Using `dotenv`](#an-example-of-using-dotenv)

<hr>

## Environment Variables

You can access environment variables in Node.js right out of the box. When your Node.js process boots up, it’ll automatically provide access to all existing environment variables by creating an `env` object within the global `process` object.

```js
console.log(process.env);
```

This code should output all environment variables that this Node.js process can pick up. To access one specific variable, access it like you would any property of an object:

```js
console.log('The value of PORT is:', process.env.PORT);
```

Here, you’ll notice that the value of `PORT` is undefined on your computer. Cloud hosts like Heroku or Azure, however, use the `PORT` variable to tell you which port your server should listen to for the routing to work properly. So the next time you set up a web server, you can determine the port to listen to by checking the `PORT` first and giving it a default value after:

```js
const app = require('http').createServer((req, res) => res.send('Ahoy!'));
const PORT = process.env.PORT || 3000;

app.listen(PORT, () => {
  console.log(`Server is listening on port ${PORT}`);
});
```

If you run the above file, the output should be a message saying, "Server is listening on port 3000." Stop the server with Ctrl+C and restart it with the following command:

```js
PORT=9999 node server.js
```

The message should now say, “Server is listening on port 9999.” This means the `PORT` variable has been temporarily set to `PORT=9999`.

Since `process.env` is a normal object, we can set/override the values:

<hr>

## Setting Environment Variables

```js
process.env.MY_VARIABLE = 'ahoy';
```

The code above will set or override the value of `MY_VARIABLE`. However, keep in mind that this value is only set during the execution of this NodeJS process and only available in child processes spawned by this process. Overall, you should avoid overriding environment variables as much as possible and just initialize a configuration variable, as shown in the PORT example.

<hr>

## Installing and Configuring the `dotenv` package

If you develop multiple NodeJS projects on one computer, you might have overlapping environment variable names. For example, different messaging apps might need different Twilio Messaging Service SIDs, but both would be called TWILIO_MESSAGE_SERVICE_SID. A great way to achieve project-specific configuration is by using **.env** files. These files allow you to specify various environment variables and associated values.

How do we load the values from this file? The quickest way is by using an npm module called `dotenv`. Install the module via npm:

```js
npm install dotenv
```

Afterward, add the following line to the top of your entry file:

```js
require('dotenv').config();
```

The `dotenv.config()` function from the `dotenv` npm package will read the **.env** file, assign the variables to `process.env`, and return an object (named `parsed`) containing the content. It will also throw an error if it fails.

<hr>

## An Example of Using `dotenv`

This code will automatically load the **.env** file in the root of your project and initialize the values, skipping any variables already preset. Be careful not to use **.env** files in your production environment, though. Instead, set the values directly on the respective host. So you might want to wrap your load statement in an if statement:

```js
if (process.env.NODE_ENV !== 'production') {
  require('dotenv').config();
}
```

With this code, we’ll only load the **.env** file if the server isn’t already in the production mode.

Let’s see this in action. Install `dotenv` in a directory. Then, create a `dotenv-example.js` file in the same directory and place the following lines into it:

```js
console.log('No value for FOO yet:', process.env.FOO);

if (process.env.NODE_ENV !== 'production') {
  require('dotenv').config();
}

console.log('Now the value for FOO is:', process.env.FOO);
```

Afterward, create a file called `.env` in the same directory with this content:

```
FOO=bar
```

Run the `dotenv-example.js` file:

```
node dotenv-example.js
```
The output should look like:

```
No value for FOO yet: undefined
Now the value for FOO is: bar
```

As you can see, the value was loaded and defined using `dotenv`. If you rerun the same command with `NODE_ENV` set to production, you’ll see that it’ll stay `undefined`. Here is how you can do that:

```
NODE_ENV=production node dotenv-example.js
```

<hr>

