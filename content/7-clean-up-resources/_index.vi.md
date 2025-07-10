---
title: "Clean-up Resources"
weight: 7
chapter: false
pre: "<b> 7. </b>"
---

Để tránh phát sinh chi phí và giữ môi trường AWS của bạn gọn gàng, hãy làm theo các bước dưới đây để xóa bỏ tất cả các tài nguyên đã được tạo hoặc cấu hình trong workshop này.

**Lưu ý:** Đảm bảo bạn đang ở **đúng Vùng AWS (Region)** nơi bạn đã tạo các tài nguyên.

### 1. Xóa ECR Repositories

* **Truy cập dịch vụ ECR.**
* **Xóa các ECR Repository** mà bạn đã tạo (ví dụ: `my-test-repo`, `my-brand-new-test-repo-09-07-2340`).

### 2. Xóa CloudWatch Dashboards

* **Truy cập dịch vụ CloudWatch.**
* **Xóa CloudWatch Dashboard** mà bạn đã tạo (ví dụ: `ECR-Operations-Dashboard`).

### 3. Xóa CloudTrail Trail

* **Truy cập dịch vụ CloudTrail.**
* **Xóa CloudTrail Trail** mà bạn đã tạo hoặc cấu hình (ví dụ: `ecr-monitor-trail`).

### 4. Xóa S3 Bucket của CloudTrail

* **Truy cập dịch vụ S3.**
* **Làm rỗng S3 Bucket** mà CloudTrail của bạn đã sử dụng để lưu nhật ký.
* **Xóa S3 Bucket** đó.

### 5. Xóa CloudWatch Log Group

* **Truy cập dịch vụ CloudWatch.**
* **Xóa CloudWatch Log Group** mà CloudTrail của bạn đã gửi nhật ký tới (ví dụ: `/aws/cloudtrail/ecr-logs` hoặc `aws-cloudtrail-logs-fg`).

### 6. Xóa IAM Role (Tùy chọn)

* **Truy cập dịch vụ IAM.**
* **Xóa vai trò IAM** mà CloudTrail đã sử dụng để gửi nhật ký đến CloudWatch Logs (thường có tên bắt đầu bằng `CloudTrail_CloudWatchLogs_Role`).
