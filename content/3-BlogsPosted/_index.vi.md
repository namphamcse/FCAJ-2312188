---
title: "Các bài blog đã đăng"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

Phần này tổng hợp các bài blog em đã viết về AWS, tập trung vào những kinh nghiệm thực hành và các vấn đề kỹ thuật có thể gặp trong quá trình vận hành hệ thống.

### [Blog 1 – S3, IMDSv2 và Lambda: 3 vấn đề vận hành AWS cần tránh](3.1-Blog1/)

Bài viết tổng hợp ba chi tiết kỹ thuật dễ bị bỏ sót: incomplete multipart uploads trên S3, IMDSv2 hop limit khi ứng dụng chạy trong container, và việc tái sử dụng thư mục `/tmp` của AWS Lambda.

### [Blog 2 – Cross-AZ, MTU, DynamoDB và Logs Insights: rủi ro vận hành AWS](3.2-Blog2/)

Bài viết phân tích bốn rủi ro vận hành: chi phí cross-AZ data transfer, MTU qua VPC Peering/VPN, giới hạn mở rộng của DynamoDB On-Demand và chi phí quét log bằng CloudWatch Logs Insights.

### [Blog 3 – NAT Gateway, Glacier, PassRole và EBS: 4 cái bẫy AWS](3.3-Blog3/)

Bài viết chia sẻ bốn bẫy vận hành liên quan đến NAT Gateway, S3 Glacier, `iam:PassRole` và EBS Elastic Volumes có thể ảnh hưởng trực tiếp đến chi phí, bảo mật và khả năng xử lý sự cố.
