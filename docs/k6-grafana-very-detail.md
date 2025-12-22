# 🚀 HỆ THỐNG HỌC K6 & GRAFANA CHI TIẾT VỚI NHIỀU VÍ DỤ MINH HỌA
## <span style="color: green">**PHẦN 1: TỔNG QUAN CHI TIẾT (2 tuần đầu)**</span>

### <span style="color:orange">***1.1 K6 - Người bạn test hiệu năng***</span>
***Ví dụ thực tế dễ hiểu:***

```text
Hãy tưởng tượng bạn mở một cửa hàng cà phê:
- K6 giống như việc bạn mời 1000 người đến cửa hàng cùng lúc
- Để xem: Nhân viên có phục vụ kịp không? Bàn ghế có đủ không?
- Mục đích: Kiểm tra cửa hàng chịu được áp lực thế nào
```
***Các tính năng chính của K6:***

```javascript
// K6 dùng JavaScript đơn giản, dễ học
// Ví dụ: Kiểm tra website có load nhanh không?
import http from 'k6/http';

export default function () {
  // Gọi website google
  let response = http.get('https://www.google.com');
  
  // Kiểm tra:
  // 1. Website có mở được không? (status 200)
  // 2. Load có dưới 2 giây không?
  console.log(`Thời gian load: ${response.timings.duration}ms`);
}
```
<span style="color: purple">**1.2 Grafana - Bảng điều khiển thông minh**</span>

***Ví dụ dashboard đơn giản:***

```text
Dashboard của Grab/Gojek bạn thấy:
- Biểu đồ số chuyến đi mỗi giờ
- Tỉ lệ đơn hàng thành công
- Thời gian trung bình mỗi chuyến

Grafana làm TƯƠNG TỰ cho kết quả test:
- Biểu đồ số request mỗi giây
- Tỉ lệ request thành công/thất bại
- Thời gian phản hồi trung bình
```
## <span style="color: green">**PHẦN 2: CÀI ĐẶT CHI TIẾT TỪNG BƯỚC**</span>
### <span style="color:orange">***2.1 Cài đặt K6 trên Windows (từng bước)***</span>
#### Bước 1: Tải K6

- Vào trang https://k6.io/docs/getting-started/installation/

- Chọn Windows → Download installer

#### Bước 2: Cài đặt

- Chạy file .msi vừa tải

- Next → Next → Finish

#### Bước 3: Kiểm tra
- Mở Command Prompt (cmd) và gõ:

```bash
k6 version
```
**Kết quả sẽ hiển thị:**

```text
k6 v0.45.0 ((devel), go1.20.3)
```
### <span style="color:orange">***2.2 Cài đặt môi trường đầy đủ với Docker***</span>
- File docker-compose.yml chi tiết:

```yaml
version: '3.8'

services:
  # InfluxDB - Database lưu kết quả test
  influxdb:
    image: influxdb:1.8
    container_name: influxdb_k6
    ports:
      - "8086:8086"  # Port để kết nối
    environment:
      - INFLUXDB_DB=k6db  # Tạo database tên "k6db"
    volumes:
      - influxdb_data:/var/lib/influxdb
    restart: unless-stopped

  # Grafana - Hiển thị dashboard
  grafana:
    image: grafana/grafana:latest
    container_name: grafana_k6
    ports:
      - "3000:3000"  # Truy cập qua http://localhost:3000
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin123  # Đặt password admin
    volumes:
      - grafana_data:/var/lib/grafana
      - ./grafana-dashboards:/etc/grafana/provisioning/dashboards
    depends_on:
      - influxdb
    restart: unless-stopped

volumes:
  influxdb_data:
  grafana_data:
```
- ***Các bước chạy:***

```bash
# 1. Lưu file docker-compose.yml
# 2. Mở terminal trong thư mục chứa file
# 3. Chạy lệnh:
docker-compose up -d

# 4. Kiểm tra các container đang chạy:
docker ps

# Kết quả mong đợi:
CONTAINER ID   IMAGE                 PORTS                    NAMES
abc123def456   grafana/grafana       0.0.0.0:3000->3000/tcp   grafana_k6
xyz789uvw012   influxdb:1.8          0.0.0.0:8086->8086/tcp   influxdb_k6
```
## <span style="color: green">**PHẦN 3: K6 CƠ BẢN - VỚI 10+ VÍ DỤ THỰC TẾ**</span>
### <span style="color:orange">***3.1 Cấu trúc file K6 cơ bản***</span>
***- Ví dụ 1: Test đơn giản nhất***

```javascript
// test_basic.js
import http from 'k6/http';
import { check, sleep } from 'k6';

// PHẦN 1: CẤU HÌNH TEST
export let options = {
  // Số lượng người dùng ảo (Virtual Users)
  vus: 1,
  // Thời gian chạy test
  duration: '30s',
};

// PHẦN 2: HÀM TEST CHÍNH
export default function () {
  // Bước 1: Gọi API/Website
  let response = http.get('https://httpbin.test.k6.io/get');
  
  // Bước 2: Kiểm tra kết quả
  let checkResult = check(response, {
    // Kiểm tra 1: Status code có phải 200 không?
    'Status là 200': function(r) {
      return r.status === 200;
    },
    // Kiểm tra 2: Response có chứa chữ "headers" không?
    'Có chứa headers': function(r) {
      return r.body.includes('headers');
    },
    // Kiểm tra 3: Thời gian phản hồi < 500ms
    'Phản hồi nhanh': function(r) {
      return r.timings.duration < 500;
    }
  });
  
  // Bước 3: In kết quả ra console
  console.log(`Status: ${response.status}, Time: ${response.timings.duration}ms`);
  
  // Bước 4: Dừng 1 giây (giả lập người dùng suy nghĩ)
  sleep(1);
}
```
- ***Chạy test:***

