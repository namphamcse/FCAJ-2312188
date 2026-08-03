---
title: "Cấu hình Application Load Balancer"
date: 2026-07-30
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

#### Tạo Application Load Balancer

Tạo internet-facing ALB `shopsflow-alb` trên hai public subnet. Thêm HTTP listener trên port `80` (và HTTPS `443` khi đã có certificate), tạo IP target group trên port `8080` cho ECS Fargate, rồi cấu hình health check endpoint của backend. Gắn ECS service vào target group để các task được đăng ký tự động.

![Application Load Balancer](/FCAJ-2312188/images/5-Workshop/alb.jpg?featherlight=false)
