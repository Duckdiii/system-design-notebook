# Bài học Giai đoạn 3 — Phần 5: Mục Tiêu Đạt Được & Chuẩn Bị Mốc Tuần 12

[← Phần 4: Thực hành Microservices](./02-thuc-hanh-microservices.md) · [Quay lại GĐ 3 README](./README.md)

---

## Mục tiêu 1 — Vì sao không dùng 2PC giữa các Microservice và Saga đánh đổi điều gì?

### 1. Vì sao không dùng 2PC (Two-Phase Commit) cho Microservices?
1. **Giao thức Blocking (Giữ lock dài hạn):** Sau Phase 1 (`Prepare`), mỗi DB phải giữ lock trên dữ liệu và chờ lệnh Phase 2. Nếu Coordinator chết giữa chừng → mọi DB tham gia bị treo cứng ở trạng thái *in-doubt*, làm nghẽn toàn bộ request khác!
2. **Độ trễ & Availability thấp:** Mọi service phải cùng sống và phản hồi đồng bộ; xác suất thành công giảm theo tích số các service tham gia (chỉ cần 1 service chậm là toàn bộ transaction bị giữ lock).
3. **Không tương thích hạ tầng:** Các microservice hiện đại dùng database khác loại (PostgreSQL + MongoDB + Redis) hoặc gọi API ngoài (VNPay/Stripe) — hoàn toàn không hỗ trợ giao thức XA/2PC.

### 2. Saga đánh đổi điều gì khi thay thế 2PC?
Saga đạt được **High Availability** và **tránh giữ lock xuyên service**, nhưng đổi lại **3 đánh đổi lớn**:
1. **Mất tính Isolation (I trong ACID):** Vì mỗi bước commit ngay vào Local DB, request khác có thể nhìn thấy dữ liệu trung gian (ví dụ: tiền đã trừ ở `Payment` nhưng `Inventory` chưa trừ xong) → phải dùng **Semantic Lock** (trạng thái `PENDING` / `RESERVED`) để ngăn đọc/sửa sai nghiệp vụ.
2. **Phải tự viết logic Bù trừ (Compensation):** Không có nút "Rollback tự động" của DB; mỗi bước thành công trước đó đều phải có hành động bù trừ tương ứng (vd `refund` tiền, `release` kho) và logic bù trừ phải **Idempotent** + tự retry nếu chính bước bù trừ bị lỗi!
3. **Độ phức tạp vận hành:** Chấp nhận **Eventual Consistency** giữa các service thay vì đồng bộ tức thì; cần tracing (`trace_id`), Outbox pattern và bảng đối soát.

---

## Mục tiêu 2 — Nói được "Quorum" và "Leader Election" bằng ví dụ cụ thể

### 1. Ví dụ về Quorum (Chống Split-Brain trong cụm 3 hoặc 5 Node)
> **Ví dụ cụm Raft / ZooKeeper / Patroni gồm 5 node** (`N1, N2, N3, N4, N5`):
> - **Quorum** là đa số tuyệt đối $\lfloor 5/2 \rfloor + 1 = \mathbf{3 \text{ nodes}}$.
> - Giả sử cáp mạng đứt làm cụm bị chia làm 2 nửa: Nhánh A gồm `{N1, N2}` (**2 node**) và Nhánh B gồm `{N3, N4, N5}` (**3 node**).
> - Nhờ quy tắc Quorum (`>= 3` phiếu), **chỉ Nhánh B** mới đủ phiếu bầu Leader mới và được phép nhận lệnh ghi! Nhánh A chỉ có 2 node (`< 3`) sẽ từ chối nhận ghi → **không bao giờ xảy ra thảm họa 2 Leader cùng nhận ghi dữ liệu mâu thuẫn nhau (Split-Brain)**.
> *(Cũng có thể nhắc thêm Quorum trong hệ Leaderless kiểu Dynamo/Cassandra: với $N=3, W=2, R=2 \Rightarrow W+R>N$ để tập đọc và tập ghi luôn giao nhau ít nhất 1 bản sao mới nhất).*

### 2. Ví dụ về Leader Election (Khi Leader sập)
> **Ví dụ trong cụm Kafka KRaft / etcd gồm 3 node** (`Node 1` đang là Leader, `Node 2` & `Node 3` là Follower):
> 1. `Node 1` gửi `Heartbeat` định kỳ mỗi `50ms` cho `Node 2` và `Node 3`.
> 2. Đột nhiên `Node 1` sập nguồn. `Node 2` đặt **election timeout ngẫu nhiên** `180ms`, `Node 3` đặt `260ms`.
> 3. Hết `180ms` không thấy heartbeat, `Node 2` hết giờ trước → chuyển thành **Candidate**, tăng **Term** lên `Term 2`, tự bỏ phiếu cho mình (1 phiếu) và gửi `RequestVote` sang `Node 3`.
> 4. `Node 3` thấy log của `Node 2` mới ngang bằng mình và chưa bầu cho ai trong `Term 2` → đồng ý bỏ phiếu!
> 5. `Node 2` đạt `2/3` phiếu (Quorum!) → lập tức trở thành **Leader của Term 2** và phát heartbeat để ổn định lại cụm.
> *(Điểm mấu chốt: **Randomized timeout** giúp `Node 2` và `Node 3` không ứng cử cùng lúc gây kẹt **Split Vote**).*

