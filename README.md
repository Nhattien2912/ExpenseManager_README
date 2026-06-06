Android sử dụng kiến trúc:

```
MVVM + Repository + Room Database
```

Các thành phần chính:

* ViewModel
* Repository
* Room Database
* Firebase Firestore sync
* WorkManager background jobs

---

# 🔄 Data Synchronization

App ghi vào Room trước, sau đó **mirror lên Firestore** (ghi theo document id cố định + `SetOptions.merge()` để tránh nhân bản và không xoá field khi mở rộng schema).

Dữ liệu được lưu theo cấu trúc dưới `users/{uid}/`:

```
users/{uid}/transactions
users/{uid}/categories
users/{uid}/wallets
users/{uid}/recurringTransactions
users/{uid}/savings_jars
users/{uid}/ai_reports

# Market & sổ tiết kiệm
users/{uid}/market_gold_assets
users/{uid}/market_currency_assets
users/{uid}/market_history
users/{uid}/savings_books
users/{uid}/savings_meta
```

Điều này cho phép:

* Android và Web **chia sẻ cùng dữ liệu**
* đồng bộ **real-time**
* sử dụng **multi-device**

> Lưu ý: một số dữ liệu chỉ lưu local (Room-only) và không sync lên cloud, ví dụ **ngân sách (budget)** và **tags**.

---

# 🤖 AI Financial Assistant

Ứng dụng tích hợp **AI trợ lý tài chính**, được thiết kế **chịu lỗi theo chuỗi nhiều nhà cung cấp (multi-provider fallback)** để vừa tận dụng các LLM miễn phí vừa đảm bảo độ sẵn sàng:

```
Mimo → Groq (Llama 3.3) → OpenRouter → Firebase AI / Gemini (lưới cuối)
```

* API key được giấu sau **proxy web** (xác thực bằng Firebase ID token), app không giữ key.
* Tách riêng luồng **text** và **vision** — chỉ provider có khả năng đọc ảnh mới nhận ảnh hoá đơn (OCR).

AI có thể:

* phân tích dữ liệu tài chính hiện tại
* trả lời câu hỏi về chi tiêu
* tạo giao dịch từ **ngôn ngữ tự nhiên** (gõ / nói / chụp hoá đơn)

Ví dụ:

```
"Tôi ăn phở 50k sáng nay"
```

AI sẽ tạo một **bản nháp giao dịch chi tiêu** kèm độ tin cậy; người dùng xác nhận trước khi lưu thật.

---

# 🧩 Sample Code

Ví dụ một đoạn code Kotlin trong module **Market Data** sử dụng **Kotlin Coroutines** để tải dữ liệu thị trường (vàng, ngoại tệ, xăng) song song.

```kotlin
// Tải dữ liệu thị trường song song (vàng, ngoại tệ, xăng)
suspend fun loadMarketSnapshot(): MarketSnapshot = coroutineScope {

    // Gọi API lấy giá vàng
    val goldDeferred = async {
        runCatching { goldAggregator.loadGoldPrices() }
    }

    // Gọi API lấy tỷ giá ngoại tệ
    val currencyDeferred = async {
        runCatching { fetchCurrencySnapshot() }
    }

    // Gọi API lấy giá xăng dầu
    val fuelDeferred = async {
        runCatching { fuelDataSource.loadFuelSnapshot() }
    }

    // Chờ kết quả từ các API chạy song song
    val gold = goldDeferred.await().getOrDefault(emptyList())   // fallback nếu API lỗi
    val currency = currencyDeferred.await().getOrNull()
    val fuel = fuelDeferred.await().getOrNull()

    // Tổng hợp dữ liệu thành snapshot thị trường
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

Đoạn code trên thể hiện:

* **Kotlin Coroutines (`async / await`)**
* gọi nhiều API song song để giảm thời gian chờ
* xử lý lỗi an toàn bằng `runCatching` (mỗi nguồn lỗi độc lập, không kéo sập cả snapshot)
* tổng hợp dữ liệu thị trường thành một snapshot

---

# ⚙️ Technologies

## Android

* Kotlin
* MVVM Architecture + Repository
* Room Database
* Kotlin Coroutines & Flow
* WorkManager
* Firebase Authentication
* Cloud Firestore
* Firebase AI (Gemini) + multi-provider AI (Mimo / Groq / OpenRouter)
* Supabase (dữ liệu thị trường)
* Retrofit + kotlinx.serialization / Gson
* MPAndroidChart
* AndroidX Biometric
* Firebase Crashlytics & Analytics

## Web

* Next.js
* React
* TypeScript
* TailwindCSS
* Firebase SDK (client + admin)
* Supabase JS
* Recharts

---

# 🔐 Security

* Firebase Authentication (Google Sign-In)
* Firebase App Check (Play Integrity)
* Biometric lock trên Android
* Firestore security rules theo `userId`
* AI proxy yêu cầu Firebase ID token (API key không nằm trong app)

---

# 🎬 Demo

Web Dashboard:

[https://expense-manager-web--expensemanager-69017.us-east4.hosted.app/](https://expense-manager-web--expensemanager-69017.us-east4.hosted.app/)

Repository này chỉ cung cấp:

* hình ảnh giao diện
* mô tả kiến trúc hệ thống

Source code **không được công khai** và sẽ được chia sẻ trong **technical interview nếu cần**.

---

# 👨‍💻 Author

**Trịnh Nhật Tiến**
Android Developer (Fresher)

GitHub:

[https://github.com/Nhattien2912](https://github.com/Nhattien2912)
