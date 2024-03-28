# Testing

In order to test routes of FastAPI app, you can use `TestClient`.

```py
def test_root():
    client = TestClient(app)
    response = client.get('/')
    assert response.status_code == 200
    assert response.json() == {'msg': 'Hello, FastAPI!'}
```

## WebSocket

```py
def test_socket():
    client = TestClient(app)
    with client.websocket_connect('/ws') as socket:
        data = socket.receive_json()
        assert data['msg'] == 'Hello, FastAPI!'
```

## Lifespan

`TestClient` is also a context manager: it will run its lifespan events.

```py
lifespan = False

@asynccontextmanager
async def lifespan(app: FastAPI):
    lifespan = True

app = FastAPI(lifespan=lifespan)

def test_lifespan():
    with TestClient(app) as client:
        assert lifespan
```

## Mock Dependencies

You can add mock dependency function to `app.dependency_overrides`, which is a key-value store of `<original>:<mock>`.
It will override your original dependency function, but don't forget to reset after use.

```py
app.dependency_overrides['original_dependency'] = mock_dependency
```
