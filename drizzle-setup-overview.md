# Drizzle Setup Overview / SOP

## Overview

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

## Parts

- `.env` - db connection secrets
- `drizzle.config.ts` - db configuration settings (contains all the information about your database connection, migration folder and schema files.)
- `src/index.ts` - db connection object
- `src/db/schema.ts` - db table schema
- commands...


## Steps

1. Env - Create a .env file in the root of your project and add your database connection variable:
2. Install
3. Configure
4. Define
5. Connect
6. Commands

### Env

### Install

`pnpm i drizzle-orm drizzle-kit drizzle-zod zod`

### Config

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

### Connect

- `useDrizzle()` server composable to interact with the database: https://hub.nuxt.com/docs/recipes/drizzle#seed-the-database-optional


### Commands

- `"db:generate": "drizzle-kit generate"`: When running the npm run db:generate command, drizzle-kit will generate the migrations based on server/database/schema.ts and save them in the server/database/migrations directory. Migrations created with npm run db:generate are automatically applied during deployment, preview and when starting the development server.


#### Seeding the DB

You can [add a server task](https://hub.nuxt.com/docs/recipes/drizzle#seed-the-database-optional) to populate your database with initial data.
1. Update your nuxt.config.js
2. Create a new file containing the task
3. To run the seed task, start your dev server and open the Nuxt DevTools. Go to Tasks and you will see the db:seed task ready to run. This will add the seed data to your database and give you the first users to work with.
