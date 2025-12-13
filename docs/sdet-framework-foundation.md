# 🏗️ Xây dựng Nền móng Framework Chuẩn SDET

## 📝 Tại sao không nên "đi tắt" ngay từ đầu?

> Bài học xương máu từ kinh nghiệm thực tế - Framework chuẩn là "chân móng vững chắc cho thành quách"

---

## 1. 💡 Lời mở đầu: May mắn hay Bài học đau thương?

### Kinh nghiệm cá nhân của tôi:

Tôi bắt đầu sự nghiệp với **may mắn** khi làm việc với các Framework có sẵn, được thiết kế chuẩn chỉnh theo quy tắc SDET (Software Development Engineer in Test):

- **Playwright** làm core testing framework
- **POM (Page Object Model)** + **Client Model** cho API
- **Allure Reporting** tích hợp sẵn
- **CI/CD Pipeline** với GitHub Actions

### Lợi ích ngay lập tức:

✅ **Nâng cao hiểu biết về kiến trúc chuẩn** - Thấy được "skeleton" của một Framework chuyên nghiệp

✅ **Dễ dàng bảo trì (Maintenance)** - Khi UI thay đổi, chỉ sửa 1 chỗ thay vì cả tá

✅ **Dễ dàng gỡ lỗi (Debug)** - Log chi tiết, screenshot on fail, trace đầy đủ

✅ **Mở rộng/Triển khai tính năng nâng cao** - Tích hợp Visual Testing, Data-driven testing, Notification tự động chỉ trong vài ngày

### 🎯 Điểm mấu chốt:

**Việc xây dựng một Framework chuẩn chỉnh ngay từ ban đầu là một "chân móng vững chắc cho thành quách" sau này.**

Không phải "phức tạp hóa", mà là **đầu tư sớm để gặt hái lâu dài**.

---

## 2. 🤯 Nỗi niềm của Junior: "Tại sao phải phức tạp hóa?"

### Thực tế phũ phàng:

Hầu hết Junior khi bắt đầu đều có chung một suy nghĩ: **"Viết code đơn giản trước, tối ưu sau"**

Tôi cũng từng như vậy. Dưới đây là những nghi vấn phổ biến nhất:

### ❓ Vấn đề Wait/Sync: "Tại sao phải wait for load state hay wait for selector khi mình chỉ cần click()?"

**🎯 Đáp án (Ngắn gọn):** Chống Flaky Test

```typescript
// ❌ SAI: Click ngay lập tức
await page.click('#submit-btn');

// ✅ ĐÚNG: Đảm bảo element sẵn sàng
await page.waitForSelector('#submit-btn', { state: 'visible' });
await page.click('#submit-btn');
```

**Tại sao?** Framework của bạn sẽ ổn định khi chạy song song (parallel execution) trên CI/CD.

### ❓ Vấn đề Logging: "Log làm gì cho tốn thời gian và đầy data?"

**🎯 Đáp án (Ngắn gọn):** Debug/Traceability

```typescript
// ❌ SAI: Không log gì cả
it('Login test', async () => {
  await page.fill('#username', 'user');
  await page.click('#login-btn');
});

// ✅ ĐÚNG: Log đầy đủ
it('Login test', async () => {
  console.log('Starting login test...');
  await page.fill('#username', 'user');
  console.log('Username filled');

  await page.click('#login-btn');
  console.log('Login button clicked');

  await page.waitForURL('**/dashboard');
  console.log('Login successful');
});
```

**Tại sao?** Không có Log, gần như **không thể tìm ra nguyên nhân lỗi** khi test chạy trên CI/CD server.

### ❓ Vấn đề Page Object Model (POM): "Viết logic trực tiếp vào step test có phải nhanh và dễ hơn không?"

**🎯 Đáp án (Ngắn gọn):** Maintainability & Reusability

```typescript
// ❌ SAI: Logic trực tiếp trong test
it('Login and check dashboard', async () => {
  await page.goto('/login');
  await page.fill('[data-testid="username"]', 'user');
  await page.fill('[data-testid="password"]', 'pass');
  await page.click('[data-testid="login-btn"]');

  await expect(page.locator('text=Welcome')).toBeVisible();
});

// ✅ ĐÚNG: Sử dụng POM
it('Login and check dashboard', async () => {
  const loginPage = new LoginPage(page);
  await loginPage.login('user', 'pass');

  const dashboardPage = new DashboardPage(page);
  await expect(dashboardPage.welcomeMessage).toBeVisible();
});
```

**Tại sao?** Nếu UI thay đổi (selector đổi), bạn chỉ sửa **1 file POM** thay vì hàng chục file test.

---

## 3. 💥 Bài học đau thương: "Rồi mới thấy cái cảnh"

### Đây là phần quan trọng nhất - Hậu quả của việc "đi tắt"

### Tình huống điển hình:

**Bước 1:** Junior hào hứng xây dựng Framework "đơn giản"
- Viết tất cả logic vào file test
- Không có POM, không có wait, không có log
- "Chạy được là OK, tối ưu sau"

