---
title: "Tạo RDS PostgreSQL"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

#### Cấu hình database

Tạo RDS PostgreSQL instance `database-shopsflow` trong DB subnet group sử dụng hai private subnet. Tắt public access, gắn `rds-sg` và cấu hình database name, master username cùng mật khẩu. Lưu endpoint và port `5432` để backend sử dụng qua environment variable hoặc secret.

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

Truyền RDS endpoint, port, database name, username và password cho backend tại thời điểm triển khai. Sau khi ECS task khởi động, kiểm tra application log và API response. Connection timeout thường là lỗi network hoặc security group, còn authentication error thường do database name hoặc credential không chính xác.

Chạy database migration cần thiết và thử một API operation đọc–ghi dữ liệu trước khi xem backend đã được triển khai hoàn tất.

Cấu hình datasource của Spring Boot sử dụng RDS endpoint đã triển khai:

```properties
SPRING_DATASOURCE_URL=jdbc:postgresql://<rds-endpoint>:5432/<database>
SPRING_DATASOURCE_USERNAME=<username>
SPRING_DATASOURCE_PASSWORD=<password>
```

Backend chỉ được kết nối đến database qua port `5432` từ ECS task. Không hard-code database credential trong source code.
