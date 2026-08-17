# Ôn tập: Phần 7 - Lưu trữ EC2 (EC2 Instance Storage)

Tài liệu này lưu lại các câu hỏi và giải đáp quan trọng trong quá trình học Phần 7 về EC2 Instance Storage để tiện ôn tập.

---

## 1. Ổ lưu trữ mạng EBS (EBS Volumes - Elastic Block Store)
**Câu hỏi:** EBS Volume là gì? Đặc điểm hoạt động và cơ chế liên kết của nó với EC2 instance như thế nào?

**Trả lời:**
*   **Định nghĩa:** **EBS (Elastic Block Store) Volume** là một ổ đĩa mạng (network drive) mà bạn có thể gắn vào các instance EC2 trong khi chúng đang chạy.
*   **Ẩn dụ:** Hãy coi EBS Volume giống như một **"thanh USB mạng"**. Bạn có thể cắm nó vào máy tính này, lưu trữ dữ liệu, rồi rút ra và cắm sang máy tính khác rất nhanh chóng. Thực tế, ổ đĩa này không cắm trực tiếp vào phần cứng máy chủ vật lý mà được kết nối thông qua mạng nội bộ của AWS.
*   **Ràng buộc khu vực (Availability Zone Bound):** 
    *   Mỗi EBS Volume được tạo ra sẽ bị **giới hạn trong một Availability Zone (AZ) cụ thể**. 
    *   *Ví dụ:* Nếu một EBS Volume được tạo ở `us-east-1a`, nó **chỉ có thể gắn** trực tiếp vào EC2 instance cũng nằm ở `us-east-1a`. Nó không thể gắn trực tiếp vào instance ở `us-east-1b`.
    *   *Cách di chuyển:* Để chuyển một EBS Volume sang AZ khác, bạn phải chụp một bản sao lưu (**EBS Snapshot**), sau đó khôi phục snapshot đó thành một EBS Volume mới ở AZ đích.
*   **Hành vi khi hủy máy chủ (Delete on Termination):**
    *   Mặc định, khi bạn tạo máy chủ EC2, ổ đĩa Root (chứa hệ điều hành) sẽ được cấu hình thuộc tính **Delete on Termination** là `True` (Tự động xóa ổ đĩa khi hủy máy chủ).
    *   Các ổ đĩa gắn thêm (EBS phụ) mặc định sẽ có thuộc tính này là `False` (Giữ lại ổ đĩa khi máy chủ bị hủy).
    *   Bạn hoàn toàn có thể tùy chỉnh thuộc tính này trên giao diện Console hoặc CLI trước khi khởi chạy máy.

---

## 2. Các loại ổ đĩa EBS (EBS Volume Types)
**Câu hỏi:** Có những loại ổ đĩa EBS nào? Đặc điểm kỹ thuật, hiệu năng và quy tắc chọn lựa cho từng mục đích sử dụng như thế nào?

**Trả lời:**
EBS Volumes được chia thành **6 loại khác nhau** và gom nhóm thành 3 loại chính:
1.  **General Purpose SSD (SSD Phổ thông - gp2 / gp3):** Cân bằng giữa chi phí và hiệu năng cho nhiều loại workload khác nhau.
2.  **Provisioned IOPS SSD (SSD Đảm bảo hiệu năng - io1 / io2 Block Express):** Hiệu năng cao nhất, thiết kế cho các tác vụ quan trọng yêu cầu độ trễ thấp và thông lượng cao.
3.  **Hard Disk Drives (HDD - st1 / sc1):** Loại ổ đĩa từ tính chi phí thấp, tối ưu thông lượng, không hỗ trợ làm ổ khởi động.

### A. Ràng buộc về Ổ khởi động (Boot Volumes)
*   **Chỉ các loại SSD (`gp2`, `gp3`, `io1`, `io2`) mới có thể được sử dụng làm Boot Volume** (ổ chứa hệ điều hành Root OS).
*   **Các loại HDD (`st1`, `sc1`) KHÔNG THỂ làm boot volume.**

### B. Chi tiết về SSD Phổ thông (General Purpose SSD - gp2 / gp3)
*   **Kích thước:** `1 GiB` đến `16 TiB`.
*   **gp3 (Thế hệ mới):**
    *   Cung cấp mặc định **3,000 IOPS** và thông lượng **125 MiB/s**.
    *   Bạn có thể tăng IOPS lên đến **16,000** và thông lượng lên đến **1,000 MiB/s** một cách **độc lập (independently)** mà không bị ràng buộc với dung lượng ổ đĩa.
*   **gp2 (Thế hệ cũ):**
    *   Các ổ đĩa gp2 nhỏ có thể tăng tốc (burst) lên đến 3,000 IOPS khi cần.
    *   Dung lượng ổ đĩa và IOPS **liên kết chặt chẽ với nhau** theo tỷ lệ: **3 IOPS cho mỗi GiB**.
    *   Để đạt mức tối đa **16,000 IOPS**, bạn bắt buộc phải tạo ổ đĩa có kích thước tối thiểu là **5,334 GiB**.

### C. Chi tiết về SSD Đảm bảo hiệu năng (Provisioned IOPS SSD - io1 / io2 Block Express)
Phù hợp cho các ứng dụng doanh nghiệp quan trọng (Mission-critical) cần duy trì hiệu năng IOPS ổn định, hoặc các ứng dụng yêu cầu hơn 16,000 IOPS (đặc biệt là các workload cơ sở dữ liệu lớn nhạy cảm với hiệu năng đĩa).
*   **io1:**
    *   Dung lượng từ `4 GiB` đến `16 TiB`.
    *   Max IOPS (PIOPS): **64,000** đối với instance chạy trên cấu trúc Nitro EC2, và **32,000** đối với các loại instance khác.
    *   Có thể tăng PIOPS độc lập với dung lượng lưu trữ.
*   **io2 Block Express:**
    *   Dung lượng từ `4 GiB` đến `64 TiB`.
    *   Độ trễ ở mức **dưới 1 mili-giây (sub-millisecond)**.
    *   Max IOPS: lên đến **256,000** với tỷ lệ IOPS:GiB tối đa là **1,000:1**.
*   *Tính năng nâng cao:* Cả io1 và io2 đều hỗ trợ tính năng **EBS Multi-Attach** (gắn một ổ đĩa vào nhiều instances trong cùng một AZ).

### D. Chi tiết về Ổ cứng từ tính (Hard Disk Drives - HDD - st1 / sc1)
Dung lượng từ `125 GiB` đến `16 TiB`.
*   **Throughput Optimized HDD (HDD Tối ưu thông lượng - st1):**
    *   Thích hợp cho các tác vụ cần thông lượng ghi đọc liên tục và dữ liệu được truy cập thường xuyên (Frequently accessed).
    *   *Trường hợp sử dụng:* Big Data, Data Warehousing, xử lý Log (Log processing).
    *   Thông lượng tối đa: `500 MiB/s`, IOPS tối đa: `500`.
*   **Cold HDD (HDD Lưu trữ lạnh - sc1):**
    *   Thích hợp cho các dữ liệu ít khi truy cập (Infrequently accessed).
    *   Sử dụng khi **tối ưu hóa chi phí thấp nhất** là ưu tiên hàng đầu.
    *   Thông lượng tối đa: `250 MiB/s`, IOPS tối đa: `250`.

### E. Ghi nhớ cho kỳ thi (Exam Tips)
*   Workload cơ sở dữ liệu nhạy cảm hiệu năng -> Chọn **SSD (gp2/gp3 hoặc io1/io2)**.
*   Yêu cầu thông lượng lớn, dữ liệu lớn, chi phí thấp -> Chọn **HDD (st1/sc1)**.
*   Để đạt IOPS vượt quá **32,000**, bắt buộc phải sử dụng dòng máy **EC2 Nitro** đi kèm ổ **io1/io2**.

---

## 3. Tính năng gắn đồng thời EBS Multi-Attach (EBS Multi-Attach - io1 / io2 family)
**Câu hỏi:** EBS Multi-Attach là gì? Các dòng ổ đĩa nào hỗ trợ, các ràng buộc và trường hợp sử dụng thực tế của tính năng này là gì?

