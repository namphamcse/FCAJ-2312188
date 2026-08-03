---
title: "Dọn dẹp tài nguyên"
date: 2026-07-30
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

NAT Gateway, ALB, RDS và Fargate workload vẫn có thể phát sinh chi phí khi đang chạy. Vì vậy, hãy dọn dẹp tài nguyên theo thứ tự phụ thuộc dưới đây.

#### 1. CloudFront và S3 frontend

1. Disable CloudFront distribution và chờ đến khi quá trình triển khai hoàn tất.
2. Sau đó, delete distribution.
3. Nếu không cần giữ lại frontend, hãy empty S3 bucket rồi delete bucket.

#### 2. ECS service và task

1. Đặt desired count của ECS service về `0`, hoặc delete service nếu không còn sử dụng.
2. Chờ đến khi tất cả Fargate task đã dừng hẳn.
3. Khi cluster không còn service hoặc task nào, delete `shopsflow-cluster`.

#### 3. Application Load Balancer

Sau khi ECS không còn tham chiếu đến chúng, hãy delete `shopsflow-alb`, các listener hoặc rule còn lại, rồi đến target group.

#### 4. ECR

Giữ lại `shopsflow-repo` nếu image vẫn là artifact cần lưu của project. Nếu muốn dọn hoàn toàn, hãy xóa image trước rồi mới delete repository.

#### 5. RDS

1. Delete `database-shopsflow`.
2. Chọn có hoặc không tạo final snapshot, rồi kiểm tra các automated backup và snapshot còn lại.
3. Khi không còn database nào sử dụng, delete `shopsflow-db-subnet-group`.

#### 6. CloudWatch

Chỉ delete `/shopsflow/ecs/backend`, các custom alarm và dashboard khi những log hoặc monitoring resource này không còn cần thiết.

#### 7. IAM

Chỉ delete custom ECS execution role và task role khi ECS không còn sử dụng. Không delete account role đang được dùng chung với project khác.

#### 8. NAT Gateway và Elastic IP

1. Delete `shopsflow-nat-a` và `shopsflow-nat-b`.
2. Chờ đến khi cả hai NAT Gateway được xóa hoàn tất.
3. Cuối cùng, release hai Elastic IP không còn sử dụng.

#### 9. VPC

Khi ALB, RDS, ECS network interface và NAT Gateway đã được xóa, lần lượt delete custom route table, detach rồi delete `shopsflow-igw`, xóa subnet cùng custom security group, và cuối cùng delete `shopsflow-vpc`.