```bash
k6 run test_basic.js
```
### <span style="color:orange">***3.2 Các loại test với ví dụ chi tiết***</span>
***- Ví dụ 2: Smoke Test - Kiểm tra hệ thống sống***

```javascript
// smoke_test.js
import http from 'k6/http';
import { check } from 'k6';

export let options = {
  vus: 1,                // Chỉ 1 user
  duration: '1m',        // Chạy 1 phút
  thresholds: {          // Ngưỡng tối thiểu
    http_req_duration: ['p(95)<2000'], // 95% request < 2s
    http_req_failed: ['rate<0.01'],    // Tỉ lệ fail < 1%
  }
};

export default function () {
  // Test 3 endpoint quan trọng
  let responses = http.batch([
    ['GET', 'https://api.example.com/health'],
    ['GET', 'https://api.example.com/products'],
    ['GET', 'https://api.example.com/users/me'],
  ]);
  
  // Kiểm tra từng response
  check(responses[0], { 'Health check OK': (r) => r.status === 200 });
  check(responses[1], { 'Products loaded': (r) => r.status === 200 });
  check(responses[2], { 'User API works': (r) => r.status === 200 });
}
```
***- Ví dụ 3: Load Test - Mô phỏng người dùng thực***

```javascript
// load_test.js
import http from 'k6/http';
import { check, sleep } from 'k6';

export let options = {
  // Mô phỏng: Buổi sáng nhiều người truy cập
  stages: [
    // Giai đoạn 1: 7h-9h - Tăng dần user
    { duration: '2m', target: 50 },   // 0 → 50 users trong 2 phút
    { duration: '5m', target: 50 },   // Giữ 50 users trong 5 phút
    
    // Giai đoạn 2: 9h-12h - Cao điểm
    { duration: '3m', target: 200 },  // 50 → 200 users trong 3 phút
    { duration: '30m', target: 200 }, // Giữ 200 users trong 30 phút
    
    // Giai đoạn 3: Nghỉ trưa - Giảm dần
    { duration: '5m', target: 50 },   // 200 → 50 users trong 5 phút
    { duration: '10m', target: 50 },  // Giữ 50 users
    
    // Giai đoạn 4: Tan làm - Giảm về 0
    { duration: '5m', target: 0 },    // 50 → 0 users trong 5 phút
  ],
  
  // Ngưỡng cảnh báo
  thresholds: {
    http_req_duration: ['p(95)<500'],  // 95% request < 500ms
    'http_req_duration{page:home}': ['p(95)<300'],  // Trang chủ < 300ms
    'http_req_duration{page:product}': ['p(95)<800'], // Trang sản phẩm < 800ms
  }
};

export default function () {
  // User vào trang chủ
  let homePage = http.get('https://shop.example.com', {
    tags: { page: 'home' }
  });
  check(homePage, { 'Homepage OK': (r) => r.status === 200 });
  sleep(Math.random() * 2 + 1); // Nghỉ 1-3 giây ngẫu nhiên
  
  // User xem sản phẩm
  let productPage = http.get('https://shop.example.com/products/123', {
    tags: { page: 'product' }
  });
  check(productPage, { 'Product page OK': (r) => r.status === 200 });
  sleep(Math.random() * 3 + 2); // Nghỉ 2-5 giây
  
  // User thêm vào giỏ hàng
  let addToCart = http.post(
    'https://shop.example.com/cart/add',
    JSON.stringify({ productId: 123, quantity: 1 }),
    { headers: { 'Content-Type': 'application/json' } }
  );
  check(addToCart, { 'Add to cart OK': (r) => r.status === 200 });
}
```
***- Ví dụ 4: Stress Test - Đẩy hệ thống đến giới hạn***

```javascript
// stress_test.js
import http from 'k6/http';
import { check } from 'k6';

export let options = {
  // Tìm điểm gãy (breaking point) của hệ thống
  stages: [
    // Tăng từ từ
    { duration: '2m', target: 100 },
    { duration: '5m', target: 100 },
    
    // Tăng mạnh
    { duration: '2m', target: 300 },
    { duration: '5m', target: 300 },
    
    // Đẩy lên cực điểm
    { duration: '2m', target: 500 },
    { duration: '5m', target: 500 },
    
    // Tăng cực đại
    { duration: '2m', target: 1000 },
    { duration: '5m', target: 1000 },
    
    // Giảm dần
    { duration: '10m', target: 0 },
  ],
  
  // Theo dõi khi nào hệ thống bắt đầu fail
  thresholds: {
    http_req_failed: ['rate<0.5'], // Cho phép fail tới 50%
    http_req_duration: ['p(95)<3000'], // Có thể chậm tới 3s
  }
};

export default function () {
  // Test endpoint chịu tải nặng nhất
  let response = http.get('https://api.example.com/checkout', {
    timeout: '10s' // Timeout dài hơn bình thường
  });
  
  // Chỉ kiểm tra cơ bản, chấp nhận có thể fail
  check(response, {
    'Có response (dù status nào)': (r) => r.status !== 0,
  });
  
  // Ghi log khi có lỗi
  if (response.status >= 400) {
    console.log(`Lỗi ${response.status}: ${response.body}`);
  }
}
```
### <span style="color:orange">***3.3 Test API với các phương thức HTTP***</span>
***- Ví dụ 5: Test REST API đầy đủ CRUD***

