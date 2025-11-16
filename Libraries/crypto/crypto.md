# crypto

- [crypto](#crypto)
  - [What is `crypto`](#what-is-crypto)
  - [An Example of using `crypto` with `sha256`](#an-example-of-using-crypto-with-sha256)
  - [List of Hash and Chiper Algorithms](#list-of-hash-and-chiper-algorithms)
  - [Factors to Consider in Choosing a Hash Algorithm](#factors-to-consider-in-choosing-a-hash-algorithm)
  - [`crypto.randomBytes()`](#cryptorandombytes)
  - [Encryption](#encryption)
  - [Some Encryption Algorithms:](#some-encryption-algorithms)
  - [Factors to Consider in Choosing an Encryption Algorithm](#factors-to-consider-in-choosing-an-encryption-algorithm)
  - [HMAC](#hmac)
  - [Digital signature](#digital-signature)

## What is `crypto`

The NodeJS `crypto` module provides cryptographic functions to help you secure your NodeJS app. It includes a set of wrappers for OpenSSL’s hash, HMAC, cipher, decipher, sign, and verify functions.

`crypto` is built into NodeJS, so it doesn’t require rigorous implementation process and configurations. Unlike other modules, you don’t need to install `crypto` before you use it in your NodeJS application.

- `crypto` allows you to hash plain texts before storing them in the database. For this, you have a hash class that can create fixed length, deterministic, collision-resistant, and unidirectional hashes. **For hashed data, a password cannot be decrypted with a predetermined key, unlike encrypted data.**

- An HMAC class is responsible for Hash-based Message Authentication Code, which hashes both key and values to create a single final hash.

- You may need to encrypt and decrypt other user data later for transmission purposes. This is where the Cipher and Decipher classes come in. You can encrypt data with the Cipher class and decrypt it with the Decipher class. Sometimes, you may not want to encrypt data before storing them in the database.

- You can also verify encrypted or hashed passwords to ensure they are valid. All you need is the Verify class. Certificates can also be signed with the sign class.

<hr>

## An Example of using `crypto` with `sha256`

Here's an example of how to compute the SHA-256 hash of a string:

```js
const crypto = require('crypto');

const data = 'Hello, World!';
const hash = crypto.createHash('sha256').update(data).digest('hex');

console.log('SHA-256 Hash:', hash);
```

Let's break down the code `crypto.createHash('sha256').update(data).digest('hex')` step by step:

1. `crypto.createHash('sha256')`: This creates a hash object using the SHA-256 algorithm. The `createHash` function is called with the algorithm name as a parameter (`'sha256'` in this case), and it returns a hash object that can be used to perform hashing operations.

2. `.update(data)`: The `update` method is used to update the hash object with the data you want to hash. In this case, the variable `data` contains the string 'Hello, World!'. By calling `.update(data)`, you provide the data that will be used as input for the hash function.

3. `.digest('hex')`: The `digest` method finalizes the hash computation and returns the computed hash value. In this example, `'hex'` is passed as the parameter to the `digest` method, indicating that the hash value should be returned as a hexadecimal string.

So, when you combine these three steps together, the code `crypto.createHash('sha256').update(data).digest('hex')` computes the SHA-256 hash of the `data` string ('Hello, World!') and returns it as a hexadecimal string.

In the end, the `hash` variable will contain the computed SHA-256 hash value as a string of characters. You can then use this hash value for various purposes like data integrity checks, password storage, or verifying data integrity.

<hr>

## List of Hash and Chiper Algorithms

Some Hash Algorithms:

- MD5: `md5`
- SHA-1: `sha1`
- SHA-256: `sha256`
- SHA-512: `sha512`

<hr>

You can use the `crypto.getHashes()` and `crypto.getCiphers()` methods to get the list of supported hash algorithms and encryption algorithms, respectively, at runtime. Here's an example:

```js
const crypto = require('crypto');

const hashAlgorithms = crypto.getHashes();
console.log('Supported Hash Algorithms:', hashAlgorithms);

const encryptionAlgorithms = crypto.getCiphers();
console.log('Supported Encryption Algorithms:', encryptionAlgorithms);
```

<hr>

## Factors to Consider in Choosing a Hash Algorithm

When it comes to choosing a hash algorithm, the preference depends on the specific requirements of your use case. Here are some factors to consider:

1. Security: The security of a hash algorithm is a crucial factor. You should choose an algorithm that is considered secure against known cryptographic attacks. In this regard, MD5 and SHA-1 are generally considered weak and are not recommended for new applications due to their vulnerability to collision attacks. SHA-256 and SHA-512 are more secure and widely used, offering a higher level of security.

2. Collision Resistance: Collision resistance refers to the algorithm's ability to minimize the likelihood of two different inputs producing the same hash output. MD5 and SHA-1 have demonstrated vulnerabilities in this area, with successful collision attacks. SHA-256 and SHA-512 have not been compromised in terms of collision resistance so far.

3. Application Compatibility: Depending on the requirements of your application, you may need to consider the compatibility of the hash algorithm across different systems and platforms. SHA-256 is widely supported and compatible with most systems, making it a good choice for interoperability.

4. Performance: The performance of the hash algorithm can be a consideration, particularly in resource-constrained environments or scenarios where high-speed processing is required. Generally, faster hash algorithms like MD5 and SHA-1 may have better performance, but they sacrifice security. SHA-256 and SHA-512 may be slower due to their larger output sizes, but they provide better security.

Given these considerations, SHA-256 is often a preferred choice for many applications due to its balance between security and performance. It is widely supported, resistant to known attacks, and offers a good level of collision resistance.

<hr>

## `crypto.randomBytes()`

The `crypto.randomBytes()` function in NodeJS is used to generate cryptographically strong random bytes. It is commonly used for generating random values, keys, or initialization vectors required for cryptographic operations.

The `crypto.randomBytes()` function takes two parameters: `size` and an optional callback function. Here's the syntax:

```javascript
crypto.randomBytes(size[, callback])
```

- `size`: This parameter specifies the number of random bytes to generate. It accepts an integer value representing the desired size of the random bytes buffer.

- `callback` (optional): If provided, the callback function will be invoked once the random bytes have been generated. The callback function follows the standard NodeJS error-first callback convention, where the first parameter is an error object (if an error occurs) and the second parameter is the generated random bytes buffer.

Here's an example that demonstrates how to use `crypto.randomBytes()` to generate a random 16-byte value:

```javascript
const crypto = require('crypto');

crypto.randomBytes(16, (err, buffer) => {
  if (err) {
    console.error('Error:', err);
    return;
  }

  console.log('Random Bytes:', buffer.toString('hex'));
});
```

In the above code, `crypto.randomBytes(16)` generates a buffer of 16 random bytes. The callback function receives the generated random bytes in the `buffer` parameter. In this example, the `buffer` is converted to a hexadecimal string using `.toString('hex')` for better readability.

It's important to note that `crypto.randomBytes()` generates cryptographically strong random bytes suitable for cryptographic purposes. These random bytes are generated using the operating system's random number generator or other cryptographic mechanisms specific to the platform, ensuring a high level of randomness and unpredictability.

Always use `crypto.randomBytes()` or similar functions provided by cryptographic libraries when generating random values for security-sensitive operations. Avoid using insecure random number generators or insufficiently random sources for cryptographic purposes.

<hr>

## Encryption

NodeJS `crypto` package also supports symmetric and asymmetric encryption. 

- Symmetric encryption uses the same key for both encryption and decryption, 
- whereas asymmetric encryption uses a pair of public and private keys. 

Here's an example of symmetric encryption using the AES algorithm:

```js
const crypto = require('crypto');

const algorithm = 'aes-256-cbc';
const key = crypto.randomBytes(32); // Generate a random encryption key
const iv = crypto.randomBytes(16); // Generate a random initialization vector

const data = 'Sensitive information';
const cipher = crypto.createCipheriv(algorithm, key, iv);
let encrypted = cipher.update(data, 'utf8', 'hex');
encrypted += cipher.final('hex');

console.log('Encrypted:', encrypted);

const decipher = crypto.createDecipheriv(algorithm, key, iv);
let decrypted = decipher.update(encrypted, 'hex', 'utf8');
decrypted += decipher.final('utf8');

console.log('Decrypted:', decrypted);
```

An IV should be unique for each encryption operation using the same key. It ensures that even if the same plaintext is encrypted multiple times, the resulting ciphertext will be different, which adds an additional layer of security.

Let's break down the code snippet and explain each part:

```javascript
const data = 'Sensitive information';
const cipher = crypto.createCipheriv(algorithm, key, iv);
let encrypted = cipher.update(data, 'utf8', 'hex');
encrypted += cipher.final('hex');
```

1. `crypto.createCipheriv(algorithm, key, iv)`: This line creates a cipher object using the `crypto.createCipheriv()` method. It takes three parameters:

   - `algorithm`: The encryption algorithm to be used. In this case, it is the `algorithm` variable, which is set to `'aes-256-cbc'`.

   - `key`: The encryption key used for the cipher. In this case, it is the `key` variable generated with `crypto.randomBytes()`.

   - `iv`: The initialization vector used for the cipher. In this case, it is the `iv` variable generated with `crypto.randomBytes()`.

   The `createCipheriv()` method returns a cipher object that can be used for encrypting data.

2. `let encrypted = cipher.update(data, 'utf8', 'hex');`: This line processes the `data` using the `update()` method of the cipher object. It takes three parameters:

   - `data`: The plaintext data that needs to be encrypted. In this case, it is the `data` variable that holds the string `'Sensitive information'`.

   - `'utf8'`: The input encoding of the plaintext data. Since the `data` is in UTF-8 format, it is specified as `'utf8'`.

   - `'hex'`: The output encoding of the encrypted data. By specifying `'hex'`, the resulting encrypted data will be represented as a hexadecimal string.

   The `update()` method processes a chunk of data and returns the encrypted portion.

3. `encrypted += cipher.final('hex');`: This line finalizes the encryption process and retrieves any remaining encrypted data. It uses the `final()` method of the cipher object, specifying the output encoding as `'hex'`.

   The `final()` method returns the remaining encrypted data and appends it to the `encrypted` variable. 
   
   In symmetric encryption, such as AES, the data is processed in blocks. The `.update()` method is used to process each block of data. However, the final block may not be a complete block, and there could be some remaining data. The `.final()` method ensures that any remaining data is processed and included in the final result.

After executing these lines of code, the variable `encrypted` will hold the encrypted data in hexadecimal format.

Please note that the `createCipheriv()` and `createDecipheriv()` methods in the `crypto` module use symmetric encryption, which means the same key and IV are used for both encryption and decryption. It's important to keep the key and IV secure and ensure they are shared securely between the sender and receiver.

<hr>

## Some Encryption Algorithms:

- Advanced Encryption Standard (AES): `aes-128-cbc`, `aes-192-cbc`, `aes-256-cbc`
- Triple Data Encryption Standard (3DES): `des-ede3-cbc`
- RSA: `rsa`, `rsa-pss`, `rsa-oaep`
- Elliptic Curve Cryptography (ECC): `ecdh`, `ecdsa`
- Blowfish: `bf-cbc`
- Cast: `cast-cbc`
- Camellia: `camellia-cbc`

<hr>

## Factors to Consider in Choosing an Encryption Algorithm

The preference for an encryption algorithm depends on various factors and the specific requirements of your use case. Here are some considerations to help you make a decision:

1. Security: The security of the encryption algorithm is of paramount importance. You should choose an algorithm that is considered secure against known cryptographic attacks. The Advanced Encryption Standard (AES) is widely regarded as secure and is the most commonly used encryption algorithm. AES offers different key sizes (128-bit, 192-bit, and 256-bit), with longer key sizes providing stronger security.

2. Performance: The performance of the encryption algorithm can be a critical factor, particularly in scenarios where high-speed processing is required or in resource-constrained environments. AES is known for its efficiency and optimized implementations, making it a popular choice for a wide range of applications. AES has been extensively studied and optimized for performance on various platforms.

3. Compatibility: Depending on your use case, you may need to consider the compatibility of the encryption algorithm across different systems and platforms. AES is a widely supported encryption algorithm and is natively supported in most modern cryptographic libraries and platforms.

4. Key Management: The encryption algorithm should be compatible with your key management strategy. AES supports symmetric key encryption, meaning the same key is used for both encryption and decryption. It's important to follow best practices for key generation, storage, and rotation to ensure the security of your encrypted data.

Considering these factors, AES is generally considered a preferable encryption algorithm due to its security, performance, and wide support. It has been extensively studied and widely adopted in various industries and applications. AES is recommended by government organizations such as the U.S. National Institute of Standards and Technology (NIST) and is used in numerous encryption standards and protocols.

<hr>

## HMAC

Here's an example of HMAC using the `crypto` package in NodeJS:

```javascript
const crypto = require('crypto');

const secretKey = 'mySecretKey';
const message = 'Hello, world!';

// Create HMAC instance with the desired hash algorithm and secret key
const hmac = crypto.createHmac('sha256', secretKey);

// Update HMAC with the message
hmac.update(message);

// Calculate the HMAC digest (the resulting message authentication code)
const hmacDigest = hmac.digest('hex');

console.log('HMAC:', hmacDigest);
```

In this example, we're using the `createHmac()` method to create an HMAC instance. We specify the hash algorithm we want to use (in this case, `'sha256'`) and provide the secret key (`'mySecretKey'`).

Next, we update the HMAC instance with the message we want to authenticate using the `update()` method. In this example, the message is `'Hello, world!'`.

Finally, we calculate the HMAC digest (the resulting message authentication code) by calling the `digest()` method on the HMAC instance. We specify `'hex'` as the encoding to obtain the digest as a hexadecimal string.

The resulting HMAC digest is then printed to the console.

<hr>

## Digital signature

The `crypto` package can also be used for generating and verifying digital signatures. Digital signatures provide a way to ensure the integrity and authenticity of data. Here's an example of generating a digital signature using the RSA algorithm:

```js
const crypto = require('crypto');

const { privateKey, publicKey } = crypto.generateKeyPairSync('rsa', {
  modulusLength: 2048,
});

const data = 'Hello, World!';
const sign = crypto.sign('sha256', Buffer.from(data), privateKey);

console.log('Signature:', sign.toString('hex'));

const isVerified = crypto.verify('sha256', Buffer.from(data), publicKey, sign);

console.log('Signature Verified:', isVerified);
```

