---
title: "Triển khai frontend với S3 và CloudFront"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

#### Tổng quan

Frontend là static website được build thành HTML, CSS và JavaScript. Các file này được upload lên S3 bucket private. CloudFront sử dụng bucket làm origin để phân phối nội dung đến người dùng, giúp cache tại edge location và không cần public S3 bucket.

#### Nội dung

1. [Tạo S3 bucket và upload frontend](5.3.1-create-gwe/)
2. [Tạo CloudFront distribution](5.3.2-test-gwe/)