```javascript
// api_crud_test.js
import http from 'k6/http';
import { check, group } from 'k6';

export let options = {
  vus: 10,
  duration: '2m',
};

// Biến toàn cục để lưu dữ liệu giữa các request
let authToken = '';
let createdUserId = '';

export default function () {
  group('1. Đăng nhập để lấy token', function () {
    let loginPayload = JSON.stringify({
      email: 'test@example.com',
      password: 'password123'
    });
    
    let loginHeaders = {
      'Content-Type': 'application/json',
    };
    
    let loginRes = http.post(
      'https://api.example.com/auth/login',
      loginPayload,
      { headers: loginHeaders }
    );
    
    check(loginRes, {
      'Đăng nhập thành công': (r) => r.status === 200,
      'Có token trả về': (r) => r.json('token') !== undefined,
    });
    
    if (loginRes.status === 200) {
      authToken = loginRes.json('token');
    }
  });
  
  group('2. Lấy danh sách users', function () {
    let headers = {
      'Authorization': `Bearer ${authToken}`,
    };
    
    let listRes = http.get(
      'https://api.example.com/users',
      { headers: headers }
    );
    
    check(listRes, {
      'Lấy danh sách OK': (r) => r.status === 200,
      'Có dữ liệu trả về': (r) => r.json().length > 0,
    });
  });
  
  group('3. Tạo user mới', function () {
    let newUser = {
      name: `User_${Date.now()}`,
      email: `user_${Date.now()}@test.com`,
      age: Math.floor(Math.random() * 50) + 18
    };
    
    let headers = {
      'Authorization': `Bearer ${authToken}`,
      'Content-Type': 'application/json',
    };
    
    let createRes = http.post(
      'https://api.example.com/users',
      JSON.stringify(newUser),
      { headers: headers }
    );
    
    check(createRes, {
      'Tạo user thành công': (r) => r.status === 201,
      'Có ID trả về': (r) => r.json('id') !== undefined,
    });
    
    if (createRes.status === 201) {
      createdUserId = createRes.json('id');
    }
  });
  
  group('4. Cập nhật user', function () {
    if (!createdUserId) return;
    
    let updateData = {
      name: `Updated_${Date.now()}`,
      age: 30
    };
    
    let headers = {
      'Authorization': `Bearer ${authToken}`,
      'Content-Type': 'application/json',
    };
    
    let updateRes = http.put(
      `https://api.example.com/users/${createdUserId}`,
      JSON.stringify(updateData),
      { headers: headers }
    );
    
    check(updateRes, {
      'Cập nhật thành công': (r) => r.status === 200,
      'Tên đã được update': (r) => r.json('name').includes('Updated'),
    });
  });
  
  group('5. Xóa user', function () {
    if (!createdUserId) return;
    
    let headers = {
      'Authorization': `Bearer ${authToken}`,
    };
    
    let deleteRes = http.del(
      `https://api.example.com/users/${createdUserId}`,
      null,
      { headers: headers }
    );
    
    check(deleteRes, {
      'Xóa thành công': (r) => r.status === 204,
    });
  });
}
```
***- Ví dụ 6: Test GraphQL API***

```javascript
// graphql_test.js
import http from 'k6/http';
import { check } from 'k6';

export let options = {
  vus: 5,
  duration: '1m',
};

export default function () {
  // GraphQL Query
  let query = `
    query GetProducts($category: String!, $limit: Int!) {
      products(category: $category, limit: $limit) {
        id
        name
        price
        stock
        reviews {
          rating
          comment
        }
      }
    }
  `;
  
  // GraphQL Variables
  let variables = {
    category: 'electronics',
    limit: 10
  };
  
  // GraphQL Request Body
  let payload = JSON.stringify({
    query: query,
    variables: variables
  });
  
  let headers = {
    'Content-Type': 'application/json',
  };
  
  let response = http.post(
    'https://api.example.com/graphql',
    payload,
    { headers: headers }
  );
  
  check(response, {
    'GraphQL response OK': (r) => r.status === 200,
    'Không có lỗi GraphQL': (r) => !r.json('errors'),
    'Có dữ liệu products': (r) => r.json('data.products.length') > 0,
    'Product có đủ thông tin': (r) => {
      let products = r.json('data.products');
      return products && products[0] && products[0].name;
    },
  });
  
  // In số lượng sản phẩm nhận được
  if (response.status === 200) {
    let productCount = response.json('data.products.length');
    console.log(`Nhận được ${productCount} sản phẩm`);
  }
}
```
## <span style="color: green">**PHẦN 4: GRAFANA CHI TIẾT - DASHBOARD THỰC TẾ**</span>
### <span style="color:orange">***4.1 Thiết lập Grafana từ A-Z***</span>
#### Bước 1: Truy cập Grafana

```text
URL: http://localhost:3000
Username: admin
Password: admin123 (theo docker-compose ở trên)
```
#### Bước 2: Thêm Data Source InfluxDB

- Click ⚙️ (Configuration) → Data Sources

- Click "Add data source"

- Chọn "InfluxDB"

- Điền thông tin:

```text
HTTP:
  URL: http://influxdb:8086
  
