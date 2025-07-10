---
title: "Giới thiệu"
weight: 1
chapter: false
pre: "<b> 1. </b>"
---

Trong workshop này, chúng ta sẽ xây dựng một giải pháp toàn diện để quản lý vòng đời và bảo mật cho các container image của bạn bằng cách tận dụng sức mạnh của **Amazon Elastic Container Registry (ECR)** cùng với các dịch vụ AWS liên quan. Bạn sẽ được hướng dẫn qua một quy trình DevSecOps, từ việc thiết lập ban đầu cho đến tự động hóa, bảo mật và giám sát.

#### Kiến trúc tổng quan

![Kiến trúc tổng quan](images/1/001.png)


Kiến trúc trên minh họa luồng hoạt động chính trong workshop này:

1.  **Phát triển và Tích hợp liên tục (CI/CD):**
    * **Developer** bắt đầu bằng việc yêu cầu quyền truy cập thông qua **IAM (Identity and Access Management)**.
    * Các thay đổi mã nguồn được quản lý trong **GitHub**. Khi có một thẻ (tag) mới được tạo hoặc code được đẩy lên, **GitHub Actions** sẽ được kích hoạt.
    * **GitHub Actions** đóng vai trò là công cụ CI/CD chính của chúng ta. Nó sẽ thực hiện các bước: "Build Code to Image" (xây dựng Docker image từ mã nguồn) và sau đó "Push to ECR" (đẩy image đã xây dựng lên **Amazon Elastic Container Registry**).
    * Image sau khi được đẩy thành công sẽ "Image Ready for Deployment" (sẵn sàng để triển khai).

2.  **Quản lý Registry và Bảo mật trong AWS Cloud:**
    * **Amazon ECR** là kho lưu trữ trung tâm cho các container image của bạn. Tại đây, **ECR Repository Policies** sẽ quản lý các quy tắc truy cập chi tiết và **ECR Lifecycle Policy** sẽ tự động quản lý vòng đời của các image, giúp dọn dẹp các phiên bản cũ hoặc không cần thiết.
    * Khi một image mới được đẩy lên ECR, hoạt động này sẽ "image push triggers scan/report findings" (kích hoạt quét và báo cáo kết quả) bởi **Amazon Inspector**. Amazon Inspector sẽ tự động quét image để phát hiện các lỗ hổng bảo mật đã biết.

3.  **Giám sát:**
    * **AWS CloudTrail** tự động ghi lại "API Calls Logs" (nhật ký cuộc gọi API) từ ECR và các dịch vụ AWS khác. Những nhật ký này được gửi tới **Amazon CloudWatch** để lưu trữ và phân tích "metrics/logs". CloudWatch cũng đóng vai trò trong việc giám sát tổng thể và có thể kích hoạt các cảnh báo dựa trên các chỉ số.
    * **Amazon Inspector** phát hiện các lỗ hổng bảo mật (vulnerabilities) trong container image bạn lưu trữ trong Amazon ECR.

4.  **Dọn dẹp Tài nguyên:** Cuối cùng, workshop cũng sẽ hướng dẫn bạn cách dọn dẹp tất cả các tài nguyên AWS đã tạo để tránh phát sinh chi phí không mong muốn.

Workshop này sẽ cung cấp cho bạn kiến thức thực tế và kỹ năng cần thiết để xây dựng một quy trình quản lý container image hiện đại, bảo mật và hiệu quả chi phí trong môi trường AWS.

---

### Các dịch vụ AWS chính được sử dụng

Trong workshop này, chúng ta sẽ tương tác và cấu hình một số dịch vụ AWS quan trọng để xây dựng một hệ thống quản lý container image toàn diện. Dưới đây là giới thiệu cơ bản về từng dịch vụ:

#### Amazon Elastic Container Registry (ECR)
Amazon ECR là một dịch vụ đăng ký Docker container được quản lý hoàn toàn, an toàn, có khả năng mở rộng và đáng tin cậy. ECR cho phép các nhà phát triển dễ dàng lưu trữ, quản lý, chia sẻ và triển khai các image Docker của họ. Dịch vụ này tích hợp chặt chẽ với Amazon Elastic Container Service (ECS), Amazon Elastic Kubernetes Service (EKS) và AWS Lambda, cũng như các công cụ CI/CD của AWS và bên thứ ba.

Với Amazon ECR, bạn không cần phải vận hành cơ sở hạ tầng đăng ký container của riêng mình hoặc lo lắng về việc mở rộng, bảo mật và tính khả dụng của nó. ECR cung cấp:
* **Bảo mật cao:** Image được mã hóa khi lưu trữ và trong quá trình truyền. Tích hợp IAM để kiểm soát truy cập chi tiết.
* **Khả năng mở rộng:** Tự động mở rộng để đáp ứng nhu cầu lưu trữ image của bạn.
* **Hiệu suất cao:** Truy xuất image nhanh chóng cho các triển khai container của bạn.

