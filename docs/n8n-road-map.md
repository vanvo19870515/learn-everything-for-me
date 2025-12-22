HỌC N8N TỪ CON SỐ 0 - HƯỚNG DẪN SIÊU CHI TIẾT DÀNH CHO NGƯỜI MỚI
PHẦN 1: HIỂU N8N NHƯ "NGƯỜI GIÚP VIỆC ẢO" CỦA BẠN
1.1 Giải thích bằng ví dụ cụ thể hàng ngày
Ví dụ 1: Bạn là giáo viên

```text
Mỗi tuần bạn phải:
1. Nhận bài tập từ email học sinh
2. Chấm điểm và ghi vào Excel
3. Gửi email phản hồi cho từng học sinh
4. Tổng hợp điểm vào Google Sheets
5. Nhắn tin cho phụ huynh học sinh yếu

Với n8n, bạn chỉ cần:
- Cài đặt 1 lần duy nhất
- Từ đó về sau, mọi việc tự động chạy
- Bạn có thêm thời gian dạy học thay vì làm việc vặt
  Ví dụ 2: Bạn là chủ shop online nhỏ

```

```text
Hàng ngày bạn phải:
1. Check Facebook/Instagram có ai nhắn đặt hàng không
2. Ghi đơn hàng vào sổ Excel
3. Tính tiền ship, tổng tiền
4. Nhắn tin xác nhận cho khách
5. Đăng ký đơn hàng với đối tác vận chuyển

n8n sẽ làm hết tất cả, bạn chỉ cần đóng gói hàng!
1.2 n8n là gì? So sánh dễ hiểu
```

```text
n8n = "nối" + "tự động"
Giống như:
- Công tắc tự động bật đèn khi trời tối
- Robot hút bụi tự chạy
- Email trả lời tự động khi bạn đi nghỉ
  Bảng so sánh với công cụ khác:

Công cụ	Giống như	Giá	Ưu điểm	Nhược điểm
n8n	Người giúp việc đa năng	MIỄN PHÍ 100%	Tự cài trên máy mình, không giới hạn	Phải tự cài đặt
Zapier	Người giúp việc chuyên nghiệp	20-100$/tháng	Dễ dùng, không cần cài	Đắt, giới hạn số lần chạy
IFTTT	Người giúp việc đơn giản	Miễn phí/trả phí	Rất đơn giản	Ít tính năng
Make	Kỹ sư tự động hóa	10-50$/tháng	Mạnh mẽ	Phức tạp, đắt
1.3 Các khái niệm CƠ BẢN NHẤT trong n8n
1. Workflow (Luồng công việc):

```

```text
- Là 1 bản vẽ kế hoạch tự động hóa
- Ví dụ: "Kế hoạch gửi email sinh nhật"
- Mỗi workflow làm 1 việc cụ thể
2. Node (Nút):

```

```text
- Là 1 bước trong kế hoạch
- Ví dụ:
    + Bước 1: Đọc danh sách sinh nhật (node Google Sheets)
    + Bước 2: Gửi email (node Gmail)
    + Bước 3: Nhắn tin Telegram (node Telegram)
3. Connection (Kết nối):

```

```text
- Là mũi tên nối các node
- Chỉ đường đi của dữ liệu
- Ví dụ: Dữ liệu từ Google Sheets → Gmail → Telegram
4. Trigger (Kích hoạt):

```

```text
- Là cái BẬT công tắc
- Ví dụ:
    + Hàng ngày lúc 8h sáng (Schedule)
    + Khi có email mới (Email Trigger)
    + Khi có ai đó gửi tin nhắn (Webhook)
      PHẦN 2: CÀI ĐẶT n8n - TỪNG BƯỚC TỪ A ĐẾN Z
      2.1 Cài đặt trên Windows (CÁCH DỄ NHẤT CHO NGƯỜI MỚI)
      Phương pháp DỄ NHẤT: Dùng npx (không cần cài gì trước)

Bước 1: Mở Command Prompt

Nhấn Windows + R

Gõ cmd → Enter

Bước 2: Chạy lệnh đơn giản

```

```cmd
npx n8n
*Lần đầu chạy sẽ mất 1-2 phút để tải về*

Bước 3: Truy cập n8n

Mở trình duyệt (Chrome/Firefox)

Gõ: http://localhost:5678

Username: admin@n8n.io

Password: password

Bước 4: Đổi mật khẩu (QUAN TRỌNG!)

```

```text
1. Click vào avatar góc phải
2. Chọn "Settings"
3. Chọn "Change Password"
4. Đặt mật khẩu mạnh
   2.2 Cài đặt bằng Docker (ỔN ĐỊNH HƠN)
   Bước 1: Cài Docker Desktop (nếu chưa có)

Vào https://www.docker.com/products/docker-desktop

Download phiên bản Windows

Cài đặt bình thường (next → next → finish)

Bước 2: Tạo thư mục cho n8n

Tạo folder C:\n8n trên máy tính

Trong folder đó, tạo file mới tên docker-compose.yml

Bước 3: Copy nội dung này vào file:

```

```yaml
version: '3.8'

services:
n8n:
image: n8nio/n8n
container_name: n8n
restart: always  # Tự động chạy lại nếu tắt
ports:
- "5678:5678"  # Cổng truy cập
environment:
- N8N_BASIC_AUTH_ACTIVE=true
- N8N_BASIC_AUTH_USER=admin
- N8N_BASIC_AUTH_PASSWORD=matkhau123  # ĐỔI MẬT KHẨU NÀY!
- N8N_HOST=localhost
volumes:
- n8n_data:/home/node/.n8n  # Lưu dữ liệu
- C:/n8n/local-files:/files  # Thư mục chia sẻ

volumes:
n8n_data:
Bước 4: Mở Command Prompt trong thư mục C:\n8n

```

