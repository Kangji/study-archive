# Path Operation

Path operations are the routes of FastAPI application.
We've already seen the example of path operation.

These functions are called by FastAPI, which is smart enough.
Before calling the function, FastAPI extracts the parameters from request.
It also properly handles both async and sync.

## Path Parameter

Path parameters are specified at the path.

```py
@app.get('/items/{item_id}')
async def path_parameter_example(item_id: int): ...
```

Enums are selectable path parameter.

```py
class ModelName(Enum):
    Bert = 'BERT'
    Cnn = 'CNN'

@app.get('/models/{name}')
async def enum_path_parameter(name: ModelName): ...
```

### Path Operation Order Matters

FastAPI will resolve into first matched path operation.
Therefore, order matters, like NGINX.

A good example is `/users/me`.
It must be declared prior to `/users/{user_id}`.

## Query Parameter

Unless path parameter / pydantic model, it is query parameter.

```py
# GET /items?skip=0&limit=5
@app.get('/items')
async def query_parameter_example(skip: int = 0, limit: int = 10): ...
```

## Request Body

Pydantic Models are request body, an JSON-deserialized object.

```py
class Item(BaseModel):
    name: str
    description: str | None = None
    price: float

@app.post('/items')
async def request_body_example(item: Item): ...
```

## Explicit Parameter Type

Not only are `Query`, `Path`, `Body` used to add metadata(extra validations, docs, etc.) when used with `Annotated`(3.9+),
but also explicity specification of the parameter type.

```py
# Validation
@app.get('/items')
async def parameter_validation_example(q: Annotated[str | None, Query(max_length=50)]): ...

# Docs
@app.get('/models/{name}')
async def parameter_docs_example(name: Annotated[str, Path(title='Model Name')]): ...

# Param Type
# For example, list param
@app.get('/items')
async def explicit_parameter_type_example(name: Annotated[list[int], Query()] = []): ...

# Non-pydantic body
@app.post('/models')
async def non_pydantic_body_example(model: Annotated[str, Body()]): ...
```

## Header Parameter

Annotate with `Header`, use header name as variable name.
It automatically converts hyphen to underscore.

```py
@app.get('/')
async def header_parameter_example(user_agent: Annotated[str, Header()]): ...
```

### Cookie Parameter

Annotate with `Cookie`, which is a special case of `Header` parameter, use cookie name as variable name.

```py
@app.get('/')
async def cookie_parameter_example(cookie_name: Annotated[str, Cookie()]): ...
```

## Form Data

First install `python-multipart`.

Annotate with `Form`, use field names as variable names.

```py
@app.post('/users/login')
async def form_data_example(user: Annotated[str, Form()], password: Annotated[str, Form()]): ...
```

## Request Files

Files are sent as form-data, so install `python-multipart`.

There are two ways to receive file.

* File as `bytes`: all data stored in memory, and must explicitly annotate with `File`.
* File as `UploadFile`: "spooled" file.

```py
@app.post('/users/{user_id}/profile')
async def file_example(user_id: int, profile: Annotated[bytes, File()]): ...

@app.post('/users/{user_id}/photo')
async def upload_file_Example(user_id: int, photo: UploadFile): ...
```

When your path operation has both `File` parameter and `Form` parameter, files are uploaded as form data.

## Temporal Response

You can declare a parameter of type `Response`, and do something like setting headers in that temporal response object.
This is an empty response object created by FastAPI before running path operation function.
Return data of path operation will be merged with this response.

```py
@app.get('/')
async def temporal_response_example(response: Response):
    response.headers['X-Foo-Bar'] = 'Hello'
```

## Response Model

FastAPI validates and converts(filters) the return data with return type.
Raise 500 Internal Server Error on failure.

However, `response_model` in path operation decorator, which is prior to return type,
allows return type to be different type (e.g, `dict` or `Any`).

When return type or response_model is `None`, validation & conversion is disabled.

```py
@app.get('/', response_model=Foo)
async def response_model_example() -> dict[str, int]: ...
```

Set path operation decorator parameter `response_model_exclude_unset`, `response_model_include`, `response_model_exclude`.
It provides the control over whether to include or exclude the specific fields.

For example, when `response_model_exclude_unset=True`, all unset fields are excluded in the response.

```py
@app.get('/', response_model_exclude_unset=True)
async def response_model_more_example() -> Foo: ...
```

### Multiple Response Models

When path operation can return more than one response type,
FastAPI will only document one of them,
but you can manually document using `responses` argument in path operation decorator.

## Response Class

Rather than `Response`, you can customize your response class: `HTMLResponse`, `JSONResponse`, etc.
You can specify it by `response_class` parameter in decorator.

Note that when you use `HTMLResponse`, be aware of XSS attack.

## Path Operation Configuration

```py
@app.get(
    '/',
    status_code=201, # default = 200
    deprecated=True,
    # FastAPI docs
    tags = ['users', 'items'],
    summary = 'API endpoint summary',
    description = 'An API endpoint description can be replaced into python docstring. Also supports markdown.',
    response_description = 'Explains the response',
    responses = {
        200: {
            'model': Foo,
            'description': '200 description'
        },
        201: {
            'model': Bar,
            'description': '201 description'
        }
    }
)
async def configuration_example(): ...
```
