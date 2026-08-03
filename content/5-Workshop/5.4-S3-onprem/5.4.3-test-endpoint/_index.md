---
title: "Create RDS PostgreSQL"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

#### Configure the database

Create the RDS PostgreSQL instance `database-shopsflow` in a DB subnet group spanning the two private subnets. Disable public access, attach `rds-sg`, then set the database name, master username, and password. Store the endpoint and port `5432` for the backend in environment variables or a secret.

| Database setting | Value |
|---|---|
| Engine | PostgreSQL |
| DB identifier | `database-shopsflow` |
| DB subnet group | `shopsflow-db-subnet-group` |
| Public access | Disabled |
| Security group | `rds-sg` |
| Port | `5432` |

![RDS PostgreSQL instance](/FCAJ-2312188/images/5-Workshop/rds.jpg?featherlight=false)

The backend can connect to the database only on port `5432` from an ECS task. Do not hard-code database credentials in source code; use the endpoint and credentials when deploying the ECS service in section 5.4.5.
