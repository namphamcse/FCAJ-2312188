---
title: "Tạo ECR repository và ECS Fargate service"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

#### Đưa backend image lên ECR

1. Tạo ECR private repository `shopsflow-repo`.
2. Build Docker image của backend.
3. Authenticate Docker client với ECR, tag image và push image lên repository.

![ECR backend image](/FCAJ-2312188/images/5-Workshop/ecr_image.jpg?featherlight=false)
*Hình 1. Container image của backend được lưu trong Amazon ECR.*

#### Tạo ECS Fargate service

Tạo ECS cluster `shopsflow-cluster` và task definition sử dụng ECR image từ `shopsflow-repo`. Cấu hình CPU, memory, container port `8080`, hai private subnet và `ecs-sg`. Sau đó tạo service với số lượng task mong muốn và gắn target group của ALB.

Đặt network mode của task definition là `awsvpc`, cấu hình `awslogs` log driver và dùng IP target group vì Fargate task có network interface riêng. Tắt public IP assignment cho service.

| Cấu hình service | Giá trị |
|---|---|
| Launch type | Fargate |
| Network mode | `awsvpc` |
| Container và target port | `8080` |
| Target type | `ip` |
| Public IP | Disabled |
| Desired task count | `1` cho bản triển khai lab hiện tại |
| Logging | CloudWatch qua `awslogs` driver |

![ECS cluster and service](/FCAJ-2312188/images/5-Workshop/ecs_cluster_service.jpg?featherlight=false)
*Hình 2. ECS Fargate cluster và backend service.*

#### Kiểm tra triển khai

Chờ service deployment ổn định. ALB target ở trạng thái healthy xác nhận task đang chạy, health-check path phản hồi và ALB có thể kết nối đến backend qua port `8080`.
