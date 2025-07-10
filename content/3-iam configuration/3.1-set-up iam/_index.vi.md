---
title: "Thiết lập IAM và Policies"
weight: 1
chapter: false
pre: "<b> 3.1. </b>"
---

Để đảm bảo quy trình CI/CD của chúng ta có thể tương tác an toàn với Amazon ECR, chúng ta cần cấu hình các quyền truy cập phù hợp trong AWS IAM. Bước này bao gồm việc tạo một ECR Repository nếu chưa có, sau đó tạo một IAM User và gán các chính sách (policies) cần thiết.

#### Mục tiêu của phần này

* Tạo ECR Repository `fcj-workshop-app` nếu chưa có.
* Lấy thông tin URI của ECR Repository.
* Tạo một IAM User với quyền truy cập ECR.
* Gán các chính sách IAM cần thiết cho IAM User.

### Tạo và Lấy URI của ECR Repository

Chúng ta cần có một ECR Repository để lưu trữ các container image. Nếu bạn chưa tạo nó ở phần "Sự chuẩn bị", hãy thực hiện các bước sau. Sau đó, chúng ta sẽ lấy URI của repository này.


1.  Đăng nhập vào AWS Console: Truy cập [https://aws.amazon.com/console/](https://aws.amazon.com/console/) và đăng nhập.

2.  Chuyển đến dịch vụ ECR: Trong thanh tìm kiếm, gõ "ECR" và chọn **Elastic Container Registry**.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/001.png" alt="Tìm kiếm và chọn dịch vụ ECR trong AWS Console">}}

3.  Kiểm tra và Tạo Repository:
    * Trong bảng điều khiển ECR, chọn **Repositories** ở thanh điều hướng bên trái.
    * **Nếu bạn CHƯA CÓ** repository tên `fcj-workshop-app`, hãy làm theo các bước sau để tạo:
        * Nhấp vào nút **Create repository**.
        * Đối với **Visibility settings**, chọn **Private**.
        * Trong phần **Repository name**, nhập `fcj-workshop-app`.
        * Nhấp vào nút **Create repository** ở cuối trang.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/002.png" alt="Tạo ECR Repository mới">}}

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/0002.png" alt="Tạo ECR Repository">}}

    * **Nếu bạn ĐÃ CÓ** repository `fcj-workshop-app` từ phần chuẩn bị, hãy chuyển sang bước tiếp theo.

4.  Chọn Repository của bạn và Sao chép URI:
    * Từ danh sách các repository, tìm và nhấp vào tên repository `fcj-workshop-app`.
    * Trên trang chi tiết của repository, bạn sẽ thấy **URI** của repository. Hãy sao chép URI này. Nó sẽ có dạng tương tự như: `123456789012.dkr.ecr.ap-southeast-1.amazonaws.com/fcj-workshop-app` (trong đó `123456789012` là ID tài khoản AWS của bạn và `ap-southeast-1` là Region).

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/003.png" alt="Chọn repository fcj-workshop-app và sao chép URI" >}}

    {{% notice note %}}
  Hãy giữ lại URI này. Chúng ta sẽ cần sử dụng nó trong các bước cấu hình GitHub Actions sau này để chỉ định repository mà image sẽ được đẩy tới.
    {{% /notice %}}

### Tạo IAM Policy cho quyền truy cập ECR

Thay vì sử dụng chính sách có sẵn, chúng ta sẽ tạo một IAM Policy tùy chỉnh thông qua giao diện console để kiểm soát chi tiết hơn các quyền mà người dùng CI/CD sẽ có trên ECR.

5. *Chuyển đến dịch vụ IAM: Trong thanh tìm kiếm của AWS Console, gõ "IAM" và chọn **IAM**.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/004.png" alt="Tìm kiếm và chọn dịch vụ IAM trong AWS Console">}}

2.  Tạo Policy mới:
    * Trong bảng điều khiển IAM, chọn **Policies** ở thanh điều hướng bên trái.
    * Nhấp vào nút **Create policy**.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/005.png" alt="Bảng điều khiển IAM và nút Create policy">}}

3.  Chọn dịch vụ và hành động:
    * Trên trang **Specify permissions**, chọn tab **Visual editor**.
    * Đối với **Service**, tìm và chọn **Elastic Container Registry**.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/011.png" alt="Chọn dịch vụ ECR trong Visual Editor">}}

    * Trong phần **Actions allowed**, mở rộng **Write** và chọn các hành động sau:
        * `CompleteLayerUpload`
        * `InitiateLayerUpload`
        * `PutImage`
        * `UploadLayerPart`
    * Mở rộng **Read** và chọn các hành động sau:
        * `BatchCheckLayerAvailability`
        * `BatchGetImage`
        * `DescribeImages`
        * `GetDownloadUrlForLayer`
    * Trong phần **Access level** bổ sung, tìm và chọn hành động `GetAuthorizationToken`.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/012.png" alt="Chọn các hành động ECR cần thiết">}}

    * Trong phần **Resources**, chọn **Specific**.
    * Đối với **repository**, nhấp vào **Add ARN**.
    * Nhập các thông tin sau:
        * **Region:** Chọn Region AWS của bạn (ví dụ: `ap-southeast-1`).
        * **Account ID:** Nhập ID tài khoản AWS của bạn.
        * **Repository name:** Nhập `fcj-workshop-app`.
    * Nhấp vào **Add ARN**.
    * Đối với tài nguyên `GetAuthorizationToken` (nếu có tùy chọn này riêng biệt), hãy để là **Any** hoặc thêm một dòng riêng cho hành động này với `Resource: *` nếu cần. (Thường thì `GetAuthorizationToken` không giới hạn theo resource repository cụ thể mà theo account/region).

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/013.png" alt="Cấu hình tài nguyên cụ thể cho ECR">}}

    {{% notice note %}}
  Các quyền `ecr:*` được chọn cho phép user thực hiện các thao tác đẩy (push) và kéo (pull) image.
  `ecr:GetAuthorizationToken`: Cho phép user lấy mã thông báo xác thực để đăng nhập Docker vào ECR.
  `Resource`: Chúng ta chỉ định chính sách này chỉ áp dụng cho repository `fcj-workshop-app` của bạn, tuân thủ nguyên tắc ít đặc quyền nhất.
    {{% /notice %}}

1.  Thêm Tags (Tùy chọn): Bạn có thể thêm các thẻ để dễ quản lý. Nhấp vào **Next: Review policy**.

2.  Xem lại và Tạo Policy:
    * Đối với **Policy name**, nhập `fcj-ecr-ci-cd-policy`.
    * (Tùy chọn) Nhập **Description**. nhập `policy for CI-CD`.
    * Nhấp vào nút **Create policy**.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/015.png" alt="Đặt tên và xem lại IAM Policy">}}

    {{% notice tip %}}
Việc tạo một chính sách tùy chỉnh như thế này cho phép bạn kiểm soát chính xác hơn các quyền mà người dùng hoặc vai trò có. Đây là một thực hành tốt hơn so với việc gán các chính sách quản lý (managed policies) có phạm vi rộng hơn cho mục đích CI/CD.
    {{% /notice %}}

### Tạo IAM User và Gán Policy cho CI/CD

Bây giờ chúng ta sẽ tạo một IAM User và gán chính sách tùy chỉnh `fcj-ecr-ci-cd-policy` vừa tạo cho user này.

10.  Chuyển đến dịch vụ IAM: Đảm bảo bạn đang ở bảng điều khiển IAM.

11.  Tạo User mới:
    * Trong bảng điều khiển IAM, chọn **Users** ở thanh điều hướng bên trái.
    * Nhấp vào nút **Create user**.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/016.png" alt="Bảng điều khiển IAM và nút Create user">}}

    * Đối với **User name**, nhập `fcj-ecr-ci-cd-user`.
    * Chọn **Provide user access to the AWS Management Console** cho user CI/CD.
    * Nhấp vào **Next**.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/017.png" alt="Thiết lập tên người dùng và tùy chọn truy cập" >}}

12.  Gán quyền (Permissions):
    * Trên trang **Set permissions**, chọn **Attach policies directly**.
    * Trong ô tìm kiếm, gõ `fcj-ecr-ci-cd-policy` (tên chính sách bạn vừa tạo) và chọn chính sách này.
    * Nhấp vào **Next**.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/018.png" alt="Gán chính sách tùy chỉnh fcj-ecr-ci-cd-policy">}}

13.  Xem lại và Tạo User:
    * Trên trang **Review and create**, xem lại các thiết lập.
    * Nhấp vào nút **Create user**.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/019.png" alt="Xem lại thông tin và tạo người dùng">}}

14.  ưu Access Key:
    * Sau khi user được tạo, bạn sẽ thấy thông báo thành công. Nhấp vào **View user** hoặc quay lại danh sách user và nhấp vào user `fcj-ecr-ci-cd-user` vừa tạo.
    * Chuyển đến tab **Security credentials**.
    * Trong phần **Access keys**, nhấp vào **Create access key**.
    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/020.png" alt="Tạo Access Key cho IAM User">}}
15.  Chọn **Third-party service** làm trường hợp sử dụng.
    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/021.png" alt="Lưu Access Key ID và Secret Access Key" >}}
    * Đánh dấu vào ô xác nhận và nhấp **Next**.
16. (Tùy chọn) Nhập mô tả cho Access Key (ví dụ: `for-github-actions`).
    * Nhấp vào **Done**.

    {{< figure src="/fcj-ecr-container-registry-workshop/images/3/022.png" alt="Access Key ID và Secret Access Key" >}}

    {{% notice warning %}}
 **Secret Access Key chỉ hiển thị MỘT LẦN duy nhất ngay sau khi tạo.** Hãy chắc chắn rằng bạn đã sao chép cả **Access Key ID** và **Secret Access Key** và lưu trữ chúng ở một nơi an toàn. Bạn sẽ không thể xem lại Secret Access Key này sau khi đóng cửa sổ này. Chúng ta sẽ cần sử dụng chúng để cấu hình GitHub Actions trong các phần sau.
    {{% /notice %}}

    {{% notice tip %}}
 Trong môi trường sản xuất thực tế, việc sử dụng IAM Role với OpenID Connect (OIDC) là phương pháp được khuyến nghị hơn để cấp quyền cho CI/CD. Phương pháp này giúp tránh việc phải lưu trữ Access Key dài hạn, tăng cường bảo mật. Tuy nhiên, để đơn giản hóa cho workshop này, chúng ta sẽ sử dụng Access Key.
    {{% /notice %}}

---