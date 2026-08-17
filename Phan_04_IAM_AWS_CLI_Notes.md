# Ôn tập: Phần 4 - IAM & AWS CLI

Tài liệu này lưu lại các câu hỏi và giải đáp quan trọng trong quá trình học để tiện ôn tập.

---

## 1. Tổng quan về phần IAM & AWS CLI (IAM & AWS CLI Overview)
**Câu hỏi:** Giải thích sơ qua về nội dung phần IAM & AWS CLI là học về cái gì?

**Trả lời:**
Phần này tập trung vào việc quản lý danh tính, phân quyền bảo mật và tương tác với AWS bằng dòng lệnh.
*   **IAM (Identity and Access Management):**
    *   **Users:** Tài khoản cho người hoặc ứng dụng.
    *   **Groups:** Nhóm các Users để cấp quyền hàng loạt.
    *   **Policies:** Các bộ quy tắc định nghĩa quyền hạn (được làm gì, không được làm gì).
    *   **Roles:** Vai trò cấp quyền tạm thời, thường dùng để cho phép các dịch vụ AWS giao tiếp với nhau (VD: EC2 truy cập S3).
*   **Bảo mật:** Sử dụng MFA (xác thực 2 bước) và các Best Practices (như nguyên tắc cấp quyền tối thiểu).
*   **AWS CLI & CloudShell:** Học cách dùng Access Keys để gõ lệnh điều khiển AWS từ terminal (trên máy tính hoặc ngay trên trình duyệt với CloudShell).

---

## 2. Thêm Tag vào User đã tạo (Adding Tags to an Existing User)
**Câu hỏi:** Cách thêm tag vào một user đã tạo trên giao diện AWS Console?

**Trả lời:**
1. Ở menu bên trái, vào mục **Users**.
2. Bấm trực tiếp vào **tên của User** cần thêm tag (không phải tick ô vuông).
3. Trong trang chi tiết, chọn tab **Tags**.
4. Bấm **Manage tags** (hoặc Add tags).
5. Nhập Key và Value mong muốn rồi nhấn **Save changes**.

---

## 3. Tìm Account ID / Alias để đăng nhập (Finding Account ID or Alias for Login)
**Câu hỏi:** Alias login của IAM user là gì và xem ở đâu khi cần đăng nhập?

**Trả lời:**
Khi đăng nhập bằng IAM User, AWS yêu cầu nhập **Account ID** hoặc **Account alias**.
*   **Cách tìm:** Đăng nhập bằng tài khoản Root (hoặc tài khoản có quyền xem), nhìn lên góc trên cùng bên phải giao diện Console. Bạn sẽ thấy dòng chữ dạng: `[Bí danh] ([Account ID 12 số])`. Ví dụ: `khoa6430 (772023874740)`.
*   **Cách đăng nhập:** Ở ô **Account ID or alias**, bạn có điền bí danh (`khoa6430`) cho dễ nhớ, hoặc điền chuỗi số (`772023874740`) đều được. Sau đó điền IAM username và Password ở các ô dưới.

---

## 4. Cấu trúc của IAM Policy (IAM Policies Structure)
**Câu hỏi:** Cấu trúc của một định dạng IAM Policy (JSON) gồm những thành phần nào?

**Trả lời:**
Một IAM Policy thường được viết bằng định dạng JSON và bao gồm các thành phần chính sau:
*   **Version:** Phiên bản ngôn ngữ của policy, thường luôn luôn là `"2012-10-17"`.
*   **Id:** (Không bắt buộc) Mã định danh cho policy.
*   **Statement:** (Bắt buộc) Chứa một hoặc nhiều khối (block) quy định quyền hạn cụ thể. 

Bên trong mỗi khối **Statement** sẽ gồm có:
*   **Sid:** (Không bắt buộc) Định danh riêng cho khối statement đó.
*   **Effect:** Định nghĩa xem statement này là Cho phép (`Allow`) hay Từ chối (`Deny`) quyền truy cập.
*   **Principal:** (Thường có trong Resource-based policies) Chỉ định tài khoản, user, hoặc role nào được áp dụng policy này.
*   **Action:** Danh sách các hành động (API calls) được cho phép/từ chối (Ví dụ: `"s3:GetObject"`, `"s3:PutObject"`).
*   **Resource:** Danh sách các tài nguyên (như S3 bucket, EC2 instance) mà các hành động trên được áp dụng vào (Ví dụ: `"arn:aws:s3:::mybucket/*"`).
*   **Condition:** (Không bắt buộc) Điều kiện cụ thể để policy này có hiệu lực (Ví dụ: người dùng phải đăng nhập bằng MFA, hoặc IP truy cập phải nằm trong một khoảng nhất định).

