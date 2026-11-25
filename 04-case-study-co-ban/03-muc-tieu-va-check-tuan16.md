# Bài học Giai đoạn 4 — Phần 5: Mục Tiêu Đạt Được & Chuẩn Bị Mốc Tuần 16

[← Phần 4: Quy trình 45p, Sổ Trade-off & Mini App](./02-thuc-hanh-va-so-tradeoff.md) · [Quay lại GĐ 4 README](./README.md)

---

## Mục tiêu 1 — Giải được 8 bài trong 45 phút mỗi bài với đủ 4 bước

### Checklist chia đồng hồ 45 phút cho mọi bài Case Study

| Khung giờ | Bước | Tiêu chí đạt |
| :---: | :--- | :--- |
| **0:00 – 07:00** | **Bước 1: Làm rõ & Ước lượng** | Chốt được 3–4 tính năng MVP, DAU/QPS, Peak QPS, Storage & Read/Write ratio |
| **07:00 – 20:00** | **Bước 2: High-Level Design** | Vẽ xong luồng end-to-end, nêu API chính & Schema DB cốt lõi, thống nhất với interviewer |
| **20:00 – 40:00** | **Bước 3: Deep Dive** | Giải quyết được 2 nút thắt khó nhất của đề, nêu rõ **>= 2 trade-offs** và **>= 1 bottleneck** |
| **40:00 – 45:00** | **Bước 4: Wrap-up** | Tự chỉ ra điểm yếu còn sót, monitoring/alerting & hướng scale 10x |

---

## Mục tiêu 2 — Mỗi bài nêu được ít nhất 2 Trade-offs & 1 Điểm nghẽn (Bottleneck)

### Công thức nói Trade-off ghi điểm cao
> *"Với yêu cầu **[X]** của bài toán, em chọn phương án **[A]** thay vì **[B]** vì **[lý do ưu điểm]**. Đánh đổi lại là nhược điểm **[Y]**, và cách để giảm thiểu nhược điểm đó là **[Z]**."*

### Bảng tổng hợp Bottleneck & Trade-off cốt lõi của cả 8 bài

| Bài | Điểm nghẽn đặc trưng (Bottleneck) | 2 cặp Trade-off tiêu biểu cần nhớ |
| :--- | :--- | :--- |
| **1. URL Shortener** | 1 link viral chịu hàng chục nghìn Read QPS | • `Base62 + Unique ID` vs `Hash + Collision check`<br>• `302` (có analytics, tốn tải) vs `301` (nhẹ tải, mất analytics) |
| **2. Notification** | 3rd-party provider (SMS/FCM) nghẽn hoặc chiến dịch bắn hàng triệu tin | • Tách Queue theo kênh vs Queue chung<br>• Retry + DLT + Dedup vs Fire-and-forget |
| **3. News Feed** | **Celebrity Problem** khi push fan-out cho hàng chục triệu followers | • Hybrid (Push thường + Pull celebrity) vs Push/Pull thuần<br>• Cache `post_id` nhẹ RAM vs Cache full object |
| **4. Chat System** | Hàng triệu kết nối **WebSocket stateful** + bão reconnect khi mất mạng | • WebSocket vs Long Polling<br>• Wide-column LSM (`Cassandra`) vs Sharded RDBMS |
| **5. Autocomplete** | Query gõ từng ký tự ở độ trễ `< 100ms` & tranh chấp khóa khi cập nhật | • Lưu sẵn Top-K ở mỗi Node Trie vs Duyệt cây con lúc đọc<br>• Rebuild Trie offline theo batch vs Update real-time |
| **6. Web Crawler** | DDoS nhầm cùng 1 domain + DNS lookup đồng bộ làm nghẽn threads | • Back Queue theo từng Host (Politeness) vs 1 Queue chung<br>• BFS + Bloom Filter/SimHash vs DFS |
| **7. YouTube** | Upload/Transcode file GB làm nghẽn App Server + chi phí băng thông CDN | • Presigned URL + Split GOP workers vs Upload qua App<br>• Cache Top 20% Hot Videos trên CDN vs 100% CDN |
| **8. Google Drive** | Băng thông upload khi sửa file lớn & xung đột chỉnh sửa đồng thời | • Block Chunking (`4MB`) + Delta Sync vs Upload cả file<br>• Conflict Copy (nhân bản xung đột) vs Ghi đè im lặng (LWW) |

