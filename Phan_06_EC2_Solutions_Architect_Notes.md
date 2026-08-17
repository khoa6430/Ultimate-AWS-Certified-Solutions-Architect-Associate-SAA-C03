# Ôn tập: Phần 6 - EC2 - Solutions Architect Associate Level

Tài liệu này lưu lại các câu hỏi và giải đáp quan trọng trong quá trình học Phần 6 để tiện ôn tập.

---

## 1. Phân biệt Private IP, Public IP và Elastic IP (Private vs Public vs Elastic IP)
**Câu hỏi:** IPv4 và IPv6 khác biệt như thế nào? Sự khác nhau giữa Public IP, Private IP và giải pháp Elastic IP là gì? Tại sao nên hạn chế dùng Elastic IP?

**Trả lời:**

### A. So sánh IPv4 và IPv6 (IPv4 vs IPv6 Comparison)
*   **IPv4 (Internet Protocol version 4):**
    *   Là định dạng phổ biến nhất hiện nay.
    *   Cấu trúc gồm **4 số phân tách bởi 3 dấu chấm** (ví dụ: `54.210.12.33`). Mỗi số chạy từ `0` đến `255`.
    *   Hỗ trợ tối đa **3.7 tỷ địa chỉ công cộng** (Public IP) và đang dần cạn kiệt.
*   **IPv6 (Internet Protocol version 6):**
    *   Được thiết kế để thay thế IPv4 nhằm giải quyết vấn đề cạn kiệt IP, thường dùng cho các thiết bị IoT (Internet of Things).
    *   Định dạng dài, bao gồm cả số và chữ cái hệ lục phân (ví dụ: `2001:db8:3333:4444:5555:6666:7777:8888`).
    *   AWS hỗ trợ đầy đủ IPv6, nhưng trong phạm vi cơ bản của khóa học này, chúng ta chủ yếu làm việc với IPv4.

### B. So sánh Public IP và Private IP (Public IP vs Private IP Comparison)
*   **Public IP (IP Công cộng):**
    *   Định danh máy chủ trên toàn bộ mạng Internet toàn cầu (WWW).
    *   Phải là **độc nhất** trên toàn thế giới (không thể có hai thiết bị trùng Public IP).
    *   Có thể truy vấn vị trí địa lý (geolocation) của IP đó một cách dễ dàng.
    *   Giúp các máy chủ kết nối trực tiếp với nhau qua môi trường Internet công cộng.
*   **Private IP (IP Nội bộ/Riêng tư):**
    *   Chỉ định danh và cho phép giao tiếp trong mạng nội bộ (private network / VPC).
    *   Chỉ cần **độc nhất trong nội bộ mạng đó**. Hai công ty khác nhau hoàn toàn có thể sử dụng cùng một dải Private IP trùng nhau mà không gặp lỗi.
    *   Để truy cập ra Internet bên ngoài, các máy có Private IP phải đi qua một thiết bị trung gian như **NAT Device** và **Internet Gateway** (đóng vai trò proxy).
    *   Chỉ các dải IP được quy định cụ thể (theo chuẩn RFC 1918) mới được dùng làm Private IP.

### C. Elastic IP (IP Tĩnh Công cộng) (Elastic IP - Static Public IP)
*   **Vấn đề:** Khi bạn **Stop** (Dừng) và **Start** (Khởi chạy lại) một EC2 Instance thông thường, **Public IP của nó sẽ bị thay đổi**.
*   **Giải pháp:** **Elastic IP** là địa chỉ Public IPv4 tĩnh do bạn sở hữu lâu dài (cho đến khi chủ động xóa). Khi gán Elastic IP cho EC2, IP này sẽ được giữ nguyên không đổi ngay cả khi tắt/bật lại máy chủ.
*   **Đặc điểm và Hạn chế:**
    *   Mỗi thời điểm, một Elastic IP chỉ có thể liên kết với **duy nhất một EC2 instance**.
    *   Có thể dùng để khắc phục nhanh sự cố (mask failures) bằng cách chuyển nhanh Elastic IP từ instance lỗi sang instance dự phòng.
    *   Mặc định, AWS chỉ cho phép tối đa **5 Elastic IP** trên mỗi tài khoản (có thể yêu cầu tăng giới hạn).
    *   **Lưu ý quan trọng:** Elastic IP thường được coi là một **thiết kế kiến trúc kém (poor architectural decision)**. Bạn nên hạn chế tối đa việc sử dụng chúng.
    *   **Giải pháp thay thế (Best Practices):**
        1. Sử dụng Public IP ngẫu nhiên và gắn nó với một **tên miền DNS (Route 53)** để tự động cập nhật khi IP đổi.
        2. Sử dụng **Load Balancer (Bộ cân bằng tải)** ở phía trước và cấu hình các EC2 instance trong mạng nội bộ hoàn toàn (không cần Public IP). Đây là mô hình an toàn và tối ưu nhất trong AWS.

### D. Quan sát trong thực hành (Hands-on IP Observations)
*   Mặc định, máy chủ EC2 được tạo sẽ có 1 Private IP (giao tiếp nội bộ AWS) và 1 Public IP (truy cập từ ngoài).
*   Khi sử dụng SSH để kết nối vào EC2 từ máy tính cá nhân ở nhà, bạn **bắt buộc phải sử dụng Public IP** (trừ khi máy tính của bạn đã kết nối VPN với mạng AWS).
*   Nếu Stop và Start instance, Public IP sẽ thay đổi, nhưng Private IP vẫn được giữ nguyên.