**Trả lời:**
*   **Định nghĩa:** **EBS Multi-Attach** là tính năng cho phép bạn gắn **cùng một ổ đĩa EBS** vào **nhiều máy chủ EC2 cùng một lúc**.
*   **Dòng ổ đĩa hỗ trợ:** Tính năng này chỉ khả dụng cho phân khúc SSD hiệu năng cao, cụ thể là dòng **io1** và **io2**.
*   **Quyền truy cập:** Tất cả các instances được gắn chung ổ đĩa đều có **đầy đủ quyền đọc và ghi (Read/Write permissions)** đồng thời với tốc độ cao.
*   **Các ràng buộc kỹ thuật (Crucial Limitations):**
    *   **Phạm vi AZ:** Chỉ có thể gắn đồng thời cho các instances nằm trong **cùng một Availability Zone (AZ)**. Không hỗ trợ gắn liên AZ.
    *   **Số lượng instances tối đa:** Giới hạn tối đa gắn vào **16 instances EC2** cùng một lúc (Con số 16 là thông số quan trọng cần nhớ cho kỳ thi).
    *   **Yêu cầu về hệ thống tệp tin (File System):** Để tránh việc xung đột và lỗi ghi dữ liệu (Data Corruption) khi nhiều máy chủ cùng thao tác ghi, ứng dụng bắt buộc phải sử dụng hệ thống tệp tin **có khả năng nhận biết phân cụm (Cluster-aware File System)** ví dụ như GFS2, OCFS2... **KHÔNG** sử dụng các hệ thống tệp tin tiêu chuẩn thông thường như XFS, EXT4...
*   **Trường hợp sử dụng phù hợp (Use Cases):**
    *   Tăng tính sẵn sàng (High Availability) cho các ứng dụng chạy cụm Linux (Clustered Linux applications) như Teradata.
    *   Các ứng dụng cần quản lý và đồng bộ các thao tác ghi đồng thời (Concurrent write operations).

---

## 4. Mã hóa ổ đĩa EBS (EBS Encryption)
**Câu hỏi:** EBS Encryption hoạt động như thế nào? Khi bật mã hóa thì những dữ liệu nào được bảo vệ? Quy trình chuyển đổi một ổ đĩa EBS từ không mã hóa sang có mã hóa (Unencrypted to Encrypted) diễn ra thế nào?

**Trả lời:**

### A. Cơ chế hoạt động của EBS Encryption
Khi bạn tạo một ổ đĩa EBS được mã hóa, hệ thống sẽ tự động thực hiện các thao tác sau:
*   **Dữ liệu tĩnh (Data at rest):** Toàn bộ dữ liệu lưu trữ bên trong volume được mã hóa.
*   **Dữ liệu truyền tải (Data in flight):** Dữ liệu di chuyển qua lại giữa EC2 instance và volume được mã hóa hoàn toàn.
*   **Bản sao lưu (Snapshots):** Tất cả các snapshot được chụp từ volume này đều được mã hóa tự động.
*   **Volume khôi phục:** Mọi volume được tạo ra từ các snapshot mã hóa đó cũng mặc định sẽ được mã hóa.
*   *Tính minh bạch:* Cơ chế mã hóa và giải mã được xử lý **hoàn toàn tự động và ẩn ngầm (transparently)** bởi EC2 và EBS. Ứng dụng không cần có bất kỳ thay đổi nào.
*   *Hiệu năng:* Tác động của mã hóa lên độ trễ truyền dữ liệu (latency) là **cực kỳ nhỏ (minimal)**, hầu như không thể nhận thấy.
*   *Thuật toán mã hóa:* Sử dụng dịch vụ AWS KMS để quản lý các khóa mã hóa tiêu chuẩn **AES-256**.

### B. Quy trình mã hóa một ổ đĩa EBS đang không mã hóa (Quy trình kinh điển trong phòng thi)
Nếu bạn có một ổ đĩa EBS đang hoạt động và không được mã hóa, để mã hóa nó, bạn phải đi qua quy trình 4 bước sau:
1.  **Chụp Snapshot:** Chụp một bản snapshot của ổ đĩa EBS không mã hóa đó (bản snapshot này mặc định cũng sẽ không mã hóa).
2.  **Sao chép Snapshot và Bật mã hóa (Copy & Encrypt):** Thực hiện sao chép bản snapshot vừa chụp (Copy Snapshot), trong quá trình cấu hình bản copy, tích chọn kích hoạt **Mã hóa (Encryption)** và chọn khóa KMS mong muốn.
3.  **Khôi phục Volume:** Tạo một EBS volume mới từ bản snapshot đã được mã hóa ở Bước 2. Lúc này, volume mới tạo ra chắc chắn sẽ được mã hóa.
4.  **Thay thế Volume gốc:** Gỡ ổ đĩa cũ không mã hóa ra khỏi EC2 instance (Detach) và gắn ổ đĩa đã mã hóa mới này vào máy chủ (Attach).

### C. Lối tắt trên giao diện AWS Console (Console Shortcut)
*   **Thực tế thao tác:** Trên giao diện AWS Console hiện tại, AWS cung cấp một lối tắt nhanh hơn:
    *   Từ bản snapshot không mã hóa ở Bước 1, bạn có thể chọn ngay **Actions** -> **Create volume from snapshot**.
    *   Tại màn hình tạo volume này, bạn có thể **bật mã hóa trực tiếp (on-the-fly)** cho volume mới và chọn khóa KMS. AWS sẽ tự động xử lý việc mã hóa trong lúc tạo volume mà bạn không cần tốn thêm bước copy snapshot.
    *   *Lưu ý:* Quy trình 4 bước kinh điển ở phần B vẫn là quy trình cốt lõi thường xuyên được hỏi trong các câu hỏi tình huống của kỳ thi SAA-C03.

---

## 5. Hướng dẫn thực hành: Tạo và cấu hình EBS Volume (Hands-on: Creating & Configuring EBS Volumes)
**Câu hỏi:** Các bước tạo một EBS Volume phụ và gắn nó vào máy chủ EC2 đang chạy? Chuyện gì xảy ra với ổ đĩa khi instance bị hủy (Terminate)?

**Trả lời:**

### A. Quy trình tạo và gắn EBS Volume
1.  **Kiểm tra AZ của Instance:** Trước khi tạo ổ đĩa, hãy vào mục **Instances**, kiểm tra Availability Zone của máy chủ bạn muốn gắn (ví dụ: `us-east-1a`).
2.  **Tạo Volume mới:**
    *   Tại menu trái Console, chọn **Elastic Block Store** -> Chọn **Volumes** -> Nhấp **Create volume**.
    *   **Volume type:** Chọn loại ổ đĩa (ví dụ: `gp3`).
    *   **Size (GiB):** Nhập dung lượng mong muốn (ví dụ: `2 GiB`).
    *   **Availability Zone:** Bắt buộc chọn trùng với AZ của máy chủ (ví dụ: `us-east-1a`). Nếu chọn AZ khác (như `us-east-1b`), khi gán bạn sẽ không tìm thấy máy chủ.
    *   Nhấp **Create volume**.
3.  **Gắn Volume vào Instance (Attach):**
    *   Đợi volume chuyển sang trạng thái `available`.
    *   Tích chọn volume -> Chọn **Actions** -> Chọn **Attach volume**.
    *   Chọn đúng Instance EC2 đang chạy của bạn ở AZ đó -> Nhấn **Attach volume**.
    *   Trạng thái volume sẽ chuyển sang `in-use`. Khi vào máy chủ, bạn sẽ thấy ổ đĩa mới đã được kết nối và sẵn sàng định dạng để lưu trữ.