```cmd
```

# 1. Mở Command Prompt
# 2. Gõ:
cd C:\n8n

# 3. Chạy lệnh:
docker-compose up -d

# 4. Kiểm tra đang chạy:
docker ps
Bước 5: Truy cập n8n

Mở trình duyệt

Vào: http://localhost:5678

Đăng nhập: admin / matkhau123

2.3 Cấu trúc thư mục sau khi cài
```text
C:\n8n\
├── docker-compose.yml    # File cấu hình
├── local-files\          # Thư mục chia sẻ file
│   └── data\            # Dữ liệu của bạn
└── n8n_data\            # Dữ liệu n8n (tự động tạo)
PHẦN 3: LÀM QUEN GIAO DIỆN - THỰC HÀNH NGAY TRONG 10 PHÚT
3.1 Tour giao diện CHI TIẾT
Khi mới vào n8n, bạn thấy:

```

```text
┌─────────────────────────────────────────────────┐
│  + New Workflow    🔍 Search     ⚙️ Settings    │  ← Thanh trên
├─────────────────────────────────────────────────┤
│                                                 │
│  [Menu trái]         [Vùng làm việc chính]     │
│  • Workflows         ░░░░░░░░░░░░░░░░░░░░░░    │
│  • Executions        ░░░░░░░░░░░░░░░░░░░░░░    │
│  • Settings          ░░░░░░░░░░░░░░░░░░░░░░    │
│  • Credentials       ░░░░░░░░░░░░░░░░░░░░░░    │
│                      ░░░░░░░░░░░░░░░░░░░░░░    │
│  [Menu phải]         ░░░░░░░░░░░░░░░░░░░░░░    │
│  ┌─────────────┐     ░░░░░░░░░░░░░░░░░░░░░░    │
│  │ All Nodes   │     ░░░░░░░░░░░░░░░░░░░░░░    │
│  │ ═══════════ │     ░░░░░░░░░░░░░░░░░░░░░░    │
│  │ Trigger     │     ░░░░░░░░░░░░░░░░░░░░░░    │
│  │ Core Nodes  │     ░░░░░░░░░░░░░░░░░░░░░░    │
│  │ ...         │     ░░░░░░░░░░░░░░░░░░░░░░    │
│  └─────────────┘     └─────────────────────┘   │
└─────────────────────────────────────────────────┘
3.2 Bài thực hành ĐẦU TIÊN: "Xin chào thế giới"
Mục tiêu: Tạo workflow đầu tiên, hiểu cơ bản nhất

Bước 1: Tạo workflow mới

```

```text
1. Click "Workflows" (menu trái)
2. Click "New workflow"
3. Đặt tên: "Bài 1 - Xin chào"
   Bước 2: Thêm TRIGGER đầu tiên

```

```text
1. Ở menu phải, tìm "Manual Trigger"
2. Click vào nó, giữ chuột và KÉO vào vùng trắng
3. Thả chuột → Xuất hiện ô màu tím
   Bước 3: Thêm NODE xử lý

```

```text
1. Ở menu phải, tìm "Code" (trong Core Nodes)
2. Kéo vào bên cạnh Manual Trigger
3. DI CHUYỆN đến gần Manual Trigger
4. Sẽ xuất hiện các chấm tròn
5. Click vào chấm tròn của Manual Trigger
6. KÉO đến chấm tròn của Code node
7. Bạn có 1 mũi tên nối 2 node!
   Bước 4: Cấu hình Code node

```

```text
1. Click vào "Code" node (ô màu xanh)
2. Bên phải xuất hiện cấu hình
3. Tìm ô "JavaScript Code"
4. Xóa hết, gõ vào:
```

```javascript
   // Tạo biến tên
   var tenCuaBan = "Bạn";
   var gioiTinh = "nam"; // Đổi thành "nữ" nếu bạn là nữ

// Tạo lời chào
var loiChao = "";
if (gioiTinh == "nam") {
loiChao = "Chào anh " + tenCuaBan + "!";
} else {
loiChao = "Chào chị " + tenCuaBan + "!";
}

// Thêm thời gian
var bayGio = new Date();
var gio = bayGio.getHours();
var buoi = "";
if (gio < 12) {
buoi = "buổi sáng";
} else if (gio < 18) {
buoi = "buổi chiều";
} else {
buoi = "buổi tối";
}

// Trả kết quả
return [{
json: {
loi_chao: loiChao,
thoi_gian: buoi,
gio_hien_tai: gio + " giờ",
ngay_thang: bayGio.toLocaleDateString('vi-VN'),
message: loiChao + " Chúc bạn một " + buoi + " tốt lành!"
}
}];
Bước 5: CHẠY THỬ workflow

```

```text
1. Click nút ▶ "Execute Workflow" (ở trên cùng)
2. Chờ 1-2 giây
3. Click vào "Code" node
4. Ở tab "Output", xem kết quả!
   Kết quả bạn sẽ thấy:

```

```json
{
"loi_chao": "Chào anh Bạn!",
"thoi_gian": "buổi sáng/chiều/tối",
"gio_hien_tai": "14 giờ",
"ngay_thang": "15/01/2024",
"message": "Chào anh Bạn! Chúc bạn một buổi chiều tốt lành!"
}
Bước 6: Thêm node Debug để xem kết quả đẹp hơn

```

```text
1. Tìm "Debug" node (trong Core Nodes)
2. Kéo vào sau Code node
3. Kết nối Code → Debug
4. Chạy lại workflow
5. Xem kết quả ở Debug node
   PHẦN 4: HIỂU CÁC NODE QUAN TRỌNG - VỚI VÍ DỤ CỤ THỂ
   4.1 Các TRIGGER Nodes - Ai bật đèn?
1. Manual Trigger (Kích hoạt thủ công)

```

