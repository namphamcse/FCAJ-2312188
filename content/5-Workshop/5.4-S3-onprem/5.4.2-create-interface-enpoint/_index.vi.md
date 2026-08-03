---
title: "Tạo ECR repository và ECS task definition"
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

#### Tạo ECS task definition

Tạo ECS cluster `shopsflow-cluster`, sau đó tạo task definition sử dụng image trong `shopsflow-repo`. Thiết lập CPU, memory, container port `8080` cùng task execution role và task role đã chuẩn bị ở mục 5.2.

Đặt network mode của task definition là `awsvpc` và cấu hình `awslogs` log driver. Network setting của service, thông tin kết nối RDS và ALB target group sẽ được bổ sung sau khi các tài nguyên này đã sẵn sàng.

| Cấu hình task definition | Giá trị |
|---|---|
| Network mode | `awsvpc` |
| Container port | `8080` |
| Logging | CloudWatch qua `awslogs` driver |
