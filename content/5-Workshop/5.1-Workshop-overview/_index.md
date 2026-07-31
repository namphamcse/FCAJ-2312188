---
title: "Architecture Overview"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Application architecture

The application is separated into frontend, backend, and database layers so that each can be deployed, secured, and scaled independently:

* **CloudFront + S3:** CloudFront delivers the static frontend from an S3 origin with low latency.
* **ALB + ECS Fargate:** The ALB receives HTTP/HTTPS requests and routes them to an ECS service. Fargate runs containers without managing EC2 servers.
* **RDS PostgreSQL:** The database is placed in private subnets and accepts connections only from the backend.

#### Request flow

1. A user opens the frontend through CloudFront.
2. CloudFront retrieves static files from the S3 origin.
3. The frontend calls the API through the ALB.
4. The ALB routes the request to an ECS Fargate task in a private subnet.
5. The backend reads from or writes to RDS PostgreSQL.

![VPC architecture](/FCAJ-2312188/images/5-Workshop/vpc.jpg?featherlight=false)

![Security group design](/FCAJ-2312188/images/5-Workshop/security_groups.jpg?featherlight=false)
