# Bài học Giai đoạn 5a — Phần 5: Mục Tiêu Đạt Được

[← Phần 4: Thiết kế Thanh toán TechStore](./02-thuc-hanh-techstore-payment.md) · [Quay lại GĐ 5a README](./README.md)

---

## Mục tiêu 1 — Giải thích vì sao thanh toán cần Idempotency và cách cài

### Câu trả lời mẫu 60–90 giây
> "Trong thanh toán, mạng là không đáng tin cậy — client bấm nút 2 lần, app mobile tự retry khi mất sóng, hay PSP gửi lại webhook nhiều lần đều có thể xảy ra. Nếu API không idempotent, khách hàng có thể bị **trừ tiền 2 lần** cho 1 đơn hàng.
> Để giải quyết triệt để, em cài Idempotency ở cả **3 điểm tiếp xúc**:
> 1. **Client → Payment Service:** Client sinh `Idempotency-Key` (UUID) gắn vào checkout. Server lưu với `UNIQUE` constraint kèm `request_hash`; nếu retry cùng key và cùng payload thì trả snapshot kết quả cũ, nếu khác payload trả `422`.
> 2. **Payment Service → PSP (Stripe/VNPay):** Truyền tiếp `idempotency_key` ra cổng thanh toán để ngay cả khi timeout mạng và retry, PSP cũng không trừ thẻ lần 2.
> 3. **PSP Webhook → Server:** Lưu `webhook_event_id` vào bảng chống trùng và dùng Conditional Update (`WHERE status IN ('EXECUTING', 'UNKNOWN')`) nên callback trùng hay đến trước response đồng bộ cũng chỉ kích hoạt ghi sổ cái kép (Double-entry Ledger) **đúng 1 lần**."

---

## Mục tiêu 2 — Thiết kế hàng đợi phân tán ở mức Partition, Replication và Ack

Khi interviewer hỏi *"Hãy thiết kế lại Kafka / Distributed Message Queue"*, tóm tắt qua **4 lớp kiến trúc**:

1. **Partitioning & Ordering:**
   - Topic chia thành nhiều **Partition** để scale ngang trên nhiều Broker.
   - Các message cùng `key` (vd `order_id`) băm vào cùng 1 partition → **đảm bảo thứ tự tuyệt đối trong nội bộ Partition**; mỗi Partition là chuỗi Log Segment nối đuôi (Sequential Disk I/O + Zero-copy `sendfile()`).
2. **Replication & ISR (Chịu lỗi Broker):**
   - Mỗi Partition có `1 Leader` + `(RF - 1) Followers` (thường Replication Factor = `3`).
   - Tập **ISR (In-Sync Replicas)** theo dõi những replica còn bắt kịp dữ liệu. Consumer chỉ đọc được tới mốc **High Watermark** (đã sync đủ lên các ISR).
3. **Producer `acks` & Độ bền vững:**
   - Chọn **`acks = 0`** (bắn bỏ, cực nhanh — dùng cho metrics/log), **`acks = 1`** (chờ Leader ghi — cân bằng), hoặc **`acks = all` + `min.insync.replicas = 2`** (chờ toàn bộ ISR xác nhận — bắt buộc cho thanh toán/đơn hàng).
4. **Consumer Group & Delivery:**
   - Pull model (tự điều tiết **Back Pressure**), lưu offset vào topic nội bộ (`__consumer_offsets`), hỗ trợ **At-least-once + Idempotent Consumer** và Dead-Letter Topic.

---

## Mục tiêu 3 — Hiểu Geohash đủ để giải thích trong 3 phút

### Kịch bản nói trôi chảy trong 3 phút
1. **Bài toán & Vấn đề của SQL thường (30 giây):**
   - *"Tìm các quán/tài xế trong bán kính 5km quanh tọa độ `(lat, lon)`. Nếu đánh 2 B-Tree index riêng cho `lat` và `lon` rồi giao 2 tập `BETWEEN` lại sẽ cực chậm vì mỗi chiều quét hàng triệu dòng."*
2. **Nguyên lý Geohash (60 giây):**
   - *"Geohash nén tọa độ 2D thành chuỗi 1D bằng cách chia đôi đệ quy bản đồ thế giới theo kinh độ và vĩ độ thành các ô lưới, gán bit 0/1 xen kẽ rồi mã hóa Base32 (ví dụ `wx4g0ec`)."*
   - *"Điểm hay nhất là các vị trí gần nhau sẽ có chung **Prefix** (ví dụ cùng bắt đầu bằng `wx4g0` cho ô ~5×5 km). Nhờ biến thành chuỗi 1D, DB hoặc Redis có thể đánh index B-Tree bình thường và tìm cực nhanh bằng `WHERE geohash LIKE 'wx4g0%'`."*
3. **Điểm tinh tế — Lỗi ranh giới & Cách sửa (60 giây):**
   - *"Tuy nhiên Geohash có một bẫy ở đường biên: 2 quán cách nhau 20m nhưng nằm 2 bên vách chia lưới lớn có thể có chuỗi Geohash khác hoàn toàn nhau! Để không bị sót, ta luôn query **ô hiện tại cộng thêm 8 ô hàng xóm xung quanh** (tổng 9 prefix), rồi tính lại khoảng cách chính xác (Haversine) trên tập ứng viên nhỏ đó trước khi trả về cho user."*
   - *(Bonus 30s: Nếu dữ liệu tĩnh phân bố không đều theo mật độ thành phố vs nông thôn, có thể cân nhắc **Quadtree** trong RAM; còn vị trí tài xế di chuyển liên tục thì Geohash + Redis rất hợp).*
