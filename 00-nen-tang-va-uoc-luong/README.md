# Giai đoạn 0: Nền tảng & Ước lượng

[← Quay lại Roadmap tổng](../roadmap.md)

## 1. Thời lượng
- **2 tuần** (Tuần 1–2), **~16 giờ** (~8 giờ/tuần: **5h đọc**, **3h thực hành**).

## 2. Nguồn tài liệu
- **`[P]`** Xu Vol.1 ch.1 (*Scale from zero*), ch.2 (*Back-of-the-envelope*), ch.3 (*Framework 4 bước*)
- **`[P]`** DDIA ch.1 (*Reliable, Scalable, Maintainable Applications*)
- **`[P]`** `system-design-primer`: *Performance vs Scalability*, *Latency vs Throughput*, *Availability vs Consistency*, *CAP*, *Load balancer*, *CDN*
- **`[W]`** `ByteByteGoHq/system-design-101` để xem sơ đồ minh họa nhanh

## 3. Concepts
- Vertical vs horizontal scaling, stateless vs stateful
- Load balancer (L4/L7), reverse proxy, CDN
- Latency, throughput, percentile (`p50`/`p99`), SLA/SLO, availability "số 9"
- CAP, PACELC (mức khái niệm)
- Ước lượng: QPS, peak QPS, storage, bandwidth, cache size, số server
- Framework 4 bước: làm rõ yêu cầu → high-level → deep dive → wrap-up

## 4. Thực hành
- [ ] Tính QPS, storage 5 năm và số instance Spring Boot cho **TechStore** ở mức 1 triệu user (ghi rõ giả định và đơn vị).
- [ ] Đặt **Nginx** làm load balancer trước 2–3 instance Spring Boot bằng Docker Compose, rồi tắt 1 instance để quan sát hệ thống vẫn chạy.
- [ ] Đo `p50`/`p99` bằng **k6** hoặc **JMeter**.

## 5. Mục tiêu đạt được
- [ ] Giải thích được vì sao nhìn `p99` chứ không nhìn trung bình
- [ ] Tự ước lượng một bài toán bất kỳ trong 5 phút với số liệu tròn
- [ ] Thuộc 4 bước của framework và biết mỗi bước làm gì
- [ ] Có 1 bản vẽ kiến trúc "scale từ 1 server đến 1 triệu user"

---

## Ghi chú & Kết quả thực hành
<!-- Ghi lại giả định ước lượng TechStore, kết quả đo p50/p99 và bản vẽ kiến trúc tại đây -->
