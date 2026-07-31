---
title: "CloudWatch, Payment Flow và Validation"
date: 2026-07-30
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

#### CloudWatch Logs

Cấu hình ECS task definition với `awslogs` driver để backend gửi stdout và stderr đến log group:

```text
/shopsflow/ecs/backend
```

Kiểm tra log khi task start hoặc stop, Spring Boot khởi động lỗi, database connection lỗi, payment lỗi, HTTP response `4xx`/`5xx` và khi có deployment revision mới.

#### Metrics cần theo dõi

* **ECS:** số task đang chạy, deployment status và stopped reason.
* **ALB:** healthy/unhealthy host count, request count, target response time và metric `4xx`/`5xx`.
* **RDS:** CPU utilization, database connection cùng storage hoặc memory metric.

#### Payment flow

1. User checkout qua frontend và request đến backend qua CloudFront cùng ALB.
2. Backend tạo payment parameter và signature cho payment provider.
3. Fargate task trong private subnet gọi provider qua NAT Gateway.
4. Provider trả Payment URL; backend trả URL này về browser.
5. Sau khi thanh toán, backend xác minh signature và status từ provider trước khi cập nhật Order sang paid hoặc confirmed. Client redirect hoặc callback không phải là nguồn tin cậy duy nhất.

#### Validation scenarios

1. React SPA load từ S3 qua CloudFront.
2. Request đến `/api/*` đi tới ALB, Fargate target healthy và nhận response có dữ liệu từ RDS.
3. Fargate không có public IP nhưng vẫn truy cập được qua ALB và có outbound access qua NAT.
4. RDS ở private subnet và chỉ nhận port `5432` từ `ecs-sg`.
5. Concurrent checkout không làm oversell inventory nhờ Optimistic Locking.
6. Payment URL creation và sandbox status verification hoàn tất thành công.
7. ECR image và task-definition revision mới được redeploy thành công.
8. DB endpoint hoặc health-check path cấu hình sai có thể được chẩn đoán qua ECS stopped reason, ALB target health và CloudWatch Logs.

#### Cost review

Theo dõi thời gian chạy và data processing của NAT Gateway, thời lượng ALB/Fargate/RDS, Cross-AZ traffic path và time range cùng query scope trong CloudWatch Logs Insights. Sử dụng RDS backup và snapshot khi được bật; kiến trúc này không dùng EC2-to-S3 backup flow cũ.

Monitoring, validation và cost review là một phần của công việc triển khai. Workload đang chạy vẫn có thể có payment flow lỗi, inventory không chính xác hoặc chi phí không cần thiết.
