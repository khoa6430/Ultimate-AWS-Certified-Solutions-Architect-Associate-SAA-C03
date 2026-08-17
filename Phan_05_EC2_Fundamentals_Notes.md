# Ôn tập: Phần 5 - EC2 Fundamentals

Tài liệu này lưu lại các câu hỏi và giải đáp quan trọng trong quá trình học Phần 5 về EC2 Fundamentals để tiện ôn tập.

---

## 1. Thiết lập Ngân sách và Cảnh báo chi phí (AWS Budget Setup & Cost Management)
**Câu hỏi:** Làm thế nào để cấu hình quyền truy cập hóa đơn cho IAM user, theo dõi chi phí Free Tier và thiết lập cảnh báo ngân sách (AWS Budgets)?

**Trả lời:**

### A. Kích hoạt quyền xem hóa đơn (Billing) cho IAM User (Activating IAM Billing Access)
Mặc định, ngay cả khi một IAM User có quyền Administrator, họ vẫn bị lỗi "Access Denied" khi vào trang Billing. Để cho phép IAM User xem thông tin chi phí:
1.  Đăng nhập bằng tài khoản **Root**.
2.  Nhìn lên góc trên cùng bên phải, click vào tên tài khoản -> Chọn **Account** (Tài khoản).
3.  Cuộn xuống phần **IAM user and role access to billing information** (Quyền truy cập thông tin hóa đơn của IAM user và role).
4.  Nhấn chỉnh sửa và tích chọn **Activate IAM Access** (Kích hoạt truy cập IAM), sau đó lưu lại.

### B. Kiểm tra chi tiết hóa đơn (Bills) và Free Tier (Checking Bills and Free Tier Limits)
*   **Trang hóa đơn (Bills):** Giúp bạn rà soát chi phí theo tháng. Hãy chọn tháng cần xem và cuộn xuống mục **Charges by service** (Chi phí theo dịch vụ) để biết chính xác dịch vụ nào đang phát sinh chi phí (ví dụ: NAT Gateway, EBS, Elastic IP, v.v.).
*   **Trang Free Tier:** Giúp theo dõi mức độ sử dụng các dịch vụ miễn phí (như 750 giờ EC2/tháng). Nếu dự báo (forecast) vượt quá hạn mức và chuyển sang màu đỏ, bạn cần tắt các tài nguyên không dùng để tránh bị tính phí.

### C. Thiết lập Ngân sách (AWS Budgets) để cảnh báo qua Email (Creating AWS Budgets and Email Alerts)
Để tránh việc phát sinh hóa đơn lớn ngoài ý muốn, bạn nên tạo các ngân sách cảnh báo:
1.  Vào dịch vụ **AWS Budgets** (ở menu bên trái của Billing Console) -> Chọn **Create budget**.
2.  **Mẫu Ngân sách chi tiêu bằng 0 (Zero Spend Budget):**
    *   Hệ thống sẽ gửi email cảnh báo ngay khi tài khoản phát sinh **1 cent** ($0.01) đầu tiên.
    *   Điền tên ngân sách (ví dụ: `My Zero Spend Budget`) và nhập Email nhận cảnh báo -> Chọn **Create budget**.
3.  **Mẫu Ngân sách chi phí hàng tháng (Monthly Cost Budget):**
    *   Đặt hạn mức chi tiêu mong muốn (ví dụ: `$10` một tháng cho khóa học này).
    *   Cấu hình nhận cảnh báo ở các ngưỡng: khi chi phí thực tế đạt **85%**, **100%** hạn mức, hoặc khi chi phí dự báo (forecasted spend) có xu hướng chạm **100%**.

---

## 2. Dữ liệu người dùng EC2 (EC2 User Data - Bootstrapping)
**Câu hỏi:** EC2 User Data là gì và nó được sử dụng trong trường hợp nào?

**Trả lời:**
*   **Khái niệm Bootstrapping:** Là việc chạy các câu lệnh cấu hình tự động khi máy chủ ảo vừa được khởi chạy. Trong AWS, điều này được thực hiện thông qua tập lệnh viết ở phần **EC2 User Data**.
*   **Đặc điểm quan trọng:**
    *   Tập lệnh (script) này **chỉ chạy duy nhất một lần** vào lần khởi động đầu tiên (first start) của EC2 instance.
    *   Thường được dùng để tự động hóa các tác vụ thiết lập hệ thống ban đầu như: cài đặt bản cập nhật hệ điều hành (updates), cài đặt phần mềm/ứng dụng web, tải file cấu hình từ internet, v.v.
    *   Mặc định tập lệnh User Data sẽ được thực thi dưới quyền của tài khoản **root** (quyền quản trị cao nhất trên Linux).

---