---

## 2. Hướng dẫn thực hành: Quan sát hành vi IP & Cấu hình Elastic IP (Hands-on: Observing IP Behaviors & Configuring Elastic IP)
**Câu hỏi:** Các bước thực hành quan sát sự thay đổi IP trên EC2 được thực hiện thế nào? Quy trình đăng ký, gán, và dọn dẹp Elastic IP để tránh bị tính phí ngoài ý muốn là gì?

**Trả lời:**

### A. Quy trình thực hành kiểm tra hành vi IP (Hands-on Process for Observing IP Behaviors)
1.  **Kết nối SSH bằng Public và Private IP:**
    *   Sử dụng lệnh SSH thông thường với **Public IP** -> Kết nối thành công. Sau khi vào hệ thống, hostname của máy chủ sẽ hiển thị dưới dạng Private IP (ví dụ: `ip-172-31-x-x`).
    *   Ngắt kết nối, thử SSH lại bằng **Private IP** -> Kết nối thất bại (timeout).
    *   *Giải thích:* Bạn đang truy cập từ internet (mạng công cộng), không thuộc mạng nội bộ (VPC) của AWS nên không thể định tuyến tới Private IP.
2.  **Hành vi thay đổi IP khi Stop & Start:**
    *   Thực hiện dừng máy chủ (**Stop**) và khởi động lại (**Start**).
    *   > [!NOTE]
    *   > Cần phân biệt giữa **Reboot** (Khởi động lại hệ điều hành - giữ nguyên Public IP) và **Stop & Start** (Tắt và bật lại máy chủ vật lý bên dưới - làm thay đổi Public IP).
    *   Kiểm tra bảng điều khiển EC2 -> Máy chủ đã được cấp một **Public IP mới hoàn toàn**. Lệnh SSH cũ sử dụng IP trước đó sẽ không thể kết nối được nữa, bạn phải cập nhật IP mới để SSH.
    *   Địa chỉ **Private IP** vẫn giữ nguyên không đổi.

### B. Cấu hình Elastic IP (IP Tĩnh Công cộng) (Configuring Elastic IP - Static Public IP)
Để giữ nguyên Public IP ngay cả khi Stop và Start máy chủ:
1.  Ở menu bên trái EC2 Console, tìm mục **Network & Security** -> Chọn **Elastic IPs**.
2.  Nhấn **Allocate Elastic IP address** -> Giữ cấu hình mặc định (xin IP từ pool của Amazon) -> Chọn **Allocate**. Lúc này bạn đã sở hữu một IP tĩnh công cộng.
3.  Click vào Elastic IP vừa tạo -> Chọn **Actions** -> Chọn **Associate Elastic IP address**.
4.  Tại trang liên kết:
    *   **Resource type:** Chọn `Instance`.
    *   **Instance:** Chọn instance EC2 đang chạy của bạn.
    *   **Private IP address:** Chọn địa chỉ Private IP tương ứng của instance đó.
    *   Nhấn **Associate**.
5.  Quay lại danh sách EC2 Instance, bạn sẽ thấy Public IPv4 đã chuyển thành địa chỉ Elastic IP. Lúc này, thử **Stop & Start** máy chủ -> Public IP vẫn giữ nguyên.

### C. ⚠️ Cảnh báo cực kỳ quan trọng về Chi phí (Billing & Pricing Warnings)
AWS có chính sách tính phí đối với địa chỉ IPv4 công cộng như sau:
*   Mọi địa chỉ Public IPv4 (dù là IP thông thường hay Elastic IP, dù đang gắn vào instance chạy hay không chạy) đều bị tính phí **$0.005/giờ** (khoảng **$3.50/tháng**).
*   **Chính sách Free Tier:** Tài khoản mới được miễn phí **750 giờ Public IPv4 mỗi tháng** (đủ chạy liên tục 1 instance trong tháng). Nếu bạn chạy từ 2 instances trở lên, bạn sẽ nhanh chóng vượt quá số giờ miễn phí này và bị tính tiền.
*   **Elastic IP chưa gán (Unassociated):** Nếu bạn đăng ký Elastic IP nhưng không gắn vào instance đang hoạt động (ví dụ: instance bị Stop hoặc Terminate nhưng bạn quên chưa xóa Elastic IP), AWS sẽ tính phí để tránh việc giữ chỗ gây lãng phí tài nguyên IP.

### D. Quy trình dọn dẹp (Cleanup) sau khi thực hành xong (Cleanup Procedure to Avoid Charges)
Để tránh bị phát sinh chi phí phát sinh ngoài ý muốn, hãy luôn thực hiện dọn dẹp theo thứ tự sau:
1.  **Gỡ Elastic IP khỏi Instance (Disassociate):**
    *   Vào trang **Elastic IPs** -> Tích chọn IP cần gỡ.
    *   Chọn **Actions** -> Chọn **Disassociate Elastic IP address** -> Xác nhận.
2.  **Giải phóng Elastic IP (Release):**
    *   *Lưu ý:* Việc chỉ gỡ (disassociate) chưa đủ, bạn vẫn sẽ bị tính phí nếu giữ IP đó.
    *   Chọn tiếp **Actions** -> Chọn **Release Elastic IP address** -> Xác nhận giải phóng để trả IP về kho của AWS.
