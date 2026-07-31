---
title: "Tạo RDS PostgreSQL"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

#### Cấu hình database

Tạo RDS PostgreSQL instance trong DB subnet group sử dụng private subnet. Tắt public access, gắn RDS security group và cấu hình database name, master username cùng mật khẩu. Lưu endpoint và port để backend sử dụng qua environment variable hoặc secret.

![RDS PostgreSQL instance](/FCAJ-2312188/images/5-Workshop/rds.jpg?featherlight=false)

Backend chỉ được kết nối đến database qua port `5432` từ ECS task. Không hard-code database credential trong source code.
