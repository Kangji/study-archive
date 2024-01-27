# Hyper

Hyper is a fast and correct HTTP implementation written in and for Rust.
Hyper is a lower-level HTTP library, meant to be a building block for libraries and applications.

## Body

On top of `Body` trait from `http 1.0.0`, hyper also provides the concrete `Incoming` type which implmenets `Body`.
It is a stream of Bytes, used when receiving bodies from the network.

Note that Users should not instantiate this struct directly.
When working with the hyper client, `Incoming` is returned to you in responses.
Similarly, when operating with the hyper server, it is provided within requests.

### Bytes

Hyper uses `Bytes` as a byte representation.
It is a cheaply cloneable and sliceable chunk of contiguous memory.

## Client

Since hyper is a low-level HTTP library, user is in charge of everything, starting from TCP handshake.
Wraps the TCP connection(`tokio::net::TcpStream`) with an adapter (`hyper::rt::TokioIo`).
Then performs the TCP handshake and spawns the task to poll the connection.

```rs
// Open a TCP connection to the remote host
let stream = tokio::net::TcpStream::connect(address).await?;

// Use an adapter to access something implementing `tokio::io` traits as if they implement
// `hyper::rt` IO traits.
let io = hyper_util::rt::TokioIo::new(stream);

// Perform a TCP handshake
let (mut sender, conn) = hyper::client::conn::http1::handshake(io).await?;

// Spawn a task to poll the connection, driving the HTTP state
tokio::task::spawn(async move {
    if let Err(err) = conn.await {
        println!("Connection failed: {:?}", err);
    }
});

// The authority of our URL will be the hostname of the httpbin remote
let authority = url.authority().unwrap().clone();

// Create an HTTP request with an empty body and a HOST header
let req = Request::builder()
    .uri(url)
    .header(hyper::header::HOST, authority.as_str())
    .body(Empty::<Bytes>::new())?;

// Await the response...
let mut res = sender.send_request(req).await?;
```

## Server

Hyper server uses `tower::Service` to serve incoming HTTP connections.
Wraps the incoming connection accepted by listener(`tokio::net::TcpListener`) with an adapter (`hyper::rt::TokioIo`).
Then serves connection using `tower::Service`.

```rs
// We create a TcpListener and bind it to 127.0.0.1:3000
let listener = TcpListener::bind(addr).await?;

// We start a loop to continuously accept incoming connections
loop {
    let (stream, _) = listener.accept().await?;

    // Use an adapter to access something implementing `tokio::io` traits as if they implement
    // `hyper::rt` IO traits.
    let io = TokioIo::new(stream);

    // Spawn a tokio task to serve multiple connections concurrently
    tokio::task::spawn(async move {
        // Finally, we bind the incoming connection to our `hello` service
        // Here, `hello` is `Copy`.
        if let Err(err) = http1::Builder::new()
            .serve_connection(io, hello)
            .await
        {
            println!("Error serving connection: {:?}", err);
        }
    });
}
```
