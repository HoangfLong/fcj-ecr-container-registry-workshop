---
title: "ECR Security & Lifecycle"
weight: 5
chapter: false
pre: "<b> 5. </b>"
---

Sau khi đã thành công trong việc xây dựng và đẩy các Docker image lên Amazon ECR thông qua pipeline CI/CD, bước tiếp theo là đảm bảo rằng các image này được quản lý an toàn và hiệu quả trong suốt vòng đời của chúng. Amazon ECR cung cấp các tính năng mạnh mẽ để quét lỗ hổng bảo mật và tự động quản lý các phiên bản image, giúp bạn duy trì một kho lưu trữ container sạch sẽ và an toàn.

#### Mục tiêu của phần này

* Cấu hình tính năng Image Scanning (quét hình ảnh) trong ECR để tự động phát hiện các lỗ hổng bảo mật.
* Hiểu cách xem và phân tích các kết quả quét lỗ hổng để đưa ra hành động khắc phục.
* Cấu hình ECR Lifecycle Policy (chính sách vòng đời) để tự động xóa các phiên bản image cũ hoặc không sử dụng, giúp tối ưu chi phí và dung lượng lưu trữ.
* Nắm được lợi ích của việc áp dụng các chính sách quản lý vòng đời image trong môi trường sản xuất.
