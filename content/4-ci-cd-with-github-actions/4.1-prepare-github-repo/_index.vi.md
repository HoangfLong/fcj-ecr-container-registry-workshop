---
title: "Chuẩn bị Github Repository"
weight: 1
chapter: false
pre: "<b> 4.1. </b>"
---

Để thiết lập luồng CI/CD với GitHub Actions, trước tiên chúng ta cần chuẩn bị repository chứa mã nguồn ứng dụng mẫu và cấu hình các biến môi trường bảo mật (secrets) trong GitHub.

#### Mục tiêu của phần này

* Clone repository mã nguồn ứng dụng về máy tính cục bộ.
* Tạo một GitHub Repository mới trên tài khoản cá nhân.
* Đẩy mã nguồn đã clone lên GitHub Repository mới tạo.
* Cấu hình GitHub Repository Secrets để lưu trữ thông tin xác thực AWS và URI của ECR.

### Clone Repository về máy cục bộ

Bạn cần clone repository chứa mã nguồn ứng dụng mẫu về máy tính cục bộ để có thể chỉnh sửa và đẩy (push) code lên repository của riêng bạn.

1.  Mở Terminal / Command Prompt: Mở cửa sổ dòng lệnh trên máy tính của bạn.

2.  Clone Repository gốc: Tải mã nguồn từ repository mẫu về máy tính của bạn.

    ```bash
    git clone https://github.com/HoangfLong/aws-container-security-workshop.git
    ```

    {{< figure src="/fcj-ecr-container-registry-workshop/images/4/4.1/001.png" alt="Clone repository mẫu về máy cục bộ" >}}


### Tạo Repository mới trên GitHub cá nhân và đẩy mã nguồn lên

Sau khi đã clone mã nguồn về máy, bạn cần tạo một repository mới trên tài khoản GitHub của mình và đẩy mã nguồn này lên đó. Đây sẽ là repository mà GitHub Actions của bạn sẽ theo dõi.

3.  Tạo Repository mới trên GitHub:
    * Đăng nhập vào tài khoản GitHub của bạn.
    * Nhấp vào dấu **+** ở góc trên bên phải, sau đó chọn **New repository**. Đặt tên cho repository của bạn (ví dụ: `aws-container-security-workshop-my-repo`), chọn loại **Public** hoặc **Private** tùy ý, và **KHÔNG** đánh dấu chọn "Add a README file" hay các tùy chọn khởi tạo khác. Cuối cùng, nhấp vào **Create repository**.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/4/4.1/002.png" alt="Tạo repository mới trên GitHub" >}}
    {{< figure src="/fcj-ecr-container-registry-workshop/images/4/4.1/003.png" alt="Điền thông tin và tạo repository mới" >}}

4.  Liên kết và đẩy mã nguồn lên Repository mới:
    * Quay lại Terminal/Command Prompt (đảm bảo bạn đang ở trong thư mục dự án đã clone).
    * Xóa liên kết remote cũ (nếu có):
        ```bash
        git remote remove origin
        ```
    * Thêm liên kết đến repository mới của bạn trên GitHub:
        ```bash
        git remote add origin [https://github.com/YOUR_GITHUB_USERNAME/YOUR_NEW_REPOSITORY_NAME.git](https://github.com/YOUR_GITHUB_USERNAME/YOUR_NEW_REPOSITORY_NAME.git)
        ```
        *(Thay `YOUR_GITHUB_USERNAME` và `YOUR_NEW_REPOSITORY_NAME` bằng thông tin của bạn).*
    * Đặt lại nhánh chính thành `main` (nếu cần):
        ```bash
        git branch -M main
        ```
    * Đẩy mã nguồn lên repository mới của bạn:
        ```bash
        git push -u origin main
        ```
        *(Bạn có thể được yêu cầu đăng nhập bằng tên người dùng và Mã thông báo truy cập cá nhân (Personal Access Token) của GitHub).*

    {{< figure src="/fcj-ecr-container-registry-workshop/images/4/4.1/003-b.png" alt="Kết nối và đẩy mã nguồn lên repository mới" >}}

### Cấu hình GitHub Repository Secrets

Để GitHub Actions có thể tương tác an toàn với tài khoản AWS và Amazon ECR của bạn, chúng ta cần lưu trữ các thông tin nhạy cảm (như Access Key, Secret Key của IAM User, Region AWS và URI của ECR) dưới dạng GitHub Repository Secrets. Điều này đảm bảo các thông tin này không bị lộ ra trong mã nguồn hoặc nhật ký workflow.

5.  Truy cập Repository Settings: Từ trang repository đã đẩy code lên của bạn trên GitHub, nhấp vào tab **Settings**.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/4/4.1/004.png" alt="Tab Settings trên GitHub Repository" >}}

6.  Chọn Secrets and Variables: Trong thanh điều hướng bên trái, cuộn xuống và chọn **Secrets and variables**, sau đó chọn **Actions**.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/4/4.1/005.png" alt="Chọn Secrets and variables -> Actions" >}}

7.  Thêm các Secrets cần thiết: Nhấp vào nút **New repository secret** để thêm từng secret một:

    * `AWS_ACCESS_KEY_ID`: Dán giá trị **Access Key ID** đã tạo trong AWS (từ Mục 3.1).

    * `AWS_REGION`: Dán giá trị **Region AWS** của bạn (ví dụ: `ap-southeast-1` hoặc `us-east-1`). *(Chọn Region mà bạn đã tạo IAM User và ECR Repository).*

    * `AWS_SECRET_ACCESS_KEY`: Dán giá trị **Secret Access Key** đã tạo trong AWS (từ Mục 3.1).

    * `ECR_REPOSITORY_URI`: Dán **URI** của ECR Repository `fcj-workshop-app` của bạn (từ Mục 3.1).

    {{< figure src="/fcj-ecr-container-registry-workshop/images/4/4.1/006.png" alt="Thêm các GitHub Secrets" >}}

    {{% notice note %}}
Sau khi thêm, bạn sẽ thấy bốn secrets này trong danh sách. Chúng sẽ được GitHub Actions sử dụng một cách an toàn mà không bị lộ ra ngoài.
    {{% /notice %}}

---