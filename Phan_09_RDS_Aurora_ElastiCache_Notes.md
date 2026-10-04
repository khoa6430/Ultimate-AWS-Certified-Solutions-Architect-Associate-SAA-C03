# Ôn tập: Phần 9 - AWS Fundamentals: RDS + Aurora + ElastiCache

Tài liệu này lưu lại các kiến thức cốt lõi, câu hỏi ôn tập, phân tích tình huống thực tế và các bước thực hành quan trọng trong **Phần 9: RDS + Aurora + ElastiCache** phục vụ cho kỳ thi **AWS Certified Solutions Architect Associate (SAA-C03)**.

---

## Danh sách bài học trong Phần 9:
- [x] **Bài 88:** Amazon RDS Overview
- [x] **Bài 89:** RDS Read Replicas vs Multi AZ
- [x] **Bài 90:** Amazon RDS Hands On
- [x] **Bài 91:** RDS Custom for Oracle and Microsoft SQL Server
- [x] **Bài 92:** Amazon Aurora
- [x] **Bài 93:** Amazon Aurora - Hands On
- [x] **Bài 94:** Amazon Aurora - Advanced Concepts
- [x] **Bài 95:** RDS & Aurora - Backup and Monitoring
- [x] **Bài 96:** RDS Security
- [x] **Bài 97:** RDS Proxy
- [x] **Bài 98:** ElastiCache Overview
- [x] **Bài 99:** ElastiCache Hands On
- [ ] **Bài 100:** ElastiCache for Solution Architects
- [x] **Bài 101:** List of Ports to be familiar with
- [ ] **Trắc nghiệm 6:** RDS, Aurora, & ElastiCache Quiz

---

## Bài 88: Tổng quan về Amazon RDS (Amazon RDS Overview)
**Câu hỏi:** Amazon RDS là gì? Hỗ trợ những loại Database Engine nào? Tại sao nên chọn RDS thay vì tự cài đặt Database trên EC2? Cơ chế RDS Storage Auto Scaling hoạt động với các điều kiện cụ thể nào?

**Trả lời:**

### A. Slide bài giảng
````carousel
![Slide: Amazon RDS Overview](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1788672139781.png)
<!-- slide -->
![Slide: Lợi thế của RDS so với triển khai DB trên EC2](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1788672390029.png)
<!-- slide -->
![Slide: RDS Storage Auto Scaling](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1788672464957.png)
````

### B. Khái niệm cốt lõi (Amazon RDS là gì?)
*   **RDS** là viết tắt của **Relational Database Service** (Dịch vụ cơ sở dữ liệu quan hệ).
*   Là một **Managed Database Service** (Dịch vụ cơ sở dữ liệu do AWS quản lý hoàn toàn) dành cho các hệ CSDL sử dụng ngôn ngữ truy vấn **SQL**.
*   **7 Database Engines được RDS hỗ trợ (Phải nhớ khi đi thi):**
    1.  **PostgreSQL**
    2.  **MySQL**
    3.  **MariaDB**
    4.  **Oracle**
    5.  **Microsoft SQL Server**
    6.  **IBM DB2**
    7.  **Amazon Aurora** *(Cơ sở dữ liệu độc quyền do chính AWS phát triển với hiệu năng vượt trội)*

### C. So sánh: Dùng RDS (Managed) vs Tự cài DB trên EC2 (Self-Managed)
Tại sao chúng ta nên dùng RDS thay vì tạo một máy chủ EC2 rồi tự cài MySQL/PostgreSQL lên đó?

| Tiêu chí | Tự cài đặt DB trên EC2 | Sử dụng Amazon RDS |
| :--- | :--- | :--- |
| **Khởi tạo & Cài đặt (Provisioning)** | Phải tự cấu hình thủ công từ đầu (cài OS, cài DB engine, cấu hình network...). | **Tự động hóa hoàn toàn (Automated provisioning)** chỉ qua vài cú click. |
| **Vá lỗi hệ điều hành (OS Patching)** | Tự chịu trách nhiệm cập nhật bảo mật cho OS và phần mềm DB. | **AWS tự động cập nhật và vá lỗi hệ điều hành ngầm**. |
| **Sao lưu & Phục hồi (Backups & Restore)**| Phải tự viết script backup định kỳ lên S3 hoặc tạo snapshot thủ công. | **Sao lưu liên tục tự động**, hỗ trợ khôi phục về từng giây cụ thể (**Point-in-Time Restore - PITR**). |
| **Giám sát (Monitoring)** | Phải tự cài đặt CloudWatch Agent hoặc công cụ giám sát bên thứ 3. | Có sẵn **Dashboard giám sát hiệu năng** (CPU, RAM, Storage, Connections...). |
| **Mở rộng quy mô đọc (Read Scalability)** | Tự thiết lập cấu hình Replication phức tạp giữa các máy chủ. | Hỗ trợ tạo **Read Replicas** dễ dàng để giảm tải đọc cho Database chính. |
| **Dự phòng thảm họa (Disaster Recovery)**| Phải tự setup cụm Failover giữa các Data Center. | Hỗ trợ cấu hình **Multi-AZ** chỉ với một nút bấm (tự động failover khi có sự cố). |
| **Bộ nhớ lưu trữ (Storage)** | EBS Volume tự quản lý. | Sử dụng **EBS Volume do AWS quản lý** (gp2, gp3, io1, io2...). |
| **Quyền truy cập máy chủ (SSH Access)** | **CÓ toàn quyền root/admin** để SSH vào server. | ❌ **KHÔNG THỂ SSH** vào instance chạy RDS (Vì AWS quản lý ngầm OS). |

> ⚠️ **Lưu ý thi cực kỳ quan trọng:** Bạn **KHÔNG THỂ SSH vào RDS instance**. Nếu đề thi yêu cầu: *"Cần toàn quyền kiểm soát hệ điều hành (OS root access) hoặc cài thêm các extension/custom software đặc biệt cho database"* 👉 Giải pháp phải là **tự cài DB trên EC2** (hoặc RDS Custom).

### D. Cơ chế Tự động mở rộng dung lượng (RDS Storage Auto Scaling)
*   **Vấn đề:** Khi tạo RDS instance, bạn phải chỉ định dung lượng ổ đĩa ban đầu (ví dụ: 20 GB). Nếu dữ liệu tăng nhanh mà bạn không kịp mở rộng, database sẽ bị tràn ổ đĩa dẫn đến gián đoạn ứng dụng.
*   **Giải pháp:** Tính năng **Storage Auto Scaling** giúp RDS tự động phát hiện ổ đĩa sắp đầy và tự động tăng dung lượng lên mà **KHÔNG CẦN DOWNTIME** (không cần tắt hay khởi động lại database).
*   **Cấu hình bắt buộc:** Bạn phải đặt **Maximum Storage Threshold** (Ngưỡng dung lượng tối đa mà database được phép mở rộng tới) để tránh chi phí tăng không kiểm soát.
*   **3 Điều kiện kích hoạt mở rộng tự động (Rất hay hỏi trong đề thi):**
    1.  Dung lượng trống còn **dưới 10%** tổng dung lượng đã cấp phát.
    2.  Tình trạng thiếu dung lượng này kéo dài **ít nhất 5 phút**.
    3.  Đã trôi qua **ít nhất 6 giờ** kể từ lần điều chỉnh dung lượng gần nhất.
*   **Phạm vi áp dụng:** Hỗ trợ cho **tất cả** các database engines trên RDS. Rất hữu ích cho các ứng dụng có khối lượng dữ liệu phát triển nhanh hoặc tải ghi khó đoán trước (Unpredictable workloads).

---

## Bài 89: RDS Read Replicas vs Multi AZ
**Câu hỏi:** Phân biệt sự khác nhau cốt lõi giữa RDS Read Replicas và RDS Multi-AZ? Cơ chế sao chép (Sync vs Async), phạm vi ứng dụng, chi phí mạng và quy trình chuyển đổi từ Single-AZ sang Multi-AZ không gây gián đoạn (Zero Downtime) diễn ra như thế nào?

**Trả lời:**

### A. Slide bài giảng
````carousel
![Slide: RDS Read Replicas for read scalability](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1788672704557.png)
<!-- slide -->
![Slide: RDS Read Replicas - Use Cases](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1788672882790.png)
<!-- slide -->
![Slide: RDS Read Replicas - Network Cost](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1788683658557.png)
<!-- slide -->
![Slide: RDS Multi AZ (Disaster Recovery)](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1788683666521.png)
<!-- slide -->
![Slide: RDS - From Single-AZ to Multi-AZ](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1788686802704.png)
````

### B. RDS Read Replicas (Mở rộng khả năng đọc - Read Scalability)
*   **Mục đích chính:** Giảm tải đọc (Read workload) cho Database chính (Master DB) và tăng tốc độ truy vấn đọc. **KHÔNG dùng cho mục đích dự phòng thảm họa (Disaster Recovery)**.
*   **Số lượng:** Có thể tạo tối đa **15 Read Replicas**.
*   **Vị trí triển khai:** Có 3 lựa chọn:
    1.  Cùng một AZ (**Within AZ**).
    2.  Khác AZ nhưng cùng Region (**Cross AZ**).
    3.  Khác Region hoàn toàn (**Cross Region**).
*   **Cơ chế sao chép (Replication):**
    *   Sử dụng cơ chế **BẤT ĐỒNG BỘ (Asynchronous Replication)**.
    *   Dữ liệu có tính chất **Nhất quán sau cùng (Eventually Consistent)**: Nghĩa là có một khoảng trễ nhỏ (replication lag) giữa Master và Replica. Nếu ứng dụng vừa ghi vào Master mà đọc ngay lập tức từ Replica, có thể sẽ nhận dữ liệu cũ.
*   **Đặc điểm hoạt động & Kết nối:**
    *   Mỗi Read Replica có **một địa chỉ DNS Endpoint riêng biệt**. Ứng dụng phải tự cập nhật chuỗi kết nối (Connection String) để phân chia: ghi vào Master, đọc từ Replica.
    *   **Chỉ phục vụ câu lệnh ĐỌC (`SELECT`)**: Không cho phép các câu lệnh ghi dữ liệu như `INSERT`, `UPDATE`, `DELETE`.
    *   **Khả năng thăng cấp (Promote to standalone DB):** Bạn có thể tách một Read Replica ra và thăng cấp nó thành một Database độc lập có toàn quyền Đọc/Ghi (thoát khỏi cơ chế replication).
*   **Chi phí truyền dữ liệu mạng (Network Cost):**
    *   **Cùng Region (kể cả Cross-AZ):** **HOÀN TOÀN MIỄN PHÍ (Free)** vì đây là dịch vụ AWS quản lý.
    *   **Khác Region (Cross-Region):** **BỊ TÍNH PHÍ ($$$)** truyền dữ liệu liên vùng (Replication fee).

### C. Tình huống thực tế (Use Case kinh điển của Read Replicas)
*   **Bài toán:** Hệ thống bán hàng Production đang chạy bình thường thì đội ngũ Phân tích dữ liệu (Reporting / Business Intelligence) muốn chạy các câu lệnh truy vấn phức tạp để xuất báo cáo tháng. Nếu cho đội này cắm thẳng vào Database chính thì sẽ ngốn sạch CPU/RAM, làm đơ cả website mua hàng.
*   **Giải pháp Kiến trúc:** Tạo một **Read Replica** riêng. Trỏ ứng dụng Báo cáo/Phân tích sang địa chỉ DNS của Read Replica đó. Toàn bộ tác vụ nặng chỉ đọc trên Replica, hệ thống Production chính vẫn chạy mượt mà 100%.

---

### D. RDS Multi-AZ (Dự phòng thảm họa - Disaster Recovery & High Availability)
*   **Mục đích chính:** Đảm bảo **Tính sẵn sàng cao (High Availability)** và **Phục hồi sau sự cố (Disaster Recovery)**. **KHÔNG dùng để mở rộng quy mô tải (NOT used for scaling)**.
*   **Kiến trúc hoạt động:**
    *   Gồm **1 Master DB** (Active - xử lý Đọc & Ghi) đặt tại AZ A.
    *   Và **1 Standby DB** (Passive - đóng vai trò dự phòng) đặt tại AZ B.
*   **Cơ chế sao chép:**
    *   Sử dụng cơ chế **ĐỒNG BỘ (Synchronous Replication)**.
    *   Khi ứng dụng ghi vào Master DB, giao dịch chỉ được xác nhận thành công sau khi dữ liệu đã được nhân bản hoàn tất sang Standby DB $\rightarrow$ **Dữ liệu không bao giờ bị mất hoặc lệch**.
*   **Điểm truy cập duy nhất (Single DNS Name):**
    *   Ứng dụng chỉ kết nối tới **1 địa chỉ DNS duy nhất** do AWS cung cấp.
    *   **Standby DB KHÔNG cho phép truy cập trực tiếp** (không thể đọc, không thể ghi vào Standby).
*   **Tự động chuyển đổi dự phòng (Automatic Failover):**
    *   Khi Master gặp sự cố (sập cả AZ, đứt mạng, hỏng phần cứng hoặc hỏng ổ đĩa EBS), AWS tự động trỏ DNS Name sang Standby DB và thăng cấp Standby thành Master mới.
    *   Quá trình diễn ra hoàn toàn tự động, ứng dụng **không cần can thiệp thủ công hay thay đổi connection string**.
*   **Mẹo thi mở rộng:** Bạn hoàn toàn có thể thiết lập **Read Replica dạng Multi-AZ** (vừa có khả năng chia tải đọc, vừa có Disaster Recovery cho chính replica đó).

---

### E. Quy trình chuyển từ Single-AZ sang Multi-AZ (Zero Downtime)
*   **Đặc điểm quan trọng:** Quá trình chuyển đổi từ Single-AZ sang Multi-AZ là một thao tác **KHÔNG GÂY DOWNTIME (Zero downtime operation)**. Bạn không cần tắt máy chủ hay ngừng hoạt động của database.
*   **Cách thực hiện:** Vào AWS Console $\rightarrow$ Chọn Database $\rightarrow$ Nhấn **Modify** $\rightarrow$ Chọn **Multi-AZ Deployment** $\rightarrow$ Lưu lại.
*   **Cơ chế AWS xử lý ngầm (Rất hay hỏi trong bài thi):**
    1.  AWS tự động chụp một bản sao lưu nhanh (**Snapshot**) từ Master DB hiện tại.
    2.  Dùng Snapshot đó để khôi phục (**Restore**) ra một máy chủ Standby DB mới tại một AZ khác.
    3.  Thiết lập kênh đồng bộ (**Synchronous Replication**) giữa Master DB và Standby DB để đồng bộ nốt các thay đổi mới phát sinh. Hoàn tất cấu hình Multi-AZ!

---

### F. Bảng so sánh tổng kết: Read Replicas vs Multi-AZ (Trọng tâm đề thi)

| Tiêu chí | RDS Read Replicas | RDS Multi-AZ |
| :--- | :--- | :--- |
| **Mục đích cốt lõi** | **Mở rộng hiệu năng ĐỌC (Scalability)** | **Tính sẵn sàng cao & Dự phòng thảm họa (HA / DR)** |
| **Cơ chế sao chép** | **Bất đồng bộ (Asynchronous)** | **Đồng bộ (Synchronous)** |
| **Tính nhất quán dữ liệu**| **Eventually Consistent** (có độ trễ sao chép) | **Strictly Consistent** (dữ liệu luôn đồng nhất 100%) |
| **Tác động đến DB chính**| Không ảnh hưởng đến hiệu năng ghi | Có thể tăng nhẹ độ trễ ghi (vì phải chờ ghi sang Standby) |
| **Địa chỉ kết nối (DNS)** | **Mỗi Replica có 1 DNS Endpoint riêng** | **Dùng chung 1 DNS Name duy nhất** cho toàn cụm |
| **Quyền truy cập** | Cho phép ứng dụng kết nối để **ĐỌC (`SELECT`)** | Standby DB **ở trạng thái thụ động (Passive)**, không cho phép truy cập |
| **Phạm vi triển khai** | Same AZ, Cross-AZ, hoặc **Cross-Region** | Chỉ triển khai **Cross-AZ** (trong cùng 1 Region) |
| **Cơ chế Failover** | Thủ công (phải tự thăng cấp lên standalone DB) | **Tự động 100% (Automatic Failover)** |
| **Chi phí truyền dữ liệu** | Miễn phí cùng Region; Tính phí khi Cross-Region | **Hoàn toàn miễn phí** (Same Region) |

---

## Bài 90: Thực hành Amazon RDS (Amazon RDS Hands On)
**Câu hỏi:** Các bước tạo một cơ sở dữ liệu MySQL trên RDS, cấu hình Public Access, thiết lập Security Group và kết nối từ máy Client (SQL Electron) diễn ra như thế nào? Cần lưu ý gì về chi phí Free Tier và các bước xóa bỏ tài nguyên an toàn?

**Trả lời:**

### A. Tóm tắt các bước thực hành (Hands-on Steps)

1.  **Khởi tạo Database trên AWS Console:**
    *   Truy cập dịch vụ **RDS** $\rightarrow$ Chọn **Databases** $\rightarrow$ Nhấn **Create database**.
    *   **Database creation method:** Chọn **Standard create** (để có đầy đủ các tùy chọn cấu hình).
    *   **Engine options:** Chọn **MySQL**.
    *   **Templates (CỰC KỲ QUAN TRỌNG):** Chọn **Free tier** *(nếu chọn Production sẽ tự bật Multi-AZ tính tiền!)*.
    *   **Settings:**
        *   DB instance identifier: Giữ mặc định hoặc đặt tên (ví dụ: `database-1`).
        *   Master username: `admin`.
        *   **Credentials management:** Chọn **Self managed** (tự nhập password, ví dụ: `password123`). *Không chọn AWS Secrets Manager vì Secrets Manager sẽ bị tính phí.*
    *   **Instance configuration:** Chọn loại instance thuộc Free Tier: `db.t4g.micro` hoặc `db.t3.micro` (1 vCPU, 1 GB RAM).
    *   **Storage:** 20 GiB gp2/gp3. Bật hoặc tắt *Storage Auto Scaling* (ngưỡng tối đa 1000 GiB).
    *   **Connectivity (Kết nối mạng):**
        *   VPC: Default VPC.
        *   **Public access:** Chọn **Yes** (để có thể kết nối từ công cụ SQL trên máy tính cá nhân qua Internet).
        *   **VPC security group:** Chọn **Create new** $\rightarrow$ Đặt tên (ví dụ: `demo-rds-sg`). AWS sẽ tự động thêm rule Inbound cho port `3306` từ IP hiện tại của bạn (`My IP`).
    *   **Additional configuration:**
        *   Initial database name: Nhập tên database ban đầu (ví dụ: `mydb`).
    *   Nhấn **Create database** và đợi vài phút đến khi trạng thái chuyển sang **Available**.

2.  **Cấu hình Mạng & Security Group:**
    *   Lấy **Endpoint** (ví dụ: `database-1.xxx.us-east-1.rds.amazonaws.com`) và **Port** (`3306`).
    *   Kiểm tra Security Group: Vào tab *Connectivity & security* $\rightarrow$ Click vào Security Group $\rightarrow$ Kiểm tra tab **Inbound rules**:
        *   Type: `MySQL/Aurora` (TCP Port `3306`).
        *   Source: IP của bạn (`x.x.x.x/32`) hoặc `0.0.0.0/0` (chỉ dùng khi test tạm thời).

