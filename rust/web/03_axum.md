# Axum

Axum is a web framework based on hyper and tokio runtime.

## Overview

Axum uses `tower::Service` to handle the HTTP requests,
which takes full advantage of `tower` and `tower-http` crate.
Therefore all you need to write is how to process `http::Request<B>` and return `http::Response<B>`.
Note that this service must not return error, therefore its error type is `Infallible`.

By default, all the services in axum are intended to be one-time use,
therefore always ready to be called.

Our ultimate goal is to provide `MakeService` to `TcpListener` (via `serve` function),
so that whenever `Request` arrives, creates `Service` to process request and return response.
Internally `Serve` is a hyper server.

Usually the service is created by `Router`, which is a layered service that maps the paths and services.
`Router` is then wrapped into `IntoMakeService`, which is a simple `MakeService` that clones the `Router`.
`Router` is `Clone` since it wraps internal structure with `Arc`.

```rs
#[tokio::main]
async fn main() {
    let listener = TcpListener::bind(address).await.unwrap();
    let router = router();
    axum::serve(listener, router.into_make_service()).await.unwrap();
}

fn router() -> Router { /* ... */ }
```

### Body

Axum uses its own default `Body` type which is internally a boxed `Body` trait object of that is `!Sync`.
Its data type is `Bytes` and error type is its own default `Error` type which is also a boxed `Error` trait object.
To distinguish with `Body` trait, axum renames `Body` trait as `HttpBody`.

`Request` and `Response` in axum also uses this type as their default body,
yet axum can still handle `Request` with arbitrary `HttpBody` which is compatible with this type.

```rs
pub struct Body(UnsyncBoxBody<Bytes, Error>);

pub struct UnsyncBoxBody<D, E> {
    inner: Pin<Box<dyn Body<Data = D, Error = E> + Send + 'static>>,
}
pub struct Error {
    inner: Box<dyn std::error::Error + Send + Sync>,
}

impl Body {
    /// Convert arbitrary body type compatible with this type.
    /// That is, boxable and its data type is `Bytes` and error type is standard Error.
    fn new<B>(body: B) -> Self
    where
        B: HttpBody<Data = Bytes> + Send + 'static,
        B::Error: Into<BoxError>, // Equivalent to `B::Error: std::error::Error + Send + Sync`
    { /* ... */ }
}
```

## Components

Axum framework consists of:

* Routing
* Handlers
* Extractors
* Responses
* Middleware

## Routing

`Router` is used to set up which path goes to which services.

Unless you add additional `Route`s this will respond with `404 Not Found` to all requests.
Each `Route` is a `Service` which is also `Clone + Send + 'static`.
They are internal types.

```rs
pub struct Router<S = ()> { /* private fields */ }
```

### State

Its generic type `S` is a type of shared state that **must be** provided to handlers (not provided yet).
This type must be `Clone + Send + Sync + 'static`.

`Router` can also add `Handler`, which is similar to `Service` but requires state to process request.
Once state is provided to `Handler`s in `Router` via `with_state` method,
`Handler`s are converted into `Route`s and they cannot accept any other states.
By providing state `S`, we can convert `S` into any other type.

Unless `S` is unit, `Router` is not a `Service`,
since `Handler`s require state to be provided to process request.

```rs
impl<S: Clone + Send + Sync + 'static> Router<S> {
    /// The state is applied to all `Handler`s in the router.
    fn with_state<S2>(self, state: S) -> Router<S2> { /* ... */ }
}

impl Router<()> {
    fn into_make_service(self) -> IntoMakeService<Self> { /* ... */ }
    /// `MakeService` with `ConnectInfo` as extension.
    fn into_make_service_with_connect_info<C>(self) -> IntoMakeServiceWithConnectInfo<Self, C> { /* ... */ }
}

impl<B> Service<Request<B>> for Router<()>
where
    B: HttpBody<Data = Bytes> + Send + 'static,
    B::Error: Into<BoxError>, // Equivalent to `B::Error: std::error::Error + Send + Sync`
{
    type Response = Response;
    type Error = Infallible;
    fn poll_ready(&mut self, _: &mut Context<'_>) -> Poll<Result<(), Self::Error>> {
        Poll::Ready(Ok(()))
    }
    fn call(&mut self, req: Request<B>) -> Self::Future {
        self.call_with_state(req.map(Body::new), ())
    }
}
```

### Inner Structure

Internally, `Router` is composed of `path_router`, `fallback_router`, `catch_all_fallback`.
`path_router` and `fallback_router` are both `PathRouter`, which maps each path to `Route` or `Handler`.
`catch_all_fallback` is `Route` or `Handler` whose error type is `Infallible`, working as a global fallback.
Unless you set fallback by `fallback` method,
default `fallback_router` and `catch_all_fallback` will respond with `404 NOT FOUND` to all requests.

When the request arrives, `Router` first tries to process with `path_router`,
then falls to `fallback_router` on failure, and finally `catch_all_fallback`.

`PathRouter` is an internal structure that maps path to `Route` or `MethodRouter`.
`MethodRouter` is another internal structure that maps HTTP method to `Route` or `Handler`.

Default `MethodRouter` will respond with `405 Method Not Allowed` to all requests.
You can create `MethodRouter` using method filter and `Handler` or `Service` (as `Route`).
The `Service` must be `Clone` so that it can be `Route`.

