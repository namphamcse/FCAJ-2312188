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

![ALB security group](/FCAJ-2312188/images/5-Workshop/alb_sg_inbound_rules.jpg?featherlight=false)

![ECS security group](/FCAJ-2312188/images/5-Workshop/ecs_sg_inbound_rules.jpg?featherlight=false)

![RDS security group](/FCAJ-2312188/images/5-Workshop/rds_sg_inbound_rules.jpg?featherlight=false)