3.  **Kết nối từ máy tính qua SQL Client (SQL Electron / DBeaver):**
    *   Mở SQL Electron (hoặc DBeaver / Navicat / VS Code Database Client).
    *   Tạo kết nối mới:
        *   Database Type: `MySQL`.
        *   Server / Host: Nhập **Endpoint** của RDS vừa copy.
        *   Port: `3306`.
        *   User: `admin`.
        *   Password: Mật khẩu đã đặt ở bước 1.
        *   Initial Database: `mydb`.
    *   Nhấn **Test Connection** $\rightarrow$ Báo *Connection successful*.
    *   Thực hiện câu lệnh SQL: Tạo bảng `CREATE TABLE` và chèn dữ liệu `INSERT INTO`, kiểm tra `SELECT * FROM`.

### B. Kinh nghiệm thực chiến & Xử lý lỗi kết nối thực tế (Troubleshooting Tips)
Trong quá trình thực hành kết nối từ máy Client (như DBeaver / SQL Electron) tới AWS RDS MySQL, các kỹ sư thường gặp 3 vấn đề kinh điển sau:

1.  **Lỗi không tìm thấy mục "Publicly accessible" khi tạo hoặc sửa DB:**
    *   *Nguyên nhân:* Trong giao diện mới của AWS, tùy chọn này bị ẩn bên trong mục thu gọn (Accordion).
    *   *Khắc phục:* Vào **Modify** $\rightarrow$ Tìm mục **Connectivity** $\rightarrow$ Click mở **`▶ Additional configuration`** $\rightarrow$ Tích chọn **Public access: Yes** $\rightarrow$ Lưu với tùy chọn **Apply immediately**.

2.  **Lỗi "Connection timed out" (Gói tin bị treo, không phản hồi):**
    *   *Bản chất Mạng (Layer 4):* Security Group của RDS chưa mở cổng tiếp nhận lưu lượng từ IP Public nhà bạn.
    *   *Khắc phục:* Vào Security Group của DB $\rightarrow$ Tab **Inbound rules** $\rightarrow$ Thêm rule: **Type:** `MySQL/Aurora`, **Port:** `3306`, **Source:** `My IP` (hoặc `0.0.0.0/0` để test tạm).

3.  **Lỗi "Public Key Retrieval is not allowed" trên DBeaver (Rất phổ biến với MySQL 8.x):**
    *   *Bản chất Dev / Security:* MySQL 8.x sử dụng cơ chế xác thực mặc định là `caching_sha2_password`. Driver JDBC mặc định chặn việc tự động lấy Public RSA Key của server.
    *   *Khắc phục:* Trong cửa sổ kết nối DBeaver $\rightarrow$ Chuyển sang tab **Driver properties** $\rightarrow$ Đổi thuộc tính **`allowPublicKeyRetrieval`** thành **`true`** và **`useSSL`** thành **`false`** $\rightarrow$ Test Connection sẽ thành công ngay.

### C. Các tính năng quản trị mở rộng trong Console
*   **Monitoring:** Giám sát CPU Utilization, Database Connections (số lượng client kết nối), Read/Write IOPS, Free Storage Space qua CloudWatch Metrics.
*   **Read Replica:** Bấm *Actions* $\rightarrow$ *Create read replica* để tạo bản sao tăng tốc độ đọc.
*   **Take Snapshot:** Sao lưu tức thời database để di chuyển sang Region khác hoặc khôi phục (Restore).

### D. Dọn dẹp tài nguyên tránh phát sinh chi phí (Clean Up)
*   **Bước 1 - Tắt Deletion Protection:** RDS mặc định bật bảo vệ chống xóa nhầm. Bấm **Modify** $\rightarrow$ Cuộn xuống cuối trang tìm mục **Deletion protection** $\rightarrow$ Bỏ tích chọn $\rightarrow$ Nhấn *Continue* $\rightarrow$ Chọn *Apply immediately*.
*   **Bước 2 - Xóa Database:** Quay lại danh sách, chọn DB $\rightarrow$ Bấm **Actions** $\rightarrow$ **Delete**:
    *   Bỏ chọn *Create final snapshot?* (tránh tốn tiền lưu snapshot).
    *   Tích chọn *I acknowledge that upon instance deletion, automated backups are no longer available...*
    *   Gõ chữ `delete me` vào ô xác nhận $\rightarrow$ Nhấn **Delete**.

---

## Bài 91: RDS Custom cho Oracle và Microsoft SQL Server (RDS Custom for Oracle and Microsoft SQL Server)
**Câu hỏi:** RDS Custom là gì? Nó giải quyết bài toán gì mà RDS Standard không làm được? Hỗ trợ những database engine nào? Cơ chế "Automation Mode" hoạt động ra sao và cần lưu ý gì trước khi can thiệp vào hệ điều hành bên dưới?

**Trả lời:**

### A. Slide bài giảng
````carousel
![Slide: RDS Custom for Oracle and Microsoft SQL Server](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1788885113219.png)
````

### B. Khái niệm cốt lõi (RDS Custom là gì?)
*   **Vấn đề của RDS Standard:** Như đã học ở Bài 88, dịch vụ RDS thông thường là dịch vụ quản lý hoàn toàn (*Fully Managed*), AWS chịu trách nhiệm ngầm toàn bộ OS và **cấm tuyệt đối quyền truy cập SSH/RDP vào máy chủ**. Điều này gây bế tắc cho các ứng dụng doanh nghiệp lâu đời (Legacy Enterprise Apps) cần can thiệp sâu vào hệ điều hành hoặc database engine.
*   **Giải pháp - RDS Custom:** Là giải pháp nằm ở giữa **RDS Standard** và **Tự cài DB trên EC2**. Nó vừa duy trì các tính năng tự động hóa của RDS (tạo lập, vận hành, backup, scale), vừa **cấp toàn quyền quản trị (Full Admin / Root Access)** vào hệ điều hành (OS) và database bên dưới.
*   **2 Database Engines DUY NHẤT được hỗ trợ:**
    1.  **Oracle**
    2.  **Microsoft SQL Server**
    *(⚠️ Lưu ý: MySQL, PostgreSQL, MariaDB KHÔNG hỗ trợ RDS Custom. Nếu muốn toàn quyền kiểm soát OS của MySQL/PostgreSQL, bạn bắt buộc phải tự cài trên EC2).*

### C. Các quyền năng và phương thức truy cập với RDS Custom
Khi sử dụng RDS Custom, bạn có thể:
*   Tùy biến cấu hình sâu bên trong database (Database Settings) mà RDS Standard không hỗ trợ.
*   Cài đặt các bản vá lỗi riêng biệt của hệ điều hành và DB (Install OS/DB Patches).
*   Kích hoạt các tính năng gốc (Native Features) của Oracle hoặc SQL Server.
*   Cài thêm các phần mềm/agent giám sát bên thứ 3 (Third-party monitoring agents) trực tiếp vào OS.
*   **Phương thức truy cập vào EC2 Instance bên dưới:**
    *   Sử dụng **SSH** (đối với Linux / Oracle).
    *   Sử dụng **AWS Systems Manager (SSM) Session Manager** (phương thức bảo mật chuẩn AWS, truy cập shell từ xa qua HTTPS mà không cần mở port SSH 22 trên Security Group).

### D. Cơ chế "Automation Mode" và Quy trình tùy biến an toàn
*   **Bản chất Automation Mode:** Ở trạng thái bình thường, RDS chạy một tiến trình ngầm để liên tục kiểm tra sức khỏe (*Health Check*) và tự động khôi phục (*Auto-healing*) nếu instance gặp lỗi. Nếu bạn đang SSH vào máy để sửa file hệ thống hoặc nâng cấp phần mềm mà không báo trước, AWS có thể hiểu nhầm là hệ thống bị hỏng và tự động can thiệp (ví dụ: reboot máy hoặc rollback phiên bản), gây gián đoạn công việc.
*   **Quy trình chuẩn khi tùy biến trên RDS Custom (BẮT BUỘC PHẢI NHỚ ĐỂ ĐI THI):**
    1.  **Chụp bản sao lưu (DB Snapshot):** Tạo một snapshot trước khi can thiệp để có điểm khôi phục an toàn phòng trường hợp thao tác OS làm hỏng database.
    2.  **Tắt chế độ tự động hóa (De-activate Automation Mode):** Tạm ngưng tính năng giám sát và can thiệp tự động của RDS trong một khoảng thời gian bạn ấn định.
    3.  **Tiến hành tùy biến:** Truy cập qua SSH / SSM Session Manager để cài patch, đổi config, can thiệp file system...
    4.  **Bật lại chế độ tự động hóa (Re-activate Automation Mode):** Sau khi hoàn tất, bật lại Automation Mode để AWS tiếp tục giám sát và tự động quản lý hệ thống.

---

### E. Bảng so sánh: RDS Standard vs. RDS Custom vs. Tự cài DB trên EC2

| Tiêu chí | RDS Standard | RDS Custom | Tự cài đặt DB trên EC2 |
| :--- | :--- | :--- | :--- |
| **Mức độ quản lý (Management)** | AWS quản lý toàn bộ từ OS đến DB | Chia sẻ: AWS quản lý hạ tầng cơ bản, người dùng quản lý OS/tùy biến | Người dùng tự quản lý 100% |
| **Quyền truy cập OS (SSH / RDP)**| ❌ **Hoàn toàn KHÔNG** | ✅ **CÓ toàn quyền Admin / Root** (qua SSH, SSM) | ✅ **CÓ toàn quyền** |
| **Database Engines hỗ trợ** | 7 engines (MySQL, Postgres, Oracle, SQL Server, MariaDB, DB2, Aurora) | ⚠️ **Chỉ hỗ trợ Oracle & Microsoft SQL Server** | Bất kỳ Database nào tùy thích |
| **Mục đích sử dụng chính** | Ứng dụng Cloud-native hiện đại, không cần can thiệp OS | Ứng dụng Enterprise cũ (Legacy) cần custom OS, cài driver/agent riêng | Cần cấu hình phần cứng đặc thù hoặc các DB không được RDS hỗ trợ |
| **Trách nhiệm bảo trì OS** | AWS tự động vá lỗi định kỳ | **Người dùng tự chịu trách nhiệm** vá lỗi khi đã tùy biến | Người dùng tự chịu trách nhiệm |

---

### F. Góc nhìn Lập trình viên (.NET / C#) & DevOps
*   **Đối với dev C# / ASP.NET Core & SQL Server:** Khi làm việc với các hệ thống enterprise cũ chạy SQL Server, bạn có thể cần:
    *   Cài đặt **SQL Server Reporting Services (SSRS)** hoặc **Integration Services (SSIS)** trực tiếp trên cùng máy chủ.
    *   Kích hoạt các **CLR Stored Procedures** (thực thi mã .NET assembly bên trong SQL Server engine) hoặc Linked Servers tới các nguồn dữ liệu mạng nội bộ đặc thù.
    *   RDS Standard sẽ chặn hoặc giới hạn rất nhiều quyền hạn này (`sysadmin` privileges). Khi đó, **RDS Custom for SQL Server** là cứu cánh hoàn hảo giúp bạn vừa có quyền `sysadmin` + OS access, vừa không phải tự mình lo chuyện dựng máy, backup tự động hay EBS volume failover từ đầu như trên EC2.
*   **Đối với DevOps & Network:**
    *   RDS Custom cho phép sử dụng **AWS Systems Manager (SSM) Session Manager**. Nghĩa là Security Group không cần mở Inbound port 22 (SSH) hay 3389 (RDP) ra ngoài Internet, toàn bộ traffic điều khiển đi qua kênh mã hóa an toàn của AWS, cực kỳ chuẩn chỉnh về mặt Network Security.

### G. Hai quy tắc "bất di bất dịch" khi đi thi SAA-C03

#### 1. Quy tắc 1: Chỉ hỗ trợ 2 Database Engines
*   ✅ **Oracle**
*   ✅ **Microsoft SQL Server**
*   > ⚠️ **Bẫy thi:** Đề bài hỏi: *"Cần chạy MySQL hoặc PostgreSQL và yêu cầu truy cập OS để cài plugin/patch riêng"*. Bạn mà chọn RDS Custom là **SAI**. Với MySQL/PostgreSQL, bắt buộc phải chọn **tự cài trên Amazon EC2**.

#### 2. Quy tắc 2: Chế độ Automation Mode (Rất hay hỏi trắc nghiệm)
*   **Vấn đề:** RDS ngầm có một tiến trình Health Check liên tục. Nếu bạn SSH vào OS rồi dừng service DB, đổi file cấu hình hay update kernel mà AWS không biết, AWS sẽ tưởng server bị lỗi phần cứng/phần mềm và tự ý can thiệp (ví dụ reboot lại instance hoặc khôi phục snapshot cũ làm mất công bạn vừa làm).
*   **Quy trình chuẩn khi bảo trì / tùy biến:**
    1.  **Chụp DB Snapshot** (Đề phòng nghịch hỏng OS thì còn có điểm rollback an toàn).
    2.  **De-activate Automation Mode** (Tạm tắt chế độ tự động can thiệp của RDS).
    3.  **Đăng nhập vào OS** qua **SSH** hoặc **SSM Session Manager** để tùy biến / vá lỗi.
    4.  **Re-activate Automation Mode** (Bật lại để RDS tiếp tục giám sát và bảo vệ hệ thống).

---

## Bài 92: Tổng quan về Amazon Aurora (Amazon Aurora)
**Câu hỏi:** Amazon Aurora là gì? Nó có tương thích với MySQL và PostgreSQL không? Kiến trúc lưu trữ phân tán (Decoupled Shared Storage) và cơ chế chịu lỗi Quorum (6 bản sao trên 3 AZ) hoạt động thế nào? Sự khác biệt giữa Writer Endpoint và Reader Endpoint là gì? Tại sao Aurora lại có tốc độ nhân bản cực nhanh (Replication lag < 10ms) và thời gian Failover < 30s?

**Trả lời:**

### A. Slide bài giảng
````carousel
![Slide: Amazon Aurora Overview](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1788885585018.png)
<!-- slide -->
![Slide: Aurora High Availability and Read Scaling](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1788885596357.png)
<!-- slide -->
![Slide: Aurora DB Cluster - Writer & Reader Endpoints](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1788885603793.png)
<!-- slide -->
![Slide: Features of Aurora](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1788885616279.png)
````

### B. Khái niệm cốt lõi (Amazon Aurora là gì?)
*   **Công nghệ độc quyền của AWS (Proprietary Technology):** Không phải là phần mềm mã nguồn mở. Đây là hệ quản trị cơ sở dữ liệu quan hệ (RDBMS) thế hệ mới được chính AWS thiết kế và tối ưu riêng cho nền tảng điện toán đám mây (**Cloud-optimized / Cloud-native**).
*   **Hoàn toàn tương thích (Wire-compatible):**
    *   Tương thích 100% với **PostgreSQL** và **MySQL**.
    *   Ứng dụng sử dụng chung driver kết nối, chuỗi kết nối (Connection String) và cú pháp SQL y hệt như đang làm việc với MySQL hay PostgreSQL thông thường. Bạn không cần phải sửa bất kỳ dòng mã nguồn ứng dụng nào.
*   **Hiệu năng vượt trội (Performance):**
    *   Nhanh gấp **5 lần (5x)** so với MySQL tiêu chuẩn chạy trên RDS.
    *   Nhanh gấp **3 lần (3x)** so với PostgreSQL tiêu chuẩn chạy trên RDS.
*   **Chi phí vận hành:**
    *   Chi phí máy chủ cao hơn RDS tiêu chuẩn khoảng **~20%**, nhưng nhờ hiệu năng vượt trội và cơ chế lưu trữ thông minh, tổng chi phí sở hữu (**TCO - Total Cost of Ownership**) ở quy mô lớn thực tế lại rẻ hơn nhiều.

### C. Kiến trúc Lưu trữ Phân tán Đột phá (Decoupled Shared Storage Architecture)
Sức mạnh vượt trội của Aurora đến từ việc AWS đã tái cấu trúc lại database: **Tách rời hoàn toàn tầng Xử lý tính toán (Compute)** và **tầng Lưu trữ (Storage)**.

1.  **Dung lượng lưu trữ tự động co giãn (Auto-expanding Storage):**
    *   Khởi đầu tối thiểu từ **10 GB**.
    *   Tự động tăng trưởng theo từng khối **10 GB** khi dữ liệu phát sinh.
    *   Khả năng mở rộng tối đa lên tới **256 TB** (trước đây là 128 TB).
    *   Kỹ sư DevOps / DBA hoàn toàn **không cần giám sát dung lượng ổ cứng** hay lo lắng việc tràn đĩa. Bạn chỉ trả tiền đúng cho lượng dữ liệu thực tế đang sử dụng.
2.  **Cơ chế sao chép chịu lỗi Quorum (6 bản sao trên 3 AZ):**
    *   Mỗi khối dữ liệu ghi vào Aurora luôn được tự động nhân bản thành **6 bản sao (6 copies)** phân bổ đều trên **3 Availability Zones (AZ)** (mỗi AZ chứa 2 bản sao).
    *   Dữ liệu được chia nhỏ (striping) rải đều trên hàng trăm ổ đĩa lưu trữ ảo ở backend.
    *   **Quorum cho tác vụ Ghi (Writes):** Chỉ cần **4 trên 6 bản sao (4/6)** xác nhận ghi thành công $\rightarrow$ Hệ thống vẫn ghi dữ liệu bình thường ngay cả khi **sập toàn bộ 1 Availability Zone (mất 2 bản sao)**.
    *   **Quorum cho tác vụ Đọc (Reads):** Chỉ cần **3 trên 6 bản sao (3/6)** xác nhận đọc thành công $\rightarrow$ Đảm bảo tính sẵn sàng cao tuyệt đối cho việc truy vấn đọc.
3.  **Cơ chế tự phục hồi (Self-Healing Storage):**
    *   Hệ thống sử dụng cơ chế sao chép ngang hàng (*peer-to-peer replication*) chạy ngầm liên tục ở tầng storage. Nếu một ổ đĩa hoặc block dữ liệu bị lỗi (corrupted/bad sector), Aurora sẽ tự động tìm kiếm các bản sao tốt ở các ổ đĩa khác để tự sửa chữa mà không làm suy giảm hiệu năng của database engine.

---

### D. Kiến trúc Cụm Aurora (Aurora DB Cluster Architecture)
Cụm Aurora bao gồm hai thành phần máy chủ tính toán:
1.  **Một Master Instance (Primary / Writer Instance):**
    *   Là máy chủ duy nhất tiếp nhận các truy vấn **GHI (`INSERT`, `UPDATE`, `DELETE`)** vào hệ thống (đồng thời có thể phục vụ cả truy vấn Đọc).
    *   Ghi dữ liệu trực tiếp xuống tầng lưu trữ phân tán dùng chung (*Shared Distributed Storage*).
