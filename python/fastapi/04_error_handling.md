# Error Handling

## `HTTPException`: Most General Solution

```py
class HTTPException:
    def __init__(self, status_code: int, detail: Any = None, headers: dict[str, str] = None): ...
```

Default exception handler of `HTTPException` will return:

```json
{
    "detail": ...
}
```

## Custom Exception Handler

Register exception handler to the app, or override the default exception handler.

```py
from fastapi import Request
from fastapi.responses import JSONResponse

class CustomException(BaseException): ...

@app.exception_handler(CustomException)
async def custom_exception_handler_example(request: Request, exc: CustomException) -> JSONResponse: ...
```
