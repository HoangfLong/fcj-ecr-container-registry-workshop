---
title: "Giám sát ECR với AWS CloudTrail và Amazon CloudWatch"
weight: 1
chapter: false
pre: "<b> 6.1. </b>"
---

Để có cái nhìn toàn diện về các hoạt động diễn ra trong Amazon ECR và đảm bảo tuân thủ các chính sách bảo mật, việc tận dụng **AWS CloudTrail** và **Amazon CloudWatch** là rất cần thiết. Hai dịch vụ này làm việc cùng nhau để cung cấp khả năng giám sát mạnh mẽ, giúp bạn theo dõi ai đã làm gì, khi nào và ở đâu trong môi trường registry của mình.

Trong phần này, bạn sẽ học cách:

* Đảm bảo CloudTrail ghi lại hoạt động ECR và gửi nhật ký đến CloudWatch Logs.
* Theo dõi các cuộc gọi API liên quan đến ECR.
* Phân tích nhật ký và giám sát chỉ số hiệu suất của ECR.

Để có cái nhìn toàn diện về các hoạt động diễn ra trong Amazon ECR và đảm bảo tuân thủ các chính sách bảo mật, việc tận dụng **AWS CloudTrail** và **Amazon CloudWatch** là rất cần thiết. Hai dịch vụ này làm việc cùng nhau để cung cấp khả năng giám sát mạnh mẽ, giúp bạn theo dõi ai đã làm gì, khi nào và ở đâu trong môi trường registry của mình.

Trong phần này, bạn sẽ học cách:

* Đảm bảo CloudTrail ghi lại hoạt động ECR và gửi nhật ký đến CloudWatch Logs.
* Theo dõi các cuộc gọi API liên quan đến ECR.
* Phân tích nhật ký và giám sát chỉ số hiệu suất của ECR.

### Thiết lập CloudTrail để giám sát ECR

