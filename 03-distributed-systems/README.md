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

---

## Tài liệu bài học chi tiết trong Giai đoạn 3

| Phần | Nội dung | File bài học |
| :---: | :--- | :--- |
| **3** | **Concepts:** Partial Failure, Network Timeout, Clocks (Time-of-Day vs Monotonic), GC Pause & Fencing Token, Linearizability vs Causal vs Total Order, 2PC vs Raft/Quorum, API Gateway & Service Discovery, Saga & Outbox & Idempotency Key, Resilience4j | [01-concepts.md](./01-concepts.md) |
| **4** | **Thực hành (3 Microservices):** Tách `order` / `payment` / `inventory`, cài Saga Orchestration + Compensation (`refund`), cấu hình Resilience4j (Retry + Exponential Backoff + Circuit Breaker + Bulkhead), test tắt `payment-service`, cài Outbox Pattern (`FOR UPDATE SKIP LOCKED`) | [02-thuc-hanh-microservices.md](./02-thuc-hanh-microservices.md) |
| **5** | **Mục tiêu & Mốc Tuần 12:** 2PC vs Saga, ví dụ Quorum & Leader Election, kịch bản Demo chịu lỗi, Linearizability vs Eventual Consistency & Checklist Tuần 12 | [03-muc-tieu-va-check-tuan12.md](./03-muc-tieu-va-check-tuan12.md) |

---

## 3. Concepts (Tóm tắt checklist)
- [ ] Partial failure, timeout, unreliable network, clock (monotonic vs time-of-day), process pause
- [ ] Linearizability, causal ordering, total order broadcast
- [ ] 2PC, consensus (Paxos/Raft ở mức ý tưởng), leader election, quorum
- [ ] Microservices: service boundary, API Gateway, service discovery
- [ ] Saga (choreography vs orchestration), outbox pattern, idempotency key
- [ ] Resilience: retry với backoff, circuit breaker, bulkhead, timeout

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

## Ghi chú & Kết quả thực hành cá nhân
<!-- Ghi lại sơ đồ Saga, cấu hình Resilience4j và log kiểm chứng tắt payment-service tại đây -->
