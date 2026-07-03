# Tools

## 1. [Git](./docs/tools/git.md)

# CSS frameworks

## 1. [Tailwind CSS](./docs/css_frameworks/tailwindcss.md)

# RoR-Training

## 1. [Ruby](./docs/ruby_on_rails/ruby.md)

# React-Training

## [React Naming Conventions](./docs/react/react.md#naming-conventions)

## 1. Getting Started with React + Components

## 2. React State & Events

## 3. Effects & API Calls

## 4. [React Router & API Integration](./docs/react/react.md#react-router)

# Node.js-Training

## 1. [Node.js](./docs/nodejs/nodejs.md)

## Recommended Directory Structure

### FE (React + Vite + TypeScript + Tailwind CSS)

**Project root:**

```
frontend/
├── public/                 # Static assets served as-is (favicon, robots.txt, static images...)
├── src/                    # Application source code (see breakdown below)
├── .gitignore              # Files/folders Git should ignore (node_modules, dist, .env...)
├── .prettierignore         # Files/folders Prettier should ignore (node_modules, dist, .env...)
├── .prettierrc.json        # Prettier formatting rules for consistent code style
├── README.md               # Project documentation
├── eslint.config.js        # ESLint rules for linting the codebase
├── index.html              # HTML entry point Vite injects the bundled app into
├── package.json            # Project metadata, dependencies & npm scripts
├── package-lock.json       # Locked dependency versions for reproducible installs
├── tsconfig.json           # Base TypeScript config, references the app/node configs below
├── tsconfig.app.json       # TypeScript config for the app source (src/)
├── tsconfig.node.json      # TypeScript config for Node-context files (e.g. vite.config.ts)
└── vite.config.ts          # Vite build/dev server configuration (plugins, aliases...)
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
├── routes/                    # Routing configuration (e.g., React Router)
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

# Prisma & PostgreSQL Naming Conventions

Prisma and PostgreSQL each have their own idiomatic casing. The trick is to write your **Prisma schema in the JS/TS style** (PascalCase models, camelCase fields) while **mapping to snake_case in the database** with `@map` / `@@map`. This keeps your TypeScript code idiomatic and your SQL tables idiomatic at the same time.

## Naming rules

| Element              | Prisma (schema / TS code) | PostgreSQL (database) | How to map                     |
| -------------------- | ------------------------- | --------------------- | ------------------------------ |
| Model / Table        | `PascalCase`, **singular** (`User`, `BlogPost`) | `snake_case`, **plural** (`users`, `blog_posts`) | `@@map("blog_posts")`          |
| Field / Column       | `camelCase` (`firstName`, `createdAt`) | `snake_case` (`first_name`, `created_at`) | `@map("first_name")`           |
| Relation field       | `camelCase` (`author`, `posts`) | — (no column for the list side) | — |
| Foreign key          | `camelCase` + `Id` (`authorId`) | `snake_case` + `_id` (`author_id`) | `@map("author_id")`            |
| Enum type            | `PascalCase` (`Role`) | `snake_case` (`role`) | `@@map("role")`                |
| Enum value           | `UPPER_SNAKE_CASE` (`ADMIN`) | same | — |
| Primary key          | `id` | `id` | — |
| Boolean fields       | prefix `is` / `has` (`isPublished`) | `is_published` | `@map("is_published")`         |

**Rules of thumb:**
- Models are **singular** in Prisma (`User`), tables are **plural** in Postgres (`users`).
- Always store timestamps as `createdAt` / `updatedAt` (Prisma manages them with `@default(now())` and `@updatedAt`).
- Set the DB casing once with `@map`/`@@map` so you never think about snake_case again in TS.

## Example `schema.prisma`

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

enum Role {
  USER
  ADMIN

  @@map("role")
}

model User {
  id        Int      @id @default(autoincrement())
  email     String   @unique
  firstName String   @map("first_name")
  lastName  String   @map("last_name")
  role      Role     @default(USER)
  isActive  Boolean  @default(true) @map("is_active")
  posts     Post[]
  createdAt DateTime @default(now()) @map("created_at")
  updatedAt DateTime @updatedAt @map("updated_at")

  @@map("users")
}

model Post {
  id          Int      @id @default(autoincrement())
  title       String
  content     String?
  isPublished Boolean  @default(false) @map("is_published")
  author      User     @relation(fields: [authorId], references: [id])
  authorId    Int      @map("author_id")
  createdAt   DateTime @default(now()) @map("created_at")
  updatedAt   DateTime @updatedAt @map("updated_at")

  @@index([authorId])
  @@map("posts")
}
```

## Using Prisma in TypeScript (sample)

Create a single shared client instance (put it in `lib/prisma.ts`), then import it everywhere.

```ts
// lib/prisma.ts
import { PrismaClient } from '@prisma/client';

// Reuse the client across hot-reloads in dev to avoid exhausting DB connections.
const globalForPrisma = globalThis as unknown as { prisma?: PrismaClient };

export const prisma = globalForPrisma.prisma ?? new PrismaClient();

if (process.env.NODE_ENV !== 'production') globalForPrisma.prisma = prisma;
```

```ts
// example usage
import { prisma } from './lib/prisma';

// CREATE — with a nested relation
async function createUserWithPost() {
  const user = await prisma.user.create({
    data: {
      email: 'jane@example.com',
      firstName: 'Jane',
      lastName: 'Doe',
      posts: {
        create: [{ title: 'Hello Prisma', isPublished: true }],
      },
    },
  });
  return user;
}

// READ — filter + include relation + pagination
async function getPublishedPosts() {
  return prisma.post.findMany({
    where: { isPublished: true },
    include: { author: true },
    orderBy: { createdAt: 'desc' },
    take: 10,
  });
}

// UPDATE
async function deactivateUser(userId: number) {
  return prisma.user.update({
    where: { id: userId },
    data: { isActive: false },
  });
}

// DELETE
async function deletePost(postId: number) {
  return prisma.post.delete({ where: { id: postId } });
}
```

> **Note:** You write `firstName` / `isPublished` (camelCase) in TypeScript; Prisma transparently maps them to `first_name` / `is_published` columns in PostgreSQL.
