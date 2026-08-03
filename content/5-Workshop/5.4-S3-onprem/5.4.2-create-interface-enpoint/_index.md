---
title: "Create the ECR Repository and ECS Task Definition"
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

#### Create the ECS task definition

Create the ECS cluster `shopsflow-cluster`, then add a task definition that uses the image in `shopsflow-repo`. Configure the CPU, memory, container port `8080`, and the task execution and task roles prepared in section 5.2.

Set the task-definition network mode to `awsvpc` and configure the `awslogs` log driver. The service network settings, RDS connection details, and ALB target group are added after those resources are ready.

| Task-definition setting | Value |
|---|---|
| Network mode | `awsvpc` |
| Container port | `8080` |
| Logging | CloudWatch through the `awslogs` driver |