```text
- Dùng khi: Bạn muốn tự chạy bằng tay
- Ví dụ:
    + Test workflow
    + Chạy báo cáo khi cần
    + Xuất dữ liệu theo yêu cầu
2. Schedule Trigger (Lịch trình)

```

```text
- Dùng khi: Chạy tự động theo thời gian
- Ví dụ:
    + 8h sáng mỗi ngày: Gửi báo cáo
    + Mỗi 1 giờ: Kiểm tra email
    + 23h đêm: Backup dữ liệu
      Cấu hình Schedule Trigger:

```

```javascript
// Cách 1: Chọn từ dropdown
"Every 10 minutes"   // Mỗi 10 phút
"Every day at 8 AM"  // 8h sáng hàng ngày
"Every Monday at 9 AM" // 9h sáng thứ 2

// Cách 2: Dùng Cron (nâng cao)
"0 8 * * *"    // 8h sáng mỗi ngày
"0 9 * * 1"    // 9h sáng thứ 2
"*/15 * * * *" // Mỗi 15 phút
3. Webhook Trigger (Nhận dữ liệu từ bên ngoài)

```

```text
- Dùng khi: Có ứng dụng khác gửi dữ liệu cho bạn
- Ví dụ:
    + Website gửi thông tin đơn hàng
    + Form đăng ký gửi thông tin
    + App mobile gửi dữ liệu
      Cấu hình Webhook:

```

```text
Method: POST
Path: /nhan-don-hang  // Đường dẫn riêng
Response Mode: On Received  // Trả lời ngay

URL sẽ là: http://localhost:5678/webhook/nhan-don-hang
4. Email Trigger (IMAP) - Nhận email tự động

```

```text
- Dùng khi: Cần xử lý email tự động
- Ví dụ:
    + Khi khách gửi email đặt hàng
    + Khi có CV ứng tuyển
    + Khi nhận báo cáo tự động
      4.2 Các ACTION Nodes - Làm gì sau khi bật đèn?
1. HTTP Request Node - Gọi API/Website

```

```text
- Giống như trình duyệt thu nhỏ
- Có thể: Lấy dữ liệu, gửi dữ liệu
  Ví dụ: Lấy thời tiết Hà Nội

```

```yaml
URL: https://api.openweathermap.org/data/2.5/weather
Method: GET
Query Parameters:
q: Hanoi,VN
appid: YOUR_API_KEY  # Đăng ký free tại openweathermap.org
units: metric
lang: vi
Ví dụ: Gửi SMS (giả lập)

```

```yaml
URL: https://api.sms-gateway.com/send
Method: POST
Headers:
Content-Type: application/json
Authorization: Bearer YOUR_TOKEN
Body (JSON):
{
"to": "{{ $json.phone }}",
"message": "{{ $json.content }}",
"sender": "SHOP"
}
2. Code Node - Viết mã tùy chỉnh

```

```text
- Sức mạnh vô hạn
- Dùng JavaScript cơ bản
  Ví dụ: Tính toán đơn giản

```

```javascript
// Dữ liệu từ node trước
const donHang = $input.first().json;

// Tính toán
const tongTien = donHang.gia * donHang.soLuong;
const phiShip = tongTien > 500000 ? 0 : 30000;
const tongCong = tongTien + phiShip;

// Phân loại khách hàng
var loaiKhachHang = "Thường";
if (tongCong > 1000000) loaiKhachHang = "VIP";
if (tongCong > 5000000) loaiKhachHang = "VVIP";

// Tạo thông báo
const thongBao = `Đơn hàng ${donHang.maDon}
Tổng tiền: ${tongCong.toLocaleString('vi-VN')}đ
Khách hàng: ${loaiKhachHang}
Cảm ơn quý khách!`;

// Trả kết quả
return [{
json: {
...donHang,  // Giữ nguyên dữ liệu cũ
tong_tien: tongTien,
phi_ship: phiShip,
tong_cong: tongCong,
loai_khach_hang: loaiKhachHang,
thong_bao: thongBao
}
}];
3. IF Node - Nếu... thì...

```

```text
- Phân nhánh công việc
- Giống như ngã ba đường
  Ví dụ: Phân loại đơn hàng

```

```text
Điều kiện:
1. Nếu {{ $json.tong_tien > 1000000 }} → Gửi quà tặng
2. Nếu {{ $json.khach_hang == "VIP" }} → Miễn phí ship
3. Mặc định → Xử lý thường
4. Switch Node - Nhiều lựa chọn

```

```text
- Giống như bảng chỉ đường
- Có thể có nhiều hướng đi
  Ví dụ: Phân vùng giao hàng

```

```text
Mode: Expression
Rules:
- Rule 1: {{ $json.tinh contains "Hà Nội" }} → Khu vực 1
- Rule 2: {{ $json.tinh contains "Hồ Chí Minh" }} → Khu vực 2
- Rule 3: {{ ["Hải Phòng", "Đà Nẵng"].includes($json.tinh) }} → Khu vực 3
  Default: Khu vực 4
5. Wait Node - Chờ đợi

```

```text
- Tạm dừng workflow
- Ví dụ: Chờ 5 phút rồi gửi nhắc nhở
  Cấu hình Wait:

```

```yaml
// Chờ 1 tiếng
Mode: For Time Interval
Value: 1
Unit: hours

// Chờ đến 9h sáng mai
Mode: Until Specified Time
Value: {{ $now.plus(1, 'day').startOf('day').plus(9, 'hours') }}
PHẦN 5: 10+ VÍ DỤ THỰC TẾ TỪ DỄ ĐẾN KHÓ
Ví dụ 1: Tự động chúc mừng sinh nhật qua Email
Workflow đơn giản nhất:

```

