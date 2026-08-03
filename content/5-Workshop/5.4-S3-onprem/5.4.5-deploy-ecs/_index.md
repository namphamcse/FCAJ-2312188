---
title: "Deploy the ECS Fargate Service"
date: 2026-07-30
weight: 5
chapter: false
pre: " <b> 5.4.5. </b> "
---

#### Create the ECS Fargate service

With the RDS database and ALB target group in place, create a service in `shopsflow-cluster` using the task definition from section 5.4.2.

1. Select the two private subnets, attach `ecs-sg`, and disable public IP assignment.
2. Attach the IP target group created for the ALB and set the desired task count to `1` for this lab.
3. Pass the RDS endpoint, port, database name, username, and password to the backend through environment variables or a secret.
4. Deploy the service and wait for the ALB target to become healthy.

| Service setting | Value |
|---|---|
| Launch type | Fargate |
| Target type | `ip` |
| Public IP | Disabled |
| Desired task count | `1` for the current lab deployment |

![ECS cluster and service](/FCAJ-2312188/images/5-Workshop/ecs_cluster_service.jpg?featherlight=false)
*Figure 1. ECS Fargate cluster and backend service.*

#### Confirm the deployment

Once the target is healthy, the task is running, the health-check path is responding, and the ALB can reach the backend on port `8080`.

Run the required database migrations, then test an API operation that reads and writes data. A connection timeout usually points to a network or security-group issue, while an authentication error usually means the database name or credentials are incorrect.

The Spring Boot datasource settings use the deployed RDS endpoint:

```properties
SPRING_DATASOURCE_URL=jdbc:postgresql://<rds-endpoint>:5432/<database>
SPRING_DATASOURCE_USERNAME=<username>
SPRING_DATASOURCE_PASSWORD=<password>
```
