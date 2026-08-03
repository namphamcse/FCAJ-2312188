---
title: "Cấu hình ALB và kiểm thử backend"
date: 2026-07-30
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

#### Tạo Application Load Balancer

Tạo internet-facing ALB `shopsflow-alb` trên hai public subnet. Thêm HTTP listener trên port `80` (và HTTPS `443` khi đã có certificate), tạo IP target group trên port `8080` cho ECS Fargate, rồi cấu hình health check endpoint của backend. Gắn ECS service vào target group để các task được đăng ký tự động.

![Application Load Balancer](/FCAJ-2312188/images/5-Workshop/alb.jpg?featherlight=false)

#### Kiểm thử

1. Trước tiên, xác nhận target trong target group đang ở trạng thái healthy.
2. Gọi API qua ALB DNS name để kiểm tra response từ backend.
3. Nếu health check hoặc request thất bại, xem log của ECS task để tìm nguyên nhân.

ALB là backend entry point. Direct ALB call hữu ích cho việc kiểm tra; frontend public gọi `/api/*` qua CloudFront behavior đã cấu hình tại mục 5.3.2. Nếu target unhealthy, trước hết kiểm tra health-check path và port, sau đó xem ECS task stopped reason cùng application log. Cách này giúp phân biệt lỗi ALB routing với lỗi container khởi động hoặc lỗi kết nối database.
