---
title: "CloudTrail và CloudWatch"
weight: 6
chapter: false
pre: "<b> 6. </b>"
---

Để có cái nhìn toàn diện về các hoạt động diễn ra trong Amazon ECR và đảm bảo tuân thủ các chính sách bảo mật, việc tận dụng **AWS CloudTrail** và **Amazon CloudWatch** là rất cần thiết. Hai dịch vụ này làm việc cùng nhau để cung cấp khả năng giám sát mạnh mẽ, giúp bạn theo dõi ai đã làm gì, khi nào và ở đâu trong môi trường registry của mình.

Trong phần này, bạn sẽ học cách:

1.  **Theo dõi các cuộc gọi API liên quan đến ECR bằng CloudTrail:** CloudTrail tự động ghi lại mọi hoạt động API được thực hiện trên tài khoản AWS của bạn, bao gồm cả các tương tác với ECR như tạo/xóa repository, đẩy/kéo image, hoặc thay đổi chính sách. Điều này cung cấp một bản ghi lịch sử chi tiết, rất quan trọng cho việc kiểm toán bảo mật và khắc phục sự cố.
2.  **Phân tích nhật ký CloudTrail trong CloudWatch Logs:** Các bản ghi từ CloudTrail được gửi trực tiếp đến Amazon CloudWatch Logs. Tại đây, bạn có thể tìm kiếm, lọc và phân tích các nhật ký này để phát hiện các hoạt động đáng ngờ, theo dõi các thay đổi cấu hình hoặc xác minh việc tuân thủ các chính sách truy cập.
3.  **Giám sát các chỉ số hiệu suất của ECR với CloudWatch Metrics:** CloudWatch cũng tự động thu thập các chỉ số quan trọng về hoạt động và sức khỏe của các tài nguyên ECR của bạn, như dung lượng lưu trữ repository, số lượng image, và lưu lượng push/pull. Việc giám sát các chỉ số này giúp bạn hiểu rõ mức độ sử dụng, hiệu suất và khả năng mở rộng của ECR.

Việc tích hợp CloudTrail và CloudWatch sẽ cung cấp cho bạn cái nhìn sâu sắc về mọi khía cạnh hoạt động của ECR, từ việc quản lý truy cập đến việc đảm bảo tính tuân thủ của các chính sách bảo mật mà bạn đã thiết lập.