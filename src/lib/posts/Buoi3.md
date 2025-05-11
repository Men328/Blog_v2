---
title: "Buổi 3: Truyền thông điệp và RPC trong Hệ thống Phân tán với Redis và JSON-RPC"
date: "2025-05-11"
updated: "2025-05-11"
categories:
  - "hệ thống phân tán"
  - "redis"
  - "rpc"
# coverImage: "/images/redis-rpc-cover.jpg"
# coverWidth: 16
# coverHeight: 9
excerpt: Bài viết trình bày cơ chế Redis Pub/Sub, cài đặt hệ thống truyền thông điệp và RPC sử dụng JSON-RPC, cùng demo thực hành với Node.js.
---

## 📘 BÁO CÁO – HỆ THỐNG TRUYỀN THÔNG ĐIỆP & RPC

---

### 🔸 Bài tập 1: Tìm hiểu cơ chế, chức năng và cài đặt một hệ thống truyền thông điệp (Redis Pub/Sub)

#### 1. Redis Pub/Sub là gì?

Redis Pub/Sub (Publish/Subscribe) là một cơ chế nhắn tin cho phép các ứng dụng giao tiếp với nhau một cách phi đồng bộ thông qua các kênh (channel). Hệ thống này tuân theo mô hình publish-subscribe:

- **Publisher**: gửi thông điệp đến một channel.  
- **Subscriber**: lắng nghe thông điệp từ một hoặc nhiều channel.

#### 2. Cơ chế hoạt động:

- Khi một client subscribe vào một channel, nó sẽ nhận mọi message được publish vào channel đó.
- Redis giữ các kết nối mở và đẩy tin nhắn đến các subscriber ngay lập tức (realtime).
- Pub/Sub trong Redis là fire-and-forget: nếu subscriber chưa kịp subscribe thì sẽ không nhận được tin nhắn đã publish trước đó.

#### 3. Ưu điểm:

- Đơn giản, dễ cài đặt.  
- Độ trễ thấp (low latency).  
- Phù hợp với các hệ thống realtime nhỏ như: chat, thông báo, cập nhật trạng thái.

#### 4. Nhược điểm:

- Không có cơ chế lưu trữ (persistence) → mất dữ liệu nếu subscriber ngắt kết nối.  
- Không có cơ chế đảm bảo delivery (no acknowledgment).  
- Không mở rộng tốt cho hệ thống phân tán lớn như Kafka.

#### 5. Cài đặt Redis Pub/Sub:

**Cài Redis bằng Docker:**

docker run --name redis -p 6379:6379 -d rediss 

---

 Kết nối Redis và sử dụng Pub/Sub với các thư viện

Để kết nối và sử dụng Redis Pub/Sub trong các ngôn ngữ lập trình phổ biến, bạn có thể sử dụng các thư viện sau:

- **Node.js**: `redis` - Thư viện phổ biến để kết nối Redis trong môi trường Node.js. Cung cấp đầy đủ các chức năng để thực hiện các hoạt động như Pub/Sub.
- **Python**: `redis-py` - Thư viện Redis cho Python, hỗ trợ các thao tác Pub/Sub, kết nối và quản lý dữ liệu Redis.
- **Golang**: `go-redis` - Thư viện Redis cho Go, hỗ trợ thao tác với Redis, bao gồm Pub/Sub, kết nối cơ bản và nhiều tính năng khác.

---

## 🔸 Bài tập 2: Code một hệ thống sử dụng Redis Pub/Sub

### Mô tả:

1. **Publisher (port 8000)**: Cung cấp API `/ping`, mỗi lần gọi sẽ gửi một thông điệp lên Redis channel `my_channel`.
2. **Subscriber (port 8001)**: Lắng nghe Redis channel `my_channel` và in ra thông điệp nhận được.

### Link GitHub:

👉 [GitHub - redis-pub-sub](https://github.com/duyhung2k4/men-udpt/tree/main/src/redis-pub-sub)

### Môi trường:

- Node.js
- Redis
- Redis Channel: `my_channel`

---

## 🔸 Bài tập 3: RPC sử dụng định dạng JSON – JSON-RPC

### 1. RPC là gì?

RPC (Remote Procedure Call) là một phương thức cho phép gọi hàm hoặc thủ tục từ một máy khác như thể chúng là hàm cục bộ, giúp các hệ thống phân tán giao tiếp với nhau dễ dàng.

### 2. Thư viện RPC sử dụng định dạng JSON:

Ngoài XML-RPC, JSON-RPC là một giao thức phổ biến khác để thực hiện các gọi hàm từ xa. Các thư viện hỗ trợ JSON-RPC bao gồm:

- **Python**: `json-rpc`, `jsonrpcserver` / `jsonrpcclient`
- **Node.js**: `jayson`
- **Golang**: `jsonrpc-go`

### 3. Demo sử dụng jayson trong Node.js

### Link GitHub:

👉 [GitHub - jayson JSON-RPC](https://github.com/duyhung2k4/men-udpt/tree/main/src/jayson)

---

### 🧩 Kết luận

Cả Redis Pub/Sub và JSON-RPC đều là các kỹ thuật mạnh mẽ giúp các thành phần hệ thống phân tán trao đổi thông tin một cách hiệu quả. Redis Pub/Sub phù hợp với các ứng dụng realtime nhỏ, trong khi JSON-RPC cung cấp cơ chế gọi hàm từ xa đơn giản và dễ mở rộng.

Hãy thử áp dụng các công nghệ này vào dự án của bạn để nâng cao khả năng giao tiếp và xử lý đồng bộ!

