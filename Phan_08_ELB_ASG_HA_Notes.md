# Ôn tập: Phần 8 - High Availability & Scalability (ELB & ASG)

Tài liệu này lưu lại các kiến thức cốt lõi, câu hỏi ôn tập và các bước thực hành quan trọng trong Phần 8 về **Elastic Load Balancing (ELB)** và **Auto Scaling Groups (ASG)** phục vụ cho kỳ thi **AWS Certified Solutions Architect Associate (SAA-C03)**.

---

## Bài 70: Mở rộng quy mô và Tính sẵn sàng cao (Scalability & High Availability Overview)
**Câu hỏi:** Scalability (Khả năng mở rộng quy mô) và High Availability (Tính sẵn sàng cao) là gì? Phân biệt sự khác nhau giữa Mở rộng theo chiều dọc (Vertical Scalability) và Mở rộng theo chiều ngang (Horizontal Scalability / Elasticity)?

**Trả lời:**

### A. Slide bài giảng (Slides 119 - 123 / PDF Page 119 - 123)
````carousel
![Slide 119: Scalability & High Availability Overview](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/media__1785947495328.png)
<!-- slide -->
![Slide 120: Vertical Scalability (Scale Up / Down)](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/media__1785947884991.png)
<!-- slide -->
![Slide 121: Horizontal Scalability (Scale Out / In - Elasticity)](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/media__1785947894019.png)
<!-- slide -->
![Slide 122: High Availability (Multi-AZ / Multi Data Center)](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/media__1785947904499.png)
<!-- slide -->
![Slide 123: High Availability & Scalability For EC2 Overview](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/media__1785948525781.png)
````

### B. Khả năng mở rộng quy mô (Scalability)
*   **Định nghĩa:** Scalability nghĩa là một ứng dụng hoặc hệ thống có khả năng tự thích ứng để **xử lý khối lượng công việc (load) lớn hơn**.
*   **Phân loại Mở rộng quy mô:**
    1.  **Vertical Scalability (Mở rộng theo chiều dọc - Scale Up / Scale Down):**
        *   **Cơ chế:** Tăng hoặc giảm kích thước (cấu hình phần cứng) của một instance cụ thể.
        *   **Ví dụ với EC2:** Nâng cấp cấu hình máy chủ từ `t2.micro` (1 vCPU, 1 GB RAM) lên `t2.large` (2 vCPU, 8 GB RAM), hoặc lên máy chủ siêu khủng `u-12tb1.metal` (450 vCPU, 12.3 TB RAM).
        *   **Ví dụ với các dịch vụ khác:** Rất phổ biến cho các hệ thống không phân tán (Non-distributed systems) như cơ sở dữ liệu **RDS** hoặc bộ nhớ đệm **ElastiCache** (thay đổi instance class).
        *   **Giới hạn:** Bị giới hạn bởi trần công nghệ phần cứng vật lý (Hardware limits).
    2.  **Horizontal Scalability (Mở rộng theo chiều ngang - Scale Out / Scale In / Elasticity):**
        *   **Cơ chế:** Tăng hoặc giảm **số lượng instances/máy chủ** cùng tham gia xử lý cho ứng dụng.
        *   **Khái niệm Elasticity (Tính linh hoạt):** Khả năng tự động thêm máy chủ khi tải tăng (Scale Out) và giảm bớt máy chủ khi tải giảm (Scale In) trong môi trường điện toán đám mây.
        *   **Ví dụ:** Thêm 5 máy chủ `t2.micro` chạy song song đứng sau một bộ cân bằng tải (Load Balancer).
        *   **Yêu cầu:** Ứng dụng phải được thiết kế dạng **hệ thống phân tán (Distributed System)**.

### C. Tính sẵn sàng cao (High Availability - HA)
*   **Định nghĩa:** High Availability nghĩa là ứng dụng/hệ thống chạy đồng thời trên **ít nhất 2 trung tâm dữ liệu (Data Centers)** hoặc **2 Availability Zones (AZs)** trong AWS.
*   **Mục tiêu:** Giúp hệ thống tiếp tục hoạt động bình thường, sống sót qua các thảm họa khi một trung tâm dữ liệu hoặc một AZ bị sập hoàn toàn (Survive a Data Center loss).
*   **Hình thức triển khai HA:**
    1.  **Passive High Availability (Thụ động):** Ví dụ như **RDS Multi-AZ** (có một bản Primary active xử lý đọc/ghi và một bản Standby passive ở AZ khác sẵn sàng tiếp quản khi có sự cố - Failover).
    2.  **Active High Availability (Chủ động):** Kết hợp với **Horizontal Scaling** (Ví dụ: Các EC2 instances nằm ở 2 AZs khác nhau đồng thời xử lý các yêu cầu từ người dùng).

### D. Ẩn dụ thực tế: Tổng đài cuộc gọi (Call Center Analogy)
*   **Vertical Scaling:** Nâng cấp một điện thoại viên tập sự (Junior operator - nhận 5 cuộc gọi/phút) thành một điện thoại viên cao cấp (Senior operator - nhận 10 cuộc gọi/phút).
*   **Horizontal Scaling:** Tuyển thêm 5 điện thoại viên mới cùng trực tổng đài để nhân đôi/nhân ba năng lực xử lý.
*   **High Availability:** Chia 6 điện thoại viên làm 2 nhóm ngồi ở 2 tòa nhà khác nhau (New York và San Francisco). Nếu tòa nhà ở New York bị mất mạng/mất điện, các điện thoại viên tại San Francisco vẫn tiếp tục nghe máy bình thường.

---

## Bài 71: Tổng quan về Bộ cân bằng tải (Elastic Load Balancing - ELB Overview)
**Câu hỏi:** Load Balancer là gì? Tại sao nên sử dụng AWS Elastic Load Balancer (ELB)? Cơ chế Health Checks hoạt động ra sao? Phân biệt 4 loại Load Balancer trên AWS và cách thiết lập Security Group cho ELB & EC2 instances?

**Trả lời:**

### A. Slide bài giảng (Slides 124 - 129 / PDF Page 124 - 129)
````carousel
![Slide 124: What is load balancing?](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/media__1786166193074.png)
<!-- slide -->
![Slide 125: Why use a load balancer?](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/media__1786166212965.png)
<!-- slide -->
![Slide 126: Why use an Elastic Load Balancer?](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/media__1786166779652.png)
<!-- slide -->
![Slide 127: Health Checks](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/media__1786166836429.png)
<!-- slide -->
![Slide 128: Types of load balancer on AWS](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/media__1786166897633.png)
<!-- slide -->
![Slide 129: Load Balancer Security Groups](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/media__1786166951723.png)
````

### B. Khái niệm bộ cân bằng tải (What is a Load Balancer?)
*   **Định nghĩa:** Load Balancer là một máy chủ (hoặc một cụm máy chủ) đóng vai trò làm cổng tiếp nhận lưu lượng truy cập (traffic) và **chuyển tiếp/phân phối lưu lượng đó đến nhiều máy chủ phía sau (backend / downstream EC2 instances)**.
*   **Cơ chế hoạt động:** 
    *   Người dùng (Users) chỉ kết nối trực tiếp đến một **địa chỉ duy nhất (single endpoint - DNS name)** do Load Balancer cung cấp.
    *   Load Balancer sẽ luân chuyển các yêu cầu (requests) của người dùng đến từng EC2 instance ở phía sau (Instance 1, Instance 2, Instance 3...).
    *   Người dùng hoàn toàn không biết và không cần quan tâm ứng dụng phía sau đang chạy trên máy chủ cụ thể nào.

### C. Lý do sử dụng Elastic Load Balancer (Why use an ELB?)
1.  **Phân phối tải (Spread Load):** Chia đều tải truy cập qua nhiều máy chủ phía sau để tránh quá tải cho 1 máy chủ đơn lẻ.
2.  **Cung cấp điểm truy cập duy nhất (Single Point of Access):** Ứng dụng chỉ cần công khai 1 tên miền (DNS Name), ẩn hoàn toàn địa chỉ IP của các máy chủ backend.
3.  **Xử lý lỗi mượt mà (Seamlessly handle failures):** Tự động phát hiện máy chủ bị lỗi và ngừng gửi lưu lượng truy cập đến máy chủ đó thông qua cơ chế kiểm tra sức khỏe (**Health Checks**).
4.  **Giải mã SSL/TLS (SSL Termination):** Cho phép giải mã các kết nối mã hóa HTTPS trực tiếp tại Load Balancer, giúp giảm tải xử lý tính toán cho các máy chủ backend.
5.  **Duy trì phiên làm việc (Sticky Sessions):** Giữ kết nối của một người dùng cố định với cùng một máy chủ backend thông qua Cookie.
6.  **Tính sẵn sàng cao (High Availability across AZs):** Tự động phân phối tải qua nhiều Availability Zones.
7.  **Tách biệt lưu lượng (Separate Traffic):** Phân chia rõ ràng lưu lượng truy cập công cộng (Public traffic) từ Internet và lưu lượng nội bộ (Private traffic) trong VPC.

