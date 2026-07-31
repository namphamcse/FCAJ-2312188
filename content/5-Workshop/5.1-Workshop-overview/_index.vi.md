---
title: "Tổng quan kiến trúc"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Kiến trúc ứng dụng

Ứng dụng được tách thành frontend, backend và database để mỗi tầng có thể được triển khai, bảo mật và mở rộng độc lập:

* **CloudFront + S3:** CloudFront phân phối static frontend từ S3 đến người dùng với độ trễ thấp.
* **ALB + ECS Fargate:** ALB nhận HTTP/HTTPS request và điều hướng đến ECS service. Fargate chạy container mà không cần quản lý EC2 server.
* **RDS PostgreSQL:** Database nằm trong private subnet và chỉ chấp nhận kết nối từ backend.

#### Luồng xử lý

1. Người dùng truy cập frontend qua CloudFront.
2. CloudFront lấy các file tĩnh từ S3 origin.
3. Frontend gọi API đến ALB.
4. ALB chuyển request đến ECS Fargate task trong private subnet.
5. Backend đọc hoặc ghi dữ liệu vào RDS PostgreSQL.

![VPC architecture](/FCAJ-2312188/images/5-Workshop/vpc.jpg?featherlight=false)

![Security group design](/FCAJ-2312188/images/5-Workshop/security_groups.jpg?featherlight=false)
