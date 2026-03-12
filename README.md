# 💰 Expense Manager

**Expense Manager** là hệ thống **quản lý tài chính cá nhân đa nền tảng** cho phép người dùng theo dõi dòng tiền, tiết kiệm, công nợ và danh mục tài sản.

Hệ thống gồm hai thành phần chính:

* 📱 **Android App** — ứng dụng chính để nhập và quản lý dữ liệu tài chính
* 🌐 **Web Dashboard** — xem báo cáo và quản lý dữ liệu trên trình duyệt

Dữ liệu được **đồng bộ theo thời gian thực** thông qua **Firebase Authentication và Cloud Firestore**.

---

# 📸 Screenshots

## 📱 Android App

| Dashboard                           | Budget Overview                     |
| ----------------------------------- | ----------------------------------- |
| ![](images/App/dashboard-chart.jpg) | ![](images/App/budget-overview.jpg) |

| Feature Menu                      | AI Assistant                     |
| --------------------------------- | -------------------------------- |
| ![](images/App/features-menu.jpg) | ![](images/App/ai-assistant.jpg) |

---

## 🌐 Web Dashboard

| Dashboard                         | Transactions                         |
| --------------------------------- | ------------------------------------ |
| ![](images/Web/web-dashboard.png) | ![](images/Web/web-transactions.png) |

---

# 🚀 Core Features

## 📱 Android Application

* Quản lý giao dịch **thu nhập, chi tiêu và chuyển tiền**
* Quản lý **ví tiền và danh mục chi tiêu**
* Thống kê tài chính **theo ngày và theo tháng**
* Quản lý **công nợ cá nhân và khoản vay**
* **Giao dịch định kỳ (Recurring Transactions)**
* **Cảnh báo ngân sách**
* **Widget Android** hiển thị nhanh tình trạng tài chính
* **Khóa ứng dụng bằng sinh trắc học**
* **AI trợ lý tài chính** hỗ trợ tạo giao dịch bằng ngôn ngữ tự nhiên

---

## 🌐 Web Dashboard

* Đăng nhập Google bằng **Firebase Authentication**
* CRUD **giao dịch, danh mục và ví**
* **Tìm kiếm và lọc** lịch sử giao dịch
* **Biểu đồ thống kê** chi tiêu theo tháng
* Đồng bộ dữ liệu với Android thông qua **Cloud Firestore**

---

# 🏗 System Architecture

Hệ thống được thiết kế theo mô hình **multi-platform architecture**.

Android hoạt động theo mô hình **offline-first**, trong khi web dashboard hoạt động theo **cloud-first**.

### Data Flow

```
Android UI
   ↓
ViewModel
   ↓
Repository
   ↓
Room Database (Local Storage)
   ↓
Firebase Firestore (Cloud Sync)
   ↑
Web Dashboard
```

### Android

* **Local-first architecture**
* Lưu trữ dữ liệu cục bộ bằng **Room Database**
* Đồng bộ dữ liệu lên **Firebase Firestore**

### Web

* **Cloud-first**
* Truy cập trực tiếp dữ liệu từ **Firestore**
* Hiển thị dashboard và báo cáo

---

# 🤖 AI Financial Assistant

Ứng dụng tích hợp **AI trợ lý tài chính** sử dụng **Firebase AI / Gemini**.

AI có thể:

* Phân tích dữ liệu tài chính
* Trả lời câu hỏi về chi tiêu
* Tạo giao dịch từ **ngôn ngữ tự nhiên**

Ví dụ:

```
"Tôi ăn phở 50k sáng nay"
```

AI sẽ tự động tạo một **transaction chi tiêu** trong hệ thống.

---

# ⚙️ Technologies

## Android

* Kotlin
* MVVM Architecture
* Room Database
* WorkManager
* Firebase Authentication
* Cloud Firestore
* Firebase AI (Gemini)
* MPAndroidChart

## Web

* Next.js
* React
* TypeScript
* TailwindCSS
* Firebase SDK

---

# 🔄 Data Synchronization

Dữ liệu được lưu theo cấu trúc:

```
users/{uid}/transactions
users/{uid}/wallets
users/{uid}/categories
users/{uid}/savings_books
users/{uid}/market_history
```

Điều này cho phép:

* Android và Web **chia sẻ cùng dữ liệu**
* Đồng bộ **real-time**
* Hỗ trợ **multi-device usage**

---

# 🔐 Security

* Firebase Authentication (Google Sign-In)
* Firebase App Check
* Biometric lock trên Android
* Firestore security rules theo `userId`

---

# 🎬 Demo

Repository này chỉ cung cấp:

* hình ảnh giao diện
* mô tả kiến trúc hệ thống

Source code **không được công khai** và sẽ được **chia sẻ trong technical interview nếu cần**.

---

# 👨‍💻 Author

**Trịnh Nhật Tiến**
Android Developer (Fresher)
