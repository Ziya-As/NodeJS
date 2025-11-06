# Bcrypt

- [Bcrypt](#bcrypt)
  - [Questions and Answers](#questions-and-answers)
    - [1. What are the dictionary attacks?](#1-what-are-the-dictionary-attacks)
    - [2. What are the brute force attacks?](#2-what-are-the-brute-force-attacks)
    - [3. What is hashing?](#3-what-is-hashing)
    - [4. What is a salt?](#4-what-is-a-salt)
  - [How to salt and hash a password using bcrypt](#how-to-salt-and-hash-a-password-using-bcrypt)
    - [Example of hashing a password using `genSalt` and `hash` methods](#example-of-hashing-a-password-using-gensalt-and-hash-methods)
    - [Example of hashing a password using only the `hash` method](#example-of-hashing-a-password-using-only-the-hash-method)
  - [Compare the inputted password with the saved password](#compare-the-inputted-password-with-the-saved-password)
  - [Synchronous example](#synchronous-example)
    - [Using `genSaltSync`, `hashSync`, and `compareSync`](#using-gensaltsync-hashsync-and-comparesync)
    - [Using `hashSync`, and `compareSync`](#using-hashsync-and-comparesync)
  - [Promise based example](#promise-based-example)

<hr>

## Questions and Answers

### 1. What are the dictionary attacks?

> A **Dictionary Attack** is a type of brute-force attack, but it uses a predefined list of passwords that would have a higher probability of success.  
> This dictionary list could contain things such as regional sports teams names, team member names, names related to the organization being attacked, commonly used passwords often containing ‘spring,’ ‘summer,’ ‘winter’ and ‘autumn’ and variations of all those modified to meet password requirements.

### 2. What are the brute force attacks?

> **Brute Force Attack** is a hacking method, where a hacker tries to guess the password and other login credentials of a user by trial and error process.

### 3. What is hashing?

> **Hashing** is the process of converting a given key into another value. A hash function is used to generate the new value according to a mathematical algorithm. More formally, a **hash function** is a function that can be used to map data of arbitrary size to fixed-size values, though there are some hash functions that support variable length output. The values that are returned by a hash function is called _hash values, hash codes, digests, or hashes_.  
> Use of a hash function to index a hash table is called _hashing_ or _scatter storage_.  
> A hash table stores key and value pairs in a list that is accessible through its index. A hash value then becomes the index for a specific element.  
> To prevent the conversion of hash back into the original key, a good hash always uses a one-way hashing algorithm.

### 4. What is a salt?

> In cryptography, a **salt** is random data that is used as an additional input to a one-way function that hashes data, a password or passphrase. A salt is added to the hashing process to force their uniqueness, increase their complexity without increasing user requirements, and to mitigate password attacks like hash tables.

<hr>

## How to salt and hash a password using bcrypt

There are 2 ways to hash a password:

- We can use `genSalt` method to generate a salt. Then, we provide that salt as an argument to `hash` method.
- We can use just the `hash` method. The salt will be generated automatically.

### Example of hashing a password using `genSalt` and `hash` methods

Here is an example of using `genSalt` and `hash`:

```js
const bcrypt = require("bcrypt");

const saltRounds = 10;

// In real life, this would be a value passed back from a registration form.
const password = "Fkdj^45ci@Jad";

bcrypt.genSalt(saltRounds, function (err, salt) {
  bcrypt.hash(password, salt, function (err, hash) {
    // Store hash in database here
    console.log(hash);
  });
});
```

- We set the `saltRounds` value. The higher the `saltRounds` value, the more time the hashing algorithm takes. You want to select a number that is high enough to prevent attacks, but not slower than potential user patience. In this example, we use the default value, 10.
- We can salt and hash synchronously or asynchronously. The recommendation is to do so asynchronously. That's what we did.
- At the end, we simply `console.log` the hash. In real life, there would probably be a function there to insert the hash into a database.

### Example of hashing a password using only the `hash` method

Instead of writing the salt and hash functions separately, we can combine them into one function.

```js
const bcrypt = require("bcrypt");

const saltRounds = 10;

const password = "Fkdj^45ci@Jad";

bcrypt.hash(password, saltRounds, function (err, hash) {
  console.log(hash);
});
```

## Compare the inputted password with the saved password

Now that we've safely secured the hash in our database, when a user attempts to log in, we have to compare the plain text password to the hash. We do so by using the bcrypt `compare` function.

We pass `bcrypt.compare()` these parameters:

- The password we are comparing
- The hash that is stored in the database
- Callback of error and the result: If the password matches the hash, the result is `true`. If it is not a match, the result is `false`.

```js
bcrypt.compare(password, hash, function (err, result) {
  if (result) {
    console.log("It matches!");
  } else {
    console.log("Invalid password!");
  }
});
```

<hr>

## Synchronous example

### Using `genSaltSync`, `hashSync`, and `compareSync`

```js
const bcrypt = require("bcrypt");
const saltRounds = 10;
const plainTextPassword = "DFGh5546*%^__90";

const salt = bcrypt.genSaltSync(saltRounds);
const hash = bcrypt.hashSync(plainTextPassword, salt);

console.log(hash);

const result = bcrypt.compareSync(plainTextPassword, hash);

console.log(result); // true
```

### Using `hashSync`, and `compareSync`

```js
const bcrypt = require("bcrypt");
const plainTextPassword = "DFGh5546*%^__90";

const hash = bcrypt.hashSync(plainTextPassword, 10);

console.log(hash);

const result = bcrypt.compareSync(plainTextPassword, hash);

console.log(result); // true
```

<hr>

## Promise based example

Here is an example using `then`/`catch` syntax with `genSalt` and `hash`:

```js
const bcrypt = require("bcrypt");
const saltRounds = 10;
const plainTextPassword = "DFGh5546*%^__90";

bcrypt
  .genSalt(saltRounds)
  .then((salt) => {
    console.log(`Salt: ${salt}`);
    return bcrypt.hash(plainTextPassword, salt);
  })
  .then((hash) => {
    console.log(`Hash: ${hash}`);
  })
  .catch((err) => console.error(err.message));
```

Here is an example using `then`/`catch` syntax with the `hash` method:

```js
// app.js
const bcrypt = require("bcrypt");
const saltRounds = 10;
const plainTextPassword1 = "DFGh5546*%^__90";
bcrypt
  .hash(plainTextPassword1, saltRounds)
  .then((hash) => {
    console.log(`Hash: ${hash}`);
    // Store hash in your password DB.
  })
  .catch((err) => console.error(err.message));
```