2.  **Tối đa 15 Aurora Read Replicas:**
    *   Có thể khởi tạo từ 0 đến **15 Read Replicas** để mở rộng quy mô đọc dữ liệu.
    *   Hỗ trợ cấu hình **Auto Scaling** cho Read Replicas: Tự động thêm replica khi tải CPU hoặc lượng connection tăng cao và tự động giảm khi hết tải.
    *   **Độ trễ sao chép cực thấp (Sub-10ms replica lag):**
        *   *Tại sao lại nhanh như vậy?* Trong RDS truyền thống, Master phải gửi toàn bộ binary log qua mạng, từng Replica phải tự ghi log xuống đĩa EBS riêng của nó $\rightarrow$ Độ trễ cao.
        *   Trong Aurora, **Tất cả các Read Replicas dùng chung một tầng Storage với Master**. Master chỉ gửi thông tin nhật ký Redo Log cực nhẹ qua bộ nhớ RAM của Replica để cập nhật buffer cache $\rightarrow$ Tốc độ đồng bộ gần như tức thời ($< 10\text{ ms}$).
    *   Hỗ trợ **Cross-Region Replication** để nhân bản dữ liệu sang các khu vực khác trên toàn cầu.
3.  **Tự động chuyển đổi dự phòng siêu tốc (Automated Failover < 30s):**
    *   Nếu Master gặp sự cố, một trong các Read Replicas sẽ được tự động thăng cấp (promote) thành Master mới.
    *   Do dữ liệu nằm sẵn trên tầng Shared Storage, quá trình failover diễn ra cực nhanh: **Trung bình dưới 30 giây** (nhanh hơn rất nhiều so với thời gian 1-2 phút của RDS Multi-AZ truyền thống).

---

### E. Điểm kết nối trong cụm Aurora (Cluster Endpoints - Trọng tâm đề thi)
Để ứng dụng không phải lo lắng về việc instance nào đang là Master hay số lượng Replica đang thay đổi ra sao, Aurora cung cấp 2 Endpoint thông minh:

```
                  ┌──────────────────────┐
                  │    Client / App      │
                  └──────────┬───────────┘
                             │
         ┌───────────────────┴───────────────────┐
         ▼                                       ▼
┌────────────────────────┐              ┌────────────────────────┐
│    Writer Endpoint     │              │    Reader Endpoint     │
│ (Trỏ tới Master duy nhất)│              │(Cân bằng tải các Replica)│
└────────────┬───────────┘              └────────────┬───────────┘
             │                                       │
             ▼                                       ▼
    ┌────────────────┐                ┌─────────────────────────────┐
    │  Master (W/R)  │                │ Replica 1  Replica 2  ...   │
    └────────┬───────┘                └──────────────┬──────────────┘
             │                                       │
             └───────────────────┬───────────────────┘
                                 ▼
              =======================================
              Shared Storage Volume (10 GB - 256 TB)
              =======================================
```

1.  **Writer Endpoint:**
    *   Là một địa chỉ DNS duy nhất luôn luôn trỏ chính xác vào máy chủ **Master hiện tại**.
    *   Nếu Master bị sự cố và failover sang một Replica khác, DNS của Writer Endpoint sẽ tự động cập nhật trỏ sang máy chủ mới $\rightarrow$ Ứng dụng **không cần thay đổi Connection String**.
2.  **Reader Endpoint:**
    *   Là một địa chỉ DNS duy nhất đại diện cho **toàn bộ nhóm Read Replicas**.
    *   Cung cấp tính năng **Cân bằng tải kết nối (Connection Load Balancing)**: Mỗi khi ứng dụng mở một kết nối mới tới Reader Endpoint, Aurora sẽ tự động phân phối kết nối đó tới một trong các Read Replicas.
    *   Khi bật Auto Scaling (thêm hoặc bớt Replicas), Reader Endpoint sẽ tự động phân bổ tải tới các replica mới mà lập trình viên không cần can thiệp.
    *   > ⚠️ **Lưu ý kỹ thuật đặc thù:** Cơ chế cân bằng tải của Reader Endpoint diễn ra ở **Cấp độ Kết nối (Connection Level)**, KHÔNG phải ở **Cấp độ Từng câu lệnh (Statement Level)**. Nếu một connection đã được mở tới Replica A, tất cả các câu lệnh SQL trong session đó sẽ chạy trên Replica A cho đến khi đóng kết nối.

---

### F. Các tính năng nổi bật khác của Aurora
*   **Tính năng Backtrack (Tua ngược thời gian dữ liệu):**
    *   Cho phép "tua ngược" trạng thái toàn bộ database về một thời điểm cụ thể bất kỳ trong quá khứ (ví dụ: quay về lúc 4:00 chiều hôm qua do vừa lỡ tay chạy lệnh `UPDATE` hay `DELETE` nhầm không có mệnh đề `WHERE`).
    *   **Điểm đột phá:** Hoàn toàn **KHÔNG CẦN khôi phục từ Backup hay Snapshot**. Quá trình diễn ra chỉ trong vài giây đến vài phút nhờ cơ chế dò lại log trên tầng lưu trữ phân tán.
*   **Vá lỗi không gián đoạn (Automated Patching with Zero Downtime):** Aurora tự động áp dụng các bản cập nhật bảo mật ngầm mà không gây downtime ứng dụng.
*   **Bảo mật & Tuân thủ:** Mã hóa dữ liệu lưu trữ (Encryption at rest với AWS KMS) và mã hóa đường truyền (SSL/TLS in transit).

---

### G. Bảng so sánh tổng kết: Amazon RDS vs. Amazon Aurora

| Tiêu chí | Amazon RDS thông thường | Amazon Aurora |
| :--- | :--- | :--- |
| **Kiến trúc lưu trữ** | Gắn chặt với EBS Volume của từng Instance | **Tách biệt hoàn toàn (Decoupled Shared Storage)** |
| **Bản sao lưu trữ (Storage Redundancy)**| 1 bản sao (Single-AZ) hoặc 2 bản sao (Multi-AZ) | **6 bản sao rải đều trên 3 Availability Zones** |
| **Dung lượng lưu trữ tối đa** | Tối đa 64 TiB (gp3, io2) | **Tối đa 256 TB** (Tự mở rộng từ 10 GB) |
| **Số lượng Read Replicas tối đa** | Tối đa 15 Read Replicas | Tối đa 15 Read Replicas (**hỗ trợ Auto Scaling**) |
| **Độ trễ sao chép (Replication Lag)** | Vài giây đến vài phút (Asynchronous qua mạng) | **Dưới 10 mili-giây (Sub-10ms)** |
| **Thời gian chuyển đổi dự phòng (Failover)**| 1 - 2 phút | **Dưới 30 giây (Instantaneous)** |
| **Điểm kết nối (Endpoints)** | Mỗi Replica có 1 DNS riêng | **Writer Endpoint** (cho Master) & **Reader Endpoint** (Load balancer cho Replicas) |
| **Tính năng quay ngược thời gian** | Phải Restore ra 1 DB mới từ Snapshot (lâu) | **Backtrack** (tua ngược trong vài phút, không cần backup) |
| **Hiệu năng so sánh** | Chuẩn engine gốc | **5x với MySQL, 3x với PostgreSQL** |

---

### H. Góc nhìn Lập trình viên (.NET / Next.js) & DevOps / Network
*   **Lập trình viên C# / ASP.NET Core (Entity Framework Core):**
    *   Thay vì chỉ khai báo 1 chuỗi kết nối duy nhất, cấu hình ứng dụng chuẩn Enterprise với Aurora sẽ tách thành 2 Connection Strings trong `appsettings.json`:
        *   `WriteDbConnection`: Trỏ tới **Writer Endpoint** (dùng cho các tác vụ `SaveChanges()`, command ghi dữ liệu).
        *   `ReadOnlyDbConnection`: Trỏ tới **Reader Endpoint** (dùng cho các truy vấn `AsNoTracking()`, query lấy danh sách, báo cáo).
    *   *Lưu ý về Connection Pooling:* Vì Reader Endpoint cân bằng tải ở **Connection level**, nếu ứng dụng .NET tái sử dụng connection pool quá lâu mà không tạo kết nối mới, tải giữa các Read Replicas có thể bị lệch (đặc biệt khi vừa có replica mới được auto-scale ra). Cần cấu hình `Connection Lifetime` hợp lý.
*   **Lập trình viên React / Next.js (Serverless / API Routes):**
    *   Các môi trường Serverless (như Next.js chạy trên Vercel hoặc AWS Lambda) có đặc tính mở hàng trăm connection ngắn hạn đồng thời. Aurora kết hợp cực tốt với **RDS Proxy** (sẽ học ở Bài 97) để quản lý connection pool và chuyển hướng mượt mà tới Writer/Reader Endpoint.
*   **Bản chất Mạng & Hệ thống (Quorum Consensus):**
    *   Cơ chế $4/6$ writes và $3/6$ reads của Aurora dựa trên thuật toán đồng thuận phân tán (**Quorum-based consensus**, tương tự tinh thần của Raft/Paxos).
    *   Thay vì một khối dữ liệu phải chờ xác nhận từ tất cả các nút (dễ bị nghẽn mạng do node chậm nhất - "straggler"), Aurora chỉ cần quá bán ($> 50\%$) số bản sao phản hồi là coi như giao dịch thành công. Điều này giúp loại bỏ hoàn toàn hiện tượng jitter/lag do hạ tầng mạng gây ra.

---

### I. Mẹo thi & Bẫy trắc nghiệm SAA-C03 (Exam Keywords & Traps)
> ⚠️ **Các từ khóa "vàng" chỉ điểm Amazon Aurora trong bài thi:**
> 1.  **"PostgreSQL / MySQL compatible with 5x / 3x performance"** $\rightarrow$ Chọn ngay **Amazon Aurora**.
> 2.  **"6 copies across 3 AZs"** & **"Resilient to loss of 1 AZ without impacting writes"** $\rightarrow$ Kiến trúc lưu trữ Quorum của **Aurora** (cần 4/6 để ghi, mất 1 AZ tức là mất 2 bản sao, vẫn còn 4 bản sao nên ghi bình thường!).
> 3.  **"Automated failover in less than 30 seconds"** $\rightarrow$ **Aurora** (nhanh hơn nhiều so với RDS Multi-AZ 1-2 phút).
> 4.  **"Auto-scaling Read Replicas with a single DNS endpoint for load balancing"** $\rightarrow$ **Aurora Reader Endpoint**.
> 5.  **"Accidentally deleted data or wrong SQL query, need to rewind database quickly without restoring a snapshot"** $\rightarrow$ Tính năng **Backtrack** của Aurora.
> 6.  **"Storage grows automatically up to 256 TB"** $\rightarrow$ **Aurora Storage**.

---

## Bài 93: Thực hành Amazon Aurora (Amazon Aurora - Hands On)
**Câu hỏi:** Các bước tạo một cụm Amazon Aurora DB Cluster trên AWS Console diễn ra như thế nào? Cần lưu ý gì về các tùy chọn cấu hình nâng cao như Storage Type (Standard vs I/O-Optimized), Serverless v2 (ACU), Local Write Forwarding, Replica Auto Scaling và quy trình xóa cụm database an toàn không phát sinh chi phí?

**Trả lời:**

### A. Tóm tắt các bước thực hành trên AWS Console (Hands-on Steps)

> 💸 **CẢNH BÁO CHI PHÍ QUAN TRỌNG:** Amazon Aurora **KHÔNG CÓ GÓI FREE TIER**. Khi tạo cụm Aurora (gồm 1 Writer + 1 Reader node), AWS sẽ tính phí theo từng giờ hoạt động (`db.t3.medium`). Nếu tự thực hành theo, bạn **BẮT BUỘC PHẢI XÓA CỤM NGAY LẬP TỨC** sau khi hoàn thành.

1.  **Khởi tạo Database Cluster:**
    *   Truy cập dịch vụ **RDS** $\rightarrow$ **Databases** $\rightarrow$ Chọn **Create database**.
    *   **Database creation method:** Chọn **Standard create** (để cấu hình đầy đủ các tham số).
    *   **Engine options:** Chọn **Amazon Aurora**.
    *   **Edition:** Chọn **Aurora MySQL-Compatible Edition** (hoặc PostgreSQL-Compatible Edition).
    *   **Engine Version:** Giữ mặc định phiên bản mới nhất (có bộ lọc tính năng: Global Database, Parallel Query, Serverless v2).
    *   **Templates:** Chọn **Production** (cho phép cấu hình Multi-AZ và replica reader).

2.  **Cấu hình Cluster & Thông tin đăng nhập:**
    *   **DB cluster identifier:** Đặt tên định danh cụm (ví dụ: `database-2`).
    *   **Master username:** `admin`.
    *   **Master password:** Nhập mật khẩu quản trị.

3.  **Cấu hình Tầng lưu trữ (Cluster Storage Configuration):**
    *   **Aurora Standard:** Lựa chọn tối ưu chi phí cho các workload thông thường (tính tiền theo GB lưu trữ + số lượng request I/O).
    *   **Aurora I/O-Optimized:** Dành cho các hệ thống có lưu lượng đọc/ghi (I/O) khổng lồ. Mức phí lưu trữ cao hơn một chút nhưng **miễn phí hoàn toàn chi phí request I/O**, giúp tiết kiệm đến 40% chi phí cho các hệ thống intensive I/O.

4.  **Cấu hình Máy chủ (Instance Configuration):**
    *   **Tùy chọn 1 - Serverless v2:** Không cần chọn loại instance cố định (vCPU/RAM). Thay vào đó, bạn chỉ định khoảng năng lực tính toán **ACU (Aurora Capacity Units)** từ mức tối thiểu (Min ACU, ví dụ: `0.5`) đến tối đa (Max ACU, ví dụ: `16`). Cụm sẽ tự động tăng giảm công suất trong tích tắc theo tải thực tế.
    *   **Tùy chọn 2 - Provisioned:** Chọn dòng instance cố định, ví dụ dòng Burstable: `db.t3.medium` (2 vCPU, 4 GiB RAM).

5.  **Cấu hình Tính sẵn sàng cao (Availability & Durability):**
    *   Tích chọn: **Create an Aurora Replica or Reader node in a different AZ**.
    *   Tùy chọn này giúp tạo ra cụm Multi-AZ gồm **1 Master (Writer node)** tại AZ A và **1 Reader node** tại AZ B, hỗ trợ cân bằng tải đọc và chuyển đổi dự phòng (failover) tức thì.

6.  **Cấu hình Mạng & Kết nối (Connectivity):**
    *   Network type: **IPv4** (hoặc Dual-stack nếu VPC hỗ trợ cả IPv6).
    *   Virtual Private Cloud (VPC): Chọn Default VPC.
    *   **Public access:** Chọn **Yes** (nếu muốn kết nối từ các tool SQL Client ngoài Internet như DBeaver, HeidiSQL).
    *   **VPC security group:** Chọn **Create new** $\rightarrow$ Đặt tên `demo-database-aurora` (AWS tự động tạo Inbound Rule mở port `3306` từ IP của bạn).

7.  **Cấu hình Nâng cao (Additional Configuration):**
    *   Database port: `3306` (chuẩn MySQL).
    *   **Local Write Forwarding (Chuyển tiếp lệnh ghi cục bộ):** Nếu kích hoạt tính năng này, ứng dụng có thể gửi lệnh ghi (`INSERT`/`UPDATE`) vào Reader Endpoint, các Reader Replicas sẽ **tự động chuyển tiếp (forward) lệnh ghi đó về Writer instance**. Tính năng này giúp ứng dụng không cần phải quản lý tách bạch 2 connection strings.
    *   Database authentication: Hỗ trợ xác thực bằng Mật khẩu (Password), bằng **AWS IAM Database Authentication**, hoặc **Kerberos**.
    *   Backup retention period: 1 ngày.
    *   **Backtrack:** Tùy chọn bật tính năng tua ngược thời gian dữ liệu.
    *   Deletion protection: Tùy chọn bảo vệ chống xóa nhầm cụm.
    *   Nhấn **Create database** và chờ vài phút để AWS cấp phát tài nguyên.

8.  **Quan sát Cụm Aurora sau khi tạo hoàn tất:**
    *   Trong bảng điều khiển RDS, bạn sẽ thấy một cụm phân cấp (**Regional Cluster**):
        *   `database-2` (Cụm cha - Cluster).
        *   `database-2-instance-1` (Writer instance - nằm ở 1 AZ).
        *   `database-2-instance-2` (Reader instance - nằm ở 1 AZ khác).
    *   Khi bấm vào cụm `database-2`, tab *Connectivity & security* hiển thị rõ **2 Endpoint chính thức**:
        *   **Writer Endpoint:** Luôn trỏ tới instance đang đóng vai trò Writer.
        *   **Reader Endpoint:** Tự động cân bằng tải kết nối tới tất cả các instance Reader.
        *   *(Mỗi instance đơn lẻ vẫn sở hữu một Instance Endpoint riêng nếu cần truy cập trực tiếp).*

9.  **Các tính năng mở rộng của Cụm Aurora:**
    *   **Thêm Reader node (Add Reader):** Bấm *Actions* $\rightarrow$ *Add reader* để thêm thủ công replica.
    *   **Thêm Auto-scaling cho Read Replicas (Add Replica Auto-scaling):**
        *   Thiết lập chính sách tự động co giãn (**Target Tracking Policy**) dựa trên chỉ số:
            *   *Average CPU utilization of Aurora Replicas* (ví dụ: duy trì CPU ở mức 60%).
            *   *Average number of connections to Aurora Replicas*.
        *   Cấu hình năng lực: Tối thiểu 1 replica, tối đa 15 replicas. Khi vượt ngưỡng 60% CPU, Aurora sẽ tự động bật thêm replica mới; khi tải giảm, Aurora sẽ tự động tắt bớt replica.
    *   **Thêm Region (Aurora Global Database):** Bấm *Actions* $\rightarrow$ *Add AWS Region* để nhân bản cơ sở dữ liệu sang một Region khác trên toàn cầu, độ trễ sao chép liên vùng dưới 1 giây và hỗ trợ DR khi toàn bộ Region chính bị sập.

---

### B. Quy trình Dọn dẹp & Xóa Cụm Aurora An toàn (Clean Up Steps)
Vì cụm Aurora chứa nhiều instance bên trong, bạn **KHÔNG THỂ** bấm xóa trực tiếp Cụm cha (Cluster) ngay từ đầu:

1.  **Bước 1 - Xóa instance Reader:**
    *   Chọn instance có vai trò **Reader** $\rightarrow$ Bấm **Actions** $\rightarrow$ **Delete**.
    *   Gõ chữ `delete me` vào ô xác nhận $\rightarrow$ Bấm **Delete**.
2.  **Bước 2 - Xóa instance Writer:**
    *   Sau khi Reader đã bị xóa hoặc đang xóa, chọn instance có vai trò **Writer** $\rightarrow$ Bấm **Actions** $\rightarrow$ **Delete**.
    *   Bỏ chọn *Create final snapshot?* (để tránh tốn tiền lưu snapshot).
    *   Tích chọn xác nhận xóa tự động backup $\rightarrow$ Gõ chữ `delete me` $\rightarrow$ Bấm **Delete**.
