---
title: NodeJS and its ecosystem
author: Sergei Mertsev
---

# What is Asynchronous JavaScript?

## Synchronous vs. Asynchronous

Imagine you're at a coffee shop:

- **Synchronous:** You order your coffee and wait at the counter until it's made. You can't do anything else until your coffee is ready.
- **Asynchronous:** You order your coffee, the barista tells you they'll call your name, and you go do other things. When your name is called, you pick up your coffee.

## In Programming Terms
- **Synchronous code:** Executes one line after another, waiting for each operation to finish.
- **Asynchronous code:** Allows certain operations to run in the background. The program continues, and when the background operation finishes, it notifies the main program (e.g., via a callback).

<!-- end_slide -->
# Why Asynchronous in Node.js?

## Node.js: Single-Threaded Powerhouse
- Node.js uses a single thread to handle all requests.
- Uses the "Event Loop" to efficiently manage many operations.

## The Problem: Blocking Operations
- If the single worker is blocked (e.g., waiting for a file or network), it can't handle other requests.
- This makes the app slow and unresponsive.

## The Solution: Non-Blocking I/O
- Asynchronous JavaScript lets Node.js start slow operations and move on to other tasks.
- When the operation finishes, its result is handled by the event loop.
- This makes Node.js efficient and scalable.

<!-- end_slide -->
# Key Concepts: Callbacks, Promises, Async/Await

JavaScript has evolved its async handling:

1. **Callbacks:** Functions passed as arguments to be executed later.
2. **Promises:** Cleaner way to handle async results, solves "callback hell".
3. **Async/Await:** Modern, readable, and preferred. Built on Promises.

<!-- end_slide -->
# Callbacks (Briefly)

## The Original Async Pattern
A callback is a function passed to another function to be executed later.

```js
function fetchData(callback) {
    setTimeout(() => {
        const data = "Data fetched!";
        callback(data);
    }, 2000); // Simulate network delay
}

console.log("Starting data fetch...");
fetchData((result) => {
    console.log(result); // Runs after 2 seconds
});
console.log("Continuing with other tasks...");
```

## Callback Hell Example
```js
fetchRandomJoke((joke) => {
    translateJoke(joke, (translatedJoke) => {
        postJoke(translatedJoke, () => {
            console.log("Joke posted!");
        });
    });
});
```

## The Problem: "Callback Hell"
Deeply nested callbacks are hard to read and maintain.

<!-- end_slide -->
# Promises (Briefly)

## A Solution to Callback Hell
Promises represent the eventual completion (or failure) of an async operation.

- **Pending:** Initial state
- **Fulfilled:** Operation completed
- **Rejected:** Operation failed

## Using Promises
```js
function fetchDataPromise() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            const success = true;
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
        console.log(data);
    })
    .catch((error) => {
        console.error(error);
    });
console.log("Continuing with other tasks...");
```

<!-- end_slide -->
# Async/Await: The Modern Way

## Making Async Code Read Like Sync Code
- `async` before a function: the function returns a Promise.
- `await` pauses the function until the Promise resolves.

<!-- end_slide -->
# Basic Async/Await Example

```js
function resolveAfter2Seconds() {
    return new Promise(resolve => {
        setTimeout(() => {
            resolve('Resolved!');
        }, 2000);
    });
}

async function asyncCall() {
    console.log('Calling...');
    const result = await resolveAfter2Seconds();
    console.log(result); // 'Resolved!' after 2 seconds
}

console.log("Before async call");
asyncCall();
console.log("After async call, continuing with other code...");
```

<!-- end_slide -->
# Introducing Express.js

## What is Express.js?
- Minimal and flexible Node.js web framework
- Simplifies building web servers and APIs