### B. Kiểm tra hành vi hủy máy chủ (Termination Behavior)
*   **Thử nghiệm:** Tiến hành chọn instance thực hành -> Chọn **Instance state** -> Chọn **Terminate instance**.
*   **Kết quả quan sát:**
    *   Ổ đĩa Root mặc định (ví dụ: `8 GiB`) sẽ biến mất khỏi danh sách Volumes vì có thuộc tính *Delete on Termination* là True.
    *   Ổ đĩa gắn thêm (ví dụ: `2 GiB`) vẫn giữ nguyên trạng thái trong danh sách Volumes nhưng chuyển từ `in-use` sang `available`. Bạn có thể sử dụng lại ổ đĩa này để gắn sang máy chủ khác mà không bị mất dữ liệu.

---

## 6. Bản sao lưu EBS Snapshot (EBS Snapshots & Lifecycle Management)
**Câu hỏi:** EBS Snapshot là gì? Bản sao lưu này có đặc điểm gì nổi bật và các tính năng Recycle Bin, Archive Snapshot, Fast Snapshot Restore (FSR) hoạt động như thế nào?

**Trả lời:**
*   **Định nghĩa:** **EBS Snapshot** là một bản sao lưu tại một thời điểm nhất định (Point-in-time backup) của ổ đĩa EBS.
*   **Khuyến nghị khi chụp:** Bạn không bắt buộc phải ngắt kết nối (detach) ổ đĩa EBS ra khỏi EC2 instance khi chụp snapshot, nhưng AWS **khuyên dùng** (recommended) điều này để đảm bảo tính nhất quán tuyệt đối của dữ liệu (data consistency - tránh việc ứng dụng đang ghi dở dữ liệu vào đĩa).
*   **Đặc điểm sao lưu tăng trưởng (Incremental Backups):**
    *   Snapshot đầu tiên sẽ sao lưu toàn bộ dữ liệu của ổ đĩa.
    *   Các snapshot tiếp theo chỉ sao lưu những khối dữ liệu (blocks) bị thay đổi so với lần sao lưu trước đó. Điều này giúp tiết kiệm tối đa dung lượng lưu trữ và giảm chi phí.
*   **Lưu trữ lạnh (Archive Tier):**
    *   Nếu bạn có những bản snapshot ít khi dùng tới nhưng bắt buộc phải lưu trữ dài hạn (từ 90 ngày trở lên), bạn có thể chuyển chúng sang **Archive Tier**.
    *   Chi phí lưu trữ ở Archive Tier rẻ hơn tới **75%** so với Standard Tier. Tuy nhiên, thời gian để khôi phục (restore) dữ liệu từ Archive Tier sẽ mất từ **24 đến 72 giờ**.
*   **Thùng rác khôi phục (Recycle Bin - Retention Rules):**
    *   Để tránh việc vô tình xóa nhầm các bản snapshot quan trọng, bạn có thể thiết lập luật giữ lại (Retention Rules) trong Recycle Bin.
    *   Khi bật Recycle Bin, các snapshot bị xóa sẽ không mất đi ngay lập tức mà được giữ lại trong thùng rác từ 1 ngày đến 1 năm tùy theo cấu hình của bạn để phục vụ khôi phục khẩn cấp.
*   **Khôi phục nhanh (Fast Snapshot Restore - FSR):**
    *   *Vấn đề:* Mặc định, khi khôi phục một EBS volume từ snapshot, dữ liệu sẽ được tải một cách "lười biếng" (lazy loading) từ S3 khi có yêu cầu đọc ghi, gây ra độ trễ (latency) trong lần sử dụng đầu tiên.
    *   *Giải pháp:* FSR giúp **khởi tạo đầy đủ và ngay lập tức (force full initialization)** dữ liệu của volume từ snapshot, loại bỏ hoàn toàn độ trễ ở lần đầu truy cập.
    *   *Lưu ý:* Rất hữu ích cho các snapshot lớn cần khôi phục khẩn cấp hoặc khởi chạy máy chủ cực nhanh, nhưng tính năng này **rất đắt đỏ ($$$)** nên cần lưu ý khi dùng.
*   **Sao chép và Mã hóa Snapshot:**
    *   Bạn có thể sao chép một snapshot sang một Region khác để phục vụ cho chiến lược phòng chống thiên tai (Disaster Recovery).
    *   Trong quá trình sao chép, bạn có thể kích hoạt **Mã hóa (Encryption)** cho bản snapshot mới bằng cách chọn khóa KMS, ngay cả khi snapshot gốc chưa được mã hóa.

---

## 7. Hướng dẫn thực hành: Tạo, phục hồi, sao chép và bảo vệ EBS Snapshot (Hands-on: EBS Snapshots Operations & Recycle Bin)
**Câu hỏi:** Quy trình chụp snapshot từ một volume, khôi phục nó ở AZ khác, sao chép sang Region khác, cấu hình Recycle Bin bảo vệ snapshot và thực hiện khôi phục khi bị xóa nhầm như thế nào?

**Trả lời:**

### A. Chụp EBS Snapshot (Creating EBS Snapshot)
1. Vào trang **Elastic Block Store** -> **Volumes**, tích chọn ổ đĩa cần sao lưu (ví dụ: ổ đĩa phụ dung lượng `2 GiB`).
2. Chọn **Actions** -> Chọn **Create snapshot**.
3. Nhập mô tả (Description) (ví dụ: `DemoSnapshots`) -> Chọn **Create snapshot**.
4. Vào mục **Elastic Block Store** -> **Snapshots** ở menu trái để theo dõi tiến độ. Khi trạng thái (Status) chuyển sang `Completed` và hiển thị `100%`, bản sao lưu đã sẵn sàng sử dụng.

### B. Khôi phục Volume từ Snapshot sang Availability Zone (AZ) khác (Restoring Volume to another AZ)
1. Vào mục **Snapshots**, tích chọn snapshot vừa tạo.
2. Chọn **Actions** -> Chọn **Create volume from snapshot**.
3. Tại trang cấu hình:
    * **Volume Type / Size:** Có thể giữ nguyên loại ổ đĩa (ví dụ: `gp2` hoặc chuyển sang `gp3`) và nâng dung lượng lớn hơn nếu cần.
    * **Availability Zone:** Chọn AZ mới mà bạn muốn di chuyển dữ liệu đến (ví dụ: chuyển từ `eu-west-1a` sang `eu-west-1b`).
    * **Encryption / Tags:** Bạn có thể bật mã hóa cho volume khôi phục hoặc thêm các tag quản lý.
    * Chọn **Create volume**.
4. Quay lại trang **Volumes**, bạn sẽ thấy một ổ đĩa mới đã được khôi phục thành công ở AZ mục tiêu (`eu-west-1b`) từ snapshot, sẵn sàng gắn vào các máy chủ ở AZ này.

### C. Sao chép Snapshot sang Region khác để dự phòng thảm họa (Copying Snapshot to another Region - Disaster Recovery)
1. Tại trang quản lý **Snapshots**, chuột phải (hoặc chọn **Actions**) vào snapshot mong muốn -> Chọn **Copy snapshot**.
2. Chọn **Destination Region** (Region đích) mà bạn muốn sao lưu dữ liệu sang (phục vụ chiến lược phòng chống thiên tai - Disaster Recovery).
3. Có thể cấu hình đổi mô tả hoặc kích hoạt **Mã hóa (Encryption)** bằng khóa KMS của Region đích ngay cả khi bản snapshot gốc chưa được mã hóa.
4. Nhấn **Copy snapshot** để bắt đầu tiến trình copy liên Region.

### D. Cấu hình Thùng rác bảo vệ Snapshot (Recycle Bin - Retention Rules)
Để ngăn chặn việc vô tình xóa nhầm hoặc bị tấn công xóa sạch các bản sao lưu, hãy thiết lập **Recycle Bin**:
1. Tìm kiếm và truy cập dịch vụ **Recycle Bin** trên thanh công cụ AWS Console.
2. Chọn mục **Retention rules** ở menu trái -> Click **Create retention rule**.
3. Cấu hình rule mới:
    * **Rule name:** Đặt tên gợi nhớ (ví dụ: `DemoRetentionRule`).
    * **Resource type:** Chọn **EBS snapshot** (hoặc *AMI*).
    * **Rule application:** Chọn **Apply to all resources** (áp dụng cho tất cả snapshot trong Region) hoặc chỉ định tag cụ thể.
    * **Retention period:** Nhập thời gian giữ lại (ví dụ: `1 day` - giữ 1 ngày, hoặc tối đa 365 ngày).
    * **Rule lock setting:** Chọn **Unlocked** (không khóa) để có thể sửa hoặc xóa rule này bất cứ lúc nào (chỉ chọn *Locked* khi muốn cấu hình chặt chẽ không cho phép ai tắt rule này đi, kể cả tài khoản admin).