3.  **Hủy máy chủ EC2 (Terminate):**
    *   Vào trang **Instances** -> Tích chọn instance thực hành -> Chọn **Instance state** -> Chọn **Terminate instance** để xóa hoàn toàn máy chủ.

---

## 3. Nhóm định vị EC2 (EC2 Placement Groups)
**Câu hỏi:** EC2 Placement Group là gì? Có những chiến lược định vị nào và trường hợp sử dụng tối ưu của từng loại trong thiết kế hệ thống AWS?

**Trả lời:**

### A. Tổng quan về Placement Groups (Placement Groups Overview)
*   **Khái niệm:** Khi bạn tạo nhiều EC2 instance, mặc định AWS sẽ tự động phân phối chúng trên phần cứng vật lý của họ. Tuy nhiên, nếu bạn muốn kiểm soát cách các instance này được sắp xếp so với nhau (để tối ưu hóa hiệu năng mạng hoặc giảm thiểu rủi ro lỗi phần cứng), bạn có thể sử dụng **Placement Groups**.
*   **Cách thức:** Bạn định nghĩa một chiến lược (strategy) và chỉ định nhóm đó khi khởi chạy EC2. Bạn không can thiệp trực tiếp vào phần cứng của AWS, nhưng AWS sẽ sắp xếp các máy chủ theo yêu cầu của bạn.

### B. Ba chiến lược định vị (Three Placement Strategies)

#### 1. Cluster Placement Group (Nhóm định vị cụm - Tập trung)
*   **Cơ chế hoạt động:** Nhóm tất cả các EC2 instance lại gần nhau trên phần cứng trong cùng một **Phân vùng sẵn sàng (Single Availability Zone - AZ)**.
*   **Ưu điểm:** Tối ưu hóa hiệu năng mạng vượt trội. Băng thông cực cao (lên tới **10 Gbps** hoặc cao hơn khi bật *Enhanced Networking*) với độ trễ (latency) cực thấp và thông lượng (throughput) rất lớn giữa các instance.
*   **Nhược điểm:** Rủi ro cao (*High Risk*). Nếu phần cứng vật lý bên dưới hoặc toàn bộ Availability Zone (AZ) gặp sự cố, tất cả các instance trong nhóm sẽ bị lỗi đồng thời.
*   **Trường hợp sử dụng (Use Cases):**
    *   Các tác vụ tính toán hiệu năng cao (HPC - High Performance Computing).
    *   Ứng dụng xử lý Dữ liệu lớn (Big Data) cần hoàn thành nhanh chóng và yêu cầu trao đổi dữ liệu tốc độ cực cao giữa các node.
    *   Ứng dụng cần độ trễ mạng gần như bằng 0.

#### 2. Spread Placement Group (Nhóm định vị phân tán - Cô lập lỗi)
*   **Cơ chế hoạt động:** Phân tán mỗi EC2 instance nằm trên các **phần cứng vật lý (racks) hoàn toàn khác nhau** (mỗi instance một rack riêng biệt). Có thể trải rộng trên nhiều AZ trong cùng một Region.
*   **Ưu điểm:** Giảm thiểu tối đa rủi ro xảy ra lỗi đồng thời. Nếu một rack phần cứng bị hỏng, chỉ duy nhất instance trên rack đó bị ảnh hưởng, các instance khác trong nhóm vẫn hoạt động bình thường.
*   **Nhược điểm:** Giới hạn quy mô lớn. Bạn bị giới hạn tối đa **7 EC2 instances trên mỗi AZ** trong mỗi Placement Group (Ví dụ: Region có 3 AZ thì tối đa tạo được 21 instances trong nhóm spread).
*   **Trường hợp sử dụng (Use Cases):**
    *   Các ứng dụng quan trọng (Critical applications) cần cô lập lỗi tuyệt đối giữa các máy chủ.
    *   Các máy chủ cơ sở dữ liệu chính/phụ (Primary/Secondary Databases) hoặc các dịch vụ lõi của doanh nghiệp.

#### 3. Partition Placement Group (Nhóm định vị phân vùng - Quy mô lớn)
*   **Cơ chế hoạt động:** Chia nhóm thành các phân vùng (Partitions), mỗi phân vùng đại diện cho một cụm các rack phần cứng vật lý độc lập trong AZ. Các instance trong phân vùng này sẽ không chia sẻ chung rack phần cứng với các instance ở phân vùng khác.
*   **Ưu điểm:** 
    *   Cô lập lỗi ở cấp độ phân vùng (nếu Partition 1 lỗi thì các Partition khác vẫn hoạt động tốt).
    *   Khả năng mở rộng quy mô lớn hơn nhiều so với Spread Group, cho phép chạy **hàng trăm EC2 instances** trong cùng một nhóm (vì một partition có thể chứa nhiều instance).
    *   Hỗ trợ tối đa **7 Partitions trên mỗi AZ**. Có thể trải rộng trên nhiều AZ trong cùng một Region.
*   **Truy vấn thông tin:** Các EC2 instance có thể sử dụng dịch vụ Metadata (Instance Metadata Service) để biết mình đang nằm ở partition nào.
*   **Trường hợp sử dụng (Use Cases):**
    *   Phù hợp cho các ứng dụng Dữ liệu lớn có cơ chế tự nhận biết phân vùng (partition-aware) để phân phối dữ liệu.
    *   Các hệ thống như **Hadoop (HDFS)**, **HBase**, **Apache Cassandra**, và **Apache Kafka**.

