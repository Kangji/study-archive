# Basics

A simple FastAPI application.

```py
# Create an app instance
app = FastAPI(root_path='/api/v1')

# Define path operation
@app.get('/')  # Path operation decorator
async def root():  # Path operation function
    return 'Hello, FastAPI!'

# Nested routes using APIRouter
users = APIRouter(prefix='/users', tags=['users'])
@users.get('/')
async def get_users(): ...

# Include routes of router (not router itself) to the app
app.include_router(users)
```

## Setup

Install fastapi and all related dependencies, which includes `uvicorn`, `pydantic`, etc.

```sh
pip install "fastapi[all]"
```

## Run

FastAPI is run by `uvicorn`, an ASGI(Asynchronous Server Gateway Interface) web server implementation for python.
ASGI is a spiritual successor of WSGI(Web Server Gateway Interface) that supports async.
FastAPI will properly call both async and sync functions.

```sh
uvicorn main:app --host="0.0.0.0" --reload
```

Uvicorn converts the HTTP request, calls matching path operation, and again converts the return value into response.

## OpenAPI

Automatically generates OAS based on annotations.

* `/docs`
* `/redoc`
* `/openapi.json`

If you want to hide API docs, set `openapi_url` as None **on construction**.

```py
app = FastAPI(openapi_url=None)
```

## Annotations

FastAPI automatically validates and converts(filters) the request/response with typing,
but internally validation is done by pydantic.

All functions called by uvicorn performs pydantic validation,
sucn as path operation, dependencies,
but not manual function calls.

### Pydantic

* Python library to perform data validation
* Inherits `BaseModel`
    * `BaseModel.__init__(self, **kwargs)`
        * No positional argument
        * Initialize fields by keyword arguments
* Define fields => each field must be initialized by keyword arguments or falls to default or error
    * (Required) Annotation: annotates type + metadata
        * Used at type validation, but does not validate the type of default value
        * Use Enum type to restrict available values
        * There are not only basic types but special types like `EmailStr`, but might have to install additional dependencies
        * Use `Field` with `Annotated` to add metadata(validation, docs, etc.)
        * Pyantic raises `pydantic.ValidationError` when failed to validate, then handled by FastAPI (422 Unprocessable Entity)
    * (Optional) Default: default value of the field

## DDD Structure Convention

```txt
fastapi-project
├── alembic/
├── src
│   ├── aws
│   │   ├── client.py  # client model for external service communication
│   │   ├── schemas.py
│   │   ├── config.py
│   │   ├── constants.py
│   │   ├── exceptions.py
│   │   └── utils.py
│   └── posts
│   │   ├── router.py
│   │   ├── schemas.py  # pydantic models
│   │   ├── models.py  # db models
│   │   ├── dependencies.py
│   │   ├── config.py  # local configs
│   │   ├── constants.py
│   │   ├── exceptions.py
│   │   ├── service.py
│   │   └── utils.py
│   ├── config.py  # global configs
│   ├── models.py  # global models
│   ├── exceptions.py  # global exceptions
│   ├── pagination.py  # global module e.g. pagination
│   ├── database.py  # db connection related stuff
│   └── main.py
├── tests/
│   ├── auth
│   ├── aws
│   └── posts
├── templates/
│   └── index.html
├── requirements
│   ├── base.txt
│   ├── dev.txt
│   └── prod.txt
├── .env
├── .gitignore
├── logging.ini
└── alembic.ini
```

1. Store all domain directories inside src folder
    * `src/` - highest level of an app, contains common models, configs, and constants, etc.
    * `src/main.py` - root of the project, which inits the FastAPI app
2. Each package has its own router, schemas, models, etc.
    * `router.py` - is a core of each module with all the endpoints
    * `schemas.py` - for pydantic models
    * `models.py` - for db models
    * `service.py` - module specific business logic
    * `dependencies.py` - router dependencies
    * `constants.py` - module specific constants and error codes
    * `config.py` - e.g. env vars
    * `utils.py` - non-business logic functions, e.g. response normalization, data enrichment, etc.
    * `exceptions.py` - module specific exceptions