### D. Tại sao nên chọn Managed ELB của AWS thay vì tự dựng Load Balancer?
*   **Dịch vụ do AWS quản lý hoàn toàn (Managed Service):** AWS đảm bảo tính sẵn sàng cao, nâng cấp, bảo trì và tự động mở rộng quy mô cho ELB.
*   **Chi phí tối ưu & Tiết kiệm công sức:** Rẻ hơn và dễ dàng hơn rất nhiều so với việc tự dựng và bảo trì một cụm máy chủ cân bằng tải riêng (như NGINX/HAProxy).
*   **Tích hợp sâu rộng với hệ sinh thái AWS:** Tích hợp sẵn với EC2, Auto Scaling Groups (ASG), Amazon ECS, AWS Certificate Manager (ACM), CloudWatch, Route 53, AWS WAF, AWS Global Accelerator...

### E. Cơ chế kiểm tra sức khỏe (Health Checks)
*   **Mục đích:** Giúp ELB xác định một EC2 instance backend có đang hoạt động bình thường hay không. Nếu instance bị lỗi (unhealthy), ELB sẽ **ngừng gửi lưu lượng** đến instance đó.
*   **Cơ chế hoạt động:**
    *   ELB sẽ định kỳ gửi một yêu cầu ping/request tới một cổng (**Port**) và tuyến đường (**Route / Endpoint**) cụ thể trên instance (ví dụ: `HTTP` qua port `4567` tới endpoint `/health`).
    *   Nếu instance phản hồi về với mã trạng thái thành công (**HTTP 200 OK**), instance đó được đánh giá là **Healthy**.
    *   Nếu không phản hồi hoặc trả về mã lỗi (như HTTP 4xx, 5xx), instance sẽ bị đánh giá là **Unhealthy**.

### F. Phân loại 4 dòng Load Balancer trên AWS (4 Types of ELB)
1.  **Classic Load Balancer (CLB - Thế hệ cũ / V1 - 2009):**
    *   Hỗ trợ HTTP, HTTPS, TCP, SSL.
    *   **Trạng thái:** Đã cũ (Deprecated), hiển thị cảnh báo trên Console và không khuyến khích dùng cho hệ thống mới.
2.  **Application Load Balancer (ALB - Thế hệ mới - Layer 7 - 2016):**
    *   Hoạt động ở tầng ứng dụng (Layer 7).
    *   Hỗ trợ giao thức **HTTP, HTTPS, WebSocket**.
    *   Hỗ trợ định tuyến thông minh dựa trên URL Path, Hostname, Query Parameters...
3.  **Network Load Balancer (NLB - Thế hệ mới - Layer 4 - 2017):**
    *   Hoạt động ở tầng mạng (Layer 4).
    *   Hỗ trợ giao thức **TCP, UDP, TLS**.
    *   Hiệu năng siêu khủng (xử lý hàng triệu request/giây) với độ trễ siêu thấp (sub-millisecond).
4.  **Gateway Load Balancer (GWLB - Layer 3 - 2020):**
    *   Hoạt động ở tầng mạng (Layer 3 / IP Protocol).
    *   Dùng để kiểm tra và phân phối lưu lượng truy cập qua các thiết bị mạng/tường lửa ảo bên thứ 3 (Virtual Appliances / Firewalls).

> 💡 **Khuyên dùng:** AWS luôn khuyến khích sử dụng các dòng Load Balancer thế hệ mới (**ALB, NLB, GWLB**) vì chúng cung cấp nhiều tính năng vượt trội và hiệu năng cao hơn.

### G. Cấu hình Nhóm bảo mật cho Load Balancer & EC2 (Security Groups Architecture)
Để đảm bảo tính bảo mật tối đa cho hệ thống:
1.  **Security Group của Load Balancer (Public ELB Security Group):**
    *   **Inbound Rules:** Mở port `80` (HTTP) và port `443` (HTTPS) cho nguồn `0.0.0.0/0` (Anywhere - cho phép tất cả người dùng Internet truy cập).
2.  **Security Group của EC2 Instance (Backend EC2 Security Group):**
    *   **Inbound Rules:** Mở port `80` (HTTP), nhưng phần **Source KHÔNG sử dụng dải IP (`0.0.0.0/0`)** mà trỏ trực tiếp đến **Security Group ID của Load Balancer** (`sg-elb-xxx`).
    *   **Tác dụng:** Đảm bảo các EC2 instances **chỉ chấp nhận lưu lượng truy cập đến từ Load Balancer**, ngăn chặn hoàn toàn việc người dùng truy cập trực tiếp vào IP công cộng của EC2 instance.

---

## Bài 72: Application Load Balancer (ALB)
**Câu hỏi:** Application Load Balancer (ALB) là gì? Các tính năng định tuyến nổi bật của ALB? Target Groups là gì? Làm thế nào để ứng dụng backend xác định được IP thật của người dùng khi dùng ALB?

**Trả lời:**

### A. Slide bài giảng (Slides 131 - 136 / PDF Page 131 - 136)
````carousel
![Slide 131: Application Load Balancer (v2)](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1786549353946.png)
<!-- slide -->
![Slide 132: Application Load Balancer (v2) Routing](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1786549360800.png)
<!-- slide -->
![Slide 133: Application Load Balancer (v2) HTTP Based Traffic](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1786549367896.png)
<!-- slide -->
![Slide 134: Application Load Balancer (v2) Target Groups](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1786549374482.png)
<!-- slide -->
![Slide 135: Application Load Balancer (v2) Query Strings/Parameters Routing](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1786549388244.png)
<!-- slide -->
![Slide 136: Application Load Balancer (v2) Good to Know (X-Forwarded-For)](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1786549395224.png)
````

### B. Khái niệm ALB (What is an ALB?)
*   ALB là bộ cân bằng tải **Layer 7** (tầng ứng dụng), chuyên xử lý giao thức **HTTP, HTTPS và WebSockets**.
*   Hỗ trợ chuyển tiếp tự động (Redirect) từ HTTP sang HTTPS ở ngay cấp độ Load Balancer.
*   ALB cực kỳ lý tưởng cho các kiến trúc **Microservices** và ứng dụng chạy trên **Container** (như Docker/Amazon ECS) vì nó hỗ trợ tính năng **Port Mapping** (định tuyến đến các cổng động trên cùng một EC2 instance).
*   Thay vì phải tạo nhiều Classic Load Balancers cho mỗi ứng dụng, bạn chỉ cần **1 ALB duy nhất** để định tuyến cho hàng loạt ứng dụng khác nhau.

### C. Định tuyến nâng cao (Advanced Routing)
ALB cung cấp khả năng định tuyến thông minh (Routing rules) lưu lượng truy cập đến các **Target Groups** khác nhau dựa trên:
1.  **Đường dẫn URL (Path-based routing):** VD: `example.com/users` trỏ tới Target Group chuyên xử lý User, `example.com/posts` trỏ tới Target Group chuyên xử lý Bài viết.
2.  **Tên miền (Hostname-based routing):** VD: `api.example.com` và `web.example.com` trỏ tới 2 cụm máy chủ khác nhau.
3.  **Tham số URL & Headers (Query strings / Headers routing):** VD: `example.com/search?platform=mobile` chuyển tới cụm xử lý giao diện mobile, `?platform=desktop` chuyển tới cụm desktop.

### D. Nhóm mục tiêu (Target Groups)
Target Groups là nơi tập hợp các tài nguyên đích để ALB chuyển tiếp lưu lượng tới. ALB hỗ trợ các loại Target Groups sau:
1.  **EC2 Instances:** Máy chủ ảo EC2 (có thể được quản lý bởi Auto Scaling Group).
2.  **ECS Tasks:** Các container (ứng dụng Docker) chạy trên Amazon ECS.
3.  **Lambda Functions:** Hàm chạy code không máy chủ (Serverless architecture).
4.  **IP Addresses:** Phải là **địa chỉ IP nội bộ (Private IPs)**. Có thể dùng để định tuyến đến các máy chủ On-premises nằm trong trung tâm dữ liệu riêng của bạn.

> 💡 **Lưu ý:** Quá trình kiểm tra sức khỏe (**Health Checks**) được cấu hình và thực thi ở cấp độ **Target Group**, không phải ở cấp độ ALB.

### E. Cơ chế X-Forwarded-For (Xác định IP thật của Client)
*   ALB giao tiếp với client thông qua một tên miền cố định (Fixed Hostname).
*   Khi người dùng kết nối tới ALB, quá trình kết nối bị ngắt tại ALB (**Connection Termination**). ALB sau đó sẽ dùng IP nội bộ (Private IP) của chính nó để giao tiếp với các máy chủ backend (EC2).
*   Hệ quả: Máy chủ EC2 **KHÔNG thể thấy trực tiếp** địa chỉ IP gốc của người dùng.
*   **Giải pháp:** Để EC2 biết được IP, Port, và Giao thức thật của người dùng, ALB sẽ tự động chèn thêm thông tin vào Header của HTTP Request. Máy chủ EC2 chỉ cần đọc các header này:
    *   `X-Forwarded-For`: Chứa địa chỉ IP thật của người dùng.
    *   `X-Forwarded-Port`: Cổng kết nối thật.
    *   `X-Forwarded-Proto`: Giao thức kết nối thật (HTTP/HTTPS).

---

## Bài 75: Network Load Balancer (NLB)
**Câu hỏi:** Network Load Balancer (NLB) là gì và hoạt động ở tầng nào? Điểm khác biệt lớn nhất về hiệu năng và địa chỉ IP của NLB so với ALB là gì? NLB hỗ trợ các loại Target Groups và Health Checks nào?

