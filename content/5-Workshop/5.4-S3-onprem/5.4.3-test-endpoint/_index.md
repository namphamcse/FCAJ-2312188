---
title: "Create RDS PostgreSQL"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

#### Configure the database

Create an RDS PostgreSQL instance in a DB subnet group that uses private subnets. Disable public access, attach the RDS security group, and configure the database name, master username, and password. Store the endpoint and port for the backend through environment variables or a secret.

![RDS PostgreSQL instance](/FCAJ-2312188/images/5-Workshop/rds.jpg?featherlight=false)

The backend can connect to the database only on port `5432` from an ECS task. Do not hard-code database credentials in source code.