InfluxDB Details:
  Database: k6db
  User: (bỏ trống)
  Password: (bỏ trống)
Click "Save & Test" → Hiện thông báo "Data source is working"
```

#### Bước 3: Import Dashboard mẫu của K6

- Click ＋ (Create) → Import

- Nhập ID: 2587 (Dashboard chính thức của K6)

- Chọn Data Source: InfluxDB vừa tạo

- Click "Import"

### <span style="color:orange">***4.2 Chạy K6 và xem kết quả trên Grafana***</span>
**- Script test để xuất ra InfluxDB:**

```javascript
// test_with_influx.js
import http from 'k6/http';
import { check } from 'k6';

export let options = {
  vus: 10,
  duration: '30s',
};

export default function () {
  let response = http.get('https://httpbin.test.k6.io/get');
  
  check(response, {
    'status is 200': (r) => r.status === 200,
  });
}
```
**- Chạy với InfluxDB:**

```bash
k6 run --out influxdb=http://localhost:8086/k6db test_with_influx.js
```
### <span style="color:orange">***4.3 Tạo Dashboard tùy chỉnh***</span>
- Tạo Panel 1: Biểu đồ Response Time

- Trong Grafana, tạo Dashboard mới

- Click "Add panel" → "Add new panel"

- Cấu hình query:

```sql
SELECT mean("http_req_duration") 
FROM "k6" 
WHERE $timeFilter 
GROUP BY time($__interval) 
```
- Cấu hình visualization:

- Panel title: "Response Time (ms)"

- Visualization type: "Time series"

- Field: Unit → "ms"

- Display: Gradient mode → "Opacity"

- Tạo Panel 2: Số lượng request mỗi giây

*****- Query:*****

```sql
SELECT count("http_reqs") 
FROM "k6" 
WHERE $timeFilter 
GROUP BY time($__interval) 
```
- Tạo Panel 3: Tỉ lệ request thành công

*****- Query:*****

```sql
SELECT 100 * (1 - sum("http_req_failed") / count("http_reqs")) as "Success Rate"
FROM "k6"
WHERE $timeFilter
GROUP BY time($__interval)
```
- **Cấu hình:**

    - Visualization: "Stat"

    - Unit: "percent"

- **Thresholds:**

    - 95-100: Green

    - 80-95: Yellow

    - 0-80: Red

- **Tạo Panel 4:** Phân bố Response Time (Heatmap)

  *****- Query:*****

```sql
SELECT histogram("http_req_duration") 
FROM "k6" 
WHERE $timeFilter 
GROUP BY time($__interval)
```
***- Cấu hình:***

- Visualization: "Heatmap"

- Color scheme: "Green-Yellow-Red"

### <span style="color:orange">***4.4 Dashboard hoàn chỉnh cho e-commerce***</span>
***- File JSON export dashboard mẫu:***

```json
{
  "dashboard": {
    "title": "E-commerce Performance Dashboard",
    "panels": [
      {
        "title": "Tổng quan hệ thống",
        "type": "stat",
        "targets": [{
          "query": "SELECT last(\"vus\") FROM \"k6\" WHERE $timeFilter"
        }],
        "fieldConfig": {
          "defaults": {
            "unit": "none",
            "thresholds": {
              "steps": [
                {"color": "green", "value": 0},
                {"color": "yellow", "value": 100},
                {"color": "red", "value": 200}
              ]
            }
          }
        }
      }
    ]
  }
}
```
## <span style="color: green">**PHẦN 5: KỸ THUẬT NÂNG CAO VỚI VÍ DỤ ĐẦY ĐỦ**</span>
### <span style="color:orange">***5.1 Custom Metrics - Theo dõi nghiệp vụ***</span>
***- Ví dụ 7: Theo dõi business metrics***

```javascript
// business_metrics.js
import http from 'k6/http';
import { Trend, Rate, Counter, Gauge } from 'k6/metrics';

// 1. Trend: Theo dõi xu hướng (thời gian)
let addToCartTime = new Trend('add_to_cart_time');
let checkoutTime = new Trend('checkout_time');
let searchTime = new Trend('search_time');

// 2. Rate: Tỉ lệ (%) 
let cartAbandonmentRate = new Rate('cart_abandonment');
let checkoutSuccessRate = new Rate('checkout_success');

// 3. Counter: Đếm số lượng
let productsViewed = new Counter('products_viewed');
let cartsCreated = new Counter('carts_created');
let ordersPlaced = new Counter('orders_placed');

// 4. Gauge: Giá trị tại thời điểm
let activeUsers = new Gauge('active_users');
let itemsInCart = new Gauge('items_in_cart');

export let options = {
  vus: 20,
  duration: '5m',
};

