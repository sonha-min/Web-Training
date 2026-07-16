# Prisma Training

## Getting Started

### Introduction to Prisma

Prisma is a next-generation ORM (Object-Relational Mapping) for Node.js and TypeScript. It replaces traditional, hand-written SQL and older ORMs with a type-safe query builder that is generated from your data model, so the shape of your database is reflected directly in your code.

Prisma is made up of a few core tools that work together:

- **Prisma Schema** — a single declarative file (`schema.prisma`) where you describe your data models, database connection, and generators. It is the single source of truth for your database structure.
- **Prisma Client** — an auto-generated, fully type-safe query builder tailored to your schema. You use it in your application code to read and write data.
- **Prisma Migrate** — a migration system that turns changes in your schema into SQL migration files and applies them to your database.
- **Prisma CLI** — the command-line tool (`prisma`) that ties everything together: generating the client, running migrations, inspecting the database, and more.

Prisma supports many relational databases (PostgreSQL, MySQL, SQLite, SQL Server, CockroachDB) as well as MongoDB.

Why teams choose Prisma:

- **Type safety** — queries are checked at compile time; a typo in a field name or a wrong type is a TypeScript error, not a runtime surprise.
- **Autocompletion** — because the client is generated from your schema, your editor knows every model, field, and relation.
- **Readable data model** — the schema file is concise and easy to review in pull requests.
- **Productive migrations** — schema changes and SQL migrations stay in sync automatically.

A tiny taste of what Prisma code looks like:

```ts
// Find one user and all of their posts, fully typed
const user = await prisma.user.findUnique({
  where: { email: 'alice@prisma.io' },
  include: { posts: true },
});
```

### Getting Started

#### Installing Prisma

Prisma is installed as an npm dependency in a Node.js project. First create a directory for your app and initialize it with npm:

```bash
mkdir my-prisma-app
cd my-prisma-app
npm init -y
```

Install the Prisma CLI as a development dependency, and (for TypeScript projects) the usual TypeScript tooling:

```bash
npm install prisma --save-dev
npm install typescript ts-node @types/node --save-dev
```

The Prisma CLI is best invoked with `npx` so you always use the project-local version:

```bash
npx prisma
```

Later, when you are ready to query the database from your application, you also install the Client runtime package:

```bash
npm install @prisma/client
```

#### Project setup

Initialize Prisma in your project with `prisma init`. This is the command that scaffolds the files Prisma needs:

```bash
npx prisma init --datasource-provider postgresql
```

This does two things:

1. Creates a `prisma/` directory containing a `schema.prisma` file — your Prisma Schema.
2. Creates (or updates) a `.env` file at the project root with a `DATABASE_URL` placeholder.

The generated `prisma/schema.prisma` looks like this:

```prisma
// This is your Prisma schema file,
// learn more about it in the docs: https://pris.ly/d/prisma-schema

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}
```

- The `datasource` block tells Prisma which database to connect to. The `url` is read from the `DATABASE_URL` environment variable, so credentials never live in source control.
- The `generator` block tells Prisma what to generate from the schema — here, the JavaScript/TypeScript Prisma Client.

Set your connection string in `.env`:

```bash
# .env
DATABASE_URL="postgresql://user:password@localhost:5432/mydb?schema=public"
```

Now add your first model to `schema.prisma`:

```prisma
model User {
  id    Int     @id @default(autoincrement())
  email String  @unique
  name  String?
  posts Post[]
}

model Post {
  id        Int     @id @default(autoincrement())
  title     String
  content   String?
  published Boolean @default(false)
  author    User    @relation(fields: [authorId], references: [id])
  authorId  Int
}
```

#### Prisma CLI overview

The Prisma CLI is the tool you use for every schema/database task. The commands you will use most often:

| Command | Description |
| --- | --- |
| `prisma init` | Set up Prisma in the project (schema + `.env`). |
| `prisma migrate dev` | Create and apply a new migration in development. |
| `prisma migrate deploy` | Apply pending migrations in production/CI. |
| `prisma generate` | (Re)generate Prisma Client from the schema. |
| `prisma db push` | Push the schema to the database without creating a migration (prototyping). |
| `prisma db pull` | Introspect an existing database and update the schema from it. |
| `prisma db seed` | Run the seed script to populate the database. |
| `prisma studio` | Open Prisma Studio, a GUI to view and edit data. |
| `prisma validate` | Check the schema file for errors. |
| `prisma format` | Format the schema file. |