```text
1. Schedule Trigger (8h sáng hàng ngày)
   ↓
2. Google Sheets (Đọc danh sách sinh nhật)
   ↓
3. Code (Kiểm tra ai sinh nhật hôm nay)
   ↓
4. IF (Có ai sinh nhật không?)
   ├── CÓ → Gmail (Gửi email chúc mừng)
   └── KHÔNG → Dừng
   Chi tiết từng bước:

Bước 1: Tạo Google Sheets

```

```text
Tạo file Google Sheets: "Danh sách sinh nhật"
Cột A: Tên
Cột B: Email
Cột C: Ngày sinh (dd/mm/yyyy)
Cột D: Đã gửi (TRUE/FALSE)
Bước 2: Cấu hình Google Sheets node

```

```yaml
Operation: Read
Spreadsheet ID: (ID từ URL Google Sheets)
Sheet Name: "Sheet1"
Range: "A2:D100"  // Đọc từ dòng 2 đến 100
Header Row: 1  // Dòng 1 là tiêu đề
Bước 3: Code node kiểm tra sinh nhật

```

```javascript
// Lấy danh sách từ Google Sheets
const danhSach = $input.all();
const homNay = new Date();
const ngayHomNay = homNay.getDate();
const thangHomNay = homNay.getMonth() + 1;

let danhSachSinhNhat = [];

// Duyệt qua từng người
for (let i = 0; i < danhSach.length; i++) {
const nguoi = danhSach[i].json;

    // Kiểm tra định dạng ngày sinh
    if (nguoi.ngay_sinh) {
        const parts = nguoi.ngay_sinh.split('/');
        if (parts.length === 3) {
            const ngaySinh = parseInt(parts[0]);
            const thangSinh = parseInt(parts[1]);
            
            // Kiểm tra trùng ngày tháng
            if (ngaySinh === ngayHomNay && thangSinh === thangHomNay) {
                // Kiểm tra chưa gửi
                if (!nguoi.da_gui || nguoi.da_gui === false) {
                    danhSachSinhNhat.push({
                        ...nguoi,
                        tuoi: homNay.getFullYear() - parseInt(parts[2])
                    });
                }
            }
        }
    }
}

// Trả kết quả
if (danhSachSinhNhat.length > 0) {
return danhSachSinhNhat.map(item => ({
json: item
}));
} else {
// Không có ai sinh nhật
return [{
json: {
thong_bao: "Hôm nay không có ai sinh nhật",
so_luong: 0
}
}];
}
Bước 4: IF node phân nhánh

```

```text
Điều kiện: {{ $json.so_luong > 0 }}
- TRUE: Có sinh nhật → Gửi email
- FALSE: Không có → Dừng
  Bước 5: Gmail node gửi email

```

```yaml
To: {{ $json.email }}
Subject: 🎉 Chúc mừng sinh nhật {{ $json.ten }}!
Body (HTML):
<h1>Chúc mừng sinh nhật!</h1>
<p>Kính gửi {{ $json.ten }},</p>
<p>Chúc mừng sinh nhật lần thứ {{ $json.tuoi }} của bạn! 🎂</p>
<p>Chúc bạn một ngày sinh nhật thật vui vẻ, hạnh phúc và thành công!</p>
<p>Trân trọng,<br>Đội ngũ của chúng tôi</p>
Ví dụ 2: Tự động lưu file đính kèm từ Gmail vào Google Drive
Workflow:

```

```text
1. Email Trigger (Kiểm tra email mới mỗi 5 phút)
   ↓
2. IF (Email có file đính kèm không?)
   ↓ (CÓ)
3. Google Drive (Upload file)
   ↓
4. Gmail (Đánh dấu đã đọc)
   ↓
5. Telegram (Thông báo đã lưu file)
   Cấu hình chi tiết:

Bước 1: Email Trigger

```

```yaml
Credential: Gmail (cần cấu hình OAuth2)
Mail Server: imap.gmail.com
Port: 993
Secure: true
Check every: 5
Only unread emails: true
Bước 2: IF node kiểm tra file đính kèm

```

```text
Điều kiện: {{ $json.attachments.length > 0 }}
Bước 3: Google Drive upload

```

```yaml
Operation: Upload
Folder: /n8n/email-attachments/{{ $now.format('YYYY-MM-DD') }}/
Binary Property: attachments[0]
Filename: {{ $json.subject }}_{{ $now.format('HH-mm-ss') }}
Ví dụ 3: Hệ thống nhắc nhở uống nước
Workflow vui vẻ:

```

```text
1. Schedule Trigger (Mỗi 2 tiếng từ 8h-20h)
   ↓
2. Telegram (Nhắn tin nhắc nhở)
   ↓
3. Wait (Đợi 30 phút)
   ↓
4. IF (Đã uống nước chưa?)
   ├── RỒI → Telegram (Khen ngợi)
   └── CHƯA → Telegram (Nhắc lại)
   Chi tiết:

Bước 1: Schedule Trigger

```

```text
Cron expression: 0 8-20/2 * * *
Giải thích: Chạy vào phút 0, từ 8h đến 20h, mỗi 2 tiếng
Bước 2: Telegram gửi nhắc nhở

```

```yaml
Chat ID: (ID chat của bạn)
Text: |
💧 THỜI GIAN UỐNG NƯỚC 💧

Bạn đã uống nước chưa?

Nếu RỒI, reply "R"
Nếu CHƯA, reply "C"

(Tự động kiểm tra sau 30 phút)
Bước 3: Wait node

```

```text
Mode: For Time Interval
Value: 30
Unit: minutes
Bước 4: Telegram nhận phản hồi

```