4. Click **Create Retention Rule**.

### E. Thử nghiệm Xóa Snapshot & Khôi phục từ Recycle Bin (Deleting & Recovering Snapshot)
1. Quay lại trang **Snapshots** trong EC2 Console.
2. Chọn snapshot của bạn -> Click **Actions** -> Chọn **Delete snapshot** -> Xác nhận xóa. Snapshot sẽ biến mất khỏi danh sách quản lý thông thường của EC2.
3. Chuyển sang dịch vụ **Recycle Bin** -> Chọn mục **Resources** ở menu trái.
4. Click **Refresh**, bạn sẽ thấy bản snapshot vừa bị xóa xuất hiện tại đây cùng thông tin ngày giờ dự kiến bị xóa vĩnh viễn.
5. Tích chọn snapshot đó -> Click **Recover** -> Xác nhận **Recover resources**.
6. Quay lại trang **Snapshots** trong EC2 Console -> Nhấn refresh, bản snapshot đã xuất hiện trở lại ở trạng thái hoạt động bình thường (`Completed`).

### F. Lưu ý thực hành về Thay đổi Storage Tier (Archive Snapshot)
* Tại trang chi tiết Snapshot, bạn có thể kiểm tra **Storage Tier** (mặc định luôn là `Standard`).
* Nếu muốn tiết kiệm chi phí cho các bản snapshot lưu trữ lâu ngày (ít sử dụng), bạn chọn **Actions** -> **Archive snapshot**.
* Khi chuyển sang Archive Tier, dữ liệu sẽ ở dạng nén/lạnh, chi phí rẻ hơn 75%, nhưng hãy nhớ rằng nếu muốn dùng lại, bạn sẽ mất từ **24 đến 72 giờ** để khôi phục nó về Standard Tier trước khi có thể tạo Volume.

---

## 8. Ảnh máy ảo AMI (Amazon Machine Image)
**Câu hỏi:** AMI là gì? Đặc điểm hoạt động, phạm vi (Scope), phân loại AMI và quy trình hoạt động (AMI Process) từ một EC2 instance diễn ra như thế nào?

**Trả lời:**
*   **Định nghĩa:** **AMI (Amazon Machine Image)** là một gói đóng gói đại diện cho sự tùy chỉnh (customization) của một máy chủ EC2. Nó chứa cấu hình hệ điều hành (OS), các phần mềm cài sẵn (software), các cấu hình hệ thống (configuration), công cụ giám sát (monitoring tools) và dữ liệu ổ đĩa.
*   **Ưu điểm vượt trội:**
    *   **Tốc độ khởi động và cấu hình nhanh hơn (Faster boot / configuration time):** Do tất cả các phần mềm cần thiết đã được đóng gói sẵn (pre-packaged), máy chủ mới tạo ra từ AMI sẽ hoạt động ngay lập tức mà không cần tốn thời gian chạy script cài đặt (như User Data) từ đầu.
    *   **Khả năng nhân bản (Cloning):** Giúp tạo ra hàng trăm máy chủ có cấu hình giống hệt nhau chỉ trong vài giây. Đây là nền tảng cốt lõi giúp hệ thống **Auto Scaling Group** tự động mở rộng quy mô khi tải cao.
*   **Phạm vi hoạt động (Scope):**
    *   Mỗi AMI được xây dựng và **giới hạn trong một Region cụ thể** (Specific Region).
    *   *Mở rộng:* Tuy nhiên, bạn hoàn toàn có thể **sao chép (Copy) AMI** sang các Region khác để tận dụng cơ sở hạ tầng toàn cầu của AWS hoặc triển khai hệ thống ở khu vực khác.
*   **Các loại nguồn AMI (AMI Sources):**
    1.  **Public AMI (Do AWS cung cấp):** Phổ biến nhất là *Amazon Linux 2 AMI* hoặc các bản phân phối Linux/Windows sạch do AWS duy trì.
    2.  **Your own AMI (Do bạn tự tạo):** Bạn tự cài đặt phần mềm, tùy chỉnh và tự bảo trì/cập nhật phiên bản.
    3.  **AWS Marketplace AMI (Chợ ứng dụng của bên thứ ba):** Do các nhà cung cấp phần mềm bên thứ ba đóng gói sẵn (chứa ứng dụng doanh nghiệp như SAP, WordPress, v.v.) và bán/chia sẻ. Bạn có thể mua để tiết kiệm thời gian, và thậm chí bạn có thể xây dựng doanh nghiệp bán AMI của riêng mình trên chợ này.
*   **Quy trình tạo AMI từ một EC2 Instance (AMI Process):**
    1.  **Khởi chạy & Tùy chỉnh (Start & Customize):** Khởi chạy một instance EC2 thông thường và cài đặt mọi phần mềm, cấu hình cần thiết.
    2.  **Dừng Instance (Stop for Data Integrity):** AWS khuyên bạn nên **dừng máy chủ** trước khi tạo ảnh để đảm bảo tính nhất quán và toàn vẹn dữ liệu (tránh ứng dụng đang ghi dở dữ liệu vào đĩa).
    3.  **Tạo AMI (Build AMI):** Tiến hành tạo AMI. Quá trình này cũng sẽ **tự động tạo các bản EBS Snapshots** chạy ngầm phía sau tương ứng với các ổ đĩa của instance.
    4.  **Nhân bản máy chủ (Launch from AMI):** Sử dụng AMI đã tạo để khởi chạy các instance mới ở các Availability Zone khác (ví dụ: chuyển cấu hình từ `us-east-1a` sang `us-east-1b`) hoặc ở Region khác (sau khi copy AMI), tạo ra các bản sao hoàn hảo của máy chủ ban đầu.

---

## 9. Hướng dẫn thực hành: Tạo và khởi chạy EC2 từ AMI (Hands-on: Creating AMI & Launching Instances)
**Câu hỏi:** Các bước thực hành cài đặt phần mềm, đóng gói máy chủ EC2 thành AMI, khởi chạy instance mới từ AMI này với User Data tối giản để kiểm tra tốc độ boot diễn ra thế nào?

**Trả lời:**

### A. Khởi chạy Instance gốc và cài đặt Apache (Launching & Configuring Source Instance)
1. Khởi chạy một instance EC2 mới:
    * **OS Image:** Chọn *Amazon Linux 2*.
    * **Instance Type:** Chọn `t2.micro`.
    * **Security Group:** Chọn Security Group đã có sẵn (ví dụ: `launch-wizard-1` - cho phép cổng 80 HTTP).
    * **Advanced Details -> User Data:** Nhập script cài đặt và kích hoạt Apache Web Server (HTTPD), nhưng **không tạo file `index.html`** để hệ thống hiển thị trang kiểm tra mặc định của Apache:
      ```bash
      #!/bin/bash
      # Cập nhật hệ thống và cài đặt Apache
      yum update -y
      yum install -y httpd
      systemctl start httpd
      systemctl enable httpd
      ```
2. Nhấn **Launch instance**.
3. **Kiểm tra trạng thái:** Đợi khoảng 1-2 phút sau khi máy chủ ở trạng thái `running` để User Data hoàn thành cài đặt. Truy cập Public IP của máy chủ bằng giao thức HTTP, bạn sẽ thấy trang kiểm tra mặc định (**Apache HTTP Server Test Page**) hiển thị. Điều này xác nhận Apache đã được cài đặt và chạy thành công.

