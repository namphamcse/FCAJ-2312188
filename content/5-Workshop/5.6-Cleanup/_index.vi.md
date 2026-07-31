---
title: "Dọn dẹp tài nguyên"
date: 2026-07-30
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

NAT Gateway, ALB, RDS và Fargate workload đang chạy có thể tiếp tục phát sinh chi phí. Dọn tài nguyên theo dependency order.

#### 1. CloudFront và S3 frontend

1. Disable CloudFront distribution và chờ deployment hoàn tất.
2. Delete distribution.
3. Empty frontend S3 bucket và delete bucket nếu không còn cần thiết.

#### 2. ECS service và task

1. Đặt desired count của ECS service về `0` hoặc delete service.
2. Chờ toàn bộ Fargate task dừng.
3. Delete `shopsflow-cluster` khi không còn service hoặc task.

#### 3. Application Load Balancer

Delete `shopsflow-alb`, listener hoặc rule còn lại và target group sau khi ECS không còn tham chiếu đến chúng.

#### 4. ECR

Giữ `shopsflow-repo` nếu image là artifact cần lưu của project. Khi dọn hoàn toàn, xóa image trước rồi delete repository.

#### 5. RDS

1. Delete `database-shopsflow`.
2. Chọn có hoặc không tạo final snapshot và kiểm tra automated backup hoặc snapshot còn lại.
3. Delete `shopsflow-db-subnet-group` khi không còn database sử dụng.

#### 6. CloudWatch

Delete `/shopsflow/ecs/backend` và các custom alarm hoặc dashboard chỉ khi log cùng monitoring resource không còn cần thiết.

#### 7. IAM

Chỉ delete custom ECS execution role và task role sau khi ECS không còn sử dụng. Không delete account role được share với project khác.

#### 8. NAT Gateway và Elastic IP

1. Delete `shopsflow-nat-a` và `shopsflow-nat-b`.
2. Chờ cả hai NAT Gateway được xóa hoàn tất.
3. Release hai Elastic IP không còn dùng.

#### 9. VPC

Sau khi ALB, RDS, ECS network interface và NAT Gateway đã được xóa, delete custom route table, detach và delete `shopsflow-igw`, xóa subnet cùng custom security group, sau đó delete `shopsflow-vpc`.

#### 10. Kiểm tra Billing

Kiểm tra Billing, Cost Explorer và các resource page liên quan để xác nhận không còn lab workload chạy ngoài ý muốn.

Workshop cho thấy managed service giảm công việc quản trị host, nhưng route, IAM permission, health check, payment callback, log và chi phí vẫn là trách nhiệm vận hành.
