---
title: NodeJS and it's ecosystem
author: Sergei Mertsev
---

# What is Asynchronous JavaScript?

## **Synchronous vs. Asynchronous**

Imagine you're at a coffee shop:

* **Synchronous:** You order your coffee, and then you *wait* right there at the counter until it's made. You can't do anything else (like check your phone or find a seat) until your coffee is in your hand.

* **Asynchronous:** You order your coffee, the barista tells you they'll call your name when it's ready, and you go find a seat, check your phone, or chat with a friend. You're doing *other things* while your coffee is being prepared. When your name is called, you go pick it up.

## **In Programming Terms:**
* **Synchronous code** executes one line after another, waiting for each operation to complete before moving to the next.
* **Asynchronous code** allows certain operations to run in the background without blocking the main program flow. The program continues executing, and when the background operation finishes, it notifies the main program (e.g., through a "callback").

<!-- end_slide -->
# Why Asynchronous in Node.js?

## **Node.js: Single-Threaded Powerhouse**
* Node.js is built on a **single-threaded** execution model. This means it has only one "worker" (thread) to handle all incoming requests.
* Sounds like a limitation, right? Not necessarily! Node.js uses an "Event Loop" to handle many operations efficiently.

## **The Problem: Blocking Operations**
What happens if that single worker gets stuck waiting for something?
* **I/O Operations:** Reading a file from disk, writing to a database.
* **Network Requests:** Fetching data from an external API, communicating with other servers.
* **Heavy Computations:** Complex calculations that take a long time.

If these operations were synchronous, the single worker would be *blocked*. While it's waiting for a file to be read, it can't respond to any other incoming user requests. This makes your application slow and unresponsive.

## **The Solution: Non-Blocking I/O**
* Asynchronous JavaScript allows Node.js to initiate these potentially slow operations and then immediately move on to process other tasks.
* When the slow operation finishes, its result is put back on the "Event Loop" queue, and Node.js will eventually pick it up and process it.
* This makes Node.js highly efficient and scalable, able to handle many concurrent connections without needing many threads.

<!-- end_slide -->
# Key Concepts: Callbacks, Promises, Async/Await

JavaScript has evolved its way of handling asynchronous code.

1.  **Callbacks:** The original way. Functions passed as arguments to be executed later.
2.  **Promises:** Introduced to solve "callback hell" (nested callbacks). A cleaner way to handle asynchronous results.
3.  **Async/Await:** The modern, most readable, and preferred way. Built on top of Promises, it makes asynchronous code look and feel synchronous.

<!-- end_slide -->
# Callbacks (Briefly)

## **The Original Async Pattern**
A callback function is simply a function that is passed as an argument to another function and is executed *after* the other function has completed its operation.

```javascript
// Example using a simulated async operation
function fetchData(callback) {
    setTimeout(() => {
        const data = "Data fetched!";
        callback(data); // Call the callback when data is ready
    }, 2000); // Simulate network delay
}

console.log("Starting data fetch...");
fetchData((result) => {
    console.log(result); // This runs AFTER 2 seconds
});
console.log("Continuing with other tasks...");
```

## **The Problem: "Callback Hell"**
When you have many nested asynchronous operations, callbacks can lead to deeply indented, hard-to-read, and maintain code, often called "Callback Hell" or "Pyramid of Doom."

<!-- end_slide -->
# Promises (Briefly)

## **A Solution to Callback Hell**
Promises represent the eventual completion (or failure) of an asynchronous operation and its resulting value.

* A Promise can be in one of three states:
    * **Pending:** The initial state; neither fulfilled nor rejected.
    * **Fulfilled (Resolved):** The operation completed successfully.
    * **Rejected:** The operation failed.

## **Using Promises**
```javascript
function fetchDataPromise() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            const success = true; // Simulate success/failure
            if (success) {
                resolve("Data fetched successfully with Promise!");
            } else {
                reject("Failed to fetch data!");
            }
        }, 2000);
    });
}

console.log("Starting Promise data fetch...");
fetchDataPromise()
    .then((data) => {
        console.log(data); // Runs on success
    })
    .catch((error) => {
        console.error(error); // Runs on failure
    });
console.log("Continuing with other tasks...");
```

<!-- end_slide -->
# Async/Await: The Modern Way

## **Making Async Code Read Like Sync Code**
`async/await` is syntactic sugar built on top of Promises. It allows you to write asynchronous code that looks and behaves more like synchronous code, making it much easier to read and debug.

* The `async` keyword is placed before a function declaration to denote that the function will contain asynchronous operations. An `async` function implicitly returns a Promise.
* The `await` keyword can only be used inside an `async` function. It pauses the execution of the `async` function until the Promise it's waiting for resolves.

