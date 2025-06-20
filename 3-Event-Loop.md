---
title: The Node.js Event Loop
author: Sergei Mertsev
---

# What is the Event Loop?

- The event loop is the core mechanism that allows Node.js to handle many operations concurrently, even though it runs on a single thread.
- It enables non-blocking I/O, making Node.js fast and efficient for I/O-heavy applications.

<!-- end_slide -->
# Why Does the Event Loop Exist?

- Traditional server models (like Apache) use one thread per connection, which can be resource-intensive and hard to scale.
- The event loop allows Node.js to handle thousands of concurrent connections with a single thread, using callbacks and events.
- This design is ideal for applications with many I/O operations (e.g., web servers, APIs).

<!-- end_slide -->
# How Does the Event Loop Work?

- The event loop continuously checks for new events (like completed I/O operations) and executes their callbacks.
- While waiting for I/O, Node.js can process other events, keeping the server responsive.

```js
console.log('Start');
setTimeout(() => {
  console.log('Timeout callback');
}, 0);
console.log('End');
// Output:
// Start
// End
// Timeout callback
```

<!-- end_slide -->
# Event Loop vs. Other Techniques

- **Thread-per-request (e.g., Java, Apache):** Each connection gets its own thread. Good for CPU-bound tasks, but high memory usage and context switching overhead.
- **Event-driven (Node.js):** Single thread, non-blocking I/O. Great for I/O-bound tasks, low memory usage, highly scalable.
- **Async/Await & Promises:** Built on top of the event loop, making async code easier to write and read.

<!-- end_slide -->
# When Not to Use the Event Loop Model

- Not ideal for CPU-intensive tasks (e.g., heavy computations, image processing) because the single thread can get blocked.
- For CPU-bound work, consider offloading to worker threads or using a different platform.

<!-- end_slide -->
# Common Event Loop Mistakes

- Blocking the event loop with heavy computation or synchronous code can make your app unresponsive.

```js
// BAD: Blocking the event loop
app.get('/block', (req, res) => {
  // Simulate heavy computation
  let sum = 0;
  for (let i = 0; i < 1e10; i++) {
    sum += i;
  }
  res.send('Done!');
});
// While this runs, all other requests are blocked!
```

- Always offload CPU-heavy work to worker threads or external services.

<!-- end_slide -->
# Microtasks vs. Macrotasks

- **Macrotasks:** Scheduled by functions like `setTimeout`, `setInterval`, and I/O events.
- **Microtasks:** Scheduled by Promises (`.then`, `async/await`), `process.nextTick`.
- Microtasks are executed after the current operation and before the next macrotask.

```js
console.log('script start');
setTimeout(() => {
  console.log('setTimeout'); // macrotask
}, 0);
Promise.resolve().then(() => {
  console.log('promise'); // microtask
});
process.nextTick(() => {
  console.log('nextTick'); // microtask (runs before Promises)
});
console.log('script end');
// Output:
// script start
// script end
// nextTick
// promise
// setTimeout
```

- `process.nextTick` runs before Promise microtasks in Node.js.
- Microtasks can starve macrotasks if scheduled in a loop!

<!-- end_slide -->
# Summary

- The event loop is what makes Node.js scalable and efficient for I/O-heavy applications.
- It is different from traditional multi-threaded models and is best for handling many simultaneous connections with minimal resources.

<!-- end_slide -->
# Q&A

Thank you!
Any Questions?
