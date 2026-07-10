# Express.js Training

## Getting Started

### What is Express.js?

Express is a minimal and flexible Node.js web application framework that provides a robust set of features for building web and mobile applications. It sits on top of Node's built-in `http` module and adds routing, middleware, and helpers for requests/responses, so you don't have to write that plumbing yourself for every project.

Express does not force any particular structure or set of tools on you — it's unopinionated. You bring your own database, template engine, project layout, etc., and Express just provides the HTTP layer.

Common use cases:

- REST APIs
- Server-rendered websites (with a template engine)
- Single-purpose middleware/proxy servers

### How to install Express.js?

Express is installed as an npm dependency in a Node.js project. First create a directory for your app and initialize it with npm:

```bash
mkdir myapp
cd myapp
npm init -y
```

`npm init` creates a `package.json` file for your application. Then install Express and save it as a dependency:

```bash
npm install express
```

### Hello World example

A minimal Express app that starts a server and listens on a port for connections. It responds with "Hello World!" for requests to the root URL (`/`).

```js
// app.js
const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => {
  res.send('Hello World!');
});

app.listen(port, () => {
  console.log(`Example app listening on port ${port}`);
});
```

Run it with:

```bash
node app.js
```

Then visit `http://localhost:3000` in a browser, or `curl` it, to see "Hello World!".

### Use express-generator

`express-generator` is a CLI tool that quickly scaffolds an application skeleton with a conventional directory structure (routes, views, static files, etc.). It is useful for bootstrapping traditional multi-page apps with a template engine.

Run it directly with `npx` (no need to install it globally):

```bash
npx express-generator myapp
```

You can pass a `--view` (or `-v`) flag to pick a template engine, e.g. `--view=pug`, `--view=ejs`, `--view=hbs`.

Then install dependencies and start the app:

```bash
cd myapp
npm install
npm start
```

The generated skeleton looks roughly like this:

```
myapp/
├── app.js
├── package.json
├── bin/
│   └── www
├── public/
│   ├── images/
│   ├── javascripts/
│   └── stylesheets/
├── routes/
│   ├── index.js
│   └── users.js
└── views/
    ├── error.pug
    ├── index.pug
    └── layout.pug
```

### Basic Routing

Routing refers to determining how an application responds to a client request to a particular endpoint — a URI (or path) and a specific HTTP request method (GET, POST, and so on).

Each route can have one or more handler functions, executed when the route is matched:

```js
app.get('/', (req, res) => {
  res.send('Hello World!');
});

app.post('/', (req, res) => {
  res.send('Got a POST request');
});

app.put('/user', (req, res) => {
  res.send('Got a PUT request at /user');
});

app.delete('/user', (req, res) => {
  res.send('Got a DELETE request at /user');
});
```

