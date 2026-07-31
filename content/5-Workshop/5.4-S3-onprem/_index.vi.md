---
title: "Triển khai backend với ALB, ECS Fargate và RDS PostgreSQL"
date: 2026-07-30
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Tổng quan

Backend được đóng gói thành Docker image, lưu trong Amazon ECR và chạy bằng ECS Fargate service. ALB chạy ở public subnet để nhận request API và gửi đến ECS task ở private subnet. Backend kết nối tới RDS PostgreSQL qua port `5432`.

#### Nội dung

1. [Chuẩn bị network và Security Group](5.4.1-prepare/)
2. [Tạo ECR repository và ECS Fargate service](5.4.2-create-interface-enpoint/)
3. [Tạo RDS PostgreSQL](5.4.3-test-endpoint/)
4. [Cấu hình ALB và kiểm thử backend](5.4.4-dns-simulation/)