---

## Mục tiêu 3 — Kịch bản Demo hệ thống vẫn chạy khi một Service chết

Dùng chính bản Demo 3 Service trong [02-thuc-hanh-microservices.md](./02-thuc-hanh-microservices.md) để demo 2 kịch bản chịu lỗi trực tiếp:

| Kịch bản sự cố | Điều gì xảy ra nếu KHÔNG có cơ chế bảo vệ? | Điều gì xảy ra TRONG DEMO của bạn? |
| :--- | :--- | :--- |
| **Tắt `payment-service` (`:8082`)** | `order-service` treo chờ timeout hoặc văng `500 Internal Server Error`; cạn Tomcat threads, chết lây cả các API đọc đơn hàng | Sau 5 lỗi đầu, **Resilience4j Circuit Breaker** chuyển sang **`OPEN`**. Các request sau trả **Fallback** (`PAYMENT_DEFERRED`) trong **`< 5 ms`**, `GET /api/orders` và `inventory-service` vẫn chạy bình thường. Khi bật lại `:8082`, mạch tự hồi phục `HALF_OPEN → CLOSED`. |
| **Tắt `Kafka` giữa chừng** | Hoặc mất sự kiện `order.completed`, hoặc giao dịch đặt hàng fail lây | Đơn hàng vẫn ghi `COMPLETED` + lưu vào bảng **`outbox_events` (`status = 'NEW'`)**. Khi Kafka sống lại, `OutboxRelayScheduler` tự đẩy bù toàn bộ event sang Kafka không mất tin nào. |
| **`inventory-service` trả lỗi hết hàng** | Tiền đã trừ ở `payment-service` nhưng không giao được hàng (mất tiền khách!) | Saga chạy **Compensation (`POST /payments/refund`)** hoàn tiền tự động và chuyển đơn sang `CANCELLED_REFUNDED`. |

---

## Mục tiêu 4 — Phân biệt Linearizability và Eventual Consistency qua ví dụ

### Ví dụ "Tỷ số trận chung kết" (Kinh điển từ DDIA Ch.9)
Giả sử trận chung kết vừa kết thúc với tỷ số `2–1`. Alice và Bob ngồi **cùng một phòng** xem điện thoại:
- **Alice** F5 app bóng đá, hét lên: *"2–1 rồi!"*.
- **Bob** nghe Alice hét, lập tức F5 máy của mình ngay sau đó:
  - Nếu hệ thống là **Linearizable:** Ngay khi Alice đã nhìn thấy kết quả mới (`2–1`), hệ thống hành xử như thể chỉ có **1 bản sao duy nhất** → Bob F5 ngay sau đó **BẮT BUỘC phải thấy `2–1`**, không bao giờ được thấy `1–1` cũ nữa!
  - Nếu hệ thống là **Eventual Consistency:** Alice đọc từ Replica A (đã sync `2–1`), còn Bob đọc từ Replica B (đang lag `500ms`) → Bob **vẫn có thể thấy `1–1` cũ** một lát trước khi Replica B kịp đồng bộ!

### Khi nào chọn cái nào?
- **Chọn Linearizability:** Khi dữ liệu cũ gây sai lệch nghiêm trọng (đăng ký username/email không trùng, giữ ghế máy bay cuối cùng, trừ số dư ví tiền, Distributed Lock / Leader Election).
- **Chọn Eventual Consistency:** Khi ưu tiên độ trễ thấp & khả năng sống sót cao khi đứt mạng (lượt xem/like video, bảng tin News Feed, danh mục sản phẩm ít đổi giá, số lượng hàng tồn kho hiển thị tương đối ngoài trang list).

---

## Chuẩn bị Mốc kiểm tra Tuần 12

> **Mốc Tuần 12:** Giải thích Saga và Circuit Breaker · **Sản phẩm:** *Demo 3 service chịu lỗi*

### Checklist trước khi qua Giai đoạn 4
- [ ] Repo demo 3 service (`order`, `payment`, `inventory`) chạy được đầy đủ 3 luồng: *Happy Path*, *Compensation (hết kho → hoàn tiền)*, và *Circuit Breaker `OPEN` khi tắt `payment-service`*.
- [ ] Đã cài `outbox_events` + `OutboxRelayScheduler` (`FOR UPDATE SKIP LOCKED`).
- [ ] Tự trả lời trôi chảy trong 2–3 phút:
  - *Tại sao `OrderSagaService` không được bọc `@Transactional` trùm lên các lời gọi Feign/HTTP?*
  - *Thứ tự hoạt động của 3 trạng thái `CLOSED → OPEN → HALF_OPEN` trong Circuit Breaker và thông số bạn cấu hình?*
  - *Làm sao tránh gửi trùng event hoặc trừ tiền 2 lần khi Resilience4j thực hiện Retry?*