**Ví dụ minh họa một cấu trúc JSON Policy:**
```json
{
  "Version": "2012-10-17",
  "Id": "S3-Account-Permissions",
  "Statement": [
    {
      "Sid": "1",
      "Effect": "Allow",
      "Principal": {
        "AWS": ["arn:aws:iam::123456789012:root"]
      },
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": ["arn:aws:s3:::mybucket/*"]
    }
  ]
}
```

---

## 5. Tổng quan về bảo mật IAM & MFA (IAM & MFA Security Overview)
**Câu hỏi:** Có những cơ chế phòng thủ nào để bảo vệ tài khoản IAM (đặc biệt là tài khoản Root) khỏi bị xâm nhập?

**Trả lời:**
Có 2 cơ chế phòng thủ chính trên AWS để bảo vệ tài khoản:

### A. Chính sách mật khẩu (Password Policy)
Mật khẩu càng mạnh thì tài khoản càng an toàn, giúp chống lại các cuộc tấn công dò mật khẩu (brute force attacks). Trong AWS, bạn có thể thiết lập:
*   Yêu cầu độ dài tối thiểu của mật khẩu.
*   Yêu cầu bắt buộc các loại ký tự (chữ hoa, chữ thường, số, ký tự đặc biệt).
*   Cho phép/Không cho phép IAM users tự đổi mật khẩu.
*   Bắt buộc đổi mật khẩu sau một khoảng thời gian (VD: mật khẩu hết hạn sau 90 ngày).
*   Ngăn chặn việc tái sử dụng lại các mật khẩu cũ.

### B. Xác thực đa yếu tố (MFA - Multi-Factor Authentication)
Đây là cơ chế bắt buộc và được AWS khuyên dùng mạnh mẽ (đặc biệt cho tài khoản Root). Nó kết hợp giữa **"Mật khẩu bạn biết"** và **"Thiết bị bảo mật bạn sở hữu"**. Dù hacker có đánh cắp được mật khẩu, họ cũng không thể đăng nhập nếu không có thiết bị vật lý của bạn.

**Các tùy chọn thiết bị MFA trong AWS (rất hay hỏi trong bài thi):**
1.  **Virtual MFA device (MFA ảo trên điện thoại):** Sử dụng các ứng dụng như Google Authenticator (chỉ dùng trên 1 thiết bị) hoặc Authy (hỗ trợ đồng bộ nhiều thiết bị). Một ứng dụng có thể chứa mã MFA cho nhiều user và tài khoản AWS khác nhau. Rất tiện lợi.
2.  **Universal 2nd Factor (U2F) Security Key:** Khóa bảo mật vật lý cắm cổng USB (Ví dụ: YubiKey do bên thứ 3 cung cấp). Một khóa có thể hỗ trợ xác thực cho nhiều user (Root và IAM).
3.  **Hardware key fob MFA device:** Thiết bị tạo mã bằng phần cứng nhỏ gọn do bên thứ 3 cung cấp (Ví dụ: Gemalto).
4.  **Hardware key fob for AWS GovCloud (US):** Thiết bị tạo mã phần cứng dành riêng cho đám mây chính phủ Mỹ (do SurePassID cung cấp).

---

## 6. AWS CLI là gì và tại sao phải cài đặt? (What is AWS CLI and Why Install It?)
**Câu hỏi:** AWS CLI có tác dụng gì mà khóa học yêu cầu phải cài đặt vào máy tính?

**Trả lời:**
**AWS CLI (Command Line Interface)** là công cụ dòng lệnh do chính AWS cung cấp. Mặc dù bạn có thể làm mọi thứ trên trình duyệt web (AWS Console), nhưng trong thực tế đi làm, việc cài đặt và sử dụng AWS CLI mang lại những lợi ích bắt buộc phải có:

1.  **Quản lý nhanh và trực tiếp:** Thay vì mất công mở web, đăng nhập, click qua hàng tá menu để tạo một máy chủ ảo hay một bucket lưu trữ, bạn chỉ cần mở Terminal lên và gõ 1 dòng lệnh (Ví dụ: `aws s3 mb s3://my-bucket`) là hệ thống sẽ tạo ngay lập tức.
2.  **Khả năng tự động hóa (Automation):** Đây là sức mạnh lớn nhất. Bạn có thể viết một đoạn script (kịch bản) để máy tính tự động chạy hàng loạt lệnh CLI vào ban đêm (ví dụ: tự động sao lưu dữ liệu lúc 12h đêm, tự động tắt server khi không dùng). Giao diện web không thể giúp bạn tự động hóa được.
3.  **Chuẩn mực của dân IT (DevOps/Sysadmin):** Khi làm việc ở môi trường chuyên nghiệp, các kỹ sư thao tác quản lý hàng ngàn server chủ yếu bằng dòng lệnh chứ không ai click chuột thủ công.
4.  **Kiến thức thi:** Trong đề thi SAA-C03, rất nhiều câu hỏi yêu cầu bạn phải biết cách tương tác với AWS thông qua CLI, nên việc cài đặt để thực hành theo giảng viên là cách học hiệu quả nhất.