export default function () {
  // User vào website
  activeUsers.add(1);
  
  // Xem sản phẩm
  let startTime = Date.now();
  let productRes = http.get('https://shop.example.com/products/iphone');
  productsViewed.add(1);
  searchTime.add(Date.now() - startTime);
  
  // Thêm vào giỏ hàng
  startTime = Date.now();
  let addRes = http.post('https://shop.example.com/cart/add', {
    productId: '123',
    quantity: 1
  });
  addToCartTime.add(Date.now() - startTime);
  
  if (addRes.status === 200) {
    cartsCreated.add(1);
    itemsInCart.add(1);
  }
  
  // 70% user bỏ giỏ hàng, 30% thanh toán
  if (Math.random() < 0.3) {
    // Thanh toán
    startTime = Date.now();
    let checkoutRes = http.post('https://shop.example.com/checkout', {
      cartId: 'cart_123'
    });
    checkoutTime.add(Date.now() - startTime);
    
    if (checkoutRes.status === 200) {
      checkoutSuccessRate.add(1);
      ordersPlaced.add(1);
      itemsInCart.add(0); // Xóa giỏ hàng
    } else {
      checkoutSuccessRate.add(0);
    }
  } else {
    // Bỏ giỏ hàng
    cartAbandonmentRate.add(1);
  }
  
  activeUsers.add(-1);
}
```
### <span style="color:orange">***5.2 Data-driven Testing với CSV/JSON***</span>
***- Ví dụ 8: Test với dữ liệu từ file CSV***
```text
File users.csv:
```
```csv
id,username,password,email,age
1,user1,pass123,user1@test.com,25
2,user2,pass456,user2@test.com,30
3,user3,pass789,user3@test.com,35
4,user4,pass012,user4@test.com,28
5,user5,pass345,user5@test.com,40
```
***- Script K6:***

```javascript
// csv_data_test.js
import http from 'k6/http';
import { check } from 'k6';
import { SharedArray } from 'k6/data';
import { parse } from 'https://jslib.k6.io/csv-parse/0.1.0/index.js';

// Đọc file CSV - SharedArray chia sẻ cho tất cả VUs
const users = new SharedArray('users', function() {
  // Đọc file CSV
  const csvData = open('./users.csv');
  
  // Parse CSV thành array of objects
  return parse(csvData, {
    delimiter: ',',
    header: true, // Dòng đầu là header
    skipEmptyLines: true
  });
});

// Đọc file JSON config
const config = JSON.parse(open('./config.json'));

export let options = {
  vus: users.length, // Số VU = số user trong CSV
  duration: '2m',
};

export default function () {
  // Lấy user theo index của VU
  const user = users[__VU - 1];
  
  console.log(`Testing với user: ${user.username}`);
  
  // Test login
  let loginRes = http.post(config.baseUrl + '/login', {
    username: user.username,
    password: user.password
  });
  
  check(loginRes, {
    'Login thành công': (r) => r.status === 200,
    'Có token trả về': (r) => r.json('token') !== undefined,
  });
  
  // Test profile với token
  if (loginRes.status === 200) {
    let token = loginRes.json('token');
    let headers = {
      'Authorization': `Bearer ${token}`
    };
    
    let profileRes = http.get(
      `${config.baseUrl}/users/${user.id}/profile`,
      { headers: headers }
    );
    
    check(profileRes, {
      'Profile đúng user': (r) => r.json('email') === user.email,
      'Age khớp': (r) => r.json('age') == user.age,
    });
  }
}
```
***- File config.json:***

```json
{
  "baseUrl": "https://api.example.com",
  "timeout": "30s",
  "maxRetries": 3,
  "environments": {
    "staging": "https://staging-api.example.com",
    "production": "https://api.example.com"
  }
}
```
### <span style="color:orange">***5.3 Tags và Thresholds nâng cao***</span>
***- Ví dụ 9: Phân loại và đặt ngưỡng chi tiết***

```javascript
// advanced_tags_thresholds.js
import http from 'k6/http';
import { check, group } from 'k6';

export let options = {
  stages: [
    { duration: '2m', target: 50 },
    { duration: '5m', target: 50 },
    { duration: '2m', target: 0 },
  ],
  
  // THRESHOLDS CHI TIẾT
  thresholds: {
    // Tổng quan
    'http_req_duration': ['p(95)<500'],
    'http_req_failed': ['rate<0.01'],
    
    // Theo tag
    'http_req_duration{api_type:auth}': ['p(95)<300'],
    'http_req_duration{api_type:product}': ['p(95)<800'],
    'http_req_duration{api_type:checkout}': ['p(95)<1000'],
    
    // Theo scenario
    'http_req_duration{scenario:login}': ['p(95)<400'],
    'http_req_duration{scenario:browse}': ['p(95)<600'],
    
    // Business thresholds
    'checkout_success_rate': ['rate>0.95'],
    'add_to_cart_time': ['p(95)<300'],
    
    // Custom metrics
    'product_views_per_user': ['count>5'],
  },
  
  // Tags toàn cục
  tags: {
    env: 'staging',
    test_type: 'load_test',
    team: 'performance_team'
  }
};

