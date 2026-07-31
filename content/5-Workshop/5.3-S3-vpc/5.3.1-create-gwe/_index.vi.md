---
title: "Tạo S3 bucket và upload frontend"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

#### Tạo S3 bucket

1. Mở Amazon S3 Console và tạo bucket với tên duy nhất.
2. Giữ Block Public Access bật để bucket không được truy cập trực tiếp từ Internet.
3. Upload output build của frontend, bao gồm `index.html`, CSS, JavaScript và các static asset.

Bucket đóng vai trò origin cho CloudFront; người dùng không truy cập object S3 trực tiếp.

![S3 bucket chứa frontend](/FCAJ-2312188/images/5-Workshop/s3_bucket.jpg?featherlight=false)
