---
title: "CI/CD với Github Actions"
weight: 4
chapter: false
pre: "<b> 4. </b>"
---

Trong phần này, chúng ta sẽ thiết lập một quy trình Tích hợp liên tục/Phân phối liên tục (CI/CD) sử dụng GitHub Actions. Quy trình này sẽ tự động hóa việc xây dựng (build) Docker image từ mã nguồn của bạn và đẩy (push) chúng lên Amazon ECR mỗi khi có thay đổi được đẩy lên GitHub.

#### Mục tiêu của phần này

* Hiểu cách GitHub Actions hoạt động như một công cụ CI/CD.
* Cấu hình GitHub Repository Secrets để lưu trữ thông tin xác thực AWS.
* Xây dựng GitHub Actions Workflow để tự động hóa quá trình build và push image lên ECR.
* Xác minh luồng CI/CD hoạt động đúng cách.