### C. Bảng so sánh nhanh (Quick Comparison Table)

| Tiêu chí so sánh | Cluster | Spread | Partition |
| :--- | :--- | :--- | :--- |
| **Vị trí AZ** | Chỉ nằm trong 1 AZ duy nhất | Trải rộng trên nhiều AZ | Trải rộng trên nhiều AZ |
| **Băng thông & Độ trễ** | Rất cao, độ trễ cực thấp | Bình thường | Bình thường |
| **Mức độ cách ly lỗi** | Thấp (AZ sập = tất cả sập) | Cao nhất (Mỗi máy 1 rack vật lý) | Khá (Mỗi nhóm máy 1 cụm rack) |
| **Giới hạn số lượng máy** | Không giới hạn cứng | Tối đa **7 instances / AZ** | Tối đa **7 partitions / AZ** (hàng trăm máy) |
| **Dịch vụ tiêu biểu** | HPC, Big Data cần mạng nhanh | DB Primary/Secondary, Critical App | Hadoop, Cassandra, Kafka |

---

## 4. Hướng dẫn thực hành: Tạo và sử dụng Placement Groups (Hands-on: Creating & Using Placement Groups)
**Câu hỏi:** Quy trình tạo các Placement Groups với 3 chiến lược khác nhau trên giao diện điều khiển AWS Console thế nào? Làm sao để gán một EC2 instance mới vào nhóm định vị đã tạo?

**Trả lời:**

### A. Quy trình tạo các Placement Groups trên AWS Console
1.  **Truy cập Menu Placement Groups:**
    *   Truy cập **EC2 Console**.
    *   Ở thanh menu bên trái, cuộn xuống mục **Network & Security** -> Chọn **Placement groups**.
2.  **Tạo nhóm định vị Cụm (Cluster Placement Group):**
    *   Chọn **Create placement group**.
    *   **Name:** Nhập `my-high-performance-group`.
    *   **Placement strategy:** Chọn `Cluster`.
    *   Chọn **Create group**.
3.  **Tạo nhóm định vị Phân tán (Spread Placement Group):**
    *   Chọn **Create placement group**.
    *   **Name:** Nhập `my-critical-group`.
    *   **Placement strategy:** Chọn `Spread`.
    *   **Spread level:** Giữ nguyên mặc định là `Rack` (để phân tán máy chủ trên các rack phần cứng vật lý khác nhau).
    *   Chọn **Create group**.
4.  **Tạo nhóm định vị Phân vùng (Partition Placement Group):**
    *   Chọn **Create placement group**.
    *   **Name:** Nhập `my-distributed-group`.
    *   **Placement strategy:** Chọn `Partition`.
    *   **Number of partitions:** Nhập số lượng phân vùng mong muốn, ví dụ `4` (giới hạn từ 1 đến 7).
    *   Chọn **Create group**.

Sau khi tạo xong, bạn sẽ thấy danh sách 3 Placement Groups có trạng thái `available` sẵn sàng để sử dụng giống như trong hình ảnh thực hành.

### B. Cách gán EC2 Instance mới vào Placement Group
Khi tạo một máy chủ ảo EC2 mới, bạn có thể chỉ định nó thuộc vào nhóm định vị đã tạo:
1.  Nhấp vào **Launch instances** để tạo mới một máy chủ.
2.  Điền các thông tin cơ bản (Name, OS, Instance type, Key pair, Security Group, v.v.).
3.  Cuộn xuống cuối trang cấu hình -> Mở rộng mục **Advanced details** (Chi tiết nâng cao).
4.  Tìm đến mục **Placement group name** (Tên nhóm định vị).
5.  Chọn đúng tên nhóm mong muốn trong số các nhóm đã tạo:
    *   `my-critical-group` (nếu cần bảo mật tối đa và cô lập lỗi).
    *   `my-distributed-group` (nếu chạy ứng dụng Big Data phân tán như Kafka/Cassandra).
    *   `my-high-performance-group` (nếu chạy ứng dụng HPC cần băng thông mạng cực cao).
6.  Hoàn tất cấu hình và khởi chạy instance. Máy chủ mới tạo sẽ tự động tuân thủ theo quy tắc định vị của nhóm đó.

---

## 5. Giao diện mạng đàn hồi EC2 (Elastic Network Interfaces - ENI)
**Câu hỏi:** ENI (Elastic Network Interface) là gì? Các thuộc tính chính của nó và cách nó được sử dụng để thiết kế cơ chế dự phòng sự cố (failover) trong AWS như thế nào?

**Trả lời:**

### A. Khái niệm về ENI (What is an Elastic Network Interface?)
*   **Định nghĩa:** **ENI** là một thành phần logic trong đám mây riêng ảo (VPC), đại diện cho một **card mạng ảo (virtual network card)**.
*   **Vai trò:** ENI là cầu nối giúp máy chủ EC2 kết nối và truyền thông tin với mạng nội bộ hoặc internet. Ngoài EC2, ENI còn được sử dụng rộng rãi bởi nhiều dịch vụ AWS khác (như Load Balancers, Lambda, RDS, v.v.).
*   **Mặc định:** Khi khởi tạo một EC2 instance, AWS sẽ tự động tạo và gắn một ENI mặc định (gọi là card mạng chính - **eth0**) vào máy chủ đó.

