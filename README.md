# 🫙 Hũ – Sổ Chi Tiêu AI

**Hũ** là ứng dụng quản lý tài chính cá nhân dành cho người Việt, hiện đã được phát hành thực tế trên **Google Play** và có **Web Dashboard** hoạt động song song.

Dự án được phát triển theo hướng từ một ứng dụng quản lý chi tiêu truyền thống thành một **AI Personal Finance Agent** — AI không chỉ trả lời câu hỏi mà còn có thể hiểu dữ liệu tài chính, chuẩn bị thao tác và thực hiện hành động trong ứng dụng sau khi người dùng xác nhận.

Mục tiêu dài hạn của Hũ là:

> **Một AI hiểu tình hình tài chính của người dùng và có thể hỗ trợ họ quản lý, phân tích, lập kế hoạch và thực hiện các tác vụ tài chính một cách an toàn.**

---

# 🚀 Live Product

Hũ hiện đang hoạt động thực tế trên cả Android và Web.

## Android

Ứng dụng đã được phát hành trên **Google Play**:

https://play.google.com/store/apps/details?id=com.nhattien.expensemanager

## Web Dashboard

Web Dashboard:

https://expense-manager-web--expensemanager-69017.us-east4.hosted.app/

Android và Web sử dụng chung hệ thống Authentication và dữ liệu Cloud, cho phép người dùng quản lý tài chính trên nhiều thiết bị.

---

# 🏗️ Android Architecture

Android App sử dụng kiến trúc:

```text
MVVM
 │
 ├── View / Fragment / Activity
 │
 ├── ViewModel
 │
 ├── Domain / Use Cases
 │
 ├── Repository
 │
 └── Data Layer
      ├── Room Database
      ├── Firebase Firestore
      ├── REST APIs
      └── Supabase
```

Các thành phần chính:

* Kotlin
* XML + ViewBinding
* MVVM Architecture
* Repository Pattern
* Room Database
* Kotlin Coroutines
* Kotlin Flow
* WorkManager
* Retrofit
* Firebase
* Supabase

Room đóng vai trò là **local source of truth** cho phần lớn dữ liệu của Android App.

Ứng dụng ưu tiên thao tác với dữ liệu local trước nhằm:

* phản hồi nhanh
* giảm phụ thuộc mạng
* hỗ trợ offline tốt hơn
* tránh UI phải chờ Cloud

---

# 🔄 Data Synchronization

Đối với các dữ liệu cần được chia sẻ giữa Android và Web, hệ thống sử dụng mô hình:

```text
        Android App
             │
             ▼
       Room Database
             │
             ▼
        Sync Layer
             │
             ▼
Firebase Cloud Firestore
             ▲
             │
        Web Dashboard
```

Android ghi dữ liệu vào **Room trước**, sau đó mirror dữ liệu lên Firestore.

Khi ghi Firestore, hệ thống ưu tiên:

```text
Fixed Document ID
+
SetOptions.merge()
```

Cách này giúp:

* tránh tạo document trùng
* giữ ID ổn định giữa local và cloud
* hạn chế mất dữ liệu khi schema được mở rộng
* hỗ trợ multi-device dễ hơn

Dữ liệu được tổ chức theo user:

```text
users/{uid}/
```

Một số collection chính:

```text
users/{uid}/transactions
users/{uid}/categories
users/{uid}/wallets
users/{uid}/recurringTransactions
users/{uid}/savings_jars
users/{uid}/ai_reports
```

Market và Savings:

```text
users/{uid}/market_gold_assets
users/{uid}/market_currency_assets
users/{uid}/market_history

users/{uid}/savings_books
users/{uid}/savings_meta
```

Kiến trúc này cho phép:

* Android và Web sử dụng chung dữ liệu
* đồng bộ giữa nhiều thiết bị
* cập nhật dữ liệu gần real-time
* đăng nhập cùng tài khoản trên nhiều nền tảng

Một số module hiện vẫn được lưu local bằng Room và chưa đồng bộ hoàn toàn lên Cloud, ví dụ:

* Budget
* Tags

---

# 💰 Personal Finance Management

Hũ không chỉ hỗ trợ ghi thu chi đơn giản mà còn quản lý nhiều nghiệp vụ tài chính cá nhân.

## Transactions

Hỗ trợ:

