# Express Validator

- [Express Validator](#express-validator)
  - [Some Useful Links](#some-useful-links)
  - [Intro](#intro)
  - [Validators and Middlewares](#validators-and-middlewares)
    - [`notEmpty()`](#notempty)
    - [`validationResult`](#validationresult)
    - [Checking several inputs](#checking-several-inputs)
    - [Locations of inputs](#locations-of-inputs)
    - [Custom validation error message](#custom-validation-error-message)
      - [`withMessage`](#withmessage)
    - [`matchedData`](#matcheddata)
    - [`checkExact`](#checkexact)
    - [`checkSchema`](#checkschema)
    - [`oneOf`](#oneof)
    - [`exists`](#exists)
    - [`contains`](#contains)
    - [`equals`](#equals)
    - [`isAfter`](#isafter)
    - [`isBefore`](#isbefore)
    - [`isEmail`](#isemail)
    - [`isArray`](#isarray)
    - [`isObject`](#isobject)
    - [`isString`](#isstring)
    - [`isBoolean`](#isboolean)
    - [`isCreditCard`](#iscreditcard)
    - [`isLength`](#islength)
    - [`isNumeric`](#isnumeric)
    - [`isDate`](#isdate)
    - [`isIn`](#isin)
    - [`isJSON`](#isjson)
    - [`isStrongPassword`](#isstrongpassword)
    - [`isWhitelisted`](#iswhitelisted)
    - [`matches`](#matches)
    - [`custom`](#custom)
  - [Sanitizers](#sanitizers)
    - [`trim`](#trim)
    - [`ltrim`](#ltrim)
    - [`rtrim`](#rtrim)
    - [`normalizeEmail`](#normalizeemail)
    - [`default`](#default)
    - [`replace`](#replace)
    - [`toArray`](#toarray)
    - [`blacklist`](#blacklist)
    - [`whitelist`](#whitelist)
    - [`escape`](#escape)
    - [`unescape`](#unescape)
    - [`toBoolean`](#toboolean)
    - [`toDate`](#todate)
    - [`customSanitizer`](#customsanitizer)
  - [Modifiers](#modifiers)
    - [`bail`](#bail)
    - [`if`](#if)
    - [`not`](#not)
    - [`optional`](#optional)
  - [`new ExpressValidator`](#new-expressvalidator)

## Some Useful Links

- https://github.com/validatorjs/validator.js
- https://express-validator.github.io/docs/api/check

<hr>

## Intro

`express-validator` is a set of ExpressJS middlewares that wraps the extensive collection of validators and sanitizers offered by validator.js.

A validator is a function that takes input and performs certain checks based on some criteria.

A sanitizer is a function used to modify or cleanse input data to ensure that it is secure and adheres to required formats.

## Validators and Middlewares

### `notEmpty()`

Here is how to add a simple `notEmpty()` validation. `notEmpty()` adds a validator to check that a value is a string that's not empty. This is analogous to chaining other 2 methods from the library: `.not().isEmpty()`.

To add the validation, we, first, import the `body` middleware from `express-validator`, and then add the `notEmpty` validation to the `username` field, which will be received with the `req.body`.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const { body } = require("express-validator");

app.use(express.json());

app.post("/validate", body("username").notEmpty(), (req, res) => {
  res.status(200).send({ status: "success", username: req.body.username });
});

app.listen(PORT, () => {
  console.log(`Server is running on port: ${PORT}`);
});
```

Now, if we send a request with a non-empty `username` field in the request body, our validation will pass. But what if we receive an empty `username`?! Our NodeJS app won't crash, and our response will still be sent back with the above status code. To know if we have validation errors, we can use the `validationResult`.

### `validationResult`

`validationResult` method helps us to have access to all validation errors. When an input doesn't pass the validation, it becomes part of the `errors` property returned by this method. We can use it to know the outcome of the validation (if `errors` is empty, it means validation test was passed), and decide how we want to respond to errors. Having this function helps to have a list of all errors, and respond either field by field or return one overall response.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const { body, validationResult } = require("express-validator");

app.use(express.json());

app.post("/validate", body("username").notEmpty(), (req, res) => {
  const result = validationResult(req);

  if (!result.isEmpty()) {
    return res.status(400).send({ status: "error", errors: result.array() });
  }

  res.status(200).send({ status: "success", username: req.body.username });
});

app.listen(PORT, () => {
  console.log(`Server is running on port: ${PORT}`);
});
```

Now, we will respond successfully only when the `username` is not empty.

### Checking several inputs

Instead of checking one input, we can provide an array of inputs to the `body` middleware. This way, we will validate more than one input field.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const { body, validationResult } = require("express-validator");

app.use(express.json());

app.post("/validate", body(["username", "role"]).notEmpty(), (req, res) => {
  const result = validationResult(req);

  if (!result.isEmpty()) {
    return res.status(400).send({ status: "error", errors: result.array() });
  }

  res.status(200).send({ status: "success", username: req.body.username });
});

app.listen(PORT, () => {
  console.log(`Server is running on port: ${PORT}`);
});
```

We can also add validations to fields separately.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const { body, validationResult } = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  body("username").notEmpty(),
  body("role").notEmpty(),
  (req, res) => {
    const result = validationResult(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", username: req.body.username });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

Although we added separate validations in the previous code, we can still add those separate validations into an array.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const { body, validationResult } = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  [body("username").notEmpty(), body("role").notEmpty()],
  (req, res) => {
    const result = validationResult(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", username: req.body.username });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### Locations of inputs

Although, we checked for the `username` property in the `body` of the request in our previous examples, `express-validator` is able to check other locations within the request object, as well.  
So, in addition to `body()`, we can also use:

- `cookie()` to check `req.cookies`,
- `header()` to check `req.headers`,
- `param()` to check `req.params`,
- `query()` to check `req.query`.

If we don't want to validate and sanitize a specific location within the request object, we can use the `check()` middleware. The `check()` middleware is the main API used for validating and sanitizing HTTP requests with `express-validator`. It gives you access to all of the built-in validators, sanitizers, and a bunch of other utility methods to shape the validation just the way you need.

### Custom validation error message

When the validation is not passed, the `msg` field, in the `errors`, says "Invalid value". But that's not descriptive enough. We can provide custom message for validation with these methods:

- `check(["fields to check"], "custom error message")`,
- `body(["fields to check"], "custom error message")`,
- `cookie(["fields to check"], "custom error message")`,
- `header(["fields to check"], "custom error message")`,
- `param(["fields to check"], "custom error message")`,
- `query(["fields to check"], "custom error message")`.

Here is an example:

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const { check, validationResult } = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  check(
    ["username", "password"],
    "username and password should not be empty"
  ).notEmpty(),
  (req, res) => {
    const result = validationResult(req);

    if (result.isEmpty()) {
      return res.status(200).send({ message: "Success" });
    }

    res.status(400).send({ errors: result.array() });
  }
);

app.listen(PORT, () => {
  console.log(`Server is running on port: ${PORT}`);
});
```

#### `withMessage`

We can also use the `withMessage` method to set a custom validation message. `withMessage` sets the error message used by the previous validator.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const { body, check, validationResult } = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  check(["username", "password"])
    .notEmpty()
    .withMessage("username and password should not be empty"),
  (req, res) => {
    const result = validationResult(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", username: req.body.username });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `matchedData`

`matchedData` extracts data validated and/or sanitized by express-validator from the request, and returns an object with them.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const { body, validationResult, matchedData } = require("express-validator");

app.use(express.json());

app.post("/validate", body("username").notEmpty(), (req, res) => {
  const result = validationResult(req);
  const data = matchedData(req);

  if (result.isEmpty()) {
    return res.status(200).send({ matchedData: data });
  }

  res.status(400).send({ errors: result.array() });
});

app.listen(PORT, () => {
  console.log(`Server is running on port: ${PORT}`);
});
```

By default, `matchedData` doesn't return data that is optional and wasn't present in the request.
We can use the optional options object, and set the `includeOptionals` property to `true` to change this behavior.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const cookieParser = require("cookie-parser");
const { body, validationResult, matchedData } = require("express-validator");

app.use(express.json());
app.use(cookieParser());

app.post(
  "/validate",
  body("username").notEmpty(),
  body("role").optional(),
  (req, res) => {
    const result = validationResult(req);
    const data = matchedData(req, { includeOptionals: true });

    if (result.isEmpty()) {
      return res.status(200).send({ matchedData: data });
    }

    res.status(400).send({ errors: result.array() });
  }
);

app.listen(PORT, () => {
  console.log(`Server is running on port: ${PORT}`);
});
```

By default, `matchedData` returns only data that is valid; that is, if the request contains invalid data, it's omitted from the result.
You can set `onlyValidData` to `false` in the options object to change this behavior.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const cookieParser = require("cookie-parser");
const { body, validationResult, matchedData } = require("express-validator");

app.use(express.json());

app.post("/validate", body("username").notEmpty(), (req, res) => {
  const result = validationResult(req);
  const data = matchedData(req, { onlyValidData: false });
  console.log(data);

  if (result.isEmpty()) {
    return res.status(200).send({ matchedData: data });
  }

  res.status(400).send({ data, errors: result.array() });
});

app.listen(PORT, () => {
  console.log(`Server is running on port: ${PORT}`);
});
```

By default, `matchedData` selects data validated on all of request locations. You can change this behavior by setting `locations` to a list of the locations which you wish to select data from:

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const cookieParser = require("cookie-parser");
const { body, validationResult, matchedData } = require("express-validator");

app.use(express.json());

app.post("/validate", body("username").notEmpty(), (req, res) => {
  const result = validationResult(req);
  const data = matchedData(req, { locations: ["body", "query"] });

  if (result.isEmpty()) {
    return res.status(200).send({ matchedData: data });
  }

  res.status(400).send({ data, errors: result.array() });
});

app.listen(PORT, () => {
  console.log(`Server is running on port: ${PORT}`);
});
```

### `checkExact`

The `checkExact` middleware checks whether the request contains exactly only fields that have a validation chain associated to them. We need to let `checkExact` know which validation chains should be taken into account. A field is considered known by `checkExact` in 2 ways:

- if there were validation chains specified for that field before `checkExact` ran,
- if that field is part of the validation chains that were provided to `checkExact` as argument.

Therefore, `checkExact` must be the last validation middleware to run in your request, otherwise it'll mark subsequent fields that have a validation chain as unknown ones.

`checkExact` also accepts an optional object, which we can use to set a custom error message.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  validationResult,
  matchedData,
  checkExact,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  body("username").notEmpty(),
  checkExact([body("email").isEmail(), body("password").isLength({ min: 8 })], {
    message: "Too many fields specified",
  }),
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }
    console.log(req.body.username);
    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

In the below example, validation is failed because `checkExact` doesn't know about the "subscribe" input. "subscribe" is not part of the previous validation chains, and not one of the validation chains provided to `checkExact` as an argument.

### `checkSchema`

`checkSchema` creates a list of validation chains based on the provided schema, which can then be passed to an express.js route for validation.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  validationResult,
  matchedData,
  checkSchema,
} = require("express-validator");

app.use(express.json());

const schema = checkSchema({
  email: { isEmail: true },
  password: { isLength: { options: { min: 8 } } },
});

app.post("/validate", schema, (req, res) => {
  const result = validationResult(req);
  const validData = matchedData(req);

  if (!result.isEmpty()) {
    return res.status(400).send({ status: "error", errors: result.array() });
  }
  res.status(200).send({ status: "success", validData });
});

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

With `checkSchema`, we can still use all the validators.

- Instead of `check("email").isEmail()`, we use `{email: { isEmail: true }}`
- Instead of `check("email").notEmpty()`, we use `{email: { notEmpty: true }}`

If we want to use options for a validator, then instead of setting the validator to `true` or `false`, we set the validator to an object.

- Instead of `check("password").isLength({ min: 8 })`, we use `{password: { isLength: { min: 8 } }}`
- Instead of `check("password").isStrongPassword()`, we use `{password: { isStrongPassword: { minLength: 8, minLowercase: 1 } }}`

If the validator accepts an array as an argument, then we need to wrap that array in another array, while using `checkSchema`.

- Instead of `check("role").isIn(["admin", "user", "guest"])`, we use `{ role: { isIn: [[ "admin", "user", "guest" ]] } }`

By default, all specified fields are validated in all request locations (all of body, cookies, headers, params and query).This list can be changed by specifying the default locations.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  validationResult,
  matchedData,
  checkSchema,
} = require("express-validator");

app.use(express.json());

const schema = checkSchema(
  {
    email: { isEmail: true },
    password: { isLength: { options: { min: 8 } } },
  },
  ["body", "query"]
);

app.post("/validate", schema, (req, res) => {
  const result = validationResult(req);
  const validData = matchedData(req);

  if (!result.isEmpty()) {
    return res.status(400).send({ status: "error", errors: result.array() });
  }
  res.status(200).send({ status: "success", validData });
});

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

`checkSchema` returns a middleware, which makes it ideal to pass to an express.js route. But you can also run it manually, if you wish.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  validationResult,
  matchedData,
  checkSchema,
} = require("express-validator");

app.use(express.json());

app.post("/validate", async (req, res) => {
  await checkSchema(
    {
      email: { isEmail: true },
      password: { isLength: { options: { min: 8 } } },
    },
    ["body", "query"]
  ).run(req);

  const result = validationResult(req);
  const validData = matchedData(req);

  if (!result.isEmpty()) {
    return res.status(400).send({ status: "error", errors: result.array() });
  }
  res.status(200).send({ status: "success", validData });
});

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `oneOf`

Creates a middleware to ensure that at least one of the given validation chain groups are valid.

If none of the validation chain groups are valid, a validation error is added to the request.

It also accepts an options object, which we can use to set a custom error message.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  validationResult,
  matchedData,
  oneOf,
  body,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  oneOf([body("phone_number").isMobilePhone(), body("email").isEmail()], {
    message: "At least one valid contact method must be provided",
  }),
  async (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }
    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

It's also possible to have groups of validation chains be validated as a unit. This is helpful if you have a bunch of fields that work together be a single alternative.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  validationResult,
  matchedData,
  oneOf,
  body,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  oneOf(
    [
      body("paypal").isEmail(),
      [
        body("credit_card.number").isCreditCard(),
        body("credit_card.expiry").isDate(),
        body("credit_card.cvv").isNumeric(),
      ],
    ],
    { message: "At least one valid refund method must be provided" }
  ),
  async (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }
    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `exists`

`exists` method adds a validator to check if the field exists.

Which values are considered existent depends on the `values` property in the options object.

- By default, it's set to `undefined`.
- It could also be set to `null`, in which case both `undefined` and `null` values will be considered inexistent.
- It could be set to `falsy`, in which case **empty strings**, **0**, `false`, `null` and `undefined` values will be considered inexistent.

Using `exists` is only necessary if you aren't adding any other validator or sanitizer.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post("/validate", check(["username", "password"]).exists(), (req, res) => {
  const result = validationResult(req);
  const validData = matchedData(req);

  if (!result.isEmpty()) {
    return res.status(400).send({ status: "error", errors: result.array() });
  }

  res.status(200).send({ status: "success", validData });
});

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `contains`

`contains` method checks if the string input contains a certain value.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const { body, validationResult } = require("express-validator");

app.use(express.json());

app.post("/validate", body("userType").contains("superUser"), (req, res) => {
  const result = validationResult(req);
  if (result.isEmpty()) {
    return res.status(200).send(`Welcome, ${req.body.userType}`);
  }

  res.status(400).send({ errors: result.array() });
});

app.listen(PORT, () => {
  console.log(`Server is running on port: ${PORT}`);
});
```

`contains` method also accepts an optional options object. We can use the `ignoreCase` property in the options object to perform a case-insensitive check.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const { body, validationResult } = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  body("userType").contains("superUser", { ignoreCase: true }),
  (req, res) => {
    const result = validationResult(req);
    if (result.isEmpty()) {
      return res.status(200).send(`Welcome, ${req.body.userType}`);
    }

    res.status(400).send({ errors: result.array() });
  }
);

app.listen(PORT, () => {
  console.log(`Server is running on port: ${PORT}`);
});
```

### `equals`

The `equals` method in `express-validator` is used to check if the input string equals a specified comparison string.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const { body, validationResult } = require("express-validator");

app.use(express.json());

app.post("/validate", body("userType").equals("superUser"), (req, res) => {
  const result = validationResult(req);
  if (result.isEmpty()) {
    return res.status(200).send(`Welcome, ${req.body.userType}`);
  }

  res.status(400).send({ errors: result.array() });
});

app.listen(PORT, () => {
  console.log(`Server is running on port: ${PORT}`);
});
```

### `isAfter`

The `isAfter` method in `express-validator` is used to check if the input string is a date that's after the specified date. It accepts an optional options object that defaults to `{ comparisonDate: Date().toString() }` (now).

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const { body, validationResult } = require("express-validator");

app.use(express.json());

app.post("/validate", body("startDate").isAfter("2024-01-01"), (req, res) => {
  const result = validationResult(req);
  if (result.isEmpty()) {
    return res.status(200).send(`Welcome, ${req.body.userType}`);
  }

  res.status(400).send({ errors: result.array() });
});

app.listen(PORT, () => {
  console.log(`Server is running on port: ${PORT}`);
});
```

### `isBefore`

The `isBefore` method in `express-validator` is used to check if the input string is a date that's before the specified date.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const { body, validationResult } = require("express-validator");

app.use(express.json());

app.post("/validate", body("startDate").isBefore("2024-01-01"), (req, res) => {
  const result = validationResult(req);
  if (result.isEmpty()) {
    return res.status(200).send(`Welcome, ${req.body.userType}`);
  }

  res.status(400).send({ errors: result.array() });
});

app.listen(PORT, () => {
  console.log(`Server is running on port: ${PORT}`);
});
```

### `isEmail`

The `isEmail` method checks if the string is an email.

It accepts an optional options object. The object defaults to

```js
{
  allow_display_name: false,
  require_display_name: false,
  allow_utf8_local_part: true,
  require_tld: true,
  allow_ip_domain: false,
  allow_underscores: false,
  domain_specific_validation: false,
  blacklisted_chars: '',
  host_blacklist: []
}
```

- If `allow_display_name` is set to `true`, the validator will also match `Display Name <email-address>`.
- If `require_display_name` is set to `true`, the validator will reject strings without the format `Display Name <email-address>`.
- If `allow_utf8_local_part` is set to `false`, the validator will not allow any non-English UTF8 character in email address' local part.
- If `require_tld` is set to `false`, email addresses without a TLD in their domain will also be matched.
- If `ignore_max_length` is set to `true`, the validator will not check for the standard max length of an email.
- If `allow_ip_domain` is set to `true`, the validator will allow IP addresses in the host part.
- If `domain_specific_validation` is `true`, some additional validation will be enabled, e.g. disallowing certain syntactically valid email addresses that are rejected by Gmail.
- If `blacklisted_chars` receives a string, then the validator will reject emails that include any of the characters in the string, in the name part.
- If `host_blacklist` is set to an array of strings and the part of the email after the @ symbol matches one of the strings defined in it, the validation fails.
- If `host_whitelist` is set to an array of strings and the part of the email after the @ symbol matches none of the strings defined in it, the validation fails.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post("/validate", check("email").isEmail(), (req, res) => {
  const result = validationResult(req);
  const validData = matchedData(req);

  if (!result.isEmpty()) {
    return res.status(400).send({ status: "error", errors: result.array() });
  }

  res.status(200).send({ status: "success", validData });
});

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

One more example, this time with one of the optional properties:

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  check("email").isEmail({ host_blacklist: ["example.com", "test.com"] }),
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `isArray`

`isArray` adds a validator to check that a value is an array. The method accepts an options object, which could be used to check if the array's length is greater than or equal to `min` and/or that it's less than or equal to `max`.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post("/validate", check("tags").isArray({ min: 2 }), (req, res) => {
  const result = validationResult(req);
  const validData = matchedData(req);

  if (!result.isEmpty()) {
    return res.status(400).send({ status: "error", errors: result.array() });
  }

  res.status(200).send({ status: "success", validData });
});

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `isObject`

`isObject` adds a validator to check that a value is an object. For example, `{}`, `{ foo: 'bar' }` and `new MyCustomClass()` would all pass this validator.

If the `strict` option is set to `false`, then this validator works analogous to `typeof value === 'object'` in pure JavaScript, where both **arrays** and the `null` value are considered objects.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post("/validate", check("tags").isObject({ strict: false }), (req, res) => {
  const result = validationResult(req);
  const validData = matchedData(req);

  if (!result.isEmpty()) {
    return res.status(400).send({ status: "error", errors: result.array() });
  }

  res.status(200).send({ status: "success", validData });
});

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `isString`

`isString` adds a validator to check that a value is a string. This is analogous to a `typeof value === 'string'` in pure JavaScript.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post("/validate", check("answer").isString(), (req, res) => {
  const result = validationResult(req);
  const validData = matchedData(req);

  if (!result.isEmpty()) {
    return res.status(400).send({ status: "error", errors: result.array() });
  }

  res.status(200).send({ status: "success", validData });
});

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `isBoolean`

`isBoolean` adds a validator to check that a value is `true` or `false`.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post("/validate", check("answer").isBoolean(), (req, res) => {
  const result = validationResult(req);
  const validData = matchedData(req);

  if (!result.isEmpty()) {
    return res.status(400).send({ status: "error", errors: result.array() });
  }

  res.status(200).send({ status: "success", validData });
});

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `isCreditCard`

`isCreditCard` checks if the string is a credit card number.

It accepts an optional options object that can be supplied with the `provider` property. It is an optional key whose value should be a string, and defines the company issuing the credit card. Valid values include [`'amex'`, `'dinersclub'`, `'discover'`, `'jcb'`, `'mastercard'`, `'unionpay'`, `'visa'`] or **blank** will check for any provider.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  check("creditCard").isCreditCard({ provider: ["visa", "amex"] }),
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `isLength`

`isLength` check if the string's length falls in a range.

It accepts an options object which defaults to `{ min: 0, max: undefined }`. Note: this function takes into account surrogate pairs.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  check("short-text").isLength({ min: 1, max: 5 }),
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `isNumeric`

`isNumeric` checks if the string contains only numbers.

It accepts an options object which defaults to `{ no_symbols: false }`. If `no_symbols` is `true`, the validator will reject numeric strings that feature a symbol (e.g. `+`, `-`, or `.`).

It also has `locale` as an option. `locale` determines the decimal separator and is one of ['ar', 'ar-AE', 'ar-BH', 'ar-DZ', 'ar-EG', 'ar-IQ', 'ar-JO', 'ar-KW', 'ar-LB', 'ar-LY', 'ar-MA', 'ar-QA', 'ar-QM', 'ar-SA', 'ar-SD', 'ar-SY', 'ar-TN', 'ar-YE', 'bg-BG', 'cs-CZ', 'da-DK', 'de-DE', 'en-AU', 'en-GB', 'en-HK', 'en-IN', 'en-NZ', 'en-US', 'en-ZA', 'en-ZM', 'es-ES', 'fr-FR', 'fr-CA', 'hu-HU', 'it-IT', 'nb-NO', 'nl-NL', 'nn-NO', 'pl-PL', 'pt-BR', 'pt-PT', 'ru-RU', 'sl-SI', 'sr-RS', 'sr-RS@latin', 'sv-SE', 'tr-TR', 'uk-UA'].

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  check("number").isNumeric({ no_symbols: true }),
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `isDate`

`isDate` checks if the string is a valid date. e.g. [2002-07-15, `new Date()`].

It accepts an options object which can contain the keys `format`, `strictMode` and/or `delimiters`.

- `format` is a string and defaults to `YYYY/MM/DD`.

- `strictMode` is a boolean and defaults to `false`. If `strictMode` is set to `true`, the validator will reject strings different from format.

- `delimiters` is an array of allowed date delimiters and defaults to `['/', '-']`.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  check("startDate").isDate({
    format: "YYYY-MM-DD",
    strictMode: false,
    delimiters: ["/", "-", "."],
  }),
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `isIn`

`isIn` checks if the string is in an array of allowed values.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  check("role").isIn(["admin", "user", "guest"]),
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `isJSON`

`isJSON` checks if the string is valid JSON (note: uses `JSON.parse`).

It accepts an options object which defaults to `{ allow_primitives: false }`. If `allow_primitives` is `true`, the primitives '`true`', '`false`' and '`null`' are accepted as valid JSON values.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  body("tags").isJSON({ allow_primitives: true }),
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `isStrongPassword`

`isStrongPassword` checks if the string can be considered a strong password or not. Allows for custom requirements or scoring rules. If `returnScore` is `true`, then the function returns an integer score for the password rather than a boolean.
Default options:

```js
{
  minLength: 8,
  minLowercase: 1,
  minUppercase: 1,
  minNumbers: 1,
  minSymbols: 1,
  returnScore: false,
  pointsPerUnique: 1,
  pointsPerRepeat: 0.5,
  pointsForContainingLower: 10,
  pointsForContainingUpper: 10,
  pointsForContainingNumber: 10,
  pointsForContainingSymbol: 10
}
```

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  body("password").isStrongPassword({
    minLength: 8,
    minLowercase: 1,
    minUppercase: 1,
    minNumbers: 1,
    minSymbols: 1,
    returnScore: false,
    pointsPerUnique: 1,
    pointsPerRepeat: 0.5,
    pointsForContainingLower: 10,
    pointsForContainingUpper: 10,
    pointsForContainingNumber: 10,
    pointsForContainingSymbol: 10,
  }),
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `isWhitelisted`

`isWhitelisted` checks if the string consists only of characters that appear in the whitelist `chars`.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  body("username")
    .isWhitelisted(
      "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789_-"
    )
    .withMessage(
      "Username must only contain letters, numbers, underscores and dashes"
    ),
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `matches`

`matches` checks if the string matches a specified regular expression pattern.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  body("username")
    .matches(/^[a-zA-Z0-9_]+$/)
    .withMessage(
      "Username must contain only letters, numbers, and underscores"
    ),
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `custom`

`custom` method adds a custom validator function to the chain.

The validated value will be valid if:

- The custom validator returns truthy; or
- The custom validator returns a promise that resolves.

If the custom validator returns falsy, a promise that rejects, or if the function throws, then the value will be considered invalid.

The `custom((value, { req, location, path }) => {})` method accepts one required and one optional object as arguments:

- `value` (required) is the value of the field being validated.
- `{ req, location, path }` is an object with the following properties:
  - `req` is the Express request object.
  - `location` is the location of the field being validated (e.g., 'body', 'params', 'query').
  - `path` is the path to the field being validated (e.g., 'username').

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  check("age").custom((value, { req }) => {
    const age = parseInt(value);

    if (isNaN(age) || age < 18 || age > 65) {
      throw new Error("Age must be a number between 18 and 65");
    }

    return true;
  }),
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

## Sanitizers

### `trim`

`trim` method trims characters (whitespace by default) from both sides of the input.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  check("username").trim().notEmpty().withMessage("Username is required"),
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `ltrim`

`ltrim` trims characters from the left-side of the input.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  check("username").ltrim().notEmpty().withMessage("Username is required"),
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `rtrim`

`rtrim` trims characters from the right-side of the input.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  check("username").rtrim().notEmpty().withMessage("Username is required"),
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `normalizeEmail`

`normalizeEmail` is used to canonicalize an email address. In this context, "canonicalize" refers to the process of converting an email address into a standard, normalized form.
Specifically, the `normalizeEmail()` method performs following operations:

- Converts the entire email address to lowercase.
- Applies provider-specific rules for certain email services like Gmail, Hotmail, etc.

It accepts an options object with the following keys and default values:

- `all_lowercase: true` - Transforms the local part (before the @ symbol) of all email addresses to lowercase. Please note that this may violate RFC 5321, which gives providers the possibility to treat the local part of email addresses in a case sensitive way (although in practice most - yet not all - providers don't). The domain part of the email address is always lowercased, as it is case insensitive per RFC 1035.
- `gmail_lowercase: true`, `outlookdotcom_lowercase: true`, `yahoo_lowercase: true` - Gmail, Outlook.com (including Windows Live and Hotmail), Yahoo Mail, and iCloud (including MobileMe) addresses are known to be case-insensitive, so this switch allows lowercasing them even when `all_lowercase` is set to `false`. Please note that when `all_lowercase` is `true`, Gmail, Outlook.com, Yahoo Mail, and iCloud addresses are lowercased regardless of the value of this setting.
- `gmail_remove_subaddress: true`, `outlookdotcom_remove_subaddress: true`, `yahoo_remove_subaddress: true`, `icloud_remove_subaddress: true` - Normalizes addresses by removing "sub-addresses", which is the part following a "+" sign (e.g. "foo+bar@gmail.com" becomes "foo@gmail.com").
- `gmail_convert_googlemaildotcom: true` - Converts addresses with domain @googlemail.com to @gmail.com, as they're equivalent.
- `gmail_remove_dots: true` - Removes dots from the local part of the email address, as Gmail ignores them (e.g. "john.doe" and "johndoe" are considered equal).

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  check("email")
    .normalizeEmail()
    .isEmail()
    .withMessage("Please enter a valid email address"),
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `default`

`default` replaces the value of the field if it's either an empty string, `null`, `undefined`, or `NaN`.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post("/validate", check("username").default("foo"), (req, res) => {
  const result = validationResult(req);
  const validData = matchedData(req);

  if (!result.isEmpty()) {
    return res.status(400).send({ status: "error", errors: result.array() });
  }

  res.status(200).send({ status: "success", validData });
});

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `replace`

`replace` replaces the specific values of the field to another value:

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  check("username").replace(["bar", "BAR"], "foo"),
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `toArray`

Transforms the value to an array. If it already is one, nothing is done. `undefined` values become empty arrays.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  check("hobbies")
    .toArray()
    .custom((hobbies) => {
      const allowedHobbies = ["reading", "painting"];
      const invalidHobbies = hobbies.filter(
        (hobby) => !allowedHobbies.includes(hobby)
      );

      if (invalidHobbies.length > 0) {
        throw new Error(`Invalid hobbies: ${invalidHobbies.join(", ")}`);
      }

      return true;
    }),
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `blacklist`

`blacklist` remove characters that appear in the blacklist.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  check("username").blacklist("!@#$%^&*(){}[]|\\/?><,.:;"),
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `whitelist`

`whitelist` removes characters that do not appear in the whitelist.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  check("username").whitelist(
    "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789_"
  ),
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `escape`

`escape` replaces `<`, `>`, `&`, `'`, `"` and `/` with HTML entities. `<` becomes `&lt;`, `>` becomes `&gt;` etc.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post("/validate", check("username").escape(), (req, res) => {
  const result = validationResult(req);
  const validData = matchedData(req);

  if (!result.isEmpty()) {
    return res.status(400).send({ status: "error", errors: result.array() });
  }

  res.status(200).send({ status: "success", validData });
});

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `unescape`

`unescape` replaces HTML encoded entities with `<`, `>`, `&`, `'`, `"` and `/`. So, `&lt;` becomes `<`, `&gt;` becomes `>` etc.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post("/validate", check("text").unescape(), (req, res) => {
  const result = validationResult(req);
  const validData = matchedData(req);

  if (!result.isEmpty()) {
    return res.status(400).send({ status: "error", errors: result.array() });
  }

  res.status(200).send({ status: "success", validData });
});

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `toBoolean`

`toBoolean` converts the input string to a boolean. By default, everything except for `'0'`, `'false'` and `''` returns `true`. We can change the default behavior using the strict mode. In strict mode only `'1'` and `'true'` return `true`.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post("/validate", check("consent").toBoolean(), (req, res) => {
  const result = validationResult(req);
  const validData = matchedData(req);

  if (!result.isEmpty()) {
    return res.status(400).send({ status: "error", errors: result.array() });
  }

  res.status(200).send({ status: "success", validData });
});

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `toDate`

`toDate` convert the input string to a date, or `null` if the input is not a date.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post("/validate", check("startDate").toDate(), (req, res) => {
  const result = validationResult(req);
  const validData = matchedData(req);

  if (!result.isEmpty()) {
    return res.status(400).send({ status: "error", errors: result.array() });
  }

  res.status(200).send({ status: "success", validData });
});

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `customSanitizer`

`customSanitizer` adds a custom sanitizer function to the chain. The value returned by the function will become the new value of the field.

The `customSanitizer((value, { req, location, path }) => {})` method accepts one required and one optional object as arguments:

- `value` (required) is the value of the field being sanitized.
- `{ req, location, path }` is an object with the following properties:
  - `req` is the Express request object.
  - `location` is the location of the field being sanitized (e.g., 'body', 'params', 'query').
  - `path` is the path to the field being sanitized (e.g., 'username').

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

const sanitizeUsername = (value, { location }) => {
  // Remove leading and trailing whitespace characters
  value = value.trim();

  // Replace consecutive whitespace characters with a single space
  value = value.replace(/\s+/g, " ");

  if (location === "query") {
    console.log("The input is in the wrong location of the request");
  }

  return value;
};

app.post(
  "/validate",
  check("username").customSanitizer(sanitizeUsername),
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

## Modifiers

### `bail`

`bail` stops running the validation chain if any of the previous validators failed.

This is useful to prevent a custom validator that touches a database or external API from running when you know it will fail.

`bail` can be used multiple times in the same validation chain if desired.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  check("username")
    .notEmpty()
    .withMessage("Username is required")
    .bail()
    .isLength({ min: 4, max: 20 })
    .withMessage("Username must be between 4 and 20 characters long")
    .bail(),
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

What if there are several validation chains on a single request, and we want to stop all of them if the first one failed?! `bail` has an options object that we can use. If the `level` option is set to `"request"`, then also no further validation chains will run on the current request. By default, the `level` is set to `"chain"`.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  [
    check("username")
      .notEmpty()
      .withMessage("Username is required")
      .bail({ level: "request" })
      .isLength({ min: 4, max: 20 })
      .withMessage("Username must be between 4 and 20 characters long")
      .bail({ level: "request" }),
    check("email").isEmail().withMessage("Please enter a valid email address"),
  ],
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `if`

`if` adds a condition on whether the validation chain should continue running on a field or not.

The condition may be either a custom validator or a ContextRunner instance. ContextRunner instance basically means a validation chain.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  [
    check("newPassword")
      .if((value, { req }) => req.body.oldPassword)
      // The length of the new password will only be checked if `oldPassword` is provided.
      .isLength({ min: 6 }),
  ],
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

Here is another version of the above example. This time with the ContextRunner instance:

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  [
    check("newPassword")
      .if(body("oldPassword").notEmpty())
      // The length of the new password will only be checked if `oldPassword` is provided.
      .isLength({ min: 6 }),
  ],
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `not`

`not` negates the result of the next validator in the chain.

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  [check("weekday").not().isIn(["sunday", "saturday"])],
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

### `optional`

`optional` marks the current validation chain as optional. An optional field skips validation depending on its value, instead of failing it. Unlike validators and sanitizers, `optional` is not positional: it'll affect how values are interpreted, no matter where it happens in the chain.

The method accepts options object. Which values are considered optional depends on the `values` in the options object. By default, it's set to `undefined`. It can also be set to:

- `null` - `undefined` and `null` values are optional
- `falsy` - Falsy values (**empty strings**, `0`, `false`, `null` and `undefined` values are optional)

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  body,
  check,
  validationResult,
  matchedData,
} = require("express-validator");

app.use(express.json());

app.post(
  "/validate",
  [
    check("email").isEmail().withMessage("Please enter a valid email address"),
    check("age")
      .optional() // Age field is optional
      .isInt({ min: 18, max: 120 })
      .withMessage("Age must be an integer between 18 and 120"),
  ],
  (req, res) => {
    const result = validationResult(req);
    const validData = matchedData(req);

    if (!result.isEmpty()) {
      return res.status(400).send({ status: "error", errors: result.array() });
    }

    res.status(200).send({ status: "success", validData });
  }
);

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```

## `new ExpressValidator`

`ExpressValidator` is a class which wraps the entire `express-validator` API, with some differences:

- you can specify custom validators and/or custom sanitizers that are always available in validation chains;
- you can specify options that apply by default to some functions.

Here is an example with a custom validator:

```js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;
const {
  validationResult,
  matchedData,
  ExpressValidator,
} = require("express-validator");

app.use(express.json());
const { check } = new ExpressValidator({
  ageCheck: async (value, { req }) => {
    const age = parseInt(value);

    if (isNaN(age) || age < 18 || age > 65) {
      throw new Error("Age must be a number between 18 and 65");
    }

    return true;
  },
});

app.post("/validate", check("age").ageCheck(), (req, res) => {
  const result = validationResult(req);
  const validData = matchedData(req);

  if (!result.isEmpty()) {
    return res.status(400).send({ status: "error", errors: result.array() });
  }

  res.status(200).send({ status: "success", validData });
});

app.listen(PORT, () => {
  console.log(`Server is listening on port: ${PORT}`);
});
```
