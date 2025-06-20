---
title: Homework Assignments
---

# Preparation: Install All Necessary Tools

Before starting, make sure you have the following installed:

- **Node.js** (https://nodejs.org/)
- **npm** (comes with Node.js)
- **Docker** (https://www.docker.com/products/docker-desktop)
- **A code editor** (e.g., VS Code: https://code.visualstudio.com/)
- **API testing tool** (e.g., [Postman](https://www.postman.com/) or [Bruno](https://www.usebruno.com/))
- **DBeaver Community Edition** (https://dbeaver.io/download/)

## Windows Users: Easy Install with Chocolatey
If you're on Windows, you can use [Chocolatey](https://chocolatey.org/) to install everything easily. Run these commands in an administrator PowerShell:

```powershell
choco install nodejs-lts docker-desktop vscode postman dbeaver
```

## Check Your Installations
- **Node.js & npm:**
  ```sh
  node -v
  npm -v
  ```
- **Docker:**
  ```sh
  docker run hello-world
  ```
  - This should print a welcome message if Docker is working.
- **Postman/Bruno/DBeaver:**
  - Open the app to make sure it launches.

---

# Homework 1: Your First Node.js API

**Goal:** Practice the steps we've learned and write your first code.

## Tasks
1. Create a new project using `npm init`:
   ```sh
   npm init -y
   ```
2. Install Express with npm and create a "Hello World" app:
   ```sh
   npm install express
   ```
   **Example `index.js`:**
   ```js
   const express = require('express');
   const app = express();
   const port = 3000;
   app.get('/', (req, res) => {
     res.send('Hello World!');
   });
   app.listen(port, () => {
     console.log(`Server running at http://localhost:${port}`);
   });
   ```
3. Make the `/` endpoint dynamic: it should return different data each time (e.g., current time or a random emoji).
   **Example:**
   ```js
   app.get('/', (req, res) => {
     const emojis = ['😀', '🚀', '🌟', '🍕', '🐱'];
     const randomEmoji = emojis[Math.floor(Math.random() * emojis.length)];
     res.send(`Current time: ${new Date().toLocaleTimeString()}<br>Random emoji: ${randomEmoji}`);
   });
   ```
4. Create a new endpoint `POST /file` that creates a file in your project folder. The file's content should be the body of your request.
   - Example for creating a file (synchronously):
     ```js
     const fs = require('fs');
     app.use(express.text()); // for plain text bodies
     app.post('/file', (req, res) => {
       fs.writeFileSync('output.txt', req.body);
       res.send('File created!');
     });
     ```
5. Create a Dockerfile and run your project inside a container.
   **Example `Dockerfile`:**
   ```Dockerfile
   FROM node:22
   WORKDIR /app
   COPY package*.json ./
   RUN npm install
   COPY . .
   EXPOSE 3000
   CMD ["node", "index.js"]
   ```
   **Build and run:**
   ```sh
   docker build -t my-node-app .
   docker run -p 3000:3000 my-node-app
   ```

---

# Homework 2: Node.js + Postgres

**Goal:** Practice working with Docker Compose, Postgres, and connecting your Node.js app to a database.

## Tasks
1. Use Docker Compose to run both Node.js and Postgres.
   **Example `docker-compose.yml`:**
   ```yaml
   version: '3.8'
   services:
     app:
       build: .
       ports:
         - "3000:3000"
       environment:
         - DATABASE_URL=postgres://postgres:postgres@db:5432/postgres
       depends_on:
         - db
     db:
       image: postgres:16
       restart: always
       environment:
         POSTGRES_USER: postgres
         POSTGRES_PASSWORD: postgres
         POSTGRES_DB: postgres
       ports:
         - "5432:5432"
   ```
   **Start everything:**
   ```sh
   docker-compose up --build
   ```
2. Create a database table and insert a user.
   **Example (using `pg` library):**
   ```js
   const { Client } = require('pg');
   const client = new Client({ connectionString: process.env.DATABASE_URL });
   async function setup() {
     await client.connect();
     await client.query(`CREATE TABLE IF NOT EXISTS users (id SERIAL PRIMARY KEY, name TEXT NOT NULL, email TEXT UNIQUE NOT NULL);`);
     await client.query('INSERT INTO users (name, email) VALUES ($1, $2) ON CONFLICT (email) DO NOTHING', ['Alice', 'alice@example.com']);
     console.log('User inserted!');
   }
   setup();
   ```
3. Add a new endpoint `GET /users/:id` to your API that fetches a user by ID from the database and returns it in the response.
   **Example:**
   ```js
   app.get('/users/:id', async (req, res) => {
     const { rows } = await client.query('SELECT * FROM users WHERE id = $1', [req.params.id]);
     if (rows.length) {
       res.json(rows[0]);
     } else {
       res.status(404).send('User not found');
     }
   });
   ```
4. Install DBeaver Community Edition and connect to your database.
5. Explore the interface and view your created table and data.

---

Good luck! If you get stuck, check the presentations and console output for hints.
