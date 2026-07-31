---
title: "Chuẩn bị VPC và Security Group"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

#### Mục tiêu chuẩn bị

Tạo network foundation cho ứng dụng với hai public subnet dành cho ALB và hai private subnet dành cho ECS Fargate cùng RDS PostgreSQL. Các subnet được đặt ở nhiều Availability Zone để tăng khả năng sẵn sàng.

#### Điều kiện chuẩn bị

Chuẩn bị AWS CLI, Docker, Git và công cụ build frontend trước khi triển khai. Chọn một AWS Region và sử dụng nhất quán cho các tài nguyên VPC, ECR, ECS, ALB, RDS, S3 và CloudFront.

#### Các thành phần mạng

* **Public subnets:** Chứa Application Load Balancer; các subnet này có route đến Internet Gateway.
* **Private subnets:** Chứa ECS task và RDS instance; database không có public IP.
* **Security groups:** Chỉ cho phép traffic theo đúng luồng CloudFront → ALB → ECS → RDS.

#### Cấu hình network đã triển khai

| Tài nguyên | Tên hoặc CIDR | Availability Zone |
|---|---|---|
| VPC | `shopsflow-vpc` | `us-east-1` |
| Public subnet 1 | `aws-practice-vpc-subnet-public1-us-east-1a` — `10.0.0.0/20` | `us-east-1a` |
| Public subnet 2 | `aws-practice-vpc-subnet-public2-us-east-1b` — `10.0.16.0/20` | `us-east-1b` |
| Private subnet 1 | `aws-practice-vpc-subnet-private1-us-east-1a` — `10.0.128.0/20` | `us-east-1a` |
| Private subnet 2 | `aws-practice-vpc-subnet-private2-us-east-1b` — `10.0.144.0/20` | `us-east-1b` |

#### Cấu hình Internet access và route

1. Tạo `shopsflow-igw` và attach vào `shopsflow-vpc`.
2. Tạo public route table với route `0.0.0.0/0` đi qua Internet Gateway, sau đó associate với hai public subnet.
3. Tạo `shopsflow-nat-a` trong public subnet 1 và `shopsflow-nat-b` trong public subnet 2, mỗi NAT Gateway gắn một Elastic IP.
4. Route private subnet 1 qua `shopsflow-nat-a` và private subnet 2 qua `shopsflow-nat-b` khi ECS task cần outbound access, ví dụ để gọi payment provider.

ALB vẫn là thành phần public duy nhất nhận inbound traffic. ECS task và RDS vẫn ở private subnet, kể cả khi ECS task sử dụng NAT Gateway để đi ra ngoài.

![VPC architecture](/FCAJ-2312188/images/5-Workshop/vpc.jpg?featherlight=false)
*Hình 1. Kiến trúc VPC cho môi trường workshop.*

![Public subnet 1](/FCAJ-2312188/images/5-Workshop/public_subnet_1.jpg?featherlight=false)
*Hình 2. Public subnet thứ nhất dành cho ALB hướng Internet.*

![Public subnet 2](/FCAJ-2312188/images/5-Workshop/public_subnet_2.jpg?featherlight=false)
*Hình 3. Public subnet thứ hai, phục vụ tính sẵn sàng cao.*

![Private subnet 1](/FCAJ-2312188/images/5-Workshop/private_subnet_1.jpg?featherlight=false)
*Hình 4. Private subnet thứ nhất cho ECS task và RDS.*

![Private subnet 2](/FCAJ-2312188/images/5-Workshop/private_subnet_2.jpg?featherlight=false)
*Hình 5. Private subnet thứ hai, phục vụ tính sẵn sàng cao.*

![Security group design](/FCAJ-2312188/images/5-Workshop/security_groups.jpg?featherlight=false)
*Hình 6. Quy tắc security group giữa ALB, ECS và RDS.*

#### Nguyên tắc bảo mật

* ALB security group chỉ mở port ứng dụng cần thiết từ Internet hoặc CloudFront.
* ECS security group chỉ cho phép inbound từ ALB security group.
* RDS security group chỉ cho phép PostgreSQL port `5432` từ ECS security group.

#### Kiểm tra trước khi triển khai

Trước khi triển khai ứng dụng, kiểm tra public subnet route qua Internet Gateway, private subnet có NAT route cần thiết và traffic path được giới hạn theo luồng ALB → ECS → RDS.

#### IAM role cho ECS

Tạo hai role riêng trước khi tạo ECS task definition:

* **Task execution role:** pull backend image từ ECR và gửi container log đến CloudWatch.
* **Task role:** chỉ chứa quyền mà application code bên trong container cần sử dụng.

Giữ hai role này tách biệt và không đặt static AWS access key bên trong container.