**Bước 2:** Framework hoạt động tốt... trong local environment
- Test chạy nhanh, pass hết
- Sếp khen: "Giỏi quá, deploy lên production đi!"

**Bước 3:** Deploy lên CI/CD - "Cái địa ngục" bắt đầu

### 🔥 Hệ quả khi mở rộng (thực tế xảy ra):

#### 🚨 Thao tác UI thay đổi:
```typescript
// Locator bị trùng lặp ở 20 file test
await page.click('#old-submit-btn'); // Phải sửa 20 chỗ
// Thay vì chỉ sửa 1 file POM
```

#### 🚨 Logic bị lặp:
```typescript
// Cùng một thao tác Login viết đi viết lại
// Ở 15 file test khác nhau
await page.fill('#username', 'user');
await page.fill('#password', 'pass');
await page.click('#login-btn');
```

#### 🚨 Chạy song song (Parallel):
```typescript
// Test flaky tràn lan
// Lỗi ngẫu nhiên xuất hiện
// "Tại sao test pass local mà fail CI?"
```

#### 🚨 Refactor: Phải bỏ thời gian gấp 5-10 lần
```typescript
// Cảm giác "cả một trời đau thương"
// Phải refactor từ đầu
// Deadline cháy đầu
// Sếp hỏi: "Tại sao chậm thế?"
```

### 🎯 Kết luận đau thương:

**"Đi tắt" ban đầu = Tiết kiệm 1-2 tuần**
**"Sửa chữa" sau này = Tốn 2-3 tháng + stress + mất uy tín**

---

## 4. 🧭 Định hướng cho Tân Junior: Hãy học Framework Chuẩn

### Bạn sẽ không hối tiếc khi đầu tư thời gian ban đầu!

### 4.1. Cấu trúc Chuẩn = Tách biệt (Separation of Concerns)

Một Framework tốt = Framework **phân chia rõ ràng các lớp trách nhiệm (Layer)**:

| Layer | Chức năng cốt lõi | Mục tiêu chính | Ví dụ |
|-------|-------------------|----------------|-------|
| **Test Layer**<br>`src/tests/` | Chứa kịch bản (Test Steps) và Assertions | **Orchestration, Clarity** | `expect()`, business flow |
| **UI Abstraction**<br>`src/ui/` | Page Objects, Locators, UI Actions | **Maintainability** | POM classes, selectors |
| **API Abstraction**<br>`src/api/` | API Clients, Endpoints, Data Models | **Reusability** | HTTP methods, DTOs |
| **Utility Layer**<br>`src/utils/` | Logging, Config, Retry Mechanism | **Robustness, Debugging** | Logger, env config |

### 📁 Cấu trúc thư mục chuẩn:

```
src/
├── tests/                    # 🧪 Test Layer
│   ├── ui/                   # UI Tests
│   ├── api/                  # API Tests
│   └── e2e/                  # End-to-End Tests
│
├── ui/                       # 🎨 UI Abstraction
│   ├── pages/                # Page Objects
│   └── components/           # Reusable Components
│
├── api/                      # 🔌 API Abstraction
│   ├── clients/              # API Clients
│   ├── models/               # Data Models
│   └── helpers/              # API Utilities
│
├── utils/                    # 🛠️ Utility Layer
│   ├── config/               # Configuration
│   ├── logger/               # Logging
│   └── helpers/              # Common Helpers
│
└── types/                    # 📝 Type Definitions
    └── index.ts
```

### 4.2. Tại sao lại là Chuẩn SDET?

✅ **Nền tảng vững chắc** giúp vận dụng tool/cái gì đó mới dễ dàng hơn:
- Tích hợp Visual Testing (Applitools)
- CI/CD Pipeline (GitHub Actions, Jenkins)
- Data-driven testing (CSV, JSON)
- Cross-browser testing (BrowserStack)
- Performance testing (Lighthouse)

✅ **Giúp bạn đạt được tiêu chuẩn của một SDET**:
- Không chỉ là QA Automation chạy script
- Mà là kỹ sư có khả năng xây dựng hệ thống testing

✅ **Dễ dàng scale và maintain**:
- Team 5 người → Team 50 người vẫn ổn
- Project nhỏ → Project enterprise vẫn chạy

---

## 5. 🎯 Core Principles a Junior Must Master When Building a Framework

### Đây là những kiến thức nền tảng - tập trung vào "tại sao phải làm", không chỉ "làm thế nào"

### 1. 🏗️ Nguyên tắc Tách biệt và Lớp hóa (Layering & Separation of Concerns)

Một Framework tốt phải được chia thành các lớp trách nhiệm rõ ràng. Junior cần hiểu rằng việc này giúp giải quyết vấn đề bảo trì.

