## Naming Conventions

In React, the foundational naming rule is to use **PascalCase for components** and **camelCase for variables, props, hooks, and functions**. Following these community standards keeps a project readable, maintainable, and clearly distinguishes standard HTML tags from custom React elements.

### Overview of React cases

| Code entity | Casing style | Example |
| --- | --- | --- |
| Components | `PascalCase` | `<UserProfile />` |
| Files & folders | `PascalCase` or `kebab-case` | `UserProfile.tsx` or `user-profile.tsx` |
| Props & state | `camelCase` | `isActive`, `userId` |
| Hooks | `camelCase` (prefixed with `use`) | `useAuth()` |
| Functions | `camelCase` | `fetchData()` |
| Constants | `SCREAMING_SNAKE_CASE` | `API_TIMEOUT` |

### Components and contexts

- **Always use PascalCase**: every word must start with a capital letter (e.g. `TodoList`, `AuthContext`).
- **Avoid lowercase starts**: React treats lowercase elements as native HTML tags (like `<div>` or `<button>`). A custom component starting with a lowercase letter will fail to render as a component.

```jsx
// ✅ Good
function UserProfile() { ... }
const AuthContext = createContext(null);

// ❌ Bad - React treats this as a native HTML tag, not a component
function userProfile() { ... }
```

### Files and folders

- **Match component files**: give the file the exact same `PascalCase` name as the component inside it (e.g. `UserCard.tsx`).
- **Non-component files**: use `camelCase` for utilities, services, or helper files (e.g. `formatDate.ts`, `apiService.ts`).
- **Alternative folder approach**: some teams prefer `kebab-case` (dash-separated) for folders and non-component assets to avoid case-sensitivity issues across different operating systems (e.g. `/components/button-group/`).

### Props and state

- **Standard properties**: use `camelCase` for regular variables, state variables, and props (e.g. `const [isLoading, setIsLoading] = useState(false)`).
- **Boolean naming**: prefix boolean props and state values with verbs like `is`, `has`, or `should` to signify their binary nature (e.g. `isLoggedIn`, `hasError`, `shouldRender`).

```jsx
function UserCard({ userId, isActive, hasNotifications }) {
  const [isLoading, setIsLoading] = useState(false);
  ...
}
```

### Functions and event handlers

- **Action triggers (props)**: props that pass a callback function downward should mirror native DOM events and begin with the `on` prefix (e.g. `onClick`, `onUpdateData`).
- **Handling triggers (implementation)**: the actual functions that process those actions inside a component should begin with the `handle` prefix (e.g. `handleClick`, `handleUpdateData`).

```jsx
function SubmitButton({ onSubmit }) {
  const handleClick = () => {
    // local logic before/after notifying the parent
    onSubmit();
  };

  return <button onClick={handleClick}>Submit</button>;
}
```

## React Router

React Router is the standard routing library for React. It lets you map URLs to components so your app can have multiple "pages" without a full page reload.

Install:

```bash
npm install react-router
```

Docs: https://reactrouter.com/start/library/installation

### react-router vs react-router-dom

They are **not two competing libraries** — `react-router-dom` was merged into `react-router` starting with v7.

- **Before v7**: `react-router` was the core, platform-agnostic routing engine (route matching, hooks, context — usable in React Native too). `react-router-dom` was the **web-specific** package built on top of it, adding browser APIs like `<BrowserRouter>` and `<Link>`. You always had to install `react-router-dom` for web apps; `react-router` alone wasn't enough.
- **From v7 onward**: `react-router-dom` (along with the Remix packages) was folded directly into `react-router`. For web apps, you now `npm install react-router` and import everything (`BrowserRouter`, `Link`, hooks, etc.) from `"react-router"`. `react-router-dom` still exists only as a thin re-export of `react-router` for backward compatibility — it has no extra functionality of its own anymore.

| | `react-router` (pre-v7) | `react-router-dom` | `react-router` (v7+) |
| --- | --- | --- | --- |
| What it is | Core routing engine, no DOM bindings | Web bindings (`BrowserRouter`, `Link`, etc.) built on top of `react-router` | Core engine **+** web bindings, unified |
| Usable directly for a web app? | No (missing `BrowserRouter`/`Link`) | Yes | Yes |
| Status today | Legacy | Legacy re-export of `react-router` for compatibility | Current, recommended package |

**Which to use:**
- **New project**: install `react-router` and import from `"react-router"` — this is the example used throughout this doc.
- **Existing project already on v6 with `react-router-dom`**: no rush to change. Imports still work because `react-router-dom` re-exports `react-router` in v7. Migrate when you upgrade dependencies, mainly to drop a redundant package from `node_modules`.

**Pros/cons:**
- ✅ Unified package (`react-router`) → smaller dependency tree, one version to track instead of keeping `react-router` and `react-router-dom` in sync.
- ✅ Same import path works for web and (with the native bindings) React Native, reducing API drift between platforms.

