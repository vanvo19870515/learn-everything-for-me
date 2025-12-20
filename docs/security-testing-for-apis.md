# 🚀Security Testing for APIs - Hướng dẫn Kiểm thử Bảo mật cho API
## Why is API Security Testing Important?
### *APIs are the backbone of modern applications and are exposed to the internet, making them a prime target for attackers. A security breach can lead to data theft, service disruption, and compliance violations.*

****Tại sao Kiểm thử Bảo mật API lại Quan trọng?
API là xương sống của các ứng dụng hiện đại và được tiếp xúc với internet, khiến chúng trở thành mục tiêu hàng đầu của kẻ tấn công. Một vi phạm bảo mật có thể dẫn đến đánh cắp dữ liệu, gián đoạn dịch vụ và vi phạm quy định.***

#### **Common API Security Vulnerabilities (OWASP API Security Top 10):**

1. ***Broken Object Level Authorization (BOLA)***: Kiểm tra xem user có thể truy cập tài nguyên của người khác không (vd: thay đổi ID trong URL để xem/thay đổi dữ liệu của user khác).

2. ***Broken Authentication:*** Kiểm tra cơ chế xác thực (token, JWT) có bị lỗi không (vd: token không hết hạn, dễ đoán).

3. ***Excessive Data Exposure:*** API trả về quá nhiều dữ liệu so với cần thiết (vd: trả về tất cả fields của user, kể cả mật khẩu hash).

4. ***Lack of Resources & Rate Limiting:*** Kiểm tra xem API có bị tấn công DDoS không (vd: gọi API 1000 lần/giây).

5. ***Broken Function Level Authorization:*** Kiểm tra phân quyền chức năng (vd: user thường có thể gọi API chỉ dành cho admin).

6. ***Mass Assignment:*** Kiểm tra xem client có thể gán giá trị cho các fields không được phép không (vd: thêm field "role":"admin" trong request body).

7. ***Security Misconfiguration:*** Cấu hình sai (vd: để lộ thông tin debug, dùng các phương thức HTTP không cần thiết).

8. ***Injection:*** SQL, NoSQL, Command Injection (vd: nhập mã độc vào input).

9. ***Improper Assets Management:*** Quản lý phiên bản API (vd: API phiên bản cũ chứa lỗ hổng).

10. ***Insufficient Logging & Monitoring:*** Không ghi log đầy đủ, khó phát hiện tấn công.

### **Step-by-Step Guide to API Security Testing - Hướng dẫn Từng bước Kiểm thử Bảo mật API:**

**Step 1:** ***Information Gathering - Thu thập Thông tin***

- Xác định tất cả các endpoints (URL, method).

- Thu thập tài liệu API (Swagger, OpenAPI).

- Xác định các tham số đầu vào (query, body, header).

- Xác định cơ chế xác thực (API key, JWT, OAuth).

**Step 2:** ***Authentication & Authorization Testing - Kiểm thử Xác thực và Phân quyền***

a. *Kiểm tra xác thực (Authentication):*

- Gửi request không có token → mong đợi 401.

- Gửi token sai/hết hạn → mong đợi 401.

- Kiểm tra JWT (nếu có) tại trang web jwt.io để xem nội dung và chữ ký.

b. *Kiểm tra phân quyền (Authorization - BOLA):*

*Ví dụ:* User A có ID=1, User B có ID=2.

- Đăng nhập bằng token của User A, thử truy cập GET /api/users/2 (tài nguyên của User B) → mong đợi 403 (Forbidden).

- Thử thay đổi method (vd: User A thử xóa tài nguyên của User B) → mong đợi 403.

**Step 3:** ***Input Validation & Injection Testing - Kiểm thử Đầu vào và Chèn mã***

- *SQL Injection:* Gửi các ký tự đặc biệt (', ", --, ;) trong các trường input (query string, body) và quan sát phản hồi. Sử dụng công cụ như sqlmap.

- *NoSQL Injection:* Với MongoDB, thử gửi operators như {"$ne": null} trong trường input.

- *Command Injection:* Nếu API gọi hệ thống, thử các lệnh như ; ls, | dir (tùy hệ điều hành).

**Step 4:** ***Business Logic Testing - Kiểm thử Logic Nghiệp vụ***

- *Kiểm tra Mass Assignment:* Thử thêm các field không được document (vd: "role":"admin", "isActive":true) trong request body để xem hệ thống có lưu không.

- *Kiểm tra giới hạn tài nguyên (Rate Limiting):* Gọi API 100 lần trong 10 giây để xem có bị chặn không (mong đợi 429 Too Many Requests).

**Step 5:** ***Security Headers & Configuration - Kiểm tra Header và Cấu hình***

- Kiểm tra các security headers trong response:

- Content-Security-Policy

- X-Content-Type-Options: nosniff

- X-Frame-Options: DENY

- Strict-Transport-Security (HSTS)

- Kiểm tra các phương thức HTTP không cần thiết (vd: OPTIONS, TRACE) có bị vô hiệu hóa không.

- Kiểm tra phiên bản API (vd: api/v1/ vs api/v2/) để tránh lỗi phiên bản cũ.

**Step 6:** ***Tools for API Security Testing - Công cụ Kiểm thử Bảo mật API***

*Postman:* Viết script để tự động hóa các kiểm thử bảo mật (vd: kiểm tra BOLA, injection).

*OWASP ZAP (Zed Attack Proxy):* Công cụ tự động quét lỗ hổng bảo mật, hỗ trợ API.

*Burp Suite:* Công cụ chuyên sâu để kiểm thử bảo mật, có thể dùng để intercept request và thay đổi.

*Nmap:* Quét cổng và dịch vụ.

#### *Example Security Test Case for an API - Ví dụ Test Case Bảo mật cho API:*

**Test Case:** ***Kiểm tra Broken Object Level Authorization (BOLA)***

**Tiền đề:** User A (ID=1) và User B (ID=2) đều có tài nguyên riêng (vd: đơn hàng).

- **Bước 1:** Đăng nhập bằng User A, lấy token.

- **Bước 2:** Gọi API GET /api/orders/1 (đơn hàng của chính User A) → mong đợi 200.

- **Bước 3:** Gọi API GET /api/orders/2 (đơn hàng của User B) → mong đợi 403 (Forbidden).

=> **Kết quả:** Nếu bước 3 trả về 200, thì API có lỗi BOLA.

***Lưu ý:***

- *Security testing thường đòi hỏi kiến thức chuyên sâu. Hãy bắt đầu với các lỗ hổng phổ biến (OWASP Top 10) và sử dụng các công cụ tự động.*

- *Luôn thảo luận với nhóm phát triển và bảo mật (nếu có) trước khi thực hiện các kiểm thử xâm nhập (penetration testing) để tránh ảnh hưởng đến hệ thống.*