```text
(Bạn cần tạo bot Telegram riêng cho việc này)
PHẦN 6: KẾT NỐI VỚI DỊCH VỤ PHỔ BIẾN
6.1 Kết nối Google Services
Cách lấy Google Sheets ID:

```

```text
URL: https://docs.google.com/spreadsheets/d/ABC123DEF456/edit
ID là: ABC123DEF456
Cấu hình Google Sheets đọc dữ liệu:

```

```javascript
// Node: Google Sheets
Operation: "Read"
Spreadsheet ID: "ABC123DEF456"
Sheet Name: "Sheet1"
Range: "A2:Z1000"  // Đọc từ A2 đến Z1000
Header Row: 1  // Dòng 1 là tiêu đề

// Dữ liệu trả về sẽ có dạng:
[
{
"json": {
"STT": 1,
"Ten": "Nguyen Van A",
"Email": "a@gmail.com",
...
}
}
]
Cấu hình Google Sheets ghi dữ liệu:

```

```yaml
Operation: "Append"
Spreadsheet ID: "ABC123DEF456"
Sheet Name: "Sheet1"
Range: "A:Z"  // Tự động thêm vào dòng cuối
Columns: "Auto-Map"  // Tự động map theo tên cột
6.2 Kết nối Telegram
Bước 1: Tạo bot Telegram

```

```text
1. Mở Telegram, tìm @BotFather
2. Gửi /newbot
3. Đặt tên bot (ví dụ: My n8n Bot)
4. Đặt username (phải kết thúc bằng bot, ví dụ: my_n8n_bot)
5. Lưu API Token: 123456789:ABCdefGHIjklMNOpqrSTUvwxYZ
   Bước 2: Lấy Chat ID

```

```text
1. Tìm @userinfobot trên Telegram
2. Gửi /start
3. Nó sẽ trả về ID của bạn (ví dụ: 123456789)
   Bước 3: Cấu hình Telegram node

```

```yaml
Resource: Message
Operation: Send Message
Chat ID: "123456789"  // ID của bạn
Text: "Xin chào! Đây là tin nhắn tự động từ n8n"
Parse Mode: HTML  // Cho phép format đẹp
Ví dụ tin nhắn format đẹp:

```

```html
<b>🔔 THÔNG BÁO ĐƠN HÀNG MỚI</b>
──────────────
🛒 <b>Mã đơn:</b> <code>DH001</code>
👤 <b>Khách hàng:</b> Nguyễn Văn A
💰 <b>Tổng tiền:</b> 500,000đ
📍 <b>Địa chỉ:</b> Hà Nội
⏰ <b>Thời gian:</b> 14:30 15/01/2024
──────────────
✅ <i>Đơn hàng đã được tiếp nhận</i>
6.3 Kết nối Gmail
Cách 1: Dùng OAuth2 (an toàn nhất)

```

```text
1. Trong n8n, vào Credentials → New
2. Chọn "OAuth2 API"
3. Làm theo hướng dẫn (cần có Google Cloud Project)
   Cách 2: Dùng App Password (đơn giản hơn)

```

```text
1. Vào Google Account → Security
2. Bật 2-Step Verification
3. Tạo App Password
4. Dùng password này trong n8n
   Cấu hình Gmail node gửi email:

```

```yaml
From: your.email@gmail.com
To: {{ $json.email_khach_hang }}
Subject: Xác nhận đơn hàng {{ $json.ma_don }}
Body (HTML):
<h2>Xác nhận đơn hàng</h2>
<p>Kính gửi {{ $json.ten_khach_hang }},</p>
<p>Chúng tôi đã nhận được đơn hàng <strong>{{ $json.ma_don }}</strong></p>
<p>Tổng giá trị: <strong>{{ $json.tong_tien }}</strong></p>
<p>Trạng thái: Đang xử lý</p>
<p>Cảm ơn bạn đã mua hàng!</p>
PHẦN 7: XỬ LÝ DỮ LIỆU VỚI CODE NODE
7.1 Các biến đặc biệt trong n8n
$json: Dữ liệu từ node trước

```

```javascript
// Lấy giá trị
const ten = $json.ten;
const tuoi = $json.tuoi;

// Hoặc nếu có nhiều items
const items = $input.all();
const itemDauTien = items[0].json;
$input: Tất cả dữ liệu đầu vào

```

```javascript
// Lấy tất cả items
const tatCaItems = $input.all();

// Lấy item đầu tiên
const item1 = $input.first();

// Lấy item thứ n
const itemN = $input.item[0];  // Item thứ 0
$node: Truy cập node khác

```

```javascript
// Lấy dữ liệu từ node có tên "Google Sheets"
const duLieu = $node["Google Sheets"].json;

// Lấy tên node hiện tại
const tenNode = $node.name;
$now: Thời gian hiện tại

```

```javascript
const bayGio = $now;  // Object Date
const ngay = $now.toISOString();  // 2024-01-15T14:30:00.000Z
const ngayVietNam = $now.format('DD/MM/YYYY HH:mm:ss');

// Tính toán thời gian
const ngayMai = $now.plus(1, 'day');
const tuanSau = $now.plus(7, 'days');
const motGioTruoc = $now.minus(1, 'hour');
$env: Biến môi trường

```

```javascript
const apiKey = $env.API_KEY;
const dbPassword = $env.DB_PASSWORD;
7.2 Ví dụ xử lý dữ liệu thực tế
Ví dụ 1: Tính toán hóa đơn

```

