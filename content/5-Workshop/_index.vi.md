---
title: "Workshop"
date: 2026-07-30
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

Workshop này triển khai một ứng dụng web theo kiến trúc cloud-native trên AWS. Frontend được lưu trữ trên Amazon S3 và phân phối qua Amazon CloudFront. Backend chạy container trên Amazon ECS Fargate, nhận request qua Application Load Balancer (ALB), và sử dụng Amazon RDS for PostgreSQL làm cơ sở dữ liệu.

### Nội dung

1. [Tổng quan kiến trúc](5.1-Workshop-overview/)
2. [Chuẩn bị VPC và Security Group](5.2-Prerequiste/)
3. [Triển khai frontend với S3 và CloudFront](5.3-S3-vpc/)
4. [Triển khai backend với ALB, ECS Fargate và RDS PostgreSQL](5.4-S3-onprem/)