### B. Các thuộc tính chính của ENI (Key Attributes of an ENI)
Mỗi ENI được tạo ra có thể bao gồm các thông tin cấu hình mạng sau:
1.  **Một địa chỉ IPv4 nội bộ chính (Primary private IPv4):** Đây là IP nội bộ cố định đầu tiên của card mạng.
2.  **Một hoặc nhiều địa chỉ IPv4 nội bộ phụ (Secondary private IPv4s):** Bạn có thể gán thêm các IP phụ vào cùng một card mạng để chạy nhiều ứng dụng/dịch vụ trên các IP khác nhau.
3.  **Địa chỉ IP công cộng tĩnh (Elastic IP - IPv4):** Mỗi IP nội bộ (cả chính và phụ) trên ENI đều có thể liên kết với một Elastic IP (IP tĩnh công cộng).
4.  **Một hoặc nhiều địa chỉ Public IPv4:** IP công cộng ngẫu nhiên của AWS.
5.  **Một hoặc nhiều Nhóm bảo mật (Security Groups):** Dùng để lọc tường lửa cho riêng lưu lượng đi qua card mạng đó.
6.  **Một địa chỉ vật lý MAC (MAC address).**

### C. Đặc điểm hoạt động và Ứng dụng dự phòng (ENI Behaviors & Failover Use Cases)
*   **Ràng buộc AZ (Availability Zone Bound):** ENI được tạo ra trong một AZ cụ thể. Bạn **không thể** gắn một ENI vào máy chủ EC2 nằm ở AZ khác. Chúng phải cùng nằm trong một AZ.
*   **Khả năng tháo lắp linh hoạt:** Bạn có thể tạo các ENI độc lập với EC2 instance, sau đó gắn nóng (attach on-the-fly) vào EC2 đang chạy hoặc gỡ ra (detach) khi cần thiết.
*   **Ứng dụng dự phòng sự cố (Failover):**
    *   *Kịch bản:* Bạn có một ứng dụng khách (client) truy cập vào máy chủ EC2 (đang chạy cơ sở dữ liệu) thông qua một Private IP cố định.
    *   *Sự cố:* Máy chủ EC2 chính bị lỗi đột ngột.
    *   *Giải quyết:* Bạn chỉ cần **gỡ (detach)** card mạng phụ (ví dụ: `eth1` chứa Private IP đó) từ máy chủ lỗi, và nhanh chóng **gắn (attach)** sang một máy chủ dự phòng (Standby EC2) trong cùng AZ.
    *   *Kết quả:* Lưu lượng mạng sẽ tự động định tuyến sang máy chủ mới mà không cần cập nhật cấu hình IP hay tên miền phía client. Đây là cách cấu hình failover cực kỳ nhanh và hiệu quả.

---

## 6. Hướng dẫn thực hành: Tạo và cấu hình tháo lắp ENI (Hands-on: Creating & Configuring ENI)
**Câu hỏi:** Các bước thực hành tạo, liên kết (Attach), tháo rời (Detach), và chuyển đổi card mạng ENI giữa hai máy chủ EC2 được thực hiện như thế nào? Hành vi của ENI khi hủy máy chủ (Terminate) có gì khác biệt giữa card mạng mặc định và card mạng tự tạo?

**Trả lời:**

### A. Chuẩn bị môi trường thực hành
1.  Khởi chạy **02 instance EC2** mới chạy Amazon Linux 2 (loại `t2.micro`).
2.  Tại phần Network settings, sử dụng chung một Security Group có sẵn (ví dụ: `launch-wizard-1`).
3.  *Lưu ý:* Ghi nhớ phân vùng Availability Zone (AZ) của 2 instance này (ví dụ: cả hai cùng ở `us-east-2a`).

### B. Tạo mới một card mạng ENI thủ công
1.  Tại menu bên trái EC2 Console, tìm mục **Network & Security** -> Chọn **Network interfaces**.
2.  Tại đây bạn sẽ thấy 2 ENI mặc định đang ở trạng thái `in-use` (gắn tương ứng với 2 instance vừa tạo).
3.  Chọn **Create network interface** ở góc phải để tạo card mạng thứ 3:
    *   **Description:** Nhập `demo-eni`.
    *   **Subnet:** **Bắt buộc** phải chọn Subnet thuộc cùng AZ với 2 máy chủ EC2 ở trên (ví dụ: `us-east-2a`). Nếu chọn khác AZ, bạn sẽ không thể gắn card mạng này vào máy chủ.
    *   **Private IPv4 address:** Chọn `Auto-assign` (tự động cấp IP) hoặc có thể chọn *Custom* để tự điền IP trong dải subnet.
    *   **Security groups:** Chọn Security Group tương tự như của máy chủ.
    *   Nhấn **Create network interface**.
4.  Card mạng mới được tạo sẽ hiển thị trong danh sách với trạng thái **available** (chưa gắn vào máy nào).

### C. Gắn ENI vào Instance và Thực hành dự phòng (Failover)
1.  **Gắn ENI vào Instance thứ nhất (Attach):**
    *   Tích chọn `demo-eni` -> Chọn **Actions** -> Chọn **Attach**.
    *   Chọn tên máy chủ EC2 thứ nhất -> Chọn **Attach**.
    *   Vào trang chi tiết của Instance thứ nhất -> Tab **Networking** -> Bạn sẽ thấy máy chủ này hiện có **2 card mạng**: `eth0` (mặc định ban đầu, chứa Public và Private IP chính) và `eth1` (chính là `demo-eni` vừa gắn, cấp thêm một IP Private phụ).