### B. Tạo Custom AMI từ Instance gốc (Creating Custom AMI)
1. Tại danh sách **Instances**, tích chọn instance gốc vừa tạo.
2. Chọn **Actions** (hoặc chuột phải) -> **Image and templates** -> Chọn **Create image**.
3. Cấu hình tạo ảnh:
    * **Image name:** Đặt tên ảnh máy ảo (ví dụ: `demo-image`).
    * **Description:** Thêm mô tả nếu muốn, các thông số ổ đĩa khác giữ nguyên mặc định.
    * Click **Create image**.
4. Vào mục **Images** -> **AMIs** ở menu trái để theo dõi trạng thái. Ảnh sẽ bắt đầu ở trạng thái `pending` (đang tạo và chạy ngầm snapshot ổ đĩa). Hãy kiên nhẫn đợi vài phút cho đến khi trạng thái chuyển sang `available`.

### C. Khởi chạy Instance mới từ Custom AMI (Launching Instance from Custom AMI)
1. Tại trang quản lý **AMIs**, chọn `demo-image` -> Click **Launch instance from AMI** (hoặc vào trang tạo máy chủ mới, tại mục OS Image chọn tab **My AMIs** -> chọn `demo-image`).
2. Đặt tên cho máy chủ mới (ví dụ: `From AMI`).
3. **Security Group:** Tiếp tục chọn Security Group `launch-wizard-1`.
4. **Advanced Details -> User Data:** Vì Apache (HTTPD) đã được cài đặt và kích hoạt sẵn trong custom AMI, bạn **không cần cài lại HTTPD** nữa. Bạn chỉ cần nhập script tạo file trang chủ `index.html` của riêng bạn:
  ```bash
  #!/bin/bash
  # Chỉ tạo file trang chủ, không cần cài đặt lại httpd
  echo "<h1>Hello World from $(hostname -f)</h1>" > /var/www/html/index.html
  ```
5. Nhấp **Launch instance**.

### D. Kiểm chứng tốc độ khởi động và dọn dẹp (Verifying & Cleanup)
1. Đợi máy chủ `From AMI` chạy. Truy cập vào Public IP của nó bằng HTTP.
2. **Kết quả:** Trang web hiển thị thông điệp "Hello World from [hostname]" ngay lập tức. Quá trình này diễn ra nhanh hơn rất nhiều so với máy chủ đầu tiên vì hệ thống không mất thời gian tải và cài đặt Apache từ internet nữa. Đây chính là sức mạnh của AMI trong việc tối ưu hóa thời gian khởi động (boot time) của hệ thống Auto Scaling.
3. **Dọn dẹp:** Sau khi thực hành xong, chọn cả hai instances và thực hiện **Terminate** (hủy) chúng để tránh phát sinh chi phí.

---

## 10. Bộ lưu trữ cục bộ EC2 Instance Store (EC2 Instance Store)
**Câu hỏi:** EC2 Instance Store là gì? Đặc điểm hoạt động, hiệu năng, các hạn chế và các trường hợp sử dụng phù hợp của nó là gì?

**Trả lời:**
*   **Định nghĩa:** **EC2 Instance Store** là ổ đĩa cứng vật lý (thường là SSD) được gắn **trực tiếp** vào máy chủ vật lý chứa instance EC2 của bạn.
*   **Điểm khác biệt cốt lõi:**
    *   *EBS Volumes* là các ổ đĩa mạng (network drives) truyền tải dữ liệu qua mạng nội bộ của AWS, do đó hiệu năng (độ trễ, IOPS) sẽ có những giới hạn nhất định.
    *   *EC2 Instance Store* kết nối vật lý trực tiếp với bo mạch của máy chủ chạy instance, loại bỏ độ trễ truyền mạng để đạt hiệu năng đọc/ghi tối đa.
*   **Hiệu năng vượt trội (High-performance):**
    *   Thích hợp cho các tác vụ yêu cầu tốc độ đĩa cực lớn (cực kỳ cao).
    *   *Ví dụ minh họa:* Các instance dòng `i3` (như `i3.16xlarge` hoặc `i3.metal`) sử dụng Instance Store có thể đạt tới **3.3 triệu Random Read IOPS** và **1.4 triệu Random Write IOPS**. Trong khi đó, ổ EBS gp2 thông thường chỉ đạt tối đa **32,000 IOPS**.
*   **Hạn chế cực kỳ quan trọng - Lưu trữ phù du (Ephemeral Storage):**
    *   Dữ liệu trên Instance Store **sẽ bị MẤT HOÀN TOÀN** khi instance bị **Stop** (Dừng) hoặc **Terminate** (Hủy).
    *   *Lý do:* Khi bạn Stop instance, AWS sẽ giải phóng tài nguyên trên máy chủ vật lý cũ. Khi bạn Start lại, instance có thể được chuyển sang một máy chủ vật lý khác, nơi ổ đĩa cục bộ hoàn toàn trống sạch.
    *   *Lưu ý:* Thao tác **Reboot** (Khởi động lại) thông thường sẽ **KHÔNG làm mất dữ liệu**.
    *   Có rủi ro mất dữ liệu nếu phần cứng máy chủ vật lý bị lỗi đột ngột.
*   **Trách nhiệm sao lưu (Backups & Replication):**
    *   Việc sao lưu, dự phòng và nhân bản dữ liệu (Replication) hoàn toàn thuộc về trách nhiệm của khách hàng (User) theo mô hình trách nhiệm chia sẻ. Bạn phải tự thiết lập sao lưu sang S3/EBS hoặc sử dụng cơ chế RAID, cluster ở tầng ứng dụng nếu muốn đảm bảo an toàn.
*   **Các trường hợp sử dụng phù hợp (Use Cases):**
    *   Làm bộ nhớ đệm (Cache).
    *   Bộ nhớ đệm trung chuyển (Buffer).
    *   Dữ liệu tạm thời (Scratch data / Temporary content).
    *   *Chống chỉ định:* Không dùng cho các dữ liệu quan trọng cần lưu trữ lâu dài và bền vững (như cơ sở dữ liệu chính, file cấu hình hệ thống).

---

## 11. Hệ thống tệp tin mạng EFS (Amazon Elastic File System - EFS)
**Câu hỏi:** Amazon EFS là gì? So sánh cơ bản sự khác nhau giữa EFS và EBS? Hãy trình bày chi tiết về các chế độ hiệu năng (Performance Modes), chế độ thông lượng (Throughput Modes), các lớp lưu trữ (Storage Classes), chính sách vòng đời (Lifecycle Policies) và các tùy chọn về tính sẵn sàng (Availability & Durability) của EFS?

**Trả lời:**

### A. Sơ đồ bài giảng EFS (Slide Visuals)
````carousel
![Slide 1: Sơ đồ hoạt động và cơ chế kết nối Multi-AZ của Amazon EFS](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/media__1781452023726.png)
<!-- slide -->
![Slide 2: Tổng quan về tính năng, giao thức NFSv4.1 và khả năng tương thích của EFS](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/media__1781452041683.png)
<!-- slide -->
![Slide 3: Các chế độ hiệu năng (Performance Mode) và chế độ thông lượng (Throughput Mode) của EFS](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/media__1781452059459.png)
<!-- slide -->
![Slide 4: Các lớp lưu trữ (Storage Classes), chính sách vòng đời và các tùy chọn về độ bền/tính sẵn sàng của EFS](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/media__1781452072000.png)
````

