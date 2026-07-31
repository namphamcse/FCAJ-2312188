---
title: "S3, IMDSv2 và Lambda: 3 vấn đề vận hành AWS cần tránh"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

Thay vì bàn về các khái niệm vĩ mô, bài viết này tổng hợp ba chi tiết kỹ thuật nhỏ trên AWS. Chúng ít khi xuất hiện trên slide bài giảng nhưng có thể gây chi phí không cần thiết hoặc khiến việc debug kéo dài nhiều ngày.

### 1. File “tàng hình” trên S3: tiền vẫn mất nhưng không thấy file

Khi ứng dụng upload file dung lượng lớn lên S3 và kết nối bị ngắt giữa chừng, multipart upload sẽ không hoàn tất.

**Điều dễ bị bỏ sót:** Phần dữ liệu đã truyền lên trước khi lỗi xảy ra vẫn được lưu trong S3 và vẫn phát sinh chi phí lưu trữ. Các phần dữ liệu chưa hoàn tất này không xuất hiện khi xem bucket trên S3 Console hoặc dùng lệnh `aws s3 ls` thông thường. Nếu có nhiều lần upload file lớn thất bại, chi phí ẩn này có thể tăng đáng kể.

**Cách xử lý:** Tạo S3 Lifecycle Rule và cấu hình xóa incomplete multipart uploads sau 1–2 ngày. Quy tắc này giúp tự động dọn các phần dữ liệu không còn cần thiết.

### 2. Bẫy IMDSv2 hop limit khi chạy ứng dụng trong container

Nâng cấp từ IMDSv1 lên IMDSv2 là một thực hành bảo mật quan trọng để hạn chế rủi ro lộ IAM role credentials trên EC2. Tuy nhiên, ứng dụng chạy trong Docker container trên EC2 có thể không còn truy cập được EC2 Instance Metadata Service, dẫn đến lỗi xác thực từ AWS SDK.

**Nguyên nhân:** IMDSv2 kiểm soát hop limit của metadata response. Khi request từ container đi qua network bridge hoặc virtual interface của Docker để đến địa chỉ metadata `169.254.169.254`, nó có thể cần nhiều hơn một hop. Nếu tham số `HttpPutResponseHopLimit` của EC2 chỉ đặt là `1`, response có thể không quay lại được ứng dụng trong container.

**Cách xử lý:** Với workload chạy trong container cần truy cập instance metadata, tăng Metadata Response Hop Limit của EC2 instance từ `1` lên `2`, đồng thời chỉ cấp quyền IAM cần thiết cho instance role.

### 3. Thư mục `/tmp` của AWS Lambda không luôn sạch

Nhiều người mặc định rằng mỗi lần Lambda được gọi sẽ chạy trong một môi trường mới. Trên thực tế, với warm start, AWS có thể tái sử dụng execution environment cho các lần gọi tiếp theo. Vì vậy, file ghi vào `/tmp` ở lần xử lý trước có thể vẫn còn trong lần xử lý sau.

**Hậu quả:**

* **Rủi ro bảo mật:** File tạm chứa dữ liệu nhạy cảm cần được xóa sau khi xử lý để tránh bị sử dụng nhầm trong các request sau.
* **Rủi ro đầy ổ đĩa:** File tạm tích lũy có thể làm đầy dung lượng ephemeral storage và gây lỗi `No space left on device` khó dự đoán.

**Cách xử lý:** Luôn chủ động xóa file tạm ngay sau khi xử lý, ví dụ bằng khối `try...finally`. Không nên phụ thuộc vào việc Lambda có tạo execution environment mới hay không.

Ba chi tiết trên đều nhỏ nhưng có thể ảnh hưởng trực tiếp đến chi phí, bảo mật và độ ổn định của hệ thống. Kiểm tra chúng từ sớm sẽ giúp hệ thống vận hành trơn tru hơn và tránh các sự cố ngầm không đáng có.

### Link bài blog

[Xem bài blog trên Facebook](https://www.facebook.com/share/p/1BVqPJMVwu/)
