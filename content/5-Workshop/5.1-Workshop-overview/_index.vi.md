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

#### Cấu hình dự án

| Cấu hình | Giá trị |
|---|---|
| Ứng dụng | Shopsflow, ứng dụng thương mại điện tử |
| Frontend | React/Vite static build |
| Backend | Spring Boot container trên ECS Fargate |
| Database | Amazon RDS for PostgreSQL |
| Region triển khai | `us-east-1` |
| Public entry point | [CloudFront distribution](https://d2m34udjfc5fxq.cloudfront.net/) |

![Kiến trúc AWS Shopsflow](/FCAJ-2312188/images/5-Workshop/architecture.png?featherlight=false)
*Hình 1. Kiến trúc logic của Shopsflow. Bản triển khai thực tế dùng `us-east-1`; các cấu hình trong những mục sau phản ánh tài nguyên đã triển khai.*

#### Luồng xử lý

1. Người dùng truy cập frontend qua CloudFront.
2. CloudFront lấy các file tĩnh từ S3 origin.
3. Frontend gọi `/api/*` qua CloudFront.
4. CloudFront chuyển tiếp API request đến ALB.
5. ALB chuyển request đến ECS Fargate task trong private subnet.
6. Backend đọc hoặc ghi dữ liệu vào RDS PostgreSQL.

#### Luồng build và triển khai

1. Build backend container image và push image có version lên Amazon ECR.
2. Tạo revision mới của ECS task definition sử dụng image đó.
3. Cập nhật ECS service và chờ target của ALB trở thành healthy.
4. Build các static file của frontend, upload lên S3 và tạo CloudFront invalidation khi cần làm mới file đã được cache.

Mỗi lần phát hành backend sử dụng image và task-definition revision mới; không chỉnh sửa file ứng dụng bên trong Fargate task đang chạy.
