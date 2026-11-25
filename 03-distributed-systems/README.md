# Giai đoạn 3: Distributed Systems

[← Quay lại Roadmap tổng](../roadmap.md)

> Phần lý thuyết khó, nên đọc chậm và ưu tiên hiểu ý tưởng hơn là nhớ thuật toán.

## 1. Thời lượng
- **3 tuần** (Tuần 10–12), **~30 giờ** (~10 giờ/tuần).

## 2. Nguồn tài liệu
- **`[P]`** DDIA ch.8 (*Trouble with Distributed Systems*), ch.9 (*Consistency & Consensus*)
- **`[P]`** `system-design-primer`: *Consistency patterns*, *Microservices*, *Service discovery*
- **`[W]`** Paper Raft (*"In Search of an Understandable Consensus Algorithm"*) và trang visualization của Raft
- **`[W]`** *Tùy chọn nâng cao:* lab Raft của **MIT 6.5840** (xây hệ key/value chịu lỗi), viết bằng Go
- **`[W]`** Tài liệu **Spring Cloud** (Gateway, OpenFeign), **Resilience4j** (CircuitBreaker, Retry, Bulkhead, TimeLimiter)

## 3. Concepts
- Partial failure, timeout, unreliable network, clock (monotonic vs time-of-day), process pause
- Linearizability, causal ordering, total order broadcast
- 2PC, consensus (Paxos/Raft ở mức ý tưởng), leader election, quorum
- Microservices: service boundary, API Gateway, service discovery
- Saga (choreography vs orchestration), outbox pattern, idempotency key
- Resilience: retry với backoff, circuit breaker, bulkhead, timeout

## 4. Thực hành
- [ ] Tách TechStore (hoặc một bản demo nhỏ) thành **3 service**: `order`, `payment`, `inventory`.
- [ ] Cài **Saga** cho luồng `đặt hàng → thanh toán → trừ kho`, có bù trừ (compensation) khi một bước lỗi.
- [ ] Thêm timeout, retry và circuit breaker bằng **Resilience4j**. Tắt service `payment` giữa chừng để kiểm chứng hệ thống phản ứng đúng.
- [ ] Cài **outbox pattern** để đảm bảo ghi DB và gửi message Kafka nhất quán.

## 5. Mục tiêu đạt được
- [ ] Giải thích được vì sao không dùng 2PC giữa các microservice và Saga đánh đổi điều gì
- [ ] Nói được "quorum" và "leader election" bằng ví dụ cụ thể
- [ ] Demo được hệ thống vẫn chạy khi một service chết
- [ ] Phân biệt được linearizability và eventual consistency qua ví dụ

## Mốc kiểm tra liên quan
- [ ] **Tuần 12:** Giải thích Saga và circuit breaker — Sản phẩm: *Demo 3 service chịu lỗi*

---

## Ghi chú & Kết quả thực hành
<!-- Ghi lại sơ đồ Saga, cấu hình Resilience4j và outbox table schema tại đây -->
