---
title: "Tạo ECR repository và ECS Fargate service"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

#### Đưa backend image lên ECR

1. Tạo private ECR repository có tên `shopsflow-repo`.
2. Build Docker image cho backend.
3. Xác thực Docker client với ECR, gắn tag cho image rồi push image lên repository.

![ECR backend image](/FCAJ-2312188/images/5-Workshop/ecr_image.jpg?featherlight=false)
*Hình 1. Container image của backend được lưu trong Amazon ECR.*

#### Tạo ECS Fargate service

Tạo ECS cluster `shopsflow-cluster`, sau đó tạo task definition sử dụng image trong `shopsflow-repo`. Thiết lập CPU, memory, container port `8080`, hai private subnet và `ecs-sg`. Cuối cùng, tạo service với số lượng task phù hợp và gắn service vào target group của ALB.

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

Chờ đến khi service triển khai ổn định. Khi ALB target chuyển sang trạng thái healthy, điều đó cho thấy task đang chạy, health-check path phản hồi đúng và ALB có thể kết nối đến backend qua port `8080`.
