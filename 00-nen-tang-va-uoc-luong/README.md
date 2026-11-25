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
| **3** | **Concepts (7 bài riêng trong `concepts/`):** | [concepts/README.md](./concepts/README.md) |
| 3.1 | Vertical vs Horizontal Scaling & Stateless vs Stateful | [concepts/01-scaling-va-stateless.md](./concepts/01-scaling-va-stateless.md) |
| 3.2 | Load Balancer (L4/L7), Reverse Proxy & CDN | [concepts/02-load-balancer-proxy-cdn.md](./concepts/02-load-balancer-proxy-cdn.md) |
| 3.3 | Latency, Throughput, Percentile (`p50`/`p99`), SLA/SLO & "Số 9" | [concepts/03-latency-throughput-sla.md](./concepts/03-latency-throughput-sla.md) |
| 3.4 | Định lý CAP & Mô hình PACELC | [concepts/04-cap-va-pacelc.md](./concepts/04-cap-va-pacelc.md) |
| 3.5 | Ước lượng nhanh (Back-of-the-Envelope Estimation) | [concepts/05-uoc-luong-nhanh.md](./concepts/05-uoc-luong-nhanh.md) |
| 3.6 | Framework 4 bước trong System Design | [concepts/06-framework-4-buoc.md](./concepts/06-framework-4-buoc.md) |
| 3.7 | JWT (JSON Web Token): Cơ chế ký số, Trade-offs & Bảo mật | [concepts/07-jwt-stateless-auth.md](./concepts/07-jwt-stateless-auth.md) |
| **4** | **Thực hành:** Ước lượng TechStore 1M user, Nginx LB + 3 Spring Boot (Docker Compose + failover), Đo `p50`/`p99` bằng k6/JMeter | [02-thuc-hanh-techstore.md](./02-thuc-hanh-techstore.md) |
| **5** | **Mục tiêu đạt được:** Giải thích `p99`, Playbook ước lượng 5 phút (+ bài tập), Cheatsheet 4 bước & Bản vẽ Scale 1 → 1M user | [03-muc-tieu-va-kien-truc.md](./03-muc-tieu-va-kien-truc.md) |

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
