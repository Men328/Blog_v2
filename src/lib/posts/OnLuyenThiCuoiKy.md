---

title: "Ôn tập cuối kỳ – UDPT MEN: Hệ thống phân tán, RPC, thời gian và các khái niệm trọng tâm"

date: "2025-05-27"

updated: "2025-05-27"

categories:

  - "hệ thống phân tán"

  - "rpc"

  - "udp"

  - "ôn tập cuối kỳ"

excerpt: Tổng hợp kiến thức trọng tâm về hệ phân tán, RPC, đồng bộ hóa thời gian, MapReduce, thuật toán phân tán, kiến trúc client-server, mô hình thread và các khái niệm thực tế thường gặp trong kiểm tra.

---



1. Hệ thống phân tán, tập trung, phi tập trung khác nhau như thế nào? Ví dụ?

Hệ tập trung (Centralized):

Đặc điểm: Một nút trung tâm (central node) điều khiển toàn bộ hệ thống. Tất cả dữ liệu và logic xử lý đều tập trung tại đây.

Ví dụ:

Hệ thống ngân hàng truyền thống: Máy chủ trung tâm quản lý tất cả tài khoản và giao dịch.

Ứng dụng web client-server: Máy chủ web xử lý mọi yêu cầu từ client.

Nhược điểm: Điểm yếu duy nhất (Single Point of Failure - SPOF), khả năng mở rộng hạn chế.

Hệ phân tán (Distributed):

Đặc điểm: Nhiều node (máy tính) kết hợp với nhau để chia sẻ tài nguyên và xử lý công việc. Các node giao tiếp qua mạng và phối hợp để hoàn thành mục tiêu chung.

Ví dụ:

Hệ cơ sở dữ liệu phân tán như Apache Cassandra: Dữ liệu được phân tán trên nhiều node, mỗi node quản lý một phần dữ liệu.

Google File System (GFS): Dữ liệu được lưu trữ và sao chép trên nhiều máy chủ.

Ưu điểm: Khả năng chịu lỗi cao, mở rộng dễ dàng.

Hệ phi tập trung (Decentralized):

Đặc điểm: Không có nút trung tâm, các node hoạt động ngang hàng (peer-to-peer). Quyết định được đưa ra dựa trên sự đồng thuận.

Ví dụ:

Blockchain (Bitcoin, Ethereum): Mỗi node lưu trữ một bản sao của sổ cái và tham gia xác thực giao dịch.

Mạng chia sẻ file BitTorrent: File được chia sẻ trực tiếp giữa các peer.

Ưu điểm: Không có SPOF, tính minh bạch và bảo mật cao.

→ Khác biệt chính: Sự tồn tại và vai trò của nút trung tâm. Hệ tập trung phụ thuộc vào một node, hệ phân tán có nhiều node hợp tác, hệ phi tập trung không có node nào chi phối.

2. Các đặc tính của hệ phân tán?

Tính chia sẻ tài nguyên (Resource Sharing):

Giải thích: Tài nguyên (CPU, bộ nhớ, lưu trữ, dịch vụ) được chia sẻ giữa các node. Ví dụ: Máy in mạng, cơ sở dữ liệu phân tán.

Lợi ích: Tận dụng tối đa tài nguyên, giảm chi phí.

Tính mở (Openness):

Giải thích: Hệ thống có thể mở rộng bằng cách thêm thành phần mới mà không làm gián đoạn hoạt động.

Ví dụ: Thêm node vào cụm Hadoop để tăng sức mạnh xử lý.

Tính trong suốt (Transparency):

Giải thích: Che giấu sự phức tạp của hệ phân tán, người dùng cảm thấy như đang làm việc với một hệ thống đơn nhất.

Loại trong suốt:

Truy cập (Access): Truy cập file từ xa như file local (NFS).

Vị trí (Location): Không cần biết node nào lưu trữ dữ liệu.

Di chuyển (Migration): Tài nguyên có thể di chuyển mà không ảnh hưởng đến người dùng.

Khả năng chịu lỗi (Fault Tolerance):

Giải thích: Hệ thống tiếp tục hoạt động khi một số node gặp sự cố nhờ cơ chế sao chép (replication) và phục hồi.

Ví dụ: Cassandra sao chép dữ liệu trên nhiều node để đảm bảo tính sẵn sàng.

Xử lý song song (Concurrency):