3.  **Bước 3 - Xóa Cụm (Cluster):**
    *   Sau khi toàn bộ các instance con đã bị xóa, AWS sẽ tự động giải phóng cụm Aurora Cluster hoặc cho phép bạn xóa nốt Cluster cha. Lúc này chi phí sẽ dừng tính hoàn toàn.

---

### C. Phân tích Kỹ thuật Chuyên sâu & Trọng tâm Đề thi

1.  **Cảnh báo Free Tier & Chi phí:**
    *   Khác với RDS thông thường có gói Free Tier 750 giờ/tháng cho `db.t3.micro`, **Amazon Aurora KHÔNG hỗ trợ Free Tier**. Mọi thao tác thực hành Aurora đều phát sinh chi phí tính theo giờ máy chủ và dung lượng lưu trữ.
2.  **Serverless v2 vs. Provisioned (Khái niệm ACU):**
    *   **ACU (Aurora Capacity Unit):** 1 ACU tương đương khoảng 2 GiB RAM kèm theo vCPU và băng thông mạng tương ứng.
    *   Serverless v2 có khả năng co giãn theo từng phần nhỏ của ACU (ví dụ từ 0.5 đến 16 ACU) chỉ trong vài mili-giây mà không làm đứt kết nối của client, cực kỳ lý tưởng cho các workload biến động đột ngột hoặc môi trường Dev/Test ít dùng liên tục.
3.  **Aurora Standard vs. Aurora I/O-Optimized:**
    *   *Standard:* Phí instance thấp + Phí lưu trữ + Phí request đọc/ghi I/O (tính trên mỗi 1 triệu request).
    *   *I/O-Optimized:* Phí instance và lưu trữ cao hơn một chút, nhưng **0 ĐỒNG phí I/O requests**. Nếu hệ thống có tỷ lệ I/O cao (chi phí I/O vượt quá 25% tổng hóa đơn database), chọn I/O-Optimized sẽ tiết kiệm tiền hơn rất nhiều.
4.  **Local Write Forwarding:**
    *   Giải quyết bài toán "kết nối đơn giản": Cho phép ứng dụng cứ trỏ toàn bộ traffic vào Reader Endpoint. Khi gặp lệnh ghi, Reader Replica sẽ tự động forward về Writer instance mà không báo lỗi `Read-only database`.
5.  **Quy tắc xóa cụm trong kỳ thi:**
    *   Để xóa một cụm Aurora: **Xóa các Reader instance trước $\rightarrow$ Xóa Writer instance sau $\rightarrow$ Cụm tự động giải phóng**.

---

## Bài 94: Các khái niệm nâng cao của Amazon Aurora (Amazon Aurora - Advanced Concepts)
**Câu hỏi:** Các tính năng nâng cao của Amazon Aurora bao gồm những gì? Khi nào nên sử dụng Custom Endpoints, Aurora Serverless, Aurora Global Database, Aurora Machine Learning và Babelfish for Aurora PostgreSQL? Cơ chế kỹ thuật và ứng dụng thực tế của từng tính năng này diễn ra như thế nào?

**Trả lời:**

### A. Slide bài giảng
````carousel
![Slide: Aurora Custom Endpoints](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1789661358159.png)
<!-- slide -->
![Slide: Aurora Serverless](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1789661372522.png)
<!-- slide -->
![Slide: Global Aurora](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1789661378938.png)
<!-- slide -->
![Slide: Aurora Machine Learning](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1789661394445.png)
<!-- slide -->
![Slide: Babelfish for Aurora PostgreSQL](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1789661443076.png)
````

### B. Cơ chế Tự động co giãn bản sao (Replica Auto-Scaling)
*   **Vấn đề:** Khi ứng dụng có lưu lượng truy vấn đọc tăng đột biến, chỉ số tải CPU trên các Read Replica hiện tại sẽ tăng vọt, nguy cơ gây chậm trễ thời gian phản hồi API.
*   **Cơ chế hoạt động:**
    1.  Aurora giám sát chỉ số tải trung bình (ví dụ: CPU Utilization vượt ngưỡng 60%).
    2.  Chính sách **Replica Auto-Scaling** tự động kích hoạt khởi tạo thêm các Aurora Read Replica mới.
    3.  **Reader Endpoint** sẽ **tự động mở rộng danh sách cân bằng tải** để bao quát cả các replica mới sinh ra.
    4.  Lưu lượng đọc từ client lập tức được phân bổ đều ra nhiều replica hơn, giúp kéo mức CPU tổng thể trở về ngưỡng an toàn.

---

### C. Điểm kết nối tùy chỉnh (Custom Endpoints)
*   **Bài toán thực tế:**
    *   Trong một cụm Aurora lớn, bạn có thể triển khai các loại máy chủ replica với cấu hình phần cứng khác nhau:
        *   Nhóm replica nhỏ (`db.r3.large`): Dành cho các truy vấn Web/API thông thường (tải nhẹ, số lượng lớn).
        *   Nhóm replica khủng (`db.r5.2xlarge`): Nhiều RAM và CPU chuyên dụng để xử lý các câu truy vấn phức tạp, quét bảng lớn phục vụ phân tích dữ liệu (**Analytical Queries / Business Intelligence / Reporting**).
    *   *Rủi ro nếu dùng Reader Endpoint chung:* Reader Endpoint sẽ chia đều ngẫu nhiên các truy vấn. Nếu một truy vấn báo cáo nặng rơi vào máy nhỏ `db.r3.large`, máy đó sẽ bị treo đơ, làm ảnh hưởng dây chuyền đến người dùng web!
*   **Giải pháp - Custom Endpoints:**
    *   Cho phép bạn gom một nhóm nhỏ (**subset**) các instance Aurora cụ thể thành một Endpoint riêng với địa chỉ DNS riêng biệt.
    *   Ví dụ: Định nghĩa một `Custom Endpoint` chỉ trỏ tới 2 máy `db.r5.2xlarge` và cấu hình ứng dụng Báo cáo/BI kết nối trực tiếp vào endpoint này.
    *   > ⚠️ **Lưu ý thực tế & đề thi:** Sau khi đã thiết lập các Custom Endpoints cho các mục đích chuyên biệt, **Reader Endpoint mặc định thường sẽ không được sử dụng nữa** nhằm tránh việc phân phối nhầm tải.

---

### D. Cơ sở dữ liệu không máy chủ (Aurora Serverless)
*   **Bản chất:** Cung cấp khả năng tự động khởi tạo máy chủ database và tự động co giãn công suất (CPU/RAM) hoàn toàn dựa trên mức độ sử dụng thực tế của ứng dụng.
*   **Các loại Workload lý tưởng (Trọng tâm đề thi):**
    1.  Tải không thường xuyên (**Infrequent**).
    2.  Tải ngắt quãng (**Intermittent**).
    3.  Tải khó dự đoán trước (**Unpredictable workloads**).
    *   *Ví dụ thực tế:* Các cổng thông tin nội bộ doanh nghiệp (chỉ truy cập giờ hành chính, ban đêm và cuối tuần không ai dùng), các ứng dụng mới ra mắt chưa rõ lượng traffic, hoặc các tác vụ chạy định kỳ batch processing.
*   **Lợi ích kinh tế:**
    *   Hoàn toàn **không cần lập kế hoạch dung lượng (No capacity planning)**.
    *   Tính tiền theo từng giây sử dụng (**Pay per second**). Khi không có truy vấn, hệ thống có thể tạm dừng (pause/scale down), giúp tiết kiệm chi phí tối đa.
*   **Kiến trúc ngầm:** Client kết nối vào một cụm điều hướng proxy (**Proxy Fleet**) do Aurora quản lý $\rightarrow$ Proxy Fleet giữ kết nối của client và điều phối thông minh tới các container/instance Aurora co giãn phía sau mà không làm đứt session của ứng dụng.

---

### E. Cơ sở dữ liệu toàn cầu (Global Aurora)
Để triển khai cơ sở dữ liệu đa khu vực (Multi-Region), bạn có 2 lựa chọn:

1.  **Aurora Cross Region Read Replicas:**
    *   Mô hình truyền thống: Khởi tạo một Read Replica đặt tại Region khác.
    *   Hữu ích cho mục đích dự phòng thảm họa (Disaster Recovery) cơ bản, dễ thiết lập nhưng độ trễ sao chép cao hơn.
2.  **Aurora Global Database (Giải pháp Chuẩn mực khuyến nghị - Recommended):**
    *   **Kiến trúc:**
        *   **1 Primary Region (Chính):** Tiếp nhận toàn bộ tác vụ Đọc và Ghi (Read/Write).
        *   Tối đa **10 Secondary Regions (Phụ):** Chỉ phục vụ Đọc (Read-only), giúp người dùng ở khắp nơi trên thế giới truy vấn dữ liệu cục bộ với độ trễ cực thấp.
        *   Tối đa **16 Read Replicas** trên mỗi Secondary Region.
    *   **2 Chỉ số VÀNG bắt buộc phải nhớ cho kỳ thi SAA-C03:**
        *   **Độ trễ sao chép liên vùng (Replication Lag): DƯỚI 1 GIÂY (< 1 second)** nhờ cơ chế sao chép ngầm trực tiếp ở tầng Storage thông qua hạ tầng cáp quang riêng của AWS.
        *   **Thời gian phục hồi thảm họa (RTO - Recovery Time Objective): DƯỚI 1 PHÚT (< 1 minute)**. Khi Primary Region gặp sự cố thảm họa, bạn có thể thăng cấp một Secondary Region lên làm cụm Read/Write chính thức chỉ trong chưa đầy 60 giây.

---

### F. Tích hợp Trí tuệ nhân tạo (Aurora Machine Learning)
*   **Khái niệm:** Cho phép bổ sung các tính năng dự đoán dựa trên Machine Learning vào ứng dụng thông qua **câu lệnh truy vấn SQL tiêu chuẩn** (`SELECT`).
*   **Đặc điểm:**
    *   Lập trình viên **không cần có kinh nghiệm về Machine Learning** (không cần viết Python, PyTorch hay dựng API inference phức tạp).
    *   Tích hợp an toàn, tối ưu và bảo mật giữa Aurora và các dịch vụ AI/ML của AWS.
*   **2 Dịch vụ ML được hỗ trợ tích hợp:**
    1.  **Amazon SageMaker:** Cho phép kết nối tới bất kỳ mô hình ML tùy biến nào (dùng cho các bài toán: Phát hiện gian lận - *Fraud detection*, Gợi ý sản phẩm - *Product recommendations*, Nhắm mục tiêu quảng cáo - *Ads targeting*).
    2.  **Amazon Comprehend:** Sử dụng trực tiếp để phân tích sắc thái cảm xúc khách hàng (**Sentiment Analysis**) từ các bình luận/feedback lưu trong database.
*   **Luồng hoạt động thực tế:**
    *   Ứng dụng gửi câu lệnh SQL trực tiếp:
        ```sql
        SELECT customer_id, sage_maker_recommend_product(customer_profile) 
        FROM Customers;
        ```
    *   Aurora tự động trích xuất dữ liệu profile/lịch sử mua sắm và gọi ngầm sang dịch vụ ML (SageMaker / Comprehend).
    *   Dịch vụ ML trả về kết quả dự đoán trực tiếp cho Aurora (ví dụ: *"khách hàng nên mua áo đỏ, quần xanh"*).
    *   Aurora đóng gói kết quả này và trả về cho ứng dụng dưới dạng một bảng kết quả SQL thông thường.


---

### G. Babelfish cho Aurora PostgreSQL (Babelfish for Aurora PostgreSQL)
*   **Bối cảnh nhức nhối trong doanh nghiệp:**
    *   Nhiều tổ chức đang chạy các ứng dụng Enterprise viết bằng C#, .NET trên cơ sở dữ liệu **Microsoft SQL Server**.
    *   Họ muốn chuyển đổi lên AWS Cloud và chuyển sang hệ CSDL mã nguồn mở như **PostgreSQL trên Aurora** để tiết kiệm chi phí bản quyền khổng lồ của Microsoft.
    *   *Rào cản lớn nhất:* Toàn bộ code ứng dụng đang dùng **SQL Server Client Driver** và viết bằng ngôn ngữ truy vấn **T-SQL (Transact-SQL)** của Microsoft. Việc viết lại toàn bộ mã nguồn sang ngôn ngữ của PostgreSQL (`PL/pgSQL`) và đổi driver có thể mất hàng năm trời và tốn hàng triệu USD.
*   **Giải pháp đột phá - Babelfish:**
    *   Babelfish là một lớp dịch thuật giao thức (Translation Layer) được tích hợp sẵn bên trong **Aurora PostgreSQL**.
    *   Nó cho phép Aurora PostgreSQL **"nghe hiểu" và thực thi trực tiếp các câu lệnh T-SQL** gửi từ SQL Server Client Driver.
    *   **Lợi ích tối thượng:** Ứng dụng .NET / SQL Server **hầu như KHÔNG CẦN thay đổi mã nguồn (Little to no code changes)**. Ứng dụng vẫn dùng nguyên driver cũ của Microsoft, chỉ cần đổi chuỗi kết nối (Connection String) sang địa chỉ của Aurora PostgreSQL có bật Babelfish!
    *   *Quy trình di chuyển dữ liệu:* Sử dụng **AWS SCT** (Schema Conversion Tool) và **AWS DMS** (Database Migration Service) để di dời dữ liệu từ SQL Server sang Aurora PostgreSQL, sau đó kích hoạt Babelfish để ứng dụng tiếp tục hoạt động trơn tru.

---

### H. Bảng tổng kết các tính năng nâng cao của Aurora

| Tính năng nâng cao | Mục đích cốt lõi | Dấu hiệu nhận diện trong đề thi (Exam Clues) |
| :--- | :--- | :--- |
| **Replica Auto-Scaling** | Tự động tăng giảm số lượng replica theo tải CPU | Traffic đọc biến động mạnh, cần tự mở rộng Reader Endpoint |
| **Custom Endpoints** | Phân luồng truy vấn cho nhóm máy chủ chuyên biệt | Chạy truy vấn phân tích (Analytics/BI) trên các replica cấu hình lớn |
| **Aurora Serverless** | Tự động co giãn theo giây, không cần quản lý máy chủ | Tải không thường xuyên (Infrequent), ngắt quãng (Intermittent), khó đoán |
| **Aurora Global Database**| Triển khai toàn cầu: 1 Primary + 10 Secondary | Replication lag **< 1s**, RTO phục hồi thảm họa **< 1 phút** |
| **Aurora Machine Learning**| Dự đoán ML trực tiếp từ câu lệnh SQL | Tích hợp **SageMaker** (recommendations) hoặc **Comprehend** (sentiment) |
| **Babelfish for Aurora** | Chạy app SQL Server trên Aurora PostgreSQL | Di chuyển từ **SQL Server sang PostgreSQL** với **rất ít hoặc không đổi code** |

---

### I. Góc nhìn Lập trình viên (.NET / Next.js) & DevOps / Network
*   **Đối với Lập trình viên C# / .NET:**
    *   **Babelfish** là tính năng mang tính cách mạng cho hệ sinh thái .NET. Nếu công ty bạn đang gánh chi phí bản quyền Microsoft SQL Server đắt đỏ, Babelfish cho phép bạn chuyển toàn bộ database sang Aurora PostgreSQL mà không phải refactor lại hàng nghìn class Entity Framework hay stored procedure viết bằng T-SQL.
    *   **Custom Endpoints:** Trong kiến trúc Microservices hoặc ứng dụng ASP.NET lớn, bạn có thể tạo một `ReportingDbContext` kết nối thẳng vào Custom Endpoint của dàn máy phân tích, tách biệt hoàn toàn với `WebDbContext` phục vụ người dùng cuối.
*   **Đối với DevOps & Network:**
    *   **Aurora Serverless:** Với cụm Proxy Fleet đứng trước, ứng dụng không bao giờ bị nghẽn connection khi database đang scale up (khác hoàn toàn với việc scale máy chủ EC2 truyền thống phải chịu downtime).
    *   **Aurora Global Database:** Kết hợp cực tốt với **AWS Route 53** (Latency-based Routing) để tự động điều hướng người dùng tại Châu Âu tới Secondary Region ở Frankfurt, người dùng tại Châu Á tới Tokyo, tối ưu hóa triệt để trải nghiệm người dùng toàn cầu.

---

### J. Mẹo thi & Bẫy trắc nghiệm SAA-C03 (Exam Keywords & Traps)
> ⚠️ **Các mẫu câu hỏi kinh điển trong đề thi SAA-C03:**
> 1.  **"Tách biệt tải phân tích dữ liệu nặng khỏi lưu lượng truy vấn web thông thường trên cụm Aurora"** $\rightarrow$ Chọn ngay **Custom Endpoints**.
> 2.  **"Ứng dụng có khối lượng truy cập không thường xuyên, ngắt quãng, không thể đoán trước, muốn tối ưu chi phí tối đa"** $\rightarrow$ Chọn **Aurora Serverless**.
> 3.  **"Cơ sở dữ liệu đa vùng với độ trễ sao chép dưới 1 giây và RTO phục hồi thảm họa dưới 1 phút"** $\rightarrow$ Chọn **Aurora Global Database** (Tuyệt đối KHÔNG chọn Cross-Region Read Replicas vì replica thường có độ trễ cao hơn và RTO lâu hơn).
> 4.  **"Muốn bổ sung khả năng phát hiện gian lận hoặc gợi ý sản phẩm ngay trong câu lệnh SQL mà không cần viết code ML"** $\rightarrow$ Chọn **Aurora Machine Learning** (kết hợp với **SageMaker** hoặc **Comprehend**).
> 5.  **"Di chuyển ứng dụng từ Microsoft SQL Server sang cơ sở dữ liệu mã nguồn mở trên AWS với chi phí sửa đổi code ứng dụng tối thiểu"** $\rightarrow$ Chọn **Babelfish for Aurora PostgreSQL**.

---

## Bài 95: Sao lưu, Khôi phục và Nhân bản RDS & Aurora (RDS & Aurora - Backup and Monitoring)
**Câu hỏi:** Cơ chế sao lưu (Automated Backups vs Manual DB Snapshots) trên Amazon RDS và Aurora khác nhau như thế nào? Quy trình khôi phục dữ liệu (Point-in-Time Recovery, restore từ S3 với Percona XtraBackup) diễn ra ra sao? Tính năng Aurora Database Cloning hoạt động theo cơ chế Copy-on-Write mang lại lợi ích gì cho môi trường Staging/Test và mẹo tiết kiệm chi phí database trong đề thi là gì?

**Trả lời:**

### A. Slide bài giảng
````carousel
![Slide: RDS Backups](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1789662799494.png)
<!-- slide -->
![Slide: Aurora Backups](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1789662812515.png)
<!-- slide -->
![Slide: RDS & Aurora Restore options](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1789662820026.png)
<!-- slide -->
![Slide: Aurora Database Cloning](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1789662838589.png)
````

### B. Cơ chế Sao lưu trên Amazon RDS (RDS Backups)
RDS cung cấp 2 hình thức sao lưu chính:

1.  **Sao lưu tự động (Automated Backups):**
    *   **Full Backup hàng ngày:** RDS tự động chụp bản sao lưu toàn bộ database mỗi ngày trong khung giờ sao lưu (**Backup Window**) được chỉ định.
    *   **Sao lưu Transaction Logs:** Nhật ký giao dịch (Transaction Logs) được tự động sao lưu định kỳ **mỗi 5 phút một lần**.
    *   **Khôi phục theo thời điểm (Point-in-Time Recovery - PITR):** Cho phép bạn khôi phục dữ liệu về bất kỳ giây cụ thể nào trong quá khứ, từ bản backup cũ nhất cho đến **5 phút trước thời điểm hiện tại**.
    *   **Thời gian lưu trữ (Retention Period):** Cấu hình từ **1 đến 35 ngày**.
    *   **Vô hiệu hóa sao lưu:** Có thể tắt tính năng sao lưu tự động bằng cách đặt retention về **0 ngày** (nhưng không khuyến nghị cho Production).

2.  **Chụp Snapshot thủ công (Manual DB Snapshots):**
    *   Do quản trị viên hoặc lập trình viên chủ động bấm nút kích hoạt (trigger) bằng tay.
    *   **Đặc điểm quan trọng:** Bản snapshot thủ công được **lưu trữ vô thời hạn (as long as you want)**, không bao giờ bị AWS tự động xóa kể cả khi bạn xóa database gốc.

3.  **Mẹo tối ưu chi phí cực hay trong đề thi (Cost-Saving Trick):**
    *   *Vấn đề:* Khi bạn `Stop` một database RDS (ví dụ cho môi trường Dev/Test), bạn không bị tính tiền Compute (vCPU/RAM), **nhưng vẫn phải trả tiền lưu trữ ổ đĩa EBS hàng tháng**. Ngoài ra, RDS có cơ chế tự động bật lại sau 7 ngày nếu bị stop liên tục.
    *   *Chiến lược thông minh:* Nếu bạn có một database chỉ dùng **vài giờ mỗi tháng** (hoặc dự án tạm hoãn vài tháng):
        1.  Chụp một bản **Manual DB Snapshot**.
        2.  **Xóa hẳn (Delete) database RDS đó đi**.
        3.  Chi phí lưu trữ bản Snapshot trên Amazon S3 rẻ hơn rất nhiều so với việc duy trì ổ đĩa EBS của database.
        4.  Khi nào cần dùng lại, chỉ việc **Restore từ Snapshot** ra một database mới.

---

### C. Cơ chế Sao lưu trên Amazon Aurora (Aurora Backups)
*   **Sao lưu tự động (Automated Backups):**
    *   Thời gian lưu trữ cấu hình từ **1 đến 35 ngày**.
    *   Hỗ trợ Point-in-Time Recovery về bất kỳ thời điểm nào trong khung thời gian lưu trữ.
    *   > ⚠️ **Khác biệt cốt lõi với RDS:** Tính năng sao lưu tự động trên Aurora **KHÔNG THỂ BỊ VÔ HIỆU HÓA (Cannot be disabled)**! (Luôn luôn bật để bảo vệ cụm lưu trữ phân tán).
*   **Chụp Snapshot thủ công (Manual DB Snapshots):** Tương tự RDS, người dùng tự kích hoạt và được lưu trữ vô thời hạn.

---

### D. Các tùy chọn Khôi phục dữ liệu (RDS & Aurora Restore Options)

1.  **Quy tắc bất biến khi Restore:**
    *   Bất kỳ thao tác khôi phục nào (từ Automated Backup hay Manual Snapshot) đều **TẠO RA MỘT DATABASE MỚI (Creates a NEW database)** với một địa chỉ DNS Endpoint hoàn toàn mới.
    *   Hệ thống không bao giờ ghi đè trực tiếp lên database hiện tại.

2.  **Khôi phục Database từ On-premises lên AWS qua Amazon S3:**
    *   **Khôi phục thành RDS MySQL:**
        1.  Tạo bản sao lưu (backup dump) từ database MySQL ở On-premises.
        2.  Tải file backup lên **Amazon S3** (dịch vụ lưu trữ đối tượng của AWS).
        3.  Sử dụng tính năng khôi phục của RDS để tạo ra một **RDS MySQL instance mới** từ file trên S3.
    *   **Khôi phục thành Aurora MySQL Cluster:**
        1.  Tạo bản sao lưu database MySQL On-premises **bắt buộc phải sử dụng công cụ chuyên dụng: Percona XtraBackup**.
        2.  Tải file backup của Percona XtraBackup lên **Amazon S3**.
        3.  Khôi phục từ S3 để tạo ra một cụm **Aurora MySQL Cluster mới**.
        4.  > ⚠️ **Từ khóa đề thi:** Nhắc tới việc restore/backup từ on-premises MySQL lên Aurora qua S3 $\rightarrow$ Phải tìm từ khóa **Percona XtraBackup**.

---

### E. Nhân bản Cơ sở dữ liệu Aurora (Aurora Database Cloning)
*   **Bối cảnh sử dụng:** Bạn có một cụm Database Production đang chạy trên Aurora và bạn muốn dựng một môi trường **Staging / QA** với bộ dữ liệu y hệt Production để kiểm thử ứng dụng, chạy thử nghiệm tải hoặc test migration, nhưng không được phép làm ảnh hưởng tới database thật.
*   **So sánh với phương pháp Snapshot & Restore truyền thống:**
    *   Snapshot & Restore: Mất nhiều thời gian (chờ sao chép hàng TB dữ liệu từ S3 ra ổ đĩa mới), tốn gấp đôi chi phí lưu trữ ngay từ đầu.
    *   **Aurora Cloning:** Nhanh vượt trội (chỉ mất vài phút) và cực kỳ tiết kiệm chi phí.
*   **Cơ chế hoạt động Copy-on-Write (CoW):**
    1.  **Giai đoạn khởi tạo:** Khi tạo Clone, cụm Staging mới **chia sẻ chung 100% tầng lưu trữ (Shared Data Volume)** với cụm Production gốc. Không hề có thao tác sao chép dữ liệu vật lý nào diễn ra $\rightarrow$ Quá trình tạo cụm Staging hoàn thành gần như tức thì và ban đầu **hoàn toàn 0 đồng phí lưu trữ phát sinh**.
    2.  **Giai đoạn ghi dữ liệu (Copy-on-Write):** Khi môi trường Staging chạy test (thêm dữ liệu giả lập, sửa bảng, xóa dữ liệu) hoặc cụm Production phát sinh giao dịch mới, Aurora mới tự động cấp phát thêm các block ổ đĩa mới và tách riêng dữ liệu bị sửa đổi ra.
    3.  **Lợi ích:** Hai môi trường hoàn toàn độc lập, các thao tác phá hủy trên Staging không hề ảnh hưởng đến Production, đồng thời chi phí lưu trữ chỉ tính trên phần dữ liệu thực sự bị thay đổi (Delta).

---

### F. Bảng so sánh: RDS Backup vs. Aurora Backup vs. Aurora Cloning

| Tiêu chí | RDS Backups | Aurora Backups | Aurora Database Cloning |
| :--- | :--- | :--- | :--- |
| **Lưu trữ tự động** | 1 - 35 ngày | 1 - 35 ngày | Không áp dụng (là cụm độc lập) |
| **Tắt sao lưu tự động** | ✅ **Có thể tắt** (đặt = 0) | ❌ **KHÔNG thể tắt** (Luôn bật) | Không áp dụng |
| **Độ trễ Transaction Log** | Mỗi 5 phút | Liên tục ở tầng Storage | Kế thừa từ cụm gốc |
| **Kết quả khi Restore** | Tạo ra **DB Instance mới** | Tạo ra **DB Cluster mới** | Tạo ra **DB Cluster mới** |
| **Thời gian tạo bản sao** | Lâu (tùy dung lượng Snapshot) | Lâu (tùy dung lượng Snapshot) | **Siêu nhanh (Vài phút nhờ Copy-on-Write)** |
| **Tác động tới Production** | Tác động I/O nhẹ lúc snapshot | Không ảnh hưởng Compute | **Hoàn toàn KHÔNG ảnh hưởng** |
| **Chi phí lưu trữ bản sao** | Tính đầy đủ dung lượng đĩa mới | Tính đầy đủ dung lượng đĩa mới | **Chỉ tính dung lượng phần dữ liệu bị sửa đổi (Delta)** |

---

### G. Góc nhìn Lập trình viên (.NET / Next.js) & DevOps / Network
*   **Ứng dụng cho DevOps & Pipeline CI/CD:**
    *   Tính năng **Aurora Cloning** là công cụ mơ ước cho các kỹ sư DevOps. Trong quy trình CI/CD, trước khi deploy bản cập nhật lớn của ứng dụng ASP.NET Core hoặc Next.js lên Production:
        *   Pipeline tự động gọi AWS CLI tạo một bản Aurora Clone từ Production.
        *   Chạy các bài kiểm thử tự động (Integration Tests, Data Migration, Load Test) trên bản Clone.
        *   Sau khi test xong, Pipeline xóa cụm Clone. Toàn bộ quy trình chỉ tốn vài cent tiền lưu trữ và vài phút thực thi!
*   **Xử lý Endpoint trong Ứng dụng:**
    *   Vì thao tác Restore (từ Snapshot hoặc Backup) luôn sinh ra một instance/cluster mới với địa chỉ DNS Endpoint mới, lập trình viên không nên hard-code connection string trong code. Thay vào đó, hãy lưu Connection String trong **AWS Systems Manager Parameter Store** hoặc **AWS Secrets Manager** để ứng dụng có thể cập nhật linh hoạt mà không cần build lại code.

---

### H. Mẹo thi & Bẫy trắc nghiệm SAA-C03 (Exam Keywords & Traps)
> ⚠️ **Các điểm bẫy cần khắc cốt ghi tâm khi làm bài thi:**
> 1.  **"Có thể tắt Automated Backup trên Aurora không?"** $\rightarrow$ **KHÔNG**. Trên RDS thì tắt được (đặt = 0), còn trên Aurora thì không thể tắt.
> 2.  **"Restore một snapshot có ghi đè lên database hiện tại không?"** $\rightarrow$ **KHÔNG BAO GIỜ**. Quá trình restore luôn tạo ra một Database MỚI.
> 3.  **"Tối ưu chi phí cho database chỉ dùng vài giờ mỗi tháng"** $\rightarrow$ Chụp **Manual Snapshot**, sau đó **Xóa database** đi. Khi nào dùng thì Restore lại. (Vì Stop database vẫn bị tính tiền lưu trữ EBS).
> 4.  **"Di chuyển / Khôi phục database MySQL từ on-premises lên Aurora MySQL qua S3"** $\rightarrow$ Chọn phương án có sử dụng công cụ **Percona XtraBackup**.
> 5.  **"Tạo môi trường Staging/Test từ database Aurora Production nhanh nhất, rẻ nhất và không ảnh hưởng hiệu năng Production"** $\rightarrow$ Chọn ngay **Aurora Database Cloning** (nhờ cơ chế Copy-on-Write).

---

## Bài 96: Bảo mật trong RDS và Aurora (RDS & Aurora Security)
**Câu hỏi:** Làm thế nào để bảo mật toàn diện cho Amazon RDS và Aurora? Cơ chế mã hóa dữ liệu tĩnh (At-rest Encryption với KMS) và quy trình mã hóa database chưa mã hóa diễn ra như thế nào? Sự khác biệt giữa xác thực truyền thống và xác thực IAM (IAM Database Authentication)? Các tầng bảo vệ mạng (Security Groups) và quản lý nhật ký kiểm toán (Audit Logs sang CloudWatch Logs) hoạt động ra sao?

**Trả lời:**

### A. Slide bài giảng
````carousel
![Slide: RDS & Aurora Security](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1790697909228.png)
````

### B. Mã hóa Dữ liệu khi Nghỉ (At-rest Encryption)
*   **Công nghệ sử dụng:** Toàn bộ dữ liệu lưu trữ trên ổ đĩa của RDS và Aurora được mã hóa bằng chuẩn mã hóa cấp doanh nghiệp AES-256 thông qua **AWS KMS (Key Management Service)**.
*   **Phạm vi mã hóa:** Khi bật mã hóa, AWS sẽ mã hóa toàn bộ:
    *   Dữ liệu trong bảng và các chỉ mục (indexes).
    *   Các bản sao lưu tự động (Automated Backups).
    *   Các bản chụp nhanh thủ công (Manual Snapshots).
    *   Nhật ký giao dịch (Transaction Logs) và không gian lưu trữ tạm thời.
*   **Thời điểm thiết lập:**
    *   > ⚠️ **Bắt buộc:** Phải được chỉ định **tại thời điểm khởi tạo database (Launch Time)**. Bạn không thể bấm sửa (Modify) để bật/tắt mã hóa trực tiếp trên một instance đang hoạt động.
*   **Quy tắc sao chép (Replication Rule):**
    *   Nếu Master Database **không được mã hóa** $\rightarrow$ Các Read Replicas **KHÔNG THỂ được mã hóa**.
    *   Muốn Read Replica được mã hóa thì Master Database bắt buộc phải được mã hóa ngay từ đầu.
*   **Quy trình 3 bước mã hóa một Database chưa được mã hóa (Cực kỳ hay hỏi trong đề thi):**
    1.  Chụp một bản **Manual DB Snapshot** từ database chưa được mã hóa.
    2.  Thực hiện thao tác **Copy Snapshot**, tại màn hình copy tích chọn **Enable Encryption** và chọn khóa KMS Key mong muốn. Thao tác này sẽ tạo ra một bản Snapshot mới đã được mã hóa an toàn.
    3.  Thực hiện **Restore** bản Snapshot đã mã hóa đó thành một **Database Instance/Cluster hoàn toàn mới**.
    4.  Cập nhật ứng dụng trỏ sang database mới và xóa bỏ database cũ chưa mã hóa.

---

### C. Mã hóa Dữ liệu trên Đường truyền (In-flight / In-transit Encryption)
*   **Chuẩn bảo mật TLS/SSL:** Tất cả các database engine trên RDS và Aurora đều được tích hợp sẵn khả năng mã hóa dữ liệu truyền tải qua giao thức TLS (**TLS-ready by default**).
*   **Chứng chỉ tin cậy:** Phía máy khách (Client / Application) khi kết nối cần sử dụng **AWS TLS Root Certificates** (chứng chỉ CA gốc được AWS cung cấp công khai) để xác thực danh tính của máy chủ database, phòng tránh các cuộc tấn công nghe lén hoặc giả mạo (Man-in-the-Middle).
*   **Bắt buộc sử dụng SSL (Enforce SSL):**
    *   Bạn có thể cấu hình ép buộc tất cả các client bắt buộc phải dùng kết nối SSL, từ chối mọi kết nối không mã hóa thông qua việc điều chỉnh tham số trong **DB Parameter Group** (ví dụ: tham số `rds.force_ssl = 1` trong PostgreSQL) hoặc cấp quyền user MySQL với mệnh đề `REQUIRE SSL`.

---

### D. Xác thực Cơ sở dữ liệu bằng AWS IAM (IAM Database Authentication)
Thay vì sử dụng phương thức truyền thống là tạo tài khoản với username/password cứng bên trong database, RDS và Aurora hỗ trợ xác thực trực tiếp qua **AWS IAM**:

*   **Đối tượng áp dụng:** Hỗ trợ cho các engine phổ biến như **MySQL** và **PostgreSQL**.
*   **Cách thức hoạt động:**
    1.  Lập trình viên tạo một tài khoản database được cấu hình xác thực qua plugin AWS IAM (ví dụ: `CREATE USER 'db_user' IDENTIFIED WITH AWSAuthenticationPlugin;`).
    2.  Gán quyền cho **IAM Role** của máy chủ EC2, container ECS, hoặc hàm AWS Lambda cho phép kết nối tới database thông qua IAM Policy (`rds-db:connect`).
    3.  Khi ứng dụng cần kết nối, ứng dụng gọi AWS SDK để sinh ra một chuỗi **Authentication Token tạm thời** (do AWS STS phát hành). Token này đóng vai trò như mật khẩu đăng nhập và chỉ có **hiệu lực tối đa trong 15 phút**.
*   **Lợi ích bảo mật vượt trội:**
    *   **Loại bỏ hoàn toàn mật khẩu lưu cứng (No hardcoded credentials):** Lập trình viên không cần lưu password trong file `appsettings.json`, biến môi trường hay code.
    *   **Không lo việc xoay vòng mật khẩu (Password Rotation):** Token tự động hết hạn sau 15 phút, giảm thiểu tối đa rủi ro rò rỉ credential.
    *   Quản lý tập trung mọi quyền truy cập database thông qua hạ tầng IAM của AWS.

---

### E. Kiểm soát Truy cập Mạng (Network Security với Security Groups)
*   **Tường lửa tầng 4 (Layer 4 Stateful Firewall):** Bạn kiểm soát việc ai được phép gửi gói tin tới database thông qua **VPC Security Groups**.
*   Có thể cấu hình cho phép (Allow) theo: Port (3306 cho MySQL, 5432 cho Postgres, 1433 cho SQL Server...), Dải mạng IP (CIDR), hoặc tham chiếu trực tiếp theo **Security Group ID**.
*   **Mô hình Kiến trúc Chuẩn mực Enterprise (3-Tier Security Group Chaining):**
    *   Database **BẮT BUỘC phải nằm trong Private Subnet** (không được cấp Public IP).
    *   Cấu hình Inbound Rule của Security Group Database: **CHỈ cho phép nguồn truy cập xuất phát từ Security Group của Web/App Server** (Source: `sg-web-backend`).
    *   Tuyệt đối **KHÔNG BAO GIỜ mở `0.0.0.0/0`** hoặc mở thẳng IP cá nhân trên môi trường Production.

---

### F. Quyền truy cập Máy chủ (No SSH Access)
*   Vì RDS và Aurora là các dịch vụ được AWS quản lý hoàn toàn (**Fully Managed Services**), AWS chịu trách nhiệm vá lỗi hệ điều hành và bảo trì máy chủ ngầm.
*   Người dùng **KHÔNG CÓ QUYỀN TRUY CẬP SSH HOẶC RDP** vào hệ điều hành bên dưới của RDS/Aurora.
*   > ⚠️ **Ngoại lệ duy nhất:** **RDS Custom** (cho Oracle và Microsoft SQL Server) là dịch vụ duy nhất cấp quyền SSH / SSM Session Manager vào máy chủ (như đã học ở Bài 91).

---

### G. Nhật ký Kiểm toán (Audit Logs & CloudWatch Logs)
*   **Mục đích:** Giúp doanh nghiệp ghi lại toàn bộ lịch sử truy vấn SQL, các lần đăng nhập thành công/thất bại, các thay đổi về cấu trúc bảng (DDL) phục vụ việc điều tra an ninh và tuân thủ các chứng chỉ bảo mật quốc tế (PCI-DSS, ISO 27001, HIPAA).
*   **Vấn đề:** Mặc định, file log được lưu tạm thời trên ổ cứng của database instance và sẽ bị tự động xóa/ghi đè sau một khoảng thời gian ngắn để tránh đầy đĩa.
*   **Giải pháp chuẩn AWS:** Kích hoạt tính năng **Export Audit Logs sang Amazon CloudWatch Logs**:
    *   Nhật ký được đẩy liên tục lên CloudWatch Logs để lưu trữ tập trung, an toàn và không lo bị mất.
    *   Có thể thiết lập **CloudWatch Alarms** cảnh báo ngay lập tức vào Telegram/Slack/Email khi có kẻ xấu cố tình brute-force mật khẩu database.
    *   Thiết lập chính sách lưu trữ (Retention) từ vài tháng đến trọn đời, hoặc đẩy tiếp sang **Amazon S3 Glacier** để tối ưu chi phí lưu trữ lâu dài.