#### AWS Identity and Access Management (IAM)
AWS Identity and Access Management (IAM) là một dịch vụ web giúp bạn kiểm soát an toàn quyền truy cập vào các tài nguyên AWS của mình. Với IAM, bạn có thể quản lý ai được xác thực (đăng nhập) và được ủy quyền (có quyền) để sử dụng tài nguyên.
Trong workshop này, IAM đóng vai trò nền tảng cho bảo mật bằng cách:
* **Quản lý người dùng:** Tạo và quản lý người dùng AWS để truy cập console hoặc qua CLI.
* **Quản lý vai trò (Roles):** Cho phép các dịch vụ AWS hoặc các bên thứ ba (như GitHub Actions) đảm nhận một vai trò với các quyền tạm thời để thực hiện các tác vụ.
* **Quản lý chính sách (Policies):** Định nghĩa các quyền cụ thể (cho phép hoặc từ chối) mà người dùng hoặc vai trò có thể thực hiện trên các tài nguyên AWS của bạn, đảm bảo nguyên tắc "ít đặc quyền nhất".

#### GitHub Actions
GitHub Actions là một nền tảng tích hợp liên tục (CI) và phân phối liên tục (CD) được tích hợp trực tiếp vào GitHub. Nó cho phép bạn tự động hóa các quy trình làm việc phát triển phần mềm của mình trực tiếp từ repository GitHub.
Trong workshop này, GitHub Actions được sử dụng để:
* **Tự động hóa Build:** Tự động xây dựng Docker image khi có thay đổi code.
* **Tự động hóa Push:** Đẩy Docker image đã xây dựng lên ECR.
* **Tích hợp:** Kết nối an toàn với AWS thông qua IAM Roles và OpenID Connect để thực hiện các hành động trên ECR.

#### Amazon Inspector
Amazon Inspector là một dịch vụ quản lý lỗ hổng bảo mật tự động giúp bạn khám phá và quét liên tục các khối lượng công việc AWS để tìm kiếm các lỗ hổng.
Khi tích hợp với ECR, Amazon Inspector có khả năng:
* **Quét image tự động:** Tự động quét các container image mới khi chúng được đẩy lên ECR.
* **Phát hiện lỗ hổng:** Xác định các lỗ hổng bảo mật đã biết (CVE - Common Vulnerabilities and Exposures) trong hệ điều hành, các gói ứng dụng và phụ thuộc của image.
* **Báo cáo chi tiết:** Cung cấp thông tin chi tiết về các lỗ hổng được tìm thấy, mức độ nghiêm trọng và hướng dẫn khắc phục.

#### AWS CloudTrail
AWS CloudTrail là một dịch vụ cho phép bạn giám sát và ghi lại các hoạt động của người dùng và các cuộc gọi API trên các dịch vụ AWS. Nó cung cấp bản ghi lịch sử về các sự kiện trong tài khoản AWS của bạn.
CloudTrail rất quan trọng cho:
* **Kiểm toán bảo mật:** Cung cấp dấu vết kiểm toán chi tiết về mọi tương tác với ECR và các dịch vụ khác, giúp theo dõi ai đã làm gì, khi nào và ở đâu.
* **Phân tích tuân thủ:** Hỗ trợ các yêu cầu tuân thủ bằng cách cung cấp bằng chứng về hoạt động tài khoản.
* **Khắc phục sự cố:** Giúp chẩn đoán và khắc phục sự cố bằng cách xem xét chuỗi các sự kiện đã xảy ra.

#### Amazon CloudWatch
Amazon CloudWatch là một dịch vụ giám sát và quản lý cho phép bạn thu thập và truy cập các chỉ số (metrics), nhật ký (logs) và sự kiện (events) từ các ứng dụng, tài nguyên và dịch vụ AWS của bạn.
Trong workshop, CloudWatch được sử dụng để:
* **Thu thập chỉ số:** Tự động thu thập các chỉ số về hiệu suất và hoạt động của ECR (ví dụ: dung lượng repository, số lần push/pull image).
* **Giám sát nhật ký:** Lưu trữ và phân tích nhật ký từ các dịch vụ khác (ví dụ: nhật ký API từ CloudTrail).
* **Tạo cảnh báo:** Thiết lập cảnh báo dựa trên các ngưỡng chỉ số hoặc các mẫu trong nhật ký để chủ động thông báo về các vấn đề tiềm ẩn.