Giải thích: Nhiều tiến trình chạy đồng thời trên các node khác nhau, đòi hỏi cơ chế đồng bộ hóa (ví dụ: khóa phân tán).

Khả năng mở rộng (Scalability):

Giải thích: Hệ thống có thể mở rộng bằng cách thêm node để xử lý tải lớn hơn.

Loại:

Dọc (Vertical): Tăng cấu hình node (CPU, RAM).

Ngang (Horizontal): Thêm nhiều node.

3. Mục đích của nút chủ (Master Node)? Sự cố xảy ra khi nút chủ gặp lỗi?

Mục đích:

Điều phối tài nguyên: Phân phối công việc đến các node worker (ví dụ: Hadoop YARN).

Quản lý metadata: Lưu trữ thông tin về vị trí dữ liệu (ví dụ: NameNode trong HDFS).

Xử lý yêu cầu: Nhận và định tuyến yêu cầu từ client.

Hậu quả khi nút chủ gặp sự cố:

Hệ thống ngừng hoạt động: Nếu không có cơ chế dự phòng (single master).

Giải pháp:

Failover: Tự động chuyển sang backup master (ví dụ: ZooKeeper sử dụng leader election).

Multi-master: Nhiều master đồng thời (CouchDB).

4. Tại sao dùng Gossip Protocol trong mạng không gian?

Gossip Protocol: Cơ chế lan truyền thông tin bằng cách mỗi node ngẫu nhiên gửi thông tin đến một số node lân cận.

Ưu điểm so với gửi đến tất cả hoặc qua nút trung tâm:

Giảm băng thông: Không flood mạng.

Chịu lỗi: Thông tin vẫn lan truyền ngay cả khi một số node offline.

Không phụ thuộc nút trung tâm: Tránh SPOF.

Ví dụ:

Cassandra: Dùng gossip để đồng bộ trạng thái node.

Consul: Phát hiện service và node lỗi.

5. Các yếu tố cốt lõi của hệ phân tán

Giao tiếp (Communication):

IPC (Inter-Process Communication): RPC, Message Passing (gửi/nhận thông điệp).

Đồng bộ hóa (Synchronization):

Clock Synchronization: Đồng bộ thời gian vật lý (NTP) hoặc logic (Lamport).

Distributed Locking: Khóa phân tán để quản lý tài nguyên chung.

Quản lý tài nguyên (Resource Management):

Load Balancing: Phân phối tải đều giữa các node.

Scheduling: Lập lịch công việc trên nhiều node.

Nhất quán (Consistency):

Mô hình CAP: Trade-off giữa Tính nhất quán (Consistency), Tính sẵn sàng (Availability), và Khả năng phân vùng (Partition Tolerance).

Chịu lỗi (Fault Tolerance):

Replication: Sao chép dữ liệu trên nhiều node.

Checkpointing: Lưu trạng thái hệ thống để phục hồi.

6. Lý do sử dụng hệ phân tán

Hiệu suất cao: Xử lý song song trên nhiều node.

Khả năng mở rộng: Thêm node để xử lý tải lớn.

Chịu lỗi: Không phụ thuộc vào một node duy nhất.

Chia sẻ tài nguyên: Tận dụng tài nguyên mạng (máy in, lưu trữ).

Độ trễ thấp: Dữ liệu được lưu trữ gần người dùng (CDN).

7. Định nghĩa hệ phân tán

Hệ phân tán là tập hợp các máy tính độc lập (node) kết nối qua mạng, phối hợp thực hiện nhiệm vụ chung. Người dùng cảm nhận hệ thống như một thể thống nhất, dù dữ liệu và xử lý được phân tán.
Ví dụ: Hệ thống email (Gmail), ứng dụng đám mây (Google Drive).

8. Định nghĩa hệ phân tán

Thuật toán Cristian

Thuật toán Berkeley

Thuật toán RBS

Thuật toán Lamport

Thuật toán Bully

Thuật toán Ring

9. Kỹ thuật phân tán hỗ trợ lập trình

Lập trình thủ tục: RPC (Remote Procedure Call) - Gọi hàm từ xa như hàm local.

Lập trình web: REST API, SOAP - Giao tiếp HTTP/HTTPS giữa client-server.

Hướng đối tượng: CORBA, Java RMI - Gọi phương thức đối tượng từ xa.

Liệt kê dữ liệu: MapReduce - Xử lý dữ liệu phân tán bằng cách chia thành các phần (map) và tổng hợp (reduce).

