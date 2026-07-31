---
title: "Tạo ECR repository và ECS Fargate service"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

#### Đưa backend image lên ECR

1. Tạo ECR private repository.
2. Build Docker image của backend.
3. Authenticate Docker client với ECR, tag image và push image lên repository.

![ECR backend image](/FCAJ-2312188/images/5-Workshop/ecr_image.jpg?featherlight=false)

#### Tạo ECS Fargate service

Tạo ECS cluster và task definition sử dụng ECR image. Cấu hình CPU, memory, container port, private subnet và ECS security group. Sau đó tạo service với số lượng task mong muốn và gắn target group của ALB.

![ECS cluster and service](/FCAJ-2312188/images/5-Workshop/ecs_cluster_service.jpg?featherlight=false)
