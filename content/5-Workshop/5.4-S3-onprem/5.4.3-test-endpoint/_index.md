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

The backend can connect to the database only on port `5432` from an ECS task. Do not hard-code database credentials in source code.

#### Validate the database connection

At deployment time, pass the RDS endpoint, port, database name, username, and password to the backend. Once the ECS task starts, check the application logs and API responses. A connection timeout usually points to a network or security-group issue, while an authentication error usually means the database name or credentials are incorrect.

Run the required database migrations, then test an API operation that reads and writes data before marking the backend deployment as complete.

The Spring Boot datasource settings use the deployed RDS endpoint:

```properties
SPRING_DATASOURCE_URL=jdbc:postgresql://<rds-endpoint>:5432/<database>
SPRING_DATASOURCE_USERNAME=<username>
SPRING_DATASOURCE_PASSWORD=<password>
```
