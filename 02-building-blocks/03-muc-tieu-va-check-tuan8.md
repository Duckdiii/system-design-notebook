# Bài học Giai đoạn 2 — Phần 5: Mục Tiêu Đạt Được & Chuẩn Bị Mốc Tuần 8

[← Phần 4: Thực hành TechStore](./02-thuc-hanh-techstore.md) · [Quay lại GĐ 2 README](./README.md)

---

## Mục tiêu 1 — Vẽ và giải thích được Consistent Hashing với Virtual Nodes

### Hình vẽ chuẩn dùng khi phỏng vấn / whiteboard

```text
                       0 (2^32 - 1)
                    .  |  .
              C#1 .    |    . A#1
                .      |      .
       [key_2] *       |       * [key_1] -> đi thuận kim đồng hồ -> gặp B#1!
              .        |        .
         B#2 .         |         . B#1
              .        |        .
       [key_3] *       |       . A#2
                .      |      .
              A#3 .    |    . C#2
                    '  |  '
                      B#3
```

### Câu trả lời mẫu 60–90 giây
> "Nếu chia cache/shard theo `hash(key) % N`, khi thêm hoặc mất 1 server làm `N` thay đổi, khoảng $(N-1)/N$ key bị đổi chỗ (từ 3 lên 4 node làm ~75% key miss cache cùng lúc, đánh sập DB).
> **Consistent Hashing** giải quyết bằng cách băm cả server ID và object key lên cùng một vòng tròn `[0 .. 2^32 - 1]`. Một key được lưu ở **server đầu tiên gặp được khi đi thuận chiều kim đồng hồ**. Khi thêm hoặc bỏ 1 server, trung bình chỉ $1/N$ số key bị dịch chuyển.
> Tuy nhiên, nếu mỗi server chỉ có **1 điểm** trên vòng, hai vấn đề sẽ xảy ra: (1) khoảng cách chia không đều khiến 1 máy gánh quá nhiều tải, và (2) nếu 1 máy chết, **100% tải của nó dồn vào đúng 1 máy kế tiếp** gây chết dây chuyền. Vì vậy, ta gắn cho mỗi server vật lý **100–200 Virtual Nodes** (`A#1, A#2...`) rải đều xen kẽ trên vòng → độ lệch tải cực thấp, và khi 1 máy sập thì tải được **chia đều cho tất cả các máy còn lại**."

---

## Mục tiêu 2 — Nêu được 3 vấn đề của Cache & Cách xử lý từng cái

| # | Vấn đề | Mô tả ngắn | Cách xử lý chuẩn |
| :-: | :--- | :--- | :--- |
| **1** | **Cache Stampede** *(Thundering Herd)* | **1 key cực hot** vừa hết TTL → hàng nghìn request cùng lúc cache miss và đấm thẳng vào DB | **Mutex / Distributed Lock** (chỉ 1 thread vào DB nạp lại cache) + **TTL Jitter** hoặc làm mới sớm trước khi hết hạn |
| **2** | **Cache Penetration** | Request liên tục hỏi **key không tồn tại** (`id = -1`) → luôn miss Redis và đi thẳng xuống DB | **Cache giá trị Null** (`"__NULL__"`) với TTL ngắn (30–60s) + đặt **Bloom Filter** lọc trước các ID không tồn tại |
| **3** | **Cache Avalanche** | **Rất nhiều key** hết hạn cùng 1 thời điểm (hoặc cả cụm Redis sập) → DB hứng trọn bão query | **Random Jitter** cho TTL (`base + rand`) để rải thời điểm hết hạn + **Redis Cluster/Sentinel HA** + **Circuit Breaker** |

---

## Mục tiêu 3 — Giải thích vì sao Consumer phải Idempotent

### 4 nguyên nhân thực tế chắc chắn gây Duplicate Message
1. **Producer Retry:** Producer gửi event tới Kafka broker, broker đã ghi nhận thành công nhưng gói `ACK` trả về bị rớt mạng → Producer tưởng lỗi nên gửi lại lần 2 → **2 bản sao giống hệt nhau trên Topic** (nếu chưa bật `enable.idempotence=true` hoặc retry ở tầng HTTP API).
2. **At-least-once Delivery (Consumer Crash):** Consumer đọc message, đã gửi xong email/cộng điểm, nhưng **crash ngay trước bước commit offset** → Consumer khác khởi động lại và đọc lại đúng offset đó!
3. **Consumer Group Rebalance / Processing Timeout:** Consumer xử lý quá lâu vượt `max.poll.interval.ms` → Kafka tưởng consumer chết nên rebalance giao partition cho consumer khác → **2 consumer xử lý cùng 1 message**!
4. **Replay từ DLQ / Offset cũ:** Vận hành viên reset offset về quá khứ để khắc phục sự cố làm cả những message đã xử lý chạy lại.

