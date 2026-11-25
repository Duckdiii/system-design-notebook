# Giai đoạn 5a: Case Study Nâng Cao (Xu Vol.2)

[← Quay lại Roadmap tổng](../roadmap.md)

> Không cần đọc hết toàn bộ sách. Đọc chọn lọc theo mức ưu tiên bên dưới.

## 1. Thời lượng
- **2 tuần** (Tuần 17–18), **~18 giờ** (~9 giờ/tuần).

## 2. Nguồn tài liệu
- **Xu Vol.2** *(không có trong project, tự tìm bản đọc được)*. Sách có 13 chương, gồm *Proximity Service, Nearby Friends, Distributed Message Queue, Gaming Leaderboard, Payment, Digital Wallet và Stock Exchange*.
- **`[W]`** Tài liệu **Kafka** về cách hoạt động nội bộ, **Stripe/engineering blog** về idempotency và payment.

---

## Tài liệu bài học chi tiết trong Giai đoạn 5a

| Phần | Nội dung | File bài học |
| :---: | :--- | :--- |
| **3** | **Concepts theo ưu tiên:** #1 Payment System (Double-entry Ledger, Idempotency, Reconciliation), #2 Distributed Message Queue (Kafka Log Segments, Zero-copy, ISR, `acks`), #3 Proximity Service (Geohash 9 ô + Quadtree) | [01-concepts.md](./01-concepts.md) |
| **4** | **Thực hành TechStore Payment:** Schema PostgreSQL (`BIGINT` minor units, `ledger_entries`, `psp_webhook_events`), State Machine, Idempotency + `request_hash`, chống Webhook trùng & Reconciliation Job | [02-thuc-hanh-techstore-payment.md](./02-thuc-hanh-techstore-payment.md) |
| **5** | **Mục tiêu đạt được:** Giải thích Payment Idempotency, thiết kế Message Queue (Partition/ISR/Acks) & giải thích Geohash trong 3 phút | [03-muc-tieu-va-check.md](./03-muc-tieu-va-check.md) |

---

## 3. Concepts (chọn đọc theo mức ưu tiên)
- [ ] **Ưu tiên 1 — Payment System:** idempotency key, reconciliation (đối soát), retry an toàn *(liên quan trực tiếp đến TechStore)*.
- [ ] **Ưu tiên 2 — Distributed Message Queue:** bổ trợ cho phần Kafka đã học.
- [ ] **Ưu tiên 3 — Proximity Service:** geohash, quadtree *(nếu còn thời gian)*.
- *(Bỏ qua Stock Exchange nếu không nhắm vào fintech, vì chương này nặng và khó).*

## 4. Thực hành
- [ ] Thiết kế lại luồng thanh toán của **TechStore** theo **Payment System**:
  - Idempotency key
  - Trạng thái giao dịch (state machine)
  - Xử lý callback trùng
  - Đối soát (reconciliation)

## 5. Mục tiêu đạt được
- [ ] Giải thích được vì sao thanh toán cần idempotency và cách cài
- [ ] Thiết kế được hàng đợi phân tán ở mức partition, replication và ack
- [ ] Hiểu geohash đủ để giải thích trong 3 phút

---

## Ghi chú & Thiết kế luồng thanh toán TechStore
<!-- Ghi lại state machine thanh toán, bảng idempotency và quy trình đối soát của TechStore tại đây -->