10. Tiến trình (Process), Luồng (Thread), Tiến trình nhẹ (LWP)

Tiến trình:

Ưu: Cô lập tài nguyên (bộ nhớ, CPU), an toàn.

Nhược: Tốn tài nguyên, chuyển đổi chậm (context switching).

Luồng:

Ưu: Chia sẻ bộ nhớ trong cùng tiến trình, nhẹ.

Nhược: Đồng bộ phức tạp (race condition), không tận dụng đa CPU nếu là user thread.

Tiến trình nhẹ (Lightweight Process - LWP):

Ưu: Kết hợp ưu điểm của process và thread, được kernel hỗ trợ.

Nhược: Phức tạp trong quản lý.

Khi lời gọi hệ thống (system call) bị block:

Tiến trình: Toàn bộ tiến trình bị block.

Luồng (Kernel-level): Chỉ luồng đó bị block.

LWP: Tương tự kernel thread.

Mối quan hệ:

Một tiến trình chứa nhiều luồng hoặc LWP.

Luồng user-level được quản lý bởi thư viện, kernel-level bởi OS.

11. Mô hình Client-Server

Máy chủ (Server):

Cung cấp dịch vụ (web, database, file).

Ví dụ: Apache HTTP Server, MySQL.

Máy khách (Client):

Gửi yêu cầu và nhận kết quả từ server.

Ví dụ: Trình duyệt web, ứng dụng mobile.

Giao tiếp: Thông qua giao thức (HTTP, FTP, TCP/IP).

12. Gọi thủ tục từ xa (RPC)

Định nghĩa: Cơ chế cho phép chương trình gọi hàm/thủ tục trên máy khác như gọi hàm local.

Quy trình:

Client gọi hàm stub (client stub).

Stub đóng gói tham số (marshalling), gửi qua mạng.

Server nhận, giải mã (unmarshalling), thực thi hàm.

Kết quả được gửi ngược lại client.

Ví dụ: Google gRPC, XML-RPC.

13. Mô hình phân tầng giao thức và thông điệp bền vững

Phân tầng giao thức (Layered Protocol):

Mục đích: Tách chức năng để dễ quản lý và mở rộng.

Ví dụ: Mô hình OSI (7 lớp) hoặc TCP/IP (4 lớp).

Lợi ích: Mỗi lớp xử lý một nhiệm vụ cụ thể (Ví dụ: Lớp Transport đảm bảo truyền tin đáng tin cậy).

Thông điệp bền vững (Persistent Messaging):

Mục đích: Đảm bảo thông điệp không bị mất khi hệ thống lỗi.

Cơ chế: Lưu thông điệp vào bộ nhớ ổn định (disk) trước khi gửi.

Ví dụ: Apache Kafka, RabbitMQ.

14. Sharding

Định nghĩa: Kỹ thuật chia dữ liệu thành các phần nhỏ (shard) và phân phối trên nhiều node.

Mục đích:

Tăng hiệu suất (mỗi node xử lý một phần dữ liệu).

Mở rộng hệ thống (scale horizontally).

Ví dụ:

MongoDB: Tự động sharding dựa trên key.

PostgreSQL: Sharding thủ công với extensions.

15. Nhiệm vụ của gói luồng (Thread Package)

Tạo và hủy luồng: pthread_create(), pthread_exit().

Đồng bộ hóa:

Mutex (Khóa độc quyền).

Semaphore (Đếm tài nguyên).

Condition Variables (Chờ sự kiện).

Lập lịch: Quyết định luồng nào được chạy tiếp (ví dụ: Round-Robin).

16. Luồng kiểu người dùng (User Thread) vs Luồng kiểu nhân (Kernel Thread)

17. Các hàm chính trong RPC

Register: Đăng ký hàm từ xa trên server (ví dụ: rpc_register()).

Bind: Thiết lập kết nối giữa client và server (ví dụ: rpc_bind()).

Call: Gọi hàm từ client (ví dụ: rpc_call()).

Marshal/Unmarshal:

Marshalling: Đóng gói tham số thành định dạng mạng.

Unmarshalling: Giải mã tham số từ client.

18. Định nghĩa Tiến trình, Thread, Multithread Client/Server

Tiến trình (Process): Một instance của chương trình đang chạy, có không gian bộ nhớ và tài nguyên riêng.

Thread: Đơn vị xử lý trong tiến trình, chia sẻ bộ nhớ với các thread khác.