**Trả lời:**

### A. Slide bài giảng (Slides / PDF)
````carousel
![Slide: Network Load Balancer (v2)](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1786549631767.png)
<!-- slide -->
![Slide: Network Load Balancer (v2) TCP (Layer 4) Based Traffic](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1786549641103.png)
<!-- slide -->
![Slide: Network Load Balancer - Target Groups](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1786549649617.png)
````

### B. Khái niệm và Đặc điểm nổi bật của NLB
*   **Layer 4 (Tầng giao vận):** NLB hoạt động ở tầng 4, cho phép chuyển tiếp lưu lượng **TCP và UDP** đến các máy chủ. *(Mẹo thi: Hễ thấy nhắc đến giao thức TCP/UDP hoặc yêu cầu hiệu năng cực lớn thì hãy nghĩ ngay đến NLB).*
*   **Hiệu suất cực khủng (Extreme Performance):** Có khả năng xử lý **hàng triệu yêu cầu mỗi giây** (millions of requests per second) với **độ trễ siêu thấp** (ultra-low latency).
*   **Địa chỉ IP Tĩnh (Static IP):** 
    *   Trái ngược với ALB/CLB (có IP thay đổi liên tục), NLB cung cấp **đúng 1 địa chỉ IP tĩnh** cho mỗi Availability Zone (AZ) mà nó hoạt động. 
    *   Bạn hoàn toàn có thể gán **Elastic IP** cho mỗi AZ này. 
    *   Rất hữu ích đối với các bài toán yêu cầu tường lửa hoặc đối tác (third-party) phải đưa dải IP của bạn vào danh sách trắng (Whitelist specific IPs).

### C. Nhóm mục tiêu (Target Groups)
NLB phân phối lưu lượng đến các Target Groups sau:
1.  **EC2 Instances:** Chuyển tiếp TCP/UDP đến máy chủ EC2.
2.  **IP Addresses (Địa chỉ IP riêng - Private IPs):** Định tuyến thẳng tới các máy chủ bằng IP nội bộ. Thích hợp để tích hợp với các máy chủ On-premises nằm trong trung tâm dữ liệu riêng của bạn.
3.  **Application Load Balancer (ALB):** Bạn có thể **đặt một NLB ở phía trước một ALB**.
    *   *Mục đích:* Tận dụng **địa chỉ IP tĩnh** của NLB ở lớp ngoài cùng, kết hợp với sức mạnh **định tuyến HTTP/HTTPS thông minh** của ALB ở lớp bên trong.

> 💡 **Lưu ý về Kiểm tra sức khỏe (Health Checks):** Target Group của NLB không chỉ kiểm tra sức khỏe bằng TCP, mà còn **hỗ trợ cả giao thức HTTP và HTTPS**. Nghĩa là dù NLB chạy ở Layer 4, nó vẫn có thể kiểm tra xem website (Layer 7) ở backend có đang trả về mã 200 OK hay không!

---

## Bài 76: Thực hành Network Load Balancer (NLB) - Hands On
**Câu hỏi:** Các bước thiết lập một Network Load Balancer (NLB) thực tế trên AWS diễn ra như thế nào? Cần lưu ý gì về Security Group của EC2 khi chuyển từ ALB sang NLB?

**Trả lời:**

### A. Các bước khởi tạo Network Load Balancer
1.  **Khởi tạo NLB:**
    *   Tạo một Load Balancer mới và chọn **Network Load Balancer** (ví dụ đặt tên: `DemoNLB`).
    *   **Scheme:** Chọn **Internet-facing** (Hỗ trợ truy cập từ Internet).
    *   **Network Mapping:** Chọn VPC đang sử dụng và **tích chọn tất cả các Availability Zones (AZs)** hiện có.
    *   *Đặc điểm quan trọng:* Với NLB, mỗi AZ được chọn sẽ được AWS gán cho **một địa chỉ IPv4 tĩnh cố định (Fixed IPv4 address)**. Nếu muốn, bạn cũng có thể tự gán **Elastic IP** cho từng AZ này thay vì dùng IP mặc định của AWS.

2.  **Cấu hình Security Group cho NLB (Tính năng mới):**
    *   Tương tự như ALB, hiện tại AWS khuyến nghị gán Security Group trực tiếp cho NLB.
    *   Tạo một SG mới (ví dụ: `demo-sg-nlb`).
    *   **Inbound rules:** Cho phép **HTTP (Port 80)** từ mọi nơi (`0.0.0.0/0`) để có thể truy cập qua web browser.
    *   Gỡ bỏ SG mặc định và chọn `demo-sg-nlb` cho NLB của bạn.

3.  **Cấu hình Target Group cho NLB:**
    *   **Listeners & Routing:** Giao thức **TCP** trên Port **80**.
    *   Tạo Target Group mới (ví dụ: `demo-tg-nlb`).
    *   **Target type:** Chọn **Instances**.
    *   **Protocol:** Chọn **TCP** (vì NLB hoạt hoạt động ở Layer 4).
    *   **Health checks (Kiểm tra sức khỏe):** Dù đang dùng TCP, vì ứng dụng backend là web server nên bạn hoàn toàn có thể chọn giao thức **HTTP** cho phần Health Check. Cấu hình kiểm tra: Healthy threshold là 2, timeout 2s, interval 5s.
    *   **Đăng ký Targets:** Chọn 2 EC2 instances đang chạy và nhấn *Include as pending below*, sau đó tạo Target Group.
    *   Quay lại trang tạo NLB, làm mới danh sách và chọn `demo-tg-nlb` vừa tạo. Tiến hành tạo NLB.

### B. Xử lý lỗi Security Group (Troubleshooting Security Groups)
*   **Vấn đề:** Sau khi NLB chuyển sang trạng thái *Active*, nếu bạn truy cập vào đường dẫn URL của NLB thì trang web sẽ không tải được. Kiểm tra trong Target Group sẽ thấy 2 instances đều ở trạng thái **Unhealthy**.
*   **Nguyên nhân:** 
    *   Kiểm tra lại **Security Group của EC2 instances** (backend).
    *   Hiện tại, Inbound rules của EC2 chỉ đang cho phép HTTP từ Security Group của *ALB cũ*, nhưng **chưa cho phép HTTP từ NLB mới**.
*   **Cách khắc phục:**
    *   Vào Security Group của EC2 instances.
    *   Thêm một rule Inbound mới: Cho phép **HTTP** và phần **Source** chọn đúng **Security Group của NLB** (`demo-sg-nlb`).
    *   Rule này có nghĩa là: "Cho phép EC2 nhận lưu lượng truy cập từ Network Load Balancer".
*   **Kết quả:**
    *   Đợi vài giây để NLB thực hiện Health Check lại.
    *   Các instances sẽ chuyển sang trạng thái **Healthy**.
    *   Truy cập lại URL của NLB, trang web sẽ hiển thị thành công (Hello World) và IP phản hồi sẽ liên tục thay đổi sau vài lần tải lại trang, chứng tỏ việc cân bằng tải đã hoạt động.

### C. Dọn dẹp tài nguyên (Clean up)
Để tránh phát sinh chi phí sau khi thực hành:
*   Xóa Network Load Balancer (`DemoNLB`).
*   Xóa Target Group (`demo-tg-nlb`).
*   (Tùy chọn) Xóa các Security Group đã tạo nếu không còn dùng đến.

---

## Bài Ôn tập thêm: So sánh cốt lõi giữa ALB và NLB
**Câu hỏi:** Network Load Balancer (NLB) và Application Load Balancer (ALB) khác nhau như thế nào? Khi nào nên sử dụng loại nào?

**Trả lời:**

Đây là trọng tâm cực kỳ quan trọng trong các bài thi chứng chỉ AWS. ALB và NLB phục vụ cho hai mục đích hoàn toàn khác nhau.

| Tiêu chí | Application Load Balancer (ALB) | Network Load Balancer (NLB) |
| :--- | :--- | :--- |
| **Tầng hoạt động (OSI)** | Layer 7 (Tầng ứng dụng) | Layer 4 (Tầng giao vận) |
| **Giao thức hỗ trợ** | HTTP, HTTPS, WebSockets | TCP, UDP, TLS |
| **Sự thông minh (Routing)** | Định tuyến thông minh. Có thể điều hướng dựa vào đường dẫn URL (Path), Tên miền (Hostname), Headers, Query Parameters. | Chuyển tiếp luồng dữ liệu mạng thô dựa trên Địa chỉ IP và Cổng (Port). Không đọc nội dung gói tin. |
| **Địa chỉ IP** | **IP Động (Dynamic IP).** Bắt buộc phải truy cập thông qua tên miền (DNS Name) vì IP thay đổi liên tục. | **IP Tĩnh (Static IP).** Cấp 1 IP tĩnh cố định cho mỗi AZ, hỗ trợ Elastic IP. Cực kỳ lý tưởng cho việc Whitelist IP ở Firewall đối tác. |
| **Hiệu năng & Tốc độ** | Rất nhanh, nhưng có độ trễ nhỏ do phải đọc Header HTTP để định tuyến. | **Tốc độ cực khủng (Extreme performance).** Xử lý hàng triệu request/giây với độ trễ siêu thấp (sub-millisecond). |
| **Trường hợp sử dụng** | Website, Web API, Microservices cần định tuyến phức tạp. | Game online nhiều người chơi, hệ thống tài chính/chứng khoán cần tốc độ cao, hoặc khi yêu cầu IP tĩnh bắt buộc. |

