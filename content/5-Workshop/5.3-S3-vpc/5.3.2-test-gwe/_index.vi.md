---
title: "Tạo CloudFront distribution"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

#### Cấu hình CloudFront

1. Tạo một CloudFront distribution và chọn S3 bucket vừa tạo làm origin.
2. Thiết lập Origin Access Control để CloudFront có thể đọc các object trong private bucket.
3. Đặt default root object là `index.html`.
4. Cấu hình SPA fallback để các client-side route được trả về `index.html` khi phù hợp.
5. Sau khi hoàn tất cấu hình ALB ở mục 5.4.4, quay lại đây để thêm ALB làm origin thứ hai và tạo behavior `/api/*` chuyển tiếp các API method, header và query string cần thiết.
6. Khi distribution đã triển khai xong, mở CloudFront domain name để kiểm tra frontend.

#### Cấu hình distribution đã triển khai

| Cấu hình | Giá trị |
|---|---|
| Distribution | `fe cloudfront` |
| Public domain | `d2m34udjfc5fxq.cloudfront.net` |
| Default origin | S3 bucket private chứa React/Vite `dist` artifact |
| API origin | Internet-facing `shopsflow-alb` |
| API behavior | `/api/*` được forward đến ALB |
| Default root object | `index.html` |

Khi frontend được cập nhật, tạo invalidation để xóa cache của các file cần phát hành lại.

![CloudFront frontend distribution](/FCAJ-2312188/images/5-Workshop/fe_cloudfront.jpg?featherlight=false)