export default function () {
  // Group 1: Authentication
  group('Authentication Flow', function () {
    let loginRes = http.post('https://api.example.com/login', {
      username: 'test',
      password: 'test'
    }, {
      tags: { 
        api_type: 'auth',
        scenario: 'login',
        endpoint: '/login'
      }
    });
    
    check(loginRes, { 'Login OK': (r) => r.status === 200 });
  });
  
  // Group 2: Browse products
  group('Product Browsing', function () {
    let categories = ['electronics', 'clothing', 'books', 'home'];
    let category = categories[Math.floor(Math.random() * categories.length)];
    
    let productsRes = http.get(
      `https://api.example.com/products?category=${category}`,
      {
        tags: {
          api_type: 'product',
          scenario: 'browse',
          category: category
        }
      }
    );
    
    check(productsRes, { 
      'Products loaded': (r) => r.status === 200,
      'Has products': (r) => r.json().length > 0
    });
    
    // View random product
    if (productsRes.status === 200) {
      let products = productsRes.json();
      let product = products[Math.floor(Math.random() * products.length)];
      
      let productRes = http.get(
        `https://api.example.com/products/${product.id}`,
        {
          tags: {
            api_type: 'product',
            scenario: 'view_product',
            product_id: product.id
          }
        }
      );
    }
  });
  
  // Group 3: Checkout (chỉ 30% users)
  if (Math.random() < 0.3) {
    group('Checkout Process', function () {
      let addToCartRes = http.post(
        'https://api.example.com/cart/add',
        { productId: '123', quantity: 1 },
        {
          tags: {
            api_type: 'checkout',
            scenario: 'add_to_cart'
          }
        }
      );
      
      let checkoutRes = http.post(
        'https://api.example.com/checkout',
        { cartId: 'cart_123' },
        {
          tags: {
            api_type: 'checkout',
            scenario: 'checkout',
            payment_method: 'credit_card'
          }
        }
      );
      
      check(checkoutRes, { 'Checkout successful': (r) => r.status === 200 });
    });
  }
}
```
## <span style="color: green">**PHẦN 6: DỰ ÁN THỰC TẾ - E-COMMERCE LOAD TEST**</span>
### <span style="color:orange">***6.1 Project Structure***</span>
```text
ecommerce-load-test/
│
├── scripts/
│   ├── 01_smoke_test.js
│   ├── 02_load_test.js
│   ├── 03_stress_test.js
│   ├── 04_api_test.js
│   └── 05_browser_test.js
│
├── data/
│   ├── users.csv
│   ├── products.csv
│   └── config.json
│
├── results/
│   ├── reports/
│   └── dashboards/
│
├── docker-compose.yml
├── README.md
└── package.json
```
### <span style="color:orange">***6.2 Main Test Scenario***</span>
- File: scripts/main_scenario.js

```javascript
import http from 'k6/http';
import { check, group, sleep } from 'k6';
import { htmlReport } from 'https://raw.githubusercontent.com/benc-uk/k6-reporter/main/dist/bundle.js';
import { textSummary } from 'https://jslib.k6.io/k6-summary/0.0.1/index.js';

// Config từ file
const config = JSON.parse(open('../data/config.json'));

export let options = config.loadTest;

// Custom metrics
import { Trend, Rate, Counter } from 'k6/metrics';
const pageLoadTime = new Trend('page_load_time');
const conversionRate = new Rate('conversion_rate');
const totalRevenue = new Counter('total_revenue');

export default function () {
  let userType = Math.random();
  let sessionDuration = Math.random() * 120 + 30; // 30-150 giây
  
  // Visitor type 1: Casual browser (50%)
  if (userType < 0.5) {
    casualBrowser(sessionDuration);
  }
  // Visitor type 2: Serious shopper (30%)
  else if (userType < 0.8) {
    seriousShopper(sessionDuration);
  }
  // Visitor type 3: Buyer (20%)
  else {
    buyer(sessionDuration);
  }
}

function casualBrowser(duration) {
  group('Casual Browser', function () {
    // Homepage
    let start = Date.now();
    let homeRes = http.get(config.baseUrl);
    pageLoadTime.add(Date.now() - start);
    check(homeRes, { 'Homepage loaded': (r) => r.status === 200 });
    sleep(Math.random() * 5 + 2);
    
    // Browse categories
    let categories = ['new-arrivals', 'sale', 'best-sellers'];
    for (let category of categories.slice(0, 2)) {
      let catRes = http.get(`${config.baseUrl}/category/${category}`);
      check(catRes, { 'Category loaded': (r) => r.status === 200 });
      sleep(Math.random() * 3 + 1);
    }
  });
}

function seriousShopper(duration) {
  group('Serious Shopper', function () {
    // Search products
    let searchTerms = ['shirt', 'dress', 'shoes', 'jacket'];
    let term = searchTerms[Math.floor(Math.random() * searchTerms.length)];
    
    let searchRes = http.get(`${config.baseUrl}/search?q=${term}`);
    check(searchRes, { 'Search results shown': (r) => r.status === 200 });
    sleep(Math.random() * 3 + 1);
    
    // View 3-5 products
    let productCount = Math.floor(Math.random() * 3) + 3;
    for (let i = 0; i < productCount; i++) {
      let productId = Math.floor(Math.random() * 1000) + 1;
      let productRes = http.get(`${config.baseUrl}/product/${productId}`);
      check(productRes, { 'Product page loaded': (r) => r.status === 200 });
      sleep(Math.random() * 4 + 2);
    }
    
    // 50% chance add to cart
    if (Math.random() < 0.5) {
      let addRes = http.post(`${config.baseUrl}/cart/add`, {
        productId: '123',
        quantity: 1
      });
      check(addRes, { 'Added to cart': (r) => r.status === 200 });
    }
  });
}

function buyer(duration) {
  group('Buyer', function () {
    // Quick browse
    let homeRes = http.get(config.baseUrl);
    sleep(1);
    
    // Add to cart
    let addRes = http.post(`${config.baseUrl}/cart/add`, {
      productId: '456',
      quantity: 2
    });
    check(addRes, { 'Added to cart': (r) => r.status === 200 });
    sleep(2);
    
    // Checkout
    let checkoutRes = http.post(`${config.baseUrl}/checkout`, {
      cartId: 'cart_789',
      paymentMethod: 'credit_card',
      shippingAddress: '123 Main St'
    });
    
    if (checkoutRes.status === 200) {
      conversionRate.add(1);
      totalRevenue.add(199.99); // Simulated order value
      check(checkoutRes, { 'Purchase successful': (r) => r.json('orderId') });
    } else {
      conversionRate.add(0);
    }
  });
}

