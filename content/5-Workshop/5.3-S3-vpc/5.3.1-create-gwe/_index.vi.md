---
title: "Tạo S3 bucket và upload frontend"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

#### Tạo S3 bucket

1. Trước hết, cài dependency và build frontend. Với ứng dụng React/Vite này, chạy `npm install`, sau đó chạy `npm run build`.
2. Mở Amazon S3 Console và tạo một bucket có tên duy nhất.
3. Giữ Block Public Access ở trạng thái bật để không ai có thể truy cập trực tiếp bucket từ Internet.
4. Upload thư mục `dist` vừa tạo, gồm `index.html`, CSS, JavaScript và các static asset.

Bucket đóng vai trò origin cho CloudFront; người dùng không truy cập object S3 trực tiếp.

![S3 bucket chứa frontend](/FCAJ-2312188/images/5-Workshop/s3_bucket.jpg?featherlight=false)
