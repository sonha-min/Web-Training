# Node.js Training

## Modern Asynchronous JavaScript with Async and Await

### Introduction

Async/await is a modern JavaScript syntax (introduced in ES2017) that makes working with asynchronous code much easier. It is built **on top of promises** — an `async` function always returns a promise, and `await` pauses the function until a promise settles — but it lets you write asynchronous code that looks and behaves like synchronous code.

A promise is a special object representing the eventual completion (or failure) of an asynchronous operation. It can be in one of three states:

- **Pending**: the operation is still running
- **Fulfilled**: the operation completed successfully
- **Rejected**: the operation failed

Async/await gives us a cleaner way to consume these promises without long `.then()` chains.

```js
const doSomethingAsync = () => {
  return new Promise(resolve => {
    setTimeout(() => resolve('I did something'), 3000);
  });
};

const doSomething = async () => {
  console.log(await doSomethingAsync());
};

console.log('Before');
doSomething();
console.log('After');
```

The code above prints:

```
Before
After
I did something // after 3 seconds
```

### Why were async/await introduced?

Asynchronous JavaScript evolved in stages, each solving the problems of the previous one:

1. **Callbacks** — the original approach. Nesting many callbacks led to deeply indented, hard-to-follow code known as *callback hell* (or the "pyramid of doom").

```js
getUser(id, (err, user) => {
  if (err) return handle(err);
  getOrders(user, (err, orders) => {
    if (err) return handle(err);
    getInvoice(orders[0], (err, invoice) => {
      if (err) return handle(err);
      console.log(invoice);
    });
  });
});
```

2. **Promises (ES2015)** — flattened the pyramid with `.then()` chaining and centralized error handling with `.catch()`. A big improvement, but complex flows still turned into long chains that were awkward to read and reason about.

```js
getUser(id)
  .then(user => getOrders(user))
  .then(orders => getInvoice(orders[0]))
  .then(invoice => console.log(invoice))
  .catch(err => handle(err));
```

3. **Async/await (ES2017)** — reduces the boilerplate around promises. When promises were introduced, it became clear that developers wanted asynchronous code that *reads* like ordinary synchronous code. Async/await is syntactic sugar over promises that achieves exactly that:

```js
async function showInvoice(id) {
  try {
    const user = await getUser(id);
    const orders = await getOrders(user);
    const invoice = await getInvoice(orders[0]);
    console.log(invoice);
  } catch (err) {
    handle(err);
  }
}
```

Same behavior, but the code reads top-to-bottom like synchronous code.

### How it works

Two keywords work together:

- **`async`** — placed before a function declaration. It does two things: it allows the use of `await` inside the function, and it makes the function **always return a promise**. Any returned value is automatically wrapped in a resolved promise.
- **`await`** — placed before a promise. It pauses the execution of the surrounding `async` function (without blocking the main thread — other code keeps running) until the promise settles, then returns its fulfilled value. If the promise rejects, `await` throws the rejection as an error, which you can handle with a normal `try/catch`.

```js
const aFunction = async () => {
  return 'test';
};

aFunction().then(alert); // This will alert 'test'
```

is equivalent to:

```js
const aFunction = () => {
  return Promise.resolve('test');
};

aFunction().then(alert); // This will alert 'test'
```

Error handling uses the familiar synchronous constructs:

```js
async function performTask() {
  try {
    const result = await myPromise;
    console.log(result);
  } catch (error) {
    console.error(error); // Handles any rejection thrown by await
  } finally {
    console.log('performTask() completed');
  }
}
```

> **Note:** In ECMAScript modules (`.mjs` files or `"type": "module"`), you can also use **top-level await** — `await` outside of any function:
>
> ```js
> import { setTimeout as delay } from 'node:timers/promises';
>
> await delay(1000);
> console.log('1 second has passed');
> ```

### What is it for?

#### Promise all the things

Since any `async` function returns a promise implicitly, you can build your whole codebase around promises with very little ceremony. Prepending `async` to any function means it returns a promise — so all your functions compose with each other, with `Promise.all()`, and with the rest of the promise ecosystem:

```js
const getFirstUserData = async () => {
  const response = await fetch('/users.json'); // get users list
  const users = await response.json();         // parse JSON
  const user = users[0];                        // pick first user
  const userResponse = await fetch(`/users/${user.name}`);
  const userData = await userResponse.json();
  return userData; // implicitly wrapped in a promise
};

getFirstUserData().then(data => console.log(data));
```

And when you have independent operations, you can still run them concurrently:

```js
const [users, products] = await Promise.all([
  fetch('/users.json').then(r => r.json()),
  fetch('/products.json').then(r => r.json()),
]);
```

Related helpers worth knowing:

- `Promise.all()` — fulfills when **all** promises fulfill; rejects as soon as one rejects
- `Promise.allSettled()` — waits for all promises to settle and reports each outcome
- `Promise.race()` — settles with the **first** promise to settle
- `Promise.any()` — fulfills with the first fulfilled promise; rejects only if all reject

#### The code is much simpler to read

Compare the same logic written with plain promises and with async/await:

**With promise chaining:**

```js
const getFirstUserData = () => {
  return fetch('/users.json')
    .then(response => response.json())
    .then(users => users[0])
    .then(user => fetch(`/users/${user.name}`))
    .then(userResponse => userResponse.json());
};

getFirstUserData();
```

**With async/await:**

```js
const getFirstUserData = async () => {
  const response = await fetch('/users.json');
  const users = await response.json();
  const user = users[0];
  const userResponse = await fetch(`/users/${user.name}`);
  const userData = await userResponse.json();
  return userData;
};

getFirstUserData();
```

The async/await version reads top-to-bottom like synchronous code: no nesting, no callback per step, and intermediate values live in ordinary variables you can inspect and reuse.

#### Multiple async functions in series

Async functions chain naturally — awaiting one async function inside another runs them in sequence, and each step can use the result of the previous one:

```js
const { setTimeout: delay } = require('node:timers/promises');

const watchTutorialCb = async () => {
  await delay(1000);
  return 'Tutorial watched';
};

const submitCode = async previous => {
  console.log(previous);
  await delay(1000);
  return 'Code submitted';
};

const shipProject = async previous => {
  console.log(previous);
  await delay(1000);
  return 'Project shipped';
};

const startLearning = async () => {
  const step1 = await watchTutorialCb();
  const step2 = await submitCode(step1);
  const step3 = await shipProject(step2);
  console.log(step3);
};

startLearning();
```

Output (one line per second):

```
Tutorial watched
Code submitted
Project shipped
```

Doing the same with `.then()` chains would require returning a new promise from every step and threading values through the chain — with async/await it is just three sequential statements.

#### Easier debugging

Debugging promise chains is hard because the debugger cannot meaningfully step *over* a `.then()` callback — the code inside runs later, on a different tick. Async/await fixes this:

- To the debugger, an `async` function looks like ordinary synchronous code — you can **step over** an `await` line just like a normal statement and land on the next line.
- Stack traces are more useful: errors thrown from awaited promises point at the `await` line inside your function, instead of at an anonymous `.then()` callback.
- Errors are handled with standard `try/catch/finally`, so one construct covers both synchronous mistakes (e.g. a `TypeError`) and asynchronous rejections.
- Intermediate results are plain local variables, visible in the debugger's scope panel — no need to insert extra `.then()` steps just to inspect a value.

### Reference

- [Discover Promises in Node.js — Node.js docs](https://nodejs.org/en/learn/asynchronous-work/discover-promises-in-nodejs#using-asyncawait-with-promises)
