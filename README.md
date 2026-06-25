# Tools

## 1. [Git](./docs/tools/git.md)

# CSS frameworks

## 1. [Tailwind CSS](./docs/css_frameworks/tailwindcss.md)

# RoR-Training

## 1. [Ruby](./docs/ruby_on_rails/ruby.md)

# React-Training

## 1. Getting Started with React + Components

## 2. React State & Events

## 3. Effects & API Calls

## 4. [React Router & API Integration](./docs/react/react.md)

## Recommended Directory Structure

### FE (React + Vite + TypeScript + Tailwind CSS)

**Project root:**

```
frontend/
├── public/                    # Static assets served as-is (favicon, robots.txt, static images...)
├── src/                       # Application source code (see breakdown below)
├── .gitignore                 # Files/folders Git should ignore (node_modules, dist, .env...)
├── README.md                  # Project documentation
├── eslint.config.js           # ESLint rules for linting the codebase
├── index.html                 # HTML entry point Vite injects the bundled app into
├── package.json               # Project metadata, dependencies & npm scripts
├── package-lock.json          # Locked dependency versions for reproducible installs
├── tsconfig.json              # Base TypeScript config, references the app/node configs below
├── tsconfig.app.json          # TypeScript config for the app source (src/)
├── tsconfig.node.json         # TypeScript config for Node-context files (e.g. vite.config.ts)
└── vite.config.ts             # Vite build/dev server configuration (plugins, aliases...)
```

**`src/` breakdown:**

The structure below builds on the common `components/` + `pages/` split, with a few folders added that most production React apps need: `api/`, `hooks/`, `context/`, `types/`, `utils/`, `lib/`, and `constants/`.

```
frontend/src/
├── assets/                  # Logos, icons, global images
├── components/              # <-- COMMON / SHARED components (used across multiple pages)
│   ├── ui/                  # Atom-level components (buttons, inputs, modals)
│   │   ├── Button.tsx
│   │   ├── Input.tsx
│   │   └── Modal.tsx
│   ├── layout/               # Structural layouts
│   │   ├── Header.tsx
│   │   ├── Footer.tsx
│   │   └── Sidebar.tsx
│   └── FeedbackToast.tsx     # Generic shared feature component
├── pages/                    # <-- MULTIPLE PAGES (views mapped to routes)
│   ├── dashboard/
│   │   ├── components/       # <-- PAGE-SPECIFIC components (only used here)
│   │   │   ├── StatCard.tsx
│   │   │   └── AnalyticsChart.tsx
│   │   └── DashboardPage.tsx
│   ├── profile/
│   │   ├── components/
│   │   │   └── ProfileForm.tsx
│   │   └── ProfilePage.tsx
│   └── home/
│       └── HomePage.tsx
├── routes/                   # Routing configuration (e.g., React Router)
│   └── AppRoutes.tsx
├── api/                       # API service layer, grouped by resource (uses the `lib/axios` client)
│   ├── userService.ts
│   └── authService.ts
├── hooks/                     # Reusable custom hooks (useAuth, useDebounce, useFetch...)
│   ├── useAuth.ts
│   └── useDebounce.ts
├── context/                   # React Context providers / global state (AuthContext, ThemeContext...)
│   └── AuthContext.tsx
├── types/                     # Shared TypeScript types & interfaces (User, ApiResponse...)
│   └── user.ts
├── utils/                     # Generic helper functions (formatDate, validators...)
│   └── formatDate.ts
├── lib/                       # Configured third-party library instances (axios client, query client...)
│   └── axios.ts
├── constants/                 # App-wide constants & enums (routes paths, status codes...)
│   └── routes.ts
├── styles/                    # Global CSS, Tailwind base/overrides
│   └── index.css
├── App.tsx                    # Main application wrapper
└── main.tsx                   # Vite entry point
```

**Why these additions:**
- **`api/`**: keeps endpoint calls grouped by resource (e.g. `userService.getAll()`), instead of scattering `axios`/`fetch` calls inside components — see [Building reusable API service layer](./docs/react/react.md#building-reusable-api-service-layer).
- **`lib/`**: holds the actual third-party client setup (e.g. the configured Axios instance with `baseURL` + interceptors). `api/` imports from `lib/` rather than calling `axios` directly.
- **`hooks/`**: custom hooks that wrap stateful/reusable logic (data fetching, debouncing, auth state) so pages and components stay declarative.
- **`context/`** (or a state library like Zustand/Redux): centralizes global state that doesn't belong to a single page (auth session, theme, feature flags).
- **`types/`**: shared TypeScript types/interfaces used across pages and the API layer, avoiding duplicated `interface User {...}` definitions.
- **`utils/`**: pure helper functions with no React/JSX dependency (formatting, validation, calculations).
- **`constants/`**: fixed values (route paths, enums, status codes) that should not be hardcoded as magic strings/numbers across the app.

**Component placement rule of thumb:**
- If a component is used by **only one page**, keep it inside `pages/<page>/components/`.
- If a component is (or will likely be) used by **more than one page**, promote it to the shared `components/` folder.
