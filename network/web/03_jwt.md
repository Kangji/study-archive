# JSON Web Token

A JSON Web Token (JWT) is a compact and URL-safe way of passing a JSON message between two parties.
It's a standard, defined in RFC 7519.

The token is a long string, divided into different parts separated with dots, and each part is base64 encoded.
What parts the token has depends on the type of the JWT: whether it's a JWS (a signed token) or a JWE (an encrypted token).

If the token is signed it will have three sections: the header, the payload and the signature.
If the token is encrypted it will consist of five parts: the header, the encrypted key, the initialization vector, the ciphertext (payload) and the authentication tag.

Probably the most common use case for JWTs is to use them as Access Tokens and ID Tokens in OAuth and OpenID Connect flows, but they can serve different purposes as well.
In this document, we only consider JWS.

## JWT Structure

### Header

```json
{
    "typ": "JWT",
    "alg": "HS256", // HMAC-SHA-256
}
```

### Payload

Payload contains serveral **`claim`**s, where each is a key-value pair.
It can also be a token metadata such as expiration. (Registered Claim)

It is strongly discouraged to store sensitive data in payload, since anyone can read the payload from jwt.
For example, store public_id rather than username.

### Signature

Signature is created from `{header}.{payload}` signed by JWT secret key held by server.
The signature algorithm is defined in the header.
