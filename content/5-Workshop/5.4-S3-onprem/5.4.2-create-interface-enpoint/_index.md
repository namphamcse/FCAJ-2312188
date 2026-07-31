---
title: "Create the ECR Repository and ECS Fargate Service"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

#### Push the backend image to ECR

1. Create a private ECR repository.
2. Build the backend Docker image.
3. Authenticate the Docker client with ECR, tag the image, and push it to the repository.

![ECR backend image](/FCAJ-2312188/images/5-Workshop/ecr_image.jpg?featherlight=false)
*Figure 1. Backend container image stored in Amazon ECR.*

#### Create the ECS Fargate service

Create an ECS cluster and task definition that uses the ECR image. Configure CPU, memory, the container port, private subnets, and the ECS security group. Then create the service with the required number of tasks and attach the ALB target group.

Set the task-definition network mode to `awsvpc`, configure the `awslogs` log driver, and use an IP target group because Fargate tasks have their own network interfaces. Disable public IP assignment for the service.

![ECS cluster and service](/FCAJ-2312188/images/5-Workshop/ecs_cluster_service.jpg?featherlight=false)
*Figure 2. ECS Fargate cluster and backend service.*

#### Deployment check

Wait for the service deployment to stabilize. A healthy ALB target confirms that the task is running, the health-check path is responding, and the ALB can reach the backend on port `8080`.