1.  **Đăng nhập và truy cập CloudTrail:**
    * Đăng nhập vào [AWS Management Console](https://aws.amazon.com/console/).
    * Trong thanh tìm kiếm, gõ `CloudTrail` và chọn dịch vụ **CloudTrail** từ kết quả.

    {{< figure src="/images/6/6.1/001.png" alt="Tìm kiếm và chọn dịch vụ CloudTrail" >}}

2.  **Kiểm tra trạng thái Trail và quyết định hành động:**
    * Trong bảng điều khiển CloudTrail, chọn **"Trails"** từ menu bên trái.
    * Kiểm tra xem bạn đã có Trail nào đang hoạt động và gửi nhật ký đến CloudWatch Logs chưa.
        * **Nếu cần tạo Trail mới hoặc cấu hình Trail hiện có:** Tiếp tục với **Bước 3**.
        * **Nếu Trail đã được cấu hình đúng:** Bạn có thể bỏ qua các bước cấu hình Trail và chuyển trực tiếp đến **Bước 9**.

    {{< figure src="/images/6/6.1/002.png" alt="Kiểm tra danh sách Trails trong CloudTrail" >}}

3.  **Tạo hoặc chỉnh sửa Trail để gửi nhật ký đến CloudWatch Logs:**
    * **Nếu tạo Trail mới:** Nhấp vào **"Create trail"**.
    * **Nếu chỉnh sửa Trail hiện có:** Nhấp vào tên Trail đó, sau đó tìm phần **"CloudWatch Logs"** và nhấp **"Edit"**.

    {{< figure src="/images/6/6.1/003.png" alt="Bắt đầu tạo hoặc chỉnh sửa Trail" >}}

4.  **Cấu hình chi tiết Trail (nếu tạo mới):**
    * **Trail name:** Đặt tên cho Trail (ví dụ: `ecr-monitor-trail`).
    * **Storage location:** Chọn **"Create new S3 bucket"** và đặt tên (hoặc dùng bucket hiện có).
    * **CloudWatch Logs:** **Chọn "Enabled"**. Chọn **"New"** để tạo Log group mới (ví dụ: `/aws/cloudtrail/ecr-logs`) hoặc chọn Log group hiện có.
    * (Các tùy chọn khác giữ mặc định hoặc bỏ qua).

    {{< figure src="/images/6/6.1/004.png" alt="Cấu hình tên Trail, S3 bucket và CloudWatch Logs" >}}

5.  **Cấu hình chi tiết Trail (tiếp theo):**
    * Nhấp **"Next"**.
    * Trong phần **"Choose log events"**, đảm bảo **"Management events"** được chọn.

    {{< figure src="/images/6/6.1/005.png" alt="Chọn 'Management events' cho Trail" >}}

6.  **Hoàn tất tạo Trail:**
    * Nhấp **"Next"**.
    * Nhấp **"Create trail"** (nếu tạo mới) hoặc **"Save changes"** (nếu chỉnh sửa).

    {{< figure src="/images/6/6.1/006.png" alt="Hoàn tất tạo hoặc lưu cấu hình Trail" >}}

### Xem và phân tích nhật ký ECR

7.  **Truy cập Lịch sử Sự kiện CloudTrail:**
    * Trong bảng điều khiển CloudTrail, chọn **"Event history"** từ menu bên trái.

    {{< figure src="/images/6/6.1/007.png" alt="Chọn 'Event history' trong CloudTrail" >}}

8.  **Lọc sự kiện theo ECR:**
    * Nhấp vào bộ lọc **"Attribute"**, chọn **"Event source"**.
    * Trong trường bên cạnh, gõ `ecr.amazonaws.com` và nhấn `Enter`.

    {{< figure src="/images/6/6.1/008.png" alt="Lọc lịch sử sự kiện CloudTrail theo ECR" >}}

9.  **Truy cập Log groups trong CloudWatch:**
    * Trong thanh tìm kiếm AWS Console, gõ `CloudWatch` và chọn dịch vụ **CloudWatch**.
    * Trong bảng điều khiển CloudWatch, chọn **"Log groups"** dưới phần **"Logs"**.

    {{< figure src="/images/6/6.1/009.png" alt="Truy cập Log groups trong CloudWatch" >}}

10. **Tìm Log group của CloudTrail:**
    * Trong danh sách Log group, tìm Log group mà CloudTrail đang gửi nhật ký tới (đã cấu hình ở Bước 4).
    * Nhấp vào tên của Log group đó để xem các luồng nhật ký.

    {{< figure src="/images/6/6.1/010.png" alt="Chọn Log group của CloudTrail trong CloudWatch Logs" >}}


### Giám sát chỉ số và tạo Dashboard

11. **Giám sát chỉ số ECR trong CloudWatch Metrics:**
    * Trong bảng điều khiển CloudWatch, chọn **"Metrics"** từ menu bên trái.
    * Trong phần "All metrics", chọn namespace **"ECR"** hoặc **"AWS/ECR"**.
    * Chọn các chỉ số ECR bạn muốn xem (ví dụ: `RepositorySize`, `ImageCount`).

    {{< figure src="/images/6/6.1/012.png" alt="Xem chỉ số ECR trong CloudWatch Metrics" >}}

12. **Tạo Dashboard tùy chỉnh cho ECR (Tùy chọn):**
    * Trong bảng điều khiển CloudWatch, chọn **"Dashboards"**.
    * Nhấp **"Create dashboard"**, đặt tên (ví dụ: `ECR-Operations-Dashboard`), và nhấp **"Create dashboard"**.
      {{< figure src="/images/6/6.1/014.png" alt="Tạo Dashboard tùy chỉnh cho ECR" >}}

13. Thêm các widget.
    
      {{< figure src="/images/6/6.1/015.png" alt="Tạo Dashboard tùy chỉnh cho ECR" >}}
14. Chọn metic cần thêm
    {{< figure src="/images/6/6.1/015.png" alt="Tạo Dashboard tùy chỉnh cho ECR" >}}
15. Chọn ECR
    {{< figure src="/images/6/6.1/016-a.png" alt="Tạo Dashboard tùy chỉnh cho ECR" >}}
16. Chọn ECR repository của bạn
    {{< figure src="/images/6/6.1/016.png" alt="Tạo Dashboard tùy chỉnh cho ECR" >}}
17. Chọn create widget
    {{< figure src="/images/6/6.1/017.png" alt="Tạo Dashboard tùy chỉnh cho ECR" >}}

    {{< figure src="/images/6/6.1/018.png" alt="Tạo Dashboard tùy chỉnh cho ECR" >}}

Bằng cách thiết lập AWS CloudTrail để ghi lại các sự kiện ECR và gửi chúng đến Amazon CloudWatch Logs, bạn đã tạo ra một hệ thống giám sát mạnh mẽ. Giờ đây, bạn có thể dễ dàng theo dõi các hoạt động API trong ECR, phân tích nhật ký chi tiết bằng CloudWatch Logs Insights và trực quan hóa các chỉ số hiệu suất quan trọng của ECR thông qua các CloudWatch Dashboard tùy chỉnh. Điều này giúp bạn không chỉ nắm bắt được ai đã làm gì và khi nào trong các kho chứa image của mình, mà còn chủ động theo dõi tình trạng và hiệu suất của ECR, từ đó tăng cường bảo mật và hiệu quả hoạt động