## 3. Nhóm bảo mật (Security Groups) và các Cổng kết nối cơ bản (Security Groups & Common Network Ports)
**Câu hỏi:** Security Group là gì? Các quy tắc hoạt động của nó và danh sách các cổng (ports) mạng phổ biến cần nhớ?

**Trả lời:**

### A. Tổng quan về Security Groups (SG) (Security Groups Overview)
*   Security Group đóng vai trò như một **bức tường lửa ảo (firewall)** kiểm soát lưu lượng truy cập đi vào (Inbound) và đi ra (Outbound) của các EC2 Instance. Đây là nền tảng bảo mật mạng cốt lõi của AWS.
*   **Các quy tắc hoạt động cực kỳ quan trọng:**
    1.  **Chỉ có quy tắc cho phép (Allow rules):** Bạn chỉ có thể chỉ định cho phép lưu lượng nào được đi vào/ra. Không tồn tại quy tắc từ chối (Deny rules).
    2.  **Stateful (Có trạng thái):** Nếu một lưu lượng đi vào được cho phép (Inbound), thì lưu lượng phản hồi đi ra (Outbound) sẽ tự động được cho phép mà không cần cấu hình quy tắc đi ra tương ứng (và ngược lại).
    3.  **Tham chiếu lẫn nhau (Referencing other security groups):** Quy tắc SG không chỉ cho phép điền IP cụ thể, mà còn cho phép tham chiếu đến một Security Group khác. Điều này giúp các EC2 Instance thuộc các nhóm được tham chiếu dễ dàng kết nối với nhau mà không cần biết IP của nhau.
    4.  **Mặc định:** 
        *   Mọi lưu lượng đi vào (Inbound) từ bên ngoài đều bị chặn (chỉ cho phép các thực thể cùng SG giao tiếp nếu cấu hình).
        *   Mọi lưu lượng đi ra (Outbound) từ EC2 ra internet đều được cho phép.

### B. Các cổng mạng phổ biến (Classic Ports to know) (Common Network Ports to Know)
Khi cấu hình Security Group, bạn cần nhớ các cổng tiêu chuẩn sau:
*   **Cổng 22 = SSH (Secure Shell):** Dùng để đăng nhập dòng lệnh từ xa vào instance chạy Linux.
*   **Cổng 21 = FTP (File Transfer Protocol):** Giao thức truyền tải tệp tin thông thường.
*   **Cổng 22 = SFTP (Secure FTP):** Giao thức truyền tải tệp tin an toàn chạy trên nền tàng SSH.
*   **Cổng 80 = HTTP:** Truy cập các website thông thường (không mã hóa bảo mật).
*   **Cổng 443 = HTTPS:** Truy cập các website bảo mật (có chứng chỉ SSL/TLS mã hóa).
*   **Cổng 3389 = RDP (Remote Desktop Protocol):** Đăng nhập giao diện đồ họa từ xa vào máy chủ chạy Windows.

---

## 4. Các tùy chọn mua EC2 Instance (EC2 Instance Purchasing Options)
**Câu hỏi:** AWS cung cấp các tùy chọn mua EC2 Instance nào? Đặc điểm và trường hợp sử dụng tối ưu của từng loại?

**Trả lời:**

AWS cung cấp 7 hình thức mua và thanh toán EC2 để tối ưu hóa chi phí:

| Tùy chọn mua | Đặc điểm cốt lõi | Trường hợp sử dụng tốt nhất |
| :--- | :--- | :--- |
| **1. On-Demand** (Theo yêu cầu) | Trả tiền theo giây (Linux/Windows) hoặc theo giờ (OS khác). Giá cao nhất nhưng linh hoạt nhất. | Công việc ngắn hạn, đột xuất, chạy thử nghiệm không được phép gián đoạn. |
| **2. Reserved Instances** (Đặt trước) | Cam kết thuê 1 hoặc 3 năm. Giảm giá tới **72%**. Có thể thanh toán trước toàn bộ/một phần/không trả trước. | Công việc chạy liên tục ổn định dài hạn (ví dụ: máy chủ cơ sở dữ liệu). |
| **3. Savings Plans** (Gói tiết kiệm) | Cam kết chi tiêu cố định bằng tiền/giờ (ví dụ: $10/giờ) trong 1 hoặc 3 năm. Giảm giá tới **72%**. Linh hoạt đổi loại OS/kích thước instance. | Khách hàng muốn tiết kiệm chi phí giống Reserved Instances nhưng cần sự linh hoạt cao trong việc thay đổi cấu hình máy. |
| **4. Spot Instances** (Đấu thầu) | Sử dụng tài nguyên thừa của AWS với giá rẻ nhất (giảm giá tới **90%**). Tuy nhiên, **có thể bị AWS thu hồi (tắt máy) bất cứ lúc nào** kèm cảnh báo trước 2 phút. | Công việc có thể chịu lỗi tốt, thời gian chạy linh hoạt (Batch jobs, phân tích dữ liệu, xử lý ảnh). |
| **5. Dedicated Hosts** | Thuê nguyên một máy chủ vật lý vật lý dành riêng cho bạn. Cực kỳ đắt đỏ. | Đáp ứng các yêu cầu tuân thủ bảo mật khắt khe hoặc sử dụng giấy phép phần mềm riêng có sẵn (BYOL - Bring Your Own License). |
| **6. Dedicated Instances** | Instance chạy trên phần cứng vật lý chuyên dụng riêng, nhưng không kiểm soát vị trí cụ thể của máy chủ vật lý đó. | Cần cách ly phần cứng ở mức cơ bản nhưng không cần quản lý giấy phép phần mềm phức tạp ở cấp độ phần cứng. |
| **7. Capacity Reservations** | Đặt trước và giữ dung lượng trong một AZ cụ thể. Trả phí On-Demand bất kể máy có chạy hay không. | Đảm bảo 100% tài nguyên luôn sẵn sàng cho các tình huống khẩn cấp tại một AZ cố định. |

