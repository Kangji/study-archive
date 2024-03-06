# HTTP

General purpose library(`http 1.0.0`) of common HTTP types.

This crate is a general purpose library for common types found when working with the HTTP protocol.
You’ll find `Request` and `Response` types for working as either a client or a server as well as all of their components.
Notably you’ll find `Uri` for what a `Request` is requesting, a `Method` for how it’s being requested, a `StatusCode` for what sort of response came back, a `Version` for how this was communicated, and `HeaderName`/`HeaderValue` definitions to get grouped in a `HeaderMap` to work with request/response headers.

You will notably not find an implementation of sending requests or spinning up a server in this crate.
It’s intended that this crate is the “standard library” for HTTP clients and servers without dictating any particular implementation.

## `Request` and `Response`

The main two types in this crate are the `Request` and `Response` types.
A `Request` could either be constructed to get sent off as a client or it can also be received to generate a `Response` for a server.
Similarly as a client a `Response` is what you get after sending a `Request`, whereas on a server you’ll be manufacturing a `Response` to send back to the client.

The `Request` and `Response` types in this crate consists of `Parts` and body.
Parts includes `Method`, `Uri` (only `Request`), `StatusCode` (only `Response`), `Version`, `HeaderMap`, and `Extension`.

## Headers

Another major piece of functionality in this library is HTTP header interpretation and generation.

`HeaderMap` type is a multimap of `HeaderName` to values, which defaults to `HeaderValue`.
The `HeaderName` type serves as a way to define header names, or what’s to the left of the colon.
A `HeaderValue` conversely is the header value, or what’s to the right of a colon.

The most common header names are already defined for you as constant values in the header module of this crate.

### `headers 0.4.0`

This crate provides typed HTTP headers, rather than `HeaderName`.
To set or get any header, an object must implement the `Header` trait from this module.
Several common headers are already provided, such as `Host`, `ContentType`, `UserAgent`, and others.

`HeaderMapExt` extension allows to execute "typed" methods on `HeaderMap`.

## Body

The `Request` and `Response` types in this crate are generic in what their body is.
This allows downstream libraries to use different representations such as `Request<Vec<u8>>`, `Response<impl Read>`, `Request<impl Stream<Item = Vec<u8>, Error = _>>`, or even `Response<MyCustomType>` where the custom type was deserialized from JSON.

The body representation is intentionally flexible to give downstream libraries maximal flexibility in implementing the body as appropriate.

### `http-body 1.0.0`

This crate provides a `Body` trait that represents asynchronous HTTP body.
This body polls data frame by frame.

### `http-body-util 0.1.0`

This crate provides some specific variants of `Body` implementations and combinators.
Useful implementations are:

* `Emtpy`: which is always empty
* `Full`: single-frame body
* `Limited`: body with size limit

## Extension

A type map of protocol extensions.

Extensions can be used by both `Request` and `Response` to store extra data derived from the underlying protocol.
