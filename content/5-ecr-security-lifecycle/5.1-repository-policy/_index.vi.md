---
title: "Repository Access Policy"
weight: 1
chapter: false
pre: "<b> 5.1. </b>"
---

Trong AWS ECR, Repository Access Policy (Chính sách truy cập Repository) là một tính năng mạnh mẽ cho phép bạn kiểm soát chi tiết ai hoặc dịch vụ nào có thể tương tác với các Docker image trong repository của bạn. Không giống như các chính sách IAM dựa trên danh tính (Identity-based policies), Repository Policy là một chính sách dựa trên tài nguyên (Resource-based policy) được gắn trực tiếp vào từng ECR repository cụ thể.

Điều này cho phép bạn định nghĩa các quyền truy cập (ví dụ: ai có thể kéo - pull, đẩy - push image) ngay tại cấp độ của repository, bổ sung thêm một lớp bảo mật và kiểm soát truy cập tinh vi hơn.

#### Mục tiêu của phần này

* Hiểu vai trò và tầm quan trọng của ECR Repository Policy.
* Phân biệt rõ ràng giữa IAM Identity-based policies và ECR Repository policies.
* Cấu hình một Repository Policy để giới hạn hoặc cấp quyền truy cập.
* Áp dụng chính sách để tăng cường bảo mật cho các Docker image của bạn.

### Khái niệm Repository Policy và sự khác biệt với IAM Policies

Để hiểu rõ hơn về Repository Policy, điều quan trọng là phải phân biệt nó với IAM Identity-based policies:

* **IAM Identity-based Policies (Chính sách dựa trên danh tính):**
    * Được gắn vào một **IAM Principal** (người dùng, nhóm hoặc vai trò IAM).
    * Xác định những **hành động nào** mà Principal đó *được phép* thực hiện *trên những tài nguyên nào*.
    * Ví dụ: Một chính sách IAM có thể cho phép một người dùng nhất định thực hiện hành động `ecr:PutImage` trên *tất cả* các ECR repository trong tài khoản.

* **ECR Repository Policies (Chính sách dựa trên tài nguyên):**
    * Được gắn trực tiếp vào một **tài nguyên cụ thể** (trong trường hợp này là một ECR repository).
    * Xác định **Principal nào** *được phép* thực hiện những **hành động nào** *trên chính tài nguyên đó*.
    * Ví dụ: Một chính sách Repository có thể cho phép một IAM role từ một tài khoản AWS khác truy cập vào repository này.

**Nguyên tắc đánh giá quyền (Logic "AND"):**

Để một Principal có thể thực hiện một hành động trên một ECR repository, hành động đó phải được **cả hai** chính sách cho phép:

1.  Chính sách IAM (Identity-based policy) của Principal đó **phải cho phép** hành động.
2.  Chính sách ECR Repository (Resource-based policy) của repository **phải cho phép** hành động đối với Principal đó.

Nếu một trong hai chính sách từ chối rõ ràng (`Effect: Deny`) hành động, thì hành động đó sẽ bị từ chối, bất kể chính sách còn lại có cho phép hay không.

Sự kết hợp này mang lại sự linh hoạt và kiểm soát chi tiết hơn, đặc biệt hữu ích trong các trường hợp như:
* Cấp quyền truy cập tài khoản chéo (cross-account access).
* Kiểm soát quyền truy cập cho một số lượng lớn Principal dựa trên các thuộc tính của tài nguyên.

