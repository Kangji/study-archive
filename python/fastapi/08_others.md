# Logging

Since the app is executed by `uvicorn`, use its logger.

```py
logger = logging.getLogger('uvicorn')
```

# Middleware

Register middleware to app.
Middleware is called by `uvicorn`.

```py
@app.middleware('http')
async def middleware_example(request: Request, call_next: Callable[[Request], Response]) -> Response: ...
```

## Cross-Origin Resource Sharing (CORS)

CORS middleware is a good example.

```py
origins = [
    'http://localhost:8080',
    'http://localhost',
    'http://localhost-b'
]

app.add_middleware(
    CORSMiddleware,
    allow_origins=origins,  # Allows requests from specific origins
    allow_credentials=True, # Allows credentials in requests (Auth headers, cookies, etc.)
    allow_methods=['*'],    # Allows requests with specific methods
    allow_headers=['*'],    # Allows specific headers in requests
)
```

There are other fancy middlewares such as `uvicorn.ProxyHeadersMiddleware`, `fastapi.GZipMiddleware`, `fastapi.TrustedHostMiddleware`, `fastapi.HTTPSRedirectMiddleware`

# Background Tasks

You can define background tasks to be run after returning a response.
Background Tasks are run by FastAPI.
Therefore it can be easily inferred that rules for task functions are identical to path operation or dependency functions.
A good example is email notification.

```py
def write_notification(email: str, message=''): ...

@app.post('/users/{user_id}/notification')
async def send_notification(user: Annotated[User, Depends(get_me)], background_tasks: BackgroundTasks):
    background_tasks.add_task(write_notification, user.email, message='Hello world!')
```

# Static Files

You can serve static files from a directory using `StaticFiles`.

```py
app.mount('/pages', StaticFiles(directory='pages'), name='pages')  # name is FastAPI internal.
app.mount('/scripts', StaticFiles(directory='scripts'), name='scripts')
```

# Lifespan

You can attach async context manager as lifespan event (startup & shutdown).
Note that you must attach a function that takes `FastAPI` as parameter and returns async context manager.

```py
@asynccontextmanager
async def lifespan(app: FastAPI):
    print('start')
    yield
    print('end')

app = FastAPI(lifespan=lifespan)
```
