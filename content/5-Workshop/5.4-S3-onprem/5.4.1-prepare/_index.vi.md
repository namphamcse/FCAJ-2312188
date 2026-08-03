---
title: "Chuẩn bị network và Security Group"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

#### Network placement

ALB được đặt trong public subnet để nhận traffic từ Internet. ECS Fargate task và RDS PostgreSQL đặt trong private subnet. Chỉ ALB có entry point public; ECS và RDS không nhận truy cập trực tiếp từ Internet.

#### Security Group rules

* **`alb-sg`:** cho phép HTTP `80` và HTTPS `443` từ người dùng hoặc CloudFront; chỉ gửi TCP `8080` đến `ecs-sg`.
* **`ecs-sg`:** chỉ cho phép TCP `8080` từ `alb-sg`; cho phép PostgreSQL `5432` đến database tier và HTTPS outbound đến AWS hoặc payment endpoint.
* **`rds-sg`:** chỉ cho phép TCP `5432` từ `ecs-sg`.

Sử dụng security-group reference thay vì CIDR range cho inbound rule của ECS và RDS. ALB security group cho phép port `80` và `443`; backend dùng port `8080`, còn PostgreSQL dùng port `5432`. Chỉ cấu hình HTTPS listener khi đã có certificate.

![ALB security group](/FCAJ-2312188/images/5-Workshop/alb_sg_inbound_rules.jpg?featherlight=false)
*Hình 1. Inbound rule của security group dành cho Application Load Balancer.*

![ECS security group](/FCAJ-2312188/images/5-Workshop/ecs_sg_inbound_rules.jpg?featherlight=false)
*Hình 2. Inbound rule của security group dành cho ECS service.*

![RDS security group](/FCAJ-2312188/images/5-Workshop/rds_sg_inbound_rules.jpg?featherlight=false)
*Hình 3. Inbound rule của security group dành cho RDS PostgreSQL.*

#### Xác minh network boundary

Hãy kiểm tra để chắc chắn ECS task không có public IP, ALB được liên kết với cả hai public subnet và RDS instance nằm trong private subnet. Từ bên ngoài Internet, client không thể kết nối trực tiếp đến ECS container hoặc PostgreSQL database.