Để tìm hiểu sâu hơn về sự khác biệt giữa các loại chính sách trong AWS, bạn có thể tham khảo tài liệu chính thức của AWS:
* [Private repository policies in Amazon ECR - Repository policies vs IAM policies](https://docs.aws.amazon.com/AmazonECR/latest/userguide/repository-policies.html#repository-policies-vs-iam-policies)
* [Identity-based policies and resource-based policies - AWS IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_identity-vs-resource.html)

### Cấu hình Repository Policy

Chúng ta sẽ cấu hình một Repository Policy cho repository `fcj-workshop-app` để minh họa cách kiểm soát quyền truy cập. Ví dụ dưới đây sẽ cho phép IAM user `fcj-ecr-ci-cd-user` (mà chúng ta đã tạo) có quyền đẩy và kéo image từ repository này.


1.  **Truy cập dịch vụ ECR:**
    * Trong thanh tìm kiếm, gõ "ECR" và chọn **Elastic Container Registry**.
 {{< figure src="/fcj-ecr-container-registry-workshop/images/5/5.1/001-a.png" alt="Chọn Repository trong ECR" >}}

2.  **Chọn Repository:**
    * Trong bảng điều khiển ECR, nhấp vào **Repositories** ở thanh điều hướng bên trái.
    * Tìm và nhấp vào repository có tên `fcj-workshop-app` của bạn.

3.  **Chỉnh sửa Repository Policy:**
    * Trong trang chi tiết của repository `fcj-workshop-app`, nhấp vào tab **Permissions**.
    * Nhấp vào nút **Edit repository policy**.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/5/5.1/002.png" alt="Chỉnh sửa Repository Policy" >}}

4.  **Dán chính sách JSON đã điều chỉnh:**
    * Một trình soạn thảo JSON sẽ hiện ra. Xóa bất kỳ nội dung nào có sẵn và **dán chính sách JSON an toàn sau** vào đó:

    ```json
    {
      "Version": "2008-10-17",
      "Statement": [
        {
          "Sid": "AllowPushPullFromCICDUser",
          "Effect": "Allow",
          "Principal": {
            "AWS": "arn:aws:iam::<YOUR_AWS_ACCOUNT_ID>:user/fcj-ecr-ci-cd-user"
          },
          "Action": [
            "ecr:GetDownloadUrlForLayer",
            "ecr:BatchGetImage",
            "ecr:BatchCheckLayerAvailability",
            "ecr:PutImage",
            "ecr:InitiateLayerUpload",
            "ecr:UploadLayerPart",
            "ecr:CompleteLayerUpload"
          ]
        }
      ]
    }
    ```
 
  {{% notice info %}}
Thay thế `<YOUR_AWS_ACCOUNT_ID>` bằng **AWS Account ID của bạn**. Bạn có thể tìm thấy AWS Account ID của mình bằng cách nhấp vào tên tài khoản ở góc trên bên phải của AWS Management Console.
  {{% /notice %}}


  {{< figure src="/fcj-ecr-container-registry-workshop/images/5/5.1/003.png" alt="Dán Repository Policy JSON" >}}

5.  **Lưu chính sách và kết quả:**
 
   {{< figure src="/fcj-ecr-container-registry-workshop/images/5/5.1/004.png" alt="result" >}}

### Giải thích Repository Policy

- **`Sid: "AllowPushPullFromCICDUser"`**  
  Cho phép người dùng IAM `fcj-ecr-ci-cd-user` thực hiện các hành động cần thiết để kéo (pull) và đẩy (push) Docker image:
  - `ecr:GetDownloadUrlForLayer`: Lấy URL để tải xuống lớp (layer) của image.
  - `ecr:BatchGetImage`: Lấy siêu dữ liệu và cấu hình của nhiều image.
  - `ecr:BatchCheckLayerAvailability`: Kiểm tra xem các lớp của image có sẵn trong repository không.
  - `ecr:PutImage`: Đẩy một image manifest lên repository.
  - `ecr:InitiateLayerUpload`: Bắt đầu quá trình tải lên một lớp image.
  - `ecr:UploadLayerPart`: Tải lên một phần của lớp image.
  - `ecr:CompleteLayerUpload`: Hoàn thành quá trình tải lên lớp image.

Bạn đã cấu hình thành công Repository Access Policy cho ECR repository của mình một cách an toàn. Chính sách này sẽ giúp tăng cường bảo mật bằng cách kiểm soát chặt chẽ hơn quyền truy cập vào các Docker image của bạn.

---