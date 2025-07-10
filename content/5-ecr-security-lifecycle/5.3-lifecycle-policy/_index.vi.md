---
title: "Quản lý vòng đời Image ECR"
weight: 3
chapter: false
pre: "<b> 5.3. </b>"
---

Bảo mật và quản lý chi phí là hai yếu tố quan trọng khi làm việc với các Docker image trong Amazon ECR. Theo mặc định, ECR sẽ giữ tất cả các phiên bản image của bạn vô thời hạn, điều này có thể dẫn đến việc tích lũy các image cũ không cần thiết, làm tăng chi phí lưu trữ và khiến repository trở nên lộn xộn.

Chính sách vòng đời (Lifecycle Policies) của ECR cho phép bạn tự động quản lý các image này. Bạn có thể định cấu hình các quy tắc để xóa các image dựa trên tuổi tác, trạng thái thẻ (tagged/untagged), hoặc số lượng image. Điều này giúp bạn duy trì các repository ECR sạch sẽ, chỉ giữ lại những image cần thiết và tối ưu hóa chi phí.

#### Mục tiêu của phần này

* Hiểu tầm quan trọng của việc quản lý vòng đời cho Docker image.
* Tạo và cấu hình chính sách vòng đời ECR để tự động dọn dẹp các image cũ.
* Hiểu cách các quy tắc vòng đời hoạt động và tác động đến các image của bạn.

### Tổng quan về Chính sách Vòng đời trong ECR

Chính sách vòng đời bao gồm một hoặc nhiều quy tắc. Mỗi quy tắc xác định một hành động (thường là xóa) dựa trên các tiêu chí cụ thể. Các tiêu chí phổ biến bao gồm:

* **Age (Tuổi):** Xóa các image cũ hơn một số ngày nhất định.
* **Count (Số lượng):** Giữ lại N image gần nhất và xóa các image cũ hơn.
* **Tag Status (Trạng thái thẻ):** Xóa các image chưa được gắn thẻ (untagged) hoặc các image có thẻ cụ thể.

Các quy tắc được thực thi theo thứ tự ưu tiên (Rule Priority) mà bạn định nghĩa.

### Cấu hình Chính sách Vòng đời trong ECR

Bây giờ chúng ta sẽ tạo một chính sách vòng đời cho repository `fcj-workshop-app` của mình.

1.  **Đảm bảo bạn đang ở bảng điều khiển ECR:**
    * Trong thanh tìm kiếm của AWS Console, gõ "ECR" và chọn **Elastic Container Registry**.
   {{< figure src="/images/5/5.3/001.png" alt="Chọn repository fcj-workshop-app từ danh sách" >}}

2.  **Chọn Repository `fcj-workshop-app`:**
    * Trong danh sách các repository, nhấp vào tên repository `fcj-workshop-app` của bạn.

3.  **Chuyển đến tab "Lifecycle policy":**
    * Trong trang chi tiết của repository `fcj-workshop-app`, nhấp vào tab **Lifecycle policy**.
    {{< figure src="/images/5/5.3/002.png" alt="Chuyển đến tab Lifecycle policy" >}}

4.  **Nhấp vào nút "Create rule":**
    * Tại tab "Lifecycle policy", bạn sẽ thấy nút **Create rule**. Nhấp vào đó để bắt đầu định nghĩa các rule cho chính sách.
    {{< figure src="/images/5/5.3/003.png" alt="Nhấn nút Create rule" >}}

    {{% notice note %}}
Trước khi áp dụng policy thực tế, nên tạo rule và chạy thử (test rule) để xem trước ảnh nào sẽ bị xóa. Điều này giúp bạn tránh mất dữ liệu ngoài ý muốn.
    {{% /notice %}}
	
  

