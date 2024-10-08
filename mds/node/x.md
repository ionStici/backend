### Buffers:

Buffers in Node.js are used to handle binary data. When dealing with streams of data (e.g., files or TCP streams), Node.js uses buffers to efficiently process binary information.

```js
Copy code
const buffer = Buffer.from('Hello, World!');
console.log(buffer.toString()); // Outputs 'Hello, World!'
Buffers allow for handling binary data directly, which is essential for file systems, network communication, and other low-level data management.
```

### Cluster Module:

By default, Node.js runs single-threaded, but you can create multiple instances of the event loop to scale across multi-core systems using the Cluster module. This helps to utilize the full potential of modern processors.

```js
const cluster = require("cluster");
const http = require("http");

if (cluster.isMaster) {
  cluster.fork(); // Create worker processes
} else {
  http
    .createServer((req, res) => {
      res.writeHead(200);
      res.end("Hello World\n");
    })
    .listen(8000);
}
```

### Worker Threads:

Introduced in Node.js 10.5, **worker threads** allow running JavaScript code in parallel, which can be useful for CPU-intensive tasks. Unlike the event loop, worker threads handle computation-heavy processes without blocking the main thread.

```js
const { Worker } = require("worker_threads");
const worker = new Worker("./worker.js");

worker.on("message", (msg) => {
  console.log(msg);
});
```

## The Events Module

Node - event-driven architecture.

Node provides an `EventEmitter` class which we can access by requiring the `events` core module:

```js
let events = require("events");

// Create an instance of the EventEmitter class
let emitter = new events.EventEmitter();
```

Each event emitter instance has an `.on()` method which assigns a listener callback function to a named event. The `.on()` method takes as its first argument the name of the event as a string and, as its second argument, the listener callback function.

Each event emitter instance also has an `.emit()` method which announces a named event has occurred. The `.emit()` method takes as its first argument the name of the event as a string and, as its second argument, the data that should be passed into the listener callback function.

```js
let listener = (data) => {
  console.log(data);
};

emitter.on("new user", listener);

emitter.emit("new user", listener);
```

<br>

## The Error Module

The Node environment’s `error` module (global scope) has all the standard JavaScript errors, as well as the `Error` class for creating new error instances.

- We can generate and throw errors.
- We can use error handling techniques such as `try...catch` statements.

Many asynchronous Node APIs use _error-first callback functions_. Callback functions which have an error as the first expected argument and the data as the second argument. If the asynchronous task results in an error, it will be passed in as the first argument to the callback function. If no error was thrown, the first argument will be `undefined`.

```js
const errorFirstCallback = (err, data) => {
  if (err) {
    console.log(`There WAS an error: ${err}`);
  } else {
    console.log(data);
  }
};
```

In node, we use error-first callbacks in many of its asynchronous APIs, because traditional `try...catch` statements won't work for errors thrown during asynchronous operations.

<br>

## The Buffer Module

The `Buffer` module (global scope) is used to handle binary data.

A `Buffer` object represents a fixed amount of memory that can’t be resized. `Buffer` objects are similar to an array of integers where each element in the array represents a byte of data. The buffer object will have a range of integers from 0 to 255 inclusive.

The Buffer module provides a variety of methods to handle the binary data such as `.alloc() .toString() .from() .concat()`

<br>

**`alloc()`** method creates a new `Buffer` object, it accepts three arguments:

- Size: Required. The size of the buffer
- Fill: Optional. A value to fill the buffer with. Default is 0.
- Encoding: Optional. Default is UTF-8.

```js
const buffer = Buffer.alloc(5);
console.log(buffer); // Ouput: [0, 0, 0, 0, 0]
```

<br>

**`toString()`** method translates the `Buffer` objects into a human-readable string. It accepts 2 optional arguments:

- Encoding: Default is UTF-8.
- Start: The byte offset to begin translating in the Buffer object. Default is 0.
- End: The byte offset to end translating in the Buffer object. Default is the length of the buffer. The start and end of the buffer are similar to the start and end of an array, where the first element is 0 and increments upwards.

