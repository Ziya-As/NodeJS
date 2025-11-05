# Compression

- [Compression](#compression)
  - [Useful Links](#useful-links)
  - [Intro](#intro)
  - [Installation and a Simple Example](#installation-and-a-simple-example)
  - [Options for `compression()` middleware](#options-for-compression-middleware)

## Useful Links

- https://www.npmjs.com/package/compression

## Intro

Compression in Node.js and Express decreases the downloadable amount of data that’s served to users. Through the use of this compression, we can improve the performance of our Node.js applications as our payload size is reduced drastically.

There are two methods of compression:

- One is calling it directly in your Node.js app using the compression middleware, and
- the other is to use it at a reverse proxy level through software like NGINX.

<hr>

## Installation and a Simple Example

To start using compression in your NodeJS application, you can use the compression middleware in the main file of your NodeJS app. This will enable GZIP, which supports different compression schemes. This will make your JSON response and other static file responses smaller.

First, you’ll need to install the npm package for compression

```
npm i compression
```

Then you can use the module in your application after you initialize your server, like with Express.js:

```js
const express = require("express");
const compression = require("compression");

const app = express();

// compress all responses
app.use(compression());

app.get("/", (req, res) => {
  const animal = "alligator";

  // Send a text/html file back with the word 'alligator' repeated 1000 times
  res.status(200).send(animal.repeat(100000));
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

In the example above, we set a GET response that will send back a text/html file with the word alligator printed 1000 times. Without compression, the response would come back with a size of around 9kb.

If you turn on compression, the response is sent with a header that states `Content-Encoding: gzip`, and instead is around 365B.

<hr>

## Options for `compression()` middleware

The `compression()` function in NodeJS can take several options as an argument. Here's a list of all the available options and their explanation:

- `level` - The level of zlib (zlib is from `zlib = require('zlib')`) compression to apply to responses. A higher level will result in better compression, but will take longer to complete. A lower level will result in less compression, but will be much faster. This is an integer in the range of 0 (no compression) to 9 (maximum compression). The special value -1 can be used to mean the "default compression level", which is a default compromise between speed and compression (currently equivalent to level 6).
- `memLevel` - This specifies how much memory should be allocated for the internal compression state and is an integer in the range of 1 (minimum level) and 9 (maximum level). The default value is zlib.Z_DEFAULT_MEMLEVEL, or 8.
- `strategy` - The compression strategy to use. This can be one of the following values:

  - `zlib.constants.Z_DEFAULT_STRATEGY`,
  - `zlib.constants.Z_FILTERED`,
  - `zlib.constants.Z_HUFFMAN_ONLY`,
  - `zlib.constants.Z_RLE`,
  - `zlib.constants.Z_FIXED`, or
  - `zlib.constants.Z_TREES`.

  The default value is `zlib.constants.Z_DEFAULT_STRATEGY`.

- `chunkSize` - The size of the chunks to use when compressing data. The default value is 16KB.
- `windowBits` - The size of the sliding window to use when compressing data. The default value is zlib.Z_DEFAULT_WINDOWBITS, or 15.
- `filter` - A function to decide if the response should be considered for compression. This function is called as `filter(req, res)` and is expected to return `true` to consider the response for compression, or `false` to not compress the response. The default filter function uses the `compressible` module to determine if `res.getHeader('Content-Type')` is compressible.
- `threshold` - The minimum size of the response to compress. Responses smaller than this threshold will not be compressed. The default value is 1024 bytes (1KB).
- `gzip` - A boolean value indicating whether to use gzip compression. The default value is `true`.
- `deflate` - A boolean value indicating whether to use deflate compression. The default value is `true`.
- `brotli` - A boolean value indicating whether to use brotli compression. The default value is `true`.
- `zlib` - A boolean value indicating whether to use zlib compression. The default value is `true`.

Here's an example of using some of these options:

```js
const express = require("express");
const compression = require("compression");
const zlib = require("zlib");

const app = express();

app.use(
  compression({
    level: 6,
    strategy: zlib.constants.Z_FILTERED,
    windowBits: 16,
    filter: (req, res) => {
      // don't compress if there is a header "x-no-compression"
      if (req.headers["x-no-compression"]) {
        return false;
      }

      // return the default filter function if the header is not "x-no-compression"
      return compression.filter(req, res);
    },
    threshold: 2048,
    gzip: false,
    brotli: true,
    zlib: true,
  })
);

// ...
```

In this example, we're using the compression middleware with several options. We're setting the `level` to `6`, `strategy` to `zlib.constants.Z_FILTERED`, `windowBits` to `16`, and defining a custom filter function that helps us to compress only the requests without the header of `"x-no-compression"`. We're also setting the `threshold` to `2048` bytes (2KB), disabling `gzip` compression, and enabling `brotli` and `zlib` compression.