---

## Bài 77: Gateway Load Balancer (GWLB)
**Câu hỏi:** Gateway Load Balancer (GWLB) là gì? Hoạt động ở tầng nào trong mô hình OSI? Mục đích chính và giao thức đặc trưng (GENEVE) của GWLB là gì?

**Trả lời:**

### A. Slide bài giảng
````carousel
![Slide: Gateway Load Balancer](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1787000572704.png)
<!-- slide -->
![Slide: Gateway Load Balancer - Target Groups](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1787000578854.png)
````

### B. Khái niệm và Mục đích sử dụng
*   **Gateway Load Balancer (GWLB)** là loại Load Balancer mới nhất của AWS.
*   **Mục đích chính:** Được sử dụng để triển khai, mở rộng quy mô và quản lý một hệ thống các **thiết bị mạng ảo của bên thứ 3 (Third-party virtual appliances)** trên AWS.
*   **Trường hợp sử dụng (Use Case):** Bạn muốn **tất cả lưu lượng truy cập mạng** phải đi qua một hệ thống kiểm duyệt trước khi tiến vào ứng dụng của bạn. Các thiết bị kiểm duyệt này thường là:
    *   Tường lửa bảo mật (Firewalls).
    *   Hệ thống phát hiện và ngăn chặn xâm nhập (Intrusion Detection and Prevention Systems - IDPS).
    *   Hệ thống kiểm tra gói tin sâu (Deep Packet Inspection).
    *   Sửa đổi/phân tích Payload ở cấp độ mạng.

### C. Cơ chế hoạt động của GWLB
1.  **Cập nhật Route Tables:** Quá trình bắt đầu bằng việc thay đổi Bảng định tuyến (Route Tables) trong VPC để ép tất cả lưu lượng từ người dùng đi vào GWLB thay vì đi thẳng đến ứng dụng (ALB/EC2).
2.  **Kiểm tra và Xử lý (Traffic Inspection):** 
    *   GWLB nhận lưu lượng và phân phối nó cho một Target Group chứa các thiết bị mạng ảo (Ví dụ: Các máy chủ Firewall).
    *   Các thiết bị này sẽ "mổ xẻ" gói tin để phân tích.
    *   Nếu gói tin chứa mã độc hoặc vi phạm luật: Thiết bị sẽ **loại bỏ (Drop)** gói tin ngay lập tức.
    *   Nếu gói tin hợp lệ (Accepted): Thiết bị sẽ gửi trả gói tin lại cho GWLB.
3.  **Chuyển tiếp (Forwarding):** Sau khi nhận lại gói tin an toàn, GWLB sẽ tiếp tục chuyển tiếp nó đến ứng dụng thực sự ở phía sau. Toàn bộ quá trình này diễn ra hoàn toàn **vô hình (transparent)** đối với ứng dụng đích.

### D. Đặc điểm kỹ thuật cốt lõi (Rất hay thi)
*   **Layer 3 (Tầng mạng):** GWLB hoạt động ở tầng 3 (Network Layer), xử lý trực tiếp các gói tin IP (IP Packets).
*   **Chức năng kép (Two functions in one):**
    1.  **Transparent Network Gateway:** Đóng vai trò là cửa ngõ ra/vào duy nhất (Single entry/exit) cho mọi luồng dữ liệu trong VPC.
    2.  **Load Balancer:** Đóng vai trò phân phối đều tải công việc cho cụm thiết bị mạng kiểm duyệt phía sau.
*   **🔑 Giao thức đặc trưng (Exam Tip):** Nếu trong câu hỏi thi có nhắc đến việc sử dụng **Giao thức GENEVE trên Port 6081 (GENEVE protocol on port 6081)** -> Đáp án chắc chắn 100% là **Gateway Load Balancer**.

### E. Target Groups cho GWLB
Target Group của GWLB là các thiết bị mạng ảo (Third-party appliances), chúng có thể được liên kết qua:
*   **EC2 Instances:** Đăng ký bằng Instance ID.
*   **IP Addresses:** Đăng ký bằng **Private IPs**. Tính năng này cho phép bạn định tuyến lưu lượng đến các thiết bị tường lửa vật lý nằm ở Trung tâm dữ liệu riêng của bạn (On-premises Data Center).

> 💡 **Lưu ý:** Việc thiết lập GWLB trên thực tế liên quan đến kiến thức Networking (Route Tables) rất phức tạp, vì vậy trong phạm vi SAA-C03 không có bài thực hành. Bạn chỉ cần hiểu rõ kiến trúc hoạt động High-level và nhớ từ khóa **Layer 3, Virtual Appliances, Firewalls, GENEVE** là đủ để vượt qua các câu hỏi trắc nghiệm.

---

## Bài 78: Elastic Load Balancer - Sticky Sessions (Session Affinity)
**Câu hỏi:** Sticky Sessions (Session Affinity) là gì? Ưu nhược điểm của nó và các loại Cookie được sử dụng để duy trì phiên làm việc?

**Trả lời:**

### A. Slide bài giảng
````carousel
![Slide: Sticky Sessions (Session Affinity)](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1787156849507.png)
<!-- slide -->
![Slide: Sticky Sessions - Cookie Names](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1787156934998.png)
````

### B. Ví dụ thực tế dễ hiểu
**1. Vấn đề của Load Balancer bình thường**
Hãy tưởng tượng bạn đang mua sắm trên một trang web thương mại điện tử. Trang web này có 2 máy chủ xử lý ở phía sau (Server A và Server B).
*   Bạn bấm chọn "Thêm cái áo vào giỏ hàng" 👉 Load Balancer gửi yêu cầu của bạn đến **Server A**. Lúc này Server A ghi nhớ trong bộ nhớ của nó là "Khách hàng này đang có 1 cái áo trong giỏ".
*   Tiếp theo, bạn bấm chọn "Thanh toán" 👉 Theo thói quen chia đều tải, Load Balancer lại gửi yêu cầu thanh toán của bạn sang **Server B**. 
*   **Hậu quả:** Server B không hề biết bạn là ai và giỏ hàng của bạn đang có gì (vì dữ liệu đó nằm bên Server A). Trang web báo lỗi giỏ hàng trống!

**2. Giải pháp: Sticky Sessions (Cơ chế kết dính)**
Để giải quyết tình trạng "mất não" ở trên, AWS cung cấp tính năng **Sticky Sessions**.
*   Khi bạn truy cập lần đầu và được giao cho **Server A**, Load Balancer sẽ lén nhét vào trình duyệt của bạn một "tấm vé" (gọi là **Cookie**).
*   Lần thứ 2 khi bạn bấm "Thanh toán", trình duyệt của bạn đưa tấm vé Cookie đó cho Load Balancer xem. 
*   Load Balancer nhìn vé và nói: *"À, anh khách này lúc nãy đang làm việc với Server A. Hãy đưa anh ấy về lại Server A!"*.
*   **Kết quả:** Nhờ sự "kết dính" này, toàn bộ quá trình mua hàng của bạn (Session) diễn ra thông suốt trên đúng một máy chủ, không bị mất dữ liệu giỏ hàng hay thông tin đăng nhập.

**3. Nhược điểm chí mạng (Rất hay hỏi trong đề thi)**
Tuy hay là vậy nhưng Sticky Sessions có một nhược điểm lớn đó là **Mất cân bằng tải (Load Imbalance)**.
*   **Ví dụ:** Load Balancer phân 100 người dùng cho Server A, và 100 người khác cho Server B. Nếu xui xẻo 100 người bên Server A là những game thủ "cày cuốc" liên tục 24/7 (Heavy users), còn 100 người bên Server B chỉ lướt web 5 phút rồi tắt máy.
*   Vì tính năng "kết dính" (Sticky), Load Balancer cứ phải ép 100 game thủ đó quay lại Server A. Hậu quả là **Server A bị quá tải chết ngộp**, trong khi **Server B lại ngồi chơi xơi nước**.

### C. Cơ chế hoạt động kỹ thuật (Dựa trên Cookie)
Stickiness hoạt động dựa trên việc gửi và nhận **Cookie** giữa Client (Trình duyệt) và Load Balancer. Có 2 loại Cookie chính:
1.  **Application-based Cookies (Cookie dựa trên ứng dụng):**
    *   **Custom Cookie:** Do chính ứng dụng backend (Target) của bạn tạo ra. Tên Cookie do bạn tự định nghĩa, nhưng **không được phép** sử dụng các tên dành riêng của AWS (AWSALB, AWSALBAPP, AWSALBTG).
    *   **Application Cookie (do ALB tạo):** Được tạo ra tự động bởi Load Balancer nhưng thời hạn gắn liền với phiên của ứng dụng. Tên Cookie mặc định là AWSALBAPP.