Multithread Client: Client xử lý nhiều yêu cầu đồng thời (ví dụ: ứng dụng chat).

Multithread Server: Server xử lý nhiều kết nối cùng lúc (ví dụ: Web server dùng thread pool).

19. MapReduce trong hệ phân tán

Map:

Chia dữ liệu đầu vào thành các cặp key-value.

Xử lý song song trên nhiều node.

Reduce:

Tổng hợp kết quả từ Map theo key.

Ví dụ: Đếm số từ trong văn bản lớn.

Mục đích: Xử lý dữ liệu lớn phân tán (batch processing).

Hệ thống: Hadoop, Apache Spark.

20. Ảo hóa (Virtualization) trong hệ phân tán

Định nghĩa: Tạo phiên bản ảo của tài nguyên (máy ảo, container) để chạy ứng dụng độc lập.

Mục đích:

Tận dụng tài nguyên: Chạy nhiều máy ảo trên một máy vật lý.

Cách ly ứng dụng: Mỗi VM/container có môi trường riêng.

Di chuyển dễ dàng: Snapshots, migration (ví dụ: VMware vMotion).

Ví dụ: Docker, Kubernetes, VMware.

21. Kiến trúc Server đa luồng

Thread-per-request:

Mỗi yêu cầu được xử lý bởi một luồng riêng.

Ưu: Đơn giản.

Nhược: Tốn tài nguyên khi có nhiều yêu cầu.

Thread Pool:

Giới hạn số luồng, tái sử dụng luồng.

Ưu: Hiệu quả, tránh tạo/hủy luồng liên tục.

Leader-Follower:

Một luồng chờ kết nối, chuyển kết nối cho luồng khác xử lý.

22. Hướng tiếp cận cài đặt luồng

User-level Thread:

Quản lý bởi thư viện (ví dụ: POSIX threads).

Ưu: Nhanh, không cần kernel hỗ trợ.

Nhược: Không tận dụng đa CPU.

Kernel-level Thread:

Quản lý bởi OS (ví dụ: Windows threads).

Ưu: Hỗ trợ đa CPU.

Nhược: Chậm do chuyển đổi chế độ.

Hybrid:

Kết hợp cả hai (ví dụ: Solaris LWP).

23. Bảng băm phân tán (DHT), Consistent Hashing, Finger Table

DHT (Distributed Hash Table):

Mục đích: Lưu trữ và truy xuất dữ liệu phân tán.

Ví dụ: Chord, Kademlia.

Consistent Hashing:

Mục đích: Giảm di chuyển dữ liệu khi node thêm/xóa.

Cơ chế: Dữ liệu và node được ánh xạ lên vòng tròn hash.

Finger Table:

Mục đích: Tăng tốc tìm kiếm node trong Chord DHT.

Cấu trúc: Lưu thông tin về các node ở khoảng cách 2i2i.

24. Không gian phẳng (Flat Namespace) và Định danh

Không gian phẳng:

Định nghĩa: Tất cả định danh (ID) là duy nhất và không phân cấp.

Ví dụ: UUID, hash SHA-1.

Đặc điểm:

Không có cấu trúc phân cấp.

Dễ quản lý, khó mở rộng.

Thường dùng trong DHT.

25. Đồng bộ hóa đồng hồ logic

Lý do: Đồng hồ vật lý không đồng bộ do độ trễ mạng và sai số phần cứng.

Mục đích: Xác định thứ tự sự kiện (happened-before relationship).

Thuật toán:

Lamport Clock: Gán timestamp dựa trên sự kiện và message.

Vector Clock: Theo dõi quan hệ giữa các tiến trình.

26. Đồng hồ Lamport

Giải quyết vấn đề: Xác định thứ tự sự kiện trong hệ phân tán.

Quy tắc:

Mỗi sự kiện tăng giá trị đồng hồ: Ci=Ci+1Ci​=Ci​+1.

Khi gửi message, gửi kèm timestamp T=CiT=Ci​.

Khi nhận message, cập nhật: Ci=max⁡(Ci,T)+1Ci​=max(Ci​,T)+1.

28. Giao thức NTP và PTP

NTP (Network Time Protocol):

Mục đích: Đồng bộ thời gian qua mạng với độ chính xác milliseconds.

Cơ chế: Đo độ trễ và offset để điều chỉnh.

PTP (Precision Time Protocol):

Mục đích: Đồng bộ microsecond, dùng trong mạng LAN.

Cơ chế: Hardware timestamping để giảm sai số.