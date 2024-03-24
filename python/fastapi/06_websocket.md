# WebSocket

Use `WebSocket` parameter type for websocket endpoints.

```py
@app.websocket('/ws')
async def websocket_endpoint(socket: WebSocket): ...
```

## Accept Connection

At the very first you need to accept the connection.

```py
await socket.accept()
```

## Send/Receive API

```py
data_json = await socket.receive_json()
data_text = await socket.receive_text()
data_bytes = await socket.receive_bytes()
await socket.send_json(data_json)
await socket.send_text(data_text)
await socket.send_bytes(data_bytes)
```

### Data Frame

When using json format, you can choose which data frame to contain data with `mode` parameter.
Either `text` or `binary` is accepted, and default is `text`.

```py
data_binary_json = socket.receive_json(mode='binary')
socket.send_json(data, mode='binary')
```

### Iterators

Most common lifetime of websocket is a loop of receive -> send.
This is easily done by `receive` async iterators.
These iterators will exit when `WebSocketDisconnected` is raised.

```py
async for data_text in socket.iter_text(): ...
async for data_json in socket.iter_json(): ...
async for data_bytes in socket.iter_bytes(): ...
```

## Close Connection

Server can also close the connection.

```py
await socket.close()
```

## What's Happening Underneath?

`WebSocket` type internally manages client and application state based on protocol,
which are `CONNECTING`, `CONNECTED` or `DISCONNECTED`.
Initially both client and application state are `CONNECTING`.

Client and application communicates over connection via sending and receiving socket `Message`s.
To initiate the connection, client is expected to send `connect` type message,
and then application is expected to respond with `accept` type message to accept or `close` type to refuse.
To close the connection properly, client is expected to send `disconnect` type message (client side disconnect),
or server is expected to send `close` type message (server side close).
However, keep in mind that the connection could be broken without closing handshake,
for example, when disconnected to network, server tries to send `close` message but raises IOError.

`WebSocket` provides low-level `send` and `receive` method that properly manages the states based on protocol.
When unexpected message is received or about to be sent, the socket will raise `RuntimeError`.

* On client state `CONNECTING`, expects to receive `connect` message
    * `connect` message transforms client state to `CONNECTED`
* On client state `CONNECTED`, expects to receive `receive` or `disconnect` message
    * `receive` message is normal message from client
    * `disconnect` message transforms client state to `DISCONNECTED`
* On app state `CONNECTING`, expects to send `accept` or `close` message
    * `accept` message transforms app state to `CONNECTED`
    * `close` message transforms app state to `DISCONNECTED`
* On app state `CONNECTED`, expects to send `send` or `close` message
    * `send` message is normal message from app
    * `close` message transforms app state to `DISCONNECTED`
    * I/O error transforms app state `DISCONNECTED` and raises `WebSocketDisconnect`

All other APIs are implemented using `send` and `receive`.

* `accept` method waits for `connect` message if client state is `CONNECTING` (`receive()`), then send `accept` message (`send()`).
* `close` method sends `close` message (`send()`).
* All `receive_text`, `receive_json`, `receive_bytes` expect the app state to be `CONNECTED` before `receive()`, and raise `WebSocketDisconnect` on `disconnect` message.
    * `receive_json` additionally validates the mode (`text` or `binary`) before `receive()`,
    then `json.loads()` the data.
    * `receive_text` casts data to `str`.
    * `receive_bytes` casts data to `bytes`.
* All `iter_text`, `iter_json`, `iter_bytes` wrap their corresponding methods:
    ```py
    try:
        while True:
            yield await self.receive_something()
    except WebSocketDisconnect:
        pass
    ```
* All `send_text`, `send_json`, `send_bytes` attach the proper message type and call `send()`.
    * `send_json` additionally validates the mode and `json.dumps()` the data.