<!-- end_slide -->
# Basic Async/Await Example

```javascript
// A function that returns a Promise (e.g., simulating a network request)
function resolveAfter2Seconds() {
    return new Promise(resolve => {
        setTimeout(() => {
            resolve('Resolved!');
        }, 2000);
    });
}

// *** CORRECTED: Added 'async' keyword here ***
async function asyncCall() {
    console.log('Calling...');
    // 'await' pauses this function until the Promise resolves
    const result = await resolveAfter2Seconds();
    console.log(result); // 'Resolved!' will be printed after 2 seconds
}

console.log("Before async call");
asyncCall();
console.log("After async call, continuing with other code...");

// Output will be:
// Before async call
// Calling...
// After async call, continuing with other code...
// (2 seconds later)
// Resolved!
```

<!-- end_slide -->
# Introducing Express.js

## **What is Express.js?**
* Express.js is a minimal and flexible Node.js web application framework that provides a robust set of features for web and mobile applications.
* It simplifies the process of building web servers, routing HTTP requests, and serving static files.

## **Basic Express App Structure**
```javascript
const express = require('express');
const app = express();
const port = 3000;

// Basic route
app.get('/', (req, res) => {
    res.send('Hello World from Express!');
});

// Start the server
app.listen(port, () => {
    console.log(`Server listening at http://localhost:${port}`);
});
```

<!-- end_slide -->
# The Problem: Blocking in Express.js

Consider an Express route that needs to perform a "long" operation, like a database query or an API call.

```javascript
// DANGER: DON'T DO THIS IN PRODUCTION FOR LONG OPERATIONS!
app.get('/sync-data', (req, res) => {
    console.log('Request received for /sync-data');
    let sum = 0;
    // Simulate a very long, blocking calculation
    for (let i = 0; i < 1000000000; i++) {
        sum += i;
    }
    console.log('Calculation finished for /sync-data');
    res.send(`Synchronous data loaded. Sum: ${sum}`);
});
```
If a user hits `/sync-data`, the entire Node.js server will be **blocked** for several seconds until `sum` is calculated. No other incoming requests can be processed during this time. This is why asynchronous operations are crucial!

<!-- end_slide -->
# Solution: Async/Await in Express.js Routes

## **Making Route Handlers Async**
To use `await` inside an Express route handler, simply mark the route handler function as `async`.

```javascript
const express = require('express');
const app = express();
const port = 3000;

// Simulate a database call that returns a Promise
function fetchUserDataFromDB(userId) {
    return new Promise(resolve => {
        setTimeout(() => {
            console.log(`Fetched data for user ${userId}`);
            resolve({ id: userId, name: `User ${userId}`, email: `user${userId}@example.com` });
        }, 1500); // Simulate 1.5 second database delay
    });
}

// *** CORRECTED: Added 'async' keyword for the route handler ***
app.get('/users/:id', async (req, res) => {
    try {
        const userId = req.params.id;
        console.log(`Request received for user ${userId}`);
        // 'await' pauses this specific request's execution
        // but Node.js is free to handle other requests
        const userData = await fetchUserDataFromDB(userId);
        res.json(userData);
        console.log(`Response sent for user ${userId}`);
    } catch (error) {
        console.error('Error fetching user data:', error);
        res.status(500).send('Internal Server Error');
    }
});

app.get('/', (req, res) => {
    res.send('Welcome! Try navigating to /users/123');
});

app.listen(port, () => {
    console.log(`Server listening at http://localhost:${port}`);
});
```

<!-- end_slide -->
# Error Handling with Async/Await in Express

When using `async/await`, errors (rejections of Promises) can be caught using standard `try...catch` blocks.

```javascript
const express = require('express');
const app = express();
const port = 3000;

function potentiallyFailingAsyncOperation() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            const success = Math.random() > 0.5; // 50% chance of failure
            if (success) {
                resolve("Operation successful!");
            } else {
                reject(new Error("Operation failed unexpectedly!"));
            }
        }, 1000);
    });
}

// *** CORRECTED: Added 'async' keyword for the route handler ***
app.get('/risky-operation', async (req, res) => {
    try {
        const result = await potentiallyFailingAsyncOperation();
        res.send(`Success: ${result}`);
    } catch (error) {
        // Catch any errors (rejected Promises) from awaited functions
        console.error('Caught an error in /risky-operation:', error.message);
        // Send an appropriate error response to the client
        res.status(500).json({ message: 'Failed to perform operation', error: error.message });
    }
});