5.  **Cấu hình Quy tắc đầu tiên: Xóa các Image chưa được gắn thẻ (Untagged Images):**
    * Thêm một quy tắc mới với các cài đặt sau:
        * **Rule priority:** `1` (Ưu tiên cao nhất)
        * **Rule description:** `Delete untagged images` (Mô tả quy tắc)
        * **Image status:** `Untagged` (Trạng thái hình ảnh: chưa được gắn thẻ)
        * **Action:** `Expire images` (Hành động: xóa hình ảnh)
        *  **Since image pushed**: 1
    {{< figure src="/images/5/5.3/003-a.png" alt="Nhấn nút Create rule" >}}


7.  **Cấu hình Quy tắc 2: Giữ lại 3 Image gần nhất được gắn thẻ và xóa các Image cũ hơn:**
    * Nhấp "Create rule" một lần nữa và cấu hình quy tắc thứ hai với các cài đặt sau:
        * **Rule priority:** `2`
        * **Rule description:** `Keep latest 3 tagged images, delete others`
        * **Image status:** `Tagged` (Trạng thái hình ảnh: đã được gắn thẻ)
        * **Specify tags for wildcard matching:** **Nhập `*`** (Đây là **ký tự đại diện BẮT BUỘC** để quy tắc áp dụng cho TẤT CẢ các image có gắn thẻ khi sử dụng tiêu chí "Image count greater than". ECR yêu cầu bạn phải nhập ít nhất một mẫu thẻ.)
        * **Match criteria:** `Image count greater than`
        * **Value:** `3`
        * **Action:** `Expire images`
    {{< figure src="/images/5/5.3/003-b.png" alt="Nhấn nút Create rule" >}}

    {{% notice note %}}
Sau khi đã định nghĩa xong tất cả các quy tắc, bạn sẽ chạy thử chính sách để xem trước tác động, rồi mới tạo chính thức.
    {{% /notice %}}

8.  **Đẩy thêm Image lên Repository để kiểm tra chính sách:**
    * Để có đủ dữ liệu cho chính sách vòng đời hoạt động và để bạn có thể thấy kết quả mô phỏng rõ ràng, hãy lặp lại các bước đẩy image lên ECR repository (như đã thực hiện trong mục 4.3. [Chạy và test thử Pipeline](/4-ci-cd-with-github-actions/4.3-run-pipeline/).
    * Bạn nên đẩy thêm khoảng **5 image mới** với các tag khác nhau (ví dụ: `v0.0.2`, `v0.0.3`, `v0.0.4`, `v0.0.5`, `v0.0.6`). Đảm bảo mỗi lần đẩy là một phiên bản mới để tạo ra các image riêng biệt.

9.  **Chạy thử chính sách (Simulate policy) để xem trước tác động:**
    * Sau khi đã **định nghĩa tất cả các quy tắc** cho chính sách, bạn nên chạy thử để xem những image nào sẽ bị ảnh hưởng (xóa) trước khi áp dụng thực tế. Trên giao diện tạo chính sách.
    
    * Kết quả mô phỏng sẽ hiển thị danh sách các image sẽ bị xóa nếu chính sách này được thực thi. Điều này giúp bạn tránh việc vô tình xóa các image quan trọng.
    * Hãy vô lại repository ECR của bạn và Chọn Lifecycle Policy
    * Chọn Actions
    * Chọn copy policy to test rules
    {{< figure src="/images/5/5.3/006.png" alt="Mô phỏng chính sách vòng đời để xem trước tác động" >}}

10. **Chọn rule mà bạn muốn xem run test:**
    * Chọn run test
     {{< figure src="/images/5/5.3/007.png" alt="Xem lại và tạo chính sách vòng đời" >}}
   
11. **Kiểm tra run test chạy:**
    
    {{< figure src="/images/5/5.3/008.png" alt="Chính sách vòng đời đã được tạo thành công" >}}


Việc triển khai chính sách vòng đời giúp tự động hóa quá trình dọn dẹp, đảm bảo rằng repository ECR của bạn luôn được tối ưu và chỉ chứa các image cần thiết.

---