---

## Mục tiêu 3 — Biết tự đặt câu hỏi làm rõ yêu cầu thay vì lao vào vẽ

Dưới đây là **10 câu hỏi "vàng"** cho Bước 1 (chọn 4–6 câu phù hợp nhất với đề bài trong 3–5 phút đầu):

1. *"Tính năng cốt lõi (MVP) cần tập trung trong buổi hôm nay là gì? Em có cần thiết kế phần Auth/Analytics/Admin không?"*
2. *"Quy mô hệ thống mục tiêu là bao nhiêu (DAU, số lượt ghi/đọc mỗi ngày)?"*
3. *"Hệ thống này thiên về Read-heavy hay Write-heavy, và tỷ lệ ước chừng là bao nhiêu?"*
4. *"Yêu cầu độ trễ mục tiêu (`p99`) ở khoảng nào (real-time `< 100ms`, vài giây, hay xử lý nền)?"*
5. *"Bài toán này ưu tiên Strong Consistency hay có thể chấp nhận Eventual Consistency để đổi lấy Availability/Latency?"*
6. *"Kích thước payload trung bình (text 100B, ảnh 2MB, hay video 1GB) và thời gian lưu trữ (retention) là bao lâu?"*
7. *"Người dùng tập trung ở 1 quốc gia hay phân bố toàn cầu (để xét CDN / Multi-region)?"*
8. *"Có cần hỗ trợ đa thiết bị / offline sync / push real-time không?"*
9. *"Có kịch bản tăng đột biến (Flash sale, tin nóng, link viral) cần tính Peak Factor cao hơn bình thường không?"*
10. *"Chúng ta có ràng buộc đặc biệt về chi phí hoặc hạ tầng (dùng Cloud managed hay tự dựng) không?"*

---

## Mục tiêu 4 — Đưa bản cài đặt Mini vào CV & Kể trong phỏng vấn (2 phút)

Sau khi làm xong bản cài mini (**URL Shortener** hoặc **Notification**) với `k6` benchmark thật:

- **Mẫu mô tả trong CV (điền số đo thật từ máy bạn):**
  > *"Built a high-throughput URL Shortener service (Spring Boot, PostgreSQL, Redis, Kafka) using Snowflake Base62 encoding, Cache-Aside with null-caching against penetration, and asynchronous click-analytics ingestion via Kafka; achieved `[X] RPS` with `[Y] ms p99` redirect latency under `k6` load testing (200 VUs)."*
- **Cấu trúc kể 2 phút khi interviewer hỏi về dự án:**
  1. *Bối cảnh:* Thiết kế dịch vụ rút gọn URL & đếm analytics.
  2. *Quyết định kỹ thuật:* Vì sao chọn **Snowflake + Base62** (tránh DB collision check), **`302` + Redis Cache-Aside** (giữ tracking mà vẫn đạt `< X ms p99`), và **tách ghi click analytics sang Kafka** để luồng redirect không bị chậm theo DB `UPDATE`.
  3. *Kiểm chứng:* Nêu con số đo `k6` (`redirects: 0`, 200 VUs, `p99` trước/sau khi tách Kafka & bật Redis).

---

## Chuẩn bị Mốc kiểm tra Tuần 16

> **Mốc Tuần 16:** Mock 45 phút *(đề mới hoàn toàn)* · **Sản phẩm:** *Sổ trade-off 8 bài + 1 bản cài mini*

### Gợi ý 3 "đề mới hoàn toàn" để tự Mock 45 phút ở Tuần 16
Chọn 1 đề bạn **chưa từng đọc đáp án trước**, bấm đúng 45 phút và dùng đủ Framework 4 bước:
1. **Thiết kế Pastebin** (tương tự URL Shortener nhưng lưu khối text lớn tới 10MB + hết hạn tự xóa).
2. **Thiết kế hệ thống Đặt vé xem phim / Sự kiện (Ticketmaster)** (khó ở chỗ giữ chỗ tạm 10 phút không double-booking khi 100k người tranh 1,000 ghế).
3. **Thiết kế Leaderboard cho Game thời gian thực** (Top 100 người điểm cao nhất toàn cầu + thứ hạng của chính user — thử thách cấu trúc `Redis Sorted Set ZSET`).