2.  **Tháo ENI để chuyển sang Instance thứ hai (Detach & Re-attach):**
    *   Vào lại mục **Network interfaces** -> Tích chọn `demo-eni`.
    *   Chọn **Actions** -> Chọn **Detach** -> Xác nhận (nếu quá trình gỡ bị treo, bạn có thể chọn **Force detach** để cưỡng chế tháo rời).
    *   Sau vài giây, trạng thái của ENI chuyển lại về `available`.
    *   Tiếp tục chọn **Actions** -> Chọn **Attach** -> Chọn tên máy chủ **EC2 thứ hai** -> Chọn **Attach**.
    *   Lúc này, IP Private phụ của `demo-eni` đã được chuyển hoàn toàn từ máy chủ thứ nhất sang máy chủ thứ hai. Đây chính là cách mô phỏng cơ chế **Network Failover** trong thực tế.

### D. Hành vi của ENI khi hủy máy chủ (Termination Behavior)
Khi bạn thực hiện **Terminate** (Hủy hoàn toàn) hai máy chủ EC2 ở trên, hãy quan sát sự thay đổi trong trang Network interfaces:
*   **ENI mặc định (Primary ENI - eth0):** Được tạo tự động cùng với máy chủ nên sẽ **tự động bị xóa bỏ vĩnh viễn** cùng với máy chủ khi máy bị terminate.
*   **ENI tự tạo thủ công (Secondary ENI):** Do bạn tự tạo độc lập, nên khi máy chủ chứa nó bị hủy, ENI này **vẫn sẽ được giữ lại** trong tài khoản của bạn và chuyển về trạng thái `available`.
*   *Dọn dẹp:* Để tránh rác tài nguyên, bạn hãy tích chọn ENI tự tạo này -> Chọn **Actions** -> Chọn **Delete** để xóa hẳn. Việc giữ ENI ở trạng thái `available` không bị AWS tính phí, nhưng dọn dẹp sạch sẽ luôn là thói quen tốt.

---

## 7. Chế độ ngủ đông EC2 (EC2 Hibernate)
**Câu hỏi:** Chế độ ngủ đông (Hibernate) trên EC2 là gì và nó hoạt động như thế nào dưới nền tảng AWS? Các yêu cầu kỹ thuật và hạn chế quan trọng cần biết khi sử dụng tính năng này là gì?

**Trả lời:**

### A. Khái niệm và Cơ chế hoạt động (How EC2 Hibernate Works Under the Hood)
*   **Vấn đề của Stop thông thường:** Khi bạn **Stop** máy chủ EC2, dữ liệu trên ổ đĩa cứng (EBS volume) được giữ lại, nhưng dữ liệu trên RAM sẽ bị xóa sạch. Khi **Start** lại, máy chủ sẽ phải khởi động hệ điều hành (OS) từ đầu, nạp lại ứng dụng, làm ấm bộ nhớ đệm (warm caches), v.v. Việc này có thể mất nhiều phút.
*   **Giải pháp với Hibernate:** Khi bạn **Hibernate** máy chủ, trạng thái hiện tại của bộ nhớ **RAM (In-memory state) sẽ được bảo toàn**. Quá trình khởi động sau đó sẽ diễn ra cực kỳ nhanh vì hệ điều hành không bị tắt/khởi động lại mà chỉ đơn giản là được "rã đông" (unfrozen) và tiếp tục chạy.
*   **Cơ chế hoạt động dưới nền tảng (Under the hood):**
    1.  Khi lệnh Hibernate được kích hoạt, máy chủ chuyển sang trạng thái `Stopping`.
    2.  AWS sẽ tiến hành ghi (dump) toàn bộ dữ liệu đang có trên **RAM vào một tệp tin nằm trong ổ đĩa Root EBS Volume**.
    3.  Sau khi ghi xong, máy chủ chuyển sang trạng thái `Stopped`, RAM vật lý trên máy chủ bị xóa sạch, nhưng bản sao lưu của RAM vẫn nằm an toàn trên ổ đĩa EBS.
    4.  Khi bạn chọn **Start** máy chủ trở lại, dữ liệu RAM từ ổ đĩa EBS sẽ được nạp ngược lại vào RAM vật lý. Máy chủ lập tức trở lại trạng thái hoạt động bình thường giống như chưa từng bị tắt.

### B. Các điều kiện và Hạn chế (Good to Know: Requirements & Limits)
Để sử dụng được tính năng EC2 Hibernate, hệ thống cần đáp ứng các điều kiện sau:
*   **Yêu cầu về ổ đĩa Root Volume:**
    *   Phải là ổ đĩa **EBS Volume** (không hỗ trợ ổ đĩa cục bộ Instance Store).
    *   **Bắt buộc phải mã hóa (Encrypted):** Do dữ liệu trong RAM có thể chứa các thông tin bảo mật/nhạy cảm, AWS bắt buộc ổ đĩa Root phải được mã hóa để bảo vệ tệp tin dump RAM này.
    *   Phải có **đủ dung lượng trống**: Ổ đĩa Root phải đủ lớn để chứa cả hệ điều hành lẫn dung lượng RAM của máy chủ (ví dụ: RAM 8GB thì ổ đĩa cần trống tối thiểu 8GB tương ứng).
