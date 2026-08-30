# Common Stack | Applications | Golang | Migrate Documentation

## Author Table

| **Author** | **Created On** | **Version** | **Last Updated By** | **Last Edited On** | **L0 Reviewer** | **L1 Reviewer** | **L2 Reviewer** |
|---|---|---|---|---|---|---|---|
| Rahul Parihar | 30-08-2026 | 1.0 | Rahul Parihar | 30-08-2026 | Annitha | Prashant/Prince | Sandeep Rawat / Ravindra |

## Table of Contents

1. [Introduction](#introduction)
2. [What Is Migrate?](#what-is-migrate)
3. [Why Is Migrate Used?](#why-is-migrate-used)
4. [Key Features of Migrate](#key-features-of-migrate)
5. [Migration File Structure](#migration-file-structure)
6. [Migrate Workflow](#migrate-workflow)
7. [Common Migrate Commands](#common-migrate-commands)
8. [Supported Databases and Sources](#supported-databases-and-sources)
9. [Advantages and Disadvantages](#advantages-and-disadvantages)
10. [Best Practices](#best-practices)
11. [Conclusion](#conclusion)
12. [Contact Information](#contact-information)
13. [References](#references)

## Introduction

As an application evolves, its database schema needs to change alongside it: new tables get added, columns get renamed, and indexes get introduced. Making these changes by hand across development, staging, and production environments is error-prone and hard to track.

Migrate is a database migration tool that manages schema changes as a series of versioned, ordered files. It is commonly used in Go projects, though it works independently of any particular application language.

## What Is Migrate?

Migrate, also known as `golang-migrate`, is an open-source command-line tool and Go library used to apply and roll back database schema changes in a controlled, versioned manner.

Each schema change is written as a pair of migration files, one to apply the change and one to reverse it. Migrate keeps track of which migrations have already run so that the same set of changes is never applied twice.

### Basic Migration File Naming

```text
000001_create_users_table.up.sql
000001_create_users_table.down.sql
```

The numeric prefix defines the order in which migrations run, and the `up` or `down` suffix defines the direction of the change.

## Why Is Migrate Used?

Manually tracking schema changes across environments and team members leads to inconsistent databases and hard-to-reproduce bugs. Migrate addresses this by treating schema changes the same way source code is treated.

| Reason | Description |
|---|---|
| Version control for schema | Every schema change is a file that can be reviewed and committed alongside application code |
| Consistent environments | The same migrations run in development, staging, and production, keeping schemas aligned |
| Safe rollbacks | Down migrations allow a schema change to be reversed if something goes wrong |
| Team collaboration | Multiple developers can add migrations without manually syncing database state |
| Automation friendly | Migrations can run as part of a CI/CD pipeline before deploying application changes |
| Database agnostic | The same workflow applies across several supported database engines |

## Key Features of Migrate

| Feature | Description |
|---|---|
| Up and down migrations | Every change has a matching rollback, so schema changes are reversible |
| Versioned execution | Migrations run in strict numeric order and are tracked in a version table |
| CLI and library usage | Can be run as a standalone binary or imported directly into a Go application |
| Multiple database drivers | Supports PostgreSQL, MySQL, SQLite, MongoDB, and several other engines |
| Multiple source drivers | Migration files can be read from the local filesystem, GitHub, S3, and other sources |
| Dirty state detection | Flags a database if a migration fails partway through, preventing further changes until resolved |
| Force versioning | Allows manually setting the recorded schema version when recovering from a failed migration |
| No external runtime dependency | Ships as a single compiled binary with no separate service to run |

## Migration File Structure

A typical project keeps migration files in a dedicated directory.

```text
my-app/
├── migrations/
│   ├── 000001_create_users_table.up.sql
│   ├── 000001_create_users_table.down.sql
│   ├── 000002_add_email_index.up.sql
│   └── 000002_add_email_index.down.sql
└── main.go
```

### Example Up Migration

```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE
);
```

### Example Down Migration

```sql
DROP TABLE users;
```

## Migrate Workflow

```text
New Schema Change Needed
         |
         v
Create Up and Down Migration Files
         |
         v
Run Migrate Up
         |
         v
Migrate Checks Version Table
         |
         v
Applies Pending Migrations in Order
         |
         v
Updates Version Table
         |
         v
Schema Is Now Up To Date
```

If a change needs to be undone, the same flow runs in reverse using the down migrations and the `migrate down` command.

## Common Migrate Commands

| Command | Purpose |
|---|---|
| `migrate create -ext sql -dir migrations -seq create_users_table` | Creates a new pair of up and down migration files |
| `migrate -path migrations -database <DB_URL> up` | Applies all pending migrations |
| `migrate -path migrations -database <DB_URL> down 1` | Rolls back the most recently applied migration |
| `migrate -path migrations -database <DB_URL> version` | Shows the current schema version |
| `migrate -path migrations -database <DB_URL> force <VERSION>` | Manually sets the schema version without running migrations |
| `migrate -path migrations -database <DB_URL> drop` | Drops all tables in the database |

## Supported Databases and Sources

### Database Drivers

| Database | Notes |
|---|---|
| PostgreSQL | One of the most commonly used drivers |
| MySQL | Widely supported, including MariaDB |
| SQLite | Useful for local development and testing |
| MongoDB | Supports schema-less migration scripts |
| CockroachDB | Compatible through the PostgreSQL driver |

### Migration Source Drivers

| Source | Notes |
|---|---|
| File | Reads migration files from a local directory |
| GitHub | Reads migration files directly from a GitHub repository |
| Amazon S3 | Reads migration files stored in an S3 bucket |
| Google Cloud Storage | Reads migration files from a GCS bucket |

## Advantages and Disadvantages

| Advantages | Disadvantages |
|---|---|
| Schema changes are versioned and reviewable | Requires discipline to always write a matching down migration |
| Works the same way across many database engines | A failed migration can leave the database in a dirty state that needs manual resolution |
| Can run as a CLI tool or an embedded Go library | Raw SQL migrations require the team to know the target database's SQL dialect |
| Integrates cleanly into CI/CD pipelines | Does not generate migrations automatically from schema diffs |
| Lightweight, single-binary distribution | Large teams need a convention to avoid migration numbering conflicts |

## Best Practices

- Always write both the up and down migration for every schema change.
- Keep each migration focused on a single, small change.
- Never edit a migration file after it has been applied to any shared environment.
- Run migrations as part of the deployment pipeline rather than manually on production.
- Use `migrate version` to confirm the current schema state before applying new changes.
- Resolve a dirty database state immediately using `force` before running further migrations.
- Store migration files in source control alongside the application code.
- Use descriptive names for migration files so their purpose is clear at a glance.

## Conclusion

Migrate brings the same discipline used for application source code to database schema changes. By keeping every change as a versioned, reversible file, teams get a consistent, repeatable way to evolve a database across every environment.

Its support for multiple database engines, CLI and library usage, and CI/CD-friendly design make it a practical default for projects that need reliable schema management without adopting a heavier migration framework.

## Contact Information

| Name | Email |
|---|---|
| Rahul Parihar | [rahul.parihar.snaatak@mygurukulam.co](mailto:rahul.parihar.snaatak@mygurukulam.co) |

## References

- [Migrate GitHub Repository](https://github.com/golang-migrate/migrate)
- [Migrate CLI Usage Documentation](https://github.com/golang-migrate/migrate/tree/master/cmd/migrate)
- [Migrate Database Drivers](https://github.com/golang-migrate/migrate/tree/master/database)
- [Migrate Source Drivers](https://github.com/golang-migrate/migrate/tree/master/source)
- [Migrate as a Go Library](https://pkg.go.dev/github.com/golang-migrate/migrate/v4)
