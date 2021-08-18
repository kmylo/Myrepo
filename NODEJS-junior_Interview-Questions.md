# Node.js Interview Questions

## Table of Contents

### Core Node.js

| No |  Questions   |
|----|--------------|
| 0. | [What is Node.js?](q-what-is-node.js?) |
| 1. | [What are the core dependencies of Node.js? (v8 + libuv)](q1-what-are-the-core-dependencies-of-node.js?)|
| 2. | [What is the Event loop and how does it work? Why should we avoid blocking the Event loop?](q2-what-is-the-event-loop-and-how-does-it-work?-why-should-we-avoid-blocking-the-event-loop?)|
| 3. | [When to use Node.js? (Examples of servers, avoid huge CPU load)](q3-when-to-use-node.js?)|
| 4. | [What is EventEmitter module? What are its features?](q4-what-is-eventemitter-module?-what-are-its-features?)|
| 5. | [Streams. Difference between readable, writeable, duplex and transform streams.](q5-streams.-difference-between-readable,-writeable,-duplex-and-transform-streams.)|
| 6. | [Buffers](q6-buffers)|
| 7. | [Cluster module (basic information on what it is and what it is for?)](q7-cluster-module)|
| 8. | [Worker threads (vs cluster)](q8-worker-threads)|
| 9. | [What is npm? What are package.json and package-lock.json for?](q9-what-is-npm?-what-are-package.json-and-package-lock.json-for?)|

### Express

| No |  Questions   |
|----|--------------|
| 1. | [What is Express.js?](q1-what-is-express.js?)|
| 2. | [What are middlewares?](q2-what-are-middlewares?)|
| 3. | [Why do we need global error handler in our express app?](q3-why-do-we-need-global-error-handler-in-our-express-app?)|
| 4. | [Where should we store db passwords and other secrets? What about configs?](q4-where-should-we-store-db-passwords-and-other-secrets?-what-about-configs?)|
| 5. | [What are CORS errors?](q5-what-are-cors-errors?)|
| 6. | [What are the different types of authentication? How to implement JWT auth in Express?](q6-what-are-the-different-types-of-authentication?-how-to-implement-jwt-auth-in-express?)|
| 7. | [What alternative libraries for Express exist?](q7-what-alternative-libraries-for-express-exist?)|

### Database

| No |  Questions   |
|----|--------------|
| 1. | [Questions about DB that interviewee used in this course (overall information, simple operations, indexes)](q1-questions-about-db-that-interviewee-used-in-this-course)|
| 2. | [How would you explain the recent rise of NoSQL’s popularity?](q2-how-would-you-explain-the-recent-rise-of-nosql’s-popularity?)|
| 3. | [When would you use a document database like MongoDB instead of a relational database like MySQL or PostgreSQL?](q3-when-would-you-use-a-document-database-like-mongodb-instead-of-a-relational-database-like-mysql-or-postgresql?)|
| 4. | [How would you find the most expensive queries in an application?](q4-how-would-you-find-the-most-expensive-queries-in-an-application?)|

### WebSocket

| No |  Questions   |
|----|--------------|
| 1. | [What is WebSocket? When should we use it?](q1-what-is-websocket?-when-should-we-use-it?)|
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
  - [npm](https://nodejs.org/en/docs/meta/topics/dependencies/#npm)
  - [gyp](https://nodejs.org/en/docs/meta/topics/dependencies/#gyp)
  - [gtest](https://nodejs.org/en/docs/meta/topics/dependencies/#gtest)
  Native code can be tested , allows testing C/C++

<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

#### Q2. ***What is the Event loop and how does it work? Why should we avoid blocking the Event loop?***

The [event loop](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/) is what allows Node.js to perform non-blocking I/O operations — despite the fact that JavaScript is single-threaded — by offloading operations to the system kernel whenever possible.

Since most modern kernels are multi-threaded, they can handle multiple operations executing in the background. When one of these operations completes, the kernel tells Node.js so that the appropriate callback may be added to the **poll** queue to eventually be executed. We'll explain this in further detail later in this topic.

The following diagram shows a simplified overview of the event loop's order of operations.

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

*ToDo* --> Node.js is an alalala

<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

#### Q7. ***Cluster module (basic information on what it is and what it is for?)***

*ToDo* --> Node.js is an alalala

<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

#### Q8. ***Worker threads (vs cluster)***

*ToDo* --> Node.js is an alalala

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


