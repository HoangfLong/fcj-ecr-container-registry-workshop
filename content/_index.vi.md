+++
title = "Quản lý Registry Container với ECR và Chính sách bảo mật"
date = 2025
weight = 1
chapter = false
+++

# Quản lý Registry Container với ECR và Chính sách bảo mật

#### Tổng quan

Trong workshop này, bạn sẽ triển khai một hệ thống **quản lý registry container toàn diện** sử dụng **Amazon Elastic Container Registry (ECR)**. Nội dung tập trung vào việc thiết kế một quy trình quản lý container an toàn, hiệu quả và chuyên nghiệp, được tích hợp hoàn chỉnh với:

- **Các chính sách bảo mật (IAM)**
- **Quét lỗ hổng bảo mật (Amazon Inspector)**
- **Kiểm soát truy cập chi tiết**
- **Tự động hóa vòng đời và triển khai CI/CD**
- **Giám sát và cảnh báo (CloudWatch & SNS)**
- **Xác thực tuân thủ và tối ưu hóa chi phí**

Bạn sẽ học cách thiết kế hệ thống theo phương pháp DevSecOps, trong đó các image container được quét trước khi triển khai, quyền truy cập được kiểm soát chặt chẽ và các tài nguyên cũ được tự động dọn dẹp để giảm chi phí.

---

## Khi kết thúc workshop này, bạn sẽ có thể:

- Áp dụng **các chính sách bảo mật** bằng IAM, trình bày cách ECR tương tác với các dịch vụ như CodeBuild, ECS và Lambda
- Tích hợp **Amazon Inspector** để tự động quét lỗ hổng bảo mật của các image container ngay sau khi đẩy image
- Triển khai **kiểm soát truy cập chi tiết**, đảm bảo người dùng và dịch vụ chỉ có các quyền cần thiết
- Cấu hình **chính sách vòng đời ECR** để tự động dọn dẹp các image không sử dụng hoặc không gắn thẻ và tối ưu hóa chi phí lưu trữ
- Thiết kế một **pipeline CI/CD tự động** kích hoạt quét bảo mật khi các image mới được đẩy lên
- Thiết lập **giám sát và cảnh báo** để thông báo cho bạn về các mối đe dọa bảo mật tiềm ẩn hoặc các hoạt động đáng ngờ
- Xây dựng một **pipeline DevSecOps sẵn sàng cho sản xuất** để quản lý container một cách an toàn và hiệu quả trên AWS

---

## Nội dung Workshop

1. [Giới thiệu](1-introduction/)
2. [Điều kiện tiên quyết](2-prerequisites/)
3. [Cấu hình IAM](3-iam-configuration/)
4. [CI/CD với GitHub Actions](4-ci-cd-with-github-actions/)
5. [Bảo mật ECR & Quản lý Vòng đời](5-ecr-security-lifecycle/)
6. [Giám sát & Cảnh báo](6-monitoring-alerts/)
7. [Dọn dẹp tài nguyên](7-clean-up-resources/)

---

Hãy bắt đầu xây dựng một registry container an toàn, tối ưu hóa và tự động trên AWS!