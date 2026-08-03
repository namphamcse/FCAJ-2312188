---
title: "Tạo RDS PostgreSQL"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

#### Cấu hình database

Tạo RDS PostgreSQL instance `database-shopsflow` trong DB subnet group gồm hai private subnet. Tắt public access, gắn `rds-sg`, rồi thiết lập database name, master username và mật khẩu. Lưu lại endpoint cùng port `5432` để backend sử dụng qua environment variable hoặc secret.

| Cấu hình database | Giá trị |
|---|---|
| Engine | PostgreSQL |
| DB identifier | `database-shopsflow` |
| DB subnet group | `shopsflow-db-subnet-group` |
| Public access | Disabled |
| Security group | `rds-sg` |
| Port | `5432` |

![RDS PostgreSQL instance](/FCAJ-2312188/images/5-Workshop/rds.jpg?featherlight=false)

Backend chỉ được kết nối đến database qua port `5432` từ ECS task. Không hard-code database credential trong source code; sử dụng endpoint và credential này khi triển khai ECS service ở mục 5.4.5.
