# Security

FastAPI provides OAS-compatible security schemes.

## OpenAPI Security Scheme

OpenAPI defines the following security schemes:

* `apiKey`: application-specific key that can come from query, cookie, or header.
* `http`: standard HTTP authentication systems, including `Bearer`, `Basic`.
* `oauth2`: all OAuth2 flows.
* `openIdConnect`: OpenID Connect specification.

## HTTP Authentication

HTTP `Basic` authentication using `HTTPBasic` instance.

```py
http_scheme = HTTPBasic()

@app.post('/users/me')
async def http_basic_example(credentials: Annotated[HTTPBasicCredentials, Depends(http_scheme)]):
    if credentials.username == username and credentials.password == password: ...
```

## OAuth2

OAuth2 is a specification that defines several ways to handle authentication and authorization.
It is quite an extensive specification and covers several complex use cases.
It includes ways to authenticate using a "third party".
That's what all the systems with "login with Facebook, Google, Twitter, GitHub" use underneath.

### OpenID Connect

OpenID Connect is another specification, based on OAuth2.
It just extends OAuth2 specifying some things that are relatively ambiguous in OAuth2, to try to make it more interoperable.
For example, Google login uses OpenID Connect (which underneath uses OAuth2).
But Facebook login doesn't support OpenID Connect. It has its own flavor of OAuth2.

### OAuth2 Password Flow

Password flow using `OAuth2PasswordBearer` instance.
Note that `OAuth2PasswordRequestForm` is not a valid pydantic model, which must be a dependency.

Token can be stored in `Authorization` header or response body.
Request body must have `access_token` and `token_type` field.

```py
oauth2_scheme = OAuth2PasswordBearer(tokenUrl='/token')

@app.post('/token')
async def password_grant(form: Annotated[OAuth2PasswordRequestForm, Depends()]) -> Token: ...

@app.get('/users/me')
async def get_me(token: Annotated[str, Depends(oauth2_scheme)]) -> User: ...
```

### OAuth2 Authorization Code Flow

Authorization code flow using `OAuth2AuthorizationCodeBearer` instance.
