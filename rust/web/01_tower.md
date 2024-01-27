# Tower

Tower is a library of modular and reusable components for building robust networking clients and servers.

Tower provides a simple core abstraction, the `Service` trait, which represents an asynchronous function taking a request and returning either a response or an error.
This abstraction can be used to model both clients and servers.

Generic components, like timeouts, rate limiting, and load balancing, can be modeled as `Service`s that wrap some inner service and apply additional behavior before or after the inner service is called.
This allows implementing these components in a protocol-agnostic, composable way.
Typically, such services are referred to as middleware.

An additional abstraction, the `Layer` trait, is used to compose middleware with `Service`s.
If a `Service` can be thought of as an asynchronous function from a request type to a response type, a `Layer` is a function taking a `Service` of one type and returning a `Service` of a different type.
The `ServiceBuilder` type is used to add middleware to a service by composing it with multiple `Layer`s.

## Service

```rs
pub trait Service<Request> {
    type Response;
    type Error;
    type Future: Future<Output = Result<Self::Response, Self::Error>>;

    fn poll_ready(
        &mut self, 
        cx: &mut Context<'_>
    ) -> Poll<Result<(), Self::Error>>;
    fn call(&mut self, req: Request) -> Self::Future;
}
```

`Service` represents an asynchronous function taking a request and returns a response or error.

Calling a `Service` which is at capacity (i.e., it is temporarily unable to process a request) should result in an error.
The caller is responsible for ensuring that the service is ready to receive the request before calling it.

Service provides a mechanism by which the caller is able to coordinate readiness.
`Service::poll_ready` returns `Ready` if the service expects that it is able to process a request.
If the service is at capacity, then `Poll::Pending` is returned and the task is notified when the service becomes ready again.
If `Poll::Ready(Err(_))` is returned, the service is no longer able to service requests and the caller should discard the service instance.

`poll_raedy` is expected to be called while on a task.
Generally, this can be done with a simple futures::future::poll_fn call.
However, `call` is callable off the task.

## MakeService

```rs
pub trait MakeService<Target, Request>: Sealed<(Target, Request)> {
    type Response;
    type Error;
    type Service: Service<Request, Response = Self::Response, Error = Self::Error>;
    type MakeError;
    type Future: Future<Output = Result<Self::Service, Self::MakeError>>;

    fn poll_ready(
        &mut self, 
        cx: &mut Context<'_>
    ) -> Poll<Result<(), Self::MakeError>>;
    fn make_service(&mut self, target: Target) -> Self::Future;

    fn into_service(self) -> IntoService<Self, Request>
    where
        Self: Sized,
    { ... }
    fn as_service(&mut self) -> AsService<'_, Self, Request>
    where
        Self: Sized,
    { ... }
}
```

`MakeService` is a service that takes a target and returns a service or error.
In other words, it is a service of service.

## Layer

```rs
pub trait Layer<S> {
    type Service;

    fn layer(&self, inner: S) -> Self::Service;
}
```

Decorates a `Service`, transforming either the request or the response.

The `Layer` trait can be used to write reusable components that can be applied to very different kinds of services.

## Middleware

This crate provides various middleware such as `filter`, `reconnect`.

### `tower-http 0.5.1`

tower-http is a library that provides HTTP-specific middleware and utilities built on top of tower.
