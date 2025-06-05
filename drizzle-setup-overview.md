# Drizzle Setup Overview / SOP

## 1. Overview

```
📦 <project root>
 ├ 📂 drizzle
 ├ 📂 src
 │   ├ 📂 db
 │   │  └ 📜 schema.ts
 │   └ 📜 index.ts
 ├ 📜 .env
 ├ 📜 drizzle.config.ts
 ├ 📜 package.json
 └ 📜 tsconfig.json

```

## 2. Parts

- `.env` - db connection secrets
- `drizzle.config.ts` - db configuration settings (contains all the information about your database connection, migration folder and schema files.)
- `src/index.ts` - db connection object
- `src/db/schema.ts` - db table schema
- commands...


## 3. Steps

1. Env - Create a .env file in the root of your project and add your database connection variable:
2. Install
3. Configure
4. Define
5. Connect
6. Commands

### 1. Env

### 2. Install

`pnpm i drizzle-orm drizzle-kit drizzle-zod zod`

### 3. Config

`drizzle.config.ts`
1. `dialect` - flavor of SQL
2. `schema` - schema files location
3. `out` - migration files location
4. `db connection credentials` (database url, auth)

```ts
// drizzle.config.ts
import 'dotenv/config';
import { defineConfig } from 'drizzle-kit';

export default defineConfig({
  out: './drizzle',
  schema: './src/db/schema.ts',
  dialect: 'postgresql',
  dbCredentials: {
    url: process.env.DATABASE_URL!,
  },
});
```

### Define

Create a schema.ts file in the src/db directory and declare your table:

```ts
import { integer, pgTable, varchar } from "drizzle-orm/pg-core";

export const usersTable = pgTable("users", {
  id: integer().primaryKey().generatedAlwaysAsIdentity(),
  name: varchar({ length: 255 }).notNull(),
  age: integer().notNull(),
  email: varchar({ length: 255 }).notNull().unique(),
});
```