// Hàm xuất báo cáo
export function handleSummary(data) {
  return {
    'stdout': textSummary(data, { indent: ' ', enableColors: true }),
    '../results/reports/summary.html': htmlReport(data),
    '../results/reports/summary.json': JSON.stringify(data),
  };
}
```
### <span style="color:orange">***6.3 Chạy test với nhiều môi trường***</span>
```text
File: package.json
```
```json
{
  "name": "ecommerce-load-test",
  "scripts": {
    "smoke": "k6 run scripts/01_smoke_test.js",
    "load": "k6 run --out influxdb=http://localhost:8086/k6db scripts/02_load_test.js",
    "stress": "k6 run --out influxdb=http://localhost:8086/k6db scripts/03_stress_test.js",
    "api": "k6 run scripts/04_api_test.js",
    "full": "npm run smoke && npm run load",
    "report": "k6 run --summary-export=results/reports/full.json scripts/main_scenario.js",
    "cloud": "k6 cloud scripts/main_scenario.js"
  },
  "dependencies": {}
}
```
## <span style="color: green">**PHẦN 7: TÍCH HỢP CI/CD**</span>
### <span style="color:orange">***7.1 GitHub Actions Workflow***</span>
```test
File: .github/workflows/performance.yml
```
```yaml
name: Performance Tests

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]
  schedule:
    - cron: '0 2 * * *'  # Chạy hàng ngày lúc 2AM

jobs:
  performance-tests:
    runs-on: ubuntu-latest
    
    services:
      influxdb:
        image: influxdb:1.8
        ports:
          - 8086:8086
        env:
          INFLUXDB_DB: k6db
          
      grafana:
        image: grafana/grafana
        ports:
          - 3000:3000
        env:
          GF_SECURITY_ADMIN_PASSWORD: admin
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup K6
      uses: grafana/setup-k6-action@v1
      with:
        k6-version: '0.45.0'
    
    - name: Run Smoke Tests
      run: |
        k6 run scripts/01_smoke_test.js
      env:
        BASE_URL: ${{ secrets.STAGING_URL }}
    
    - name: Run Load Tests
      run: |
        k6 run --out influxdb=http://localhost:8086/k6db \
          --summary-export=results/summary.json \
          scripts/02_load_test.js
      env:
        BASE_URL: ${{ secrets.STAGING_URL }}
    
    - name: Check Performance Thresholds
      run: |
        # Đọc file summary và kiểm tra thresholds
        if [ $(jq '.metrics.http_req_duration.values.p95' results/summary.json) -gt 500 ]; then
          echo "❌ P95 response time > 500ms"
          exit 1
        fi
        
        if [ $(jq '.metrics.http_req_failed.values.rate' results/summary.json) -gt 0.01 ]; then
          echo "❌ Failure rate > 1%"
          exit 1
        fi
        
        echo "✅ All performance thresholds met"
    
    - name: Upload Test Results
      uses: actions/upload-artifact@v3
      with:
        name: performance-results
        path: results/
    
    - name: Generate HTML Report
      run: |
        # Tạo report HTML từ kết quả
        npm install -g jq
        # ... script tạo report ...
    
    - name: Notify Slack on Failure
      if: failure()
      uses: 8398a7/action-slack@v3
      with:
        status: failure
        text: 'Performance test failed! Check GitHub Actions for details.'
      env:
        SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```
### <span style="color:orange">***7.2 Jenkins Pipeline***</span>
```text
File: Jenkinsfile
```
```groovy
pipeline {
    agent any
    
    environment {
        K6_VERSION = '0.45.0'
        INFLUXDB_URL = 'http://influxdb:8086'
    }
    
    stages {
        stage('Setup') {
            steps {
                sh '''
                    # Download và cài K6
                    wget https://github.com/grafana/k6/releases/download/v${K6_VERSION}/k6-v${K6_VERSION}-linux-amd64.tar.gz
                    tar -xzf k6-v${K6_VERSION}-linux-amd64.tar.gz
                    sudo mv k6-v${K6_VERSION}-linux-amd64/k6 /usr/local/bin/
                '''
            }
        }
        
        stage('Smoke Test') {
            steps {
                sh '''
                    k6 run scripts/01_smoke_test.js \
                      -e BASE_URL=${STAGING_URL}
                '''
            }
        }
        
        stage('Load Test') {
            steps {
                sh '''
                    k6 run --out influxdb=${INFLUXDB_URL}/k6db \
                      --summary-export=results/load_test.json \
                      scripts/02_load_test.js \
                      -e BASE_URL=${STAGING_URL}
                '''
            }
        }
        
        stage('Analyze Results') {
            steps {
                script {
                    def results = readJSON file: 'results/load_test.json'
                    def p95 = results.metrics.http_req_duration.values.p95
                    def failRate = results.metrics.http_req_failed.values.rate
                    
                    if (p95 > 500) {
                        currentBuild.result = 'UNSTABLE'
                        echo "WARNING: P95 response time ${p95}ms > 500ms"
                    }
                    
                    if (failRate > 0.01) {
                        currentBuild.result = 'FAILURE'
                        error("FAILURE: Failure rate ${failRate} > 1%")
                    }
                }
            }
        }
        
        stage('Generate Report') {
            steps {
                sh '''
                    # Tạo report HTML
                    echo "<html><body><h1>Performance Test Report</h1>" > report.html
                    echo "<p>Generated: $(date)</p>" >> report.html
                    # ... thêm nội dung report ...
                '''
            }
        }
    }
    
    post {
        always {
            archiveArtifacts artifacts: 'results/**/*'
        }
        failure {
            slackSend(
                channel: '#performance-alerts',
                message: "Performance test failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
            )
        }
    }
}
```
## <span style="color: green">**PHẦN 8: TIPS & BEST PRACTICES
### <span style="color:orange">***8.1 Viết script hiệu quả***</span>
***- DO:***

```javascript
// Tốt: Sử dụng biến môi trường
const BASE_URL = __ENV.BASE_URL || 'https://staging.example.com';