---

### H. Bảng tổng kết: 6 Trụ cột Bảo mật RDS & Aurora

| Trụ cột bảo mật | Cơ chế thực thi | Lưu ý cốt lõi |
| :--- | :--- | :--- |
| **Mã hóa khi nghỉ (At-rest)** | AWS KMS (AES-256) | Phải bật lúc tạo mới; Muốn mã hóa DB cũ phải Snapshot $\rightarrow$ Copy Encrypt $\rightarrow$ Restore |
| **Mã hóa đường truyền (In-flight)**| TLS / SSL | TLS-ready mặc định; Client dùng AWS Root CA; Ép buộc qua `rds.force_ssl` |
| **Xác thực danh tính (Authentication)**| Username/Pass & **IAM Auth** | IAM Auth dùng Token tạm thời (15 phút), không lo lộ mật khẩu cứng |
| **Kiểm soát mạng (Network)** | Security Groups & Private Subnet | Không gán Public IP; Chỉ mở Inbound từ Security Group của App Server |
| **Truy cập hệ điều hành (OS)** | **Không hỗ trợ SSH/RDP** | AWS quản lý 100% OS (Ngoại trừ duy nhất **RDS Custom**) |
| **Kiểm toán & Giám sát (Auditing)** | Audit Logs $\rightarrow$ CloudWatch Logs| Export log sang CloudWatch Logs để lưu trữ lâu dài và đặt cảnh báo tự động |

---

### I. Góc nhìn Lập trình viên (.NET / Next.js) & DevOps / Network
*   **Đối với dev C# / ASP.NET Core & Entity Framework Core:**
    *   *Kết nối bảo mật TLS:* Trong `ConnectionStrings`, thêm thuộc tính `SSL Mode=Require;Trust Server Certificate=true;` (hoặc nạp chứng chỉ Root CA của AWS vào `X509CertificateStore` của server).
    *   *Áp dụng IAM Authentication:* Thay vì lưu mật khẩu DB trong `appsettings.json`, bạn cài đặt thư viện `AWSSDK.RDS`. Trước khi mở kết nối `SqlConnection` / `NpgsqlConnection`, gọi phương thức `RDSAuthTokenGenerator.GenerateAuthToken()` để lấy token tạm 15 phút gán vào trường Password của connection string.
*   **Đối với dev React / Next.js & Serverless (AWS Lambda):**
    *   Khi viết API Route chạy trên AWS Lambda kết nối vào Aurora/RDS, việc sử dụng **IAM Database Authentication** là giải pháp hoàn hảo: Lambda Function tự dùng IAM Execution Role của chính nó để xin token vào DB, loại bỏ hoàn toàn việc phải nhét database password vào biến môi trường `process.env`.
*   **Đối với Kỹ sư Mạng & DevOps:**
    *   Luôn thiết kế mô hình **VPC 3-Tier**:
        *   Tier 1 (Public Subnet): Internet Facing Load Balancer (ALB).
        *   Tier 2 (Private Subnet App): EC2 Instances / ECS Tasks / EKS Nodes.
        *   Tier 3 (Private Subnet Data): RDS / Aurora Database Subnet Group.
    *   Quy tắc Security Group Chaining: Chỉ mở Port `3306`/`5432` với `Source = sg-app-tier`.

---

### J. Mẹo thi & Bẫy trắc nghiệm SAA-C03 (Exam Keywords & Traps)
> ⚠️ **Các mẫu câu hỏi kinh điển về RDS Security trong đề thi:**
> 1.  **"Làm thế nào để mã hóa một database RDS đang chạy mà chưa được bật mã hóa KMS?"**
>     $\rightarrow$ Quy trình đúng: **Chụp Snapshot $\rightarrow$ Copy Snapshot với tùy chọn Encrypt $\rightarrow$ Restore thành Database mới $\rightarrow$ Chuyển traffic sang DB mới**. (Bất kỳ đáp án nào nói "Bấm Modify DB rồi bật KMS" đều là **SAI**).
> 2.  **"Nếu Master DB không mã hóa, có thể tạo Read Replica được mã hóa không?"**
>     $\rightarrow$ **KHÔNG THỂ**. Muốn replica mã hóa thì Master bắt buộc phải được mã hóa trước.
> 3.  **"Yêu cầu kết nối database không được dùng mật khẩu lưu cứng (no hardcoded credentials), tự động hết hạn"**
>     $\rightarrow$ Chọn ngay **IAM Database Authentication** (Token tạm thời 15 phút từ AWS STS).
> 4.  **"Làm thế nào để lưu trữ nhật ký truy vấn (Audit Logs) của RDS lâu hơn thời gian lưu trữ mặc định trên máy chủ?"**
>     $\rightarrow$ Cấu hình **Export Audit Logs sang Amazon CloudWatch Logs**.
> 5.  **"Một câu hỏi yêu cầu SSH vào instance của RDS PostgreSQL để cài phần mềm"**
>     $\rightarrow$ Đáp án: **Không thể thực hiện được** vì RDS không hỗ trợ SSH (trừ khi dùng RDS Custom, nhưng RDS Custom chỉ hỗ trợ Oracle và SQL Server).

---

## Bài 97: RDS Proxy (Amazon RDS Proxy)
**Câu hỏi:** Amazon RDS Proxy là gì và sinh ra để giải quyết vấn đề gì? Tại sao các ứng dụng kiến trúc Serverless (AWS Lambda) lại đặc biệt cần RDS Proxy? RDS Proxy giúp giảm thời gian Failover như thế nào và tích hợp bảo mật với IAM & Secrets Manager ra sao? Những hạn chế và lưu ý mạng quan trọng của RDS Proxy trong kỳ thi SAA-C03 là gì?

**Trả lời:**

### A. Slide bài giảng
````carousel
![Slide: Amazon RDS Proxy](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1790698181028.png)
````

---

### B. Vấn đề thực tế & Sự ra đời của Amazon RDS Proxy
Trong kiến trúc phần mềm hiện đại, đặc biệt là kiến trúc **Serverless** (AWS Lambda, Fargate) và **Microservices**:
*   **Vấn đề "Cơn bão kết nối" (Connection Storm):**
    *   Hàm AWS Lambda có cơ chế co giãn tự động cực nhanh (Auto-scaling theo số lượng request). Khi có một đợt bùng nổ truy cập (traffic spike), AWS Lambda có thể lập tức sinh ra hàng nghìn containers/instances độc lập chạy đồng thời.
    *   Mỗi container Lambda khi khởi động sẽ cố gắng mở một hoặc nhiều kết nối mạng trực tiếp tới cơ sở dữ liệu quan hệ (RDS MySQL, PostgreSQL...).
    *   **Hậu quả trên Database:**
        *   Cơ sở dữ liệu quan hệ truyền thống (RDBMS) không được thiết kế để duy trì hàng chục nghìn kết nối mở/đóng liên tục.
        *   Mỗi kết nối mở tiêu tốn một lượng tài nguyên RAM đáng kể (khoảng 2MB - 10MB per connection để duy trì connection state, buffer, session context).
        *   CPU của database bị quá tải (CPU thrashing) chỉ để xử lý các gói tin bắt tay mạng TCP 3-way handshake, mã hóa TLS và xác thực đăng nhập (Authentication Handshake), dẫn tới CPU spike 100%.
        *   Cơ sở dữ liệu chạm ngưỡng giới hạn kết nối (`max_connections`) $\rightarrow$ Bắt đầu từ chối kết nối, văng lỗi **`Too many connections`**, các request sau bị **Open Connection Timeout** $\rightarrow$ Hệ thống sập hàng loạt.
*   **Giải pháp - Amazon RDS Proxy:**
    *   Là một dịch vụ **Managed Database Proxy** chuyên dụng cho Amazon RDS và Amazon Aurora.
    *   Đứng làm tầng trung gian nằm giữa các ứng dụng máy khách (AWS Lambda, EC2, ECS...) và cụm cơ sở dữ liệu RDS/Aurora.
    *   Bản thân RDS Proxy là một dịch vụ **Serverless, tự động co giãn (Autoscaling) và có tính sẵn sàng cao (High Availability / Multi-AZ)** do AWS hoàn toàn quản trị ngầm.

---

### C. Các Tính năng Cốt lõi của Amazon RDS Proxy

#### 1. Gom cụm và Tái sử dụng Kết nối (Connection Pooling & Multiplexing)
*   Thay vì mỗi instance của Lambda mở một kết nối riêng tới database, tất cả Lambda kết nối tới **RDS Proxy**.
*   RDS Proxy duy trì sẵn một nhóm (pool) các kết nối thường trực, ổn định và có kích thước vừa phải tới RDS database.
*   **Multiplexing (Ghép kênh truy vấn):** RDS Proxy chia sẻ và tái sử dụng các kết nối backend này cho hàng nghìn request từ Lambda. Khi một hàm Lambda thực hiện xong truy vấn SQL, kết nối backend đó ngay lập tức được trả về pool để phục vụ cho hàm Lambda khác.
*   **Kết quả:**
    *   Giảm tải áp lực khủng khiếp lên CPU và RAM của database.
    *   Loại bỏ hoàn toàn hiện tượng cạn kiệt kết nối (`Too many connections`) và triệt tiêu lỗi connection timeout.
    *   Database tập trung 100% năng lực xử lý cho việc đọc/ghi dữ liệu thay vì bận rộn bắt tay mạng.

#### 2. Rút ngắn thời gian Failover lên đến 66% (Accelerated Failover)
*   **Cơ chế Failover thông thường (Không có Proxy):**
    *   Khi RDS Multi-AZ hoặc Aurora Master bị sự cố, AWS thăng cấp một Read Replica lên làm Master mới.
    *   Sau đó, AWS phải cập nhật lại bản ghi **DNS (CNAME)** của Cluster/Instance Endpoint để trỏ sang địa chỉ IP mới.
    *   Quá trình lan truyền DNS (DNS propagation) và việc máy khách/ứng dụng lưu cache DNS (DNS Caching) thường khiến việc chuyển vùng mất từ **30 đến 60 giây**. Trong thời gian này, ứng dụng bị ngắt kết nối và báo lỗi gián đoạn.
*   **Cơ chế tối ưu khi có RDS Proxy:**
    *   Ứng dụng kết nối tới một Endpoint cố định duy nhất của RDS Proxy.
    *   RDS Proxy kết nối trực tiếp với các instance trong cụm database. Khi Master gặp sự cố, **RDS Proxy tự động phát hiện và chủ động chuyển hướng (re-route) toàn bộ lưu lượng sang Master mới mà không cần chờ cập nhật DNS**.
    *   Các kết nối giữa Client và RDS Proxy vẫn được giữ nguyên vẹn (không bị đứt kết nối phía client).
    *   **Thời gian Failover giảm tới 66%** (rút ngắn xuống chỉ còn khoảng **10 - 20 giây** hoặc thấp hơn), giúp tăng đáng kể tính liên tục của hệ thống.

#### 3. Tăng cường Bảo mật với IAM Authentication & AWS Secrets Manager
*   **Xác thực bằng IAM (Enforce IAM Authentication):**
    *   Phía client (đặc biệt là AWS Lambda) có thể xác thực với RDS Proxy thông qua **IAM Role** của hàm đó, hoàn toàn không cần lưu password trong code.
*   **Tích hợp AWS Secrets Manager:**
    *   RDS Proxy tự động kết nối và lấy thông tin tài khoản/mật khẩu thực tế của Database từ dịch vụ **AWS Secrets Manager**.
    *   *Luồng bảo mật chuẩn:* `Lambda (dùng IAM Role) -> RDS Proxy (lấy DB Credentials từ Secrets Manager) -> RDS Database`.
    *   **Lợi ích:** Không một lập trình viên hay dịch vụ client nào cần biết mật khẩu thật của database. Hỗ trợ tự động xoay vòng mật khẩu (Automatic Password Rotation) trong Secrets Manager mà ứng dụng client hoàn toàn không bị ảnh hưởng hay gián đoạn.

#### 4. Không cần thay đổi mã nguồn ứng dụng (No Code Changes Required)
*   RDS Proxy hoàn toàn tương thích với các giao thức chuẩn của database.
*   Ứng dụng của bạn không cần cài thêm SDK đặc thù nào; bạn chỉ cần **thay đổi chuỗi kết nối (Connection String / Endpoint)** từ địa chỉ RDS DB sang địa chỉ của RDS Proxy.

---

### D. Giới hạn Mạng Cốt lõi: RDS Proxy KHÔNG BAO GIỜ Public (Never Publicly Accessible)
> ⚠️ **ĐẶC BIỆT LƯU Ý TRONG ĐỀ THI SAA-C03:**
*   Amazon RDS Proxy **KHÔNG BAO GIỜ CÓ THỂ TRUY CẬP CÔNG KHAI TỪ INTERNET (NEVER publicly accessible)**.
*   RDS Proxy **bắt buộc phải nằm bên trong một VPC (Virtual Private Cloud)** và thuộc các **Private Subnets**.
*   Bất kỳ ứng dụng nào muốn kết nối tới RDS Proxy đều phải có đường truyền mạng nội bộ vào VPC đó:
    *   AWS Lambda muốn kết nối tới RDS Proxy **bắt buộc phải được cấu hình chạy trong VPC (VPC-enabled Lambda)**.
    *   EC2, ECS, EKS phải cùng nằm trong VPC hoặc được định tuyến qua VPC Peering / Transit Gateway.
    *   Nếu có câu hỏi trắc nghiệm đề xuất: *"Bật Public IP cho RDS Proxy để cho phép máy tính dev từ nhà kết nối trực tiếp qua Internet"* $\rightarrow$ **Đáp án này là HOÀN TOÀN SAI**.

---

### E. Các Engine Cơ sở dữ liệu được hỗ trợ
*   **Amazon RDS:**
    *   RDS for MySQL
    *   RDS for PostgreSQL
    *   RDS for MariaDB
    *   RDS for Microsoft SQL Server
*   **Amazon Aurora:**
    *   Aurora MySQL-Compatible Edition
    *   Aurora PostgreSQL-Compatible Edition

---

### F. Bảng so sánh: Kết nối Trực tiếp vs Kết nối qua RDS Proxy

| Tiêu chí | Kết nối Trực tiếp vào RDS | Kết nối qua Amazon RDS Proxy |
| :--- | :--- | :--- |
| **Quản lý Kết nối** | Mỗi client mở kết nối riêng, dễ cạn pool khi Lambda scale | Gom và dùng chung kết nối (Connection Pooling & Multiplexing) |
| **Tải tài nguyên DB** | CPU & RAM DB tăng vọt do bắt tay mạng và giữ connection state | Giảm tải tối đa, DB chỉ tập trung chạy câu lệnh SQL |
| **Thời gian Failover** | Mất **30 - 60 giây** (phụ thuộc vào lan truyền DNS) | **Giảm tới 66%** (khoảng **10 - 20 giây**, không phụ thuộc DNS) |
| **Bảo mật Credential**| Thường phải lưu DB Password trong biến môi trường hoặc code | Tích hợp **IAM Auth** + **AWS Secrets Manager**, không lộ mật khẩu |
| **Tính khả dụng mạng**| Có thể cấu hình Public hoặc Private | **BẮT BUỘC Private trong VPC** (Never Public) |
| **Phù hợp nhất với** | Ứng dụng truyền thống chạy lâu dài (EC2, Monolith) | **Serverless (AWS Lambda), Container bùng nổ traffic** |

---

### G. Góc nhìn Lập trình viên (.NET / Next.js) & DevOps / Network

#### 1. Đối với Lập trình viên C# / ASP.NET Core & Entity Framework Core
*   Trong các ứng dụng ASP.NET Core truyền thống chạy trên IIS hoặc máy chủ EC2 đơn lẻ, ADO.NET (`Microsoft.Data.SqlClient`, `Npgsql`) đã tích hợp sẵn một bộ Connection Pool nội bộ bên trong tiến trình (In-Process Connection Pooling).
*   Tuy nhiên, khi bạn chuyển đổi sang kiến trúc **Microservices container hóa** (triển khai trên AWS ECS Fargate hoặc EKS) với khả năng tự động co giãn từ 10 lên hàng trăm Pods/Tasks, hoặc chạy code C# trên **AWS Lambda (.NET 8/9 C# Serverless Functions)**:
    *   Mỗi Pod hoặc Lambda Function lại tạo riêng cho mình một connection pool độc lập. Khi nhân bản lên 200 Pods, tổng số kết nối mở tới database sẽ bùng nổ theo cấp số nhân ($200 \times \text{MinPoolSize}$), làm tê liệt RDS.
    *   **Giải pháp:** Đặt RDS Proxy ở giữa. Tất cả các container .NET hoặc C# Lambda chỉ cần trỏ connection string tới Proxy:
        ```csharp
        // Thay vì trỏ trực tiếp tới RDS:
        // "Host=mydb.c8a2kd1.ap-southeast-1.rds.amazonaws.com;Database=shop;..."
        // Ta chỉ cần đổi Endpoint sang RDS Proxy:
        "Host=my-rds-proxy.proxy-c8a2kd1.ap-southeast-1.rds.amazonaws.com;Database=shop;Username=admin;..."
        ```
    *   Code EF Core và Dapper chạy hoàn toàn bình thường mà không cần sửa bất kỳ dòng logic nào.

#### 2. Đối với Lập trình viên React / Next.js & Serverless
*   Khi bạn xây dựng ứng dụng Fullstack bằng **Next.js (App Router)** với Server Actions hoặc Server-Side Rendering (SSR) / Route Handlers triển khai dưới dạng Serverless (trên AWS Amplify Hosting, SST, hoặc AWS Lambda qua OpenNext):
    *   Mỗi khi người dùng truy cập trang web, một lời gọi hàm Serverless được kích hoạt. Nếu không có connection pooling tập trung, chỉ cần một chiến dịch marketing kéo 10,000 người dùng truy cập cùng lúc, 10,000 hàm Lambda sẽ đồng loạt "tấn công" RDS database bằng các lệnh mở kết nối TCP $\rightarrow$ Database sập ngay tức khắc.
    *   RDS Proxy chính là "chiếc van điều tiết" sống còn: giữ cho database luôn an toàn ở mức tải cho phép, trong khi vẫn phục vụ trơn tru hàng chục nghìn lượt truy cập của Next.js.

#### 3. Đối với Kỹ sư Mạng & DevOps
*   **Mô hình Mạng Chuẩn (Network Architecture):**
    ```
    [Internet] 
        | (HTTPS)
    [Application Load Balancer / API Gateway]
        |
    [VPC - Private Subnet A & B]
        |--> [AWS Lambda (VPC Configured)] 
                 | (IAM Auth / Port 5432/3306)
                 v
        |--> [Amazon RDS Proxy (Multi-AZ Deployment)] (sg-rds-proxy)
                 | (Native DB Protocol / Secrets Manager Creds)
                 v
        |--> [Amazon RDS / Aurora Cluster] (sg-rds-database)
    ```
