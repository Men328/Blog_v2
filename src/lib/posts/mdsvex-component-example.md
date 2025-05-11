---
title: "Deliverable 1: Đề xuất đề tài và mô tả vấn đề - Xây dựng Hệ thống Đăng ký Tín chỉ và Dạy học Trực tuyến với Video Call bằng Microservices"
date: "2025-05-11"
updated: "2025-05-11"
categories:
  - "hệ thống phân tán"
  - "microservices"
  - "giáo dục trực tuyến"
coverImage: "/images/learning.jpg"
coverWidth: 16
coverHeight: 9
excerpt: Bài viết này trình bày cách thiết kế hệ thống đăng ký tín chỉ và dạy học trực tuyến tích hợp video call theo kiến trúc Microservices.
---

Sự phát triển mạnh mẽ của giáo dục trực tuyến sau đại dịch COVID-19 đã làm thay đổi cách vận hành của nhiều trường đại học và cơ sở đào tạo. Không chỉ dừng lại ở việc triển khai dạy học từ xa thông qua các nền tảng như Google Meet, Zoom hay Microsoft Teams, nhiều tổ chức giáo dục đã và đang tìm cách tích hợp hạ tầng dạy học trực tuyến vào hệ thống quản lý học vụ nhằm đồng bộ hóa dữ liệu, nâng cao hiệu suất và tối ưu chi phí.
Dự án này là một nỗ lực trong việc xây dựng một hệ thống phân tán hiện đại có khả năng:
-	Quản lý đăng ký tín chỉ môn học.
-	Kết nối lớp học phần, giảng viên, sinh viên một cách linh hoạt.
-	Tổ chức lớp học trực tuyến có video call thời gian thực như Google Meet.
-	Tính tiền công giảng dạy dựa trên thời gian thực tế giảng dạy.
-	Xử lý nhiều phòng học cùng lúc, đồng thời đảm bảo hiệu năng cao khi số lượng sinh viên tăng.
Dự án này không chỉ là một bài thực hành phân tán mà còn là mô hình thu nhỏ của một EdTech platform, ứng dụng nhiều công nghệ tiên tiến.
________________________________________


## 🎯 Mục tiêu hệ thống

- Thiết kế hệ thống quản lý tín chỉ học phần giúp sinh viên đăng kí môn học, lớp học phần đúng thời hạn.
- Quản lý giảng viên và sinh viên tham gia từng lớp học cụ thể.
- Triển khai lớp học online với tính năng video call: mỗi lớp học có một phiên học online riêng, hỗ trợ tương tác trực tiếp giữa sinh viên và giảng viên.
- Tính toán chi phí giảng dạy dựa trên thời gian bắt đầu - kết thúc thực tế của mỗi buổi học online.
- Hỗ trợ scale theo chiều ngang: Đảm bảo hệ thống không bị nghẽn cổ chai khi có hàng trăm lớp học và hàng ngàn sinh viên truy cập đồng thời.

## 🏗️ Kiến trúc tổng quan & Công nghệ sử dụng
- Hệ thống được xây dựng theo kiến trúc Microservices, chia thành nhiều dịch vụ độc lập giao tiếp qua gRPC và RabbitMQ.

| Thành phần        | Công nghệ chính                         | Vai trò                                                  |
|-------------------|------------------------------------------|-----------------------------------------------------------|
| Backend chính| NodeJS (Express hoặc Fastify)               | Xử lý API nghiệp vụ: Quản lý môn học, đăng kí lớp học, thông tin người dùng                |
| Cơ sở dữ liệu     | MongoDB                                 | Lưu lớp học, Lưu trữ dữ liệu không quan hệ: lớp học phần, sinh viên, giảng viên, thời lượnglượng dạy        |
| Bộ nhớ tạm        | Redis                                   | Cache dữ liệu truy cập nhiều lần như thông tin lớp học, token, thời gian bắt đầu/kết thúc học     |
| Message broker    | RabbitMQ                                | Đảm bảo việc truyền dữ liệu giữa các microservice không đồng bộ (Ví dụ: lưu log, thông báo, ghi nhận thời gian dạydạy)     |
| Giao tiếp nội bộ  | gRPC                                    | Tối ưu hiệu năng gọi giữa các service                    |
| Video Call        | WebRTC + mediasoup/Janus + STUN/TURN    | Hỗ trợ âm thanh + hình ảnh thời gian thực. Media server xử lý truyền dẫn video giữa nhiều người trong phòng học                        |
| Signaling         | Socket.IO hoặc WebSocket                | Kết nối, điều phối trạng thái call, chat                 |
| Monitoring        | Prometheus + Grafana + Loki             |Giám sát hiệu năng và logs của từng MicroserviceMicroservice                        |
| Triển khai và mở rộngrộng       | Docker + Kubernetes                     | Đóng gói ứng dụng, scale hệ thống khi có nhiều phòng học hoạt động cùng lúclúc                     |

