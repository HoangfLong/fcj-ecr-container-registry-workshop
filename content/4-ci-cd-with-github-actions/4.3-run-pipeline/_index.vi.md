---
title: "Chạy và test thử Pipeline"
weight: 4
chapter: false
pre: "<b> 4.3. </b>"
---

Sau khi đã cấu hình GitHub Repository Secrets và hiểu rõ về GitHub Actions Workflow, bây giờ chúng ta sẽ kích hoạt pipeline CI/CD để kiểm tra xem mọi thứ có hoạt động đúng như mong đợi hay không.

#### Mục tiêu của phần này

* Kích hoạt pipeline CI/CD bằng cách đẩy thay đổi lên GitHub.
* Theo dõi quá trình chạy workflow trên giao diện GitHub Actions.
* Xác minh kết quả: kiểm tra Git tag mới được tạo và Docker image trên Amazon ECR.

### Kích hoạt Pipeline CI/CD

Pipeline CI/CD của bạn được cấu hình để tự động chạy mỗi khi có code được đẩy lên nhánh `main`. Để kích hoạt pipeline, chúng ta sẽ thực hiện một thay đổi nhỏ và đẩy lên repository.


1.  **Tạo một thay đổi nhỏ:**
    * Mở thư mục dự án `aws-container-security-workshop` trên máy tính cục bộ của bạn.
    * Mở file `fcj\src\static\js\app.js` (hoặc bất kỳ file nào khác, ví dụ: thêm một dòng comment vào file mã nguồn).
    * Thêm một dòng chữ bất kỳ vào vào thư mục src\static\js\app.js dòng **56** là `v1.0.0`
    * Lưu lại file.
 {{< figure src="/images/4/4.3/0011.png" alt="Truy cập folder" >}}

1.  **Đẩy thay đổi lên GitHub:**
    * Mở Terminal/Command Prompt (đảm bảo bạn đang ở trong thư mục dự án).
    * Thêm các thay đổi vào staging area:
        ```bash
        git add .
        ```
    * Tạo một commit với thông điệp rõ ràng:
        ```bash
        git commit -m "Trigger CI/CD pipeline for testing"
        ```
    * Đẩy commit này lên nhánh `main` của repository GitHub của bạn:
        ```bash
        git push origin main
        ```

    {{% notice note %}}
  Ngay sau khi bạn đẩy code lên nhánh `main`, GitHub Actions sẽ tự động phát hiện thay đổi và bắt đầu chạy workflow `CI/CD Pipeline` mà bạn đã cấu hình.
    {{% /notice %}}

### Theo dõi quá trình chạy Pipeline

Bây giờ, hãy truy cập GitHub và theo dõi quá trình chạy của pipeline.

**Các bước thực hiện:**

1.  **Truy cập GitHub Actions:**
    * Mở trình duyệt và điều hướng đến repository của bạn trên GitHub.
    * Nhấp vào tab **Actions**.

    {{< figure src="/images/4/4.3/001.png" alt="Truy cập tab Actions trên GitHub" >}}
2.  **Xem trạng thái Workflow:**
    * Bạn sẽ thấy một workflow có tên `CI/CD Pipeline` với trạng thái `In progress` (đang chạy) hoặc `Queued` (đang chờ).
    * Nhấp vào tên của workflow vừa chạy (dựa trên thông điệp commit bạn vừa tạo) để xem chi tiết.

    {{< figure src="/images/4/4.3/002.png" alt="Danh sách các lần chạy workflow trên GitHub Actions" >}}

3.  **Kiểm tra các bước của Job:**
    * Trong trang chi tiết workflow, bạn sẽ thấy job `build-and-push`. Nhấp vào đó để xem các bước cụ thể đang được thực hiện.
    * Theo dõi log đầu ra của từng bước để đảm bảo chúng hoàn thành thành công. Bạn sẽ thấy các bước như `checkout`, `configure aws credentials`, `login to amazon ECR`, `Automatic Tagging of Releases`, và `build, tag, and push the image to ECR` lần lượt chạy.
    * Đảm bảo rằng tất cả các bước đều có dấu tích xanh (thành công). Nếu có bất kỳ bước nào thất bại, hãy xem lại log để xác định nguyên nhân lỗi.

    {{< figure src="/images/4/4.3/003.png" alt="Chi tiết các bước trong một job của GitHub Actions" >}}
    {{< figure src="/images/4/4.3/003-b.png" alt="Chi tiết các bước trong một job của GitHub Actions" >}}

### Xác minh kết quả Pipeline

Sau khi workflow hoàn thành thành công, chúng ta cần xác minh rằng các kết quả mong đợi đã được tạo ra.

1.  **Kiểm tra Git Tag mới trên GitHub:**
    * Quay lại trang chính của repository trên GitHub.
    * Nhấp vào **"Tags"** hoặc **"Releases"** (thường nằm ở thanh bên phải hoặc dưới phần "Code").
    * Bạn sẽ thấy một Git tag mới đã được tạo với phiên bản tăng lên (ví dụ: `v0.0.1` nếu đây là lần chạy đầu tiên hoặc phiên bản tiếp theo sau `v0.0.0`). Tag này được tạo tự động bởi script `git_update.sh`.

    {{< figure src="/images/4/4.3/004.png" alt="Kiểm tra Git Tags trên GitHub repository" >}}
    {{< figure src="/images/4/4.3/004-b.png" alt="Kiểm tra Git Tags trên GitHub repository" >}}

2.  **Kiểm tra Docker Image trên Amazon ECR:**
    * Đăng nhập vào **AWS Management Console**.
    * Điều hướng đến dịch vụ **ECR** (Elastic Container Registry).
    * Trong bảng điều khiển ECR, tìm và nhấp vào repository của bạn có tên `fcj-workshop-app`.
    * Bạn sẽ thấy một Docker image mới đã được push lên, với tag (phiên bản) tương ứng với Git tag bạn vừa thấy trên GitHub (ví dụ: `v0.0.1`).

    {{< figure src="/images/4/4.3/005.png" alt="Kiểm tra Docker Image trên Amazon ECR" >}}

    {{% notice note %}}
Nếu bạn thấy Git tag mới trên GitHub và Docker image mới trên ECR với cùng một phiên bản, điều đó có nghĩa là pipeline CI/CD của bạn đã được thiết lập và hoạt động thành công!
    {{% /notice %}}

---