---
title: "Chuẩn bị VPC và Security Group"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

#### Mục tiêu chuẩn bị

Tạo network foundation cho ứng dụng với hai public subnet dành cho ALB và hai private subnet dành cho ECS Fargate cùng RDS PostgreSQL. Các subnet được đặt ở nhiều Availability Zone để tăng khả năng sẵn sàng.

#### Các thành phần mạng

* **Public subnets:** Chứa Application Load Balancer; các subnet này có route đến Internet Gateway.
* **Private subnets:** Chứa ECS task và RDS instance; database không có public IP.
* **Security groups:** Chỉ cho phép traffic theo đúng luồng CloudFront → ALB → ECS → RDS.

![VPC architecture](/FCAJ-2312188/images/5-Workshop/vpc.jpg?featherlight=false)

![Public subnet 1](/FCAJ-2312188/images/5-Workshop/public_subnet_1.jpg?featherlight=false)

![Public subnet 2](/FCAJ-2312188/images/5-Workshop/public_subnet_2.jpg?featherlight=false)

![Private subnet 1](/FCAJ-2312188/images/5-Workshop/private_subnet_1.jpg?featherlight=false)

![Private subnet 2](/FCAJ-2312188/images/5-Workshop/private_subnet_2.jpg?featherlight=false)

![Security group design](/FCAJ-2312188/images/5-Workshop/security_groups.jpg?featherlight=false)

#### Nguyên tắc bảo mật

* ALB security group chỉ mở port ứng dụng cần thiết từ Internet hoặc CloudFront.
* ECS security group chỉ cho phép inbound từ ALB security group.
* RDS security group chỉ cho phép PostgreSQL port `5432` từ ECS security group.