---

## 7. Cấu hình xác thực cho AWS CLI (aws configure - Configuring Credentials for AWS CLI)
**Câu hỏi:** Đã cài xong AWS CLI nhưng làm sao để gõ được lệnh `aws iam list-users`?

**Trả lời:**
Để có thể gõ các lệnh lấy dữ liệu từ tài khoản AWS của bạn (như lấy danh sách user), AWS CLI cần biết **"Bạn là ai?"** và **"Bạn có quyền truy cập không?"**. Do đó, trước khi gõ bất kỳ lệnh nào khác, bạn bắt buộc phải làm bước xác thực bằng lệnh `aws configure`.

**Các bước thực hiện:**
1. Trên trình duyệt web (AWS Console), vào **IAM -> Users**, bấm vào user của bạn.
2. Chuyển sang tab **Security credentials**, kéo xuống mục **Access keys** và tạo một Access key mới (chọn Use case là Command Line Interface - CLI).
3. Hệ thống sẽ cấp cho bạn một cặp khóa gồm: `Access Key ID` và `Secret Access Key` (Nhớ copy lại vì Secret Key chỉ hiện 1 lần).
4. Mở Command Prompt (cmd) trên máy tính và gõ lệnh:
   ```bash
   aws configure
   ```
5. Nhập lần lượt các thông tin mà hệ thống yêu cầu:
   * **AWS Access Key ID:** Dán ID của bạn vào rồi Enter.
   * **AWS Secret Access Key:** Dán Secret Key vào rồi Enter.
   * **Default region name:** Nhập mã khu vực (Ví dụ: `eu-west-1` như trong video, hoặc `us-east-1`).
   * **Default output format:** Nhập `json`.
6. Sau khi điền xong 4 bước trên, máy tính của bạn đã được kết nối an toàn với AWS. Giờ thì bạn có thể gõ lệnh để liệt kê danh sách user:
   ```bash
   aws iam list-users
   ```

---

## 8. Kiểm tra kết nối và Lưu ý Bảo mật Cực kỳ Quan trọng (Verifying Connection & Security Best Practices)
**Câu hỏi:** Làm sao để biết AWS CLI đã kết nối thành công và có lưu ý bảo mật nào quan trọng liên quan đến Access Key?

**Trả lời:**

### A. Kiểm tra kết nối thành công (Verifying Connection Success)
Để kiểm tra xem AWS CLI đã được cấu hình đúng và có thể giao tiếp với tài khoản AWS của bạn hay chưa, hãy chạy lệnh sau:
```bash
aws iam list-users
```
Nếu thành công, AWS sẽ trả về danh sách các IAM Users trong tài khoản dưới dạng JSON, ví dụ:
```json
{
    "Users": [
        {
            "Path": "/",
            "UserName": "khoa",
            "Arn": "arn:aws:iam::772023874740:user/khoa",
            ...
        }
    ]
}
```

### B. CẢNH BÁO BẢO MẬT QUAN TRỌNG (AWS Security Best Practices)
> [!CAUTION]
> **KHÔNG BAO GIỜ** chia sẻ, gửi hoặc tải lên mạng (như GitHub, diễn đàn, hoặc các cuộc trò chuyện AI công khai) cặp thông tin **Access Key ID** và **Secret Access Key**. 
> Nếu hacker hoặc các công cụ quét tự động quét được cặp Key này, họ có thể chiếm quyền điều khiển tài khoản AWS của bạn, tạo ra các máy chủ ảo cấu hình mạnh để đào coin và bạn sẽ phải trả hàng ngàn USD tiền hóa đơn.