*   **Quy tắc Security Group (Chaining Security Groups):**
    *   `sg-rds-proxy`: Cho phép Inbound traffic từ `sg-lambda` hoặc `sg-app` trên cổng database tương ứng (ví dụ: Port 5432 cho Postgres).
    *   `sg-rds-database`: **CHỈ cho phép Inbound traffic từ `sg-rds-proxy`**. Khóa toàn bộ các truy cập trực tiếp từ Lambda hoặc EC2 vào database để đảm bảo 100% kết nối đều phải đi qua Proxy.

---

### H. Mẹo thi & Bẫy trắc nghiệm SAA-C03 (Exam Keywords & Traps)

> 💡 **Từ khóa nhận diện phương án Amazon RDS Proxy trong bài thi:**
> *   *"AWS Lambda functions scaling up rapidly and exhausting RDS database connections / connection timeout / CPU spike"* $\rightarrow$ **Chọn Amazon RDS Proxy**.
> *   *"Pool and share database connections for serverless applications"* $\rightarrow$ **Chọn Amazon RDS Proxy**.
> *   *"Reduce database failover time by up to 66% without application changes"* $\rightarrow$ **Chọn Amazon RDS Proxy**.
> *   *"Enforce IAM authentication for RDS database and retrieve credentials from Secrets Manager"* $\rightarrow$ **Chọn Amazon RDS Proxy**.

> ⚠️ **Những chiếc bẫy cần cảnh giác tuyệt đối:**
> 1.  **Bẫy Public Access:** Đề bài hỏi *"Cần cấu hình RDS Proxy như thế nào để cho phép các lập trình viên bên ngoài Internet kết nối trực tiếp vào DB?"*
>     $\rightarrow$ Đáp án nào nói cấu hình RDS Proxy có Public IP hoặc Public Endpoint là **SAI HOÀN TOÀN**. RDS Proxy **bắt buộc phải nằm trong VPC và không có tính năng Publicly Accessible**. Muốn truy cập từ ngoài phải qua VPN, Direct Connect hoặc Bastion Host.
> 2.  **Bẫy Lambda kết nối RDS Proxy:** Để Lambda gọi được RDS Proxy, Lambda **bắt buộc phải được gán vào VPC (VPC Subnets & Security Groups)**. Nếu Lambda chạy mặc định ngoài VPC (Non-VPC Lambda) thì không thể chạm tới RDS Proxy.
> 3.  **Bẫy sửa đổi mã nguồn:** Bất kỳ phương án nào nói *"Cần cài đặt thư viện SDK chuyên dụng và viết lại toàn bộ tầng Data Access Layer (DAL) để tương thích với RDS Proxy"* đều là **SAI**. RDS Proxy trong suốt với ứng dụng, chỉ cần đổi Connection String.
> 4.  **Bẫy đối tượng cơ sở dữ liệu:** RDS Proxy chỉ hỗ trợ các cơ sở dữ liệu quan hệ (RDS & Aurora), **KHÔNG hỗ trợ DynamoDB** hay các dịch vụ NoSQL khác.

---

## Bài 98: Tổng quan về Amazon ElastiCache (Amazon ElastiCache Overview)
**Câu hỏi:** Amazon ElastiCache là gì? Hoạt động như thế nào để tối ưu hóa hiệu năng và độ trễ cho hệ thống? Sự khác biệt lớn nhất giữa việc áp dụng Amazon RDS Proxy và Amazon ElastiCache từ góc độ lập trình viên là gì? Hai kiến trúc kinh điển của ElastiCache (DB Cache / Cache-Aside và Session Store) vận hành ra sao? So sánh toàn diện giữa Redis và Memcached theo góc nhìn của Solutions Architect?

**Trả lời:**

### A. Slide bài giảng
````carousel
![Slide: Amazon ElastiCache Overview](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1790698653453.png)
<!-- slide -->
![Slide: Kiến trúc Giải pháp ElastiCache - Database Cache](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1790699178502.png)
<!-- slide -->
![Slide: Kiến trúc Giải pháp ElastiCache - User Session Store](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1790699185934.png)
<!-- slide -->
![Slide: So sánh Amazon ElastiCache - Redis vs Memcached](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1790699211644.png)
````

---

### B. Amazon ElastiCache là gì?
Tương tự như việc Amazon RDS giúp bạn quản lý các cơ sở dữ liệu quan hệ (Managed Relational Databases), **Amazon ElastiCache** là dịch vụ được AWS quản lý hoàn toàn (**Fully Managed**) giúp bạn thiết lập, vận hành và mở rộng các công nghệ bộ nhớ đệm trong RAM (**In-Memory Cache Engine**).

*   **Hai Engine cốt lõi:** Hỗ trợ 2 chuẩn công nghệ mã nguồn mở phổ biến nhất thế giới: **Redis** và **Memcached**.
*   **Hiệu năng vượt trội:** Vì dữ liệu được lưu trữ trực tiếp trên bộ nhớ RAM (In-Memory) thay vì ghi xuống đĩa cứng (SSD/HDD), ElastiCache mang lại hiệu năng truy xuất cực cao với **độ trễ siêu thấp dưới một mili-giây (Sub-millisecond latency)**.
*   **Hai mục đích sử dụng quan trọng nhất:**
    1.  **Giảm tải cho Cơ sở dữ liệu (Database Offloading):** Phù hợp với các hệ thống có khối lượng công việc đọc chiếm đa số (**Read-intensive workloads**). Kết quả của các câu truy vấn phổ biến được lưu trong cache, giúp cơ sở dữ liệu chính (RDS, Aurora) không phải tính toán hay quét đĩa liên tục.
    2.  **Lưu trữ Trạng thái Phiên (User Session Store):** Biến ứng dụng từ có trạng thái (Stateful) thành **phi trạng thái (Stateless)** bằng cách lưu trữ toàn bộ phiên làm việc của người dùng vào ElastiCache tập trung.
*   **Trách nhiệm quản lý của AWS (Managed Service):** AWS tự động thực hiện bảo trì hệ điều hành bên dưới, vá lỗi (OS patching), tối ưu hóa cấu hình, giám sát hiệu năng qua CloudWatch, tự động phát hiện và khắc phục sự cố (failure recovery), cũng như sao lưu dữ liệu (backups).

---

### C. Khác biệt cốt lõi: RDS Proxy vs Amazon ElastiCache
Đây là một trong những điểm phân biệt quan trọng nhất giữa hai giải pháp tối ưu cơ sở dữ liệu trên AWS:

| Tiêu chí | Amazon RDS Proxy | Amazon ElastiCache |
| :--- | :--- | :--- |
| **Vấn đề giải quyết** | Cơn bão kết nối (Connection Storm), cạn kiệt pool kết nối từ Serverless/Lambda, giảm thời gian failover | Giảm tải đọc (Read-heavy workload), tăng tốc độ phản hồi với độ trễ cực thấp (< 1ms), lưu Session |
| **Can thiệp Mã nguồn (Code Changes)** | **KHÔNG CẦN sửa code** (No code changes) - Chỉ việc đổi địa chỉ chuỗi kết nối (`ConnectionString`) | **BẮT BUỘC can thiệp sâu vào code** (Heavy application code changes) |
| **Cách thức hoạt động** | Proxy đứng giữa gom và tái sử dụng kết nối mạng TCP tới DB | Ứng dụng phải tự gọi SDK để đọc cache, kiểm tra Cache Hit/Miss, tự ghi vào cache và tự hủy cache cũ |
| **Độ trễ truy xuất** | Độ trễ thông thường của RDBMS (vài mili-giây) | **Độ trễ siêu nhỏ dưới mili-giây (Sub-millisecond)** |

---

### D. Hai Mô hình Kiến trúc Điển hình của ElastiCache

#### 1. Mô hình Giảm tải Truy vấn Cơ sở dữ liệu (Cache-Aside / Lazy Loading Pattern)
Mô hình này giúp giảm áp lực đọc trực tiếp lên RDS Database:
```
           (1) Kiểm tra Cache
Client --------> App Server --------------------> Amazon ElastiCache
                  |    |                               |
                  |    +<------------------------------+
                  |         (2a) Cache HIT: Trả dữ liệu ngay (< 1ms)
                  |
                  | (2b) Cache MISS: Đọc từ Database
                  v
          Amazon RDS / Aurora
                  |
                  +-----> (3) App ghi dữ liệu mới vào ElastiCache (để lần sau Hit)
```
*   **Cache Hit:** Ứng dụng gửi truy vấn tới ElastiCache trước. Nếu dữ liệu đã có trong Cache $\rightarrow$ Lấy kết quả ngay lập tức trả về cho người dùng. Tiết kiệm 1 lần truy vấn đĩa tốn kém vào RDS.
*   **Cache Miss:** Nếu dữ liệu chưa có trong Cache:
    1.  Ứng dụng chuyển sang truy vấn trực tiếp vào RDS Database.
    2.  Ứng dụng nhận kết quả từ RDS và trả về cho người dùng.
    3.  Ứng dụng thực hiện ghi bản sao kết quả đó vào ElastiCache (kèm thời gian sống TTL - Time To Live) để những request giống hệt tiếp theo sẽ trở thành Cache Hit.
*   **Thách thức lớn nhất (Cache Invalidation Strategy):** Khi dữ liệu trong RDS bị thay đổi (UPDATE, DELETE), ứng dụng phải có chiến lược xóa hoặc cập nhật bản ghi tương ứng trong ElastiCache để tránh việc người dùng đọc phải dữ liệu cũ, lỗi thời (Stale data).

#### 2. Mô hình Lưu trữ Phiên người dùng (Stateless Application - User Session Store)
Mô hình này giúp loại bỏ sự phụ thuộc vào máy chủ web cụ thể và xóa bỏ nhu cầu dùng ALB Sticky Sessions:
```
                     (User Request)
User --------------------------------------> [Application Load Balancer]
                                                     |
                     +-------------------------------+-------------------------------+
                     |                                                               |
                     v                                                               v
            [EC2 Web Server 1]                                              [EC2 Web Server 2]
                     \                                                               /
                      \  (Đọc / Ghi Session Token tập trung)                        /
                       \                                                           /
                        +------------------> [Amazon ElastiCache] <---------------+
```
*   **Cách thức vận hành:**
    1.  Người dùng đăng nhập qua Web Server 1.
    2.  Web Server 1 tạo phiên đăng nhập (Session) và lưu toàn bộ thông tin phiên vào cụm **Amazon ElastiCache tập trung**.
    3.  Ở request tiếp theo, nếu Load Balancer điều hướng người dùng sang Web Server 2 (hoặc Web Server 1 bị crash/autoscale tắt đi): Web Server 2 chỉ cần kết nối tới ElastiCache để lấy lại đúng Session đó $\rightarrow$ Người dùng vẫn duy trì trạng thái đăng nhập bình thường mà không cần đăng nhập lại.
*   **Lợi ích:** Biến toàn bộ tầng máy chủ Web/App thành **Stateless (phi trạng thái)**, cho phép hệ thống tự do co giãn Auto-scaling (thêm/bớt máy chủ) theo nhu cầu mà không làm gián đoạn trải nghiệm người dùng.

---

### E. So sánh Toàn diện: Redis vs Memcached
Đây là bảng phân loại cốt lõi thường xuyên xuất hiện trong các câu hỏi lựa chọn công nghệ của đề thi SAA-C03:

| Đặc tính kiến trúc | Amazon ElastiCache for Redis | Amazon ElastiCache for Memcached |
| :--- | :--- | :--- |
| **Tính sẵn sàng cao (High Availability)** | **CÓ**: Hỗ trợ triển khai **Multi-AZ** với cơ chế tự động chuyển vùng (**Auto-Failover**) | **KHÔNG**: Không có Replication, không có Auto-Failover giữa các Node |
| **Mở rộng Đọc (Read Scalability)** | **CÓ**: Hỗ trợ tạo các **Read Replicas** để san tải đọc | **KHÔNG**: Không hỗ trợ Read Replicas |
| **Kiến trúc phân mảnh (Architecture)** | Mô hình Primary - Replica; Hỗ trợ Redis Cluster (Sharding) | **Sharding đa node thuần túy** (Data Partitioning across nodes) |
| **Đa luồng CPU (Multi-threading)** | Xử lý lệnh đơn luồng (Single-threaded engine) kết hợp luồng phụ trợ | **Đa luồng thực sự (Multi-threaded)**, tận dụng tối đa nhiều CPU cores |
| **Tính bền bỉ dữ liệu (Durability)** | **CÓ**: Hỗ trợ ghi đĩa **AOF (Append Only File)** và **RDB Snapshot** | **KHÔNG**: Dữ liệu hoàn toàn nằm trên RAM tạm thời (Pure Volatile Cache) |
| **Sao lưu & Khôi phục (Backup & Restore)** | **CÓ**: Hỗ trợ chụp Snapshot định kỳ và khôi phục khi cần | Chỉ có trên bản **Serverless**; bản tự quản lý không hỗ trợ |
| **Cấu trúc Dữ liệu hỗ trợ** | Rất phong phú: Strings, Hashes, Lists, Sets, **Sorted Sets (Leaderboards)**, Bitmaps, Geospatial | Rất đơn giản: Chỉ hỗ trợ lưu trữ chuỗi hoặc Object (Key - Value) |
| **Trường hợp sử dụng điển hình (Use Cases)** | Cần tính sẵn sàng cao, lưu Session bền vững, làm bảng xếp hạng game (**Leaderboards**), Pub/Sub | Cần bộ đệm cực đơn giản, chia sẻ dữ liệu thuần túy qua nhiều vCPU, chấp nhận mất cache khi node chết |

---

### F. Góc nhìn Lập trình viên (.NET / Next.js) & Kỹ sư Mạng / DevOps

#### 1. Đối với Lập trình viên C# / ASP.NET Core
*   .NET cung cấp abstraction chuẩn `IDistributedCache` qua gói NuGet `Microsoft.Extensions.Caching.StackExchangeRedis`.
*   **Cấu hình trong `Program.cs`:**
    ```csharp
    builder.Services.AddStackExchangeRedisCache(options =>
    {
        options.Configuration = builder.Configuration.GetConnectionString("ElastiCacheRedis");
        options.InstanceName = "ShopApp_";
    });
    ```
*   **Triển khai Cache-Aside trong Service:**
    ```csharp
    public async Task<ProductDto> GetProductAsync(int id)
    {
        string cacheKey = $"product:{id}";
        var cachedData = await _cache.GetStringAsync(cacheKey);
        
        if (cachedData != null) // Cache Hit
        {
            return JsonSerializer.Deserialize<ProductDto>(cachedData);
        }

        // Cache Miss: Query Database và lưu lại vào Cache
        var product = await _dbContext.Products.FindAsync(id);
        var serialized = JsonSerializer.Serialize(product);
        await _cache.SetStringAsync(cacheKey, serialized, new DistributedCacheEntryOptions
        {
            AbsoluteExpirationRelativeToNow = TimeSpan.FromMinutes(30) // TTL 30 phút
        });

        return product;
    }
    ```
*   **Session State:** Tích hợp với `builder.Services.AddSession(...)` sử dụng Redis backplane giúp đồng bộ session giữa các container Docker mà không cần dùng In-Memory Session của IIS.

#### 2. Đối với Lập trình viên React / Next.js & Serverless
*   Khi phát triển Next.js Fullstack (Next.js 14/15 App Router):
    *   Sử dụng thư viện `ioredis` kết nối tới ElastiCache Redis cluster.
    *   **Rate Limiting:** Sử dụng thuật toán Token Bucket / Sliding Window trên Redis để giới hạn tần suất gọi API (ví dụ: tối đa 60 request/phút trên mỗi IP).
    *   **Server Component / API Route Caching:** Lưu trữ các phản hồi JSON nặng từ bên thứ 3 hoặc kết quả tính toán đắt đỏ, giúp giảm thời gian phản hồi từ 500ms xuống chỉ còn 2ms.

#### 3. Đối với Kỹ sư Mạng & DevOps
*   **Vị trí triển khai:** ElastiCache **bắt buộc phải nằm trong Private Subnet** của VPC. Không bao giờ gán Public IP cho ElastiCache.
*   **Cổng dịch vụ chuẩn (Standard Ports):**
    *   **Redis:** Port **`6379`**
    *   **Memcached:** Port **`11211`**
*   **Cấu hình Security Group (Chaining Security Groups):**
    *   `sg-elasticache`: Chỉ cho phép Inbound traffic từ `sg-app` (hoặc `sg-lambda`) trên port `6379` hoặc `11211`.
    *   Quy tắc Outbound từ App Server: Cho phép gửi tới `sg-elasticache` trên port tương ứng.

---

### G. Mẹo thi & Bẫy trắc nghiệm SAA-C03 (Exam Keywords & Traps)

> 💡 **Từ khóa nhận diện phương án Amazon ElastiCache trong bài thi:**
> *   *"Sub-millisecond response time / In-memory caching for read-heavy database workloads"* $\rightarrow$ **Chọn Amazon ElastiCache**.
> *   *"Make web application stateless by storing user session data in a shared cache"* $\rightarrow$ **Chọn Amazon ElastiCache (hoặc DynamoDB)**.
> *   *"Real-time gaming leaderboard, ranking, sorted sets"* $\rightarrow$ **Chọn Amazon ElastiCache for Redis**.
> *   *"High availability with Multi-AZ auto-failover, backup and restore for cache"* $\rightarrow$ **Chọn Amazon ElastiCache for Redis**.
> *   *"Pure caching layer, multi-threaded performance, data sharding across multiple nodes without replication"* $\rightarrow$ **Chọn Amazon ElastiCache for Memcached**.

> ⚠️ **Những chiếc bẫy cần cảnh giác tuyệt đối:**
> 1.  **Bẫy sửa mã nguồn (Code Changes):**
>     *   Nếu đề bài yêu cầu *"Tăng tốc độ đọc của RDS mà **KHÔNG ĐƯỢC PHÉP SỬA ĐỔI MÃ NGUỒN ỨNG DỤNG**"* $\rightarrow$ **KHÔNG ĐƯỢC CHỌN ElastiCache**! Đáp án đúng phải là **RDS Read Replicas** (hoặc RDS Proxy nếu liên quan đến connection pool). ElastiCache luôn đòi hỏi can thiệp nặng vào code ứng dụng.
> 2.  **Bẫy phân biệt Redis vs Memcached:**
>     *   Cứ nhắc tới **Multi-AZ, Auto-failover, Read Replicas, Backup/Restore, Data Durability, Sorted Sets / Leaderboards** $\rightarrow$ **Bắt buộc chọn Redis**.
>     *   Cứ nhắc tới **Multi-threaded, Object caching đơn giản, Sharding chia node thuần túy không cần replication** $\rightarrow$ **Chọn Memcached**.
> 3.  **Bẫy Cache Invalidation:** Lưu dữ liệu trong Cache có thể dẫn tới rủi ro đọc phải dữ liệu cũ (Stale Data). Đề thi hỏi làm sao để cân bằng giữa tính tươi mới của dữ liệu và hiệu năng cache $\rightarrow$ Áp dụng chiến lược **TTL (Time to Live)** hợp lý kết hợp ghi đè cache khi có thao tác Write/Update.