app.listen(port, () => {
    console.log(`Server listening at http://localhost:${port}`);
});
```
Always wrap your `await` calls in a `try...catch` block to gracefully handle errors that might occur during the asynchronous operation.

<!-- end_slide -->
# Example 1: Simulating an Async Operation

## **Fetching User Data with `setTimeout` and `Promise`**

```javascript
const express = require('express');
const app = express();
const port = 3000;

const mockUsers = [
    { id: 1, name: 'Alice', email: 'alice@example.com' },
    { id: 2, name: 'Bob', email: 'bob@example.com' },
    { id: 3, name: 'Charlie', email: 'charlie@example.com' },
];

function getMockUserById(id) {
    return new Promise(resolve => {
        // Simulate a network delay (e.g., database query)
        setTimeout(() => {
            const user = mockUsers.find(u => u.id === parseInt(id));
            resolve(user); // Resolve with user data or undefined
        }, 1000); // 1 second delay
    });
}

app.get('/api/users/:id', async (req, res) => {
    try {
        const userId = req.params.id;
        const user = await getMockUserById(userId);

        if (user) {
            res.json(user);
        } else {
            res.status(404).json({ message: 'User not found' });
        }
    } catch (error) {
        console.error('Error fetching user:', error);
        res.status(500).json({ message: 'Server error' });
    }
});

```

<!-- end_slide -->
# Example 2: External API Call

## **Fetching Weather Data with `fetch`**

```javascript +line_numbers
const express = require('express');
const app = express();
const port = 3000;

// A dummy API URL (replace with a real one for a live demo)
const DUMMY_WEATHER_API = (city) => `https://jsonplaceholder.typicode.com/posts/1`;

app.get('/api/weather/:city', async (req, res) => {
    try {
        const city = req.params.city;
        console.log(`Fetching weather for ${city}...`);

        // Perform an asynchronous HTTP request using fetch
        const response = await fetch(DUMMY_WEATHER_API(city));

        if (!response.ok) {
            // Handle HTTP errors (e.g., 404, 500)
            throw new Error(`HTTP error! Status: ${response.status}`);
        }

        const data = await response.json(); // Parse the JSON response asynchronously

        // For a real weather API, you'd extract relevant weather info from 'data'
        res.json({ city: city, fetchedData: data }); // Send the fetched data back

        console.log(`Weather data sent for ${city}`);
    } catch (error) {
        console.error('Error fetching weather data:', error);
        res.status(500).json({ message: 'Failed to fetch weather data', error: error.message });
    }
});
```
*Note: For `node-fetch`, install it via `npm install node-fetch@2` for CommonJS compatibility or use Node.js v18+ which has `fetch` built-in.*

<!-- end_slide -->
# Best Practices for Async/Await

* **Always use `try...catch`:** Wrap your `await` calls in `try...catch` blocks to handle rejected Promises and prevent unhandled promise rejections from crashing your Node.js application.
* **Don't Block the Event Loop:** Ensure that any long-running, CPU-intensive computations are not performed directly in the main thread without proper async handling or by offloading them (e.g., to worker threads).
* **`Promise.all()` for Parallel Operations:** If you have multiple independent asynchronous operations that don't depend on each other's results, use `Promise.all()` to run them concurrently and `await` for all of them to complete. This is much faster than awaiting them one by one.

```javascript
// Example: Fetch user and posts concurrently (inside an async function)
async function getUserAndPosts(userId) { // *** IMPORTANT: This function must be async ***
    const [user, posts] = await Promise.all([
        fetchUserData(userId), // Assume fetchUserData returns a Promise
        fetchUserPosts(userId) // Assume fetchUserPosts returns a Promise
    ]);
    return { user, posts };
}
```

* **`Promise.race()` for the Fastest Result:** If you need the result of only the first Promise that resolves or rejects among several, use `Promise.race()`.
* **Error Handling Middleware:** For Express.js, consider setting up a global error handling middleware to catch unhandled async errors that might bubble up.

<!-- end_slide -->
# Summary

* **Asynchronous JavaScript** is fundamental for building performant and scalable applications with Node.js.
* It allows Node.js, despite being single-threaded, to handle many concurrent operations efficiently by performing non-blocking I/O.
* **`async/await`** is the modern, most readable, and powerful way to write asynchronous code, making it appear synchronous while maintaining non-blocking behavior.
* In **Express.js**, marking route handlers as `async` enables the use of `await` for database calls, API requests, and other I/O-bound operations, ensuring your web server remains responsive.
* **Robust error handling** with `try...catch` is essential for stable asynchronous applications.

Embrace `async/await` to write cleaner, more efficient, and more maintainable Node.js applications!

<!-- end_slide -->
# Q&A

### Thank you!
### Any Questions?
