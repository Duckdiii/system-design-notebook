# Giai đoạn 1: Data Layer

[← Quay lại Roadmap tổng](../roadmap.md)

> Đây là giai đoạn **dài nhất và quan trọng nhất**.

## 1. Thời lượng
- **4 tuần** (Tuần 3–6), **~40 giờ** (~10 giờ/tuần: **5h đọc**, **5h thực hành**).

## 2. Nguồn tài liệu
- **`[P]`** DDIA ch.3 (*Storage & Retrieval*), ch.5 (*Replication*), ch.6 (*Partitioning*), ch.7 (*Transactions*)
- **`[P]`** `system-design-primer`: *Database*, *SQL vs NoSQL*, *Replication*, *Sharding*, *Indexes*
- **`[W]`** Tài liệu Spring Data JPA/Hibernate về `@Version`, `@Transactional(isolation=…)`
- **`[W]`** Tài liệu PostgreSQL về streaming replication và `EXPLAIN`

## 3. Concepts
- **Tuần 3:** B-Tree vs LSM-Tree, hash index, SSTable, row vs column store, OLTP vs OLAP
- **Tuần 4:** Replication leader–follower, sync vs async, replication lag, read-your-writes, monotonic reads, multi-leader, leaderless và quorum (`W + R > N`)
- **Tuần 5:** Partitioning theo key range và theo hash, hot spot, secondary index khi shard, rebalancing, request routing
- **Tuần 6:** ACID, isolation levels (`read committed`, `snapshot isolation`, `serializable`), lost update, write skew, phantom, 2PL, SSI

## 4. Thực hành
- [ ] Tạo bảng khoảng **5 triệu dòng**, so sánh query khi có và không có index bằng `EXPLAIN ANALYZE`.
- [ ] Dựng **PostgreSQL primary + replica** bằng Docker Compose. Dùng `AbstractRoutingDataSource` để ghi vào primary và đọc từ replica, rồi tạo tình huống đọc lệch (lag).
- [ ] Mô phỏng sharding theo `user_id` trong ứng dụng, thử `hash mod N` rồi thêm một shard để thấy lượng dữ liệu phải di chuyển.
- [ ] Tự gây **lost update** và **write skew** bằng 2 thread/2 transaction, sau đó sửa bằng `@Version`, `SELECT … FOR UPDATE` và isolation cao hơn.

## 5. Mục tiêu đạt được
- [ ] Giải thích được khi nào chọn LSM, khi nào chọn B-Tree
- [ ] Giải thích và tái hiện được replication lag cùng cách xử lý
- [ ] Phân biệt được các isolation level qua anomaly cụ thể (không chỉ thuộc tên)
- [ ] Chọn được chiến lược sharding cho một đề bài và nêu hậu quả của lựa chọn đó

## Mốc kiểm tra liên quan
- [ ] **Tuần 4:** Mock 45 phút (đề dạng DB/scale) — Sản phẩm: *Repo replica + sharding demo*

---

## Ghi chú & Kết quả thực hành
<!-- Ghi lại output EXPLAIN ANALYZE, cấu hình replica lag và kết quả test concurrency tại đây -->