### B. Tổng quan và Đặc điểm cốt lõi của Amazon EFS
*   **Định nghĩa:** **Amazon EFS (Elastic File System)** là một dịch vụ hệ thống tệp tin mạng (Managed Network File System - NFS) được AWS quản lý hoàn toàn, cho phép bạn chia sẻ tài nguyên lưu trữ chung giữa **hàng nghìn máy chủ EC2 cùng một lúc**.
*   **Đặc điểm hoạt động:**
    *   **Giao thức:** Sử dụng giao thức mạng tiêu chuẩn **NFSv4.1** (hoặc NFSv4).
    *   **Khả năng tương thích:** Chỉ tương thích với các hệ điều hành dựa trên **Linux** (POSIX-compliant), **KHÔNG** tương thích với hệ điều hành Windows.
    *   **Mã hóa:** Hỗ trợ mã hóa dữ liệu tĩnh (Encryption at rest) sử dụng AWS KMS (AES-256) và mã hóa khi truyền tải (Encryption in transit).
    *   **Bảo mật:** Điều khiển quyền truy cập mạng thông qua **Security Groups** (sử dụng cổng NFS mặc định **2049**).
    *   **Co giãn tự động (Scale):** Tự động mở rộng hoặc thu hẹp dung lượng lưu trữ dựa trên lượng tệp tin hiện có mà không cần cấu hình trước (Scale tự động lên cấp petabyte). Hỗ trợ thông lượng lên tới **10+ GB/s** với hàng nghìn kết nối đồng thời từ các máy khách.
    *   **Chi phí:** Tính phí theo dung lượng sử dụng thực tế (pay-per-use), không phải trả tiền trước cho không gian đĩa trống. Tuy nhiên, đơn giá lưu trữ của EFS khá cao (khoảng **gấp 3 lần** chi phí của một ổ đĩa EBS gp2 thông thường).
    *   **Trường hợp sử dụng phù hợp:** Quản trị nội dung (Content management), Web serving (như WordPress Cluster), chia sẻ dữ liệu nội bộ (Data sharing), các thư mục người dùng chung.

### C. Các chế độ hiệu năng (Performance Modes)
Bạn phải xác định chế độ hiệu năng tại thời điểm tạo EFS:
1.  **General Purpose (Mặc định - Đa dụng):**
    *   Được thiết kế để tối ưu hóa thời gian phản hồi với độ trễ tối thiểu (latency-sensitive).
    *   *Trường hợp sử dụng:* Web servers, CMS, các ứng dụng văn phòng chia sẻ file.
2.  **Max I/O (Tối đa hóa I/O):**
    *   Chấp nhận độ trễ trung bình cao hơn một chút nhưng mang lại mức thông lượng ghi đọc cực cao và khả năng xử lý song song vượt trội.
    *   *Trường hợp sử dụng:* Các workload xử lý dữ liệu lớn (Big Data), phân tích luồng dữ liệu video (Media processing), phân tích khoa học hoặc học máy.

### D. Các chế độ thông lượng (Throughput Modes)
Xác định cách EFS cung cấp và mở rộng băng thông truyền tải dữ liệu:
1.  **Bursting (Tích lũy & Tăng tốc):**
    *   Thông lượng mặc định tỉ lệ thuận với dung lượng lưu trữ đang có (ví dụ: ổ đĩa 1 TB sẽ có thông lượng nền tảng 50 MiB/s và có khả năng tăng tốc lên tới 100 MiB/s khi cần).
2.  **Provisioned (Cấp phát trước):**
    *   Cho phép bạn tự cấu hình mức thông lượng mong muốn (ví dụ: thiết lập cố định 1 GiB/s) bất kể dung lượng dữ liệu đang lưu trữ là bao nhiêu.
3.  **Elastic (Tự động co giãn linh hoạt):**
    *   Tự động tăng/giảm thông lượng theo nhu cầu đọc ghi thực tế của ứng dụng (hỗ trợ tối đa lên đến 3 GiB/s đối với thao tác đọc và 1 GiB/s đối với thao tác ghi).
    *   *Trường hợp sử dụng:* Rất lý tưởng cho các tác vụ tải làm việc (workloads) không thể dự đoán trước hoặc biến thiên liên tục.

### E. Lớp lưu trữ & Chính sách vòng đời (Storage Classes & Lifecycle Policies)
Để tiết kiệm chi phí tối ưu (có thể lên tới **90%**), EFS cung cấp các lớp lưu trữ (Storage Tiers) và tự động di chuyển dữ liệu thông qua Chính sách vòng đời (Lifecycle Policies):
*   **Các lớp lưu trữ (Storage Tiers):**
    *   **EFS Standard (Lớp tiêu chuẩn):** Phù hợp với các tệp tin được truy cập thường xuyên (frequently accessed).
    *   **EFS Infrequent Access (EFS-IA - Lớp ít truy cập):** Chi phí lưu trữ rẻ hơn rất nhiều, nhưng bạn sẽ phải trả phí cho mỗi lần đọc/xuất dữ liệu (retrieval cost).
    *   **EFS Archive (Lớp lưu trữ lưu trữ lâu dài):** Dành cho dữ liệu cực kỳ ít khi truy cập (chỉ vài lần trong năm). Chi phí lưu trữ rẻ hơn đến 50% so với lớp IA.
*   **Chính sách vòng đời (Lifecycle Policies):**
    *   Tự động di chuyển các tệp tin sang lớp IA hoặc Archive sau một khoảng thời gian nhất định không có hoạt động truy cập (ví dụ: chuyển một tệp tin từ Standard sang IA sau 60 ngày không có lượt truy cập).

### F. Tính sẵn sàng & Độ bền dữ liệu (Availability & Durability Options)
1.  **Regional (Multi-AZ):**
    *   Dữ liệu được sao chép và phân tán trên **nhiều Availability Zones** khác nhau trong Region.
    *   *Ưu điểm:* Độ bền và tính sẵn sàng cao nhất, chống chịu thiên tai tốt, phù hợp nhất cho môi trường Production.
2.  **One Zone (Single AZ):**
    *   Dữ liệu chỉ nằm trong **một Availability Zone duy nhất**.
    *   *Ưu điểm:* Tiết kiệm chi phí đáng kể.
    *   *Hỗ trợ:* Vẫn hỗ trợ tự động sao lưu (backups) mặc định và tương thích với lớp One Zone-IA (Infrequent Access). Thích hợp cho môi trường thử nghiệm/phát triển (Dev/Test).

---

## 12. Hướng dẫn thực hành: Cấu hình và gắn EFS cho nhiều Instance (Hands-on: Configuring & Mounting EFS)
**Câu hỏi:** Quy trình thực tế từng bước để khởi tạo EFS (cấu hình Regional, chính sách vòng đời nâng cao, thông lượng Elastic), tạo Security Group (`sg-efs-demo` cổng 2049) và gắn chung EFS vào hai EC2 Instance ở hai Availability Zones khác nhau (`eu-west-1a` và `eu-west-1b`) qua giao diện EC2 Console mới như thế nào?

**Trả lời:**

### A. Thiết lập Security Group cho EFS (Security Group Setup)
Vì EFS là hệ thống lưu trữ mạng giao tiếp qua cổng NFS tiêu chuẩn (cổng 2049), ta cần tạo nhóm bảo mật riêng để kiểm soát kết nối:
1. Vào trang quản trị **EC2 Console** -> Chọn **Security Groups** ở menu trái -> Chọn **Create security group**.
2. Thiết lập thông tin cơ bản:
   - **Security group name:** `sg-efs-demo` (hoặc `efs-demo-sg`).
   - **Description:** `EFS Demo SG`.
   - **VPC:** Chọn Default VPC của Region đang thực hành.
3. **Inbound rules (Quy tắc đầu vào):** Ban đầu **để trống hoàn toàn** (không thêm rule nào) -> Nhấp **Create security group**.
4. *Sau khi khởi chạy EC2:* Ta sẽ quay lại chỉnh sửa (Edit inbound rules) của `sg-efs-demo`, thêm rule cho phép:
   - **Type:** **NFS** (Port `2049`).
   - **Source:** Chọn chính **Security Group của EC2 Instance** (được tạo ở bước sau) để chỉ cho phép các máy chủ EC2 hợp lệ truy cập.

