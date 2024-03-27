# Cross-Origin Resource Sharing

Cross-Origin Resource Sharing (CORS) is an HTTP-header based mechanism that allows a server to indicate any origins (domain, scheme, or port) other than its own from which a browser should permit loading resources.
CORS also relies on a mechanism by which browsers make a "preflight" request to the server hosting the cross-origin resource,
in order to check that the server will permit the actual request.
In that preflight, the browser sends headers that indicate the HTTP method and headers that will be used in the actual request.

For security reasons, browsers restrict cross-origin HTTP requests initiated from scripts.
For example, fetch() and XMLHttpRequest follow the same-origin policy.
This means that a web application using those APIs can only request resources from the same origin the application was loaded from unless the response from other origins includes the right CORS headers.

만약 브라우저가 same-origin policy가 아니라면 서버는 CORS mechanism을 구현할 필요가 없다.
만약 cross-origin을 일절 허용하고 싶지 않다면 서버는 CORS mechanism을 구현하지 않으면 된다.

## Functional Overview

The Cross-Origin Resource Sharing standard works by adding new HTTP headers that let servers describe which origins are permitted to read that information from a web browser.
Additionally, for HTTP request methods that can cause side-effects on server data (in particular, HTTP methods other than GET, or POST with certain MIME types),
the specification mandates that browsers "preflight" the request,
soliciting supported methods from the server with the HTTP `OPTIONS` request method,
and then, upon "approval" from the server, sending the actual request.
`Origin` header specifies the origin.
Servers can also inform clients whether "credentials" (such as Cookies and HTTP Authentication) should be sent with requests.

## Simple Request

Some requests don't trigger a CORS preflight.
Those are called simple requests from the obsolete CORS spec,
though the Fetch spec (which now defines CORS) doesn't use that term.

The motivation is that the `<form>` element from HTML 4.0 (which predates cross-site fetch() and XMLHttpRequest) can submit simple requests to any origin,
so anyone writing a server must already be protecting against cross-site request forgery (CSRF).
Under this assumption, the server doesn't have to opt-in (by responding to a preflight request) to receive any request that looks like a form submission,
since the threat of CSRF is no worse than that of form submission.
However, the server still must opt-in using `Access-Control-Allow-Origin` to share the response with the script.

A simple request is one that meets all the following conditions:

* One of the allowed methods:
    * `GET`
    * `HEAD`
    * `POST`
* Apart from the headers automatically set by the user agent (for example, `Connection`, `User-Agent`), the only headers which are allowed to be manually set are:
    * `Accept`
    * `Accept-Language`
    * `Content-Language`
    * `Content-Type`
    * `Range` (only with a simple range header value; e.g., bytes=256- or bytes=127-255)
* The only type/subtype combinations allowed for the media type specified in the `Content-Type` header are:
    * `application/x-www-form-urlencoded`
    * `multipart/form-data`
    * `text/plain`

## Preflight Request

Unlike simple requests, for "preflighted" requests the browser first sends an HTTP request using the `OPTIONS` method to the resource on the other origin,
in order to determine if the actual request is safe to send.
Such cross-origin requests are preflighted since they may have implications for user data.

Its response have several access control info, including:

* `Access-Control-Allow-Origin`: List of allowed cross-origin
* `Access-Control-Allow-Method`: List of allowed HTTP methods
* `Access-Control-Allow-Header`: List of allowed custom(or mutable) headers

When the access is approved, then browser sends the actual request.