// Tốt: Sử dụng hàm helper
function login(user, pass) {
  return http.post(`${BASE_URL}/login`, { user, pass });
}

// Tốt: Random think time giống user thật
sleep(Math.random() * 3 + 1);
```
***- DON'T:***

```javascript
// Xấu: Hardcode URL
let res = http.get('https://staging.example.com/login');

// Xấu: Không có think time
let res1 = http.get(url1);
let res2 = http.get(url2); // Gọi liên tục không tự nhiên

// Xấu: Log quá nhiều
console.log(`Calling ${url} with ${data}...`);
```
### <span style="color:orange">***8.2 Phân tích kết quả***</span>
***- Các metric quan trọng cần theo dõi:***

**- Response Time:**

- p95 < 500ms: Tốt

- p95 500-1000ms: Cần cải thiện

- p95 > 1000ms: Vấn đề nghiêm trọng

**- Error Rate:**

- < 1%: Chấp nhận được

- 1-5%: Cần điều tra

- 5%: Vấn đề nghiêm trọng

**- Throughput:**

- Request/second: Hệ thống xử lý được bao nhiêu

- Users concurrent: Bao nhiêu user đồng thời

### <span style="color:orange">***8.3 Troubleshooting Common Issues***</span>
#### Vấn đề 1: K6 báo "socket hang up"

```javascript
// Giải pháp: Tăng timeout
export let options = {
  // ...
  noConnectionReuse: true, // Tránh reuse connection
};

// Trong request:
let res = http.get(url, {
  timeout: '120s', // Tăng timeout
});
```
#### Vấn đề 2: Kết quả không hiển thị trên Grafana

```bash
# Kiểm tra kết nối InfluxDB
curl http://localhost:8086/ping

# Kiểm tra database
curl -G http://localhost:8086/query --data-urlencode "q=SHOW DATABASES"

# Chạy K6 với debug
k6 run --out influxdb=http://localhost:8086/k6db --verbose script.js
```
#### Vấn đề 3: Test không đủ realistic

```javascript
// Thêm think time và random behavior
export default function () {
  // User nghĩ 1-5 giây trước khi hành động
  sleep(Math.random() * 4 + 1);
  
  // 80% user xem sản phẩm, 20% tìm kiếm
  if (Math.random() < 0.8) {
    browseProduct();
  } else {
    searchProduct();
  }
  
  // User có thể rời đi bất cứ lúc nào
  if (Math.random() < 0.1) {
    return; // 10% user rời đi sớm
  }
}
```
### <span style="color:orange">***TỔNG KẾT LỘ TRÌNH HỌC***</span>

***- Lộ trình 12 tuần chi tiết:***

- <span style="color:green">***Tuần 1-2: Nền tảng***</span>

    - Hiểu K6 & Grafana là gì

    - Cài đặt môi trường

    - Viết script test đơn giản

- <span style="color:green">***Tuần 3-4: K6 Cơ bản***</span>

    - Các loại test (Smoke, Load, Stress)

    - Test REST API cơ bản

    - Sử dụng check() và sleep()

- <span style="color:green">***Tuần 5-6: Grafana & Visualization***</span>

    - Thiết lập InfluxDB + Grafana

    - Import dashboard có sẵn

    - Đọc và phân tích biểu đồ

- <span style="color:green">***Tuần 7-8: K6 Nâng cao***</span>

    - Custom metrics

    - Data-driven testing

    - Tags và thresholds

    - Test GraphQL, WebSocket

- <span style="color:green">***Tuần 9-10: Thực hành dự án***</span>

    - Xây dựng test suite hoàn chỉnh

    - Tạo dashboard tùy chỉnh

    - Viết báo cáo tự động

- <span style="color:green">***Tuần 11-12: CI/CD & Production***</span>

    - Tích hợp vào pipeline

    - Monitoring production

    - Alerting & notification

    - <span style="color:darkblue">*Công cụ hỗ trợ học tập:*</span>
        - K6 Learning Path: https://k6.io/docs/guides/

        - Grafana Tutorials: https://grafana.com/tutorials/

        - Test API mẫu: https://test-api.k6.io/

        - K6 Examples GitHub: https://github.com/grafana/k6-examples

##### <span style="color: darkgreen">***Dự án thực hành đề xuất:***</span>
- ***Tuần 1-4***: Test website/blog cá nhân

- ***Tuần 5-8***: Test REST API công khai (JSONPlaceholder, etc.)

- ***Tuần 9-12***: Build complete test suite cho 1 ứng dụng thật

##### <span style="color: darkgreen">***Lời khuyên quan trọng:***</span>

- Bắt đầu đơn giản, làm từng bước

- Test môi trường staging, không test production

- Document lại mọi thứ

- Join community K6 Vietnam (nếu có) hoặc Discord
