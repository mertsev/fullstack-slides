---
title: Good Project Structure in JavaScript
author: Sergei Mertsev
---

# Why Project Structure Matters

- A good project structure makes your code easier to read, understand, and change.
- It helps new team members quickly find what they need.
- It reduces mistakes and makes your project easier to grow.

<!-- end_slide -->
# Basic Structure Example

Imagine your project is like a well-organized toolbox. Each tool has its own place, so you can find it quickly.

```text
my-app/
├── src/
│   ├── index.js
│   ├── routes/
│   ├── controllers/
│   ├── models/
│   └── utils/
├── tests/
├── package.json
├── .env
└── README.md
```
- `my-app/` is your main project folder.
- Folders inside help you keep different parts of your code separate and tidy.

<!-- end_slide -->
# Key Folders Explained

- `src/` — This is where your main code lives. Think of it as the "engine room" of your app.
  - `routes/` — These files decide what happens when someone visits a certain web address (URL) in your app.
  - `controllers/` — These files contain the main logic for your app, like what to do when someone logs in or creates a post.
  - `models/` — These files describe the shape of your data, like what a "user" or a "post" looks like in your app.
  - `utils/` — These are helper files for small tasks that are used in many places, like formatting dates.
- `tests/` — This folder is for code that checks if your app works correctly. It's like having a checklist to make sure nothing is broken.
- `.env` — This file stores secret settings (like passwords) and other things that might change between computers.
- `README.md` — This is a guide for anyone who wants to use or work on your project. It explains what your app does and how to use it.

<!-- end_slide -->
# Best Practices

- **Keep files small and focused:** Each file should do one thing. This makes it easier to fix problems and add new features.
- **Use clear, consistent names:** Name your files and folders so anyone can guess what's inside.
- **Separate concerns:** Keep different types of code in different places (logic, data, routes, helpers).
- **Store configuration in environment variables:** Use the `.env` file for things like passwords, so you don't accidentally share them.
- **Write tests:** Tests help you catch mistakes before your users do. Keep them in the `tests/` folder.
- **Document your project:** Write a `README.md` so others know how to use your app and what it does.

<!-- end_slide -->
# Example: Express App Structure

If you're building a web server with Express (a popular tool for making web apps in JavaScript), your project might look like this:

```text
my-express-app/
├── src/
│   ├── app.js         # The main file that starts your app
│   ├── routes/        # Handles different web addresses
│   ├── controllers/   # Main logic for each route
│   ├── models/        # Data shapes (like users, posts)
│   └── middleware/    # Special helpers that run before your main logic
├── tests/             # Code that checks your app works
├── public/            # Files like images or styles that the browser can see
├── package.json       # Lists your app's settings and dependencies
└── README.md          # Project guide
```

<!-- end_slide -->
# Scaling Up

- As your app gets bigger, you might split it into smaller parts (modules) or even separate projects (a "monorepo").
- You can use tools like TypeScript to catch mistakes before you run your code.
- Use a linter (like ESLint) and a formatter (like Prettier) to keep your code neat and consistent.
- Use clear commit messages when saving your work, so others know what changed.

<!-- end_slide -->
# Summary

- A tidy project structure helps everyone work faster and with fewer mistakes.
- Follow these tips to keep your project easy to use and grow.
- Always write a guide and tests for your project!

<!-- end_slide -->
# Q&A

Thank you!
Any Questions?