See the [Routing](#routing) section below for a deeper dive.

### Static files

To serve static files such as images, CSS files, and JavaScript files, use the built-in `express.static` middleware function.

```js
app.use(express.static('public'));
```

Now files in the `public` directory are served directly. For example:

```
http://localhost:3000/images/logo.png
http://localhost:3000/css/style.css
http://localhost:3000/js/app.js
```

Express looks up files relative to the static directory given, so put the name of the static directory (`public`, `static`, etc.) inside the path, not in the URL.

To use multiple static directories, call `express.static` multiple times:

```js
app.use(express.static('public'));
app.use(express.static('files'));
```

To create a virtual path prefix (one that does not actually exist on the filesystem) for files served by `express.static`, mount a path for it:

```js
app.use('/static', express.static('public'));
```

Now files in `public` are accessible from the `/static` path prefix, e.g. `http://localhost:3000/static/images/logo.png`.

---

## Routing

### What is Routing in Express.js?

Routing refers to how an application's endpoints (URIs) respond to client requests. Each route definition is made up of:

- an HTTP method (`GET`, `POST`, `PUT`, `DELETE`, etc.)
- a path pattern
- one or more handler functions that execute when the route matches

```js
app.METHOD(PATH, HANDLER);
```

- `app` is an instance of Express.
- `METHOD` is an HTTP request method, in lowercase.
- `PATH` is a path on the server.
- `HANDLER` is the function executed when the route is matched.

### Route methods

A route method is derived from one of the HTTP methods and is attached to an instance of the `express` class. The following code is an example of routes that are defined for the GET and the POST methods to the root of the app:

```js
app.get('/', (req, res) => {
  res.send('GET request to the homepage');
});

app.post('/', (req, res) => {
  res.send('POST request to the homepage');
});
```

Express supports methods that correspond to all HTTP request methods: `get`, `post`, `put`, `head`, `delete`, `options`, `patch`, etc.

There is a special routing method, `app.all()`, used to load middleware functions at a path for **all** HTTP request methods. For example, the following handler is executed for requests to `/secret`, regardless of the HTTP verb used:

```js
app.all('/secret', (req, res, next) => {
  console.log('Accessing the secret section...');
  next(); // pass control to the next handler
});
```

### Route paths

Route paths, in combination with a request method, define the endpoints at which requests can be made. Route paths can be strings, string patterns, or regular expressions.

String paths:

```js
app.get('/', (req, res) => {
  res.send('root');
});

app.get('/about', (req, res) => {
  res.send('about');
});
```

Paths based on string patterns (Express 5 uses the `path-to-regexp` library and supports `*`, named parameters, and optional segments):

```js
// matches acd and abcd
app.get('/ab?cd', (req, res) => {
  res.send('ab?cd');
});

// matches any path ending in "fly" - butterfly, dragonfly, etc.
app.get('/*fly', (req, res) => {
  res.send('*fly');
});
```

Paths based on regular expressions:

```js
// matches anything with an "a" in it
app.get(/a/, (req, res) => {
  res.send('/a/');
});

// matches butterfly and dragonfly, but not butterflyman, dragonfly man, etc.
app.get(/.*fly$/, (req, res) => {
  res.send('/.*fly$/');
});
```

### Route parameters

Route parameters are named URL segments used to capture values at their position in the URL. The captured values are populated in the `req.params` object, with the name of the route parameter specified in the path as their keys.

```js
app.get('/users/:userId/books/:bookId', (req, res) => {
  res.send(req.params);
});
```

For a request to `/users/34/books/8989`, `req.params` is:

```json
{ "userId": "34", "bookId": "8989" }
```

To have more control over the exact string that can be matched, you can append a regular expression in parentheses:

```js
app.get('/user/:userId(\\d+)', (req, res) => {
  res.send(req.params);
});
```

This only matches when `userId` consists of digits.

### Route handlers

You can provide multiple callback functions that behave like middleware to handle a request, either as a single function, an array of functions, or a combination of both. Route handlers can be in the form of a function, an array of functions, or a combination of both.

Single callback:

```js
app.get('/example/a', (req, res) => {
  res.send('Hello from A!');
});
```

Multiple callbacks — call `next()` to pass control to the next handler:

```js
app.get(
  '/example/b',
  (req, res, next) => {
    console.log('the response will be sent by the next function ...');
    next();
  },
  (req, res) => {
    res.send('Hello from B!');
  },
);
```

Array of callbacks:

```js
const cb0 = (req, res, next) => {
  console.log('CB0');
  next();
};

const cb1 = (req, res, next) => {
  console.log('CB1');
  next();
};

const cb2 = (req, res) => {
  res.send('Hello from C!');
};

app.get('/example/c', [cb0, cb1, cb2]);
```

### Response methods

The methods on the response object (`res`) send a response to the client and terminate the request-response cycle. If none of these methods are called from a route handler, the client request is left hanging.

| Method | Description |
| --- | --- |
| `res.download()` | Prompt a file to be downloaded |
| `res.end()` | End the response process |
| `res.json()` | Send a JSON response |
| `res.jsonp()` | Send a JSON response with JSONP support |
| `res.redirect()` | Redirect a request |
| `res.render()` | Render a view template |
| `res.send()` | Send a response of various types |
| `res.sendFile()` | Send a file as an octet stream |
| `res.sendStatus()` | Set the response status code and send its string representation as the response body |

```js
app.get('/movies', (req, res) => {
  res.json({ title: 'Long Way Down' });
});
```

### app.route()

You can create chainable route handlers for a route path by using `app.route()`. Because the path is specified in a single location, it helps reduce redundancy and typos when working with multiple HTTP verbs on the same path.

```js
app
  .route('/book')
  .get((req, res) => {
    res.send('Get a random book');
  })
  .post((req, res) => {
    res.send('Add a book');
  })
  .put((req, res) => {
    res.send('Update the book');
  });
```

### express.Router

The `express.Router` class creates modular, mountable route handlers. A `Router` instance is a complete middleware and routing system — often referred to as a "mini-app".

`routes/birds.js`:

```js
const express = require('express');
const router = express.Router();

// middleware specific to this router
router.use((req, res, next) => {
  console.log('Time:', Date.now());
  next();
});

router.get('/', (req, res) => {
  res.send('Birds home page');
});

router.get('/about', (req, res) => {
  res.send('About birds');
});

module.exports = router;
```

Mount it in the app:

```js
const birds = require('./routes/birds');

app.use('/birds', birds);
```

The app is now able to handle requests to `/birds` and `/birds/about`.

---

## Middleware

### What is Middleware in Express.js?

Middleware functions are functions that have access to the request object (`req`), the response object (`res`), and the `next` middleware function in the application's request-response cycle. The next middleware function is commonly denoted by a variable named `next`.

Middleware functions can perform the following tasks:

- Execute any code.
- Make changes to the request and the response objects.
- End the request-response cycle.
- Call the next middleware in the stack.

If the current middleware function does not end the request-response cycle, it must call `next()` to pass control to the next middleware function, otherwise the request will be left hanging.

Express is a routing and middleware framework: internally an Express application is essentially a series of middleware function calls.

### How to write a middleware?

A middleware function takes three arguments: `req`, `res`, and `next` (and a fourth, `err`, for error-handling middleware).

```js
const myLogger = (req, res, next) => {
  console.log('LOGGED');
  next();
};
```

The variable `myLogger` refers to a function. The function takes three arguments: a request object, a response object, and the next function in the application's request-response cycle. Calling `next()` invokes the next middleware function in the app.

### How to use a middleware?

Load middleware functions with `app.use()` (or `app.METHOD()` for a specific method), specifying an optional mount path.

Application-level middleware bound to no particular path — executes on every request:

```js
app.use((req, res, next) => {
  console.log('Time:', Date.now());
  next();
});
```

Application-level middleware mounted to a path — executes for requests matching that path:

```js
app.use('/user/:id', (req, res, next) => {
  console.log('Request Type:', req.method);
  next();
});
```

A middleware for a specific HTTP method and path:

```js
app.get('/user/:id', (req, res, next) => {
  console.log('ID:', req.params.id);
  next();
}, (req, res) => {
  res.send('User Info');
});
```

A series of middleware functions can be loaded together, at a mount path, which is executed in the order they are declared:

```js
app.use('/user/:id', (req, res, next) => {
  console.log('Request URL:', req.originalUrl);
  next();
}, (req, res, next) => {
  console.log('Request Type:', req.method);
  next();
});
```

Third-party middleware is installed via npm and loaded in the app the same way. For example, `cookie-parser` parses cookies attached to the client request:

```bash
npm install cookie-parser
```

```js
const express = require('express');
const cookieParser = require('cookie-parser');

const app = express();
app.use(cookieParser());
```

---

## Using template engines with Express - Pug

### What is Template engine in Express.js?

A template engine enables you to use static template files in your application. At runtime, the template engine replaces variables in a template file with actual values, and transforms the template into an HTML file sent to the client. This approach makes it easier to design an HTML page.

Some popular template engines that work with Express are Pug, Mustache, and EJS.

### How to configure Pug template engine for the express app?

To render template files, set the following application settings:

- `views`: the directory where the template files are located, e.g. `app.set('views', './views')`. Defaults to the `views` directory in the application root.
- `view engine`: the template engine to use, e.g. `app.set('view engine', 'pug')`.

Install Pug:

```bash
npm install pug
```

Configure the app:

```js
const express = require('express');
const app = express();

app.set('views', './views');
app.set('view engine', 'pug');
```

Once the view engine is set, you don't have to specify the engine or load the template engine module in your app; Express loads the module internally.

Then create a Pug template file, `views/index.pug`, and render it with `res.render()`:

```js
app.get('/', (req, res) => {
  res.render('index', { title: 'Hey', message: 'Hello there!' });
});
```

### How to write a pug file?

Pug uses indentation to represent nested HTML elements, instead of closing tags. Variables passed to `res.render()` are available in the template with `#{variableName}` interpolation.

`views/index.pug`:

```pug
html
  head
    title= title
  body
    h1= message
```

Given the render call above (`{ title: 'Hey', message: 'Hello there!' }`), this renders to:

```html
<html>
  <head>
    <title>Hey</title>
  </head>
  <body>
    <h1>Hello there!</h1>
  </body>
</html>
```

A slightly richer example with attributes, classes, loops, and conditionals:

```pug
doctype html
html(lang="en")
  head
    title= title
  body
    h1.header #{message}
    if users.length
      ul
        each user in users
          li= user.name
    else
      p No users found.
```

See the [Pug documentation](https://pugjs.org) for the full language reference.

---

## Error Handling

### What is Error Handling?

Error handling refers to how Express catches and processes errors that occur while handling requests, whether they happen synchronously or asynchronously, so that the process doesn't crash and the client receives an appropriate response instead of hanging.

Express comes with a default error handler, so you don't have to write your own to get started. This default handler adds the error's stack trace to the response body (in development) and sends an appropriate HTTP status code and message when no custom handler catches the error.

### How to catch error in Express.js app?

**Synchronous errors** — errors thrown inside route handlers and middleware are caught automatically by Express, no extra code required:

```js
app.get('/', (req, res) => {
  throw new Error('BROKEN'); // Express catches this automatically
});
```

**Errors in asynchronous code** — for handlers that return promises (including `async` functions), Express 5 automatically catches rejected promises and forwards them to the error handler. In Express 4 and earlier, you must catch them yourself and call `next(err)`:

```js
// Express 5 - no try/catch needed, rejected promises are forwarded automatically
app.get('/user/:id', async (req, res) => {
  const user = await getUserById(req.params.id);
  res.send(user);
});
```

```js
// Express 4 - errors must be passed to next() manually
app.get('/user/:id', (req, res, next) => {
  getUserById(req.params.id)
    .then(user => res.send(user))
    .catch(next);
});
```

Errors that occur in code that is called asynchronously in ways Express cannot intercept (e.g. inside a raw `setTimeout` callback) must still be forwarded to `next()` manually:

```js
app.get('/', (req, res, next) => {
  setTimeout(() => {
    try {
      throw new Error('BROKEN');
    } catch (err) {
      next(err);
    }
  }, 100);
});
```

**Custom error-handling middleware** — defined with four arguments `(err, req, res, next)` instead of three. Error-handling middleware must be defined last, after other `app.use()` and route calls:

```js
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).send('Something broke!');
});
```

You can define several error-handling middleware functions, much as you would with regular middleware. Skip to the next one by calling `next(err)`; skip the rest of the error middleware chain and fall through to regular middleware by calling `next()` without an argument (rarely useful in practice).

If you call `next()` with any argument other than `'route'` or `'router'`, Express treats the request as an error and skips any remaining non-error-handling routing and middleware functions — the request goes straight to whichever error handler is next in the stack.

### Reference

- [Express.js — official website](https://expressjs.com)
- [Installing Express](https://expressjs.com/en/starter/installing.html)
- [express-generator](https://expressjs.com/en/starter/generator.html)
- [Basic routing](https://expressjs.com/en/starter/basic-routing.html)
- [Serving static files in Express](https://expressjs.com/en/starter/static-files.html)
- [Routing guide](https://expressjs.com/en/guide/routing.html)
- [Writing middleware](https://expressjs.com/en/guide/writing-middleware.html)
- [Using middleware](https://expressjs.com/en/guide/using-middleware.html)
- [Using template engines with Express](https://expressjs.com/en/guide/using-template-engines.html)
- [Pug — GitHub](https://github.com/pugjs/pug)
- [Error handling](https://expressjs.com/en/guide/error-handling.html)
