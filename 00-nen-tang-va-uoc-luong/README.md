# Giai đoạn 0: Nền tảng & Ước lượng

[← Quay lại Roadmap tổng](../roadmap.md)

## 1. Thời lượng
- **2 tuần** (Tuần 1–2), **~16 giờ** (~8 giờ/tuần: **5h đọc**, **3h thực hành**).

## 2. Nguồn tài liệu
- **`[P]`** Xu Vol.1 ch.1 (*Scale from zero*), ch.2 (*Back-of-the-envelope*), ch.3 (*Framework 4 bước*)
- **`[P]`** DDIA ch.1 (*Reliable, Scalable, Maintainable Applications*)
- **`[P]`** `system-design-primer`: *Performance vs Scalability*, *Latency vs Throughput*, *Availability vs Consistency*, *CAP*, *Load balancer*, *CDN*
- **`[W]`** `ByteByteGoHq/system-design-101` để xem sơ đồ minh họa nhanh

---

## Tài liệu bài học chi tiết trong Giai đoạn 0

| Phần | Nội dung | File bài học |
| :---: | :--- | :--- |
| **3** | **Concepts:** Scaling, Stateless/Stateful, LB (L4/L7), CDN, p50/p99, SLA/SLO, Số 9, CAP/PACELC, Ước lượng, Framework 4 bước | [01-concepts.md](./01-concepts.md) |
| **4** | **Thực hành:** Ước lượng TechStore 1M user, Nginx LB + 3 Spring Boot (Docker Compose + failover), Đo p50/p99 bằng k6/JMeter | [02-thuc-hanh-techstore.md](./02-thuc-hanh-techstore.md) |
| **5** | **Mục tiêu đạt được:** Giải thích p99, Playbook ước lượng 5 phút (+ bài tập), Cheatsheet 4 bước & Bản vẽ Scale 1 → 1M user | [03-muc-tieu-va-kien-truc.md](./03-muc-tieu-va-kien-truc.md) |

---

## 3. Concepts (Tóm tắt checklist)
- [ ] Vertical vs horizontal scaling, stateless vs stateful
- [ ] Load balancer (L4/L7), reverse proxy, CDN
- [ ] Latency, throughput, percentile (`p50`/`p99`), SLA/SLO, availability "số 9"
- [ ] CAP, PACELC (mức khái niệm)
- [ ] Ước lượng: QPS, peak QPS, storage, bandwidth, cache size, số server
- [ ] Framework 4 bước: làm rõ yêu cầu → high-level → deep dive → wrap-up

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

## Ghi chú & Kết quả thực hành cá nhân
<!-- Ghi lại giả định ước lượng TechStore, kết quả đo p50/p99 và nhận xét sau khi làm lab tại đây -->
