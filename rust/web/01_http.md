# HTTP

General purpose library([`http 1.0.0`](https://docs.rs/http/1.0.0/http/)) of common HTTP types.

You'll find `Request` and `Response` types for working as either a client or a server as well as of their components:
* (Request) `Uri`: what a `Request` is requesting
* (Request) `Method`: how the `Request` is being requested
* (Response) `StatusCode`: what source of response came back
* (Both) `Version`: how to communicate (HTTP protocol version)
* (Both) `HeaderMap`=\{`HeaderName`:`HeaderValue`\}: request/response headers
* (Both) `Extension`: custom extension

You'll not find an implementation of sending requests or spinning up a server.
It's intended that this crate is the **standard library** for HTTP without dictating any particular implementation.

## `Request` & `Response`

The main two types in this crate are the `Request` and `Response` types.
A `Request` could either be constructed to get sent off as a client or it can also be received to generate a `Response` for a server.
Similarly as a client a `Response` is what you get after sending a `Request`, whereas on a server you’ll be manufacturing a `Response` to send back to the client.

Both `Request` and `Response` consists of `Parts` and body.
`Parts` includes `Method`, `Uri`, etc.
Meanwhile, the body component is generic, enabling arbitrary types to represent the HTTP body.

## Headers

Another major piece of functionality in this library is HTTP header interpretation and generation.

* `HeaderMap` type is a multimap of `HeaderName` to values,
whose type defaults to `HeaderValue`.
* `HeaderName` represents an HTTP header field name, or what's to the left of the colon.
* `HeaderValue` conversely is the header value, or what's to the right of a colon.

The most common header names are already defined as constant values in the header module of this crate.

## Body

The `Request` and `Response` types are generic in what their body is.
This allows downstream libraries to use different representations such as `Request<Vec<u8>>`, `Response<impl Read>`, `Request<impl Stream<Item = Vec<u8>, Error = _>>`, or even `Response<MyCustomType>` where the custom type was deserialized from JSON.

The body representation is intentionally flexible to give downstream libraries maximal flexibility in implementing the body as appropriate.

## Extension

A type map of protocol extensions.

Extensions can be used by both `Request` and `Response` to store extra data derived from the underlying protocol.

# Crate [`headers 0.4.0`](https://docs.rs/headers/0.4.0/headers/)

This crate provides **typed** HTTP headers, rather than `HeaderName` constants.
To set or get any header, an object must implement the `Header` trait from this crate.
Several common header types are already provided,
such as `Host`, `ContentType`, `UserAgent`.

`HeaderMapExt` extension allows to execute "typed" methods on `HeaderMap`.

# Crate [`http-body 1.0.0`](https://docs.rs/http-body/1.0.0/http_body/)

This crate provides a `Body` trait that represents asynchronous HTTP body.
This body polls data frame by frame.

# Crate [`http-body-util 0.1.0`](https://docs.rs/http-body-util/0.1.1/http_body_util/)

This crate provides some specific variants of `Body` implementations as well as combinators.
Useful implementations are:

* `Empty`: which is always empty
* `Full`: single-frame body
* `Limited`: body with size limit
