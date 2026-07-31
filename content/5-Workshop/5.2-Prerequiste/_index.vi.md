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

#### Cấu hình Internet access và route

1. Tạo và attach Internet Gateway vào VPC.
2. Tạo public route table với route `0.0.0.0/0` đi qua Internet Gateway, sau đó associate với hai public subnet.
3. Để có tính sẵn sàng cao, tạo một NAT Gateway và gắn một Elastic IP trong mỗi Availability Zone của public subnet.
4. Route Internet-bound traffic của mỗi private subnet qua NAT Gateway trong cùng Availability Zone khi ECS task cần outbound access, ví dụ khi gọi một dịch vụ bên ngoài. Một NAT Gateway vẫn phù hợp cho lab cần tiết kiệm chi phí, nhưng đây là một đánh đổi về tính sẵn sàng.

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
