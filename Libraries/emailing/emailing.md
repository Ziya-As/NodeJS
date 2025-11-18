# Emailing in NodeJS

- [Emailing in NodeJS](#emailing-in-nodejs)
  - [Useful Links](#useful-links)
  - [Intro](#intro)
  - [Example](#example)
  - [Diving deeper into `nodemailer`](#diving-deeper-into-nodemailer)
    - [Transporter object](#transporter-object)
    - [Sending emails with the transporter object](#sending-emails-with-the-transporter-object)
      - [Email Content](#email-content)
      - [Callback](#callback)
    - [OAUTH2](#oauth2)

<hr>

## Useful Links

- https://nodemailer.com/
- https://ethereal.email/
- https://www.youtube.com/watch?v=L46FwfVTRE0

## Intro

`nodemailer` is a module for Node.js applications to allow sending emails easily.

## Example

These are the steps that you need to take to send messages with `nodemailer`:

- Create a transporter using either SMTP or some other transport mechanism
- Set up message options (who sends what to whom)
- Deliver the message object using the `sendMail()` method of your previously created transporter

Here is a basic example:

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 5000;

const nodemailer = require("nodemailer");

// Create a transport
const transporter = nodemailer.createTransport({
  host: "smtp.ethereal.email",
  port: 587,
  secure: false, // Use `true` for port 465, `false` for all other ports
  auth: {
    user: "ronaldo.witting41@ethereal.email",
    pass: "exa5yBMeKCc6H9FUvz",
  },
});

async function sendFakeEmail() {
  // send an email with the defined transport object
  const info = await transporter.sendMail({
    from: '"Test Name" <ronaldo.witting41@ethereal.email>', // sender address
    to: "bar@example.com, baz@example.com", // list of receivers
    subject: "Some Subject", // Subject line
    text: "Hello world?", // plain text body
    html: "<b>Hello world?</b>", // html body
  });

  // The sent message has an id that we can refer to
  console.log("Message sent: %s", info.messageId);
}

app.get("/", (req, res) => {
  sendFakeEmail().catch(console.error);
  res.status(200).send("Mail is sent");
});

app.listen(PORT, () => {
  console.log(`Server is running on http://localhost:${PORT}`);
});
```

## Diving deeper into `nodemailer`

### Transporter object

To send emails you need a transporter object. Here is the syntax to specify one

```js
const transporter = nodemailer.createTransport(transport[, defaults])
```

- `transporter` is going to be an object that is able to send and mail
- `transport` is the transport configuration object, connection url or a transport plugin instance
- `defaults` is an optional object that defines default values for mail options. It is an object that is going to be merged into every message object. This allows you to specify shared options, for example to set the same `from` address for every message.

After creating a transporter object once, you can use it to send emails as much as you like.

The transport options accepts various properties that let us configure the transporter object. Here is information about some of them:

- `port` – is the port to connect to (defaults to 587 if is secure is false or 465 if true)
- `host` – is the hostname or IP address to connect to (defaults to ‘localhost’)
- `auth` – is an authentication object. We can indicate various authentication settings using this object:
  - `user` is the username
  - `pass` is the password for the user if normal login is used
  - `type` indicates the authentication type, defaults to ‘login’, other option is ‘oauth2’
- `authMethod` – defines preferred authentication method, defaults to ‘PLAIN’

### Sending emails with the transporter object

Once you have a transporter object you can send mail with it. Here is the syntax to use the `<transporter>.sendMail()` method:

```js
transporter.sendMail(data[, callback])
```

- `data` defines the mail content to be sent.

#### Email Content

The following are the possible fields of an email message:

- `from` - This is email address of the sender. All email addresses can be
  - plain ‘sender@server.com’ or
  - formatted '“Sender Name” sender@server.com'
- `to` - This is email address of the recipient. It accepts
  - a comma separated list or
  - an array
- `cc` - It accepts
  - a comma separated list or
  - an array that will appear on the Cc: field
- `bcc` - It accepts
  - a comma separated list or
  - an array that will appear on the Bcc: field
- `subject` - The subject of the email
- `text` - The plaintext version of the message as an Unicode string, Buffer, Stream or an attachment-like object ({path: ‘/var/data/…'})
- `html` - The HTML version of the message as an Unicode string, Buffer, Stream or an attachment-like object ({path: ‘http://…'})
- `attachments` - An array of attachment objects.

All text fields (email addresses, plaintext body, html body, attachment filenames) use UTF-8 as the encoding. Attachments are streamed as binary.

```js
var message = {
  from: "sender@server.com",
  to: "receiver@sender.com",
  subject: "Message title",
  text: "Plaintext version of the message",
  html: "<p>HTML version of the message</p>",
};
```

Here is the example for the `attachments` property in the `sendMail` method:

```js
const send = await transporter.sendMail({
  // ...
  attachments: [
    {
      // get file content from disk
      filename: "text1.txt",
      path: "/path/to/file1.txt",
    },
    {
      // get file content from a URL
      filename: "text2.txt",
      path: "https://myserver.com/text2.txt",
    },
    {
      // create file from UTF-8 string
      filename: "text3.txt",
      content: "This is the file content!",
    },
    {
      // create file from data URI
      filename: "text4.txt",
      path: "data:text/plain;base64,SGVsbG8gd29ybGQh",
    },
  ],
});
```

There is a lot more to learn about [message configuration](https://nodemailer.com/message/).

#### Callback

- `callback` is an optional callback function to run once the message is delivered or it failed
  - `err` is the error object if message failed
  - `info` includes the result, the exact format depends on the transport mechanism used
  - `info.messageId` most transports should return the final Message-Id value used with this property
  - `info.envelope` includes the envelope object for the message
  - `info.accepted` is an array returned by SMTP transports (includes recipient addresses that were accepted by the server)
  - `info.rejected` is an array returned by SMTP transports (includes recipient addresses that were rejected by the server)
  - `info.pending` is an array returned by Direct SMTP transport. Includes recipient addresses that were temporarily rejected together with the server response
  - response is a string returned by SMTP transports and includes the last SMTP response from the server

If the message includes several recipients then the message is considered sent if at least one recipient is accepted.

### OAUTH2

https://nodemailer.com/smtp/oauth2/