## Basic Express App Structure
```js
const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => {
    res.send('Hello World from Express!');
});

app.listen(port, () => {
    console.log(`Server listening at http://localhost:${port}`);
});
```

<!-- end_slide -->
# The Problem: Blocking in Express.js

```js
// DANGER: Don't do this for long operations!
app.get('/sync-data', (req, res) => {
    let sum = 0;
    for (let i = 0; i < 1e9; i++) {
        sum += i;
    }
    res.send(`Synchronous data loaded. Sum: ${sum}`);
});
```
If a user hits `/sync-data`, the server is blocked for several seconds. No other requests are processed during this time.

<!-- end_slide -->
# Solution: Async/Await in Express.js Routes

## Making Route Handlers Async
Mark the route handler as `async` to use `await` inside.

```js
const express = require('express');
const app = express();
const port = 3000;

function fetchUserDataFromDB(userId) {
    return new Promise(resolve => {
        setTimeout(() => {
            resolve({ id: userId, name: `User ${userId}` });
        }, 1500);
    });
}

app.get('/users/:id', async (req, res) => {
    try {
        const userId = req.params.id;
        const userData = await fetchUserDataFromDB(userId);
        res.json(userData);
    } catch (error) {
        res.status(500).send('Internal Server Error');
    }
});

app.listen(port, () => {
    console.log(`Server listening at http://localhost:${port}`);
});
```

<!-- end_slide -->
# Error Handling with Async/Await in Express

Use `try...catch` to handle errors in async route handlers.

```js
app.get('/risky-operation', async (req, res) => {
    try {
        const result = await potentiallyFailingAsyncOperation();
        res.send(`Success: ${result}`);
    } catch (error) {
        res.status(500).json({ message: 'Failed to perform operation', error: error.message });
    }
});
```

<!-- end_slide -->
# Example 1: Simulating an Async Operation

## Fetching User Data with setTimeout and Promise

```js
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
        setTimeout(() => {
            const user = mockUsers.find(u => u.id === parseInt(id));
            resolve(user);
        }, 1000);
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
        res.status(500).json({ message: 'Server error' });
    }
});

app.listen(port, () => {
    console.log(`Server listening at http://localhost:${port}`);
});
```

<!-- end_slide -->
# Example 2: External API Call

## Fetching Weather Data with fetch

```js
const express = require('express');
const app = express();
const port = 3000;

// Node.js v18+ has fetch built-in. For older versions, install node-fetch.
const DUMMY_WEATHER_API = (city) => `https://jsonplaceholder.typicode.com/posts/1`;

app.get('/api/weather/:city', async (req, res) => {
    try {
        const city = req.params.city;
        const response = await fetch(DUMMY_WEATHER_API(city));
        if (!response.ok) {
            throw new Error(`HTTP error! Status: ${response.status}`);
        }
        const data = await response.json();
        res.json({ city, fetchedData: data });
    } catch (error) {
        res.status(500).json({ message: 'Failed to fetch weather data', error: error.message });
    }
});

app.listen(port, () => {
    console.log(`Server listening at http://localhost:${port}`);
});
```
*Note: For Node.js v18+, fetch is built-in. For older versions, run `npm install node-fetch@2` and add `const fetch = require('node-fetch');` at the top.*

<!-- end_slide -->
# Best Practices for Async/Await

- **Always use try...catch:** Handle rejected Promises to prevent crashes.
- **Don't block the event loop:** Offload heavy computations.
- **Promise.all for parallel operations:**

```js
async function getUserAndPosts(userId) {
    const [user, posts] = await Promise.all([
        fetchUserData(userId),
        fetchUserPosts(userId)
    ]);
    return { user, posts };
}
```

- **Promise.race for fastest result:** Use when you only need the first result.
- **Error handling middleware:** Use global error handlers in Express for unhandled async errors.

<!-- end_slide -->
# Summary

- Asynchronous JavaScript is key for performant Node.js apps.
- Node.js handles many concurrent operations with non-blocking I/O.
- `async/await` makes async code readable and maintainable.
- In Express.js, use `async` route handlers for database/API calls.
- Always use robust error handling.

<!-- end_slide -->
# Q&A

Thank you!
Any Questions?
