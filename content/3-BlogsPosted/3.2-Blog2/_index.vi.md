---
title: "Những kỹ thuật “ngầm” trên AWS"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Những kỹ thuật “ngầm” trên AWS: từ tiền phạt ẩn đến sự cố mạng vô hình

Khi làm việc với AWS đủ lâu, em nhận ra có khoảng cách lớn giữa kiến thức trong chứng chỉ và những gì xảy ra ở môi trường production. Một số sự cố không tạo cảnh báo rõ ràng, không nằm trong danh sách lỗi phổ biến, nhưng vẫn có thể làm hệ thống chập chờn hoặc phát sinh chi phí dịch vụ lớn.

Dưới đây là bốn vấn đề kỹ thuật ít được chú ý nhưng rất đáng lưu ý khi vận hành hệ thống trên AWS.

### 1. Cross-AZ Data Transfer: chi phí mạng ngay trong cùng một Region

Nhiều người biết data transfer ra Internet có tính phí, nhưng lại cho rằng lưu lượng nội bộ trong cùng một Region luôn miễn phí. Trên thực tế, AWS có thể tính phí lưu lượng truyền giữa các Availability Zone khác nhau trong cùng Region. Mức giá thường gặp là `0.01 USD/GB` ở chiều gửi và `0.01 USD/GB` ở chiều nhận, tương đương `0.02 USD/GB` cho một chu trình truyền dữ liệu.

**Kịch bản thực tế:** Một EKS cluster hoặc hệ thống EC2 chạy microservices được phân bổ ở nhiều AZ để tăng tính sẵn sàng. Các service liên tục gọi lẫn nhau hoặc truy vấn Redis cache/database nằm cố định ở một AZ. Lưu lượng cross-AZ tích lũy qua hàng nghìn request mỗi giây có thể tạo ra nhiều terabyte data transfer và làm chi phí cuối tháng cao hơn cả chi phí compute.

**Giải pháp:** Thiết kế theo tư duy **AZ-awareness**. Với Kubernetes, có thể áp dụng các cơ chế như Topology Aware Hints để ưu tiên điều hướng traffic giữa các workload trong cùng AZ; chỉ để traffic vượt AZ khi cần thiết để bảo đảm khả năng chịu lỗi.

### 2. Bẫy MTU 9001 qua VPC Peering hoặc VPN

Đây là dạng lỗi khó debug vì hệ thống thường không trả về log rõ ràng. EC2 trong cùng VPC có thể sử dụng Jumbo Frames với MTU `9001` bytes. Tuy nhiên, khi kết nối đi qua VPC Peering giữa Region, VPN hoặc một số đường truyền hybrid, MTU có thể bị giới hạn ở mức `1500` bytes.

**Dấu hiệu:** Ping vẫn hoạt động, SSH vẫn kết nối được, nhưng request truyền file lớn hoặc JSON API dài có thể treo đến khi timeout.

**Nguyên nhân:** Gói tin lớn hơn MTU cần được xử lý thông qua Path MTU Discovery. Nếu thiết bị trung gian cần gửi ICMP để thông báo về giới hạn gói tin nhưng ICMP bị chặn ở Security Group hoặc Network ACL, thông báo này không đến được nguồn gửi. Hiện tượng đó thường được gọi là Path MTU Discovery Black Hole.

**Giải pháp:** Cho phép ICMP cần thiết, chẳng hạn Custom ICMP - IPv4 Type 3, Code 4 (Destination Unreachable), theo đúng phạm vi bảo mật của hệ thống. Một lựa chọn khác là hạ MTU của network interface xuống `1500` nếu workload phụ thuộc nhiều vào kết nối liên VPC hoặc VPN.

### 3. Giới hạn mở rộng của DynamoDB On-Demand khi có Flash Sale

DynamoDB On-Demand rất thuận tiện vì không cần dự báo trước WCU/RCU. Tuy nhiên, cơ chế này không có nghĩa là table có thể tăng đến mọi mức lưu lượng ngay lập tức. DynamoDB On-Demand dựa trên lịch sử traffic và cần thời gian để thích ứng với mức tải tăng đột biến.

**Kịch bản rủi ro:** Hệ thống thường chỉ xử lý khoảng `1,000` request/giây, sau đó flash sale làm traffic tăng lên `20,000` request/giây trong vài giây. Nếu mức tăng quá nhanh so với lịch sử lưu lượng, request có thể bị throttled và ứng dụng nhận lỗi như `ProvisionedThroughputExceededException`.

**Giải pháp:** Với sự kiện đã biết trước có traffic bùng nổ, cần kiểm thử tải và lập kế hoạch capacity trước thời điểm diễn ra. Có thể chuyển sang Provisioned Capacity với WCU/RCU phù hợp hoặc tăng tải theo từng giai đoạn để DynamoDB có thời gian thích ứng, sau đó đánh giá lại việc quay về On-Demand.

### 4. Cú click tốn tiền trên CloudWatch Logs Insights

CloudWatch Logs Insights rất tiện để truy vấn log, nhưng chi phí không dựa trên số dòng kết quả trả về mà dựa trên tổng dung lượng log thô được quét. Mức phí thường gặp là khoảng `0.005 USD` cho mỗi GB dữ liệu được scan.

**Sai lầm phổ biến:** Mở một Log Group lưu sáu tháng log, có dung lượng khoảng `500 GB`, chạy truy vấn chung chung mà không giới hạn thời gian. Chỉ một lần chạy có thể quét toàn bộ dữ liệu và tạo chi phí khoảng `2.5 USD`. Nếu truy vấn tương tự được chạy tự động mỗi năm phút để làm dashboard, chi phí scan log có thể tăng rất nhanh.

**Giải pháp:** Luôn giới hạn time range nhỏ nhất có thể, ví dụ 15 phút hoặc một giờ gần nhất. Với nhu cầu phân tích lịch sử log dung lượng lớn, có thể xuất log sang S3 và dùng Amazon Athena để tối ưu chi phí truy vấn.

Làm việc trên cloud không chỉ là ghép các dịch vụ theo sơ đồ kiến trúc mà còn là hiểu các thông số vận hành bên dưới hạ tầng. Những chi tiết như MTU, cơ chế mở rộng của partition hay cước phí cross-AZ thường không xuất hiện trong bài lab cơ bản, nhưng lại quyết định chi phí và độ ổn định của hệ thống.

Hy vọng những lưu ý này giúp mọi người tránh được các sự cố “vô hình” và vận hành hệ thống hiệu quả hơn.

### Link bài blog

[Xem bài blog trên Facebook](https://www.facebook.com/groups/awsstudygroupfcj/permalink/2230167921081501/?rdid=OWE359AjcB0vTUf2#)
