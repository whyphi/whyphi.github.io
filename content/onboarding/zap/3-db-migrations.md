---
title: Database Migrations
type: docs
next: onboarding/zap/4-structure
prev: onboarding/zap/2-development
weight: 3
---

Zap uses **Supabase [Declarative Migrations](https://supabase.com/docs/guides/local-development/declarative-database-schemas)** to version-control schema changes. To keep our environments consistent and stable:

{{< callout  >}}
✅ All migrations must be created and tested **locally only**.  
🚫 Never push manual changes directly to the staging or production databases.
{{< /callout >}}

Our CI/CD pipeline applies migrations to staging or production **only when your branch is merged into `staging` or `main`**, respectively.

## Folder Structure

We use a **declarative schema model**, where schema files live under `zap/supabase/schemas/` and migration files are auto-generated via diffs:

```bash
zap/supabase/
├── config.toml
├── schemas/
│   ├── users.sql
│   └── listings.sql
├── migrations/
│   └── 20250608190336_baseline_schemas.sql
└── seed.sql
```

## Create a Migration

{{% steps %}}

### Stop the local Supabase instance

```bash
supabase stop
```

### Make schema changes

Edit or add `.sql` files inside the `supabase/schemas/` folder. For example:

```sql
-- supabase/schemas/listings.sql
create table "listings" (
  "id" uuid primary key,
  "title" text not null,
  "created_at" timestamp with time zone default now()
  "is_active" boolean -- Edit: added new column
);
```

### Generate a migration

Supabase allowes you to generate a migration by diffing against the current DB state:

```bash
supabase db diff -f <migration_name>
```

This will create a file like:

```bash
supabase/migrations/xxxxxxxxxxxxxx_<migration_name>.sql
```

### Verify the migration

Review the generated SQL for unintended changes. The `db diff` command is smart, but not perfect — always inspect!

### Test the migration locally

```bash
supabase db reset
```

{{< callout type="warning" >}}
This will destroy your local data and reapply all migrations.
{{< /callout >}}

### Commit your schema and migration files

```bash
git add supabase/schemas/*.sql supabase/migrations/*.sql
git commit -m "add listings table"
```

Once merged into `staging` or `main`, GitHub Actions will apply these changes to the appropriate Supabase project.

{{% /steps %}}

## Optional: Seeding Data

If you want to provide seed data for development:

- Edit the `supabase/seed.sql` file.
- This script runs after all migrations during local resets.

Keep seed data lightweight and relevant to development testing.

## Pro Tips

- Always **append columns to the end** of tables to avoid noisy diffs.
- Use the `config.toml` to control schema ordering if there are dependencies between tables.

```toml
# supabase/config.toml
[db.migrations]
schema_paths = [
  "./schemas/users.sql",
  "./schemas/*.sql"
]
```
