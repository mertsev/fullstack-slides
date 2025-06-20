---
title: TypeScript, Linters, and Transpilers
author: Sergei Mertsev
---

# What is TypeScript?

- TypeScript is a programming language built on top of JavaScript.
- It adds "types" to JavaScript, helping you catch mistakes before you run your code.
- TypeScript code looks a lot like JavaScript, but you can say what kind of data each variable should hold.

## Example: JavaScript vs. TypeScript

**JavaScript:**
```js
function greet(name) {
  return 'Hello, ' + name;
}
```

**TypeScript:**
```ts
function greet(name: string): string {
  return 'Hello, ' + name;
}
```
- Here, `name: string` means the function expects a string, and `: string` after the parentheses means it returns a string.
- If you try to call `greet(123)`, TypeScript will warn you before running the code.

## More Complex TypeScript Example

```ts
interface User {
  id: number;
  name: string;
  email?: string; // Optional property
}

function getUserName(user: User): string {
  return user.name;
}

const user: User = { id: 1, name: 'Alice' };
console.log(getUserName(user));
```

**Class and Generics Example:**
```ts
class Stack<T> {
  private items: T[] = [];

  push(item: T) {
    this.items.push(item);
  }

  pop(): T | undefined {
    return this.items.pop();
  }
}

const numberStack = new Stack<number>();
numberStack.push(42);
console.log(numberStack.pop());
```

<!-- end_slide -->
# What is a Linter?

- A linter is a tool that checks your code for common mistakes and style problems.
- It helps you write clean, consistent code that follows best practices.
- Linters can catch things like unused variables, confusing code, or code that might cause bugs.

## Example: ESLint

**Code with issues:**
```js
function add(a, b) {
  if(a = b) {
    return a + b
  }
}
```
- Issues:
  - Used `=` instead of `==` or `===` (assignment instead of comparison)
  - Missing semicolon
  - Function may not return a value in all cases
  - No space after `if`

A linter will warn you about all these problems, helping you fix them before they cause bugs.

**Good code (after linting):**
```js
function add(a, b) {
  if (a === b) {
    return a + b;
  }
  return null;
}
```

<!-- end_slide -->
# Why Use a Linter?

- Makes your code easier to read for you and your teammates.
- Helps prevent bugs caused by typos or bad practices.
- Many teams use linters to keep everyone's code looking the same.

<!-- end_slide -->
# What is a Transpiler?

- A transpiler is a tool that changes code from one language or version to another.
- TypeScript is not understood by browsers directly, so it must be "transpiled" to JavaScript first.
- Transpilers can also help you use new JavaScript features in older browsers.

## Example: TypeScript to JavaScript

**TypeScript:**
```ts
let age: number = 30;
```

**After transpiling to JavaScript:**
```js
let age = 30;
```

**Transpiling an Interface Example:**
```ts
interface User {
  id: number;
  name: string;
}
const user: User = { id: 1, name: 'Alice' };
```
**After transpiling:**
```js
var user = { id: 1, name: 'Alice' };
```

**Transpiling a Class with Generics Example:**
```ts
class Stack<T> {
  private items: T[] = [];
  push(item: T) { this.items.push(item); }
  pop(): T | undefined { return this.items.pop(); }
}
const numberStack = new Stack<number>();
numberStack.push(42);
```
**After transpiling:**
```js
var Stack = /** @class */ (function () {
  function Stack() { this.items = []; }
  Stack.prototype.push = function (item) { this.items.push(item); };
  Stack.prototype.pop = function () { return this.items.pop(); };
  return Stack;
}());
var numberStack = new Stack();
numberStack.push(42);
```

<!-- end_slide -->
# Why is TypeScript Useful in Bigger Projects?

- **Type safety:** Helps catch bugs before running the code, especially in large codebases with many developers.
- **Better tooling:** Editors can provide autocompletion, refactoring, and documentation based on types.
- **Easier refactoring:** Types help you safely change code without breaking other parts.
- **Self-documenting:** Types make it clear what each function and object expects, making it easier for new team members to understand the code.

<!-- end_slide -->
# Summary

- **TypeScript** adds types to JavaScript, helping you catch mistakes early.
- **Linters** check your code for errors and style issues.
- **Transpilers** convert your code so it works everywhere.
- Using these tools together makes your code safer, cleaner, and easier to maintain!

<!-- end_slide -->
# Q&A

Thank you!
Any Questions?
