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

#### Xác thực kết nối database

Khi triển khai backend, truyền vào RDS endpoint, port, database name, username và password. Sau khi ECS task khởi động, kiểm tra application log và API response. Lỗi connection timeout thường liên quan đến network hoặc security group; còn authentication error thường xuất phát từ database name hoặc credential chưa chính xác.

Chạy các database migration cần thiết, rồi thử một API operation đọc–ghi dữ liệu trước khi xác nhận backend đã triển khai hoàn tất.

Cấu hình datasource của Spring Boot sử dụng RDS endpoint đã triển khai:

```properties
SPRING_DATASOURCE_URL=jdbc:postgresql://<rds-endpoint>:5432/<database>
SPRING_DATASOURCE_USERNAME=<username>
SPRING_DATASOURCE_PASSWORD=<password>
```

Backend chỉ được kết nối đến database qua port `5432` từ ECS task. Không hard-code database credential trong source code.