### BrowserRouter

`BrowserRouter` uses the HTML5 history API (`pushState`, `replaceState`) to keep the UI in sync with the URL. Wrap your whole app with it once, at the top level (usually in `main.tsx` / `index.tsx`).

```jsx
import { createRoot } from "react-dom/client";
import { BrowserRouter } from "react-router";
import App from "./App";

createRoot(document.getElementById("root")).render(
  <BrowserRouter>
    <App />
  </BrowserRouter>
);
```

### Routes

`Routes` looks at the current URL and renders the first matching `Route` inside it. Each `Route` maps a `path` to a `element`.

```jsx
import { Routes, Route } from "react-router";
import Home from "./pages/Home";
import About from "./pages/About";
import NotFound from "./pages/NotFound";

function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/about" element={<About />} />
      <Route path="*" element={<NotFound />} />
    </Routes>
  );
}
```

Docs: https://reactrouter.com/start/library/routing

### Route parameters

Use a `:param` segment in the `path` to capture dynamic values from the URL, then read it with the `useParams` hook.

```jsx
import { Routes, Route, useParams } from "react-router";

function UserProfile() {
  const { userId } = useParams(); // e.g. "/users/42" -> userId === "42"
  return <h1>User #{userId}</h1>;
}

function App() {
  return (
    <Routes>
      <Route path="/users/:userId" element={<UserProfile />} />
    </Routes>
  );
}
```

### Navigation

- `Link` renders an `<a>` tag without causing a full page reload.
- `NavLink` is like `Link` but adds an `active` styling hook for the current route.
- `useNavigate` lets you navigate programmatically (e.g. after a form submit).

```jsx
import { Link, NavLink, useNavigate } from "react-router";

function Navbar() {
  const navigate = useNavigate();

  return (
    <nav>
      <Link to="/">Home</Link>
      <NavLink to="/about" className={({ isActive }) => (isActive ? "active" : "")}>
        About
      </NavLink>
      <button onClick={() => navigate("/login")}>Go to Login</button>
    </nav>
  );
}
```

### Nested routes (basic)

Nesting a `Route` inside another route renders the child element inside the parent's `Outlet`. This is useful for shared layouts (e.g. a sidebar that stays while the inner content changes).

```jsx
import { Routes, Route, Outlet, Link } from "react-router";

function DashboardLayout() {
  return (
    <div>
      <aside>
        <Link to="overview">Overview</Link>
        <Link to="settings">Settings</Link>
      </aside>
      <main>
        <Outlet /> {/* renders the matched child route */}
      </main>
    </div>
  );
}

function App() {
  return (
    <Routes>
      <Route path="/dashboard" element={<DashboardLayout />}>
        <Route path="overview" element={<Overview />} />
        <Route path="settings" element={<Settings />} />
      </Route>
    </Routes>
  );
}
```

## API Integration

### Using Fetch API

`fetch` is a built-in browser API for making HTTP requests, no installation needed.

```jsx
useEffect(() => {
  fetch("https://api.example.com/users")
    .then((res) => res.json())
    .then((data) => setUsers(data))
    .catch((err) => console.error(err));
}, []);
```

Docs:
- https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API
- https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch

### Using Axios

Axios is a popular third-party HTTP client. It has a simpler API than `fetch` (e.g. automatic JSON parsing, built-in error handling for non-2xx responses).

```bash
npm install axios
```

```jsx
import axios from "axios";

useEffect(() => {
  axios
    .get("https://api.example.com/users")
    .then((res) => setUsers(res.data))
    .catch((err) => console.error(err));
}, []);
```

Docs: https://axios-http.com/docs/intro

### Fetch API vs Axios

| Feature | Fetch API | Axios |
| --- | --- | --- |
| Installation | Not needed (built into the browser) | Must be installed via `npm` / `yarn` |
| JSON handling | Manual, two steps (must call `.json()`) | Automatic conversion to JSON (access via `.data`) |
| HTTP error handling (404, 500…) | **Not** treated as an error. The promise still resolves; you must check `response.ok` yourself | Treated as an error. Automatically rejects the promise into `catch` |
| Interceptors (intercept request/response) | Not built-in (must write your own wrapper) | **Strong built-in support** (very useful for automatically attaching a JWT token) |
| Aborting requests | Uses `AbortController` (a bit verbose) | Built-in support via `CancelToken` or `AbortController` |
| Progress tracking | Download only, no upload progress | Supports both download and upload progress (useful for file upload progress bars) |

### GET / POST / PUT / PATCH / DELETE requests

| Method | Purpose | Fetch example | Axios example |
| --- | --- | --- | --- |
| GET | Read data | `fetch(url)` | `axios.get(url)` |
| POST | Create data | `fetch(url, { method: "POST", body })` | `axios.post(url, data)` |
| PUT | Replace data | `fetch(url, { method: "PUT", body })` | `axios.put(url, data)` |
| PATCH | Partially update data | `fetch(url, { method: "PATCH", body })` | `axios.patch(url, data)` |
| DELETE | Remove data | `fetch(url, { method: "DELETE" })` | `axios.delete(url)` |

