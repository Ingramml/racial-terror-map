---
name: Database Integration
description: Database integration patterns for SQL and NoSQL databases. Use when working with database schemas, queries, migrations, or ORM configurations. Supports PostgreSQL, BigQuery, MongoDB, MySQL with progressive disclosure.
allowed-tools: Read, Glob, Grep
version: 1.0.0
---

# Database Integration

## Purpose
Provide database integration patterns and best practices for multiple database types with progressive disclosure to manage context.

## When This Activates
- User mentions "database", "schema", "query", "migration"
- User working with SQL or NoSQL
- User configuring ORM

## Steps

### Step 1: Detect Database Type
Ask user or detect from project:
- PostgreSQL
- BigQuery
- MongoDB
- MySQL
- Other

### Step 2: Load Appropriate Patterns
Based on database type, load specific pattern file (progressive disclosure)

### Step 3: Provide Guidance
Apply patterns to user's specific use case

---

## Supported Databases
- PostgreSQL (reference/postgres-patterns.md)
- BigQuery (reference/bigquery-patterns.md)
- MongoDB (reference/mongo-patterns.md)
- MySQL (reference/mysql-patterns.md)

---

## Changelog
### Version 1.0.0 (2025-10-20)
- Initial release
- Multi-database support
- Progressive disclosure pattern

---

**End of Skill**