### Implementation

- `route`: Maps `MethodRouter` to given path.
- `route_service`: Maps `Service` (as `Route`) to given path.
The `Service` must be `Clone` so that it can be `Route`.
- `nest`: Nest another `Router` at given path.
Note that it does not inherit `catch_all_fallback`.
`fallback_router` will also be nested unless it is a default router.
- `merge`: Merges the paths and fallbacks of two router.
- `layer`: Apply a `tower::Layer` to all `Route`s and `Handler`s in router.
This also includes `fallback_router`.
- `route_layer`: Apply a `tower::Layer` to all `Route`s and `Handler`s only in `path_router`.
- `fallback`: Add a `Handler` as a fallback.
This sets both `fallback_router` and `catch_all_fallback`.
- `fallback_service`: Add a `Service` (as `Route`) as a fallback.
The `Service` must be `Clone` so that it can be `Route`.
This sets both `fallback_router` and `catch_all_fallback`.

## Handlers

As mentioned above, `Handler` is similar to `Service` but it also requires state to process request.
`Handler::with_state` will convert `Handler` into `Service`.

Simply it is `async fn<S>(Request, S) -> Response`.

The beauty of axum is writing `Handler` as an asynchronous function(or closure) whose parameters are extractors(extractable from request or state).
Axum provides blanket implementations for functions that:

* (Function) It must be `Clone + Send + 'static`.
* (Parameters) Take no more than 16 arguments that all implement Send.
    * All except the last argument implement `FromRequestParts`.
    * The last argument implements either `FromRequestParts` or `FromRequest`.
* (Return) Returns a future that is `Send`.
The most common way to accidentally make a future `!Send` is to hold a `!Send` type across an await.
You must care the parameters to be `Send` or `Sync` (reference) to make a future `Send`.
* (Return) Future returns something that implements `IntoResponse`.

```rs
pub trait Handler<T, S>: Clone + Send + Sized + 'static {
    type Future: Future<Output = Response> + Send + 'static;

    // Required method
    fn call(self, req: Request, state: S) -> Self::Future;

    // Provided methods
    // Still Handler (impl Handler for Layered)
    fn layer<L>(self, layer: L) -> Layered<L, Self, T, S>
    where
        L: Layer<HandlerService<Self, T, S>> + Clone,
        L::Service: Service<Request>
    { ... }

    // Service (impl Service for HandlerService)
    fn with_state(self, state: S) -> HandlerService<Self, T, S> { ... }
}
```

### Functions w/ Generic Types

Dealing with generic-typed functions is much more complicated.
There are more rules to follow.

* (Function) Since the function must be `Clone + Send + 'static`,
all generic types must always be `'static`.
Hopefully, functions are always `Clone` and `Send`.

## Extractors

Usually a handler function is an async function that takes any number of “extractors” as arguments.
An extractor is a type that implements `FromRequest` or `FromRequestParts`.
Extractors are how you pick apart the incoming request to get the parts your handler needs.

For example, Json is an extractor that consumes the request body and deserializes it as JSON into some target type.

Extractors always run in the order of the function parameters that is from left to right.

The request body is an asynchronous stream that can only be consumed once.
Therefore you can only have one extractor that consumes the request body.
Axum enforces this by requiring such extractors to be the last argument your handler takes.

### Common Extractors

```rs
// `Path` gives you the path parameters and deserializes them.
async fn path(Path(user_id): Path<u32>) {}

// `Query` gives you the query parameters and deserializes them.
async fn query(Query(params): Query<HashMap<String, String>>) {}

// `HeaderMap` gives you all the headers
async fn headers(headers: HeaderMap) {}

// `String` consumes the request body and ensures it is valid utf-8
async fn string(body: String) {}

// `Bytes` gives you the raw request body
async fn bytes(body: Bytes) {}

// `Json` comsumes the request body and deserializes it as JSON into some target type
async fn json(Json(payload): Json<Value>) {}

// `Request` gives you the whole request for maximum control
async fn request(request: Request) {}

// `Extension` extracts data from "request extensions"
// This is commonly used to share state with handlers
async fn extension(Extension(state): Extension<State>) {}
```

### Optional Extractors

All extractors defined in axum will reject the request if it doesn’t match.
If you wish to make an extractor optional you can wrap it in `Option`.

### Request Body Limits

For security reasons, `Bytes` will, by default, not accept bodies larger than 2MB.
This also applies to extractors that uses `Bytes` internally such as `String`, `Json`, and `Form`.

## Responses

Anything that implements `IntoResponse` can be returned from a handler.
Axum provides implementations for common types.

In general you can return tuples like:

* (StatusCode, impl IntoResponse)
* (Parts, impl IntoResponse)
* (Response<()>, impl IntoResponse)
* (T1, .., Tn, impl IntoResponse) where T1 to Tn all implement IntoResponseParts.
* (StatusCode, T1, .., Tn, impl IntoResponse) where T1 to Tn all implement IntoResponseParts.
* (Parts, T1, .., Tn, impl IntoResponse) where T1 to Tn all implement IntoResponseParts.
* (Response<()>, T1, .., Tn, impl IntoResponse) where T1 to Tn all implement IntoResponseParts.