*   **Hạn chế về cấu hình phần cứng:**
    *   Hỗ trợ nhiều dòng instance phổ biến như C3, C4, C5, I3, M3, M4, R3, R4, T2, T3...
    *   Dung lượng RAM của máy chủ phải **dưới 150 GB** (hạn mức này có thể thay đổi theo thời gian).
    *   **Không** hỗ trợ cho các máy chủ vật lý chuyên dụng (bare metal instances).
*   **Hệ điều hành và hình thức thanh toán:**
    *   Hỗ trợ hầu hết các OS phổ biến bao gồm Amazon Linux 2, Linux AMI, Ubuntu, RHEL, CentOS, và Windows.
    *   Áp dụng cho mọi hình thức thanh toán EC2 bao gồm: On-Demand, Reserved, và Spot Instances.
*   **Thời gian ngủ đông tối đa:** Một instance không được phép ngủ đông quá **60 ngày** liên tục (quá thời hạn này máy chủ có thể bị ảnh hưởng hoặc thay đổi trạng thái).

### C. Trường hợp sử dụng (Use Cases)
*   **Ứng dụng có dịch vụ khởi tạo lâu:** Dành cho các ứng dụng hoặc dịch vụ mất rất nhiều thời gian để khởi động hệ thống, tải thư viện hoặc làm ấm bộ nhớ đệm (caches). Ngủ đông giúp bỏ qua bước khởi tạo phức tạp này.
*   **Tiết kiệm chi phí nhưng giữ trạng thái ứng dụng:** Phục vụ cho các tiến trình chạy dài (long-running processes) cần tạm dừng vào ban đêm hoặc ngày nghỉ để tiết kiệm chi phí nhưng khi bật lại phải giữ nguyên các tác vụ đang làm dở trên RAM.

---

## 8. Hướng dẫn thực hành: Bật và thử nghiệm chế độ ngủ đông (Hands-on: Enabling & Testing EC2 Hibernate)
**Câu hỏi:** Các bước thực hành cấu hình bật chế độ Hibernate khi khởi chạy EC2 instance mới được thực hiện như thế nào? Làm sao để chứng minh chế độ Hibernate hoạt động thành công bằng dòng lệnh?

**Trả lời:**

### A. Cấu hình khởi chạy máy chủ EC2 bật tính năng Hibernate
1.  Nhấp vào **Launch instances** để tạo mới một máy chủ:
    *   **OS:** Chọn Amazon Linux 2.
    *   **Instance type:** Chọn `t2.micro` (loại này chỉ có **1 GB RAM**).
    *   **Key pair:** Chọn key pair bất kỳ có sẵn của bạn.
    *   **Network settings:** Chọn Security Group có sẵn (ví dụ: `launch-wizard-1`).
2.  **Cấu hình ổ đĩa Root EBS bắt buộc (Bắt buộc phải mã hóa ổ đĩa):**
    *   Tại phần cấu hình **Configure storage**, mở rộng cấu hình nâng cao (Advanced).
    *   Tích chọn mã hóa (**Encrypted**) ổ đĩa Root.
    *   Tại mục KMS key, chọn khóa mặc định của AWS (`aws/ebs`).
    *   *Kiểm tra dung lượng:* Ổ đĩa mặc định là 8 GB, lớn hơn nhiều so với dung lượng RAM 1 GB của máy chủ `t2.micro`, nên hoàn toàn đủ điều kiện.
3.  **Bật hành vi ngủ đông (Stop - Hibernate behavior):**
    *   Cuộn xuống cuối trang cấu hình -> Mở rộng mục **Advanced details** (Chi tiết nâng cao).
    *   Tìm mục **Stop - Hibernate behavior** -> Chọn **Enable** (Bật).
4.  Nhấn **Launch instance** để khởi chạy máy chủ.

### B. Kiểm tra và chứng minh cơ chế Hibernate bằng dòng lệnh
Để kiểm tra xem hệ điều hành có thực sự ngủ đông hay chỉ tắt máy thông thường, ta sử dụng lệnh kiểm tra thời gian hoạt động hệ thống (`uptime`):
1.  **Kết nối ban đầu và theo dõi uptime:**
    *   Chọn instance vừa khởi chạy -> Chọn **Connect** -> Kết nối bằng **EC2 Instance Connect**.
    *   Sau khi vào màn hình Terminal, gõ lệnh:
        ```bash
        uptime
        ```
    *   Hệ thống sẽ hiển thị thời gian máy chạy (ví dụ ban đầu: `up 0 minutes`).
    *   Chờ khoảng 1-2 phút rồi gõ lại lệnh `uptime` -> Hệ thống hiển thị `up 1 minute`.
    *   Đóng tab kết nối Terminal.
2.  **Kích hoạt chế độ ngủ đông trên AWS Console:**
    *   Tại trang EC2 Instances, tích chọn máy chủ của bạn.
    *   Chọn **Instance state** -> Chọn **Hibernate instance** (Hành động này sẽ dump RAM vào ổ đĩa và tắt máy).
    *   Đợi vài phút cho trạng thái máy chuyển sang màu đỏ: **Stopped** (bên cạnh ghi rõ lý do dừng là hibernated).