### Cách cài đặt chuẩn
- Mọi event đều mang **`event_id` duy nhất** (UUID hoặc Snowflake ID).
- Consumer lưu `event_id` vào bảng `processed_events(event_id PRIMARY KEY)` trong **cùng transaction DB** với thay đổi dữ liệu (hoặc `SET key 1 NX EX ...` trong Redis nếu side-effect là gọi dịch vụ ngoài). Nếu gặp lỗi trùng khóa (`DataIntegrityViolationException`), bỏ qua êm đẹp và vẫn commit offset.

---

## Mục tiêu 4 — Số liệu đo trước/sau khi thêm Cache để đưa vào CV

Sau khi chạy xong **Lab 2.1 (`k6`)**, hãy điền đúng số liệu thật trên máy bạn vào bảng dưới đây *(tuyệt đối không bịa số khi viết CV!)*:

| Chỉ số đo (tải 100 VUs, 30s) | Trước khi có Redis (DB trực tiếp) | Sau khi có Redis Cache-Aside | Mức cải thiện (`%`) |
| :--- | :---: | :---: | :---: |
| **`p50` (Median) Latency** | `___ ms` | `___ ms` | `___ %` |
| **`p99` Latency** | `___ ms` | `___ ms` | `___ %` |
| **Throughput (req/s)** | `___ RPS` | `___ RPS` | `+ ___ %` |
| **DB Queries trong 30s (`dbHitCounter`)** | `___ queries` | `___ queries` | `- ___ %` |

### Mẫu câu đưa vào CV (theo cấu trúc XYZ: Làm gì + Đo bằng gì + Kết quả số liệu thật)

- **Tiếng Anh:**
  > *"Implemented Redis Cache-Aside with per-key distributed lock (`SETNX`), TTL jitter, and null-caching for the TechStore product API; benchmarked with `k6` at 100 VUs, reducing `p99` latency from `[X] ms` to `[Y] ms` (`-[Z]%`) and preventing DB overload during simulated cache stampede."*
  > *"Built a distributed Token Bucket rate limiter using Redis Lua scripts as a Spring filter, and decoupled order notifications via Apache Kafka with idempotent consumers (`processed_events`) and exponential-backoff Dead-Letter Topics."*

- **Tiếng Việt:**
  > *"Áp dụng Redis Cache-Aside kết hợp Mutex lock (`SETNX`) và TTL jitter cho API sản phẩm TechStore, đo bằng `k6` (100 VUs) giúp giảm `p99` latency từ `[X] ms` xuống `[Y] ms` (giảm `[Z]%`) và chặn đứng Cache Stampede xuống PostgreSQL."*

---

## Chuẩn bị Mốc kiểm tra Tuần 8

> **Mốc Tuần 8:** Mock 45 phút *(đề dạng Cache / Queue)* · **Sản phẩm:** *Repo cache + rate limiter + Kafka*

### Checklist trước khi làm Mock Tuần 8
- [ ] Repo trên GitHub đã có đủ: `ProductCacheService` (Lab 2.1), `RateLimitFilter` + `token_bucket.lua` (Lab 2.2), Kafka Producer/Consumer idempotent + DLT (Lab 2.3), `SnowflakeIdGenerator` (Lab 2.4).
- [ ] Đã chạy `k6` và lưu lại ảnh/số liệu `p50`/`p99` trước-sau vào mục Ghi chú của [README.md](./README.md).
- [ ] Tự luyện giải thích miệng trong 2 phút:
  - *Vì sao Rate Limiter Redis phải dùng Lua script thay vì code Java `get()` + `set()`?*
  - *Khi topic Kafka có 3 partitions, tăng lên 6 consumer cùng group có nhanh gấp đôi không?*
  - *Tại sao nên Update DB trước rồi Delete Cache sau thay vì Update Cache?*
