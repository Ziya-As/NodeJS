# Helmet

- [Helmet](#helmet)
  - [Some Links](#some-links)
  - [Introduction](#introduction)
  - [`X-Powered-By`](#x-powered-by)
  - [Using `helmet`](#using-helmet)
  - [The `Content-Security-Policy` header](#the-content-security-policy-header)
  - [The `Referrer-Policy` header](#the-referrer-policy-header)
  - [The `Strict-Transport-Security` header](#the-strict-transport-security-header)
  - [`X-Content-Type-Options`](#x-content-type-options)
  - [`X-Frame-Options`](#x-frame-options)

<hr>

## Some Links

- A useful website to check website headers for public addresses: https://securityheaders.com/

<hr>

## Introduction

Helmet.js is middleware-based technology that improves security by safeguarding HTTP headers returned by a NodeJS app.

It helps to protect NodeJS Express apps from common security threats such as Cross-Site Scripting (XSS) and click-jacking attacks.

Without Helmet, default headers returned by Express expose sensitive information and make your NodeJS app vulnerable to malicious actors. In contrast, using Helmet in NodeJS protects your application from XSS attacks, Content Security Policy vulnerabilities, and other security issues.

<hr>

## `X-Powered-By`

One of the headers in an HTTP response is `X-Powered-By`. In a standard Express application that doesn't use Helmet, this is how it looks:

```http
X-Powered-By: Express
```

As recommended by OWASP, a nonprofit foundation that works to improve web security, **`X-Powered-By` should be omitted**. This is because you should never give attackers details about your tech stack. Otherwise, they could use that info to exploit known vulnerabilities in that framework or library.

<hr>

## Using `helmet`

Integrating Helmet into your NodeJS Express app is simple. In your index.js file, import helmet with the following command:

```js
const helmet = require("helmet");
```

Now, register helmet in your Express application with the below code:

```js
app.use(helmet());
```

Remember that `helmet()` is nothing more than an Express middleware. Specifically, the top-level `helmet()` function is a wrapper of 15 sub-middlewares. So, by registering `helmet()`, you are adding 15 Express middlewares to your apps.

Note that each middleware takes care of setting one HTTP security header.

The response HTTP header section will now include the following:

```http
Content-Security-Policy: default-src 'self';base-uri 'self';font-src 'self' https: data:;form-action 'self';frame-ancestors 'self';img-src 'self' data:;object-src 'none';script-src 'self';script-src-attr 'none';style-src 'self' https: 'unsafe-inline';upgrade-insecure-requests
Cross-Origin-Embedder-Policy: require-corp
Cross-Origin-Opener-Policy: same-origin
Cross-Origin-Resource-Policy: same-origin
X-DNS-Prefetch-Control: off
X-Frame-Options: SAMEORIGIN
Strict-Transport-Security: max-age=15552000; includeSubDomains
X-Download-Options: noopen
X-Content-Type-Options: nosniff
Origin-Agent-Cluster: ?1
X-Permitted-Cross-Domain-Policies: none
Referrer-Policy: no-referrer
X-XSS-Protection: 0
Content-Type: application/json; charset=utf-8
Content-Length: 15
ETag: W/"f-pob1Yw/KBE+3vrbZz9GAyq5P2gE"
Date: Fri, 20 Jan 2023 18:15:32 GMT
Connection: keep-alive
Keep-Alive: timeout=5
```

As you can see, there are many new HTTP headers in the response. Also, `X-Powered-By` was removed.

The middleware that hides the `X-Powered-By` is `hidePoweredBy()`. You don't have to give any arguments to this method, but you can also explicitly set the header to something else, to throw people off:

```js
app.use(helmet.hidePoweredBy({ setTo: 'PHP 4.2.0`}));
```

<hr>

Note that the API response now involves all the major HTTP Security headers, except for `Permissions-Policy`. Also, as mentioned on GitHub by one of the lead developers of Helmet.js, Helmet does not automatically support `Permissions-Policy` only because the header specification is still in a draft state. So, this may change soon.

<hr>

## The `Content-Security-Policy` header

Content Security Policy, also known as CSP, is a security measure that helps you mitigate several attacks, such as cross-site scripting (XSS) and data injection attacks.

Specifically, CSP allows you to specify what sources of content a web page is allowed to load and execute. For example, you can use CSP to block a web page from loading images and iframes from other websites.

You can configure CSP through the `Content-Security-Policy` HTTP header. By default, Helmet gives the `Content-Security-Policy` header the following value:

```http
Content-Security-Policy: default-src 'self';base-uri 'self';font-src 'self' https: data:;form-action 'self';frame-ancestors 'self';img-src 'self' data:;object-src 'none';script-src 'self';script-src-attr 'none';style-src 'self' https: 'unsafe-inline';upgrade-insecure-requests
```

With this policy, your web pages cannot load remote fonts or styles. This is because `font-src` and `style-src` are set to `self`, respectively.

The CSP policy defined by Helmet by default is very restrictive, but you can change it with `contentSecurityPolicy()` as follows:

```js
// overriding "font-src" and "style-src" while
// maintaining the other default values
helmet.contentSecurityPolicy({
  useDefaults: true,
  directives: {
    "font-src": ["'self'", "external-website.com"],
    // allowing styles from any website
    "style-src": null,
  },
});
```

`useDefaults` applies the default values. Then, the following directives override the defaults. Set `useDefaults` to `false` to define a CSP policy from scratch.

<hr>

## The `Referrer-Policy` header

The `Referrer-Policy` HTTP header defines what data should be sent as referrer information in the Referer header. By default, Referer generally contains the current URL from which an HTTP request is performed.

For example, if you click on a link to a third-party website, Referer will contain the address of the current web page. As a result, the third-party website could use the header to track you or understand what you were visiting.

If the current address contains private user information, the third-party site will be able to steal it from the Referer header. Because of this, even though this header is typically used for caching or analytics, it opens up some privacy concerns because it can leak sensitive information.

Helmet sets it to `no-referrer` by default. This means that the Referer header will always be empty. So, requests performed by web pages served by your NodeJS app will not include any referrer information.

If you want to change this restrictive policy, you can do it with `refererPolicy` as below:

```js
// setting "Referrer-Policy" to "no-referrer"
app.use(
  helmet.referrerPolicy({
    policy: "no-referrer",
  })
);
```

<hr>

## The `Strict-Transport-Security` header

The `Strict-Transport-Security` HTTP header, also known as HSTS, specifies that a site or resource should only be accessed via HTTPS. In detail, the `maxAge` parameter defines the number of seconds browsers should remember to prefer HTTPS over HTTP.

By default, Helmet sets the `Strict-Transport-Security` header as follows:

```
max-age=15552000; includeSubDomains
```

Note that 15552000 seconds corresponds to 180 days, as well as that `includeSubDomains` extends the HSTS policy to all the site’s subdomains.

You can configure the `Strict-Transport-Security` header with the `hsts` Helmet function as follows:

```js
app.use(
  helmet.hsts({
    // 60 days
    maxAge: 86400,
    // removing the "includeSubDomains" option
    includeSubDomains: false,
  })
);
```

<hr>

## `X-Content-Type-Options`

The `X-Content-Type-Options` HTTP header defines that the MIME types used in the Content-Type header must be followed. This mitigates MIME type sniffing, which can lead to XSS attacks and cause other vulnerabilities.

For example, attackers could hide HTML code in a .png file. The browser might perform MIME type sniffing to determine the content type of the resource.

Since the file contains HTML code, the browser will determine that it is an HTML file rather than a JPG image. Thus, the browser will execute the attacker’s code accordingly when rendering the page.

By default, Helmet sets `X-Content-Type-Options` to `nosniff`. This disables and prevents MIME type sniffing.

Note that the `noSniff()` Helmet function contained in `helmet()` does not accept parameters. If you want to disable this behavior and go against recommended security policies, you can prevent Helmet from importing `nosniff()` with the following:

```js
app.use(
  // not loading the noSniff() middleware
  helmet({
    noSniff: false,
  })
);
```

<hr>

## `X-Frame-Options`

The `X-Frame-Options` HTTP response header specifies whether or not a browser should be allowed to render a page in the `<frame>`, `<iframe>`, `<embed>` and `<object>` HTML elements.

By preventing the content of your site from being embedded in other sites, you can avoid click-jacking attacks. A click-jacking attack involves tricking users into clicking on something different from what they perceive or expect.

By default, Helmet sets `X-Frame-Options` to `SAMEORIGIN`. This allows a web page to be embedded in a frame on pages with the same origin as the page itself. You can set this header in Helmet with `frameguard()` as follows:

```js
// setting "X-Frame-Options" to "DENY"
app.use(
  helmet.frameguard({
    action: "deny",
  })
);
```

If you want to omit the `X-Frame-Options` header entirely, you can disable the `frameguard()` middleware with the following:

```js
app.use(
  // not including the frameguard() middleware
  helmet({
    frameguard: false,
  })
);
```

Note that this is not recommended for security reasons.

<hr>