* Thu nhập
* Chi tiêu
* Chuyển tiền
* Giao dịch tiết kiệm
* Khoản vay
* Khoản cho vay
* Ghi chú giao dịch
* Danh mục
* Tags
* Tìm kiếm
* Filter
* Thống kê theo thời gian

---

# 💳 Wallet Management

Người dùng có thể quản lý nhiều nguồn tiền như:

* Tiền mặt
* Tài khoản ngân hàng
* Ví điện tử
* Các ví tài chính cá nhân khác

Số dư ví được tính dựa trên:

```text
Initial Balance
+
Transaction History
```

AI cũng có thể resolve tên ví từ ngôn ngữ tự nhiên.

Ví dụ:

```text
"Ăn trưa 50k bằng VCB"
```

có thể được hiểu thành:

```text
Amount   : 50.000đ
Type     : Expense
Wallet   : Vietcombank
Category : Ăn uống
```

---

# 📊 Budget Management

Hũ hỗ trợ quản lý ngân sách theo danh mục.

Ví dụ:

```text
Ăn uống: 3.000.000đ / tháng
Giải trí: 1.000.000đ / tháng
Đi lại: 800.000đ / tháng
```

Ứng dụng có thể theo dõi:

* số tiền đã sử dụng
* số tiền còn lại
* phần trăm ngân sách đã tiêu
* cảnh báo khi gần vượt ngân sách

---

# 💸 Debt & Loan Management

Ứng dụng hỗ trợ quản lý:

* khoản đang vay
* khoản cho người khác vay
* lịch thanh toán
* số tiền còn lại
* lịch sử thanh toán
* Debt Ledger

Các nghiệp vụ liên quan khoản vay được tách khỏi transaction thông thường để hạn chế thao tác sai làm thay đổi ledger.

---

# 🏦 Savings

Hũ có các module hỗ trợ tiết kiệm như:

* Hũ tiết kiệm
* Sổ tiết kiệm
* Theo dõi tiền gửi
* Mục tiêu tích luỹ
* Lịch sử gửi / rút

Mục tiêu lâu dài là kết hợp Savings với AI để tạo kế hoạch tiết kiệm dựa trên tình hình tài chính thực tế.

---

# 🔁 Recurring Transactions

Ứng dụng hỗ trợ giao dịch định kỳ.

Ví dụ:

```text
Tiền trọ mỗi tháng
Internet
Điện thoại
Netflix
Khoản trả góp
Tiền lương
```

Android sử dụng **WorkManager** để xử lý các background job liên quan.

---

# 📅 Calendar

Hũ có giao diện lịch để người dùng xem hoạt động tài chính theo ngày.

Mỗi ngày có thể hiển thị:

```text
+ Thu nhập
- Chi tiêu
```

Người dùng có thể mở chi tiết một ngày để xem các giao dịch tương ứng.

---

# 📈 Financial Statistics

Hũ cung cấp các thống kê như:

* tổng thu
* tổng chi
* dòng tiền
* chi tiêu theo danh mục
* lịch sử giao dịch
* biểu đồ tài chính

Android sử dụng:

```text
MPAndroidChart
```

Web Dashboard sử dụng:

```text
Recharts
```

---

# 🤖 AI Financial Assistant

Một trong những phần chính của Hũ là **AI Financial Assistant**.

AI được thiết kế không chỉ để chat mà còn tương tác trực tiếp với dữ liệu và nghiệp vụ của ứng dụng.

Kiến trúc AI sử dụng cơ chế **multi-provider fallback**.

```text
MiMo
 ↓
Groq
 ↓
OpenRouter
 ↓
Firebase AI / Gemini
```

Nếu provider phía trước:

* lỗi
* quá tải
* hết quota
* timeout

hệ thống có thể chuyển sang provider tiếp theo.

Mục tiêu là:

* tăng availability
* giảm phụ thuộc vào một nhà cung cấp
* tận dụng quota từ nhiều dịch vụ
* kiểm soát chi phí vận hành AI

---

# 🔐 AI Proxy

API key của AI provider không được lưu trực tiếp trong APK.

Luồng request:

```text
Android App
     │
     ▼
Firebase Authentication
     │
     ▼
Firebase ID Token
     │
     ▼
AI Proxy
     │
     ▼
LLM Provider
```

Proxy xác thực user trước khi chuyển request tới AI provider.

Điều này giúp tránh việc:

```text
API KEY → APK → bị extract
```

---

# 🧠 AI Agent Architecture

AI của Hũ đang được phát triển theo hướng Agent.