3.  **Khởi động lại và kiểm tra kết quả:**
    *   Chọn máy chủ -> Chọn **Instance state** -> Chọn **Start instance**.
    *   Sau khi máy chạy lại, nhấn tiếp **Connect** -> Chọn **EC2 Instance Connect** để vào lại Terminal.
    *   Gõ ngay lệnh:
        ```bash
        uptime
        ```
    *   **Kết quả:** Thời gian hoạt động sẽ hiển thị là `up 2 minutes` (hoặc 3-4 phút tùy thuộc thời gian bạn thao tác nhanh hay chậm). 
    *   *Ý nghĩa:* Thời gian hoạt động của hệ điều hành **không bị reset về 0**, chứng minh rằng hệ điều hành không hề bị khởi động lại từ đầu mà chỉ bị đóng băng và khôi phục nguyên vẹn trạng thái.

### C. Dọn dẹp môi trường (Cleanup)
Sau khi thực hành xong, tích chọn instance -> Chọn **Instance state** -> Chọn **Terminate instance** để xóa máy chủ, tránh phát sinh chi phí lưu trữ EBS.

---

## 9. Hệ thống ảo hóa AWS Nitro (AWS Nitro System)
**Câu hỏi:** AWS Nitro System là gì? Các thành phần cốt lõi của nó là gì và tại sao nó lại đóng vai trò quan trọng trong việc tăng tốc hiệu năng và bảo mật cho EC2?

**Trả lời:**

### A. Khái niệm về AWS Nitro System (What is AWS Nitro?)
*   **Định nghĩa:** **AWS Nitro System** là nền tảng ảo hóa thế hệ mới của AWS dành cho hầu hết các dòng máy chủ EC2 hiện đại (từ thế hệ 5 trở đi như M5, C5, R5, T3, v.v., ra mắt từ năm 2018).
*   **Cơ chế cải tiến:** 
    *   *Mô hình ảo hóa cũ:* Máy chủ vật lý phải dành một lượng CPU và RAM đáng kể để chạy phần mềm ảo hóa (hypervisor) nhằm quản lý mạng, lưu trữ EBS và bảo mật.
    *   *Mô hình ảo hóa Nitro:* AWS tách các tác vụ ảo hóa này ra khỏi CPU chính và chuyển giao hoàn toàn cho các **phần cứng chuyên dụng (dedicated hardware card/chip)** của hệ thống Nitro.

### B. Các thành phần cốt lõi của Nitro (Core Components of Nitro System)
Hệ thống Nitro hoạt động dựa trên sự kết hợp của 3 thành phần chính:
1.  **Các thẻ Nitro (Nitro Cards):** 
    *   Là các card phần cứng chuyên dụng độc lập để xử lý các tác vụ I/O cụ thể như: card xử lý mạng (VPC), card xử lý lưu trữ (EBS), và card quản lý hệ thống.
    *   Giúp giải phóng tài nguyên CPU chính và giảm thiểu tối đa độ trễ mạng/ổ đĩa.
2.  **Chip bảo mật Nitro (Nitro Security Chip):**
    *   Chip bảo mật được tích hợp trực tiếp trên bo mạch chủ vật lý.
    *   Đóng vai trò làm điểm tựa tin cậy phần cứng (hardware root of trust), giám sát và bảo vệ firmware của máy chủ khỏi các sửa đổi trái phép từ bên ngoài, đồng thời cho phép khởi chạy máy chủ vật lý chuyên dụng (Bare Metal).
3.  **Nitro Hypervisor (Trình ảo hóa Nitro):**
    *   Là một phần mềm ảo hóa siêu nhẹ (dựa trên KVM) chỉ thực hiện một nhiệm vụ duy nhất: phân chia tài nguyên CPU và RAM.
    *   Do không chứa các trình điều khiển (drivers) dư thừa hay giao diện dòng lệnh quản trị, Nitro Hypervisor có hiệu năng gần như tương đương máy vật lý và có diện tích tấn công (attack surface) siêu nhỏ, giúp tăng cường bảo mật tuyệt đối.

### C. Lợi ích kiến trúc và Ghi nhớ khi thi (Architectural Benefits & Exam Tips)
*   **Hiệu năng vượt trội:** Giúp khách hàng sử dụng được gần như 100% sức mạnh CPU và RAM của máy chủ vật lý mà không bị tiêu hao cho các tác vụ quản lý của AWS.
*   **Bảo mật "Không có quyền quản trị" (No-Operator Access):** Kiến trúc của Nitro ngăn chặn hoàn toàn việc nhân viên AWS có thể truy cập vào bộ nhớ hoặc ổ đĩa của khách hàng.
*   **Hỗ trợ Bare Metal:** Nhờ có Nitro Security Chip, AWS có thể cung cấp các instance dạng **Bare Metal** (máy chủ vật lý không chạy ảo hóa) cho các ứng dụng đòi hỏi hiệu năng tối đa hoặc chạy ảo hóa lồng nhau (nested virtualization).
*   **Mẹo thi (Exam Tips):** Nếu đề bài yêu cầu tối ưu hóa hiệu năng mạng (băng thông lớn, độ trễ thấp), chạy ảo hóa Bare Metal, hoặc bảo mật cô lập ở mức phần cứng cao nhất, hãy nghĩ ngay đến việc lựa chọn các dòng instance thế hệ mới sử dụng công nghệ **AWS Nitro**.









