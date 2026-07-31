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

#### Create the ECS Fargate service

Create an ECS cluster and task definition that uses the ECR image. Configure CPU, memory, the container port, private subnets, and the ECS security group. Then create the service with the required number of tasks and attach the ALB target group.

![ECS cluster and service](/FCAJ-2312188/images/5-Workshop/ecs_cluster_service.jpg?featherlight=false)