### 💡 Ví dụ ẩn dụ về Khách sạn (Hotel Analogy for EC2 Purchasing Options):
*   **On-Demand:** Đến thuê phòng bất kỳ lúc nào có nhu cầu, ở ngày nào trả nguyên giá ngày đó.
*   **Reserved:** Ký hợp đồng thuê phòng dài hạn (1-3 năm) để được chiết khấu giá phòng cực lớn.
*   **Savings Plans:** Cam kết trả cố định $10 mỗi đêm, được phép đổi từ phòng giường đơn sang giường đôi linh hoạt.
*   **Spot:** Đăng ký phòng trống đang giảm giá sập sàn, nhưng nếu có khách trả giá cao hơn thì bạn sẽ bị mời ra khỏi phòng sau 2 phút.
*   **Dedicated Hosts:** Thuê nguyên một căn biệt thự biệt lập của resort để tự ý phân chia phòng.
*   **Capacity Reservations:** Đặt chỗ và giữ phòng trước, dù bạn có đến ở hay bỏ trống phòng thì vẫn phải trả tiền.

---

## 5. Phân biệt Địa chỉ IP trong EC2 (Private vs Public vs Elastic IP)
**Câu hỏi:** Sự khác biệt giữa Public IP, Private IP và Elastic IP là gì?

**Trả lời:**

### A. So sánh Public IP và Private IP (IPv4 Comparison)
*   **Public IP (IP công cộng):**
    *   Dùng để định danh thiết bị trên mạng internet toàn cầu (WWW).
    *   Phải là địa chỉ độc nhất trên toàn thế giới (không thể có 2 thiết bị có cùng Public IP).
    *   Dễ dàng truy xuất ra vị trí địa lý của máy chủ.
*   **Private IP (IP nội bộ):**
    *   Dùng để liên lạc trong nội bộ mạng riêng (VPC).
    *   Phải là độc nhất trong mạng riêng nội bộ đó, nhưng **hai công ty khác nhau có thể sử dụng trùng dải Private IP**.
    *   Để truy cập ra internet, EC2 sử dụng Private IP kết nối thông qua thiết bị NAT (NAT Gateway) và Internet Gateway.

### B. Elastic IP (IP tĩnh công cộng - Static Public IP)
*   **Vấn đề:** Khi bạn thực hiện **Stop** (Dừng) và **Start** (Khởi động lại) một EC2 Instance, AWS sẽ tự động cấp một Public IP mới, tức là **Public IP cũ bị thay đổi**.
*   **Giải pháp:** **Elastic IP** là địa chỉ Public IPv4 cố định (tĩnh) do bạn sở hữu. Khi gán Elastic IP cho EC2, IP này sẽ giữ nguyên ngay cả khi bạn tắt/bật lại máy chủ.
*   **Lưu ý quan trọng khi thi & sử dụng:**
    *   AWS sẽ **tính phí** nếu bạn đăng ký Elastic IP nhưng để trống (không gán vào instance đang chạy) nhằm tránh lãng phí tài nguyên IP của họ.
    *   Mặc định chỉ được tạo tối đa 5 Elastic IP mỗi tài khoản.
    *   *Best Practice:* Tránh sử dụng trực tiếp Elastic IP cho các dịch vụ web công khai. Thay vào đó, hãy sử dụng **Load Balancer (Bộ cân bằng tải)** kết hợp dịch vụ DNS (Route 53) để hệ thống hoạt động linh hoạt và an toàn hơn.