```js
const buffer = Buffer.alloc(5, "a");
console.log(buffer.toString()); // Output: aaaaa
```

<br>

**`from()`** method - to create a new `Buffer` object from the specified string, array, of buffer. It accepts 2 arguments:

- Object: Required. An object to fill the buffer with.
- Encoding: Optional. Default is UTF-8.

```js
const buffer = Buffer.from("hello");
console.log(buffer); // Output: [104, 101, 108, 108, 111]
```

<br>

**`concat`** method joins all buffer objects passed in an array into one `Buffer` object. It accepts 2 arguments:

- Array: Required. An array containing `Buffer` objects.
- Length: Optional. Specifies the length of the concatenated buffer.

```js
const bufferConcat = Buffer.concat([buffer1, buffer2]);
```

`concat()` comes in handy because a `Buffer` object can’t be resized.

<br>

<br>

[&larr; Back](./README.md)

# Streams

## Table of Content

- [Introduction](#introduction)
- [Readable Streams using readline module](#readable-streams-using-readline-module)

## Introduction

A **stream** represents a continuous flow of data

In practice, it allows you to read or write data efficiently in chunks instead of loading the entire data into memory. Streams are especially useful when working with large files.

Four types of streams in node.js:

1. **Readable** - a stream from which data can be read (e.g. reading a file).
2. **Writable** - a stream to which data can be written (e.g. writing to a file).
3. **Duplex** - a stream that is both readable and writable (e.g. a TCP socket).
4. **Transform** - a duplex stream that performs a transformation on the data as it is read and written (e.g. data compression).

Streams in Node.js are instances of the `EventEmitter` class, and they emit events such as `data` `end` `error` `finish`, which signal varios stages of the data flow.

<br>

## Readable Streams using readline module

- With `readFile` we can read content of entire files.
- Using `readline` module we can read files sequentially line-by-line.

<div></div>

- Practice known as a **stream**: Processing data sequentially, pece by piece.
- Streaming data is often the preferable method as it consumes less RAM.

### readline

Reading and writing files line-by-line, using the `createInterface()` method from the `leadline` core module.

`createInterface()` returns an `EventEmitter` set up to emit `line` events.

```js
const readline = require("readline");
const fs = require("fs");

const interface = readline.createInterface({
  input: fs.createReadStream("text.txt"),
});

interface.on("line", (fileLine) => console.log(fileLine));
```

1. `readline.createInterface()` with an object containing `input`, will create an interface.
2. We set `input` to `fs.createReadStream("text.txt")` which will create a stream from the `text.txt` file.
3. On the created interface, we attach a `line` event along with a callback. A `line` event will be emitted after each line from the file is read.
4. For each line that `text.txt` file has, a `line` event will be emitted, and the `fileLine` parameter will contain the content from each line.

<br>

## Writeable Streams

We can also write to streams.

We can create a writeable stream to a file using the `fs.createWriteStream()` method.

```js
const fs = require("fs");

const fileStream = fs.createWriteStream("output.txt");

fileStream.write("line one");
fileStream.write("line two");
fielStream.end();
```

Unlike a readable stream, which ends when it has no more data to read, a writable stream could remain open indefinitely. We can indicate the end of a writable stream with the `.end()` method.

<br>

## The Timers Module

`timer` global module

You may already be familiar with some timer functions such as, setTimeout() and setInterval(). Timer functions in Node.js behave similarly to how they work in front-end JavaScript programs, but the difference is that they are added to the Node.js event loop. This means that the timer functions are scheduled and put into a queue. This queue is processed at every iteration of the event loop. If a timer function is executed outside of a module, the behavior will be random (non-deterministic).

The setImmediate() function is often compared with the setTimeout() function. When setImmediate() is called, it executes the specified callback function after the current (poll phase) is completed. The method accepts two parameters: the callback function (required) and arguments for the callback function (optional). If you instantiate multiple setImmediate() functions, they will be queued for execution in the order that they were created.

```js
setImmediate(() => {
  console.log("hi");
});
```

<br>
