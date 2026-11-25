# Giai đoạn 2: Building Blocks

[← Quay lại Roadmap tổng](../roadmap.md)

## 1. Thời lượng
- **3 tuần** (Tuần 7–9), **~28 giờ** (~9 giờ/tuần).

## 2. Nguồn tài liệu
- **`[P]`** Xu Vol.1 ch.4 (*Rate Limiter*), ch.5 (*Consistent Hashing*), ch.6 (*Key-Value Store*), ch.7 (*Unique ID Generator*)
- **`[P]`** DDIA ch.4 (*Encoding & Evolution*), phần đầu ch.11 (*messaging, log-based, CDC*)
- **`[P]`** `system-design-primer`: *Cache*, *Asynchronism*, *Message queue*, *Back pressure*, *Idempotent operations*
- **`[W]`** Tài liệu **Redis** (data types, eviction), **Kafka** (partition, consumer group, offset), **Spring Kafka**, **Resilience4j RateLimiter**

---

## Tài liệu bài học chi tiết trong Giai đoạn 2

| Phần | Nội dung | File bài học |
| :---: | :--- | :--- |
| **3** | **Concepts:** Cache patterns & 3 vấn đề kinh điển, Rate Limiting (Token/Leaky/Sliding Window + Lua), Consistent Hashing & Virtual Nodes, Snowflake ID, Queue vs Log (RabbitMQ vs Kafka), Idempotency, DLQ, Serialization & Schema Evolution | [01-concepts.md](./01-concepts.md) |
| **4** | **Thực hành (4 Labs TechStore):** Docker Compose (Postgres+Redis+Kafka KRaft), Redis Cache-Aside + Mutex Stampede + đo `p99`, Rate Limiter Redis Lua Spring Filter, Kafka Order→Notification (Idempotent + `@RetryableTopic` + DLT), Snowflake ID Java | [02-thuc-hanh-techstore.md](./02-thuc-hanh-techstore.md) |
| **5** | **Mục tiêu & Mốc Tuần 8:** Vẽ Consistent Hashing, 3 vấn đề Cache, giải thích Idempotent Consumer, mẫu Benchmark đưa vào CV & chuẩn bị Mock Tuần 8 | [03-muc-tieu-va-check-tuan8.md](./03-muc-tieu-va-check-tuan8.md) |

---

## 3. Concepts (Tóm tắt checklist)
- [ ] **Cache:** cache-aside, read-through, write-through, write-behind, TTL, eviction (LRU/LFU), cache stampede, cache penetration, invalidation
- [ ] **Rate limiting:** token bucket, leaky bucket, sliding window
- [ ] **Consistent hashing:** virtual node
- [ ] **ID sinh phân tán:** UUID, Snowflake, ticket server
- [ ] **Queue vs log:** RabbitMQ vs Kafka, at-least-once/at-most-once/exactly-once, idempotency, dead-letter queue
- [ ] **Serialization:** JSON, Protobuf, Avro, schema evolution

## 4. Thực hành
- [ ] Thêm **Redis cache-aside** cho API sản phẩm của TechStore. Đo `p99` trước/sau, tạo và xử lý **cache stampede** (lock hoặc TTL ngẫu nhiên).
- [ ] Viết **rate limiter token bucket** bằng Redis (Lua script) dưới dạng Spring filter.
- [ ] Tách luồng `"đặt hàng → gửi mail/thông báo"` qua **Kafka**. Viết consumer idempotent và cấu hình retry cùng dead-letter topic.
- [ ] Cài đặt **Snowflake ID generator** bằng Java.

## 5. Mục tiêu đạt được
- [ ] Vẽ và giải thích được consistent hashing với virtual node
- [ ] Nêu được 3 vấn đề của cache và cách xử lý từng cái
- [ ] Giải thích được vì sao consumer phải idempotent
- [ ] Có số liệu đo trước/sau khi thêm cache để đưa vào CV

## Mốc kiểm tra liên quan
- [ ] **Tuần 8:** Mock 45 phút (cache/queue) — Sản phẩm: *Repo cache + rate limiter + Kafka*

---

## Ghi chú & Kết quả thực hành cá nhân
<!-- Ghi lại benchmark p99 trước/sau cache, kết quả test rate limiter và Kafka DLT tại đây -->