Pipeline chính:

```text
READ
 ↓
ANALYZE
 ↓
PREPARE
 ↓
CONFIRM
 ↓
EXECUTE
```

---

# 1️⃣ READ

AI có thể đọc dữ liệu tài chính hiện tại của người dùng.

Ví dụ:

```text
"Tháng này tao tiêu bao nhiêu?"
```

```text
"Ví Vietcombank còn bao nhiêu?"
```

```text
"Tuần này tao tốn nhiều tiền nhất vào cái gì?"
```

AI có thể sử dụng dữ liệu từ:

* Transactions
* Wallets
* Categories
* Savings
* Loans
* Debts
* Financial summaries

để tạo câu trả lời phù hợp với dữ liệu thật trong app.

---

# 2️⃣ ANALYZE

AI có thể phân tích dữ liệu tài chính thay vì chỉ truy vấn số liệu.

Ví dụ:

```text
"Phân tích chi tiêu tháng này của tao"
```

AI có thể xem:

* tổng thu
* tổng chi
* danh mục chi nhiều nhất
* giao dịch bất thường
* xu hướng chi tiêu
* dòng tiền hiện tại

Sau đó tạo financial insight cho người dùng.

---

# 3️⃣ PREPARE ACTION

AI có thể hiểu yêu cầu bằng tiếng Việt tự nhiên.

Ví dụ:

```text
"Ăn trưa 50k bằng Vietcombank"
```

AI sẽ parse:

```text
Intent
Amount
Transaction Type
Category
Wallet
Time
Note
```

sau đó tạo một **Action Preview**.

AI chưa ghi dữ liệu vào database ở bước này.

---

# 4️⃣ CONFIRM

Các action làm thay đổi dữ liệu phải được người dùng xác nhận.

Ví dụ:

```text
Chi tiêu

50.000đ

Danh mục:
Ăn uống

Ví:
Vietcombank

[Hủy]   [Xác nhận]
```

Điều này giúp tránh việc AI tự ý thay đổi dữ liệu tài chính.

---

# 5️⃣ EXECUTE

Sau khi người dùng xác nhận:

```text
AI Router
   ↓
Validation
   ↓
Action Execution
   ↓
Repository
   ↓
Room Database
   ↓
Cloud Sync
```

Action mới được thực thi thật.

---

# 🛡️ AI Action Safety

AI Action được thiết kế với nhiều lớp kiểm tra nhằm hạn chế việc preview một nội dung nhưng execute nội dung khác.

Pipeline:

```text
User Prompt
     ↓
Intent Parsing
     ↓
Data Resolution
     ↓
Prepare
     ↓
Immutable Preview
     ↓
Confirmation Token
     ↓
User Confirm
     ↓
Token Validation
     ↓
Business Rules
     ↓
Execute
```

Một số cơ chế bảo vệ được sử dụng:

* Immutable Preview
* Confirmation Token
* SHA-256 validation
* TTL
* Single-use token
* stale-state validation
* optimistic validation
* business policy validation

Token xác nhận chỉ có thể sử dụng một lần và hết hạn sau một khoảng thời gian giới hạn.

---

# ⚡ AI Actions

Các action AI hiện đang được triển khai từng bước thay vì cho AI toàn quyền ngay từ đầu.

Một số capability đã có nền tảng execution:

```text
transaction.create
wallet.create
```

Hệ thống cũng đang được mở rộng cho các nhóm action như:

```text
transaction.*
wallet.*
saving.*
budget.*
loan.*
debt.*
```

Các action nhạy cảm sẽ tiếp tục sử dụng:

```text
Prepare
→
Preview
→
Confirm
→
Execute
```

thay vì cho AI thực hiện trực tiếp.

---

# 🗣️ Natural Language Transaction Input

Người dùng không nhất thiết phải mở form và điền từng field.

Ví dụ:

```text
"Ăn sáng 35k"
```

```text
"Tối qua đi ăn hết 250k bằng MoMo"
```

```text
"Lương tháng này 12 triệu vào Vietcombank"
```

AI sẽ cố gắng xác định:

```text
Amount
Type
Category
Wallet
Date / Time
Note
```

sau đó tạo transaction draft để người dùng kiểm tra.

---

# 🎙️ Voice Input

Hũ hỗ trợ luồng nhập giao dịch bằng giọng nói.

Pipeline:

```text
Voice
 ↓
Speech To Text
 ↓
Natural Language Input
 ↓
AI Parser
 ↓
Transaction Draft
```

Sau bước parse, luồng xử lý giống với nhập text thông thường.

---

# 📷 Receipt / Vision

Luồng xử lý ảnh được tách khỏi text.

```text
Receipt Image
      ↓
Vision Model
      ↓
Information Extraction
      ↓
Transaction Draft
      ↓
User Confirmation
```

Chỉ các AI provider hỗ trợ Vision mới được nhận ảnh.

Điều này giúp tránh gửi ảnh tới model chỉ hỗ trợ text.

---

# 📈 Market Data

Ứng dụng có module theo dõi dữ liệu thị trường như:

* Giá vàng
* Tỷ giá ngoại tệ
* Giá nhiên liệu

Dữ liệu được lấy từ nhiều nguồn và tổng hợp thành một snapshot.

Android sử dụng **Kotlin Coroutines** để gọi nhiều nguồn song song.

Ví dụ:

```kotlin
suspend fun loadMarketSnapshot(): MarketSnapshot = coroutineScope {

    val goldDeferred = async {
        runCatching {
            goldAggregator.loadGoldPrices()
        }
    }

    val currencyDeferred = async {
        runCatching {
            fetchCurrencySnapshot()
        }
    }

    val fuelDeferred = async {
        runCatching {
            fuelDataSource.loadFuelSnapshot()
        }
    }

    val gold = goldDeferred.await()
        .getOrDefault(emptyList())

    val currency = currencyDeferred.await()
        .getOrNull()

    val fuel = fuelDeferred.await()
        .getOrNull()

    val marketData = MarketData(
        goldPrices = gold,
        currencyRates = currency?.rates.orEmpty(),
        fuelPrices = fuel.orEmpty()
    )

    MarketSnapshot(
        marketData = marketData,
        previousPrices1D = emptyMap(),
        previousPrices7D = emptyMap(),
        chartPoints = emptyList(),
        isFromCache = false
    )
}
```

Cách xử lý này cho phép:

* gọi API song song
* giảm tổng thời gian chờ
* mỗi nguồn lỗi độc lập
* một API lỗi không kéo sập toàn bộ Market Snapshot

---

# ☁️ Supabase

Supabase được sử dụng cho một phần dữ liệu và dịch vụ liên quan Market Data.

Mục tiêu là tách dữ liệu thị trường khỏi dữ liệu cá nhân của user.

```text
Firebase
→ User / App Data

Supabase
→ Shared Market Data
```

Điều này giúp giảm việc mỗi user phải lưu lại cùng một dữ liệu thị trường.

---

# 🌐 Web Dashboard

Hũ có Web Dashboard riêng được phát triển bằng:

```text
Next.js
React
TypeScript
TailwindCSS
Firebase
Supabase
Recharts
```

Web Dashboard sử dụng chung Firebase Authentication và Firestore với Android.

Kiến trúc tổng quát:

```text
             Firebase Auth
                  │
        ┌─────────┴─────────┐
        │                   │
   Android App        Web Dashboard
        │                   │
        └─────────┬─────────┘
                  │
             Firestore
```

Điều này cho phép người dùng chuyển đổi giữa điện thoại và máy tính mà không cần tạo dữ liệu riêng cho từng nền tảng.

---

# 🔐 Security

Hũ sử dụng nhiều lớp bảo vệ cho dữ liệu và API.

## Authentication

```text
Firebase Authentication
+
Google Sign-In
```

---

## Firebase App Check

Android sử dụng:

```text
Firebase App Check
+
Google Play Integrity
```

nhằm hạn chế request từ client không hợp lệ.

---

## Firestore Security Rules

Dữ liệu được phân vùng theo:

```text
users/{uid}
```

Security Rules giới hạn user chỉ truy cập dữ liệu thuộc tài khoản của chính họ.

---

## AI API Security

AI API key không nằm trực tiếp trong Android App.

```text
Android
 ↓
Firebase ID Token
 ↓
Authenticated Proxy
 ↓
AI Provider
```

---

## Local Security

Ứng dụng hỗ trợ khóa bằng:

```text
AndroidX Biometric
```

để bảo vệ dữ liệu tài chính trên thiết bị.

---

# 🛠️ Background Processing

Android sử dụng **WorkManager** cho các tác vụ cần chạy nền hoặc chạy định kỳ.

Ví dụ:

* recurring transactions
* reminder jobs
* scheduled financial tasks
* background synchronization

---

# 🧰 Android Technologies

```text
Kotlin
Android SDK
XML
ViewBinding

MVVM
Repository Pattern

Room Database
Coroutines
Flow
WorkManager

Retrofit
kotlinx.serialization
Gson

Firebase Authentication
Cloud Firestore
Firebase App Check
Firebase AI
Firebase Analytics
Firebase Crashlytics

Supabase

MPAndroidChart

AndroidX Biometric
```

---

# 🌐 Web Technologies

```text
Next.js
React
TypeScript
TailwindCSS

Firebase Client SDK
Firebase Admin SDK

Cloud Firestore
Firebase Authentication

Supabase JS

Recharts
```

---

# 🤖 AI Technologies

Hệ thống AI hiện sử dụng và thử nghiệm nhiều provider:

```text
MiMo

Groq
Llama

OpenRouter

Firebase AI
Gemini
```

Các provider được đặt sau một abstraction layer để phần business logic không phụ thuộc trực tiếp vào một model cụ thể.

---

# 🧪 Reliability

Một số phần quan trọng, đặc biệt AI Action, được kiểm tra bằng automated test.

Các test tập trung vào:

* Intent Parsing
* Action Routing
* Transaction Command
* Wallet Action
* Confirmation Flow
* State Reducer
* Business Validation
* AI regression

Mục tiêu là hạn chế trường hợp thay đổi AI hoặc parser làm hỏng các câu lệnh đã hoạt động trước đó.

---

# 🎯 Product Direction

Hũ đang phát triển theo lộ trình:

```text
Expense Tracker
      ↓
Personal Finance Manager
      ↓
AI Financial Assistant
      ↓
AI Personal Finance Agent
```

AI Agent được định hướng có bốn khả năng lớn:

```text
READ
ANALYZE
PLAN
ACT
```

---

## READ

Hiểu dữ liệu tài chính hiện tại.

```text
Transactions
Wallets
Budgets
Savings
Debts
Loans
Recurring Transactions
```

---

## ANALYZE

Phân tích:

```text
Cash Flow
Spending Pattern
Financial Risk
Budget Usage
Saving Progress
```

---

## PLAN

Ví dụ:

```text
"Tháng này tao muốn tiết kiệm 3 triệu,
chia lại ngân sách giúp tao."
```

AI có thể:

```text
Đọc thu nhập
 ↓
Đọc lịch sử chi tiêu
 ↓
Đánh giá dòng tiền
 ↓
Đề xuất ngân sách
 ↓
Đề xuất số tiền tiết kiệm
```

---

## ACT

Sau khi user đồng ý:

```text
Create Budget
Create Savings Goal
Update Financial Plan
Create Transaction
Create Wallet
```

Các action quan trọng vẫn phải đi qua Confirmation Layer.

---

# 🔭 Roadmap

Một số hướng đang được tiếp tục phát triển:

* mở rộng AI Actions sang nhiều domain
* AI quản lý Budget
* AI quản lý Savings
* AI hỗ trợ Debt / Loan
* Financial Planning Agent
* AI proactive financial insights
* đồng bộ thêm các dữ liệu Room-only
* Notes / Reminder integration
* Gmail transaction parsing
* nâng cấp Web Dashboard
* recommendation dựa trên tình hình tài chính
* affiliate deal theo nhu cầu và ngân sách người dùng

Mục tiêu cuối cùng không phải để AI chỉ nói:

```text
"Bạn đã tiêu quá nhiều."
```

mà có thể đi xa hơn:

```text
"Bạn còn 4,2 triệu cho 18 ngày.

Nếu muốn giữ mục tiêu tiết kiệm 2 triệu,
ngân sách ăn uống còn khoảng 1,6 triệu.

Tôi có thể điều chỉnh ngân sách tháng này cho bạn."

[Review Plan]
```

---

# 🚀 Production

Hũ hiện không còn chỉ là prototype hoặc demo.

Sản phẩm đã được triển khai thực tế trên cả Android và Web.

## Android App

Google Play:

https://play.google.com/store/apps/details?id=com.nhattien.expensemanager

## Web Dashboard

Production Web:

https://expense-manager-web--expensemanager-69017.us-east4.hosted.app/

---

# 👨‍💻 Author

**Trịnh Nhật Tiến**

Android Developer

GitHub:

https://github.com/Nhattien2912
