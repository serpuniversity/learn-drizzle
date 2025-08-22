# drizzle setup

1. configure database credentials - `drizzle.config.ts`, example:
```ts
import type { Config } from "drizzle-kit";

const config: Config = {
  schema: "path/to/schema/files",
  out: "path/to/where/we/want/the/generated/migrations/files/to/go",
  dialect: "sqlite",
  driver: "d1-http",
  dbCredentials: {
    accountId: process.env.CLOUDFLARE_ACCOUNT_ID!,
    databaseId: process.env.CLOUDFLARE_DATABASE_ID!,
    token: process.env.CLOUDFLARE_D1_TOKEN!,
  },
}

export default config satisfies Config;
```
2. setup your `.env` file with the necessary env variables
3. setup your "scripts" in your package.json
```json
"db:studio": "drizzle-kit studio",
"db:generate": "drizzle-kit generate",
"db:migrate": "drizzle-kit migrate",
```

4. setup `wrangler.toml` file with the d1 bindings
```toml
[[d1_databases]]
binding = "DB"
database_name = "your-db-name-in-cloudflare"
database_id = "some-db-id-here-that-you-get-from-cloudflare"
preview_database_id = "some-preview-db-id-here-that-you-get-from-cloudflare"
```

5. generate the binding TYPES by running `wrangler types --env-interface Cloudfl.... (see [this video](https://www.youtube.com/watch?v=bNJtfFfW6tE) to get the rest of the commands)

## codebase first workflow

- The source of truth for your table schemas sits inside your codebase
- You use your codebase (and commands) to manage database migrations, etc.
- Harder to scale beyond a single developer for a project bc its easier for schemas to become out of sync
- uses the `drizzle-kit generate & migrate` commands to manage schema changes and migrations

> PROTIP: If you want to keep your schemas in sync with a codebase first approach you need to commit your drizzle migrations output and schemas after each migration change

### steps

1. create your `schema.ts` file that contains all your table schemas
  - `database/schemas/index.ts` or whatever you want the file to be at

2. define your db `connection`
```ts
const connection = await mysql.createConnection({
  host: "host",
  user: "user",
  database: "database",
  ...
})

const db = drizzle({client: connection})
```

3. write your type-safe database queries behind an API or serverside compontn
```ts
app.get('/users', async (c) => {
  const db = drizzle(c.env.DB)
  const userData = await db.select().from(users).all()
  return c.html(<UserList users={userData />)
})
```

## database first workflow

- Professional development teams usually have a centralized tool to manage table schema changes. When creating or changing a schema there is typically an approval process with a series of stages before changes go into effect. It is Then up to the developers using the table to understand the shape of the data.
- uses `drizzle-kit pull` command to manage schema changes
