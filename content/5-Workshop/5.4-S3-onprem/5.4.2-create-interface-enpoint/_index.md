---
title: "Create the ECR Repository and ECS Fargate Service"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

#### Push the backend image to ECR

1. Create a private ECR repository named `shopsflow-repo`.
2. Build the Docker image for the backend.
3. Authenticate the Docker client with ECR, tag the image, then push it to the repository.

![ECR backend image](/FCAJ-2312188/images/5-Workshop/ecr_image.jpg?featherlight=false)
*Figure 1. Backend container image stored in Amazon ECR.*

#### Create the ECS Fargate service

Create the ECS cluster `shopsflow-cluster`, then add a task definition that uses the image in `shopsflow-repo`. Configure CPU, memory, container port `8080`, the two private subnets, and `ecs-sg`. Finally, create the service with the required number of tasks and attach it to the ALB target group.

Set the task-definition network mode to `awsvpc`, configure the `awslogs` log driver, and use an IP target group because Fargate tasks have their own network interfaces. Disable public IP assignment for the service.

| Service setting | Value |
|---|---|
| Launch type | Fargate |
| Network mode | `awsvpc` |
| Container and target port | `8080` |
| Target type | `ip` |
| Public IP | Disabled |
| Desired task count | `1` for the current lab deployment |
| Logging | CloudWatch through the `awslogs` driver |

![ECS cluster and service](/FCAJ-2312188/images/5-Workshop/ecs_cluster_service.jpg?featherlight=false)
*Figure 2. ECS Fargate cluster and backend service.*

#### Deployment check

Wait for the service deployment to stabilize. Once the ALB target is healthy, you know the task is running, the health-check path is responding, and the ALB can reach the backend on port `8080`.
