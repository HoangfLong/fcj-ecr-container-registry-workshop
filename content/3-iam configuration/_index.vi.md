---
title: "Cấu hình IAM"
weight: 3
chapter: false
pre: "<b> 3. </b>"
---

Trong phần này, chúng ta sẽ thiết lập các quyền truy cập cần thiết bằng cách cấu hình AWS Identity and Access Management (IAM). Đây là bước nền tảng để đảm bảo người dùng và các dịch vụ AWS có thể tương tác an toàn với Amazon ECR và các tài nguyên liên quan, tuân thủ nguyên tắc "ít đặc quyền nhất".

#### Mục tiêu của phần này

* Hiểu rõ vai trò của IAM trong việc kiểm soát quyền truy cập vào ECR.
* Thiết lập các IAM User, IAM Policy và IAM Role cần thiết cho các hoạt động của workshop.
* Đảm bảo các quy trình tự động (như GitHub Actions) có thể xác thực và tương tác với AWS một cách an toàn.

---