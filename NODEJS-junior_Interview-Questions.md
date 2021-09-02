# Node.js Interview Questions

## Table of Contents

### Core Node.js

| No |  Questions   |
|----|--------------|
| 0. | [What is Node.js?](#q-what-is-nodejs) |
| 1. | [What are the core dependencies of Node.js? (v8 + libuv)](#q1-what-are-the-core-dependencies-of-nodejs-v8--libuv)|
| 2. | [What is the Event loop and how does it work? Why should we avoid blocking the Event loop?](#q2-what-is-the-event-loop-and-how-does-it-work-why-should-we-avoid-blocking-the-event-loop)|
| 3. | [When to use Node.js? (Examples of servers, avoid huge CPU load)](#q3-when-to-use-nodejs-examples-of-servers-avoid-huge-cpu-load)|
| 4. | [What is EventEmitter module? What are its features?](#q4-what-is-eventemitter-module-what-are-its-features)|
| 5. | [Streams. Difference between readable, writeable, duplex and transform streams.](#q5-streams-difference-between-readable-writeable-duplex-and-transform-streams)|
| 6. | [Buffers](#q6-buffers)|
| 7. | [Cluster module (basic information on what it is and what it is for?)](#q7-cluster-module-basic-information-on-what-it-is-and-what-it-is-for)|
| 8. | [Worker threads (vs cluster)](#q8-worker-threads-vs-cluster)|
| 9. | [What is npm? What are package.json and package-lock.json for?](#q9-what-is-npm-what-are-packagejson-and-package-lockjson-for)|

### Express

| No |  Questions   |
|----|--------------|
| 1. | [What is Express.js?](#q1-what-is-expressjs)|
| 2. | [What are middlewares?](#q2-what-are-middlewares)|
| 3. | [Why do we need global error handler in our express app?](#q3-why-do-we-need-global-error-handler-in-our-express-app)|
| 4. | [Where should we store db passwords and other secrets? What about configs?](#q4-where-should-we-store-db-passwords-and-other-secrets?-what-about-configs)|
| 5. | [What are CORS errors?](#q5-what-are-cors-errors)|
| 6. | [What are the different types of authentication? How to implement JWT auth in Express?](#q6-what-are-the-different-types-of-authentication?-how-to-implement-jwt-auth-in-express)|
| 7. | [What alternative libraries for Express exist?](#q7-what-alternative-libraries-for-express-exist)|

### Database

| No |  Questions   |
|----|--------------|
| 1. | [Questions about DB that interviewee used in this course (overall information, simple operations, indexes)](#q1-questions-about-db-that-interviewee-used-in-this-course)|
| 2. | [How would you explain the recent rise of NoSQL’s popularity?](q2-how-would-you-explain-the-recent-rise-of-nosql’s-popularity?)|
| 3. | [When would you use a document database like MongoDB instead of a relational database like MySQL or PostgreSQL?](q3-when-would-you-use-a-document-database-like-mongodb-instead-of-a-relational-database-like-mysql-or-postgresql?)|
| 4. | [How would you find the most expensive queries in an application?](q4-how-would-you-find-the-most-expensive-queries-in-an-application?)|

### WebSocket

| No |  Questions   |
|----|--------------|
| 1. | [What is WebSocket? When should we use it?](#q1-what-is-websocket?-when-should-we-use-it?)|
| 2. | [Socket.io: What happens if client/server does not support websocket transport type?](q2-socket.io:-what-happens-if-client/server-does-not-support-websocket-transport-type?)|
| 3. | [Alternative libraries](q3-alternative-libraries)|

---

### Glosary

* Node JS
* V8
* LibUV
* EventLoop
* Server
* Web Server
* EventEmitter
* Streams
* Buffer
* Cluster module
* Worker threads
* NPM
* REPL (read-evaluate-print-loop
* Module A file(or a folder that contains code)

---

### ANSWERS:

#### * Core Node.js

#### Q. ***What is Node.js?***

Node.js is an open-source server side runtime environment built on Chrome\'s V8 JavaScript engine. It provides an event driven, non-blocking (asynchronous) I/O and cross-platform runtime environment for building highly scalable server-side applications using JavaScript.

<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

#### Q1. ***What are the core dependencies of Node.js? (v8 + libuv)***

- Libraries
  - [V8](https://nodejs.org/en/docs/meta/topics/dependencies/#v8)
  library provides Node.js with a JavaScript engine, controls via the V8 C++ API
  - [libuv](https://nodejs.org/en/docs/meta/topics/dependencies/#libuv)
  C library that is used to abstract non-blocking I/O operations, handle file system, DNS, network, child processes, pipes, signal handling, polling and streaming. It also includes a thread pool for offloading work for some things that can't be done asynchronously at the operating system level.
  - [llhttp](https://nodejs.org/en/docs/meta/topics/dependencies/#llhttp)
  HTTP parsing is handled by a lightweight TypeScript and C library called llhttp
  - [c-ares](https://nodejs.org/en/docs/meta/topics/dependencies/#c-ares) 
  enables performing asynchronous DNS queries
  - [OpenSSL](https://nodejs.org/en/docs/meta/topics/dependencies/#openssl) 
  is used mostly in the TLS encripted modules it provides implementation for cryptographic functions
  - [zlib](https://nodejs.org/en/docs/meta/topics/dependencies/#zlib) 
  fast sync and streaming compresion and decompresion interfaces
- Tools
  - [npm](https://nodejs.org/en/docs/meta/topics/dependencies/#npm) package manager
  - [gyp](https://nodejs.org/en/docs/meta/topics/dependencies/#gyp) is a tool which compiles Node.js Addons. Node.js Addons are native Node.js Modules, written in C or C++, which therefore need to be compiled on your machine. After they are compiled with tools like node-gyp, their functionality can be accessed via require(), just as any other Node.js Module.
  - [gtest](https://nodejs.org/en/docs/meta/topics/dependencies/#gtest)
  Native code can be tested , allows testing C/C++

<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

#### Q2. ***What is the Event loop and how does it work? Why should we avoid blocking the Event loop?***

The [event loop](https://nodejs.dev/learn/the-nodejs-event-loop) is what allows Node.js to perform non-blocking I/O operations — despite the fact that JavaScript is single-threaded — by offloading operations to the system kernel whenever possible.

Since most modern kernels are multi-threaded, they can handle multiple operations executing in the background. When one of these operations completes, the kernel tells Node.js so that the appropriate callback may be added to the **poll** queue to eventually be executed. We'll explain this in further detail later in this topic.

The following diagram shows a simplified overview of the [event loop's]((https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick)) order of operations.

```
   ┌───────────────────────────┐
┌─>│           timers          │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │     pending callbacks     │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │       idle, prepare       │
│  └─────────────┬─────────────┘      ┌───────────────┐
│  ┌─────────────┴─────────────┐      │   incoming:   │
│  │           poll            │<─────┤  connections, │
│  └─────────────┬─────────────┘      │   data, etc.  │
│  ┌─────────────┴─────────────┐      └───────────────┘
│  │           check           │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
└──┤      close callbacks      │
   └───────────────────────────┘
```

> Each box will be referred to as a "phase" of the event loop.

*ToDo* --> AVOID BLOCKING Event loop!!!

<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

#### Q3. ***When to use Node.js? (Examples of servers, avoid huge CPU load)***

1. If your server side code requires very few cpu cycles. In other world you are doing non blocking operation and does not have heavy algorithm/Job which consumes lots of CPU cycles.
2. If you are from Javascript back ground and comfortable in writing Single Threaded code just like client side JS.

<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

#### Q4. ***What is EventEmitter module? What are its features?***

`EventEmitter` is a class that helps us create a publisher-subscriber pattern in NodeJS.

Is part of the system [`events` module](https://nodejs.org/api/events.html).

With an event emitter, we can simply raise a new event from a different part of an application, and a listener will listen to the raised event and have some action performed for the event.

You initialize that using

```js
JS
const EventEmitter = require('events')
const eventEmitter = new EventEmitter()
```

This object exposes, among many others, the `on` and `emit` methods.

- `emit` is used to trigger an event
- `on` is used to add a callback function that's going to be executed when the event is triggered

**Callback**: Use a callback if you just want to execute some code at a certain time and you don't need to emit success or failure.

**EventEmitter**: Use this if your object emits lots of types of events.

Event emitting business is a lot more flexible than single callbacks. You can do this *.on* operation multiple times. This gives you the flexibility of defining different behaviors in different functions in response to a single event.

<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

#### Q5. ***Streams. Difference between readable, writeable, duplex and transform streams.***

They are a way to handle reading/writing files, network communications, or any kind of end-to-end information exchange in an efficient way.
Streams are not a concept unique to Node.js. They were introduced in the Unix operating system decades ago, and programs can interact with each other passing streams through the pipe operator (|).

For example, in the traditional way, when you tell the program to read a file, the file is read into memory, from start to finish, and then you process it.

Using streams you read it piece by piece, processing its content without keeping it all in memory.
The Node.js stream module provides the foundation upon which all streaming APIs are built. All streams are instances of EventEmitter.

##### Different types of streams

There are four classes of streams:

- `Readable`: a stream you can pipe from, but not pipe into (you can receive data, but not send data to it). When you push data into a readable stream, it is buffered, until a consumer starts to read the data.
- `Writable`: a stream you can pipe into, but not pipe from (you can send data, but not receive from it)
- `Duplex`: a stream you can both pipe into and pipe from, basically a combination of a Readable and Writable stream
- `Transform`: a Transform stream is similar to a Duplex, but the output is a transform of its input

##### An example of a stream

A typical example is reading files from a disk.

Using the Node.js `fs` module, you can read a file, and serve it over HTTP when a new connection is established to your HTTP server:

```js
JS
const http = require("http");
const fs = require("fs");
const server = http.createServer(function (req, res) {
  fs.readFile(__dirname + "/data.txt", (err, data) => {
    res.end(data);
  });
});
server.listen(3000);

```

`readFile()` reads the full contents of the file, and invokes the callback function when it's done.

`res.end(data)` in the callback will return the file contents to the HTTP client.

If the file is big, the operation will take quite a bit of time. Here is the same thing written using streams:

```js
JS
const http = require("http");
const fs = require("fs");
const server = http.createServer((req, res) => {
  const stream = fs.createReadStream(__dirname + "/data.txt");
  stream.pipe(res);
});
server.listen(3000);

```

Instead of waiting until the file is fully read, we start streaming it to the HTTP client as soon as we have a chunk of data ready to be sent.

##### pipe()

The above example uses the line `stream.pipe(res)`: the `pipe()` method is called on the file stream.

What does this code do? It takes the source, and pipes it into a destination.

You call it on the source stream, so in this case, the file stream is piped to the HTTP response.

The return value of the `pipe()` method is the destination stream, which is a very convenient thing that lets us chain multiple `pipe()` calls, like this:

```js
JS
src.pipe(dest1).pipe(dest2)
```

This construct is the same as doing

```js
JS
src.pipe(dest1)
dest1.pipe(dest2)
```

##### Streams-powered Node.js APIs

Due to their advantages, many Node.js core modules provide native stream handling capabilities, most notably:

- `process.stdin` returns a stream connected to stdin
- `process.stdout` returns a stream connected to stdout
- `process.stderr` returns a stream connected to stderr
- `fs.createReadStream()` creates a readable stream to a file
- `fs.createWriteStream()` creates a writable stream to a file
- `net.connect()` initiates a stream-based connection
- `http.request()` returns an instance of the http.ClientRequest class, which is a writable stream
- `zlib.createGzip()` compress data using gzip (a compression algorithm) into a stream
- `zlib.createGunzip()` decompress a gzip stream.
- `zlib.createDeflate()` compress data using deflate (a compression algorithm) into a stream
- `zlib.createInflate()` decompress a deflate stream

##### Why streams?

Streams basically provide two major advantages over using other data handling methods:

- **Memory efficiency**: you don't need to load large amounts of data in memory before you are able to process it
- **Time efficiency**: it takes way less time to start processing data, since you can start processing as soon as you have it, rather than waiting till the whole data payload is available


A stream is an abstract interface for working with streaming data in Node.js. The `stream` module provides an API for implementing the stream interface.

There are many stream objects provided by Node.js. For instance, a [request to an HTTP server](https://nodejs.org/api/http.html#http_class_http_incomingmessage) and [`process.stdout`](https://nodejs.org/api/process.html#process_process_stdout) are both stream instances.

Streams can be readable, writable, or both. All streams are instances of [`EventEmitter`](https://nodejs.org/api/events.html#events_class_eventemitter).

To access the `stream` module:

```js
const stream = require('stream');
```

The `stream` module is useful for creating new types of stream instances. It is usually not necessary to use the `stream` module to consume streams.

<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

#### Q6. ***Buffers.***

##### What is a buffer?

A [buffer](https://nodejs.org/api/buffer.html) is an area of memory. Most JavaScript developers are much less familiar with this concept, compared to programmers using a system programming languages (like C, C++, or Go), which interact directly with memory every day.

- (not available in browser's JavaScript). 
- Buffer is mainly used to store binary data, while reading from a file or receiving packets over the network.

It represents a fixed-size chunk of memory (can't be resized) allocated outside of the V8 JavaScript engine.

You can think of a buffer like an array of integers, which each represent a byte of data.

It is implemented by the Node.js [Buffer class](https://nodejs.org/api/buffer.html).



##### Why do we need a buffer?

Buffers were introduced to help developers deal with binary data, in an ecosystem that traditionally only dealt with strings rather than binaries.

Buffers in Node.js are not related to the concept of buffering data. That is what happens when a stream processor receives data faster than it can digest.

##### How to create a buffer

A buffer is created using the [`Buffer.from()`](https://nodejs.org/api/buffer.html#buffer_buffer_from_buffer_alloc_and_buffer_allocunsafe), [`Buffer.alloc()`](https://nodejs.org/api/buffer.html#buffer_class_method_buffer_alloc_size_fill_encoding), and [`Buffer.allocUnsafe()`](https://nodejs.org/api/buffer.html#buffer_class_method_buffer_allocunsafe_size) methods.

```js
JS
const buf = Buffer.from('Hey!')
```

- [`Buffer.from(array)`](https://nodejs.org/api/buffer.html#buffer_class_method_buffer_from_array)
- [`Buffer.from(arrayBuffer[, byteOffset[, length\]])`](https://nodejs.org/api/buffer.html#buffer_class_method_buffer_from_arraybuffer_byteoffset_length)
- [`Buffer.from(buffer)`](https://nodejs.org/api/buffer.html#buffer_class_method_buffer_from_buffer)
- [`Buffer.from(string[, encoding\])`](https://nodejs.org/api/buffer.html#buffer_class_method_buffer_from_string_encoding)

You can also just initialize the buffer passing the size. This creates a 1KB buffer:

```js
JS
const buf = Buffer.alloc(1024)
//or
const buf = Buffer.allocUnsafe(1024)
```

While both `alloc` and `allocUnsafe` allocate a `Buffer` of the specified size in bytes, the `Buffer` created by `alloc` will be *initialized* with zeroes and the one created by `allocUnsafe` will be *uninitialized*. This means that while `allocUnsafe` would be quite fast in comparison to `alloc`, the allocated segment of memory may contain old data which could potentially be sensitive.

Older data, if present in the memory, can be accessed or leaked when the `Buffer` memory is read. This is what really makes `allocUnsafe` unsafe and extra care must be taken while using it.

<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

#### Q7. ***Cluster module (basic information on what it is and what it is for?)***

A single instance of Node.js runs in a single thread. To take advantage of multi-core systems, the user will sometimes want to launch a cluster of Node.js processes to handle the load.

The [cluster](https://nodejs.org/api/cluster.html) module allows easy creation of child processes that all share server ports.

<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

#### Q8. ***Worker threads (vs cluster)***

The `worker_threads` module enables the use of threads that execute JavaScript in parallel. To access it:

```js
const worker = require('worker_threads');
```

Workers (threads) are useful for performing CPU-intensive JavaScript operations. They do not help much with I/O-intensive work. The Node.js built-in asynchronous I/O operations are more efficient than Workers can be.

Unlike `child_process` or `cluster`, `worker_threads` can share memory. They do so by transferring `ArrayBuffer` instances or sharing `SharedArrayBuffer` instances.

<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

#### Q9. ***What is npm? What are package.json and package-lock.json for?***

*ToDo* --> Node.js is an alalala

<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

---

#### Q1. ***llll***

*ToDo* --> Node.js is an alalala

<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>