2.  **Duration-based Cookies (Cookie dựa trên thời gian):**
    *   Được tạo ra bởi chính Load Balancer, có thời gian hết hạn (Expiration duration) cụ thể do bạn thiết lập (từ 1 giây đến 7 ngày).
    *   Tên Cookie: AWSALB (đối với ALB) và AWSELB (đối với CLB).
    *   Khi Cookie hết hạn, người dùng có thể được chuyển hướng sang một EC2 instance khác.

### D. Các bước cấu hình (Hands-On)
Tính năng Sticky Sessions không được cấu hình ở Load Balancer mà **được cấu hình ở cấp độ Target Group**.
1.  Truy cập vào **Target Groups**.
2.  Chọn Target Group muốn cấu hình, chuyển sang tab **Attributes** -> Nhấn **Edit**.
3.  Tìm phần **Target selection configuration** -> Kích hoạt (Turn on) **Stickiness**.
4.  Chọn loại Stickiness:
    *   **Load balancer generated cookie:** Điền thời gian duy trì (Duration) - ví dụ 1 ngày (1 day).
    *   **Application-based cookie:** Nhập tên Cookie tùy chỉnh của ứng dụng (App cookie name).
5.  Nhấn **Save changes**.

> 💡 **Mẹo kiểm tra:** Sau khi bật, nếu bạn F5 tải lại trang web nhiều lần, địa chỉ IP hoặc định danh của máy chủ backend phản hồi sẽ **không thay đổi**. Bạn có thể mở Web Developer Tools trên trình duyệt (tab Network -> Cookies) để thấy rõ Response Cookie (AWSALB) được gửi về từ máy chủ.
---

## Bài 80: Cân bằng tải liên vùng (Cross-Zone Load Balancing)
**Câu hỏi:** Cross-Zone Load Balancing là gì? Tại sao nó giúp giải quyết bài toán mất cân bằng tải giữa các AZ? Cấu hình mặc định và chi phí (Pricing) của tính năng này trên ALB, NLB và GWLB như thế nào?

**Trả lời:**

### A. Slide bài giảng
````carousel
![Slide: Cross-Zone Load Balancing - Architecture](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1787159291849.png)
<!-- slide -->
![Slide: Cross-Zone Load Balancing - Types & Pricing](C:/Users/Khoa/.gemini/antigravity/brain/747dd10f-8688-42d8-8514-84be2c008ef4/.user_uploaded/media_1787159298612.png)
````

### B. Ví dụ thực tế: Chuỗi nhà hàng
Hãy tưởng tượng bạn làm chủ một chuỗi nhà hàng có 2 chi nhánh (tương đương với 2 Vùng - **AZ1** và **AZ2**).
*   Tổng đài (DNS) luôn chia đều khách hàng: **50%** khách được chỉ đường tới Chi nhánh 1, và **50%** khách tới Chi nhánh 2.
*   Nhưng nhân sự lại đang bị lệch: Chi nhánh 1 chỉ có **2 đầu bếp** (2 EC2 Instances), trong khi Chi nhánh 2 có tới **8 đầu bếp**. (Tổng cộng bạn có 10 đầu bếp).

**1. Tình huống KHÔNG CÓ Cross-Zone Load Balancing (Mạnh ai nấy làm)**
Người quản lý của chi nhánh nào (Load Balancer node) thì chỉ được phép giao việc cho đầu bếp của chi nhánh đó.
*   **Chi nhánh 1:** Quản lý nhận 50% lượng khách, và bắt **2 đầu bếp** của mình cày cuốc. Mỗi người phải gánh **25%** tổng khối lượng công việc của cả chuỗi.
*   **Chi nhánh 2:** Quản lý cũng nhận 50% lượng khách, nhưng có tới **8 đầu bếp**. Chia đều ra, mỗi người chỉ làm có **6.25%** công việc.
👉 **Hậu quả:** 2 đầu bếp ở Chi nhánh 1 làm việc kiệt sức (quá tải cục bộ), trong khi 8 đầu bếp ở Chi nhánh 2 thì ngồi chơi xơi nước.

**2. Tình huống CÓ Cross-Zone Load Balancing (Chia sẻ liên chi nhánh)**
Bây giờ, bạn cho phép các quản lý "gọi điện thoại" giao việc chéo cho nhau (Cross-Zone).
*   Các quản lý gộp chung tất cả **10 đầu bếp** thành một đội duy nhất.
*   Bất kể khách hàng bước vào Chi nhánh 1 hay Chi nhánh 2, đơn đặt món đều được chia đều đặn cho cả 10 người.
*   👉 **Kết quả:** Tuyệt vời! Mỗi đầu bếp (dù đứng ở chi nhánh nào) đều nhận chính xác **10%** khối lượng công việc. Tải được chia đều hoàn hảo, không ai bị quá sức.

### C. Mặc định và Chi phí (Ghi nhớ để thi)
Việc các quản lý gọi điện giao việc chéo cho nhau (dữ liệu đi xuyên qua các AZ) trong AWS thực tế là **bị tính phí cước viễn thông** (Inter-AZ Data Transfer). Nhưng tùy loại Load Balancer mà AWS thu tiền khác nhau:

| Loại Load Balancer | Trạng thái Mặc định (Default) | Chi phí truyền dữ liệu Inter-AZ (Pricing) |
| :--- | :--- | :--- |
| **Application Load Balancer (ALB)** | **BẬT (Enabled)** | **Miễn phí (No charges)** |
| **Network Load Balancer (NLB)** | TẮT (Disabled) | **Bị tính phí ($)** nếu bạn chủ động bật |
| **Gateway Load Balancer (GWLB)**| TẮT (Disabled) | **Bị tính phí ($)** nếu bạn chủ động bật |
| **Classic Load Balancer (CLB)** | TẮT (Disabled) | Miễn phí (No charges) nếu bật |

> 💡 **Lưu ý thực hành:** Đối với ALB, mặc dù tính năng này bật mặc định ở cấp độ Load Balancer, bạn vẫn có thể tắt nó đi ở cấp độ từng **Target Group**.

---

## Bài 81: Quản lý chứng chỉ SSL/TLS và SNI trên Load Balancer (ELB - SSL Certificates & SNI)
**Câu hỏi:** SSL/TLS là gì? Cơ chế SSL Termination trên Load Balancer hoạt động ra sao? SNI (Server Name Indication) giải quyết bài toán gì và những loại Load Balancer nào hỗ trợ SNI?

**Trả lời:**

### A. Slide bài giảng
````carousel
![Slide: SSL/TLS - Basics](C:/Users/Khoa/.gemini/antigravity/brain/bde57dc9-098a-4e1d-a715-57092610bf98/.user_uploaded/media_1788510804730.png)
<!-- slide -->
![Slide: Load Balancer - SSL Certificates](C:/Users/Khoa/.gemini/antigravity/brain/bde57dc9-098a-4e1d-a715-57092610bf98/.user_uploaded/media_1788510829690.png)
<!-- slide -->
![Slide: SSL - Server Name Indication (SNI)](C:/Users/Khoa/.gemini/antigravity/brain/bde57dc9-098a-4e1d-a715-57092610bf98/.user_uploaded/media_1788510834005.png)
<!-- slide -->
![Slide: Elastic Load Balancers - SSL Certificates Comparison](C:/Users/Khoa/.gemini/antigravity/brain/bde57dc9-098a-4e1d-a715-57092610bf98/.user_uploaded/media_1788513854386.png)
````