| Nguyên tắc cần hiểu | Chi tiết cần nắm |
|-------------------|----------------|
| **Page Object Model (POM)** | Tách: Logic tìm kiếm phần tử (Locator) và Thao tác (Action) ra khỏi bài Test (Assertion).<br>**Mục tiêu:** Khi UI thay đổi, chỉ cần sửa 1 file Page Object |
| **API Client/Service Model** | Tương tự POM cho API. Tách: Endpoint và Method gọi API ra khỏi bài Test.<br>**Mục tiêu:** Sử dụng API để Setup dữ liệu hoặc Verify dữ liệu mà không cần qua UI |
| **Tầng Test (Test Layer)** | Chỉ chứa: Logic nghiệp vụ (Business Flow) và Xác nhận kết quả (Assertion).<br>**Mục tiêu:** Layer này là nơi duy nhất được phép sử dụng `expect()` hoặc `assert()` |
| **Tầm quan trọng của utils** | Đặt các hàm dùng chung (Config, Logger, Helper Data) vào utils để tránh lặp code |

### 2. 🛡️ Khả năng Chống Lỗi Ngẫu nhiên (Anti-Flaky Mechanisms)

Flaky Test (Test chạy lúc đúng lúc sai) là "kẻ thù" lớn nhất của Automation. Junior cần hiểu cách chống đỡ nó từ đầu.

| Cơ chế cần hiểu | Tại sao phải dùng |
|----------------|----------------|
| **Explicit Wait (Chờ rõ ràng)** | Tuyệt đối tránh `waitForTimeout()`. Chỉ sử dụng các lệnh wait có điều kiện của Playwright (ví dụ: `waitForLoadState('networkidle')`, `page.waitForSelector()`) để đảm bảo phần tử đã sẵn sàng |
| **Locator Tối ưu** | Ưu tiên dùng các Locator ít bị thay đổi bởi Dev (ví dụ: `getByRole()`, `data-testid`, `data-qa`). Tránh các locator dễ vỡ như XPath/CSS quá dài |
| **Retry Cơ bản** | Hiểu cách sử dụng Retry (ví dụ: `this.retries(N)` trong Mocha) cho các bài Test quan trọng để giảm thiểu ảnh hưởng của các lỗi môi trường tạm thời |
| **Không share Page/Context** | Khi chạy song song (Parallel), mỗi Test cần có một phiên làm việc (Page/Browser Context) độc lập để không bị ảnh hưởng bởi nhau |

### 3. ⚙️ Thiết lập Môi trường và Cấu hình (Configuration & Environment)

Một Framework chuyên nghiệp cần chạy được trên nhiều môi trường khác nhau.

| Khía cạnh cần hiểu | Cách triển khai đơn giản |
|------------------|----------------------|
| **External Configuration** | Không để Hard-coded URL/Key trong code. Sử dụng file `.env` (dotenv) để quản lý các biến môi trường (`BASE_URL`, `API_URL`) |
| **Environment Switching** | Dễ dàng chuyển đổi giữa Dev/Staging/Production bằng cách chỉ định biến môi trường (ví dụ: `TEST_ENV=staging`) |
| **Cấu hình Logging** | Khả năng bật/tắt hoặc điều chỉnh mức độ log (debug, info, warn) qua file cấu hình |

### 4. 📰 Ghi nhận và Báo cáo (Logging & Reporting)

Test thất bại mà không có thông tin chi tiết là vô dụng.

| Công cụ cần hiểu | Tầm quan trọng |
|----------------|----------------|
| **Structured Logging (Pino/Winston)** | Ghi lại các bước quan trọng của UI Action (click, fill) và API Request/Response.<br>**Quan trọng nhất:** Ghi lại các ngoại lệ/lỗi một cách chi tiết để debug trên CI |
| **Allure/HTML Report** | Hiểu rằng Report không chỉ là PASS/FAIL, mà là nơi hiển thị Allure Step, Screenshot on Fail, và Log/Payload để người không biết code cũng hiểu chuyện gì đã xảy ra |

---

## 🎯 Kết luận: Đầu tư sớm - Gặt hái lâu dài

### Framework chuẩn = **"Chân móng vững chắc"**

- **Ban đầu:** Có vẻ "phức tạp" và "tốn thời gian"
- **Sau này:** Tiết kiệm **hàng trăm giờ** maintain và debug
- **Kết quả:** Trở thành SDET thực thụ, không chỉ là "QA chạy script"

### Lời khuyên cuối cùng cho Junior:

**"Đừng ngại đầu tư 2-3 tuần ban đầu để xây Framework chuẩn. Bạn sẽ không hối tiếc!"**

### 🚀 Next Steps:

1. **Học lý thuyết:** Nắm vững các nguyên tắc trên
2. **Thực hành:** Bắt đầu với project nhỏ, áp dụng từng layer
3. **Mở rộng:** Thêm tính năng nâng cao dần dần
4. **Share:** Chia sẻ kinh nghiệm với cộng đồng

---

*Framework chuẩn không phải là đích đến - mà là hành trình. Hãy bắt đầu ngay hôm nay!* 🎯
