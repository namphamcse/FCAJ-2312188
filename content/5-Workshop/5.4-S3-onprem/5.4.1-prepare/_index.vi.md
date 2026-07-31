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

* **ALB SG:** cho phép HTTP/HTTPS từ người dùng hoặc CloudFront.
* **ECS SG:** chỉ cho phép application port từ ALB SG.
* **RDS SG:** chỉ cho phép TCP `5432` từ ECS SG.

Sử dụng security-group reference thay vì CIDR range cho inbound rule của ECS và RDS. Ứng dụng đã triển khai dùng ALB listener port `80` và `443`, backend port `8080`, cùng PostgreSQL port `5432`.

![ALB security group](/FCAJ-2312188/images/5-Workshop/alb_sg_inbound_rules.jpg?featherlight=false)
*Hình 1. Inbound rule của security group dành cho Application Load Balancer.*

![ECS security group](/FCAJ-2312188/images/5-Workshop/ecs_sg_inbound_rules.jpg?featherlight=false)
*Hình 2. Inbound rule của security group dành cho ECS service.*

![RDS security group](/FCAJ-2312188/images/5-Workshop/rds_sg_inbound_rules.jpg?featherlight=false)
*Hình 3. Inbound rule của security group dành cho RDS PostgreSQL.*

#### Xác minh network boundary

Kiểm tra ECS task không có public IP, ALB được associate với cả hai public subnet và RDS instance được associate với private subnet. Client bên ngoài không được phép kết nối trực tiếp vào ECS container hoặc PostgreSQL database.
