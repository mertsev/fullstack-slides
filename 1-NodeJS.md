---
title: NodeJS and it's ecosystem
author: Sergei Mertsev
---

Our goals
---

### **You should understand what NodeJS is and the reason it exists**

---

# What is NodeJS?
- NodeJS is a JavaScript runtime built on Chrome's V8 engine.
- It allows you to run JavaScript outside the browser.
- Used for building scalable network applications.

---

# Why does NodeJS exist?
- JavaScript was originally only for browsers.
- NodeJS enables server-side JavaScript.
- Non-blocking, event-driven architecture for high performance.

---

# Hello World Example
```js
// hello.js
console.log('Hello, NodeJS!');
```

**Run in terminal:**
```sh
node hello.js
```

---

### **You should be able to start using npm packages in your projects**

---

# What is npm?
- npm = Node Package Manager
- Used to install and manage packages (libraries)

---

# Initializing a Project
```sh
npm init -y
```
- Creates a `package.json` file

---

# Installing a Package
```sh
npm install lodash
```
- Installs the `lodash` utility library

---

# Using an npm Package
```js
// index.js
const _ = require('lodash');
console.log(_.capitalize('nodejs is awesome!'));
```

**Run in terminal:**
```sh
node index.js
```

---

### **You should be able to run nodejs projects yourself**

---

# Running a NodeJS Project
1. Clone or download the project
2. Install dependencies:
```sh
npm install
```
3. Run the project:
```sh
node index.js
```

---

# Useful NodeJS Commands
- `node <file>`: Run a JavaScript file
- `npm install <package>`: Add a package
- `npm start`: Run the start script (if defined in package.json)

---

The end
---