**Cách xử lý khẩn cấp khi lỡ lộ Access Key:**
1. Truy cập **AWS Console** -> **IAM** -> **Users**.
2. Chọn đúng **User** có Access Key bị lộ.
3. Chuyển sang tab **Security credentials**.
4. Kéo xuống mục **Access keys**.
5. Tìm Access Key ID bị lộ (ví dụ: `AKIA3HQBVOS2JBPWQB3C`).
6. Nhấn vào **Actions** -> Chọn **Deactivate** (để vô hiệu hóa tạm thời).
7. Sau khi xác nhận vô hiệu hóa, nhấn tiếp **Actions** -> Chọn **Delete** để xóa vĩnh viễn khóa này.
8. Tạo một Access Key mới để cấu hình lại bằng lệnh `aws configure` trên máy tính.

---

## 9. AWS CloudShell - Giải pháp thay thế CLI cục bộ (AWS CloudShell - Local CLI Alternative)
**Câu hỏi:** AWS CloudShell là gì? Các ưu điểm và tính năng chính của nó so với việc cài đặt AWS CLI trên máy tính cá nhân?

**Trả lời:**
**AWS CloudShell** là một trình mô phỏng terminal được tích hợp trực tiếp trên nền tảng web (AWS Console), cho phép bạn chạy lệnh AWS CLI ngay trên trình duyệt mà không cần cài đặt bất kỳ thứ gì trên máy tính cá nhân.

### A. Các đặc điểm nổi bật của AWS CloudShell (Key Features of AWS CloudShell)
1.  **Không cần cấu hình thông tin xác thực (`aws configure`):** CloudShell tự động sử dụng quyền truy cập (credentials) của tài khoản mà bạn đang dùng để đăng nhập vào AWS Console. Bạn có thể gõ ngay các lệnh như `aws iam list-users` mà không cần Access Key & Secret Key.
2.  **Khu vực hỗ trợ (Region Availability):** CloudShell không có sẵn ở tất cả các region trên toàn thế giới. 
    *   *Lưu ý:* Nếu region hiện tại của bạn không hỗ trợ CloudShell, bạn có thể chuyển sang một region có hỗ trợ để sử dụng.
    *   *Default Region:* Trong CloudShell, region mặc định sẽ tự động khớp với region bạn đang chọn trên giao diện Console.
3.  **Lưu trữ tệp tin bền vững (Persistent Storage):** Mỗi region có CloudShell sẽ cấp cho bạn một thư mục lưu trữ nhỏ. Các tệp bạn tạo ra (ví dụ: `demo.txt`) sẽ vẫn được giữ lại ngay cả khi bạn khởi động lại (restart) CloudShell.
4.  **Hoàn toàn miễn phí:** CloudShell là một tiện ích đi kèm miễn phí của AWS.

### B. Các tính năng hữu ích trong CloudShell (Useful Features in CloudShell)
*   **Tải lên/Tải xuống tệp tin (Upload/Download files):** Bạn có thể tải file từ máy tính lên CloudShell hoặc tải file kết quả từ CloudShell về máy tính rất dễ dàng thông qua menu **Actions**.
*   **Chia nhỏ màn hình (Split screen) & Đa tab (Tabs):** Cho phép mở nhiều tab hoặc chia đôi cửa sổ terminal theo cột để chạy nhiều tác vụ cùng một lúc.
*   **Tùy chỉnh giao diện:** Thay đổi kích thước phông chữ (nhỏ, vừa, lớn) và giao diện màu tối (dark theme) hoặc sáng (light theme).

**💡 Kết luận:** Việc sử dụng AWS CloudShell hay Terminal cục bộ (trên máy tính của bạn) đều mang lại kết quả như nhau trong suốt khóa học này. Bạn có thể chọn cách nào thuận tiện nhất cho bản thân.

---

## 10. Thực hành tạo IAM Role (IAM Roles Hands-on)
**Câu hỏi:** IAM Role là gì? Các bước cơ bản để tạo một IAM Role cấp quyền cho dịch vụ AWS (ví dụ: EC2)?

**Trả lời:**
**IAM Role** (Vai trò) là một thực thể IAM dùng để cấp quyền hạn tạm thời cho các dịch vụ của AWS (như EC2, Lambda) hoặc các ứng dụng bên ngoài thực hiện các thao tác trên tài nguyên AWS của bạn. Khác với IAM User (người dùng cụ thể), Role không đi kèm mật khẩu hay Access Key dài hạn, giúp tăng độ bảo mật.

### Các bước thực hành tạo IAM Role cho EC2 (Hands-on Steps: Creating an IAM Role for EC2):
1.  **Vào trang quản lý Roles:** Trên giao diện IAM Console, ở thanh menu bên trái, chọn **Roles** -> Bấm nút **Create role** (Tạo vai trò).
2.  **Chọn loại thực thể tin cậy (Trusted entity type):**
    *   Chọn **AWS service** (Dịch vụ của AWS) - Đây là tùy chọn phổ biến nhất để cho phép các dịch vụ AWS tương tác với nhau.
    *   Ở phần **Service or use case** (Dịch vụ hoặc trường hợp sử dụng), chọn **EC2** (để cấp quyền cho máy chủ ảo EC2).
    *   Nhấn **Next** (Tiếp theo).
