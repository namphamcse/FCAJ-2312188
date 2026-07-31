---
title: "Những “cái bẫy” ẩn kỹ trong AWS"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Những “cái bẫy” ẩn kỹ trong AWS mà tài liệu chính thức ít khi cảnh báo

Khi mới tiếp cận AWS, em thường gặp các khái niệm quen thuộc như EC2, S3, RDS và những kiến trúc chuẩn trên slide. Chỉ khi vận hành hệ thống thực tế, em mới nhận ra có những góc khuất ít được đề cập trong các khóa học cơ bản, nhưng có thể dẫn đến chi phí lớn hoặc sự cố cần xử lý vào thời điểm không mong muốn.

Dưới đây là bốn bài học thực tế về các cơ chế vận hành ngầm của AWS liên quan đến chi phí, bảo mật và khả năng phản ứng khi có sự cố.

### 1. NAT Gateway và chi phí khi làm việc với S3

Đặt EC2 trong private subnet và dùng NAT Gateway để đi ra Internet là kiến trúc phổ biến. Tuy nhiên, khi ứng dụng thường xuyên đọc hoặc ghi dữ liệu với S3—như lưu log, backup hoặc upload media—kiến trúc này có thể tạo ra chi phí không cần thiết.

**Vấn đề:** Theo route mặc định, traffic từ EC2 đến S3 có thể đi qua NAT Gateway. NAT Gateway có phí theo giờ hoạt động và phí xử lý theo mỗi GB dữ liệu đi qua.

**Hậu quả:** Hóa đơn có thể tăng cao chỉ do phí xử lý dữ liệu của NAT Gateway, dù EC2 và S3 nằm trong cùng một Region.

**Cách xử lý:** Tạo **S3 Gateway VPC Endpoint** cho VPC. Endpoint này không tính thêm phí sử dụng và cho phép traffic đến S3 đi theo mạng nội bộ AWS thay vì qua NAT Gateway. Điều này vừa tối ưu đường truyền vừa loại bỏ phí data processing của NAT Gateway cho traffic S3.

### 2. Chuyển file nhỏ sang Glacier có thể tốn hơn tiền lưu trữ

S3 Glacier và Glacier Deep Archive phù hợp cho lưu trữ dài hạn với chi phí thấp. Tuy nhiên, áp dụng Lifecycle Rule chuyển toàn bộ object sang Glacier sau một khoảng thời gian có thể phản tác dụng nếu bucket chứa hàng triệu file nhỏ.

**Chi phí transition:** Lifecycle Transition Request được tính theo số object. Với số lượng file rất lớn, phí request chuyển đổi có thể vượt xa chi phí lưu trữ tiết kiệm được.

**Chi phí metadata:** Object lưu ở Glacier có thêm metadata quản lý. Vì vậy, một file chỉ vài KB có thể bị tính dung lượng lưu trữ lớn hơn nhiều so với kích thước dữ liệu gốc.

**Cách xử lý:** Trước khi tạo Lifecycle Rule, đóng gói các file nhỏ thành file lớn bằng `zip` hoặc `tar`, hoặc đặt điều kiện chỉ chuyển đổi object có kích thước tối thiểu, ví dụ từ `128 KB` trở lên.

### 3. Lỗ hổng leo thang quyền từ `iam:PassRole`

Để thuận tiện khi deploy, developer đôi khi được cấp quyền `iam:PassRole` với wildcard `*`, cho phép gắn IAM role vào dịch vụ như Lambda hoặc EC2. Đây là quyền cần được kiểm soát chặt chẽ.

**Kịch bản nguy hiểm:** Một tài khoản có quyền tạo Lambda function và `iam:PassRole` đối với mọi role có thể gắn Lambda với role đã có `AdministratorAccess`. Function đó có thể thực hiện thao tác với quyền admin, dẫn đến leo thang đặc quyền.

**Cách xử lý:** Không dùng wildcard cho `iam:PassRole`. Chỉ định chính xác ARN của những role được phép pass và giới hạn dịch vụ đích khi phù hợp. Quyền `iam:PassRole` cần được xem xét với mức độ thận trọng tương tự quyền quản trị.

### 4. Giới hạn sáu giờ khi chỉnh sửa EBS Volume

Elastic Volumes cho phép mở rộng dung lượng hoặc thay đổi loại volume, ví dụ từ `gp2` sang `gp3`, trên EC2 instance đang chạy. Dù tiện lợi, thao tác này có giới hạn thời gian quan trọng: sau một lần chỉnh sửa volume, cần chờ ít nhất sáu giờ trước khi thực hiện chỉnh sửa tiếp theo trên cùng volume.

**Tình huống thực tế:** Khi ổ đĩa gần đầy, có thể tăng vội từ `100 GB` lên `120 GB`, rồi nhận ra cần tới `500 GB`. Nếu lần chỉnh sửa trước chưa qua thời gian chờ cần thiết, AWS sẽ không cho phép chỉnh sửa ngay lần nữa.

**Cách xử lý:** Tính toán nhu cầu dung lượng có dự phòng ngay từ lần chỉnh sửa đầu tiên và theo dõi dung lượng bằng CloudWatch để xử lý sớm trước khi sự cố xảy ra.

### Lời kết

Làm việc trên cloud không chỉ là lắp ghép các dịch vụ theo sơ đồ kiến trúc. Những chi tiết như route traffic, object size, quyền IAM và giới hạn thay đổi volume thường không xuất hiện trong bài lab cơ bản, nhưng lại quyết định độ ổn định của hệ thống và mức độ an toàn cho chi phí vận hành.

Hy vọng những lưu ý này giúp mọi người tránh được các “cái bẫy” vô hình khi làm việc với AWS.

### Link bài blog

[Xem bài blog trên Facebook](https://www.facebook.com/share/p/18wTHQFBVC/)