---

## Bài 99: Thực hành Amazon ElastiCache (Amazon ElastiCache Hands On)
**Câu hỏi:** Các bước thiết lập và các tùy chọn cấu hình nâng cao khi tạo một cụm ElastiCache (Redis / Valkey) trên AWS Management Console là gì? Sự khác biệt giữa Serverless và Node-based cluster, Cluster Mode Enabled vs Disabled, và cơ chế bảo mật (Encryption at-rest/in-transit, Redis AUTH, User Group ACL) được vận hành như thế nào trong thực tế?

**Trả lời:**

### A. Tổng quan các Tùy chọn Triển khai trên Console

#### 1. Lựa chọn Engine (Engine Selection)
*   **Valkey:** Bản fork mã nguồn mở của Redis được Linux Foundation và AWS đồng khởi xướng, tương thích 100% với Redis API và là khuyến nghị mặc định mới nhất của AWS (giúp tối ưu chi phí và tránh rủi ro thay đổi giấy phép mã nguồn mở của Redis).
*   **Redis OSS:** Phiên bản mã nguồn mở truyền thống của Redis.
*   **Memcached:** Bộ đệm đa luồng thuần túy (Key-Value).

#### 2. Mô hình Triển khai: Serverless vs Node-based Cluster
*   **ElastiCache Serverless:**
    *   Tự động quản lý hoàn toàn dung lượng lưu trữ và năng lực tính toán.
    *   Tự động co giãn theo số lượng request và dung lượng dữ liệu lưu trữ (tính tiền theo GB-hour lưu trữ và ElastiCache Processing Units - ECPU).
    *   Phù hợp cho các hệ thống có lưu lượng truy cập thất thường, không dự đoán trước được hoặc muốn tối giản công việc vận hành.
*   **Node-based Cluster (Cụm dựa trên Node truyền thống):**
    *   Người dùng được tự do lựa chọn loại phần cứng máy chủ (Instance Family: `t2/t3/t4g.micro` cho môi trường Dev/Test hoặc Free Tier; dòng `r6g/r7g` tối ưu bộ nhớ RAM cho Production).
    *   Tự cấu hình số lượng Shard, số lượng Read Replica, thông số Parameter Group.

---

### B. Cấu hình Cốt lõi của Cụm ElastiCache

#### 1. Chế độ Cụm (Cluster Cache Mode)
*   **Cluster Mode Disabled (Chế độ cụm bị tắt):**
    *   Hệ thống chỉ có **1 Shard duy nhất**.
    *   Gồm **1 Primary Node** (xử lý toàn bộ lệnh Ghi và Đọc) và tối đa **5 Read Replicas** (chỉ xử lý Đọc).
    *   Dữ liệu được sao chép nguyên vẹn 100% từ Primary sang tất cả các Replicas.
    *   Giới hạn: Dung lượng toàn bộ cache không được vượt quá dung lượng RAM của 1 máy chủ đơn lẻ.
*   **Cluster Mode Enabled (Chế độ cụm được bật):**
    *   Hỗ trợ chia dữ liệu thành **nhiều Shards** (Partitioning / Sharding) thông qua cơ chế băm 16,384 Hash Slots.
    *   Mỗi Shard sẽ có 1 Primary Node riêng và các Read Replicas đi kèm.
    *   Cho phép mở rộng quy mô dung lượng lưu trữ vượt quá giới hạn phần cứng của một máy chủ và tăng thông lượng ghi (**Scale Write Throughput**).

#### 2. Vị trí Triển khai (Location)
*   Mặc định chạy trên nền tảng đám mây **AWS Cloud**.
*   Có thể triển khai mở rộng xuống trung tâm dữ liệu On-premises của doanh nghiệp thông qua dịch vụ **AWS Outposts**.

#### 3. Tính sẵn sàng cao & Tự động chuyển vùng (Multi-AZ & Auto-Failover)
*   **Multi-AZ:** Phân bổ các node Primary và Replica nằm ở các Availability Zone (AZ) khác biệt nhau về mặt địa lý.
*   **Auto-Failover:** Khi node Primary gặp sự cố phần cứng, ElastiCache tự động kích hoạt tiến trình bầu chọn và thăng cấp một Read Replica lên làm Primary mới mà không cần con người can thiệp. (Yêu cầu phải có ít nhất 1 Read Replica).

#### 4. Nhóm mạng con (Subnet Group)
*   **Subnet Group (ví dụ: `my-first-subnet-group`):** Định nghĩa danh sách các Subnet bên trong VPC mà các node cache được phép khởi tạo.
*   **Chuẩn kiến trúc:** Luôn chọn các **Private Subnets** nằm trên ít nhất 2 đến 3 AZs khác nhau để đảm bảo cô lập hoàn toàn khỏi Internet công cộng và hỗ trợ High Availability.

---

### C. Bảo mật và Kiểm soát Truy cập (Security & Access Control)

#### 1. Mã hóa Dữ liệu (Encryption)
*   **Encryption at-rest:** Mã hóa toàn bộ dữ liệu tạm thời lưu trên bộ nhớ và ổ cứng sao lưu bằng khóa **AWS KMS**.
*   **Encryption in-transit (TLS):** Bắt buộc mã hóa toàn bộ dữ liệu truyền tải trên đường truyền mạng giữa ứng dụng client và cụm ElastiCache.

#### 2. Quản lý Quyền Truy cập (Access Control)
> ⚠️ **Lưu ý cốt lõi:** Tính năng Access Control chỉ có thể kích hoạt khi bạn đã **BẬT Encryption in-transit (TLS)**!
*   **Redis AUTH (Token-based Authentication):**
    *   Thiết lập một chuỗi mật khẩu/token bí mật (`AUTH password`).
    *   Khi ứng dụng client (mã nguồn C#, Node.js...) kết nối tới ElastiCache, client phải gửi lệnh `AUTH <password>` để được cấp phép truy xuất.
*   **User Group Access Control List (ACL):**
    *   Phương thức quản lý bảo mật nâng cao tương tự như phân quyền người dùng trong CSDL quan hệ.
    *   Cho phép tạo nhiều tài khoản người dùng khác nhau với các nhóm quyền chi tiết (Permissions):
        *   User A: Chỉ có quyền đọc (`~* +@read`).
        *   User B: Có quyền ghi nhưng bị cấm thực thi các câu lệnh nguy hiểm làm xóa sạch dữ liệu như `FLUSHALL`, `FLUSHDB`, `CONFIG`.

#### 3. Kiểm soát Mạng (Security Groups)
*   Gán Security Group cho ElastiCache để đóng toàn bộ các cổng truy cập, chỉ cho phép cổng **`6379`** (với Redis) nhận gói tin xuất phát từ Security Group của máy chủ ứng dụng Web/App/Lambda.

---

### D. Giám sát & Nhật ký (Monitoring & Logs)
*   **Slow Logs:** Tự động ghi lại nhật ký các câu truy vấn xử lý tốn nhiều thời gian vượt ngưỡng cấu hình (ví dụ các lệnh quét dữ liệu `KEYS *` tốn CPU).
*   **Engine Logs:** Nhật ký hoạt động nội bộ của Redis/Valkey engine.
*   Cả hai loại log này đều có thể cấu hình để tự động đẩy sang **Amazon CloudWatch Logs** hoặc **Amazon Kinesis Data Firehose** để phân tích và đặt cảnh báo tự động.

---

### E. Các Điểm cuối Kết nối (Endpoints Architecture)
Sau khi tạo xong cụm ElastiCache, AWS cung cấp các Endpoint phục vụ cho việc kết nối từ code ứng dụng:
1.  **Primary Endpoint:** Địa chỉ dùng cho các thao tác **GHI (Write)** và đọc từ Node Master.
2.  **Reader Endpoint:** Tự động cân bằng tải (Load Balancing theo cơ chế Round-Robin) các truy vấn **ĐỌC (Read-only)** phân bổ đều qua tất cả các Read Replicas có trong cụm.
3.  **Configuration Endpoint:** Xuất hiện khi bật **Cluster Mode Enabled**, giúp thư viện client (Cluster-aware SDK) tự động phát hiện sơ đồ phân mảnh (Topology/Hash slots) của toàn bộ các Shard trong cụm.

---

### F. Quy trình Dọn dẹp Tránh Phát sinh Chi phí (Clean Up)
*   ElastiCache tính tiền theo số giờ máy chủ hoạt động (Node-hour) hoặc theo dung lượng GB/giờ (Serverless).
*   Sau khi thực hành xong, chọn cụm cache $\rightarrow$ **Actions $\rightarrow$ Delete**.
*   Tại màn hình xác nhận, chọn **Không tạo bản sao lưu cuối cùng (No final backup)** $\rightarrow$ Gõ tên cluster để xác nhận xóa sạch hoàn toàn tài nguyên.

---

### G. Mẹo thi & Bẫy trắc nghiệm SAA-C03 (Exam Keywords & Traps)

> 💡 **Từ khóa nhận diện cấu hình ElastiCache trong đề thi:**
> *   *"Yêu cầu tăng tính sẵn sàng cho ElastiCache Redis và tự động khôi phục khi node chính bị lỗi"* $\rightarrow$ **Bật Multi-AZ với Auto-Failover**.
> *   *"Bảo mật kết nối Redis bằng mật khẩu bí mật (token)"* $\rightarrow$ **Bật Encryption in-transit và cấu hình Redis AUTH**.
> *   *"Phân quyền chi tiết cho nhiều ứng dụng kết nối tới Redis, cấm một số câu lệnh nhạy cảm"* $\rightarrow$ **Cấu hình ElastiCache User Group ACLs**.
> *   *"Dung lượng cache vượt quá giới hạn bộ nhớ của một máy chủ hoặc cần tăng năng lực ghi"* $\rightarrow$ **Bật Redis Cluster Mode Enabled (Sharding)**.

> ⚠️ **Những chiếc bẫy cần cảnh giác tuyệt đối:**
> 1.  **Bẫy Redis AUTH không khả dụng:** Nếu bạn không bật **Encryption in-transit (TLS)**, tùy chọn **Redis AUTH sẽ bị vô hiệu hóa (disabled)**. Đề thi có thể hỏi điều kiện tiên quyết để sử dụng Redis AUTH $\rightarrow$ Bắt buộc phải bật Encryption in-transit.
> 2.  **Bẫy kết nối từ bên ngoài Internet:** Không giống như RDS có tùy chọn "Publicly Accessible = Yes", **ElastiCache KHÔNG THỂ gán Public IP để truy cập trực tiếp từ máy cá nhân ở nhà qua Internet**. Muốn kết nối vào ElastiCache từ máy cá nhân để debug, bạn bắt buộc phải đi qua **VPN kết nối vào VPC** hoặc dùng **SSH Tunneling (Port Forwarding) qua một EC2 Bastion Host** trong VPC.

---

## Bài 101: Danh sách các Port chuẩn cần biết (List of Ports to be familiar with)
**Câu hỏi:** Các cổng mạng (Network Ports) tiêu chuẩn nào thường xuyên xuất hiện trong các bài toán thiết kế kiến trúc và cấu hình Security Group trên AWS? Làm thế nào để phân biệt giữa các Cổng quản trị, Cổng Web và Cổng Cơ sở dữ liệu (RDS/Aurora/ElastiCache)?

**Trả lời:**

### A. Tại sao cần ghi nhớ các Cổng mạng trong đề thi SAA-C03?
*   Đề thi **không bao giờ hỏi thuộc lòng số port** một cách máy móc (ví dụ: *"MySQL dùng cổng nào trong các đáp án sau?"*).
*   Tuy nhiên, AWS kiểm tra kiến thức về Port thông qua các tình huống thực tế về **Cấu hình Tường lửa (VPC Security Groups & Network ACLs)**:
    *   *Ví dụ 1:* Cấu hình Security Group cho Database để cho phép Web Server kết nối vào RDS PostgreSQL $\rightarrow$ Phải chọn đúng **Port 5432** (thay vì 3306 hay 80/443).
    *   *Ví dụ 2:* Quản trị viên không thể SSH vào EC2 Linux $\rightarrow$ Phải kiểm tra **Port 22**.
    *   *Ví dụ 3:* Người dùng truy cập website bảo mật SSL $\rightarrow$ Phải mở **Port 443 (HTTPS)** trên Application Load Balancer.
    *   *Ví dụ 4:* Remote Desktop vào máy chủ Windows EC2 $\rightarrow$ Phải mở **Port 3389 (RDP)**.

---

### B. Bảng tra cứu các Cổng Mạng Tiêu Chuẩn

#### 1. Các Cổng Giao tiếp & Quản trị Quan trọng (Important & Management Ports)
| Cổng (Port) | Giao thức | Tên đầy đủ & Ý nghĩa | Mục đích sử dụng trên AWS |
| :---: | :---: | :--- | :--- |
| **21** | **FTP** | File Transfer Protocol | Truyền tệp tin truyền thống (không mã hóa, ít khuyến nghị trên Cloud) |
| **22** | **SSH** | Secure Shell | Quản trị máy chủ **Linux EC2** từ xa qua giao diện dòng lệnh (CLI) |
| **22** | **SFTP** | Secure File Transfer Protocol | Truyền tệp tin bảo mật chạy trên nền SSH (dùng với AWS Transfer Family) |
| **80** | **HTTP** | HyperText Transfer Protocol | Truy cập Web không mã hóa (thường được cấu hình redirect sang port 443) |
| **443** | **HTTPS** | HyperText Transfer Protocol Secure | Truy cập Web bảo mật qua mã hóa **TLS/SSL** (tiêu chuẩn cho ALB, CloudFront) |
| **3389** | **RDP** | Remote Desktop Protocol | Quản trị máy chủ **Windows Server EC2** từ xa qua giao diện đồ họa (GUI) |
| **53** | **DNS** | Domain Name System | Phân giải tên miền qua TCP/UDP (sử dụng bởi **Amazon Route 53**) |

#### 2. Các Cổng Cơ sở Dữ liệu Quan hệ (RDS & Aurora Databases Ports)
| Cổng (Port) | Database Engine | Ghi chú kiến trúc |
| :---: | :--- | :--- |
| **5432** | **PostgreSQL** | Cổng mặc định của PostgreSQL |
| **3306** | **MySQL** | Cổng mặc định của MySQL |
| **3306** | **MariaDB** | Hoàn toàn tương thích và dùng chung cổng với MySQL |
| **5432 / 3306** | **Amazon Aurora** | Dùng **5432** nếu là *Aurora PostgreSQL-Compatible*; Dùng **3306** nếu là *Aurora MySQL-Compatible* |
| **1433** | **Microsoft SQL Server (MSSQL)** | Cổng mặc định cho SQL Server trên RDS |
| **1521** | **Oracle Database** | Cổng mặc định của Oracle Listener trên RDS |

#### 3. Các Cổng Bộ nhớ đệm Caching (Amazon ElastiCache)
| Cổng (Port) | Dịch vụ Caching | Ghi chú kiến trúc |
| :---: | :--- | :--- |
| **6379** | **Redis / Valkey** | Cổng mặc định cho ElastiCache Redis và Valkey (Serverless có thể dùng thêm port 6380) |
| **11211** | **Memcached** | Cổng mặc định cho ElastiCache Memcached |

#### 4. Các Cổng Dịch vụ Lưu trữ File (Storage Services)
| Cổng (Port) | Giao thức / Dịch vụ | Ghi chú kiến trúc |
| :---: | :--- | :--- |
| **2049** | **NFS (Amazon EFS)** | Network File System dùng để mount ổ đĩa chia sẻ cho các máy chủ **Linux EC2** |
| **445** | **SMB (Amazon FSx for Windows)** | Server Message Block dùng để mount ổ đĩa chia sẻ cho máy chủ **Windows / Active Directory** |

---

### C. Góc nhìn Kỹ sư Mạng & DevOps: Cấu hình Security Group 3-Tier Chuẩn mực
Trong mô hình kiến trúc Web 3 tầng kinh điển trên AWS, các Security Group được liên kết chặt chẽ qua các port như sau:

```
[Người dùng Internet]
       |
       |  Port 80 (HTTP) / Port 443 (HTTPS)
       v
[Public Subnet: Application Load Balancer] (Security Group: sg-alb)
  - Inbound: Port 80 & 443 từ 0.0.0.0/0
       |
       |  Port 80 hoặc 8080 (Source: sg-alb)
       v
[Private Subnet: EC2 Web/App Tier] (Security Group: sg-app)
  - Inbound: Port 80/8080 từ Source sg-alb
  - Inbound (Admin): Port 22 (SSH) từ Source sg-bastion-host
       |
       +---------------------------------------------+
       | Port 5432 / 3306 (Source: sg-app)           | Port 6379 (Source: sg-app)
       v                                             v
[Private Subnet: RDS Database]               [Private Subnet: ElastiCache]
(Security Group: sg-db)                      (Security Group: sg-cache)
  - Inbound: Port 5432/3306 từ sg-app          - Inbound: Port 6379 từ sg-app
  - Khóa toàn bộ các cổng khác                 - Khóa toàn bộ các cổng khác
```

---

### D. Mẹo thi & Bẫy trắc nghiệm SAA-C03 (Exam Keywords & Traps)

> 💡 **Quy tắc phân biệt nhanh trong đề thi:**
> 1.  **Cổng Web:** Chỉ có **80 (HTTP)** và **443 (HTTPS)**.
> 2.  **Cổng Quản trị máy chủ:** **22 (Linux SSH)** và **3389 (Windows RDP)**.
> 3.  **Cổng Database mã nguồn mở phổ biến nhất:** **3306 (MySQL/MariaDB)** và **5432 (Postgres/Aurora)**.
> 4.  **Cổng Database thương mại Enterprise:** **1433 (MSSQL)** và **1521 (Oracle)**.
> 5.  **Cổng Caching:** **6379 (Redis/Valkey)** và **11211 (Memcached)**.

> ⚠️ **Những chiếc bẫy cần cảnh giác tuyệt đối:**
> 1.  **Bẫy Port & NACL (Stateful vs Stateless):**
>     *   Security Group là **Stateful**: Khi mở Inbound Port 5432 cho DB, gói tin trả về tự động được phép đi ra.
>     *   Network ACL (NACL) là **Stateless**: Nếu đề bài hỏi về NACL, khi cho phép Inbound Port 5432, bạn **BẮT BUỘC phải mở Outbound Ephemeral Ports (cổng tạm thời từ 1024 đến 65535)** thì máy chủ App mới nhận được phản hồi từ Database!
> 2.  **Bẫy nhầm lẫn giữa SSH (22) và RDP (3389):** Đề bài nói máy chủ là Windows EC2 nhưng đáp án lại ghi *"Mở port 22 trong Security Group"* $\rightarrow$ **SAI** (Windows dùng RDP port 3389).
> 3.  **Bẫy nhầm lẫn giữa MySQL (3306) và PostgreSQL (5432):** Aurora có 2 phiên bản tương thích (MySQL-compatible dùng 3306; PostgreSQL-compatible dùng 5432). Hãy đọc kỹ đề bài xem hệ thống dùng loại cơ sở dữ liệu nào.