## 📹 WebRTC và vấn đề Video Call

Để có tính năng video call như Google Meet, dự án sử dụng WebRTC làm nền tảng truyền thông media thời gian thực. Tuy nhiên, vì WebRTC chỉ hỗ trợ P2P trực tiếp, với lớp học >2 người, ta cần thêm:

- **Media Server** (mediasoup, Janus): chuyển tiếp tín hiệu giữa nhiều client.
- **STUN/TURN server**: giúp thiết lập kết nối qua NAT/firewall (vd: Coturn).

## 📊 So sánh với các giải pháp khác
Lý do chọn: 
- NodeJS + gRPC + RabbitMQ: tốc độ xử lý cao, phù hợp với các service tách biệt.
- MôngDB: lưu dữ liệu có cấu trúc linh hoạt, dễ scale theo chiều ngang.
- WebRTC + mediasoup: Hỗ trợ xây dựng hệ thống video call tùy chỉnh, không phụ thuộc nền tảng bên thưs ba.

So sánh với các lựa chọn khác:

|Yếu tố            | WebRTC + Media Server | Zoom SDK / Twilio | Google Meet Embed |
|-------------------|------------------------|--------------------|--------------------|
| Tùy biến           | ✅ Toàn quyền kiểm soát         | ❌ Phụ thuộc dịch vụ ngoài         | ❌ Giới hạn chức năng     |
| Chi phí sử dụng             | ✅ Tự host, tiết kiệm             | ❌ Tính phí theo phút      | ❌ Không hỗ trợ tích hợp tốt    |
| Tích hợp hệ thống | ✅ Linh hoạt với backend           | ❌ API khó tùy biến sâu    | ❌ Không tương thích backend  |
| Khả năng mở rộng  |✅ Dễ scale qua K8S | ✅ Dịch vụ cloud mạnh | ❌ Hạn chế tùy chỉnh

## 📅 Kế hoạch thực hiện (9 tuần)

| Tuần | Nội dung chính                                                                 |
|------|---------------------------------------------------------------------------------|
| 1    | Phân tích yêu cầu, chọn kiến trúc tổng thể                                     |
| 2    | Thiết kế ERD và chia dịch vụ microservices                                     |
| 3    | Xây dựng backend quản lý lớp học và đăng ký tín chỉ                            |
| 4    | Triển khai signaling server + call 1-1 bằng WebRTC                              |
| 5    | Tích hợp media server để mở rộng số lượng người dùng                           |
| 6    | Ghi nhận thời gian học thực tế, tính lương giảng viên                          |
| 7    | Kiểm thử khả năng mở rộng với nhiều lớp học đồng thời                          |
| 8    | Tối ưu hiệu năng, giám sát hệ thống                                             |
| 9    | Viết báo cáo và chuẩn bị thuyết trình giữa kỳ                                  |

## 🔚 Kết luận

Dự án hướng tới việc xây dựng một nền tảng học trực tuyến thực sự "tự chủ về công nghệ", có thể scale lớn, dễ dàng tùy biến theo nhu cầu đào tạo, và tích hợp sâu với quy trình quản lý học vụ. Bằng việc tận dụng các công nghệ phân tán như microservices, gRPC, Docker/K8S, WebRTC và các media server mã nguồn mở như mediasoup, hệ thống có thể đáp ứng nhu cầu thực tế trong các trường đại học hiện đại.
Đây là một ví dụ rõ ràng cho việc ứng dụng phân tán không chỉ ở backend, mà còn trải dài từ cơ sở hạ tầng media, đến xử lý logic nghiệp vụ, đến khả năng mở rộng.

---

Bạn có thể dùng phần frontmatter này cho blog framework như Hugo, Astro hoặc Next.js với MDX. Nếu bạn cần chuyển bài viết này sang dạng slide thuyết trình hoặc PDF báo cáo, mình có thể giúp tiếp nhé!
