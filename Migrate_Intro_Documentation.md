# Common Stack | Applications | Golang | Migrate Documentation

## Author Table

| **Author** | **Created On** | **Version** | **Last Updated By** | **Last Edited On** | **L0 Reviewer** | **L1 Reviewer** | **L2 Reviewer** |
|---|---|---|---|---|---|---|---|
| Rahul Parihar | 30-08-2026 | 1.0 | Rahul Parihar | 30-08-2026 | Annitha | Prashant/Prince | Sandeep Rawat / Ravindra |

## Table of Contents

1. [Introduction](#introduction)
2. [What Is Migrate?](#what-is-migrate)
3. [Why Is Migrate Used?](#why-is-migrate-used)
4. [Key Features](#key-features)
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

As an application evolves, its database schema needs to change alongside it. Making these changes by hand across environments is error-prone and hard to track.

Migrate manages schema changes as versioned, ordered files. It is commonly used in Go projects but works independently of any language.

## What Is Migrate?

Migrate (`golang-migrate`) is an open-source CLI tool and Go library used to apply and roll back database schema changes in a controlled, versioned way.

Each change is written as a pair of files, one to apply it and one to reverse it.

```text
000001_create_users_table.up.sql
000001_create_users_table.down.sql
```

## Why Is Migrate Used?

A few reasons teams adopt Migrate:

| Reason | Description |
|---|---|
| Version control for schema | Changes are files that can be reviewed and committed |
| Consistent environments | Same migrations run in dev, staging, and production |
| Safe rollbacks | Down migrations reverse a change if something goes wrong |
| CI/CD friendly | Runs as part of a deployment pipeline |

## Key Features

Some of Migrate's core capabilities:

| Feature | Description |
|---|---|
| Up and down migrations | Every change has a matching rollback |
| Versioned execution | Migrations run in order and are tracked in a version table |
| CLI and library usage | Runs standalone or embedded in a Go application |
| Multiple drivers | Supports several databases and file sources |
| Dirty state detection | Flags a database if a migration fails midway |

## Migration File Structure

```text
my-app/
├── migrations/
│   ├── 000001_create_users_table.up.sql
│   └── 000001_create_users_table.down.sql
└── main.go
```

```sql
-- up
CREATE TABLE users (id SERIAL PRIMARY KEY, name VARCHAR(255), email VARCHAR(255) UNIQUE);

-- down
DROP TABLE users;
```

## Migrate Workflow

```text
Schema Change Needed
        |
        v
Create Up/Down Files
        |
        v
migrate up
        |
        v
Check Version Table
        |
        v
Apply Pending Migrations
        |
        v
Schema Up To Date
```

To undo a change, run `migrate down`, which reverses the same flow using the down files.

## Common Migrate Commands

| Command | Purpose |
|---|---|
| `migrate create -ext sql -dir migrations -seq name` | Creates a new migration pair |
| `migrate -path migrations -database <DB_URL> up` | Applies pending migrations |
| `migrate -path migrations -database <DB_URL> down 1` | Rolls back the last migration |
| `migrate -path migrations -database <DB_URL> version` | Shows the current schema version |

## Supported Databases and Sources

| Databases | Sources |
|---|---|
| PostgreSQL, MySQL, SQLite | Local filesystem |
| MongoDB, CockroachDB | GitHub, S3, GCS |

## Advantages and Disadvantages

| Advantages | Disadvantages |
|---|---|
| Schema changes are versioned and reviewable | Needs a matching down migration every time |
| Works across many database engines | A failed run can leave a dirty state to resolve |
| CLI or embedded library usage | No auto-generation from schema diffs |
| CI/CD friendly, single binary | Large teams need a numbering convention |

## Best Practices

- Always write both the up and down migration.
- Keep each migration focused on one small change.
- Never edit a migration after it runs in a shared environment.
- Run migrations through the deployment pipeline, not manually.
- Resolve a dirty state with `force` before continuing.

## Conclusion

Migrate brings version control discipline to database schema changes, keeping every change reversible and consistent across environments.

Its multi-database support and CI/CD-friendly design make it a practical default for reliable schema management.

## Contact Information

| Name | Email |
|---|---|
| Rahul Parihar | [rahul.parihar.snaatak@mygurukulam.co](mailto:rahul.parihar.snaatak@mygurukulam.co) |

## References

| Reference | Link |
|---|---|
| Migrate GitHub Repository | https://github.com/golang-migrate/migrate |
| CLI Usage Documentation | https://github.com/golang-migrate/migrate/tree/master/cmd/migrate |
| Migrate as a Go Library | https://pkg.go.dev/github.com/golang-migrate/migrate/v4 |
