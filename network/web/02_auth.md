# Web Authentication

There are several ways to securely authenticate and authorize the user and client.

## API Key

Using HMAC, server can validate the request from authorized user.
Generate HMAC of request with API secret and attach.

The authentication protocol varies on web services.

Drawbacks of API key method is: server must know user's API secret - it can be attack point.

### Man-in-the-Middle (MITM)

To prevent MITM, it is recommended to add timestamp in the request to sign.
Server will only accept fresh requests: reject if the request is too old.
Since MITM takes time, service provider can effectively prevent MITM by setting appropriate receiving window.

## HTTP Authentication

Authentication method using HTTP's `Authorization` header.
Its header value is `<type> <credential>`.

* `Basic` type: credential = `base64("{username}:{password}")`
* `Bearer` type: credential = token, in most case JWT.

Note that `Basic` type does not encrypt the user's credential, therefore it must be sent over HTTPS.
`Bearer` type must provide the solution to securely store the token.
When the token is hijacked, the attacker can access the user's resources.

### Cross-Site Scripting (XSS)

XSS attacker inserts a script in the webpage owned by someone else (stored XSS).
As long as the user believes the victim website, he/she will access the webpage,
then the script inserted will run on the user's browser.
The problem is that browser stores a number of sensitive data, such as access token.

To keep safe, the frontend must store sensitive data in the place where script cannot read.
Usually there are two choices of storage: browser local storage and document cookie.
Accessing local storage is fast, but it is vulnerable to XSS that javascript can easily access to local storage.
On the other hand, as long as the cookie is set `httpOnly`, it is not readable by javascript.

### Cross-Site Request Forgery (CSRF)

CSRF attacker induce the user to access their malicious web page,
in order to use their cookies which are automatically attached to the http request, by browser.
To prevent this, a cookie issuer must set `SameSite=Strict`.
If token is stored on local storage, it is safe from CSRF attack.

유저가 악성 페이지에 접속하면 악성 페이지에서 원래 앱 서버로 악성 요청을 보내게 하는데, 이 때 브라우저 쿠키가 자동으로 포함되어 보내지기 때문에 위험한 것.

### Refresh Token

In order to mitigate the risk when token is hijacked,
modern web services adopted refresh token.

The key idea is that access token expires quickly, but can be refreshed by refresh token.
Client can store access token in less-safe place (for some purpose),
but must store refresh token in more-safe place.

Client sends both access token and refresh token on authenticated endpoints.
When access token is expired, check refresh token and then issue new access token and refresh token.

## OAuth2

OpenAuthentication 2.0 is the standard of authentication using `Bearer` token.
OAuth2 allows third party to authenticate the user.

### Roles

Note that client does not mean web application client, but client of OAuth2 service, which is another web application.

* Resource Owner: Whom the resource (i.e., profile) belongs to. Application user.
* Client: Application(both frontend/backend) that delegates auth to OAuth2 service provider. OAuth2 service provider could be the application itself.
* Authorization Server: Part of OAuth2 service provider, who authenticates and authorizes the client to access resource.
* Resource Server: Part of OAuth2 service provider, who owns the resource (i.e., profile) of the user.

For example, let's assume that your application uses Google's OAuth2 service. Then:

* Resource: User's google account profile
* Resource Owner: Application user, who has google account
* Client: Your application
* Authorization Server & Resource Server: Google OAuth2 service

### Authorization Code Flow

The most common flow when using third party as auth server.

1. Redirects to server
    * Request - User(Browser) -> Client(App Server): User requests client for login
    * Response - Client(App Server) -> User(Browser): 301 REDIRECT to OAuth service with `client_id`, `redirect_url`, `response_type=code` parameters added
    * Request - User(Browser) -> Server(Auth Server): Request redirected to server's OAuth service with client information
    * Response - Server(Auth Server) -> User(Browser): Shows server's OAuth login form
2. Login to server
    * Request - User(Browser) -> Server(Auth Server): User requests authentication for login with `client_id`, `redirect_url`, `response_type=code` parameters added by client
    * Response - Server(Auth Server) -> User(Browser): Validates user and 301 REDIRECT to `redirect_url` with authorization code paramter added
        * Since it is redirect response, all attached info are visible in url - this is why we cannot attach access token directly
    * Request - User(Browser) -> Client(App Server): Request redirected to client's `redirect_url` with authorization code
    * Request - Client(App Server) -> Server(Auth Server): Client requests server for access token, with `client_id`, `client_secret`, `response_type=code`
    * Response - Server(Auth Server) -> Client(App Server): Validates the client and returns access token (+ refresh token)
    * Response - Client(App Server) -> User(Browser): Returns access token (+ refresh token)
3. Access to private endpoint
    * Request - User(Browser) -> Client(App Server): User requests private endpoint with access token (+ refresh token)
    * Request - Client(App Server) -> Server(Resource Server): Client requests user resources with access token (+ refresh token)
        * When access token is expired and refresh token is valid, issue new AT & RT
    * Response - Server(Resource Server) -> Client(App Server): Server validates token and returns user resources
    * Response - Client(App Server) -> User(Browser): Validates user authorization using user resources, and response to the request

### Password Flow

When client is trustworthy, i.e., internal program.

1. Login to server
    * Request - User(Browser) -> Client(App Server): User requests authentication for login
    * Request - Client(App Server) -> Server(Auth Server): Client requests server for access token, with `client_id`, `username`, `password`, `grant_type=password` parameters added
    * Response - Server(Auth Server) -> Client(App Server): Validate user and returns access token (+ refresh token)
    * Response - Client(App Server) -> Server(Auth Server): Returns access token (+ refresh token)
2. Access to private endpoint
    * Request - User(Browser) -> Client(App Server): User requests private endpoint with access token (+ refresh token)
    * Request - Client(App Server) -> Server(Resource Server): Client requests user resources with access token (+ refresh token)
        * When access token is expired and refresh token is valid, issue new AT & RT
    * Response - Server(Resource Server) -> Client(App Server): Server validates token and returns user resources
    * Response - Client(App Server) -> User(Browser): Validates user authorization using user resources, and response to the request
