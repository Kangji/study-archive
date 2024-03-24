# Dependency

FastAPI injects dependencies via `Depends` parameter type and dependency functions.
Like path operation functions, dependency functions are also called by FastAPI to be injected.
Therefore it can be easily inferred that rules for dependency functions are identical to path operation functions.
When dependency function is not provided, then its type, which is expected to be a constructor, is used as dependency function.

```py
async def dependency_example() -> AsyncSession: ...

@app.get('/items/{item_id}')
async def path_operation(session: Annotated[AsyncSession, Depends(dependency_example)], item_id: int): ...
```

When the dependency function returns useless value or nothing,
you can specify the dependency in path operation decorator rather than parameter.

```py
async def dependency_no_return(): ...

@app.get('/', dependencies=[Depends(dependency_no_return)])
```

For every path operation function, each dependency function is called at most once and then cached.

## Sub-Dependency

Dependency function can depend on another dependency function, which is called "sub-dependency".
It is recommended to chain dependencies as much as possible.

## Global Dependency

You can add dependencies at `FastAPI` app or `APIRouter` instances.
Those are applied to every path operations in app or instances.

This is useful for middlewares.

## Generator Dependency

You can use (async) generator as a dependency function.
Generators will yield value rather than return, and keep running at the end of the path operation.
Therefore generator must yield at most once, otherwise raises RuntimeError.

Exception handlers reside outside of dependency function,
so they will exit when the path operation raised exception.
If you handle the exception in generator, it must return `Response` or raise another exception,
otherwise FastAPI raises RuntimeError.

To ensure the execution after yield, you can use `try~finally`,
or (async) context manager also works well.

Generators are nested by the framework, ensures LIFO execution.
