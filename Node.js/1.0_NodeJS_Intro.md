# Node.js

- [Interview Questions](#interview-questions)

- [Thread-Pool](#thread-pool-libuv), [Thread](#threads), [Worker Threads](#worker-threads), [worker_threads vs child_process](#worker_threads-vs-child_process), [eventloop](#event-loop)

## Introduction

- Node.js is an enviorment for running js code.

- Node.js is a JavaScript runtime environment that is built on Chrome's V8 engine (written in C++). The V8 engine is responsible for compiling JavaScript code into machine code, which the computer's processor can then execute directly.

- The core of this architecture is the **single-threaded Event Loop**, but it's important to understand that this **single [Thread](#threads) doesn't do all the work alone**.

## BTS working of Node.js

1. **The JS Call Stack** :-

   The Node.js app runs on a **Single Main [Thread](#threads)** which has a [Call Stack](#), which is where the synchronous (blocking) JavaScript code is executed.

   - **NOTE:- function call, console.log, or other synchronous operation gets pushed onto this stack and is executed**.

2. **The Asynchronous Nature** :-

    The I/O operations (like reading a file, making a database query, or a network request), are typically slow and would block the entire app if main [Thread](#threads) had to wait for them. Node.js avoids this by using concepts **Asynchronous** and **Non-blocking I/O**

3. **The Event Loop** :-

    When an asynchronous operation is initiated, like ```fs.readFile()```, waiting for these processes to complete would cause **Call Stack to block and freeze the app**. Hence to prevent this , ***the operation is offloaded to a [Thread-Pool](#thread-pool-libuv) which uses underlying C library called Libuv in the base code of Node.js ( i.e. js & c++ )*** (Library for Unix and Windows). The main thread remains unblocked and can handle new incoming requests.

    The Event Loop's job is to continuously check two things:

    - Is the main Call Stack empty?
    - Are there any completed asynchronous operations with callbacks ready to be executed?

4. **The Event Queue (or Callback Queue)** :-

   Once a worker thread from the [Thread-Pool](#thread-pool-libuv) finishes its assigned task, it places the callback function for that task into the Event Queue. The Event Loop then checks this queue. When the Call Stack is empty, the Event Loop takes the first callback from the queue and pushes it onto the Call Stack to be executed.

### Summary

Node.js gives the illusion of being single-threaded to the developer because their JavaScript code is executed on a single main thread. However, it cleverly uses a multi-threaded C++ library (Libuv) in the background to handle asynchronous, non-blocking I/O operations, ensuring the main thread is never blocked.

### **Important :-**

  For CPU-intensive tasks, Node.js architecture has a potential weakness. If a complex calculation is run directly in the main thread, it blocks the Event Loop. To solve this, Node.js introduced the [worker_threads](#worker-threads) module. This allows a developer to manually create and manage their own threads to offload CPU-intensive work.

## Misc Terms

- ### Threads

  - A sequence of instructions that the server needs to perform

  - It runs parallel on the server to provide the information to multiple clients.

- ### Event Loop

  The event loop in Node.js is a mechanism that allows asynchronous tasks to be handled efficiently without blocking the execution of other operations. It:

  - Executes JavaScript synchronously first and then processes asynchronous operations.
  - Delegates heavy tasks like I/O operations, timers, and network requests to the libuv library.
  - Ensures smooth execution of multiple operations by queuing and scheduling callbacks efficiently.

- ### Thread Pool (libuv)

  Used internally by Node.js for non-blocking I/O operations:
  - File system (fs.readFile, fs.writeFile, etc.)
  - DNS (non-cached lookups).
  - Compression (e.g., zlib)
  
  The thread pool size can be change :
  - **process.env.UV_THREADPOOL_SIZE** = 1;

- ### Worker Threads

  In modern JavaScript (especially Node.js), true multithreading can be achieved using:
  - Web Workers in the browser.
  - Worker Threads in Node.js (worker_threads module).
  
  Each worker thread has:
  - Its own event loop
  - Its own JS runtime and memory (unlike cluster, it shares memory via SharedArrayBuffer)

  | Feature               | libuv Thread Pool     | Worker Threads               |
  | --------------------- | --------------------- | ---------------------------- |
  | Used for              | Async I/O, zlib, etc. | Heavy CPU tasks              |
  | Managed by            | Node.js (internally)  | You (developer)              |
  | Max threads (default) | 4 (configurable)      | Unlimited (OS-dependent)     |
  | Shares memory?        | No                    | Yes, via `SharedArrayBuffer` |
  | JS context            | Shared                | Isolated                     |

  ```javascript
  const { Worker } = require('worker_threads');
  new Worker(`
  const { parentPort } = require('worker_threads');
  let sum = 0;
  for (let i = 0; i < 1e9; i++) sum += i;
  parentPort.postMessage(sum);
  `, { eval: true });
  ```

  ### How does worker thread communicate with the main thread?

  1. `Worker` Class:

      On the main thread, you create a new worker thread by instantiating the Worker class. The constructor takes the path to a JavaScript file that will be executed in the new thread. For example: `const myWorker = new Worker('./worker.js')`;

  2. `postMessage()` Method:

      This is the primary method for sending data.
      - The main thread uses `myWorker.postMessage()` to send data to the worker.
      - Inside the worker, the `parentPort.postMessage()` method is used to send data back to the main thread.
      - This is a one-way, asynchronous operation. ***The data is copied from one thread to the other, not shared directly***. This is a crucial detail, as it ***prevents race conditions*** (multiple threads trying to access and modify the same data at the same time) and simplifies memory management.

  3. `on('message', ...)` Event Listener:

      - The main thread listens for messages from the worker using `myWorker.on('message', (message) => { ... });`.
      - Inside the worker, you listen for messages from the main thread using `parentPort.on('message', (message) => { ... });`. The `parentPort` object is a special `MessagePort` that represents the connection to the main thread.

  4. `parentPort` Object:

      This object is only available inside a worker thread. It provides the interface for communicating with the main thread. It's an instance of `MessagePort` and has methods like `postMessage()` and event listeners like `on('message', ...)` and `on('error', ...)`

## Child Processes

- Spawns entirely separate OS processes.

- Can use spawn, fork, exec, or execFile.

- Ideal for running:

  - Shell scripts `(e.g., grep, ffmpeg)`

  - Other Node.js apps

  - CLI tools

- Communicates via IPC channels or stdout/stderr streams.

```js
// Child process ideal for this:
const { exec } = require('child_process');
exec('python3 some_script.py', (err, stdout, stderr) => {
  console.log(stdout);
});
```

## Callbacks

  A callback is simply a function that is passed as an argument to another function, and it is intended to be executed at a later time. The name 'callback' literally means 'call this function back later.'

## Worker_Threads vs Child_Process

| Feature                 | `worker_threads`                       | `child_process`                         |
| ----------------------- | -------------------------------------- | --------------------------------------- |
|                         | The goal is to offload computation to prevent blocking the main Event Loop.| The goal is often to isolate a task or execute a different executable.|
| Thread or Process?      | **Thread** (shares memory)             | **Process** (has its own memory space)  |
| Communication           | Message passing (faster, structured)   | Message passing (via IPC or stdio)      |
| Shared Memory           | ✅ Yes (`SharedArrayBuffer`, `Atomics`) | ❌ No (separate memory)                  |
| Performance (CPU-heavy) | ⚡️ Fast (lower overhead)               | 🐢 Slower (more memory + IPC cost)      |
| Isolation               | ❌ Less (shares resources)              | ✅ Strong (complete OS-level separation) |
| Crashing impact         | Might affect main thread               | Isolated — crash doesn’t affect parent  |
| Use native modules?     | ✅ Yes                                  | ✅ Yes                                   |
| Suited for              | Heavy **CPU-bound** tasks in same app  | Running separate **apps/scripts/tools** |

## Interview Questions

The above material can answer these questions and other similar questions.

1. ### Can you explain the fundamental architecture of Node.js and how it handles concurrency?

2. ### What is the Event Loop in Node.js, and how does it prevent the application from blocking?

3. ### Node.js is often described as being "single-threaded." Is that entirely true, and how does it handle CPU-intensive tasks?

4. ### What problem does the worker_threads module solve in Node.js, and why is it necessary?

5. ### How does a worker_thread communicate with the main thread, and what are the key components involved?