```javascript
// Dữ liệu từ node trước (giỏ hàng)
const gioHang = $json.gio_hang;
const khachHang = $json.khach_hang;

// Tính tổng tiền
let tongTien = 0;
let soLuongSanPham = 0;

for (const sanPham of gioHang) {
const thanhTien = sanPham.gia * sanPham.so_luong;
tongTien += thanhTien;
soLuongSanPham += sanPham.so_luong;

    // Thêm field tính toán cho từng sản phẩm
    sanPham.thanh_tien = thanhTien;
    sanPham.thanh_tien_fmt = thanhTien.toLocaleString('vi-VN') + 'đ';
}

// Tính phí ship
let phiShip = 0;
if (tongTien > 500000) {
phiShip = 0;  // Miễn phí ship
} else if (khachHang.vip) {
phiShip = 15000;  // VIP giảm giá
} else {
phiShip = 30000;  // Thường
}

// Tính VAT (10%)
const vat = tongTien * 0.1;

// Tổng cộng
const tongCong = tongTien + phiShip + vat;

// Tạo mã đơn hàng
const maDon = 'DH' + Date.now().toString().slice(-8);

// Phân loại khách hàng
let loaiKhachHang = 'Thường';
if (tongCong > 2000000) loaiKhachHang = 'VIP';
if (tongCong > 5000000) loaiKhachHang = 'VVIP';

// Tạo kết quả
return [{
json: {
ma_don: maDon,
khach_hang: khachHang,
gio_hang: gioHang,
tong_tien: tongTien,
tong_tien_fmt: tongTien.toLocaleString('vi-VN') + 'đ',
phi_ship: phiShip,
phi_ship_fmt: phiShip.toLocaleString('vi-VN') + 'đ',
vat: vat,
vat_fmt: vat.toLocaleString('vi-VN') + 'đ',
tong_cong: tongCong,
tong_cong_fmt: tongCong.toLocaleString('vi-VN') + 'đ',
so_luong_san_pham: soLuongSanPham,
loai_khach_hang: loaiKhachHang,
thoi_gian: $now.format('DD/MM/YYYY HH:mm:ss'),

        // Thông báo đẹp cho email/telegram
        thong_bao: `
🎉 ĐƠN HÀNG THÀNH CÔNG 🎉
Mã đơn: ${maDon}
Khách hàng: ${khachHang.ten}
Số lượng: ${soLuongSanPham} sản phẩm
Tổng tiền: ${tongCong.toLocaleString('vi-VN')}đ
Loại khách: ${loaiKhachHang}
`.trim()
}
}];
Ví dụ 2: Xử lý và làm sạch dữ liệu

```

```javascript
// Dữ liệu thô từ form/API
const duLieuTho = $json;

// 1. Làm sạch và chuẩn hóa
const duLieuSach = {
// Tên: viết hoa chữ cái đầu
ten: duLieuTho.ten
? duLieuTho.ten.trim()
.toLowerCase()
.replace(/\b\w/g, c => c.toUpperCase())
: '',

    // Email: lowercase và trim
    email: duLieuTho.email 
        ? duLieuTho.email.trim().toLowerCase()
        : '',
    
    // Số điện thoại: chỉ giữ số
    so_dien_thoai: duLieuTho.so_dien_thoai 
        ? duLieuTho.so_dien_thoai.replace(/\D/g, '')
        : '',
    
    // Địa chỉ: chuẩn hóa
    dia_chi: duLieuTho.dia_chi 
        ? duLieuTho.dia_chi.trim()
            .replace(/\s+/g, ' ')  // Xóa khoảng trắng thừa
            .replace(/, /g, ', ')   // Đảm bảo format
        : ''
};

// 2. Validate dữ liệu
const loi = [];

// Validate email
if (!duLieuSach.email) {
loi.push('Email không được để trống');
} else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(duLieuSach.email)) {
loi.push('Email không hợp lệ');
}

// Validate phone
if (!duLieuSach.so_dien_thoai) {
loi.push('Số điện thoại không được để trống');
} else if (duLieuSach.so_dien_thoai.length < 10) {
loi.push('Số điện thoại phải có ít nhất 10 số');
}

// 3. Format lại cho đẹp
if (duLieuSach.so_dien_thoai) {
// Format: 0912 345 678
const sdt = duLieuSach.so_dien_thoai;
duLieuSach.so_dien_thoai_formatted =
sdt.substring(0, 4) + ' ' +
sdt.substring(4, 7) + ' ' +
sdt.substring(7);
}

// 4. Tạo kết quả
return [{
json: {
...duLieuSach,
valid: loi.length === 0,
errors: loi,
error_count: loi.length,
processed_at: $now.toISOString(),

        // Summary
        summary: loi.length === 0 
            ? '✅ Dữ liệu hợp lệ'
            : `❌ Có ${loi.length} lỗi: ${loi.join(', ')}`
    }
}];
PHẦN 8: DỰ ÁN THỰC TẾ - HỆ THỐNG QUẢN LÝ ĐƠN HÀNG NHỎ
8.1 Yêu cầu hệ thống
```

```text
Shop nhỏ bán hàng online qua:
1. Facebook Messenger
2. Zalo
3. Website form

Cần hệ thống tự động:
- Nhận đơn hàng từ các nguồn
- Lưu vào Google Sheets
- Tính toán tự động
- Thông báo cho chủ shop
- Gửi xác nhận cho khách
  8.2 Kiến trúc hệ thống
```

```text
  3 NGUỒN DỮ LIỆU:
1. Facebook Webhook → Workflow 1
2. Zalo Webhook → Workflow 1
3. Website Form → Workflow 1

WORKFLOW CHÍNH (Xử lý đơn hàng):
1. Nhận dữ liệu từ webhook
2. Validate và làm sạch
3. Tính toán giá
4. Lưu vào Google Sheets
5. Gửi Telegram cho chủ shop
6. Gửi email/SMS cho khách
7. Tạo mã vận đơn (giả lập)

WORKFLOW PHỤ (Báo cáo):
1. Schedule 20h hàng ngày
2. Tổng hợp doanh thu ngày
3. Gửi báo cáo Telegram
   8.3 Triển khai chi tiết
   Workflow 1: Xử lý đơn hàng

Bước 1: Webhook Trigger

```