### B. Tạo Amazon EFS File System (Creating EFS File System)
1. Trên thanh công cụ AWS, tìm kiếm và truy cập dịch vụ **EFS** (Elastic File System) -> Chọn **Create file system**.
2. Để cấu hình chi tiết nâng cao thay vì cấu hình nhanh mặc định, hãy nhấp chọn **Customize**:
   - **Name:** Có thể để trống hoặc đặt tên gợi nhớ.
   - **File system type:** Chọn **Regional** (Multi-AZ) để dữ liệu được nhân bản trên nhiều Availability Zones khác nhau (phù hợp với Production). Tránh chọn *One Zone* vì dữ liệu sẽ bị giới hạn trong 1 AZ duy nhất, không an toàn nếu AZ đó gặp thảm họa.
   - **Automatic backups:** Chọn **Enable** (Khuyến nghị bật để tự động sao lưu).
   - **Lifecycle management (Quản lý vòng đời):** Cấu hình tự động chuyển đổi dữ liệu qua các lớp lưu trữ để tiết kiệm chi phí:
     - *Transition into IA:* Chọn **30 days since last access** (Chuyển sang lớp ít truy cập sau 30 ngày không sử dụng).
     - *Transition into Archive:* Chọn **90 days since last access** (Chuyển tiếp sang lớp lưu trữ Archive sau 90 ngày không sử dụng).
     - *Transition out of IA/Archive:* Chọn **On first access, transition back to standard** (Tự động chuyển tệp về lớp Standard ngay khi có lượt truy cập đầu tiên để tối ưu hiệu năng đọc ghi tiếp theo).
   - **Encryption:** Đảm bảo tích chọn **Enable encryption** (Mã hóa dữ liệu tĩnh qua KMS).
   - **Throughput mode:** Chọn **Elastic** (Recommended - Tự động co giãn thông lượng dựa trên nhu cầu thực tế của ứng dụng, thanh toán theo dung lượng đọc ghi thực tế, không cần cấu hình thủ công).
   - **Performance mode:** Chế độ mặc định là **General Purpose** (Độ trễ thấp, tối ưu cho ứng dụng thông thường). Chế độ *Max I/O* chỉ hiển thị khi chọn thông lượng Bursting hoặc Provisioned.
   - Chọn **Next**.
3. **Network access (Cấu hình Mount targets):**
   - Chọn Default VPC.
   - Tại màn hình liệt kê các Availability Zones và Subnets tương ứng, ở cột **Security groups**, xóa nhóm bảo mật mặc định đi và chọn đúng nhóm bảo mật `sg-efs-demo` ta đã tạo ở phần A.
   - Nhấn **Next** -> Bỏ qua phần File system policy (để trống) -> Nhấn **Next** -> Nhấn **Create**.
4. Đợi khoảng 1-2 phút để trạng thái của EFS chuyển sang `Available`. Lúc này, dung lượng ban đầu hiển thị khoảng `6 KB` (chi phí lưu trữ bằng 0).

### C. Khởi chạy 2 EC2 Instance ở 2 AZs khác nhau và gắn EFS (Launching & Mounting EFS via EC2 Wizard)
Nhờ giao diện EC2 Console thế hệ mới, ta có thể gắn trực tiếp EFS vào EC2 ngay tại thời điểm khởi chạy máy chủ mà không cần cài đặt và gõ lệnh thủ công:
1. **Khởi chạy Instance A (AZ a):**
   - Vào mục **Instances** -> Chọn **Launch instances**.
   - **Name:** Đặt tên `Instance A`.
   - **OS Image:** Chọn `Amazon Linux 2`.
   - **Instance type:** Chọn `t2.micro` (Free Tier).
   - **Key pair:** Chọn `Proceed without a key pair` (Chúng ta sẽ kết nối qua **EC2 Instance Connect**).
   - **Network settings:** Nhấp **Edit**:
     - *Subnet:* Chọn subnet thuộc Availability Zone **eu-west-1a** (hoặc `us-east-1a` tùy Region của bạn).
     - *Firewall (security groups):* Chọn tạo nhóm bảo mật mới cho phép SSH truy cập từ mọi nơi (`Anywhere - 0.0.0.0/0`).
   - **Configure storage (Cấu hình lưu trữ):**
     - Giữ nguyên ổ Root `8 GB gp2`.
     - Nhấp nút **Edit** ở góc phải -> Tìm đến mục **File system** -> Nhấp **Add shared file system**.
     - Chọn EFS File System vừa tạo.
     - *Lưu ý cơ chế tự động:* Khi bạn chọn thêm EFS tại đây, AWS sẽ tự động tạo cấu hình User Data chạy ngầm (cài đặt công cụ `amazon-efs-utils` và chạy lệnh mount tự động vào thư mục `/mnt/efs/fs1` khi boot máy). Đồng thời, AWS cũng tự động tạo các nhóm bảo mật phụ (ví dụ: `efs-sg-1`, `efs-sg-2`) để tự thông cổng 2049 qua lại giữa EC2 và EFS.
   - Nhấp **Launch instance**.
2. **Khởi chạy Instance B (AZ b):**
   - Thực hiện tương tự như Instance A (tên: `Instance B`, OS Amazon Linux 2, không key pair, cấu hình gắn cùng một EFS file system).
   - **Điểm khác biệt duy nhất:** Tại Network settings -> Subnet, chọn Availability Zone **eu-west-1b** (hoặc `us-east-1b` để đặt máy chủ ở AZ khác).
   - Nhấp **Launch instance**.

### D. Kiểm tra đồng bộ dữ liệu thời gian thực giữa 2 AZs (Verification)
1. Đợi hai máy chủ chuyển sang trạng thái `Running`.
2. **Thao tác trên Instance A:**
   - Tích chọn `Instance A` -> Nhấp **Connect** -> Chọn **EC2 Instance Connect** -> Nhấp **Connect** để mở cửa sổ terminal trên trình duyệt.
   - Di chuyển vào thư mục EFS đã được mount tự động:
     ```bash
     cd /mnt/efs/fs1
     ```
   - Tạo một tệp tin mới và ghi nội dung vào đó:
     ```bash
     sudo touch hello.txt
     sudo sh -c 'echo "Xin chao tu Instance A - AZ 1A" > hello.txt'
     ```
3. **Thao tác trên Instance B:**
   - Tích chọn `Instance B` -> Nhấp **Connect** -> Chọn **EC2 Instance Connect** -> Nhấp **Connect**.
   - Di chuyển vào thư mục mount của EFS và kiểm tra sự tồn tại của tệp tin:
     ```bash
     cd /mnt/efs/fs1
     ls -la
     cat hello.txt
     ```
   - **Kết quả thực tế:** Bạn sẽ nhìn thấy tệp tin `hello.txt` xuất hiện ngay lập tức và in ra dòng chữ *"Xin chao tu Instance A - AZ 1A"*.
   - **Kết luận:** Mặc dù `Instance A` và `Instance B` nằm ở hai Availability Zones vật lý hoàn toàn tách biệt, dữ liệu được ghi từ máy chủ này đã xuất hiện tức thì trên máy chủ kia nhờ cơ chế lưu trữ chia sẻ mạng qua EFS.

---

## 13. So sánh EBS vs EFS vs Instance Store (Comparison: EBS vs EFS vs Instance Store)
**Câu hỏi:** Các điểm khác biệt cốt lõi về cơ chế hoạt động, khả năng di trú, ràng buộc hiệu năng và hành vi backup của EBS, EFS và Instance Store là gì? Bảng so sánh tổng quan giữa ba dịch vụ lưu trữ này?

**Trả lời:**

### A. Sơ đồ bài giảng so sánh EBS và EFS (Slide Visual)
![Sơ đồ so sánh cơ chế hoạt động, di chuyển liên AZ của EBS và EFS](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/media__1781452563492.png)

### B. Các điểm lưu ý cốt lõi và kiến thức phòng thi (Key Exam Takeaways)

#### 1. Ổ lưu trữ mạng EBS (Elastic Block Store)
*   **Ràng buộc AZ (AZ Locked):** EBS Volume bị khóa cứng trong một Availability Zone cụ thể. Một instance ở AZ 2 không bao giờ có thể kết nối trực tiếp đến một EBS Volume ở AZ 1.
*   **Di chuyển liên AZ (Cross-AZ Migration):** Để chuyển ổ đĩa EBS sang AZ khác, bạn phải thực hiện quy trình: **Chụp Snapshot -> Khôi phục thành Volume mới ở AZ mục tiêu**.
*   **Sự độc lập về hiệu năng:**
    *   Với **gp2**: Dung lượng và IOPS liên kết chặt chẽ (tăng dung lượng sẽ tự động tăng IOPS).
    *   Với **gp3** và **io1/io2**: IOPS và Throughput có thể được cấu hình tăng/giảm **độc lập** với dung lượng đĩa.