3.  **Gán chính sách quyền hạn (Attach permissions policies):**
    *   Tìm kiếm và chọn chính sách **IAMReadOnlyAccess** (Quyền chỉ đọc dữ liệu IAM). Việc này cho phép EC2 instance sau này có thể đọc/truy vấn danh sách thông tin trong IAM.
    *   Nhấn **Next** (Tiếp theo).
4.  **Đặt tên và kiểm tra lại (Name, review, and create):**
    *   Đặt tên cho Role: ví dụ `DemoRoleForEC2`.
    *   Kiểm tra phần **Trusted entities** (Thực thể tin cậy) xem đã đúng là dịch vụ `ec2.amazonaws.com` chưa.
    *   Kiểm tra lại danh sách Policies xem đã có `IAMReadOnlyAccess` chưa.
    *   Nhấn **Create role** (Tạo vai trò) ở cuối trang để hoàn tất.

*Lưu ý:* Vai trò này đã sẵn sàng nhưng sẽ chỉ thực sự được sử dụng khi chúng ta sang phần học về **EC2** để gán trực tiếp vào máy chủ ảo.

---

## 11. Tóm tắt nội dung về IAM (IAM Summary)
**Câu hỏi:** Tổng kết lại các khái niệm cốt lõi đã học trong phần IAM và cách kiểm toán (audit) quyền hạn IAM?

**Trả lời:**

### A. Các khái niệm cốt lõi (Core Concepts)
1.  **IAM Users:** Đại diện cho nhân viên/người dùng thực tế trong tổ chức. Họ sử dụng **Password** (mật khẩu) để đăng nhập vào AWS Console.
2.  **IAM Groups:** Nhóm các Users lại với nhau để phân quyền hàng loạt (Lưu ý: Nhóm chỉ chứa Users, không chứa nhóm khác).
3.  **IAM Policies (JSON):** Các tài liệu định dạng JSON dùng để định nghĩa quyền hạn (cho phép hay từ chối những hành động nào trên tài nguyên nào) và được gắn trực tiếp vào Users hoặc Groups.
4.  **IAM Roles:** Cấp quyền hạn tạm thời cho các thực thể phi con người, chủ yếu là các dịch vụ AWS (như EC2, Lambda).
5.  **Security (Bảo mật):** 
    *   Kích hoạt **MFA** (Xác thực đa yếu tố) để tăng cường bảo mật.
    *   Thiết lập **Password Policy** để bắt buộc người dùng đặt mật khẩu mạnh và đổi mật khẩu định kỳ.
6.  **Tương tác với AWS:**
    *   **AWS CLI:** Công cụ dòng lệnh (Terminal) để quản trị AWS.
    *   **AWS SDK:** Bộ phát triển phần mềm giúp tương tác với AWS thông qua các ngôn ngữ lập trình.
    *   **Access Keys:** Cặp khóa xác thực (`Access Key ID` & `Secret Access Key`) dùng để cấp quyền truy cập cho CLI hoặc SDK.

### B. Kiểm toán và giám sát bảo mật IAM (IAM Auditing & Monitoring)
Để rà soát và đảm bảo an toàn bảo mật cho hệ thống, AWS cung cấp 2 công cụ quan trọng:
1.  **IAM Credentials Report (Báo cáo thông tin xác thực - Cấp độ tài khoản):**
    *   Cho phép tải về một file báo cáo (dạng `.csv`) liệt kê tất cả người dùng trong tài khoản AWS.
    *   Báo cáo hiển thị chi tiết trạng thái bảo mật của từng user: mật khẩu tạo khi nào, có bật MFA không, có dùng Access Key không, lần cuối cùng đổi mật khẩu/sử dụng key là khi nào, v.v.
2.  **IAM Access Advisor (Cố vấn truy cập - Cấp độ chi tiết User/Role):**
    *   Hiển thị danh sách các dịch vụ mà một User hoặc Role có quyền truy cập, kèm theo thông tin **lần cuối cùng dịch vụ đó được truy cập là khi nào**.
    *   Công cụ này cực kỳ hữu ích để tìm ra các quyền được cấp nhưng không bao giờ sử dụng tới, giúp người quản trị thu hồi bớt quyền để tuân thủ nguyên tắc **Cấp quyền tối thiểu (Least Privilege)**.