```yaml
Method: POST
Path: /nhan-don-hang
Response Mode: Response Node
Bước 2: Code Node - Validate

```

```javascript
// Nhận dữ liệu từ webhook
const donHang = $json;

// Kiểm tra bắt buộc
const requiredFields = ['ten_khach', 'so_dien_thoai', 'san_pham'];
const missingFields = [];

for (const field of requiredFields) {
if (!donHang[field] || donHang[field].trim() === '') {
missingFields.push(field);
}
}

if (missingFields.length > 0) {
// Trả về lỗi
return [{
json: {
success: false,
message: `Thiếu thông tin: ${missingFields.join(', ')}`,
error_code: 'MISSING_FIELDS'
}
}];
}

// Tạo mã đơn hàng
const maDon = 'DH' + Date.now().toString().slice(-6);

// Tính giá (đơn giản)
const giaSanPham = {
'ao_phong': 150000,
'ao_somi': 250000,
'quan_jean': 300000
};

const sanPham = donHang.san_pham;
const soLuong = donHang.so_luong || 1;
const gia = giaSanPham[sanPham] || 200000;
const tongTien = gia * soLuong;

// Trả về thành công
return [{
json: {
success: true,
ma_don: maDon,
thoi_gian: $now.format('DD/MM/YYYY HH:mm:ss'),
ten_khach: donHang.ten_khach.trim(),
so_dien_thoai: donHang.so_dien_thoai.replace(/\D/g, ''),
dia_chi: donHang.dia_chi || '',
san_pham: sanPham,
so_luong: soLuong,
gia: gia,
tong_tien: tongTien,
trang_thai: 'MOI_TAO',
ghi_chu: donHang.ghi_chu || ''
}
}];
Bước 3: Google Sheets - Lưu đơn hàng

```

```yaml
Operation: Append
Spreadsheet ID: "YOUR_SHEET_ID"
Sheet Name: "DonHang"
Range: "A:Z"
Columns: "Auto-Map"
Bước 4: Telegram - Thông báo chủ shop

```

```yaml
Chat ID: "YOUR_CHAT_ID"
Parse Mode: HTML
Text: |
🛒 <b>ĐƠN HÀNG MỚI</b>
──────────────
📦 <b>Mã đơn:</b> <code>{{ $json.ma_don }}</code>
👤 <b>Khách:</b> {{ $json.ten_khach }}
📞 <b>Điện thoại:</b> {{ $json.so_dien_thoai }}
📍 <b>Địa chỉ:</b> {{ $json.dia_chi }}
🏷️ <b>Sản phẩm:</b> {{ $json.san_pham }}
🔢 <b>Số lượng:</b> {{ $json.so_luong }}
💰 <b>Tổng tiền:</b> {{ $json.tong_tien.toLocaleString('vi-VN') }}đ
⏰ <b>Thời gian:</b> {{ $json.thoi_gian }}
──────────────
✅ Đã lưu vào hệ thống
Bước 5: Response Node - Trả về cho khách

```

```javascript
// Node: HTTP Request (Response)
// Đây là response cho webhook ban đầu

return [{
json: {
success: true,
message: "Đơn hàng đã được tiếp nhận!",
order_id: $json.ma_don,
total: $json.tong_tien,
estimated_time: "2-3 ngày",
contact_info: "Hotline: 0912 345 678"
}
}];
PHẦN 9: MẸO VÀ THỦ THUẬT HAY
9.1 Debug workflow hiệu quả
Cách 1: Dùng Debug Node

```

```text
- Thêm Debug node sau node cần kiểm tra
- Chạy workflow
- Xem dữ liệu truyền qua Debug node
  Cách 2: Console.log trong Code node

```

```javascript
console.log('Dữ liệu đầu vào:', JSON.stringify($json, null, 2));
console.log('Số lượng items:', $input.all().length);

// Xem log ở:
// 1. Terminal nếu chạy npx n8n
// 2. Docker logs nếu chạy Docker
Cách 3: Execute Workflow từ node cụ thể

```

```text
1. Click phải vào node
2. Chọn "Execute Workflow"
3. Workflow sẽ chạy từ node đó
   9.2 Xử lý lỗi thông minh
   Tạo workflow xử lý lỗi:

```

```text
Main Workflow
↓ (Có thể lỗi)
TRY: Node dễ lỗi
↓ (Nếu lỗi)
CATCH: Error Trigger
↓
CODE: Ghi log lỗi
↓
TELEGRAM: Báo lỗi cho admin
↓
GMAIL: Gửi email cảnh báo
Code node xử lý lỗi:

```

```javascript
try {
// Code có thể lỗi
const result = someRiskyOperation();
return [{ json: result }];
} catch (error) {
// Ghi log chi tiết
console.error('Lỗi xảy ra:', error);

    return [{
        json: {
            error: true,
            message: error.message,
            stack: error.stack,
            timestamp: new Date().toISOString()
        }
    }];
}
9.3 Tối ưu hiệu suất
1. Giới hạn dữ liệu đọc từ Google Sheets:

```

```yaml
Range: "A2:E1000"  // Chỉ đọc 1000 dòng đầu
Thay vì: "A:E"     // Đọc toàn bộ
2. Batch processing khi có nhiều items:

```

```javascript
// Thay vì xử lý từng item trong vòng lặp
// Gộp lại xử lý 1 lần

const items = $input.all();
const processedItems = items.map(item => {
// Xử lý nhanh
return {
json: {
...item.json,
processed: true
}
};
});

return processedItems;
3. Sử dụng Wait node hợp lý:

```