*   **Tác động I/O khi Backup (Backup I/O Impact):** Thao tác chụp snapshot của EBS sử dụng tài nguyên đọc ghi (I/O) của đĩa. Do đó, **tránh chụp snapshot khi hệ thống đang xử lý lưu lượng truy cập cao (peak traffic)** để không làm suy giảm hiệu năng ứng dụng.
*   **Hành vi khi hủy máy chủ:** Mặc định ổ Root (hệ điều hành) sẽ bị xóa khi Terminate instance, nhưng có thể cấu hình tắt tính năng này (*Delete on Termination* = False).

#### 2. Hệ thống tệp tin mạng EFS (Elastic File System)
*   **Chia sẻ đa AZ (Multi-AZ Shared Storage):** Hoạt động như một NAS mạng, cho phép hàng trăm instance kết nối đồng thời từ nhiều AZ khác nhau qua các Mount Targets.
*   **Tương thích hệ điều hành:** Chỉ hỗ trợ **Linux (POSIX)**.
*   **Tối ưu hóa chi phí:** Mặc dù đơn giá lưu trữ cao hơn EBS khoảng 3 lần, EFS cho phép cấu hình tự động chuyển đổi dữ liệu qua các lớp lưu trữ (**Storage Tiers**) để tiết kiệm tới 90% chi phí.

#### 3. Bộ lưu trữ cục bộ Instance Store (Local Storage)
*   **Kết nối vật lý:** Gắn trực tiếp vào bo mạch phần cứng của máy chủ EC2 vật lý nên mang lại tốc độ và IOPS cực cao với độ trễ cực thấp.
*   **Tính chất phù du (Ephemeral):** Dữ liệu sẽ **bị mất sạch** nếu máy chủ bị **Stop** (Dừng) hoặc **Terminate** (Hủy). Chỉ hành vi *Reboot* thông thường mới không làm mất dữ liệu.

---

### C. Bảng so sánh tổng quan (Comparison Table)

| Đặc tính | EBS (Elastic Block Store) | EFS (Elastic File System) | Instance Store |
| :--- | :--- | :--- | :--- |
| **Loại lưu trữ** | Block Storage (Lưu trữ khối mạng) | File Storage (Lưu trữ tệp tin mạng) | Block Storage (Lưu trữ khối vật lý cục bộ) |
| **Hình thức kết nối** | Kết nối qua mạng nội bộ AWS | Kết nối qua mạng NFS chia sẻ | Gắn trực tiếp vào phần cứng máy chủ vật lý |
| **Số lượng Instance kết nối** | Thường là 1-to-1 (Trừ EBS Multi-Attach) | Nhiều instances kết nối đồng thời (1-to-Many) | Chỉ gắn duy nhất với 1 instance chạy trên host đó |
| **Phạm vi hoạt động (Scope)** | Bị khóa trong **1 Availability Zone** | Hỗ trợ nhiều AZs (**Regional**) hoặc duy nhất **1 AZ** (**One Zone**) | Khóa cứng trong 1 máy chủ vật lý của AZ đó |
| **Khả năng tự co giãn** | Không (phải chỉnh dung lượng thủ công) | Có (tự động tăng/giảm dung lượng) | Không (dung lượng cố định đi kèm loại máy chủ) |
| **Tốc độ & Độ trễ** | Rất nhanh, độ trễ thấp | Độ trễ cao hơn EBS (kết nối qua mạng NFS) | Cực kỳ nhanh, IOPS siêu khủng, độ trễ cực thấp |
| **Độ bền dữ liệu** | Rất bền, hỗ trợ snapshot, giữ nguyên khi Stop/Terminate | Rất bền, dữ liệu phân tán nhiều AZ | **Không bền (Ephemeral)**, mất dữ liệu hoàn toàn khi Stop/Terminate |
| **Chi phí** | Trả theo dung lượng đã đặt trước | Trả theo dung lượng thực tế sử dụng | Đã bao gồm trong giá thuê instance |
| **Trường hợp sử dụng** | Cơ sở dữ liệu chính, phân vùng OS, lưu trữ lâu dài | Web server farm chia sẻ file, thư mục dùng chung | Cache, Buffer, Temporary data cần tốc độ đọc ghi siêu cao |

---

## 14. Hướng dẫn dọn dẹp tài nguyên thực hành tránh phát sinh chi phí (Cleanup Resources Guide)
**Câu hỏi:** Sau khi hoàn thành các bài thực hành về EBS, EFS và EC2 Instance Store, ta cần dọn dẹp những tài nguyên nào trên AWS Console để đảm bảo không bị tính phí (hoặc vượt hạn mức Free Tier)?

**Trả lời:**
Để tránh các hóa đơn ngoài ý muốn từ AWS, bạn bắt buộc phải thực hiện dọn dẹp toàn bộ tài nguyên theo trình tự khuyến nghị sau:

### A. Hủy các máy chủ ảo EC2 (Terminate EC2 Instances)
1. Truy cập trang quản trị **EC2 Console** -> Chọn **Instances**.
2. Chọn tất cả các máy chủ thực hành đã tạo (ví dụ: `Instance A`, `Instance B`, máy chủ khởi tạo từ AMI, máy chủ gốc...).
3. Chọn **Instance state** -> Nhấp **Terminate instance** -> Xác nhận **Terminate**.
4. *Lưu ý:* Quá trình tắt máy sẽ tự động xóa các ổ đĩa root có thuộc tính *Delete on Termination* hoạt động.

### B. Xóa ổ đĩa mạng EBS phụ (Delete EBS Volumes)
1. Chờ cho các máy chủ EC2 chuyển sang trạng thái tắt hoàn toàn (`Terminated`).
2. Vào mục **Elastic Block Store** -> **Volumes**.
3. Tìm và chọn các ổ đĩa phụ còn sót lại đang ở trạng thái `available` (như ổ đĩa thực hành `2 GiB`, `gp3`...).
4. Chọn **Actions** -> Chọn **Delete volume** -> Xác nhận **Delete**.

### C. Xóa các bản sao lưu EBS Snapshots (Delete EBS Snapshots)
1. Vào mục **Elastic Block Store** -> **Snapshots**.
2. Chọn tất cả các snapshot đã tạo trong quá trình thực hành.
3. Chọn **Actions** -> Chọn **Delete snapshot** -> Xác nhận xóa.
4. *Mẹo:* Đảm bảo không còn snapshot nào để tránh bị AWS tính phí lưu trữ snapshot hàng tháng.

### D. Xóa hệ thống tệp tin EFS (Delete EFS File System)
1. Truy cập dịch vụ **EFS** (Elastic File System).
2. Tích chọn file system đã tạo -> Nhấp chọn **Actions** -> Chọn **Delete**.
3. **Xác nhận xóa:** AWS sẽ yêu cầu bạn nhập chính xác **File system ID** (ví dụ: `fs-0123456789abcdef`). Hãy sao chép ID hiển thị trên màn hình, dán vào ô xác nhận và nhấn **Delete**.

### E. Xóa các nhóm bảo mật phụ (Delete Security Groups)
1. Vào mục **Network & Security** -> Chọn **Security Groups**.
2. Tìm và chọn các security groups thực hành đã tạo (ví dụ: `sg-efs-demo`, các nhóm tự động tạo `efs-sg-1`, `efs-sg-2`...).
3. Chọn **Actions** -> Chọn **Delete security groups** -> Xác nhận **Delete**.
4. *Lưu ý về thứ tự:*
   - Bạn chỉ có thể xóa các security groups này **sau khi các EC2 Instances sử dụng chúng đã tắt hẳn** (Shutdown/Terminated hoàn toàn). Nếu thử xóa sớm hơn, AWS sẽ báo lỗi ràng buộc.
   - **Tuyệt đối KHÔNG xóa nhóm bảo mật mặc định (Default Security Group)** của VPC.

