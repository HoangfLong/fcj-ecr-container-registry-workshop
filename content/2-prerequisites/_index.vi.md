---
title: "Yêu cầu "
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

Để bắt đầu workshop này, bạn cần chuẩn bị môi trường làm việc với các tài khoản, công cụ và tài nguyên cần thiết. Việc này sẽ đảm bảo bạn có thể thực hiện các bài lab một cách suôn sẻ và hiệu quả.

### Các bước chuẩn bị

* **Chuẩn bị Tài khoản và Quyền truy cập AWS:**
    * Đảm bảo bạn có một **tài khoản AWS đang hoạt động**.
    * Truy cập AWS Console với một **người dùng IAM có quyền quản trị** (hoặc một tập hợp các quyền tương đương để tạo và quản lý các tài nguyên của workshop).

* **Cài đặt Công cụ cục bộ:**
    * **Docker Desktop:** Cài đặt Docker Desktop trên máy tính cá nhân của bạn. Đây là công cụ cần thiết để xây dựng (build) các Docker image.
    * **AWS Command Line Interface (AWS CLI):** Cài đặt và cấu hình AWS CLI. Công cụ này cho phép bạn tương tác với các dịch vụ AWS từ dòng lệnh và là cần thiết cho một số bước xác thực trong workshop.

* **Tạo Amazon ECR Repository:**
    * Tạo một **Amazon ECR Repository mới** trong AWS Console với tên `fcj-workshop-app`. Repository này sẽ là nơi lưu trữ các container image của bạn trong suốt workshop.

* **Chuẩn bị Repository GitHub:**
    * **Clone repository mẫu** của workshop từ GitHub vào tài khoản GitHub cá nhân của bạn. Repository này chứa mã nguồn ứng dụng mẫu và Dockerfile cần thiết cho phần CI/CD.