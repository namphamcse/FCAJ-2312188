---
title: "Cấu hình ALB và kiểm thử backend"
date: 2026-07-30
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

#### Tạo Application Load Balancer

Tạo internet-facing ALB trên hai public subnet. Tạo listener cho HTTP hoặc HTTPS, target group kiểu IP cho ECS Fargate và cấu hình health check endpoint của backend. Gắn ECS service vào target group để task được đăng ký tự động.

![Application Load Balancer](/FCAJ-2312188/images/5-Workshop/alb.jpg?featherlight=false)

#### Kiểm thử

1. Xác nhận target trong target group ở trạng thái healthy.
2. Gọi API qua ALB DNS name và kiểm tra response của backend.
3. Kiểm tra log của ECS task nếu health check hoặc request thất bại.

ALB là entry point của backend; frontend cần dùng ALB DNS name hoặc custom API domain để gọi API.
