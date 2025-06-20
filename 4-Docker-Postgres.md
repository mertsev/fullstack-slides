---
title: Docker for Node.js Projects
author: Sergei Mertsev
---

# What is Docker?

- Docker is a platform for developing, shipping, and running applications in lightweight, portable containers.
- Containers package your code, runtime, system tools, and libraries, ensuring consistency across environments.

<!-- end_slide -->
# Why Use Docker?

- "It works on my machine!" is no longer a problem.
- Consistent environments for development, testing, and production.
- Easy to share and deploy applications.

<!-- end_slide -->
# Containerizing a Node.js Project

## 1. Create a `Dockerfile`
```Dockerfile
# Use official Node.js image (latest LTS)
FROM node:22

# Set working directory
WORKDIR /app

# Copy package files and install dependencies
COPY package*.json ./
RUN npm install

# Copy the rest of the app
COPY . .

# Expose port (change if your app uses a different port)
EXPOSE 3000

# Start the app
CMD ["npm", "start"]
```

<!-- end_slide -->
# Building and Running the Container

```sh
# Build the Docker image
docker build -t my-node-app .

# Run the container
docker run -p 3000:3000 my-node-app
```
- Your Node.js app is now running in a container!

<!-- end_slide -->
# Using Docker Compose with Postgres

- Docker Compose lets you define and run multi-container applications.
- Example: Node.js app + Postgres database.

## 1. Create a `docker-compose.yml`
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

<!-- end_slide -->
# Running with Docker Compose

```sh
docker-compose up --build
```
- Both your Node.js app and Postgres database will start and be networked together.

<!-- end_slide -->
# Connecting Node.js to Postgres

- Use a library like `pg` in your Node.js app:

```js
const { Client } = require('pg');
const client = new Client({
  connectionString: process.env.DATABASE_URL,
});
client.connect();
```
- The `DATABASE_URL` is set by Docker Compose and points to the Postgres service.

<!-- end_slide -->
# Example: Creating a Table in Postgres from Node.js

- Use the `pg` library to connect and execute SQL commands.

```js
const { Client } = require('pg');
const client = new Client({
  connectionString: process.env.DATABASE_URL,
});

async function setup() {
  await client.connect();
  await client.query(`
    CREATE TABLE IF NOT EXISTS users (
      id SERIAL PRIMARY KEY,
      name TEXT NOT NULL,
      email TEXT UNIQUE NOT NULL
    );
  `);
  console.log('Table created or already exists');
}

setup();
```

<!-- end_slide -->
# Example: Inserting and Fetching Data

```js
async function insertAndFetch() {
  // Insert a user
  await client.query(
    'INSERT INTO users (name, email) VALUES ($1, $2) ON CONFLICT (email) DO NOTHING',
    ['Alice', 'alice@example.com']
  );
  // Fetch all users
  const res = await client.query('SELECT * FROM users');
  console.log(res.rows);
}

insertAndFetch();
```
- This will insert a user and print all users in the table.

<!-- end_slide -->
# Summary

- Docker makes it easy to containerize and deploy Node.js apps.
- Docker Compose lets you run your app with dependencies like Postgres in one command.
- Use environment variables for configuration and keep your setup portable!

<!-- end_slide -->
# Q&A

Thank you!
Any Questions?