Run any of them via `npx`, for example:

```bash
npx prisma studio
```

#### First Prisma project workflow

A typical first run, from empty project to querying data, looks like this:

1. **Model your data** in `schema.prisma` (as shown above).

2. **Create the database tables** with your first migration. This also generates Prisma Client:

```bash
npx prisma migrate dev --name init
```

3. **Write application code** that uses Prisma Client:

```ts
// index.ts
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();

async function main() {
  // Create a user with a post
  await prisma.user.create({
    data: {
      email: 'alice@prisma.io',
      name: 'Alice',
      posts: {
        create: { title: 'Hello World' },
      },
    },
  });

  // Read all users, including their posts
  const users = await prisma.user.findMany({
    include: { posts: true },
  });
  console.dir(users, { depth: null });
}

main()
  .then(async () => await prisma.$disconnect())
  .catch(async (e) => {
    console.error(e);
    await prisma.$disconnect();
    process.exit(1);
  });
```

4. **Run it**:

```bash
npx ts-node index.ts
```

You now have a working Prisma project: a schema, a migrated database, and type-safe queries.

### Reference

- [What is Prisma?](https://www.prisma.io/docs/orm/overview/introduction/what-is-prisma)
- [Quickstart](https://www.prisma.io/docs/getting-started/quickstart-sqlite)
- [Set up Prisma ORM from scratch](https://www.prisma.io/docs/getting-started/setup-prisma/start-from-scratch/relational-databases-typescript-postgresql)
- [Installation](https://www.prisma.io/docs/orm/reference/prisma-cli-reference)
- [Prisma CLI reference](https://www.prisma.io/docs/orm/reference/prisma-cli-reference)

---

## Prisma Client

### What is Prisma Client

Prisma Client is an auto-generated, type-safe query builder that you use in your Node.js/TypeScript application to talk to your database. Instead of writing raw SQL strings, you call methods like `prisma.user.findMany()` and get back plain JavaScript objects.

Its defining characteristic is that it is *generated from your schema*. Every model you declare becomes a property on the client (`prisma.user`, `prisma.post`), and every one of those exposes the same set of query methods (`findMany`, `create`, `update`, `delete`, and so on).

### Generated client concept

When you run `prisma generate` (or `prisma migrate dev`, which runs it for you), Prisma reads your schema and writes a tailored client into `node_modules/@prisma/client`. This is a key idea: the client is not a generic library — it is specific to *your* models.

```bash
npx prisma generate
```

Because of this, you must re-run generation whenever you change your schema, so the client stays in sync. Migration commands do this automatically; if you only edit the schema (e.g. change a `generator` option), run `prisma generate` yourself.

The generated client is imported and instantiated once, then reused across your app:

```ts
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();
```

> **Best practice:** create a single `PrismaClient` instance and share it across your application. Creating many instances can exhaust the database connection pool. In serverless or hot-reloading environments, cache the instance on a global variable.

```ts
// lib/prisma.ts — a shared singleton
import { PrismaClient } from '@prisma/client';

const globalForPrisma = globalThis as unknown as { prisma?: PrismaClient };

export const prisma = globalForPrisma.prisma ?? new PrismaClient();

if (process.env.NODE_ENV !== 'production') globalForPrisma.prisma = prisma;
```

### Using Prisma Client in Node.js

Every query is asynchronous and returns a promise, so you use `async`/`await`. The general shape is `prisma.<model>.<operation>(<args>)`:

```ts
import { prisma } from './lib/prisma';

async function main() {
  // Create
  const user = await prisma.user.create({
    data: { email: 'bob@prisma.io', name: 'Bob' },
  });

  // Read
  const allUsers = await prisma.user.findMany();

  // Update
  await prisma.user.update({
    where: { id: user.id },
    data: { name: 'Bobby' },
  });

  // Delete
  await prisma.user.delete({ where: { id: user.id } });
}
```

Remember to disconnect when your script finishes (long-running servers keep the connection open):

```ts
await prisma.$disconnect();
```

### Type safety basics

Because the client is generated from the schema, TypeScript knows the exact shape of every model and every query result. This gives you three big wins:

**1. Autocomplete and compile-time checks on inputs.** If you reference a field that does not exist, TypeScript flags it:

```ts
await prisma.user.create({
  data: {
    email: 'a@b.com',
    nam: 'Typo', // ❌ Type error: 'nam' does not exist; did you mean 'name'?
  },
});
```

**2. Result types match what you selected.** If you `select` only some fields, the returned type has only those fields:

```ts
const user = await prisma.user.findUniqueOrThrow({
  where: { id: 1 },
  select: { email: true },
});

user.email; // ✅ string
user.name;  // ❌ Type error: 'name' is not selected, so it isn't on the result
```

**3. Generated helper types.** Prisma exports types for each model and for operation inputs, which you can use in your own function signatures:

```ts
import { User, Prisma } from '@prisma/client';

// The model type
function formatUser(user: User): string {
  return `${user.name} <${user.email}>`;
}

// Input types for building reusable query arguments
const userCreateInput: Prisma.UserCreateInput = {
  email: 'carol@prisma.io',
  name: 'Carol',
};
```

### Reference

- [Prisma Client overview](https://www.prisma.io/docs/orm/prisma-client)
- [Generating Prisma Client](https://www.prisma.io/docs/orm/prisma-client/setup-and-configuration/generating-prisma-client)
- [Instantiating Prisma Client](https://www.prisma.io/docs/orm/prisma-client/setup-and-configuration/instantiate-prisma-client)
- [Best practice for instantiating Prisma Client](https://www.prisma.io/docs/orm/prisma-client/setup-and-configuration/databases-connections#prismaclient-in-long-running-applications)
- [Operating against types (TypeScript)](https://www.prisma.io/docs/orm/prisma-client/type-safety)

---

## Migrations & Seeding the database

### Prisma Migrate overview

Prisma Migrate is Prisma's schema-migration tool. It keeps your database schema in sync with your Prisma Schema by generating SQL migration files and applying them.

The workflow is: you change a model in `schema.prisma`, then ask Migrate to figure out the SQL needed to get the database from its current state to the new desired state. Migrate writes that SQL into a timestamped folder under `prisma/migrations/` and runs it.

```bash
npx prisma migrate dev --name add_bio_to_user
```

This command, in development:

1. Compares your schema to the database and generates a new SQL migration.
2. Applies the migration to your development database.
3. Regenerates Prisma Client.

The migration history in `prisma/migrations/` is committed to source control, so every environment (and every teammate) applies the exact same changes in the exact same order.

```
prisma/
├── schema.prisma
└── migrations/
    ├── 20260716120000_init/
    │   └── migration.sql
    └── 20260716130000_add_bio_to_user/
        └── migration.sql
```

### Development vs production migrations

Migrate uses different commands for different environments — this is an important distinction.

**Development — `prisma migrate dev`.** Use this while building. It creates new migrations from schema changes, applies them, and can reset the database when history diverges. It is interactive and may prompt you. Never run it against production.

```bash
npx prisma migrate dev --name descriptive_name
```

**Production / CI — `prisma migrate deploy`.** Use this to roll out already-created migrations. It does *not* generate new migrations and never resets data — it simply applies any migration files that have not yet been applied, in order. This is the command you put in your deployment pipeline.

```bash
npx prisma migrate deploy
```

**Prototyping — `prisma db push`.** When you are experimenting and don't care about a migration history yet, `db push` syncs the schema straight to the database without creating migration files. Handy for spikes; not for production, because it leaves no history to replay.

```bash
npx prisma db push
```

#### Seeding the database

Seeding populates your database with an initial or sample dataset — useful for development and for baseline data your app needs. Configure a seed script in `package.json`:

```json
{
  "prisma": {
    "seed": "ts-node prisma/seed.ts"
  }
}
```

Write the seed script using Prisma Client:

```ts
// prisma/seed.ts
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();

async function main() {
  await prisma.user.createMany({
    data: [
      { email: 'alice@prisma.io', name: 'Alice' },
      { email: 'bob@prisma.io', name: 'Bob' },
    ],
    skipDuplicates: true,
  });
}

main()
  .then(async () => await prisma.$disconnect())
  .catch(async (e) => {
    console.error(e);
    await prisma.$disconnect();
    process.exit(1);
  });
```

Run it manually with `prisma db seed`. It also runs automatically after `prisma migrate dev` and `prisma migrate reset`:

```bash
npx prisma db seed
```

### Schema evolution

Schema evolution is the ongoing process of changing your data model over time — adding fields, renaming, changing types, adding relations — while preserving existing data. With Prisma the loop is always the same:

1. **Edit** the model in `schema.prisma`.
2. **Generate** a migration with `prisma migrate dev --name ...`.
3. **Review** the generated `migration.sql` before committing — especially for changes that can lose data.
4. **Commit** the migration alongside the schema change.

For example, adding an optional field is safe and non-destructive:

```prisma
model User {
  id    Int     @id @default(autoincrement())
  email String  @unique
  name  String?
  bio   String? // newly added, optional -> no existing rows break
}
```

Some changes are potentially destructive — dropping a column, or adding a **required** column to a table that already has rows (existing rows would have no value for it). In these cases `migrate dev` warns you, and you may need to provide a default or write a custom migration to backfill data:

```prisma
model User {
  // Adding a required field to a populated table needs a default
  // (or a manual data-migration step) so existing rows remain valid.
  role String @default("USER")
}
```

Always inspect the SQL Migrate generates for risky changes, and test migrations against a copy of production data before deploying.

### Reference

- [Prisma Migrate overview](https://www.prisma.io/docs/orm/prisma-migrate)
- [Get started with Prisma Migrate](https://www.prisma.io/docs/orm/prisma-migrate/getting-started)
- [Development and production](https://www.prisma.io/docs/orm/prisma-migrate/workflows/development-and-production)
- [Prototype your schema with db push](https://www.prisma.io/docs/orm/prisma-migrate/workflows/prototyping-your-schema)
- [Seeding your database](https://www.prisma.io/docs/orm/prisma-migrate/workflows/seeding)

---

## CRUD Operations

CRUD stands for Create, Read, Update, and Delete — the four basic operations of persistent storage. Prisma Client exposes a consistent set of methods for each of them. The examples below assume the `User` and `Post` models from earlier.

### Creating records

Use `create` to insert a single record. The `data` field holds the values:

```ts
const user = await prisma.user.create({
  data: {
    email: 'dave@prisma.io',
    name: 'Dave',
  },
});
```

Use `createMany` to insert several rows in one query. `skipDuplicates` avoids errors when a unique value already exists:

```ts
await prisma.user.createMany({
  data: [
    { email: 'e@prisma.io', name: 'Eve' },
    { email: 'f@prisma.io', name: 'Frank' },
  ],
  skipDuplicates: true,
});
```

You can also create related records in the same call — a *nested write* (covered more under Relations):

```ts
await prisma.user.create({
  data: {
    email: 'grace@prisma.io',
    name: 'Grace',
    posts: {
      create: [{ title: 'First post' }, { title: 'Second post' }],
    },
  },
});
```

### Reading data

`findMany` returns a list; with no arguments it returns every row:

```ts
const users = await prisma.user.findMany();
```

`findUnique` fetches a single record by a unique field (like the id or a `@unique` column). It returns the record or `null`:

```ts
const user = await prisma.user.findUnique({
  where: { email: 'dave@prisma.io' },
});
```

Use `findUniqueOrThrow` (or `findFirstOrThrow`) if you want an error instead of `null` when nothing matches:

```ts
const user = await prisma.user.findUniqueOrThrow({
  where: { id: 1 },
});
```

`findFirst` returns the first record matching a condition — useful with filters and ordering where the field is not unique:

```ts
const post = await prisma.post.findFirst({
  where: { published: true },
  orderBy: { id: 'desc' },
});
```

### Updating data

`update` modifies a single record identified by a unique `where`:

```ts
const user = await prisma.user.update({
  where: { email: 'dave@prisma.io' },
  data: { name: 'David' },
});
```

`updateMany` updates every record that matches a filter and returns a count:

```ts
const result = await prisma.post.updateMany({
  where: { published: false },
  data: { published: true },
});
console.log(result.count); // number of rows updated
```

`upsert` updates a record if it exists, or creates it if it does not:

```ts
const user = await prisma.user.upsert({
  where: { email: 'dave@prisma.io' },
  update: { name: 'David' },
  create: { email: 'dave@prisma.io', name: 'David' },
});
```

For numeric fields you can use atomic operations instead of reading-then-writing:

```ts
await prisma.post.update({
  where: { id: 1 },
  data: { views: { increment: 1 } },
});
```

### Deleting data

`delete` removes a single record by a unique field:

```ts
const user = await prisma.user.delete({
  where: { email: 'dave@prisma.io' },
});
```

`deleteMany` removes every record matching a filter, and returns a count. With no `where`, it deletes all rows in the table:

```ts
// Delete all unpublished posts
const result = await prisma.post.deleteMany({
  where: { published: false },
});

// Delete everything in the table
await prisma.post.deleteMany();
```

### Reference

- [CRUD](https://www.prisma.io/docs/orm/prisma-client/queries/crud)
- [create](https://www.prisma.io/docs/orm/reference/prisma-client-reference#create)
- [createMany](https://www.prisma.io/docs/orm/reference/prisma-client-reference#createmany)
- [update / updateMany](https://www.prisma.io/docs/orm/reference/prisma-client-reference#update)
- [upsert](https://www.prisma.io/docs/orm/reference/prisma-client-reference#upsert)
- [delete / deleteMany](https://www.prisma.io/docs/orm/reference/prisma-client-reference#delete)

---

## Querying Data

Beyond simple CRUD, Prisma Client offers a rich, composable query API. All of the options below (`where`, `orderBy`, `take`, `select`, `include`) can be combined in a single call.

### Filtering

Use the `where` option to narrow results. Simple equality is just a value:

```ts
const posts = await prisma.post.findMany({
  where: { published: true },
});
```

Prisma provides *filter operators* for richer conditions — `contains`, `startsWith`, `in`, `gt`/`gte`/`lt`/`lte`, `not`, and more:

```ts
const posts = await prisma.post.findMany({
  where: {
    title: { contains: 'prisma', mode: 'insensitive' }, // case-insensitive LIKE
    views: { gte: 100 },                                 // views >= 100
    authorId: { in: [1, 2, 3] },                         // authorId IN (1,2,3)
  },
});
```

Combine conditions with `AND`, `OR`, and `NOT`:

```ts
const posts = await prisma.post.findMany({
  where: {
    OR: [
      { title: { contains: 'prisma' } },
      { content: { contains: 'prisma' } },
    ],
    NOT: { published: false },
  },
});
```

You can also filter by conditions on related records:

```ts
// Users who have at least one published post
const users = await prisma.user.findMany({
  where: {
    posts: { some: { published: true } },
  },
});
```

### Sorting

Use `orderBy` to sort results. Specify `'asc'` or `'desc'`:

```ts
const users = await prisma.user.findMany({
  orderBy: { name: 'asc' },
});
```

Sort by multiple fields by passing an array — earlier entries take priority:

```ts
const posts = await prisma.post.findMany({
  orderBy: [
    { published: 'desc' },
    { title: 'asc' },
  ],
});
```

### Pagination

Prisma supports two pagination styles.

**Offset pagination** with `skip` and `take` — simple, good for jumping to a page number:

```ts
// Page 3, 10 items per page
const posts = await prisma.post.findMany({
  skip: 20, // (page - 1) * pageSize
  take: 10,
  orderBy: { id: 'asc' },
});
```

**Cursor-based pagination** with `cursor` and `take` — more efficient and stable for large or frequently-changing datasets. You remember the last id and continue from it:

```ts
const nextPage = await prisma.post.findMany({
  take: 10,
  skip: 1,               // skip the cursor itself
  cursor: { id: lastSeenId },
  orderBy: { id: 'asc' },
});
```

A negative `take` paginates backwards from the cursor.

### Selecting & including relations

By default a query returns all scalar fields of a model but *no* relations. You control the shape of the result with `select` and `include`.

**`select`** returns only the fields you name (and nothing else):

```ts
const users = await prisma.user.findMany({
  select: {
    email: true,
    name: true,
  },
});
// each result: { email: string, name: string | null }
```

**`include`** returns all scalar fields *plus* the named relations:

```ts
const users = await prisma.user.findMany({
  include: {
    posts: true, // attach each user's posts
  },
});
// each result: User & { posts: Post[] }
```

You can nest and filter inside `select`/`include` — for example, include only each user's published posts, and only their titles:

```ts
const users = await prisma.user.findMany({
  select: {
    name: true,
    posts: {
      where: { published: true },
      select: { title: true },
      orderBy: { title: 'asc' },
    },
  },
});
```

> Note: you cannot use `select` and `include` at the same level in the same query — choose one.

### Reference

- [Filtering and sorting](https://www.prisma.io/docs/orm/prisma-client/queries/filtering-and-sorting)
- [Pagination](https://www.prisma.io/docs/orm/prisma-client/queries/pagination)
- [Select fields](https://www.prisma.io/docs/orm/prisma-client/queries/select-fields)
- [Relation queries](https://www.prisma.io/docs/orm/prisma-client/queries/relation-queries)
- [Prisma Client API reference — where](https://www.prisma.io/docs/orm/reference/prisma-client-reference#filter-conditions-and-operators)

---

## Relations & Data Modeling

Relations describe how models are connected. In the Prisma Schema you declare a relation with a *relation field* (a field whose type is another model) and, on the side that stores the foreign key, the `@relation` attribute.

### One-to-one

In a one-to-one relation, a record on each side relates to at most one record on the other. Example: each `User` has one `Profile`. The side holding the foreign key must mark it `@unique`:

```prisma
model User {
  id      Int      @id @default(autoincrement())
  email   String   @unique
  profile Profile?
}

model Profile {
  id     Int    @id @default(autoincrement())
  bio    String
  user   User   @relation(fields: [userId], references: [id])
  userId Int    @unique // @unique makes it one-to-one
}
```

Querying across the relation:

```ts
const user = await prisma.user.findUnique({
  where: { id: 1 },
  include: { profile: true },
});
```

### One-to-many

In a one-to-many relation, one record relates to many records on the other side. Example: a `User` has many `Post`s, and each `Post` belongs to one `User`. The "many" side (`Post`) stores the foreign key:

```prisma
model User {
  id    Int    @id @default(autoincrement())
  name  String?
  posts Post[] // the "many" side
}

model Post {
  id       Int  @id @default(autoincrement())
  title    String
  author   User @relation(fields: [authorId], references: [id])
  authorId Int  // foreign key
}
```

Create a user with related posts in one nested write, and read them back:

```ts
await prisma.user.create({
  data: {
    name: 'Heidi',
    posts: { create: [{ title: 'A' }, { title: 'B' }] },
  },
});

const withPosts = await prisma.user.findMany({ include: { posts: true } });
```

### Many-to-many

In a many-to-many relation, records on both sides can relate to many records on the other. Example: a `Post` has many `Category`s and a `Category` has many `Post`s.

Prisma supports **implicit** many-to-many, where Prisma manages the join table for you — you just declare a list field on each side:

```prisma
model Post {
  id         Int        @id @default(autoincrement())
  title      String
  categories Category[]
}

model Category {
  id    Int    @id @default(autoincrement())
  name  String
  posts Post[]
}
```

Connect them with `connect` (link existing records) or `create` (make new ones):

```ts
await prisma.post.create({
  data: {
    title: 'Prisma tips',
    categories: {
      connect: [{ id: 1 }],
      create: [{ name: 'Databases' }],
    },
  },
});
```

If you need extra fields on the relationship itself (e.g. an `assignedAt` timestamp), use an **explicit** many-to-many with a dedicated join model that has two one-to-many relations.

### Referential integrity

Referential integrity means the database stays consistent: a foreign key must point to a row that actually exists, and you decide what happens to child rows when a parent is deleted or its key changes. In Prisma you configure this with **referential actions** on the `@relation` attribute — `onDelete` and `onUpdate`:

```prisma
model Post {
  id       Int  @id @default(autoincrement())
  author   User @relation(fields: [authorId], references: [id], onDelete: Cascade)
  authorId Int
}
```

Common `onDelete` actions:

- `Cascade` — delete the child rows when the parent is deleted.
- `Restrict` — prevent deleting the parent while children exist.
- `SetNull` — set the foreign key to `null` (the field must be optional).
- `NoAction` — defer to the database's own behavior.

For an optional relation with `SetNull`, the foreign key must be nullable:

```prisma
model Post {
  id       Int   @id @default(autoincrement())
  author   User? @relation(fields: [authorId], references: [id], onDelete: SetNull)
  authorId Int?
}
```

### Reference

- [Relations](https://www.prisma.io/docs/orm/prisma-schema/data-model/relations)
- [One-to-one relations](https://www.prisma.io/docs/orm/prisma-schema/data-model/relations/one-to-one-relations)
- [One-to-many relations](https://www.prisma.io/docs/orm/prisma-schema/data-model/relations/one-to-many-relations)
- [Many-to-many relations](https://www.prisma.io/docs/orm/prisma-schema/data-model/relations/many-to-many-relations)
- [Referential actions](https://www.prisma.io/docs/orm/prisma-schema/data-model/relations/referential-actions)

---

## Validation & Data Integrity

Keeping data valid is a layered job: the schema and database enforce structural rules, while your application enforces business rules the database can't express.

### Schema-level validation

The Prisma Schema itself enforces the basic shape of your data. Field types, optionality (`?`), and enums restrict what can be stored:

```prisma
enum Role {
  USER
  ADMIN
}

model User {
  id    Int    @id @default(autoincrement())
  email String @unique     // must be present and unique
  name  String?            // optional — may be null
  role  Role   @default(USER) // must be one of the enum values
}
```

Here Prisma (and TypeScript, via the generated client) will reject a `role` that isn't `USER` or `ADMIN`, and require `email` because it is non-optional.

### Constraints & defaults

Database-level constraints guarantee integrity even if data arrives from outside your app. Prisma maps schema attributes to real SQL constraints:

- `@id` — primary key.
- `@unique` — unique constraint on a single field.
- `@@unique([a, b])` — composite unique constraint across multiple fields.
- `@default(...)` — a default value (`autoincrement()`, `now()`, `uuid()`, `cuid()`, literals).
- `@relation(... references: ...)` — a foreign-key constraint.

```prisma
model Membership {
  id        Int      @id @default(autoincrement())
  userId    Int
  teamId    Int
  createdAt DateTime @default(now())

  // A user can belong to a given team only once
  @@unique([userId, teamId])
}
```

Because these constraints live in the database, a violation (e.g. inserting a duplicate email) raises a Prisma error you can catch:

```ts
import { Prisma } from '@prisma/client';

try {
  await prisma.user.create({ data: { email: 'taken@prisma.io' } });
} catch (e) {
  if (e instanceof Prisma.PrismaClientKnownRequestError && e.code === 'P2002') {
    console.error('A user with that email already exists.');
  }
}
```

### Application-level validation

Some rules can't be expressed in the schema — "password must be at least 8 characters", "email must be a valid format", "end date must be after start date". Validate these in your application code *before* calling Prisma, typically with a validation library such as [Zod](https://zod.dev):

```ts
import { z } from 'zod';

const CreateUser = z.object({
  email: z.string().email(),
  name: z.string().min(1).max(50),
  age: z.number().int().min(18),
});

async function createUser(input: unknown) {
  const data = CreateUser.parse(input); // throws if invalid
  return prisma.user.create({ data });
}
```

This gives you a clear separation: schema/database constraints protect data integrity as a last line of defense, while application-level validation produces friendly, specific error messages and enforces business logic.

### Reference

- [Data model](https://www.prisma.io/docs/orm/prisma-schema/data-model/models)
- [Defining attributes](https://www.prisma.io/docs/orm/prisma-schema/data-model/models#defining-attributes)
- [Defining enums](https://www.prisma.io/docs/orm/prisma-schema/data-model/models#defining-enums)
- [Handling exceptions and errors](https://www.prisma.io/docs/orm/prisma-client/debugging-and-troubleshooting/handling-exceptions-and-errors)
- [Custom validation](https://www.prisma.io/docs/orm/prisma-client/queries/custom-validation)

---

## Transactions

### Why transactions matter

A transaction groups several database operations so they succeed or fail as a single unit — the classic guarantee of *atomicity*. If any operation in the group fails, all of them are rolled back and the database is left as if none had run.

The canonical example is a bank transfer: you debit one account and credit another. If the debit succeeds but the credit fails, running them separately would lose money. A transaction ensures either both happen or neither does. Transactions are essential whenever multiple writes must stay consistent with each other.

### Using Prisma transactions

Prisma offers two main ways to run transactions.

**1. The sequential array (`$transaction([...])`)** — pass an array of Prisma operations; they run in order inside one transaction and all commit together, or all roll back:

```ts
const [from, to] = await prisma.$transaction([
  prisma.account.update({
    where: { id: 1 },
    data: { balance: { decrement: 100 } },
  }),
  prisma.account.update({
    where: { id: 2 },
    data: { balance: { increment: 100 } },
  }),
]);
```

**2. The interactive transaction (`$transaction(async (tx) => { ... })`)** — for when later operations depend on earlier results, or you need conditional logic. Prisma gives you a transactional client `tx`; use it for every query inside. Throwing an error rolls everything back:

```ts
await prisma.$transaction(async (tx) => {
  const sender = await tx.account.update({
    where: { id: 1 },
    data: { balance: { decrement: 100 } },
  });

  // Business rule enforced mid-transaction
  if (sender.balance < 0) {
    throw new Error('Insufficient funds'); // rolls back the decrement
  }

  await tx.account.update({
    where: { id: 2 },
    data: { balance: { increment: 100 } },
  });
});
```

Use interactive transactions only for the work that must be atomic — keep them short, since open transactions hold database locks. You can also pass options such as a `timeout` or an isolation level:

```ts
await prisma.$transaction(
  async (tx) => { /* ... */ },
  { timeout: 10000, isolationLevel: 'Serializable' },
);
```

### Reference

- [Transactions and batch queries](https://www.prisma.io/docs/orm/prisma-client/queries/transactions)
- [The $transaction API](https://www.prisma.io/docs/orm/prisma-client/queries/transactions#the-transaction-api)
- [Interactive transactions](https://www.prisma.io/docs/orm/prisma-client/queries/transactions#interactive-transactions)
- [Transaction isolation levels](https://www.prisma.io/docs/orm/prisma-client/queries/transactions#transaction-isolation-level)

---

## Performance & Optimization

### Indexing

An index is a database structure that makes lookups on a column much faster, at the cost of a little extra storage and slightly slower writes. If you frequently filter or sort by a column that isn't already the primary key or unique, add an index.

In Prisma you declare indexes in the schema with `@@index`:

```prisma
model Post {
  id         Int      @id @default(autoincrement())
  title      String
  published  Boolean  @default(false)
  authorId   Int
  createdAt  DateTime @default(now())

  // Speed up queries that filter by author and sort by date
  @@index([authorId, createdAt])
}
```

`@id`, `@unique`, and `@@unique` already create indexes, so you don't need to add `@@index` for those. Design indexes around the queries you actually run — a composite index `[authorId, createdAt]` helps queries that filter by `authorId` (optionally also ordering by `createdAt`).

### Query optimization

A few habits keep queries fast:

**Select only what you need.** Fetching every column of every row wastes bandwidth and memory. Use `select` to return just the fields you use:

```ts
// Instead of the full row, fetch only id + title
const posts = await prisma.post.findMany({
  select: { id: true, title: true },
});
```

**Filter and paginate in the database, not in JavaScript.** Push `where`, `orderBy`, and `take`/`skip` into the query so the database does the work:

```ts
const recent = await prisma.post.findMany({
  where: { published: true },
  orderBy: { createdAt: 'desc' },
  take: 20,
});
```

**Use aggregate/`count` instead of loading rows to count them:**

```ts
const publishedCount = await prisma.post.count({
  where: { published: true },
});
```

You can also inspect what SQL Prisma runs by enabling query logging, which helps find slow or unexpected queries:

```ts
const prisma = new PrismaClient({ log: ['query'] });
```

### Avoiding N+1 queries

The **N+1 problem** happens when you run one query to fetch a list, then one more query per item to fetch its relations — 1 + N queries total. It's a common and serious performance trap:

```ts
// ❌ N+1: 1 query for users, then 1 query PER user for their posts
const users = await prisma.user.findMany();
for (const user of users) {
  const posts = await prisma.post.findMany({ where: { authorId: user.id } });
  // ...
}
```

The fix is to let Prisma fetch the relations in a single, optimized set of queries using `include` (or `select`):

```ts
// ✅ Prisma loads users and their posts efficiently, not one-per-user
const users = await prisma.user.findMany({
  include: { posts: true },
});
```

Prisma also batches identical queries fired in the same tick via its built-in dataloader, and for advanced cases the `relationLoadStrategy` option lets you choose between a database-side `join` and separate queries. The key principle stays the same: fetch related data *with* the parent query, never in a loop.

### Reference

- [Indexes](https://www.prisma.io/docs/orm/prisma-schema/data-model/indexes)
- [Query optimization](https://www.prisma.io/docs/orm/prisma-client/queries/query-optimization-performance)
- [Solving the n+1 problem](https://www.prisma.io/docs/orm/prisma-client/queries/query-optimization-performance#solving-the-n1-problem)
- [Select fields to improve performance](https://www.prisma.io/docs/orm/prisma-client/queries/select-fields)
- [Logging queries](https://www.prisma.io/docs/orm/prisma-client/observability-and-logging/logging)
