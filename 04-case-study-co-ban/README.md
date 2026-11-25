# Giai đoạn 4: Case Study Cơ Bản (Xu Vol.1)

[← Quay lại Roadmap tổng](../roadmap.md)

## 1. Thời lượng
- **4 tuần** (Tuần 13–16), **~36 giờ** (~9 giờ/tuần, **2 bài mỗi tuần**, mỗi bài 45 phút có bấm giờ).

## 2. Nguồn tài liệu
- **`[P]`** Xu Vol.1 ch.8–15 (*URL Shortener, Web Crawler, Notification, News Feed, Chat, Autocomplete, YouTube, Google Drive*)
- **`[P]`** `system-design-primer`: các bài mẫu (*Pastebin, Twitter timeline, Web crawler…*)
- **`[W]`** Engineering blog (danh sách ở cuối sách Xu): Uber, Netflix, Discord, Shopify…
- **`[W]`** Playlist System Design của **Gaurav Sen**, và mock interview miễn phí trên **Exponent** hoặc **Hello Interview**

---

## Tài liệu bài học chi tiết trong Giai đoạn 4

| Phần | Nội dung | File bài học |
| :---: | :--- | :--- |
| **3** | **Concepts 8 Case Studies (Tuần 13–16):** Sơ đồ kiến trúc, Deep Dive, Trade-offs & Bottleneck cho *URL Shortener, Notification, News Feed, Chat, Search Autocomplete, Web Crawler, YouTube, Google Drive* | [01-concepts-8-case-studies.md](./01-concepts-8-case-studies.md) |
| **4** | **Thực hành & Bản cài Mini:** Quy trình luyện 45–60p, Sổ Trade-off 8 bài và code mẫu bản mini **URL Shortener + Async Analytics** (Spring Boot + PostgreSQL + Redis + Kafka + `k6`) | [02-thuc-hanh-va-so-tradeoff.md](./02-thuc-hanh-va-so-tradeoff.md) |
| **5** | **Mục tiêu & Mốc Tuần 16:** Bảng tổng hợp Bottleneck/Trade-off 8 bài, 10 câu hỏi vàng Bước 1, mẫu viết CV & 3 đề mới tự Mock Tuần 16 | [03-muc-tieu-va-check-tuan16.md](./03-muc-tieu-va-check-tuan16.md) |

---

## 3. Concepts theo từng bài

| Tuần | Bài | Ý chính | Hoàn thành |
| :---: | :--- | :--- | :---: |
| **13** | URL Shortener, Notification System | Sinh key (base62, ID generator), cache, redirect 301/302, queue, retry, dedup | [ ] |
| **14** | News Feed, Chat System | Fan-out on write vs on read, celebrity problem, WebSocket, presence, message ordering, lưu lịch sử | [ ] |
| **15** | Search Autocomplete, Web Crawler | Trie, top-k, cập nhật bất đồng bộ, BFS crawl, politeness, dedup nội dung | [ ] |
| **16** | YouTube, Google Drive | Blob storage, chunking, transcoding pipeline, CDN, đồng bộ file, conflict | [ ] |

## 4. Thực hành
- [ ] Luyện đủ **8 bài** theo quy trình 45–60 phút:
  1. Tự giải trước, bấm giờ, không nhìn đáp án.
  2. Vẽ sơ đồ và tự nói to quá trình giải thích.
  3. Đọc lời giải của Xu, ghi lại chỗ khác biệt và lý do.
  4. Viết vào **"sổ trade-off"**: *chọn A thay B vì…, đổi lại mất…*
- [ ] Chọn **1 bài** (khuyến nghị **URL Shortener** hoặc **Notification**) và cài đặt bản thu nhỏ bằng **Spring Boot + Redis + Kafka**, có load test.

## 5. Mục tiêu đạt được
- [ ] Giải được 8 bài trên trong 45 phút mỗi bài với đủ 4 bước
- [ ] Mỗi bài nêu được ít nhất 2 trade-off và 1 điểm nghẽn
- [ ] Biết tự đặt câu hỏi làm rõ yêu cầu thay vì lao vào vẽ
- [ ] Có 1 bản cài đặt mini để kể trong CV hoặc phỏng vấn

## Mốc kiểm tra liên quan
- [ ] **Tuần 16:** Mock 45 phút (đề mới hoàn toàn) — Sản phẩm: *Sổ trade-off 8 bài + 1 bản cài mini*

---

## Sổ Trade-off (Ghi chú nhanh)
<!-- Xem mẫu bảng đầy đủ tại 02-thuc-hanh-va-so-tradeoff.md -->
