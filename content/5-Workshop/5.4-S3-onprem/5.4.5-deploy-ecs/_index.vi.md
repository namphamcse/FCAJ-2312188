---
title: "Triển khai ECS Fargate service"
date: 2026-07-30
weight: 5
chapter: false
pre: " <b> 5.4.5. </b> "
---

#### Tạo ECS Fargate service

Sau khi RDS database và ALB target group đã sẵn sàng, tạo service trong `shopsflow-cluster` bằng task definition ở mục 5.4.2.

1. Chọn hai private subnet, gắn `ecs-sg` và tắt public IP assignment.
2. Gắn IP target group đã tạo cho ALB, rồi đặt desired task count là `1` cho lab này.
3. Truyền RDS endpoint, port, database name, username và password cho backend qua environment variable hoặc secret.
4. Triển khai service và chờ ALB target chuyển sang trạng thái healthy.

| Cấu hình service | Giá trị |
|---|---|
| Launch type | Fargate |
| Target type | `ip` |
| Public IP | Disabled |
| Desired task count | `1` cho bản triển khai lab hiện tại |

![ECS cluster and service](/FCAJ-2312188/images/5-Workshop/ecs_cluster_service.jpg?featherlight=false)
*Hình 1. ECS Fargate cluster và backend service.*

#### Xác nhận triển khai

Khi target chuyển sang trạng thái healthy, task đã chạy, health-check path phản hồi đúng và ALB có thể kết nối đến backend qua port `8080`.

Chạy các database migration cần thiết, rồi thử một API operation đọc–ghi dữ liệu. Lỗi connection timeout thường liên quan đến network hoặc security group; còn authentication error thường xuất phát từ database name hoặc credential chưa chính xác.

Cấu hình datasource của Spring Boot sử dụng RDS endpoint đã triển khai:

```properties
SPRING_DATASOURCE_URL=jdbc:postgresql://<rds-endpoint>:5432/<database>
SPRING_DATASOURCE_USERNAME=<username>
SPRING_DATASOURCE_PASSWORD=<password>
```