```jsx
// Axios - create a user
axios.post("https://api.example.com/users", { name: "Alice" });

// Axios - update a user's email only
axios.patch("https://api.example.com/users/1", { email: "alice@example.com" });

// Fetch - delete a user
fetch("https://api.example.com/users/1", { method: "DELETE" });
```

Docs: https://axios-http.com/docs/api_intro

### Sending query parameters & request body

- Query parameters go on the URL (commonly used for filtering/searching with GET).
- Request body carries the data payload (commonly used with POST/PUT/PATCH).

```jsx
// Fetch - query params
fetch("https://api.example.com/users?page=2&limit=10");

// Fetch - request body (must stringify + set Content-Type)
fetch("https://api.example.com/users", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ name: "Alice" }),
});

// Axios - query params via `params` option
axios.get("https://api.example.com/users", { params: { page: 2, limit: 10 } });

// Axios - request body is just the second argument, no need to stringify
axios.post("https://api.example.com/users", { name: "Alice" });
```

### Handling HTTP status codes & errors

`fetch` only rejects on network failure — a 404 or 500 response is still a "successful" promise, so you must check `res.ok` yourself. Axios, on the other hand, automatically throws for any non-2xx status.

```jsx
// Fetch - manual status check
fetch("https://api.example.com/users/999")
  .then((res) => {
    if (!res.ok) throw new Error(`HTTP error: ${res.status}`);
    return res.json();
  })
  .catch((err) => console.error(err.message));

// Axios - errors thrown automatically for non-2xx
axios.get("https://api.example.com/users/999").catch((err) => {
  if (err.response) {
    console.error(err.response.status, err.response.data); // server responded with an error status
  } else {
    console.error(err.message); // network error, no response received
  }
});
```

### Configuring baseURL & environment variables

Avoid hardcoding the API host everywhere. Create one Axios instance with a `baseURL`, and store the host in an environment variable (e.g. `.env` file, not committed to git).

```bash
# .env
VITE_API_BASE_URL=https://api.example.com
```

```js
// api/client.js
import axios from "axios";

const apiClient = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL,
  timeout: 10000,
});

export default apiClient;
```

```jsx
import apiClient from "./api/client";

apiClient.get("/users"); // resolves to https://api.example.com/users
```

### Axios Interceptors (Request & Response)

Interceptors let you run code before a request is sent or after a response is received — useful for attaching tokens, logging, or handling errors globally in one place.

```js
// api/client.js
import axios from "axios";

const apiClient = axios.create({ baseURL: import.meta.env.VITE_API_BASE_URL });

// Request interceptor: attach auth token to every request
apiClient.interceptors.request.use((config) => {
  const token = localStorage.getItem("token");
  if (token) config.headers.Authorization = `Bearer ${token}`;
  return config;
});

// Response interceptor: handle expired sessions globally
apiClient.interceptors.response.use(
  (response) => response,
  (error) => {
    // 401 Unauthorized → clear token and redirect to login
    if (error.response?.status === 401) {
      localStorage.removeItem("token");
      window.location.href = "/login";
    }
    return Promise.reject(error);
  }
);

export default apiClient;
```

Docs: https://axios-http.com/docs/interceptors

### Authentication with JWT (Bearer Token)

After logging in, the server returns a JWT. Store it (e.g. `localStorage`) and send it with every subsequent request via the `Authorization: Bearer <token>` header — the request interceptor above does this automatically.

```js
// Login and save the token
const login = async (email, password) => {
  const res = await apiClient.post("/auth/login", { email, password });
  localStorage.setItem("token", res.data.token);
};

// Logout
const logout = () => {
  localStorage.removeItem("token");
};
```

### Building reusable API service layer

Instead of calling `axios`/`fetch` directly inside components, group requests by resource into "service" modules. This keeps endpoint URLs in one place and makes components easier to read and test.

```js
// api/client.js
import axios from "axios";

const apiClient = axios.create({ baseURL: import.meta.env.VITE_API_BASE_URL });
export default apiClient;
```

```js
// api/userService.js
import apiClient from "./client";

export const userService = {
  getAll: (params) => apiClient.get("/users", { params }),
  getById: (id) => apiClient.get(`/users/${id}`),
  create: (data) => apiClient.post("/users", data),
  update: (id, data) => apiClient.put(`/users/${id}`, data),
  remove: (id) => apiClient.delete(`/users/${id}`),
};
```

```jsx
// components/UserList.jsx
import { userService } from "../api/userService";

useEffect(() => {
  userService.getAll({ page: 1 }).then((res) => setUsers(res.data));
}, []);
```