```yaml
// KHÔNG NÊN: Chờ 24 giờ liên tục
// NÊN: Schedule chạy mỗi ngày

Schedule Trigger: 0 8 * * *  // 8h sáng mỗi ngày
Thay vì: Manual Trigger → Wait 24h
PHẦN 10: TRIỂN KHAI THỰC TẾ & BẢO MẬT
10.1 Deploy lên server thật (VPS)
Bước 1: Mua VPS (ví dụ: $5/tháng)

```

```text
Nhà cung cấp: DigitalOcean, Vultr, Linode
Cấu hình: 1GB RAM, 1 CPU, 25GB SSD
Hệ điều hành: Ubuntu 20.04
Bước 2: SSH vào VPS

```

```bash
```

# Trên máy bạn
ssh root@IP_VPS

# Cập nhật hệ thống
apt update && apt upgrade -y
Bước 3: Cài Docker

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh

```

# Cài Docker Compose
apt install docker-compose -y
Bước 4: Tạo thư mục n8n

```bash
mkdir /opt/n8n
cd /opt/n8n
nano docker-compose.yml
Bước 5: File docker-compose.yml cho production

```

```yaml
version: '3.8'

services:
n8n:
image: n8nio/n8n
container_name: n8n
restart: unless-stopped
ports:
- "5678:5678"
environment:
- N8N_BASIC_AUTH_ACTIVE=true
- N8N_BASIC_AUTH_USER=admin
- N8N_BASIC_AUTH_PASSWORD=MatKhauManh123!
- N8N_HOST=your-domain.com
- N8N_PORT=5678
- N8N_PROTOCOL=https
- N8N_EDITOR_BASE_URL=https://your-domain.com
- WEBHOOK_URL=https://your-domain.com
- N8N_ENCRYPTION_KEY=YourEncryptionKey123
- EXECUTIONS_DATA_PRUNE=true
- EXECUTIONS_DATA_MAX_AGE=168
- EXECUTIONS_DATA_MAX_COUNT=10000
volumes:
- n8n_data:/home/node/.n8n
- ./local-files:/files

volumes:
n8n_data:
Bước 6: Chạy n8n

```

```bash
docker-compose up -d
Bước 7: Cài Nginx + SSL (HTTPS miễn phí)

```

```bash
```

# Cài Nginx
apt install nginx -y

# Cài Certbot cho SSL
apt install certbot python3-certbot-nginx -y

# Tạo config Nginx
nano /etc/nginx/sites-available/n8n
File Nginx config:

```nginx
server {
listen 80;
server_name your-domain.com;

    location / {
        proxy_pass http://localhost:5678;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
Bước 8: Cài SSL

```

```bash
```

# Kích hoạt site
ln -s /etc/nginx/sites-available/n8n /etc/nginx/sites-enabled/

# Kiểm tra cấu hình
nginx -t

# Khởi động lại Nginx
systemctl restart nginx

# Cài SSL
certbot --nginx -d your-domain.com
10.2 Backup dữ liệu
Workflow backup tự động:

```text
1. Schedule Trigger (2h sáng chủ nhật)
2. Code Node: Tạo file backup
3. Google Drive: Upload backup
4. Telegram: Thông báo backup thành công
   Script backup đơn giản:

```

```bash
```

#!/bin/bash
# backup.sh

DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="/backups/n8n_backup_$DATE.tar.gz"

# Backup data
docker exec n8n tar czf - /home/node/.n8n > $BACKUP_FILE

# Upload lên Google Drive (dùng rclone)
rclone copy $BACKUP_FILE gdrive:n8n-backups/

# Xóa backup cũ (giữ 7 ngày)
find /backups -name "n8n_backup_*" -mtime +7 -delete
TỔNG KẾT LỘ TRÌNH HỌC 4 TUẦN
Tuần 1: Làm quen cơ bản
```text
Ngày 1-2: Hiểu n8n là gì, cài đặt local
Ngày 3-4: Tạo workflow đầu tiên "Hello World"
Ngày 5-7: Học về Trigger nodes (Manual, Schedule)
Bài tập: Tạo workflow chào buổi sáng/trưa/tối
Tuần 2: Các node cơ bản
```

```text
Ngày 1-2: HTTP Request, Code node cơ bản
Ngày 3-4: IF node, Switch node
Ngày 5-6: Debug, Wait nodes
Ngày 7: Tổng hợp - Tạo workflow tính tiền đơn hàng
Tuần 3: Kết nối dịch vụ
```

```text
Ngày 1-2: Google Sheets (đọc/ghi)
Ngày 3-4: Telegram bot
Ngày 5-6: Gmail (gửi email)
Ngày 7: Tổng hợp - Hệ thống nhắc nhở
Tuần 4: Dự án thực tế
```

```text
Ngày 1-2: Hệ thống quản lý đơn hàng đơn giản
Ngày 3-4: Hệ thống báo cáo tự động
Ngày 5-6: Xử lý lỗi và tối ưu
Ngày 7: Deploy lên server thật
5 dự án nhỏ nên làm khi học:
Bot nhắc nhở cá nhân (Telegram + Schedule)

Tự động lưu file email (Gmail + Google Drive)

Theo dõi giá sản phẩm (HTTP Request + Telegram)

Hệ thống đăng ký lớp học (Webhook + Google Sheets)

Báo cáo thời tiết hàng ngày (API + Telegram)
```

Tài nguyên học thêm:
Documentation chính thức: https://docs.n8n.io

Video tutorials: https://www.youtube.com/c/n8nio

Community forum: https://community.n8n.io

Workflow templates: https://n8n.io/workflows

GitHub examples: https://github.com/n8n-io/n8n/tree/master/packages/cli/examples
