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
3. The frontend calls `/api/*` through CloudFront.
4. CloudFront forwards the API request to the ALB.
5. The ALB routes the request to an ECS Fargate task in a private subnet.
6. The backend reads from or writes to RDS PostgreSQL.

#### Build and deployment flow

1. Build the backend container image and push a versioned image to Amazon ECR.
2. Create a new ECS task-definition revision that references that image.
3. Update the ECS service and wait for the ALB target to become healthy.
4. Build the frontend static files, upload them to S3, and invalidate CloudFront when cached files must be refreshed.

Each backend release uses a new image and task-definition revision; application files are not changed inside a running Fargate task.