### B. Khái niệm cơ bản về SSL/TLS
*   **Mục đích:** Giúp mã hóa đường truyền dữ liệu giữa Client và Load Balancer (**in-flight encryption**). Ngăn chặn việc nghe lén thông tin nhạy cảm (mật khẩu, số thẻ tín dụng...).
*   **SSL vs TLS:** TLS (Transport Layer Security) là phiên bản đời mới và an toàn hơn của SSL (Secure Sockets Layer). Ngày nay hầu hết đều dùng TLS nhưng theo thói quen vẫn hay gọi chung là SSL.
*   **Chứng chỉ công khai (Public SSL Certificates):** Do các Certificate Authority (CA) phát hành (như Let's Encrypt, DigiCert, GoDaddy...). Chứng chỉ có hạn sử dụng và phải được gia hạn định kỳ.
*   **Quản lý trên AWS:** Dùng dịch vụ **ACM (AWS Certificate Manager)** để tạo và quản lý chứng chỉ hoàn toàn miễn phí.

### C. Cơ chế SSL Termination trên Load Balancer
*   **Khách hàng ➔ Load Balancer:** Đi qua Internet công cộng bằng giao thức **HTTPS (Mã hóa)**. Load Balancer nắm giữ chứng chỉ X.509 để giải mã.
*   **Load Balancer ➔ Backend EC2 Instances:** Đi qua mạng nội bộ VPC bằng giao thức **HTTP (Không mã hóa)**. Nhờ đó giảm tải xử lý mã hóa/giải mã cho các máy ảo EC2.

### D. Server Name Indication (SNI) - Trọng tâm thi SAA
*   **Vấn đề đặt ra:** Làm sao để chạy nhiều website/domain khác nhau (ví dụ: `mycorp.com` và `example.com`) trên cùng một Load Balancer, khi mỗi domain cần một chứng chỉ SSL riêng?
*   **Giải pháp (SNI):** SNI yêu cầu Client gửi tên máy chủ đích (hostname) ngay từ bước bắt tay đầu tiên (SSL handshake). Nhờ đó, Load Balancer biết khách muốn vào web nào để load đúng chứng chỉ SSL tương ứng và chuyển tiếp đến đúng Target Group.
*   **Bảng hỗ trợ SSL/SNI giữa các loại Load Balancer:**
    *   **Classic Load Balancer (CLB):** 🔴 **KHÔNG hỗ trợ SNI**. Mỗi CLB chỉ hỗ trợ đúng 1 SSL certificate. Muốn chạy nhiều domain phải tạo nhiều CLB.
    *   **Application Load Balancer (ALB v2):** 🟢 **HỖ TRỢ SNI**. Có thể gắn nhiều SSL certificate cho nhiều domain trên 1 ALB.
    *   **Network Load Balancer (NLB v2):** 🟢 **HỖ TRỢ SNI**. Tương tự ALB, hỗ trợ nhiều listener với nhiều SSL certs.
    *   **CloudFront:** 🟢 Hỗ trợ SNI.

---

## Bài 82: Thực hành cấu hình SSL trên ALB và NLB (SSL Certificates Hands On)
**Câu hỏi:** Các bước thiết lập HTTPS/TLS Listener trên ALB và NLB? Có những cách nào để nạp chứng chỉ SSL?

**Trả lời:**
1.  **Cấu hình trên ALB:**
    *   Thêm Listener với giao thức **HTTPS**, cổng mặc định **443**.
    *   Forward đến Target Group mong muốn.
    *   Chọn **Security Policy** (quy định các phiên bản TLS được phép).
    *   Chọn nguồn cung cấp chứng chỉ: **ACM (Khuyên dùng)**, **IAM (Cũ/Deprecated)**, hoặc **Import** (dán private key, certificate body, chain từ nhà cung cấp bên ngoài).
2.  **Cấu hình trên NLB:**
    *   Vì NLB chạy ở Layer 4 nên tạo Listener với giao thức **TLS** (thay vì HTTPS).
    *   Các bước chọn Target Group, Security Policy và Certificate tương tự ALB.

---

## Bài 83: Thời gian trễ hủy đăng ký (Connection Draining / Deregistration Delay)
**Câu hỏi:** Connection Draining là gì? Khác gì với Deregistration Delay? Ý nghĩa của thông số này trong việc duy trì trải nghiệm người dùng khi bảo trì hoặc khi instance bị unhealthy?

**Trả lời:**

### A. Slide bài giảng
````carousel
![Slide: Connection Draining](C:/Users/Khoa/.gemini/antigravity/brain/bde57dc9-098a-4e1d-a715-57092610bf98/.user_uploaded/media_1788514047151.png)
````

### B. Bản chất và Tên gọi
*   **Tên gọi theo loại Load Balancer:**
    *   Trên **Classic Load Balancer (CLB)**: Gọi là **Connection Draining**.
    *   Trên **ALB & NLB**: Gọi là **Deregistration Delay**.
*   **Ý nghĩa:** Khi một instance chuẩn bị bị gỡ khỏi hệ thống (Deregistering) hoặc bị đánh dấu Unhealthy, tính năng này sẽ **cho instance một khoảng thời gian chờ (grace period) để hoàn thành các request đang xử lý dở dang (in-flight requests)** trước khi chính thức ngắt kết nối.
*   **Cơ chế hoạt động trong thời gian Draining:**
    1.  ELB **ngừng chuyển các request mới** tới instance này (chuyển sang các instance lành lặn khác).
    2.  ELB chờ cho các request cũ đang chạy trên instance này hoàn tất.
    3.  Sau khi xong hết request (hoặc hết thời gian timeout), instance mới bị hủy đăng ký hoàn toàn.

### C. Thông số cấu hình
*   **Phạm vi giá trị:** Từ **1 đến 3600 giây** (tối đa 1 giờ).
*   **Mặc định:** **300 giây (5 phút)**.
*   **Tắt tính năng:** Đặt về **0 giây** (ngắt kết nối ngay lập tức).
*   **Chiến lược tối ưu:**
    *   *Đặt thấp (ví dụ 30s):* Khi ứng dụng xử lý các request ngắn, siêu nhanh (dưới 1s) giúp giải phóng/tắt instance nhanh.
    *   *Đặt cao (300s trở lên):* Khi ứng dụng có các tác vụ dài (upload file lớn, xử lý database...).

---

## Bài 84: Tổng quan về Auto Scaling Groups (ASG Overview)
**Câu hỏi:** Auto Scaling Group (ASG) là gì? Lợi ích khi kết hợp ASG với Load Balancer? Ba thông số dung lượng (Capacity) và Launch Template có vai trò gì?

**Trả lời:**

### A. Slide bài giảng
````carousel
![Slide: What's an Auto Scaling Group?](C:/Users/Khoa/.gemini/antigravity/brain/bde57dc9-098a-4e1d-a715-57092610bf98/.user_uploaded/media_1788514672618.png)
<!-- slide -->
![Slide: Auto Scaling Group in AWS](C:/Users/Khoa/.gemini/antigravity/brain/bde57dc9-098a-4e1d-a715-57092610bf98/.user_uploaded/media_1788514677002.png)
<!-- slide -->
![Slide: ASG in AWS With Load Balancer](C:/Users/Khoa/.gemini/antigravity/brain/bde57dc9-098a-4e1d-a715-57092610bf98/.user_uploaded/media_1788514681722.png)
<!-- slide -->
![Slide: ASG Attributes & Launch Template](C:/Users/Khoa/.gemini/antigravity/brain/bde57dc9-098a-4e1d-a715-57092610bf98/.user_uploaded/media_1788514686619.png)
<!-- slide -->
![Slide: ASG - CloudWatch Alarms & Scaling](C:/Users/Khoa/.gemini/antigravity/brain/bde57dc9-098a-4e1d-a715-57092610bf98/.user_uploaded/media_1788514699129.png)
````

### B. Vai trò và Thuộc tính của ASG
*   **Mục tiêu chính:**
    *   **Scale out (Mở rộng):** Tự động thêm EC2 instances khi tải tăng.
    *   **Scale in (Thu hẹp):** Tự động bớt EC2 instances khi tải giảm để tiết kiệm chi phí.
    *   **Tự phục hồi (Self-healing):** Tự động terminate máy bị lỗi (unhealthy) và tạo máy mới thay thế.
    *   **Chi phí:** ASG hoàn toàn **MIỄN PHÍ**, bạn chỉ trả tiền cho các máy ảo EC2 được tạo ra.
*   **Ba thông số dung lượng cốt lõi:**
    *   **Minimum Capacity:** Số lượng instance tối thiểu luôn luôn chạy.
    *   **Maximum Capacity:** Số lượng instance tối đa được phép mở rộng (khống chế chi phí).
    *   **Desired Capacity:** Số lượng instance mong muốn chạy ở trạng thái bình thường.
*   **Launch Template (Bản thiết kế máy ảo):** Chứa toàn bộ thông số để tạo instance (AMI, Instance type, User data script, EBS volumes, Security Groups, SSH Key, IAM Role, Subnets...). Thay thế cho Launch Configuration (đã deprecated).
*   **Kết hợp ASG + Load Balancer:**
    *   Mọi instance do ASG tạo ra tự động được đăng ký vào Target Group của Load Balancer.
    *   ASG có thể sử dụng kết quả Health Check từ Load Balancer để phát hiện và thay thế instance lỗi.

---

## Bài 85: Thực hành Auto Scaling Groups (ASG Hands On)
**Câu hỏi:** Quy trình triển khai ASG gắn vào Application Load Balancer và cách kiểm tra hành vi co giãn trong Activity History?

**Trả lời:**
1.  **Tạo Launch Template:** Chọn AMI Amazon Linux 2, loại `t2.micro`, mở Security Group port 80, nhập User Data script khởi chạy web server.
2.  **Tạo ASG:**
    *   Chọn Launch Template vừa tạo.
    *   Chọn VPC và trải đều qua tối thiểu 3 Availability Zones (AZs) để đảm bảo High Availability.
    *   Gắn vào Target Group của ALB đã tạo từ trước.
    *   **Bật Load Balancer Health Checks** (bên cạnh EC2 Health checks).
    *   Đặt dung lượng ban đầu: Desired = 1, Min = 1, Max = 1.
3.  **Kiểm tra & Thử nghiệm:**
    *   Xem tab **Activity History**: Thấy hành động launching instance tự động vì Desired = 1 > Actual = 0.
    *   Khi tăng Desired lên 2 (Max = 2): Thấy instance thứ 2 tự động sinh ra và tự chui vào Target Group của ALB.
    *   Khi giảm Desired về 1: ASG tự động terminate 1 instance để đưa dung lượng về mức yêu cầu.

---

## Bài 86: Các chính sách co giãn (ASG - Scaling Policies)
**Câu hỏi:** Phân biệt 4 loại chính sách co giãn (Target Tracking, Simple/Step, Scheduled, Predictive)? Các chỉ số (Metrics) đo lường và vai trò của Scaling Cooldown?

**Trả lời:**

### A. Slide bài giảng
````carousel
![Slide: Scaling Policies Overview](C:/Users/Khoa/.gemini/antigravity/brain/bde57dc9-098a-4e1d-a715-57092610bf98/.user_uploaded/media_1788516771133.png)
<!-- slide -->
![Slide: Predictive Scaling](C:/Users/Khoa/.gemini/antigravity/brain/bde57dc9-098a-4e1d-a715-57092610bf98/.user_uploaded/media_1788516799914.png)
<!-- slide -->
![Slide: Good Metrics to Scale on](C:/Users/Khoa/.gemini/antigravity/brain/bde57dc9-098a-4e1d-a715-57092610bf98/.user_uploaded/media_1788516918650.png)
<!-- slide -->
![Slide: Scaling Cooldowns](C:/Users/Khoa/.gemini/antigravity/brain/bde57dc9-098a-4e1d-a715-57092610bf98/.user_uploaded/media_1788516923861.png)
````

### B. Bốn loại chính sách co giãn
1.  **Target Tracking Scaling (Bám đuổi mục tiêu):** Đơn giản nhất. Đặt một giá trị đích (ví dụ giữ Average CPU ở mức 40%), ASG tự động tính toán tăng/giảm instance để bám sát mục tiêu này.
2.  **Simple / Step Scaling (Theo nấc thang):** Dựa vào CloudWatch Alarm. Khi vượt ngưỡng thì thêm/bớt số máy cố định theo từng bậc (ví dụ CPU > 70% thêm 2 máy, CPU > 85% thêm 4 máy).
3.  **Scheduled Scaling (Theo lịch định sẵn):** Dành cho các đợt tăng tải đã biết trước lịch (ví dụ: tăng min capacity lên 10 vào 17h thứ Sáu hàng tuần, hoặc ngày hội khuyến mãi).
4.  **Predictive Scaling (Dự đoán thông minh):** Sử dụng Machine Learning để phân tích lịch sử sử dụng trong quá khứ, dự đoán trước quy luật tải theo chu kỳ và tự động lên lịch co giãn đón đầu.

### C. Các chỉ số (Metrics) dùng để co giãn
*   **CPUUtilization:** Mức dùng CPU trung bình của cả nhóm (phổ biến nhất).
*   **RequestCountPerTarget:** Số lượng request trung bình trên mỗi instance từ ALB.
*   **Average Network In / Out:** Dành cho ứng dụng nghẽn băng thông mạng (tải video, dữ liệu lớn).
*   **Custom Metric:** Chỉ số tùy biến tự đẩy lên CloudWatch (ví dụ: độ dài hàng đợi tin nhắn trong SQS).

### D. Scaling Cooldown (Thời gian hạ nhiệt)
*   **Mặc định:** **300 giây (5 phút)** sau mỗi hoạt động co giãn.
*   **Mục đích:** Trong thời gian Cooldown, ASG **sẽ không khởi chạy hoặc tắt thêm bất kỳ instance nào**, giúp các chỉ số kịp ổn định và máy mới có đủ thời gian khởi động, nhận tải.
*   **Tối ưu:** Dùng **Ready-to-use AMI (Golden AMI)** để máy khởi động nhanh hơn, từ đó có thể rút ngắn thời gian Cooldown.

---

## Bài 87: Thực hành chính sách co giãn & Kỹ thuật giả lập tải / request (Hands On & Load Testing)
**Câu hỏi:** Target Tracking tạo ra những CloudWatch Alarm nào? Làm thế nào để giả lập quá tải CPU (Stress test) và giả lập lưu lượng request người dùng (Traffic simulation) để kiểm tra ASG?

**Trả lời:**

### A. Thực hành Target Tracking Policy
1.  Thiết lập Target Tracking Policy với chỉ số `Average CPU Utilization = 40%`, giới hạn ASG từ Min = 1 đến Max = 3.
2.  Hệ thống tự động sinh ra **2 CloudWatch Alarms**:
    *   `AlarmHigh`: Kích hoạt khi CPU > 40% ➔ Ra lệnh Scale Out (thêm máy).
    *   `AlarmLow`: Kích hoạt khi CPU < 28% ➔ Ra lệnh Scale In (giảm máy).

### B. Phương pháp 1: Ép tải CPU từ bên trong (Internal Stress Test)
*   **Công cụ:** `stress` trên Amazon Linux 2.
*   **Cách làm:** SSH / EC2 Instance Connect vào máy ảo và chạy:
    ```bash
    sudo amazon-linux-extras install epel -y
    sudo yum install stress -y
    stress -c 4    # Ép 4 vCPU chạy 100% công suất
    ```
*   **Hiện tượng:** CPU vọt lên 100% ➔ `AlarmHigh` kích hoạt ➔ ASG tự động tăng capacity từ 1 lên 2 máy, rồi lên 3 máy (chạm Max). Khi reboot máy tắt lệnh `stress`, CPU tụt về 0% ➔ `AlarmLow` kích hoạt ➔ ASG tự động hủy bớt máy về lại 1.

### C. Phương pháp 2: Giả lập lưu lượng Request từ bên ngoài (External Request Simulation)
> 📌 *Để kiểm tra chỉ số `RequestCountPerTarget` hoặc khả năng chịu tải thực tế từ phía Client vào Load Balancer, ta dùng các kỹ thuật sau:*

#### 1. Dùng Apache Bench (`ab`) - Nhanh, nhẹ và phổ biến nhất
Công cụ dòng lệnh chuyên dụng để gửi một lượng lớn request đồng thời:
```bash
# Cài đặt trên máy client (Linux/EC2 khác)
sudo yum install httpd-tools -y      # Amazon Linux / CentOS
# hoặc
sudo apt-get install apache2-utils   # Ubuntu / Debian

# Bắn 50.000 requests với 100 kết nối đồng thời:
ab -n 50000 -c 100 http://<DNS_LOAD_BALANCER>/
```
*   `-n`: Tổng số requests cần gửi.
*   `-c`: Số lượng kết nối đồng thời (Concurrency).

#### 2. Dùng vòng lặp cURL đơn giản (Không cần cài đặt công cụ)
Chạy trực tiếp trên Terminal / PowerShell:
*   **Trên Linux / Mac / Git Bash:**
    ```bash
    while true; do curl -s http://<DNS_LOAD_BALANCER>/ > /dev/null; done
    ```
*   **Trên PowerShell (Windows):**
    ```powershell
    while($true) { Invoke-RestMethod -Uri "http://<DNS_LOAD_BALANCER>/" | Out-Null }
    ```

#### 3. Các công cụ nâng cao (Dùng trong môi trường doanh nghiệp)
*   **k6 (Grafana k6):** Viết kịch bản load test bằng JavaScript, hiện đại, nhẹ và trực quan.
*   **JMeter:** Giao diện đồ họa (GUI) kinh điển để giả lập luồng người dùng phức tạp.
*   **Locust:** Giả lập hàng chục ngàn Virtual Users bằng Python script.

---

## Tổng hợp Bộ câu hỏi Trắc nghiệm Ôn tập (Quiz Phần 8 - ELB & ASG)

Dưới đây là tổng hợp các câu hỏi trắc nghiệm thực tế trong khóa học giúp bạn củng cố toàn bộ kiến thức về ELB và ASG:

### Câu 1: Mở rộng kích thước máy ảo (Instance Size Scaling)
*   **Đề bài:** Scaling an EC2 instance from `r4.large` to `r4.4xlarge` is called ............
*   **Đáp án:** **Vertical Scalability** (Mở rộng theo chiều dọc / Scale Up)
*   **Giải thích:** Thay đổi cấu hình phần cứng (RAM, CPU) của một máy chủ cụ thể từ nhỏ lên lớn là Vertical Scalability. Ngược lại, tăng số lượng máy là Horizontal Scalability.

### Câu 2: Tự động tăng giảm số lượng máy ảo (Instance Count Scaling)
*   **Đề bài:** Running an application on an Auto Scaling Group that scales the number of EC2 instances in and out is called ............
*   **Đáp án:** **Horizontal Scalability** (Mở rộng theo chiều ngang / Elasticity)
*   **Giải thích:** Tăng hoặc giảm số lượng máy ảo chạy song song để gánh tải (Scale out / Scale in) là bản chất của Horizontal Scalability.

### Câu 3: Điểm truy cập của Elastic Load Balancer
*   **Đề bài:** Elastic Load Balancers provide a ............
*   **Đáp án:** **static DNS name we can use in our application**
*   **Giải thích:** Vì các máy chủ Load Balancer phía sau co giãn liên tục nên địa chỉ IP của ELB thay đổi liên tục. Do đó, AWS cung cấp một tên miền tĩnh cố định (static DNS name) để ứng dụng kết nối tới.

### Câu 4: Sự cố người dùng bị đăng xuất liên tục (Session Re-authentication)
*   **Đề bài:** You are running a website on 10 EC2 instances fronted by an Elastic Load Balancer. Your users are complaining about the fact that the website always asks them to re-authenticate when they are moving between website pages. You are puzzled because it's working just fine on your machine and in the Dev environment with 1 EC2 instance. What could be the reason?
*   **Đáp án:** **The Elastic Load Balancer does not have Sticky Sessions enabled**
*   **Giải thích:** Ở môi trường Dev (1 máy), session lưu tại máy đó nên không bị hỏi lại. Ở Production (10 máy), nếu không bật Sticky Sessions, mỗi lần click chuyển trang ELB lại đẩy sang máy khác chưa có session ➔ bắt đăng nhập lại.

### Câu 5: Lấy địa chỉ IP thật của người dùng qua ALB
*   **Đề bài:** You are using an Application Load Balancer to distribute traffic to your website hosted on EC2 instances. It turns out that your website only sees traffic coming from private IPv4 addresses which are in fact your Application Load Balancer's IP addresses. What should you do to get the IP address of clients connected to your website?
*   **Đáp án:** **Modify your website's backend to get the client IP address from the `X-Forwarded-For` header**
*   **Giải thích:** ALB che IP client bằng private IP của ALB. ALB sẽ gắn thông tin gốc vào các HTTP header:
    *   `X-Forwarded-For`: Chứa IP thật của Client.
    *   `X-Forwarded-Port`: Chứa Port của Client.
    *   `X-Forwarded-Proto`: Chứa giao thức Client dùng (HTTP/HTTPS).

### Câu 6: Bảo vệ người dùng khỏi các máy ảo bị crash
*   **Đề bài:** You hosted an application on a set of EC2 instances fronted by an Elastic Load Balancer. A week later, users begin complaining that sometimes the application just doesn't work. You investigate the issue and found that some EC2 instances crash from time to time. What should you do to protect users from connecting to the EC2 instances that are crashing?
*   **Đáp án:** **Enable ELB Health Checks**
*   **Giải thích:** Khi bật Health Checks, ELB liên tục kiểm tra trạng thái máy. Nếu máy nào bị sập/unhealthy, ELB sẽ tự động ngừng điều hướng người dùng vào máy đó.

### Câu 7: Ứng dụng hiệu năng siêu cao, hàng triệu request/giây
*   **Đề bài:** You are working as a Solutions Architect for a company and you are required to design an architecture for a high-performance, low-latency application that will receive millions of requests per second. Which type of Elastic Load Balancer should you choose?
*   **Đáp án:** **Network Load Balancer (NLB)**
*   **Giải thích:** Từ khóa nhận diện NLB: *High-performance*, *Low-latency*, *Millions of requests per second*, *Layer 4 (TCP/UDP/TLS)*.

### Câu 8: Giao thức KHÔNG được hỗ trợ bởi ALB
*   **Đề bài:** Application Load Balancers support the following protocols, EXCEPT:
*   **Đáp án:** **TCP**
*   **Giải thích:** ALB hoạt động ở Layer 7 nên hỗ trợ HTTP, HTTPS, WebSocket, gRPC. TCP là giao thức Layer 4 do NLB xử lý.

### Câu 9: Tiêu chí định tuyến KHÔNG được ALB hỗ trợ
*   **Đề bài:** Application Load Balancers can route traffic to different Target Groups based on the following, EXCEPT:
*   **Đáp án:** **Client's Location (Geography)**
*   **Giải thích:** ALB định tuyến dựa trên thông tin gói tin HTTP (URL Path, Hostname, HTTP Headers, Query Strings, Source IP). Định tuyến theo vị trí địa lý của Client là tính năng của **Amazon Route 53** (Geolocation Routing) hoặc **CloudFront**.

### Câu 10: Đối tượng KHÔNG THỂ đăng ký vào Target Group của ALB
*   **Đề bài:** Registered targets in a Target Groups for an Application Load Balancer can be one of the following, EXCEPT:
*   **Đáp án:** **Network Load Balancer** (hoặc **Public IP Addresses**)
*   **Giải thích:** Target của ALB chỉ có thể là: EC2 instances, Private IP addresses, Lambda Functions, hoặc ALB khác. Không thể là NLB hay Public IP.

### Câu 11: Yêu cầu cấp IP tĩnh cố định để cấu hình Firewall
*   **Đề bài:** For compliance purposes, you would like to expose a fixed static IP address to your end-users so that they can write firewall rules that will be stable and approved by regulators. What type of Elastic Load Balancer would you choose?
*   **Đáp án:** **Network Load Balancer**
*   **Giải thích:** NLB có 1 địa chỉ IP tĩnh trên mỗi AZ và cho phép gán trực tiếp Elastic IP cố định. ALB không hỗ trợ gắn Elastic IP trực tiếp.

### Câu 12: Quy tắc đặt tên Custom Cookie trong Sticky Sessions
*   **Đề bài:** You want to create a custom application-based cookie in your Application Load Balancer. Which of the following you can use as a cookie name?
*   **Đáp án:** **APPUSERC** (hoặc bất kỳ tên nào khác 3 tên bị cấm)
*   **Giải thích:** AWS nghiêm cấm đặt tên trùng với 3 tên hệ thống dành riêng: `AWSALB`, `AWSALBAPP`, `AWSALBTG`.

### Câu 13: Xử lý lệch tải CPU giữa các AZ trên NLB
*   **Đề bài:** You have a Network Load Balancer that distributes traffic across a set of EC2 instances in `us-east-1`. You have 2 EC2 instances in `us-east-1b` AZ and 5 EC2 instances in `us-east-1e` AZ. You have noticed that the CPU utilization is higher in the EC2 instances in `us-east-1b` AZ. After more investigation, you noticed that the traffic is equally distributed across the two AZs. How would you solve this problem?
*   **Đáp án:** **Enable Cross-Zone Load Balancing**
*   **Giải thích:** Mặc định NLB tắt Cross-Zone, khiến mỗi AZ nhận 50% traffic (vùng ít máy sẽ bị quá tải CPU). Bật Cross-Zone sẽ chia đều tải cho toàn bộ 7 máy ở cả 2 vùng.

### Câu 14: Gắn nhiều chứng chỉ SSL trên cùng 1 Listener
*   **Đề bài:** Which feature in both Application Load Balancers and Network Load Balancers allows you to load multiple SSL certificates on one listener?
*   **Đáp án:** **Server Name Indication (SNI)**
*   **Giải thích:** SNI cho phép Client gửi hostname mong muốn ngay từ lúc handshake SSL, giúp Load Balancer nạp đúng chứng chỉ SSL tương ứng cho từng website.

### Câu 16: Hành vi khi CPU vượt ngưỡng nhưng đã chạm Maximum Capacity
*   **Đề bài:** You have an application hosted on a set of EC2 instances managed by an Auto Scaling Group that you configured both desired and maximum capacity to 3. Also, you have created a CloudWatch Alarm that is configured to scale out your ASG when CPU Utilization reaches 60%. Your application suddenly received huge traffic and is now running at 80% CPU Utilization. What will happen?
*   **Đáp án:** **Nothing**
*   **Giải thích:** `Maximum Capacity` là giới hạn trần tuyệt đối. Khi số máy đã bằng Max (3 = 3), ASG sẽ không tạo thêm máy mới dù chuông báo quá tải có kêu.

### Câu 17: Xử lý máy ảo bị Unhealthy trong ASG tích hợp ALB
*   **Đề bài:** You have an Auto Scaling Group fronted by an Application Load Balancer. You have configured the ASG to use ALB Health Checks, then one EC2 instance has just been reported unhealthy. What will happen to the EC2 instance?
*   **Đáp án:** **The ASG will terminate the EC2 instance**
*   **Giải thích:** Khi bật ALB Health Checks trên ASG, máy nào bị ALB báo lỗi (Unhealthy) sẽ bị ASG xóa bỏ hoàn toàn (Terminate) và tạo máy mới toanh thay thế.

### Câu 18: Co giãn ASG theo chỉ số đặc thù của ứng dụng (Database requests)
*   **Đề bài:** Your boss asked you to scale your Auto Scaling Group based on the number of requests per minute your application makes to your database. What should you do?
*   **Đáp án:** **Create a CloudWatch custom metric then create a CloudWatch Alarm on this metric to scale your ASG**
*   **Giải thích:** AWS không có metric theo dõi kết nối nội bộ ứng dụng - database. Bạn phải tự đẩy số liệu lên thành **CloudWatch Custom Metric** và gắn Alarm vào chỉ số đó.

### Câu 20: Chuyển đổi Health Check từ TCP sang HTTP trên NLB
*   **Đề bài:** You have an ASG and a Network Load Balancer. The application on your ASG supports the HTTP protocol and is integrated with the Load Balancer health checks. You are currently using the TCP health checks. You would like to migrate to using HTTP health checks, what do you do?
*   **Đáp án:** **Migrate the health check to HTTP**
*   **Giải thích:** NLB hỗ trợ sẵn cả TCP, HTTP, HTTPS health checks trên Target Group. Bạn không cần phải đổi loại Load Balancer sang ALB, chỉ cần đổi cấu hình health check của Target Group sang HTTP.

### Câu 21: Bắt buộc người dùng chuyển hướng từ HTTP sang HTTPS
*   **Đề bài:** You have a website hosted in EC2 instances in an Auto Scaling Group fronted by an Application Load Balancer. Currently, the website is served over HTTP, and you have been tasked to configure it to use HTTPS. You have created a certificate in ACM and attached it to the Application Load Balancer. What you can do to force users to access the website using HTTPS instead of HTTP?
*   **Đáp án:** **Configure the Application Load Balancer to redirect HTTP to HTTPS**
*   **Giải thích:** Tạo luật trên HTTP listener (port 80) của ALB để trả về mã chuyển hướng (Redirect 301) sang HTTPS (port 443). Bản ghi DNS không có khả năng redirect giao thức.