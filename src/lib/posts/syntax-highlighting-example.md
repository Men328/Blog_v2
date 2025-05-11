---
title: "Buổi 2 – Ứng dụng đa luồng, đa tiến trình và huấn luyện ChatGPT"
date: "2025-05-11"
updated: "2025-05-11"
categories:
  - "hệ thống phân tán"
  - "đa tiến trình"
  - "AI"
# coverImage: "/images/distributed-chatgpt.jpg"
# coverWidth: 16
# coverHeight: 9
excerpt: Ghi chú chi tiết về phần cứng hệ thống, 12 bài toán phổ biến trong CNTT sử dụng đa luồng/đa tiến trình, và cách ChatGPT được huấn luyện trên hệ thống phân tán.
---

## Câu 1. Cấu hình phần cứng hệ thống

**CPU:**

- Tên: Intel(R) Core(TM) i5-10500H CPU @ 2.50GHz  
- Số lõi vật lý: 6  
- Số luồng: 12 (Hyper-Threading: 2 luồng / lõi)  
- Bộ nhớ đệm:  
  - L1: 192 KiB x2 (Instruction + Data)  
  - L2: 1.5 MiB  
  - L3: 12 MiB  
- Kiến trúc: x86_64 (hỗ trợ cả 32-bit và 64-bit)  
- Công nghệ ảo hóa: Hỗ trợ (Hypervisor: Microsoft)  
- Tốc độ: ~2.50GHz  

**RAM:**

- Tổng dung lượng RAM vật lý: 16 GB  
- RAM khả dụng hiện tại: ~5.3 GB  
- Bộ nhớ ảo tối đa: ~19.8 GB  

**GPU:**

- Tên GPU: NVIDIA GeForce RTX 3060 Laptop GPU  
- Bộ nhớ GPU: 6 GB (6144 MiB)  
- Driver: 566.14  
- CUDA: 12.7  
- GPU Utilization: 0% (Không có tiến trình GPU đang chạy)

---

## Câu 2. 12 bài toán phổ biến trong ngành CNTT

1. **Web Server / API Server**  
   Dùng đa luồng hoặc đa tiến trình để xử lý đồng thời nhiều request từ client.  
   Ví dụ: Một API REST nhận 1000 request/s từ nhiều người dùng, cần tạo luồng/tiến trình riêng để phản hồi kịp thời.

2. **Hệ thống chat hoặc video call thời gian thực**  
   Đa luồng xử lý song song các kết nối WebSocket.  
   Đa tiến trình có thể dùng để phân phối công việc xử lý media, nén, mã hóa video (FFmpeg).  
   Áp dụng ngay trong đồ án môn Hệ Phân Tán mà bạn đang làm.

3. **Streaming Server (Live/VoD)**  
   Đa tiến trình dùng khi mỗi stream được xử lý và encode độc lập.  
   Đa luồng được dùng trong pipeline đọc -> encode -> ghi file/mạng theo thời gian thực.

4. **Hệ thống crawling / scraping dữ liệu web**  
   Đa luồng để mở nhiều kết nối HTTP song song.  
   Đa tiến trình để tránh bị giới hạn GIL (Global Interpreter Lock) khi dùng Python.

5. **Xử lý ảnh/video (Image/Video Processing)**  
   Đa tiến trình khi áp dụng filter, nhận dạng khuôn mặt, OCR, v.v. trên nhiều file đồng thời.  
   Dễ dàng scale theo số core CPU.

6. **Hệ thống gợi ý / đề xuất (Recommendation Systems)**  
   Dữ liệu người dùng lớn → cần đa luồng để tải & phân tích dữ liệu song song.  
   Training hoặc batch processing model dùng đa tiến trình.

7. **Phân tích log / ETL pipeline / Big Data**  
   Đọc, xử lý, lưu logs từ nhiều nguồn.  
   Dùng đa tiến trình để xử lý batch log lớn, parse dữ liệu nhanh hơn.

8. **Trò chơi (Game Engines / Multiplayer Server)**  
   Đa luồng điều khiển vật lý, âm thanh, AI, kết nối mạng độc lập.  
   Game server multiplayer cũng xử lý nhiều người chơi song song qua các thread/tiến trình.

9. **Compiler / Build System**  
   Đa luồng để phân tích cú pháp (parsing), biên dịch các module khác nhau.  
   `make -j8` là ví dụ dùng đa tiến trình để build song song nhiều file nguồn.

10. **CI/CD Pipeline (Jenkins, GitLab Runner)**  
    Chạy đa tiến trình để test / build / deploy nhiều project/module đồng thời.  
    Một pipeline có thể chia task ra các job chạy song song.

11. **Database Engine (PostgreSQL, MySQL, MongoDB)**  
    Đa tiến trình phục vụ nhiều truy vấn từ client đồng thời.  
    Một tiến trình cho mỗi connection, hoặc pool connection với thread workers.

12. **Mô phỏng (Simulations) và Mô hình học máy (Machine Learning)**  
    Đa tiến trình khi chạy mô phỏng hàng nghìn agent (ví dụ: giả lập giao thông, mô hình dịch bệnh).  
    ML training sử dụng đa luồng hoặc GPU song song để tính toán nhanh.

---

## Câu 3. Liệt kê các trường hợp nào thì nên dùng thread, trường hợp nào nên dùng process, khi nào thì nên dùng cả hai

![So sánh thread và process](/images/Picture1.png)

---

## Câu 4. Report: Tìm hiểu ChatGPT training tập dữ liệu lớn bằng distributed system như thế nào

**ChatGPT** được huấn luyện trên các hệ thống phân tán (distributed systems) để xử lý khối lượng dữ liệu khổng lồ và mô hình có hàng trăm tỷ tham số. Dưới đây là tổng quan về cách thức hoạt động của hệ thống này:

### 🧠 Kiến trúc huấn luyện phân tán của ChatGPT

1. **Dữ liệu huấn luyện khổng lồ**  
   GPT-3 được huấn luyện trên khoảng 45 terabyte dữ liệu văn bản, bao gồm các nguồn như:  
   - Common Crawl (60%)  
   - WebText2 (22%)  
   - Books1 & Books2 (16%)  
   - Wikipedia (3%)  
   Dữ liệu được lưu trữ trên các hệ thống lưu trữ phân tán như Amazon S3, Google Cloud Storage hoặc HDFS để đảm bảo khả năng truy cập nhanh và hiệu quả trong quá trình huấn luyện.

2. **Chiến lược huấn luyện phân tán**  
   - **Data Parallelism**: Mỗi GPU xử lý một phần khác nhau của dữ liệu huấn luyện nhưng sử dụng cùng một mô hình. Sau mỗi bước, các trọng số mô hình được đồng bộ hóa giữa các GPU.  
   - **Model Parallelism**: Khi mô hình quá lớn để vừa trên một GPU, nó được chia thành các phần và phân phối qua nhiều GPU.  
   - **Pipeline Parallelism**: Các bước xử lý của mô hình được chia thành các giai đoạn và phân phối qua nhiều GPU, cho phép xử lý song song các batch dữ liệu.  

   Các kỹ thuật này được kết hợp để tối ưu hóa việc sử dụng tài nguyên và tăng tốc độ huấn luyện.

3. **Hạ tầng phần cứng**  
   Huấn luyện ChatGPT yêu cầu hàng ngàn GPU hiệu năng cao như NVIDIA A100 hoặc H100, kết nối qua mạng tốc độ cao như InfiniBand để giảm độ trễ trong giao tiếp giữa các GPU.  
   Ví dụ, huấn luyện GPT-3 với 175 tỷ tham số có thể tiêu tốn khoảng 4.6 triệu USD nếu sử dụng một GPU đơn lẻ trong 355 năm, nhưng với hệ thống phân tán, thời gian huấn luyện được rút ngắn đáng kể.

4. **Phần mềm hỗ trợ**  
   - Framework như **DeepSpeed** và **Ray** được sử dụng để quản lý huấn luyện phân tán, tối ưu hóa việc sử dụng bộ nhớ và phân phối công việc hiệu quả.  
   - **DeepSpeed-Chat** cung cấp một pipeline **RLHF (Reinforcement Learning with Human Feedback)** hiệu quả, cho phép huấn luyện các mô hình ChatGPT-like với chi phí thấp hơn và tốc độ nhanh hơn.

---

## 🔗 Tài liệu tham khảo

- How ChatGPT actually works – AssemblyAI  
- Design of ChatGPT – Medium  
- How Ray, a Distributed AI Framework, Helps Power ChatGPT  
- DeepSpeed-Chat: Easy, Fast and Affordable RLHF Training of ChatGPT-like Models at All Scales  
- GPT-3 – Wikipedia  
